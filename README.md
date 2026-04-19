# VRR-ooM
**Activate VRR for AMD GPU Linux Cinnamon (X11) systems w/ toggle script**


-This will remove Muffin from global mesa blacklist, turn VRR to true, and provide a toggle script that alows user to run script and reboot (afterwards a relog is enough) in order to turn VRR on/off

**Note Muffin/Cinnamon have been added to mesa blacklist because some monitors may start flickering or show odd behavior with VRR on. If you are experiencing this please revert steps or timeshift.**

-Please make a timeshift snapshot (backup) before starting this guide.

## Download instructions

Copy vrr-backup folder to your home directory  (/home/yourusername/)

  For manual instructions
  
   1. Create a folder in /home/yourusername/ and name it vrr-backup
   
   2. Download all files and place them in folder you just made ~/home/yourusername/vrr-backup/

      -click on each file and to the right of where it says raw click download raw file

## Make Backups!

**Make a timeshift backup first!**

Did you make the timeshift snapshot?

Then skip to bottom and read **possible ways your system can break** before continuing

Backup files that will be replaced by comands. (running install commands will completly replace those files)

  -Run these commands in terminal to backup the files

  ```sudo cp /etc/X11/xorg.conf.d/20-amdgpu.conf /etc/X11/xorg.conf.d/20-amdgpu.conf.backup 2>/dev/null || echo "No existing AMD config to backup"```
```cp ~/.drirc ~/.drirc.backup 2>/dev/null || echo "No existing ~/.drirc to backup"```


## Installation

1. Make sure vrr-backup folder is in your home directory (/home/yourusername/) with all needed files.

2. Run all commands to copy files into place (copy/paste into terminal)
```
sudo cp ~/vrr-backup/20-amdgpu.conf /etc/X11/xorg.conf.d/
cp ~/vrr-backup/drirc-minimal.conf ~/.drirc
mkdir -p ~/.local/bin
cp ~/vrr-backup/vrr-safe-toggle ~/.local/bin/
chmod +x ~/.local/bin/vrr-safe-toggle
```
3. Reboot.

After reboot VRR should be functionally on

  -you can verify if VRR is working by checking your monitor's OSD (it should fluctuate)
  
To turn VRR off run script (paste into terminal)
```vrr-safe-toggle```

And then relog for changes to take effect



## Restore original files

-If no drirc file existed before then you can remove the one added in this readme
```rm ~/.drirc 2>/dev/null```

To restore original AMD config from backup
```sudo mv /etc/X11/xorg.conf.d/20-amdgpu.conf.backup /etc/X11/xorg.conf.d/20-amdgpu.conf 2>/dev/null```

To restore orignal drirc from backup
```mv ~/.drirc.backup ~/.drirc 2>/dev/null```



## Recovery

**To get to TTY for recovery**

  - ctrl + alt + F4 (if F4 is not working try F1-F6)
  - enter username and password

Here you can timeshift or restore files from backup using "restore original files from backup commands"


**Timeshift commands**

1. ``` sudo timeshift --list```
  -will show a list of timeshift snapshot

2. ```sudo timeshift --restore```
  -this will show the same list as above but you will need to then type a number (not name)

3. Type the number of the timeshift snapshot you want to restore and hit enter

4. It will ask if you want to restore; type yes

5. It will ask about Grub. Accept default option (yes) then hit enter

6. Select drive for Grub

   -Leagacy/bios mode: Grub should be installed to the device

   -UEFI mode: Grub should be installed to the EFI System Partition

7. Do not interrupt restoration process

   -Your pc might go to sleep if left unattended depending on settings and this will interrupt the process!

8. Once finished it will reboot


## Possible ways system can break

1. You already have a custom /etc/X11/xorg.conf.d/20-amdgpu.conf

   -sudo cp will overwrite your existing config

   -If the orignal file contained essential options those are lost

   -As a result Xorg may fail to start (black screen), 2nd monitor stops working, GPU perforamce degrades,or screen tearing appears.

2. You already have a non-trivial ~/.drirc

   -the command cp ~/vrr-backup/drirc-minimal.conf ~/.drirc overwrites the existing file 

   -As a result any game-spefic workarounds or mesa options will be lost

3. You are not using an amd gpu driver (eg, Intel, NVIDIA, or an old AMD card with readeon driver)

   -The AMD-specific config file (20-amdgpu.conf) is placed in xorg.conf.d.

   -Xorg will try to apply option "VariableRefresh" to a non‑amdgpu device, which may be ignored or cause a parsing error, leading to Xorg failing to start.

   -This will result in Black Screen on boot (recoverable by restoring backup file from TTY)

4. If you use a different display manager or session

   -Toggle script restarts Cinnamon which may not work correctly or crash.


- You can try recovery steps above if you get black screen
