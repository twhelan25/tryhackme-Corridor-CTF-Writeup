# tryhackme-Corridor-CTF-Writeup
![intro](https://github.com/user-attachments/assets/4121713f-0ca3-4a3c-8d9c-69d157c6179c)

This is a walkthrough for the tryhackme CTF Corridor. I will not provide any flags or passwords as this is intended to be used as a guide. 

## Scanning/Reconnaissance

First off, let's store the target IP as a variable for easy access.

Command: export ip=xx.xx.xx.xx

Next, let's run an nmap scan on the target IP:
```bash
nmap -sV -sC -A -v $ip -oN
```

Command break down:

-sV

Service Version Detection: This option enables version detection, which attempts to determine the version of the software running on open ports. For example, it might identify an HTTP server as Apache with a specific version.
-sC

Default Scripts: This option runs a collection of default NSE (Nmap Scripting Engine) scripts that are commonly useful. These scripts perform various functions like checking for vulnerabilities, gathering additional information, and identifying open services. They’re a good starting point for gathering basic information about a host.
-A

Aggressive Scan: This option enables several scans at once. It combines OS detection (-O), version detection (-sV), script scanning (-sC), and traceroute (--traceroute). It’s useful for a comprehensive scan but can be intrusive and time-consuming.
-v

Verbose Mode: Enables verbose output, which provides more detailed information about the scan’s progress and results.
$ip

Target IP: This is a placeholder for the target IP address you want to scan. In practice, replace $ip with the actual IP of the machine you are targeting.
-oN

Output in Normal Format: This option saves the scan results in a plain text file format. After -oN, specify a filename where you want to store the output.
The nmap scan reveals a webserver on tcp/80:

![nmap](https://github.com/user-attachments/assets/50c3c05d-f63d-4f2a-a4f4-b3b138cb203c)

Visiting this website show a barron white corridor with 13 unmarked doors. When we click on each door it shows a different directory, which appears to be a hash.

![corridor](https://github.com/user-attachments/assets/e1782ab3-e6d4-4097-a40d-1be1d7c7ffe4)

Here is the first door on the left:

![door1](https://github.com/user-attachments/assets/4fb8db24-f6ad-4b7f-9414-a17728692ac8)

I visited a hash identifier site which shows that the hash is most likely md5.

![hashid](https://github.com/user-attachments/assets/8669f8eb-806d-4feb-b480-fc6a33ce3a89)


