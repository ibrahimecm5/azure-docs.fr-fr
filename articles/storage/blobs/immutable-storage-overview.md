---
title: Vue d’ensemble du stockage immuable pour les données blob
titleSuffix: Azure Storage
description: Le Stockage Azure assure la prise en charge des disques optiques non réinscriptibles (WORM) pour le Stockage Blob, qui permettent aux utilisateurs de stocker des données dans un état immuable et non effaçable. Les stratégies de rétention basées sur le temps stockent les données blob dans un état WORM pendant un intervalle spécifié, alors que les archivages juridiques restent en place jusqu’à ce qu’ils soient explicitement effacés.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 08/31/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: references_regions
ms.openlocfilehash: 7514531d77b145061d8993ccf75cb78e2976ad20
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131441454"
---
# <a name="store-business-critical-blob-data-with-immutable-storage"></a>Stocker des données blob critiques pour l’entreprise avec un stockage immuable

Le stockage immuable pour le Stockage Blob Azure permet aux utilisateurs de stocker des données critiques pour l’entreprise dans un état WORM (écriture unique, lectures multiples). Dans l’état WORM, les données ne peuvent pas être modifiées ou supprimées pendant un intervalle spécifié par l’utilisateur. En configurant des stratégies d’immuabilité pour les données blob, vous pouvez protéger vos données contre les remplacements et les suppressions.

Le stockage immuable du Stockage Blob Azure prend en charge deux types de stratégies d’immuabilité.

- **Stratégies de rétention basée sur le temps** : avec une stratégie de rétention basée sur le temps, les utilisateurs peuvent définir des stratégies pour stocker des données pendant un intervalle spécifié. Lorsqu’une stratégie de rétention basée sur le temps est définie, des objets peuvent être créés et lus, mais ils ne peuvent être ni modifiés ni supprimés. Après l’expiration de la période de rétention, les objets peuvent être supprimés, mais pas remplacés. Pour plus d’informations sur les stratégies de rétention basées sur le temps, consultez [Stratégies de rétention basées sur le temps pour les données d’objets blob immuables](immutable-time-based-retention-policy-overview.md).

- **Stratégies de conservation légale** : la conservation légale stocke des données immuables jusqu’à ce que la conservation légale soit explicitement désactivée. Lorsqu’une conservation juridique est définie, des objets peuvent être créés et lus, mais ils ne peuvent être ni modifiés ni supprimés. Pour en savoir plus sur les stratégies de conservation légale, consultez [Conservation légale pour les données d’objets blob immuables](immutable-legal-hold-overview.md).

Le diagramme suivant montre comment les stratégies de rétention basées sur le temps et les conservations légales empêchent les opérations d’écriture et de suppression quand elles sont appliquées.

:::image type="content" source="media/immutable-storage-overview/worm-diagram.png" alt-text="Diagramme montrant comment les stratégies de rétention et les conservations légales empêchent les opérations d’écriture et de suppression":::

## <a name="about-immutable-storage-for-blobs"></a>À propos du stockage immuable pour des objets blob

Le stockage immuable permet aux établissements de soins de santé, aux institutions financières et aux secteurs associés &mdash; en particulier les organisations de courtage &mdash; de stocker des données en toute sécurité. Il peut être utilisé dans n’importe quel scénario pour protéger les données vitales contre la modification ou la suppression.

Les applications typiques incluent :

- **Conformité réglementaire** : Le stockage immuable pour le Stockage Blob Azure permet aux organisations de respecter entre autres les réglementations SEC 17a-4(f), CFTC 1.31(d) et FINRA.

- **Conservation sécurisée des documents** : avec le stockage immuable des objets blob, les données ne sont ni modifiables ni supprimables par les utilisateurs, même s’ils disposent d’un compte avec privilèges administratifs.

