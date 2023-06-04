---
title: "Luks on Raspberry Pi"
date: 2022-09-03 17:00:00 +0200
tags: [luks, raspberry pi]
---

[A]:https://www.willhaley.com/blog/encrypted-file-container-disk-image-in-linux/
[B]:https://rr-developer.github.io/LUKS-on-Raspberry-Pi/

**Based on [this][A] and [that][B] but very lazy**

The idea is to encrypt the home directory of an existing and running raspberry pi, without too much hassle.

## A dirty recipe

__Disclaimer__: this is an incomplete but working setup as long as you have the patience it takes to remount the encrypted disk manually at each boot.
For me this is OK for a starting point since I don't reboot the berry that often. A cleaner solution will follow at some point.

First the image file
```bash
sudo mkdir -p /crypt/bawey
sudo chown -R bawey /crypt/bawey/
cd /crypt/bawey
dd if=/dev/zero of=hoome.img bs=1 count=0 seek=30G
```

Then encrypt the image:
```bash
# available ciphers can be tested like so: (can use aes-xts for comparison)
sudo cryptsetup benchmark --cipher xchacha20,aes-adiantum-plain64
# will ask for a passphrase
sudo cryptsetup luksFormat home.img --cipher xchacha20,aes-adiantum-plain64
```

Next, unlock the image, create a filesystem and "move in"
```bash
sudo cryptsetup luksOpen home.img crypthome
sudo mkfs.ext4 /dev/mapper/crypthome
sudo chown -R bawey /mnt
rsync -apr /home/bawey /mnt/
```

Check the status like so:
```bash
sudo cryptsetup status crypthome
```

The unmounting, if need be:
```bash
sudo umount /home/bawey
sudo cryptsetup luksClose crypthome
```