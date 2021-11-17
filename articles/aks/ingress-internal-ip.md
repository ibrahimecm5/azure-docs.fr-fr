---
title: Contrôleur d’entrée sur le réseau interne
titleSuffix: Azure Kubernetes Service
description: Découvrez comment installer et configurer un contrôleur d’entrée NGINX pour un réseau privé interne dans un cluster Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 04/23/2021
ms.openlocfilehash: e33daa41f0ddae73e58d77b89350c46023010d71
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132554101"
---
# <a name="create-an-ingress-controller-to-an-internal-virtual-network-in-azure-kubernetes-service-aks"></a>Créer un contrôleur d’entrée pour un réseau virtuel interne dans Azure Kubernetes Service (AKS)

Un contrôleur d’entrée est un logiciel qui fournit un proxy inversé, un routage du trafic configurable et un arrêt TLS pour les services Kubernetes. Des ressources d’entrée Kubernetes sont utilisées pour configurer les règles d’entrée et les itinéraires des services Kubernetes individuels. En utilisant un contrôleur d’entrée et des règles d’entrée, une seule adresse IP peut être utilisée pour router le trafic vers plusieurs services dans un cluster Kubernetes.

Cet article montre comment déployer le [contrôleur d’entrée NGINX][nginx-ingress] dans un cluster Azure Kubernetes Service (AKS). Le contrôleur d’entrée est configuré sur un réseau virtuel privé interne et une adresse IP. Aucun accès externe n’est autorisé. Plusieurs applications sont ensuite exécutées dans le cluster AKS, chacune étant accessible via l’adresse IP unique.

Vous pouvez également :

- [Créer un contrôleur d’entrée dans Azure Kubernetes Service (AKS)][aks-ingress-basic]
- [Routage d’applications HTTP][aks-http-app-routing]
- [Créer un contrôleur d’entrée qui utilise vos propres certificats TLS][aks-ingress-own-tls]
- Créer un contrôleur d’entrée qui utilise Let’s Encrypt pour générer automatiquement des certificats TLS [avec une adresse IP publique dynamique][aks-ingress-tls] ou [avec une adresse IP publique statique][aks-ingress-static-tls]

## <a name="before-you-begin"></a>Avant de commencer

Cet article utilise [Helm 3][helm] pour installer le contrôleur d’entrée Nginx sur une [version prise en charge de Kubernetes][aks-supported versions]. Assurez-vous que vous utilisez la version la plus récente de Helm et que vous avez accès au référentiel Helm *ingress-nginx*. Les étapes décrites dans cet article peuvent ne pas être compatibles avec les versions précédentes du Helm Chart, NGINX Ingres Controller ou Kubernetes. Pour plus d’informations sur la configuration et l’utilisation de Helm, consultez [Installer des applications avec Helm dans Azure Kubernetes Service (AKS)][use-helm].

Pour les besoins de cet article, vous devez également exécuter Azure CLI version 2.0.64 ou ultérieure. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, voir [Installer Azure CLI][azure-cli-install].

De plus, cet article suppose que vous disposez d’un cluster AKS avec un [ACR intégré][aks-integrated-acr].

## <a name="import-the-images-used-by-the-helm-chart-into-your-acr"></a>Importer les images utilisées par le graphique Helm dans votre ACR

Souvent, lors de l’utilisation d’un cluster AKS avec un réseau privé, il est nécessaire de gérer la provenance des images conteneur utilisées au sein du cluster. Consultez [Meilleures pratiques relatives à la gestion et à la sécurisation des images de conteneur dans Azure Kubernetes Services (AKS)][aks-container-best-practices] pour plus d’informations.  Pour prendre en charge cette exigence, et pour des conditions d’exhaustivité, les exemples de cet article s’appuient sur l’importation des trois images de conteneur utilisées par le [graphique NGINX Ingress Controller Helm][ingress-nginx-helm-chart] dans votre ACR.

Utilisez `az acr import` pour importer ces images dans votre ACR.

