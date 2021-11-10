---
title: Monter le Stockage Blob Azure avec le protocole NFS 3.0 | Microsoft Docs
description: Découvrez comment monter un conteneur dans le stockage Blob à partir d’une machine virtuelle Azure ou d’un client qui s’exécute localement à l’aide du protocole NFS 3.0.
author: normesta
ms.subservice: blobs
ms.service: storage
ms.topic: conceptual
ms.date: 06/21/2021
ms.author: normesta
ms.reviewer: yzheng
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 6b22b99cdd883ed8dedb90f925a918fe9c25d9bb
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131444873"
---
# <a name="mount-blob-storage-by-using-the-network-file-system-nfs-30-protocol"></a>Monter le Stockage Blob avec le protocole NFS 3.0

Vous pouvez monter un conteneur dans le stockage Blob à partir d’une machine virtuelle Azure basée sur Linux ou d’un système Linux qui s’exécute localement à l’aide du protocole NFS 3.0. Cet article fournit un guide pas à pas. Pour en savoir plus sur la prise en charge du protocole NFS 3.0 dans le service Stockage Blob, consultez [Prise en charge du protocole NFS (Network File System) 3.0 dans Stockage Blob Azure](network-file-system-protocol-support.md).

## <a name="step-1-create-an-azure-virtual-network-vnet"></a>Étape 1 : Créer un réseau virtuel Azure (VNet)

Votre compte de stockage doit se trouver dans un réseau virtuel. Un réseau virtuel permet aux clients de se connecter en toute sécurité à votre compte de stockage. Pour en savoir plus sur le réseau virtuel et sur la façon d’en créer un, consultez la [documentation relative au réseau virtuel](../../virtual-network/index.yml).

