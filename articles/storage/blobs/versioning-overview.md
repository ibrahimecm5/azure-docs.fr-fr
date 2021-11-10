---
title: Contrôle de version des objets blob
titleSuffix: Azure Storage
description: Le contrôle de version du stockage Blob conserve automatiquement les versions antérieures d’un objet et les identifie à l’aide d’horodateurs. Vous pouvez restaurer une version antérieure d’un blob pour récupérer vos données si celles-ci sont modifiées ou supprimées par erreur.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 05/10/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 6807423e59c5877b67a8faaf3aebe4fd60e671fd
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131424928"
---
# <a name="blob-versioning"></a>Contrôle de version des objets blob

Vous pouvez activer le contrôle de version du stockage d’objets blob pour gérer automatiquement les versions précédentes d’un objet. Lorsque le contrôle de version est activé, vous pouvez restaurer une version antérieure d’un objet blob pour récupérer vos données si celles-ci ont été modifiées ou supprimées par erreur.

## <a name="recommended-data-protection-configuration"></a>Configuration recommandée de la protection des données

Le contrôle de version des blobs fait partie d’une stratégie complète de protection des données pour les données blob. Pour une protection optimale de vos données blob, Microsoft recommande d’activer toutes les fonctionnalités de protection des données suivantes :

- Gestion des versions des objets blob, pour gérer automatiquement les versions précédentes d’un objet blob. Lorsque le contrôle de version est activé, vous pouvez restaurer une version antérieure d’un objet blob pour récupérer vos données si celles-ci ont été modifiées ou supprimées par erreur. Pour savoir comment activer le contrôle de version des blobs, consultez [Activer et gérer le contrôle de version des blobs](versioning-enable.md).
- Suppression réversible de conteneur, pour restaurer un conteneur supprimé. Pour savoir comment activer la suppression réversible de conteneur, consultez [Activer et gérer la suppression réversible pour les conteneurs](soft-delete-container-enable.md).
- Suppression réversible de blob, pour restaurer un blob, un instantané ou une version supprimés. Pour savoir comment activer la suppression réversible de blob, consultez [Activer et gérer la suppression réversible pour les blobs](soft-delete-blob-enable.md).

Pour en savoir plus sur les recommandations de Microsoft en matière de protection des données, consultez [Vue d’ensemble de la protection des données](data-protection-overview.md).

## <a name="how-blob-versioning-works"></a>Fonctionnement du contrôle de version des objets blob

La version capture l’état de l’objet blob à un moment donné. Chaque version est identifiée par un ID de version. Lorsque le contrôle de version des blobs est activé pour un compte de stockage, Stockage Azure crée automatiquement une nouvelle version avec un ID unique lorsqu’un blob est créé pour la première fois et chaque fois que le blob est modifié par la suite.

Un ID de version peut identifier la version actuelle ou une version antérieure. Un blob ne peut avoir qu’une seule version actuelle à la fois.

Lorsque vous créez un blob, une seule version existe et cette version est la version actuelle. Lorsque vous modifiez un blob existant, la version actuelle devient une version antérieure. Une nouvelle version est créée pour capturer l’état mis à jour, et cette nouvelle version est la version actuelle. Lorsque vous supprimez un objet blob, la version actuelle de l’objet blob devient la version antérieure et qu’il n’y a plus de version actuelle. Toutes les versions antérieures du blob sont conservées.

Le diagramme suivant montre la façon dont les versions sont créées lors d’opérations d’écriture et dont une version antérieure peut être promue en version actuelle :

:::image type="content" source="media/versioning-overview/blob-versioning-diagram.png" alt-text="Diagramme montrant le fonctionnement du contrôle de version des objets blob":::

Les versions d’objets blob sont immuables. Vous ne pouvez pas modifier le contenu ou les métadonnées d’une version existante de l’objet blob.

Le fait de disposer d’un grand nombre de versions par blob peut augmenter la latence des opérations de listage des blobs. Microsoft recommande de conserver moins de 1000 versions par blob. Vous pouvez utiliser la gestion de cycle de vie pour supprimer automatiquement les anciennes versions. Pour plus d’informations sur la gestion de cycle de vie, consultez [Optimiser les coûts en automatisant les niveaux d’accès de Stockage Blob Azure](./lifecycle-management-overview.md).

