---
title: Utilisation des entrées Azure Application Gateway
description: Comment utiliser l’entrée d’Azure Application Gateway avec Open Service Mesh
services: container-service
ms.topic: article
ms.date: 8/26/2021
ms.custom: mvc, devx-track-azurecli
ms.author: pgibson
ms.openlocfilehash: 70eaa03f3a10e01e9e3f17963f355117890a510d
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131066802"
---
# <a name="deploy-an-application-managed-by-open-service-mesh-osm-using-azure-application-gateway-ingress-aks-add-on"></a>Déployer une application gérée par Open Service Mesh (OSM) à l’aide du module complémentaire AKS d’entrée d’Azure Application Gateway

Ce didacticiel présente les procédures suivantes :

> [!div class="checklist"]
>
> - Afficher la configuration actuelle du cluster OSM
> - Créer les espaces de noms pour qu’OSM puisse gérer les applications déployées dans les espaces de noms
> - Intégrer les espaces de noms à gérer par OSM
> - Déployer l’exemple d’application
> - Vérifier l’exécution de l’application dans le cluster AKS
> - Créer une instance Azure Application Gateway à utiliser comme contrôleur d’entrée pour l’application
> - Exposer un service via l’entrée d’Azure Application Gateway sur Internet

## <a name="before-you-begin"></a>Avant de commencer

Les étapes détaillées dans cette procédure pas à pas supposent que vous avez déjà activé le module complémentaire OSM AKS pour votre cluster AKS. Si ce n’est pas le cas, consultez l’article [Déployer le module complémentaire OSM AKS](./open-service-mesh-deploy-addon-az-cli.md) avant de poursuivre. En outre, votre cluster AKS doit avoir la version `1.19+` ou une version ultérieure de Kubernetes avec Kubernetes RBAC activé, une connexion `kubectl` doit être établie avec le cluster (si vous avez besoin d’aide pour l’un de ces éléments, consultez le [démarrage rapide d’AKS](./kubernetes-walkthrough.md)) et le module complémentaire AKS OSM doit être installé.

Les ressources suivantes doivent être installées :

- Azure CLI, version 2.20.0 ou ultérieure
- OSM version 0.11.1 ou ultérieure
- Processeur JSON « jq » version 1.6+

## <a name="view-and-verify-the-current-osm-cluster-configuration"></a>Afficher et vérifier la configuration actuelle du cluster OSM

Une fois que le module complémentaire OSM pour AKS a été activé sur le cluster AKS, vous pouvez afficher les paramètres de configuration actuels dans la ressource osm-mesh-config. Exécutez la commande suivante pour afficher les propriétés :

```azurecli-interactive
kubectl get meshconfig osm-mesh-config -n kube-system -o yaml
```

La sortie montre la ressource OSM MeshConfig du cluster.

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

