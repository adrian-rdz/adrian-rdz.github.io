---
title: "Prueba Latex changed"
date: 2017-12-15
tags: [prueba,latex]
excerpt: "Data Science"
mathjax: "true"
---

Como se explica en el video siguiente https://www.youtube.com/watch?v=qWrcgHwSG8M, se puede usar codigo latex facilmente con este template solo agregando la libreria mathjax

Solo hay que agregar este codigo en un archivo de configuracion para que siempre se precargue la libreria

```javascript
{% if page.mathjax %}
<script type="text/javascript" async
  src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-MML-AM_CHTML">
</script>
{% endif %}
```

### Pruebas Latex


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



Referencias:

 - [http://academy.ghost.org/adding-mathjax-to-your-ghost-blog/](http://academy.ghost.org/adding-mathjax-to-your-ghost-blog/)

 - [https://www.mathjax.org/](https://www.mathjax.org/)

 - [http://blog.mollywhite.net/how-to-display-mathematical-equations-in-ghost/](http://blog.mollywhite.net/how-to-display-mathematical-equations-in-ghost/)
