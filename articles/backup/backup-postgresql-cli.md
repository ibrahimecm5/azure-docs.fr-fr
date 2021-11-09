---
title: Sauvegarde d’Azure Database pour PostgreSQL avec rétention à long terme à l’aide d’Azure CLI
description: Découvrez comment sauvegarder une base de données Azure PostgreSQL avec Azure CLI.
ms.topic: conceptual
ms.date: 10/24/2021
ms.custom: devx-track-azurecli
ms.author: v-amallick
ms.openlocfilehash: ad6602c5a7b9307643703aef87f29738cd6d99fa
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131098180"
---
# <a name="back-up-azure-postgresql-databases-using-azure-cli"></a>Sauvegarder des bases de données Azure PostgreSQL avec Azure CLI

Cet article explique comment sauvegarder une [base de données Azure PostgreSQL](../postgresql/overview.md#azure-database-for-postgresql---single-server) avec Azure CLI.

Dans cet article, vous allez apprendre à :

-  Créer un coffre de sauvegarde
-  Créer une stratégie de sauvegarde
-  Configurer une sauvegarde d’une base de données Azure PostgreSQL
-  Exécuter un travail de sauvegarde à la demande

Pour plus d’informations sur les scénarios pris en charge et les limitations des bases de données Azure PostgreSQL, consultez la [matrice de prise en charge](backup-azure-database-postgresql-overview.md#support-matrix).

## <a name="create-a-backup-vault"></a>Créer un coffre de sauvegarde

Un coffre de sauvegarde est une entité de stockage dans Azure qui stocke les données de sauvegarde de diverses nouvelles charges de travail prises en charge par Sauvegarde Azure, telles que des serveurs Azure Database pour PostgreSQL, des blobs dans un compte de stockage et des disques Azure. Il vous aide à organiser vos données de sauvegarde tout en réduisant la charge de gestion. Les coffres Sauvegarde sont basés sur le modèle Azure Resource Manager, qui fournit des fonctionnalités améliorées pour sécuriser les données de sauvegarde.

Avant de créer un coffre Sauvegarde, choisissez la redondance de stockage des données dans le coffre. Ensuite, procédez à la création du coffre Sauvegarde avec cette redondance de stockage et l’emplacement.

Dans cet article, nous allons créer un coffre Sauvegarde _TestBkpVault_ dans la région _westus_ sous le groupe de ressources _testBkpVaultRG_. Utilisez la commande [az dataprotection vault create](/cli/azure/dataprotection/backup-vault?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_vault_create) pour créer un coffre Sauvegarde. Pour plus d’informations, consultez [Création d’un coffre Sauvegarde](./backup-vault-overview.md#create-a-backup-vault).

```azurecli-interactive
az dataprotection backup-vault create -g testBkpVaultRG --vault-name TestBkpVault -l westus --type SystemAssigned --storage-settings datastore-type="VaultStore" type="LocallyRedundant"

{
  "eTag": null,
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/testBkpVaultRG/providers/Microsoft.DataProtection/BackupVaults/TestBkpVault",
  "identity": {
    "principalId": "2ca1d5f7-38b3-4b61-aa45-8147d7e0edbc",
    "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "type": "SystemAssigned"
  },
  "location": "westus",
  "name": "TestBkpVault",
  "properties": {
    "provisioningState": "Succeeded",
    "storageSettings": [
      {
        "datastoreType": "VaultStore",
        "type": "LocallyRedundant"
      }
    ]
  },
  "resourceGroup": "testBkpVaultRG",
  "systemData": null,
  "tags": null,
  "type": "Microsoft.DataProtection/backupVaults"
}
```

Après la création du coffre, nous allons élaborer une stratégie de sauvegarde pour protéger les bases de données Azure PostgreSQL.

## <a name="create-a-backup-policy"></a>Créer une stratégie de sauvegarde

### <a name="understanding-postgresql-backup-policy"></a>Présentation de la stratégie de sauvegarde PostgreSQL

Tandis que la sauvegarde sur disque permet plusieurs sauvegardes par jour et que la sauvegarde d’objets blob constitue une sauvegarde _continue_ sans déclencheur, la sauvegarde PostgreSQL offre une protection de l’archive. Les données de sauvegarde qui sont envoyées pour la première fois au coffre peuvent être déplacées vers le niveau _archive_, en fonction d’une règle définie ou d’un _cycle de vie_. Dans ce contexte, nous allons comprendre l’objet de stratégie de sauvegarde pour PostgreSQL.

- PolicyRule
   -  BackupRule
      - BackupParameter
        -  BackupType (sauvegarde complète de base de données dans ce cas)
        -  Magasin de données initial (emplacement initial des sauvegardes)
        -  Déclencheur (mode de déclenchement de la sauvegarde)
           -  Suivant une planification
           -  Critères de marquage par défaut (« étiquette » par défaut pour toutes les sauvegardes planifiées, qui lie les sauvegardes à la règle de rétention)
   -  Règle de rétention par défaut (règle appliquée à toutes les sauvegardes, par défaut, sur le magasin de données initial)

Par conséquent, cet objet définit le type des sauvegardes déclenchées, leur mode de déclenchement (suivant une planification), leur marquage, leur emplacement (magasin de données) et le cycle de vie des données de sauvegarde dans un magasin de données. L’objet PowerShell par défaut pour PostgreSQL indique de déclencher une sauvegarde *complète* toutes les semaines. Les données arriveront dans le coffre, où elles seront stockées pendant trois mois.

Pour ajouter le niveau *archive* à la stratégie, vous devez choisir quand les données sont déplacées du coffre vers l’archive, combien de temps elles restent dans l’archive et quelles sauvegardes planifiées doivent être étiquetées comme _archivables_. Par conséquent, vous devez ajouter une *règle de rétention*, dans laquelle vous définissez le cycle de vie des données de sauvegarde entre le magasin de données du coffre et le magasin de données d’archive et la durée pendant laquelle elles sont conservées dans le magasin de données *archive*. Vous devez ensuite ajouter une *étiquette* qui marque les sauvegardes planifiées comme _admissibles à l’archivage_.

L’objet PowerShell qui en résulte est le suivant :

-  PolicyRule
   -  BackupRule
      - BackupParameter
        -  BackupType (sauvegarde complète de base de données dans ce cas)
        -  Magasin de données initial (emplacement initial des sauvegardes)
        -  Déclencheur (mode de déclenchement de la sauvegarde)
           -  Suivant une planification
           -  Critères de marquage par défaut (« étiquette » par défaut pour toutes les sauvegardes planifiées, qui lie les sauvegardes à la règle de rétention)
           -  Nouveaux critères de marquage de la nouvelle règle de rétention portant le même nom (« X »)
   -  Règle de rétention par défaut (règle appliquée à toutes les sauvegardes, par défaut, sur le magasin de données initial)
   -  Nouvelle règle de rétention nommée « X »
      -  Cycle de vie
         -  Magasin de données source
         -  Suppression après une certaine période dans le magasin de données source
         -  Copie dans le magasin de données cible

### <a name="retrieve-the-policy-template"></a>Récupérer le modèle de stratégie

Pour comprendre les composants internes d’une stratégie de sauvegarde pour la sauvegarde d’une base de données Azure PostgreSQL, récupérez le modèle de stratégie à l’aide de la commande [az dataprotection backup-policy get-default-policy-template](/cli/azure/dataprotection/backup-policy?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_policy_get_default_policy_template). Cette commande retourne un modèle de stratégie par défaut pour un type de source de données donné. Utilisez ce modèle de stratégie pour créer une nouvelle stratégie.

```azurecli-interactive
az dataprotection backup-policy get-default-policy-template --datasource-type AzureDatabaseForPostgreSQL
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

Le modèle de stratégie est constitué d’un déclencheur (qui détermine le déclenchement de la sauvegarde) et d’un cycle de vie (qui décide quand supprimer/copier/déplacer la sauvegarde). Dans la sauvegarde de base de données Azure PostgreSQL, la valeur par défaut du déclencheur est un déclencheur hebdomadaire planifié (une sauvegarde tous les 7 jours) et la rétention de chaque sauvegarde pendant trois mois.

**Déclencheur planifié :**

```json
"trigger": {
        "objectType": "ScheduleBasedTriggerContext",
        "schedule": {
          "repeatingTimeIntervals": [
            "R/2021-08-15T06:30:00+00:00/P1W"
          ],
          "timeZone": "UTC"
        }
```

**Cycle de vie par défaut de la règle de rétention :**

```json
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
```

### <a name="modifying-the-policy-template"></a>Modification du modèle de stratégie

> [!IMPORTANT]
> Dans Azure PowerShell, les _objets_ peuvent être utilisés comme emplacements intermédiaires pour effectuer toutes les modifications. Dans Azure CLI, nous devons utiliser des fichiers, car il n’existe aucune notion d’*objets*. Chaque opération de modification doit être redirigée vers un nouveau fichier, où le contenu est lu à partir du fichier d’entrée et redirigé vers le fichier de sortie. Vous pouvez ensuite renommer le fichier selon vos besoins lors de son utilisation dans un script.

#### <a name="modify-the-schedule"></a>Modification de la planification

Le modèle de stratégie par défaut comporte une sauvegarde par semaine. Vous pouvez modifier la planification de la sauvegarde pour qu’elle se produise plusieurs jours par semaine. Pour modifier la planification, utilisez la commande [az dataprotection backup-policy trigger set](/cli/azure/dataprotection/backup-policy/trigger?view=azure-cli-latest#az_dataprotection_backup_policy_trigger_set&preserve-view=true).

Dans l’exemple suivant, la sauvegarde hebdomadaire est modifiée de façon à se produire le dimanche, le mercredi et le vendredi toutes les semaines. Le tableau des dates de planification mentionne les dates. Les jours de la semaine de ces dates sont pris comme jours de la semaine. Vous devez également spécifier que ces planifications se répètent chaque semaine. Par conséquent, l’intervalle de planification est « 1 » et le type d’intervalle est « Weekly » (Hebdomadaire).

```azurecli
az dataprotection backup-policy trigger create-schedule --interval-type Weekly --interval-count 1 --schedule-days 2021-08-15T22:00:00 2021-08-18T22:00:00 2021-08-20T22:00:00
[
  "R/2021-08-15T22:00:00+00:00/P1W",
  "R/2021-08-18T22:00:00+00:00/P1W",
  "R/2021-08-20T22:00:00+00:00/P1W"
]

az dataprotection backup-policy trigger set --policy .\OSSPolicy.json  --schedule R/2021-08-15T22:00:00+00:00/P1W R/2021-08-18T22:00:00+00:00/P1W R/2021-08-20T22:00:00+00:00/P1W > EditedOSSPolicy.json
```

#### <a name="add-a-new-retention-rule"></a>Ajout d’une nouvelle règle de rétention

Si vous souhaitez ajouter la protection de *l’archive*, vous devez modifier le modèle de stratégie comme indiqué ci-dessous.

Le modèle par défaut comporte un cycle de vie pour le magasin de données initial sous la règle de rétention par défaut. Dans ce scénario, la règle indique de supprimer les données de sauvegarde au bout de trois mois. Ajoutez une nouvelle règle de rétention qui définit le moment où les données sont *déplacées* vers le magasin de données *d’archivage*. Autrement dit, les données de sauvegarde sont d’abord copiées vers le magasin de données d’archivage, puis supprimées dans le magasin de données du coffre. En outre, la règle doit définir la durée pendant laquelle les données sont conservées dans le magasin de données *d’archivage*. Utilisez la commande [az dataprotection backup-policy retention-rule create-lifecycle](/cli/azure/dataprotection/backup-policy/retention-rule?view=azure-cli-latest#az_dataprotection_backup_policy_retention_rule_create_lifecycle&preserve-view=true) pour créer de nouveaux cycles de vie, et utilisez la commande [az dataprotection backup-policy retention-rule set](/cli/azure/dataprotection/backup-policy/retention-rule?view=azure-cli-latest#az_dataprotection_backup_policy_retention_rule_set&preserve-view=true) pour les associer aux nouvelles règles ou aux règles existantes.

Dans l’exemple suivant, une règle de rétention nommée *Monthly* (Mensuelle) est créée : la première sauvegarde réussie de chaque mois doit être conservée dans le coffre pendant 6 mois, déplacée vers le niveau archive et conservée dans ce niveau pendant 24 mois.

```azurecli
az dataprotection backup-policy retention-rule create-lifecycle --retention-duration-count 6 --retention-duration-type Months --source-datastore VaultStore --target-datastore ArchiveStore --copy-option CopyOnExpiryOption > VaultToArchiveLifeCycle.JSON

az dataprotection backup-policy retention-rule create-lifecycle --retention-duration-count 24 --retention-duration-type Months -source-datastore ArchiveStore > OnArchiveLifeCycle.JSON

az dataprotection backup-policy retention-rule set --lifecycles .\VaultToArchiveLifeCycle.JSON .\OnArchiveLifeCycle.JSON --name Monthly --policy .\EditedOSSPolicy.JSON > AddedRetentionRulePolicy.JSON
```

#### <a name="add-a-tag-and-the-relevant-criteria"></a>Ajout d’une étiquette et des critères pertinents

Une fois qu’une règle de rétention est a été créée, vous devez créer une *étiquette* correspondante dans la propriété *Déclencheur* de la stratégie de sauvegarde. Utilisez la commande [az dataprotection backup-policy tag create-absolute-criteria](/cli/azure/dataprotection/backup-policy/tag?view=azure-cli-latest#az_dataprotection_backup_policy_tag_create_absolute_criteria&preserve-view=true) pour créer un critère de marquage, et utilisez la commande [az dataprotection backup-policy tag set](/cli/azure/dataprotection/backup-policy/tag?view=azure-cli-latest#az_dataprotection_backup_policy_tag_set&preserve-view=true) pour mettre à jour l’étiquette existante ou créer une nouvelle étiquette.

Dans l’exemple suivant, une *étiquette* est créée avec les critères (première sauvegarde réussie du mois) sous le même nom que la règle de rétention correspondante à appliquer.

Dans cet exemple, les critères d’étiquette doivent être nommés *Monthly*.

```azurecli
az dataprotection backup-policy tag create-absolute-criteria --absolute-criteria FirstOfMonth > tagCriteria.JSON
az dataprotection backup-policy tag set --criteria .\tagCriteria.JSON --name Monthly --policy .\AddedRetentionRulePolicy.JSON > AddedRetentionRuleAndTag.JSON
```

Supposons que la planification se compose de plusieurs sauvegardes par semaine (tous les dimanches, mercredis et jeudis comme dans l’exemple ci-dessus) et que vous souhaitez archiver les sauvegardes du dimanche et du vendredi : vous pouvez modifier les critères de marquage comme suit, en utilisant la commande [az dataprotection backup-policy tag create-generic-criteria](/cli/azure/dataprotection/backup-policy/tag?view=azure-cli-latest#az_dataprotection_backup_policy_tag_create_generic_criteria&preserve-view=true).

```azurecli
az dataprotection backup-policy tag create-generic-criteria --days-of-week Sunday Friday > tagCriteria.JSON
az dataprotection backup-policy tag set --criteria .\tagCriteria.JSON --name Monthly --policy .\AddedRetentionRulePolicy.JSON > AddedRetentionRuleAndTag.JSON
```

### <a name="create-a-new-postgresql-backup-policy"></a>Création d’une stratégie de sauvegarde PostgreSQL

Une fois le modèle modifié conformément aux exigences, utilisez la commande [az dataprotection backup-policy create](/cli/azure/dataprotection/backup-policy?view=azure-cli-latest#az_dataprotection_backup_policy_create&preserve-view=true) pour créer une stratégie avec ce modèle.

```azurecli
az dataprotection backup-policy create --backup-policy-name FinalOSSPolicy --policy AddedRetentionRuleAndTag.JSON --resource-group testBkpVaultRG --vault-name TestBkpVault
```

## <a name="configure-backup"></a>Configurer une sauvegarde

Une fois le coffre et la stratégie créés, il y a trois points critiques que l’utilisateur doit prendre en compte pour protéger une base de données Azure PostgreSQL.

### <a name="key-entities-involved"></a>Entités clés impliquées

#### <a name="postgresql-database-to-be-protected"></a>Base de données PostgreSQL à protéger

Récupérez l’ID Azure Resource Manager (ARM) de la base de données PostgreSQL à protéger. Il s’agit de l’identificateur de la base de données. Nous allons prendre un exemple de base de données nommé **empdb11** sous un serveur PostgreSQL **testposgresql**, qui est présent dans le groupe de ressources **ossrg** sous un autre abonnement.

L’exemple suivant utilise bash.

```azurecli
ossId="/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/ossrg/providers/Microsoft.DBforPostgreSQL/servers/archive-postgresql-ccy/databases/empdb11"
```

#### <a name="azure-key-vault"></a>Coffre de clés Azure

Le service Sauvegarde Azure ne stocke pas le nom d’utilisateur ni le mot de passe permettant de se connecter à la base de données PostgreSQL. Au lieu de cela, l’administrateur de sauvegarde doit amorcer les *clés* dans le coffre de clés. Ensuite, le service Sauvegarde accède au coffre de clés, lit les clés puis accède à la base de données. Notez l’identificateur de secret de la clé concernée.

L’exemple suivant utilise bash.

```azure-cli
keyURI="https://testkeyvaulteus.vault.azure.net/secrets/ossdbkey"
```

#### <a name="backup-vault"></a>Archivage de sauvegarde

Le coffre de sauvegarde doit se connecter au serveur PostgreSQL, puis accéder à la base de données via les clés présentes dans le coffre de clés. Il lui faut par conséquent un accès au serveur PostgreSQL et au coffre de clés. Cet accès est accordé au MSI du coffre Sauvegarde.

[Familiarisez-vous avec les autorisations](/azure/backup/backup-azure-database-postgresql-overview#set-of-permissions-needed-for-azure-postgresql-database-backup) que vous devez accorder au MSI du coffre de sauvegarde sur le serveur PostgreSQL et le coffre de clés Azure, où sont stockées les clés de la base de données.

### <a name="prepare-the-request"></a>Préparer la requête

Une fois que toutes les autorisations appropriées sont définies, la configuration de la sauvegarde est effectuée en deux étapes.

1. Nous préparons la demande pertinente en utilisant le coffre, la stratégie et la base de données PostgreSQL appropriés à l’aide de la commande [az dataprotection backup-instance initialize](/cli/azure/dataprotection/backup-instance?view=azure-cli-latest#az_dataprotection_backup_instance_initialize&preserve-view=true).
1. Nous soumettons la requête visant à protéger la base de données à l’aide de la commande [az dataprotection backup-instance create](/cli/azure/dataprotection/backup-instance?view=azure-cli-latest#az_dataprotection_backup_instance_create&preserve-view=true).

```azurecli
az dataprotection backup-instance initialize --datasource-id $ossId --datasource-type AzureDatabaseForPostgreSQL -l <vault-location> --policy-id <policy_arm_id>  --secret-store-type AzureKeyVault --secret-store-uri $keyURI > OSSBkpInstance.JSON

az dataprotection backup-instance create --resource-group testBkpVaultRG --vault-name TestBkpVault TestBkpvault --backup-instance .\OSSBkpInstance.JSON
```

## <a name="run-an-on-demand-backup"></a>Exécuter une sauvegarde à la demande

Vous devez spécifier une règle de rétention lorsque vous déclenchez une sauvegarde. Pour afficher les règles de rétention dans la stratégie, accédez au fichier JSON de stratégie des règles de rétention. Dans l’exemple suivant, il existe deux règles de rétention nommées **Default** et **Monthly**. Nous utiliserons la règle **Monthly** pour la sauvegarde à la demande.

```azurecli
az dataprotection backup-policy show  -g ossdemorg --vault-name ossdemovault-1 --subscription e3d2d341-4ddb-4c5d-9121-69b7e719485e --name osspol5
{
  "id": "/subscriptions/e3d2d341-4ddb-4c5d-9121-69b7e719485e/resourceGroups/ossdemorg/providers/Microsoft.DataProtection/backupVaults/ossdemovault-1/backupPolicies/osspol5",
  "name": "osspol5",
  "properties": {
    "datasourceTypes": [
      "Microsoft.DBforPostgreSQL/servers/databases"
    ],
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
              "R/2020-04-04T20:00:00+00:00/P1W",
              "R/2020-04-01T20:00:00+00:00/P1W"
            ],
            "timeZone": "UTC"
          },
          "taggingCriteria": [
            {
              "criteria": [
                {
                  "absoluteCriteria": [
                    "FirstOfMonth"
                  ],
                  "daysOfMonth": null,
                  "daysOfTheWeek": null,
                  "monthsOfYear": null,
                  "objectType": "ScheduleBasedBackupCriteria",
                  "scheduleTimes": null,
                  "weeksOfTheMonth": null
                }
              ],
              "isDefault": false,
              "tagInfo": {
                "eTag": null,
                "id": "Monthly_",
                "tagName": "Monthly"
              },
              "taggingPriority": 15
            },
            {
              "criteria": null,
              "isDefault": true,
              "tagInfo": {
                "eTag": null,
                "id": "Default_",
                "tagName": "Default"
              },
              "taggingPriority": 99
            }
          ]
        }
      },
      {
        "isDefault": false,
        "lifecycles": [
          {
            "deleteAfter": {
              "duration": "P10Y",
              "objectType": "AbsoluteDeleteOption"
            },
            "sourceDataStore": {
              "dataStoreType": "VaultStore",
              "objectType": "DataStoreInfoBase"
            },
            "targetDataStoreCopySettings": []
          }
        ],
        "name": "Monthly",
        "objectType": "AzureRetentionRule"
      },
      {
        "isDefault": true,
        "lifecycles": [
          {
            "deleteAfter": {
              "duration": "P1Y",
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
  },
  "resourceGroup": "ossdemorg",
  "systemData": null,
  "type": "Microsoft.DataProtection/backupVaults/backupPolicies"
}
```

## <a name="trigger-an-on-demand-backup-using-command"></a>Déclencher une sauvegarde à la demande à l’aide d’une commande

Déclenchez une sauvegarde à la demande à l’aide de la commande [az dataprotection backup-instance adhoc-backup](/cli/azure/dataprotection/backup-instance?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_adhoc_backup).

```azurecli
az dataprotection backup-instance adhoc-backup --name "ossrg-empdb11" --rule-name "Monthly" --resource-group testBkpVaultRG --vault-name TestBkpVault
```

## <a name="track-jobs"></a>Suivre les travaux

Effectuez le suivi de tous les travaux à l’aide de la commande [az dataprotection job list](/cli/azure/dataprotection/job?view=azure-cli-latest&preserve-view=true#az_dataprotection_job_list). Vous pouvez répertorier tous les travaux et extraire le détail d’un travail particulier.

Vous pouvez également utiliser _Az.ResourceGraph_ pour suivre tous les travaux dans l’ensemble des coffres de sauvegarde. Utilisez la commande [az dataprotection job list-from-resourcegraph](/cli/azure/dataprotection/job?view=azure-cli-latest&preserve-view=true#az_dataprotection_job_list_from_resourcegraph) pour récupérer les travaux pertinents qui se trouvent dans les coffres de sauvegarde.

```azurecli
az dataprotection job list-from-resourcegraph --datasource-type AzureDatabaseForPostgreSQL --status Completed
```

## <a name="next-steps"></a>Étapes suivantes

- [Restaurer une base de données Azure PostgreSQL avec Azure CLI](restore-postgresql-database-cli.md)
