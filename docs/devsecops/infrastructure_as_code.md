# Infrastructure as Code Security

## Overview

Infrastructure as Code (IaC) security is critical for modern cloud-native deployments. This section covers security scanning, policy enforcement, and best practices for securing infrastructure definitions using tools like Terraform, Ansible, and CloudFormation.

## IaC Security Challenges

### Common Security Issues
- **Misconfigured Resources:** Insecure default configurations in cloud resources
- **Excessive Permissions:** Over-privileged IAM roles and policies
- **Unencrypted Data:** Storage and transmission without encryption
- **Public Exposure:** Accidentally exposed resources to the internet
- **Missing Security Controls:** Lack of logging, monitoring, and access controls

### Supply Chain Risks
- **Malicious Modules:** Compromised third-party modules and providers
- **Outdated Dependencies:** Using modules with known vulnerabilities
- **Untrusted Sources:** Modules from unverified repositories

## Security Scanning Tools

### 1. Checkov - Comprehensive IaC Scanner

**Installation and Basic Usage**
```bash
# Install Checkov
pip install checkov

# Scan Terraform files
checkov -f main.tf

# Scan entire directory
checkov -d /path/to/terraform

# Output formats
checkov -d . --framework terraform --output json
checkov -d . --framework terraform --output sarif
```

**Custom Policy Example**
```python
# custom_policies/ensure_s3_encryption.py
from checkov.common.models.enums import TRUE_VALUES
from checkov.terraform.checks.resource.base_resource_check import BaseResourceCheck

class S3BucketEncryption(BaseResourceCheck):
    def __init__(self):
        name = "Ensure S3 bucket encryption is enabled"
        id = "CKV_AWS_141"
        supported_resources = ['aws_s3_bucket']
        categories = ['ENCRYPTION']
        super().__init__(name=name, id=id, categories=categories, supported_resources=supported_resources)

    def scan_resource_conf(self, conf):
        if 'server_side_encryption_configuration' in conf:
            encryption_config = conf['server_side_encryption_configuration'][0]
            if isinstance(encryption_config, dict):
                return CheckResult.PASSED
        return CheckResult.FAILED

check = S3BucketEncryption()
```

### 2. Terrascan - Policy as Code Scanner

**Configuration and Usage**
```bash
# Install Terrascan
curl -L "$(curl -s https://api.github.com/repos/tenable/terrascan/releases/latest | grep -o -E "https://.+?_Linux_x86_64.tar.gz")" > terrascan.tar.gz
tar -xf terrascan.tar.gz terrascan && rm terrascan.tar.gz
sudo mv terrascan /usr/local/bin

# Scan with specific policies
terrascan scan -t terraform -p aws

# Custom policy directory
terrascan scan -t terraform -p aws --policy-path ./custom-policies
```

**Custom OPA Policy Example**
```rego
# custom-policies/aws_s3_public_access.rego
package accurics

deny[{"alertMsg": msg, "suggestion": suggestion, "error": error}] {
    resource := input.aws_s3_bucket[name]
    resource.config.acl[_] == "public-read"
    msg := sprintf("S3 bucket %s allows public read access", [name])
    suggestion := "Remove public-read ACL from S3 bucket"
    error := ""
}

deny[{"alertMsg": msg, "suggestion": suggestion, "error": error}] {
    resource := input.aws_s3_bucket[name]
    resource.config.acl[_] == "public-read-write"
    msg := sprintf("S3 bucket %s allows public read-write access", [name])
    suggestion := "Remove public-read-write ACL from S3 bucket"
    error := ""
}
```

### 3. TFLint - Terraform Linter

**Advanced Configuration**
```hcl
# .tflint.hcl
config {
  module = true
  force = false
  disabled_by_default = false
}

plugin "aws" {
  enabled = true
  version = "0.18.0"
  source  = "github.com/terraform-linters/tflint-ruleset-aws"
}

rule "aws_instance_invalid_type" {
  enabled = true
}

rule "aws_instance_previous_type" {
  enabled = true
}

rule "terraform_deprecated_interpolation" {
  enabled = true
}

rule "terraform_unused_declarations" {
  enabled = true
}
```

### 4. KICS - Comprehensive Security Scanner