```azurecli
REGISTRY_NAME=<REGISTRY_NAME>
SOURCE_REGISTRY=k8s.gcr.io
CONTROLLER_IMAGE=ingress-nginx/controller
CONTROLLER_TAG=v1.0.4
PATCH_IMAGE=ingress-nginx/kube-webhook-certgen
PATCH_TAG=v1.1.1
DEFAULTBACKEND_IMAGE=defaultbackend-amd64
DEFAULTBACKEND_TAG=1.5

az acr import --name $REGISTRY_NAME --source $SOURCE_REGISTRY/$CONTROLLER_IMAGE:$CONTROLLER_TAG --image $CONTROLLER_IMAGE:$CONTROLLER_TAG
az acr import --name $REGISTRY_NAME --source $SOURCE_REGISTRY/$PATCH_IMAGE:$PATCH_TAG --image $PATCH_IMAGE:$PATCH_TAG
az acr import --name $REGISTRY_NAME --source $SOURCE_REGISTRY/$DEFAULTBACKEND_IMAGE:$DEFAULTBACKEND_TAG --image $DEFAULTBACKEND_IMAGE:$DEFAULTBACKEND_TAG
```

> [!NOTE]
> En plus d’importer des images de conteneur dans votre ACR, vous pouvez également importer des graphiques Helm dans votre ACR. Pour plus d’informations, consultez [Envoyer (push) et tirer (pull) des graphiques Helm vers un registre de conteneurs Azure][acr-helm].

## <a name="create-an-ingress-controller"></a>Créer un contrôleur d’entrée

Par défaut, un contrôleur d’entrée NGINX est créé avec une affectation d’adresse IP publique dynamique. Une exigence de configuration courante est celle d’utiliser un réseau privé interne et une adresse IP. Cette approche vous permet de limiter l’accès à vos services aux utilisateurs internes, en excluant tout accès externe.

Créez un fichier nommé *interne ingress.yaml* en utilisant l’exemple suivant de fichier manifeste. Cet exemple attribue l’adresse *10.240.0.42* à la ressource *loadBalancerIP*. Fournissez votre propre adresse IP interne pour une utilisation avec le contrôleur d’entrée. Assurez-vous que cette adresse IP est pas utilisée au sein de votre réseau virtuel. En outre, si vous utilisez un réseau virtuel et un sous-réseau existants, vous devez configurer votre cluster AKS avec les autorisations appropriées afin de pouvoir les gérer. Pour plus d’informations, consultez [Utiliser la mise en réseau kubenet avec vos propres plages d’adresses IP dans Azure Kubernetes Service (AKS)][aks-configure-kubenet-networking] ou [Configurer un réseau Azure CNI dans Azure Kubernetes Service (AKS)][aks-configure-advanced-networking].

```yaml
controller:
  service:
    loadBalancerIP: 10.240.0.42
    annotations:
      service.beta.kubernetes.io/azure-load-balancer-internal: "true"
```

À présent, déployez le graphique *nginx-ingress* avec Helm. Pour utiliser le fichier manifeste créé à l’étape précédente, ajoutez le paramètre `-f internal-ingress.yaml`. Pour renforcer la redondance, deux réplicas des contrôleurs d’entrée NGINX sont déployés avec le paramètre `--set controller.replicaCount`. Pour tirer pleinement parti de l’exécution de réplicas des contrôleurs d’entrée, vérifiez que votre cluster AKS comprend plusieurs nœuds.

Le contrôleur d’entrée doit également être planifié sur un nœud Linux. Les nœuds Windows Server ne doivent pas exécuter le contrôleur d’entrée. Un sélecteur de nœud est spécifié en utilisant le paramètre `--set nodeSelector` pour que le planificateur Kubernetes exécute le contrôleur d’entrée NGINX sur un nœud Linux.

> [!TIP]
> L’exemple suivant crée un espace de noms Kubernetes pour les ressources d’entrée *ingress-basic* et est destiné à fonctionner dans cet espace de noms. Spécifiez un espace de noms de votre propre environnement, si besoin. Si le contrôle d’accès en fonction du rôle (RBAC Kubernetes) n’est pas activé sur votre cluster AKS, ajoutez `--set rbac.create=false` aux commandes Helm.

> [!TIP]
> Si vous souhaitez activer la [préservation de l’adresse IP source du client][client-source-ip] pour les requêtes aux conteneurs de votre cluster, ajoutez `--set controller.service.externalTrafficPolicy=Local` à la commande d’installation Helm. L’IP source du client est stockée dans l’en-tête de la requête sous *X-Forwarded-For*. Lors de l’utilisation d’un contrôleur d’entrée pour lequel la conservation de l’adresse IP source du client est activée, un transfert direct TLS ne fonctionne pas.

