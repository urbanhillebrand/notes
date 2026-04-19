# OPNsense

**Aliase updaten**

```
/usr/local/bin/flock -n -E 0 -o /tmp/filter_update_tables.lock /usr/local/opnsense/scripts/filter/update_tables.py
```

**Konfiguration inkl. Aliase lesen**

```
view /conf/config.xml
```

**Logfilter-Script** (`filter_log.awk`)

```
#!/usr/bin/awk -f

# Beispielaufruf: awk -f filter_log.awk -v d_port=25 -v act=block </var/log/filter/latest.log

BEGIN {
    FS = ","  # OPNsense Filter-Logs sind CSV-basiert
    print "timestamp                  | action | interface | dir | source:port -> destination:port"
    print "-----------------------------------------------------------------------------------------------"
}

{
    # Zuweisung der Felder (basierend auf Standard OPNsense Logging)
    log_ident     = $1
    log_interface = $5
    log_action    = $7
    log_direction = $8
    log_src_ip    = $19
    log_dst_ip    = $20
    log_src_port  = $21
    log_dst_port  = $22

    # Filter-Logik: Falls eine Variable gesetzt ist, muss sie übereinstimmen.
    # Wenn sie leer ist, wird dieser Filter ignoriert.
    # Variablen werden mit -v an der Commandline übergeben.
    # s_ip, d_ip und iface sind regex-matches!
    if ((s_ip == ""   || log_src_ip ~ s_ip) &&
        (d_ip == ""   || log_dst_ip ~ d_ip) &&
        (s_port == "" || log_src_port == s_port) &&
        (d_port == "" || log_dst_port == d_port) &&
        (dir == ""    || log_direction == dir) &&
        (iface == ""  || log_interface ~ iface) &&
        (act == ""    || log_action == act)) {
             split(log_ident, liarray, " ")
             printf "%-26s | %-6s | %-9s | %-3s | %s:%s -> %s:%s\n", 
                       liarray[2], log_action, log_interface, log_direction, log_src_ip, log_src_port, log_dst_ip, log_dst_port
        }
}
```

!!! info Beispiele
```
awk -f filter_log.awk -v act=block <logfile
awk -f filter_log.awk -v act=block -v s_ip=1.2.3.4 <logfile
awk -f filter_log.awk -v act=block -v s_ip='^1.2.3.4$' <logfile # gleich wie vorige Zeile, aber exakten Match erzwungen
awk -f filter_log.awk -v iface='^vlan' -v s_ip='^10\.8\.' <logfile
```
!!!

!!! warning Achtung
Nur `s_ip`, `d_ip` und `iface` werden per regex gematcht - alle anderen Felder müssen exakt übereinstimmen.
!!!

## Debugging

* **Rules ausgeben**\
`pfctl -vvsr`
* **zugehörige Labels finden**\
`view /tmp/rules.debug`

**Weitere `pfctl`**

```
pfctl -d # disable
pfctl -e # enable
pfctl -si # Info
pfctl -sr # show rules (auch mit -v)
pfctl -sr # show states
pfctl -sn # show nat (auch mit -v)
pfctl -sl # labels (?)

pfctl -t mytable -T show # table anzeigen
pfctl -t mytable -T test 1.2.3.4 # prüfen ob Eintrag in table vorh.
```

## Logfilerecherche

Exakte Syntax: [github](https://github.com/opnsense/ports/blob/master/opnsense/filterlog/files/description.txt)

**Wichtige Feldnummern f. `awk`**

|     |     |
| --- | --- |
| 5 | interface |
| 7 | action (pass, block) |
| 17 | protoname (tcp, udp) |
| 19 | srcip |
| 20 | dstip |
| 21 | srcport |
| 22 | dstport |

**Snippets**

```bash
awk -F, '$19 == "10.128.8.34"' fre-sample.log
awk -F, '$5 == "vlan0.100" && $19 == "10.128.8.34"'
awk -F, '$5 ~ /ovpns[34]/' latest.log
awk -F, '$5 == "ovpns4" && $22 ~ /445|1433/ { print $1, $19, $20 ":", $22 }' latest.log

awk -F, 'BEGIN {time=1; interface=5; action=7; proto=17; srcip=19; dstip=20; srcport=21; dstport=22} \
 $srcip=="10.2.33.137" && $action=="block"' latest.log

awk -F, 'BEGIN {time=1; interface=5; action=7; proto=17; srcip=19; dstip=20; srcport=21; dstport=22} \
 $srcip=="10.2.33.137" && $action=="block" \
 { print $interface, ":", $srcip, "->", $dstip, ":", $dstport }' latest.log
```

**Parameter (mit `-v` angeben):**

|     |     |
| --- | --- |
| i | interface |
| a | action (block, pass) |
| p | proto (tcp, udp) |
| si | source ip |
| di | dst ip |
| sp | source port |
| dp | dst port |

So übergebene Parameter sind regex-String-Matches - `-v si=172.16.128.1` matcht also auch auf `172.16.128.111`.

Abhilfe: `-v si='172.16.128.1$'`

**Logfilter für copy/paste mit benannten Spalten**

```bash
awk 'BEGIN {FS=,"; time=1; interface=5; action=7; proto=17; srcip=19; dstip=20; srcport=21; dstport=22} $srcip=="10.2.33.137" && $action=="block" { print $interface, ":", $srcip, "->", $dstip, ":", $dstport }' latest.log
```

## API

### bash / curl

**Environment bereitstellen**

```bash
cat env_urban.lab
key=xNdPdIbg5wPxbRsSUJvakhXmU7TSxpraH/gEpKJ9L79FHyTe0L2ZcZZ9rtKbJYLAk8LJT6/Nr3iw7UyR
secret=3RiBV2usFHDK4ozNYRjVPsryvmluNKTe0ZDYOes5UFqDQFf0B9MzgdBZZJzeNnzI0Qk+g6GrPvbIvb3m
baseurl=https://vmopnsense.urban.lab

source env_urban.lab
```

**Beispiele**

```bash
curl -s -k -u "$key":"$secret" $baseurl/api/core/firmware/info
curl -s -k -u "$key":"$secret" $baseurl/api/core/firmware/status

curl -XPOST -d "" -s -k -u "$key":"$secret" $baseurl/api/core/firmware/check
curl -XPOST -d "" -s -k -u "$key":"$secret" $baseurl/api/core/firmware/update
curl -XPOST -d "" -s -k -u "$key":"$secret" $baseurl/api/core/firmware/upgrade
```

* `update` für minor Updates
* `upgrade` für major Updates

### perl

```perl
#!/usr/sbin/perl

use strict;
use warnings;
use MIME::Base64;
use REST::Client;
use Data::Dumper;

my $key='xNdPdIbg5wPxbRsSUJvakhXmU7TSxpraH/gEpKJ9L79FHyTe0L2ZcZZ9rtKbJYLAk8LJT6/Nr3iw7UyR';
my $secret='3RiBV2usFHDK4ozNYRjVPsryvmluNKTe0ZDYOes5UFqDQFf0B9MzgdBZZJzeNnzI0Qk+g6GrPvbIvb3m';
my $baseurl='https://vmopnsense.urban.lab';
my $apireq='api/core/firmware/running';

my $client = REST::Client->new();
$client->setHost("$baseurl");

my $headers = {
    Authorization => 'Basic ' . encode_base64($key . ':' . $secret, ""),
};

$client->GET("$apireq", $headers);

print $client->responseCode() . "\n";
print $client->responseContent(). "\n";

#print Dumper($headers);
```
