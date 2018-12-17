---
title: "Naive Bayes Seniment Analysis"
date: 2018-12-17
tags: [ML,Clasificación,computacion]
excerpt: "Clasificador Ingenuo Bayesiano aplicado a analisis de sentimientos"
mathjax: "true"
---

## Clasificador Ingenuo Bayesiano

### Un poco de historia y teoria

https://www.analyticsvidhya.com/blog/2017/09/naive-bayes-explained/

El clasificador ingenuo bayesiano, es un clasificador que hace uso de la regla de bayes para el calculo de probabilidades, aun y que es sencillo aplicarlo e implementarlo, y tiene varios casos de uso también sirve como base para testear otros algoritmos, en cuanto a su precisión.

El clasificador ingenuo bayesiano supone que los predictores son independientes entre si (suposición ignenua) y esto simplifica los calculos de la regla de bayes dado que la probabilidad de dos eventos independientes de manera conjunta es el producto de ellas.

mas teoria importante se puede ver en http://sebastianraschka.com/Articles/2014_naive_bayes_1.html y en el capitulo 4 de Jurafsky (Speech and Language Processing)

**Teorema/Regla de Bayes**

$$P(c|x) = \frac{P(x|c)P(c)}{P(x)}$$

Donde 

* $$P(c|x)$$ es la probabilidad a posteriori
* $$P(x|c)$$ el Likelihood
* $$P(c)$$ es la probabilidad a priori de la clase (las suposiciones o evidencias que tenemos (se puede basar en proporciones y conteos a partir de los datos que tenemos disponibles))
* $$P(x)$$ probabilidad a priori del predictor

Esta regla es muy util dado que podemos predecir la probabilidad de una clase dada la información observable al igual los otros parametros son estimables con la informacion observable o incorporando nuestro conocimiento previo del proceso.

El lado izquierdo de la regla de bayes la probabildiad a posteriori es la que nos permitira decidir a que clase asignamos un neuvo documento.

**Aplicaciones**

* Clasificación de textos (Sentimientos, Spam,etc)

* Prediccion en tiempo real (incluyendo el punto previo) dada la ventaja de poder incorporar nueva informacion se puede implementar para ir prediciendo al vuelo en base a la informacion disponible
* etc...

### Aplicación a análisis de sentimientos

Resumen  de Pasos:

Se parte de  un conjunto de textos ya etiquetados como negativos y positivos, en este caso es posible tener los textos pertencientes a cada clase en una carpeta distinta,

Se lee los datos de entrenamiento, y se extraen los conteos necesarios y razones, las probabilidades prior y los likelihood de cada palabra a pertenecer a cada clase, ya habiendo entrenado el modelo se puede usar para predecir nuevos textos.

el pseudo algoritmo que se puede ver en el libro de Juraksky aplicado a un problema de n clases es el siguiente

