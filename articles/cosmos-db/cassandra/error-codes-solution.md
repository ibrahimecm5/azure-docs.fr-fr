---
title: Diagnostics de serveur pour l’API Cassandra Azure Cosmos DB
description: Cet article décrit certains codes d’erreur courants dans l’API Cassandra Azure Cosmos DB et comment résoudre les problèmes à l’aide de Log Analytics
author: IriaOsara
ms.author: IriaOsara
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: troubleshooting
ms.date: 10/12/2021
ms.custom: template-how-to
ms.openlocfilehash: bbef336e16623113c45929710e49101bddfdf64e
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131097919"
---
# <a name="server-diagnostics-for-azure-cosmos-db-cassandra-api"></a>Diagnostics de serveur pour l’API Cassandra Azure Cosmos DB
[!INCLUDE[appliesto-cassandra-api](../includes/appliesto-cassandra-api.md)]

Log Analytics est un outil du portail Azure qui vous permet d’exécuter des diagnostics de serveur sur votre compte d’API Cassandra. Exécutez des requêtes de journal à partir des données collectées par les journaux Azure Monitor et analysez leurs résultats de manière interactive. Les enregistrements récupérés à partir des requêtes Log Analytics aident à fournir divers insights sur vos données.

## <a name="prerequisites"></a>Prérequis

- Créez un [espace de travail Log Analytics](../../azure-monitor/logs/quick-create-workspace.md).
- Créez des [paramètres de diagnostic](../cosmosdb-monitor-resource-logs.md).
- Démarrez [Log Analytics](../../azure-monitor/logs/log-analytics-overview.md) sur votre compte d’API Cassandra.

## <a name="use-log-analytics"></a>Utiliser Log Analytics
Une fois que vous avez terminé l’installation de Log Analytics, vous pouvez commencer à explorer vos journaux pour obtenir plus d’insights.

### <a name="explore-data-plane-operations"></a>Explorer des opérations de plan de données
Utilisez la table CDBCassandraRequests pour voir les opérations de plan de données spécifiques à votre compte d’API Cassandra. Exemple de requête pour afficher les topN (10) demandes de consommation et obtenir des informations détaillées sur chaque demande effectuée.

```Kusto
CDBCassandraRequests
| where RequestCharge  > 0
| project DatabaseName, CollectionName, DurationMs, OperationName, ActivityId, ErrorCode, RequestCharge, PIICommandText 
| order by RequestCharge
| take 10
```

#### <a name="error-codes-and-possible-solutions"></a>Codes d’erreur et solutions possibles
|Code d’état | Code d'erreur           | Description  |
|------------|----------------------|--------------|
| 200 | -1 | Réussite |
| 400 | 8704 | La requête est correcte, mais la syntaxe n’est pas valide. |
| 400 | 8 192 | La requête envoyée contient une erreur de syntaxe. Passez en revue votre requête. |
| 400 | 8960 | La requête n’est pas valide en raison d’un problème de configuration. |
| 401 |8448 | L’utilisateur connecté ne dispose pas des autorisations appropriées pour exécuter la requête. |
| 403 | 8448 | Réponse Interdit, car l’utilisateur n’a peut-être pas les autorisations nécessaires pour mener à bien la demande. |
| 404 | 5376 | Exception sans délai d’attente au cours d’une demande d’écriture suite à une réponse introuvable. |
| 405 | 0 | Erreur Cassandra côté serveur. L’erreur se produit rarement, ouvrez un ticket de support. |
| 408 | 4608 | Délai d’attente lors d’une demande de lecture. |
| 408 | 4352 | Exception de délai d’attente lors d’un serviceRequest d’écriture. |
| 409 | 9216 | Tentative de création d’un espace de clés ou d’une table qui existe déjà. |
| 412 | 5376 | Échec de la condition préalable. Pour garantir l’intégrité des données, nous vérifions que la demande d’écriture basée sur la réponse de lecture est vraie. Une exception de demande d’écriture sans délai d’attente est retournée. |
| 413 | 5376 | Cette exception sans délai d’attente au cours d’une demande d’écriture est due à une charge utile peut-être trop importante. Actuellement, il existe une limite de 2 Mo par ligne. |
| 417 | 9472 | L’exception est levée lorsqu’une instruction préparée n’est pas mise en cache sur le nœud serveur. Elle doit être temporaire/non bloquante. |
| 423 | 5376 | Il y a un verrou en raison d’une demande d’écriture en cours de traitement. |
| 429 | 4097| L’exception de surcharge est due à une pénurie de RU ou un taux élevé de demandes. Plus de RU sont probablement nécessaires pour gérer la demande de volume supérieure. Dans Cassandra en mode natif, cela peut être interprété comme l’une des machines virtuelles qui n’a pas suffisamment de ressources processeur. Nous vous recommandons de passer en revue le modèle de données actuel pour vérifier que vous n’avez pas d’asymétries excessives susceptibles d’entraîner des partitions à chaud. |
| 449 | 5376 | Exception d’exécution simultanée. Cela se produit pour garantir une seule mise à jour d’écriture à la fois pour une ligne donnée. |
| 500 | 0 | Erreur Cassandra de serveur : un événement inattendu s’est produit. Cela indique un bogue côté serveur. |
| 503 | 4096 | Service indisponible. |
|   | 256 | Cela peut être dû à des informations d’identification de connexion non valides. Vérifiez vos informations d’identification de connexion. |
|   | 10 | Violation de protocole déclenchée par un message client. Un exemple est le message de requête envoyé avant l’envoi d’un message de démarrage. |

### <a name="troubleshoot-query-consumption"></a>Résoudre les problèmes de consommation des requêtes
La table CDBPartitionKeyRUConsumption contient des détails sur la consommation d’unités de requête (RU) pour les clés logiques dans chaque région au sein de chacune de leurs partitions physiques.

```Kusto
CDBPartitionKeyRUConsumption 
| summarize sum(todouble(RequestCharge)) by PartitionKey, PartitionKeyRangeId
| render columnchart
 ```

### <a name="explore-control-plane-operations"></a>Explorer des opérations de plan de contrôle
La table CBDControlPlaneRequests contient des détails sur les opérations de plan de contrôle, en particulier pour les comptes d’API Cassandra. 

```Kusto
CDBControlPlaneRequests
| where TimeGenerated > now(-6h)
| where  ApiKind == "Cassandra"
| where OperationName in ("Create", "Upsert", "Delete", "Execute")
| summarize by OperationName
 ```

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur [Log Analytics](../../azure-monitor/logs/log-analytics-tutorial.md).
- Découvrez comment [migrer d’Apache Cassandra natif vers l’API Cassandra dans Azure Cosmos DB](migrate-data-databricks.md).