# DMARC

**DNS-Eintrag**

    _dmarc.cibex.net.	466	IN	TXT	"v=DMARC1; p=none; rua=mailto:dmarcreports@cibex.net"

**minimal**

    v=DMARC1; p=none;

**Schrittweise verschärfen**

```
v=DMARC1; p=none; rua=mailto:dmarcreports@cibex.net;

v=DMARC1; p=quarantine; pct=25; rua=mailto:dmarcreports@cibex.net

v=DMARC1; p=reject; pct=1; rua=mailto:dmarcreports@cibex.net
```
