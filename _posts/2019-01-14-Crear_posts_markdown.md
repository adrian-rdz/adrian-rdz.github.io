---
author: Adrián Rodríguez
title: Crear post Markdown y presentación de resultados
date: 2019-01-14
tags: [Markdown,LaTeX,resultados,blog,posts,tools,herramientas]
excerpt: "Herramientas para crear entradas en blog, para generar resultados, para crear papers o articulos, etc.."
mathjax: "true"
---

## Introducción

Que herramientas elegir para la creación de un post o contenido que crearas para un blog puede ser decisivo para ahorrar tiempo a la hora de crear entradas, en este post descrbiré las diferentes formas en las que genero archivos de resultados o reportes y ademas que herramientas uso para crear los posts de este blog.

Una parte importante de la ciencia de datos y de cualquier actividad ingenieril o cientifica es presentar resultados de manera que sea facil de compartir y de manera que ayude a hacer reproducibles los resultados logrados, dado que una parte importante del proceso de investigación es lograr comunicar adecuadamente los resultados.

Quizas la formas mas extendida de reportar resultados o generar contenido en el ámbito científico ya sea papers o publicaciones, sea mediante el uso del lenguaje LaTeX con el cual se pueden generar reportes y papers de gran calidad, pero ultimamente han ganado algo de terreno al menos en lo que respecta  a publicaciones cortas o la generación de articulos por vía electronica el usar lenguajes simplificados como Markdown, ejemplos de ello es que muchos sistemas de blogging como Wordpress ahora permite generar comentarios y contenido usando este lenguaje, ademas la pagina github la cual permite hacer versionado de software usando git, tambien admite para docuemntar usar Markdown.

## Markdown, Latex etc...

Es importante notar que con markdown puro no podemos lograr lo que en latex, como el escribir ecuaciones matematicas y simbolos, y también notar que usar LaTex puro para algun reporte sencillo quizas sea algo engorroso y tardado. Por esta razón si planeas hacer notas simples o documentación simple yo recomendaría usar Markdown y si requieres publicar algo mas serio y profesional usar LaTeX. 

Aunque que mejor que mezclar ambos lenguajes y obtener lo mejor de ambos, en github se permite realizar esto uno puede incluir ecuaciones en latex y en sistemas de blogging también se permite hacer anexando una libreria javascript que interpreta el codigo latex **mathjax**.



En el caso de los estadisticos y programadores de R, tienen la oportunidad de usar Rmarkdown el cual igualemente permite mezclar Markdown, Latex y ademas permite incrustar codigo R que se ejecute para mostrar resultados de una manera sencilla y rapida.

## Herramientas de Software para crear contenido

Entre las herramientas y software disponible para generar contenido existe software privativo y software open-source, algunas herramientas son mas ventajosas sobre otras dependiendo lo que estes desarrollando o publicando

Hé aquí un listado de los que uso o he usado en alguna ocasión:

- **Rstudio (usando Latex y pandoc)**

- **jupyter-notebooks (Usando pandoc y latex para exportar notebooks a markdown, html o pdf)**

- **Overleaf** (Sitio web que permite realizar documentos,presentaciones y papers y lo mejor es que permite que se haga de manera colaborativa, es extremadamente útil)

- **Typora**

- **Mark Text**

- **Atom o Sublime Text o otro editor de Codigo o Texto (generar codigo markdown directamente en codigo)**

- **pandoc**

### Ejemplos de Herramientas en uso

Ejemplo de un reporte **jupyter-notebook** (No se muestra pero uno puede anexar documentacion latex y agregar imagenes estáticas etc...)

![Imgur](https://i.imgur.com/ufeme4s.png)

Ejemplo de un reporte en **Rmarkdown** (En este ejemplo se crea una presentacion tipo latex y lo interesante es que uno puede crear contenido en la salida del reporte la cual la genere algun calculo o codigo en **R**, asimismo se puede agregar plots creados en R a la salida)

![Imgur](https://i.imgur.com/ao7N9gh.png)

Ahora muestro como se ve en **Marktext** cuando editamos y vemos la salida al vuelo (lado derecho)) o si queremos trabajar directo en codigo lado-izquierdo de la image

![Imgur](https://i.imgur.com/Zkq36Lv.png)



### Lo que uso para este blog

Particularmente para generar las entradas de este blog de github, he estado usando normalmente **Typora**, el cual es excelente mas no es opensource, y ahora investigando me acabo de dar cuenta de **Mark Text** el cual es practicamente un alternativa opensource de Typora, y por lo cual quiero comenzar  usar este último.

En menor cantidad para los post he hecho el codigo sobre jupyter-notebooks y exportado a Markdown la notebook, y otros pocos los he hecho con Rstudio usando el lenguaje Rmarkdown el cual no es mas que markdown pero permite ingresar formulas latex y lo mejor es que tambien puedes meter codigo R y el cual al interpretarse con R se genera la salida, y por lo mismo puedes generar reportes muy guay con poco esfuerzo y tiempo



## Sugerencias

Otras formas de generar contenido o reportes es usando LaTex puro, una herramienta y mas que herramienta una plataforma web que te permite generar reportes o crear papers en latex es **Overleaf** , la recomiendo ampliamente te será mucho mas facil realizar tus reportes ahi a generarte un ambiente completo con todas las librerias de latex necesarias

Dependiendo que es lo que vayas a realizar puede servirte mas una herramienta que otra, pero mi sugerencia es si estas analizando un coonjunto de datos o aplicando clasfiicación o alguna técnica de machine learning sobre algun dataset y estas trabajando en R lo mas facil para generarte tu reporte de resultados es Rmarkdown y si esta usando python pues lo mas directo es con Juypyter-notebooks con lo cual sobre la misma notebook documentar tu codigo y exportar tu notebook a un markdown o html.

Si te encuentras en los casos previos descritos pero requieres hacer un reporte mas profesional o mas customizado quizas lo mas indicado sea que exportes tus resultados y hagas un reporte con latex puro para lograr una mayor flexiblidad a la hora de fomatear tu documento.

Y  si solo quieres crear una entrada en un blog yo recomendaría usar un editor de textos simple o si quieres hacer las cosas mas visualemente y ver como va quedando  tu reporte pues usar Typora o Marktext.

## Referencias

- https://marktext.github.io/website/

- https://github.com/marktext/marktext

- https://typora.io/

- https://rmarkdown.rstudio.com/

- https://pandoc.org/

- https://es.overleaf.com/

- https://jupyter.org/

- https://www.latex-project.org/
