# Ansible Homelab

[![Ansible CI](https://github.com/edilbertorodriguez/ansible-homelab/actions/workflows/ansible-ci.yml/badge.svg)](https://github.com/edilbertorodriguez/ansible-homelab/actions/workflows/ansible-ci.yml)

Production-inspired Infrastructure as Code (IaC) project built with **Ansible** to automate Linux systems administration, security hardening, and home lab infrastructure using modern Systems Engineering and DevOps practices.

This repository serves as my personal automation framework for managing my home lab while demonstrating production-quality Ansible development practices.

---

# Project Goals

- Develop reusable and idempotent Ansible roles.
- Standardize Linux server configuration across multiple hosts.
- Automate systems administration tasks.
- Apply infrastructure security best practices.
- Demonstrate Infrastructure as Code (IaC) methodologies.
- Build a maintainable automation framework suitable for enterprise environments.
- Integrate with Terraform, Proxmox, and monitoring platforms.

---

# Current Technologies

## Implemented

- Ansible Core
- Ubuntu Server 24.04 LTS
- GitHub Actions
- YAML
- Jinja2 Templates
- OpenSSH
- Proxmox VE

## Planned

- Terraform
- Puppet
- Docker
- Prometheus Node Exporter
- Wazuh Agent
- Tailscale
- Molecule
- Ansible Vault

---

# Current Features

## Common Role

- Linux bootstrap automation
- Baseline package management
- Hostname management
- Timezone management
- OpenSSH daemon management
- SSH configuration validation
- Handler-based service management
- Host-specific feature flags
- Idempotent configuration management

## Code Quality

- GitHub Actions CI
- ansible-lint (Production Profile)
- yamllint
- Ansible syntax validation
- Repeatable idempotency testing

---

# Design Principles

This project follows several engineering principles used in professional infrastructure environments:

- Infrastructure as Code (IaC)
- Idempotent automation
- Modular role architecture
- Feature flag driven deployments
- Vendor-supported configuration management
- Configuration validation before service restart
- Minimal external dependencies
- Incremental deployment strategy
- Reusable role design

---

# Repository Structure

```text
ansible-homelab/
├── .github/
│   └── workflows/
│       └── ansible-ci.yml
├── docs/
├── inventories/
│   └── homelab/
│       ├── group_vars/
│       ├── host_vars/
│       └── hosts.yml
├── playbooks/
│   ├── bootstrap.yml
│   ├── infrastructure.yml
│   ├── monitoring.yml
│   ├── security.yml
│   └── updates.yml
├── roles/
│   └── common/
│       ├── defaults/
│       ├── handlers/
│       ├── tasks/
│       └── templates/
├── ansible.cfg
├── site.yml
└── README.md
```

---

# Testing and Validation

Every change introduced into this repository is validated through multiple quality gates.

### Static Validation

- YAML syntax validation
- ansible-lint (Production Profile)
- Ansible syntax checks

### Functional Validation

- Idempotency verification (`changed=0`)
- SSH configuration validation (`sshd -t`)
- Manual deployment testing against lab infrastructure

### Continuous Integration

Every push automatically executes GitHub Actions to verify repository integrity before changes are accepted.

---

# Roadmap

## Completed

- Linux bootstrap automation
- Common role
- Package management
- Hostname management
- Timezone management
- SSH daemon management
- GitHub Actions pipeline

## In Progress

- Documentation
- Users role

## Planned

- User management
- SSH authorized keys
- Sudo management
- UFW firewall
- Fail2Ban
- Docker role
- Prometheus Node Exporter
- Wazuh Agent
- Proxmox automation
- Terraform integration
- Puppet integration
- Molecule testing
- Secrets management using Ansible Vault

---

# Future Repository Ecosystem

This repository is part of a larger Infrastructure Engineering portfolio.

| Repository | Purpose |
|------------|---------|
| ansible-homelab | Configuration Management |
| terraform-proxmox-lab | Infrastructure Provisioning |
| puppet-homelab *(planned)* | Continuous Configuration Enforcement |

Together these projects demonstrate a complete Infrastructure as Code workflow from provisioning to long-term configuration management.

---

# About This Project

Although this repository is built around a home lab, the architecture and engineering practices intentionally mirror production environments.

The objective is to demonstrate practical experience with:

- Linux Systems Administration
- Infrastructure Automation
- Configuration Management
- Infrastructure as Code
- Security Hardening
- DevOps Engineering

---

# Author

**Edilberto Rodriguez**

Systems Administration • Infrastructure Engineering • Cybersecurity

GitHub:

https://github.com/edilbertorodriguez

---

# License

MIT License
