# Security in CI/CD Pipelines

## Overview

Integrating security into CI/CD pipelines is essential for modern DevSecOps practices. This approach enables early detection of security vulnerabilities, ensures compliance with security policies, and automates security testing throughout the development lifecycle.

## Core Security Gates in CI/CD

### 1. Source Code Security

**Static Application Security Testing (SAST)**
```yaml
# GitHub Actions example
name: SAST Scan
on: [push, pull_request]
jobs:
  sast:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Run Semgrep
        uses: returntocorp/semgrep-action@v1
        with:
          config: >-
            p/security-audit
            p/secrets
            p/owasp-top-ten
```

**Secret Scanning**
```yaml
# GitLab CI example
secret_detection:
  stage: test
  image: registry.gitlab.com/gitlab-org/security-products/analyzers/secrets:latest
  script:
    - /analyzer run
  artifacts:
    reports:
      secret_detection: gl-secret-detection-report.json
```

### 2. Dependency Security

**Software Composition Analysis (SCA)**
```yaml
# Jenkins pipeline example
pipeline {
    agent any
    stages {
        stage('Dependency Check') {
            steps {
                sh 'npm audit --audit-level=high'
                sh 'safety check --json > safety-report.json'
                publishHTML([
                    allowMissing: false,
                    alwaysLinkToLastBuild: false,
                    keepAll: true,
                    reportDir: '.',
                    reportFiles: 'safety-report.json',
                    reportName: 'Safety Report'
                ])
            }
        }
    }
}
```

### 3. Container Security

**Container Image Scanning**
```yaml
# Azure DevOps pipeline
trigger:
- main

pool:
  vmImage: 'ubuntu-latest'

steps:
- task: Docker@2
  displayName: 'Build Docker Image'
  inputs:
    command: 'build'
    dockerfile: '**/Dockerfile'
    tags: '$(Build.BuildId)'

- task: AquaSecurityTrivy@0
  displayName: 'Scan Docker Image'
  inputs:
    image: 'myapp:$(Build.BuildId)'
    exitCode: 1
```

### 4. Infrastructure Security

**Infrastructure as Code Scanning**
```yaml
# GitHub Actions for Terraform
name: Terraform Security
on: [push, pull_request]
jobs:
  terraform-security:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Run Checkov
        uses: bridgecrewio/checkov-action@master
        with:
          directory: .
          framework: terraform
          soft_fail: true
          
      - name: Run tfsec
        uses: aquasecurity/tfsec-pr-commenter-action@v1.2.0
        with:
          github_token: ${{ github.token }}
```

## Advanced Security Pipeline Patterns

### 1. Security Policy Enforcement

**Open Policy Agent (OPA) Integration**
```yaml
# Kubernetes admission controller
apiVersion: v1
kind: ConfigMap
metadata:
  name: security-policies
data:
  security.rego: |
    package kubernetes.admission
    
    deny[msg] {
      input.request.kind.kind == "Pod"
      input.request.object.spec.containers[_].securityContext.privileged == true
      msg := "Privileged containers are not allowed"
    }
    
    deny[msg] {
      input.request.kind.kind == "Pod"
      not input.request.object.spec.containers[_].securityContext.runAsNonRoot
      msg := "Containers must run as non-root user"
    }
```

### 2. Dynamic Application Security Testing (DAST)

**OWASP ZAP Integration**
```yaml
# GitLab CI DAST example
dast:
  stage: test
  image: owasp/zap2docker-stable
  script:
    - mkdir -p /zap/wrk
    - zap-baseline.py -t $CI_ENVIRONMENT_URL -r zap-report.html
  artifacts:
    reports:
      dast: zap-report.html
    expire_in: 1 week
  only:
    - schedules
```

### 3. Compliance as Code

**Compliance Scanning**
```yaml
# AWS Config Rules in CI/CD
version: 0.2
phases:
  install:
    runtime-versions:
      python: 3.8
  pre_build:
    commands:
      - pip install awscli boto3
  build:
    commands:
      - |
        # Check S3 bucket encryption
        aws configservice get-compliance-details-by-config-rule \
          --config-rule-name s3-bucket-server-side-encryption-enabled \
          --query 'EvaluationResults[?ComplianceType==`NON_COMPLIANT`]'
```

## Security Pipeline Best Practices

### 1. Fail-Fast Strategy
```yaml
# Progressive security checks
stages:
  - name: "Quick Security Checks"
    jobs:
      - secret_scan
      - lint_security
  - name: "Comprehensive Security"
    jobs:
      - sast_scan
      - dependency_check
  - name: "Integration Security"
    jobs:
      - dast_scan
      - compliance_check
```

### 2. Security Metrics and Reporting

