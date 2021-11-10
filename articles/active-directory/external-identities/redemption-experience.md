---
title: Utilisation d’une invitation dans B2B Collaboration – Azure AD
description: Cet article décrit l’utilisation des invitations dans Azure AD B2B Collaboration pour les utilisateurs finaux, dont l’acceptation des conditions de confidentialité.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 10/21/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.collection: M365-identity-device-management
ms.openlocfilehash: a3d20d621b648fb84f2cedee08f206d6eb257f51
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130227713"
---
# <a name="azure-active-directory-b2b-collaboration-invitation-redemption"></a>Utilisation d'invitations Azure Active Directory B2B Collaboration

Cet article décrit les différentes façons dont les utilisateurs invités peuvent accéder à vos ressources, et le processus de consentement qu’ils vont rencontrer. Si vous envoyez un e-mail d’invitation à l’invité, l’invitation comprend un lien que l’invité peut utiliser pour obtenir l’accès à votre application ou portail. L’e-mail d’invitation n’est qu’une des possibilités permettant aux invités d’obtenir l’accès à vos ressources. Comme alternative, vous pouvez ajouter des invités à votre annuaire et leur donner un lien direct vers le portail ou l’application que vous souhaitez partager. Quelle que soit la méthode qu’ils utilisent, les invités sont guidés dans un processus de consentement initial. Ce processus garantit que vos invités sont d’accord avec les conditions de confidentialité, et qu’ils acceptent les [conditions d’utilisation](../conditional-access/terms-of-use.md) que vous avez définies.

Lorsque vous ajoutez un utilisateur invité à votre annuaire, le compte d’utilisateur invité présente un état de consentement (affichable dans PowerShell) qui est initialement défini sur **PendingAcceptance**. Ce paramètre est maintenu jusqu’à ce que l’invité accepte votre invitation et approuve votre politique de confidentialité ainsi que vos conditions d’utilisation. Après cela, l’état de consentement passe à **Accepté**, et les pages de consentement ne sont plus présentées à l’invité.

