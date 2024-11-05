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

Next I googled a md5 decrypter site and revealed that the first directory decrypts to 1:

![md5 decrypt](https://github.com/user-attachments/assets/f6512233-9387-44e1-911c-e815313543ac)

Likewise, decrypting the other dirs reveals that they are in numerical order from left to right.

I then used this python script to fuzz the md5 directories:
```bash
import requests
import time
import sys
import hashlib

# IP address (update this to your target IP)
ip = "10.10.205.16"

def md5(string):
    return hashlib.md5(string.encode()).hexdigest()

def check_directory(number):
    directory = md5(str(number))
    url = f"http://{ip}/{directory}"
    print(f"Checking URL: {url}")
    
    try:
        response = requests.get(url, timeout=5)
        return response.status_code, response.text
    except requests.RequestException as e:
        print(f"Request failed: {e}")
        return None, None

def main():
    print("Starting scan...")

    for i in range(50):  # Checking first 50 numbers
        print(f"\nChecking directory {i}...")
        status, content = check_directory(i)
        
        if status is None:
            print("Error (connection failed)")
        else:
            print(f"Status: {status}")
            if content and len(content) > 0:
                print(f"Content (first 100 chars): {content[:100]}...")
                if 'flag' in content.lower():
                    print("FLAG FOUND IN THIS DIRECTORY!")
                    print(f"Full content: {content}")
                    return
            else:
                print("No content or empty response")
        
        time.sleep(1)  # 1-second delay between requests

    print("\nScan complete. No flag found.")

if __name__ == "__main__":
    try:
        main()
    except KeyboardInterrupt:
        print("\nScan interrupted by user.")
        sys.exit(0)
    except Exception as e:
        print(f"An unexpected error occurred: {e}")
        sys.exit(1)
```
This script is designed to scan through a range of directories on a server at IP address 10.10.143.116. Here’s a breakdown of its operation:

Hashing Directory Names: Each directory is represented by a 32-character MD5-like hex string derived from the integer range 0 to 50. The script converts each number to a hexadecimal string and uses it in constructing the URL.

Directory Checking: For each directory (from 0 to 50), it sends a GET request to the generated URL. The check_directory function handles the request and returns the HTTP status code and response content if the connection is successful; otherwise, it returns None.

Content Analysis: If any content is returned by the server, the script checks the content for the keyword "flag" (case-insensitive). If found, it prints a message indicating a "FLAG FOUND."

Output: For each directory:

If there’s content, it prints the status, directory number, the first 100 characters of the content, and a special note if "flag" is detected.
If there’s no content or a connection fails, it prints a status update accordingly.

And the script reveals the directory with the flag:

![flag](https://github.com/user-attachments/assets/ea6d6d33-3faf-4614-b9b5-d11e09eb2657)

