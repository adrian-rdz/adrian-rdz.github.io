---
title: "NLP word2vec"
date: 2018-12-19
tags: [ML,Clasificación,computacion]
excerpt: "NLP word2vec (Representación densa de palabras en un espacio vectorial)"
mathjax: "true"
---

## Representaciones de palabras y documentos en el contexto NLP

Existen varias formas de representar y tratar la informacion de tipo texto, y entre ellas se pueden dividir en representacones sparse o "ralas" y en representaciones densas, el concepto de denso o sparse se refiere a si la representación vectorial o matricial contiene muchos valores 0 o no,

Una representación densa puede comprimir el espacio en memoria y en disco de representar palabras y documentos, por ejemplo si queremos obtener la representacion DTM (Document Term Matrix) de un corpus de 10000 documentos en los cuales hay un vocabulario de cardinalidad (30000), para representar dicho corpus se requiere de una matriz de dimensiones 10000x30000 lo cual ocupa una gran cantidad de memoria (aprox en R 2.2 GB en flotantes o 1.1GB si es enteros) ; las implementaciones para trabajar con esto permiten guardar la DTM en modo sparse, con lo cual la matriz ocpue menos espacion en memoria; pero los manejos sobre la misma requieren algoritmos que puedan manejar matrices sparse.

Otra forma de atacar ese problema es buscar una representación densa de los documentos como lo es doc2vec con lo cual ajustando a que cada documento se represente por un vector de dimension 300, nuestra matriz quedaría de 1000x300 (aprox 22 MB o 10 Mb en enteros) la cual es manejable sin tener que convertir a representación sparse.

Para este post hablaremos de la representacion de palabras word2vec, la cual es una representación vectorial densa, la cual a diferencia del clasico BOW (Bag of Words), toma en consideración la semantica y el orden.

En una representacion Bag of Words (Term-Document-Matrix), una representacion vectorial de una palabra sería un vector de frecuencias que representa las apariciones de la palabra en un documento, claramente muchas palaras no ocurren en muchos documentos y hay otras que ocurren en la mayoria, las cuales algun subconjunto de las mismas las llamamos stopwords.

### Wordvectors

Para el caso que concierne este post, word2vec es una representación que capta la semantica de la información, lo cual se puede corroborar al ver las palabras proyectadas en un espacio PCA o t-sne, y donde se puede apreciar que palabras con contextos o semanticas similares quedan cercanas.

Previo a esta representación se podia usar representaciones vectoriales sparse como las Term-Term Matrix, las cuales podrían captar coocurrencias de palabras en una cierta ventana (distancia de aparicion de una palabra con respecto  a otra en un texto ), estas representaciones se siguen usando para Information Retrieval, Analisis de Topicos etc,..etc.

Este hecho de que los wordvectors capten la semantica puede ayudar dado que podriamos esperar que capte o aglomere palabras negativas con negativas, y positivas mas cercanas a positivas por lo cual puede ayudar en analisis de senitimientos, tambien se puede dar el uso de por ejemplo buscar sinonimos o ver la pertenencia a un grupo semantico de palabras o rankear la pertenencia a un campo semantico; Tambien con ciertas manipulaciones y transformaciones lineales (Matriz de transformación) se podria crear un traductor de un lenguaje a otro; tambien verificar similaridad de frases o textos (doc2vec) y muchas mas aplicaciones.

### Implementaciones y librerías para word2vec

Existen varias implementaciones de word2vec en distintos lenguajes de programacion de  la implmentación original, por ejemplo para R existen mas de un wrapper (entre ellos el usado aqui **wordVectors**) hacia esa implmentacion de C, en  python esta **gensim** (el que se usa en este post) el cual es un port para python de la implementacion en C en el sitio recomiendan tener instalado cython para acelerar el proceso de entrenamiento.

Las implementaciones se basan en el paper de Thomas Mikolov https://arxiv.org/pdf/1301.3781.pdf y https://arxiv.org/pdf/1310.4546.pdf

### Ejemplo de entrenamiento de wordvectors en R y python

#### R

```
library(wordVectors)

setwd("/home/adrianrdzv/Escritorio/CIMAT/Datos complejos/NLP/t3/clean_corpus/")
## Generamos un archivo preprocesado
prep_word2vec(origin="spanish_billion_words",destination="billions.txt",lowercase=T,force=T)

## si ya esta entrenado el binario ya no se vuelve a correr el entrenamiento
if (!file.exists("billions_vectors.bin")) {model_billions = train_word2vec("billions.txt","billions_vectors.bin",vectors=200,threads=7,window=12,iter=3,negative_samples=5)} else model_billions = read.vectors("billions_vectors.bin")

### solo correr lo siguiente si quieres sobreescrbiir el binario 
#model_billions = train_word2vec("billions.txt","billions_vectors.bin",vectors=200,threads=7,window=12,iter=3,negative_samples=5,force = T)
beep()

# cargar el binario
model_billions = read.vectors("billions_vectors.bin")
dim(model_billions)

```

#### python

