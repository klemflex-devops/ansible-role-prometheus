# Ansible Role: Prometheus

This Ansible role installs and configures **Prometheus** from the official GitHub release.  
It also supports provisioning targets for **Node Exporter**.

The role performs the following tasks:

- Checks whether Prometheus is already installed
- Installs Prometheus if it is not present
- Creates a dedicated `prometheus` system user
- Creates required directories
- Downloads and installs Prometheus binaries
- Deploys Prometheus configuration
- Creates and enables a systemd service
- Optionally provisions Node Exporter targets

---

# Requirements

- Ansible >= 2.9
- Linux system with **systemd**
- Internet access to download Prometheus releases

---

# Role Variables

Variables are defined in `defaults/main.yml`.

| Variable | Default | Description |
|--------|--------|--------|
| prometheus_version | `3.9.1` | Prometheus version to install |
| provision | `node_exporter` | Enables Node Exporter provisioning |

Example override:

```yaml
prometheus_version: 3.9.1
provision: node_exporter
```

---

# Role Structure

```
ansible-role-prometheus
├── defaults
│   └── main.yml
├── files
│   └── config.yml
├── handlers
│   └── main.yml
├── meta
│   └── main.yml
├── tasks
│   ├── install.yml
│   ├── main.yml
│   └── provision_node_exporter.yml
├── templates
│   ├── node_exporter_targets.json.j2
│   ├── prometheus.service.j2
│   └── prometheus.yml.j2
├── tests
│   ├── ansible.cfg
│   ├── inventory.yml
│   └── test.yml
├── vars
│   └── main.yml
└── README.md
```

---

# What the Role Does

1. Checks whether Prometheus is installed:

```
which prometheus
```

2. If Prometheus is not installed:

- Creates the `prometheus` user
- Creates required directories:

```
/etc/prometheus
/var/lib/prometheus
```

3. Downloads Prometheus from GitHub releases.

4. Extracts the archive and installs binaries:

```
/usr/local/bin/prometheus
/usr/local/bin/promtool
```

5. Deploys the Prometheus configuration file:

```
/etc/prometheus/prometheus.yml
```

6. Installs the systemd service:

```
/etc/systemd/system/prometheus.service
```

7. Enables and starts Prometheus.

---

# Example Playbook

```yaml
- hosts: prometheus
  become: true

  roles:
    - prometheus
```

---

# Example Inventory

```yaml
all:
  hosts:
    prometheus:
      ansible_host: 192.168.1.10
```

---

# Prometheus Web Interface

After installation, Prometheus will be available at:

```
http://<server_ip>:9090
```

Check service status:

```
systemctl status prometheus
```

---

# Node Exporter Provisioning

This role supports automatic provisioning of **Node Exporter targets** using file-based service discovery.

Targets are generated from the template:

```
templates/node_exporter_targets.json.j2
```

---

# Testing

A simple test environment is available in the `tests` directory.

Run the test playbook:

```
ansible-playbook -i tests/inventory.yml tests/test.yml
```

---

# License

MIT