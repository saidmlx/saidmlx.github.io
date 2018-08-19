---
layout: post
title:  "Welcome to Jekyll!"
date:   2000-01-01 21:11:15 -0500
categories: jekyll update
---
You’ll find this post in your `_posts` directory. Go ahead and edit it and re-build the site to see your changes. You can rebuild the site in many different ways, but the most common way is to run `jekyll serve`, which launches a web server and auto-regenerates your site when a file is updated.

To add new posts, simply add a file in the `_posts` directory that follows the convention `YYYY-MM-DD-name-of-post.ext` and includes the necessary front matter. Take a look at the source for this post to get an idea about how it works.

Jekyll also offers powerful support for code snippets:

{% highlight ruby %}
def print_hi(name)
  puts "Hi, #{name}"
end
print_hi('Tom')
#=> prints 'Hi, Tom' to STDOUT.
{% endhighlight %}

Check out the [Jekyll docs][jekyll-docs] for more info on how to get the most out of Jekyll. File all bugs/feature requests at [Jekyll’s GitHub repo][jekyll-gh]. If you have questions, you can ask them on [Jekyll Talk][jekyll-talk].

[jekyll-docs]: https://jekyllrb.com/docs/home
[jekyll-gh]:   https://github.com/jekyll/jekyll
[jekyll-talk]: https://talk.jekyllrb.com/





## Como utilizar [SASS](http://sass-lang.com/)

primero creamos una carpeta _sass dentro de nuestra carpeta raiz y dentro de ello ponemos todos nuestros archivos SASS

## Que son los temas y como aplicarlos

Jekyll trea diferentes temas

## Trabajando con drafts

Si eres como yo que aveces empieza a escribir posts y no los termina, la mejor forma de administrarlos es a travez de los drafts, de esta forma los veras de forma local pero no desplegados.

Para hacer lo anterior lo hacemos en dos pasos 

1. Dentro de la raiz creamos una carpeta con el nombre de _drafts
2. para poder ver estos borradores  de forma local lanzamos el server de la siguiente forma
```terminal
$ bundle exec jekyll serve --drafts
```

Y listo de esta forma podemos trabajar con borradores.







