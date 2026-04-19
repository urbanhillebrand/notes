# mount

## Partition aus iso (oder dd-File) mounten
```
kpartx -l <imagename>   # anzeigen der enthaltenen Partitionen
kpartx -a <imagename>   # enthaltene Partitionen nach /dev/mapper mounten
sudo mount -o loop /dev/mapper/<devicename> /mnt
```
