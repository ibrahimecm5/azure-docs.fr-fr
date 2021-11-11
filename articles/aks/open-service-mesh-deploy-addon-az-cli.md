---
title: Déployer Open Service Mesh
description: Déployer Open Service Mesh sur Azure Kubernetes Service (AKS) avec Azure CLI
services: container-service
ms.topic: article
ms.date: 8/26/2021
ms.custom: mvc, devx-track-azurecli
ms.author: pgibson
ms.openlocfilehash: cf89b21c3aceee55e121d918f21db4bcf7c51d42
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131440485"
---
# <a name="deploy-the-open-service-mesh-aks-add-on-using-azure-cli"></a>Déployer le module complémentaire AKS Open Service Mesh avec Azure CLI

Cet article explique comment déployer le module complémentaire OSM sur AKS.

## <a name="prerequisites"></a>Prérequis

- Azure CLI, version 2.20.0 ou ultérieure
- OSM version 0.11.1 ou ultérieure

## <a name="install-open-service-mesh-osm-azure-kubernetes-service-aks-add-on-for-a-new-aks-cluster"></a>Installer le module complémentaire Open Service Mesh (OSM) Azure Kubernetes Service (AKS) pour un nouveau cluster AKS

Pour le scénario de déploiement d’un nouveau cluster AKS, vous commencerez par un tout nouveau déploiement de cluster AKS en activant le module complémentaire OSM lors de l’opération de création du cluster.

### <a name="create-a-resource-group"></a>Créer un groupe de ressources

Dans Azure, vous allouez les ressources associées à un groupe de ressources. Créez un groupe de ressources à l’aide de la commande [az group create](/cli/azure/group#az_group_create). L’exemple suivant est utilisé pour créer un groupe de ressources à un emplacement spécifié (région) :

```azurecli-interactive
az group create --name <my-osm-aks-cluster-rg> --location <azure-region>
```

### <a name="deploy-an-aks-cluster-with-the-osm-add-on-enabled"></a>Déployer un cluster AKS avec le module complémentaire OSM activé

Déployez ensuite un nouveau cluster AKS avec le module complémentaire OSM activé.

> [!NOTE]
> Notez que la commande de déploiement AKS suivante utilise des disques de système d’exploitation éphémères. Vous trouverez plus d’informations ici sur [les disques de système d’exploitation éphémères pour AKS](./cluster-configuration.md#ephemeral-os).

```azurecli-interactive
az aks create -n <my-osm-aks-cluster-name> -g <my-osm-aks-cluster-rg> --node-osdisk-type Ephemeral --node-osdisk-size 30 --network-plugin azure --enable-managed-identity -a open-service-mesh
```

#### <a name="get-aks-cluster-access-credentials"></a>Obtenir les informations d’identification d’accès au cluster AKS

Obtenez les informations d’identification d’accès pour le nouveau cluster Kubernetes managé.

```azurecli-interactive
az aks get-credentials -n <my-osm-aks-cluster-name> -g <my-osm-aks-cluster-rg>
```

## <a name="enable-open-service-mesh-osm-azure-kubernetes-service-aks-add-on-for-an-existing-aks-cluster"></a>Activer le module complémentaire Open Service Mesh (OSM) Azure Kubernetes Service (AKS) pour un cluster AKS existant

Pour un scénario de cluster AKS existant, vous allez activer le module complémentaire OSM sur un cluster AKS qui a déjà été déployé.

### <a name="enable-the-osm-add-on-to-existing-aks-cluster"></a>Activer le module complémentaire OSM sur un cluster AKS existant

Pour activer le module complémentaire AKS OSM, vous devez exécuter la commande `az aks enable-addons --addons` en transmettant le paramètre `open-service-mesh`.

> [!NOTE]
> Pour que le déploiement du module complémentaire OSM aboutisse, une seule instance de maillage OSM doit être déployée sur votre cluster. Si vous avez d’autres instances de maillage OSM sur votre cluster, désinstallez-les avant d’exécuter la commande `enable-addons`.

```azurecli-interactive
az aks enable-addons --addons open-service-mesh -g <my-osm-aks-cluster-rg> -n <my-osm-aks-cluster-name>
```

Vous devez voir une sortie similaire à la sortie ci-dessous pour confirmer que le module complémentaire AKS OSM a été installé.

```json
{- Finished ..
  "aadProfile": null,
  "addonProfiles": {
    "KubeDashboard": {
      "config": null,
      "enabled": false,
      "identity": null
    },
    "openServiceMesh": {
      "config": {},
      "enabled": true,
      "identity": {
...
```

## <a name="validate-the-aks-osm-add-on-installation"></a>Valider l’installation du module complémentaire AKS OSM

Plusieurs commandes doivent être exécutées pour vérifier que tous les composants du module complémentaire AKS OSM sont activés et en cours d’exécution :

Tout d’abord, nous pouvons interroger les profils de module complémentaire du cluster pour vérifier l’état d’activation des modules complémentaires installés. La commande suivante doit renvoyer la valeur « true ».

```azurecli-interactive
az aks list -g <my-osm-aks-cluster-rg> -o json | jq -r '.[].addonProfiles.openServiceMesh.enabled'
```

Les commandes `kubectl` suivantes signalent l’état d’osm-controller.

```azurecli-interactive
kubectl get deployments -n kube-system --selector app=osm-controller
kubectl get pods -n kube-system --selector app=osm-controller
kubectl get services -n kube-system --selector app=osm-controller
```

### <a name="check-osm-add-on-version"></a>Vérifier la version du module complémentaire OSM

La version du module complémentaire OSM installée doit être v0.11.1 ou une version ultérieure. Pour vérifier cela, vous pouvez exécuter la commande suivante pour vérifier la version de l’image du contrôleur OSM, qui est encodé dans l’étiquette de l’image : 

```azurecli-interactive
kubectl get deployment -n kube-system osm-controller -o=jsonpath='{$.spec.template.spec.containers[:1].image}'
```

## <a name="accessing-the-aks-osm-add-on-configuration"></a>Accès à la configuration du module complémentaire AKS OSM

Actuellement, vous pouvez configurer le contrôleur OSM et accéder à sa configuration par le biais de la ressource OSM MeshConfig. Pour voir les paramètres de configuration du contrôleur OSM par le biais de l’interface CLI, utilisez la commande **kubectl** get, comme indiqué ci-dessous.

```azurecli-interactive
kubectl get meshconfig osm-mesh-config -n kube-system -o yaml
```

La sortie de MeshConfig doit se ressembler à ceci :

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

> [!WARNING]
> Avant de continuer, vérifiez que votre mode de stratégie de trafic permissif est défini sur true. Si ce n’est pas le cas, définissez-le sur **true** à l’aide de la commande ci-dessous.

```OSM Permissive Mode to True
kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"traffic":{"enablePermissiveTrafficPolicyMode":true}}}' --type=merge
```

## <a name="disable-open-service-mesh-osm-add-on-for-your-aks-cluster"></a>Désactiver le module complémentaire Open Service Mesh (OSM) pour votre cluster AKS

Pour désactiver le module complémentaire OSM, exécutez la commande suivante :

```azurecli-interactive
az aks disable-addons -n <AKS-cluster-name> -g <AKS-resource-group-name> -a open-service-mesh
```

<!-- Links -->
<!-- Internal -->

[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
