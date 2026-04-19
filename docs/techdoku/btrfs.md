# btrfs

## Root auf Snapshot zurücksetzen (mit Archlinux)

* von USB booten
* `loadkeys de-latin1`

**Bei einem verschlüsselten System:**

`cryptsetup open --type luks /dev/sda1 cryptroot`

* btrfs-root mounten

  ```
  lsblk
  mount /dev/sda1 /mnt
  cd /mnt
  ```

  _oder ggf._ `/dev/mapper/cryptroot`
* passenden Snapshot herausfinden

  ```
  btrfs subvol list /mnt
  ```

  _ggf._ `info.xml` im jeweiligen Snapshot-Verzeichnis untersuchen
* defektes Subvolume umbenennen

  `mv /mnt/@ /mnt/@-defekt`
* Snapshot als neues `@` definieren

  `btrfs subvol snapshot /mnt/@snapshots-root/1/snapshot /mnt/@`
* `cd / && umount /mnt && reboot`

## Subvolume auf andere Platte verschieben

**🔥 CAUTION**\
Subvolumes können "nested" sein -> mit `btrfs subvolume list /` prüfen, ob dies zutrifft. Falls ja muss die nachfolgende Prozedur ggf. entsprechend oft für jedes einzelne Subvolume wiederholt werden!

```
pimtest:~ # btrfs subvol list / | grep gromox
ID 275 gen 209026 top level 256 path @/var/lib/gromox
```

Im nachfolgenden Beispiel migrieren wir `/var/lib/gromox` (ohne nested subvolumes) auf eine neue Platte.

### Neue Platte im Hypervisor dazuhängen, partitionieren, formatieren:

**🔥 CAUTION**\
"/dev/sdX" durch korrekten Datenträger anpassen. Bei falscher Angabe des Ziel-Volumes wird hier die komplette Platte gelöscht - VORSICHT!

    lsblk # Neue Platte identifizieren -> z.B. /dev/sdX
    sudo sgdisk -og -n 1:0:0 -c 1:"Mail" -t 1:8300 /dev/sdX   # Label mit "-c" angeben (optional), "8300" = Linux Filesystem
    sudo mkfs.btrfs -L Mail /dev/sdX1

### Quell-Snapshot erstellen & übertragen

* Wir verwenden `btrfs send` zum Übertragen des gesamten Filesystems. Dies ist nur mit einem readonly-Snapshot als Quelle möglich. Um Dateninkonsistenzen zu vermeiden, stoppen wir deshalb vorab alle relevanten Dienste.
* Eine Alternative mit weniger Downtime wäre ggf. mit `rsync` oder reflinks (nicht getestet)

    mount /dev/sdX1 /mnt # neue Platte temporär einhängen
    sudo systemctl stop gromox-midb gromox-imap gromox-pop3 gromox-zcore grommunio-admin-api gromox-delivery gromox-delivery-queue gromox-http.service nginx # alle relevanten Dienste stoppen
    sudo btrfs subvol snapshot -r /var/lib/gromox /var/lib/gromox-ro
    sudo btrfs send /var/lib/gromox-ro | sudo btrfs receive /mnt
    sudo btrfs subvol list /mnt # check
    sudo btrfs subvol snapshot /mnt/gromox-ro /mnt/@gromox # Neuen Snapshot vom Ziel mit "rw" erstellen
    sudo btrfs subvol delete /mnt/gromox-ro
    sudo btrfs subvol list /mnt # check again
    sudo umount /var/lib/gromox
    sudo vi /etc/fstab # auf neues Subvolume auf neuer Platte verweisen, s.u.
    sudo mount /var/lib/gromox
    sudo systemctl start gromox-midb gromox-imap gromox-pop3 gromox-zcore grommunio-admin-api gromox-delivery gromox-delivery-queue gromox-http.service nginx # alle relevanten Dienste starten

Anpassen von `/etc/fstab`:

Mit `lsblk` die UUID der neuen Partition ermitteln. Dann:

Ersetzen von

    UUID=3951890d-f006-43a3-bc3c-6b89c715050f /var/lib/gromox btrfs defaults,subvol=@/var/lib/gromox 0 0

durch

    UUID=d41df301-c7cc-4e04-b5c4-f5510c6c03a6 /var/lib/gromox btrfs defaults,subvol=@gromox 0 0

**Nach Tests bereinigen altes Subvolume löschen**

    sudo mount /dev/sdALT /mnt -o subvolid=5 # top-level suvol mounten
    btrfs subvol list /mnt/old
    btrfs subvol delete /mnt/old/\@/var/lib/gromox/