```console
# Add the ingress-nginx repository
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx

# Set variable for ACR location to use for pulling images
ACR_URL=<REGISTRY_URL>

# Use Helm to deploy an NGINX ingress controller
helm install nginx-ingress ingress-nginx/ingress-nginx \
    --namespace ingress-basic --create-namespace \
    --set controller.replicaCount=2 \
    --set controller.nodeSelector."kubernetes\.io/os"=linux \
    --set controller.image.registry=$ACR_URL \
    --set controller.image.image=$CONTROLLER_IMAGE \
    --set controller.image.tag=$CONTROLLER_TAG \
    --set controller.image.digest="" \
    --set controller.admissionWebhooks.patch.nodeSelector."kubernetes\.io/os"=linux \
    --set controller.admissionWebhooks.patch.image.registry=$ACR_URL \
    --set controller.admissionWebhooks.patch.image.image=$PATCH_IMAGE \
    --set controller.admissionWebhooks.patch.image.tag=$PATCH_TAG \
    --set controller.admissionWebhooks.patch.image.digest="" \
    --set defaultBackend.nodeSelector."kubernetes\.io/os"=linux \
    --set defaultBackend.image.registry=$ACR_URL \
    --set defaultBackend.image.image=$DEFAULTBACKEND_IMAGE \
    --set defaultBackend.image.tag=$DEFAULTBACKEND_TAG \
    --set defaultBackend.image.digest=""
```

Lorsque le service équilibreur de charge Kubernetes est créé pour le contrôleur d’entrée NGINX, votre adresse IP interne est attribuée. Pour obtenir l’adresse IP publique, utilisez la commande `kubectl get service`.

```console
kubectl --namespace ingress-basic get services -o wide -w nginx-ingress-ingress-nginx-controller
```

L’attribution de l’adresse IP au service peut prendre quelques minutes, comme dans l’exemple de sortie suivant :

```
$ kubectl --namespace ingress-basic get services -o wide -w nginx-ingress-ingress-nginx-controller

NAME                                     TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)                      AGE   SELECTOR
nginx-ingress-ingress-nginx-controller   LoadBalancer   10.0.74.133   EXTERNAL_IP     80:32486/TCP,443:30953/TCP   44s   app.kubernetes.io/component=controller,app.kubernetes.io/instance=nginx-ingress,app.kubernetes.io/name=ingress-nginx
```

Aucune règle d’entrée n’a encore été créée. Par conséquent, la page 404 par défaut du contrôleur d’entrée NGINX s’affiche si vous accédez à l’adresse IP interne. Les étapes suivantes permettent de configurer les règles d’entrée.

## <a name="run-demo-applications"></a>Exécuter des applications de démonstration

Pour voir le contrôleur d’entrée à l’œuvre, exécutons deux applications de démonstration dans votre cluster AKS. Pour cet exemple, utilisez `kubectl apply` en vue de déployer deux instances d’une application *Hello world*.

Créez un fichier *aks-helloworld.yaml* et copiez-le dans l’exemple de YAML suivant :

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: aks-helloworld
spec:
  replicas: 1
  selector:
    matchLabels:
      app: aks-helloworld
  template:
    metadata:
      labels:
        app: aks-helloworld
    spec:
      containers:
      - name: aks-helloworld
        image: mcr.microsoft.com/azuredocs/aks-helloworld:v1
        ports:
        - containerPort: 80
        env:
        - name: TITLE
          value: "Welcome to Azure Kubernetes Service (AKS)"
---
apiVersion: v1
kind: Service
metadata:
  name: aks-helloworld
spec:
  type: ClusterIP
  ports:
  - port: 80
  selector:
    app: aks-helloworld
```

Créez un fichier *ingress-demo.yaml* et copiez-le dans l’exemple de YAML suivant :

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: ingress-demo
spec:
  replicas: 1
  selector:
    matchLabels:
      app: ingress-demo
  template:
    metadata:
      labels:
        app: ingress-demo
    spec:
      containers:
      - name: ingress-demo
        image: mcr.microsoft.com/azuredocs/aks-helloworld:v1
        ports:
        - containerPort: 80
        env:
        - name: TITLE
          value: "AKS Ingress Demo"
---
apiVersion: v1
kind: Service
metadata:
  name: ingress-demo
spec:
  type: ClusterIP
  ports:
  - port: 80
  selector:
    app: ingress-demo
```

