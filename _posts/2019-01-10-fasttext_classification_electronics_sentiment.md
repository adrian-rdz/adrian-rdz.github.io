---
title: Fasttext para clasificacion de sentimientos en NLP (textos en español)
date: 2019-01-10
author: Adrián Rodríguez
tags: [python,bash,nlp,clasificación,text,sentiment-analysis]
excerpt: "En este post se ve el uso de fasttext aplicado a la clasificacion de textos en particular a clasfiicar sentimientos en textos de reseñas de articulos, y también se explora el uso conjunto  de bash y python y como mezclarlos"
mathjax: "true"
---

## Introducción 

Ya habia hablado un poco de fasttext, como habia dicho es un implementación de wordvectors pero que aprovecha las llamadas subword-representation caracteristicas para añadir mas información al modelo, para ver el post anterior aquí esta la liga [https://adrian-rdz.github.io/NLP_wordvectors_p2/](https://adrian-rdz.github.io/NLP_wordvectors_p2/)

Anteriormente habia usado la librería directamente en bash, pero en la misma pagina de github existe un wrapper para usarlo en python entonces quise aplicarlo a un conjunto de datos de los que tengo por ahi y el cual habia usado para otro post [https://adrian-rdz.github.io/Naive-Bayes-Seniment-Analysis/](https://adrian-rdz.github.io/Naive-Bayes-Seniment-Analysis/) , al igual que en ese post el objetivo sera clasificación del sentimientopero ahora usando las representaciones vectoriales, con la librería fasttext nos podemos permitir realizar aprendizaje supervisado y no supervisado, en el aprendizaje supervisado al igual que cuando lo use directamente en consola hay que armar un archivo el cual contenga una linea por documento, donde cada linea tiene las etiquetas con el formato \__label\__etiqueta, para este caso la etiqueta es **yes** y **no** para sentimiento positivo y negativo resepectivamente.

Algo interesante de la implementación de fasttext para clasificación es que permite realizar multilabel classification esto, es que una misma instancia o documento puede tener mas de una etiqueta asignada, tal como los tags en un post web, o en una imagen detectar la presencia de mas de un objeto etc..

## Código

Ahora si el código 

importamos las librerías necesarias

```python
import os
import numpy
import fastText
from __future__ import absolute_import
from __future__ import division
from __future__ import print_function
from __future__ import unicode_literals
```

Se crea una función para representar la salida formateada (tomada del código de ejemplo de fasttext)


```python
def print_results(N, p, r):
    print("N\t" + str(N))
    print("P@{}\t{:.3f}".format(1, p))
    print("R@{}\t{:.3f}".format(1, r))
```

Vemos en que directorio estamos posicionado con los comandos magicos de ipython


```python
!pwd
```

    /home/adrianrdzv/Escritorio/VACACIONES_DIC2018/NLP

Vemos los directorios disponibles

```python
ls SFU_spanish_reviews_train_test/train/
```

    no  yes

Conatamos el numero de archivos disponibles de etiqueta positiva

```python
!ls SFU_spanish_reviews_train_test/train/yes/ | wc -l
```

    160

Conatamos el numero de archivos disponibles de etiqueta negativa

```python
!ls SFU_spanish_reviews_train_test/train/no/ | wc -l
```

    160

Creamos unas variables para guardar los paths postivo y negativo

```python
path_yes="/home/adrianrdzv/Escritorio/VACACIONES_DIC2018/NLP/SFU_spanish_reviews_train_test/train/yes/"
```


```python
path_no="/home/adrianrdzv/Escritorio/VACACIONES_DIC2018/NLP/SFU_spanish_reviews_train_test/train/no/"
```

Vemos igualmente ahora con python el numero de positivos y negativos


```python
print(len(os.listdir(path_yes))) # numero de archivos yes
print(len(os.listdir(path_no))) # numero de archivos no
```

    160
    160

A partir de aquí voy hacer algo que quizas sea algo arcaico dado que voy a mezclar código python con bash, dado que realizare unos preprocesos y pues como no soy experto en bash y no tengo a la mano ciertos comandos de python mezclare ambos lenguajes para lograr mi objetivo.

He aqui un ejemplo de mezclar python con bash (Se puede ver que la ultima linea del siguiente bloque de código es un comando de bash y para hacer que funcione en jupyter-notebook o en ipython es necesario anteponer un signo de admiracion )

```python
for i in os.listdir(path_yes):
    archivo_temp = path_yes+i
    !cat $archivo_temp | wc -l
```

    6
    97
    32
    23
    ....


Dado que mis archivos estan en un encoding diferente a UTF-8 (ISO-8859-1) hare un preproceso mezclando python y bash, es algo medio sucio el código pero resuelve el problema


```python
## archivo con encoding raro
!cat $archivo_temp
```

No se muestra la salida, pero al verlo sin cambiar el encoding se ven caracteres extraños

Otro ejemplo de mezclar python con bash es guardar la salida de un comando bash en una variable python como el código que sigue:

```python
#Guardamos la salida de un comando bash en una variable de python
archivo_temp_salida =!echo $archivo_temp
```

Lo que se hara para convertir a utf8 los documentos de texto sera crera 2 carpetas donde se almancene un copia en ese formato destino **no_utf8** y **yes_utf8**


```python
path_yes_new = path_yes+".."
!ls $path_yes_new
```

    no  no_utf8  yes  yes_utf8

Para convertir el encoding usare el comando o utileria iconv que se puede llamar desde bash

código de ejemplo:

```python
# hay una utileria en bash para convertir entre encodings la usare
##aqui convierto un archivo de ISO a utf-8
!iconv -f ISO-8859-1 -t UTF-8 $archivo_temp
```


Cremos variables python que apunten a los destinos donde residiran los documentos convertidos a formato utf8


```python
path_yes_new = "/home/adrianrdzv/Escritorio/VACACIONES_DIC2018/NLP/SFU_spanish_reviews_train_test/train/yes_utf8/"
path_no_new = "/home/adrianrdzv/Escritorio/VACACIONES_DIC2018/NLP/SFU_spanish_reviews_train_test/train/no_utf8/"
```

Creamos las carpetas


```python
#Creo las 2 carpetas destino
%mkdir $path_yes_new
%mkdir $path_no_new
```

Si ya las tienes creadas te mostrara error claramente:

    mkdir: no se puede crear el directorio Â«/home/adrianrdzv/Escritorio/VACACIONES_DIC2018/NLP/SFU_spanish_reviews_train_test/train/yes_utf8/Â»: El archivo ya existe
    mkdir: no se puede crear el directorio Â«/home/adrianrdzv/Escritorio/VACACIONES_DIC2018/NLP/SFU_spanish_reviews_train_test/train/no_utf8/Â»: El archivo ya existe


Ahora lo mismo para  las carpetas de Test


```python
path_yes_test = "/home/adrianrdzv/Escritorio/VACACIONES_DIC2018/NLP/SFU_spanish_reviews_train_test/test/yes/"
path_no_test = "/home/adrianrdzv/Escritorio/VACACIONES_DIC2018/NLP/SFU_spanish_reviews_train_test/test/no/"
```


```python
path_yes_new_test = "/home/adrianrdzv/Escritorio/VACACIONES_DIC2018/NLP/SFU_spanish_reviews_train_test/test/yes_utf8/"
path_no_new_test = "/home/adrianrdzv/Escritorio/VACACIONES_DIC2018/NLP/SFU_spanish_reviews_train_test/test/no_utf8/"
```


```python
#Creo las 2 carpetas destino
%mkdir $path_yes_new_test
%mkdir $path_no_new_test
```

### códigos para convertir a utf8

Convertir a utf8 toda la carpeta yes para Train


```python
#Mezclando python con bash
for i in os.listdir(path_yes):
    archivo_temp = path_yes+i
    archivo_destino = path_yes_new+i
    !iconv -f ISO-8859-1 -t UTF-8 $archivo_temp > $archivo_destino
```

Convertirt a utf8 toda la carpeta no


```python
#Mezclando python con bash
for i in os.listdir(path_no):
    archivo_temp = path_no+i
    archivo_destino = path_no_new+i
    !iconv -f ISO-8859-1 -t UTF-8 $archivo_temp > $archivo_destino
```

Convertir a utf8 toda la carpeta yes para Test


```python
#Mezclando python con bash
for i in os.listdir(path_yes_test):
    archivo_temp = path_yes_test+i
    archivo_destino = path_yes_new_test+i
    !iconv -f ISO-8859-1 -t UTF-8 $archivo_temp > $archivo_destino
```


```python
#Mezclando python con bash
for i in os.listdir(path_no_test):
    archivo_temp = path_no_test+i
    archivo_destino = path_no_new_test+i
    !iconv -f ISO-8859-1 -t UTF-8 $archivo_temp > $archivo_destino
```

Ya habiendo terminado el preproceso usando iconv para cambiar el formato a los archivos, ahora procedemos a dejar en el formato que lo espera fasttext

### Crear archivos con documentos etiquetado con el formato que espera fasttext

Crear un solo archivo etiquetado para train


```python
lista_yes = [] 
for i in os.listdir(path_yes_new):
    file = open(path_yes_new+i,"r")
    texto = file.read()
    lista_yes.append(texto)
    
lista_no = [] 
for i in os.listdir(path_no_new):
    file = open(path_no_new+i,"r")
    texto = file.read()
    lista_no.append(texto)
```

Crear un solo archivo etiquetado para test


```python
lista_yes_test = [] 
for i in os.listdir(path_yes_new_test):
    file = open(path_yes_new_test+i,"r")
    texto = file.read()
    lista_yes_test.append(texto)
    
lista_no_test = [] 
for i in os.listdir(path_no_new_test):
    file = open(path_no_new_test+i,"r")
    texto = file.read()
    lista_no_test.append(texto)
```


```python
lista_yes[0][0:20]
```


    'Vamos a ser sinceros'


```python
lista_no[0][0:20]
```


    'Hola voy a hablaros '


```python
lista_yes_test[0][0:20]
```


    'Hace unos meses se n'



El formato que soliocitar fasttext para hacer clasificación es una linea por texto o documento y que se tenga el marcado \__label__clase donde clase es la etiqueta en este caso sera yes y no


```python
with open(path_yes+"../"+"etiquetado_train.txt","w") as file:
    for i in lista_yes:
        file.write("__label__yes "+i.replace("\n"," ")+"\n")
```


```python
with open(path_yes+"../"+"etiquetado_train.txt","a") as file:
    for i in lista_no:
        file.write("__label__no "+i.replace("\n"," ")+"\n")
```

Ahora crear archivo etiquetado para **test**


```python
with open(path_yes+"../"+"etiquetado_test.txt","w") as file:
    for i in lista_yes_test:
        file.write("__label__yes "+i.replace("\n"," ")+"\n")
```


```python
with open(path_yes+"../"+"etiquetado_test.txt","a") as file:
    for i in lista_no_test:
        file.write("__label__no "+i.replace("\n"," ")+"\n")
```

###  Entrenar modelo de clasificación con fasttext

Para entrenar el modelo supervisado con fasttext es algo similar a como se hace directo con la aplicación de consola, y los parametros mantienen los mismos nombres la única diferencia es que aquí se usan unos metodos wrapper de python.


```python
train_data = path_yes+"../"+"etiquetado_train.txt"
test_data = path_yes+"../"+"etiquetado_test.txt"
```


```python
    # train_supervised uses the same arguments and defaults as the fastText cli
    model = fastText.train_supervised(
        input=train_data, epoch=50, lr=1, wordNgrams=4, verbose=2, minCount=2,dim=200,neg=5,loss="hs"
    )
    print_results(*model.test(train_data))

```

### Accuracy en train

    N	320
    P@1	0.994
    R@1	0.994

### Accuracy en test

En train parece jalar o sobreajustar muy bien ahora en test, veamos que tal

```python
print_results(*model.test(test_data))
```

    N	80
    P@1	0.775
    R@1	0.775

**A diferencia de los modelos del post previo lo mas alto que se lograba era un 70% de accuracy en test, aqui el accuracy ronda entre 77 y 80 en varias corridas por lo cual si representa una mejora y ademas falta realizar mas tuneo de paraemtros, lo cual podría mejorar aun mas el accuracy en test**

###  Referencias
- https://fasttext.cc/docs/en/supervised-tutorial.html
- https://fasttext.cc/docs/en/options.html
- https://github.com/facebookresearch/fastText/blob/master/python/doc/examples/train_supervised.py
- https://stackoverflow.com/questions/147741/character-reading-from-file-in-python
- https://stackoverflow.com/questions/20263909/converting-ansi-to-utf-8-in-shell
- https://www.pythonforbeginners.com/files/reading-and-writing-files-in-python
