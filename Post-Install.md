# Post-Install

## Necessities:
* macOS installed

## Step 1 - Installing clover to disk
This process is pretty similar to installing clover to the bootable usb in the pre-install section

Download the latest version of clover from [here](https://github.com/Dids/clover-builder/releases/latest/) (click `Clover_vx.x_rxxxx.pkg`, should be ~18mb)

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

Select your USB drive and click Install.

## Step 2 - Kexts
Now we're going to install our kexts (kernel extensions), just like we did in the [Pre-Install](Pre-Install.md#step-3---downloading-kexts).

First, [mount the EFI partition](Tips.md#how-to-mount-efi) of the disk you installed High Sierra on.

### Here are some general kexts you will definitely need:
* [FakeSMC](https://github.com/kozlek/HWSensors) (This is needed to boot **any** hackintosh. This kexts spoofs the presence of a valid SMC chip and stops the trigger of DontStealMacOS.kext when booting on non-mac hardware.)
* [Lilu](https://github.com/vit9696/Lilu) (Kext patch platform)
* [AppleALC](https://github.com/vit9696/AppleALC) (For audio)
* [USBInjectAll](https://github.com/RehabMan/OS-X-USB-Inject-All) (More info [here](.Tips.md#usbinjectall))
* A LAN kext (We will go into more detail about this later.)

You can find the latest compiled kexts in [here](https://1drv.ms/f/s!AiP7m5LaOED-mo9XA4Ml-69cwAsikQ) (Massive thanks to GoldFish64 for setting up and maintaining his kext repo.)
Or you can find official compiled by kext owner in [here](https://docs.google.com/spreadsheets/d/1WQ87XQKgJVPPub_CbjoHsUscgyxrGg3DWzZz7Nnf_RU/)

### LAN Kext
Check your motherboard specifications and look for the LAN Chipset. This could be one of the following:
* Intel® GbE LAN chip ([IntelMausiEthernet](https://github.com/Mieze/IntelMausiEthernet))
* Realtek RTL8111 ([RealtekRTL8111](https://github.com/Mieze/RTL8111_driver_for_OS_X))
* Atheros E2200 ([AtherosE2200](https://github.com/Mieze/AtherosE2200Ethernet))
* Realtek RTL8100 ([RealtekRTL8100](https://github.com/Mieze/RealtekRTL8100))
* Something else (check the [kext repo](https://1drv.ms/f/s!AiP7m5LaOED-mo9XA4Ml-69cwAsikQ) for a fitting kext)

### Graphics Kexts
If you do not have an SMBIOS listed [here](Tips.md#nvidiagraphicsfixup-and-some-smbioses-explained) or  if you do not have an AMD gpu, skip this step.

If you have an AMD GPU, make sure to get `WhateverGreen.kext` and Lilu. 
(Disclaimer: This works on some cards. There are often extra steps that need to take place for you to get full graphics acceleration.)

If you have the iMac15,1 and up (iMac17,1, iMac18,x) or MacPro6,1 SMBIOS, you will need to follow the instructions [here](Tips.md#nvidiagraphicsfixup-and-some-smbioses-explained).

All kexts can be downloaded from [here](https://1drv.ms/f/s!AiP7m5LaOED-mo9XA4Ml-69cwAsikQ)

## Step 3 - Setting up the config.plist
Yet again, for ease of use you'll have to install [Clover Configurator](http://mackie100projects.altervista.org/download-mac.php?version=classic) and configure our config.plist with that. Again you can also use [Clover Configurator Cloud (it's a little outdated but still functional)](http://cloudclovereditor.altervista.org/cce/index.php). 

First, [mount the EFI partition](Tips.md#how-to-mount-efi) of the install media and the disk you installed High Sierra on.

Now let's copy over the config.plist we used on your install media.

* In boot you can now remove Verbose (-v) and debug=0x100

Practically you should be set up on this part.

## Step 4 - USB setup
You can download USBInjectAll [here](https://1drv.ms/f/s!AiP7m5LaOED-mo9XA4Ml-69cwAsikQ).

If you're on a 200-series mobo you want to get [XHCI-200-series-injector.kext](https://github.com/RehabMan/OS-X-USB-Inject-All/tree/master/XHCI-200-series-injector.kext/Contents).

If you're on a X79/X99 mobo you want to get [XHCI-x99-injector.kext](https://github.com/RehabMan/OS-X-USB-Inject-All/tree/master/XHCI-x99-injector.kext/Contents).

You should have either USBInjectAll, XHCI-200-series-injector.kext or XHCI-x99-injector.kext. You should never have more USB/XHCI injector.

To make all the USB ports on your build work, you need to raise the port limit. (more info [here](Tips.md#usbinjectall))

Go to Kernel and Kext Patches and add a new KextsToPatch entry with the following info:
```
Name:    com.apple.driver.usb.AppleUSBXHCIPCI
Find:    837d8c10
Replace: 837d8c1b
Comment: change 15 port limit to 26 in XHCI kext
```

NOTE: all these steps should be taken in combination with having the [USBInjectAll](https://github.com/RehabMan/OS-X-USB-Inject-All) kext.

## Step 5 - Install graphics drivers
If you're using an AMD GPU you can skip this step.

If you have an NVIDIA Card, you need the NVIDIA WebDrivers for Graphics Acceleration. You can download the corresponding WebDriver from [this link](https://cookiemonster.pro/nvidia_driver_table).

Install the driver and open your config.plist in Clover Configurator. Go to System Parameters and check NvidiaWeb, save and reboot.

If your WebDrivers are not functioning properly (you check this by clicking the NVIDIA driver button in the top bar),you  need to check if your NVRAM is working the way it should be. For more in-depth [click here](Tips,md#nvidia-web-drivers-not-kicking-in).

## Step 6 - Audio
Check out the guide we made on audio [here](Audio.md).