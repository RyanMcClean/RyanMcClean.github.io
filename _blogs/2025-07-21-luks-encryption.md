---
layout: post
title:  "First Post, LUKS Encryption"
date:   2025-07-21 19:00:00 +0100
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


