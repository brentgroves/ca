https://linux.how2shout.com/3-ways-to-change-hostname-on-ubuntu-20-04-lts-focal-fossa-linux/
3 Ways to Change Hostname on Ubuntu 22.04 or 20.04 LTS
Last Updated on: June 27, 2023 by Heyan Maurya
If you are using Ubuntu 20.04 LTS Focal Fossa, then here are the commands and GUI method to change the HOSTNAME or Device name.

What is a hostname in Linux?

In plain words, if we have multiple Linux computers in a network then to identify each of them using a custom name, we can generate hostnames, custom names generated by the user. Well, while setting up any OS, the hostname is usually set by it automatically depending upon the system. However, there are many scenarios where we need to set the one manually to easily identify the system over a network so that there will not be any kind of conflict relating to the hostname.

Ways to Change Hostname on Ubuntu 20.04 LTS via CLI or GUI
The given steps will also work for Ubuntu 22.04, 21.04, 19.04,18.04; Linux Mint, MX Linux, Rocky and other Linux systems.

Identify existing Hostname
Your system will already have a pre-assigned hostname, let’s first check what is that? The command to print the current hostname on Ubuntu Linux is:

hostname
or

hostnamectl

Change Ubuntu 20.04 hostname for the current session only
If you just want to change the hostname for temporary until your current session is not over or you have not logged out/rebooted your system; use the given command in the terminal.

sudo hostname moto.busche-cnc.com
Replace the new one with the hostname you want to assign to your system temporarily. To see the changes,  initialize your current environment or rerfersh your current session using:

newgrp

Use hostnamectl set-hostname to change without rebooting (permanent)
Well, if you want to change your hostname permanently without rebooting your Ubuntu Linux, then use the “hostnamectl set-hostname” command.

sudo hostnamectl set-hostname moto.busche-cnc.com
To see the changes on your terminal, refresh the session:

newgrp