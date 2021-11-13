---
title: FAQ sur SMB pour Azure NetApp Files | Microsoft Docs
description: Répond à des questions fréquemment posées sur le protocole SMB d’Azure NetApp Files.
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: conceptual
author: b-juche
ms.author: b-juche
ms.date: 10/11/2021
ms.openlocfilehash: 7b1c6b1e3e9814ed83c92ada2388ff54f5b3829c
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130270810"
---
# <a name="smb-faqs-for-azure-netapp-files"></a>FAQ sur SMB pour Azure NetApp Files

Cet article répond aux questions fréquemment posées sur le protocole SMB d’Azure NetApp Files.

## <a name="which-smb-versions-are-supported-by-azure-netapp-files"></a>Quelles sont les versions de SMB prises en charge par Azure NetApp Files ?

Azure NetApp Files prend en charge les versions SMB 2.1 et SMB 3.1 (qui inclut la prise en charge de SMB 3.0). 

## <a name="is-an-active-directory-connection-required-for-smb-access"></a>Est-ce qu’une connexion Active Directory est requise pour l’accès à SMB ? 

Oui, vous devez créer une connexion Active Directory avant de déployer un volume SMB. Les contrôleurs de domaine spécifiés doivent être accessibles par le sous-réseau délégué d’Azure NetApp Files pour une connexion réussie.  Consultez [Créer un volume SMB](./azure-netapp-files-create-volumes-smb.md) pour plus d’informations. 

## <a name="how-many-active-directory-connections-are-supported"></a>Combien de connexions Active Directory sont prises en charge ?

Vous ne pouvez configurer qu’une seule connexion Active Directory (AD) par abonnement et par région. Pour plus d’informations, consultez [Configuration requise pour les connexions Active Directory](create-active-directory-connections.md#requirements-for-active-directory-connections). 

Toutefois, vous pouvez mapper plusieurs comptes NetApp d’un même abonnement et d’une même région à un serveur AD commun créé dans l’un des comptes NetApp. Consultez [Mapper plusieurs comptes NetApp d’un même abonnement et d’une même région à une connexion AD](create-active-directory-connections.md#shared_ad). 

## <a name="does-azure-netapp-files-support-azure-active-directory"></a>Est-ce qu’Azure NetApp Files prend en charge Azure Active Directory ? 

[Azure Active Directory (AD) Domain Services](../active-directory-domain-services/overview.md) et [Active Directory Domain Services (AD DS)](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) sont tous deux pris en charge. Vous pouvez utiliser des contrôleurs de domaine Active Directory existants avec Azure NetApp Files. Les contrôleurs de domaine peuvent résider dans Azure en tant que machines virtuelles ou en local via ExpressRoute ou S2S VPN. Azure NetApp Files ne prend pas en charge la jonction AD pour [Azure Active Directory](https://azure.microsoft.com/resources/videos/azure-active-directory-overview/) pour l’instant.

Si vous utilisez Azure NetApp Files avec Azure Active Directory Domain Services, le chemin d’accès de l’unité d’organisation est `OU=AADDC Computers` lorsque vous configurez Active Directory pour votre compte NetApp.

## <a name="what-versions-of-windows-server-active-directory-are-supported"></a>Quelles versions de Windows Server Active Directory sont prises en charge ?

Azure NetApp Files prend en charge les versions Windows Server 2008r2SP1-2019 d’Active Directory Domain Services.

## <a name="im-having-issues-connecting-to-my-smb-share-what-should-i-do"></a>Je n’arrive pas à me connecter à mon partage SMB. Que dois-je faire ?

Il est recommandé de définir la tolérance maximale de synchronisation de l’horloge de l’ordinateur sur cinq minutes. Pour plus d’informations, consultez [Tolérance maximale de synchronisation de l’horloge de l’ordinateur](/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/jj852172(v=ws.11)). 

## <a name="can-i-manage-smb-shares-sessions-and-open-files-through-computer-management-console-mmc"></a>Puis-je gérer `SMB Shares`, `Sessions` et `Open Files` via la console de gestion de l’ordinateur (MMC) ?

La gestion de `SMB Shares`, `Sessions` et `Open Files` via la console de gestion de l’ordinateur (MMC) n’est pas prise en charge pour le moment.

## <a name="how-can-i-obtain-the-ip-address-of-an-smb-volume-via-the-portal"></a>Comment puis-je obtenir l’adresse IP d’un volume SMB via le portail ?

Utilisez le lien **Affichage JSON** dans le volet de vue d’ensemble du volume et recherchez l’identificateur **startIp** sous **Propriétés** -> **mountTargets**.

## <a name="can-an-azure-netapp-files-smb-share-act-as-an-dfs-namespace-dfs-n-root"></a>Un partage SMB d’Azure NetApp Files peut-il faire office de racine pour un espace de noms DFS (DFS-N) ?

Non. Toutefois, les partages SMB d’Azure NetApp Files peuvent servir de cible de dossier pour un espace de noms DFS (DFS-N).   
Pour utiliser un partage SMB d’Azure NetApp Files comme cible de dossier DFS-N, indiquez le chemin d’accès de montage UNC (Convention d’affectation des noms) du partage SMB d’Azure NetApp Files à l’aide de la procédure [Ajouter un dossier cible de DFS](/windows-server/storage/dfs-namespaces/add-folder-targets#to-add-a-folder-target).  

## <a name="can-the-smb-share-permissions-be-changed"></a>Les autorisations de partage SMB peuvent-elles être modifiées ?   

Non, les autorisations de partage ne peuvent pas être modifiées. Toutefois, les autorisations NTFS du volume `root` peuvent être modifiées à l’aide de la procédure [Autorisations de fichier et de dossier NTFS](azure-netapp-files-create-volumes-smb.md#ntfs-file-and-folder-permissions). 


## <a name="next-steps"></a>Étapes suivantes  

- [Questions fréquentes (FAQ) sur les performances SMB pour Azure NetApp Files](azure-netapp-files-smb-performance.md)
- [Création d’une demande de support Azure](../azure-portal/supportability/how-to-create-azure-support-request.md)
- [FAQ sur la mise en réseau](faq-networking.md)
- [Forum aux questions sur la sécurité](faq-security.md)
- [Questions fréquentes (FAQ) relatives aux performances](faq-performance.md)
- [FAQ relatifs au NFS](faq-nfs.md)
- [Questions fréquentes (FAQ) sur la gestion de la capacité](faq-capacity-management.md)
- [Questions fréquentes sur la migration et la protection des données](faq-data-migration-protection.md)
- [FAQ sur la sauvegarde Azure NetApp Files](faq-backup.md)
- [FAQ relatives à la résilience des applications](faq-application-resilience.md)
- [Forum aux questions sur l’intégration](faq-integration.md)
