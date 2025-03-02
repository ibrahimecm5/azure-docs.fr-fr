---
title: Liaison de sortie de Stockage File d’attente Azure pour Azure Functions
description: Apprenez à créer des messages de Stockage File d’attente Azure dans Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/18/2020
ms.author: cshoe
ms.custom: devx-track-csharp, cc996988-fb4f-47, devx-track-python
ms.openlocfilehash: 015c61b7f02bec8cec274f3f746b3d693ccdffed
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129992727"
---
# <a name="azure-queue-storage-output-bindings-for-azure-functions"></a>Liaisons de sortie de Stockage File d’attente Azure pour Azure Functions

La solution Azure Functions peut créer des messages de Stockage File d’attente Azure en configurant une liaison de sortie.

Pour plus d’informations sur les détails d’installation et de configuration, consultez la [vue d’ensemble](./functions-bindings-storage-queue.md).

## <a name="example"></a>Exemple

# <a name="c"></a>[C#](#tab/csharp)

L’exemple suivant montre un code [de fonction C#](functions-dotnet-class-library.md) qui crée un message de file d’attente pour chaque requête HTTP reçue.

```csharp
[StorageAccount("MyStorageConnectionAppSetting")]
public static class QueueFunctions
{
    [FunctionName("QueueOutput")]
    [return: Queue("myqueue-items")]
    public static string QueueOutput([HttpTrigger] dynamic input,  ILogger log)
    {
        log.LogInformation($"C# function processed: {input.Text}");
        return input.Text;
    }
}
```

# <a name="c-script"></a>[Script C#](#tab/csharp-script)

L’exemple suivant montre une liaison de déclencheur HTTP dans un fichier *function.json*, ainsi que le code du [script C# (.csx)](functions-reference-csharp.md) qui utilise cette liaison. La fonction crée un élément de file d’attente avec une charge utile d’objet **CustomQueueMessage** pour chaque requête HTTP reçue.

Voici le fichier *function.json* :

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "direction": "in",
      "authLevel": "function",
      "name": "input"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "$return",
      "queueName": "outqueue",
      "connection": "MyStorageConnectionAppSetting"
    }
  ]
}
```

La section [configuration](#configuration) décrit ces propriétés.

Voici le code Script C# qui crée un message de file d’attente unique :

```cs
public class CustomQueueMessage
{
    public string PersonName { get; set; }
    public string Title { get; set; }
}

public static CustomQueueMessage Run(CustomQueueMessage input, ILogger log)
{
    return input;
}
```

Vous pouvez envoyer plusieurs messages à la fois en utilisant un paramètre `ICollector` ou `IAsyncCollector`. Voici le code Script C# qui envoie plusieurs messages, l’un avec les données de requête HTTP, et l’autre avec des valeurs codées en dur :

```cs
public static void Run(
    CustomQueueMessage input, 
    ICollector<CustomQueueMessage> myQueueItems, 
    ILogger log)
{
    myQueueItems.Add(input);
    myQueueItems.Add(new CustomQueueMessage { PersonName = "You", Title = "None" });
}
```

# <a name="java"></a>[Java](#tab/java)

 L’exemple ci-après montre une fonction Java qui crée un message de file d’attente en cas de déclenchement par une requête HTTP.

```java
@FunctionName("httpToQueue")
@QueueOutput(name = "item", queueName = "myqueue-items", connection = "MyStorageConnectionAppSetting")
 public String pushToQueue(
     @HttpTrigger(name = "request", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.ANONYMOUS)
     final String message,
     @HttpOutput(name = "response") final OutputBinding<String> result) {
       result.setValue(message + " has been added.");
       return message;
 }
```

Dans la [bibliothèque runtime des fonctions Java](/java/api/overview/azure/functions/runtime), utilisez l’annotation `@QueueOutput` sur les paramètres dont la valeur serait écrite dans Stockage File d’attente.  Le type de paramètre doit être `OutputBinding<T>`, où `T` est n’importe quel type Java natif d’un POJO.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

L’exemple suivant montre une liaison de déclencheur HTTP dans un fichier *function.json*, et une [fonction JavaScript](functions-reference-node.md) qui utilise la liaison. La fonction crée un élément de file d’attente pour chaque requête HTTP reçue.

Voici le fichier *function.json* :

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "direction": "in",
      "authLevel": "function",
      "name": "input"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "myQueueItem",
      "queueName": "outqueue",
      "connection": "MyStorageConnectionAppSetting"
    }
  ]
}
```

