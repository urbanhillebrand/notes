# certutil

Paketname Ubuntu: `libnss3-tools`

**Snippets**

```
certutil -d sql:$HOME/.pki/nssdb -L  # list all
certutil -d sql:$HOME/.pki/nssdb -L -n <certificate nickname>  # Details eines Certs
certutil -d sql:$HOME/.pki/nssdb -A -t "C,," -n <certificate nickname> -i <certificate filename>  # einer CA vertrauen (nicht für server/user/intermediate-Certs!)
certutil -d sql:$HOME/.pki/nssdb -A -t ",," -n <certificate nickname> -i <certificate filename>   # für eine intermediate CA
certutil -d sql:$HOME/.pki/nssdb -A -t "P,," -n <certificate nickname> -i <certificate filename>  # für ein self-signed Cert
certutil -d sql:$HOME/.pki/nssdb -D -n <certificate nickname>
```

Genaue Erklärung aller Trust-Flags: [link](https://blogs.oracle.com/meena/about-trust-flags-of-certificates-in-nss-database-that-can-be-modified-by-certutil)