> [!IMPORTANT]
> - **À partir du 12 juillet 2021**, si les clients Azure AD B2B configurent de nouvelles intégrations Google pour une utilisation avec l’inscription en libre-service pour leurs applications métier ou personnalisées, l’authentification avec des identités Google ne fonctionne pas tant que les authentifications ne sont pas déplacées vers les vues web système. [Plus d’informations](google-federation.md#deprecation-of-web-view-sign-in-support)
> - **À partir du 30 septembre 30, 2021**, Google [déprécie la prise en charge de la connexion aux vues web intégrée](https://developers.googleblog.com/2016/08/modernizing-oauth-interactions-in-native-apps.html). Si vos applications authentifient les utilisateurs avec une vue Web incorporée et que vous utilisez Google Federation avec [Azure AD B2C](../../active-directory-b2c/identity-provider-google.md) ou Azure AD B2B pour des [invitations utilisateur externes](google-federation.md) ou une [inscription en libre-service](identity-providers.md), les utilisateurs de Google Gmail ne peuvent pas s’authentifier. [Plus d’informations](google-federation.md#deprecation-of-web-view-sign-in-support)
> - **À partir du 1er novembre 2021**, nous allons commencer à déployer un changement afin d’activer la fonctionnalité de code secret à usage unique envoyé par e-mail pour tous les locataires existants et de l’activer par défaut pour les nouveaux locataires. Dans le cadre de cette modification, Microsoft cessera de créer des comptes et des locataires Azure AD non managés (« viraux ») lors de l’acceptation d’invitation de collaboration B2B. Pour réduire au minimum les perturbations pendant les fêtes de fin d’année et les blocages de déploiements, les modifications seront déployées pour la majorité des locataires en janvier 2022. Il s’agit en effet d’une méthode d’authentification de secours transparente pour les utilisateurs invités. Cependant, si vous ne voulez pas autoriser l’activation automatique de cette fonctionnalité, vous pouvez [la désactiver](one-time-passcode.md#disable-email-one-time-passcode). 

## <a name="redemption-and-sign-in-through-a-common-endpoint"></a>Acceptation et connexion via un point de terminaison commun

Les utilisateurs invités peuvent désormais se connecter à vos applications multilocataires ou à vos applications principales Microsoft via un point de terminaison commun (URL), par exemple `https://myapps.microsoft.com`. Jusqu’ici, une URL commune redirigeait l’utilisateur invité vers son locataire d’origine au lieu de votre locataire de ressources pour l’authentification. Un lien spécifique au locataire était donc nécessaire (par exemple `https://myapps.microsoft.com/?tenantid=<tenant id>`). Désormais, l’utilisateur invité peut accéder à l’URL commune de l’application, choisir **Options de connexion**, puis sélectionner **Sign in to an organization** (Se connecter à une organisation). L’utilisateur tape ensuite le nom de votre organisation.

![Connexion au point de terminaison commun](media/redemption-experience/common-endpoint-flow-small.png)

L’utilisateur est ensuite redirigé vers votre point de terminaison de locataire, où il peut soit se connecter avec son adresse e-mail, soit sélectionner un fournisseur d’identité que vous avez configuré.

## <a name="redemption-through-a-direct-link"></a>Échange via un lien direct

Au lieu de l’e-mail d’invitation ou de l’URL commune d’une application, vous pouvez fournir à un invité un lien direct vers votre application ou votre portail. Vous devez d’abord ajouter l’utilisateur invité à votre annuaire par le biais du [portail Azure](./b2b-quickstart-add-guest-users-portal.md) ou de [PowerShell](./b2b-quickstart-invite-powershell.md). Ensuite, vous pouvez utiliser un des [moyens personnalisables de votre choix pour déployer des applications vers les utilisateurs](../manage-apps/end-user-experiences.md), notamment les liens d’authentification directe. Lorsqu’un invité utilise un lien direct au lieu de l’e-mail d’invitation, il est toujours guidé dans l’expérience de consentement initial.

> [!NOTE]
> Un lien direct est spécifique au locataire. En d’autres termes, il inclut un ID de locataire ou un domaine vérifié pour permettre à l’invité d’être authentifié dans votre locataire, là où se trouve l’application partagée. Voici quelques exemples de liens directs avec contexte de locataire :
 > - Panneau d’accès des applications : `https://myapps.microsoft.com/?tenantid=<tenant id>`
 > - Panneau d’accès des applications pour un domaine vérifié : `https://myapps.microsoft.com/<;verified domain>`
 > - Portail Azure : `https://portal.azure.com/<tenant id>`
 > - Application individuelle : voir comment utiliser un [lien d’authentification directe](../manage-apps/end-user-experiences.md#direct-sign-on-links)

Il existe certains cas où l’e-mail d’invitation est recommandé par rapport à un lien direct. Si ces cas particuliers sont importants pour votre organisation, nous vous conseillons d’inviter les utilisateurs par l’intermédiaire de méthodes qui envoient toujours l’e-mail d’invitation :
 - L’utilisateur n’a pas de compte Azure AD, de MSA ni de compte d’e-mail dans une organisation fédérée. À moins d’utiliser la fonctionnalité de code secret à usage unique, l’invité doit accepter l’e-mail d’invitation pour être guidé à travers les étapes de la création d’un MSA.
 - Parfois, l’objet utilisateur invité n’a pas d’adresse e-mail en raison d’un conflit avec un objet contact (par exemple, un objet contact Outlook). Dans ce cas, l’utilisateur doit cliquer sur l’URL d’acceptation de l’e-mail d’invitation.
 - L’utilisateur peut se connecter avec un alias de l’adresse e-mail via laquelle il a été invité (un alias est une adresse e-mail supplémentaire associée à un compte de courrier). Dans ce cas, l’utilisateur doit cliquer sur l’URL d’acceptation de l’e-mail d’invitation.

## <a name="redemption-through-the-invitation-email"></a>Acceptation via l’e-mail d’invitation

Lorsque vous ajoutez un utilisateur invité à votre annuaire en [utilisant le portail Azure](./b2b-quickstart-add-guest-users-portal.md), un e-mail d’invitation est envoyé à l’invité dans le processus. Vous pouvez également choisir d’envoyer des e-mails d’invitation lorsque vous [utilisez PowerShell](./b2b-quickstart-invite-powershell.md) pour ajouter des utilisateurs invités à votre annuaire. Voici une description de l’expérience de l’invité lorsqu’il accepte le lien dans l’e-mail.

1. L’invité reçoit un [e-mail d’invitation](./invitation-email-elements.md) qui est envoyé depuis **Invitations Microsoft**.
2. L’invité sélectionne **Accepter l’invitation** dans l’e-mail.
3. L’invité utilise ses propres informations d’identification pour se connecter à votre répertoire. Si l’invité n’a pas de compte qui peut être fédéré à votre répertoire et que la fonctionnalité [Code secret e-mail à usage unique (OTP)](./one-time-passcode.md) n’est pas activée, l’invité est invité à créer un [MSA](https://support.microsoft.com/help/4026324/microsoft-account-how-to-create) personnel ou un [compte libre-service Azure AD](../enterprise-users/directory-self-service-signup.md). Pour plus d’informations, reportez-vous au [flux d’acceptation d’invitation](#invitation-redemption-flow).
4. L’invité est guidé tout au long de l’[expérience de consentement](#consent-experience-for-the-guest) décrite ci-dessous.

## <a name="redemption-limitation-with-conflicting-contact-object"></a>Limitation d’échange avec objet contact en conflit
Parfois, l’e-mail de l’utilisateur externe invité peut être en conflit avec un [Objet de contact](/graph/api/resources/contact?view=graph-rest-1.0&preserve-view=true) existant, ce qui entraîne la création de l’utilisateur invité sans proxyAddress. Il s’agit d’une limitation connue qui empêche les utilisateurs invités de faire ce qui suit : 
- Échanger une invitation via un lien direct à l’aide d’un fournisseur d’identité [SAML/WS-FED](./direct-federation.md), de [comptes Microsoft](./microsoft-account.md), de la [fédération Google](./google-federation.md) ou d’un compte à [Code secret à usage unique d’e-mail](./one-time-passcode.md). 
- Échanger une invitation via un lien d’échange d’e-mail d’invitation à l’aide d’un fournisseur d’identité [SAML/WS-FED](./direct-federation.md) et d’un compte à [Code secret à usage unique d’e-mail](./one-time-passcode.md).
- Se reconnecter à une application après l’échange à l’aide des comptes [IdP SAML/WS-FED](./direct-federation.md) et de [fédération Google](./google-federation.md).

Pour débloquer des utilisateurs qui ne peuvent pas échanger une invitation en raison d’un conflit avec un [Objet de contact](/graph/api/resources/contact?view=graph-rest-1.0&preserve-view=true), procédez comme suit :
1. Supprimez l’objet de contact conflictuel.
2. Supprimez l’utilisateur invité dans le portail Azure (la propriété « Invitation acceptée » de l’utilisateur doit être dans un état d’attente).
3. Réinvitez l’utilisateur.
4. Attendez que l’utilisateur accepte l’invitation.
5. Rajoutez l’e-mail de contact de l’utilisateur dans Exchange et toutes les listes de distribution dont il doit faire partie.

## <a name="invitation-redemption-flow"></a>Flux d’acceptation d’invitation

Quand un utilisateur clique sur le lien **Accepter l’invitation** dans un [e-mail d’invitation](invitation-email-elements.md), Azure AD accepte automatiquement l’invitation en fonction du flux d’acceptation, comme illustré ci-dessous :

![Capture d’écran montrant le diagramme du flux d’acceptation](media/redemption-experience/invitation-redemption-flow.png)

**Si le nom d’utilisateur principal (UPN) de l’utilisateur correspond à la fois à un compte Azure AD existant et à un compte MSA personnel, l’utilisateur est invité à choisir le compte avec lequel il souhaite accepter l’invitation.*

1. Azure AD effectue une découverte basée sur l’utilisateur pour déterminer si l’utilisateur existe dans un [locataire Azure AD](./what-is-b2b.md#easily-invite-guest-users-from-the-azure-ad-portal).

2. Si un administrateur a activé la [fédération SAML/WS-Fed IdP](./direct-federation.md), Azure AD vérifie si le suffixe de domaine de l’utilisateur correspond au domaine d’un fournisseur d’identité SAML/WS-Fed configuré et redirige l’utilisateur vers le fournisseur d’identité préconfiguré.

3. Si un administrateur a activé la [fédération Google](./google-federation.md), Azure AD vérifie si le suffixe de domaine de l’utilisateur est gmail.com ou googlemail.com et redirige l’utilisateur vers Google.

4. Le processus d’échange vérifie si l’utilisateur dispose d’un [compte Microsoft (MSA)](https://support.microsoft.com/help/4026324/microsoft-account-how-to-create) personnel existant pour les échanges juste-à-temps (JIT), mais pas pour l’échange de lien d’invitation par e-mail. Si l’utilisateur a déjà un MSA existant, il se connecte avec son MSA existant.

5. Une fois que le **répertoire de base** de l’utilisateur est identifié, l’utilisateur est envoyé au fournisseur d’identité correspondant pour se connecter.  

6. Si les étapes 1 à 4 ne parviennent pas à trouver un répertoire de base pour l’utilisateur invité, Azure AD détermine si le locataire qui invite a activé la fonctionnalité [Code secret e-mail à usage unique (OTP)](./one-time-passcode.md) pour les invités.

7. Si le [code secret e-mail à usage unique pour les invités est activé](./one-time-passcode.md#when-does-a-guest-user-get-a-one-time-passcode), un code secret est envoyé à l’utilisateur par le biais de l’adresse e-mail invitée. L’utilisateur récupère et entre ce code secret dans la page de connexion Azure AD.

8. Si le code secret e-mail à usage unique pour les invités est désactivé, Azure AD vérifie le suffixe de domaine pour déterminer s’il appartient à un compte client. Si c’est le cas, l’utilisateur est invité à créer un [compte Microsoft](https://support.microsoft.com/help/4026324/microsoft-account-how-to-create) personnel. Si ce n’est pas le cas, l’utilisateur est invité à créer un [compte libre-service Azure AD](../enterprise-users/directory-self-service-signup.md).

9. Azure AD tente de créer un [compte libre-service Azure AD](../enterprise-users/directory-self-service-signup.md) en vérifiant l’accès à l’adresse e-mail. La vérification du compte est effectuée en envoyant un code à l’adresse e-mail et en demandant à l’utilisateur de le récupérer et de l’envoyer à Azure AD. Toutefois, si le locataire de l’utilisateur invité est fédéré ou si le champ AllowEmailVerifiedUsers est défini sur False dans le locataire de l’utilisateur invité, l’utilisateur ne peut pas terminer l’acceptation et le flux génère une erreur. Pour plus d’informations, consultez [Résolution des problèmes d’Azure Active Directory B2B Collaboration](./troubleshoot.md#the-user-that-i-invited-is-receiving-an-error-during-redemption).

10. L’utilisateur est invité à créer un [compte Microsoft (MSA)](https://support.microsoft.com/help/4026324/microsoft-account-how-to-create) personnel.

11. Après s’être authentifié auprès du fournisseur d’identité approprié, l’utilisateur est redirigé vers Azure AD pour terminer l’[expérience de consentement](#consent-experience-for-the-guest).  

Pour les acceptations juste-à-temps (JAT), où l’acceptation s’effectue via un lien d’application avec locataire, les étapes 8 à 10 ne sont pas disponibles. Si un utilisateur atteint l’étape 6 et que la fonctionnalité Code secret e-mail à usage unique n’est pas activée, l’utilisateur reçoit un message d’erreur et ne peut pas accepter l’invitation. Pour éviter cette erreur, les administrateurs doivent [activer le code secret e-mail à usage unique](./one-time-passcode.md#when-does-a-guest-user-get-a-one-time-passcode) ou s’assurer que l’utilisateur clique sur un lien d’invitation.

## <a name="consent-experience-for-the-guest"></a>Expérience de consentement de l’invité

Lorsqu’un invité se connecte la première fois pour accéder aux ressources d’une organisation partenaire, il est guidé dans les pages suivantes. 

1. L’invité passe en revue la page **Révision des autorisations** décrivant la déclaration de confidentialité de l’organisation qui invite. Pour continuer, l’utilisateur doit **Accepter** l’utilisation de ses informations conformément à la politique de confidentialité de l’organisation invitante.

   ![Capture d’écran montrant la page Passer en revue les autorisations](media/redemption-experience/review-permissions.png) 

   > [!NOTE]
   > Pour plus d’informations sur la manière dont vous pouvez, en tant qu’administrateur de locataire, créer un lien vers la déclaration de confidentialité de votre organisation, consultez le [Guide pratique pour ajouter les informations de confidentialité de votre organisation dans Azure Active Directory](../fundamentals/active-directory-properties-area.md).

2. Si les conditions d’utilisation sont configurées, l’invité ouvre et passe en revue les conditions d’utilisation, puis il sélectionne **Accepter**. 

   ![Capture d’écran montrant les nouvelles conditions d’utilisation](media/redemption-experience/terms-of-use-accept.png) 

   Vous pouvez configurer les [conditions d’utilisation](../conditional-access/terms-of-use.md) dans **Identités externes** > **conditions d’utilisation**.

3. Sauf indication contraire, l’invité est redirigé vers le panneau d’accès des applications qui liste les applications auxquelles l’invité peut accéder.

   ![Capture d’écran montrant le panneau d’accès des applications](media/redemption-experience/myapps.png) 

> [!NOTE]
> L’expérience de consentement s’affiche uniquement une fois que l’utilisateur se connecte et non avant. Dans certains cas, l’expérience de consentement ne sera pas affichée pour l’utilisateur, par exemple :
> - L’utilisateur a déjà accepté l’expérience de consentement
> - L’administrateur [accorde à une application le consentement administrateur à l’échelle du locataire](../manage-apps/grant-admin-consent.md)

Dans votre annuaire, la valeur de **Invitation acceptée** de l’invité passe à **Oui**. Si un MSA a été créé, la **Source** de l’invité affiche **Compte Microsoft**. Pour plus d’informations sur les propriétés du compte d’utilisateur invité, consultez [Propriétés d’un utilisateur B2B Collaboration Azure AD](user-properties.md). Si vous voyez une erreur qui requiert le consentement administrateur lors de l’accès à une application, consultez [comment accorder le consentement administrateur aux applications](../develop/v2-admin-consent.md).

## <a name="next-steps"></a>Étapes suivantes

- [Qu'est-ce que la collaboration B2B d'Azure AD ?](what-is-b2b.md)
- [Ajouter des utilisateurs Azure Active Directory B2B Collaboration dans le Portail Azure](add-users-administrator.md)
- [Comment les professionnels de l’information ajoutent-ils des utilisateurs B2B Collaboration à Azure Active Directory ?](add-users-information-worker.md)
- [Ajouter des utilisateurs Azure Active Directory B2B Collaboration à l’aide de PowerShell](customize-invitation-api.md#powershell)
- [Quitter une organisation en tant qu’utilisateur invité](leave-the-organization.md)