## Arch Installation Cheatsheet ##

### Keyboard layout ###

```
loadkeys {us,dvorak}
```


### Wifi ###

```
netctl  
wifi-menu
```


### Partitioning ###

#### Suggested partitions (dual boot, single root) ####

EFI Partition (200-400mb) [ef00 EFI System]  
Mircosoft Reserved (16mb?) [0c01 Microsoft reserved]  
Windows Filesystem [0700 Microsoft basic data]  
Swap Partition (1gb+) [Linux Swap]  
Linux Filesystem [Linux Filesystem]


#### Tools ####

```
fdisk  
gdisk  
cfdisk  
cgdisk  
sfdisk  
sgdisk  
parted
```


### Formatting ###

```
mkfs.fat -F32 /dev/sdxY  
mkswap /dev/sdxY  
mkfs.ext4 /dev/sdxY
```

### Mounting ###

```
mount /dev/sdxY /mnt
```

### Mirrorlist ###

```
vim /etc/pacman.d/mirrorlist
```

### Installing base ###

```
pacstrap /mnt base base-devel
```

### Generate fstab ###

```
genfstab -U /mnt >> /mnt/etc/fstab
```

### Chroot ###

```
arch-chroot /mnt
```


### Timezone ###


TODO.


### Hostname ###

> /etc/hostname
> ```
> hostname
> ```


### Root password ###

```
passwd
```

### rEFInd ###

```
pacman -Sy refind-efi  
refind-install
mkrlconf
```

>/boot/refind_linux.conf
>```
>"Boot using default options"     "root=PARTUUID=XXXXXXXX rw add_efi_memmap"
>"Boot using fallback initramfs"  "root=PARTUUID=XXXXXXXX rw add_efi_memmap initrd=/boot/initramfs-linux-fallback.img"
>"Boot to terminal"               "root=PARTUUID=XXXXXXXX rw add_efi_memmap systemd.unit=multi-user.target"
>```


### Really old stuff ###

```
#Partitioning (BIOS-GPT)

sgdisk -og -n 1:0:+1m -c 1:”BIOS Boot” -t 1:ef02 -n 2:0:+200m -c 2:”Linux Boot” -t 2:0700 -n 3:0:+32g -c 3:”Linux /” -t 3:8300 -n 4:0:+16g -c 4:”Linux /var” -t 4:8300 -n 5:0:0 -c 5:”/home” -t 5:8300 -p /dev/sda

#Make filesystem
mkfs -t ext4 /dev/sda2
mkfs -t ext4 /dev/sda3
mkfs -t ext4 /dev/sda4
mkfs -t ext4 /dev/sda5

#Mount
mount /dev/sda3 /mnt
mkdir /mnt/boot
mkdir /mnt/var
mkdir /mnt/home
mount /dev/sda2 /mnt/boot
mount /dev/sda4 /mnt/var
mount /dev/sda5 /mnt/home

#Install
pacstrap /mnt base grub

arch-chroot /mnt

#Bootloader
grub-install –target=i386-pc –recheck –debug /dev/sda
grub-mkconfig -o /boot/grub/grub.cfg
```
