---
description: I have a Debian desktop with 128GB RAM (RAM isn't too expensive, so this
  wasn't too bad). My hypervisor is KVM. I started out trying to run the Firepower
  Management Center and Firepower Thread Defense firewalls in EVE-NG, but that didn't
  seem to work, so I installed them as their own VMs.
pubDate: '2024-08-14'
title: Labbing a Cisco FTD Upgrade with KVM on Linux
---


## Setup KVM

I have a Debian desktop with 128GB RAM (RAM isn't too expensive, so this wasn't too bad).  My hypervisor is KVM.  I started out trying to run the Firepower Management Center and Firepower Thread Defense firewalls in EVE-NG, but that didn't seem to work, so I installed them as their own VMs.

I setup bridging in the /etc/network/interfaces file, so that the VMs could communicate on the 192.168.1.0/24 subnet.

```
auto br0
iface br0 inet static
	address 192.168.1.200
	gateway 192.168.1.1
	dns-nameservers 192.168.1.254
	bridge_ports enp5s0
```

## Cisco Firepower Management Center virt-install script

```
virt-install \
	--name=fmc1 \
	--arch=x86_64 \
	--cpu=host \
	--vcpus=4 \
	--ram=32768 \
	--os-variant=rhel8-unknown \
	--import \
	--hvm \
	--virt-type=kvm \
	--graphics vnc,listen=0.0.0.0 \
	--noautoconsole \
	--disk path=/home/steve/Cisco_Firepower_Mgmt_Center_Virtual_KVM-7.0.6-236.qcow2,format=qcow2,device=disk,bus=virtio,cache=writethrough \
	--disk=/home/steve/VMs/fmc1.qcow2,size=100 \
	# --network type=direct,model=virtio,source=br0,source_mode=bridge
```

I didn't end up needing a dedicated network option.  

## Setting up the Cisco Firepower Threat Defense Firewall

The FTD took some trial and error.  On boot, it complained about not having 4 interfaces, so I gave it 4 interfaces and it was happy.

```
virt-install \
	--network bridge=br0,model=virtio \
	--network bridge=br0,model=virtio \
	--network bridge=br0,model=virtio \
	--network bridge=br0,model=virtio \
	--name=ftd7 \
	--arch=x86_64 \
	--cpu=host \
	--vcpus=4 \
	--ram=16384 \
	--os-variant=generic \
	--import \
	--virt-type=kvm \
	--graphics vnc,listen=0.0.0.0 \
	--noautoconsole \
	--disk path=/home/steve/VMs/Cisco_Firepower_Threat_Defense_Virtual-6.6.1-91.qcow2,format=qcow2,device=disk,bus=virtio,cache=writethrough \
	--disk=/home/steve/VMs/ftd7.qcow2,size=100 \
```

I was able to configure the FMC to manage the FTD through the configure command on the FTD CLI

```
configure manager 192.168.1.60 Cisco123
```

and then add the FTD from the FMC Device Management tab.  Once I had that, I was able to upload upgrade files to the FMC, push to the FTD and then upgrade the FTD.  Worked well.  I hope this saves you some time if you're trying to lab this out.
