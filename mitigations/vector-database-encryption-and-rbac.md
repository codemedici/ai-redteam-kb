---
title: "Vector Database Encryption and RBAC"
tags:
  - type/mitigation
  - target/rag
  - target/embedding
  - source/ai-native-llm-security
maturity: draft
created: 2026-02-14
updated: 2026-02-14
---
# Vector Database Encryption and RBAC

## Summary

Vector database encryption and role-based access control (RBAC) protects embedding vectors and associated metadata at rest and in transit using cryptographic controls and fine-grained access policies. This mitigation ensures that unauthorized parties—including cloud provider admins, database administrators, and attackers with infrastructure access—cannot read or modify sensitive vector data. By combining server-side encryption with tenant-scoped key management and row-level security policies, organizations maintain confidentiality and integrity of embedding data throughout its lifecycle.

## Defends Against

| ID | Technique | Description |
|----|-----------|-------------|
| | [[techniques/vector-embedding-weaknesses]] | Prevents unauthorized access to vector storage and protects against embedding inversion attacks |
| AML.T0008 | [[techniques/ai-infrastructure-attacks]] | Defends against infrastructure compromise by encrypting vectors at rest; limits damage from database access |
| | [[techniques/unauthorized-knowledge-disclosure]] | Encryption prevents direct database access from exposing sensitive embeddings; RBAC limits query permissions |
| | [[techniques/weak-access-segmentation]] | Row-level security enforces tenant isolation at database layer |

## Implementation

### Threat Model: Database-Level Attacks

**Attack Scenarios:**

1. **Cloud Provider Insider:** Rogue cloud provider admin accesses customer's vector database
2. **Database Breach:** Attacker compromises database server, dumps vector tables
3. **Backup Theft:** Stolen database backups expose embeddings
4. **DBA Abuse:** Database administrator with elevated privileges reads sensitive embeddings
5. **SQL Injection:** Application vulnerability allows unauthorized database queries

**Without encryption/RBAC:**
- Attacker reads embeddings directly from database
- Performs embedding inversion to reconstruct source documents
- Extracts PII, trade secrets, confidential information

**With encryption/RBAC:**
- Encrypted vectors unusable without decryption keys
- Row-level security blocks cross-tenant access at database level
- RBAC limits which users/services can query embeddings

### Component 1: Server-Side Encryption at Rest

**Encrypt all vector data stored in database:**

#### PostgreSQL (PGVector) with Encryption

```sql
-- Enable transparent data encryption (TDE) at tablespace level
-- Requires PostgreSQL compiled with encryption support or use cloud-native encryption

-- AWS RDS approach: Enable encryption at instance creation
-- (Cannot encrypt existing unencrypted RDS instances, must create new encrypted instance)

-- Azure PostgreSQL: Enable encryption through Azure portal
-- Managed through Azure Key Vault

-- Self-hosted approach: Use pgcrypto extension for column-level encryption
CREATE EXTENSION IF NOT EXISTS pgcrypto;

-- Create embeddings table with encrypted vector column
CREATE TABLE embeddings (
    id SERIAL PRIMARY KEY,
    tenant_id VARCHAR(255) NOT NULL,
    document_id VARCHAR(255) NOT NULL,
    document_text TEXT,
    -- Store encrypted embedding vector
    embedding_encrypted BYTEA,
    -- Store IV (initialization vector) for AES decryption
    embedding_iv BYTEA,
    metadata JSONB,
    created_at TIMESTAMP DEFAULT NOW()
);

-- Encrypt embedding on insert
-- (Application generates encryption key from KMS)
CREATE OR REPLACE FUNCTION insert_encrypted_embedding(
    p_tenant_id VARCHAR,
    p_document_id VARCHAR,
    p_document_text TEXT,
    p_embedding_vector VECTOR(768),  -- Plaintext vector
    p_encryption_key BYTEA,  -- Retrieved from KMS
    p_metadata JSONB
) RETURNS INTEGER AS $$
DECLARE
    v_id INTEGER;
    v_iv BYTEA;
    v_encrypted BYTEA;
BEGIN
    -- Generate random IV
    v_iv := gen_random_bytes(16);
    
    -- Encrypt embedding vector
    -- Convert vector to bytea, then encrypt with AES-256-CBC
    v_encrypted := encrypt(
        p_embedding_vector::TEXT::BYTEA,
        p_encryption_key,
        'aes-cbc'
    );
    
    -- Insert encrypted data
    INSERT INTO embeddings (
        tenant_id,
        document_id,
        document_text,
        embedding_encrypted,
        embedding_iv,
        metadata
    ) VALUES (
        p_tenant_id,
        p_document_id,
        p_document_text,
        v_encrypted,
        v_iv,
        p_metadata
    ) RETURNING id INTO v_id;
    
    RETURN v_id;
END;
$$ LANGUAGE plpgsql;
```