**Usage Examples**
```bash
# Scan multiple IaC frameworks
kics scan -p ./infrastructure --report-formats json,sarif

# Scan with specific queries
kics scan -p ./terraform -q ./custom-queries

# Generate Software Bill of Materials
kics scan -p ./infrastructure --bom
```

## CI/CD Integration

### 1. GitHub Actions Integration

**Comprehensive Security Pipeline**
```yaml
name: IaC Security Scan
on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main]

jobs:
  terraform-security:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v3

      - name: Setup Terraform
        uses: hashicorp/setup-terraform@v2
        with:
          terraform_version: 1.6.0

      - name: Terraform fmt
        run: terraform fmt -check -recursive

      - name: Terraform validate
        run: |
          terraform init -backend=false
          terraform validate

      - name: Run Checkov
        uses: bridgecrewio/checkov-action@master
        with:
          directory: .
          framework: terraform
          output_format: sarif
          output_file_path: checkov-results.sarif
          quiet: true
          soft_fail: true

      - name: Run TFLint
        uses: reviewdog/action-tflint@master
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          reporter: github-pr-review
          filter_mode: nofilter
          fail_on_error: true
          flags: --module

      - name: Run Terrascan
        uses: tenable/terrascan-action@main
        with:
          iac_type: terraform
          iac_version: v14
          policy_type: aws
          only_warn: true
          sarif_upload: true

      - name: Upload SARIF results
        uses: github/codeql-action/upload-sarif@v2
        with:
          sarif_file: checkov-results.sarif
```

### 2. GitLab CI Integration

**GitLab CI Configuration**
```yaml
# .gitlab-ci.yml
stages:
  - validate
  - security-scan
  - plan
  - apply

variables:
  TF_ROOT: terraform/
  TF_VERSION: 1.6.0

before_script:
  - cd $TF_ROOT
  - terraform --version
  - terraform init -backend=false

terraform-validate:
  stage: validate
  script:
    - terraform fmt -check -recursive
    - terraform validate

security-scan:
  stage: security-scan
  image: bridgecrew/checkov:latest
  script:
    - checkov -d . --framework terraform --output json > checkov-results.json
    - checkov -d . --framework terraform --output sarif > checkov-results.sarif
  artifacts:
    reports:
      sast: checkov-results.sarif
    paths:
      - checkov-results.json
    expire_in: 1 week
  allow_failure: true

tflint:
  stage: security-scan
  image: ghcr.io/terraform-linters/tflint:latest
  script:
    - tflint --init
    - tflint --format=junit > tflint-results.xml
  artifacts:
    reports:
      junit: tflint-results.xml
    expire_in: 1 week
```

## Security Best Practices by Tool

### 1. Terraform Security

**Secure Resource Configuration**
```hcl
# Secure S3 bucket configuration
resource "aws_s3_bucket" "secure_bucket" {
  bucket = "my-secure-bucket"
}

resource "aws_s3_bucket_versioning" "secure_bucket" {
  bucket = aws_s3_bucket.secure_bucket.id
  versioning_configuration {
    status = "Enabled"
  }
}

resource "aws_s3_bucket_server_side_encryption_configuration" "secure_bucket" {
  bucket = aws_s3_bucket.secure_bucket.id

  rule {
    apply_server_side_encryption_by_default {
      kms_master_key_id = aws_kms_key.s3_key.arn
      sse_algorithm     = "aws:kms"
    }
  }
}

resource "aws_s3_bucket_public_access_block" "secure_bucket" {
  bucket = aws_s3_bucket.secure_bucket.id

  block_public_acls       = true
  block_public_policy     = true
  ignore_public_acls      = true
  restrict_public_buckets = true
}

# Secure EC2 instance configuration
resource "aws_instance" "secure_instance" {
  ami           = data.aws_ami.amazon_linux.id
  instance_type = "t3.micro"
  
  vpc_security_group_ids = [aws_security_group.secure_sg.id]
  subnet_id              = aws_subnet.private_subnet.id
  
  metadata_options {
    http_endpoint = "enabled"
    http_tokens   = "required"
    http_put_response_hop_limit = 1
  }
  
  root_block_device {
    encrypted   = true
    kms_key_id  = aws_kms_key.ebs_key.arn
    volume_type = "gp3"
  }
  
  tags = {
    Name = "secure-instance"
  }
}

# Secure Security Group
resource "aws_security_group" "secure_sg" {
  name_prefix = "secure-sg-"
  vpc_id      = aws_vpc.main.id

  # No inbound rules by default
  
  egress {
    from_port   = 443
    to_port     = 443
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
    description = "HTTPS outbound"
  }
  
  tags = {
    Name = "secure-security-group"
  }
}
```

