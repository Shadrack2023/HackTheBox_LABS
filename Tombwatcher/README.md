> Writeup · part of **[htb-walkthroughs](../README.md)** · [⬇ original PDF](Tombwatcher.pdf)

---

# **<u>TOMBWATCHER SEASON 8 - HACKTHEBOX</u>** 

### Initial credentials: **henry || H3nry_987TGV!** 

Nmap output: Port **88 kerberos** and **389 ldap** are open signaling we are attacking a Domain Controller. 

![](images/Tombwatcher-Sn8-HTB.pdf-0001-03.png)

## **PORT 445(SMB SHARES ENUMERATION)** 

Nothing much can be done or extracted from the shares. 

![](images/Tombwatcher-Sn8-HTB.pdf-0001-06.png)

## **LDAP DOMAIN DUMP ENUMERATION** 

![](images/Tombwatcher-Sn8-HTB.pdf-0002-01.png)

## **BLOODHOUND DUMP OUTPUT Exploitation:** 

![](images/Tombwatcher-Sn8-HTB.pdf-0002-03.png)

### User Henry has **writeSPN** permission over user Alfred 

![](images/Tombwatcher-Sn8-HTB.pdf-0002-05.png)

### Using a tool called 

**targetedkerberoast.py** <u>(https://github.com/Shadrack2023/targetedKerberoast/blob/main/targeted Kerberoast.py) we are able to retrieve the krb hash for user</u> **Alfred** . 

![](images/Tombwatcher-Sn8-HTB.pdf-0003-02.png)

### Cracked the hash using john → cleartext password **alfred:basketball** 

![](images/Tombwatcher-Sn8-HTB.pdf-0003-04.png)

### Using nxc we confirm that the credentials are working. 

![](images/Tombwatcher-Sn8-HTB.pdf-0003-06.png)

## **LATERAL MOVEMENT** 

### User alfred can addself to group INFRASTRACTURE 

![](images/Tombwatcher-Sn8-HTB.pdf-0003-09.png)

### Using **bloodyAD tool** , we can add user alfred to the **INFRASTRUCTURE** GROUP 

![](images/Tombwatcher-Sn8-HTB.pdf-0004-01.png)

The image below confirms our user is added to the infrastructure group. 

![](images/Tombwatcher-Sn8-HTB.pdf-0004-03.png)

We’ll use NetExec to enumerate **Group Managed Service Accounts (gMSAs)** from Active Directory, retrieving their **account names** , **Kerberos keys** (plaintext or hashes), and related metadata if permissions allow. 

![](images/Tombwatcher-Sn8-HTB.pdf-0004-05.png)

Using nxc, we are able to retrieve a machine account’s hash 

**ansible_dev$:4f46405647993c7d4e1dc1c25dd6ecf4** Wap 10.10,11.72Zs-/sc834tur3bak/HTB-T-Labs_reports/HTB/Tonbuat-0 alfred cher si **o** riovilie **t** .72i7 389" ocot Windows 10 / Server 2019 Build 17763 

These credentials are working. Confirmed this using nxc 

![](images/Tombwatcher-Sn8-HTB.pdf-0005-01.png)

With this machine account credentials, we can force user “ **sam** ” to change password and authenticate using **sam’s** credentials 

![](images/Tombwatcher-Sn8-HTB.pdf-0005-03.png)

Using the **net rpc tool** , we managed to change password for user sam and confirmed authentication using nxc as in the image below. 

![](images/Tombwatcher-Sn8-HTB.pdf-0005-05.png)

## **SAM TO JOHN** 

![](images/Tombwatcher-Sn8-HTB.pdf-0006-01.png)

``` 

To change the ownership of the object, you may use Impacket's **owneredit** example script (cf. "grant ownership" reference for the exact link). 

owneredit.py -action write -owner 'attacker' -target 'victim' 'DOMAIN'/'USER':'PASSWORD' 

To abuse ownership of a user object, you may grant yourself the GenericAll permission. 

Impacket's **dacledit** can be used for that purpose (cf. "grant rights" reference for the link). 

dacledit.py -action 'write' -rights 'FullControl' -principal 'controlledUser' -target 'targetUser' 'domain'/'controlledUser':'password' 

``` 

![](images/Tombwatcher-Sn8-HTB.pdf-0006-09.png)

![](images/Tombwatcher-Sn8-HTB.pdf-0007-00.png)

Having **FullControl** rights on user john, we can now change his password using the net  rpc tools and authenticate to the target via winrm. 

![](images/Tombwatcher-Sn8-HTB.pdf-0007-02.png)

## **USER FLAG** 

![](images/Tombwatcher-Sn8-HTB.pdf-0007-04.png)

## **PRIVILEGE ESCALATION TO ROOT** 

![](images/Tombwatcher-Sn8-HTB.pdf-0008-01.png)

### The ADCS service is enabled 

![](images/Tombwatcher-Sn8-HTB.pdf-0008-03.png)

### Enumerated the ADCS to find vulnerable templates using the **certify** tool. 

![](images/Tombwatcher-Sn8-HTB.pdf-0008-05.png)

No misconfigured templates for this user, let's dig a further. 

![](images/Tombwatcher-Sn8-HTB.pdf-0009-00.png)

Found deleted objects ` Get-ADObject -Filter {isDeleted -eq $true} -IncludeDeletedObjects -Properties * | Select-Object Name,ObjectGUID` 

![](images/Tombwatcher-Sn8-HTB.pdf-0009-02.png)

We managed to restore the deleted object as seen below `Restore-ADObject -Identity c1f1f0fe-df9c-494c-bf05-0679e181b358` `Get-ADUser -Identity 'cert_admin' | Format-List Name,DistinguishedName,Enabled` → checks if the object is restored. 

![](images/Tombwatcher-Sn8-HTB.pdf-0010-00.png)

John has **GenericAll rights** on ADCS group which by speculation user **cert_admin** belong to. We’ll now abuse these rights. 

![](images/Tombwatcher-Sn8-HTB.pdf-0010-02.png)

Perfect, now we have user cert_admin. Lets find misconfigured templates with this user. 

![](images/Tombwatcher-Sn8-HTB.pdf-0010-04.png)

![](images/Tombwatcher-Sn8-HTB.pdf-0011-00.png)

### Found a misconfigured cert template **ESC15** 

![](images/Tombwatcher-Sn8-HTB.pdf-0011-02.png)

We can now request cert template using certipy 

![](images/Tombwatcher-Sn8-HTB.pdf-0011-04.png)

Then authenticate as user administrator 

![](images/Tombwatcher-Sn8-HTB.pdf-0012-00.png)

Right in the shell, we are able to change administrator’s password as seen below, which can then be used for authentication 

enabteraceount-user'=-enabte the User"s account dump = Dumps ‘the domain. 

## **ROOT FLAG** 

![](images/Tombwatcher-Sn8-HTB.pdf-0012-04.png)
