---
title: indexer des données à partir de SharePoint Online (préversion)
titleSuffix: Azure Cognitive Search
description: Configurez un indexeur SharePoint Online pour automatiser l’indexation du contenu de bibliothèques de documents dans le service Recherche cognitive Azure.
author: gmndrg
ms.author: gimondra
manager: nitinme
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/01/2021
ms.openlocfilehash: f0e5afcc4f0869c4fe12a634e34e7b0669da5c75
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132137796"
---
# <a name="index-data-from-sharepoint-online"></a>indexer des données à partir de SharePoint Online

> [!IMPORTANT] 
> Le support SharePoint Online est actuellement en préversion publique sous [Conditions d’utilisation supplémentaires](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). [Demandez l’accès](https://aka.ms/azure-cognitive-search/indexer-preview) à cette fonctionnalité et, une fois l’action activée, utilisez une [API REST en préversion (préversion 2020-06-30 ou ultérieure)](search-api-preview.md) pour indexer votre contenu. La prise en charge du portail est actuellement limitée, et le Kit de développement logiciel (SDK) .NET n’est pas pris en charge.

Cet article explique comment utiliser le service Recherche cognitive Azure pour indexer des documents (fichiers PDF, documents Microsoft Office et plusieurs autres formats courants) stockés dans des bibliothèques de documents SharePoint Online. Tout d’abord, il présente les concepts de base de la définition et de la configuration de l’indexeur. Ensuite, il offre une exploration plus approfondie des comportements et des scénarios que vous êtes susceptible de rencontrer.

> [!NOTE]
> SharePoint Online prend en charge un modèle d’autorisation granulaire qui détermine l’accès par utilisateur au niveau du document. L’indexeur SharePoint Online n’extrait pas ces autorisations dans l’index de recherche, et la Recherche cognitive ne prend pas en charge l’autorisation au niveau du document. Lorsqu’un document est indexé de SharePoint Online vers un service de recherche, le contenu est accessible à toute personne disposant d’un accès en lecture à l’index. Si vous avez besoin d’autorisations au niveau du document, examinez les filtres de sécurité pour découper les résultats du contenu non autorisé. Pour plus d’informations, consultez [Découpage de sécurité à l’aide d’identités Active Directory](search-security-trimming-for-azure-search-with-aad.md).

## <a name="functionality"></a>Fonctionnalité

Dans le service Recherche cognitive Azure, un indexeur est un robot qui extrait des données et métadonnées pouvant faire l’objet d’une recherche à partir d’une source de données. L’indexeur SharePoint Online se connecte à votre site SharePoint Online et indexe des documents d’une ou plusieurs bibliothèques de documents. L’indexeur offre les fonctionnalités suivantes :
+ Indexer le contenu d’une ou plusieurs bibliothèques de documents SharePoint Online.
+ L’indexeur prend en charge l’indexation incrémentielle : il identifie le contenu modifié de la bibliothèque de documents et indexe uniquement le contenu mis à jour au cours des prochaines exécutions d’indexation. Prenons l’exemple de 5 fichiers PDF initialement indexés par l’indexeur. Si l’un d’eux est mis à jour et que l’indexeur s’exécute à nouveau, ce dernier indexe uniquement le fichier PDF qui a été mis à jour.
+ Par défaut, les images texte et normalisées sont extraites des documents qui sont indexés. Vous pouvez éventuellement ajouter un ensemble de compétences au pipeline pour améliorer l’enrichissement du contenu. Pour plus d’informations sur les ensembles de compétences, consultez l’article [Concepts des ensembles de compétences dans Recherche cognitive Azure](cognitive-search-working-with-skillsets.md).

## <a name="supported-document-formats"></a>Formats de document pris en charge

L’indexeur SharePoint Online de Recherche cognitive Azure peut extraire du texte à partir des formats de document suivants :

[!INCLUDE [search-document-data-sources](../../includes/search-blob-data-sources.md)]

## <a name="incremental-indexing-and-deletion-detection"></a>Indexation incrémentielle et détection des suppressions
Par défaut, l’indexeur SharePoint Online prend en charge l’indexation incrémentielle : il identifie le contenu modifié de la bibliothèque de documents et indexe uniquement le contenu mis à jour au cours des prochaines exécutions d’indexation. Prenons l’exemple de 5 documents Word initialement indexés par l’indexeur. Si l’un d’eux est mis à jour et que l’indexeur s’exécute à nouveau, ce dernier réindexe uniquement le document Word qui a été mis à jour.

La détection des suppressions est également prise en charge par défaut. Cela signifie que si un document est supprimé d’une bibliothèque de documents SharePoint Online, l’indexeur détectera la suppression au cours d’une prochaine exécution et enlèvera le document de l’index.

## <a name="setting-up-sharepoint-online-indexing"></a>Configuration de l’indexation SharePoint Online
Pour configurer l’indexeur SharePoint Online, vous devez effectuer certaines actions dans le portail Azure et d’autres à l’aide de l’API REST en préversion. Cette préversion n’est pas prise en charge par le SDK.

 La vidéo suivante montre comment configurer l’indexeur SharePoint Online.
 
> [!VIDEO https://www.youtube.com/embed/QmG65Vgl0JI]

### <a name="step-1-optional-enable-system-assigned-managed-identity"></a>Étape 1 (facultatif) : activer l’identité managée affectée par le système

Quand une identité managée affectée par le système est activée, Azure crée une identité pour votre service de recherche, qui peut être utilisée par l’indexeur. Cette identité est utilisée pour détecter automatiquement le locataire dans lequel le service de recherche est approvisionné.

Si le site SharePoint Online se trouve dans le même locataire que le service de recherche, vous devrez activer l’identité managée affectée par le système pour le service de recherche. Si le site SharePoint Online se trouve dans un autre locataire que le service de recherche, il n’est pas nécessaire d’activer l’identité managée affectée par le système.

![Activer l’identité managée affectée par le système](media/search-howto-index-sharepoint-online/enable-managed-identity.png "Activer l’identité managée affectée par le système")

Après avoir sélectionné **Enregistrer**, vous verrez un ID d’objet qui a été attribué à votre service de recherche.

![Identité managée affectée par le système](media/search-howto-index-sharepoint-online/system-assigned-managed-identity.png "Identité gérée attribuée par le système")

### <a name="step-2-create-an-aad-application"></a>Étape 2 : Créer une application AAD
L’indexeur SharePoint Online utilisera cette application AAD pour l’authentification. 

1.  Accédez au [portail Azure](https://portal.azure.com/).

1.  Ouvrez le menu sur le côté gauche de la page principale. Sélectionnez **Azure Active Directory**, puis **Inscriptions d’applications**. Sélectionnez **+ Nouvelle inscription**.
    1.  Attribuez un nom à votre application.
    2.  Sélectionnez **Client unique**.
    3.  Aucun URI de redirection n’est nécessaire.
    4.  Sélectionnez **Inscrire**.

1.  Sélectionnez **Autorisations de l’API** dans le menu de gauche. Sélectionnez ensuite **Ajouter une autorisation**, puis **Microsoft Graph**, puis **Autorisations déléguées**. Ajoutez les autorisations d’API suivantes : 
    1.  **Delegated - Files.Read.All** 
    2.  **Delegated - Sites.Read.All** 
    3.  **Delegated - User.Read**

    ![Autorisations d’API déléguées](media/search-howto-index-sharepoint-online/delegated-api-permissions.png "Autorisations d’API déléguées")

    Quand vous utilisez des autorisations déléguées, l’indexeur accède au site SharePoint dans le contexte utilisateur. Ainsi, quand vous exécutez l’indexeur, il a accès uniquement au contenu auquel l’utilisateur connecté a accès. La connexion de l’utilisateur s’effectue au moment de la création de l’indexeur ou de la mise à jour de la source de données. L’étape de connexion est décrite plus loin dans cet article.

1.  Sélectionnez l’onglet **Authentification**. Définissez **Autoriser les flux de clients publics** sur **Oui**, puis sélectionnez **Enregistrer**.

1.  Sélectionnez **+ Ajouter une plateforme**, puis **Applications de bureau et mobiles**. Sélectionnez `https://login.microsoftonline.com/common/oauth2/nativeclient`, puis **Configurer**.

    ![Configuration de l’authentification d’application AAD](media/search-howto-index-sharepoint-online/aad-app-authentication-configuration.png "Configuration de l’authentification d’application AAD")

1.  Accordez le consentement administrateur (nécessaire uniquement pour certains locataires).

    Certains locataires sont verrouillés, et le consentement administrateur est alors nécessaire pour ces autorisations d’API déléguées. Dans ce cas, vous devez demander à un administrateur d’accorder son consentement pour cette application AAD avant de créer l’indexeur.

    Cette exigence ne s’impose pas à tous les locataires. Nous vous recommandons donc d’ignorer cette étape pour le moment et de continuer à suivre les instructions. Vous saurez si vous avez besoin d’un consentement administrateur à la création de l’indexeur, si l’authentification échoue et que le système indique qu’un administrateur doit approuver l’authentification. Dans ce cas, demandez à un administrateur de locataire d’accorder son consentement à l’aide du bouton ci-dessous.

    ![Accorder le consentement administrateur pour l’application AAD](media/search-howto-index-sharepoint-online/aad-app-grant-admin-consent.png "Accorder le consentement administrateur pour l’application AAD")

<a name="create-data-source"></a>

### <a name="step-3-create-data-source"></a>Étape 3 : Créer une source de données
> [!IMPORTANT] 
> À partir de cette section et pour les étapes qui suivent, vous devez utiliser l’API REST en préversion. Si vous n’êtes pas familiarisé avec l’API REST de Recherche cognitive Azure, nous vous suggérons de vous reporter à ce [guide de démarrage rapide](search-get-started-rest.md).

Une source de données spécifie les données à indexer, les informations d’identification nécessaires pour accéder aux données et les stratégies qui identifient efficacement les changements dans les données (telles que des lignes modifiées ou supprimées). Une source de données peut être utilisée par plusieurs indexeurs dans le même service de recherche.

Pour l’indexation SharePoint, la source de données doit avoir les propriétés requises suivantes :
+ **name** est le nom unique de la source de données au sein de votre service de recherche.
+ Le **type** doit être « sharepoint ». Cette valeur respecte la casse.
+ la propriété **credentials** fournit le point de terminaison SharePoint Online et l’ID (client) d’application AAD. `https://microsoft.sharepoint.com/teams/MySharePointSite` est un exemple de point de terminaison SharePoint Online. Vous pouvez récupérer le point de terminaison SharePoint Online en accédant à la page d’accueil de votre site SharePoint et en copiant l’URL à partir du navigateur.
+ **container** spécifie la bibliothèque de documents à indexer. Pour plus d’informations sur la création du conteneur, consultez la section [Contrôle des documents indexés](#controlling-which-documents-are-indexed) de ce document.

Pour créer une source de données :

```http
POST https://[service name].search.windows.net/datasources?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [admin key]

{
    "name" : "sharepoint-datasource",
    "type" : "sharepoint",
    "credentials" : { "connectionString" : "SharePointOnlineEndpoint=[SharePoint Online site url];ApplicationId=[AAD App ID];TenantId=[SharePoint Online site tenant id]" },
    "container" : { "name" : "defaultSiteLibrary", "query" : null }
}
```

> [!NOTE]
> Si le site SharePoint Online se trouve dans le même locataire que le service de recherche et si l’identité managée affectée par le système est activée, il n’est pas nécessaire d’inclure `TenantId` dans la chaîne de connexion. Si le site SharePoint Online se trouve dans un autre locataire du service de recherche, `TenantId` doit être inclus.

### <a name="step-4-create-an-index"></a>Étape 4 : Créer un index
L’index spécifie les champs d’un document, les attributs et d’autres constructions qui façonnent l’expérience de recherche.

Voici comment créer un index avec un champ de contenu pouvant faire l’objet d’une recherche pour stocker le texte extrait des documents d’une bibliothèque de documents :

```http
POST https://[service name].search.windows.net/indexes?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "sharepoint-index",
    "fields": [
        { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
        { "name": "metadata_spo_item_name", "type": "Edm.String", "key": false, "searchable": true, "filterable": false, "sortable": false, "facetable": false },
        { "name": "metadata_spo_item_path", "type": "Edm.String", "key": false, "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "metadata_spo_item_content_type", "type": "Edm.String", "key": false, "searchable": false, "filterable": true, "sortable": false, "facetable": true },
        { "name": "metadata_spo_item_last_modified", "type": "Edm.DateTimeOffset", "key": false, "searchable": false, "filterable": false, "sortable": true, "facetable": false },
        { "name": "metadata_spo_item_size", "type": "Edm.Int64", "key": false, "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
    ]
}

```

> [!IMPORTANT]
> Seul [`metadata_spo_site_library_item_id`](#metadata) peut être utilisé en tant que champ clé dans un index rempli par l’indexeur SharePoint Online. Si un champ clé n’existe pas dans la source de données, `metadata_spo_site_library_item_id` est automatiquement mappé au champ clé.

Pour plus d’informations, consultez [Créer un index (API REST)](/rest/api/searchservice/create-index).

### <a name="step-5-create-an-indexer"></a>Étape 5 : Créer un indexeur
Un indexeur connecte une source de données à un index de recherche cible et fournit une planification afin d’automatiser l’actualisation des données. Une fois l’index et la source de données créés, vous êtes prêt à créer l’indexeur.

Au cours de cette section, vous serez invité à vous connecter avec les informations d’identification de votre organisation, ayant accès au site SharePoint. Si possible, nous vous recommandons de créer un compte d’utilisateur d’organisation et de donner à ce nouvel utilisateur les autorisations exactes que vous souhaitez attribuer à l’indexeur.

La création de l’indexeur s’effectue en quelques étapes :

1.  Envoyez une requête pour tenter de créer l’indexeur.

    ```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30-Preview
    Content-Type: application/json
    api-key: [admin key]
    
        {
          "name" : "sharepoint-indexer",
          "dataSourceName" : "sharepoint-datasource",
          "targetIndexName" : "sharepoint-index",
          "fieldMappings" : [
            { 
              "sourceFieldName" : "metadata_spo_site_library_item_id", 
              "targetFieldName" : "id", 
              "mappingFunction" : { 
                "name" : "base64Encode" 
              } 
            }
          ]
        }
    
    ```

1.  Quand vous créez l’indexeur pour la première fois, la création échoue et l’erreur suivante s’affiche. Accédez au lien figurant dans le message d’erreur. Si vous n’accédez pas au lien dans un délai de 10 minutes, le code expire et vous devez recréer la [source de données](#create-data-source).

    ```http
    {
        "error": {
            "code": "",
            "message": "Error with data source: To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code <CODE> to authenticate.  Please adjust your data source definition in order to proceed."
        }
    }
    ```

1.  Indiquez le code fourni dans le message d’erreur.

    ![Entrer le code de l’appareil](media/search-howto-index-sharepoint-online/enter-device-code.png "Entrer le code de l’appareil")

1.  L’indexeur SharePoint accède au contenu SharePoint comme utilisateur connecté. L’utilisateur connecté est celui qui se connecte au cours de cette étape. Ainsi, si vous vous connectez avec un compte d’utilisateur qui n’a pas accès à un document à indexer de la bibliothèque de documents, l’indexeur n’y aura pas accès non plus.

    Si possible, nous vous recommandons de créer un compte d’utilisateur et de donner à ce nouvel utilisateur les autorisations exactes que vous souhaitez attribuer à l’indexeur.

1.  Approuvez les autorisations demandées.

    ![Approuver les autorisations d’API](media/search-howto-index-sharepoint-online/aad-app-approve-api-permissions.png "Approuver les autorisations d’API")

1.  Renvoyez la requête de création de l’indexeur. Cette fois, la requête doit aboutir. 

    ```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30-Preview
    Content-Type: application/json
    api-key: [admin key]
    
    {
        "name" : "sharepoint-indexer",
        "dataSourceName" : "sharepoint-datasource",
        "targetIndexName" : "sharepoint-index"
    }
    ```

### <a name="step-6-check-the-indexer-status"></a>Étape 6 : Vérifier l’état de l’indexeur
Une fois l’indexeur créé, vous pouvez vérifier son état en effectuant la requête suivante.

```http
GET https://[service name].search.windows.net/indexers/sharepoint-indexer/status?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [admin key]
```

Pour plus d’informations sur l’état de l’indexeur, consultez [Get Indexer Status](/rest/api/searchservice/get-indexer-status).

## <a name="updating-the-data-source"></a>Mise à jour de la source de données
Si l’objet source de données ne fait l’objet d’aucune mise à jour, l’indexeur peut s’exécuter selon une planification sans aucune intervention de l’utilisateur. Toutefois, chaque fois que l’objet source de données Recherche cognitive Azure est mis à jour, vous devez vous reconnecter pour que l’indexeur s’exécute. Par exemple, si vous modifiez la requête de source de données, vous devez vous reconnecter avec `https://microsoft.com/devicelogin` et un nouveau code.

Une fois la source de données mise à jour, effectuez les étapes suivantes :

1.  Lancez manuellement une exécution de l’indexeur.

    ```http
    POST https://[service name].search.windows.net/indexers/sharepoint-indexer/run?api-version=2020-06-30-Preview  
    Content-Type: application/json
    api-key: [admin key]
    ```

    Pour plus d’informations sur la requête d’exécution de l’indexeur, consultez [Run Indexer](/rest/api/searchservice/run-indexer).

1.  Vérifiez l’état de l’indexeur. Si la dernière exécution de l’indexeur génère une erreur indiquant que vous devez accéder à la `https://microsoft.com/devicelogin`, accédez à cette page et indiquez le nouveau code. 

    ```http
    GET https://[service name].search.windows.net/indexers/sharepoint-indexer/status?api-version=2020-06-30-Preview
    Content-Type: application/json
    api-key: [admin key]
    ```

    Pour plus d’informations sur l’état de l’indexeur, consultez [Get Indexer Status](/rest/api/searchservice/get-indexer-status).

1.  Connexion

1.  Lancez manuellement une nouvelle exécution de l’indexeur et vérifiez l’état de l’indexeur. Cette fois, l’exécution de l’indexeur doit démarrer correctement.

<a name="metadata"></a>

## <a name="indexing-document-metadata"></a>Indexation des métadonnées de document
Si vous avez défini l’indexeur pour indexer les métadonnées de document, les métadonnées suivantes seront disponibles pour l’indexation.

| Identificateur | Type | Description | 
| ------------- | -------------- | ----------- |
| metadata_spo_site_library_item_id | Edm.String | Clé combinant l’ID de site, l’ID de bibliothèque et l’ID d’élément, identifiant de façon unique un élément dans une bibliothèque de documents pour un site. |
| metadata_spo_site_id | Edm.String | ID du site SharePoint Online. |
| metadata_spo_library_id | Edm.String | ID de la bibliothèque de documents. |
| metadata_spo_item_id | Edm.String | ID de l’élément (document) de la bibliothèque. |
| metadata_spo_item_last_modified | Edm.DateTimeOffset | Date/heure (UTC) de la dernière modification de l’élément. |
| metadata_spo_item_name | Edm.String | Nom de l'élément. |
| metadata_spo_item_size | Edm.Int64 | Taille (en octets) de l’élément. | 
| metadata_spo_item_content_type | Edm.String | Type de contenu de l’élément. | 
| metadata_spo_item_extension | Edm.String | Extension de l’élément. |
| metadata_spo_item_weburi | Edm.String | URI de l’élément. |
| metadata_spo_item_path | Edm.String | Combinaison du chemin parent et du nom de l’élément. | 

L’indexeur SharePoint Online prend également en charge les métadonnées spécifiques à chaque type de document. Pour plus d’informations, consultez [Propriétés des métadonnées de contenu utilisées dans Recherche cognitive Azure](search-blob-metadata-properties.md).

> [!NOTE]
> Pour indexer les métadonnées personnalisées, [`additionalColumns` doit être spécifié dans la définition de requête](#query)

<a name="controlling-which-documents-are-indexed"></a>

## <a name="controlling-which-documents-are-indexed"></a>Contrôle des documents indexés
Un seul indexeur SharePoint Online peut indexer du contenu à partir d’une ou plusieurs bibliothèques de documents. Utilisez le paramètre *container* quand vous créez votre source de données pour spécifier les bibliothèques de documents à indexer. Le paramètre *container* de la source de données a deux propriétés : *name* et *query*. 

### <a name="name"></a>Nom
La propriété *name* est obligatoire et doit avoir l’une des trois valeurs suivantes :
+ *defaultSiteLibrary*
    + Indexe tout le contenu de la bibliothèque de documents par défaut d’un site.
+   *allSiteLibraries*
    + Indexe tout le contenu de toutes les bibliothèques de documents d’un site. Les bibliothèques de documents d’un sous-site ne sont pas indexées. Elles peuvent toutefois être spécifiées dans la propriété *query*.
+   *useQuery*
    + Seul le contenu défini dans la propriété *query* est indexé.

<a name="query"></a>

### <a name="query"></a>Requête
La propriété *query* est composée de paires mot clé/valeur. Les mots clés qui peuvent être utilisés sont indiqués ci-après. Les valeurs sont des URL de site ou de bibliothèque de documents.

> [!NOTE]
> Pour obtenir la valeur d’un mot clé particulier, nous vous recommandons d’ouvrir SharePoint Online dans un navigateur, d’accéder à la bibliothèque de documents que vous essayez d’inclure/d’exclure et de copier l’URI à partir du navigateur. Il s’agit du moyen le plus simple d’obtenir la valeur à utiliser avec un mot clé dans la requête.

| Mot clé | Description de la requête | Exemple |
| ------------- | -------------- | ----------- |
| null | Si la valeur est Null ou vide, indexe la bibliothèque de documents par défaut ou toutes les bibliothèques de documents selon le nom du conteneur. | Indexe tout le contenu de la bibliothèque du site par défaut : <br><br>  ``` "container" : { "name" : "defaultSiteLibrary", "query" : null } ``` |
| includeLibrariesInSite | Indexe le contenu de toutes les bibliothèques du site défini dans la chaîne de connexion. Celles-ci sont limitées aux sous-sites de votre site. <br><br> La valeur *query* pour ce mot clé doit être l’URI du site ou du sous-site. | Indexe tout le contenu de toutes les bibliothèques de documents de mysite. <br><br> ``` "container" : { "name" : "useQuery", "query" : "includeLibrariesInSite=https://mycompany.sharepoint.com/mysite" } ``` |
| includeLibrary | Indexe le contenu de cette bibliothèque. <br><br> La valeur *query* pour ce mot clé doit être dans l’un des formats suivants : <br><br> Exemple 1 : <br><br> *includeLibrary=[site ou sous-site]/[bibliothèque de documents]* <br><br> Exemple 2 : <br><br> URI copié à partir de votre navigateur. | Indexe tout le contenu de MyDocumentLibrary : <br><br> Exemple 1 : <br><br> ``` "container" : { "name" : "useQuery", "query" : "includeLibrary=https://mycompany.sharepoint.com/mysite/MyDocumentLibrary" } ``` <br><br> Exemple 2 : <br><br> ``` "container" : { "name" : "useQuery", "query" : "includeLibrary=https://mycompany.sharepoint.com/teams/mysite/MyDocumentLibrary/Forms/AllItems.aspx" } ``` |
| excludeLibrary |  N’indexe pas le contenu de cette bibliothèque. <br><br> La valeur *query* pour ce mot clé doit être dans l’un des formats suivants : <br><br> Exemple 1 : <br><br> *excludeLibrary=[URI du site ou sous-site]/[bibliothèque de documents]* <br><br> Exemple 2 : <br><br> URI copié à partir de votre navigateur. | Indexe tout le contenu de toutes vos bibliothèques, à l’exception de MyDocumentLibrary : <br><br> Exemple 1 : <br><br> ``` "container" : { "name" : "useQuery", "query" : "includeLibrariesInSite=https://mysite.sharepoint.com/subsite1; excludeLibrary=https://mysite.sharepoint.com/subsite1/MyDocumentLibrary" } ``` <br><br> Exemple 2 : <br><br> ``` "container" : { "name" : "useQuery", "query" : "includeLibrariesInSite=https://mycompany.sharepoint.com/teams/mysite; excludeLibrary=https://mycompany.sharepoint.com/teams/mysite/MyDocumentLibrary/Forms/AllItems.aspx" } ``` |
| additionalColumns | Indexe les colonnes de cette bibliothèque. <br><br> La valeur de requête pour ce mot clé doit inclure une liste de noms de colonnes, séparés par des virgules, que vous souhaitez indexer. Utilisez une double barre oblique inverse pour échapper des points-virgules et des virgules dans les noms de colonne : <br><br> Exemple 1 : <br><br> additionalColumns=MyCustomColumn,MyCustomColumn2 <br><br> Exemple 2 : <br><br> additionalColumns=MyCustomColumnWith\\,,MyCustomColumn2With\\; | Indexe tout le contenu de MyDocumentLibrary : <br><br> Exemple 1 : <br><br> ``` "container" : { "name" : "useQuery", "query" : "includeLibrary=https://mycompany.sharepoint.com/mysite/MyDocumentLibrary;additionalColumns=MyCustomColumn,MyCustomColumn2" } ``` <br><br> Notez les doubles barres obliques inverses lors de l’échappement des caractères : JSON exige qu’une barre oblique inverse soit échappée par une autre barre oblique inverse. <br><br> Exemple 2 : <br><br> ``` "container" : { "name" : "useQuery", "query" : "includeLibrary=https://mycompany.sharepoint.com/teams/mysite/MyDocumentLibrary/Forms/AllItems.aspx;additionalColumns=MyCustomColumnWith\\,,MyCustomColumnWith\\;" } ``` |

## <a name="index-by-file-type"></a>Indexer par type de fichier
Vous pouvez contrôler les documents qui sont indexés et ignorés.

### <a name="include-documents-having-specific-file-extensions"></a>Inclure les documents ayant des extensions de fichier spécifiques
Vous pouvez indexer uniquement les documents avec les extensions de nom de fichier que vous spécifiez à l’aide du paramètre de configuration d’indexeur `indexedFileNameExtensions`. La valeur est une chaîne contenant une liste d'extensions de fichier séparées par des virgules (précédées d'un point). Par exemple, pour indexer uniquement les documents .PDF et .DOCX, procédez comme suit :

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [admin key]

{
    ... other parts of indexer definition
    "parameters" : { "configuration" : { "indexedFileNameExtensions" : ".pdf,.docx" } }
}
```

### <a name="exclude-documents-having-specific-file-extensions"></a>Exclure les documents ayant des extensions de fichier spécifiques
Vous pouvez exclure de l’indexation les documents avec des extensions de nom de fichier spécifiques à l’aide du paramètre de configuration `excludedFileNameExtensions`. La valeur est une chaîne contenant une liste d'extensions de fichier séparées par des virgules (précédées d'un point). Par exemple, pour indexer tous les contenus sauf ceux qui ont les extensions .PNG et .JPEG, procédez comme suit :

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [admin key]

{
    ... other parts of indexer definition
    "parameters" : { "configuration" : { "excludedFileNameExtensions" : ".png,.jpeg" } }
}
```

Si les paramètres `indexedFileNameExtensions` et `excludedFileNameExtensions` sont tous deux présents, la Recherche cognitive Azure regarde d’abord `indexedFileNameExtensions`, puis `excludedFileNameExtensions`. Cela signifie que, si la même extension de fichier est présente dans les deux listes, elle sera exclue de l'indexation.

## <a name="handling-errors"></a>Gestion des erreurs
Par défaut, l’indexeur SharePoint Online s’arrête dès qu’il rencontre un document avec un type de contenu non pris en charge (par exemple, une image). Vous pouvez évidemment utiliser le paramètre `excludedFileNameExtensions` pour ignorer certains types de contenu. Toutefois, vous devrez peut-être indexer des documents sans connaître à l’avance tous les types de contenu possibles. Pour poursuivre l’indexation quand un type de contenu non pris en charge est détecté, définissez le paramètre de configuration `failOnUnsupportedContentType` sur false :

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [admin key]

{
    ... other parts of indexer definition
    "parameters" : { "configuration" : { "failOnUnsupportedContentType" : false } }
}
```

Pour certains documents, le service Recherche cognitive Azure ne parvient pas à déterminer le type de contenu ou à traiter un document avec un autre type de contenu pris en charge. Pour ignorer ce mode d’échec, définissez le paramètre de configuration `failOnUnprocessableDocument` sur false :

```http
"parameters" : { "configuration" : { "failOnUnprocessableDocument" : false } }
```

La Recherche cognitive Azure limite la taille des documents indexés. Ces limites sont documentées dans [Limites de service de Recherche cognitive Azure](./search-limits-quotas-capacity.md). Par défaut, les documents surdimensionnés sont traités comme des erreurs. Toutefois, vous pouvez toujours indexer des métadonnées de stockage de documents surdimensionnés en définissant le paramètre de configuration `indexStorageMetadataOnlyForOversizedDocuments` sur true :

```http
"parameters" : { "configuration" : { "indexStorageMetadataOnlyForOversizedDocuments" : true } }
```

Vous pouvez également poursuivre l’indexation si des erreurs se produisent à tout moment du traitement, que ce soit durant l’analyse de documents ou l’ajout de documents à un index. Pour ignorer un nombre spécifique d’erreurs, définissez les paramètres de configuration `maxFailedItems` et `maxFailedItemsPerBatch` sur les valeurs souhaitées. Par exemple :

```http
{
    ... other parts of indexer definition
    "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 10 }
}
```

## <a name="see-also"></a>Voir aussi
+ [Indexeurs dans Recherche cognitive Azure](search-indexer-overview.md)
+ [Propriétés des métadonnées de contenu utilisées dans Recherche cognitive Azure](search-blob-metadata-properties.md)
