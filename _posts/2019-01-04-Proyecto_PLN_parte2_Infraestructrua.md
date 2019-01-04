---
title: Proyecto PLN para datos de noticias de México (Parte 2) Infraestructura
date: 2019-01-04
author: Adrián Rodríguez
tags: [tecnico, linux, infraestructura, docker]
excerpt: "Descripción de la infraestructura usada para el proyecto y algunos pasos tecnicos que se llevaron acabo"
mathjax: "true"
---

## Introducción

Una parte importante de un proyecto es la infraestructura que lo va soportar, la infraestructura puede ser la diferencia entre un proyecto que se pueda escalar a uno que no. También la elección correcta de los componentes de software que compondran la infraestructura puede hacer mas o menos eficiente los procesos,

Ademas del objetivo de escalamiento, existen también el componente que esta presente en cualquier proyecto el componente del tiempo, dado el tiempo que se tenga disponible se debe elegir o ajustarse a una cierta infraestructura, para este proyecto de NLP se tuvo la restricción del tiempo, por lo cual en este momento la infraestructura trato de optimizarse hacia el modo de desarrollo rapido.

## Arquitectura

Dada la restricción en tiempo y la facilidad que brinda el uso de containers para acelerar la instalación de componentes y librerías necesarias para trabajar tareas de NLP y Ciencia de datos en general , se opto por hacer una infraestructura de containers docker.

Se instalo inicialmente en el server de Desarrollo, el cual es una laptop de gama media y se replico dicho ambiente a un servidor virtual (una maquina virtual que se nos proporciono en un server de alto rendimiento)

**Server  Desarrollo**

- Ubuntu Server 16.04.5
- 4 GB RAM
- Procesador Corei5 (4 hilos)

**Server  CIMAT**

