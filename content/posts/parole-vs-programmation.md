---
title: "Parole Vs Programmation"
date: 2017-07-30T12:51:14Z
tags: [logique, dev]
draft: true
---

On me fait souvent remarquer que j’ai du mal à exprimer une idée « directement ». Généralement, pour un simple positionnement sur un sujet, u$ e multitude d’idées, mots me viennent en tête en même temps et mon cerveau n’arrive pas à faire tout de suite le tri. Cela provoque donc deux choses :  

**– De longues phrases :**  

Elles devraient être courtes mais au fur et à mesure que je prononce mes « mots », d’autres briques viennent s’ajouter pour exprimer une autre directio n etc  
Au final ça ressemble à quelque chose comme ça :  

    for opinion in my_Head :
        print("Mon idée premiè... [deuxième idée] [troisième idée] [condition de la première idée] [développement de la troisième idée]")

**– Le mutisme / frustration**  

Je sais par avance qu’il ne faut pas que je prenne part à une discussion car toutes mes « idées » se valent pour donner une réponse et n’en citer qu’un e me donnera le sentiment d’être « simplet ».  

Au final ça ressemble à :  

    opinion = Sujet
    my_Head = ['idea1', 'idea2', 'idea3', 'idea4', 'idea5', 'idea6']
    for idea in my_Head :
        print(idea)

Je m’exprime donc via morceau de code  

J’ai encore eu l’exemple aujourd’hui d’un client qui n’arrivait pas bien à comprendre le fonctionnement de son serveur face à un problème. J’ai trouvé très compliqué de lui expliquer via un mail le comportement de sa machine, ma réponse ressemblait donc à :  

    line=$(cat postfix.log | grep mail@ndd.com)
    if [ $? -eq 1 ] ; then
        echo -ne "\n========\nPas d'erreur\n========"
    else
        echo -ne "\n========\nErreurs rencontrees\n========"
    cat postfix.log | grep mail.ndd.com | grep --color -i 'error|warning|fail'
    fi
    # exemple de retour :
    #
    # ========
    # Pas d'erreur
    # ========

Pas de longues phrases inutiles, le diagnostique est simple, et surtout, ce qui est extrêmement différent de la parole humaine il n’y a qu’une seule in terprétation.  

Bref, c’était une aparté que je viendrai mettre à jour pour mieux mettre en forme (oui, même la c’est difficile de mettre clairement mon « ressenti »)
