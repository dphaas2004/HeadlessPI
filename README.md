# HeadlessPI
Headless Install on a Rasberry Pi 4B (Bonus IOT setup)

There are a lot of tutorials online for this already so this will mainly be a summary however not all explain how to do this without a display or keyboard which is why i documented my process.  The best one I have seen is here https://github.com/BretStateham/headlesspi and really covers everything I do and much more and explains what is going on in much more detail.  So check that out first.  

# Disclaimer
These are my notes and they may not be fully complete or accurate.

# Things you'll Need
- imaging software.  i have used win32diskimager and balenaEtcher. https://www.balena.io/etcher/  or there is one on Raspberry Pi foundations site below(i have not used this one)
- program editor (not really needed but better for modifying the configuration files notepad should work)
- Rasbian Buster image https://www.raspberrypi.org/downloads/
- USB to serial adapter of sorts.  a TTL breakout like the FT232R works perfect for this.  https://www.sparkfun.com/products/12731
- VNC Viewer.  i like realvnc as it has the proper encoding for linux systems.  i have had issues with tight in the past.  use what you want.  https://www.realvnc.com/en/connect/download/viewer/

 # Getting things ready to go.
 - flash the image to the SD Card using the imaging software from above.
 - Modify the config.txt file to enable the serial interface
 -- add enable_uart=1 to the end of the file.
 - Modify the cmdline.txt file possibly?
 -- check the this file to ensure that it contains console=serial0,115200.  it should have this console as well as the console=tty1
 -- sample cmdline.txt console=serial0,115200 console=tty1 root=PARTUUID=d9b3f436-02 rootfstype=ext4 elevator=deadline fsck.repair=yes rootwait quiet init=/usr/lib/raspi-config/init_resize.sh splash plymouth.ignore-serial-consoles
 - connect the ground Tx and Rx to the Raspberry Pi

 # First Boot
 - before we boot we need to setup a serial window on our computer we will use to interact with the Raspberry Pi.  I use putty for this but it should be possible using terminal services or minicom if you are using linux
 - open it up set to your adapters com port and set the baud to 115200.  default parity 181n will work fine.
 - insert the SD card and power on the PI.
--	~17sec until first response
--	~60sec to login screen
 - log in with the default username (pi) and passowrd (raspberry)

 # Connect to wireless (the easy way) and setup for better access
 - the easy way is to use the raspberry pi configurator.
 -- sudo raspi-config
 --- 2 network options
 ----enable the predicatable network interface
 ----enter ssid and passphrase
 # Optional
 --- 5 interfacing options
 ---- enable SSL
 ---- enable VNC
 ----- if you enable VNC set a default resolution in the advanced options or you will have problems
 --- scroll down to finish and hit enter......first reboot
 - go ahead and close the terminal we dont need it anymore

 # Launch VNC and base setup
 - you will need to find you devices ip address. the easy way to do this is through your routers DHCP leases.
 - go through the pi welcome screen.
 -- go ahead and skip the network connection (we already did that) and the update software (we'll do that in a minute.)
 -- select reboot (if you changed your password you will need to relaunch the VNC viewer.

 # Make sure everything is up to date
 - open terminal
 -- sudo apt-get update
 -- sudo apt-get upgrade


# Optional IOT Setup
## Install web server
 - sudo apt-get install apache2

## Check apaches installation is working
 - start the apache service.
 -- /etc/init.d/apache2 start
 -- check http://localhost

## Enable CGI
- sudo a2enmod cgi
- restart apache
- sudo /etc/init.d/apache2 restart
-- or systemd way. sudo systemctl reload apache2

## make test perl file
- sudo nano /www/html/test.pl
#!/usr/bin/perl
print "Content-type: text/html\n\n";
print "Hello, World.";

## setting the permissions properly
https://httpd.apache.org/docs/2.4/howto/cgi.html#permissions
- chmod a+x /www/html/test.pl
- chown www-data /www/html/test.pl

## wiringpi - please checkout this project in full as the developer is no longer supporting it.
http://wiringpi.com/download-and-install/
- check to make sure wiringPi is version 2.52
-- gpio -v
- sudo apt-get purge wiringpi
-- hash -r
-- cd /tmp
- wget https://project-downloads.drogon.net/wiringpi-latest.deb
-- sudo dpkg -i wiringpi-latest.deb

# Other Things
- You may need to set to disable bluetooth in your configuration file as it was at one point having weird issues with the wifi card.
-- dtoverlay=pi3-disable-bt-overlay

