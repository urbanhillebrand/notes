# Sonstiges

## Partitionen aus Disk image bearbeiten

```
sudo kpartx -av <pfad_zu_image> # -> add map loop1p1 (254:1): 0 3919872 linear 7:1 2048
sudo mount /dev/mapper/loop1p1 /mnt # -> bearbeiten
sudo umount /mnt
sudo kpartx -d <pfad_zu_image>
```

**Alternativ mit fdisk:**

`fdisk -l <pfad_zu_image>`

-> Startpunkt finden: sector size * Start der gewünschten Partition
z.B. 512 * 2048 = 1048576

`sudo mount -o loop,offset=1048576 <pfad_zu_image> <mountpoint>`

## Icinga
* **Ticket generieren**\
`root@slmonp01:/etc/icinga2/zones.d/hsj# icinga2 pki ticket --cn slsvcp01.hsj.local`

## Keepass crasht bei Windows-Button

* **/usr/bin/keepass editieren**\
`exec mono --verify-all /usr/share/keepass/KeePass.exe "$@"`

## pdftk (PDFs manipulieren)

```
# Seiten 5-10 und 12 zu neuem PDF hinzufügen
pdftk datei.pdf cat 5-10 12 output datei_neu.pdf

# mehrere PDFs zu einem zusammenfügen
pdftk datei1.pdf datei2.pdf datei3.pdf cat output datei123.pdf

# Seiten pro PDF selektiv
pdftk A=datei1.pdf B=datei2.pdf cat A1-7 B1-5 A8 output zusammen.pdf 

# In Einzelseiten zerlegen
pdftk datei.pdf burst
# oder besser...
pdftk datei.pdf burst output ~/pfad/zu/neuem/Verzeichnis

# Seiten drehen (nach "east" = Uhrzeigersinn)
pdftk datei.pdf cat 1-endeast output datei90.pdf

# nur ausgewählte Seiten drehen
pdftk datei.pdf cat 1-6 7-10south 11-end output dateineu.pdf

# Metadaten ausgeben
pdftk datei.pdf dump_data output info.txt
```
