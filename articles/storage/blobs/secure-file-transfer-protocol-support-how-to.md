---
title: Se connecter à Stockage Blob Azure à l’aide du protocole SFTP (préversion) | Microsoft Docs
description: Découvrez comment activer la prise en charge de SFTP pour votre compte Stockage Blob Azure afin de pouvoir vous connecter directement à votre compte Stockage Azure à l’aide d’un client SFTP.
author: normesta
ms.subservice: blobs
ms.service: storage
ms.topic: conceptual
ms.date: 11/15/2021
ms.author: normesta
ms.reviewer: ylunagaria
ms.openlocfilehash: b5249fa06dad80cb675723f724b12cf638c452fe
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132557325"
---
# <a name="connect-to-azure-blob-storage-by-using-the-secure-file-transfer-sftp-protocol-preview"></a>Se connecter à Stockage Blob Azure à l’aide du protocole SFTP (préversion)

Vous pouvez vous connecter en toute sécurité au point de terminaison Stockage Blob d’un compte Stockage Azure en utilisant un client SFTP, puis charger et télécharger des fichiers. Cet article explique comment activer la prise en charge du protocole SFTP, puis comment vous connecter à Stockage Blob à l’aide d’un client SFTP. 

Pour en savoir plus sur la prise en charge du protocole SFTP dans Stockage Blob Azure, consultez [Prise en charge du protocole SFTP dans Stockage Blob Azure](secure-file-transfer-protocol-support.md).

