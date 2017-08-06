---
title: "Dockeriser le client Nova"
date: 2017-08-06T16:37:07Z
draft: true
---

Avec le `Public Cloud` OVH, il vous est possible de piloter vos instances **OpenStack** directement depuis le client `Nova`. 

C'est plutôt pratique de passer par la ligne de commande plutôt que de devoir accéder au manager qui est souvent, disons-le, lent.

Mais pour éviter de devoir à chaque fois préparer un environnement de travail compatible avec `Nova` (ref [Comment préparer son environnement pour utiliser l'api openstack](https://www.ovh.com/fr/publiccloud/guides/g1851.preparer_lenvironnement_pour_utiliser_lapi_openstack) + [Charger les variables d'environnement openstack](https://www.ovh.com/fr/publiccloud/guides/g1852.charger_les_variables_denvironnement_openstack) ), j'ai trouvé plus intéressant de directement créer une **image Docker** prévue à cet effet.

De cette manière, plus besoin d'installer quoique ce soit, si ce n'est le `docker daemon` sur son poste.

Le DockerFile ressemble donc à :

```
FROM debian:latest

MAINTAINER f00b4rch@gmail.com

RUN apt-get update && apt-get install python-glanceclient python-novaclient -y

env OS_AUTH_URL=""
env OS_TENANT_ID=""
env OS_TENANT_NAME=""
env OS_USERNAME=""
env OS_PASSWORD=""
env OS_REGION_NAME=""
```

Notez que j'utilise l'image de base `debian` parce que je l'ai déjà en local, mais vous pouvez très bien remplacer l'os par celui que vous souhaitez et adapter le contenu du `RUN`.

Il faut également remplir les variables d'environnement avec les informations relatives à votre `env OpenStack`. Je vous renvois pour celà au [guide](https://www.ovh.com/fr/publiccloud/guides/g1852.charger_les_variables_denvironnement_openstack) prévu à cet effet.

Un coup de :
    
    sudo docker build -t clientNova .

Puis il suffit de le lancer votre conteneur comme suivant :

    sudo docker run --rm -it novaClient bash

Vous pouvez ajouter l'alias suivant dans votre `.bashrc` / `.zshrc`

    alias nova="sudo docker run --rm -it novaClient bash"

Comme d'habitude, les [sources sont disponibles](https://github.com/F00b4rch/SandBox/tree/master/Docker/OpenStack/novaClient) !

Enjoy !
