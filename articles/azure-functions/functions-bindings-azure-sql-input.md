---
title: Liaison de sortie Azure SQL pour Functions
description: Découvrez comment utiliser la liaison d’entrée Azure SQL dans Azure Functions.
author: dzsquared
ms.topic: reference
ms.date: 11/12/2021
ms.author: drskwier
ms.reviewer: cachai
ms.openlocfilehash: 3c910ab85d3689eadfdf4ba32cc2702ce03ace65
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132491086"
---
# <a name="azure-sql-input-binding-for-azure-functions-preview"></a>Liaison d’entrée Azure SQL pour Azure Functions (préversion)

La liaison d’entrée Azure SQL récupère les données d’une base de données et les transmet au paramètre d’entrée de la fonction.

Pour plus d’informations sur les détails d’installation et de configuration, consultez la [vue d’ensemble](./functions-bindings-azure-sql.md).

<a id="example" name="example"></a>

# <a name="c"></a>[C#](#tab/csharp)

Cette section contient les exemples suivants :

* [Déclencheur HTTP, rechercher l’ID à partir de la chaîne de requête](#http-trigger-look-up-id-from-query-string-c)
* [Déclencheur HTTP, obtention de plusieurs documents à partir de données d’itinéraire](#http-trigger-get-multiple-items-from-route-data-c)

Les exemples font référence à un type `ToDoItem` et à une table de base de données correspondante :

```cs
namespace AzureSQLSamples
{
    public class ToDoItem
    {
        public string Id { get; set; }
        public int Priority { get; set; }
        public string Description { get; set; }
    }
}
```

```sql
CREATE TABLE dbo.ToDo (
    [Id] int primary key,
    [Priority] int null,
    [Description] nvarchar(200) not null
)
```

<a id="http-trigger-look-up-id-from-query-string-c"></a>

### <a name="http-trigger-look-up-id-from-query-string"></a>Déclencheur HTTP, rechercher l’ID à partir de la chaîne de requête

L’exemple suivant illustre une [fonction C#](functions-dotnet-class-library.md) qui récupère un dossier unique. La fonction est déclenchée par une requête HTTP qui utilise une chaîne de requête pour spécifier l’ID à rechercher. Cet ID est utilisé pour récupérer un `ToDoItem`enregistrement avec la requête spécifiée.

> [!NOTE]
> Le paramètre de chaîne de requête HTTP respecte la casse.
>

```cs
using System.Collections.Generic;
using System.Linq;
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;

namespace AzureSQLSamples
{
    public static class GetToDoItem
    {
        [FunctionName("GetToDoItem")]
        public static IActionResult Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", Route = "gettodoitem")]
            HttpRequest req,
            [Sql("select * from dbo.ToDo where Id = @Id",
                CommandType = System.Data.CommandType.Text,
                Parameters = "@Id={Query.id}",
                ConnectionStringSetting = "SqlConnectionString")]
            IEnumerable<ToDoItem> toDoItem)
        {
            return new OkObjectResult(toDoItem.FirstOrDefault());
        }
    }
}
```

<a id="http-trigger-get-multiple-items-from-route-data-c"></a>

### <a name="http-trigger-get-multiple-items-from-route-data"></a>Déclencheur HTTP, obtention de plusieurs documents élément à partir de données d’itinéraire

L’exemple suivant illustre une [fonction C#](functions-dotnet-class-library.md) qui récupère les documents retournés par la requête. Cette fonction est déclenchée par une requête HTTP qui utilise des données de routage pour spécifier la valeur du paramètre de la requête. Ce paramètre est utilisé pour filtrer les enregistrements `ToDoItem` dans la requête spécifiée.

```cs
using System.Collections.Generic;
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;

namespace AzureSQLSamples
{
    public static class GetToDoItems
    {
        [FunctionName("GetToDoItems")]
        public static IActionResult Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", Route = "gettodoitems/{priority}")]
            HttpRequest req,
            [Sql("select * from dbo.ToDo where [Priority] > @Priority",
                CommandType = System.Data.CommandType.Text,
                Parameters = "@Priority={priority}",
                ConnectionStringSetting = "SqlConnectionString")]
            IEnumerable<ToDoItem> toDoItems)
        {
            return new OkObjectResult(toDoItems);
        }
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

La liaison Azure SQL pour Azure Functions ne prend pas en charge JavaScript pour le moment.

# <a name="python"></a>[Python](#tab/python)

La liaison Azure SQL pour Azure Functions ne prend pas en charge Python pour le moment.

 ---

## <a name="attributes-and-annotations"></a>Attributs et annotations

# <a name="c"></a>[C#](#tab/csharp)

Dans les [bibliothèques de classes C#](functions-dotnet-class-library.md), utilisez l’attribut [Sql](https://github.com/Azure/azure-functions-sql-extension/blob/main/src/SqlAttribute.cs).

Le constructeur de l'attribut prend le texte de la commande SQL, le type de commande, les paramètres et le nom du paramètre de la chaîne de connexion. La commande peut être une requête Transact-SQL (T-SQL) avec le type de commande `System.Data.CommandType.Text` ou le nom de la procédure stockée avec le type de commande `System.Data.CommandType.StoredProcedure`. Le nom du paramètre de la chaîne de connexion correspond au paramètre d’application (dans `local.settings.json` pour le développement local) qui contient la [chaîne de connexion](/dotnet/api/microsoft.data.sqlclient.sqlconnection.connectionstring?view=sqlclient-dotnet-core-3.0&preserve-view=true#Microsoft_Data_SqlClient_SqlConnection_ConnectionString) à l’instance SQL ou Azure SQL.

Voici un exemple d’attribut `Sql` dans une signature de méthode :

```csharp
    [FunctionName("GetToDoItems")]
    public static IActionResult Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", Route = "gettodoitems/{priority}")]
            HttpRequest req,
            [Sql("select * from dbo.ToDo where [Priority] > @Priority",
                CommandType = System.Data.CommandType.Text,
                Parameters = "@Priority={priority}",
                ConnectionStringSetting = "SqlConnectionString")]
            IEnumerable<ToDoItem> toDoItems)
    {
        ...
    }
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

La liaison Azure SQL pour Azure Functions ne prend pas en charge JavaScript pour le moment.

# <a name="python"></a>[Python](#tab/python)

La liaison Azure SQL pour Azure Functions ne prend pas en charge Python pour le moment.


---


[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]



## <a name="next-steps"></a>Étapes suivantes

- [Enregistrer des données dans une base de données (liaison de sortie)](./functions-bindings-azure-sql-output.md)