Le contrôle de version des blobs est disponible pour les comptes de stockage Blob hérités, les comptes d’objets blobs de blocs Premium et les comptes v2 universels Standard. Les comptes de stockage avec espace de noms hiérarchique activé pour une utilisation avec Azure Data Lake Storage Gen2 ne sont actuellement pas pris en charge.

La version 2019-10-10 et les versions ultérieures de l’API REST de stockage Azure prennent en charge le contrôle de version des objets blob.

> [!IMPORTANT]
> Le contrôle de version des objets blob ne peut pas vous aider à récupérer après la suppression accidentelle d’un compte ou d’un conteneur de stockage. Pour empêcher toute suppression accidentelle du compte de stockage, configurez un verrou sur la ressource du compte de stockage. Pour plus d’informations sur le verrouillage d’un compte de stockage, consultez [Appliquer un verrou Azure Resource Manager à un compte de stockage](../common/lock-account-resource.md).

### <a name="version-id"></a>ID de version

Chaque version du blob est identifiée par un ID de version unique. La valeur de l’ID de version est l’horodateur qui correspond au moment où le blob a été mis à jour. L’ID de version est affecté au moment de la création de la version.

Vous pouvez effectuer des opérations de lecture ou de suppression sur une version spécifique d’un objet blob en fournissant son ID de version. Si vous omettez l’ID de version, l’opération agit sur la version actuelle.

Lorsque vous appelez une opération d’écriture pour créer ou modifier un objet blob, le stockage Azure retourne l’en-tête *x-ms-version-ID* dans la réponse. Cet en-tête contient l’ID de version de la version actuelle de l’objet blob qui a été créé par l’opération d’écriture.

L’ID de version reste le même pendant toute la durée de vie de la version.

### <a name="versioning-on-write-operations"></a>Contrôle de version sur les opérations d’écriture

Lorsque le contrôle de version des objets blob est activé, chaque opération d’écriture dans un objet blob crée une nouvelle version. Les opérations d’écriture incluent [Put Blob](/rest/api/storageservices/put-blob), [Put block List](/rest/api/storageservices/put-block-list), [Copy Blob](/rest/api/storageservices/copy-blob)et [Set Blob Metadata](/rest/api/storageservices/set-blob-metadata).

Si l’opération d’écriture crée un nouvel objet blob, l’objet blob qui en résulte est la version actuelle de l’objet blob. Si l’opération d’écriture modifie un blob existant, la version actuelle devient une version antérieure et une nouvelle version actuelle est créée pour capturer le blob mis à jour.

Le diagramme suivant montre comment les opérations d’écriture affectent les versions d’objets blob. Par souci de simplicité, les diagrammes présentés dans cet article affichent l’ID de version sous forme d’une valeur entière simple. En réalité, l’ID de version est un horodateur. La version actuelle est affichée en bleu et les versions précédentes sont affichées en gris.

:::image type="content" source="media/versioning-overview/write-operations-blob-versions.png" alt-text="Diagramme montrant comment les opérations d’écriture affectent les objets blob avec contrôle de version.":::

> [!NOTE]
> Un objet blob qui a été créé avant l’activation du contrôle de version pour le compte de stockage n’a pas d’ID de version. Lorsque cet objet blob est modifié, il devient la version actuelle et une version est créée pour enregistrer l’état de l’objet blob avant sa mise à jour. La version se voit attribuer un ID de version qui correspond à son heure de création.

Lorsque le contrôle de version des blobs est activé pour un compte de stockage, toutes les opérations d’écriture sur les objets blobs de blocs déclenchent la création d’une nouvelle version, à l’exception de l’opération [Put Block](/rest/api/storageservices/put-block).

Pour les objets blobs de pages et les objets blobs d’ajout, seul un sous-ensemble d’opérations d’écriture déclenche la création d’une version. Ces opérations comprennent :

- [Put Blob](/rest/api/storageservices/put-blob)
- [Put Block List](/rest/api/storageservices/put-block-list)
- [Set Blob Metadata](/rest/api/storageservices/set-blob-metadata)
- [Copy Blob](/rest/api/storageservices/copy-blob)

Les opérations suivantes ne déclenchent pas de création d’une nouvelle version. Pour capturer les modifications de ces opérations, créez un instantané manuel :

