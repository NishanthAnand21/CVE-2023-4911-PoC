# GNU C Library's Dynamic Loader Vulnerability (CVE-2023-4911)

## Overview
The GNU C Library (glibc) serves as the C library in the GNU system and is integral to Linux-based systems. At its core, glibc defines essential functionalities for programs, including system calls and common functions like open, malloc, printf, and exit. The dynamic loader, a vital component of glibc, plays a key role in preparing and executing programs by managing shared libraries and linking them at runtime.

## GLIBC_TUNABLES Environment Variable
Introduced to empower users without recompilation, the GLIBC_TUNABLES environment variable lets users modify glibc's behavior at runtime. It provides a means to adjust performance and behavior parameters, affecting application startup.

## Looney Tunables Vulnerability
A buffer overflow vulnerability in the dynamic loader's processing of GLIBC_TUNABLES poses a critical security risk. Exploitation grants local attackers elevated privileges, potentially leading to full root access. Successful exploitation on major distributions like Fedora, Ubuntu, and Debian underscores the severity and widespread impact.

## Potential Impact
Misuse or exploitation of this vulnerability can impact system performance, reliability, and security. While the exploit details are withheld, the risk is imminent, as buffer overflows can quickly transform into data-only attacks. Popular Linux distributions using glibc may be susceptible, emphasizing the need for prompt awareness and mitigation.

## CVE-2023-4911
Assigned: October 3, 2023

Type: Local Privilege Escalation

Affected Versions: glibc 2.34 and later

Distributions: RHEL, Ubuntu, Fedora, Debian, Amazon Linux, Gentoo, and others using glibc
Mitigation

Upgrade: Apply glibc updates promptly.

Monitor: Stay informed about security advisories.

Review: Assess GLIBC_TUNABLES usage in applications.

Audit: Examine system logs for suspicious activity.


## Developed for:

Ubuntu 22.04
Ubuntu GLIBC 2.35-0ubuntu3.1
su from util-linux 2.37.2
ASLR ON

## Creating the Virtual Environment to check this Vulnerability

> Used Ubuntu (version 22.04.1) iso for creating virtual machine

## Post Installation

**After successfull installation we need not to update anything but we will install git and GNU Binutils package because it is not found in the system, and when you compile the exploit python script you will face the error.**

> By using these commands:

```
$ sudo apt install git
```

```
$ sudo apt install binutils
```

> Check if it's vulnerable
``` 
$ env -i "GLIBC_TUNABLES=glibc.malloc.mxfast=glibc.malloc.mxfast=A" "Z=`printf '%08192x' 1`" /usr/bin/su --help
```
> If it outputs ```Segmentation fault (core dumped)``` then it is vulnerable.

<img src="images/1.png">



> check the version of the dynamic linker/loader, often associated with the GNU C Library (glibc)

```
$ ldd --version
```
> Output will give the version of GLIBC

```
ldd (Ubuntu GLIBC 2.35-0ubuntu3.6) 2.35
Copyright (C) 2022 Free Software Foundation, Inc.
This is free software; see the source for copying conditions.  There is NO
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
Written by Roland McGrath and Ulrich Drepper.
```
<img src="images/2.png">

**Now that we know our machine is vulnerable and ready to escalate the root privilages**

***Note: We should not install gcc in the machine, if we install it will lose its vulnerability, hence the script will not work.***
## Exploitation

> Since we doesn't have gcc installed and we need to compile the C program in host machine and then import it to guest machine.

> we can compile the C program with this command ```$ gcc exploit.c -o (outputname)```

> After importing the output file of C program also import the python(genlib.py) script.

> Now compile the python script ``` $ python3 genlib.py```

> After Compiling a '"' folder is created.

> We will do this in /tmp folder so user can execute script and program

> Now finally we will execute the program to escalate the root privilages 

```
$ ./(nameoftheprogram)
```

## Proofs

> It will take some time and will get the root shell.

<img src="images/3.png">


***Secure to Empower, Not Exploit!***

**References:**
1. https://www.qualys.com/2023/10/03/cve-2023-4911/looney-tunables-local-privilege-escalation-glibc-ld-so.txt
2. https://man7.org/linux/man-pages/man8/ld.so.8.html
3. https://seclists.org/oss-sec/2023/q4/18
4. https://ubuntu.com/security/CVE-2023-4911
5. https://github.com/elastic/detection-rules/blob/main/rules/linux/credential_access_potential_linux_local_account_bruteforce.toml
6. https://www.crowdsec.net/blog/detect-looney-tunables-exploitation-attempts?hss_channel=tw-1261314743190589447
