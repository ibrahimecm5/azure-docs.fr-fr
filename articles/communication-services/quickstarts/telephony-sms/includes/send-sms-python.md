---
title: Fichier include
description: Fichier include
services: azure-communication-services
author: lakshmans
manager: ankita
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 06/30/2021
ms.topic: include
ms.custom: include file
ms.author: lakshmans
ms.openlocfilehash: c6fcc4c63b786af08c48450791a58b4f6f13a6ad
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132529473"
---
Commencez avec Azure Communication Services en utilisant le kit de développement logiciel (SDK) Communication Services SMS Python pour envoyer des SMS.

Le fait de suivre ce guide de démarrage rapide entraîne une petite dépense de quelques cents USD tout au plus dans votre compte Azure.

> [!NOTE]
> Vous trouverez le code finalisé pour ce guide de démarrage rapide sur [GitHub](https://github.com/Azure-Samples/communication-services-python-quickstarts/tree/main/send-sms-quickstart).

## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Python](https://www.python.org/downloads/) 2.7 ou 3.6+.
- Une ressource Communication Services active et la chaîne de connexion. [Créez une ressource Communication Services](../../create-communication-resource.md).
- Un numéro de téléphone permettant de recevoir des SMS. [Obtenez un numéro de téléphone](../get-phone-number.md).

### <a name="prerequisite-check"></a>Vérification du prérequis

- Dans une fenêtre de terminal ou de commande, exécutez la commande `python --version` pour vérifier que Python est installé.
- Pour voir les numéros de téléphone associés à votre ressource Communication Services, connectez-vous au [portail Azure](https://portal.azure.com/), localisez votre ressource Communication Services, puis ouvrez l’onglet **Phone Numbers** (Numéros de téléphone) dans le volet de navigation gauche.

## <a name="setting-up"></a>Configuration

### <a name="create-a-new-python-application"></a>Créer une application Python

Ouvrez votre fenêtre de terminal ou de commande, créez un répertoire pour votre application, puis accédez-y.

```console
mkdir sms-quickstart && cd sms-quickstart
```

Utilisez un éditeur de texte pour créer un fichier appelé **send-sms.py** dans le répertoire racine du projet, puis ajoutez la structure du programme, notamment la gestion des exceptions de base. Dans les sections suivantes, vous ajouterez l’ensemble du code source de ce guide de démarrage rapide dans ce fichier.

```python
import os
from azure.communication.sms import SmsClient

try:
    # Quickstart code goes here
except Exception as ex:
    print('Exception:')
    print(ex)
```

### <a name="install-the-package"></a>Installer le package

Toujours dans le répertoire de l’application, installez le kit de développement logiciel (SDK) Communication Services SMS pour Python à l’aide de la commande `pip install`.

```console
pip install azure-communication-sms
```

## <a name="object-model"></a>Modèle objet

Les classes et les interfaces suivantes gèrent certaines des principales fonctionnalités du kit de développement logiciel (SDK) Azure Communication Services SMS pour Python.

| Nom                                  | Description                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| SmsClient | Cette classe est nécessaire pour toutes les fonctionnalités SMS. Vous l’instanciez avec vos informations d’abonnement et vous l’utilisez pour envoyer des SMS.                                                                                                                 |
| SmsSendResult               | Cette classe contient le résultat du service SMS.                                          |

## <a name="authenticate-the-client"></a>Authentifier le client

Instanciez un **SmsClient** avec votre chaîne de connexion. Découvrez comment [gérer la chaîne de connexion de la ressource](../../create-communication-resource.md#store-your-connection-string).

```python
# Create the SmsClient object which will be used to send SMS messages
sms_client = SmsClient.from_connection_string(<connection_string>)
```
Par souci de simplicité, nous utilisons des chaînes de connexion dans ce démarrage rapide, mais, dans les environnements de production, nous recommandons l’utilisation de [principaux de service](../../../quickstarts/identity/service-principal.md).

## <a name="send-a-11-sms-message"></a>Envoi d’un message SMS 1:1

Pour envoyer un message SMS à un seul destinataire, appelez la méthode ```send``` à partir du **SmsClient** avec un numéro de téléphone de destinataire unique. Vous pouvez également transmettre des paramètres facultatifs pour spécifier si le rapport de remise doit être activé et pour définir des balises personnalisées. Ajoutez ce code à la fin du bloc `try` dans **send-sms.py** :

```python

# calling send() with sms values
sms_responses = sms_client.send(
    from_="<from-phone-number>",
    to="<to-phone-number>",
    message="Hello World via SMS",
    enable_delivery_report=True, # optional property
    tag="custom-tag") # optional property

```

Vous devez remplacer `<from-phone-number>` par un numéro de téléphone permettant de recevoir des SMS associé à votre service de communication et `<to-phone-number>` par le numéro de téléphone auquel vous souhaitez envoyer un message.

> [!WARNING]
> Notez que les numéros de téléphone doivent être fournis au format standard international E.164 (par exemple , +14255550123). Le numéro de téléphone **From** peut également être un code court (par exemple : 23456).

## <a name="send-a-1n-sms-message"></a>Envoyer un message SMS 1:N

Pour envoyer un message SMS à une liste de destinataires, appelez la méthode ```send``` à partir du **SmsClient** avec une liste de numéros de téléphone de destinataire. Vous pouvez également transmettre des paramètres facultatifs pour spécifier si le rapport de remise doit être activé et pour définir des balises personnalisées. Ajoutez ce code à la fin du bloc `try` dans **send-sms.py** :

```python

# calling send() with sms values
sms_responses = sms_client.send(
    from_="<from-phone-number>",
    to=["<to-phone-number-1>", "<to-phone-number-2>"],
    message="Hello World via SMS",
    enable_delivery_report=True, # optional property
    tag="custom-tag") # optional property

```

Vous devez remplacer `<from-phone-number>` par un numéro de téléphone permettant de recevoir des SMS associé à votre service de communication et `<to-phone-number-1>` `<to-phone-number-2>` par le ou les numéros de téléphone auxquels vous souhaitez envoyer un message.

> [!WARNING]
> Notez que les numéros de téléphone doivent être fournis au format standard international E.164 (par exemple , +14255550123). Le numéro de téléphone **From** peut également être un code court (par exemple : 23456).

## <a name="optional-parameters"></a>Paramètres facultatifs

Le paramètre `enable_delivery_report` est un paramètre facultatif que vous pouvez utiliser pour configurer la création de rapports de remise. C’est utile pour les scénarios où vous souhaitez émettre des événements quand des SMS sont remis. Consultez le guide de démarrage rapide [Gérer les événements SMS](../handle-sms-events.md) pour configurer la création de rapports de remise pour vos SMS.

Le paramètre `tag` est un paramètre facultatif que vous pouvez utiliser pour appliquer une balise au rapport de remise.

## <a name="run-the-code"></a>Exécuter le code
Exécutez l’application à partir de votre répertoire d’application avec la commande `python`.

```console
python send-sms.py
```

Le script Python complet doit se présenter comme suit :

```python

import os
from azure.communication.sms import SmsClient

try:
    # Create the SmsClient object which will be used to send SMS messages
    sms_client = SmsClient.from_connection_string("<connection string>")
    # calling send() with sms values
    sms_responses = sms_client.send(
       from_="<from-phone-number>",
       to="<to-phone-number>",
       message="Hello World via SMS",
       enable_delivery_report=True, # optional property
       tag="custom-tag") # optional property

except Exception as ex:
    print('Exception:')
    print(ex)
```