### 2. Ansible Security

**Secure Ansible Playbook**
```yaml
---
- name: Secure server configuration
  hosts: webservers
  become: yes
  vars:
    ansible_python_interpreter: /usr/bin/python3
  
  tasks:
    - name: Ensure SSH key-based authentication
      lineinfile:
        path: /etc/ssh/sshd_config
        regexp: '^PasswordAuthentication'
        line: 'PasswordAuthentication no'
        state: present
      notify: restart ssh
      
    - name: Configure secure cipher suites
      lineinfile:
        path: /etc/ssh/sshd_config
        regexp: '^Ciphers'
        line: 'Ciphers aes256-gcm@openssh.com,chacha20-poly1305@openssh.com,aes256-ctr'
        state: present
      notify: restart ssh
      
    - name: Install and configure fail2ban
      package:
        name: fail2ban
        state: present
      
    - name: Configure fail2ban for SSH
      copy:
        content: |
          [sshd]
          enabled = true
          port = ssh
          filter = sshd
          logpath = /var/log/auth.log
          maxretry = 3
          bantime = 3600
        dest: /etc/fail2ban/jail.local
      notify: restart fail2ban
      
    - name: Ensure firewall is configured
      ufw:
        rule: allow
        port: '22'
        proto: tcp
        src: '{{ ansible_default_ipv4.network }}/24'
        
    - name: Enable firewall
      ufw:
        state: enabled
        policy: deny
        
  handlers:
    - name: restart ssh
      service:
        name: sshd
        state: restarted
        
    - name: restart fail2ban
      service:
        name: fail2ban
        state: restarted
```

### 3. CloudFormation Security

**Secure CloudFormation Template**
```yaml
AWSTemplateFormatVersion: '2010-09-09'
Description: 'Secure infrastructure template'

Parameters:
  VpcCidr:
    Type: String
    Default: '10.0.0.0/16'
    AllowedPattern: '^(([0-9]|[1-9][0-9]|1[0-9]{2}|2[0-4][0-9]|25[0-5])\.){3}([0-9]|[1-9][0-9]|1[0-9]{2}|2[0-4][0-9]|25[0-5])(\/(1[6-9]|2[0-8]))$'

Resources:
  # KMS Key for encryption
  KMSKey:
    Type: AWS::KMS::Key
    Properties:
      Description: 'KMS key for encryption'
      KeyPolicy:
        Statement:
          - Sid: Enable IAM User Permissions
            Effect: Allow
            Principal:
              AWS: !Sub 'arn:aws:iam::${AWS::AccountId}:root'
            Action: 'kms:*'
            Resource: '*'
          - Sid: Allow CloudTrail to encrypt logs
            Effect: Allow
            Principal:
              Service: cloudtrail.amazonaws.com
            Action:
              - kms:GenerateDataKey*
              - kms:DescribeKey
            Resource: '*'

  # Secure S3 Bucket
  SecureS3Bucket:
    Type: AWS::S3::Bucket
    Properties:
      BucketName: !Sub '${AWS::StackName}-secure-bucket'
      BucketEncryption:
        ServerSideEncryptionConfiguration:
          - ServerSideEncryptionByDefault:
              SSEAlgorithm: aws:kms
              KMSMasterKeyID: !Ref KMSKey
      PublicAccessBlockConfiguration:
        BlockPublicAcls: true
        BlockPublicPolicy: true
        IgnorePublicAcls: true
        RestrictPublicBuckets: true
      VersioningConfiguration:
        Status: Enabled
      LoggingConfiguration:
        DestinationBucketName: !Ref LoggingBucket
        LogFilePrefix: access-logs/
      LifecycleConfiguration:
        Rules:
          - Id: DeleteOldVersions
            Status: Enabled
            NoncurrentVersionExpirationInDays: 90

  # CloudTrail for audit logging
  CloudTrail:
    Type: AWS::CloudTrail::Trail
    Properties:
      TrailName: !Sub '${AWS::StackName}-cloudtrail'
      S3BucketName: !Ref SecureS3Bucket
      IncludeGlobalServiceEvents: true
      IsLogging: true
      IsMultiRegionTrail: true
      EnableLogFileValidation: true
      KMSKeyId: !Ref KMSKey
      EventSelectors:
        - ReadWriteType: All
          IncludeManagementEvents: true
          DataResources:
            - Type: AWS::S3::Object
              Values:
                - !Sub '${SecureS3Bucket}/*'
```

