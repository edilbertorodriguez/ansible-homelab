# Ansible Homelab

[![Ansible CI](https://github.com/edilbertorodriguez/ansible-homelab/actions/workflows/ansible-ci.yml/badge.svg)](https://github.com/edilbertorodriguez/ansible-homelab/actions/workflows/ansible-ci.yml)

Production-inspired Infrastructure as Code (IaC) laboratory built with **Ansible**, designed to automate Linux systems administration, infrastructure provisioning, and security hardening following modern DevOps and Systems Engineering practices.

This repository serves as my personal automation framework for managing my home lab while demonstrating professional Ansible development practices.

---

# Objectives

- Build reusable and idempotent Ansible roles.
- Automate Linux server deployment.
- Standardize system configuration across multiple hosts.
- Apply security hardening using Infrastructure as Code.
- Integrate Ansible with Terraform and Proxmox.
- Demonstrate CI/CD validation through GitHub Actions.

---

# Technologies

- Ansible
- GitHub Actions
- Ubuntu Server 24.04 LTS
- Proxmox VE
- Terraform (planned)
- Docker (planned)
- Tailscale (planned)

---

# Current Features

- Automated Ubuntu bootstrap playbook
- Reusable Common role
- Package management
- Timezone configuration
- GitHub Actions CI
- YAML validation
- ansible-lint validation
- Syntax validation
- Inventory organization following Ansible best practices

---

# Repository Structure

```text
ansible-homelab/
├── .github/
│   └── workflows/
├── group_vars/
├── host_vars/
├── inventories/
├── playbooks/
├── roles/
├── ansible.cfg
├── requirements.yml
└── README.md
```

---

# CI Pipeline

Every push automatically performs:

- YAML linting
- ansible-lint
- Ansible syntax validation

This helps ensure every commit maintains production-quality standards.

---

# Future Roadmap

- User management role
- SSH hardening
- UFW firewall role
- Docker role
- Tailscale role
- Proxmox automation
- Terraform integration
- Molecule testing
- Secrets management with Ansible Vault

---

# Author

**Edilberto Rodriguez**

Systems Administration • Infrastructure Engineering • Cybersecurity

GitHub:

https://github.com/edilbertorodriguez

---

# License

MIT License
