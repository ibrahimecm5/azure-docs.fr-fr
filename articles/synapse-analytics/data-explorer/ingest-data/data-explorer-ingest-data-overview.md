---
title: Présentation de l’ingestion des données Azure Synapse Data Explorer (préversion)
description: Découvrez les différentes manières d’ingérer (charger) des données dans Azure Synapse Data Explorer.
ms.topic: overview
ms.date: 11/02/2021
author: shsagir
ms.author: shsagir
ms.reviewer: tzgitlin
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: data-explorer
ms.openlocfilehash: 739867e66bf111d19226ea99198d4d5efec0eeb5
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131479004"
---
# <a name="azure-synapse-data-explorer-data-ingestion-overview-preview"></a>Présentation de l’ingestion des données Azure Synapse Data Explorer (préversion)

L’ingestion des données est le processus qui consiste à charger des enregistrements de données d’une ou plusieurs sources pour importer des données dans une table du pool Azure Synapse Data Explorer. Une fois ingérées, les données sont disponibles pour les requêtes.

Le service de gestion des données Azure Synapse Data Explorer, qui est responsable de l’ingestion des données, implémente le processus suivant :

- Extrait des données par lot ou en streaming d’une source externe et lit les demandes d’une file d’attente Azure.
- Les données traitées par lot à destination de la même base de données et de la même table sont optimisées pour un débit d’ingestion élevé.
- Les données initiales sont validées et le format est converti si nécessaire.
- Parmi les autres tâches de manipulation des données, citons la mise en correspondance des schémas, l’organisation, l’indexation, l’encodage et la compression des données.
- Les données sont conservées dans le stockage en fonction d’une stratégie de conservation définie.
- Les données ingérées sont validées dans le moteur, où elles sont disponibles pour la requête.

## <a name="supported-data-formats-properties-and-permissions"></a>Formats de données pris en charge, propriétés et autorisations

* **[Formats de données pris en charge](data-explorer-ingest-data-supported-formats.md)**

* **[Propriétés d’ingestion](data-explorer-ingest-data-properties.md)**  : propriétés qui affectent la façon dont les données sont ingérées (par exemple : étiquetage, mappage, heure de création).

* **Autorisations** : pour ingérer des données, le processus nécessite des [autorisations de niveau Ingéreur de base de données](/azure/data-explorer/kusto/management/access-control/role-based-authorization?context=/azure/synapse-analytics/context/context). D’autres actions, comme l’exécution d’une requête, peuvent nécessiter des autorisations d’administrateur de base de données, d’utilisateur de base de données ou d’administrateur de table.

## <a name="batching-vs-streaming-ingestions"></a>Ingestions par lot ou en streaming

* L’ingestion par lot, qui traite les données par lot, est optimisée pour un débit d’ingestion élevé. Cette méthode d’ingestion est la plus performante et donc recommandée. Les données sont traitées par lot en fonction des propriétés d’ingestion. De petits lots de données sont fusionnés et optimisés pour des résultats de requête rapides. La stratégie d’[ingestion par lot](/azure/data-explorer/kusto/management/batchingpolicy?context=/azure/synapse-analytics/context/context) peut être définie sur des bases de données ou des tables. Par défaut, les valeurs maximales de traitement par lot sont les suivantes : 5 minutes, 1000 éléments ou une taille totale de 1 Go.  La taille maximale des données pour une commande d’ingestion par lots est de 4 Go.

* L’[ingestion en streaming](data-explorer-ingest-data-streaming.md) ingère les données en continu à partir d’une source de streaming. L’ingestion en streaming permet une latence en quasi temps réel pour les petits jeux de données par table. Les données sont initialement ingérées dans un rowstore, puis déplacées dans des étendues columnstore.

## <a name="ingestion-methods-and-tools"></a>Méthodes d’ingestion et outils

Azure Synapse Data Explorer prend en charge plusieurs méthodes d’ingestion, chacune avec ses propres scénarios cibles. Parmi ces méthodes, citons des outils d’ingestion, des connecteurs et des plug-ins pour divers services, des pipelines gérés, l’ingestion programmatique à l’aide de kits SDK et l’accès direct à l’ingestion.

### <a name="ingestion-using-managed-pipelines"></a>Ingestion à l’aide de pipelines gérés

Pour les organisations qui souhaitent confier la gestion (limitation, nouvelles tentatives, analyses, alertes, etc.) à un service externe, l’utilisation d’un connecteur est probablement la solution la plus appropriée. L’ingestion en file d’attente convient aux grands volumes de données. Azure Synapse Data Explorer prend en charge les pipelines Azure suivants :

