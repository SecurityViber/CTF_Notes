---
tags: [basics, technique, windows, encryption]
---



## Mounting Bitlocker 

```bash 
sudo apt-get install dislocker 

sudo mkdir -p /media/bitlocker
sudo mkdir -p /media/bitlockermount 

sudo losetup -f -P Backup.vhd
# Decrypt the drive using -upassword
sudo dislocker /dev/loop0p2 -u1234qwer -- /media/bitlocker 
sudo mount -o loop /media/bitlocker/dislocker-file /media/bitlockermount 

cd /media/bitlockermount

sudo umount /media/bitlockermount
sudo umount /media/bitlocker

```