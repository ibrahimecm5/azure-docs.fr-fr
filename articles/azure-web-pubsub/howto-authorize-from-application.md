---
title: Autoriser la requête aux ressources Web PubSub avec Azure AD à partir d’applications Azure
description: Cet article fournit des informations sur l’autorisation des demandes adressées aux ressources Web PubSub avec Azure AD à partir d’applications Azure
author: terencefan
ms.author: tefa
ms.date: 09/06/2021
ms.service: azure-web-pubsub
ms.topic: conceptual
ms.openlocfilehash: 7bca3e4840cf78b497f5907e780a267221dd1b8b
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131479062"
---
# <a name="authorize-request-to-web-pubsub-resources-with-azure-ad-from-azure-applications"></a>Autoriser la requête aux ressources Web PubSub avec Azure AD à partir d’applications Azure

Le service Azure Web PubSub prend en charge l’autorisation des demandes d’[applications Azure](../active-directory/develop/app-objects-and-service-principals.md) par Azure Active Directory (Azure AD). 

Cet article explique comment configurer votre ressource et vos codes Web PubSub pour autoriser la demande à une ressource Web PubSub à partir d’une application Azure.

## <a name="register-an-application"></a>Inscrire une application

La première étape consiste à inscrire une application Azure.

1. Dans le [portail Azure](https://portal.azure.com/), recherchez et sélectionnez **Azure Active Directory**
2. Sous la section **Gérer**, sélectionnez **Inscriptions d’applications**.
3. Cliquez sur **Nouvelle inscription**.

    ![Capture d’écran de l’inscription d’une application](./media/aad-authorization/register-an-application.png)

4. Entrez un **nom** d’affichage pour votre application.
5. Cliquez sur **S’inscrire** pour confirmer l’inscription.

Une fois votre application inscrite, vous pouvez trouver **l’ID de l’application (client)** et l’**ID de répertoire (locataire)** sous sa page Vue d’ensemble. Ces GUID peuvent être utiles lors des étapes suivantes.

![Capture d’écran d’une application](./media/aad-authorization/application-overview.png)

Pour en savoir plus sur l’inscription d’une application, consultez
- [Guide de démarrage rapide : Inscrire une application à l’aide de la plateforme d’identités Microsoft](../active-directory/develop/quickstart-register-app.md).

## <a name="add-credentials"></a>Ajouter les informations d’identification

Vous pouvez ajouter des certificats et des secrets clients (une chaîne) en tant qu’informations d’identification à votre inscription d’application cliente confidentielle.

### <a name="client-secret"></a>Clé secrète client

L’application nécessite une clé secrète client pour prouver son identité lors de la requête de jeton. Pour créer une clé secrète client, procédez comme suit.

1. Sous la section **Gérer**, sélectionnez **Certificats et secrets**
1. Sous l’onglet **Clés secrètes client**, cliquez sur **Nouvelle clé secrète client**.
![Capture d’écran de la création d’une clé secrète client](./media/aad-authorization/new-client-secret.png)
1. Entrez une **Description** pour la clé secrète client, puis choisissez une **durée d’expiration**.
1. Copiez la valeur de la **clé secrète client**, puis collez-la dans un emplacement sécurisé. 
    > [!NOTE]
    > Le secret ne s’affiche qu’une seule fois.
### <a name="certificate"></a>Certificat

Vous pouvez également charger une certification au lieu de créer un secret client.

![Capture d’écran du chargement d’une certification](./media/aad-authorization/upload-certificate.png)

Pour en savoir plus sur l’ajout d’informations d’identification, consultez

- [Ajouter les informations d’identification](../active-directory/develop/quickstart-register-app.md#add-credentials)

## <a name="add-role-assignments-on-azure-portal"></a>Ajouter des attributions de rôles sur le portail Azure

Cet exemple montre comment attribuer un rôle `Web PubSub Service Owner` à un principal de service (application) sur une ressource Web PubSub. 

> [!Note]
> Un rôle peut être affecté à n’importe quelle étendue, y compris le groupe d’administration, l’abonnement, le groupe de ressources ou une ressource unique. Pour en savoir plus sur l’étendue, consultez [Comprendre l’étendue pour Azure RBAC](../role-based-access-control/scope-overview.md)
1. Sur le [portail Azure](https://portal.azure.com/), accédez à votre ressource Web PubSub.

1. Cliquez sur **Contrôle d’accès (IAM)** pour afficher les paramètres de contrôle d’accès d’Azure Web PubSub.

1. Cliquez sur l’onglet **Attributions de rôles** afin d’afficher les attributions de rôles pour cette étendue.

   La capture d’écran suivante montre un exemple de la page Contrôle d’accès (IAM) pour une ressource Web PubSub.

   ![Capture d’écran du contrôle d’accès](./media/aad-authorization/access-control.png)

1. Cliquez sur **Ajouter > Ajouter une attribution de rôle**.

1. Sous l’onglet **Rôles**, sélectionnez `Web PubSub App Server`.

1. Cliquez sur **Suivant**.

   ![Capture d’écran de l’ajout de l’attribution de rôle](./media/aad-authorization/add-role-assignment.png)

1. Sous l’onglet **Membres**, sous **Affecter l’accès à**, sélectionnez **Utilisateur, groupe ou principal du service**.

1. Cliquez sur **Sélectionner des membres**

3. Recherchez et sélectionnez l’application à laquelle vous souhaitez affecter le rôle.

1. Cliquez sur **Sélectionner** pour confirmer la sélection.

4. Cliquez sur **Suivant**.

   ![Capture d’écran de l’affectation d’un rôle à des principaux de service](./media/aad-authorization/assign-role-to-service-principals.png)

5. Cliquez sur **Examiner + affecter** pour confirmer la modification.

> [!IMPORTANT]
> Les attributions de rôles Azure peuvent prendre jusqu’à 30 minutes pour se propager.
Pour en savoir plus sur l’attribution et la gestion des attributions de rôles Azure, consultez les articles suivants :
- [Attribuer des rôles Azure à l’aide du portail Azure](../role-based-access-control/role-assignments-portal.md)
- [Attribuer des rôles Azure à l’aide de l’API REST](../role-based-access-control/role-assignments-rest.md)
- [Attribuer des rôles Azure à l’aide d’Azure PowerShell](../role-based-access-control/role-assignments-powershell.md)
- [Attribuer des rôles Azure à l’aide d’Azure CLI](../role-based-access-control/role-assignments-cli.md)
- [Attribuer des rôles Azure avec des modèles Azure Resource Manager](../role-based-access-control/role-assignments-template.md)

## <a name="configure-your-server"></a>Configurer votre serveur

Il est recommandé de configurer l’identité et les informations d’identification dans vos variables d’environnement :

| Variable  | Description |
|------|------
| `AZURE_TENANT_ID` | ID du locataire Azure Active Directory (annuaire). |
| `AZURE_CLIENT_ID` | ID client (application) d’une inscription d’application dans le locataire. |
| `AZURE_CLIENT_SECRET` | Un secret client qui a été généré pour l’inscription de l’application. |
| `AZURE_CLIENT_CERTIFICATE_PATH` | Chemin d’accès au certificat et à la paire de clés privées au format PEM ou PFX, qui peut authentifier l’inscription de l’application. |
| `AZURE_USERNAME`  | Nom d’utilisateur, également appelé upn, d’un compte d’utilisateur Azure Active Directory. |
| `AZURE_PASSWORD`  | Mot de passe du compte d’utilisateur Azure Active Directory. Notez que les comptes avec MFA activé ne sont pas pris en charge. |

En procédant ainsi, vous pouvez utiliser [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) ou [EnvironmentCredential](/dotnet/api/azure.identity.environmentcredential) pour configurer vos points de terminaison Web PubSub.

### <a name="sample-codes"></a>Exemples de code

Voici des exemples de codes pour C#. Pour les autres langages pris en charge, consultez JavaScript/Python/Java.

```C#
var endpoint = new Uri("https://<resource1>.webpubsub.azure.com");
var client = new WebPubSubServiceClient(endpoint, "hub", new DefaultAzureCredential());
```

Pour en savoir plus sur le fonctionnement de `DefaultAzureCredential`, consultez la [classe DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential).

```C#
var endpoint = new Uri("https://<resource1>.webpubsub.azure.com");
var client = new WebPubSubServiceClient(endpoint, "hub", new EnvironmentCredential());
```

Vous pouvez également utiliser [ClientSecretCredential](/dotnet/api/azure.identity.clientsecretcredential) ou [ClientCertificateCredential](/dotnet/api/azure.identity.clientcertificatecredential) directement si vous le souhaitez.

```C#
var endpoint = new Uri("https://<resource1>.webpubsub.azure.com");
var credential = new ClientSecretCredential("tenantId", "clientId", "clientSecret");
var client = new WebPubSubServiceClient(endpoint, "hub", credential);
```
```C#
var endpoint = new Uri("https://<resource1>.webpubsub.azure.com");
var credential = new ClientCertificateCredential("tenantId", "clientId", "pathToCert");
var client = new WebPubSubServiceClient(endpoint, "hub", credential);
```

Pour en savoir plus sur la création de `TokenCredential` pour l’autorisation Azure AD, consultez les articles suivants :

- [Classe DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential)
- [Constructeurs ClientSecretCredential](/dotnet/api/azure.identity.clientsecretcredential.-ctor)
- [Constructeurs ClientCertificateCredential](/dotnet/api/azure.identity.clientcertificatecredential.-ctor)

## <a name="next-steps"></a>Étapes suivantes

Consultez les articles associés suivants :
- [Présentation d’Azure AD pour Web PubSub](concept-azure-ad-authorization.md)
- [Autoriser la requête aux ressources Web PubSub avec Azure AD à partir d’identités managées](howto-authorize-from-managed-identity.md)