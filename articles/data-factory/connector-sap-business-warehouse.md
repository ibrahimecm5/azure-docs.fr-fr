---
title: Copier des données à partir de SAP BW
titleSuffix: Azure Data Factory & Azure Synapse
description: Apprenez à utiliser l'activité de copie dans un pipeline Azure Data Factory ou Synapse Analytics pour copier des données de SAP Business Warehouse vers des banques de données réceptrices prises en charge.
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.subservice: data-movement
ms.topic: conceptual
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: 2cbdd3dc237c3f2e7b3cf23bb844a06fdd40d605
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132325844"
---
# <a name="copy-data-from-sap-business-warehouse-using-azure-data-factory-or-synapse-analytics"></a>Copier des données de SAP Business Warehouse à l'aide d'Azure Data Factory ou Synapse Analytics
> [!div class="op_single_selector" title1="Sélectionnez la version du service Data Factory que vous utilisez :"]
> * [Version 1](v1/data-factory-sap-business-warehouse-connector.md)
> * [Version actuelle](connector-sap-business-warehouse.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Cet article explique comment utiliser l'activité de copie dans des pipelines Azure Data Factory et Synapse Analytics pour copier des données à partir d'un SAP Business Warehouse (BW). Il s’appuie sur l’article [Vue d’ensemble de l’activité de copie](copy-activity-overview.md).

>[!TIP]
>Pour en savoir plus sur la prise en charge générale du service pour l'intégration de données SAP, consultez le livre blanc [Intégration de données SAP à l'aide d'Azure Data Factory](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) offrant une présentation détaillée sur chaque connecteur SAP, une comparaison et des conseils.

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

Ce connecteur SAP Business Warehouse est pris en charge pour les activités suivantes :

- [Activité Copy](copy-activity-overview.md) avec [prise en charge de la matrice source/du récepteur](copy-activity-overview.md)
- [Activité de recherche](control-flow-lookup-activity.md)

Vous pouvez copier des données de SAP Business Warehouse vers toute banque de données réceptrice prise en charge. Pour obtenir la liste des banques de données prises en charge en tant que sources ou récepteurs par l’activité de copie, consultez le tableau [Banques de données prises en charge](copy-activity-overview.md#supported-data-stores-and-formats).

Plus précisément, ce connecteur SAP Business Warehouse prend en charge ce qui suit :

- SAP Business Warehouse **version 7.x**.
- Copie de données **d’InfoCubes et de QueryCubes** (y compris des requêtes BEx) à l’aide de requêtes MDX.
- Copie de données en utilisant une authentification de base.

>[!NOTE]
>Le connecteur SAP Business Warehouse ne prend actuellement pas en charge les paramètres avec MDX.  Si le filtrage avec des paramètres MDX est requis, vous pouvez envisager d’utiliser l’autre [connecteur SAP Open Hub](connector-sap-business-warehouse-open-hub.md) à la place.

## <a name="prerequisites"></a>Prérequis

Pour utiliser ce connecteur SAP Business Warehouse, vous devez :

- Configurer un Runtime d’intégration autohébergé. Pour plus d’informations, consultez l’article [Runtime d’intégration autohébergé](create-self-hosted-integration-runtime.md).
- Installer la **bibliothèque SAP NetWeaver** sur l’ordinateur exécutant le runtime d’intégration. Vous pouvez obtenir la bibliothèque SAP Netweaver auprès de votre administrateur SAP, ou directement depuis le [Centre de téléchargement de logiciel SAP](https://support.sap.com/swdc). Recherchez la **Note SAP 1025361** pour obtenir l’emplacement de téléchargement de la version la plus récente. Veillez à choisir la bibliothèque SAP NetWeaver **64 bits** qui correspond à votre installation du runtime d’intégration. Ensuite, installez tous les fichiers inclus dans le Kit de développement logiciel (SDK) RFC de SAP NetWeaver en fonction de la note SAP. La bibliothèque de SAP NetWeaver est également incluse dans l’installation des outils clients SAP.

>[!TIP]
>Pour résoudre le problème de connectivité à SAP BW, vérifiez que :
>- Toutes les bibliothèques de dépendance extraites du SDK NetWeaver RFC sont en place dans le dossier %windir%\system32. Il s’agit généralement des fichiers icudt34.dll, icuin34.dll, icuuc34.dll, libicudecnumber.dll, librfc32.dll, libsapucum.dll, sapcrypto.dll, sapcryto_old.dll, sapnwrfc.dll.
>- Les ports nécessaires pour se connecter au serveur SAP sont activés sur l’ordinateur IR autohébergé (en général les ports 3300 et 3201).

## <a name="getting-started"></a>Prise en main

[!INCLUDE [data-factory-v2-connector-get-started](includes/data-factory-v2-connector-get-started.md)]

## <a name="create-a-linked-service-to-sap-bw-using-ui"></a>Créer un service lié à SAP BW à l’aide de l’interface utilisateur

Utilisez les étapes suivantes pour créer un service lié à SAP BW dans l’interface utilisateur du portail Azure.

1. Accédez à l’onglet Gérer dans votre espace de travail Azure Data Factory ou Synapse et sélectionnez Services liés, puis cliquez sur Nouveau :

    # <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory).

    :::image type="content" source="media/doc-common-process/new-linked-service.png" alt-text="Créez un nouveau service lié avec l’interface utilisateur Azure Data Factory.":::

    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

    :::image type="content" source="media/doc-common-process/new-linked-service-synapse.png" alt-text="Créez un nouveau service lié avec l’interface utilisateur Azure Synapse.":::

2. Recherchez SAP et sélectionnez le SAP BW via le connecteur MDX.

    :::image type="content" source="media/connector-sap-business-warehouse/sap-business-warehouse-connector.png" alt-text="Sélectionnez le SAP BW via le connecteur MDX.":::    

1. Configurez les informations du service, testez la connexion et créez le nouveau service lié.

    :::image type="content" source="media/connector-sap-business-warehouse/configure-sap-business-warehouse-linked-service.png" alt-text="Configurez un service lié à SAP BW.":::

## <a name="connector-configuration-details"></a>Informations de configuration des connecteurs

Les sections suivantes fournissent des informations détaillées sur les propriétés utilisées pour définir les entités Data Factory spécifiques du connecteur SAP Business Warehouse.

## <a name="linked-service-properties"></a>Propriétés du service lié

Les propriétés prises en charge pour le service lié SAP Business Warehouse sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété type doit être définie sur : **SapBw** | Oui |
| server | Nom du serveur sur lequel réside l’instance SAP BW. | Oui |
| systemNumber | Numéro de système du système SAP BW.<br/>Valeur autorisée : nombre décimal à deux chiffres représenté sous forme de chaîne. | Oui |
| clientId | ID client du client dans le système SAP W.<br/>Valeur autorisée : nombre décimal à trois chiffres représenté sous forme de chaîne. | Oui |
| userName | Nom de l’utilisateur ayant accès au serveur SAP. | Oui |
| mot de passe | Mot de passe pour l’utilisateur. Marquez ce champ en tant que SecureString afin de le stocker en toute sécurité, ou [référencez un secret stocké dans Azure Key Vault](store-credentials-in-key-vault.md). | Oui |
| connectVia | [Runtime d’intégration](concepts-integration-runtime.md) à utiliser pour la connexion à la banque de données. Un Runtime d’intégration autohébergé est nécessaire comme indiqué dans [Prérequis](#prerequisites). |Oui |

**Exemple :**

```json
{
    "name": "SapBwLinkedService",
    "properties": {
        "type": "SapBw",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client id>",
            "userName": "<SAP user>",
            "password": {
                "type": "SecureString",
                "value": "<Password for SAP user>"
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

Pour obtenir la liste complète des sections et propriétés disponibles pour la définition de jeux de données, consultez l’article sur les [jeux de données](concepts-datasets-linked-services.md). Cette section fournit la liste des propriétés prises en charge par le jeu de données SAP BW.

Pour copier des données de SAP BW, affectez la valeur **SapBwCube** à la propriété type du jeu de données. Tandis qu’aucune propriété spécifique d’un type n’est prise en charge pour le jeu de données SAP BW du type RelationalTable.

**Exemple :**

```json
{
    "name": "SAPBWDataset",
    "properties": {
        "type": "SapBwCube",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SAP BW linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

Si vous utilisiez un dataset typé `RelationalTable`, il reste pris en charge tel quel, mais nous vous suggérons d’utiliser désormais le nouveau dataset.

## <a name="copy-activity-properties"></a>Propriétés de l’activité de copie

Pour obtenir la liste complète des sections et des propriétés disponibles pour la définition des activités, consultez l’article [Pipelines](concepts-pipelines-activities.md). Cette section fournit la liste des propriétés prises en charge par la source SAP BW.

### <a name="sap-bw-as-source"></a>SAP BW en tant que source

Pour copier des données à partir de SAP BW, les propriétés prises en charge dans la section **source** de l’activité de copie sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété type de la source d’activité de copie doit être définie sur : **SapBwSource** | Oui |
| query | Spécifie la requête MDX pour lire les données de l’instance SAP BW. | Oui |

**Exemple :**

```json
"activities":[
    {
        "name": "CopyFromSAPBW",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP BW input dataset name>",
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
                "type": "SapBwSource",
                "query": "<MDX query for SAP BW>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Si vous utilisiez une source de données typée `RelationalSource`, elle reste prise en charge telle quelle, mais nous vous suggérons d’utiliser désormais la nouvelle source.

## <a name="data-type-mapping-for-sap-bw"></a>Mappage de type de données pour SAP BW

Lors de la copie de données à partir de SAP BW, les mappages suivants sont utilisés entre les types de données SAP BW et les types de données intermédiaires utilisés en interne dans le service. Pour découvrir comment l’activité de copie mappe le schéma et le type de données la source au récepteur, voir [Mappages de schémas et de types de données](copy-activity-schema-and-type-mapping.md).

| Type de données SAP BW | Type de données de service intermédiaire |
|:--- |:--- |
| ACCP | Int |
| CHAR | String |
| CLNT | String |
| CURR | Decimal |
| CUKY | String |
| DEC | Decimal |
| FLTP | Double |
| INT1 | Byte |
| INT2 | Int16 |
| INT4 | Int |
| LANG | String |
| LCHR | String |
| LRAW | Byte[] |
| PREC | Int16 |
| QUAN | Decimal |
| RAW | Byte[] |
| RAWSTRING | Byte[] |
| STRING | String |
| UNITÉ | String |
| DATS | String |
| NUMC | String |
| TIMS | String |


## <a name="lookup-activity-properties"></a>Propriétés de l’activité Lookup

Pour en savoir plus sur les propriétés, consultez [Activité Lookup](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Étapes suivantes
Pour obtenir une liste des magasins de données pris en charge comme sources et récepteurs par l’activité de copie, consultez la section sur les [magasins de données pris en charge](copy-activity-overview.md#supported-data-stores-and-formats).
