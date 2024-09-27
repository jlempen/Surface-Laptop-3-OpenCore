![OpenCore logo](https://github.com/acidanthera/OpenCorePkg/raw/master/Docs/Logos/OpenCore_with_text_Small.png)

# Surface-Laptop-3-OpenCore
macOS on the Microsoft Surface Laptop 3 thanks to [Acidanthera's OpenCore bootloader](https://github.com/acidanthera/OpenCorePkg).

## Abstract
Apart from ACPI S3 Sleep which is broken, everything on the `Intel 13.5-Inch and 15-Inch Surface Laptop 3` is working perfectly like on a real Mac. ACPI S4 Hibernate works great, though, and resuming from Hibernation takes around ten to fifteen seconds. The advantage Hibernate has over Sleep is that the device doesn't consume any power while in a hibernated state.

The battery runtime is around five hours.

## Disclaimer
This repository is neither a howto nor an installation manual. Using these files requires at least basic knowledge of [Acidanthera's OpenCore bootloader](https://github.com/acidanthera/OpenCorePkg), ACPI, UEFI and the art of hackintoshing in general. I recommend reading the excellent [Dortania's OpenCore Install Guide](https://dortania.github.io/OpenCore-Install-Guide), as well as all its linked resources. For those who wish to improve their hackintoshing knowledge, [5T33Z0's OC-Little-Translated](https://github.com/5T33Z0/OC-Little-Translated) repository is the most comprehensive resource I've found on the subject.

## Recommendations
I recommend completely erasing the device's SSD by creating a new GPT partition table before attempting to install macOS, as it makes the installation process much easier. You may use any Linux live ISO with a partitioning tool such as `GParted` or `KPartition` to erase the SSD.

For macOS to be able to boot on the Surface Laptop 3, the `Secure Boot` option _**must be disabled**_ in the UEFI. The boot screen will then display a large red bar with a lock icon at the top of the display when Secure Boot is disabled. This is normal.

Please be aware that all `PlatformInfo` and `SMBIOS` information was removed from the OpenCore `config.plist` file. Users will therefore need to generate their own `PlatformInfo` with [CorpNewt's GenSMBIOS tool](https://github.com/corpnewt/GenSMBIOS) before attempting to boot a Surface Laptop 3 with this repository's EFI folder.

`BigSurfaceSLB3.kext` is required to enable the trackpad, the keyboard, the touchscreen, the battery status, the power and volume buttons and the ambient light sensor. Updating this `kext` with the official one from [Xiashangning's BigSurface repo](https://github.com/Xiashangning/BigSurface) will most certainly break something! `BigSurfaceSLB3.kext` is a modified version of `BigSurface` v6.5 which fixes the skipping/lagging trackpad and hibernate mode 25.

An UEFI firmware released by Micro$oft in August 2023 broke the Surface Laptop 3's trackpad in macOS in several ways. Downgrading the UEFI firmware to the last known working version `13.101.140.0` is required to fix the trackpad and hibernate mode 25 on macOS. See the detailed instructions below to easily downgrade the UEFI firmware. This needs to be done before installing any version of macOS or you won't have a working trackpad and/or keyboard during installation.

`AirportItlwm-Ventura.kext`, `AirportItlwm-Sonoma140.kext` and `AirportItlwm-Sonoma144.kext` from the [OpenIntelWireless repo](https://github.com/OpenIntelWireless/itlwm) are required to enable the Wifi chip. This EFI will dynamically load the appropriate kext for macOS Ventura or Sonoma depending on the running kernel. No need to manually replace the kext file when updating your version of macOS. As the Intel Wifi chip does not yet work with the `AirportItlwm.kext` in macOS Sequoia, you'll need to use the `Itlwm.kext` and its companion app [HeliPort](https://github.com/OpenIntelWireless/HeliPort/releases) to connect to a Wifi network. You'll find the latest stable `HeliPort.dmg` in the [Tools folder](https://github.com/jlempen/Surface-Laptop-3-OpenCore/blob/main/Tools/HeliPort.dmg) of this repo. This EFI will dynamically load the `Itlwm.kext` instead of `AirportItlwm.kext` when you boot into macOS Sequoia.

Windows and Linux should be detected automagically by the OpenCore boot loader even when installed after macOS.

<details>
  <summary>Software Specifications</summary>
  
## Software Specifications
| Software         | Version                            |
| ---------------- | ---------------------------------- |
| Target OS        | Apple macOS 15 Sequoia, 14 Sonoma and 13 Ventura |
| OpenCore         | [OC v1.0.1](https://github.com/acidanthera/OpenCorePkg/releases/download/1.0.1/OpenCore-1.0.1-RELEASE.zip) |
| SMBIOS           | MacBookPro16,2 |
| SSD format       | APFS file system, GPT partition table |
</details>

<details>
  <summary>Computer Specifications</summary>
  
## Computer Specifications
| Device           | Hardware                           |
| ---------------- | ---------------------------------- |
| CPU              | Intel Core i7-1065G7 or Intel Core i5-1035G7 |
| iGPU             | Intel Iris Plus Graphics |
| Audio            | Realtek ALC |
| RAM              | 8 or 16 GB RAM |
| Wifi + Bluetooth | Wifi6 AX200, Bluetooth 5.0 |
| Storage          | Kioxia/Toshiba/SK Hynix PCIe NVMe 2230 SSD |
| USB Type-C 3.1 Gen 1 | Supports Power Delivery and DisplayPort |
| Camera | 720p |
| IR camera | Intel(R) AVStream Camera 2500, ISP Interface |
| Keyboard / Trackpad | Microsoft Type Cover |
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
- [x] Touchscreen
- [x] Surface Pen
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
- [ ] Sleep (hibernatemode 3) - the device only turns off the display without sleeping
- [ ] On macOS Sequoia, the user needs to close and open the lid again to wake up the display after hibernation
</details>

<details>
  <summary>What will probably never work</summary>
  
## What will probably never work
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
2. Download and unzip the compressed firmware archive [SL3_FW13.101.140.0.zip](https://github.com/jlempen/Surface-Laptop-3-OpenCore/blob/main/UEFI%20Firmware/SL3_FW13.101.140.0.zip) from this repository.
3. Add the line `OnlyTrusted=false` to the `/etc/fwupd/daemon.conf` config file.
4. Open a terminal and navigate to the folder where you extracted the firmware files.
5. Connect your Surface device to a power supply.
6. Copy the following lines and paste them into the terminal:
```
for f in *; do 
  sudo fwupdmgr install --allow-older --allow-reinstall --no-reboot-check "$f"
done
```
7. Close the terminal and reboot the computer.

For some firmware files, the `fwupdmgr` tool may complain that it is unable to find a matching device. This is normal, as not all Surface Laptop 3 models use the exact same hardware, thus the compressed firmware archive contains all the required files for all models.

The Surface Laptop 3 will reboot and downgrade all UEFI firmwares at once, which takes around 10 minutes. You'll see progress bars with different colours depending on which type of firmware is being flashed.
Once the process is done, your laptop will restart a few times and seem to hang on the Surface logo for 20 or 30 seconds each time, this is normal. Then it will restart for good to your OpenCore picker.

Now restart while holding the F4/Volume Up key to check the firmware version in the UEFI. In the Firmware section, `System UEFI` should now show `13.101.140`.

Reboot and you're done.

If you are using Windows on the laptop, you'll have to find a way to prevent Windows Update from updating the firmware to the latest version again! I don't know how to do that, but DuckDuckGo is your friend.
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
</details>

<details>
  <summary>Fix broken Bluetooth on Wake from Hibernation</summary>
  
## Fix broken Bluetooth on Wake from Hibernation
After the device wakes up from Hibernation, Bluetooth may be broken / unable to connect.

A very simple fix for this issue is to [download and install Bluesnooze](https://github.com/odlp/bluesnooze). Launch the app, enable `Launch at login` and you're done!
</details>

<details>
  <summary>Enabling native HiDPI display settings in macOS</summary>
  
## Enabling native HiDPI display settings in macOS
I recommend downloading and installing [BetterDisplay](https://github.com/waydabber/BetterDisplay) to change and manage the display resolutions on the Surface Laptop 3.
</details>

<details>
  <summary>Fixing broken Apple Messages and FaceTime</summary>
  
## Fixing broken Apple Messages and FaceTime
To fix issues with Apple Messages and FaceTime related to the [Intel Wireless driver](https://github.com/OpenIntelWireless/itlwm) on macOS Sonoma, disable all `AirportItlwm-***.kext` entries under `Kernel -> Add` in your `config.plist` file and use the [itlwm_v2.3.0_stable.kext.zip](https://github.com/OpenIntelWireless/itlwm/releases/download/v2.3.0/itlwm_v2.3.0_stable.kext.zip) and its companion app [HeliPort](https://github.com/OpenIntelWireless/HeliPort/releases/download/v1.5.0/HeliPort.dmg) instead.
The latest version 2.3.0 of itlwm.kext is already included in the Kext folder and `config.plist` file.
</details>

<details>
  <summary>Related repositories</summary>
  
## Related repositories
* https://github.com/jc-bao/surface-laptop3-ventura
</details>
