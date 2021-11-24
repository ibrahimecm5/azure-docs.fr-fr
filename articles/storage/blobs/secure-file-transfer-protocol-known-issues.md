---
title: Problèmes connus avec SFTP dans le Stockage Blob Azure (préversion) | Microsoft Docs
description: Découvrez les limitations et les problèmes connus de la prise en charge du protocole SFTP (Secure File Transfer Protocol) dans le Stockage Blob Azure.
author: normesta
ms.subservice: blobs
ms.service: storage
ms.topic: conceptual
ms.date: 11/15/2021
ms.author: normesta
ms.reviewer: ylunagaria
ms.openlocfilehash: c9c7a1395006aa7613aa40ff9ea7b402f69bbf5d
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132557434"
---
# <a name="known-issues-with-secure-file-transfer-protocol-sftp-support-in-azure-blob-storage-preview"></a>Problèmes connus liés à la prise en charge de la prise en charge du protocole SFTP (Secure File Transfer Protocol) dans le Stockage Blob Azure (préversion)

Cet article décrit les limitations et les problèmes connus de la prise en charge de SFTP dans le stockage Blob Azure.

> [!IMPORTANT]
> La prise en charge du SFTP est actuellement en préversion et est disponible dans [ces régions](secure-file-transfer-protocol-support.md#regional-availability).
> 
> Pour connaître les conditions juridiques qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou plus généralement non encore en disponibilité générale, consultez [l’Avenant aux conditions d’utilisation des préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
> Pour vous inscrire à la préversion, voir [ce formulaire](https://forms.office.com/r/gZguN0j65Y).

## <a name="data-redundancy-options"></a>Options de redondance de données

- Le stockage géo-redondant (GRS) et le stockage géoredondant dans une zone géographique (GZRS) ne sont pas pris en charge dans les comptes pour lesquels SFTP est activé.

## <a name="authorization"></a>Autorisation

- Les utilisateurs locaux sont la seule forme de gestion des identités qui est actuellement prise en charge pour le point de terminaison SFTP.

- Azure Active Directory (Azure AD), la signature d’accès partagé (sap) et l’autorisation de clé de compte ne sont pas prises en charge pour le point de terminaison SFTP.

- Les listes de contrôle d’accès de type POSIX (ACL) ne sont pas prises en charge pour le point de terminaison SFTP.

  > [!NOTE]
  > Une fois que vos données sont ingérées dans le stockage Azure, vous pouvez utiliser la totalité des paramètres de sécurité du stockage Azure. Bien que les mécanismes d’autorisation tels que le contrôle d’accès en fonction du rôle (RBAC) et les listes de contrôle d’accès ne soient pas pris en charge pour autoriser un client SFTP de connexion, ils peuvent être utilisés pour autoriser l’accès via les outils Azure (tels que le portail Azure, Azure CLI, les commandes Azure PowerShell et AzCopy), ainsi que les kits de développement logiciel Azure et les API REST Azure. 

- Les opérations au niveau du compte telles que la liste, la mise en production, la création et la suppression de conteneurs ne sont pas prises en charge.
 
## <a name="networking"></a>Mise en réseau

- Les points de terminaison DNS partitionnés ne sont pas pris en charge.

- Pour accéder au compte de stockage par SFTP, votre réseau doit autoriser le trafic sur le port 22.

- Lorsqu’un pare-feu est configuré, les connexions à partir d’adresses IP non autorisées ne sont pas rejetées comme prévu. Toutefois, si une connexion est établie pour un utilisateur authentifié, toutes les opérations du plan de données sont rejetées.

## <a name="supported-algorithms"></a>Algorithmes pris en charge

| Clé hôte | Échange de clés | Chiffrements/chiffrement | Intégrité/MAC | Clé publique |
|----------|--------------|--------------------|---------------|------------|
| rsa-sha2-256 | ecdh-sha2-nistp384 | aes128-gcm@openssh.com | hmac-sha2-256 | ssh-rsa |
| rsa-sha2-512 | ecdh-sha2-nistp256 | aes256-gcm@openssh.com | hmac-sha2-512 | ecdsa-sha2-nistp256 |
| ecdsa-sha2-nistp256 | diffie-hellman-group14-sha256 | aes128-cbc| | ecdsa-sha2-nistp384 |
| ecdsa-sha2-nistp384| diffie-hellman-group16-sha512 | aes256-cbc |  | 
||| aes192-cbc ||

## <a name="security"></a>Sécurité

- Les clés d’hôte sont publiées [ici](secure-file-transfer-protocol-host-keys.md). Pendant la préversion publique, les clés d’hôte changent jusqu’à une fois par mois.

- Il existe plusieurs raisons engendrant un avertissement « l’identification de l’hôte distant a changé » :

  - La clé d’hôte distant a été mise à jour (les clés d’hôte changent régulièrement).
  
  - Le client a sélectionné un algorithme de clé d’hôte différent de celui stocké dans le fichier « known_hosts » SSH local. OpenSSH utilise une clé déjà approuvée si l’hôte (account.blob.core.windows.net) correspond, même si l’algorithme ne correspond pas nécessairement.
  
  - Le compte de stockage a basculé vers une autre région.
  
  - L’hôte distant (account.blob.core.windows.net) est falsifié.

## <a name="integrations"></a>Intégrations

- Le flux de modification n’est pas pris en charge.

- Les métriques de compte telles que les transactions et la capacité sont disponibles. Filtrez les journaux par opérations pour voir l’activité SFTP.

- Le système de fichiers réseau (NFS) 3.0 et SFTP ne peuvent pas être activés sur le même compte de stockage.

## <a name="performance"></a>Performances

- Les performances de chargement avec des paramètres par défaut pour certains clients peuvent être lentes. Cela est attendu, car SFTP est un protocole bavard et envoie des demandes de message de petite taille. L’augmentation de la taille de la mémoire tampon et l’utilisation de plusieurs connexions simultanées peuvent améliorer considérablement la vitesse. 

  - Pour WinSCP, vous pouvez utiliser un maximum de 9 connexions simultanées pour télécharger plusieurs fichiers. 

  - pour OpenSSH sur Windows, vous pouvez augmenter la taille de la mémoire tampon à 100000 : sftp-B 100000testaccount.user1@testaccount.blob.core.windows.net 

  - pour OpenSSH sur Linux, vous pouvez augmenter la taille de la mémoire tampon à 262000 : sftp -B 262000 -R 32 testaccount.user1@testaccount.blob.core.windows.net 

- Il y a un délai de 4 minutes pour les connexions en veille ou inactives. OpenSSH semble se bloquer et se déconnecter. Certains clients se reconnectent automatiquement. 

- La taille maximale du fichier chargé est limitée par la taille de message client. En voici quelques exemples : 

  - message de 32 Ko (valeur par défaut OpenSSH) * 50 000 blocs = 1,52 Go 

  - message de 100 Ko (OpenSSH Windows max) * 50 000 blocs = 4,77 Go 

  - message de 256 Ko (OpenSSH Linux max) * 50 000 blocs = 12,20 Go 

## <a name="other"></a>Autres

- Les liens symboliques ne sont pas pris en charge.

- PowerShell et Azure CLI ne sont pas pris en charge. Vous pouvez tirer parti du portail et des modèles ARM pour la préversion publique.

## <a name="see-also"></a>Voir aussi

- [Prise en charge du protocole SFTP (Secure File Transfer) dans le stockage Blob Azure](secure-file-transfer-protocol-support.md)