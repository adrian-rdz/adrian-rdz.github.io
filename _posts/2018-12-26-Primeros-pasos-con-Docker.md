---
title: Primeros pasos con Docker
date: 2018-12-26
author: Adrián Rodríguez
---

## Docker instalación

Los pasos recomendados para instalar la community edition de docker son los siguientes para ubuntu linux, (para derivados ubuntu es casi igual)
Actualizar los repos

```
sudo apt-get update
```

Obtener las dependencias

```
sudo apt-get install \
    apt-transport-https \
    ca-certificates \
    curl \
    software-properties-common
```

Se agrega el repo oficial de docker

```
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -

    sudo apt-key fingerprint 0EBFCD88

    sudo add-apt-repository \
    "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
    $(lsb_release -cs) \
    stable"
```

Actualizar los repos e instalar

    sudo apt-get install docker-ce
    sudo apt-get update

Despues de instalar docker el paso mas sensato es agregar nuestro user al grupo docker para no tener que usar sudo:

    sudo groupadd docker
    sudo usermod -aG docker $USER
Y la prueba para ver que esta funcioando

```
tuzy-server@tuzy-lap:~$ docker run hello-world

Hello from Docker!
This message shows that your installation appears to be working correctly.
```

## Comandos utiles

Los comandos docker de uso comun son los siguientes

- #### docker pull imagen

  - Obtener una imagen del repositorio oficial de docker

- #### docker run 

  - Crear y correr un container en base a una imagen (si no se tiene la imagen realizara el pull si se le permite )

- #### docker start

  - Iniciar o levantar un container que ya se tiene creado 

- #### docker stop

  - Detener un container (para que no este ocupando cpu o memoria)

- #### docker image ls

  - Listar todas las imagenes docker que tenemos en nuestra maquina disponibles

- #### docker container ls

  - Listar todos los containers que tenemos creados

- #### docker rm containerid

  - Eliminar un container usando su nombre o id

- #### docker rmi

  - Eliminar una imagen docker que tengamos descargada (solo se podra eliminar si no tiene containers creados asociados)

- #### docker system df -v

  - Listar ciertas estadisticas como imaagenes el espacio y containers asociados, tambien volumnenes que se usan y mas

- #### docker exec container comando

  - Mandar executar un comando dentro de un container especificado que este activo

- #### docker comit container new_image

  - Hacer un commit de los cambios e instalaciones que hayamos hecho en un container hacia una nueva imagen docker

- #### docker inspect containerid

  - Inspeccionar toda la configuración de un container que tengamos (que ip tiene asignada a que red esta conectado, que volumenes tiene asignados, etc...)

Para comenzar a trabajar en docker ocupas tener imagenes para de ellas generar containers, y algo que me agrada de docker es que acorta el tiempo de conseguir tener un container corriendo, dado que proporciona imagenes para muchos casos de uso; las cuales puedes conseguir en [dockerhub](https://hub.docker.com/) 

Algunos containers que puedes correr en cuestion de minutos y corriendo solo el docker pull es lo siguiente

- container con ubuntu, debian centos u otra distro
- servidores web como apache o nginx
- container de mysql, postgress, y muchas otras opciones de Base de datos
- containers con Nextcloud, servidor de torrents
- Containers el ecosistema anaconda y jupyter-notebooks y muchas librerias de python preinstaladas
- Containers de R, Rstudio, y muchas imagenes con librerias especializadas para ciertas tareas Estadisticas
- Muchas otras cosas que te llevaria configurar a mano un buen de tiempo

## Ejemplos de comandos en uso

Para iniciar la creacion de un container a partir de una de estas imagenes oficiales basta con hacer

```
#obtener la imagen de rstudio
docker pull rocker/rstudio
```

Crear un container en base a la imagen

```
docker run -d -p 8787:8787 --name verseNLP -v $PWD/dockerrstudio/:/home/rstudio -e PASSWORD=mipassword rocker/rstudio
```

quizas se ve algo feo el comando pero leyendo la documentacion lo puedes diseccionar, el parametro -p es para hacer un port forwarding y decir que expondremos cierto puerto aqui dice que el puerto 8787 del host se rediriijara al 8787 del container

el tag -v es para decirle que se asigne un volumen en este caso se hace un binding y esto quiere decir que un directorio de la maquina host sera visible en el container, esto es muy conveniente dado que asi podremos persistir nuestra informacion que genermos (segun la documentación el hacer bindings solo es recomendable en un ambiente de desarrollo y lo ideal para producción es usar los named-volumes y realizar respaldos)

el tag --name es para que se asigne un nombre al container que se creara asi sera mas facil identificarlo si no se pone se genera en automatico un id nada facil de recordar

```bash
tuzy-server@tuzy-lap:~$ docker image ls
REPOSITORY                       TAG                 IMAGE ID            CREATED             SIZE
rocker/geospatial                latest              53d59caacc8e        3 weeks ago         3.53GB
nlpdb_complete                   latest              bc8b6b7b0068        4 weeks ago         331MB
anaconda_nlp_complete            latest              77930ce0c14d        4 weeks ago         5.15GB
<none>                           <none>              f38470f43b15        2 months ago        4.19GB
<none>                           <none>              fcba639b0260        2 months ago        4.19GB
postgres                         latest              7a2907672aab        2 months ago        311MB
continuumio/anaconda3            latest              d5337c5206ca        2 months ago        3.6GB
joyzoursky/python-chromedriver   latest              3e19c9813e18        2 months ago        355MB
hello-world                      latest              4ab4c602aa5e        3 months ago        1.84kB
joyzoursky/python-chromedriver   3.6-xvfb-selenium   334da877cd59        11 months ago       1.12GB

```

```
tuzy-server@tuzy-lap:~$ docker container ls
CONTAINER ID        IMAGE                   COMMAND                  CREATED             STATUS              PORTS                    NAMES
17a1839dcfbc        rocker/geospatial       "/init"                  3 weeks ago         Up 3 weeks          0.0.0.0:8787->8787/tcp   rockergisnew
70ead3f0f88b        postgres                "docker-entrypoint.s…"   2 months ago        Up 6 weeks          0.0.0.0:5433->5432/tcp   nlpdb
f8b44c4c00dd        continuumio/anaconda3   "/usr/bin/tini -- /b…"   2 months ago        Up 2 months         0.0.0.0:8888->8888/tcp   anacondajup_new

```

Comandos que tengo en mi history y aqui muestro como ejemplo

```
# Parar una container llamado rockergisnew
docker stop rockergisnew

# eliminar un container llamado rockergisnew
docker rm rockergisnew

# Entrar el bash de un container 
docker exec -it anacondajup_new /bin/bash

# Inspeccionar un container (Util para ver volumenes y puertos expuestos)
docker inspect anacondajup_new | grep -i -A 5 - B 5 volume

# Correr un shell script dentro de un container
docker exec anacondajup_new sh /opt/notebook/scrapping_misc.sh
```

El comando que mas uso para monitorear es 

```
docker system df -v
```

![1545606443507](https://i.imgur.com/eOIUoGw.png)

## Referencias

- https://docs.docker.com/engine/reference/run/#general-form
- https://hub.docker.com/