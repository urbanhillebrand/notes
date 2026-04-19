# journalctl

**Anzeige**

|     |     |
| --- | --- |
| journalctl | startet beim ältesten Eintrag |
| journalctl -n | letzte 10 Einträge |
| journalctl -n 33 | letzte 33 Einträge |
| journalctl -f | follow |
| journalctl -e | springt zum Ende |
| journalctl -r | reverse (neue zuerst) |

**boots**

|     |     |
| --- | --- |
| journalctl -b | current boot |
| journalctl --list-boots | zeigt alle aufgezeichneten Reboots an |
| journalctl -b -1 | Einträge vom vorherigen Boot |
| journalctl -b i5a9aa9... | ID von `list-boots` kann angegeben werden |

**Datum und Zeit**

|     |     |
| --- | --- |
| journalctl --utc | Zeiten in UTC |
| journalctl --since "9:00" | alles ab Uhrzeit |
| journalctl --since "2020-02-20" | alles ab Datum |
| journalctl --since "2020-02-20 15:00" | inkl. Datum und Uhrzeit |
| journalctl --since "..." --until "..." | von - bis |
| journalctl --since "9:00" --until "1 hour ago" | today, yesterday, now, ago... |
| journalctl --since "2 days ago" | today, yesterday, now, ago... |
| journalctl --since -2d | gleich wie "2 days ago" |
| journalctl --since -1h | 1 hour ago (m=minutes, h=hours, d=days...) |
| journalctl -S -1h | 1 hour ago (m=minutes, h=hours, d=days...) |
| journalctl -S -1h -U -10m | zwischen 1h und 10m ago |

**Einschränken**

|     |     |
| --- | --- |
| journalctl -u apache -u postfix | Unit(s) |
| journalctl -t postfix | syslog identifier |
| journalctl -u postfix -g suchstring | grep mit PCRE (wenn alles klein dann case insensitive!) |
| journalctl _PID=8088 | auf process ID |
| journalctl _UID=33 | auf UID |
| journalctl _GID=22 | auf GID |
| journalctl -F _PID | zeigt alle aufgezeichneten Werte für `_PID` an |
| journalctl /usr/sbin/postfix | alle Einträge zu Executable |
| journalctl --no-hostname | keine Ausgabe des Hostnames |
| journalctl -o with-unit | vollständige Ausgabe der Units |

**📌 NOTE**\
s. `man systemd.journal-fields`

**Prioritäten**

|     |     |
| --- | --- |
| journalctl -p err | alles mit Prio `err` (oder höher) |

**Verfügbare Prioritäten**

* 0: emerg
* 1: alert
* 2: crit
* 3: err
* 4: warning
* 5: notice
* 6: info
* 7: debug

Es kann die Nummer oder der Name verwendet werden.

**Sonstiges**

|     |     |
| --- | --- |
| journalctl -k | kernel ring buffer (dmesg) |

## Output

Normalerweise wird ein Pager verwendet. Mit den Pfeiltasten kann auf abgeschnittene Teile zugegriffen werden.

Mit `journalctl --no-pager` kann der Pager deaktiviert werden.

Output kann auch in anderen Formaten ausgegeben werden, z.B. mit `-o json` oder `-o json-pretty´.

## Plattenplatz

|     |     |
| --- | --- |
| journalctl --disk-usage | zeigt verwendeten Platz an |
| journalctl --vaccuum-size=1G | auf angegebene Größe verkleinern |
| journalctl --vacuum-time=10days | auf angegebene Datumsrange verkleinern |

Konfiguration in `/etc/systemd/journald.conf` (`SystemMaxUser`, `SystemKeepfree` etc.)