Exécutez les deux applications de démonstration avec `kubectl apply` :

```console
kubectl apply -f aks-helloworld.yaml --namespace ingress-basic
kubectl apply -f ingress-demo.yaml --namespace ingress-basic
```

## <a name="create-an-ingress-route"></a>Créer un itinéraire d’entrée

Les deux applications sont maintenant en cours d’exécution sur votre cluster Kubernetes. Pour acheminer le trafic vers chaque application, créez une ressource d’entrée Kubernetes. La ressource d’entrée configure les règles qui acheminent le trafic vers l’une des deux applications.

Dans l’exemple suivant, le trafic vers l’adresse `http://10.240.0.42/` est acheminé vers le service nommé `aks-helloworld`. Le trafic vers l’adresse `http://10.240.0.42/hello-world-two` est acheminé vers le service `ingress-demo`.

Créez un fichier nommé `hello-world-ingress.yaml` et copiez-y l’exemple de code YAML suivant.

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: hello-world-ingress
  namespace: ingress-basic
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/ssl-redirect: "false"
    nginx.ingress.kubernetes.io/use-regex: "true"
    nginx.ingress.kubernetes.io/rewrite-target: /$1
spec:
  rules:
  - http:
      paths:
      - path: /hello-world-one(/|$)(.*)
        pathType: Prefix
        backend:
          service:
            name: aks-helloworld
            port:
              number: 80
      - path: /hello-world-two(/|$)(.*)
        pathType: Prefix
        backend:
          service:
            name: ingress-demo
            port:
              number: 80
      - path: /(.*)
        pathType: Prefix
        backend:
          service:
            name: aks-helloworld
            port:
              number: 80
```

Créez la ressource d’entrée avec la commande `kubectl apply -f hello-world-ingress.yaml`.

```console
kubectl apply -f hello-world-ingress.yaml
```

L’exemple de sortie suivant montre la ressource d’entrée créée.

```
$ kubectl apply -f hello-world-ingress.yaml

ingress.extensions/hello-world-ingress created
```

## <a name="test-the-ingress-controller"></a>Tester le contrôleur d’entrée

Pour tester les itinéraires du contrôleur d’entrée, accédez aux deux applications avec un client web. Si nécessaire, vous pouvez rapidement tester cette fonctionnalité uniquement interne à partir d’un pod sur le cluster AKS. Créez un pod de test et attachez-y une session Terminal :

```console
kubectl run -it --rm aks-ingress-test --image=mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11 --namespace ingress-basic
```

Installez `curl` dans le pod en utilisant `apt-get` :

```console
apt-get update && apt-get install -y curl
```

Accédez à présent à l’adresse de votre contrôleur d’entrée Kubernetes en utilisant `curl`, par exemple *http://10.240.0.42* . Fournissez votre propre adresse IP interne spécifiée lors du déploiement du contrôleur d’entrée dans le cadre de la première étape de cet article.

```console
curl -L http://10.240.0.42
```

Aucun chemin d’accès supplémentaire n’ayant été fourni avec l’adresse, l’itinéraire par défaut du contrôleur d’entrée est */* . La première application de démonstration est retournée, comme l’illustre l’exemple de sortie condensée suivant :

```
$ curl -L http://10.240.0.42

<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <link rel="stylesheet" type="text/css" href="/static/default.css">
    <title>Welcome to Azure Kubernetes Service (AKS)</title>
[...]
```

Ajoutez maintenant le chemin d’accès */hello-world-two* à l’adresse, par exemple, *http://10.240.0.42/hello-world-two* . La deuxième application de démonstration avec le titre personnalisé est retournée, comme l’illustre l’exemple de sortie condensée suivant :

```
$ curl -L -k http://10.240.0.42/hello-world-two

<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <link rel="stylesheet" type="text/css" href="/static/default.css">
    <title>AKS Ingress Demo</title>
