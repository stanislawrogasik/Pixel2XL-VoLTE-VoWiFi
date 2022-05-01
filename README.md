# Introduction

This guide allows you to enable VoLTE and VoWiFi on Pixel 2 XL with Android 12(probably also future androids and previous versions).

It uses:

- [EFS Tools](https://github.com/JohnBel/EfsTools),

- [Qualcom Drivers via QPST](https://qpsttool.com/qpst-tool-v2-7-496),

- [ADB - Platform Tools with proper Google drivers](https://developer.android.com/studio/releases/platform-tools)

- Magisk

- Few Magisk modules(because I like to have multiple modules instead of one),

- Windows(tested on Windows 10 - unfortunately EFS tools doesn't work with Linux),

- MBN file from another device(e.g. OnePlus 5/Xiaomi Mi6) and one file which enables the uploaded MBN,

# Test cases
Tested on ArrowOS12 with Pixel 2XL - Poland Orange - used Xiaomi Mi6 MBN from "group_no"


# Thanks
Big thanks to [Kars88 from XDA](https://forum.xda-developers.com/m/kars88.1417781/) as this guide wouldn't exist without him :)

# Steps
1. Download whole repo or download proper files
2. Install QPST from link above
3. Download Magisk Modules to your device(from **MagiskModules/** folder on GitHub)
4. Download file called **mcfg_autoselect_by_uim** from "**MBN/**"
5. Download your MBN from repo(all MBNs from Xiaomi Mi6 and OnePlus 5 in **MBN/** ) - you need to select a proper MBN for your provider
6. Put your **MBN** and **MCFG_autoselect_by_uim** to EfsTools folder
7. Flash both modules and reboot device
8. Connect your device to the PC
9. Launch powershell/CMD and go into EFSTools folder, launch this command:
```
EfsTools.exe efsInfo
```
If you see information from your device you can skip to step **13**.

If not(or you see an error) don't skip.

10. Launch **adb shell** and those commands:
```
su

resetprop ro.bootmode usbradio

resetprop ro.build.type userdebug

resetprop sys.usb.configfs 1

resetprop sys.usb.config diag,serial_cdev,rmnet_gsi,adb

diag_mdlog
```

If diag_mdlog complains with another process already launched kill it with:

```
kill -9 <PID>
```

then launch **diag_mdlog**

12. You should see your device with `efsTools.exe efsInfo` if not open an issue and I'll try to help or try to reboot device. 
13. Launch those commands in CMD with EfsTools:
```
EfsTools.exe writeFile -i mcfg_autoselect_by_uim -o /nv/item_files/mcfg/mcfg_autoselect_by_uim

EfsTools.exe uploadDirectory -i mcfg_sw.mbn -o / -v
```
Your MBN should be placed in your device(no errors should be visible in console)

15. Disable diagnostic module and reboot device.
16. Be happy with VoLTE and VoWiFi :)



# What modules do?

Module called "DiagPortP2XL" does essentially this on boot:
```
setenforce 0
resetprop sys.usb.configfs 1
resetprop sys.usb.config diag,serial_cdev,rmnet_gsi,adb
```
As we need to do it in order to get diag_port working

Module called "VoLTE and VoWiFi enabler Pixel 2 XL" enables VoLTE and VoWiFi on your device.

It does this:

```
persist.dbg.ims_volte_enable=1
persist.dbg.volte_avail_ovr=1
persist.dbg.vt_avail_ovr=1
persist.dbg.wfc_avail_ovr=1
persist.radio.rat_on=combine
persist.radio.data_ltd_sys_ind=1
persist.radio.data_con_rprt=1
persist.radio.calls.on.ims=1
```
