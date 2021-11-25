---
title: Référence – Kit de développement logiciel (SDK) .NET pour Azure Web PubSub
description: Cette référence décrit le Kit de développement logiciel (SDK) .NET pour le service Azure Web PubSub.
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: conceptual
ms.date: 11/11/2021
ms.openlocfilehash: a3ec19ab07ebe74c618ce3c6af8981d442f6fc21
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132486645"
---
# <a name="azure-web-pubsub-service-client-library-for-net"></a>Bibliothèque de client du service Azure Web PubSub pour .NET

[Le service Azure Web PubSub](https://aka.ms/awps/doc) est un service managé Azure qui aide les développeurs à créer facilement des applications web avec des fonctionnalités en temps réel et un modèle publication/abonnement. Tout scénario qui nécessite une messagerie de type publication-abonnement en temps réel entre un serveur et des clients ou entre des clients peut avoir recours au service Azure Web PubSub. Les fonctionnalités en temps réel traditionnelles qui demandent souvent d’interroger un serveur ou d’envoyer des requêtes HTTP peuvent aussi utiliser le service Azure Web PubSub.

Vous pouvez utiliser cette bibliothèque dans vos applications côté serveur pour gérer les connexions client WebSocket, comme indiqué dans le diagramme ci-dessous :

![Le diagramme de dépassement montre le dépassement de l’utilisation de la bibliothèque de client du service.](media/sdk-reference/service-client-overflow.png)

Utilisez cette bibliothèque pour effectuer les opérations suivantes :
- Envoyer des messages aux hubs et aux groupes. 
- Envoyer des messages à des utilisateurs particuliers et à des connexions.
- Organiser les utilisateurs et les connexions en groupes.
- Fermer les connexions
- Accorder, révoquer et vérifier des autorisations pour une connexion existante

Les détails des termes utilisés ici sont décrits dans la section [Concepts clés](#key-concepts).
 
[Code source](https://github.com/Azure/azure-sdk-for-net/blob/main/sdk/webpubsub/Azure.Messaging.WebPubSub/src) |
[Package](https://www.nuget.org/packages/Azure.Messaging.WebPubSub) |
[Documentation de référence de l’API](https://aka.ms/awps/sdk/csharp) |
[Documentation du produit](https://aka.ms/awps/doc) |
[Exemples][samples_ref]

## <a name="getting-started"></a>Prise en main

### <a name="install-the-package"></a>Installer le package

Installer la bibliothèque de client pour [NuGet](https://www.nuget.org/) :

```dotnetcli
dotnet add package Azure.Messaging.WebPubSub
```

### <a name="prerequisites"></a>Prérequis

- Un [abonnement Azure][azure_sub].
- Une instance de service Azure Web PubSub existante.

### <a name="create-and-authenticate-a-webpubsubserviceclient"></a>Créez et authentifiez un`WebPubSubServiceClient`

Pour interagir avec le service, vous devez créer une instance de la classe `WebPubSubServiceClient`. Pour que cela soit possible, vous avez besoin de la chaîne de connexion ou d’une clé, à laquelle vous pouvez accéder dans le portail Azure.

```C# Snippet:WebPubSubAuthenticate
var serviceClient = new WebPubSubServiceClient(new Uri(endpoint), "some_hub", new AzureKeyCredential(key));
```

## <a name="key-concepts"></a>Concepts clés

### <a name="connection"></a>Connexion

Une connexion, également appelée client ou connexion client, représente une connexion WebSocket individuelle connectée au service Web PubSub. Une fois la connexion réussie, un ID de connexion unique est affecté à cette connexion par le service Web PubSub.

### <a name="hub"></a>Hub

Un hub est un concept logique pour un ensemble de connexions client. En règle générale, vous utilisez un hub dans un seul but, par exemple un hub de conversations ou un hub de notifications. Lorsqu’une connexion client est créée, elle se connecte à un hub et, pendant toute sa durée de vie, elle appartient à ce hub. Différentes applications peuvent partager un service Azure Web PubSub en utilisant différents noms de hubs.

### <a name="group"></a>Groupe

Un groupe est un sous-ensemble de connexions au hub. Vous pouvez ajouter une connexion cliente à un groupe, ou la supprimer du groupe, quand vous le souhaitez. Par exemple, quand un client rejoint une salle de conversation ou quand il la quitte, cette salle de conversation peut être considérée comme un groupe. Un client peut rejoindre plusieurs groupes, et un groupe peut contenir plusieurs clients.

### <a name="user"></a>Utilisateur

Les connexions à Web PubSub ne peuvent appartenir qu’à un seul utilisateur. Un utilisateur peut avoir plusieurs connexions. C’est le cas, par exemple, quand un seul utilisateur est connecté sur plusieurs appareils ou plusieurs onglets de navigateur.

### <a name="message"></a>Message

Quand un client est connecté, il peut envoyer des messages à l’application en amont ou recevoir des messages de l’application en amont par le biais de la connexion WebSocket.

## <a name="examples"></a>Exemples

### <a name="broadcast-a-text-message-to-all-clients"></a>Diffuser un message texte à tous les clients

```C# Snippet:WebPubSubHelloWorld
var serviceClient = new WebPubSubServiceClient(new Uri(endpoint), "some_hub", new AzureKeyCredential(key));

serviceClient.SendToAll("Hello World!");
```

### <a name="broadcast-a-json-message-to-all-clients"></a>Diffuser un message JSON à tous les clients

```C# Snippet:WebPubSubSendJson
var serviceClient = new WebPubSubServiceClient(new Uri(endpoint), "some_hub", new AzureKeyCredential(key));

serviceClient.SendToAll(RequestContent.Create(
        new
        {
            Foo = "Hello World!",
            Bar = 42
        }),
        ContentType.ApplicationJson);
```

### <a name="broadcast-a-binary-message-to-all-clients"></a>Diffuser un message binaire à tous les clients

```C# Snippet:WebPubSubSendBinary
var serviceClient = new WebPubSubServiceClient(new Uri(endpoint), "some_hub", new AzureKeyCredential(key));

Stream stream = BinaryData.FromString("Hello World!").ToStream();
serviceClient.SendToAll(RequestContent.Create(stream), ContentType.ApplicationOctetStream);
```

## <a name="troubleshooting"></a>Résolution des problèmes

### <a name="setting-up-console-logging"></a>Configuration de la journalisation de la console
Vous pouvez également [activer la journalisation de la console](https://github.com/Azure/azure-sdk-for-net/blob/main/sdk/core/Azure.Core/samples/Diagnostics.md#logging) facilement si vous souhaitez approfondir les requêtes que vous formulez sur le service.

## <a name="next-steps"></a>Étapes suivantes

[!INCLUDE [next step](includes/include-next-step.md)]


[azure_sub]: https://azure.microsoft.com/free/dotnet/
[samples_ref]: https://github.com/Azure/azure-sdk-for-net/blob/main/sdk/webpubsub/Azure.Messaging.WebPubSub/tests/Samples/
[awps_sample]: https://github.com/Azure/azure-webpubsub/tree/main/samples/csharp
