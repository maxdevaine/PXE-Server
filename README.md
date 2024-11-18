# PXE Server

Example of PXE Server to automaticaly deploy clonezilla images via network.

Boot steps is

UEFI SB Enabled -> PXE ipv4 boot -> Shim -> Grub -> CloneZilla kernel

## Steps
1) create image directory and symlinks
```
mkdir /srv/PXE-Server/images
ln -s /srv/PXE-Server/images /srv/PXE-Server/EFI/x64/images
ln -s /srv/PXE-Server/images /srv/PXE-Server/EFI/ia32/images
ln -s /srv/PXE-Server/images /srv/PXE-Server/BIOS/images
```

2) download Clonezilla x64 zip file

https://clonezilla.org/downloads/download.php?branch=stable

3) unzip Clonezilla
```
mkdir /srv/PXE-Server/images/CloneZilla
cd /srv/PXE-Server/images/CloneZilla
unzip ~/Downloads/clonezilla-live-3.2.0-5-amd64.zip
```

4) setup tftpd server with root directory set to
```
/srv/PXE-Server
```

5) setup dhcp server to boot this file
```
/shimx64.efi
```

# Sources
all binary files are from Clonezilla live and:

https://mirrors.edge.kernel.org/pub/linux/utils/boot/syslinux/Testing/6.04/syslinux-6.04-pre1.zip

shimx64.efi is bootx64.efi from Clonezilla (CloneZilla/EFI/boot/bootx64.efi)

# other files

ipxe.efi - just for test boot via http, there is no settings about it at this time

wimboot - just for test booting win image directly, there is no settings about it at this time

# Configuration

All configs are in the /srv/PXE-Server/grub/grub.cfg

# Workarounds

1) live-netdev="eth0"

This eliminate problems with Thinkpad X13 and LTE modem. Clonezilla tries LTE interface instead of eth interface from time to time

2) ocs_postrun1="sleep 20" ocs_postrun2="echo b > /proc/sysrq-trigger"

Force restart PC after clonning done. Sometimes Clonezilla freeze in restarting process on Thinkpad X13 and other devices. So, we will wait 20s (to be sure that all io writes are done) and then we will run sysrq to force restart.

