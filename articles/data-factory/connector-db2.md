---
title: Copier des données de DB2
titleSuffix: Azure Data Factory & Azure Synapse
description: Découvrez comment utiliser l'activité de copie dans un pipeline Azure Data Factory ou Synapse Analytics pour copier des données de DB2 vers des banques de données réceptrices prises en charge.
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.custom: synapse
ms.topic: conceptual
ms.date: 09/09/2021
ms.author: jianleishen
ms.openlocfilehash: 5fb5a7c83aba80d5eb58bac4437121ce37b6345a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131024388"
---
# <a name="copy-data-from-db2-using-azure-data-factory-or-synapse-analytics"></a>Copier des données de DB2 à l’aide d’Azure Data Factory ou de Synapse Analytics
> [!div class="op_single_selector" title1="Sélectionnez la version du service Data Factory que vous utilisez :"]
> * [Version 1](v1/data-factory-onprem-db2-connector.md)
> * [Version actuelle](connector-db2.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Cet article décrit comment utiliser l’activité de copie dans des pipelines Azure Data Factory et Synapse Analytics pour copier des données à partir d’une base de données DB2. Il s’appuie sur l’article [Vue d’ensemble de l’activité de copie](copy-activity-overview.md).

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

Ce connecteur de base de données DB2 est pris en charge pour les activités suivantes :

- [Activité Copy](copy-activity-overview.md) avec [prise en charge de la matrice source/du récepteur](copy-activity-overview.md)
- [Activité de recherche](control-flow-lookup-activity.md)

Vous pouvez copier des données d’une base de données DB2 vers toute banque de données réceptrice prise en charge. Pour obtenir la liste des banques de données prises en charge en tant que sources ou récepteurs par l’activité de copie, consultez le tableau [Banques de données prises en charge](copy-activity-overview.md#supported-data-stores-and-formats).

En particulier, ce connecteur DB2 prend en charge les plateformes et versions IBM DB2 suivantes avec les versions 9, 10 et 11 de SQL Access Manager (SQLAM) DRDA (Distributed Relational Database Architecture).  Il utilise le protocole DDM/DRDA.

* IBM DB2 pour z/OS 12.1
* IBM DB2 pour z/OS 11.1
* IBM DB2 pour z/OS 10.1
* IBM DB2 pour i 7.3
* IBM DB2 pour i 7.2
* IBM DB2 pour i 7.1
* IBM DB2 pour LUW 11
* IBM DB2 pour LUW 10.5
* IBM DB2 pour LUW 10.1

>[!TIP]
>Le connecteur DB2 s’appuie sur le Fournisseur OLE DB Microsoft pour DB2. Pour résoudre les erreurs du connecteur DB2, reportez-vous à [Codes d’erreur du fournisseur de données](/host-integration-server/db2oledbv/data-provider-error-codes#drda-protocol-errors).

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [data-factory-v2-integration-runtime-requirements](includes/data-factory-v2-integration-runtime-requirements.md)]

Le runtime d’intégration fournit un pilote DB2 intégré. Ainsi, vous n’avez pas besoin d’installer manuellement un pilote lors de la copie des données de DB2.

## <a name="getting-started"></a>Prise en main

[!INCLUDE [data-factory-v2-connector-get-started](includes/data-factory-v2-connector-get-started.md)]

## <a name="create-a-linked-service-to-db2-using-ui"></a>Créer un service lié à DB2 à l’aide de l’interface utilisateur

Utilisez les étapes suivantes pour créer un service lié à DB2 dans l’interface utilisateur du portail Azure.

1. Accédez à l’onglet Gérer dans votre espace de travail Azure Data Factory ou Synapse et sélectionnez Services liés, puis cliquez sur Nouveau :

    # <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory).

    :::image type="content" source="media/doc-common-process/new-linked-service.png" alt-text="Capture d’écran montrant la création d’un service lié avec l’interface utilisateur Azure Data Factory.":::

    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

    :::image type="content" source="media/doc-common-process/new-linked-service-synapse.png" alt-text="Capture d’écran de la création d’un nouveau service lié avec l’interface utilisateur Azure Synapse.":::

2. Recherchez DB2 et sélectionnez le connecteur DB2.

    :::image type="content" source="media/connector-db2/db2-connector.png" alt-text="Capture d’écran du connecteur DB2.":::    

1. Configurez les informations du service, testez la connexion et créez le nouveau service lié.

    :::image type="content" source="media/connector-db2/configure-db2-linked-service.png" alt-text="Capture d’écran de la configuration du service lié pour DB2.":::

## <a name="connector-configuration-details"></a>Informations de configuration des connecteurs

Les sections suivantes fournissent des informations sur les propriétés utilisées pour définir les entités Data Factory spécifiques du connecteur DB2.

## <a name="linked-service-properties"></a>Propriétés du service lié

Les propriétés prises en charge pour le service lié DB2 sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété type doit être définie sur : **Db2** | Oui |
| connectionString | Spécifiez les informations nécessaires pour se connecter à l’instance DB2.<br/> Vous pouvez également définir un mot de passe dans Azure Key Vault et extraire la configuration `password` de la chaîne de connexion. Pour plus d’informations, reportez-vous aux exemples suivants et à l’article [Stocker des informations d’identification dans Azure Key Vault](store-credentials-in-key-vault.md). | Oui |
| connectVia | [Runtime d’intégration](concepts-integration-runtime.md) à utiliser pour la connexion à la banque de données. Pour plus d’informations, consultez la section [Conditions préalables](#prerequisites). À défaut de spécification, le runtime d’intégration Azure par défaut est utilisé. |Non |

Propriétés courantes dans la chaîne de connexion :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| server |Nom du serveur DB2. Vous pouvez spécifier le numéro de port à la suite du nom du serveur en le séparant par un signe deux-points, par exemple, `server:port`.<br>Le connecteur DB2 utilise le protocole DDM/DRDA et, par défaut s’il n’est pas spécifié, le port 50000. Le port utilisé par votre base de données DB2 peut être différent selon la version et vos paramètres. Par exemple, le port par défaut est 50000 pour DB2 LUW, et 446 ou 448 pour AS400 lorsque le protocole TLS est activé. Pour plus d’informations sur la configuration classique du port, consultez les documents DB2 suivants : [DB2 z/OS](https://www.ibm.com/support/knowledgecenter/SSEPGG_11.5.0/com.ibm.db2.luw.qb.dbconn.doc/doc/t0008229.html), [DB2 iSeries](https://www.ibm.com/support/knowledgecenter/ssw_ibm_i_74/ddp/rbal1ports.htm) et [DB2 LUW](https://www.ibm.com/support/knowledgecenter/en/SSEKCU_1.1.3.0/com.ibm.psc.doc/install/psc_t_install_typical_db2_port.html). |Oui |
| database |Nom de la base de données DB2. |Oui |
| authenticationType |Type d'authentification utilisé pour se connecter à la base de données DB2.<br/>Valeur autorisée : **De base**. |Oui |
| username |Spécifiez le nom d’utilisateur pour la connexion à la base de données DB2. |Oui |
| mot de passe |Spécifiez le mot de passe du compte d’utilisateur que vous avez spécifié pour le nom d’utilisateur. Marquez ce champ en tant que SecureString afin de le stocker en toute sécurité, ou [référencez un secret stocké dans Azure Key Vault](store-credentials-in-key-vault.md). |Oui |
| packageCollection    | Spécifiez sous quel emplacement les packages nécessaires sont créés automatiquement par le service lors de l’interrogation de la base de données. Si cette option n’est pas définie, le service utilise {username} comme valeur par défaut. | Non |
| certificateCommonName | Lorsque vous utilisez le chiffrement SSL (Secure Sockets Layer) ou TLS (Transport Layer Security), vous devez entrer une valeur pour le nom commun du certificat. | Non |

> [!TIP]
> Si vous recevez un message d’erreur indiquant `The package corresponding to an SQL statement execution request was not found. SQLSTATE=51002 SQLCODE=-805`, la raison est qu’un package nécessaire n’est pas créé pour l’utilisateur. Par défaut, le service tente de créer le package sous la collection désignée à l’aide du nom de l’utilisateur dont vous vous êtes servi pour vous connecter à DB2. Spécifiez la propriété du regroupement des packages pour indiquer à quel endroit vous souhaitez que le service crée les packages nécessaires lors de l’interrogation de la base de données.

**Exemple :**

```json
{
    "name": "Db2LinkedService",
    "properties": {
        "type": "Db2",
        "typeProperties": {
            "connectionString": "server=<server:port>;database=<database>;authenticationType=Basic;username=<username>;password=<password>;packageCollection=<packagecollection>;certificateCommonName=<certname>;"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```
**Exemple : stockage du mot de passe dans Azure Key Vault**

```json
{
    "name": "Db2LinkedService",
    "properties": {
        "type": "Db2",
        "typeProperties": {
            "connectionString": "server=<server:port>;database=<database>;authenticationType=Basic;username=<username>;packageCollection=<packagecollection>;certificateCommonName=<certname>;",
            "password": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

Si vous utilisiez le service lié DB2 avec la charge utile suivante, il reste pris en charge tel quel, mais nous vous suggérons d’utiliser le nouveau à l’avenir.

**Charge utile précédente :**

```json
{
    "name": "Db2LinkedService",
    "properties": {
        "type": "Db2",
        "typeProperties": {
            "server": "<servername:port>",
            "database": "<dbname>",
            "authenticationType": "Basic",
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
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

Pour obtenir la liste complète des sections et propriétés disponibles pour la définition de jeux de données, consultez l’article sur les [jeux de données](concepts-datasets-linked-services.md). Cette section fournit la liste des propriétés prises en charge par le jeu de données DB2.

Pour copier des données à partir de DB2, les propriétés suivantes sont prises en charge :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété type du jeu de données doit être définie sur : **Db2Table** | Oui |
| schéma | Nom du schéma. |Non (si « query » dans la source de l’activité est spécifié)  |
| table | Nom de la table. |Non (si « query » dans la source de l’activité est spécifié)  |
| tableName | Nom de la table avec le schéma. Cette propriété est prise en charge pour la compatibilité descendante. Utilisez `schema` et `table` pour une nouvelle charge de travail. | Non (si « query » dans la source de l’activité est spécifié) |

**Exemple**

```json
{
    "name": "DB2Dataset",
    "properties":
    {
        "type": "Db2Table",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<DB2 linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

Si vous utilisiez un dataset typé `RelationalTable`, il reste pris en charge tel quel, mais nous vous suggérons d’utiliser désormais le nouveau dataset.

## <a name="copy-activity-properties"></a>Propriétés de l’activité de copie

Pour obtenir la liste complète des sections et des propriétés disponibles pour la définition des activités, consultez l’article [Pipelines](concepts-pipelines-activities.md). Cette section fournit la liste des propriétés prises en charge par la source DB2.

### <a name="db2-as-source"></a>DB2 en tant que source

Pour copier des données de DB2, les propriétés prises en charge dans la section **source** de l’activité de copie sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété type de la source d’activité de copie doit être définie sur : **Db2Source** | Oui |
| query | Utiliser la requête SQL personnalisée pour lire les données. Par exemple : `"query": "SELECT * FROM \"DB2ADMIN\".\"Customers\""`. | Non (si « tableName » est spécifié dans dataset) |

**Exemple :**

```json
"activities":[
    {
        "name": "CopyFromDB2",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<DB2 input dataset name>",
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
                "type": "Db2Source",
                "query": "SELECT * FROM \"DB2ADMIN\".\"Customers\""
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Si vous utilisiez une source de données typée `RelationalSource`, elle reste prise en charge telle quelle, mais nous vous suggérons d’utiliser désormais la nouvelle source.

## <a name="data-type-mapping-for-db2"></a>Mappage de type de données pour DB2

Lors de la copie de données à partir de DB2, les mappages suivants sont utilisés entre les types de données DB2 et les types de données intermédiaires utilisés en interne dans le service. Pour découvrir comment l’activité de copie mappe le schéma et le type de données la source au récepteur, voir [Mappages de schémas et de types de données](copy-activity-schema-and-type-mapping.md).

| Type de base de données DB2 | Type de données de service intermédiaire |
|:--- |:--- |
| BigInt |Int64 |
| Binary |Byte[] |
| Objet blob |Byte[] |
| Char |String |
| Clob |String |
| Date |Datetime |
| DB2DynArray |String |
| DbClob |String |
| Decimal |Decimal |
| DecimalFloat |Decimal |
| Double |Double |
| Float |Double |
| Graphic |String |
| Integer |Int32 |
| LongVarBinary |Byte[] |
| LongVarChar |String |
| LongVarGraphic |String |
| Numérique |Decimal |
| Real |Unique |
| SmallInt |Int16 |
| Temps |TimeSpan |
| Timestamp |DateTime |
| VarBinary |Byte[] |
| VarChar |String |
| VarGraphic |String |
| Xml |Byte[] |

## <a name="lookup-activity-properties"></a>Propriétés de l’activité Lookup

Pour en savoir plus sur les propriétés, consultez [Activité Lookup](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir une liste des magasins de données pris en charge comme sources et récepteurs par l’activité de copie, consultez la section sur les [magasins de données pris en charge](copy-activity-overview.md#supported-data-stores-and-formats).