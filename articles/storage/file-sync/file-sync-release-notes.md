---
title: Notes de publication de l’agent Azure File Sync | Microsoft Docs
description: Lisez les notes de publication de l’agent Azure File Sync, qui vous permet de centraliser les partages de fichiers de votre organisation dans Azure Files.
services: storage
author: wmgries
ms.service: storage
ms.topic: conceptual
ms.date: 11/9/2021
ms.author: wgries
ms.subservice: files
ms.openlocfilehash: 19e1d5f8eab559114f26d10a15c835cf0758b225
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132133229"
---
# <a name="release-notes-for-the-azure-file-sync-agent"></a>Notes de publication de l’agent Azure File Sync
Azure File Sync vous permet de centraliser les partages de fichiers de votre organisation dans Azure Files sans perdre la flexibilité, le niveau de performance et la compatibilité d’un serveur de fichiers local. Il transforme vos installations Windows Server en un cache rapide de votre partage de fichiers Azure. Vous pouvez utiliser tout protocole disponible dans Windows Server pour accéder à vos données localement (notamment SMB, NFS et FTPS). Vous pouvez avoir autant de caches que nécessaire dans le monde entier.

Cet article fournit les notes de publication concernant les versions prises en charge de l’agent Azure File Sync.

## <a name="supported-versions"></a>Versions prises en charge
Les versions prises en charge de l'agent Azure File Sync sont les suivantes :

