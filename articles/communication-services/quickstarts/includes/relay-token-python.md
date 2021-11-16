---
title: Fichier include
description: inclure fichier
services: azure-communication-services
author: rahulva
manager: shahen
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 07/30/2021
ms.topic: include
ms.custom: include file
ms.author: rahulva
ms.openlocfilehash: 35319f125578143abc2518d8037e0050dd9a6041
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2021
ms.locfileid: "131893216"
---
> [!NOTE]
> Vous trouverez le code finalisé pour ce guide de démarrage rapide sur [GitHub](https://github.com/Azure-Samples/communication-services-python-quickstarts/tree/main/relay-token-quickstart).

## <a name="prerequisites-for-python"></a>Prérequis pour Python

 Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Python](https://www.python.org/downloads/) 2.7 ou 3.6+.
- Une ressource Communication Services active et la chaîne de connexion. [Créez une ressource Communication Services](../create-communication-resource.md).

## <a name="setting-up"></a>Configuration

### <a name="create-a-new-python-application"></a>Créer une application Python

1. Ouvrez votre fenêtre de terminal ou de commande, créez un répertoire pour votre application, puis accédez-y.

   ```console
   mkdir access-tokens-quickstart && cd relay-tokens-quickstart
   ```

2. Utilisez un éditeur de texte pour créer un fichier appelé **issue-relay-tokens.py** dans le répertoire racine du projet, puis ajoutez la structure du programme, notamment la gestion des exceptions de base. Dans les sections suivantes, vous ajouterez l’ensemble du code source de ce guide de démarrage rapide dans ce fichier.

   ```python
    import os
    from azure.communication.networktraversal import CommunicationRelayClient
    from azure.identity import DefaultAzureCredential
    from azure.communication.identity import CommunicationIdentityClient
    
    try:
      print("Azure Communication Services - Access Relay Configuration  Quickstart")
      # Quickstart code goes here
    except Exception as ex:
      print("Exception:")
      print(ex)
   ```

### <a name="install-the-package"></a>Installer le package

Toujours dans le répertoire de l’application, installez le package du kit de développement logiciel (SDK) Azure Communication Services Identity pour Python à l’aide de la commande `pip install`.

```console
pip install azure-communication-identity
pip install azure.communication.networktraversal
```

## <a name="authenticate-the-client"></a>Authentifier le client

Instanciez un `CommunicationIdentityClient` avec la clé d’accès et le point de terminaison de votre ressource. Le code ci-dessous récupère la chaîne de connexion de la ressource à partir d’une variable d’environnement nommée `COMMUNICATION_SERVICES_CONNECTION_STRING`. Découvrez comment [gérer la chaîne de connexion de la ressource](../create-communication-resource.md#store-your-connection-string). 

Ajoutez ce code dans le bloc `try` :

```python

# You can find your endpoint and access token from your resource in the Azure Portal
connection_str = "endpoint=ENDPOINT;accessKey=KEY"
endpoint = "https://<RESOURCE_NAME>.communication.azure.com"

# To use Azure Active Directory Authentication (DefaultAzureCredential) make sure to have
# AZURE_TENANT_ID, AZURE_CLIENT_ID and AZURE_CLIENT_SECRET as env variables.
# We also need Identity client to get a User Identifier
identity_client = CommunicationIdentityClient(endpoint, DefaultAzureCredential())
relay_client = CommunicationRelayClient(endpoint, DefaultAzureCredential())

#You can also authenticate using your connection string
identity_client = CommunicationIdentityClient.from_connection_string(self.connection_string)
relay_client = CommunicationRelayClient.from_connection_string(self.connection_string)
```

## <a name="create-a-user-from-identity"></a>Créer un utilisateur à partir d’une identité 

Azure Communication Services gère un répertoire LID (Lightweight Identity Directory). Utilisez la méthode `create_user` pour créer une entrée, avec une valeur `Id` unique, dans le répertoire. Stockez l’identité reçue avec un mappage aux utilisateurs de votre application. Par exemple, en les stockant dans la base de données de votre serveur d’applications. L’identité sera demandée ultérieurement pour émettre des jetons d’accès.

```python
user = identity_client.create_user()
```

## <a name="getting-the-relay-configuration"></a>Obtention de la configuration de relais
Appeler le service de jeton Azure Communication pour échanger le jeton d’accès utilisateur pour un jeton de service TURN

```python
relay_configuration = relay_client.get_relay_configuration(user)

for iceServer in config.ice_servers:
    assert iceServer.username is not None
    print('Username: ' + iceServer.username)

    assert iceServer.credential is not None
    print('Credential: ' + iceServer.credential)
    
    assert iceServer.urls is not None
    for url in iceServer.urls:
        print('Url:' + url)
```     

## <a name="run-the-code"></a>Exécuter le code

À partir d’une invite de console, accédez au répertoire contenant le fichier *issue-relay-tokens.py*, puis exécutez la commande `python` suivante pour lancer l’application.

```console
python ./issue-relay-tokens.py
```
