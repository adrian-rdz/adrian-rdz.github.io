title: "Prueba Latex changed"
date: 2017-12-15
tags: [prueba,latex]
excerpt: "Data Science"
mathjax: "true"

##Prueba Latex 

Al final se dan varias opciones y la que vi al principio pues no fue la que termine usando, encontré una mas fácil.

[Solucion usando inyección de codigo](http://academy.ghost.org/adding-mathjax-to-your-ghost-blog/)

Solo fue pegar el CDN, al igual que como todos usamos jquery, es raro que alguien lo aloje en su servidor, por lo general desde un cdn se manda llamar, muchas veces es mas rapido, pero ya depende de gustos porque si llega a morir por un momento el servicio pues no funcionara la librería, pero confio mas en cloudfare que en mi internet.



Formula prueba
$$(H\_0: \mu\_{A} = \mu\_{B})$$

Aproximación de Leibiniz para PI
$$4*\sum_{i=1}^n \frac{(-1^n)}{2n+1}$$

$$( \sum _{n=0}^{\infty }\,{\frac {(-1)^{n}}{2n+1}}\;=\;{\frac {\pi }{4}}.$$

Otras formulas
<p><span class="math inline">\(a^2 + b^2 = c^2\)</span></p>
<p><span class="math inline">\(v(t) = v_0 + \frac{1}{2}at^2\)</span></p>
<p><span class="math inline">\(\gamma = \frac{1}{\sqrt{1 - v^2/c^2}}\)</span></p>
<p><span class="math inline">\(\exists x \forall y (Rxy \equiv Ryx)\)</span></p>
<p><span class="math inline">\(p \wedge q \models p\)</span></p>
<p><span class="math inline">\(\Box\diamond p\equiv\diamond p\)</span></p>
<p><span class="math inline">\(\int_{0}^{1} x dx = \left[ \frac{1}{2}x^2 \right]_{0}^{1} = \frac{1}{2}\)</span></p>
<p><span class="math inline">\(e^x = \sum_{n=0}^\infty \frac{x^n}{n!} = \lim_{n\rightarrow\infty} (1+x/n)^n\)</span></p>
$$ a^2 + b^2 = c^2$$


Formula prueba
`\(H\_0: \mu\_{A} = \mu\_{B}\)`

Aproximación de Leibiniz para PI
`\(4*\sum_{i=1}^n \frac{(-1^n)}{2n+1}\)`

`\( \sum _{n=0}^{\infty }\,{\frac {(-1)^{n}}{2n+1}}\;=\;{\frac {\pi }{4}}.\)`

es muy engorroso, me tomo mucho tiempo para que me funcionara el codigo, hay que agregar escapeos con slash y encerrar la formula entre parentesis sino no la pela la librería.

Referencias:

 - [http://academy.ghost.org/adding-mathjax-to-your-ghost-blog/](http://academy.ghost.org/adding-mathjax-to-your-ghost-blog/)

 - [https://www.mathjax.org/](https://www.mathjax.org/)

 - [http://blog.mollywhite.net/how-to-display-mathematical-equations-in-ghost/](http://blog.mollywhite.net/how-to-display-mathematical-equations-in-ghost/)
