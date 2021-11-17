---
title: 'Démarrage rapide : API Table avec .NET - Azure Cosmos DB'
description: Ce guide de démarrage rapide montre comment accéder à l’API Table Azure Cosmos DB à partir d’une application .NET en utilisant le kit SDK Azure.Data.Tables
author: DavidCBerry13
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: csharp
ms.topic: quickstart
ms.date: 09/26/2021
ms.author: daberry
ms.custom: devx-track-csharp
ms.openlocfilehash: e76341f8d3d0e7b7c67be6a19ab3fb8c5b902794
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132324172"
---
# <a name="quickstart-build-a-table-api-app-with-net-sdk-and-azure-cosmos-db"></a>Démarrage rapide : Créer une application d’API Table avec le SDK .NET et Azure Cosmos DB

[!INCLUDE[appliesto-table-api](../includes/appliesto-table-api.md)]

Ce guide de démarrage rapide montre comment accéder à l’[API Table](introduction.md) Azure Cosmos DB à partir d’une application .NET.  L’API Table Cosmos DB est un magasin de données sans schéma qui permet aux applications de stocker des données structurées NoSQL dans le cloud.  Étant donné que les données sont stockées dans une conception sans schéma, de nouvelles propriétés (colonnes) sont automatiquement ajoutées à la table lorsqu’un objet doté d’un nouvel attribut est ajouté à la table.

