---
id: 1434
title: Installing Windows with Secure Boot from USB drive
date: 2017-07-18T13:58:01+01:00
author: Anders Lybecker

guid: http://www.lybecker.com/blog/?p=1434
permalink: /2017/07/18/installing-windows-with-secure-boot-from-usb-drive/
dsq_thread_id:
  - "5997382197"
categories:
  - Stuff
  - Useful tools
tags:
  - BIOS
  - BitLocker
  - Secure Boot
  - UEFI
  - Windows 10
---
Once and a while I reinstall my machine. It feels nice with a clean slate as I tend to install all kinds of applications that pollutes my machine. A newly installed machine just runs better somehow.

My machine needs to be secure, so Secure Boot and encrypted drive via BitLocker is a must. It limits the risk of someone messing with my machine and stealing my data.

Here is how to create a bootable USB that works with Secure Boot enabled:

  1. Download 64-bit Windows ISO file and [download Rufus](http://rufus.akeo.ie/), a portable utility to create bootable USB drives.
  2. Connect a 4+ GB USB drive. The data on the drive will be deleted, so make sure to backup the content.
  3. Run Rufus and accept the UAC prompt to launch the tool.
  4. Select the USB drive that you want to make bootable, select _GTP partition scheme for UEFI_, _FAT32_ and select the Windows ISO image.<img loading="lazy" class="alignnone size-full wp-image-1435" src="http://www.lybecker.com/blog/wp-content/uploads/2017/07/Rufus.png" alt="" width="365" height="518" srcset="http://www.lybecker.com/blog/wp-content/uploads/2017/07/Rufus.png 365w, http://www.lybecker.com/blog/wp-content/uploads/2017/07/Rufus-211x300.png 211w" sizes="(max-width: 365px) 100vw, 365px" />

Do remember to enable Secure Boot in the BIOS settings and set it in setup mode/clear the keys.

GTP (GUID Partition Table) is a replacement form MBR partitioning allowing for larger disk sizes which requires a 64-bit OS. [Read more](https://msdn.microsoft.com/en-us/library/windows/hardware/dn640535%28v=vs.85%29.aspx).