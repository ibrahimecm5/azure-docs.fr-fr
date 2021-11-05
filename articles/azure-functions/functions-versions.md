---
title: Vue d’ensemble des versions du runtime Azure Functions
description: Azure Functions prend en charge plusieurs versions du runtime. Découvrez les différences entre elles et comment choisir celle qui vous convient.
ms.topic: conceptual
ms.custom: devx-track-dotnet
ms.date: 10/26/2021
ms.openlocfilehash: 1a1d2cc0e5aab3daac5bb65881f9e891497fd994
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131048719"
---
# <a name="azure-functions-runtime-versions-overview"></a>Vue d’ensemble des versions du runtime Azure Functions

Azure Functions prend actuellement en charge plusieurs versions de l’hôte du runtime. Le tableau suivant détaille les versions disponibles, leur niveau de support et le moment où elles doivent être utilisées :

| Version | Niveau de support | Description |
| --- | --- | --- |
| 4.x | Préversion | Prend en charge tous les langages. Utilisez cette version pour [exécuter des fonctions C# sur .NET 6.0](functions-dotnet-class-library.md#supported-versions). |
| 3.x | GA | _Version recommandée du runtime pour les fonctions dans tous les langages._ |
| 2.x | GA | Prise en charge pour les [applications héritées des versions 2.x](#pinning-to-version-20). Cette version est en mode maintenance, et les améliorations ne seront apportées que dans les versions ultérieures.|
| 1.x | GA | Recommandé uniquement pour les applications C# qui doivent utiliser .NET Framework ; ne prend en charge que le développement dans le portail Azure, le portail Azure Stack Hub ou localement sur les ordinateurs Windows. Cette version est en mode maintenance, et les améliorations ne seront apportées que dans les versions ultérieures. |

Cet article détaille certaines des différences entre ces versions, la façon dont vous pouvez créer chaque version, et la manière de changer la version sur laquelle vos fonctions s’exécutent.

[!INCLUDE [functions-support-levels](../../includes/functions-support-levels.md)]

## <a name="languages"></a>Languages

À compter de la version 2.x, le runtime utilise un modèle d’extensibilité de langage, et toutes les fonctions d’une application de fonction doivent partager le même langage. Vous avez choisi le langage des fonctions de votre application de fonction lors de la création de l’application. Le langage de votre application de fonction est conservé dans le paramètre [FUNCTIONS\_WORKER\_RUNTIME](functions-app-settings.md#functions_worker_runtime) et ne doit pas être modifié lorsque des fonctions existent. 

Le tableau suivant montre les langages de programmation actuellement pris en charge dans chaque version du runtime.

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

## <a name="run-on-a-specific-version"></a><a name="creating-1x-apps"></a>Exécuter sur une version spécifique

Par défaut, les applications de fonction créées dans le portail Azure et par l’interface Azure CLI sont configurées pour la version 3.x. Vous pouvez modifier cette version en fonction de vos besoins. Vous pouvez repasser à la version 1.x du runtime seulement après avoir créé votre application de fonction et avant d’ajouter des fonctions.  Le passage de la version 2.x à la version 3.x est autorisé, même avec les applications qui contiennent déjà des fonctions. Avant de faire passer une application qui comprend des fonctions de la version 2.x à la version 3.x, tenez compte des [changements cassants qui ont été effectués entre les versions 2.x et 3.x](#breaking-changes-between-2x-and-3x). 

Avant d’apporter une modification à la version principale du runtime, vous devez d’abord tester votre code en le déployant sur une autre application de fonction qui s’exécute sur la dernière version principale. Ce test permet de vérifier que le code s’exécute correctement après la mise à niveau. 

Le passage de la version v3.x à la version v2.x n’est pas pris en charge. Lorsque cela est possible, vous devez toujours exécuter vos applications sur la dernière version prise en charge du runtime Functions. 

### <a name="changing-version-of-apps-in-azure"></a>Changement de la version des applications dans Azure

La version du runtime Functions utilisée par les applications publiées dans Azure dépend du paramètre d’application [`FUNCTIONS_EXTENSION_VERSION`](functions-app-settings.md#functions_extension_version). Les valeurs des versions majeures du runtime suivantes sont prises en charge :

| Valeur | Cible du runtime |
| ------ | -------- |
| `~4` | 4.x (préversion) |
| `~3` | 3.x |
| `~2` | 2.x |
| `~1` | 1.x |

>[!IMPORTANT]
> Ne modifiez pas arbitrairement ce paramètre, car d’autres modifications des paramètres de l’application et d’autres modifications du code dans vos fonctions peuvent être nécessaires.

Pour en savoir plus, voir [Comment cibler des versions du runtime Azure Functions](set-runtime-version.md).  

### <a name="pinning-to-a-specific-minor-version"></a>Épinglage sur une version mineure spécifique

Pour résoudre les problèmes liés à l’exécution de votre application de fonction sur la version principale la plus récente, vous devez épingler votre application à une version mineure spécifique. Cela vous donne le temps de faire le nécessaire pour que votre application s’exécute correctement sur la dernière version majeure. La façon dont vous épinglez l’application à une version mineure n’est pas la même dans Windows et dans Linux. Pour en savoir plus, voir [Comment cibler des versions du runtime Azure Functions](set-runtime-version.md).

Les versions mineures les plus anciennes sont régulièrement supprimées de Functions. Pour obtenir les dernières informations sur les versions Azure Functions, notamment sur la suppression des versions mineures les plus anciennes, surveillez les [annonces Azure App Service](https://github.com/Azure/app-service-announcements/issues). 

### <a name="pinning-to-version-20"></a>Épinglage sur la version ~2.0

Les applications de fonction .NET qui sont exécutées sur la version 2.x (`~2`) sont automatiquement mises à niveau pour s’exécuter sur .NET Core 3.1, qui est une version de .NET Core 3 avec prise en charge à long terme. En exécutant vos fonctions .NET sur .NET Core 3.1, vous pouvez tirer parti des dernières mises à jour de sécurité et améliorations produit. 

Les applications de fonction épinglées à `~2.0` continuent de s’exécuter sur .NET Core 2.2, qui ne reçoit plus de mises à jour de sécurité ni aucune autre mise à jour. Pour plus d’informations, consultez [Considérations relatives à Functions v2.x](functions-dotnet-class-library.md#functions-v2x-considerations).   

## <a name="migrating-from-3x-to-4x-preview"></a><a name="migrating-from-3x-to-4x"></a>Migration de la version 3.x vers la version 4.x (préversion)

Azure Functions version 4.x (préversion) offre une compatibilité descendante forte avec la version 3.x. De nombreuses applications doivent être mises à niveau sans problème vers la version 4.x sans modification importante du code. Veillez néanmoins à effectuer des tests intensifs avant de changer la version principale dans les applications de production.

### <a name="upgrading-an-existing-app"></a>Mise à niveau d’une application existante

#### <a name="local-project"></a>Projet local

# <a name="c"></a>[C\#](#tab/csharp)
 
Pour mettre à jour une application .NET vers .NET 6 et Azure Functions 4.x, mettez à jour `TargetFramework` et `AzureFunctionsVersion` :

```xml
<TargetFramework>net6.0</TargetFramework>
<AzureFunctionsVersion>v4</AzureFunctionsVersion>
```

Veillez également à mettre à jour les packages NuGet référencés par votre application vers les dernières versions. Pour plus d’informations, consultez les [changements cassants](#breaking-changes-between-3x-and-4x).

##### <a name="net-6-in-process"></a>.NET 6 in-process

* [Microsoft.NET.Sdk.Functions](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions/) 4.0.0 ou ultérieur

##### <a name="net-6-isolated"></a>.NET 6 isolé

* [Microsoft.Azure.Functions.Worker](https://www.nuget.org/packages/Microsoft.Azure.Functions.Worker/) 1.5.2 ou ultérieur
* [Microsoft.Azure.Functions.Worker.Sdk](https://www.nuget.org/packages/Microsoft.Azure.Functions.Worker.Sdk/) 1.2.0 ou ultérieur

# <a name="java"></a>[Java](#tab/java)

Pour mettre à jour votre application Java avec Azure Functions 4.x, mettez à jour votre installation locale d’[Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools) vers la version 4.x et le [bundle d’extensions Azure Functions](functions-bindings-register.md#extension-bundles) de votre application vers la version 2.x ou ultérieure. Pour plus d’informations, consultez les [changements cassants](#breaking-changes-between-3x-and-4x).

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Pour mettre à jour votre application Node.js avec Azure Functions 4.x, mettez à jour votre installation locale d’[Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools) vers la version 4.x et le [bundle d’extensions Azure Functions](functions-bindings-register.md#extension-bundles) de votre application vers la version 2.x ou ultérieure. Pour plus d’informations, consultez les [changements cassants](#breaking-changes-between-3x-and-4x).

> [!NOTE]
> Node.js 10 et 12 ne sont pas pris en charge dans Azure Functions 4.x.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Pour mettre à jour votre application PowerShell avec Azure Functions 4.x, mettez à jour votre installation locale d’[Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools) vers la version 4.x et le [bundle d’extensions Azure Functions](functions-bindings-register.md#extension-bundles) de votre application vers la version 2.x ou ultérieure. Pour plus d’informations, consultez les [changements cassants](#breaking-changes-between-3x-and-4x).

> [!NOTE]
> PowerShell 6 n’est pas pris en charge dans Azure Functions 4.x.

# <a name="python"></a>[Python](#tab/python)

Pour mettre à jour votre application Python avec Azure Functions 4.x, mettez à jour votre installation locale d’[Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools) vers la version 4.x et le [bundle d’extensions Azure Functions](functions-bindings-register.md#extension-bundles) de votre application vers la version 2.x ou ultérieure. Pour plus d’informations, consultez les [changements cassants](#breaking-changes-between-3x-and-4x).

> [!NOTE]
> Python 3.6 n’est pas pris en charge dans Azure Functions 4.x.

---


#### <a name="azure"></a>Azure

Pour migrer une application de la version 3.x à la version 4.x, définissez le paramètre d’application `FUNCTIONS_EXTENSION_VERSION` sur `~4` à l’aide de la commande Azure CLI suivante :

```bash
az functionapp config appsettings set --settings FUNCTIONS_EXTENSION_VERSION=~4 -n <APP_NAME> -g <RESOURCE_GROUP_NAME>

# For Windows function apps only, also enable .NET 6.0 that is needed by the runtime
az functionapp config set --net-framework-version v6.0 -n <APP_NAME> -g <RESOURCE_GROUP_NAME>
```

### <a name="breaking-changes-between-3x-and-4x"></a>Changements cassants entre la version 3.x et la version 4.x

Voici quelques changements à prendre en considération avant de mettre à niveau une application de la version 3.x vers la version 4.x. Pour obtenir une liste complète, consultez les problèmes Azure Functions GitHub étiquetés [*Breaking Change: Approved*](https://github.com/Azure/azure-functions/issues?q=is%3Aissue+label%3A%22Breaking+Change%3A+Approved%22+is%3A%22closed+OR+open%22) (Changement cassant : Approuvé). D’autres changements sont attendus pendant la période de préversion. Abonnez-vous au flux [App Service Announcements](https://github.com/Azure/app-service-announcements/issues) (Annonces relatives à App Service) pour obtenir des mises à jour.

#### <a name="runtime"></a>Runtime

- Azure Functions Proxies n’est plus pris en charge dans la version 4.x. Il est recommandé d’utiliser [Gestion des API Azure](../api-management/import-function-app-as-api.md).

- La journalisation vers Stockage Azure à l’aide d’*AzureWebJobsDashboard* n’est plus prise en charge dans la version 4.x. Il est recommandé d’utiliser [Application Insights](./functions-monitoring.md). ([#1923](https://github.com/Azure/Azure-Functions/issues/1923))

- Azure Functions 4.x applique des [exigences de version minimale](https://github.com/Azure/Azure-Functions/issues/1987) pour les extensions. Effectuez une mise à niveau vers la version la plus récente des extensions concernées. Pour les langages autres que .NET, [effectuez une mise à niveau](./functions-bindings-register.md#extension-bundles) vers la version 2.x ou ultérieure du pack d’extension. ([#1987](https://github.com/Azure/Azure-Functions/issues/1987))

- Des délais d’expiration par défaut et maximum sont désormais appliqués dans les applications de fonction de consommation Linux 4.x. ([#1915](https://github.com/Azure/Azure-Functions/issues/1915))

- Application Insights n’est pas inclus par défaut dans Azure Functions 4.0.0.16714 (préversion). Il est disponible sous forme d’extension distincte. ([#2027](https://github.com/Azure/Azure-Functions/issues/2027))
    
    > [!NOTE]
    > Il s’agit d’un changement temporaire dans la version 4.0.0.16714. Une extension n’est pas demandée pour utiliser Application Insights dans les versions ultérieures. Si vous avez installé l’extension, supprimez-la de votre application quand vous utilisez Azure Functions version 4.0.1.16815 ou ultérieure.
    
    - Pour les applications .NET in-process, ajoutez le package d’extension [Microsoft.Azure.WebJobs.Extensions.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.ApplicationInsights/) à votre application de fonction.
    - Pour les applications .NET isolées :
        - Ajoutez le package d’extension [Microsoft.Azure.Functions.Worker.Extensions.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Azure.Functions.Worker.Extensions.ApplicationInsights/) à votre application de fonction.
        - Mettez à jour les packages [Microsoft.Azure.Functions.Worker](https://www.nuget.org/packages/Microsoft.Azure.Functions.Worker/) et [Microsoft.Azure.Functions.Worker.Sdk](https://www.nuget.org/packages/Microsoft.Azure.Functions.Worker.Sdk/) vers les versions les plus récentes.
    - Pour les autres langages, une mise à jour future des [packs d’extension Azure Functions](functions-bindings-register.md#extension-bundles) inclura l’extension Application Insights. Votre application utilisera automatiquement le nouveau pack quand il sera disponible. Tant que le bundle d’extension mis à jour n’est pas prêt, supprimez les deux paramètres d’application `APPINSIGHTS_INSTRUMENTATIONKEY` et `APPLICATIONINSIGHTS_CONNECTION_STRING` de votre application de fonction. Cela empêche l’échec du démarrage de l’hôte.

- Les applications de fonction qui partagent des comptes de stockage ont un échec de démarrage si leurs noms d’hôtes calculés sont identiques. Utilisez un compte de stockage distinct pour chaque application de fonction. ([#2049](https://github.com/Azure/Azure-Functions/issues/2049))

#### <a name="languages"></a>Langages

# <a name="c"></a>[C\#](#tab/csharp)

- Azure Functions 4.x prend en charge les applications in-process et isolées .NET 6.

- `InvalidHostServicesException` est désormais une erreur irrécupérable. ([#2045](https://github.com/Azure/Azure-Functions/issues/2045))

- `EnableEnhancedScopes` est activé par défaut. ([#1954](https://github.com/Azure/Azure-Functions/issues/1954))

- Supprimer `HttpClient` en tant que serveur inscrit. ([#1911](https://github.com/Azure/Azure-Functions/issues/1911))

# <a name="java"></a>[Java](#tab/java)

- Utilisez le chargeur de classe unique dans Java 11. ([#1997](https://github.com/Azure/Azure-Functions/issues/1997))

- Arrêtez le chargement des fichiers JAR de Worker dans Java 8. ([#1991](https://github.com/Azure/Azure-Functions/issues/1991))

# <a name="javascript"></a>[JavaScript](#tab/javascript)

- Node.js 10 et 12 ne sont pas pris en charge dans Azure Functions 4.x. ([#1999](https://github.com/Azure/Azure-Functions/issues/1999))

- La sérialisation de sortie dans les applications Node.js a été mise à jour pour résoudre les incohérences précédentes. ([#2007](https://github.com/Azure/Azure-Functions/issues/2007))

# <a name="powershell"></a>[PowerShell](#tab/powershell)

- PowerShell 6 n’est pas pris en charge dans Azure Functions 4.x. ([#1999](https://github.com/Azure/Azure-Functions/issues/1999))

- Le nombre de Threads par défaut a été mis à jour. Les fonctions qui ne sont pas thread-safe ou qui présentent une utilisation élevée de la mémoire peuvent être affectées. ([#1962](https://github.com/Azure/Azure-Functions/issues/1962))

# <a name="python"></a>[Python](#tab/python)

- Python 3.6 n’est pas pris en charge dans Azure Functions 4.x. ([#1999](https://github.com/Azure/Azure-Functions/issues/1999))

- Le transfert de mémoire partagée est activé par défaut. ([#1973](https://github.com/Azure/Azure-Functions/issues/1973))

- Le nombre de Threads par défaut a été mis à jour. Les fonctions qui ne sont pas thread-safe ou qui présentent une utilisation élevée de la mémoire peuvent être affectées. ([#1962](https://github.com/Azure/Azure-Functions/issues/1962))

---

## <a name="migrating-from-2x-to-3x"></a>Migration de 2.x vers 3.x

Azure Functions version 3.x offre une compatibilité descendante forte avec la version 2.x.  De nombreuses applications peuvent être mise à niveau sans problème vers la version 3.x, sans aucune modification du code. Bien que le passage à la version 3.x soit encouragé, effectuez néanmoins des tests intensifs avant de changer la version principale dans les applications de production.

### <a name="breaking-changes-between-2x-and-3x"></a>Changements cassants entre 2.x et 3.x

Voici les changements spécifiques au langage à prendre en considération avant de mettre à niveau une application 2.x vers la version 3.x.

# <a name="c"></a>[C\#](#tab/csharp)

Lorsque vous exécutez des fonctions de la bibliothèque de classes .NET, la principale différence entre les versions se trouve au niveau du runtime .NET Core. Functions version 2.x est conçu pour s’exécuter sur .NET Core 2.2, et la version 3.x est conçue pour s’exécuter sur .NET Core 3.1.  

* [Les opérations de serveur synchrones sont désactivées par défaut](/dotnet/core/compatibility/2.2-3.0#http-synchronous-io-disabled-in-all-servers).

* Changements cassants de .NET Core dans la [version 3.1](/dotnet/core/compatibility/3.1) et la [version 3.0](/dotnet/core/compatibility/3.0), qui ne sont pas relatifs à Functions, mais qui peuvent quand même affecter votre application.

>[!NOTE]
>En raison des problèmes de prise en charge de .NET Core 2.2, les applications de fonction épinglées à la version 2 (`~2`) s’exécutent essentiellement sur .NET Core 3.1. Pour plus d’informations, consultez [Mode de compatibilité Functions v2.x](functions-dotnet-class-library.md#functions-v2x-considerations).

# <a name="java"></a>[Java](#tab/java)

Aucun.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

* Les liaisons de sortie affectées via `context.done` ou des valeurs de retour se comportent désormais de la même façon qu’une définition dans `context.bindings`.

* L’objet de déclencheur de minuteur est en casse mixte au lieu de la casse Pascal

* Les fonctions déclenchées par Event Hub avec un `dataType` binaire reçoivent un tableau de `binary` au lieu d’un tableau de `string`.

* La charge utile des requêtes HTTP n’est plus accessible via `context.bindingData.req`.  Elle néanmoins toujours accessible en tant que paramètre d’entrée, `context.req`, et dans `context.bindings`.

* Node.js 8 n’est plus pris en charge et ne s’exécute pas dans les fonctions 3.x.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Aucun.

# <a name="python"></a>[Python](#tab/python)

Aucun.

---

## <a name="migrating-from-1x-to-later-versions"></a>Migration de la version 1.x vers les versions ultérieures

Vous pouvez choisir de migrer une application existante écrite pour utiliser la version 1.x du runtime pour qu’elle utilise à la place une version plus récente. La plupart des modifications que vous devez apporter sont liées au runtime du langage, par exemple des modifications de l’API C# entre .NET Framework 4.8 et .NET Core. Vous devez également vous assurer que le code et les bibliothèques sont compatibles avec le runtime de langage choisi. Enfin, veillez à noter toutes les modifications soulignées ci-après qui affectent les déclencheurs, liaisons et fonctionnalités. Pour une migration plus performante, vous devez créer une application de fonction dans une nouvelle version et porter le code de la fonction existante en version 1.x vers la nouvelle application.  

Bien qu’il soit possible de procéder à une mise à niveau « sur place » en mettant à jour manuellement la configuration de l’application, passer de la version 1.x à une version ultérieure implique certains changements cassants. Par exemple, en C#, l’objet de débogage passe de `TraceWriter` à `ILogger`. Quand vous créez un projet en version 3.x, vous commencez par des fonctions mises à jour basées sur les modèles de version 3.x les plus récents.

### <a name="changes-in-triggers-and-bindings-after-version-1x"></a>Changements dans les déclencheurs et les liaisons après la version 1.x

À compter de la version 2.x, vous devez installer dans votre application les extensions pour les déclencheurs et les liaisons spécifiques utilisés par les fonctions. La seule exception concerne les déclencheurs HTTP et de la minuterie, qui ne nécessitent aucune extension.  Pour plus d’informations, voir [Inscrire et installer des extensions de liaison](./functions-bindings-register.md).

Il convient également de noter quelques modifications dans *function.json* ou dans les attributs de la fonction entre les versions. Par exemple, la propriété `path` d’Event Hub est désormais `eventHubName`. Consultez le [tableau des liaisons existantes](#bindings). Il contient des liens vers de la documentation sur chaque liaison.

### <a name="changes-in-features-and-functionality-after-version-1x"></a>Changements apportés aux fonctionnalités après la version 1.x

Quelques fonctionnalités ont été supprimées, mises à jour ou remplacées après la version 1.x. Cette section détaille les changements que vous voyez dans les versions ultérieures après avoir utilisé la version 1.x.

Les modifications suivantes ont été apportées à la version 2.x :

* Les clés permettant d’appeler les points de terminaison HTTP sont toujours stockées et chiffrées dans le Stockage Blob Azure. Dans la version 1.x, les clés étaient stockées par défaut dans Azure Files. Au moment de mettre à niveau une application de la version 1.x à la version 2.x, les secrets existants qui se trouvent dans Azure Files sont réinitialisés.

* La version 2.x du runtime n’intègre aucune prise en charge des fournisseurs de webhooks. Cette modification a été apportée pour améliorer les performances. Vous pouvez continuer à utiliser des déclencheurs HTTP comme points de terminaison des webhooks.

* Le fichier de configuration d’hôte (host.json) doit être vide ou contenir la chaîne `"version": "2.0"`.

* Pour améliorer la surveillance, le tableau de bord WebJobs se trouvant dans le portail, qui a utilisé le paramètre [`AzureWebJobsDashboard`](functions-app-settings.md#azurewebjobsdashboard), est remplacé par Azure Application Insights, qui utilise le paramètre [`APPINSIGHTS_INSTRUMENTATIONKEY`](functions-app-settings.md#appinsights_instrumentationkey). Pour plus d’informations, consultez [Surveiller l’exécution des fonctions Azure](functions-monitoring.md).

* Toutes les fonctions d’une application de fonction doivent partager le même langage. Lorsque vous créez une application de fonction, vous devez choisir une pile d’exécution pour l’application. La pile d’exécution est spécifiée par la valeur [`FUNCTIONS_WORKER_RUNTIME`](functions-app-settings.md#functions_worker_runtime) dans les paramètres de l’application. Cette exigence a été ajoutée pour améliorer l’empreinte mémoire et le temps de démarrage. Lorsque vous développez en local, vous devez également inclure ce paramètre dans le [fichier local.settings.json](functions-develop-local.md#local-settings-file).

* Le délai d’expiration par défaut pour les fonctions dans un plan App Service est de 30 minutes. Vous pouvez modifier ce délai d’expiration manuellement et l’indiquer à nouveau comme étant illimité en utilisant le paramètre [functionTimeout](functions-host-json.md#functiontimeout) dans host.json.

* Les limitations d’accès concurrentiel HTTP sont implémentées par défaut pour les fonctions de plan Consommation, avec une valeur par défaut de 100 requêtes simultanées par instance. Vous pouvez configurer ces limitations dans le paramètre [`maxConcurrentRequests`](functions-host-json.md#http) du fichier host.json.

* En raison des [limitations de .NET Core](https://github.com/Azure/azure-functions-host/issues/3414), la prise en charge des fonctions de script F# (.fsx) a été supprimée. Les fonctions F# compilées (.fs) restent prises en charge.

* Le format d’URL des webhooks de déclencheur Event Grid a été remplacé par `https://{app}/runtime/webhooks/{triggerName}`.

### <a name="locally-developed-application-versions"></a>Versions d’une application développée en local

Vous pouvez effectuer les mises à jour suivantes sur les applications de fonction afin de changer localement les versions ciblées.

#### <a name="visual-studio-runtime-versions"></a>Versions du runtime Visual Studio

Dans Visual Studio, vous sélectionnez la version du runtime au moment de créer un projet. Azure Functions Tools pour Visual Studio prend en charge les trois versions majeures du runtime. La version appropriée est utilisée au moment du débogage et de la publication, en fonction des paramètres de projet. Les paramètres de version sont définis dans le fichier `.csproj`, dans les propriétés suivantes :

# <a name="version-4x-preview"></a>[Version 4.x (préversion)](#tab/v4)

```xml
<TargetFramework>net6.0</TargetFramework>
<AzureFunctionsVersion>v4</AzureFunctionsVersion>
```

> [!NOTE]
> Azure Functions 4.x nécessite que l’extension `Microsoft.NET.Sdk.Functions` soit au moins à la version `4.0.0`.

# <a name="version-3x"></a>[Version 3.x](#tab/v3)

```xml
<TargetFramework>netcoreapp3.1</TargetFramework>
<AzureFunctionsVersion>v3</AzureFunctionsVersion>
```

> [!NOTE]
> Azure Functions 3.x et .NET nécessitent que l’extension `Microsoft.NET.Sdk.Functions` soit au moins `3.0.0`.

# <a name="version-2x"></a>[Version 2.x](#tab/v2)

```xml
<TargetFramework>netcoreapp2.1</TargetFramework>
<AzureFunctionsVersion>v2</AzureFunctionsVersion>
```

# <a name="version-1x"></a>[Version 1.x](#tab/v1)

```xml
<TargetFramework>net472</TargetFramework>
<AzureFunctionsVersion>v1</AzureFunctionsVersion>
```
---

###### <a name="updating-2x-apps-to-3x-in-visual-studio"></a>Mise à jour des applications 2.x vers 3.x dans Visual Studio

Vous pouvez ouvrir une fonction existante ciblant 2.x et passer à 3.x en modifiant le fichier `.csproj` et en mettant à jour les valeurs ci-dessus.  Visual Studio gère automatiquement pour vous les versions du runtime en fonction des métadonnées du projet.  C’est néanmoins possible si vous n’ayez jamais créé d’application 3.x avant que Visual Studio ne dispose des modèles et du runtime pour 3.x sur votre machine.  Ceci peut se présenter soi-même avec une erreur comme « Aucun runtime Functions disponible ne correspond à la version spécifiée dans le projet ».  Pour récupérer les modèles et le runtime les plus récents, effectuez l’expérience de création d’un projet de fonction.  Quand vous accédez à l’écran de sélection de la version et du modèle, attendez que Visual Studio termine la récupération des modèles les plus récents. Une fois les derniers modèles .NET Core 3 disponibles et affichés, vous pouvez exécuter et déboguer les projets configurés pour la version 3.x.

> [!IMPORTANT]
> Les fonctions en version 3.x peuvent être développées dans Visual Studio seulement si vous utilisez Visual Studio version 16.4 ou ultérieure.

#### <a name="vs-code-and-azure-functions-core-tools"></a>VS Code et Azure Functions Core Tools

[Azure Functions Core Tools](functions-run-local.md) est utilisé pour développer la ligne de commande, ainsi que par l’[extension Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) pour Visual Studio Code. Pour développer pour la version 3.x, installez la version 3.x de Core Tools. Le développement en version 2.x nécessite la version 2.x de Core Tools, etc. Pour plus d’informations, voir [Installer Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools).

Pour un développement avec Visual Studio Code, vous devrez peut-être également mettre à jour le paramètre utilisateur pour `azureFunctions.projectRuntime` afin qu’il corresponde à la version des outils installés.  Ce paramètre met également à jour les modèles et les langages utilisés lors de la création de l’application de fonction.  Pour créer des applications dans `~3`, vous devez mettre à jour le paramètre utilisateur `azureFunctions.projectRuntime` sur `~3`.

![Paramètre de runtime de l’extension Azure Functions](./media/functions-versions/vs-code-version-runtime.png)

#### <a name="maven-and-java-apps"></a>Applications Maven et Java

Vous pouvez migrer des applications Java de la version 2.x vers la version 3.x en [installant la version 3.x de Core Tools](functions-run-local.md#install-the-azure-functions-core-tools) nécessaire pour une exécution locale.  Après avoir vérifié que votre application fonctionne correctement en local sur la version 3.x, mettez à jour le fichier `POM.xml` de l’application en changeant le paramètre `FUNCTIONS_EXTENSION_VERSION` en `~3`, comme dans l’exemple suivant :

```xml
<configuration>
    <resourceGroup>${functionResourceGroup}</resourceGroup>
    <appName>${functionAppName}</appName>
    <region>${functionAppRegion}</region>
    <appSettings>
        <property>
            <name>WEBSITE_RUN_FROM_PACKAGE</name>
            <value>1</value>
        </property>
        <property>
            <name>FUNCTIONS_EXTENSION_VERSION</name>
            <value>~3</value>
        </property>
    </appSettings>
</configuration>
```

## <a name="bindings"></a>Liaisons

À compter de la version 2.x, le runtime utilise un nouveau [modèle d’extensibilité de liaison](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview) qui présente les avantages suivants :

* Prise en charge pour les extensions de liaison de tiers.

* Découplage du runtime et des liaisons. Cela permet de gérer les versions des extensions de liaison et leur publication de façon indépendante. Vous pouvez, par exemple, choisir de mettre à niveau vers une version d’une extension qui s’appuie sur une version plus récente d’un kit de développement logiciel (SDK) sous-jacent.

* Un environnement d’exécution plus léger, où seules les liaisons en cours d’utilisation sont connues et chargées par le runtime.

À l’exception des déclencheurs HTTP et de la minuterie, toutes les liaisons doivent être explicitement ajoutées au projet d’application de fonction, ou enregistrées dans le portail. Pour plus d’informations, voir [Inscrire des extensions de liaison](./functions-bindings-expressions-patterns.md).

Le tableau suivant indique les liaisons prises en charge dans chaque version du runtime.

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

[!INCLUDE [Timeout Duration section](../../includes/functions-timeout-duration.md)]

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez les ressources suivantes :

* [Coder et tester Azure Functions localement](functions-run-local.md)
* [Procédure pour cibler des versions du runtime Azure Functions](set-runtime-version.md)
* [Notes de publication](https://github.com/Azure/azure-functions-host/releases)
