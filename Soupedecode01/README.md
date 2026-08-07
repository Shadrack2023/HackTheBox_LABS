> Writeup · part of **[htb-walkthroughs](../README.md)** · [⬇ original PDF](Soupedecode01.pdf)

---

# **<u>SOUPEDECODE 01 - TRYHACKME</u>** 

I started with a Nmap scan, which revealed multiple services typical of an Active Directory environment, including DNS (53), **Kerberos** (88, 464), **SMB** (445), **LDAP** (389, 636), Global Catalog services (3268, 3269), and MSRPC/NetBIOS (135, 139). Additional services such as Remote Desktop Protocol (3389), Active Directory Web Services (9389) 

![](images/Soupedecode_01.pdf-0001-02.png)

I had access to smb as guest where I was able to enumerate shares 

![](images/Soupedecode_01.pdf-0001-04.png)

<u>#https://www.hackingarticles.in/as-rep-roasting/ #https://www.hackingarticles.in/ad-recon-kerberos-username-bruteforce/</u> 

## **RID-based enumeration** 

To further enumerate domain users, I performed a RID brute-force, since the IPC$ share is readable. 

![](images/Soupedecode_01.pdf-0002-02.png)

### Bruteforcing for valid accounts, I found one ybob317 

![](images/Soupedecode_01.pdf-0002-04.png)

![](images/Soupedecode_01.pdf-0002-05.png)

### Share Enumeration with this account. 

![](images/Soupedecode_01.pdf-0003-01.png)

I connected to the Users shares since user ybob317 had read permission on this share. 

![](images/Soupedecode_01.pdf-0003-03.png)

## **USER FLAG** 

![](images/Soupedecode_01.pdf-0004-01.png)

## **Kerberoast Attack** 

![](images/Soupedecode_01.pdf-0004-03.png)

![](images/Soupedecode_01.pdf-0005-00.png)

### Cracked hash using hashcat → **file_svc:Password123!!** 

![](images/Soupedecode_01.pdf-0005-02.png)

We have read permission on backup shares. The backup share had backed up account credentials. 

![](images/Soupedecode_01.pdf-0005-04.png)

![](images/Soupedecode_01.pdf-0006-00.png)

## **Password Spraying** 

Sprayed the passwords and found one active account, which turned out to be/have domain admin privileges. 

![](images/Soupedecode_01.pdf-0006-03.png)

This confirms this domain admin 

![](images/Soupedecode_01.pdf-0006-05.png)

## **ROOT FLAG** 

I accessed the machine viar RDP and got the root flag. 

![](images/Soupedecode_01.pdf-0007-02.png)
