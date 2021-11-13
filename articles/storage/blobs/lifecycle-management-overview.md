---
title: Optimiser les coûts en gérant automatiquement le cycle de vie des données
titleSuffix: Azure Storage
description: Utilisez les stratégies de gestion de cycle de vie de Stockage Azure pour créer des règles automatiques de déplacement des données entre les niveaux chaud, froid et archive.
author: tamram
ms.author: tamram
ms.date: 08/18/2021
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.reviewer: yzheng
ms.custom: devx-track-azurepowershell, references_regions
ms.openlocfilehash: 69cb9048ef723744da93478b01ba7122152e1db9
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131460302"
---
# <a name="optimize-costs-by-automatically-managing-the-data-lifecycle"></a>Optimiser les coûts en gérant automatiquement le cycle de vie des données

Les jeux de données ont des cycles de vie différents. Tôt dans le cycle de vie, les utilisateurs accèdent souvent à certaines données. Mais la nécessité d’y accéder diminue souvent de façon spectaculaire à mesure que les données vieillissent. Certaines données restent inactives dans le cloud et sont rarement utilisées une fois stockées. Certains jeux de données expirent plusieurs jours ou mois après leur création, tandis que d’autres jeux de données sont activement lus et modifiés tout au long de leur vie. La gestion de cycle de vie de Stockage Azure offre une stratégie basée sur des règles que vous pouvez utiliser pour faire passer les données blob aux niveaux d’accès appropriés ou faire expirer les données à la fin de leur cycle de vie.

Grâce à la stratégie de gestion de cycle de vie, vous pouvez effectuer les opération suivantes :

- Transférer des blobs du niveau froid au niveau chaud dès qu’ils sont consultés afin d’optimiser les performances.
- Transférer les blob, les versions de blobs et les instantanés de blob vers un niveau de stockage plus froid si ces objets n’ont pas été consultés ni modifiés pendant un certain temps afin d’optimiser les coûts. Dans ce scénario, la stratégie de gestion de cycle de vie peut déplacer les objets du niveau chaud au niveau froid, du niveau chaud au niveau archive ou du niveau froid au niveau archive.
- Supprimer les blob, les versions de blobs et les instantanés de blob à la fin de leur cycle de vie.
- Définir des règles à exécuter une fois par jour au niveau du compte de stockage.
- Appliquer des règles à des conteneurs ou à un sous-ensemble de blobs en utilisant des préfixes de noms ou des [balises d’index de blobs](storage-manage-find-blobs.md) en tant que filtres.

Considérez un scénario où des données sont sollicitées fréquemment durant les premières étapes du cycle de vie, mais seulement occasionnellement après deux semaines. Au-delà du premier mois, le jeu de données est rarement sollicité. Dans ce scénario, le stockage chaud est préférable durant les premiers temps. Un stockage froid est plus approprié pour un accès occasionnel. L’option Stockage archive est la meilleure une fois que les données ont plus d’un mois. En déplaçant les données vers le niveau de stockage approprié en fonction de leur ancienneté grâce aux règles de stratégie de gestion de cycle de vie, vous pouvez concevoir la solution la moins coûteuse correspondant à vos besoins.

Les stratégies de gestion de cycle de vie sont prises en charge pour les objets blob de blocs et d’ajout dans les comptes v2 universels, les objets blob de blocs premium et les comptes Stockage Blob. La gestion de cycle de vie ne concerne pas les conteneurs système comme les conteneurs *$logs* ou *$web*.

> [!IMPORTANT]
> Si un jeu de données doit être lisible, ne définissez pas de stratégie pour déplacer les blobs vers le niveau archive. Les blobs du niveau archive ne peuvent pas être lus s’ils ne sont pas d’abord réhydratés, un processus qui peut être long et coûteux. Pour plus d’informations, voir [Vue d’ensemble de la réactivation d’objets blob à partir du niveau Archive](archive-rehydrate-overview.md).

