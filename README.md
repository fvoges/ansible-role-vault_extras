# Vault Extras Ansible Role

This is a simple Ansible Role that complements [jacobmammoliti/ansible-role-vault](https://github.com/jacobmammoliti/ansible-role-vault), and adds a couple of things:

- Creates a bash profile in `etc/profile.d` to declare `VAULT_ADDR` pointing to the local host.
- Manages `/var/log/vault`
- Configures `logrotate` to rotate Vault's audit log

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
