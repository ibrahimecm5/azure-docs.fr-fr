---
title: Configurer Azure Red Hat OpenShift v4.x avec Container Insights | Microsoft Docs
description: Cet article décrit comment configurer l’analyse d’un cluster Kubernetes avec Azure Monitor, hébergé sur Azure Red Hat OpenShift version 4 ou ultérieure.
ms.topic: conceptual
ms.date: 03/05/2021
ms.openlocfilehash: 4f9bdaf07903294ede0b8530a28e716c27d9cd9e
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130231509"
---
# <a name="configure-azure-red-hat-openshift-v4x-with-container-insights"></a>Configurer Azure Red Hat OpenShift v4.x avec Container Insights

Container Insights offre une expérience de surveillance riche pour les clusters Azure Kubernetes Service (AKS) et Moteur AKS. Cet article explique comment parvenir à une expérience d’analyse similaire en activant la surveillance pour les clusters Kubernetes hébergés sur [Azure Red Hat OpenShift](../../openshift/intro-openshift.md) version 4.x.

>[!NOTE]
>La prise en charge d’Azure Red Hat OpenShift est actuellement une fonctionnalité en préversion publique.
>

Vous pouvez activer Container Insights pour un ou plusieurs déploiements existants d’Azure Red Hat OpenShift v4.x à l’aide des méthodes prises en charge décrites dans cet article.

