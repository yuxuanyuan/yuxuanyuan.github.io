---
layout: post
title: Hardware monitoring
date: 2019-10-10 
tag: Hardware
---

In a bioinformatics lab, you may have your private servers to support your research. Hardware monitoring is also not a trivial task and you may need to learn how to handle it. Luckily, the Linux community has provided some useful tools to help achieve this.

### Check how many disks on your server

You may use the command below to check


```python
$ df -h 
```

Here is one example of the disks on one of our servers


```python
Filesystem      Size  Used Avail Use% Mounted on
/dev/nvme0n1p1  413G  9.3G  404G   3% /
devtmpfs         63G     0   63G   0% /dev
tmpfs            63G  4.0K   63G   1% /dev/shm
tmpfs            63G  291M   63G   1% /run
tmpfs            63G     0   63G   0% /sys/fs/cgroup
/dev/sda2        23T   25G   23T   1% /home
/dev/sda1       4.6T  106M  4.6T   1% /Database
tmpfs            13G     0   13G   0% /run/user/1000
```

From here you may see we have two normally used `sda` disks and one `ssd` disk (nvme). 

Next, you may learn how to monitor your hard drives. 

### Monitor HDD

`smartctl` controls the Self-Monitoring, Analysis and Reporting  Technology (SMART) system built into most ATA/SATA and SCSI/SAS hard drives and solid-state drives. The purpose of SMART is to monitor the reliability of the hard drive and predict drive failures, and to carry out different types of drive self-tests.

On Ubuntu, you may install `smartctl` using


```python
$ sudo apt-get install smartmontools
```

After your installation, you may use `smartctl` to monitor your HDDs.

There are different formats for HDD devices:


```python
1. "/dev/sd[a-z]" for ATA/SATA and SCSI/SAS devices
2. For SCSI Tape Drives and Changers with  TapeAlert  support  use  the  devices  "/dev/nst*"  and "/dev/sg*"
3. For  disks  behind 3ware controllers you may need "/dev/sd[a-z]" or "/dev/twe[0-9]", "/dev/twa[0-9]" or "/dev/twl[0-9]"
```

Detailed information 


```python
1. For disks behind  HighPoint  RocketRAID controllers you may need "/dev/sd[a-z]".  
2. For disks behind  Areca  SATA  RAID  controllers,  you  need  "/dev/sg[2-9]"  
3. For HP  Smart  Array  RAID  controllers, there are three currently supported drivers: cciss, hpsa, and hpahcisr.  
       a. For disks accessed via the cciss  driver  the  device nodes are of the form "/dev/cciss/c[0-9]d0".  
       b. For disks accessed via the hpahcisr and hpsa drivers, the device nodes you need are "/dev/sg[0-9]*".     
       c. Use the forms "/dev/nvme[0-9]" (broadcast namespace) or "/dev/nvme[0-9]n[1-9]"  (specific namespace 1-9) for NVMe devices.
```

You may use such as 


```python
$ sudo smartctl -a /dev/sda 
```

to show all SMART information for your device.


```python
$ sudo smartctl -x /dev/sda 
```

can give all information for your device, such as `status`, `powered hours` `firmware state` and `drive temperature`.

If you want to check the `health status`, you may need to use 


```python
$ sudo smartctl -H /dev/sda 
```

For identity information, you may use


```python
$ sudo smartctl -i /dev/sda 
```

After typing the cmd above, you may see 


```python
SMART support is: Available - device has SMART capability.
SMART support is: Enabled
```

in the last two lines.

### Monitor HDD in RAID 

Sometimes, your server may have a RAID architecture. RAID (Redundant Array of Inexpensive Disks or Drives, or Redundant Array of Independent Disks) is a data storage virtualization technology that combines multiple physical disk drive components into one or more logical units for the purposes of data redundancy, performance improvement, or both. 

After typing


```python
$ sudo smartctl -x /dev/sda
```

you may see 


```python
smartctl 6.5 2016-05-07 r4318 [x86_64-linux-3.10.0-957.1.3.el7.x86_64] (local build)
Copyright (C) 2002-16, Bruce Allen, Christian Franke, www.smartmontools.org

=== START OF INFORMATION SECTION ===
Vendor:               AVAGO
Product:              MR9460-16i
Revision:             5.02
User Capacity:        29,388,850,593,792 bytes [29.3 TB]
Logical block size:   512 bytes
Physical block size:  4096 bytes
Logical Unit id:      0x600062b203b904c023aa7a01fd41796b
Serial number:        006b7941fd017aaa23c004b903b26200
Device type:          disk
Local Time is:        Tue Oct  8 22:52:14 2019 PDT
SMART support is:     Unavailable - device lacks SMART capability.
Read Cache is:        Enabled
Writeback Cache is:   Enabled

...
```

To monitor your devices in this RAID, check your `Product` type. Here we use `megacli` to monitor.

`megacli` can be installed using


```python
$ wget https://docs.broadcom.com/docs-and-downloads/raid-controllers/raid-controllers-common-files/8-07-14_MegaCLI.zip
$ unzip 8-07-14_MegaCLI.zip
$ cd Linux
$ sudo alien -k --scripts MegaCli-8.07.14-1.noarch.rpm
$ sudo dpkg -imegacli_8.07.14-2_all.deb
$ sudo ln -s /opt/MegaRAID/MegaCli/MegaCli64 /usr/local/bin/megacli
$ sudo apt-get install sg3-utils
```

Using the command below, you may check your hard drive device id


```python
$ sudo megacli -PDList -aALL
```

After that, you can check all the information on the specific device using


```python
$ sudo smartctl -a -d megaraid,$id /dev/ada (# use the id number)
```

You may also use the cmd below to check the status of your devices


```python
$ sudo megacli -LDInfo -Lall -aALL
```

<br>
Copyright@[Andy's blog](http://yuxuanyuan.github.io) » [Hardware monitoring](http://yuxuanyuan.github.io/2019/10/Hardware_monitoring/)  
