---
title: Événements d’audit Azure SQL Database avec des tables prenant en charge la comptabilité
description: Vue d’ensemble des fonctionnalités d’audit du Registre Azure SQL Database
ms.date: 09/09/2021
ms.service: sql-database
ms.subservice: security
ms.reviewer: vanto
ms.topic: conceptual
author: rothja
ms.author: jroth
ms.openlocfilehash: 18e3c5ef5d9cb0316118e38f2ffb8dccc8c06fd7
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132061867"
---
# <a name="azure-sql-database-audit-events-with-ledger-enabled-tables"></a>Événements d’audit du Registre Azure SQL Database avec des tables prenant en charge la comptabilité

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> Le Registre Azure SQL Database est actuellement en préversion publique.

Quand vous effectuez des activités de forensique avec des tables prenant en charge les registres, les données sont capturées dans la vue de registre et dans le registre des bases de données. D’autres ID d’action sont ajoutés aux journaux d’audit SQL. Les tableaux suivants décrivent ces nouveaux événements de journalisation d’audit. Les conditions qui déclenchent les événements sont indiquées après chaque tableau.

## <a name="enable-ledger"></a>Activer le registre

| Colonne | Valeur |
|--|--|
| **action_id** | ENLR |
| **name** | ACTIVER LE REGISTRE  |
| **class_desc** | OBJECT |
| **covering_action_desc** | NULL |
| **parent_class_desc** | DATABASE |
| **covering_parent_action_name** | LEDGER_OPERATION_GROUP |
| **configuration_level** | NULL |
| **configuration_group_name** | LEDGER_OPERATION_GROUP |
| **action_in_log** | 1 |

**Conditions qui déclenchent l’événement** : quand vous créez une table de registre ou convertissez une table classique en table de registre.

## <a name="alter-ledger"></a>Modifier le registre

| Colonne | Valeur |
|--|--|
| **action_id** | ALLR |
| **name** | MODIFIER LE REGISTRE |
| **class_desc** | OBJECT |
| **covering_action_desc** | NULL |
| **parent_class_desc** | DATABASE |
| **covering_parent_action_name** | LEDGER_OPERATION_GROUP |
| **configuration_level** | NULL |
| **configuration_group_name** | LEDGER_OPERATION_GROUP |
| **action_in_log** | 1 |

**Conditions qui déclenchent l’événement** : quand vous supprimez ou renommez une table de registre, convertissez une table de registre en table classique et ajoutez, supprimez ou renommez une colonne d’une table de registre.


## <a name="generate-ledger-digest"></a>Générer un résumé du registre

| Colonne | Valeur |
|--|--|
| **action_id** | GDLR |
| **name** | GÉNÉRER UN RÉSUMÉ DU REGISTRE |
| **class_desc** | DATABASE |
| **covering_action_desc** | LEDGER_OPERATION_GROUP |
| **parent_class_desc** | SERVER |
| **covering_parent_action_name** | LEDGER_OPERATION_GROUP |
| **configuration_level** | NULL |
| **configuration_group_name** | LEDGER_OPERATION_GROUP  |
| **action_in_log** | 1 |

**Conditions qui déclenchent l’événement** : quand vous générez une synthèse de registre.

## <a name="verify-ledger"></a>Vérifier le registre

| Colonne | Valeur |
|--|--|
| **action_id** | VFLR |
| **name** | VÉRIFIER LE GRAND LIVRE |
| **class_desc** | DATABASE |
| **covering_action_desc** | LEDGER_OPERATION_GROUP |
| **parent_class_desc** | SERVER |
| **covering_parent_action_name** | LEDGER_OPERATION_GROUP |
| **configuration_level** | NULL |
| **configuration_group_name** | LEDGER_OPERATION_GROUP |
| **action_in_log** | 1 |

**Conditions qui déclenchent l’événement** : quand vous vérifiez une synthèse de registre.

## <a name="ledger-operation-group"></a>Groupe d’opérations de registre

| Colonne | Valeur |
|--|--|
| **action_id** | OPLR |
| **name** | LEDGER_OPERATION_GROUP |
| **class_desc** | DATABASE |
| **covering_action_desc** | NULL |
| **parent_class_desc** | SERVER |
| **covering_parent_action_name** | NULL |
| **configuration_level** | GROUP |
| **configuration_group_name** | LEDGER_OPERATION_GROUP |
| **action_in_log** | 0 |

**Conditions qui déclenchent l’événement** : non applicable

| Colonne | Valeur |
|--|--|
| **action_id** | OPLR |
| **name** | LEDGER_OPERATION_GROUP |
| **class_desc** | SERVER |
| **covering_action_desc** | NULL |
| **parent_class_desc** | NULL |
| **covering_parent_action_name** | NULL |
| **configuration_level** | GROUP |
| **configuration_group_name** | LEDGER_OPERATION_GROUP |
| **action_in_log** | 0 |

**Conditions qui déclenchent l’événement** : non applicable 

## <a name="next-steps"></a>Étapes suivantes

- [Audit pour Azure SQL Database et Azure Synapse Analytics](auditing-overview.md)
- [Vue d’ensemble du registre Azure SQL Database](ledger-overview.md)
- [Démarrage rapide : Créer une base de données dans Azure SQL Database avec le registre activé](ledger-create-a-single-database-with-ledger-enabled.md)
