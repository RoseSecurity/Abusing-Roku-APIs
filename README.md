# Roku-Hacking

____________________________________________________________________________________________________________________________________________________________________

![Roku](https://user-images.githubusercontent.com/72598486/133314573-0f0ebc16-9d51-4c1f-b2ad-1c7e15851214.png)

____________________________________________________________________________________________________________________________________________________________________

## Introduction: 

____________________________________________________________________________________________________________________________________________________________________
  I am excited to share how to successfully enumerate your network for Roku devices, issue External Control Protocol (ECP) commands to remotely manipulate televisions, and how to design custom scripts to automate messing with your friends!
____________________________________________________________________________________________________________________________________________________________________

## Enumerating Your Network for Roku Devices:

____________________________________________________________________________________________________________________________________________________________________

  Utilizing Nmap, we can issue the following command to enumerate our network for Roku devices by looking for service versions (-sV), operating systems (-O), and speeding up the scan by not resolving DNS (-n).
```
nmap -sV -O -n 192.168.X.X/24
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
  
