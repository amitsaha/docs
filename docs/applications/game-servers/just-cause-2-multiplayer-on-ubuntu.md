---
author:
    name: Linode Community
    email: docs@linode.com
description: 'Install and configure a Just Causew 2 Multiplayer server on Ubuntu 14.04.'
keywords: 'just cause,just cause 2,game servers,games,ubuntu, ubuntu 14.04,steam'
license: '[CC BY-ND 3.0](http://creativecommons.org/licenses/by-nd/3.0/us/)'
published: 'Friday, October 9th, 2015'
modified: Friday, October 9th, 2015
modified_by:
    name: Linode
title: 'Install and Configure a Just Cause 2 Multiplayer Server on Ubuntu 14.04'
contributor:
    name: Andrew Gottschling
    link: https://github.com/agottschling
---

*This is a Linode Community guide. [Write for us](/docs/contribute) and earn $250 per published guide.*

<hr>

Just Cause 2 is a single-player game published by Square Enix. Because it has no multiplayer mode, the modding community has created a multiplayer mod for the game that is quite popular. This guide will explain how to prepare your VPS, install SteamCMD and then install and configure Just Cause 2's multiplayer mod.

## Before You Begin

Have the following items available:

- A [Steam](http://store.steampowered.com) account.
- A copy of [Just Cause 2](http://store.steampowered.com/app/8190/) that you have purchased on Steam.
- A copy of the [Just Cause 2 Multiplayer Mod](http://store.steampowered.com/app/259080/) that has been added to your steam library.
- An up-to-date Linode running Ubuntu 14.04. We suggest you follow our [Getting Started](/docs/getting-started) guide for help configuring your Linode.

{: .note }
>This guide is written for a non-root user. Commands that require elevated privileges are prefixed with `sudo`. If you’re not familiar with the sudo command, reference the [Users and Groups](/docs/tools-reference/linux-users-and-groups) guide.

## Preparing your Linode

Just Cause 2 is sold on Steam, along with its multiplayer mod. To download and maintain servers for the game, we will use SteamCMD.

Because current generation Linodes run a 64-bit operating system, we need to download a few extra libraries in order to run SteamCMD.

1.  Configure the package manager to include packages for i386 architecture:

        sudo dpkg --add-architecture i386

2.  Update the system:

        sudo apt-get update && sudo apt-get upgrade

3.  Install the 32-bit libraries required:

        sudo apt-get install lib32gcc1 lib32stdc++6 libc6-i386 libcurl4-gnutls-dev:i386 screen

    {: .note }
    > If you're running a legacy Linode on a 32-bit kernel, install these packages instead:
    >
    >     sudo apt-get install libcurl4-gnutls-dev:i386 libc6-i386 libgcc1 screen

If you have a firewall running on your Linode, add exceptions for SteamCMD:

    sudo iptables -A INPUT -p udp- m udp --sport 4380 --dport 1025:65355 -j ACCEPT
    sudo iptables -A INPUT -p udp -m udp --sport 10999 --dport 1025:65355 -j ACCEPT
    sudo iptables -A INPUT -p udp -m udp --sport 7777 --dport 1025:65355 -j ACCEPT

{: .note }
> If you've configured your firewall according to our [Securing Your Server](/docs/security/securing-your-server) guide, be sure to add these port ranges to your `/etc/iptables.firewall.rules` file.

## Install SteamCMD and Just Cause 2

1.  From your user's home folder, download SteamCMD into its own directory:

        mkdir steamcmd
        cd steamcmd
        wget http://media.steampowered.com/installer/steamcmd_linux.tar.gz

3.  Extract the package and remove the archive file:

        tar -xvzf steamcmd_linux.tar.gz
        rm steamcmd_linux.tar.gz

4.  Run the SteamCMD Installer:

        ./steamcmd.sh

    This command will display output similar to this:

        Redirecting stderr to '/home/linode/Steam/logs/stderr.txt'
        [  0%] Checking for available updates...
        [----] Downloading update (0 of 7,013 KB)...
        [  0%] Downloading update (1,300 of 7,013 KB)...
        [ 18%] Downloading update (3,412 of 7,013 KB)...
        [ 48%] Downloading update (5,131 of 7,013 KB)...
        [ 73%] Downloading update (6,397 of 7,013 KB)...
        [ 91%] Downloading update (7,013 of 7,013 KB)...
        [100%] Download complete.
        [----] Installing update...
        [----] Extracting package...
        [----] Extracting package...
        [----] Extracting package...
        [----] Installing update...
        [----] Installing update...
        [----] Installing update...
        [----] Cleaning up...
        [----] Update complete, launching Steam...
        Redirecting stderr to '/home/linode/Steam/logs/stderr.txt'
        [  0%] Checking for available updates...
        [----] Verifying installation...
        Steam Console Client (c) Valve Corporation
        -- type 'quit' to exit --
        Loading Steam API...OK.

        Steam>

    The `Steam>` prompt is similar to the Linux command prompt, with the exception of its not being able to execute normal Linux commands. 

4.  Install JC2-MP from the SteamCMD prompt:

        login anonymous
        force_install_dir ../jc2mp-server
        app_update 261140 validate

    This can take some time. If the download looks as if it has frozen, be patient; it may take about 10 minutes. Once the download is complete, you should see this output:

        Success! App '261140' fully installed.

        Steam>

5.  Finally, exit SteamCMD:

        quit

##Configuring Just Cause 2 - Multiplayer (JC2-MP)

1.  Before you configure JC2-MP, make a copy of the default configuration file:

        cd ~/jc2mp-server
        cp default_config.lua config.lua

2.  Open the configuration file with `nano` to edit the configuration. Every possible server option is explained in the configuration file. Simply follow the instructions:

        nano config.lua

3.  When you are finished, exit `nano` and save your changes.

##Using the Server

1.  Before starting up the server for the first time, it is good to symlink a library file to avoid a possible error:

        cd ~/jc2mp-server
        ln -s ~/steamcmd/linux32/libstdc++.so.6 libstdc++.so.6

2.  To start the server, simply run the executable: 
        screen ./Jcmp-server
        
3.  To detach from the screen session running the server console, press these two key-combinations in succession:

    **CONTROL + A**<br>
    **CONTROL + D**

4.  To bring the console back, type the following command:

        screen -r

5.  To stop the server, either bring back the console or type **quit**.

## Entering The Server

[![JC2-MP Server with users on it](/docs/assets/JC2running_resized.png)](/docs/assets/JC2running.png)

Now that you have installed and configured Just Cause 2 - Multiplayer, you have your very own Just Cause 2 server for you and your friends to play on. Your users can access the server by opening the server list, finding your server’s name, clicking **Connect**, and then entering a password - if you choose to set one.
