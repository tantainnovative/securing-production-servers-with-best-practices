# Modern Container & Kubernetes Security

## Overview

Container security in 2024 encompasses comprehensive protection across the entire container lifecycle - from development through runtime. This guide covers modern security practices for containers, Kubernetes, and cloud-native environments with emphasis on automation, compliance, and zero-trust principles.

## Modern Container Security Landscape

- **Supply Chain Security:** Protecting against compromised base images and malicious dependencies
- **Runtime Protection:** Real-time threat detection and response in containerized environments
- **Policy Enforcement:** Automated compliance and security policy enforcement
- **Zero-Trust Architecture:** Implementing zero-trust principles in container networks
- **Cloud-Native Integration:** Seamless integration with cloud security services
- **DevSecOps Integration:** Security embedded throughout the development lifecycle

## Modern Container Security Framework

### 1. Secure Container Build Process

**Distroless and Minimal Images**
```dockerfile
# Use distroless base images
FROM gcr.io/distroless/java:11

# Multi-stage build for minimal attack surface
FROM maven:3.8-openjdk-11 AS builder
WORKDIR /app
COPY pom.xml .
RUN mvn dependency:go-offline
COPY src ./src
RUN mvn clean package -DskipTests

FROM gcr.io/distroless/java:11
COPY --from=builder /app/target/app.jar /app.jar
USER 1000:1000
ENTRYPOINT ["java", "-jar", "/app.jar"]
```

**Automated Vulnerability Scanning**
```yaml
# GitHub Actions for container scanning
name: Container Security Scan
on: [push, pull_request]

jobs:
  container-scan:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Build image
        run: docker build -t myapp:${{ github.sha }} .
      
      - name: Run Trivy scanner
        uses: aquasecurity/trivy-action@master
        with:
          image-ref: 'myapp:${{ github.sha }}'
          format: 'sarif'
          output: 'trivy-results.sarif'
      
      - name: Upload Trivy results
        uses: github/codeql-action/upload-sarif@v2
        with:
          sarif_file: 'trivy-results.sarif'
```

### 2. Kubernetes Security Hardening

**Pod Security Standards**
```yaml
# Restricted Pod Security Standard
apiVersion: v1
kind: Pod
metadata:
  name: secure-pod
spec:
  securityContext:
    runAsNonRoot: true
    runAsUser: 1000
    runAsGroup: 1000
    fsGroup: 2000
    seccompProfile:
      type: RuntimeDefault
  containers:
  - name: app
    image: myapp:latest
    securityContext:
      allowPrivilegeEscalation: false
      readOnlyRootFilesystem: true
      capabilities:
        drop:
        - ALL
    resources:
      requests:
        memory: "64Mi"
        cpu: "250m"
      limits:
        memory: "128Mi"
        cpu: "500m"
    volumeMounts:
    - name: tmp
      mountPath: /tmp
    - name: var-run
      mountPath: /var/run
  volumes:
  - name: tmp
    emptyDir: {}
  - name: var-run
    emptyDir: {}
```

**Network Policies**
```yaml
# Deny all ingress traffic by default
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: deny-all-ingress
spec:
  podSelector: {}
  policyTypes:
  - Ingress
---
# Allow specific traffic only
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-frontend-to-backend
spec:
  podSelector:
    matchLabels:
      app: backend
  policyTypes:
  - Ingress
  ingress:
  - from:
    - podSelector:
        matchLabels:
          app: frontend
    ports:
    - protocol: TCP
      port: 8080
```

### 3. Runtime Security Monitoring

**Falco Runtime Security**
```yaml
# Falco custom rules
- rule: Detect shell execution in container
  desc: Detect shell execution in container
  condition: >
    spawned_process and container and
    (proc.name in (shell_binaries) or
     proc.name in (bash, sh, zsh, fish))
  output: >
    Shell spawned in container (user=%user.name container=%container.name
    image=%container.image.repository proc=%proc.cmdline)
  priority: WARNING
  tags: [container, shell, mitre_execution]

- rule: Detect privilege escalation
  desc: Detect privilege escalation attempts
  condition: >
    spawned_process and container and
    proc.name in (sudo, su, doas) and
    not user.name in (allowed_users)
  output: >
    Privilege escalation attempt (user=%user.name container=%container.name
    image=%container.image.repository proc=%proc.cmdline)
  priority: CRITICAL
  tags: [container, privilege_escalation]
```

### 4. Advanced Secret Management

**External Secrets Operator**
```yaml
# HashiCorp Vault integration
apiVersion: external-secrets.io/v1beta1
kind: SecretStore
metadata:
  name: vault-backend
spec:
  provider:
    vault:
      server: "https://vault.example.com"
      path: "secret"
      version: "v2"
      auth:
        kubernetes:
          mountPath: "kubernetes"
          role: "example-role"
---
apiVersion: external-secrets.io/v1beta1
kind: ExternalSecret
metadata:
  name: app-secrets
spec:
  refreshInterval: 1h
  secretStoreRef:
    name: vault-backend
    kind: SecretStore
  target:
    name: app-secrets
  data:
  - secretKey: database-password
    remoteRef:
      key: database
      property: password
```

### 5. Supply Chain Security

**Container Image Signing**
```bash
# Sign container images with cosign
cosign generate-key-pair
cosign sign --key cosign.key myregistry.com/myapp:v1.0.0

# Verify signatures in admission controller
kubectl apply -f - <<EOF
apiVersion: config.sigstore.dev/v1alpha1
kind: ClusterImagePolicy
metadata:
  name: image-policy
spec:
  images:
  - glob: "myregistry.com/*"
  authorities:
  - keyless:
      url: https://fulcio.sigstore.dev
      ca-cert:
        data: |
          -----BEGIN CERTIFICATE-----
          ...
          -----END CERTIFICATE-----
EOF
```

