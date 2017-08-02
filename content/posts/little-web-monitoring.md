---
title: "Little Web Monitoring"
date: 2017-08-02T08:22:16Z
tags: [script, python, monitoring]
draft: false
---

Parfois, faire des petits services `OneJob` est efficace en terme de temps, organisation, fonctionnalités.
Dans l'exemple de cet article, je vais essayer de montrer en quoi il peut être pertinent d'écrire soit-même des `micros-scripts` sous ce modèle.

On va dire que l'on souhaite s'assurer, pour une liste de site donnée, que ces derniers retournent tous des codes `200` en moins de `2s`, auquel cas, on recevra une notification sur `Slack`.
Les sources de ce posts sont disponibles [ici](https://github.com/F00b4rch/Slack-Bot-Web-Alert/blob/master/bot.py).

Pour le langage, `python3` sera largement suffisant.

```
#!/bin/python3.6
#
# Dev : F00b4rch

import requests
import time
from slackclient import SlackClient

slack_token = "YOUR_TOKEN_HERE"
sc = SlackClient(slack_token)

urls=['site1', 'site2', 'site3']

while True :

    for i in urls :
        r = requests.head(i)
        t = requests.get(i, timeout=5).elapsed.total_seconds()
        # print(i, r.status_code, t)
        time.sleep(2)

        if r.status_code != 200 :
            sc.api_call(
            "chat.postMessage",
            channel="#infra",
            text=("ALERTE !", i, r.status_code)
            )

        if t > 2 :
            sc.api_call(
            "chat.postMessage",
            channel="#infra",
            text=("LoadSite too long : ", i, r.status_code)
            )

    time.sleep(10)
```

Petite analyse rapide :

On utilise : 

* `requests` pour pouvoir call les sites/url.
* `time` pour paused un peu le code.
* `SlackClient` pour être notifié.

Au final avec seulement 30-35 lignes de codes, on est capable de s'assurer **rapidement** du bon fonctionnement d'une liste de sites.
Ca prend moins de 10minutes à coder, c'est `rapide`, `léger` et `transportable` partout.

L'avantage de ce type de scripts, c'est qu'ils sont très modulaires, l'ajout de fonctionnalités est tout aussi rapide que l'élaboration du code et on s'assure qu'on obtient uniquement les informations dont on a besoin, ce qui est un réel avantage en terme de performances. 
