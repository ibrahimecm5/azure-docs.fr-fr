---
title: Autoriser les requêtes aux ressources Web PubSub avec Azure AD à partir d’identités managées
description: Cet article fournit des informations sur l’autorisation des requêtes adressées aux ressources Web PubSub avec Azure AD à partir d’identités managées
author: terencefan
ms.author: tefa
ms.date: 11/08/2021
ms.service: azure-web-pubsub
ms.topic: conceptual
ms.openlocfilehash: 7f0b5cff34b74953970672d0d48c4f027f4811b0
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2021
ms.locfileid: "131997739"
---
# <a name="authorize-request-to-web-pubsub-resources-with-azure-ad-from-managed-identities"></a>Autoriser les requêtes aux ressources Web PubSub avec Azure AD à partir d’identités managées
Le service Azure Web PubSub prend en charge l’autorisation des requêtes Azure Active Directory (Azure AD) à partir des [identités managées pour les ressources Azure](../active-directory/managed-identities-azure-resources/overview.md). 

Cet article explique comment configurer votre ressource et vos codes Web PubSub pour autoriser la requête à une ressource Web PubSub à partir d’une identité managée.

## <a name="configure-managed-identities"></a>Configurer des identités managées

La première étape consiste à configurer des identités managées.

Il s’agit d’un exemple de configuration de `System-assigned managed identity` sur un `Virtual Machine` à l’aide du portail Azure.

