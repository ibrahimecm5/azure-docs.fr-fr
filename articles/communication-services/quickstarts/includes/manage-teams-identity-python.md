---
title: Fichier Include
description: Fichier Include
services: azure-communication-services
author: gistefan
manager: soricos
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 10/08/2021
ms.topic: include
ms.custom: include file
ms.author: gistefan
ms.openlocfilehash: 60c534a64bae703de30cd7aaddd1da31d27e750b
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131461143"
---
## <a name="set-up-prerequisites"></a>Configurer les prérequis

- [Python](https://www.python.org/downloads/) 2.7 ou 3.6+.

## <a name="set-up"></a>Configurer

### <a name="create-a-new-python-application"></a>Créer une application Python

1. Ouvrez votre fenêtre de terminal ou de commande, créez un répertoire pour votre application, puis accédez-y.

   ```console
   mkdir communication-access-tokens-quickstart && cd communication-access-tokens-quickstart
   ```

1. Utilisez un éditeur de texte pour créer un fichier appelé `exchange-communication-access-tokens.py` dans le répertoire racine du projet, puis ajoutez la structure du programme, notamment la gestion des exceptions de base. Dans les sections suivantes, vous ajouterez l’ensemble du code source de ce guide de démarrage rapide dans ce fichier.

   ```python
   import os
   from azure.communication.identity import CommunicationIdentityClient, CommunicationUserIdentifier

   try:
      print("Azure Communication Services - Access Tokens Quickstart")
      # Quickstart code goes here
   except Exception as ex:
      print("Exception:")
      print(ex)
   ```

### <a name="install-the-package"></a>Installer le package

Toujours dans le répertoire de l’application, installez le package du kit de développement logiciel (SDK) Azure Communication Services Identity pour Python à l’aide de la commande `pip install`.

```console
pip install azure-communication-identity
pip install msal
```

### <a name="step-1-receive-the-azure-ad-user-token-via-the-msal-library"></a>Étape 1 : Recevoir le jeton utilisateur Azure AD par le biais de la bibliothèque MSAL

La première étape du flux d’échange de jetons consiste à obtenir un jeton pour votre utilisateur Teams à l’aide de [Microsoft.Identity.Client](../../../active-directory/develop/reference-v2-libraries.md).

```python
from msal.application import PublicClientApplication

client_id = "<contoso_application_id>"
tenant_id = "<contoso_tenant_id>"
authority = "https://login.microsoftonline.com/%s" % tenant_id

app = PublicClientApplication(client_id, authority=authority)

scope = [ "https://auth.msft.communication.azure.com/VoIP" ]
teams_token_result = app.acquire_token_interactive(scope)
```

### <a name="step-2-initialize-the-communicationidentityclient"></a>Étape 2 : Initialiser CommunicationIdentityClient

Instanciez un `CommunicationIdentityClient` avec votre chaîne de connexion. Le code ci-dessous récupère la chaîne de connexion de la ressource à partir d’une variable d’environnement nommée `COMMUNICATION_SERVICES_CONNECTION_STRING`. Découvrez comment [gérer la chaîne de connexion de la ressource](../create-communication-resource.md#store-your-connection-string).

Ajoutez ce code dans le bloc `try` :

```python
# This code demonstrates how to fetch your connection string
# from an environment variable.
connection_string = os.environ["COMMUNICATION_SERVICES_CONNECTION_STRING"]

# Instantiate the identity client
client = CommunicationIdentityClient.from_connection_string(connection_string)
```

### <a name="step-3-exchange-the-azure-ad-user-token-for-the-teams-access-token"></a>Étape 3 : Échanger le jeton utilisateur Azure AD contre le jeton d’accès Teams

Utilisez la méthode `get_token_for_teams_user` afin d’émettre, pour l’utilisateur Teams, un jeton d’accès qui peut être utilisé avec les SDK Azure Communication Services.

```python
token_result = client.get_token_for_teams_user(teams_token_result['access_token'])
print("Token: " + token_result.token)
```

## <a name="run-the-code"></a>Exécuter le code

À partir d’une invite de console, accédez au répertoire contenant le fichier *exchange-teams-access-tokens.py*, puis exécutez la commande `python` suivante pour exécuter l’application.

```console
python ./exchange-communication-access-tokens.py
```
