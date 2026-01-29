# Ansible Role: Vector

Installs and configures [Vector](https://vector.dev/) logging agent on Rocky Linux 9 and 10.

## Requirements

- Ansible >= 2.15
- Rocky Linux 9 or 10

## Role Variables

### Repository Management

| Variable | Default | Description |
|----------|---------|-------------|
| `vector_manage_repo` | `true` | Whether to manage the Vector yum repository |
| `vector_version` | `""` | Specific version to install (empty = latest) |

### Sources

| Variable | Default | Description |
|----------|---------|-------------|
| `vector_sources_journald_enabled` | `true` | Collect logs from journald |
| `vector_sources_journald_units` | `[]` | Limit to specific systemd units |
| `vector_sources_docker_enabled` | `false` | Collect Docker container logs |
| `vector_sources_files` | `[]` | List of file sources (see example) |

### Sinks

| Variable | Default | Description |
|----------|---------|-------------|
| `vector_sinks` | `{}` | Dictionary of sink configurations |

### Transforms

| Variable | Default | Description |
|----------|---------|-------------|
| `vector_transforms` | `{}` | Dictionary of transform configurations |

### Service

| Variable | Default | Description |
|----------|---------|-------------|
| `vector_service_enabled` | `true` | Enable service at boot |
| `vector_service_state` | `started` | Desired service state |

## Example Playbook

```yaml
- hosts: servers
  become: true
  roles:
    - role: vector
      vars:
        vector_sources_journald_enabled: true
        vector_sources_docker_enabled: true
        vector_sources_files:
          - name: app_logs
            include:
              - /var/log/myapp/*.log
            exclude:
              - /var/log/myapp/debug.log

        vector_transforms:
          parse_json:
            type: remap
            inputs:
              - app_logs
            source: |
              . = parse_json!(.message)

        vector_sinks:
          elasticsearch:
            type: elasticsearch
            inputs:
              - journald
              - docker
              - parse_json
            endpoints:
              - "https://elasticsearch:9200"
            bulk:
              index: "logs-%Y-%m-%d"
```

## Testing

This role includes Molecule tests for Rocky Linux 9 and 10.

```bash
# Install dependencies
pip install molecule molecule-plugins[docker]

# Run tests
cd roles/vector
molecule test
```

## License

MIT
