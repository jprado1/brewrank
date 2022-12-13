---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

layout: home
---
Hello,
## Qu'est ce que  c'est:
```Jekyll``` est un generateur de site statique basé sur ```Ruby```utilisé pour créer des sites dans Github Pages. Afin pouvoir éditer cette page/travailler avec ```Jekyll```, il faut:

- [Installer Ruby](https://rubygems.org)
- [Installer Jekyll](https://jekyllrb.com/)
- Installer un manageur d'environnement pour Ruby, Github propose [Bundler](https://bundler.io)
    - Je suis pas très à l'aise avec Ruby encore, mais l'idée principale c'est que c'est un language de webdev où on travaille avec des imports de modules/librairies à la Python. Bundler est pour Ruby ce que Conda est pour Python.
- Une fois ceci fait, vous pouvez telecharger la repo de ce mockup afin de jouer avec, éditer ce site et tester des choses pour notre site finale. [Le lien pour la repo est ici](https://github.com/jprado1/brewrank).
    - Note : il y a deux branchs dans cette repo, j'ai pas mis la branch principale dans "main" pour cacher un peu le site. Il faut push dans "prod" pour que les effets soient visibles pour tout le monde.

- Si vous voulez juste tester des choses en local (dans ce site ou dans un autre que vous créez tous seul.e.s). Il suffit de ```bundle exec jekyll serve```.

- Si vous voulez faire tout du début, les guides que j'ai suivi sont [ici](https://docs.github.com/en/pages) et [ici](https://jekyllrb.com/). 
# Le site est très moche...
- En effet, mais il on peux changer très aisement le thème du site (à la Wordpress). Comment le faire vient juste après, mais vous pouvez voir des designs [ici](https://jekyllrb.com/docs/themes/)
# Points positifs de Jekyll
- En gros, Jekyll permet de créer des sites sans savoir rien de HTML, tout est fait en markdown et yaml. C'est peut être plus simple d'expliquer en personne, mais tout site a un fichier ```_config.yml``` qui a les configs de base du site (titre, URL, theme, plugins, socials...) et des fichiers .md ou .markdown qui gardent le vrai contenu. Tout le contenu personalisé de cette page se trouve dans ``ìndex.markdown```. 
- Les thèmes permettent pas mal de personalisation (fonts, tailles, couleurs, format en général) mais je ne sais pas encore comment ça marche. 
# Points négatifs
-blablabla
- Il faut s'y mettre pour apprendre lol...
# Test
{% include test_adv_usa.html %}
# FAQ : comment faire les choses qu'on a besoin de faire

- Rajouter une image (ou gif) : 
![](/images/test_gif_beer.gif){:class="img-responsive"}
- Le code est:
```![test](/images/test_gif_beer.gif){:class="img-responsive"}```
- Quoi d'autre...? Demandez moi et je cherche