```
from gensim.test.utils import common_texts, get_tmpfile #para ejemplos
from gensim.models import KeyedVectors
from gensim.models import Word2Vec
import os
import pandas as pd

# funcion para preprocesar el texto e ir iterando de forma eficiente los textos
class MySentences(object):
    def __init__(self, dirname):
        self.dirname = dirname
 
    def __iter__(self):
        for fname in os.listdir(self.dirname):
            for line in open(os.path.join(self.dirname, fname)):
                yield line.lower().split()
 
sentences = MySentences('/home/adrianrdzv/Escritorio/CIMAT/Datos complejos/NLP/t3/clean_corpus/spanish_billion_words') # a memory-friendly iterator
model = Word2Vec(sentences,workers=6,iter=3,size=200,window=12,negative=5)
```



Ambas entrenamientos previos se refieren a un corpus llamado billion_spanish_words, el cual como su nombre lo dice es algo extenso, por lo cual solo se entreno con 3 de los 100 archivos 200MB/8GB , es un proceso tardado (mas en R) , por lo cual hay que ser paciente

Otra observacion es que la implementacion de R, antes de mandar entrenar llama la funcion prep la cual junta los n archivos en un solo , por lo cual implica que debes tener espacio en disco disponible para poder albergar 2 veces el tamaño de texto a analizar, a diferencia en python se va leyendo usando un iterador, por lo cual no ocupa crear una copia, solo hay que tomar en cuenta que a esa funcion de python hay que agregarle el preprocesado de los textos para eliminar caracteres erroneos correos y demas cosas que se quieran limpiar del corpus al vuelo.

Otra cosa es que este ejercicio de obtener los vectores de las palabras dado que es muy costoso es posible guardar el binario generado y cargarlo en memoria para no tener que reentrenarlo, existen varios sitios en los que se puede encontrar los binarios y archivos Vec ya generados, por lo cual solo implica descargarlos, pero si tu objetivo es entrenar con un corpus que sea de algo muy especifico quizas si lo mejor sea que reentrenes y obtengas tus propios embeddings, o igual partir de un modelo y hacerle update al modelo para hacerlo mas robusto al contexto de los textos a analizar.

### Ejemplos similaridades en palabras

```
In [2]: model = KeyedVectors.load_word2vec_format('/home/adrianrdzv/Escritorio/C
   ...: IMAT/Datos complejos/NLP/t3/clean_corpus/modelo_gensim_billion_10a.bin')
   ...: 


In [3]: 

In [3]: model.similar_by_word("feo")
Out[3]: 
[('guapo', 0.7663998007774353),
 ('gracioso', 0.7659042477607727),
 ('tonto', 0.7174733281135559),
 ('simpático', 0.7106404304504395),
 ('estúpido', 0.7090980410575867),
 ('ingenuo', 0.7080118656158447),
 ('lindo', 0.7007209062576294),
 ('grosero', 0.6943960189819336),
 ('raro', 0.6915751695632935),
 ('pícaro', 0.6907127499580383)]

In [4]: model.wv.similar_by_word("auriculares")
/home/adrianrdzv/anaconda3/envs/keras/bin/ipython:1: DeprecationWarning: Call to deprecated `wv` (Attribute will be removed in 4.0.0, use self instead).
  #!/home/adrianrdzv/anaconda3/envs/keras/bin/python
Out[4]: 
[('altavoces', 0.8445422649383545),
 ('estéreo', 0.7970720529556274),
 ('audífonos', 0.7883132696151733),
 ('amplificadores', 0.7443886995315552),
 ('micrófono', 0.7427089214324951),
 ('conectores', 0.7186887264251709),
 ('adaptadores', 0.7145746946334839),
 ('bluetooth', 0.6967614889144897),
 ('firewire', 0.6708147525787354),
 ('cables', 0.66102135181427)]

```

```
In [5]: model.wv.similar_by_word("linux")
/home/adrianrdzv/anaconda3/envs/keras/bin/ipython:1: DeprecationWarning: Call to deprecated `wv` (Attribute will be removed in 4.0.0, use self instead).
  #!/home/adrianrdzv/anaconda3/envs/keras/bin/python
Out[5]: 
[('debian', 0.7560994029045105),
 ('gnu', 0.7424882054328918),
 ('gentoo', 0.7409307956695557),
 ('freebsd', 0.7348160743713379),
 ('ubuntu', 0.7318314909934998),
 ('mandriva', 0.7203937768936157),
 ('unix', 0.7106302976608276),
 ('delphi', 0.702393114566803),
 ('gnome', 0.690615177154541),
 ('kernel', 0.6830037832260132)]

```

```
In [6]: model.wv.similar_by_word("internet")
/home/adrianrdzv/anaconda3/envs/keras/bin/ipython:1: DeprecationWarning: Call to deprecated `wv` (Attribute will be removed in 4.0.0, use self instead).
  #!/home/adrianrdzv/anaconda3/envs/keras/bin/python
Out[6]: 
[('wifi', 0.6328126192092896),
 ('web', 0.5981825590133667),
 ('adsl', 0.5898529887199402),
 ('internautas', 0.5779457688331604),
 ('módem', 0.5693455934524536),
 ('intranet', 0.5435858964920044),
 ('online', 0.5365487337112427),
 ('rdsi', 0.5346505641937256),
 ('inalámbrico', 0.5322741270065308),
 ('wireless', 0.5117411613464355)]


```



