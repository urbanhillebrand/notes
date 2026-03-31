# imap

* **IMAPS**\
`openssl s_client -crlf -connect www.example.net:993`
* **TLS**\
`openssl s_client -connect www.example.net:143 -starttls imap`
* **Beispielsession**
```
1 login uh "mein passwort"
2 list "" "*"
. examine "Gesendete Elemente"
3 select INBOX
. fetch 1 fast
. fetch 1 all
. fetch 1 full
. fetch 1:* envelope
. fetch 1:* uid
4 uid fetch "1:*" uid
5 uid fetch 12345 envelope
6 uid fetch 12345 bodystructure
7 uid fetch 12345 flags
8 uid fetch 12345 internaldate
. uid fetch 12345 rfc822
. uid fetch 12345 rfc822.header
. uid fetch 12345 rfc822.size
. uid fetch 12345 rfc822.text
z logout
```

* **Exchange Authuser-Login**\
`. login MSH/migrate/fax@wt-partner.at <passwort>`
* **Mail löschen**
```
. store 1233 +FLAGS (\Deleted)
. expunge
```

* **Ordner löschen**\
`. delete <foldername>`
* **Ordner ohne kaskadierte Unterordner anzeigen**\
`. list "" %`
* **Ordner mit kaskadierten Unterordnern anzeigen**\
`. list "" *`
* **Unterordner eines Ordners anzeigen**\
`. list "unterordner" "*"`
* **Beispiele für UID-Searches**
```
. uid search before "01-Jan-2013"
. uid search after "01-Jan-2013"
. uid search keyword seen
. uid search from "u.hillebrand@cibex.net"
. uid search all
```

* **Beispiele für uid fetch**
```
. uid fetch 1234 (BODY[HEADER.FIELDS (from to subject date)])
. uid fetch 1234 (bodystructure)
```

* **Ordner**
```
. create inbox.sub1
. delete inbox.sub1
. rename inbox.sub1
```

* **Flags**
```
. store 1:* +flags seen
. store 1:* -flags seen
. store 22:45 +flags deleted
. store 77 -flags deleted
```

* **Verfügbare Flags**
  * seen
  * answered
  * flagged
  * deleted
  * draft
  * recent
* **Subscriptions**
```
. LSUB "" "*"
. SUBSCRIBE path/to/folder
. UNSUBSCRIBE path/to/folder
```

* **Sonstiges**
```
. examine Calendar
. status inbox (messages)
. copy 1:2 inbox.sub1
. capability
. getquotaroot inbox
. getacl inbox
```
