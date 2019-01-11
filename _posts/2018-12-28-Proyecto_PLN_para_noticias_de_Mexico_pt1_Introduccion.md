---
title: "Proyecto PLN para datos de noticias de México (Parte 1) Introducción"
date: 2018-12-28
tags: [NLP, webscrapping, Clasificación]
excerpt: "Introducción al proyecto de Procesamiento de Lenguaje Natural aplicado a noticias de México"
mathjax: "true"
---

## Introducción

Durante el 3er semestre de la maestría estuve trabajando con 3 amigos y compañeros de la maestria en un proyecto de NLP aplicado a noticias:

- Jorge Ramos
- Kenny Méndez
- Jonatan Romo

La especificación del proyecto requería que lo que se fuesé a elegir conllevará el uso de información extraída ya sea por alguna API o de alguna forma que fuese consumiendo datos cada x tiempo, y ademas que se hiciera uso de datos de tipo no convencional (datos no tipo tabla o columnares) por lo cual se sugería hacer uso de datos textuales o geolocalizados o de imagenes o grafos, u otro tipo de dato complejo y ademas usar o extender algunas de los temas y tecnicas vistos durante la clase (NLP, GIS, Imagenes etc..)

En este proyecto quisimos explorar varias tecnicas de NLP y aplicarlas a los textos de ciertos sitios de noticias de México

### Pasos Generales del Proyecto:

1. Habilitar la infraestructura para trabajar (Uso de containers Docker para acelerar la obtención del ambiente de trabajo)
   1. Se usaron 4 containers 
      1. container anaconda jupyter-notebook (**anacondajupnew**)
      2. container **nlpdb** (base de datos postgress)
      3. container selenium (para escrapear sitios con contenido dinamico)
      4. container con R Rstudio y librerías GIS (**rockergis**)
2. Elegir de donde obtener la información (Selecion de sitios de noticias a consultar)
3. Obtener la informacion de las noticias (Web Scrapping)
   1. Programar scrapeadors para cada web a escrapear
   2. Insertar en la base de datos las noticias que se vayan registrando
4. Validacion del contenido obtenido (noticias escrapeadas)
5. Automatizar el proceso de scrapeo y almacenamiento
   1. Se crearon bash-scripts para automatizar las tareas
   2. Se creo tareas programadas en el cron diarias (ejecucion diarias de bash-scripts)
   3. Se creo una estructura de logs para poder revisar lo que se ingresaba y lo que fallaba
6. Una vez tenienedo información se realizaron varios análisis y se aplicaron varias técnicas de NLP
   1. Clasificación de complejidad de textos (Usando caracteristicas de tageo y de morfologia de plabras)
   2. Clasificación de textos usando doc2vec (Clasificar )
   3. Buscar noticias similares (usando doc2vec)
   4. análisis de Topícos en noticias 
      1. Modelo de tópicos para seccion policiaca
      2. Modelo de tópicos para seccion opinion
      3. Modelo de tópicos para seccion local 
      4. Modelo de tópicos para seccion opinion nivel nacional

### Tencicas de NLP usadas en el proyecto:

- Preprocesamiento (Lematización, Stemming, descarte de stopwords, etcc)
- POS tagging (Encontrar verbos y otras partes del lenguaje para crear caracteristicas para un clasificador)
- Word emdeddings word2vec y doc2vec
- LDA (Latent Dirichlet Allocation) para el modelado de tópicos

### Librerías usadas

- Spacy (NLP, POStagging, doc2vec, Lematizacion etc)
- Gensim (doc2vec, LDA)
- NLTK (Para preprocesamientos)
- numpy
- scipy
- pandas
- folium (Mapas leaflet interactivos)
- Matplotlib
- plotly para graficos interactivos
- seaborn - solo para hacer mas agradables los graficos de matplotlib
- animation (R package)
- etc...

Usamos mayormente python para realizar los análisis y el escrapeo web, en realidad R para este proyecto solo lo usamos para validar clasificadores y para realizar unos choroplets animados

En la descripción de los pasos generales del proyecto cada paso conlleva varios subpasos y se puede extedner mucho; en cada uno de ellos por lo cual tratare de ir llenando un post por cada tema importante y este post servira como índice para poder acceder a las proximas publicaciones que hare sobre este proyecto

## Índice

- [Proyecto PLN para datos de noticias de México (Parte 1) Introducción (post actual)](https://adrian-rdz.github.io/Proyecto_PLN_para_noticias_de_Mexico_pt1_Introduccion/)
- [Proyecto PLN para datos de noticias de México  Infraestructura](https://adrian-rdz.github.io/Proyecto_PLN_parte2_Infraestructrua/)
- [Proyecto PLN para datos de noticias de México  Obtención de Informacion Scrapping-Web](https://adrian-rdz.github.io/Proyecto_PLN_parte3_Scrapping/)
- Proyecto PLN para datos de noticias de México Clasificación de complejidad de textos
- Proyecto PLN para datos de noticias de México  Doc2Vec Clasificación de textos
- Proyecto PLN para datos de noticias de México Doc2Vec Buscar noticias similares
- Proyecto PLN para datos de noticias de México Análsis de Tópicos modelo LDA
