---
layout: post
title: 'Creating Virtual Machines using QEMU/KVM'
tags:
- virtualization
- qemu
categories:
- linux
date: 2017-10-01 10:00:30
---

Virtual machines and virtualization is everywhere today. For understanding cloud infrastrcture today, it is  first important to undertsand how virtual machines work. In this article, we'll create a VM in Linux using QEMU and KVM. 

## Introduction

### QEMU and KVM

QEMU or Quick Emulator is an open-source machine emulator and virtualizer. It can be used for full-system emulation, user-mode emulation as well as virtualization. We'll be using the full-system emulation capabilities here. 

KVM or Kernel-based Virtual Machine on the other hand is a virtualization infrastructure which turns Linux into a hypervisor. It is part of the Linux kernel mainline since 2007. KVM when activated along with Qemu, speeds up the emulation several times. 

There are numerous manager software for both QEMU and KVM, which give a UI for creating VMs and make optioning easier. However, using QEMU directly helps in better understanding the process.

## Setting up the environment

### Installing QEMU 

QEMU is available as a PPA for Ubuntu, which can be downloaded using

```shell
$ sudo apt-get install qemu
```

### Downloading iso image
To demonstrate the process we'll create a Debian VM.(for potential use as a SSH or web server)
```shell
$ wget https://cdimage.debian.org/debian-cd/current/amd64/iso-cd/debian-9.1.0-amd64-netinst.iso
```

### Creating a virtual hard-disk

The first step in creating the VM is to create a storage space for the operating system and files on the VM.
A virtual hard drive is an image with its own partition table, onto which operating systems can be installed. We can create one using *qemu-img*.
```shell
$ qemu-img create -f qcow2 debian.qcow2 6G
```
The command takes the location and size to create an hard-drive image. The *-f* option specifies the format for disk-image to be created. 
We'll later to this image to install Debian.

## Installing the ISO and Booting Up VM

### Installation
To install the Debian image onto the virtual hard-drive we just created.

```shell
$ qemu-system-x86_64 -enable-kvm -hda debian.qcow2 -cdrom ../Downloads/debian-9.1.0-amd64-netinst.iso -net user -net nic -m 4G -boot d 
```
qemu-system-x86_64 is the utility used for creating 64 bit VMs. It takes as input the options required for our machine, and starts up the machine with given options.

In the command, for *-cdrom* option, we enter the location of our iso file. Option *-m* tells the system memory, and 4 GB of RAM should work fine for most installations. Time taken for the install depends on the amount of RAM allocated.

Once the install is complete, QEMU reboots automatically. We can then close the window and go back to the terminal.

### Booting up

Now to boot up the virtual machine, we use
```shell
$ qemu-system-x86_64 -enable-kvm -hda debian.qcow2 -boot c -net user -net nic -m 2G
```
The *-boot* option changes to let the machine to boot from the hard-drive image, and RAM size is reduced to 2 GB.

## Conclusion

We've created a simple VM running Debian. This VM can be used for purposes such as learning about the new OS, testing softwares and packages on it or even creating a simple file or mail server to understand its workings. Then there's also the satisfaction of having succesfully installed and used an operating system without ruining your main hard-drve. 