### Representaciones PCA y t-sne de word2vec

Dado que es un corpus grande es dificil visualizar en un plano las palabras

```
from sklearn.decomposition import PCA
from matplotlib import pyplot

%matplotlib

X = model[model.wv.vocab]
pca = PCA(n_components=2)
result = pca.fit_transform(X)
pyplot.scatter(result[:, 0], result[:, 1])



pyplot.scatter(result[1:100, 0], result[1:100, 1])

words = list(model.wv.vocab)
for i, word in enumerate(words):
    pyplot.annotate(word, xy=(result[i, 0], result[i, 1]))
    if(i==100):
        break

```



En R

Para mejorar un poco la visualizacion se realizo clustering con K-medias y se grafico plots con solo samples de 2 y 3 clusters  a la vez

```
pca_model_billions<-prcomp(model_billions)
sum((pca_model_billions$sdev/sum(pca_model_billions$sdev))[1:100])

pca_model_billions<-prcomp(model_billions,rank. = 100)
dim(pca_model_billions$rotation)

model_billions_pc<-predict(object = pca_model_billions)
dim(model_billions_pc)
print(object.size(model_billions_pc),units="Mb")

set.seed(6)
res_kmeans<-kmeans(model_billions_pc,centers = 350,iter.max = 40)
res_kmeans$cluster

```

```
## Exploracion de clusters
clusters_visualizar<-c(1,2,3)
indices_clusters<-which(model_aumentado[,1] %in% clusters_visualizar)
length(indices_clusters)

## hacer sampling de los elementos de los clusters
set.seed(1)
indices_clusters<-sample(indices_clusters,200)
res_kmeans$cluster[indices_clusters]

plot(model_billions_pc[indices_clusters,])
text(model_billions_pc[indices_clusters,],row.names(model_billions[indices_clusters,]),col = as.integer(as.factor(res_kmeans$cluster[indices_clusters])),cex=0.7)

## Exploracion de clusters
clusters_visualizar<-c(30,40,50)
indices_clusters<-which(model_aumentado[,1] %in% clusters_visualizar)
length(indices_clusters)

## hacer sampling de los elementos de los clusters
set.seed(1)
indices_clusters<-sample(indices_clusters,200)
res_kmeans$cluster[indices_clusters]

plot(model_billions_pc[indices_clusters,])
text(model_billions_pc[indices_clusters,],row.names(model_billions[indices_clusters,]),col = as.integer(as.factor(res_kmeans$cluster[indices_clusters])),cex=0.7)

## Exploracion de clusters
clusters_visualizar<-c(11,12,13)
indices_clusters<-which(model_aumentado[,1] %in% clusters_visualizar)
length(indices_clusters)

## hacer sampling de los elementos de los clusters
set.seed(1)
indices_clusters<-sample(indices_clusters,200)
res_kmeans$cluster[indices_clusters]

plot(model_billions_pc[indices_clusters,])
text(model_billions_pc[indices_clusters,],row.names(model_billions[indices_clusters,]),col = as.integer(as.factor(res_kmeans$cluster[indices_clusters])),cex=0.7)

```

![](https://i.imgur.com/n5ZUqNR.png)

![](https://i.imgur.com/lstVKR4.png)

![](https://i.imgur.com/0InPY3T.png)

Otra visualizacion que usa un tipo de escalamiento multidimensional t-SNE falta agregarla, por lo general se reporta que aglomera mejor los objetos similares, la libreria wordVectos ya cuenta con la funcion sobrecargada de plot la cual manda hacer t-sne y su plot.

### Consideraciones

Como comentaba el proceso de entrenar los wordvectors puede llegar a ser muy intensivo en CPU y dependiendo del corpus podria tomar de horas a días.

Otra consideración es que hay que investigar bien el modelo para entender los parametros que hay que tunear, como la ventana y el negative_sampling, con una mala paraemtrización puedes perder tiempo y obtener malas representaciones  de las palabras.

### Referencias

1.  https://arxiv.org/pdf/1301.3781.pdf
2. https://arxiv.org/pdf/1310.4546.pdf
3. https://rare-technologies.com/parallelizing-word2vec-in-python/
4. https://rare-technologies.com/word2vec-tutorial/
5. https://github.com/bmschmidt/wordVectors
6. http://kavita-ganesan.com/gensim-word2vec-tutorial-starter-code/#.W7blKxRRefg
7. https://stackoverflow.com/questions/12703842/how-to-tokenize-natural-english-text-in-an-input-file-in-python
8. https://github.com/RaRe-Technologies/gensim/issues/567
9. https://stackoverflow.com/questions/50555303/how-to-install-gensim-from-anaconda-prompt
10. http://kavita-ganesan.com/gensim-word2vec-tutorial-starter-code/#.W7blKxRRefg