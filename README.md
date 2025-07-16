# Modern Linux Security: A Comprehensive Guide for Cloud-Native and DevSecOps Environments

[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg)](https://github.com/tantainnovative/securing-production-servers-with-best-practices/pulls) [![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT) [![Twitter Follow](https://img.shields.io/twitter/follow/your_twitter_username.svg?style=social&label=Follow)](https://twitter.com/tantainnovative) [![LinkedIn](https://img.shields.io/badge/LinkedIn-Follow-blue.svg)](https://www.linkedin.com/company/tantainnovative)

Created by [@tantainnovative](https://twitter.com/tantainnovative) and contributors

A modern, comprehensive guide to securing Linux systems in cloud-native environments with DevSecOps practices, container security, and automated compliance. This resource provides practical, actionable steps for developers, engineers, and security professionals working with contemporary infrastructure.

### Why This Guide?

- **Modern Practices**: Updated for 2024+ with cloud-native security, container hardening, and DevSecOps integration
- **Developer-Focused**: Designed for engineers working with CI/CD pipelines, Infrastructure as Code, and automated deployments
- **Practical Implementation**: Real-world examples with working code for immediate implementation
- **Comprehensive Coverage**: From foundational security to advanced cloud-native and Kubernetes hardening
- **Automated Compliance**: Integration with modern compliance frameworks and automated security scanning

## Overview

This guide covers modern Linux security practices for cloud-native environments, emphasizing automation, DevSecOps integration, and contemporary threat landscapes. It bridges traditional Linux hardening with modern development practices, container security, and Infrastructure as Code.

For detailed introduction and objectives, see the [Introduction](docs/introduction.md).

## Table of Contents

### Core Security Foundations
1. **[Foundational Hardening Steps](docs/foundational/README.md)**
    - [System Inventory and Baseline Configuration](docs/foundational/system_inventory.md)
    - [BIOS/UEFI Security](docs/foundational/bios_uefi_security.md)
    - [Disk Encryption and Partitioning](docs/foundational/disk_encryption.md)
    - [Boot Directory Protection](docs/foundational/boot_directory_protection.md)
    - [Disabling Unnecessary USB Ports](docs/foundational/disabling_usb_ports.md)
    - [Regular System Updates](docs/foundational/system_updates.md)
    - [Modern Package Management & Supply Chain Security](docs/foundational/package_management.md)

2. **[Advanced Hardening Techniques](docs/advanced/README.md)**
    - [Secure SSH](docs/advanced/secure_ssh.md)
    - [Enable SELinux](docs/advanced/enable_selinux.md)
    - [Set Network Parameters](docs/advanced/network_parameters.md)

### Modern Security Practices
3. **[DevSecOps Integration](docs/devsecops/README.md)** - NEW
    - [Security in CI/CD Pipelines](docs/devsecops/ci_cd_security.md)
    - [Infrastructure as Code Security](docs/devsecops/infrastructure_as_code.md)
    - [Container Security in Development](docs/devsecops/container_security_dev.md)
    - [Secret Management](docs/devsecops/secret_management.md)
    - [Security Testing Automation](docs/devsecops/security_testing.md)
    - [Policy as Code](docs/devsecops/policy_as_code.md)

4. **[Modern Network Security](docs/network_security/README.md)**
    - [Modern Firewall Configuration with nftables](docs/network_security/firewall_configuration.md)
    - [Network Segmentation](docs/network_security/network_segmentation.md)
    - [Zero Trust Networking](docs/network_security/zero_trust.md)

### Application and Infrastructure Security
5. **[Application Hardening](docs/application_hardening/README.md)**
    - [Web Server Security](docs/application_hardening/web_server_security.md)
    - [Database Security](docs/application_hardening/database_security.md)
    - [Modern Container & Kubernetes Security](docs/application_hardening/container_security.md)

6. **[Security Monitoring and Logging](docs/monitoring_logging/README.md)**
    - [File and Directory Permissions](docs/monitoring_logging/file_directory_permissions.md)
    - [Centralized Logging](docs/monitoring_logging/centralized_logging.md)
    - [Audit Trails](docs/monitoring_logging/audit_trails.md)
    - [SIEM Integration](docs/monitoring_logging/siem_integration.md)

### Governance and Compliance
7. **[Backup and Disaster Recovery](docs/backup_disaster_recovery/README.md)**
    - [Backup Strategies](docs/backup_disaster_recovery/backup_strategies.md)
    - [Disaster Recovery Planning](docs/backup_disaster_recovery/disaster_recovery_planning.md)
    - [Cloud Backup Security](docs/backup_disaster_recovery/cloud_backup.md)

8. **[Compliance and Standards](docs/compliance_standards/README.md)**
    - [Adhering to Security Frameworks](docs/compliance_standards/adhering_to_frameworks.md)
    - [Compliance Auditing](docs/compliance_standards/compliance_auditing.md)
    - [Automated Compliance Checking](docs/compliance_standards/automated_compliance.md)

9. **[Automation and Tooling](docs/automation_tooling/README.md)**
    - [Automation Tools](docs/automation_tooling/automation_tools.md)
    - [Security Scanners](docs/automation_tooling/security_scanners.md)
    - [Modern Security Orchestration](docs/automation_tooling/security_orchestration.md)

10. **[Conclusion](docs/conclusion.md)**
11. **[References](docs/references.md)**

## Prerequisites

### Technical Requirements
- **Linux Administration**: Basic understanding of Linux systems and command-line usage
- **Development Experience**: Familiarity with CI/CD pipelines, version control (Git), and development workflows
- **Cloud Knowledge**: Understanding of cloud platforms (AWS, Azure, GCP) and containerization (Docker, Kubernetes)
- **Security Fundamentals**: Basic knowledge of security concepts and best practices

### Modern Development Context
This guide assumes you're working in environments with:
- Infrastructure as Code (Terraform, Ansible, CloudFormation)
- Container orchestration (Kubernetes, Docker Swarm)
- CI/CD pipelines (GitHub Actions, GitLab CI, Jenkins)
- Cloud-native applications and microservices
- DevSecOps practices and automation

### Recommended Tools
- **Container Runtime**: Docker or Podman
- **Orchestration**: Kubernetes (local or cloud-managed)
- **IaC Tools**: Terraform, Ansible, or similar
- **Security Scanners**: Trivy, Checkov, or equivalent
- **Version Control**: Git with platform integration (GitHub, GitLab, etc.)

## What's New in This Edition

### Major Updates
- **DevSecOps Integration**: Comprehensive CI/CD security pipeline examples
- **Container Security**: Modern Kubernetes hardening and runtime security
- **Infrastructure as Code**: Security scanning and policy enforcement
- **Cloud-Native Security**: Multi-cloud security practices and zero-trust architecture
- **Automated Compliance**: Continuous compliance monitoring and reporting
- **Modern Tooling**: Updated tools replacing deprecated solutions (nftables vs iptables, dnf vs yum)

### Target Audience
- **DevOps Engineers**: Implementing security in deployment pipelines
- **Security Engineers**: Modern security practices and automation
- **Platform Engineers**: Securing cloud-native infrastructure
- **Developers**: Security integration in development workflows
- **System Administrators**: Transitioning to modern security practices

## Quick Start

1. **Assessment**: Begin with [System Inventory](docs/foundational/system_inventory.md) to understand your current state
2. **Foundation**: Implement [Foundational Hardening](docs/foundational/README.md) steps
3. **Development Integration**: Set up [DevSecOps](docs/devsecops/README.md) practices
4. **Container Security**: Implement [Container & Kubernetes Security](docs/application_hardening/container_security.md)
5. **Monitoring**: Deploy [Security Monitoring](docs/monitoring_logging/README.md) solutions

## Contributing

We welcome contributions to this guide! If you have suggestions, improvements, or new content to add, please review
our [Contribution Guidelines](CONTRIBUTING.md) for more information on how to get involved.

## License

This guide is distributed under the [MIT License](LICENSE). See the license file for more details.
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

## Acknowledgments

A special thanks to all the contributors and sources that have helped shape this guide. Your expertise and insights are
invaluable to the community.

## Disclaimer

This guide is provided "as is" and is intended for educational purposes only. While we strive to provide accurate and
up-to-date information, we cannot guarantee the completeness or suitability of the content for any specific purpose.
Please use this guide at your own discretion and consider consulting with professional security experts for tailored
advice.

## References

For further reading and exploration, check out the [References](docs/references.md) section of this guide.
