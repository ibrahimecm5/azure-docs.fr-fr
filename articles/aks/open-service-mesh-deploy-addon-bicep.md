---
title: Déployer le module complémentaire Open Service Mesh AKS à l’aide de Bicep
description: Déployer Open Service Mesh sur Azure Kubernetes Service (AKS) avec Bicep
services: container-service
ms.topic: article
ms.date: 9/20/2021
ms.custom: mvc, devx-track-azurecli
ms.author: pgibson
ms.openlocfilehash: b0452588b8eb9eebbf8ab592ad2d0a8a0e9a6b92
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131066783"
---
# <a name="deploy-open-service-mesh-osm-azure-kubernetes-service-aks-add-on-using-bicep"></a>Déployer le module complémentaire Open Service Mesh (OSM) Azure Kubernetes Service (AKS) à l’aide de Bicep

Cet article explique comment déployer le module complémentaire OSM sur AKS à l’aide d’un modèle [Bicep](../azure-resource-manager/bicep/index.yml).

[Bicep](/azure/azure-resource-manager/bicep/overview) est un langage spécifique à un domaine (DSL) qui utilise la syntaxe déclarative pour déployer des ressources Azure. Bicep peut être utilisé à la place de la création de modèles [ARM](/azure/azure-resource-manager/templates/overview) pour déployer vos ressources Azure infrastructure-as-code.

## <a name="prerequisites"></a>Prérequis

