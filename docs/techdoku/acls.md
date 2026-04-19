# ACLs

```
sudo apt install acl # userspace-Tools installieren

setfacl -m u:urban:r acl.txt # set
setfacl -m u:urban:-,g:docker:rw acl.txt # no access f. user

getfacl acl.txt # display
setfacl -x u:urban acl.txt # remove single
setfacl -b acl.txt # remove all
setfacl -Rb directory # remove all recursive
```

***🔥 CAUTION***\
ACLs werden von oben nach unten abgearbeitet, der erste Treffer zählt!

## Masken

* Eine ACL-Maske kann optional verwendet werden, um die maximalen Rechte festzulegen, die über eine ACL gesetzt werden können
* Masken können explizit mit `-m:MASK` gesetzt werden
* Masken sind die Vereinigungsmenge der Gruppenberechtigung und der benannten Benutzer
* Ein `+` am Ende der Rechte bei `ls` zeigt an, dass ACLs gesetzt sind. Anzeige von ls dann nicht mehr wie gewohnt -> Die vermeintliche Gruppenberechtigung zeigt nun die Maske an
* Ein `chmod` auf eine Datei mit aktivierter ACL verändert dann in Wirklichkeit die Maske

## Default-ACLs

Auf Verzeichnisse kann man auch eine Default-ACL erstellen, und legen die genauen Rechte für in diesem Verzeichnis erstellte Files/Directories fest - unabhängig von sgid, umask usw.!

    sudo setfacl -m u:urban:rwx,g:docker:rx directoryname # ACL setzen
    sudo setfacl -dm u:urban:rwx,g:docker:rx directoryname # Default-ACL setzen
    sudo setfacl -k acl.txt # entfernen der Default-ACL

***📌 NOTE***\
Default-ACLs als Verzeichnisse sollten das `x`-bit natürlich gesetzt haben - dieses wird bei in diesem Verzeichnis erstellten Files automatisch entfernt

## ACLs nachträglich übertragen

    getfacl DIR | setfacl -R -M- DIR # überträgt rekursiv auf alle Unterordner

    getfacl -R -n DIR > acl.txt # rekursiv speichern
    setfacl --restore=acl.txt # wiederherstellen

## Troubleshooting

***Nicht alle Shellcommands/Programme können mit ACLs umgehen***

* `tar` speichert keine ACLs mit ab (Workaround s.o. mit `--restore`)
* find zeigt keinen Hinweis auf ACLs mit `-ls`
* Dateimanager zeigen nicht alle ACLs an oder benötigen Erweiterungen
