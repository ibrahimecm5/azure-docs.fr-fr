---
title: Création d’une relation d’approbation entre une application et GitHub
titleSuffix: Microsoft identity platform
description: Configurez une relation d’approbation entre une application dans Azure AD et un référentiel GitHub.  Cela permet à un flux de travail GitHub Actions d’accéder à des ressources protégées Azure AD sans utiliser de secrets ni de certificats.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 10/18/2021
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: keyam, udayh, vakarand
ms.openlocfilehash: 3e1e3d4857555b648b841a544e02346e6144bb2b
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131096949"
---
# <a name="configure-an-app-to-trust-a-github-repo-preview"></a>Configuration d’une application de façon à approuver un référentiel GitHub (préversion)

Cet article explique comment créer une relation d’approbation entre une application dans Azure Active Directory (Azure AD) et un référentiel GitHub.  Vous pourrez alors configurer un flux de travail GitHub Actions pour échanger un jeton provenant de GitHub contre un jeton d’accès issu de la Plateforme d’identités Microsoft et accéder à des ressources Azure AD protégées sans avoir à gérer de secrets.  Pour plus d’informations sur le flux de travail d’échange de jetons, consultez [Fédération des identités de charge de travail](workload-identity-federation.md).  Pour établir la relation d’approbation, vous devez configurer les informations d’identification de l’identité fédérée sur l’inscription de votre application, sur le Portail Azure ou avec Microsoft Graph.

Toute personne disposant de l’autorisation de créer une inscription d’application et d’ajouter un secret ou un certificat a la possibilité d’ajouter les informations d’identification d’une identité fédérée.  Toutefois, si le commutateur **Les utilisateurs peuvent inscrire des applications** du panneau [Paramètres utilisateur](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/UserSettings) est défini sur **Non**, vous n’êtes pas en mesure de créer une inscription d’application ni de configurer les informations d’identification de l’identité fédérée.  Trouvez un administrateur qui puisse configurer les informations d’identification de l’identité fédérée en votre nom.  Toute personne disposant du rôle Administrateur d’application ou Propriétaire d’application est autorisée à le faire.

Une fois que vous avez configuré votre application de sorte qu’elle approuve un référentiel GitHub, [configurez votre flux de travail GitHub Actions](/azure/developer/github/connect-from-azure) pour obtenir un jeton d’accès auprès du fournisseur d’identité Microsoft et accéder aux ressources protégées Azure AD.

## <a name="prerequisites"></a>Prérequis
[Créez une inscription d’application](quickstart-register-app.md) dans Azure AD.  Accordez à votre application l’accès aux ressources Azure visées par votre flux de travail GitHub.  

Recherchez l’ID objet de l’application (et non l’ID d’application (client)) dont vous aurez besoin dans les étapes suivantes.  Vous le trouverez sur le Portail Azure.  Accédez à la liste des [applications inscrites](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) sur le Portail Azure et sélectionnez l’inscription de votre application.  Dans **Vue d’ensemble**->**Services essentiels**, recherchez **l’ID objet**.

Récupérez les informations relatives à l’organisation, au référentiel et à l’environnement de votre référentiel GitHub dont vous aurez besoin dans les étapes suivantes.

## <a name="configure-a-federated-identity-credential"></a>Configuration des informations d’identification d’une identité fédérée

# <a name="azure-portal"></a>[Azure portal](#tab/azure-portal)

