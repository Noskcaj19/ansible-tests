# Ansible Role: Base Server

Base server configuration for Rocky Linux 9 and 10, including HTTP proxy environment variables, CA certificate installation, and CIFS mount setup.

## Requirements

- Ansible >= 2.15
- Rocky Linux 9 or 10
- `ansible.posix` collection (for `ansible.posix.mount`)

## Role Variables

### Proxy

| Variable | Default | Description |
|----------|---------|-------------|
| `base_server_http_proxy` | `""` | HTTP proxy URL |
| `base_server_https_proxy` | `""` | HTTPS proxy URL |
| `base_server_no_proxy` | `""` | Comma-separated list of proxy exclusions |

### CA Certificate

| Variable | Default | Description |
|----------|---------|-------------|
| `base_server_ca_cert_url` | `""` | URL to download the CA certificate from |
| `base_server_ca_cert_filename` | `"custom-ca.crt"` | Filename for the installed certificate |

### CIFS Mount

| Variable | Default | Description |
|----------|---------|-------------|
| `base_server_cifs_share` | `""` | CIFS share path (e.g., `//server/share`) |
| `base_server_cifs_mount_point` | `"/mnt/share"` | Local mount point |
| `base_server_cifs_username` | `""` | CIFS username |
| `base_server_cifs_password` | `""` | CIFS password |
| `base_server_cifs_domain` | `""` | CIFS domain |
| `base_server_cifs_options` | `"vers=3.0,uid=0,gid=0,file_mode=0644,dir_mode=0755"` | Mount options |

## Example Playbook

```yaml
- hosts: servers
  become: true
  roles:
    - role: jackson.base_server
      vars:
        base_server_http_proxy: "http://proxy.example.com:8080"
        base_server_https_proxy: "http://proxy.example.com:8080"
        base_server_no_proxy: "localhost,127.0.0.1,.example.com"

        base_server_ca_cert_url: "https://pki.example.com/root-ca.crt"
        base_server_ca_cert_filename: "example-root-ca.crt"

        base_server_cifs_share: "//fileserver.example.com/data"
        base_server_cifs_mount_point: "/mnt/data"
        base_server_cifs_username: "svc_account"
        base_server_cifs_password: "secret"
        base_server_cifs_domain: "EXAMPLE"
```

## License

MIT
