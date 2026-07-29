# Users Role

## Overview

The `users` role manages local Linux user accounts in a declarative and idempotent manner.

Its primary responsibilities include:

- Creating and maintaining local user accounts.
- Managing supplementary Linux groups.
- Deploying SSH public keys.
- Managing sudo privileges.
- Validating user definitions before making changes.

The role is designed to be modular, predictable, and reusable across development, laboratory, and production environments.

---

## Responsibilities

The role is responsible for:

- Validating user configuration.
- Creating required Linux groups.
- Creating and maintaining user accounts.
- Deploying managed SSH public keys.
- Managing sudo policies.
- Ensuring idempotent execution.

The role intentionally does **not** manage:

- User passwords.
- SSH key generation.
- LDAP or Active Directory accounts.
- Centralized identity providers.

These capabilities may be introduced as optional features in future releases.

---

## Architecture

The role is organized into independent task files, each responsible for a single aspect of user lifecycle management.

```text
Users Role
│
├── validation.yml
├── groups.yml
├── users.yml
├── authorized_keys.yml
└── sudo.yml
```

Each task file can be enabled or disabled through feature flags, allowing selective management without modifying the role itself.

---

## Feature Flags

| Variable | Purpose |
|----------|---------|
| `users_manage_accounts` | Create and maintain Linux user accounts |
| `users_manage_authorized_keys` | Deploy managed SSH public keys |
| `users_manage_sudo` | Manage sudo policies |

Feature flags make the role suitable for different environments without requiring multiple versions of the same role.

---

## Directory Layout

```text
roles/users/
├── defaults/
├── files/
│   └── ssh_keys/
├── tasks/
└── handlers/
```

SSH public keys are stored under:

```text
roles/users/files/ssh_keys/
```

Each managed user may have a corresponding public key file named:

```text
<username>.pub
```

---

## Variables

Primary variables include:

| Variable | Description |
|----------|-------------|
| `users_accounts` | List of managed user accounts |
| `users_default_shell` | Default login shell |
| `users_default_groups` | Default supplementary groups |
| `users_default_state` | Default user state |
| `users_ssh_keys_directory` | Directory containing managed public keys |
| `users_sudo_passwordless` | Default passwordless sudo behavior |

---

## User Definition

Example configuration:

```yaml
users_accounts:
  - username: alice
    comment: Infrastructure Engineer
    groups:
      - sudo
    sudo: true
```

The inventory defines the desired state while supporting artifacts, such as SSH public keys, remain within the role.

---

## SSH Public Key Management

The role separates user definitions from SSH public keys.

Rather than embedding long public keys inside inventory files, keys are stored as version-controlled files within the role.

Example:

```text
roles/users/files/ssh_keys/alice.pub
```

Advantages include:

- Cleaner inventories.
- Easier key rotation.
- Better Git history.
- Simpler code reviews.
- Improved maintainability.

Each `.pub` file may contain one or more public keys.

If no matching key file exists for a managed user, SSH key deployment is skipped without generating an error.

---

## Sudo Management

Sudo privileges are managed independently from Linux group membership.

A user receives a dedicated sudo policy only when explicitly configured.

Example:

```yaml
sudo: true
```

Each managed policy is deployed as:

```text
/etc/sudoers.d/<username>
```

All files are validated using `visudo` before installation to prevent invalid sudo configurations.

---

## Idempotency

The role is designed to converge managed systems to the desired state.

Validation included:

- Initial deployment against a laboratory Ubuntu Server.
- Verification of user creation.
- Verification of supplementary group membership.
- Verification of sudo policy generation.
- Verification that SSH key deployment safely skips users without managed key files.
- Repeated execution confirming an idempotent result (`changed=0`).

These tests provide confidence that repeated executions do not introduce unintended configuration drift.

---

## Testing

The role is validated using:

- `ansible-playbook --syntax-check`
- `ansible-lint` (production profile)
- Functional deployment against a laboratory Ubuntu host.
- Repeated execution to verify idempotency.

---

## Future Enhancements

Potential future improvements include:

- Custom home directory support.
- Password management using Ansible Vault.
- Account removal workflows.
- SSH key rotation policies.
- Expiration dates for accounts.
- Group policy management.
- Molecule integration for automated testing.

---

## Design Philosophy

The role follows several engineering principles:

- Infrastructure as Code.
- Idempotent configuration management.
- Minimal external dependencies.
- Feature-flag driven behavior.
- Separation of configuration from managed artifacts.
- Clear, maintainable task organization.

The objective is to provide a reusable role suitable for professional Linux administration and infrastructure automation.

## Design Decisions

Several architectural decisions were made intentionally during development:

- Use built-in Ansible modules whenever practical to minimize external dependencies.
- Store SSH public keys as managed repository artifacts rather than inline inventory variables.
- Separate user definitions from SSH key material.
- Manage sudo policy independently from Linux group membership.
- Validate sudo configuration using `visudo` before deployment.
- Organize functionality into independent task files controlled by feature flags.

These decisions improve maintainability, portability, and long-term scalability while keeping the role straightforward to understand.
