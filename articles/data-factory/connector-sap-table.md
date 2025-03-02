---
title: Copier des données à partir d’une table SAP
titleSuffix: Azure Data Factory & Azure Synapse
description: Découvrez comment copier des données dans des magasins de données récepteurs pris en charge à partir d’une table SAP en utilisant une activité de copie dans un pipeline Azure Data Factory ou Azure Synapse Analytics.
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.subservice: data-movement
ms.topic: conceptual
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: e4d77aa3d4456154149c5ad38b9fdc769953f8ad
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124764048"
---
# <a name="copy-data-from-an-sap-table-using-azure-data-factory-or-azure-synapse-analytics"></a>Copier des données à partir d’une table SAP à l’aide d’Azure Data Factory ou d’Azure Synapse Analytics

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Cet article décrit comment utiliser l’activité de copie dans des pipelines Azure Data Factory et Azure Synapse Analytics pour copier des données à partir d’une table SAP. Pour plus d’informations, consultez l’article [Vue d’ensemble d’activité de copie](copy-activity-overview.md).

>[!TIP]
>Pour en savoir plus sur la prise en charge générale de l’intégration de données SAP, consultez le livre blanc [Intégration de données SAP à l’aide d’Azure Data Factory](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) offrant une présentation détaillée sur chaque connecteur SAP, une comparaison et des conseils.

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

Ce connecteur de table SAP est pris en charge pour les activités suivantes :

- [Activité Copy](copy-activity-overview.md) avec [prise en charge de la matrice source/du récepteur](copy-activity-overview.md)
- [Activité de recherche](control-flow-lookup-activity.md)

