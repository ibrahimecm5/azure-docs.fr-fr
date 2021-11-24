---
title: Liaison de sortie Azure SQL pour Functions
description: Découvrez comment utiliser la liaison de sortie Azure SQL dans Azure Functions.
author: dzsquared
ms.topic: reference
ms.date: 11/12/2021
ms.author: drskwier
ms.reviewer: cachai
ms.openlocfilehash: 37a9601f30c64108b079ec8573553a05088c8629
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132493264"
---
# <a name="azure-sql-output-binding-for-azure-functions-preview"></a>Liaison de sortie Azure SQL pour Azure Functions (préversion)

La liaison de sortie Azure SQL vous permet d’écrire dans une base de données.

Pour plus d’informations sur les détails d’installation et de configuration, consultez la [vue d’ensemble](./functions-bindings-azure-sql.md).


<a id="example" name="example"></a>

# <a name="c"></a>[C#](#tab/csharp)

Cette section contient les exemples suivants :

* [Déclencheur http, écriture d’un enregistrement](#http-trigger-write-one-record-c)
* [Déclencheur http, écriture d’enregistrements à l’aide d’IAsyncCollector](#http-trigger-write-records-using-iasynccollector-c)

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

<a id="http-trigger-write-one-record-c"></a>

### <a name="http-trigger-write-one-record"></a>Déclencheur http, écriture d’un enregistrement

L’exemple suivant montre une [fonction C#](functions-dotnet-class-library.md) qui ajoute un document à une base de données, à l’aide des données fournies dans le message de Stockage File d’attente.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;
using System;

namespace AzureSQLSamples
{
    public static class WriteOneRecord
    {
        [FunctionName("WriteOneRecord")]
        public static IActionResult Run(
            [HttpTrigger(AuthorizationLevel.Function, "get", Route = "addtodo")] HttpRequest req,
            ILogger log,
            [Sql("dbo.ToDo", ConnectionStringSetting = "SqlConnectionString")] out ToDoItem newItem)
        {
            newItem = new ToDoItem
            {
                Id = req.Query["id"],
                Description =req.Query["desc"]
            };

            log.LogInformation($"C# HTTP trigger function inserted one row");
            return new CreatedResult($"/api/addtodo", newItem);
        }
    }
}
```

<a id="http-trigger-write-records-using-iasynccollector-c"></a>

### <a name="http-trigger-write-records-using-iasynccollector"></a>Déclencheur http, écriture d’enregistrements à l’aide d’IAsyncCollector

L’exemple suivant montre une [fonction C#](functions-dotnet-class-library.md) qui ajoute une collection d’enregistrements à une base de données en utilisant les données fournies dans le JSON d’un corps HTTP POST.

```cs
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Newtonsoft.Json;
using System.IO;
using System.Threading.Tasks;

namespace AzureSQLSamples
{
    public static class WriteRecordsAsync
    {
        [FunctionName("WriteRecordsAsync")]
        public static async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "post", Route = "addtodo-asynccollector")]
            HttpRequest req,
            [Sql("dbo.Products", ConnectionStringSetting = "SqlConnectionString")] IAsyncCollector<ToDoItem> newItems)
        {
            string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
            var incomingItems = JsonConvert.DeserializeObject<ToDoItem[]>(requestBody);
            foreach (ToDoItem newItem in incomingItems)
            {
                await newItems.AddAsync(newItem);
            }
            // Rows are upserted here
            await newItems.FlushAsync();

            return new CreatedResult($"/api/addtodo-asynccollector", "done");
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

Le constructeur de l’attribut prend le texte de la commande SQL et le nom du paramètre de la chaîne de connexion. Pour une liaison de sortie, la chaîne de commande SQL est le nom de la table dans laquelle les données doivent être stockées. Le nom du paramètre de la chaîne de connexion correspond au paramètre d’application (dans `local.settings.json` pour le développement local) qui contient la [chaîne de connexion](/dotnet/api/microsoft.data.sqlclient.sqlconnection.connectionstring?view=sqlclient-dotnet-core-3.0&preserve-view=true#Microsoft_Data_SqlClient_SqlConnection_ConnectionString) à l’instance SQL ou Azure SQL.

Voici un exemple d’attribut `Sql` dans une signature de méthode :

```csharp
    [FunctionName("HTTPtoSQL")]
    public static IActionResult Run(
            [HttpTrigger(AuthorizationLevel.Function, "get", Route = "addtodo")] HttpRequest req,
            [Sql("dbo.ToDo", ConnectionStringSetting = "SqlConnectionString")] out ToDoItem newItem)
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

- [Lire des données à partir d’une base de données (liaison d’entrée)](./functions-bindings-azure-sql-input.md)
