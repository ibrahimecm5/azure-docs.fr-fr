---
title: Configurer une connexion à Azure SQL Database à l’aide d’une identité managée
titleSuffix: Azure Cognitive Search
description: Découvrez comment configurer une connexion d’indexeur à Azure SQL Database à l’aide d’une identité managée
author: gmndrg
ms.author: gimondra
manager: nitinme
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/02/2021
ms.openlocfilehash: 1a975d8eeebe86bbdf09870ab6c450727bdd545b
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2021
ms.locfileid: "131894404"
---
# <a name="set-up-an-indexer-connection-to-azure-sql-database-using-a-managed-identity"></a>Configurer une connexion d’indexeur à Azure SQL Database à l’aide d’une identité managée

Cette page explique comment configurer une connexion d’indexeur à Azure SQL Database à l’aide d’une identité managée au lieu de fournir des informations d’identification dans la chaîne de connexion de l’objet source de données.

Vous pouvez utiliser une identité managée affectée par le système ou une identité managée affectée par l’utilisateur (préversion).

Avant d’en apprendre plus sur cette fonctionnalité, il est recommandé de comprendre ce qu’est un indexeur et savoir comment configurer un indexeur pour votre source de données. Pour plus d’informations, consultez les liens suivants :

* [Présentation de l’indexeur](search-indexer-overview.md)
* [Indexeur Azure SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)

## <a name="1---set-up-a-managed-identity"></a>1 – Configurer une identité managée

Configurez l’[identité managée](../active-directory/managed-identities-azure-resources/overview.md) à l’aide de l’une des options suivantes.

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

