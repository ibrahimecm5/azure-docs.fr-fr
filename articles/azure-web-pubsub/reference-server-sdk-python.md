---
title: Référence – Kit de développement logiciel (SDK) de serveur Python pour Azure Web PubSub
description: La référence décrit le kit de développement logiciel (SDK) de serveur Python pour le service Azure Web PubSub.
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: conceptual
ms.date: 11/08/2021
ms.openlocfilehash: ba9ed9a90dc39ef51e7e1ee57940afb75b38f7f6
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2021
ms.locfileid: "131997511"
---
# <a name="python-server-sdk-for-azure-web-pubsub"></a>Kit de développement logiciel (SDK) de serveur Python pour Azure Web PubSub

Vous pouvez utiliser cette bibliothèque pour effectuer les opérations suivantes :

- Envoyer des messages aux hubs et aux groupes. 
- Envoyer des messages à des utilisateurs particuliers et à des connexions.
- Organiser les utilisateurs et les connexions en groupes.
- Fermer les connexions.
- Accorder, révoquer et vérifier des autorisations pour une connexion existante.

[Code source](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/webpubsub/azure-messaging-webpubsubservice) | [Package (Pypi)][package] | [Documentation de référence de l’API](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/webpubsub/azure-messaging-webpubsubservice) | [Documentation du produit][webpubsubservice_docs] |
[Exemples][samples_ref]

## <a name="get-started"></a>Bien démarrer

Installez le package comme suit :

```bash
python -m pip install azure-messaging-webpubsubservice==1.0.0b1
```

### <a name="prerequisites"></a>Prérequis

- Python 2.7, 3.6 ou version ultérieure est requis pour utiliser ce package.
- Un [abonnement Azure][azure_sub].
- Une instance de service Azure Web PubSub existante.

### <a name="authenticate-the-client"></a>Authentifier le client

Pour interagir avec le service Azure Web PubSub, vous devez créer une instance de la classe [`WebPubSubServiceClient`][webpubsubservice_client_class]. Pour vous authentifier auprès du service, vous devez passer une instance AzureKeyCredential avec un point de terminaison et une clé d’accès. Le point de terminaison et la clé d’accès se trouvent sur le portail Azure.

```python
>>> from azure.messaging.webpubsubservice import WebPubSubServiceClient
>>> from azure.core.credentials import AzureKeyCredential
>>> client = WebPubSubServiceClient(endpoint='<endpoint>', credential=AzureKeyCredential('somesecret'))
>>> client
<WebPubSubServiceClient endpoint:'<endpoint>'>
```

## <a name="examples"></a>Exemples

### <a name="sending-a-request"></a>Envoi d’une requête

```python
>>> from azure.messaging.webpubsubservice import WebPubSubServiceClient
>>> from azure.core.credentials import AzureKeyCredential
>>> from azure.messaging.webpubsubservice.rest import build_send_to_all_request
>>> client = WebPubSubServiceClient(endpoint='<endpoint>', credential=AzureKeyCredential('somesecret'))
>>> request = build_send_to_all_request('default', json={ 'Hello':  'webpubsub!' })
>>> request
<HttpRequest [POST], url: '/api/hubs/default/:send?api-version=2020-10-01'>
>>> response = client.send_request(request)
>>> response
<RequestsTransportResponse: 202 Accepted>
>>> response.status_code 
202
>>> with open('file.json', 'r') as f:
>>>    request = build_send_to_all_request('ahub', content=f, content_type='application/json')
>>>    response = client.send_request(request)
>>> print(response)
<RequestsTransportResponse: 202 Accepted>
```

## <a name="key-concepts"></a>Concepts clés

[!INCLUDE [Terms](includes/terms.md)]

## <a name="troubleshooting"></a>Résolution des problèmes

Ce Kit de développement logiciel (SDK) utilise la bibliothèque de journalisation standard de Python. Vous pouvez configurer la journalisation des informations de débogage de l’impression dans `stdout` ou tout emplacement de votre choix.

```python
import logging

logging.basicConfig(level=logging.DEBUG)
```

Avec cette configuration de journalisation, les détails de la requête et de la réponse HTTP sont imprimés dans `stdout`.

[webpubsubservice_docs]: ./index.yml
[azure_cli]: /cli/azure
[azure_sub]: https://azure.microsoft.com/free/
[webpubsubservice_client_class]: https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/webpubsub/azure-messaging-webpubsubservice/azure/messaging/webpubsubservice/__init__.py
[package]: https://pypi.org/project/azure-messaging-webpubsubservice/
[default_cred_ref]: https://aka.ms/azsdk-python-identity-default-cred-ref
[samples_ref]: https://github.com/Azure/azure-webpubsub/tree/main/samples/python

## <a name="next-steps"></a>Étapes suivantes

[!INCLUDE [next step](includes/include-next-step.md)]