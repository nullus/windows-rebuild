# Disk Upgrade

Transfer Windows install to a new disk using Windows Recovery Environment

## Summary

Having recently purchased an NVMe to replace my SATA SSD, I wanted a method to transfer my Windows installation with minimal hassle. In the past I've used a bootable FreeBSD live DVD to clone partitions, but this was before I'd upgraded to a UEFI system, and I suspected there could be some issues with such a low level approach. Besides, I wanted to get more comfortable with Windows 10, and I suspected Windows Recovery Environment would provide sufficient tools to do the job.

Searching [DuckDuckGo](https://duckduckgo.com/about) for "migrating" or "cloning" a Windows 10 install produced numerous results for 3rd-party GUI utilities, but that's not what I was looking for. Eventually I found Microsoft documentation to [capture and apply an image](https://docs.microsoft.com/en-us/windows-hardware/manufacture/desktop/capture-and-apply-windows-using-a-single-wim) which, though intended for hardware manufacturers, demonstrated all the tools required to perform the transfer.

## Process

After installing the NVMe and verifying that the device is working via Disk Manager (in this instance I initialised the disk with GPT), reboot into Windows Recovery Environment (hold shift while selecting Power -> Restart in the Start menu). [Find your way to the command prompt...]

### Partitioning/Formatting

Use `diskpart` to identify and partition the new drive:

    X:\windows\system32>diskpart

    Microsoft DiskPart version 10.0.19041.1

    Copyright (C) Microsoft Corporation.
    On computer: MININT-IGF6SH7

    DISKPART> list disk

      Disk ###  Status         Size     Free     Dyn  Gpt
      --------  -------------  -------  -------  ---  ---
      Disk 0    Online          223 GB      0 B        *
      Disk 1    Online          931 GB      0 B        *
      Disk 2    Online          465 GB   465 GB        *

You can see that disk 2 doesn't have any partitions by the Free column.

Select the disk we want to use, and apply the recommended partition layout:

    DISKPART> select disk 2
    
    Disk 2 is now the selected disk.

    DISKPART> create partition efi size=260

    DiskPart succeeded in creating the specified partition.

    DISKPART> format quick fs=fat32 label="System"

    100 percent completed

    DiskPart successfully formatted the volume.

    DISKPART> assign letter="S"

    DiskPart successfully assigned the drive letter or mount point.

    DISKPART> create partition msr size=16

    DiskPart succeeded in creating the specified partition.

    DISKPART> create partition primary

    DiskPart succeeded in creating the specified partition.

    DISKPART> shrink minimum=650

    DiskPart successfully shrunk the volume by:  650 MB

    DISKPART> format quick fs=ntfs label="Windows"

    100 percent completed

    DiskPart successfully formatted the volume.

    DISKPART> assign letter="W"

    DiskPart successfully assigned the drive letter or mount point.

    DISKPART> create partition primary

    DiskPart succeeded in creating the specified partition.

    DISKPART> format quick fs=ntfs label="Recovery tools"

    100 percent completed

    DiskPart successfully formatted the volume.

    DISKPART> assign letter="R"

    DiskPart successfully assigned the drive letter or mount point.

    DISKPART> set id="de94bba4-06d1-4d40-a16a-bfd50179d6ac"

    DiskPart successfully set the partition ID.

    DISKPART> gpt attributes=0x8000000000000001

    DiskPart successfully assigned the attributes to the selected GPT partition.

The above process can be scripted, and the script passed to `diskpart`.

Display the available volumes:

    DISKPART> list volume

    Volume ###  Ltr  Label        Fs     Type        Size     Status     Info
    ----------  ---  -----------  -----  ----------  -------  ---------  --------
    Volume 0     C   System Rese  NTFS   Partition     50 MB  Healthy
    Volume 1     D                NTFS   Partition    222 GB  Healthy
    Volume 2     F                NTFS   Partition    505 MB  Healthy
    Volume 3                      FAT32  Partition    100 MB  Healthy    Hidden
    Volume 4     E   data         NTFS   Partition    931 GB  Healthy
    Volume 5     W   Windows      NTFS   Partition    464 GB  Healthy
    Volume 6     R   Recovery to  NTFS   Partition    650 MB  Healthy
    Volume 7     S   SYSTEM       FAT32  Partition    260 MB  Healthy    Hidden

In this case I know that `D:` is our original Windows partition, and we have assigned `W:` to the destination partition.

### Data Transfer

Using DISM (Deployment Image Servicing and Management tool) we can capture (backup), and apply (restore) an image. You'll need a place to store the image. For this we used `E:`, which is a separate data disk.

Capture the original Windows system:

    X:\windows\system32>mkdir E:\Images
    
    X:\windows\system32>dism /capture-image /imagefile:"E:\Images\System.wim" /capturedir:D:\ /Name:System

    Deployment Image Servicing and Management tool
    Version: 10.0.19041.1

    Saving image
    [==========================100.0%==========================]
    The operation completed successfully.

Check the captured image:

    X:\windows\system32>dir E:\Images
     Volume in drive E is data
     Volume Serial Number is 76DB-B94D

     Directory of E:\Images

    02/22/2021  04:23 PM    <DIR>          .
    02/22/2021  04:23 PM    <DIR>          ..
    02/22/2021  04:52 PM   106,091,528,008 System.wim
                   1 File(s) 106,091,528,008 bytes
                   2 Dir(s)  233,562,648,576 bytes free

Apply the captured image to the destination partition:

    X:\windows\system32>dism /apply-image /imagefile:"E:\Images\System.wim" /ApplyDir:W:\ /name:System

    Deployment Image Servicing and Management tool
    Version: 10.0.19041.1

    Applying image
    [==========================100.0%==========================]
    The operation completed successfully.

Check the restored system:

    X:\windows\system32>dir W:\
     Volume in drive W is Windows
     Volume Serial Number is 7614-CB38

     Directory of W:\

    12/07/2020  08:28 PM    <DIR>          AMD
    12/07/2019  01:14 AM    <DIR>          PerfLogs
    02/18/2021  10:45 PM    <DIR>          Program Files
    12/16/2020  10:02 PM    <DIR>          Program Files (x86)
    10/12/2020  06:05 AM    <DIR>          Users
    02/15/2021  09:19 PM    <DIR>          Windows
                   0 File(s)              0 bytes
                   6 Dir(s)  341,876,269,056 bytes free

### Transfer boot/recovery files

Use bcdboot to transfer boot files to EFI partition `S:`

    X:\windows\system32>bcdboot W:\Windows /s S:
    Boot files successfully created.

Check the created files:

    X:\windows\system32>dir S:
     Volume in drive S is SYSTEM
     Volume Serial Number is A8E6-BA0D

     Directory of S:\

    02/22/2021  05:24 PM    <DIR>          EFI
                   0 File(s)              0 bytes
                   1 Dir(s)     240,435,200 bytes free

At this stage the Microsoft documentation how to transfer the WinRE files, however these weren't present in my install. I don't know exactly how I was _using_ Windows Recovery Environment, but by this point I was done.

### Booting the new system

Final steps:
  * Exit console
  * Shutdown system
  * Disconnect original Windows disk
  * Power on
  * Everything is fine 🔥

## Next steps

  * System Restore needed to be manually reconfigured, it still listed the old drive
