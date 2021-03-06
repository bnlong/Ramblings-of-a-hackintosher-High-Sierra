# Pre-Install

## Pre-word
If you do not have a Mac computer, you can use a VM or check out this guide [here](https://github.com/camielverdult/Ramblings-of-a-hackintosher-High-Sierra/blob/master/InternetInstall.md#install-macosx-directly-from-the-internet).

## Necessities:
* 8GB+ USB stick
* Computer running MacOS (If you do not have a computer running MacOS, ask a friend/family member or check out how to make a Virtual Machine running MacOS on windows [here](https://techsviewer.com/install-macos-high-sierra-vmware-windows/).)
* A lot of time (remember: This is not something you do in 30 minutes and are done with forever.)
* A copy of the **full** High Sierra installer

## Step 1 - Making a bootable USB
Open up Disk Utility and select your USB **drive**, not the partition, and click erase. 

(If you cannot see your drive, click on show in the top left corner and select "Drives".)

For "Format", you want to select MacOS Extended (journaled) or APFS (journaled). For "Partition Scheme", you want to select "GUID Partition Table". Click Erase and wait until the process is finished. 

(If the process fails, try again. This has happened to me several times on (High) Sierra's Disk Utility with different PCs.)

Rename your USB to "USB".

## Step 2
Open up App Store on your MacOS VM or computer and search for Install High Sierra (hotlink [here](https://itunes.apple.com/us/app/macos-high-sierra/id1246284741?mt=12))

Check if you have the full installer, the size should be 4.8GB in total.

Open Terminal and run the following command

```
sudo /Applications/Install\ macOS\ High\ Sierra.app/Contents/Resources/createinstallmedia --volume /Volumes/USB
```

This will make a bootable USB to install High Sierra with, let the process run until it finishes.


## Step 3 - Install Clover
Download the latest version of clover from [here](https://github.com/Dids/clover-builder/releases/tag/v2.4k_r4370) (click `Clover_vx.x_rxxxx.pkg`, should be ~18mb)

Right click (or CMD+click) on the package and click Open, you will get a prompt telling you that the software is from an unidentified developer instead. ([how to disable this](http://osxdaily.com/2016/09/27/allow-apps-from-anywhere-macos-gatekeeper/))

Click Continue, Continue, Agree and Agree. Now, click Customize and select the following for booting Clover UEFI:
```
Install Clover for UEFI booting only
Install Clover to the ESP
Drivers64UEFI
    OsxAptioFix2Drv-64
    apfs
    PartitionDxe-64
```

For booting Legacy:
```
Install Clover to the ESP
Bootloader
    Install boot0ss in MBR
CloverEFI
    CloverEFI 64-bits SATA
```

## Step 3 - Downloading Kexts
Mount the EFI partition of your USB (howto [here](Tips.md#how-to-mount-efi))

We are now going to install some kernel extensions (often referred to as "kexts") necessary for booting at all. These include the following:
* FakeSMC
* USBInjectAll

If you have the iMac15,1;, iMac17,1;, iMac18,x or MacPro6,1, you also want:
* Lilu
* NvidiaGraphicsFixup

More info on that [here](Tips.md#nvidiagraphicsfixup-and-some-smbioses-explained),

All can be downloaded from [here](https://1drv.ms/f/s!AiP7m5LaOED-mo9XA4Ml-69cwAsikQ). Full kext spreadsheet [here](http://docs.google.com/spreadsheets/d/1WQ87XQKgJVPPub_CbjoHsUscgyxrGg3DWzZz7Nnf_RU/).

## Step 4 - Setting up the config.plist
For ease of use, we are going to download [Clover Configurator](http://mackie100projects.altervista.org/download-mac.php?version=classic) and configure our config.plist with that. You can also use [Clover Configurator Cloud (it's a little outdated but still functional)](http://cloudclovereditor.altervista.org/cce/index.php). 

* Start with a new, empty Clover Configurator file.
* In Boot: select Verbose (-v) and debug=0x100.
* In Devices, under USB, select Inject, Add ClockID and FixOwnership. 
* In GUI, under Scan, select Custom, Entries and Tool.
* In SMBIOS, click the dropdown button and select [a SMBIOS that corresponds with your system](Tips.md#choosing-a-smbios).
* If you have an Intel CPU with an iGPU, click the drop-down menu under ig-platform-id and select an id that matches your iGPU.
* Under Kernel and Kext Patches, check Apple RTC, AppleIntelCPUPM and KernelPM. Also add the following patch to your KextsToPatch:
```
Comment: change 15 port limit to 26 in XHCI kext
Name:    com.apple.driver.usb.AppleUSBXHCIPCI
Find:    837d8c10
Replace: 837d8c1b
```
* In System Paremeters, set Inject Kexts to Yes, check Inject System ID and click Generate New next to Custom UUID. Copy the generated UUID and paste it into smUUID in the SMBIOS section.
* In Rt Variables, click Generate, set BooterConfig to 0x28 and CSRActiveConfig to 0x3E7.

## Step 5 - BIOS/UEFI settings

Change the following settings before you boot into the macOS installer:

* Virtualization : Enabled
* VT-d : Disabled
* XHCI Hand-Off : Enabled
* Legacy USB Support: Auto/Enabled
* IO SerialPort : Disabled
* Network Stack : Disabled
* XMP Profile :  Auto / Profile 1/Enabled
* UEFI Booting set to Enabled and set Priority over Legacy
* Secure Boot : Disabled
* Fast Boot : Disabled
* OS Type: Other OS (you may want to try Windows 10, a lot of hackers recommend this)
* Wake on LAN : Disabled

Based on the GPU you’re using, change the following settings:
- Dedicated graphics card:
  - Integrated Graphics : Disabled 
  - Graphics: PEG/PCIe Slot 1
  - Initial Display Output : PCIe 1 Slot
- Intel iGPU:
  - Integrated Graphics : Enabled
  - Graphics: IGD/Integrated/iGPU/CPU Graphics
  - DVMT Pre-Allocated : 128M (If you cannot change this, check out the info [here](Tips.md#intelgraphicsdvmtfixup))

All done! You can now boot into the macOS installer.
Checkout how to install macOS on a mac [here](https://support.apple.com/en-us/HT204904).

If you get an error while booting, check [troubleshooting](Trobleshooting.md).
