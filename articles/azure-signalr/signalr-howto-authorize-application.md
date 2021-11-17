---
title: Autoriser les demandes de ressources SignalR avec Azure AD à partir d’applications Azure
description: Cet article fournit des informations sur l’autorisation des demandes de ressources SignalR avec Azure AD à partir d’applications Azure
author: terencefan
ms.author: tefa
ms.date: 09/06/2021
ms.service: signalr
ms.topic: conceptual
ms.openlocfilehash: 8297a715d66206bbad2721faaca89c3616744ce4
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131479152"
---
# <a name="authorize-request-to-signalr-resources-with-azure-ad-from-azure-applications"></a>Autoriser les demandes de ressources SignalR avec Azure AD à partir d’applications Azure

Azure SignalR Service prend en charge l’autorisation par Azure Active Directory (Azure AD) des demandes à partir d’[applications Azure](../active-directory/develop/app-objects-and-service-principals.md).

Cet article explique comment configurer votre ressource et vos codes SignalR pour autoriser la demande à une ressource SignalR à partir d’une application Azure.

## <a name="register-an-application"></a>Inscrire une application

La première étape consiste à inscrire une application Azure.

1. Dans le [portail Azure](https://portal.azure.com/), recherchez et sélectionnez **Azure Active Directory**
2. Sous la section **Gérer**, sélectionnez **Inscriptions d’applications**.
3. Cliquez sur **Nouvelle inscription**.

    ![Capture d’écran de l’inscription d’une application](./media/authenticate/register-an-application.png)

4. Entrez un **nom** d’affichage pour votre application.
5. Cliquez sur **S’inscrire** pour confirmer l’inscription.

Une fois votre application inscrite, vous pouvez trouver **l’ID de l’application (client)** et l’**ID de répertoire (locataire)** sous sa page Vue d’ensemble. Ces GUID peuvent être utiles lors des étapes suivantes.

![Capture d’écran d’une application](./media/authenticate/application-overview.png)

Pour en savoir plus sur l’inscription d’une application, consultez
- [Guide de démarrage rapide : Inscrire une application à l’aide de la plateforme d’identités Microsoft](../active-directory/develop/quickstart-register-app.md).


## <a name="add-credentials"></a>Ajouter les informations d’identification

Vous pouvez ajouter des certificats et des secrets clients (une chaîne) en tant qu’informations d’identification à votre inscription d’application cliente confidentielle.

### <a name="client-secret"></a>Clé secrète client

L’application nécessite une clé secrète client pour prouver son identité lors de la requête de jeton. Pour créer une clé secrète client, procédez comme suit.

1. Sous la section **Gérer**, sélectionnez **Certificats et secrets**
1. Sous l’onglet **Clés secrètes client**, cliquez sur **Nouvelle clé secrète client**.
![Capture d’écran de la création d’une clé secrète client](./media/authenticate/new-client-secret.png)
1. Entrez une **Description** pour la clé secrète client, puis choisissez une **durée d’expiration**.
1. Copiez la valeur de la **clé secrète client**, puis collez-la dans un emplacement sécurisé. 
    > [!NOTE]
    > Le secret ne s’affiche qu’une seule fois.

### <a name="certificate"></a>Certificat

Vous pouvez également charger une certification au lieu de créer un secret client.

![Capture d’écran du chargement d’une certification](./media/authenticate/upload-certificate.png)

Pour en savoir plus sur l’ajout d’informations d’identification, consultez

- [Ajouter les informations d’identification](../active-directory/develop/quickstart-register-app.md#add-credentials)

## <a name="add-role-assignments-on-azure-portal"></a>Ajouter des attributions de rôles sur le portail Azure

Cet exemple montre comment attribuer un rôle `SignalR App Server` à un principal de service (application) sur une ressource SignalR. 

> [!Note]
> Un rôle peut être affecté à n’importe quelle étendue, y compris le groupe d’administration, l’abonnement, le groupe de ressources ou une ressource unique. Pour en savoir plus sur l’étendue, consultez [Comprendre l’étendue pour Azure RBAC](../role-based-access-control/scope-overview.md)

1. Dans le [portail Azure](https://portal.azure.com/), accédez à votre ressource SignalR.

1. Cliquez sur **Contrôle d’accès (IAM)** pour afficher les paramètres de contrôle d’accès d’Azure SignalR.

1. Cliquez sur l’onglet **Attributions de rôles** afin d’afficher les attributions de rôles pour cette étendue.

   La capture d’écran suivante montre un exemple de la page Contrôle d’accès (IAM) pour une ressource SignalR.

   ![Capture d’écran du contrôle d’accès](./media/authenticate/access-control.png)

1. Cliquez sur **Ajouter > Ajouter une attribution de rôle**.

1. Sous l’onglet **Rôles**, sélectionnez `SignalR App Server`.

1. Cliquez sur **Suivant**.
    
   ![Capture d’écran de l’ajout de l’attribution de rôle](./media/authenticate/add-role-assignment.png)

1. Sous l’onglet **Membres**, sous **Affecter l’accès à**, sélectionnez **Utilisateur, groupe ou principal du service**.

1. Cliquez sur **Sélectionner des membres**

3. Recherchez et sélectionnez l’application à laquelle vous souhaitez affecter le rôle.

1. Cliquez sur **Sélectionner** pour confirmer la sélection.

4. Cliquez sur **Suivant**.
    
   ![Capture d’écran de l’affectation d’un rôle à des principaux de service](./media/authenticate/assign-role-to-service-principals.png)

5. Cliquez sur **Examiner + affecter** pour confirmer la modification.

> [!IMPORTANT]
> Les attributions de rôles Azure peuvent prendre jusqu’à 30 minutes pour se propager.

Pour en savoir plus sur l’attribution et la gestion des attributions de rôles Azure, consultez les articles suivants :
- [Attribuer des rôles Azure à l’aide du portail Azure](../role-based-access-control/role-assignments-portal.md)
- [Attribuer des rôles Azure à l’aide de l’API REST](../role-based-access-control/role-assignments-rest.md)
- [Attribuer des rôles Azure à l’aide d’Azure PowerShell](../role-based-access-control/role-assignments-powershell.md)
- [Attribuer des rôles Azure à l’aide d’Azure CLI](../role-based-access-control/role-assignments-cli.md)
- [Attribuer des rôles Azure avec des modèles Azure Resource Manager](../role-based-access-control/role-assignments-template.md)

## <a name="configure-you-app"></a>Configurer votre application

### <a name="app-server"></a>App Server

La meilleure pratique consiste à configurer l’identité et les informations d’identification dans vos variables d’environnement :

| Variable  | Description |
|------|------
| `AZURE_TENANT_ID` | ID du locataire Azure Active Directory (annuaire). |
| `AZURE_CLIENT_ID` | ID client (application) d’une inscription d’application dans le locataire. |
| `AZURE_CLIENT_SECRET` | Un secret client qui a été généré pour l’inscription de l’application. |
| `AZURE_CLIENT_CERTIFICATE_PATH` | Chemin d’accès au certificat et à la paire de clés privées au format PEM ou PFX, qui peut authentifier l’inscription de l’application. |
| `AZURE_USERNAME`  | Nom d’utilisateur, également appelé upn, d’un compte d’utilisateur Azure Active Directory. |
| `AZURE_PASSWORD`  | Mot de passe du compte d’utilisateur Azure Active Directory. Notez que les comptes avec MFA activé ne sont pas pris en charge. |

En procédant ainsi, vous pouvez utiliser [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) ou [EnvironmentCredential](/dotnet/api/azure.identity.environmentcredential) pour configurer vos points de terminaison SignalR.

```C#
services.AddSignalR().AddAzureSignalR(option =>
{
    option.Endpoints = new ServiceEndpoint[]
    {
        new ServiceEndpoint(new Uri("https://<resource-name>.service.signalr.net"), new DefaultAzureCredential())
    };
});
```

Vous pouvez également utiliser `EnvironmentalCredential` directement.

```C#
services.AddSignalR().AddAzureSignalR(option =>
{
    option.Endpoints = new ServiceEndpoint[]
    {
        new ServiceEndpoint(new Uri("https://<resource-name>.service.signalr.net"), new EnvironmentCredential())
    };
});
```

Pour en savoir plus sur le fonctionnement de `DefaultAzureCredential`, consultez la [classe DefaultAzureCredential](/dotnet/api/overview/azure/identity-readme#defaultazurecredential).

#### <a name="use-different-credentials-while-using-multiple-endpoints"></a>Utilisez d’autres informations d’identification si vous avez plusieurs points de terminaison.

Il se peut que vous souhaitiez utiliser des informations d’identification distinctes pour différents points de terminaison.

Dans ce cas, vous pouvez utiliser [ClientSecretCredential](/dotnet/api/azure.identity.clientsecretcredential) ou [ClientCertificateCredential](/dotnet/api/azure.identity.clientcertificatecredential).

```csharp
services.AddSignalR().AddAzureSignalR(option =>
{
    var credential1 = new ClientSecretCredential("tenantId", "clientId", "clientSecret");
    var credential2 = new ClientCertificateCredential("tenantId", "clientId", "pathToCert");

    option.Endpoints = new ServiceEndpoint[]
    {
        new ServiceEndpoint(new Uri("https://<resource1>.service.signalr.net"), credential1),
        new ServiceEndpoint(new Uri("https://<resource2>.service.signalr.net"), credential2),
    };
});
```

### <a name="azure-functions-signalr-bindings"></a>Liaisons Signalr Azure Functions

> [!WARNING]
> La liaison de déclencheur SignalR ne prenant pas encore en charge la connexion basée sur l’identité, des chaînes de connexion restent nécessaires.

Les liaisons Signalr Azure Functions utilisent des [paramètres d’application](/azure/azure-functions/functions-how-to-use-azure-function-app-settings) sur le portail ou [`local.settings.json`](/azure/azure-functions/functions-develop-local#local-settings-file) localement pour configurer des identités d’application Azure afin d’accéder vos ressources SignalR.

Tout d’abord, vous devez spécifier l’URI de service de SignalR, dont la clé est `serviceUri`, commençant par un **préfixe de nom de connexion** (par défaut, `AzureSignalRConnectionString`) et un séparateur (`__` sur le portail Azure et `:` dans le fichier local.settings.json). Vous pouvez personnaliser le nom de la connexion à l’aide de la propriété de liaison [`ConnectionStringSetting`](/azure/azure-functions/functions-bindings-signalr-service). Poursuivez la lecture pour voir l’échantillon.

Vous choisissez ensuite de configurer l’identité de votre application Azure dans des [variables d’environnement prédéfinies](#configure-identity-in-pre-defined-environment-variables) ou dans des [variables spécifiées par SignalR](#configure-identity-in-signalr-specified-variables).

#### <a name="configure-identity-in-pre-defined-environment-variables"></a>Configurer l’identité dans des variables d’environnement prédéfinies

Pour voir la liste des variables d’environnement prédéfinies, consultez [Variables d’environnement](/dotnet/api/overview/azure/identity-readme#environment-variables). Lorsque vous disposez de plusieurs services, il est recommandé d’inclure Signalr dépendant de la même identité d’application Azure, afin de ne pas avoir à configurer l’identité pour chaque service. Ces variables d’environnement peuvent également être utilisées par d’autres services en fonction des paramètres de ceux-ci.

Par exemple, pour utiliser des informations d’identification de clé secrète client, définissez la configuration suivante dans le fichier `local.settings.json`.
```json
{
  "Values": {
    "<CONNECTION_NAME_PREFIX>:serviceUri": "https://<SIGNALR_RESOURCE_NAME>.service.signalr.net",
    "AZURE_CLIENT_ID":"...",
    "AZURE_CLIENT_SECRET":"...",
    "AZURE_TENANT_ID":"..."
  }
}
```
Sur le portail Azure, ajoutez des paramètres comme suit :
```
 <CONNECTION_NAME_PREFIX>__serviceUri=https://<SIGNALR_RESOURCE_NAME>.service.signalr.net
AZURE_CLIENT_ID = ...
AZURE_TENANT_ID = ...
AZURE_CLIENT_SECRET = ...
 ```

#### <a name="configure-identity-in-signalr-specified-variables"></a>Configurer l’identité dans des variables spécifiées par Signalr

Les variables spécifiées par Signalr partagent le préfixe de la clé `serviceUri`. Voici la liste des variables que vous pouvez utiliser :
* clientId
* clientSecret
* tenantId

Voici les échantillons pour utiliser les informations d’identification de clé secrète client :

Dans le fichier `local.settings.json` :
```json
{
  "Values": {
    "<CONNECTION_NAME_PREFIX>:serviceUri": "https://<SIGNALR_RESOURCE_NAME>.service.signalr.net",
    "<CONNECTION_NAME_PREFIX>:clientId": "...",
    "<CONNECTION_NAME_PREFIX>:clientSecret": "...",
    "<CONNECTION_NAME_PREFIX>:tenantId": "..."
  }
}
```

Sur le portail Azure, ajoutez des paramètres comme suit :
```
<CONNECTION_NAME_PREFIX>__serviceUri = https://<SIGNALR_RESOURCE_NAME>.service.signalr.net
<CONNECTION_NAME_PREFIX>__clientId = ...
<CONNECTION_NAME_PREFIX>__clientSecret = ...
<CONNECTION_NAME_PREFIX>__tenantId = ...
```
## <a name="next-steps"></a>Étapes suivantes

Consultez les articles associés suivants :
- [Vue d’ensemble d’Azure AD pour SignalR](signalr-concept-authorize-azure-active-directory.md)
- [Autoriser les requêtes aux ressources SignalR avec Azure AD à partir d’identités managées](signalr-howto-authorize-managed-identity.md)