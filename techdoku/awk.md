# awk

* **Wie grep**\
`awk '/^UUID/' /etc/fstab`
* **Nur erstes Feld ausgeben**\
`awk '/UUID/ {print $1}' /etc/fstab`
* **Nur eine Spalte**\
`awk -F: '{print $1}' /etc/passwd`
* **Interne Variablen**
  * FILENAME
  * FS (field separator; def. = whitespace)
  * OFS (output field separator)
  * RS (record separator; def. = newline)
  * ORS (output record separator; def. = newline)
  * NR (number of current record)
  * NF (number of fields = letzes Feld)
* **BEGIN und END blocks**\
`awk 'BEGIN {action; } /search/ { action; } END {action; }' input file`
* **Beispiele**
```
awk 'BEGIN {FS=":"; OFS=";"} {print $1,$6}' /etc/passwd
awk -F: -v OFS=";" '{print $1,$6}' /etc/passwd # Alternative f. FS/OFS
awk -F "/" '/^\// {print $NF}' /etc/shells
```

* **Komplizierteres Beispiel**
```
awk 'BEGIN { FS=":"; print "User\t\tUID\t\tGID\t\tHome\t\tShell\n--------------"; }
{print $1,"\t\t",$3,"\t\t",$4,"\t\t",$6,"\t\t",$7;}
END { print "---------\nFile Complete" }' /etc/passwd
```

* **Match auf ein Feld**
```
awk '$2 ~ /^sa/' test.txt
```
**📌 NOTE**\
Das "^" bezieht sich damit auf den Beginn eines Feldes!

* **Komplexerer Match**
```
awk '$2 !~ /^sa/ && $1 < 5' favorite_food.txt
```

* **Beispiel mit Loop, RegEx und POSIX character class**
```
#!/usr/bin/env bash

while read line; do
    # Kommentarzeilen und leere Zeilen ignorieren
    { echo "$line" | tr -d '\r' | grep -E '^#|^ *$' ; } > /dev/null && continue

    # Ausgabe wenn Mailadresse nicht leer
    echo "$line" | awk -F, '{ if ($4 !~ /^[[:blank:]]*$/) print $2 "," $3 "," $4; }'
done
```

* **IPs sortieren**
```
ip a show dev eth1 | awk '/inet/ {print $2}' | sort -t . -k 1,1n -k 2,2n -k 3,3n -k4,4n
```

* **`awk` kann rechnen**
```
df  | awk -v OFS="!" '/^tmpfs/ {print $1,$3,$4,"Differenz: ",$3 - $4}'
ps auxw | awk '$1 = "root" && $2 < 1024'
awk 'BEGIN { for (i=1; i<=10; i++) print "i Quadrat von", i, "ist", i*i;}'
```

* **Filter auf Länge eines Feldes**\
`awk 'length($0) < 8' /etc/shells`
* **Nur Zeilen von bis ausgeben**\
`awk 'NR==2, NR==5 {print NR, $0}' /etc/passwd`