**Application-level encryption (more control, more complexity):**

```python
from cryptography.hazmat.primitives.ciphers import Cipher, algorithms, modes
from cryptography.hazmat.backends import default_backend
import os
import numpy as np

class EncryptedVectorStore:
    """Vector store with client-side encryption"""
    
    def __init__(self, kms_client, postgres_conn):
        self.kms_client = kms_client
        self.postgres_conn = postgres_conn
    
    def insert_encrypted_embedding(self, tenant_id, document_id, embedding_vector, metadata):
        """
        Encrypt embedding vector before storing in database
        
        Args:
            tenant_id: Tenant identifier
            document_id: Document identifier
            embedding_vector: Plaintext numpy array
            metadata: Document metadata
        """
        # Get tenant-specific encryption key from KMS
        encryption_key = self.kms_client.get_data_key(
            tenant_id=tenant_id,
            key_id=f"tenant-{tenant_id}-embedding-key"
        )
        
        # Serialize embedding vector to bytes
        embedding_bytes = embedding_vector.tobytes()
        
        # Generate random IV
        iv = os.urandom(16)
        
        # Encrypt embedding with AES-256-CBC
        cipher = Cipher(
            algorithms.AES(encryption_key),
            modes.CBC(iv),
            backend=default_backend()
        )
        encryptor = cipher.encryptor()
        
        # Pad to AES block size (16 bytes)
        padded_embedding = self._pad_to_block_size(embedding_bytes, 16)
        encrypted_embedding = encryptor.update(padded_embedding) + encryptor.finalize()
        
        # Store encrypted embedding in database
        cursor = self.postgres_conn.cursor()
        cursor.execute("""
            INSERT INTO embeddings (tenant_id, document_id, embedding_encrypted, embedding_iv, metadata)
            VALUES (%s, %s, %s, %s, %s)
        """, (tenant_id, document_id, encrypted_embedding, iv, metadata))
        self.postgres_conn.commit()
    
    def retrieve_and_decrypt_embedding(self, tenant_id, document_id):
        """
        Retrieve and decrypt embedding vector
        
        Returns: numpy array (decrypted embedding)
        """
        # Retrieve encrypted embedding from database
        cursor = self.postgres_conn.cursor()
        cursor.execute("""
            SELECT embedding_encrypted, embedding_iv
            FROM embeddings
            WHERE tenant_id = %s AND document_id = %s
        """, (tenant_id, document_id))
        
        row = cursor.fetchone()
        if not row:
            raise ValueError(f"Embedding not found: {document_id}")
        
        encrypted_embedding, iv = row
        
        # Get decryption key from KMS
        decryption_key = self.kms_client.get_data_key(
            tenant_id=tenant_id,
            key_id=f"tenant-{tenant_id}-embedding-key"
        )
        
        # Decrypt embedding
        cipher = Cipher(
            algorithms.AES(decryption_key),
            modes.CBC(iv),
            backend=default_backend()
        )
        decryptor = cipher.decryptor()
        decrypted_padded = decryptor.update(encrypted_embedding) + decryptor.finalize()
        
        # Remove padding
        decrypted_embedding_bytes = self._unpad_from_block_size(decrypted_padded)
        
        # Deserialize to numpy array
        embedding_vector = np.frombuffer(decrypted_embedding_bytes, dtype=np.float32)
        
        return embedding_vector
    
    def _pad_to_block_size(self, data, block_size):
        """PKCS7 padding"""
        padding_length = block_size - (len(data) % block_size)
        return data + bytes([padding_length] * padding_length)
    
    def _unpad_from_block_size(self, data):
        """Remove PKCS7 padding"""
        padding_length = data[-1]
        return data[:-padding_length]
```

#### Pinecone with Encryption

Pinecone encrypts data at rest by default using AES-256. For additional tenant isolation:

