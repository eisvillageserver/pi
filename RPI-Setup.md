# Pi setup Guid

#Adding  account
```
sudo useradd -m eis
sudo passwd eis
sudo usermod -a -G sudo eis
```


remove known hosts on terminal
```
rm -f .ssh/known_hosts
```

=======

Update all Packages using :

```
sudo apt-get update
sudo apt-get upgrade
```

install dhcp

```
sudo apt-get install isc-dhcp-server
```

HostAPD
```
wget https://github.com/jenssegers/RTL8188-hostapd/archive/v1.1.tar.gz
tar -zxvf v1.1.tar.gz
cd RTL8188-hostapd-1.1/hostapd
sudo make
sudo make install
```
configure dhcp
```
sudo nano /etc/dhcp/dhcpd.conf
```
Find the following section and comment it out by placing a hashtag at the beginning of the line.

```
option domain-name "example.org";
option domain-name-servers ns1.example.org, ns2.example.org;
```
Next find the section below and un-comment the word authoritative (remove hastag):

```
# If this DHCP server is the official DHCP server for the local
# network, the authoritative directive should be uncommented.
#authoritative;
```
Next we need to define the network and network addresses that the DHCP server will be serving. This is done by adding the following block of configuration to the end of file:

```
subnet 192.168.10.0 netmask 255.255.255.0 {
 range 192.168.10.10 192.168.10.20;
 option broadcast-address 192.168.10.255;
 option routers 192.168.10.1;
 default-lease-time 600;
 max-lease-time 7200;
 option domain-name "local-network";
 option domain-name-servers 8.8.8.8, 8.8.4.4;
}
```


```
sudo nano /etc/default/isc-dhcp-server
```
Scroll down to the line saying interfaces and update the line to say:

```
INTERFACES="wlan0"
```

**Configuring HostAPD**

```
sudo nano /etc/hostapd/hostapd.conf
```

The standard configuration will create a new wireless network called wifi with the password YourPassPhrase. You can change the parameter “ssid=wifi” to the SSID wifi name you want and the parameter “wpa_passphrase=YourPassPhrase” to your own password.

**Enable NAT**

to forward traffic from wlan1 to wlan0
```
sudo nano /etc/sysctl.conf
```
Scroll down to the last line of the file and add the line:

```
net.ipv4.ip_forward=1
```
```
sudo sh -c "echo 1 > /proc/sys/net/ipv4/ip_forward"
```
Next step is setting up the actual translation between the ethernet port called eth0 and the wireless card called wlan0.
```
sudo iptables -t nat -A POSTROUTING -o wlan1 -j MASQUERADE
sudo iptables -A FORWARD -i wlan1 -o wlan0 -m state --state RELATED,ESTABLISHED -j ACCEPT
sudo iptables -A FORWARD -i wlan0 -o wlan1 -j ACCEPT
```


static IP wo wlan0
```
sudo ifdown wlan0
```
```
sudo nano /etc/network/interfaces
```


```
auto lo

iface lo inet loopback
iface eth0 inet dhcp

allow-hotplug wlan0
iface wlan0 inet static
address 192.168.10.1
netmask 255.255.255.0


allow-hotplug wlan1
iface wlan1 inet dhcp
wpa-conf /etc/wpa_supplicant/wpa_supplicant.conf
iface default inet dhcp

up iptables-restore < /etc/iptables.ipv4.nat
```

Starting your wireless router
```
sudo service isc-dhcp-server start
sudo service hostapd start
```

to re-run dhcp and hostapd after a reboot

run this
```
sudo ln -s /etc/init.d/hostapd /etc/rc2.d/S02hostapd at the end to correct problem with
sudo update-rc.d hostapd enable
sudo update-rc.d isc-dhcp-server enable
```
http://www.raspberrypi.org/forums/viewtopic.php?f=63&t=37898

set root password
```
sudo passwd root
```

content  of supplicant.conf for wifi settings
```
sudo nano /etc/wpa_supplicant/wpa_supplicant.conf
```

```
ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev
update_config=1

network={
        ssid="WIFI"
        psk="wifipassword"
        proto=WPA
        key_mgmt=WPA-PSK
        pairwise=TKIP
        group=TKIP WEP104 WEP40
        auth_alg=OPEN
}

network={
        ssid="ubcvisitor"
        key_mgmt=NONE
        auth_alg=OPEN
}
```

saving NAT configurations
Run this command to backup the NAT configuration.

```
sudo sh -c "iptables-save > /etc/iptables.ipv4.nat"
```
incase after reboot the iptable rulls didn’t load!

```
apt-get install iptables-persistent
```
#Github access

```
sudo apt-get install git

git clone
sudo git branch
git checkout handheld_v1
sudo git pull
to ignore your own changes
git stash

git stash clear
```
