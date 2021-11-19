---
title: Gérer le consentement pour les applications et évaluer les demandes de consentement
description: Apprenez à gérer les demandes de consentement lorsque le consentement de l’utilisateur est désactivé ou limité, puis à évaluer une demande de consentement de l’administrateur au niveau du locataire pour une application dans Azure Active Directory.
titleSuffix: Azure AD
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 08/25/2021
ms.author: davidmu
ms.reviewer: phsignor
ms.openlocfilehash: e871bfe8f6fa787a3ffffc4fa0efb2df6d920a0e
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132548384"
---
# <a name="manage-consent-to-applications-and-evaluate-consent-requests"></a>Gérer le consentement pour les applications et évaluer les demandes de consentement

Microsoft recommande de [limiter le consentement de l’utilisateur](../../active-directory/manage-apps/configure-user-consent.md) afin que les utilisateurs ne puissent consentir qu’aux applications provenant d’éditeurs vérifiés et uniquement pour les autorisations que vous sélectionnez. Pour les applications qui ne respectent pas cette stratégie, le processus décisionnel sera centralisé auprès de l’équipe d’administrateurs de la sécurité et des identités de votre organisation.

Une fois que le consentement de l’utilisateur final est désactivé ou limité, plusieurs points importants sont à prendre en considération pour garantir que votre organisation reste sécurisée tout en permettant l’utilisation d’applications critiques pour l’entreprise. Ces étapes sont essentielles pour réduire l’impact sur l’équipe de support de votre organisation et les administrateurs informatiques tout en empêchant l’utilisation de comptes non gérés dans les applications tierces.

