Crear entradas para nuestro sitio github en Rmarkdown
================

Crear entradas para nuestro stio web en Rmarkdown
-------------------------------------------------

Al entrar a R crear un nuevo doocumento File--&gt;New File--&gt;Rmarkdown

![Tipo](https://i.imgur.com/L2eJN3r.png)

Despues ya podemos editar nuestro codigo markdown y agregar codigo R

![Ejemplo](https://i.imgur.com/r4eo2AD.png)

Para poner imagenes en las entradas cargare las imagenes a mi cuenta de imgur, para no tener que saturar github y ademas estar teniendo que subir las iamgenes.

GitHub Documents
----------------

Rmarkdown da la posibilidad de crear entradas para github usando codigo markdown y embebiendo la salida de comandos de R, y ademas generar imagenes de por ejemplo plots

Ejemplo de codigo en R
----------------------

You can include R code in the document as follows:

``` r
summary(cars)
```

    ##      speed           dist       
    ##  Min.   : 4.0   Min.   :  2.00  
    ##  1st Qu.:12.0   1st Qu.: 26.00  
    ##  Median :15.0   Median : 36.00  
    ##  Mean   :15.4   Mean   : 42.98  
    ##  3rd Qu.:19.0   3rd Qu.: 56.00  
    ##  Max.   :25.0   Max.   :120.00

Including Plots
---------------

Para embeber plots

``` r
plot(pressure)
```

![](https://raw.githubusercontent.com/adrian-rdz/adrian-rdz.github.io/master/_posts/2017-12-16-Crear-entradas-github-en-Rmarkdown_files/figure-markdown_github-ascii_identifiers/pressure-1.png)
usando echo=FALSE podemos omitir el mostrar el codigo de R y solo mostrar la salida
