---
title: Proyecto PLN para datos de noticias de México (Parte 3) Web-Scrapping
date: 2019-01-06
author: Adrián Rodríguez
tags: [web-scrapping,scrapping,html,javascript,http,cron,python]
excerpt: "Proceso de Web-Scrapping aplicado a proyecto NLP para noticias de México"
mathjax: "true"
---

## Introducción

Para el proyecto de NLP para noticias de México evaluamos como obtener la información y llegamos a la conclusion de usar web-scrapping y asi obtener la información de los sitios que seleccionamos, dado que unos no contaban con otra manera de extraer dicha información

### ¿Que es web-scrapping?

Web scrapping es obtener contenido crudo de sitios web, dado que al obtener información de sitios web directo de su fuente se vienen etiquetas html y demas información que requerimos descartar para ciertos propositos, es necesario de alguna manera parsear lo que se obtiene para llegar a obtener solo el texto que requerimos

Existen sitios que son faciles de escrapear y otros que no dado que contienen información dinamica (que se genera al interactuar con la misma (**AJAX**))

Hay sitios web que ya de por si tienen la manera de acceder información de una mejor forma; por ejemplo usando una API o usando un lector de RSS; pero no todos lo sitios cuentan con estas ventajas y para el caso de RSS no siempre se disponde del texto completo de los articulo.

### Librerías en R y Python

Existen librerías en los distintos lenguajes de programación, aqui solo hablare de R y Python,

R

- Rvest
- RCrawler

Python

- BeautifulSoap
- urllib
- Selenium

En R solo he probado un poco Rvest y es muy conveniente para scrapear sitios web, en algunos casos incluso puedes escrapear tablas html a data frames  casi directamente, en el caso de python he probado esas 3 que pongo pero existen mas.

En el caso de toparse con la necesidad de scrapear un sitio web que contiene información dinamica la mejor forma que encontre en internet de lograr hacerlo fue usando Selenium, ya que emulas un navegador y asi es posible captar contenido que se genera por un cierto evento o al vuelo; segun vi también se usa para web-testing esa librería, en el caso de R no vi como hacerlo pero seguramente tienen un wrapper para Selenium o algo similar.

## Aplicar Web-Scrapping a sitios de noticias

Para realizar el trabajo se genero una base de datos con noticias de 38 periodicos de varios estados del país que conforman una colección hasta el día de hoy (11-Dic-2018) de 10,937 noticias, divididas en tres secciones:

- Local
- Policiaca
- Opinion (se subdivide en nivel de importancia local y nacional)

Se seleccion una cadena de periodicos que cubría gran parte de la republica mexicana a nivel de noticas de importancia local, y se seleccion solo usar 3 secciones a nivel local (loca,opinion,policiaca) y ademas se seleccion otros sitios de noticias locales fuera del grupo OEM y tambien unos noticieros de nivel de importancia nacional para analizar la seccion de opnion.

