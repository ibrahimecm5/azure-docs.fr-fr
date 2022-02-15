---
title: 'Démarrage rapide : Appeler Microsoft Graph à partir d’un démon Python | Azure'
titleSuffix: Microsoft identity platform
description: Dans ce guide de démarrage rapide, vous allez découvrir comment un processus Python peut obtenir un jeton d’accès et appeler une API protégée par une plateforme d’identités Microsoft en utilisant la propre identité de l’application
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: portal
ms.workload: identity
ms.date: 01/10/2022
ROBOTS: NOINDEX
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, devx-track-python, "scenarios:getting-started", "languages:Python", mode-api
ms.openlocfilehash: 5ccacf12ca8e250201d3d97ce4df188037af76e2
ms.sourcegitcommit: 8d56da997b0b3ab07f91f6bef0c5661847758c4a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/05/2022
ms.locfileid: "138039362"
---
# <a name="quickstart-acquire-a-token-and-call-microsoft-graph-api-from-a-python-console-app-using-apps-identity"></a>Démarrage rapide : acquérir un jeton et appeler l’API Microsoft Graph à partir d’une application console Python à l’aide de l’identité de l’application

> [!div renderon="docs"]
> Bienvenue ! Ce n’est probablement pas la page que vous attendiez. Pendant que nous travaillons sur un correctif, ce lien devrait vous permettre d’accéder au bon article :
> 
> > [Démarrage rapide : application console Python qui appelle une API](console-app-quickstart.md?pivots=devlang-python)
> 
> Nous vous prions de nous excuser pour le désagrément et nous vous remercions de votre patience.