| Jalon | Numéro de version de l’agent | Date de publication | Statut |
|----|----------------------|--------------|------------------|
| Version V14 - [KB5001872](https://support.microsoft.com/topic/92290aa1-75de-400f-9442-499c44c92a81)| 14.0.0.0 | 29 octobre 2021 | Prise en charge : Distribution de version d’évaluation |
| Version V13 - [KB4588753](https://support.microsoft.com/topic/632fb833-42ed-4e4d-8abd-746bd01c1064)| 13.0.0.0 | 12 juillet 2021 | Prise en charge |
| Version v12.1 – [KB4588751](https://support.microsoft.com/topic/497dc33c-d38b-42ca-8015-01c906b96132)| 12.1.0.0 | 20 mai 2021 | Prise en charge |
| Mise en production V12 – [KB4568585](https://support.microsoft.com/topic/b9605f04-b4af-4ad8-86b0-2c490c535cfd)| 12.0.0.0 | 26 mars 2021 | Prise en charge |
| Version V11.3 : [KB4539953](https://support.microsoft.com/topic/f68974f6-bfdd-44f4-9659-bf2d8a696c26)| 11.3.0.0 | 7 avril 2021 | Prise en charge |
| Version V11.2 – [KB4539952](https://support.microsoft.com/topic/azure-file-sync-agent-v11-2-release-february-2021-c956eaf0-cd8e-4511-98c0-e5a1f2c84048)| 11.2.0.0 | 2 février 2021 | Prise en charge |
| Mise en production V 11.1 – [KB4539951](https://support.microsoft.com/help/4539951)| 11.1.0.0 | 4 novembre 2020 | Prise en charge |

## <a name="unsupported-versions"></a>Versions non prises en charge
Les versions suivantes de l'agent Azure File Sync ont expiré et ne sont plus prises en charge :

| Jalon | Numéro de version de l’agent | Date de publication | Statut |
|----|----------------------|--------------|------------------|
| Version V10 | 10.0.0.0 - 10.1.0.0 | N/A | Non prise en charge : les versions de l’agent ont expiré le 28 juin 2021. |
| Version V9 | 9.0.0.0 - 9.1.0.0 | N/A | Non prise en charge : les versions de l’agent ont expiré le 16 février 2021. |
| Version 8 | 8.0.0.0 | N/A | Non prise en charge : les versions de l’agent ont expiré le 12 janvier 2021. |
| Version V7 | 7.0.0.0 - 7.2.0.0 | N/A | Non pris en charge - Les versions de l'agent ont expiré le 1er septembre 2020 |
| Version V6 | 6.0.0.0 - 6.3.0.0 | N/A | Non pris en charge : les versions de l’agent ont expiré le 21 avril 2020 |
| Version V5 | 5.0.2.0 à 5.2.0.0 | N/A | Non pris en charge : les versions de l’agent ont expiré le 18 mars 2020 |
| Version V4 | 4.0.1.0 - 4.3.0.0 | N/A | Non pris en charge : les versions de l’agent ont expiré le 6 novembre 2019 |
| mise en production V3 | 3.1.0.0 - 3.4.0.0 | N/A | Non pris en charge : les versions de l’agent ont expiré le 19 août 2019 |
| Agents antérieurs à la disponibilité générale | 1.1.0.0 - 3.0.13.0 | N/A | Non pris en charge : les versions de l’agent ont expiré le 1er octobre 2018 |

### <a name="azure-file-sync-agent-update-policy"></a>Stratégie de mise à jour de l’agent Azure File Sync
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="agent-version-14000"></a>Version de l’agent 14.0.0.0
Les notes de publication suivantes concernent la version 14.0.0.0 de l’agent Azure File Sync (mise en production le 29 octobre 2021).

### <a name="improvements-and-issues-that-are-fixed"></a>Améliorations et problèmes résolus
- Transactions réduites lors des exécutions de travail d’énumération des modifications cloud 
    - Azure File Sync inclut un travail d’énumération des modifications cloud qui s’exécute toutes les 24 heures pour détecter les modifications apportées directement au partage de fichiers Azure et synchroniser ces modifications sur les serveurs dans vos groupes de synchronisation. Nous avons apporté des améliorations pour réduire le nombre de transactions lors de l’exécution de ce travail.

- Amélioration des conseils de mise en service des points de terminaison de serveur dans le portail
    - Lors de la suppression d’un point de terminaison de serveur via le portail, nous fournissons désormais des instructions pas à pas basées sur la raison de la suppression du point de terminaison de serveur, afin que vous puissiez éviter la perte de données et vous assurer que vos données sont là où elles doivent être (serveur ou partage de fichiers Azure). Cette fonctionnalité comprend également de nouvelles cmdlets PowerShell (Get-StorageSyncStatus et New-StorageSyncUploadSession) que vous pouvez utiliser sur votre serveur local pour vous aider dans le processus d’annulation de l’approvisionnement.

- Améliorations du cmdlet Invoke-AzStorageSyncChangeDetection
    - Avant la version v14, si vous avez apporté des modifications directement dans le partage de fichiers Azure, vous pouviez utiliser le cmdlet Invoke-AzStorageSyncChangeDetection pour détecter les modifications et les synchroniser avec les serveurs de votre groupe de synchronisation. Toutefois, l’exécution du cmdlet échoue si le chemin d’accès spécifié contient plus de 10 000 éléments. Nous avons amélioré le cmdlet Invoke-AzStorageSyncChangeDetection et la limite de 10 000 éléments ne s’applique plus lors de l’analyse de l’ensemble du partage. Pour plus d’informations, consultez la documentation [Invoke-AzStorageSyncChangeDetection](https://docs.microsoft.com/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection).

- Améliorations diverses
    - Azure File Sync est désormais pris en charge dans la région USA Ouest 3.
    - Correction d’un bogue qui avait pour effet que le script FileSyncErrorsReport.ps1 ne fournissait pas la liste de toutes les erreurs par élément.
    - Transactions réduites lorsqu’un fichier ne parvient jamais à se charger en raison d’une erreur de synchronisation par élément.
    - Améliorations diverses de la fiabilité et de la télémétrie de la hiérarchisation cloud et de la synchronisation. 

### <a name="evaluation-tool"></a>Outil d’évaluation
Avant de déployer l’agent Azure File Sync, vous devez évaluer s’il est compatible avec votre système à l’aide de l’outil d’évaluation Azure File Sync. Cet outil est une applet de commande Azure PowerShell qui recherche les problèmes potentiels liés à votre système de fichiers et à votre jeu de données, comme des caractères non pris en charge ou une version de système d’exploitation non prise en charge. Pour des instructions d’installation et d’utilisation, voir la section [Outil d’évaluation](file-sync-planning.md#evaluation-cmdlet) du guide de planification. 

### <a name="agent-installation-and-server-configuration"></a>Installation de l’agent et configuration du serveur
Pour plus d’informations sur l’installation et la configuration de l’agent Azure File Sync avec Windows Server, consultez [Planification d’un déploiement Azure File Sync](file-sync-planning.md) et [Guide pratique pour déployer Azure File Sync](file-sync-deployment-guide.md).

- Un redémarrage est nécessaire pour les serveurs sur lesquels la version de l’agent Azure File Sync actuellement installé est antérieure à la version 12.0.
- Le package d’installation de l’agent doit être installé avec des autorisations (administrateur) élevées.
- L’agent n’est pas pris en charge par l’option de déploiement de Nano Server.
- L'agent est pris en charge uniquement sur Windows Server 2019, Windows Server 2016, Windows Server 2012 R2 et Windows Server 2022.
- 2 Gio de mémoire nécessaires pour l’agent. Si le serveur s’exécute sur une machine virtuelle où la mémoire dynamique est activée, la machine virtuelle doit être configurée avec un minimum de 2 048 Mio de mémoire. Pour plus d'informations, consultez [Ressources système recommandées](file-sync-planning.md#recommended-system-resources).
- Le service Storage Sync Agent (FileSyncSvc) ne prend pas en charge les points de terminaison serveur situés sur un volume dont le répertoire d’informations de volume système (SVI) est compressé. Cette configuration produit des résultats inattendus.

### <a name="interoperability"></a>Interopérabilité
- Les antivirus, applications de sauvegarde et autres applications qui ont accès à des fichiers hiérarchisés peuvent provoquer des rappels indésirables, sauf s’ils respectent l’attribut hors connexion et ignorent la lecture du contenu de ces fichiers. Pour plus d’informations, consultez [Résoudre les problèmes liés à Azure File Sync](file-sync-troubleshoot.md).
- Les filtres de fichiers FSRM peuvent entraîner des échecs de synchronisation sans fin lorsque les fichiers sont bloqués en raison du filtre de fichier.
- L’exécution de sysprep sur un serveur sur lequel l’agent Azure File Sync est installé n’est pas prise en charge et peut produire des résultats inattendus. L’agent Azure File Sync doit être installé après avoir déployé l’image du serveur et terminé la mini-configuration de sysprep.

### <a name="sync-limitations"></a>Limitations de synchronisation
Les éléments suivants ne se synchronisent pas, mais le reste du système continue d’opérer normalement :
- Fichiers avec caractères non pris en charge. Reportez-vous au [Guide de résolution des problèmes](file-sync-troubleshoot.md#handling-unsupported-characters) pour consulter la liste des caractères non pris en charge.
- Fichiers ou répertoires se terminant par un point.
- Chemins de plus de 2 048 caractères.
- La partie liste de contrôle d’accès système (SACL) d’un descripteur de sécurité qui est utilisée pour l’audit.
- Attributs étendus.
- Autres flux de données.
- Points d’analyse.
- Liens physiques.
- Si définie sur un serveur de fichiers, la compression n’est pas conservée lorsque les changements se synchronisent avec ce fichier depuis d’autres points de terminaison.
- Tous les fichiers chiffrés avec EFS (ou tout autre chiffrement de mode utilisateur) qui empêche le service de lire les données.

    > [!Note]  
    > Azure File Sync chiffre toujours les données en transit. Les données sont toujours chiffrées au repos dans Azure.
 
### <a name="server-endpoint"></a>Point de terminaison de serveur
- Un point de terminaison de serveur ne peut être créé que sur un volume NTFS. ReFS, FAT, FAT32 et d’autres systèmes de fichiers ne sont actuellement pas pris en charge par Azure File Sync.
- La hiérarchisation cloud n’est pas prise en charge sur le volume système. Pour créer un point de terminaison de serveur sur le volume système, désactivez la hiérarchisation cloud quand vous créez le point de terminaison de serveur.
- Le clustering de basculement est pris en charge uniquement avec les disques en cluster, pas avec les volumes partagés de cluster (CSV).
- Un point de terminaison de serveur ne peut pas être imbriqué. Il peut coexister sur le même volume en parallèle avec un autre point de terminaison.
- Ne stockez pas de système d’exploitation ni de fichier de pagination d’application au sein d’un emplacement de point de terminaison de serveur.

### <a name="cloud-endpoint"></a>Point de terminaison cloud
- Azure File Sync prend en charge les modifications directes dans le partage de fichiers Azure. Toutefois, les modifications apportées au partage de fichiers Azure doivent d’abord être détectées par un travail de détection des modifications Azure File Sync. Un travail de détection des modifications est lancé pour un point de terminaison cloud une fois toutes les 24 heures. Pour synchroniser immédiatement les fichiers qui ont été modifiés dans le partage de fichiers Azure, le cmdlet PowerShell [Invoke-AzStorageSyncChangeDetection](/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) permet de lancer manuellement la détection des modifications apportées au partage de fichiers Azure. Par ailleurs, les modifications apportées à un partage de fichiers Azure via le protocole REST ne mettent pas à jour l’heure de dernière modification de SMB et ne sont pas visibles comme des modifications par la synchronisation.
- Le service de synchronisation de stockage et/ou le compte de stockage peuvent être déplacés vers un autre groupe de ressources, un autre abonnement ou un autre locataire Azure AD. Une fois le service de synchronisation de stockage ou le compte de stockage déplacé, vous devez donner à l’application Microsoft.StorageSync l’accès au compte de stockage (consultez [Vérifiez qu’Azure File Sync a accès au compte de stockage](file-sync-troubleshoot.md?tabs=portal1%252cportal#troubleshoot-rbac)).

    > [!Note]  
    > Lors de la création du point de terminaison cloud, le service de synchronisation du stockage et le compte de stockage doivent se trouver dans le même locataire Azure AD. Une fois le point de terminaison cloud créé, le service de synchronisation du stockage et le compte de stockage peuvent être déplacés vers des locataires Azure AD différents.

### <a name="cloud-tiering"></a>Hiérarchisation cloud
- Si un fichier hiérarchisé est copié vers un autre emplacement à l’aide de Robocopy, le fichier résultant n’est pas hiérarchisé. L’attribut hors connexion peut être défini car Robocopy inclut cet attribut de façon erronée dans les opérations de copie.
- Lors de la copie de fichiers à l’aide de Robocopy, utilisez l’option /MIR pour conserver les horodatages des fichiers. Les plus anciens fichiers sont ainsi hiérarchisés plus tôt que les derniers fichiers utilisés.

## <a name="agent-version-13000"></a>Version de l’agent 13.0.0.0
Les notes de publication suivantes concernent la version 13.0.0.0 de l’agent Azure File Sync (publiée le 12 juillet 2021).

### <a name="improvements-and-issues-that-are-fixed"></a>Améliorations et problèmes résolus
- Chargement faisant autorité  
    - Le chargement faisant autorité est un nouveau mode disponible lors de la création du premier point de terminaison de serveur dans un groupe de synchronisation. Il est utile dans les situations où le cloud (partage de fichiers Azure) a une partie ou la plupart des données. Il est donc obsolète et doit récupérer les données les plus récentes sur le nouveau point de terminaison de serveur pour rattraper son décalage. Ce scénario peut se produire lors de migrations hors connexion, avec Data Box par exemple. Quand une Data Box est pleine et envoyée à Azure, les utilisateurs du serveur local continuent à modifier/ajouter/supprimer des fichiers sur le serveur local. Cela rend les données dans la Data Box et, par conséquent, le partage de fichiers Azure, légèrement obsolètes. Avec le chargement faisant autorité, vous pouvez maintenant indiquer au serveur et au cloud comment résoudre ce problème et mettre à jour le cloud de manière fluide avec les dernières modifications sur le serveur. 

        Quelle que soit la manière dont les données se retrouvent dans le cloud, ce mode peut mettre à jour le partage de fichiers Azure si les données proviennent de l’emplacement correspondant sur le serveur. Veillez à éviter les grandes restructurations de répertoires entre la copie initiale dans le cloud et le rattrapage avec le chargement faisant autorité. De cette façon, seules les mises à jour seront transportées. Le renommage de répertoires entraîne le rechargement de tous les fichiers contenus dans ces répertoires renommés. Cette fonctionnalité est comparable à la sémantique de RoboCopy /MIR (mise en miroir de la source sur la cible), y compris la suppression de fichiers sur la cible qui n’existent plus sur la source. 
        
        Le chargement faisant autorité remplace la fonctionnalité « Transfert de données hors connexion » pour l’intégration de Data Box à Azure File Sync par le biais d’un partage intermédiaire. Un partage intermédiaire n’est plus nécessaire pour utiliser Data Box. Les nouvelles tâches de transfert de données hors connexion ne peuvent plus être démarrées avec l’agent AFS V13. Les tâches existantes sur un serveur se poursuivent même avec la mise à niveau de l’agent vers la version 13.

- Améliorations du portail pour afficher l’énumération des modifications cloud et la progression de la synchronisation  
    - Quand un groupe de synchronisation est créé, tout point de terminaison de serveur connecté ne peut commencer la synchronisation qu’une fois l’énumération des modifications cloud terminée.  Si des fichiers existent déjà dans le point de terminaison cloud (partage de fichiers Azure) de ce groupe de synchronisation, l’énumération des modifications du contenu dans le cloud peut prendre du temps. Plus il y a d’éléments (fichiers et dossiers) dans l’espace de noms, plus ce processus peut prendre du temps. Les administrateurs peuvent désormais accéder à la progression de l’énumération des modifications cloud dans le portail Azure pour estimer un ETA pour l’achèvement et le début de la synchronisation avec les serveurs.

- Prise en charge du renommage du serveur  
    - Si un serveur inscrit est renommé, Azure File Sync indique désormais le nouveau nom du serveur dans le portail. Si le serveur a été renommé avant la version v13, le nom du serveur dans le portail est désormais le bon.

- Prise en charge pour Windows Server 2022  
    - L'agent Azure File Sync est désormais pris en charge sur Windows Server 2022.

    > [!Note]  
    > Windows Server 2022 prend en charge TLS 1.3, qui n’est actuellement pas pris en charge par Azure File Sync. Si les [paramètres TLS](/windows-server/security/tls/tls-ssl-schannel-ssp-overview) sont gérés par le biais de la stratégie de groupe, le serveur doit être configuré pour prendre en charge TLS 1.2. 

- Améliorations diverses
    - Améliorations de la fiabilité en matière de synchronisation, de hiérarchisation cloud et d’énumération des modifications cloud.
    - En cas de modification d’un grand nombre de fichiers sur le serveur, le chargement de la synchronisation est maintenant effectué à partir d’un instantané VSS, ce qui réduit les erreurs par élément et les échecs de session de synchronisation. 
    - L’applet de commande Invoke-StorageSyncFileRecall rappelle désormais tous les fichiers hiérarchisés associés à un point de terminaison de serveur, même si le fichier a été déplacé en dehors de l’emplacement du point de terminaison de serveur. 
    - Explorer.exe est désormais exclu du suivi du dernier accès de la hiérarchisation cloud.
    - Nouvelle télémétrie (ID d’événement 6664) pour superviser la progression du nettoyage des fichiers hiérarchisés orphelins après la suppression d’un point de terminaison de serveur avec activation de la hiérarchisation cloud.


### <a name="evaluation-tool"></a>Outil d’évaluation
Avant de déployer l’agent Azure File Sync, vous devez évaluer s’il est compatible avec votre système à l’aide de l’outil d’évaluation Azure File Sync. Cet outil est une applet de commande Azure PowerShell qui recherche les problèmes potentiels liés à votre système de fichiers et à votre jeu de données, comme des caractères non pris en charge ou une version de système d’exploitation non prise en charge. Pour des instructions d’installation et d’utilisation, voir la section [Outil d’évaluation](file-sync-planning.md#evaluation-cmdlet) du guide de planification. 

### <a name="agent-installation-and-server-configuration"></a>Installation de l’agent et configuration du serveur
Pour plus d’informations sur l’installation et la configuration de l’agent Azure File Sync avec Windows Server, consultez [Planification d’un déploiement Azure File Sync](file-sync-planning.md) et [Guide pratique pour déployer Azure File Sync](file-sync-deployment-guide.md).

- Un redémarrage est nécessaire pour les serveurs sur lesquels la version de l’agent Azure File Sync actuellement installé est antérieure à la version 12.0.
- Le package d’installation de l’agent doit être installé avec des autorisations (administrateur) élevées.
- L’agent n’est pas pris en charge par l’option de déploiement de Nano Server.
- L'agent est pris en charge uniquement sur Windows Server 2019, Windows Server 2016, Windows Server 2012 R2 et Windows Server 2022.
- 2 Gio de mémoire nécessaires pour l’agent. Si le serveur s’exécute sur une machine virtuelle où la mémoire dynamique est activée, la machine virtuelle doit être configurée avec un minimum de 2 048 Mio de mémoire. Pour plus d'informations, consultez [Ressources système recommandées](file-sync-planning.md#recommended-system-resources).
- Le service Storage Sync Agent (FileSyncSvc) ne prend pas en charge les points de terminaison serveur situés sur un volume dont le répertoire d’informations de volume système (SVI) est compressé. Cette configuration produit des résultats inattendus.

### <a name="interoperability"></a>Interopérabilité
- Les antivirus, applications de sauvegarde et autres applications qui ont accès à des fichiers hiérarchisés peuvent provoquer des rappels indésirables, sauf s’ils respectent l’attribut hors connexion et ignorent la lecture du contenu de ces fichiers. Pour plus d’informations, consultez [Résoudre les problèmes liés à Azure File Sync](file-sync-troubleshoot.md).
- Les filtres de fichiers FSRM peuvent entraîner des échecs de synchronisation sans fin lorsque les fichiers sont bloqués en raison du filtre de fichier.
- L’exécution de sysprep sur un serveur sur lequel l’agent Azure File Sync est installé n’est pas prise en charge et peut produire des résultats inattendus. L’agent Azure File Sync doit être installé après avoir déployé l’image du serveur et terminé la mini-configuration de sysprep.

### <a name="sync-limitations"></a>Limitations de synchronisation
Les éléments suivants ne se synchronisent pas, mais le reste du système continue d’opérer normalement :
- Fichiers avec caractères non pris en charge. Reportez-vous au [Guide de résolution des problèmes](file-sync-troubleshoot.md#handling-unsupported-characters) pour consulter la liste des caractères non pris en charge.
- Fichiers ou répertoires se terminant par un point.
- Chemins de plus de 2 048 caractères.
- La partie liste de contrôle d’accès système (SACL) d’un descripteur de sécurité qui est utilisée pour l’audit.
- Attributs étendus.
- Autres flux de données.
- Points d’analyse.
- Liens physiques.
- Si définie sur un serveur de fichiers, la compression n’est pas conservée lorsque les changements se synchronisent avec ce fichier depuis d’autres points de terminaison.
- Tous les fichiers chiffrés avec EFS (ou tout autre chiffrement de mode utilisateur) qui empêche le service de lire les données.

    > [!Note]  
    > Azure File Sync chiffre toujours les données en transit. Les données sont toujours chiffrées au repos dans Azure.
 
### <a name="server-endpoint"></a>Point de terminaison de serveur
- Un point de terminaison de serveur ne peut être créé que sur un volume NTFS. ReFS, FAT, FAT32 et d’autres systèmes de fichiers ne sont actuellement pas pris en charge par Azure File Sync.
- La hiérarchisation cloud n’est pas prise en charge sur le volume système. Pour créer un point de terminaison de serveur sur le volume système, désactivez la hiérarchisation cloud quand vous créez le point de terminaison de serveur.
- Le clustering de basculement est pris en charge uniquement avec les disques en cluster, pas avec les volumes partagés de cluster (CSV).
- Un point de terminaison de serveur ne peut pas être imbriqué. Il peut coexister sur le même volume en parallèle avec un autre point de terminaison.
- Ne stockez pas de système d’exploitation ni de fichier de pagination d’application au sein d’un emplacement de point de terminaison de serveur.

### <a name="cloud-endpoint"></a>Point de terminaison cloud
- Azure File Sync prend en charge les modifications directes dans le partage de fichiers Azure. Toutefois, les modifications apportées au partage de fichiers Azure doivent d’abord être détectées par un travail de détection des modifications Azure File Sync. Un travail de détection des modifications est lancé pour un point de terminaison cloud une fois toutes les 24 heures. Pour synchroniser immédiatement les fichiers qui ont été modifiés dans le partage de fichiers Azure, le cmdlet PowerShell [Invoke-AzStorageSyncChangeDetection](/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) permet de lancer manuellement la détection des modifications apportées au partage de fichiers Azure. Par ailleurs, les modifications apportées à un partage de fichiers Azure via le protocole REST ne mettent pas à jour l’heure de dernière modification de SMB et ne sont pas visibles comme des modifications par la synchronisation.
- Le service de synchronisation de stockage et/ou le compte de stockage peuvent être déplacés vers un autre groupe de ressources, un autre abonnement ou un autre locataire Azure AD. Une fois le service de synchronisation de stockage ou le compte de stockage déplacé, vous devez donner à l’application Microsoft.StorageSync l’accès au compte de stockage (consultez [Vérifiez qu’Azure File Sync a accès au compte de stockage](file-sync-troubleshoot.md?tabs=portal1%252cportal#troubleshoot-rbac)).

    > [!Note]  
    > Lors de la création du point de terminaison cloud, le service de synchronisation du stockage et le compte de stockage doivent se trouver dans le même locataire Azure AD. Une fois le point de terminaison cloud créé, le service de synchronisation du stockage et le compte de stockage peuvent être déplacés vers des locataires Azure AD différents.

### <a name="cloud-tiering"></a>Hiérarchisation cloud
- Si un fichier hiérarchisé est copié vers un autre emplacement à l’aide de Robocopy, le fichier résultant n’est pas hiérarchisé. L’attribut hors connexion peut être défini car Robocopy inclut cet attribut de façon erronée dans les opérations de copie.
- Lors de la copie de fichiers à l’aide de Robocopy, utilisez l’option /MIR pour conserver les horodatages des fichiers. Les plus anciens fichiers sont ainsi hiérarchisés plus tôt que les derniers fichiers utilisés.

## <a name="agent-version-12100"></a>Version de l’agent 12.1.0.0
Les notes de publication suivantes concernent la version 12.1.0.0 de l’agent Azure File Sync publiée le 20 mai 2021. Ces notes s’ajoutent aux notes de publication de la version 12.0.0.0.

### <a name="improvements-and-issues-that-are-fixed"></a>Améliorations et problèmes résolus 
La version de l’agent v12.0 comportait deux bogues qui ont été résolus dans cette version :
- La mise à jour automatique de l’agent ne peut pas à mettre à jour l’agent vers une version ultérieure.
- Le script FileSyncErrorsReport.ps1 ne fournit pas la liste des erreurs par élément.

## <a name="agent-version-12000"></a>Version de l’agent 12.0.0.0
Les notes de publication suivantes concernent la version 12.0.0.0 de l’agent Azure File Sync publiée le 26 mars 2021.

### <a name="improvements-and-issues-that-are-fixed"></a>Améliorations et problèmes résolus
- Nouvelle expérience de portail pour configurer une stratégie d’accès réseau et des connexions de point de terminaison privé
    - Vous pouvez désormais utiliser le portail pour désactiver l’accès au point de terminaison public du service de synchronisation du stockage, et approuver, rejeter et supprimer des connexions de point de terminaison privé. Pour configurer la stratégie d’accès réseau et les connexions de point de terminaison privé, ouvrez le portail du service de synchronisation du stockage, accédez à la section Paramètres, puis cliquez sur Réseau.
 
- Prise en charge de la hiérarchisation cloud pour les tailles de cluster de volumes supérieures à 64 Kio
    - La hiérarchisation cloud prend désormais en charge les tailles de cluster de volume jusqu’à 2 Mio sur le serveur 2019. Pour en savoir plus, consultez [Quelle est la taille minimale d’un fichier à hiérarchiser ?](./file-sync-choose-cloud-tiering-policies.md#minimum-file-size-for-a-file-to-tier).
 
- Mesurer la bande passante et la latence pour le service Azure File Sync et le compte de stockage
    - Vous pouvez désormais utiliser la cmdlet Test-StorageSyncNetworkConnectivity pour mesurer la latence et la bande passante pour le service Azure File Sync et le compte de stockage. La latence pour le service Azure File Sync et le compte de stockage est mesurée par défaut lors de l’exécution de la cmdlet.  La bande passante de chargement et de téléchargement pour le compte de stockage est mesurés lors de l’utilisation du paramètre « -MeasureBandwidth ».
 
        Par exemple, pour mesurer la bande passante et la latence pour le service Azure File Sync et le compte de stockage, exécutez les commandes PowerShell suivantes :
 
        ```powershell
        Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
        Test-StorageSyncNetworkConnectivity -MeasureBandwidth 
        ``` 
 
- Amélioration des messages d’erreur dans le portail en cas d’échec de création de point de terminaison de serveur
    - Suite à vos commentaires, nous avons amélioré les messages d’erreur et les conseils qui s’affichent en cas d’échec de création de point de terminaison de serveur.
 
- Améliorations diverses au niveau des performances et de la fiabilité
    - Amélioration des performances de détection des modifications pour détecter les fichiers modifiés dans le partage de fichiers Azure.
    - Améliorations des performances des sessions de synchronisation de réconciliation. 
    - Améliorations de la synchronisation pour réduire les erreurs ECS_E_SYNC_METADATA_KNOWLEDGE_SOFT_LIMIT_REACHED et ECS_E_SYNC_METADATA_KNOWLEDGE_LIMIT_REACHED.
    - Correction d’un bogue qui entraîne une altération des données si la hiérarchisation cloud est activée et que les fichiers hiérarchisés sont copiés à l’aide de Robocopy avec le paramètre /B.
    - Correction d’un bogue pouvant entraîner l’échec de la hiérarchisation des fichiers sur le serveur 2019 si la déduplication des données est activée sur le volume.
    - Correction d’un bogue pouvant entraîner l’échec de la compression des fichiers par AFSDiag quand un fichier est d’une taille supérieure à 2Gio.

### <a name="evaluation-tool"></a>Outil d’évaluation
Avant de déployer l’agent Azure File Sync, vous devez évaluer s’il est compatible avec votre système à l’aide de l’outil d’évaluation Azure File Sync. Cet outil est une applet de commande Azure PowerShell qui recherche les problèmes potentiels liés à votre système de fichiers et à votre jeu de données, comme des caractères non pris en charge ou une version de système d’exploitation non prise en charge. Pour des instructions d’installation et d’utilisation, voir la section [Outil d’évaluation](file-sync-planning.md#evaluation-cmdlet) du guide de planification. 

### <a name="agent-installation-and-server-configuration"></a>Installation de l’agent et configuration du serveur
Pour plus d’informations sur l’installation et la configuration de l’agent Azure File Sync avec Windows Server, consultez [Planification d’un déploiement Azure File Sync](file-sync-planning.md) et [Guide pratique pour déployer Azure File Sync](file-sync-deployment-guide.md).

- Un redémarrage est nécessaire pour les serveurs disposant d’une installation de l’agent Azure File Sync.
- Le package d’installation de l’agent doit être installé avec des autorisations (administrateur) élevées.
- L’agent n’est pas pris en charge par l’option de déploiement de Nano Server.
- L’agent est uniquement pris en charge sur Windows Server 2019, Windows Server 2016 et Windows Server 2012 R2.
- 2 Gio de mémoire nécessaires pour l’agent. Si le serveur s’exécute sur une machine virtuelle où la mémoire dynamique est activée, la machine virtuelle doit être configurée avec un minimum de 2 048 Mio de mémoire. Pour plus d'informations, consultez [Ressources système recommandées](file-sync-planning.md#recommended-system-resources).
- Le service Storage Sync Agent (FileSyncSvc) ne prend pas en charge les points de terminaison serveur situés sur un volume dont le répertoire d’informations de volume système (SVI) est compressé. Cette configuration produit des résultats inattendus.

### <a name="interoperability"></a>Interopérabilité
- Les antivirus, applications de sauvegarde et autres applications qui ont accès à des fichiers hiérarchisés peuvent provoquer des rappels indésirables, sauf s’ils respectent l’attribut hors connexion et ignorent la lecture du contenu de ces fichiers. Pour plus d’informations, consultez [Résoudre les problèmes liés à Azure File Sync](file-sync-troubleshoot.md).
- Les filtres de fichiers FSRM peuvent entraîner des échecs de synchronisation sans fin lorsque les fichiers sont bloqués en raison du filtre de fichier.
- L’exécution de sysprep sur un serveur sur lequel l’agent Azure File Sync est installé n’est pas prise en charge et peut produire des résultats inattendus. L’agent Azure File Sync doit être installé après avoir déployé l’image du serveur et terminé la mini-configuration de sysprep.

### <a name="sync-limitations"></a>Limitations de synchronisation
Les éléments suivants ne se synchronisent pas, mais le reste du système continue d’opérer normalement :
- Fichiers avec caractères non pris en charge. Reportez-vous au [Guide de résolution des problèmes](file-sync-troubleshoot.md#handling-unsupported-characters) pour consulter la liste des caractères non pris en charge.
- Fichiers ou répertoires se terminant par un point.
- Chemins de plus de 2 048 caractères.
- La partie liste de contrôle d’accès système (SACL) d’un descripteur de sécurité qui est utilisée pour l’audit.
- Attributs étendus.
- Autres flux de données.
- Points d’analyse.
- Liens physiques.
- Si définie sur un serveur de fichiers, la compression n’est pas conservée lorsque les changements se synchronisent avec ce fichier depuis d’autres points de terminaison.
- Tous les fichiers chiffrés avec EFS (ou tout autre chiffrement de mode utilisateur) qui empêche le service de lire les données.

    > [!Note]  
    > Azure File Sync chiffre toujours les données en transit. Les données sont toujours chiffrées au repos dans Azure.
 
### <a name="server-endpoint"></a>Point de terminaison de serveur
- Un point de terminaison de serveur ne peut être créé que sur un volume NTFS. ReFS, FAT, FAT32 et d’autres systèmes de fichiers ne sont actuellement pas pris en charge par Azure File Sync.
- La hiérarchisation cloud n’est pas prise en charge sur le volume système. Pour créer un point de terminaison de serveur sur le volume système, désactivez la hiérarchisation cloud quand vous créez le point de terminaison de serveur.
- Le clustering de basculement est pris en charge uniquement avec les disques en cluster, pas avec les volumes partagés de cluster (CSV).
- Un point de terminaison de serveur ne peut pas être imbriqué. Il peut coexister sur le même volume en parallèle avec un autre point de terminaison.
- Ne stockez pas de système d’exploitation ni de fichier de pagination d’application au sein d’un emplacement de point de terminaison de serveur.
- Le nom de serveur dans le portail n’est pas mis à jour si le serveur est renommé.

### <a name="cloud-endpoint"></a>Point de terminaison cloud
- Azure File Sync prend en charge les modifications directes dans le partage de fichiers Azure. Toutefois, les modifications apportées au partage de fichiers Azure doivent d’abord être détectées par un travail de détection des modifications Azure File Sync. Un travail de détection des modifications est lancé pour un point de terminaison cloud une fois toutes les 24 heures. Pour synchroniser immédiatement les fichiers qui ont été modifiés dans le partage de fichiers Azure, le cmdlet PowerShell [Invoke-AzStorageSyncChangeDetection](/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) permet de lancer manuellement la détection des modifications apportées au partage de fichiers Azure. Par ailleurs, les modifications apportées à un partage de fichiers Azure via le protocole REST ne mettent pas à jour l’heure de dernière modification de SMB et ne sont pas visibles comme des modifications par la synchronisation.
- Le service de synchronisation de stockage et/ou le compte de stockage peuvent être déplacés vers un autre groupe de ressources, un autre abonnement ou un autre locataire Azure AD. Une fois le service de synchronisation de stockage ou le compte de stockage déplacé, vous devez donner à l’application Microsoft.StorageSync l’accès au compte de stockage (consultez [Vérifiez qu’Azure File Sync a accès au compte de stockage](file-sync-troubleshoot.md?tabs=portal1%252cportal#troubleshoot-rbac)).

    > [!Note]  
    > Lors de la création du point de terminaison cloud, le service de synchronisation du stockage et le compte de stockage doivent se trouver dans le même locataire Azure AD. Une fois le point de terminaison cloud créé, le service de synchronisation du stockage et le compte de stockage peuvent être déplacés vers des locataires Azure AD différents.

### <a name="cloud-tiering"></a>Hiérarchisation cloud
- Si un fichier hiérarchisé est copié vers un autre emplacement à l’aide de Robocopy, le fichier résultant n’est pas hiérarchisé. L’attribut hors connexion peut être défini car Robocopy inclut cet attribut de façon erronée dans les opérations de copie.
- Lors de la copie de fichiers à l’aide de Robocopy, utilisez l’option /MIR pour conserver les horodatages des fichiers. Les plus anciens fichiers sont ainsi hiérarchisés plus tôt que les derniers fichiers utilisés.

## <a name="agent-version-11300"></a>Version de l’agent 11.3.0.0
Les notes de publication suivantes concernent la version 11.3.0.0 de l’agent Azure File Sync (publiée le 7 avril 2021). Ces notes s’ajoutent aux notes de publication de la version 11.1.0.0.

### <a name="improvements-and-issues-that-are-fixed"></a>Améliorations et problèmes résolus 
Correction d’un bogue qui entraîne une altération des données si la hiérarchisation cloud est activée et que les fichiers hiérarchisés sont copiés à l’aide de Robocopy avec le paramètre /B.

## <a name="agent-version-11200"></a>Version de l’agent 11.2.0.0
Les notes de publication suivantes concernent la version 11.2.0.0 de l’agent Azure File Sync publiée le 2 février 2021. Ces notes s’ajoutent aux notes de publication de la version 11.1.0.0.

### <a name="improvements-and-issues-that-are-fixed"></a>Améliorations et problèmes résolus 
- Si une session de synchronisation est annulée en raison d'un nombre élevé d'erreurs par élément, la synchronisation peut passer par un rapprochement lorsqu'une nouvelle session démarre si le service Azure File Sync détermine qu'une session de synchronisation personnalisée est nécessaire pour corriger les erreurs par élément.
- L’inscription d’un serveur à l’aide de la cmdlet Register-AzStorageSyncServer peut échouer avec l’erreur « Exception non prise en charge ».
- Nouvelle cmdlet PowerShell (Add-StorageSyncAllowedServerEndpointPath) pour configurer les chemins d’accès aux points de terminaison de serveur autorisés sur un serveur. Cette cmdlet est utile pour les scénarios dans lesquels le déploiement Azure File Sync est managé par un fournisseur de solutions cloud (CSP) ou un fournisseur de services, quand le client souhaite configurer des chemins d’accès aux points de terminaison de serveur autorisés sur un serveur. Lors de la création d’un point de terminaison de serveur, si le chemin d’accès spécifié ne figure pas dans la liste d’autorisation, la création du point de terminaison de serveur échoue. Notez qu’il s’agit d’une fonctionnalité facultative et que tous les chemins d’accès pris en charge sont autorisés par défaut lors de la création d’un point de terminaison de serveur.  

    
    - Pour ajouter un chemin d’accès autorisé au point de terminaison de serveur, exécutez les commandes PowerShell suivantes sur le serveur :

    ```powershell
    Import-Module 'C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll' -verbose
    Add-StorageSyncAllowedServerEndpointPath -Path <path>
    ```  

    - Pour obtenir la liste des chemins d’accès pris en charge, exécutez la commande PowerShell suivante :
    
    ```powershell
    Get-StorageSyncAllowedServerEndpointPath
    ```     
    - Pour supprimer un lien, exécutez la commande PowerShell suivante :
    
    ```powershell
    Remove-StorageSyncAllowedServerEndpointPath -Path <path>
    ```  
## <a name="agent-version-11100"></a>Version de l’agent 11.1.0.0
Les notes de publication suivantes concernent la version 11.1.0.0 de l’agent Azure File Sync (mise en production le 4 novembre 2020).

### <a name="improvements-and-issues-that-are-fixed"></a>Améliorations et problèmes résolus
- Nouveaux modes de hiérarchisation cloud pour contrôler le téléchargement initial et le rappel proactif
    - Mode de téléchargement initial : vous pouvez maintenant choisir la manière dont vous souhaitez que vos fichiers soient téléchargés initialement sur votre nouveau point de terminaison de serveur. Vous voulez que tous vos fichiers soient hiérarchisés ou que le plus grand nombre possible de fichiers soient téléchargés sur votre serveur en fonction du dernier horodateur de modification ? Vous pouvez le faire. Vous ne pouvez pas utiliser une hiérarchisation cloud ? Vous pouvez désormais choisir d’éviter les fichiers hiérarchisés sur votre système. Pour plus d’informations, consultez la section [Créer un point de terminaison de serveur](../file-sync/file-sync-deployment-guide.md?tabs=azure-portal%252cproactive-portal#create-a-server-endpoint) dans la documentation Déployer Azure File Sync.
    - Mode de rappel proactif : chaque fois qu’un fichier est créé ou modifié, vous pouvez le rappeler de manière proactive sur les serveurs que vous spécifiez dans le même groupe de synchronisation. Cela a pour effet de rendre le fichier disponible en vue de son utilisation sur chaque serveur que vous avez spécifié. Vous avez des équipes disséminées dans le monde qui travaillent sur les mêmes données ? Activez le rappel proactif afin que, quand une équipe arrive le matin, tous les fichiers mis à jour par une autre équipe opérant dans un fuseau horaire différent soient téléchargés et prêts à l’emploi. Pour plus d’informations, consultez la section [Rappeler de manière proactive les fichiers nouveaux et modifiés à partir d’un partage de fichiers Azure](../file-sync/file-sync-deployment-guide.md?tabs=azure-portal%252cproactive-portal#proactively-recall-new-and-changed-files-from-an-azure-file-share) de la documentation Déployer Azure File Sync.

- Exclure des applications du suivi de l’heure du dernier accès de la hiérarchisation cloud Vous pouvez désormais exclure des applications du suivi de l’heure du dernier accès. Quand une application accède à un fichier, l’heure du dernier accès au fichier est mise à jour dans la base de données de hiérarchisation cloud. Les applications qui analysent le système de fichiers, telles que les antivirus, ont pour effet que tous les fichiers ont la même heure de dernier accès, ce qui a un impact sur leur hiérarchisation.

    Pour exclure des applications du suivi de l’heure du dernier accès, ajoutez le nom du processus au paramètre de registre HeatTrackingProcessNameExclusionList qui se trouve dans HKEY_LOCAL_MACHINE \SOFTWARE\Microsoft\Azure\StorageSync.

    Exemple : reg ADD "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure\StorageSync" /v HeatTrackingProcessNameExclusionList /t REG_MULTI_SZ /d "SampleApp.exe\0AnotherApp.exe" /f

    > [!Note]  
    > Les processus de déduplication des données et de Gestionnaire de ressources du serveur de fichiers (FSRM) sont exclus par défaut (codés en dur), et la liste d’exclusion de processus est actualisée toutes les 5 minutes.

- Améliorations diverses au niveau des performances et de la fiabilité
    - Amélioration des performances de détection des modifications pour détecter les fichiers modifiés dans le partage de fichiers Azure.
    - Amélioration des performances de chargement de la synchronisation.
    - Le chargement initial est maintenant effectué à partir d’un instantané VSS qui réduit les erreurs par élément et les échecs de session de synchronisation.
    - Améliorations de la fiabilité de synchronisation pour certains modèles d’E/S.
    - Correction d’un bogue pour empêcher la base de données de synchronisation de remonter dans le temps sur des clusters de basculement en cas de basculement.
    - Amélioration des performances de rappel lors de l’accès à un fichier hiérarchisé.

### <a name="evaluation-tool"></a>Outil d’évaluation
Avant de déployer l’agent Azure File Sync, vous devez évaluer s’il est compatible avec votre système à l’aide de l’outil d’évaluation Azure File Sync. Cet outil est une applet de commande Azure PowerShell qui recherche les problèmes potentiels liés à votre système de fichiers et à votre jeu de données, comme des caractères non pris en charge ou une version de système d’exploitation non prise en charge. Pour des instructions d’installation et d’utilisation, voir la section [Outil d’évaluation](../file-sync/file-sync-planning.md#evaluation-cmdlet) du guide de planification. 

### <a name="agent-installation-and-server-configuration"></a>Installation de l’agent et configuration du serveur
Pour plus d’informations sur l’installation et la configuration de l’agent Azure File Sync avec Windows Server, consultez [Planification d’un déploiement Azure File Sync](../file-sync/file-sync-planning.md) et [Guide pratique pour déployer Azure File Sync](../file-sync/file-sync-deployment-guide.md).

- Le package d’installation de l’agent doit être installé avec des autorisations (administrateur) élevées.
- L’agent n’est pas pris en charge par l’option de déploiement de Nano Server.
- L’agent est uniquement pris en charge sur Windows Server 2019, Windows Server 2016 et Windows Server 2012 R2.
- 2 Gio de mémoire nécessaires pour l’agent. Si le serveur s’exécute sur une machine virtuelle où la mémoire dynamique est activée, la machine virtuelle doit être configurée avec un minimum de 2 048 Mio de mémoire. Pour plus d'informations, consultez [Ressources système recommandées](../file-sync/file-sync-planning.md#recommended-system-resources).
- Le service Storage Sync Agent (FileSyncSvc) ne prend pas en charge les points de terminaison serveur situés sur un volume dont le répertoire d’informations de volume système (SVI) est compressé. Cette configuration produit des résultats inattendus.

### <a name="interoperability"></a>Interopérabilité
- Les antivirus, applications de sauvegarde et autres applications qui ont accès à des fichiers hiérarchisés peuvent provoquer des rappels indésirables, sauf s’ils respectent l’attribut hors connexion et ignorent la lecture du contenu de ces fichiers. Pour plus d’informations, consultez [Résoudre les problèmes liés à Azure File Sync](../file-sync/file-sync-troubleshoot.md).
- Les filtres de fichiers FSRM peuvent entraîner des échecs de synchronisation sans fin lorsque les fichiers sont bloqués en raison du filtre de fichier.
- L’exécution de sysprep sur un serveur sur lequel l’agent Azure File Sync est installé n’est pas prise en charge et peut produire des résultats inattendus. L’agent Azure File Sync doit être installé après avoir déployé l’image du serveur et terminé la mini-configuration de sysprep.

### <a name="sync-limitations"></a>Limitations de synchronisation
Les éléments suivants ne se synchronisent pas, mais le reste du système continue d’opérer normalement :
- Fichiers avec caractères non pris en charge. Reportez-vous au [Guide de résolution des problèmes](../file-sync/file-sync-troubleshoot.md#handling-unsupported-characters) pour consulter la liste des caractères non pris en charge.
- Fichiers ou répertoires se terminant par un point.
- Chemins de plus de 2 048 caractères.
- La partie liste de contrôle d’accès système (SACL) d’un descripteur de sécurité qui est utilisée pour l’audit.
- Attributs étendus.
- Autres flux de données.
- Points d’analyse.
- Liens physiques.
- Si définie sur un serveur de fichiers, la compression n’est pas conservée lorsque les changements se synchronisent avec ce fichier depuis d’autres points de terminaison.
- Tous les fichiers chiffrés avec EFS (ou tout autre chiffrement de mode utilisateur) qui empêche le service de lire les données.

    > [!Note]  
    > Azure File Sync chiffre toujours les données en transit. Les données sont toujours chiffrées au repos dans Azure.
 
### <a name="server-endpoint"></a>Point de terminaison de serveur
- Un point de terminaison de serveur ne peut être créé que sur un volume NTFS. ReFS, FAT, FAT32 et d’autres systèmes de fichiers ne sont actuellement pas pris en charge par Azure File Sync.
- La hiérarchisation cloud n’est pas prise en charge sur le volume système. Pour créer un point de terminaison de serveur sur le volume système, désactivez la hiérarchisation cloud quand vous créez le point de terminaison de serveur.
- Le clustering de basculement est pris en charge uniquement avec les disques en cluster, pas avec les volumes partagés de cluster (CSV).
- Un point de terminaison de serveur ne peut pas être imbriqué. Il peut coexister sur le même volume en parallèle avec un autre point de terminaison.
- Ne stockez pas de système d’exploitation ni de fichier de pagination d’application au sein d’un emplacement de point de terminaison de serveur.
- Le nom de serveur dans le portail n’est pas mis à jour si le serveur est renommé.

### <a name="cloud-endpoint"></a>Point de terminaison cloud
- Azure File Sync prend en charge les modifications directes dans le partage de fichiers Azure. Toutefois, les modifications apportées au partage de fichiers Azure doivent d’abord être détectées par un travail de détection des modifications Azure File Sync. Un travail de détection des modifications est lancé pour un point de terminaison cloud une fois toutes les 24 heures. Pour synchroniser immédiatement les fichiers qui ont été modifiés dans le partage de fichiers Azure, le cmdlet PowerShell [Invoke-AzStorageSyncChangeDetection](/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) permet de lancer manuellement la détection des modifications apportées au partage de fichiers Azure. Par ailleurs, les modifications apportées à un partage de fichiers Azure via le protocole REST ne mettent pas à jour l’heure de dernière modification de SMB et ne sont pas visibles comme des modifications par la synchronisation.
- Le service de synchronisation de stockage et/ou le compte de stockage peuvent être déplacés vers un autre groupe de ressources, un autre abonnement ou un autre locataire Azure AD. Une fois le service de synchronisation de stockage ou le compte de stockage déplacé, vous devez donner à l’application Microsoft.StorageSync l’accès au compte de stockage (consultez [Vérifiez qu’Azure File Sync a accès au compte de stockage](../file-sync/file-sync-troubleshoot.md?tabs=portal1%252cportal#troubleshoot-rbac)).

    > [!Note]  
    > Lors de la création du point de terminaison cloud, le service de synchronisation du stockage et le compte de stockage doivent se trouver dans le même locataire Azure AD. Une fois le point de terminaison cloud créé, le service de synchronisation du stockage et le compte de stockage peuvent être déplacés vers des locataires Azure AD différents.

### <a name="cloud-tiering"></a>Hiérarchisation cloud
- Si un fichier hiérarchisé est copié vers un autre emplacement à l’aide de Robocopy, le fichier résultant n’est pas hiérarchisé. L’attribut hors connexion peut être défini car Robocopy inclut cet attribut de façon erronée dans les opérations de copie.
- Lors de la copie de fichiers à l’aide de Robocopy, utilisez l’option /MIR pour conserver les horodatages des fichiers. Les plus anciens fichiers sont ainsi hiérarchisés plus tôt que les derniers fichiers utilisés.
    > [!Warning]  
    > Le commutateur Robocopy /B n'est pas pris en charge avec Azure File Sync. L'utilisation du commutateur Robocopy /B avec un point de terminaison de serveur Azure File Sync comme source peut entraîner une corruption de fichiers.
