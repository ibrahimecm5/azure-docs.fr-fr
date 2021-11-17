---
title: Configuration de nœuds Azure Kubernetes Service (AKS) avec un proxy HTTP
description: Utilisez la fonctionnalité de configuration du proxy HTTP pour les nœuds Azure Kubernetes Service (AKS).
services: container-service
author: nickomang
ms.topic: article
ms.date: 09/09/2021
ms.author: nickoman
ms.openlocfilehash: 81631bfea3cc55b52dc95a81cb17c3420cf9638d
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132346108"
---
# <a name="http-proxy-support-in-azure-kubernetes-service-preview"></a>Prise en charge du proxy HTTP dans Azure Kubernetes Service (préversion)

Les clusters Azure Kubernetes Service (AKS), qu’ils soient déployés dans un réseau virtuel managé ou personnalisé, ont besoin de certaines dépendances sortantes pour fonctionner correctement. Avant, dans les environnements qui avaient besoin d’un accès à Internet pour organiser le routage via des proxys HTTP, cela posait un problème. Les nœuds n’avaient aucun moyen de démarrer la configuration, les variables d’environnement et les certificats nécessaires pour accéder aux services Internet.

Cette fonctionnalité ajoute la prise en charge du proxy HTTP aux clusters AKS, en exposant une interface simple que les opérateurs de cluster peuvent utiliser pour sécuriser le trafic réseau dont a besoin AKS dans les environnements dépendant du proxy.

Certaines solutions plus complexes peuvent nécessiter une chaîne d’approbation pour établir des communications sécurisées sur le réseau. La fonctionnalité permet également d’installer une autorité de certification approuvée sur les nœuds dans le cadre du démarrage de cluster.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="limitations-and-other-details"></a>Limitations et autres détails

Les scénarios suivants ne sont **pas** pris en charge :
- Module complémentaire de surveillance
- Configurations de proxy différentes par pool de nœuds
- Mise à jour des paramètres de proxy après la création du cluster
- Authentification par nom d’utilisateur/mot de passe
- Autorités de certification personnalisées pour la communication du serveur d’API
- Clusters Windows
- Pools de nœuds utilisant des groupes à haute disponibilité de machines virtuelles (VMAS)

