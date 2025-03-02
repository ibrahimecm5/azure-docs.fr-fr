---
title: Ajouter du stockage à Azure HPC Cache
description: Comment définir des cibles de stockage pour qu’Azure HPC Cache puisse utiliser votre système NFS local ou des conteneurs d’objets blob Azure dans le but de stocker des fichiers à long terme
author: femila
ms.service: hpc-cache
ms.topic: how-to
ms.date: 09/22/2021
ms.custom: subject-rbac-steps
ms.author: femila
ms.openlocfilehash: 29ecaecc579d1947d3e86ef6d9e080030a419331
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131070600"
---
# <a name="add-storage-targets"></a>Ajouter des cibles de stockage

Les *cibles de stockage* constituent un stockage back-end pour des fichiers accessibles via un Azure HPC Cache. Vous pouvez ajouter un stockage NFS (comme un système local), ou stocker des données dans un objet blob Azure.

Vous pouvez définir 10 cibles de stockage différentes pour n’importe quel cache, et les caches plus volumineux peuvent [prendre en charge jusqu’à 20 cibles de stockage](#size-your-cache-correctly-to-support-your-storage-targets).

Le cache présente toutes les cibles de stockage d’un [espace de noms agrégé](hpc-cache-namespace.md). Les chemins d’accès à l’espace de noms sont configurés séparément une fois que vous avez ajouté les cibles de stockage.

N’oubliez pas que les exportations de stockage doivent être accessibles à partir du réseau virtuel de votre cache. Pour le stockage matériel local, vous devrez peut-être configurer un serveur DNS capable de résoudre les noms d’hôtes pour l’accès au stockage NFS. Pour plus d’informations, lisez [Accès DNS](hpc-cache-prerequisites.md#dns-access).

Ajoutez des cibles de stockage après avoir créé votre cache. Procédez comme suit :

1. [Créer le cache](hpc-cache-create.md)
1. Définir une cible de stockage (informations disponibles dans cet article)
1. [Créer les chemins d’accès côté client](add-namespace-paths.md) (pour l’[espace de noms agrégé](hpc-cache-namespace.md))

La procédure d’ajout d’une cible de stockage est légèrement différente selon le type de stockage utilisé. Vous trouverez ci-dessous des informations détaillées pour chacune de ces options.

Cliquez sur l’image ci-dessous pour regarder une [vidéo de démonstration](https://azure.microsoft.com/resources/videos/set-up-hpc-cache/) montrant comment créer un cache et ajouter une cible de stockage à partir du portail Azure.

[![Miniature de vidéo : Azure HPC Cache : Configuration (cliquez pour visiter la page vidéo)](media/video-4-setup.png)](https://azure.microsoft.com/resources/videos/set-up-hpc-cache/)

## <a name="size-your-cache-correctly-to-support-your-storage-targets"></a>Dimensionner correctement votre cache pour prendre en charge vos cibles de stockage

Le nombre de cibles de stockage prises en charge dépend de la taille du cache définie lors de la création de ce dernier. La capacité du cache est une combinaison de la capacité de débit (en Go/s) et de la capacité de stockage (en To).

* Jusqu’à 10 cibles de stockage : un cache standard avec la valeur de stockage de cache la plus petite ou moyenne pour votre débit sélectionné peut compter un maximum de 10 cibles de stockage.

  Par exemple, si vous choisissez un débit de 2 Go/seconde et que vous ne choisissez pas la taille de stockage de cache la plus élevée, votre cache prend en charge un maximum de 10 cibles de stockage.

* Jusqu’à 20 cibles de stockage -

  * Tous les caches à haut débit (qui ont des tailles de stockage de cache préconfigurées) peuvent prendre en charge jusqu’à 20 cibles de stockage.
  * Les caches standard peuvent prendre en charge jusqu’à 20 cibles de stockage si vous choisissez la taille de cache disponible la plus élevée pour la valeur de débit sélectionnée. (Si vous utilisez Azure CLI, choisissez la taille de cache valide la plus élevée pour votre référence SKU de cache.)

Pour plus d’informations sur les paramètres de débit et de taille de cache, consultez [Définir la capacité du cache](hpc-cache-create.md#set-cache-capacity) .

## <a name="choose-the-correct-storage-target-type"></a>Choisir le type de cible de stockage approprié

Vous pouvez sélectionner parmi trois types de cible de stockage : **NFS**, **Blob** et **ADLS-NFS**. Choisissez le type qui correspond au type de système de stockage que vous utiliserez pour stocker vos fichiers au cours de ce projet de cache HPC.

* **NFS** - Créer une cible de stockage NFS pour accéder aux données sur un système de stockage connecté au réseau. Il peut s’agir d’un système de stockage local ou d’un autre type de stockage accessible via NFS.

  * Exigences : [Conditions requises pour le stockage NFS](hpc-cache-prerequisites.md#nfs-storage-requirements)
  * Instructions : [Ajouter une cible de stockage NFS](#add-a-new-nfs-storage-target)

* **Objet BLOB** - Utilisez une cible de stockage Blob pour stocker vos fichiers de travail dans un nouveau conteneur d’objets BLOB Azure. Ce conteneur ne peut être lu ou écrit que dans l’Azure HPC Cache.

  * Prérequis : [Exigences relatives au stockage Blob](hpc-cache-prerequisites.md#blob-storage-requirements)
  * Instructions : [Ajouter une cible de stockage Blob Azure](#add-a-new-azure-blob-storage-target)

* **ADLS-NFS** - La cible de stockage ADLS-NFS accèdent aux données à partir d’un conteneur [d’objets BLOB NFS](../storage/blobs/network-file-system-protocol-support.md). Vous pouvez précharger le conteneur à l’aide de commandes NFS standard, et les fichiers peuvent être lus plus tard avec NFS.

  * Prérequis : [Exigences relatives au stockage ADLS-NFS](hpc-cache-prerequisites.md#nfs-mounted-blob-adls-nfs-storage-requirements)
  * Instructions : [Ajouter une cible de stockage ADLS-NFS](#add-a-new-adls-nfs-storage-target)

## <a name="add-a-new-azure-blob-storage-target"></a>Ajouter une cible de stockage Blob Azure

Les nouvelles cibles de stockage Blob nécessitent un conteneur d’objets blob vide ou un conteneur comprenant des données au format du système de fichiers cloud Azure HPC Cache. Pour plus d’informations sur le préchargement d’un conteneur d’objets blob, consultez [Déplacer les données dans le stockage d’objets blob Azure](hpc-cache-ingest.md).

Sur le portail Azure, la page **Ajouter une cible de stockage** offre la possibilité de créer un conteneur d’objets blob juste avant de l’ajouter.

> [!NOTE]
>
> * Pour le stockage Blob monté sur NFS, utilisez le type [Cible de stockage ADLS-NFS](#add-a-new-adls-nfs-storage-target).
> * [Les configurations de cache à haut débit](hpc-cache-create.md#choose-the-cache-type-for-your-needs) ne prennent pas en charge les cibles de stockage d’objets BLOB Azure standard. Utilisez plutôt le stockage d’objets BLOB NFS (ADLS-NFS).

### <a name="portal"></a>[Portail](#tab/azure-portal)

À partir du portail Azure, ouvrez votre instance de cache, puis cliquez sur **Cibles de stockage** dans la barre latérale gauche.

![capture d’écran des paramètres > page de la cible de stockage, avec deux cibles de stockage existantes dans une table et une mise en surbrillance autour du bouton + ajouter une cible de stockage au-dessus du tableau](media/add-storage-target-button.png)

La page **Cibles de stockage** répertorie toutes les cibles existantes et fournit un lien permettant d’en ajouter une nouvelle.

Cliquez sur le bouton **Ajouter une cible de stockage**.

![capture d’écran de la page Ajouter une cible de stockage, où ont été renseignées les informations relatives à la nouvelle cible de stockage d’objets blob Azure](media/hpc-cache-add-blob.png)

Pour définir un conteneur d’objets blob Azure, entrez les informations ci-dessous.

* **Nom de la cible de stockage** - Définissez un nom permettant d’identifier cette cible de stockage dans Azure HPC Cache.
* **Type de cible** : choisissez **Objet blob**.
* **Compte de stockage** : sélectionnez le compte que vous souhaitez utiliser.

  Vous devez autoriser l’instance de cache à accéder au compte de stockage, en suivant la procédure décrite dans [Ajouter les rôles d’accès](#add-the-access-control-roles-to-your-account).

  Pour plus d’informations sur le type de compte de stockage que vous pouvez utiliser, lisez [Exigences relatives au stockage Blob](hpc-cache-prerequisites.md#blob-storage-requirements).

* **Conteneur de stockage** : sélectionnez le conteneur d’objets blob pour cette cible ou cliquez sur **Créer nouveau**.

  ![capture d’écran de la boîte de dialogue permettant de spécifier le nom et le niveau d’accès (privé) du nouveau conteneur](media/add-blob-new-container.png)

Lorsque vous avez terminé, cliquez sur **OK** pour ajouter la cible de stockage.

> [!NOTE]
> Si votre pare-feu de compte de stockage est défini de façon à limiter l’accès uniquement aux « réseaux sélectionnés », utilisez la solution de contournement temporaire décrite dans [Paramètres du pare-feu de compte de Stockage Blob](hpc-cache-blob-firewall-fix.md).

### <a name="add-the-access-control-roles-to-your-account"></a>Ajouter les rôles de contrôle d’accès à votre compte

Azure HPC Cache utilise le [contrôle d’accès en fonction du rôle (Azure RBAC)](../role-based-access-control/index.yml) pour autoriser le service de cache à accéder aux cibles de stockage Blob Azure de votre compte de stockage.

Le propriétaire du compte de stockage doit ajouter explicitement les rôles [Contributeur de comptes de stockage](../role-based-access-control/built-in-roles.md#storage-account-contributor) et [Contributeur aux données Blob du stockage](../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) pour l’utilisateur « HPC Cache Resource Provider ».

Vous pouvez le faire à l’avance, ou en cliquant sur le lien de la page du portail à partir de laquelle vous ajoutez une cible de stockage Blob. Rappelez-vous qu’il peut falloir jusqu’à cinq minutes pour que les paramètres de rôle se propagent dans l’environnement Azure. Par conséquent, après avoir ajouté les rôles, vous devez attendre quelques minutes avant de créer une cible de stockage.

1. Ouvrez **Contrôle d’accès (IAM)** pour votre compte de stockage.

1. Sélectionnez **Ajouter** > **Ajouter une attribution de rôle** pour ouvrir la page Ajouter une attribution de rôle.

1. Attribuez les rôles suivants, un par un. Pour connaître les étapes détaillées, consultez [Attribuer des rôles Azure à l’aide du portail Azure](../role-based-access-control/role-assignments-portal.md).
    
    | Paramètre | Valeur |
    | --- | --- |
    | Rôles | [Contributeur de compte de stockage](../role-based-access-control/built-in-roles.md#storage-account-contributor) <br/>  [Contributeur aux données Blob du stockage](../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) |
    | Attribuer l’accès à | Fournisseur de ressources HPC Cache |

    ![Page Ajouter une attribution de rôle](../../includes/role-based-access-control/media/add-role-assignment-page.png)

   > [!NOTE]
   > Si vous ne trouvez pas le fournisseur de ressources HPC Cache, essayez de rechercher la chaîne « storagecache » à la place. Il s’agissait d’un nom antérieur à la disponibilité générale pour le principal du service.

<!-- 
Steps to add the Azure roles:

1. Open the **Access control (IAM)** page for the storage account. (The link in the **Add storage target** page automatically opens this page for the selected account.)

1. Click the **+** at the top of the page and choose **Add a role assignment**.

1. Select the role "Storage Account Contributor" from the list.

1. In the **Assign access to** field, leave the default value selected ("Azure AD user, group, or service principal").  

1. In the **Select** field, search for "hpc".  This string should match one service principal, named "HPC Cache Resource Provider". Click that principal to select it.

   > [!NOTE]
   > If a search for "hpc" doesn't work, try using the string "storagecache" instead. Users who participated in previews (before GA) might need to use the older name for the service principal.

1. Click the **Save** button at the bottom.

1. Repeat this process to assign the role "Storage Blob Data Contributor".  

![screenshot of add role assignment GUI](media/hpc-cache-add-role.png) -->

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

### <a name="prerequisite-storage-account-access"></a>Condition préalable : Accès au compte de stockage

[Configurez Azure CLI pour Azure HPC Cache](./az-cli-prerequisites.md).

Avant d’ajouter une cible de stockage blob, vérifiez que le cache dispose des rôles appropriés pour accéder au compte de stockage et que les paramètres du pare-feu permettront la création de la cible de stockage.

Azure HPC Cache utilise le [contrôle d’accès en fonction du rôle (Azure RBAC)](../role-based-access-control/index.yml) pour autoriser le service de cache à accéder aux cibles de stockage Blob Azure de votre compte de stockage.

Le propriétaire du compte de stockage doit ajouter explicitement les rôles [Contributeur de comptes de stockage](../role-based-access-control/built-in-roles.md#storage-account-contributor) et [Contributeur aux données Blob du stockage](../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) pour l’utilisateur « HPC Cache Resource Provider ».

La création de la cible de stockage échoue si le cache ne dispose pas de ces rôles.

Il peut s’écouler jusqu’à cinq minutes pour que les paramètres de rôle se propagent dans l’environnement Azure. Par conséquent, après avoir ajouté les rôles, vous devez attendre quelques minutes avant de pouvoir créer une cible de stockage.

Consultez [Ajouter ou supprimer des attributions de rôle Azure à l’aide d’Azure CLI](../role-based-access-control/role-assignments-cli.md) pour obtenir des instructions détaillées.

Vérifiez également les paramètres de pare-feu de votre compte de stockage. Si le pare-feu est configuré pour limiter l’accès uniquement aux « réseaux sélectionnés », la création de la cible de stockage peut échouer. Utilisez la solution de contournement décrite dans [Contourner les paramètres de pare-feu de compte de stockage Blob](hpc-cache-blob-firewall-fix.md).

### <a name="add-a-blob-storage-target-with-azure-cli"></a>Ajouter une cible de stockage d’objets blob avec Azure CLI

Utilisez l’interface [az hpc-cache blob-storage-target add](/cli/azure/hpc-cache/blob-storage-target#az_hpc_cache_blob_storage_target_add) pour définir une cible de stockage d’objets blob Azure.

> [!NOTE]
> Les commandes Azure CLI vous obligent à créer un chemin d’accès d’espace de noms lorsque vous ajoutez une cible de stockage. Cela diffère du processus utilisé avec l’interface du portail Azure.

Outre les paramètres standard du groupe de ressources et du nom de cache, vous devez fournir ces options pour la cible de stockage :

* ``--name`` - Définissez un nom permettant d’identifier cette cible de stockage dans Azure HPC Cache.

* ``--storage-account`` - L’identificateur du compte sous la forme : /subscriptions/ *<id_abonnement>* /resourceGroups/ *<groupe_ressources_stockage>* /providers/Microsoft.Storage/storageAccounts/ *<nom_compte>*

  Pour plus d’informations sur le type de compte de stockage que vous pouvez utiliser, lisez [Exigences relatives au stockage Blob](hpc-cache-prerequisites.md#blob-storage-requirements).

* ``--container-name`` -Spécifiez le nom du conteneur à utiliser pour cette cible de stockage.

* ``--virtual-namespace-path`` - Définissez le chemin côté client de cette cible de stockage. Placez les chemins d’accès entre guillemets. Pour plus d’informations sur la fonctionnalité Espace de noms virtuels, consultez [Planifier l’espace de noms agrégé](hpc-cache-namespace.md).

Exemple de commande :

```azurecli
az hpc-cache blob-storage-target add --resource-group "hpc-cache-group" \
    --cache-name "cache1" --name "blob-target1" \
    --storage-account "/subscriptions/<subscriptionID>/resourceGroups/myrgname/providers/Microsoft.Storage/storageAccounts/myaccountname" \
    --container-name "container1" --virtual-namespace-path "/blob1"
```

---

## <a name="add-a-new-nfs-storage-target"></a>Ajouter une cible de stockage NFS

Une cible de stockage NFS a des paramètres différents d’une cible de stockage d’objets BLOB, y compris un paramètre de modèle d’utilisation qui indique au cache comment stocker les données de ce système de stockage.

![Capture d’écran de la page Ajouter la cible de stockage avec le type de cible défini sur NFS](media/add-nfs-target.png)

> [!NOTE]
> Avant de créer une cible de stockage NFS, assurez-vous que votre système de stockage est accessible à partir de l’Azure HPC Cache et répond aux exigences d’autorisation. La création d’une cible de stockage échoue si le cache ne peut pas accéder au système de stockage. Pour plus d’informations, consultez [Conditions requises pour le stockage NFS](hpc-cache-prerequisites.md#nfs-storage-requirements) et [Résolution des problèmes de configuration NAS et des cibles de stockage NFS](troubleshoot-nas.md).

### <a name="choose-a-usage-model"></a>Choisir un modèle d’utilisation

Lorsque vous créez une cible de stockage qui utilise NFS pour atteindre son système de stockage, vous devez choisir un modèle d’utilisation pour cette cible. Ce modèle détermine la façon dont vos données sont mises en cache.

Pour plus d’informations sur l’ensemble de ces paramètres, consultez [Comprendre les modèles d’utilisation](cache-usage-models.md).

Les modèles d’utilisation intégrés de HPC Cache vous permettent de choisir la manière d’équilibrer la vitesse de réponse avec le risque d’obtenir des données obsolètes. Si vous souhaitez optimiser la vitesse de lecture des fichiers, vous pourriez ne pas vous préoccuper du fait que les fichiers dans le cache sont vérifiés par rapport aux fichiers sur le serveur principal. En revanche, si vous souhaitez vous assurer que vos fichiers sont toujours à jour avec le stockage étendu, choisissez un modèle qui effectue des vérifications fréquemment.

> [!NOTE]
> [Les caches de style à débit élevé](hpc-cache-create.md#choose-the-cache-type-for-your-needs) prennent en charge la mise en cache en lecture uniquement.

Ces trois options couvrent la plupart des situations :

* **Nombreuses lectures, écritures peu fréquentes** : accélère l’accès en lecture aux fichiers statiques ou rarement modifiés.

  Cette option met en cache les fichiers des lectures client, mais transmet immédiatement les écritures client au stockage principal. Les fichiers stockés dans le cache ne sont pas comparés automatiquement aux fichiers sur le volume de stockage NFS.

  N’utilisez pas cette option s’il existe un risque de modification directe d’un fichier sur le système de stockage sans l’avoir d’abord écrit dans le cache. Si cela se produit, la version mise en cache du fichier n’est pas synchronisée avec le fichier sur le serveur principal.

* **Opérations d’écriture supérieures à 15 %**  : cette option accélère les performances de lecture et d’écriture.

  Les lectures et écritures du client sont mises en cache. Les fichiers du cache sont supposés être plus récents que les fichiers sur le système de stockage principal. Les fichiers mis en cache ne sont automatiquement vérifiés par rapport aux fichiers du stockage principal que toutes les huit heures. Les fichiers modifiés dans le cache sont écrits dans le système de stockage principal une fois qu’ils se trouvent dans le cache pendant 20 minutes sans aucune modification supplémentaire.

  N’utilisez pas cette option si un client monte directement le volume de stockage principal, car il existe un risque que des fichiers soient obsolètes.

* **Les clients écrivent dans la cible NFS en ignorant le cache** : choisissez cette option si des clients dans votre workflow écrivent des données directement dans le système de stockage sans écrire au préalable dans le cache, ou si vous voulez optimiser la cohérence des données.

  Les fichiers demandés par les clients sont mis en cache, mais toute modification apportée à ces fichiers à partir du client est immédiatement retransmise au système de stockage backend. Les fichiers du cache sont fréquemment comparés aux versions backend pour y rechercher des mises à jour. Cette vérification assure la cohérence des données lorsque les fichiers sont modifiés directement sur le système de stockage plutôt que dans le cache.

Pour plus d’informations sur les autres options, consultez [Comprendre les modèles d’utilisation](cache-usage-models.md).

Ce tableau résume les différences entre les modèles d’utilisation :

[!INCLUDE [usage-models-table.md](includes/usage-models-table.md)]

> [!NOTE]
> La valeur **Vérification du serveur principal** s’affiche lorsque le cache compare automatiquement ses fichiers aux fichiers sources dans le stockage étendu. Toutefois, vous pouvez déclencher une comparaison en envoyant une requête client qui comprend une opération readdirplus sur le système de stockage principal. Readdirplus est une API NFS standard (également appelée lecture étendue) qui retourne des métadonnées d’annuaire, ce qui amène le cache à comparer et à mettre à jour les fichiers.

### <a name="create-an-nfs-storage-target"></a>Créer une cible de stockage NFS

### <a name="portal"></a>[Portail](#tab/azure-portal)

À partir du portail Azure, ouvrez votre instance de cache, puis cliquez sur **Cibles de stockage** dans la barre latérale gauche.

![capture d’écran des paramètres > page de la cible de stockage, avec deux cibles de stockage existantes dans une table et une mise en surbrillance autour du bouton + ajouter une cible de stockage au-dessus du tableau](media/add-storage-target-button.png)

La page **Cibles de stockage** répertorie toutes les cibles existantes et fournit un lien permettant d’en ajouter une nouvelle.

Cliquez sur le bouton **Ajouter une cible de stockage**.

![Capture d’écran de la page Ajouter la cible de stockage avec le type de cible défini sur NFS](media/add-nfs-target.png)

Fournissez les informations suivantes pour une cible de stockage NFS :

* **Nom de la cible de stockage** - Définissez un nom permettant d’identifier cette cible de stockage dans Azure HPC Cache.

* **Type de cible** - Choisissez **NFS**.

* **Nom d’hôte** - Entrez l’adresse IP ou le nom de domaine complet de votre système de stockage NFS (Utilisez un nom de domaine uniquement si votre cache a accès à un serveur DNS capable de résoudre le nom.) Vous pouvez entrer plusieurs adresses IP si votre système de stockage est référencé par plusieurs adresses IP.

* **Modèle d’utilisation** : choisissez l’un des profils de mise en cache des données en fonction de votre workflow, comme décrit dans [Choisir un modèle d’utilisation](#choose-a-usage-model) précédemment.

Lorsque vous avez terminé, cliquez sur **OK** pour ajouter la cible de stockage.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[Configurez Azure CLI pour Azure HPC Cache](./az-cli-prerequisites.md).

Utilisez la commande Azure CLI [az hpc-cache nfs-storage-target add](/cli/azure/hpc-cache/nfs-storage-target#az_hpc_cache_nfs_storage_target_add) pour créer la cible de stockage.

> [!NOTE]
> Les commandes Azure CLI vous obligent à créer un chemin d’accès d’espace de noms lorsque vous ajoutez une cible de stockage. Cela diffère du processus utilisé avec l’interface du portail Azure.

Fournissez ces valeurs en plus du nom du cache et du groupe de ressources du cache :

* ``--name`` - Définissez un nom permettant d’identifier cette cible de stockage dans Azure HPC Cache.
* ``--nfs3-target`` -L’adresse IP de votre système de stockage NFS. (Vous pouvez utiliser un nom de domaine complet ici si votre cache a accès à un serveur DNS capable de résoudre ce nom).
* ``--nfs3-usage-model`` -L’un des profils de mise en cache de données, décrit dans [Choisir un modèle d’utilisation](#choose-a-usage-model) précédemment.

  Vérifiez les noms des modèles d’utilisation à l’aide de la commande [az hpc-cache usage-model list](/cli/azure/hpc-cache/usage-model#az_hpc_cache_usage_model_list).

* ``--junction`` -Le paramètre junction lie le chemin d’accès du fichier virtuel orienté client à un chemin d’exportation sur le système de stockage.

  Une cible de stockage NFS peut avoir plusieurs chemins d’accès virtuels, à condition que chaque chemin d’accès représente une exportation ou un sous-répertoire différent sur le même système de stockage. Créez tous les chemins d’accès d’un système de stockage sur une cible de stockage.

  Vous pouvez [ajouter et modifier des chemins d’accès d’espace de noms](add-namespace-paths.md) sur une cible de stockage à tout moment.

  Le paramètre ``--junction`` utilise les valeurs suivantes :

  * ``namespace-path`` : chemin d’accès au fichier virtuel orienté client
  * ``nfs-export`` : exportation du système de stockage à associer au chemin d’accès orienté client
  * ``target-path`` (facultatif) : sous-répertoire de l’exportation, si nécessaire

  Exemple : ``--junction namespace-path="/nas-1" nfs-export="/datadisk1" target-path="/test"``

  Pour plus d’informations sur la fonctionnalité Espace de noms virtuels, consultez [Configurer un espace de noms agrégé](hpc-cache-namespace.md).

Exemple de commande :

```azurecli

az hpc-cache nfs-storage-target add --resource-group "hpc-cache-group" --cache-name "doc-cache0629" \
    --name nfsd1 --nfs3-target 10.0.0.4 --nfs3-usage-model WRITE_WORKLOAD_15 \
    --junction namespace-path="/nfs1/data1" nfs-export="/datadisk1" target-path=""
```

Sortie :
```azurecli

{- Finished ..
  "clfs": null,
  "id": "/subscriptions/<subscriptionID>/resourceGroups/hpc-cache-group/providers/Microsoft.StorageCache/caches/doc-cache0629/storageTargets/nfsd1",
  "junctions": [
    {
      "namespacePath": "/nfs1/data1",
      "nfsExport": "/datadisk1",
      "targetPath": ""
    }
  ],
  "location": "eastus",
  "name": "nfsd1",
  "nfs3": {
    "target": "10.0.0.4",
    "usageModel": "WRITE_WORKLOAD_15"
  },
  "provisioningState": "Succeeded",
  "resourceGroup": "hpc-cache-group",
  "targetType": "nfs3",
  "type": "Microsoft.StorageCache/caches/storageTargets",
  "unknown": null
}

```

---

## <a name="add-a-new-adls-nfs-storage-target"></a>Ajouter une nouvelle cible de stockage ADLS-NFS

Les cibles de stockage ADLS-NFS utilisent des conteneurs d’objets Blob Azure qui prennent en charge le protocole NFS 3.0.

Consultez [Prise en charge du protocole NFS 3.0](../storage/blobs/network-file-system-protocol-support.md) pour en savoir plus sur cette fonctionnalité.

Les cibles de stockage ADLS-NFS ont des similarités avec les cibles de stockage d’objets Blob et avec les cibles de stockage NFS. Par exemple :

* Comme pour une cible de stockage d’objets Blob, vous devez accorder à Azure HPC Cache l’autorisation d’[accéder à votre compte de stockage](#add-the-access-control-roles-to-your-account).
* À l’instar d’une cible de stockage NFS, vous devez définir un [modèle d’utilisation](#choose-a-usage-model) du cache.
* Étant donné que les conteneurs d’objets Blob compatibles NFS possèdent une structure hiérarchique compatible NFS, vous n’avez pas besoin d’utiliser le cache pour ingérer les données, et les conteneurs sont lisibles par d’autres systèmes NFS.

  Vous pouvez précharger des données dans un conteneur ADLS-NFS, les ajouter à un cache HPC en tant que cible de stockage, puis accéder aux données ultérieurement à partir de l’extérieur d’un cache HPC. Lorsque vous utilisez un conteneur d’objets Blob standard en tant que cible de stockage du cache HPC, les données sont écrites dans un format propriétaire et sont accessibles uniquement à partir d’autres produits compatibles avec le Azure HPC Cache.

Avant de pouvoir créer une cible de stockage ADLS-NFS, vous devez créer un compte de stockage compatible NFS. Suivez les étapes dans [Conditions préalables pour Azure HPC Cache](hpc-cache-prerequisites.md#nfs-mounted-blob-adls-nfs-storage-requirements) et les instructions dans [Monter le stockage Blob à l’aide de NFS](../storage/blobs/network-file-system-protocol-support-how-to.md). Si vous n’utilisez pas le même réseau virtuel pour le cache et le compte de stockage, assurez-vous que le réseau virtuel du cache peut accéder au réseau virtuel du compte de stockage.

Une fois votre compte de stockage configuré, vous pouvez créer un conteneur lors de la création de la cible de stockage.

Pour en savoir plus sur cette configuration, consultez [Utiliser le stockage blob monté sur NFS avec Azure HPC Cache](nfs-blob-considerations.md).

Pour créer une cible de stockage ADLS-NFS, ouvrez la page **Ajouter une cible de stockage** dans le portail Azure. (Des méthodes supplémentaires sont en cours de développement.)

![Capture d’écran de la page Ajouter la cible de stockage avec le type de cible défini sur ADLS-NFS](media/add-adls-target.png)

Entrez les informations suivantes.

* **Nom de la cible de stockage** - Définissez un nom permettant d’identifier cette cible de stockage dans Azure HPC Cache.
* **Type de cible** - Choisissez **ADLS-NFS**.
* **Compte de stockage** : sélectionnez le compte que vous souhaitez utiliser. Si votre compte de stockage NFS ne figure pas dans la liste, vérifiez qu’il est conforme aux conditions préalables et que le cache peut y accéder.

  Vous devez autoriser l’instance de cache à accéder au compte de stockage, en suivant la procédure décrite dans [Ajouter les rôles d’accès](#add-the-access-control-roles-to-your-account).

* **Conteneur de stockage** : sélectionnez le conteneur d’objets blob compatible NFS pour cette cible ou cliquez sur **Créer nouveau**.

* **Modèle d’utilisation** : choisissez l’un des profils de mise en cache des données en fonction de votre workflow, comme décrit dans [Choisir un modèle d’utilisation](#choose-a-usage-model) précédemment.

Lorsque vous avez terminé, cliquez sur **OK** pour ajouter la cible de stockage.

## <a name="view-storage-targets"></a>Afficher les cibles de stockage

Vous pouvez utiliser le portail Azure ou l’interface de ligne de commande Azure pour afficher les cibles de stockage déjà définies pour votre cache.

### <a name="portal"></a>[Portail](#tab/azure-portal)

À partir du portail Azure, ouvrez votre instance de cache, puis cliquez sur **Cibles de stockage**, qi se trouve sous le titre Paramètres dans la barre latérale gauche. La page des cibles de stockage répertorie toutes les cibles existantes et les commandes permettant de les ajouter ou de les supprimer.

Cliquez sur le nom d’une cible de stockage pour ouvrir la page de détails correspondante.

Pour en savoir plus, consultez [Afficher et gérer les cibles de stockage](manage-storage-targets.md) et [Modifier les cibles de stockage](hpc-cache-edit-storage.md).

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[Configurez Azure CLI pour Azure HPC Cache](./az-cli-prerequisites.md).

Utilisez l’option [az hpc-cache storage-target list](/cli/azure/hpc-cache/storage-target#az_hpc_cache_storage-target-list) pour afficher les cibles de stockage existantes pour un cache. Indiquez le nom du cache et le groupe de ressources (sauf si vous l’avez défini globalement).

```azurecli
az hpc-cache storage-target list --resource-group "scgroup" --cache-name "sc1"
```

Utilisez [az hpc-cache storage-target show](/cli/azure/hpc-cache/storage-target#az_hpc_cache_storage-target-list) pour afficher des détails sur une cible de stockage particulière. (Spécifiez la cible de stockage par nom.)

Exemple :

```azurecli
$ az hpc-cache storage-target show --cache-name doc-cache0629 --name nfsd1

{
  "clfs": null,
  "id": "/subscriptions/<subscription_ID>/resourceGroups/scgroup/providers/Microsoft.StorageCache/caches/doc-cache0629/storageTargets/nfsd1",
  "junctions": [
    {
      "namespacePath": "/nfs1/data1",
      "nfsExport": "/datadisk1",
      "targetPath": ""
    }
  ],
  "location": "eastus",
  "name": "nfsd1",
  "nfs3": {
    "target": "10.0.0.4",
    "usageModel": "WRITE_WORKLOAD_15"
  },
  "provisioningState": "Succeeded",
  "resourceGroup": "scgroup",
  "targetType": "nfs3",
  "type": "Microsoft.StorageCache/caches/storageTargets",
  "unknown": null
}
$
```

---

## <a name="next-steps"></a>Étapes suivantes

Après avoir créé des cibles de stockage, poursuivez avec ces tâches pour que votre cache soit opérationnel :

* [Configurer l’espace de noms agrégé](add-namespace-paths.md)
* [Monter le cache Azure HPC Cache](hpc-cache-mount.md)
* [Déplacer les données dans le stockage d’objets blob Azure](hpc-cache-ingest.md)

Si vous devez mettre à jour des paramètres, vous pouvez [modifier une cible de stockage](hpc-cache-edit-storage.md).
