---
title: Vue d'ensemble de la réplication d'objets
titleSuffix: Azure Storage
description: La réplication d'objets copie de manière asynchrone des objets blob de blocs entre un compte de stockage source et un compte de destination. Utilisez la réplication d’objets pour réduire la latence sur les demandes de lecture, pour renforcer l’efficacité des charges de travail de calcul, pour optimiser la distribution des données et pour réduire les coûts.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 09/02/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 1c3f90087c2e016cdfd160c98df28cf3da25ca28
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131471950"
---
# <a name="object-replication-for-block-blobs"></a>Réplication d'objets blob de blocs

La réplication d'objets copie de manière asynchrone des objets blob de blocs entre un compte de stockage source et un compte de destination. Voici quelques scénarios pris en charge par la réplication d’objets :

- **Minimisation de la latence.** La réplication d’objets peut réduire la latence des demandes de lecture en permettant aux clients d’utiliser des données provenant d’une région plus proche en termes de proximité physique.
- **Augmentation de l’efficacité des charges de travail de calcul.** Avec la réplication d’objets, les charges de travail de calcul peuvent traiter les mêmes ensembles d’objets blob de blocs dans différentes régions.
- **Optimisation de la distribution des données.** Vous pouvez traiter ou analyser des données incluses dans un emplacement unique, puis répliquer uniquement les résultats dans d’autres régions.
- **Optimisation des coûts.** Une fois vos données répliquées, vous pouvez réduire les coûts en les déplaçant vers le niveau archive à l’aide des stratégies de gestion du cycle de vie.

Le diagramme suivant montre comment la réplication d’objets réplique les objets blob de blocs d’un compte de stockage source dans une région vers des comptes de destination dans deux régions différentes.

:::image type="content" source="media/object-replication-overview/object-replication-diagram.svg" alt-text="Diagramme montrant le fonctionnement de la réplication d’objets":::

