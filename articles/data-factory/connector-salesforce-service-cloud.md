---
title: Copier des données depuis et vers Salesforce Service Cloud
titleSuffix: Azure Data Factory & Azure Synapse
description: Découvrez comment copier des données de Salesforce Service Cloud vers des banques de données réceptrices prises en charge et comment copier des données de banques de données sources prises en charge vers Salesforce Service Cloud, à l’aide de l’activité de copie disponible dans le pipeline Azure Data Factory ou Synapse Analytics.
ms.author: jianleishen
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: conceptual
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: 9f59c7f810646734552d7eaefe59d1c99be1518c
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124782757"
---
# <a name="copy-data-from-and-to-salesforce-service-cloud-using-azure-data-factory-or-synapse-analytics"></a>Copier des données de et vers Salesforce Service Cloud en utilisant Azure Data Factory ou Synapse Analytics

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Cet article décrit comment utiliser l’activité Copier dans des pipelines Azure Data Factory et Synapse Analytics pour copier des données depuis et vers Salesforce Service Cloud. Il s’appuie sur l’article [Vue d’ensemble de l’activité de copie](copy-activity-overview.md).

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

Ce connecteur Salesforce Service Cloud est pris en charge pour les activités suivantes :

- [Activité Copy](copy-activity-overview.md) avec [prise en charge de la matrice source/du récepteur](copy-activity-overview.md)
- [Activité de recherche](control-flow-lookup-activity.md)

