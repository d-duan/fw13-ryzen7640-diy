# fw13-ryzen7640-diy
> [!NOTE]
> Documentation and notes for my FW13 AMD build.\
> Last updated: May 2025

## Documentation

### Components
- [x] AMD Ryzen 7640u
- [x] Crucial DDR5 5600MHz SODIMM CT2K16G56C46S5, 2x16 GB
- [x] SK hynix Gold P31, 2TB
- [x] Additional AX210 WiFi card (reported sleep battery drain [here](https://community.frame.work/t/framework-13-amd-ryzen-ai-sleep-battery-drain-with-intel-ax210-wifi/68959), ~~further testing needed~~ scroll down to see my own testings)
- [x] General upgrades: 4kg hinge set, 61W battery, and the new 2.8k matte display kit
- [x] 40x80mm of Honeywell PTM7950 via aliexpress

### Assembly
First up, I'm going to replace the stock liquid metal with Honeywell, following a detailed write-up from [Michael Wu](https://community.frame.work/t/honeywell-ptm7950-phase-change-thermal-pads-sheets-application-tips-and-results/20245) via FW forum.\
Refrigerate the PTM7950, disassemble the fan + heat sink following FW's guide.\
Cut PTM to size (slightly larger helped covering the edges, I've found.) Lift off plastic film on one side, apply, knead, then remove the second plastic film. Using small tweezers helps here, as lifting the 2nd plastic film too fast could lift some of the PTM, too.

Next, AX210 card, the new hinges, the bezel, the RAMs & SSD, and finally, the keyboards. Follow FW's guides and make sure to read the comments under each step—it will go smoothly!

One thing to note re: keyboard installation is a crucial point of cable management. It's something that the guide doesn't state very clearly.\
Looking at my FW13 on arrival, on the right hand side near the hinge, the pair of webcam+antenna cables (thin, black & white) is threaded **above** a thicker, black cable that runs from the top cover to the memory modules. However, to make sure that the keyboard fits flush to the chassis around the fingerprint reader area, the cabling needs to be re-arranged like so in the picture below:
![re-cabling](https://raw.githubusercontent.com/d-duan/fw13-ryzen7640-diy/refs/heads/main/reroute_cable.jpg)
Make sure that the pair of webcam+antenna cables are **underneath** the black cable, so that they are as low to the bottom plate as possible. This should help the keyboard sit flush to the chassis.

### Installation
Running Windows 11 IoT LTSC 2024.\
Latest drivers and BIOS packages downloaded via FW's official site.\
AX210's WIFI 6E and BT drivers downloaded via Intel's official site.

> [!NOTE]
> You will need another Windows machine to create a bootable usb

Follow FW's official [guide](https://guides.frame.work/Guide/Windows+11+Installation+on+the+Framework+Laptop+13+(AMD+Ryzen%E2%84%A2+Series)/214) and make a bootable usb with the LTSC image using Rufus to bypass the network requirement (AMD build specific).\
Refer to [this thread](https://community.frame.work/t/solved-usb-audio-problems-with-framework-16/60636) for a potential audio issue & fix.

Also, MAS' guide points to [this de-telemetry guide](https://gist.github.com/ave9858/a2153957afb053f7d0e7ffdd6c3dcb89) by ave9858. Further debloating could be needed but I will leave it for now. Running LTSC is already without most of the annoying MS features.\

Lastly, consider updating the Windows Color Management with [Notebookcheck's calibration](https://www.notebookcheck.net/Framework-Laptop-13-5-Core-Ultra-7-review-New-2-8K-120-Hz-display-with-Arc-8-graphics.874187.0.html) and get the most out of your 2.8k matte display. Their .icm file can be found in their colour testing section. Note that if you are still using their older glossy display, Notebookcheck also has a colour profile download via their review of the Ryzen model.

---

## Notes

### AX210

Follwing @Mikkoter's post on FW's forum, I set out test whether the WiFi card itself is causing excessive battery drain issues after installing the latest WiFi and BT drivers.

After doing Sleep Study and looking at the results, I didn't catch any critical offenders (in red) that pointed to AX210; however, I did have suspicious lid-closed screen wakes that had periodically interrupted the sleep state. 

Digging through Event Viewer > Windows Log > System, I found a Warning at the same time that the first of a series of wake-from-sleeps took place. The Warning message is as follows:
```
The description for Event ID 6062 from source Netwtw14 cannot be found. Either the component that raises this event is not installed on your local computer or the installation is corrupted. You can install or repair the component on the local computer.
If the event originated on another computer, the display information had to be saved with the event.

The following information was included with the event: 

\Device\NDMP14
Intel(R) Wi-Fi 6E AX210 160MHz
```
The timing made me suspect that the waking might have had something to do with this event. Digging around more, I had found [this Intel forum post](https://archive.md/Tr9r5)(saved on archive.md just in case) that pointed to the same issue, and a resolution that fixes the Netwtw14 cannot be found error.

After rebooting, I'm no longer experiencing the same Warning in Event Viewer, and haven't had any suspicious AX210-related offences in my Sleep Study.

### Modern Standby (S0)

Windows' Modern Standby is notorious for its unreliability. I'm taking some steps to tweak it to my habit and liking:

Use the commands below to set both battery & plugged S0 to be Network Disconnected, even when the option to toggle it is hidden in Control Pane/Power Options:
```
powercfg -setacvalueindex scheme_current sub_none connectivityinstandby 0
powercfg -setdcvalueindex scheme_current sub_none connectivityinstandby 0
```
Since I won't need my device to stay connected to the internet or bluetooth while sleeping (if needed, I can keep the machine awake with MS Powertoys,) Disconnected Standby ensures that no network traffic can wake my computer up, though the LTSC version already promises much, much reduced frequency of Windows fetching for updates. FWIW, AX210's wake on WoWLAN/magic packet/pattern match is also turned off in Device Manager > {the WiFi card} > Properties > Advanced.

Using powercfg /SleepStudy, I can see that my laptop slept like a baby last night, with ~4% battery drain over just a bit over 5 hours, in line with Microsoft's S0 targets:

![sleepstufy](https://raw.githubusercontent.com/d-duan/fw13-ryzen7640-diy/refs/heads/main/sleepstudy.png)

Here, I'm going to change the Standby Budget to 3% as a personal preference—I value conserved battery level over ~5s of wait from hibernation to wake;)

```
powercfg /setacvalueindex scheme_current sub_none standbybudgetpercent 3
powercfg /setdcvalueindex scheme_current sub_none standbybudgetpercent 3
```

### MS Apps 

IoT LTSC 2024 is a very lean version of Windows 11. However, to bring back some of the enhanced apps from the GAC version, reinstall ms store...
```
wsreset -i
```
... and bring back [App Installer](https://apps.microsoft.com/detail/9nblggh4nns1?hl=en-US&gl=AU), Sticky Notes, Paint, Windows Notepad, Calculator etc


### RMB Context Menu

To revert back to the classic RMB click context menu, use the following command in cmd to alter a reg value:
```
reg.exe add "HKCU\Software\Classes\CLSID\{86ca1aa0-34aa-4e8b-a509-50c905bae2a2}\InprocServer32" /f /ve
```
This forces (/f) an emtpy value (/ve and then nothing) onto the Default value, which points to the .dll that launches the Windows 11 new context menu UI (see image below.)
![Reg location](https://raw.githubusercontent.com/d-duan/fw13-ryzen7640-diy/refs/heads/main/classic%20RMB%20menu.png)
To restore the new UI, change the Default value back to the .dll:
```
reg.exe add "HKCU\Software\Classes\CLSID\{86ca1aa0-34aa-4e8b-a509-50c905bae2a2}\InprocServer32" /f /ve /d "C:\Windows\System32\Windows.UI.FileExplorer.dll"
```