> [!NOTE]
> Les clients du même réseau virtuel peuvent monter des conteneurs dans votre compte. Vous pouvez également monter un conteneur à partir d’un client qui s’exécute sur un réseau local, mais vous devrez d’abord connecter votre réseau local à votre réseau virtuel. Consultez [Connexions réseau prises en charge](network-file-system-protocol-support.md#supported-network-connections).

## <a name="step-2-configure-network-security"></a>Étape 2 : Configurer la sécurité réseau

La seule façon de sécuriser les données dans votre compte consiste à utiliser un réseau virtuel et d’autres paramètres de sécurité réseau. Tous les autres outils utilisé pour sécuriser les données, notamment l’autorisation de clé de compte, la sécurité Azure Active Directory (AD) et les listes de contrôle d’accès (ACL), ne sont pas encore pris en charge dans les comptes sur lesquels la prise en charge du protocole NFS 3.0 est activée.

Pour sécuriser les données dans votre compte, consultez les recommandations suivantes : [Recommandations de sécurité réseau pour Stockage Blob](security-recommendations.md#networking).

## <a name="step-3-create-and-configure-a-storage-account"></a>Étape 3 : Créer et configurer un compte de stockage

Pour monter un conteneur à l’aide du protocole NFS 3.0, vous devez créer un compte de stockage. Vous ne pouvez pas activer les comptes existants.

Le protocole NFS 3.0 est pris en charge pour les comptes de stockage universel v2 standard et pour les comptes de stockage d’objets blob de blocs Premium. Pour plus d’informations sur ces types de comptes de stockage, consultez [Vue d’ensemble des comptes de stockage](../common/storage-account-overview.md).

Au fur et à mesure que vous configurez le compte, choisissez les valeurs suivantes :

|Paramètre | Niveau de performance Premium | Niveau de performance Standard
|----|---|---|
|Emplacement|Toutes les régions disponibles |Toutes les régions disponibles
|Performances|Premium| Standard
|Type de compte|BlockBlobStorage| Universel v2
|Réplication|Stockage localement redondant (LRS), Stockage redondant interzone (ZRS)| Stockage localement redondant (LRS), Stockage redondant interzone (ZRS)
|Méthode de connectivité|Point de terminaison public (réseaux sélectionnés) pour point de terminaison privé |Point de terminaison public (réseaux sélectionnés) pour point de terminaison privé
|Espace de noms hiérarchique|activé|activé
|NFS V3|activé |activé

Vous pouvez accepter les valeurs par défaut pour tous les autres paramètres.

## <a name="step-4-create-a-container"></a>Étape 4 : Créer un conteneur

Créez un conteneur dans votre compte de stockage à l’aide de l’un de ces outils ou kits de développement logiciel (SDK) :

|Outils|Kits SDK|
|---|---|
|[Azure portal](https://portal.azure.com)|[.NET](data-lake-storage-directory-file-acl-dotnet.md#create-a-container)|
|[AZCopy](../common/storage-use-azcopy-v10.md#transfer-data)|[Java](data-lake-storage-directory-file-acl-java.md)|
|[PowerShell](data-lake-storage-directory-file-acl-powershell.md#create-a-container)|[Python](data-lake-storage-directory-file-acl-python.md#create-a-container)|
|[Azure CLI](data-lake-storage-directory-file-acl-cli.md#create-a-container)|[JavaScript](data-lake-storage-directory-file-acl-javascript.md)|
||[REST](/rest/api/storageservices/create-container)|

> [!NOTE]
> Par défaut, l’option Squash racine d’un nouveau conteneur est `no root squash`. Toutefois, vous pouvez la remplacer par `root squash` ou `all squash`. Pour plus d’informations sur les options Squash, reportez-vous à la documentation de votre système d’exploitation.

L’image suivante montre les options Squash telles qu’elles apparaissent dans le portail Azure.

> [!div class="mx-imgBorder"]
> ![Options Squash dans le portail Azure](./media/network-file-system-protocol-how-to/squash-options-azure-portal.png)

## <a name="step-5-mount-the-container"></a>Étape 5 : Monter le conteneur

Créez un répertoire sur votre système Linux, puis montez un conteneur dans le compte de stockage.

1. Sur un système Linux, créez un répertoire.

   ```
   mkdir -p /mnt/test
   ```

2. Montez un conteneur à l’aide de la commande suivante.

   ```
   mount -o sec=sys,vers=3,nolock,proto=tcp <storage-account-name>.blob.core.windows.net:/<storage-account-name>/<container-name>  /mnt/test
   ```

   - Remplacez la valeur d’espace réservé `<storage-account-name>` qui s’affiche dans cette commande par le nom de votre compte de stockage.

   - Remplacez l’espace réservé `<container-name>` par le nom de votre conteneur.

---

## <a name="resolve-common-errors"></a>Résoudre les erreurs courantes

|Error | Cause/Résolution|
|---|---|
|`Access denied by server while mounting`|Vérifiez que votre client s’exécute sur un sous-réseau pris en charge. Consultez les [emplacements réseau pris en charge](network-file-system-protocol-support.md#supported-network-connections).|
|`No such file or directory`| Vous devez taper la commande de montage et ses paramètres directement dans le terminal. Si vous copiez et collez une partie de cette commande dans le terminal à partir d’une autre application, les caractères masqués dans les informations collées peuvent provoquer l’apparition de cette erreur. Cette erreur peut également se produire si le compte n’est pas activé pour NFS 3.0. |
|`Permision denied`| Le mode par défaut d’un nouveau conteneur NFS v3 est 0750. Les utilisateurs non racines n’ont pas accès au volume. Si un utilisateur non racine doit y accéder, il doit passer en mode 0755. Exemple de commande : `sudo chmod 0755 /mnt/<newcontainer>`|
|`EINVAL ("Invalid argument"`) |Cette erreur peut se produire lorsqu’un client tente d’effectuer les opérations suivantes :<li>Écrire des données dans un objet blob créé à partir d’un point de terminaison d’objet blob.<li>Supprimer un objet blob qui comprend un instantané ou qui se trouve dans un conteneur auquel est appliquée une stratégie WORM.|
|`EROFS ("Read-only file system"`) |Cette erreur peut se produire lorsqu’un client tente d’effectuer les opérations suivantes :<li>Écrire des données dans un objet blob ou supprimer un objet blob dont le bail est actif.<li>Écrire des données dans un objet blob ou supprimer un objet blob qui se trouve dans un conteneur auquel est appliquée une stratégie WORM. |
|`NFS3ERR_IO/EIO ("Input/output error"`) |Cette erreur peut se produire lorsqu’un client tente de lire, d’écrire ou de définir des attributs dans des objets blob qui sont stockés dans le niveau d’accès de l’archive. |
|`OperationNotSupportedOnSymLink` erreurs| Cette erreur peut être retournée lors d’une opération d’écriture effectuée par le biais d’un objet blob ou d’une API Azure Data Lake Storage Gen2. L’utilisation de ces API pour écrire ou supprimer des liens symboliques créés à l’aide du protocole NFS 3.0 n’est pas autorisée. Veillez à utiliser le point de terminaison NFS v3 pour les liens symboliques. |
|`mount: /mnt/test: bad option;`| Installez le programme d'aide nfs en utilisant **sudo apt install nfs-common**.|

## <a name="see-also"></a>Voir aussi

- [Prise en charge du protocole NFS 3.0 dans le service Stockage Blob Azure](network-file-system-protocol-support.md)
- [Problèmes connus concernant la prise en charge du protocole NFS 3.0 dans le service Stockage Blob Azure](network-file-system-protocol-known-issues.md)