Connectez-vous au [portail Azure](https://portal.azure.com/).  Accédez à **Inscriptions d’applications** et ouvrez l’application que vous souhaitez configurer.

Accédez à **Certificats et secrets**.  Dans l’onglet **Informations d’identification fédérées**, sélectionnez **Ajouter des informations d’identification**.  Le panneau **Ajouter des informations d’identification** s’ouvre.

Dans la zone déroulante **Scénario Informations d’identification fédérées**, sélectionnez **Actions GitHub de déploiement de ressources Azure**.

Spécifiez **l’Organisation** et le **Référentiel** de votre flux de travail GitHub Actions.  

Pour **Type d’entité**, sélectionnez **Environnement**, **Branche**, **Demande de tirage (pull request)** ou **Étiquette** et spécifiez la valeur.

Donnez un **Nom** aux informations d’identification fédérées.

Les champs **Émetteur**, **Audiences** et **Identificateur de l’objet** sont remplis automatiquement à partir des valeurs entrées.

Cliquez sur **Ajouter** pour configurer les informations d’identification fédérées.

:::image type="content" source="media/workload-identity-federation-create-trust-github/add-credential.png" alt-text="Capture d’écran de la fenêtre Ajouter des informations d’identification, indiquant des exemples de valeurs." :::

> [!NOTE]
> Si vous configurez accidentellement le référentiel GitHub de quelqu’un d’autre dans le paramètre *subject* (à cause d’une faute de frappe), vous pouvez créer les informations d’identification de l’identité fédérée.  Dans la configuration GitHub toutefois, vous obtenez une erreur, car vous n’êtes pas en mesure d’accéder au référentiel d’une autre personne.

> [!IMPORTANT]
> Les valeurs **Organisation**, **Référentiel** et **Type d’entité** doivent correspondre exactement à la configuration du flux de travail GitHub. Dans le cas contraire, la Plateforme d’identités Microsoft examine le jeton externe entrant et rejette l’échange contre un jeton d’accès.  Vous n’obtenez pas d’erreur : l’échange échoue sans erreur.

# <a name="microsoft-graph"></a>[Microsoft Graph](#tab/microsoft-graph)
Lancez [Azure Cloud Shell](https://portal.azure.com/#cloudshell/) et connectez-vous à votre locataire.

### <a name="create-a-federated-identity-credential"></a>Création des informations d’identification d’une identité fédérée

Exécutez la commande suivante pour [créer les informations d’identification d’une nouvelle identité fédérée](/graph/api/application-post-federatedidentitycredentials?view=graph-rest-beta&preserve-view=true) sur votre application (spécifiée par son ID objet).  *issuer* identifie GitHub comme l’émetteur du jeton externe.  *subject* indique l’organisation, le référentiel et l’environnement GitHub de votre flux de travail Actions GitHub.  Quand le flux de travail GitHub Actions demande à la Plateforme d’identités Microsoft d’échanger un jeton GitHub contre un jeton d’accès, les valeurs des informations d’identification de l’identité fédérée sont confrontées au jeton GitHub fourni.

```azurecli
az rest --method POST --uri 'https://graph.microsoft.com/beta/applications/f6475511-fd81-4965-a00e-41e7792b7b9c/federatedIdentityCredentials' --body '{"name":"Testing","issuer":"https://token.actions.githubusercontent.com/","subject":"repo:octo-org/octo-repo:environment:Production","description":"Testing","audiences":["api://AzureADTokenExchange"]}' 
```

Vous recevez la réponse suivante :
```azurecli
{
  "@odata.context": "https://graph.microsoft.com/beta/$metadata#applications('f6475511-fd81-4965-a00e-41e7792b7b9c')/federatedIdentityCredentials/$entity",
  "audiences": [
    "api://AzureADTokenExchange"
  ],
  "description": "Testing",
  "id": "1aa3e6a7-464c-4cd2-88d3-90db98132755",
  "issuer": "https://token.actions.githubusercontent.com/",
  "name": "Testing",
  "subject": "repo:octo-org/octo-repo:environment:Production"
}
```

*name* : nom de votre application Azure.

*issuer* : chemin du fournisseur GitHub OIDC (`https://token.actions.githubusercontent.com/`). Cet émetteur sera approuvé par votre application Azure.

*subject* : la demande doit correspondre aux conditions définies ici pour qu’Azure accorde un jeton d’accès.
- Pour les travaux liés à un environnement : `repo:< Organization/Repository >:environment:< Name >`.
- Pour les travaux non liés à un environnement, incluez le chemin de référence de la branche ou de l’étiquette en fonction de celui utilisé pour déclencher le flux de travail (`repo:< Organization/Repository >:ref:< ref path>`).  Par exemple, `repo:n-username/ node_express:ref:refs/heads/my-branch` ou `repo:n-username/ node_express:ref:refs/tags/my-tag`.
- Pour les flux de travail déclenchés par un événement de demande de tirage (pull request) : `repo:< Organization/Repository >:pull-request`.

*audiences* : la valeur requise est `api://AzureADTokenExchange`.

> [!NOTE]
> Si vous configurez accidentellement le référentiel GitHub de quelqu’un d’autre dans le paramètre *subject* (à cause d’une faute de frappe), vous pouvez créer les informations d’identification de l’identité fédérée.  Dans la configuration GitHub toutefois, vous obtenez une erreur, car vous n’êtes pas en mesure d’accéder au référentiel d’une autre personne.

> [!IMPORTANT]
> Le paramètre *subject* doit correspondre exactement à la configuration du flux de travail GitHub.  Dans le cas contraire, la Plateforme d’identités Microsoft examine le jeton externe entrant et rejette l’échange contre un jeton d’accès.  Vous n’obtenez pas d’erreur : l’échange échoue sans erreur.

### <a name="list-federated-identity-credentials-on-an-app"></a>Affichage de la liste des informations d’identification des identités fédérées sur une application

Exécutez la commande suivante pour [répertorier les informations d’identification de la ou des identités fédérées](/graph/api/application-list-federatedidentitycredentials?view=graph-rest-beta&preserve-view=true) d’une application (spécifiée par son ID objet) :

```azurecli
az rest -m GET -u 'https://graph.microsoft.com/beta/applications/f6475511-fd81-4965-a00e-41e7792b7b9c/federatedIdentityCredentials' 
```

Vous recevez une réponse de ce type :

```azurecli
{
  "@odata.context": "https://graph.microsoft.com/beta/$metadata#applications('f6475511-fd81-4965-a00e-41e7792b7b9c')/federatedIdentityCredentials",
  "value": [
    {
      "audiences": [
        "api://AzureADTokenExchange"
      ],
      "description": "Testing",
      "id": "1aa3e6a7-464c-4cd2-88d3-90db98132755",
      "issuer": "https://token.actions.githubusercontent.com/",
      "name": "Testing",
      "subject": "repo:octo-org/octo-repo:environment:Production"
    }
  ]
}
```

### <a name="delete-a-federated-identity-credential"></a>Suppression des informations d’identification d’une identité fédérée

Exécutez la commande suivante pour [supprimer les informations d’identification d’une identité fédérée](/graph/api/application-list-federatedidentitycredentials?view=graph-rest-beta&preserve-view=true) d’une application (spécifiée par son ID objet) :

```azurecli
az rest -m DELETE  -u 'https://graph.microsoft.com/beta/applications/f6475511-fd81-4965-a00e-41e7792b7b9c/federatedIdentityCredentials/1aa3e6a7-464c-4cd2-88d3-90db98132755' 
```
---

## <a name="get-the-application-client-id-and-tenant-id-from-the-azure-portal"></a>Récupération de l’ID d’application (client) et de l’ID de locataire sur le Portail Azure

Avant de configurer votre flux de travail GitHub Actions, récupérez les valeurs *tenant-id* et *client-id* de l’inscription de votre application.  Vous pouvez trouver ces valeurs dans le portail Azure. Accédez à la liste des [applications inscrites](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps), puis sélectionnez l’inscription de votre application.  Dans **Vue d’ensemble**->**Services essentiels**, recherchez **l’ID d’application (client)** et **l’ID de répertoire (locataire)** . Définissez ces valeurs dans votre environnement GitHub à utiliser dans l’action de connexion Azure de votre flux de travail.  

## <a name="next-steps"></a>Étapes suivantes
[Configurez un flux de travail GitHub Actions](/azure/developer/github/connect-from-azure) pour obtenir un jeton d’accès auprès du fournisseur d’identité Microsoft et accéder aux ressources Azure.

Pour savoir comment configurer votre flux de travail GitHub Actions de façon à obtenir un jeton d’accès auprès du fournisseur d’identité Microsoft et à accéder aux ressources Azure, consultez la [documentation de GitHub Actions](https://docs.github.com/actions/deployment/security-hardening-your-deployments/configuring-openid-connect-in-azure).