---
title: Référence – Kit de développement logiciel (SDK) de serveur Python pour Azure Web PubSub
description: La référence décrit le kit de développement logiciel (SDK) de serveur Python pour le service Azure Web PubSub.
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: conceptual
ms.date: 11/08/2021
ms.openlocfilehash: 256dbd5e988f95f8e4df7ea6fd1c83b88bc45d46
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132487425"
---
# <a name="azure-web-pubsub-service-client-library-for-python"></a>Bibliothèque de client du service Azure Web PubSub pour Python

Le [service Azure Web PubSub](https://aka.ms/awps/doc) est un service géré Azure qui aide les développeurs à créer facilement des applications web avec des fonctionnalités en temps réel et un modèle publication-abonnement. Tout scénario qui nécessite une messagerie de type publication-abonnement en temps réel entre un serveur et des clients ou entre des clients peut avoir recours au service Azure Web PubSub. Les fonctionnalités en temps réel traditionnelles qui demandent souvent d’interroger un serveur ou d’envoyer des requêtes HTTP peuvent aussi utiliser le service Azure Web PubSub.

Vous pouvez utiliser cette bibliothèque dans vos applications côté serveur pour gérer les connexions client WebSocket, comme indiqué dans le diagramme ci-dessous :

![Le diagramme de dépassement montre le dépassement de l’utilisation de la bibliothèque de client du service.](media/sdk-reference/service-client-overflow.png)

Utilisez cette bibliothèque pour effectuer les opérations suivantes :
- Envoyer des messages aux hubs et aux groupes.
- Envoyer des messages à des utilisateurs particuliers et à des connexions.
- Organiser les utilisateurs et les connexions en groupes.
- Fermer les connexions
- Accorder, révoquer et vérifier des autorisations pour une connexion existante

[Code source](https://github.com/Azure/azure-sdk-for-python/blob/main/sdk/webpubsub/azure-messaging-webpubsubservice) | [Package (Pypi)][package] | [Documentation de référence de l’API](https://github.com/Azure/azure-sdk-for-python/tree/main/sdk/webpubsub/azure-messaging-webpubsubservice) | [Documentation du produit][webpubsubservice_docs]

## <a name="_disclaimer_"></a>_Clause d’exclusion de responsabilité_

_La prise en charge des packages Python du Kit de développement logiciel (SDK) Azure pour Python 2.7 se termine le 1er janvier 2022. Pour obtenir plus d’informations et des questions, reportez-vous à la page https://github.com/Azure/azure-sdk-for-python/issues/20691._

## <a name="getting-started"></a>Prise en main

### <a name="prerequisites"></a>Prérequis

- Python 2.7 ou 3.6 ou version ultérieure est requis pour utiliser ce package.
- Pour utiliser ce package, vous avez besoin d’un [abonnement Azure][azure_sub] et d’une [instance de service Azure Web PubSub][webpubsubservice_docs].
- Une instance de service Azure Web PubSub existante.

### <a name="1-install-the-package"></a>1. Installer le package

```bash
python -m pip install azure-messaging-webpubsubservice
```

### <a name="2-create-and-authenticate-a-webpubsubserviceclient"></a>2. Créer et authentifier un client WebPubSubService

Vous pouvez authentifier `WebPubSubServiceClient` à l’aide d’une [chaîne de connexion][connection_string] :

```python
>>> from azure.messaging.webpubsubservice import WebPubSubServiceClient

>>> service = WebPubSubServiceClient.from_connection_string(connection_string='<connection_string>', hub='hub')
```

Ou à l’aide du point de terminaison de service et de la clé d’accès :

```python
>>> from azure.messaging.webpubsubservice import WebPubSubServiceClient
>>> from azure.core.credentials import AzureKeyCredential

>>> service = WebPubSubServiceClient(endpoint='<endpoint>', hub='hub', credential=AzureKeyCredential("<access_key>"))
```

Ou en utilisant [Azure Active Directory][aad_doc] :
1. Installez [`azure-identity`][azure_identity_pip] à l’aide de [pip][pip]
2. Suivez le document pour [activer l’authentification AAD sur votre ressource Webpubsub][aad_doc]
3. Mettez à jour le code pour utiliser [DefaultAzureCredential][default_azure_credential]

    ```python
    >>> from azure.messaging.webpubsubservice import WebPubSubServiceClient
    >>> from azure.identity import DefaultAzureCredential
    >>> service = WebPubSubServiceClient(endpoint='<endpoint>', hub='hub', credential=DefaultAzureCredential())
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

### <a name="broadcast-messages-in-json-format"></a>Diffuser des messages au format JSON

```python
>>> from azure.messaging.webpubsubservice import WebPubSubServiceClient

>>> service = WebPubSubServiceClient.from_connection_string('<connection_string>', hub='hub1')
>>> service.send_to_all(message = {
        'from': 'user1',
        'data': 'Hello world'
    })
```

Le client WebSocket recevra le texte JSON sérialisé : `{"from": "user1", "data": "Hello world"}`.

### <a name="broadcast-messages-in-plain-text-format"></a>Diffuser des messages au format texte brut

```python
>>> from azure.messaging.webpubsubservice import WebPubSubServiceClient
>>> service = WebPubSubServiceClient.from_connection_string('<connection_string>', hub='hub1')
>>> service.send_to_all(message = 'Hello world', content_type='text/plain')
```

Le client WebSocket recevra le texte suivant : `Hello world`.

### <a name="broadcast-messages-in-binary-format"></a>Diffuser des messages au format binaire

```python
>>> import io
>>> from azure.messaging.webpubsubservice import WebPubSubServiceClient
>>> service = WebPubSubServiceClient.from_connection_string('<connection_string>', hub='hub')
>>> service.send_to_all(message=io.StringIO('Hello World'), content_type='application/octet-stream')
```
Le client WebSocket recevra le texte binaire suivant : `b'Hello world'`.

## <a name="troubleshooting"></a>Résolution des problèmes

### <a name="logging"></a>Journalisation

Ce kit de développement logiciel (SDK) utilise la bibliothèque de journalisation standard Python.
Vous pouvez configurer la journalisation pour qu’elle imprime les informations de débogage dans le stdout ou tout emplacement de votre choix.

```python
import sys
import logging
from azure.identity import DefaultAzureCredential
>>> from azure.messaging.webpubsubservice import WebPubSubServiceClient

# Create a logger for the 'azure' SDK
logger = logging.getLogger('azure')
logger.setLevel(logging.DEBUG)

# Configure a console output
handler = logging.StreamHandler(stream=sys.stdout)
logger.addHandler(handler)

endpoint = "<endpoint>"
credential = DefaultAzureCredential()

# This WebPubSubServiceClient will log detailed information about its HTTP sessions, at DEBUG level
service = WebPubSubServiceClient(endpoint=endpoint, hub='hub', credential=credential, logging_enable=True)
```

De la même façon, `logging_enable` peut activer la journalisation détaillée pour un seul appel, même si elle n’est pas activée pour le WebPubSubServiceClient :

```python
result = service.send_to_all(..., logging_enable=True)
```

Les détails de la requête et de la réponse HTTP sont imprimés dans StdOut avec cette configuration de journalisation.

## <a name="next-steps"></a>Étapes suivantes

Consultez d’[autres exemples ici][samples].

## <a name="contributing"></a>Contribution

Ce projet accepte les contributions et les suggestions. La plupart des contributions vous demandent d’accepter un contrat de licence de contribution (CLA) déclarant que vous avez le droit de nous accorder, et que vous nous accordez réellement, les droits d’utilisation de votre contribution.
Pour plus d’informations, visitez https://cla.microsoft.com.

Quand vous envoyez une demande de tirage (pull request), un bot CLA détermine automatiquement si vous devez fournir un contrat CLA et agrémenter la demande de tirage de façon appropriée (par exemple, avec une étiquette ou un commentaire). Suivez simplement les instructions fournies par le bot. Vous ne devez effectuer cette opération qu’une seule fois sur tous les dépôts utilisant notre contrat CLA.

Ce projet a adopté le [Code de conduite Open Source de Microsoft][code_of_conduct]. Pour plus d’informations, consultez la FAQ sur le code de conduite ou contactez opencode@microsoft.com pour toute question ou tout commentaire supplémentaire.

<!-- LINKS -->
[webpubsubservice_docs]: https://aka.ms/awps/doc
[azure_cli]: /cli/azure
[azure_sub]: https://azure.microsoft.com/free/
[package]: https://pypi.org/project/azure-messaging-webpubsubservice/
[default_cred_ref]: https://aka.ms/azsdk-python-identity-default-cred-ref
[cla]: https://cla.microsoft.com
[code_of_conduct]: https://opensource.microsoft.com/codeofconduct/
[coc_faq]: https://opensource.microsoft.com/codeofconduct/faq/
[coc_contact]: mailto:opencode@microsoft.com
[azure_identity_credentials]: https://github.com/Azure/azure-sdk-for-python/tree/main/sdk/identity/azure-identity#credentials
[azure_identity_pip]: https://pypi.org/project/azure-identity/
[default_azure_credential]: https://github.com/Azure/azure-sdk-for-python/tree/main/sdk/identity/azure-identity#defaultazurecredential
[pip]: https://pypi.org/project/pip/
[enable_aad]: howto-develop-create-instance.md
[api_key]: howto-websocket-connect.md#authorization
[connection_string]: howto-websocket-connect.md#authorization
[azure_portal]: howto-develop-create-instance.md
[azure-key-credential]: https://aka.ms/azsdk-python-core-azurekeycredential
[aad_doc]: howto-authorize-from-application.md
[samples]: https://github.com/Azure/azure-sdk-for-python/tree/main/sdk/webpubsub/azure-messaging-webpubsubservice/samples