Les applications .NET peuvent accéder à l’API Table Cosmos DB à l’aide du package NuGet [Azure.Data.Tables](https://www.nuget.org/packages/Azure.Data.Tables/).  Le package [Azure.Data.Tables](https://www.nuget.org/packages/Azure.Data.Tables/) est une bibliothèque [.NET Standard 2.0](/dotnet/standard/net-standard) qui fonctionne avec les applications .NET Framework (4.7.2 et versions ultérieures) et .NET Core (2.0 et versions ultérieures).

## <a name="prerequisites"></a>Prérequis

L’exemple d’application est écrit dans [.NET Core 3.1](https://dotnet.microsoft.com/download/dotnet/3.1), bien que les principes s’appliquent à la fois aux applications .NET Framework et .NET Core.  Vous pouvez utiliser [Visual Studio](https://www.visualstudio.com/downloads/), [Visual Studio pour Mac](https://visualstudio.microsoft.com/vs/mac/) ou [Visual Studio Code](https://code.visualstudio.com/) comme environnement de développement intégré (IDE).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note-dotnet.md)]

## <a name="sample-application"></a>Exemple d’application

L’exemple d’application de ce tutoriel peut être cloné ou téléchargé à partir du dépôt [https://github.com/Azure-Samples/msdocs-azure-data-tables-sdk-dotnet](https://github.com/Azure-Samples/msdocs-azure-data-tables-sdk-dotnet).  Une application de démarrage et une application terminée sont incluses dans l’exemple de dépôt.

```bash
git clone https://github.com/Azure-Samples/msdocs-azure-data-tables-sdk-dotnet
```

L’exemple d’application utilise des données météorologiques comme modèle pour illustrer les fonctionnalités de l’API Table. Les objets représentant les observations météorologiques sont stockés et récupérés à l’aide de l’API Table, entre autres le stockage d’objets avec des propriétés supplémentaires pour mettre en évidence les fonctionnalités sans schéma de l’API Table.

:::image type="content" source="./media/create-table-dotnet/table-api-app-finished-application-720px.png" alt-text="Capture d’écran de l’application terminée affichant des données stockées dans une table Cosmos DB à l’aide de l’API Table." lightbox="./media/create-table-dotnet/table-api-app-finished-application.png":::

## <a name="1---create-an-azure-cosmos-db-account"></a>1 – Créer un compte Azure Cosmos DB

Vous devez d’abord créer un compte API Tables Cosmos DB qui contiendra la ou les tables utilisées dans votre application.  Pour cela, utilisez le portail Azure, Azure CLI ou Azure PowerShell.

### <a name="azure-portal"></a>[Azure portal](#tab/azure-portal)

Connectez-vous au [portail Azure](https://portal.azure.com/) et suivez ces étapes pour créer un compte Cosmos DB.

| Instructions    | Capture d'écran |
|:----------------|-----------:|
| [!INCLUDE [Create cosmos db account step 1](./includes/create-table-dotnet/create-cosmos-db-acct-1.md)] | :::image type="content" source="./media/create-table-dotnet/azure-portal-create-cosmos-db-account-table-api-1-240px.png" alt-text="Capture d’écran montrant comment utiliser la zone de recherche dans la barre d’outils supérieure pour trouver des comptes Cosmos DB dans Azure." lightbox="./media/create-table-dotnet/azure-portal-create-cosmos-db-account-table-api-1.png":::           |
| [!INCLUDE [Create cosmos db account step 1](./includes/create-table-dotnet/create-cosmos-db-acct-2.md)] | :::image type="content" source="./media/create-table-dotnet/azure-portal-create-cosmos-db-account-table-api-2-240px.png" alt-text="Capture d’écran montrant l’emplacement du bouton Créer sur la page des comptes Cosmos DB dans Azure." lightbox="./media/create-table-dotnet/azure-portal-create-cosmos-db-account-table-api-2.png":::           |
| [!INCLUDE [Create cosmos db account step 1](./includes/create-table-dotnet/create-cosmos-db-acct-3.md)] | :::image type="content" source="./media/create-table-dotnet/azure-portal-create-cosmos-db-account-table-api-3-240px.png" alt-text="Capture d’écran montrant l’option Azure Table comme option appropriée à sélectionner." lightbox="./media/create-table-dotnet/azure-portal-create-cosmos-db-account-table-api-3.png":::           |
| [!INCLUDE [Create cosmos db account step 1](./includes/create-table-dotnet/create-cosmos-db-acct-4.md)] | :::image type="content" source="./media/create-table-dotnet/azure-portal-create-cosmos-db-account-table-api-4-240px.png" alt-text="Capture d’écran montrant comment renseigner les champs de la page de création d’un compte Cosmos DB." lightbox="./media/create-table-dotnet/azure-portal-create-cosmos-db-account-table-api-4.png":::           |

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Les comptes Cosmos DB sont créés à l’aide de la commande [az cosmosdb create](/cli/azure/cosmosdb#az_cosmosdb_create). Vous devez inclure l’option `--capabilities EnableTable` permettant d’activer le stockage de tables dans votre instance Cosmos DB.  Comme toutes les ressources Azure doivent être contenues dans un groupe de ressources, l’extrait de code suivant crée également un groupe de ressources pour le compte Cosmos DB.

Les noms de compte Cosmos DB doivent être compris entre 3 et 44 caractères, et ne contenir que des lettres minuscules, des chiffres et le caractère de trait d’union (-).  Les noms de compte Cosmos DB doivent également être uniques dans Azure.

Les commandes Azure CLI peuvent être exécutées dans [Azure Cloud Shell](https://shell.azure.com) ou sur une station de travail dans laquelle l’interface [Azure CLI est installée](/cli/azure/install-azure-cli).

En règle générale, le processus de création de compte Cosmos DB s’exécute en quelques minutes.

```azurecli
LOCATION='eastus'
RESOURCE_GROUP_NAME='rg-msdocs-tables-sdk-demo'
COSMOS_ACCOUNT_NAME='cosmos-msdocs-tables-sdk-demo-123'    # change 123 to a unique set of characters for a unique name
COSMOS_TABLE_NAME='WeatherData'

az group create \
    --location $LOCATION \
    --name $RESOURCE_GROUP_NAME

az cosmosdb create \
    --name $COSMOS_ACCOUNT_NAME \
    --resource-group $RESOURCE_GROUP_NAME \
    --capabilities EnableTable
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Les comptes Azure Cosmos DB sont créés à l’aide de l’applet de commande [New-AzCosmosDBAccount](/powershell/module/az.cosmosdb/new-azcosmosdbaccount). Vous devez inclure l’option `-ApiKind "Table"` permettant d’activer le stockage de tables dans votre instance Cosmos DB.  Comme toutes les ressources Azure doivent être contenues dans un groupe de ressources, l’extrait de code suivant crée également un groupe de ressources pour le compte Azure Cosmos DB.

Les noms de compte Azure Cosmos DB doivent être compris entre 3 et 44 caractères, et ne contenir que des lettres minuscules, des chiffres et le caractère de trait d’union (-).  Les noms de compte Azure Cosmos DB doivent également être uniques dans Azure.

Les commandes Azure PowerShell peuvent être exécutées dans [Azure Cloud Shell](https://shell.azure.com) ou sur une station de travail dans laquelle [Azure PowerShell est installé](/powershell/azure/install-az-ps).

En règle générale, le processus de création de compte Cosmos DB s’exécute en quelques minutes.

```azurepowershell
$location = 'eastus'
$resourceGroupName = 'rg-msdocs-tables-sdk-demo'
$cosmosAccountName = 'cosmos-msdocs-tables-sdk-demo-123'  # change 123 to a unique set of characters for a unique name

# Create a resource group
New-AzResourceGroup `
    -Location $location `
    -Name $resourceGroupName

# Create an Azure Cosmos DB 
New-AzCosmosDBAccount `
    -Name $cosmosAccountName `
    -ResourceGroupName $resourceGroupName `
    -Location $location `
    -ApiKind "Table"
```

---

## <a name="2---create-a-table"></a>2 – Créer une table

Ensuite, vous devez créer une table dans votre compte Cosmos DB, celle-ci servira à votre application.  Contrairement à une base de données classique, il vous suffit de spécifier le nom de la table, et non les propriétés (colonnes) de la table.  À mesure que les données sont chargées dans votre table, les propriétés (colonnes) sont automatiquement créées en fonction des besoins.

### <a name="azure-portal"></a>[Azure portal](#tab/azure-portal)

Dans le [portail Azure](https://portal.azure.com/), effectuez les étapes suivantes pour créer une table dans votre compte Cosmos DB.

| Instructions    | Capture d'écran |
|:----------------|-----------:|
| [!INCLUDE [Create cosmos db table step 1](./includes/create-table-dotnet/create-cosmos-table-1.md)] | :::image type="content" source="./media/create-table-dotnet/azure-portal-create-cosmos-db-table-api-1-240px.png" alt-text="Capture d’écran montrant comment utiliser la zone de recherche dans la barre d’outils supérieure pour trouver vos comptes Cosmos DB." lightbox="./media/create-table-dotnet/azure-portal-create-cosmos-db-table-api-1.png":::           |
| [!INCLUDE [Create cosmos db table step 2](./includes/create-table-dotnet/create-cosmos-table-2.md)] | :::image type="content" source="./media/create-table-dotnet/azure-portal-create-cosmos-db-table-api-2-240px.png" alt-text="Capture d’écran montrant l’emplacement du bouton Ajouter une table." lightbox="./media/create-table-dotnet/azure-portal-create-cosmos-db-table-api-2.png":::           |
| [!INCLUDE [Create cosmos db table step 3](./includes/create-table-dotnet/create-cosmos-table-3.md)] | :::image type="content" source="./media/create-table-dotnet/azure-portal-create-cosmos-db-table-api-3-240px.png" alt-text="Capture d’écran montrant comment renseigner la boîte de dialogue Nouvelle table pour une table Cosmos DB." lightbox="./media/create-table-dotnet/azure-portal-create-cosmos-db-table-api-3.png":::           |

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Les tables dans Cosmos DB sont créées à l’aide de la commande [az cosmosdb table create](/cli/azure/cosmosdb/table#az_cosmosdb_table_create).

```azurecli
COSMOS_TABLE_NAME='WeatherData'

az cosmosdb table create \
    --account-name $COSMOS_ACCOUNT_NAME \
    --resource-group $RESOURCE_GROUP_NAME \
    --name $COSMOS_TABLE_NAME \
    --throughput 400
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Les tables dans Cosmos DB sont créées à l’aide de la commande [New-AzCosmosDBTable](/powershell/module/az.cosmosdb/new-azcosmosdbtable).

```azurepowershell
$cosmosTableName = 'WeatherData'

# Create the table for the application to use
New-AzCosmosDBTable `
    -Name $cosmosTableName `
    -AccountName $cosmosAccountName `
    -ResourceGroupName $resourceGroupName
```

---

## <a name="3---get-cosmos-db-connection-string"></a>3 – Obtenir la chaîne de connexion Cosmos DB

Pour accéder à une de vos tables dans Cosmos DB, votre application a besoin de la chaîne de connexion de la table pour le compte de stockage CosmosDB.  La chaîne de connexion peut être récupérée à l’aide du portail Azure, de l’interface Azure CLI ou d’Azure PowerShell.

### <a name="azure-portal"></a>[Azure portal](#tab/azure-portal)

| Instructions    | Capture d'écran |
|:----------------|-----------:|
| [!INCLUDE [Get cosmos db table connection string step 1](./includes/create-table-dotnet/get-cosmos-connection-string-1.md)] | :::image type="content" source="./media/create-table-dotnet/azure-portal-cosmos-db-table-connection-string-1-240px.png" alt-text="Capture d’écran montrant l’emplacement du lien des chaînes de connexion sur la page Cosmos DB." lightbox="./media/create-table-dotnet/azure-portal-cosmos-db-table-connection-string-1.png":::           |
| [!INCLUDE [Get cosmos db table connection string step 2](./includes/create-table-dotnet/get-cosmos-connection-string-2.md)] | :::image type="content" source="./media/create-table-dotnet/azure-portal-cosmos-db-table-connection-string-2-240px.png" alt-text="Capture d’écran montrant la chaîne de connexion à sélectionner et à utiliser dans votre application." lightbox="./media/create-table-dotnet/azure-portal-cosmos-db-table-connection-string-2.png":::           |

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pour récupérer la chaîne de connexion du stockage de la table primaire par le biais de l’interface Azure CLI, utilisez la commande [az cosmosdb keys list](/cli/azure/cosmosdb/keys#az_cosmosdb_keys_list) avec l’option `--type connection-strings`.  Cette commande se sert d’une [requête JMESPath](https://jmespath.org/) pour afficher uniquement la chaîne de connexion de la table primaire.

```azurecli
# This gets the primary Table connection string
az cosmosdb keys list \
    --type connection-strings \
    --resource-group $RESOURCE_GROUP_NAME \
    --name $COSMOS_ACCOUNT_NAME \
    --query "connectionStrings[?description=='Primary Table Connection String'].connectionString" \
    --output tsv
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Pour obtenir la chaîne de connexion du stockage de la table primaire avec Azure PowerShell, utilisez l’applet de commande [Get-AzCosmosDBAccountKey](/powershell/module/az.cosmosdb/get-azcosmosdbaccountkey).

```azurepowershell
# This gets the primary Table connection string  
 $(Get-AzCosmosDBAccountKey `
    -ResourceGroupName $resourceGroupName `
    -Name $cosmosAccountName `
    -Type "ConnectionStrings")."Primary Table Connection String"
```

---

La chaîne de connexion de votre compte Cosmos DB est considérée comme secret d’application, et doit être protégée comme tout autre secret ou mot de passe d’application.  Cet exemple utilise l’[outil Secret Manager](/aspnet/core/security/app-secrets#secret-manager) pour stocker la chaîne de connexion pendant le développement et la mettre à disposition de l’application.  Vous pouvez accéder à l’outil Secret Manager à partir de Visual Studio ou de l’interface CLI .NET.

### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Pour ouvrir l’outil Secret Manager à partir de Visual Studio, cliquez avec le bouton droit sur le projet et sélectionnez **Gérer les données secrètes de l’utilisateur** dans le menu contextuel.  Le fichier *secrets.json* s’ouvre pour le projet.  Remplacez le contenu du fichier par le code JSON ci-dessous, en substituant la chaîne de connexion de votre table Cosmos DB.

```json
{
  "ConnectionStrings": {
    "CosmosTableApi": "<cosmos db table connection string>"
  }  
}
```

### <a name="net-cli"></a>[CLI .NET](#tab/netcore-cli)

Pour utiliser l’outil Secret Manager, vous devez d’abord l’initialiser pour votre projet à l’aide de la commande `dotnet user-secrets init`.

```dotnetcli
dotnet user-secrets init
```

Ensuite, utilisez la commande `dotnet user-secrets set` pour ajouter la chaîne de connexion de la table Cosmos DB en tant que secret.

```dotnetcli
dotnet user-secrets set "ConnectionStrings:CosmosTableApi" "<cosmos db table connection string>"
```

---

## <a name="4---install-azuredatatables-nuget-package"></a>4 – Installer le package NuGet Azure.Data.Tables

Pour accéder à l’API Table Cosmos DB à partir d’une application .NET, installez le package NuGet [Azure.Data.Tables](https://www.nuget.org/packages/Azure.Data.Tables).

### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

```PowerShell
Install-Package Azure.Data.Tables
```

### <a name="net-cli"></a>[CLI .NET](#tab/netcore-cli)

```dotnetcli
dotnet add package Azure.Data.Tables
```

---

## <a name="5---configure-the-table-client-in-startupcs"></a>5 – Configurer le client Table dans Startup.cs

Le kit SDK Azure communique avec Azure à l’aide d’objets de client pour exécuter différentes opérations sur Azure.  L’objet [TableClient](/dotnet/api/azure.data.tables.tableclient) est l’objet utilisé pour communiquer avec l’API Table Cosmos DB.

En général, une application crée un seul objet [TableClient](/dotnet/api/azure.data.tables.tableclient) par table à utiliser dans toute l’application.  Il est recommandé d’utiliser l’injection de dépendances et d’inscrire l’objet [TableClient](/dotnet/api/azure.data.tables.tableclient) comme singleton pour effectuer cette opération.  Pour plus d’informations sur l’utilisation de l’injection de dépendances à l’aide du kit SDK Azure, consultez [Injection de dépendances avec le kit SDK Azure pour .NET](/dotnet/azure/sdk/dependency-injection).

Dans le fichier `Startup.cs` de l’application, modifiez la méthode ConfigureServices() pour qu’elle corresponde à l’extrait de code suivant :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddRazorPages()
        .AddMvcOptions(options =>
        {
            options.Filters.Add(new ValidationFilter());
        });
    
    var connectionString = Configuration.GetConnectionString("CosmosTableApi");
    services.AddSingleton<TableClient>(new TableClient(connectionString, "WeatherData"));
    
    services.AddSingleton<TablesService>();
}
```

Vous devez également ajouter l’instruction suivante en haut du fichier Startup.cs.

```csharp
using Azure.Data.Tables;
```

## <a name="6---implement-cosmos-db-table-operations"></a>6 – Mettre en œuvre les opérations de table Cosmos DB

Toutes les opérations de table Cosmos DB pour l’exemple d’application sont implémentées dans la classe `TableService` située dans le répertoire *Services*.  Vous devez importer les espaces de noms `Azure` et `Azure.Data.Tables` en haut de ce fichier pour utiliser des objets dans le package du kit SDK `Azure.Data.Tables`.

```csharp
using Azure;
using Azure.Data.Tables;
```

Au début de la classe `TableService`, ajoutez une variable membre pour l’objet [TableClient](/dotnet/api/azure.data.tables.tableclient), et un constructeur pour permettre l’injection de l’objet [TableClient](/dotnet/api/azure.data.tables.tableclient) dans la classe.

```csharp
private TableClient _tableClient;

public TablesService(TableClient tableClient)
{
    _tableClient = tableClient;
}
```

### <a name="get-rows-from-a-table"></a>Obtenir les lignes d’une table

La classe [TableClient](/dotnet/api/azure.data.tables.tableclient) contient une méthode nommée [Query](/dotnet/api/azure.data.tables.tableclient.query) qui vous permet de sélectionner des lignes de la table.  Dans cet exemple, étant donné qu’aucun paramètre n’est passé à la méthode, toutes les lignes sont sélectionnées dans la table.

La méthode accepte également un paramètre générique de type [ITableEntity](/dotnet/api/azure.data.tables.itableentity) qui spécifie que les données de la classe de modèle seront retournées en tant que telles. En l’occurrence, la classe intégrée [TableEntity](/dotnet/api/azure.data.tables.itableentity) est utilisée, ce qui signifie que la méthode `Query` retournera une collection `Pageable<TableEntity>` comme résultats.

```csharp
public IEnumerable<WeatherDataModel> GetAllRows()
{
    Pageable<TableEntity> entities = _tableClient.Query<TableEntity>();

    return entities.Select(e => MapTableEntityToWeatherDataModel(e));
}
```

La classe [TableEntity](/dotnet/api/azure.data.tables.itableentity) définie dans le package `Azure.Data.Tables` dispose de propriétés pour la valeur de la clé de partition et celle de la clé de ligne dans la table.  Ensemble, ces deux valeurs représentent une clé unique pour la ligne de la table.  Dans cet exemple d’application, le nom de la station météo (ville) est stocké dans la clé de partition, et la date/l’heure de l’observation l’est dans la clé de ligne.  Toutes les autres propriétés (température, humidité, vitesse du vent) sont stockées dans un dictionnaire, dans l’objet `TableEntity`.

Il est courant de mapper un objet [TableEntity](/dotnet/api/azure.data.tables.tableentity) à un objet de votre propre définition.  L’exemple d’application définit une classe `WeatherDataModel` dans le répertoire *Models* à cet effet.  Cette classe possède des propriétés pour le nom de la station et pour la date d’observation auxquels la clé de partition et la clé de ligne sont mappés, fournissant ainsi des noms de propriétés plus significatifs pour ces valeurs.  Elle utilise ensuite un dictionnaire pour stocker toutes les autres propriétés sur l’objet.  Il s’agit d’un schéma courant lors de l’utilisation du stockage de tables, car une ligne peut comporter un certain nombre de propriétés arbitraires, et nous voulons que nos objets de modèle puissent les capturer tous.  Cette classe contient également des méthodes permettant de lister les propriétés de la classe.

```csharp
public class WeatherDataModel 
{
    // Captures all of the weather data properties -- temp, humidity, wind speed, etc
    private Dictionary<string, object> _properties = new Dictionary<string, object>();

    public string StationName { get; set; }

    public string ObservationDate { get; set; }

    public DateTimeOffset? Timestamp { get; set; }

    public string Etag { get; set; }

    public object this[string name] 
    { 
        get => ( ContainsProperty(name)) ? _properties[name] : null; 
        set => _properties[name] = value; 
    }
    
    public ICollection<string> PropertyNames => _properties.Keys;

    public int PropertyCount => _properties.Count;

    public bool ContainsProperty(string name) => _properties.ContainsKey(name);       
}
```

La méthode `MapTableEntityToWeatherDataModel` est utilisée pour mapper un objet [TableEntity](/dotnet/api/azure.data.tables.itableentity) à un objet `WeatherDataModel`.  L’objet [TableEntity](/dotnet/api/azure.data.tables.itableentity) contient une propriété [Keys](/dotnet/api/azure.data.tables.tableentity.keys) permettant d’obtenir tous les noms de propriétés contenus dans la table pour l’objet (en fait, les noms des colonnes pour cette ligne de la table).  La méthode `MapTableEntityToWeatherDataModel` mappe directement les propriétés `PartitionKey`, `RowKey`, `Timestamp` et `Etag`, puis utilise la propriété `Keys` pour itérer sur les autres propriétés de l’objet `TableEntity` et les mapper à l’objet `WeatherDataModel`, sans les propriétés qui ont déjà été mappées directement.

Modifiez le code dans la méthode `MapTableEntityToWeatherDataModel` pour qu’il corresponde au bloc de code suivant.

```csharp
public WeatherDataModel MapTableEntityToWeatherDataModel(TableEntity entity)
{
    WeatherDataModel observation = new WeatherDataModel();
    observation.StationName = entity.PartitionKey;
    observation.ObservationDate = entity.RowKey;
    observation.Timestamp = entity.Timestamp;
    observation.Etag = entity.ETag.ToString();

    var measurements = entity.Keys.Where(key => !EXCLUDE_TABLE_ENTITY_KEYS.Contains(key));
    foreach (var key in measurements)
    {
        observation[key] = entity[key];
    }
    return observation;            
}
```

### <a name="filter-rows-returned-from-a-table"></a>Filtrer les lignes retournées à partir d’une table

Pour filtrer les lignes retournées à partir d’une table, vous pouvez transmettre une chaîne de filtre de style OData à la méthode [Query](/dotnet/api/azure.data.tables.tableclient.query). Par exemple, si vous souhaitez obtenir tous les relevés météorologiques pour Chicago entre le 1er juillet 2021 et le 2 juillet 2021 (inclus), vous devez passer la chaîne de filtre suivante.

```odata
PartitionKey eq 'Chicago' and RowKey ge '2021-07-01 12:00 AM' and RowKey le '2021-07-02 12:00 AM'
```

Vous pouvez afficher tous les opérateurs de filtre OData sur le site web OData à la section [Filter System Query Option](https://www.odata.org/documentation/odata-version-2-0/uri-conventions/) (Filtrer dans l’option de requête système).

Dans l’exemple d’application, l’objet `FilterResultsInputModel` est conçu pour capturer tous les critères de filtre fournis par l’utilisateur.

```csharp
public class FilterResultsInputModel : IValidatableObject
{
    public string PartitionKey { get; set; }
    public string RowKeyDateStart { get; set; }
    public string RowKeyTimeStart { get; set; }
    public string RowKeyDateEnd { get; set; }
    public string RowKeyTimeEnd { get; set; }
    [Range(-100, +200)]
    public double? MinTemperature { get; set; }
    [Range(-100,200)]
    public double? MaxTemperature { get; set; }
    [Range(0, 300)]
    public double? MinPrecipitation { get; set; }
    [Range(0,300)]
    public double? MaxPrecipitation { get; set; }
}
```

Lorsque cet objet est passé à la méthode `GetFilteredRows` dans la classe `TableService`, il crée une chaîne de filtre pour chaque valeur de propriété qui n’est pas Null.  Il crée ensuite une chaîne de filtre combinée en joignant toutes les valeurs avec une clause « and ».  Cette chaîne de filtre combinée est transmise à la méthode [Query](/dotnet/api/azure.data.tables.tableclient.query) sur l’objet [TableClient](/dotnet/api/azure.data.tables.tableclient), et seules les lignes correspondant à la chaîne de filtre sont retournées.  Vous pouvez utiliser une méthode similaire dans votre code pour élaborer des chaînes de filtre appropriées en fonction des besoins de votre application.

```csharp
public IEnumerable<WeatherDataModel> GetFilteredRows(FilterResultsInputModel inputModel)
{
    List<string> filters = new List<string>();

    if (!String.IsNullOrEmpty(inputModel.PartitionKey))
        filters.Add($"PartitionKey eq '{inputModel.PartitionKey}'");
    if (!String.IsNullOrEmpty(inputModel.RowKeyDateStart) && !String.IsNullOrEmpty(inputModel.RowKeyTimeStart))
        filters.Add($"RowKey ge '{inputModel.RowKeyDateStart} {inputModel.RowKeyTimeStart}'");
    if (!String.IsNullOrEmpty(inputModel.RowKeyDateEnd) && !String.IsNullOrEmpty(inputModel.RowKeyTimeEnd))
        filters.Add($"RowKey le '{inputModel.RowKeyDateEnd} {inputModel.RowKeyTimeEnd}'");
    if (inputModel.MinTemperature.HasValue)
        filters.Add($"Temperature ge {inputModel.MinTemperature.Value}");
    if (inputModel.MaxTemperature.HasValue)
        filters.Add($"Temperature le {inputModel.MaxTemperature.Value}");
    if (inputModel.MinPrecipitation.HasValue)
        filters.Add($"Precipitation ge {inputModel.MinTemperature.Value}");
    if (inputModel.MaxPrecipitation.HasValue)
        filters.Add($"Precipitation le {inputModel.MaxTemperature.Value}");

    string filter = String.Join(" and ", filters);
    Pageable<TableEntity> entities = _tableClient.Query<TableEntity>(filter);

    return entities.Select(e => MapTableEntityToWeatherDataModel(e));
}
```

### <a name="insert-data-using-a-tableentity-object"></a>Insérer les données à l’aide d’un objet TableEntity

Le moyen le plus simple d’ajouter des données à une table consiste à utiliser un objet [TableEntity](/dotnet/api/azure.data.tables.itableentity).  Dans cet exemple, les données sont mappées à partir d’un objet de modèle d’entrée sur un objet [TableEntity](/dotnet/api/azure.data.tables.itableentity).  Les propriétés de l’objet d’entrée représentant le nom de la station météo et la date/l’heure de l’observation sont mappées aux propriétés [PartitionKey](/dotnet/api/azure.data.tables.tableentity.partitionkey) et [RowKey](/dotnet/api/azure.data.tables.tableentity.rowkey) respectivement, formant ensemble une clé unique pour la ligne de la table.  Ensuite, les propriétés supplémentaires sur l’objet de modèle d’entrée sont mappées aux propriétés de dictionnaire sur l’objet TableEntity.  Enfin, la méthode [AddEntity](/dotnet/api/azure.data.tables.tableclient.addentity) sur l’objet [TableClient](/dotnet/api/azure.data.tables.tableclient) est utilisée pour insérer les données dans la table.

Modifiez la classe `InsertTableEntity` de l’exemple d’application pour qu’elle contienne le code suivant.

```csharp
public void InsertTableEntity(WeatherInputModel model)
{
    TableEntity entity = new TableEntity();
    entity.PartitionKey = model.StationName;
    entity.RowKey = $"{model.ObservationDate} {model.ObservationTime}";

    // The other values are added like a items to a dictionary
    entity["Temperature"] = model.Temperature;
    entity["Humidity"] = model.Humidity;
    entity["Barometer"] = model.Barometer;
    entity["WindDirection"] = model.WindDirection;
    entity["WindSpeed"] = model.WindSpeed;
    entity["Precipitation"] = model.Precipitation;

    _tableClient.AddEntity(entity);
}
```

### <a name="upsert-data-using-a-tableentity-object"></a>Faire un upsert des données à l’aide d’un objet TableEntity

Si vous essayez d’insérer une ligne dans une table avec une combinaison clé de partition/clé de ligne qui existe déjà dans cette table, vous recevrez une erreur.  Ainsi, il est souvent préférable d’utiliser [UpsertEntity](/dotnet/api/azure.data.tables.tableclient.upsertentity) plutôt que la méthode AddEntity lors de l’ajout de lignes à une table.  Si la combinaison clé de partition/clé de ligne déterminée existe déjà dans la table, la méthode [UpsertEntity](/dotnet/api/azure.data.tables.tableclient.upsertentity) met à jour la ligne existante.  Dans le cas contraire, la ligne est ajoutée à la table.

```csharp
public void UpsertTableEntity(WeatherInputModel model)
{
    TableEntity entity = new TableEntity();
    entity.PartitionKey = model.StationName;
    entity.RowKey = $"{model.ObservationDate} {model.ObservationTime}";

    // The other values are added like a items to a dictionary
    entity["Temperature"] = model.Temperature;
    entity["Humidity"] = model.Humidity;
    entity["Barometer"] = model.Barometer;
    entity["WindDirection"] = model.WindDirection;
    entity["WindSpeed"] = model.WindSpeed;
    entity["Precipitation"] = model.Precipitation;

    _tableClient.UpsertEntity(entity);
}
```

### <a name="insert-or-upsert-data-with-variable-properties"></a>Insérer ou faire un upsert des données avec des propriétés de variable

Un des avantages de l’utilisation de l’API Table Cosmos DB réside dans le fait que si un objet chargé dans une table contient des propriétés nouvelles, ces propriétés sont automatiquement ajoutées à la table, et les valeurs stockées dans Cosmos DB.  Il n’est pas nécessaire d’exécuter des instructions DDL, telles que ALTER TABLE, pour ajouter des colonnes, comme c’est le cas dans une base de données classique.

Ce modèle offre de la flexibilité à votre application lors de la gestion de sources de données pouvant ajouter ou modifier des éléments nécessaires aux données pour leur capture au fil du temps, ou lorsque plusieurs entrées fournissent différentes données à votre application. Dans l’exemple d’application, nous pouvons simuler une station météorologique qui envoie non seulement les données météorologiques de base, mais également quelques valeurs supplémentaires. Lorsqu’un objet doté de ces nouvelles propriétés est stocké dans la table pour la première fois, les propriétés correspondantes (colonnes) sont automatiquement ajoutées à la table.

Dans l’exemple d’application, la classe `ExpandableWeatherObject` est élaborée autour d’un dictionnaire interne afin de prendre en charge n’importe quel jeu de propriétés sur l’objet.  Cette classe représente un schéma classique lorsqu’un objet doit contenir un ensemble arbitraire de propriétés.

```csharp
public class ExpandableWeatherObject
{
    public Dictionary<string, object> _properties = new Dictionary<string, object>();

    public string StationName { get; set; }

    public string ObservationDate { get; set; }

    public object this[string name]
    {
        get => (ContainsProperty(name)) ? _properties[name] : null;
        set => _properties[name] = value;
    }

    public ICollection<string> PropertyNames => _properties.Keys;

    public int PropertyCount => _properties.Count;

    public bool ContainsProperty(string name) => _properties.ContainsKey(name);
}
```

Pour insérer un tel objet ou en faire un upsert à l’aide de l’API Table, mappez les propriétés de l’objet extensible dans un objet [TableEntity](/dotnet/api/azure.data.tables.tableentity) et utilisez les méthodes [AddEntity](/dotnet/api/azure.data.tables.tableclient.addentity) ou [UpsertEntity](/dotnet/api/azure.data.tables.tableclient.upsertentity) sur l’objet [TableClient](/dotnet/api/azure.data.tables.tableclient), si nécessaire.

```csharp
public void InsertExpandableData(ExpandableWeatherObject weatherObject)
{
    TableEntity entity = new TableEntity();
    entity.PartitionKey = weatherObject.StationName;
    entity.RowKey = weatherObject.ObservationDate;

    foreach (string propertyName in weatherObject.PropertyNames)
    {
        var value = weatherObject[propertyName];
        entity[propertyName] = value;
    }
    _tableClient.AddEntity(entity);
}

        
public void UpsertExpandableData(ExpandableWeatherObject weatherObject)
{
    TableEntity entity = new TableEntity();
    entity.PartitionKey = weatherObject.StationName;
    entity.RowKey = weatherObject.ObservationDate;

    foreach (string propertyName in weatherObject.PropertyNames)
    {
        var value = weatherObject[propertyName];
        entity[propertyName] = value;
    }
    _tableClient.UpsertEntity(entity);
}

```
  
### <a name="update-an-entity"></a>Mise à jour d'une entité

Les entités peuvent être mises à jour en appelant la méthode [UpdateEntity](/dotnet/api/azure.data.tables.tableclient.updateentity) sur l’objet [TableClient](/dotnet/api/azure.data.tables.tableclient).  Étant donné qu’une entité (ligne) stockée à l’aide de l’API Table peut contenir n’importe quel ensemble arbitraire de propriétés, il est souvent utile de créer un objet de mise à jour, basé sur un objet Dictionary similaire à `ExpandableWeatherObject` abordé précédemment.  Dans ce cas, la seule différence est l’ajout d’une propriété `Etag`, qui est utilisée pour le contrôle d’accès concurrentiel lors des mises à jour.

```csharp
public class UpdateWeatherObject
{
    public Dictionary<string, object> _properties = new Dictionary<string, object>();

    public string StationName { get; set; }
    public string ObservationDate { get; set; }
    public string Etag { get; set; }

    public object this[string name]
    {
        get => (ContainsProperty(name)) ? _properties[name] : null;
        set => _properties[name] = value;
    }

    public ICollection<string> PropertyNames => _properties.Keys;

    public int PropertyCount => _properties.Count;

    public bool ContainsProperty(string name) => _properties.ContainsKey(name);
}
```

Dans l’exemple d’application, cet objet est passé à la méthode `UpdateEntity` dans la classe `TableService`.  Cette méthode charge d’abord l’entité existante à partir de l’API Table à l’aide de la méthode [GetEntity](/dotnet/api/azure.data.tables.tableclient.getentity) sur [TableClient](/dotnet/api/azure.data.tables.tableclient).  Elle met ensuite à jour cet objet d’entité, et utilise la méthode `UpdateEntity` pour enregistrer les mises à jour dans la base de données.  Remarquez de quelle façon la méthode [UpdateEntity](/dotnet/api/azure.data.tables.tableclient.updateentity) prend l’étiquette d’entité actuelle de l’objet pour s’assurer que l’objet n’a pas été modifié depuis son chargement initial.  Si vous souhaitez mettre à jour l’entité indépendamment, vous pouvez passer une valeur de `Etag.Any` à la méthode `UpdateEntity`.

```csharp
public void UpdateEntity(UpdateWeatherObject weatherObject)
{
    string partitionKey = weatherObject.StationName;
    string rowKey = weatherObject.ObservationDate;

    // Use the partition key and row key to get the entity
    TableEntity entity = _tableClient.GetEntity<TableEntity>(partitionKey, rowKey).Value;

    foreach (string propertyName in weatherObject.PropertyNames)
    {
        var value = weatherObject[propertyName];
        entity[propertyName] = value;
    }

    _tableClient.UpdateEntity(entity, new ETag(weatherObject.Etag));
}
```

### <a name="remove-an-entity"></a>Supprimer une entité

Pour supprimer une entité d’une table, appelez la méthode [DeleteEntity](/dotnet/api/azure.data.tables.tableclient.deleteentity) sur l’objet [TableClient](/dotnet/api/azure.data.tables.tableclient) avec la clé de partition et la clé de ligne de l’objet.

```csharp
public void RemoveEntity(string partitionKey, string rowKey)
{
    _tableClient.DeleteEntity(partitionKey, rowKey);           
}
```

## <a name="7---run-the-code"></a>7 – Exécuter le code

Exécutez l’exemple d’application pour interagir avec l’API Table Cosmos DB.  La première fois que vous exécuterez l’application, il n’y aura aucune donnée affichée, car la table est vide.  Utilisez l’un des boutons situés en haut de l’application pour ajouter des données à la table.

:::image type="content" source="./media/create-table-dotnet/table-api-app-data-insert-buttons-480px.png" alt-text="Capture d’écran de l’application montrant l’emplacement des boutons utilisés pour insérer des données dans Cosmos DB à l’aide de l’API Table." lightbox="./media/create-table-dotnet/table-api-app-data-insert-buttons.png":::

En sélectionnant le bouton **Insert using Table Entity** (Insérer à l’aide d’une entité de table), vous ouvrez une boîte de dialogue dans laquelle vous pouvez insérer une nouvelle ligne ou en faire un upsert en utilisant un objet `TableEntity`.

:::image type="content" source="./media/create-table-dotnet/table-api-app-insert-table-entity-480px.png" alt-text="Capture d’écran de l’application montrant la boîte de dialogue utilisée pour insérer des données à l’aide d’un objet TableEntity." lightbox="./media/create-table-dotnet/table-api-app-insert-table-entity.png":::

La sélection du bouton **Insert using Expandable Data** (Insérer à l’aide de données extensibles) affiche une boîte de dialogue qui vous permet d’insérer un objet avec des propriétés personnalisées, illustrant ainsi comment l’API Table Cosmos DB ajoute automatiquement des propriétés (colonnes) à la table, si nécessaire.  Utilisez le bouton *Add Custom Field* (Ajouter un champ personnalisé) pour ajouter une ou plusieurs nouvelles propriétés et illustrer cette fonctionnalité.

:::image type="content" source="./media/create-table-dotnet/table-api-app-insert-expandable-entity-480px.png" alt-text="Capture d’écran de l’application montrant la boîte de dialogue utilisée pour insérer des données à l’aide d’un objet doté de champs personnalisés." lightbox="./media/create-table-dotnet/table-api-app-insert-expandable-entity.png":::

Utilisez le bouton **Insert Sample Data** (Insérer des exemples de données) pour charger des exemples de données dans votre table Cosmos DB.

:::image type="content" source="./media/create-table-dotnet/table-api-app-sample-data-insert-480px.png" alt-text="Capture d’écran de l’application montrant l’emplacement du bouton d’insertion des exemples de données." lightbox="./media/create-table-dotnet/table-api-app-sample-data-insert.png":::

Sélectionnez l’élément **Filter Results** (Filtrer les résultats) dans le menu supérieur pour atteindre la page de filtre des résultats.  Dans cette page, renseignez les critères de filtre pour démontrer comment une clause de filtre peut être générée et transmise à l’API Table Cosmos DB.

:::image type="content" source="./media/create-table-dotnet/table-api-app-filter-data-480px.png" alt-text="Capture d’écran de l’application montrant la possibilité de filtrage des résultats, et la mise en évidence de l’élément de menu utilisé pour accéder à la page." lightbox="./media/create-table-dotnet/table-api-app-filter-data.png":::

## <a name="clean-up-resources"></a>Nettoyer les ressources

Lorsque vous n’avez plus besoin de l’exemple d’application, supprimez de votre compte Azure toutes les ressources Azure associées à cet article.  Vous effectuez cette opération en supprimant le groupe de ressources.

### <a name="azure-portal"></a>[Azure portal](#tab/azure-portal)

Il est possible de supprimer un groupe de ressources dans le [portail Azure](https://portal.azure.com/) en procédant comme indiqué ici.

| Instructions    | Capture d'écran |
|:----------------|-----------:|
| [!INCLUDE [Delete resource group step 1](./includes/create-table-dotnet/remove-resource-group-1.md)] | :::image type="content" source="./media/create-table-dotnet/azure-portal-remove-resource-group-1-240px.png" alt-text="Capture d’écran montrant comment rechercher un groupe de ressources." lightbox="./media/create-table-dotnet/azure-portal-remove-resource-group-1.png"::: |
| [!INCLUDE [Delete resource group step 2](./includes/create-table-dotnet/remove-resource-group-2.md)] | :::image type="content" source="./media/create-table-dotnet/azure-portal-remove-resource-group-2-240px.png" alt-text="Capture d’écran montrant l’emplacement du bouton Supprimer le groupe de ressources." lightbox="./media/create-table-dotnet/azure-portal-remove-resource-group-2.png"::: |
| [!INCLUDE [Delete resource group step 3](./includes/create-table-dotnet/remove-resource-group-3.md)] | :::image type="content" source="./media/create-table-dotnet/azure-portal-remove-resource-group-3-240px.png" alt-text="Capture d’écran montrant la boîte de dialogue de confirmation de la suppression d’un groupe de ressources." lightbox="./media/create-table-dotnet/azure-portal-remove-resource-group-3.png"::: |

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pour supprimer un groupe de ressources à l’aide de l’interface Azure CLI, utilisez la commande [az group delete](/cli/azure/group#az_group_delete) avec le nom du groupe de ressources à supprimer.  La suppression d’un groupe de ressources entraîne également la suppression de toutes les ressources Azure qu’il contient.

```azurecli
az group delete --name $RESOURCE_GROUP_NAME
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Pour supprimer un groupe de ressources à l’aide d’Azure PowerShell, utilisez la commande [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) avec le nom du groupe de ressources à supprimer.  La suppression d’un groupe de ressources entraîne également la suppression de toutes les ressources Azure qu’il contient.

```azurepowershell
Remove-AzResourceGroup -Name $resourceGroupName
```

---

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez appris à créer un compte Azure Cosmos DB, à créer une table à l’aide de l’Explorateur de données, et à exécuter une application.  Maintenant, vous pouvez interroger vos données à l’aide de l’API Table.  

> [!div class="nextstepaction"]
> [Importer des données de table dans l’API Table](table-import.md)
