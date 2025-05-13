# fw13-ryzen7640-diy
> [!NOTE]
> Documentation and notes for my FW13 AMD build.\
> Last updated: May 2025

### Documentation

#### Components
- [x] AMD Ryzen 7640u
- [x] Crucial DDR5 5600MHz SODIMM CT2K16G56C46S5, 2x16 GB
- [x] SK hynix Gold P31, 2TB
- [x] Additional AX210 WiFi card (reported sleep battery drain [here](https://community.frame.work/t/framework-13-amd-ryzen-ai-sleep-battery-drain-with-intel-ax210-wifi/68959), further testing needed)
- [x] General upgrades: 4kg hinge set, 61W battery, and the new 2.8k matte display kit
- [x] 40x80mm of Honeywell PTM7950 via aliexpress

#### Assembly
First up, I'm going to replace the stock liquid metal with Honeywell, following a detailed write-up from [Michael Wu](https://community.frame.work/t/honeywell-ptm7950-phase-change-thermal-pads-sheets-application-tips-and-results/20245) via FW forum.\
Refrigerate the PTM7950, disassemble the fan + heat sink following FW's guide.\
Cut PTM to size (slightly larger helped covering the edges, I've found.) Lift off plastic film on one side, apply, knead, then remove the second plastic film. Using small tweezers helps here, as lifting the 2nd plastic film too fast could lift some of the PTM, too.

Next, AX210 card, the new hinges, the bezel, the RAMs & SSD, and finally, the keyboards. Follow FW's guides and make sure to read the comments under each step—it will go smoothly!

#### Installation
Running Windows 11 IoT LTSC 2024.\
Latest drivers and BIOS packages downloaded via FW's official site.\
AX210's WIFI 6E and BT drivers downloaded via Intel's official site.

> [!NOTE]
> You will need another Windows machine to create a bootable usb

Follow FW's official [guide](https://guides.frame.work/Guide/Windows+11+Installation+on+the+Framework+Laptop+13+(AMD+Ryzen%E2%84%A2+Series)/214) and make a bootable usb with the LTSC image using Rufus to bypass the network requirement (AMD build specific).\



---

### Notes

#### OS 

IoT LTSC 2024 is a very lean version of Windows 11. However, to bring back some of the enhanced apps from the GAC version, reinstall ms store...
```
wsreset -i
```
... and bring back [App Installer](https://apps.microsoft.com/detail/9nblggh4nns1?hl=en-US&gl=AU), Sticky Notes, Paint, Windows Notepad, Calculator etc

Also, MAS' guide points to [this de-telemetry guide](https://gist.github.com/ave9858/a2153957afb053f7d0e7ffdd6c3dcb89) by ave9858. Further debloating could be needed but I will leave it for now. Running LTSC is already without most of the annoying ms features.
