---
title: Utiliser la bibliothèque de client Tables Azure pour Java
description: Stockez des données structurées dans le cloud avec la bibliothèque de client Tables Azure pour Java.
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: Java
ms.topic: sample
ms.date: 12/10/2020
author: ThomasWeiss
ms.author: thweiss
ms.custom: devx-track-java
ms.openlocfilehash: e5eab216c40245aef19a7fc9ef8fd1a5bd8bc029
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132487653"
---
# <a name="how-to-use-the-azure-tables-client-library-for-java"></a>Guide pratique pour utiliser la bibliothèque de client Tables Azure pour Java

[!INCLUDE[appliesto-table-api](../includes/appliesto-table-api.md)]

[!INCLUDE [storage-selector-table-include](../../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-applies-to-storagetable-and-cosmos](../../../includes/storage-table-applies-to-storagetable-and-cosmos.md)]

Cet article explique comment créer des tables, stocker vos données et effectuer des opérations CRUD sur celles-ci. Les exemples ont été écrits en Java et utilisent la [bibliothèque de client Tables Azure pour Java][Azure Tables client library for Java]. Les scénarios abordés sont les suivants : **création**, **suppression** et **affichage d’une liste de tables**, **insertion**, **interrogation**, **modification** et **suppression** des entités d’une table. Pour plus d'informations sur les tables, consultez la section [Étapes suivantes](#next-steps) .

> [!IMPORTANT]
> La dernière version de la bibliothèque de client Tables Azure qui prend en charge Stockage Table et Table Cosmos DB est [12+][Azure Tables client library for Java].

## <a name="create-an-azure-service-account"></a>Créer un compte de service Azure

[!INCLUDE [cosmos-db-create-azure-service-account](../includes/cosmos-db-create-azure-service-account.md)]

**Créer un compte de stockage Azure**

[!INCLUDE [cosmos-db-create-storage-account](../includes/cosmos-db-create-storage-account.md)]

**Création d’un compte Azure Cosmos DB**

[!INCLUDE [cosmos-db-create-tableapi-account](../includes/cosmos-db-create-tableapi-account.md)]

## <a name="create-a-java-application"></a>Création d’une application Java

