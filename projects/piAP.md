[< HOME](../README.md)
# pi Access Point / Router

## Base Pi Setup
1. `sudo raspi-config`
   1. Set `Display Options > Screen Blanking` to `No`.
   2. Set `Display Options > Underscan` to `Yes`.
   3. Enable `Interface Options > VNC`.
   4. Enable `Interface Options > SPI`.
   5. Enable `Interface Options > I2C`.
   6. Under `Interface Options > Serial Port`, set `Login Shell` to `No`, `Serial Port Hardware` to `Yes`.
   7. Set `Wi-Fi Country` to `US` under `Localisation`.
2. Install Node
   1. `curl -sSL https://deb.nodesource.com/setup_16.x | sudo bash -`
   2. `sudo apt install -y nodejs`
3. Install VsCode (optional for remote development)
   1. `sudo apt install code`
4. `sudo apt-get install dnsmasq hostapd bridge-utils iptables`
5. Turn off `dnsmasq` and `hostapd` until configured.
   1. `sudo systemctl stop dnsmasq`
   2. `sudo systemctl stop hostapd`
6. Configure a static IP for the wlan0 interface
   1. `sudo nano /etc/dhcpcd.conf`
   2. Add this at the end of the file.
      ````
      interface wlan0
      static ip_address=10.0.88.1/24
      #denyinterfaces eth0
      denyinterfaces wlan0
      ````
   3. `sudo service dhcpcd restart`, note ... this might reset SSH conection.