## <a name="lifecycle-management-policy-definition"></a>Définition d’une stratégie de gestion de cycle de vie

Une stratégie de gestion de cycle de vie est une collection de règles dans un document JSON. L’exemple JSON suivant montre une définition de règle complète :

```json
{
  "rules": [
    {
      "name": "rule1",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {...}
    },
    {
      "name": "rule2",
      "type": "Lifecycle",
      "definition": {...}
    }
  ]
}
```

Une stratégie est une collection de règles, comme décrit dans le tableau suivant :

| Nom du paramètre | Type de paramètre | Notes |
|----------------|----------------|-------|
| `rules`        | Un ensemble d’objets de règle | Une stratégie requiert au moins une règle. Vous pouvez définir jusqu’à 100 règles dans une stratégie.|

Chaque règle de la stratégie a plusieurs paramètres, décrits dans le tableau suivant :

| Nom du paramètre | Type de paramètre | Notes | Obligatoire |
|--|--|--|--|
| `name` | String | Un nom de règle peut compter jusqu’à 256 caractères alphanumériques. Les noms de règle respectent la casse. Ils doivent être uniques dans la stratégie. | True |
| `enabled` | Boolean | Valeur booléenne facultative pour permettre la désactivation temporaire d’une règle. La valeur par défaut est true. | False |
| `type` | Une valeur enum | Le type valide actuel est `Lifecycle`. | True |
| `definition` | Un objet qui définit la règle du cycle de vie | Chaque définition se compose d’un jeu de filtres et d’un jeu d’actions. | True |

## <a name="lifecycle-management-rule-definition"></a>Définition d’une règle de gestion de cycle de vie

