---
title: Oracle Database dans les stratégies de sauvegarde de machine virtuelle Azure
description: Options de sauvegarde des bases de données Oracle dans un environnement de machine virtuelle Linux Azure.
author: cro27
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: article
ms.date: 01/28/2021
ms.author: cholse
ms.reviewer: dbakevlar
ms.openlocfilehash: 5be1a70ecd9a5eefdd1df29a5305747cee09ddaa
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132523749"
---
# <a name="oracle-database-in-azure-linux-vm-backup-strategies"></a>Oracle Database dans les stratégies de sauvegarde de machine virtuelle Linux Azure

**S’applique à :** :heavy_check_mark: Machines virtuelles Linux 

Les sauvegardes de base de données protègent la base de données contre des pertes de données résultant de défaillances du composant de stockage et du centre de données. Elles peuvent également permettre de réparer des erreurs humaines, ainsi que de cloner des bases de données à des fins de développement ou de test. 

Dans Azure, étant donné que tout le stockage est hautement redondant et que la perte d’un ou plusieurs disques n’entraîne pas de panne de base de données, les sauvegardes sont le plus souvent utilisées pour la protection contre des erreurs humaines, les opérations de clonage ou la préservation de données en vue de satisfaire à des prescriptions réglementaires. Elles permettent également de se prémunir contre d’éventuelles pannes régionales quand une technologie de récupération d’urgence telle que DataGuard n’est pas disponible. Dans ce cas, les sauvegardes doivent être stockées dans différentes régions Azure à l’aide d’une réplication géo-redondante, de sorte qu’elles soient disponibles en dehors de la région de la base de données primaire.

## <a name="azure-storage"></a>Stockage Azure 

Le [service Stockage Azure](../../../storage/common/storage-introduction.md) est une solution de stockage cloud de Microsoft pour des scénarios de stockage de données modernes. Il offre une panoplie de services utilisables pour monter sur une machine virtuelle Linux Azure un stockage externe faisant office de support de sauvegarde pour des bases de données Oracle. Un outil de sauvegarde tel que RMAN d’Oracle est requis pour lancer une opération de sauvegarde ou de restauration, et copier la sauvegarde vers/depuis le service Stockage Azure.
 
Le service Stockage Azure offre les avantages suivants :

-  Durable et hautement disponible. La redondance garantit que vos données sont sécurisées en cas de défaillance matérielle temporaire. Par défaut, tout le stockage est triplé en miroir. Vous pouvez également choisir de répliquer des données entre des centres de données ou des régions géographiques pour une protection supplémentaire contre les catastrophes locales ou les catastrophes naturelles. Les données ainsi répliquées restent hautement disponibles en cas de panne inattendue.

-  Sécurisé. Toutes les données écrites dans un compte de stockage Azure sont chiffrées par le service. Le Stockage Azure vous permet de contrôler de manière plus précise qui a accès à vos données.

-  Évolutif. Le Stockage Azure est conçu pour être hautement évolutif afin de répondre aux besoins de stockage de données et de performances des applications actuelles.

-  Géré. Azure gère la maintenance du matériel, les mises à jour et les problèmes critiques pour vous.

-  Accessible. Les données dans le Stockage Azure sont accessibles n’importe où dans le monde via HTTP ou HTTPS. Microsoft fournit des bibliothèques clientes pour le Stockage Azure dans une variété de langages, dont .NET, Java, Node.js, Python, PHP, Ruby, Go et autres encore, ainsi qu’une API REST avancée. Le stockage Azure prend en charge l’écriture de scripts Azure PowerShell ou l’interface de ligne de commande Azure. De plus, le portail Azure et l’Explorateur Stockage Azure offrent des solutions visuelles simples pour utiliser vos données.

La plateforme de stockage Azure inclut les services de données suivants, utilisables en tant que support de sauvegarde pour la base de données Oracle :

-  Objets blob Azure : magasin d’objets hautement scalable pour les données texte ou binaires. Prend également en charge l’analytique Big Data via Data Lake Storage Gen2.

