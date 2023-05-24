## Deploy windows_exporter on windows servers

### Set environment variables
```
export ROLE_ID=00000000-0000-0000-0000-000000000000
export SECRET_ID=00000000-0000-0000-0000-000000000000
export VAULT_ADDR=https://vault.fqdn.tld:8200
```
### Create an inventory

```
cat << EOF > hosts.yml
all:
    hosts:
        ansibletest01.fqdn.tld:
vars:
    ansible_user: "username@FQDN.TLD"
    ansible_connection: winrm
    ansible_winrm_transport: kerberos
    ansible_winrm_server_cert_validation: ignore
EOF
```

### Create a variable file

```
cat << EOF > vars.yml
match_host: ansibletest01.fqdn.tld
package_url: http://webserverwithmsi/
install_dir: "C:/Program Files/windows_exporter"
package_name: windows_exporter-0.20.0-amd64.msi
product_id: EDD0CDE3-4519-4C1A-9FB4-C8C067615698
prometheus_config: printer-config.yml.j2
uninstall: "no"
cert_ttl: 8760h
install_params: "EXTRA_FLAGS=\"--config.file \"\"C:\\Program Files\\windows_exporter\\config.yml\"\" --web.config.file \"\"C:\\Program Files\\windows_exporter\\web-config.yml\"\"\""
EOF
```

### Get a kerberos ticket (Windows)
```
kinit username@DOMAIN.TLD
```

### Install prometheus.prometheus collection (Linux)
```
ansible-galaxy collection install prometheus.prometheus
```

### Set up hashicorp vault app role authentication env vars
```
export ROLE_ID=00000000-0000-0000-0000-000000000000
export SECRET_ID=00000000-0000-0000-0000-000000000000
```

### Install Prometheus on Windows
```
ansible-playbook -i hosts.yml -e @vars.yml deploy-windows_exporter.yml
```