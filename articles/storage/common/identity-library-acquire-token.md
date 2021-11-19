---
title: Utiliser la bibliothèque Azure Identity pour obtenir un jeton d’accès pour l’autorisation
titleSuffix: Azure Storage
description: Apprenez à utiliser la bibliothèque de client Azure Identity pour obtenir un jeton d’accès que vos applications peuvent utiliser pour autoriser l’accès aux données dans Stockage Azure. Avec la bibliothèque Azure Identity, vous pouvez utiliser le même code pour récupérer le jeton d’accès dans l’environnement de développement ou dans Azure.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 11/13/2021
ms.author: tamram
ms.reviewer: santoshc
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: 3de8e679cb6c99db4057ec5e5b0ae48b2b4c3500
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132490285"
---
# <a name="use-the-azure-identity-library-to-get-an-access-token-for-authorization"></a>Utiliser la bibliothèque Azure Identity pour obtenir un jeton d’accès pour l’autorisation

La bibliothèque de client Azure Identity simplifie le processus d’obtention d’un jeton d’accès OAuth 2.0 pour l’autorisation avec Azure Active Directory (Azure AD) via le [Kit de développement logiciel (SDK) Azure](https://github.com/Azure/azure-sdk). Les versions les plus récentes des bibliothèques de client Stockage Azure pour .NET, Java, Python, JavaScript et Go s’intègrent aux bibliothèques Azure Identity pour chacun de ces langages afin de fournir un moyen simple et sécurisé d’acquérir un jeton d’accès pour l’autorisation des demandes Stockage Azure.

L’un des avantages de la bibliothèque de client Azure Identity est qu’elle vous permet d’utiliser le même code pour acquérir le jeton d’accès, que votre application soit exécutée dans l’environnement de développement ou dans Azure. La bibliothèque de client Azure Identity renvoie un jeton d’accès pour un principal de sécurité. Lorsque votre code s’exécute dans Azure, le principal de sécurité peut être une identité managée pour les ressources Azure, un principal de service, un utilisateur ou un groupe. Dans l’environnement de développement, la bibliothèque de client fournit un jeton d’accès pour un utilisateur ou un principal de service à des fins de test.

Le jeton d’accès renvoyé par la bibliothèque de client Azure Identity est encapsulé dans les informations d’identification d’un jeton. Vous pouvez ensuite utiliser les informations d’identification du jeton pour obtenir un objet client de service à utiliser lors de l’exécution d’opérations autorisées sur Stockage Azure. Un moyen simple d’obtenir le jeton d’accès et les informations d’identification du jeton consiste à utiliser la classe **DefaultAzureCredential** fournie par la bibliothèque de client Azure Identity. Une instance de cette classe tente d’obtenir les informations d’identification du jeton de diverses manières courantes, et elle fonctionne à la fois dans l’environnement de développement et dans Azure.

Pour plus d’informations sur la bibliothèque de client Azure Identity pour votre langage de programmation, consultez l’un des articles suivants :

- [Bibliothèque cliente Azure Identity pour .NET](/dotnet/api/overview/azure/identity-readme)
- [Bibliothèque cliente Azure Identity pour Java](/java/api/overview/azure/identity-readme)
- [Bibliothèque cliente Azure Identity pour Python](/python/api/overview/azure/identity-readme)
- [Bibliothèque cliente Azure Identity pour JavaScript](/javascript/api/overview/azure/identity-readme)
- [Bibliothèque cliente Azure Identity pour Go](/javascript/api/overview/azure/identity-readme)

## <a name="assign-azure-roles-for-access-to-data"></a>Attribuer des rôles Azure pour l’accès aux données

Lorsqu’un principal de sécurité Azure AD tente d’accéder à des données dans un compte Stockage Azure, ce principal de sécurité doit avoir des autorisations pour la ressource de données. Que le principal de sécurité soit une identité managée dans Azure ou un compte d’utilisateur Azure AD exécutant du code dans l’environnement de développement, un rôle Azure qui accorde l’accès aux données dans Stockage Azure doit être attribué au principal de sécurité. Pour obtenir des informations sur l’attribution d’autorisations d’accès aux données via le contrôle d’accès en fonction du rôle Azure, consultez l’un des articles suivants :

- [Attribuer un rôle Azure pour l’accès aux données d’objet blob](../blobs/assign-azure-role-data-access.md)
- [Attribuer un rôle Azure pour l’accès aux données de file d’attente](../queues/assign-azure-role-data-access.md)
- [Attribuer un rôle Azure pour l’accès aux données de table (préversion)](../tables/assign-azure-role-data-access.md)

> [!NOTE]
> Lorsque vous créez un compte de stockage Azure, aucune autorisation d’accès aux données ne vous est automatiquement attribuée via Azure AD. Vous devez vous attribuer explicitement un rôle Azure pour le Stockage Azure. Vous pouvez l’attribuer au niveau de votre abonnement, groupe de ressources, compte de stockage, conteneur, file d’attente ou table.

## <a name="authenticate-the-user-in-the-development-environment"></a>Authentifier l’utilisateur dans l’environnement de développement

Lorsque votre code s’exécute dans l’environnement de développement, l’authentification peut être gérée automatiquement ou nécessiter une connexion du navigateur, selon les outils que vous utilisez. Par exemple, Microsoft Visual Studio et Microsoft Visual Studio Code prennent en charge l’authentification unique (SSO) afin que le compte d’utilisateur Azure AD actif soit automatiquement utilisé pour l’authentification. Pour plus d’informations sur l’authentification unique, consultez [Authentification unique aux applications](../../active-directory/manage-apps/what-is-single-sign-on.md).

Vous pouvez également créer un principal de service et définir des variables d’environnement que l’environnement de développement peut lire au moment de l’exécution.

## <a name="authenticate-a-service-principal-in-the-development-environment"></a>Authentifier un principal de service dans l’environnement de développement

Si votre environnement de développement ne prend pas en charge l’authentification unique ou la connexion via un navigateur web, vous pouvez utiliser un principal de service pour vous authentifier à partir de l’environnement de développement. Après avoir créé le principal de service, ajoutez les valeurs renvoyées pour le principal de service aux variables d’environnement.

### <a name="create-the-service-principal"></a>Créer le principal du service

Pour créer un principal du service avec Azure CLI et attribuer un rôle Azure, appelez la commande [az ad sp create-for-rbac](/cli/azure/ad/sp#az_ad_sp_create_for_rbac). Fournissez un rôle d’accès aux données de Stockage Azure à attribuer au nouveau principal du service. Indiquez également l’étendue de l’attribution de rôle. Pour plus d’informations sur les rôles intégrés fournis pour Stockage Azure, consultez [Rôles intégrés Azure](../../role-based-access-control/built-in-roles.md).

Si vous ne disposez pas des autorisations suffisantes pour attribuer un rôle au principal du service, vous devrez peut-être demander au propriétaire du compte ou à l’administrateur d’effectuer l’attribution de rôle.

L’exemple suivant utilise Azure CLI pour créer un nouveau principal de service et lui attribuer le rôle **Contributeur aux données Blob du stockage**, délimité au compte de stockage :

```azurecli-interactive
az ad sp create-for-rbac \
    --name <service-principal> \
    --role "Storage Blob Data Contributor" \
    --scopes /subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
```

La commande `az ad sp create-for-rbac` retourne une liste de propriétés de principal du service au format JSON. Copiez ces valeurs pour pouvoir les utiliser afin de créer les variables d’environnement nécessaires à l’étape suivante.

```json
{
    "appId": "generated-app-ID",
    "displayName": "service-principal-name",
    "name": "http://service-principal-uri",
    "password": "generated-password",
    "tenant": "tenant-ID"
}
```

Pour plus d’informations sur la création d’un principal de service, consultez l’un des articles suivants :

- [Créer une application et un principal de service Azure AD dans le portail](../../active-directory/develop/howto-create-service-principal-portal.md)
- [Créer un principal de service Azure avec Azure PowerShell](/powershell/azure/create-azure-service-principal-azureps)
- [Créer un principal du service Azure avec Azure CLI](/cli/azure/create-an-azure-service-principal-azure-cli)

> [!IMPORTANT]
> Les attributions de rôle Azure peuvent prendre plusieurs minutes pour se propager.

### <a name="set-environment-variables"></a>Définir des variables d’environnement

La bibliothèque cliente Azure Identity lit les valeurs de trois variables d’environnement au moment de l’exécution pour authentifier le principal du service. Le tableau suivant indique la valeur à définir pour chaque variable d’environnement.

|Variable d’environnement|Valeur
|-|-
|`AZURE_CLIENT_ID`|ID de l’application pour le principal du service
|`AZURE_TENANT_ID`|ID de locataire Azure AD du principal du service
|`AZURE_CLIENT_SECRET`|Mot de passe généré pour le principal du service

> [!IMPORTANT]
> Après avoir défini les variables d’environnement, fermez et rouvrez votre fenêtre de console. Si vous utilisez Visual Studio ou un autre environnement de développement, vous devrez peut-être redémarrer l’environnement de développement afin qu’il enregistre les nouvelles variables d’environnement.

Pour plus d’informations, consultez [Créer une identité pour une application Azure dans le portail](../../active-directory/develop/howto-create-service-principal-portal.md).

## <a name="get-an-access-token-for-authorization"></a>Obtenir un jeton d’accès pour les autorisations

La bibliothèque de client Azure Identity fournit des classes que vous pouvez utiliser pour obtenir un jeton d’accès afin d’autoriser les demandes avec Azure AD. Les bibliothèques de client Stockage Azure incluent des constructeurs pour les objets client de service qui prennent des informations d’identification de jeton comme paramètre. Vous pouvez utiliser les deux ensemble pour autoriser facilement les requêtes Stockage Azure avec des informations d’identification Azure AD.

L’utilisation de la classe **DefaultAzureCredential** est recommandée pour la plupart des scénarios simples où votre application doit obtenir un jeton d’accès à la fois dans l’environnement de développement et dans Azure. Divers types d’informations d’identification de jeton sont également disponibles pour d’autres scénarios.

L’exemple suivant montre comment utiliser DefaultAzureCredential pour obtenir un jeton dans .NET. L’application utilise ensuite le jeton pour créer un client de service, qui est ensuite utilisé pour créer un conteneur de blobs. Bien que cet exemple utilise .NET et le service Stockage Blob, la classe **DefaultAzureCredential** se comporte de la même façon avec d’autres langages et d’autres services Azure.

```csharp
static void CreateBlobContainer(string accountName, string containerName)
{
    // Construct the blob container endpoint from the arguments.
    string containerEndpoint = string.Format("https://{0}.blob.core.windows.net/{1}",
                                                accountName,
                                                containerName);

    // Get a token credential and create a service client object for the blob container.
    BlobContainerClient containerClient = new BlobContainerClient(new Uri(containerEndpoint),
                                                                  new DefaultAzureCredential());

    // Create the container if it does not exist.
    containerClient.CreateIfNotExists();
}
```  

Pour plus d’informations sur l’utilisation de la classe DefaultAzureCredential pour autoriser une identité managée à accéder à Stockage Azure, consultez [Bibliothèque de client Azure Identity pour .NET](/dotnet/api/overview/azure/identity-readme).

## <a name="see-also"></a>Voir aussi

- [Applications et principaux de service dans Azure AD](../../active-directory/develop/app-objects-and-service-principals.md)
- [Bibliothèques d’authentification de plateforme d’identité Microsoft](../../active-directory/develop/reference-v2-libraries.md)