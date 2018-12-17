---
title: "Expresiones regulares para analizar textos"
date: 2018-12-16
tags: [NLP,Regular Expressions,R,python]
excerpt: "Expresiones regulares para analizar textos"
mathjax: "true"
---

## Expresiones Regulares para anlizar textos

El analisis de expresiones regulares se remonta tiempo atras, si eres alguien de Ciencias de la Computación o Ingeniería en Sistemas lo mas probable es que hayas visto este tema, en la clase Teoria de Automatas y Lenguajes Formales (Si eres desarrollador web es probable que lo uses muy comunmente para validar strings), y existe una definición formal de como es posible generar lenguajes usando expresiones regulares;

Las expresiones regulares son un algebra para poder representar los patrones que pueden describir un automata

Las expresiones regulares se puede convertir a automatas y un automata se puede convertir a una expresion regular (Ejercicio que normalmente se pide en un examén a alguien de Ciencias de la Computación o para una tarea)

http://infolab.stanford.edu/~ullman/focs/ch10.pdf   (en este enlace se puede ver mas de la importante teoría)

Una de las primeras formas de manejar textos que se viene a la mente es hacer proceso basados en reglas (Rule-based), por ejemplo el caso del POS tagging es posible hacerlo algo mas robusto agregando reglas dependientes del lenguaje, el problema de este enfoque es que puede llegar a hacer mucho esfuerzo para lograr un poco mayor de precision del que un metodo desatendido y probabilistico llega a tener.

Las expresiones regulares siguen usandose muy extensamente como preproceso en NLP  y en otras tareas de programación y ademas tienen caracter historico

A continuación muestro algunas de las cosas que se pueden hacer con expresiones regulares usando el comando grep en la consola:

**Encontrar cadenas  fijas en textos**

Texto que se estara analizando

```bash
:~/Descargas$ cat scrap.txt 
Diario de Xalapa

Por los delitos de robo y secuestro, fincan responsabilidad a dos personas

2 de octubre de 2018

Los datos de prueba aportados por el agente del Ministerio Público de la Procuraduría General de la República (PGR) permitieron obtener auto de vinculación a proceso en contra de dos personas, por su probable responsabilidad en los delitos de robo a transporte público federal y secuestro, en la modalidad de secuestro exprés. La delegación de la PGR en Veracruz informó que policías federales atendieron la denuncia de un particular e implementaron un operativo en la carretera 150D Puebla–Córdoba, en el municipio de Córdoba, donde aseguraron un tracto-camión acoplado a un semirremolque, que transportaba 3 mil 363 cajas con cosméticos, mismo que contaba con reporte de robo, detuvieron a dos personas. 


 El fiscal de la federación solicitó audiencia ante el Juez de Control, quien calificó de legal la detención, dictó auto de vinculación a proceso y estableció como medida cautelar prisión preventiva oficiosa, por lo que los hoy imputados fueron recluidos en el Centro Federal de Readaptación Social 5 Oriente, en Villa Aldama, Veracruz. El juzgador fijó un plazo de tres meses para el cierre de la investigación complementaria.
 


```

Buscar linea que contiene la palabra fiscal

```
:~/Descargas$ cat scrap.txt | grep "fiscal"
 El fiscal de la federación solicitó audiencia ante el Juez de Control, quien calificó de legal la detención, dictó auto de vinculación a proceso y estableció como medida cautelar prisión preventiva oficiosa, por lo que los hoy imputados fueron recluidos en el Centro Federal de Readaptación Social 5 Oriente, en Villa Aldama, Veracruz. El juzgador fijó un plazo de tres meses para el cierre de la investigación complementaria.


```

Buscar lineas que contienen la palabra octubre

```
:~/Descargas$ cat scrap.txt | grep "octubre"
2 de octubre de 2018


```

**Encontrar patrones en textos**

Para extraer numeros

cat scrap.txt | grep "\b[0-9]*\b"

![1544988187506](https://i.imgur.com/eMlsFeY.png)

Encontrar palabras pegadas a  signos de puntuación

![1544988150597](https://i.imgur.com/HbHK2pX.png)

Encontrar palabras con un separador

cat scrap.txt | grep "\b[a-zA-Z]*[^0-9a-zA-Z ][a-zA-Z]*\b"

![1544988322206](https://i.imgur.com/ZmfKjT0.png)

Extraer fechas textuales con cierto patrón

```
~/Descargas$ cat scrap.txt | grep -E "[0-9]* de \b[a-zA-Z]*\b de [0-9]*"
2 de octubre de 2018
```

**Algunos caracteres (operadores) especiales y para que sirven**

El operador * - sirve para denotar que el token previo se puede repetir 0 o mas veces

El operadoor + - sirve para denotar que el token previo se puede repetir 1 o mas veces

El operador . denota cualquier caracter a excepcion de \n

El operador $ puede denotar que la cadena que haga match al final de la linea

El operador ^ puede denotar varias cosas en un rango [0-9] niega el rango, su otro uso es como el $ pero para denotar que comienza la linea o cadena

Existen los parentesis cuando  se quiere usar los primeros 2 operadores sobre tokens mas grandes que una letra por ejempo (ha)* puede capturar hahahaha cuando ha* capturaria haaaaa, tambien puede servir para referenciar una expresion regular con \1 o \2 segun el numero que le corresponda, esto puede llegar a ser muy util para expresiones regulares mas complejas

Existen mas operadores estos son solo algunos y tambien existen varias vertientes de las expresiones regulares que varian, por lo cual es bueno revisar la documentacion de cada implementación pero por lo general los previos comentados son estandares, el uso de parentesis y operadores como \| (or) con grep se ocupa el tag -E que denota expresiones regulares extendidas.

### Como se puede aplicar a NLP

Lo visto previamente puede servir para la parte de preproceso en NLP, a veces tenemos textos que vienen con puntuaciones pegadas a palabras, palabras que vienen con Mayuscula-minscula, caracteres pegados a digitos, correos,  y patrones que no aportan información para ciertos modelos como por ejemplo LDA en modelación de topicos, y con expresiones regulares puedes previo a entrenar tu modelo quitar todo eso que afectaria el modelo y asi lograr quizas un mejor modelo, también en el caso de clasificación quizas necesites preprocesar y quitar  stopwords o una deformación de las stopwords.

A diferencia de usar grep en consola , en los lenguajes de programación podemos aplicar expresiones regulares sobre textos tokenizados (separados posiblemente por espacios y algunas otras variantes), por lo que podemos aplicar expresiones regulares sobre cada token y asi de esa manera filtrar los tokens de interes que cumplan el patron que deseamos preservar o analizar

### Expresiones Regulares con R usando **stringr**

https://stringr.tidyverse.org/articles/regular-expressions.html

stringr en conjunto con rvest puede ser util para escrapear sitios web

### Expresiones Regulares en python usando **re**

https://docs.python.org/3/library/re.html

Despues en otro post pondre algunos ejemplos en python al realizar web-scrapping