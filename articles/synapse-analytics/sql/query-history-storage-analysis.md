---
title: Analyse et stockage des requêtes historiques dans Azure Synapse Analytics
description: L’analyse des requêtes historiques est l’un des besoins cruciaux des ingénieurs Données. Azure Synapse Analytics prend en charge quatre méthodes principales pour analyser les performances et l’historique des requêtes. Il s’agit notamment du Magasin des requêtes, des vues DMV, d’Azure Log Analytics et d’Azure Data Explorer.
author: mariyaali
ms.author: mariyaali
ms.reviewer: wiassaf
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 10/28/2021
ms.custom: template-concept
ms.openlocfilehash: 9f6493b2011a06a56f64e47711756b1172300885
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132490266"
---
# <a name="historical-query-storage-and-analysis-in-azure-synapse-analytics"></a>Analyse et stockage des requêtes historiques dans Azure Synapse Analytics

L’analyse des requêtes historiques est l’un des besoins cruciaux des ingénieurs Données. Azure Synapse Analytics prend en charge quatre méthodes principales pour analyser les performances et l’historique des requêtes. Il s’agit notamment du Magasin des requêtes, des vues DMV, d’Azure Log Analytics et d’Azure Data Explorer. 

Cet article vous montre comment utiliser chacune de ces options selon vos besoins. Passez en revue les cas d’utilisation concernant l’analyse de l’historique des requêtes et la meilleure méthode pour chacun d’entre eux.

| **Besoin du client** |  **Magasin de requêtes** |  **DMV**    | **Azure Log Analytics** | **Explorateur de données Azure** |
|------------- | --- | ----- | ------------- |-------------------|
|**Solution prête à l’emploi** | Activation requise | :heavy_check_mark: | Autre service nécessaire |    Autre service nécessaire|
|**Périodes d’analyse plus longues** | 30 jours |    Jusqu’à 10 000 lignes d’historique     | Personnalisable | Personnalisable|
|**Disponibilité des métriques essentielles** |    Limité    | :heavy_check_mark: |    Limité    | Personnalisable|
|**Utiliser SQL pour l’analyse** | :heavy_check_mark: | :heavy_check_mark:| KQL nécessaire | La prise en charge de SQL est limitée|
|||||

## <a name="query-store"></a>Magasin des requêtes

La fonctionnalité de Magasin des requêtes fournit des insights sur les performances et le choix du plan de requête. Elle simplifie la résolution des problèmes de performances en vous permettant de trouver rapidement les différences de performances provoquées par des changements de plan de requête. 

Le Magasin des requêtes n’est pas activé par défaut pour les nouvelles bases de données Azure Synapse Analytics. Pour activer le Magasin des requêtes, exécutez la commande T-SQL suivante :

```sql
ALTER DATABASE <database_name>
SET QUERY_STORE = ON;
```

Par exemple :

```sql
ALTER DATABASE [SQLPOOL1]
SET QUERY_STORE = ON;
```

Vous pouvez exécuter des tâches d’audit des performances et de résolution des problèmes en recherchant la dernière requête exécutée, le nombre d’exécutions, les requêtes les plus longues, les requêtes avec des résultats d’E/S physiques maximaux. Reportez-vous à la section [Analyse des performances à l’aide du Magasin des requêtes](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store#performance) pour obtenir des exemples de requêtes.

Avantages :
* Seuil de 30 jours pour les données de requête de stockage.
* Les données peuvent être utilisées dans le même outil que celui dans lequel vous exécutez la requête.

Restriction connue :
* Les scénarios d’analyse sont limités dans le Magasin des requêtes pour Azure Synapse par rapport aux vues DMV.

## <a name="dmvs"></a>DMV

Les vues de gestion dynamique (DMV) sont extrêmement utiles pour collecter des informations sur les temps d’attente de requête, les plans d’exécution, la mémoire, etc. Il est vivement recommandé d’étiqueter votre requête d’intérêt pour effectuer son suivi ultérieurement. Par exemple :

```sql
-- Query with Label
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query');
```

Pour plus d’informations sur l’étiquetage de vos requêtes dans Azure Synapse SQL, consultez [Utiliser des étiquettes de requête dans Synapse SQL](develop-label.md).

Pour plus d’informations sur l’utilisation des vues DMV pour superviser votre charge de travail Azure Synapse Analytics, consultez [Superviser la charge de travail de votre pool SQL dédié à l’aide de vues de gestion dynamique](../sql-data-warehouse/sql-data-warehouse-manage-monitor.md?context=/azure/synapse-analytics/context/context). Pour obtenir de la documentation sur les affichages catalogue spécifiques à Azure Synapse Analytics, consultez [Affichages catalogue Azure Synapse Analytics](/sql/relational-databases/system-catalog-views/sql-data-warehouse-and-parallel-data-warehouse-catalog-views).

Avantages :
* Les données peuvent être utilisées dans le même outil de requête.
* Les vues DMV fournissent des options complètes pour l’analyse.

Limitations connues :
* Les vues DMV sont limitées à 10 000 lignes d’entrées historiques. 
* Les affichages sont réinitialisés quand le pool est suspendu/repris.

## <a name="log-analytics"></a>Log Analytics
Les espaces de travail Log Analytics peuvent être créés facilement dans le portail Azure. Pour obtenir des instructions supplémentaires sur la connexion de Synapse à Log Analytics, consultez [Superviser une charge de travail dans le portail Azure](../sql-data-warehouse/sql-data-warehouse-monitor-workload-portal.md).

Comme Azure Data Explorer, Log Analytics utilise le langage de requête Kusto (KQL). Pour plus d’informations sur la syntaxe Kusto, consultez [Vue d’ensemble des requêtes Kusto](/azure/data-explorer/kusto/query/). 

Avec la période de rétention configurable, vous choisissez l’espace de travail que vous ciblez spécifiquement pour les requêtes dans Log Analytics. Log Analytics vous offre la flexibilité vous permettant de stocker des données, d’exécuter et d’enregistrer des requêtes.

Avantages :
* Azure Log Analytics dispose d’une stratégie de rétention des journaux personnalisable

Limitations connues :
* L’utilisation de KQL étend la courbe d’apprentissage.
* La journalisation des vues limitées est prête à l’emploi.

## <a name="azure-data-explorer-adx"></a>Azure Data Explorer (ADX)

Azure Data Explorer (ADX) est un service d’exploration des données majeur. Ce service peut être utilisé pour analyser les requêtes historiques d’Azure Synapse Analytics. Pour configurer un pipeline Azure Data Factory (ADF) afin de copier et stocker les journaux dans ADX, consultez [Copier des données depuis ou vers Azure Data Explorer](/azure/data-factory/connector-azure-data-explorer). Dans ADX, vous pouvez exécuter une requête Kusto performante pour analyser vos journaux. Vous pouvez combiner d’autres stratégies, par exemple pour interroger et charger la sortie DMV sur ADX via ADF.
  
Avantages :
* ADX fournit une stratégie de rétention de journal personnalisable.
* Exécution performante des requêtes sur une grande quantité de données, en particulier des requêtes impliquant la recherche de chaînes.

Restriction connue :
* L’utilisation de KQL étend la courbe d’apprentissage.

## <a name="next-steps"></a>Étapes suivantes

 - [Explorateur de données Azure](/azure/data-explorer/)
 - [Azure Data Factory](/azure/data-factory/).
 - [Log Analytics dans Azure Monitor](/azure/azure-monitor/logs/log-analytics-overview)
 - [Architecture Azure Synapse SQL](overview-architecture.md)
 - [Bien démarrer avec Azure Synapse Analytics](../get-started.md)
