---
title: Comandos útiles Linux (bash)
date: 2019-01-02
author: Adrián Rodríguez
tags: [tecnico, linux, bash, consola]
excerpt: "Comandos bash utiles para Linux"
mathjax: "true"
---

## Introducción

El usar mayormente la consola (CLI o TUI's Text User Interfaces) en lugar de usar programas con ambiente grafico (GUI's Graphical User Interfaces) puede ser visto como anticuado por algunas personas, pero en mi experiencia y punto de vista creo que puede darte una mayor flexibilidad en algunos casos, y para ciertas tareas.

![Imgur](https://i.imgur.com/sgxCYRG.png)

La terminal por defecto en la mayoría de sistemas linux usan el interprete bash y bourne shell **sh**, existen otras variantes (csh zsh, korn shell) que igual tienen sus ventajas mas yo no he usado esas otras variantes ,en el caso de windows existe MSDos y powershell, MSDOS es muy pobre en cuanto a lo que se puede hacer, pero parece ser que powershell si fue una mejora o un intento de tener un bash en windows, y si no te gusta puedes usar bash en windows si gozas de tener ciertas versiones de windows 10 puedes correr un ubuntu dentro de windows, y la otra opcion libre la cual yo usaba era instalar **cygwin** el cual es un emulador de terminal linux en windows, con lo cual puedes usar muchos comandos de linux usar pipes, y demas utilerias como ssh en windows.

## Casos de uso

Algunas tareas para las cuales yo recomendaría usar la terminal son las siguientes:

1. Operaciones de archivos (mover, copiar, crear directorios, etc..)

  - Sobre todo cuando se quiere hacer cambios masivos
2. Operaciones de busqueda de archivos o busqueda de un texto dentro de archivos

  - Usando los comandos locate, cat y grep  se puede lograr facilmente
3. Como lo describí en otro post un primer analisis de conjuntos de datos se puede realizar en consola y en  veces de forma mas eficiente cuando se maneja archivos de gran tamaño

  - comandos cat, less, more, grep, sed, awk, tuberías, tr, column
4. Normalizacion de Textos
  - comando tr, sed, sort, uniq (Ver jurafsky Speech and Language Processing (Draft page 23))
  - para ver normalizaciones y preprocesos mas avanzados usando solo consola ver [https://fasttext.cc/docs/en/supervised-tutorial.html](https://fasttext.cc/docs/en/supervised-tutorial.html)
5. Automatizar operaciones usando bash o shell-scripts y cron
6. Conectarse a servidores remotos como se describio en otro post

## Comandos Esenciales

Los comandos mas esenciales a aprender son los de manejo de directorios creacion de archivos, 

En mi opinion el primer comando a aprender es **man**  (se usa **man comando_a_revisa**r) este comando sirve para ver la documentacion de un comando desde la consola, de hecho puedes ver la propia documentación de man usando **man man** , otro de los comandos mas utiles es **history** para ver el historial de comandos que hemos usado

Los comandos para manejo de archivos que mas uso y veo conveniente saber son:

- **ls**
  - Listar los archivos y directorios del directorio en el que te encuentras o el que se mande de parametro
- **mv**
  - Mover un archivo  o carpeta
- **cp**
  - Copiar archivos o directorios
- **touch**
  - Crear un archivo vacio
- **mkdir**
  - Crear un directorio

Comndos de administración

- **ps**
  - Ver los procesos corriendo en nuestro sistema
- **df**
  - Ver el uso de disco
- **free**
  - Ver el uso de memoria RAM y SWAP
- **top**
  - Ver estadisticas del sistema como lo que se ve con df free y ps en un solo comando
- **htop**
  - Un mejor top
- **du**
  - Ver el uso de disco en una carpeta (mostrar el uso de almacenmiento por cada carpeta y archivo)
- **lshw**
  - Listar el hardware disponible y algunos detalles
- **lspci, lsusb**
  - Mostrar el hardware pci y usb respectivamente
- **dmesg**
  - Mostrar mensajes del sistema (muy util para detectar problemas de sistema o ver como se detecta algun dispositivo de hardware al ingresarlo)
- **chown**
  - Modificar los dueños de un directorio o archivo (user y grupo), 
- **chmod**
  - Modificar los permisos de un directorio

Comandos de red y servicios:

- **ping**
  - Verificar la conexión o disponibildiad de un server o sitio
- **ifconfig** (el nuevo comando es ip)
  - Ver detalles de conexión (existe un version para ver solo lo wireless iwconfig)
- **ss**
  - Ver conexiones de sockets, muy util para debugear aplicaciones que usen red, o para encontrar procesos corriendo sobre ciertos puertos
- **lsof -i:port**
  - Este comando es muiy util para encontrar un proceso asociado a un puerto especifico
- **lsof**  (directorio | archivo)
  - Lista los procesos haciendo uso de archivos de cierto directorio o archivo
- **traceroute**
  - Ver la ruta que sigue un paquete de red al viajar desde la fuente hasta el destino (lista todos los servers por los que viaja un paquete)
- **digg**
  - Ver el dns usado para resolver un host
- **hostnamectl**
  - Modificar el nombre de host 

Otros comandos utiles

- **ssh**
  - Conectarse a un server , crear tuneles, tuneles inversos etc..
- **curl**
  - consultar web desde consola, descargar un archivo, o hacer perticiones post
- **wget**
  - Descargar archivos desde consola, util usarlo en Dockerfiles para descargar software y librerias de manera automatizada
- **sftp**
  - transferir archivos de manera segura
- **git**
  - Usar control de versiones sobre documentos y software
- **head**
  - Listar los primeros n lineas de un archivo
- **tail**
  - Mostar las ultimas lineas de un archivo (muy util usar tail -f para ver las ultimas lineas de un archivo y ver como cambian en tiempo rea, ideal para visualizar logs)
- **youtube-dl**
  - descargar videos de ciertas plataformas web como youtube
- **links**
  - Navegar desde consola con este browser en modo texto
  - ![links](https://i.imgur.com/EOKwu2X.png)

## Comandos de ejemplo de uso comun que tengo en mi historial

```bash
#Descargar un video de youtube
youtube-dl https://www.youtube.com/watch?v=_ZVZXgTff2U

# Clonar un repositorio de github
git clone https://github.com/facebookresearch/fastText.git

# Enterenar un modelo NLP para clasiicar usando fasttext
/fasttext supervised -input salida_training.txt -output modelo_izqder -label __label__ -lr 1 -epoch 888 -neg 15 -ws 10 -wordNgrams 2 -dim 300

#Conetarme a un server en mi red local
ssh tuzy-server@192.168.1.70
# Listar un archivo y quitar duplicados
cat antonimo.txt | uniq
# Ver l contenido de un archivo sin duplicados y ordenado
cat antonimo.txt | sort | uniq
# Activar un ambiente virtual de python llamado keras
source activate keras
# Ver el estatus de git en el directorio que estoy posicionado (visualizar cambios)
git status 
# Agregar el seguimiento a un archivio con git
git add _posts/2018-12-30-GIS_primeros_pasos.md 
# Confirmar cambios con git
git commit -m "Se agrega post GIS primeros pasos"
# Mandar cambios al nodo master
git push 
# Escanear un archivo en busca de virus
clamscan 154.pdf 
# Contar las lineas de un archivo de texto
cat spanish_billion_words_00 | wc -l
# Correr un script o programa de python
python dash_gdp_slider.py 
# Buscar un archivo o binario indexado
locate turtl | grep bin
# Usar editor ligero de texto en consola (puede ser vim o emacs u otro)
nano dash_gdp_slider.py
# Usar un multiplexador de terminal para dividir la terminal en subventanas
tmux 
#usar python interactivamente
ipython
# usar R interactivo
R

```

Existen comandos que actuan de manera interactiva en el sentido de que se va actualizando lo que presenta en consola en tiempo real pero hay otros que no, pero para esos que no la solucion es usar el comando watch, comando con el que podras monitorizar la salida de otro programa

```bash
## Ver el uso de memoria refrescando cada segundo
watch free -m
## Ver el consumo de la memoria de gpu y los procesadores gpu's en tiempo real
watch nvidia-smi

```



Existe muchos comandos y hay algunos que en algun tiempo use mucho y despues no y se fueron borrando de mi historial, es de tomar en cuenta que el historial tiene un limite el cual se puede ajustar, por lo general si mal no recuerdo el default es 1k o 2k comandos, por lo cual si usas mucho la consola puede pasarte muy seguido que un comando largo se te pierde de tu historial, eso se puede solucionar aumentando el tamaño del history o haciendo respaldos del history cada x tiempo lo cual lo puedes programar con cron.

Para ver muchos mas comandos utiles recomiendo estos enlaces:

- [https://www.howtoforge.com/linux-commands/](https://www.howtoforge.com/linux-commands/)
- [https://www.commandlinefu.com/commands/browse](https://www.commandlinefu.com/commands/browse)
- [https://www.linuxito.com/gnu-linux/nivel-basico/988-comandos-y-trucos-de-linea-de-comandos-de-viejo-lobo-sysadmin](https://www.linuxito.com/gnu-linux/nivel-basico/988-comandos-y-trucos-de-linea-de-comandos-de-viejo-lobo-sysadmin)
- [https://github.com/jlevy/the-art-of-command-line/blob/master/README.md](https://github.com/jlevy/the-art-of-command-line/blob/master/README.md)
- https://stackoverflow.com/questions/5725296/difference-between-sh-and-bash