## Policy as Code Integration

### 1. Open Policy Agent (OPA) Integration

**OPA Policy for Terraform**
```rego
# terraform-policies/aws_security.rego
package terraform.aws

import rego.v1

# Deny S3 buckets without encryption
deny contains msg if {
    resource := input.resource.aws_s3_bucket[_]
    not resource.server_side_encryption_configuration
    msg := sprintf("S3 bucket '%s' must have encryption enabled", [resource.bucket])
}

# Deny EC2 instances without encryption
deny contains msg if {
    resource := input.resource.aws_instance[_]
    not resource.root_block_device[_].encrypted
    msg := sprintf("EC2 instance '%s' must have encrypted root volume", [resource.tags.Name])
}

# Deny security groups with wide open access
deny contains msg if {
    resource := input.resource.aws_security_group[_]
    rule := resource.ingress[_]
    rule.cidr_blocks[_] == "0.0.0.0/0"
    rule.from_port == 0
    rule.to_port == 65535
    msg := sprintf("Security group '%s' allows unrestricted access", [resource.name])
}
```

### 2. Conftest Integration

**Conftest Configuration**
```bash
# Install conftest
curl -L -o conftest.tar.gz https://github.com/open-policy-agent/conftest/releases/latest/download/conftest_Linux_x86_64.tar.gz
tar xzf conftest.tar.gz
sudo mv conftest /usr/local/bin

# Test Terraform plan
terraform plan -out=tfplan.binary
terraform show -json tfplan.binary > tfplan.json
conftest test --policy terraform-policies/ tfplan.json
```

## Secrets Management in IaC

### 1. HashiCorp Vault Integration

**Vault Provider Configuration**
```hcl
# Configure Vault provider
provider "vault" {
  address = "https://vault.example.com"
  token   = var.vault_token
}

# Retrieve secrets from Vault
data "vault_generic_secret" "db_password" {
  path = "secret/database"
}

# Use secrets in resources
resource "aws_db_instance" "main" {
  identifier = "main-db"
  
  engine         = "postgres"
  engine_version = "13.7"
  instance_class = "db.t3.micro"
  
  db_name  = "myapp"
  username = "admin"
  password = data.vault_generic_secret.db_password.data["password"]
  
  encrypted = true
  kms_key_id = aws_kms_key.rds_key.arn
  
  skip_final_snapshot = true
}
```

### 2. AWS Secrets Manager Integration

**Terraform with AWS Secrets Manager**
```hcl
# Create secret in AWS Secrets Manager
resource "aws_secretsmanager_secret" "db_password" {
  name_prefix = "myapp-db-password-"
  description = "Database password for MyApp"
  
  kms_key_id = aws_kms_key.secrets_key.arn
  
  replica {
    region = "us-west-2"
  }
}

resource "aws_secretsmanager_secret_version" "db_password" {
  secret_id = aws_secretsmanager_secret.db_password.id
  secret_string = jsonencode({
    username = "admin"
    password = random_password.db_password.result
  })
}

# Generate random password
resource "random_password" "db_password" {
  length  = 32
  special = true
}

# Use secret in RDS instance
data "aws_secretsmanager_secret_version" "db_password" {
  secret_id = aws_secretsmanager_secret.db_password.id
}

locals {
  db_creds = jsondecode(data.aws_secretsmanager_secret_version.db_password.secret_string)
}

resource "aws_db_instance" "main" {
  identifier = "main-db"
  
  engine         = "postgres"
  engine_version = "13.7"
  instance_class = "db.t3.micro"
  
  db_name  = "myapp"
  username = local.db_creds.username
  password = local.db_creds.password
  
  encrypted = true
  kms_key_id = aws_kms_key.rds_key.arn
  
  skip_final_snapshot = true
}
```

By implementing these Infrastructure as Code security practices, you establish a robust foundation for secure, compliant, and maintainable infrastructure deployments while integrating security throughout the development lifecycle.