La section [configuration](#configuration) décrit ces propriétés.

Voici le code JavaScript :

```javascript
module.exports = function (context, input) {
    context.bindings.myQueueItem = input.body;
    context.done();
};
```

Vous pouvez envoyer plusieurs messages à la fois en définissant un tableau de messages pour la liaison de sortie `myQueueItem`. Le code JavaScript suivant envoie deux messages de file d’attente avec des valeurs codées en dur pour chaque requête HTTP reçue.

```javascript
module.exports = function(context) {
    context.bindings.myQueueItem = ["message 1","message 2"];
    context.done();
};
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Les exemples de code suivants montrent comment générer un message de file d’attente à partir d’une fonction déclenchée par le protocole HTTP. La section de configuration avec le `type` `queue` définit la liaison de sortie.

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "type": "httpTrigger",
      "direction": "in",
      "name": "Request",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "Response"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "Msg",
      "queueName": "outqueue",
      "connection": "MyStorageConnectionAppSetting"
    }
  ]
}
```

Avec cette configuration de liaison, une fonction PowerShell peut créer un message de file d’attente à l’aide de `Push-OutputBinding`. Dans cet exemple, un message est créé à partir d’une chaîne de requête ou d’un paramètre de corps.

```powershell
using namespace System.Net

# Input bindings are passed in via param block.
param($Request, $TriggerMetadata)

# Write to the Azure Functions log stream.
Write-Host "PowerShell HTTP trigger function processed a request."

# Interact with query parameters or the body of the request.
$message = $Request.Query.Message
Push-OutputBinding -Name Msg -Value $message
Push-OutputBinding -Name Response -Value ([HttpResponseContext]@{
    StatusCode = 200
    Body = "OK"
})
```

Pour envoyer plusieurs messages à la fois, définissez un tableau de messages et utilisez `Push-OutputBinding` pour envoyer des messages à la liaison de sortie de file d’attente.

```powershell
using namespace System.Net

# Input bindings are passed in via param block.
param($Request, $TriggerMetadata)

# Write to the Azure Functions log stream.
Write-Host "PowerShell HTTP trigger function processed a request."

# Interact with query parameters or the body of the request.
$message = @("message1", "message2")
Push-OutputBinding -Name Msg -Value $message
Push-OutputBinding -Name Response -Value ([HttpResponseContext]@{
    StatusCode = 200
    Body = "OK"
})
```

# <a name="python"></a>[Python](#tab/python)

L’exemple suivant montre comment sortir des valeurs uniques et multiples dans les files d’attente de stockage. La configuration requise pour *function.json* est la même dans les deux sens.

Une liaison de file d’attente de stockage est définie dans *function.json*, où *Type* est défini sur `queue`.

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "msg",
      "queueName": "outqueue",
      "connection": "AzureStorageQueuesConnectionString"
    }
  ]
}
```

Pour définir un message individuel sur la file d’attente, vous passez une valeur unique à la méthode `set`.

```python
import azure.functions as func

def main(req: func.HttpRequest, msg: func.Out[str]) -> func.HttpResponse:

    input_msg = req.params.get('message')

    msg.set(input_msg)

    return 'OK'
```

Pour créer plusieurs messages dans la file d’attente, déclarez un paramètre comme type de liste approprié et transmettez un tableau de valeurs (correspondant au type de liste) à la méthode `set`.

```python
import azure.functions as func
import typing

def main(req: func.HttpRequest, msg: func.Out[typing.List[str]]) -> func.HttpResponse:

    msg.set(['one', 'two'])

    return 'OK'
```

---

## <a name="attributes-and-annotations"></a>Attributs et annotations

# <a name="c"></a>[C#](#tab/csharp)

Dans les [bibliothèques de classes C#](functions-dotnet-class-library.md), utilisez l’attribut [QueueAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Queues/QueueAttribute.cs).

L’attribut s’applique à un paramètre `out` ou à la valeur de retour de la fonction. Le constructeur de l’attribut prend le nom de la file d’attente, comme illustré dans l’exemple suivant :

```csharp
[FunctionName("QueueOutput")]
[return: Queue("myqueue-items")]
public static string Run([HttpTrigger] dynamic input,  ILogger log)
{
    ...
}
```

Vous pouvez définir la propriété `Connection` pour spécifier le compte de stockage à utiliser, comme indiqué dans l’exemple suivant :

```csharp
[FunctionName("QueueOutput")]
[return: Queue("myqueue-items", Connection = "StorageConnectionAppSetting")]
public static string Run([HttpTrigger] dynamic input,  ILogger log)
{
    ...
}
```

Pour obtenir un exemple complet, consultez [Exemple de sortie](#example).

Vous pouvez utiliser l’attribut `StorageAccount` pour spécifier le compte de stockage au niveau de la classe, de la méthode ou du paramètre. Pour plus d’informations, consultez Déclencheur - attributs.

# <a name="c-script"></a>[Script C#](#tab/csharp-script)

Les attributs ne sont pas pris en charge par le script C#.

# <a name="java"></a>[Java](#tab/java)

L’annotation `QueueOutput` vous permet d’écrire un message de sortie d’une fonction. L’exemple suivant montre une fonction déclenchée par HTTP qui crée un message de file d’attente.

```java
package com.function;
import java.util.*;
import com.microsoft.azure.functions.annotation.*;
import com.microsoft.azure.functions.*;

