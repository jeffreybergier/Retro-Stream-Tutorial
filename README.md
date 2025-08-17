[![iMac G4 Streaming Youtube](Images/000-Title/01-Title-Thumb.jpg)](Images/000-Title/02-Title-Full.jpg)

# Retro Stream Tutorial

A tutorial on how to stream Youtube or any other video content to PowerPC Macs.
To see a video of the kind of quality you can expect,
[check this recording](https://drive.google.com/file/d/13i3V9a7xyKCAwo0-r4rAtxuz6pWQDmoB/view?usp=sharing)
on my Google Drive. I have noticed that the streaming quality from Google 
Drive directly is not always great, so download it to be sure you are seeing 
the full quality.

## Table of Contents
- [Retro Stream Tutorial](#retro-stream-tutorial)
  - [Who Am I](#who-am-i)
  - [Background](#background)
    - [Why Can't Old Computers Play Youtube](#why-cant-old-computers-play-youtube)
    - [How to Stream High Quality Video on a PowerPC Mac](#how-to-stream-high-quality-video-on-a-powerpc-mac)
    - [Known Limitations](#known-limitations)
    - [System Requirements](#system-requirements)
    - [Tools](#tools)
    - [Why Use a Virtual Machine](#why-use-a-virtual-machine)
- [Tutorial](#tutorial)
  - [Step 1: Prepare your Virtual Machine](#step-1-prepare-your-virtual-machine)
  - [Step 2: Install FFMPEG](#step-2-install-ffmpeg)
  - [Step 3: Install Black Hole 2CH](#step-3-install-black-hole-2ch)
  - [Step 4: Test on Your Modern Mac](#step-4-test-on-your-modern-mac)
  - [Step 5: Prepare the PowerPC Mac](#step-5-prepare-the-powerpc-mac)
  - [🥳Congratulations🎉](#congratulations)
- [Optimize Playback Performance](#optimize-playback-performance)
  - [XVID](#xvid)
  - [MPEG1 and MPEG2](#mpeg1-and-mpeg2)
  - [Streaming without Audio](#streaming-without-audio)
- [FAQ](#faq)

## Who Am I

I'm Jeff, a software developer and general Mac lover. I recently got an 
iMac G4 and restored it with SSD and max RAM. I got this machine because 
when I was in High School I desperately wanted one but could never get it. 
But I didn't want to get it if it was going to sit on a shelf and collect dust.

So before I bought it I tried this streaming setup to make sure it could work. 
And I am very happy to report that I am happy with the results and this 
machine now makes a great music video display for my KPOP listening 
enjoyment while I'm working.

You can see all updates on my iMac G4, including the restoration, 
using the [\#iMacG4](https://jeffburg.social/tags/iMacG4) tag on my Mastodon. 
No account required. 

## Background

I am writing this in 2025 and I would say that it has been a good 10 years
since pretty much any computing device we may have can easily easily stream 
1080p and 4K content directly from the internet with absolutely no issues.

However, before 2015, this was not always the case. I will paste in specs
for the iPhone 5 and the original iPhone. You can see from the specs that 
even with special decoding hardware, the iPhone could only do 480p H.264.

iPhone 5 - 2012 \([Source](https://everymac.com/systems/apple/iphone/specs/apple-iphone-5-a1428-gsm-lte-4-17-north-america-specs.html)\):
> H.264 video up to 1080p, 30 frames per second

iPhone - 2007 \([Source](https://everymac.com/systems/apple/iphone/specs/apple-iphone-specs.html)\)
> H.264 video, up to 1.5 Mbps, **640 by 480 pixels**, 30 frames per second, 
> Low-Complexity version of the H.264 Baseline Profile

As another anecdote, I remember having a [1.67GHz PowerBook G4](https://everymac.com/systems/apple/powerbook_g4/specs/powerbook_g4_1.67_15_hr.html)
from late 2005 and being super amazed that it could rip DVD's into MPEG4 
format (not H.264 yet) at 1x meaning that it ONLY took 2 hours to rip a 2 hour 
DVD. This was incredible speed during this era.

So while we may feel like high quality Youtube video playback has been a given
forever, it actually has not. And so when when we use old Macs we may get
frustrated by their inability to do "simple" tasks like play Youtube. But 
the reason I am giving this background is to illustrated that this is no
simple task.

### Why Can't Old Computers Play Youtube

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
   
### How to Stream High Quality Video on a PowerPC Mac

We are basically going to use the fact that we have a local high speed ethernet
network that our PowerPC Mac sits on to stream an incredibly high bit rate
yet simple to decode video codec like MPEG1, MPEG2, or Xvid. We are going 
to use so much bandwidth that even a modern internet connection would have 
trouble streaming this video over the internet (10+Mbps). 

The approach replaces the limited CPU power of PowerPC Macs and replaces it
with tons of bandwidth which they can handle thanks to their 10/100Mbps
ethernet ports.

### Known Limitations

#### Video Quality:

I want to be clear here that we are not going to get beautiful 4K content
playing on our PowerPC Macs. They just do not have enough power. But depending
on the system you have you will probably at least get:

- 720p video (480p on Tiger)
   - 1080p may be possible on fast G4's and G5's
- 20-30FPS
- Decently clear picture without too many blocky compression artifacts

#### Audio Quality

I could actually use suggestions on this because this seems to be an FFMPEG
limitation on Apple Silicon Macs (I did not try an Intel Mac). But I cannot get
FFMPEG to capture 44,100 or 48,000hz audio on the host Mac. It always comes
out poppy and out of sync and weird. So I had to reduce the audio quality on
the capture side to 16,000hz which is not ideal.

That said, this is a limitation of FFMPEG and not the PowerPC Mac. As we all 
know, audio quality with high bit rate MP3 and AAC files on old Macs works 
really well.

### System Requirements

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
1. Apple Silicon Mac: This will be the streaming server. Theoretically, any 
   computer that runs FFMPEG could be the streaming server. But this tutorial 
   is for Apple Silicon Macs.
   
#### My Setup

- [iMac (Flat Panel 17-inch, 1GHz)](https://everymac.com/systems/apple/imac/specs/imac_1.0_17_fp.html) from 2003 with SSD and 1.5GB RAM Upgrade
- [MacBook Air \(2020\)](https://everymac.com/systems/apple/macbook-air/specs/macbook-air-m1-8-core-8-core-gpu-13-retina-display-2020-specs.html) with M1 Chip and 16GB of RAM

### Tools

1. [Virtual Buddy](https://github.com/insidegui/VirtualBuddy) - An excellent 
   Apple Silicon OS Virtualization Tool
1. [FFMPEG](https://ffmpeg.org/download.html#build-mac) - The one-stop-shop 
   for all video encoding and decoding
1. [Black Hole](https://github.com/ExistentialAudio/BlackHole) - A virtual 
   audio interface that we need so FFMPEG can capture the system audio
1. [VLC](https://www.videolan.org/vlc/download-macosx.html) - The one-stop-shop 
   for all video playback on OS X for 25 years

### Why Use a Virtual Machine

Using a virtual machine is totally optional. However, FFMPEG uses AVFoundation
to capture the system video and audio. However, AVFoundation only presents
entire screens, not windows, as a capture device that FFMPEG can record.
This means if you don't use a VM, you would need to sacrifice an entire screen
on your Mac to streaming to the PowerPC Mac. Using the VM means, you just
manage the streaming session as a little window that you can resize or minimize
without affecting the stream in progress.

# Tutorial

I tried to add as many screenshots as possible. So if you are getting lost, 
use Command+F and search for Screenshot to highlight all of the screenshot 
links in the tutorial.

## Step 1: Prepare your Virtual Machine

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
   1. **(Required)** Privacy→Screen Recording: Add Terminal - [Screenshot](Images/010-VM/14-VMSettings-Privacy.png)
   1. (Optional) Software Update: Disable automatic updating - [Screenshot](Images/010-VM/15-VMSettings-Update1.png)
   1. (Optional) Software Update: Perform Updates that do not update to different major version of OSX
      1. Apple actually makes this kind of confusing. You need to look for 
        "Also Available→Other Updates Available→More Info"
      1. [More Info Screenshot](Images/010-VM/16-VMSettings-Update2.png) 
      1. [Installing Software Update Screenshot](Images/010-VM/17-VMSettings-Update3.png) 
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

## Step 4: Test on Your Apple Silicon Mac

This requires you to install [install VLC](https://www.videolan.org/vlc/download-macosx.html)
on your Apple Silicon Mac (not the PowerPC Mac and not the VM). I won't cover 
that because thats a normal app installation. - [Screenshot](Images/030-Test/01-Test-Download.png)

1. On the VM: Get the capture device ID's - [Screenshot](Images/030-Test/02-Test-Devices.png)
   1. FFMPEG Uses Apple's AVFoundation to capture system video and audio and 
      AVFoundation presents devices with an ID number. On the VM it should be
      `0:0` but it could be different. So lets find out
   1. `ffmpeg -f avfoundation -list_devices true -i ""`
1. On the VM: Get the Bonjour name for your Apple Silicon Mac - [Screenshot](Images/030-Test/03-Test-Bonjour.png)
   1. System Settings→General→Sharing→Local Hostname
   1. When using UDP, the VM and FFMPEG are basically going to fire and forget
      a huge high-bandwidth stream of bits at the target computer. So when
      starting the command, you need to know the IP Address or Bonjour name
      of the target Mac, whether thats the PowerPC Mac or the Apple Silicon Mac
      (in this case)
   1. Note: If you want to change the Bonjour name it is best to change the
      whole system name under General→About→Name. Doing this will also change
      the bonjour name.
1. On the VM: Start the Stream - [Screenshot](Images/030-Test/04-Test-FFMPEG1.png)
   1. You need to change the Bonjour name on the last line of this command
   1. If the devices you found are not both 0, then you need to change 
      the line that says `-i 0:0`
   1. I will explain all of the options later in this tutorial
   1. When you run this command the first time, you will be asked to allow
      the Terminal to access the microphone. Be sure to choose "OK". - [Screenshot](Images/030-Test/05-Test-FFMPEG2.png)
   1. ```
      ffmpeg \
        -f avfoundation \
        -framerate 30 \
        -i "0:0" \
        -vcodec mpeg4 \
        -qscale:v 1 \
        -vtag XVID \
        -acodec libmp3lame \
        -b:a 192k \
        -ac 2 \
        -f mpegts \
        "udp://BonjourName.local:1234?pkt_size=1316"
      ```
1. On the Apple Silicon Mac: Start VLC and Choose File→Open Network - [Screenshot](Images/030-Test/06-Test-VLC.png)
   1. Type `udp://@:1234` and click Open
1. Success Screenshots
   1. [Initial Stream Results](Images/030-Test/07-Test-Working1.png)
   1. [Open Youtube in the VM](Images/030-Test/08-Test-Working2.png)
   1. [Full Screen Youtube](Images/030-Test/09-Test-Working3.png)
1. On the VM: Type Q in the Terminal to quit FFMPEG  - [Screenshot](Images/030-Test/10-Test-Quit.png)

## Step 5: Prepare the PowerPC Mac

Note that for Leopard, the VLC website say you can use VLC 2.0.10 but I found
this version to have terrible performance. A search online revealed the same
thing. Instead you should use the newest version of 1.0 that you can find. 
Credit to [PowerPC Liberation: Video on PowerPC: Part 1 - Playback on G4/G5](https://powerpcliberation.blogspot.com/2012/08/video-on-powerpc-playback-on-g4g5.html)

Note that due to the [The TLS Apocalypse](https://oldvcr.blogspot.com/2020/11/fun-with-crypto-ancienne-tls-for.html)
mentioned above, you will almost certainly not be able to download these 
directly on the PowerPC Mac. Instead you will need to download them on your
modern Mac and then transfer them over file sharing.

1. On the Apple Silicon Mac: Download an old version of VLC
   1. Leopard: [VLC 1.1.12 from Macintosh Repository](https://www.macintoshrepository.org/11636-vlc-media-player) - [Screenshot](Images/040-PPC/01-PPC-Download-Leopard.png)
   1. Tiger: [VLC 0.9.10 from VLC Website](https://www.videolan.org/vlc/download-macosx.html) - [Screenshot](Images/040-PPC/02-PPC-Download-Tiger.png)
   1. Transfer the downloaded file to the PowerPC Mac via File Sharing
      1. New Macs can still connect to the AFP servers on old versions of OSX.
1. On the PowerPC Mac: Install and run VLC
   1. [Leopard Screenshot](Images/040-PPC/03-PPC-VLC-Leopard.png)
   1. [Tiger Screenshot](Images/040-PPC/04-PPC-VLC-Tiger.png)
1. On the PowerPC Mac: Get the Bonjour Name
   1. Note that this [used to be called Rendezvous](https://www.mactech.com/2004/07/22/apple-to-change-rendezvous-name-in-settlement/). 
      So you might see that name used instead of Bonjour in the UI.
   1. System Preferences→Sharing - [Leopard Screenshot](Images/040-PPC/05-PPC-Bonjour-Leopard.png)
   1. System Preferences→Sharing - [Tiger Screenshot](Images/040-PPC/06-PPC-Bonjour-Tiger.png)
1. On the VM: Start a new, much lower quality stream
   1. ```
      ffmpeg \
        -f avfoundation \
        -framerate 15 \
        -i "0:0" \
        -vf scale=iw/2:ih/2 \
        -vcodec mpeg4 \
        -qscale:v 10 \
        -vtag XVID \
        -acodec libmp3lame \
        -b:a 192k \
        -ac 2 \
        -f mpegts \
        "udp://BonjourName.local:1234?pkt_size=1316"
      ```
1. On the PowerPC Mac: Open VLC and choose File→Open Network - [Screenshot](Images/040-PPC/07-PPC-VLC-Open.png)
   1. `udp://@:1234`
   1. [Successfully Playing Stream Screenshot](Images/040-PPC/08-PPC-VLC-Play.png)
1. (Optional) On the PowerPC Mac: Save the playlist for easy opening later - [Screenshot](Images/040-PPC/09-PPC-VLC-Save.png)

## 🥳Congratulations🎉

You have a successful streaming setup completed. Now its time to optimize 
playback performance for your PowerPC Mac

# Optimize Playback Performance

All of my testing was done on my 1GHz iMac G4. But if you have a more powerful
Mac, you can get more bandwidth and quality out of it. Likewise, if you have 
a slower Mac, you can reduce quality.

To increase the speed of debugging, know that you can press Q to quit the
FFMPEG server and then change the settings and restart it again. The PowerPC 
Mac will just pause until you restart FFMPEG. Note that you can only change 
minor settings like framerate and quality settings. If you change resolution 
or codecs, then you will need to stop the stream on the PowerPC Mac and 
restart it.

## XVID

Overall, I found XVID had the best balance of quality and smoothness on my iMac 
but MPEG 1 was really close as well.

### XVID Command for Copy and Paste

Note I removed the scaling command. You can add it back in if needed.

```
ffmpeg \
  -f avfoundation \
  -framerate 20 \
  -i "0:0" \
  -vcodec mpeg4 \
  -qscale:v 3 \
  -vtag XVID \
  -acodec libmp3lame \
  -b:a 192k \
  -ac 2 \
  -f mpegts \
  "udp://BonjourName.local:1234?pkt_size=1316"
```

### XVID Command Explanation

| Command                                        | Explanation |
|------------------------------------------------|-------------|
| `ffmpeg`                                       |             |
| `-f avfoundation \`                            |             |
| `-framerate 20 \`                              | Lowering the framerate is one of the easiest ways to reduce load on the PowerPC CPU. I find 20 to be the best balance, but set it to a multiple of 60 for best performance. |
| `-i "0:0" \`                                   | This is the AVFoundation device numbers, left is video and right is audio. You shouldn't change this unless you decide not to stream audio (covered below). |
| `-vf scale=720x450 \`                          | Scaling the output size of the video is also one of the easiest ways to reduce load on the PowerPC CPU. I was able to get 720p working just fine on Leopard, but on Tiger I needed to reduce. Use `-vf scale=iw/2:ih/2` to reduce the resolution in half rather than manually calculating it. |
| `-vcodec mpeg4 \`                              | In VLC, the XVID encoder is part of the MPEG4 encoder |
| `-qscale:v 3 \`                                | Lower the video quality also greatly help improve stream performance. 1 is the highest quality and 31 is the lowest, but I found anything above 10 to be very very low quality. I found 3 to always give a good compromise, but on my iMac I can use 1 |
| `-vtag XVID \`                                 | Enables the XVID encoder inside the MPEG4 encoder |
| `-acodec libmp3lame \`                         | Streams as MP3 Audio. This is changeable, but I found MP3 was always easy for the PowerPC to handle |
| `-b:a 192k \`                                  | MP3 bitrate. Good MP3 is usually 192k, 256k, or 320k. Note that because I could not get 44,100Hz audio into FFMPEG successfully, the audio will never really sound good for music until I figure that out. |
| `-ac 2 \`                                      | 2 Channel Audio. |
| `-f mpegts \`                                  | MPEG Transport Stream is the format we are using for streaming. |
| `"udp://BonjourName.local:1234?pkt_size=1316"` | This is the destination of the stream, IP or Bonjour Name work here. ChatGPT said I should specify the packet size to perfectly match MPEGTS, but I am not sure if it helps or not. |

## MPEG1 and MPEG2

My 1GHz G4 was not really powerful enough to play MPEG2 at 720p. While this 
machine can play DVD, you have to remember that DVD is MPEG2 at 480p max. So 
720p is just too much for this CPU. However, MPEG1 did work pretty well at 
about 8MBps of bandwidth. 

### MPEG1 Command for Copy and Paste

Note I removed the scaling command. You can add it back in if needed.

```
ffmpeg \
  -f avfoundation \
  -framerate 20 \
  -i "0:0" \
  -c:v mpeg1video \
  -b:v 8M \
  -maxrate 8M \
  -bufsize 16M \
  -acodec libmp3lame \
  -b:a 192k \
  -ar 32000 \
  -ac 2 \
  -f mpegts \
  "udp://BonjourName.local:1234?pkt_size=1316"
```

### MPEG1 Command Explanation

| Command                                        | Explanation |
|------------------------------------------------|-------------|
| `ffmpeg`                                       |             |
| `-f avfoundation \`                            |             |
| `-framerate 20 \`                              | Lowering the framerate is one of the easiest ways to reduce load on the PowerPC CPU. I find 20 to be the best balance, but set it to a multiple of 60 for best performance. |
| `-i "0:0" \`                                   | This is the AVFoundation device numbers, left is video and right is audio. You shouldn't change this unless you decide not to stream audio (covered below). |
| `-vf scale=720x450 \`                          | Scaling the output size of the video is also one of the easiest ways to reduce load on the PowerPC CPU. I was able to get 720p working just fine on Leopard, but on Tiger I needed to reduce. Use `-vf scale=iw/2:ih/2` to reduce the resolution in half rather than manually calculating it. |
| `-vcodec mpeg1video \`                         | MPEG1 encoder, you can change the 1 to 2 if you think your system can handle MPEG2 |
| `-b:v 8M \`                                    | Average bit rate, lower to increase performance. |
| `-maxrate 8M \`                                | Max bit rate. I always just specify this as the same as the average |
| `-bufsize 16M \`                               | This has to do with how strict the buffering control is. I found double the average bitrate worked best. |
| `-acodec libmp3lame \`                         | Streams as MP3 Audio. This is changeable, but I found MP3 was always easy for the PowerPC to handle |
| `-b:a 192k \`                                  | MP3 bitrate. Good MP3 is usually 192k, 256k, or 320k. Note that because I could not get 44,100Hz audio into FFMPEG successfully, the audio will never really sound good for music until I figure that out. |
| `-ac 2 \`                                      | 2 Channel Audio. |
| `-f mpegts \`                                  | MPEG Transport Stream is the format we are using for streaming. |
| `"udp://BonjourName.local:1234?pkt_size=1316"` | This is the destination of the stream, IP or Bonjour Name work here. ChatGPT said I should specify the packet size to perfectly match MPEGTS, but I am not sure if it helps or not. |

## Streaming without Audio

There are many reasons you may not want to stream with Audio

### Advantages

 - 16,000 Hz just sounds bad. Until I figure out how to get 44,100 Hz out of 
   the Virtual Machine, I don't think its realistic to listen to music via 
   this streaming solution.
 - Your Apple Silicon Mac has better audio connectivity options like Airpods
 - Streaming the audio does take some performance away from the video

### Disadvantages

 - The audio being played on your Apple Silicon Mac will always be 1 or 2 
   seconds out of sync with the video being played on your PowerPC Mac
   - I am open to suggestions on how to manually add an audio delay to the 
     Virtual Machine. But I wasn't able to find anything that would do more 
     than 1 second... which I wasn't sure would really be enough.

### XVID Command without Audio Streaming

 - The `-i` command changed to only include the video `"0:"`
 - Deleted 3 rows related to audio
   - `-acodec libmp3lame \`
   - `-b:a 192k \`
   - `-ac 2 `

```
ffmpeg \
  -f avfoundation \
  -framerate 20 \
  -i "0:" \
  -vcodec mpeg4 \
  -qscale:v 3 \
  -vtag XVID \
  -f mpegts \
  "udp://BonjourName.local:1234?pkt_size=1316"
```

# FAQ

1. Isn't this hard on a Retro Mac?
   1. Yeah, this is very hard on your old Mac. My iMac G4 can do it for hours
      with no stability challenges, but we are talking 100% CPU and many many 
      gigabytes of network traffic.
   1. This is more of a question on outlook of life than anything else. I 
      didn't get my retro Mac so it could sit on the shelf and look pretty. 
      They were meant to be used and so I wanted to use it as my
      secondary/background music screen.
1. Won't this slow down my Apple Silicon Mac?
   1. I have the absolute slowest Apple Silicon Mac. Its an M1 MacBook Air
      with no fan. Its true that this solution takes about 100% of 1 core of 
      the CPU. But I have not noticed any slow downs or throttling. This 
      includes when I am using Xcode to develop software for iOS and Mac apps.
1. There must be a way to get 44,100Hz sampling?
   1. I agree. This has been a big annoyance, but since I listen to the audio 
      on my Airpods anyway instead of through the stream, I haven't dove into
      more than a few hours.
   1. Perhaps if I used a Linux VM this would not be a problem? I also thought
      that it was because I was doing the audio recording in a VM... but I 
      did the exact same setup on the MacBook Air directly and it had the same 
      issue. So, not sure at all what the problem is.
1. Can this be done without an Apple Silicon Mac?
   1. Absolutely! The server could be any machine that runs FFMPEG which is 
      pretty much any machine. I look forward to seeing a future tutorial that 
      uses a Raspberry Pi 🍓
   1. I just created this tutorial using Virtual Buddy and Apple Silicon 
      because thats what I have. But there is no reason the exact same thing 
      can't be done with an Intel Mac and VMWare Fusion 
      (which is free now by the way)
   1. Also, a Virtual Machine is totally not needed. For example you could 
      get one of these [HDMI dummy plugs](https://www.amazon.com/Woieyeks-Virtual-Emulator-Headless-Supports/dp/B0CKKLTWMN/ref=sr_1_3?crid=2I5T3W67D1BIN&dib=eyJ2IjoiMSJ9.V54_l_dy5H-QDQPGm4g64-IB4nnDKf6MMnGSzGvNaa04HMqCrtl_czQuKDGjzR34HWrJ0t9lDaNQOTOnfdk6Kza_xoSY4_EpvtrPCd6MZzrWcPd6eXbIOZ4-kROOlNDpiB3sy7UaIzeBHIYQDTen3DneHtjS2mhbMYfVTxIo9lEHalFwkwXg7XSfYfZh79h9O-EWcOsyRawr2s1EyhAbp0LePhoqbrCzGr0P1Qhg5Io.hdRAftjL-y2y_Og7ss0CIH8VCx6QJecdZOSzQT2h7LM&dib_tag=se&keywords=HDMI%2BDummy%2BPlug&qid=1755416251&refresh=1&sprefix=hdmi%2Bdummy%2Bplug%2Caps%2C283&sr=8-3&th=1) 
      and use that "screen" as the capture device for FFMPEG/AVFoundation and 
      then use your PowerPC Mac as a second monitor for your main Mac. 
      My MacBook Air only supports one  external monitor, so I did not take 
      this approach.
1. Dedicating 4 Cores and 8GB of RAM to this VM seems excessive, no?
   1. I agree. After I got everything all set up and running, I lowered the 
      the CPU Cores to 2 and the RAM to 4GB and macOS Ventura still seems to 
      run fine and stream video with no issues.