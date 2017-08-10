---
title: "Ajouter Gitlab Registry dans Kubernetes"
date: 2017-08-10T12:06:05Z
tags: [kubernetes, gitlab, registry]
draft: true
---

Maintenant que `Gitlab` propose son propre registry d'images, il est possible de l'utiliser directement dans notre environnement `K8s` !
Si vous avez loupé l'info (qui commence à dater maintenant), je vous renvois à [cet article](https://about.gitlab.com/2016/05/23/gitlab-container-registry/).

Pour ajouter le registry privé Gitlab dans `Kubernetes` il faut créer un `secret` :

    > kubectl create secret docker-registry regsecret --docker-server=registry.gitlab.xyz --docker-username='' --docker-password='' --docker-email=""

Là où :
 
```
--docker-server         regitry gitlab
--docker-username       user gitlab autorisé à acceder au registry
--docker-password       son mot de passe
--docker-email          son email
```

On va vérifier la bonne création du secret :

```
> kubectl get secret regsecret
NAME        TYPE                      DATA      AGE
regsecret   kubernetes.io/dockercfg   1         19h
```

Afficher les détails :

```
> kubectl get secret regsecret --output=yaml
apiVersion: v1
data:
  .dockercfg: eyJiXXXXXXteoirutXXXXetusrnXX=
kind: Secret
metadata:
  creationTimestamp: 2017-08-07T13:32:09Z
  name: regsecret
  namespace: default
  resourceVersion: "2783"
  selfLink: /api/v1/namespaces/default/secrets/regsecret
  uid: cfeXXXb7-7b74-XXX-XXX907-4iu8237492
type: kubernetes.io/dockercfg
```

Il est maintenant possible d'utiliser les images de votre registry directement dans vos `deployment`.

Exemple

```
apiVersion: apps/v1beta1 
kind: Deployment
metadata:
  name: test-registry
spec:
  replicas: 3
  template:
    metadata:
      labels:
        app: my-app
        version: v1
    spec:
      containers:
      - name: my-app
        image: registry.gitlab.xyz/images/my-app:latest
        imagePullPolicy: Always
        ports:
        - containerPort: 80
      imagePullSecrets:
        - name: regsecret
```

Notez le call du `secret` : 

```
imagePullSecrets:
  - name: regsecret
```

Enjoy !
