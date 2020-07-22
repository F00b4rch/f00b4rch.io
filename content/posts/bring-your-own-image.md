---
title: "Ma première feature : Bring Your Own Image"
date: 2020-07-20T12:00:00Z
tags: [linux, cloud, baremetal, image, ovh]
draft: false
---

Après quelques mois de travail, recherches, remise en question, la feature est enfin là.

La technologie **Bring Your Own Image** permet de faire booter sur un serveur physique n'importe quelle image cloud (ou non). 

Même si aujourd'hui tous les éditeurs ne sont pas encore prêts à mettre à disposition des images complètement agnostiques (et j'entends par là **UEFI** ready et/ou **legacy boot**) en triturant (**packer**) un peu l'ensemble, on peut arriver à quelque chose de fonctionnel.

C'est un premier pas vers le **IAC hybride** en tout cas.

[La documetation est ici.](https://docs.ovh.com/fr/dedicated/bringyourownimage/)

**TLDR** ~ _du qu'est ce que ça fait vraiment ce nouveau truc_ ~ 

- déploiment par API (+automatisation)
- post-install scripting (+automatisation)
- installation et système transparents (+sécurité)
- personnalisation des templates (+sécurité)
- nova boot custom (+tech)

Un petit warning tout de même sur le côté _bête et méchant_ du procédé, comme dit un peu plus haut, toutes les images ne sont pas encore prêtes à booter automatiquement, il y a de nombreux bug-tracks ouverts chez différents éditeurs à cause de comportements non désirés au **boot time**, **grub config**, **phase d'init** etc. 

Si vous êtes ammené à voir ces erreurs via un **KVM/IPMI**, c'est que l'installation s'est bien passée mais que le point de blocage réside non pas sur la technologie de déploiment en elle même mais sur l'image installée sur disques.

J'ai bon espoir que les éditeurs de distributions tendent à être _server agnostic_, c'est déjà le cas des images proposées par **Canonical**.
