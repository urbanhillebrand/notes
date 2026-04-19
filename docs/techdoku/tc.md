# tc

## Basics

* Eingehenden Traffic am Internet am internen Interface -> Kontrolle wie schnell Pakete gesendet werden
* Jede root-qdisc hat mindestens eine root-Klasse
* In die Klassen unterhalb der root-Klasse ("leafs") wird Traffic über Filter oder
iptables (oder den Default-Eintrag) zugeordnet
* shaping erfolgt innerhalb der leafs, nie in der root-Klasse - diese existiert
nur um "borrowing" zu kontrollieren (welches nur innerhalb einer Root-Classe
erfolgen kann)
* Man kann die root-Klasse auch weglassen (leafs unterhalb der qdisc) - dann
erfolgt keinerlei "borrowing"
* wird "ceil" nicht definiert, ist es automatisch gleich groß wie "rate" (der Subklasse!) 
* die Summe der rates der leafs sollte im Idealfall gleich groß wie die rate
der root-class sein
* tc-Syntax: kbps means kilobytes and kbit means kilobits!
* an die leaf-classes hängt man noch einen sfq qdisc an (verteilt den Traffic an wartende Verbindungen besser) 

## Links

[LARTC](http://lartc.org/howto/lartc.qdisc.classful.html), [HTB](http://luxik.cdi.cz/~devik/qos/htb/manual/userg.htm)

## Snippets

```
tc qdisc show dev eth0
tc qdisc del dev eth0 root
tc class show dev eth0

tc filter show dev eth0
tc filter del dev eth0
```

**💡 TIP**\
Wird die qdisc gelöscht, werden auch automatisch alle zugehörigen classes und filters gelöscht

**Statistiken**

```
tc -s -d qdisc show dev eth1
tc -s -d class show dev eth1
```

## Konfigurationsbeispiele

**1 HTB qdisc, nur outbound shaping, filter expressions**

```bash
#!/bin/bash

# vars
INTIF=eth0
EXTIF=eth1

# root qdisc
tc qdisc add dev $EXTIF root handle 1: htb default 50

# root class
tc class add dev $EXTIF parent 1: classid 1:1 htb rate 150mbit burst 15k

# leaf classes
#tc class add dev $EXTIF parent 1:1 classid 1:10 htb rate 2mbit ceil 15mbit burst 15k
tc class add dev $EXTIF parent 1:1 classid 1:10 htb rate 20mbit burst 15k
#tc class add dev $EXTIF parent 1:1 classid 1:50 htb rate 10mbit ceil 15mbit burst 15k
tc class add dev $EXTIF parent 1:1 classid 1:50 htb rate 10mbit burst 15k
#tc class add dev $EXTIF parent 1:1 classid 1:90 htb rate 3mbit ceil 3mbit burst 15k
tc class add dev $EXTIF parent 1:1 classid 1:90 htb rate 3mbit burst 15k

# sfq
tc qdisc add dev $EXTIF parent 1:10 handle 10: sfq perturb 10
tc qdisc add dev $EXTIF parent 1:50 handle 50: sfq perturb 10
tc qdisc add dev $EXTIF parent 1:90 handle 90: sfq perturb 10
```

## Filter

### nur mit tc (`tc filter`)

siehe [LARTC](http://lartc.org/howto/lartc.qdisc.filters.html#LARTC.FILTERING.SIMPLE)

### mit tc und iptables

**tc**

```
tc filter add dev $EXTIF parent 1:0 protocol all handle 1 fw classid 1:10
tc filter add dev $EXTIF parent 1:0 protocol all handle 9 fw classid 1:90
```

_Anm.: Nicht spezifzierter Traffic landet somit in der Default-Class (1:50)_

**iptables**

```
iptables -t mangle -F
iptables -A PREROUTING -t mangle -p tcp --dport 80 -j MARK --set-mark 1
iptables -A PREROUTING -t mangle -p tcp --dport 25 -j MARK --set-mark 9
```

**🔥 CAUTION**\
Verwendung von `-i eth1` hat hier nicht funktioniert (aller Traffic in der Default-Class)

## Offene Fragen
* Quantum für kleine Bandbreiten (kleiner als MTU) manuell definieren? (s. [HTB](http://luxik.cdi.cz/~devik/qos/htb/manual/userg.htm))

## Performance / Funktionstest mit virtuellen Servern

KVM auf Thinkpad T470

**Clients:**

* Ubuntu 14.04
* 1 GB RAM

**Firewalls:**

* mox-Image 2015
* Kernel 2.6.23
* 256 MB RAM
* 1 vCPU

WARN: Test via mox ohne Limitierung crasht die mox bei Verwendung von rtl8139 als NIC - musste auf e1000 umgestellt werden (damit wird auch Gigabit-Link ermöglicht)

Tests mit **iperf**

### Ohne shaping

| Verbindungsart | Durchsatz | Anmerkungen |
| --- | --- | --- |
| direkt | 28,2 Gbits/sec |  |
| via mox | ~600-700 Mbits/s | mit oder ohne NAT |

### Mit shaping

**Mit einer root qdisc:**

* 1 root qdisc, 15mbit, def. leaf = 50
* 3 leaf-classes:
  * classid 10 f. priorisierten Traffic
  * classid 50 10/- f. Default-Traffic
  * classid 90 3/3 f. nieder priorisierten Traffic
* unter jeder subclass noch eine sfq-qdisc

|  | 2/15, 10/15, 3/3 | 2/15, -, 3/3 |
| --- | --- | --- |
| **id 10** | 2,0 | 11,3  |
| **id 50** | 9,6 | - |
| **id 90** | 2,9 | 2,8  |
