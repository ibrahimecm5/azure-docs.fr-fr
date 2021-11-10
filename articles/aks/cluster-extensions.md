---
title: Extensions de cluster pour Azure Kubernetes Service (AKS) (préversion)
description: Découvrez comment déployer et gérer le cycle de vie des extensions sur Azure Kubernetes Service (AKS).
ms.service: container-service
ms.date: 10/13/2021
ms.topic: article
author: nickomang
ms.author: nickoman
ms.openlocfilehash: 80d6eb34e1b1e0bbce6a8a1f1d2de58dbec51b4c
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131447420"
---
# <a name="deploy-and-manage-cluster-extensions-for-azure-kubernetes-service-aks-preview"></a>Déployer et gérer des extensions de cluster pour Azure Kubernetes Service (AKS) (préversion)

Les extensions de cluster offrent une expérience pilotée par Azure Resource Manager pour l’installation et la gestion du cycle de vie de services comme Azure Machine Learning (ML) sur un cluster AKS. Cette fonctionnalité permet :

* Le déploiement basé sur Azure Resource Manager d’extensions, notamment des déploiements à grande échelle sur des clusters AKS.
* La gestion du cycle de vie de l’extension (mise à jour, suppression) à partir d’Azure Resource Manager.

Dans cet article, vous allez découvrir :
> [!div class="checklist"]

> * Comment créer une instance d’extension.
> * Les extensions de cluster disponibles sur AKS.
> * Comment afficher, lister, mettre à jour et supprimer des instances d’extension.

Une vue d’ensemble conceptuelle de cette fonctionnalité est disponible dans l’article [Extensions de cluster - Kubernetes avec Azure Arc][arc-k8s-extensions].

