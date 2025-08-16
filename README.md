# Retro Stream Tutorial
A tutorial on how to stream Youtube or any other video content to PowerPC Macs.

## Background
I am writing this in 2025 and I would say that it has been a good 10 years
since pretty much any computing device we may have can easily easily stream 
1080p and 4K content directly from the internet with absolutely no issues.

However, before 2015, this was not always the case. I will paste in specs
for the iPhone 5 and the original iPhone, emphasis mine:

iPhone 5 - 2007 \([Source](https://everymac.com/systems/apple/iphone/specs/apple-iphone-5-a1428-gsm-lte-4-17-north-america-specs.html)\):
> H.264 video up to 1080p, 30 frames per second
iPhone - 2012 \([Source](https://everymac.com/systems/apple/iphone/specs/apple-iphone-specs.html)\)
> H.264 video, up to 1.5 Mbps, **640 by 480 pixels**, 30 frames per second, 
> Low-Complexity version of the H.264 Baseline Profile

So while we may feel like high quality Youtube video playback has been a given
forever, it actually has not. And so when when we use old Macs we may get
frustrated by their inability to do "simple" tasks like play Youtube. But 
the reason I am giving this background is to illustrated that this is no
simple task.

## Why Can't Old Computers Play Youtube

1\) **[The TLS Apocalypse](https://oldvcr.blogspot.com/2020/11/fun-with-crypto-ancienne-tls-for.html)**: 
Means PowerPC macs can't connect to Youtube at all.

 - A few years ago, there was a big push for web servers to
   disable support for TLSv1.1 because there were fundamental flaws found in
   its cryptography. However, on pretty much any system older than 2015, 
   TLS v1.2 is not supported. If the web were working as expected, then these
   systems would just negotiate a TLSv1.1 or v1.0 connection. Hell, even 
   start the connection with no TLS for 90's system. But basically the entire
   internet decided that just rejecting the connection all together was better
   than allowing an insecure connection. So now what you see is any Mac running
   an OS older than macOS 10.9 Maverics (2013), basically can't browse the 
   internet at all.
   
2\) **H.264 is too Sophisticated**: Even if these Macs could connect to Youtube, 
H.264 is such a computationally intensive codec, that these old Macs can
barely play back postage stamp sized video in H.264.

 - H.264 was such a huge success because it meant extremely high quality 
   videos used very little bandwidth compared to its predecessors like Xvid
   and MPEG2. So while old Macs have not much trouble MPEG1, MPEG2, and Xvid, 
   we never got high quality videos back in the day because there was not
   enough bandwidth on the internet to get quality video out of these codecs.
   
## How to Stream High Quality Video on a PowerPC Mac

We are basically going to use the fact that we have a local high speed ethernet
network that our PowerPC Mac sits on to stream an incredibly-high bit-rate
yet simple to decode video codec like MPEG1, MPEG2, or Xvid. We are going 
to use so much bandwidth that even a modern internet connection would have 
trouble streaming this video over the internet (10+Mbps). 

The approach replaces the limited CPU power of PowerPC Macs and replaces it
with tons of bandwidth which they can handle thanks to their 10/100Mbps
ethernet ports.

## Known Limitations

### Video Quality:

I want to be clear here that we are not going to get beautiful 4K content
playing on our PowerPC Macs. They just do not have enough power. But depending
on the system you have you will probably at least get:

- 720p video (480p on Tiger)
   - 1080p may be possible on fast G4's and G5's
- 20-30FPS
- Decently clear picture without too many blocky compression artifacts

### Audio Quality

I could actually use suggestions on this because this seems to be an FFMPEG
limitation on Apple Silicon Macs (I did not try an Intel Mac). But I cannot get
FFMPEG to capture 44,100 or 48,000hz audio on the host Mac. It always comes
out poppy and out of sync and weird. So I had to reduce the audio quality on
the capture side to 16,000hz which is not ideal.

## System Requirements

1. PowerPC Mac running at least Mac OS X 10.4 Tiger (10.5 Leopard preferred)
   1. What about Jaguar? [VLC claims](https://www.videolan.org/vlc/download-macosx.html) 
      they have a version that supports 10.2 Jaguar but when I launched it on 
      10.2.8 it just crashed and I didn't try to troubleshoot at all. But 
      theoretically this would work on any version of OS X if you could get 
      VLC to start.
   1. Why is Leopard preferred? In the many hours of testing I have done, 
      Leopard seems to have a better networking stack that can handle 10+Mbps
      onslaught of UDP packets we are going to throw at the system. I was able
      to get almost double the resolution in Leopard (1152x720p) than I was
      able to in Tiger. But Tiger still works fine, just lower quality.
1. PowerPC Mac connected to your home network over ethernet: Sorry, the Airport
   cards on these old Macs are too slow for this.
1. Apple Silicon Mac: It goes without saying that these Macs are so overpowered
   for this that using this set up does not even push them at all.
   
### My Setup

- [iMac (Flat Panel 17-inch, 1GHz)](https://everymac.com/systems/apple/imac/specs/imac_1.0_17_fp.html) from 2003 with SSD and 1.5GB RAM Upgrade
- [MacBook Air \(2020\)](https://everymac.com/systems/apple/macbook-air/specs/macbook-air-m1-8-core-8-core-gpu-13-retina-display-2020-specs.html) with M1 Chip and 16GB of RAM

## Tools

1. [Virtual Buddy](https://github.com/insidegui/VirtualBuddy) - An excellent 
   Apple Silicon OS Virtualization Tool
1. [FFMPEG](https://ffmpeg.org/download.html#build-mac) - The one-stop-shop 
   for all video encoding and decoding
1. [Black Hole](https://github.com/ExistentialAudio/BlackHole) - A virtual 
   audio interface that we need so FFMPEG can capture the system audio
1. [VLC](https://www.videolan.org/vlc/download-macosx.html) - The one-stop-shop 
   for all video playback on OS X for 25 years

# Tutorial: Set up the Host

## Step 1: Set up your Virtual Machine

1. Install [Virtual Buddy](https://github.com/insidegui/VirtualBuddy)
1. Open Virtual Buddy and create a new VM
   1. Select Ventura→macOS 13.5.2 - [Screenshot](Images/010-VM/00-VMSettings-OS.png)
      1. You can use which ever version you want, but older systems take less 
         disk space
      1. This is not changeable later, so you want to set it as low as possible 
         to not waste space on your Mac
   1. Virtual Buddy will ask you for the settings - [Screenshot](Images/010-VM/01-VMSettings.png)
      1. Boot Disk: 40GB (64 may be needed for newer systems)
      1. Display: 1152x720 with 72 Pixels Per Inch
         1. This depends on the size of the PowerPC Mac screen. You want to 
            match the aspect ratio and resolution as close as possible.
         1. This is changeable later and also you can scale it in FFMPEG if 
            its too high for the PowerPC Mac.
         1. Network: Bridged (Automatic) - **do not choose NAT**
         1. Enable Sound but you can disable sound input
      1. [Installing Screenshot](Images/010-VM/02-VMInstall.png)
1. Boot up your VM and Configure the User - [Screenshot](Images/010-VM/03-VMBoot.png)
   1. Migration: Not Now - [Screenshot](Images/010-VM/04-VMSettings-Migrate.png)
   1. iCloud: Set Up Later - [Screenshot](Images/010-VM/05-VMSettings-iCloud.png)
   1. User: Simple username and password - [Screenshot](Images/010-VM/06-VMSettings-User.png)
      1. Simple username and password makes access convenient. 
         Please don't boot this VM outside of your home network.
   1. Location Services: Disable - [Screenshot](Images/010-VM/07-VMSettings-Location.png)
   1. ScreenTime: Set Up Later - [Screenshot](Images/010-VM/08-VMSettings-ScreenTime.png)
   1. [Booted-Up Screenshot](Images/010-VM/09-VMSettings-Booted.png)
1. Configure System Settings
   1. (Optional) Users&Groups: Enable Automatic Login - [Screenshot](Images/010-VM/10-VMSettings-AutoLogin.png)
   1. (Optional) Lock Screen: Disable Screensaver, Monitor Poweroff, and password - [Screenshot](Images/010-VM/11-VMSettings-LockScreen.png)
   1. (Optional) General→About: Give it a fun name - [Screenshot](Images/010-VM/12-VMSettings-Name.png)
   1. (Optional) General→Sharing: Enable Screen Sharing, File Sharing, and Remote Login - [Screenshot](Images/010-VM/13-VMSettings-Sharing.png)
   1. **(Required)** Privacy→Screen Recording: Add Termina - [Screenshot](Images/010-VM/14-VMSettings-Privacy.png)
   1. (Optional) Software Update: Disable automatic updating - [Screenshot](Images/010-VM/15-VMSettings-Update1.png)
   1. (Optional) Software Update: Perform Updates that do not update to different major version of OSX
      1. Apple actually makes this kind of confusing. You need to look for 
        "Also Available→Other Updates Available→More Info"
      1. [Screenshot 1](Images/010-VM/15-VMSettings-Update1.png) 
      1. [Screenshot 2](Images/010-VM/16-VMSettings-Update2.png) 
      1. [Screenshot 3](Images/010-VM/17-VMSettings-Update3.png) 
1. Login to Youtube and play a video - you should hear sound through your Mac's speakers - [Screenshot](Images/010-VM/20-VMSettings-YoutubeLogin.png)

## Step 2: Install FFMPEG

FFMPEG is probably most easily installed via HomeBrew but HomeBrew requires
installing Xcode developer tools which is pretty large. So instead I chose to
download an install package from [https://ffmpeg.martin-riedl.de](https://ffmpeg.martin-riedl.de)

1. Download a Release Build Package under the `macOS (Apple Silicon/arm64)` section - [Screenshot](Images/020-Install/01-FF-Download.png)
1. Run the installer package
1. Test that FFMPEG works from Terminal: `ffmpeg -version` - [Screenshot](Images/020-Install/02-FF-Confirm.png)

## Step 3: Install Black Hole 2CH

Follow the instructions on the [Black Hole Github Project.](https://github.com/ExistentialAudio/BlackHole?tab=readme-ov-file#installation-instructions)
Black Hole requires you to submit your email address to get the installer
link. If you don't want to do that, then you can use HomeBrew to install it.

1. [Submit your email address](https://existential.audio/blackhole/) to download BlackHole 2CH - [Screenshot](Images/020-Install/03-BH-Email.png)
1. Check your email, open the link, and download the 2CH version. - [Screenshot](Images/020-Install/04-BH-Download.png)
   1. Since we turned on File Sharing to your VM you can transfer the file that way
1. Run the installer package
1. Restart the VM
1. Open the 'Audio MIDI Setup' application. This is built-in to OSX and is 
   under the Utilities folder
1. Change the Format to 16,000 Hz - [Screenshot](Images/020-Install/05-BH-Configure.png)
   1. As I said, this is a workaround to an FFMPEG problem where it cannot
      seem to capture audio when it is set to 44,100 or 48,000. PowerPC Macs
      use 41,100 Hz and so that would be ideal for audio quality. I really
      want to know how to fix this problem, but I have not been able to find
      any solutions.
1. Confirm it works by playing a Youtube video again: In the Speaker menu
   you can choose to play through Speakers or BlackHole 2CH - [Screenshot](Images/020-Install/06-BH-Confirm.png)
   1. When you play through Speakers you should hear the video on the host Mac. 
   And when you change it to BlackHole you should no longer hear it.


