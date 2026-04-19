# linux-tools

## Doku

    tldr <cmd>   # Kurz-Doku anhand von Beispielen

    # cht.sh, tw. etwas ausführlicher
    curl -s https://cht.sh/:cht.sh | install /dev/stdin ~/bin/cht.sh -m 755
    sudo pacman -S rlwrap

    cht.sh <cmd>
    cht.sh $(cht.sh :list | fzf)

## Snippets

**`ping` nur für fehlende Pakete**

```
ping -Oq 1.2.3.4
```

**`fzf`**

* `eval "$(fzf --bash)"`
* `Ctrl-T` durchsucht Files/Folder im pwd (auch mitten unter Command!)
* `Ctrl-R` wird ersetzt
* `Alt-c` fuzzy find directory unterhalb von pwd
* `***` -> z.B. `ssh *** <tab>`, `kill ** <tab>`
* während fuzzy-find Auswahl mit `Tab`, bestätigen mit `Enter`

**base64 encode/decode**

```
base64 -d <<< 'QVc6IFNGSTogVlBOIGbDvHIgU2NodWNodGVyIEZlbnN0ZXIgSW1zdA=='
# oder
openssl enc -base64 -d <<< 'QVc6IFNGSTogVlBOIGbDvHIgU2NodWNodGVyIEZlbnN0ZXIgSW1zdA=='
```

## Troublshooting PMTUD (path MTU discovery)

### Linux

    ping -M do -s $((1500-28)) cloud.cibex.net

**📌 NOTE**\
Wir müssen 20 bytes für den IP-Header, und 8 bytes für den ICMP-Header abziehen

Funktioniert PMTUD, bekommen wir in der Antwort die korrekte Länge angezeigt:

```
ping -M do -s 1700 cloud.cibex.net
PING cloud.cibex.net (80.120.70.235) 1700(1728) bytes of data.
ping: local error: message too long, mtu=1500
```

### Windows

    ping -f -l 1472 cloud.cibex.net

Windows zeigt die korrekte MTU nicht direkt an, diese kann jedoch mit

    netsh interface ipv4 show destinationcache address=80.120.70.235

1. herausgefunden werden.
