---
title: Configurer Azure Arc pour App Service, Functions et Logic Apps
description: Pour vos clusters Kubernetes avec Azure Arc, découvrez comment activer des applications App Service, des applications de fonction et des applications logiques.
ms.topic: article
ms.date: 11/02/2021
ms.openlocfilehash: a330d68ed556a60261ca91e6bfb32fdddf52dc14
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131435583"
---
# <a name="set-up-an-azure-arc-enabled-kubernetes-cluster-to-run-app-service-functions-and-logic-apps-preview"></a>Configurer un cluster Kubernetes activé pour Azure Arc pour exécuter App Service, Functions et Logic Apps (préversion)

Si vous disposez d’un [cluster Kubernetes avec Azure Arc](../azure-arc/kubernetes/overview.md), vous pouvez l’utiliser pour créer un [emplacement personnalisé avec App Service](overview-arc-integration.md) et y déployer des applications web, des applications de fonction et des applications logiques.

Kubernetes avec Azure Arc vous permet de rendre votre cluster Kubernetes local ou cloud visible pour App Service, Functions et Logic Apps dans Azure. Vous pouvez créer une application et la déployer comme une autre région Azure.

## <a name="prerequisites"></a>Prérequis

Si vous n’avez pas encore de compte Azure, [inscrivez-vous aujourd’hui](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-app-service-extension&mktingSource=vscode-tutorial-app-service-extension) pour bénéficier d’un compte gratuit.

<!-- ## Prerequisites

