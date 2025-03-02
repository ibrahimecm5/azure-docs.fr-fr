---
title: Liaisons de service SignalR Azure Functions
description: Découvrez comment utiliser les liaisons de service SignalR dans Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/28/2019
ms.author: cshoe
ms.openlocfilehash: 34460fb0076c4cb666242d996add913cc2b74d22
ms.sourcegitcommit: 98308c4b775a049a4a035ccf60c8b163f86f04ca
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/30/2021
ms.locfileid: "113108636"
---
# <a name="signalr-service-bindings-for-azure-functions"></a>Liaisons de service SignalR pour Azure Functions

Cet ensemble d’articles explique comment authentifier et envoyer des messages en temps réel aux clients connectés à [Azure SignalR Service](https://azure.microsoft.com/services/signalr-service/) à l’aide des liaisons SignalR Service dans Azure Functions. Azure Functions prend en charge des liaisons d’entrée et de sortie pour le service SignalR.

| Action | Type |
|---------|---------|
| Prendre en charge les messages de SignalR Service | [Liaison de déclencheur](./functions-bindings-signalr-service-trigger.md) |
| Retourner l’URL du point de terminaison de service et le jeton d’accès | [Liaison d’entrée](./functions-bindings-signalr-service-input.md) |
| Envoyer des messages SignalR Service |[Liaison de sortie](./functions-bindings-signalr-service-output.md) |

## <a name="add-to-your-functions-app"></a>Ajouter à votre application de fonction

### <a name="functions-2x-and-higher"></a>Functions 2.x et versions ultérieures

Pour utiliser le déclencheur et les liaisons, vous devez référencer le package approprié. Le package NuGet est utilisé pour les bibliothèques de classes .NET, tandis que le bundle d’extensions est utilisé pour tous les autres types d’applications.

| Langage                                        | Ajouter via...                                   | Notes 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | L’installation du [package NuGet], version 3.x | |
| Script C#, Java, JavaScript, Python, PowerShell | L’inscription du [bundle d’extensions]          | Il est recommandé d’utiliser l’[extension Azure Tools] avec Visual Studio Code. |
| Script C# (en ligne uniquement dans le portail Azure)         | Ajout d’une liaison                            | Pour mettre à jour des extensions de liaison existantes sans avoir à republier votre application de fonction, voir [Mettre à jour vos extensions]. |

[Package NuGet]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SignalRService
[core tools]: ./functions-run-local.md
[Bundle d’extensions]: ./functions-bindings-register.md#extension-bundles
[Mettre à jour vos extensions]: ./functions-bindings-register.md
[Extension Azure Tools]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

Pour plus d’informations sur la configuration et l’utilisation de SignalR Service et Azure Functions ensemble, reportez-vous à [Développement et configuration Azure Functions avec Azure SignalR Service](../azure-signalr/signalr-concept-serverless-development-config.md).

### <a name="annotations-library-java-only"></a>Bibliothèque d’annotations (Java uniquement)

Pour utiliser les annotations SignalR Service dans les fonctions Java, vous devez ajouter une dépendance à l’artefact *azure-fonctions-java-bibliothèque-signalr* (version 1.0 ou version ultérieure) à votre fichier *pom.xml*.

```xml
<dependency>
    <groupId>com.microsoft.azure.functions</groupId>
    <artifactId>azure-functions-java-library-signalr</artifactId>
    <version>1.0.0</version>
</dependency>
```

## <a name="connection-string-settings"></a>Paramètres de la chaîne de connexion

Ajoutez la clé `AzureSignalRConnectionString` au fichier _host.json_ qui pointe vers le paramètre d’application avec votre chaîne de connexion. Pour un développement local, cette valeur peut exister dans le fichier _local.settings.json_.

## <a name="next-steps"></a>Étapes suivantes

- [Prendre en charge les messages de SignalR Service (liaison de déclencheur)](./functions-bindings-signalr-service-trigger.md)
- [Retourner l’URL du point de terminaison de service et le jeton d’accès (Liaison d’entrée)](./functions-bindings-signalr-service-input.md)
- [Envoyer des messages SignalR Service (Liaison de sortie)](./functions-bindings-signalr-service-output.md)