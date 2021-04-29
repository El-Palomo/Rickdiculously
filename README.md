# Rickdiculously
Desarrollo del CTF Rickdiculously

## 1. Configuración de la VM

- Download de la VM: https://www.vulnhub.com/entry/rickdiculouslyeasy-1,207/
- Esta VM tiene por objetivo obtener 130 puntos.


## 2. Escaneo de Puertos

```
Nmap 7.91 scan initiated Tue Apr 27 00:13:09 2021 as: nmap -n -P0 -p- -sC -sV -O -T5 -oA full 192.168.56.106
Nmap scan report for 192.168.56.106
Host is up (0.00036s latency).
Not shown: 65528 closed ports
PORT      STATE SERVICE VERSION
21/tcp    open  ftp     vsftpd 3.0.3
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
| -rw-r--r--    1 0        0              42 Aug 22  2017 FLAG.txt
|_drwxr-xr-x    2 0        0               6 Feb 12  2017 pub
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to ::ffff:192.168.56.104
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 3
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
22/tcp    open  ssh?
| fingerprint-strings: 
|   NULL: 
|_    Welcome to Ubuntu 14.04.5 LTS (GNU/Linux 4.4.0-31-generic x86_64)
|_ssh-hostkey: ERROR: Script execution failed (use -d to debug)
80/tcp    open  http    Apache httpd 2.4.27 ((Fedora))
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-server-header: Apache/2.4.27 (Fedora)
|_http-title: Morty's Website
9090/tcp  open  http    Cockpit web service 161 or earlier
|_http-title: Did not follow redirect to https://192.168.56.106:9090/
13337/tcp open  unknown
| fingerprint-strings: 
|   NULL: 
|_    FLAG:{TheyFoundMyBackDoorMorty}-10Points
22222/tcp open  ssh     OpenSSH 7.5 (protocol 2.0)
| ssh-hostkey: 
|   2048 b4:11:56:7f:c0:36:96:7c:d0:99:dd:53:95:22:97:4f (RSA)
|   256 20:67:ed:d9:39:88:f9:ed:0d:af:8c:8e:8a:45:6e:0e (ECDSA)
|_  256 a6:84:fa:0f:df:e0:dc:e2:9a:2d:e7:13:3c:e7:50:a9 (ED25519)
60000/tcp open  unknown
|_drda-info: ERROR
| fingerprint-strings: 
|   NULL, ibm-db2: 
|_    Welcome to Ricks half baked reverse shell...
```

<img src="https://github.com/El-Palomo/Rickdiculously/blob/main/rick1.jpg" width=80% />

## 3. Enumeración 

## 3.1. Acceso directo a servicios

- Existen puertos sobre los cuales podemos probar accesos por NETCAT

```
┌──(root💀kali)-[~/RICKDICULOUS]
└─# nc 192.168.56.106 60000
Welcome to Ricks half baked reverse shell...
# whoami
root 
# ls
FLAG.txt 
# cat FLAG.txt
FLAG{Flip the pickle Morty!} - 10 Points 
                                                                                                                                          
┌──(root💀kali)-[~/RICKDICULOUS]
└─# nc 192.168.56.106 13337                                                                                                           1 ⨯
FLAG:{TheyFoundMyBackDoorMorty}-10Points
```
<img src="https://github.com/El-Palomo/Rickdiculously/blob/main/rick2.jpg" width=80% />

## 3.2. Enumeración HTTP

```
┌──(root💀kali)-[~/tools/dirsearch]
└─# python3 dirsearch.py -u http://192.168.56.106/ -t 16 -r -e txt,html,php,asp,aspx,jsp -f -w /usr/share/wordlists/dirbuster/directory-list-1.0.txt 
/root/tools/dirsearch/thirdparty/requests/__init__.py:91: RequestsDependencyWarning: urllib3 (1.26.2) or chardet (4.0.0) doesn't match a supported version!
  warnings.warn("urllib3 ({}) or chardet ({}) doesn't match a supported "

  _|. _ _  _  _  _ _|_    v0.4.1
 (_||| _) (/_(_|| (_| )

Extensions: txt, html, php, asp, aspx, jsp | HTTP method: GET | Threads: 16 | Wordlist size: 1133344

Error Log: /root/tools/dirsearch/logs/errors-21-04-28_22-45-16.log

Target: http://192.168.56.106/

Output File: /root/tools/dirsearch/reports/192.168.56.106/_21-04-28_22-45-16.txt

[22:45:16] Starting: 
[22:45:16] 403 -  217B  - /cgi-bin/     (Added to queue)
[22:45:29] 200 -  326B  - /index.html
[22:45:53] 200 -   72KB - /icons/     (Added to queue)
[22:46:09] 200 -  126B  - /robots.txt
[22:47:03] 200 -    1KB - /passwords/     (Added to queue)
[22:47:03] 301 -  240B  - /passwords  ->  http://192.168.56.106/passwords/
```

