# Security Role

## Overview

The **security** role provides a modular security baseline for Debian-based Linux systems.

Rather than enforcing every hardening control at once, the role allows each security capability to be enabled or disabled independently through feature flags. This design keeps the role reusable across different environments while maintaining predictable and idempotent behavior.

Current capabilities include:

- Platform validation
- Automatic security updates
- Fail2ban SSH protection

Future capabilities will include:

- Auditd
- Chrony
- Kernel hardening (sysctl)
- Host firewall (UFW)

---

# Responsibilities

The security role is responsible for:

- Validating supported operating systems
- Installing security-related packages
- Configuring unattended security updates
- Protecting SSH against brute-force attacks with Fail2ban
- Maintaining idempotent security configuration

The role intentionally avoids managing user accounts, SSH daemon configuration, or general operating system configuration, which are handled by other roles.

---

# Directory Layout

```text
roles/security/

├── defaults/
│   └── main.yml
│
├── handlers/
│   └── main.yml
│
├── tasks/
│   ├── main.yml
│   ├── validation.yml
│   ├── packages.yml
│   ├── unattended_upgrades.yml
│   ├── fail2ban.yml
│   ├── auditd.yml
│   ├── chrony.yml
│   ├── sysctl.yml
│   └── firewall.yml
│
├── templates/
│
└── files/
```

---

# Feature Flags

Each capability can be enabled independently.

| Variable | Default | Description |
|-----------|---------|-------------|
| security_manage_packages | false | Install optional shared security packages |
| security_manage_unattended_upgrades | true | Configure unattended security updates |
| security_manage_fail2ban | false | Configure Fail2ban |
| security_manage_auditd | false | Configure Linux Audit Framework |
| security_manage_chrony | false | Configure Chrony time synchronization |
| security_manage_sysctl | false | Apply kernel hardening |
| security_manage_firewall | false | Configure UFW firewall |

---

# Configuration Variables

## Unattended Update Packages

```yaml
security_unattended_packages:
  - unattended-upgrades
  - apt-listchanges
```

These packages are installed whenever unattended upgrades are enabled.

---

## Shared Security Packages

```yaml
security_packages: []
```

This list is intentionally empty by default.

Individual security components install their own required packages.

The list exists for optional security tooling that should always be present regardless of enabled security features.

Examples:

```yaml
security_packages:
  - lynis
  - needrestart
  - debsums
```

---

# Validation

Before making any configuration changes, the role verifies:

- Debian-based operating system
- APT package manager

If validation fails, execution stops immediately.

---

# Automatic Security Updates

When enabled, the role:

- Installs unattended-upgrades
- Installs apt-listchanges
- Creates:

```text
/etc/apt/apt.conf.d/20auto-upgrades
```

Configuration:

```text
APT::Periodic::Update-Package-Lists "1";
APT::Periodic::Unattended-Upgrade "1";
```

This enables automatic installation of security updates using the native Debian/Ubuntu update mechanism.

---

# Fail2ban

When enabled, the role:

- Installs Fail2ban
- Deploys an SSH jail configuration
- Enables the service
- Starts the service
- Restarts Fail2ban automatically when configuration changes

Current SSH jail configuration:

- backend: systemd
- maxretry: 5
- findtime: 10 minutes
- bantime: 1 hour

Configuration is stored in:

```text
/etc/fail2ban/jail.d/sshd.local
```

---

# Handlers

The role currently includes:

```
Restart Fail2ban
```

Handlers execute only when configuration changes occur.

---

# Idempotency

The role was validated on Ubuntu Server 24.04.

Validation included:

- Syntax validation
- ansible-lint (production profile)
- Functional deployment
- Service verification
- Repeated execution

Second execution produced:

```text
changed=0
failed=0
```

demonstrating full idempotency.

---

# Testing

The role has been validated by confirming:

- Fail2ban service enabled
- Fail2ban service running
- SSH jail loaded
- Automatic updates configured
- Handler execution
- Idempotent behavior

Verification commands:

```bash
systemctl is-enabled fail2ban

systemctl is-active fail2ban

sudo fail2ban-client status

sudo fail2ban-client status sshd

apt-config dump | grep Periodic
```

---

# Design Decisions

The role follows several design principles.

## Modular Features

Each security capability is isolated into its own task file.

Examples:

- fail2ban.yml
- unattended_upgrades.yml
- auditd.yml

This minimizes coupling between security controls.

---

## Feature Flags

Every capability can be enabled independently through inventory variables.

This allows different environments to consume only the security controls they require.

---

## Idempotent Configuration

Every task is designed to be repeatable without producing unnecessary changes.

---

## Built-in Modules

The role uses only Ansible built-in modules.

No external collections are required.

---

## Validation Before Configuration

The role validates prerequisites before making system modifications.

This provides early failure and predictable execution.

---

# Future Enhancements

Planned capabilities include:

- Linux Audit Framework (auditd)
- Chrony
- Kernel hardening
- UFW firewall
- Additional security utilities
- CIS benchmark alignment
- Automated compliance validation

---

# Status

Current implementation:

- ✅ Platform validation
- ✅ Automatic security updates
- ✅ Fail2ban
- ⏳ Auditd
- ⏳ Chrony
- ⏳ Sysctl hardening
- ⏳ UFW firewall
