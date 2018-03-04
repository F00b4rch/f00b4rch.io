---
title: "Ubuntu Vrack Ovh Fix"
date: 2018-03-04T14:41:25Z
draft: false
---

Vous l'avez peut-être remarqué, mais lorsque vous faîtes poper une instance PCI OVH sous Ubuntu en activant le Vrack, votre Vm ne dispose pas de son IP privée au boot.
Alors, oui, je n'aime pas Ubuntu, mais parfois on n'a pas le choix.

Breeef, tout ça pour dire qu'on a pas notre IP privée et c'est trop triste. (RT)

Le truc pour fix est con.
Très con.

Ajoutez :
```
allow-hotplug ens4
iface ens4 inet dhcp
```
Dans le fichier `/etc/network/interface`.

Ensuite :
```
systemctl restart network
```
et
```
ifup ens4
```

C'est tout.

J'ai fais un message sur la ML [cloud] d'OVH, parce que j'ai tout de même trouvé ça étrange que ce bug existe.

C'était le 21/11/2017.

```
Hello la team,

Juste une petite remarque sur l'installation d'un Ubuntu PCI avec Vrack.
En fait je suis obligé d'ajouter :

allow-hotplug ens4
iface ens4 inet dhcp

Dans /etc/network/interface puis

systemctl restart network
et
ifup ens4

Il me semble que sur les autres distrib' les confs network s'ajoutent automatiquement à l'install non ?


Je passe peut-être à côté d'un truc... en même temps je ne connais pas bien les systèmes en .deb...

Bises à tout le monde

F00b4rch
```

La réponse : 
```
21/11/2017
	
À cloud
Hello,
Exact, sur debian 9 (voir 8), les interfaces sont montée automatiquement...
Je crois qu'il a un bug d'ouvert cote ubuntu pour ça, si je le retrouve je te l'envoi.
```

Devinez si c'est fix depuis…
