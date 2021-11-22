---
title: "Linux on Windows!"
date: 2021-11-18T13:03:30
categories:
  - windows
tags:
  - linux
---

In my past job I had the chance to work on a project using NodeJS, to be honest, I don't like JavaScript very much (I don't like frontend development) and at that time it was a pain for me as a backend developer.

As time passed by I felt more confident in using javascript and even installed a second OS on my laptop (I installed Ubuntu) just to feel more comfortable developing for NodeJS. I am mainly a .NET developer, which means that at some point I'm forced to use Windows as my main OS or that is what I thought until I heard about WSL which is a Linux running inside windows natively (You don't need a VM) and I'm very happy having the best from both worlds, we can use even docker for Linux instead of installing it on Windows!.

## Prerequisites
- Windows 10/11.
- I highly recommend installing the new and shiny Windows Terminal, just go to the Windows Store and search 'Windows Terminal'.

## Install WSL2
WSL2 is the most recent version of WSL and can be installed in Windows 10 or Windows 11, so that's the version we are going to install.

First, we need to open up a PowerShell or a cmd prompt with elevated privileges, now we will check what distributions do we have available to install running this command:

```
wsl --list --online
```

We will see a list of the available distributions that we can install like this:

```
NAME              FRIENDLY NAME
Ubuntu            Ubuntu
Debian            Debian GNU/Linux
kali-linux        Kali Linux Rolling
Ubuntu-20.04      Ubuntu 20.04 LTS
```
In my case I´d like to install Ubuntu 20.04, since this is the last and default distribution we can just run:

```
wsl --install
```

Or we can specify the Linux distro we like:

```
wsl --install -d Ubuntu-20.04
```

If everything went well, we can now use Ubuntu by opening a new tab in Windows Terminal and selecting Ubuntu. That was not my case, y faced an error:

```
wslRegisterDistribution failed with error: 0x80370102
```

What I did to fix this was enable the CPU Virtualization in the BIOS, which lead us to conclude that what Windows does is create a VM for executing Linux.

That would be all for this time.

Happy coding!