-  Azure Files : partages de fichiers gérés pour les déploiements sur le cloud ou locaux.

-  Disques Azure : Volumes de stockage de niveau bloc pour les machines virtuelles Azure.

### <a name="cross-regional-storage-mounting"></a>Montage de stockage interrégion

La possibilité d’accéder à un stockage de sauvegarde dans plusieurs régions est un aspect important de la continuité d’activité et reprise d’activité (BCDR), utile pour le clonage de bases de données à partir de sauvegardes dans différentes régions géographiques. Le stockage cloud Azure offre cinq niveaux de [redondance](../../../storage/common/storage-redundancy.md) :
- Le stockage localement redondant [(LRS)](../../../storage/common/storage-redundancy.md#locally-redundant-storage) réplique vos données trois fois au sein d’un même emplacement physique dans la région primaire.  
- Le stockage redondant interzone [(ZRS)](../../../storage/common/storage-redundancy.md#zone-redundant-storage) protège vos données par LRS dans la région primaire, et les réplique de façon synchrone dans trois zones de disponibilité (AZ) au sein de la région primaire. Chaque AZ est également protégée par LRS. 
- Le stockage géo-redondant [(GRS)](../../../storage/common/storage-redundancy.md#geo-redundant-storage) protège vos données par LRS dans la région primaire, et les réplique de façon asynchrone dans une région secondaire où elles sont également protégées par LRS.
- Le stockage géo-redondant interzone [(GZRS)](../../../storage/common/storage-redundancy.md#geo-redundant-storage) copie vos données de façon synchrone dans trois zones de disponibilité Azure au sein de la région primaire en utilisant un ZRS. Elle copie ensuite vos données de façon asynchrone vers un emplacement physique unique dans la région secondaire. Dans tous les emplacements, les données sont également protégées par LRS. 
- Le stockage géo-redondant avec accès en lecture [(RA-GRS)](../../../storage/common/storage-redundancy.md#geo-redundant-storage) et le stockage géo-redondant interzone avec accès en lecture [(RA-GZRS)](../../../storage/common/storage-redundancy.md#geo-redundant-storage) permettent à tout moment un accès en lecture seule aux données répliquées dans la région secondaire. 

#### <a name="blob-storage-and-file-storage"></a>Stockage Blob et Stockage Fichier

Lorsque vous utilisez Azure Files avec les protocoles SMB ou NFS 4.1 pour monter un stockage de sauvegarde, notez qu’Azure Files ne prend pas en charge le stockage géo-redondant avec accès en lecture (RA-GRS) ni le stockage géo-redondant interzone avec accès en lecture (RA-GZRS). 

En outre, si l’exigence de stockage de sauvegarde est supérieure à 5 Tio, Azure Files nécessite que la [fonctionnalité de partage de fichiers volumineux](../../../storage/files/storage-files-planning.md) soit activée. Celle-ci ne prend pas en charge la redondance GRS ou GZRS mais uniquement le stockage localement redondant (LRS). 

Un Blob Azure monté à l’aide du protocole NFS 3.0 ne prend actuellement en charge que les redondances LRS et ZRS.  

Un Blob Azure configuré avec n’importe quelle option de redondance peut être monté à l’aide de Blobfuse.

#### <a name="recovery-services-vault"></a>Coffre Recovery Services 

Un coffre Recovery Services est une entité de gestion qui stocke les points de récupération créés au fil du temps et fournit une interface permettant d’effectuer des opérations liées à la sauvegarde. Cela inclut l’exécution de sauvegardes à la demande, l’exécution de restaurations et la création de stratégies de sauvegarde.
La Sauvegarde Azure gère automatiquement le stockage du coffre. Vous devez spécifier le mode de réplication de ce stockage lors de sa création. Il n’est pas possible de le modifier une fois les éléments protégés dans le coffre. Pour la redondance régionale, choisissez le paramètre géo-redondant.

Si vous envisagez d’effectuer une restauration vers une [région jumelée Azure](../../../best-practices-availability-paired-regions.md) secondaire, activez fonctionnalité de [restauration interrégion](../../../backup/backup-create-rs-vault.md). Lorsque la restauration interrégion est activée, le stockage de sauvegarde est déplacé du stockage géo-redondant (GRS) vers un stockage géo-redondant avec accès en lecture (RA-GRS). 

### <a name="azure-blob-storage"></a>Stockage Blob Azure

Un Stockage Blob Azure est un service de stockage cloud éminemment économique, durable et sécurisé, utilisé pour stocker de grandes quantités de données non structurées. Il s’agit d’un support idéal pour des sauvegardes Oracle Database. Vous pouvez monter un Stockage Blob Azure sur des machines virtuelles Linux Azure à l’aide du protocole NFS v3.0 ou de Blobfuse (FUSE Linux).

#### <a name="azure-blob-blobfuse"></a>Blobfuse d’objet blob Azure

[Blobfuse](../../../storage/blobs/storage-how-to-mount-container-linux.md) est un projet open source développé pour fournir un système de fichiers virtuel adossé au Stockage Blob Azure. Il utilise la bibliothèque open source libfusible pour communiquer avec le module de noyau FUSE Linux, et implémente les opérations de système de fichiers à l’aide des API REST Azure Storage Blob. Blobfuse est actuellement disponible pour les distributions Ubuntu et Centos/RedHat. Il est également disponible pour Kubernetes à l’aide du [pilote CSI](https://github.com/kubernetes-sigs/blob-csi-driver). 

Bien que Blobfuse soit omniprésent dans toutes les régions Azure et fonctionne avec tous les types de comptes de stockage, dont v1/v2 à usage général et Azure Data Lake Store Gen2, les performances qu’il offre sont inférieures à celles d’autres protocoles tels que SMB ou NFS. Pour un support de sauvegarde de base de données, nous vous recommandons d’utiliser les protocoles SMB ou [NFS](../../../storage/blobs/storage-how-to-mount-container-linux.md) pour monter le Stockage Blob Azure. 

#### <a name="azure-blob-nfs-v30"></a>Azure Blob NFS v3.0

Le support Azure pour le protocole NFS v3.0 est désormais disponible. La prise en charge du protocole [NFS](../../../storage/blobs/network-file-system-protocol-support.md) permet à des clients Windows et Linux de monter un conteneur de stockage d’objets blob sur une machine virtuelle Azure. 

Pour garantir la sécurité du réseau, le compte de stockage utilisé pour le montage NFS doit être contenu dans un réseau virtuel. La sécurité et les listes de contrôle d’accès (ACL) d’Azure Active Directory (AD) ne sont pas encore prises en charge dans les comptes sur lesquels la prise en charge du protocole NFS 3.0 est activée.

### <a name="azure-files"></a>Azure Files

[Azure Files](../../../storage/files/storage-files-introduction.md) est un système de fichiers distribué complètement managé basé sur le cloud, qui peut être monté sur des clients Windows, Linux ou macOS locaux ou dans le cloud.

Azure Files offre des partages de fichiers multiplateformes complètement managés dans le cloud, qui sont accessibles via les protocoles SMB et NFS. Actuellement, Azure Files ne prend pas en charge l’accès multiprotocole, de sorte qu’un partage ne peut être qu’un partage NFS ou un partage SMB. Nous vous recommandons de déterminer quel protocole correspond le mieux à vos besoins avant de créer des partages de fichiers Azure.

Vous pouvez également protéger des partages de fichiers Azure via le service Sauvegarde Azure dans le coffre Recovery Services, ce qui a pour effet de fournir une couche supplémentaire de protection aux sauvegardes RMAN Oracle.

#### <a name="azure-files-nfs-v41"></a>Azure Files NFS v4.1

Vous pouvez monter des partages de fichiers Azure dans des distributions Linux à l’aide du protocole NFS (Network File System) v4.1. Il existe un certain nombre de limitations aux fonctionnalités prises en charge. Pour plus d’informations, consultez [Prise en charge des fonctionnalités de Stockage Azure](../../../storage/files/files-nfs-protocol.md#support-for-azure-storage-features). 

[!INCLUDE [files-nfs-regional-availability](../../../../includes/files-nfs-regional-availability.md)]

#### <a name="azure-files-smb-30"></a>Azure Files SMB 3.0

Les partages de fichiers Azure peuvent être montés dans des distributions Linux à l’aide du client SMB en mode noyau. Le protocole CIFS (Common Internet File System), disponible sur les distributions Linux, est un dialecte de SMB. Le protocole SMB Azure Files sur des machines virtuelles Linux est monté en tant que système de fichiers de type CIFS et le package CIFS doit être installé. 

Le protocole SMB Azure Files est généralement disponible dans toutes les régions Azure, et présente les mêmes caractéristiques de performances que les protocoles NFS v3.0 et v4.1. Il s’agit donc actuellement de la méthode recommandée pour fournir un support de stockage de sauvegarde aux machines virtuelles Linux Azure.  

Deux versions du protocole SMB sont prises en charge, SMB 2.1 et SMB 3.0, cette dernière étant recommandée parce qu’elle prend en charge le chiffrement en transit. Toutefois, diverses versions de noyaux Linux assurent une prise en charge différente de SMB 2.1 et 3.0. Pour obtenir davantage d’informations, voir [Monter un partage de fichiers SMB Azure sur Linux](../../../storage/files/storage-how-to-use-files-linux.md) pour vérifier que votre application prend en charge SMB 3.0. 

Étant donné qu’Azure Files est conçu pour être un service de partage de fichiers multi-utilisateurs, vous devez régler certaines caractéristiques afin de le rendre plus adapté en tant que support de stockage de sauvegarde. Il est recommandé de désactiver la mise en cache et la définition des ID d’utilisateur et de groupe pour les fichiers créés.

## <a name="azure-netapp-files"></a>Azure NetApp Files

Le service [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-solution-architectures.md) est une solution de stockage complète pour bases de données Oracle dans des machines virtuelles Azure. S’appuyant sur un stockage de fichiers mesuré hautes performances de qualité professionnelle, il prend en charge tout type de charge de travail et est hautement disponible par défaut. Avec le pilote dNFS (Direct NFS) Oracle, le service Azure NetApp Files fournit une couche de stockage hautement optimisée pour Oracle Database.

Le service Azure NetApp Files fournit des copies d’instantanés adossées à un stockage efficace sur le système de stockage sous-jacent qui utilise un mécanisme de redirection en écriture (Redirect on Write, RoW). Bien que la prise et la restauration des copies d’instantanés soient extrêmement rapides, elles font uniquement office de première ligne de défense pouvant prendre en charge la grande majorité des opérations de restauration requises d’une organisation donnée, qui consistent souvent à réparer des erreurs humaines. Toutefois, les copies instantanées ne constituent pas une sauvegarde complète. Pour couvrir toutes les exigences en matière de sauvegarde et de restauration, des réplicas d’instantanés externes et/ou d’autres copies de sauvegarde doivent être créés dans une zone géographique distante pour protéger contre toute panne régionale. Pour en savoir plus sur l’utilisation de fichiers NetApp pour des bases de données Oracle DB sur Azure, consultez ce [rapport](https://www.netapp.com/pdf.html?item=/media/17105-tr4780pdf.pdf).

## <a name="azure-backup-service"></a>Service Azure Backup

Le service [Sauvegarde Azure](../../../backup/backup-overview.md) est une PaaS complètement managée fournissant des solutions simples, sécurisées et rentables pour sauvegarder vos données et les restaurer à partir du cloud Microsoft Azure. Le service Sauvegarde Azure peut sauvegarder et restaurer des clients locaux, des machines virtuelles Azure, des partages Azure Files, ainsi que des bases de données SQL Server, Oracle, MySQL, PostreSQL et SAP HANA sur des machines virtuelles Azure. 

Le service Sauvegarde Azure fournit des sauvegardes indépendantes et isolées pour éviter une destruction accidentelle des données d’origine. Les sauvegardes sont stockées dans un [coffre Recovery Services](../../../backup/backup-azure-recovery-services-vault-overview.md) avec gestion intégrée des points de récupération. La configuration et la scalabilité sont simples : les sauvegardes sont optimisées, et vous pouvez facilement effectuer des restaurations en fonction des besoins. Le service Sauvegarde Azure utilise la puissance sous-jacente et la mise à l’échelle illimitée du cloud Azure pour offrir une haute disponibilité, sans surcharge de maintenance ou de supervision. Le service Sauvegarde Azure ne limite pas la quantité de données entrantes ou sortantes que vous transférez, et n’occasionne pas de frais pour les données transférées, celles-ci étant sécurisées tant en transit qu’au repos. 

Par souci de durabilité, le service Sauvegarde Azure offre plusieurs types de réplication pour assurer la haute disponibilité des données de votre sauvegarde.

- Le stockage localement redondant [(LRS)](../../../storage/common/storage-redundancy.md#locally-redundant-storage) réplique vos données trois fois (en créant trois copies de vos données) dans une unité d’échelle de stockage au sein d’un centre de données.
- Le stockage géo-redondant [(GRS)](../../../storage/common/storage-redundancy.md#geo-redundant-storage) est l’option de réplication par défaut recommandée. Le stockage géo-redondant réplique vos données vers une région secondaire, distante de plusieurs centaines de kilomètres de l’emplacement principal des données sources.

Un coffre créé avec une redondance GRS inclut l’option permettant de configurer la fonctionnalité [Restauration interrégion](../../../backup/backup-create-rs-vault.md#set-storage-redundancy) qui vous permet de restaurer des données dans une région jumelée Azure secondaire.

Le service Sauvegarde Azure fournit une [infrastructure](../../../backup/backup-azure-linux-app-consistent.md) pour assurer la cohérence des applications lors des sauvegardes de machines virtuelles Windows et Linux pour diverses applications telles qu’Oracle, MySQL, Mongo DB, SAP HANA et PostGreSQL, appelées instantanés de cohérence d’application. Cela implique l’appel d’un pré-script (pour suspendre les applications) avant la prise d’une capture instantanée de disques, et l’appel d’un post-script (commandes pour dégeler les applications) une fois l’instantané capturé, afin de ramener les applications en mode normal. Si des exemples de pré-scripts et post-scripts sont fournis sur GitHub, la création et la maintenance de ceux-ci vous incombent. Dans le cas d’Oracle, la base de données doit être en mode archivelog pour permettre des sauvegardes en ligne, et les commandes de sauvegarde de début et de fin de la base de données appropriées sont exécutées dans les pré-scripts et post-scripts que vous devez créer et gérer. 

Le service Sauvegarde Azure fournit désormais une [infrastructure améliorée de pré-script et de post-script](https://github.com/Azure/azure-linux-extensions/tree/master/VMBackup/main/workloadPatch/DefaultScripts), actuellement en préversion, consistant en pré-scripts et post-scripts empaquetés pour des applications sélectionnées. Les utilisateurs du service Sauvegarde Azure doivent simplement nommer l’application. Ensuite, la sauvegarde de machine virtuelle Azure appelle automatiquement les pré-scripts et post-scripts appropriés. Les pré-scripts et post-scripts empaquetés sont gérés par l’équipe du service Sauvegarde Azure afin que les utilisateurs soient assurés de la prise en charge, de la propriété et de la validité desdits scripts. Actuellement, les applications prises en charge pour l’infrastructure améliorée sont Oracle et MySQL. D’autres types d’applications sont attendus à l’avenir. L’instantané étant une copie complète du stockage, et non un instantané incrémentiel ou de copie sur écriture, il s’agit d’un support efficace à partir duquel restaurer votre base de données.

## <a name="next-steps"></a>Étapes suivantes

- [Démarrage rapide Créer une base de données Oracle DB](oracle-database-quick-create.md)
- [Sauvegarder une base de données Oracle DB sur Azure Files](oracle-database-backup-azure-storage.md)
- [Sauvegarder une base de données Oracle DB à l’aide du service Sauvegarde Azure](oracle-database-backup-azure-backup.md)
