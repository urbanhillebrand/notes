# LDAP

## ldapsearch

### Snippets
* **Unverschlüsselt**\
`ldapsearch -H ldap://10.29.0.3 -D "cn=admin,dc=ugm,dc=ecaportal,dc=at" -w '<password>' -b "dc=ugm,dc=ecaportal,dc=at" "(&(objectClass=ecaPerson)(uid=s9999))"`
* **mit LDAP/SSL (deprecated!)**\
`ldapsearch -H ldaps://swadsp01.ci.local -D "CN=admext,OU=System,OU=Users,OU=ci,DC=ci,DC=local" -w '<password>' -b "OU=Users,OU=cibex,OU=ci,DC=ci,DC=local" "(&(objectclass=user)(memberOf=CN=TestgruppeUrban,OU=Groups,OU=cibex,OU=ci,DC=ci,DC=local))" cn`
* **mit STARTTLS**\
`ldapsearch -H ldap://swadsp01.ci.local -D "CN=admext,OU=System,OU=Users,OU=ci,DC=ci,DC=local" -w '<password>' -b "OU=Users,OU=cibex,OU=ci,DC=ci,DC=local" -ZZ "(&(objectclass=user)(memberOf=CN=TestgruppeUrban,OU=Groups,OU=cibex,OU=ci,DC=ci,DC=local))" cn`

**❗ IMPORTANT**\
Wird dem Zertifikat nicht vertraut schlägt die Verbindung mit einer nichtssagenden Fehlermeldung fehl. Mehr sieht man mit `-d1` im Debug-Modus.

* **Ohne Zertifikats-Trust**\
`LDAPTLS_REQCERT=never ldapsearch -H ldaps://mtdserver.mtd.local ...`
* **via Socket**\
``sudo ldapsearch -Y EXTERNAL -H ldapi:/// -b cn=config``

sortiert! -> `-S samaccountname`

* **mit base64-decode**\
`/usr/bin/ldapsearch -o ldif-wrap=no "$@" | perl -MMIME::Base64 -Mutf8 -pe 's/^([-a-zA-Z0-9;]+):(:\s+)(\S+)$/$1.$2.&decode_base64($3)/e'`

### binäre Filter

Über folgende OIDs können binäre AND/OR-Matches gemacht werden (z.B. f. AD userAccountControl):

* `1.2.840.113556.1.4.803` = AND
* `1.2.840.113556.1.4.804` = OR

**Beispiel mit `AND`**

```
"(&
  (objectClass=person)
  (!(UserAccountControl:1.2.840.113556.1.4.803:=2))
  (memberOf=CN=Grafana_Prod,CN=Users,DC=BSE,DC=local)
)"
```

**Beispiel mit `OR`**

```
(useraccountcontrol:1.2.840.113556.1.4.804:=514)
```

**📌 NOTE**\
Liefert alle User mit gesetzten Flags 2 (=AD User disabled) oder 512 (=AD normal user) zurück

**Filter auf leeres Feld "ecaIM"**

    "(&(objectClass=ecaPerson)(!(ecaDisabled=TRUE))(!(ecaIM=*)))"

## Base finden

`ldapsearch -H <ldapserver> -D <username> -w '<password>' -s base configContext namingContexts`

## Tests mit nmap

`nmap -p 389 --script ldap-rootdse <host> # Root-DSE herausfinden`

## Notbremse nach Aussperren

```
sudo systemctl stop slapd
sudo slapcat -F /etc/ldap/slapd.d -b cn=config -l /tmp/config.ldif
# --> config.ldif editieren (olcSecurity, olcRootPW, olcRootDN...)
sudo mv /etc/ldap/slapd.d{,.old}
sudo mkdir /etc/ldap/slapd.d
sudo slapadd -F /etc/ldap/slapd.d -b cn=config -l /tmp/config.ldif
sudo chown -R openldap:openldap /etc/ldap/slapd.d
sudo systemctl start slapd
```

Bei gravierenden Fehlern reicht obiger Prozess ggf. nicht - man kann zur Not auch `/etc/ldap/slapd.d/cn=config.ldif` direkt editieren und den Fehler beheben. Danach kommt bei jedem Start ein `checksum error`, der aber nur kosmetisch ist. Dieser Fehler lässt sich durch oben beschriebenen Ex-/Import wieder entfernen.

Will man nicht nur `cn=config`, sondern auch die Datenbank(en) sichern und wiederherstellen:

* `slapcat` auch mit `-b dc=blah,dc=blah` durchführen
* `/var/lib/ldap` umbenennen und leer neuerstellen
* `slapadd` auch mit `-b dc=blah,dc=blah` durchführen
* `chown` auch für `/var/lib/ldap` durchführen

## LDIF

**📌 NOTE**\
Gute Infos zu `ldapmodify` hier: [digitalocean](https://www.digitalocean.com/community/tutorials/how-to-use-ldif-files-to-make-changes-to-an-openldap-system)

Bei mehreren Einträgen diese entweder mit Leerzeile trennen (dann ist neue DN betroffen), oder - falls mehrere Felder einer DN geändert werden sollen, mit einem Hyphen:

```
dn: CN=John Smith,OU=Legal,DC=example,DC=com
 changetype: modify
 replace: employeeID
 employeeID: 1234
 -
 replace: employeeNumber
 employeeNumber: 98722
 -
 replace: extensionAttribute6
 extensionAttribute6: JSmith98
 -

 dn: CN=Jane Smith,OU=Accounting,DC=example,DC=com
 changetype: modify
 replace: employeeID
 employeeID: 5678
 -
 replace: employeeNumber
 employeeNumber: 76543
```