- Create a Kubernetes cluster in a supported Kubernetes distribution and connect it to Azure Arc in a supported region. See [Public preview limitations](overview-arc-integration.md#public-preview-limitations).
- [Install Azure CLI](/cli/azure/install-azure-cli), or use the [Azure Cloud Shell](../cloud-shell/overview.md).
- [Install kubectl](https://kubernetes.io/docs/tasks/tools/). It's also preinstalled in the Azure Cloud Shell.

## Obtain cluster information

Set the following environment variables based on your Kubernetes cluster deployment:

```bash
aksClusterGroupName="<name-resource-group-with-aks-cluster>"
groupName="<name-of-resource-group-with-the-arc-connected-cluster>"
clusterName="<name-of-arc-connected-cluster>"
geomasterLocation="TODO: Why so many different locations for different resources? Shouldn't we just say create everything in the connected cluster's resource group and location?"
``` -->

## <a name="add-azure-cli-extensions"></a>Ajouter des extensions Azure CLI

Lancez l’environnement Bash dans [Azure Cloud Shell](../cloud-shell/quickstart.md).

[![Lancer Cloud Shell dans une nouvelle fenêtre](../../includes/media/cloud-shell-try-it/hdi-launch-cloud-shell.png)](https://shell.azure.com)

Comme ces commandes CLI ne font pas encore partie de l’ensemble principal des commandes CLI, ajoutez-les avec les commandes suivantes.

```azurecli-interactive
az extension add --upgrade --yes --name connectedk8s
az extension add --upgrade --yes --name k8s-extension
az extension add --upgrade --yes --name customlocation
az provider register --namespace Microsoft.ExtendedLocation --wait
az provider register --namespace Microsoft.Web --wait
az provider register --namespace Microsoft.KubernetesConfiguration --wait
az extension remove --name appservice-kube
az extension add --upgrade --yes --name appservice-kube
```

## <a name="create-a-connected-cluster"></a>Créer un cluster connecté

> [!NOTE]
> Ce didacticiel utilise [Azure Kubernetes Service (AKS)](../aks/index.yml) pour fournir des instructions concrètes pour la configuration d’un environnement à partir de zéro. Toutefois, dans le cas d’une charge de travail de production, vous ne souhaiterez probablement pas activer Azure Arc sur un cluster AKS, car il est déjà géré dans Azure. Les étapes ci-dessous vous aideront à comprendre le service, mais pour les déploiements de production, elles doivent être affichées sous forme d’illustration et non de manière normative. Consultez [Démarrage rapide : Connecter un cluster Kubernetes existant à Azure Arc](../azure-arc/kubernetes/quickstart-connect-cluster.md) pour obtenir des instructions générales sur la création d’un cluster Kubernetes compatible avec Azure Arc.

1. Créez un cluster dans Azure Kubernetes Service avec une adresse IP publique. Remplacez `<group-name>` par le nom de groupe de ressources souhaité.

    # <a name="bash"></a>[bash](#tab/bash)

    ```azurecli-interactive
    aksClusterGroupName="<group-name>" # Name of resource group for the AKS cluster
    aksName="${aksClusterGroupName}-aks" # Name of the AKS cluster
    resourceLocation="eastus" # "eastus" or "westeurope"

    az group create -g $aksClusterGroupName -l $resourceLocation
    az aks create --resource-group $aksClusterGroupName --name $aksName --enable-aad --generate-ssh-keys
    infra_rg=$(az aks show --resource-group $aksClusterGroupName --name $aksName --output tsv --query nodeResourceGroup)
    ```

    # <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```powershell
    $aksClusterGroupName="<group-name>" # Name of resource group for the AKS cluster
    $aksName="${aksClusterGroupName}-aks" # Name of the AKS cluster
    $resourceLocation="eastus" # "eastus" or "westeurope"

    az group create -g $aksClusterGroupName -l $resourceLocation
    az aks create --resource-group $aksClusterGroupName --name $aksName --enable-aad --generate-ssh-keys
    ```

    ---
    
2. Récupérez le fichier [kubeconfig](https://kubernetes.io/docs/concepts/configuration/organize-cluster-access-kubeconfig/) et testez votre connexion au cluster. Par défaut, le fichier kubeconfig est enregistré dans `~/.kube/config`.

    ```azurecli-interactive
    az aks get-credentials --resource-group $aksClusterGroupName --name $aksName --admin
    
    kubectl get ns
    ```
    
3. Créez un groupe de ressources pour accueillir vos ressources Azure Arc. Remplacez `<group-name>` par le nom de groupe de ressources souhaité.

    # <a name="bash"></a>[bash](#tab/bash)

    ```azurecli-interactive
    groupName="<group-name>" # Name of resource group for the connected cluster

    az group create -g $groupName -l $resourceLocation
    ```

    # <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```powershell
    $groupName="<group-name>" # Name of resource group for the connected cluster

    az group create -g $groupName -l $resourceLocation
    ```

    ---
    
4. Connectez le cluster que vous avez créé à Azure Arc.

    # <a name="bash"></a>[bash](#tab/bash)

    ```azurecli-interactive
    clusterName="${groupName}-cluster" # Name of the connected cluster resource

    az connectedk8s connect --resource-group $groupName --name $clusterName
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)


    ```powershell
    $clusterName="${groupName}-cluster" # Name of the connected cluster resource

    az connectedk8s connect --resource-group $groupName --name $clusterName
    ```

    ---
    
5. Validez la connexion à l’aide de la commande suivante. Elle devrait afficher la propriété `provisioningState` avec la valeur `Succeeded`. Si ce n’est pas le cas, réexécutez la commande après une minute.

    ```azurecli-interactive
    az connectedk8s show --resource-group $groupName --name $clusterName
    ```
    
## <a name="create-a-log-analytics-workspace"></a>Créer un espace de travail Log Analytics

Bien qu’un [espace de travail Log Analytics](../azure-monitor/logs/quick-create-workspace.md) ne soit pas requis pour exécuter App service dans Azure Arc, il s’agit de la manière dont les développeurs peuvent obtenir des journaux d’applications pour leurs applications s’exécutant dans le cluster Kubernetes avec Azure Arc. 

1. Par souci de simplicité, créez l’espace de travail maintenant.

    # <a name="bash"></a>[bash](#tab/bash)

    ```azurecli-interactive
    workspaceName="$groupName-workspace" # Name of the Log Analytics workspace
    
    az monitor log-analytics workspace create \
        --resource-group $groupName \
        --workspace-name $workspaceName
    ```

    # <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```powershell
    $workspaceName="$groupName-workspace"

    az monitor log-analytics workspace create `
        --resource-group $groupName `
        --workspace-name $workspaceName
    ```

    ---
    
2. Exécutez les commandes suivantes pour obtenir l’ID d’espace de travail encodé et la clé partagée pour un espace de travail Log Analytics existant. Vous aurez besoin de ces informations à l’étape suivante.

    # <a name="bash"></a>[bash](#tab/bash)

    ```azurecli-interactive
    logAnalyticsWorkspaceId=$(az monitor log-analytics workspace show \
        --resource-group $groupName \
        --workspace-name $workspaceName \
        --query customerId \
        --output tsv)
    logAnalyticsWorkspaceIdEnc=$(printf %s $logAnalyticsWorkspaceId | base64 -w0) # Needed for the next step
    logAnalyticsKey=$(az monitor log-analytics workspace get-shared-keys \
        --resource-group $groupName \
        --workspace-name $workspaceName \
        --query primarySharedKey \
        --output tsv)
    logAnalyticsKeyEnc=$(printf %s $logAnalyticsKey | base64 -w0) # Needed for the next step
    ```

    # <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```powershell
    $logAnalyticsWorkspaceId=$(az monitor log-analytics workspace show `
        --resource-group $groupName `
        --workspace-name $workspaceName `
        --query customerId `
        --output tsv)
    $logAnalyticsWorkspaceIdEnc=[Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes($logAnalyticsWorkspaceId))# Needed for the next step
    $logAnalyticsKey=$(az monitor log-analytics workspace get-shared-keys `
        --resource-group $groupName `
        --workspace-name $workspaceName `
        --query primarySharedKey `
        --output tsv)
    $logAnalyticsKeyEnc=[Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes($logAnalyticsKey))
    ```
    
    ---

## <a name="install-the-app-service-extension"></a>Installer l’extension App Service

1. Définissez les variables d’environnement suivantes pour le nom souhaité de l’[extension App service](overview-arc-integration.md), l’espace de noms de cluster dans lequel les ressources doivent être approvisionnées, et le nom de l’environnement Kubernetes App service. Choisissez un nom unique pour `<kube-environment-name>`, car il fera partie du nom de domaine pour l’application créée dans l’environnement Kubernetes App Service.

    # <a name="bash"></a>[bash](#tab/bash)

    ```bash
    extensionName="appservice-ext" # Name of the App Service extension
    namespace="appservice-ns" # Namespace in your cluster to install the extension and provision resources
    kubeEnvironmentName="<kube-environment-name>" # Name of the App Service Kubernetes environment resource
    ```

    # <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```powershell
    $extensionName="appservice-ext" # Name of the App Service extension
    $namespace="appservice-ns" # Namespace in your cluster to install the extension and provision resources
    $kubeEnvironmentName="<kube-environment-name>" # Name of the App Service Kubernetes environment resource
    ```

    ---
    
2. Installez l’extension App Service sur votre cluster connecté Azure Arc, avec Log Analytics activé. Là encore, si Log Analytics n’est pas requis, vous ne pouvez pas l’ajouter à l’extension ultérieurement. Il est donc plus facile de le faire maintenant.

    # <a name="bash"></a>[bash](#tab/bash)

    ```azurecli-interactive
    az k8s-extension create \
        --resource-group $groupName \
        --name $extensionName \
        --cluster-type connectedClusters \
        --cluster-name $clusterName \
        --extension-type 'Microsoft.Web.Appservice' \
        --release-train stable \
        --auto-upgrade-minor-version true \
        --scope cluster \
        --release-namespace $namespace \
        --configuration-settings "Microsoft.CustomLocation.ServiceAccount=default" \
        --configuration-settings "appsNamespace=${namespace}" \
        --configuration-settings "clusterName=${kubeEnvironmentName}" \
        --configuration-settings "keda.enabled=true" \
        --configuration-settings "buildService.storageClassName=default" \
        --configuration-settings "buildService.storageAccessMode=ReadWriteOnce" \
        --configuration-settings "customConfigMap=${namespace}/kube-environment-config" \
        --configuration-settings "envoy.annotations.service.beta.kubernetes.io/azure-load-balancer-resource-group=${aksClusterGroupName}" \
        --configuration-settings "logProcessor.appLogs.destination=log-analytics" \
        --configuration-protected-settings "logProcessor.appLogs.logAnalyticsConfig.customerId=${logAnalyticsWorkspaceIdEnc}" \
        --configuration-protected-settings "logProcessor.appLogs.logAnalyticsConfig.sharedKey=${logAnalyticsKeyEnc}"
    ```

    # <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```powershell
    az k8s-extension create `
        --resource-group $groupName `
        --name $extensionName `
        --cluster-type connectedClusters `
        --cluster-name $clusterName `
        --extension-type 'Microsoft.Web.Appservice' `
        --release-train stable `
        --auto-upgrade-minor-version true `
        --scope cluster `
        --release-namespace $namespace `
        --configuration-settings "Microsoft.CustomLocation.ServiceAccount=default" `
        --configuration-settings "appsNamespace=${namespace}" `
        --configuration-settings "clusterName=${kubeEnvironmentName}" `
        --configuration-settings "keda.enabled=true" `
        --configuration-settings "buildService.storageClassName=default" `
        --configuration-settings "buildService.storageAccessMode=ReadWriteOnce" `
        --configuration-settings "customConfigMap=${namespace}/kube-environment-config" `
        --configuration-settings "envoy.annotations.service.beta.kubernetes.io/azure-load-balancer-resource-group=${aksClusterGroupName}" `
        --configuration-settings "logProcessor.appLogs.destination=log-analytics" `
        --configuration-protected-settings "logProcessor.appLogs.logAnalyticsConfig.customerId=${logAnalyticsWorkspaceIdEnc}" `
        --configuration-protected-settings "logProcessor.appLogs.logAnalyticsConfig.sharedKey=${logAnalyticsKeyEnc}"
    ```

    ---

    > [!NOTE]
    > Pour installer l’extension sans l’intégration de Log Analytics, supprimez les trois paramètres `--configuration-settings` de la commande.
    >

    Le tableau suivant décrit les différents paramètres `--configuration-settings` inclus lors de l’exécution de la commande :
    
    | Paramètre | Description |
    | - | - |
    | `Microsoft.CustomLocation.ServiceAccount` | Compte de service qui doit être créé pour l’emplacement personnalisé qui sera créé. Il est recommandé de définir celui-ci sur la valeur `default`. |
    | `appsNamespace` | Espace de noms pour approvisionner les définitions et les pod d’application. **Doit** correspondre à l’espace de noms de la version de l’extension. |
    | `clusterName` | Nom de l’environnement Kubernetes App Service qui sera créé par rapport à cette extension. |
    | `keda.enabled` | Indique si [KEDA](https://keda.sh/) doit être installé sur le cluster Kubernetes. Accepte `true` ou `false`. |
    | `buildService.storageClassName` | [Nom de la classe de stockage](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#class) pour le service de build qui stocke les artefacts de build. Une valeur telle que `default` spécifie une classe nommée `default`, non une [classe marquée comme par défaut](https://kubernetes.io/docs/tasks/administer-cluster/change-default-storage-class/).  La valeur par défaut est une classe de stockage valide pour AKS et AKS HCI, mais il se peut que ce ne soit pas le cas pour d’autres distributions/plateformes. |
    | `buildService.storageAccessMode` | [Mode d’accès](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes) à utiliser avec la classe de stockage nommée ci-dessus. Accepte `ReadWriteOnce` ou `ReadWriteMany`. |
    | `customConfigMap` | Nom de la carte de configuration qui sera définie par l’environnement Kubernetes App Service. Actuellement, il doit s’agir de `<namespace>/kube-environment-config`, en remplaçant `<namespace>` par la valeur de `appsNamespace` ci-dessus. |
    | `envoy.annotations.service.beta.kubernetes.io/azure-load-balancer-resource-group` | Nom du groupe de ressources dans lequel réside le cluster Azure Kubernetes Service. Valide et obligatoire uniquement lorsque le cluster sous-jacent est un cluster Azure Kubernetes Service.  |
    | `logProcessor.appLogs.destination` | facultatif. Accepte `log-analytics` ou `none`. Le choix d’aucun désactive les journaux de plateforme. |
    | `logProcessor.appLogs.logAnalyticsConfig.customerId` | Obligatoire uniquement quand `logProcessor.appLogs.destination` a la valeur `log-analytics`. ID d’espace de travail Log Analytics codé en base64. Ce paramètre doit être configuré en tant que paramètre protégé. |
    | `logProcessor.appLogs.logAnalyticsConfig.sharedKey` | Obligatoire uniquement quand `logProcessor.appLogs.destination` a la valeur `log-analytics`. Clé partagée d’espace de travail Log Analytics codé en base64. Ce paramètre doit être configuré en tant que paramètre protégé. |
    | | |
        
3. Enregistrez la propriété `id` de l’extension App service pour plus tard.

    # <a name="bash"></a>[bash](#tab/bash)

    ```azurecli-interactive
    extensionId=$(az k8s-extension show \
        --cluster-type connectedClusters \
        --cluster-name $clusterName \
        --resource-group $groupName \
        --name $extensionName \
        --query id \
        --output tsv)
    ```

    # <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```powershell
    $extensionId=$(az k8s-extension show `
        --cluster-type connectedClusters `
        --cluster-name $clusterName `
        --resource-group $groupName `
        --name $extensionName `
        --query id `
        --output tsv)
    ```

    ---

4. Attendez que l’extension soit complètement installée avant de continuer. Il se peut que vous deviez faire attendre votre session de terminal jusqu’à ce que l’opération soit terminée en exécutant la commande suivante :

    ```azurecli-interactive
    az resource wait --ids $extensionId --custom "properties.installState!='Pending'" --api-version "2020-07-01-preview"
    ```

Vous pouvez utiliser `kubectl` pour afficher les pods créés dans votre cluster Kubernetes :

```bash
kubectl get pods -n $namespace
```

Pour en savoir plus sur ces pods et leur rôle dans le système, consultez [Pods créés par l’extension App Service](overview-arc-integration.md#pods-created-by-the-app-service-extension).

## <a name="create-a-custom-location"></a>Créer un emplacement personnalisé

L’[emplacement personnalisé](../azure-arc/kubernetes/custom-locations.md) dans Azure est utilisé pour attribuer l’environnement Kubernetes App service.

<!-- https://github.com/MicrosoftDocs/azure-docs-pr/pull/156618 -->

1. Définissez les variables d’environnement suivantes pour le nom souhaité de l’emplacement personnalisé et pour l’ID du cluster connecté Azure Arc.

    # <a name="bash"></a>[bash](#tab/bash)

    ```bash
    customLocationName="my-custom-location" # Name of the custom location
    
    connectedClusterId=$(az connectedk8s show --resource-group $groupName --name $clusterName --query id --output tsv)
    ```

    # <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```powershell
    $customLocationName="my-custom-location" # Name of the custom location
    
    $connectedClusterId=$(az connectedk8s show --resource-group $groupName --name $clusterName --query id --output tsv)
    ```

    ---
    
2. Créez l’emplacement personnalisé :

    # <a name="bash"></a>[bash](#tab/bash)

    ```azurecli-interactive
    az customlocation create \
        --resource-group $groupName \
        --name $customLocationName \
        --host-resource-id $connectedClusterId \
        --namespace $namespace \
        --cluster-extension-ids $extensionId
    ```

    # <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```azurecli-interactive
    az customlocation create `
        --resource-group $groupName `
        --name $customLocationName `
        --host-resource-id $connectedClusterId `
        --namespace $namespace `
        --cluster-extension-ids $extensionId
    ```

    ---
    
    <!-- --kubeconfig ~/.kube/config # needed for non-Azure -->

3. Vérifiez que l’emplacement personnalisé a été correctement créé avec la commande suivante. La sortie devrait afficher la propriété `provisioningState` avec la valeur `Succeeded`. Si ce n’est pas le cas, réexécutez la commande après une minute.

    ```azurecli-interactive
    az customlocation show --resource-group $groupName --name $customLocationName
    ```
    
4. Enregistrez l’ID d’emplacement personnalisé pour l’étape suivante.

    # <a name="bash"></a>[bash](#tab/bash)

    ```azurecli-interactive
    customLocationId=$(az customlocation show \
        --resource-group $groupName \
        --name $customLocationName \
        --query id \
        --output tsv)
    ```

    # <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```azurecli-interactive
    $customLocationId=$(az customlocation show `
        --resource-group $groupName `
        --name $customLocationName `
        --query id `
        --output tsv)
    ```

    ---
    
## <a name="create-the-app-service-kubernetes-environment"></a>Créer l’environnement Kubernetes App Service

Avant de pouvoir commencer à créer des applications à l’emplacement personnalisé, vous avez besoin d’un [environnement Kubernetes App service](overview-arc-integration.md#app-service-kubernetes-environment).

1. Créez l’environnement Kubernetes App Service :
    
    # <a name="bash"></a>[bash](#tab/bash)

    ```azurecli-interactive
    az appservice kube create \
        --resource-group $groupName \
        --name $kubeEnvironmentName \
        --custom-location $customLocationId \
    ```

    # <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```azurecli-interactive
    az appservice kube create `
        --resource-group $groupName `
        --name $kubeEnvironmentName `
        --custom-location $customLocationId `      
    ```

    ---
    
2. Vérifiez que l’environnement Kubernetes App Service est correctement créé avec la commande suivante. La sortie devrait afficher la propriété `provisioningState` avec la valeur `Succeeded`. Si ce n’est pas le cas, réexécutez la commande après une minute.

    ```azurecli-interactive
    az appservice kube show --resource-group $groupName --name $kubeEnvironmentName
    ```
    

## <a name="next-steps"></a>Étapes suivantes

- [Démarrage rapide : Créer une application web sur Azure Arc](quickstart-arc.md)
- [Créer votre première fonction sur Azure Arc](../azure-functions/create-first-function-arc-cli.md)
- [Créer votre première application logique sur Azure Arc](../logic-apps/azure-arc-enabled-logic-apps-create-deploy-workflows.md)