- [Put Page](/rest/api/storageservices/put-page) (objet blob de pages)
- [Append Block](/rest/api/storageservices/append-block) (objet blob d’ajouts)

Toutes les versions d’un objet blob doivent être du même type d’objet blob. Si un blob a des versions antérieures, vous ne pouvez pas remplacer un blob d’un type par un autre type, sauf si vous supprimez d’abord le blob et toutes ses versions.

### <a name="versioning-on-delete-operations"></a>Contrôle de version sur les opérations de suppression

Lorsque vous appelez l’opération [Delete Blob](/rest/api/storageservices/delete-blob) sans spécifier un ID de version, la version actuelle devient une version antérieure et il n’y a plus de version actuelle. Toutes les versions antérieures du blob sont conservées.

Le diagramme suivant montre l’effet d’une opération de suppression sur un objet blob avec contrôle de version :

:::image type="content" source="media/versioning-overview/delete-versioned-base-blob.png" alt-text="Diagramme montrant la suppression d’un objet blob avec contrôle de version.":::

Pour supprimer la version spécifique d’un blob, indiquez l’ID de cette version sur l’opération de suppression. Si la suppression réversible des blobs est également activée pour le compte de stockage, la version est conservée dans le système jusqu’à ce que la période de rétention de la suppression réversible soit écoulée.

L’écriture de nouvelles données dans le blob crée une nouvelle version actuelle de ce blob. Les versions existantes ne sont pas affectées, comme indiqué dans le diagramme suivant.

:::image type="content" source="media/versioning-overview/recreate-deleted-base-blob.png" alt-text="Diagramme montrant la recréation d’un objet blob avec contrôle de version après suppression.":::

### <a name="access-tiers"></a>Niveaux d’accès

Vous pouvez déplacer n’importe quelle version d’un objet blob de blocs, y compris la version actuelle, vers un autre niveau d’accès en appelant l’opération [Set Blob Tier](/rest/api/storageservices/set-blob-tier). Vous pouvez tirer parti d’une tarification de capacité inférieure en déplaçant les anciennes versions d’un objet blob vers le niveau de stockage froid ou le niveau de stockage archive. Pour plus d’informations, consultez [Niveaux d’accès chaud, froid et archive pour les données d’objet blob](access-tiers-overview.md).

Pour automatiser le processus de déplacement d’objets blob de blocs vers le niveau approprié, utilisez la gestion du cycle de vie des objets blob. Pour plus d’informations sur la gestion du cycle de vie, consultez [Gérer le cycle de vie du stockage d’objets blob Azure](./lifecycle-management-overview.md).

## <a name="enable-or-disable-blob-versioning"></a>Activer/désactiver le contrôle de version des objets blob

Pour savoir comment activer le contrôle de version des objets blob, consultez [Activer et gérer le contrôle de version des objets blob](versioning-enable.md).

La désactivation du contrôle de version des objets blob ne supprime pas les objets blob, les versions ou les instantanés existants. Lorsque vous désactivez le contrôle de version des objets blob, toutes les versions existantes restent accessibles dans votre compte de stockage. Aucune nouvelle version n’est créée par la suite.

Si un objet blob a été créé ou modifié après la désactivation du contrôle de version sur le compte de stockage, le remplacement de l’objet blob crée une nouvelle version. L’objet blob mis à jour n’est plus la version actuelle et n’a pas d’ID de version. Toutes les mises à jour ultérieures de l’objet blob remplacent ses données sans enregistrer l’état précédent.

Vous pouvez lire ou supprimer des versions à l’aide de l’ID de version une fois que le contrôle de version est désactivé. Vous pouvez également répertorier les versions d’un objet blob après la désactivation du contrôle de version.

Le diagramme suivant montre comment la modification d’un objet blob après la désactivation du contrôle de version crée un objet blob dont la version n’est pas contrôlée. Toutes les versions existantes associées à l’objet blob sont conservées.

:::image type="content" source="media/versioning-overview/modify-base-blob-versioning-disabled.png" alt-text="Diagramme montrant l’objet blob de base modifié après la désactivation du contrôle de version.":::

## <a name="blob-versioning-and-soft-delete"></a>Contrôle de version des objets blob et suppression réversible