Vous pouvez copier des données à partir d’une table SAP vers n’importe quel magasin de données récepteur pris en charge. Pour obtenir la liste des banques de données prises en charge en tant que sources ou récepteurs par l’activité de copie, consultez le tableau [banques de données prises en charge](copy-activity-overview.md#supported-data-stores-and-formats).

Plus précisément, ce connecteur de table SAP prend en charge les opérations suivantes :

- Copie de données à partir d’une table SAP dans :

  - SAP ERP Central Component (SAP ECC) 7.01 ou version ultérieure (dans une pile de package de support SAP récente publiée après 2015).
  - SAP Business Warehouse (SAP BW) version 7.01 ou ultérieure (dans une pile SAP Support Package Stack postérieure à 2015).
  - SAP S/4HANA.
  - Autres produits de SAP Business Suite version 7.01 ou ultérieure (dans une pile SAP Support Package Stack postérieure à 2015).

- Copie de données à partir d'une table transparente SAP, d'une table mise en pool, d'une table clusterisée et d'une vue.
- Copie de données à l’aide de l’authentification de base ou de Secure Network Communications (SNC), si SNC est configuré.
- Connexion à un serveur d’applications SAP ou un serveur de messagerie SAP.
- Récupération de données par le biais d’une RFC par défaut ou personnalisée.

La version 7.01 ou ultérieure fait référence à la version SAP NetWeaver au lieu de la version SAP ECC. Par exemple, SAP ECC 6.0 EHP 7 dispose en général de la version NetWeaver >=7.4. Si vous n’êtes pas sûr de votre environnement, voici les étapes à suivre pour confirmer la version de votre système SAP :

1. Utilisez l’interface graphique utilisateur SAP pour vous connecter au système SAP. 
2. Accédez à **Système** -> **État**. 
3. Vérifiez la version de SAP_BASIS et assurez-vous qu’elle est supérieure ou égale à 701.  
      :::image type="content" source="./media/connector-sap-table/sap-basis.png" alt-text="Vérifier SAP_BASIS":::

## <a name="prerequisites"></a>Prérequis

Pour utiliser ce connecteur de table SAP, vous devez effectuer les opérations suivantes :

- Configurez un runtime d’intégration auto-hébergé (version 3.17 ou ultérieure). Pour plus d’informations, consultez [Créer et configurer un runtime d’intégration auto-hébergé](create-self-hosted-integration-runtime.md).

- Téléchargez la version 64 bits de [SAP Connector for Microsoft .NET 3.0](https://support.sap.com/en/product/connectors/msnet.html) à partir du site web de SAP et installez-la sur la machine dotée du runtime d’intégration auto-hébergé. Lors de l'installation, veillez à sélectionner l’option **Installer les assemblys dans le GAC** dans la fenêtre des **étapes de configuration facultatives**.

  :::image type="content" source="./media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png" alt-text="Installer SAP Connector pour .NET":::

- L’utilisateur SAP utilisé dans le connecteur de table SAP doit disposer des autorisations suivantes :

  - Autorisation d'utiliser des destinations RFC (Remote Function Call).
  - Autorisations relatives à l’activité Exécuter de l’objet d’autorisation S_SDSAUTH. Vous pouvez vous reporter à la note SAP 460089 sur les objets d'autorisation majoritaires. Certains RFC sont requis par le connecteur NCo sous-jacent, par exemple RFC_FUNCTION_SEARCH. 

## <a name="get-started"></a>Bien démarrer

[!INCLUDE [data-factory-v2-connector-get-started](includes/data-factory-v2-connector-get-started.md)]

## <a name="create-a-linked-service-to-an-sap-table-using-ui"></a>Créer un service lié à une table SAP à l’aide de l’interface utilisateur

Utilisez les étapes suivantes pour créer un service lié à une table SAP dans l’interface utilisateur du portail Azure.

1. Accédez à l’onglet Gérer dans votre espace de travail Azure Data Factory ou Synapse et sélectionnez Services liés, puis cliquez sur Nouveau :

    # <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory).

    :::image type="content" source="media/doc-common-process/new-linked-service.png" alt-text="Capture d’écran montrant la création d’un service lié avec l’interface utilisateur Azure Data Factory.":::

    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

    :::image type="content" source="media/doc-common-process/new-linked-service-synapse.png" alt-text="Capture d’écran de la création d’un nouveau service lié avec l’interface utilisateur Azure Synapse.":::

2. Recherchez SAP et sélectionnez le connecteur de table SAP.

    :::image type="content" source="media/connector-sap-table/sap-table-connector.png" alt-text="Capture d’écran du connecteur de table SAP.":::    

1. Configurez les informations du service, testez la connexion et créez le nouveau service lié.

    :::image type="content" source="media/connector-sap-table/configure-sap-table-linked-service.png" alt-text="Capture d’écran de la configuration d’un service de table SAP lié .":::

## <a name="connector-configuration-details"></a>Informations de configuration des connecteurs

Les sections suivantes fournissent des informations sur les propriétés utilisées pour définir les entités spécifiques au connecteur de table SAP.

## <a name="linked-service-properties"></a>Propriétés du service lié

Les propriétés prises en charge pour le service lié SAP BW Open Hub sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| `type` | La propriété `type` doit être définie sur `SapTable`. | Oui |
| `server` | Nom du serveur sur lequel réside l’instance SAP.<br/>Permet de se connecter à un serveur d'applications SAP. | Non |
| `systemNumber` | Numéro du système SAP.<br/>Permet de se connecter à un serveur d'applications SAP.<br/>Valeur autorisée : Nombre décimal à deux chiffres représenté sous forme de chaîne. | Non |
| `messageServer` | Nom d’hôte du serveur de messagerie SAP.<br/>Permet de se connecter à un serveur de messagerie SAP. | Non |
| `messageServerService` | Nom de service ou numéro de port du serveur de messagerie.<br/>Permet de se connecter à un serveur de messagerie SAP. | Non |
| `systemId` | ID du système SAP où se trouve la table.<br/>Permet de se connecter à un serveur de messagerie SAP. | Non |
| `logonGroup` | Groupe de connexion du système SAP.<br/>Permet de se connecter à un serveur de messagerie SAP. | Non |
| `clientId` | ID du client dans le système SAP.<br/>Valeur autorisée : Nombre décimal à trois chiffres représenté sous forme de chaîne. | Oui |
| `language` | Langue utilisée par le système SAP.<br/>La valeur par défaut est `EN`.| Non |
| `userName` | Nom de l’utilisateur ayant accès au serveur SAP. | Oui |
| `password` | Mot de passe de l’utilisateur. Marquez ce champ avec le type `SecureString` afin de le stocker en toute sécurité, ou [référencez un secret stocké dans Azure Key Vault](store-credentials-in-key-vault.md). | Oui |
| `sncMode` | Indicateur d’activation SNC permettant d’accéder au serveur SAP où se trouve la table.<br/>Applicable si vous voulez utiliser SNC pour vous connecter au serveur SAP.<br/>Valeurs autorisées : `0` (off, valeur par défaut) ou `1` (on). | Non |
| `sncMyName` | Nom SNC de l’indicateur permettant d’accéder au serveur SAP où se trouve la table.<br/>S’applique si `sncMode` est défini sur On. | Non |
| `sncPartnerName` | Nom SNC du partenaire de communication permettant d’accéder au serveur SAP où se trouve la table.<br/>S’applique si `sncMode` est défini sur On. | Non |
| `sncLibraryPath` | Bibliothèque du produit de sécurité externe permettant d’accéder au serveur SAP où se trouve la table.<br/>S’applique si `sncMode` est défini sur On. | Non |
| `sncQop` | Niveau SNC Quality of Protection à appliquer.<br/>S’applique si `sncMode` est défini sur On. <br/>Les valeurs autorisées sont `1` (Authentification), `2` (Intégrité), `3` (Confidentialité), `8` (Par défaut), `9` (Maximum). | Non |
| `connectVia` | Le [runtime d’intégration](concepts-integration-runtime.md) à utiliser pour se connecter à la banque de données. Un runtime d’intégration auto-hébergé est nécessaire, comme indiqué précédemment dans [Prérequis](#prerequisites). |Oui |

### <a name="example-1-connect-to-an-sap-application-server"></a>Exemple 1 : Se connecter à un serveur d'applications SAP

```json
{
    "name": "SapTableLinkedService",
    "properties": {
        "type": "SapTable",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client ID>",
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

### <a name="example-2-connect-to-an-sap-message-server"></a>Exemple 2 : Se connecter à un serveur de messagerie SAP

```json
{
    "name": "SapTableLinkedService",
    "properties": {
        "type": "SapTable",
        "typeProperties": {
            "messageServer": "<message server name>",
            "messageServerService": "<service name or port>",
            "systemId": "<system ID>",
            "logonGroup": "<logon group>",
            "clientId": "<client ID>",
            "userName": "<SAP user>",
            "password": {
                "type": "SecureString",
                "value": "<Password for SAP user>"
            }
        },
        "connectVia": {
            "referenceName": "<name of integration runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="example-3-connect-by-using-snc"></a>Exemple 3 : Se connecter via SNC

```json
{
    "name": "SapTableLinkedService",
    "properties": {
        "type": "SapTable",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client ID>",
            "userName": "<SAP user>",
            "password": {
                "type": "SecureString",
                "value": "<Password for SAP user>"
            },
            "sncMode": 1,
            "sncMyName": "<SNC myname>",
            "sncPartnerName": "<SNC partner name>",
            "sncLibraryPath": "<SNC library path>",
            "sncQop": "8"
        },
        "connectVia": {
            "referenceName": "<name of integration runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Propriétés du jeu de données

Pour obtenir la liste complète des sections et propriétés permettant de définir des jeux de données, consultez [Jeux de données](concepts-datasets-linked-services.md). Cette section fournit la liste des propriétés prises en charge par le jeu de données de la table SAP.

Pour copier des données depuis et vers le service lié SAP BW Open Hub, les propriétés suivantes sont prises en charge :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| `type` | La propriété `type` doit être définie sur `SapTableResource`. | Oui |
| `tableName` | Nom de la table SAP à partir de laquelle copier des données. | Oui |

### <a name="example"></a>Exemple

```json
{
    "name": "SAPTableDataset",
    "properties": {
        "type": "SapTableResource",
        "typeProperties": {
            "tableName": "<SAP table name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SAP table linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriétés de l’activité de copie

Pour obtenir la liste complète des sections et des propriétés permettant de définir des activités, consultez [Pipelines](concepts-pipelines-activities.md). Cette section fournit la liste des propriétés prises en charge par la source de la table SAP.

### <a name="sap-table-as-source"></a>Table SAP en tant que source

Pour copier des données à partir d’une table SAP, les propriétés suivantes sont prises en charge :

| Propriété                         | Description                                                  | Obligatoire |
| :------------------------------- | :----------------------------------------------------------- | :------- |
| `type`                             | La propriété `type` doit être définie sur `SapTableSource`.         | Oui      |
| `rowCount`                         | Nombre de lignes à récupérer.                              | Non       |
| `rfcTableFields`                 | Champs (colonnes) à copier à partir de la table SAP. Par exemple : `column0, column1`. | Non       |
| `rfcTableOptions`                | Options permettant de filtrer les lignes d’une table SAP. Par exemple : `COLUMN0 EQ 'SOMEVALUE'`. Voir également la table des opérateurs de requête SAP plus loin dans cet article. | Non       |
| `customRfcReadTableFunctionModule` | Module de fonction RFC personnalisé qui peut être utilisé pour lire des données à partir de la table SAP.<br>Vous pouvez utiliser un module de fonction RFC personnalisé pour définir la façon dont les données sont récupérées à partir de votre système SAP et retournées au service. Le module de fonction personnalisé doit avoir une interface implémentée (importation, exportation, tables) similaire à `/SAPDS/RFC_READ_TABLE2`, l'interface par défaut utilisée par le service.| Non       |
| `partitionOption`                  | Mécanisme de partition permettant d’effectuer une lecture à partir d’une table SAP. Les options prises en charge sont les suivantes : <ul><li>`None`</li><li>`PartitionOnInt` (entier normal ou valeurs entières avec remplissage à l’aide de zéros à gauche ; par exemple, `0000012345`)</li><li>`PartitionOnCalendarYear` (4 chiffres au format « AAAA »)</li><li>`PartitionOnCalendarMonth` (6 chiffres au format « AAAAMM »)</li><li>`PartitionOnCalendarDate` (8 chiffres au format « AAAAMMJJ »)</li><li>`PartitionOntime` (6 chiffres au format « HHMMSS », par exemple `235959`)</li></ul> | Non       |
| `partitionColumnName`              | Nom de la colonne utilisée pour partitionner les données.                | Non       |
| `partitionUpperBound`              | Valeur maximale de la colonne spécifiée dans `partitionColumnName` qui sera utilisée pour poursuivre le partitionnement. | Non       |
| `partitionLowerBound`              | Valeur minimale de la colonne spécifiée dans `partitionColumnName` qui sera utilisée pour poursuivre le partitionnement. (Remarque : `partitionLowerBound` ne peut pas être « 0 » quand l’option de partition est `PartitionOnInt`) | Non       |
| `maxPartitionsNumber`              | Nombre maximal de partitions à utiliser pour diviser les données. La valeur par défaut est 1.    | Non       |
| `sapDataColumnDelimiter` | Caractère unique utilisé comme délimiteur passé à SAP RFC pour fractionner les données de sortie. | Non |

>[!TIP]
>Si votre table SAP contient un grand volume de données, par exemple plusieurs milliards de lignes, utilisez `partitionOption` et `partitionSetting` pour fractionner les données en partitions plus petites. Dans ce cas, les données sont lues par partition et chaque partition de données est extraite de votre serveur SAP via un seul appel RFC.<br/>
<br/>
>Si nous prenons l’exemple de `partitionOption` avec la valeur `partitionOnInt`, le nombre de lignes dans chaque partition est calculé à l’aide de cette formule : (nombre total de lignes comprises entre `partitionUpperBound` et `partitionLowerBound`)/`maxPartitionsNumber`.<br/>
<br/>
>Pour charger des partitions de données en parallèle afin d’accélérer la copie, le degré parallèle est contrôlé par le paramètre [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) de l’activité de copie. Par exemple, si vous définissez `parallelCopies` sur la valeur quatre, le service génère et exécute simultanément quatre requêtes selon l’option de partition et les paramètres que vous avez spécifiés, chacune récupérant une partie des données de votre table SAP. Nous vous recommandons vivement de faire de `maxPartitionsNumber` un multiple de la valeur de la propriété `parallelCopies`. Lors de la copie de données dans un magasin de données basé sur des fichiers, il est également recommandé de les écrire dans un dossier sous forme fichiers multiples (spécifiez uniquement le nom du dossier). Ceci offre de meilleures performances qu’une écriture dans un fichier unique.


>[!TIP]
> L’option `BASXML` est activée par défaut pour ce connecteur de table SAP dans le service.

Dans `rfcTableOptions`, vous pouvez utiliser les opérateurs de requête SAP courants suivants pour filtrer les lignes :

| Opérateur | Description |
| :------- | :------- |
| `EQ` | Égal à |
| `NE` | Différent de |
| `LT` | Inférieur à |
| `LE` | Inférieur ou égal à |
| `GT` | Supérieur à |
| `GE` | Supérieur ou égal à |
| `IN` | Comme dans `TABCLASS IN ('TRANSP', 'INTTAB')` |
| `LIKE` | Comme dans `LIKE 'Emma%'` |

### <a name="example"></a>Exemple

```json
"activities":[
    {
        "name": "CopyFromSAPTable",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP table input dataset name>",
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
                "type": "SapTableSource",
                "partitionOption": "PartitionOnInt",
                "partitionSettings": {
                     "partitionColumnName": "<partition column name>",
                     "partitionUpperBound": "2000",
                     "partitionLowerBound": "1",
                     "maxPartitionsNumber": 500
                 }
            },
            "sink": {
                "type": "<sink type>"
            },
            "parallelCopies": 4
        }
    }
]
```

## <a name="join-sap-tables"></a>Joindre des tables SAP

Actuellement, le connecteur de table SAP ne prend en charge qu’une seule table avec le module de fonction par défaut. Pour récupérer les données jointes de plusieurs tables, vous pouvez tirer parti de la propriété [customRfcReadTableFunctionModule](#copy-activity-properties) dans le connecteur de table SAP en procédant comme suit :

- [Écrivez un module de fonction personnalisé](#create-custom-function-module) qui peut accepter une requête comme OPTIONS et appliquer votre propre logique pour récupérer les données.
- Pour « Module de fonction personnalisé », entrez le nom de votre module de fonction personnalisé.
- Pour « Options de table RFC », spécifiez l’instruction de jointure de tables à alimenter dans votre module de fonction en tant qu’OPTIONS, par exemple « `<TABLE1>` INNER JOIN `<TABLE2>` ON COLUMN0 ».

Voici un exemple :

:::image type="content" source="./media/connector-sap-table/sap-table-join.png" alt-text="Jointure de tables SAP"::: 

>[!TIP]
>Vous pouvez également envisager d’agréger les données jointes dans AFFICHAGE, qui est pris en charge par le connecteur de table SAP.
>Vous pouvez également essayer d’extraire les tables associées pour les intégrer à Azure (par exemple, Stockage Azure, Azure SQL Database), puis utiliser Data Flow pour procéder à une jointure ou un filtrage supplémentaire.

## <a name="create-custom-function-module"></a>Créer un module de fonction personnalisé

Pour la table SAP, nous prenons actuellement en charge la propriété [customRfcReadTableFunctionModule](#copy-activity-properties) dans la source de copie, ce qui vous permet de tirer parti de votre propre logique et de traiter les données.

Pour vous aider rapidement, voici quelques conditions requises pour prendre en main le « module de fonction personnalisé » :

- Définition :

    :::image type="content" source="./media/connector-sap-table/custom-function-module-definition.png" alt-text="Définition"::: 

- Exportez les données dans l’une des tables ci-dessous :

    :::image type="content" source="./media/connector-sap-table/export-table-1.png" alt-text="Table d’exportation 1"::: 

    :::image type="content" source="./media/connector-sap-table/export-table-2.png" alt-text="Table d’exportation 2":::
 
Vous trouverez ci-dessous des illustrations du fonctionnement du connecteur de table SAP avec le module de fonction personnalisé :

1. Créez une connexion avec le serveur SAP via SAP NCO.

1. Appelez le « module de fonction personnalisé » avec les paramètres définis comme suit :

    - QUERY_TABLE : nom de la table que vous définissez dans le jeu de données de la table SAP ; 
    - Délimiteur : délimiteur que vous définissez dans la source de la table SAP ; 
    - ROWCOUNT/Option/Fields : nombre de lignes/option d’agrégation/champs que vous définissez dans la source de la table.

1. Obtenez le résultat et analysez les données en suivant les méthodes ci-dessous :

    1. Analysez la valeur de la table Fields pour récupérer les schémas.

        :::image type="content" source="./media/connector-sap-table/parse-values.png" alt-text="Analyser les valeurs dans Fields":::

    1. Obtenez les valeurs de la table de sortie pour voir quelle table contient ces valeurs.

        :::image type="content" source="./media/connector-sap-table/get-values.png" alt-text="Obtenir les valeurs de la table de sortie":::

    1. Récupérez les valeurs dans la table OUT_TABLE, analysez les données, puis écrivez-les dans le récepteur.

## <a name="data-type-mappings-for-an-sap-table"></a>Mappages de type de données pour une table SAP

Lors de la copie de données à partir d’une table SAP, les mappages suivants sont utilisés à partir des types de données de table SAP en types de données intermédiaires utilisés dans le service. Pour découvrir comment l’activité de copie mappe le schéma et le type de données la source au récepteur, consultez [Mappage de schéma dans l’activité de copie](copy-activity-schema-and-type-mapping.md).

| Type SAP ABAP | Type de données de service intermédiaire |
|:--- |:--- |
| `C` (Chaîne) | `String` |
| `I` (Entier) | `Int32` |
| `F` (Flottant) | `Double` |
| `D` (Date) | `String` |
| `T` (Heure) | `String` |
| `P` P (« packed » DCB, Devise, Décimal, Qté) | `Decimal` |
| `N` (Numérique) | `String` |
| `X` (Données binaires et brutes) | `String` |

## <a name="lookup-activity-properties"></a>Propriétés de l’activité Lookup

Pour en savoir plus sur les propriétés, consultez [Activité Lookup](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Étapes suivantes

Pour obtenir une liste des magasins de données pris en charge comme sources et récepteurs par l’activité de copie, consultez [Magasins de données pris en charge](copy-activity-overview.md#supported-data-stores-and-formats).
