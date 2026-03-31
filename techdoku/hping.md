# hping

|     |     |
| --- | --- |
| hping3 --icmp 1.1.1.1 | ICMP ping |
| hping3 --ack 1.1.1.1 | ACK scan; prüft ob RST auf ACK kommt (geht nicht via stateful fw) |
| hping3 --udp 1.1.1.1 -p 80 | UDP port scan |
| hping3 --scan 10,20-30,80-90 --syn srv.urban.lan -V |  |
| hping3 --icmp 10.10.0.x --rand-dest -I en-dock | subnet scan mit ICMP (langsam) |
| hping3 srv.urban.lan -p 80 --syn | syn-Packet auf Zielport |
| hping3 srv.urban.lan -p 80 -s 123 --syn | syn-Packet auf Zielport mit explizit gesetztem Quellport |
| hping3 --syn srv.urban.lan --spoof 192.168.1.1 -p 80 --flood | SYN-flood mit gespoofter IP |