[...]
```

## <a name="clean-up-resources"></a>Nettoyer les ressources

Cet article vous a montré comment utiliser Helm pour installer les composants d’entrée. Quand vous déployez un graphique Helm, une série de ressources Kubernetes est créée. Ces ressources incluent des pods, des déploiements et des services. Pour nettoyer ces ressources, vous pouvez supprimer l’espace de noms exemple en entier ou des ressources individuelles.

### <a name="delete-the-sample-namespace-and-all-resources"></a>Supprimer l’espace de noms exemple et toutes les ressources

Pour supprimer l’espace de noms exemple en entier, utilisez la commande `kubectl delete` et spécifiez le nom de votre espace de noms. Toutes les ressources de l’espace de noms sont supprimées.

```console
kubectl delete namespace ingress-basic
```

### <a name="delete-resources-individually"></a>Supprimer les ressources individuellement

Sinon, une approche plus précise consiste à supprimer les ressources individuelles créées. Listez les versions de Helm avec la commande `helm list`. 

```console
helm list --namespace ingress-basic
```

Recherchez les graphiques nommés *nginx-ingress* et *aks-helloworld*, comme illustré dans l’exemple de sortie suivant :

```
$ helm list --namespace ingress-basic

NAME                    NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                   APP VERSION
nginx-ingress           ingress-basic   1               2020-01-06 19:55:46.358275 -0600 CST    deployed        nginx-ingress-1.27.1    0.26.1  
```

Désinstallez les versions avec la commande `helm uninstall`.

```console
helm uninstall nginx-ingress --namespace ingress-basic
```

L’exemple suivant désinstalle le déploiement d’entrée NGINX.

```
$ helm uninstall nginx-ingress --namespace ingress-basic

release "nginx-ingress" uninstalled
```

Ensuite, supprimez les deux exemples d’applications :

```console
kubectl delete -f aks-helloworld.yaml --namespace ingress-basic
kubectl delete -f ingress-demo.yaml --namespace ingress-basic
```

Supprimez la route d’entrée qui a dirigé le trafic vers les exemples d’applications :

```console
kubectl delete -f hello-world-ingress.yaml
```

Pour finir, vous pouvez supprimer l’espace de noms lui-même. Utilisez la commande `kubectl delete` et spécifiez le nom de votre espace de noms :

```console
kubectl delete namespace ingress-basic
```

## <a name="next-steps"></a>Étapes suivantes

Cet article a mentionné certains composants qui n’appartiennent pas à AKS. Pour plus d’informations sur ces composants, consultez les pages projet suivantes :

- [Interface CLI Helm][helm-cli]
- [Contrôleur d’entrée NGINX][nginx-ingress]

Vous pouvez également :

- [Créer un contrôleur d’entrée dans Azure Kubernetes Service (AKS)][aks-ingress-basic]
- [Routage d’applications HTTP][aks-http-app-routing]
- [Create an HTTPS ingress controller on Azure Kubernetes Service (AKS)][aks-ingress-tls] (Créer un contrôleur d’entrée HTTPS dans Azure Kubernetes Service (AKS))
- [Créer un contrôleur d’entrée avec une adresse IP publique statique et configurer Let’s Encrypt pour générer automatiquement des certificats TLS][aks-ingress-static-tls]

<!-- LINKS - external -->
[helm]: https://helm.sh/
[helm-cli]: ./kubernetes-helm.md
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx

<!-- LINKS - internal -->
[use-helm]: kubernetes-helm.md
[azure-cli-install]: /cli/azure/install-azure-cli
[aks-ingress-basic]: ingress-basic.md
[aks-ingress-tls]: ingress-tls.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-own-tls]: ingress-own-tls.md
[client-source-ip]: concepts-network.md#ingress-controllers
[aks-configure-kubenet-networking]: configure-kubenet.md
[aks-configure-advanced-networking]: configure-azure-cni.md
[aks-supported versions]: supported-kubernetes-versions.md
[ingress-nginx-helm-chart]: https://github.com/kubernetes/ingress-nginx/tree/main/charts/ingress-nginx
[aks-integrated-acr]: cluster-container-registry-integration.md?tabs=azure-cli#create-a-new-aks-cluster-with-acr-integration
[acr-helm]: ../container-registry/container-registry-helm-repos.md
[aks-container-best-practices]: operator-best-practices-container-image-management.md
