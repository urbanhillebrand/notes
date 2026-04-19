# openssl

* **Keypair erstellen**
```
ssh-keygen -t ed25519 -C "Beschreibung"
```

* **Wichtigste Infos**
```
openssl x509 -in test.pem -noout -subject -issuer -enddate -ext subjectAltName
```

* **Self-signed cert erstellen**
```
sudo openssl req -x509 -nodes -days 3650 -newkey rsa:2048 -keyout /etc/ssl/private/`hostname -f`.privkey.pem -out /etc/ssl/certs/`hostname -f`.cert.pem
```

* **Self-signed cert mit SAN(s) erstellen**
```
tee v3.ext << 'EOF'
subjectKeyIdentifier   = hash
authorityKeyIdentifier = keyid:always,issuer:always
basicConstraints       = CA:TRUE
keyUsage               = digitalSignature, nonRepudiation, keyEncipherment, dataEncipherment, keyAgreement, keyCertSign
subjectAltName         = DNS:srv-mail1.bergbkitzdomain.local, DNS:srv-mail1
issuerAltName          = issuer:copy
EOF

openssl genrsa -des3 -out srv-mail1.bergbkitzdomain.local_2025_key_pass.pem 2048
openssl req -new -key srv-mail1.bergbkitzdomain.local_2025_key.pem -out srv-mail1.bergbkitzdomain.local_2025_csr.pem

openssl rsa -in srv-mail1.bergbkitzdomain.local_2025_key_pass.pem -out srv-mail1.bergbkitzdomain.local_2025_key.pem # PW entf.

openssl x509 -req -in srv-mail1.bergbkitzdomain.local_2025_csr.pem -signkey srv-mail1.bergbkitzdomain.local_2025_key.pem -out srv-mail1.bergbkitzdomain.local_2025_cert.pem -days 3560 -sha256 -extfile v3.ext
```

* **Key & CSR f. ext. CA erstellen**
```
openssl req -new -nodes -keyout dateiname.key -out dateiname.csr -newkey rsa:2048
```

* **Key-Passwort ändern**
```
openssl rsa -des3 -in client_key.pem -out client_key.pem.new
openssl pkcs8 -topk8 -in client.key.org -out client.key.test
```

* **Ubuntu CA-Zertifikat hinzufügen**
```
cp cert.crt /usr/local/share/ca-certificates
sudo update-ca-certificates
```

* **Arch CA-Zertifikat hinzufügen**
```
cp cert.crt /etc/ca-certificates/trust-source/anchors
sudo update-ca-trust
```

* **Zertifikat anzeigen**
```
openssl s_client -showcerts -servername mail.callidus.at -connect mail.callidus.at:443 </dev/null
```

* **PKCS12 anzeigen**
```
openssl pkcs12 -in server.p12 -info
```

* **PKCS12 erstellen**
```
openssl pkcs12 -export -inkey server.key -in server.crt -out server.p12
# oder f. Kompatibiltät mit älteren Windows-Versionen:
openssl pkcs12 -export -legacy -inkey server.key -in server.crt -out server.p12
# oder mit Intermediate:
openssl pkcs12 -export -inkey server.key -in server.crt -certfile intermediate.crt -out server.p12 # ①
```
1. Man kann nur 1 Intermediate angeben - will man mehrere, vorher mit `cat` ein .pem erstellen

**🔥 CAUTION**\
Ältere Windows-Versionen können an der Stelle u.U. kein AES256 -> `-legacy` verwenden, sonst kommt die irreführende Fehlermeldung "Falsches Kennwort" beim Import!

* **PFX konvertieren**
```
openssl pkcs12 -in file.pfx -nocerts -out file_pw_key.pem
openssl pkcs12 -in file.pfx -clcerts -nokeys -out file_cert.pem
openssl rsa in file_pw_key.pem -out file_nopw_key.pem
```

* **.cer zu .pem**
```
openssl x509 -inform der -in cert.cer -out cert.pem
```

* **Test auf FREAK-Attacke**
```
openssl s_client -connect www.mein-server.de:443 -cipher EXPORT
```

**📌 NOTE**\
Kommt "handshake failure", ist der Server nicht angreifbar

* **SMTP-Session mit TLS**
```
openssl s_client -connect mail.example.com:25 -starttls smtp
# oder manchmal nötig:
openssl s_client -connect mail.example.com:25 -starttls smtp -crlf
```

* **POP3 mit TLS**
```
openssl s_client -connect mail.example.com:995 -crlf # crlf f. Exchange nötig!
```

* **Ausgabe des Zertifikats eines SMTP-Servers (inkl. Ablaufdatum usw.)**
```
echo | openssl s_client -starttls smtp -connect mail2.bergbahn-kitzbuehel.at:25 2>/dev/null | openssl x509 -noout -text
```

* **Zertifikat extrahieren**
```
echo -n | openssl s_client -connect swadsp01.ci.local:389 -starttls ldap 2>/dev/null | sed -ne '/-BEGIN CERTIFICATE-/,/-END CERTIFICATE-/p'
echo -n | openssl s_client -connect smtp.cibex.net:587 -starttls smtp 2>/dev/null | sed -ne '/-BEGIN CERTIFICATE-/,/-END CERTIFICATE-/p' | openssl x509 -text -noout

# oder nur einzelne Felder:
echo -n | openssl s_client -connect i-doit.cibex.net:443 2>/dev/null | sed -ne '/-BEGIN CERTIFICATE-/,/-END CERTIFICATE-/p' | openssl x509 -noout -subject -issuer -enddate
```

* **Prüfen ob Zertifikat zu Key gehört (Output muss identisch sein)**
```
openssl x509 -noout -modulus -in certificate.pem | openssl md5
openssl rsa  -noout -modulus -in certificate.key | openssl md5
openssl req  -noout -modulus -in certificate.csr | openssl md5
```

## Zertifikate validieren

```
openssl verify wildcard_eca.at.cert.pem
openssl verify -CAfile wildcard_eca.at.chain.pem wildcard_eca.at.cert.pem
openssl verify -CAfile ca.pem -untrusted intermediate.pem cert.pem # intermediate auch mehrmals!
```

## Chain prüfen

```
openssl crl2pkcs7 -nocrl -certfile cert_and_chain.pem | openssl pkcs7 -print_certs -noout
# oder einzeln:
openssl x509 -noout -subject -issuer -in cert.pem
```

## TLS-Version erzwingen

```
openssl s_client -connect sldirt01.l1.r1.cbx.c:389 -starttls ldap -tls1_3
```

**Tls-Versionen**

* `-tls1`
* `-tls1_1`
* `-tls1_2`
* `-tls1_3`
