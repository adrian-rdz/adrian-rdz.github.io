---
title: "Spacy primeros pasos y la Fundación de Isaac Asimov"
date: 2018-12-20
tags: [general,estadistica,computacion]
excerpt: "Computo Estadistico,NLP,GIS,Imagenes,MCMC,Bootstrap,Proyectos"
mathjax: "true"
---

## Introducción

Spacy es una libreria libre y open source de python destinada a procesamiento de lenguaje natural, la cual se puede usar en conjunto con otras librerias como gensim, pytorch y tensorflow

Entre las operaciones que puedes realizar se encuentran:

- Tokenización
- Lematización
- NER (Named Entity Recognition)
- Wordvectors preentrenados
- Integración con Deep Learning
- POS (Part of Speech Tagging)
- Labelled Dependency Parsing
- Similaridad de Textos usando representaciones vectoriales
- Segmentacion de sentencias
- Visulizaciones para NER y Dependency parsing
- etc..

En la pagina oficial puedes ver todas las operaciones que puedes llevar a cabo y algunas ventajas con respecto a otras librerias como la velocidad ([https://spacy.io/](https://spacy.io/))

## Aplicación

La libreria spacy y en general los procesamientos de lenguaje natural se pueden aplicar a analizar textos muy variados y a enriquecer análisis donde se tiene textos (noticias, tweets, encuestas, reseñas, etc), en este caso para mostrar algunas de las operaciones con Spacy, voy a analizar el libro **La fundacion de Isaac Asimov** libro que forma parte del Ciclo de Trantor y el cual lo tengo en formato **epub**

La elección de analizar un libro de Isaac Asimov es porque soy fan de varias de sus sagas, y ademas esta inspirado en un post que vi en un blog hace tiempo (ya no encontre el enlace) en el cual analizaban en una linea temporal la serie de game of thrones haciendo uso de tecnicas de ML, por lo cual este solo es un incio pero planeo aplicar mas tecnicas de procesamiento de lenguaje natural al analisis de los libros de Isaac Asimov , y también planeo realizar el análisis de series que me agradan haciendo uso de los subtitulos en formato srt que se puede encontrar en paginas como opensubtitles y subdivx etc...

Para analizar los capitulos del libro primero debemos ser capaces de obtener el texto del archivo epub, El formato epub internamente esta formado por  archivos xhmtl, para poder diseccionarlo hare uso de la librería ebooklib, y ya teniendo el codigo xhtml obtendre el texto scrapenadolo con la librería Beautifulsoup

#### Importamos la librerías

```python
import ebooklib
from ebooklib import epub
import spacy
nlp = spacy.load('es')  # spanish
import os
from ebooklib.utils import debug
from bs4 import BeautifulSoup
```

#### Cambiamos el directorio de trabajo


```python
os.chdir("/home/adrianrdzv/Descargas")
```

#### Leemos el archivo epub correspondiente al libro


```python
book = epub.read_epub("isaac_asimov_fundacion.epub")
```

#### Vemos algunos metadatos de libro


```python
print(book.EPUB_VERSION);print("\n")
#print(book.IDENTIFIER_ID);print("\n")
#print(book.items);print("\n")
print(book.title);print("\n")
print(book.version);print("\n")
print(book.get_metadata);print("\n")

```

    None

​    
​    Fundación


​    
​    2.0


​    
​    <bound method EpubBook.get_metadata of <ebooklib.epub.EpubBook object at 0x7f34842802b0>>


​    

#### Vemos la estructura de los documentos del epub

```python
## Ver como esta el epub cuantos documentos xhtml lo forman
for x in  book.get_items_of_type(ebooklib.ITEM_DOCUMENT):
    debug(x.id)
```

    'ciclo_trantor.html'
    'x0001.xhtml'
    'x0002.xhtml'
    'x0003.xhtml'
    'x0004.xhtml'
    'x0005.xhtml'
    'notas.html'
    'autor.xhtml'
    'cubierta.xhtml'
    'dedicatoria.xhtml'
    'info.xhtml'
    'sinopsis.xhtml'
    'titulo.xhtml'
    'primera_parte.xhtml'
    'segunda_parte.xhtml'
    'tercera_parte.xhtml'
    'cuarta_parte.xhtml'
    'quinta_parte.xhtml'

#### Vemos la tabla de contenidos del libro

```python
## Ver la tabla de contenidos TOC
for x in book.toc:
    print(x.title)
```

    Cubierta
    Fundación
    El Ciclo de Trántor
    Los Psicohistoriadores
    Los Enciclopedistas
    Los Alcaldes
    Los Comerciantes
    Los Príncipes Comerciantes
    Autor

#### Guardamos en una lista los xhmtls que analizaremos

```python
## Guardar los documentos del epub en una lista
book_xhtmls = []
for x in  book.get_items_of_type(ebooklib.ITEM_DOCUMENT):
   book_xhtmls.append(x)
```


```python
book_xhtmls[11].get_content()
```




    b'<?xml version=\'1.0\' encoding=\'utf-8\'?>\n<!DOCTYPE html>\n<html xmlns="http://www.w3.org/1999/xhtml" xmlns:epub="http://www.idpf.org/2007/ops" epub:prefix="z3998: http://www.daisy.org/z3998/2012/vocab/structure/#" lang="en" xml:lang="en">\n  <head/>\n  <body><div class="sinopsis">&#13;\n    <p class="salto10">El hombre se ha dispersado por los planetas de la galaxia. La capital del Imperio es Tr\xc3\xa1ntor, centro de todas las intrigas y s\xc3\xadmbolo de la corrupci\xc3\xb3n imperial. Un psicohistoriador, Hari Seldon, prev\xc3\xa9, gracias a su ciencia fundada en el estudio matem\xc3\xa1tico de los hechos hist\xc3\xb3ricos, el derrumbamiento del Imperio y el retorno a la barbarie por varios milenios. Seldon decide crear dos Fundaciones, situadas en cada extremo de la galaxia, a fin de reducir este periodo de barbarie a mil a\xc3\xb1os.</p>&#13;\n  </div>&#13;\n</body>\n</html>\n'




```python
## Las funciones get_content y get_body_content nos ayudan a extraer 
book_xhtmls[0].get_content()[0:300]
```




    b'<?xml version=\'1.0\' encoding=\'utf-8\'?>\n<!DOCTYPE html>\n<html xmlns="http://www.w3.org/1999/xhtml" xmlns:epub="http://www.idpf.org/2007/ops" epub:prefix="z3998: http://www.daisy.org/z3998/2012/vocab/structure/#" lang="en" xml:lang="en">\n  <head/>\n  <body><h1>El Ciclo de Tr\xc3\xa1ntor</h1>&#13;\n&#13;\n  <p '




```python
## esta solo se trae el body de xhtml
book_xhtmls[0].get_body_content()[0:300]
```




    b'&#13;\n  <h1>El Ciclo de Tr\xc3\xa1ntor</h1>&#13;\n&#13;\n  <p class="asangre">En 1966, en la 24 Convenci\xc3\xb3n Mundial de Ciencia Ficci\xc3\xb3n, celebrada en Cleveland, se otorg\xc3\xb3 el premio \xc2\xabHugo\xc2\xbb<a href="../Text/notas.html#a4" id="a0"><sup>[1]</sup></a> a la mejor \xc2\xabserie de novelas\xc2\xbb del g\xc3\xa9nero a la Trilog\xc3\xada '



El problema que tenemos aqui es que para analizar el texto lo ocuapamos como texto en formato crudo y todas esas tags xhtml hay que extirparlas, una manera rapida de extraer el texto es simular que escrapeamos ese html con BeautifulSoup4


```python
soup = BeautifulSoup(book_xhtmls[0].get_body_content())
```


```python
## ahora si podemos trabajar este texto
soup.get_text()[0:300]
```




    '\nEl Ciclo de Trántor\nEn 1966, en la 24 Convención Mundial de Ciencia Ficción, celebrada en Cleveland, se otorgó el premio Â«HugoÂ»[1] a la mejor Â«serie de novelasÂ» del género a la TrilogÃ­a de las Fundaciones de Isaac Asimov, de la que el presente tÃ­tulo, Fundación, constituye la primera parte. El cita'

#### Obtenemos un string con todo el texto del libro concatenado


```python
## El libro como tal son los elementos del 1 al 5 de documentos dentro del epub
libro_texto =""
for i in range(1,6):
    soup = BeautifulSoup(book_xhtmls[i].get_body_content())
    text_temp = soup.get_text()
    libro_texto = libro_texto+text_temp
```


```python
#Tamanio del texto completo del libro (solo cuerpo)
len(libro_texto)
```




    403112

### Aquí comenzamos a realizar operaciones con Spacy (Tokenización, NER, POStagging, Dependency Parsing etc..)


```python
#Analizar los primeros 5k caracteres
res_nlp = nlp(libro_texto[0:5000])
```

#### POS tagging


```python
# Ver el POStagging de las primeras 20 palabras
i=0
for token in res_nlp:
    if(i==20):
        break
    print(token.text, token.pos_, token.dep_)
    i+=1
```


     SPACE 
    1 NUM nummod


​    
​     SPACE 
​    HARI PROPN nsubj
​    SELDON PROPN ROOT
​    â€” PUNCT punct
​    â€¦ PUNCT punct
​    Nació VERB ccomp
​    el DET det
​    aÃ±o NOUN obl
​    11988 NUM compound
​    de ADP case
​    la DET det
​    Era PROPN nmod
​    Galáctica PROPN flat
​    ; PUNCT punct
​    falleció VERB advcl
​    en ADP case
​    12069 NOUN obl
​    . PUNCT punct

#### NER (Named Entity Recognition)

```python
## ver las entidades que extrae Spacy
for ent in res_nlp.ents:
    if(ent.label_=="PER"):
        print(ent.text, ent.start_char, ent.end_char, ent.label_)
```

    Gaal Dornick 872 884 PER
    Seldon 917 923 PER
    Gaal Dornick 1036 1048 PER
    Trántor 1101 1108 PER
    Gaal 1765 1769 PER
    Hari Seldon 3574 3585 PER
    Trántor 3596 3603 PER
    Proyecto Seldon 3640 3655 PER
    Gaal 3664 3668 PER
    Trántor 3728 3735 PER
    No 3753 3755 PER
    Trántor 4401 4408 PER


Entre las entidades de tipo PER ("persona") si capta nombres como Gaal Dornick y Hari Seldon, pero capta tambien lugares y otros erroneos


```python
## ver las entidades que extrae Spacy
for ent in res_nlp.ents:
    if(ent.label_=="LOC"):
        print(ent.text, ent.start_char, ent.end_char, ent.label_)
```


​    
​     2 4 LOC
​    Helicón 227 234 LOC
​    Arturo 246 252 LOC


​    
​     1023 1025 LOC
​    Galaxia 1517 1524 LOC
​    Galaxia 1620 1627 LOC
​    Trántor 1687 1694 LOC
​    Fueron 1696 1702 LOC
​    Synnax 2027 2033 LOC
​    Galaxia 3011 3018 LOC



```python
#vamos a correr sobre el libro entero NER POsTagging y las demas cosas
# que nos regresa Spacy
res_nlp = nlp(libro_texto)
```


```python
## ver las entidades que extrae Spacy
i=0
for ent in res_nlp.ents:
    if(i==20):
        break
    if(ent.label_=="LOC"):
        print(ent.text, ent.start_char, ent.end_char, ent.label_)
        i=i+1
```


​    
​     2 4 LOC
​    Helicón 227 234 LOC
​    Arturo 246 252 LOC


​    
​     1023 1025 LOC
​    Galaxia 1517 1524 LOC
​    Galaxia 1620 1627 LOC
​    Trántor 1687 1694 LOC
​    Fueron 1696 1702 LOC
​    Synnax 2027 2033 LOC
​    Galaxia 3011 3018 LOC
​    Â¡Aquello 7324 7332 LOC
​    Synnax 7426 7432 LOC
​    ParecÃ­a 7798 7805 LOC
​    Dornick 7837 7844 LOC
​    La 8305 8307 LOC
​    Llegó 8360 8365 LOC
​    Trató 8810 8815 LOC
​    Nómbreme 8923 8931 LOC
​    Brillaba 9213 9221 LOC
​    El billete 9338 9348 LOC


Entre los lugares igualmente capta Galaxia, 
 planeta Trantor, planeta Synnax, planeta Hélicon


```python
# Ver el POStagging de las primeras 20 palabras
i=0
for token in res_nlp:
    if(i==20):
        break
    if token.pos_=="PROPN":
        print(token.text, token.pos_, token.dep_)
        i=i+1
```

    HARI PROPN nsubj
    SELDON PROPN ROOT
    Era PROPN nmod
    Galáctica PROPN flat
    Era PROPN nmod
    Fundacional PROPN flat
    E. PROPN obj
    F. PROPN flat
    Nacido PROPN flat
    Helicón PROPN nmod
    Arturo PROPN nmod
    Indudablemente PROPN flat
    Seldon PROPN nsubj
    Gaal PROPN obj
    Dornick PROPN flat
    Seldon PROPN obj
    Enciclopedia PROPN appos
    Galáctica[2 PROPN flat
    Gaal PROPN nsubj
    Dornick PROPN flat

#### Segmentación de Sentencias

```python
## Sentence splitting (Obtener sentencias)
sents = list(res_nlp.sents)

```


```python
sents[10]
```




    Por aquel entonces, habÃ­a cerca de veinticinco millones de planetas habitados en la Galaxia, y absolutamente todos eran leales al imperio, con sede en Trántor.




```python
sents[2]
```




    Las anécdotas sobre su inteligencia son innumerables, y algunas contradictorias.



### Visualizaciones de Spacy para NER y Dependency Parsing


```python
from spacy import displacy
```


```python
soup = BeautifulSoup(book_xhtmls[0].get_body_content())
res_nlp = nlp(soup.get_text())
```


```python
displacy.render(res_nlp, style='ent', jupyter=True)
```


<div class="entities" style="line-height: 2.5">
<mark class="entity" style="background: #7aecec; padding: 0.45em 0.6em; margin: 0 0.25em; line-height: 1; border-radius: 0.35em; box-decoration-break: clone; -webkit-box-decoration-break: clone">
​    

    <span style="font-size: 0.8em; font-weight: bold; line-height: 1; border-radius: 0.35em; text-transform: uppercase; vertical-align: middle; margin-left: 0.5rem">ORG</span>
</mark>

<mark class="entity" style="background: #ff9561; padding: 0.45em 0.6em; margin: 0 0.25em; line-height: 1; border-radius: 0.35em; box-decoration-break: clone; -webkit-box-decoration-break: clone">
​    El Ciclo
​    <span style="font-size: 0.8em; font-weight: bold; line-height: 1; border-radius: 0.35em; text-transform: uppercase; vertical-align: middle; margin-left: 0.5rem">LOC</span>
</mark>
 de 
<mark class="entity" style="background: #ddd; padding: 0.45em 0.6em; margin: 0 0.25em; line-height: 1; border-radius: 0.35em; box-decoration-break: clone; -webkit-box-decoration-break: clone">
​    Trántor

    <span style="font-size: 0.8em; font-weight: bold; line-height: 1; border-radius: 0.35em; text-transform: uppercase; vertical-align: middle; margin-left: 0.5rem">PER</span>
</mark>
En 1966, en la 24 Convención 
<mark class="entity" style="background: #ddd; padding: 0.45em 0.6em; margin: 0 0.25em; line-height: 1; border-radius: 0.35em; box-decoration-break: clone; -webkit-box-decoration-break: clone">
​    Mundial de Ciencia Ficción
​    <span style="font-size: 0.8em; font-weight: bold; line-height: 1; border-radius: 0.35em; text-transform: uppercase; vertical-align: middle; margin-left: 0.5rem">MISC</span>
</mark>
, celebrada en 
<mark class="entity" style="background: #ff9561; padding: 0.45em 0.6em; margin: 0 0.25em; line-height: 1; border-radius: 0.35em; box-decoration-break: clone; -webkit-box-decoration-break: clone">
​    Cleveland
​    <span style="font-size: 0.8em; font-weight: bold; line-height: 1; border-radius: 0.35em; text-transform: uppercase; vertical-align: middle; margin-left: 0.5rem">LOC</span>
</mark>
, se otorgó el premio Â«HugoÂ»[1] a la mejor Â«serie de novelasÂ» del género a la 
<mark class="entity" style="background: #7aecec; padding: 0.45em 0.6em; margin: 0 0.25em; line-height: 1; border-radius: 0.35em; box-decoration-break: clone; -webkit-box-decoration-break: clone">
​    TrilogÃ­a de las Fundaciones de
​    <span style="font-size: 0.8em; font-weight: bold; line-height: 1; border-radius: 0.35em; text-transform: uppercase; vertical-align: middle; margin-left: 0.5rem">ORG</span>
</mark>

<mark class="entity" style="background: #ddd; padding: 0.45em 0.6em; margin: 0 0.25em; line-height: 1; border-radius: 0.35em; box-decoration-break: clone; -webkit-box-decoration-break: clone">
​    Isaac Asimov
​    <span style="font-size: 0.8em; font-weight: bold; line-height: 1; border-radius: 0.35em; text-transform: uppercase; vertical-align: middle; margin-left: 0.5rem">PER</span>
</mark>
, de la que el presente tÃ­tulo, 
<mark class="entity" style="background: #ddd; padding: 0.45em 0.6em; margin: 0 0.25em; line-height: 1; border-radius: 0.35em; box-decoration-break: clone; -webkit-box-decoration-break: clone">
​    Fundación
​    <span style="font-size: 0.8em; font-weight: bold; line-height: 1; border-radius: 0.35em; text-transform: uppercase; vertical-align: middle; margin-left: 0.5rem">PER</span>
</mark>
, constituye la primera parte. 
<mark class="entity" style="background: #ddd; padding: 0.45em 0.6em; margin: 0 0.25em; line-height: 1; border-radius: 0.35em; box-decoration-break: clone; -webkit-box-decoration-break: clone">
​    El citado premio se estableció
​    <span style="font-size: 0.8em; font-weight: bold; line-height: 1; border-radius: 0.35em; text-transform: uppercase; vertical-align: middle; margin-left: 0.5rem">MISC</span>
</mark>
 por primera vez aquel aÃ±o, y no galardonaba, como los demás Â«
<mark class="entity" style="background: #ddd; padding: 0.45em 0.6em; margin: 0 0.25em; line-height: 1; border-radius: 0.35em; box-decoration-break: clone; -webkit-box-decoration-break: clone">
​    Hugos
​    <span style="font-size: 0.8em; font-weight: bold; line-height: 1; border-radius: 0.35em; text-transform: uppercase; vertical-align: middle; margin-left: 0.5rem">MISC</span>
</mark>
Â», Ãºnicamente el mejor trabajo del aÃ±o en su categorÃ­a (la Â«serie de novelasÂ» no es un fenómeno tan frecuente como para poder establecer un premio anual en esta categorÃ­a), sino la mejor serie de 
<mark class="entity" style="background: #ff9561; padding: 0.45em 0.6em; margin: 0 0.25em; line-height: 1; border-radius: 0.35em; box-decoration-break: clone; -webkit-box-decoration-break: clone">
​    CF
​    <span style="font-size: 0.8em; font-weight: bold; line-height: 1; border-radius: 0.35em; text-transform: uppercase; vertical-align: middle; margin-left: 0.5rem">LOC</span>
</mark>
 hasta entonces escrita.
<mark class="entity" style="background: #7aecec; padding: 0.45em 0.6em; margin: 0 0.25em; line-height: 1; border-radius: 0.35em; box-decoration-break: clone; -webkit-box-decoration-break: clone">
​    

    <span style="font-size: 0.8em; font-weight: bold; line-height: 1; border-radius: 0.35em; text-transform: uppercase; vertical-align: middle; margin-left: 0.5rem">ORG</span>
</mark>
Y de lo que no hay duda es de que se trata de una de las obras más ambiciosas del género en cuanto a planteamiento y amplitud. 
<mark class="entity" style="background: #ddd; padding: 0.45em 0.6em; margin: 0 0.25em; line-height: 1; border-radius: 0.35em; box-decoration-break: clone; -webkit-box-decoration-break: clone">
​    Asimov
​    <span style="font-size: 0.8em; font-weight: bold; line-height: 1; border-radius: 0.35em; text-transform: uppercase; vertical-align: middle; margin-left: 0.5rem">PER</span>
</mark>
 toma como punto de partida de su narración-especulación el comienzo de la decadencia 
<mark class="entity" style="background: #ddd; padding: 0.45em 0.6em; margin: 0 0.25em; line-height: 1; border-radius: 0.35em; box-decoration-break: clone; -webkit-box-decoration-break: clone">
​    â€”en
​    <span style="font-size: 0.8em; font-weight: bold; line-height: 1; border-radius: 0.35em; text-transform: uppercase; vertical-align: middle; margin-left: 0.5rem">PER</span>
</mark>
 un remotÃ­simo futuroâ€” de un colosal imperio galáctico que abarca a toda la humanidad, diseminada por millones de mundos. 
<mark class="entity" style="background: #ff9561; padding: 0.45em 0.6em; margin: 0 0.25em; line-height: 1; border-radius: 0.35em; box-decoration-break: clone; -webkit-box-decoration-break: clone">
​    La capital de este superestado cósmico
​    <span style="font-size: 0.8em; font-weight: bold; line-height: 1; border-radius: 0.35em; text-transform: uppercase; vertical-align: middle; margin-left: 0.5rem">LOC</span>
</mark>
 es 
<mark class="entity" style="background: #ddd; padding: 0.45em 0.6em; margin: 0 0.25em; line-height: 1; border-radius: 0.35em; box-decoration-break: clone; -webkit-box-decoration-break: clone">
​    Trántor
​    <span style="font-size: 0.8em; font-weight: bold; line-height: 1; border-radius: 0.35em; text-transform: uppercase; vertical-align: middle; margin-left: 0.5rem">PER</span>
</mark>
, un planeta Ã­ntegramente destinado a las tareas administrativas, totalmente dependiente de los suministros exterioresâ€¦ y por ello extremadamente vulnerableâ€¦ Un psicólogo y matemático genial prevé el derrumbamiento del 
<mark class="entity" style="background: #ff9561; padding: 0.45em 0.6em; margin: 0 0.25em; line-height: 1; border-radius: 0.35em; box-decoration-break: clone; -webkit-box-decoration-break: clone">
​    Imperio
​    <span style="font-size: 0.8em; font-weight: bold; line-height: 1; border-radius: 0.35em; text-transform: uppercase; vertical-align: middle; margin-left: 0.5rem">LOC</span>
</mark>
 y el subsiguiente caos, y decide emplear la ciencia psicohistórica (una especie de psicologÃ­a de masas matemáticamente estructurada) para reducir al mÃ­nimo el inevitable perÃ­odo de barbarie que antecederá a la consolidación de un 
<mark class="entity" style="background: #ddd; padding: 0.45em 0.6em; margin: 0 0.25em; line-height: 1; border-radius: 0.35em; box-decoration-break: clone; -webkit-box-decoration-break: clone">
​    Segundo Imperio
​    <span style="font-size: 0.8em; font-weight: bold; line-height: 1; border-radius: 0.35em; text-transform: uppercase; vertical-align: middle; margin-left: 0.5rem">PER</span>
</mark>
.
<mark class="entity" style="background: #7aecec; padding: 0.45em 0.6em; margin: 0 0.25em; line-height: 1; border-radius: 0.35em; box-decoration-break: clone; -webkit-box-decoration-break: clone">
​    

    <span style="font-size: 0.8em; font-weight: bold; line-height: 1; border-radius: 0.35em; text-transform: uppercase; vertical-align: middle; margin-left: 0.5rem">ORG</span>
</mark>
Para ello establece dos 
<mark class="entity" style="background: #ddd; padding: 0.45em 0.6em; margin: 0 0.25em; line-height: 1; border-radius: 0.35em; box-decoration-break: clone; -webkit-box-decoration-break: clone">
​    Fundaciones
​    <span style="font-size: 0.8em; font-weight: bold; line-height: 1; border-radius: 0.35em; text-transform: uppercase; vertical-align: middle; margin-left: 0.5rem">PER</span>
</mark>
, una en cada extremo de la 
<mark class="entity" style="background: #ff9561; padding: 0.45em 0.6em; margin: 0 0.25em; line-height: 1; border-radius: 0.35em; box-decoration-break: clone; -webkit-box-decoration-break: clone">
​    Galaxia
​    <span style="font-size: 0.8em; font-weight: bold; line-height: 1; border-radius: 0.35em; text-transform: uppercase; vertical-align: middle; margin-left: 0.5rem">LOC</span>
</mark>
, con el fin de preservar el saber humano. 
<mark class="entity" style="background: #ddd; padding: 0.45em 0.6em; margin: 0 0.25em; line-height: 1; border-radius: 0.35em; box-decoration-break: clone; -webkit-box-decoration-break: clone">
​    A partir de aquÃ­
​    <span style="font-size: 0.8em; font-weight: bold; line-height: 1; border-radius: 0.35em; text-transform: uppercase; vertical-align: middle; margin-left: 0.5rem">MISC</span>
</mark>
, se irán sucediendo diversas épocas 
<mark class="entity" style="background: #ff9561; padding: 0.45em 0.6em; margin: 0 0.25em; line-height: 1; border-radius: 0.35em; box-decoration-break: clone; -webkit-box-decoration-break: clone">
​    â€”cuyo
​    <span style="font-size: 0.8em; font-weight: bold; line-height: 1; border-radius: 0.35em; text-transform: uppercase; vertical-align: middle; margin-left: 0.5rem">LOC</span>
</mark>
 advenimiento vendrá marcado por otras tantas crisisâ€” previstas por la psicohistoria, en las que cambiarán las cabezas visibles del poder y las formas de ejercerlo, pero en las que la 
<mark class="entity" style="background: #ddd; padding: 0.45em 0.6em; margin: 0 0.25em; line-height: 1; border-radius: 0.35em; box-decoration-break: clone; -webkit-box-decoration-break: clone">
​    Primera Fundación
​    <span style="font-size: 0.8em; font-weight: bold; line-height: 1; border-radius: 0.35em; text-transform: uppercase; vertical-align: middle; margin-left: 0.5rem">MISC</span>
</mark>
 (de la segunda no tendremos noticias hasta la Ãºltima parte de la trilogÃ­a) irá expandiendo y afianzando cada vez más su influencia sobre la 
<mark class="entity" style="background: #ff9561; padding: 0.45em 0.6em; margin: 0 0.25em; line-height: 1; border-radius: 0.35em; box-decoration-break: clone; -webkit-box-decoration-break: clone">
​    Galaxia
​    <span style="font-size: 0.8em; font-weight: bold; line-height: 1; border-radius: 0.35em; text-transform: uppercase; vertical-align: middle; margin-left: 0.5rem">LOC</span>
</mark>
.</br>Inspirándose directamente â€”como él mismo ha reconocidoâ€” en la historia de nuestro pasado, 
<mark class="entity" style="background: #ddd; padding: 0.45em 0.6em; margin: 0 0.25em; line-height: 1; border-radius: 0.35em; box-decoration-break: clone; -webkit-box-decoration-break: clone">
​    Asimov
​    <span style="font-size: 0.8em; font-weight: bold; line-height: 1; border-radius: 0.35em; text-transform: uppercase; vertical-align: middle; margin-left: 0.5rem">PER</span>
</mark>
 bosqueja los procesos sociopolÃ­ticos de su futuro hipotético, el paso de una forma de gobierno basada en la religión a una plutocracia más explÃ­cita, o, si se prefiere, del supersticioso 
<mark class="entity" style="background: #ff9561; padding: 0.45em 0.6em; margin: 0 0.25em; line-height: 1; border-radius: 0.35em; box-decoration-break: clone; -webkit-box-decoration-break: clone">
​    Medioevo
​    <span style="font-size: 0.8em; font-weight: bold; line-height: 1; border-radius: 0.35em; text-transform: uppercase; vertical-align: middle; margin-left: 0.5rem">LOC</span>
</mark>
 al 
<mark class="entity" style="background: #ddd; padding: 0.45em 0.6em; margin: 0 0.25em; line-height: 1; border-radius: 0.35em; box-decoration-break: clone; -webkit-box-decoration-break: clone">
​    Renacimiento
​    <span style="font-size: 0.8em; font-weight: bold; line-height: 1; border-radius: 0.35em; text-transform: uppercase; vertical-align: middle; margin-left: 0.5rem">PER</span>
</mark>
, con sus prÃ­ncipes de mercaderes.
<mark class="entity" style="background: #7aecec; padding: 0.45em 0.6em; margin: 0 0.25em; line-height: 1; border-radius: 0.35em; box-decoration-break: clone; -webkit-box-decoration-break: clone">
​    

    <span style="font-size: 0.8em; font-weight: bold; line-height: 1; border-radius: 0.35em; text-transform: uppercase; vertical-align: middle; margin-left: 0.5rem">ORG</span>
</mark>
AsÃ­, en este primer volumen asistimos a las Â«crisis de crecimientoÂ» de la 
<mark class="entity" style="background: #ddd; padding: 0.45em 0.6em; margin: 0 0.25em; line-height: 1; border-radius: 0.35em; box-decoration-break: clone; -webkit-box-decoration-break: clone">
​    Primera Fundación
​    <span style="font-size: 0.8em; font-weight: bold; line-height: 1; border-radius: 0.35em; text-transform: uppercase; vertical-align: middle; margin-left: 0.5rem">MISC</span>
</mark>
, hasta que extiende sus dominios hacia el mismo centro de la 
<mark class="entity" style="background: #ddd; padding: 0.45em 0.6em; margin: 0 0.25em; line-height: 1; border-radius: 0.35em; box-decoration-break: clone; -webkit-box-decoration-break: clone">
​    Galaxiaâ€¦
​    <span style="font-size: 0.8em; font-weight: bold; line-height: 1; border-radius: 0.35em; text-transform: uppercase; vertical-align: middle; margin-left: 0.5rem">MISC</span>
</mark>
, donde, inevitablemente, tropezará con los restos del antiguo 
<mark class="entity" style="background: #ddd; padding: 0.45em 0.6em; margin: 0 0.25em; line-height: 1; border-radius: 0.35em; box-decoration-break: clone; -webkit-box-decoration-break: clone">
​    Imperio
​    <span style="font-size: 0.8em; font-weight: bold; line-height: 1; border-radius: 0.35em; text-transform: uppercase; vertical-align: middle; margin-left: 0.5rem">MISC</span>
</mark>
, desmembrado y en continua decadencia, pero aun asÃ­ fortÃ­simo.
<mark class="entity" style="background: #7aecec; padding: 0.45em 0.6em; margin: 0 0.25em; line-height: 1; border-radius: 0.35em; box-decoration-break: clone; -webkit-box-decoration-break: clone">
​    

    <span style="font-size: 0.8em; font-weight: bold; line-height: 1; border-radius: 0.35em; text-transform: uppercase; vertical-align: middle; margin-left: 0.5rem">ORG</span>
</mark>
Este colosal encuentro cósmico dará lugar a la segunda parte de la trilogÃ­a, 
<mark class="entity" style="background: #ddd; padding: 0.45em 0.6em; margin: 0 0.25em; line-height: 1; border-radius: 0.35em; box-decoration-break: clone; -webkit-box-decoration-break: clone">
​    Fundación
​    <span style="font-size: 0.8em; font-weight: bold; line-height: 1; border-radius: 0.35em; text-transform: uppercase; vertical-align: middle; margin-left: 0.5rem">PER</span>
</mark>
 e 
<mark class="entity" style="background: #ddd; padding: 0.45em 0.6em; margin: 0 0.25em; line-height: 1; border-radius: 0.35em; box-decoration-break: clone; -webkit-box-decoration-break: clone">
​    Imperio
​    <span style="font-size: 0.8em; font-weight: bold; line-height: 1; border-radius: 0.35em; text-transform: uppercase; vertical-align: middle; margin-left: 0.5rem">PER</span>
</mark>
, donde la sÃºbita aparición de un factor imprevisible amenaza con desbaratar el gigantesco y meticuloso plan de los psicohistoriadores. 
<mark class="entity" style="background: #ddd; padding: 0.45em 0.6em; margin: 0 0.25em; line-height: 1; border-radius: 0.35em; box-decoration-break: clone; -webkit-box-decoration-break: clone">
​    Pues dicho elemento perturbador
​    <span style="font-size: 0.8em; font-weight: bold; line-height: 1; border-radius: 0.35em; text-transform: uppercase; vertical-align: middle; margin-left: 0.5rem">MISC</span>
</mark>
 es un mutante, un individuo dotado de extraordinarios poderes mentales y que la psicohistoria no puede integrar en sus cálculos, ya que se trata de un individuo aislado y esta ciencia sólo puede operar sobre la base de grandes masas humanas (del mismo modo que la teorÃ­a cinética de los gases puede predecir el comportamiento global de millones de moléculas, pero no el de una molécula determinada).
<mark class="entity" style="background: #7aecec; padding: 0.45em 0.6em; margin: 0 0.25em; line-height: 1; border-radius: 0.35em; box-decoration-break: clone; -webkit-box-decoration-break: clone">
​    

    <span style="font-size: 0.8em; font-weight: bold; line-height: 1; border-radius: 0.35em; text-transform: uppercase; vertical-align: middle; margin-left: 0.5rem">ORG</span>
</mark>
Entonces entrará en escena la 
<mark class="entity" style="background: #ddd; padding: 0.45em 0.6em; margin: 0 0.25em; line-height: 1; border-radius: 0.35em; box-decoration-break: clone; -webkit-box-decoration-break: clone">
​    Segunda Fundación
​    <span style="font-size: 0.8em; font-weight: bold; line-height: 1; border-radius: 0.35em; text-transform: uppercase; vertical-align: middle; margin-left: 0.5rem">MISC</span>
</mark>
, dando paso a la tercera y Ãºltima parte de la serieâ€¦ Pero no anticipemos los acontecimientos, pues uno de los mayores alicientes de la trilogÃ­a es su tratamiento poco menos que detectivescoâ€¦ Un absorbente relato de intriga montado a una escala gigantesca, tanto espacial como temporal.
<mark class="entity" style="background: #7aecec; padding: 0.45em 0.6em; margin: 0 0.25em; line-height: 1; border-radius: 0.35em; box-decoration-break: clone; -webkit-box-decoration-break: clone">
​    

    <span style="font-size: 0.8em; font-weight: bold; line-height: 1; border-radius: 0.35em; text-transform: uppercase; vertical-align: middle; margin-left: 0.5rem">ORG</span>
</mark>
Cada una de las cinco partes que componen 
<mark class="entity" style="background: #ddd; padding: 0.45em 0.6em; margin: 0 0.25em; line-height: 1; border-radius: 0.35em; box-decoration-break: clone; -webkit-box-decoration-break: clone">
​    Fundación
​    <span style="font-size: 0.8em; font-weight: bold; line-height: 1; border-radius: 0.35em; text-transform: uppercase; vertical-align: middle; margin-left: 0.5rem">PER</span>
</mark>
, asÃ­ como las que integran los otros dos tÃ­tulos de la trilogÃ­a, constituyen un relato autónomo (de hecho, inicialmente fueron publicados en revistas como relatos sueltos), aunque obviamente relacionado con los demás, como las partes de un texto de historia.
<mark class="entity" style="background: #7aecec; padding: 0.45em 0.6em; margin: 0 0.25em; line-height: 1; border-radius: 0.35em; box-decoration-break: clone; -webkit-box-decoration-break: clone">
​    

    <span style="font-size: 0.8em; font-weight: bold; line-height: 1; border-radius: 0.35em; text-transform: uppercase; vertical-align: middle; margin-left: 0.5rem">ORG</span>
</mark>
Del mismo modo, cada uno de los tres volÃºmenes de la trilogÃ­a constituye un todo en sÃ­ mismo, aunque una comprensión completa exige la lectura de toda la obra, y, a ser posible, en el orden indicado, que es el mismo que hemos seguido en su publicación.
<mark class="entity" style="background: #7aecec; padding: 0.45em 0.6em; margin: 0 0.25em; line-height: 1; border-radius: 0.35em; box-decoration-break: clone; -webkit-box-decoration-break: clone">
​    

    <span style="font-size: 0.8em; font-weight: bold; line-height: 1; border-radius: 0.35em; text-transform: uppercase; vertical-align: middle; margin-left: 0.5rem">ORG</span>
</mark>
Por Ãºltimo, por si algÃºn lector se pregunta por qué esta introducción se titula Â«
<mark class="entity" style="background: #ddd; padding: 0.45em 0.6em; margin: 0 0.25em; line-height: 1; border-radius: 0.35em; box-decoration-break: clone; -webkit-box-decoration-break: clone">
​    El ciclo de Trántor
​    <span style="font-size: 0.8em; font-weight: bold; line-height: 1; border-radius: 0.35em; text-transform: uppercase; vertical-align: middle; margin-left: 0.5rem">MISC</span>
</mark>
Â», y no, por ejemplo, Â«
<mark class="entity" style="background: #ddd; padding: 0.45em 0.6em; margin: 0 0.25em; line-height: 1; border-radius: 0.35em; box-decoration-break: clone; -webkit-box-decoration-break: clone">
​    La trilogÃ­a de las Fundaciones
​    <span style="font-size: 0.8em; font-weight: bold; line-height: 1; border-radius: 0.35em; text-transform: uppercase; vertical-align: middle; margin-left: 0.5rem">MISC</span>
</mark>
Â», les aclararé que eso es algo que entenderán perfectamenteâ€¦ en cuanto concluya la serie.
<mark class="entity" style="background: #7aecec; padding: 0.45em 0.6em; margin: 0 0.25em; line-height: 1; border-radius: 0.35em; box-decoration-break: clone; -webkit-box-decoration-break: clone">
​    

    <span style="font-size: 0.8em; font-weight: bold; line-height: 1; border-radius: 0.35em; text-transform: uppercase; vertical-align: middle; margin-left: 0.5rem">ORG</span>
</mark>
CARLO FRABETTI
<mark class="entity" style="background: #7aecec; padding: 0.45em 0.6em; margin: 0 0.25em; line-height: 1; border-radius: 0.35em; box-decoration-break: clone; -webkit-box-decoration-break: clone">
​    

    <span style="font-size: 0.8em; font-weight: bold; line-height: 1; border-radius: 0.35em; text-transform: uppercase; vertical-align: middle; margin-left: 0.5rem">ORG</span>
</mark>
</div>



```python
soup = BeautifulSoup(book_xhtmls[0].get_body_content())
res_nlp = nlp(soup.get_text()[0:500])
```


```python
displacy.serve(res_nlp, style='dep')
```


    [93m    Serving on port 5000...[0m
        Using the 'dep' visualizer


​    
​        Shutting down server on port 5000.



El resultado grafico hay que verlo en el navegador 127.0.0.1:5000
Abajo esta la imagen de un pedazo del grafo

![](https://i.imgur.com/WtYfBjO.png)

###  Referencias

- https://www.crummy.com/software/BeautifulSoup/bs4/doc/
- http://docs.sourcefabric.org/projects/ebooklib/en/latest/tutorial.html
- https://github.com/aerkalov/ebooklib/blob/master/samples/04_markdown_parse/epub2markdown.py
- https://pypi.org/project/EbookLib/
- https://stackoverflow.com/questions/3114786/python-library-to-extract-epub-information
- https://github.com/aerkalov/ebooklib
- https://github.com/ropensci/epubr
