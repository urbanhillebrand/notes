# iptables

## conntrack

Problem z.b. bei Änderung einer externen (Voip-)IP -> durch bestehende Einträge in der conntrack-table und ständige Verbinungsversuche wird weiterhin ausgehend auf die alte / falsche Source-IP geNATtet.

Lösung:

`conntrack -D --src-nat --reply-dst old.ext.ip.adr`

(Quelle: [serverfault](https://serverfault.com/questions/367626/delete-specific-conntrack-entries))

Problem kann auch bei cibex-Kunden mit DHCP-Relay über VPN auftreten.

## Neue Connections/sec zählen

    conntrack -E -e NEW | pv -l -i 1 -r >/dev/null
