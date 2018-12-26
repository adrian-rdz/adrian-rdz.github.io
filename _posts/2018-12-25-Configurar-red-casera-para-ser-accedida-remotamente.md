---
title: Configurar red casera para ser accedida remotamente
date: 2018-12-25
author: Adrián Rodríguez
---

## Configurar red casera para ser accedida remotamente

### Para que?

Motivaciones: lo primero que uno se preguntaria es el objetivo o el para que hacerlo, y mi respuesta es que puedes tener como tu nube personal, ya sabemos que en la actualidad hay muchos servicios que han quitado ciertas necesidades previas, pero el tener hosteados tus propios servicios puede darte mas seguridad o mas control (aunque tambiém puede llegar a  ser peor y mas inseguro); tambien para evitar gastar unos pesos (podría ser que gastes mas en luz **hay que evaluarlo**, hasta el momento yo veo que el gasto de luz que genero es muy poco, pero no lo uso intensivamente)

Se podría lograr tener por ejemplo un sitio web o blog hosteado en tu casa y que se alcanzable desde internet, o como tener tu propia version de dropbox, o como habilitar una maquina para realizar analisis (tener python, R y librerias listas para usar asi como los VPS que rentan en AWS o en DigitalOcean)

Dependiendo en la zona donde vivas puede que tengas mas o menos ancho de banda, esto es una cuestion clave, si no gozas de un internet decente quizas no sea muy funcional el hacer esto dado que dificilmente podras acceder o será muy lento

### ¿Como  realizarlo?

Cada servicio que se quiera habilitar conlleva una serie de pasos tecnicos, pero aqui me enfocare a primero decir como habilitar que tu maquina sea accedida remotamente; fuera de los servicios que quieras habilitar

Pasos resumidos:

1. Habilitar una maquina para que quede siempre encendida en tu casa (y lo mejor seria optar por alguna distribucion linux)
2. Instalar el server de openssh 
   - apt-get install openssh-server
3. Entrar a la configuración del router casero que te proporciona tu compañia telefonica o de cable
   1. ![](https://i.imgur.com/AJrF8Tl.png)
4. Entrar a la parte de port-forwarding (redirección de puertos) y hay que crear una regla para cada puerto que se quiera exponer (en este caso solo abrire el 81 y lo redireccionare al 22, (puede ser del 22 al 22))
   1. ![](https://i.imgur.com/2a71KnF.png)

1. Suponiendo del paso anterior que te concierna la seguridad, pienso yo que solo debieses exponer un puerto para realizar conexiones por SSH (Secure Shell) ( la regla general seria redireccionar 0.0.0.0:22 --- ip_maquina_linux_local:22 ) 
   - Con lo cual cada que se haga la peticion a la ip externa y el puerto 22 (ip de internet) se redirijara a la ip de la maquina habilitada

En el paso 4 si crees saber lo suficiente de seguridad informatica y contar con el tiempo necesario para estar siempre teniendo actualizado tu software y revisar logs, podrias exponer los puertos como el 80 para una pagina web, puertos para acceder a otros servicios como plex (centro multimedia remoto), servidor de torrents (para mandar descargar a tu maquina remotamente enlaces torrents) etc....

Pero si no tienes el tiempo de revisar o no sabes de seguridad lo mejor es solo exponer el puerto 22 de SSH y cualquier cosa a la que quieras acceder lo puedes hacer mediante un tunel ssh (Esto hace que la conexion a tu servicio viaje encriptada), aun y que esto produce algo mas de latencia en el trafico de la información dado qeu cada paquete de información conlleva informacion extra por que va encriptado, aun así creo es lo mas recomndable, otra opcion es configurar un servidor VPN en tu red local, y asi cuando quieras conectarte remtoamente accedes a la VPN (esto ultimo nunca lo he hecho)

Al día de hoy yo puedo acceder a los siguiente servicios en mi maquina a traves de tuneles ssh (de forma remota):

- Servidor postgress (puerto 5433)
- Servidor anaconda y jupyter-notebook (puerto 8888)
- Servidor de Rstudio (puerto 8787)
- Antiguamente tenia un servidor de torrents (deluged) ya lo quite

Y planeo poner:

- Nextcloud (servidor de archivos tipo dropbox)
- InfluxDB (base de datos para datos temporales como series de tiempo y monitoreos)
- etc...

Algo asi seria en un diagrama lo que tengo en casa

![](https://i.imgur.com/AOMn4eH.png)

### Consideraciones: 

- No todos los servicios de internet (Telmex, Izzi, y Axtel) son tan faciles de configurar, y si preguntas por telefono a los de soporte para ver si es posible hacer esto, lo mas probable es que no sepan, lo que te puedo decir es que al menos en Telmex funciona y por lo mismo no me he cambiado a otra compañía
- Ninguna compañia te va dar ip fija externa y eso es un pequeño problema, por lo mismo hay que ver la manera de hacer el seguimiento a la ip externa (noip duckdns o un comando para ir guardando la nueva ip cada x tiempo y guardarlo en un archivo)
  - He usado noip (un servicio de dns dinamicos) el cual te da un nombre como de subdominio para que apunte a tu ip, y solo hay que instalar un programita para irle diciendo al servicio que ip tenemos (Pero hay que estar renovando cada mes para la version free)
  - ![](https://i.imgur.com/HLpF9nT.png)
  - Otra opcion similar y free y open.source es duckdns , ahorita lo tengo y funciona bien y ya no tengo que andarme preocupando por renovar
  - ![](https://i.imgur.com/d1MHVO6.png)
- Uno puede pensar que hay un gasto excesivo de cpu por tener varios servicios a la vez corriendo, pero corriendo sobre containers docker he visto que se consume poco procesamiento y memoria y  por ende no he visto que suba mucho el recibo de luz



### Detalles Técnicos:

Todo lo que describí fue platicadito, pero en otra entrada pondre mas a detalle como realizar el paso de crear tuneles ssh y tomare screenshots para mostrar mejor como se hace.

### Referencias

- https://www.duckdns.org/
- https://www.noip.com/