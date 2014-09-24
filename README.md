Installation and Usage
============

1. perform default install of Debian for ARM on a Beaglebone 
2. install dependencies `apt-get install python-crypto python python-impacket python-pcapy libpcap0.8 bridge-utils ebtables arptables hostapd macchanger git openssh-server` 
3. change necessary config files to make the device stealthy enough during bootup
4. make sure autosniff.py starts on bootup

5. connect two usb ethernet dongles and reboot the device (you need two because the builtin ethernet won't support promiscuous mode)

6. perform physical ethernet cable beagle in the middle and wait for dhcp on the wireless AP



Deps
=====
Also see apt-get command in the steps above.

python2
pycrypto
python2-pcapy
impacket
libpcap
bridge-utils
ebtables
iptables
arptables
hostapd

Stealth
========

```
#run following commands
update-rc.d apache2 disable
echo "net.ipv6.conf.all.disable_ipv6 = 1" > /etc/sysctl.d/40-ipv6.conf
update-rc.d avahi-daemon remove

```

Comment the first rule "BeagleBone: net device ()" in /etc/udev/rules.d/70-persistent-net.rules because it stops additional ethernet interfaces from working.





hostapd.conf

```
interface=wlan0
ssid=NothingToSeeHere
channel=1
#bridge=br0

# WPA and WPA2 configuration

macaddr_acl=0
auth_algs=3
ignore_broadcast_ssid=0
wpa=3
wpa_passphrase=hackallthethings
wpa_key_mgmt=WPA-PSK
wpa_pairwise=CCMP TKIP
rsn_pairwise=CCMP

# Hardware configuration

driver=rtl871xdrv
ieee80211n=1
hw_mode=g
device_name=RTL8192CU
manufacturer=Realtek

```

/etc/rc.local

```
cd /root/BitM/ && python autosniff.py &

ifconfig wlan0 169.254.44.44/24
ifconfig wlan0 up
udhcpd /etc/udhcpd-wlan0.conf
echo "" > /etc/resolv.conf
/etc/init.d/ssh start &

exit 0

```

/etc/udhcpd-wlan0.conf

```
start      169.254.44.50
end        169.254.44.60
interface  wlan0
max_leases 1
option subnet 255.255.255.0
option router 169.254.44.44

```

/etc/network/interfaces
```
# The loopback network interface
auto lo
iface lo inet loopback

auto wlan0
iface wlan0 inet static
address 169.254.44.44
netmask 255.255.255.0
broadcast 169.254.44.255

```

Update the listenaddress in /etc/ssh/sshd_config
```
# Use these options to restrict which interfaces/protocols sshd will bind to
#ListenAddress ::
#ListenAddress 0.0.0.0
ListenAddress 169.254.44.44

```

License
=======

Just give me some credits if you build on this and keep it open source :)
