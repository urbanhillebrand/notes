# ubuntu

## Partition nach HD-Vergrößerung im Hypervisor anpassen

Geht nur, wenn die **letzte** Partition zu vergrößern ist. `growpart` vergrößert bis zum Plattenende, oder ansonsten bis zur nächsten Partition.

- zuerst im HV vergrößern
- mit `lsblk` prüfen, ob die VM die vergrößerte Platte erkennt (sonst `echo 1 > /sys/class/block/sda/device/rescan` oder zur Not Reboot)
- `sudo growpart /dev/sdX n` (mit Blank; `n` ist die Partitionsnummer)
- PV erweitern: `sudo pvresize /dev/sdXn`

## alte Kernel-Versionen entfernen

```bash
dpkg -l 'linux-*' | sed '/^ii/!d;/'"$(uname -r | sed "s/\(.*\)-\([^0-9]\+\)/\1/")"'/d;s/^[^ ]* [^ ]* \([^ ]*\).*/\1/;/[0-9]/!d' | xargs sudo apt-get -y purge

#oder

dpkg -l 'linux-[ihs]*' | sed '/^ii/!d;/'"$(uname -r | sed "s/\([-0-9]*\)-\([^0-9]\+\)/\1/")"'/d;s/^[^ ]* [^ ]* \([^ ]*\).*/\1/;/[0-9]/!d' | tee zu_entfernende_Kernel 
cat zu_entfernende_Kernel | xargs sudo apt-get -y purge 
```

## IPv6 f. apt deaktivieren

```bash
echo 'Acquire::ForceIPv4 "true";' > /etc/apt/apt.conf.d/99disable-ipv6
```

## Bsp.: preseeding f. postfix

```""
RUN echo mail > /etc/hostname
RUN echo "postfix postfix/main_mailer_type string Internet site" > preseed.txt
RUN echo "postfix postfix/mailname string mail.example.com" >> preseed.txt

# load pre config for apt
RUN debconf-set-selections preseed.txt
```
