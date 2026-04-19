# arp

## remote cache flush erzwingen

### arping

`arping -c 1 -S so.ur.ce.ip ro.ut.er.ip`

**📌 NOTE**\
Angabe von Source-IP nur auf multihomed-Routern zwingend

### arp-sk

**🔥 CAUTION**\
Alt, kompliziert, deprecated, wird wohl nicht mehr weiterentwickelt -> `arping` verwenden!

**Beispielscript für moxen**

```
INETDEV=eth1
GW=10.2.0.100

ip addr show dev $INETDEV | while read -r line; do
    IPLONG=$(echo "$line" | awk '/[:blank:]*inet/ { print $2 }')
    [ ! -z $IPLONG ] && (
    IP=${IPLONG%/*} # strip netmask
    echo "---------- SENDE ARP-SK AN GW $GW fuer IP $IP ----------"
    arp-sk -i $INETDEV -r -d $GW -S $IP -D $GW -c1
    )
done
```
