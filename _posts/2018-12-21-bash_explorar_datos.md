---
title: "Bash y CLI para analizar datos columnares csv tsv txt etc"
date: 2018-12-21
tags: [general,estadistica,computacion]
excerpt: "bash,linux,comandos"
mathjax: "true"
---



En este post se exploran algunos comandos linux para explorar datos columnares como csv, tsv, txts, pero que igual pueden servir para archivos de texto como de NLP.

### pwd, ls, grep, cat


```python
!pwd
```




    '/home/adrianrdzv/Escritorio/VACACIONES_DIC2018/post_bash'




```python
!ls -lh
```

    total 4.9M
    -rw-rw-r-- 1 adrianrdzv adrianrdzv    6 dic 21 10:40 1.txt
    -rw-rw-r-- 1 adrianrdzv adrianrdzv    6 dic 21 10:40 2.txt
    -rw-rw-r-- 1 adrianrdzv adrianrdzv    6 dic 21 10:40 3.txt
    -rw-rw-rw- 1 adrianrdzv adrianrdzv 4.5M feb  8  2018 censo_nl.csv
    -rw-rw-r-- 1 adrianrdzv adrianrdzv   72 dic 21 10:52 Untitled.ipynb
    -rw-rw-r-- 1 adrianrdzv adrianrdzv 376K feb  8  2018 wine_quality.csv



```python
!ls | grep csv
```

    censo_nl.csv
    wine_quality.csv



```python
!cat 1.txt
```

    1
    2
    3



```python
!cat 2.txt
```

    4
    5
    6



```python
## Concatenar todos los archivos de extension .txt
!cat *.txt
```

    1
    2
    3
    4
    5
    6
    7
    8
    9


El comando previo es util dado que algunas veces se proporcionan archivos separados por categorias o por timestamps y se requiere unirlos para analizarlos en un solo bloque

### Revisar un subconjunto de la informacion

Antes de usar el comando cat hay que tener cuidado de que el archivo no sea muy grande por lo cual se podría recomendar tratar de evitar usar cat directamente para echar un primer vistazo

Una recomendación seria siempre usar para el primer vistazo head, tail, more o less

- head -- Lista los primeros n registros de un archivo (default n=10)
- tail -- Igual que el anterior pero los ultimos en vez de los primeros
- more -- es un comando para ir visualizando poco a poco aun archivo 
- less -- mejor que el anterior porque puedes regresar al principio (more solo te deja ir avanzando)

Ver los primeros 10 registros del archivo de los vinos


```python
!head wine_quality.csv
```

    wine,fixed acidity,volatile acidity,citric acid,residual sugar,chlorides,free sulfur dioxide,total sulfur dioxide,density,pH,sulphates,alcohol,quality
    red,7.4,0.7,0,1.9,0.076,11,34,0.9978,3.51,0.56,9.4,5
    red,7.8,0.88,0,2.6,0.098,25,67,0.9968,3.2,0.68,9.8,5
    red,7.8,0.76,0.04,2.3,0.092,15,54,0.997,3.26,0.65,9.8,5
    red,11.2,0.28,0.56,1.9,0.075,17,60,0.998,3.16,0.58,9.8,6
    red,7.4,0.7,0,1.9,0.076,11,34,0.9978,3.51,0.56,9.4,5
    red,7.4,0.66,0,1.8,0.075,13,40,0.9978,3.51,0.56,9.4,5
    red,7.9,0.6,0.06,1.6,0.069,15,59,0.9964,3.3,0.46,9.4,5
    red,7.3,0.65,0,1.2,0.065,15,21,0.9946,3.39,0.47,10,7
    red,7.8,0.58,0.02,2,0.073,9,18,0.9968,3.36,0.57,9.5,7


Ver los ultimos 10 registros del arcivo de vinos


```python
!tail wine_quality.csv
```

    white,6.8,0.22,0.36,1.2,0.052,38,127,0.9933,3.04,0.54,9.2,5
    white,4.9,0.235,0.27,11.75,0.03,34,118,0.9954,3.07,0.5,9.4,6
    white,6.1,0.34,0.29,2.2,0.036,25,100,0.98938,3.06,0.44,11.8,6
    white,5.7,0.21,0.32,0.9,0.038,38,121,0.99074,3.24,0.46,10.6,6
    white,6.5,0.23,0.38,1.3,0.032,29,112,0.99298,3.29,0.54,9.7,5
    white,6.2,0.21,0.29,1.6,0.039,24,92,0.99114,3.27,0.5,11.2,6
    white,6.6,0.32,0.36,8,0.047,57,168,0.9949,3.15,0.46,9.6,5
    white,6.5,0.24,0.19,1.2,0.041,30,111,0.99254,2.99,0.46,9.4,6
    white,5.5,0.29,0.3,1.1,0.022,20,110,0.98869,3.34,0.38,12.8,7
    white,6,0.21,0.38,0.8,0.02,22,98,0.98941,3.26,0.32,11.8,6



```python
more wine_quality.csv
```

Dado que estoy trabajando en jupyter notebooks no es muy util aqui less y more con el operador admiración dado que no tenemos la funcion como en la consola

## Comandos mas elaborados y usando tuberias (\|)

Usando el operador pipe se puede hace que la salida de un comando sirva como entrada para otro comando por ejemplo el mismo comando head puede usar la salida del comando cat


