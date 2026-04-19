# swaks (swiss army knife for SMTP)

## Snippets

```bash
swaks --server localhost --from uh@cibex.net --to postmaster@eca.at --cc a@b --bcc b@c

# mit Auth., TLS über den submission-Port:
swaks --server localhost --tls --from alfa.test@eca.at --to uh@cibex.net --auth-user alfa.test@eca.at --auth-password 'test!dummy' -p 587

# mit --quit-after abbrechen (z.B. --quit-after auth|rcpt|starttls|data...)
swaks --server localhost --from a@b --to b@c --quit-after rcpt

# body via stdin (mit --data stattdessen kompletten DATA-Teil)
swaks --server localhost --from a@b --to b@c --body -

# body aus Datei
... --body @filename.txt

# mit Datei-Anhang
... --body "body text" --attach-type "application/pdf" --attach /path/to/file.pdf
```

## Interessante Optionen

- `--xlient-(addr|name|port|proto|destaddr|destport|helo|login|reverse-name)`
- Header mit `--header` setzen (z.B. `--header "Subject: Testmail`")
