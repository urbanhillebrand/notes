# proxmox

Url: https://node:8006

**Shell-Commands**

```
qm stop 123
qm start 123
qm config 123

# tagged VLANs mitgeben (geht nicht über GUI):
qm set 123 -net1 "virtio=CA:85:82:35:28:62,bridge=vmbr0,trunks=10-20;30"
```

## resize

siehe https://pve.proxmox.com/wiki/Resize_disks

```
qm config 998
qm resize 998 scsi2 +300G

lsblk
fdisk -l /dev/sdX
parted /dev/sdX
print

# -> "Not all of the space available appears to be used..." -> "F" für "Fix"
resizepart 1 100%
print

# btrfs
btrfs filesystem resize max /path
```

## VM umbenennen

* PVE-Host auswählen (nicht VM)
* Shell öffnen
* `cd /etc/pve/qemu-server`
* .conf-File der VM editieren + Namen ändern

## Snapshot hangs

```
qm unlock 123
qm listsnapshot 123
qm delsnapshot 158 <snapshotname> # evt. mit -f (force)
```

**🔥 CAUTION**\
VM danach häufig beleidigt!

## SCSI-Disk identifizieren

in VM: `lsblk -o +SERIAL`

## post-install

### Repos

**`/etc/apt/sources.list`**

```
deb http://ftp.debian.org/debian bullseye main contrib
deb http://ftp.debian.org/debian bullseye-updates main contrib
deb http://security.debian.org/debian-security bullseye-security main contrib
deb http://download.proxmox.com/debian/pve bullseye pve-no-subscription
```

Danach in `/etc/apt/sources.list.d/pve-enterprise.list` das Enterprise-Repo auskommentieren.

`apt update && apt dist-upgrade`

### ZFS Pool(s) erstellen

* Disks unter pve-Host - Disks prüfen -> falls die relevanten Disks nicht `none` als "Usage" haben, via ssh Partitionen mit fdisk löschen
* danach unter Disks - ZFS den Pool erstellen

## VLANS aktivieren

`pve Host - Network - Bridge - VLAN aware` aktivieren

Will man den pve-Host nur für spezielle VLANs aktivieren, kann man das in `/etc/network/interfaces` tun (z.B. `bridge-vids 123 234 345`, oder `bridge-vids 2-4096`)

## Cluster

* Für einen Cluster-Join dürften keine VMs installiert sein!
* Netzwerkkonfiguration vorher finalisieren!
-- 
* am ersten Server "create cluster", dann die "Join information" kopieren
* am 2. Server "join cluster" mit angeführtem Link
