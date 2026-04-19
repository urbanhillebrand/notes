# Grommunio

**Links**

* [Docs](https://docs.grommunio.com/)
* [grommunio-admin](https://docs.grommunio.com/admin/administration.html#grommunio-admin-cli-acli)

**proxy-auth**

```
. Adminuser in Organisation anlegen & auf Postfach berechtigen
. Zugriff via Webmail
. IMAP-Auth: LOGIN adminuser!mailboxname pw
```

**Snippets**

```
grommunio-admin shell # Autovervollständigung

grommunio-admin domain list # domainID ermitteln
grommunio-admin user query -f domainID=18

USERLIST=$(grommunio-admin user query -f domainID=<idVonoben> | awk '{print $2}' | grep -vE '^username')
echo $USERLIST

# z.B. User löschen:
for user in $USERLIST; do grommunio-admin user delete --yes $user; done
# oder mit --keep-files um Mailboxen zu erhalten!

grommunio-admin user modify --lang=de_DE  lohnverrechnung@haus-sanktjosef.at

grommunio-admin org show oft1208

grommunio-admin user query -f domainID=4 username maildir
grommunio-admin user query -f orgID=19

grommunio-admin user show urban.hillebrand@cibex.net

grommunio-admin ldap downsync -o oft1208 -c # -c = --complete

grommunio-admin user create --lang de_DE --domain 1 --property givenname="Weisskopf" --property surname="Josef" --property displayname="Josef Weisskopf" josef@meine-domain.de

grommunio-admin passwd # reset admin pw

# Exchange-Import
gromox-pff2mt andreas.woegerer.pst | gromox-mt2exm -u andreas.woegerer@awomed.at

# s. https://community.grommunio.com/d/1883-mailbox-stored-size-is-huge-compared-to-its-content/9   
gromox-mbop -u urban.hillebrand@cibex.net purge-softdelete -r -t "30d20h" IPM_SUBTREE
gromox-mbop -u urban.hillebrand@cibex.net purge-datafiles
```

## Mailboxen oder Teile davon exportieren

siehe https://docs.grommunio.com/man/gromox-exm2eml.8.html

Einzelne Nachricht: `gromox-exm2eml -u "g.prosser@lauscher-hoschek.at" 393381 >393381.eml`

**📌 NOTE**\
ID lässt sich im Webmail mit Rechtsklick unter "Optionen" herausfinden

## Grommunio Servermigration (ungeprüft!)

Quelle: https://community.grommunio.com/d/1531-migrieren-eines-grommunio-servers-auf-neuen-server/7

**Nacharbeiten:**

* Zertifikate kopieren
* `/etc/gromox/ldap_adaptor.cfg` manuell kopieren

```
Quell- und Zielsystem updaten und rebooten: grommunio-update update && reboot
Neues System mit denselben Parametern einrichten wie das bisherige System. Keine Anlegungen von Daten.
Dienste beenden auf beiden Systemen:
systemctl --all --output json list-units| jq '.[]|select(.unit|test("(grom.|nginx|.fpm).service")).unit' |xargs systemctl stop
Export der MariaDB-Datenbank:
mysql --execute="SHOW DATABASES" --skip-column-names --batch |grep -Ev '^(mysql|(performance|information)_schema)$' |while read -r DB; do mysqldump --single-transaction --routines --triggers --events --add-drop-database $DB > /usr/local/share/$DB.sql ; done
Für Folgepunkte ggf. /root/.ssh/known_hosts umbenennen / löschen, wenn vorher Übertragungen an gleiche IP stattgefunden haben.
Nutzerdatenübertragung Q -> Z !!! entspricht Spiegelung, Verzeichnisinhalte im Ziel werden gelöscht !!!:
rsync -aH -essh --delete --numeric-ids -P --stats --inplace /usr/local/share/ root@192.168.xxx.xxx:/usr/local/share/
CronDaily Übertragung Q -> Z !!! entspricht Spiegelung, Verzeichnisinhalte im Ziel werden gelöscht !!!:
rsync -aH -essh --delete --numeric-ids -P --stats --inplace /etc/cron.daily/ root@192.168.xxx.xxx:/etc/cron.daily/
CronHourly Übertragung Q -> Z !!! entspricht Spiegelung, Verzeichnisinhalte im Ziel werden gelöscht !!!:
rsync -aH -essh --delete --numeric-ids -P --stats --inplace /etc/cron.hourly/ root@192.168.xxx.xxx:/etc/cron.hourly/
Postfachübertragung Q -> Z !!! entspricht Spiegelung, Verzeichnisinhalte im Ziel werden gelöscht !!!:
rsync -aH -essh --delete --numeric-ids -P --stats --inplace /var/lib/gromox/ root@192.168.xxx.xxx:/var/lib/gromox/
Ordnerberechtigungen prüfen // gromox falsch konfiguriert
chown -Rf gromox:gromox /var/lib/gromox && chown -Rf grommunio:gromox /var/lib/gromox/user/* && chmod -Rf 0770 /var/lib/gromox && chmod -Rf 0775 /var/lib/gromox/domain/*
Import der MariaDB-Datenbank (nur Core):
mysql grommunio < /usr/local/share/grommunio.sql
mysql sys < /usr/local/share/sys.sql
Importuser prüfen /var/lib/gromox/user/x/y/: grommunio-admin user query username maildir auf beiden Systemen ausführen
Dienste starten auf beiden Systemen:
systemctl --all --output json list-units| jq '.[]|select(.unit|test("(grom.|nginx|.fpm).service")).unit' |xargs systemctl start && reboot
```
