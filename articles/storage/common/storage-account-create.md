---
title: Créez un compte de stockage.
titleSuffix: Azure Storage
description: Découvrez comment créer un compte de stockage pour stocker des objets blob, des fichiers, des files d’attente et des tables. Un compte de stockage Azure fournit un espace de noms unique dans Microsoft Azure destiné à la lecture et à l’écriture de vos données.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 05/18/2021
ms.author: tamram
ms.subservice: common
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 7b34040e9516daeda71704d74ceac64a03572214
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131452506"
---
# <a name="create-a-storage-account"></a>Créez un compte de stockage.

Un compte de stockage Azure contient tous vos objets de données de stockage Azure : objets blob, fichiers, files d’attente et tables. Le compte de stockage fournit pour vos données de stockage Azure un espace de noms unique, accessible de n’importe où dans le monde via HTTP ou HTTPS. Pour plus d’informations sur les comptes de stockage Azure, consultez [Vue d’ensemble des comptes de stockage](storage-account-overview.md).

Dans cet article sur les procédures, vous apprenez à créer un compte de stockage à l’aide du [Portail Azure](https://portal.azure.com/), d’[Azure PowerShell](/powershell/azure/), de l’[interface Azure CLI](/cli/azure) ou d’un [modèle Azure Resource Manager](../../azure-resource-manager/management/overview.md).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Prérequis

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

# <a name="portal"></a>[Portail](#tab/azure-portal)

Aucun.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Pour créer un compte de stockage Azure avec PowerShell, vérifiez que vous avez installé le [module PowerShell Az](https://www.powershellgallery.com/packages/Az) version 0.7 ou ultérieure. Pour plus d'informations, consultez [Présentation du module Azure PowerShell Az](/powershell/azure/new-azureps-module-az).

Pour connaître votre version actuelle, exécutez la commande suivante :

```powershell
Get-InstalledModule -Name "Az"
```

Pour installer ou mettre à niveau Azure PowerShell, consultez [Installer le module Azure PowerShell](/powershell/azure/install-az-ps).

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Vous pouvez vous connecter à Azure et exécuter des commandes Azure CLI de l’une des deux façons suivantes :

- Vous pouvez exécuter des commandes CLI à partir du Portail Azure, dans Azure Cloud Shell.
- Vous pouvez installer l’interface CLI et exécuter des commandes CLI localement.

### <a name="use-azure-cloud-shell"></a>Utiliser Azure Cloud Shell

Azure Cloud Shell est un interpréteur de commandes Bash gratuit, que vous pouvez exécuter directement dans le portail Azure. L’interface Azure CLI est préinstallée et configurée pour être utilisée avec votre compte. Cliquez sur le bouton **Cloud Shell** du menu situé dans la section supérieure droite de la fenêtre du Portail Azure :

[![Cloud Shell](./media/storage-quickstart-create-account/cloud-shell-menu.png)](https://portal.azure.com)

Ce bouton lance un interpréteur de commandes interactif que vous pouvez utiliser pour exécuter les étapes décrites dans cet article de procédure :

[![Capture d’écran représentant la fenêtre Cloud Shell du portail](./media/storage-quickstart-create-account/cloud-shell.png)](https://portal.azure.com)

### <a name="install-the-cli-locally"></a>Installer la CLI localement

Vous pouvez également installer et utiliser Azure CLI localement. Les exemples de cet article nécessitent la version 2.0.4 ou ultérieure d’Azure CLI. Exécutez `az --version` pour rechercher la version installée. Si vous devez effectuer une installation ou une mise à niveau, consultez [Installer Azure CLI](/cli/azure/install-azure-cli).

# <a name="template"></a>[Modèle](#tab/template)

Aucun.

---

Ensuite, connectez-vous à Azure.

# <a name="portal"></a>[Portail](#tab/azure-portal)

Connectez-vous au [portail Azure](https://portal.azure.com).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Connectez-vous à votre abonnement Azure avec la commande `Connect-AzAccount` et suivez les instructions à l’écran pour l’authentification.

```powershell
Connect-AzAccount
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pour lancer Azure Cloud Shell, connectez-vous au [Portail Azure](https://portal.azure.com).

Pour vous connecter à votre installation locale de l’interface CLI, exécutez la commande [az login](/cli/azure/reference-index#az_login) :

```azurecli-interactive
az login
```

# <a name="template"></a>[Modèle](#tab/template)

N/A

---

## <a name="create-a-storage-account"></a>Créez un compte de stockage.

Un compte de stockage est une ressource Azure Resource Manager. Resource Manager est le service de déploiement et de gestion d’Azure. Pour plus d’informations, consultez [Présentation d’Azure Resource Manager](../../azure-resource-manager/management/overview.md).

Chaque ressource Resource Manager, y compris un compte de stockage Azure, doit appartenir à un groupe de ressources Azure. Un groupe de ressources est un conteneur logique servant à grouper vos services Azure. Lorsque vous créez un compte de stockage, vous avez le choix entre créer un groupe de ressources ou utiliser un groupe de ressources existant. Ce guide pratique montre comment créer un groupe de ressources.

# <a name="portal"></a>[Portail](#tab/azure-portal)

Pour créer un compte de stockage Azure avec le portail Azure, effectuez les étapes suivantes :

1. Dans le menu du portail de gauche, sélectionnez **Comptes de stockage** pour afficher la liste de vos comptes de stockage.
1. Sur la page **Comptes de stockage**, sélectionnez **Créer**.

Les options de votre nouveau compte de stockage sont organisées en onglets dans la page **Créer un compte de stockage**. Les sections suivantes décrivent chacun des onglets et ses options.

### <a name="basics-tab"></a>Onglet Informations de base

Dans l’onglet **Informations de base**, fournissez les informations essentielles pour votre compte de stockage. Une fois que vous avez terminé l’onglet **Informations de base**, vous pouvez choisir de personnaliser davantage votre compte de stockage en définissant les options sous les autres onglets, ou vous pouvez sélectionner **Vérifier + créer** pour accepter les options par défaut, puis procéder à la validation et à la création du compte.

Le tableau suivant décrit les champs de l’onglet **Informations de base**.

| Section | Champ | Obligatoire ou facultatif | Description |
|--|--|--|--|
| Détails du projet | Abonnement | Obligatoire | Sélectionnez l’abonnement pour le nouveau compte de stockage. |
| Détails du projet | Groupe de ressources | Obligatoire | Créez un groupe de ressources pour ce compte de stockage ou sélectionnez-en un existant. Pour plus d’informations, consultez [Groupes de ressources](../../azure-resource-manager/management/overview.md#resource-groups). |
| Détails de l’instance | Nom du compte de stockage | Obligatoire | Fournissez un nom unique pour votre compte de stockage. Les noms des comptes de stockage doivent comporter entre 3 et 24 caractères, uniquement des lettres minuscules et des chiffres. |
| Détails de l’instance | Région | Obligatoire | Sélectionnez la région de votre compte de stockage. Pour plus d'informations, consultez [Régions et zones de disponibilité dans Azure](../../availability-zones/az-overview.md).<br /><br />Toutes les régions ne sont pas prises en charge pour tous les types de comptes de stockage ou toutes les configurations de redondance. Pour plus d’informations, consultez [Redondance de Stockage Azure](storage-redundancy.md).<br /><br />Le choix de la région peut avoir un impact sur la facturation. Pour plus d’informations, consultez [Facturation des comptes de stockage](storage-account-overview.md#storage-account-billing). |
| Détails de l’instance | Performances | Obligatoire | Sélectionnez les performances **Standard** pour les comptes de stockage v2 à usage général (par défaut). Ce type de compte est recommandé par Microsoft pour la plupart des scénarios. Pour plus d’informations, consultez [Types de comptes de stockage](storage-account-overview.md#types-of-storage-accounts).<br /><br />Sélectionnez **Premium** pour les scénarios nécessitant une faible latence. Après avoir sélectionné **Premium**, sélectionnez le type de compte de stockage Premium à créer. Voici les types de comptes Stockage Premium qui sont disponibles : <ul><li>[Objets blob de blocs](./storage-account-overview.md)</li><li>[Partages de fichiers](../files/storage-files-planning.md#management-concepts)</li><li>[Objets blob de pages](../blobs/storage-blob-pageblob-overview.md)</li></ul> |
| Détails de l’instance | Redondance | Obligatoire | Sélectionnez la configuration de redondance de votre choix. Toutes les options de redondance ne sont pas disponibles pour tous les types de comptes de stockage dans toutes les régions. Pour plus d’informations sur les configurations de redondance, consultez [Redondance du stockage Azure](storage-redundancy.md).<br /><br />Si vous sélectionnez une configuration géo-redondante (GRS ou GZRS), vos données sont répliquées dans un centre de données dans une autre région. Pour un accès en lecture aux données dans la région secondaire, sélectionnez **Permettre l’accès en lecture aux données disponibles en cas d’indisponibilité régionale**. |

L’illustration suivante montre une configuration standard pour un nouveau compte de stockage.

:::image type="content" source="media/storage-account-create/create-account-basics-tab.png" alt-text="Capture d’écran montrant une configuration standard pour un nouveau compte de stockage - Onglet Informations de base":::

### <a name="advanced-tab"></a>Onglet Avancé

Dans l’onglet **Avancé**, vous pouvez configurer des options supplémentaires et modifier les paramètres par défaut de votre nouveau compte de stockage. Certaines de ces options peuvent également être configurées après la création du compte de stockage, tandis que d’autres doivent être configurées au moment de la création.

Le tableau suivant décrit les champs de l’onglet **Avancé**.

| Section | Champ | Obligatoire ou facultatif | Description |
|--|--|--|--|
| Sécurité | Activer le transfert sécurisé | Facultatif | Activez le transfert sécurisé pour exiger que les demandes entrantes vers ce compte de stockage soient effectuées uniquement via HTTPS (par défaut). Recommandé pour une sécurité optimale. Pour plus d’informations, voir [Exiger un transfert sécurisé pour garantir des connexions sécurisées](storage-require-secure-transfer.md). |
| Sécurité | Activer le chiffrement d’infrastructure | Facultatif | Par défaut, le chiffrement de l’infrastructure n’est pas activé. Activez le chiffrement de l’infrastructure pour chiffrer vos données au niveau du service et de l’infrastructure. Pour plus d’informations, consultez [Créer un compte de stockage avec le chiffrement d’infrastructure activé à des fins de double chiffrement des données](infrastructure-encryption-enable.md). |
| Sécurité | Activer l’accès public aux objets blob | Facultatif | Lorsqu’il est activé, ce paramètre permet à un utilisateur disposant des autorisations appropriées d’activer l’accès public anonyme à un conteneur dans le compte de stockage (par défaut). La désactivation de ce paramètre empêche tout accès public anonyme au compte de stockage. Pour en savoir plus, consultez la section [Configure anonymous public read access for containers and blobs](../blobs/anonymous-read-access-prevent.md) (Empêcher l’accès en lecture publique anonyme aux conteneurs et aux objets blob).<br> <br> Activer l’accès public au blob ne rend pas ses données accessibles au public, à moins que l’utilisateur n’effectue l’étape supplémentaire consistant à configurer explicitement le paramètre d’accès public du conteneur. |
| Sécurité | Activer l’accès de clé de compte de stockage (préversion) | Facultatif | Lorsqu’il est activé, ce paramètre permet aux clients d’autoriser les demandes au compte de stockage à l’aide des clés d’accès du compte ou d’un compte Azure Active Directory (Azure AD - par défaut). La désactivation de ce paramètre empêche l’autorisation avec les clés d’accès du compte. Pour plus d’informations, consultez [Empêcher l’autorisation avec clé partagée pour un compte de stockage Azure](shared-key-authorization-prevent.md). |
| Sécurité | Version TLS minimale | Obligatoire | Sélectionnez la version minimale requise du protocole TLS (Transport Layer Security) pour les demandes entrantes adressées au compte de stockage. La valeur par défaut est TLS version 1.2. Lorsqu’elle est définie sur la valeur par défaut, les requêtes entrantes effectuées à l’aide de TLS 1.0 ou TLS 1.1 sont rejetées. Pour plus d’informations, consultez [Appliquer une version minimale requise du protocole TLS (Transport Layer Security) pour des demandes adressées à un compte de stockage](transport-layer-security-configure-minimum-version.md). |
| Data Lake Storage Gen2 | Activer l’espace de noms hiérarchique | Facultatif | Pour utiliser ce compte de stockage pour les charges de travail Azure Data Lake Storage Gen2, configurez un espace de noms hiérarchique. Pour plus d’informations, consultez [Présentation d’Azure Data Lake Storage Gen2](../blobs/data-lake-storage-introduction.md). |
| Stockage d'objets blob | Activer le partage de fichiers réseau (NFS) v3 | Facultatif | NFS v3 assure la compatibilité du système de fichiers Linux au niveau du stockage des objets, et permet aux clients Linux de monter un conteneur dans Stockage Blob à partir d’une machine virtuelle Azure ou d’un ordinateur local. Pour plus d’informations, consultez [Prise en charge du protocole NFS (Network File System) 3.0 dans le stockage Blob Azure](../blobs/network-file-system-protocol-support.md). |
| Stockage d'objets blob | Niveau d’accès | Obligatoire | Les niveaux d’accès aux objets blob vous permettent de stocker les données blob de la manière la plus économique, en fonction de l’utilisation. Sélectionnez le niveau chaud (par défaut) pour les données fréquemment utilisées. Sélectionnez le niveau froid pour les données rarement utilisées. Pour plus d’informations, consultez [Niveaux d’accès chaud, froid et archive pour les données d’objet blob](../blobs/access-tiers-overview.md). |
| Azure Files | Activer les partages de fichiers volumineux | Facultatif | Disponible uniquement pour les partages de fichiers standard avec les redondances LRS ou ZRS. |
| Tables et files d’attente | Activer la prise en charge des clés gérées par le client | Facultatif | Pour activer la prise en charge des clés gérées par le client pour les tables et les files d’attente, vous devez sélectionner ce paramètre au moment de la création du compte de stockage. Pour plus d’informations, consultez [Créer un compte qui prend en charge les clés gérées par le client pour les tables et les files d’attente](account-encryption-key-create.md). |

### <a name="networking-tab"></a>Onglet Réseau

Sous l’onglet **Mise en réseau**, vous pouvez configurer la connectivité réseau et les paramètres de préférence de routage pour votre nouveau compte de stockage. Ces options peuvent également être configurées après la création du compte de stockage.

Le tableau suivant décrit les champs de l’onglet **Mise en réseau**.

| Section | Champ | Obligatoire ou facultatif | Description |
|--|--|--|--|
| Connectivité réseau | Méthode de connectivité | Obligatoire | Par défaut, le trafic réseau entrant est acheminé vers le point de terminaison public de votre compte de stockage. Vous pouvez spécifier que le trafic doit être acheminé vers le point de terminaison public par le biais d’un réseau virtuel Azure. Vous pouvez également configurer des points de terminaison privés pour votre compte de stockage. Pour plus d’informations, consultez [Utiliser des points de terminaison privés pour le stockage Azure](storage-private-endpoints.md). |
| Routage réseau | Préférence de routage | Obligatoire | La préférence de routage réseau spécifie la façon dont le trafic réseau est acheminé vers le point de terminaison public de votre compte de stockage à partir de clients sur Internet. Par défaut, un nouveau compte de stockage utilise le routage réseau Microsoft. Vous pouvez également choisir d’acheminer le trafic réseau via le POP le plus proche du compte de stockage, ce qui peut réduire les coûts de mise en réseau. Pour plus d’informations, consultez [Préférence de routage réseau pour le service Stockage Azure](network-routing-preference.md). |

### <a name="data-protection-tab"></a>Onglet Protection des données

Sous l’onglet **Protection des données**, vous pouvez configurer les options de protection des données pour les données d’objet blob dans votre nouveau compte de stockage.  Ces options peuvent également être configurées après la création du compte de stockage. Pour obtenir une vue d’ensemble des options de protection des données dans le Stockage Azure, consultez [Vue d’ensemble de la protection des données](../blobs/data-protection-overview.md).

Le tableau suivant décrit les champs de l’onglet **protection des données**.

| Section | Champ | Obligatoire ou facultatif | Description |
|--|--|--|--|
| Récupération | Activer la restauration à un instant dans le passé pour les conteneurs | Facultatif | La limite de restauration dans le temps offre une protection contre les suppressions ou altérations accidentelles en vous permettant de restaurer des données d’objet blob de blocs à un état antérieur. Pour plus d’informations, consultez [Restauration à un instant dans le passé pour les blobs de bloc](../blobs/point-in-time-restore-overview.md).<br /><br />L’activation de la restauration à un instant dans le passé active également le contrôle de version des objets blob, la suppression réversible des objets blob et le flux des modifications d’objets blob. Ces fonctionnalités prérequises peuvent avoir un impact sur les coûts. Pour plus d’informations, consultez [Tarification et facturation](../blobs/point-in-time-restore-overview.md#pricing-and-billing) pour la restauration à un instant dans le passé. |
| Récupération | Activer la suppression réversible pour les objets blob | Facultatif | La suppression réversible d’objets blob protège un objet blob, un instantané ou une version contre les suppressions ou les remplacements accidentels en conservant les données supprimées dans le système pendant un laps de temps spécifié. Pendant la période de conservation, vous pouvez restaurer un objet supprimé de manière réversible à son état au moment de sa suppression. Pour plus d’informations, consultez [Suppression réversible pour les blobs](../blobs/soft-delete-blob-overview.md).<br /><br />Microsoft recommande d’activer la suppression réversible d’objets blob pour vos comptes de stockage et de définir une période de rétention minimale de sept jours. |
| Récupération | Activer la suppression réversible pour les conteneurs (préversion) | Facultatif | La suppression réversible de conteneur protège un conteneur et son contenu des suppressions accidentelles en conservant les données supprimées dans le système pendant une période de conservation spécifiée. Pendant la période de conservation, vous pouvez restaurer un conteneur supprimé de manière réversible à son état au moment de sa suppression. Pour plus d’informations, consultez [Suppression réversible pour les conteneurs (préversion)](../blobs/soft-delete-container-overview.md).<br /><br />Microsoft recommande d’activer la suppression réversible de conteneur pour vos comptes de stockage et de définir une période de rétention minimale de sept jours. |
| Récupération | Activer la suppression réversible pour les partages de fichiers | Facultatif | La suppression réversible pour les partages de fichiers protège un partage de fichiers et son contenu des suppressions accidentelles en conservant les données supprimées dans le système pendant une période de conservation spécifiée. Pendant la période de conservation, vous pouvez restaurer un partage de fichiers supprimé de manière réversible à son état au moment de sa suppression. Pour plus d’informations, consultez [Empêcher la suppression accidentelle de partages de fichiers Azure](../files/storage-files-prevent-file-share-deletion.md).<br /><br />Microsoft recommande d’activer la suppression réversible pour les partages de fichiers pour les charges de travail Azure Files et de définir une période de rétention minimale de sept jours. |
| Suivi | Activer le versioning des blobs | Facultatif | Le contrôle de version des objets blob enregistre automatiquement l’état d’un objet blob dans une version précédente lorsque l’objet blob est remplacé. Pour plus d’informations, consultez [Contrôle de version des objets blob](../blobs/versioning-overview.md).<br /><br />Microsoft recommande d’activer le contrôle de version des objets blob pour la protection optimale des données pour le compte de stockage. |
| Suivi | Activer le flux de modification des objets blobs | Facultatif | Le flux de modification des objets blob fournit des journaux des transactions de toutes les modifications apportées à tous les objets blob de votre compte de stockage, ainsi qu’à leurs métadonnées. Pour plus d’informations, consultez [Modifier la prise en charge des flux dans Stockage Blob Azure](../blobs/storage-blob-change-feed.md). |

### <a name="tags-tab"></a>Onglet balises

Dans l’onglet **Balises**, vous pouvez spécifier des balises Resource Manager pour faciliter l’organisation de vos ressources Azure. Pour plus d’informations, consultez [Baliser les ressources, les groupes de ressources et les abonnements pour l’organisation logique](../../azure-resource-manager/management/tag-resources.md).

### <a name="review--create-tab"></a>Onglet Vérifier + créer

Lorsque vous accédez à l’onglet **Vérifier + créer**, Azure exécute la validation sur les paramètres du compte de stockage que vous avez choisis. Si la validation réussit, vous pouvez procéder à la création du compte de stockage.

Si la validation échoue, le portail indique les paramètres qui doivent être modifiés.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Pour créer un compte de stockage v2 à usage général avec PowerShell, commencez par créer un nouveau groupe de ressources en appelant la commande [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) :

```azurepowershell-interactive
$resourceGroup = "<resource-group>"
$location = "<location>"
New-AzResourceGroup -Name $resourceGroup -Location $location
```

Si vous ne savez pas quelle région spécifier pour le paramètre `-Location`, vous pouvez récupérer la liste des régions prises en charge pour votre abonnement avec la commande [Get-AzLocation](/powershell/module/az.resources/get-azlocation) :

```azurepowershell-interactive
Get-AzLocation | select Location
```

Ensuite, créez un compte de stockage universel standard v2 avec l’option Stockage géo-redondant avec accès en lecture (RA-GRS) en utilisant la commande [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount). N’oubliez pas que le nom du compte de stockage doit être unique dans Azure. Par conséquent, remplacez la valeur d’espace réservé entre crochets par votre propre valeur unique :

```azurepowershell-interactive
New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name <account-name> `
  -Location $location `
  -SkuName Standard_RAGRS `
  -Kind StorageV2
```

Pour activer un espace de noms hiérarchique pour le compte de stockage afin d’utiliser [Azure Data Lake Storage](https://azure.microsoft.com/services/storage/data-lake-storage/), définissez le paramètre `EnableHierarchicalNamespace' parameter to ` sur True sur l’appel à la commande **New-AzStorageAccount**.

Le tableau suivant indique les valeurs à utiliser pour les paramètres `SkuName` et `Kind` pour créer un type particulier de compte de stockage avec la configuration de redondance souhaitée.

| Type de compte de stockage | Configurations de redondance prises en charge | Valeurs prises en charge pour le paramètre Kind | Valeurs prises en charge pour le paramètre SkuName | Prend en charge l’espace de noms hiérarchique |
|--|--|--|--|--|
| Usage général v2 Standard | LRS / GRS / RA-GRS / ZRS / GZRS / RA-GZRS | StorageV2 | Standard_LRS / Standard_GRS / Standard_RAGRS/ Standard_ZRS / Standard_GZRS / Standard_RAGZRS | Oui |
| Objets blob de blocs Premium | LRS / ZRS | BlockBlobStorage | Premium_LRS / Premium_ZRS | Oui |
| Partages de fichiers Premium | LRS / ZRS | FileStorage | Premium_LRS / Premium_ZRS | Non |
| Objets blob de pages Premium | LRS | StorageV2 | Premium_LRS | Non |
| v1 à usage général standard hérité | LRS / GRS / RA-GRS | Stockage | Standard_LRS / Standard_GRS / Standard_RAGRS | Non |
| Stockage blob hérité | LRS / GRS / RA-GRS | BlobStorage | Standard_LRS / Standard_GRS / Standard_RAGRS | Non |

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pour créer un compte de stockage v2 à usage général avec Azure CLI, commencez par créer un nouveau groupe de ressources en appelant la commande [az group create](/cli/azure/group#az_group_create).

```azurecli-interactive
az group create \
  --name storage-resource-group \
  --location westus
```

Si vous ne savez pas quelle région spécifier pour le paramètre `--location`, vous pouvez récupérer la liste des régions prises en charge pour votre abonnement avec la commande [az account list-locations](/cli/azure/account#az_account_list).

```azurecli-interactive
az account list-locations \
  --query "[].{Region:name}" \
  --out table
```

Ensuite, créez un compte de stockage universel standard v2 avec l’option Stockage géo-redondant avec accès en lecture en utilisant la commande [az storage account create](/cli/azure/storage/account#az_storage_account_create). N’oubliez pas que le nom du compte de stockage doit être unique dans Azure. Par conséquent, remplacez la valeur d’espace réservé entre crochets par votre propre valeur unique :

```azurecli-interactive
az storage account create \
  --name <account-name> \
  --resource-group storage-resource-group \
  --location westus \
  --sku Standard_RAGRS \
  --kind StorageV2
```

Pour activer un espace de noms hiérarchique pour le compte de stockage afin d’utiliser [Azure Data Lake Storage](https://azure.microsoft.com/services/storage/data-lake-storage/), définissez le paramètre `enable-hierarchical-namespace` sur `true` sur l’appel à la commande **az storage account create**. La création d’un espace de noms hiérarchique requiert Azure CLI version 2.0.79 ou ultérieure.

Le tableau suivant indique les valeurs à utiliser pour les paramètres `sku` et `kind` pour créer un type particulier de compte de stockage avec la configuration de redondance souhaitée.

| Type de compte de stockage | Configurations de redondance prises en charge | Valeurs prises en charge pour le paramètre kind | Valeurs prises en charge pour le paramètre sku | Prend en charge l’espace de noms hiérarchique |
|--|--|--|--|--|
| Usage général v2 Standard | LRS / GRS / RA-GRS / ZRS / GZRS / RA-GZRS | StorageV2 | Standard_LRS / Standard_GRS / Standard_RAGRS/ Standard_ZRS / Standard_GZRS / Standard_RAGZRS | Oui |
| Objets blob de blocs Premium | LRS / ZRS | BlockBlobStorage | Premium_LRS / Premium_ZRS | Oui |
| Partages de fichiers Premium | LRS / ZRS | FileStorage | Premium_LRS / Premium_ZRS | Non |
| Objets blob de pages Premium | LRS | StorageV2 | Premium_LRS | Non |
| v1 à usage général standard hérité | LRS / GRS / RA-GRS | Stockage | Standard_LRS / Standard_GRS / Standard_RAGRS | Non |
| Stockage blob hérité | LRS / GRS / RA-GRS | BlobStorage | Standard_LRS / Standard_GRS / Standard_RAGRS | Non |

# <a name="template"></a>[Modèle](#tab/template)

Vous pouvez utiliser Azure PowerShell ou Azure CLI pour déployer un modèle Resource Manager afin de créer un compte de stockage. Le modèle utilisé dans cet article de procédure est issu des [Modèles de démarrage rapide Azure Resource Manager](https://azure.microsoft.com/resources/templates/storage-account-create/). Pour exécuter les scripts, sélectionnez **Essayer** pour ouvrir Azure Cloud Shell. Pour coller le script, cliquez avec le bouton droit dans l’interpréteur de commandes, puis sélectionnez **Coller**.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location "$location"
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.storage/storage-account-create/azuredeploy.json"
```

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the location (i.e. centralus):" &&
read location &&
az group create --name $resourceGroupName --location "$location" &&
az deployment group create --resource-group $resourceGroupName --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.storage/storage-account-create/azuredeploy.json"
```

> [!NOTE]
> Ce modèle n’est utilisé qu’à titre d’exemple. De nombreux paramètres de compte de stockage ne sont pas configurés dans le cadre de ce modèle. Par exemple, si vous souhaitez utiliser [Data Lake Storage](https://azure.microsoft.com/services/storage/data-lake-storage/), vous devez modifier ce modèle en affectant à la propriété `isHnsEnabledad` de l’objet `StorageAccountPropertiesCreateParameters` la valeur `true`.

Pour savoir comment modifier ce modèle ou en créer de nouveaux, consultez :

- [Documentation Azure Resource Manager](../../azure-resource-manager/index.yml).
- [Référence de modèle de compte de stockage](/azure/templates/microsoft.storage/allversions).
- [Exemples supplémentaires de modèles de compte de stockage](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Storage).

---

## <a name="delete-a-storage-account"></a>Suppression d'un compte de stockage

La suppression d’un compte de stockage supprime l’intégralité du compte, y compris toutes les données qu’il contient. Veillez à sauvegarder toutes les données que vous souhaitez conserver avant de supprimer le compte.

Dans certains cas, un compte de stockage supprimé peut être récupéré, mais la récupération n’est pas garantie. Pour plus d'informations, consultez [Récupérer un compte de stockage supprimé](storage-account-recover.md).

Si vous essayez de supprimer un compte de stockage associé à une machine virtuelle Azure, vous pouvez obtenir une erreur indiquant que le compte de stockage est toujours en cours d’utilisation. Pour obtenir de l’aide sur la résolution de cette erreur, consultez [Résoudre les erreurs liées à la suppression de compte de stockage](/troubleshoot/azure/virtual-machines/storage-resource-deletion-errors).

# <a name="portal"></a>[Portail](#tab/azure-portal)

1. Accédez au compte de stockage dans le [portail Azure](https://portal.azure.com).
1. Cliquez sur **Supprimer**.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Pour supprimer le compte de stockage, utilisez la commande [Remove-AzStorageAccount](/powershell/module/az.storage/remove-azstorageaccount) :

```powershell
Remove-AzStorageAccount -Name <storage-account> -ResourceGroupName <resource-group>
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pour supprimer le compte de stockage, utilisez la commande [az storage account delete](/cli/azure/storage/account#az_storage_account_delete) :

```azurecli-interactive
az storage account delete --name <storage-account> --resource-group <resource-group>
```

# <a name="template"></a>[Modèle](#tab/template)

Pour supprimer le compte de stockage, utilisez Azure PowerShell ou Azure CLI.

```azurepowershell-interactive
$storageResourceGroupName = Read-Host -Prompt "Enter the resource group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"
Remove-AzStorageAccount -Name $storageAccountName -ResourceGroupName $storageResourceGroupName
```

```azurecli-interactive
echo "Enter the resource group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
az storage account delete --name storageAccountName --resource-group resourceGroupName
```

---

Vous pouvez également supprimer le groupe de ressources, ce qui supprime le compte de stockage et toutes les autres ressources de ce groupe. Pour plus d’informations sur la suppression d’un groupe de ressources, consultez [Supprimer un groupe de ressources et des ressources](../../azure-resource-manager/management/delete-resource-group.md).

## <a name="next-steps"></a>Étapes suivantes

- [Vue d’ensemble du compte de stockage](storage-account-overview.md)
- [Mettre à niveau vers un compte de stockage v2 à usage général](storage-account-upgrade.md)
- [Déplacer un compte de stockage vers une autre région](storage-account-move.md)
- [récupérer un compte de stockage supprimé](storage-account-recover.md)