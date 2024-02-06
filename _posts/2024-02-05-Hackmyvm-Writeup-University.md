---
layout: single
title: University - Hackmyvm
excerpt: "Un ctf facil de la plataforma hackmyvm  donde vemos vulnerabilidades como online adminission system y gerapy"
date: 2024-02-05
classes: wide
header:
  teaser: /assets/images/hackmyvm-writeup-university/university-logo.png
  teaser_home_page: true
categories:
  - hackmyvm
  - ctf
tags:
  - linux
  - easy
  - ctf-like
  - CVE-2021-43857
  - Online Admission System
  - nickdevplus-ctf
---
Link: [Descarga Maquina](https://hackmyvm.eu/machines/machine.php?vm=University)

![](/assets/images/hackmyvm-writeup-university/university-logo.png)

El dia de hoy vamos a hacer una maquina facil de hackmyvm y bueno para mi es facil pero si no estas atento al final a todo se te puede complicar el root

## Bueno comenzamos

Comenzamos con un ping para saber si tenemos traza y conexion con nuestra maquina victima

```console
# ping -c 1  192.168.10.25
PING 192.168.10.25 (192.168.10.25) 56(84) bytes of data.
64 bytes from 192.168.10.25: icmp_seq=1 ttl=64 time=3.11 ms

--- 192.168.10.25 ping statistics ---
1 packets transmitted, 1 received, 0% packet loss, time 0ms
rtt min/avg/max/mdev = 3.113/3.113/3.113/0.000 ms

```
Bueno vemos que la maquina nos responde y es ttl=64 --- osea linux si fuera 128 seria windows

## Comenzamos con el escaneo por nmap

Luego despues de hacer el ping haremos un escaneo por nmap para encontrar puertos abiertos

```console
#  sudo nmap -sS -p- --open --min-rate 5000 -n -Pn 192.168.10.25
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-02-05 13:10 EST
Nmap scan report for 192.168.10.25
Host is up (0.0023s latency).
Not shown: 65533 closed tcp ports (reset)
PORT   STATE SERVICE
22/tcp open  ssh
80/tcp open  http
MAC Address: 08:00:27:34:E9:E3 (Oracle VirtualBox virtual NIC)

```
## Que tenemos hasta este momento:

```
# Puerto 80 y 22 abierto , http y ssh 

ademas de la mac de la maquina xd

```
Despues de ver estos puertos abiertos meteremos unos scripts basicos de enumeracion para ver que ocultan estos puertos

```console
# nmap -sCV -p80,22 192.168.10.25 -oN targeted           
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-02-05 13:15 EST
Nmap scan report for 192.168.10.25
Host is up (0.0015s latency).

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.4p1 Debian 5 (protocol 2.0)
| ssh-hostkey: 
|   3072 8e:ee:da:29:f1:ae:03:a5:c3:7e:45:84:c7:86:67:ce (RSA)
|   256 f8:1c:ef:96:7b:ae:74:21:6c:9f:06:9b:20:0a:d8:56 (ECDSA)
|_  256 19:fc:94:32:41:9d:43:6f:52:c5:ba:5a:f0:83:b4:5b (ED25519)
80/tcp open  http    nginx 1.18.0
|_http-server-header: nginx/1.18.0
| http-cookie-flags: 
|   /: 
|     PHPSESSID: 
|_      httponly flag not set
| http-git: 
|   192.168.10.25:80/.git/
|     Git repository found!
|     Repository description: Unnamed repository; edit this file 'description' to name the...
|     Remotes:
|_      https://github.com/rskoolrash/Online-Admission-System
|_http-title: Site doesn't have a title (text/html; charset=UTF-8).
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 8.17 seconds

```
## Que tenemos hasta ahora

* Un /.git/ --- Lo ves y te tira un 302 un forwand

* [El repositorio](https://github.com/rskoolrash/Online-Admission-System)

![](/assets/images/hackmyvm-writeup-university/admission.png)

Viendo el github del escaneo de nmap se puede ver que es el sistema con el que esta creado el panel de login
lo que me hizo buscar si en searchsploit habia alguna vulnerabilidad con este fichero

## Buscando en searchsploit

```console
# * searchsploit Online Admission System
------------------------------------------------------------------------------------------------------------------------------------ ---------------------------------
 Exploit Title                                                                                                                      |  Path
------------------------------------------------------------------------------------------------------------------------------------ ---------------------------------
Online Admission System 1.0 - Remote Code Execution (RCE) (Unauthenticated)                                                         | php/webapps/50623.py
------------------------------------------------------------------------------------------------------------------------------------ ---------------------------------
Shellcodes: No Results

```
bueno vemos que tiene una vulnerabilidad asi que lo guardamos en nuestra maquina y vamos pa dentro :3

Ya en la maquina ejecutamos el script para ver sus instrucciones y ver como lo ejecutamos

![](/assets/images/hackmyvm-writeup-university/10.png)

Necesitamos la ip que este en caso es la 192.168.10.25 y el puerto que en este caso el 80 ademas de nuestra ip cuando dice localhost y local port se refiere a nuestra ip y un puerto que este caso abriremos con netcat para que pueda explotar la vulnerabilidad y podamos acceder e ir para adentro de la maquina linux
 
![](/assets/images/hackmyvm-writeup-university/11.png)

ya habierto el puerto ejecutare el sploit 

![](/assets/images/hackmyvm-writeup-university/12.png)

y pues ponemos python3 -c 'import pty; pty.spawn("/bin/bash")' para que nos de una bin/bash mediante python

![](/assets/images/hackmyvm-writeup-university/13.png)

vemos que hemos entrado ala maquina y somos data tenemos que comenzar a escalar privilegios

![](/assets/images/hackmyvm-writeup-university/14.png)

Buscando en los archivos con privelegios e encontrado esto `.sandra_secret` 

![](/assets/images/hackmyvm-writeup-university/15.png)

Lo abrimos con cat

![](/assets/images/hackmyvm-writeup-university/16.png)

Nos da esta contraseña `Myyogaiseasy` para entrar por `ssh` Y vamos a entrar
![](/assets/images/hackmyvm-writeup-university/17.png)

## Entramos a donde sandra

Bueno ya dentro podemos ver la flag user que antes no nos dejaba

![](/assets/images/hackmyvm-writeup-university/18.PNG)

```
# User flag:

HMV0948328974325HMV

```

Bueno y ya comenzaremos con la escala de privilegios para volvernos superusuario root

## Pasamos a root y violamos la maquina xd

![](/assets/images/hackmyvm-writeup-university/19.PNG)

Bueno viendo varias cosas `sudo -l` hay un archivo que en el momento de ejecucion nos lo deja ejecutar como superusuario `/usr/local/bin/gerapy`

Bueno ahora veremos si en searchsploit tiene alguna vulnerabilidad para ejecutarla

![](/assets/images/hackmyvm-writeup-university/20.PNG)

Bueno vemos que si entonces lo bajamos en la maquina y lo ejecutamos para ver que necesitamos

![](/assets/images/hackmyvm-writeup-university/21.PNG)

Pero Bueno vemos que necesitamos lo mismo que el anterior ip de la maquina victima y el puerto pero que te genera gerapy y pues bueno la ip de nuestra y nuestro puerto asi que antes de eso iremos ala libreria en github de gerapy para ver como ejecutamos y como levantamos el servidor

![](/assets/images/hackmyvm-writeup-university/22.PNG)

Pues bueno luego montamos todo pero es mejor dejar el usuario admin y la contraseña igual si no tienen que ir y cambiar eso en exploit y batallan pero igual si les pasa ya saben para que no les pase como yo xd

y ejecutar aunque parece obvio el servidor como root `sudo` a menos que quieran explotar ala misma pobre sandra xd

![](/assets/images/hackmyvm-writeup-university/23.PNG)

Y bueno montamos el puerto con el netcat por `9000` que luego lo cambie ... pero eso no tiene importancia

![](/assets/images/hackmyvm-writeup-university/24.PNG)


Bueno lo cambie fue por ciertos errores que me salieron y probando cosas pero les dire un error que deben tener en cuenta


![](/assets/images/hackmyvm-writeup-university/30.PNG)

cuando salga este error deben hacer lo siguien:


Bueno irse a la pagina que te monta el gerapy osea la ip y el puerto que te da


![](/assets/images/hackmyvm-writeup-university/26.PNG)


Bueno en este caso seria `admin:admin` y entrar al gestor de contenido e irse a proyects


![](/assets/images/hackmyvm-writeup-university/27.PNG)

y Bueno crear uno en este caso le puse test y luego ir otra vez al exploit y ya colara y pues bueno haz hackeado la maquina ya puedes ver la flag root hacer un dev/null y mandar la maquina alv etc


![](/assets/images/hackmyvm-writeup-university/31.PNG)
----


![](/assets/images/hackmyvm-writeup-university/32.PNG)


----




















