# Grommunio-Migration auf neuen Server 202602

von slcocp0001 172.16.160.12 (1 socket, 12 cores, 48GB RAM, 4TB HD, VID 201)
auf slcocp10   172.16.160.19

- Temp. Inst. PW: GromInst2026!
- DB: grommunio / iOUsfTlq52K9wncH
- Web-Admin: GromInst2026!

<https://slcocp10.d1.r1.cbx.c:8443>
<https://pim2.cibex.net>

Sync:
`slcocp0001:/var/lib/gromox # rsync -av . pim2.cibex.net:/var/lib/gromox/migration`

## TODO

- bareos
- qemu-agent
- zabbix
- LVs
- sac