- **Conservation légale** : le stockage immuable des objets blob permet aux utilisateurs de stocker dans un état de protection inviolable des informations sensibles critiques dans le cadre d’une utilisation professionnelle ou portant sur un litige, pour la durée souhaitée jusqu’à ce que la conservation prenne fin. Cette fonctionnalité n’est pas limitée aux seuls cas d’utilisation légale, mais peut également être considérée comme une conservation basée sur un événement ou un verrou de l’entreprise, lorsqu’il est nécessaire de protéger les données sur la base de déclencheurs d’événements ou d’une stratégie d’entreprise.

## <a name="regulatory-compliance"></a>Conformité aux normes

Microsoft a choisi les services de Cohasset Associates, une importante société d’évaluation indépendante spécialisée dans la gestion des dossiers et la gouvernance de l’information, afin d’évaluer le stockage immuable des objets blob et sa conformité aux exigences propres au secteur des services financiers. Cohasset a confirmé que le stockage immuable d’objets blob, lorsqu’il est utilisé pour conserver les objets blob dans un état WORM, répond aux exigences de stockage appropriées décrites par la réglementation FINRA 4511, le paragraphe 1.31(c)-(d) de la réglementation CFTC et le paragraphe 17a-4(f) de la réglementation SEC. Microsoft a opté pour cet ensemble de règles, car elles représentent les recommandations les plus normatives au monde en matière de rétention des enregistrements pour les institutions financières.