> [!IMPORTANT]
> La prise en charge du protocole SFTP est actuellement en PRÉVERSION et est disponible dans [ces régions](secure-file-transfer-protocol-support.md#regional-availability).
>
> Pour connaître les conditions juridiques qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou plus généralement non encore en disponibilité générale, consultez [l’Avenant aux conditions d’utilisation des préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
> Pour vous inscrire à la préversion, voir [ce formulaire](https://forms.office.com/r/gZguN0j65Y).

## <a name="prerequisites"></a>Prérequis

- Un compte de stockage d’objets blobs de blocs Premium ou v2 universel Standard. Pour plus d’informations sur ces types de comptes de stockage, consultez [Vue d’ensemble des comptes de stockage](../common/storage-account-overview.md).

- L’option de redondance de compte du compte de stockage est définie sur stockage localement redondant (LRS) ou stockage redondant interzone (ZRS).

- La fonctionnalité d’espace de noms hiérarchique du compte doit être activée. Pour activer la fonctionnalité d’espace de noms hiérarchique, consultez [Mettre à niveau Stockage Blob Azure avec les capacités d’Azure Data Lake Storage Gen2](upgrade-to-data-lake-storage-gen2-how-to.md).

- Si vous vous connectez à partir d’un réseau local, assurez-vous que votre client autorise les communications sortantes via le port 22. Le protocole SFTP utilise ce port.

## <a name="register-the-feature"></a>Inscrire la fonctionnalité

Avant de pouvoir activer la prise en charge de SFTP, vous devez inscrire la fonctionnalité SFTP dans votre abonnement.

1. Connectez-vous au [portail Azure](https://portal.azure.com/).

2. Ouvrez la page de configuration de votre abonnement.

3. Sous **Paramètres**, sélectionnez **Fonctionnalités d’évaluation**.

   > [!div class="mx-imgBorder"]
   > ![Paramètre Fonctionnalités d’évaluation](./media/secure-file-transfer-protocol-support-how-to/preview-features-setting.png)

4. Dans la page **Fonctionnalités d’évaluation**, sélectionnez la fonctionnalité **AllowSFTP**, puis sélectionnez **Inscrire**.

### <a name="verify-feature-registration"></a>Vérifier l’inscription des fonctionnalités

Vérifiez que la fonctionnalité est inscrite avant de passer aux autres étapes de cet article. 

1. Ouvrez la page **Fonctionnalités d’évaluation** de votre abonnement. 

2. Recherchez la fonctionnalité **AllowSFTP** et assurez-vous que l’état **Inscrite** s’affiche dans la colonne **État**.

## <a name="enable-sftp-support"></a>Activer la prise en charge de SFTP

1. Dans le [Portail Azure](https://portal.azure.com/), accédez à votre compte de stockage.

2. Sous **Paramètres**, sélectionnez **SFTP**.

   > [!NOTE]
   > Cette option s’affiche uniquement si la fonctionnalité d’espace de noms hiérarchique du compte a été activée. Pour activer la fonctionnalité d’espace de noms hiérarchique, consultez [Mettre à niveau Stockage Blob Azure avec les capacités d’Azure Data Lake Storage Gen2](upgrade-to-data-lake-storage-gen2-how-to.md).

3. Sélectionnez **Activer SFTP**. 

   > [!div class="mx-imgBorder"]
   > ![Activer le bouton SFTP](./media/secure-file-transfer-protocol-support-how-to/sftp-enable-option.png)

   >[!NOTE]
   > Si aucun utilisateur local n’apparaît dans la page de configuration de SFTP, vous devrez en ajouter au moins un. Pour ajouter des utilisateurs locaux, consultez la section suivante.

## <a name="configure-permissions"></a>Configurer les autorisations

Stockage Azure ne prend pas en charge la signature d’accès partagé (SAP) ni l’authentification Azure Active Directory (Azure AD) pour accéder au point de terminaison SFTP. Au lieu de cela, vous devez utiliser une identité appelée « utilisateur local » qui peut être sécurisée à l’aide d’un mot de passe généré par Azure ou d’une paire de clés Secure Shell (SSH). Pour accorder l’accès à un client de connexion, le compte de stockage doit avoir une identité associée au mot de passe ou à la paire de clés. Cette identité est appelée *utilisateur local*. 

Dans cette section, vous allez apprendre à créer un utilisateur local, à choisir une méthode d’authentification, puis à attribuer des autorisations pour cet utilisateur local. 

Pour en savoir plus sur le modèle d’autorisations SFTP, consultez la section [Modèle d’autorisations SFTP](secure-file-transfer-protocol-support.md#sftp-permissions-model). 

1. Dans le [Portail Azure](https://portal.azure.com/), accédez à votre compte de stockage.

2. Sous **Paramètres**, sélectionnez **SFTP**, puis **Ajouter un utilisateur local**. 

   > [!div class="mx-imgBorder"]
   > ![Bouton Ajouter des utilisateurs locaux](./media/secure-file-transfer-protocol-support-how-to/sftp-local-user.png)

3. Dans le volet de configuration **Ajouter un utilisateur local**, ajoutez le nom d’un utilisateur, puis sélectionnez les méthodes d’authentification que vous souhaitez associer à cet utilisateur local. Vous pouvez associer un mot de passe ou une clé SSH. 

   > [!IMPORTANT]
   > Bien que vous puissiez activer les deux formes d’authentification, les clients SFTP peuvent se connecter en utilisant une seule d’entre elles. L’authentification multifacteur, qui requiert à la fois un mot de passe valide et une paire de clés publique et privée valide pour une authentification réussie, n’est pas prise en charge.

   Si vous sélectionnez **Sécuriser avec un mot de passe**, votre mot de passe s’affichera lorsque vous aurez effectué toutes les étapes du volet de configuration **Ajouter un utilisateur local**.

   Si vous sélectionnez **Sécuriser avec une clé publique SSH**, sélectionnez **Ajouter une source de clé** pour spécifier une source de clé. 

   > [!div class="mx-imgBorder"]
   > ![Volet de configuration de l’utilisateur local](./media/secure-file-transfer-protocol-support-how-to/add-local-user-config-page.png)

   Le tableau suivant décrit chaque option de source de clé :

   | Option | Guidance |
   |----|----|
   | Générer une nouvelle paire de clés | Utilisez cette option pour créer une paire de clés publique-privée. La clé publique est stockée dans Azure avec le nom de clé que vous avez fourni. La clé privée peut être téléchargée une fois que l’utilisateur local a été ajouté. |
   | Utiliser la clé existante stockée dans Azure | Utilisez cette option si vous souhaitez utiliser une clé publique déjà stockée dans Azure. Pour rechercher les clés existantes dans Azure, consultez [Répertorier les clés](/azure/virtual-machines/ssh-keys-portal#list-keys). Lorsque les clients SFTP se connectent à Stockage Blob Azure, ces clients doivent fournir la clé privée associée à cette clé publique. |
   | Utiliser la clé publique existante | Utilisez cette option si vous souhaitez charger une clé publique qui est stockée en dehors d’Azure. Si vous n’avez pas de clé publique, mais que vous souhaitez en générer une en dehors d’Azure, consultez [Générer des clés avec ssh-keygen](/azure/virtual-machines/linux/create-ssh-keys-detailed#generate-keys-with-ssh-keygen). |

4. Sélectionnez **Suivant** pour ouvrir l’onglet **Autorisations du conteneur** du volet de configuration.

5. Dans l’onglet **Autorisations du conteneur**, sélectionnez les conteneurs que vous souhaitez mettre à la disposition de cet utilisateur local. Ensuite, sélectionnez les types d’opérations que vous voulez permettre à cet utilisateur local d’effectuer.

   > [!div class="mx-imgBorder"]
   > ![Onglet Autorisations du conteneur](./media/secure-file-transfer-protocol-support-how-to/container-perm-tab.png)

6. Dans la zone d’édition **Répertoire de base**, saisissez le nom du conteneur ou le chemin d’accès au répertoire (y compris le nom du conteneur) qui sera l’emplacement par défaut associé à cet utilisateur local. 

   Pour en savoir plus sur le répertoire de base, consultez [Répertoire de base](secure-file-transfer-protocol-support.md#home-directory).

7. Sélectionnez le **bouton Ajouter** pour ajouter l’utilisateur local.

   Si vous avez activé l’authentification par mot de passe, le mot de passe généré par Azure s’affiche dans une boîte de dialogue une fois que l’utilisateur local a été ajouté. 

   > [!IMPORTANT]
   > Vous ne pouvez pas récupérer ce mot de passe ultérieurement. Veillez donc à le copier, puis à le stocker dans un endroit où vous pouvez le retrouver.

   Si vous avez choisi de générer une nouvelle paire de clés, vous êtes invité à télécharger la clé privée de cette paire de clés une fois que l’utilisateur local a été ajouté.

## <a name="connect-an-sftp-client"></a>Connecter un client SFTP

Vous pouvez utiliser n’importe quel client SFTP pour vous connecter et transférer des fichiers en toute sécurité. La capture d’écran suivante montre une session Windows PowerShell qui utilise [Open SSH](/windows-server/administration/openssh/openssh_overview) et l’authentification par mot de passe pour se connecter, puis charger un fichier nommé `logfile.txt`.  

> [!div class="mx-imgBorder"]
> ![Connexion avec Open SSH](./media/secure-file-transfer-protocol-support-how-to/ssh-connect-and-transfer.png)

> [!NOTE]
> Vous pouvez être invité à approuver une clé d’hôte. Au cours de la préversion publique, les clés d’hôte valides sont publiées [ici](secure-file-transfer-protocol-host-keys.md).  

Une fois le transfert terminé, vous pouvez afficher et gérer le fichier dans le portail Azure. 

> [!div class="mx-imgBorder"]
> ![Le fichier chargé s’affiche dans le compte de stockage](./media/secure-file-transfer-protocol-support-how-to/uploaded-file-in-storage-account.png)

> [!NOTE]
> Le portail Azure utilise l’API REST Blob et l’API REST Data Lake Storage Gen2. La possibilité d’interagir avec un fichier chargé dans le portail Azure illustre l’interopérabilité entre SFTP et REST.

Consultez la documentation de votre client SFTP pour savoir comment vous connecter et transférer des fichiers.

## <a name="see-also"></a>Voir aussi

- [Prise en charge du protocole SFTP dans Stockage Blob Azure](secure-file-transfer-protocol-support.md)
- [Problèmes connus concernant la prise en charge du protocole SFTP dans Stockage Blob Azure](secure-file-transfer-protocol-known-issues.md)
