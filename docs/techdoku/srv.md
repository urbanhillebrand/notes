# srv.urban.intra

## evpn

**`evpn-urban.conf`**

```
# settings f. torrent-route
route-noexec            # route von evpn ignorieren
script-security 2       # up/down-Scripts erlauben
up /etc/openvpn/evpn.up
down /etc/openvpn/evpn.down
```

**`evpn-up`**

```
#!/bin/sh

iptables -t mangle -A OUTPUT -m owner --uid-owner torrent -j MARK --set-mark 3
iptables -t mangle -A OUTPUT -m owner --uid-owner debian-transmission -j MARK --set-mark 3
iptables -t nat -A POSTROUTING -o $1 -j SNAT --to-source $4

ip rule add fwmark 3 lookup 200
ip route add 10.2.0.0/24 dev eno1 table 200
ip route add default via $5 dev $1 table 200
```

**`evpn-down`**

```
#!/bin/sh

iptables -t mangle -D OUTPUT -m owner --uid-owner torrent -j MARK --set-mark 3
iptables -t mangle -D OUTPUT -m owner --uid-owner debian-transmission -j MARK --set-mark 3
iptables -t nat -D POSTROUTING -o $1 -j SNAT --to-source $4

ip rule delete fwmark 3
ip route flush table 200
```
