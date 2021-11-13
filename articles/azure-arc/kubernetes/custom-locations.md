---
title: Créer et gérer des emplacements personnalisés sur Kubernetes avec Azure Arc
ms.service: azure-arc
ms.date: 10/19/2021
ms.topic: article
author: shashankbarsin
ms.author: shasb
ms.custom: references_regions, devx-track-azurecli
description: Utiliser des emplacements personnalisés pour déployer des services Azure PaaS sur des clusters Kubernetes avec Azure Arc
ms.openlocfilehash: f241ec384fc9ed7ee96d7415074e009cea486811
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130257023"
---
# <a name="create-and-manage-custom-locations-on-azure-arc-enabled-kubernetes"></a>Créer et gérer des emplacements personnalisés sur Kubernetes avec Azure Arc

 Les *emplacements personnalisés* permettent aux administrateurs de locataire ou de cluster de configurer leurs clusters Kubernetes avec Azure Arc comme emplacements cibles pour le déploiement d’instances de services Azure, par ex.,  des ressources comme SQL Managed Instance avec Azure Arc et PostgreSQL Hyperscale avec Azure Arc. Sur les clusters Kubernetes avec Azure Arc, l’emplacement personnalisé représente une abstraction d’un espace de noms au sein du cluster Kubernetes avec Azure Arc. Les administrateurs de locataire ou de cluster peuvent attribuer des autorisations de contrôle d’accès en fonction du rôle (RBAC) aux développeurs d’applications ou aux administrateurs de base de données pour déployer des ressources de type instances managées SQL avec Azure Arc, instances PostgreSQL Hyperscale avec Azure Arc ou applications web Azure sur l’emplacement personnalisé. 
 
Une vue d’ensemble conceptuelle de cette fonctionnalité est disponible dans l’article [Emplacements personnalisés – Kubernetes avec Azure Arc](conceptual-custom-locations.md). 

Dans cet article, vous apprendrez comment :
> [!div class="checklist"]
> * Activer les emplacements personnalisés sur votre cluster Kubernetes avec Azure Arc.
> * Créer un emplacement personnalisé.


## <a name="prerequisites"></a>Prérequis

- [Procédez à l’installation ou à la mise à niveau d’Azure CLI](/cli/azure/install-azure-cli) vers une version >= 2.16.0.

- Installez les extensions Azure CLI suivantes :
    - `connectedk8s` (version 1.1.0 ou ultérieure)
    - `k8s-extension` (version 0.2.0 ou ultérieure)
    - `customlocation` (version 0.1.0 ou ultérieure) 
  
    ```azurecli
    az extension add --name connectedk8s
    az extension add --name k8s-extension
    az extension add --name customlocation
    ```
    
    Si les extensions `connectedk8s`, `k8s-extension` et `customlocation` sont déjà installées, mettez-les à jour vers la version la plus récente à l’aide de la commande suivante :

    ```azurecli
    az extension update --name connectedk8s
    az extension update --name k8s-extension
    az extension update --name customlocation
    ```
    >[!NOTE]
    >Nous vous recommandons d’utiliser la dernière version des extensions CLI pour obtenir les fonctionnalités les plus récentes.  

- Vérifiez que l’inscription du fournisseur est terminée pour `Microsoft.ExtendedLocation`.
    1. Entrez les commandes suivantes :
    
        ```azurecli
        az provider register --namespace Microsoft.ExtendedLocation
        ```

    2. Supervisez le processus d’inscription. L’inscription peut prendre jusqu’à 10 minutes.
    
        ```azurecli
        az provider show -n Microsoft.ExtendedLocation -o table
        ```

        Une fois l’inscription terminée, l’état `RegistrationState` aura la valeur de `Registered`.

