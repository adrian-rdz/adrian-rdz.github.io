---
title: SSH usar servicios remotos y conectar discos duros remotos
date: 2018-12-27
author: Adrián Rodríguez
tags: [técnico, ssh, tunel-ssh, bash]
excerpt: "Explorar usos de SSH como tuneles para conexión a servicios como bases datos y conexión a discos duros remotos usando SSHFS"
mathjax: "true"
---

## SSH introducción e historia

SSH que significa secure shell , es protocolo seguro de comunicaciones, y viene a ser como la version mejorada y securizada de telnet, el cual igualmente era un protocolo de conexión

SSH es la herramienta preferida para conectarse  por consola a un servidor, o aun VPS en la nube que tengamos contratado, por ejemplo en Amazon o DigitalOcean u otro proveedor la forma mas comun de conectarse es usando ssh o tuneles ssh

La diferencia primordial entre ssh y telnet es que en telnet los datos de conexión viajan en texto plano, por  lo cual alguien que snifee y capture el trafico de la red con alguna herramienta como wireshark, puede ver users, passwords y demas datos que viajan a travez de ese protocolo; Con ssh ya no se tiene este problema dado que usan cifrado  de llave publica y pues es mucho mas seguro por lo mismo, como nota aun y que sea mas seguro hay que mantener actualizado el servidor y cliente dado que si se encuentran vulnerabilidades cada X tiempo.

## Aplicaciones

## Conectarse por consola

El uso mas habitual de ssh es entrar a la consola de comnados de un server

```bash
## Conectarse a server en red local
ssh usuario@192.168.1.70

## conectarse a server de manera remota
ssh usuario@ipexterna
```

Siempre hay que tomar en cuenta que si nos encontramos en la misma red que el servidor al que nos  queremos conectar lo mejor es usar la ip de la red local, dado que el ancho de banda sera mas alto, (esto es aun mas recomendable en los tuneles que pueden llegar a usar mas ancho de banda)

### Tuneles SSH

Si no eres un sysadmin o si simplemente no eres muy agil en la consola puedes pensar de que te podria servir SSH, pues si que puede servirte por medio de tuneles

Un tunel SSH es una manera de conectarse o securizar una conexión y encriptar todo el trafico de esa conexión, por ejemplo hoy en día no es recomendable usar FTP simple, y por lo cual existe SFTP (Secure FTP) y FTPS, suponiendo que ya tienes un server de FTP simple (el cual no esta encriptado) una forma de conectarse a el de manera segura sería hacer un tunel ssh

Supon que tienes habilitada una base de datos en un servidor, y por obvias razones no quieres exponer a internet el puerto sobre el que trabaja la base de datos, por que tienes información sensible, pero aun asi te gustaria poder acceder de manera remota a la base de datos; pues lo que puedes hacer es acceder por medio de un tunel

Existe 2 formas de realizar el caso de uso previo

1. Crear el tunel desde la consola y despues conectarse a la base haciendo referencia a localhost y el puerto
   1. ![](https://i.imgur.com/Ja3ZIFV.png)
   2. ![](https://i.imgur.com/umcUxgg.png)
2. Crear el tunel desde la misma aplicacion y ahi mismo conectarse
   1. ![](https://i.imgur.com/8iFvGq6.png)

Hay aplicaciones como esta en las cuales el cliente mismo te permite realizar el tunel y pues es muy conveniente pero no todas las aplicaciones permiten hacer esto

Por ejemplo si tengo en un servidor corriendo anaconda y jupyter notebook , y quiero hacer un analisis en ese server , pero no quiero exponer el puerto jupyter notebook dado que no tiene configurado https, la solucion igualemente es realizar el tunel y despues abrir la direccion localhost asi podre trabajar remotamente con jupyertnotebook y accederlo en mi computadora desde localhost y todo el trafico viajaria encriptado

Pasos

1. Realizar tunel ssh al puerto 8888

   1. ```
      ssh -L 8888:localhost:8888 tuzy-server@192.168.1.70
      ```

      Se debe dejar el tunel abierta mientras querramos trabajar con jpuyter

2. Abrir el navegador y entrar a 127.0.0.1:8888 y ahi ya puedes trabajar con jupyter

![img](https://i.imgur.com/ZQUDMfI.png)

### Consideraciones

- El cliente de ssh forma parte de los binarios que vienen preinstalados , pero el server si hay que instalarlo, 
- para los usuarios de windows ahi si se ocupa un aplicacion para correr el cliente, con putty se puede hacer de manera facil, existen otras mas.

### Conectar discos duros remotos

Otro de los usos que le podemos dar  a ssh es conectar discos duros remotos en nuestra maquina, esto es muy conveniente en ciertas situaciones, ya que a veces tenemos que estar trabajando con datos de un servidor y es mas engorroso trabajarlo y luego subirlo por sftp, para evitar ese embrollo podemos instalar sshfs (Secure Shell File System)

Pasos:

- Instalar SSHFS

  - ```
    sudo apt install -y sshfs
    ```

- Habilitar un directorio donde montaremos el disco remoto

  - ```
    mkdir /media/tuzy
    ```

- Hacer el enlace o tunel

  - ```bash
    adrianrdzv@adrianrdzv-lap:~$ sudo sshfs -o allow_other tuzy-server@192.168.1.70:/home/tuzy-server/ /media/tuzy
    [sudo] password for adrianrdzv: 
    tuzy-server@192.168.1.70's password: 
    ```

  - Se te solicitara el password tanto de tu maquina como del server remoto

  - Imagen del directorio montado

    - ![](https://i.imgur.com/Fofi4Ws.png)

Para este caso solo se monto la carpeta de un usario del servidor, pero pudo haberse montado todo el disco duro entero si se desea

```
sudo sshfs -o allow_other tuzy-server@192.168.1.70:/ /media/tuzy
```

## Conclusiones

Cualquier protocolo inseguro le puedes añadir una capa de seguridad al no exponerlo directamente a internet y mejor accederlo sobre tuneles ssh, quizas es algo muy tecnico y poca gente lo usa , 

Cabe recordar que en ambientes empresariales por lo general se prefiere usar VPN (Redes virtuales privada), cualquiera que le toque trabajar de home-office quizas lo haya usado para trabajar remotamente con las aplicaciones de la compañia de una forma segura.