---
title: Créer un contrôleur de données Azure Arc | Mode de connexion directe
description: Explique comment créer le contrôleur de données en mode de connexion directe.
author: dnethi
ms.author: dinethi
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 11/03/2021
ms.topic: overview
ms.openlocfilehash: 30c67a343be4b19d689df1e5faa2b72ed6ab83e3
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131562938"
---
#  <a name="create-azure-arc-data-controller-in-direct-connectivity-mode-using-cli"></a>Créer un contrôleur de données Azure Arc en mode de connectivité directe à l’aide de l’interface CLI

Cet article décrit comment créer le contrôleur de données Azure Arc en mode de connectivité **directe** à l’aide de l’interface CLI pendant la préversion actuelle de cette fonctionnalité. 


## <a name="complete-prerequisites"></a>Répondre aux prérequis

Avant de commencer, vérifiez que vous répondez aux prérequis dans [Déployer le contrôleur de données – Mode de connexion directe – Prérequis](create-data-controller-direct-prerequisites.md).

La création d’un contrôleur de données Azure Arc en mode de connectivité **directe** implique les étapes suivantes :

1. Créer une extension des services de données avec Azure Arc. 
1. Créer un emplacement personnalisé.
1. Créez le contrôleur de données.

> [!NOTE]
> Actuellement, cette étape peut être effectuée uniquement à partir du portail. Pour plus d’informations, consultez les [notes de publication](release-notes.md). 

## <a name="create-an-azure-arc-enabled-data-services-extension"></a>Créer une extension des services de données avec Azure Arc

Utilisez l’interface CLI k8s-extension pour créer une extension des services de données.

### <a name="set-environment-variables"></a>Définir des variables d’environnement

Définissez les variables d’environnement suivantes qui seront utilisées à la prochaine étape.

#### <a name="linux"></a>Linux

``` terminal
# where you want the connected cluster resource to be created in Azure 
export subscription=<Your subscription ID>
export resourceGroup=<Your resource group>
export resourceName=<name of your connected kubernetes cluster>
export location=<Azure location>
export AZDATA_LOGSUI_USERNAME=<username for Kibana dashboard>
export AZDATA_LOGSUI_PASSWORD=<password for Kibana dashboard>
export AZDATA_METRICSUI_USERNAME=<username for Grafana dashboard>
export AZDATA_METRICSUI_PASSWORD=<password for Grafana dashboard>
```

#### <a name="windows-powershell"></a>Windows PowerShell
``` PowerShell
# where you want the connected cluster resource to be created in Azure 
$ENV:subscription="<Your subscription ID>"
$ENV:resourceGroup="<Your resource group>"
$ENV:resourceName="<name of your connected kubernetes cluster>"
$ENV:location="<Azure location>"
$ENV:AZDATA_LOGSUI_USERNAME="<username for Kibana dashboard>"
$ENV:AZDATA_LOGSUI_PASSWORD="<password for Kibana dashboard>"
$ENV:AZDATA_METRICSUI_USERNAME="<username for Grafana dashboard>"
$ENV:AZDATA_METRICSUI_PASSWORD="<password for Grafana dashboard>"
```

### <a name="create-the-arc-data-services-extension"></a>Créer l’extension des services de données Arc

#### <a name="linux"></a>Linux

```bash
az k8s-extension create -c ${resourceName} -g ${resourceGroup} --name ${ADSExtensionName} --cluster-type connectedClusters --extension-type microsoft.arcdataservices --auto-upgrade false --scope cluster --release-namespace arc --config Microsoft.CustomLocation.ServiceAccount=sa-bootstrapper

az k8s-extension show -g ${resourceGroup} -c ${resourceName} --name ${ADSExtensionName} --cluster-type connectedclusters
```

#### <a name="windows-powershell"></a>Windows PowerShell
```PowerShell
$ENV:ADSExtensionName="ads-extension"

az k8s-extension create -c "$ENV:resourceName" -g "$ENV:resourceGroup" --name "$ENV:ADSExtensionName" --cluster-type connectedClusters --extension-type microsoft.arcdataservices --auto-upgrade false --scope cluster --release-namespace arc --config Microsoft.CustomLocation.ServiceAccount=sa-bootstrapper

az k8s-extension show -g "$ENV:resourceGroup" -c "$ENV:resourceName" --name "$ENV:ADSExtensionName" --cluster-type connectedclusters
```