* **type** représente le type de l’identité. Les valeurs valides sont « SystemAssigned » ou « UserAssigned », ou « SystemAssigned, UserAssigned » si vous souhaitez utiliser les deux. La valeur « None » permet d’effacer du service de recherche toutes les identités affectées.
* **userAssignedIdentities** comprend les détails de l’identité managée affectée par l’utilisateur.
    * Format de l’identité managée affectée par l’utilisateur : 
        * /subscriptions/**ID d’abonnement**/resourcegroups/**nom du groupe de ressources**/providers/Microsoft.ManagedIdentity/userAssignedIdentities/**nom de l’identité managée**

Exemple d’affectation d’une identité managée affectée par l’utilisateur à un service de recherche :

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

## <a name="2---provision-azure-active-directory-admin-for-sql-server"></a>2 – Approvisionner un administrateur Azure Active Directory pour SQL Server

Lors de la connexion à la base de données à l’étape suivante, vous devez vous connecter avec un compte Azure Active Directory (Azure AD) disposant d’un accès administrateur à la base de données afin de permettre à votre service de recherche d’y accéder.

Consultez [Configurer et gérer l’authentification Azure AD avec Azure SQL](../azure-sql/database/authentication-aad-configure.md?tabs=azure-powershell) pour donner à votre administrateur de compte Azure AD accès administrateur à la base de données.

## <a name="3---assign-permissions-to-read-the-database"></a>3 – Attribuer des autorisations pour lire la base de données

Suivez les étapes ci-dessous pour attribuer au service de recherche ou à l’identité managée affectée par l’utilisateur l’autorisation de lire la base de données.

1. Connectez-vous à Visual Studio.

    ![Connect to Visual Studio](./media/search-managed-identities/connect-with-visual-studio.png "Connectez-vous à Visual Studio.")

2. S'authentifier à l'aide du compte Azure AD

    ![Authentifier](./media/search-managed-identities/visual-studio-authenticate.png "Authenticate")

3. Exécutez les commandes suivantes :

    Incluez les crochets autour du nom de votre service de recherche ou du nom d’identité managée affectée par l’utilisateur.
    
    ```
    CREATE USER [insert your search service name here or user-assigned managed identity name] FROM EXTERNAL PROVIDER;
    EXEC sp_addrolemember 'db_datareader', [insert your search service name here or user-assigned managed identity name];
    ```

    ![Nouvelle requête](./media/search-managed-identities/visual-studio-new-query.png "Nouvelle requête")

    ![Execute query](./media/search-managed-identities/visual-studio-execute-query.png "Exécuter la requête")

>[!NOTE]
> Si l’identité du service de recherche ou l’identité affectée par l’utilisateur de l’étape 1 sont modifiés à l’issue de cette étape, vous devez supprimer l’appartenance au rôle et l’utilisateur dans la base de données SQL, puis rajouter les autorisations en suivant à nouveau l’étape 3.
> La suppression de l’appartenance au rôle et de l’utilisateur peut être effectuée en exécutant les commandes suivantes :
> ```
> sp_droprolemember 'db_datareader', [insert your search service name or user-assigned managed identity name];
> DROP USER IF EXISTS [insert your search service name or user-assigned managed identity name];
> ```

## <a name="4---add-a-role-assignment"></a>4 – Ajouter une attribution de rôle

Au cours de cette étape, vous allez accorder à votre service Recherche cognitive Azure l’autorisation de lire les données de votre serveur SQL.

1. Dans le portail Azure, accédez à votre page Azure SQL Server.
2. Sélectionnez **Contrôle d’accès (IAM)**
3. Sélectionnez **Ajouter**, puis **Ajouter une attribution de rôle**.

    ![Ajouter une attribution de rôle](./media/search-managed-identities/add-role-assignment-sql-server.png "Ajouter une attribution de rôle")

4. Sélectionnez le rôle **Lecteur** approprié.
5. Laissez **Attribuer l’accès à** sur **Utilisateur, groupe ou principal de service Azure AD**.
6. Si vous utilisez une identité managée affectée par le système, recherchez votre service de recherche, puis sélectionnez-le. Si vous utilisez une identité managée affectée par l’utilisateur, recherchez le nom de l’identité managée affectée par l’utilisateur, puis sélectionnez-la. Sélectionnez **Enregistrer**.

    Exemple pour Azure SQL utilisant une identité managée affectée par le système :

    ![Add reader role assignment](./media/search-managed-identities/add-role-assignment-sql-server-reader-role.png "Ajouter une attribution de rôle Lecteur")

## <a name="5---create-the-data-source"></a>5 – Créer la source de données

Créez la source de données et indiquez une identité managée affectée par le système ou une identité managée affectée par l’utilisateur (préversion). Notez que vous n’utilisez plus l’API REST de gestion dans les étapes ci-dessous.

### <a name="option-1---create-the-data-source-with-a-system-assigned-managed-identity"></a>Option 1 – Créer la source de données avec une identité managée affectée par le système

L’[API REST](/rest/api/searchservice/create-data-source), le portail Azure et le [Kit de développement logiciel (SDK) .NET](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourceconnection) prennent l’identité managée affectée par le système. Voici un exemple de création d’une source de données pour indexer des données à partir d’une instance Azure SQL Database à l’aide de l’[API REST](/rest/api/searchservice/create-data-source) et d’une chaîne de connexion d’identité gérée. Le format de chaîne de connexion d’identité managée est le même pour l’API REST, le kit de développement logiciel (SDK) .NET et le portail Azure.

Lors de la création d’une source de données à l’aide de l’[API REST](/rest/api/searchservice/create-data-source), la source de données doit avoir les propriétés requises suivantes :

* **name** est le nom unique de la source de données au sein de votre service de recherche.
* **type** est `azuresql`
* **credentials**
    * Le format d’**informations d’identification** est différent selon que vous utilisez ou non une identité managée. Vous indiquerez ici un nom Initial Catalog ou Database et un ResourceId qui n’a pas de clé de compte ni de mot de passe. Le ResourceId doit inclure l’ID d’abonnement d’Azure SQL Database, le groupe de ressources d’Azure SQL Database et le nom de la base de données SQL. 
    * Format de la chaîne de connexion de l’identité managée :
        * *Initial Catalog|Database=**nom de la base de données**;ResourceId=/subscriptions/**votre ID d’abonnement**/resourceGroups/**votre nom de groupe de ressources**/providers/Microsoft.Sql/servers/**votre nom de serveur SQL**/;Connection Timeout=**durée de la connexion**;*
* **container** spécifie le nom de la table ou de l’affichage que vous souhaitez indexer.

Exemple de création d’un objet de source de données Azure SQL à l’aide de l’[API REST](/rest/api/searchservice/create-data-source) :

```
POST https://[service name].search.windows.net/datasources?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "sql-datasource",
    "type" : "azuresql",
    "credentials" : { 
        "connectionString" : "Database=[SQL database name];ResourceId=/subscriptions/[subscription ID]/resourceGroups/[resource group name]/providers/Microsoft.Sql/servers/[SQL Server name];Connection Timeout=30;"
    },
    "container" : { 
        "name" : "my-table" 
    }
} 
```

### <a name="option-2---create-the-data-source-with-a-user-assigned-managed-identity"></a>Option 2 – Créer la source de données avec une identité managée affectée par l’utilisateur

