#  Hetzner Encryption at Rest with Dropbear

Based on recent full disk encryption support (CRYPT setting) added in https://github.com/hetzneronline/installimage/commit/5589fd2a2eda5a4cae87bf63707e641d6a09e5b6

Effort to help solve https://github.com/hetzneronline/installimage/issues/37 with additions from https://github.com/hetzneronline/installimage/pull/21 to provide remote SSH unlock of encryption using Dropbear.

* For Hetzner Root servers.
* Ubuntu, Debian supported.

## Quickstart for Ubuntu 20.04

Set a public SSH key in Key Management area for your Rescue system before you begin. This is the key Dropbear will use to authenticate you.

#### SSH into server.
```bash
git clone --single-branch --branch crypt_dropbear https://github.com/gnat/installimage.git
alias installimage='/root/installimage/installimage'
nano autosetup
```

#### Content of autosetup.
```bash
# Encryption.
CRYPT 1
CRYPTPASSWORD test-password
CRYPTDROPBEAR 1
# Optionally specify CRYPTDROPBEARPORT
# Drives.
DRIVE1 /dev/nvme0n1
DRIVE2 /dev/nvme1n1
# RAID mirror.
SWRAID 1
SWRAIDLEVEL 1
# Other.
BOOTLOADER grub
HOSTNAME test-host
# Partitions.
PART /boot ext3 2G
PART lvm   vg0  all crypt
# Logical Volumes.
LV vg0 tmp  /tmp      ext4  16G
LV vg0 log  /var/log  ext4  16G
LV vg0 swap swap      swap  32G
LV vg0 root /         ext4  all
# Install image.
IMAGE /root/.oldroot/nfs/install/../images/Ubuntu-2004-focal-64-minimal.tar.gz
```

#### Begin installation.
```bash
installimage -c /root/autosetup -f yes -a
```

#### After reboot
SSH in and use one of the following to unlock your disk.
* `echo -ne "password"> /lib/cryptsetup/passfifo`
* `cryptroot-unlock`

This contribution is public domain.
