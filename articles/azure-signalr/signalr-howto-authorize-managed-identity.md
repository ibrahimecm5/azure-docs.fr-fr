---
title: Autoriser les requêtes aux ressources SignalR avec Azure AD à partir d’identités managées
description: Cet article fournit des informations sur l’autorisation des requêtes adressées aux ressources SignalR avec Azure AD à partir d’identités managées
author: terencefan
ms.author: tefa
ms.date: 09/06/2021
ms.service: signalr
ms.topic: conceptual
ms.openlocfilehash: 1d332fd5d69b088717501771ccc03d8d24126478
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131479433"
---
# <a name="authorize-request-to-signalr-resources-with-azure-ad-from-managed-identities"></a>Autoriser les requêtes aux ressources SignalR avec Azure AD à partir d’identités managées
Azure SignalR Service prend en charge l’autorisation des requêtes Azure Active Directory (Azure AD) à partir des [identités managées pour les ressources Azure](../active-directory/managed-identities-azure-resources/overview.md).

Cet article explique comment configurer votre ressource et vos codes SignalR pour autoriser la demande à une ressource SignalR à partir d’une identité managée.

## <a name="configure-managed-identities"></a>Configurer des identités managées

La première étape consiste à configurer des identités managées.

Il s’agit d’un exemple de configuration de `System-assigned managed identity` sur un `Virtual Machine` à l’aide du portail Azure.