![](https://i.imgur.com/m4QfyOg.png)

Se puede ver en el algoritmo que las verosimlitudes de cada palabra en una clase, corresponden a que tanto aparece una palabra en un documento marcado con cierta clase, y ponderado sobre el numero de palabras del vocabulario entero que estan asociados a la clase

### Conjunto de datos a usar

El conjunto de datos a usar son unas reseñas de electronicos, las cuales ya estan separadas en su clase correspondiente, y se parte el set de datos para poder obtener el accuracy de prueba.

https://www.sfu.ca/~mtaboada/SFU_Review_Corpus.html

**Ejemplo de reseña negativa:**

*Ahora se porque fagor da tantos años de garantia, en esos años acabas de la lavadora hasta el sombrero, por no decir una burrada. En la tienda me dijeron que fagor era extraordinario y una marca estupenda, pues bien esto fue antes de poner la lavadora, en 3 años, 3 reparaciones. la primera el circuito electronico, la segunda engancha la ropa (en julio de este año) y como no saben lo que es pues cambian el tambor, la puerta y los amortiguadores. Pues bien, la ultima ayer, despues de 5 meses ahora ya nadie sabe nada sin centrifugar ni nada se pone como loca y empieza a dar bandazos por la cocina, eso sí llevandose todo por delante, armarios desencajados y marcados y eso estando en casa, que ni estoy no se lo que hubiese encontrado.*

*Y claro, que dice fagor, nada que alguna vez sale alguna lavadora mala...je,je,je me rio yo.Y ya sabeis , la garantia solo incluye las piezas, asi que cada vez que se rompre 60 minimos de mano de obra y desplazamiento. Es un chollo lo de arreglar lavadoras fagor.* 

### Resultados y comparación vs otros metodos de clasificación

#### Naive-Bayes

Ya particioando en train y test se tiene para train 320 documentos de los cuales 160 son positivos y 160 negativos, y para el test son 80 (40+ yh 40-)

Accuracy en test

![1545084574046](https://i.imgur.com/2n6tDqD.png)

Tambien se probo otros metodos de clasificación obteniendo la matriz de terminos documentos y normalizandola a TFIDF (Term Frequency Inverse Document Frequency), los componentes de la matriz sirven de caracteristicas o predictores para los siguientes metodos de clasificación, es posible mejorar los predictores tomando una representacion mas densa de los tokens en el texto o de los documentos como word2vec, phrase2vec y doc2vec, en otro post se analizara la aplicacion de esas otras representaciones para clasificar textos

#### a) SVM

```
library(caret)
library(doParallel)

set.seed(123)
# Use the expand.grid to specify the search space 
grid <- expand.grid(C = c( 0,0.2,0.5,1)
)

ctrl <- trainControl(method="cv",   # 10fold cross validation
                     summaryFunction=twoClassSummary, # Use AUC to pick the best model
                     classProbs=TRUE)
cl <- makePSOCKcluster(4)
registerDoParallel(cl)  

#Train and Tune the SVM
svm.tune <- train(sentimiento ~ ., data = DF_TFIDF[81:400,], 
                  method = "svmLinear",
                  preProc = c("center","scale"),
                  tuneGrid = grid,
                  trControl=ctrl)

stopCluster(cl)
```

![1545084632997](https://i.imgur.com/RklaoFH.png)



![1545084651070](https://i.imgur.com/gR9kHY7.png)

#### b) CART 

Con parametrización

```
library(doParallel)
cl <- makePSOCKcluster(4)
registerDoParallel(cl)  

tune.gridcart <- expand.grid(cp=seq(0.00005,0.5,0.005)) # Tunear usando parametro de complejidad

cctrl1 <- trainControl(method = "cv", number = 10)
set.seed(123)
test_class_cv_form <- train(sentimiento ~ ., data = DF_TFIDF[81:400,], 
                            method = "rpart", 
                            tuneGrid =tune.gridcart,
                            trControl = cctrl1
)
#Stop after trainning
stopCluster(cl)
```

![1545084672446](https://i.imgur.com/9DDVEgZ.png)

#### c) Adaboost

con parametrizacion

```
library(doParallel)
cl <- makePSOCKcluster(4)
registerDoParallel(cl)

library(adabag)
grid <- expand.grid(mfinal = c(8,15), maxdepth = c(3, 5, 6),coeflearn = c("Breiman"))


cctrl1 <- trainControl(method = "cv", number = 10, returnResamp = "all",
                       classProbs = TRUE)


set.seed(123)
test_class_cv_model <- train(sentimiento ~ ., data = DF_TFIDF[81:400,], 
                             method = "AdaBoost.M1", 
                             trControl = cctrl1,
                             tuneGrid = grid)

stopCluster(cl)
```

![1545084689318](https://i.imgur.com/J5554Kr.png)

![1545084708472](https://i.imgur.com/qRY8rgF.png)

#### d) Red neuronal

con parametrizacion

```
modelo_entrenado<- neuralnet(f,
                             data = as.matrix(DF_TFIDF[81:400,]),
                             hidden = c(5,5),
                             act.fct = "logistic",
                             linear.output = FALSE,
                             lifesign = "minimal")
```



![1545084746094](https://i.imgur.com/3s47Mrr.png)



Aun y que otros metodos de ML lo superan, al menos para este caso no es por mucho, y como ya se decia pues sirve como de base, si quieres proponer un nuevo metodo siempre hay que compararse contra los metodos clasicos, y ver si logras superarlos.