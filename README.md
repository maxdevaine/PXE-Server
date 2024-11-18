# PXE Server

Example of PXE Server to automatically deploy clonezilla images via network with Secure Boot support

Boot steps are

UEFI SB Enabled -> PXE ipv4 boot -> Shim -> Grub -> CloneZilla kernel

## Deploy/Configure PXE Server
1) create image directory and symlinks
```
mkdir /mnt/datastore1/PXE-Server/images
ln -s /mnt/datastore1/PXE-Server/images /mnt/datastore1/PXE-Server/EFI/x64/images
ln -s /mnt/datastore1/PXE-Server/images /mnt/datastore1/PXE-Server/EFI/ia32/images
ln -s /mnt/datastore1/PXE-Server/images /mnt/datastore1/PXE-Server/BIOS/images
```

2) download Clonezilla x64 zip file

https://clonezilla.org/downloads/download.php?branch=stable

3) unzip Clonezilla
```
mkdir /mnt/datastore1/PXE-Server/images/CloneZilla
cd /mnt/datastore1/PXE-Server/images/CloneZilla
unzip ~/Downloads/clonezilla-live-3.2.0-5-amd64.zip
```

4) setup tftpd server with root directory set to
```
/mnt/datastore1/PXE-Server
```

5) setup dhcp server to boot this file
```
/shimx64.efi
```

# Sources
all binary files are from Clonezilla live and:

https://mirrors.edge.kernel.org/pub/linux/utils/boot/syslinux/Testing/6.04/syslinux-6.04-pre1.zip

shimx64.efi is bootx64.efi from Clonezilla (CloneZilla/EFI/boot/bootx64.efi)

## other files

ipxe.efi - just for test boot via http, there is no settings about it at this time

wimboot - just for test booting win image directly, there is no settings about it at this time

# Configuration

All configs are in the /mnt/datastore1/PXE-Server/grub/grub.cfg

# Workarounds

This eliminate problems with Thinkpad X13 and LTE modem. Clonezilla tries LTE interface instead of eth interface from time to time
```
live-netdev="eth0"
```

Force restart PC after is clonning done. Sometimes Clonezilla freezes in restarting process on Thinkpad X13 and other devices. So, we will wait 20s (to be sure that all io writes are done) and then we will run sysrq to force restart.
```
ocs_postrun1="sleep 20" ocs_postrun2="echo b > /proc/sysrq-trigger"
```
