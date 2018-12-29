---
title: Imputación Estadistica
date: 2018-12-28
author: Adrián Rodríguez
---

## Introducción

En un gran parte de los datasets disponibles a lo largo de la historia se tienen datos faltantes, y muchos de los datasets historicos que podemos usar en la actaulidad los cuales no tenemos con datos faltantes se tiene indicios de que les fueron removidos, ya que se tenía la creencia que un estudio en el cual se tenga abundantes datos faltantes es un estudio que no se podia tomar en serio.

Los datos faltantes se pueden producir por una variedad de razones y en una variedad distinta de tipos de datasets.

El hecho de remover los datos faltantes es una de la forma de tratar con los datos faltantes pero podria decirse que aunque es la forma mas facil de tratarlos quizas también sea una de las peores formas de manjarlos, dado que no estamos usando toda la información disponible en el dataset por lo tanto podriamo estar sesgando el analisis.

## Objetivos de imputación

Una cosa que hay que diferenciar es que imputación y predicción son 2 cosas distintas, por lo cual la manera de revisar que tan buena es una imputación no se basa tanto en si los valores imputados son cercanos a los valores reales, mas bien imputación tiene como objetivo dar un valor aproximado en los datos faltantes con la finalidad de poder realizar un analisis posterior de manera transparante (regresion, clasificación etc)

La finalidad de la imputación es poder usar todos los datos observados en los posteriores análisis, y la forma de analizar que tan bien estamos imputando es viendo si no deformamos la distribución y los estadisticos.

## Mecansimos de perdida de datos

Existen diferentes mecanismos de perdida de datos, y dependiendo el mecansimo podremos llegar a imputar mejor con ciertos métodos

- MCAR (Missing Complete At Random) ($P(X_{m}|X_{miss},X_{obs}) = Pr(X^{m})$)
- MAR (Missing At Random) ($P(X_{m}|X_{miss},X_{obs}) = Pr(X^{m}|X_{obs})$)
- MNAR (Missing Not At Random) Existe dependencia entre los datos faltantes un ejemplo, personas que rechazan decir sueldo en una encuesta.

Por lo general los métodos de imputación esperan o tienen el supuesto de que los datos provengan de los primeros 2 mecanismos de perdida de datos.

El caso MNAR puede darse en casos donde hay censura de datos

## División de los métodos para tratar Missings

Los métodos para tratar con datos faltantes se pueden dividir en categorias:

- Métodos que eliminan observaciones
- 1. Listwise deletion (Analisis de casos completos)

- Métodos que utilizan todos los datos disponibles
- 1. métodos de maxima verosimiltud (ML)
- 2. Expectation Maximization

- Métodos que imputan los datos faltantes
- 1. Imputación Simple (LOCF,media,Hot deck. KNN)
- 2. Imputación Multiple (Mice, Missing Forest, EMB, etc)

## Imputacion Simple

Los métodos de imputación simple, son los que se usan mas frecuentemente, debido a la conveniencia en la facilidad de aplicarlos, al finalizar la imputación simple obtenemos un conjunto de datos completo imputado con el cual podemos realizar analisis posteriores , como regresión, SVM u otra cosa.

## Imputación Multiple

Imputacion multiple implica imputar m valores por cada celda o valor en una matriz de datos, creando m conjuntos de datos completos. Los valores observados son iguales en cada data set, y los valores faltantes imputados variane entre cada data set, los cuales son muestreados de una distribución predictiva de los datos faltantes.

Al finalizar una imputación multiple tenemos m conjuntos de datos completos, con los cuales podemos realizar analisis, y el modo de usar todos los m conjuntos es usando las reglas de rubin.

La imputación multiple es recomendado contra la imputación simple; para reducir el sesgo en los estadisticos, y el m puede variar entre; m mayor va mejorando.

## Imputacion Multivariada
A un patron de datos faltantes se le llama univariado solo si una sola variable del conjunto es a la que se tiene que imputar datos, en caso contrario es multivariado

Los patrones de datos faltantes se se pueden dividir en monotonos y no monotono, otra forma de categorizarlos es en conexos y no conexos.

## Problemas a enfrentar con imputación multivariada

- Los predictores usados para imputar en una o mas variables pueden contener missings
- Variables de tipos distintos
- Colinearidad
- El orden de las columnas puede ser significativo
- Relaciones complejas entre la porcion a imputar y los predictores

## Estrategias para imputar datos multivariados

Imputación monotona

Joint Modelling 

Fully conditional specification

## Imputación monotona

La imputación de datos monotona, puede ser generada por una secuencia de métodos univariados.

Suponiendo que tenemos las variables $Y_1,Y_2,..Y_p$ con un patron de datos faltantes montonos, se imputa de $Y_1,X$ ignorando $(Y_2,...Y_p)$, en el segundo paso se imputa $Y_2$ ignorado $Y_1,Y3,...Y_p$ cada imputación se puede realizar con distintos métodos (KNN,PMM,Regresion Logistica, etc)

