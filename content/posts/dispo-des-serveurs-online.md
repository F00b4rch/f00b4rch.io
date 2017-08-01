---
title: "Dispo des serveurs Online"
date: 2017-08-01T09:40:07Z
tags: [bash, online, serveur, linux]
draft: false
---

On a besoin de serveurs chez Online, mais il n'y a pas de dispo !
Donc on est venu me demander si je n'avais pas de solution magique...

Un peu de `bash`... un `notifier` en l'occurence `Slack` et c'est parti !  
Comme d'habitude [les sources sont disponibles](https://github.com/F00b4rch/OnlineSrvDispo).

#### En mode cochon 

*Pour être alerté via slack il faut créer un [incoming-webhook](https://my.slack.com/services/new/incoming-webhook/) qui génèrera un lien.*

Pour un serveur de gamme XC 2016 :


    text="DISPO : https://www.online.net/fr/serveur-dedie/dedibox-xc"; json="{\"channel\": \"#infra\", \"text\": \"$text\"}" ; while true ; do curl --silent https://www.online.net/fr/serveur-dedie  | grep '<button class="btn btn--primary js-order-dedibox"' | grep -i 'xc 2016' | grep -i 'victime' || curl -s -d "payload=$json" "https://hooks.slack.com/services/XXX/XXXX/XXXX/XXXX" ; sleep 5 ; done 

Le curl ne s'effectuera que si le serveur est disponible.

#### En mode propre 

En ajoutant un peu de formatage au code :
```
#!/bin/bash

webhook="https://hooks.slack.com/services/XXX/XXXX/XXXX/XXXX"
url="https://www.online.net/fr/serveur-dedie/XXXXX"
text="DISPO : $url"
channel="test"
json="{\"channel\": \"#$channel\", \"text\": \"$text\"}"
server="XC 2016"

while true
    do curl --silent https://www.online.net/fr/serveur-dedie | \
    grep '<button class="btn btn--primary js-order-dedibox"' | \
    grep -i "$server" | grep -i 'victimeeu' || \
    curl -s -d "payload=$json" "$webhook" ; \
    sleep 5 ; \
done
```

On peut également pousser le vice plus loin en filtrant sur la **région** et les **types de disques**.

#### Toujours en mode cochon

Parce que... voilà

```
text="DISPO https://www.online.net/fr/serveur-dedie/dedibox-xc"; json="{\"channel\": \"#test\", \"text\": \"$text\"}" ; while true ; do curl --silent https://www.online.net/fr/serveur-dedie/dedibox-xc | egrep -io '<option value="\w+">ssd / france / dc2</option>' && curl -s -d "payload=$json" "https://hooks.slack.com/services/XXX/XXXX/XXXX" ; sleep 5 ; done
```

Enfin on pourrait aussi simuler l'achat... Suite au prochain épisode peut-être (?)
