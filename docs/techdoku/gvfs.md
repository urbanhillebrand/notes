# gvfs

**Snippts**

```
pacman -Ss ^gvfs
sudo pacman -S gvfs gvfs-smb

gio mount smb://uh@swadsp01.ci.local/daten
gio mount -u smb://uh@swadsp01.ci.local/daten

gio mount sftp://slerpp02.l1.s1.cbx.c
gio mount davs://cloud.cibex.net/remote.php/dav/files/68FCF44E-8B67-420F-9751-B0BED868375C # benötigt App-Token zur Auth.!
```

**Diverses**

* Yazi-Plugin: https://github.com/boydaihungst/gvfs.yazi
* Nach Abspeichern von Thunar werden gespeicherte Credentials auch an der Commandline verwendet
