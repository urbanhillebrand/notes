# DHCP

## Tools

**`broadcast-dhcp-discover` mit `nmap`**

```
sudo nmap --script broadcast-dhcp-discover
Starting Nmap 7.92 ( https://nmap.org ) at 2022-03-22 10:11 CET
Pre-scan script results:
| broadcast-dhcp-discover:
|   Response 1 of 1:
|     Interface: wl-int
|     IP Offered: 10.2.0.207
|     DHCP Message Type: DHCPOFFER
|     Server Identifier: 10.2.0.100
|     IP Address Lease Time: 2h00m00s
|     Subnet Mask: 255.255.255.0
|     Router: 10.2.0.100
|     Domain Name Server: 10.2.0.100
|_    Domain Name: urban.intra
```

**`dhcp-discover` mit `nmap`**

```
sudo nmap -sU -p 67 --script=dhcp-discover 10.2.0.100 # verwendet DHCPINFORM
sudo nmap -sU -p 67 --script=dhcp-discover --script-args dhcp-discover.dhcptype=DHCPREQUEST 10.2.0.100
```

**`dhcping` für gezielte Abfrage (aber keine Ausgabe von Offer etc.)**

```
sudo dhcping
sudo dhcping -s 10.2.0.100        # gezielt an Server
sudo dhcping -h 01:02:03:04:05:06 # MAC angeben
Got answer from: 10.2.0.100
```

* `dhcping` sollte mit `-v` eigentlich die Antwort ausgeben -> das scheint nicht zu funktionieren -> paralleler `tcpdump` nötig!
* bei Angabe einer MAC sieht man die Antwort ohnehin nur in einem `tcpdump`
