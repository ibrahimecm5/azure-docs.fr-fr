---
title: Référence – Kit de développement logiciel (SDK) Java pour Azure Web PubSub
description: Cette référence décrit le Kit de développement logiciel (SDK) Java pour le service Azure Web PubSub.
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: conceptual
ms.date: 11/01/2021
ms.openlocfilehash: e004e32235330509051d635774b0ff685fb2eb4d
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132493047"
---
# <a name="azure-web-pubsub-service-client-library-for-java"></a>Bibliothèque de client du service Azure Web PubSub pour Java

Le [service Azure Web PubSub](https://aka.ms/awps/doc) est un service géré Azure qui aide les développeurs à créer facilement des applications web avec des fonctionnalités en temps réel et un modèle publication-abonnement. Tout scénario qui nécessite une messagerie de type publication-abonnement en temps réel entre un serveur et des clients ou entre des clients peut avoir recours au service Azure Web PubSub. Les fonctionnalités en temps réel traditionnelles qui demandent souvent d’interroger un serveur ou d’envoyer des requêtes HTTP peuvent aussi utiliser le service Azure Web PubSub.

Vous pouvez utiliser cette bibliothèque dans vos applications côté serveur pour gérer les connexions client WebSocket, comme indiqué dans le diagramme ci-dessous :

![Le diagramme de dépassement montre le dépassement de l’utilisation de la bibliothèque de client du service.](media/sdk-reference/service-client-overflow.png)

Utilisez cette bibliothèque pour effectuer les opérations suivantes :
- Envoyer des messages aux hubs et aux groupes. 
- Envoyer des messages à des utilisateurs particuliers et à des connexions.
- Organiser les utilisateurs et les connexions en groupes.
- Fermer les connexions
- Accorder, révoquer et vérifier des autorisations pour une connexion existante

Les détails des termes utilisés ici sont décrits dans la section [Concepts clés](#key-concepts).

[Code source][source_code] | [Documentation de référence de l’API][api] | [Documentation du produit][product_documentation] | [Exemples][samples_readme]

## <a name="getting-started"></a>Prise en main

### <a name="prerequisites"></a>Prérequis

- Un [Kit de développement Java (JDK)][jdk_link], version 8 ou ultérieure.
- [Abonnement Azure][azure_subscription]

### <a name="include-the-package"></a>Inclure le package

[//]: # ({x-version-update-start;com.azure:azure-messaging-webpubsub;current})

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-messaging-webpubsub</artifactId>
    <version>1.0.0-beta.6</version>
</dependency>
```

[//]: # ({x-version-update-end})

### <a name="create-a-webpubsubserviceclient-using-connection-string"></a>Créer un `WebPubSubServiceClient` à l’aide d’une chaîne de connexion

<!-- embedme ./src/samples/java/com/azure/messaging/webpubsub/ReadmeSamples.java#L21-L24 -->
```java
WebPubSubServiceClient webPubSubServiceClient = new WebPubSubServiceClientBuilder()
    .connectionString("{connection-string}")
    .hub("chat")
    .buildClient();
```

### <a name="create-a-webpubsubserviceclient-using-access-key"></a>Créer un `WebPubSubServiceClient` à l’aide d’une clé d’accès

<!-- embedme ./src/samples/java/com/azure/messaging/webpubsub/ReadmeSamples.java#L31-L35 -->
```java
WebPubSubServiceClient webPubSubServiceClient = new WebPubSubServiceClientBuilder()
    .credential(new AzureKeyCredential("{access-key}"))
    .endpoint("<Insert endpoint from Azure Portal>")
    .hub("chat")
    .buildClient();
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

Quand le client est connecté, il peut envoyer des messages à l’application en amont ou recevoir des messages de l’application en amont par le biais de la connexion WebSocket.

## <a name="examples"></a>Exemples

* [Diffuser un message à l’ensemble d’un hub](#broadcast-all "Diffuser un message à l’ensemble d’un hub")
* [Diffusez un message à un groupe](#broadcast-group "Diffusez un message à un groupe")
* [Envoyer un message à une connexion](#send-to-connection "Envoyer un message à une connexion")
* [Envoyer un message à un utilisateur](#send-to-user "Envoyer un message à un utilisateur")

<a name="broadcast-all"></a>

### <a name="broadcast-message-to-entire-hub"></a>Diffuser un message à l’ensemble d’un hub

<!-- embedme ./src/samples/java/com/azure/messaging/webpubsub/ReadmeSamples.java#L47-L47 -->
```java
webPubSubServiceClient.sendToAll("Hello world!", WebPubSubContentType.TEXT_PLAIN);
```

<a name="broadcast-group"></a>

### <a name="broadcast-message-to-a-group"></a>Diffusez un message à un groupe

<!-- embedme ./src/samples/java/com/azure/messaging/webpubsub/ReadmeSamples.java#L59-L59 -->
```java
webPubSubServiceClient.sendToGroup("java", "Hello Java!", WebPubSubContentType.TEXT_PLAIN);
```

<a name="send-to-connection"></a>

### <a name="send-message-to-a-connection"></a>Envoyer un message à une connexion

<!-- embedme ./src/samples/java/com/azure/messaging/webpubsub/ReadmeSamples.java#L71-L71 -->
```java
webPubSubServiceClient.sendToConnection("myconnectionid", "Hello connection!", WebPubSubContentType.TEXT_PLAIN);
```

<a name="send-to-user"></a>

### <a name="send-message-to-a-user"></a>Envoyer un message à un utilisateur
<!-- embedme ./src/samples/java/com/azure/messaging/webpubsub/ReadmeSamples.java#L83-L83 -->
```java
webPubSubServiceClient.sendToUser("Andy", "Hello Andy!", WebPubSubContentType.TEXT_PLAIN);
```

## <a name="troubleshooting"></a>Dépannage

### <a name="enable-client-logging"></a>Activer la journalisation du client
Vous pouvez définir la variable d’environnement `AZURE_LOG_LEVEL` pour afficher les instructions de journalisation figurant dans la bibliothèque de client. Par exemple, le paramètre `AZURE_LOG_LEVEL=2` affiche tous les messages d’information, d’avertissement et de journal des erreurs. Les niveaux de journal se trouvent ici : [niveaux de journal][log_levels].

### <a name="default-http-client"></a>Client HTTP par défaut
Toutes les bibliothèques de client utilisent par défaut le client HTTP Netty. L’ajout de la dépendance ci-dessus configure automatiquement la bibliothèque de client pour utiliser le client HTTP Netty. La configuration ou la modification du client HTTP sont détaillées dans le [wiki pour clients HTTP](https://github.com/Azure/azure-sdk-for-java/wiki/HTTP-clients).

### <a name="default-ssl-library"></a>Bibliothèque SSL par défaut
Toutes les bibliothèques de client utilisent par défaut la bibliothèque BoringSSL Tomcat native pour permettre des performances de niveau natif pour les opérations SSL. La bibliothèque BoringSSL est un fichier uber jar contenant des bibliothèques natives pour Linux/macOS/Windows. Elle offre de meilleures performances que l’implémentation SSL par défaut au sein du JDK. Pour plus d’informations, notamment sur la réduction de la taille des dépendances, consultez la section du wiki consacrée à l’[optimisation des performances][performance_tuning].

[!INCLUDE [next step](includes/include-next-step.md)]


<!-- LINKS -->

[azure_subscription]: https://azure.microsoft.com/free
[jdk_link]: /java/azure/jdk
[source_code]: https://github.com/Azure/azure-sdk-for-java/tree/main/sdk/webpubsub/azure-messaging-webpubsub/src
[product_documentation]: https://aka.ms/awps/doc
[samples_readme]: https://github.com/Azure/azure-sdk-for-java/blob/main/sdk/webpubsub/azure-messaging-webpubsub/src/samples/README.md
[log_levels]: https://github.com/Azure/azure-sdk-for-java/blob/main/sdk/core/azure-core/src/main/java/com/azure/core/util/logging/ClientLogger.java
[performance_tuning]: https://github.com/Azure/azure-sdk-for-java/wiki/Performance-Tuning
[cla]: https://cla.microsoft.com
[coc]: https://opensource.microsoft.com/codeofconduct/
[coc_faq]: https://opensource.microsoft.com/codeofconduct/faq/
[coc_contact]: mailto:opencode@microsoft.com
[api]: https://aka.ms/awps/sdk/java