Microsoft recommande d’activer le contrôle de version et la suppression réversible d’objets blob pour vos comptes de stockage afin d’obtenir une protection optimale des données. Pour plus d’informations sur la suppression réversible d’objets blob, consultez [Suppression réversible pour les objets blob de Stockage Azure](./soft-delete-blob-overview.md).

### <a name="overwriting-a-blob"></a>Remplacement d’un blob

Si le contrôle de version des objets blob et la suppression réversible d’objets blob sont tous deux activés sur un compte de stockage, alors le remplacement d’un objet blob crée automatiquement une nouvelle version. La nouvelle version n’est pas supprimée de manière réversible et n’est pas supprimée à l’expiration de la période de rétention de la suppression réversible. Aucun instantané supprimé de manière réversible n’est créé.

### <a name="deleting-a-blob-or-version"></a>Suppression d’un objet blob ou d’une version

Si le contrôle de version et la suppression réversible sont tous deux activés pour un compte de stockage, lorsque vous supprimez un blob, la version actuelle du blob devient une version antérieure. Aucune nouvelle version n’est créée et aucun instantané supprimé de manière réversible n’est créé. La période de rétention de la suppression réversible n’est pas en vigueur pour le blob supprimé.

La suppression réversible offre une protection supplémentaire pour la suppression des versions d’objets blob. Lorsque vous supprimez une version antérieure du blob, cette version est supprimée de manière réversible. La version supprimée de manière réversible est conservée jusqu’à ce que la période de rétention de la suppression réversible soit écoulée, après quoi elle est définitivement supprimée.

Pour supprimer une version antérieure d’un blob, appelez l’opération **Delete Blob** et spécifiez l’ID de version.

Le diagramme suivant montre ce qui se passe lorsque vous supprimez un objet blob ou une version d’un objet blob.

:::image type="content" source="media/versioning-overview/soft-delete-historical-version.png" alt-text="Diagramme montrant la suppression d’une version avec suppression réversible activée.":::

### <a name="restoring-a-soft-deleted-version"></a>Restauration d’une version supprimée de manière réversible

Vous pouvez utiliser l’opération [Annuler la suppression d’un objet blob](/rest/api/storageservices/undelete-blob) pour restaurer des versions supprimées pendant la période de rétention de la suppression réversible. L’opération **Annuler la suppression d’un objet blob** restaure toujours toutes les versions supprimées de manière réversible de l’objet blob. Il n’est pas possible de restaurer une seule version supprimée de manière réversible.

La restauration de versions supprimées de manière réversible avec l’opération **Undelete Blob** ne promeut aucune version à la version actuelle. Pour restaurer la version actuelle, restaurez tout d’abord toutes les versions supprimées de manière réversible, puis utilisez l’opération [Copy Blob](/rest/api/storageservices/copy-blob) pour copier une version antérieure vers une nouvelle version actuelle.

Le diagramme suivant montre comment restaurer des versions d’objets blob supprimés de manière réversible avec l’opération **Undelete Blob** et comment restaurer la version actuelle de l’objet blob avec l’opération **Copy Blob**.

:::image type="content" source="media/versioning-overview/undelete-version.png" alt-text="Diagramme montrant comment restaurer des versions supprimées de manière réversible.":::

Une fois la période de rétention de la suppression réversible terminée, toutes les versions des objets blob supprimés de manière réversible sont définitivement supprimées.

## <a name="blob-versioning-and-blob-snapshots"></a>Contrôle de version des objets blob et instantanés d’objets blob

L’instantané d’objet blob est une version en lecture seule d’un objet blob capturée à un instant donné. Les instantanés d’objets blob et les versions d’objets blob sont similaires, mais les instantanés sont créés manuellement par vous ou par votre application, tandis que les versions d’objet blob sont créées automatiquement lors des opérations d’écriture ou de suppression lorsque le contrôle de version des objets blob est activé sur votre compte de stockage.

> [!IMPORTANT]
> Microsoft recommande qu’une fois activé le contrôle de version des objets blob, vous mettiez également à jour votre application pour cesser de prendre des instantanés d’objets blob de blocs. Si le contrôle de version est activé sur votre compte de stockage, toutes les mises à jour et suppressions d’objets blob de blocs sont capturées et conservées par les versions. La création d’instantanés n’offre aucune protection supplémentaire à vos données d’objets blob de blocs si le contrôle de version des objets blob est activé et peut augmenter les coûts et accroître la complexité des applications.

