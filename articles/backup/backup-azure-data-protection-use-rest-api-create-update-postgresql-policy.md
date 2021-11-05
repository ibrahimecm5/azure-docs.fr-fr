---
title: Créer des stratégies de sauvegarde pour les bases de données Azure PostgreSQL à l’aide de l’API REST de protection des données
description: Dans cet article, vous apprenez à créer et gérer des stratégies de sauvegarde pour les bases de données Azure PostgreSQL à l’aide de l’API REST.
ms.topic: conceptual
ms.date: 10/21/2021
ms.author: v-amallick
ms.assetid: 759ee63f-148b-464c-bfc4-c9e640b7da6b
ms.openlocfilehash: 2caa9d89ae69d9710f42dc4b3c958d6fa8873098
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131097591"
---
# <a name="create-azure-data-protection-backup-policies-for-azure-postgresql-databases-using-rest-api"></a>Créer des stratégies de sauvegarde de protection des données Azure pour les bases de données Azure PostgreSQL à l’aide de l’API REST

Une stratégie de sauvegarde régit la rétention et la planification de vos sauvegardes. La sauvegarde de base de données Azure PostgreSQL offre une conservation à long terme et prend en charge une sauvegarde par jour.

Vous pouvez réutiliser une stratégie de sauvegarde existante pour configurer la sauvegarde des bases de données PostgreSQL dans un coffre ou [créer une stratégie de sauvegarde pour un coffre Azure Recovery Services à l’aide de l’API REST](/rest/api/dataprotection/backup-policies/create-or-update).

## <a name="understanding-postgresql-backup-policy"></a>Présentation de la stratégie de sauvegarde PostgreSQL

Alors que la sauvegarde sur disque offre plusieurs sauvegardes par jour et que la sauvegarde de blobs est une sauvegarde *continue* sans déclencheur, la sauvegarde PostgreSQL offre une protection de niveau archive. Les données de sauvegarde qui sont envoyées pour la première fois au coffre peuvent être déplacées vers le niveau *archive*, conformément à une règle définie ou à un *cycle de vie*. Dans ce contexte, examinons l’objet de stratégie de sauvegarde pour PostgreSQL.

-  PolicyRule
   -  BackupRule
      -  BackupParameter
         -  BackupType (sauvegarde complète de base de données, dans notre cas)
         -  Magasin de données initial (où les sauvegardes sont enregistrées initialement)
         -  Déclencheur (mode de déclenchement de la sauvegarde)
            -  À partir d’une planification
            -  Critères d’étiquetage par défaut (une « étiquette » par défaut pour toutes les sauvegardes planifiées. Cette étiquette relie les sauvegardes à la règle de conservation)
   -  Règle de conservation par défaut (règle appliquée à toutes les sauvegardes, par défaut, dans le magasin de données initial)

Par conséquent, cet objet définit le type des sauvegardes qui sont déclenchées, la façon dont elles sont déclenchées (avec une planification), l’étiquette qu’elles reçoivent, leur emplacement (un magasin de données) et le cycle de vie des données de sauvegarde dans un magasin de données. L’objet PowerShell par défaut pour PostgreSQL indique de déclencher une sauvegarde *complète* toutes les semaines. Ces sauvegardes sont enregistrées dans le coffre où elles sont stockées pendant trois mois.

Pour ajouter le niveau *archive* à la stratégie, vous devez choisir quand les données sont déplacées du coffre vers l’archive, combien de temps elles restent dans l’archive et quelles sauvegardes planifiées doivent être étiquetées comme *archivables*. Par conséquent, vous devez ajouter une *règle de conservation* où vous définissez le cycle de vie des données de sauvegarde entre le magasin de données du coffre et le magasin de données d’archive, et la durée pendant laquelle elles sont conservées dans le magasin de données d’*archive*. Vous devez ensuite ajouter une « étiquette » qui indique que les sauvegardes planifiées sont _éligibles pour l’archivage_. L’objet PowerShell qui en résulte est le suivant :

