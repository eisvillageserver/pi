#Pi Initial Setup
There are several packages required to setup the Pi as a Wireless Access Point with the intention of running our web application

##Flash Raspbian Lite
* Download from (https://www.raspberrypi.org/downloads/raspbian/)
* Use Win32DiskImager (if on windows) to write to SD card

##Update all Packages using :
* `sudo apt-get update`
* `sudo apt-get upgrade`

##The Raspberry Pi requires the following packages to be installed as follows
* `sudo apt-get install dnsmasq`
* `sudo apt-get install hostapd`
* `sudo apt-get install python-pip`
* `sudo apt-get install ppp screen elinks`
* `sudo apt-get install git`

#Configuring Network settings

1. We need to give the Pi a static IP address. Edit the file /etc/network/interfaces and replace the line "iface wlan0 inet dhcp" to:
```
allow-hotplug wlan0
auto wlan0
iface wlan0 inet static
	address 192.168.1.1
	netmask 255.255.255.0
```

#ConfiguringHostAPD

1. To create an open wireless network, edit the file /etc/hostapd/hostapd.conf (create it if it doesn't exist) and add the following lines:
```
interface=wlan0
driver=nl80211 #this value changes depending on the wifi dongle
ssid=Apoint
hw_mode=g
channel=6
auth_algs=1
wmm_enabled=0
```

2. Edit the file /etc/default/hostapd and change the line:
`#DAEMON_CONF=""`
to:
`DAEMON_CONF="/etc/hostapd/hostapd.conf`


#Configuring DHCP Server (Using dnsmasq)
Any DHCP package can be used. Dnsmasq is more lightweight than isc-dhcp-server.

1. Edit the file /etc/dnsmasq.conf and add the follow lines:
```
interface=wlan0
dhcp-range=192.168.1.50,192.168.1.150,12h
```

#Configuring custom Domain Name for Raspberry Pi
The DNS server will automatically check hosts file to resolve any internal domains

1. Edit the file /etc/hosts and add the line to the end:
`192.168.1.1	apoint.io`

#Confguring files for GSM (fona from adafruit)

1. In the directory /etc/ppp/peers , download a file using the command:
`wget https://raw.githubusercontent.com/adafruit/FONA_PPP/master/fona`

2. In the downloaded file, edit the lines:
`connect "/usr/sbin/chat -v -f /etc/chatscripts/gprs -T ****"`
Replace the **** with the APN value of the network carrier. This can be found on the
network carriers website or provided by the network carriers support desk.

`#/dev/ttyAMA0`
to
`/dev/ttyAMA0`

#Clone the Web application
1. Clone our web application using git
`Git clone https://github.com/eisvillageserver/integration.git`

#Note
* All the modified file are in the repository in directory /etc
* When Downloading new content to Raspberry Pi, disable the DHCP server with the command
`Sudo service dnsmasq stop`
