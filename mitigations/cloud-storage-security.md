---
title: "Cloud Storage Security for AI"
tags:
  - type/mitigation
  - target/training-pipeline
  - target/ml-model
  - source/red-teaming-ai
maturity: draft
created: 2026-02-14
updated: 2026-02-14
---

# Cloud Storage Security for AI

## Summary

Cloud storage security for AI protects training data, model artifacts, and datasets from unauthorized access, data leakage, and tampering through restrictive access controls, encryption at rest and in transit, bucket policies that deny public access, and regular security audits. AI systems store gigabytes to terabytes of sensitive data in cloud storage (S3, Azure Blob, GCS), making misconfigurations a critical attack vector for data exfiltration and model theft.

## Defends Against

| ID | Technique | Description |
|----|-----------|-------------|
| AML.T0008 | [[techniques/ai-infrastructure-attacks]] | Prevents unauthorized access to training data, model theft via publicly readable storage, and data poisoning through weak access controls |

## Implementation

### Storage Access Controls

**Private by default with explicit grants:**
```json
// AWS S3 bucket policy - deny public access
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "DenyPublicAccess",
      "Effect": "Deny",
      "Principal": "*",
      "Action": "s3:*",
      "Resource": [
        "arn:aws:s3:::ml-training-data",
        "arn:aws:s3:::ml-training-data/*"
      ],
      "Condition": {
        "StringNotEquals": {
          "aws:PrincipalAccount": "123456789012"
        }
      }
    },
    {
      "Sid": "AllowTrainingPipelineAccess",
      "Effect": "Allow",
      "Principal": {
        "AWS": "arn:aws:iam::123456789012:role/ML-Training-Pipeline"
      },
      "Action": [
        "s3:GetObject",
        "s3:ListBucket"
      ],
      "Resource": [
        "arn:aws:s3:::ml-training-data",
        "arn:aws:s3:::ml-training-data/datasets/*"
      ]
    },
    {
      "Sid": "AllowModelUpload",
      "Effect": "Allow",
      "Principal": {
        "AWS": "arn:aws:iam::123456789012:role/ML-Training-Pipeline"
      },
      "Action": [
        "s3:PutObject"
      ],
      "Resource": "arn:aws:s3:::ml-training-data/models/*",
      "Condition": {
        "StringEquals": {
          "s3:x-amz-server-side-encryption": "AES256"
        }
      }
    }
  ]
}
```

**Block public access at account level:**
```bash
# AWS: Enable account-level block public access
aws s3control put-public-access-block \
  --account-id 123456789012 \
  --public-access-block-configuration \
    BlockPublicAcls=true,IgnorePublicAcls=true,BlockPublicPolicy=true,RestrictPublicBuckets=true
```

**Azure Storage firewall:**
```bash
# Azure: Restrict storage account to specific VNets
az storage account update \
  --name mlstorageaccount \
  --resource-group ml-rg \
  --default-action Deny

az storage account network-rule add \
  --account-name mlstorageaccount \
  --resource-group ml-rg \
  --vnet-name ml-vnet \
  --subnet ml-subnet
```

### Encryption

**At-rest encryption (server-side):**
```python
import boto3

s3 = boto3.client('s3')

# Enable default encryption on bucket
s3.put_bucket_encryption(
    Bucket='ml-training-data',
    ServerSideEncryptionConfiguration={
        'Rules': [
            {
                'ApplyServerSideEncryptionByDefault': {
                    'SSEAlgorithm': 'aws:kms',
                    'KMSMasterKeyID': 'arn:aws:kms:us-east-1:123456789:key/abc-123'
                },
                'BucketKeyEnabled': True
            }
        ]
    }
)

# Upload with enforced encryption
s3.put_object(
    Bucket='ml-training-data',
    Key='datasets/training-data-v2.parquet',
    Body=data,
    ServerSideEncryption='aws:kms',
    SSEKMSKeyId='arn:aws:kms:us-east-1:123456789:key/abc-123'
)
```

**In-transit encryption (TLS/SSL):**
```json
// S3 bucket policy: enforce TLS
{
  "Sid": "DenyInsecureTransport",
  "Effect": "Deny",
  "Principal": "*",
  "Action": "s3:*",
  "Resource": [
    "arn:aws:s3:::ml-training-data",
    "arn:aws:s3:::ml-training-data/*"
  ],
  "Condition": {
    "Bool": {
      "aws:SecureTransport": "false"
    }
  }
}
```

