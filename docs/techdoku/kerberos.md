# kerberos

* **Keytab erstellen**
```
> ktutil
addent -password -p uh@CI.LOCAL -k 1 -e rc4-hmac
wkt .uh.keytab
quit
```

* **Login mit Keytab**
```
kinit -k -t ~/.uh.keytab uh@CI.LOCAL
```
