# Steam On Fedora
I'd been using Windows 11 for about a year on this PC.  It's an AMD Ryzen 7 5800X CPU with an AMD Radeon 5800 GPU.  It worked fine at first, but then somewhere along the line I noticed the graphics performance was degrading.  Plus I was not happy with things I'd read about Windows 11 and it's propensity advertise your existence to every Tom Dick and Harry on the Internet before you even log in.  I'd read that Steam was doing great things on Linux using the Proton emulation layer, so I decided to give it a try.   I put a new NVME drive in and loaded up Fedora 39 Workstation.  I chose this one because it supports UEFI.  

Personal note:  I am a long time Unix / Linux system admin.  I do HPC configuration for a living, so getting into this is not a scary proposition.  However, I'm at the age now where when I'm not working, I don't want to work ... know what I'm saying?  This is my gaming PC.  I do not want it to become a job in order to play some games.  I want to get the most out of my hardware.  i want to good, clean, and responsive interface.  Decent email, WhatsApp, and Spotify clients are important too.  If we meet these criteria, I'm happy.

## Fedora Installation
Not much to say here.  Write the installer to a USB, boot from it.  Pick the right disk.  Next, next, next.  I picked all the defaults and made choices when required.  It was easy.  One thing I will say though is there was nothing special involved with the ADM Radeon GPU.  Apparently these are ready to go out of the box.  None of the nVidia, rebuild kernel heartache required.  And when I started loading up a game from Steam, I was amazed at the performance.  But more on that later.

## Install Steam
Very easy.  Opened the Fedora Software tool and searched for Steam then installed it and logged into my account.

![image](https://github.com/scqcasc/SteamOnFedora/assets/106348300/bf62dcd3-23a5-43e0-94b0-b9cfe19ba4ec)

## Mouse settings
I have a Logitech Hero mouse that I wanted to program some buttons on.  This was a little trickier than installing Steam.  First I installed piper
>$ sudo dnf install piper

The GUI wasn’t great.  It didn't do what I wanted it to do (which was map the keys) but was it was able to tell me which key was which and then I was able to use the ratbagctl command to do the rest.

The Piper Window

![image](https://github.com/scqcasc/SteamOnFedora/assets/106348300/489f88ec-b02b-4d99-be5a-b7419ac24bd5)

By clicking the configuration button of the keys I wanted to map I was able to learn their button names, which were buttons 6 and 7 in my case.  Then I used the command line to figure out the device name (singing-gundi):

>$ ratbagctl list
>
> singing-gundi:       Logitech G502 HERO Gaming Mouse 

then
>$ ratbagctl singing-gundi button 7 action set macro KEY_J
>
>$ ratbagctl singing-gundi button 6 action set macro KEY_k

Then I made sure Fallout had those keys do what I wanted

To make sure these settings survive logouts and reboots I added them to ~/.bash_profile.  

## Steam Mods

On Windows I used Vortex to manage mods, and so I was glad to learn that Vortex would also work using the same emulation as the games.  After doing a bit of reading on this I found the general order of operations was install steamtinkerlaunch, and then install Vortex from that.  Installing steamtinkerlaunch wasn't too bad.  I'd found a Fedora repo for it.  But after installing it, short of launching the initial GUI I found the tool to be completely useless. 

![image](https://github.com/scqcasc/SteamOnFedora/assets/106348300/e3b0b156-2868-4222-9181-84fb1d08f4c6)

Good looking GUI, I suppose.  But when I select Vortex, and choose a Proton version a white GUI pops up then quickly disapears.  Clearly it cannot access something it needs.  I banged my head on this wall for a while, but was unable to Google my way out of that situation in a reasonable time.   Instead I decided to install mods the old fashioned way:  download them and unzip them into the game's Data directory.  Then I used the game's Mods menu to order and enable them.   The data directory is easily found by opening the Steam Library and right-clicking on the game in question, then browse local files like so: 

![image](https://github.com/scqcasc/SteamOnFedora/assets/106348300/a8388481-601a-4973-acaa-42955df02448)

this opens a Files window in that directory.

Also had to create the Fallout4custom.ini file. This is located in the game's root directory (same level as Data) and only contains a few lines:

>[Archive]
>
>bInvalidateOlderFiles=1
>
>sResourceDataDirsFinal=
>

I've installed about 15 mods this way so far.  It's working great.  Vortex … who needs it?

## F4SE
Installing the Fallout 4 Script Extender was fairly easy but it's important to do steps 3 & 4
    1. Downloaded the Extender from https://f4se.silverlock.org/ and unzipped in my Downloads directory
    2. Copied the .exe and both .dlls to ~/.local/share/Steam/steamapps/common/Fallout 4
    3. Renamed Fallout4Launcher.exe to Fallout4Launcher-orig.exe
    4. Renamed f4se_loader.exe to Fallout4Launcher.exe
    5. Copied the Data directory to ~/.local/share/Steam/steamapps/common/Fallout 4

## Snapshots
Since /home uses BTRFS I can use snapshots to protect the game directories.
1. cd ~/.local/share/Steam/steamapps/common/Fallout 4
2. mv 'Fallout 4' 'Fallout 4 Orig'
3. sudo btrfs subvolume create 'Fallout 4'
4. sudo chown -R $(id -u):$(id -g) Fallout\ 4
5. cd Fallout\ 4\ Orig/
6. mv * ../Fallout\ 4

I tested FO4 and it still works.  Now will be able to use the btrfs command to create snap shots before adding further mods.  Now need to create a btrfs partition to store the snapshots.  Meantime, I created first snapshot using 

> $ sudo btrfs subvolume snapshot -r ~/.local/share/Steam/steamapps/common/Fallout\ 4 .snapshots/FO4-1