**Client-side encryption for highly sensitive data:**
```python
from cryptography.fernet import Fernet
import boto3

# Generate encryption key (store securely, e.g., AWS Secrets Manager)
key = Fernet.generate_key()
cipher = Fernet(key)

# Encrypt before upload
plaintext_data = open('sensitive-model.pkl', 'rb').read()
encrypted_data = cipher.encrypt(plaintext_data)

# Upload encrypted blob
s3 = boto3.client('s3')
s3.put_object(
    Bucket='ml-training-data',
    Key='models/sensitive-model.enc',
    Body=encrypted_data
)

# Download and decrypt
response = s3.get_object(Bucket='ml-training-data', Key='models/sensitive-model.enc')
encrypted_blob = response['Body'].read()
decrypted_data = cipher.decrypt(encrypted_blob)
```

### IAM Best Practices

**Least privilege roles:**
```json
// IAM role for training pipeline (read-only data access)
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "s3:GetObject",
        "s3:ListBucket"
      ],
      "Resource": [
        "arn:aws:s3:::ml-training-data/datasets/*"
      ]
    },
    {
      "Effect": "Allow",
      "Action": [
        "s3:PutObject"
      ],
      "Resource": [
        "arn:aws:s3:::ml-training-data/models/experimental/*"
      ]
    },
    {
      "Effect": "Deny",
      "Action": [
        "s3:DeleteObject",
        "s3:DeleteBucket"
      ],
      "Resource": "*"
    }
  ]
}
```

**MFA enforcement for sensitive operations:**
```json
// Require MFA for production model uploads
{
  "Effect": "Allow",
  "Action": "s3:PutObject",
  "Resource": "arn:aws:s3:::ml-training-data/models/production/*",
  "Condition": {
    "BoolIfExists": {
      "aws:MultiFactorAuthPresent": "true"
    }
  }
}
```

**Short-lived credentials with STS:**
```python
import boto3

sts = boto3.client('sts')

# Assume role for temporary credentials (1 hour)
response = sts.assume_role(
    RoleArn='arn:aws:iam::123456789:role/ML-Training-Pipeline',
    RoleSessionName='training-session-12345',
    DurationSeconds=3600
)

credentials = response['Credentials']

# Use temporary credentials
s3_temp = boto3.client(
    's3',
    aws_access_key_id=credentials['AccessKeyId'],
    aws_secret_access_key=credentials['SecretAccessKey'],
    aws_session_token=credentials['SessionToken']
)

# Credentials expire after 1 hour
```

### Automated Security Auditing

**Prowler for AWS security scanning:**
```bash
# Install Prowler
pip install prowler

# Scan S3 buckets for misconfigurations
prowler aws --services s3 --output-formats json html

# Check for:
# - Public buckets
# - Unencrypted buckets
# - Buckets without logging
# - Buckets without versioning
```

**Custom compliance checks:**
```python
import boto3

def audit_ml_storage_buckets():
    """Audit S3 buckets storing ML artifacts"""
    s3 = boto3.client('s3')
    
    buckets = s3.list_buckets()['Buckets']
    
    issues = []
    
    for bucket in buckets:
        bucket_name = bucket['Name']
        
        # Check encryption
        try:
            encryption = s3.get_bucket_encryption(Bucket=bucket_name)
        except s3.exceptions.ServerSideEncryptionConfigurationNotFoundError:
            issues.append(f"{bucket_name}: No encryption configured")
        
        # Check public access block
        public_access = s3.get_public_access_block(Bucket=bucket_name)
        config = public_access['PublicAccessBlockConfiguration']
        
        if not all([
            config['BlockPublicAcls'],
            config['IgnorePublicAcls'],
            config['BlockPublicPolicy'],
            config['RestrictPublicBuckets']
        ]):
            issues.append(f"{bucket_name}: Public access not fully blocked")
        
        # Check versioning (for data lineage)
        versioning = s3.get_bucket_versioning(Bucket=bucket_name)
        if versioning.get('Status') != 'Enabled':
            issues.append(f"{bucket_name}: Versioning not enabled")
    
    return issues
```

