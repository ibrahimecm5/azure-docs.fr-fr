---
title: Surveiller les clusters Kubernetes avec Azure Arc
ms.date: 04/05/2021
ms.topic: article
author: shashankbarsin
ms.author: shasb
description: Collecter les métriques et les journaux des clusters Kubernetes avec Azure Arc à l’aide d’Azure Monitor
ms.openlocfilehash: 90a648c5cb6ace4d1f055532d335a705220e2716
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131465456"
---
# <a name="azure-monitor-container-insights-for-azure-arc-enabled-kubernetes-clusters"></a>Azure Monitor Container Insights pour les clusters Kubernetes avec Azure Arc

[Azure Monitor Container Insights](container-insights-overview.md) fournit une expérience de surveillance enrichie pour les clusters Kubernetes avec Azure Arc.


## <a name="supported-configurations"></a>Configurations prises en charge

- Azure Monitor Container Insights prend en charge la supervision de clusters Kubernetes avec Azure Arc comme décrit dans l’article [Vue d’ensemble](container-insights-overview.md), à l’exception des fonctionnalités de données en temps réel. En outre, les utilisateurs n’ont pas besoin d’autorisations de [Propriétaire](../../role-based-access-control/built-in-roles.md#owner) pour [activer les métriques](container-insights-update-metrics.md)
- `Docker`, `Moby` et les runtimes de conteneur compatibles CRI tels que `CRI-O` et `containerd`.
- Les proxys sortants sans authentification et les proxys sortants avec l’authentification de base sont pris en charge. Les proxys sortants qui attendent des certificats approuvés ne sont pas pris en charge actuellement.

## <a name="prerequisites"></a>Prérequis

- Vous avez rempli les conditions préalables indiquées dans la [documentation sur les extensions de cluster génériques](../../azure-arc/kubernetes/extensions.md#prerequisites).
- Un espace de travail Log Analytics : Azure Monitor Container Insights prend en charge un espace de travail Log Analytics dans les régions répertoriées sous la [page Produits Azure par région](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor). Vous pouvez le créer en utilisant [Azure Resource Manager](../logs/resource-manager-workspace.md), [PowerShell](../logs/powershell-workspace-configuration.md) ou le [portail Azure](../logs/quick-create-workspace.md).
- Vous devez disposer d’une attribution de rôle [Contributeur](../../role-based-access-control/built-in-roles.md#contributor) sur l’abonnement Azure contenant la ressource Kubernetes avec Azure Arc. Si l’espace de travail Log Analytics se trouve dans un autre abonnement, l’attribution de rôle Log Analytics [contributeur](../logs/manage-access.md#manage-access-using-azure-permissions) est nécessaire sur l’espace de travail log Analytics.
- Pour afficher les données de surveillance, vous devez avoir l’affectation de rôle [Lecteur Log Analytics](../logs/manage-access.md#manage-access-using-azure-permissions) dans l’espace de travail Log Analytics.
- Les points de terminaison suivants doivent être activés pour l’accès sortant en plus de ceux mentionnés dans [Connecter un cluster Kubernetes à Azure Arc](../../azure-arc/kubernetes/quickstart-connect-cluster.md#meet-network-requirements).

    | Point de terminaison | Port |
    |----------|------|
    | `*.ods.opinsights.azure.com` | 443 |
    | `*.oms.opinsights.azure.com` | 443 |
    | `dc.services.visualstudio.com` | 443 |
    | `*.monitoring.azure.com` | 443 |
    | `login.microsoftonline.com` | 443 |

    Si votre ressource Kubernetes avec Azure Arc se trouve dans l’environnement Azure US Government, les points de terminaison suivants doivent être activés pour l’accès sortant :

    | Point de terminaison | Port |
    |----------|------|
    | `*.ods.opinsights.azure.us` | 443 |
    | `*.oms.opinsights.azure.us` | 443 |
    | `dc.services.visualstudio.com` | 443 |
    

- Si vous avez déjà déployé Azure Monitor Insights Container Insights sur ce cluster à l’aide d’un script sans extensions de cluster, suivez les instructions répertoriées [ici](container-insights-optout-hybrid.md) pour supprimer ce graphique Helm. Vous pouvez ensuite continuer à créer une instance d’extension de cluster pour Azure Monitor Container Insights.


### <a name="identify-workspace-resource-id"></a>Identifier l’ID de ressource d’espace de travail

Exécutez les commandes suivantes pour localiser l’identificateur Azure Resource Manager complet de l’espace de travail Log Analytics. 

1. Affichez la liste de tous les abonnements auxquels vous avez accès à l’aide de la commande suivante :

    ```azurecli
    az account list --all -o table
    ```

2. Basculez vers l’abonnement qui héberge l’espace de travail Log Analytics à l’aide de la commande suivante :

    ```azurecli
    az account set -s <subscriptionId of the workspace>
    ```

3. L’exemple suivant affiche la liste des espaces de travail de vos abonnements au format JSON par défaut.

    ```
    az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json
    ```

    Dans la sortie, recherchez le nom de l’espace de travail qui vous intéresse. Le champ `id` qui représente l’identificateur Azure Resource Manager de cet espace de travail Log Analytics.

    >[!TIP]
    > Ce `id` se trouve également dans le panneau *Vue d’ensemble* de l’espace de travail Log Analytics via le portail Azure.

## <a name="create-extension-instance-using-azure-cli"></a>Créer une instance d’extension à l’aide d’Azure CLI

### <a name="option-1---with-default-values"></a>Option 1 - Avec les valeurs par défaut

Cette option utilise les valeurs par défaut suivantes :

- Crée ou utilise l’espace de travail Log Analytics par défaut existant correspondant à la région du cluster
- La mise à niveau automatique est activée pour l’extension de cluster Azure Monitor

```azurecli
az k8s-extension create --name azuremonitor-containers --cluster-name <cluster-name> --resource-group <resource-group> --cluster-type connectedClusters --extension-type Microsoft.AzureMonitor.Containers
```

### <a name="option-2---with-existing-azure-log-analytics-workspace"></a>Option 2 - Avec l’espace de travail Azure Log Analytics existant

Vous pouvez utiliser un espace de travail Azure Log Analytics existant dans n’importe quel abonnement sur lequel vous disposez d’une attribution de rôle *Contributeur* ou d’un rôle plus permissif.

```azurecli
az k8s-extension create --name azuremonitor-containers --cluster-name <cluster-name> --resource-group <resource-group> --cluster-type connectedClusters --extension-type Microsoft.AzureMonitor.Containers --configuration-settings logAnalyticsWorkspaceResourceID=<armResourceIdOfExistingWorkspace>
```

### <a name="option-3---with-advanced-configuration"></a>Option 3 - Avec la configuration avancée

Si vous souhaitez modifier les limites et demandes de ressources par défaut, vous pouvez utiliser les paramètres de la configuration avancée :

```azurecli
az k8s-extension create --name azuremonitor-containers --cluster-name <cluster-name> --resource-group <resource-group> --cluster-type connectedClusters --extension-type Microsoft.AzureMonitor.Containers --configuration-settings  omsagent.resources.daemonset.limits.cpu=150m omsagent.resources.daemonset.limits.memory=600Mi omsagent.resources.deployment.limits.cpu=1 omsagent.resources.deployment.limits.memory=750Mi
```

Consultez la [section Requêtes et limites des ressources des graphiques Helm](https://github.com/helm/charts/blob/master/incubator/azuremonitor-containers/values.yaml) pour les paramètres de configuration disponibles.

### <a name="option-4---on-azure-stack-edge"></a>Option 4 - Sur Azure Stack Edge

Si le cluster Kubernetes avec Azure Arc se trouve sur Azure Stack Edge, un chemin de montage personnalisé `/home/data/docker` doit être utilisé.

```azurecli
az k8s-extension create --name azuremonitor-containers --cluster-name <cluster-name> --resource-group <resource-group> --cluster-type connectedClusters --extension-type Microsoft.AzureMonitor.Containers --configuration-settings omsagent.logsettings.custommountpath=/home/data/docker
```

>[!NOTE]
> Si vous spécifiez explicitement la version de l’extension à installer dans la commande create, assurez-vous que la version spécifiée est >= 2.8.2.

## <a name="create-extension-instance-using-azure-portal"></a>Créer une instance d’extension à l’aide du portail Azure

>[!IMPORTANT]
>  Si vous déployez Azure Monitor sur un cluster Kubernetes s’exécutant sur Azure Stack Edge, l’option Azure CLI doit être suivie au lieu de l’option du portail Azure, car un chemin de montage personnalisé doit être défini pour ces clusters.    

### <a name="onboarding-from-the-azure-arc-enabled-kubernetes-resource-blade"></a>Intégration à partir du panneau de ressources Kubernetes avec Azure Arc

1. Dans le portail Azure, sélectionnez le cluster Kubernetes avec Azure Arc que vous souhaitez surveiller.

2. Sélectionnez l’élément « Insights » sous la section « Surveillance » du panneau de ressources.

3. Dans la page d’intégration, sélectionnez le bouton « Configurer Azure Monitor »

4. Vous pouvez maintenant choisir l’[espace de travail Log Analytics](../logs/quick-create-workspace.md) auquel envoyer vos métriques et journaux de données.

5. Sélectionnez le bouton « Configurer » pour déployer l’extension de cluster Azure Monitor Container Insights.

### <a name="onboarding-from-azure-monitor-blade"></a>Intégration à partir du panneau Azure Monitor

1. Dans le portail Azure, accédez au panneau « Moniteur », puis sélectionnez l’option « Conteneurs » dans le menu « Insights ».

2. Sélectionnez l’onglet « Clusters non surveillés » pour afficher les clusters Kubernetes avec Azure Arc pour lesquels vous pouvez activer l’analyse.

3. Cliquez sur le lien « Activer » en regard du cluster pour lequel vous souhaitez activer l’analyse.

4. Choisissez l’espace de travail Log Analytics et cliquez sur le bouton « Configurer » pour continuer.

## <a name="create-extension-instance-using-azure-resource-manager"></a>Créer une instance d’extension à l’aide d’Azure Resource Manager

1. Téléchargez le modèle et les paramètres Azure Resource Manager :

    ```console
    curl -L https://aka.ms/arc-k8s-azmon-extension-arm-template -o arc-k8s-azmon-extension-arm-template.json
    curl -L https://aka.ms/arc-k8s-azmon-extension-arm-template-params -o  arc-k8s-azmon-extension-arm-template-params.json
    ```

2. Mettez à jour les valeurs de paramètre dans le fichier arc-k8s-azmon-extension-arm-template-params.json. Pour le cloud public Azure, `opinsights.azure.com` doit être utilisé comme valeur de workspaceDomain et, pour AzureUSGovernment, `opinsights.azure.us` doit être utilisé comme valeur de workspaceDomain.

3. Déployer le modèle pour créer l’extension Azure Monitor Container Insights 

    ```azurecli
    az login
    az account set --subscription "Subscription Name"
    az deployment group create --resource-group <resource-group> --template-file ./arc-k8s-azmon-extension-arm-template.json --parameters @./arc-k8s-azmon-extension-arm-template-params.json
    ```

## <a name="verify-extension-installation-status"></a>Vérifier l’état de l’installation de l’extension
Une fois que vous avez créé l’extension Azure Monitor pour votre cluster Kubernetes avec Azure Arc, vous pouvez également vérifier l’état de l’installation à l’aide du portail Azure ou de l’interface CLI. Les installations ayant réussi doivent indiquer l’état « Installé ». Si l’état est « Échec » ou demeure « En attente » pendant longtemps, passez à la section Résolution des problèmes ci-dessous.

### <a name="azure-portal"></a>Portail Azure
1. Dans le portail Azure, sélectionnez le cluster Kubernetes Azure Arc avec l’extension en cours d’installation.
2. Sélectionnez l’élément « Extensions » sous la section « Paramètres » du panneau de ressources.
3. Vous devez voir une extension avec le nom « azuremonitor-Containers », avec l’état indiqué dans la colonne « État de l’installation »
### <a name="azure-cli"></a>Azure CLI
Exécutez la commande suivante pour afficher le dernier état de l’extension `Microsoft.AzureMonitor.Containers`.
```azurecli
az k8s-extension show --name azuremonitor-containers --cluster-name <cluster-name> --resource-group <resource-group> --cluster-type connectedClusters -n azuremonitor-containers
```

## <a name="delete-extension-instance"></a>Supprimer une instance d’extension

La commande suivante supprime uniquement l’instance d’extension, mais pas l’espace de travail Log Analytics. Les données de la ressource Log Analytics restent intactes.

```bash
az k8s-extension delete --name azuremonitor-containers --cluster-type connectedClusters --cluster-name <cluster-name> --resource-group <resource-group>
```

## <a name="disconnected-cluster"></a>Cluster déconnecté
Si votre cluster est déconnecté d’Azure pendant plus de 48 heures, Azure Resource Graph n’aura pas d’informations sur votre cluster. Par conséquent, le panneau Insights peut afficher des informations incorrectes sur l’état de votre cluster.

## <a name="troubleshooting"></a>Dépannage
Pour les problèmes liés à l’activation du monitoring, nous avons fourni un [script de dépannage](https://aka.ms/azmon-ci-troubleshooting) pour vous aider à diagnostiquer les problèmes.

## <a name="next-steps"></a>Étapes suivantes

- Une fois l’analyse activée pour collecter l’utilisation des ressources et l’intégrité de votre cluster Kubernetes avec Azure Arc et des charges de travail s’y exécutant, découvrez [comment utiliser](container-insights-analyze.md) Container Insights.

- Par défaut, l’agent conteneurisé collecte les journaux de conteneurs stdout/ stderr de tous les conteneurs en cours d’exécution dans tous les espaces de noms, sauf kube-system. Pour configurer la collecte des journaux de conteneurs spécifiques d’un ou plusieurs espaces de noms particuliers, consultez [Configurer la collecte de données de l’agent pour Azure Monitor pour conteneurs](container-insights-agent-config.md) afin de configurer les paramètres de collecte de données souhaités dans votre fichier de configurations ConfigMap.

- Pour scraper et analyser des métriques Prometheus à partir de votre cluster, consultez [Configurer la capture des métriques Prometheus avec Azure Monitor pour conteneurs](container-insights-prometheus-integration.md).
