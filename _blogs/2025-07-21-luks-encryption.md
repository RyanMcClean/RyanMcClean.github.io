---
layout: post
title:  "luks_encryption"
date:   2025-07-21 12:00:00 +0100
categories: Blog LUKS
tags: LUKS Encryption Debian 
---

# LUKS Drive Encryption

## The start

The journey into LUKS this time started for me cause I'd fucked my drive partitions.
I had set up Debian 12 with a separate root and home. I had quickly filled the root partition.
So I tried to expand it, used lvresize to shrink the home partition and grow my root partition.
When I used resize2fs on my root, it worked fine, but it fell down when I tried it on my home partition.
Tried to fix it failed. So I reinstalled Debian 12, but I only used 50% of my drive this time.
Then I could size my root partition up to 100GB, and finally just grow my /home partition to take up the rest of the drive.

## Encryption

While doing all that I figured I'd give encrypting the other drives on my system a go. How hard could it be.
Turns out not very hard. I copied my data off, ran `cryptsetup luksFormat /{path to drive}` and it worked pretty much straight out of the gate.
I just opened the encrypted drive, make the ext4 file system inside it (`mkfs.ext4`), and then updated my `/etc/fstab` and `/etc/crypttab` files.

I opted to use `decrypt_keyctl` to cache my LUKS password and have it open all my drives with one password (I obviously set all encrypted drives to have the same password).
This was as simple as setting `keyscript` to be equal to `decrypt_keyctl` as one of the options in `/etc/crypttab`.

## The Escalation

This was all well and good, but then I decided to encrypt a USB drive I regularly use (It honestly it's never not plugged in).
Encryption was easy, same process as above. However, decryption was something else.
What I wanted was the drive to be decrypted and mount at boot if it's plugged in.
But also, to be automatically decrypted and mounted if it it plugged in while the system was running.

<details>
<summary>
#### Quick backstory
</summary>

I have recently moved to linux from windows, I have used linux a lot, professionally and as a hobby (Rasberry Pi's and such). Linux requires a lot more tinkering than windows, which I prefer, I enjoy the process. Part of this process has involved me fiddling with `udev` rules.
I wanted to get my nintendo switch pro controller to work with my debian install. I got it working in the end, but that leads us back to the main story.

#### End backstory
</details>


I was aware that `udev` rules can be used to perform actions when devices connect to the computer. So I was pretty sure that I could use it to handle hot-plugging the usb drive. This got quite intense, at one point I had `journalctl -xef` running on one screen, `udevadm monitor` on another, then `watch lsblk` (to see if the drive got mounted) and my text editor on the other. My brain started to hurt after a while.

What I finally ended up with was this rule:

```text
# Rules to decrypt and mount LUKS USB drive
ACTION=="add",KERNEL=="sd[a-z][1-9]",SUBSYSTEM=="block",ATTRS{model}=="SSD 970 EVO Plus",ATTRS{vendor}=="Samsung ",RUN+="/home/ryan_mcc/Documents/LocalCode/decrypt.sh $kernel"ENV{SYSTEMD_WANTS}="automount.service"
```

I spent a lot of time trying to script the decryption and the mounting of the drive. I hadn't realised at the time that the udev service had the `PrivateMounts=yes` option in it. This prevented the mounting from ever being successful. It turned out that the workaround was to call up another service from the udev rule which wouldn't be limited by this service option and would therefore be able to mount the drive. This also had the unintentional effect of timing the mounting of the drive to be after the decryption. It also means that should I have other drives that I want to make hot-puggable then I can just call the same service.

This is the service file that I created:

```text
[Unit]
Description=Auto decypt USB drive and mount

[Service] 
Type=oneshot
ExecStart=/usr/bin/mount -a
```

This relies on the proper setup of my `/etc/fstab` file. 

With all of this done my usb drive is now encrypted and when I plug it into my computer it is decrypted and mounted. Success. 

## Key commands

`udevadm`
`journalctl`
`cryptsetup`
`mkfs.ext4`
`lsblk`
`mount`