- Azure CLI, version 2.20.0 ou ultérieure
- OSM version 0.11.1 ou ultérieure
- Une clé publique SSH utilisée pour le déploiement d’AKS
- [Visual Studio Code](https://code.visualstudio.com/) utilisant un terminal Bash
- L’[extension Bicep](../azure-resource-manager/bicep/install.md) pour Visual Studio Code

## <a name="install-the-aks-preview-extension"></a>Installer l’extension aks-preview

Vous aurez besoin de la version 0.5.24 ou d’une version ultérieure de l’extension Azure CLI _aks-preview_. Installez l’extension d’Azure CLI _aks-preview_ à l’aide de la commande [az extension add][az-extension-add]. Ou installez toutes les mises à jour disponibles à l’aide de la commande [az extension update][az-extension-update].

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

## <a name="register-the-aks-openservicemesh-preview-feature"></a>Inscrire la fonctionnalité d’évaluation `AKS-OpenServiceMesh`

Pour créer un cluster AKS capable d’utiliser le module complémentaire Open Service Mesh, vous devez activer l’indicateur de fonctionnalité `AKS-OpenServiceMesh` sur votre abonnement.

Inscrivez l’indicateur de fonctionnalité `AKS-OpenServiceMesh` à l’aide de la commande [az feature register][az-feature-register], comme indiqué dans l’exemple suivant :

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "AKS-OpenServiceMesh"
```

Quelques minutes sont nécessaires pour que l’état s’affiche _Registered_ (Inscrit). Vérifiez l’état de l’inscription à l’aide de la commande [az feature list][az-feature-list] :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKS-OpenServiceMesh')].{Name:name,State:properties.state}"
```

Lorsque vous êtes prêt, actualisez l’inscription du fournisseur de ressources _Microsoft.ContainerService_ à l’aide de la commande [az provider register][az-provider-register] :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="install-the-osm-aks-add-on-for-a-new-aks-cluster-using-bicep"></a>Installer le module complémentaire OSM AKS pour un nouveau cluster AKS à l’aide de Bicep

Pour le scénario de déploiement d’un nouveau cluster AKS, commencez par un tout nouveau déploiement de cluster AKS avec le module complémentaire OSM activé lors de l’opération de création du cluster. Les instructions suivantes utilisent un modèle Bicep générique qui déploie un cluster AKS en utilisant des disques éphémères, en utilisant le CNI [`kubenet`](./configure-kubenet.md) et en activant le module complémentaire AKS OSM. Pour des scénarios de déploiement plus avancés, consultez la documentation de [Bicep](../azure-resource-manager/bicep/overview.md).

### <a name="create-a-resource-group"></a>Créer un groupe de ressources

Dans Azure, vous pouvez associer des ressources connexes à l’aide d’un groupe de ressources. Créez un groupe de ressources à l’aide de la commande [az group create](/cli/azure/group#az_group_create). L’exemple suivant est utilisé pour créer un groupe de ressources nommé à un emplacement Azure spécifié (région) :

```azurecli-interactive
az group create --name <my-osm-bicep-aks-cluster-rg> --location <azure-region>
```

### <a name="create-the-main-and-parameters-bicep-files"></a>Créer les fichiers Bicep main et parameters

En utilisant Visual Studio Code avec un terminal Bash ouvert, créez un répertoire pour stocker les fichiers de déploiement Bicep nécessaires. L’exemple suivant crée un répertoire nommé `bicep-osm-aks-addon` et apporte des modifications au répertoire.

```azurecli-interactive
mkdir bicep-osm-aks-addon
cd bicep-osm-aks-addon
```

Créez ensuite les fichiers main et parameters, comme indiqué dans l’exemple suivant.

```azurecli-interactive
touch osm.aks.bicep && touch osm.aks.parameters.json
```

Ouvrez le fichier `osm.aks.bicep` et copiez-y le contenu de l’exemple suivant, puis enregistrez le fichier.

```azurecli-interactive
// https://docs.microsoft.com/azure/aks/troubleshooting#what-naming-restrictions-are-enforced-for-aks-resources-and-parameters
@minLength(3)
@maxLength(63)
@description('Provide a name for the AKS cluster. The only allowed characters are letters, numbers, dashes, and underscore. The first and last character must be a letter or a number.')
param clusterName string
@minLength(3)
@maxLength(54)
@description('Provide a name for the AKS dnsPrefix. Valid characters include alphanumeric values and hyphens (-). The dnsPrefix can\'t include special characters such as a period (.)')
param clusterDNSPrefix string
param k8Version string
param sshPubKey string


resource aksCluster 'Microsoft.ContainerService/managedClusters@2021-03-01' = {
  name: clusterName
  location: resourceGroup().location
  identity: {
    type: 'SystemAssigned'
  }
  properties: {
    kubernetesVersion: k8Version
    dnsPrefix: clusterDNSPrefix
    enableRBAC: true
    agentPoolProfiles: [
      {
        name: 'agentpool'
        count: 3
        vmSize: 'Standard_DS2_v2'
        osDiskSizeGB: 30
        osDiskType: 'Ephemeral'
        osType: 'Linux'
        mode: 'System'
      }
    ]
    linuxProfile: {
      adminUsername: 'adminUserName'
      ssh: {
        publicKeys: [
          {
            keyData: sshPubKey
          }
        ]
      }
    }
    addonProfiles: {
        openServiceMesh: {
            enabled: true
            config: {}
      }
    }
  }
}
```

Ouvrez le fichier `osm.aks.parameters.json` et copiez-y le contenu de l’exemple suivant. Ajoutez les paramètres spécifiques au déploiement, puis enregistrez le fichier.

> [!NOTE]
> Le fichier `osm.aks.parameters.json` est un exemple de modèle de fichier parameters nécessaire pour le déploiement de Bicep. Vous devrez mettre à jour les paramètres mentionnés spécifiquement pour votre environnement de déploiement. Les valeurs de paramètre spécifiques utilisées dans cet exemple nécessitent la mise à jour des paramètres suivants. Il s’agit de _clusterName_, _clusterDNSPrefix_, _k8Version_ et _sshPubKey_. Pour obtenir la liste des versions Kubernetes prises en charge dans votre région, utilisez la commande `az aks get-versions --location <region>`.

```azurecli-interactive
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "clusterName": {
      "value": "<YOUR CLUSTER NAME HERE>"
    },
    "clusterDNSPrefix": {
      "value": "<YOUR CLUSTER DNS PREFIX HERE>"
    },
    "k8Version": {
      "value": "<YOUR SUPPORTED KUBERNETES VERSION HERE>"
    },
    "sshPubKey": {
      "value": "<YOUR SSH KEY HERE>"
    }
  }
}
```

### <a name="deploy-the-bicep-file"></a>Déployer le fichier Bicep

Pour déployer les fichiers Bicep créés précédemment, ouvrez le terminal et authentifiez-vous auprès de votre compte Azure pour Azure CLI à l’aide de la commande `az login`. Une fois que vous êtes authentifié auprès de votre abonnement Azure, exécutez les commandes suivantes pour le déploiement.

```azurecli-interactive
az group create --name osm-bicep-test --location eastus2

az deployment group create \
  --name OSMBicepDeployment \
  --resource-group osm-bicep-test \
  --template-file osm.aks.bicep \
  --parameters @osm.aks.parameters.json
```

Une fois le déploiement terminé, un message doit s’afficher pour indiquer que le déploiement a réussi.

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

## <a name="accessing-the-aks-osm-add-on-configuration"></a>Accès à la configuration du module complémentaire AKS OSM

Actuellement, vous pouvez accéder à la configuration du contrôleur OSM et la paramétrer par l’intermédiaire de la ressource OSM MeshConfig. Vous pouvez afficher les paramètres de configuration du contrôleur OSM par l’intermédiaire de l’interface CLI à l’aide de la commande get **kubectl** comme indiqué ci-dessous.

```azurecli-interactive
kubectl get meshconfig osm-mesh-config -n kube-system -o yaml
```

La sortie de la MeshConfig est illustrée ci-dessous :

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

Notez que le paramètre **enablePermissiveTrafficPolicyMode** est configuré sur **true**. Le mode de stratégie de trafic permissif dans OSM est un mode dans lequel l’application de la stratégie de trafic [SMI](https://smi-spec.io/) est ignorée. Dans ce mode, OSM découvre automatiquement les services qui font partie du maillage de services. Les services découverts auront des règles de stratégie de trafic programmées sur chaque proxy side-car Envoy pour permettre les communications entre ces services.

> [!WARNING]
> Avant de continuer, vérifiez que votre mode de stratégie de trafic permissif est défini sur true. Si ce n’est pas le cas, définissez-le sur **true** à l’aide de la commande ci-dessous.

```OSM Permissive Mode to True
kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"traffic":{"enablePermissiveTrafficPolicyMode":true}}}' --type=merge
```

## <a name="clean-up-resources"></a>Nettoyer les ressources

Lorsque les ressources Azure ne sont plus nécessaires, utilisez Azure CLI pour supprimer le groupe de ressources de test de déploiement.

```
az group delete --name osm-bicep-test
```

<!-- Links -->
<!-- Internal -->

[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
