---
title: Prise en charge du protocole SFTP pour Stockage Blob Azure (préversion) | Microsoft Docs
description: Stockage Blob prend désormais en charge le protocole SFTP.
author: normesta
ms.subservice: blobs
ms.service: storage
ms.topic: conceptual
ms.date: 11/15/2021
ms.custom: references_regions
ms.author: normesta
ms.reviewer: ylunagaria
ms.openlocfilehash: 0cfd4a8dc9968ad9ef9e7538ac51414deb974fdb
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132557345"
---
# <a name="secure-file-transfer-protocol-sftp-support-for-azure-blob-storage-preview"></a>Prise en charge du protocole SFTP pour Stockage Blob Azure (préversion)

Stockage Blob prend désormais en charge le protocole SFTP. Cette prise en charge permet de se connecter de manière sécurisée aux comptes Stockage Blob via un point de terminaison SFTP, ce qui vous permet d’exploiter SFTP pour l’accès aux fichiers, le transfert de fichiers, ainsi que la gestion des fichiers.  

> [!IMPORTANT]
> La prise en charge de SFTP est actuellement en PRÉVERSION et est disponible dans [ces régions](secure-file-transfer-protocol-support.md#regional-availability).
>
> Pour connaître les conditions juridiques qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou plus généralement non encore en disponibilité générale, consultez [l’Avenant aux conditions d’utilisation des préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
> Pour vous inscrire à la préversion, voir [ce formulaire](https://forms.office.com/r/gZguN0j65Y).

Azure permet le transfert sécurisé de données vers des comptes Stockage Blob à l’aide de l’API REST du service BLOB d’Azure, des Kits de développement logiciel (SDK) Azure et d’outils tels qu’AzCopy. Toutefois, les charges de travail héritées utilisent souvent des protocoles de transfert de fichiers traditionnels tels que SFTP. Vous pouvez mettre à jour des applications personnalisées pour utiliser l’API REST et les Kits de développement logiciel (SDK) Azure, mais uniquement en apportant des modifications importantes au code.

Avant la publication de cette fonctionnalité, si vous vouliez utiliser SFTP pour transférer des données vers Stockage Blob Azure, vous deviez soit acheter un produit tiers, soit orchestrer votre propre solution. Vous deviez créer une machine virtuelle dans Azure pour héberger un serveur SFTP, puis trouver un moyen de déplacer les données dans le compte de stockage. 

Désormais, grâce à la prise en charge de SFTP pour Stockage Blob Azure, vous pouvez activer un point de terminaison SFTP pour les comptes Stockage Blob avec un seul paramètre. Vous pouvez ensuite configurer des identités d’utilisateur local pour l’authentification afin de transférer des données en toute sécurité sans avoir à effectuer d’autres tâches. 

Cet article décrit la prise en charge de SFTP pour Stockage Blob Azure. Pour savoir comment activer SFTP pour votre compte de stockage, consultez [Se connecter à Stockage Blob Azure à l’aide du protocole SFTP (préversion)](secure-file-transfer-protocol-support-how-to.md).

## <a name="sftp-and-the-hierarchical-namespace"></a>SFTP et l’espace de noms hiérarchique

La prise en charge du protocole SFTP impose que les blobs soient organisés dans un espace de noms hiérarchique. La possibilité d’utiliser un espace de noms hiérarchique a été introduite par Azure Data Lake Storage Gen2. Il organise les objets (fichiers) selon une hiérarchie de répertoires et sous-répertoires, de la même façon que le système de fichiers sur votre ordinateur. L’espace de noms hiérarchique est mis à l’échelle de façon linéaire, et ne dégrade pas la capacité ou les performances des données. 

Différents protocoles s’étendent à partir de l’espace de noms hiérarchique. SFTP est l’un de ces protocoles disponibles.

> [!div class="mx-imgBorder"]
> ![espace de noms hiérarchique](./media/secure-file-transfer-protocol-support/hierarchical-namespace-and-sftp-support.png)

## <a name="sftp-permissions-model"></a>Modèle d’autorisations SFTP

Stockage Azure ne prend pas en charge la signature d’accès partagé (SAP) ni l’authentification Azure Active Directory (Azure AD) pour la connexion des clients SFTP. Au lieu de cela, les clients SFTP doivent utiliser un mot de passe ou une clé privée Secure Shell (SSH) comme informations d’identification.

Pour accorder l’accès à un client de connexion, le compte de stockage doit avoir une identité associée à ces informations d’identification. Cette identité est appelée utilisateur local. Les utilisateurs locaux sont une nouvelle forme de gestion des identités fournie avec la prise en charge de SFTP. Vous pouvez ajouter 1000 utilisateurs locaux à un compte de stockage.

Pour configurer les autorisations d’accès, vous allez créer un utilisateur local et choisir des méthodes d’authentification. Ensuite, pour chaque conteneur dans votre compte, vous pouvez spécifier le niveau d’accès que vous souhaitez accorder à cet utilisateur.
 
> [!NOTE]
> Une fois que vos données sont ingérées dans Stockage Azure, vous pouvez utiliser la totalité des paramètres de sécurité du stockage Azure. Bien que les mécanismes d’autorisation tels que le contrôle d’accès en fonction du rôle (RBAC) et les listes de contrôle d’accès ne soient pas pris en charge pour autoriser un client SFTP de connexion, ils peuvent être utilisés pour autoriser l’accès via les outils Azure (tels que Portail Azure, Azure CLI, les commandes Azure PowerShell et AzCopy), ainsi que les Kits de développement logiciel (SDK) Azure et les API REST Azure. 
> 
> Pour en savoir plus, consultez [Modèle de contrôle d’accès dans Azure Data Lake Storage Gen2](data-lake-storage-access-control-model.md).

## <a name="authentication-methods"></a>Méthodes d’authentification

Vous pouvez authentifier un client SFTP de connexion à l’aide d’un mot de passe ou d’une paire de clés publique-privée Secure Shell (SSH). Vous pouvez configurer les deux formes d’authentification et laisser les clients de connexion choisir celle à utiliser. Toutefois, l’authentification multifacteur, qui requiert à la fois un mot de passe valide et une paire de clés publique-privée valide pour une authentification réussie, n’est pas prise en charge. 

#### <a name="passwords"></a>Mots de passe

Les mots de passe sont générés pour vous. Si vous choisissez l’authentification par mot de passe, votre mot de passe est fourni une fois que vous avez terminé la configuration d’un utilisateur local. Veillez à copier ce mot de passe et à l’enregistrer à un emplacement où vous pourrez le retrouver ultérieurement. Vous ne pourrez pas récupérer ce mot de passe à partir d’Azure. Si vous perdez le mot de passe, vous devrez en générer un nouveau. Pour des raisons de sécurité, vous ne pouvez pas définir vous-même le mot de passe.   

#### <a name="ssh-key-pairs"></a>Paires de clés SSH

Une paire de clés publique-privée est la forme la plus courante d’authentification pour Secure Shell (SSH). La clé privée est secrète et n’est connue que de vous. La clé publique est stockée dans Azure. Lorsqu’un client SSH se connecte au compte de stockage, il envoie un message contenant la clé privée et la signature. Azure valide le message et vérifie que l’utilisateur et la clé sont reconnus par le compte de stockage. Pour en savoir plus, consultez [Vue d’ensemble de SSH et des clés](/azure/virtual-machines/linux/ssh-from-windows##overview-of-ssh-and-keys).

Si vous choisissez de vous authentifier avec une paire de clés publique-privée, vous pouvez en générer une, en utiliser une déjà stockée dans Azure ou fournir à Azure la clé publique d’une paire de clés publique-privée existante. 

## <a name="container-permissions"></a>Autorisations du conteneur

Dans la version actuelle, vous ne pouvez spécifier que des autorisations au niveau du conteneur. Les autorisations au niveau du répertoire ne sont pas prises en charge. Vous pouvez choisir les conteneurs auxquels vous souhaitez accorder l’accès et le niveau d’accès que vous souhaitez fournir (Lecture, Écriture, Liste, Suppression et Création). Ces autorisations s’appliquent à tous les répertoires et sous-répertoires du conteneur. Vous pouvez accorder à chaque utilisateur local un accès à un maximum de 100 conteneurs. Les autorisations de conteneur peuvent également être mises à jour après la création d’un utilisateur local. Le tableau suivant décrit chaque autorisation plus en détail.

| Autorisation | Code d’autorisation | Description |
|---|---|---|
| Lire | r | <li>Lire le contenu des fichiers</li> |
| Write | w | <li>Charger le fichier</li><li>Créer un répertoire</li><li>Charger des répertoires</li> |
| List | l | <li>Répertorier le contenu dans le conteneur</li><li>Répertorier le contenu dans les répertoires</li> |
| Supprimer | d | <li>Supprimer des fichiers/répertoires</li> |
| Créer | c | <li>Charger le fichier s’il n’existe pas</li><li>Créer le répertoire s’il n’existe pas</li><li>Créer des répertoires</li>|

## <a name="home-directory"></a>Répertoire de base

Quand vous configurez des autorisations, vous avez la possibilité de définir un répertoire de base pour l’utilisateur local. Si aucun autre conteneur n’est spécifié dans une demande de connexion SFTP, il s’agit du répertoire auquel l’utilisateur se connecte par défaut. Par exemple, considérez la requête suivante effectuée en utilisant [Open SSH](/windows-server/administration/openssh/openssh_overview). Cette requête ne spécifie pas de nom de conteneur ou de répertoire dans le cadre de la commande `sftp`.

```powershell
sftp myaccount.myusername@myaccount.blob.core.windows.net
put logfile.txt
```

Si vous définissez le répertoire de base d’un utilisateur sur `mycontainer/mydirectory`, le client se connecte alors à ce répertoire. Ensuite, le fichier `logfile.txt` sera chargé dans `mycontainer/mydirectory`. Si vous n’avez pas défini le répertoire de base, la tentative de connexion échoue. Au lieu de cela, les clients de connexion doivent spécifier un conteneur avec la demande, puis utiliser les commandes SFTP pour accéder au répertoire cible avant de charger un fichier. L’exemple suivant illustre cela :

```powershell
sftp myaccount.mycontainer.myusername@myaccount.blob.core.windows.net
cd mydirectory
put logfile.txt  
```

## <a name="known-issues-and-limitations"></a>Problèmes connus et limitations

Consultez l’article [Problèmes connus](secure-file-transfer-protocol-known-issues.md) pour obtenir la liste complète des problèmes et des limites de la version actuelle de la prise en charge de SFTP.

## <a name="regional-availability"></a>Disponibilité régionale

La prise en charge de SFTP est disponible dans les régions suivantes : 

- Centre-Nord des États-Unis
- USA Est 2
- USA Est 2 (EUAP)
- EUAP USA Centre
- Est du Canada
- Centre du Canada
- Europe Ouest
- Europe Nord
- Australie Est
- Suisse Nord
- Allemagne Centre-Ouest
- Asie Est
- France Centre

## <a name="pricing-and-billing"></a>Tarification et facturation

> [!IMPORTANT]
> Pendant la préversion publique, l’utilisation de SFTP n’entraîne pas de frais supplémentaires. Toutefois, les prix standard de transaction, de stockage et de mise en réseau pour le compte Azure Data Lake Store Gen2 sous-jacent s’appliquent toujours. SFTP pourrait entraîner des frais supplémentaires lorsque la fonctionnalité passera en disponibilité générale.  

Les coûts liés aux transactions et au stockage sont basés sur des facteurs tels que le type de compte de stockage et le point de terminaison que vous utilisez pour transférer des données vers le compte de stockage. Pour en savoir plus, consultez [Comprendre le modèle de facturation complet pour Stockage Blob Azure](../common/storage-plan-manage-costs.md#understand-the-full-billing-model-for-azure-blob-storage).

## <a name="see-also"></a>Voir aussi

- [Se connecter à Stockage Blob Azure à l’aide du protocole SFTP (préversion)](secure-file-transfer-protocol-support-how-to.md)
- [Problèmes connus concernant la prise en charge du protocole SFTP dans Stockage Blob Azure (préversion)](secure-file-transfer-protocol-known-issues.md)