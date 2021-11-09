---
title: Gérer une nouvelle application avec Open Service Mesh
description: Procédure de gestion d’une nouvelle application avec Open Service Mesh
services: container-service
ms.topic: article
ms.date: 8/26/2021
ms.custom: mvc, devx-track-azurecli
ms.author: pgibson
ms.openlocfilehash: 1dc58fa0709cdf1e09482a3f3ac3ee05788cd1eb
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131066859"
---
# <a name="manage-a-new-application-with-the-open-service-mesh-osm-azure-kubernetes-service-aks-add-on"></a>Gérez une nouvelle application avec le module complémentaire Open Service Mesh (OSM) Azure Kubernetes Service (AKS)

## <a name="before-you-begin"></a>Avant de commencer

Les étapes détaillées dans cette procédure pas à pas supposent que vous avez créé un cluster AKS (Kubernetes `1.19+` et versions ultérieures avec Kubernetes RBAC activé), que vous avez établi une connexion `kubectl` avec le cluster (si vous avez besoin d’aide pour l’un de ces éléments, consultez le [démarrage rapide d’AKS](./kubernetes-walkthrough.md)) et que vous avez installé le module complémentaire AKS OSM.

Les ressources suivantes doivent être installées :

- Azure CLI, version 2.20.0 ou ultérieure
- OSM version 0.11.1 ou ultérieure
- Processeur JSON « jq » version 1.6+

## <a name="create-namespaces-for-the-application"></a>Créer des espaces de noms pour l’application

Dans cette procédure pas à pas, nous utiliserons l’application bookstore OSM qui dispose des services Kubernetes suivants :

- `bookbuyer`
- `bookthief`
- `bookstore`
- `bookwarehouse`

Créez des espaces de noms pour chacun de ces composants d’application.

```azurecli-interactive
for i in bookstore bookbuyer bookthief bookwarehouse; do kubectl create ns $i; done
```

Vous devez normalement voir la sortie suivante :

```Output
namespace/bookstore created
namespace/bookbuyer created
namespace/bookthief created
namespace/bookwarehouse created
```

## <a name="onboard-the-namespaces-to-be-managed-by-osm"></a>Intégrer les espaces de noms à gérer par OSM

En ajoutant les espaces de noms au maillage OSM, cette action permet au contrôleur OSM d’injecter automatiquement votre application dans les conteneurs du proxy side-car Envoy. Exécutez la commande suivante pour intégrer les espaces de noms de l’application bookstore OSM.

```azurecli-interactive
osm namespace add bookstore bookbuyer bookthief bookwarehouse
```

Vous devez normalement voir la sortie suivante :

```Output
Namespace [bookstore] successfully added to mesh [osm]
Namespace [bookbuyer] successfully added to mesh [osm]
Namespace [bookthief] successfully added to mesh [osm]
Namespace [bookwarehouse] successfully added to mesh [osm]
```

## <a name="deploy-the-bookstore-application-to-the-aks-cluster"></a>Déployer l’application Bookstore sur le cluster AKS

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.11/docs/example/manifests/apps/bookbuyer.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.11/docs/example/manifests/apps/bookthief.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.11/docs/example/manifests/apps/bookstore.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.11/docs/example/manifests/apps/bookwarehouse.yaml
```

Toutes les sorties du déploiement sont résumées ci-dessous.

```Output
serviceaccount/bookbuyer created
service/bookbuyer created
deployment.apps/bookbuyer created

serviceaccount/bookthief created
service/bookthief created
deployment.apps/bookthief created

service/bookstore created
serviceaccount/bookstore created
deployment.apps/bookstore created

