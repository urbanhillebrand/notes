# systemd

## systemctl

**Grundlegenes**

    sudo systemctl start odoo
    sudo systemctl stop odoo
    sudo systemctl restart odoo
    sudo systemctl reload odoo
    sudo systemctl reload-or-restart odoo

**Units anzeigen/editieren**

    sudo systemctl
    sudo systemctl list-units --type=service
    sudo systemctl cat odoo
    sudo systemctl show odoo
    sudo systemctl edit odoo # zusätzliche Settings in eigenem File
    sudo systemctl edit --full odoo # orginales Startscript editieren
    sudo systemctl daemon-reload # nach Änderungen an Unit-Files

**Status**

    sudo systemctl status odoo
    sudo systemctl is-enabled odoo
    sudo systemctl is-active odoo
    sudo systemctl is-failed odoo
    sudo systemctl list-dependencies odoo

**enable/disable und mask**

    sudo systemctl enable odoo
    sudo systemctl disable odoo
    sudo systemctl mask odoo # maskierte lassen sich nicht starten
    sudo systemctl unmask odoo

**user units**

    systemctl --user list-units --type=service
    journalctl --user
    journalctl --user-unit wireplumber.service

## Beispiel-Service

```
[Unit]
Description=Simple WSGI Server

[Service]
Type=simple
ExecStart=/home/BENUTZER/code/simple_wsgi_server.py
#User=www-data # sonst root
#After=network-online.target

[Install]
WantedBy=multi-user.target
```

## timedatectl

**Snippets**

```
timedatectl status
timedatectl timesync-status

timedatectl list-timezones
sudo timedatectl set-timezone Europe/Vienna
timedatectl status

# nach Änderungen von NTP-Server (/etc/systemd/timesyncd.conf):
sudo timedatectl set-ntp 0
sudo timedatectl set-ntp 1
```

## journalctl

**Grundlegendes**

    journalctl # alle Meldungen!
    journalctl --no-pager # kein less als Pager verw.
    journalctl -f
    journalctl -n 10 # die letzten 10 Zeilen
    journalctl --utc # in UTC anzeigen

**Bootvorgänge**

    journalctl -b # seit letztem Boot
    journalctl --list-boots
    journalctl -b -1 # Daten von vorherigem Bootvorgang (s. --list-boots)

**Zeit**

    journalctl -S 1d # 1 Tag zurück
    journalctl -S 1h # 1 Stunde
    journalctl -S 5m # 5 Minuten zurück
    journalctl --since "2022-03-01"
    journalctl --since "6:00"
    journalctl --since "2022-03-01 4:00" --until "2022-03-01 5:00"
    journalctl --since yesterday --until "1 hour ago"

**nach Units einschränken**

    journalctl -u ssh.service
    journalctl -u nginx -u php-fpm # mehrere möglich

**nach Pfad**

    journalctl /usr/sbin/apache2

**spezielle Felder - s. "man systemd.journal-fields"**

    journalctl _PID=1234 # nach PID
    journalctl _UID=33 # nach UID
    journalctl -F _UID # alle UIDs anzeigen, die gespeichert wurden
    journalctl -F _GID # gleiches f. GIDs

**Kernel-Meldungen (dmesg)**

    journalctl -k # f. aktuellen Bootvorgang
    journalctl -k -b -5 # f. alternativen Bootvorgang (s. --list-boots)

**Prioritäten**

    journalctl -p err -b

**Outputs (weitere s. manpage)**

    journalctl -o short
    journalctl -o short-full
    journalctl -o verbose
    journalctl -o json-pretty

    .Prioritätenliste
    - 0 emerg
    - 1 alert
    - 2 crit
    - 3 err
    - 4 warning
    - 5 notice
    - 6 info
    - 7 debug

**Ausgabeoptionen**

    journalctl # mit eingebautem Pager, rechts abgeschnitten, scrollbar mit Pfeiltasten
    journalctl --no-full # rechts hart abschneiden
    journalctl --no-pager
    journalctl -b -u apache2 -o json # JSON-Output
    journalctl -b -u apache2 -o json-pretty # lesbarer JSON-Output
    journalctl -n # letzte 10 Einträge (wie tail)
    journalctl -n 50 # letzte 50 Einträge
    journalctl -f # wie tail -f
