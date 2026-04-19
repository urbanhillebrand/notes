# wireshark

## Capture filters

pcap-filter Syntax

## Display filters

**Bsp**

```
dns

! (smb || smb2 || snmp)

tcp.port eq 25 or icmp

ip.addr==1.2.3.4

ip.src==1.2.3.4/x

ip.src != xxx.xxx.xxx.xxx && ip.dst != xxx.xxx.xxx.xxx && sip

http.request.uri matches "gl=se$" # regex!

ip and not ip.geoip.country == "United States" ①
```
1. Benötigt GeoIP (s.u.)

`ip.addr` ist ein Alias fuer `ip.src==... or ip.dst==...`. Damit ist die Negierung nicht trivial möglich.

So geht´s:
`! ( ip.addr == 1.2.3.4 )`

## GeoIP

**📌 NOTE**\
Benötigt MaxMind GeoIP-Datenbanken (Registrierung) in einem lokalen Verzeichnis!

* Je nach gewünschter Genauigkeit die gewünschten Datenbanken (Country, City...) im `.mmdb`-Format herunterladen (auf maxmind.com[MaxMind] anmelden, Downloadseite im Profil)
* Datenbanken in ein Verzeichnis kopieren, und dieses unter menu:Edit[Preferences > Name Resolution] angeben
* Wireshark neu starten

-> Danach sind die Infos in einem Capture unter menu:Statistics[Endpoints], sowie im IPv4 packet detail ersichtlich.

Beispiele f. Display Filter s.o.