<img src="https://github.com/El-Palomo/Rickdiculously/blob/main/rick3.jpg" width=80% />

- Identificamos el archivo robots.txt y archivos CGI.

<img src="https://github.com/El-Palomo/Rickdiculously/blob/main/rick4.jpg" width=80% />

<img src="https://github.com/El-Palomo/Rickdiculously/blob/main/rick5.jpg" width=80% />

- El archivo tracertool.cgi tiene una inyección de comandos y podemos concatenar comandos con el punto y coma (;). Además, el comando CAT no funciona, por eso utilizamos el comando TAIL.

<img src="https://github.com/El-Palomo/Rickdiculously/blob/main/rick6.jpg" width=80% />

- Enumeramos los usuarios del sistema operativo.

```
root:x:0:0:root:/root:/bin/bash
bin:x:1:1:bin:/bin:/sbin/nologin
daemon:x:2:2:daemon:/sbin:/sbin/nologin
adm:x:3:4:adm:/var/adm:/sbin/nologin
lp:x:4:7:lp:/var/spool/lpd:/sbin/nologin
sync:x:5:0:sync:/sbin:/bin/sync
shutdown:x:6:0:shutdown:/sbin:/sbin/shutdown
halt:x:7:0:halt:/sbin:/sbin/halt
mail:x:8:12:mail:/var/spool/mail:/sbin/nologin
operator:x:11:0:operator:/root:/sbin/nologin
games:x:12:100:games:/usr/games:/sbin/nologin
ftp:x:14:50:FTP User:/var/ftp:/sbin/nologin
nobody:x:99:99:Nobody:/:/sbin/nologin
systemd-coredump:x:999:998:systemd Core Dumper:/:/sbin/nologin
systemd-timesync:x:998:997:systemd Time Synchronization:/:/sbin/nologin
systemd-network:x:192:192:systemd Network Management:/:/sbin/nologin
systemd-resolve:x:193:193:systemd Resolver:/:/sbin/nologin
dbus:x:81:81:System message bus:/:/sbin/nologin
polkitd:x:997:996:User for polkitd:/:/sbin/nologin
sshd:x:74:74:Privilege-separated SSH:/var/empty/sshd:/sbin/nologin
rpc:x:32:32:Rpcbind Daemon:/var/lib/rpcbind:/sbin/nologin
abrt:x:173:173::/etc/abrt:/sbin/nologin
cockpit-ws:x:996:994:User for cockpit-ws:/:/sbin/nologin
rpcuser:x:29:29:RPC Service User:/var/lib/nfs:/sbin/nologin
chrony:x:995:993::/var/lib/chrony:/sbin/nologin
tcpdump:x:72:72::/:/sbin/nologin
RickSanchez:x:1000:1000::/home/RickSanchez:/bin/bash
Morty:x:1001:1001::/home/Morty:/bin/bash
Summer:x:1002:1002::/home/Summer:/bin/bash
apache:x:48:48:Apache:/usr/share/httpd:/sbin/nologin
```

- En la carpeta /passwords encontramos un flag y una contraseña: winter.

<img src="https://github.com/El-Palomo/Rickdiculously/blob/main/rick7.jpg" width=80% />

<img src="https://github.com/El-Palomo/Rickdiculously/blob/main/rick8.jpg" width=80% />


## 4. Acceso al Sistema

### 4.1. Acceso por SSH

- Probamos la contraseña obtenida en los usuarios del sistema enumerados. Con el usuario Summer:winter, podemos ingresar.

