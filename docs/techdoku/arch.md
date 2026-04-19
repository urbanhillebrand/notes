# arch

## LVM innerhalb von LUKS von archiso einbinden

```
loadkeys de-latin1
cryptsetup open /dev/nvme0n1p2 cryptroot
vgscan -v
vgchange -a y myvg # oder entsprechenden VG-Namen
lvs
mount /dev/mapper/... /mnt
```

## UEFI Booteintrag neu erstellen

```
blkid # Partition mit Type "crypto_LUKS" identifizieren, und UUID unten einsetzen

# ggf. anpassen:
# - /dev/nvme0n1 --part 1 = UEFI /boot
# - UUID s.o.
# - Name der VG ("myvg")


efibootmgr --disk /dev/nvme0n1 --part 1 --create \
--label "Arch Linux" --loader /vmlinuz-linux \
--unicode 'cryptdevice=UUID=765dc878-4503-453b-a77e-12f4e2ec2204:cryptlvm \
root=/dev/myvg/root resume=/dev/myvg/swap rw \
initrd=\intel-ucode.img initrd=\initramfs-linux.img' --verbose
```

## Installationsdoku T470

```
# Inst.medium erstellen, und mit deaktiviertem Secure Boot booten
loadkeys de-latin1
ls /sys/firmware/efi/efivars # EFI da?
ping heise.de # connectivity?
timedatectl
fdisk -l

fdisk /dev/nvme0n1
# -> Partition Typ "EFI Boot" mit 1GB erstellen
# -> Partition Typ "Linux Filesystem" mit Rest erstellen (f. LUKS, darin dann LVM)
mkfs.fat -F32 /dev/nvme0n1p1
cryptsetup luksFormat /dev/nvme0n1p2
cryptsetup open /dev/nvme0n1p2 cryptlvm
ls -l /dev/mapper

pvcreate /dev/mapper/cryptlvm
vgcreate myvg /dev/mapper/cryptlvm

lvcreate -L 32G myvg -n swap
lvcreate -L 200G myvg -n root
lvcreate -L 200G myvg -n home

vim /etc/mkinitcpio.conf
# -> unter HOOKS folgende verifizieren/ergänzen: keyboard keymap encrypt lvm2
# REIHENFOLGE?? encrypt lvm2 vor filesystem??

mkswap /dev/mapper/myvg-swap
swapon /dev/mapper/myvg-swap
mkfs.ext4 /dev/mapper/myvg-root
mkfs.ext4 /dev/mapper/myvg-home

mount /dev/mapper/myvg-root /mnt
mount --mkdir /dev/mapper/myvg-home /mnt/home
mount --mkdir /dev/nvme0n1p1 /mnt/boot

pacstrap -K /mnt base linux linux-firmware vim lvm2 efibootmgr intel-ucode
genfstab -U /mnt >> /mnt/etc/fstab
arch-chroot /mnt
ln -sf /usr/share/zoneinfo/Europe/Vienna /etc/localtime
hwclock --systohc
vim /etc/locale.gen # en_US.UTF-8 UTF-8 aktivieren
locale-gen

echo -n 'LANG=en_US.UTF-8' >/etc/locale.conf
echo -n 'KEYMAP=de-latin1' >/etc/vconsole.conf
echo -n 'lap-t470' >/etc/hostname

vim /etc/mkinitcpio.conf
# -> unter HOOKS folgende verifizieren/ergänzen: keyboard keymap encrypt lvm2
# REIHENFOLGE? encrypt lvm2 vor filesystem?
mkinitcpio -P # generate initramfs

passwd # set root pw
blkid # UUID der Partition mit Type "crypto_LUKS" herausfinden und unten einsetzen - ggf. auch vg-Namen anpassen!

efibootmgr --disk /dev/nvme0n1 --part 1 --create \
--label "Arch Linux" --loader /vmlinuz-linux \
--unicode 'cryptdevice=UUID=765dc878-4503-453b-a77e-12f4e2ec2204:cryptlvm \
root=/dev/myvg/root resume=/dev/myvg/swap rw \
initrd=\intel-ucode.img initrd=\initramfs-linux.img' --verbose

```
