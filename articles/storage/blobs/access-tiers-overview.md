---
title: Niveaux d’accès chaud, froid et archive pour les données d’objet blob
titleSuffix: Azure Storage
description: Le Stockage Azure offre différents niveaux d’accès afin que vous puissiez stocker vos données d’objet blob de la manière la plus économique en fonction de la façon dont elles sont utilisées. En savoir plus sur les niveaux d’accès chaud, froid et archive pour Stockage Blob.
author: tamram
ms.author: tamram
ms.date: 10/25/2021
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: fryu
ms.openlocfilehash: 45f330ad2e40cce5b5fca0b6f6fd99d3271bea76
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131063781"
---
# <a name="hot-cool-and-archive-access-tiers-for-blob-data"></a>Niveaux d’accès chaud, froid et archive pour les données d’objet blob

Les données stockées dans le cloud connaissent une croissance exponentielle. Pour gérer les coûts liés à vos besoins croissants en matière de stockage, il peut être utile d’organiser vos données en fonction de la fréquence à laquelle elles sont accessibles et de la durée pendant laquelle elles sont conservées. Le Stockage Azure offre différents niveaux d’accès afin que vous puissiez stocker vos données d’objet blob de la manière la plus économique en fonction de la façon dont elles sont utilisées. Les niveaux d’accès Stockage Azure comprennent les suivants :

- **Niveau chaud** : niveau en ligne optimisé pour le stockage des données qui sont fréquemment consultées ou modifiées. Le niveau chaud offre les coûts de stockage les plus élevés, mais les coûts d’accès les plus faibles.
- **Niveau froid** : niveau en ligne optimisé pour le stockage des données rarement consultées ou modifiées. Les données dans le niveau d’accès froid doivent être stockées pendant un minimum de 30 jours. Le niveau d’accès froid possède des coûts de stockage plus faibles et des coûts d’accès plus élevés que le niveau chaud.
- **Archive** : un niveau hors ligne optimisé pour le stockage des données rarement sollicitées, sous des conditions de latence flexibles, de l’ordre de quelques heures. Les données dans le niveau Archive doivent être stockées pendant un minimum de 180 jours.

Les limites de capacité de Stockage Azure sont définies au niveau du compte, plutôt qu’en fonction du niveau d’accès. Vous pouvez choisir d’optimiser l’utilisation de la capacité dans un niveau ou de répartir la capacité entre deux niveaux ou plus.

## <a name="online-access-tiers"></a>Niveaux d’accès en ligne

Lorsque vos données sont stockées dans un niveau d’accès en ligne (chaud ou froid), les utilisateurs peuvent y accéder immédiatement. Le niveau chaud est le meilleur choix pour les données qui sont régulièrement utilisées, tandis que le niveau froid est idéal pour les données qui sont sollicitées moins fréquemment, mais qui doivent toujours être disponibles en lecture et en écriture.

Voici quelques exemples de scénarios d’utilisation pour le niveau d’accès chaud :

- Données activement utilisées ou censées être fréquemment sollicitées en lecture et écriture.
- Données conservées pour traitement et migration finale vers le niveau d’accès froid.

Voici quelques exemples de scénarios d’utilisation pour le niveau d’accès froid :

- Sauvegarde à court terme et récupération d’urgence des données.
- Les jeux de données plus anciens qui ne sont pas utilisés fréquemment, mais censés être disponibles pour un accès immédiat.
- De grands ensembles de données qui doivent être stockés de manière rentable pendant que des données supplémentaires sont recueillies pour être traitées.

