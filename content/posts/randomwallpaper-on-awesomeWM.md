---
title: "Fond d'écran aléatoire avec AwesomeWM !"
date: 2020-01-10T11:10:33Z
draft: False
tags: [awesomewm, linux, wallpaper, random]
---

J'ai cherché le moyen de casser un peu ma routine lorsque je bosse sur mon laptop. Je me suis dit que changer de fond d'écran de manière aléatoire et automatique était un bon moyen de casser un peu cette monotonie propre à mon outil de travail.

J'utilise awesomeWM (dans sa version 4)
```
[f00b@void ~]$ awesome --version
awesome v4.3 (Too long)
 • Compiled against Lua 5.3.5 (running with Lua 5.3)
 • D-Bus support: ✔
 • execinfo support: ✘
 • xcb-randr version: 1.6
 • LGI version: 0.9.2
```

Il me fallait donc trois choses:
 
- Un dossier contenant plein d'images
- Un petit script qui va en choisir une au hasard
- Un call à ce script depuis l'init d'awesome

Pour ce qui est des images, j'utilise [l'excellent repo](https://github.com/LukeSmithxyz/wallpapers) de **Luck Smith**.

Pour ce qui est du script, rien de bien méchant :
```
#!/bin/bash
# author : F00b4rch
# descr : Make your wallpaper change on each start !
#
# I'm using Luck Smith wallpaper git repo for all images
# link : https://github.com/LukeSmithxyz/wallpapers

# current awesome theme
THEME="powerarrow-dark"

# image should have absolute path to image folder
IMAGE=$(find $HOME/Pictures/wallpapers/ -type f | shuf -n 1 | sed 's/\ /\\ /g') && cp -f $IMAGE $HOME/.config/awesome/themes/$THEME/wall.png

# don't forget to add those lines at the end of your rc.lua (replace with your correct path and script name)
#
# -- Startup programs
# awful.util.spawn_with_shell("~/bin/wallpaper.sh")
```

Et donc comme indiqué en commentaire, il suffit d'ajouter ces deux lignes (ou juste une sans le commentaire) pour call le script via le chargement d'awesomeWM.

A mettre donc à la fin de votre `~/.config/awesome/rc.lua`.
```
-- Startup programs
awful.util.spawn_with_shell("~/bin/wallpaper.sh")
```

À chaque redémarrage d'awesome, vous trouverez un nouveau joli (ou pas) fond d'écran.

[Le script est dispo via gist !](https://gist.github.com/F00b4rch/785796c5e4e37a4d759f0f8c89c41f0e)
