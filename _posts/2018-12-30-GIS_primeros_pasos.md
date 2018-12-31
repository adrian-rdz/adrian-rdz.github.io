---
title: GIS primeros pasos
date: 2018-12-30
author: Adrián Rodríguez
tags: [GIS, geospacial, estadística, visualizaciones, mapas]
excerpt: "Introducción a GIS, analisis espaciales (estadistica espacial) y al uso de datos geospaciales"
mathjax: "true"
---

## Introducción

Los temas de Sistemas de información Geografica, Estadistica Espacial, son temas que he tenido un acercamiento introductorio, pero que me gustaría poco a poco ir aprendiendo mas, por lo cual en este post es un acercamiento introductorio a estos temas, orientado a personas que tengan cero conocimiento de estos temas o que quieran ver como adentrarse un poco a estos temas.

### ¿Que es GIS?

Por sus siglas en Ingles se refiere a Sistemas de información Geograficos, *segun la wikipedia es un conjunto de herramientas que integra y relaciona diversos componentes (usuarios, hardware, software,
procesos) que permiten la organización, almacenamiento, manipulación, 
análisis y modelización de grandes cantidades de datos procedentes del 
mundo real que están vinculados a una referencia espacial, facilitando 
la incorporación de aspectos sociales-culturales, económicos y 
ambientales que conducen a la toma de decisiones de una manera más 
eficaz*