Pour plus d'informations sur la configuration de la réplication d'objets, consultez [Configurer la réplication d'objets](object-replication-configure.md).

## <a name="prerequisites-for-object-replication"></a>Prérequis pour la réplication d’objets

La réplication d'objets exige que les fonctionnalités de stockage Azure suivantes soient également activées :

- [Flux de modification](storage-blob-change-feed.md) : doit être activé sur le compte source. Pour plus d'informations sur l'activation du flux de modifications, consultez [Activer et désactiver le flux de modifications](storage-blob-change-feed.md#enable-and-disable-the-change-feed).
- [Contrôle de version des objets blob](versioning-overview.md) : doit être activé sur les comptes source et de destination. Pour plus d'informations sur l'activation du contrôle de version des objets blob, consultez [Activer et gérer le contrôle de version des objets blob](versioning-enable.md).

L’activation de ces fonctionnalités peut entraîner des coûts supplémentaires. Pour plus d’informations, consultez la [page des tarifs du stockage Azure](https://azure.microsoft.com/pricing/details/storage/).

La réplication d’objet est prise en charge pour les comptes de stockage v2 universels et les comptes de stockage d’objets blob de blocs Premium. Les comptes source et de destination doivent être des comptes à usage général v2 ou des comptes d’objet blob de blocs Premium. La réplication d’objets prend en charge uniquement les objets blob de blocs. Les objets blob d’ajouts et les objets blob de pages ne sont pas pris en charge.

> [!IMPORTANT]
> La réplication d’objets pour les comptes d’objets Blob de blocs Premium est actuellement en version **PRÉLIMINAIRE**. Pour connaître les conditions juridiques qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou plus généralement non encore en disponibilité générale, consultez [l’Avenant aux conditions d’utilisation des préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="how-object-replication-works"></a>Fonctionnement de la réplication d’objets

La réplication d’objets copie de façon asynchrone les objets blob de blocs dans un conteneur selon les règles que vous configurez. Le contenu de l’objet blob, toutes les versions associées à l’objet blob, ainsi que les métadonnées et propriétés de l’objet blob sont copiés du conteneur source vers le conteneur de destination.

> [!IMPORTANT]
> Étant donné que les données d’objets blob de blocs sont répliquées de façon asynchrone, le compte source et le compte de destination ne sont pas immédiatement synchronisés. Il n’existe actuellement aucun contrat de niveau de service (SLA) sur le temps nécessaire à la réplication des données vers le compte de destination. Vous pouvez vérifier l’état de réplication sur l’objet blob source afin de déterminer si la réplication est terminée. Pour plus d’informations, consultez [Vérifier l’état de réplication d’un objet blob](object-replication-configure.md#check-the-replication-status-of-a-blob).

### <a name="blob-versioning"></a>Contrôle de version des objets blob

La réplication d’objets implique que le contrôle de version des objets blob soit activé sur les comptes source et de destination. Lorsqu’un objet blob répliqué dans le compte source est modifié, une nouvelle version de l’objet blob est créée dans le compte source et reflète l’état précédent de l’objet blob, avant modification. La version actuelle du compte source reflète les mises à jour les plus récentes. La version actuelle ainsi que toutes les versions précédentes sont répliquées vers le compte de destination. Pour plus d’informations sur la façon dont les opérations d’écriture affectent les versions d’objets blob, consultez [Contrôle de version sur les opérations d’écriture](versioning-overview.md#versioning-on-write-operations).

Lorsqu’un blob du compte source est supprimé, la version actuelle du blob devient une version antérieure, et il n’y a plus de version actuelle. Toutes les versions antérieures du blob sont conservées. Cet état est répliqué dans le compte de destination. Pour plus d’informations sur la façon dont les opérations de suppression affectent les versions d’objets blob, consultez [Contrôle de version sur les opérations de suppression](versioning-overview.md#versioning-on-delete-operations).

### <a name="snapshots"></a>Instantanés

La réplication d’objets ne prend pas en charge les instantanés d’objet blob. Les instantanés d’un objet blob du compte source ne sont pas répliqués vers le compte de destination.

### <a name="blob-tiering"></a>Hiérarchisation des objets blob

La réplication d’objets est prise en charge lorsque les comptes source et de destination se trouvent au niveau chaud ou froid. Les comptes source et de destination peuvent se trouver dans des niveaux différents. Toutefois, la réplication d’objets échoue si un objet blob du compte source ou de destination a été déplacé vers le niveau archive. Pour plus d’informations sur les niveaux des blobs, consultez [Niveaux d’accès chaud, froid et archive pour les données blob](access-tiers-overview.md).

### <a name="immutable-blobs"></a>Objets blob immuables

Les stratégies d’immuabilité comprennent des stratégies de rétention limitées dans le temps et la conservation légale. Lorsqu’une stratégie d’immuabilité est en vigueur sur le compte de destination, la réplication d’objets peut être affectée. Pour plus d’informations sur les stratégies d’immuabilité relatives au stockage d’objets blob, consultez [Stocker des données blob critiques pour l’entreprise avec un stockage immuable](immutable-storage-overview.md).

Si une stratégie d’immuabilité au niveau du conteneur est en vigueur pour un conteneur du compte de destination et qu’un objet du conteneur source est mis à jour ou supprimé, l’opération sur le conteneur source peut réussir, mais la réplication de cette opération vers le conteneur de destination échouera. Pour plus d’informations sur les opérations interdites avec une stratégie d’immuabilité limitée à un conteneur, consultez [Scénarios avec une portée au niveau du conteneur](immutable-storage-overview.md#scenarios-with-container-level-scope).

Si une stratégie d'immutabilité au niveau de la version est en vigueur pour une version de blob dans le compte de destination, et qu'une opération de suppression ou de mise à jour est effectuée sur la version de blob dans le conteneur source, l'opération sur l'objet source peut réussir, mais la réplication de cette opération sur l'objet de destination échouera. Pour plus d'informations sur les opérations interdites par une politique d'immuabilité appliquée à un conteneur, voir [Scénarios avec une portée de niveau version](immutable-storage-overview.md#scenarios-with-version-level-scope).

## <a name="object-replication-policies-and-rules"></a>Stratégies et règles de réplication d’objets

Lorsque vous configurez la réplication d’objets, vous créez une stratégie de réplication qui spécifie le compte de stockage source et le compte de destination. Une stratégie de réplication comprend une ou plusieurs règles qui spécifient un conteneur source et un conteneur de destination et indiquent quels objets blob de blocs du conteneur source seront répliqués.

Une fois que vous avez configuré la réplication d’objets, Stockage Azure vérifie régulièrement le flux de modification du compte source et réplique de manière asynchrone toutes les opérations d’écriture ou de suppression dans le compte de destination. La latence de la réplication dépend de la taille de l’objet blob de blocs en cours de réplication.

### <a name="replication-policies"></a>Stratégies de réplication

Lorsque vous configurez une réplication d’objet, vous créez une stratégie de réplication sur le compte de destination via le fournisseur de ressources de Stockage Azure. Une fois la stratégie de réplication créée, le service Stockage Azure lui attribue un ID de stratégie. Vous devez ensuite associer cette stratégie de réplication au compte source à l’aide de l’ID de stratégie. Pour que la réplication ait lieu, l’ID de stratégie doit être le même sur les comptes source et de destination.

Un compte source peut être répliqué sur deux comptes de destination maximum, avec une stratégie pour chaque compte de destination. De même, un compte peut faire office de compte de destination pour au moins deux stratégies de réplication.

Les comptes source et de destination peuvent se trouver dans la même région ou dans des régions différentes. Ils peuvent également résider dans le même abonnement ou dans des abonnements différents. Si vous le souhaitez, les comptes source et de destination peuvent se trouver dans différents locataires du répertoire actif Azure (Azure AD). Une seule stratégie de réplication peut être créée pour chaque paire compte source/compte de destination.

### <a name="replication-rules"></a>Règles de réplication

Les règles de réplication spécifient comment Stockage Azure va répliquer les objets blob d’un conteneur source vers un conteneur de destination. Vous pouvez spécifier jusqu’à 10 règles de réplication pour chaque stratégie de réplication. Chaque règle de réplication définit un seul conteneur source et de destination, et chaque conteneur source et de destination ne peut être utilisé que dans une seule règle, ce qui signifie qu’un maximum de 10 conteneurs source et 10 conteneurs de destination peuvent être inclus dans une même stratégie de réplication.

Quand vous créez une règle de réplication, par défaut, seuls les nouveaux objets blob de blocs ajoutés par la suite au conteneur source sont copiés. Vous pouvez spécifier que les objets blob de blocs, qu'ils soient nouveaux et existants, soient copiés, ou bien vous pouvez définir une étendue de copie personnalisée qui copie les objets blob de blocs créés à partir d'un moment donné.

Vous pouvez aussi spécifier un ou plusieurs filtres dans le cadre d’une règle de réplication pour filtrer les objets blob de blocs par préfixe. Lorsque vous spécifiez un préfixe, seuls les objets blob correspondant à ce préfixe dans le conteneur source sont copiés dans le conteneur de destination.

Les conteneurs source et de destination doivent tous les deux exister pour que vous puissiez les spécifier dans une règle. Une fois que vous avez créé la stratégie de réplication, les opérations d’écriture vers le conteneur de destination ne sont pas autorisées. Toute tentative d’écriture dans le conteneur de destination échoue avec le code d’erreur 409 (Conflit). Pour écrire dans un conteneur de destination pour lequel une règle de réplication est configurée, vous devez soit supprimer la règle configurée pour ce conteneur, soit supprimer la stratégie de réplication. Les opérations de lecture et de suppression sur le conteneur de destination sont autorisées lorsque la stratégie de réplication est active.

Vous pouvez appeler l’opération [Définir le niveau du blob](/rest/api/storageservices/set-blob-tier) sur un blob dans le conteneur de destination pour le déplacer vers le niveau archive. Pour plus d’informations sur le niveau archive, consultez [Niveaux d’accès chaud, froid et archive pour les données blob](access-tiers-overview.md#archive-access-tier).

## <a name="policy-definition-file"></a>Fichier de définition de la stratégie

Une stratégie de réplication d’objet est définie par le fichier JSON. Vous pouvez récupérer le fichier de définition de la stratégie à partir d’une stratégie de réplication d’objet existante. Vous pouvez également créer une stratégie de réplication d’objet en téléchargeant un fichier de définition de la stratégie.

### <a name="sample-policy-definition-file"></a>Exemple de fichier de définition de la stratégie

L’exemple suivant définit une stratégie de réplication sur le compte de destination avec une règle unique correspondant au préfixe *b*, et définit l’heure de création minimale des objets blob à répliquer. N’oubliez pas de remplacer les valeurs entre crochets par vos propres valeurs :

```json
{
  "properties": {
    "policyId": "default",
    "sourceAccount": "/subscriptions/<subscriptionId>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>",
    "destinationAccount": "/subscriptions/<subscriptionId>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>",
    "rules": [
      {
        "ruleId": "",
        "sourceContainer": "<source-container>",
        "destinationContainer": "<destination-container>",
        "filters": {
          "prefixMatch": [
            "b"
          ],
          "minCreationTime": "2021-08-028T00:00:00Z"
        }
      }
    ]
  }
}
```

### <a name="specify-full-resource-ids-for-the-source-and-destination-accounts"></a>Spécifiez des ID de ressources complets pour les comptes source et de destination

Lorsque vous créez le fichier de définition de la stratégie, spécifiez les ID de ressource complets du Gestionnaire de ressource Azure pour les entrées **sourceAccount** et **destinationAccount**, comme indiqué dans l’exemple de la section précédente. Pour savoir comment localiser l’ID de ressource pour un compte de stockage, consultez [Obtenir l’ID de ressource pour un compte de stockage](../common/storage-account-get-info.md#get-the-resource-id-for-a-storage-account).

L’ID de ressource complet est au format suivant :

```http
/subscriptions/<subscriptionId>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
```

Le fichier de définition de la stratégie ne nécessitait précédemment que le nom du compte, au lieu de l’ID de ressource complet du compte de stockage. Avec l’introduction de la propriété de sécurité **AllowCrossTenantReplication** dans la version du 02/01/2021 de l’API REST du fournisseur de ressource du Stockage Azure, vous devez maintenant fournir l’ID de ressource complet pour toutes les stratégies de réplication d’objet créées lorsque la réplication entre locataires est interdite pour un compte de stockage qui participe à la stratégie de réplication. Le Stockage Azure utilise l’ID de ressource complet pour vérifier si les comptes source et de destination se trouvent dans le même locataire. Pour en savoir plus sur l’interdiction des stratégies de réplication entre locataires, consultez [Empêcher la réplication entre les locataires Azure AD](#prevent-replication-across-azure-ad-tenants).

Si le nom du compte est toujours pris en charge lorsque la réplication entre locataires est autorisée pour un compte de stockage, Microsoft recommande de toujours fournir l’ID de ressource complet comme meilleure pratique. Toutes les versions précédentes de l’API REST du fournisseur de ressource du Stockage Azure prennent en charge l’utilisation du chemin d’accès complet de l’ID de ressource dans les stratégies de réplication d’objets.

Le tableau suivant décrit ce qui se produit lorsque vous créez une stratégie de réplication avec l’ID de ressource complet spécifié, plutôt que le nom du compte, dans les scénarios où la réplication entre locataires est autorisée ou interdite pour le compte de stockage.

| Identificateur de compte de Stockage dans la définition de la stratégie | Réplication entre locataires autorisée | Réplication entre locataires interdite |
|--|--|--|
| ID de ressource complet | Les mêmes stratégies de locataire peuvent être créées.<br /><br /> Les stratégies entre locataires peuvent être créées. | Les mêmes stratégies de locataire peuvent être créées.<br /><br /> Les stratégies entre locataires ne peuvent pas être créées. |
| Nom du compte uniquement | Les mêmes stratégies de locataire peuvent être créées.<br /><br /> Les stratégies entre locataires peuvent être créées. | Vous ne pouvez pas créer de stratégies de locataires identiques ou entre locataires. Une erreur se produit, car le Stockage Azure ne peut pas vérifier que les comptes source et de destination se trouvent dans le même locataire. L’erreur indique que vous devez spécifier l’ID de ressource complet pour les entrées **sourceAccount** et **destinationAccount** dans le fichier de définition de la stratégie. |

### <a name="specify-the-policy-and-rule-ids"></a>Spécifier les ID de stratégie et de règle

Le tableau suivant récapitule les valeurs à utiliser pour les entrées **policyId** et **ruleId** dans le fichier de définition de la stratégie de chaque scénario.

| Lors de la création du fichier de définition de la stratégie pour ce compte... | Définissez l’ID de stratégie sur cette valeur | Définissez les ID de règle sur cette valeur |
|-|-|-|
| Compte de destination | Valeur de chaîne *par défaut*. Le service Stockage Azure créera la valeur d’ID de stratégie pour vous. | Chaîne vide. Le service Stockage Azure créera les valeurs d’ID de règle pour vous. |
| Compte source | La valeur de l’ID de stratégie est retournée quand vous téléchargez le fichier de définition de la stratégie pour le compte de destination. | La valeur des ID de règle est retournée quand vous téléchargez le fichier de définition de la stratégie pour le compte de destination. |

## <a name="prevent-replication-across-azure-ad-tenants"></a>Empêcher la réplication entre les locataires Azure AD

Un locataire du répertoire actif Azure (Azure AD) est une instance dédiée d’Azure AD qui représente une organisation à des fins de gestion des identités et des accès. Chaque abonnement Azure dispose d’une relation d’approbation avec un locataire Azure AD. Toutes les ressources d’un abonnement, y compris les comptes de stockage, sont associées au même locataire Azure AD. Pour plus d’informations, consultez [Qu’est-ce qu’Azure Active Directory ?](../../active-directory/fundamentals/active-directory-whatis.md)

Par défaut, un utilisateur disposant des autorisations appropriées peut configurer la réplication d’objets avec un compte de stockage source qui se trouve dans un locataire Azure AD et un compte de destination qui se trouve dans un autre locataire. Si vos stratégies de sécurité exigent que vous restreigniez la réplication d’objets aux comptes de stockage qui résident dans le même locataire uniquement, vous pouvez interdire la réplication entre les locataires en définissant une propriété de sécurité, la propriété **AllowCrossTenantReplication** (préversion). Lorsque vous interdisez la réplication d’objets entre locataires pour un compte de stockage, le Stockage Azure demandera alors à toute stratégie de réplication d’objet configurée avec ce compte de stockage en tant que compte source ou de destination, que les comptes source et de destination résident dans le même Azure AD locataire. Pour plus d’informations sur l’interdiction de la réplication d’objets entre locataires, consultez [Empêcher la réplication d’objets entre les locataires du Répertoire actif Azure](object-replication-prevent-cross-tenant-policies.md).

Pour interdire la réplication d’objets entre locataires pour un compte de stockage, affectez la valeur *false* à la propriété **AllowCrossTenantReplication**. Si le compte de stockage ne participe actuellement à aucune stratégie de réplication d’objet entre locataires, l’affectation de la valeur *false* à la propriété **AllowCrossTenantReplication** empêche la configuration ultérieure de stratégies de réplication d’objets entre locataires avec ce compte de stockage comme source ou destination.

Si le compte de stockage participe actuellement à une ou plusieurs stratégies de réplication d’objet entre locataires, l’affectation de la valeur *false* à la propriété **AllowCrossTenantReplication** n’est pas autorisée. Vous devez supprimer les stratégies entre locataires existantes avant de pouvoir désactiver la réplication entre locataires.

Par défaut, la propriété **AllowCrossTenantReplication** n’est pas définie pour un compte de stockage et sa valeur est *null*, ce qui équivaut à *true*. Lorsque la valeur de la propriété **AllowCrossTenantReplication** pour un compte de stockage est *null* ou *true*, les utilisateurs autorisés peuvent configurer des stratégies de réplication d’objets entre locataires avec ce compte comme source ou destination. Pour plus d’informations sur la configuration des stratégies entre locataires, consultez [Configurer la réplication d’objets pour les objets Blob de blocs](object-replication-configure.md).

Vous pouvez utiliser la Stratégie Azure pour auditer un ensemble de comptes de stockage et vous assurer que la propriété **AllowCrossTenantReplication** est définie pour empêcher la réplication d’objets entre locataires. Vous pouvez également utiliser la Stratégie Azure pour appliquer la gouvernance d’un ensemble de comptes de stockage. Par exemple, vous pouvez créer une stratégie avec l’effet de refus pour empêcher un utilisateur de créer un compte de stockage où la propriété **AllowCrossTenantReplication** est définie sur *true* ou de modifier un compte de stockage existant pour remplacer la valeur de la propriété par *true*.

## <a name="replication-status"></a>État de la réplication

Vous pouvez vérifier l’état de réplication d’un objet blob dans le compte source. Pour plus d’informations, consultez [Vérifier l’état de réplication d’un objet blob](object-replication-configure.md#check-the-replication-status-of-a-blob).

Si l’état de réplication d’un blob dans le compte source indique un échec, examinez les causes possibles suivantes :

- Assurez-vous que la stratégie de réplication d’objet est configurée sur le compte de destination.
- Vérifiez que le conteneur de destination existe toujours.
- Si le blob source a été chiffré à l’aide d’une clé fournie par le client dans le cadre d’une opération d’écriture, la réplication d’objet échoue. Pour plus d’informations sur les clés fournies par le client, consultez [Fournir une clé de chiffrement lors d’une requête au stockage d’objets blob](encryption-customer-provided-keys.md).

## <a name="feature-support"></a>Prise en charge des fonctionnalités

Ce tableau montre comment cette fonctionnalité est prise en charge dans votre compte ainsi que l’impact sur la prise en charge lorsque vous activez certaines fonctionnalités.

| Type de compte de stockage                | Stockage Blob (prise en charge par défaut)   | Data Lake Storage Gen2 <sup>1</sup>                        | NFS 3.0 <sup>1</sup>
|-----------------------------|---------------------------------|------------------------------------|--------------------------------------------------|
| Usage général v2 Standard | ![Oui](../media/icons/yes-icon.png) |![Non](../media/icons/no-icon.png)              | ![Non](../media/icons/no-icon.png) |
| Objets blob de blocs Premium          | ![Oui](../media/icons/yes-icon.png) |![Non](../media/icons/no-icon.png)              | ![Non](../media/icons/no-icon.png) |

<sup>1</sup>    Data Lake Storage Gen2 et le protocole NFS (Network File System) 3.0 requièrent tous deux un compte de stockage avec un espace de noms hiérarchique activé.

## <a name="billing"></a>Facturation

La réplication d'objets entraîne des coûts supplémentaires pour les transactions de lecture et d'écriture sur les comptes source et de destination, ainsi que des frais de sortie pour la réplication des données du compte source vers le compte de destination, et des frais de lecture pour le traitement des flux de modification.

## <a name="next-steps"></a>Étapes suivantes

- [Configurer la réplication d’objets](object-replication-configure.md)
- [Empêcher la réplication d’objets sur les locataires du Répertoire actif Azure ](object-replication-prevent-cross-tenant-policies.md)
- [Contrôle de version des blobs](versioning-overview.md)
- [Prise en charge du flux de modification dans Stockage Blob Azure](storage-blob-change-feed.md)