serviceaccount/bookwarehouse created
service/bookwarehouse created
deployment.apps/bookwarehouse created
```

## <a name="checkpoint-what-got-installed"></a>Point de contrôle : Quels sont les éléments installés ?

L’application Bookstore est un exemple d’application à plusieurs niveaux qui fonctionne bien pour tester la fonctionnalité de maillage de service. L’application se compose de quatre services, à savoir `bookbuyer`, `bookthief`, `bookstore` et `bookwarehouse`. Les services `bookbuyer` et `bookthief` communiquent avec le service `bookstore` pour récupérer des livres auprès du service `bookstore`. Le service `bookstore` récupère des livres hors du service `bookwarehouse` pour fournir `bookbuyer` et `bookthief`. Il s’agit d’une application simple à plusieurs niveaux qui montre bien l’utilisation d’un maillage de services pour protéger et autoriser les communications entre les services d’applications. Tout au long de la procédure, nous allons activer et désactiver les stratégies SMI (Service Mesh Interface) pour autoriser ou non les services à communiquer via OSM. Vous trouverez ci-dessous un diagramme d’architecture de ce qui a été installé pour l’application bookstore.

![Architecture de l’application de librairie OSM](./media/aks-osm-addon/osm-bookstore-app-arch.png)

## <a name="verify-the-bookstore-application-running-inside-the-aks-cluster"></a>Vérifier l’exécution de l’application Bookstore dans le cluster AKS

Pour l’instant, nous avons déployé l’application multiconteneur `bookstore`, mais elle n’est accessible qu’à partir du cluster AKS. Les tutoriels ultérieurs vous aideront à exposer l’application en dehors du cluster via un contrôleur d’entrée. Pour le moment, nous allons utiliser la redirection de port pour accéder à l’application `bookbuyer` à l’intérieur du cluster AKS afin de vérifier qu’elle achète des livres auprès du service `bookstore`.

Pour vérifier que l’application s’exécute dans le cluster, nous allons utiliser une redirection de port pour afficher l’interface utilisateur des composants `bookbuyer` et `bookthief`.

Commençons par récupérer le nom du pod de `bookbuyer`

```azurecli-interactive
kubectl get pod -n bookbuyer
```

Le résultat ressemble à ce qui suit. Un nom unique est ajouté à votre pod de `bookbuyer`.

```Output
NAME                         READY   STATUS    RESTARTS   AGE
bookbuyer-7676c7fcfb-mtnrz   2/2     Running   0          7m8s
```

Une fois que nous avons le nom du pod, nous pouvons utiliser la commande port-forward pour configurer un tunnel entre notre système local et l’application dans le cluster AKS. Exécutez la commande suivante pour configurer la redirection de port pour le port 8080 du système local. Utilisez à nouveau le nom du pod de bookbuyer que vous avez spécifié.

> [!NOTE]
> Pour toutes les commandes de redirection de port, il est préférable d’utiliser un terminal supplémentaire afin de pouvoir continuer à travailler tout au long de cette procédure sans déconnecter le tunnel. Il est également préférable d’établir le tunnel de redirection de port en dehors d’Azure Cloud Shell.

```Bash
kubectl port-forward bookbuyer-7676c7fcfb-mtnrz -n bookbuyer 8080:14001
```

Vous devez obtenir une sortie similaire à la suivante :

```Output
Forwarding from 127.0.0.1:8080 -> 14001
Forwarding from [::1]:8080 -> 14001
```

Pendant que la session de redirection de port est en place, accédez à l’URL `http://localhost:8080` à partir d’un navigateur. Vous devriez maintenant être en mesure de voir l’interface utilisateur de l’application `bookbuyer` dans le navigateur, comme dans l’image ci-dessous.

![Image de l’interface utilisateur de l’application bookbuyer OSM](./media/aks-osm-addon/osm-bookbuyer-service-ui.png)

Vous remarquerez également que le nombre total de livres achetés continue de s’incrémenter au service v1 `bookstore`. Le service v2 `bookstore` n’a pas encore été déployé. Nous déploierons le service v2 `bookstore` lorsque nous présenterons les stratégies de division du trafic SMI.

Vous pouvez également faire de même pour le service `bookthief`.

```azurecli-interactive
kubectl get pod -n bookthief
```

Le résultat ressemble à ce qui suit. Un nom unique est ajouté à votre pod de `bookthief`.

```Output
NAME                         READY   STATUS    RESTARTS   AGE
bookthief-59549fb69c-cr8vl   2/2     Running   0          15m54s
```

Transmission de port au pod `bookthief`.

```Bash
kubectl port-forward bookthief-59549fb69c-cr8vl -n bookthief 8080:14001
```

Accédez à l’URL `http://localhost:8080` à partir d’un navigateur. Vous devez voir que `bookthief` est en train de voler des livres du service `bookstore` ! Plus tard, nous implémenterons une stratégie de trafic pour arrêter `bookthief`.

![Image de l’interface utilisateur de l’application bookthief OSM](./media/aks-osm-addon/osm-bookthief-service-ui.png)

## <a name="disable-osm-permissive-traffic-mode-for-the-mesh"></a>Désactiver le mode de trafic permissif d’OSM pour le maillage

