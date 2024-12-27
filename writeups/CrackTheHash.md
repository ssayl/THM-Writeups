# Crack the Hash

**Room:** [Crack the Hash](https://tryhackme.com/room/crackthehash)  
**Difficulty:** Easy  
**Category:** Cryptography

---

## Overview

In this room, we learn how to identify and crack different hash types. Below is my step-by-step approach for each provided hash.

---

## Task 1: Level 1

### 1. Hash: `48bb6e862e54f2a795ffc4e541caed4d`
**Hint says:** This is an **MD5** hash.

#### Steps Taken
1. Saved the hash in a file named `hash1_1.txt`.  
2. Used Hashcat with mode `-m 0` (MD5):
   ~~~bash
   hashcat -m 0 hash1_1.txt /usr/share/wordlists/rockyou.txt --show
   ~~~

**Cracked Password**  
   ~~~
   easy
   ~~~

---

### 2. Hash: `CBFDAC6008F9CAB4083784CBD1874F76618D2A97`
**Hint says:** This is a **SHA** hash. Hashcat can only crack a handful of SHA hash types, and the hash type turned out to be **SHA1**.

#### Steps Taken
1. Saved the hash in `hash1_2.txt`.  
2. Used Hashcat with mode `-m 100` (SHA1):
   ~~~bash
   hashcat -m 100 hash1_2.txt /usr/share/wordlists/rockyou.txt --show
   ~~~

**Cracked Password**  
   ~~~
   password123
   ~~~

---

### 3. Hash: `1C8BFE8F801D79745C4631D09FFF36C82AA37FC4CCE4FC946683D7B336B63032`
**Hint says:** This is a **SHA** hash. After some testing it turns out it is a **SHA256** hash this time.

#### Steps Taken
1. Saved the hash in `hash1_3.txt`.  
2. Used Hashcat with mode `-m 1400` (SHA256):
   ~~~bash
   hashcat -m 1400 hash1_3.txt /usr/share/wordlists/rockyou.txt --show
   ~~~

**Cracked Password**  
   ~~~
   letmein
   ~~~

---

### 4. Hash: `$2y$12$Dwt1BZj6pcyc3Dy1FWZ5ieeUz...`
**Hint says:** This is a **bcrypt** hash.

#### Steps Taken
1. Saved the hash in `hash1_4.txt`.  
2. Used Hashcat with mode `-m 3200` (bcrypt):
   ~~~bash
   hashcat -m 3200 hash1_4.txt /usr/share/wordlists/rockyou.txt --show
   ~~~

**Cracked Password**  
   ~~~
   bleh
   ~~~

---

### 5. Hash: `279412f945939ba78ce0758d3fd83daa`
**Hint says:** This is an **MD4** hash. 

1. Used an online cracking tool from the room’s hint.

**Cracked Password**  
   ~~~
   Eternity22
   ~~~

---

## Task 2: Level 2

This task increases the difficulty. All of the answers will be in the classic [rockyou](https://github.com/brannondorsey/naive-hashcat/releases/tag/data) password list.

You might have to start using hashcat here and not online tools. It might also be handy to look at some example hashes on [hashcats page](https://hashcat.net/wiki/).

---

### 1. Hash: `F09EDCB1FCEF6CDFB23DC3505A882655FF7737E5ED98AA2D1C13F640FCCC2D0C85`
There is no hint for this one but it seems like a **SHA256** hash (which turns out to be right).

#### Steps Taken
1. Saved the hash in a file named `hash2_1.txt`.  
2. Used Hashcat with mode `-m 1400` (SHA256):
   ~~~bash
   hashcat -m 1400 hash2_1.txt /usr/share/wordlists/rockyou.txt --show
   ~~~

**Cracked Password**  
   ~~~
   paule
   ~~~

---

### 2. Hash: `1DFECA0C002AE40B8619ECF94819CC1B`

The hint says this is an **NTLM** hash.  
Command used to crack the hash (NTLM mode = 1000):
   ~~~bash
   hashcat -m 1000 hash2_2.txt /usr/share/wordlists/rockyou.txt --show
   ~~~

**Cracked Password**  
   ~~~
   	n63umy8lkf4i
   ~~~

---

### 3. Hash: `$6$aReallyHardSalt$6WKUTqzq.UQQmrm0p/T7MPpMbGNnzXPMAXi4bJMl9be.cfi3/qxIf.hsGpS41BqMhSrHVXgMpdjS6xeKZAs02.`
**Salt:** aReallyHardSalt

This one seems to be a **SHA-512 (Unix)** hash after looking up the **$6$** on hashcat.  
Command used to crack the hash (mode = 1800):
   ~~~bash
   hashcat -m 1800 hash2_3.txt /usr/share/wordlists/rockyou.txt --show
   ~~~

**Cracked Password**  
   ~~~
   waka99
   ~~~

---

### 4. Hash: `e5d8870e5bdd26602cab8dbe07a942c8669e56d6`
**Salt:** tryhackme

The hint says it is a **HMAC-SHA1** hash.  
Command used to crack the hash (mode = 160):
   ~~~bash
   hashcat -m 160 hash2_4.txt /usr/share/wordlists/rockyou.txt --show
   ~~~

**Cracked Password**  
   ~~~
   481616481616
   ~~~

---

## Final Thoughts

As the difficulty ramps up, you’ll need to identify both the hash **type** and any **salt** or special parameters. Using the correct `-m` (mode) in Hashcat is key. If rockyou.txt doesn’t work, consider trying other wordlists or an online tool, but usually for classic hashes, rockyou is a good start. I recommend using [CrackStation](https://crackstation.net/).
