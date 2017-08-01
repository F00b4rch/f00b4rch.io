---
title: "Galera Monitoring"
date: 2017-07-30T15:08:41Z
tags: [galera, monitoring, golang, dev]
draft: false
---

Monitorer une base de données en standalone c'est une chose, mais dès qu'on parle clustering, c'est tout de suite un peu plus complexe.

C'est le cas avec le Galera clustering (mariaDB/mysql). Zabbix (&co) m'offrait des solutions simples à mettre en place pour du single database server, mais je n'ai pas trouvé de template réellement intéressant pour le monitoring d'un cluster Galera pour la production.

Plusieurs intérrogations donc, qu'est ce qu'il faut monitorer, comment alerter, quelle est la meilleure méthode ?

Je me suis basé sur [la documentation officielle de Galera](http://galeracluster.com/documentation-webpages/monitoringthecluster.html) pour disposer de tous les éléments importants à surveiller.
Pour le choix du langage Golang m'a semblé m'apporter les fonctionnalités nécessaires.
Quant au choix de l'alerting, j'ai décidé d'utiliser une App Slack.

Vous pouvez trouver les [sources de mon projet ici](https://github.com/F00b4rch/GaleraMonitoring).

Exemple d'un output sain :

```
> go run main.go

### Version ####
2017/06/16 14:19:48 Serveur n1 - version 5.6.35-1xenial
2017/06/16 14:19:48 Serveur n2 - version 5.6.35-1xenial
2017/06/16 14:19:48 Serveur n3 - version 5.6.35-1xenial
### UUID ###
2017/06/16 14:19:48 n1 a1c404a9-51b4-11e7-b057-237cc5970d38
2017/06/16 14:19:48 n2 a1c404a9-51b4-11e7-b057-237cc5970d38
2017/06/16 14:19:48 n3 a1c404a9-51b4-11e7-b057-237cc5970d38
### Nodes ###
2017/06/16 14:19:48 Total Nodes : 3
2017/06/16 14:19:48 Number of Nodes counts : 3
2017/06/16 14:19:48 Number of Nodes counts : 3
2017/06/16 14:19:48 Number of Nodes counts : 3
### STATUS ###
2017/06/16 14:19:48 n1 status : Primary
2017/06/16 14:19:48 n2 status : Primary
2017/06/16 14:19:48 n3 status : Primary
2017/06/16 14:19:48 n1 is ready : [ON]
2017/06/16 14:19:48 n2 is ready : [ON]
2017/06/16 14:19:48 n3 is ready : [ON]
2017/06/16 14:19:48 n1 is connected : [ON]
2017/06/16 14:19:48 n2 is connected : [ON]
2017/06/16 14:19:48 n3 is connected : [ON]
### Average Replication ###
2017/06/16 14:19:48 Average on n2 : 0.000000
2017/06/16 14:19:48 Average on n3 : 0.000000
2017/06/16 14:19:48 Average on n1 : 0.100000
```

Il faut utiliser le package `slackApp` dans lequel j'ai placé une fonction exposée `PayloadSlack()` pour ensuite personnaliser vos alertes.

L'ensemble de l'output n'est pas affiché dans le **STDOUT** lorsque ce dernier est sain. Néanmoins il suffit de décommenter les println dans le `main.go` pour pouvoir effectuer vos tests.
