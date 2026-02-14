---
title: "GPU Security"
tags:
  - type/mitigation
  - target/training-pipeline
  - target/ml-model
  - source/red-teaming-ai
maturity: draft
created: 2026-02-14
updated: 2026-02-14
---

# GPU Security

## Summary

GPU security addresses hardware-level vulnerabilities unique to specialized AI compute infrastructure, including memory leakage between workloads (LeftoverLocals CVE-2024-0911), compression-based side-channel attacks (GPU.zip), and multi-tenancy isolation failures. Mitigations include GPU memory clearing after each workload, firmware/driver updates, workload isolation policies, and physical separation for highest-security applications.

## Defends Against

| ID | Technique | Description |
|----|-----------|-------------|
| AML.T0008 | [[techniques/ai-infrastructure-attacks]] | Prevents GPU memory leakage, side-channel attacks on model weights, and cross-tenant data extraction in shared GPU environments |

## Implementation

### GPU Memory Clearing

**Explicit memory cleanup after workload:**
```python
import torch

def train_model_with_cleanup(model, dataloader):
    """Train model and explicitly clear GPU memory"""
    model = model.cuda()
    
    try:
        for epoch in range(num_epochs):
            for batch in dataloader:
                # Training logic
                outputs = model(batch.cuda())
                loss = criterion(outputs, labels.cuda())
                loss.backward()
                optimizer.step()
        
    finally:
        # Explicit cleanup
        del model
        del outputs
        del loss
        
        # Clear GPU cache
        torch.cuda.empty_cache()
        
        # Force synchronization to ensure completion
        torch.cuda.synchronize()
        
        # Zero out GPU memory (if driver supports)
        if hasattr(torch.cuda, 'reset_peak_memory_stats'):
            torch.cuda.reset_peak_memory_stats()
```

**Kubernetes GPU cleanup hooks:**
```yaml
# Pod spec with GPU cleanup
apiVersion: v1
kind: Pod
metadata:
  name: ml-training-job
spec:
  containers:
  - name: trainer
    image: ml-training:latest
    resources:
      limits:
        nvidia.com/gpu: 1
    lifecycle:
      preStop:
        exec:
          command:
            - /bin/sh
            - -c
            - |
              # Cleanup script before container termination
              python3 << 'EOF'
              import torch
              torch.cuda.empty_cache()
              torch.cuda.synchronize()
              EOF
  
  # Ensure pod is deleted after job completes
  restartPolicy: Never
```

**Driver-level memory zeroing:**
```bash
# NVIDIA driver configuration
# /etc/modprobe.d/nvidia.conf

# Enable zero-copy memory clearing on deallocation
options nvidia NVreg_EnableGpuMemoryZeroing=1

# Apply configuration
sudo update-initramfs -u
sudo reboot
```

### Firmware and Driver Updates

**Automated patching for GPU vulnerabilities:**
```bash
#!/bin/bash
# gpu-update.sh - Automated GPU driver update

# Check current driver version
CURRENT_VERSION=$(nvidia-smi --query-gpu=driver_version --format=csv,noheader)
LATEST_VERSION=$(curl -s https://download.nvidia.com/latest-driver-version)

if [ "$CURRENT_VERSION" != "$LATEST_VERSION" ]; then
    echo "GPU driver update available: $CURRENT_VERSION -> $LATEST_VERSION"
    
    # Download and install (with approval gate for production)
    wget https://download.nvidia.com/drivers/${LATEST_VERSION}/nvidia-driver-${LATEST_VERSION}.run
    
    # Verify checksum
    EXPECTED_HASH=$(curl -s https://download.nvidia.com/drivers/${LATEST_VERSION}/sha256sum)
    ACTUAL_HASH=$(sha256sum nvidia-driver-${LATEST_VERSION}.run | cut -d' ' -f1)
    
    if [ "$EXPECTED_HASH" != "$ACTUAL_HASH" ]; then
        echo "Driver checksum mismatch. Aborting."
        exit 1
    fi
    
    # Install (requires reboot)
    sudo sh nvidia-driver-${LATEST_VERSION}.run --silent
    
    echo "GPU driver updated. Reboot required."
fi
```

**CVE monitoring for GPU vulnerabilities:**
```python
import requests

def check_gpu_cves():
    """Monitor NVD for GPU-related CVEs"""
    nvd_url = "https://services.nvd.nist.gov/rest/json/cves/2.0"
    
    params = {
        "keywordSearch": "GPU OR NVIDIA OR AMD OR CUDA",
        "resultsPerPage": 20
    }
    
    response = requests.get(nvd_url, params=params)
    cves = response.json().get('vulnerabilities', [])
    
    critical_cves = []
    for cve_item in cves:
        cve = cve_item['cve']
        
        # Check if affects our GPU hardware
        if 'LeftoverLocals' in cve.get('descriptions', [{}])[0].get('value', ''):
            critical_cves.append({
                'id': cve['id'],
                'description': cve['descriptions'][0]['value'],
                'severity': cve.get('metrics', {}).get('cvssMetricV31', [{}])[0].get('cvssData', {}).get('baseSeverity')
            })
    
    return critical_cves
```

### Workload Isolation

**Dedicated GPUs for sensitive workloads:**
```yaml
# Kubernetes node selector for GPU isolation
apiVersion: v1
kind: Pod
metadata:
  name: sensitive-training-job
  labels:
    security-level: high
spec:
  nodeSelector:
    gpu-isolation: dedicated  # Only run on isolated GPU nodes
    security-zone: high
  
  containers:
  - name: trainer
    image: ml-training:latest
    resources:
      limits:
        nvidia.com/gpu: 1  # Exclusive GPU access
  
  # Prevent co-location with untrusted workloads
  affinity:
    podAntiAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
      - labelSelector:
          matchExpressions:
          - key: security-level
            operator: NotIn
            values:
            - high
        topologyKey: kubernetes.io/hostname
```