- Linux Mint Sonya
- 16 Gigas de Ram
- 8 hilos (Procesadores Xeon

Dentro del servidor de desarrollo se instaló la versión de docker actual, y para este proyecto dado que se requiere realizar escrapeo, almacenamiento (DB) y análisis se ocuparon 4 contenedores (instancias docker)

**Contenedores Docker**

- Contenedor (**anacondajup new**)
  - Configuracion:
    - Este contenedor se basa en la imagen oficial de anaconda, la cual ya incluye librerı́as como
      matplotlib, numpy, scipy, entre otras, y ademas trae preinstalado jupyter-notebook
    -  Esta imagen se extendio usando un Dockerfile en el cual se mando instalar otras librerias necesarios
      pára realizar los procesos
  - Uso: Este contenedor es el que mas se usa para el proyecto dado que todos los integrantes pueden
    contectarse a el remotamento para acceder al jupyter-notebook y crear analisis con la inforamcion de la
    base de datos contenida en la instancia ”nlpdb”,
- Contenedor (**seleniumdocker**)
  - Configuracion: Esta imagen se extiende de una imagen encontrada en dockerhub, se dejo intacta dado
    que cumple con el proposito.
  - Uso: Esta imagen solo sirve para realizar escrapeo automatizado en páginas las cuales tienen contenido
    dinámico (Contenido que se genera al vuelo con javascript y AJAX); el cual no puede ser captado
    por los escrapeadores comunes como urllib o BeautifulSoap, por lo cual se usa “Selenium” el cual usa
    un navegador y emula un usuario al escrapear
- Contenedor (**nlpdb**) Este contenedor es una instancia de la imagen oficial de “postgress”, en este contenedor
  reside nuestra base de datos, para esta imagen se expone el puerto 5433 y se redirige al puerto 5432 el cual es el
  puerto oficial de postgress, Se realizó la configuración de un usuario para la base y se creó una tabla, la cual es la
  que tiene la estructura que necesitamos para almacenarlos datos
- Contenedor (**rockergisnew**)
  - Este contenedor es una instancia de la imagen oficial “rocker geospatial”, el cual es una versión optimi-zada de R con la paqueteria necesaria de GIS (rgdal,maptools,etc.....), ademas usa una versión optimizada de BLAS OpenBLAS lo cual acelera los cálculos matriciales en comparación con una instalación
    de R normal, y otra ventaja es que se puede accedera un Rstudio remotamente desde un navergador web
    usando el puerto 8787,

**Puertos Expuestos:** 

- 8787 (Rstudio)
- 8888 (jupyter-notebook)
- 5432 (postgress)
- 22 (ssh)
- 81 (En server de desarrollo este puerto se usa para ssh (Se usar portforwarding para redirigir al 22) y es accesible remotamente, en el server de CIMAT queda completamente aislado de internet y solo es posible accederlo desde esa RED)

**Servicios corriendo en server:**

- jupyter-notebook
- Rstudio
- base de datos postgress
- ssh
- docker
- crond (para la automtizacion de tareas)

![Imgur](https://i.imgur.com/Z9hmz9P.png)



### Diagrama de Arquitectura

Por obvias cuestiones de seguridad  se eligio no exponer directamente el server de desarrollo, se prefirio dejar solo expuesto el puerto de ssh (81), para realizar tuneles SSH hacia los servicios de los containers, el diagrama completo es como sigue:

![](https://i.imgur.com/AOMn4eH.png)

## Migracion de ambiente a otro servidor

**Hablar del uso de dockerfiles y de lo que implica replicar el ambiente a otro server**

1.-instalar docker
​    https://docs.docker.com/install/linux/docker-ce/ubuntu/#install-using-the-repository
​            

    sudo apt-get update
    sudo apt-get install \
    apt-transport-https \
    ca-certificates \
    curl \
    software-properties-common
    
    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
    
    sudo apt-key fingerprint 0EBFCD88
    
    sudo add-apt-repository \
    "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
    $(lsb_release -cs) \
    stable"
    
    ### $(lsb_release -cs) Esto podria no jalar en linux mint
    # se reemplazo por xenial que es la distribucion equivalente de ubuntu
    
    sudo apt-get update
    
    sudo apt-get install docker-ce
    
    ## probar
    sudo docker run hello-world
    
    #Despues de instalar docker hay que agregar nuestro user al grupo de docker para ya no tener que usar sudo
    sudo groupadd docker
    sudo usermod -aG docker $USER

2.-Pasos para configurar instancia seleniumdocker:
​    Correr el siguiente comando (automatico descaragara de hubdocker)

2.1Se dara de volumen el home de la maquina server

```
docker run --name seleniumdocker -it -v $(pwd):/usr/workspace joyzoursky/python-chromedriver:3.6-xvfb-selenium sh

```

​    
3.- Pasos para configurar instancia nlpdb
​    3.1Correr el siguiente comando para descargar la imagen e instalar el binding creara una carpeta en el home 

```
        docker run --name nlpdb -e POSTGRES_PASSWORD=pass -d -v $HOME/docker/volumes/postgres:/var/lib/postgresql/data -p 5433:5432  postgres
```



3.2 Generar export de la base de datos y copiar a una carpeta dentro del server

3.3 Con los datos del paso previo hacer el import 

entrar  a instancia nlpdb

```
docker exec -it nlpdb /bin/bash
```

Esto se hace desde el servidor en cimat:

```
root@cc7f6444529f:/# su - postgres
postgres@cc7f6444529f:~$ psql
psql (11.1 (Debian 11.1-1.pgdg90+1))
```


        CREATE USER kenny WITH SUPERUSER CREATEDB CREATEROLE PASSWORD 'pass';


        root@70ead3f0f88b:/# createdb -U kenny -T template0 newspapers_oem1
    
        root@70ead3f0f88b:/# psql -U kenny newspapers_oem1 < /var/lib/postgresql/data/export_db/newspapers_oem1_backup_all1.sql

4.- Pasos para configurar instancia anacondajup_new
​    4.1 Copiar docker file Dockerfile al server

    4.2 hacer build de imagen
    
    docker build -t anacondacustom .

con el siguiente comando 

​        
​        docker run -i -t --name anacondajup_new -p 8888:8888 -p 5000:5000 -v $(pwd):/opt/notebooks anacondacustom /bin/bash
​    
​    4.2 Encender o levantar jupyter-notebook dentro de la instancia

entrar a la instancia

​        docker exec -it anacondajup_new /bin/bash

correr el siguiente comando

​        nohup jupyter-notebook --ip=0.0.0.0 --port=8888 --allow-root &

setear PASSWORD

​        /opt/conda/bin/jupyter-notebook password 
​        teclear "pass


    4.2
        Copiar al home todos los archivos y notebooks del home del server se puede hacer por sftp
        sftp -p 81 tuzy-server@redlocaltor.ddns.net
            get *.ipynb 
            get *.py 
            get *.txt
            get *.sh
            
    4.3
        Modificar archivos .sh rutas absolutas a que empate con las rutas nuevas o usar $(pwd)
    
    4.4
        Crear entradas en cron
            42 10 * * * /usr/bin/docker exec anacondajup_new sh /opt/notebook/scrapping_misc.sh
            30 11 * * * sh /home/tuzy-server/script_oem_urls.sh
            45 11 * * * sh /home/tuzy-server/script_oem.sh
    
    4.5 Instalar instancia de rocker GIS
        ## Nueva instancia con R con librerias gis, animation y dplyr instalado
        docker run -d -p 8787:8787 --name rockergis -v /home/equipo1/Rfolder:/opt/foo -e PASSWORD=pass rocker/geospatial
​    

    sudo apt-get install \
    apt-transport-https \
    ca-certificates \
    curl \
    software-properties-common
    
    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
    
    sudo apt-key fingerprint 0EBFCD88
    
    sudo add-apt-repository \
    "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
    $(lsb_release -cs) \
    stable"
    
    ### $(lsb_release -cs) Esto podria no jalar en linux mint
    # se reemplazo por xenial que es la distribucion equivalente de ubuntu
    
    sudo apt-get update
    
    sudo apt-get install docker-ce
    
    ## probar
    sudo docker run hello-world
    
    #Despues de instalar docker hay que agregar nuestro user al grupo de docker para ya no tener que usar sudo
    sudo groupadd docker
    sudo usermod -aG docker $USER

2.-Pasos para configurar instancia seleniumdocker:
​    Correr el siguiente comando (automatico descargara de dockerhub)

2.1Se dara de volumen el home de la maquina server

​    

```
docker run --name seleniumdocker -it -v $(pwd):/usr/workspace joyzoursky/python-chromedriver:3.6-xvfb-selenium sh

```

​    
​    
3.- Pasos para configurar instancia nlpdb
​    3.1Correr el siguiente comando para descargar la imagen e instalar el binding creara una carpeta en el home 

```
        docker run --name nlpdb -e POSTGRES_PASSWORD=pass -d -v $HOME/docker/volumes/postgres:/var/lib/postgresql/data -p 5433:5432  postgres
```



3.2 Generar export de la base de datos y copiar a una carpeta dentro del server

3.3 Con los datos del paso previo hacer el import 

entrar  a instancia nlpdb

```
docker exec -it nlpdb /bin/bash
```

Esto se hace desde el servidor en cimat:

```
root@cc7f6444529f:/# su - postgres
postgres@cc7f6444529f:~$ psql
psql (11.1 (Debian 11.1-1.pgdg90+1))
```


        CREATE USER kenny WITH SUPERUSER CREATEDB CREATEROLE PASSWORD 'pass';


        root@70ead3f0f88b:/# createdb -U kenny -T template0 newspapers_oem1
    
        root@70ead3f0f88b:/# psql -U kenny newspapers_oem1 < /var/lib/postgresql/data/export_db/newspapers_oem1_backup_all1.sql

4.- Pasos para configurar instancia anacondajup_new
​    4.1 Copiar docker file Dockerfile a server

​    4.2 hacer build de imagen

    docker build -t anacondacustom .

con el siguiente comando 

​        
​        docker run -i -t --name anacondajup_new -p 8888:8888 -p 5000:5000 -v $(pwd):/opt/notebooks anacondacustom /bin/bash
​    
​    4.2 Encender o levantar jupyter-notebook dentro de la instancia
​    #entrar a la instancia
​        docker exec -it anacondajup_new /bin/bash
​    #correr el siguiente comando
​        nohup jupyter-notebook --ip=0.0.0.0 --port=8888 --allow-root &
​    #setear PASSWORD
​        /opt/conda/bin/jupyter-notebook password 
​        teclear "pasword"


    4.2
        Copiar al home todos los archivos y notebooks del home del server se puede hacer por sftp
        sftp -p 81 tuzy-server@redlocaltor.ddns.net
            get *.ipynb 
            get *.py 
            get *.txt
            get *.sh
            
    4.3
        Modificar archivos .sh rutas absolutas a que empate con las rutas nuevas o usar $(pwd)
    
    4.4
        Crear entradas en cron para automatizar scripts
            42 10 * * * /usr/bin/docker exec anacondajup_new sh /opt/notebook/scrapping_misc.sh
            30 11 * * * sh /home/tuzy-server/script_oem_urls.sh
            45 11 * * * sh /home/tuzy-server/script_oem.sh
    
    4.5 Instalar instancia de rocker GIS
        ## Nueva instancia con R con librerias gis, animation y dplyr instalado
        docker run -d -p 8787:8787 --name rockergis -v /home/equipo1/Rfolder:/opt/foo -e PASSWORD=pass rocker/geospatial

### Dockerfile para imagen personalizada de Anaconda

Ya habia hablado en un post de docker acerca de lo que es un dockerfile ([Parte_2_Docker_Usando_Dockerfiles](https://adrian-rdz.github.io/Parte_2_Docker_Usando_Dockerfiles/)) para este proyecto se creo un dockerfile para que la imagen de la cual parta la creacion del container ya cuente con las librerias necesarias para el proyecto nuevamente aquí pongo el contenido del dockerfile y hago notar nuevamente que parto de una imagen oficial de anaconda

```dockerfile
FROM continuumio/anaconda3:latest

ENV TZ=America/Monterrey
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

# librerias necesarias
RUN conda install --yes beautifulsoup4
RUN /opt/conda/bin/conda install --yes spacy && \
    conda install --yes -c plotly plotly && \
    conda install --yes -c conda-forge psycopg2 && \
    pip install pandas_ml && \
    python -m spacy download en && \
    python -m spacy download es && \
    python -m spacy download es_core_news_sm && \
    python -m spacy download es_core_news_md && \
    pip install --upgrade gensim && \
    pip install joblib && \
    pip install pyldavis


## se debe modificar password en instancia
#RUN /opt/conda/bin/jupyter-notebook password=


ENTRYPOINT [ "/usr/bin/tini", "--" ]
CMD [ "/bin/bash" ]
```

Una vez realizada la migracion se propone estrcturar mejor los arhcivos de programas, análisis, códigos, logs, modelos, etc...

**Nota**: Claramente los passwords en las configuraciones mostradas no son **pass** se cambio esas lineas al crear el post

## Cambios futuros

Para realizar una infraestructura y un desarrollo nivel producción es requerido manejar errorres de una manera que sea tolerante a ello o pueda reinicializarse en automatico y para el caso de infraestructura, se debiese tener al menos 2 maquinas formando un cluster de alta disponiblidad, arquitecturas a nivel producción se puede lograr haciendo uso de kubernetes, en un  futuro se planea pasar el proyecto a un infraestructura tomando en cuenta los puntos mencionados.



## Referencias

- https://docs.docker.com/
- https://unix.stackexchange.com/
- https://kubernetes.io/