#### <a name="deploy-azure-arc-data-services-extension-using-private-container-registry-and-credentials"></a>Déployer l’extension des services de données Azure Arc à l’aide du registre de conteneurs privé et des informations d’identification

Utilisez la commande ci-dessous si vous déployez à partir de votre référentiel privé :

```azurecli
az k8s-extension create -c "<connected cluster name>" -g "<resource group>" --name "<extension name>" --cluster-type connectedClusters --extension-type microsoft.arcdataservices --scope cluster --release-namespace "<namespace>" --config Microsoft.CustomLocation.ServiceAccount=sa-bootstrapper --config imageCredentials.registry=<registry info> --config imageCredentials.username=<username> --config systemDefaultValues.image=<registry/repo/arc-bootstrapper:<imagetag>> --config-protected imageCredentials.password=$ENV:DOCKER_PASSWORD --debug
```

 Par exemple
```azurecli
az k8s-extension create -c "my-connected-cluster" -g "my-resource-group" --name "arc-data-services" --cluster-type connectedClusters --extension-type microsoft.arcdataservices --scope cluster --release-namespace "arc" --config Microsoft.CustomLocation.ServiceAccount=sa-bootstrapper --config imageCredentials.registry=mcr.microsoft.com --config imageCredentials.username=arcuser --config systemDefaultValues.image=mcr.microsoft.com/arcdata/arc-bootstrapper:latest --config-protected imageCredentials.password=$ENV:DOCKER_PASSWORD --debug
```


> [!NOTE]
> L’installation de l’extension des services de données Arc peut prendre quelques minutes.

### <a name="verify-the-arc-data-services-extension-is-created"></a>Vérifier que l’extension des services de données Arc est créée

Vous pouvez vérifier que l’extension des services de données Azure Arc est créée, à partir du portail ou en vous connectant directement au cluster Kubernetes avec Azure Arc. 

#### <a name="azure-portal"></a>Portail Azure
1. Connectez-vous au portail Azure et accédez au groupe de ressources dans lequel se trouve la ressource de cluster connectée à Kubernetes.
1. Sélectionnez le cluster Kubernetes avec Azure Arc (Type = « Kubernetes - Azure Arc ») dans lequel l’extension a été déployée.
1. Dans la partie gauche du volet de navigation, sous **Paramètres**, sélectionnez **Extensions**.
1. Vous devez voir l’extension qui a été créée plus tôt dans un état installé.

:::image type="content" source="media/deploy-data-controller-direct-mode-prerequisites/dc-extensions-dashboard.png" alt-text="Tableau de bord des extensions":::

#### <a name="kubectl-cli"></a>Interface CLI kubectl

1. Connectez-vous à votre cluster Kubernetes au moyen d’une fenêtre de terminal.
1. Exécutez la commande ci-dessous et veillez à ce que (1) l’espace de noms mentionné plus haut soit créé et (2) que le pod `bootstrapper` se trouve dans l’état En cours d’exécution avant de passer à l’étape suivante.

``` console
kubectl get pods -n <name of namespace used in the json template file above>
```

Par exemple, l’exemple suivant obtient les pods de l’espace de noms `arc`.

```console
#Example:
kubectl get pods -n arc
```

## <a name="retrieve-the-managed-identity-and-grant-roles"></a>Récupérer l’identité managée et accorder des rôles

L’identité managée qui est créée lors de la création de l’extension des services de données Azure Arc doit se voir attribuer certains rôles pour l’utilisation et/ou les métriques à charger automatiquement.

### <a name="1-retrieve-managed-identity-of-the-arc-data-controller-extension"></a>(1) Récupérer l’identité managée de l’extension du contrôleur de données Azure Arc

```powershell
$Env:MSI_OBJECT_ID = (az k8s-extension show --resource-group <resource group>  --cluster-name <connectedclustername> --cluster-type connectedClusters --name <name of extension> | convertFrom-json).identity.principalId
#Example
$Env:MSI_OBJECT_ID = (az k8s-extension show --resource-group myresourcegroup  --cluster-name myconnectedcluster --cluster-type connectedClusters --name ads-extension | convertFrom-json).identity.principalId
```

### <a name="2-assign-role-to-the-managed-identity"></a>(2) Attribuer un rôle à l’identité managée

Exécutez la commande ci-dessous pour attribuer le rôle **Publication des métriques de surveillance** :
```powershell
az role assignment create --assignee $Env:MSI_OBJECT_ID --role 'Monitoring Metrics Publisher' --scope "/subscriptions/$SUBSCRIPTION_ID/resourceGroups/$RESOURCE_GROUP_NAME"

```

