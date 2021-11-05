---
title: Services liés
titleSuffix: Azure Data Factory & Azure Synapse
description: Découvrez les services liés dans Azure Data Factory et Azure Synapse Analytics. Les services liés lient les unités de calcul et les magasins de données au service.
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.subservice: data-movement
ms.custom: synapse
ms.topic: conceptual
ms.date: 09/09/2021
ms.openlocfilehash: ce62577dd73d6c6318c5358e4cff3ada4f4bc0a5
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131068860"
---
# <a name="linked-services-in-azure-data-factory-and-azure-synapse-analytics"></a>Services liés dans Azure Data Factory et Azure Synapse Analytics

> [!div class="op_single_selector" title1="Sélectionnez la version du service Data Factory que vous utilisez :"]
> * [Version 1](v1/data-factory-create-datasets.md)
> * [Version actuelle](concepts-linked-services.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Cet article décrit ce que sont les services liés, comment ils sont définis au format JSON et comment ils sont utilisés dans Azure Data Factory et Azure Synapse Analytics.

Pour en savoir plus, lisez l’article d’introduction d’[Azure Data Factory](introduction.md) ou d’[Azure Synapse](../synapse-analytics/overview-what-is.md).

## <a name="overview"></a>Vue d’ensemble

Azure Data Factory et Azure Synapse Analytics peuvent avoir un ou plusieurs pipelines. Un **pipeline** constitue un regroupement logique d’**activités** qui exécutent ensemble une tâche. Les activités d’un pipeline définissent les actions à effectuer sur les données. Par exemple, vous pouvez utiliser une activité de copie pour copier des données d’un serveur SQL Server vers un stockage Blob Azure. Ensuite, vous pouvez utiliser une activité Hive qui exécute un script Hive sur un cluster Azure HDInsight pour traiter les données du stockage Blob afin de produire des données de sortie. Enfin, vous pouvez utiliser une deuxième activité de copie pour copier les données de sortie dans Azure Synapse Analytics sur lequel des solutions de génération de rapports d’aide à la décision sont développées. Pour plus d’informations sur les pipelines et les activités, consultez [Pipelines et activités](concepts-pipelines-activities.md).

À présent, un **jeu de données** est une vue de données nommée qui pointe ou fait référence simplement aux données que vous souhaitez utiliser dans vos **activités** en tant qu’entrées et sorties.

Avant de créer un jeu de données, vous devez créer un **service lié** pour lier votre magasin de données à l’instance Data Factory ou à l’espace de travail Synapse. Les services liés ressemblent à des chaînes de connexion. Ils définissent les informations de connexion nécessaires au service pour se connecter à des ressources externes. Considérez les choses de la façon suivante : le jeu de données représente la structure des données à l’intérieur des magasins de données liés, et le service lié définit la connexion à la source de données. Par exemple, un service lié Stockage Azure lie un compte de stockage au service. Un jeu de données d'objets blob représente le conteneur d’objets blob et le dossier à l’intérieur de ce compte Stockage Azure contenant les objets blob d’entrée à traiter.

Voici un exemple de scénario. Pour copier des données du stockage Blob vers une base de données SQL Database, vous devez créer deux services liés : Stockage Azure et Azure SQL Database. Créez ensuite deux jeux de données : le jeu de données d’objets blob Azure (qui fait référence au service lié Stockage Azure) et le jeu de données de table SQL Azure (qui fait référence au service lié Azure SQL Database). Les services liés Stockage Azure et Azure SQL Database contiennent des chaînes de connexion utilisées par le service pendant l’exécution pour se connecter à votre instance Stockage Azure et Azure SQL Database, respectivement. Le jeu de données d’objets blob Azure spécifie le conteneur d’objets blob et le dossier d’objets blob qui contient les objets blob d’entrée dans votre stockage Blob. Le jeu de données de table SQL Azure spécifie la table SQL dans votre base de données SQL Database dans laquelle les données doivent être copiées.

Le diagramme suivant montre la relation entre le pipeline, l’activité, le jeu de données et le service lié dans le service :

:::image type="content" source="media/concepts-datasets-linked-services/relationship-between-data-factory-entities.png" alt-text="Relation entre le pipeline, l’activité, le jeu de données et les services liés":::

## <a name="linked-service-json"></a>Service lié JSON

Un service lié se définit de la façon suivante au format JSON :

```json
{
    "name": "<Name of the linked service>",
    "properties": {
        "type": "<Type of the linked service>",
        "typeProperties": {
              "<data store or compute-specific type properties>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

La table suivante décrit les propriétés dans le JSON ci-dessus :

Propriété | Description | Obligatoire |
-------- | ----------- | -------- |
name | Nom du service lié. Consultez les [règles de nommage](naming-rules.md). |  Oui |
type | Type du service lié. Par exemple : AzureBlobStorage (magasin de données) ou AzureBatch (calcul). Consultez la description de typeProperties. | Oui |
typeProperties | Les propriétés type sont différentes pour chaque magasin de données et chaque unité de calcul. <br/><br/> Vous trouverez la liste des types de magasins de données pris en charge et leurs propriétés dans l’article [vue d’ensemble du connecteur](copy-activity-overview.md#supported-data-stores-and-formats). Accédez à l’article sur le connecteur de magasin de données pour en savoir plus sur les propriétés de type propres à un magasin de données. <br/><br/> Vous trouverez la liste des types de calcul pris en charge et leurs propriétés sur la page [Services liés de calcul](compute-linked-services.md). | Oui |
connectVia | [Runtime d’intégration](concepts-integration-runtime.md) à utiliser pour la connexion à la banque de données. Vous pouvez utiliser Azure Integration Runtime ou Integration Runtime auto-hébergé (si votre magasin de données se trouve dans un réseau privé). À défaut de spécification, le runtime d’intégration Azure par défaut est utilisé. | Non

## <a name="linked-service-example"></a>Exemple de service lié

Le service lié suivant est un service lié au stockage d’objets blob. Notez que le type est défini sur Stockage d’objets blob Azure. Les propriétés de type du service lié au stockage d’objets blob Azure comprennent une chaîne de connexion. Le service l’utilise pour se connecter au magasin de données à l’exécution.

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="create-linked-services"></a>Créez des services liés

Il est possible de créer des services liés dans l’expérience utilisateur Azure Data Factory via le [hub de gestion](author-management-hub.md) et des activités, jeux de données ou flux de données qui y font référence.

Vous pouvez créer des services liés en utilisant l’un de ces outils ou Kits de développement logiciel (SDK) : [API .NET](quickstart-create-data-factory-dot-net.md), [PowerShell](quickstart-create-data-factory-powershell.md), [API REST](quickstart-create-data-factory-rest-api.md), [Modèle Azure Resource Manager](quickstart-create-data-factory-resource-manager-template.md) et [Portail Azure](quickstart-create-data-factory-portal.md).


## <a name="data-store-linked-services"></a>Services liés de banque de données

Vous trouverez la liste des magasins de données pris en charge dans l’article [Vue d’ensemble du connecteur](copy-activity-overview.md#supported-data-stores-and-formats). Cliquez sur un magasin de données pour en savoir plus sur les propriétés de connexion prises en charge.

## <a name="compute-linked-services"></a>Services liés de calcul

Reportez-vous aux [environnements de calcul pris en charge](compute-linked-services.md) pour plus d’informations sur les différents environnements de calcul auxquels vous pouvez vous connecter à partir de votre service, ainsi que les différentes configurations.

## <a name="next-steps"></a>Étapes suivantes

- [Découvrez comment utiliser les informations d’identification d’une identité managée affectée par l’utilisateur dans un service lié](credentials.md).

Consultez les didacticiels suivants pour obtenir des instructions pas à pas sur la création de pipelines et de jeux de données à l’aide de l’un de ces outils ou kits de développement logiciel (SDK).

- [Démarrage rapide : Créer une fabrique de données avec .NET](quickstart-create-data-factory-dot-net.md)
- [Démarrage rapide : Créer une fabrique de données à l’aide de PowerShell](quickstart-create-data-factory-powershell.md)
- [Démarrage rapide : Créer une fabrique de données à l’aide d’une API REST](quickstart-create-data-factory-rest-api.md)
- [Démarrage rapide : Créer une fabrique de données à l’aide du portail Azure](quickstart-create-data-factory-portal.md)