![Imgur](https://i.imgur.com/c9ptmBs.png)

El numero de noticias locales recogidas por cada estado hasta el 11 de diciembre se puede resumir en el siguiente choroplet

![Imgur](https://i.imgur.com/0OJz8GA.png)



### Proceso

El proceso de web-scrapping requirió realizar un analisis de la estructura html de los sitios web's y evaluar que tan factible era extraer la información relevante y poder automatizar dicho proceso.

La elección de un grupo de noticias locales tiene el objetivo de con un solo codigo poder escrapear varios sittios de distintos estados aprovechando que comparten gran parte de la estrutctura HTML.

### Automatización

Ya una vez programados los escrapeadores el siguiente paso es poder dejar automatizado el scrapeo con una cierta periodcidad en este caso fue periodicidad diaria, para lograr la automtización primero hay que validar que todos los scripts funcionen correctamente, despues hay que planear cual sera el orden y cuanto tiempo entre llamada sera suficiente para que no se produzca errores; despues de hacer pruebas se procedió a programar un bash-script en el cual se realiza todo el proceso y se asignan tiempos de timeout entre tareas.

Un extracto del bash script para extrar las ligas con selenium es asi:

```bash
#bash script
#!/bin/sh

### Encender la instancia
docker start seleniumdocker

## esperar que levante internamente sus servicios
sleep 10 

docker exec seleniumdocker sh -c 'python /usr/workspace/scrap_urls_selenium_chrome_parametrizado.py --link https://www.elsoldehidalgo.com.mx/ --destino hidalgo_hidalgo' 2>> /home/tuzy-server/log_urls.txt && echo "Finalizado exitosamente hidalgo_hidalgo_scrapped a las $(date)" >> /home/tuzy-server/log_urls.txt || echo "Error al scrapear ligas hidalgo_hidalgo_scrapped.txt a las $(date)" >> /home/tuzy-server/log_urls.txt
echo "termino hidalgo_hidalgo"

sleep 100
docker stop seleniumdocker
docker start seleniumdocker 
sleep 10 

docker exec seleniumdocker sh -c 'python /usr/workspace/scrap_urls_selenium_chrome_parametrizado.py --link https://www.elsoldetulancingo.com.mx/ --destino tulancingo_hidalgo' 2>> /home/tuzy-server/log_urls.txt && echo "Finalizado exitosamente tulancingo_hidalgo_scrapped.txt a las $(date)" >> /home/tuzy-server/log_urls.txt || echo "Error al scrapear ligas tulancingo_hidalgo_scrapped.txt a las $(date)" >> /home/tuzy-server/log_urls.txt
echo "termino tulancingo hidalgo"
docker stop seleniumdocker

```

Un extracto del codigo para escrapear el contenido de las ligas obtenidas en el día es como sigue:

```bash
#!/bin/sh

### Tulancingo
docker exec anacondajup_new sh -c 'TZ=America/Monterrey && export TZ && python /opt/notebook/scrap_selenium_pol_loc.py --archivo /opt/notebook/tulancingo_hidalgo_scrapped.txt' 2>> /home/tuzy-server/prueba_error.txt && echo "Finalizado exitosamente tulancingo_hidalgo_scrapped.txt a las $(date)" >> /home/tuzy-server/prueba_error.txt || echo "Error al scrapear tulancingo_hidalgo_scrapped.txt a las $(date)" >> /home/tuzy-server/prueba_error.txt

sleep 25

### Bajio
docker exec anacondajup_new sh -c 'TZ=America/Monterrey && export TZ && python /opt/notebook/scrap_selenium_pol_loc.py --archivo /opt/notebook/bajio_scrapped.txt' 2>> /home/tuzy-server/prueba_error.txt && echo "Finalizado exitosamente bajio_scrapped.txt a las $(date)" >> /home/tuzy-server/prueba_error.txt || echo "Error al scrapear bajio_scrapped.txt a las $(date)" >> /home/tuzy-server/prueba_error.txt

sleep 25

### Centro Aguascalientes
docker exec anacondajup_new sh -c 'TZ=America/Monterrey && export TZ && python /opt/notebook/scrap_selenium_pol_loc.py --archivo /opt/notebook/centro_aguascalientes_scrapped.txt' 2>> /home/tuzy-server/prueba_error.txt && echo "Finalizado exitosamente centro_aguascalientes_scrapped.txt a las $(date)" >> /home/tuzy-server/prueba_error.txt || echo "Error al scrapear centro_aguascalientes_scrapped.txt a las $(date)" >> /home/tuzy-server/prueba_error.txt

sleep 25

```

La calendarizción en el cron queda algo similar a esto

```bash
# Pegar cron jobs
50 09 * * * /usr/bin/docker exec anacondajup_new sh /opt/notebook/scrapping_misc.sh
00 12 * * * sh /home/tuzy-server/script_oem_urls.sh
35 14 * * * sh /home/tuzy-server/script_oem.sh
```

Codigo python para scrapear los enlaces de las 3 secciones de interes con selenium:

Cabe notar que el codigo para las 3 secciones se puede iterar sobre las secciones, dado que es practicamente lo mismo por seccion.

```python
from selenium import webdriver
from selenium.common.exceptions import NoSuchElementException
import os
import time
import argparse

parser = argparse.ArgumentParser()

parser.add_argument("-l", "--link", type=str, required=True)
parser.add_argument("-d", "--destino", type=str, required=True)

args = parser.parse_args()

### Para los demas similares solo cambiar estos 2 parametros
link_raiz=args.link
archivo_destino=args.destino

#### Policiaca

url = link_raiz+"policiaca/"

chrome_options = webdriver.ChromeOptions()
chrome_options.add_argument('--no-sandbox')
chrome_options.add_argument('--headless')
chrome_options.add_argument('--disable-gpu')
driver = webdriver.Chrome(chrome_options=chrome_options)
driver.implicitly_wait(15)

driver.get(url)
time.sleep(20)
#browser.page_source

panel_lateral=driver.find_element_by_class_name("lazy-on-demand")

res=driver.find_element_by_class_name("hard-news-2")
res_a=res.find_elements_by_tag_name("a")

links=set()
for i in range(0,len(res_a)):
    links.add(res_a[i].get_attribute("href"))

res=driver.find_element_by_class_name("hard-news-1")
res_a=res.find_elements_by_tag_name("a")

for i in range(0,len(res_a)):
    links.add(res_a[i].get_attribute("href"))


links=links.difference({None,link_raiz,link_raiz+'policiaca/',link_raiz+'analisis/',link_raiz+'local/'})

file = open("/usr/workspace/"+archivo_destino+"_scrapped.txt","w")
for i in links:
    if("/autor/" in i):
        print(i+"Descartado")
    else:
        file.write(i+'\n')

file.close()

```

codigo para scrapear cerca de 30 sitios del mismo grupo (se parametriza con argumentos):

```python
#Diarios de OEM secciones local y policiaca
from bs4 import BeautifulSoup
from urllib.request import Request, urlopen
import re
from scraputils import *           #Funcion para cambiar el formato de la fecha
from pgdbqueries import send_data_to_db  # module to connect data base
from lugarNoticia import lugarDefault
import datetime
import time
import argparse

parser = argparse.ArgumentParser()
parser.add_argument("-a", "--archivo", type=str, required=True)
args = parser.parse_args()
archivo=args.archivo

now=datetime.datetime.now() 
news_paper_name=""
file = open(archivo, "r")

for link_noticia in file.readlines():
	site= link_noticia
	hdr = {'User-Agent': 'Mozilla/5.0'}
	req = Request(site,headers=hdr)
	data = urlopen(req)

	### Nombre del periodico
	news_paper_name = site[str.find(site,".")+1:][:str.find(site[str.find(site,".")+1:],".")]
	importance_level="local"

	### obtener section_name
	if("/local/" in site):
		site_raiz = site[0:str.find(site,"/local/")+len("/local/")]
		section_name="local"
	elif("/policiaca/" in site):
		site_raiz = site[0:str.find(site,"/policiaca/")+len("/policiaca/")]
		section_name="policia"
	else:
		site_raiz = site[0:str.find(site,"/analisis/")+len("/analisis/")]
		section_name="opinion"

	soup=BeautifulSoup(data,"html5lib")
	if(soup.find('p',attrs={'class','published-date'})!=None):
		fecha=soup.find('p',attrs={'class','published-date'}).get_text() #Consigue la fecha	
		fecha=re.sub("/ ","",fecha)
		fecha=fecha[fecha.find(re.findall(r'\d+',fecha)[0]):]   #################3Arreglar fecha
                ### obtener fecha en formato dd/mm/yyyy
		fecha_aux=[int(s) for s in str.split(fecha) if s.isdigit()]
		dia=fecha_aux[0]
		anio=fecha_aux[1]
		m=['enero','febrero','marzo','abril','mayo','junio','julio','agosto','septiembre','octubre','noviembre','diciembre']

		for i in m:
			if(i in fecha.lower()):
				mes=i
				break
		fecha_final=""+str(dia).zfill(2)+"/"+str(mes)+"/"+str(anio)
		print(fecha_final)
		fecha_final=str_to_date(fecha_final)
		title=soup.find('title')
		title=re.sub('<[^>]+>', '', str(title))
		periodico=title[title.find(" - ")+3:] #Nombre del periodico
		title=title[:title.find(" - ")] #Consigue el titulo ya en el formato deseado
		a=soup.find("div", {"id", re.compile(r"^content-*")}).find_all('p')
		a=re.sub(str(soup.find('p', {"class","kicker"})),"",str(a))
		for i in soup.find("div", {"id", re.compile(r"^content-*")}).find_all('a'):
			a=str(a).replace(str(i),"")
		a=str(a).replace("<p></p>","\n\n")
		a=str(a).replace("<br/><br/>","\n\n")
		a=re.sub('<[^>]+>', '', a)
		a=re.sub('\.,','.',a)
		a=re.sub(' , ','',a)
		a=re.sub('\n,', "\n",a) #Quita las comas raras
		a=a.replace("  ","")


		print(site)
		print(news_paper_name)
		print(section_name)
		print(importance_level)
		print(fecha_final)

		a=a[1:(len(a)-1)]
		a=a.strip()
			## Insertar en base de datos
		lugar_reportado =lugarDefault(news_paper_name)

		if(re.search("\, [A-Z]",a[0:30]) != None):
			lugar_reportado=a[0:re.search("\, [A-Z]",a).end()-3]

		send_data_to_db(news_title = title, scrapping_date = datetime.datetime.now(), 
				section_name = section_name, 
				news_content = a, importance_level = importance_level,
				news_link = site, 
				newspaper_name = news_paper_name, news_name_place_reported = lugar_reportado.lower(),
				news_date_reported = fecha_final)  # send data

print("acabo"+ news_paper_name)
file.close()

```

Para este ultimo codigo aun no hago uso de una librería para manejar las fechas que programo mi compañero Kenny, por lo cual hay un mejurque por ahí, pero ya pronto modificaré esa parte, para estandarizarlo.

Desde mi punto de vista mi compañero Kenny que automatizo un subconjunto de los sitios hizo un mejor trabajo al dejar la llamada de un bash script internamente en el container con lo cual se evito algunos hardcodeos en el bashscript, que en mi caso no logre evitar, y por lo cual para este momento quedo así, pero en algun punto lo estandarizare a la otra forma que el tiene.

### Diagrama

Simplificadamente el proceso de scrapeo se muestra en este diagrama (se muestra en cada carril el container en el que se hace dicho proceso):

![Imgur](https://i.imgur.com/XKAstvb.png)

Proceso que queda automatizado diariamente en el cron.

## Consideraciones

### Timezone

Cuanod se manda extrarer o scrapear y cuando se va almacenar el contenido en algunos casos estuvimos teniendo problemas y ya despues de debugear nos dimos cuenta, que el problema era  que el container no tenia asociado el timezone correcto, por lo cual en las llamadas de los bashscripts y al ejecutar comandos de manera desatendida era necesario generar la variable de entorno que seteara el timezone correcto a nuestra región, para que las fechas y zona horaria se ajustara apropiadamente.

### Contenido dinamico (elementos generados al vuelo con javascript y AJAX)

Como ya comente el hecho de que sitios web tengan contenido dinamico que quieras extraer agrega un problema extra, dado que las librerías convencionales no logran extraer ese contenido crudo por lo cual nuestra solución fue usar Selenium para estos casos.

### Containers

Como ver contenido entre containers y como hacer para que un container sea visible para otro es todo un tema, uno puede realizar redes en docker, para este caso dado que se exponian puertos del container, entonces podiamos acceder a los servicios como si estuviesen en el host, por ejemplo la base de datos en postgress. Pero cabe remarcar que hay que buscar cuales son las mejores practicas para realizar este tipo de tareas.

## Nota

Este post puede extenderse mas, tratare de ir agregando mas, por algun lado tenía un notebook de python con algunas estadisticas descrptivas relativas a esta sección.