1. Ouvrez le [portail Azure](https://portal.azure.com/), recherchez et sélectionnez une Machine virtuelle.
1. Sous la section **Paramètres**, sélectionnez **Identité**.
1. Dans l’onglet **Affecté par le système**, définissez l’**État** sur **Activé**.
   ![Capture d’écran d’une application](./media/authenticate/identity-virtual-machine.png)
1. Cliquez sur le bouton **Enregistrer** pour confirmer les modifications.


Pour savoir comment créer des identités managées affectées par l’utilisateur, consultez cet article :
- [Créer une identité managée attribuée par l’utilisateur](../active-directory/managed-identities-azure-resources/how-manage-user-assigned-managed-identities.md#create-a-user-assigned-managed-identity)

Pour en savoir plus sur la configuration des identités managées, consultez l’un des articles suivants :

- [Configurer des identités managées pour ressources Azure sur une machine virtuelle en utilisant le portail Azure](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
- [Configurer des identités managées pour ressources Azure sur une machine virtuelle Azure en utilisant PowerShell](../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Configurer des identités managées pour ressources Azure sur une machine virtuelle Azure en utilisant Azure CLI](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Configurer des identités managées pour les ressources Azure sur une machine virtuelle Azure en utilisant des modèles](../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Configurer une machine virtuelle avec des identités managées pour ressources Azure en utilisant un SDK Azure](../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

### <a name="for-app-service-and-azure-functions"></a>Pour App Service et Azure Functions

Consultez [Guide pratique pour utiliser des identités managées avec App Service et Azure Functions](../app-service/overview-managed-identity.md).

## <a name="add-role-assignments-on-azure-portal"></a>Ajouter des attributions de rôles sur le portail Azure

Cet exemple montre comment attribuer un rôle `SignalR App Server` à une identité affectée par le système sur une ressource SignalR. 

> [!Note]
> Un rôle peut être affecté à n’importe quelle étendue, y compris le groupe d’administration, l’abonnement, le groupe de ressources ou une ressource unique. Pour en savoir plus sur l’étendue, consultez [Comprendre l’étendue pour Azure RBAC](../role-based-access-control/scope-overview.md)

1. Ouvrez le [portail Azure](https://portal.azure.com/), accédez à votre ressource SignalR.

1. Cliquez sur **Contrôle d’accès (IAM)** pour afficher les paramètres de contrôle d’accès d’Azure SignalR.

   Voici un exemple de la page Contrôle d’accès (IAM) pour un groupe de ressources.

1. Cliquez sur l’onglet **Attributions de rôles** afin d’afficher les attributions de rôles pour cette étendue.

   La capture d’écran suivante montre un exemple de la page Contrôle d’accès (IAM) pour une ressource SignalR.

   ![Capture d’écran du contrôle d’accès](./media/authenticate/access-control.png)

1. Cliquez sur **Ajouter > Ajouter une attribution de rôle**.

1. Sous l’onglet **Rôles**, sélectionnez `SignalR App Server`.

1. Cliquez sur **Suivant**.

   ![Capture d’écran de l’ajout de l’attribution de rôle](./media/authenticate/add-role-assignment.png)

1. Dans l’onglet **Membres**, sous **Affecter l’accès à**, sélectionnez **Identité managée**.

1. Cliquez sur **Sélectionner des membres**.

1. Dans le volet **Sélectionner des identités managées**, sélectionnez **Identité managée affectée par le système > Machine virtuelle**

1. Recherchez et sélectionnez la machine virtuelle à laquelle vous souhaitez affecter le rôle.

1. Cliquez sur **Sélectionner** pour confirmer la sélection.

2. Cliquez sur **Suivant**.

   ![Capture d’écran de l’affectation d’un rôle à des identités managées](./media/authenticate/assign-role-to-managed-identities.png)

3. Cliquez sur **Examiner + affecter** pour confirmer la modification.

> [!IMPORTANT]
> Les attributions de rôles Azure peuvent prendre jusqu’à 30 minutes pour se propager.

Pour en savoir plus sur l’attribution et la gestion des attributions de rôles Azure, consultez les articles suivants :
- [Attribuer des rôles Azure à l’aide du portail Azure](../role-based-access-control/role-assignments-portal.md)
- [Attribuer des rôles Azure à l’aide de l’API REST](../role-based-access-control/role-assignments-rest.md)
- [Attribuer des rôles Azure à l’aide d’Azure PowerShell](../role-based-access-control/role-assignments-powershell.md)
- [Attribuer des rôles Azure à l’aide d’Azure CLI](../role-based-access-control/role-assignments-cli.md)
- [Attribuer des rôles Azure avec des modèles Azure Resource Manager](../role-based-access-control/role-assignments-template.md)

## <a name="configure-your-app"></a>Configuration de votre application

### <a name="app-server"></a>App Server

#### <a name="using-system-assigned-identity"></a>Utiliser une identité attribuée par le système

Vous pouvez utiliser [DefaultAzureCredential](/dotnet/api/overview/azure/identity-readme#defaultazurecredential) ou [ManagedIdentityCredential](/dotnet/api/azure.identity.managedidentitycredential) pour configurer vos points de terminaison SignalR.


Toutefois, la meilleure pratique consiste à utiliser `ManagedIdentityCredential` directement.

L’identité managée affectée par le système sera utilisée par défaut, mais **vérifiez que vous n’avez pas configuré de variables d’environnement** qui sont conservées par [EnvironmentCredential](/dotnet/api/azure.identity.environmentcredential) si vous utilisiez `DefaultAzureCredential`. Dans le cas contraire, il revient à utiliser `EnvironmentCredential` pour effectuer la requête, ce qui aboutira à une réponse `Unauthorized` dans la plupart des cas.

```C#
services.AddSignalR().AddAzureSignalR(option =>
{
    option.Endpoints = new ServiceEndpoint[]
    {
        new ServiceEndpoint(new Uri("https://<resource1>.service.signalr.net"), new ManagedIdentityCredential()),
    };
});
```

#### <a name="using-user-assigned-identity"></a>Utilisation d’une identité affectée par l’utilisateur

Fournissez `ClientId` pendant la création de l’objet `ManagedIdentityCredential`.

> [!IMPORTANT]
> Utilisez l’**ID client**, et non l’ID (principal) d’objet, même s’ils sont tous les deux GUID !

```C#
services.AddSignalR().AddAzureSignalR(option =>
{
    option.Endpoints = new ServiceEndpoint[]
    {
        var clientId = "<your identity client id>";
        new ServiceEndpoint(new Uri("https://<resource1>.service.signalr.net"), new ManagedIdentityCredential(clientId)),
    };
```

### <a name="azure-functions-signalr-bindings"></a>Liaisons Signalr Azure Functions

> [!WARNING]
> La liaison de déclencheur SignalR ne prenant pas encore en charge la connexion basée sur l’identité, des chaînes de connexion restent nécessaires.

Les liaisons Signalr Azure Functions utilisent des [paramètres d’application](/azure/azure-functions/functions-how-to-use-azure-function-app-settings) sur le portail ou [`local.settings.json`](/azure/azure-functions/functions-develop-local#local-settings-file) localement pour configurer des identités managées afin d’accéder vos ressources SignalR.

Vous pouvez avoir besoin d’un groupe de paires clé-valeur pour configurer une identité. Les clés de toutes les paires clé-valeur doivent commencer par un **préfixe de nom de connexion** (la valeur par défaut est `AzureSignalRConnectionString`) et un séparateur (`__` sur le portail et `:` au niveau local). Le préfixe peut être personnalisé avec la propriété de liaison [`ConnectionStringSetting`](/azure/azure-functions/functions-bindings-signalr-service).

#### <a name="using-system-assigned-identity"></a>Utiliser une identité attribuée par le système

Si vous configurez uniquement l’URI du service, `DefaultAzureCredential` est utilisé. Cela est utile lorsque vous souhaitez partager la même configuration sur Azure et l’environnement de développement local. Pour en savoir plus sur le fonctionnement de `DefaultAzureCredential`, consultez [DefaultAzureCredential](/dotnet/api/overview/azure/identity-readme#defaultazurecredential).

Sur le Portail Azure, définissez comme suit pour configurer un `DefaultAzureCredential`. Si vous vous assurez de ne pas configurer les [variables d’environnement répertoriées ici](/dotnet/api/overview/azure/identity-readme#environment-variables), l’identité affectée par le système sera utilisée pour l’authentification.
```
<CONNECTION_NAME_PREFIX>__serviceUri=https://<SIGNALR_RESOURCE_NAME>.service.signalr.net
```

Voici un exemple de configuration de `DefaultAzureCredential` dans le fichier `local.settings.json`. Notez qu’au niveau local, il n’y a pas d’identité managée et l’authentification via Visual Studio, Azure CLI et les comptes Azure PowerShell seront tentées dans l’ordre.
```json
{
  "Values": {
    "<CONNECTION_NAME_PREFIX>:serviceUri": "https://<SIGNALR_RESOURCE_NAME>.service.signalr.net"
  }
}
```

Si vous souhaitez utiliser l’identité affectée par le système indépendamment et sans l’influence d'[autres variables d’environnement](/dotnet/api/overview/azure/identity-readme#environment-variables), vous devez définir la clé `credential` avec le préfixe de nom de connexion sur `managedidentity`. Voici un exemple de paramètres d’application :

```
<CONNECTION_NAME_PREFIX>__serviceUri = https://<SIGNALR_RESOURCE_NAME>.service.signalr.net
<CONNECTION_NAME_PREFIX>__credential = managedidentity
```

#### <a name="using-user-assigned-identity"></a>Utilisation d’une identité affectée par l’utilisateur

Si vous souhaitez utiliser une identité affectée par l’utilisateur, vous devez affecter une clé `clientId` supplémentaire avec un préfixe de nom de connexion par rapport à l’identité affectée par le système. Voici un exemple de paramètres d’application :
```
<CONNECTION_NAME_PREFIX>__serviceUri = https://<SIGNALR_RESOURCE_NAME>.service.signalr.net
<CONNECTION_NAME_PREFIX>__credential = managedidentity
<CONNECTION_NAME_PREFIX>__clientId = <CLIENT_ID>
```
## <a name="next-steps"></a>Étapes suivantes

Consultez les articles associés suivants :
- [Vue d’ensemble d’Azure AD pour SignalR](signalr-concept-authorize-azure-active-directory.md)
- [Autoriser la requête aux ressources SignalR avec Azure AD à partir d’applications Azure](signalr-howto-authorize-application.md)
