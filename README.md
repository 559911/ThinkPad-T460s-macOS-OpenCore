# ThinkPad T460s running macOS 15.8.1 (OpenCore bootloader)

<img align="right" src="/Images/T460s-Ventura.jpg" alt="Lenovo Thinkpad T460s macOS Hackintosh OpenCore" width="300">

[![macOS](https://img.shields.io/badge/macOS-15.8.1-yellow)](https://developer.apple.com/documentation/macos-release-notes)
[![OpenCore](https://img.shields.io/badge/OpenCore-1.0.x-green)](https://github.com/acidanthera/OpenCorePkg)
[![Model](https://img.shields.io/badge/Model-20F9*-lightgrey)](https://psref.lenovo.com/Product/ThinkPad_T460s)
[![BIOS](https://img.shields.io/badge/BIOS-1.53-yellow)](https://pcsupport.lenovo.com/us/en/products/laptops-and-netbooks/thinkpad-t-series-laptops/thinkpad-t460s/downloads/driver-list/component?name=BIOS%2FUEFI)
[![License](https://img.shields.io/badge/license-MIT-purple)](/LICENSE)

**DISCLAIMER:**  
Read the entire README before you start.
The developers are not responsible for any damages you may cause.  
Should you find an error or improve anything, whether in the config or in the documentation, please consider opening an issue or pull request.

---

## Introduction

<details>  
<summary><strong>Getting started 📖</strong></summary>
</br>

**Meet the bootloader:**

- [Why OpenCore](https://dortania.github.io/OpenCore-Install-Guide/why-oc.html)
- Dortania's [website](https://dortania.github.io)

**Recommended tools:**

- Plist editor: [ProperTree](https://github.com/corpnewt/ProperTree)
- ESP mounting script: [MountEFI](https://github.com/corpnewt/MountEFI)
- Update OpenCore and kexts: [OCAuxiliaryTools](https://github.com/ic005k/OCAuxiliaryTools)

**Resources**

- [OpenCore](https://github.com/acidanthera/OpenCorePkg)
- [OC-little](https://github.com/daliansky/OC-little)
- [X1 Carbon config](https://github.com/tylernguyen/x1c6-hackintosh)
- [T460 config](https://github.com/MSzturc/Lenovo-T460-OpenCore)

</details>

<details>  
<summary><strong>Tested Hardware 💻</strong></summary>
</br>

| Model              | Thinkpad T460s 20F90002\*\*                                                                               |
| :----------------- | :-------------------------------------------------------------------------------------------------------- |
| Processor          | Core i5-6200U (2C, 2.4 / 3.0GHz, 3MB)                                                                     |
| Graphics           | Integrated Intel HD Graphics 520                                                                          |
| Memory             | 4GB Soldered + 8GB DIMM 2133MHz DDR4, dual-channel                                                        |
| Display            | 14" Full HD (1920x1080) IPS, Touch (read [Post-install > Enable Touchscreen](#enable-touchscreen))        |
| Storage            | Western Digital Black SN550 500GB NVMe SSD                                                                |
| Ethernet           | Intel Ethernet Connection I219-LM (Jacksonville)                                                          |
| WLAN + Bluetooth   | 11ac+BT, Intel Dual Band Wireless-AC 8260, 2x2 card                                                       |
| Camera             | HD720p resolution, low light sensitive, fixed focus                                                       |
| Audio support      | HD Audio, Realtek ALC3245 codec, stereo speakers 1Wx2, dual array microphone, combo audio/microphone jack |
| Keyboard           | 6-row, spill-resistant, multimedia Fn keys, LED backlight                                                 |
| Battery            | Front Li-Polymer 3-cell (23Wh) and rear Li-Ion 3-cell (26Wh), both Integrated                             |

</details>

<details>  
<summary><strong>Hardware compatibility 🧰</strong></summary>
</br>

This EFI will suit any T460s regardless of CPU model<sup>[1](#CPU)</sup>, amount of RAM, display resolution<sup>[2](#Res)</sup> and internal storage<sup>[3](#NVMe)</sup>.

<a name="CPU">1</a>. Optional custom CPU Power Management guide.  
<a name="Res">2</a>. 1440p displays should change `NVRAM -> Add -> 7C436110-AB2A-4BBB-A880-FE41995C9F82 -> UIScale`:`2` to get proper scaling while booting.  
<a name="NVMe">3</a>. Follow the NVMe fix guide below for NVMe drives.

This bootloader configuration will probably suit other 6th generation ThinkPads, but there could be some defects, such as not working USB ports or the inability to connect any displays. If you own a model other than a T460s, check out these repositories:

| Maintainer | Model | Bootloader |
| :------------ | ----------: | ---------: |
| MSzturc | [T460](https://github.com/MSzturc/Lenovo-T460-OpenCore) | OpenCore |
| duszmox | [X1 Carbon Gen 4](https://github.com/duszmox/ThinkPad-X1C4-macOS-OpenCore) | OpenCore |
| Tluck | [T560/T460](https://github.com/tluck/Lenovo-T460-Clover) | Clover |

</details>

---

## Installation

<details>  
<summary><strong>How to install macOS</strong></summary>
</br>

1. [Create an installation media](https://dortania.github.io/OpenCore-Install-Guide/installer-guide/#making-the-installer)
1. Download the [latest EFI folder](https://github.com/simprecicchiani/ThinkPad-T460s-macOS-OpenCore/releases) and copy it into the ESP partition
1. Change your BIOS settings according to the table below
1. Boot from the USB installer (press `F12` to choose boot volume) and [start the installation process](https://dortania.github.io/OpenCore-Install-Guide/installation/installation-process.html#booting-the-opencore-usb)

| Menu     |                   |                                 | Setting     |
| -------- | ----------------- | ------------------------------- | ----------- |
| Config   | USB               | UEFI BIOS Support               | `Enable`    |
|          | Power             | Intel SpeedStep Technology      | `Enable`    |
|          |                   | CPU Power Management            | `Enable`    |
|          | CPU               | Hyper-Threading Technology      | `Enable`    |
| Security | Security Chip     |                                 | `Disable`   |
|          | Memory Protection | Execution Prevention            | `Enable`    |
|          | Virtualization    | Intel Virtualization Technology | `Enable`    |
|          |                   | Intel VT-d Feature              | `Enable`    |
|          | Anti-Theft        | Computrace                      | `Disable`   |
|          | Secure Boot       |                                 | `Disable`   |
|          | Intel SGX         |                                 | `Disable`   |
|          | Device Guard      |                                 | `Disable`   |
| Startup  | UEFI/Legacy Boot  |                                 | `UEFI Only` |
|          | CSM Support       |                                 | `No`        |
|          | Boot Mode         |                                 | `Quick`     |

</details>

<details>  
<summary><strong>Enable Apple Services</strong></summary>
</br>

1. Run the following script in Terminal

```bash
git clone https://github.com/corpnewt/GenSMBIOS && cd GenSMBIOS && chmod +x GenSMBIOS.command && ./GenSMBIOS.command
```

2. Type `3` to Generate SMBIOS, then press ENTER
3. Type `MacBookPro15,4 5`, then press ENTER. Leave this Terminal window open.
4. Open `/EFI/OC/Config.plist` with any editor and navigate to `PlatformInfo -> Generic`
5. Add the script's last result to `MLB`, `SystemSerialNumber` and `SystemUUID`

```diff
<key>PlatformInfo</key>
<dict>
   <key>Generic</key>
   <dict>
      <key>AdviseFeatures</key>
      <false/>
      <key>MLB</key>
+     <string>M0000000000000001</string>
      <key>MaxBIOSVersion</key>
      <false/>
      <key>ProcessorType</key>
      <integer>1537</integer>
      <key>ROM</key>
+     <data>ESIzRFVm</data>
      <key>SpoofVendor</key>
      <true/>
      <key>SystemMemoryStatus</key>
      <string>Auto</string>
      <key>SystemProductName</key>
      <string>MacBookPro15,4</string>
      <key>SystemSerialNumber</key>
+     <string>W00000000001</string>
      <key>SystemUUID</key>
+     <string>00000000-0000-0000-0000-000000000000</string>
   </dict>
</dict>
```

6. Save and reboot the system

**Important:** The SMBIOS values in this repository are placeholders. Always generate your own. Never use someone else's `MLB`, `SystemSerialNumber`, `SystemUUID` or `ROM`.

</details>

<details>  
<summary><strong>How to update the bootloader</strong></summary>
</br>

1. Download the [latest release](https://github.com/simprecicchiani/ThinkPad-T460s-macOS-OpenCore/releases)
1. Copy and paste your `PlatformInfo`
1. Enable optional kexts if needed, such as NVMeFix and AirportItlwm
1. Test the new bootloader with a USB stick. Set `BootProtect: None` whenever booting with external drives
1. Customize boot preferences, such as skip picker and disable verbose
1. Mount your ESP partition
1. Backup your old EFI folder and replace it with the new one

</details>

---

## Post-install (optional)

<details id="enable-touchscreen">  
<summary><strong>Enable Touchscreen</strong></summary>
</br>

1. Open `/EFI/OC/Config.plist` with any editor
1. Add the content of [#touchscreen.plist](EFI/OC/%23touchscreen.plist)
1. Save and reboot the system

P.S. Tested on Big Sur, working with gestures  
https://youtu.be/-F0JAVIG92M

</details>

<details>  
<summary><strong>Enable Intel WLAN cards</strong></summary>
</br>

1. Open `/EFI/OC/Config.plist` with any editor
1. Add the content of [#intel-wlan.plist](/EFI/OC/%23intel-wlan.plist) according to your macOS version
1. Save and reboot the system

**Important for macOS 15.8.1:** To make Wi-Fi work, you must install **OCLP-Mod** and apply the root patches. After installing the driver patch and rebooting, Wi-Fi should work. Without OCLP-Mod root patches, Intel Wi-Fi may not work on macOS 15.8.1.

**Note:** The drivers provided in this repo are tested up to macOS 15.8.1 where possible. If you are running a different version of macOS, please use the corresponding [AirportItlwm.kext](https://github.com/OpenIntelWireless/itlwm/releases).

- If you have Bluetooth problems, please reference the [IntelBluetoothFirmware FAQ](https://openintelwireless.github.io/IntelBluetoothFirmware/FAQ.html#what-does-this-kext-do)

Optional: [Remove unnecessary firmware files from OpenIntelWireless drivers](/Guides/Clean-OpenIntelWireless.md).

</details>

<details>
<summary><strong>Enable non-natively supported Broadcom WLAN cards</strong></summary>
</br>

1. Download [AirportBrcmFixup](https://github.com/acidanthera/AirportBrcmFixup/releases) and [BrcmPatchRAM](https://github.com/acidanthera/BrcmPatchRAM/releases).
1. Copy `AirportBrcmFixup.kext`, `BrcmBluetoothInjector.kext`, `BrcmFirmwareData.kext` and `BrcmPatchRAM3.kext` to `/EFI/OC/Kexts`
1. Open `/EFI/OC/Config.plist` with any editor
1. Add the content of [#broadcom-wlan.plist](/EFI/OC/%23broadcom-wlan.plist)
1. Save and reboot the system

- If you have Bluetooth problems, please reference [AirportBrcmFixup](https://github.com/acidanthera/AirportBrcmFixup).

</details>

<details>  
<summary><strong>Fix NVMe power management</strong></summary>
</br>

1. Open `/EFI/OC/Config.plist` with any editor
1. Add the content of [#nvme-fix.plist](/EFI/OC/%23nvme-fix.plist)
1. Save and reboot the system

</details>

<details>  
<summary><strong>Custom CPU Power Management</strong></summary>
</br>

1. Run the following script in Terminal

```bash
git clone https://github.com/corpnewt/CPUFriendFriend; cd CPUFriendFriend; chmod +x ./CPUFriendFriend.command; ./CPUFriendFriend.command
```

1. When asked, select preferred values
1. From the pop-up window, copy `ssdt_data.aml` into `/EFI/OC/ACPI/` folder. Rename it if you want
1. Open `/EFI/OC/Config.plist` with any editor
1. Add the content of [#cpu-pm.plist](/EFI/OC/%23cpu-pm.plist). Make sure `SSDT-PLUG.aml` is disabled and match your new SSDT filename
1. Save and reboot the system

</details>

<details>  
<summary><strong>ThinkPad Dock USB ports mapping</strong></summary>
</br>

I have never had one, so there is a chance something might not be working. [USB mapping guide](https://dortania.github.io/OpenCore-Post-Install/usb/).

</details>

---

## Other tweaks

<details>  
<summary><strong>Enable HiDPI</strong></summary>
</br>

1. [Disable SIP](https://dortania.github.io/OpenCore-Install-Guide/troubleshooting/troubleshooting.html#disabling-sip)
1. Run the following script in Terminal
   ```bash
   bash -c "$(curl -fsSL https://raw.githubusercontent.com/xzhih/one-key-hidpi/master/hidpi.sh)"
   ```
1. Follow the instructions, then reboot
1. Re-enable SIP if desired

[Alternative method](https://github.com/bbhardin/A-Guide-to-MacOS-Scaled-Resolutions)

</details>

<details>  
<summary><strong>Enable multimedia keys, fan and LEDs control</strong></summary>
</br>

1. Download and install [YogaSMC-App-Release.dmg](https://github.com/zhen-zen/YogaSMC/releases), both the pref-panel and the app itself
1. Open the app
1. Check the `launch on login` option

</details>

<details>  
<summary><strong>Use PrtSc key as Screenshot shortcut</strong></summary>
</br>

Super useful shortcut that I wish I had on my previous MBP. Default is `Command + Shift + 5`.

1. Open SystemPreferences.app
1. Go under `Keyboard > Shortcuts > Screenshots`
1. Click on `Screenshot and recording options` field
1. Press `PrtSc` on your keyboard. It should come out as `F13`

</details>

<details>  
<summary><strong>Use calibrated display profile</strong></summary>
</br>

NotebookCheck's calibrated profiles. Not all panels are the same, so the final result may vary.

1. Run one of the following scripts in Terminal
   - for 1440p displays
     ```bash
     cd ~/Library/ColorSync/Profiles; wget https://github.com/simprecicchiani/ThinkPad-T460s-macOS-OpenCore/raw/master/Files/DisplayColorProfiles/T460s_WQHD_VVX14T058J02.icm
     ```
   - for 1080p displays
     ```bash
     cd ~/Library/ColorSync/Profiles; wget https://github.com/simprecicchiani/ThinkPad-T460s-macOS-OpenCore/raw/master/Files/DisplayColorProfiles/T460s_FHD_N140HCE_EAA.icm
     ```
2. Go under `SystemPreferences > Displays > Colour`
3. Select the profile

<img src="/Images/display-profile.png" alt="Lenovo Thinkpad T460s macOS Hackintosh OpenCore" height="300">

</details>

<details>
<summary><strong>Add Apple Watch authentication to sudo</strong></summary>
</br>

If you have an Apple Watch and you already [replaced the built-in WiFi card](/Guides/Replace-WLAN.md), you can enable authenticating as sudo with your Apple Watch using [pam-watch](https://github.com/biscuitehh/pam-watchid).

1. Download the latest [ZIP file](https://github.com/biscuitehh/pam-watchid/archive/main.zip)
2. Unzip it. By default it creates a folder called `pam-watchid-main`
3. Open Terminal and install it:

   - `$ cd ~/Downloads/pam-watchid-main`
   - `$ sudo make install`

4. Register the new PAM module for sudo:

   - Edit `/etc/pam.d/sudo`
   - Add a new line under line 1, which is a comment, containing:
     ```bash
     auth sufficient pam_watchid.so
     ```

That is it. Now, whenever you use sudo, you have the option of using your Watch to authenticate.

<img src="/Images/AW-sudo.png" alt="Apple Watch authenticating with sudo" height="300">

</details>

<details>  
<summary><strong>Monitor temperatures and power consumption</strong></summary>
</br>

1. Download and install [HWMonitor](https://github.com/kzlekk/HWSensors/releases)
1. Check `launch on login` if desired

</details>

<details>  
<summary><strong>Faster macOS dock animation</strong></summary>
</br>

This enables auto-hide and speeds up the animation.

1. Run the following script in Terminal
   ```bash
   defaults write com.apple.dock autohide-delay -float 0; defaults write com.apple.dock autohide-time-modifier -float 0.5; killall Dock
   ```

</details>

<details>  
<summary><strong>Boot process tweaks</strong></summary>
</br>

| Menu |       |            | Setting    | What does it do?     |
| :--- | :---- | :--------- | :--------- | :------------------- |
| Misc | Boot  | ShowPicker | `False`    | Skip bootloader page |
| UEFI | Audio | PlayChime  | `Disabled` | Always silent boot   |

</details>

<details>  
<summary><strong>Setup Hibernatemode and Sleep at low Battery script</strong></summary>
</br>

<a href="https://www.tonymacx86.com/threads/release-sleeponlowbattery-solb.264785">Script that performs auto sleep/hibernate at low battery</a>

1. Open Terminal
2. Enter the commands below one by one

Settings for AC:

```
sudo pmset -c standby 1
sudo pmset -c hibernatemode 0
```

Settings for battery:

```
sudo pmset -b standby 1
sudo pmset -b standbydelayhigh 900
sudo pmset -b standbydelaylow 60
sudo pmset -b hibernatemode 25
sudo pmset -b highstandbythreshold 70
```

Settings for all:

```
sudo pmset -a acwake 0
sudo pmset -a lidwake 1
sudo pmset -a powernap 0
```

To restore default system settings, run `pmset restoredefaults`.

<details>  
<summary><strong>Commands description</strong></summary>

`acwake` - wake the machine when power source, AC or battery, is changed. Value = 0/1

`lidwake` - wake the machine when the laptop lid or clamshell is opened. Value = 0/1

`powernap` - enable or disable Power Nap on supported machines. Value = 0/1

`standbydelayhigh` and `standbydelaylow` specify the delay, in seconds, before writing the hibernation image to disk and powering off memory for Standby. `standbydelayhigh` is used when the remaining battery capacity is above `highstandbythreshold`, which has a default value of 50 percent. `standbydelaylow` is used when the remaining battery capacity is below `highstandbythreshold`.

`hibernatemode` supports values of 0, 3, or 25.

To disable hibernation, set `hibernatemode` to 0.

`hibernatemode` = 0 by default on desktops. The system will not back memory up to persistent storage. The system must wake from the contents of memory. The system will lose context on power loss.

`hibernatemode` = 3 by default on portables. The system will store a copy of memory to persistent storage, the disk, and will power memory during sleep. The system will wake from memory, unless a power loss forces it to restore from the hibernation image.

`hibernatemode` = 25 is only settable via pmset. The system will store a copy of memory to persistent storage, the disk, and will remove power to memory. The system will restore from the disk image. If you want hibernation, slower sleeps, slower wakes, and better battery life, you should use this setting.

[pmset Descriptions Source](https://www.dssw.co.uk/reference/pmset.html)

</details>

</details>

<details>  
<summary><strong>BIOS Mod</strong></summary>
</br>

I know it can be scary at first, but with the right amount of carefulness anyone can do it.  
Is it worth the effort and risk? I do not think so. Did I enjoy it? 100%.  
A [brief guide referencing other guides](/Guides/Bios-Mod.md).

</details>

---

## Status

<details>  
<summary><strong>What's working ✅</strong></summary>
</br>

- [x] CPU Power Management `~1W on IDLE`
- [x] Intel HD Graphics 520 / 620 `including graphics acceleration`  
  **Note:** Check the `DeviceProperties` section below. T460s usually uses HD 520 (`0x1916` / `0x191b0000`), not HD 620 (`0x5916` / `0x591b0000`).
- [x] USB ports
- [x] Internal camera `working fine on FaceTime, Skype, Zoom and others`
- [x] Sleep / Hibernatemode `25 or 3` / Wake / Shutdown / Reboot
- [x] Intel Gigabit Ethernet
- [x] WiFi, Bluetooth, AirDrop, Handoff, Continuity, Sidecar wireless `some functionalities may be buggy or broken on Intel WLAN cards`  
  **Note:** On macOS 15.8.1, Wi-Fi requires **OCLP-Mod** and root patches. After installing the driver patch and rebooting, Wi-Fi should work.
- [x] iMessage, FaceTime, App Store, iTunes Store `Please generate your own SMBIOS`
- [x] Speakers and headphones combo jack
- [x] Batteries
- [x] Keyboard map and hotkeys with [YogaSMC](https://github.com/zhen-zen/YogaSMC)
- [x] Touchscreen
- [x] [Trackpad, Trackpoint and physical buttons](/Images/VoodooRMI-T460s-trackpad-gestures.gif) `all macOS gestures working thanks to VoodooRMI`
- [x] SIP and FileVault 2 can be turned on
- [x] HDMI `with digital audio passthrough`
- [x] SD Card Reader `slow r/w speed but works`

</details>

<details>  
<summary><strong>What's not working ⚠️</strong></summary>
</br>

- [ ] Some users reported Mini DisplayPort is broken for them with latest updates, but it is working for me just fine
- [ ] Safari DRM `Use Chromium engine to watch Apple TV+, Amazon Prime Video, Netflix and others`
- [ ] WWAN, needs to be implemented
- [ ] Fingerprint Reader
- [ ] Bluetooth. You can enable Bluetooth in the config.plist, but it may cause the "volume hash mismatch" problem. Waiting for a solution.
- [ ] Touchpad above three buttons not working: left, middle, right

</details>

<details>  
<summary><strong>Update tracker 🔄</strong></summary>
</br>

| [EFI Release](https://github.com/simprecicchiani/ThinkPad-T460s-macOS-OpenCore/releases)       | Your EFI release |
|------------------------------------------------------------------------------------------------|------------------|
| [macOS](https://www.apple.com/macos/)                                                          | 15.8.1           |
| [OpenCore](https://github.com/acidanthera/OpenCorePkg/releases)                                | 1.0.x            |
| [Lilu](https://github.com/acidanthera/Lilu/releases)                                           | 1.6.7            |
| [VirtualSMC](https://github.com/acidanthera/VirtualSMC/releases)                               | 1.3.2            |
| [SMCBatteryManager](https://github.com/acidanthera/VirtualSMC/releases)                        | 1.3.2            |
| [SMCProcessor](https://github.com/acidanthera/VirtualSMC/releases)                             | 1.3.2            |
| [YogaSMC](https://github.com/zhen-zen/YogaSMC/releases)                                        | 1.5.3            |
| [WhateverGreen](https://github.com/acidanthera/WhateverGreen/releases)                         | 1.6.6            |
| [AppleALC](https://github.com/acidanthera/AppleALC/releases)                                   | 1.8.5            |
| [VoodooPS2Controller](https://github.com/acidanthera/VoodooPS2/releases)                       | 2.3.5            |
| [VoodooInput](https://github.com/acidanthera/VoodooPS2/releases)                               | 1.1.4            |
| [IntelMausi](https://github.com/acidanthera/IntelMausi/releases)                               | 1.0.7            |
| [HibernationFixup](https://github.com/acidanthera/HibernationFixup/releases)                   | 1.4.9            |
| [NVMeFix](https://github.com/acidanthera/NVMeFix/releases)                                     | 1.1.1            |
| [RTCMemoryFixup](https://github.com/acidanthera/RTCMemoryFixup/releases)                       | 1.0.7            |
| [AirportItlwm](https://github.com/OpenIntelWireless/itlwm/releases)                            | 2.3.0            |
| [IntelBluetoothFirmware](https://github.com/OpenIntelWireless/IntelBluetoothFirmware/releases) | 2.3.0            |
| [IntelBTPatcher](https://github.com/OpenIntelWireless/IntelBluetoothFirmware/releases)         | 2.3.0            |
| [BlueToolFixup](https://github.com/acidanthera/BrcmPatchRAM/releases)                          | 2.6.8            |
| [AppleBacklightSmoother](https://github.com/hieplpvip/AppleBacklightSmoother/releases)         | 1.0.2            |
| [BrightnessKeys](https://github.com/acidanthera/BrightnessKeys/releases)                       | 1.0.3            |
| [RealtekCardReader](https://github.com/0xFireWolf/RealtekCardReader/releases)                  | 0.9.7            |
| [RealtekCardReaderFriend](https://github.com/0xFireWolf/RealtekCardReaderFriend/releases)      | 1.0.4            |
| [FeatureUnlock](https://github.com/acidanthera/FeatureUnlock/releases)                         | 1.1.5            |

</details>

---

## EFI Configuration Summary (based on current config.plist)

<details>  
<summary><strong>ACPI</strong></summary>
</br>

### ACPI -> Add

- `SSDT-XDSM.aml`: `_DSM` replacement for non macOS
- `SSDT-XHCI.aml`: USB map fixup
- `SSDT-DTGP.aml`: DTGP method
- `SSDT-PLUG.aml`: X86 Injector / CPU power management
- `SSDT-VDEV.aml`: Virtual devices for macOS
- `SSDT-PMCR.aml`: PPMC to PMCR
- `SSDT-PNLF.aml`: Notebook brightness fixup
- `SSDT-HRTF.aml`: HPET, TIMR and RTC fixup
- `SSDT-KBRD.aml`: ThinkPad T460s Keyboard map
- `SSDT-PTWK.aml`: Shutdown, sleep-wake fixup
- `SSDT-BATX.aml`: Battery fixes
- `SSDT-EC.aml`: EC RW
- `SSDT-DYTC.aml`: Initialize system variables
- `SSDT-THINK.aml`: ThinkSMC

### ACPI -> Patch

- `_DSM` to `XDSM` (ALL)
- `_UPC` to `XUPC` (XHCI)
- HPET `\WNTF\!WXPF` to `_OSI("Darwin")`
- `_PRW` (0x6d, 0x04) to (0x6d, 0) (IGBE)
- `_PTS` to `ZPTS(1,N)`
- `_WAK` to `ZWAK(1,N)`
- `\_SB..EC.HWAC` to `\_SB..EC.WACH`
- BATX: `Notify(BAT0, xx)` to `BATX`
- BATX: `Notify(BAT1, xx)` to `BATX`

</details>

<details>  
<summary><strong>Kernel</strong></summary>
</br>

### Kernel -> Add

| Kext | Version | Description |
| :--- | :--- | :--- |
| Lilu.kext | 1.6.7 | Patch engine |
| VirtualSMC.kext | 1.3.2 | SMC emulator |
| WhateverGreen.kext | 1.6.6 | Graphics patches |
| AppleALC.kext | 1.8.5 | Audio patches |
| IntelMausi.kext | 1.0.7 | Intel Ethernet LAN |
| SMCBatteryManager.kext | 1.3.2 | Battery SMC plugin |
| SMCProcessor.kext | 1.3.2 | Processor SMC plugin |
| YogaSMC.kext | 1.5.3 | Fan, LED, hotkeys |
| HibernationFixup.kext | 1.4.9 | Hibernation fix |
| RTCMemoryFixup.kext | 1.0.7 | RTC offset fix |
| AppleBacklightSmoother.kext | 1.0.2 | Smooth backlight transition |
| BrightnessKeys.kext | 1.0.3 | Brightness keys |
| AirportItlwm.kext | 2.3.0 | Intel WLAN |
| IntelBluetoothFirmware.kext | 2.3.0 | Intel Bluetooth firmware |
| BlueToolFixup.kext | 2.6.8 | Bluetooth fix |
| NVMeFix.kext | 1.1.1 | NVMe power management |
| RealtekCardReader.kext | 0.9.7 | Realtek card reader |
| RealtekCardReaderFriend.kext | 1.0.4 | Card reader helper |
| VoodooPS2Controller.kext | 2.3.5 | PS/2 keyboard and trackpad |
| VoodooInput.kext | 1.1.4 | Voodoo input |
| VoodooPS2Keyboard.kext | 2.3.5 | PS/2 keyboard |
| VoodooPS2Trackpad.kext | 2.3.5 | PS/2 trackpad |
| IntelBTPatcher.kext | 2.3.0 | Intel Bluetooth patch |
| FeatureUnlock.kext | 1.1.5 | Disabled by default |

### Kernel -> Force

- `IO80211Family.kext`
- `IO80211Family.kext/Contents/PlugIns/AirPortBrcmNIC.kext`
- `IO80211Family.kext/Contents/PlugIns/IO80211NetBooter.kext`

### Kernel -> Quirks key items

- `AppleXcpmCfgLock`: true
- `CustomSMBIOSGuid`: true
- `DisableLinkeditJettison`: true
- `PanicNoKextDump`: true
- `PowerTimeoutKernelPanic`: true
- `SetApfsTrimTimeout`: -1
- `XhciPortLimit`: false

</details>

<details>  
<summary><strong>DeviceProperties</strong></summary>
</br>

The current config injects the following main PCI device properties:

- `PciRoot(0x0)/Pci(0x0,0x0)`: Sky Lake Host Bridge / DRAM Registers
- `PciRoot(0x0)/Pci(0x14,0x0)`: Sunrise Point-LP USB 3.0 xHCI Controller
- `PciRoot(0x0)/Pci(0x14,0x2)`: Sunrise Point-LP Thermal subsystem
- `PciRoot(0x0)/Pci(0x16,0x0)`: Sunrise Point-LP CSME HECI #1
- `PciRoot(0x0)/Pci(0x17,0x0)`: Sunrise Point-LP SATA Controller [AHCI mode]
- `PciRoot(0x0)/Pci(0x1C,0x0)`: Sunrise Point-LP PCI Express Root Port #1
- `PciRoot(0x0)/Pci(0x1C,0x0)/Pci(0x0,0x0)`: RTS522A PCI Express Card Reader
- `PciRoot(0x0)/Pci(0x1C,0x2)`: Sunrise Point-LP PCI Express Root Port #3
- `PciRoot(0x0)/Pci(0x1C,0x2)/Pci(0x0,0x0)`: Wireless 8260
- `PciRoot(0x0)/Pci(0x1F,0x0)`: Sunrise Point-LP LPC Controller
- `PciRoot(0x0)/Pci(0x1F,0x2)`: Sunrise Point-LP PMC
- `PciRoot(0x0)/Pci(0x1F,0x3)`: 100 Series/C230 Series HD Audio Controller
- `PciRoot(0x0)/Pci(0x1F,0x4)`: Sunrise Point-LP SMBus
- `PciRoot(0x0)/Pci(0x1F,0x6)`: Ethernet Connection I219-LM
- `PciRoot(0x0)/Pci(0x2,0x0)`: Graphics properties

The graphics section currently uses:

- `AAPL,ig-platform-id`: `AAAbWQ==`, which is `0x591b0000`
- `device-id`: `FlkAAA==`, which is `0x5916`
- `model`: Intel HD Graphics 620
- `framebuffer-patch-enable`: 1
- `framebuffer-pipecount`: 3
- `framebuffer-portcount`: 3
- `framebuffer-con1-enable`: 1
- `framebuffer-con1-type`: `AAgAAA==`
- `framebuffer-con2-enable`: 1
- `framebuffer-con2-type`: `AAgAAA==`
- `enable-hdmi20`: 1
- `force-online`: 1

**Important note:**  
If this is a T460s, the CPU is usually i5-6200U / i5-6300U / i7-6600U, and the iGPU is **HD Graphics 520**, not HD 620. Common HD 520 properties are:

```text
AAPL,ig-platform-id = 0x191b0000  // or 0x19160000
device-id           = 0x1916
model               = Intel HD Graphics 520
```

The current config uses HD 620 / `0x5916` / `0x591b0000`, which may have been copied from a T470s or another Kaby Lake configuration. If you encounter graphics acceleration issues, abnormal VRAM, or no HDMI output on a T460s, fix this first.

The audio section currently uses:

- `layout-id`: `HQAAAA==`, which is `29`
- `device-id`: `cJ0AAA==`, which is `0x9d70`
- `hda-gfx`: `onboard-1`
- `model`: 100 Series/C230 Series Chipset Family HD Audio Controller

</details>

<details>  
<summary><strong>NVRAM / PlatformInfo / UEFI</strong></summary>
</br>

### NVRAM -> Add

- `boot-args`: `keepsyms=1`
- `csr-active-config`: `AAAAAA==`, which means SIP is fully enabled
- `prev-lang:kbd`: `en-US:0`
- `run-efi-updater`: `No`
- `UIScale`: `1`
- `DefaultBackgroundColor`: `AAAAAA==`
- `rtc-blacklist`: set
- `SystemAudioVolume`: `Rg==`

**Note:** The current EFI has SIP enabled by default. If you want to run HiDPI scripts or modify system files, temporarily disable SIP first.

### PlatformInfo -> Generic

- `SystemProductName`: MacBookPro15,4
- `ProcessorType`: 1537
- `SpoofVendor`: true
- `SystemMemoryStatus`: Auto
- `UpdateSMBIOSMode`: Custom
- `CustomSMBIOSGuid`: true
- `Automatic`: true

**Always generate your own `MLB`, `SystemSerialNumber`, `SystemUUID`, and `ROM` with GenSMBIOS.**

### UEFI -> Drivers

- `HfsPlus.efi`
- `OpenRuntime.efi`
- `OpenCanopy.efi`
- `AudioDxe.efi`

### UEFI -> Audio / Output / Input

- `AudioSupport`: true
- `AudioDevice`: `PciRoot(0x0)/Pci(0x1f,0x3)`
- `PlayChime`: Disabled
- `ProvideConsoleGop`: true
- `Resolution`: Max
- `TextRenderer`: BuiltinGraphics
- `UIScale`: -1
- `KeySupport`: true
- `KeySupportMode`: Auto
- `PointerSupport`: false

### UEFI -> ReservedMemory

- Address: 569344
- Size: 4096
- Type: Reserved
- Comment: Fix black screen on wake from hibernation for Lenovo ThinkPad T480/T490/X1C6/T460s/?

### Booter Quirks key items

- `AvoidRuntimeDefrag`: true
- `DiscardHibernateMap`: true
- `EnableSafeModeSlide`: true
- `EnableWriteUnprotector`: true
- `ProvideCustomSlide`: true
- `SetupVirtualMap`: true
- `SyncRuntimePermissions`: true

### Misc key items

- `HibernateMode`: NVRAM
- `HideAuxiliary`: true
- `ShowPicker`: false
- `Timeout`: 3
- `PickerMode`: External
- `PickerVariant`: Acidanthera\GoldenGate
- `PollAppleHotKeys`: true
- `ScanPolicy`: 0
- `SecureBootModel`: Default
- `Vault`: Optional

</details>

---

## Performances

<details>  
<summary><strong>Power consumption and thermals ❗Not tested on my T460s (6200U) 🔥</strong></summary>
</br>

| Idle State                | Max Frequency                 | 2 Thread Frequency            | All Thread Frequency          | GPU Max Frequency             |
| ------------------------- | ----------------------------- | ----------------------------- | ----------------------------- | ----------------------------- |
| ![](/Images/ipg-idle.png) | ![](/Images/ipg-max-freq.png) | ![](/Images/ipg-two-freq.png) | ![](/Images/ipg-all-freq.png) | ![](/Images/ipg-gpu-freq.png) |

</details>

<details>  
<summary><strong>Benchmarks ⏱</strong></summary>
</br>

| CPU            | Single-Core | Multi-Core |
| :------------- | ----------: | ---------: |
| Geekbench 5    |         730 |       1611 |
| **GPU**        |  **OpenCL** |  **Metal** |
| Geekbench 5    |        4097 |       4179 |

<small>macOS 12.3.1, EFI release 0.8.0, CPU: 6200U</small>

</details>

---

## Thanks to

- [simprecicchiani](https://github.com/simprecicchiani)
- The hackintosh community on GitHub
- [InsanelyMac](https://www.insanelymac.com/forum/)
- [r/hackintosh](https://www.reddit.com/r/hackintosh/)
