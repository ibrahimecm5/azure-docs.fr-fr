---
title: Copier des données à partir de Google AdWords
description: Découvrez comment utiliser l'activité de copie dans un pipeline Azure Data Factory ou Synapse Analytics pour copier des données Google AdWords vers des banques de données réceptrices prises en charge.
titleSuffix: Azure Data Factory & Azure Synapse
ms.author: jianleishen
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: conceptual
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: fb7510c4be3b71b575de5b29f49babbfdd0943e6
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124815183"
---
# <a name="copy-data-from-google-adwords-using-azure-data-factory-or-synapse-analytics"></a>Copier des données à partir de Google AdWords avec Azure Data Factory ou Synapse Analytics

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]


Cet article explique comment utiliser l'activité de copie dans Azure Data Factory ou Synapse Analytics pour copier des données de Google AdWords. Il s’appuie sur l’article [Vue d’ensemble de l’activité de copie](copy-activity-overview.md).

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

Ce connecteur Google AdWords est pris en charge pour les activités suivantes :

- [Activité Copy](copy-activity-overview.md) avec [prise en charge de la matrice source/du récepteur](copy-activity-overview.md)
- [Activité de recherche](control-flow-lookup-activity.md)


Vous pouvez copier les données depuis Google AdWords vers toute banque de données réceptrice prise en charge. Pour obtenir la liste des banques de données prises en charge en tant que sources ou récepteurs par l’activité de copie, consultez le tableau [Banques de données prises en charge](copy-activity-overview.md#supported-data-stores-and-formats).

La service fournit un pilote intégré qui permet la connexion. Vous n'avez donc pas besoin d'installer manuellement un pilote à l'aide de ce connecteur.

## <a name="getting-started"></a>Prise en main

[!INCLUDE [data-factory-v2-connector-get-started](includes/data-factory-v2-connector-get-started.md)]

## <a name="create-a-linked-service-to-google-adwords-using-ui"></a>Créer un service lié à Google AdWords à l’aide de l’interface utilisateur

Suivez les étapes suivantes pour créer un service lié à Google AdWords dans l’interface utilisateur du portail Azure.

1. Accédez à l’onglet Gérer dans votre espace de travail Azure Data Factory ou Synapse, sélectionnez Services liés, puis cliquez sur Nouveau :

    # <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory).

    :::image type="content" source="media/doc-common-process/new-linked-service.png" alt-text="Capture d’écran montrant la création d’un service lié avec l’interface utilisateur Azure Data Factory.":::

    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

    :::image type="content" source="media/doc-common-process/new-linked-service-synapse.png" alt-text="Capture d’écran de la création d’un service lié avec l’interface utilisateur Azure Synapse.":::

2. Recherchez Google et sélectionnez le connecteur Google AdWords.

   :::image type="content" source="media/connector-google-adwords/google-adwords-connector.png" alt-text="Capture d’écran du connecteur Google AdWords.":::    


1. Configurez les informations du service, testez la connexion et créez le nouveau service lié.

   :::image type="content" source="media/connector-google-adwords/configure-google-adwords-linked-service.png" alt-text="Capture d’écran de la configuration du service lié pour Google AdWords.":::

## <a name="connector-configuration-details"></a>Informations de configuration du connecteur

Les sections suivantes fournissent des informations détaillées sur les propriétés utilisées pour définir les entités Data Factory spécifiques du connecteur Google AdWords.

## <a name="linked-service-properties"></a>Propriétés du service lié

Les propriétés prises en charge pour le service lié Google AdWords sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété type doit être définie sur : **GoogleAdWords** | Oui |
| clientCustomerID | ID client du client du compte AdWords pour lequel vous souhaitez extraire des données de rapport.  | Oui |
| developerToken | Jeton de développeur associé au compte de gestionnaire que vous utilisez pour accorder l’accès à l’API AdWords.  Vous pouvez choisir de marquer ce champ comme SecureString pour le stocker en toute sécurité, ou stocker le mot de passe dans Azure Key Vault et laisser l'activité de copie en tirer (pull) les données lors de la copie. Pour plus d'informations, consultez la page [Stocker des informations d'identification dans Key Vault](store-credentials-in-key-vault.md). | Oui |
| authenticationType | Mécanisme d’authentification OAuth 2.0 utilisé pour l’authentification. ServiceAuthentication ne peut être utilisé que sur un runtime d’intégration auto-hébergé. <br/>Les valeurs autorisées sont les suivantes : **ServiceAuthentication**, **UserAuthentication** | Oui |
| refreshToken | Jeton d’actualisation obtenu depuis Google pour autoriser l’accès à AdWords pour UserAuthentication. Vous pouvez choisir de marquer ce champ comme SecureString pour le stocker en toute sécurité, ou stocker le mot de passe dans Azure Key Vault et laisser l'activité de copie en tirer (pull) les données lors de la copie. Pour plus d'informations, consultez la page [Stocker des informations d'identification dans Key Vault](store-credentials-in-key-vault.md). | Non |
| clientId | ID client de l’application Google utilisé pour acquérir le jeton d’actualisation. Vous pouvez choisir de marquer ce champ comme SecureString pour le stocker en toute sécurité, ou stocker le mot de passe dans Azure Key Vault et laisser l'activité de copie en tirer (pull) les données lors de la copie. Pour plus d'informations, consultez la page [Stocker des informations d'identification dans Key Vault](store-credentials-in-key-vault.md). | Non |
| clientSecret | Clé secrète client de l’application google utilisée pour acquérir le jeton d’actualisation. Vous pouvez choisir de marquer ce champ comme SecureString pour le stocker en toute sécurité, ou stocker le mot de passe dans Azure Key Vault et laisser l'activité de copie en tirer (pull) les données lors de la copie. Pour plus d'informations, consultez la page [Stocker des informations d'identification dans Key Vault](store-credentials-in-key-vault.md). | Non |
| email | ID d’e-mail du compte de service utilisé pour ServiceAuthentication et qui ne peut être utilisé que sur un runtime d’intégration auto-hébergé.  | Non |
| keyFilePath | Chemin complet du fichier de clé .p12 utilisé pour authentifier l’adresse e-mail du compte de service et qui ne peut être utilisé que sur un runtime d’intégration auto-hébergé.  | Non |
| trustedCertPath | Chemin complet du fichier .pem contenant les certificats d’autorité de certification approuvés permettant de vérifier le serveur en cas de connexion TLS. Cette propriété n’est disponible que si le protocole TLS est utilisé sur un runtime d’intégration auto-hébergé. Valeur par défaut : le fichier cacerts.pem installé avec le runtime d’intégration.  | Non |
| useSystemTrustStore | Indique s’il faut utiliser un certificat d’autorité de certification provenant du magasin de confiance du système ou d’un fichier PEM spécifié. La valeur par défaut est false.  | Non |

**Exemple :**

```json
{
    "name": "GoogleAdWordsLinkedService",
    "properties": {
        "type": "GoogleAdWords",
        "typeProperties": {
            "clientCustomerID" : "<clientCustomerID>",
            "developerToken": {
                "type": "SecureString",
                "value": "<developerToken>"
            },
            "authenticationType" : "ServiceAuthentication",
            "refreshToken": {
                "type": "SecureString",
                "value": "<refreshToken>"
            },
            "clientId": {
                "type": "SecureString",
                "value": "<clientId>"
            },
            "clientSecret": {
                "type": "SecureString",
                "value": "<clientSecret>"
            },
            "email" : "<email>",
            "keyFilePath" : "<keyFilePath>",
            "trustedCertPath" : "<trustedCertPath>",
            "useSystemTrustStore" : true,
        }
    }
}

```

## <a name="dataset-properties"></a>Propriétés du jeu de données

Pour obtenir la liste complète des sections et propriétés disponibles pour la définition de jeux de données, consultez l’article sur les [jeux de données](concepts-datasets-linked-services.md). Cette section fournit la liste des propriétés prises en charge par le jeu de données Google AdWords.

Pour copier des données à partir de Google AdWords, définissez la propriété type du jeu de données sur **GoogleAdWordsObject**. Les propriétés prises en charge sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété type du jeu de données doit être définie sur : **GoogleAdWordsObject** | Oui |
| tableName | Nom de la table. | Non (si « query » dans la source de l’activité est spécifié) |

**Exemple**

```json
{
    "name": "GoogleAdWordsDataset",
    "properties": {
        "type": "GoogleAdWordsObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<GoogleAdWords linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}

```

## <a name="copy-activity-properties"></a>Propriétés de l’activité de copie

Pour obtenir la liste complète des sections et des propriétés disponibles pour la définition des activités, consultez l’article [Pipelines](concepts-pipelines-activities.md). Cette section fournit la liste des propriétés prises en charge par la source Google AdWords.

### <a name="google-adwords-as-source"></a>Google AdWords comme source

Pour copier des données à partir de Google AdWords, définissez le type de source sur **GoogleAdWordsSource** dans l’activité de copie. Les propriétés prises en charge dans la section **source** de l’activité de copie sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété type de la source d’activité de copie doit être définie sur : **GoogleAdWordsSource** | Oui |
| query | Utiliser la requête SQL personnalisée pour lire les données. Par exemple : `"SELECT * FROM MyTable"`. | Non (si « tableName » est spécifié dans dataset) |

**Exemple :**

```json
"activities":[
    {
        "name": "CopyFromGoogleAdWords",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<GoogleAdWords input dataset name>",
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
                "type": "GoogleAdWordsSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="lookup-activity-properties"></a>Propriétés de l’activité Lookup

Pour en savoir plus sur les propriétés, consultez [Activité Lookup](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Étapes suivantes
Pour obtenir une liste des magasins de données pris en charge comme sources et récepteurs par l’activité de copie, consultez la section sur les [magasins de données pris en charge](copy-activity-overview.md#supported-data-stores-and-formats).
