layout: post
title: "Pxe boot to install dual boot operation system"
date: 2016-04-25 12:15
tags:
- Pxe
- Dual Boot
categories:
- 方案
---

### Install winPE

```
#!ipxe
dhcp net0
set boot-url http://${dhcp-server}
initrd ${boot-url}/images/winre.iso
kernel ${boot-url}/memdisk iso raw
boot
```

### Install Ubuntu

```
#!ipxe
set boot-url http://${dhcp-server}
kernel ${boot-url}/iso/ubuntu1604/casper/vmlinuz.efi root=/dev/nfs boot=casper netboot=nfs nfsroot=${dhcp-server}:C:/Users/mengjue/Desktop/pxe/Pxeserver/files/iso/ubuntu1604 ip=dhcp ro
initrd ${boot-url}/iso/ubuntu1604/casper/initrd.lz
boot
```

===============================

Index:           
<http://labalec.fr/erwan/?p=773>

QuickPE:             
<http://reboot.pro/topic/18744-quickpe/>

TinyPxe Server:         
<http://reboot.pro/topic/18488-tiny-pxe-server/>