L’API REST en préversion du 30/04/2021 prend en charge l’identité managée affectée par l’utilisateur. Vous trouverez ci-dessous un exemple de création de source de données pour indexer les données d’un compte de stockage à l’aide de l’[API REST](/rest/api/searchservice/create-data-source), d’une chaîne de connexion d’identité managée et de l’identité managée affectée par l’utilisateur.

La source de données doit avoir les propriétés requises suivantes :

* **name** est le nom unique de la source de données au sein de votre service de recherche.
* **type** est `azuresql`
* **credentials**
    * Le format d’**informations d’identification** est différent selon que vous utilisez ou non une identité managée. Vous indiquerez ici un nom Initial Catalog ou Database et un ResourceId qui n’a pas de clé de compte ni de mot de passe. Le ResourceId doit inclure l’ID d’abonnement d’Azure SQL Database, le groupe de ressources d’Azure SQL Database et le nom de la base de données SQL. 
    * Format de la chaîne de connexion de l’identité managée :
        * *Initial Catalog|Database=**nom de la base de données**;ResourceId=/subscriptions/**votre ID d’abonnement**/resourceGroups/**votre nom de groupe de ressources**/providers/Microsoft.Sql/servers/**votre nom de serveur SQL**/;Connection Timeout=**durée de la connexion**;*
* **container** spécifie le nom de la table ou de l’affichage que vous souhaitez indexer.
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
    "name" : "sql-datasource",
    "type" : "azuresql",
    "credentials" : { 
        "connectionString" : "Database=[SQL database name];ResourceId=/subscriptions/[subscription ID]/resourceGroups/[resource group name]/providers/Microsoft.Sql/servers/[SQL Server name];Connection Timeout=30;"
    },
    "container" : { 
        "name" : "my-table" 
    },
    "identity" : { 
        "@odata.type": "#Microsoft.Azure.Search.DataUserAssignedIdentity",
        "userAssignedIdentity" : "/subscriptions/[subscription ID]/resourcegroups/[resource group name]/providers/Microsoft.ManagedIdentity/userAssignedIdentities/[managed identity name]"
    }
}   
```

## <a name="6---create-the-index"></a>6 – Créer l’index

L’index spécifie les champs d’un document, les attributs et d’autres constructions qui façonnent l’expérience de recherche.

Voici comment créer un index avec un champ `booktitle` pouvant faire l’objet d’une recherche :   

```
POST https://[service name].search.windows.net/indexes?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "my-target-index",
    "fields": [
        { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
        { "name": "booktitle", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
    ]
}
```

Pour plus d’informations sur la création d’index, consultez [Création d'un index](/rest/api/searchservice/create-index)

## <a name="7---create-the-indexer"></a>7 – Créer l’indexeur

Un indexeur connecte une source de données à un index de recherche cible et fournit une planification afin d’automatiser l’actualisation des données.

Une fois l’index et la source de données créés, vous êtes prêt à créer l’indexeur.

Exemple de définition d’indexeur pour un indexeur Azure SQL :

```
POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "sql-indexer",
    "dataSourceName" : "sql-datasource",
    "targetIndexName" : "my-target-index",
    "schedule" : { "interval" : "PT2H" }
}
```    

Cet indexeur s’exécutera toutes les deux heures (intervalle de planification défini sur « PT2H »). Pour exécuter un indexeur toutes les 30 minutes, définissez l’intervalle sur « PT30M ». Le plus court intervalle pris en charge est de 5 minutes. La planification est facultative : en cas d’omission, un indexeur ne s’exécute qu’une seule fois lorsqu’il est créé. Toutefois, vous pouvez à tout moment exécuter un indexeur à la demande.   

Pour plus d’informations sur l’API Créer un indexeur, consultez [Créer un indexeur](/rest/api/searchservice/create-indexer).

Pour plus d’informations sur la définition des planifications de l’indexeur, consultez [Comment planifier des indexeurs pour la Recherche cognitive Azure](search-howto-schedule-indexers.md).

## <a name="troubleshooting"></a>Dépannage

Si vous recevez une erreur lorsque l’indexeur tente de se connecter à la source de données qui indique que le client n’est pas autorisé à accéder au serveur, consultez les [erreurs courantes de l’indexeur](./search-indexer-troubleshooting.md).

## <a name="see-also"></a>Voir aussi

En savoir plus sur l’indexeur Azure SQL :
* [Indexeur Azure SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)