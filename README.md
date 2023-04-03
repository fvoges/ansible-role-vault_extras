# Vault Extras Ansible Role

This is a simple Ansible Role that complements [jacobmammoliti/ansible-role-vault](https://github.com/jacobmammoliti/ansible-role-vault), and adds a couple of things:

- Creates a bash profile in `etc/profile.d` to declare `VAULT_ADDR` pointing to the local host.
- Manages `/var/log/vault`
- Configures `logrotate` to rotate Vault's audit log

## Dependencies

This Role uses [geerlingguy.fluentd](https://galaxy.ansible.com/geerlingguy/fluentd)

## Role variables

Some of the variables use the same name as [jacobmammoliti/ansible-role-vault](https://github.com/jacobmammoliti/ansible-role-vault) for simplicity.

### `vault_enable_bash_profile`

- Create `/etc/profile.d/vault_cluster.sh`
- default: true

### `disable_tls`

- Use `http` or `https` for the `VAULT_ADDR` URL in the bash profile
- Default value: true

### `vault_user`

- OS user name
- Default value: vault

### `vault_group`

- OS group name
- Default value: vault

### `vault_log_dir`

- Directory for the Vault audit logs
- Default value: /var/log/

### `vault_log_file_name`

- Vault audit log file name
- Default value: audit

### `vault_log_file_ext`

- Vault audit log file extension
- Default value: log

### `vault_logrotate_config_filepath`

- Vault audit `logrotate` configuration
- Default value: /etc/logrotate.d/vault.conf

### `vault_logrotate_retention`

- Vault audit log retention (in days)
- Default value: 30

### `vault_system_vault_ca_cert`

- Install Vault CA cert to the OS trust store
- Default value: false

### `vault_enable_proxy`

- If true, it will create a `systemd` unit file override exposing proxy settings as environment variables for the Vault service
- Default value: false

### `vault_proxy_http`

- URL to use for the `http_proxy` environment variable
- Default value: undefined

### `vault_proxy_https`

- URL to use for the `https_proxy` environment variable. If undefined/empty, it defaults to the value of `vault_proxy_https`
- Default value: undefined

### `vault_proxy_no_proxy`

- Value to use for the `no_proxy` environment variable
- Default value: undefined

## Additional information

### Overriding `systemd` to expose environment variables

The official way to do this is manually, is to run `systemctl edit myservice`, which will create an override file for you or let you edit an existing one.

In normal installations this will create a directory `/etc/systemd/system/vault.service.d`, and inside that directory create a file whose name ends in `.conf` (typically, `override.conf`), and in this file you can add to or override any part of the unit shipped by the distribution.

For instance, in a file `/etc/systemd/system/vault.service.d/environment.conf`:

```ini
[Service]
Environment=http_proxy="https://proxy.example.com"
Environment=https_proxy="https://proxy.example.com"
Environment=no_proxy=".example.com
```

### Environment variables for proxy settings

As discussed in [this article](https://about.gitlab.com/blog/2021/01/27/we-need-to-talk-no-proxy/), the environment variables for configuring proxies in unix are more a convention than a standard.

There's a lot of "copy & paste" information on the internet, where people say things without quoting an actual source.

This Ansible Role uses the lower case versions of the environment variables, as these seem to be the "proper" ones.

Below, you can see a summary of how these variables work in common situations (shamelessly copied from the article above).

#### `http_proxy`, `https_proxy`, `HTTP_PROXY`, and  `HTTPS_PROXY`

| curl            | wget      | Ruby           | Python        | Go                                 |
|-----------------|-----------|----------------|---------------|------------------------------------|
| http_proxy      | Yes       | Yes            | Yes           | Yes                                |
| HTTP_PROXY      | No        | No             | Yes (warning) | Yes (if REQUEST_METHOD not in env) |
| https_proxy     | Yes       | Yes            | Yes           | Yes                                |
| HTTPS_PROXY     | Yes       | No             | Yes           | Yes                                |
| Case precedence | lowercase | lowercase only | lowercase     | lowercase                          |

#### `no_proxy`, and  `NO_PROXY`

Comma or space-separated list of hosts/domains to connect directly.

|                        | curl      | wget           | Ruby      | Python    | Go        |
|------------------------|-----------|----------------|-----------|-----------|-----------|
| no_proxy               | Yes       | Yes            | Yes       | Yes       | Yes       |
| NO_PROXY               | Yes       | No             | Yes       | Yes       | Yes       |
| Case precedence        | lowercase | lowercase only | lowercase | lowercase | Uppercase |
| Matches suffixes?      | Yes       | Yes            | Yes       | Yes       | Yes       |
| Strips leading `.`?    | Yes       | No             | Yes       | Yes       | No        |
| `*` matches all hosts? | Yes       | No             | No        | Yes       | Yes       |
| Supports regexes?      | No        | No             | No        | No        | No        |
| Supports CIDR blocks?  | No        | No             | Yes       | No        | Yes       |
| Detects loopback IPs?  | No        | No             | No        | No        | Yes       |
