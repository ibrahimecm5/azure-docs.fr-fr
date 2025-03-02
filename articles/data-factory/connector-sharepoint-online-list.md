---
title: Copier des données à partir de SharePoint Online List
titleSuffix: Azure Data Factory & Azure Synapse
description: Découvrez comment copier des données dans des magasins de données récepteurs pris en charge à partir de SharePoint Online List en utilisant une activité de copie dans un pipeline Azure Data Factory ou Azure Synapse Analytics.
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.custom: synapse
ms.topic: conceptual
ms.date: 09/09/2021
ms.author: jianleishen
ms.openlocfilehash: ad9adb2c9f6b8308aba79f9e9bd1eadbf755760a
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124763934"
---
# <a name="copy-data-from-sharepoint-online-list-by-using-azure-data-factory-or-azure-synapse-analytics"></a>Copier des données à partir de SharePoint Online List à l’aide d’Azure Data Factory ou d’Azure Synapse Analytics
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Cet article décrit comment utiliser l’activité de copie dans des pipelines Azure Data Factory et Azure Synapse pour copier des données à partir de SharePoint Online List. Il s’appuie sur l’article [Activité de copie](copy-activity-overview.md), qui présente une vue d’ensemble de cette activité.

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

Ce connecteur SharePoint Online List est pris en charge pour les activités suivantes :

- [Activité Copy](copy-activity-overview.md) avec [prise en charge de la matrice source/du récepteur](copy-activity-overview.md)
- [Activité de recherche](control-flow-lookup-activity.md)

