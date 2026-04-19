# mox

## Temporäres Logging in eigenes File

**/etc/syslog-ng.conf**

```
destination iptablesdebuglog { file("/var/log/iptablesdebug.log"); };
filter f_iptablesdebug { level(debug) and match("FWDEBUG"); };
log { source(kern);  filter(f_iptablesdebug); destination(iptablesdebuglog); flags(final); }; # ①
echo "etc/syslog-ng.conf" >>/mox/config.tarlist
rsyslogd restart
```
1. Zeile vor `log { source(kern);  destination(kernlog); flags(final); };` stellen

**/etc/firewall/firewall_rules**

```bash
# Logging-Chains f. Debugging
for chain in logaccept logdrop logreject; do
  $cmd -N $chain
done

# logaccept
$cmd -A logaccept -j LOG --log-prefix "FWDEBUG ACCEPT " --log-level 7
$cmd -A logaccept -j ACCEPT


# logdrop
$cmd -A logdrop -j LOG --log-prefix "FWDEBUG DROP " --log-level 7
$cmd -A logdrop -j DROP

# logreject
$cmd -A logreject -j LOG --log-prefix "FWDEBUG REJECT " --log-level 7
$cmd -A logreject -j REJECT
```

**💡 TIP**\
Danach kann Logging für Rules mit den Targets `log{accept,drop,reject}` aktiviert werden.