## <a name="create-a-custom-location-using-custom-location-cli-extension"></a>Créer un emplacement personnalisé à l’aide de l’extension CLI d’emplacement personnalisé

Un emplacement personnalisé est une ressource Azure comparable à un espace de noms dans un cluster Kubernetes.  Les emplacements personnalisés sont utilisés en tant que cibles pour déployer des ressources vers ou à partir d’Azure. Apprenez-en davantage sur les emplacements personnalisés dans la [documentation relative aux emplacements personnalisés s’appuyant sur Kubernetes avec Azure Arc](../kubernetes/conceptual-custom-locations.md).

### <a name="set-environment-variables"></a>Définir des variables d’environnement

#### <a name="linux"></a>Linux

```bash
export clName=mycustomlocation
export clNamespace=arc
export hostClusterId=$(az connectedk8s show -g ${resourceGroup} -n ${resourceName} --query id -o tsv)
export extensionId=$(az k8s-extension show -g ${resourceGroup} -c ${resourceName} --cluster-type connectedClusters --name ${ADSExtensionName} --query id -o tsv)

az customlocation create -g ${resourceGroup} -n ${clName} --namespace ${clNamespace} \
  --host-resource-id ${hostClusterId} \
  --cluster-extension-ids ${extensionId} --location eastus
```

#### <a name="windows-powershell"></a>Windows PowerShell
```PowerShell
$ENV:clName="mycustomlocation"
$ENV:clNamespace="arc"
$ENV:hostClusterId = az connectedk8s show -g "$ENV:resourceGroup" -n "$ENV:resourceName" --query id -o tsv
$ENV:extensionId = az k8s-extension show -g "$ENV:resourceGroup" -c "$ENV:resourceName" --cluster-type connectedClusters --name "$ENV:ADSExtensionName" --query id -o tsv

az customlocation create -g "$ENV:resourceGroup" -n "$ENV:clName" --namespace "$ENV:clNamespace" --host-resource-id "$ENV:hostClusterId" --cluster-extension-ids "$ENV:extensionId"
```

## <a name="validate--the-custom-location-is-created"></a>Valider la création de l’emplacement personnalisé

À partir du terminal, exécutez la commande ci-dessous pour lister les emplacements personnalisés, puis vérifiez que l’**État de provisionnement** indique Opération réussie :

```azurecli
az customlocation list -o table
```

## <a name="create-the-azure-arc-data-controller"></a>Créer le contrôleur de données Azure Arc

Après avoir créé l’extension et l’emplacement personnalisé, procédez au déploiement du contrôleur de données Azure Arc comme suit.

```
az arcdata dc create --name <name> --resource-group <resourcegroup> --location <location> --connectivity-mode direct --profile-name <profile name>  --auto-upload-logs true --custom-location <name of custom location>
# Example
az arcdata dc create -n arc-dc1 --resource-group my-resource-group --location eastasia --connectivity-mode direct --profile-name azure-arc-aks-premium-storage  --auto-upload-logs true --custom-location mycustomlocation
```

Si vous souhaitez créer le contrôleur de données Azure Arc à l’aide d’un modèle de configuration personnalisé, suivez les étapes décrites dans [Créer un profil de configuration personnalisée](create-custom-configuration-template.md) et indiquez le chemin d’accès au fichier comme suit :

```
az arcdata dc create --name <name> --resource-group <resourcegroup> --location <location> --connectivity-mode direct --path ./azure-arc-custom  --auto-upload-logs true --custom-location <name of custom location>
# Example
az arcdata dc create --name arc-dc1 --resource-group my-resource-group --location eastasia --connectivity-mode direct --path ./azure-arc-custom  --auto-upload-logs true --custom-location mycustomlocation
```


## <a name="monitor-the-creation"></a>Superviser la création

Lorsque l’état du déploiement sur le portail Azure indique que le déploiement a réussi, vous pouvez vérifier l’état du déploiement du contrôleur de données Arc sur le cluster, de la manière suivante :

```console
kubectl get datacontrollers -n arc
```

## <a name="next-steps"></a>Étapes suivantes

[Créer un groupe de serveurs PostgreSQL Hyperscale avec Azure Arc](create-postgresql-hyperscale-server-group.md)

[Créer une instance gérée Azure SQL sur Azure Arc](create-sql-managed-instance.md)
