---
title: Configurer une connexion à un compte de stockage à l’aide d’une identité managée
titleSuffix: Azure Cognitive Search
description: Découvrez comment configurer une connexion d’indexeur à un compte Stockage Azure à l’aide d’une identité managée
author: gmndrg
ms.author: gimondra
manager: nitinme
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/01/2021
ms.openlocfilehash: d714c4cd1b507a8722bb6407fb991a2b158b72e6
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2021
ms.locfileid: "131894371"
---
# <a name="set-up-a-connection-to-an-azure-storage-account-using-a-managed-identity"></a>Configurer une connexion à un compte Stockage Azure à l’aide d’une identité managée

Cette page explique comment configurer une connexion d’indexeur à un compte Stockage Azure à l’aide d’une identité managée au lieu de fournir des informations d’identification dans la chaîne de connexion de l’objet source de données.

Vous pouvez utiliser une identité managée affectée par le système ou une identité managée affectée par l’utilisateur (préversion).

Cet article suppose que vous connaissez les concepts et la configuration des indexeurs. Si vous ne connaissez pas les indexeurs, commencez par les liens suivants :

* [Présentation de l’indexeur](search-indexer-overview.md)
* [Indexeur de blobs Azure](search-howto-indexing-azure-blob-storage.md)
* [Indexeur Azure Data Lake Storage Gen2](search-howto-index-azure-data-lake-storage.md)
* [Indexeur de tables Azure](search-howto-indexing-azure-tables.md)

## <a name="1---set-up-a-managed-identity"></a>1 - Configurer une identité managée

Configurez l’[identité managée](../active-directory/managed-identities-azure-resources/overview.md) pour un service Recherche cognitive Azure à l’aide de l’une des options suivantes. 

Le service de recherche doit être de niveau De base ou supérieur.

### <a name="option-1---turn-on-system-assigned-managed-identity"></a>Option 1 – Activer l’identité managée affectée par le système

Quand une identité managée affectée par le système est activée, Azure crée une identité pour votre service de recherche qui peut être utilisée pour vous authentifier auprès d’autres services Azure au sein du même locataire et du même abonnement. Vous pouvez ensuite utiliser cette identité dans les attributions de contrôle d’accès en fonction du rôle Azure (Azure RBAC) qui autorisent l’accès aux données pendant l’indexation.

![Turn on system assigned managed identity](./media/search-managed-identities/turn-on-system-assigned-identity.png "Activer l’identité managée affectée par le système")

Après avoir sélectionné **Enregistrer**, vous verrez un ID d’objet qui a été attribué à votre service de recherche.

![ID d’objet](./media/search-managed-identities/system-assigned-identity-object-id.png "ID de l'objet")

### <a name="option-2---assign-a-user-assigned-managed-identity-to-the-search-service-preview"></a>Option 2 – Affecter une identité managée affectée par l’utilisateur au service de recherche (préversion)

Si vous n’avez pas encore créé d’identité managée affectée par l’utilisateur, vous devez en créer une. Une identité managée affectée par l’utilisateur est une ressource Azure.