-  PolicyRule
   -  BackupRule
      -  BackupParameter
         -  BackupType (sauvegarde complète de base de données, dans notre cas)
         -  Magasin de données initial (où les sauvegardes sont enregistrées initialement)
         -  Déclencheur (mode de déclenchement de la sauvegarde)
            -  À partir d’une planification
            -  Critères d’étiquetage par défaut (une « étiquette » par défaut pour toutes les sauvegardes planifiées. Cette étiquette relie les sauvegardes à la règle de conservation)
            -  Nouveaux critères d’étiquetage pour la nouvelle règle de conservation de même nom « X »
   -  Règle de conservation par défaut (règle appliquée à toutes les sauvegardes, par défaut, dans le magasin de données initial)
   -  Une nouvelle règle de conservation nommée « X »
      -  Cycle de vie
         -  Magasin de données source
         -  Supprimer au bout d’une période donnée dans le magasin de données source
         - Copier dans le magasin de données cible

Pour créer une stratégie de sauvegarde des bases de données PostgreSQL, effectuez les actions suivantes :

## <a name="create-a-policy"></a>Créer une stratégie

>[!IMPORTANT]
>Actuellement, ni la mise à jour ni la modification d’une stratégie existante n’est prise en charge. Sinon, vous pouvez créer une nouvelle stratégie avec les détails requis et l’attribuer à l’instance de sauvegarde appropriée.

Pour créer une stratégie de sauvegarde Azure, utilisez l’opération *PUT* suivante :

```http
PUT https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataProtection/backupVaults/{vaultName}/backupPolicies/{policyName}?api-version=2021-01-01
```

`{policyName}` et `{vaultName}` sont fournis dans l’URI. Des informations supplémentaires sont fournies dans le corps de la demande.

## <a name="create-the-request-body"></a>Créer le corps de la demande

Par exemple, pour créer une stratégie pour une sauvegarde PostgreSQL, le corps de la demande a besoin des composants suivants :

