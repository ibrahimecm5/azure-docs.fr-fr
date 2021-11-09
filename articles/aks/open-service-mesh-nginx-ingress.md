---
title: Utilisation des entrées NGINX
description: Utilisation de l’entrée NGINX avec Open Service Mesh
services: container-service
ms.topic: article
ms.date: 8/26/2021
ms.custom: mvc, devx-track-azurecli
ms.author: pgibson
ms.openlocfilehash: ee313000b5b2fd21e7f629c57f45bbe0d3557835
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131066821"
---
# <a name="deploy-an-application-managed-by-open-service-mesh-osm-with-nginx-ingress"></a>Déployer une application gérée par Open Service Mesh (OSM) avec entrée NGINX

Open Service Mesh (OSM) est un maillage de services natif coud léger et extensible qui permet aux utilisateurs de gérer et de sécuriser uniformément les environnements de microservices hautement dynamiques et de bénéficier de fonctionnalités d’observabilité prêtes à l’emploi.

Ce didacticiel présente les procédures suivantes :

> [!div class="checklist"]
>
> - Afficher la configuration actuelle du cluster OSM
> - Créer les espaces de noms pour qu’OSM puisse gérer les applications déployées dans les espaces de noms
> - Intégrer les espaces de noms à gérer par OSM
> - Déployer l’exemple d’application
> - Vérifier l’exécution de l’application dans le cluster AKS
> - Créer un contrôleur d’entrée NGINX utilisé pour l’application
> - Exposer un service via l’entrée d’Azure Application Gateway sur Internet

## <a name="before-you-begin"></a>Avant de commencer

Les étapes détaillées dans cet article supposent que vous avez créé un cluster AKS (Kubernetes `1.19+` et versions ultérieures avec Kubernetes RBAC activé), que vous avez établi une connexion `kubectl` avec le cluster (si vous avez besoin d’aide pour l’un de ces éléments, consultez le [démarrage rapide d’AKS](./kubernetes-walkthrough.md)) et que vous avez installé le module complémentaire AKS OSM.

Les ressources suivantes doivent être installées :

- Azure CLI, version 2.20.0 ou ultérieure
- OSM version 0.11.1 ou ultérieure
- Processeur JSON « jq » version 1.6+

### <a name="view-and-verify-the-current-osm-cluster-configuration"></a>Afficher et vérifier la configuration actuelle du cluster OSM

Une fois que le module complémentaire OSM pour AKS a été activé sur le cluster AKS, vous pouvez afficher les paramètres de configuration actuels dans la ressource osm-mesh-config. Exécutez la commande suivante pour afficher les propriétés :

```azurecli-interactive
kubectl get meshconfig osm-mesh-config -n osm-system -o yaml
```

La sortie indique la configuration actuelle d’OSM pour le cluster.

```
apiVersion: config.openservicemesh.io/v1alpha1
kind: MeshConfig
metadata:
  creationTimestamp: "0000-00-00A00:00:00A"
  generation: 1
  name: osm-mesh-config
  namespace: kube-system
  resourceVersion: "2494"
  uid: 6c4d67f3-c241-4aeb-bf4f-b029b08faa31
spec:
  certificate:
    serviceCertValidityDuration: 24h
  featureFlags:
    enableEgressPolicy: true
    enableMulticlusterMode: false
    enableWASMStats: true
  observability:
    enableDebugServer: true
    osmLogLevel: info
    tracing:
      address: jaeger.osm-system.svc.cluster.local
      enable: false
      endpoint: /api/v2/spans
      port: 9411
  sidecar:
    configResyncInterval: 0s
    enablePrivilegedInitContainer: false
    envoyImage: mcr.microsoft.com/oss/envoyproxy/envoy:v1.18.3
    initContainerImage: mcr.microsoft.com/oss/openservicemesh/init:v0.9.1
    logLevel: error
    maxDataPlaneConnections: 0
    resources: {}
  traffic:
    enableEgress: true
    enablePermissiveTrafficPolicyMode: true
    inboundExternalAuthorization:
      enable: false
      failureModeAllow: false
      statPrefix: inboundExtAuthz
      timeout: 1s
    useHTTPSIngress: false
```

