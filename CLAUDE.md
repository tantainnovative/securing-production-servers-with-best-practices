# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a modern, comprehensive documentation project about Linux server security in cloud-native environments. The project focuses on DevSecOps practices, container security, Infrastructure as Code, and automated compliance, targeting developers and engineers working with contemporary infrastructure.

## Repository Structure

The repository follows a modern documentation structure organized around contemporary security practices:

```
docs/
├── foundational/          # Basic hardening steps (BIOS, disk encryption, modern package mgmt)
├── advanced/             # Advanced techniques (SSH, SELinux, network parameters)
├── devsecops/            # NEW: DevSecOps integration (CI/CD security, IaC, automation)
├── monitoring_logging/   # Security monitoring, logging, and SIEM integration
├── network_security/     # Modern firewall (nftables), zero-trust, cloud networking
├── application_hardening/ # Web server, database, container & Kubernetes security
├── backup_disaster_recovery/ # Backup strategies, disaster recovery, cloud backup
├── compliance_standards/ # Security frameworks, compliance auditing, automation
├── automation_tooling/   # Automation tools, security scanners, orchestration
├── introduction.md       # Guide introduction and objectives
├── conclusion.md         # Summary and final recommendations
└── references.md         # External resources and references
```

## Content Categories

### 1. Foundational Hardening Steps
- System inventory and baseline configuration
- BIOS/UEFI security settings
- Disk encryption and partitioning
- Boot directory protection
- USB port restrictions
- Modern package management & supply chain security

### 2. Advanced Hardening Techniques
- SSH security configuration
- SELinux implementation
- Network parameter tuning

### 3. DevSecOps Integration (NEW)
- Security in CI/CD pipelines
- Infrastructure as Code security
- Container security in development
- Secret management
- Security testing automation
- Policy as Code

### 4. Modern Network Security
- nftables firewall configuration (replaces iptables)
- Zero-trust networking
- Cloud-native network security
- Container network policies

### 5. Application Hardening
- Web server security
- Database security
- Modern container & Kubernetes security

### 6. Security Monitoring and Logging
- File and directory permissions
- Centralized logging systems
- Audit trail implementation
- SIEM integration

### 7. Backup and Disaster Recovery
- Backup strategies
- Disaster recovery planning
- Cloud backup security

### 8. Compliance and Standards
- Security framework adherence
- Compliance auditing procedures
- Automated compliance checking

### 9. Automation and Tooling
- Automation tools for security
- Security scanning tools
- Modern security orchestration

## Development Guidelines

### Documentation Standards
- All documentation is written in Markdown format
- Each section includes practical, actionable steps with modern tools
- Real-world examples focusing on CI/CD integration and automation
- Working code examples for immediate implementation
- Each topic includes rationale ("why") and modern context
- Cloud-native and container-focused examples

### Content Structure
- Each major section has a README.md file explaining the topic area
- Individual topics are covered in separate .md files
- Cross-references between related topics are maintained
- Prerequisites include modern development tools and practices
- Integration examples for popular CI/CD platforms

### Writing Style
- Technical but accessible for developers and engineers
- Step-by-step instructions with modern tool integration
- Command-line examples with proper formatting
- Security implications explained for contemporary threats
- Adaptability guidance for cloud and container environments
- Focus on automation and DevSecOps practices

## Common Tasks

Since this is a documentation project, there are no build, test, or lint commands. Common tasks include:

- **Content Review**: Read through documentation files to understand current content
- **Documentation Updates**: Edit existing .md files to improve or update content
- **New Content Creation**: Add new sections or topics following the established structure
- **Cross-Reference Updates**: Ensure links between documents remain valid
- **Formatting Consistency**: Maintain consistent Markdown formatting across all files
- **Modern Practice Integration**: Update content to reflect contemporary security practices
- **Code Example Updates**: Ensure all code examples work with modern tools and platforms
- **CI/CD Integration**: Add examples for popular CI/CD platforms and automation tools

## Quality Standards

- Ensure all security recommendations are current and follow modern best practices
- Verify that command-line examples are accurate and tested with current tools
- Maintain consistency in terminology and formatting
- Provide context for why each hardening step is important in modern environments
- Include warnings about potential impacts of security changes
- Focus on cloud-native, container, and DevSecOps practices
- Ensure all tool recommendations are current and actively maintained
- Include practical CI/CD integration examples
- Verify compatibility with modern Linux distributions and cloud platforms

## Target Audience

The documentation is designed for:
- DevOps engineers implementing security in deployment pipelines
- Security engineers adopting modern security practices and automation
- Platform engineers securing cloud-native infrastructure
- Developers integrating security into development workflows
- System administrators transitioning to modern security practices
- Anyone responsible for cloud-native and containerized security

Content assumes basic Linux administration knowledge, familiarity with modern development practices (CI/CD, containers, cloud), and explains security concepts in the context of contemporary infrastructure.

## Modern Tools and Practices

The guide emphasizes modern tools and practices including:
- **Container Security**: Docker, Kubernetes, container registries
- **Infrastructure as Code**: Terraform, Ansible, CloudFormation
- **CI/CD Security**: GitHub Actions, GitLab CI, Jenkins
- **Security Scanning**: Trivy, Checkov, Terrascan, KICS
- **Cloud Platforms**: AWS, Azure, GCP security services
- **Modern Linux Tools**: nftables (vs iptables), dnf (vs yum)
- **Policy as Code**: OPA, Falco, Gatekeeper
- **Secret Management**: HashiCorp Vault, cloud-native solutions
- **Monitoring**: Prometheus, Grafana, cloud-native observability