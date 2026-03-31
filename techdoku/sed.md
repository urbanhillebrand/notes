# sed

**Bereiche**

```
sed -n '10p' # nur 10. Zeile ausgeben
sed -n '1,5p' # Zeilen 1-5 ausgeben
sed -n '10,+2p' # Zeile 10 + 2 folgende ausgeben
sed '1,3d' # Zeilen 1-3 löschen
sed -n '1~2p' # beginnend mit 1 jede 2. Zeile ausgeben
sed '/^START$/,/^END$/d' # zwischen den Suchbegriffen löschen
sed '/suchbegriff/s/alt/neu/'
sed '$d' # $ matcht auf letzte Zeile
sed '2,5 iHello' # insert vor matchenden Zeilen mit einer weiteren Zeile mit "Hello"
sed '2,5 aHello' # append nach matchenden Zeilen mit einer weiteren Zeile mit "Hello"
sed '/suchbegriff/ c replacetext # ersetzt matchende Zeilen
```

**📌 NOTE**\
Der Delimiter ist variabel (z.B. bei Ersetzung von Filenamen: `sed 's!pfad=.*!pfad=/neuer/pfad!'`)

**Extended Regex (damit geht dann auch z.B. `+`)**

```
sed -E 's/a+/b'
```

**📌 NOTE**\
Mit extended Regex kann man mit `#` matchen, außerdem dürfen/müssen runde Klammern nicht mehr escaped werden!

**inline edit**

```
sed -i '1,5d' # ersten 5 Zeilen löschen
sed -i.bak '1,5d' # wie oben, aber Sicherung in.bak
```

**Ersetzen**

```
sed 's/alt/neu/' # ersetzen (non-greedy!)
sed 's/alt/neu/g' # ersetzen (greedy)
sed 's/alt/neu/3' # nur das 3. Vorkommen ersetzen
sed '1,3s/alt/neu/' # nur in den ersten 3 Zeilen ersetzen
sed -n 's/alt/neu/2p' # nur die Zeile mit Ersetzung ausgeben
```

**Backreferences**

```
# ohne EREs müssen die runden Klammern escaped werden:
journalctl -S today | grep -i 'pickup.*from=.*@dengg-med.at' | sed 's/.*pickup.*: \(.*\): uid=.*/\1/'

# oder mit EREs:
journalctl -S today | grep -Ei 'pickup.*from=.*@dengg-med.at' | sed -E 's/.*pickup.*: (.*): uid=.*/\1/'

# ein '&' steht für den kompletten gematchten Bereich:
sed 's/test/"&"/g'
```


**Mehrere Commands**

`sed -e 's/and/\&/' -e 's/blah/blubb/'`

oder

`sed 's/and/\&/;s/blah/blubb/'`

## Snippets

```
sed -i 's/[ \t]*$//' filename # trailing whitespace entfernen
sed '4 r /etc/hosts' # liest die angegebene Datei ab Zeile 4 ein
sed -n '/suchstring/l' # gibt inkl. Sonderzeichen aus
sed 'y/abc/ABC/' # ersetzt a mit A, b mit B, c mit C (ähnlich aber flexibler als tr)
```