Notez que le paramètre **enablePermissiveTrafficPolicyMode** est configuré sur **true**. Le mode de stratégie de trafic permissif dans OSM est un mode dans lequel l’application de la stratégie de trafic [SMI](https://smi-spec.io/) est ignorée. Dans ce mode, OSM découvre automatiquement les services qui font partie du maillage de services et programme des règles de stratégie de trafic sur chaque side-car de proxy Envoy pour pouvoir communiquer avec ces services. Pour plus d’informations sur le mode de trafic permissif, consultez et lisez l’article [Mode de stratégie de trafic permissif](https://docs.openservicemesh.io/docs/guides/traffic_management/permissive_mode/).

## <a name="create-namespaces-for-the-application"></a>Créer des espaces de noms pour l’application

Dans ce tutoriel, nous allons utiliser l’application `bookstore` OSM qui comporte les composants d’application suivants :

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

L’ajout des espaces de noms au maillage OSM permettra au contrôleur OSM d’injecter automatiquement votre application dans les conteneurs du proxy side-car Envoy. Exécutez la commande suivante pour intégrer les espaces de noms de l’application `bookstore` OSM.

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
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookbuyer.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookthief.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookstore.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookwarehouse.yaml
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

## <a name="update-the-bookbuyer-service"></a>Mettre à jour le service Bookbuyer

Mettez à jour le service `bookbuyer` avec la configuration correcte du port entrant à l’aide du manifeste de service suivant.

```azurecli-interactive
kubectl apply -f - <<EOF
apiVersion: v1
kind: Service
metadata:
  name: bookbuyer
  namespace: bookbuyer
  labels:
    app: bookbuyer
spec:
  ports:
  - port: 14001
    name: inbound-port
  selector:
    app: bookbuyer
EOF
```

## <a name="verify-the-bookstore-application-running-inside-the-aks-cluster"></a>Vérifier l’exécution de l’application Bookstore dans le cluster AKS

Pour l’instant, nous avons déployé l’application multiconteneur `bookstore`, mais elle n’est accessible qu’à partir du cluster AKS. Plus tard, nous ajouterons le contrôleur d’entrée d’Azure Application Gateway pour exposer l’application en dehors du cluster AKS. Pour vérifier que l’application s’exécute dans le cluster, nous allons utiliser une redirection de port pour afficher l’interface utilisateur du composant `bookbuyer`.

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

```azurecli-interactive
kubectl port-forward bookbuyer-7676c7fcfb-mtnrz -n bookbuyer 8080:14001
```

La sortie doit ressembler à celle ci-dessous :

```Output
Forwarding from 127.0.0.1:8080 -> 14001
Forwarding from [::1]:8080 -> 14001
```

Pendant que la session de redirection de port est en place, accédez à l’URL `http://localhost:8080` à partir d’un navigateur. Vous devriez maintenant être en mesure de voir l’interface utilisateur de l’application `bookbuyer` dans le navigateur, comme dans l’image ci-dessous.

![Image de l’interface utilisateur de l’application bookbuyer OSM pour NGINX](./media/aks-osm-addon/osm-agic-bookbuyer-img.png)

## <a name="create-an-nginx-ingress-controller-in-azure-kubernetes-service-aks"></a>Créer un contrôleur d’entrée NGINX dans Azure Kubernetes Service (AKS)

Un contrôleur d’entrée est un logiciel qui fournit un proxy inversé, un routage du trafic configurable et un arrêt TLS pour les services Kubernetes. Des ressources d’entrée Kubernetes sont utilisées pour configurer les règles d’entrée et les itinéraires des services Kubernetes individuels. En utilisant un contrôleur d’entrée et des règles d’entrée, une seule adresse IP peut être utilisée pour router le trafic vers plusieurs services dans un cluster Kubernetes.

Nous utiliserons le contrôleur d’entrée pour exposer l’application gérée par OSM à Internet. Pour créer le contrôleur d’entrée, utilisez Helm pour installer nginx-ingress. Pour renforcer la redondance, deux réplicas des contrôleurs d’entrée NGINX sont déployés avec le paramètre `--set controller.replicaCount`. Pour tirer pleinement parti de l’exécution de réplicas des contrôleurs d’entrée, vérifiez que votre cluster AKS comprend plusieurs nœuds.

Le contrôleur d’entrée va être planifié sur un nœud Linux. Les nœuds Windows Server ne doivent pas exécuter le contrôleur d’entrée. Un sélecteur de nœud est spécifié en utilisant le paramètre `--set nodeSelector` pour que le planificateur Kubernetes exécute le contrôleur d’entrée NGINX sur un nœud Linux.

> [!TIP]
> L’exemple suivant crée un espace de noms Kubernetes pour les ressources d’entrée _ingress-basic_. Spécifiez un espace de noms de votre propre environnement, si besoin.

```azurecli-interactive
# Create a namespace for your ingress resources
kubectl create namespace ingress-basic

# Add the ingress-nginx repository
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx

# Update the helm repo(s)
helm repo update

# Use Helm to deploy an NGINX ingress controller in the ingress-basic namespace
helm install nginx-ingress ingress-nginx/ingress-nginx \
    --namespace ingress-basic \
    --set controller.replicaCount=1 \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set controller.admissionWebhooks.patch.nodeSelector."beta\.kubernetes\.io/os"=linux
```

Un service équilibreur de charge Kubernetes est créé pour le contrôleur d’entrée NGINX. Une adresse IP publique dynamique est attribuée, comme illustré dans l’exemple de sortie suivant :

```Output
$ kubectl --namespace ingress-basic get services -o wide -w nginx-ingress-ingress-nginx-controller

NAME                                     TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)                      AGE   SELECTOR
nginx-ingress-ingress-nginx-controller   LoadBalancer   10.0.74.133   EXTERNAL_IP     80:32486/TCP,443:30953/TCP   44s   app.kubernetes.io/component=controller,app.kubernetes.io/instance=nginx-ingress,app.kubernetes.io/name=ingress-nginx
```

Aucune règle d’entrée n’a encore été créée. Actuellement, la page 404 par défaut du contrôleur d’entrée NGINX s’affiche si vous accédez à l’adresse IP interne. Les étapes suivantes permettent de configurer les règles d’entrée.

## <a name="expose-the-bookbuyer-service-to-the-internet"></a>Exposer le service bookbuyer à Internet

```azurecli-interactive
kubectl apply -f - <<EOF
---
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: bookbuyer-ingress
  namespace: bookbuyer
  annotations:
    kubernetes.io/ingress.class: nginx

spec:

  rules:
    - host: bookbuyer.contoso.com
      http:
        paths:
        - path: /
          backend:
            serviceName: bookbuyer
            servicePort: 14001

  backend:
    serviceName: bookbuyer
    servicePort: 14001
EOF
```

Vous devez normalement voir la sortie suivante :

```Output
Warning: extensions/v1beta1 Ingress is deprecated in v1.14+, unavailable in v1.22+; use networking.k8s.io/v1 Ingress
ingress.extensions/bookbuyer-ingress created
```

## <a name="view-the-nginx-logs"></a>Afficher les journaux NGINX

```azurecli-interactive
POD=$(kubectl get pods -n ingress-basic | grep 'nginx-ingress' | awk '{print $1}')

kubectl logs $POD -n ingress-basic -f
```

La sortie indique l’état du contrôleur d’entrée NGINX quand la règle d’entrée a bien été appliquée :

```Output
I0321 <date>       6 event.go:282] Event(v1.ObjectReference{Kind:"Pod", Namespace:"ingress-basic", Name:"nginx-ingress-ingress-nginx-controller-54cf6c8bf4-jdvrw", UID:"3ebbe5e5-50ef-481d-954d-4b82a499ebe1", APIVersion:"v1", ResourceVersion:"3272", FieldPath:""}): type: 'Normal' reason: 'RELOAD' NGINX reload triggered due to a change in configuration
I0321 <date>        6 event.go:282] Event(v1.ObjectReference{Kind:"Ingress", Namespace:"bookbuyer", Name:"bookbuyer-ingress", UID:"e1018efc-8116-493c-9999-294b4566819e", APIVersion:"networking.k8s.io/v1beta1", ResourceVersion:"5460", FieldPath:""}): type: 'Normal' reason: 'Sync' Scheduled for sync
I0321 <date>        6 controller.go:146] "Configuration changes detected, backend reload required"
I0321 <date>        6 controller.go:163] "Backend successfully reloaded"
I0321 <date>        6 event.go:282] Event(v1.ObjectReference{Kind:"Pod", Namespace:"ingress-basic", Name:"nginx-ingress-ingress-nginx-controller-54cf6c8bf4-jdvrw", UID:"3ebbe5e5-50ef-481d-954d-4b82a499ebe1", APIVersion:"v1", ResourceVersion:"3272", FieldPath:""}): type: 'Normal' reason: 'RELOAD' NGINX reload triggered due to a change in configuration
```

## <a name="view-the-nginx-services-and-bookbuyer-service-externally"></a>Afficher les services NGINX et le service bookbuyer en externe

```azurecli-interactive
kubectl get services -n ingress-basic
```

```Output
NAME                                               TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)                      AGE
nginx-ingress-ingress-nginx-controller             LoadBalancer   10.0.100.23   20.193.1.74   80:31742/TCP,443:32683/TCP   4m15s
nginx-ingress-ingress-nginx-controller-admission   ClusterIP      10.0.163.98   <none>        443/TCP                      4m15s
```

Puisque le nom d’hôte dans le manifeste d’entrée est un nom fictif utilisé à des fins de test, le nom DNS n’est pas disponible sur Internet. Nous pouvons également utiliser le programme curl et transmettre l’en-tête de l’hôte à l’IP publique de NGINX et recevoir un code 200 qui nous connecte correctement au service bookbuyer.

```azurecli-interactive
curl -H 'Host: bookbuyer.contoso.com' http://EXTERNAL-IP/
```

Vous devez normalement voir la sortie suivante :

```Output
<!doctype html>
<html itemscope="" itemtype="http://schema.org/WebPage" lang="en">
  <head>
      <meta content="Bookbuyer" name="description">
      <meta content="text/html; charset=UTF-8" http-equiv="Content-Type">
      <title>Bookbuyer</title>
      <style>
        #navbar {
            width: 100%;
            height: 50px;
            display: table;
            border-spacing: 0;
            white-space: nowrap;
            line-height: normal;
            background-color: #0078D4;
            background-position: left top;
            background-repeat-x: repeat;
            background-image: none;
            color: white;
            font: 2.2em "Fira Sans", sans-serif;
        }
        #main {
            padding: 10pt 10pt 10pt 10pt;
            font: 1.8em "Fira Sans", sans-serif;
        }
        li {
            padding: 10pt 10pt 10pt 10pt;
            font: 1.2em "Consolas", sans-serif;
        }
      </style>
      <script>
        setTimeout(function(){window.location.reload(1);}, 1500);
      </script>
  </head>
  <body bgcolor="#fff">
    <div id="navbar">
      &#128214; Bookbuyer
    </div>
    <div id="main">
      <ul>
        <li>Total books bought: <strong>1833</strong>
          <ul>
            <li>from bookstore V1: <strong>277</strong>
            <li>from bookstore V2: <strong>1556</strong>
          </ul>
        </li>
      </ul>
    </div>

    <br/><br/><br/><br/>
    <br/><br/><br/><br/>
    <br/><br/><br/><br/>

    Current Time: <strong>Fri, 26 Mar 2021 15:02:53 UTC</strong>
  </body>
</html>
```
