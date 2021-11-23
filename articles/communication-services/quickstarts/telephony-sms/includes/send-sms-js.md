---
title: Fichier include
description: Fichier include
services: azure-communication-services
author: bertong
manager: ankita
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 06/30/2021
ms.topic: include
ms.custom: include file
ms.author: bertong
ms.openlocfilehash: c63a3859f9ea2fcec88ee220c0abca8429d9b86e
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132529933"
---
Commencez avec Azure Communication Services en utilisant le kit de développement logiciel (SDK) Communication Services SMS JavaScript pour envoyer des SMS.

Le fait de suivre ce guide de démarrage rapide entraîne une petite dépense de quelques cents USD tout au plus dans votre compte Azure.

> [!NOTE]
> Vous trouverez le code finalisé pour ce guide de démarrage rapide sur [GitHub](https://github.com/Azure-Samples/communication-services-javascript-quickstarts/tree/main/send-sms).

## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Node.js](https://nodejs.org/) versions Active LTS et Maintenance LTS (8.11.1 et 10.14.1 recommandées).
- Une ressource Communication Services active et la chaîne de connexion. [Créez une ressource Communication Services](../../create-communication-resource.md).
- Un numéro de téléphone permettant de recevoir des SMS. [Obtenez un numéro de téléphone](../get-phone-number.md).

### <a name="prerequisite-check"></a>Vérification du prérequis

- Dans une fenêtre de terminal ou de commande, exécutez `node --version` pour vérifier que Node.js est installé.
- Pour voir les numéros de téléphone associés à votre ressource Communication Services, connectez-vous au [portail Azure](https://portal.azure.com/), localisez votre ressource Communication Services, puis ouvrez l’onglet **Phone Numbers** (Numéros de téléphone) dans le volet de navigation gauche.

## <a name="setting-up"></a>Configuration

### <a name="create-a-new-nodejs-application"></a>Créer une application Node.js

Pour commencer, ouvrez votre fenêtre de terminal ou de commande, créez un répertoire pour votre application, puis accédez-y.

```console
mkdir sms-quickstart && cd sms-quickstart
```

Exécutez `npm init -y` pour créer un fichier **package.json** avec les paramètres par défaut.

```console
npm init -y
```

Utilisez un éditeur de texte pour créer un fichier appelé **send-sms.js** dans le répertoire racine du projet. Dans les sections suivantes, vous ajouterez l’ensemble du code source de ce démarrage rapide dans ce fichier.

### <a name="install-the-package"></a>Installer le package

Utilisez la commande `npm install` pour installer le kit de développement logiciel (SDK) Azure Communication Services SMS pour JavaScript.

```console
npm install @azure/communication-sms --save
```

L’option `--save` liste la bibliothèque comme dépendance dans votre fichier **package.json**.

## <a name="object-model"></a>Modèle objet

Les classes et les interfaces suivantes gèrent certaines des principales fonctionnalités du kit de développement logiciel (SDK) Azure Communication Services SMS pour Node.js.

| Nom                                  | Description                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| SmsClient | Cette classe est nécessaire pour toutes les fonctionnalités SMS. Vous l’instanciez avec vos informations d’abonnement et vous l’utilisez pour envoyer des SMS. |
| SmsSendRequest | Cette interface est le modèle de génération de la demande par SMS (par exemple, configurer les numéros de téléphone d’origine et de destination, ainsi que le contenu du SMS). |
| SmsSendOptions | Cette interface fournit des options permettant de configurer la création de rapports de remise. Si `enableDeliveryReport` est défini sur `true`, un événement est émis quand la remise a réussi. |
| SmsSendResult               | Cette classe contient le résultat du service SMS.                                          |

## <a name="authenticate-the-client"></a>Authentifier le client

Importez le **SmsClient** à partir du kit de développement logiciel (SDK) et instanciez-le avec votre chaîne de connexion. Le code ci-dessous récupère la chaîne de connexion pour la ressource à partir d’une variable d’environnement nommée `COMMUNICATION_SERVICES_CONNECTION_STRING`. Découvrez comment [gérer la chaîne de connexion de la ressource](../../create-communication-resource.md#store-your-connection-string).

Créez un fichier nommé **send-sms.js**, ouvrez-le et ajoutez le code suivant :

```javascript
const { SmsClient } = require('@azure/communication-sms');

// This code demonstrates how to fetch your connection string
// from an environment variable.
const connectionString = process.env['COMMUNICATION_SERVICES_CONNECTION_STRING'];

// Instantiate the SMS client
const smsClient = new SmsClient(connectionString);
```

## <a name="send-a-1n-sms-message"></a>Envoyer un message SMS 1:N

Pour envoyer un message SMS à une liste de destinataires, appelez la fonction `send` à partir du SmsClient avec une liste de numéros de téléphone de destinataire (si vous souhaitez envoyer un message à un destinataire unique, n’incluez qu’un seul numéro dans la liste). Ajoutez ce code à la fin du fichier **send-sms.js** :

```javascript
async function main() {
  const sendResults = await smsClient.send({
    from: "<from-phone-number>",
    to: ["<to-phone-number-1>", "<to-phone-number-2>"],
    message: "Hello World 👋🏻 via SMS"
  });

  // individual messages can encounter errors during sending
  // use the "successful" property to verify
  for (const sendResult of sendResults) {
    if (sendResult.successful) {
      console.log("Success: ", sendResult);
    } else {
      console.error("Something went wrong when trying to send this message: ", sendResult);
    }
  }
}

main();
```
Vous devez remplacer `<from-phone-number>` par un numéro de téléphone permettant de recevoir des SMS et associé à votre ressource Communication Services, et `<to-phone-number-1>` et `<to-phone-number-2>` par le ou les numéros de téléphone auxquels vous souhaitez envoyer un message.

> [!WARNING]
> Notez que les numéros de téléphone doivent être fournis au format standard international E.164 (par exemple , +14255550123). Le numéro de téléphone **From** peut également être un code court (par exemple : 23456).

## <a name="send-a-1n-sms-message-with-options"></a>Envoyer un message SMS 1:N avec des options

Vous pouvez également transmettre un objet d’options pour spécifier si le rapport de remise doit être activé et pour définir des balises personnalisées.

```javascript

async function main() {
  const sendResults = await smsClient.send({
    from: "<from-phone-number>",
    to: ["<to-phone-number-1>", "<to-phone-number-2>"],
    message: "Weekly Promotion!"
  }, {
    //Optional parameters
    enableDeliveryReport: true,
    tag: "marketing"
  });

  // individual messages can encounter errors during sending
  // use the "successful" property to verify
  for (const sendResult of sendResults) {
    if (sendResult.successful) {
      console.log("Success: ", sendResult);
    } else {
      console.error("Something went wrong when trying to send this message: ", sendResult);
    }
  }
}

main();
```

Vous devez remplacer `<from-phone-number>` par un numéro de téléphone permettant de recevoir des SMS et associé à votre ressource Communication Services, et `<to-phone-number-1>` et `<to-phone-number-2>` par le ou les numéros de téléphone auxquels vous souhaitez envoyer un message.

> [!WARNING]
> Notez que les numéros de téléphone doivent être fournis au format standard international E.164 (par exemple , +14255550123). Le numéro de téléphone **From** peut également être un code court (par exemple : 23456).

Le paramètre `enableDeliveryReport` est un paramètre facultatif que vous pouvez utiliser pour configurer la création de rapports de remise. C’est utile pour les scénarios où vous souhaitez émettre des événements quand des SMS sont remis. Consultez le guide de démarrage rapide [Gérer les événements SMS](../handle-sms-events.md) pour configurer la création de rapports de remise pour vos SMS.
`tag` est un paramètre facultatif que vous pouvez utiliser pour appliquer une balise au rapport de remise.

## <a name="run-the-code"></a>Exécuter le code

Utilisez la commande `node` pour exécuter le code que vous avez ajouté au fichier **send-sms.js**.

```console

node ./send-sms.js

```
