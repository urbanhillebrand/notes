# synology

## User für Dienste im GUI nicht sichtbar

* /etc/passwd editieren - alte UID notieren (z.B. 101), neue UID >1024 vergeben
* find /volume1/ -user &lt;alteUID> -print
* find /volume1/ -user &lt;alteUID> -print -exec chown &lt;username> {} \;

## Weitere IP hinz.

Pro IP in `/etc/sysconfig/network-scripts` ein File erstellen.

**Beispiel f. VLAN-IP**

```
DEVICE=bond0.128
VLAN_ROW_DEVICE=bond0
VLAN_ID=128
ONBOOT=yes
BOOTPROTO=static
IPADDR=10.10.128.12
NETMASK=255.255.255.0
```

Danach `/etc/rc.network restart`