1. Connectez-vous au [portail Azure](https://portal.azure.com/).

1. Sélectionnez **+ Créer une ressource**.

1. Dans la barre de recherche « Services de recherche et marketplace », recherchez « Identité managée affectée par l’utilisateur », puis sélectionnez **Créer**.

1. Donnez à l’identité un nom descriptif.

Ensuite, attribuez l’identité managée affectée par l’utilisateur au service de recherche. Pour ce faire, vous pouvez utiliser l’[API de gestion en préversion du 01/04/2021](/rest/api/searchmanagement/2021-04-01-preview/services/create-or-update).

La propriété d’identité prend un type et une ou plusieurs identités affectées par l’utilisateur complètes :

* **type** représente le type de l’identité. Les valeurs valides sont « SystemAssigned », « UserAssigned » ou « SystemAssigned, UserAssigned » pour les deux. La valeur « None » permet d’effacer du service de recherche toutes les identités affectées.

* **userAssignedIdentities** comprend les détails de l’identité managée affectée par l’utilisateur. Le format est le suivant :

  ```bash
    /subscriptions/<your-subscription-ID>/resourcegroups/<your-resource-group-name>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<your-managed-identity-name>
  ```

Exemple d’attribution d’identité managée affectée par l’utilisateur :

```http
PUT https://management.azure.com/subscriptions/[subscription ID]/resourceGroups/[resource group name]/providers/Microsoft.Search/searchServices/[search service name]?api-version=2021-04-01-preview
Content-Type: application/json

{
  "location": "[region]",
  "sku": {
    "name": "[sku]"
  },
  "properties": {
    "replicaCount": [replica count],
    "partitionCount": [partition count],
    "hostingMode": "default"
  },
  "identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
      "/subscriptions/[subscription ID]/resourcegroups/[resource group name]/providers/Microsoft.ManagedIdentity/userAssignedIdentities/[name of managed identity]": {}
    }
  }
} 
```

## <a name="2---add-a-role-assignment"></a>2 – Ajouter une attribution de rôle

Au cours de cette étape, vous allez autoriser votre service Recherche cognitive Azure ou votre identité managée affectée par l’utilisateur à lire les données du compte de stockage.

1. Dans le portail Azure, accédez au compte de stockage qui contient les données que vous souhaitez indexer.

2. Sélectionnez **Contrôle d’accès (IAM)**

3. Sélectionnez **Ajouter**, puis **Ajouter une attribution de rôle**.

    ![Ajouter une attribution de rôle](./media/search-managed-identities/add-role-assignment-storage.png "Ajouter une attribution de rôle")

4. Sélectionnez les rôles appropriés en fonction du type de compte de stockage que vous souhaitez indexer :

    * Stockage Blob Azure implique que vous ajoutiez votre service de recherche au rôle **Lecteur des données blob du stockage**.
    * Azure Data Lake Storage Gen2 nécessite que vous ajoutiez votre service de recherche au rôle **Lecteur des données blob du stockage**.
    * Stockage Table Azure implique que vous ajoutiez votre service de recherche au rôle **Lecteur et accès aux données**.

5. Laissez **Attribuer l’accès à** sur **Utilisateur, groupe ou principal de service Azure AD**.

6. Si vous utilisez une identité managée affectée par le système, recherchez votre service de recherche, puis sélectionnez-le. Si vous utilisez une identité managée affectée par l’utilisateur, recherchez le nom de l’identité managée affectée par l’utilisateur, puis sélectionnez-la. Sélectionnez **Enregistrer**.

    Exemple pour Stockage Blob Azure et Azure Data Lake Storage Gen2 avec une identité managée affectée par le système :

    ![Add Storage Blob Data Reader role assignment](./media/search-managed-identities/add-role-assignment-storage-blob-data-reader.png "Ajouter une attribution de rôle Lecteur des données blob du stockage")

    Exemple pour Stockage Table Azure avec une identité managée affectée par le système :

    ![Add reader and data access role assignment](./media/search-managed-identities/add-role-assignment-reader-and-data-access.png "Ajouter une attribution de rôle Lecteur et accès aux données")

Pour obtenir des exemples de code en C#, consultez [Index Data Lake Gen2 using Azure AD](https://github.com/Azure-Samples/azure-search-dotnet-samples/blob/master/data-lake-gen2-acl-indexing/README.md) (Indexer Data Lake Gen2 à l’aide d’Azure AD) sur GitHub.

## <a name="3---create-the-data-source"></a>3 – Créer la source de données

Créez la source de données et indiquez une identité managée affectée par le système ou une identité managée affectée par l’utilisateur (préversion). Notez que vous n’utilisez plus l’API REST de gestion dans les étapes ci-dessous.

### <a name="option-1---create-the-data-source-with-a-system-assigned-managed-identity"></a>Option 1 : Créer la source de données avec une identité managée affectée par le système

L’[API REST](/rest/api/searchservice/create-data-source), le portail Azure et le [kit SDK .NET](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourceconnection) prennent en charge l’utilisation d’une identité managée affectée par le système. Voici un exemple de création d’une source de données pour indexer des données à partir d’un compte de stockage à l’aide de l’[API REST](/rest/api/searchservice/create-data-source) et d’une chaîne de connexion d’identité gérée. Le format de chaîne de connexion d’identité managée est le même pour l’API REST, le kit de développement logiciel (SDK) .NET et le portail Azure.

Lors de l’indexation d’un compte de stockage, la source de données doit avoir les propriétés requises suivantes :

* **name** est le nom unique de la source de données au sein de votre service de recherche.
* **type**
    * Stockage Blob Azure : `azureblob`
    * Stockage Table Azure : `azuretable`
    * Azure Data Lake Storage Gen2 : `adlsgen2`
* **credentials**
    * Le format d’**informations d’identification** est différent selon que vous utilisez ou non une identité managée. Vous indiquerez ici un ResourceId qui n’a pas de clé de compte ni de mot de passe. Le ResourceId doit inclure l’ID d’abonnement du compte de stockage, le groupe de ressources du compte de stockage et le nom du compte de stockage.
    * Format de l’identité managée : 
        * *ResourceId=/subscriptions/**votre ID d’abonnement**/resourceGroups/**le nom de votre groupe de ressources**/providers/Microsoft.Storage/storageAccounts/**le nom de votre compte de stockage**/;*
* **container** spécifie le nom d’un conteneur ou d’une table dans votre compte de stockage. Par défaut, tous les objets blob du conteneur sont récupérables. Si vous souhaitez indexer uniquement les objets blob dans un répertoire virtuel particulier, vous pouvez spécifier ce répertoire à l’aide du paramètre facultatif **query**.

Exemple de création d’un objet source de données blob à l’aide de l’[API REST](/rest/api/searchservice/create-data-source) :

```http
POST https://[service name].search.windows.net/datasources?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "blob-datasource",
    "type" : "azureblob",
    "credentials" : { 
        "connectionString" : "ResourceId=/subscriptions/[subscription ID]/resourceGroups/[resource group name]/providers/Microsoft.Storage/storageAccounts/[storage account name]/;" 
    },
    "container" : { 
        "name" : "my-container", "query" : "<optional-virtual-directory-name>" 
    }
}   
```

### <a name="option-2---create-the-data-source-with-a-user-assigned-managed-identity"></a>Option 2 - Créer la source de données avec une identité managée affectée par l’utilisateur

L’API REST en préversion du 30/04/2021 prend en charge l’identité managée affectée par l’utilisateur. Vous trouverez ci-dessous un exemple de création de source de données pour indexer les données d’un compte de stockage à l’aide de l’[API REST](/rest/api/searchservice/create-data-source), d’une chaîne de connexion d’identité managée et de l’identité managée affectée par l’utilisateur.

Lors de l’indexation d’un compte de stockage, la source de données doit avoir les propriétés requises suivantes :

* **name** est le nom unique de la source de données au sein de votre service de recherche.
* **type**
    * Stockage Blob Azure : `azureblob`
    * Stockage Table Azure : `azuretable`
    * Azure Data Lake Storage Gen2 : `adlsgen2`
* **credentials**
    * Le format d’**informations d’identification** est différent selon que vous utilisez ou non une identité managée. Vous indiquerez ici un ResourceId qui n’a pas de clé de compte ni de mot de passe. Le ResourceId doit inclure l’ID d’abonnement du compte de stockage, le groupe de ressources du compte de stockage et le nom du compte de stockage.
    * Format de l’identité managée : 
        * *ResourceId=/subscriptions/**votre ID d’abonnement**/resourceGroups/**le nom de votre groupe de ressources**/providers/Microsoft.Storage/storageAccounts/**le nom de votre compte de stockage**/;*
* **container** spécifie le nom d’un conteneur ou d’une table dans votre compte de stockage. Par défaut, tous les objets blob du conteneur sont récupérables. Si vous souhaitez indexer uniquement les objets blob dans un répertoire virtuel particulier, vous pouvez spécifier ce répertoire à l’aide du paramètre facultatif **query**.
* **identity** contient la collection d’identités managées affectées par l’utilisateur. Une seule identité managée affectée par l’utilisateur doit être fournie au moment de la création de la source de données.
    * **userAssignedIdentities** comprend les détails de l’identité managée affectée par l’utilisateur.
        * Format de l’identité managée affectée par l’utilisateur : 
            * /subscriptions/**ID d’abonnement**/resourcegroups/**nom du groupe de ressources**/providers/Microsoft.ManagedIdentity/userAssignedIdentities/**nom de l’identité managée**

Exemple de création d’un objet source de données blob à l’aide de l’[API REST](/rest/api/searchservice/create-data-source) :

```http
POST https://[service name].search.windows.net/datasources?api-version=2021-04-30-preview
Content-Type: application/json
api-key: [admin key]

{
    "name" : "blob-datasource",
    "type" : "azureblob",
    "credentials" : { 
        "connectionString" : "ResourceId=/subscriptions/[subscription ID]/resourceGroups/[resource group name]/providers/Microsoft.Storage/storageAccounts/[storage account name]/;" 
    },
    "container" : { 
        "name" : "my-container", "query" : "<optional-virtual-directory-name>" 
    },
    "identity" : { 
        "@odata.type": "#Microsoft.Azure.Search.DataUserAssignedIdentity",
        "userAssignedIdentity" : "/subscriptions/[subscription ID]/resourcegroups/[resource group name]/providers/Microsoft.ManagedIdentity/userAssignedIdentities/[managed identity name]" 
    }
}   
```

## <a name="4---create-the-index"></a>4 – Créer l’index

L’index spécifie les champs d’un document, les attributs et d’autres constructions qui façonnent l’expérience de recherche.

Voici comment créer un index avec un champ `content` pouvant faire l'objet d'une recherche afin de stocker le texte extrait d'objets blob :   

```http
    POST https://[service name].search.windows.net/indexes?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
          ]
    }
```

Pour plus d’informations sur la création d’index, consultez [Création d'un index](/rest/api/searchservice/create-index)

## <a name="5---create-the-indexer"></a>5 – Créer l’indexeur

Un indexeur connecte une source de données à un index de recherche cible et fournit une planification afin d’automatiser l’actualisation des données.

Une fois l’index et la source de données créés, vous êtes prêt à créer l’indexeur.

Exemple de définition d’indexeur pour un indexeur de blobs :

```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "blob-indexer",
      "dataSourceName" : "blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }
```

Cet indexeur s’exécutera toutes les deux heures (intervalle de planification défini sur « PT2H »). Pour exécuter un indexeur toutes les 30 minutes, définissez l’intervalle sur « PT30M ». Le plus court intervalle pris en charge est de 5 minutes. La planification est facultative : en cas d’omission, un indexeur ne s’exécute qu’une seule fois lorsqu’il est créé. Toutefois, vous pouvez à tout moment exécuter un indexeur à la demande.   

Pour plus d’informations sur l’API Créer un indexeur, consultez [Créer un indexeur](/rest/api/searchservice/create-indexer).

Pour plus d’informations sur la définition des planifications de l’indexeur, consultez [Comment planifier des indexeurs pour la Recherche cognitive Azure](search-howto-schedule-indexers.md).

## <a name="accessing-network-secured-data-in-storage-accounts"></a>Accès aux données sécurisées du réseau dans les comptes de stockage

Les comptes de stockage Azure peuvent être sécurisés davantage à l’aide de pare-feu et de réseaux virtuels. Si vous souhaitez indexer le contenu d’un compte de stockage Blob ou d’un compte de stockage Data Lake Gen2 qui est sécurisé par un pare-feu ou un réseau virtuel, suivez les instructions fournies dans [Accès aux données dans les comptes de stockage de manière sécurisée via une exception de service approuvé](search-indexer-howto-access-trusted-service-exception.md).

## <a name="see-also"></a>Voir aussi

* [Indexeur de blobs Azure](search-howto-indexing-azure-blob-storage.md)
* [Indexeur Azure Data Lake Storage Gen2](search-howto-index-azure-data-lake-storage.md)
* [Indexeur de tables Azure](search-howto-indexing-azure-tables.md)
* [Exemple C# : Indexer Data Lake Gen2 à l’aide d’Azure AD (GitHub)](https://github.com/Azure-Samples/azure-search-dotnet-samples/blob/master/data-lake-gen2-acl-indexing/README.md)