```                                                                                                                                           
┌──(root💀kali)-[/home/kali]
└─# ssh -p 22222 Summer@192.168.56.106
Summer@192.168.56.106's password: 
Last login: Thu Apr 29 00:59:36 2021 from 192.168.56.104
[Summer@localhost ~]$ whoami
Summer
[Summer@localhost ~]$ pwd
/home/Summer
[Summer@localhost ~]$ 
```

<img src="https://github.com/El-Palomo/Rickdiculously/blob/main/rick9.jpg" width=80% />

### 4.2. Enumeración de información en la carpeta /home

```
[Summer@localhost home]$ ls -laR /home
/home:
total 0
drwxr-xr-x.  5 root        root         52 Aug 18  2017 .
dr-xr-xr-x. 17 root        root        236 Aug 18  2017 ..
drwxr-xr-x.  2 Morty       Morty       131 Sep 15  2017 Morty
drwxr-xr-x.  4 RickSanchez RickSanchez 113 Sep 21  2017 RickSanchez
drwx------.  2 Summer      Summer      111 Apr 29 02:10 Summer

/home/Morty:
total 64
drwxr-xr-x. 2 Morty Morty   131 Sep 15  2017 .
drwxr-xr-x. 5 root  root     52 Aug 18  2017 ..
-rw-------. 1 Morty Morty     1 Sep 15  2017 .bash_history
-rw-r--r--. 1 Morty Morty    18 May 30  2017 .bash_logout
-rw-r--r--. 1 Morty Morty   193 May 30  2017 .bash_profile
-rw-r--r--. 1 Morty Morty   231 May 30  2017 .bashrc
-rw-r--r--. 1 root  root    414 Aug 22  2017 journal.txt.zip
-rw-r--r--. 1 root  root  43145 Aug 22  2017 Safe_Password.jpg

/home/RickSanchez:
total 12
drwxr-xr-x. 4 RickSanchez RickSanchez 113 Sep 21  2017 .
drwxr-xr-x. 5 root        root         52 Aug 18  2017 ..
-rw-r--r--. 1 RickSanchez RickSanchez  18 May 30  2017 .bash_logout
-rw-r--r--. 1 RickSanchez RickSanchez 193 May 30  2017 .bash_profile
-rw-r--r--. 1 RickSanchez RickSanchez 231 May 30  2017 .bashrc
drwxr-xr-x. 2 RickSanchez RickSanchez  18 Sep 21  2017 RICKS_SAFE
drwxrwxr-x. 2 RickSanchez RickSanchez  26 Aug 18  2017 ThisDoesntContainAnyFlags

/home/RickSanchez/RICKS_SAFE:
total 12
drwxr-xr-x. 2 RickSanchez RickSanchez   18 Sep 21  2017 .
drwxr-xr-x. 4 RickSanchez RickSanchez  113 Sep 21  2017 ..
-rwxr--r--. 1 RickSanchez RickSanchez 8704 Sep 21  2017 safe

/home/RickSanchez/ThisDoesntContainAnyFlags:
total 4
drwxrwxr-x. 2 RickSanchez RickSanchez  26 Aug 18  2017 .
drwxr-xr-x. 4 RickSanchez RickSanchez 113 Sep 21  2017 ..
-rw-rw-r--. 1 RickSanchez RickSanchez  95 Aug 18  2017 NotAFlag.txt

/home/Summer:
total 32
drwx------. 2 Summer Summer  111 Apr 29 02:10 .
drwxr-xr-x. 5 root   root     52 Aug 18  2017 ..
-rw-------. 1 Summer Summer 1765 Apr 29 03:21 .bash_history
-rw-r--r--. 1 Summer Summer   18 May 30  2017 .bash_logout
-rw-r--r--. 1 Summer Summer  193 May 30  2017 .bash_profile
-rw-r--r--. 1 Summer Summer  231 May 30  2017 .bashrc
-rw-rw-r--. 1 Summer Summer   48 Aug 22  2017 FLAG.txt
-rwxr--r--. 1 Summer Summer 8704 Apr 29 02:10 safe
```

<img src="https://github.com/El-Palomo/Rickdiculously/blob/main/rick10.jpg" width=80% />


- Me llama la atención los archivos: journal.txt.zip y Safe_Password.jpg. Los descargamos y analizamos.




























