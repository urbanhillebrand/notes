# tcpdump

**Snippets**

```bash
tcpdump ether host aa:bb:cc:11:22:33
tcpdump -ni en-int "tcp[tcpflags] & (tcp-syn|tcp-fin) != 0"
tcpdump -ni vtnet1 host 185.212.101.40 and icmp and "icmp[0]!=0" and "icmp[0]!=8" # alles außer icmp echo req / reply
tcpdump -i igb0 -nn -e vlan # inkl. vlan-tag!
```

## Komplexere Beispiele

### Sniffen nach einem Teil der MAC-Adresse

(s. [Ethernet Frame](https://en.wikipedia.org/wiki/Ethernet_frame))

Die dst MAC ist ab Offset 0, die src MAC ab Offset 6. Folgendes Beispiel snifft nach src oder dst MAC mit 2 vorgegebenen Octets (`48:ee`):

    tcpdump -nei igb0 \( ether [0:2] == "0x48ee" or ether [6:2] == "0x48ee" \)

**📌 NOTE**\
Die Größe muss 1, 2 oder 4 sein, man kann also so nicht z.B. nach den ersten 3 Octets suchen.

## tcpdump remote

**📌 NOTE**\
Scheint auf moxen nicht zu funktionieren (Ubuntu, OPNsense schon)

    ssh opnsense.urban.lan "sudo tcpdump -Unw - -i igb1 icmp" > ~/tmp/test.pcap

oder gleich in wireshark anzeigen mit

    wireshark -k -i <( ssh opnsense.urban.lan sudo tcpdump -i igb0 -s 0 -U -n -w - icmp )

oder mit FIFO

```bash
mkfifo ~/tmp/pcap/fifo.pcap
wireshark -k -i ~/tmp/pcap/fifo.pcap
ssh opnsense.urban.lan "sudo tcpdump -U -n -w - -i igb1" > /tmp/pcap/fifo.pcap
```
