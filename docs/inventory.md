# Inventory Design

## Overview

The inventory defines the infrastructure managed by Ansible and provides the logical organization of hosts, groups, and variables.

Rather than grouping systems solely by operating system, this project organizes infrastructure according to functional responsibility. This approach improves scalability, readability, and flexibility as the environment grows.

The repository currently contains multiple inventories to represent different deployment environments.

```
inventories/
├── homelab/
├── lab/
└── production/
```

Each inventory is independent and can contain its own hosts, variables, and environment-specific configuration.

---

# Inventory Philosophy

The inventory is designed around the following principles:

- Logical grouping over physical location
- Environment isolation
- Minimal configuration duplication
- Clear variable inheritance
- Scalable host organization

This structure closely resembles inventory layouts commonly used in enterprise environments.

---

# Current Inventory Layout

The primary working inventory is currently:

```
inventories/homelab/
```

Within that inventory, hosts are organized into functional groups.

Example:

```
all
├── linux
│   ├── infrastructure_services
│   ├── monitoring_and_security
│   └── network_infrastructure
```

Individual hosts may belong to one or more groups depending on their responsibilities.

This allows playbooks to target:

- All Linux systems
- Infrastructure servers
- Monitoring systems
- Network-related systems

without duplicating inventory definitions.

---

# Host Groups

## linux

The parent group containing every managed Linux host.

This allows baseline configuration tasks, such as package management or security updates, to be applied consistently across all managed systems.

Examples include:

- Ubuntu servers
- Raspberry Pi systems
- Future Linux virtual machines

---

## infrastructure_services

Systems providing core infrastructure services.

Examples include:

- Pi-hole
- Home Assistant
- Future DNS services
- Future virtualization services

---

## monitoring_and_security

Systems responsible for monitoring, logging, or security operations.

Examples include:

- Wazuh
- Security Onion
- Prometheus
- Grafana
- Future SIEM platforms

---

## network_infrastructure

Hosts dedicated to networking functions.

Examples may include:

- Routing platforms
- VPN gateways
- Network appliances
- Future automation nodes

---

# Variable Organization

Variables are intentionally stored inside each inventory.

Example:

```
inventories/
└── homelab/
    ├── group_vars/
    ├── host_vars/
    └── hosts.yml
```

This keeps each environment self-contained and prevents accidental sharing of environment-specific configuration.

---

# Group Variables

Group variables define configuration shared by multiple systems.

Examples include:

- Package lists
- Common SSH settings
- Default timezones
- DNS configuration
- Organization-wide defaults

Using group variables minimizes duplication and improves consistency.

---

# Host Variables

Host variables define configuration unique to an individual system.

Examples include:

- Hostname
- Static IP information
- Feature flags
- Host-specific package selections

Only values that differ from the group defaults should be stored within host variables.

---

# Variable Precedence

The repository follows Ansible's standard variable precedence model.

General configuration is defined in:

```
group_vars/
```

Specific overrides are defined in:

```
host_vars/
```

This allows most hosts to inherit shared configuration while still supporting individual customization when necessary.

---

# Inventory Scalability

The inventory is designed to support future infrastructure growth without requiring structural redesign.

Additional systems can be introduced by:

- Adding new hosts
- Creating new functional groups
- Extending existing group variables
- Creating new inventories for additional environments

This approach scales from a small laboratory environment to significantly larger deployments.

---

# Deployment Strategy

Playbooks target logical groups rather than individual hosts whenever practical.

For example:

```
hosts: linux
```

instead of listing every server individually.

Benefits include:

- Simpler playbooks
- Reduced maintenance
- Automatic inclusion of new hosts
- Consistent deployments

---

# Future Expansion

As additional infrastructure is introduced, the inventory may expand to include groups such as:

- Containers
- Kubernetes
- Storage
- Backup
- Development
- Staging
- Production

The current structure is intentionally designed so these additions require minimal architectural changes.

---

# Design Summary

The inventory architecture is built around several guiding principles:

- Environment isolation
- Logical host grouping
- Reusable configuration
- Minimal duplication
- Scalable organization
- Clear variable inheritance

This design provides a maintainable foundation for Infrastructure as Code while remaining flexible enough to support future enterprise-scale automation.
