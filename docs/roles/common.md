# Common Role

## Overview

The **Common** role provides the baseline configuration applied to every managed Linux system.

Rather than performing every system administration task, the role establishes a consistent and secure operating environment upon which additional infrastructure roles can build.

The role is intentionally modular, allowing each capability to be enabled independently through feature flags.

---

# Responsibilities

The Common role currently manages:

- Hostname configuration
- System timezone
- OpenSSH daemon configuration
- Baseline package installation
- Validation tasks

The role serves as the foundation for every managed Linux host.

---

# Directory Structure

```
roles/common/
├── defaults/
│   └── main.yml
├── handlers/
│   └── main.yml
├── tasks/
│   ├── main.yml
│   ├── validation.yml
│   ├── hostname.yml
│   ├── timezone.yml
│   ├── sshd.yml
│   └── packages.yml
└── templates/
    └── sshd_config.j2
```

Each component has a single responsibility, making the role easier to understand, maintain, and extend.

---

# Execution Flow

The role executes tasks in the following order:

```
Validation
      │
      ▼
Hostname
      │
      ▼
Timezone
      │
      ▼
SSH
      │
      ▼
Packages
```

The sequence is intentional.

Validation occurs first, ensuring that prerequisites are satisfied before any configuration changes are attempted.

---

# Feature Flags

Major capabilities are controlled through inventory variables.

Example:

```yaml
common_manage_hostname: true

common_manage_timezone: true

common_manage_sshd: true
```

Feature flags allow hosts to opt into specific functionality without modifying the role itself.

Advantages include:

- Incremental deployments
- Host-specific customization
- Safer testing
- Reduced operational risk

---

# Variables

Configuration is centralized within the role defaults.

Examples include:

Hostname:

```yaml
common_manage_hostname
common_hostname
```

Timezone:

```yaml
common_manage_timezone
common_system_timezone
```

SSH:

```yaml
common_manage_sshd
common_sshd_port
common_sshd_password_authentication
common_sshd_pubkey_authentication
common_sshd_permit_root_login
common_sshd_client_alive_interval
common_sshd_client_alive_count_max
```

Packages:

```yaml
common_packages
common_update_cache
common_upgrade_packages
```

Keeping defaults centralized improves readability and simplifies future maintenance.

---

# SSH Management

The role configures OpenSSH using the supported drop-in configuration directory:

```
/etc/ssh/sshd_config.d/
```

instead of replacing:

```
/etc/ssh/sshd_config
```

This approach provides:

- Better compatibility with operating system updates
- Cleaner configuration management
- Reduced maintenance effort
- Lower upgrade risk

Before restarting the SSH service, the generated configuration is validated using:

```
sshd -t
```

This helps prevent invalid configurations from disrupting remote administrative access.

---

# Handlers

The role uses handlers to restart services only when configuration changes occur.

Current handlers include:

- Restart SSH service

This minimizes unnecessary service interruptions and supports idempotent execution.

---

# Templates

Templates are used when configuration files require variable substitution.

The Common role currently includes:

```
sshd_config.j2
```

Using Jinja2 templates allows the same configuration to be reused across multiple hosts while adapting to inventory-specific variables.

---

# Idempotency

Every task within the role is designed to be idempotent.

Executing the same playbook repeatedly should not introduce additional configuration changes once the desired state has been achieved.

Expected result:

```
changed=0
```

This behavior reduces operational risk and enables safe repeated deployments.

---

# Validation Strategy

The role incorporates multiple validation mechanisms.

These include:

- YAML validation
- ansible-lint
- Ansible syntax checking
- SSH configuration validation
- Manual functional testing
- Idempotency verification

This layered approach improves confidence before introducing changes into managed systems.

---

# Extending the Role

Future enhancements may include:

- NTP configuration
- Locale management
- MOTD configuration
- Auditd configuration
- System limits
- Sysctl tuning
- Automatic security updates
- Logging improvements

Each additional capability should follow the existing design principles:

- Single responsibility
- Feature flag support
- Idempotent implementation
- Configuration validation
- Handler-based service management

---

# Design Summary

The Common role establishes a consistent baseline for every managed Linux system.

Its design emphasizes:

- Modularity
- Readability
- Safety
- Maintainability
- Reusability
- Incremental deployment
- Infrastructure as Code best practices

As additional roles are introduced, the Common role will continue serving as the foundational layer of the repository.
