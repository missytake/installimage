
this directory contains all the scripts for installimage.

## Known Issues

LUKS disk encryption only works with Debian & Ubuntu.

LUKS disk encryption only works with RSA SSH keys.

cryptroot-unlock doesn't work with Debian 9.

# Usage

I wanted to started with the `installimage` command (documentation:
https://wiki.hetzner.de/index.php/Installimage/en), but it didn't support LUKS.
So I cloned a fork of the script I found on GitHub, which I forked again:

```
cd /tmp
git clone https://github.com/missytake/installimage
cd installimage
# I had to change the IMAGESPATH to "/root/.oldroot/nfs/images/" in config.sh
# I changed the OLDIMAGESPATH to "/root/.oldroot/nfs/images.old/" in config.sh
/tmp/install-luks/installimage
```

The options I chose in the Text User Interface:

```
Debian
Debian-103-buster-64-minimal
```

And the options I chose in the choosing screen:

```
DRIVE1 /dev/sda
DRIVE2 /dev/sdb
SWRAID 1
SWRAIDLEVEL 1
HOSTNAME ezra
LUKS_PASSWORD s3cr3t
FDE_SSH_UNLOCK 1
PART /boot ext3 1G
PART lvm+luks   vg0      all
LV vg0   swap   swap     swap          6G
LV vg0   root   /        ext4         all
```

I saved & exitted with F10, confirmed to write the system to /dev/sda and
/dev/sdb, and waited for the installation to complete.

### First login

I rebooted, to login to the dropbear SSH server and unlock the disk.

```
user@xubuntu-1804$ ssh ezra -p 54321
The authenticity of host '[78.46.128.202]:54321 ([78.46.128.202]:54321)' can't be established.
ECDSA key fingerprint is SHA256:1fxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxySU.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '[78.46.128.202]:54321' (ECDSA) to the list of known hosts.


BusyBox v1.30.1 (Debian 1:1.30.1-4) built-in shell (ash)
Enter 'help' for a list of built-in commands.

~ # cryptroot-unlock 
Please unlock disk crypt_dev_md_1: 
cryptsetup: crypt_dev_md_1 set up successfully
~ # Connection to 78.46.128.202 closed by remote host.
Connection to 78.46.128.202 closed.
```

A while after that, I could SSH successfully into the new Debian 10 system on Ezra.