[!INCLUDE [preview features note](./includes/preview/preview-callout.md)]

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, vous pouvez créer un [compte gratuit](https://azure.microsoft.com/free).
* [Azure CLI](/cli/azure/install-azure-cli) version 2.16.0 ou ultérieure installée.

### <a name="register-provider-for-cluster-extensions"></a>Inscrire le fournisseur aux extensions de cluster

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Entrez les commandes suivantes :

    ```azurecli-interactive
    az provider register --namespace Microsoft.KubernetesConfiguration
    az provider register --namespace Microsoft.ContainerService
    ```

2. Supervisez le processus d’inscription. L’inscription peut prendre jusqu’à 10 minutes.

    ```azurecli-interactive
    az provider show -n Microsoft.KubernetesConfiguration -o table
    az provider show -n Microsoft.ContainerService -o table
    ```

    Une fois l’inscription effectuée, vous devriez voir que l’état `RegistrationState` pour ces espaces de noms passe à `Registered`.

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Entrez les commandes suivantes :

    ```azurepowershell
    Register-AzResourceProvider -ProviderNamespace Microsoft.KubernetesConfiguration
    Register-AzResourceProvider -ProviderNamespace Microsoft.ContainerService
    ```

1. Supervisez le processus d’inscription. L’inscription peut prendre jusqu’à 10 minutes.

    ```azurepowershell
    Get-AzResourceProvider -ProviderNamespace Microsoft.KubernetesConfiguration
    Get-AzResourceProvider -ProviderNamespace Microsoft.ContainerService
    ```

    Une fois l’inscription effectuée, vous devriez voir que l’état `RegistrationState` pour ces espaces de noms passe à `Registered`.

---

### <a name="register-the-aks-extensionmanager-preview-features"></a>Inscrire les fonctionnalités d’évaluation `AKS-ExtensionManager`

Pour créer un cluster AKS capable d’utiliser des extensions de cluster, vous devez activer l’indicateur de fonctionnalité `AKS-ExtensionManager` sur votre abonnement.

Inscrivez l’indicateur de fonctionnalité `AKS-ExtensionManager` à l’aide de la commande [az feature register][az-feature-register], comme indiqué dans l’exemple suivant :

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "AKS-ExtensionManager"
```

Quelques minutes sont nécessaires pour que l’état s’affiche *Registered* (Inscrit). Vérifiez l’état de l’inscription à l’aide de la commande [az feature list][az-feature-list] :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKS-ExtensionManager')].{Name:name,State:properties.state}"
```

Quand vous êtes prêt, actualisez l’inscription des fournisseurs de ressources *Microsoft.KubernetesConfiguration* et *Microsoft.ContainerService* en utilisant la commande [az provider register][az-provider-register] :

```azurecli-interactive
az provider register --namespace Microsoft.KubernetesConfiguration
az provider register --namespace Microsoft.ContainerService
```

### <a name="setup-the-azure-cli-extension-for-cluster-extensions"></a>Configurer l’extension Azure CLI pour les extensions de cluster

> [!NOTE]
> La version minimale prise en charge pour l’extension Azure CLI `k8s-extension` est `1.0.0`. Si vous n’êtes pas sûr de la version que vous avez installée, exécutez `az extension show --name k8s-extension` et recherchez le champ `version`.

Vous aurez également besoin de l’extension Azure CLI `k8s-extension`. Installez la en exécutant les commandes suivantes :
  
```azurecli-interactive
az extension add --name k8s-extension
```

Si l’extension `k8s-extension` est déjà installée, vous pouvez la mettre à jour vers la version la plus récente à l’aide de la commande suivante :

```azurecli-interactive
az extension update --name k8s-extension
```

## <a name="currently-available-extensions"></a>Extensions actuellement disponibles

>[!NOTE]
> Les extensions de cluster fournissent une plateforme pour les différentes extensions à installer et à gérer sur un cluster AKS. Si vous rencontrez des problèmes lors de l’utilisation de l’une de ces extensions, ouvrez un ticket de support auprès du service respectif.

| Extension | Description |
| --------- | ----------- |
| [Dapr][dapr-overview] | Dapr est un runtime portable piloté par les événements qui permet à tout développeur de créer facilement des applications résilientes, sans état et avec état, qui s’exécutent sur le cloud et en périphérie. |
| [Azure ML][azure-ml-overview] | Utilisez des clusters Azure Container Service pour l’apprentissage, l’inférence et la gestion des modèles d’apprentissage automatique dans Azure Machine Learning. |

## <a name="supported-regions-and-kubernetes-versions"></a>Régions et versions de Kubernetes prises en charge

Les extensions de cluster peuvent être utilisées sur des clusters AKS dans les [régions prenant en charge Kubernetes avec Azure Arc][arc-k8s-regions].

Pour les versions Kubernetes prises en charge, reportez-vous à la documentation correspondante pour chaque extension.

## <a name="usage-of-cluster-extensions"></a>Utilisation des extensions de cluster

> [!NOTE]
> Les exemples fournis dans cet article ne sont pas complets et sont uniquement destinés à présenter les fonctionnalités. Pour obtenir une liste complète des commandes et de leurs paramètres, consultez les [informations de référence sur az k8s-extension pour CLI][k8s-extension-reference].

### <a name="create-extensions-instance"></a>Créer une instance d’extensions

Créez une nouvelle instance d’extension avec la commande `k8s-extension create`, transmettant des valeurs pour les paramètres obligatoires. La commande ci-dessous crée une instance d’extension Azure Machine Learning sur votre cluster AKS :

```azurecli
az k8s-extension create --name aml-compute --extension-type Microsoft.AzureML.Kubernetes --scope cluster --cluster-name <clusterName> --resource-group <resourceGroupName> --cluster-type managedClusters --configuration-settings enableInference=True allowInsecureConnections=True
```

> [!NOTE]
> Le service Extensions de cluster ne peut pas conserver d’informations sensibles pendant plus de 48 heures. Si les agents d’extensions de cluster n’ont pas de connectivité réseau pendant plus de 48 heures et ne peuvent pas déterminer s’il faut créer une extension sur le cluster, l’extension passe à l’état `Failed`. Une fois l’extension dans l’état `Failed`, vous devrez réexécuter `k8s-extension create` pour créer une nouvelle instance d’extension.

#### <a name="required-parameters"></a>Paramètres obligatoires

| Nom du paramètre | Description |
|----------------|------------|
| `--name` | Nom de l’instance d’extension |
| `--extension-type` | Sélectionnez le type d’extension que vous souhaitez installer sur le cluster. Par exemple : Microsoft.AzureML.Kubernetes | 
| `--cluster-name` | Nom du cluster AKS sur lequel l’instance d’extension doit être créée |
| `--resource-group` | Le groupe de ressources contenant le cluster AKS |
| `--cluster-type` | Type de cluster sur lequel l’instance d’extension doit être créée. Spécifier `managedClusters` car il est mappé à des clusters AKS|

#### <a name="optional-parameters"></a>Paramètres facultatifs

| Nom du paramètre | Description |
|--------------|------------|
| `--auto-upgrade-minor-version` | Propriété booléenne qui spécifie si la version mineure de l’extension sera mise à niveau automatiquement ou non. Par défaut : `true`.  Si ce paramètre est défini sur true, vous ne pouvez pas définir le paramètre `version`, car la version sera mise à jour de façon dynamique. Si la valeur est `false`, l’extension ne sera pas mise à niveau automatiquement même pour les versions correctives. |
| `--version` | Version de l’extension à installer (version spécifique à laquelle épingler l’instance d’extension). Ne doit pas être fournie si la mise à niveau automatique de version mineure est définie sur `true`. |
| `--configuration-settings` | Paramètres qui peuvent être transmis dans l’extension pour contrôler sa fonctionnalité. Ils doivent être transmis en tant que paires `key=value` séparées par des espaces après le nom du paramètre. Si ce paramètre est utilisé dans la commande, `--configuration-settings-file` ne peut pas être utilisé dans la même commande. |
| `--configuration-settings-file` | Chemin d’accès au fichier JSON avec des paires clé-valeur à utiliser pour transmettre des paramètres de configuration à l’extension. Si ce paramètre est utilisé dans la commande, `--configuration-settings` ne peut pas être utilisé dans la même commande. |
| `--configuration-protected-settings` | Ces paramètres ne pouvant pas être récupérés à l’aide d’appels d’API `GET` ou de commandes `az k8s-extension show`, ils sont donc utilisés pour transmettre des paramètres sensibles. Ils doivent être transmis en tant que paires `key=value` séparées par des espaces après le nom du paramètre. Si ce paramètre est utilisé dans la commande, `--configuration-protected-settings-file` ne peut pas être utilisé dans la même commande. |
| `--configuration-protected-settings-file` | Chemin d’accès au fichier JSON avec des paires clé-valeur à utiliser pour transmettre des paramètres sensibles à l’extension. Si ce paramètre est utilisé dans la commande, `--configuration-protected-settings` ne peut pas être utilisé dans la même commande. |
| `--scope` | Étendue d’installation pour l’extension : `cluster` ou `namespace` |
| `--release-namespace` | Ce paramètre indique l’espace de noms dans lequel la mise en production doit être créée. Ce paramètre n’est pertinent que si le paramètre `scope` a la valeur `cluster`. |
| `--release-train` |  Les auteurs d’extensions peuvent publier des versions dans différents trains de version, tels que `Stable`, `Preview`, etc. Si ce paramètre n’est pas défini explicitement, `Stable` est utilisé comme valeur par défaut. Ce paramètre ne peut pas être utilisé quand le paramètre `autoUpgradeMinorVersion` a la valeur `false`. |
| `--target-namespace` | Ce paramètre indique l’espace de noms dans lequel la mise en production sera créée. L’autorisation du compte système créé pour cette instance d’extension sera limitée à cet espace de noms. Ce paramètre n’est pertinent que si le paramètre `scope` a la valeur `namespace`. |

### <a name="show-details-of-an-extension-instance"></a>Afficher les détails d’une instance d’extension

Affichez les détails d’une instance d’extension actuellement installée avec la commande `k8s-extension show` transmettant des valeurs pour les paramètres obligatoires :

```azurecli
az k8s-extension show --name azureml --cluster-name <clusterName> --resource-group <resourceGroupName> --cluster-type managedClusters
```

### <a name="list-all-extensions-installed-on-the-cluster"></a>Répertorier toutes les extensions installées sur le cluster

Répertoriez toutes les extensions installées sur un cluster avec la commande `k8s-extension list` transmettant des valeurs pour les paramètres obligatoires.

```azurecli
az k8s-extension list --cluster-name <clusterName> --resource-group <resourceGroupName> --cluster-type managedClusters
```

### <a name="update-extension-instance"></a>Mettre à jour l’instance d’extension

> [!NOTE]
> Reportez-vous à la documentation du type d’extension (par exemple, Azure ML) pour en savoir plus sur les paramètres spécifiques qui sont autorisés à être mis à jour sous ConfigurationSetting et ConfigurationProtectedSettings. Pour ConfigurationProtectedSettings, tous les paramètres doivent être fournis durant la mise à jour d’un paramètre unique. Si certains paramètres sont omis, ils sont considérés comme obsolètes et supprimés.

Mettez à jour une instance d’extension existante avec `k8s-extension update` en passant les valeurs pour les paramètres obligatoires. La commande ci-dessous met à jour le paramètre auto-upgrade pour une instance d’extension Azure Machine Learning :

```azurecli
az k8s-extension update --name azureml --extension-type Microsoft.AzureML.Kubernetes --scope cluster --cluster-name <clusterName> --resource-group <resourceGroupName> --cluster-type managedClusters
```

**Paramètres obligatoires**

| Nom du paramètre | Description |
|----------------|------------|
| `--name` | Nom de l’instance d’extension |
| `--extension-type` | Sélectionnez le type d’extension que vous souhaitez installer sur le cluster. Par exemple : Microsoft.AzureML.Kubernetes | 
| `--cluster-name` | Nom du cluster AKS sur lequel l’instance d’extension doit être créée |
| `--resource-group` | Le groupe de ressources contenant le cluster AKS |
| `--cluster-type` | Type de cluster sur lequel l’instance d’extension doit être créée. Spécifier `managedClusters` car il est mappé à des clusters AKS|

**Paramètres facultatifs**

| Nom du paramètre | Description |
|--------------|------------|
| `--auto-upgrade-minor-version` | Propriété booléenne qui spécifie si la version mineure de l’extension sera mise à niveau automatiquement ou non. Par défaut : `true`.  Si ce paramètre est défini sur true, vous ne pouvez pas définir le paramètre `version`, car la version sera mise à jour de façon dynamique. Si la valeur est `false`, l’extension ne sera pas mise à niveau automatiquement même pour les versions correctives. |
| `--version` | Version de l’extension à installer (version spécifique à laquelle épingler l’instance d’extension). Ne doit pas être fournie si la mise à niveau automatique de version mineure est définie sur `true`. |
| `--configuration-settings` | Paramètres qui peuvent être transmis dans l’extension pour contrôler sa fonctionnalité. Seuls les paramètres qui nécessitent une mise à jour doivent être fournis. Les paramètres fournis seraient remplacés par les valeurs fournies. Ils doivent être transmis en tant que paires `key=value` séparées par des espaces après le nom du paramètre. Si ce paramètre est utilisé dans la commande, `--configuration-settings-file` ne peut pas être utilisé dans la même commande. |
| `--configuration-settings-file` | Chemin d’accès au fichier JSON avec des paires clé-valeur à utiliser pour transmettre des paramètres de configuration à l’extension. Si ce paramètre est utilisé dans la commande, `--configuration-settings` ne peut pas être utilisé dans la même commande. |
| `--configuration-protected-settings` | Ces paramètres ne pouvant pas être récupérés à l’aide d’appels d’API `GET` ou de commandes `az k8s-extension show`, ils sont donc utilisés pour transmettre des paramètres sensibles. Lors de la mise à jour d’un paramètre, tous les paramètres doivent être fournis. Si certains paramètres sont omis, ils sont considérés comme obsolètes et supprimés. Ils doivent être transmis en tant que paires `key=value` séparées par des espaces après le nom du paramètre. Si ce paramètre est utilisé dans la commande, `--configuration-protected-settings-file` ne peut pas être utilisé dans la même commande. |
| `--configuration-protected-settings-file` | Chemin d’accès au fichier JSON avec des paires clé-valeur à utiliser pour transmettre des paramètres sensibles à l’extension. Si ce paramètre est utilisé dans la commande, `--configuration-protected-settings` ne peut pas être utilisé dans la même commande. |
| `--scope` | Étendue d’installation pour l’extension : `cluster` ou `namespace` |
| `--release-train` |  Les auteurs d’extensions peuvent publier des versions dans différents trains de version, tels que `Stable`, `Preview`, etc. Si ce paramètre n’est pas défini explicitement, `Stable` est utilisé comme valeur par défaut. Ce paramètre ne peut pas être utilisé quand le paramètre `autoUpgradeMinorVersion` a la valeur `false`. |

### <a name="delete-extension-instance"></a>Supprimer une instance d’extension

>[!NOTE]
> La ressource Azure représentant cette extension est immédiatement supprimée. La version Helm sur le cluster associé à cette extension n’est supprimée que quand les agents s’exécutant sur le cluster Kubernetes disposent d’une connectivité réseau et peuvent à nouveau accéder aux services Azure pour extraire l’état souhaité.

Supprimez une instance d’extension sur un cluster avec la commande `k8s-extension delete` transmettant des valeurs pour les paramètres obligatoires.

```azurecli
az k8s-extension delete --name azureml --cluster-name <clusterName> --resource-group <resourceGroupName> --cluster-type managedClusters
```

<!-- LINKS -->
<!-- INTERNAL -->
[arc-k8s-extensions]: ../azure-arc/kubernetes/conceptual-extensions.md
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[azure-ml-overview]: ../machine-learning/how-to-attach-arc-kubernetes.md
[dapr-overview]: ./dapr.md
[k8s-extension-reference]: /cli/azure/k8s-extension

<!-- EXTERNAL -->
[arc-k8s-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=azure-arc&regions=all