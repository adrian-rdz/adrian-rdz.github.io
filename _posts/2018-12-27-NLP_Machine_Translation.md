---
title: NLP Machine Translation (wordvectors parte 3)
author: Adrián Rodríguez
date: 2018-12-27
tags: [NLP, Traducción, word2vec, API, R]
excerpt: "Traducción de un idioma a otro usando representaciones vectoriales de idiomas y corpus pareados"
mathjax: "true"
---

## Traducción usando word2vec

En este post voy a describir los pasos necesarios para poder crear un traductor de un lenguaje a otro usando las representaciones vectoriales word2vec, esta forma de realizar la traducción no es de mi autoría, si se quiere remitir al paper original de Mikolov aqui esta la liga  [https://arxiv.org/pdf/1309.4168.pdf](https://arxiv.org/pdf/1309.4168.pdf)

La forma en que realice la implementación, no es tal cual la propuesta en el paper, mas adelante explico algunas diferencias que hubo ya sea por conveniencia o facilitar la implementación

### Pasos resumidos

1. Conseguir corpus pareados de los 2 idiomas entre los cuales se quiera realizar traducción
2. Entrenar los embeddings por separado para cada corpus

   1. Se obtiene 2 modelos (2 binarios)
3. De alguna forma obtener los cinco mil terminos mas frecuentes de ambos idiomas

   1. Se puede crear la matriz de terminos documentos para cada idioma (Dado que solo tenemos un documento no sera una matriz sino un vector de frecuencias de las paalbras, asi que solo tomammos los 5k mas frecuentes)
   2. NOTA: Este paso podria ser solo obtener los 5k mas frecuentes del idioma fuente del cual queremos traducir al otro idioma
4. Dado que aun y que tenemos los corpus pareados el hecho de obtener los 5k terminos mas frecuentes no nos garantiza tener como un diccionario de un idioma a otro, por lo cual tendremos que traducir los terminos usando algo de forma automatizada

   1. Se puede usar la api de Google para usar GoogleTranslate de forma automatica
   2. Formamos el diccionario de 5000 palabras y su traducción
5. Ya que tenemos el diccionario de las 5000 palabras, ahora teneemos que obtener una matriz de predictores y una matriz de respuestas

   1. La matriz de predictores corresponde ideonemante a una matriz de (5000xTamanio_representacion_vectrial)
   2. La matriz de respuestas (5000xTamanio_representacion_vectrial word2vec)
   3. Cada vector predictor y cada vector respuesta se obtiene consultando los modelos entrenados en el paso dos.
6. Aplicar ML- entrenar un modelo que entrene usando las 5k predictores y que obtenga las 5k respuestas
   1. En el paper de Mikolov usan SGD (Stochastic Gradient Descent)
   2. Yo usare regresión multivariada por conveniencia dado que esta en las librerias base de R y ademas no es un problema tan gigantesco que no pueda ser atacado en memoria.
7. Ya teniendo el modelo entrenado podemos traducir palabras (predecir la palabra en el otro idioma), cuando hacemos el predict del modelo y metemos una palabra nueva con la cual no se entreno el modelo estamos aplicandole una transformación y obteniendo un nuevo vector respuesta, este vector respuesta puede que no exista tal cual en el modelo word2vec destino por lo cual hay que traernos la palabra mas cercana a ese vector y con suerte será la traducción correcta

El paso 5 y 6 se puede empatar con lo comentado en la sección del paper cuando hablan de la matriz de traducción

$$\underset{W}{min}\sum_{i=1}^{n}||Wx_i - z_i||^2$$

Lo cual resumido en palabras es encontrar la matriz  de trasnformación W tal que al aplicarla a la representacion vectorial de una palabra fuente me regrese un vector similar o cercano al vector destino (el vector de la palabra traducida)



## Resultados:

Estadisticas de API de Google

![](https://i.imgur.com/QpeWlcQ.png)



Ejemplo de traducción de palabra mexico en google

![](https://i.imgur.com/xUkRUjh.png)

Ejemplo traduccion usando matriz de transformación

```
> res_vector<-W%*%t(model_en[rownames(model_en)=="mexico",])
> closest_to(model_es,t(res_vector))
        word similarity to t(res_vector)
1     méxico                0.8064970175
2      chile                0.6815989127
3  sudáfrica                0.6122477243
4    américa                0.6111849095
5     brasil                0.6108338745
6     canadá                0.6106123124
7      india                0.6087736302
8       perú                0.5961206555
9   colombia                0.5937931337
10 argentina                0.5900110837    
```

Algunos ejemplos que traduce relativamente bien

```
> traducir_en_es("man dog rest show free commerce can thank black",W)
[1] "hombre salchicha resto demostrar libre electrónico puede agradecerle negro"
\end{minipage}
```

Algunas palabras donde no funciona nada bien

```
> ## como bigramas de salida ocasiona que exista problemas
> traducir_en_es("who what where am know good red",W)
[1] "quienes realmente donde siento sé bueno burocracia"
```

### Accuracy en entrenamiento

Ahora para ver como traduce en los 5k (de entreanmiento por asi decirlo) y en los 1k términos siguientes en frecuencia los cuales no fueron entrenados

Para corroborar si podemos llamarlo así accuracy mande traducir las 4652 palabras con las que se entreno uasndo mi funcion y se comparo vs google translate \textbf{58.2 de accuracy}

```
contador_correcto/4652
[1] 0.5827601032
```

Considerando sinónimos digamos que podemos decir que anda mas arriba, por ahi en el paper de Mikolov comentan algo de eso.

### Accuracy en Test

Para corroborar que tal funciona en las palabras con las cuales no se entreno probé las siguientes 1000 palabras mas frecuentes (despues de las 5k); saque su traducción de google con la API y las compare vs mi traducción en esas 1000 palabras con las cuales no se entreno se logro obtener **42.6** por ciento de accuracy ,pero este puede ser aun mayor dado que existen sinónimos y dado que google también no siempre da lo mejor aunque por lo general si, de todos modos no se descompone mucho la traducción sobre los elementos con los cuales no se entreno, lo cual es bueno y denota que generaliza bien para datos nuevos.

Lo obtenido previamente se puede mejorar usando bigramas y trigramas en ambos corpus, dado que limite mi análisis al uso de unigramas esto hace que este algo limitado en cuanto a las traducciones que puedo tomar. El problema del enfoque de bigramas trigramas y quizás frases es que ocuparía un buen de espacio el binario resultante de los embeddings y ademas el tiempo de computo para el entrenamiento de los embeddings se eleva.

Notas y consideraciones:

- En el entrenamiento de los embeddings dependiendo tu parametrización puede que varíe ciertos pasos en el algoritmo dado que por ejemplo yo solo use unigramas (un solo token de palabra), si usas bigramas es probable que esos bigramas aparezca entre tus 5k palabras mas frecuentes y habria que manejarlo de algun modo
- Otra consideración es que al momento de obtener las traducciones con la api de google Translate, es poco probable que la representación de las 5k traducciones todas sea unigramas, entonces puede ser por ejemplo que la palabra why en ingles te regrese "por que", Para evitar problemas yo solo me quede con unigramas (con lo cual de 5k solo me quede como con 4500 terminos)
- Para usar la api de Google hay que registrarse y obtener un token, y ademas para consultarla de manera programatica hay que ver una forma eficiente de hacerlo, en este caso yo use una libreria **library(googleLanguageR)**
- Para obtener las matrices de terminos documentos también puede complicarse dependiendo que implementación uso, primero intente con la libreria **tm** y tardaba mucho, despues use **library(text2vec)** y esta funciona mucho mejor para textos grandes
- Para manipulaciones de informacion y dataframes use la librería dplyr, la cual no es necesaria pero me facilta las cosas en veces 

## Conclusión

Lo interestante de llevar a cabo este problema es que implica el manejo de tecicas variadas, NLP; Machine Learning, uso de API's, manipulacion de datos y ademas lo mas importante entender el concepto del como se lleva a cabo y porque funciona lo descrito en el paper.

Creo que siempre es muy interesante el tratar de comprender e implementar lo visto en un paper , si es que se tiene el tiempo disponible.

## Referencias

- https://arxiv.org/pdf/1309.4168.pdf