![](https://i.imgur.com/lwtqeUS.png)

Existen sistemas y software tanto privativol como opensource, ademas algunos lenguajes de programación se pueden adaptar con librerías para dar la funcion GIS 

### Software

![](https://i.imgur.com/atwcnUO.jpg)

#### Sistemas comerciales:

- ARCGIS
- Geomedia
- Mapinfo
- ManifoldGIS
- etc....

#### Sistemas opensource

- QGIS
- GRASS
- Kosmo
- TerraView
- gvSIG
- etc....

A los opensource se les puede añadir el usar lenguajes de programación (python, R) haciendo uso de librerías opensource para GIS

#### Librerías GIS en R

- sp
- sf
- rgdal
- raster
- leaflet
- mapbox plotly
- maptools

#### Librerías GIS en python

- geopandas (manipuacion de datos espaciales)
- folium (visualizacion wrapper de leaflet en python)
- rasterio
- matplotlib (visualizaciones)
- pyproj
- rasterstats

### ¿Que es la Estadística Espacial?

Analisis Espacial o Estadistica espacial incluye cualquiera de las técnicas formales que estudian entidades usando sus propiedades topologicas, geometricas o geograficas. El analisis espacial usa técnicas muy variadas de las cuales algunas aun estan en temprana etapa de desarrollo, y ademas cubre diversos enfoques y campos de estudio como astronomía, fabricación chips, geografía etc.

El analisis geoespacial es un subconjunto del analisis espacial el cual se enfoca a aplicar en analisis estadistico y otras técnicas analiticas a datos de tipo geografico. Tales analisis incluyen el uso de sistemas  de software capaces de desplegar mapas y procesar datos espaciales incluyendo GIS y geomatica

<!-- **Geospatial analysis**, or just **spatial analysis**,[[32\]](https://en.wikipedia.org/wiki/Spatial_analysis#cite_note-32) is an approach to applying [statistical analysis](https://en.wikipedia.org/wiki/Statistical_analysis) and other analytic techniques to data which has a geographical or spatial aspect [[33\]](https://en.wikipedia.org/wiki/Spatial_analysis#cite_note-33). Such analysis would typically employ software capable of rendering maps processing spatial data, and applying [analytical](https://en.wikipedia.org/wiki/Scientific_modelling) methods to terrestrial or [geographic](https://en.wikipedia.org/wiki/Geographic) datasets, including the use of [geographic information systems](https://en.wikipedia.org/wiki/Geographic_information_system) and [geomatics](https://en.wikipedia.org/wiki/Geomatics) -->

#### Algunas tipos de técnicas y métodos en estadistica espacial

- Autocorrelacion espacial
- Interpolacion espacial
- Regresión espacial
- Interacción espacial
- Simulación y modelado
- Analisis geespacial

### Donde conseguir información geospacial

En la actualidad se genera mucha información taggeada geospacialmente, es posible que en algunas apis puedas encontrar información georeferenciada, tambien los metadatos a veces revelan coordenadas por ejemplo los metadatos de las imagenes, y hay sitios especializados que brindan información geoespacial o de estudios geospaciales como en INEGI.

[http://gaia.inegi.org.mx](http://gaia.inegi.org.mx)

Para méxico es posible encotrar informacion georeferenciada de negocios y ubicaciones de organizaciones en la pagina del ingei y tambien en el geoportal de la conabio

[http://www.conabio.gob.mx/informacion/gis/](http://www.conabio.gob.mx/informacion/gis/)

En el portal de la conabio se puede encontrar datos geospaciales y mapas en formato SHP, KML, WMS, de los poligonos de las divisiones polticas del país (Estatal, Municipal, AGEB's etc.) tambien se puede encontrar datos de indicadores georeferenciados, entre otras cosas.

![](https://i.imgur.com/dkLmxXO.png)

### Algunas Aplicaciones

#### Visualizar Shape Files en QGIS

1. Abrimos QGIS
2. En el menu Capa --> Añadir Capa --> Capa Vectorial
3. Seleccionamos el archivo SHP (En este caso es la division geopolitica de mexico en muncipios)
4. Veremos algo asi![](https://i.imgur.com/no8ija8.png)
5. Despues si queremos visualizar alguna propiedad como un texto podemos hacerlo 
   1. Damos click derecho sobre la capa ![](https://i.imgur.com/k3Egfdt.png)
   2. Despues elegimos propiedades y en el pane izquierdo elegimos mostrar etiquetas y seleccionamos Mostrar etiquetas para esta capa y en el combo **Etiquetar con** elegimos la etiqueta que viene en el shape file yo pondre el nombre del municiṕio ![](https://i.imgur.com/fs1f8GH.png)
   3. El resultado ![](https://i.imgur.com/J0hqw8c.png)

Este es un pequeño ejemplo pero se puede analizar multiples capas para un mapa, y pintar los poligonos en base a alguna metrica y mas cosas, ademas QGIS se puede integrar con R y con python (con python por default)

#### Ejemplos lectura de archivos geospaciales en R y python (SHP, geojson)

El ejemplo basico que se mostro de QGIS es facilmente replicable usando R o python haciendo uso de librerias GIS, eso sera los siguientes ejemplos.

#### R

```R
rm(list=ls())
##Instalar librerias para mapas
#install.packages("rgdal")
#install.packages("maptools")
#install.packages("rgeos")
library(rgeos)
library(rgdal)
library(maptools)
library(sp)

wd<-"/home/rstudio/Descargas/shp_muni/muni_2016gw"
setwd(wd) 
dsn<-getwd()
#Read OGR vector maps into Spatial objects
ogrInfo(dsn=dsn,layer="muni_2016gw") ## descirbir los layers del shp file
prov.ogr<-readOGR(dsn=dsn,layer="muni_2016gw")
summary(prov.ogr)
### Leemos los poligonos
prov<-readShapePoly("muni_2016gw")  
# Graficar con sp
spplot(prov ,c("CVE_ENT"),scales=list(draw=TRUE,cex=0.85))
```

```R
## El shapfile tiene informacion guardada al leerlo obtenemos un spatialdataframe
> colnames(prov.ogr@data)
 [1] "OBJECTID"  "CVE_ENT"   "NOM_ENT"   "CVE_MUN"   "NOM_MUN"   "CVEGEO"   
 [7] "AREA"      "PERIMETER" "COV_"      "COV_ID"   
```

![](https://i.imgur.com/hnULLlU.png)

En R se puede hacer mapas interactivos usando leaflet y se puede hacer dashboards usando shiny en conjunto con leaflet para lograr interesantes dashboards.

#### python

En python existe la libreria geopandas (en ella puedes trabajar directamente con los shp files) pero es un embrollo lograr instalarla, igualmente algunas librerías en python usan mas comunmente otros tipo de datos como geojson y para manipular la informacion la puedes manipular como json

```
import folium
print (folium.__version__)

mexico_map = folium.Map(location=[19.432608, -99.133209],tiles='Mapbox Bright', zoom_start=5)
mexico_map

## Se crea un choroplet con los datos del json (En este caso todo se colorea uniforme)
mexico_map.choropleth(geo_data='mexico.json',fill_color='Orange',)
#kanton_map.choropleth(geo_path='switzerland.geojson')

## Se agregar un marcador a la localizacion especificada
feature_group = folium.FeatureGroup("Locations")
feature_group.add_child(folium.Marker(location=[19.432608, -99.133209], popup=str("hola"))) 
mexico_map.add_child(feature_group)


mexico_map
```

![](https://i.imgur.com/ovMJDsX.png)

El geojson mexico.json para este ejemplo tiene datos a nivel estado

#### Ejemplos manipulaciones de data frames espaciales en R

Con la libreria sp en R podemos manejar los datos geoespaciales  y manipularlos como dataframes en este caso los llamaremos data frames espaciales

```R
## subsetear un data frame espacial
> prov_nuevo_leon<-prov[prov$NOM_ENT=="Nuevo León",]
## Listar los elementos de un atributo del dataframe
> prov_nuevo_leon$NOM_MUN
 [1] Montemorelos             Cerralvo                 Lampazos de Naranjo     
 [4] Vallecillo               Linares                  Doctor Coss             
 [7] Anáhuac                  Los Aldamas              Iturbide                
[10] China                    Doctor González          General Terán           
[13] San Pedro Garza García   General Escobedo         Monterrey               
[16] Parás                    Melchor Ocampo           Villaldama              
[19] Los Ramones              García                   Santa Catarina          
[22] Hualahuises              General Zaragoza         Doctor Arroyo           
[25] General Zuazua           Pesquería                Cadereyta Jiménez       
[28] Marín                    Ciénega de Flores        Mina                    
[31] San Nicolás de los Garza Sabinas Hidalgo          Higueras                
[34] El Carmen                Abasolo                  Hidalgo                 
[37] Galeana                  General Treviño          Bustamante              
[40] Agualeguas               Mier y Noriega           Allende                 
[43] Juárez                   Aramberri                Santiago                
[46] Los Herreras             General Bravo            Rayones                 
[49] Apodaca                  Guadalupe                Salinas Victoria        
2318 Levels: Abalá Abasolo Abejones Acacoyagua Acajete Acala ... Zumpango
## Ver un summary de los atributos del dataframe espacial
> summary(prov)
Object of class SpatialPolygonsDataFrame
Coordinates:
        min       max
x -118.3651 -86.71041
y   14.5321  32.71865
Is projected: NA 
proj4string : [NA]
Data attributes:
    OBJECTID         CVE_ENT                                NOM_ENT    
 Min.   :   1.0   20     : 570   Oaxaca                         : 570  
 1st Qu.: 615.2   21     : 217   Puebla                         : 217  
 Median :1229.5   30     : 212   Veracruz de Ignacio de la Llave: 212  
 Mean   :1229.5   14     : 125   Jalisco                        : 125  
 3rd Qu.:1843.8   15     : 125   México                         : 125  
 Max.   :2458.0   07     : 118   Chiapas                        : 118  
                  (Other):1091   (Other)                        :1091  
    CVE_MUN                NOM_MUN         CVEGEO          AREA        
 002    :  32   Benito Juárez  :   7   01001  :   1   Min.   :    221  
 003    :  32   Ocampo         :   6   01002  :   1   1st Qu.:   8582  
 001    :  31   Emiliano Zapata:   5   01003  :   1   Median :  23223  
 004    :  31   Hidalgo        :   5   01004  :   1   Mean   :  79581  
 005    :  31   Juárez         :   5   01005  :   1   3rd Qu.:  66179  
 008    :  31   Morelos        :   5   01006  :   1   Max.   :5323784  
 (Other):2270   (Other)        :2425   (Other):2452                    
   PERIMETER             COV_            COV_ID      
 Min.   :   6.337   Min.   :   0.0   Min.   :   1.0  
 1st Qu.:  56.477   1st Qu.: 614.2   1st Qu.: 615.2  
 Median : 104.346   Median :1228.5   Median :1229.5  
 Mean   : 146.436   Mean   :1228.5   Mean   :1229.5  
 3rd Qu.: 184.047   3rd Qu.:1842.8   3rd Qu.:1843.8  
 Max.   :2173.729   Max.   :2457.0   Max.   :2458.0  
                                                     
> class(prov)
[1] "SpatialPolygonsDataFrame"
attr(,"package")
[1] "sp"


```

Podemos plotear los subset que hagamos por ejemplo plotear el estado de nuevo leon y sus municipios

```R
spplot(prov_nuevo_leon ,c("AREA"),scales=list(draw=TRUE,cex=0.85))
```

![](https://i.imgur.com/wE8FHhS.png)

#### Ejemplos manipulaciones de data frames espaciales en python

Para datos tipo geojson que al final son json podemos hacer uso de la libreria json para manejarlos un pequeño ejemplo

```
import json

with open('mexico.json') as f:
    data = json.load(f)

mexico_map = folium.Map(location=[19.432608, -99.133209],tiles='Mapbox Bright', zoom_start=5)
geo_json_data = json.load(open("mexico.json"))
folium.GeoJson(geo_json_data,
              style_function=lambda feature: {
        'fillColor': '#ffff00',
        'color': 'black',
        'weight': 2,
        'dashArray': '5, 5'
    }).add_to(mexico_map)

feature_group = folium.FeatureGroup("Locations")
# Iterar los elementos caracteristica del json
for feature in data['features']:
    if feature["properties"]["name"] != "":
        feature_group.add_child(folium.Marker(location=[feature["properties"]["latitude"], feature["properties"]["longitude"]], popup=feature["properties"]["name"])) 

## Adherir al mapa los marcadaores
mexico_map.add_child(feature_group)

mexico_map
```

![](/home/adrianrdzv/.config/Typora/typora-user-images/1546218910977.png)

##Conclusiones

Lo mostrado es una probadita de cosas que se pueden hacer, usando herramientas y lenguajes opensource, pero se puede hacer analisis mas interesantes y extendidos.

En otro proximo post mostrare como usar Choropolets para mostrar intensidades de una metrica, y como mezclar información externa con shp files y geojson (Hacer joins con cierta clave u objeto geospacial) y ademas mostrar visualizaciones de leaflet.



## Referencias

- http://www.data-analysis-in-python.org/t_gis.html
- http://darribas.org/gds15/notes/Class_01.html
- http://darribas.org/gds15/content/labs/lab_03.pdf
- https://en.wikipedia.org/wiki/Spatial_analysis
- https://mappinggis.com/2012/09/aplicaciones-gis-open-source/
- https://gisgeography.com/commercial-gis-software/
- https://medium.com/@chrieke/essential-geospatial-python-libraries-5d82fcc38731
- http://www.conabio.gob.mx/informacion/gis/
- https://github.com/rocker-org/geospatial
- https://github.com/python-visualization/folium