### 6. Policy as Code with OPA Gatekeeper

**Custom Security Policies**
```yaml
# Gatekeeper constraint template
apiVersion: templates.gatekeeper.sh/v1beta1
kind: ConstraintTemplate
metadata:
  name: k8srequiredsecuritycontext
spec:
  crd:
    spec:
      names:
        kind: K8sRequiredSecurityContext
      validation:
        openAPIV3Schema:
          type: object
          properties:
            runAsNonRoot:
              type: boolean
  targets:
    - target: admission.k8s.gatekeeper.sh
      rego: |
        package k8srequiredsecuritycontext
        
        violation[{"msg": msg}] {
          container := input.review.object.spec.containers[_]
          not container.securityContext.runAsNonRoot
          msg := "Container must run as non-root user"
        }
---
apiVersion: constraints.gatekeeper.sh/v1beta1
kind: K8sRequiredSecurityContext
metadata:
  name: must-run-as-non-root
spec:
  match:
    kinds:
    - apiGroups: [""]
      kinds: ["Pod"]
  parameters:
    runAsNonRoot: true
```

### 7. Container Runtime Security

**gVisor for Enhanced Isolation**
```yaml
# RuntimeClass for gVisor
apiVersion: node.k8s.io/v1
kind: RuntimeClass
metadata:
  name: gvisor
handler: runsc
---
apiVersion: v1
kind: Pod
metadata:
  name: secure-workload
spec:
  runtimeClassName: gvisor
  containers:
  - name: app
    image: myapp:latest
```

### 8. Compliance and Auditing

**CIS Benchmark Compliance**
```bash
# Use kube-bench for CIS compliance
kubectl apply -f https://raw.githubusercontent.com/aquasecurity/kube-bench/main/job.yaml

# View results
kubectl logs job/kube-bench

# Automated compliance checking
kubectl apply -f - <<EOF
apiVersion: batch/v1
kind: CronJob
metadata:
  name: compliance-check
spec:
  schedule: "0 2 * * *"
  jobTemplate:
    spec:
      template:
        spec:
          containers:
          - name: kube-bench
            image: aquasec/kube-bench:latest
            command: ["kube-bench", "--json"]
          restartPolicy: OnFailure
EOF
```

## Cloud-Native Security Integration

### Multi-Cloud Security
```yaml
# AWS EKS Pod Identity
apiVersion: v1
kind: ServiceAccount
metadata:
  name: my-service-account
  annotations:
    eks.amazonaws.com/role-arn: arn:aws:iam::123456789012:role/my-role

# Azure AKS Workload Identity
apiVersion: v1
kind: ServiceAccount
metadata:
  name: workload-identity-sa
  annotations:
    azure.workload.identity/client-id: "12345678-1234-1234-1234-123456789012"
```

### Zero Trust Container Networking
```yaml
# Istio service mesh security
apiVersion: security.istio.io/v1beta1
kind: PeerAuthentication
metadata:
  name: default
spec:
  mtls:
    mode: STRICT
---
apiVersion: security.istio.io/v1beta1
kind: AuthorizationPolicy
metadata:
  name: allow-frontend
spec:
  selector:
    matchLabels:
      app: backend
  rules:
  - from:
    - source:
        principals: ["cluster.local/ns/default/sa/frontend"]
    to:
    - operation:
        methods: ["GET", "POST"]
```

## Modern Security Tools

### Comprehensive Security Platform
- **Falco**: Runtime security monitoring and threat detection
- **OPA Gatekeeper**: Policy enforcement and compliance
- **Trivy**: Vulnerability scanning and SBOM generation
- **Cosign**: Container image signing and verification
- **Istio**: Service mesh security and zero-trust networking
- **External Secrets**: Centralized secret management

### Observability and Monitoring
```yaml
# Prometheus security metrics
apiVersion: monitoring.coreos.com/v1
kind: ServiceMonitor
metadata:
  name: falco-metrics
spec:
  selector:
    matchLabels:
      app: falco
  endpoints:
  - port: metrics
    interval: 30s
    path: /metrics
```

## Emergency Response Procedures

### Incident Response Automation
```yaml
# Automated pod quarantine
apiVersion: v1
kind: ConfigMap
metadata:
  name: incident-response
data:
  quarantine.sh: |
    #!/bin/bash
    NAMESPACE=$1
    POD=$2
    
    # Isolate the pod
    kubectl label pod $POD quarantine=true -n $NAMESPACE
    
    # Apply restrictive network policy
    kubectl apply -f quarantine-netpol.yaml
    
    # Collect forensics
    kubectl exec $POD -n $NAMESPACE -- ps aux > forensics-$POD.txt
    kubectl logs $POD -n $NAMESPACE > logs-$POD.txt
```

### Compliance Reporting
```bash
# Generate compliance reports
kubectl get pods --all-namespaces -o json | \
  jq '.items[] | select(.spec.securityContext.runAsNonRoot != true) | \
  {name: .metadata.name, namespace: .metadata.namespace, issue: "not_running_as_non_root"}'
```

By implementing these modern container and Kubernetes security practices, you establish a comprehensive security posture that protects against contemporary threats while enabling secure, scalable container operations in cloud-native environments.