Le rapport Cohasset est disponible dans le [Centre de gestion de la confidentialité des services Microsoft](https://aka.ms/AzureWormStorage). Le [Centre de confidentialité Azure](https://www.microsoft.com/trustcenter/compliance/compliance-overview) contient des informations détaillées sur les certifications de conformité Microsoft. Pour demander une lettre d’attestation de Microsoft concernant la conformité à l’immuabilité WORM, contactez le [Support Azure](https://azure.microsoft.com/support/options/).

## <a name="immutability-policy-scope"></a>Étendue des stratégies d’immuabilité

Les stratégies d’immuabilité peuvent être étendues à une version d’objet blob (préversion) ou à un conteneur. Dans le cadre d’une stratégie d’immuabilité, le comportement d’un objet dépend de l’étendue de la stratégie. Pour plus d’informations sur l’étendue des stratégies pour chaque type de stratégie d’immuabilité, consultez les sections suivantes :

- [Etendue de la stratégie de rétention limitée dans le temps](immutable-time-based-retention-policy-overview.md#time-based-retention-policy-scope)
- [Étendue de conservation légale](immutable-legal-hold-overview.md#legal-hold-scope)

Vous pouvez configurer une stratégie de rétention basée sur le temps et une conservation légale pour une ressource (version de conteneur ou d’objet blob), en fonction de l’étendue. Le tableau suivant récapitule les stratégies d’immuabilité prises en charge pour chaque étendue des ressources :

| Étendue | Le conteneur prend en charge les stratégies d’immuabilité au niveau de la version | Le conteneur ne prend pas en charge les stratégies d’immuabilité au niveau de la version |
|--|--|--|
| Conteneur | Prend en charge une stratégie d’immuabilité par défaut au niveau de la version. La stratégie par défaut s’applique à toutes les versions créées dans le conteneur après sa configuration.<br /><br /> Ne prend pas en charge la conservation légale. | Prend en charge une stratégie d’immuabilité au niveau du conteneur et une conservation légale. Une stratégie portant sur une version de blob peut remplacer une stratégie par défaut spécifiée dans le conteneur. |
| Version de blob | Prend en charge une stratégie d’immuabilité au niveau de la version et une conservation légale. | N/A |

### <a name="about-the-preview"></a>À propos de la préversion

La préversion relative aux stratégies d’immuabilité au niveau de la version est disponible dans les régions suivantes :

- Centre du Canada
- Est du Canada
- France Centre
- France Sud

> [!IMPORTANT]
> Les stratégies d’immuabilité au niveau de la version sont actuellement en **PRÉVERSION**. Pour connaître les conditions juridiques qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou plus généralement non encore en disponibilité générale, consultez [l’Avenant aux conditions d’utilisation des préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="summary-of-immutability-scenarios"></a>Résumé des scénarios d’immuabilité

La protection offerte par une stratégie d’immuabilité dépend de l’étendue de la stratégie d’immuabilité et, dans le cas d’une stratégie de rétention basée sur le temps, de son état verrouillée ou déverrouillée et de son état actif ou expiré.

### <a name="scenarios-with-version-level-scope"></a>Scénarios avec étendue au niveau de la version

Le tableau suivant fournit un résumé des protections fournies par les stratégies d’immuabilité au niveau de la version.

| Scénario | Opérations interdites | Protection des objets blob | Protection des conteneurs | Protection des comptes |
|--|--|--|--|--|
| Une version blob est protégée par une stratégie de rétention *active* et/ou une conservation légale est en vigueur | [Delete Blob](/rest/api/storageservices/delete-blob), [Set Blob Metadata](/rest/api/storageservices/set-blob-metadata), [Put Page](/rest/api/storageservices/put-page) et [Append Block](/rest/api/storageservices/append-block)<sup>1</sup> | La version blob ne peut pas être supprimée. Les métadonnées de l’utilisateur ne peuvent pas être écrites. <br /><br /> Le remplacement d’un objet blob par [Put Blob](/rest/api/storageservices/put-blob), [Put Block List](/rest/api/storageservices/put-block-list) ou [Copy Blob](/rest/api/storageservices/copy-blob) crée un nouvelle version.<sup>2</sup> | La suppression du conteneur échoue si au moins un blob existe dans le conteneur, que la stratégie soit verrouillée ou déverrouillée. | La suppression du compte de stockage échoue s’il existe au moins un conteneur dont le stockage immuable au niveau de la version est activé. |
| Une version blob est protégée par une stratégie de rétention *expirée* et/ou une conservation légale est en vigueur | [Set Blob Metadata](/rest/api/storageservices/set-blob-metadata), [Put Page](/rest/api/storageservices/put-page) et [Append Block](/rest/api/storageservices/append-block)<sup>1</sup> | La version blob peut être supprimée. Les métadonnées de l’utilisateur ne peuvent pas être écrites. <br /><br /> Le remplacement d’un objet blob par [Put Blob](/rest/api/storageservices/put-blob), [Put Block List](/rest/api/storageservices/put-block-list) ou [Copy Blob](/rest/api/storageservices/copy-blob) crée un nouvelle version<sup>2</sup>. | La suppression du conteneur échoue si au moins un blob existe dans le conteneur, que la stratégie soit verrouillée ou déverrouillée. | La suppression du compte de stockage échoue si au moins un conteneur contient une version blob avec une stratégie de rétention basée sur le temps verrouillée.<br /><br />Les stratégies déverrouillées ne permettent pas de supprimer la protection. |

<sup>1</sup> L’opération [Append Block](/rest/api/storageservices/append-block) n’est autorisé que pour les stratégies de rétention basées sur le temps avec la propriété **allowProtectedAppendWrites** activée. Pour plus d’informations, consultez [Autoriser les écritures protégées de blobs d’ajout](immutable-time-based-retention-policy-overview.md#allow-protected-append-blobs-writes).
<sup>2</sup> Les versions d’objets blob sont toujours immuables pour le contenu. Si le contrôle de version est activé pour un compte de stockage, une opération d’écriture sur un objet blob de bloc crée une nouvelle version, à l’exception de l’opération [Put Block](/rest/api/storageservices/put-block).

### <a name="scenarios-with-container-level-scope"></a>Scénarios avec étendue au niveau du conteneur

Le tableau suivant fournit un résumé des protections fournies par les stratégies d’immuabilité au niveau du conteneur.

| Scénario | Opérations interdites | Protection des objets blob | Protection des conteneurs | Protection des comptes |
|--|--|--|--|--|
| Un conteneur est protégé par une stratégie de rétention basée sur le temps *active* avec une étendue de conteneur et/ou une conservation légale est en vigueur. | [Delete Blob](/rest/api/storageservices/delete-blob), [Put Blob](/rest/api/storageservices/put-blob)<sup>1</sup>, [Set Blob Metadata](/rest/api/storageservices/set-blob-metadata), [Put Page](/rest/api/storageservices/put-page), [Set Blob Properties](/rest/api/storageservices/set-blob-properties), [Snapshot Blob](/rest/api/storageservices/snapshot-blob), [Incremental Copy Blob](/rest/api/storageservices/incremental-copy-blob), [Append Block](/rest/api/storageservices/append-block)<sup>2</sup> | Tous les objets blob du conteneur sont immuables pour le contenu et les métadonnées utilisateur | La suppression du conteneur échoue si une stratégie au niveau du conteneur est en vigueur. | La suppression du compte de stockage échoue si un conteneur présente au moins un objet blob. |
| Un conteneur est protégé par une stratégie de rétention basée sur le temps *expirée* avec une étendue de conteneur et aucune conservation légale n’est en vigueur. | [Put Blob](/rest/api/storageservices/put-blob)<sup>1</sup>, [Set Blob Metadata](/rest/api/storageservices/set-blob-metadata), [Put Page](/rest/api/storageservices/put-page), [Set Blob Properties](/rest/api/storageservices/set-blob-properties), [Snapshot Blob](/rest/api/storageservices/snapshot-blob), [Incremental Copy Blob](/rest/api/storageservices/incremental-copy-blob), [Append Block](/rest/api/storageservices/append-block)<sup>2</sup> | Aucune opération de suppression n'est autorisée. Les opérations de remplacement ne sont pas autorisées. | La suppression du conteneur échoue si au moins un blob existe dans le conteneur, que la stratégie soit verrouillée ou déverrouillée. | La suppression du compte de stockage échoue si au moins un conteneur présente une stratégie de rétention basée sur le temps verrouillée.<br /><br />Les stratégies déverrouillées ne permettent pas de supprimer la protection. |

<sup>1</sup> Le Stockage Azure permet à l’opération [Put Blob](/rest/api/storageservices/put-blob) de créer un nouvel objet blob. Les opérations de remplacement ultérieures sur un chemin d’objets blob existant dans un conteneur immuable ne sont pas autorisées.

<sup>2</sup> L’opération [Append Block](/rest/api/storageservices/append-block) n’est autorisé que pour les stratégies de rétention basées sur le temps avec la propriété **allowProtectedAppendWrites** activée. Pour plus d’informations, consultez [Autoriser les écritures protégées de blobs d’ajout](immutable-time-based-retention-policy-overview.md#allow-protected-append-blobs-writes).

> [!NOTE]
> Certaines charges de travail, telles que [Sauvegarde SQL vers une URL](/sql/relational-databases/backup-restore/sql-server-backup-to-url), créent un blob et l’ajoutent ensuite. Si un conteneur est doté d’une stratégie active de rétention basée sur le temps ou d’une conservation légale en place, ce modèle échouera.

## <a name="supported-account-configurations"></a>Configurations de compte prises en charge

Les stratégies d’immuabilité sont prises en charge pour les comptes de stockage nouveaux et existants. Le tableau suivant répertorie les types de comptes de stockage pris en charge pour chaque type de stratégie :

| Type de stratégie d’immuabilité | Étendue de la stratégie | Types de comptes de stockage pris en charge | Prend en charge l’espace de noms hiérarchique (préversion) |
|--|--|--|--|
| Stratégie de rétention limitée dans le temps | Étendue au niveau de la version (préversion) | Usage général v2<br />Objet blob de blocs Premium | Non |
| Stratégie de rétention limitée dans le temps | Étendue au niveau du conteneur | Usage général v2<br />Objet blob de blocs Premium<br />v1 universel (héritée)<sup>1</sup><br> Stockage d’objets blob (hérité) | Oui |
| Conservation légale | Étendue au niveau de la version (préversion) | Usage général v2<br />Objet blob de blocs Premium | Non |
| Conservation légale | Étendue au niveau du conteneur | Usage général v2<br />Objet blob de blocs Premium<br />v1 universel (héritée)<sup>1</sup><br> Stockage d’objets blob (hérité) | Oui |

<sup>1</sup> Microsoft recommande de mettre à niveau les comptes v1 universels vers v2 universel pour vous permettre de tirer parti d’un plus grand nombre de fonctionnalités. Pour plus d’informations sur la mise à niveau d’un compte de stockage v1 universel, consultez [Mettre à niveau un compte de stockage](../common/storage-account-upgrade.md).

### <a name="access-tiers"></a>Niveaux d’accès

Tous les niveaux d’accès des objets blob prennent en charge le stockage immuable. Vous pouvez modifier le niveau d’accès d’un objet blob à l’aide de l’opération Set Blob Tier. Pour plus d’informations, consultez [Niveaux d’accès chaud, froid et archive pour les données d’objet blob](access-tiers-overview.md).

### <a name="redundancy-configurations"></a>Configurations de redondance

Toutes les configurations de redondance prennent en charge le stockage immuable. Pour les configurations géo-redondantes, le basculement géré par le client n’est pas pris en charge. Pour plus d’informations sur les configurations de redondance, consultez [Redondance du stockage Azure](../common/storage-redundancy.md).

### <a name="hierarchical-namespace-support"></a>Prise en charge de l’espace de noms hiérarchique

La prise en charge du stockage immuable pour les comptes avec un espace de noms hiérarchique est en préversion. Pour bénéficier de cette préversion, consultez [Fonctionnalités d’évaluation sur Azure Data Lake Storage](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR2EUNXd_ZNJCq_eDwZGaF5VUOUc3NTNQSUdOTjgzVUlVT1pDTzU4WlRKRy4u).

N’oubliez pas que vous ne pouvez pas renommer ou déplacer un objet blob lorsque cet objet blob se trouve à l’état immuable et que l’espace de noms hiérarchique est activé sur le compte. Le nom de l’objet blob et la structure de répertoires fournissent des données essentielles de niveau conteneur qui ne peuvent pas être modifiées une fois la stratégie immuable en place.

> [!IMPORTANT]
> Le stockage immuable pour le stockage blob Azure dans les comptes pour lesquels la fonctionnalité d’espace de noms hiérarchique activée est actuellement en PRÉVERSION. Pour connaître les conditions juridiques qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou plus généralement non encore en disponibilité générale, consultez [l’Avenant aux conditions d’utilisation des préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="recommended-blob-types"></a>Types d’objets blob recommandés

Microsoft vous recommande de configurer des stratégies d’immuabilité principalement pour les objets blob de blocs et les objets blob d’ajouts. La configuration d’une stratégie d’immuabilité pour un objet blob de pages stockant un disque dur virtuel pour une machine virtuelle active est déconseillée, car les écritures sur le disque sont bloquées. Avant de verrouiller des stratégies limitées dans le temps, Microsoft vous recommande de consulter attentivement la documentation et de tester vos scénarios.

## <a name="immutable-storage-with-blob-soft-delete"></a>Stockage immuable avec suppression conditionnelle d’objets blob

Lorsque la suppression conditionnelle des objets blob est configurée pour un compte de stockage, elle s’applique à tous les objets blob du compte, qu’une stratégie de rétention basée dans le temps ou conservation légale soit appliquée ou non. Microsoft recommande d’activer la suppression conditionnelle pour une protection supplémentaire avant d’appliquer des stratégies immuables.

Si vous activez la suppression conditionnelle d’objets blob, puis configurez une stratégie d’immuabilité, tous les objets blob déjà supprimés de manière conditionnelle seront définitivement supprimés une fois la stratégie de rétention de suppression conditionnelle expirée. Les objets supprimés de manière conditionnelle peuvent être restaurés au cours de la période de rétention de la suppression conditionnelle. Un objet blob (ou une version) qui n’a pas encore été supprimé de manière conditionnel est protégé par la stratégie d’immuabilité et ne peut pas être supprimé de manière conditionnelle tant que la stratégie de rétention basée sur la temps n’a pas expiré ou que la conservation légale n’a pas été supprimée.

## <a name="use-blob-inventory-to-track-immutability-policies"></a>Utiliser l’inventaire d’objets blob pour suivre les stratégies d’immuabilité

L’inventaire d’objets blob Stockage Azure offre une vue d’ensemble des conteneurs de vos comptes de stockage, ainsi que des objets blob, instantanés et versions blob qu’ils contiennent. Vous pouvez utiliser le rapport d’inventaire d’objets blob pour comprendre les attributs des objets blob et des conteneurs, notamment si une stratégie d’immuabilité est configurée pour une ressource.

Lorsque vous activez l’inventaire d’objets blob, le Stockage Azure génère quotidiennement un rapport d’inventaire. Il vous fournit une synthèse de vos données pour les besoins de l’entreprise et ses exigences de conformité.

Pour plus d’informations sur l’inventaire des objets blob, consultez [Inventaire des objets blob du Stockage Azure (préversion)](blob-inventory.md).

## <a name="pricing"></a>Tarifs

L’utilisation du stockage immuable en génère pas de frais de capacité supplémentaires. Les données immuables sont facturées de la même façon que les données modifiables classiques. Pour plus d’informations sur les prix du stockage Blob Azure, consultez la [page de tarification de Stockage Azure](https://azure.microsoft.com/pricing/details/storage/blobs/).

La création, la modification ou la suppression d’une stratégie de conservation légale ou de rétention limitée dans le temps portant sur une version blob entraîne des frais de transaction d’écriture.

Si vous ne vous acquittez pas de votre facture et que votre compte présente une stratégie de rétention basée sur le temps active, les stratégies de rétention des données classiques s’appliquent comme stipulé dans les conditions générales du contrat Microsoft. Pour obtenir des informations générales, consultez [Gestion des données chez Microsoft](https://www.microsoft.com/trust-center/privacy/data-management).

## <a name="feature-support"></a>Prise en charge des fonctionnalités

Ce tableau montre comment cette fonctionnalité est prise en charge dans votre compte ainsi que l’impact sur la prise en charge lorsque vous activez certaines fonctionnalités.

| Type de compte de stockage                | Stockage Blob (prise en charge par défaut)   | Data Lake Storage Gen2 <sup>1</sup>                        | NFS 3.0 <sup>1</sup>
|-----------------------------|---------------------------------|------------------------------------|--------------------------------------------------|
| Usage général v2 Standard | ![Oui](../media/icons/yes-icon.png) |![Oui](../media/icons/yes-icon.png)<sup>2</sup>              | ![Oui](../media/icons/yes-icon.png)<sup>2</sup> |
| Objets blob de blocs Premium          | ![Oui](../media/icons/yes-icon.png) |![Oui](../media/icons/yes-icon.png)<sup>2</sup> | ![Oui](../media/icons/yes-icon.png)<sup>2</sup> |

<sup>1</sup>    Data Lake Storage Gen2 et le protocole NFS (Network File System) 3.0 requièrent tous deux un compte de stockage avec un espace de noms hiérarchique activé.

<sup>2</sup> La fonctionnalité est prise en charge dans la préversion.

## <a name="next-steps"></a>Étapes suivantes

- [Vue d’ensemble de la protection des données](data-protection-overview.md)
- [Stratégies de rétention limitées dans le temps pour les données d’objets blob immuables](immutable-time-based-retention-policy-overview.md)
- [Conservation légale des données d’objets blob immuables](immutable-legal-hold-overview.md)
- [Configurer des stratégies d’immuabilité pour les versions d’objets blob (préversion)](immutable-policy-configure-version-scope.md)
- [Configurer des stratégies d’immuabilité pour les conteneurs](immutable-policy-configure-container-scope.md)
