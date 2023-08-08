# uninstall apache on ubuntu 22.04

# Managing the Apache Process
Now that you have your web server up and running, let’s review some basic management commands using systemctl.
To stop your web server, run:
sudo systemctl stop apache2
To start the web server when it is stopped, run:
sudo systemctl start apache2
To stop and then start the service again, run:


# install apache on ubuntu 22.04
systemctl status httpd
https://www.digitalocean.com/community/tutorials/how-to-install-the-apache-web-server-on-ubuntu-22-04
Step 1 — Installing Apache
Apache is available within Ubuntu’s default software repositories, making it possible to install it using conventional package management tools.
Begin by updating the local package index to reflect the latest upstream changes:
sudo apt update
Then, install the apache2 package:
sudo apt install apache2
After confirming the installation, apt will install Apache and all required dependencies.

# Checking your Web Server
At the end of the installation process, Ubuntu 22.04 starts Apache. The web server will already be up and running.
Make sure the service is active by running the command for the systemd init system:
sudo systemctl status apache2

As confirmed by this output, the service has started successfully. However, the best way to test this is to request a page from Apache.

You can access the default Apache landing page to confirm that the software is running properly through your IP address. If you do not know your server’s IP address, you can get it a few different ways from the command line.

Try writing the following at your server’s command prompt:

Aug 07 15:41:12 moto.BUSCHE-CNC.com apachectl[286035]: AH00558: apache2: Could not reliably determine the server's fully qualified domain name, using moto.BUSCHE-CN>
hostname -I
10.1.1.83 172.19.0.1 172.17.0.1 10.1.210.64 
You will receive a few addresses separated by spaces. You can try each in your web browser to determine if they work.
Another option is to use the free icanhazip.com tool. This is a website that, when accessed, returns your machine’s public IP address as read from another location on the internet:
curl -4 icanhazip.com
When you have your server’s IP address, enter it into your browser’s address bar:
http://your_server_ip
http://10.1.1.83
curl -k https://moto
curl http://10.1.1.83

netstat -ntlp
netstat -anp | grep 80

https://www.golinuxcloud.com/openssl-create-client-server-certificate/

Configure Apache with SSL (HTTPS)
I will not go much into the detail steps to configure Apache with HTTPS as that in not our primary agenda of this article. I will configure a basic webserver to use Port 8443 on centos8-3

 https://httpd.apache.org/docs/2.4/ssl/ssl_howto.html

 Step 3 — Checking your Web Server
At the end of the installation process, Ubuntu 22.04 starts Apache. The web server will already be up and running.
Make sure the service is active by running the command for the systemd init system:
sudo systemctl status apache2