# Network

Linux Netzwerk-Troubleshooting

## Dropped connections (Bsp. WSW)

siehe [hier](https://medium.com/@kaivanov/troubleshooting-syns-to-listen-sockets-dropped-message-from-netstat-a421316fd29b)

Detailliertes schrittweises Troubleshooting: [hier](http://www.linux-admins.net/2020/08/troubleshooting-syns-to-listen-sockets.html)

**Symptom**

```
root@desk01:~# netstat -s|grep -i listen
    6 times the listen queue of a socket overflowed
    139 SYNs to LISTEN sockets dropped
```

**Checks**

```
# transmit queue der NIC
ifconfig eth0 | grep txqueuelen
          collisions:0 txqueuelen:1000

# dropped packets due to transmit queue
tc -s qdisc show dev eth0 | grep dropped
 Sent 552433357373 bytes 584562122 pkt (dropped 0, overlimits 0 requeues 4856094)

# fragmentation problems?
cat /proc/net/snmp | grep '^Ip:' | cut -f17 -d' '

# accept queue des Services prüfen
# Recv-Q = # sockets in der accept queue
# Send-Q = # backlog parameter
ss -plnt sport = :443
State      Recv-Q Send-Q                  Local Address:Port                                 Peer Address:Port
LISTEN     0      511                                 *:443                                             *:*
#[...]

# # conns in SYN-RECV (werden dann ESTABLISHED)
ss -n state syn-recv sport = :443 | wc -l
41

# file descriptors
# # allocated file handles, nused but allocated file handles, system-wide max

sysctl fs.file-nr
fs.file-nr = 44736	0	6568616

# half-closed conns (warten auf FIN, ACK)
ss -n state time-wait | wc -l
1301

# established conns
ss -n state established | wc -l
15200

# Rate von neuen Verbindungen in Echtzeit darstellen
# ggf. conntrack oder conntrack-tools und pv installieren
# ggf. modprobe ip_conntrack
conntrack -E -e NEW | pv -l -i 1 -r >/dev/null

# Memory & Load prüfen
free -m
w

# dropped SYNs anzeigen
netstat -s | grep -i listen
    6 times the listen queue of a socket overflowed
    139 SYNs to LISTEN sockets dropped

nstat -az | grep -i listen
TcpExtListenOverflows           6                  0.0
TcpExtListenDrops               139                0.0
TcpExtTCPFastOpenListenOverflow 0                  0.0

```
