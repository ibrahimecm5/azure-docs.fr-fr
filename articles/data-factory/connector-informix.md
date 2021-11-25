---
title: Copier des données vers et depuis IBM Informix
description: Découvrez comment copier des données vers et depuis IBM Informix à l’aide d’une activité de copie dans un pipeline Azure Data Factory ou Synapse Analytics.
titleSuffix: Azure Data Factory & Azure Synapse
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.custom: synapse
ms.topic: conceptual
ms.date: 11/09/2021
ms.author: jianleishen
ms.openlocfilehash: d3ce22128b209a65d5f9036b0943f2425e2c5de3
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132401247"
---
# <a name="copy-data-from-and-to-ibm-informix-using-azure-data-factory-or-synapse-analytics"></a>Copier des données vers ou à partir d’IBM Informix à l’aide d’Azure Data Factory ou de Synapse Analytics

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Cet article explique comment utiliser l’activité de copie dans un pipeline Azure Data Factory ou Synapse Analytics pour copier des données à partir d’un magasin de données IBM Informix. Il s’appuie sur l’article [Vue d’ensemble de l’activité de copie](copy-activity-overview.md).

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

Ce connecteur Informix est pris en charge pour les activités suivantes :

- [Activité Copy](copy-activity-overview.md) avec [prise en charge de la matrice source/du récepteur](copy-activity-overview.md)
- [Activité de recherche](control-flow-lookup-activity.md)

