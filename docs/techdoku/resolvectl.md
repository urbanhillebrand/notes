# resolvectl

## stub-Resolver aktivieren

-> frägt 127.0.0.53; damit zieht dann auch uneingeschränkt `/etc/resolved.conf`

Details: https://wiki.ubuntuusers.de/systemd/systemd-resolved/#Einbindung-in-das-System

**📌 NOTE**\
Sollte eigentlich Default sein!

**Aktivierung**

```bash
sudo rm /etc/resolv.conf && sudo ln -s /run/systemd/resolve/stub-resolv.conf /etc/resolv.conf
```

## Snippets

```
resolvectl # führt resolvectl status aus
sudo resolvectl domain wlo1 "~." # Veränderung on-the-fly
sudo resolvectl domain wlo1 ""   # Eintrag löschen
```

## DNS zusammen mit VPNs und NetworkManager

**🔥 CAUTION**\
Hat kein Adapter unter Domains die Wildcard `~.` konfiguriert, werden DNS-Searches u.U. immer an **alle** Adapter geschickt -> DNS-Leaks!

Abhilfe: Bei der "Standard-Verbindung" die Wildcard über `nmcli` dazukonfigurieren:

`sudo nmcli connection modify Kabelgebundene\ Verbindung\ 1 ipv4.dns-search "~."`

* diese Veränderung erstellt ein File in `/etc/NetworkManager/system-connections`
* `nmcli` kann überall Tab-Completion
* kürzerer Name vmtl. trotzdem keine schlechte Idee
