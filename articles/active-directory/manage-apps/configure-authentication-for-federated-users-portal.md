---
title: Configurer l’accélération automatique de la connexion à l’aide de la découverte du domaine d’accueil
titleSuffix: Azure AD
description: Découvrez comment configurer la stratégie de découverte du domaine d’accueil pour l’authentification Azure Active Directory pour les utilisateurs fédérés (et notamment l’accélération automatique et les indications de domaine).
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 08/13/2021
ms.author: davidmu
ms.custom: seoapril2019
ms.collection: M365-identity-device-management
ms.reviewer: hirsin
ms.openlocfilehash: c0d182f2e0eb4fc3efe21be1c0a4e6111ad8de39
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132555486"
---
# <a name="configure-sign-in-behavior"></a>Configurer le comportement de connexion

Cet article est une introduction à la configuration du comportement d’authentification Azure Active Directory (Azure AD) pour les utilisateurs fédérés à l’aide de la stratégie de découverte du domaine d’accueil.  Il aborde l’utilisation de l’accélération automatique pour ignorer l’écran d’entrée du nom d’utilisateur et transférer automatiquement les utilisateurs vers les points de terminaison de connexion fédérés.  Microsoft déconseille la configuration de l’accélération automatique, car cela peut empêcher l’utilisation de méthodes d’authentification plus fortes telles que FIDO (First Identity Online) et entrave la collaboration.

## <a name="prerequisites"></a>Prérequis

Pour configurer la stratégie découverte du domaine pour une application dans Azure AD, vous avez besoin des éléments suivants :

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Un des rôles suivants : Administrateur général, Administrateur d’application cloud, Administrateur d’application ou propriétaire du principal de service.
- La dernière préversion des cmdlets Azure AD PowerShell.

## <a name="set-up-an-hrd-policy-on-an-application"></a>Définir une stratégie de découverte du domaine d’accueil sur une application

Nous allons utiliser des applets de commande PowerShell Azure AD dans le cadre de quelques scénarios, dont les suivants :

- Configurer une stratégie de découverte du domaine d’accueil afin d’exécuter l’accélération automatique pour une application dans un client avec un seul domaine fédéré.

- Configurer une stratégie de découverte du domaine d’accueil afin d’exécuter l’accélération automatique pour une application vers un des domaines parmi ceux qui sont vérifiés pour votre client.

- Configurer une stratégie de découverte du domaine d’accueil afin d’effectuer l’authentification directe du nom d’utilisateur/mot de passe sur Azure AD pour un utilisateur fédéré.

- Répertorier les applications pour lesquelles une stratégie est configurée.

Dans les exemples suivants, vous créez, mettez à jour, liez et supprimez des stratégies sur des principaux de service d’application dans Azure AD.

1. Avant de commencer, exécutez la commande Connect pour vous connecter à votre compte d'administrateur Azure AD.

    ```powershell
    Connect-AzureAD -Confirm
    ```

1. Exécutez la commande suivante pour afficher toutes les stratégies dans votre organisation :

    ```powershell
    Get-AzureADPolicy
    ```

Si aucun résultat n’est retourné, cela signifie qu’aucune stratégie n’est créée dans votre locataire.

Dans cet exemple, vous créez une stratégie qui, lorsqu’elle est assignée à une application :

- accélère automatiquement les utilisateurs vers un écran de connexion AD FS quand ils se connectent à une application lorsque votre client comporte un seul domaine.
- Accélère automatiquement les utilisateurs vers un écran de connexion AD FS quand il existe plusieurs domaines fédérés dans votre client.
- Active la connexion du nom d’utilisateur/mot de passe non interactive directement dans Azure AD pour les utilisateurs fédérés dans les applications auxquelles la stratégie est affectée.

## <a name="create-an-hrd-policy"></a>Créer une stratégie de découverte du domaine d’accueil

La stratégie suivante accélère automatiquement les utilisateurs vers un écran de connexion AD FS quand ils se connectent à une application lorsque votre client comporte un seul domaine.

```powershell
New-AzureADPolicy -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AccelerateToFederatedDomain`":true}}") -DisplayName BasicAutoAccelerationPolicy -Type HomeRealmDiscoveryPolicy
```

La stratégie suivante accélère automatiquement les utilisateurs vers un écran de connexion AD FS quand il existe plusieurs domaines fédérés dans votre client. Si vous avez plusieurs domaines fédérés qui authentifient les utilisateurs pour les applications, vous devez spécifier le domaine pour l’accélération automatique.

```powershell
New-AzureADPolicy -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AccelerateToFederatedDomain`":true, `"PreferredDomain`":`"federated.example.edu`"}}") -DisplayName MultiDomainAutoAccelerationPolicy -Type HomeRealmDiscoveryPolicy
```

Pour créer une stratégie pour activer l’authentification du nom d’utilisateur/mot de passe pour les utilisateurs fédérés directement avec Azure AD pour des applications spécifiques, exécutez la commande suivante :

```powershell
New-AzureADPolicy -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AllowCloudPasswordValidation`":true}}") -DisplayName EnableDirectAuthPolicy -Type HomeRealmDiscoveryPolicy
```