```python
!cat wine_quality.csv | head
```

    wine,fixed acidity,volatile acidity,citric acid,residual sugar,chlorides,free sulfur dioxide,total sulfur dioxide,density,pH,sulphates,alcohol,quality
    red,7.4,0.7,0,1.9,0.076,11,34,0.9978,3.51,0.56,9.4,5
    red,7.8,0.88,0,2.6,0.098,25,67,0.9968,3.2,0.68,9.8,5
    red,7.8,0.76,0.04,2.3,0.092,15,54,0.997,3.26,0.65,9.8,5
    red,11.2,0.28,0.56,1.9,0.075,17,60,0.998,3.16,0.58,9.8,6
    red,7.4,0.7,0,1.9,0.076,11,34,0.9978,3.51,0.56,9.4,5
    red,7.4,0.66,0,1.8,0.075,13,40,0.9978,3.51,0.56,9.4,5
    red,7.9,0.6,0.06,1.6,0.069,15,59,0.9964,3.3,0.46,9.4,5
    red,7.3,0.65,0,1.2,0.065,15,21,0.9946,3.39,0.47,10,7
    red,7.8,0.58,0.02,2,0.073,9,18,0.9968,3.36,0.57,9.5,7
    cat: error de escritura: Tubería rota


#### Revisar el numero de lineas que tiene un archivo

Se pasa los datos de la salida estandar al comando wc con el cual se puede contar lineas palabras etc...


```python
!cat wine_quality.csv | wc -l
```

    6498


Contar cuantos vinos blancos


```python
!cat wine_quality.csv | grep white | wc -l
```

    4898


Contar cuantos vinos rojos


```python
!cat wine_quality.csv |grep red | wc -l
```

    1599



```python
!cat censo_nl.csv | wc -l
```

    5266


#### Obtener solo ciertas columnas de un archivo csv u otro archivo que cuente con un separador bien definido


```python
!cat censo_nl.csv | cut -f 1,2,3,4 -d "," | column -t -s"," | head
```

    column: El carácter multibyte o extendido está incompleto o es inválido
    "CVEGEO"     "NOMBRE"             "POB1"  "POB2"
    "190010001"  "Abasolo"            1976    107
    "190020001"  "Agualeguas"         1995    80
    "190030001"  "Los Aldamas"        545     19
    "190040001"  "Ciudad de Allende"  26065   1408
    cut: error de escritura: Tubería rota



```python
!cat wine_quality.csv | cut -f 1,2,3,4 -d "," | column -t -s"," | head
```

    wine   fixed acidity  volatile acidity  citric acid
    red    7.4            0.7               0
    red    7.8            0.88              0
    red    7.8            0.76              0.04
    red    11.2           0.28              0.56
    red    7.4            0.7               0
    red    7.4            0.66              0
    red    7.9            0.6               0.06
    red    7.3            0.65              0
    red    7.8            0.58              0.02


En los 2 comandos previos se usa cuadruple tubería, lo que implica que la salida del primer comando la procesa el segundo comando y luego esa salida la procesa el terecero y asi sucesivamente,

En ese comando concatenado se usa otros 2 comandos nuevos cut y column

- cut -- Este comando recibe con que separador se cortara, y con el tag f podemos especificar las columnas que queremos
- column -- Este comando solo lo uso para que se formatee mas bonito ya que aquí lo que hace es cambiar la coma por un tab

#### Ver los valores unicos de una variable categorica


```python
 !cat wine_quality.csv | cut -f 1 -d "," | uniq
```

    wine
    red
    white


En el comando previo se usa el comando uniq el cual quita duplicados, por lo cual te puede ser util si tienes un archivo con valores duplicados.

###  Mas posibilidades

Es posible hacer cosas muy interesantes, aunque algunas cosas si ya es mejor hacerlas desde algun interprete de otro lenguaje.

Otras de las cosas que se puede utilizar es las expresiones regulares con grep y reemplazar textos en archivos usando sed, es comun que tenemos archivos que tienen algun caracter extraño y que al leerlos en python o R, truena la lectura o que causa problemas, para esos casos se puede usar el comando sed y reemplazar lo que causa problema

Con el comando grep, podemos buscar dentro de un archivo csv o un archivo de texto la ocurrencia de un cierto patron o expresión regular, esto puede ser muy util a manera de exploracion, o tambien si solo queremos trabajar con un subconjunto de la informacion que cumpla con cierto patron

### Mas CLI tools 

Ademas de los comandos bash se puede hacer muchas cosas a manera de Interface de Texto (TUI), por ejemplo para mi en veces es mas comodo entrar a ipython o R desde consola, y trabajar interactivamente desde ahi, dado que tiene varias ventajas, 

- ipython -- ipython notebook es la interfaz de consola de python igual que jupyter notebook pero desde consola, tiene la ventaja de que carga mas rapido para entrar, y a veces es mas comodo para trabajar si estas mas acostumbrados a la consola.

- R -- El binario de R lo puedes llamar desde bash y trabajar interactivamente desde la consola al igual que ipython la carga inicial es muy rapido, mas rapido que ipython al menos en mi maquina, y te evitas gastar como medio giga en memoria de usar Rstudio u otra IDE

Tanto en R como ipython desde consola puedes manadar generar graficos y verlos al tiempo que los llamas, solo para ipython hay que tomar en cuenta que se requiere usar el comando %%matplotlib para que puede llamarse interactivamente

Hay un libro que salio hace poco para las personas como a mi que les gusta trabajar en consola que ahonda mas en este tema de como trabajar desde la consola en el contexto de analizar datos, aun y que se enfoca un poco mas a R, también habla de caracteristicas que pueden servir desde bash o que no dependen de R

[www.datascienceatthecommandline.com](https://www.datascienceatthecommandline.com/)

tratare de irlo leyendo poco a poco, y lo mejor es que de libre acceso para leerlo en linea, ya si lo quieres fisico pues si hay que comprarlo claramente
