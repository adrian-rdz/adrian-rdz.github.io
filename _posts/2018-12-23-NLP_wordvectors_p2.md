---
title: NLP wordvectors parte 2
date: 2018-12-23
author: Adrian Alejandro Rodriguez Villarreal
---

## Wordvectors p2

Existen varias implementaciones ademas de word2vec, fasttext, glove entre otras,

En este caso voy a revisar un poquito la implementacion de fasttext (la vez pasada era word2vec con gensim en python y con la librería wordVectors de R)

Una de las diferencias clave de fasttext a otras representaciones vectoriales de palabras es que permite integrar lo que se le llama subword-representations y esto se refiere a entrenar los embeddings no solo con los tokens a nivel palabra, si no a nivel n-gramas de una palabra por ejemplo tomar los token a nivel caracter o silaba 

Ventajas de este enfoque:

- Se puede lograr generar el vector de palabras que no existen en el diccionario siempre y cuando existe alguna subparte de la palabra (Esto es una gran ventaja con respecto a otros modelos)
- Se puede lograr captar la semantica a nivel lexicografico, por ejemplo cambios de tension, o inflexiones de palabras , esto tampoco se logra captar con otras implementaciones

Vamos a comenzar, por usar un modelo precargado que brindan los de facebook en la pagina de fasttext

```
import gensim
model_es = gensim.models.KeyedVectors.load_word2vec_format("cc.es.300.vec",limit=100000)
existent_word = "computadora"
existent_word in model_es.wv.vocab
Out[4]: True
model_es.most_similar("computadora",topn=10)
Out[7]: 
[('computador', 0.8332194089889526),
 ('compu', 0.7348629832267761),
 ('ordenador', 0.7246257066726685),
 ('computadoras', 0.7238732576370239),
 ('Computadora', 0.7233468890190125),
 ('laptop', 0.7221924662590027),
 ('notebook', 0.6761027574539185),
 ('computadores', 0.6303346157073975),
 ('computación', 0.6041660308837891),
 ('Computador', 0.5840513706207275)]

```

Ver la pertencia a un campo semantico

```
In [8]: not_matching = model_es.doesnt_match("unix computadora ubuntu linux".split())
In [9]: not_matching
Out[9]: 'computadora'

```

Ejemplos

Obtener inflexiones de palabras a tiempo pasado, u otros tiempos usando una transformacion lineal de los vectores

```
In [26]: model_es.similar_by_vector(model_es["estuve"]-model_es["estar"]+model_es["correr"] ,topn=10)
Out[26]: 
[('corrí', 0.6363905668258667),
 ('anduve', 0.5837474465370178),
 ('volví', 0.5745130777359009),
 ('estuve', 0.570054292678833),
 ('correr', 0.5648515224456787),
 ('salí', 0.5633352994918823),
 ('hice', 0.5631434917449951),
 ('empecé', 0.557849109172821),
 ('pasé', 0.5555869936943054),
 ('fui', 0.5544785261154175)]

In [27]: model_es.similar_by_vector(model_es["estuve"]-model_es["estar"]+model_es["jugar"] ,topn=10)
Out[27]: 
[('jugué', 0.7661914229393005),
 ('jugaba', 0.6636667847633362),
 ('jugar', 0.6550686359405518),
 ('jugue', 0.6255834102630615),
 ('jugábamos', 0.5827611684799194),
 ('jugando', 0.5807048082351685),
 ('jugamos', 0.5788702964782715),
 ('estuve', 0.5546524524688721),
 ('pasé', 0.5507714748382568),
 ('empecé', 0.545343279838562)]

In [28]: model_es.similar_by_vector(model_es["estuve"]-model_es["estar"]+model_es["llegar"] ,topn=10)
Out[28]: 
[('llegué', 0.7508662939071655),
 ('volví', 0.6238141059875488),
 ('Llegué', 0.6231335997581482),
 ('estuve', 0.6226938962936401),
 ('conseguí', 0.6154546737670898),
 ('fui', 0.6064709424972534),
 ('fuí', 0.5994151830673218),
 ('llego', 0.5954252481460571),
 ('pasé', 0.5948404669761658),
 ('visité', 0.5948381423950195)]

In [29]: model_es.similar_by_vector(model_es["estuviste"]-model_es["estar"]+model_es["llegar"] ,topn=10)
Out[29]: 
[('llegaste', 0.5637564659118652),
 ('llegar', 0.49563658237457275),
 ('llegué', 0.4868238866329193),
 ('conseguiste', 0.4632262885570526),
 ('hiciste', 0.46203112602233887),
 ('llegas', 0.4600544273853302),
 ('llegamos', 0.46001720428466797),
 ('lograste', 0.4598486125469208),
 ('conociste', 0.4595335125923157),
 ('llegues', 0.45254701375961304)]


```

Otros tipos de analogias

![](https://i.imgur.com/aGlKWIJ.png)

```
In [72]: model_es2.most_similar(positive=["xbox","playstation"],negative=["sony"])
Out[72]: 
[('psp', 0.6574689745903015),
 ('gamecube', 0.6418702006340027),
 ('cwf', 0.63817298412323),
 ('zune', 0.63556307554245),
 ('nintendo', 0.6317270994186401),
 ('instant', 0.6309843063354492),
 ('pocket', 0.6306006908416748),
 ('slots', 0.6225025057792664),
 ('game', 0.6195276975631714),
 ('scanner', 0.6117942333221436)]

In [80]: model_es2.most_similar(positive=["rico","malo"],negative=["pobre"])
Out[80]: 
[('provechoso', 0.47595012187957764),
 ('bueno', 0.4716564416885376),
 ('rican', 0.4585948884487152),
 ('sensato', 0.4568844139575958),
 ('beneficioso', 0.41141441464424133),
 ('banús', 0.40097182989120483),
 ('paradójico', 0.3990849256515503),
 ('vallarta', 0.39753490686416626),
 ('unico', 0.3932851552963257),
 ('ventajoso', 0.3847326934337616)]

```

Varios de los ejemplos en ingles que vienen no me funcionaron tan bonito para español

![1545531674637](https://i.imgur.com/R4Z77Q2.png)

Esto que se realizo a mano se puede mejorar obteniendo varios vectores de entrenamiento pareados y obtener una matriz de transformación usando Regresion multivariada o SGD (Stochastic Gradient Descent), esto lo vere en otro post planeado de como realizar traducciones usando embeddings de 2 idiomas.

Revisando hay otros embeddings preentrenados que parece que capta mejor la semantica (para el español) con la parametrización que los hicieron, es la liga del final, ahi vienen los .vec para bajarlos 

Algo que veo en los embeddings que proporciona fasttext es que acepta muchas palabras como con formatos malos aun y que es una infinidad de palabras no se que tanto pegue el tener tantas versiones de las palabras , al menos en mi punto de vista le pega algo al realizar estas analogias y otras cosas.

## Referencias

- https://fasttext.cc/docs/en/unsupervised-tutorial.html
- https://stackoverflow.com/questions/50910287/loading-of-fasttext-pre-trained-german-word-embeddings-vec-file-throwing-out-o
- La clase que dio un compañero (Jean Arreola) en la maestría 
- https://radimrehurek.com/gensim/models/keyedvectors.html
- https://github.com/uchile-nlp/spanish-word-embeddings