Il est possible de copier des données de SharePoint Online List vers n’importe quel magasin de données récepteur pris en charge. Pour obtenir la liste des magasins de données pris en charge en tant que sources et récepteurs pour l’activité de copie, consultez [Magasins de données et formats pris en charge](copy-activity-overview.md#supported-data-stores-and-formats).

Plus précisément, ce connecteur SharePoint List Online utilise l’authentification du principal du service et récupère les données suivant le protocole OData.

> [!TIP]
> Ce connecteur prend en charge la copie de données à partir d’une **liste** SharePoint Online List, et non de fichiers. Découvrez comment copier un fichier dans la section [Copier un fichier à partir de SharePoint Online](#copy-file-from-sharepoint-online).

## <a name="prerequisites"></a>Prérequis

Le connecteur SharePoint List Online utilise l’authentification du principal du service pour se connecter à SharePoint. Suivez la procédure ci-dessous pour le configurer :

1. Inscrivez une entité d’application dans Azure Active Directory (Azure AD) en suivant les instructions de la section [Inscrire votre application à un locataire Azure AD](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant). Prenez note des valeurs suivantes, qui vous permettent de définir le service lié :

    - ID de l'application
    - Clé de l'application
    - ID client

2. Accordez une autorisation de site SharePoint Online à votre application inscrite : 

    > [!NOTE]
    > L’autorisation de propriétaire de site SharePoint Online est nécessaire pour cette opération. Pour trouver le propriétaire, accédez à la page d’accueil du site -> cliquez sur « X membres » dans le coin à droite -> regardez qui dispose du rôle « Propriétaire ».

    1. Ouvrez le lien du site SharePoint Online, par exemple `https://[your_site_url]/_layouts/15/appinv.aspx` (remplacez l’URL du site).
    2. Recherchez l’ID d’application que vous avez enregistré, renseignez les champs vides, puis cliquez sur « Créer ».

        - Domaine d'application : `localhost.com`
        - URL de redirection : `https://www.localhost.com`
        - XML de la demande d’autorisation :

        ```xml
        <AppPermissionRequests AllowAppOnlyPolicy="true">
            <AppPermissionRequest Scope="http://sharepoint/content/sitecollection/web" Right="Read"/>
        </AppPermissionRequests>
        ```

        :::image type="content" source="media/connector-sharepoint-online-list/sharepoint-online-grant-permission.png" alt-text="Autorisation SharePoint accordée":::

    3. Cliquez sur « Faire confiance à cette application ».

## <a name="get-started"></a>Bien démarrer

[!INCLUDE [data-factory-v2-connector-get-started](includes/data-factory-v2-connector-get-started.md)]

## <a name="create-a-linked-service-to-a-sharepoint-online-list-using-ui"></a>Créer un service lié à une liste SharePoint Online à l'aide de l'interface utilisateur

Suivez les étapes suivantes pour créer un service lié à une liste SharePoint Online dans l'interface utilisateur du portail Azure.

1. Accédez à l’onglet Gérer dans votre espace de travail Azure Data Factory ou Synapse et sélectionnez Services liés, puis cliquez sur Nouveau :

    # <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory).

    :::image type="content" source="media/doc-common-process/new-linked-service.png" alt-text="Capture d’écran montrant la création d’un service lié avec l’interface utilisateur Azure Data Factory.":::

    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

    :::image type="content" source="media/doc-common-process/new-linked-service-synapse.png" alt-text="Capture d’écran de la création d’un nouveau service lié avec l’interface utilisateur Azure Synapse.":::

2. Recherchez SharePoint et sélectionnez le connecteur SharePoint Online List.

    :::image type="content" source="media/connector-sharepoint-online-list/sharepoint-online-list-connector.png" alt-text="Capture d'écran du connecteur de liste SharePoint Online.":::    

1. Configurez les informations du service, testez la connexion et créez le nouveau service lié.

    :::image type="content" source="media/connector-sharepoint-online-list/configure-sharepoint-online-list-linked-service.png" alt-text="Capture d’écran de la configuration du service lié pour une liste SharePoint en ligne.":::

## <a name="connector-configuration-details"></a>Informations de configuration des connecteurs

Les sections suivantes fournissent des informations sur les propriétés permettant de définir les entités propres au connecteur SharePoint Online List.

## <a name="linked-service-properties"></a>Propriétés du service lié

Les propriétés prises en charge pour un service lié SharePoint Online List sont les suivantes :

| **Propriété**        | **Description**                                              | **Obligatoire** |
| ------------------- | ------------------------------------------------------------ | ------------ |
| type                | La propriété type doit être définie sur :  **SharePointOnlineList**.  | Oui          |
| siteUrl             | URL du site SharePoint Online, par exemple `https://contoso.sharepoint.com/sites/siteName`. | Oui          |
| servicePrincipalId  | ID (client) de l’application inscrite dans Azure Active Directory. | Oui          |
| servicePrincipalKey | La clé de l’application. Marquez ce champ en tant que **SecureString** afin de le stocker en toute sécurité, ou [référencez un secret stocké dans Azure Key Vault](store-credentials-in-key-vault.md). | Oui          |
| tenantId            | ID du locataire sous lequel réside l’application.          | Oui          |
| connectVia          | [Runtime d’intégration](concepts-integration-runtime.md) à utiliser pour la connexion au magasin de données. Pour plus d’informations, consultez [Prérequis](#prerequisites), plus haut dans cet article. À défaut de spécification, l’Azure Integration Runtime par défaut est utilisé. | Non           |

**Exemple :**

```json
{
    "name": "SharePointOnlineList",
    "properties": {
        "type": "SharePointOnlineList",
        "typeProperties": {
            "siteUrl": "<site URL>",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenantId": "<tenant ID>"
        }
    }
}
```

## <a name="dataset-properties"></a>Propriétés du jeu de données

Pour obtenir la liste complète des sections et propriétés disponibles pour la définition de jeux de données, consultez [Jeux de données et services liés](concepts-datasets-linked-services.md). Cette section fournit la liste des propriétés prises en charge par le jeu de données de la table SAP.

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété **type** du jeu de données doit être définie sur **SharePointOnlineLResource**. | Oui |
| listName | Nom de la liste SharePoint Online List. | Oui |

**Exemple**

```json
{
    "name": "SharePointOnlineListDataset",
    "properties":
    {
        "type": "SharePointOnlineListResource",
        "linkedServiceName": {
            "referenceName": "<SharePoint Online List linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties":
        {
            "listName": "<name of the list>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriétés de l’activité de copie

Pour obtenir la liste complète des sections et des propriétés permettant de définir des activités, consultez [Pipelines](concepts-pipelines-activities.md).  La section suivante donne la liste des propriétés prises en charge par la source SharePoint Online List.

### <a name="sharepoint-online-list-as-source"></a>Source SharePoint Online List

Pour copier des données à partir de SharePoint Online List, les propriétés prises en charge dans la section **source** de l’activité de copie sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété **type** de la source de l’activité de copie doit être définie sur **SharePointOnlineListSource**. | Oui |
| query | Options de requête OData personnalisées pour filtrer les données. Exemple : `"$top=10&$select=Title,Number"`. | Non |
| httpRequestTimeout | Délai d’expiration (en secondes) pour que la requête HTTP reçoive une réponse. Valeur par défaut : 300 (5 minutes). | Non |

**Exemple**

```json
"activities":[
    {
        "name": "CopyFromSharePointOnlineList",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SharePoint Online List input dataset name>",
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
            "source": {
                "type": "SharePointOnlineListSource",
                "query": "<OData query e.g. $top=10&$select=Title,Number>"
            }, 
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

> [!NOTE]
> Il n’est pas possible de sélectionner plus d’un type de données *choix* pour une source SharePoint Online List.

## <a name="data-type-mapping-for-sharepoint-online-list"></a>Mappage de type de données pour SharePoint Online List

Lors de la copie de données à partir de SharePoint Online List, les mappages suivants sont utilisés entre les types de données SharePoint Online List et les types de données intermédiaires utilisés en interne par le service.

| **Type de données SharePoint Online**                 | **Type de données OData**                                  | **Type de données intermédiaire** |
| ----------------------------------------------- | ---------------------------------------------------- | ---------------------------------------- |
| Ligne de texte unique                             | Edm.String                                           | String                                   |
| Plusieurs lignes de texte                          | Edm.String                                           | String                                   |
| Choix (menu de sélection)                    | Edm.String                                           | String                                   |
| Nombre (1, 1.0, 100)                            | Edm.Double                                           | Double                                   |
| Devise ($, ¥, &euro;)                              | Edm.Double                                           | Double                                   |
| Date et heure                                   | Edm.DateTime                                         | DateTime                                 |
| Recherche (informations déjà présentes sur ce site)       | Edm.Int32                                            | Int32                                    |
| Oui/Non (case à cocher)                              | Edm.Boolean                                          | Boolean                                  |
| Personne ou groupe                                 | Edm.Int32                                            | Int32                                    |
| Lien hypertexte ou image                            | Edm.String                                           | String                                   |
| Calculé (à partir d’autres colonnes) | Edm.String / Edm.Double / Edm.DateTime / Edm.Boolean | Chaîne / Double / DateHeure / Booléen     |
| Pièce jointe                                      | Non pris en charge                                        |                                          |
| Sortie de la tâche                                    | Non pris en charge                                        |                                          |
| Données externes                                   | Non pris en charge                                        |                                          |
| Métadonnées gérées                                | Non pris en charge                                        |                                          |

## <a name="copy-file-from-sharepoint-online"></a>Copie d’un fichier à partir de SharePoint Online

Pour copier un fichier à partir de SharePoint Online, vous pouvez utiliser **l’activité web** afin d’authentifier et de récupérer un jeton d’accès auprès de SPO, puis le transmettre à **l’activité de copie** suivante afin de copier des données avec le **connecteur HTTP comme source**.

:::image type="content" source="media/connector-sharepoint-online-list/sharepoint-online-copy-file-flow.png" alt-text="Flux de copie de fichier SharePoint":::

1. Suivez la section [Prérequis](#prerequisites) pour créer une application AAD et accorder l’autorisation à SharePoint Online. 

2. Créez une **activité web** pour récupérer le jeton d’accès auprès de SharePoint Online :

    - **URL** : `https://accounts.accesscontrol.windows.net/[Tenant-ID]/tokens/OAuth/2`. Remplacez l’ID de locataire.
    - **Méthode** : POST
    - **Headers** :
        - Content-Type : application/x-www-form-urlencoded
    - **Corps** : `grant_type=client_credentials&client_id=[Client-ID]@[Tenant-ID]&client_secret=[Client-Secret]&resource=00000003-0000-0ff1-ce00-000000000000/[Tenant-Name].sharepoint.com@[Tenant-ID]`. Remplacez l’ID client (ID d’application), la clé secrète client (clé d’application), l’ID locataire et le nom du locataire (du locataire SharePoint).

    > [!CAUTION]
    > Définissez l’option Sortie sécurisée sur « True » dans l’activité web pour empêcher la journalisation en texte brut de la valeur du jeton. Pour toutes les autres activités qui consomment cette valeur, l’option d’entrée sécurisée doit être définie sur « True ».

3. Chaînez avec une **activité de copie** en utilisant comme source le connecteur HTTP pour copier le contenu du fichier SharePoint Online :

    - Service lié HTTP :
        - **URL de base** : `https://[site-url]/_api/web/GetFileByServerRelativeUrl('[relative-path-to-file]')/$value`. Remplacez l’URL du site et le chemin relatif du fichier. Exemple de chemin relatif de fichier : `/sites/site2/Shared Documents/TestBook.xlsx`.
        - **Type d’authentification** : Anonymes *(pour utiliser le jeton du porteur configuré dans la source de l’activité de copie ultérieurement)*
    - Jeu de données : choisissez le format souhaité. Pour copier le fichier tel quel, sélectionnez le type « Binaire ».
    - Source de l’activité de copie :
        - **Méthode de demande** : GET
        - **En-tête supplémentaires** : utilisez l’expression `@{concat('Authorization: Bearer ', activity('<Web-activity-name>').output.access_token)}`, qui se sert du jeton du porteur généré par l’activité web en amont comme en-tête d’autorisation. Remplacez le nom de l’activité web.
    - Configurez le récepteur de l’activité de copie comme d’habitude.

> [!NOTE]
> Même si une application Azure AD dispose des autorisations `FullControl` sur SharePoint Online, vous ne pouvez pas copier les fichiers des bibliothèques de documents avec IRM activé.

## <a name="lookup-activity-properties"></a>Propriétés de l’activité Lookup

Pour en savoir plus sur les propriétés, consultez [Activité Lookup](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir la liste des magasins de données pris en charge en tant que sources et récepteurs pour l’activité de copie, consultez [Magasins de données et formats pris en charge](copy-activity-overview.md#supported-data-stores-and-formats).
