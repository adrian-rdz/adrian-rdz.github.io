---
title: "Creación de imagen docker usando un Dockerfile y docker build"
date: 2018-12-21
tags: [Docker, tecnico, linux]
excerpt: "Crear imagenes docker usando archivo Dockerfile y comando docker build"
mathjax: "true"
---

## Introducción

En el caso mas ideal de que vayamos a trabajar con cierta herramienta o servidor el cual ya se encuentre una version oficial, con todo lo que ocupemos, basta con hacer un **docker pull image** de alguna de las imagenes oficiales de **Docker Hub** , pero por lo general no va pasar eso y ocupamos hacer cambios o agregar cosas extras a las imagenes docker para que nuestros container esten listos para que los usemos para nuestros fines.

![](https://i.imgur.com/3YU2W38.png)

Existen varias formas de crear ambientes con containers docker, una forma es partir de una imagen oficial e ir  haciendo cambios gradulamente y al finalizar hacer un docker commit imagen_nueva, otra es usar Dockerfile, y otra mas es usar varios Dockerfiles y luego un archivo yaml el cual desplegara clusters o ambientes con nuestra especificación

En este caso me enfocare al segundo caso que es usar un Dockerfile

## Dockerfile

Un archivo Dockerfile es un archivo de texto el cual describe como se va componer una imagen docker, con lo cual podemos extender imagenes oficiales y hacerle modificaciones

### Algunas sentencias posibles en Dockerfile

- FROM
  - Para decir de que imagen va partir nuestra nueva imagen (Algo asi como extender un clase en POO)
- EXPOSE
  - Exponer puertos (Asignacion de puertos para nuestra imagen)
- ENV
  - Setear variables de entorno
- RUN
  - Correr comandos de configuración comunmente usado para lanzar instalaciones de librerias y software que contendra nuestra imagen extendida
- CMD
  - Es un comando que docker ejectura por defailt cuando hagas un **docker run image** y crees un container nuevo (Se puede sobreescribir este comportmiento **docker run image new_command** )
- Existen mas opciones posibles en los Dockerfiles para mas detalle ve la documentacion oficial de docker

## Casos de uso

Puede ser que quieras tener un ambiente de R y que por lo general tu ya tengas tu lista de packages de uso comun, entonces puedes basarte en una imagen oficial y solo realizar las instalaciones con **RUN** en el Dockerfile y asi tener tu imagen personalizada

Igualmente para un ambiente python puede ser que ocupes anaconda jupyter-notebook, pero igiualmente tengas tu seleccion de librerias que usas comunmente, entonces solo es agregar  algunos **RUN pip libreria** en tu dockerfile para obtener tu imagen personalizada

Mostrare un ejemplo de Dockerfile el cual realice poco a poco y ya lo he usado 

## Ejemplo Dockerfile Anaconda (mas librerías que ocupo)

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

### Explicación de que hace cada comando del dockerfile

Del **FROM** podemos ver que estoy extendiendo de una imagen oficial de anaconda, despues la 2da y 3era linea ENV y RUN son para configurar la zona horaria, dado que por default no viene configurada y eso causa problemas al usar comandos de python que usen **time** 

Los 2 siguientes RUN son comandos para instalar librerias que ocupo en un proyecto :

- Beautifulsoap
- spacy
- modelos spacy
- gensim
- joblib
- pyldavis
- pandas_ml
- psycopg2
- plotly



## Como se genera una imagen apartir de un dockerfile

![](https://i.imgur.com/jYx6AsL.png) 

```
docker build -t anacondacustom .
```

El comando es **docker build -t** el nombre anacondacustom es como se llamara la imagen que creare y el **.** es solo para denotar que agarre el dockerfile del directorio en el que esto actualmente

Este comando puede llevar un largo tiempo de ejecuccion y es de esperar dado que esta descargando informacion de internet de librerias e instalandolas

al finalizar podremos localizar la nueva imagen viendo en **docker image ls** o en **docker system df -v**

y para crear un container en base a esa nueva imagen solo basta hacer **docker run miimagen parametros ..**

## Ejemplo mas avanzado que encontré en internet

En internet en dockerhub y en github, puedes encontrar ejemplos muy interesantes de Dockerfiles muy elaboarados, como por ejemplo el Dockerfile de la imagend docker de python que usan para los kernels de Kaggle

#### Ejemplo:  imagen docker python de kernel Kaggle

```dockerfile
FROM gcr.io/kaggle-images/python-tensorflow-whl:1.12.0-py36 as tensorflow_whl
FROM continuumio/anaconda3:5.2.0

ADD clean-layer.sh  /tmp/clean-layer.sh
ADD patches/ /tmp/patches/
ADD patches/nbconvert-extensions.tpl /opt/kaggle/nbconvert-extensions.tpl

# This is necessary for apt to access HTTPS sources
RUN apt-get update && \
    apt-get install apt-transport-https && \
    /tmp/clean-layer.sh

    # Use a fixed apt-get repo to stop intermittent failures due to flaky httpredir connections,
    # as described by Lionel Chan at http://stackoverflow.com/a/37426929/5881346
RUN sed -i "s/httpredir.debian.org/debian.uchicago.edu/" /etc/apt/sources.list && \
    apt-get update && apt-get install -y build-essential unzip && \
    # https://stackoverflow.com/a/46498173
    conda update -y conda && conda update -y python && \
    pip install --upgrade pip && \
    apt-get -y install cmake && \
    /tmp/clean-layer.sh

# Tensorflow doesn't support python 3.7 yet. See https://github.com/tensorflow/tensorflow/issues/20517
# Fix to install tf 1.10:: Downgrade python 3.7->3.6.6 and downgrade Pandas 0.23.3->0.23.2
RUN conda install -y python=3.6.6 && \
    pip install pandas==0.23.2 && \
    # Another fix for TF 1.10 https://github.com/tensorflow/tensorflow/issues/21518
    pip install keras_applications==1.0.4 --no-deps && \
    pip install keras_preprocessing==1.0.2 --no-deps && \
    /tmp/clean-layer.sh

```

El extracto del dockerfile es solo un subconjunto el completo son mas de 500 lineas de codigo de instalaciones y configuraciones se puede ver completo aqui

[https://github.com/Kaggle/docker-python/blob/master/Dockerfile](https://github.com/Kaggle/docker-python/blob/master/Dockerfile)

En este ejemplo se puede ver que usan la sentencia **ADD** que es para copiar datos al image a construir de la maquina fuente.

No tengo mucha experiencia en esto pero lo poco que se lo he aprendido viendo la documentación y los dockerfiles disponibles en internet y hace poco hice mi primer build y fnciono como esperaba; que es el que puse de ejemplo mas arriba.

Si tu objetivo es automatizar la creacion de un ambiente **stack** (Stack me refiero a un ambiente foramdo por varios containers que en conjunto formaran un ambiente completo para alguna tarea o proyecto) lo ideal es usar **Docker Compose** usando archivos de configuración **YAML**, con eso lo lograrías; jamas he hecho uno de cero, pero he manado generar stacks en base a archivos YAML con docker compose, y es muy conveniente y rapido de usar, en mi caso fue un ejemplo de un cluster de spark con un nodo maestro y 2 nodos workers.

Para obtener stacks mas avanazados para nivel producción en un ambiente de producción una de las soluciones es kubernetes.

## Referencias

- https://stackoverflow.com/questions/37461868/difference-between-run-and-cmd-in-a-docker-file
- https://docs.docker.com/engine/reference/builder/