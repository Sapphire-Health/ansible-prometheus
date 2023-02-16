### Deploy windows_exporter on windows servers
```
kinit username@DOMAIN.TLD
ansible-playbook -i hosts.yml -e @vars.yml deploy-windows_exporter.yml
```