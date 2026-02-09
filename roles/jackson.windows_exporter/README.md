# Ansible Role: windows_exporter

Installs and configures [Prometheus windows_exporter](https://github.com/prometheus-community/windows_exporter) as a Windows service.

## Requirements

- Ansible >= 2.15
- `ansible.windows` collection
- Windows target hosts

## Role Variables

### Installation

| Variable | Default | Description |
|----------|---------|-------------|
| `windows_exporter_version` | `""` | **Required.** Version to install (e.g. `0.25.1`) |
| `windows_exporter_arch` | `amd64` | Architecture for MSI download |

### Network

| Variable | Default | Description |
|----------|---------|-------------|
| `windows_exporter_listen_addr` | `""` | Bind address (empty = all interfaces) |
| `windows_exporter_listen_port` | `9182` | Metrics listen port |
| `windows_exporter_metrics_path` | `/metrics` | Metrics endpoint path |

### Collectors

| Variable | Default | Description |
|----------|---------|-------------|
| `windows_exporter_collectors_enabled` | `["[defaults]"]` | Enabled collectors list |
| `windows_exporter_textfile_dirs` | `[]` | Directories for textfile collector |

### Configuration

| Variable | Default | Description |
|----------|---------|-------------|
| `windows_exporter_config_enabled` | `false` | Deploy a YAML config file for advanced settings |
| `windows_exporter_config` | `{}` | Dict rendered to YAML config |

### Service

| Variable | Default | Description |
|----------|---------|-------------|
| `windows_exporter_service_enabled` | `true` | Enable service at boot |
| `windows_exporter_service_state` | `started` | Desired service state |

### Firewall

| Variable | Default | Description |
|----------|---------|-------------|
| `windows_exporter_firewall_enabled` | `true` | Add Windows Firewall exception via MSI |
| `windows_exporter_firewall_remote_addr` | `""` | Restrict firewall rule to these addresses |

### Advanced

| Variable | Default | Description |
|----------|---------|-------------|
| `windows_exporter_log_output` | `eventlog` | Log destination (eventlog/stderr/stdout) |
| `windows_exporter_extra_flags` | `""` | Additional CLI flags |

## Example Playbook

```yaml
- hosts: windows_servers
  roles:
    - role: jackson.windows_exporter
      vars:
        windows_exporter_version: "0.25.1"
        windows_exporter_listen_port: 9182
        windows_exporter_collectors_enabled:
          - "[defaults]"
          - process
          - iis
        windows_exporter_config_enabled: true
        windows_exporter_config:
          collectors:
            enabled: "cpu,memory,net,logical_disk,physical_disk,os,service,system,process,iis"
          collector:
            service:
              include: "windows_exporter|w3svc"
            process:
              include: "w3wp.exe|sqlservr.exe"
```

## License

MIT
