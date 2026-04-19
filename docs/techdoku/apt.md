# apt

## Geänderte Files zu Orginal in Paketquellen suchen

```
cd tmp/dir
apt-get download <Paketname>

# oder sicherstellen, dass Version gleich der installierten ist:

apt-get download $(dpkg-query -W -f='${binary:Package}=${Version}' <Paketname>)

# auspacken
dpkg-deb -x <Paketname>.deb .

# Alternative (wenn man auch die control-Files etc. will):
ar vx <Paketame>.deb
tar xzf data.tar.gz

# Vergleichen
find etc -type f |
while read file; do
    diff $file /$file
done
```

**📌 NOTE**\
Bei sehr alten Versionen von apt gibt es kein "download" -> manueller Download von http://archive.ubuntu.com/ubuntu/pool/main/ o.ä.
