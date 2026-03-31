# KVM / QEMU

## Ideensammlung

### LVM / Backup
* für saubere Backups mit LVM Snapshots "cache=none" setzen
* 1 LVM logic volume pro Maschine?
* 1 LVM VG für alle Images?
* eigene LVM VG für QEMU (wird beim Erstellen formatiert)
* Backup-Strategie: LVM snapshot + backup + snapshot löschen
* Snapshot-Backup: entweder mount & filesystem-backup, oder dd des lv
* libvirt-Support in Veeam?
* LVM auf iSCSI LUNs: http://benpiper.com/2014/12/creating-linux-lvm-logical-volume-iscsi-san/
* Images in /var/lib/libvirt/images wg. AppArmor in Ubuntu (https://wiki.ubuntuusers.de/virsh)

### Networking
* Ein Bridge-Interface für jedes VLAN?
