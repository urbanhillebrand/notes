# Ansible2

## ad-hoc Snippets

```
# installiert Paket per ad-hoc"
ansible <target> -b -m apt -a "name=chrony state=present
ansible <target> -b -m service -a "name=chronyd state=started enabled=yes"

# Facts auslesen
ansible <target> -m setup

```
