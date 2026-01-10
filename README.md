![OpenCore logo](https://github.com/acidanthera/OpenCorePkg/raw/master/Docs/Logos/OpenCore_with_text_Small.png)

# Surface-Laptop-3-OpenCore
macOS on the Microsoft Surface Laptop 3 thanks to [Acidanthera's OpenCore bootloader](https://github.com/acidanthera/OpenCorePkg).

## Latest News
* (20260110) Added resources and instructions to enable `AirportItlwm.kext` and fix audio on macOS Tahoe ([see section below](https://github.com/jlempen/Surface-Laptop-3-OpenCore/tree/main?tab=readme-ov-file#enabling-the-intel-wireless-card-in-macos-sequoia-and-tahoe)).
* (20260110) Fixing audio in macOS Tahoe ([see section below](https://github.com/jlempen/Surface-Laptop-3-OpenCore/tree/main#fixing-audio-on-macos-tahoe)).
* (20251018) With the stuff merged today, macOS Tahoe 26.0.1 now installs (albeit with some flickering in the installer) and runs quite nicely, but expect ~~internal audio and~~ FileVault to be broken. **_Don't enable FileVault when prompted at the end of the install process._** Work in progress.
* (20251016) Added resources and instructions to enable the `AirportItlwm.kext` on macOS Sonoma
* (20251016) Added the `DisableBDPROCHOT.efi` driver to fix the throttling issues
* (20251011) Fixed issues with the firmware downgrade by adding the `--force` argument to the `fwupdmgr` command line
* (20250901) Disabled the touchscreen to put an end to the dreaded overheating issues
* (20250620) Fixed external displays over USB-C

## Software Specifications
| Software         | Version                            |
| ---------------- | ---------------------------------- |
| Target OS        | Apple macOS 13 Ventura, 14 Sonoma, 15 Sequoia and 26 Tahoe (work in progress) |
| OpenCore         | [MOD-OC v1.0.7](https://github.com/wjz304/OpenCore_NO_ACPI_Build/releases/download/1.0.7_ffbd7f5/OpenCore-Mod-1.0.7-RELEASE.zip) |
| SMBIOS           | MacBookPro16,2 |
| SSD format       | APFS file system, GPT partition table |

## Abstract
With its nice display, large and smooth trackpad, comfortable keyboard and its quite decent speakers, the `Surface Laptop 3` series is an excellent Hackintosh laptop. Moreover, the M.2 2230 SSD can be swapped easily for a larger one.

Apart from ACPI S3 Sleep which is broken, everything on the `Intel 13.5-Inch and 15-Inch Surface Laptop 3` is working perfectly like on a real Mac. ACPI S4 Hibernate works great, though, and resuming from Hibernation takes around ten to fifteen seconds. The advantage Hibernate has over Sleep is that the device doesn't draw any power while in a hibernated state.

The battery runtime is around five hours.

> [!IMPORTANT]
> For macOS to be able to boot on the Surface Laptop 3, the `Secure Boot` option _**must be disabled**_ [in the UEFI](https://github.com/jlempen/Surface-Laptop-3-OpenCore/blob/main/README.md#uefi-settings). The boot screen will then display a large red bar with a padlock symbol at the top of the display when Secure Boot is disabled.

> [!IMPORTANT]
> If your Surface Laptop 3 randomly shuts down due to overheating during the macOS installation process, disable the `DisableBDPROCHOT.efi` driver in the `UEFI -> Drivers` section of your `config.plist` file.
> Once macOS is up and running, you can enable the driver again.

> [!IMPORTANT]
> The keyboard and trackpad are now working in the installer as well as in the installed OS, but the trackpad will be lagging/skipping every few seconds. Furthermore, the keyboard and trackpad will be unresponsive after resuming from hibernation. To fix those issues, you **MUST** downgrade the firmware of your `Surface Laptop 3`. To do so, [follow these instructions](https://github.com/jlempen/Surface-Laptop-3-OpenCore?tab=readme-ov-file#downgrading-the-uefi-firmware).
>
> Then, you **MUST** also replace `BigSurface.kext` and its dependencies with `BigSurfaceSLB3.kext` and its dependencies in your `config.plist` file by following [these instructions](https://github.com/jlempen/Surface-Laptop-3-OpenCore/blob/main/README.md#replacing-bigsurfacekext-with-bigsurfaceslb3kext).

> [!CAUTION]
> ~~At the moment, it is not possible to have Windows installed at the same time as macOS, as the SL3 will immediately update to the latest firmware again when you reboot into Windows, even if you turn off your WiFi or disable automatic Windows Updates. I haven't found a way to prevent this behaviour yet. Any kind of Linux distribution is fine, though.~~
> 
> As of 6 April 2025, [@pnxl](https://github.com/pnxl) added instructions on how to run Windows on Surface Laptop 3, without Windows Update updating the firmware to the latest firmware version upon reboot. The instructions can be found [here](https://github.com/jlempen/Surface-Laptop-3-OpenCore/blob/main/README.md#running-windows-without-updating-the-firmware).

## Disclaimer
This repository is neither a howto nor an installation manual. Using these files requires at least basic knowledge of [Acidanthera's OpenCore bootloader](https://github.com/acidanthera/OpenCorePkg), ACPI, UEFI and the art of hackintoshing in general. I recommend reading the excellent [Dortania's OpenCore Install Guide](https://dortania.github.io/OpenCore-Install-Guide), as well as all its linked resources. For those who wish to improve their hackintoshing knowledge, [5T33Z0's OC-Little-Translated](https://github.com/5T33Z0/OC-Little-Translated) repository is the most comprehensive resource I've found on the subject.

## Recommendations
I recommend completely erasing the device's SSD by creating a new GPT partition table before attempting to install macOS, as it makes the installation process much easier. You may use any Linux live ISO with a partitioning tool such as `GParted` or `KPartition` to erase the SSD.

Please be aware that all `PlatformInfo` and `SMBIOS` information was removed from the OpenCore `config.plist` file. Users will therefore need to generate their own `PlatformInfo` with [CorpNewt's GenSMBIOS tool](https://github.com/corpnewt/GenSMBIOS) before attempting to boot a Surface Laptop 3 with this repository's EFI folder.

`BigSurfaceSLB3.kext` is required to enable the trackpad, the keyboard, the touchscreen, the battery status, the power and volume buttons and the ambient light sensor. Updating this `kext` with the official one from [Xiashangning's BigSurface repo](https://github.com/Xiashangning/BigSurface) will most certainly break something! `BigSurfaceSLB3.kext` is a modified version of `BigSurface` v6.5 which fixes the skipping/lagging trackpad and hibernate mode 25.

An UEFI firmware released by Micro$oft in August 2023 broke the Surface Laptop 3's trackpad in macOS in several ways. Downgrading the UEFI firmware to the last known working version `13.101.140.0` is required to fix the trackpad and hibernate mode 25 on macOS. See the detailed instructions below to easily downgrade the UEFI firmware. This needs to be done before installing any version of macOS or you won't have a working trackpad and/or keyboard during installation.

`AirportItlwm-Ventura.kext`, `AirportItlwm-Sonoma140.kext`, `AirportItlwm-Sonoma144.kext` and `AirportItlwm-Sequoia-Tahoe.kext` from the [OpenIntelWireless repo](https://github.com/OpenIntelWireless/itlwm) are drivers required to enable the Wifi chip. This EFI will dynamically load the appropriate kext for macOS Ventura, Sonoma, Sequoia or Tahoe depending on the running kernel. No need to manually replace the kext file when updating your version of macOS. 

In macOS Sequoia and Tahoe, you'll need to apply root patches with [Laobamac's OCLP-Mod Patcher](https://github.com/laobamac/OCLP-Mod/releases) once the OS is up and running in order to enable the Intel Wifi chip. [Head over to the instructions.](https://github.com/jlempen/Surface-Laptop-3-OpenCore/tree/main?tab=readme-ov-file#enabling-the-intel-wireless-card-in-macos-sequoia-and-tahoe).

the `Itlwm.kext` driver and its companion app [HeliPort](https://github.com/OpenIntelWireless/HeliPort/releases) are included but disabled in this EFI for those who prefer to connect to their Wifi network this way. You'll find the latest stable `HeliPort.dmg` in the [Tools folder](https://github.com/jlempen/Surface-Laptop-3-OpenCore/blob/main/Tools/HeliPort.dmg) of this repo.

This repository uses the unofficial OpenCore_NO_ACPI_Build fork of OpenCore by [btwise](https://gitee.com/btwise/OpenCore_NO_ACPI), wich is not endorsed by Acidanthera (the dev team behind OpenCore). The main (and only) difference between this fork and the official OpenCore version is that it allows to prevent ACPI injection (e.g. patches, tables, boot parameters) into other OSes besides macOS.

Windows and Linux should be detected automagically by the OpenCore boot loader even when installed after macOS.

<details>
  <summary>Computer Specifications</summary>
  
## Computer Specifications
| Device           | Hardware                           |
| ---------------- | ---------------------------------- |
| CPU              | Intel Core i7-1065G7 or Intel Core i5-1035G7 |
| iGPU             | Intel Iris Plus Graphics |
| Audio            | Realtek ALC274 |
| RAM              | 8 or 16 GB RAM |
| Wifi + Bluetooth | Wifi6 AX200, Bluetooth 5.0 |
| Storage          | Kioxia/Toshiba/SK Hynix PCIe NVMe 2230 SSD |
| USB Type-C 3.1 Gen 1 | Supports Power Delivery and DisplayPort |
| Camera | 720p |
| IR camera | Intel(R) AVStream Camera 2500, ISP Interface |
| Keyboard / Trackpad | |
| Display | 13.50 inch 3:2, 2256 x 1504 pixel or 15 inch 3:2, 2496 x 1664 pixel 201 PPI |
| Touchscreen | 10-point capacitive |
| Battery | |
| Ambient light sensor | |
</details>

<details>
  <summary>What works</summary>
  
## What works
- [x] CPU power management
- [x] CPU SpeedStep
- [x] iGPU with full acceleration
- [x] SSD drive
- [x] USB-C port
- [x] USB-A port
- [x] WLAN
- [x] Bluetooth
- [x] 720p camera
- [x] Internal speakers, microphone and Combojack
- [x] Power, volume up and volume down buttons
- [x] Keyboard with working brightness, volume and mute keys, working caps lock light
- [x] Trackpad with native multi-touch gestures
- [x] Ambient light sensor
- [x] Battery percentage and cycle count
- [x] Hibernation (hibernatemode 25) - the device successfully wakes up from hibernation mode
- [x] USB Type-C to HDMI
- [x] USB Type-C to USB3 & USB2
- [x] USB Type-C Power Delivery
</details>

<details>
  <summary>What needs some more work</summary>
  
## What needs some more work
- [ ] The Touchscreen is disabled for now as it causes severe throttling and overheating issues
</details>

<details>
  <summary>What will probably never work</summary>
  
## What will probably never work
- [ ] Sleep (hibernatemode 3) - the device only turns off the display without sleeping
- [ ] IR camera (Windows Hello)
</details>

<details>
  <summary>UEFI Settings</summary>
  
## UEFI Settings
To enter the UEFI Settings, power on your Surface Laptop 3 and hold the `Volume Up Button` as soon as the Surface Logo is displayed on the screen.

The `Secure Boot` setting ***must be disabled to boot macOS***. I also recommend moving `USB Storage` to the top of the boot configuration list, which makes booting from an USB stick much easier.

| Security | |
| -------- | ----- |
| Secure Boot | Disabled |

| Boot configuration | |
| -------- | ----- |
| USB Storage | Move the item to the top of the list |
</details>

<details>
  <summary>Downgrading the UEFI firmware</summary>

## Downgrading the UEFI firmware
In order to fix the skipping/lagging trackpad in macOS and make the trackpad and keyboard work after hibernation, you must downgrade your UEFI firmware to the last known working version `13.101.140.0`.

1. Boot with a Linux Live USB stick, preferably a Debian, Arch or Fedora based distribution (I use the Arch-based Manjaro).
2. Download and unzip the compressed firmware archive [SurfaceLaptop3_FW_13.101.140.0.zip](https://github.com/jlempen/Surface-Laptop-3-OpenCore/blob/main/UEFI%20Firmware/SurfaceLaptop3_FW_13.101.140.0.zip) from this repository.
3. Add the line `OnlyTrusted=false` to the `/etc/fwupd/daemon.conf` config file. On some Linux distros such as Arch, endeavourOS and Manjaro, the config file to change is `/etc/fwupd/fwupd.conf`:
```
sudo nano /etc/fwupd/daemon.conf
```
or
```
sudo nano /etc/fwupd/fwupd.conf
```
4. Open a terminal and navigate to the folder where you extracted the firmware files.
5. Connect your Surface device to a power supply.
6. Copy the following lines and paste them into the terminal:
```
for f in *; do 
  sudo fwupdmgr install --force --allow-older --allow-reinstall --no-reboot-check "$f"
done
```
7. Close the terminal and reboot into Linux once more.

For some firmware files, the `fwupdmgr` tool may complain that it is unable to find a matching device. This is normal, as not all Surface Laptop 3 models use the exact same hardware, thus the compressed firmware archive contains all the required files for all models.

The Surface Laptop 3 will reboot and downgrade all UEFI firmwares at once, which takes around 5 to 10 minutes. You'll see progress bars with different colours depending on which type of firmware is being flashed.
Once the process is done, your laptop will restart a few times and seem to hang on the Surface logo for 20 or 30 seconds each time, this is normal. Then it will restart for good to your OpenCore picker.

Now restart while holding the F4/Volume Up key to check the firmware version in the UEFI. In the Firmware section, `System UEFI` should now show `13.101.140`.

Reboot and you're done.

~~If you are using Windows on the laptop, you'll have to find a way to prevent Windows Update from automatically updating the firmware to the latest firmware version again on the next reboot into Windows! I haven't found a way to prevent this yet. Any kind of Linux distribution is fine, though.~~

As of 6 April 2025, [@pnxl](https://github.com/pnxl) added instructions on how to run Windows on Surface Laptop 3, without Windows Update updating the firmware to the latest firmware version upon reboot. The instructions can be found [here](https://github.com/jlempen/Surface-Laptop-3-OpenCore/blob/main/README.md#running-windows-without-updating-the-firmware).
</details>

<details>
  <summary>Running Windows without updating the firmware</summary>
  
## Running Windows without updating the firmware
Since Windows 7 (and Server 2008), Microsoft has added a way for system administrators to prevent the installation of device drivers for specific devices via a hardware ID block list. We can take advantage of this to prevent any firmware updates to the Surface UEFI firmware.

First, you'll have to install Windows with the Surface recovery (as the Windows installer DOES NOT include the drivers to communicate with the Surface Aggregator Module which handles HID devices, oddly enough)

Once you have Windows up and running, you'll want to install the last known working version of the drivers. Download [SurfaceLaptop3_Win10_19041_22.011.9779.0.msi](https://drive.google.com/file/d/1UfafCWYVMuY5J_2tNFkMs9-fSAdw-RYQ/view?usp=sharing) from this Google Drive mirror. If you haven't downgraded your firmware, this will do that for you too.

After complete, follow the instructions to block device driver installation for the Surface UEFI firmware.

1. Open the Registry Editor, and navigate to `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows`
2. Make a new key, named `DeviceInstall`
3. Under that key, make another new key named `Restrictions`
4. Under the `Restrictions` key, make a DWORD Value named `DenyDeviceIDs` and `DenyDeviceIDsRetroactive`. Set both values to 1.
5. Create a new key under `Restrictions`, named `DenyDeviceIDs`.
6. You should end up with something like this.
![Registry Editor showing DWORD values under Restrictions key](https://github.com/user-attachments/assets/038f12c7-022b-47c9-93fe-1b01a3d44bcd)

We're not done just yet - let's continue.
1. Open Device Manager, and locate, then expand the Firmware section.
2. You'll see a bunch of different firmware modules for the components on your Surface.
3. Right-click on one, and click on Properties.
4. Click on the details tab, and select Hardware IDs from the dropdown.
5. Right-click the top-most Hardware ID, and paste it in a text editor, temporarily.

Repeat for all the remaining firmware modules. You should end up with something like this.
![All Hardware IDs listed in a text document](https://github.com/user-attachments/assets/02877452-26b4-4c8a-a01c-d8e0f82c81a6)

We're still not done! Hang in there.
1. Go back to the Registry Editor, and go to the `DenyDeviceIDs` key we made.
2. Create a new String Value, and name it `1`.
3. Set the value to the first Hardware ID you copied.

Rinse and repeat for the rest of the Hardware IDs you copied. Increment the names of the string values as you go. (Name 1 for the first, 2 for the second, and so on)

> Pro tip!
> If you need to make lots of String Values, you'll find that it gets cumbersome to do (especially on a trackpad). You can use the following keyboard sequence to create String Values much faster: [Alt], [E], [N], [S], [number].

In the end, you'll have something similar to this.
![Registry Editor showing all Hardware IDs added to DenyDevicesIDs key](https://github.com/user-attachments/assets/76259a01-6a53-4416-b249-14190c45fe92)

Restart your computer to apply the changes.
</details>

<details>
  <summary>Replacing BigSurface.kext with BigSurfaceSLB3.kext</summary>
  
## Replacing BigSurface.kext with BigSurfaceSLB3.kext
Additionally, to fix the skipping/lagging trackpad in macOS and make the trackpad and keyboard work after hibernation, you also have to replace the official `BigSurface.kext` with the `BigSurfaceSLB3.kext`. 

In the `Kernel` -> `Add` section of your `config.plist` file, disable or delete the following kexts:
```
BigSurface.kext/Contents/PlugIns/VoodooGPIO.kext
BigSurface.kext/Contents/PlugIns/VoodooSerial.kext
BigSurface.kext/Contents/PlugIns/VoodooInput.kext
BigSurface.kext
BigSurface.kext/Contents/PlugIns/BigSurfaceHIDDriver.kext
```
Then enable the following kexts:
```
BigSurfaceSLB3.kext/Contents/PlugIns/VoodooGPIO.kext
BigSurfaceSLB3.kext/Contents/PlugIns/VoodooSerial.kext
BigSurfaceSLB3.kext/Contents/PlugIns/VoodooInput.kext
BigSurfaceSLB3.kext
BigSurfaceSLB3.kext/Contents/PlugIns/BigSurfaceHIDDriver.kext
```
Save your `config.plist` file and reboot. Your trackpad should now be buttery smooth before and after resuming from hibernation. The keyboard will now also work after resuming from hibernation.
</details>

<details>
  <summary>Disabling Sleep and enabling Hibernate</summary>
  
## Disabling Sleep and enabling Hibernate
As we still haven't found a solution for the Sleep/Wake issues on the Surface Laptop 3, disable Sleep altogether and use Hibernate for now. Open the `Terminal` and enter the following commands, then reboot for the changes to take effect:
```
sudo pmset restoredefaults
sudo pmset -a hibernatemode 25
```
If for whatever reason Hibernate is not working on your system, you should reset the `Power Management` settings and rebuild the `sleepimage` file. To do so, open the `Terminal` and enter the following commands, then reboot for the changes to take effect:
```
sudo rm /Library/Preferences/com.apple.PowerManagement*
sudo rm /var/vm/sleepimage
sudo pmset hibernatefile /var/vm/sleepimage
```
Once you are back in macOS, disable Sleep and enable Hibernate again, then reboot:
```
sudo pmset restoredefaults
sudo pmset -a hibernatemode 25
```

It's also a good idea to reset the NVRAM before rebooting into macOS. To do so, press the space bar in the OpenCore picker and use the arrow keys to select `Reset NVRAM`.

Keep in mind that once the Surface Laptop 3 hibernates, you need to let it hibernate for a couple of minutes before waking it up. Failing to do so will disrupt hibernation and the device will hang. You'll have to turn it off forcefully by pressing on the power button for 10 seconds. Sometimes you even need to press the power button for up to 20 seconds to restart the laptop.
</details>

<details>
  <summary>Enabling the Intel Wireless Card in macOS Sequoia and Tahoe</summary>
  
## Enabling the Intel Wireless Card in macOS Sequoia and Tahoe
Download and install the latest release of [Laobamac's OCLP-Mod Patcher](https://github.com/laobamac/OCLP-Mod/releases).

Launch the OCLP-Mod Patcher, this may take a few seconds. As of January 2026, the tool is only available in Chinese.

Now click on the upper right button to select the Root Patching option:
<img width="712" height="443" alt="Screenshot 2026-01-10 at 00 43 16" src="https://github.com/user-attachments/assets/3af5464a-836b-4f71-a0fc-8ac8bd4af304" />
Then click on the highlighted button or press Enter to start the patching process:
<img width="712" height="443" alt="Screenshot 2026-01-10 at 00 46 51" src="https://github.com/user-attachments/assets/843687df-655f-47ea-bfc8-5b3f06681756" />
Once the patching is done, click on the highlighted button or press Enter to close the tool and restart your computer. Your Intel wireless card should be working now.

> [!IMPORTANT]
> You'll need to repeat those steps after every macOS update!
</details>

<details>
  <summary>Fixing audio on macOS Tahoe</summary>
  
## Fixing audio on macOS Tahoe

## By root patching
As Apple removed the `AppleHDA.kext` from macOS Tahoe, [Acidanthera's AppleALC.kext](https://github.com/acidanthera/AppleALC) won't work on macOS Tahoe out of the box and audio is broken. To fix this, simply run [Laobamac's OCLP-Mod Patcher](https://github.com/laobamac/OCLP-Mod/releases) a second time once you have [fixed the Intel wireless card](https://github.com/jlempen/Surface-Laptop-3-OpenCore/tree/main?tab=readme-ov-file#enabling-the-intel-wireless-card-in-macos-sequoia-and-tahoe) and Internet is working again. The reason for this is that the OCLP-Mod Patcher needs to download the Kernel Development Kit from Apple's servers in order to reinstall the `AppleHDA.kext` on your macOS Tahoe partition and to do so, it needs a working Internet connection. 

## By installing the VoodooHDA audio driver
Another way to get back the internal speakers and microphone on macOS Tahoe is to install [SergeySlice's VoodooHDA audio driver](https://github.com/CloverHackyColor/VoodooHDA) with [chris1111's convenient VoodooHDA-Tahoe installer](https://github.com/chris1111/VoodooHDA-Tahoe). This has the added benefit of fixing the loud click you hear from your speakers when playing sound for the first time after waking your SL3 from hibernation.

Before installing the VoodooHDA driver, you need to disable the `AppleALC.kext` driver in your `config.plist` file under `Kernel -> Add` and reboot your computer.

Then [grab the latest installer](https://github.com/jlempen/Surface-Laptop-3-OpenCore/blob/main/Tools/VoodooHDA-Tahoe.pkg) from the Tools folder in my repository, launch the installer and follow the instructions.

Once you're back in macOS Tahoe after a reboot, head over to `System Settings -> Sound -> Output & Input` and select the `Output` tab, then select `Speaker (Analog)` as your sound output device.
</details>

<details>
  <summary>Enabling native HiDPI display settings in macOS</summary>
  
## Enabling native HiDPI display settings in macOS
On the installed macOS system, the default display resolution is less than ideal. To enable native HiDPI settings in the Display Preferences of macOS, download and run [xzhih's one-key-hidpi script](https://github.com/xzhih/one-key-hidpi) and select the option `(7) Manual input resolution`, then copy and paste the resolutions below into the terminal, press Enter and reboot for the changes to take effect. This will give you five sane preset resolutions for your Surface Laptop 3.

For the 13.5-Inch Surface Laptop 3:
```
2254x1504 1920x1280 1600x1066 1344x896 1280x854 1128x752
```

For the 15-Inch Surface Laptop 3:
```
2496x1664 2250x1500 2048x1366 1920x1280 1600x1066 1344x896 1280x854 1248x832
```

You may also download and install [BetterDisplay](https://github.com/waydabber/BetterDisplay) to change and manage the display resolutions on the Surface Laptop 3.
</details>

<details>
  <summary>Fixing broken Bluetooth on Wake from Hibernation</summary>
  
## Fixing broken Bluetooth on Wake from Hibernation
After the device wakes up from Hibernation, Bluetooth may be broken / unable to connect.

A very simple fix for this issue is to [download and install Bluesnooze](https://github.com/odlp/bluesnooze). Launch the app, enable `Launch at login` and you're done!
</details>

<details>
  <summary>Fixing broken Apple Messages and FaceTime on macOS Sonoma</summary>
  
## Fixing broken Apple Messages and FaceTime on macOS Sonoma
To fix issues with Apple Messages and FaceTime related to the [Intel Wireless driver](https://github.com/OpenIntelWireless/itlwm) on macOS Sonoma, disable all `AirportItlwm-***.kext` entries under `Kernel -> Add` in your `config.plist` file and use the [itlwm_v2.3.0_stable.kext.zip](https://github.com/OpenIntelWireless/itlwm/releases/download/v2.3.0/itlwm_v2.3.0_stable.kext.zip) and its companion app [HeliPort](https://github.com/OpenIntelWireless/HeliPort/releases/download/v1.5.0/HeliPort.dmg) instead.

The latest version 2.3.0 of `itlwm.kext` is already included in the Kext folder and `config.plist` file.
</details>

<details>
  <summary>Related repositories</summary>
  
## Related repositories
* https://github.com/jc-bao/surface-laptop3-ventura
* https://github.com/Xiashangning/BigSurface
* https://github.com/randomappleboi/Native-Wifi-for-Hackintoshes-with-Intel-Wireless-cards-on-macOS-sequoia
</details>
