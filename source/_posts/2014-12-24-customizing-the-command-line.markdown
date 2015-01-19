---
layout: post
title: "Customizing the Command Line"
date: 2014-12-24 18:09:21 -0500
comments: true
categories: 
---
##Introduction
In my [last post](http://doneallison.com/blog/2014/12/23/creating-a-development-environment-on-my-company-issued-laptop/), I used VirtualBox to create a Lubuntu virtual machine (VM) on a Macbook Pro host. I undertook this project because I wanted to create a POSIX-compliant development environment that will work on my company-issued Lenovo T440. Although I've technically cleared that hurdle at this point, I wanted to customize the VM a bit more so it better resembles the development environment I know and love on my Macbook Pro. Since software developers spend a lot of time using the command line, the next logical step was to customize [Bash](http://en.wikipedia.org/wiki/Bash_%28Unix_shell%29) and [LXTerminal](http://en.wikipedia.org/wiki/LXDE#Software_components).<!--more-->

##Customizing the Bash Prompt
Bourne Again Shell (Bash) is a popular command-line interpreter for Unix-like operating systems. It allows users to control computers by typing text commands into a [terminal emulator](http://en.wikipedia.org/wiki/Terminal_emulator). Before the development of [graphical user interfaces](http://en.wikipedia.org/wiki/Graphical_user_interface) (GUIs), this was the dominant means of interacting with a computer, and it's still quite common. Learning to use the command line is harder than learning to use a GUI, but the command line is generally faster and more powerful when used correctly. In many cases, for example, a sequence of several clicks on various menus can be reduced to a single command.

By default, the Bash _prompt_ (the sequence of symbols that indicates where the user should type commands) on LXTerminal looks as follows in my VM: `don@don-devenv:~$`. This prompt may look complicated, but it's actually quite simple. The `don@don-devenv` simply denotes that user `don` is logged in on the computer named `don-devenv`. The `:~` indicates that the user is in the root directory (`home/don`) of don-devenv (`~` is an alias for `home/don`). The `$` indicates the end of the prompt, where the user should type commands.

I modified this prompt to provide a bit more functionality. First, I added a timestamp to the prompt, which will allow me to tell when I issued certain commands. Second, I added some code that displays the active [Git](http://en.wikipedia.org/wiki/Git_%28software%29) branch when I change directories into a Git repository. This relieves me of having to type `git branch` to retrieve that information. Finally, I changed the `$` to a red `♥`, although I could have chosen a different Unicode character.

Thanks to the good folks at [The Flatiron School](http://flatironschool.com/) (of which I am now a proud alum), implementing these changes was pretty simple. First, I forked their [dotfiles](https://github.com/flatiron-school/dotfiles) repository and made a few small changes to the `.bash_profile` file (my changes are available [here](https://github.com/doneallison/dotfiles/blob/master/bash_profile)). Then, I copied the entire, modified `.bash_profile` file into the root directory on the VM. Finally, I added a short statement to the bottom of the `.bashrc` file on the VM to ensure that Bash loads the custom shell whenever I open LXTerminal. Step-by-step instructions are below.

<p>{% youtube gGDdl5eDR28 %}</p>

1. In the VM, open LXTerminal and navigate to the root directory (type `cd ~`)
1. Type `touch .bash_profile` to create a file for the settings, and then `leafpad .bash_profile` to open it in a text editor
1. Copy the [bash profile](https://github.com/doneallison/dotfiles/blob/master/bash_profile) on my GitHub and paste it into Leafpad
1. Save and close
1. Type `source .bash_profile` to reload the bash profile
1. Type `leafpad .bashrc` to open the `.bashrc` file
1. Type the following text at the bottom of the file: `. ~/.bash_profile`
1. Save and close
1. Exit the terminal and then restart it. The changes should be apparent

##Customizing the Look and Feel of LXTerminal
In addition to adding custom styling to my command prompt, I also like to customize the fonts and colors. In LXTerminal, I was able to do this by editing the `lxterminal.conf` file. First, I typed `leafpad ~/.config/lxterminal/lxterminal.conf` in the command prompt to open the file. Then, I added the following settings:

```
[general]
fontname=DejaVu Sans Mono 14
selchars=-A-Za-z0-9,./?%&#:_
scrollback=1000
bgcolor=#00002b2b3636
bgalpha=65535
fgcolor=#65657b7b8383
disallowbold=false
cursorblinks=false
cursorunderline=false
audiblebell=false
tabpos=top
hidescrollbar=false
hidemenubar=false
hideclosebutton=false
disablef10=false
disablealt=false
```

These settings result in a larger font and a ["solarized"](http://en.wikipedia.org/wiki/Solarisation) color scheme (specific colors courtesy of GitHub user [perusio](https://gist.github.com/perusio/1154002)).

{% img center /images/customizing-lxterminal/customized_terminal.png %}

##Coming Up...
When they're not in the command line, software developers can often be found in the text editor. Accordingly, in my next post, I'll turn my attention to installing and customizing [Sublime Text](http://www.sublimetext.com/), my text editor of choice.