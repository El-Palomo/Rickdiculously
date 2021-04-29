# Rickdiculously
Desarrollo del CTF Rickdiculously

## 1. Configuración de la VM

- Download de la VM: https://www.vulnhub.com/entry/rickdiculouslyeasy-1,207/
- Esta VM tiene por objetivo obtener 130 puntos.


## 2. Escaneo de Puertos

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
