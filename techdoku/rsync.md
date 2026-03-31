# rsync

**Klonen mit progress bar**

```
sudo rsync -ahxAHXWS --numeric-ids --info=progress2 /opt/odoo/ /mnt/odoo
```

**Optionen:**

* -a is the archive mode: it recurses into directories, copies symlinks as symlinks, and preserves permissions, owner, group, modification times, device files, and special files.
* -h output numbers in a human-readable format
* -x doesn’t cross filesystem boundaries.
* -A preserves ACLs.
* -H preserves hard links.
* -X updates the destination extended attributes to be the same as the source ones.
* -W disables the delta-transfer algorithm used to reduce network usage. It’s a convenient way to boost speed when both the source and destination are local paths.
* -S tries to handle sparse files efficiently so that they take up less space on the destination.
* –numeric-ids uses numeric IDs instead of trying to map them. It’s notably needed for backups of jailed systems (BSD jails, OpenVZ, VServer, LXC) that appear to have bogus IDs when seen from their host system because they have their own ID maps.
* –info=progress2 outputs statistics based on the whole transfer, rather than individual files.

**Verlässlicher progress bar**

```
rsync --info=progress2 --no-inc-recursive # oder --no-i-r
```

**🔥 CAUTION**\
`no-i-r` liest vorher alle Files ein, also längere Laufzeit und mehr Speicherverbrauch!
