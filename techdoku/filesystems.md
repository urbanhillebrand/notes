# filesystems

## Snippets

* **Verbrauchte inodes anzeigen**
```
sudo find . -xdev -type f | cut -d "/" -f 2 | sort | uniq -c | sort -n
```

## Reservierter Platz

**📌 NOTE**\
`ext4` reserviert 5% für Dienste, die als root laufen (das ist bei großen Platten schnell unpassend viel!)

**Ändern (auf 1%)**

    sudo tune2fs -m 1 /dev/mapper/<name>