7. Configuring the DHCP server (dnsmasq)
   1. `sudo mv /etc/dnsmasq.conf /etc/dnsmasq.conf.orig` to backup original config.
   2. `sudo nano /etc/dnsmasq.conf` to edit config.
   3. Add this to the file. Reference: [Dnsmasq](https://thekelleys.org.uk/dnsmasq/doc.html)
      ````
      interface=wlan0 # Use the require wireless interface - usually wlan0
        dhcp-range=10.0.88.10,10.0.88.100,255.255.255.0,24h
      ````
8. Configuring the access point host software (hostapd)
   1. `sudo nano /etc/hostapd/hostapd.conf` to edit config.
   3. Add this to the file.
      ````
      interface=wlan0
      driver=nl80211
      hw_mode=g
      channel=7
      wmm_enabled=0
      macaddr_acl=0
      auth_algs=1
      ignore_broadcast_ssid=0
      wpa=2
      wpa_key_mgmt=WPA-PSK
      wpa_pairwise=TKIP
      rsn_pairwise=CCMP
      ssid=piNET
      wpa_passphrase=d0abeefd0a
      ````
9. `sudo nano /etc/default/hostapd` to tell the system where to find this configuration file.
   1. Find the line with `#DAEMON_CONF`, and replace it with `DAEMON_CONF="/etc/hostapd/hostapd.conf"`
10. Turn on `dnsmasq` and `hostapd` not that they are configured.
    1. `sudo systemctl start hostapd`. If this comes back with `Failed to start hostapd.service: Unit hostapd.service is masked.`, run these commands.
       1. `sudo systemctl unmask hostapd`
       2. `sudo systemctl enable hostapd`
       3. `sudo systemctl start hostapd`
    2. `sudo systemctl start dnsmasq`
11. Add routing and masquerade
    1. Edit `sudo nano /etc/sysctl.conf` and uncomment this line: `net.ipv4.ip_forward=1`
    2. `sudo iptables -t nat -A  POSTROUTING -o wwan0 -j MASQUERADE` to add a masquerade for outbound traffic.
    3. `sudo sh -c "iptables-save > /etc/iptables.ipv4.nat"` to save the iptables rule.
    4. Edit `sudo nano /etc/rc.local` and add `iptables-restore < /etc/iptables.ipv4.nat` just above "exit 0" to install these rules on boot.

# Advanced setup (As Needed)
## Using 4G LTE on a Raspberry Pi (USB0)
Notes based on [Guide](https://www.jeffgeerling.com/blog/2022/using-4g-lte-wireless-modems-on-raspberry-pi)
1. run `lsusb` to verify your modem is available.
2. `sudo apt-get update`
3. `sudo apt-get upgrade`
4. `sudo apt-get update && sudo apt-get install libqmi-utils udhcpc` for QMI setup
5. `sudo apt-get install minicom -y` => Install `minicom` for testing.
   1. `sudo minicom -D /dev/ttyUSB2 -b 115200` => open the serial communication for `/dev/ttyUSB2` device and `115200` baudrate using `minicom`.
   2. `ctrl+A Z E` to toggle local echo of what you type.
   3. `AT+CUSBPIDSWITCH=9011,1,1` to create `usb0`.
   4. `CTRL+A Z X` to exit `minicom`.
6. `sudo dhclient -v usb0` to get ip address.
7. `ping -I usb0 www.google.com -c 5` to test data connection.

## Using 4G LTE on a Raspberry Pi (WWAN0)
Notes based on [Guide](https://www.jeffgeerling.com/blog/2022/using-4g-lte-wireless-modems-on-raspberry-pi)
1. run `lsusb` to verify your modem is available.
2. `sudo apt-get update`
3. `sudo apt-get upgrade`
4. `sudo apt-get update && sudo apt-get install libqmi-utils udhcpc` for QMI setup
5. `sudo apt-get install minicom -y` => Install `minicom` for testing.
   1. `sudo minicom -D /dev/ttyUSB2 -b 115200` => open the serial communication for `/dev/ttyUSB2` device and `115200` baudrate using `minicom`.
   2. `ctrl+A Z E` to toggle local echo of what you type.
   3. `AT+CUSBPIDSWITCH=9001,1,1` to crate `wwan0`.
   4. `CTRL+A Z X` to exit `minicom`.
6. `sudo dhclient -v wwan0` to get ip address.
7. `ping -I wwan0 www.google.com -c 5` to test data connection.

## minicom
1. Install
   1. `sudo apt-get update`
   2. `sudo apt-get upgrade`
   3. `sudo apt install minicom -y`
2. Connecting / Testing
   1. `sudo minicom -D /dev/ttyUSB2 -b 115200`
3. Notes
   1. The device may not be set to echo back what you type in, `CTRL+A` then `Z` for menu and press`E` to toggle local echo.

## Waveshare SIM7600X
1. [RNDIS Method (PREFERED)](https://www.waveshare.com/wiki/Raspberry_Pi_networked_via_RNDIS)
2. [NDIS Method](https://www.waveshare.com/wiki/Raspberry_Pi_networked_via_NDIS)

## Reference
1. [How to use your Raspberry Pi as a wireless access point](https://thepi.io/how-to-use-your-raspberry-pi-as-a-wireless-access-point/)
2. [Get Started with Super SIM, the Raspberry Pi 4 and the Waveshare 4G Hat](https://www.twilio.com/docs/iot/supersim/getting-started-super-sim-raspberry-pi-waveshare-4g-hat#hardware-setup-the-sim7600g-h-4g-hat)
3. [Internet Connection with Quectel EC25 using ECM Mode](https://docs.sixfab.com/page/internet-connection-with-quectel-ec25-by-using-ecm-mode)
4. [Using 4G LTE wireless modems on a Raspberry Pi](https://www.jeffgeerling.com/blog/2022/using-4g-lte-wireless-modems-on-raspberry-pi)
5. [Debian Network Configuration](https://wiki.debian.org/NetworkConfiguration)
6. [USB_ModeSwitch - Handling Mode-Switching USB Devices on Linux](https://www.draisberghof.de/usb_modeswitch/)
7. [Raspberry Pi as 4G LTE Router](https://newjerseystyle.github.io/en/2020/Raspberry-Pi-as-4G-LTE-Router/)
