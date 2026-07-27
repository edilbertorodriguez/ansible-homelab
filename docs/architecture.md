# Architecture

## Overview

This repository is designed following Infrastructure as Code (IaC) principles to provide a modular, maintainable, and repeatable framework for Linux systems administration and infrastructure automation.

Rather than focusing solely on automation, the project emphasizes software engineering practices commonly found in production infrastructure environments, including modular design, idempotent execution, configuration validation, and continuous integration.

The architecture intentionally favors readability, maintainability, and incremental deployment over monolithic automation.

---

# Architectural Goals

The repository is designed to achieve the following objectives:

- Standardize Linux server configuration.
- Minimize configuration drift.
- Promote reusable automation.
- Reduce operational risk during deployments.
- Support incremental infrastructure growth.
- Demonstrate professional Infrastructure as Code practices.

---

# Design Principles

## Infrastructure as Code

All system configuration is managed through version-controlled Ansible playbooks and roles.

Benefits include:

- Repeatable deployments
- Version history
- Peer review
- Easier disaster recovery
- Reduced manual configuration

---

## Idempotent Automation

Every task is designed so that executing the same playbook multiple times produces the same desired system state.

An idempotent playbook should report no changes (`changed=0`) when the target system is already configured correctly.

This approach reduces unnecessary service interruptions and makes repeated executions safe.

---

## Modular Role Architecture

Automation is organized into independent Ansible roles instead of large monolithic playbooks.

Example:

```
Common Role
├── Hostname
├── Timezone
├── SSH
├── Packages
└── Validation
```

Advantages:

- Easier maintenance
- Better code reuse
- Independent testing
- Clear separation of responsibilities
- Simpler future expansion

---

## Feature Flag Deployments

Every major capability can be enabled or disabled independently through inventory variables.

Example:

```yaml
common_manage_hostname: true
common_manage_timezone: true
common_manage_sshd: true
```

Benefits:

- Safe incremental deployments
- Host-specific customization
- Reduced deployment risk
- Easier testing of new features

---

## Configuration Validation

Configuration files should be validated before restarting critical services.

For example, SSH configuration changes are validated using:

```
sshd -t
```

before the SSH service is restarted.

This reduces the likelihood of deploying invalid configurations that could interrupt remote administrative access.

---

## Handler-Based Service Management

Services are restarted only when configuration changes occur.

This minimizes unnecessary service interruptions and aligns with Ansible best practices.

---

## Vendor-Supported Configuration

Whenever possible, configuration changes are implemented using vendor-supported mechanisms.

For example, OpenSSH is configured through:

```
/etc/ssh/sshd_config.d/
```

instead of replacing the primary `sshd_config` file.

Advantages include:

- Better compatibility with operating system updates
- Easier maintenance
- Lower upgrade risk
- Cleaner separation between vendor and administrator configuration

---

## Minimal External Dependencies

The project intentionally relies on Ansible built-in modules whenever practical.

Reducing external collections simplifies deployment, minimizes dependency management, and improves long-term maintainability.

---

## Inventory Organization

Infrastructure is grouped according to functional responsibility rather than physical location.

Current logical groups include:

- Linux systems
- Infrastructure services
- Monitoring and security
- Network infrastructure

This organization allows playbooks to target entire categories of systems while remaining scalable as additional hosts are introduced.

---

# Continuous Integration

Every change introduced into the repository passes automated validation before becoming part of the project.

Current validation includes:

- YAML validation
- ansible-lint
- Ansible syntax validation
- Manual idempotency verification
- Functional testing against laboratory systems

Future improvements will include Molecule integration and automated functional testing.

---

# Future Architecture

The long-term vision extends beyond Ansible alone.

This repository will become part of a broader Infrastructure as Code ecosystem.

```
Terraform
        │
Provision Infrastructure
        │
        ▼
Ansible
        │
Configure Systems
        │
        ▼
Puppet
        │
Maintain Desired State
```

Together, these repositories will demonstrate the complete infrastructure lifecycle:

1. Infrastructure provisioning
2. Configuration management
3. Continuous configuration enforcement

---

# Guiding Philosophy

Every implementation within this repository is evaluated using the following questions:

- Is the solution repeatable?
- Is it idempotent?
- Is it maintainable?
- Is it safe to deploy?
- Does it minimize operational risk?
- Would this approach be appropriate in a production environment?

When multiple valid solutions exist, the implementation that best satisfies these principles is preferred.