> [!div renderon="portal" class="sxs-lookup"]
> Dans ce guide de démarrage rapide, vous téléchargez et exécutez un exemple de code qui montre comment une application Python peut obtenir un jeton d’accès à l’aide de l’identité de l’application pour appeler l’API Microsoft Graph et afficher une [liste des utilisateurs](/graph/api/user-list) dans l’annuaire. L’exemple de code montre comment un travail sans assistance ou un service Windows peut s’exécuter avec l’identité d’une application, au lieu de l’identité d’un utilisateur. 
> 
> ## <a name="prerequisites"></a>Prérequis
> 
> Pour exécuter cet exemple, vous avec besoin de ce qui suit :
> 
> - [Python 2.7+](https://www.python.org/downloads/release/python-2713) or [Python 3+](https://www.python.org/downloads/release/python-364/)
> - [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python)
> 
> > [!div class="sxs-lookup"]
> ### <a name="download-and-configure-the-quickstart-app"></a>Télécharger et configurer l’application de démarrage rapide
> 
> #### <a name="step-1-configure-your-application-in-azure-portal"></a>Étape 1 : Configurer votre application dans le portail Azure
> Pour que l’exemple de code de ce guide de démarrage rapide fonctionne, créez un secret client et ajoutez l’autorisation d’application **User.Read.All** de l’API Graph.
> > [!div class="nextstepaction"]
> > [Apporter ces modifications pour moi]()
> 
> > [!div class="alert alert-info"]
> > ![Déjà configuré](media/quickstart-v2-netcore-daemon/green-check.png) Votre application est configurée avec ces attributs.
> 
> #### <a name="step-2-download-the-python-project"></a>Étape 2 : Télécharger le projet Python
> 
> > [!div class="sxs-lookup nextstepaction"]
> > [Téléchargez l’exemple de code](https://github.com/Azure-Samples/ms-identity-python-daemon/archive/master.zip).
> 
> > [!div class="sxs-lookup"]
> > > [!NOTE]
> > > `Enter_the_Supported_Account_Info_Here`
> 
> #### <a name="step-3-admin-consent"></a>Étape 3 : Consentement de l’administrateur
> 
> Si vous essayez d’exécuter l’application à ce stade, vous recevez l’erreur *HTTP 403 - Interdit* : `Insufficient privileges to complete the operation`. Cette erreur se produit parce que toute *autorisation d’application uniquement* nécessite le consentement de l’administrateur. Autrement dit, un administrateur général de votre annuaire doit donner son consentement à votre application. Sélectionnez l’une des options ci-dessous en fonction de votre rôle :
> 
> ##### <a name="global-tenant-administrator"></a>Administrateur de locataires général
> 
> Si vous êtes administrateur général, accédez à la page **Autorisations de l’API** et sélectionnez **Accorder le consentement administrateur pour Entrer_le_nom_du_locataire_ici**.
> > [!div id="apipermissionspage"]
> > [Accéder à la page Autorisations de l’API]()
> 
> ##### <a name="standard-user"></a>Utilisateur standard
> 
> Si vous êtes un utilisateur standard de votre locataire, demandez à un administrateur général de vous accorder le consentement administrateur pour votre application. Pour ce faire, donnez l’URL suivante à votre administrateur :
> 
> ```url
> https://login.microsoftonline.com/Enter_the_Tenant_Id_Here/adminconsent?client_id=Enter_the_Application_Id_Here
> ```
> 
> 
> #### <a name="step-4-run-the-application"></a>Étape 4 : Exécution de l'application
> 
> Vous devez installer les dépendances de cet exemple une seule fois.
> 
> ```console
> pip install -r requirements.txt
> ```
> 
> Ensuite, exécutez l’application via l’invite de commandes ou la console :
> 
> ```console
> python confidential_client_secret_sample.py parameters.json
> ```
> 
> Vous devriez voir sur la console sortie un fragment Json représentant une liste d’utilisateurs dans votre annuaire Azure Active Directory.
> 
> > [!IMPORTANT]
> > Cette application de démarrage rapide utilise un secret client pour s’identifier en tant que client confidentiel. Le secret client étant ajouté en texte brut à vos fichiers projet, il est recommandé, pour des raisons de sécurité, d’utiliser un certificat au lieu d’un secret client avant de considérer l’application comme application de production. Pour plus d’informations sur la façon d’utiliser un certificat, consultez [ces instructions](https://github.com/Azure-Samples/ms-identity-python-daemon/blob/master/2-Call-MsGraph-WithCertificate/README.md) dans le dépôt GitHub dédié à cet exemple, mais dans le second dossier **2-Call-MsGraph-WithCertificate**.
> 
> ## <a name="more-information"></a>Informations complémentaires
> 
> ### <a name="msal-python"></a>MSAL Python
> 
> [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python) est la bibliothèque utilisée pour connecter les utilisateurs et demander des jetons permettant d’accéder à une API protégée par la plateforme d’identités Microsoft. Comme vous l’avez vu, ce guide de démarrage rapide demande des jetons à l’aide de l’identité de l’application au lieu d’autorisations déléguées. Le flux d’authentification utilisé dans ce cas est désigné sous le terme de *[flux d’informations d’identification du client OAuth](v2-oauth2-client-creds-grant-flow.md)* . Pour plus d’informations sur l’utilisation de MSAL Python avec des applications démon, voir [cet article](scenario-daemon-overview.md).
> 
>  Vous pouvez installer MSAL Python en exécutant la commande pip suivante.
> 
> ```powershell
> pip install msal
> ```
> 
> ### <a name="msal-initialization"></a>Initialisation MSAL
> 
> Vous pouvez ajouter la référence de MSAL en ajoutant le code suivant :
> 
> ```Python
> import msal
> ```
> 
> Ensuite, initialisez MSAL à l’aide du code suivant :
> 
> ```Python
> app = msal.ConfidentialClientApplication(
>     config["client_id"], authority=config["authority"],
>     client_credential=config["secret"])
> ```
> 
> > | Où : |Description |
> > |---------|---------|
> > | `config["secret"]` | Est le secret client créé pour l’application dans le portail Azure. |
> > | `config["client_id"]` | Est l’**ID d’application (client)** de l’application inscrite dans le portail Azure. Vous pouvez retrouver cette valeur dans la page **Vue d’ensemble** de l’application dans le portail Azure. |
> > | `config["authority"]`    | Point de terminaison STS pour l’utilisateur à authentifier. Généralement `https://login.microsoftonline.com/{tenant}` pour un cloud public, où {tenant} est le nom ou l’ID de votre locataire.|
> 
> Pour plus d’informations, consultez la [documentation de référence sur `ConfidentialClientApplication`](https://msal-python.readthedocs.io/en/latest/#confidentialclientapplication).
> 
> ### <a name="requesting-tokens"></a>Demande de jetons
> 
> Pour demander un jeton à l’aide de l’identité d’application, utilisez la méthode `AcquireTokenForClient` :
> 
> ```Python
> result = None
> result = app.acquire_token_silent(config["scope"], account=None)
> 
> if not result:
>     logging.info("No suitable token exists in cache. Let's get a new one from AAD.")
>     result = app.acquire_token_for_client(scopes=config["scope"])
> ```
> 
> > |Où :| Description |
> > |---------|---------|
> > | `config["scope"]` | Contient les étendues demandées. Pour les clients confidentiels, utilisez un format similaire à `{Application ID URI}/.default`. Ce format indique que les étendues demandées sont celles qui sont définies de manière statique dans l’objet application défini dans le portail Azure (pour Microsoft Graph, `{Application ID URI}` pointe vers `https://graph.microsoft.com`). Pour les API web personnalisées, `{Application ID URI}` est défini sous la section **Exposer une API** dans **Inscription d’applications** sur le portail Azure.|
> 
> Pour plus d’informations, consultez la [documentation de référence sur `AcquireTokenForClient`](https://msal-python.readthedocs.io/en/latest/#msal.ConfidentialClientApplication.acquire_token_for_client).
> 
> [!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
> 
> ## <a name="next-steps"></a>Étapes suivantes
> 
> Pour en savoir plus sur les applications démon, consultez la page de destination du scénario.
> 
> > [!div class="nextstepaction"]
> > [Application démon qui appelle des API web](scenario-daemon-overview.md)