Il est possible de copier des données de Salesforce Service Cloud vers n’importe quel magasin de données récepteur pris en charge. Vous pouvez également copier des données de n’importe quelle banque de données source prise en charge vers Salesforce Service Cloud. Pour obtenir la liste des banques de données prises en charge en tant que sources ou récepteurs par l’activité de copie, consultez le tableau [banques de données prises en charge](copy-activity-overview.md#supported-data-stores-and-formats).

Plus précisément, ce connecteur Salesforce Service Cloud prend en charge les éléments suivants :

- Développeur Salesforce, éditions professionnelle, d’entreprise ou illimitées.
- La copie de données depuis et vers le domaine de production, le bac à sable et le domaine personnalisé de Salesforce.

Le connecteur Salesforce est basé sur l'API REST/en bloc Salesforce. Par défaut, lors de la copie de données à partir de Salesforce, le connecteur utilise [V45](https://developer.salesforce.com/docs/atlas.en-us.218.0.api_rest.meta/api_rest/dome_versions.htm) et choisit automatiquement entre les API REST et en bloc en fonction de la taille des données : lorsque le jeu de résultats est volumineux, l’API en bloc est utilisée pour de meilleures performances. Lors de l’écriture de données dans Salesforce, le connecteur utilise la version [v40](https://developer.salesforce.com/docs/atlas.en-us.208.0.api_asynch.meta/api_asynch/asynch_api_intro.htm) de l’API en bloc. Vous pouvez aussi définir explicitement la version de l’API utilisée pour lire/écrire des données via la [`apiVersion`prorpiété](#linked-service-properties) dans le service lié.

## <a name="prerequisites"></a>Prérequis

L’autorisation de l’API doit être activée dans Salesforce. Pour plus d’informations, consultez l’article [How do I enable API access in Salesforce by permission set?](https://www.data2crm.com/migration/faqs/enable-api-access-salesforce-permission-set/) (Comment activer l’accès à l’API dans Salesforce par jeu d’autorisations ?)

## <a name="salesforce-request-limits"></a>Limites des requêtes Salesforce

Salesforce prend en charge un nombre limité de requêtes d’API totales et de requêtes d’API simultanées. Notez les points suivants :

- Si le nombre de requêtes simultanées dépasse la limite autorisée, les nouvelles requêtes sont bloquées avec un risque de défaillances aléatoires.
- Si le nombre total de requêtes dépasse la limite autorisée, le compte Salesforce est bloqué pendant 24 heures.

Vous pouvez également recevoir le message d’erreur « REQUEST_LIMIT_EXCEEDED » dans les deux scénarios. Pour plus d’informations, consultez la section « API Request Limits » (Limites de requête d’API) du document [Salesforce Developer Limits](https://developer.salesforce.com/docs/atlas.en-us.218.0.salesforce_app_limits_cheatsheet.meta/salesforce_app_limits_cheatsheet/salesforce_app_limits_platform_api.htm) (Limites des développeurs Salesforce).

## <a name="get-started"></a>Bien démarrer

[!INCLUDE [data-factory-v2-connector-get-started](includes/data-factory-v2-connector-get-started.md)]

## <a name="create-a-linked-service-to-salesforce-service-cloud-using-ui"></a>Créez un service lié à Salesforce Service Cloud à l’aide de l’interface utilisateur

Utilisez les étapes suivantes pour créer un service lié à Salesforce Service Cloud dans l’interface utilisateur du portail Azure.

1. Accédez à l’onglet Gérer dans votre espace de travail Azure Data Factory ou Synapse et sélectionnez Services liés, puis cliquez sur Nouveau :

    # <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory).

    :::image type="content" source="media/doc-common-process/new-linked-service.png" alt-text="Créez un nouveau service lié avec l’interface utilisateur Azure Data Factory.":::

    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

    :::image type="content" source="media/doc-common-process/new-linked-service-synapse.png" alt-text="Créez un nouveau service lié avec l’interface utilisateur d’Azure Synapse.":::

2. Recherchez Salesforce et sélectionnez le connecteur Salesforce Service Cloud.

    :::image type="content" source="media/connector-salesforce-service-cloud/salesforce-service-cloud-connector.png" alt-text="Sélectionnez le connecteur Salesforce Service Cloud.":::    

1. Configurez les détails du service, testez la connexion et créez le nouveau service lié.

    :::image type="content" source="media/connector-salesforce-service-cloud/configure-salesforce-service-cloud-linked-service.png" alt-text="Configurez un service lié à Salesforce Service Cloud.":::

## <a name="connector-configuration-details"></a>Détails de configuration des connecteurs

Les sections suivantes fournissent des détails sur les propriétés utilisées pour définir des entités Data Factory spécifiques au connecteur Salesforce Service Cloud.

## <a name="linked-service-properties"></a>Propriétés du service lié

Les propriétés suivantes sont prises en charge pour le service lié Salesforce :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type |La propriété type doit être définie sur **SalesforceServiceCloud**. |Oui |
| environmentUrl | Spécifiez l’URL de l’instance Salesforce Service Cloud. <br> - La valeur par défaut est `"https://login.salesforce.com"`. <br> - Pour copier des données du bac à sable, spécifiez `"https://test.salesforce.com"`. <br> - Pour copier les données du domaine personnalisé, spécifiez, par exemple, `"https://[domain].my.salesforce.com"`. |Non |
| username |Spécifiez un nom d’utilisateur pour le compte d’utilisateur. |Oui |
| mot de passe |Spécifiez le mot de passe du compte d’utilisateur.<br/><br/>Marquez ce champ en tant que SecureString afin de le stocker en toute sécurité, ou [référencez un secret stocké dans Azure Key Vault](store-credentials-in-key-vault.md). |Oui |
| securityToken |Spécifiez le jeton de sécurité du compte d’utilisateur. <br/><br/>Pour en savoir plus sur les jetons de sécurité, consultez l’article [Security and the API](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_concepts_security.htm)(Sécurité et API). Le jeton de sécurité peut être uniquement ignoré si vous ajoutez l’adresse IP d'Integration Runtime à la [liste d'adresses IP approuvées](https://developer.salesforce.com/docs/atlas.en-us.securityImplGuide.meta/securityImplGuide/security_networkaccess.htm) sur Salesforce. Lorsque vous utilisez Azure IR, consultez [Adresses IP Azure Integration Runtime](azure-integration-runtime-ip-addresses.md).<br/><br/>Pour des instructions sur l'obtention et la réinitialisation d’un jeton de sécurité, consultez l’article [Obtenir un jeton de sécurité](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm). Marquez ce champ en tant que SecureString afin de le stocker en toute sécurité dans Data Factory, ou [référencez un secret stocké dans Azure Key Vault](store-credentials-in-key-vault.md). |Non |
| apiVersion | Spécifiez la version de l’API REST/en bloc de Salesforce à utiliser, par exemple `48.0`. Par défaut, le connecteur utilise [v45](https://developer.salesforce.com/docs/atlas.en-us.218.0.api_rest.meta/api_rest/dome_versions.htm) pour copier les données à partir de Salesforce, et [v40](https://developer.salesforce.com/docs/atlas.en-us.208.0.api_asynch.meta/api_asynch/asynch_api_intro.htm) pour copier les données dans Salesforce. | Non |
| connectVia | Le [runtime d’intégration](concepts-integration-runtime.md) à utiliser pour se connecter à la banque de données. À défaut de spécification, le runtime d’intégration Azure par défaut est utilisé. | Non |

**Exemple : Stocker les informations d’identification**

```json
{
    "name": "SalesforceServiceCloudLinkedService",
    "properties": {
        "type": "SalesforceServiceCloud",
        "typeProperties": {
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            },
            "securityToken": {
                "type": "SecureString",
                "value": "<security token>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exemple : Stocker les informations d’identification dans Key Vault**

```json
{
    "name": "SalesforceServiceCloudLinkedService",
    "properties": {
        "type": "SalesforceServiceCloud",
        "typeProperties": {
            "username": "<username>",
            "password": {
                "type": "AzureKeyVaultSecret",
                "secretName": "<secret name of password in AKV>",
                "store":{
                    "referenceName": "<Azure Key Vault linked service>",
                    "type": "LinkedServiceReference"
                }
            },
            "securityToken": {
                "type": "AzureKeyVaultSecret",
                "secretName": "<secret name of security token in AKV>",
                "store":{
                    "referenceName": "<Azure Key Vault linked service>",
                    "type": "LinkedServiceReference"
                }
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Propriétés du jeu de données

Pour obtenir la liste complète des sections et propriétés disponibles pour la définition de jeux de données, consultez l’article [Jeux de données](concepts-datasets-linked-services.md). Cette section fournit la liste des propriétés prises en charge par le jeu de données Salesforce Service Cloud.

Pour copier des données depuis/vers Salesforce Service Cloud, les propriétés suivantes sont prises en charge.

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété type doit être définie sur **SalesforceServiceCloudObject**.  | Oui |
| objectApiName | Nom d’objet Salesforce duquel extraire des données. | Non pour Source, Oui pour Récepteur |

> [!IMPORTANT]
> La partie « __c » du **nom de l’API** est requise pour tout objet personnalisé.

:::image type="content" source="media/copy-data-from-salesforce/data-factory-salesforce-api-name.png" alt-text="Nom d’API de connexion Salesforce":::

**Exemple :**

```json
{
    "name": "SalesforceServiceCloudDataset",
    "properties": {
        "type": "SalesforceServiceCloudObject",
        "typeProperties": {
            "objectApiName": "MyTable__c"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Salesforce Service Cloud linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété type du jeu de données doit être définie sur **RelationalTable**. | Oui |
| tableName | Nom de la table dans Salesforce Service Cloud. | Non (si « query » est spécifié dans la source de l’activité) |

## <a name="copy-activity-properties"></a>Propriétés de l’activité de copie

Pour obtenir la liste complète des sections et des propriétés disponibles pour la définition des activités, consultez l’article [Pipelines](concepts-pipelines-activities.md). Cette section fournit la liste des propriétés prises en charge par la source et le récepteur de Salesforce Service Cloud.

### <a name="salesforce-service-cloud-as-a-source-type"></a>Service Salesforce Cloud en tant que type de source

Pour copier des données à partir de Salesforce Service Cloud, les propriétés suivantes sont prises en charge dans la section **source** de l’activité de copie.

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété type de la source de l’activité de copie doit être définie sur **SalesforceServiceCloudSource**. | Oui |
| query |Utilise la requête personnalisée pour lire des données. Vous pouvez utiliser une requête SQL-92 ou [SOQL (Salesforce Object Query Language)](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql.htm). Retrouvez d’autres conseils dans la section [Conseils de requête](#query-tips). Si la requête n’est pas spécifiée, toutes les données de l’objet Salesforce Service Cloud spécifiées dans « objectApiName » dans le jeu de données seront récupérées. | Non (si « objectApiName » est spécifié dans le jeu de données) |
| readBehavior | Indique si seuls les enregistrements existants doivent être interrogés ou si tous les enregistrements, y compris ceux qui ont été supprimés, doivent être interrogés. Si rien n’est spécifié, le comportement par défaut appliqué est le premier. <br>Valeurs autorisées : **query** (valeur par défaut), **queryAll**.  | Non |

> [!IMPORTANT]
> La partie « __c » du **nom de l’API** est requise pour tout objet personnalisé.

:::image type="content" source="media/copy-data-from-salesforce/data-factory-salesforce-api-name-2.png" alt-text="Liste de noms d’API de connexion Salesforce":::

**Exemple :**

```json
"activities":[
    {
        "name": "CopyFromSalesforceServiceCloud",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Salesforce Service Cloud input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SalesforceServiceCloudSource",
                "query": "SELECT Col_Currency__c, Col_Date__c, Col_Email__c FROM AllDataType__c"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="salesforce-service-cloud-as-a-sink-type"></a>Salesforce Service Cloud en tant que type de récepteur

Pour copier des données dans Salesforce Service Cloud, les propriétés suivantes sont prises en charge dans la section du **récepteur** de l’activité de copie.

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété de type du récepteur d’activité de copie doit être définie sur **SalesforceServiceCloudSink**. | Oui |
| writeBehavior | Comportement d’écriture de l’opération.<br/>Les valeurs autorisées sont **Insert** et **Upsert**. | Non (la valeur par défaut est un point Insert) |
| externalIdFieldName | Nom du champ ID externe pour l’opération upsert. Le champ spécifié doit être défini en tant que « champ d’ID externe » dans l’objet Salesforce Service Cloud. Il ne peut pas avoir de valeurs NULL dans les données d’entrée correspondantes. | Oui, pour « Upsert » |
| writeBatchSize | Nombre de lignes de données écrites dans Salesforce Service Cloud dans chaque lot. | Non (valeur par défaut : 5,000) |
| ignoreNullValues | Indique si les valeurs NULL des données d’entrée doivent être ignorées pendant une opération d’écriture.<br/>Les valeurs autorisées sont **true** et **false**.<br>- **True** : Conserver les données dans l’objet de destination quand vous effectuez une opération upsert ou de mise à jour. Insérer une valeur définie par défaut lorsque vous effectuez une opération insert.<br/>- **False** : Mettre à jour les données dans l’objet de destination avec la valeur NULL quand vous effectuez une opération upsert ou de mise à jour. Insérer une valeur NULL lorsque vous effectuez une opération insert. | Non (valeur par défaut : false) |
| maxConcurrentConnections |La limite supérieure de connexions simultanées établies au magasin de données pendant l’exécution de l’activité. Spécifiez une valeur uniquement lorsque vous souhaitez limiter les connexions simultanées.| Non |

**Exemple :**

```json
"activities":[
    {
        "name": "CopyToSalesforceServiceCloud",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Salesforce Service Cloud output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SalesforceServiceCloudSink",
                "writeBehavior": "Upsert",
                "externalIdFieldName": "CustomerId__c",
                "writeBatchSize": 10000,
                "ignoreNullValues": true
            }
        }
    }
]
```

## <a name="query-tips"></a>Conseils pour les requêtes

### <a name="retrieve-data-from-a-salesforce-service-cloud-report"></a>Récupérer des données à partir d’un rapport Salesforce Service Cloud

Vous pouvez récupérer des données à partir de rapports Salesforce Service Cloud en spécifiant une requête sous la forme `{call "<report name>"}`. par exemple `"query": "{call \"TestReport\"}"`.

### <a name="retrieve-deleted-records-from-the-salesforce-service-cloud-recycle-bin"></a>Récupérer les enregistrements supprimés de la corbeille Salesforce Service Cloud

Pour interroger les enregistrements supprimés de manière réversible dans la corbeille Salesforce Service Cloud, vous pouvez spécifier `readBehavior` en tant que `queryAll`. 

### <a name="difference-between-soql-and-sql-query-syntax"></a>Différence entre la syntaxe de requête SOQL et SQL

Lorsque vous copiez des données à partir de Salesforce Service Cloud, vous pouvez utiliser une requête SOQL ou une requête SQL. Notez que ces deux requêtes ont une syntaxe et une prise en charge des fonctionnalités différentes, ne les mélangez pas. Vous êtes invité à utiliser la requête SOQL qui est prise en charge en mode natif par Salesforce Service Cloud. Le tableau suivant répertorie les différences principales :

| Syntaxe | Mode SOQL | Mode SQL |
|:--- |:--- |:--- |
| Sélection de colonnes | Besoin d'énumérer les champs à copier dans la requête, par exemple `SELECT field1, filed2 FROM objectname` | `SELECT *` est pris en charge en plus de la sélection de colonnes. |
| Guillemets | Les noms de champ ou d’objet ne peuvent pas être mis entre guillemets. | Les noms de champ ou d’objet peuvent être mis entre guillemets, par exemple `SELECT "id" FROM "Account"` |
| Format de date/heure |  Reportez-vous aux détails [ici](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql_select_dateformats.htm) et aux exemples fournis dans la section suivante. | Reportez-vous aux détails [ici](/sql/odbc/reference/develop-app/date-time-and-timestamp-literals) et aux exemples fournis dans la section suivante. |
| Valeurs booléennes | Représentées sous la forme de `False` et `True`, par exemple `SELECT … WHERE IsDeleted=True`. | Représenté sous la forme de 0 ou de 1, par exemple `SELECT … WHERE IsDeleted=1`. |
| Changement de nom de colonne | Non pris en charge. | Pris en charge, par exemple : `SELECT a AS b FROM …`. |
| Relation | Pris en charge, par exemple `Account_vod__r.nvs_Country__c`. | Non pris en charge. |

### <a name="retrieve-data-by-using-a-where-clause-on-the-datetime-column"></a>Récupérer des données à l’aide d’une clause where sur la colonne DateTime

Lorsque vous spécifiez une requête SOQL ou SQL, faites attention à la différence de format DateTime. Par exemple :

* **Exemple SOQL** : `SELECT Id, Name, BillingCity FROM Account WHERE LastModifiedDate >= @{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-ddTHH:mm:ssZ')} AND LastModifiedDate < @{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-ddTHH:mm:ssZ')}`
* **Exemple SQL** : `SELECT * FROM Account WHERE LastModifiedDate >= {ts'@{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-dd HH:mm:ss')}'} AND LastModifiedDate < {ts'@{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-dd HH:mm:ss')}'}`

### <a name="error-of-malformed_query-truncated"></a>Erreur MALFORMED_QUERY: Truncated

L'erreur « MALFORMED_QUERY: Truncated » peut être rencontrée lorsqu'une colonne de type JunctionIdList est présente dans les données et que la prise en charge par Salesforce de données contenant un grand nombre de lignes est limitée. Pour atténuer ce problème, essayez d'exclure la colonne JunctionIdList ou de limiter le nombre de lignes à copier (vous pouvez partitionner le travail en plusieurs exécutions d'activité de copie).

## <a name="data-type-mapping-for-salesforce-service-cloud"></a>Mappage de type de données pour Salesforce Service Cloud

Lorsque vous copiez des données à partir de Salesforce Service Cloud, les mappages suivants sont utilisés à partir des types de données Salesforce Service Cloud pour les types de données intermédiaires utilisés en interne dans le service. Pour découvrir comment l’activité de copie mappe le schéma et le type de données la source au récepteur, consultez [Mappage de schéma dans l’activité de copie](copy-activity-schema-and-type-mapping.md).

| Type de données Salesforce Service Cloud | Type de données de service intermédiaire |
|:--- |:--- |
| Numérotation automatique |String |
| Case à cocher |Boolean |
| Devise |Decimal |
| Date |DateTime |
| Date/Heure |DateTime |
| E-mail |String |
| id |String |
| Relation de recherche |String |
| Liste déroulante à sélection multiple |String |
| Number |Decimal |
| Pourcentage |Decimal |
| Téléphone |String |
| Liste déroulante |String |
| Texte |String |
| Zone de texte |String |
| Zone de texte (long) |String |
| Zone de texte (enrichi) |String |
| Texte (chiffré) |String |
| URL |String |

## <a name="lookup-activity-properties"></a>Propriétés de l’activité Lookup

Pour en savoir plus sur les propriétés, consultez [Activité Lookup](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Étapes suivantes
Consultez les [magasins de données pris en charge](copy-activity-overview.md#supported-data-stores-and-formats) pour obtenir la liste des sources et magasins de données pris en charge en tant que récepteurs par l’activité de copie.