Par défaut, *httpProxy*, *httpsProxy* et *trustedCa* n’ont aucune valeur.

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, vous pouvez créer un [compte gratuit](https://azure.microsoft.com/free).
* [Azure CLI](/cli/azure/install-azure-cli).

### <a name="install-the-aks-preview-azure-cli"></a>Installez l’interface de ligne de commande Azure `aks-preview`

Vous devez également disposer de l’extension Azure CLI *aks-preview* version 0.5.25 ou ultérieure. Installez l’extension d’Azure CLI *aks-preview* à l’aide de la commande [az extension add][az-extension-add]. Ou installez toutes les mises à jour disponibles à l’aide de la commande [az extension update][az-extension-update].

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview
# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-the-httpproxyconfigpreview-preview-feature"></a>Inscrire la fonctionnalité d’évaluation `HTTPProxyConfigPreview`

Pour utiliser la fonctionnalité, vous devez activer l’indicateur de fonctionnalité `HTTPProxyConfigPreview` sur votre abonnement.

Inscrivez l’indicateur de fonctionnalité `HTTPProxyConfigPreview` à l’aide de la commande [az feature register][az-feature-register], comme indiqué dans l’exemple suivant :

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "HTTPProxyConfigPreview"
```

Quelques minutes sont nécessaires pour que l’état s’affiche *Registered* (Inscrit). Vérifiez l’état de l’inscription à l’aide de la commande [az feature list][az-feature-list] :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/HTTPProxyConfigPreview')].{Name:name,State:properties.state}"
```

Lorsque vous êtes prêt, actualisez l’inscription du fournisseur de ressources *Microsoft.ContainerService* à l’aide de la commande [az provider register][az-provider-register] :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="configuring-an-http-proxy-using-azure-cli"></a>Configuration d’un proxy HTTP à l’aide d’Azure CLI 

Utilisez AKS avec un proxy HTTP au moment de la création du cluster, en utilisant la commande [az aks create][az-aks-create] et en passant la configuration sous forme d’un fichier JSON.

Le schéma du fichier config est du type suivant :

```json
{
  "httpProxy": "string",
  "httpsProxy": "string",
  "noProxy": [
    "string"
  ],
  "trustedCa&quot;: &quot;string"
}
```

`httpProxy` : URL de proxy à utiliser pour créer des connexions HTTP à l’extérieur du cluster. Le schéma d’URL doit être `http`.
`httpsProxy` : URL de proxy à utiliser pour créer des connexions HTTPS à l’extérieur du cluster. Si la valeur n’est pas spécifiée, `httpProxy` est utilisé à la fois pour les connexions HTTP et HTTPS.
`noProxy` : liste des noms de domaine de destination, domaines, adresses IP ou autres CIDR de réseau pour exclure le proxy.
`trustedCa` : chaîne avec le contenu de certificat d’autorité de certification de remplacement `base64 encoded`. Pour le moment, nous prenons uniquement en charge le format `PEM`. Il est également important de noter que dans un souci de compatibilité avec les composants basés sur Go qui font partie du système k8s, le certificat DOIT prendre en charge `Subject Alternative Names(SANs)` à la place des certificats de noms communs déconseillés.

Exemple d’entrée : notez que le certificat de l’autorité de certification doit être la chaîne encodée en base64 du contenu du certificat au format PEM.

```json
{
  "httpProxy": "http://myproxy.server.com:8080/", 
  "httpsProxy": "https://myproxy.server.com:8080/", 
  "noProxy": [
    "localhost",
    "127.0.0.1"
  ],
  "trustedCA": "LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUgvVENDQmVXZ0F3SUJB...b3Rpbk15RGszaWFyCkYxMFlscWNPbWVYMXVGbUtiZGkvWG9yR2xrQ29NRjNURHg4cm1wOURCaUIvCi0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0="
}
```

Créez un fichier et fournissez des valeurs pour *httpProxy*, *HttpsProxy* et *NoProxy*. Si votre environnement l’exige, fournissez également une valeur *trustedCa*. Ensuite, déployez un cluster, en passant votre nom de fichier au moyen de l’indicateur `http-proxy-config`.

```azurecli
az aks create -n $clusterName -g $resourceGroup --http-proxy-config aks-proxy-config.json
```

Votre cluster s’initialise avec le proxy HTTP configuré sur les nœuds.

## <a name="configuring-an-http-proxy-using-azure-resource-manager-arm-templates"></a>Configuration d’un proxy HTTP à l’aide de modèles ARM (Azure Resource Manager)

Le déploiement d’un cluster AKS avec un proxy HTTP configuré en utilisant un modèle ARM est simple. Le même schéma que celui utilisé pour le déploiement de l’interface CLI existe dans la définition `Microsoft.ContainerService/managedClusters` sous les propriétés :

```json
"properties": {
    ...,
    "httpProxyConfig": {
        "httpProxy": "string",
        "httpsProxy": "string",
        "noProxy": [
            "string"
        ],
        "trustedCa": "string"
    }
}
```

Dans votre modèle, fournissez des valeurs pour *httpProxy*, *HttpsProxy* et *NoProxy*. Si nécessaire, fournissez également une valeur pour « *trustedCa* ». Déployez le modèle. Votre cluster doit s’initialiser avec votre proxy HTTP configuré sur les nœuds.

## <a name="handling-ca-rollover"></a>Gestion du remplacement de l’autorité de certification

Les valeurs de *httpProxy*, *HttpsProxy* et *NoProxy* ne peuvent pas être changées après la création du cluster. Toutefois, pour prendre en charge le déploiement des certificats d’autorité de certification, la valeur de *trustedCa* peut être changée et appliquée au cluster avec la commande [az aks update][az-aks-update].

Par exemple, en supposant qu’un fichier *aks-proxy-config-2.json* a été créé avec la chaîne encodée en base64 du nouveau certificat d’autorité de certification, l’action suivante met à jour le cluster :

```azurecli
az aks update -n $clusterName -g $resourceGroup --http-proxy-config aks-proxy-config-2.json
```

## <a name="next-steps"></a>Étapes suivantes
- Pour plus d’informations sur les exigences de réseau pour les clusters AKS, consultez [Contrôler le trafic de sortie des nœuds de cluster dans AKS][aks-egress].


<!-- LINKS - internal -->
[aks-egress]: ./limit-egress-traffic.md
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-update]: /cli/azure/aks#az_aks_update
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az-extension-update