|Nom  |Obligatoire  |Type  |Description  |
|---------|---------|---------|---------|
|properties     |   True      |  BaseBackupPolicy : [BackupPolicy](/rest/api/dataprotection/backup-policies/create-or-update#backuppolicy)      | Propriétés BaseBackupPolicyResource        |

Pour obtenir la liste complète des définitions dans le corps de la demande, consultez le [document sur l’API REST de stratégie de sauvegarde](/rest/api/dataprotection/backup-policies/create-or-update).

### <a name="example-request-body"></a>Exemple de corps de demande

La stratégie indique ce qui suit :

- Déclencheur planifié pour une sauvegarde hebdomadaire et choisir l’heure de début. (Heure + P1W).
- Le magasin de données est le _magasin du coffre_, car les sauvegardes sont transférées directement au coffre.
- Les sauvegardes sont conservées dans le coffre pendant trois mois (P3M).

```json
{
  "datasourceTypes": [
    "Microsoft.DBforPostgreSQL/servers/databases"
  ],
  "name": "OssPolicy1",
  "objectType": "BackupPolicy",
  "policyRules": [
    {
      "backupParameters": {
        "backupType": "Full",
        "objectType": "AzureBackupParams"
      },
      "dataStore": {
        "dataStoreType": "VaultStore",
        "objectType": "DataStoreInfoBase"
      },
      "name": "BackupWeekly",
      "objectType": "AzureBackupRule",
      "trigger": {
        "objectType": "ScheduleBasedTriggerContext",
        "schedule": {
          "repeatingTimeIntervals": [
            "R/2021-08-15T06:30:00+00:00/P1W"
          ],
          "timeZone": "UTC"
        },
        "taggingCriteria": [
          {
            "isDefault": true,
            "tagInfo": {
              "id": "Default_",
              "tagName": "Default"
            },
            "taggingPriority": 99
          }
        ]
      }
    },
    {
      "isDefault": true,
      "lifecycles": [
        {
          "deleteAfter": {
            "duration": "P3M",
            "objectType": "AbsoluteDeleteOption"
          },
          "sourceDataStore": {
            "dataStoreType": "VaultStore",
            "objectType": "DataStoreInfoBase"
          },
          "targetDataStoreCopySettings": []
        }
      ],
      "name": "Default",
      "objectType": "AzureRetentionRule"
    }
  ]
}
```

>[!IMPORTANT]
>Les formats d’heure prennent en charge _DateHeure_ uniquement, _Heure_ uniquement n’est pas pris en charge. L’heure de la journée indique l’heure de début de la sauvegarde, et pas l’heure de fin.

Nous mettons à jour le **JSON** ci-dessus avec deux changements : les sauvegardes sur plusieurs jours de la semaine et l’ajout d’un magasin de données d’*archive* pour la conservation à long terme des sauvegardes de bases de données PostgreSQL.

L’exemple suivant modifie la sauvegarde hebdomadaire pour qu’elle se produise tous les dimanches, mercredis et vendredis. Le tableau de dates de planification mentionne les dates, et les jours de la semaine correspondant à ces dates sont pris comme les jours de la semaine. Vous devez également spécifier que ces planifications se répètent chaque semaine. Par conséquent, l’intervalle de planification est *1* et le type d’intervalle est *Weekly*.

**Déclencheur planifié :**

```json
"trigger": {
        "objectType": "ScheduleBasedTriggerContext",
        "schedule": {
          "repeatingTimeIntervals": [
            "R/2021-08-15T22:00:00+00:00/P1W",
            "R/2021-08-18T22:00:00+00:00/P1W",
            "R/2021-08-20T22:00:00+00:00/P1W"
          ],
          "timeZone": "UTC"
        }
```

Pour ajouter la protection du niveau *archive*, vous devez modifier le JSON de la stratégie de la façon suivante :

Le JSON ci-dessus a un cycle de vie pour le magasin de données initial sous la règle de conservation par défaut. Dans ce scénario, la règle indique de supprimer les données de sauvegarde au bout de trois mois. Vous devez ajouter une nouvelle règle de conservation qui définit quand les données sont *déplacées* vers le magasin de données d’*archive*, autrement dit, les données de sauvegarde sont d’abord copiées dans le magasin de données d’archive, puis supprimées dans le magasin de données du coffre. Par ailleurs, la règle doit définir les durées de conservation des données dans le magasin de données d’archive*. Nommons cette nouvelle règle **Monthly**. Elle établit que les sauvegardes doivent être conservées dans le magasin de données du coffre pendant 6 mois, puis copiées dans le magasin de données d’archive. Ensuite, la *suppression* dans le magasin de données du coffre conserve les données pendant 24 mois dans le magasin de données d’archive, puis les supprime.

**Cycle de vie de conservation :**

```json
"lifecycles": [
        {
          "deleteAfter": {
            "duration": "P3M",
            "objectType": "AbsoluteDeleteOption"
          },
          "sourceDataStore": {
            "dataStoreType": "VaultStore",
            "objectType": "DataStoreInfoBase"
          },
          "targetDataStoreCopySettings": []
        }
      ],
      "name": "Default",
      "objectType": "AzureRetentionRule"
    },
    {
      "isDefault": false,
      "lifecycles": [
        {
          "deleteAfter": {
            "duration": "P6M",
            "objectType": "AbsoluteDeleteOption"
          },
          "sourceDataStore": {
            "dataStoreType": "VaultStore",
            "objectType": "DataStoreInfoBase"
          },
          "targetDataStoreCopySettings": {
            "copyAfter": {
              "objectType": "CopyOnExpiryOption"
            },
            "dataStore": {
              "dataStoreType": "ArchiveStore",
              "objectType": "DataStoreInfoBase"
            }
          }
        },
        {
          "deleteAfter": {
            "duration": "P24M",
            "objectType": "AbsoluteDeleteOption"
          },
          "sourceDataStore": {
            "dataStoreType": "ArchiveStore",
            "objectType": "DataStoreInfoBase"
          },
          "targetDataStoreCopySettings": null
        }
      ],
      "name": "Monthly",
      "objectType": "AzureRetentionRule"
    }
```

Chaque fois que vous ajoutez une règle de conservation, vous devez ajouter une étiquette correspondante dans la propriété *Trigger* de la stratégie. L’exemple suivant crée une *étiquette* avec le critère (qui est la première sauvegarde réussie du mois) qui a le même nom que la règle de conservation correspondante à appliquer. 

Dans notre exemple, le critère d’étiquette doit être nommé *Monthly*.

**Critère d’étiquetage :**

```json
{
  "criteria": [
    {
      "absoluteCriteria": [
        "FirstOfMonth"
      ],
      "objectType": "ScheduleBasedBackupCriteria"
    }
  ],
  "isDefault": false,
  "tagInfo": {
    "tagName": "Monthly"
  },
  "taggingPriority": 15
}
```

Une fois tous les changements ajoutés, le JSON de la stratégie ressemble au suivant :

```json
{
  "datasourceTypes": [
    "Microsoft.DBforPostgreSQL/servers/databases"
  ],
  "name": "OssPolicy1",
  "objectType": "BackupPolicy",
  "policyRules": [
    {
      "backupParameters": {
        "backupType": "Full",
        "objectType": "AzureBackupParams"
      },
      "dataStore": {
        "dataStoreType": "VaultStore",
        "objectType": "DataStoreInfoBase"
      },
      "name": "BackupWeekly",
      "objectType": "AzureBackupRule",
      "trigger": {
        "objectType": "ScheduleBasedTriggerContext",
        "schedule": {
          "repeatingTimeIntervals": [
            "R/2021-08-15T22:00:00+00:00/P1W",
            "R/2021-08-18T22:00:00+00:00/P1W",
            "R/2021-08-20T22:00:00+00:00/P1W"
          ],
          "timeZone": "UTC"
        },
        "taggingCriteria": [
          {
            "isDefault": true,
            "tagInfo": {
              "id": "Default_",
              "tagName": "Default"
            },
            "taggingPriority": 99
          },
          {
            "criteria": [
              {
                "absoluteCriteria": [
                  "FirstOfMonth"
                ],
                "objectType": "ScheduleBasedBackupCriteria"
              }
            ],
            "isDefault": false,
            "tagInfo": {
              "tagName": "Monthly"
            },
            "taggingPriority": 15
          }
        ]
      }
    },
    {
      "isDefault": true,
      "lifecycles": [
        {
          "deleteAfter": {
            "duration": "P3M",
            "objectType": "AbsoluteDeleteOption"
          },
          "sourceDataStore": {
            "dataStoreType": "VaultStore",
            "objectType": "DataStoreInfoBase"
          },
          "targetDataStoreCopySettings": []
        }
      ],
      "name": "Default",
      "objectType": "AzureRetentionRule"
    },
    {
      "isDefault": false,
      "lifecycles": [
        {
          "deleteAfter": {
            "duration": "P6M",
            "objectType": "AbsoluteDeleteOption"
          },
          "sourceDataStore": {
            "dataStoreType": "VaultStore",
            "objectType": "DataStoreInfoBase"
          },
          "targetDataStoreCopySettings": {
            "copyAfter": {
              "objectType": "CopyOnExpiryOption"
            },
            "dataStore": {
              "dataStoreType": "ArchiveStore",
              "objectType": "DataStoreInfoBase"
            }
          }
        },
        {
          "deleteAfter": {
            "duration": "P24M",
            "objectType": "AbsoluteDeleteOption"
          },
          "sourceDataStore": {
            "dataStoreType": "ArchiveStore",
            "objectType": "DataStoreInfoBase"
          },
          "targetDataStoreCopySettings": null
        }
      ],
      "name": "Monthly",
      "objectType": "AzureRetentionRule"
    }
  ]
}

```

Pour plus d’informations sur la création de stratégie, consultez le document sur la [stratégie de sauvegarde de base de données PostGreSQL](backup-azure-database-postgresql.md#create-backup-policy).

## <a name="responses"></a>Réponses

La création/mise à jour d’une stratégie de sauvegarde est une opération synchrone, qui retourne _OK_ une fois l’opération réussie.

|Nom  |Type  |Description  |
|---------|---------|---------|
|200 OK     |     [BaseBackupPolicyResource](/rest/api/dataprotection/backup-policies/create-or-update#basebackuppolicyresource)     |  Ok       |

### <a name="example-responses"></a>Exemples de réponses

Une fois l’opération terminée, elle retourne 200 (OK) avec le contenu de la stratégie dans le corps de la réponse.

```json
{
    "properties": {
        "policyRules": [
            {
                "backupParameters": {
                    "backupType": "Full",
                    "objectType": "AzureBackupParams"
                },
                "trigger": {
                    "schedule": {
                        "repeatingTimeIntervals": [
                            "R/2021-08-15T22:00:00+00:00/P1W",
                            "R/2021-08-18T22:00:00+00:00/P1W",
                            "R/2021-08-20T22:00:00+00:00/P1W"
                        ],
                        "timeZone": "UTC"
                    },
                    "taggingCriteria": [
                        {
                            "tagInfo": {
                                "tagName": "Monthly",
                                "id": "Monthly_"
                            },
                            "taggingPriority": 15,
                            "isDefault": false,
                            "criteria": [
                                {
                                    "absoluteCriteria": [
                                        "FirstOfMonth"
                                    ],
                                    "objectType": "ScheduleBasedBackupCriteria"
                                }
                            ]
                        },
                        {
                            "tagInfo": {
                                "tagName": "Default",
                                "id": "Default_"
                            },
                            "taggingPriority": 99,
                            "isDefault": true
                        }
                    ],
                    "objectType": "ScheduleBasedTriggerContext"
                },
                "dataStore": {
                    "dataStoreType": "VaultStore",
                    "objectType": "DataStoreInfoBase"
                },
                "name": "BackupWeekly",
                "objectType": "AzureBackupRule"
            },
            {
                "lifecycles": [
                    {
                        "deleteAfter": {
                            "objectType": "AbsoluteDeleteOption",
                            "duration": "P6M"
                        },
                        "targetDataStoreCopySettings": [
                            {
                                "dataStore": {
                                    "dataStoreType": "ArchiveStore",
                                    "objectType": "DataStoreInfoBase"
                                },
                                "copyAfter": {
                                    "objectType": "CopyOnExpiryOption"
                                }
                            }
                        ],
                        "sourceDataStore": {
                            "dataStoreType": "VaultStore",
                            "objectType": "DataStoreInfoBase"
                        }
                    },
                    {
                        "deleteAfter": {
                            "objectType": "AbsoluteDeleteOption",
                            "duration": "P24M"
                        },
                        "targetDataStoreCopySettings": [],
                        "sourceDataStore": {
                            "dataStoreType": "ArchiveStore",
                            "objectType": "DataStoreInfoBase"
                        }
                    }
                ],
                "isDefault": false,
                "name": "Monthly",
                "objectType": "AzureRetentionRule"
            },
            {
                "lifecycles": [
                    {
                        "deleteAfter": {
                            "objectType": "AbsoluteDeleteOption",
                            "duration": "P3M"
                        },
                        "targetDataStoreCopySettings": [],
                        "sourceDataStore": {
                            "dataStoreType": "VaultStore",
                            "objectType": "DataStoreInfoBase"
                        }
                    }
                ],
                "isDefault": true,
                "name": "Default",
                "objectType": "AzureRetentionRule"
            }
        ],
        "datasourceTypes": [
            "Microsoft.DBforPostgreSQL/servers/databases"
        ],
        "objectType": "BackupPolicy"
    },
    "id": "/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/DebRG1/providers/Microsoft.DataProtection/backupVaults/DebBackupVault/backupPolicies/OssPolicy1",
    "name": "OssPolicy1",
    "type": "Microsoft.DataProtection/backupVaults/backupPolicies"
}
```

## <a name="next-steps"></a>Étapes suivantes

[Activer la protection des disques Azure](backup-azure-dataprotection-use-rest-api-backup-disks.md)

Pour plus d’informations sur les API REST de Sauvegarde Azure, voir les articles suivants :

- [API REST de protection des données Azure](/rest/api/dataprotection/)
- [Bien démarrer avec l’API REST Azure](/rest/api/azure/)
