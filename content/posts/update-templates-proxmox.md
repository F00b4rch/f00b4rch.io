---
title: "Update des Templates Proxmox"
date: 2017-07-30T14:53:16Z
tags: [proxmox, templates, linux]
draft: true
---

Pour mettre automatiquement à jour la liste des templates disponibles via prox dans votre espace local il suffit d'utiliser la commande suivante :

    pveam update

J'ai trouvé bon de le mettre en cron une fois toutes les semaines histoire de ne pas avoir de trop grand espaces de versions minor.
