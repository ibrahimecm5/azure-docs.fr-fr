---
title: Résolution des problèmes de B2B Collaboration - Azure Active Directory | Microsoft Docs
description: Solutions pour les problèmes courants liés à Azure Active Directory B2B Collaboration
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: troubleshooting
ms.date: 10/21/2021
tags: active-directory
ms.author: mimart
author: msmimart
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: e6220a68583ecaaba061dde0ccd2de639d055631
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131067144"
---
# <a name="troubleshooting-azure-active-directory-b2b-collaboration"></a>Résolution des problèmes d’Azure Active Directory B2B Collaboration

Voici des solutions pour les problèmes courants liés à Azure Active Directory (Azure AD) B2B Collaboration.

   > [!IMPORTANT]
   >
   > - **À partir du 12 juillet 2021**, si les clients B2B d’Azure AD configurent de nouvelles intégrations Google pour une utilisation avec l’inscription en libre-service pour leurs applications métier ou personnalisées, l’authentification avec les identités Google ne fonctionnera pas tant que les authentifications ne sont pas déplacées vers les vues web du système. [Plus d’informations](google-federation.md#deprecation-of-web-view-sign-in-support)
   > - **À partir du 30 septembre 30, 2021**, Google [déprécie la prise en charge de la connexion aux vues web intégrée](https://developers.googleblog.com/2016/08/modernizing-oauth-interactions-in-native-apps.html). Si vos applications authentifient les utilisateurs avec une vue Web incorporée et que vous utilisez Google Federation avec [Azure AD B2C](../../active-directory-b2c/identity-provider-google.md) ou Azure AD B2B pour des [invitations utilisateur externes](google-federation.md) ou une [inscription en libre-service](identity-providers.md), les utilisateurs de Google Gmail ne peuvent pas s’authentifier. [Plus d’informations](google-federation.md#deprecation-of-web-view-sign-in-support)
   > - **À partir du 1er novembre 2021**, nous allons commencer à déployer un changement afin d’activer la fonctionnalité de code secret à usage unique envoyé par e-mail pour tous les locataires existants et de l’activer par défaut pour les nouveaux locataires. Dans le cadre de cette modification, Microsoft cessera de créer des comptes et des locataires Azure AD non managés (« viraux ») lors de l’acceptation d’invitation de collaboration B2B. Pour réduire les perturbations pendant les vacances et les verrouillages des déploiements, les modifications seront déployées pour la majorité des locataires en janvier 2022. Il s’agit en effet d’une méthode d’authentification de secours transparente pour les utilisateurs invités. Cependant, si vous ne voulez permettre l’activation automatique de cette fonctionnalité, vous pouvez [la désactiver](one-time-passcode.md#disable-email-one-time-passcode).

## <a name="ive-added-an-external-user-but-do-not-see-them-in-my-global-address-book-or-in-the-people-picker"></a>J’ai ajouté un utilisateur externe, mais je ne le vois pas dans mon carnet d’adresses global ou dans le sélecteur de personnes

Dans les cas où les utilisateurs externes ne sont pas renseignés dans la liste, la réplication de l’objet peut prendre quelques minutes.

## <a name="a-b2b-guest-user-is-not-showing-up-in-sharepoint-onlineonedrive-people-picker"></a>Un utilisateur invité B2B ne s’affiche pas dans le sélecteur de personnes SharePoint Online/OneDrive

La fonctionnalité de recherche d’utilisateurs invités existants dans le sélecteur de personnes SharePoint Online (SPO) est désactivée par défaut pour correspondre au comportement hérité.

Vous pouvez activer cette fonctionnalité à l’aide du paramètre ShowPeoplePickerSuggestionsForGuestUsers au niveau du client et de la collection du site. Vous pouvez définir cette fonctionnalité à l’aide des applets de commande Set-SPOTenant et Set-SPOSite qui permettent aux membres de rechercher tous les utilisateurs invités existants dans le répertoire. Les modifications apportées à la portée du client n’affectent pas les sites SPO déjà configurés.

## <a name="my-guest-invite-settings-and-domain-restrictions-arent-being-respected-by-sharepoint-onlineonedrive"></a>Mon invité, les paramètres d’invitation et les restrictions de domaine ne sont pas respectés par SharePoint Online/OneDrive

Par défaut, SharePoint Online et OneDrive disposent de leur propre ensemble d’options utilisateur externes et n’utilisent pas les paramètres d’Azure AD.  Vous devez activer [l'intégration de SharePoint et de OneDrive avec Azure AD B2B](/sharepoint/sharepoint-azureb2b-integration-preview) pour vous assurer que les options sont cohérentes entre ces applications.
## <a name="invitations-have-been-disabled-for-directory"></a>Des invitations ont été désactivées pour le répertoire

Si un message vous indique que vous n’êtes pas autorisé à inviter des utilisateurs, vérifiez que votre compte d’utilisateur est autorisé à inviter des utilisateurs externes sous Azure Active Directory > Paramètres utilisateur > Utilisateurs externes > Gérer les paramètres de collaboration externes :

![Capture d’écran montrant les paramètres des utilisateurs externes](media/troubleshoot/external-user-settings.png)

Si vous avez récemment modifié ces paramètres ou affecté le rôle d’inviteur d’invités à un utilisateur, vous devrez peut-être attendre 15 à 60 minutes avant que les modifications ne prennent effet.

## <a name="the-user-that-i-invited-is-receiving-an-error-during-redemption"></a>L’utilisateur que j’ai invité reçoit une erreur au cours de l’utilisation de l'invitation

Les erreurs courantes sont les suivantes :

### <a name="invitees-admin-has-disallowed-emailverified-users-from-being-created-in-their-tenant"></a>L’administrateur de l’invité n’autorise pas la création d’utilisateurs EmailVerified dans leur client

Si vous invitez des utilisateurs dont l’organisation utilise un Azure Active Directory dans lequel le compte d’utilisateur spécifique n’existe pas (par exemple, l’utilisateur n’existe pas dans Azure AD contoso.com). L’administrateur de contoso.com peut avoir mis en place une stratégie empêchant la création d'utilisateurs. L’utilisateur doit contacter son administrateur pour déterminer si les utilisateurs externes sont autorisés. L’administrateur de l’utilisateur externe devra peut-être autoriser les utilisateurs vérifiés par e-mail dans son domaine (consultez cet [article](/powershell/module/msonline/set-msolcompanysettings) sur l’autorisation d’utilisateurs vérifiés par e-mail).

![Erreur indiquant que le locataire n’autorise pas les utilisateurs vérifiés par e-mail](media/troubleshoot/allow-email-verified-users.png)

### <a name="external-user-does-not-exist-already-in-a-federated-domain"></a>L'utilisateur externe n’existe pas déjà dans un domaine fédéré

Si vous utilisez l’authentification par fédération et si l’utilisateur n’existe pas déjà dans Azure Active Directory, l’utilisateur ne peut pas être invité.

Pour résoudre ce problème, administrateur de l’utilisateur externe doit synchroniser le compte d’utilisateur sur Azure Active Directory.

### <a name="external-user-has-a-proxyaddress-that-conflicts-with-a-proxyaddress-of-an-existing-local-user"></a>Un utilisateur externe a une proxyAddress qui entre en conflit avec une proxyAddress d’un utilisateur local existant

Lorsque nous vérifions si un utilisateur peut être invité sur votre locataire, l’un des éléments que nous vérifions concerne une collision dans la proxyAddress. Cela comprend toutes les proxyAddresses pour l’utilisateur dans son locataire de départ et toute proxyAddress pour les utilisateurs locaux de votre locataire. Pour les utilisateurs externes, nous allons ajouter l’adresse e-mail à la proxyAddress de l’utilisateur B2B existant. En ce qui concerne les utilisateurs locaux, vous pouvez leur demander de se connecter en utilisant le compte dont ils disposent déjà.

## <a name="i-cant-invite-an-email-address-because-of-a-conflict-in-proxyaddresses"></a>Je ne peux pas inviter une adresse e-mail en raison d’un conflit dans proxyAddresses

Cela se produit lorsqu’un autre objet du répertoire a la même adresse e-mail invitée que l’un de ses proxyAddresses. Pour résoudre ce problème, supprimez l’e-mail de l’objet [utilisateur](/graph/api/resources/user?view=graph-rest-1.0&preserve-view=true) et supprimez également l’objet [contact](/graph/api/resources/contact?view=graph-rest-1.0&preserve-view=true) associé avant d’essayer d’inviter à nouveau cet e-mail.

## <a name="the-guest-user-object-doesnt-have-a-proxyaddress"></a>L’objet utilisateur invité n’a pas de proxyAddress

Lors de l’invitation d’un utilisateur invité externe, cela peut parfois entrer en conflit avec un [objet contact](/graph/api/resources/contact?view=graph-rest-1.0&preserve-view=true) existant. Lorsque cela se produit, l’utilisateur invité est créé sans proxyAddress. Cela signifie que l’utilisateur ne pourra pas échanger ce compte à l’aide d’un [échange juste-à-temps](redemption-experience.md#redemption-through-a-direct-link) ou [d’une authentification par code secret à usage unique](one-time-passcode.md#user-experience-for-one-time-passcode-guest-users).

## <a name="how-does--which-is-not-normally-a-valid-character-sync-with-azure-ad"></a>Comment synchroniser « \# », qui n’est normalement pas un caractère valide, avec Azure AD ?

« \# » est un caractère réservé dans les UPN pour les utilisateurs de collaboration ou externes Azure AD B2B, car le compte invité user@contoso.com devient user_contoso.com#EXT#@fabrikam.onmicrosoft.com. Par conséquent, il est impossible pour \# dans les UPN en local de se connecter au portail Azure. 

## <a name="i-receive-an-error-when-adding-external-users-to-a-synchronized-group"></a>Je reçois une erreur lors de l’ajout d'utilisateurs externes à un groupe synchronisé

Des utilisateurs externes peuvent être ajoutés uniquement à des groupes « affectés » ou « de sécurité » et non à des groupes contrôlés localement.

## <a name="my-external-user-did-not-receive-an-email-to-redeem"></a>Mon utilisateur externe n’a pas reçu d'e-mail à utiliser

L’invité doit contacter son fournisseur de services Internet ou contrôler son filtre de courriers indésirables pour s’assurer que l’adresse suivante est autorisée : Invites@microsoft.com

> [!NOTE]
>
> - Pour le service Azure géré par 21Vianet en Chine, l’adresse de l’expéditeur est Invites@oe.21vianet.com.
> - Pour le cloud Azure AD Government, l’adresse de l’expéditeur est invites@azuread.us.

## <a name="i-notice-that-the-custom-message-does-not-get-included-with-invitation-messages-at-times"></a>Je remarque que le message personnalisé n’est parfois pas inclus dans les messages d’invitation

Par respect des lois sur la confidentialité, nos API n’incluent pas de messages personnalisés dans les invitations par e-mail lorsque :

- L’inviteur n’a pas d’adresse e-mail dans le locataire à l’origine de l’invitation
- Un principal AppService envoie l’invitation

Si ce scénario est important pour vous, vous pouvez supprimer l’API qui envoie l’invitation par e-mail, et envoyer cette dernière au moyen d’un mécanisme de messagerie de votre choix. Consultez un conseiller juridique dans votre organisation pour vous assurer que tout e-mail que vous envoyez est également conforme aux lois relatives à la vie privée.

## <a name="you-receive-an-aadsts65005-error-when-you-try-to-log-in-to-an-azure-resource"></a>Vous recevez une erreur « AADSTS65005 » lorsque vous essayez de vous connecter à une ressource Azure

Un utilisateur disposant d’un compte invité ne peut pas ouvrir de session et reçoit le message d’erreur suivant :

```plaintext
    AADSTS65005: Using application 'AppName' is currently not supported for your organization contoso.com because it is in an unmanaged state. An administrator needs to claim ownership of the company by DNS validation of contoso.com before the application AppName can be provisioned.
```

L’utilisateur dispose d’un compte d’utilisateur Azure et est un locataire viral qui a été abandonné ou non managé. En outre, il n’existe aucun administrateur général dans le locataire.

Pour résoudre ce problème, vous devez prendre le contrôle du locataire abandonné. Reportez-vous à [Prendre le contrôle d’un annuaire non géré en tant qu’administrateur dans Azure Active Directory](../enterprise-users/domains-admin-takeover.md). Vous devez également accéder au DNS accessible sur internet pour le suffixe de domaine en question afin de fournir la preuve directe que vous contrôlez l’espace de noms. Une fois que le client est renvoyé à un état géré, demandez au client si le fait de laisser les utilisateurs et les noms de domaine vérifiés est la meilleure option pour son organisation.

## <a name="a-guest-user-with-a-just-in-time-or-viral-tenant-is-unable-to-reset-their-password"></a>Un utilisateur invité avec locataire de type juste-à-temps ou « viral » ne peut pas réinitialiser son mot de passe

Si le locataire de l'identité est un locataire de type juste-à-temps (JIT) ou « viral » (à savoir un locataire Azure distinct et non managé), seul l'utilisateur invité peut réinitialiser son mot de passe. Parfois, une organisation [reprend la gestion des locataires viraux](../enterprise-users/domains-admin-takeover.md) qui sont créés lorsque des employés utilisent leur adresse e-mail professionnelle pour s'inscrire à des services. Dès lors que l'organisation adopte un locataire viral, seul un administrateur de cette organisation peut réinitialiser le mot de passe de l'utilisateur ou activer la réinitialisation de mot de passe en libre-service. Le cas échéant, en tant qu'organisation à l'origine de l'invitation, vous pouvez supprimer le compte d'utilisateur invité de votre annuaire et renvoyer une invitation.

## <a name="a-guest-user-is-unable-to-use-the-azuread-powershell-v1-module"></a>Un utilisateur invité ne peut pas utiliser le module AzureAD PowerShell V1

Depuis le 18 novembre 2019, les utilisateurs invités dans votre répertoire (définis comme des comptes utilisateurs où la propriété **userType** est égale à **Guest**) ne peuvent pas utiliser le module AzureAD PowerShell V1. À partir de cette date, un utilisateur devra soit être un utilisateur membre (où **userType** est égal à **Member**) soit utiliser le module AzureAD PowerShell V2.

## <a name="in-an-azure-us-government-tenant-i-cant-invite-a-b2b-collaboration-guest-user"></a>Dans un locataire Azure US Government, je ne peux pas inviter un utilisateur invité B2B Collaboration

Dans le Cloud Azure US Government, B2B Collaboration est actuellement prise en charge seulement entre les locataires qui se trouvent tous deux dans le Cloud Azure US Government et qui prennent tous deux en charge B2B Collaboration. Si vous invitez un utilisateur dans un locataire qui ne fait pas partie du cloud Azure US Government ou qui ne prend pas encore en charge B2B Collaboration, vous obtiendrez une erreur. Pour plus d’informations et pour connaître les limitations, consultez [Différences entre Azure Active Directory Premium P1 et P2](../../azure-government/compare-azure-government-global-azure.md#azure-active-directory-premium-p1-and-p2).

## <a name="i-receive-the-error-that-azure-ad-cannot-find-the-aad-extensions-app-in-my-tenant"></a>Je reçois une erreur indiquant qu'Azure AD ne trouve pas l'application aad-extensions-app dans mon locataire

Lorsque vous utilisez des fonctionnalités d'inscription en libre-service, comme des attributs utilisateur personnalisés ou des flux d'utilisateurs, une application appelée `aad-extensions-app. Do not modify. Used by AAD for storing user data.` est automatiquement créée. Celle-ci est utilisée par Azure Active Directory for External Identities pour stocker les informations des utilisateurs qui s'inscrivent et les attributs personnalisés collectés.

Si vous avez accidentellement supprimé l'application `aad-extensions-app`, vous disposez de 30 jours pour la récupérer. Vous pouvez la restaurer à l'aide du module Azure AD PowerShell.

1. Lancez le module Azure AD PowerShell et exécutez `Connect-AzureAD`.
1. Connectez-vous en tant qu'administrateur général du locataire Azure AD pour lequel vous souhaitez récupérer l'application supprimée.
1. Exécutez la commande PowerShell `Get-AzureADDeletedApplication`.
1. Dans la liste, recherchez l'application dont le nom d'affichage commence par `aad-extensions-app`, puis copiez sa valeur de propriété `ObjectId`.
1. Exécutez la commande PowerShell `Restore-AzureADDeletedApplication -ObjectId {id}`. Remplacez la partie `{id}` de la commande par l'`ObjectId` de l'étape précédente.

L'application restaurée doit maintenant être visible sur le portail Azure.

## <a name="a-guest-user-was-invited-successfully-but-the-email-attribute-is-not-populating"></a>Un utilisateur invité a été correctement invité, mais le champ de l’attribut d’adresse e-mail ne se remplit pas

Supposons que vous invitiez par inadvertance un utilisateur invité avec une adresse de messagerie qui correspond à un objet utilisateur déjà présent dans votre répertoireinvité. L’objet de l’utilisateur invité est créé, mais l’adresse e-mail est ajoutée à la propriété `otherMail` plutôt qu’aux propriétés `mail` ou `proxyAddresses`. Pour éviter ce problème, vous pouvez rechercher des objets utilisateur en conflit dans votre répertoire Azure AD à l’aide des étapes PowerShell suivantes :

1. Lancez le module Azure AD PowerShell et exécutez `Connect-AzureAD`.
1. Connectez-vous en tant qu'administrateur général du locataire Azure AD pour lequel vous souhaitez vérifier la présence d’objets contact en double.
1. Exécutez la commande PowerShell `Get-AzureADContact -All $true | ? {$_.ProxyAddresses -match 'user@domain.com'}`.
1. Exécutez la commande PowerShell `Get-AzureADContact -All $true | ? {$_.Mail -match 'user@domain.com'}`.

## <a name="next-steps"></a>Étapes suivantes

[Obtention de la prise en charge pour B2B Collaboration](../fundamentals/active-directory-troubleshooting-support-howto.md)
