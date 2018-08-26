## Docker para crear ambientes para Ciencia de Datos / Machine Learning y Desarrollo SW en General	

Un problema que sufre cualquier persona que se dedique a algo como desarrollador, administrador de sistemas o cientifico de datos por mencionar algunos, es el problema de lograr tener un ambiente con las librerias y herramientas de software necesario para poder comenzar a trabajar y resolver un problema como tal

La tarea de lograr un ambiente de software listo para trabajar es muy demandante en tiempo, y a veces puedes frustrarte en el camino, o lograr solo parcialmente instalar lo necesario.

Una solución o  ayuda a este problema es usar Docker para partir de ambientes o imagenes preoconfiguradas o al menos con una configuracion base, ya sea que trabajes con R, python, tensorflow o pytorch, o que estes orientado a cuestiones de NLP, redes neuronales, o algun otro tema especializado dentro de Machine Learning o Inteligencia Aritificial con un simple googleo o una busqueda en docker hub, puedes encontrar imagenes con ambientes preinstalados para NLP o para Estadistica Geospacial, Ambientes de tensorflow etc etc

![https://i.imgur.com/E319T0p.png](https://i.imgur.com/E319T0p.png)

Si en tu caso te decantas mas por el lenguaje de programación R, podras sacarle provecho a las imagenes de docker preconfiguradas con R y Rstudio y Shiny entre otras librerias usando el proyecto (Rocker https://github.com/rocker-org/rocker).



![img](https://i.imgur.com/KxDSBHe.png)

En adelante describire el proceso para lograr un ambiente de R partiendo de una imagen Rocker (Verse) y despues agregare paquetes extras los cuales requiero para NLP (coreNLP cleanNLP etc), cabe mencionar que este paso ultimo hay mejores maneras de realizarlo pero implica igual hacerlo a mano al menos una vez o ser muy crack para lograr un dockerfile con todos los parametros y comandos necesarios para hacer postinstalaciones de librerias y programas dentro de la instancia docker.

### Proceso de Creacion de Ambiente para (NLP usando R)

1. Requisitos 

   1. Instalar docker (esto solo sera una vez pero es necesario claramente)

2. Obtener una imagen de Docker con algunas herramientas de R preinstaladas

   1. Revisar en dockerhub la imagen que mas se ajuste a tus necesdidades (en mi caso uso ggplot dplyr asi que bajare una con esas librerias preinstaladas)

   2. hacer un docker pull en la consola de comndos 

      ```bash
      docker pull rocker/verse
      ```

   3. crear una instancia  a partir de esta imagen de docker

      ```bash
      docker run -d -p 8787:8787 --name verseNLP -v /home/adrianrdzv/dockerNLP/:/home/rstudio -e PASSWORD=aquivaelpass rocker/verse
      ```

      quizas sea algo intimidante el comando, pero por lo general en las ayudas de dockerhub de cada proyecto explican este paso, rapidamente el comando explica que puerto va exponer, explica  que usara de volumen una carpeta local,  y define un password para el usuario "rstudio", el puerto es porque para acceder al rstudio se hace via web localhost:8787 

      En este momento ya tenemos una instancia con R  Rstudio y librerias de tidyverse, falta agregar las librerias de NLP y configurar el ambiente

   4. Modificar librerias e instalar programas en la instancia de docker

      1. Para acceder a la instancia que queda corriendo despues del comando anterior basta con el siguiente comando para acceder a la terminal 

         ```bash
         docker exec verseNLP /bin/bash
         ```

         para poder instalar la libreria coreNLP y cleanNLP se requiere tener java8 , este paso es a nivel SO, asi que debemos hacerlo desde la consola

         ```bash
         apt-get update
         apt-gete install locate
         updatedb
         locate jdk | grep jvm | grep so
         checar que exista libjvm.so
         revisar que LD_LIBRARY_PATH tenga agregado el path de java
         ```

      2. Despues de ver que si se tenia preinstaldo java > 8, hay que instalar las librerias este paso se puede hacer desde la terminal de R o desde Rstudio, para hacerlo desde Rstudio solo entrar a localhost;8787 y agregar los siguiientes comandos:

         ```R
         #Entrar en R y revisar LD_PRELOAD (http://localhost:8787)
         #dentro de R revisar Sys.getenv( ) si #aparece en LD_LIBRARY_PATH el libjvm.so ya procedemos a instalar 
         
         install.packages('devtools')
         devtools::install_github("statsmaths/coreNLP")
         
         ```

         Este paso esta simplificado para no llenar de codigo , pero falta descargar la libreria coreNLP de standford igualmente se puede hacer desde R

      3. Una vez terminado la instalacion lo que se tiene modificado es solo la instancia que esta corriendo, si quieremos preservar los cambios en la imagen hay que hacer un commit 

         ```bash
         #docker commit instancia imagen
         docker commit verseNLP versenlp_image
         ```

A simple vista parece ser mas desmadroso que instalar directo en tu maquina pero hay que notar ciertas 

#### Ventajas:

1. portabilidad, escalabilidad, reproducibilidad

2. No tenemos que hacer chocar librerias o dependencias con nuestra maquina host

3. Si una instancia llega a un estado que no te agrado como funciona, puedes crear una nueva instancia desde la imagen que hiciste commit, la cual ya tendra todo lo previamente instalado.

4. Puedes crear ramas de imagenes unas con algun paquete o libreria que es algo conflictiva, esto en el caso de que se hiciera con maquinas virtuales no seria nada bueno ya que el tamaño en disco de las maquinas virtuales es muy superior a las instancias docker, dado que las instancias docker solo generan espacio cuando se hacen modificaciones de instalacion de librerias o programas (pero es solo el diferencial entre lo nuevo y la imagen lo que va generando espacio) ademas los datos usando como volumen nuestra maquina host, pues quedan por fuera de la instancia, esto es muy conveniente, porque puedes compartir datos con instancias variadas

5. puedes tener imagenes e instancias dedicadas para temas especificos y algunas ya estan casi completamente configuradas en dockerhub, por ejemplo para geospatial y ambientes con Rcpp con shiny.

   ![https://i.imgur.com/6hkxH1v.png](https://i.imgur.com/6hkxH1v.png)

#### Desventajas:

1. El tema de la seguridad es algo que hay que tener en mente, dado que exponemos puertos, y dado que las instancias docker contiene versiones de sistema operativo muy minimizadas, podria pensarse que carecen de ciertas librerias o medidas de seguridad, para esto hay guias, pero minimo hay que tener sentido comun y usar buenos passwords, y si vas a e exponer puertos no exponenrlos a internet haciendo portforwarding, y las cosas basicas.

#### Notas

Hay muchas cosas que se pueden hacer con Docker crear clusters de instancias (swarm, kubernetes) administrar redes en instancias, hay guis o gestores de instancias como rancher o portainer, existe un amplia variedad de subheramientas que pueden mejorar y acelerar el deploy de ambientes basta con ponerse a ver guias y tutoriales para mejorar el proceso de generar ambientes, en las referencias dejo una guia.

## Referencias

[https://github.com/rocker-org/rocker](https://github.com/rocker-org/rocker)

[https://hub.docker.com/](https://hub.docker.com/)

[https://hub.docker.com/r/rocker/verse/](https://hub.docker.com/r/rocker/verse/)

[https://journal.r-project.org/archive/2017/RJ-2017-065/RJ-2017-065.pdf](https://journal.r-project.org/archive/2017/RJ-2017-065/RJ-2017-065.pdf)

[https://docker-curriculum.com/](https://docker-curriculum.com/)