**Security Dashboard Integration**
```python
# Python script for security metrics
import json
import requests

def publish_security_metrics(scan_results):
    metrics = {
        'critical_vulnerabilities': scan_results.get('critical', 0),
        'high_vulnerabilities': scan_results.get('high', 0),
        'medium_vulnerabilities': scan_results.get('medium', 0),
        'timestamp': datetime.now().isoformat()
    }
    
    # Send to monitoring system
    response = requests.post(
        'https://monitoring.example.com/api/security-metrics',
        json=metrics,
        headers={'Authorization': f'Bearer {os.getenv("MONITORING_TOKEN")}'}
    )
    
    return response.status_code == 200
```

### 3. Automated Security Response

**Automatic Issue Creation**
```yaml
# GitHub Actions for security issue creation
- name: Create Security Issue
  if: failure()
  uses: actions/github-script@v6
  with:
    script: |
      github.rest.issues.create({
        owner: context.repo.owner,
        repo: context.repo.repo,
        title: 'Security Vulnerability Detected',
        body: 'Security scan failed. Please review the pipeline logs.',
        labels: ['security', 'urgent']
      })
```

## Tool Integration Examples

### 1. Multi-Tool Security Pipeline

**Comprehensive Security Pipeline**
```yaml
name: Complete Security Pipeline
on: [push, pull_request]

jobs:
  security-scan:
    runs-on: ubuntu-latest
    steps:
      # Secret scanning
      - name: TruffleHog OSS
        uses: trufflesecurity/trufflehog@main
        with:
          path: ./
          base: main
          head: HEAD
          
      # SAST scanning
      - name: Semgrep
        uses: returntocorp/semgrep-action@v1
        
      # Dependency scanning
      - name: Snyk
        uses: snyk/actions/node@master
        env:
          SNYK_TOKEN: ${{ secrets.SNYK_TOKEN }}
          
      # Container scanning
      - name: Trivy
        uses: aquasecurity/trivy-action@master
        with:
          image-ref: 'myapp:latest'
          format: 'sarif'
          output: 'trivy-results.sarif'
          
      # Upload results
      - name: Upload SARIF
        uses: github/codeql-action/upload-sarif@v2
        with:
          sarif_file: trivy-results.sarif
```

### 2. Custom Security Gates

**Security Quality Gates**
```bash
#!/bin/bash
# custom-security-gate.sh

set -e

CRITICAL_THRESHOLD=0
HIGH_THRESHOLD=5
MEDIUM_THRESHOLD=20

# Parse scan results
CRITICAL=$(jq '.vulnerabilities[] | select(.severity=="CRITICAL")' scan-results.json | wc -l)
HIGH=$(jq '.vulnerabilities[] | select(.severity=="HIGH")' scan-results.json | wc -l)
MEDIUM=$(jq '.vulnerabilities[] | select(.severity=="MEDIUM")' scan-results.json | wc -l)

# Apply thresholds
if [ "$CRITICAL" -gt "$CRITICAL_THRESHOLD" ]; then
    echo "❌ Critical vulnerabilities exceed threshold: $CRITICAL > $CRITICAL_THRESHOLD"
    exit 1
fi

if [ "$HIGH" -gt "$HIGH_THRESHOLD" ]; then
    echo "❌ High vulnerabilities exceed threshold: $HIGH > $HIGH_THRESHOLD"
    exit 1
fi

if [ "$MEDIUM" -gt "$MEDIUM_THRESHOLD" ]; then
    echo "⚠️  Medium vulnerabilities exceed threshold: $MEDIUM > $MEDIUM_THRESHOLD"
    echo "Proceeding with warning..."
fi

echo "✅ Security gate passed"
```

## Security Pipeline Optimization

### 1. Parallel Scanning
```yaml
# Parallel security jobs
security-parallel:
  strategy:
    matrix:
      scan-type: [sast, secrets, dependencies, containers]
  runs-on: ubuntu-latest
  steps:
    - uses: actions/checkout@v3
    - name: Run Security Scan
      run: |
        case ${{ matrix.scan-type }} in
          sast) semgrep --config=auto . ;;
          secrets) trufflehog filesystem . ;;
          dependencies) safety check -r requirements.txt ;;
          containers) trivy image myapp:latest ;;
        esac
```

### 2. Caching and Optimization
```yaml
# Cache security databases
- name: Cache Trivy DB
  uses: actions/cache@v3
  with:
    path: ~/.cache/trivy
    key: ${{ runner.os }}-trivy-db-${{ hashFiles('**/Dockerfile') }}
    restore-keys: |
      ${{ runner.os }}-trivy-db-
```

By implementing these CI/CD security practices, you create a robust security pipeline that automatically identifies, reports, and helps remediate security issues throughout the development lifecycle.