---
title: "POS tagging usando HMM y Viterbi Decoding"
date: 2018-12-15
tags: [NLP,HMM,R]
excerpt: "NLP - POS taggeo usando Modelo HMM y Viterbi Decoding para deducir tags de palabras"
mathjax: "true"
---

## Procesamiento de Lenguaje Natural (PLN o NLP en ingles)

NLP es una discinplina que mezcla varias ciencias y tecnicas Linguistica, Ciencias de la Computacion, Probabilidad y Estadistica etc.. en esta area existen varios problemas y cosas a modelar , a diferencia de cuando uno trabaja con datos tabulares y estructurados, analizar textos conlleva tener o desarrollar nuevas herramientas  y tecnicas para el analisis adecuado de este tipo de datos

Entre las tematicas y problemas que estudia NLP se encuentra:

* Preprocesamientos a textos como (Lematizacion, Stemming, StopWords, etc...)
* Expresiones Regulares y medidas de similaridad
* Information Extraction
* POS tagging (HMM)
* NER (Named Entitiy Relationships)
* Anaforas y Coreferencias
* Modelado de Topicos (LDA, LSA)
* Word2Vec, Phrase2Vec, Doc2Vec
* etc....

El campo de NLP es muy extenso hay muchos mas topicos de los descritos previamente, un buen libro para introducirse en estos temas es "Speech and Language Procesing, Daniel Jurafsky and James H. Martin" el cual se puede encontrar un draft casi completo en [https://web.stanford.edu/~jurafsky/slp3/ed3book.pdf](https://web.stanford.edu/~jurafsky/slp3/ed3book.pdf)

Volviendo al tema del articulo y a uno de los temas tratados por NLP, es POS tagging, 

#### POS tagging

Con POS tagging nos referimos a Part Of Speech Tagging, cuando analizamos una oracion sentencia o texto, podemos encontrar diferentes palabras las cuales en nuestros cursos basicos escolares las dividiamos en articulos, pronombres, sustantivos, verbos, entre muchas otras categorias mas especificas, la tarea del POS tagging es automatizar el proceso de asignarle la clasificacion correcta a cada palabra en un texto, este problema ya ha sido ampliamente estudiado y trabajado y se puede encontrar precisiones muy buenas en los trabajos State of the Art ya existentes ademas ya esta integrado esta funcion en librerias opensource como OpenNLP,CoreNLP, Spacy, y en otras librerias comerciales.

El POS tagging se puede modelar haciendo uso del modelo estadistico HMM (Hidden Markov Models) viendo que nuestros estados observables son las palabras en el texto, y nuestros estados ocultos son las taggs  (Pronombre verbo articulo, etc...)

El proceso resumido para entrenar y poder encontrar el POStaggeo de una cadena nueva de texto es el siguiente:

1. Leemos una cierta cantidad de textos de entrenamientio (donde ya se tiene pareado palabra con tag, dicho proceso lo ideal es que lo haga alguien que sepa del tema "Un linguiista")
2. Calculamos las probabilidad de emision y transmision (parte del modelo HMM) realizando conteos tomando en cuenta que una misma palabra puede tener diferente tag dependiendo la posicion en que se encuentra y entre que otros tags se encuentra
3. Una vez obtenido el modelo HMM podemos hace uso del Algoritmo Viterbi para encontrar los tags mas probables de una nueva secuencia de texto

Para crear el modelo HMM que modelara el POStagging se usara la libreria inithmm, y los parametros requeridos por la libreria es la matriz de transiciones y la matriz de emisiones 

La matriz de transiciones es una matriz de probabilidades la cual es de 52x52 dado que se tiene que hay 52 tags posibles, donde cada celda de la matriz denota la probabilidad de pasar de un tag a otro en secuencias de texto. por ejemplo la probabilidad de pasar de un verbo a un sustantivo en un texto, 

La matriz de emisiones son las probabilidaes de emitir una palabrar dado un tag, la cual es una matriz de 52x(numero de palabras en nuestro diccionario), puede llegar a ser una matriz extensa.

Los otros 2 parametros requeridos por el modelo HMM son el alfabeto de posibles tags (conjunto de estados ocultos) y el alfabeto de posibles estados observables o estado a emitir

No anexare el codigo de todos los pasos, solo pongo el utlimo paso donde ya se tiene realizados los conteos de palabras y ya se genero las matrices de emision y transicicion, ya despues aqui esta el paso 3 de generar el modelo HMM, y aplicando el viterbi decoding sobre una cadena de texto.

```R
estados_markov<-row.names(prob_transicion_tags)
alfabeto<-colnames(Prob_Emission_Matrix_final)
#install.packages("HMM")
library(HMM)

#Hay que pasar los estados markov a la matriz para reordenrla en el orden en que se pasaran los tags
row.names(Prob_Emission_Matrix_final)
row.names(Prob_Emission_Matrix_final[estados_markov,])

# Aqui vamos a instancciar un modelo pasando los estados posibles , todas las palabras posibles, y las matrices de probabilidades
modeloHMM<-initHMM(States = estados_markov,Symbols = alfabeto,transProbs = prob_transicion_tags,emissionProbs = Prob_Emission_Matrix_final[estados_markov,])

#Aqui aplicamos el algoritmo viterbi y vemos que se obtiene lo mismo que coreNLP y ademas mas rapido
viterbi(modeloHMM,observation = tokens_prueba)
```



Se probo una cadena simple y se comparo contra coreNLP usando la libreria de R cleanNLP

"Your contribution to Goodwill will mean more than you may know"

![CleanNLP_POStagging](https://i.imgur.com/eNwb0De.png)

![Paths Posibles](https://i.imgur.com/AS1nyPy.png)

![Viterbi Decoding](https://i.imgur.com/YCbvjpe.png)

Se puede ver que el decoding de esa cadena con una libreria como CoreNLP y con nuestro modelo da el mismo resultado de taggeo, 

Algo que se escapa es que hacer para el modelo HMM cuando viene una cadena en la cual aparece una palabra nueva que no esta en nuestra lista de palabras posibles, una manerda de hacerlo es aplicar un Smoothing Laplaciano, para mandar esa nueva palabra en nuestro diccionario al token "Desconocido" y asi poder manejar cualquier token desconocido como la palabra "Desconocido", se puede ayudar aun mas al modelo e intentar discernir con reglas ese token desconocido a que tag pertence.

Tratare de subir el codigo a github proximamente para no inundar el post con codigo incesario.