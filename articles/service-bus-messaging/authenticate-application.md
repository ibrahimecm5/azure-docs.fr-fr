---
title: Authentifier une application pour accéder aux entités Azure Service Bus
description: Cet article fournit des informations sur l’authentification d’une application avec Azure Active Directory pour accéder aux entités Azure Service Bus (files d’attente, rubriques, etc.)
ms.topic: conceptual
ms.date: 06/14/2021
ms.custom: subject-rbac-steps
ms.openlocfilehash: 3ca9284746460b3ab3369b4c9c00d7951784cff8
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131064009"
---
# <a name="authenticate-and-authorize-an-application-with-azure-active-directory-to-access-azure-service-bus-entities"></a>Authentifier et autoriser une application avec Azure Active Directory pour accéder aux entités Azure Service Bus
Azure Service Bus prend en charge l’utilisation d’Azure Active Directory (Azure AD) pour autoriser les requêtes d’accès aux entités Service Bus (files d’attente, rubriques, abonnements ou filtres). Avec Azure AD, vous pouvez utiliser le contrôle d’accès en fonction du rôle d’Azure (Azure RBAC) pour accorder des autorisations à un principal de sécurité, qui peut être un utilisateur, un groupe ou un principal de service d’application. Pour en savoir plus sur les rôles et les attributions de rôles, consultez [Comprendre les différents rôles](../role-based-access-control/overview.md).

## <a name="overview"></a>Vue d’ensemble
Quand un principal de sécurité (un utilisateur, un groupe ou une application) tente d’accéder à une entité Service Bus, la requête doit être autorisée. Avec Azure AD, l’accès à une ressource est un processus en deux étapes. 

 1. Pour commencer, l’identité du principal de sécurité est authentifiée, et un jeton OAuth 2.0 est renvoyé. Le nom de ressource à utiliser pour demander un jeton est `https://servicebus.azure.net`.
 1. Ensuite, ce jeton est transmis dans une requête adressée au service Service Bus pour autoriser l’accès à la ressource spécifiée.

L’étape d’authentification nécessite qu’une requête d’application contienne un jeton d’accès OAuth 2.0 au moment de l’exécution. Si une application s’exécute à partir d’une entité Azure telle qu’une machine virtuelle Azure, un groupe de machines virtuelles identiques ou une application Azure Function, elle peut utiliser une identité managée pour accéder aux ressources. Pour plus d’informations sur l’authentification des requêtes adressées par une identité managée au service Service Bus, consultez [Authentifier l’accès aux ressources Azure Service Bus avec Azure Active Directory et les identités managées pour les ressources Azure](service-bus-managed-service-identity.md). 