Pour utiliser les exemples fournis dans cet article :
1. Installez le [kit de développement Java (JDK)](/azure/developer/java/fundamentals/java-support-on-azure#supported-java-versions-and-update-schedule).
2. Créez un compte de stockage Azure ou un compte Azure Cosmos DB dans votre abonnement Azure.
3. Vérifiez que votre système de développement répond à la configuration minimale requise et aux dépendances listées dans le dépôt [Azure Tables client library for Java][Azure Tables client library for Java] sur GitHub.
4. Suivez les instructions pour télécharger et installer les bibliothèques Stockage Azure pour Java sur votre système à partir de ce dépôt.
5. Créez une application Java qui utilise les exemples fournis dans cet article.

## <a name="configure-your-app-to-access-table-storage"></a>Configurer votre application pour accéder à Stockage Table

Ajoutez l’entrée suivante à la section `dependencies` de votre fichier *pom.xml* :

```xml
<dependency>
  <groupId>com.azure</groupId>
  <artifactId>azure-data-tables</artifactId>
  <version>12.1.1</version>
</dependency>
```

Ensuite, ajoutez les instructions `import` suivantes au début du fichier Java dans lequel vous voulez utiliser les API Tables Azure pour accéder aux tables :

```java
// Include the following imports to use table APIs
import com.azure.data.tables.TableClient;
import com.azure.data.tables.TableClientBuilder;
import com.azure.data.tables.TableServiceClient;
import com.azure.data.tables.TableServiceClientBuilder;
import com.azure.data.tables.models.ListEntitiesOptions;
import com.azure.data.tables.models.TableEntity;
import com.azure.data.tables.models.TableEntityUpdateMode;
import com.azure.data.tables.models.TableTransactionAction;
import com.azure.data.tables.models.TableTransactionActionType;
```

## <a name="add-your-connection-string"></a>Ajouter votre chaîne de connexion

Vous pouvez vous connecter au compte de stockage Azure ou au compte de l’API Table Azure Cosmos DB. Obtenez la chaîne de connexion en fonction du type de compte que vous utilisez.

### <a name="add-an-azure-storage-connection-string"></a>Ajouter une chaîne de connexion au Stockage Azure

Un client Tables Azure peut utiliser une chaîne de connexion de stockage pour stocker des points de terminaison et des informations d’identification permettant d’accéder aux services de gestion des données. Lors de l’exécution dans une application cliente, vous devez spécifier la chaîne de connexion au Stockage au format suivant, en indiquant le nom de votre compte de Stockage et sa clé d’accès primaire, correspondant aux valeurs **AccountName** et **AccountKey**, sur le [portail Azure](https://portal.azure.com).

Cet exemple vous montre comment déclarer un champ statique pour qu’il contienne une chaîne de connexion :

```java
// Define the connection-string with your values.
public final String connectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key;" +
    "EndpointSuffix=core.windows.net;
```

### <a name="add-an-azure-cosmos-db-table-api-connection-string"></a>Ajouter une chaîne de connexion d’API Table d’Azure Cosmos DB

Un compte Azure Cosmos DB utilise une chaîne de connexion pour stocker le point de terminaison de table et vos informations d’identification. Lors de l’exécution dans une application cliente, vous devez spécifier la chaîne de connexion Azure Cosmos DB au format suivant, en indiquant le nom de votre compte Azure Cosmos DB et sa clé d’accès primaire, correspondant aux valeurs **AccountName** et **AccountKey**, sur le [portail Azure](https://portal.azure.com).

Cet exemple vous montre comment déclarer un champ statique pour qu’il contienne la chaîne de connexion Azure Cosmos DB :

```java
public final String connectionString =
    "DefaultEndpointsProtocol=https;" + 
    "AccountName=your_cosmosdb_account;" + 
    "AccountKey=your_account_key;" + 
    "TableEndpoint=https://your_endpoint;" ;
```

Dans une application exécutée au sein d’un rôle dans Azure, vous pouvez stocker cette chaîne dans le fichier de configuration de service *ServiceConfiguration.cscfg*. Vous pouvez y accéder en appelant la méthode `System.getenv`. Voici un exemple de code vous permettant d’extraire la chaîne de connexion à partir d’un élément **Setting** nommé *ConnectionString* dans le fichier de configuration de service :

```java
// Retrieve storage account from connection-string.
String connectionString = System.getenv("ConnectionString");
```

Vous pouvez également stocker la chaîne de connexion dans le fichier config.properties du projet :

```java
connectionString = DefaultEndpointsProtocol=https;AccountName=your_account;AccountKey=your_account_key;TableEndpoint=https://your_table_endpoint/
```

Les exemples ci-dessous partent du principe que vous avez utilisé l’une de ces méthodes pour obtenir la chaîne de connexion de stockage.

## <a name="create-a-table"></a>Créer une table

Un objet `TableServiceClient` vous permet d’interagir avec le service Tables afin de créer, de lister et de supprimer des tables. Le code suivant crée un objet `TableServiceClient` et l’utilise pour créer un objet `TableClient` représentant une table nommée `Employees`.

```java
try
{
    final String tableName = "Employees";

    // Create a TableServiceClient with a connection string.
    TableServiceClient tableServiceClient = new TableServiceClientBuilder()
        .connectionString(connectionString)
        .buildClient();

    // Create the table if it not exists.
    TableClient tableClient = tableServiceClient.createTableIfNotExists(tableName);

}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="list-the-tables"></a>Établir une liste des tables

Pour obtenir une liste de tables, appelez la méthode `TableServiceClient.listTables`afin de récupérer une liste de noms de tables pouvant être itérée.

```java
try
{
    // Create a TableServiceClient with a connection string.
    TableServiceClient tableServiceClient = new TableServiceClientBuilder()
        .connectionString(connectionString)
        .buildClient();

    // Loop through a collection of table names.
    tableServiceClient.listTables().forEach(tableItem -> 
        System.out.printf(tableItem.getName())
    );
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="add-an-entity-to-a-table"></a>Ajout d'une entité à une table

Le code suivant crée une instance de la classe `TableEntity` avec des données client à stocker. Le code appelle la méthode `upsertEntity` sur l’objet `TableClient`. Cette méthode insère la nouvelle entité de client dans la table `Employees` ou remplace l’entité si elle existe déjà.

```java
try
{
    final String tableName = "Employees";

    // Create a TableClient with a connection string and a table name.
     TableClient tableClient = new TableClientBuilder()
        .connectionString(connectionString)
        .tableName(tableName)
        .buildClient();

    // Create a new employee TableEntity.
    String partitionKey = "Sales";
    String rowKey = "0001";
    Map<String, Object> personalInfo= new HashMap<>();
    personalInfo.put("FirstName", "Walter");
    personalInfo.put("LastName", "Harp");
    personalInfo.put("Email", "Walter@contoso.com");
    personalInfo.put("PhoneNumber", "425-555-0101");
    TableEntity employee = new TableEntity(partitionKey, rowKey).setProperties(personalInfo);
        
    // Upsert the entity into the table
    tableClient.upsertEntity(employee);
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="insert-a-batch-of-entities"></a>Insertion d'un lot d'entités

Vous pouvez insérer un lot d'entités dans le service de Table en une seule opération d'écriture. Le code suivant crée un objet `List<TableTransactionAction>`, puis y ajoute trois opérations d’upsert. Chaque opération est ajoutée en créant un objet `TableEntity`, en définissant ses propriétés, puis en appelant la méthode `submitTransaction` sur l’objet `TableClient`.

```java
try
{
    final String tableName = "Employees";

    // Create a TableClient with a connection string and a table name.
    TableClient tableClient = new TableClientBuilder()
        .connectionString(connectionString)
        .tableName(tableName)
        .buildClient();

    String partitionKey = "Sales";
    List<TableTransactionAction> tableTransactionActions = new ArrayList<>();
    
    Map<String, Object> personalInfo1 = new HashMap<>();
    personalInfo1.put("FirstName", "Jeff");
    personalInfo1.put("LastName", "Smith");
    personalInfo1.put("Email", "Jeff@contoso.com");
    personalInfo1.put("PhoneNumber", "425-555-0104");
    
    // Create an entity to add to the table.
    tableTransactionActions.add(new TableTransactionAction(
        TableTransactionActionType.UPSERT_MERGE,
        new TableEntity(partitionKey, "0001")
            .setProperties(personalInfo1)
    ));
    
    Map<String, Object> personalInfo2 = new HashMap<>();
    personalInfo2.put("FirstName", "Ben");
    personalInfo2.put("LastName", "Johnson");
    personalInfo2.put("Email", "Ben@contoso.com");
    personalInfo2.put("PhoneNumber", "425-555-0102");
    
    // Create another entity to add to the table.
    tableTransactionActions.add(new TableTransactionAction(
        TableTransactionActionType.UPSERT_MERGE,
        new TableEntity(partitionKey, "0002")
            .setProperties(personalInfo2)
    ));
    
    Map<String, Object> personalInfo3 = new HashMap<>();
    personalInfo3.put("FirstName", "Denise");
    personalInfo3.put("LastName", "Rivers");
    personalInfo3.put("Email", "Denise@contoso.com");
    personalInfo3.put("PhoneNumber", "425-555-0103");
    
    // Create a third entity to add to the table.
    tableTransactionActions.add(new TableTransactionAction(
        TableTransactionActionType.UPSERT_MERGE,
        new TableEntity(partitionKey, "0003")
            .setProperties(personalInfo3)
    ));

    // Submit transaction on the "Employees" table.
    tableClient.submitTransaction(tableTransactionActions);
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

Quelques remarques sur les opérations par lots :

* Vous pouvez effectuer jusqu'à 100 opérations d'insertion, de suppression, de fusion, de remplacement, d'insertion ou fusion et d'insertion ou de remplacement dans n'importe quelle combinaison en un seul lot.
* Une opération par lot peut comporter une opération d’extraction, s’il s’agit de la seule opération du lot.
* Toutes les entités d'une opération par lot doivent avoir la même clé de partition.
* Une opération par lot est limitée à une charge utile de données de 4 Mo.

## <a name="retrieve-all-entities-in-a-partition"></a>Extraction de toutes les entités d'une partition

Pour interroger une table afin d’obtenir les entités d’une partition, utilisez une requête `ListEntitiesOptions`. Appelez `ListEntitiesOptions.setFilter` pour créer une requête sur une table donnée qui renvoie un type de résultat spécifique. Le code suivant indique un filtre pour les entités où `Sales` est la clé de partition. Quand la requête est exécutée avec un appel de `listEntities` sur l’objet `TableClient`, elle retourne un `Iterator` de `TableEntity`. Vous pouvez ensuite utiliser l’`Iterator` renvoyé dans une boucle « ForEach » pour traiter les résultats. Ce code imprime les champs de chaque entité dans les résultats de requête vers la console.

```java
try
{
    // Define constants for filters.
    final String PARTITION_KEY = "PartitionKey";
    final String tableName = "Employees";

    // Create a TableClient with a connection string and a table name.
    TableClient tableClient = new TableClientBuilder()
        .connectionString(connectionString)
        .tableName(tableName)
        .buildClient();

    // Create a filter condition where the partition key is "Sales".
    ListEntitiesOptions options = new ListEntitiesOptions().setFilter(PARTITION_KEY + " eq 'Sales'");

    // Loop through the results, displaying information about the entities.
    tableClient.listEntities(options, null, null).forEach(tableEntity -> {
        System.out.println(tableEntity.getPartitionKey() +
            " " + tableEntity.getRowKey() +
            "\t" + tableEntity.getProperty("FirstName") +
            "\t" + tableEntity.getProperty("LastName") +
            "\t" + tableEntity.getProperty("Email") +
            "\t" + tableEntity.getProperty("PhoneNumber"));
    });
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="retrieve-a-range-of-entities-in-a-partition"></a>Extraction d’un ensemble d’entités dans une partition

Si vous ne voulez pas interroger toutes les entités d’une partition, définissez une plage en utilisant les opérateurs de comparaison dans un filtre. Le code suivant combine deux filtres pour obtenir toutes les entités de la partition `Sales` avec une clé de ligne comprise entre « 0001 » et « 0004 ». Si vous utilisez les entités ajoutées à la table de la section de ce guide consacrée à l'insertion de lot, seulement deux entités sont renvoyées (Ben et Denise Smith). Si vous utilisez les entités ajoutées à la table dans la section d’insertion de lot de ce guide, seules deux entités sont retournées cette fois-ci (Ben et Denise).

```java
try
{
    // Define constants for filters.
    final String PARTITION_KEY = "PartitionKey";
    final String ROW_KEY = "RowKey";
    final String tableName = "Employees";

    // Create a TableServiceClient with a connection string.
    // Create a TableClient with a connection string and a table name.
    TableClient tableClient = new TableClientBuilder()
        .connectionString(connectionString)
        .tableName(tableName)
        .buildClient();

    // Create a filter condition where the partition key is "Sales".
    ListEntitiesOptions options = new ListEntitiesOptions().setFilter(PARTITION_KEY + " eq 'Sales' AND " + ROW_KEY + " lt '0004' AND ROW_KEY + " gt '0001'");
    
    // Loop through the results, displaying information about the entities.
    tableClient.listEntities(options, null, null).forEach(tableEntity -> {
        System.out.println(tableEntity.getPartitionKey() +
            " " + tableEntity.getRowKey() +
            "\t" + tableEntity.getProperty("FirstName") +
            "\t" + tableEntity.getProperty("LastName") +
            "\t" + tableEntity.getProperty("Email") +
            "\t" + tableEntity.getProperty("PhoneNumber"));
    });
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="retrieve-a-single-entity"></a>Extraction d'une seule entité

Vous pouvez écrire une requête pour extraire une seule entité. Le code suivant appelle `TableClient.getEntity` avec des clés de partition et de ligne pour récupérer l’entité de l’employé « Jeff Smith », ce qui évite de créer une requête `ListEntitiesOptions` et d’utiliser des filtres pour parvenir au même résultat. Lors de son exécution, l'opération d'extraction renvoie une seule entité, plutôt que plusieurs. La valeur `null` est retournée si aucune entité n’a de correspondance exacte avec la clé de partition et de ligne. La méthode la plus rapide pour extraire une seule entité dans le service de Table consiste à spécifier une clé de partition et une clé de ligne.

```java
try
{
    final String tableName = "Employees";

    // Create a TableClient with a connection string and a table name.
    TableClient tableClient = new TableClientBuilder()
        .connectionString(connectionString)
        .tableName(tableName)
        .buildClient();

    // Get the specific entity.
    TableEntity specificEntity = tableClient.getEntity("Sales", "0001");

    // Output the entity.
    if (specificEntity != null)
    {
        System.out.println(specificEntity.getPartitionKey() +
            " " + specificEntity.getRowKey() +
            "\t" + specificEntity.getProperty("FirstName") +
            "\t" + specificEntity.getProperty("LastName"));
            "\t" + specificEntity.getProperty("Email") +
            "\t" + specificEntity.getProperty("PhoneNumber"));
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="modify-an-entity"></a>Modifier une entité

Pour modifier une entité, extrayez-la dans le service de Table, apportez les modifications souhaitées à l'objet d'entité, puis enregistrez les modifications sur le service de Table via une opération de remplacement ou de fusion. Le code suivant modifie le numéro de téléphone d'un client existant. Au lieu d’appeler `tableClient.upsertEntity` comme nous l’avons fait pour l’insertion, ce code appelle `tableClient.updateEntity`.

```java
try
{
    final String tableName = "Employees";

    // Create a TableClient with a connection string and a table name.
    TableClient tableClient = new TableClientBuilder()
        .connectionString(connectionString)
        .tableName(tableName)
        .buildClient();

    // Get the specific entity.
    TableEntity specificEntity = tableClient.getEntity("Sales", "0001");

    // Specify a new phone number
    specificEntity.getProperties().put("PhoneNumber", "425-555-0105");

    // Update the specific entity
    tableClient.updateEntity(specificEntity, TableEntityUpdateMode.REPLACE);
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="query-a-subset-of-entity-properties"></a>Interrogation d'un sous-ensemble de propriétés d'entité

Vous pouvez utiliser une requête de table pour extraire uniquement quelques propriétés d’une entité. Cette technique, nommée « projection », réduit la consommation de bande passante et peut améliorer les performances des requêtes, notamment pour les entités volumineuses. La requête contenue dans le code suivant utilise la méthode `ListEntitiesOptions.setSelect` pour retourner uniquement les adresses e-mail des entités dans la table.

```java
try
{
    final String tableName = "Employees";

    // Create a TableClient with a connection string and a table name.
    TableClient tableClient = new TableClientBuilder()
        .connectionString(connectionString)
        .tableName(tableName)
        .buildClient();

    // Create a filter condition that retrieves only the Email property.
    List<String> attributesToRetrieve = new ArrayList<>();
    attributesToRetrieve.add("Email");
    
    ListEntitiesOptions options = new ListEntitiesOptions().setSelect(attributesToRetrieve);

    // Loop through the results, displaying the Email values.
    tableClient.listEntities(options, null, null).forEach(tableEntity -> {
        System.out.println(tableEntity.getProperty("Email"));
    });
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="insert-or-replace-an-entity"></a>Insérer ou remplacer une entité

Il arrive souvent de vouloir ajouter une entité à une table sans savoir si elle existe dans la table. Une opération d’insertion ou de remplacement vous permet d’effectuer une demande unique. Cette demande insère l’entité si elle n’existe pas ou remplace l’entité existante. À partir des exemples précédents, le code suivant insère ou remplace l'entité « Walter Harp ». Après la création d’une entité, ce code appelle la méthode `TableClient.upsertEntity`.

```java
try
{
    final String tableName = "Employees";

    // Create a TableClient with a connection string and a table name.
    TableClient tableClient = new TableClientBuilder()
        .connectionString(connectionString)
        .tableName(tableName)
        .buildClient();

    // Create a new table entity.
    Map<String, Object> properties = new HashMap<>();
    properties.put("FirstName", "Walter");
    properties.put("LastName", "Harp");
    properties.put("Email", "Walter@contoso.com");
    properties.put("PhoneNumber", "425-555-0101");
        
    TableEntity newEmployee = new TableEntity("Sales", "0004")
        .setProperties(properties);
        
    // Add the new customer to the Employees table.
    tableClient.upsertEntity(newEmployee);
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="delete-an-entity"></a>Suppression d’une entité

Vous pouvez supprimer une entité en fournissant sa clé de partition et sa clé de ligne via `TableClient.deleteEntity`.

```java
try
{
    final String tableName = "Employees";

    // Create a TableClient with a connection string and a table name.
    TableClient tableClient = new TableClientBuilder()
        .connectionString(connectionString)
        .tableName(tableName)
        .buildClient();

    Delete the entity for partition key 'Sales' and row key '0001' from the table.
    tableClient.deleteEntity("Sales", "0001");
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="delete-a-table"></a>Suppression d’une table

Enfin, le code suivant supprime une table d’un compte. Vous devez patienter environ 40 secondes avant de pouvoir recréer une table après l’avoir supprimée.

```java
try
{
    final String tableName = "Employees";

    // Create a TableClient with a connection string and a table name.
    TableClient tableClient = new TableClientBuilder()
        .connectionString(connectionString)
        .tableName(tableName)
        .buildClient();

    // Delete the table and all its data.
    tableClient.deleteTable();
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

[!INCLUDE [storage-check-out-samples-java](../../../includes/storage-check-out-samples-java.md)]

## <a name="next-steps"></a>Étapes suivantes

* [Getting Started with Azure Table Service in Java (Prise en main du service de Table Azure en Java)](https://github.com/Azure-Samples/storage-table-java-getting-started)
* [Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md) est une application autonome et gratuite de Microsoft qui vous permet d’exploiter visuellement les données de Stockage Azure sur Windows, macOS et Linux.
* [Bibliothèque de client Tables Azure pour Java][Azure Tables client library for Java]
* [Informations de référence sur la bibliothèque de client Tables Azure][Azure Tables client library reference documentation]
* [API REST Tables Azure][Azure Tables REST API]
* [Blog de l’équipe Tables Azure][Azure Tables Team Blog]

Pour plus d’informations, consultez [Azure pour les développeurs Java](/java/azure).

[Azure SDK for Java]: https://go.microsoft.com/fwlink/?LinkID=525671
[Azure Tables client library for Java]: https://github.com/Azure/azure-sdk-for-java/tree/main/sdk/tables/azure-data-tables
[Azure Tables client library reference documentation]: https://azure.github.io/azure-sdk-for-java/tables.html
[Azure Tables REST API]: /azure/storage/tables/table-storage-overview
[Azure Tables Team Blog]: https://blogs.msdn.microsoft.com/windowsazurestorage/
