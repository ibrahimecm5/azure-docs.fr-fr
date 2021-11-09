---
title: Configurer l’espace de noms agrégé d’Azure HPC Cache
description: Création de chemins d’accès côté client pour le stockage principal avec Azure HPC Cache
author: femila
ms.service: hpc-cache
ms.topic: how-to
ms.date: 04/22/2021
ms.author: femila
ms.openlocfilehash: 903ed4b3d17b0fc7290e5ce2755518cf768ac342
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131015368"
---
# <a name="set-up-the-aggregated-namespace"></a>Configurer l’espace de noms agrégé

Après avoir créé des cibles de stockage, vous devez également créer des chemins d’espace de noms associés. Les ordinateurs clients utilisent ces chemins d’accès virtuels pour accéder aux fichiers via le cache au lieu de se connecter directement au stockage principal. Ce système permet aux administrateurs de cache de modifier les systèmes de stockage principal sans avoir à réécrire les instructions du client.

Pour en savoir plus sur cette fonctionnalité, consultez [Planifier l’espace de noms agrégé](hpc-cache-namespace.md).

La page **Espace de noms** du portail Azure présente les chemins d’accès que les clients utilisent pour accéder à vos données via le cache. Utilisez cette page pour créer, supprimer ou modifier les chemins d’espace de noms. Vous pouvez également configurer des chemins d’espace de noms à l’aide d’Azure CLI.

Tous les chemins côté client qui ont été définis pour ce cache sont listés dans la page **Espace de noms**. Les cibles de stockage pour lesquelles aucun chemin d’espace de noms n’est défini n’apparaissent pas dans le tableau.

Vous pouvez trier les colonnes du tableau afin de mieux comprendre l’espace de noms agrégé de votre cache. Cliquez sur les flèches dans les en-têtes de colonne pour trier les chemins.