Nous allons maintenant désactiver la stratégie de mode de trafic permissif et OSM aura besoin de stratégies [SMI](https://smi-spec.io/) explicites déployées sur le cluster pour autoriser les communications dans le maillage à partir de chaque service. Pour désactiver le mode de trafic permissif, exécutez la commande suivante pour mettre à jour la propriété de la ressource OSM MeshConfig en remplaçant la valeur `true` par `false`.

```azurecli-interactive
kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"traffic":{"enablePermissiveTrafficPolicyMode":false}}}' --type=merge
```

Le résultat ressemble à ce qui suit.

```Output
meshconfig.config.openservicemesh.io/osm-mesh-config patched
```

Pour vérifier que le mode de trafic permissif a été désactivé, redirigez à nouveau le port vers le pod `bookbuyer` ou `bookthief` pour afficher leur interface utilisateur dans le navigateur et voir si les livres achetés ou volés ne s’incrémentent plus. Veillez à actualiser le navigateur. Si l’incrémentation s’est arrêtée, la stratégie a été appliquée correctement. Vous avez réussi à empêcher le `bookthief` de voler des livres, mais le `bookbuyer` ne peut pas acheter de livres auprès du `bookstore` et le `bookstore` ne peut pas récupérer de livres du `bookwarehouse`. Ensuite, nous allons implémenter des stratégies [SMI](https://smi-spec.io/) pour permettre uniquement aux services du maillage de votre choix de communiquer. Pour plus d’informations sur le mode de trafic permissif, consultez et lisez l’article [Mode de stratégie de trafic permissif](https://docs.openservicemesh.io/docs/guides/traffic_management/permissive_mode/).

## <a name="apply-service-mesh-interface-smi-traffic-access-policies"></a>Appliquer des stratégies d’accès au trafic SMI (Service Mesh Interface)

Maintenant que nous avons désactivé toutes les communications dans le maillage, autorisons notre service `bookbuyer` à communiquer avec notre service `bookstore` pour acheter des livres, et autorisons notre service `bookstore` à communiquer avec notre service `bookwarehouse` pour récupérer des livres à vendre.

Déployez les stratégies [SMI](https://smi-spec.io/) suivantes.

```azurecli-interactive
kubectl apply -f - <<EOF
---
apiVersion: access.smi-spec.io/v1alpha3
kind: TrafficTarget
metadata:
  name: bookbuyer-access-bookstore
  namespace: bookstore
spec:
  destination:
    kind: ServiceAccount
    name: bookstore
    namespace: bookstore
  rules:
  - kind: HTTPRouteGroup
    name: bookstore-service-routes
    matches:
    - buy-a-book
    - books-bought
  sources:
  - kind: ServiceAccount
    name: bookbuyer
    namespace: bookbuyer
---
apiVersion: specs.smi-spec.io/v1alpha4
kind: HTTPRouteGroup
metadata:
  name: bookstore-service-routes
  namespace: bookstore
spec:
  matches:
  - name: books-bought
    pathRegex: /books-bought
    methods:
    - GET
    headers:
    - "user-agent": ".*-http-client/*.*"
    - "client-app": "bookbuyer"
  - name: buy-a-book
    pathRegex: ".*a-book.*new"
    methods:
    - GET
  - name: update-books-bought
    pathRegex: /update-books-bought
    methods:
    - POST
---
kind: TrafficTarget
apiVersion: access.smi-spec.io/v1alpha3
metadata:
  name: bookstore-access-bookwarehouse
  namespace: bookwarehouse
spec:
  destination:
    kind: ServiceAccount
    name: bookwarehouse
    namespace: bookwarehouse
  rules:
  - kind: HTTPRouteGroup
    name: bookwarehouse-service-routes
    matches:
    - restock-books
  sources:
  - kind: ServiceAccount
    name: bookstore
    namespace: bookstore
  - kind: ServiceAccount
    name: bookstore-v2
    namespace: bookstore
---
apiVersion: specs.smi-spec.io/v1alpha4
kind: HTTPRouteGroup
metadata:
  name: bookwarehouse-service-routes
  namespace: bookwarehouse
spec:
  matches:
    - name: restock-books
      methods:
      - POST
      headers:
      - host: bookwarehouse.bookwarehouse
EOF
```

Le résultat ressemble à ce qui suit.

```Output
traffictarget.access.smi-spec.io/bookbuyer-access-bookstore-v1 created
httproutegroup.specs.smi-spec.io/bookstore-service-routes created
traffictarget.access.smi-spec.io/bookstore-access-bookwarehouse created
httproutegroup.specs.smi-spec.io/bookwarehouse-service-routes created
```

Vous pouvez maintenant configurer une session de redirection de port sur les pods `bookbuyer` ou `bookstore`, et constater que les métriques des livres achetés et vendus s’incrémentent à nouveau. Vous pouvez également faire de même pour le pod `bookthief` afin de vérifier qu’il n’est toujours plus en mesure de voler des livres.

## <a name="apply-service-mesh-interface-smi-traffic-split-policies"></a>Appliquer des stratégies de division du trafic SMI (Service Mesh Interface)

Pour notre dernière démonstration, nous allons créer une stratégie de division du trafic [SMI](https://smi-spec.io/) pour configurer le poids des communications d’un service vers plusieurs services en tant que serveur principal. La fonctionnalité de division du trafic vous permet de déplacer progressivement les connexions d’un service vers un autre en pondérant le trafic sur une échelle de 0 à 100.

Le graphique ci-dessous est un diagramme de la stratégie de division du trafic [SMI](https://smi-spec.io/) à déployer. Nous allons déployer une autre application `Bookstore` comme version 2, puis diviser le trafic entrant à partir du `bookbuyer`, en pondérant 25 % du trafic vers le service `bookstore` v1 et 75 % vers le service `bookstore` v2.

![Diagramme de division du trafic de bookbuyer OSM](./media/aks-osm-addon/osm-bookbuyer-traffic-split-diagram.png)

Déployez le service `bookstore` v2.

```azurecli-interactive
kubectl apply -f - <<EOF
---
apiVersion: v1
kind: Service
metadata:
  name: bookstore-v2
  namespace: bookstore
  labels:
    app: bookstore-v2
spec:
  ports:
  - port: 14001
    name: bookstore-port
  selector:
    app: bookstore-v2
---
# Deploy bookstore-v2 Service Account
apiVersion: v1
kind: ServiceAccount
metadata:
  name: bookstore-v2
  namespace: bookstore
---
# Deploy bookstore-v2 Deployment
apiVersion: apps/v1
kind: Deployment
metadata:
  name: bookstore-v2
  namespace: bookstore
spec:
  replicas: 1
  selector:
    matchLabels:
      app: bookstore-v2
  template:
    metadata:
      labels:
        app: bookstore-v2
    spec:
      serviceAccountName: bookstore-v2
      containers:
        - name: bookstore
          image: openservicemesh/bookstore:v0.8.0
          imagePullPolicy: Always
          ports:
            - containerPort: 14001
              name: web
          command: ["/bookstore"]
          args: ["--path", "./", "--port", "14001"]
          env:
            - name: BOOKWAREHOUSE_NAMESPACE
              value: bookwarehouse
            - name: IDENTITY
              value: bookstore-v2
---
kind: TrafficTarget
apiVersion: access.smi-spec.io/v1alpha3
metadata:
  name: bookbuyer-access-bookstore-v2
  namespace: bookstore
spec:
  destination:
    kind: ServiceAccount
    name: bookstore-v2
    namespace: bookstore
  rules:
  - kind: HTTPRouteGroup
    name: bookstore-service-routes
    matches:
    - buy-a-book
    - books-bought
  sources:
  - kind: ServiceAccount
    name: bookbuyer
    namespace: bookbuyer
EOF
```

Vous devez voir la sortie suivante.

```Output
service/bookstore-v2 configured
serviceaccount/bookstore-v2 created
deployment.apps/bookstore-v2 created
traffictarget.access.smi-spec.io/bookstore-v2 created
```

À présent, déployez la stratégie de division du trafic pour répartir le trafic `bookbuyer` entre les deux services `bookstore` v1 et v2.

```azurecli-interactive
kubectl apply -f - <<EOF
apiVersion: split.smi-spec.io/v1alpha2
kind: TrafficSplit
metadata:
  name: bookstore-split
  namespace: bookstore
spec:
  service: bookstore.bookstore
  backends:
  - service: bookstore
    weight: 25
  - service: bookstore-v2
    weight: 75
EOF
```

Vous devez voir la sortie suivante.

```Output
trafficsplit.split.smi-spec.io/bookstore-split created
```

Configurez un tunnel de redirection de port vers le pod `bookbuyer`. Vous devez maintenant voir les livres achetés auprès du service `bookstore` v2. Si vous continuez à observer l’incrément des achats, vous devriez remarquer un accroissement plus rapide des achats effectués par le service `bookstore` v2.

![Interface utilisateur des livres achetés de bookbuyer OSM](./media/aks-osm-addon/osm-bookbuyer-traffic-split-ui.png)
