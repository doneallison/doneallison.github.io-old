---
layout: post
title: "Corporate Development Environment, Part III: Customizing Sublime Text"
date: 2015-01-09 12:16:58 -0500
comments: true
categories: 
---
##Introduction
In my last two posts, I've walked through the process of [creating a Lubuntu virtual machine (VM)](http://doneallison.com/blog/2014/12/23/creating-a-development-environment-on-my-company-issued-laptop/) and [customizing its command line](http://doneallison.com/blog/2014/12/24/customizing-the-command-line/) as part of my effort to make a POSIX-compliant development environment on my company-issued Lenovo T440. In this post, I'm going to continue that series by walking through the process of installing and customizing [Sublime Text](http://www.sublimetext.com/), my favorite text editor.<!--more-->

##Install Sublime Text
My first step was to install Sublime Text using [apt-get](http://en.wikipedia.org/wiki/Advanced_Packaging_Tool). The following steps (based [an Ask Ubuntu answer by Jared Burrows](http://askubuntu.com/questions/172698/how-do-i-install-sublime-text-2-3)) worked nicely:

1. Since the official Ubuntu repositories do not contain the Sublime Text package, we have to use the `add-apt-repository` command to add a [PPA](http://en.wikipedia.org/wiki/Personal_Package_Archive) (in this case that of [WebUpd8](http://www.webupd8.org/)) that contains the package. Open LXTerminal and type `sudo add-apt-repository ppa:webupd8team/sublime-text-3`. If you prefer Sublime Text 2, type `sudo add-apt-repository ppa:webupd8team/sublime-text-2` instead.
1. Type `sudo apt-get update` to update apt-get's database of packages that can be installed and where they can be installed from
1. Type `sudo apt-get install sublime-text-installer` to actually install the Sublime Text package. If you're installing Sublime Text 2, the command is `sudo apt-get install sublime-text`

##Register Sublime Text
As its nag screen will remind you, "Sublime Text 2 may be downloaded and evaluated for free, however a license must be purchased for continued use." When you [buy a license](https://www.sublimetext.com/buy), you can get rid of the nag screen and the "UNREGISTERED" label by opening Sublime Text, clicking on `Help > Enter License`, and then pasting your license into the box that appears (including the `----- BEGIN LICENSE -----` and `----- END LICENSE -----` lines).

##Set Personal Preferences
To manage settings, Sublime Text uses configuration files rather than checkboxes and menus. Accordingly, setting preferences (e.g., font size, color scheme, etc.) requires manually editing a configuration file. Although that may seem intimidating if you're accustomed to checkboxes and menus, it's actually straightforward and much more convenient when you're configuring a new system. For example, since I already use Sublime Text on my Macbook Pro, all I had to do was copy my existing settings (a relatively short chunk of text) and then paste them into the configuration file for the version of Sublime Text installed on my VM.

I'll provide my settings below, but chances are you'll want to select your own font, color scheme, etc. You can do so as follows:

1. Open Sublime Text
1. Choose the `Settings - User` option on the `Preferences` menu. This will open a file with some curly braces preceded by the following comment: "Settings in here override those in "Default/Preferences.sublime-settings", and are overridden in turn by file type specific settings."
1. Choose the `Settings - Default` option on the `Preferences` menu. This will open another file with a range of possible settings. As a comment at the top of the file notes, you shouldn't edit this file directly. However, you should browse through an evaluate the types of things you can customize. When you see an option you'd like to change, copy it to the `Settings - User` file and give it your desired value. Then, when you open Sublime Text, the `Settings - User` file will override the `Settings - Default` file and use your preferred settings
1. When you've finished making changes, save and then close the `Settings - User` file

Here are my current settings:
```
{
	"color_scheme": "Packages/Color Scheme - Default/Monokai.tmTheme",
	"font_face": "DejaVu Sans Mono",
	"font_size": 14,
	"ignored_packages":
	[
		"Vintage"
	],
	"scroll_past_end": true,
	"tab_size": 2,
	"word_wrap": true
}
```
Since Lubuntu does not have my beloved Consolas installed, and since I didn't want to go through the trouble of finding and installing it, I chose DejaVu Sans Mono instead. It's been treating me well enough so far.

##Install Package Control
As their website states, [Package Control](https://packagecontrol.io/) is a "Sublime Text package manager that makes it exceedingly simple to find, install and keep packages up-to-date." Since the specific installation instructions vary slightly with different releases of Package Control, I'll just link to the [official installation page](https://packagecontrol.io/installation#st3).

##Install JSHint Package
As I outlined in my post on debugging JavaScript, I find a JavaScript linter invaluable for writing better JavaScript code. Accordingly, I like to use [JSHint](http://jshint.com/), a tool that helps improve the quality of your JavaScript code, in Sublime Text. It can be installed through Package Control as follows:

1. Press `Ctrl + Shift + P` to bring up the Sublime Text Command Palette
1. Begin typing "package" until "Package Control: Install Packages" is visible
1. Select "Package Control: Install Packages"
1. In the menu that appears, type "jshint"
1. Select "JSHint"

If you follow these instructions, the JSHint package will automatically annotate the code in any `.js` file you edit with helpful hints about missing or unnecessary semicolons, duplicate variable declarations, and other common mistakes.

##Other Customizations
Inevitably, you will come up with lots of other customizations you'll want to make to your own installation of SublimeText. The steps in this post, however - especially installing Package Control - are enough to get started.

##Coming Up...
Developers often have to switch rapidly between a text editor, a console, a web browser, and other programs. Accordingly, effective [window management](http://en.wikipedia.org/wiki/Window_manager) is an important part of a productive workflow. In my next post, I'll cover the steps needed to create [Aero](http://en.wikipedia.org/wiki/Windows_Aero)-like [snap](http://windows.microsoft.com/en-us/windows7/products/features/snap) functionality in Lubuntu.