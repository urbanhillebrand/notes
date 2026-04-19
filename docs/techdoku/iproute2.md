# iproute2

* **Hilfe**
```
ip help
ip addr help # usw.
```

* **Status**
```
ip addr
ip addr show dev eth1
ip link
ip link show dev eth1
ip link dev eth2 up
```

* **Adressen/Link properties**
```
ip addr add 192.168.1.1/24 dev eth0
ip addr del 192.168.1.1/24 dev eth0
ip addr add 1.2.3.4/24 dev eth1 label eth1:77 # mit altem Alias wie von ifconfig
ip link set eth0 up
ip link set eth1 mtu 9000
```

* **VLAN**
```
ip link add link eth0 name eth0.80 type vlan id 80
ip link set dev eth0.80 up
ip addr add 10.128.192.1/24 dev eth0.80
```

* **Routen**
```
ip route add default via 192.168.1.1 dev eth0
ip rote add 10.0.0.0/24 via 192.168.1.1
ip route add 10.0.0.0/24 dev eth0
ip route replace .... # added falls nicht vorhanden
ip route get 192.168.1.5 # route zu ziel anzeigen
```

* **arp**
```
ip neigh # show arp table
ip neigh show dev eth0
ip neigh add 192.168.1.1 lladdr 1:2:3:4:5:6 dev eth0
ip neigh del 192.168.1.1 dev eth0
ip neigh replace ...
```

* **Sonstiges**
```
ip -s link # statistics
```

* **nstat**
```
nstat -rn # no output, reset counters
nstat -s # statistics (wie netstat -s)
nstat -s '*Listen*' # filter
```

## Troubleshooting ICMP-redirect probs (FRE)

```
ip ro get 1.2.3.4
ip ro show cache
ip ro flush cache
```

**-> in `/etc/sysctl.d/96-disable-redirects.conf`:**

```
net.ipv4.conf.all.accept_redirects=0
net.ipv4.conf.all.secure_redirects=0
net.ipv4.conf.default.accept_redirects=0
net.ipv4.conf.default.secure_redirects=0
```

Dann mit "sysctl --system" neue Params laden.

## policy based routing

Neue Routingtabelle erstellen:

`ip ro repl default via 1.2.3.4 table 200`

oder mit Namen statt Nummer:
```
echo "200   voip" >>/etc/iproute2/rt_tables
ip ro repl default via 1.2.3.4 table voip
```

Auflisten:

`ip ro list table 200`

### Traffic via iproute2 ausfiltern

`ip rule add from 2.2.2.2 table 200`

`ip rule list`

### flexibler: Traffic via iptables ausfiltern

```
ip rule add fwmark 200 table 200 #<1>
iptables -t mangle -F
iptables -t mangle -a PREROUTING -i eth0 -s 2.2.2.2 -j MARK --set-mark 200
```
1. Die Markierung und die custom routing table müssen nicht gleich heißen, dient nur der Übersichtlichkeit