1. Ouvrez le [portail Azure](https://portal.azure.com/), recherchez et sélectionnez une Machine virtuelle.
1. Sous la section **Paramètres**, sélectionnez **Identité**.
1. Dans l’onglet **Affecté par le système**, définissez l’**État** sur **Activé**.
   ![Capture d’écran de la machine virtuelle : identité](./media/aad-authorization/identity-virtual-machine.png)
1. Cliquez sur le bouton **Enregistrer** pour confirmer les modifications.

### <a name="how-to-create-user-assigned-managed-identities"></a>Comment créer des identités managées affectées par l’utilisateur
- [Créer une identité managée attribuée par l’utilisateur](../active-directory/managed-identities-azure-resources/how-manage-user-assigned-managed-identities.md#create-a-user-assigned-managed-identity)

### <a name="how-to-configure-managed-identities-on-other-platforms"></a>Comment configurer les identités managées sur d’autres plateformes

- [Configurer des identités managées pour ressources Azure sur une machine virtuelle en utilisant le portail Azure](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
- [Configurer des identités managées pour ressources Azure sur une machine virtuelle Azure en utilisant PowerShell](../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Configurer des identités managées pour ressources Azure sur une machine virtuelle Azure en utilisant Azure CLI](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Configurer des identités managées pour les ressources Azure sur une machine virtuelle Azure en utilisant des modèles](../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Configurer une machine virtuelle avec des identités managées pour ressources Azure en utilisant un SDK Azure](../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

### <a name="how-to-configure-managed-identities-for-app-service-and-azure-functions"></a>Comment configurer des identités managées avec App Service et Azure Functions

- [Comment utiliser des identités managées avec App Service et Azure Functions](../app-service/overview-managed-identity.md).

## <a name="add-role-assignments-on-azure-portal"></a>Ajouter des attributions de rôles sur le portail Azure  

Cet exemple montre comment attribuer un rôle `Web PubSub Service Owner` à une identité affectée par le système sur une ressource Web PubSub. 

> [!Note]
> Un rôle peut être affecté à n’importe quelle étendue, y compris le groupe d’administration, l’abonnement, le groupe de ressources ou une ressource unique. Pour en savoir plus sur l’étendue, consultez [Comprendre l’étendue pour Azure RBAC](../role-based-access-control/scope-overview.md)
1. Ouvrez le [portail Azure](https://portal.azure.com/) et accédez à votre ressource Web PubSub.

1. Cliquez sur **Contrôle d’accès (IAM)** pour afficher les paramètres de contrôle d’accès d’Azure Web PubSub.

   Voici un exemple de la page Contrôle d’accès (IAM) pour un groupe de ressources.

1. Cliquez sur l’onglet **Attributions de rôles** afin d’afficher les attributions de rôles pour cette étendue.

   La capture d’écran suivante montre un exemple de la page Contrôle d’accès (IAM) pour une ressource Web PubSub.

   ![Capture d’écran du contrôle d’accès](./media/aad-authorization/access-control.png)

1. Cliquez sur **Ajouter > Ajouter une attribution de rôle**.

1. Sous l’onglet **Rôles**, sélectionnez `Web PubSub Service Owner`.

1. Cliquez sur **Suivant**.

   ![Capture d’écran de l’ajout de l’attribution de rôle](./media/aad-authorization/add-role-assignment.png)

1. Dans l’onglet **Membres**, sous **Affecter l’accès à**, sélectionnez **Identité managée**.

1. Cliquez sur **Sélectionner des membres**.

1. Dans le volet **Sélectionner des identités managées**, sélectionnez **Identité managée affectée par le système > Machine virtuelle**

1. Recherchez et sélectionnez la machine virtuelle à laquelle vous souhaitez affecter le rôle.

1. Cliquez sur **Sélectionner** pour confirmer la sélection.

2. Cliquez sur **Suivant**.

   ![Capture d’écran de l’affectation d’un rôle à des identités managées](./media/aad-authorization/assign-role-to-managed-identities.png)

3. Cliquez sur **Examiner + affecter** pour confirmer la modification.

> [!IMPORTANT]
> Les attributions de rôles Azure peuvent prendre jusqu’à 30 minutes pour se propager.
Pour en savoir plus sur l’attribution et la gestion des attributions de rôles Azure, consultez les articles suivants :
- [Attribuer des rôles Azure à l’aide du portail Azure](../role-based-access-control/role-assignments-portal.md)
- [Attribuer des rôles Azure à l’aide de l’API REST](../role-based-access-control/role-assignments-rest.md)
- [Attribuer des rôles Azure à l’aide d’Azure PowerShell](../role-based-access-control/role-assignments-powershell.md)
- [Attribuer des rôles Azure à l’aide d’Azure CLI](../role-based-access-control/role-assignments-cli.md)
- [Attribuer des rôles Azure avec des modèles Azure Resource Manager](../role-based-access-control/role-assignments-template.md)

## <a name="sample-codes-while-configuring-your-server"></a>Exemples de code pour la configuration de votre serveur

### <a name="using-system-assigned-identity"></a>Utiliser une identité attribuée par le système

Vous pouvez utiliser [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) ou [ManagedIdentityCredential](/dotnet/api/azure.identity.managedidentitycredential) pour configurer vos points de terminaison Web PubSub tout en utilisant une identité affectée par le système.

Toutefois, la meilleure pratique consiste à utiliser `ManagedIdentityCredential` directement.

L’identité managée affectée par le système sera utilisée par défaut, mais **vérifiez que vous n’avez pas configuré de variables d’environnement** qui sont conservées par [EnvironmentCredential](/dotnet/api/azure.identity.environmentcredential) si vous utilisiez `DefaultAzureCredential`. Dans le cas contraire, il revient à utiliser `EnvironmentCredential` pour effectuer la requête, ce qui aboutira à une réponse `401 Unauthorized` dans la plupart des cas.

Voici un exemple de code pour C#.

```C#
var endpoint = new Uri("https://<resource1>.webpubsub.azure.com");
var client = new WebPubSubServiceClient(endpoint, "hub", new ManagedIdentityCredential());
```

Pour d’autres exemples sur les langages pris en charge, consultez [Java](), [JavaScript](), [Python]().

### <a name="using-user-assigned-identity"></a>Utilisation d’une identité affectée par l’utilisateur

Fournissez simplement `ClientId` pendant la création de l’objet `ManagedIdentityCredential`.

> [!IMPORTANT]
> Utilisez l’**ID client**, et non l’ID (principal) d’objet, même s’ils sont similaires.

Voici un exemple de code pour C#.

```C#
var endpoint = new Uri("https://<resource1>.webpubsub.azure.com");
var clientId = "<your user-assigned identity client id>";
var client = new WebPubSubServiceClient(endpoint, "hub", new ManagedIdentityCredential(clientId));
```

Pour d’autres exemples sur les langages pris en charge, consultez [Java](), [JavaScript](), [Python]().

## <a name="next-steps"></a>Étapes suivantes

Consultez les articles associés suivants :
- [Présentation d’Azure AD pour Web PubSub](concept-azure-ad-authorization.md)
- [Autoriser la requête aux ressources Web PubSub avec Azure AD à partir d’applications Azure](howto-authorize-from-application.md)