---
layout: single
title: HackmePlease - Vulnhub - Writeup
excerpt: "esto es muy facil es para principiantes que recien empiezan"
date: 2024-02-11
classes: wide
header:
  teaser: /assets/images/hackmeplease-writeup/hackmeplease.PNG
  teaser_home_page: true
categories:
  - vulnhub
  - ctf
tags:
  - linux
  - easy
  - re-esay
  - ctf-like
  - seedDMS
  - nickdevplus-ctf
---
Link: [Descarga la maquina](https://www.vulnhub.com/entry/hack-me-please-1,731/)
![](/assets/images/hackmeplease-writeup/hackmeplease.PNG)
Bueno el dia de hoy haremos una maquina re facil para principiantes de la plataforma gratuita vulnhub entonces pero que la disfruten

## hacemos un ping a la maquina para ver si esta activa
```console
ping -c 1 192.168.58.129
PING 192.168.58.129 (192.168.58.129) 56(84) bytes of data.
64 bytes from 192.168.58.129: icmp_seq=1 ttl=64 time=3.33 ms

--- 192.168.58.129 ping statistics ---
1 packets transmitted, 1 received, 0% packet loss, time 0ms
rtt min/avg/max/mdev = 3.332/3.332/3.332/0.000 ms

```
Y bueno vemos que esta activa eso nos damos cuenta por el paquete recibido
el ttl=64 eso significa que es linux si fuera 128 seria listo y bueno comenzaremos con los escaneos con nmap para buscar vulnerabilidades

## Escaneos con nmap
```nmap
sudo nmap -sS --min-rate 5000 -p- --open -n -Pn 192.168.58.129 -oG allPorts
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-02-11 16:58 EST
Nmap scan report for 192.168.58.129
Host is up (0.00091s latency).
Not shown: 64414 closed tcp ports (reset), 1118 filtered tcp ports (no-response)
Some closed ports may be reported as filtered due to --defeat-rst-ratelimit
PORT      STATE SERVICE
80/tcp    open  http
3306/tcp  open  mysql
33060/tcp open  mysqlx
MAC Address: 00:0C:29:0D:F2:9F (VMware)

```
bueno y vemos los puertos abiertos el 80,3306,33060 y bueno vamos a ver los scripts basicos de numeracion de la maquina

## Scripts basicos de numeracion:
```nmap
sudo nmap -sCV -p80,3306,33060 -n 192.168.58.129 -oN targeted
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-02-11 16:59 EST
Nmap scan report for 192.168.58.129
Host is up (0.00058s latency).

PORT      STATE SERVICE VERSION
80/tcp    open  http    Apache httpd 2.4.41 ((Ubuntu))
|_http-server-header: Apache/2.4.41 (Ubuntu)
|_http-title: Welcome to the land of pwnland
3306/tcp  open  mysql   MySQL 8.0.25-0ubuntu0.20.04.1
|_ssl-date: TLS randomness does not represent time
| ssl-cert: Subject: commonName=MySQL_Server_8.0.25_Auto_Generated_Server_Certificate
| Not valid before: 2021-07-03T00:33:15
|_Not valid after:  2031-07-01T00:33:15
| mysql-info: 
|   Protocol: 10
|   Version: 8.0.25-0ubuntu0.20.04.1
|   Thread ID: 40
|   Capabilities flags: 65535
|   Some Capabilities: ConnectWithDatabase, ODBCClient, InteractiveClient, SupportsCompression, Support41Auth, SupportsTransactions, Speaks41ProtocolNew, IgnoreSpaceBeforeParenthesis, LongPassword, IgnoreSigpipes, SwitchToSSLAfterHandshake, SupportsLoadDataLocal, Speaks41ProtocolOld, FoundRows, DontAllowDatabaseTableColumn, LongColumnFlag, SupportsMultipleStatments, SupportsMultipleResults, SupportsAuthPlugins
|   Status: Autocommit
|   Salt: \x1A#oDn==	\x101
| K
| Q>Q\x18\x18*\x1C
|_  Auth Plugin Name: caching_sha2_password
33060/tcp open  mysqlx?
| fingerprint-strings: 
|   DNSStatusRequestTCP, LDAPSearchReq, NotesRPC, SSLSessionReq, TLSSessionReq, X11Probe, afp: 
|     Invalid message"
|     HY000
|   LDAPBindReq: 
|     *Parse error unserializing protobuf message"
|     HY000
|   oracle-tns: 
|     Invalid message-frame."
|_    HY000
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
SF-Port33060-TCP:V=7.94SVN%I=7%D=2/11%Time=65C94342%P=x86_64-pc-linux-gnu%
SF:r(NULL,9,"\x05\0\0\0\x0b\x08\x05\x1a\0")%r(GenericLines,9,"\x05\0\0\0\x
SF:0b\x08\x05\x1a\0")%r(GetRequest,9,"\x05\0\0\0\x0b\x08\x05\x1a\0")%r(HTT
SF:POptions,9,"\x05\0\0\0\x0b\x08\x05\x1a\0")%r(RTSPRequest,9,"\x05\0\0\0\
SF:x0b\x08\x05\x1a\0")%r(RPCCheck,9,"\x05\0\0\0\x0b\x08\x05\x1a\0")%r(DNSV
SF:ersionBindReqTCP,9,"\x05\0\0\0\x0b\x08\x05\x1a\0")%r(DNSStatusRequestTC
SF:P,2B,"\x05\0\0\0\x0b\x08\x05\x1a\0\x1e\0\0\0\x01\x08\x01\x10\x88'\x1a\x
SF:0fInvalid\x20message\"\x05HY000")%r(Help,9,"\x05\0\0\0\x0b\x08\x05\x1a\
SF:0")%r(SSLSessionReq,2B,"\x05\0\0\0\x0b\x08\x05\x1a\0\x1e\0\0\0\x01\x08\
SF:x01\x10\x88'\x1a\x0fInvalid\x20message\"\x05HY000")%r(TerminalServerCoo
SF:kie,9,"\x05\0\0\0\x0b\x08\x05\x1a\0")%r(TLSSessionReq,2B,"\x05\0\0\0\x0
SF:b\x08\x05\x1a\0\x1e\0\0\0\x01\x08\x01\x10\x88'\x1a\x0fInvalid\x20messag
SF:e\"\x05HY000")%r(Kerberos,9,"\x05\0\0\0\x0b\x08\x05\x1a\0")%r(SMBProgNe
SF:g,9,"\x05\0\0\0\x0b\x08\x05\x1a\0")%r(X11Probe,2B,"\x05\0\0\0\x0b\x08\x
SF:05\x1a\0\x1e\0\0\0\x01\x08\x01\x10\x88'\x1a\x0fInvalid\x20message\"\x05
SF:HY000")%r(FourOhFourRequest,9,"\x05\0\0\0\x0b\x08\x05\x1a\0")%r(LPDStri
SF:ng,9,"\x05\0\0\0\x0b\x08\x05\x1a\0")%r(LDAPSearchReq,2B,"\x05\0\0\0\x0b
SF:\x08\x05\x1a\0\x1e\0\0\0\x01\x08\x01\x10\x88'\x1a\x0fInvalid\x20message
SF:\"\x05HY000")%r(LDAPBindReq,46,"\x05\0\0\0\x0b\x08\x05\x1a\x009\0\0\0\x
SF:01\x08\x01\x10\x88'\x1a\*Parse\x20error\x20unserializing\x20protobuf\x2
SF:0message\"\x05HY000")%r(SIPOptions,9,"\x05\0\0\0\x0b\x08\x05\x1a\0")%r(
SF:LANDesk-RC,9,"\x05\0\0\0\x0b\x08\x05\x1a\0")%r(TerminalServer,9,"\x05\0
SF:\0\0\x0b\x08\x05\x1a\0")%r(NCP,9,"\x05\0\0\0\x0b\x08\x05\x1a\0")%r(Note
SF:sRPC,2B,"\x05\0\0\0\x0b\x08\x05\x1a\0\x1e\0\0\0\x01\x08\x01\x10\x88'\x1
SF:a\x0fInvalid\x20message\"\x05HY000")%r(JavaRMI,9,"\x05\0\0\0\x0b\x08\x0
SF:5\x1a\0")%r(WMSRequest,9,"\x05\0\0\0\x0b\x08\x05\x1a\0")%r(oracle-tns,3
SF:2,"\x05\0\0\0\x0b\x08\x05\x1a\0%\0\0\0\x01\x08\x01\x10\x88'\x1a\x16Inva
SF:lid\x20message-frame\.\"\x05HY000")%r(ms-sql-s,9,"\x05\0\0\0\x0b\x08\x0
SF:5\x1a\0")%r(afp,2B,"\x05\0\0\0\x0b\x08\x05\x1a\0\x1e\0\0\0\x01\x08\x01\
SF:x10\x88'\x1a\x0fInvalid\x20message\"\x05HY000");
MAC Address: 00:0C:29:0D:F2:9F (VMware)

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 14.89 seconds

```
##WEB
y bueno estos han sido los resultados por ahora no veo nada interesante asi que veremos la web

![](/assets/images/web.PNG)

bueno no veo nada interensate asi que vere el codigo de la pagina a ver que encontramos

![](/assets/images/page or.PNG) 
En la ruta main.js que esta en la parte inferior de codio de la pagina vemosen la parte comentada una ruta

![](/assets/images/page login.PNG)

Al Entrar nos encontramos un login lo que podriamos hacer seria buscar las credenciales por defecto de internet pero eso en realidad no funciona

Otra opcion y es la que haremos sera intentar buscar el github de este servidor para asegurarnos si hay alguna fuga de seguridad o una mala confinguracion y ver que encontramos

![](/assets/images/github.PNG)

Bueno al entrar nos damos cuenta que hay un archivo conf y vamos a ver si existe en nuestra pagina

![](/assets/images/forbiden.PNG) 

Vemos que si existe asi que entraremos en la carpeta del github para ver quenos encontramos

![](/assets/images/github seedms.PNG)

Uy y eso hay una advertencia en unos de los archivos que en concreto el directorio no se puede ver de manera externa vamos a ver si lo a configurado bien

![](/assets/images/base.PNG)

Y pues xd no lo tienen en la parte abajo tienen expuestos asi que acederemosutilizando el puerto de mysql

![](/assets/images/msql.PNG)

ya a dentro comenzaremos a ver las bases de datos y las tablas para ver que encontramos

![](/assets/images/show database.PNG)

![](/assets/images/show tables.PNG)
bueno entramos a seeddms y vemos las tablas vemos que hay una de users y pues vamos a ver que contiene


![](/assets/images/saket3.PNG)

Bueno vemos que dentro de users esta esa clave y contraseña y aunque lo utilizamos en la pagina no funciona 

Bueno tambien vemos otro users pero esta vez tlbUsers asi que le haremos un describle y veremos

![](/assets/images/describe tlbUSers.PNG)

![](/assets/images/tlbUsers.PNG)

Seleccionamos la rama y vemos un user y una password podemos decifrar la password o poner una nueva en este caso poner una nueva  

![](/assets/images/md5sum.PNG)

Bueno como vemos al tener 32 caracteres significa que es md5sum entonces lo que haremos sera cambiar la contraseña por `adiosbb`

Entonces le pondremos el siguiente comando y automaticamente se actualizara la passwd
![](/assets/images/change passwd.PNG) 

![](/assets/images/adiosbb.PNG)

![](/assets/images/dentro.PNG)

Y bueno hemos cambiado la contraseña :v ahora entraremos por la pagina a ver que encontramos

![](/assets/images/update shell.PNG)

Vemos que en alguna parte de la pagina se puede subir un archivo esto lo podemos utilizar para explotar pero como no sabemos cual archivo subir utilizaremos vamos a usar searchsploit a ver que encontramos

![](/assets/images/searchploit.PNG)

Vemos que al indigar la maquina hay un remote command execution lo descargaremos y miraremos

![](/assets/images/sf.PNG)

Al abrirlo nos damos cuenta que tiene una instruccion para subir una archivo php pidiendo una shell

![](/assets/images/adios.PNG)

Hacemos el archivo y lo subimos 


ya hecho todo esto podemos subir el archivo e ir ala ruta de subida de  este tal como lo indica el exploit

![](/assets/images/documt_id.PNG)

Una pequeña cuestion cuando ya lo hemos subido volvemos ala pagina de inicio y buscamos el id esto nos servira para ir ala ruta pleestablecida en este caso mi id es el numero `4`

![](/assets/images/etcpasswd.PNG)

:v FUNCIONNAA luego despues de esto tendremos que hacer una [Reverse Shell](https://medium.com/@cuncis/reverse-shell-cheat-sheet-creating-and-using-reverse-shells-for-penetration-testing-and-security-d25a6923362e) por el puerto local que le indiquemos asi que abrimos un puerto por `nc` en este caso el mio sera por el puerto `443`

![](/assets/images/nc.PNG)

Ya hecho todo esto podemos comenzar a hacer la `reverse shell`

![](/assets/images/reverse shell.PNG)

Ya hecha nuestra reverse shell podemos ver los puertos a ver si ya nos an dado acceso

![](/assets/images/here.PNG) 

:v vemos que si ya tenemos acceso y estamos adentro de la maquina victima

![](/assets/images/reset xterm.PNG)

Lo que hacemos ahora es volver la terminal una tty para que nos acepte comandos como clt+c clt+l etc 

luego despues de esto comenzaremos a ver la maquina Y vemos que hay un usuario saket igual al de la base datos que nos aparecia antes talvez usando sus credenciales podamos entrar 

![](/assets/images/su saket.PNG)

y bueno vemos que si funciona xd bueno aqui viene la parte ultradificil para convertinos en root

haciendo `sudo -l` y metemos la contraseña de saket pues podemos ver que podemos hacer lo que nos de la gana asi que haciendo un `sudo su`

Pues ya somos root

![](/assets/images/sua.PNG)

JAJAHAHAJAJAJAJAJAJJAJAJAJAJAJAJAJAJAJAJA