Les données du niveau d’accès froid offrent une disponibilité légèrement inférieure, mais présentent toujours des caractéristiques de durabilité élevée, de latence d’extraction et de débit similaires à celles des données de niveau chaud. Pour les données du niveau froid, une disponibilité légèrement inférieure et des coûts d’accès supérieurs peuvent être des compromis acceptables pour des coûts de stockage globaux inférieurs, par rapport au niveau chaud. Pour plus d’informations, consultez la page [Contrat SLA pour le stockage](https://azure.microsoft.com/support/legal/sla/storage/v1_5/).

Un objet blob du niveau froid dans un compte à usage général v2 est soumis à une pénalité de suppression précoce s’il est supprimé ou déplacé vers un autre niveau avant que 30 jours se soient écoulés. Ces charges sont calculées au prorata. Par exemple, si un objet blob est déplacé vers le niveau froid puis supprimé au bout de 21 jours, vous devrez payer des frais de suppression anticipée équivalant à 9 (30 moins 21) jours de stockage de cet objet blob dans le niveau froid.

Les niveaux à chaud et à froid prennent en charge toutes les configurations de redondance. Pour plus d’informations sur les options de redondance de données dans Stockage Azure, consultez [Redondance de Stockage Azure](../common/storage-redundancy.md).  

## <a name="archive-access-tier"></a>Niveau d’accès archive

Le niveau Archive est un niveau hors connexion pour le stockage des données rarement sollicitées. Le niveau d’accès archive dispose du plus faible coût de stockage, mais les coûts et la latence d’extraction de données sont plus élevés par rapport aux niveaux chaud et froid. Voici quelques exemples de scénarios d’utilisation pour le niveau d’accès archive :

- Sauvegarde à long terme, sauvegarde secondaire et jeux de données d’archivage
- Données d’origine (brutes) qui doivent être conservées, même après leur traitement sous un format final exploitable
- Données de conformité et d’archivage qui doivent être stockées à long terme et qui sont très rarement sollicitées

Les données doivent rester dans le niveau d’accès archive pendant au moins 180 jours ; sinon, elles sont soumises à des frais de suppression anticipée. Par exemple, si un objet blob est déplacé vers le niveau archive puis supprimé ou déplacé vers le niveau d’accès chaud après 45 jours, des frais de suppression anticipée équivalents à 135 (180 moins 45) jours de stockage de cet objet blob dans le niveau archive vous seront facturés.

Tant qu'un objet blob se trouve dans le niveau Archive, il ne peut être ni lu ni modifié. Pour lire ou télécharger un objet blob dans le niveau Archive, vous devez d’abord le réactiver sur un niveau en ligne, chaud ou froid. La réhydratation des données dans le niveau archive peut prendre jusqu’à 15 heures, en fonction de la priorité que vous spécifiez pour l’opération de réhydratation. Pour plus d’informations sur la réactivation des objets blob, consultez [Vue d’ensemble de la réactivation d’objets blob à partir du niveau Archive](archive-rehydrate-overview.md).

Les métadonnées d’un objet blob archivé restent disponibles pour l’accès en lecture, ce qui vous permet de répertorier l’objet blob et ses propriétés, métadonnées et étiquettes d’index. Les métadonnées d’un objet blob dans le niveau Archive sont en lecture seule, tandis que les étiquettes d’index blob peuvent être lues ou écrites. Les instantanés ne sont pas pris en charge pour les objets blob archivés.

Les opérations suivantes sont prises en charge pour les objets blob dans le niveau Archive :

- [Copy Blob](/rest/api/storageservices/copy-blob)
- [Delete Blob](/rest/api/storageservices/delete-blob)
- [Rechercher des objets blob par étiquettes](/rest/api/storageservices/find-blobs-by-tags)
- [Get Blob Metadata](/rest/api/storageservices/get-blob-metadata)
- [Get Blob Properties](/rest/api/storageservices/get-blob-properties)
- [Obtenir les étiquettes d’objet blob](/rest/api/storageservices/get-blob-tags)
- [Lister les objets blob](/rest/api/storageservices/list-blobs)
- [Définir des étiquettes d’objet blob](/rest/api/storageservices/set-blob-tags)
- [Set Blob Tier](/rest/api/storageservices/set-blob-tier)

> [!NOTE]
> Le niveau archive n’est pas pris en charge sur les comptes ZRS, GZRS et RA-GZRS. La migration de LRS vers GRS est prise en charge à condition qu’aucun objet blob n’ait été déplacé vers le niveau archive lorsque le compte était défini sur LRS. Un compte peut être redéfini sur GRS si la mise à jour est effectuée moins de 30 jours après la définition du compte sur LRS et si aucun objet blob n’a été déplacé vers le niveau archive lorsque le compte était défini sur LRS.

## <a name="default-account-access-tier-setting"></a>Paramètre de niveau d’accès du compte par défaut

Les comptes de stockage disposent d’un paramètre de niveau d’accès par défaut qui indique le niveau en ligne dans lequel un nouvel objet blob est créé. Le paramètre de niveau d’accès par défaut peut être défini sur chaud ou froid. Les utilisateurs peuvent remplacer le paramètre par défaut d’un objet blob individuel lors du chargement de l’objet blob ou de la modification de son niveau.

Par défaut, le niveau d’accès pour un nouveau compte de stockage universel v2 est défini sur le niveau chaud. Vous pouvez modifier le paramètre de niveau d’accès par défaut lorsque vous créez un compte de stockage ou après sa création. Si vous ne modifiez pas ce paramètre sur le compte de stockage ou si vous définissez explicitement le niveau lors du chargement d’un objet blob, un nouvel objet blob est chargé par défaut vers le niveau chaud.

Tout objet blob ne disposant pas d’un niveau explicitement attribué déduit le niveau à partir du paramètre de niveau d’accès du compte par défaut. Si le niveau d’accès d’un objet blob est déduit du paramètre de niveau d’accès au compte par défaut, le portail Azure affiche le niveau d’accès comme **Chaud (inféré)** ou **Froid (inféré)** .

La modification du paramètre de niveau d’accès au compte de stockage par défaut s’applique à tous les blobs du compte pour lesquels un niveau d’accès n’a pas été défini explicitement. Si vous basculez le paramètre de niveau d’accès par défaut de chaud à froid dans un compte à usage général v2, vous êtes facturé pour les opérations d’écriture (par tranche de 10 000) pour tous les blobs pour lesquels le niveau d’accès est inféré. Les opérations de lecture (par 10 000) et d’extraction de données (par Go) sont facturées si vous faites passer votre compte universel v2 du niveau froid au niveau chaud.

Quand vous créez un compte de stockage hérité, vous devez spécifier le niveau d’accès par défaut sur chaud ou froid au moment de la création. Aucuns frais n’incombent pour modifier le paramètre de niveau d’accès du compte par défaut de chaud à froid dans un compte de stockage d’objets blob hérité. Les opérations de lecture (par 10 000) et d’extraction de données (par Go) sont facturées si vous faites passer votre compte de stockage d’objets blob du niveau froid au niveau chaud. Microsoft recommande d’utiliser des comptes de stockage universel v2 plutôt que des comptes de stockage d’objets blob si possible.

> [!NOTE]
> Le niveau Archive n’est pas pris en charge en tant que niveau d’accès par défaut pour un compte de stockage.

## <a name="setting-or-changing-a-blobs-tier"></a>Définition ou modification du niveau d’un objet blob

Pour définir explicitement le niveau d’un objet blob lorsque vous le créez, spécifiez le niveau lorsque vous téléchargez l’objet blob.

Une fois qu’un objet blob est créé, vous pouvez modifier son niveau de l’une des manières suivantes :

- En appelant l’opération [Définir le niveau d’objet blob](/rest/api/storageservices/set-blob-tier), soit directement, soit via une stratégie de [gestion du cycle de vie](#blob-lifecycle-management). L’appel de [Définir le niveau du blob](/rest/api/storageservices/set-blob-tier) est généralement la meilleure option quand vous changez le niveau d’un blob pour passer d’un niveau plus chaud à un niveau plus froid.
- En appelant l’opération [Copier l’objet blob](/rest/api/storageservices/copy-blob) pour copier un objet blob d’un niveau à un autre. L’appel de [Copier l’objet blob](/rest/api/storageservices/copy-blob) est recommandé pour la plupart des scénarios dans lesquels vous réactivez un objet blob du niveau Archive vers un niveau en ligne, ou déplacez un objet blob d’un niveau froid à chaud. En copiant un objet blob, vous pouvez éviter la pénalité de suppression précoce, si l’intervalle de stockage requis pour l’objet blob source n’a pas encore expiré. Toutefois, la copie d’un objet blob entraîne des frais de capacité pour deux objets blob, l’objet blob source et l’objet blob de destination.

La modification du niveau d’un objet blob de chaud à froid ou Archive est instantanée, tout comme le passage de froid à chaud. La réactivation d’un objet blob d’un niveau archive vers un niveau chaud ou froid peut prendre jusqu’à 15 heures.

Gardez à l’esprit les points suivants lors du déplacement d’un objet blob entre les niveaux froid et Archive :

- S’il est déduit qu’un objet blob se trouve au niveau froid en fonction du niveau d’accès par défaut du compte de stockage et que l’objet blob est déplacé vers le niveau archive, il n’y a aucuns frais de suppression anticipée.
- Si un objet blob est explicitement déplacé vers le niveau froid, puis déplacé vers le niveau archive, des frais de suppression anticipée s’appliquent.

Le tableau suivant récapitule les approches que vous pouvez suivre pour déplacer des blobs entre différents niveaux.

| Origine/Destination | Niveau de stockage chaud | Niveau de stockage froid | Niveau de stockage archive |
|--|--|--|--|
| **Niveau chaud** | N/A | Modifiez le niveau d’un blob de chaud à froid avec **Définir le niveau du blob** ou **Copier le blob**. [En savoir plus…](manage-access-tier.md)<br /><br />Déplacez des blobs vers le niveau froid avec une stratégie de gestion du cycle de vie. [En savoir plus…](lifecycle-management-overview.md) | Modifiez le niveau d’un blob de chaud à archive avec **Définir le niveau du blob** ou **Copier le blob**. [En savoir plus…](archive-blob.md) <br /><br />Archivez les blobs avec une stratégie de gestion du cycle de vie. [En savoir plus…](lifecycle-management-overview.md) |
| **Niveau froid** | Modifiez le niveau d’un blob de froid à chaud avec **Définir le niveau du blob** ou **Copier le blob**. [En savoir plus…](manage-access-tier.md) <br /><br />Déplacez des blobs vers le niveau chaud avec une stratégie de gestion du cycle de vie. [En savoir plus…](lifecycle-management-overview.md) | N/A | Modifiez le niveau d’un blob de froid à archive avec **Définir le niveau du blob** ou **Copier le blob**. [En savoir plus…](archive-blob.md) <br /><br />Archivez les blobs avec une stratégie de gestion du cycle de vie. [En savoir plus…](lifecycle-management-overview.md) |
| **Niveau archive** | Réhydratez vers le niveau chaud avec **Définir le niveau du blob** ou **Copier le blob**. [En savoir plus…](archive-rehydrate-to-online-tier.md) | Réhydratez vers le niveau froid avec **Définir le niveau du blob** ou **Copier le blob**. [En savoir plus…](archive-rehydrate-to-online-tier.md) | N/A |

## <a name="blob-lifecycle-management"></a>Gestion de cycle de vie des objets blob

La gestion du cycle de vie du stockage d’objets blob offre une stratégie basée sur les règles que vous pouvez utiliser pour transférer vos données vers le niveau d’accès souhaité lorsque les conditions spécifiées sont remplies. Vous pouvez également utiliser la gestion du cycle de vie pour faire expirer les données à la fin de leur durée de vie. Pour plus d’informations, consultez [Optimiser les coûts en automatisant les niveaux d’accès au Stockage Blob Azure](./lifecycle-management-overview.md).

> [!NOTE]
> Les données stockées dans le niveau d’accès d’objets blob de blocs Premium ne peuvent pas être déplacées dans le niveau chaud, froid ou archive avec [Définir le niveau du blob](/rest/api/storageservices/set-blob-tier) ou à l’aide de la gestion du cycle de vie du Stockage Blob Azure. Pour déplacer les données, effectuez une copie synchrone du compte de stockage d’objets blob de blocs dans le niveau d’accès chaud d’un autre compte à l’aide de l’[API Put Block From URL](/rest/api/storageservices/put-block-from-url) ou d’une version d’AzCopy qui prend en charge cette API. L’API **Put Block From URL** copie les données sur le serveur de manière synchrone, ce qui signifie que l’appel est effectué uniquement quand toutes les données ont été déplacées de l’emplacement d’origine sur le serveur vers l’emplacement de destination.

## <a name="summary-of-access-tier-options"></a>Résumé des options de niveau d’accès

Le tableau suivant récapitule les fonctionnalités des niveaux d’accès chaud, froid et Archive.

|  | **Niveau chaud** | **Niveau froid** | **Niveau archive** |
|--|--|--|--|
| **Disponibilité** | 99,9 % | 99 % | Hors connexion |
| **Disponibilité** <br> **(Lectures RA-GRS)** | 99,99 % | 99,9 % | Hors connexion |
| **Frais d’utilisation** | Coûts de stockage supérieurs, mais coûts d’accès et de transaction inférieurs | Coûts de stockage inférieurs, mais coûts d'accès et de transaction supérieurs | Coûts de stockage les plus faibles, mais coûts d'accès et de transaction les plus élevés |
| **Période de rétention de données minimale recommandée** | N/A | 30 jours<sup>1</sup> | 180 jours |
| **Latence** <br> **(Temps jusqu’au premier octet)** | Millisecondes | Millisecondes | Heures<sup>2</sup> |
| **Configurations de redondance prises en charge** | Tous | Tous | LRS, GRS et RA-GRS<sup>3</sup> uniquement |

<sup>1</sup> Les objets du niveau froid sur des comptes à usage général v2 ont une durée de rétention minimale de 30 jours. Pour les comptes Stockage Blob, il n’y a pas de durée de rétention minimale pour le niveau d’accès froid.

<sup>2</sup> lors de la réactivation d’un objet blob à partir du niveau Archive, vous pouvez choisir une option de priorité standard ou élevée. Chaque option a des latences et des coûts de récupération différents. Pour plus d’informations, voir [Vue d’ensemble de la réactivation d’objets blob à partir du niveau Archive](archive-rehydrate-overview.md).

<sup>3</sup> Pour plus d’informations sur les configurations de redondance dans Stockage Azure, consultez [Redondance de Stockage Azure](../common/storage-redundancy.md).

## <a name="pricing-and-billing"></a>Tarification et facturation

Tous les comptes de stockage utilisent un modèle tarifaire pour le stockage d’objets blob de blocs basé sur le niveau d’un objet blob. Gardez à l’esprit les considérations relatives à la facturation décrites dans les sections suivantes.

Pour plus d’informations sur la tarification des objets blob de blocs, consultez [Tarification d’objet blob de blocs](https://azure.microsoft.com/pricing/details/storage/blobs/).

### <a name="storage-capacity-costs"></a>Coûts de capacité de stockage

Les coûts de stockage des données varient en fonction de la quantité de données stockées et du niveau d’accès. La capacité par gigaoctet diminue à mesure que le niveau refroidit.

### <a name="data-access-costs"></a>Coûts d’accès aux données

les frais d’accès aux données augmentent à mesure que le niveau refroidit. Pour les données des niveaux d’accès froid et archive, des frais d’accès aux données en lecture vous sont facturés par gigaoctet.

### <a name="transaction-costs"></a>Coûts de transaction

Des frais par transaction s'appliquent à tous les niveaux et augmentent à mesure que le niveau devient plus froid.

### <a name="geo-replication-data-transfer-costs"></a>Coûts de transfert de données de géoréplication

ces coûts s’appliquent uniquement aux comptes pour lesquels la géoréplication est configurée, notamment GRS et RA-GRS. Le transfert de données de géoréplication implique des frais par gigaoctet.

### <a name="outbound-data-transfer-costs"></a>Coûts de transfert de données sortantes

Les transferts de données sortants (données transférées hors d’une région Azure) entraînent une facturation de l’utilisation de la bande passante au gigaoctet. Pour plus d’informations sur les frais de transfert de données sortantes, consultez la page [Détails sur les tarifs de bande passante](https://azure.microsoft.com/pricing/details/bandwidth/).

### <a name="changing-the-default-account-access-tier"></a>Modification du niveau d’accès au compte par défaut

La modification du niveau d'accès au compte entraîne des frais de changement de niveau pour tous les blobs qui n'ont pas déjà un niveau explicitement défini. Pour plus d’informations, consultez la section suivante, [Modification du niveau d’accès d’un objet blob](#changing-a-blobs-access-tier).

### <a name="changing-a-blobs-access-tier"></a>Modification du niveau d’accès d’un objet blob

Gardez à l’esprit les impacts sur la facturation suivants lors de la modification du niveau d’un objet blob :

- Lorsqu’un objet blob est chargé ou que son niveau est modifié, il est facturé au tarif correspondant au moment du chargement ou du changement de niveau.
- Quand un objet blob est déplacé vers un niveau plus froid (chaud à froid, chaud à archive ou froid à archive), l’opération est facturée comme une opération d’écriture dans le niveau de destination, facturée aux tarifs des opérations d’écriture (par 10 000) et d’écriture de données (par Go) du niveau de destination.
- Lorsqu’un objet blob est déplacé vers un niveau plus chaud (archive à froid, archive à chaud ou froid à chaud), l’opération est facturée comme une lecture à partir du niveau source, facturée aux tarifs des opérations de lecture (par 10 000) et d’extraction de données (par Go) du niveau source. Des frais de suppression anticipée peut également s’appliquer pour tout objet blob déplacé hors du niveau froid ou archive.
- Lorsqu’un objet blob est réactivé à partir du niveau Archive, les données de cet objet blob sont facturées en tant que données archivées jusqu’à ce que les données soient restaurées et que le niveau de l’objet blob passe à chaud ou froid.

Le tableau suivant résume la facturation des changements de niveau.

| | **Tarif d’écriture (opération + accès)** | **Tarif de lecture (opération + accès)** |
| ---- | ----- | ----- |
| Opération **Définir le niveau de l’objet Blob** | Chaud à froid<br> Chaud à Archive<br> Froid à Archive | Archive à froid<br> Archive à chaud<br> Froid à chaud

Le changement du niveau d’accès pour un objet blob lorsque la gestion des versions des objets blob est activée, ou si l’objet blob a des instantanés, peut donner lieu à des frais supplémentaires. Pour plus d’informations sur les objets blob lorsque la gestion des versions est activée, consultez [Tarification et facturation](versioning-overview.md#pricing-and-billing) dans la documentation sur le contrôle de version des objets blob. Pour plus d’informations sur les objets blob avec instantanés, consultez [Tarification et facturation](snapshots-overview.md#pricing-and-billing) dans la documentation sur les instantanés d’objet blob.

## <a name="feature-support"></a>Prise en charge des fonctionnalités

Ce tableau montre comment cette fonctionnalité est prise en charge dans votre compte ainsi que l’impact sur la prise en charge lorsque vous activez certaines fonctionnalités.

| Type de compte de stockage | Stockage Blob (prise en charge par défaut) | Data Lake Storage Gen2 <sup>1</sup> | NFS 3.0 <sup>1</sup> |
|--|--|--|--|
| Usage général v2 Standard | ![Oui](../media/icons/yes-icon.png) | ![Oui](../media/icons/yes-icon.png) | ![Oui](../media/icons/yes-icon.png) |
| Objets blob de blocs Premium | ![Non](../media/icons/no-icon.png) | ![Non](../media/icons/no-icon.png) | ![Non](../media/icons/no-icon.png) |

<sup>1</sup> Data Lake Storage Gen2 et le protocole NFS (Network File System) 3.0 requièrent tous deux un compte de stockage avec un espace de noms hiérarchique activé.

Pour plus d’informations sur la prise en charge des fonctionnalités par région, consultez [Produits Azure disponibles par région](https://azure.microsoft.com/global-infrastructure/services/?products=storage).

## <a name="next-steps"></a>Étapes suivantes

- [Comment gérer le niveau d’un objet blob dans un compte de stockage Azure](manage-access-tier.md)
- [Comment gérer le niveau d’accès par défaut d’un compte de stockage Azure](../common/manage-account-default-access-tier.md)
- [Optimiser les coûts en automatisant les niveaux d’accès au stockage Blob Azure](./lifecycle-management-overview.md)