L’étape d’autorisation exige qu’un ou plusieurs rôles Azure soient attribués au principal de sécurité. Azure Service Bus fournit des rôles Azure qui englobent des ensembles d’autorisations pour les ressources Service Bus. Les rôles qui sont attribués à un principal de sécurité déterminent les autorisations dont disposera le principal. Pour plus d’informations sur l’attribution de rôles Azure dans Azure Service Bus, consultez [Rôles Azure intégrés pour Azure Service Bus](#azure-built-in-roles-for-azure-service-bus). 

Les applications natives et applications web qui adressent des requêtes à Service Bus peuvent également autoriser l’accès avec Azure AD. Cet article explique comment demander un jeton d’accès et l’utiliser pour autoriser les demandes de ressources Service Bus. 


## <a name="assigning-azure-roles-for-access-rights"></a>Attribution de rôles Azure pour les droits d’accès
Azure Active Directory (Azure AD) autorise les droits d’accès aux ressources sécurisées par le biais [d’Azure RBAC](../role-based-access-control/overview.md). Azure Service Bus définit un ensemble de rôles Azure intégrés qui englobent les ensembles d’autorisations couramment utilisés pour accéder aux entités Service Bus. Il est également possible de définir des rôles personnalisés pour accéder aux données.

Lorsqu’un rôle Azure est attribué à un principal de sécurité Azure AD, Azure octroie l’accès à ces ressources pour ce principal de sécurité. L’accès peut être limité au niveau de l’abonnement, du groupe de ressources ou de l’espace de noms Service Bus. Un principal de sécurité Azure AD peut correspondre à un utilisateur, à un groupe, à un principal de service d’application ou à une [identité managée pour les ressources Azure](../active-directory/managed-identities-azure-resources/overview.md).

## <a name="azure-built-in-roles-for-azure-service-bus"></a>Rôles Azure intégrés pour Azure Service Bus
Pour Azure Service Bus, la gestion des espaces de noms et de toutes les ressources associées via le Portail Azure et l’API de gestion des ressources Azure est déjà protégée à l’aide du modèle Azure RBAC. Azure fournit les rôles Azure intégrés ci-dessous pour autoriser l’accès à un espace de noms Service Bus :

- [Propriétaire de données Azure Service Bus](../role-based-access-control/built-in-roles.md#azure-service-bus-data-owner) : permet l’accès aux données de l’espace de noms Service Bus et ses entités (files d’attente, rubriques, abonnements et filtres).
- [Expéditeur de données Azure Service Bus](../role-based-access-control/built-in-roles.md#azure-service-bus-data-sender) : utilisez ce rôle pour autoriser l’accès en envoi à l’espace de noms Service Bus et à ses entités.
- [Récepteur de données Azure Service Bus](../role-based-access-control/built-in-roles.md#azure-service-bus-data-receiver) : utilisez ce rôle pour autoriser l’accès en réception à l’espace de noms Service Bus et à ses entités. 

## <a name="resource-scope"></a>Étendue des ressources 
Avant d’attribuer un rôle Azure à un principal de sécurité, déterminez l’étendue de l’accès dont doit disposer le principal de sécurité. Selon les bonnes pratiques, il est toujours préférable d’accorder la plus petite étendue possible.

La liste suivante décrit les niveaux auxquels vous pouvez étendre l’accès aux ressources Service Bus, en commençant par la plus petite étendue :

- **File d’attente**, **rubrique** ou **abonnement** : l’attribution de rôle s’applique à l’entité Service Bus spécifique. Actuellement, le Portail Azure ne prend pas en charge l’affectation d’utilisateurs, de groupes ou d’identités managées aux rôles Azure Service Bus au niveau de l’abonnement. 
- **Espace de noms Service Bus** : l’attribution de rôle s’étend à toute la topologie de Service Bus sous l’espace de noms et au groupe de consommateurs qui lui est associé.
- **Groupe de ressources** : l’attribution de rôle s’applique à toutes les ressources Service Bus sous le groupe de ressources.
- **Abonnement**: l’attribution de rôle s’applique à toutes les ressources Service Bus dans tous les groupes de ressources de l’abonnement.

> [!NOTE]
> Gardez à l’esprit que la propagation des attributions de rôles Azure peut prendre cinq minutes. 

Pour plus d’informations sur la définition des rôles intégrés, consultez [Comprendre les définitions de rôles](../role-based-access-control/role-definitions.md#control-and-data-actions). Pour plus d’informations sur la création de rôles personnalisés Azure, consultez [Rôles personnalisés Azure](../role-based-access-control/custom-roles.md).


## <a name="assign-azure-roles-using-the-azure-portal"></a>Attribuer des rôles Azure à l’aide du portail Azure  
Attribuez un des [rôles Service Bus](#azure-built-in-roles-for-azure-service-bus) au principal de service de l’application à l’étendue souhaitée (espace de noms Service Bus, groupe de ressources, abonnement). Pour connaître les étapes détaillées, consultez [Attribuer des rôles Azure à l’aide du portail Azure](../role-based-access-control/role-assignments-portal.md).

Une fois que vous avez défini le rôle et son étendue, vous pouvez tester ce comportement à l’aide des [exemples disponibles sur GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/RoleBasedAccessControl).

## <a name="authenticate-from-an-application"></a>Authentifier à partir d’une application
L’un des principaux avantages d’utiliser Azure AD avec Service Bus est que vous n’avez plus besoin de stocker vos informations d’identification dans votre code. À la place, vous pouvez demander un jeton d’accès OAuth 2.0 sur la plateforme d’identités Microsoft. Azure AD authentifie le principal de sécurité (un utilisateur, un groupe ou un principal de service) qui exécute l’application. Si l’authentification réussit, Azure AD retourne le jeton d’accès à l’application, laquelle peut ensuite l’utiliser pour autoriser les requêtes adressées à Azure Service Bus.

Les sections suivantes vous montrent comment configurer votre application native ou une application web pour l’authentification avec la plateforme d’identités Microsoft 2.0. Pour plus d’informations sur la plateforme d’identité Microsoft 2.0, veuillez consulter l’article [Présentation de la plateforme d’identités Microsoft (v2.0)](../active-directory/develop/v2-overview.md).

Pour avoir une vue d’ensemble du flux d’octroi de code OAuth 2.0, consultez [Autoriser l’accès aux applications web Azure Active Directory à l’aide du flux d’octroi de code OAuth 2.0](../active-directory/develop/v2-oauth2-auth-code-flow.md).

### <a name="register-your-application-with-an-azure-ad-tenant"></a>Inscrire votre application à un locataire Azure AD
La première étape d’utilisation d’Azure AD pour autoriser l’accès aux entités Service Bus est d’inscrire votre application cliente avec un locataire Azure AD à partir du [portail Azure](https://portal.azure.com/). Lorsque vous inscrivez votre application cliente, vous fournissez des informations la concernant à Azure AD. Azure AD fournit ensuite un ID de client (appelé aussi ID d’application) que vous pouvez utiliser pour associer votre application au runtime Azure AD. Pour en savoir plus sur l’ID de client, consultez [Objets application et principal du service dans Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md). 

Les illustrations suivantes montrent les étapes d’inscription d’une application web :

![Inscrire une application](./media/authenticate-application/app-registrations-register.png)

> [!Note]
> Si vous inscrivez votre application comme une application native, vous pouvez spécifier n’importe quel URI valide pour l’URI de redirection. Pour les applications natives, cette valeur ne devra pas être une URL réelle. Pour les applications web, l’URI de redirection doit être un URI valide, car il spécifie l’URL à laquelle les jetons sont fournis.

Une fois votre application inscrite, **l’ID (client) d’application** se trouve sous **Paramètres** :

![ID de l’application inscrite](./media/authenticate-application/application-id.png)

Pour plus d’informations sur l’inscription d’une application dans Azure AD, consultez [Intégration d’applications à Azure Active Directory](../active-directory/develop/quickstart-register-app.md).

> [!IMPORTANT]
> Notez les valeurs de **TenantId** et **ApplicationId**, car vous en aurez besoin pour exécuter l’application.

### <a name="create-a-client-secret"></a>Créer une clé secrète client   
L’application a besoin d’une clé secrète client pour prouver son identité lors de la requête de jeton. Pour ajouter le secret client, effectuez ces étapes.

1. Accédez à la page d’inscription de votre application dans le portail Azure si vous n’y êtes pas déjà.
1. Dans le menu de gauche, sélectionnez **Certificats et secrets**.
1. Sous **Secrets client**, sélectionnez **Nouveau secret client** pour créer un secret.

    ![Nouveau secret client (bouton)](./media/authenticate-application/new-client-secret-button.png)
1. Entrez une description pour le secret, choisissez un intervalle d’expiration, puis sélectionnez **Ajouter**.

    ![Ajouter un secret client (page)](./media/authenticate-application/add-client-secret-page.png)
1. Copiez immédiatement la valeur de la nouvelle clé secrète dans un emplacement sécurisé. La valeur de remplissage s’affiche pour vous une seule fois.

    ![Clé secrète client](./media/authenticate-application/client-secret.png)

### <a name="permissions-for-the-service-bus-api"></a>Autorisations pour l’API Service Bus
Si votre application est une application console, vous devez inscrire une application native et ajouter des autorisations d’API pour **Microsoft.ServiceBus** dans l’ensemble des **autorisations requises**. Les applications natives ont également besoin d’un **URI de redirection** dans Azure AD, qui servira d’identificateur. Cet URI n’est pas obligatoirement une destination réseau. Utilisez `https://servicebus.microsoft.com` pour cet exemple, étant donné que l’exemple de code utilise déjà cet URI.

### <a name="authenticating-the-service-bus-client"></a>Authentification du client Service Bus
Une fois que vous avez inscrit votre application et que vous lui avez accordé les autorisations nécessaires pour envoyer/recevoir des données dans Azure Service Bus, vous pouvez authentifier votre client avec les informations d’identification de la clé secrète client, ce qui vous permettra d’effectuer des requêtes sur Azure Service Bus.

Pour obtenir la liste de scénarios pour lesquels l’acquisition de jetons est pris en charge, veuillez consulter la section [Scénarios](https://aka.ms/msal-net-scenarios) du référentiel GitHub [Microsoft Authentication Library (MSAL) pour .NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet).

À l’aide de la bibliothèque [Azure.Messaging.ServiceBus](https://www.nuget.org/packages/Azure.Messaging.ServiceBus) la plus récente, vous pouvez authentifier le [ServiceBusClient](/dotnet/api/azure.messaging.servicebus.servicebusclient) avec un [ClientSecretCredential](/dotnet/api/azure.identity.clientsecretcredential), qui est défini dans la bibliothèque [Azure.Identity](https://www.nuget.org/packages/Azure.Identity).

```cs
TokenCredential credential = new ClientSecretCredential("<tenant_id>", "<client_id>", "<client_secret>");
var client = new ServiceBusClient("<fully_qualified_namespace>", credential);
```

Si vous utilisez les anciens packages .NET, reportez-vous aux exemples de RoleBasedAccessControl dans le [référentiel d’exemples azure-service-bus](https://github.com/Azure/azure-service-bus).

## <a name="next-steps"></a>Étapes suivantes
- Pour plus d’informations sur Azure RBAC, consultez [Qu’est-ce que le contrôle d’accès en fonction du rôle Azure (Azure RBAC)](../role-based-access-control/overview.md) ?
- Pour apprendre à attribuer et gérer les rôles Azure avec Azure PowerShell, Azure CLI ou l’API REST, consultez les articles suivants :
    - [Ajouter ou supprimer des attributions de rôle Azure à l’aide d’Azure PowerShell](../role-based-access-control/role-assignments-powershell.md)  
    - [Ajouter ou supprimer des attributions de rôle Azure à l’aide d’Azure CLI](../role-based-access-control/role-assignments-cli.md)
    - [Ajouter ou supprimer des attributions de rôle Azure à l’aide de l’API REST](../role-based-access-control/role-assignments-rest.md)
    - [Ajouter ou supprimer des attributions de rôle Azure à l’aide de modèles Azure Resource Manager](../role-based-access-control/role-assignments-template.md)

Pour en savoir plus sur la messagerie Service Bus, voir les rubriques suivantes.

- [Exemples Azure RBAC pour Service Bus](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/RoleBasedAccessControl)
- [Files d’attente, rubriques et abonnements Service Bus](service-bus-queues-topics-subscriptions.md)
- [Prise en main des files d’attente Service Bus](service-bus-dotnet-get-started-with-queues.md)
- [Utilisation des rubriques et abonnements Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)
