---
title: "Stackoverflow depuis un Terminal"
date: 2017-07-30T12:47:32Z
tags: [cli, stackoverflow, linux]
draft: false
---

En traînant sur le net (comme souvent d’ailleurs) je suis tombé sur cette petite pépite pour les geeks flemmards comme moi… qui ne souhaitent pas ouvrir une interface graphique de type navigateur pour ne pas être ébloui par la lumière générée.  

Comme je traîne très (trop) souvent sur Stackoverflow je suis tombé par hasard sur [ce github](https://github.com/santinic/how2) .  

**L’installation est courte :**

    npm install -g how2

Et le principe génial !  

Exemple :

    # /usr/local/bin/how2 while true
    shell - Is using "while true" to keep a script alive a good idea?
    That depends on how fast the perl script returns. If it returns quickly, you might want to insert a small pause between executions to avoid CPU load, $
    g:   
    while true
       do
         /someperlscript.pl
         sleep 1
       done
    This will also prevent a CPU hog if the script is not found or crashes immediately.
    The loop might also better be implemented in the perl script itself to avoid these issues.
    Edit:
    As you wrote the loop only purpose is to restart the perl script should it crashes, a better approach

