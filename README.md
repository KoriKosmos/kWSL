# [kWSL.cmd - KDE Neon for WSL](https://github.com/DesktopECHO/kWSL)

  - Net-install of KDE Neon on WSL1 using xRDP.  If you prefer GTK, see [**xWSL**](https://github.com/DesktopECHO/xWSL) or [**Kali-xRDP**](https://github.com/DesktopECHO/Kali-xRDP)
  - Runs on Windows 10 and Server 2019/2022 (including Hyper-V Core)
  - xRDP Display Server; no additional Xserver download/configuration required
  - High-quality RDP audio playback, video playback and YouTube work well and audio maintains sync reliably.
  - If you want to accesss kWSL remotely, Chrome Remote Desktop is pre-installed (Never expose RDP servers to the Internet.) Configuration steps are [**here**](https://github.com/DesktopECHO/kWSL/wiki/Enable-Chrome-Remote-Desktop)

![image](https://user-images.githubusercontent.com/33142753/100149597-d3d57d80-2e74-11eb-899a-a7476b016e27.png)

**IMPORTANT!**  Requires August/Sept 2020 WSL update for Windows 10.  This update is already included in Windows 20H2 and newer:

* 1809  · KB4571748
* 1909  · KB4566116
* 2004  · KB4571756

**INSTRUCTIONS:  From an elevated CMD.EXE prompt change to your desired install directory and type/paste the following command:**

```
PowerShell -executionpolicy bypass -command "wget https://github.com/DesktopECHO/kWSL/raw/master/kWSL.cmd -UseBasicParsing -OutFile kWSL.cmd ; .\kWSL.cmd"
```

You will be asked a few questions.  The install script finds the current DPI scaling, you can set your own value if needed:

```
[kWSL Installer 20210609]

Enter a unique name for your kWSL distro or hit Enter to use default.
Keep this name simple, no space or underscore characters [kWSL]: Neon
Port number for xRDP traffic or hit Enter to use default [3399]: 13399
Port number for SSHd traffic or hit Enter to use default [3322]: 13322
Set a custom DPI scale, or hit Enter for Windows default [1.5]: 1.25
[Not recommended!] Type X to eXclude from Windows Defender:

Installing kWSL Distro [Neon] to "C:\WSL Distros\Neon"
This will take a few minutes, please wait...
```

The installer will download all the necessary packages to convert the Windows Store Ubuntu 20.04 image into KDE Neon User Edition.  Reference times will vary depending on system performance and the presence of antivrirus software.  A fast system/network can complete the install in about 10 minutes.

```
[ 2:47:40] Installing Ubuntu 20.04 LTS (~1m30s)
[ 2:48:48] Git clone and update repositories (~1m15s)
[ 2:49:49] Remove un-needed packages (~1m30s)
[ 2:50:28] Configure apt-fast Downloader (~0m45s)
[ 2:50:42] Remote Desktop Components (~2m45s)
[ 2:52:22] KDE Neon 5.22 User Edition (~11m30s)
[ 2:58:14] Install Web Browser and CRD (~1m30s)
[ 2:58:41] Final clean-up (~0m45s)
```

Near the end of the script you will be prompted to create a non-root user.  This user will be automatically added to sudo'ers.

```
Open Windows Firewall Ports for xRDP, SSH, mDNS...
Building RDP Connection file, Console link, Init system...
Building Scheduled Task...
SUCCESS: The scheduled task "Neon" has successfully been created.

      Start: Thu 07/01/2021 @  2:46
        End: Thu 07/01/2021 @  2:58
   Packages: 1360

  - xRDP Server listening on port 13399 and SSHd on port 13322.

  - Links for GUI and Console sessions have been placed on your desktop.

  - (Re)launch init from the Task Scheduler or by running the following command:
    schtasks /run /tn Neon

 Neon Installation Complete!  GUI will start in a few seconds...
```
The install summary should indicate 1359 or 1360 packages installed, depending on Windows version.   

**Upon completion you'll be logged-into your KDE Neon Desktop.** 

**Configure kWSL to start at boot (like a service, no console window)**

 - Right-click the task in Task Scheduler, click properties
 - Click the checkbox for **Run whether user is logged on or not** and click **OK**
 - Enter your Windows credentials when prompted
 
 Reboot your PC when complete and kWSL will startup automatically.

**xWSL is configured to use Bonjour (Multicast DNS) for easy access in WSL2**

If your computer has virtualization support you can convert it to WSL2.  kWSL is faster on WSL1, but WSL2 has additional capabilities. 

Example of conversion to WSL2 on machine name "ENVY":
 - Stop WSL on ENVY:
 ````wsl --shutdown````
 - Convert the instance to WSL2:
 ````wsl --set-version kWSL 2````
 - Restart kWSL Instance:
 ````schtasks /run /tn kWSL````
 - Adjust the RDP file saved on the desktop to now point at the new WSL2 instance:
 ````ENVY-kWSL.local:3399````

**Make it your own:**

From a security standpoint, it would be best to fork this project so you (and only you) control the packages and files in the repository.

- Sign into GitHub and fork this project
- Edit ```kWSL.cmd```.  On line 2 you will see ```SET GITORG=DesktopECHO``` - Change ```DesktopECHO``` to the name of your own repository.
- Customize the script any way you like.
- Launch the script using your repository name:
 ```PowerShell -executionpolicy bypass -command "wget https://github.com/YOUR-REPO-NAME/kWSL/raw/master/kWSL.cmd -UseBasicParsing -OutFile kWSL.cmd ; .\kWSL.cmd"```

**Quirks / Limitations / Additional Info:**

- When you log out out of a KDE session the WSL instance is restarted.  This is the equivilent to having a freshly-booted desktop environment at every login, but the 'reboot' process only takes about 5 seconds.  
- kWSL should work fine with an X Server instead of xRDP but this has not been thoroughly tested.  The file ```/etc/profile.d/kWSL.sh``` contains WSL-centric environment variables that may need adjustment such as LIBGL_ALWAYS_INDIRECT.
- Plasma-discover doesn't work in Server 2019 / Win 10 v.1809 -- The installer will remove it if you're running an affected OS. 
- WSL1 Doesn't work with PolicyKit.  Enabled kdesu for apps needing elevated rights (plasma-discover, ksystemlog, muon, root console.)    
- KDE Lockscreen is disabled (due to policykit)  
- Patched KDE Activity Manager to disable WAL in sqlite3. 
- Mozilla Seamonkey is included as a stable/maintained browser. 
- QtWebEngine (Chromium-based) browsers like Falkon and Konqueror now work. (July/21)
- Installed image consumes approximately 3 GB of disk space.
- Apt-fast added to improve download speed and reliability.
- KDE uses the Breeze-Dark theme and Windows fonts (Segoe UI / Consolas)
- This is a basic installation of KDE to save bandwidth.  If you want the **complete** KDE Desktop environment (+3GB Disk) run ```sudo pkcon -y install neon-all``` 

![image](https://user-images.githubusercontent.com/33142753/100148485-33cb2480-2e73-11eb-932b-54e34b445575.png)

![image](https://user-images.githubusercontent.com/33142753/100385367-c21ce300-2ff8-11eb-9276-6f51b366839f.png)