Notez que le paramètre **enablePermissiveTrafficPolicyMode** est configuré sur **true**. Le mode de stratégie de trafic permissif dans OSM est un mode dans lequel l’application de la stratégie de trafic [SMI](https://smi-spec.io/) est ignorée. Dans ce mode, OSM découvre automatiquement les services qui font partie du maillage de services et programme des règles de stratégie de trafic sur chaque side-car de proxy Envoy pour pouvoir communiquer avec ces services.

## <a name="create-namespaces-for-the-application"></a>Créer des espaces de noms pour l’application

Dans ce tutoriel, nous allons utiliser l’application bookstore OSM qui comporte les composants d’application suivants :

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

En ajoutant les espaces de noms au maillage OSM, vous permettez au contrôleur OSM d’injecter automatiquement votre application dans les conteneurs du proxy side-car Envoy. Exécutez la commande suivante pour intégrer les espaces de noms de l’application bookstore OSM.

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

## <a name="deploy-the-bookstore-application"></a>Déployer l’application Bookstore

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.9/docs/example/manifests/apps/bookbuyer.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.9/docs/example/manifests/apps/bookthief.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.9/docs/example/manifests/apps/bookstore.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.9/docs/example/manifests/apps/bookwarehouse.yaml
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

## <a name="update-the-bookbuyer-service"></a>Mettre à jour le service `Bookbuyer`

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

## <a name="verify-the-bookstore-application"></a>Vérifier l’application Bookstore

Pour l’instant, nous avons déployé l’application multiconteneur bookstore, mais elle n’est accessible qu’à partir du cluster AKS. Plus tard, nous ajouterons le contrôleur d’entrée d’Azure Application Gateway pour exposer l’application en dehors du cluster AKS. Pour vérifier que l’application s’exécute dans le cluster, nous allons utiliser une redirection de port pour afficher l’interface utilisateur du composant `bookbuyer`.

Commençons par récupérer le nom du pod de `bookbuyer`

```azurecli-interactive
kubectl get pod -n bookbuyer
```

Le résultat ressemble à ce qui suit. Un nom unique est ajouté à votre pod de `bookbuyer`.

```Output
NAME                         READY   STATUS    RESTARTS   AGE
bookbuyer-7676c7fcfb-mtnrz   2/2     Running   0          7m8s
```

Une fois que nous avons le nom du pod, nous pouvons utiliser la commande port-forward pour configurer un tunnel entre notre système local et l’application dans le cluster AKS. Exécutez la commande suivante pour configurer la redirection de port pour le port 8080 du système local. Utilisez à nouveau le nom du pod de `bookbuyer` que vous avez spécifié.

```azurecli-interactive
kubectl port-forward bookbuyer-7676c7fcfb-mtnrz -n bookbuyer 8080:14001
```

La sortie doit ressembler à ce qui suit.

```Output
Forwarding from 127.0.0.1:8080 -> 14001
Forwarding from [::1]:8080 -> 14001
```

Pendant que la session de redirection de port est en place, accédez à l’URL `http://localhost:8080` à partir d’un navigateur. Vous devriez maintenant être en mesure de voir l’interface utilisateur de l’application `bookbuyer` dans le navigateur, comme dans l’image ci-dessous.

![Image de l’interface utilisateur de l’application bookbuyer OSM pour App Gateway](./media/aks-osm-addon/osm-agic-bookbuyer-img.png)

## <a name="create-an-azure-application-gateway-to-expose-the-bookbuyer-application"></a>Créer une passerelle Azure Application Gateway pour exposer l’application `bookbuyer`

> [!NOTE]
> Les instructions suivantes permettent de créer une nouvelle instance d’Azure Application Gateway qui sera utilisée pour l’entrée. Si vous avez déjà une instance d’Azure Application Gateway que vous souhaitez utiliser, passez directement à la section relative à l’activation du module complémentaire Application Gateway Ingress Controller.

### <a name="deploy-a-new-application-gateway"></a>Déployer une nouvelle passerelle Application Gateway

> [!NOTE]
> Nous faisons référence à la documentation existante pour activer le module complémentaire Application Gateway Ingress Controller pour un cluster AKS existant. Certaines modifications ont été apportées pour s’adapter à la documentation d’OSM. Vous trouverez une documentation plus détaillée sur le sujet [ici](../application-gateway/tutorial-ingress-controller-add-on-existing.md).

Vous allez maintenant déployer une nouvelle passerelle Application Gateway afin de simuler la présence d’une passerelle Application Gateway existante que vous comptez ensuite utiliser pour équilibrer la charge du trafic vers votre cluster AKS, _myCluster_. La passerelle Application Gateway s’appellera _myApplicationGateway_, mais vous devez d’abord créer une ressource IP publique, nommée _myPublicIp_ ainsi qu’un autre réseau virtuel appelé _myVnet_ avec l’espace d’adressage 11.0.0.0/8 et un sous-réseau avec l’espace d’adressage 11.1.0.0/16 appelé _mySubnet_. Vous devez aussi déployer votre passerelle Application Gateway dans _mySubnet_ avec _myPublicIp_.

Quand vous utilisez un cluster AKS et une passerelle Application Gateway dans des réseaux virtuels distincts, les espaces d’adressage de ces deux réseaux ne doivent pas se chevaucher. L’espace d’adressage par défaut dans lequel un cluster AKS est déployé est 10.0.0.0/8. Nous définissons donc le préfixe d’adresse du réseau virtuel Application Gateway sur 11.0.0.0/8.

```azurecli-interactive
az group create --name myResourceGroup --location eastus2
az network public-ip create -n myPublicIp -g MyResourceGroup --allocation-method Static --sku Standard
az network vnet create -n myVnet -g myResourceGroup --address-prefix 11.0.0.0/8 --subnet-name mySubnet --subnet-prefix 11.1.0.0/16
az network application-gateway create -n myApplicationGateway -l eastus2 -g myResourceGroup --sku Standard_v2 --public-ip-address myPublicIp --vnet-name myVnet --subnet mySubnet
```

> [!NOTE]
> Le complément Application Gateway Ingress Controller (AGIC) prend en charge **uniquement** les références SKU Application Gateway v2 (Standard et WAF), **pas** les références SKU Application Gateway v1.

### <a name="enable-the-agic-add-on-for-an-existing-aks-cluster-through-azure-cli"></a>Activer le module complémentaire AGIC pour un cluster AKS existant via Azure CLI

Si vous souhaitez continuer à utiliser Azure CLI, vous pouvez continuer à activer le module complémentaire AGIC dans le cluster AKS que vous avez créé, _myCluster_, et spécifier le module complémentaire AGIC pour qu’il utilise la passerelle applicative existante que vous avez créée, _myApplicationGateway_.

```azurecli-interactive
appgwId=$(az network application-gateway show -n myApplicationGateway -g myResourceGroup -o tsv --query "id")
az aks enable-addons -n myCluster -g myResourceGroup -a ingress-appgw --appgw-id $appgwId
```

Vous pouvez vérifier que le module complémentaire Azure Application Gateway AKS a été activé à l’aide de la commande suivante.

```azurecli-interactive
az aks list -g osm-aks-rg -o json | jq -r .[].addonProfiles.ingressApplicationGateway.enabled
```

La sortie de cette commande doit être `true`.

### <a name="peer-the-two-virtual-networks-together"></a>Appairer les deux réseaux virtuels

Étant donné que nous avons déployé le cluster AKS dans son propre réseau virtuel et la passerelle Application Gateway dans un autre réseau virtuel, vous devez appairer les deux réseaux virtuels afin de rendre possible le trafic entre la passerelle Application Gateway et les pods dans le cluster. L’appairage des deux réseaux virtuels s’effectue en exécutant la commande Azure CLI deux fois pour garantir une connexion bidirectionnelle. La première commande crée une connexion d’appairage du réseau virtuel Application Gateway vers le réseau virtuel AKS. La seconde commande crée une connexion d’appairage dans l’autre direction.

```azurecli-interactive
nodeResourceGroup=$(az aks show -n myCluster -g myResourceGroup -o tsv --query "nodeResourceGroup")
aksVnetName=$(az network vnet list -g $nodeResourceGroup -o tsv --query "[0].name")

aksVnetId=$(az network vnet show -n $aksVnetName -g $nodeResourceGroup -o tsv --query "id")
az network vnet peering create -n AppGWtoAKSVnetPeering -g myResourceGroup --vnet-name myVnet --remote-vnet $aksVnetId --allow-vnet-access

appGWVnetId=$(az network vnet show -n myVnet -g myResourceGroup -o tsv --query "id")
az network vnet peering create -n AKStoAppGWVnetPeering -g $nodeResourceGroup --vnet-name $aksVnetName --remote-vnet $appGWVnetId --allow-vnet-access
```

## <a name="expose-the-bookbuyer-service-to-the-internet"></a>Exposer le service `bookbuyer` à Internet

Appliquez le manifeste d’entrée suivant au cluster AKS pour exposer le service `bookbuyer` à Internet par le biais d’Azure Application Gateway.

```azurecli-interactive
kubectl apply -f - <<EOF
---
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: bookbuyer-ingress
  namespace: bookbuyer
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway

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

Vous devez voir la sortie suivante

```Output
Warning: extensions/v1beta1 Ingress is deprecated in v1.14+, unavailable in v1.22+; use networking.k8s.io/v1 Ingress
ingress.extensions/bookbuyer-ingress created
```

Puisque le nom d’hôte dans le manifeste d’entrée est un nom fictif utilisé à des fins de test, le nom DNS n’est pas disponible sur Internet. Nous pouvons également utiliser le programme curl et transmettre l’en-tête de l’hôte à l’IP publique d’Azure Application Gateway et recevoir un code 200 qui nous connecte correctement au service `bookbuyer`.

```azurecli-interactive
appGWPIP=$(az network public-ip show -g MyResourceGroup -n myPublicIp -o tsv --query "ipAddress")
curl -H 'Host: bookbuyer.contoso.com' http://$appGWPIP/
```

Vous devez voir la sortie suivante

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
        <li>Total books bought: <strong>5969</strong>
          <ul>
            <li>from bookstore V1: <strong>277</strong>
            <li>from bookstore V2: <strong>5692</strong>
          </ul>
        </li>
      </ul>
    </div>

    <br/><br/><br/><br/>
    <br/><br/><br/><br/>
    <br/><br/><br/><br/>

    Current Time: <strong>Fri, 26 Mar 2021 16:34:30 UTC</strong>
  </body>
</html>
```

## <a name="troubleshooting"></a>Résolution des problèmes

- [Documentation de résolution des problèmes relatifs à AGIC](../application-gateway/ingress-controller-troubleshoot.md)
- [Des outils de dépannage supplémentaires sont disponibles sur le référentiel GitHub d’AGIC.](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/master/docs/troubleshootings/troubleshooting-installing-a-simple-application.md)
