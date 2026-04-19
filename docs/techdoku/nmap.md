# nmap

## Snippets

```
nmap --script ssl-cert -p 443 cloud.cibex.net
nmap --script ssl-enum-ciphers -p 636 vmfocal1.urban.intra # zeigt noch kein TLS 1.3, nur ältere!

sudo nmap -sU -p 67 --script=dhcp-discover
sudo nmap --script broadcast-dhcp-discover
```
