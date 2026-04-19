# easy-rsa

**Snippets**

```
cp -r /etc/easy-rsa /path/to/my/ca
cd /path/to/my/ca
vi vars # Defaultwerte definieren - ACHTUNG - s.u.
easyrsa init-pki
easyrsa build-ca
easyrsa gen-req host1.urban.intra nopass  # "nopass" ist optional
easyrsa sign-req client host1.urban.intra # "client" oder "server"!
# oder
easyrsa --subject-alt-name="DNS:host1.urban.intra,DNS:anotherhost1.urban.intra" sign-req server host1.urban.intra # nur bei arch: Ist nur ein einzelner Name vorhanden, scheint dieser automatisch übernommen zu werden
```

`vars` wird unter Arch nur gefunden, wenn es nach `pki` verschoben wird (dann wird es aber auch bei einem `init-pki` gelöscht. Das ist entweder so ok, oder man gibt den Pfad bei jedem Aufruf von `easyrsa` explizit mit `--vars=/pfad/zu/vars` an! (tritt offenbar nur im arch-Paket so auf)

Im Idealfall sind die Keys der Clients nicht auf der CA. easy-rsa kann auf Client und CA installiert werden; am Client generierte Requests müssen dann auf den Server transferiert werden, und mit `easyrsa import-req /pfad/zu/req` importiert werden

Keine Installation nötig - kann auch einfach von [github](https://github.com/OpenVPN/easy-rsa) heruntergeladen werden, und in einem beliebigen Verzeichnis ausgeführt werden