```python
import pinecone

class TenantKeyEncryptedPinecone:
    """Pinecone with tenant-specific encryption keys"""
    
    def __init__(self, kms_client):
        self.kms_client = kms_client
        pinecone.init(api_key="your_api_key")
    
    def insert_with_encryption(self, tenant_id, vectors):
        """
        Insert vectors with tenant-specific encryption
        
        Pinecone handles storage encryption; add application-layer encryption
        for tenant key isolation
        """
        # Get tenant encryption key
        encryption_key = self.kms_client.get_data_key(
            tenant_id=tenant_id,
            key_id=f"tenant-{tenant_id}-pinecone-key"
        )
        
        # Encrypt vectors before sending to Pinecone
        encrypted_vectors = []
        for vec_id, vec_values, vec_metadata in vectors:
            encrypted_values = self._encrypt_vector(vec_values, encryption_key)
            encrypted_vectors.append((vec_id, encrypted_values, vec_metadata))
        
        # Upsert to tenant-specific index
        index = pinecone.Index(f"tenant-{tenant_id}")
        index.upsert(vectors=encrypted_vectors)
    
    def _encrypt_vector(self, vector_values, key):
        """Encrypt vector values (simplified)"""
        # In production: Use proper AES encryption
        # Placeholder: XOR with key-derived values
        import hashlib
        key_hash = hashlib.sha256(key).digest()
        
        encrypted = []
        for i, val in enumerate(vector_values):
            key_byte = key_hash[i % len(key_hash)]
            encrypted.append(val + (key_byte / 255.0))  # Simplified
        
        return encrypted
```

### Component 2: Tenant-Scoped Key Management

**Use separate encryption keys per tenant:**

```python
import boto3

class TenantKMSManager:
    """Manage tenant-specific encryption keys in AWS KMS"""
    
    def __init__(self):
        self.kms_client = boto3.client('kms')
        self.tenant_keys = {}  # Cache of {tenant_id: key_id}
    
    def get_or_create_tenant_key(self, tenant_id):
        """
        Get or create KMS key for tenant
        
        Returns: KMS key ID
        """
        if tenant_id in self.tenant_keys:
            return self.tenant_keys[tenant_id]
        
        # Check if key exists
        key_alias = f"alias/tenant-{tenant_id}-embedding-key"
        try:
            response = self.kms_client.describe_key(KeyId=key_alias)
            key_id = response['KeyMetadata']['KeyId']
        except self.kms_client.exceptions.NotFoundException:
            # Create new key for tenant
            response = self.kms_client.create_key(
                Description=f"Embedding encryption key for tenant {tenant_id}",
                KeyUsage='ENCRYPT_DECRYPT',
                Origin='AWS_KMS'
            )
            key_id = response['KeyMetadata']['KeyId']
            
            # Create alias
            self.kms_client.create_alias(
                AliasName=key_alias,
                TargetKeyId=key_id
            )
        
        self.tenant_keys[tenant_id] = key_id
        return key_id
    
    def get_data_key(self, tenant_id):
        """
        Generate data encryption key for tenant
        
        Returns: 256-bit encryption key (plaintext)
        """
        key_id = self.get_or_create_tenant_key(tenant_id)
        
        # Generate data key
        response = self.kms_client.generate_data_key(
            KeyId=key_id,
            KeySpec='AES_256'
        )
        
        # Return plaintext key for encryption
        # (Application encrypts data with this, stores encrypted key with data)
        return response['Plaintext']
    
    def rotate_tenant_key(self, tenant_id):
        """
        Rotate tenant's encryption key
        
        Triggers re-encryption of all embeddings
        """
        key_id = self.get_or_create_tenant_key(tenant_id)
        
        # Enable automatic key rotation
        self.kms_client.enable_key_rotation(KeyId=key_id)
        
        # Trigger manual re-encryption workflow
        # (Fetch all embeddings, decrypt with old key, encrypt with new key)
        self._trigger_reencryption_job(tenant_id)
```

**Advantages of tenant-scoped keys:**
- **Key isolation:** Compromise of one tenant's key doesn't affect others
- **Compliance:** Meet regulatory requirements for tenant data separation
- **Revocation:** Revoke single tenant's key without affecting others

### Component 3: Row-Level Security (RLS)

**Enforce tenant isolation at database level:**

```sql
-- PostgreSQL Row-Level Security (RLS)

-- Enable RLS on embeddings table
ALTER TABLE embeddings ENABLE ROW LEVEL SECURITY;

-- Create policy: Users can only access their tenant's data
CREATE POLICY tenant_isolation_policy ON embeddings
    USING (tenant_id = current_setting('app.current_tenant_id'));

-- Application sets tenant context before queries
-- (Prevents SQL injection from bypassing tenant filter)

-- Example session setup:
-- SET app.current_tenant_id = 'tenant-abc-123';

-- Now all queries automatically filtered by tenant:
-- SELECT * FROM embeddings;  -- Only returns tenant-abc-123's rows
```

**Application integration:**

