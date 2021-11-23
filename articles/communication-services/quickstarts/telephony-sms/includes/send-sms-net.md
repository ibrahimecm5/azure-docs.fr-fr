---
title: Fichier include
description: Fichier include
services: azure-communication-services
author: peiliu
manager: rejooyan
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 06/30/2021
ms.topic: include
ms.custom: include file
ms.author: peiliu
ms.openlocfilehash: 12e16f8078d90ec1faae990a2e1dc4e6e7a42971
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132529478"
---
Commencez avec Azure Communication Services en utilisant le kit de développement logiciel (SDK) Communication Services SMS C# pour envoyer des SMS.

Le fait de suivre ce guide de démarrage rapide entraîne une petite dépense de quelques cents USD tout au plus dans votre compte Azure.

> [!NOTE]
> Vous trouverez le code finalisé pour ce guide de démarrage rapide sur [GitHub](https://github.com/Azure-Samples/communication-services-dotnet-quickstarts/tree/main/SendSMS).

## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Dernière version du [kit SDK .NET Core](https://dotnet.microsoft.com/download/dotnet-core) pour votre système d’exploitation.
- Une ressource Communication Services active et la chaîne de connexion. [Créez une ressource Communication Services](../../create-communication-resource.md).
- Un numéro de téléphone permettant de recevoir des SMS. [Obtenez un numéro de téléphone](../get-phone-number.md).

### <a name="prerequisite-check"></a>Vérification du prérequis

- Dans une fenêtre de terminal ou de commande, exécutez la commande `dotnet` pour vérifier que le kit de développement logiciel (SDK) .NET est installé.
- Pour voir les numéros de téléphone associés à votre ressource Communication Services, connectez-vous au [portail Azure](https://portal.azure.com/), localisez votre ressource Communication Services, puis ouvrez l’onglet **Phone Numbers** (Numéros de téléphone) dans le volet de navigation gauche.

## <a name="setting-up"></a>Configuration

### <a name="create-a-new-c-application"></a>Créer une application C#

Dans une fenêtre de console (par exemple cmd, PowerShell ou Bash), utilisez la commande `dotnet new` pour créer une application console avec le nom `SmsQuickstart`. Cette commande crée un projet C# « Hello World » simple avec un seul fichier source : **Program.cs**.

```console
dotnet new console -o SmsQuickstart
```

Remplacez votre répertoire par le dossier d’application que vous venez de créer, puis utilisez la commande `dotnet build` pour compiler votre application.

```console
cd SmsQuickstart
dotnet build
```

### <a name="install-the-package"></a>Installer le package

Toujours dans le répertoire de l’application, installez le kit de développement logiciel (SDK) Communication Services SMS pour .NET à l’aide de la commande `dotnet add package`.

```console
dotnet add package Azure.Communication.Sms --version 1.0.0
```

Ajoutez une directive `using` en haut du fichier **Program.cs** pour inclure l’espace de noms `Azure.Communication`.

```csharp

using System;
using System.Collections.Generic;

using Azure;
using Azure.Communication;
using Azure.Communication.Sms;

```

## <a name="object-model"></a>Modèle objet

Les classes et les interfaces suivantes gèrent certaines des principales fonctionnalités du kit de développement logiciel (SDK) Azure Communication Services SMS pour C#.

| Nom                                       | Description                                                                                                                                                       |
| ------------------------------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| SmsClient     | Cette classe est nécessaire pour toutes les fonctionnalités SMS. Vous l’instanciez avec vos informations d’abonnement et vous l’utilisez pour envoyer des SMS.                           |
| SmsSendOptions | Cette classe fournit des options permettant de configurer la création de rapports de remise. Si enable_delivery_report est défini sur True, un événement est émis quand la remise a réussi |
| SmsSendResult               | Cette classe contient le résultat du service SMS.                                          |

## <a name="authenticate-the-client"></a>Authentifier le client

 Ouvrez **Program.cs** dans un éditeur de texte, puis remplacez le corps de la méthode `Main` par du code permettant d’initialiser un `SmsClient` avec votre chaîne de connexion. Le code ci-dessous récupère la chaîne de connexion pour la ressource à partir d’une variable d’environnement nommée `COMMUNICATION_SERVICES_CONNECTION_STRING`. Découvrez comment [gérer la chaîne de connexion de la ressource](../../create-communication-resource.md#store-your-connection-string).


```csharp
// This code demonstrates how to fetch your connection string
// from an environment variable.
string connectionString = Environment.GetEnvironmentVariable("COMMUNICATION_SERVICES_CONNECTION_STRING");

SmsClient smsClient = new SmsClient(connectionString);
```

## <a name="send-a-11-sms-message"></a>Envoyer un message SMS 1:1

Pour envoyer un message SMS à un seul destinataire, appelez la fonction `Send` ou `SendAsync` à partir du SmsClient. Ajoutez ce code à la fin de la méthode `Main` dans **Program.cs** :

```csharp
SmsSendResult sendResult = smsClient.Send(
    from: "<from-phone-number>",
    to: "<to-phone-number>",
    message: "Hello World via SMS"
);

Console.WriteLine($"Sms id: {sendResult.MessageId}");
```
Vous devez remplacer `<from-phone-number>` par un numéro de téléphone permettant de recevoir des SMS associé à votre ressource Communication Services et `<to-phone-number>` par le numéro de téléphone auquel vous souhaitez envoyer un message.

> [!WARNING]
> Notez que les numéros de téléphone doivent être fournis au format standard international E.164 (par exemple , +14255550123). Le numéro de téléphone **From** peut également être un code court (par exemple : 23456).

## <a name="send-a-1n-sms-message-with-options"></a>Envoyer un message SMS 1:N avec des options
Pour envoyer un message SMS à une liste de destinataires, appelez la fonction `Send` ou `SendAsync` à partir du SmsClient avec une liste de numéros de téléphone de destinataire. Vous pouvez également transmettre des paramètres facultatifs pour spécifier si le rapport de remise doit être activé et pour définir des balises personnalisées.

```csharp
Response<IReadOnlyList<SmsSendResult>> response = smsClient.Send(
    from: "<from-phone-number>",
    to: new string[] { "<to-phone-number-1>", "<to-phone-number-2>" },
    message: "Weekly Promotion!",
    options: new SmsSendOptions(enableDeliveryReport: true) // OPTIONAL
    {
        Tag = "marketing", // custom tags
    });

IEnumerable<SmsSendResult> results = response.Value;
foreach (SmsSendResult result in results)
{
    Console.WriteLine($"Sms id: {result.MessageId}");
    Console.WriteLine($"Send Result Successful: {result.Successful}");
}
```

Vous devez remplacer `<from-phone-number>` par un numéro de téléphone permettant de recevoir des SMS et associé à votre ressource Communication Services, et `<to-phone-number-1>` et `<to-phone-number-2>` par le ou les numéros de téléphone auxquels vous souhaitez envoyer un message.

> [!WARNING]
> Notez que les numéros de téléphone doivent être fournis au format standard international E.164 (par exemple , +14255550123). Le numéro de téléphone **From** peut également être un code court (par exemple : 23456).

Le paramètre `enableDeliveryReport` est un paramètre facultatif que vous pouvez utiliser pour configurer la création de rapports de remise. C’est utile pour les scénarios où vous souhaitez émettre des événements quand des SMS sont remis. Consultez le guide de démarrage rapide [Gérer les événements SMS](../handle-sms-events.md) pour configurer la création de rapports de remise pour vos SMS.

`Tag` est utilisé pour appliquer une étiquette au rapport de remise.

## <a name="run-the-code"></a>Exécuter le code

Exécutez l’application à partir de votre répertoire d’application avec la commande `dotnet run`.

```console
dotnet run
```

## <a name="sample-code"></a>Exemple de code

Vous pouvez télécharger l’exemple d’application à partir de [GitHub](https://github.com/Azure-Samples/communication-services-dotnet-quickstarts/tree/main/SendSMS).