Pour afficher votre nouvelle stratégie et obtenir son **ID d’objet**, exécutez la commande ci-après :

```powershell
Get-AzureADPolicy
```

Pour appliquer une stratégie de découverte du domaine d’accueil après l’avoir créée, vous pouvez l’affecter à plusieurs principaux de service d’application.

## <a name="locate-the-service-principal-to-which-to-assign-the-policy"></a>Rechercher le principal de service auquel affecter la stratégie

Vous avez besoin de **l’ID d’objet** des principaux de service auxquels vous souhaitez affecter la stratégie. Il existe plusieurs façons de rechercher **l’ID d’objet** des principaux de service.

Vous pouvez utiliser le [portail Azure](https://portal.azure.com), ou vous pouvez interroger [Microsoft Graph](/graph/api/resources/serviceprincipal). Vous pouvez également accéder à [l’outil Afficheur Graph](https://developer.microsoft.com/graph/graph-explorer) et vous connecter à votre compte Azure AD pour voir tous les principaux de service de votre organisation.

Étant donné que vous utilisez PowerShell, vous pouvez utiliser l’applet de commande suivante pour lister les principaux de service et leurs identifiants.

```powershell
Get-AzureADServicePrincipal
```

## <a name="assign-the-policy-to-your-service-principal"></a>Affecter la stratégie à votre principal de service

Une fois que vous avez **l’ID d’objet** du principal de service de l’application pour laquelle vous souhaitez configurer l’accélération automatique, exécutez la commande suivante. Cette commande associe la stratégie de découverte du domaine d’accueil que vous avez créée à l’étape 1 au principal de service que vous avez localisé à l’étape 2.

```powershell
Add-AzureADServicePrincipalPolicy -Id <ObjectID of the Service Principal> -RefObjectId <ObjectId of the Policy>
```

Vous pouvez répéter cette commande pour chaque principal de service auquel vous souhaitez ajouter la stratégie.

Dans le cas où une stratégie HomeRealmDiscovery est déjà attribuée à une application, vous ne pouvez pas en ajouter une deuxième.  Dans ce cas, modifiez la définition de la stratégie de découverte du domaine d’accueil affectée à l’application pour ajouter des paramètres supplémentaires.

### <a name="check-which-application-service-principals-your-hrd-policy-is-assigned-to"></a>Vérifier les principaux de service d’application auxquels votre stratégie de découverte du domaine d’accueil est affectée

Pour déterminer les applications pour lesquelles la stratégie de découverte du domaine d’accueil est configurée, utilisez l’applet de commande **Get-AzureADPolicyAppliedObject**. Passez-lui **l’ID d’objet** de la stratégie sur laquelle porte la vérification.

```powershell
Get-AzureADPolicyAppliedObject -id <ObjectId of the Policy>

Try the application to check that the new policy is working.

### List the applications for which HRD policy is configured

1. List all policies that were created in your organization

```powershell
Get-AzureADPolicy
```

Notez **l’ID d’objet** de la stratégie dont vous souhaitez répertorier les affectations.

2. Répertorier les principaux de service auxquels la stratégie est affectée

```powershell
Get-AzureADPolicyAppliedObject -id <ObjectId of the Policy>
```

## <a name="remove-an-hrd-policy-from-an-application"></a>Supprimer une stratégie de découverte du domaine d’accueil dans une application

1. Obtenir l’ID d’objet

Utilisez l’exemple précédent pour obtenir **l’ID d’objet** de la stratégie et celui du principal de service d’application dont vous souhaitez la supprimer.

2. Supprimer l’affectation de stratégie du principal de service d’application

```powershell
Remove-AzureADServicePrincipalPolicy -id <ObjectId of the Service Principal>  -PolicyId <ObjectId of the policy>
```

3. Vérifier la suppression en listant les principaux de service auxquels la stratégie est affectée

```powershell
Get-AzureADPolicyAppliedObject -id <ObjectId of the Policy>
```

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur le fonctionnement de la découverte de domaine d’hébergement dans Azure AD, consultez [Découverte de domaine d’hébergement pour une application dans Azure AD](home-realm-discovery-policy.md).