<!-- * **[Event Grid](https://azure.microsoft.com/services/event-grid/)**: A pipeline that listens to Azure storage, and updates Azure Synapse Data Explorer to pull information when subscribed events occur. For more information, see [Ingest Azure Blobs into Azure Synapse Data Explorer](ingest-data-event-grid.md). -->

* **[Event Hub](https://azure.microsoft.com/services/event-hubs/)**  : pipeline qui transfère des événements de services vers Azure Synapse Data Explorer. Pour plus d’informations, consultez [Ingérer des données d’un Event Hub dans Azure Synapse Data Explorer](data-explorer-ingest-event-hub-overview.md).

<!-- * **[IoT Hub](https://azure.microsoft.com/services/iot-hub/)**: A pipeline that is used for the transfer of data from supported IoT devices to Azure Synapse Data Explorer. For more information, see [Ingest from IoT Hub](ingest-data-iot-hub.md). -->

* **Pipelines Synapse** : un service complètement managé d’intégration de données pour les charges de travail analytiques dans [Pipelines Synapse](/azure/data-factory/copy-activity-overview?context=/azure/synapse-analytics/context/context&tabs=synapse-analytics) se connecte à plus de 90 sources prises en charge pour assurer un transfert de données efficace et résilient. Pipelines Synapse prépare, transforme et enrichit les données pour fournir des insights qui peuvent être supervisés de différentes façons. Ce service peut apporter une solution ponctuelle, s’inscrire dans une chronologie périodique ou être déclenché par des événements spécifiques.

<!-- ### Ingestion using connectors and plugins

* **Logstash plugin**, see [Ingest data from Logstash to Azure Synapse Data Explorer](ingest-data-logstash.md).

* **Kafka connector**, see [Ingest data from Kafka into Azure Synapse Data Explorer](ingest-data-kafka.md).

* **[:::no-loc text="Power Automate":::](https://flow.microsoft.com/)**: An automated workflow pipeline to Azure Synapse Data Explorer. :::no-loc text="Power Automate"::: can be used to execute a query and do preset actions using the query results as a trigger. See [Azure Synapse Data Explorer connector to :::no-loc text="Power Automate"::: (Preview)](flow.md).

* **Apache Spark connector**: An open-source project that can run on any Spark cluster. It implements data source and data sink for moving data across Azure Synapse Data Explorer and Spark clusters. You can build fast and scalable applications targeting data-driven scenarios. See [Azure Synapse Data Explorer Connector for Apache Spark](spark-connector.md). -->

### <a name="programmatic-ingestion-using-sdks"></a>Ingestion programmatique à l’aide de kits SDK

Azure Synapse Data Explorer fournit des Kits de développement logiciel (SDK) qui peuvent être utilisés pour l’ingestion de données et les requêtes. L’ingestion par programmation est optimisée afin de réduire les coûts d’ingestion, grâce à la réduction des transactions de stockage pendant et après le processus d’ingestion.

Avant de commencer, procédez comme suit pour obtenir les points de terminaison du pool Data Explorer pour la configuration de l’ingestion programmatique.

[!INCLUDE [data-explorer-get-endpoint](../includes/data-explorer-get-endpoint.md)]

**Kits SDK et projets open source disponibles**

* [Kit de développement logiciel (SDK) Python](/azure/data-explorer/kusto/api/python/kusto-python-client-library?context=/azure/synapse-analytics/context/context)

* [Kit de développement logiciel (SDK) .NET](/azure/data-explorer/kusto/api/netfx/about-the-sdk?context=/azure/synapse-analytics/context/context)

* [Kit SDK Java](/azure/data-explorer/kusto/api/java/kusto-java-client-library?context=/azure/synapse-analytics/context/context)

* [Node SDK](/azure/data-explorer/kusto/api/node/kusto-node-client-library?context=/azure/synapse-analytics/context/context)

* [REST API](/azure/data-explorer/kusto/api/netfx/kusto-ingest-client-rest?context=/azure/synapse-analytics/context/context)

* [SDK GO](/azure/data-explorer/kusto/api/golang/kusto-golang-client-library?context=/azure/synapse-analytics/context/context)

### <a name="tools"></a>Outils

* **[Ingestion en un clic](data-explorer-ingest-data-one-click.md)**  : vous permet d’ingérer rapidement des données en créant et en ajustant des tables à partir d’un large éventail de types sources. L’ingestion en un clic suggère automatiquement des tables et des structures de mappage en fonction de la source de données dans Azure Synapse Data Explorer. L’ingestion en un clic peut constituer une méthode d’ingestion ponctuelle. Elle peut aussi servir à définir l’ingestion continue, par le biais d’Event Grid, sur le conteneur dans lequel les données ont été ingérées.

<!-- * **[LightIngest](lightingest.md)**: A command-line utility for ad-hoc data ingestion into Azure Synapse Data Explorer. The utility can pull source data from a local folder or from an Azure blob storage container. -->

### <a name="kusto-query-language-ingest-control-commands"></a>Commandes de contrôle d’ingestion KQL

Plusieurs méthodes faisant appel à des commandes KQL (Kusto Query Language) permettent d’ingérer directement des données dans le moteur. Étant donné que cette méthode ignore les services Gestion des données, elle ne convient qu’à l’exploration et au prototypage. N’utilisez pas cette méthode dans les scénarios de production ou ceux impliquant de grands volumes.

  * **Ingestion inline** :  une commande de contrôle [.ingest inline](/azure/data-explorer/kusto/management/data-ingestion/ingest-inline?context=/azure/synapse-analytics/context/context) est envoyée au moteur, les données à ingérer étant intégrées au texte de la commande. Cette méthode est destinée à des fins de tests improvisés.

  * **Ingestion à partir d’une requête** : une commande de contrôle [.set, .append, .set-or-append ou .set-or-replace](/azure/data-explorer/kusto/management/data-ingestion/ingest-from-query?context=/azure/synapse-analytics/context/context) est envoyée au moteur, les données étant spécifiées indirectement comme résultats d’une requête ou d’une commande.

  * **Ingestion à partir du stockage - tirage (pull)**  : Une commande de contrôle [.ingest into](/data-explorer/kusto/management/data-ingestion/ingest-from-storage?context=/azure/synapse-analytics/context/context) est envoyée au moteur, les données étant stockées dans un stockage externe (par exemple, Stockage Blob Azure) accessible par le moteur et vers lequel la commande pointe.

Pour obtenir un exemple d’utilisation des commandes de contrôle d’ingestion, consultez [Analyser à l’aide d’Azure Data Explorer](../../get-started-analyze-data-explorer.md).

<!-- ## Comparing ingestion methods and tools

| Ingestion name | Data type | Maximum file size | Streaming, batching, direct | Most common scenarios | Considerations |
| --- | --- | --- | --- | --- | --- |
| [**One click ingestion**](ingest-data-one-click.md) | *sv, JSON | 1 GB uncompressed (see note)| Batching to container, local file and blob in direct ingestion | One-off, create table schema, definition of continuous ingestion with event grid, bulk ingestion with container (up to 10,000 blobs) | 10,000 blobs are randomly selected from container|
| [**LightIngest**](lightingest.md) | All formats supported | 1 GB uncompressed (see note) | Batching via DM or direct ingestion to engine |  Data migration, historical data with adjusted ingestion timestamps, bulk ingestion (no size restriction)| Case-sensitive, space-sensitive |
| [**ADX Kafka**](ingest-data-kafka.md) | | | | |
| [**ADX to Apache Spark**](spark-connector.md) | | | | |
| [**LogStash**](ingest-data-logstash.md) | | | | |
| [**Azure Data Factory (ADF)**](./data-factory-integration.md) | [Supported data formats](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats) | unlimited *(per ADF restrictions) | Batching or per ADF trigger | Supports formats that are usually unsupported, large files, can copy from over 90 sources, from on perm to cloud | This method takes relatively more time until data is ingested. ADF uploads all data to memory and then begins ingestion. |
|[ **Power Automate**](./flow.md) | | | | Ingestion commands as part of flow| Must have high-performing response time |
| [**IoT Hub**](ingest-data-iot-hub-overview.md) | [Supported data formats](ingest-data-iot-hub-overview.md#data-format)  | N/A | Batching, streaming | IoT messages, IoT events, IoT properties | |
| [**Event Hub**](ingest-data-event-hub-overview.md) | [Supported data formats](ingest-data-event-hub-overview.md#data-format) | N/A | Batching, streaming | Messages, events | |
| [**Event Grid**](ingest-data-event-grid-overview.md) | [Supported data formats](ingest-data-event-grid-overview.md#data-format) | 1 GB uncompressed | Batching | Continuous ingestion from Azure storage, external data in Azure storage | Ingestion can be triggered by blob renaming or blob creation actions. |
| [**.NET SDK**](./net-sdk-ingest-data.md) | All formats supported | 1 GB uncompressed (see note) | Batching, streaming, direct | Write your own code according to organizational needs |
| [**Python**](python-ingest-data.md) | All formats supported | 1 GB uncompressed (see note) | Batching, streaming, direct | Write your own code according to organizational needs |
| [**Node.js**](node-ingest-data.md) | All formats supported | 1 GB uncompressed (see note | Batching, streaming, direct | Write your own code according to organizational needs |
| [**Java**](kusto/api/java/kusto-java-client-library.md) | All formats supported | 1 GB uncompressed (see note) | Batching, streaming, direct | Write your own code according to organizational needs |
| [**REST**](kusto/api/netfx/kusto-ingest-client-rest.md) | All formats supported | 1 GB uncompressed (see note) | Batching, streaming, direct| Write your own code according to organizational needs |
| [**Go**](kusto/api/golang/kusto-golang-client-library.md) | All formats supported | 1 GB uncompressed (see note) | Batching, streaming, direct | Write your own code according to organizational needs |

> [!Note]
> When referenced in the above table, ingestion supports a maximum file size of 4 GB. The recommendation is to ingest files between 100 MB and 1 GB. -->

## <a name="ingestion-process"></a>Processus d’ingestion

Une fois que vous avez choisi la méthode d’ingestion la mieux adaptée à vos besoins, effectuez les étapes suivantes :

1. **Définir la stratégie de conservation**

    Les données ingérées dans une table dans Azure Synapse Data Explorer sont soumises à la stratégie de rétention en vigueur de la table. À moins d’être explicitement définie sur une table, la stratégie de conservation effective découle de la stratégie de conservation de la base de données. La conservation à chaud est une fonction de la taille de cluster et de votre stratégie de conservation. Le fait d’ingérer plus de données que d’espace disponible forcera la conservation à froid des premières données entrées.

    Vérifiez que la stratégie de conservation de la base de données est adaptée à vos besoins. Si ce n’est pas le cas, remplacez-la explicitement au niveau de la table. Pour plus d’informations, consultez [Stratégie de conservation](/azure/data-explorer/kusto/management/retentionpolicy?context=/azure/synapse-analytics/context/context).

1. **Création d'une table**

    Avant de pouvoir ingérer des données, vous devez créer une table. Utilisez l’une des options suivantes :

    * Créez une table avec une commande. Pour un exemple d’utilisation de la commande « create a table », consultez [Analyser à l’aide d’Azure Data Explorer](../../get-started-analyze-data-explorer.md).

    * Créez une table à l’aide de l’[ingestion en un clic](data-explorer-ingest-data-one-click.md).

    > [!Note]
    > Si un enregistrement est incomplet ou un champ ne peut pas être analysé en tant que type de données requis, les colonnes correspondantes de la table sont remplies avec des valeurs null.

1. **Créer un mappage de schéma**

    Le [mappage de schéma](/azure/data-explorer/kusto/management/mappings?context=/azure/synapse-analytics/context/context) permet de lier des champs de données sources à des colonnes de table de destination. Le mappage vous permet de regrouper dans une même table les données de différentes sources en fonction des attributs définis. Différents types de mappages sont pris en charge, à la fois orientés ligne (CSV, JSON et AVRO) et orientés colonne (Parquet). Dans la plupart des méthodes, des mappages peuvent également être [précréés sur la table](/azure/data-explorer/kusto/management/create-ingestion-mapping-command?context=/azure/synapse-analytics/context/context) et référencés à partir du paramètre de commande ingest.

1. **Définir une stratégie de mise à jour** (facultatif)

   Certains mappages de format de données (Parquet, JSON et Avro) prennent en charge des transformations simples et utiles au moment de l’ingestion. Si le scénario nécessite un traitement plus complexe au moment de l’ingestion, utilisez la stratégie de mise à jour. Celle-ci permet d’effectuer un traitement léger au moyen de commandes KQL. La stratégie de mise à jour exécute automatiquement les extractions et les transformations sur les données ingérées de la table d’origine, puis ingère les données résultantes dans une ou plusieurs tables de destination. Définissez votre [stratégie de mise à jour](/azure/data-explorer/kusto/management/update-policy?context=/azure/synapse-analytics/context/context).



## <a name="next-steps"></a>Étapes suivantes

- [Formats de données pris en charge](data-explorer-ingest-data-supported-formats.md)
- [Propriétés d’ingestion prises en charge](data-explorer-ingest-data-properties.md)