Vous pouvez copier des données d’une source Informix vers tout magasin de données récepteur pris en charge, ou à partir de tout magasin de données source pris en charge vers un récepteur Informix. Pour obtenir la liste des banques de données prises en charge en tant que sources ou récepteurs par l’activité de copie, consultez le tableau [Banques de données prises en charge](copy-activity-overview.md#supported-data-stores-and-formats).


## <a name="prerequisites"></a>Prérequis

Pour utiliser ce connecteur Informix, vous devez :

- Configurer un Runtime d’intégration autohébergé. Pour plus d’informations, consultez l’article [Runtime d’intégration autohébergé](create-self-hosted-integration-runtime.md).
- Téléchargez le Kit de développement logiciel (SDK) client 64 bits pour Informix afin de créer une connexion ODBC pour le magasin de données sur la machine Integration Runtime. Pour télécharger et configurer le Kit de développement logiciel (SDK), consultez cet [article](https://www.ibm.com/support/pages/informix-client-software-development-kit-client-sdk-and-informix-connect-system-requirements) pour plus d’informations ou contactez l’équipe du support technique d’IBM pour obtenir une aide à l’installation du pilote.

## <a name="getting-started"></a>Prise en main

[!INCLUDE [data-factory-v2-connector-get-started](includes/data-factory-v2-connector-get-started.md)]

## <a name="create-a-linked-service-to-informix-using-ui"></a>Créer un service lié à Informix à l’aide de l’interface utilisateur

Suivez les étapes suivantes pour créer un service lié à Informix dans l’interface utilisateur du portail Azure.

1. Accédez à l’onglet Gérer dans votre espace de travail Azure Data Factory ou Synapse, sélectionnez Services liés, puis cliquez sur Nouveau :

    # <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory).

    :::image type="content" source="media/doc-common-process/new-linked-service.png" alt-text="Capture d’écran montrant la création d’un service lié avec l’interface utilisateur Azure Data Factory.":::

    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

    :::image type="content" source="media/doc-common-process/new-linked-service-synapse.png" alt-text="Capture d’écran montrant la création d’un service lié avec l’interface utilisateur Azure Synapse.":::

2. Recherchez Informix et sélectionnez le connecteur Informix.

   :::image type="content" source="media/connector-informix/informix-connector.png" alt-text="Capture d’écran du connecteur Informix.":::    


1. Configurez les informations du service, testez la connexion et créez le nouveau service lié.

   :::image type="content" source="media/connector-informix/configure-informix-linked-service.png" alt-text="Capture d’écran de la configuration du service lié pour Informix.":::

## <a name="connector-configuration-details"></a>Détails de configuration du connecteur

Les sections suivantes fournissent des détails sur les propriétés utilisées pour définir des entités Data Factory spécifiques au connecteur Informix.

## <a name="linked-service-properties"></a>Propriétés du service lié

Les propriétés prises en charge pour le service lié Informix sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété type doit être définie sur : **Informix** | Oui |
| connectionString | Chaîne de connexion ODBC excluant la partie informations d’identification. Vous pouvez spécifier la chaîne de connexion ou utiliser le système DSN (nom de la source de données) que vous avez configuré sur la machine exécutant le runtime d’intégration (vous devez toujours spécifier la partie informations d’identification dans le service lié en conséquence). <br> Vous pouvez également définir un mot de passe dans Azure Key Vault et extraire la configuration `password` de la chaîne de connexion. Pour plus d’informations, consultez la section [Stocker des informations d’identification dans Azure Key Vault](store-credentials-in-key-vault.md).| Oui |
| authenticationType | Type d’authentification utilisé pour se connecter au magasin de données Informix.<br/>Les valeurs autorisées sont les suivantes : **De base** et **Anonyme**. | Oui |
| userName | Spécifiez le nom d’utilisateur si vous utilisez l’authentification de base. | Non |
| mot de passe | Spécifiez le mot de passe du compte d’utilisateur que vous avez défini pour le nom d’utilisateur. Marquez ce champ en tant que SecureString afin de le stocker en toute sécurité, ou [référencez un secret stocké dans Azure Key Vault](store-credentials-in-key-vault.md). | Non |
| credential | Partie de la chaîne de connexion contenant les informations d’accès, spécifiée dans un format de valeurs de propriété spécifique au pilote. Marquez ce champ comme SecureString. | Non |
| connectVia | [Runtime d’intégration](concepts-integration-runtime.md) à utiliser pour la connexion à la banque de données. Un Runtime d’intégration autohébergé est nécessaire comme indiqué dans [Prérequis](#prerequisites). |Oui |

**Exemple :**

```json
{
    "name": "InformixLinkedService",
    "properties": {
        "type": "Informix",
        "typeProperties": {
            "connectionString": "<Informix connection string or DSN>",
            "authenticationType": "Basic",
            "userName": "<username>",
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

Pour obtenir la liste complète des sections et propriétés disponibles pour la définition de jeux de données, consultez l’article sur les [jeux de données](concepts-datasets-linked-services.md). Cette section fournit la liste des propriétés prises en charge par le jeu de données Informix.

Pour copier des données à partir d’Informix, les propriétés suivantes sont prises en charge :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété type du jeu de données doit être définie sur : **InformixTable** | Oui |
| tableName | Nom de la table dans Informix. | Non pour la source (si « query » est spécifié dans la source de l’activité) ;<br/>Oui pour le récepteur |

**Exemple**

```json
{
    "name": "InformixDataset",
    "properties": {
        "type": "InformixTable",
        "linkedServiceName": {
            "referenceName": "<Informix linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "<table name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriétés de l’activité de copie

Pour obtenir la liste complète des sections et des propriétés disponibles pour la définition des activités, consultez l’article [Pipelines](concepts-pipelines-activities.md). Cette section fournit la liste des propriétés prises en charge par la source Informix.

### <a name="informix-as-source"></a>Informix en tant que source

Pour copier des données à partir d’Informix, les propriétés prises en charge dans la section **source** de l'activité de copie sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété type de la source d’activité de copie doit être définie sur : **InformixSource** | Oui |
| query | Utilise la requête personnalisée pour lire des données. Par exemple : `"SELECT * FROM MyTable"`. | Non (si « tableName » est spécifié dans dataset) |

**Exemple :**

```json
"activities":[
    {
        "name": "CopyFromInformix",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Informix input dataset name>",
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
                "type": "InformixSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="informix-as-sink"></a>Informix en tant que récepteur

Si vous souhaitez copier des données dans Informix, les propriétés suivantes sont prises en charge dans la section **sink** de l’activité de copie :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété type du récepteur d’activité de copie doit être définie sur : **InformixSink** | Oui |
| writeBatchTimeout |Temps d’attente pour que l’opération d’insertion de lot soit terminée avant d’expirer.<br/>Valeurs autorisées : timespan. Exemple : « 00:30:00 » (30 minutes). |Non |
| writeBatchSize |Insère des données dans la table SQL lorsque la taille du tampon atteint writeBatchSize<br/>Valeurs autorisées : integer (nombre de lignes). |Non (la valeur par défaut est 0, détectée automatiquement) |
| preCopyScript |Spécifiez une requête SQL pour l’activité de copie à exécuter avant l’écriture de données dans la banque de données à chaque exécution. Vous pouvez utiliser cette propriété pour nettoyer des données préchargées. |Non |
| maxConcurrentConnections |La limite supérieure de connexions simultanées établies au magasin de données pendant l’exécution de l’activité. Spécifiez une valeur uniquement lorsque vous souhaitez limiter les connexions simultanées.| Non |

**Exemple :**

```json
"activities":[
    {
        "name": "CopyToInformix",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Informix output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "InformixSink"
            }
        }
    }
]
```

## <a name="lookup-activity-properties"></a>Propriétés de l’activité Lookup

Pour en savoir plus sur les propriétés, consultez [Activité Lookup](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Étapes suivantes
Pour obtenir une liste des magasins de données pris en charge comme sources et récepteurs par l’activité de copie, consultez la section sur les [magasins de données pris en charge](copy-activity-overview.md#supported-data-stores-and-formats).
