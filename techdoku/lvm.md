# lvm

## snippets

```
apt install lvm2
fdisk /dev/sdX # Partionen(en) vom Type 8e erstellen
pvcreate /dev/sdX1 /dev/sdX2
vgcreate ubuntu-vg /dev/sdX1 /dev/sdx2
lvcreate -L 80G -n opt ubuntu-vg
```

```
lvcreate -L 10G -n opt ubuntu-vg # erstellen
lvremove /dev/mapper/ubuntu--vg-opt # entfernen
```

## Filesystem vergrößern

```
lvextend -L 50G /dev/mapper/sldmsp01--vg-opt
```

oder

```
lvextend -L +10G /dev/mapper/sldmsp01--vg-opt
```

oder

```
lvextend -l +100%FREE /dev/mapper/sldmsp01--vg-opt
```

```
e2fsck -f /dev/mapper/sldmsp01--vg-opt
resize2fs -p /dev/mapper/sldmsp01--vg-opt # auf max. Größe vergrößern
```

## logical volume verkleinern

```
umount /dev/ubuntu-vg/test
e2fsck -f /dev/ubuntu-vg/test
resize2fs -p /dev/ubuntu-vg/test 3G # ①
lvreduce -L 4G /dev/ubuntu-vg/test
resize2fs /dev/ubuntu-vg/test # ②
mount /dev/ubuntu-vg/test
```
1. Auf ca. 90% der gewünschten Größe verkleinern (kann **lange** dauern, deswegen `-p` = progress bar)
2. Vergrößert auf die maximal verfügbare Größe

## Weitere Platte in volume group aufnehmen

```
lsblk
fdisk -l /dev/sdX
fdisk /dev/sdX # neue Part. mit Typ "8e Linux LVM" erstellen!
lvmdiskscan
pvcreate /dev/sdXn # pv erstellen
vgextend sldmsp01--vg /dev/sdXn # bestehende vg erweitern
```

**_Falls neue Platte noch nicht erkannt wird:_**

reboot, oder 

```
cd /sys/class/scsi_host
ls -l
echo "- - -" > host0/scan # ggf. für weitere Hosts wiederholen!
```

Reicht im Normalfall bereits, sonst:

```
cd /sys/class/scsi_device
ls -l
echo 1 > 1\:0\:0\:0/device/rescan # ggf. SCSI-ID anpassen & f. weitere wiederholen!
```

## Platte aus volume group entfernen

**📌 NOTE**\
Ggf. muss vorher durch Verkleinerung der logical volumes genug Platz auf den betreffenden physical volumes geschaffen werden.

|     |     |
| --- | --- |
| pvscan | Anzeige der physical volumes und zugehörigen volume groups |
| pvdisplay | es müssen genug freie PEs vorh. sein wie von der zu entfernenden Platte benötigt |
| pvchange -x n /dev/sdaSOURCE | Allokierung neuer PEs auf der zu entfernenden Platte verhindern |
| pvmove -i1 -v /dev/sdaSOURCE | verschiebt die PEs zu beliebigen Platten in der volume group - ODER: |
| pvmove -i1 -v /dev/sdaSOURCE /dev/sdaDEST | verschiebt die PEs zu einer bestimmten Platte in der volume group |
| pvdisplay | sollte nun keine belegten PEs auf der zu verschiebenden Platte mehr anzeigen |
| vgreduce -v vgNAME /dev/sdaSOURCE | physical volume aus volume group entfernen |
| pvscan | Kontrolle |
| pvremove /dev/sdaSOURCE | LVM-Label entfernen |

## LVM-Volume aus Live-CD mounten

```
vgscan -v
vgchange -a y <volume_group_name>
lvs
mount /dev/mapper/... /mnt
```

**Beispiel mit LUKS**

```
blkid | grep crypto # LUKS-Partition herausfinden
cryptsetup luksOpen /dev/sda2 crypthome # letztere Bezeichnung ist der Mapping-Name, beliebig!
vgscan -v
vgchange -a y <volume_group_name>
lvs
mount /dev/mapper/... /mnt
```

## Beispiel mit Snapshot

* **Platz prüfen**\
`sudo vgs`

<dl><dt><strong>⚠️ WARNING</strong></dt><dd>

bei Kernel-Update: Backup von /boot!!

`dd if=/dev/sda1 of=boot_backup.img`
</dd></dl>

* **Snapshot-Volume erstellen**
```
sudo lvcreate --size 5G --snapshot --name snap01 /dev/mapper/ubuntu--vg-root
```

**📌 NOTE**\
Namen des bestehenden Volumes mit "sudo lvdisplay" herausfinden

**⚠️ WARNING**\
Bei ubuntu 12.04 geht /dev/mapper nicht, sondern /dev/vg-name/lv...

Update durchführen...

Wenn ok, Snapshot löschen:

`sudo lvremove /dev/mapper/ubuntu--vg-snap01`

Revert auf Zustand beim Erstellen des Snapshots:
```
sudo lvconvert --merge /dev/mapper/ubuntu--vg-snap01
sudo dd if=/path/to/boot_backup.img of=/dev/sda1
sudo reboot
```

## Backup einer kompletten Partition auf remote-Host

**Snapshot erstellen:**

`sudo lvcreate --snapshot -L 10G -n root-snap /dev/sysvg/root`

**per SSH kopieren:**

`sudo dd if=/dev/sysvg/root-snap bs=1M | ssh backupserver dd of=/pfad/zu/backup.img bs=1M`

**oder unverschlüsselt mit netcat:**

Am Zielserver: `netcat -l -p 5522 >/pfad/zu/backup.img`

Am Quellserver: `sudo dd if=/dev/sysvg/root-snap bs=1M | netcat backupserver 5522`

**Datenübertragung monitoren mit `pv`:**

`sudo dd ... | pv -ptrb | ssh ...`

**Komprimieren on-the-fly:**

```
sudo dd ... | gzip -c | ssh ...backup.img.gz bs=1M
oder am Zielserver:
sudo dd ... | ssh backupserver "gzip -c | dd of=/pfad/zu/backup.img.bz"
```

**Snapshot löschen:**

`sudo lvremove /dev/sysvg/root-snap`