### <a name="snapshot-a-blob-when-versioning-is-enabled"></a>Instantané d’un objet blob lorsque le contrôle de version est activé

Bien que cela ne soit pas recommandé, vous pouvez créer un instantané d’un objet blob avec contrôle de version. Si vous ne pouvez pas mettre à jour votre application pour arrêter de créer des instantanés d’objets blob lorsque vous activez le contrôle de version, votre application peut prendre en charge les instantanés et les versions.

Lorsque vous créez un instantané d’un objet blob avec contrôle de version, une nouvelle version est créée au moment de la création de l’instantané. Une nouvelle version actuelle est également créée dès lors qu’un instantané est créé.

Le diagramme suivant montre ce qui se passe lorsque vous créez un instantané d’un objet blob avec contrôle de version. Dans le diagramme, les versions des objets blob et des instantanés avec l’ID de version 2 et 3 contiennent les mêmes données.

:::image type="content" source="media/versioning-overview/snapshot-versioned-blob.png" alt-text="Diagramme montrant des captures instantanées d’un objet blob avec contrôle de version.":::

## <a name="authorize-operations-on-blob-versions"></a>Autoriser des opérations sur des versions d’objets blob

Vous pouvez autoriser l’accès aux versions de l’objet blob à l’aide de l’une des approches suivantes :

- En utilisant le contrôle d’accès en fonction du rôle (Azure RBAC) Azure pour accorder des autorisations à un principal de sécurité Azure Active Directory (Azure AD). Microsoft recommande d’utiliser Azure AD pour une meilleure sécurité et une plus grande facilité d’utilisation. Pour plus d’informations sur l’utilisation d’Azure AD avec les opérations blob, consultez [Autoriser l’accès aux données dans le stockage Azure](../common/authorize-data-access.md).
- En utilisant une signature d’accès partagé (SAP) pour déléguer l’accès aux versions de l’objet blob. Spécifiez l’ID de version pour le type de ressource signé `bv`, qui représente une version d’objet blob, pour créer un jeton SAP pour les opérations sur une version spécifique. Pour plus d’informations sur les signatures d’accès partagé, consultez [Accorder un accès limité aux ressources du Stockage Azure à l’aide des signatures d’accès partagé (SAP)](../common/storage-sas-overview.md).
- En utilisant les clés d’accès au compte pour autoriser les opérations sur les versions de l’objet blob avec une clé partagée. Pour plus d’informations, consultez [Autoriser avec une clé partagée](/rest/api/storageservices/authorize-with-shared-key).

Le contrôle de version des objets blob est conçu pour protéger vos données contre toute suppression accidentelle ou malveillante. Pour améliorer la protection, la suppression d’une version d’objet blob nécessite des autorisations spéciales. Les sections suivantes décrivent les autorisations nécessaires pour supprimer une version d’objet blob.

### <a name="azure-rbac-action-to-delete-a-blob-version"></a>Action RBAC Azure pour supprimer une version d’objet blob

Le tableau suivant indique les actions de contrôle d’accès en fonction du rôle (RBAC) Azure qui prennent en charge la suppression d’un objet blob ou d’une version d’objet blob.

| Description | Opération de service d’objet blob | Action obligatoire sur les données RBAC | Prise en charge de rôle intégré Azure |
|----------------------------------------------|------------------------|---------------------------------------------------------------------------------------|-------------------------------|
| Suppression de la version actuelle | Delete Blob | **Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete** | Contributeur aux données Blob du stockage |
| Suppression d’une version précédente | Delete Blob | **Microsoft.Storage/storageAccounts/blobServices/containers/blobs/deleteBlobVersion/action** | Propriétaire des données Blob du stockage |

### <a name="shared-access-signature-sas-parameters"></a>Paramètres de la signature d’accès partagé (SAP)

La ressource signée pour une version d’objet blob est `bv`. Pour en savoir plus, consultez [Créer une SAP de service](/rest/api/storageservices/create-service-sas) ou [Créer une SAP de délégation d’utilisateur](/rest/api/storageservices/create-user-delegation-sas).

Le tableau suivant présente l’autorisation requise sur une SAP pour supprimer une version d’objet blob.

| **Permission** | **Symbole d’URI** | **Opérations autorisées** |
|----------------|----------------|------------------------|
| DELETE         | x              | Supprimez une version d’objet blob. |