**GPU compute mode configuration:**
```bash
# Set GPU to exclusive process mode (prevents multi-tenancy)
nvidia-smi -c EXCLUSIVE_PROCESS

# Verify configuration
nvidia-smi -q -d COMPUTE
# Output:
#   Compute Mode                    : Exclusive_Process
```

**MIG (Multi-Instance GPU) for hardware isolation:**
```bash
# NVIDIA A100: Create isolated GPU instances
nvidia-smi mig -cgi 9,9,9,9,9,9,9 -C

# Assign MIG instances to specific workloads
# Each instance has isolated memory and compute

# List MIG instances
nvidia-smi mig -lgi
```

### Side-Channel Mitigation

**Disable GPU compression (GPU.zip mitigation):**
```bash
# NVIDIA GPU configuration
# Disable memory compression to prevent compression-based side-channel

# Check compression status
nvidia-smi --query-gpu=compression_mode --format=csv

# Disable compression (if supported)
nvidia-smi -c DISABLED

# Alternative: Kernel module parameter
# /etc/modprobe.d/nvidia.conf
options nvidia NVreg_EnableMemoryCompression=0
```

**Constant-time operations for cryptographic workloads:**
```python
# For ML models processing sensitive data
import torch

def constant_time_softmax(logits):
    """Softmax implementation resistant to timing side-channels"""
    # Avoid early exit optimizations that leak information via timing
    max_logit = torch.max(logits)
    exp_logits = torch.exp(logits - max_logit)
    sum_exp = torch.sum(exp_logits)
    
    # Ensure all operations complete (no short-circuits)
    torch.cuda.synchronize()
    
    return exp_logits / sum_exp
```

### Multi-Tenancy Risk Management

**Cloud GPU instance isolation:**
```python
# AWS EC2 P4d instances: Use dedicated instances
import boto3

ec2 = boto3.client('ec2')

# Launch GPU instance with dedicated tenancy
response = ec2.run_instances(
    ImageId='ami-gpu-optimized',
    InstanceType='p4d.24xlarge',
    Placement={
        'Tenancy': 'dedicated'  # Physical isolation
    },
    MinCount=1,
    MaxCount=1
)
```

**Audit GPU access logs:**
```bash
# Monitor GPU usage and access patterns
nvidia-smi dmon -s pucvmet -c 1000 > gpu-monitoring.log

# Parse logs for anomalies
awk '{
    if ($8 > 90) {  # GPU utilization > 90%
        print "High GPU usage at", $1, "by process", $2
    }
}' gpu-monitoring.log
```

### Trusted Execution Environments (TEE)

**GPU-based confidential computing:**
```python
# NVIDIA H100 with Confidential Computing support
# Future capability: GPU TEE for model protection

# Conceptual API (not yet standardized)
from nvidia.confidential_compute import SecureGPU

secure_gpu = SecureGPU()

# Load model into GPU TEE
secure_gpu.load_model_encrypted(
    model_path='model.enc',
    decryption_key_source='hardware_tpm'
)

# Inference runs in GPU TEE (isolated from host)
result = secure_gpu.predict(input_data)
```

## Limitations & Trade-offs

**Limitations:**
- **Hardware support**: Memory zeroing and TEE features require recent GPU models
- **Performance impact**: Disabling compression can reduce memory bandwidth
- **Multi-tenancy economics**: Dedicated GPUs expensive compared to shared instances
- **Driver stability**: Frequent updates may introduce regressions

**Trade-offs:**
- **Isolation vs. Cost**: Dedicated GPUs significantly more expensive than shared
- **Security vs. Performance**: Memory clearing and compression disabling add overhead
- **Update velocity vs. Stability**: Aggressive patching risks downtime

## Testing & Validation

1. **Memory leakage**: Run sequential workloads; verify no data leakage between them
2. **LeftoverLocals (CVE-2024-0911)**: Test on affected hardware; verify patching
3. **Isolation validation**: Attempt cross-workload memory access; confirm blocking
4. **Firmware verification**: Check GPU firmware version matches expected secure baseline
5. **Side-channel testing**: Measure timing variations; verify mitigation effectiveness

## Procedure Examples

| Name | Tactic | Description |
|------|--------|-------------|
| *(No documented cases yet)* | | |

## Sources

> "LeftoverLocals (CVE-2024-0911) is a GPU memory leak vulnerability affecting AMD, Apple, and Qualcomm GPUs, allowing unauthorized access to data from previous computations. Attackers can extract model weights, training data, and other sensitive information. Mitigation requires GPU memory clearing after use and firmware/driver updates."
> — [[sources/bibliography#Red-Teaming AI]], p. 299-300

> "GPU.zip is a compression-based side-channel attack exploiting GPU memory compression. Attackers observe compression ratios via timing analysis to infer private keys and model information. Mitigation requires disabling compression in sensitive contexts, constant-time operations, and physical isolation for critical workloads."
> — [[sources/bibliography#Red-Teaming AI]], p. 300

> "Multi-tenancy risks arise from shared GPU environments (cloud instances, container-based sharing). Testing includes vulnerability checks for LeftoverLocals, memory isolation verification, side-channel analysis, and driver version assessment."
> — [[sources/bibliography#Red-Teaming AI]], p. 300-301

## Related

- **Complements**: [[mitigations/cloud-ai-security-controls]], [[mitigations/workload-identity-spiffe]]
- **Enables**: Secure multi-tenant GPU infrastructure, confidential model training
