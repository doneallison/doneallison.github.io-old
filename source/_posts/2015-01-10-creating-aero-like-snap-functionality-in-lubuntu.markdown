---
layout: post
title: "Corporate Development Environment, Part IV: Creating Aero-Like Snap Functionality in Lubuntu"
date: 2015-01-10 11:22:49 -0500
comments: true
categories: 
---
##Introduction
In my last three posts, I've walked through the process of [creating a Lubuntu virtual machine (VM)](http://doneallison.com/blog/2014/12/23/creating-a-development-environment-on-my-company-issued-laptop/), [customizing its command line](http://doneallison.com/blog/2014/12/24/customizing-the-command-line/), and [customizing Sublime Text](http://doneallison.com/blog/2015/01/09/customizing-sublime-text/) as part of my effort to make a POSIX-compliant development environment on my company-issued Lenovo T440. In this post, I'm going to conclude that series by outlining the process of creating [Aero](http://en.wikipedia.org/wiki/Windows_Aero)-like [window snap](http://windows.microsoft.com/en-us/windows7/products/features/snap) functionality in Lubuntu.<!--more-->

##Editing the OpenBox Configuration File
The process is surprisingly straightforward. All we need to do is open a configuration file for [OpenBox](http://openbox.org/wiki/Main_Page), Lubuntu's default window manager, and add a few lines that describe the intended functionality. The following steps worked nicely for me and are based off an [Ubuntu Forums post](http://ubuntuforums.org/showthread.php?t=2076433) by ronniew (I made some small changes to the code, but the bulk of it came from ronniew's post).

1. Open the OpenBox configuration file in Sublime Text by opening LXTerminal and typing `subl ~/.config/openbox/lubuntu-rc.xml`
1. Find the line that reads `<chainQuitKey>C-g</chainQuitKey>` (in the default config file on my installation of Lubuntu, this is line 180)
1. Under that line, paste the following text:

		<!-- Aero Snap for Openbox -->
		    <keybind key="W-Left">        # HalfLeftScreen
		      <action name="Raise"/>
		      <action name="UnmaximizeFull"/>
		      <action name="MoveResizeTo">
		        <x>0</x>
		        <y>0</y>
		        <height>100%</height>
		        <width>50%</width>
		      </action>
		    </keybind>
		    <keybind key="W-Right">        # HalfRightScreen
		      <action name="Raise"/>   
		      <action name="UnmaximizeFull"/>
		      <action name="MoveResizeTo">
		        <x>-0</x>
		        <y>0</y>
		        <height>100%</height>
		        <width>50%</width>
		      </action>
		    </keybind>
		    <keybind key="W-Up">        # HalfUpperScreen
		      <action name="Raise"/>      
		      <action name="UnmaximizeFull"/>
		      <action name="MoveResizeTo">
		        <x>0</x>
		        <y>0</y>
		        <width>100%</width>
		        <height>50%</height>
		      </action>
		    </keybind>
		    <keybind key="W-Down">        # HalfLowerScreen
		      <action name="Raise"/>
		      <action name="UnmaximizeFull"/>
		      <action name="MoveResizeTo">
		        <x>0</x>
		        <y>-0</y>
		        <width>100%</width>
		        <height>50%</height>
		      </action>
		    </keybind>
		    <keybind key="W-f">        #Full Screen
		      <action name="Raise"/>
		      <action name="ToggleMaximizeFull"/>
		    </keybind>
1. Save and then close the configuration file
1. Go back into LXTerminal and type `openbox --restart`

As is possible in Windows 7, you should now be able to snap a window to any "half" of the screen (left, right, top, bottom) by pressing the super key (Windows key on PC; Command key on Mac) and the appropriate arrow key. You should also be able to make a window full screen by pressing `Super + F`.

I find these shortcuts valuable for times when I want to write (e.g., in Sublime Text) while having another document open for reference (e.g., in a web browser), or when I want to test code (e.g., in LXTerminal) while I edit it (e.g., in Sublime Text).