Chaque définition de règle dans une stratégie se compose d’un ensemble de filtres et d’un ensemble d’actions. Le [jeu de filtres](#rule-filters) limite les actions de règle à un certain ensemble d’objets dans un conteneur ou des noms d’objets. L’[ensemble d’actions](#rule-actions) applique les actions de niveau ou de suppression à l’ensemble d’objets filtré.

### <a name="sample-rule"></a>Exemple de règle

L’exemple de règle suivant filtre le compte pour exécuter les actions sur des objets existant à l’intérieur de `sample-container` et commençant par `blob1`.

- Niveau objet blob sur accès froid 30 jours après la dernière modification
- Niveau objet blob sur accès archive 90 jours après la dernière modification
- Supprimer l’objet blob 2 555 jours (sept ans) après la dernière modification
- Supprimer les versions précédentes des objets blob 90 jours après leur création

```json
{
  "rules": [
    {
      "enabled": true,
      "name": "sample-rule",
      "type": "Lifecycle",
      "definition": {
        "actions": {
          "version": {
            "delete": {
              "daysAfterCreationGreaterThan": 90
            }
          },
          "baseBlob": {
            "tierToCool": {
              "daysAfterModificationGreaterThan": 30
            },
            "tierToArchive": {
              "daysAfterModificationGreaterThan": 90
            },
            "delete": {
              "daysAfterModificationGreaterThan": 2555
            }
          }
        },
        "filters": {
          "blobTypes": [
            "blockBlob"
          ],
          "prefixMatch": [
            "sample-container/blob1"
          ]
        }
      }
    }
  ]
}
```

### <a name="rule-filters"></a>Filtres de règle

Les filtres limitent les actions des règles à un sous-ensemble d’objets blob dans le compte de stockage. Si plusieurs filtres sont définis, une opération logique `AND` est exécutée sur tous les filtres.

Les filtres sont les suivants :

| Nom du filtre | Type de filtre | Notes | Est obligatoire |
|-------------|-------------|-------|-------------|
| blobTypes   | Un ensemble de valeurs enum prédéfinies. | La version actuelle prend en charge `blockBlob` et `appendBlob`. Seule la suppression est prise en charge pour `appendBlob`, le niveau défini n’est pas pris en charge. | Oui |
| prefixMatch | Un ensemble de chaînes pour les préfixes à mettre en correspondance. Chaque règle peut définir jusqu’à 10 préfixes qui respectent la casse. Une chaîne de préfixe doit commencer par un nom de conteneur. Par exemple, si vous souhaitez faire correspondre tous les objets blob sous `https://myaccount.blob.core.windows.net/sample-container/blob1/...` pour une règle, prefixMatch est `sample-container/blob1`. | Si vous ne définissez pas prefixMatch, la règle s’applique à tous les objets blob au sein du compte de stockage. | Non |
| blobIndexMatch | Un tableau de valeurs de dictionnaire composé de conditions de clé et de valeur de balise d’index de blob à mettre en correspondance. Chaque règle peut définir jusqu’à 10 conditions de balise d’index de blob. Par exemple, si vous souhaitez mettre en correspondre tous les objets blob avec `Project = Contoso` sous `https://myaccount.blob.core.windows.net/` pour une règle, le blobIndexMatch est `{"name": "Project","op": "==","value": "Contoso"}`. | Si vous ne définissez pas blobIndexMatch, la règle s’applique à tous les objets blob au sein du compte de stockage. | Non |

Pour en savoir plus sur la fonctionnalité d’index de blob ainsi que sur les problèmes et les limites connus, consultez [Gérer et rechercher des données dans Stockage Blob Azure avec un index de blob](storage-manage-find-blobs.md).

### <a name="rule-actions"></a>Actions de règle

Des actions sont appliquées aux objets blob filtrés lorsque la condition d’exécution est remplie.

La gestion du cycle de vie prend en charge la hiérarchisation et la suppression des objets blob, des versions d’objets blob précédentes et des instantanés d’objets blob. Définissez au moins une action pour chaque règle sur les objets blob de base, les versions d’objets blob précédentes ou les instantanés d’objets blob.

| Action                      | Objet blob de base                                  | Instantané      | Version
|-----------------------------|--------------------------------------------|---------------|---------------|
| tierToCool                  | Prise en charge pour `blockBlob`                  | Prise en charge     | Prise en charge     |
| enableAutoTierToHotFromCool | Prise en charge pour `blockBlob`                  | Non pris en charge | Non pris en charge |
| tierToArchive               | Prise en charge pour `blockBlob`                  | Prise en charge     | Prise en charge     |
| supprimer                      | Pris en charge pour `blockBlob` et `appendBlob` | Prise en charge     | Prise en charge     |

> [!NOTE]
> Si vous définissez plusieurs actions sur le même objet blob, la gestion du cycle de vie applique l’action la moins coûteuse à l’objet blob. Par exemple, l’action `delete` est moins coûteuse que l’action `tierToArchive`. L’action `tierToArchive` est moins coûteuse que l’action `tierToCool`.

Les conditions d’exécution sont basées sur l’âge. Les objets blob de base utilisent l’heure de dernière modification, les versions d’objets blob utilisent l’heure de création de la version et les instantanés d’objets blob utilisent l’heure de création des instantanés pour suivre l’ancienneté.

| Condition d’exécution d’action | Valeur de la condition | Description |
|--|--|--|
| daysAfterModificationGreaterThan | Nombre entier indiquant l’âge en jours | Condition pour les actions des objets blob de base |
| daysAfterCreationGreaterThan | Nombre entier indiquant l’âge en jours | Condition pour les actions de versions d’objets blob et d’instantanés d’objets blob |
| daysAfterLastAccessTimeGreaterThan | Nombre entier indiquant l’âge en jours | Condition pour les actions de base du blob lorsque le suivi d’accès est activé |

## <a name="examples-of-lifecycle-policies"></a>Exemples de stratégies de cycle de vie

Les exemples suivants expliquent comment résoudre des scénarios courants avec les règles de stratégie du cycle de vie.

### <a name="move-aging-data-to-a-cooler-tier"></a>Déplacer les données vieillissantes vers un niveau plus froid

Cet exemple montre comment déplacer des objets blob de blocs ayant le préfixe `sample-container/blob1` ou `container2/blob2`. La stratégie déplace les objets blob qui n’ont pas été modifiés depuis plus de 30 jours vers le stockage froid et les objets blob non modifiés depuis 90 jours vers le niveau archive :

```json
{
  "rules": [
    {
      "name": "agingRule",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ],
          "prefixMatch": [ "sample-container/blob1", "container2/blob2" ]
        },
        "actions": {
          "baseBlob": {
            "tierToCool": { "daysAfterModificationGreaterThan": 30 },
            "tierToArchive": { "daysAfterModificationGreaterThan": 90 }
          }
        }
      }
    }
  ]
}
```

### <a name="move-data-based-on-last-accessed-time"></a>Déplacer des données en fonction de l’heure du dernier accès

Vous pouvez activer le suivi de l’heure du dernier accès pour conserver un enregistrement de la dernière lecture ou écriture de votre blob et en tant que filtre pour gérer la hiérarchisation et la conservation de vos données BLOB. Pour savoir comment activer le suivi de l’heure du dernier accès, consultez [Activer le suivi de l’heure d’accès (facultatif)](lifecycle-management-policy-configure.md#optionally-enable-access-time-tracking).

Lorsque le suivi de l’heure du dernier accès est activé, la propriété d’objet blob appelée `LastAccessTime` est mise à jour lors de la lecture ou de l’écriture d’un objet blob. Une opération [Obtenir un objet blob](/rest/api/storageservices/get-blob) est considérée comme une opération d’accès. [Obtenir les propriétés d’objets blob](/rest/api/storageservices/get-blob-properties), [Obtenir des métadonnées d’objets blob](/rest/api/storageservices/get-blob-metadata) et [Obtenir des étiquettes d’objet blob](/rest/api/storageservices/get-blob-tags) ne sont pas des opérations d’accès et ne mettent donc pas à jour l’heure du dernier accès.

Pour réduire l’impact sur la latence d’accès en lecture, seule la première lecture des dernières 24 heures met à jour l’heure du dernier accès. Les lectures suivantes dans la même période de 24 heures ne mettent pas à jour l’heure du dernier accès. Si un objet blob est modifié entre des lectures, l’heure du dernier accès est la plus récente des deux valeurs.

Dans l’exemple suivant, les objets BLOB sont déplacés vers le stockage froid s’ils n’ont pas fait l’objet d’accès depuis 30 jours. La propriété `enableAutoTierToHotFromCool` est une valeur booléenne qui indique si un blob doit être automatiquement reclassé de froid à chaud s’il fait l’objet d’un nouvel accès après avoir été déplacé dans le niveau froid.

```json
{
  "enabled": true,
  "name": "last-accessed-thirty-days-ago",
  "type": "Lifecycle",
  "definition": {
    "actions": {
      "baseBlob": {
        "enableAutoTierToHotFromCool": true,
        "tierToCool": {
          "daysAfterLastAccessTimeGreaterThan": 30
        }
      }
    },
    "filters": {
      "blobTypes": [
        "blockBlob"
      ],
      "prefixMatch": [
        "mylifecyclecontainer/log"
      ]
    }
  }
}
```

### <a name="archive-data-after-ingest"></a>Archiver les données après leur ingestion

Certaines données restent inactives dans le cloud et sont rarement, voire jamais, consultées. La stratégie du cycle de vie suivante est configurée pour archiver les données peu après leur ingestion. Cet exemple déplace les objets blob de blocs d’un conteneur nommé `archivecontainer` à un niveau archive. Le déplacement est accompli en agissant sur les objets blob 0 jour après l’heure de dernière modification :

```json
{
  "rules": [
    {
      "name": "archiveRule",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ],
          "prefixMatch": [ "archivecontainer" ]
        },
        "actions": {
          "baseBlob": {
              "tierToArchive": { "daysAfterModificationGreaterThan": 0 }
          }
        }
      }
    }
  ]
}

```

> [!NOTE]
> Microsoft vous recommande de charger vos blobs directement dans le niveau archive pour une plus grande efficacité. Vous pouvez spécifier le niveau archive dans l’en-tête *x-ms-access-tier* sur l’opération [Put Blob](/rest/api/storageservices/put-blob) ou [Put Block List](/rest/api/storageservices/put-block-list). L’en-tête *x-ms-access-tier* est pris en charge avec REST 2018-11-09 et versions ultérieures ou les dernières bibliothèques de client de Stockage Blob.

### <a name="expire-data-based-on-age"></a>Faire expirer les données selon l’âge

Certaines données sont supposées expirer un certain nombre de jours ou de mois après leur création. Vous pouvez configurer une stratégie de gestion du cycle de vie afin de faire expirer les données en les supprimant en fonction de leur ancienneté. L’exemple suivant présente une stratégie qui supprime tous les objets blob de bloc datant de plus de 365 jours.

```json
{
  "rules": [
    {
      "name": "expirationRule",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ]
        },
        "actions": {
          "baseBlob": {
            "delete": { "daysAfterModificationGreaterThan": 365 }
          }
        }
      }
    }
  ]
}
```

### <a name="delete-data-with-blob-index-tags"></a>Supprimer des données avec des balises d’index de blobs

Certaines données ne doivent expirer que si elles sont marquées explicitement pour suppression. Vous pouvez configurer une stratégie de gestion du cycle de vie pour faire expirer les données qui sont marquées avec des attributs clé/valeur d’index d’objets blob. L’exemple suivant présente une stratégie qui supprime tous les objets blob de bloc balisés avec `Project = Contoso`. Pour en savoir plus sur un index de blobs, consultez [Gérer et rechercher des données dans Stockage Blob Azure avec un index de blobs (préversion)](storage-manage-find-blobs.md).

```json
{
    "rules": [
        {
            "enabled": true,
            "name": "DeleteContosoData",
            "type": "Lifecycle",
            "definition": {
                "actions": {
                    "baseBlob": {
                        "delete": {
                            "daysAfterModificationGreaterThan": 0
                        }
                    }
                },
                "filters": {
                    "blobIndexMatch": [
                        {
                            "name": "Project",
                            "op": "==",
                            "value": "Contoso"
                        }
                    ],
                    "blobTypes": [
                        "blockBlob"
                    ]
                }
            }
        }
    ]
}
```

### <a name="manage-versions"></a>Gérer les versions

Pour les données modifiées et consultées régulièrement pendant toute leur durée de vie, vous pouvez activer la gestion de versions du stockage Blob afin de gérer automatiquement les versions précédentes d’un objet. Vous pouvez créer une stratégie pour hiérarchiser ou supprimer les versions précédentes. L’âge de la version est déterminé en regardant l’heure de création de cette dernière. Cette règle de stratégie hiérarchise les versions précédentes du conteneur `activedata` qui datent de 90 jours ou plus après la création de la version vers le niveau Froid, et supprime les versions antérieures datant de 365 jours ou plus.

```json
{
  "rules": [
    {
      "enabled": true,
      "name": "versionrule",
      "type": "Lifecycle",
      "definition": {
        "actions": {
          "version": {
            "tierToCool": {
              "daysAfterCreationGreaterThan": 90
            },
            "delete": {
              "daysAfterCreationGreaterThan": 365
            }
          }
        },
        "filters": {
          "blobTypes": [
            "blockBlob"
          ],
          "prefixMatch": [
            "activedata"
          ]
        }
      }
    }
  ]
}
```

## <a name="feature-support"></a>Prise en charge des fonctionnalités

Ce tableau montre comment cette fonctionnalité est prise en charge dans votre compte ainsi que l’impact sur la prise en charge lorsque vous activez certaines fonctionnalités.

| Type de compte de stockage                | Stockage Blob (prise en charge par défaut)   | Data Lake Storage Gen2 <sup>1</sup>                        | NFS 3.0 <sup>1</sup>
|-----------------------------|---------------------------------|------------------------------------|--------------------------------------------------|
| Usage général v2 Standard | ![Oui](../media/icons/yes-icon.png) |![Oui](../media/icons/yes-icon.png)              | ![Oui](../media/icons/yes-icon.png) |
| Objets blob de blocs Premium          | ![Oui](../media/icons/yes-icon.png)|![Oui](../media/icons/yes-icon.png) | ![Oui](../media/icons/yes-icon.png) |

<sup>1</sup>    Data Lake Storage Gen2 et le protocole NFS (Network File System) 3.0 requièrent tous deux un compte de stockage avec un espace de noms hiérarchique activé.

## <a name="regional-availability-and-pricing"></a>Disponibilité régionale et tarification

La fonctionnalité de gestion du cycle de vie est disponible dans toutes les régions Azure.

Les stratégies de gestion de cycle de vie sont gratuites. Les clients sont facturés au coût de fonctionnement normal pour les appels d’API [Set Blob Tier](/rest/api/storageservices/set-blob-tier). Les opérations de suppression sont gratuites.

Chaque mise à jour de l’heure du dernier accès d’un blob est facturée dans la catégorie [Autres opérations](https://azure.microsoft.com/pricing/details/storage/blobs/).

Pour plus d’informations sur les prix, consultez [Tarification Objets blob de blocs](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="faq"></a>Questions fréquentes (FAQ)

**J’ai créé une stratégie. Pourquoi les actions ne s’exécutent-elles pas immédiatement ?**

La plateforme exécute la stratégie de cycle de vie une fois par jour. Une fois que vous avez configuré une stratégie, jusqu’à 24 heures peuvent s’écouler avant que certaines actions s’exécutent pour la première fois.

**Si je mets à jour une stratégie existante, combien de temps dois-je attendre avant que les actions soient effectuées ?**

Cela peut prendre jusqu’à 24 heures avant que la stratégie mise à jour ne soit appliquée. Une fois la stratégie en vigueur, cela peut prendre jusqu’à 24 heures pour que les actions s’exécutent. Par conséquent, l’exécution des actions de la stratégie peut prendre jusqu’à 48 heures. Si la mise à jour consiste à désactiver ou à supprimer une règle et que enableAutoTierToHotFromCool a été utilisé, la hiérarchisation automatique vers le niveau chaud se produira quand même. Par exemple, définissez une règle incluant enableAutoTierToHotFromCool basée sur le dernier accès. Si la règle est désactivée/supprimée et qu’un blob est actuellement dans le niveau froid et qu’il est ensuite consulté, il repassera dans le niveau chaud, car la règle est appliquée à l’accès en dehors de la gestion de cycle de vie. Le blob ne passera pas du niveau chaud à froid si la règle de gestion de cycle de vie est désactivée/supprimée. La seule façon d’empêcher autoTierToHotFromCool consiste à désactiver le suivi de l’heure du dernier accès.

**J’ai réactivé manuellement un blob archivé. Comment puis-je empêcher son renvoi temporaire au niveau Archives ?**

Quand un objet blob est déplacé d’un niveau d’accès vers un autre, l’heure de sa dernière modification ne change pas. Si vous réactivez manuellement un blob archivé à un chaud, il est renvoyé au niveau archive par le moteur de gestion du cycle de vie. Désactivez la règle qui affecte temporairement cet objet BLOB pour empêcher son archivage. Réactivez la règle lorsque le BLOB peut être renvoyé en toute sécurité au niveau archive. Vous pouvez aussi copier le blob vers un autre emplacement s’il doit rester en permanence au niveau chaud ou froid.

## <a name="next-steps"></a>Étapes suivantes

- [Configurer une stratégie de gestion de cycle de vie](lifecycle-management-policy-configure.md)
- [Niveaux d’accès chaud, froid et archive pour les données d’objet blob](access-tiers-overview.md)
- [Gérer et rechercher des données dans Stockage Blob Azure avec un index de blobs](storage-manage-find-blobs.md)