Pour un cluster existant, exécutez [le script Bash dans Azure CLI](/cli/azure/openshift#az_openshift_create&preserve-view=true).

## <a name="supported-and-unsupported-features"></a>Fonctionnalités prises en charge et non prises en charge

Container Insights prend en charge la surveillance d’Azure Red Hat OpenShift v4.x comme décrit dans [Vue d’ensemble de Container Insights](container-insights-overview.md), à l’exception des fonctionnalités suivantes :

- Données actives (préversion)
- [Collecter les indicateurs de performance](container-insights-update-metrics.md) à partir des nœuds de cluster et des pod, et les stocker dans la base de données de métriques Azure Monitor

## <a name="prerequisites"></a>Prérequis

- Azure CLI version 2.0.72 ou ultérieure  

- Outil CLI [Helm 3](https://helm.sh/docs/intro/install/)

- Dernière version de l’[interface de ligne de commande OpenShift](https://docs.openshift.com/container-platform/4.7/cli_reference/openshift_cli/getting-started-cli.html)

- [Bash version 4](https://www.gnu.org/software/bash/)

- Outil en ligne de commande [Kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)

- Un [espace de travail Log Analytics](../logs/design-logs-deployment.md).

    Container Insights prend en charge un espace de travail Log Analytics dans les régions répertoriées dans [Produits Azure par région](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor). Pour créer votre propre espace de travail, vous pouvez utiliser [Azure Resource Manager](../logs/resource-manager-workspace.md), [PowerShell](../logs/powershell-workspace-configuration.md?toc=%2fpowershell%2fmodule%2ftoc.json) ou le [portail Azure](../logs/quick-create-workspace.md).

- Pour activer les fonctionnalités de Container Insights et y accéder, vous devez avoir au minimum le rôle Azure *Contributeur* dans l’abonnement Azure et le rôle [*Contributeur Log Analytics*](../logs/manage-access.md#manage-access-using-azure-permissions) dans l’espace de travail Log Analytics, configurés avec Container Insights.

- Pour afficher les données de surveillance, vous devez avoir le rôle [*Lecteur Log Analytics*](../logs/manage-access.md#manage-access-using-azure-permissions) dans l’espace de travail Log Analytics, configuré avec Container Insights.

## <a name="enable-monitoring-for-an-existing-cluster"></a>Activer l’analyse pour un cluster existant

Pour activer l’analyse pour un cluster Azure Red Hat OpenShift version 4 ou ultérieure, déployé dans Azure en utilisant le script Bash fourni, procédez comme suit :

1. Connectez-vous à Azure en exécutant la commande suivante :

    ```azurecli
    az login
    ```

1. Téléchargez et enregistrez dans un dossier local le script qui configure votre cluster avec le module complémentaire d’analyse à l’aide des commandes suivantes :

    `curl -o enable-monitoring.sh -L https://aka.ms/enable-monitoring-bash-script`

1. Connectez-vous au cluster ARO v4 en suivant les instructions fournies dans [Tutoriel : Se connecter à un cluster Azure Red Hat OpenShift 4](../../openshift/tutorial-connect-cluster.md).


### <a name="integrate-with-an-existing-workspace"></a>Intégrer avec un espace de travail existant

Dans cette section, vous activez l’analyse de votre cluster à l’aide du script Bash que vous avez téléchargé. Pour effectuer une intégration avec un espace de travail Log Analytics existant, effectuez les étapes suivantes pour identifier tout d’abord l’ID de la ressource complet de votre espace de travail Log Analytics requis pour le paramètre `logAnalyticsWorkspaceResourceId`, puis exécutez la commande pour activer le complément d’analyse par rapport à l’espace de travail spécifié.

Si vous n’avez pas d’espace de travail à spécifier, vous pouvez passer à l’étape [ Intégrer avec l’espace de travail par défaut](#integrate-with-the-default-workspace) et laisser le script créer un espace de travail pour vous.

1. Affichez la liste de tous les abonnements auxquels vous avez accès en exécutant la commande suivante :

    ```azurecli
    az account list --all -o table
    ```

    La sortie se présente comme suit :

    ```azurecli
    Name                                  CloudName    SubscriptionId                        State    IsDefault
    ------------------------------------  -----------  ------------------------------------  -------  -----------
    Microsoft Azure                       AzureCloud   0fb60ef2-03cc-4290-b595-e71108e8f4ce  Enabled  True
    ```

1. Copiez la valeur de **SubscriptionId**.

1. Basculez vers l’abonnement qui héberge l’espace de travail Log Analytics en exécutant la commande suivante :

    ```azurecli
    az account set -s <subscriptionId of the workspace>
    ```

1. Affichez la liste des espaces de travail de vos abonnements au format JSON par défaut en exécutant la commande suivante :

    ```
    az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json
    ```

1. Dans la sortie, recherchez le nom de l’espace de travail, puis copiez l’ID de ressource complet de cet espace de travail Log Analytics sous le champ **ID**.

1. Pour activer l’analyse, exécutez la commande suivante. Remplacez les valeurs pour les paramètres `azureAroV4ClusterResourceId` et `logAnalyticsWorkspaceResourceId`.

    ```bash
    export azureAroV4ClusterResourceId="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.RedHatOpenShift/OpenShiftClusters/<clusterName>"
    export logAnalyticsWorkspaceResourceId="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/microsoft.operationalinsights/workspaces/<workspaceName>" 
    ```

    Voici la commande que vous devez exécuter une fois que vous avez rempli les 3 variables avec des commandes d’exportation :

    `bash enable-monitoring.sh --resource-id $azureAroV4ClusterResourceId --workspace-id $logAnalyticsWorkspaceResourceId`

Une fois que vous avez activé l’analyse, 15 minutes peuvent s’écouler avant que vous puissiez voir les indicateurs de performance d’intégrité du cluster.

### <a name="integrate-with-the-default-workspace"></a>Intégrer avec l’espace de travail par défaut

Dans cette section, vous activez l’analyse de votre cluster Azure Red Hat OpenShift v4.x à l’aide du script Bash que vous avez téléchargé.

Dans cet exemple, vous n'êtes pas obligé de créer ou de spécifier un espace de travail existant. Cette commande simplifie le processus en créant un espace de travail par défaut dans le groupe de ressources par défaut de l’abonnement cluster s’il n’existe pas dans la région.

L’espace de travail créé par défaut est semblable au format de *DefaultWorkspace-\<GUID>-\<Region>* .  

Remplacez la valeur du paramètre `azureAroV4ClusterResourceId`.

```bash
export azureAroV4ClusterResourceId="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.RedHatOpenShift/OpenShiftClusters/<clusterName>"
```

Par exemple :

`bash enable-monitoring.sh --resource-id $azureAroV4ClusterResourceId 

Une fois que vous avez activé la surveillance, 15 minutes peuvent s’écouler avant que vous puissiez voir les métriques d’intégrité du cluster.

### <a name="enable-monitoring-from-the-azure-portal"></a>Activer l’analyse dans le Portail Azure

L’affichage multicluster dans Container Insights met en surbrillance vos clusters Azure Red Hat OpenShift pour lesquels la supervision n’est pas activée sous l’onglet **Clusters non supervisés**. L’option **Activer** à côté de votre cluster ne lance pas l’intégration de l’analyse à partir du portail. Vous êtes redirigé vers cet article pour activer manuellement l’analyse en effectuant les étapes décrites plus haut dans cet article.

1. Connectez-vous au [portail Azure](https://portal.azure.com).

1. Dans le volet de gauche ou depuis la page d’accueil, sélectionnez **Azure Monitor**.

1. Dans la section **Insights**, sélectionnez **Conteneurs**.

1. Dans la page **Surveiller – Conteneurs**, sélectionnez **Clusters non surveillés**.

1. Dans la liste des clusters non analysés, sélectionnez le cluster, puis cliquez sur **Activer**.

    Vous pouvez identifier les résultats dans la liste en recherchant la valeur **ARO** sous la colonne **Type de cluster**. Après avoir cliqué sur **Activer**, vous êtes redirigé vers cet article.

## <a name="next-steps"></a>Étapes suivantes

- Une fois l’analyse activée pour collecter l’utilisation des ressources et l’intégrité de votre cluster Red Hat OpenShift version 4.x et des charges de travail s’y exécutant, découvrez [comment utiliser](container-insights-analyze.md) Container Insights.

- Par défaut, l’agent conteneurisé collecte les journaux de conteneurs *stdout* et *stderr* de tous les conteneurs en cours d’exécution dans tous les espaces de noms, sauf kube-system. Pour configurer le regroupement des journaux de conteneurs spécifiques d’un ou plusieurs espaces de noms particuliers, consultez [Configurer l’agent Insight de conteneur](container-insights-agent-config.md) afin de configurer les paramètres de collection de données souhaités dans votre fichier de configurations *ConfigMap*.

- Pour scraper et analyser des indicateurs de performance Prometheus à partir de votre cluster, consultez [Configurer le scraping des métriques Prometheus](container-insights-prometheus-integration.md).

- Pour savoir comment arrêter la surveillance de votre cluster avec Container Insights, consultez [Comment arrêter la surveillance de votre cluster Azure Red Hat OpenShift](./container-insights-optout-openshift-v3.md).