**ScoutSuite for multi-cloud scanning:**
```bash
# Install ScoutSuite
pip install scoutsuite

# Scan AWS
scout aws --profile ml-production

# Scan Azure
scout azure --cli

# Scan GCP
scout gcp --project-id ml-project-123
```

### Logging and Monitoring

**Enable access logging:**
```python
import boto3

s3 = boto3.client('s3')

# Enable server access logging
s3.put_bucket_logging(
    Bucket='ml-training-data',
    BucketLoggingStatus={
        'LoggingEnabled': {
            'TargetBucket': 'ml-audit-logs',
            'TargetPrefix': 's3-access-logs/'
        }
    }
)

# Enable CloudTrail data events
cloudtrail = boto3.client('cloudtrail')

cloudtrail.put_event_selectors(
    TrailName='ml-audit-trail',
    EventSelectors=[
        {
            'ReadWriteType': 'All',
            'IncludeManagementEvents': True,
            'DataResources': [
                {
                    'Type': 'AWS::S3::Object',
                    'Values': ['arn:aws:s3:::ml-training-data/*']
                }
            ]
        }
    ]
)
```

**Anomaly detection in access patterns:**
```python
def detect_unusual_downloads():
    """Alert on unusual data access patterns"""
    logs = query_s3_access_logs(last_hours=24)
    
    for log_entry in logs:
        # Flag downloads from unusual locations
        if log_entry['remote_ip'] not in KNOWN_TRAINING_IPS:
            alert(f"S3 access from unknown IP: {log_entry['remote_ip']}")
        
        # Flag bulk downloads
        if log_entry['bytes_sent'] > 10 * 1024 * 1024 * 1024:  # 10GB
            alert(f"Large S3 download: {log_entry['bytes_sent']} bytes")
        
        # Flag access outside business hours
        hour = datetime.fromisoformat(log_entry['timestamp']).hour
        if hour < 6 or hour > 22:
            alert(f"S3 access outside business hours: {log_entry['timestamp']}")
```

## Limitations & Trade-offs

**Limitations:**
- **Misconfiguration drift**: Manual changes can bypass automated policies
- **Insider threats**: Authorized users with excessive permissions can exfiltrate data
- **Cross-account access**: Complex IAM policies prone to errors
- **Encryption key management**: KMS key compromise exposes all encrypted data

**Trade-offs:**
- **Security vs. Convenience**: Strict access controls complicate data science workflows
- **Encryption overhead**: Client-side encryption adds latency
- **Audit log volume**: Detailed logging increases storage costs

## Testing & Validation

1. **Public bucket detection**: Scan for publicly readable buckets; verify none exist
2. **Encryption enforcement**: Attempt unencrypted upload; confirm rejection
3. **IAM privilege escalation**: Test if training role can access production models
4. **Logging completeness**: Verify all access events captured in logs
5. **Access from unauthorized IP**: Attempt download from non-whitelisted IP; confirm block

## Procedure Examples

| Name | Tactic | Description |
|------|--------|-------------|
| [[case-studies/capital-one-breach]] | [[frameworks/atlas/tactics/collection]] | S3 bucket misconfiguration enabled SSRF exploitation; proper access controls would have prevented data exfiltration |

## Sources

> "Misconfigured storage (S3 buckets, Azure Blob) allows publicly readable model files and training data, leading to data and model theft. Defensive strategies include access controls (private by default), encryption at rest and in transit, bucket policies that deny public access, and regular audits."
> — [[sources/bibliography#Red-Teaming AI]], p. 294-295

> "Overly permissive IAM roles enable privilege escalation and lateral movement. Least privilege permissions, role-based access, MFA enforcement, and regular permission audits are critical defenses."
> — [[sources/bibliography#Red-Teaming AI]], p. 295

> "Regular security audits with automated tools (Prowler, ScoutSuite) identify misconfigurations early. Custom compliance checks enforce organization-specific policies."
> — [[sources/bibliography#Red-Teaming AI]], p. 295-296

## Related

- **Complements**: [[mitigations/cloud-ai-security-controls]], [[mitigations/artifact-registry-security]]
- **Enables**: Secure data lakes, tamper-evident model storage
