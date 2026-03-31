# ipsec

## Sample-Files f. mox

**/etc/ipsec.d/ipsec.conf**

```
# basic configuration
config setup
        # strictcrlpolicy=yes
        # uniqueids = no
        # charondebug="ike 0, ksnl 0, cfg 0"
        charondebug="ike 2, ksnl 2, cfg 2"

# default site-to-site config
conn sts-base
    fragmentation=yes
    dpdaction=restart
    ike=aes192gcm16-aes128gcm16-prfsha256-ecp256-ecp521,aes192-aes128-sha256-modp3072!
    esp=aes192gcm16-aes128gcm16-ecp256-modp3072,aes192-aes128-sha256-ecp256-modp3072!
    keyingtries=%forever
    left=10.2.0.90
    leftsubnet=10.3.0.0/24

include /etc/ipsec.d/conf.mox8_2
include /etc/ipsec.d/conf.mox8_3
```

**/etc/ipsec./conf.mox8_2**

```
conn mox8_2
    also=sts-base
    authby=secret
    keyexchange=ikev2
    rightsubnet=192.168.0.1/24
    right=10.2.0.91
    auto=start
```

**/etc/ipsec.d/ipsec.secrets**

```
# mox8_2
10.2.0.90 10.2.0.91 : PSK "ImIjSPgc4F8N6z1sKqHcJI9+Dc71fdvrjPhanXvY0e4="
```

**Firewall-Rules**

* kein NAT f. ipsec-Traffic: `$cmd -A POSTROUTING -m policy --pol ipsec --dir out -j ACCEPT` (**vor** der globalen SNAT-Rule)
* esp, 500/udp sowie 4500/udp erlauben
* VPN-Traffic ausfiltern

  ```
  $cmd -A FORWARD -m policy --pol ipsec --dir in -j ipsec-to
  $cmd -A FORWARD -m policy --pol ipsec --dir out -j ipsec-from
  ```

**Traffic-Dumps**

Mit "normalem" tcpdump sieht man nur verschlüsselte Pakete, sowie ein- oder ausgehende Klartext-Pakete. Dumps müssen über das spezielle nflog-Interface erzeugt werden. s. https://wiki.strongswan.org/projects/strongswan/wiki/CorrectTrafficDump

**Sonstiges**

* `START_IPSEC="yes"` in `/mox/config.txt`
* PSK erstellen: `dd if=/dev/urandom count=1 bs=32 2>/dev/null | base64`
