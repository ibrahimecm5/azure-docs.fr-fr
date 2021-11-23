---
title: Extensions de cluster Kubernetes avec Azure Arc
services: azure-arc
ms.service: azure-arc
ms.date: 06/18/2021
ms.topic: article
author: shashankbarsin
ms.author: shasb
description: Déployer des extensions sur Kubernetes avec Azure Arc et gérer leur cycle de vie
ms.openlocfilehash: 811bced5b0855ffdc44d851459b69a7b6aad6b19
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132312818"
---
# <a name="deploy-and-manage-azure-arc-enabled-kubernetes-cluster-extensions"></a>Déployer et gérer les extensions de cluster Kubernetes avec Azure Arc

La fonctionnalité d’extensions Kubernetes active ce qui suit sur des clusters Kubernetes avec Azure Arc :

* Déploiement basé sur Azure Resource Manager de l’extension de cluster.
* Gestion du cycle de vie des graphiques Helm d’extension.

Dans cet article, vous apprenez :
> [!div class="checklist"]
> * Quelles sont les extensions de cluster Kubernetes avec Azure Arc actuellement disponibles.
> * Comment créer des instances d’extension.
> * Quels sont les paramètres obligatoires et facultatifs.
> * Comment afficher, lister, mettre à jour et supprimer des instances d’extension. 

Une vue d’ensemble conceptuelle de cette fonctionnalité est disponible dans l’article [Extensions de cluster - Kubernetes avec Azure Arc](conceptual-extensions.md).

[!INCLUDE [preview features note](./includes/preview/preview-callout.md)]

## <a name="prerequisites"></a>Prérequis

- [Procédez à l’installation ou à la mise à niveau d’Azure CLI](/cli/azure/install-azure-cli) vers une version >= 2.16.0.
- Extensions Azure CLI `connectedk8s` (version >= 1.1.0) et `k8s-extension` (version >= 0.2.0). Installez ces extensions Azure CLI en exécutant les commandes suivantes :
  
    ```azurecli
    az extension add --name connectedk8s
    az extension add --name k8s-extension
    ```
    
    Si les extensions `connectedk8s` et `k8s-extension` sont déjà installées, vous pouvez les mettre à jour vers la version la plus récente à l’aide de la commande suivante :

    ```azurecli
    az extension update --name connectedk8s
    az extension update --name k8s-extension
    ```

