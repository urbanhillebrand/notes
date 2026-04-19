**`doveadm` Snippets**
```
doveadm user '*'
doveadm user 'alfa*'
doveadm user alfa.test@urban.lab
doveadm who # zeigt eingeloggte User inkl. IP

doveadm mailbox list -u alfa.test@urban.lab # listet Ordner
doveadm mailbox list -s -u bravo.test@urban.lab # nur "subscribed" folders
doveadm mailbox list -A # alle User / Ordner!
doveadm mailbox create -u bravo.test@urban.lab INBOX.subinbox2
doveadm mailbox subscribe -u bravo.test@urban.lab INBOX.subinbox2 # oder "unsubscribe"
doveadm mailbox delete -u bravo.test@urban.lab -s INBOX.subinbox # inkl. unsubscribe (-s); beinhaltet ein "expunge" der Mailbox -> alles unwiederbringlich gelöscht!
doveadm mailbox rename -u bravo.test@urban.lab -s INBOX.subinbox2 folderneu # das -s passt die Subscriptions an

doveadm mailbox status -u bravo.test@urban.lab all INBOX # s. man doveadm-mailbox
doveadm -f table mailbox status -u bravo.test@urban.lab all '*'
doveadm -f table mailbox status -u bravo.test@urban.lab "messages vsize" '*'

# viel mehr möglich, s. man doveadm-search / doveadm-fetch:
doveadm search -u bob mailbox INBOX subject todo |
while read guid uid; do
  doveadm fetch -u bob body mailbox-guid $guid uid $uid > msg.$uid
done

doveadm expunge -u alfa.test@urban.lab mailbox Trash all # löscht sofort und unwiederbringlich!
doveadm expunge -u alfa.test@urban.lab mailbox Trash savedbefore 30d # unwiederbringlich

doveadm quota get -A
doveadm quota get -u alfa.test@urban.lab
doveadm quota recalc -u alfa.test@urban.lab

doveadm reload
doveadm auth cache flush
doveadm auth test alfa.test@urban.lab
doveadm auth test alfa.test@urban.lab password
```

Weitere (s. Manpages!)
- doveadm move, copy (auch zwischen unterschiedlichen Usern!)
