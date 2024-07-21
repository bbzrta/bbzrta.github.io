---
title: Recreating EFI for Windows
layout: post
categories: [Troubleshooting,Windows]
tags: [bcdboot,diskpart,microsoft,efi,bootloader,dualboot,windows]     # TAG names should always be lowercase
---

## Boot Your PC and Prepare for a Dual Boot (Windows 8/8.1/10)

A while ago as I was playing around with my dualboot system, i accidentally deleted the EFI partitoin and had to figure out how to restore my windows installatoin. below is the steps i took to recover.

**What You'll Need:**

* A bootable USB drive or DVD containing your desired Windows 8/8.1/10 installation media.
* Some knowledge of your computer's BIOS settings (consult your motherboard manual if needed).

### **Steps:**

#### **1. Boot from Installation Media:**

* Insert your Windows installation media and restart your computer.
* During startup, you might need to press a specific key (usually Del, Esc, F2, F10, or F9) to enter the BIOS settings. Refer to your motherboard manual for the exact key.
* In the BIOS, locate the Boot Options menu and set the installation media (USB drive or DVD drive) as the first boot device. Save the changes and exit BIOS.
* Your computer should now boot from the installation media.

#### **2. Open Command Prompt:**

* On the first screen of the Windows installation process, press **Shift+F10** to open a command prompt window.

#### **3. Prepare the Partition (**Important: Back up your data before proceeding**):**

* In the command prompt window, type the following commands one at a time, pressing Enter after each:

```shell
diskpart
list disk
```
* Identify the disk number where you want to create the EFI System partition (usually Disk 0).

```shell
select disk # (Replace # with the disk number)
list partition
```

* Identify the partition number you want to shrink to create space for the EFI partition (typically your Windows OS partition or a data partition).

```shell
select partition # (Replace # with the partition number)
shrink desired=100  (Replace 100 with the desired size in MB for the EFI partition)
```

* This command shrinks the selected partition by 100 MB (adjust the value if needed).

```shell
create partition efi size=100
format quick fs=fat32
assign letter=S
list partition
list volume 
exit
```

* This creates a new EFI partition of 100 MB formatted as FAT32, assigns it the drive letter S, and lists all partitions and volumes.
* Note down the volume letter where your Windows OS is installed (usually C:).

#### **4. Configure Boot Manager**

* Type the following command and press Enter:

``` shell
bcdboot X:\windows /s S:
# Note: Replace "X" with the volume letter of the Windows OS partition.
```

* Replace "X" with the volume letter of your Windows OS partition (noted in step 3).
* This command copies the boot files from your Windows partition to the newly created EFI partition (S:) and sets it up for booting.

#### **5. Finalize and Restart**

* Type exit and press Enter to close the command prompt window.
* Remove the Windows installation media and restart your computer.

#### **6. Set Boot Order in BIOS:**

* Your computer should boot normally. However, you might need to access your BIOS settings again to ensure your primary hard drive/SSD is set as the first boot device. This ensures your existing operating system remains the default.