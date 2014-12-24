---
layout: post
title: "Creating a Development Environment on My Corporate Laptop"
date: 2014-12-23 22:21:47 -0500
comments: true
categories: 
---
##Background and Motivation
Since The Flatiron School Science Fair a couple weeks ago, my attention - like that of most of my classmates - has turned to finding a job. After shelling out for tuition and going three months without income, we're all eager to begin earning money again. For a variety of reasons, I've decided to return to the position I held before coming to The Flatiron School. Although I'm really excited about this move, it does come with an unfortunate downside: having to reacclimate to my company-issued Lenovo T440 after having spent a glorious three months with my beloved Macbook Pro.

Don't get me wrong; it could be worse. Apart from its horrendous touchpad, the T440 isn't a bad computer. It even has a pretty good display, especially if you haven't yet been spoiled by a Retina Display. For a software developer, however, the T440 has a major flaw: it runs Windows 7. This is a problem not because Windows 7 is bad, but because Windows 7, like all other versions of Windows, is incompatible with [POSIX](http://en.wikipedia.org/wiki/POSIX).

First released in 1988, POSIX (Portable Operating System Interface) is a set of standards for designing operating systems. It reduces compatibility issues between compliant operating systems by defining common [APIs](http://en.wikipedia.org/wiki/Application_programming_interface) and system utilities. POSIX is supported to some degree by most Unix-like operating systems, including Mac OS X (fully POSIX compliant) and GNU/Linux (mostly POSIX compliant).

Since many software developers use Unix-like systems to write or deploy code, having a POSIX-compliant system makes it easier to share code with other developers. Moreover, I've spent the last three months developing habits in a POSIX-compliant environment. Switching now would mean accepting a steep new learning curve to learn tools and patterns with limited relevance outside the (decreasingly important) Windows universe. Accordingly, I decided to research ways of bringing POSIX to the workplace.

##Making Windows POSIX-Compliant
Since my bosses are unlikely to spring for a Macbook Pro, and since I can't simply wipe Windows 7 and install a fresh copy of Ubuntu, I had to find some way of creating a POSIX-compliant environment within Windows 7. After a bit of research, two popular alternatives emerged: Cygwin and virtualization.

###Cygwin
[Cygwin](http://en.wikipedia.org/wiki/Cygwin) is a Windows program that provides a Unix-like command line and some degree of POSIX compliance. Although I don't fully understand Cygwin and could certainly be selling it short, it seems most valuable in providing a Unix-like alternative to Windows' stock Command Prompt. If you want to do something more complex, like use RVM to manage multiple versions of Ruby, my sense is that you begin to reach Cygwin's limits. In addition, Cygwin would likely involve somewhat of a learning curve. For these reasons, I decided to leave Cygwin as an alterantive to explore if my preferred option, virtualization, didn't work.

###Virtualization
In the context of this post, [virtualization](http://en.wikipedia.org/wiki/Virtualization) refers to the process of running a virtual instance of one computer atop the resources of a second, actual computer. Virtualization seemed like a compelling solution to the problem of bringing POSIX to Windows because it promised the ability to run mostly POSIX-compliant GNU/Linux atop Windows 7. In other words, I'd be able to run GNU/Linux on my work computer without wiping or partitioning the hard drive.

Virtualization, of course, is not magic. It comes with a performance penalty. Since the _host_ operating system (in my case, Windows 7) has to share physical resources like RAM with the _guest_ operating system (in my case, GNU/Linux), both systems take a performance hit. Thankfully, though, the type of software development I plan on doing is not especially resource-intensive. Accordingly, I decided to build a virtual GNU/Linux machine to run atop my company-issued laptop.

Since I'm traveling for the holidays and don't have access to the T440, I decided to create the virtual machine (VM) on my Macbook Pro and then transfer it later to the T440. My ability to do this highlights one of the beautiful aspects of virtualization: that the guest OS is agnostic about its host as long as the host has compatible virtualization software and sufficient physical resources. When it comes time to move the VM, all I'll have to do is copy over a single image file.

##Choosing and Obtaining Software
For the actual virtualization software, I went with [VirtualBox](https://www.virtualbox.org/). It's not quite as polished as something like VMWare, but it's powerful, cross-platform, and free. For the GNU/Linux distribution, I went with [Lubuntu](http://lubuntu.net/), which is based on [Ubuntu](http://www.ubuntu.com/desktop) but uses the light-weight [LXDE](http://lxde.org/) desktop environment. Lubuntu is a good fit because it was designed with resource-constrained systems in mind but is largely compatible with Ubuntu, which has a large, active, and helpful community. Since I decided I could only devote 1GB of RAM to the birtual machine, I grabbed the 32-bit version of Lubuntu from the [official downloads page](https://help.ubuntu.com/community/Lubuntu/GetLubuntu#Standard_PC).

##Creating the Virtual Machine
Having installed VirtualBox and downloaded the ~740MB Lubuntu .iso, I was ready to get started. The first step was to create the VM in VirtualBox. During this process, I accepted most of the defaults apart from selecting the correct OS (32-bit Ubuntu Linux), entering a unique name ("devenv"), and changing the default memory allocation from 512MB to 1024MB. 

{% youtube xh35wRElX6U %}


The next step was to boot the VM by clicking on it in the VirtualBox sidebar. Since I hadn't installed an operating system yet, VirtualBox prompted me to select the media or image to boot from. I selected the Lubuntu .iso, and VirtualBox began running it in a separate window. From that point on, I followed a standard installation process in the VM. (Please note: The video below has been edited for length. The actual installation process took about 11 minutes on my computer.)

{% youtube 724Ey73Q2n8 %}


After completing the installation process, I rebooted the VM to arrive at my fresh Lubuntu desktop.

{% img center /images/corporate-environment/lubuntu_desktop.png %}


##Incorporating Guest Additions

After booting the VM, the first thing I noticed was how low the resolution was - only 640x480. In an effort to fix this, I clicked `Start > Preferences > Monitor Settings`. This brought up the menu I was looking for, but the only other option was "Auto." This setting didn't change anything, so I started Googling around. It turns out that I needed to install a VirtualBox package called "Guest Additions" on the VM. Although the image needed to install Guest Additions comes bundled with VirtualBox, it's not the easiest thing to find and install. After some experimentation, I got it to work with the process described below. Please note that steps 1-4 may vary on non-Mac systems; however, it basically just involves copying a file to a convenient directory and then later selecting that file.

{% youtube Y2TcQK49pbA %}


1. Copy `VBoxGuestAdditions.iso` image from the VirtualBox directory to the root directory. You can do this through Finder, but the following terminal command also works: `sudo cp /Applications/VirtualBox.app/Contents/MacOS/VBoxGuestAdditions.iso ~/`
1. Start the VM and log in.
1. On the VirtualBox menu, go to `Devices > CD/DVD Devices > Choose a virtual CD/DVD disk file...`
1. Navigate to the root directory and select the `VBoxGuestAdditions.iso` file you copied there in step 1.
1. In the VM, close the autorun prompt that appears after a few moments and press `Ctrl + Alt + T` to open LXTerminal.
1. Type `cd ../../media/USER`, where USER is your username (mine is "don"), to change into the VBoxGuestAdditions.iso directory.
1. Type `ls` to show the contents of that directory. There should only be one directory, called something like `VBOXADDITIONS_4.3.20_96996` (it changes with each version). Type `cd` followed by a space and then the name of that directory to enter it. In practice, you can probably just type `cd V`, and then press tab to auto-complete the filename, and then press enter.
1. Type `sudo apt-get update` to grab any updates to packages installed on your system.
1. Type `sudo apt-get upgrade` to install those updates.
1. Type `sudo apt-get install dkms` to install [dkms](http://en.wikipedia.org/wiki/Dynamic_Kernel_Module_Support).
1. Type `sudo sh VBoxLinuxAdditions.run` to install Guest Additions.
1. Restart the VM.

After following these steps, I restarted the VM and was able to put it in full-screen mode by pressing `Right ⌘ + F` (the default is `Left ⌘ + F`, but I changed it to right). Since Lubuntu puts the menu bar at the bottom of the screen, I removed the auto-hiding menu bar that VirtualBox puts on the bottom of the screen by default. I did this by right-clicking the VM in the VirtualBox sidebar, selecting `Advanced` on the `General` menu, and deselecting `Show in Fullscreen/Seamless`.

The steps outlined in this post left me with a basic virtual machine that I will be able to run on my company-issued T440. But the VM still doesn't feel like _home_. In my next few posts, I'll cover a few of the other customizations I made to complete my corporate development environment.