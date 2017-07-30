---
title: "Notifications Finch"
date: 2017-07-30T12:42:17Z
draft: true
tags: [finch, linux, xmpp, notification]
---

Travaillant sur plusieurs espaces de travail/bureaux j'aime avoir des notify-send lorsque je reçois un message sur Finch.  

Il suffit de créer un petit script **.finchnotifications.sh** comme suivant dans votre /home/votreutilisateur :  

    #!/bin/sh
    latest_line=`find /home/utilisateur/.purple/logs/facebook/utilisateur/* -mtime -1 -printf "%T@ %Tx %TX %p" | sort -n -r | head | cut -d ' ' -f 2- | awk '{print $NF}' | head -1 | xargs tail -1 | sed -e 's#<[^>]*>##g'`mplayer $1 >/dev/null 2>&1 ¬ify-send "`echo $latest_line | cut -d ':' -f 3 | awk -F ')' '{print $2}'`" "`echo $latest_line | cut -d ':' -f 4-`"

Rendez-le exécutable :  

    chmod +x .finchnotifications.sh

Il faudra adapter les paramètres à votre configuration !  

Dans ce script d’exemple, j’ai adapté l’affichage pour mon utilisation avec Finch sur le proto Facebook.  

**Activation**  

Dans finch faîtes un ALT + a, sélectionnez SON puis :  

Méthode : Commande  
Commande pour le son : /home/votreutilisateur/.finchnotifications.sh  
Toujours activer le son  
Sélectionnez ce qui vous intéresse dans la liste  

C’est terminé !