- Cluster Kubernetes avec Azure Arc connecté.
    - Si vous n’avez pas encore connecté de cluster, utilisez notre [Guide de démarrage rapide](quickstart-connect-cluster.md).
    - [Mettez à niveau vos agents](agent-upgrade.md#manually-upgrade-agents) vers une version >= 1.1.0.

## <a name="currently-available-extensions"></a>Extensions actuellement disponibles

| Extension | Description |
| --------- | ----------- |
| [Azure Monitor](../../azure-monitor/containers/container-insights-enable-arc-enabled-clusters.md?toc=/azure/azure-arc/kubernetes/toc.json) | Fournit une visibilité des performances des charges de travail déployées sur le cluster Kubernetes. Collecte les métriques d’utilisation de la mémoire et du processeur à partir de contrôleurs, de nœuds et de conteneurs. |
| [Microsoft Defender pour le cloud](../../security-center/defender-for-kubernetes-azure-arc.md?toc=/azure/azure-arc/kubernetes/toc.json) | Collecte des informations liées à la sécurité, telles que les données du journal d'audit du cluster Kubernetes. Fournit des recommandations et des alertes de menace basées sur les données collectées. |
| [Open Service Mesh avec Azure Arc](tutorial-arc-enabled-open-service-mesh.md) | Déploie Open Service Mesh sur le cluster, et active des fonctionnalités telles que la sécurité mTLS, le contrôle d’accès affiné, le déplacement du trafic, la surveillance avec Azure Monitor ou des modules complémentaires open source de Prometheus et Grafana, le suivi avec Jaeger, et l’intégration avec la solution de gestion de certification externe. |
| [Data Services avec Azure Arc](../../azure-arc/kubernetes/custom-locations.md#create-custom-location) | Vous permet d’exécuter des services de données Azure localement, à la périphérie et dans des clouds publics en utilisant Kubernetes et l’infrastructure de votre choix. |
| [Azure App Service sur Azure Arc](../../app-service/overview-arc-integration.md) | Vous permet de provisionner un environnement App Service Kubernetes sur des clusters Kubernetes avec Azure Arc. |
| [Event Grid sur Kubernetes](../../event-grid/kubernetes/overview.md) | Vous permet de créer et gérer des ressources Event Grid (par exemple, des abonnements aux rubriques et aux événements) sur des clusters Kubernetes avec Azure Arc. |
| [Azure API Management sur Azure Arc](../../api-management/how-to-deploy-self-hosted-gateway-azure-arc.md) | Vous permet de déployer et gérer la passerelle API Management sur des clusters Kubernetes avec Azure Arc. |
| [Machine Learning avec Azure Arc](../../machine-learning/how-to-attach-arc-kubernetes.md) | Déployer et exécuter Azure Machine Learning sur des clusters Kubernetes avec Azure Arc. |

## <a name="usage-of-cluster-extensions"></a>Utilisation des extensions de cluster

### <a name="create-extensions-instance"></a>Créer une instance d’extensions

Créez une nouvelle instance d’extension avec la commande `k8s-extension create`, transmettant des valeurs pour les paramètres obligatoires. La commande ci-dessous crée un Azure Monitor pour l’instance d’extension de conteneurs sur votre cluster Kubernetes avec Azure Arc :

```azurecli
az k8s-extension create --name azuremonitor-containers  --extension-type Microsoft.AzureMonitor.Containers --scope cluster --cluster-name <clusterName> --resource-group <resourceGroupName> --cluster-type connectedClusters
```

**Output:**

```json
{
  "autoUpgradeMinorVersion": true,
  "configurationProtectedSettings": null,
  "configurationSettings": {
    "logAnalyticsWorkspaceResourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/defaultresourcegroup-eus/providers/microsoft.operationalinsights/workspaces/defaultworkspace-xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx-eus"
  },
  "creationTime": "2021-04-02T12:13:06.7534628+00:00",
  "errorInfo": {
    "code": null,
    "message": null
  },
  "extensionType": "microsoft.azuremonitor.containers",
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/demo/providers/Microsoft.Kubernetes/connectedClusters/demo/providers/Microsoft.KubernetesConfiguration/extensions/azuremonitor-containers",
  "identity": null,
  "installState": "Pending",
  "lastModifiedTime": "2021-04-02T12:13:06.753463+00:00",
  "lastStatusTime": null,
  "name": "azuremonitor-containers",
  "releaseTrain": "Stable",
  "resourceGroup": "demo",
  "scope": {
    "cluster": {
      "releaseNamespace": "azuremonitor-containers"
    },
    "namespace": null
  },
  "statuses": [],
  "systemData": null,
  "type": "Microsoft.KubernetesConfiguration/extensions",
  "version": "2.8.2"
}
```

> [!NOTE]
> * Le service ne peut pas conserver d’informations sensibles pendant plus de 48 heures. Si les agents Kubernetes avec Azure Arc n’ont pas de connectivité réseau pendant plus de 48 heures et ne peuvent pas déterminer s’il faut créer une extension sur le cluster, l’extension passe à l’état `Failed`. Une fois l’extension dans l’état `Failed`, vous devrez réexécuter `k8s-extension create` pour créer une nouvelle extension de ressource Azure.
> * Azure Monitor pour conteneurs est une extension singleton (une seule requise par cluster). Vous devez nettoyer toutes les installations précédentes de graphique Helm d’Azure Monitor pour conteneurs (sans extensions) avant de les réinstaller via des extensions. Suivez les instructions pour [supprimer le graphique Helm avant d’exécuter la commande `az k8s-extension create`](../../azure-monitor/containers/container-insights-optout-hybrid.md).

**Paramètres obligatoires**

| Nom du paramètre | Description |
|----------------|------------|
| `--name` | Nom de l’instance d’extension |
| `--extension-type` | Sélectionnez le type d’extension que vous souhaitez installer sur le cluster. Par exemple : Microsoft.AzureMonitor.Containers, microsoft.azuredefender.kubernetes | 
| `--scope` | Étendue d’installation pour l’extension : `cluster` ou `namespace` |
| `--cluster-name` | Nom de la ressource Kubernetes avec Azure Arc sur laquelle l’instance d’extension doit être créée |
| `--resource-group` | Groupe de ressources contenant la ressource Kubernetes avec Azure Arc |
| `--cluster-type` | Type de cluster sur lequel l’instance d’extension doit être créée. Actuellement seule la valeur `connectedClusters`, qui correspond à Kubernetes avec Azure Arc, est acceptée |

**Paramètres facultatifs**

| Nom du paramètre | Description |
|--------------|------------|
| `--auto-upgrade-minor-version` | Propriété booléenne qui spécifie si la version mineure de l’extension sera mise à niveau automatiquement ou non. Par défaut : `true`.  Si ce paramètre est défini sur true, vous ne pouvez pas définir le paramètre `version`, car la version sera mise à jour de façon dynamique. Si la valeur est `false`, l’extension ne sera pas mise à niveau automatiquement même pour les versions correctives. |
| `--version` | Version de l’extension à installer (version spécifique à laquelle épingler l’instance d’extension). Ne doit pas être fournie si la mise à niveau automatique de version mineure est définie sur `true`. |
| `--configuration-settings` | Paramètres qui peuvent être transmis dans l’extension pour contrôler sa fonctionnalité. Ils doivent être transmis en tant que paires `key=value` séparées par des espaces après le nom du paramètre. Si ce paramètre est utilisé dans la commande, `--configuration-settings-file` ne peut pas être utilisé dans la même commande. |
| `--configuration-settings-file` | Chemin d’accès au fichier JSON avec des paires clé-valeur à utiliser pour transmettre des paramètres de configuration à l’extension. Si ce paramètre est utilisé dans la commande, `--configuration-settings` ne peut pas être utilisé dans la même commande. |
| `--configuration-protected-settings` | Ces paramètres ne pouvant pas être récupérés à l’aide d’appels d’API `GET` ou de commandes `az k8s-extension show`, ils sont donc utilisés pour transmettre des paramètres sensibles. Ils doivent être transmis en tant que paires `key=value` séparées par des espaces après le nom du paramètre. Si ce paramètre est utilisé dans la commande, `--configuration-protected-settings-file` ne peut pas être utilisé dans la même commande. |
| `--configuration-protected-settings-file` | Chemin d’accès au fichier JSON avec des paires clé-valeur à utiliser pour transmettre des paramètres sensibles à l’extension. Si ce paramètre est utilisé dans la commande, `--configuration-protected-settings` ne peut pas être utilisé dans la même commande. |
| `--release-namespace` | Ce paramètre indique l’espace de noms dans lequel la mise en production doit être créée. Ce paramètre n’est pertinent que si le paramètre `scope` a la valeur `cluster`. |
| `--release-train` |  Les auteurs d’extensions peuvent publier des versions dans différents trains de version, tels que `Stable`, `Preview`, etc. Si ce paramètre n’est pas défini explicitement, `Stable` est utilisé comme valeur par défaut. Ce paramètre ne peut pas être utilisé quand le paramètre `autoUpgradeMinorVersion` a la valeur `false`. |
| `--target-namespace` | Ce paramètre indique l’espace de noms dans lequel la mise en production sera créée. L’autorisation du compte système créé pour cette instance d’extension sera limitée à cet espace de noms. Ce paramètre n’est pertinent que si le paramètre `scope` a la valeur `namespace`. |

### <a name="show-details-of-an-extension-instance"></a>Afficher les détails d’une instance d’extension

Affichez les détails d’une instance d’extension actuellement installée avec la commande `k8s-extension show` transmettant des valeurs pour les paramètres obligatoires :

```azurecli
az k8s-extension show --name azuremonitor-containers --cluster-name <clusterName> --resource-group <resourceGroupName> --cluster-type connectedClusters
```

**Output:**

```json
{
  "autoUpgradeMinorVersion": true,
  "configurationProtectedSettings": null,
  "configurationSettings": {
    "logAnalyticsWorkspaceResourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/defaultresourcegroup-eus/providers/microsoft.operationalinsights/workspaces/defaultworkspace-xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx-eus"
  },
  "creationTime": "2021-04-02T12:13:06.7534628+00:00",
  "errorInfo": {
    "code": null,
    "message": null
  },
  "extensionType": "microsoft.azuremonitor.containers",
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/demo/providers/Microsoft.Kubernetes/connectedClusters/demo/providers/Microsoft.KubernetesConfiguration/extensions/azuremonitor-containers",
  "identity": null,
  "installState": "Installed",
  "lastModifiedTime": "2021-04-02T12:13:06.753463+00:00",
  "lastStatusTime": "2021-04-02T12:13:49.636+00:00",
  "name": "azuremonitor-containers",
  "releaseTrain": "Stable",
  "resourceGroup": "demo",
  "scope": {
    "cluster": {
      "releaseNamespace": "azuremonitor-containers"
    },
    "namespace": null
  },
  "statuses": [],
  "systemData": null,
  "type": "Microsoft.KubernetesConfiguration/extensions",
  "version": "2.8.2"
}
```

### <a name="list-all-extensions-installed-on-the-cluster"></a>Répertorier toutes les extensions installées sur le cluster

Répertoriez toutes les extensions installées sur un cluster avec la commande `k8s-extension list` transmettant des valeurs pour les paramètres obligatoires.

```azurecli
az k8s-extension list --cluster-name <clusterName> --resource-group <resourceGroupName> --cluster-type connectedClusters
```

**Output:**

```json
[
  {
    "autoUpgradeMinorVersion": true,
    "creationTime": "2020-09-15T02:26:03.5519523+00:00",
    "errorInfo": {
      "code": null,
      "message": null
    },
    "extensionType": "Microsoft.AzureMonitor.Containers",
    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myRg/providers/Microsoft.Kubernetes/connectedClusters/myCluster/providers/Microsoft.KubernetesConfiguration/extensions/myExtInstanceName",
    "identity": null,
    "installState": "Pending",
    "lastModifiedTime": "2020-09-15T02:48:45.6469664+00:00",
    "lastStatusTime": null,
    "name": "myExtInstanceName",
    "releaseTrain": "Stable",
    "resourceGroup": "myRG",
    "scope": {
      "cluster": {
        "releaseNamespace": "myExtInstanceName1"
      }
    },
    "statuses": [],
    "type": "Microsoft.KubernetesConfiguration/extensions",
    "version": "0.1.0"
  },
  {
    "autoUpgradeMinorVersion": true,
    "creationTime": "2020-09-02T00:41:16.8005159+00:00",
    "errorInfo": {
      "code": null,
      "message": null
    },
    "extensionType": "microsoft.azuredefender.kubernetes",
    "id": "/subscriptions/0e849346-4343-582b-95a3-e40e6a648ae1/resourceGroups/myRg/providers/Microsoft.Kubernetes/connectedClusters/myCluster/providers/Microsoft.KubernetesConfiguration/extensions/defender",
    "identity": null,
    "installState": "Pending",
    "lastModifiedTime": "2020-09-02T00:41:16.8005162+00:00",
    "lastStatusTime": null,
    "name": "microsoft.azuredefender.kubernetes",
    "releaseTrain": "Stable",
    "resourceGroup": "myRg",
    "scope": {
      "cluster": {
        "releaseNamespace": "myExtInstanceName2"
      }
    },
    "type": "Microsoft.KubernetesConfiguration/extensions",
    "version": "0.1.0"
  }
]
```

### <a name="delete-extension-instance"></a>Supprimer une instance d’extension

Supprimez une instance d’extension sur un cluster avec la commande `k8s-extension delete` transmettant des valeurs pour les paramètres obligatoires.

```azurecli
az k8s-extension delete --name azuremonitor-containers --cluster-name <clusterName> --resource-group <resourceGroupName> --cluster-type connectedClusters
```

>[!NOTE]
> La ressource Azure représentant cette extension est immédiatement supprimée. La version Helm sur le cluster associé à cette extension n’est supprimée que quand les agents s’exécutant sur le cluster Kubernetes disposent d’une connectivité réseau et peuvent à nouveau accéder aux services Azure pour extraire l’état souhaité.

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les extensions de cluster actuellement disponibles pour Kubernetes avec Azure Arc :

> [!div class="nextstepaction"]
> [Azure Monitor](../../azure-monitor/containers/container-insights-enable-arc-enabled-clusters.md?toc=/azure/azure-arc/kubernetes/toc.json)
> [Microsoft Defender pour le cloud](../../security-center/defender-for-kubernetes-azure-arc.md?toc=/azure/azure-arc/kubernetes/toc.json)
> [Open Service Mesh avec Azure Arc](tutorial-arc-enabled-open-service-mesh.md)
> 
> [!div class="nextstepaction"]
> [Microsoft Defender pour le cloud](../../security-center/defender-for-kubernetes-azure-arc.md?toc=/azure/azure-arc/kubernetes/toc.json)
> 
> [!div class="nextstepaction"]
> [Azure App Service sur Azure Arc](../../app-service/overview-arc-integration.md)
> 
> [!div class="nextstepaction"]
> [Event Grid sur Kubernetes](../../event-grid/kubernetes/overview.md)
> 
> [!div class="nextstepaction"]
> [Azure API Management sur Azure Arc](../../api-management/how-to-deploy-self-hosted-gateway-azure-arc.md)