```python
import psycopg2

class RLSAwareVectorStore:
    """Vector store with Row-Level Security enforcement"""
    
    def __init__(self, db_config):
        self.db_config = db_config
    
    def get_tenant_connection(self, tenant_id):
        """
        Get database connection with tenant context set
        
        Returns: psycopg2 connection with RLS policy applied
        """
        conn = psycopg2.connect(**self.db_config)
        
        # Set tenant context for RLS
        cursor = conn.cursor()
        cursor.execute("SET app.current_tenant_id = %s", (tenant_id,))
        conn.commit()
        
        return conn
    
    def query_embeddings(self, tenant_id, query_filter):
        """
        Query embeddings with automatic tenant isolation
        
        RLS ensures only tenant's data is accessible
        """
        conn = self.get_tenant_connection(tenant_id)
        cursor = conn.cursor()
        
        # Query automatically filtered by RLS policy
        cursor.execute("""
            SELECT id, document_id, embedding_encrypted
            FROM embeddings
            WHERE metadata @> %s
        """, (query_filter,))
        
        results = cursor.fetchall()
        conn.close()
        
        return results
```

### Component 4: Encryption in Transit

**Protect vectors during transmission:**

```python
# Force TLS for database connections
db_config = {
    'host': 'vectordb.example.com',
    'database': 'embeddings',
    'user': 'app_user',
    'password': 'secure_password',
    'sslmode': 'require',  # Force TLS
    'sslrootcert': '/path/to/ca-cert.pem'  # Verify server certificate
}

# PostgreSQL with TLS
conn = psycopg2.connect(**db_config)

# Pinecone uses HTTPS by default
# Weaviate supports TLS configuration
```

### Component 5: RBAC for Database Access

**Limit database user permissions:**

```sql
-- Create read-only user for application
CREATE USER app_reader WITH PASSWORD 'secure_password';

-- Grant only SELECT permission on embeddings table
GRANT SELECT ON embeddings TO app_reader;

-- Create write-only user for embedding ingestion
CREATE USER app_writer WITH PASSWORD 'secure_password';
GRANT INSERT ON embeddings TO app_writer;

-- DBA users should NOT have direct access to production embedding tables
-- Use break-glass procedure for emergency access only
```

## Limitations & Trade-offs

**Limitations:**
- **Performance overhead:** Encryption/decryption adds latency to queries
- **Key management complexity:** Secure key storage, rotation, and access control require infrastructure
- **Limited vector search on encrypted data:** Cannot perform similarity search on encrypted vectors (must decrypt first)
- **Backup encryption:** Requires separate consideration for backup encryption and key escrow

**Trade-offs:**
- **Security vs. Performance:** Encryption adds 10-30% query latency overhead
- **Tenant key isolation vs. Complexity:** Separate keys per tenant increase key management burden
- **Application vs. Database encryption:** Application-layer encryption more secure but more complex

**Bypass Scenarios:**
- **Key compromise:** If encryption keys stolen, data decryptable
- **Application vulnerability:** SQL injection could bypass RLS if application layer compromised
- **Insider threat:** DBA with RLS bypass privileges could access all data
- **Cold boot attack:** Keys in application memory could be extracted from RAM dump

## Testing & Validation

**Functional Testing:**
1. **Encryption Verification:** Verify database contains encrypted data (not plaintext)
2. **Decryption Correctness:** Ensure decrypted embeddings match original vectors
3. **RLS Enforcement:** Test that queries cannot access other tenants' data
4. **Key Rotation:** Validate key rotation process doesn't lose data

**Security Testing:**
1. **Direct Database Access:** Attempt to read embeddings without decryption keys
2. **Cross-Tenant Query:** Try to query Tenant B's data while authenticated as Tenant A
3. **SQL Injection:** Test if SQL injection can bypass RLS policies
4. **Backup Security:** Verify backups are encrypted and unusable without keys

**Performance Testing:**
1. **Encryption Overhead:** Measure query latency increase from encryption
2. **Throughput Impact:** Benchmark inserts/second with and without encryption
3. **Key Retrieval Latency:** Measure KMS key fetch time

## Procedure Examples

| Name | Tactic | Description |
|------|--------|-------------|
| *(No documented cases yet)* | | |

## Sources

> "Store: Encryption + RBAC — Server-side AES-256 with tenant-scoped KMS keys; row-level security (`WHERE tenant_id = current_user()`) in PGVector."
> — [[sources/bibliography#AI-Native LLM Security]], p. 351-355

## Related

- **Combines with**: [[mitigations/rag-access-control]], [[mitigations/tenant-isolated-embedding-infrastructure]], [[mitigations/secure-model-storage]]
- **Enables**: Secure multi-tenant vector storage, compliance with data protection regulations, defense-in-depth for embedding confidentiality
