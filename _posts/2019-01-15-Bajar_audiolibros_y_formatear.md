---
author: Adrián Rodríguez
date: 2019-01-15
title: Descargar audiolibros y formatearlos (Bash-Linux)
tags: [youtube-dl,ivoox,audiolibro,ffmpeg,audio,codificación,mp3,m4a,libro,bash,linux]
---

## Como descargar audiolibros y formatearlos para escucharlos adecuadamente

Existen varias webs donde puedes conseguir audiolibros entre ellas  ivoox y youtube, en el casos de ivoox puedes descargar directamente en mp3 y en ocasiones ya vienen particionados de manera que sea facil escucharlos pero en el caso de youtube dado que no es su proposito por lo general los encuentras todo el audio en un solo video que puede llegar a tener una larga duración dependiendo lo largo del libro

Para obtener un audiolibro de youtube puedes descargarlo usando una herramienta que se usa por consola llamado youtube-dl o tambien existen webs para descargar video y audio de youtube tales como [https://www.clipconverter.cc/es](https://www.clipconverter.cc/es/) o este otro [https://mp3-youtube.download/es ](https://mp3-youtube.download/es), en este caso mostrare como hacerlo desde consola usando youtube-dl

existe varias formas de instalar youtube-dl yo por lo geneal lo instalo con pip

```bash
sudo pip install --upgrade youtube_dl
```

Con youtube-dl puedes descargar videos y audios de youtube y muchos otros servicios similares e incluso sitios web que no son muy populares.

### Procedimiento

Descargar audiolibro o audio de youtube

```
youtube-dl -F youtube-link
```

Obtener solo el audio (m4a)

```bash
#Despues del tag f se pone el formato correspondiente al audio
youtube-dl -f 140 youtube-link
```

Nota: es posible descargar en mp3 en lugar de m4a con youtube-dl teniendo instalado ffmpeg (cambia ligeramente el comando ) aunque en realidad solo lo que hace el comando es juntar el paso de descargar el m4a y convertirlo en mp3 en un solo paso.

Como lo estoy bajando directo en formato m4a tengo que convertir el audio con el siguiente comando

```bash
#Convertimos de m4a a mp3 
ffmpeg -i audiolibro.m4a -vn -ab 128k output.mp3
```

En mi caso prefiero tener los audiolibros cortados por duraciones que pueda escuchar de una sola vez

Aqui hay 2 opciones cortar usando la utileria split que viene por defecto en unix/linux o cortar con ffmpeg

- Para el caso de mp3's es posible cortar con split ()con el formato m4a no quedan servibles los cortes)

- cortar con ffmpeg ( se puede crear subarchivos de menor duración con un comando de ffmpeg)

Cortamos por pedazos de 40 megas (dado que son 700 megas y son aprox 13 horas, los cortes serian de una 45 minutos aprox)

```bash
split -C 40m --numeric-suffixes output.mp3 output
```

```bash
#adrianrdzv@adrianrdzv-lap:audiolibros$ ls output*
output00  output01  output02  output03  
output04  output05  output06  output07
output08  output09  output10  output11  
output12  output13  output14  output15  
output16  output17  output18  output.mp3
```

Nota: el comando split esta destinado a cortar archivos en general, pero que los pedazos sean útiles por si solos no es garantizable en el caso de textos se puede y alguna vez lo aplique a audios mp3 y note que los pedazos quedan funcionales asi que es una buena opción y muy rapida dado que no se esta recodificando el audio, con otros formatos por ejemplo m4a no funciona los cortes de audio, este comando split normalmente se usa para poder partir archivos muy grandes y despues se puede reconstruir dicho archivo usando el comando join, tal como cuando tenemos multiples archivos rar que componen un solo archivo, para que sea mas facil transmitirlos por algun servicio de almacenamiento web.

Ahora falta anexarles nuevamente la extension mp3

Movemos los pedazos a una nueva carpeta

```bash
mv output* folder_new
cd folder_new
```

Con este comando bash logramos anexarles la extensión a cada archivo

```bash
for item in *; do mv "$item" "${item}_.mp3"; done;
```

Vuala hemos terminado ya podemos pasarlo a nuestro reproductor mp3 o celular para escucharlos.

## Consideraciones

Cabe notar que muchos audiolibros encontrados en internet pueden no ser de dominio público, pero existen canales de youtube que solo suben audiolibros de libros que ya forman parte del dominio público. Hay uno interesante que lo que hace es subir audiolibros de dominio público, hablados por la voz de jorge de loquendo (La tipíca voz española que escuchas en los videotutoriales de vez en cuando)

[Canal de audiolibros de dominio público hablados por voz computarizada](https://www.youtube.com/channel/UC1suAutVkyYsadqZjFouJNw/videos)

## Referencias

- https://stackoverflow.com/questions/24592342/bash-add-suffix-to-all-files-in-the-directory-with-an-extension

- [https://www.computerhope.com/unix/usplit.htm](https://www.computerhope.com/unix/usplit.htm)

- [https://stackoverflow.com/questions/2016894/how-to-split-a-large-text-file-into-smaller-files-with-equal-number-of-lines](https://stackoverflow.com/questions/2016894/how-to-split-a-large-text-file-into-smaller-files-with-equal-number-of-lines)

- [https://www.ostechnix.com/20-ffmpeg-commands-beginners/](https://www.ostechnix.com/20-ffmpeg-commands-beginners/)

- [https://superuser.com/questions/820747/slicing-video-file-into-several-segments/820773#820773](https://superuser.com/questions/820747/slicing-video-file-into-several-segments/820773#820773)

- [https://www.ffmpeg.org/ffmpeg-formats.html#segment_002c-stream_005fsegment_002c-ssegment](https://www.ffmpeg.org/ffmpeg-formats.html#segment_002c-stream_005fsegment_002c-ssegment)

- [https://unix.stackexchange.com/questions/24630/whats-the-best-way-to-join-files-again-after-splitting-them](https://unix.stackexchange.com/questions/24630/whats-the-best-way-to-join-files-again-after-splitting-them)
