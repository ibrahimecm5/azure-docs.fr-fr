---
title: 'Démarrage rapide : Ajouter la connexion Microsoft à une application web Python | Azure'
titleSuffix: Microsoft identity platform
description: Dans ce guide de démarrage rapide, découvrez comment une application web Python peut connecter des utilisateurs, obtenir un jeton d’accès auprès de la plateforme d’identités Microsoft et appeler l’API Microsoft Graph.
services: active-directory
author: abhidnya13
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: portal
ms.workload: identity
ms.date: 11/22/2021
ms.author: abpati
ms.custom: aaddev, devx-track-python, "scenarios:getting-started", "languages:Python", mode-api
ms.openlocfilehash: e108c50cd83126c1dc16f99287f3b41f48598c43
ms.sourcegitcommit: 34d047300d800cf6ff7d9dd3e573a0d785f61abc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/12/2022
ms.locfileid: "135925823"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-a-python-web-app"></a>Démarrage rapide : Ajouter la connexion avec Microsoft à une application web Python

Dans ce guide de démarrage rapide, vous téléchargez et exécutez un exemple de code qui montre comment une application web Python peut connecter des utilisateurs et obtenir un jeton d’accès pour appeler l’API Microsoft Graph. Les utilisateurs avec un compte Microsoft personnel ou un compte de n’importe quelle organisation Azure Active Directory (Azure AD) peuvent se connecter à l’application.

Consultez [Fonctionnement de l’exemple](#how-the-sample-works) pour obtenir une illustration.

## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Python 2.7+](https://www.python.org/downloads/release/python-2713) or [Python 3+](https://www.python.org/downloads/release/python-364/)
- [Flask](http://flask.pocoo.org/), [Flask-Session](https://pypi.org/project/Flask-Session/), [requests](https://requests.kennethreitz.org/en/master/)
- [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python)

#### <a name="step-1-configure-your-application-in-azure-portal"></a>Étape 1 : Configurer votre application dans le portail Azure

Pour que l’exemple de code de ce guide de démarrage rapide fonctionne :

1. Ajoutez une URL de réponse sous la forme `http://localhost:5000/getAToken`.
1. Créer un secret client.
1. Ajoutez la permission déléguée User.ReadBasic.All de l’API Microsoft Graph.

> [!div class="nextstepaction"]
> [Apporter ces modifications pour moi]()

> [!div class="alert alert-info"]
> ![Déjà configuré](./media/quickstart-v2-aspnet-webapp/green-check.png) Votre application est configurée avec cet attribut.

#### <a name="step-2-download-your-project"></a>Étape 2 : Télécharger votre projet

Téléchargez le projet et extrayez le fichier zip dans un dossier local proche du dossier racine (par exemple, **C:\Azure-Samples**)
> [!div class="nextstepaction"]
> [Téléchargez l’exemple de code](https://github.com/Azure-Samples/ms-identity-python-webapp/archive/master.zip).

> [!NOTE]
> `Enter_the_Supported_Account_Info_Here`

#### <a name="step-3-run-the-code-sample"></a>Étape 3 : Exécuter l’exemple de code

1. Vous devez installer la bibliothèque Python MSAL, le framework Flask, des Flask-Sessions pour la gestion des sessions côté serveur et la bibliothèque Requests avec PIP, comme suit :

    ```shell
    pip install -r requirements.txt
    ```

2. Exécutez `app.py` à partir de l’interpréteur de commandes ou de la ligne de commande :

    ```shell
    python app.py
    ```

   > [!IMPORTANT]
   > Cette application de démarrage rapide utilise un secret client pour s’identifier en tant que client confidentiel. Le secret client étant ajouté en texte brut à vos fichiers projet, il est recommandé, pour des raisons de sécurité, d’utiliser un certificat au lieu d’un secret client avant de considérer l’application comme application de production. Pour savoir plus en détails comment utiliser un certificat, voir [ces instructions](./active-directory-certificate-credentials.md).

## <a name="more-information"></a>Informations complémentaires

### <a name="how-the-sample-works"></a>Fonctionnement de l’exemple
![Fonctionnement de l’exemple d’application généré par ce guide de démarrage rapide](media/quickstart-v2-python-webapp/python-quickstart.svg)

### <a name="getting-msal"></a>Obtention de MSAL
MSAL est la bibliothèque utilisée pour connecter les utilisateurs et demander des jetons permettant d’accéder à une API protégée par la Plateforme d’identités Microsoft.
Pour ajouter MSAL Python à votre application, vous pouvez utiliser Pip.

```Shell
pip install msal
```

### <a name="msal-initialization"></a>Initialisation MSAL
Pour ajouter la référence à MSAL Python, ajoutez le code suivant au début du fichier où vous allez utiliser MSAL :

```Python
import msal
```

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Étapes suivantes

Découvrez-en plus sur les applications web qui connectent les utilisateurs dans notre série de scénarios en plusieurs parties.

> [!div class="nextstepaction"]
> [Scénario : application web qui connecte les utilisateurs](scenario-web-app-sign-user-overview.md)
