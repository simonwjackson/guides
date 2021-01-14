---
description: DIY Docker & KVM hypervisor solution with alpine
---

# Alpine Hypervisor

## Prerequisites

```bash
MY_NEW_USER=<username>
```

## Add Admin User

We want to use `root` [as little as possible](https://askubuntu.com/a/16179). So we create a normal user account and give them super powers.

```bash
adduser \
  --ingroup users \
  --uid 1000
  ${MY_NEW_USER}
```

```bash
apk add sudo
```

```bash
printf "${MY_NEW_USER} ALL = (ALL:ALL) ALL\n" | \
  tee -a /etc/sudoers
```

{% hint style="info" %}
 The `sudo` command should be used with care. Never give yourself this much access unless you know what you're doing.
{% endhint %}

Upgrade to Alpine \`edge\`

```bash
sed -i -e 's/v[[:digit:]]..\//edge\//g' /etc/apk/repositories sed -e 's;^#http(.)/v3.7/community;http\1/v3.7/community;g' -i /etc/apk/repositories
```

Upgrade and update the system

```bash
apk update && \
apk upgrade \
  --update-cache \
  --available
```

apk add  e2fsprogs  git  mergerfs  qemu-img  qemu-system-x86\_64  libvirt-daemon  py3-libvirt  py3-libxml2  virt-install

## Harden SSH

> Generic tutorial can be found below

{% page-ref page="../general/harden-ssh.md" %}

## Add Docker

The Alpine host will be a hypervisor, so we want as few local installs as possible. Docker will allow us to spinup/down servers at will.

```bash
apk add \
  docker \
  docker-compose
```



```bash
rc-update add docker boot
```

Set the location for all docker images & containers

{% hint style="warning" %}
The next command will replace all contents of the file.
{% endhint %}

```bash
echo '{ "data-root": "/media/ssd/docker" }' | \
  sudo tee /etc/docker/daemon.json
```

sudo addgroup simonwjackson docker sudo addgroup simonwjackson kvm sudo addgroup simonwjackson qemu sudo addgroup simonwjackson libvirt

```bash

```

```bash

```

chmod go-w ~/ chmod 700 ~/.ssh chmod 600 ~/.ssh/authorized\_keys

UUID=6D2B-07C4 /media/alpine vfat ro,relatime,fmask=0022,dmask=0022,errors=remount-ro 0 0 UUID=e4fbb285-3f2a-451d-bb60-98f53af2474f /media/pool/ssd-usb xfs defaults 0 1 UUID=93cb282b-7d9a-4348-b952-5d24c8e1db6b /media/pool/ssd-1tb xfs defaults 0 1 UUID=54cd65d2-3c96-4616-9026-5799dd8b04e3 /media/pool/ssd-256gb btrfs defaults 0 1 UUID=a86e6c28-fc61-41b2-a8ea-a3e852dce282 /media/pool/tank xfs defaults 0 1 /media/pool/ssd-usb:/media/pool/ssd-1tb /storage mergerfs defaults,allow\_other,use\_ino,fsname=mergerFS 0 0

## mergerFS

echo fuse &gt;&gt; /etc/modules modprobe fuse mkdir /storage sudo mergerfs -o defaults,allow\_other,use\_ino,fsname=mergerFS /media/pool/ssd-usb:/media/pool/ssd-1tb /storage

echo "/media/pool/\* /storage fuse.mergerfs defaults,allow\_other,use\_ino,fsname=mergerFS 0 0" &gt;&gt; /etc/fstab

## KVM

sudo rc-update add libvirtd  
sudo rc-service libvirtd start

echo tun &gt;&gt; /etc/modules echo kvm &gt;&gt; /etc/modules echo kvm\_intel &gt;&gt; /etc/modules

virt-install  --debug  --name=gaming  --os-type=win10  --disk=/media/pool/nvme/sam-1tb/vm/gaming/disk,size=64  --graphics vnc,port=5901,listen=0.0.0.0  --vcpus=8  --cpu host  --virt-type kvm  --ram=16384  --cdrom=/media/pool/nvme/sam-1tb/isos/Win10\_2004\_English\_x64.iso  --network none

## Save State!

This is very important! You will lose everything when you reboot if you don't run this command

```bash
lbu commit -d
```

