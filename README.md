# Steam On Fedora

## Intro
I'd been using Windows 11 for about a year on this PC.  It worked fine at first, but then somewhere along the line I noticed the graphics performance was degrading.  Plus I was not happy with things I'd read about Windows 11 and it's propensity advertise your existence to every Tom, Dick, and Harry on the Internet before you even log in.  I'd read that Steam was doing great things on Linux using the Proton emulation layer, so I decided to give it a try.   I put a new NVME drive in and loaded up Fedora 39 Workstation.  I chose this one because it supports UEFI.  

Personal note:  I am a long time Unix / Linux system admin.  I do HPC configuration for a living, so getting into this is not a scary proposition.  However, I'm at the age now where when I'm not working, I don't want to work ... know what I'm saying?  This is my gaming PC.  I do not want it to become a job in order to play some games.  I want to get the most out of my hardware.  i want to good, clean, and responsive interface.  Decent email, WhatsApp, and Spotify clients are important too.  If we meet these criteria, I'm happy.  Also, for anyone reading this who isn't a Linux administrator, don't worry.  So far there hasn't been anything that requires mad computer skills.  So long as you are able to follow directions, copy and paste files, make some minore edits to text files, you'll be fine.

Another note:  Some things did not work as expected.  Most things did, and one or two took a little bit of trial and error to get right.  But on a couple topics I did give up on the initial solution and looked for alternatives.  Remember, this is not my job.  I know there is always a solution.  Sometimes it involves looking at the problem differently.  
## PC Specs
## Hardware Information:
- **Hardware Model:**                              Micro-Star International Co., Ltd. MS-7D53
- **Memory:**                                      64.0 GiB
- **Processor:**                                   AMD Ryzen™ 7 5800X × 16
- **Graphics:**                                    AMD Radeon™ RX 6600
- **Disk Capacity:**                               3.0 TB

## Software Information:
- **Firmware Version:**                            1.40
- **OS Name:**                                     Fedora Linux 39 (Workstation Edition)
- **OS Build:**                                    (null)
- **OS Type:**                                     64-bit
- **GNOME Version:**                               45.2
- **Windowing System:**                            Wayland
- **Kernel Version:**                              Linux 6.6.6-200.fc39.x86_64


## Fedora Installation
Not much to say here.  Write the installer to a USB, boot from it.  Pick the right disk.  Next, next, next.  I picked all the defaults and made choices when required.  It was easy.  One thing I will say though is there was nothing special involved with the ADM Radeon GPU.  Apparently these are ready to go out of the box.  None of the nVidia, rebuild kernel heartache required.  And when I started loading up a game from Steam, I was amazed at the performance.  But more on that later.

## Install Steam
Very easy.  Opened the Fedora Software tool and searched for Steam then installed it and logged into my account.

![image](https://github.com/scqcasc/SteamOnFedora/assets/106348300/bf62dcd3-23a5-43e0-94b0-b9cfe19ba4ec)

Once installed and logged in the other Steam change I needed to make was to allow Windows games to be compatible with Linux through the Proton layer.  This is done thtrough the Steam settings | Compatability tab.

![image](https://github.com/scqcasc/SteamOnFedora/assets/106348300/7b8476b4-d943-449e-a6dd-a75d0a2309cb)


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
>

## Performance
My favourite game is (obviously) Fallout 4.  In truth I'm a little obsessed with it.  So, when I think about gaming performance I'm thinking about the experience of running around the Commonwealth.  When I compare that experience in the two platforms Linux outperforms by nearly a factor of 2.  The average frame rate I see in Windows is around 30.  In Linux, it's 55 - 60.  

(post some screen shots)

Interestingly I ran the Gravity Mark (https://gravitymark.tellusim.com/) utility on both OS's.  Using a full screen Vulcan API benchmark the scores and FPS rates were very close:

![image](https://github.com/scqcasc/SteamOnFedora/assets/106348300/bbb4cafa-9c5d-4956-8684-e442489bdd53)

I attribute Windows' slight edge in score to propriatary drivers.  Linux's FPS were slightly higher (55.4) than Windows' (54.7).  For either metric the difference is negligeable.  

So why the dramatic improvement in a nine year old Windows game running through the Proton emulator?  Hmmm.  Some interesting things going on in that Proton layer I feel.  There are details about Proton available here: https://github.com/ValveSoftware/Proton.  There are options available that can help performance of some games.  So far I've not had reason to dive into these, but good to know they exist.  

Other games I've played so far.  I have used both keyboard and an Xbox controller with these.  I prefer the keyboard but Stray in particular needs a controller.
* Stray
* High On Life
* Cyberpunk 2077
* Atomic Heart

## Other Issues
Of course I do other things with this PC besides wander the wasteland.  Sometimes I want to print something.  the Settings | Printers tool found my Brother MFC WLAN printer no problem.  But I needed to search for it every time.  The reason was the "cups" printer service was not set to start at boot.  I ran this command:

> $ sudo systemctl enable cups
>
and now after reboots my printer appears as normal.

Another issue I found was that Firefox could not play any videos.  I kept seeing broken images where the videos should be in web sites.  I ran the following three commands in a terminal and that fixed the issue.

>sudo dnf install gstreamer1-plugins-{bad-\*,good-\*,base} gstreamer1-plugin-openh264 gstreamer1-plugin-libav --exclude=gstreamer1-plugins-bad-free-devel
>
>sudo dnf install lame\* --exclude=lame-devel
>
>sudo dnf group upgrade --with-optional Multimedia
>

THe above came from this site:  https://docs.fedoraproject.org/en-US/quick-docs/installing-plugins-for-playing-movies-and-music/

## Extra Info
About two weeks after I started working on this document this video cam across my YouTube feed:  https://youtu.be/bq9O99TgFv4?si=0iqXea1kPEdnqeQR.  Give it a watch and a like.  He does a great job describing how the Steam Deck has elevated the Linux gaming experience.

## FOEdit on Linux
So far I have yet to do any mod creation in any PC game, but I am curious about how it is done.  So of course I have started looking at the tools needed to make mods.  FO4Edit tops the list.  I downloaded the exe from Nexus (https://www.nexusmods.com/fallout4/mods/2737) -- the 4.1.5 Experimental version (only because it's the latest and I have no history with this currently).  Once I downloaded it I extracted the contents and copied the two exe files and the 'Edit Scripts' directory to ~/.local/share/Steam/steamapps/common/Fallout 4.  Then I made a symbolic link of the Fallout4.ini file to my home directory:

>
> cd ~/Documents/My\ Games/Fallout4
>
> ln -s ~/.local/share/Steam/steamapps/compatdata/377160/pfx/drive_c/users/steamuser/My\ Documents/My\ Games/Fallout4/Fallout4.ini .
>
Once done I was able to start FO4Edit via the command line:

> cd ~/.local/share/Steam/steamapps/common/Fallout 4
> 
> wine ./FO4Edit.exe
>

![image](https://github.com/scqcasc/SteamOnFedora/assets/106348300/15878746-f865-437a-a60b-9380dbac59cb)

As you can see the fonts aren't fantastic.  But it starts up anyway. Next I'll make an icon for it and then I just need to learn some modding basics.  