- Vérifiez que vous avez un [cluster connecté Kubernetes avec Azure Arc](quickstart-connect-cluster.md).
    - [Mettez à niveau vos agents](agent-upgrade.md#manually-upgrade-agents) vers la version 1.1.0 ou une version ultérieure.

## <a name="enable-custom-locations-on-cluster"></a>Activer les emplacements personnalisés sur le cluster

Si vous êtes connecté à Azure CLI en tant qu’utilisateur Azure AD, exécutez la commande suivante pour activer cette fonctionnalité sur votre cluster :

```azurecli
az connectedk8s enable-features -n <clusterName> -g <resourceGroupName> --features cluster-connect custom-locations
```

Si vous êtes connecté à Azure CLI à l’aide d’un principal de service, effectuez les étapes suivantes pour activer cette fonctionnalité sur votre cluster :

1. Récupérez l’ID d’objet de l’application Azure AD utilisée par le service Azure Arc :

    ```azurecli
    az ad sp show --id 'bc313c14-388c-4e7d-a58e-70017303ee3b' --query objectId -o tsv
    ```

1. Utilisez la valeur `<objectId>` de l’étape ci-dessus pour activer la fonctionnalité Emplacements personnalisés sur le cluster :

    ```azurecli
    az connectedk8s enable-features -n <cluster-name> -g <resource-group-name> --custom-locations-oid <objectId> --features cluster-connect custom-locations
    ```

> [!NOTE]
> 1. La fonctionnalité Emplacements personnalisés dépend de la fonctionnalité Connexion au cluster. Les deux fonctionnalités doivent donc être activées pour que les emplacements personnalisés fonctionnent.
> 2. La commande `az connectedk8s enable-features` doit être exécutée sur une machine sur laquelle le fichier `kubeconfig` pointe vers le cluster sur lequel les fonctionnalités doivent être activées.

## <a name="create-custom-location"></a>Créer un emplacement personnalisé

1. Déployez l’extension de cluster de service Azure de l’instance de service Azure que vous voulez installer sur votre cluster :

    * [Data Services avec Azure Arc](../data/create-data-controller-direct-cli.md#create-the-arc-data-services-extension)

        > [!NOTE]
        > Un proxy sortant sans authentification et un proxy sortant avec authentification de base sont pris en charge par l’extension de cluster Data Services avec Azure Arc. Un proxy sortant qui attend des certificats approuvés n’est pas pris en charge actuellement.


    * [Azure App Service sur Azure Arc](../../app-service/manage-create-arc-environment.md#install-the-app-service-extension)

    * [Event Grid sur Kubernetes](../../event-grid/kubernetes/install-k8s-extension.md)

2. Obtenez l’identificateur Azure Resource Manager du cluster Kubernetes avec Azure Arc, référencé dans les prochaines étapes avec `connectedClusterId` :

    ```azurecli
    az connectedk8s show -n <clusterName> -g <resourceGroupName>  --query id -o tsv
    ```

3. Obtenez l’identificateur Azure Resource Manager de l’extension de cluster déployée en plus du cluster Kubernetes avec Azure Arc, référencé dans les prochaines étapes avec `extensionId` :

    ```azurecli
    az k8s-extension show --name <extensionInstanceName> --cluster-type connectedClusters -c <clusterName> -g <resourceGroupName>  --query id -o tsv
    ```

4. Créez un emplacement personnalisé en référençant le cluster Kubernetes avec Azure Arc et l’extension :

    ```azurecli
    az customlocation create -n <customLocationName> -g <resourceGroupName> --namespace <name of namespace> --host-resource-id <connectedClusterId> --cluster-extension-ids <extensionIds> 
    ```

**Paramètres obligatoires**

| Nom du paramètre | Description |
|----------------|------------|
| `--name, --n` | Nom de l’emplacement personnalisé. |
| `--resource-group, --g` | Groupe de ressources de l’emplacement personnalisé  | 
| `--namespace` | Espace de noms dans le cluster lié à l’emplacement personnalisé en cours de création |
| `--host-resource-id` | Identificateur Azure Resource Manager du cluster Kubernetes avec Azure Arc (cluster connecté) |
| `--cluster-extension-ids` | Identificateurs Azure Resource Manager des instances d’extension de cluster installées sur le cluster connecté. Fournir une liste des ID d’extension de cluster séparés par des espaces  |

**Paramètres facultatifs**

| Nom du paramètre | Description |
|--------------|------------|
| `--assign-identity` | La valeur par défaut est `None`. Crée une [identité managée affectée par le système](../../active-directory/managed-identities-azure-resources/overview.md) si le paramètre est défini sur « SystemAssigned » |
| `--location, --l` | Emplacement personnalisé de la ressource Azure Resource Manager dans Azure. Par défaut, ce paramètre est défini sur l’emplacement (ou la région Azure) du cluster connecté |
| `--tags` | Liste d’étiquettes séparées par des espaces : key[=value] [key[=value] ...]. Utiliser '' pour effacer les étiquettes existantes |
| `--kubeconfig` | Kubeconfig d’administration du cluster. Doit être transmis sous forme de fichier si le cluster est un cluster non-AAD |


## <a name="show-details-of-a-custom-location"></a>Afficher les détails d’un emplacement personnalisé

Afficher les détails d’un emplacement personnalisé

```azurecli
    az customlocation show -n <customLocationName> -g <resourceGroupName> 
```

**Paramètres obligatoires**

| Nom du paramètre | Description |
|----------------|------------|
| `--name, --n` | Nom de l’emplacement personnalisé. |
| `--resource-group, --g` | Groupe de ressources de l’emplacement personnalisé  | 

## <a name="list-custom-locations"></a>Lister les emplacements personnalisés

Liste tous les emplacements personnalisés dans un groupe de ressources

```azurecli
    az customlocation show -g <resourceGroupName> 
```

**Paramètres obligatoires**

| Nom du paramètre | Description |
|----------------|------------|
| `--resource-group, --g` | Groupe de ressources de l’emplacement personnalisé  | 


## <a name="update-a-custom-location"></a>Mettre à jour un emplacement personnalisé

Utilisez la commande `update` pour ajouter de nouvelles étiquettes, associer de nouveaux ID d’extension de cluster à l’emplacement personnalisé tout en conservant les étiquettes existantes et les extensions de cluster associées. `--cluster-extension-ids`, `--tags`, `assign-identity` peuvent être mis à jour. 

```azurecli
    az customlocation update -n <customLocationName> -g <resourceGroupName> --namespace <name of namespace> --host-resource-id <connectedClusterId> --cluster-extension-ids <extensionIds> 
```
**Paramètres obligatoires**

| Nom du paramètre | Description |
|----------------|------------|
| `--name, --n` | Nom de l’emplacement personnalisé. |
| `--resource-group, --g` | Groupe de ressources de l’emplacement personnalisé  | 
| `--namespace` | Espace de noms dans le cluster lié à l’emplacement personnalisé en cours de création |
| `--host-resource-id` | Identificateur Azure Resource Manager du cluster Kubernetes avec Azure Arc (cluster connecté) |

**Paramètres facultatifs**

| Nom du paramètre | Description |
|--------------|------------|
| `--assign-identity` | Peut être mis à jour vers `None` ou `"SystemAssigned` pour attribuer une [identité managée affectée par le système](../../active-directory/managed-identities-azure-resources/overview.md) à l’emplacement personnalisé |
| `--cluster-extension-ids` | Associez les nouvelles extensions de cluster à cet emplacement personnalisé en fournissant les identificateurs Azure Resource Manager des instances d’extension de cluster installées sur le cluster connecté. Fournir une liste des ID d’extension de cluster séparés par des espaces |
| `--tags` | Ajoutez de nouvelles étiquettes en plus des existantes. Liste d’étiquettes séparées par des espaces : key[=value] [key[=value] ...]. |

## <a name="patch-a-custom-location"></a>Patcher un emplacement personnalisé

Utilisez la commande `patch` pour remplacer les étiquettes existantes par des nouvelles, ou des ID d’extension de cluster existants par des nouveaux. `--cluster-extension-ids`, `assign-identity`, `--tags` peuvent être patchés. 

```azurecli
    az customlocation patch -n <customLocationName> -g <resourceGroupName> --namespace <name of namespace> --host-resource-id <connectedClusterId> --cluster-extension-ids <extensionIds> 
```

**Paramètres obligatoires**

| Nom du paramètre | Description |
|----------------|------------|
| `--name, --n` | Nom de l’emplacement personnalisé. |
| `--resource-group, --g` | Groupe de ressources de l’emplacement personnalisé  | 

**Paramètres facultatifs**

| Nom du paramètre | Description |
|--------------|------------|
| `--assign-identity` | Peut être mis à jour vers `None` ou `"SystemAssigned` pour attribuer une [identité managée affectée par le système](../../active-directory/managed-identities-azure-resources/overview.md) à l’emplacement personnalisé |
| `--cluster-extension-ids` | Associez les nouvelles extensions de cluster à cet emplacement personnalisé en fournissant les identificateurs Azure Resource Manager des instances d’extension de cluster installées sur le cluster connecté. Fournir une liste des ID d’extension de cluster séparés par des espaces |
| `--tags` | Ajoutez de nouvelles étiquettes en plus des existantes. Liste d’étiquettes séparées par des espaces : key[=value] [key[=value] ...]. |

## <a name="delete-a-custom-location"></a>Supprimer un emplacement personnalisé

 ```azurecli
    az customlocation delete -n <customLocationName> -g <resourceGroupName> --namespace <name of namespace> --host-resource-id <connectedClusterId> --cluster-extension-ids <extensionIds> 
   ```

## <a name="next-steps"></a>Étapes suivantes

- Connectez-vous en toute sécurité au cluster à l’aide de [Connexion au cluster](cluster-connect.md).
- Poursuivez avec [Azure App Service sur Azure Arc](../../app-service/overview-arc-integration.md) pour obtenir des instructions de bout en bout sur l’installation des extensions, la création d’emplacements personnalisés et la création de l’environnement Kubernetes App Service. 
- Créez une rubrique Event Grid et un abonnement aux événements pour [Event Grid sur Kubernetes](../../event-grid/kubernetes/overview.md).
- En savoir plus sur les [extensions Kubernetes avec Azure Arc](extensions.md#currently-available-extensions) actuellement disponibles.