public class HttpTriggerQueueOutput {
    @FunctionName("HttpTriggerQueueOutput")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", methods = {HttpMethod.GET, HttpMethod.POST}, authLevel = AuthorizationLevel.FUNCTION) HttpRequestMessage<Optional<String>> request,
            @QueueOutput(name = "message", queueName = "messages", connection = "MyStorageConnectionAppSetting") OutputBinding<String> message,
            final ExecutionContext context) {

        message.setValue(request.getQueryParameters().get("name"));
        return request.createResponseBuilder(HttpStatus.OK).body("Done").build();
    }
}
```

| Propriété    | Description |
|-------------|-----------------------------|
|`name`       | Déclare le nom du paramètre dans la signature de la fonction. Quand la fonction est déclenchée, la valeur de ce paramètre a le contenu du message de la file d’attente. |
|`queueName`  | Déclare le nom de la file d’attente dans le compte de stockage. |
|`connection` | Pointe vers la chaîne de connexion du compte de stockage. |

Le paramètre associé à l’annotation `QueueOutput` est tapé en tant qu’instance [OutputBinding\<T\>](https://github.com/Azure/azure-functions-java-library/blob/master/src/main/java/com/microsoft/azure/functions/OutputBinding.java).

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Les attributs ne sont pas pris en charge par JavaScript.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Les attributs ne sont pas pris en charge par PowerShell.

# <a name="python"></a>[Python](#tab/python)

Les attributs ne sont pas pris en charge par Python.

---

## <a name="configuration"></a>Configuration

Le tableau suivant décrit les propriétés de configuration de liaison que vous définissez dans le fichier *function.json* et l’attribut `Queue`.

|Propriété function.json | Propriété d’attribut |Description|
|---------|---------|----------------------|
|**type** | n/a | Cette propriété doit être définie sur `queue`. Cette propriété est définie automatiquement lorsque vous créez le déclencheur dans le portail Azure.|
|**direction** | n/a | Cette propriété doit être définie sur `out`. Cette propriété est définie automatiquement lorsque vous créez le déclencheur dans le portail Azure. |
|**name** | n/a | Nom de la variable qui représente la file d’attente dans le code de la fonction. La valeur doit être `$return` pour faire référence à la valeur de retour de la fonction.|
|**queueName** |**QueueName** | Nom de la file d'attente. |
|**connection** | **Connection** |Nom d’un paramètre d’application ou d’une collection de paramètres d’application qui spécifie la façon de se connecter à des files d’attente Azure. Consultez [Connexions](#connections).|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

[!INCLUDE [functions-storage-queue-connections](../../includes/functions-storage-queue-connections.md)]

## <a name="usage"></a>Usage

# <a name="c"></a>[C#](#tab/csharp)

### <a name="default"></a>Default

Écrivez un message de file d’attente unique en utilisant un paramètre de méthode comme `out T paramName`. Vous pouvez utiliser le type de retour de la méthode au lieu d’un paramètre `out`, et `T` peut être un des types suivants :

* Un objet sérialisable au format JSON
* `string`
* `byte[]`
* [CloudQueueMessage] 

Si vous essayez de lier à `CloudQueueMessage` et obtenez un message d’erreur, vérifiez que vous avez une référence à [la bonne version du SDK Stockage](functions-bindings-storage-queue.md#azure-storage-sdk-version-in-functions-1x).

En C# et Script C#, écrivez plusieurs messages de file d’attente à l’aide d’un des types suivants : 

* `ICollector<T>` ou `IAsyncCollector<T>`
* [CloudQueue](/dotnet/api/microsoft.azure.storage.queue.cloudqueue)

### <a name="additional-types"></a>Autres types

Les applications qui utilisent la [version 5.0.0 ou ultérieure de l’extension de stockage](./functions-bindings-storage-queue.md#storage-extension-5x-and-higher) peuvent également utiliser des types du [SDK Azure pour .NET](/dotnet/api/overview/azure/storage.queues-readme). Cette version supprime la prise en charge des types hérités `CloudQueue` et `CloudQueueMessage` en faveur des types suivants :

- [QueueMessage](/dotnet/api/azure.storage.queues.models.queuemessage)
- [QueueClient](/dotnet/api/azure.storage.queues.queueclient) pour l’écriture de plusieurs messages de file d’attente

Pour obtenir des exemples d’utilisation de ces types, consultez [le dépôt GitHub pour l’extension](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Microsoft.Azure.WebJobs.Extensions.Storage.Queues#examples).

# <a name="c-script"></a>[Script C#](#tab/csharp-script)

### <a name="default"></a>Default

Écrivez un message de file d’attente unique en utilisant un paramètre de méthode comme `out T paramName`. `paramName` est la valeur spécifiée dans la propriété `name` de *function.json*. Vous pouvez utiliser le type de retour de la méthode au lieu d’un paramètre `out`, et `T` peut être un des types suivants :

* Un objet sérialisable au format JSON
* `string`
* `byte[]`
* [CloudQueueMessage] 

Si vous essayez de lier à `CloudQueueMessage` et obtenez un message d’erreur, vérifiez que vous avez une référence à [la bonne version du SDK Stockage](functions-bindings-storage-queue.md#azure-storage-sdk-version-in-functions-1x).

En C# et Script C#, écrivez plusieurs messages de file d’attente à l’aide d’un des types suivants : 

* `ICollector<T>` ou `IAsyncCollector<T>`
* [CloudQueue](/dotnet/api/microsoft.azure.storage.queue.cloudqueue)

### <a name="additional-types"></a>Autres types

Les applications qui utilisent la [version 5.0.0 ou ultérieure de l’extension de stockage](./functions-bindings-storage-queue.md#storage-extension-5x-and-higher) peuvent également utiliser des types du [SDK Azure pour .NET](/dotnet/api/overview/azure/storage.queues-readme). Cette version supprime la prise en charge des types hérités `CloudQueue` et `CloudQueueMessage` en faveur des types suivants :

- [QueueMessage](/dotnet/api/azure.storage.queues.models.queuemessage)
- [QueueClient](/dotnet/api/azure.storage.queues.queueclient) pour l’écriture de plusieurs messages de file d’attente

Pour obtenir des exemples d’utilisation de ces types, consultez [le dépôt GitHub pour l’extension](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Microsoft.Azure.WebJobs.Extensions.Storage.Queues#examples).

# <a name="java"></a>[Java](#tab/java)

Il existe deux options pour produire en sortie un message File d’attente à partir d’une fonction en utilisant l’annotation [QueueOutput](/java/api/com.microsoft.azure.functions.annotation.queueoutput) :

- **Valeur de retour** : En appliquant l’annotation à la fonction elle-même, la valeur renvoyée de la fonction est conservée sous la forme d’un message File d’attente.

- **Impératif** : Pour définir explicitement la valeur du message, appliquez l’annotation à un paramètre spécifique du type [`OutputBinding<T>`](/java/api/com.microsoft.azure.functions.outputbinding), où `T` est un POJO ou n’importe quel type Java natif. Avec cette configuration, le passage d’une valeur à la méthode `setValue` rend la valeur persistante en tant que message File d’attente.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

L’élément de la file d’attente en sortie est disponible via `context.bindings.<NAME>`, où `<NAME>` correspond au nom défini dans *function.json*. Vous pouvez utiliser une chaîne ou un objet sérialisable JSON pour la charge utile de l’élément de file d’attente.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

La sortie dans le message de file d’attente est disponible via `Push-OutputBinding`, où vous transmettez les arguments qui correspondent au nom désigné par le paramètre `name` de la liaison dans le fichier *function.json*.

# <a name="python"></a>[Python](#tab/python)

Il existe deux options pour produire en sortie un message File d’attente à partir d’une fonction :

- **Valeur de retour** : Définissez la propriété `name` dans *function.json* sur `$return`. Avec cette configuration, la valeur de retour de la fonction est conservée sous la forme d’un message de Stockage File d’attente.

- **Impératif** : Passez une valeur à la méthode [set](/python/api/azure-functions/azure.functions.out#set-val--t-----none) du paramètre déclaré en tant que type [Out](/python/api/azure-functions/azure.functions.out). La valeur passée à `set` est conservée en tant que message de Stockage File d’attente.

---

## <a name="exceptions-and-return-codes"></a>Exceptions et codes de retour

| Liaison |  Informations de référence |
|---|---|
| File d'attente | [Codes d’erreur de file d’attente](/rest/api/storageservices/queue-service-error-codes) |
| Objet blob, Table, File d’attente | [Codes d’erreur de stockage](/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| Objet blob, Table, File d’attente |  [Dépannage](/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

## <a name="next-steps"></a>Étapes suivantes

- [Exécuter une fonction pour modifier des données de stockage de file d’attente (déclencheur)](./functions-bindings-storage-queue-trigger.md)

<!-- LINKS -->

[CloudQueueMessage]: /dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage
