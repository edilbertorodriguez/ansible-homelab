# Security Role

## Overview

The **security** role provides a modular security baseline for Debian-based Linux systems.

Rather than enforcing every hardening control at once, the role allows each security capability to be enabled or disabled independently through feature flags. This design keeps the role reusable across different environments while maintaining predictable and idempotent behavior.

Current capabilities include:

- Platform validation
- Automatic security updates
- Fail2ban
- Linux Audit Framework (Auditd)
- Chrony time synchronization
- Sysctl kernel hardening

Future capabilities will include:

- Host firewall (UFW)

---

# Responsibilities

The security role is responsible for:

- Validating supported operating systems
- Installing packages required by enabled security features
- Configuring unattended security updates
- Protecting SSH against brute-force attacks with Fail2ban
- Configuring Linux Audit Framework (Auditd)
- Configuring secure time synchronization with Chrony
- Applying baseline kernel and network hardening through Sysctl
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

# Auditd

When enabled, the role:

- Installs Auditd
- Deploys a managed audit rules file
- Enables the service
- Starts the service
- Reloads audit rules automatically when configuration changes

The managed rules monitor:

- User account database changes
- Privileged configuration changes
- SSH daemon configuration
- Audit log modifications
- File deletion activity performed by interactive users

Configuration is stored in:

```text
/etc/audit/rules.d/99-security.rules
```

Audit rules are loaded using:

```bash
augenrules --load
```

The role intentionally manages a dedicated rules file instead of modifying distribution-provided rules. This approach simplifies maintenance, improves portability, and keeps all managed Auditd configuration isolated from operating system defaults.

---

# Chrony

When enabled, the role:

- Installs Chrony
- Deploys a managed Chrony configuration
- Enables the service
- Starts the service
- Restarts Chrony automatically when configuration changes

By default, the role configures the following public NTP servers:

- time.cloudflare.com
- time.google.com

Configuration is generated from an Ansible template and stored in:

```text
/etc/chrony/chrony.conf
```

The list of NTP servers can be customized through the `security_chrony_servers` variable.

By default, the role configures Chrony as an NTP client only. Networks allowed to synchronize with this host can be defined through the `security_chrony_allow_networks` variable if the server is intended to act as an internal NTP server.

---

# Sysctl Hardening

When enabled, the role:

- Generates a managed Sysctl configuration
- Deploys the configuration to `/etc/sysctl.d/99-security.conf`
- Applies the settings through `sysctl --system`
- Reloads kernel parameters only when the configuration changes

The default baseline includes controls for:

- ICMP redirect handling
- Source-routed packets
- Martian packet logging
- Broadcast ICMP handling
- Bogus ICMP error responses
- Address Space Layout Randomization (ASLR)
- Core dumps from privileged processes

The parameters are defined through the `security_sysctl_parameters` variable, allowing inventory-specific overrides without modifying the role.

The role uses a dedicated drop-in file instead of editing `/etc/sysctl.conf`, keeping Ansible-managed settings isolated from distribution and administrator defaults.

---

## UFW firewall

The role can install, configure, and enable UFW on managed Linux systems.

Firewall management is controlled with:

```yaml
security_manage_firewall: true
```

The role applies the following default policies:

```yaml
security_firewall_default_incoming_policy: deny
security_firewall_default_outgoing_policy: allow
security_firewall_default_routed_policy: deny
```

SSH access is configured before the firewall is enabled to reduce the risk of locking out the administrator.

Example:

```yaml
security_firewall_allow_ssh: true
security_firewall_ssh_port: 22
security_firewall_ssh_source: 10.10.60.0/24
```

Additional allowed services can be defined with:

```yaml
security_firewall_allowed_rules:
  - port: 443
    proto: tcp
    src: 10.10.60.0/24
    comment: Allow HTTPS from the lab network

  - port: 53
    proto: udp
    src: 10.10.60.0/24
    comment: Allow DNS from the lab network
```

The firewall tasks are executed in this order:

1. Install UFW.
2. Configure default policies.
3. Add the SSH allow rule.
4. Add any additional allow rules.
5. Enable UFW.

This ordering ensures that required administrative access is permitted before the firewall becomes active.

### Verification

Check the active firewall configuration with:

```bash
sudo ufw status verbose
```

Expected lab configuration:

```text
Status: active
Logging: on (low)
Default: deny (incoming), allow (outgoing), disabled (routed)

To                         Action      From
--                         ------      ----
22/tcp                     ALLOW IN    10.10.60.0/24
```

The role was verified for idempotency. A second execution completed with:

```text
changed=0
failed=0
```

---

# Handlers

The role currently includes:

- `Restart Fail2ban`
- `Load Auditd rules`
- `Restart Chrony`
- `Reload Sysctl settings`

Handlers execute only when configuration changes occur, ensuring services are only restarted or reloaded when necessary.

---

# Idempotency

The role was validated on Ubuntu Server 24.04.

Validation included:

- Syntax validation
- ansible-lint (production profile)
- Functional deployment
- Service verification
- Repeated execution

A second execution produced:

```text
changed=0
failed=0
```

demonstrating that the role is fully idempotent.

---

# Testing

The role has been validated by confirming:

- Fail2ban service enabled
- Fail2ban service running
- SSH jail loaded
- Automatic updates configured
- Auditd service enabled
- Auditd service running
- Audit rules successfully loaded
- Chrony service enabled
- Chrony service running
- Managed Sysctl configuration deployed
- Persistent Sysctl values verified
- Live kernel values verified
- Handler execution
- Idempotent behavior

Verification commands:

```bash
systemctl is-enabled fail2ban

systemctl is-active fail2ban

sudo fail2ban-client status

sudo fail2ban-client status sshd

apt-config dump | grep Periodic

systemctl is-enabled auditd

systemctl is-active auditd

sudo auditctl -l

systemctl is-enabled chrony

systemctl is-active chrony

chronyc tracking

chronyc sources -v

cat /etc/sysctl.d/99-security.conf

sysctl net.ipv4.conf.all.accept_redirects

sysctl net.ipv4.conf.all.log_martians

sysctl kernel.randomize_va_space

sysctl fs.suid_dumpable
```

---

# Design Decisions

The role follows several design principles.

## Modular Features

Each security capability is isolated into its own task file.

Examples:

- validation.yml
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

- Kernel hardening (sysctl)
- UFW firewall
- Additional security utilities (e.g., Lynis, AIDE)
- CIS Benchmark alignment
- Automated compliance validation

---

# Status

Implemented capabilities:

- ✅ Platform validation
- ✅ Automatic security updates
- ✅ Fail2ban
- ✅ Auditd
- ✅ Chrony
- ✅ Sysctl hardening
- ⏳ UFW firewall