## <a name="process-changes-and-education"></a>Changements de processus et formation

 1. Activez le [workflow de consentement de l’administrateur](configure-admin-consent-workflow.md) pour permettre aux utilisateurs de demander l’approbation de l’administrateur directement à partir de l’écran de consentement.

 2. Assurez-vous que tous les administrateurs comprennent la [structure d’autorisations et de consentement](../develop/consent-framework.md), le fonctionnement de l’[invite de consentement](../develop/application-consent-experience.md) et l’[évaluation d’une demande de consentement de l’administrateur au niveau du locataire](#evaluating-a-request-for-tenant-wide-admin-consent).
 3. Passez en revue les processus existants de votre organisation pour permettre aux utilisateurs de demander l’approbation de l’administrateur pour une application et d’effectuer des mises à jour si nécessaire. Si les processus changent :
    * Mettez à jour la documentation appropriée, la supervision, l’automatisation, etc.
    * Communiquez les changements de processus à tous les utilisateurs, développeurs, équipes de support et administrateurs informatiques concernés.

## <a name="auditing-and-monitoring"></a>Audit et supervision

1. [Auditez les applications et les autorisations accordées](../../security/fundamentals/steps-secure-identity.md#audit-apps-and-consented-permissions) dans votre organisation afin de vous assurer qu’aucun accès aux données n’a déjà été accordé à des applications indésirables ou suspectes.

2. Consultez [Détecter et résoudre les problèmes d’octroi illégal de consentement dans Office 365](/microsoft-365/security/office-365-security/detect-and-remediate-illicit-consent-grants) pour obtenir d’autres bonnes pratiques et des dispositifs de protection contre les applications suspectes demandant un consentement OAuth.

3. Si votre organisation dispose de la licence appropriée :

    * Utilisez d’autres [fonctionnalités d’audit d’application OAuth dans Microsoft Defender for Cloud Apps](/cloud-app-security/investigate-risky-oauth).
    * Utilisez l’activité connexe [Classeurs Azure Monitor pour superviser les autorisations et le consentement](../reports-monitoring/howto-use-azure-monitor-workbooks.md). Le classeur *Consent Insights* fournit une vue des applications par nombre de demandes de consentement ayant échoué. Il peut être utile de classer par ordre de priorité les applications afin de permettre aux administrateurs de les passer en revue et de décider de leur accorder ou non le consentement de l’administrateur.

### <a name="additional-considerations-for-reducing-friction"></a>Autres points à prendre en considération pour réduire les frictions

Pour réduire l’impact sur les applications approuvées critiques pour l’entreprise qui sont déjà en cours d’utilisation, accordez de manière proactive le consentement de l’administrateur aux applications qui ont un nombre élevé d’octrois de consentement de l’utilisateur :

1. Faites l’inventaire des applications déjà ajoutées à votre organisation et qui sont très utilisées, en fonction des journaux de connexion ou de l’activité d’octroi de consentement. Un script [PowerShell](https://gist.github.com/psignoret/41793f8c6211d2df5051d77ca3728c09) peut être utilisé pour découvrir de façon facile et rapide des applications avec un grand nombre d’octrois de consentement de l’utilisateur.

2. Évaluez les principales applications qui n’ont pas encore reçu le consentement de l’administrateur.

   > [!IMPORTANT]
   > Évaluez soigneusement les applications avant d’accorder le consentement de l’administrateur au niveau du locataire, même si de nombreux utilisateurs de l’organisation ont déjà consenti pour eux-mêmes.

3. Pour chaque application approuvée, accordez le consentement de l’administrateur au niveau du locataire avec l’une des méthodes décrites ci-dessous.

4. Pour chaque application approuvée, [limitez l’accès utilisateur](configure-user-consent.md).

## <a name="evaluating-a-request-for-tenant-wide-admin-consent"></a>Évaluation d’une demande de consentement de l’administrateur au niveau du locataire

L’octroi du consentement de l’administrateur au niveau du locataire est une opération sensible.  Les autorisations sont accordées pour le compte de l’ensemble de l’organisation et peuvent comprendre des autorisations permettant de tenter des opérations à privilèges élevés. Par exemple, la gestion des rôles, l’accès total à toutes les boîtes aux lettres ou à tous les sites, ainsi que l’emprunt total d’identité utilisateur.

Avant d’accorder le consentement de l’administrateur au niveau du locataire, vous devez être sûr de faire confiance à l’application et à l’éditeur de l’application, pour le niveau d’accès que vous accordez. Si vous n’êtes pas certain de savoir qui contrôle l’application et pourquoi l’application demande des autorisations, *n’accordez pas de consentement*.

La liste suivante fournit quelques recommandations à prendre en compte lors de l’évaluation d’une demande d’octroi de consentement de l’administrateur.

* **Comprendre les [autorisations et la structure de consentement](../develop/consent-framework.md) dans la plateforme d’identités Microsoft.**

* **Comprendre la différence entre les [autorisations déléguées et les autorisations d’application](../develop/v2-permissions-and-consent.md#permission-types).**

   Les autorisations d’application permettent à l’application d’accéder aux données de toute l’organisation, sans aucune interaction utilisateur. Les autorisations déléguées permettent à l’application d’agir pour le compte d’un utilisateur qui a été connecté à l’application à un moment donné.

* **Comprendre les autorisations demandées.**

   Les autorisations demandées par l’application sont listées dans l’[invite de consentement](../develop/application-consent-experience.md). Développez le titre de l’autorisation pour en voir la description. La description des autorisations d’application se termine généralement par « sans utilisateur connecté ». La description des autorisations déléguées se termine généralement par « pour le compte de l’utilisateur connecté ». Les autorisations pour l’API Microsoft Graph sont décrites dans [Informations de référence sur les autorisations Microsoft Graph](/graph/permissions-reference) – consultez la documentation d’autres API pour comprendre les autorisations qu’elles exposent.

   Si vous ne comprenez pas une autorisation demandée, *n’accordez pas de consentement*.

* **Comprendre quelle application demande des autorisations et qui a publié l’application.**

   Méfiez-vous des applications malveillantes qui essaient de ressembler à d’autres applications.

   Si vous doutez de la légitimité d’une application ou de son éditeur, *n’accordez pas de consentement*. Recherchez plutôt une confirmation supplémentaire (par exemple, directement auprès de l’éditeur de l’application).

* **Vérifiez que les autorisations demandées sont en phase avec les fonctionnalités que vous attendez de l’application.**

   Par exemple, une application qui offre la gestion de sites SharePoint peut demander un accès délégué pour lire toutes les collections de sites, mais n’a pas nécessairement besoin d’un accès total à toutes les boîtes aux lettres ni des privilèges d’emprunt total d’identité dans le répertoire.

   Si vous pensez que l’application demande plus d’autorisations que nécessaire, *n’accordez pas de consentement*. Pour obtenir plus de détails, contactez l’éditeur de l’application.

## <a name="granting-consent-as-an-administrator"></a>Octroi de consentement en tant qu’administrateur

### <a name="granting-tenant-wide-admin-consent"></a>Octroi de consentement de l’administrateur au niveau du locataire

Pour des instructions pas à pas sur l’octroi d’un consentement de l’administrateur au niveau du locataire en utilisant le portail Azure, Azure AD PowerShell ou l’invite de consentement elle-même, consultez [Accorder le consentement de l’administrateur au niveau locataire à une application](grant-admin-consent.md).

### <a name="granting-consent-on-behalf-of-a-specific-user"></a>Octroi de consentement pour le compte d’un utilisateur spécifique

Au lieu d’accorder un consentement pour toute l’organisation, un administrateur peut également utiliser l’[API Microsoft Graph](/graph/use-the-api) pour accorder un consentement aux autorisations déléguées pour le compte d’un seul utilisateur. Pour obtenir un exemple détaillé de l’utilisation de Microsoft Graph PowerShell, consultez [Accorder le consentement pour le compte d’un utilisateur unique à l’aide de PowerShell](#grant-consent-on-behalf-of-a-single-user-using-powershell).

## <a name="limiting-user-access-to-applications"></a>Limitation de l’accès utilisateur aux applications

L’accès des utilisateurs aux applications peut quand même être limité même si le consentement de l’administrateur au niveau du locataire a été accordé. Pour plus d’informations sur la façon de demander l’attribution d’utilisateurs à une application, consultez [Méthodes pour attribuer des utilisateurs et des groupes](./assign-user-or-group-access-portal.md).

Pour une vue d’ensemble plus générale comprenant comment gérer d’autres scénarios complexes, consultez [Utilisation d’Azure AD pour la gestion des accès aux applications](what-is-access-management.md).

## <a name="disable-all-future-user-consent-operations-to-any-application"></a>Désactiver toutes les futures opérations de consentement de l’utilisateur pour n’importe quelle application

La désactivation du consentement de l’utilisateur pour votre annuaire entier empêche les utilisateurs finaux de donner leur consentement pour n’importe quelle application. Les administrateurs peuvent toujours donner leur consentement au nom de l’utilisateur. Pour plus d’informations sur le consentement de l’application et sur les conditions pour donner ou refuser ce consentement, consultez la section [Comprendre le consentement de l’utilisateur et de l’administrateur](../develop/howto-convert-app-to-be-multi-tenant.md).

Pour désactiver toutes les futures opérations de consentement de l’utilisateur dans l’ensemble de votre annuaire, procédez comme suit :

1. Ouvrez le [**portail Azure**](https://portal.azure.com/) et connectez-vous en tant **qu’Administrateur général**.
2. Ouvrez **l’extension Azure Active Directory** en cliquant sur **Tous les services** en haut du menu de navigation principal de gauche.
3. Tapez « **Azure Active Directory** » dans la zone de recherche de filtre et sélectionnez l’élément **Azure Active Directory**.
4. Sélectionnez **Applications d’entreprise** et sélectionnez **Paramètres utilisateur** dans la section **Gérer**.
:::image type="content" source="media/manage-consent-requests/disable-user-consent-operations.png" alt-text="désactivation des opérations de consentement de l’utilisateur pour toutes les applications.":::
5. Désactivez toutes les futures opérations de consentement de l’utilisateur en définissant l’option les **Utilisateurs peuvent donner leur consentement aux applications qui accèdent aux données de l’entreprise en leur nom** sur **Non** , puis cliquez sur le bouton **Enregistrer**.

## <a name="grant-consent-on-behalf-of-a-single-user-using-powershell"></a>Accorder le consentement pour le compte d’un utilisateur unique à l’aide de PowerShell

Lorsqu’un utilisateur accorde son consentement pour lui-même, voici ce qui se produit :

1. Un principal de service est créé pour l’application cliente, s’il n’existe pas déjà. Un principal de service est l’instance d’une application ou d’un service dans votre locataire Azure AD. L’accès accordé à l’application ou au service est associé à cet objet de principal de service.
1. Pour chaque API à laquelle l’application requiert l’accès, une autorisation déléguée est créée pour les autorisations nécessaires à cette API pour l’application, pour l’accès pour le compte de l’utilisateur. Un octroi d’autorisation déléguée autorise une application à accéder à une API pour le compte d’un utilisateur, lorsque cet utilisateur s’est connecté.
1. L’application cliente est affectée à l’utilisateur. L’attribution de l’application à l’utilisateur garantit que l’application est listée dans le portail [Mes applications](my-apps-deployment-plan.md) pour cet utilisateur, ce qui lui permet de passer en revue et révoquer l’accès accordé en son nom.

Pour effectuer manuellement les étapes qui sont équivalentes à l’octroi d’un consentement à une application pour le compte d’un utilisateur, vous aurez besoin des informations suivantes :

* ID d’application pour l’application pour laquelle vous accordez le consentement (nous appelons l’application cliente).
* Autorisations d’API requises par l’application cliente. Vous devez connaître l’ID d’application de l’API et les ID d’autorisation ou les valeurs de revendication.
* Le nom d’utilisateur ou l’ID d’objet pour l’utilisateur auquel l’accès au compte a été accordé.

Dans l’exemple suivant, nous allons utiliser [Microsoft Graph PowerShell](/graph/powershell/get-started) pour effectuer les trois étapes indiquées ci-dessus afin d’accorder le consentement au nom d’un seul utilisateur. Pour cet exemple, l’application cliente sera [Microsoft Graph Explorer](https://aka.ms/ge) et nous accordons l’accès à l’API Microsoft Graph.

```powershell
# The app for which consent is being granted. In this example, we're granting access
# to Microsoft Graph Explorer, an application published by Microsoft.
$clientAppId = "de8bc8b5-d9f9-48b1-a8ad-b748da725064" # Microsoft Graph Explorer

# The API to which access will be granted. Microsoft Graph Explorer makes API 
# requests to the Microsoft Graph API, so we'll use that here.
$resourceAppId = "00000003-0000-0000-c000-000000000000" # Microsoft Graph API

# The permissions to grant. Here we're including "openid", "profile", "User.Read"
# and "offline_access" (for basic sign-in), as well as "User.ReadBasic.All" (for 
# reading other users' basic profile).
$permissions = @("openid", "profile", "offline_access", "User.Read", "User.ReadBasic.All")

# The user on behalf of who access will be granted. The app will be able to access 
# the API on behalf of this user.
$userUpnOrId = "user@example.com"

# Step 0. Connect to Microsoft Graph PowerShell. We need User.ReadBasic.All to get
#    users' IDs, Application.ReadWrite.All to list and create service principals, 
#    DelegatedPermissionGrant.ReadWrite.All to create delegated permission grants, 
#    and AppRoleAssignment.ReadWrite.All to assign an app role.
#    WARNING: These are high-privilege permissions!
Connect-MgGraph -Scopes ("User.ReadBasic.All Application.ReadWrite.All " `
                        + "DelegatedPermissionGrant.ReadWrite.All " `
                        + "AppRoleAssignment.ReadWrite.All")

# Step 1. Check if a service principal exists for the client application. 
#     If one does not exist, create it.
$clientSp = Get-MgServicePrincipal -Filter "appId eq '$($clientAppId)'"
if (-not $clientSp) {
   $clientSp = New-MgServicePrincipal -AppId $clientAppId
}

# Step 2. Create a delegated permission grant granting the client app access to the
#     API, on behalf of the user. (This example assumes that an existing delegated 
#     permission grant does not already exist, in which case it would be necessary 
#     to update the existing grant, rather than create a new one.)
$user = Get-MgUser -UserId $userUpnOrId
$resourceSp = Get-MgServicePrincipal -Filter "appId eq '$($resourceAppId)'"
$scopeToGrant = $permissions -join " "
$grant = New-MgOauth2PermissionGrant -ResourceId $resourceSp.Id `
                                     -Scope $scopeToGrant `
                                     -ClientId $clientSp.Id `
                                     -ConsentType "Principal" `
                                     -PrincipalId $user.Id

# Step 3. Assign the app to the user. This ensure the user can sign in if assignment
#     is required, and ensures the app shows up under the user's My Apps.
if ($clientSp.AppRoles | ? { $_.AllowedMemberTypes -contains "User" }) {
    Write-Warning ("A default app role assignment cannot be created because the " `
                 + "client application exposes user-assignable app roles. You must " `
                 + "assign the user a specific app role for the app to be listed " `
                 + "in the user's My Apps access panel.")
} else {
    # The app role ID 00000000-0000-0000-0000-000000000000 is the default app role
    # indicating that the app is assigned to the user, but not for any specific 
    # app role.
    $assignment = New-MgServicePrincipalAppRoleAssignedTo `
          -ServicePrincipalId $clientSp.Id `
          -ResourceId $clientSp.Id `
          -PrincipalId $user.Id `
          -AppRoleId "00000000-0000-0000-0000-000000000000"
}
```

## <a name="next-steps"></a>Étapes suivantes

* [Cinq étapes pour sécuriser votre infrastructure d’identité](../../security/fundamentals/steps-secure-identity.md#before-you-begin-protect-privileged-accounts-with-mfa)
* [Configurer le workflow du consentement administrateur](configure-admin-consent-workflow.md)
* [Configurer le consentement de l’utilisateur final pour une application](configure-user-consent.md)
* [Autorisations et consentement dans la plateforme d’identités Microsoft](../develop/v2-permissions-and-consent.md)