## <a name="pricing-and-billing"></a>Tarification et facturation

L’activation du contrôle de version des objets blob peut entraîner des frais de stockage de données supplémentaires pour votre compte. Lors de la conception de votre application, il est important de savoir comment ces frais peuvent s’accumuler pour pouvoir réduire les coûts.

Les versions d’objets blob, comme celles des instantanés d’objets blob, sont facturées au même tarif que les données actives. La façon dont les versions sont facturées varie selon que vous avez défini explicitement le niveau pour l’objet blob de base ou pour l’une de ses versions (ou captures instantanées). Pour plus d’informations sur les niveaux des objets blob, consultez [Niveaux d’accès chaud, froid et archive pour les données d’objet blob](access-tiers-overview.md).

Si vous n’avez pas modifié le niveau d’un objet blob ou d’une version, vous êtes facturé pour des blocs de données uniques sur cet objet blob, ses versions et ses éventuelles captures instantanées. Pour plus d’informations, consultez [Facturation quand le niveau d’objet blob n’a pas été défini explicitement](#billing-when-the-blob-tier-has-not-been-explicitly-set).

Si vous avez modifié le niveau d’un objet blob ou d’une version, vous êtes facturé pour l’intégralité de l’objet, que l’objet blob et sa version se retrouvent ou non dans le même niveau. Pour plus d’informations, consultez [Facturation quand le niveau de l’objet blob a été défini explicitement](#billing-when-the-blob-tier-has-been-explicitly-set).

> [!NOTE]
> L’activation du contrôle de version sur des données fréquemment remplacées peut entraîner une augmentation des frais en termes de capacité de stockage, ainsi qu’une latence plus élevée lors des opérations de listes. Vous pouvez limiter ces problèmes en stockant les données fréquemment remplacées dans un compte de stockage distinct avec contrôle de version désactivé.

Pour plus d’informations sur les détails de facturation des captures instantanées d’objets blob, consultez [Captures instantanées d’objets blob](snapshots-overview.md).

### <a name="billing-when-the-blob-tier-has-not-been-explicitly-set"></a>Facturation quand le niveau de l’objet blob n’a pas été défini explicitement

Si vous n’avez pas défini explicitement le niveau d’objet blob pour un objet blob de base ou l’une de ses versions, vous êtes facturé pour les blocs ou pages uniques de l’objet blob, ses versions et ses éventuelles captures instantanées. Les données partagées dans un objet blob et ses versions ne sont facturées qu’une seule fois. Lors de la mise à jour d’un objet blob, les données dans l’objet blob de base divergent des données stockées dans ses versions, et les données uniques sont facturées par bloc ou page.

Quand vous remplacez un bloc au sein d’un objet blob de bloc, ce bloc est ensuite facturé comme un bloc unique. Cela est vrai même si le bloc a le même ID et les mêmes données que dans la version précédentes. Une fois le bloc revalidé, il diffère de son homologue dans la version précédente et vous êtes facturé pour ses données. Il en va de même pour une page dans un objet blob de pages qui est mise à jour avec des données identiques.

Le stockage d’objets blob ne dispose d’aucun moyen pour déterminer si deux blocs contiennent des données identiques. Chaque bloc qui est téléchargé et validé est traité comme étant unique, même s’il a les mêmes données et le même ID de bloc. Les blocs uniques accumulant les frais, il est important de garder à l’esprit que la mise à jour d’un objet blob lorsque le contrôle de version est activé générera des blocs uniques supplémentaires, et donc une augmentation des frais.

Lorsque le contrôle de version d’objet blob est activé, appelez des opérations de mise à jour des objets blob de blocs de façon à ce qu’elles mettent à jour le plus petit nombre possible de blocs. Les opérations d’écriture qui permettent un contrôle plus précis des blocs sont [Put Block](/rest/api/storageservices/put-block) et [Put Block List](/rest/api/storageservices/put-block-list). L’opération [Put Blob](/rest/api/storageservices/put-blob), en revanche, remplace la totalité du contenu d’un objet blob et peut entraîner des frais supplémentaires.

Les scénarios suivants montrent comment les frais s’accumulent pour un objet blob de blocs et ses versions lorsque le niveau d’objet blob n’a pas été défini explicitement.

#### <a name="scenario-1"></a>Scénario 1

Dans le scénario 1, l’objet blob a une version antérieure. L’objet blob de base n’a pas été mis à jour depuis la création de la version, les frais sont donc calculés uniquement pour les blocs uniques 1, 2 et 3.

![Diagramme 1 montrant la facturation des blocs uniques dans l’objet blob de base et la version précédente.](./media/versioning-overview/versions-billing-scenario-1.png)

#### <a name="scenario-2"></a>Scénario 2

Dans le scénario 2, un bloc (le bloc 3 dans le diagramme) de l’objet blob a été mis à jour. Même si le bloc mis à jour contient les mêmes données et le même ID, il est différent du bloc 3 de la version précédente. Par conséquent, des frais pour quatre blocs sont facturés au compte.

![Diagramme 2 montrant la facturation des blocs uniques dans l’objet blob de base et la version précédente.](./media/versioning-overview/versions-billing-scenario-2.png)

#### <a name="scenario-3"></a>Scénario 3

Dans le scénario 3, l’objet blob a été mis à jour, mais pas la version. Le bloc 3 a été remplacé par le bloc 4 dans l’objet blob de base, mais la version précédente reflète toujours le bloc 3. Par conséquent, des frais pour quatre blocs sont facturés au compte.

![Diagramme 3 montrant la facturation des blocs uniques dans l’objet blob de base et la version précédente.](./media/versioning-overview/versions-billing-scenario-3.png)

#### <a name="scenario-4"></a>Scénario 4

Dans le scénario 4, l'objet blob de base a été complètement mis à jour et ne contient aucun de ses blocs d'origine. Par conséquent, le compte est facturé pour les huit blocs uniques : quatre dans l’objet blob de base et quatre dans la version précédente. Ce scénario peut se produire si vous écrivez dans un objet blob avec l’opération [Put Blob](/rest/api/storageservices/put-blob), car elle remplace tout le contenu de l’objet blob de base.

![Diagramme 4 montrant la facturation des blocs uniques dans l’objet blob de base et la version précédente.](./media/versioning-overview/versions-billing-scenario-4.png)

### <a name="billing-when-the-blob-tier-has-been-explicitly-set"></a>Facturation quand le niveau de l’objet blob a été défini explicitement

Si vous avez défini explicitement le niveau d’objet blob pour un objet blob ou une version (ou une capture instantanée), vous êtes facturé pour la longueur totale du contenu de l’objet dans le nouveau niveau, qu’il partage ou non des blocs avec un objet dans le niveau d’origine. Vous êtes également facturé pour la longueur totale du contenu de la version la plus ancienne dans le niveau d’origine. Les autres versions ou captures instantanées précédentes qui restent dans le niveau d’origine sont facturées pour les blocs uniques qu’elles peuvent partager, comme décrit dans [Facturation quand le niveau de l’objet blob n’a pas été défini explicitement](#billing-when-the-blob-tier-has-not-been-explicitly-set).

#### <a name="moving-a-blob-to-a-new-tier"></a>Déplacement d’un objet blob vers un nouveau niveau

Le tableau suivant décrit le comportement de facturation d’un objet blob ou d’une version lors de leur déplacement vers un nouveau niveau.

| Quand le niveau d’objet blob est défini explicitement sur… | Vous êtes facturé pour... |
|-|-|
| Un objet blob de base avec une version précédente | L’objet blob de base dans le nouveau niveau et la version la plus ancienne dans le niveau d’origine, ainsi que tous les blocs uniques dans d’autres versions.<sup>1</sup> |
| Un objet blob de base avec une version précédente et une capture instantanée | L’objet blob de base dans le nouveau niveau, la version la plus ancienne dans le niveau d’origine et la capture instantanée la plus ancienne dans le niveau d’origine, ainsi que tous les blocs uniques dans d’autres versions ou captures instantanées<sup>1</sup>. |
| Une version précédente | La version dans le nouveau niveau et l’objet blob de base dans le niveau d’origine, ainsi que tous les blocs uniques dans d’autres versions.<sup>1</sup> |

<sup>1</sup>s’il existe d’autres versions ou captures instantanées précédentes qui n’ont pas été déplacées à partir de leur niveau d’origine, ces versions ou captures instantanées sont facturées en fonction du nombre de blocs uniques qu’elles contiennent, comme décrit dans [Facturation quand le niveau de l’objet blob n’a pas été défini explicitement](#billing-when-the-blob-tier-has-not-been-explicitly-set).

Le diagramme suivant illustre la façon dont les objets sont facturés quand un objet blob avec contrôle de version est déplacé vers un autre niveau.

:::image type="content" source="media/versioning-overview/versioning-billing-tiers.png" alt-text="Diagramme montrant comment les objets sont facturés quand un objet blob avec contrôle version est explicitement hiérarchisé.":::

La définition explicite du niveau pour un objet blob, une version ou une capture instantanée ne peut pas être annulée. Si vous déplacez un objet blob vers un nouveau niveau, puis le replacez à son niveau d’origine, vous êtes facturé pour la longueur totale du contenu de l’objet, même s’il partage des blocs avec d’autres objets dans le niveau d’origine.

Les opérations qui définissent explicitement le niveau d’un objet blob, d’une version ou d’une capture instantanée sont les suivantes :

- [Set Blob Tier](/rest/api/storageservices/set-blob-tier)
- [Placer l’objet blob](/rest/api/storageservices/put-blob) avec le niveau spécifié
- [Placer la liste de blocs](/rest/api/storageservices/put-block-list) avec le niveau spécifié
- [Copier l’objet blob](/rest/api/storageservices/copy-blob) avec le niveau spécifié

#### <a name="deleting-a-blob-when-soft-delete-is-enabled"></a>Suppression d’un objet blob quand la suppression réversible est activée

Lorsque la suppression réversible d’objet blob est activée, si vous supprimez ou remplacez un objet blob de base dont le niveau est explicitement défini, toutes les versions précédentes de l’objet blob supprimé de manière réversible sont facturées pour la longueur totale du contenu. Pour plus d’informations sur la manière dont le contrôle de version d’objet blob et de la suppression réversible fonctionnent ensemble, consultez [Contrôle de version des objets blob et suppression réversible](#blob-versioning-and-soft-delete).

Le tableau suivant décrit le comportement de facturation d’un objet blob supprimé de manière réversible, selon que le contrôle de version est activé ou désactivé. Quand le contrôle de version est activé, une version est créée lors de la suppression d’un objet blob de manière réversible. Quand le contrôle de version est désactivé, la suppression réversible d’un objet blob crée une capture instantanée de suppression réversible.

| Quand vous remplacez un objet blob de base par son niveau explicitement défini… | Vous êtes facturé pour... |
|-|-|
| Si la suppression réversible et le contrôle de version d’objet blob sont tous deux activés | Toutes les versions existantes pour la longueur totale du contenu, quel que soit le niveau. |
| Si la suppression réversible d’objet blob est activée alors que le contrôle de version est désactivé | Tous les captures instantanées de suppression réversible existantes pour la longueur totale du contenu, quel que soit le niveau. |

## <a name="feature-support"></a>Prise en charge des fonctionnalités

Ce tableau montre comment cette fonctionnalité est prise en charge dans votre compte ainsi que l’impact sur la prise en charge lorsque vous activez certaines fonctionnalités.

| Type de compte de stockage                | Stockage Blob (prise en charge par défaut)   | Data Lake Storage Gen2 <sup>1</sup>                        | NFS 3.0 <sup>1</sup>
|-----------------------------|---------------------------------|------------------------------------|--------------------------------------------------|
| Usage général v2 Standard | ![Oui](../media/icons/yes-icon.png) |![Non](../media/icons/no-icon.png)              | ![Non](../media/icons/no-icon.png) |
| Objets blob de blocs Premium          | ![Oui](../media/icons/yes-icon.png) |![Non](../media/icons/no-icon.png)              | ![Non](../media/icons/no-icon.png) |

<sup>1</sup>    Data Lake Storage Gen2 et le protocole NFS (Network File System) 3.0 requièrent tous deux un compte de stockage avec un espace de noms hiérarchique activé.

## <a name="see-also"></a>Voir aussi

- [Activer et gérer le contrôle de version des objets blob](versioning-enable.md)
- [Création d’un instantané d’objet blob](/rest/api/storageservices/creating-a-snapshot-of-a-blob)
- [Suppression réversible pour Azure Storage Blob](./soft-delete-blob-overview.md)