## Modelación conjunta (Joint Modelling)
Este tipo de imputación inicia con el supuesto de que los datos se pueden describir por una distribución multivariada, (la mas comun Normal Multivariada (EMB)) 

Para razones de eficiencia los patrones de datos faltantes se pueden modificar agrupando (clustering de missings).

Como nota: el uso de modelos normales mutlivariados se usa para datos categoricos haciendo rounding a los datos continuos imputados (aunque el redondeo puede producir sesgo).

## Fully Conditional Specification

FCS Imputa datos multivariados, variable por variable, el metodo reuqiere especificar un modelo para cada variable incompleta (en mice se usa una matriz de predicción)

FCS es una generalizacion natural de una imputacion univariada. Tiene varias ventajas con respecto al modelado conjunto dado que parte un problema k-dimensional en k-problemas, se puede incorporar métodos de imputaacion que preserven caracteristicas como limites, interacciones 

## Algunos métodos de imputación

- Imputación por media
- Imputación por mediana
- Imputación por regresion
- Imputación por regresion estocastica
- MICE
- PMM
- KNN
- EMB

## Algunas paquetes utiles en R analizar datos faltantes

### Para explorar y visualizar

- library("naniar") 
- library("visdat")

### Para imputar

- library(VIM) (Imputacion knn)
- library(Hmisc) (Imputacion simple (mediana), (clustering de missings))
- library(mice) (Imputacion MICE, regresion, regresion estocastica etc..)
- library(amelia) (Expectation Maximization Bootstrap (EMB) )

## Ejemplos

Voy a mostrar unos ejemplos que corrí en R y los cuales se pueden encontrar en el libro (Flexible Imputation  of Missing Data)

El conjunto de datos a anlizar es air quality

### Visualización de missings

#### Distribución de missings

![](https://i.imgur.com/gUp96BE.png)



#### Distribucion de missings por pares de variables

![](https://i.imgur.com/Kb0gvqA.png)

### Efectos de metodos de imputación en distribucion resultante

#### Imputacion por media

![](https://i.imgur.com/gDbeyPe.png)

#### Imputación por regresión

![](https://i.imgur.com/KXCrDQx.png)

#### Imputación por regresión estocastica

![](https://i.imgur.com/untM0Hv.png)

### Notas

- La distribución por media sesga estadisticos y deforma la distribución de los datos enormemente
- La imputación por regresión no deforma tanto la distribucion pero aumenta correlaciones entre variables (deforma estadisticos)
- La imputación por regresión estocastica ya se ve mucho mejor dado que en la imagen de la derecha se ve que no aumenta las correlaciones como lo hace el metodo de regresión simple

## Consideraciones 

Al realizar imputación es bueno revisar nuestro(s) datasets imputados, para visualizar y comprobar que nuestra imputación mantenga la distribución de los datos y no deformemos estadisticos, dado que dependiendo el analisis posterior, podría sesgarlo.

También hay que tener en cuenta que en casos de imputación reales podemos tener colineariadad y varios retos a superar los cuales hara que fallen las funciones y tendremos que ir moviendo parametros a mano y tunenando nuestra imputación.

## Referencias:

### Fuentes de Informacion (Libros, Articulos) :

- [Flexible Imputation of Missing Data (Stef Van Buuren)](https://stefvanbuuren.name/fimd/)

- mice: [Multivariate Imputation by Chained Equations in R](https://www.jstatsoft.org/article/view/v045i03) (Journal Of Statistical Software)

- [Amelia II: A Program for Missing Data](https://www.jstatsoft.org/article/view/v045i07) (Journal of Statistical Software)

- [Imputation with the R Package VIM](https://www.jstatsoft.org/article/view/v074i07) (Journal Of Statistical Software) doi: 10.18637/jss.v074.i07

- A comparison of imputation methods for handling missing scores in biometric fusion

- https://www.cse.msu.edu/~rossarun/pubs/DingRossMissingData_PR2012.pdf

- Multiple imputation in the presence of non-normal data DOI: 10.1002/sim.7173

- Fully conditional specification in multivariate imputation - S. VAN BUUREN

- http://eprints.ucm.es/43961/1/TFM_PlanchueloGomez.pdf (Tesis en Español)

- https://dugi-doc.udg.edu/bitstream/handle/10256/11753/tcasr1de1.pdf?sequence=5 (Tesis ver 1.5.1)

### Fuentes Web (Ejemplos y Guias)

1. https://thomasleeper.com/Rcourse/Tutorials/mi.html
2. https://stats.idre.ucla.edu/r/faq/how-do-i-perform-multiple-imputation-using-predictive-mean-matching-in-r/
3. https://rpubs.com/mhanauer/mice
4. https://rpubs.com/kaz_yos/mice-amelia
5. https://rpubs.com/corey_sparks/63681
6. https://www.kdnuggets.com/2017/09/missing-data-imputation-using-r.html (Ejemplos mice)
7. http://www.rpubs.com/jkr_84/300462 (mice)
8. https://rpubs.com/auroratsai/344055 (Ejemplo VIM y mice muy completo)
9. https://rpubs.com/Vibha/384774 (Listwise deletion vs Multiple Imputation)