[![Capture d’écran de la page Espace de noms du portail avec un tableau contenant deux chemins. En-têtes de colonne : Chemin d’espace de noms, Cible de stockage, Chemin d’exportation, Sous-répertoire d’exportation et Stratégie d’accès client. Les noms de chemin de la première colonne sont des liens sur lesquels vous pouvez cliquer. Boutons principaux : Ajouter un chemin d’espace de noms, Actualiser, Supprimer](media/namespace-page.png) ](media/namespace-page.png#lightbox)

## <a name="add-or-edit-namespace-paths"></a>Ajouter ou modifier des chemins d’espace de noms

Vous devez créer au moins un chemin d’espace de noms pour que les clients puissent accéder à la cible de stockage. (Pour en savoir plus sur l’accès client, consultez [Monter le cache Azure HPC Cache](hpc-cache-mount.md).)

Si vous avez récemment ajouté une cible de stockage ou personnalisé une stratégie d’accès, une ou deux minutes peuvent s’écouler avant que vous puissiez créer un chemin d’accès d’espace de noms.

### <a name="blob-namespace-paths"></a>Chemins d’espace de noms d’objet blob

Une cible Stockage blob Azure ne peut avoir qu’un seul chemin d’espace de noms.

Suivez les instructions ci-dessous pour configurer ou modifier le chemin avec le portail Azure ou Azure CLI.

### <a name="portal"></a>[Portail](#tab/azure-portal)

Dans le portail Azure, chargez la page de paramètres **Espace de noms**. Vous pouvez ajouter, modifier ou supprimer des chemins d’espace de noms à partir de cette page.

* **Ajouter un nouveau chemin d’accès :** Cliquez sur le bouton **+ Ajouter** situé en haut et complétez les informations dans le panneau de modification.

  ![Capture d’écran des champs de modification de l’ajout d’un espace de noms avec sélection d’une cible de stockage d’objets blob Les chemins d’exportation et de sous-répertoire sont définis sur / et ne peuvent pas être modifiables.](media/namespace-add-blob.png)

  * Entrez le chemin que les clients doivent utiliser pour accéder à cette cible de stockage.

  * Sélectionnez la stratégie d’accès à utiliser pour ce chemin. Pour plus d’informations sur la personnalisation de l’accès client, consultez [Utiliser des stratégies d’accès client](access-policies.md).

  * Sélectionnez la cible de stockage dans la liste déroulante. Si une cible de stockage d’objets blob a déjà un chemin d’espace de noms, elle ne peut pas être sélectionnée.

  * Pour une cible Stockage blob Azure, les chemins d’exportation et de sous-répertoire sont automatiquement définis sur ``/``.

* **Modifier un chemin d’accès existant :** Cliquez sur le chemin d’espace de noms. Le panneau d’édition s’ouvre. Vous pouvez modifier le chemin et la stratégie d’accès, mais vous ne pouvez pas passer à une autre cible de stockage.

* **Suppression d’un chemin d’espace de noms :** Activez la case à cocher à gauche du chemin et cliquez sur le bouton **Supprimer**.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[Configurez Azure CLI pour Azure HPC Cache](./az-cli-prerequisites.md).

Lorsque vous utilisez Azure CLI, vous devez ajouter un chemin d’espace de noms lors de la création de la cible de stockage. Pour en savoir plus, consultez [Ajouter une cible Stockage blob Azure](hpc-cache-add-storage.md?tabs=azure-cli#add-a-new-azure-blob-storage-target).

Pour mettre à jour le chemin d’espace de noms d’une cible, utilisez la commande [az hpc-cache blob-storage-target update](/cli/azure/hpc-cache/blob-storage-target#az_hpc_cache_blob_storage_target_update). Les arguments de la commande de mise à jour sont similaires à ceux de la commande de création, sauf que vous ne transmettez pas le nom du conteneur ou le compte de stockage.

Vous ne pouvez pas supprimer le chemin d’espace de noms d’une cible Stockage blob avec Azure CLI, mais vous pouvez le remplacer par une autre valeur.

---

### <a name="nfs-namespace-paths"></a>Chemin d'espaces de noms NFS

Une cible de stockage NFS peut avoir plusieurs chemins d’accès virtuels, à condition que chaque chemin d’accès représente une exportation ou un sous-répertoire différent sur le même système de stockage.

Lors de la planification de votre espace de noms pour une cible de stockage NFS, souvenez-vous que chaque chemin doit être unique et ne peut pas être un sous-répertoire d’un autre chemin d’espace de noms. Par exemple, si vous avez un chemin d’espace de noms appelé ``/parent-a``, vous ne pouvez pas créer de chemins d’espace de noms tels que ``/parent-a/user1`` et ``/parent-a/user2``. Ces chemins d’accès de répertoire sont déjà accessibles dans l’espace de noms en tant que sous-répertoires de ``/parent-a``.

Tous les chemins d’espace de noms pour un système de stockage NFS sont créés sur une seule cible de stockage.

Pour chaque chemin d’espace de noms NFS, indiquez le chemin côté client, le chemin d’exportation du système de stockage et éventuellement un sous-répertoire d’exportation.

### <a name="portal"></a>[Portail](#tab/azure-portal)

Dans le portail Azure, chargez la page de paramètres **Espace de noms**. Vous pouvez ajouter, modifier ou supprimer des chemins d’espace de noms à partir de cette page.

* **Pour ajouter un nouveau chemin d’accès :** Cliquez sur le bouton **+ Ajouter** situé en haut et complétez les informations dans le panneau de modification.
* **Pour modifier un chemin d’accès existant :** Cliquez sur le chemin d’espace de noms. Le panneau de modification apparaît et vous pouvez modifier le chemin.
* **Pour supprimer un chemin d’espace de noms :** Activez la case à cocher à gauche du chemin et cliquez sur le bouton **Supprimer**.

Renseignez ces valeurs pour chaque chemin d’espace de noms :

* **Chemin d’espace de noms** - Chemin d’accès du fichier côté client.

* **Stratégie d’accès client** - Sélectionnez la stratégie d’accès à utiliser pour ce chemin. Pour plus d’informations sur la personnalisation de l’accès client, consultez [Utiliser des stratégies d’accès client](access-policies.md).

* **Cible de stockage** - Si vous créez un nouveau chemin d’espace de noms, sélectionnez une cible de stockage dans le menu déroulant.

* **Chemin d’exportation** - Entrez le chemin de l’exportation NFS. Veillez à ne pas faire d’erreur dans le nom du chemin d’exportation. Le portail valide la syntaxe pour ce champ mais ne vérifie pas le chemin d’exportation tant que vous n’avez pas validé la modification.

* **Sous-répertoire d’exportation** - Si vous souhaitez que ce chemin monte un sous-répertoire spécifique de l’exportation, entrez-le ici. Sinon, laissez ce champ vide.

![Capture d’écran de la page Espace de noms du portail avec la page de modification ouverte à droite. Le formulaire de modification affiche les paramètres du chemin d’espace de noms d’une cible de stockage NFS.](media/namespace-edit-nfs.png)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[Configurez Azure CLI pour Azure HPC Cache](./az-cli-prerequisites.md).

Lorsque vous utilisez Azure CLI, vous devez ajouter au moins un chemin d’espace de noms lors de la création de la cible de stockage. Pour en savoir plus, consultez [Ajouter une cible de stockage NFS](hpc-cache-add-storage.md?tabs=azure-cli#add-a-new-nfs-storage-target).

Pour mettre à jour le chemin d’espace de noms d’une cible ou ajouter des chemins supplémentaires, utilisez la commande [az hpc-cache nfs-storage-target update](/cli/azure/hpc-cache/nfs-storage-target#az_hpc_cache_nfs_storage_target_update). Utilisez l’option ``--junction`` pour spécifier tous les chemins d’espace de noms souhaités.

Les options utilisées pour la commande de mise à jour sont similaires à celles de la commande « create », sauf que vous ne transmettez pas les informations du système de stockage (adresse IP ou nom d’hôte) et le modèle d’utilisation est facultatif. Pour en savoir plus sur la syntaxe de l’option ``--junction``, consultez [Ajouter une nouvelle cible de stockage NFS](hpc-cache-add-storage.md?tabs=azure-cli#add-a-new-nfs-storage-target).

---

### <a name="adls-nfs-namespace-paths"></a>Chemin d’espace de noms ADLS-NFS

Comme une cible de stockage blob ordinaire, une cible de stockage ADLS-NFS n’a qu’une seule exportation, de sorte qu’elle ne peut avoir qu’un seul chemin d’accès pour l’espace de noms.

Suivez les instructions ci-dessous pour configurer ou modifier le chemin avec le portail Azure.

Chargez la page des paramètres **Espace de noms**.

* **Ajouter un nouveau chemin d’accès :** Cliquez sur le bouton **+ Ajouter** situé en haut et complétez les informations dans le panneau de modification.

  ![Capture d’écran des champs de modification de l’ajout d’un espace de noms avec sélection d’une cible de stockage ADLS-NFS. Les chemins d’exportation et de sous-répertoire sont définis sur / et ne peuvent pas être modifiables.](media/namespace-add-adls.png)

  * Entrez le chemin que les clients doivent utiliser pour accéder à cette cible de stockage.

  * Sélectionnez la stratégie d’accès à utiliser pour ce chemin. Pour plus d’informations sur la personnalisation de l’accès client, consultez [Utiliser des stratégies d’accès client](access-policies.md).

  * Sélectionnez la cible de stockage dans la liste déroulante. Si une cible de stockage ADLS-NFS a déjà un chemin d’espace de noms, elle ne peut pas être sélectionnée.

  * Pour une cible de stockage ADLS-NFS, les chemins d’exportation et de sous-répertoire sont automatiquement définis sur ``/``.

* **Modifier un chemin d’accès existant :** Cliquez sur le chemin d’espace de noms. Le panneau d’édition s’ouvre. Vous pouvez modifier le chemin et la stratégie d’accès, mais vous ne pouvez pas passer à une autre cible de stockage.

* **Suppression d’un chemin d’espace de noms :** Activez la case à cocher à gauche du chemin et cliquez sur le bouton **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Après avoir créé l’espace de noms agrégé pour vos cibles de stockage, vous pouvez monter des clients sur le cache. Pour en savoir plus, consultez les articles suivants.

* [Monter le cache Azure HPC Cache](hpc-cache-mount.md)
* [Déplacer les données dans le stockage d’objets blob Azure](hpc-cache-ingest.md)
