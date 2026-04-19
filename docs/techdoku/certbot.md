# certbot

**Schnell Zertifikat ohne vHost-Konfiguration erhalten**

```
sudo certbot certonly -n --apache -d cloud.androschin-privatstiftung.at

# oder, falls erstes Mal:

sudo certbot certonly -n --apache -d cloud.androschin-privatstiftung.at --agree-tos --email cert@cibex.net

# oder, falls kein Webserver läuft:

sudo certbot certonly -n --standalone --email cert@cibex.net --agree-tos -d turn-muc.cibex.net
```

**Snippets**

```
sudo certbot certificates # zeigt alle Zertifikate inkl. der enthaltenen Domainnamen an

sudo certbot delete # zeigt Menü zum Löschen von Zertifikaten an

sudo certbot delete --cert-name example.com # .. oder gleich Zertifikat angeben
```
