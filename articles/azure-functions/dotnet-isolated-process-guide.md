---
title: Guide pour l’exécution d’Azure Functions C# dans un processus isolé
description: Apprenez à utiliser un processus isolé .NET pour exécuter vos fonctions C# dans Azure, qui prend en charge .NET 5.0 et les versions ultérieures.
ms.service: azure-functions
ms.topic: conceptual
ms.date: 06/01/2021
ms.custom: template-concept
recommendations: false
ms.openlocfilehash: b12841b83e4c2f6f2756ddffdd4adc7bde77e73a
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132401266"
---
# <a name="guide-for-running-c-azure-functions-in-an-isolated-process"></a>Guide pour l’exécution d’Azure Functions C# dans un processus isolé

Cet article présente l’utilisation de C# pour développer des fonctions de processus isolé .NET s’exécutant hors processus dans Azure Functions. L’exécution hors processus vous permet de découpler le code de votre fonction à partir du runtime Azure Functions. Les fonctions de processus isolé C# s’exécutent à la fois sur .NET 5.0 et .NET 6.0. [Les fonctions de la bibliothèque de classes C# in-process](functions-dotnet-class-library.md) ne sont pas prises en charge sur .NET 5.0. 

| Prise en main | Concepts| Exemples |
|--|--|--| 
| <ul><li>[Utilisation de Visual Studio Code](create-first-function-vs-code-csharp.md?tabs=isolated-process)</li><li>[Utilisation d’outils en ligne de commande](create-first-function-cli-csharp.md?tabs=isolated-process)</li><li>[Utilisation de Visual Studio](functions-create-your-first-function-visual-studio.md?tabs=isolated-process)</li></ul> | <ul><li>[Options d’hébergement](functions-scale.md)</li><li>[Surveillance](functions-monitoring.md)</li> | <ul><li>[Exemples de référence](https://github.com/Azure/azure-functions-dotnet-worker/tree/main/samples)</li></ul> |

## <a name="why-net-isolated-process"></a>Pourquoi le processus isolé .NET ?

Jusqu’ici, Azure Functions prenait uniquement en charge un mode étroitement intégré pour les fonctions .NET s’exécutant [en tant que bibliothèque de classes](functions-dotnet-class-library.md) dans le même processus que l’hôte. Ce mode offre une intégration étroite entre le processus hôte et les fonctions. Par exemple, les fonctions de bibliothèque de classes .NET peuvent partager des API et des types de liaison. Toutefois, cette intégration requiert également un couplage plus étroit entre le processus hôte et la fonction .NET. Par exemple, les fonctions .NET exécutées in-process doivent être exécutées sur la même version .NET que le runtime Functions. Pour une exécution en dehors de ces contraintes, vous pouvez désormais opter pour une exécution dans un processus isolé. En outre, cette isolation du processus vous permet de développer des fonctions utilisant les versions .NET actuelles (telles que .NET 5.0), non prises en charge en mode natif par le runtime Functions. Le processus isolé et les fonctions de la bibliothèque de classes C# in-process s’exécutent sur .NET 6.0. Pour en savoir plus, consultez [Versions prises en charge](#supported-versions). 

Ces fonctions s’exécutant dans un processus distinct, il existe certaines [différences de fonctionnalités](#differences-with-net-class-library-functions) entre les applications de fonctions isolées .NET et les applications de fonctions de la bibliothèque de classes .NET.

### <a name="benefits-of-running-out-of-process"></a>Avantages de l’exécution hors processus

Exécutées hors processus, vos fonctions .NET peuvent tirer parti des avantages suivants : 

+ Moins de conflits : les fonctions s’exécutant dans un processus distinct, les assemblys utilisés dans votre application ne sont pas en conflit avec une version différente des mêmes assemblys utilisés par le processus hôte.  
+ Contrôle total du processus : vous contrôlez le démarrage de l’application, ainsi que les configurations utilisées et l’intergiciel démarré.
+ Injection de dépendances : étant donné que vous contrôlez totalement le processus, vous pouvez utiliser les comportements .NET actuels pour injecter des dépendances et incorporer l’intergiciel dans votre application de fonction. 

[!INCLUDE [functions-dotnet-supported-versions](../../includes/functions-dotnet-supported-versions.md)]

## <a name="net-isolated-project"></a>Projet isolé .NET

Un projet de fonction isolé .NET est essentiellement un projet d’application de console .NET ciblant un runtime .NET pris en charge. Voici les fichiers de base requis dans un projet isolé .NET :

+ Fichier [host.json](functions-host-json.md).
+ Fichier [local.settings.json](functions-develop-local.md#local-settings-file).
+ Fichier projet C# (.csproj) définissant le projet et les dépendances.
+ Fichier Program.cs correspondant au point d’entrée de l’application.

## <a name="package-references"></a>Références de package

En cas d’exécution hors processus, votre projet .NET utilise un ensemble unique de packages implémentant les fonctionnalités principales, ainsi que les extensions de liaison. 

### <a name="core-packages"></a>Packages principaux 

Les packages suivants sont requis pour exécuter vos fonctions .NET dans un processus isolé :

+ [Microsoft.Azure.Functions.Worker](https://www.nuget.org/packages/Microsoft.Azure.Functions.Worker/)
+ [Microsoft.Azure.Functions.Worker.Sdk](https://www.nuget.org/packages/Microsoft.Azure.Functions.Worker.Sdk/)

### <a name="extension-packages"></a>Packages d'extension

Les fonctions s’exécutant dans un processus isolé .NET utilisent différents types de liaison et requièrent un ensemble unique de packages d’extension de liaison. 

Vous trouverez ces packages d’extension sous [Microsoft.Azure.Functions.Worker.Extensions](https://www.nuget.org/packages?q=Microsoft.Azure.Functions.Worker.Extensions).

## <a name="start-up-and-configuration"></a>Démarrage et configuration 

Lorsque vous utilisez des fonctions isolées .NET, vous avez accès au démarrage de votre application de fonction, qui se trouve généralement dans Program.cs. Vous devez créer et démarrer votre propre instance d’hôte. Ainsi, vous disposez également d’un accès direct au pipeline de configuration de votre application. En cas d’exécution hors processus, vous pouvez ajouter plus facilement des configurations, injecter des dépendances et exécuter votre propre intergiciel. 

Le code suivant présente un exemple de pipeline [HostBuilder] :

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/FunctionApp/Program.cs" id="docsnippet_startup":::

Le code nécessite `using Microsoft.Extensions.DependencyInjection;`. 

Un [HostBuilder] est utilisé pour générer et renvoyer une instance [IHost] entièrement initialisée que vous exécutez de façon asynchrone pour démarrer votre application de fonction. 

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/FunctionApp/Program.cs" id="docsnippet_host_run":::

### <a name="configuration"></a>Configuration

La méthode [ConfigureFunctionsWorkerDefaults] est utilisée pour ajouter les paramètres requis pour que l’application de fonction s’exécute hors processus, ce qui comprend les fonctionnalités suivantes :

+ Ensemble de convertisseurs par défaut.
+ Définissez la valeur par défaut de [JsonSerializerOptions] sur ignorer la casse des noms de propriétés.
+ Intégration à la journalisation Azure Functions.
+ Fonctionnalités et intergiciel de liaison de sortie.
+ Intergiciel d’exécution de fonction.
+ Prise en charge par défaut de gRPC. 

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/FunctionApp/Program.cs" id="docsnippet_configure_defaults" :::   

L’accès au pipeline du générateur d’hôte vous permet également de définir des configurations spécifiques à l’application lors de l’initialisation. Vous pouvez appeler la méthode [ConfigureAppConfiguration] sur [HostBuilder] une ou plusieurs fois pour ajouter les configurations requises par votre application de fonction. Pour en savoir plus sur la configuration, consultez [Configuration dans ASP.NET Core](/aspnet/core/fundamentals/configuration/?view=aspnetcore-5.0&preserve-view=true). 

Ces configurations s’appliquent à votre application de fonction s’exécutant dans un processus distinct. Pour apporter des modifications à l’hôte Functions ou au déclencheur et à la configuration de liaison, vous devez toujours utiliser le [fichier host.json](functions-host-json.md).   

### <a name="dependency-injection"></a>Injection de dépendances

L’injection de dépendances est simplifiée par rapport aux bibliothèques de classes .NET. Plutôt que de devoir créer une classe de démarrage pour inscrire les services, il vous suffit d’appeler [ConfigureServices] sur le générateur d’hôte et d’utiliser les méthodes d’extension sur [IServiceCollection] pour injecter des services spécifiques. 

L’exemple suivant injecte une dépendance de service singleton :  
 
:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/FunctionApp/Program.cs" id="docsnippet_dependency_injection" :::

Le code nécessite `using Microsoft.Extensions.DependencyInjection;`. Pour plus d’informations, consultez [Injection de dépendances dans ASP.net Core](/aspnet/core/fundamentals/dependency-injection?view=aspnetcore-5.0&preserve-view=true).

### <a name="middleware"></a>Middleware

Le processus isolé.NET prend également en charge l’inscription d’intergiciel, à nouveau à l’aide d’un modèle similaire à ce qui existe dans ASP.NET. Ce modèle vous donne la possibilité d’injecter une logique dans le pipeline d’appel, et avant et après l’exécution des fonctions.

La méthode d’extension [ConfigureFunctionsWorkerDefaults] a une surcharge qui vous permet d’inscrire votre propre intergiciel, comme vous pouvez le voir dans l’exemple suivant.  

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/CustomMiddleware/Program.cs" id="docsnippet_middleware_register" :::

Pour obtenir un exemple plus complet de l’utilisation d’un intergiciel personnalisé dans votre application de fonction, consultez l'[exemple de référence d’intergiciel personnalisé](https://github.com/Azure/azure-functions-dotnet-worker/blob/main/samples/CustomMiddleware).

## <a name="execution-context"></a>Contexte d’exécution

Le processus isolé .NET transmet un objet [FunctionContext] à vos méthodes de fonction. Cet objet vous permet d’obtenir une instance [ILogger] pour écrire dans les journaux en appelant la méthode [GetLogger] et en fournissant une chaîne `categoryName`. Pour en savoir plus, consultez [Journalisation](#logging). 

## <a name="bindings"></a>Liaisons 

Les liaisons sont définies à l’aide d’attributs sur les méthodes, paramètres et types de retour. Une méthode de fonction est une méthode dotée d’un attribut `Function` et d’un attribut de déclencheur appliqué à un paramètre d’entrée, comme indiqué dans l’exemple suivant :

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/Extensions/Queue/QueueFunction.cs" id="docsnippet_queue_trigger" :::

L’attribut de déclencheur spécifie le type de déclencheur et lie les données d’entrée à un paramètre de méthode. L’exemple de fonction précédent est déclenché par un message de file d’attente, qui est lui-même transmis à la méthode dans le paramètre `myQueueItem`.

L’attribut `Function` marque une méthode comme point d’entrée de la fonction. Le nom doit être unique au sein d’un projet, commencer par une lettre et ne contenir que des lettres, des chiffres, `_` et `-`, jusqu’à 127 caractères. Les modèles de projets créent souvent une méthode nommée `Run`, mais le nom de la méthode peut être n’importe quel nom de méthode C# valide.

Les projets isolés .NET s’exécutant dans un processus de travail distinct, les liaisons ne peuvent pas tirer parti des classes de liaison riches, comme `ICollector<T>`, `IAsyncCollector<T>` et `CloudBlockBlob`. En outre, il n’existe pas de prise en charge directe des types hérités des kits de développement logiciel (SDK) de service sous-jacents, comme [DocumentClient] et [BrokeredMessage]. Au lieu de cela, les liaisons reposent sur des chaînes, des tableaux et des types sérialisables, comme des objets POCO (Plain Old Class Objects). 

Pour les déclencheurs HTTP, vous devez utiliser [HttpRequestData] et [HttpResponseData] afin d’accéder aux données de requête et de réponse. En effet, vous n’avez pas accès aux objets de requête et de réponse HTTP d’origine lors d’une exécution hors processus.

Pour obtenir un ensemble complet d’exemples de référence pour l’utilisation de déclencheurs et de liaisons lors de l’exécution hors processus, consultez l'[exemple de référence des extensions de liaison](https://github.com/Azure/azure-functions-dotnet-worker/blob/main/samples/Extensions). 

### <a name="input-bindings"></a>Liaisons d’entrée

Une fonction peut avoir zéro ou plusieurs liaisons d’entrée susceptibles de transmettre des données à une fonction. Comme les déclencheurs, les liaisons d’entrée sont définies en appliquant un attribut de liaison à un paramètre d’entrée. Lorsque la fonction s’exécute, le runtime tente d’obtenir les données spécifiées dans la liaison. Les données demandées dépendent souvent des informations fournies par le déclencheur à l’aide de paramètres de liaison.  

### <a name="output-bindings"></a>Liaisons de sortie

Pour écrire dans une liaison de sortie, vous devez appliquer un attribut de liaison de sortie à la méthode de fonction qui a défini la manière d’écrire dans le service lié. La valeur renvoyée par la méthode est écrite dans la liaison de sortie. Par exemple, l’exemple suivant écrit une valeur de chaîne dans une file d’attente de messages nommée `myqueue-output` à l’aide d’une liaison de sortie :

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/Extensions/Queue/QueueFunction.cs" id="docsnippet_queue_output_binding" :::

### <a name="multiple-output-bindings"></a>Liaisons de sortie multiples

Les données écrites dans une liaison de sortie correspondent toujours à la valeur renvoyée de la fonction. S’il vous faut écrire dans plusieurs liaisons de sortie, vous devez créer un type de retour personnalisé. Ce type de retour doit présenter l’attribut de liaison de sortie appliqué à une ou plusieurs propriétés de la classe. L’exemple suivant d’un déclencheur HTTP permet d’écrire à la fois dans la réponse HTTP et dans une liaison de sortie de file d’attente :

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/Extensions/MultiOutput/MultiOutput.cs" id="docsnippet_multiple_outputs":::

Dans la mesure où la réponse d’un déclencheur HTTP est toujours considérée comme une sortie, un attribut de valeur de retour n’est pas nécessaire.

### <a name="http-trigger"></a>Déclencheur HTTP

Les déclencheurs HTTP traduisent le message de requête HTTP entrant en objet [HttpRequestData] qui est transmis à la fonction. Cet objet fournit les données de la requête, notamment `Headers`, `Cookies`, `Identities`, `URL`, et un message facultatif `Body`. Cet objet est une représentation de l’objet de requête HTTP, et non de la demande elle-même. 

De même, la fonction retourne un objet [HttpResponseData], qui fournit les données utilisées pour créer la réponse HTTP, notamment le message `StatusCode`, `Headers` et éventuellement un message `Body`.  

Le code suivant correspond à un déclencheur HTTP. 

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/Extensions/Http/HttpFunction.cs" id="docsnippet_http_trigger" :::

## <a name="logging"></a>Journalisation

Au sein d’un processus isolé .NET, vous pouvez écrire dans les journaux à l’aide d’une instance [ILogger] obtenue à partir d’un objet [FunctionContext] transmis à votre fonction. Appelez la méthode [GetLogger], en transmettant une valeur de chaîne correspondant au nom de la catégorie dans laquelle les journaux sont écrits. La catégorie correspond généralement au nom de la fonction spécifique à partir de laquelle les journaux sont écrits. Pour en savoir plus sur les catégories, consultez l’[article relative à la surveillance](functions-monitoring.md#log-levels-and-categories). 

L’exemple suivant montre comment obtenir un [ILogger] et écrire des journaux à l’intérieur d’une fonction :

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/Extensions/Http/HttpFunction.cs" id="docsnippet_logging" ::: 

Utilisez différentes méthodes de [ILogger] pour écrire différents niveaux de journalisation, tels que `LogWarning` ou `LogError`. Pour en savoir plus sur les niveaux de journalisation, consultez l’[article relatif à la surveillance](functions-monitoring.md#log-levels-and-categories).

Un [ILogger] est également fourni lors de l’utilisation de l'[injection de dépendances](#dependency-injection).

## <a name="differences-with-net-class-library-functions"></a>Différences par rapport aux fonctions de la bibliothèque de classes .NET

Cette section décrit l’état actuel des différences fonctionnelles et comportementales s’exécutant sur une instance hors processus par rapport aux fonctions de la bibliothèque de classes .NET s’exécutant in-process :

| Fonctionnalité/Comportement |  In-process | Out-of-process  |
| ---- | ---- | ---- |
| Versions .NET | .NET Core 3.1<br/>.NET 6.0 | .NET 5.0<br/>.NET 6.0 |
| Packages principaux | [Microsoft.NET.Sdk.Functions](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions/) | [Microsoft.Azure.Functions.Worker](https://www.nuget.org/packages/Microsoft.Azure.Functions.Worker/)<br/>[Microsoft.Azure.Functions.Worker.Sdk](https://www.nuget.org/packages/Microsoft.Azure.Functions.Worker.Sdk) | 
| Packages d’extension de liaison | [Microsoft.Azure.WebJobs.Extensions.*](https://www.nuget.org/packages?q=Microsoft.Azure.WebJobs.Extensions)  | Sous [Microsoft.Azure.Functions.Worker.Extensions.*](https://www.nuget.org/packages?q=Microsoft.Azure.Functions.Worker.Extensions) | 
| Journalisation | [ILogger] transmis à la fonction | [ILogger] obtenu à partir de [FunctionContext] |
| Jetons d’annulation | [Pris en charge](functions-dotnet-class-library.md#cancellation-tokens) | Non pris en charge |
| Liaisons de sortie | Paramètres de sortie | Valeurs de retour |
| Types de liaisons de sortie |  `IAsyncCollector`, [DocumentClient], [BrokeredMessage] et autres types spécifiques au client | Types simples, types sérialisables JSON et tableaux. |
| Liaisons de sortie multiples | Prise en charge | [Pris en charge](#multiple-output-bindings) |
| Déclencheur HTTP | [HttpRequest]/[ObjectResult] | [HttpRequestData]/[HttpResponseData] |
| Fonctions durables | [Pris en charge](durable/durable-functions-overview.md) | Non pris en charge | 
| Liaisons impératives | [Pris en charge](functions-dotnet-class-library.md#binding-at-runtime) | Non pris en charge |
| Artefact function.json | Généré | Non généré |
| Configuration | [host.json](functions-host-json.md) | [host.json](functions-host-json.md) et initialisation personnalisée |
| Injection de dépendances | [Pris en charge](functions-dotnet-dependency-injection.md)  | [Pris en charge](#dependency-injection) |
| Middleware | Non pris en charge | Prise en charge |
| Heures de démarrage froid | Standard | Délai plus long en raison du démarrage juste-à-temps. Exécution sur Linux plutôt que Windows pour limiter les possibles retards. |
| ReadyToRun | [Pris en charge](functions-dotnet-class-library.md#readytorun) | _TBD_ | 

## <a name="next-steps"></a>Étapes suivantes

+ [En savoir plus sur les déclencheurs et les liaisons](functions-triggers-bindings.md)
+ [En savoir plus sur les meilleures pratiques pour Azure Functions](functions-best-practices.md)


[HostBuilder]: /dotnet/api/microsoft.extensions.hosting.hostbuilder?view=dotnet-plat-ext-5.0&preserve-view=true
[IHost]: /dotnet/api/microsoft.extensions.hosting.ihost?view=dotnet-plat-ext-5.0&preserve-view=true
[ConfigureFunctionsWorkerDefaults]: /dotnet/api/microsoft.extensions.hosting.workerhostbuilderextensions.configurefunctionsworkerdefaults?view=azure-dotnet&preserve-view=true#Microsoft_Extensions_Hosting_WorkerHostBuilderExtensions_ConfigureFunctionsWorkerDefaults_Microsoft_Extensions_Hosting_IHostBuilder_
[ConfigureAppConfiguration]: /dotnet/api/microsoft.extensions.hosting.hostbuilder.configureappconfiguration?view=dotnet-plat-ext-5.0&preserve-view=true
[IServiceCollection]: /dotnet/api/microsoft.extensions.dependencyinjection.iservicecollection?view=dotnet-plat-ext-5.0&preserve-view=true
[ConfigureServices]: /dotnet/api/microsoft.extensions.hosting.hostbuilder.configureservices?view=dotnet-plat-ext-5.0&preserve-view=true
[FunctionContext]: /dotnet/api/microsoft.azure.functions.worker.functioncontext?view=azure-dotnet&preserve-view=true
[ILogger]: /dotnet/api/microsoft.extensions.logging.ilogger?view=dotnet-plat-ext-5.0&preserve-view=true
[GetLogger]: /dotnet/api/microsoft.azure.functions.worker.functioncontextloggerextensions.getlogger?view=azure-dotnet&preserve-view=true
[DocumentClient]: /dotnet/api/microsoft.azure.documents.client.documentclient
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[HttpRequestData]: /dotnet/api/microsoft.azure.functions.worker.http.httprequestdata?view=azure-dotnet&preserve-view=true
[HttpResponseData]: /dotnet/api/microsoft.azure.functions.worker.http.httpresponsedata?view=azure-dotnet&preserve-view=true
[HttpRequest]: /dotnet/api/microsoft.aspnetcore.http.httprequest?view=aspnetcore-5.0&preserve-view=true
[ObjectResult]: /dotnet/api/microsoft.aspnetcore.mvc.objectresult?view=aspnetcore-5.0&preserve-view=true
[JsonSerializerOptions]: /dotnet/api/system.text.json.jsonserializeroptions?view=net-5.0&preserve-view=true
