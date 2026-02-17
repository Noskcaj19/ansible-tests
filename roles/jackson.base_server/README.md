# Ansible Role: Base Server

Base server configuration for Rocky Linux 9 and 10, including HTTP proxy environment variables, Docker daemon proxy, CA certificate installation, VAS (Quest Authentication Services), CIFS mount setup, and Kerberos configuration.

## Requirements

- Ansible >= 2.15
- Rocky Linux 9 or 10
- `ansible.posix` collection (for `ansible.posix.mount`)

## Role Variables

All features are opt-in — tasks are skipped when their primary variable is left empty.

### Proxy

| Variable | Default | Description |
|----------|---------|-------------|
| `base_server_http_proxy` | `""` | HTTP proxy URL |
| `base_server_https_proxy` | `"{{ base_server_http_proxy }}"` | HTTPS proxy URL (defaults to `base_server_http_proxy`) |
| `base_server_no_proxy` | `""` | Comma-separated list of proxy exclusions |

### Docker Daemon Proxy

Configures a systemd drop-in for the Docker service at `/etc/systemd/system/docker.service.d/http-proxy.conf`. Applied when `base_server_http_proxy` or `base_server_https_proxy` is set. Uses the same proxy variables as above.

### CA Certificate

| Variable | Default | Description |
|----------|---------|-------------|
| `base_server_ca_cert_url` | `""` | URL to download the CA certificate from |
| `base_server_ca_cert_filename` | `"custom-ca.crt"` | Filename for the installed certificate |

When configured, the certificate is installed to `/etc/pki/ca-trust/source/anchors/` and the following environment variables are set in `/etc/environment`: `AWS_CA_BUNDLE`, `PIP_CERT`, `REQUESTS_CA_BUNDLE`, `CURL_CA_BUNDLE`, `GIT_SSL_CAINFO`, `SSL_CERT_FILE`.

### VAS (Quest Authentication Services)

| Variable | Default | Description |
|----------|---------|-------------|
| `base_server_vas_realm` | `""` | Kerberos/AD realm for VAS (e.g., `EXAMPLE.COM`) |
| `base_server_vas_users_allow` | `""` | Contents of the VAS `users.allow` file |

When configured, this installs SELinux policy dependencies, configures the vasd SELinux policy, and deploys `/etc/opt/quest/vas/vas.conf` and `/etc/opt/quest/vas/users.allow`.

### Kerberos

| Variable | Default | Description |
|----------|---------|-------------|
| `base_server_krb_realm` | `"{{ base_server_vas_realm }}"` | Kerberos realm (defaults to `base_server_vas_realm`) |

When configured, deploys `/etc/krb.conf` with DNS-based KDC lookup.

### CIFS Mount

| Variable | Default | Description |
|----------|---------|-------------|
| `base_server_cifs_share` | `""` | CIFS share path (e.g., `//server/share`) |
| `base_server_cifs_mount_point` | `"/mnt/share"` | Local mount point |
| `base_server_cifs_username` | `""` | CIFS username |
| `base_server_cifs_password` | `""` | CIFS password |
| `base_server_cifs_domain` | `""` | CIFS domain |
| `base_server_cifs_options` | `"vers=3.0,uid=0,gid=0,file_mode=0644,dir_mode=0755"` | Mount options |

## Tags

| Tag | Description |
|-----|-------------|
| `base_server` | All tasks |
| `base_server_proxy` | Proxy environment variables |
| `base_server_ca_cert` | CA certificate installation |
| `base_server_docker_proxy` | Docker daemon proxy |
| `base_server_vas` | VAS configuration |
| `base_server_cifs` | CIFS mount |
| `base_server_krb` | Kerberos configuration |

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

        base_server_vas_realm: "EXAMPLE.COM"
        base_server_vas_users_allow: |
          user1@EXAMPLE.COM
          user2@EXAMPLE.COM

        base_server_cifs_share: "//fileserver.example.com/data"
        base_server_cifs_mount_point: "/mnt/data"
        base_server_cifs_username: "svc_account"
        base_server_cifs_password: "secret"
        base_server_cifs_domain: "EXAMPLE"
```

## License

MIT
