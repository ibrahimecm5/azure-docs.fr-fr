---
title: Propriétés d’un utilisateur invité B2B - Azure Active Directory | Microsoft Docs
description: Propriétés et états d’utilisateur invité Azure Active Directory B2B avant et après l’acceptation d’invitation
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 10/21/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan, seoapril2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: bed6a0eb5c97905fa82b15b15f6997568c1d3c03
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130262600"
---
# <a name="properties-of-an-azure-active-directory-b2b-collaboration-user"></a>Propriétés d’un utilisateur Azure Active Directory B2B Collaboration

Cet article décrit les propriétés et les états d’un objet utilisateur de collaboration Azure Active Directory B2B (Azure AD B2B) invité avant et après l’acceptation d’invitation. Un utilisateur de collaboration Azure AD B2B est un utilisateur externe, généralement issu d’une organisation partenaire, que vous invitez à se connecter à votre organisation Azure AD à l’aide de ses propres informations d’identification. Cet utilisateur de collaboration B2B (également appelé *utilisateur invité*) peut ensuite accéder aux applications et aux ressources que vous souhaitez partager avec lui. Un objet utilisateur est créé pour l’utilisateur de collaboration B2B dans le même répertoire que celui utilisé pour vos employés. Les objets utilisateurs de collaboration B2B ont des privilèges limités dans votre répertoire par défaut, et ils peuvent être gérés comme des employés, ajoutés à des groupes, etc.

Selon les besoins de l’organisation qui émet l’invitation, un utilisateur Azure AD B2B Collaboration peut présenter l’un des états de compte suivants :

- État 1 : Hébergé dans une instance externe d’Azure AD et représenté sous la forme d’un utilisateur invité de l’organisation à l’origine de l’invitation. Dans ce cas, l’utilisateur B2B se connecte avec un compte Azure AD qui appartient au locataire invité. Même si l’organisation partenaire n’utilise pas Azure AD, l’utilisateur invité d’Azure AD est créé. Les conditions requises sont les suivantes : les utilisateurs se servent de leur invitation et Azure AD vérifie leur adresse e-mail. Cet arrangement est aussi appelé une location juste-à-temps (JIT), une location « virale » ou une location Azure AD non gérée.

   > [!IMPORTANT]
   > **À partir du 1er novembre 2021**, nous allons commencer à déployer un changement afin d’activer la fonctionnalité de code secret à usage unique envoyé par e-mail pour tous les locataires existants et de l’activer par défaut pour les nouveaux locataires. Dans le cadre de cette modification, Microsoft cessera de créer des comptes et des locataires Azure AD non managés (« viraux ») lors de l’acceptation d’invitation de collaboration B2B. Pour réduire les perturbations pendant les vacances et les verrouillages des déploiements, les modifications seront déployées pour la majorité des locataires en janvier 2022. Il s’agit en effet d’une méthode d’authentification de secours transparente pour les utilisateurs invités. Cependant, si vous ne voulez permettre l’activation automatique de cette fonctionnalité, vous pouvez [la désactiver](one-time-passcode.md#disable-email-one-time-passcode).

- État 2 : Hébergé dans un compte Microsoft ou autre et représenté en tant qu’utilisateur invité dans l’organisation hôte. Dans ce cas, l’utilisateur invité se connecte avec un compte Microsoft ou un compte social (google.com ou similaire). L’identité de l’utilisateur invité est créée en tant que compte Microsoft dans l’annuaire de l’organisation à l’origine de l’invitation durant l’utilisation de l’offre.

- État 3 : Hébergé dans l’instance Active Directory locale de l’organisation hôte et synchronisé avec l’instance Azure AD de l’organisation hôte. Vous pouvez utiliser Azure AD Connect pour synchroniser les comptes de partenaires dans le cloud en tant qu’utilisateurs Azure AD B2B (UserType = Invité). Consultez [Accorder un accès aux comptes de partenaires gérés en local aux ressources cloud](hybrid-on-premises-to-cloud.md).

- État 4 : Hébergé dans l’instance Azure AD de l’organisation hôte avec la propriété UserType = Invité et des informations d’identification gérées par l’organisation hôte.

  ![Diagramme illustrant les quatre états d’utilisateur](media/user-properties/redemption-diagram.png)


À présent, voyons à quoi ressemble un utilisateur Azure AD B2B Collaboration dans Azure AD.

### <a name="before-invitation-redemption"></a>Avant l'utilisation de l'invitation

Les comptes dans l’état 1 et l’état 2 sont le résultat de l’invitation d’utilisateurs invités à collaborer à l’aide des informations d’identification des utilisateurs invités. Quand l’invitation est initialement envoyée à l’utilisateur invité, un compte est créé dans votre annuaire. Ce compte n’est pas associé à des informations d’identification, car l’authentification est effectuée par le fournisseur d’identité de l’utilisateur invité. La propriété **Source** du compte d’utilisateur invité dans votre annuaire est définie avec la valeur **Utilisateur invité**. 

![Capture d’écran montrant les propriétés de l’utilisateur avant l’acceptation de l’offre](media/user-properties/before-redemption.png)

### <a name="after-invitation-redemption"></a>Après l'utilisation de l'invitation

Quand l’utilisateur invité accepte l’invitation, la propriété **Source** est mise à jour selon le fournisseur d’identité de l’utilisateur invité.

Pour les utilisateurs invités dans l’état 1, la propriété **Source** a la valeur **Azure Active Directory externe**.

![Utilisateur invité dans l’état 1 après utilisation de l’offre](media/user-properties/after-redemption-state1.png)

Pour les utilisateurs invités dans l’état 2, la propriété **Source** a la valeur **Compte Microsoft**.

![Utilisateur invité dans l’état 2 après l’utilisation de l’offre](media/user-properties/after-redemption-state2.png)

Pour les utilisateurs invités dans l’état 3 et l’état 4, la propriété **Source** a la valeur **Azure Active Directory** ou **Windows Server AD**, comme décrit dans la section suivante.

## <a name="key-properties-of-the-azure-ad-b2b-collaboration-user"></a>Propriétés clés de l’utilisateur Azure AD B2B collaboration
### <a name="usertype"></a>UserType
Cette propriété indique la relation de l’utilisateur avec la location hôte. Cette propriété peut avoir deux valeurs :
- Membre : Cette valeur désigne un employé de l’organisation hôte et un utilisateur du service de paie de l’organisation. Par exemple, cet utilisateur ne peut accéder qu’à des sites internes. Cet utilisateur n’est pas considéré comme collaborateur externe.

- Invité : Cette valeur indique un utilisateur qui n’est pas considéré comme interne à l’entreprise, tel qu’un collaborateur externe, un partenaire ou un client. Un tel utilisateur n’est pas censé recevoir de mémo interne du PDG ou bénéficier des avantages de la société, par exemple.

  > [!NOTE]
  > La valeur UserType n’a aucun lien avec le mode de connexion de l’utilisateur, le rôle d’annuaire de l’utilisateur, etc. Cette propriété indique simplement la relation de l’utilisateur avec l’organisation hôte et permet à l’organisation d’appliquer des stratégies qui dépendent de cette propriété.

Pour plus d’informations sur les tarifs, reportez-vous à [Tarification Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory).

### <a name="source"></a>Source
Cette propriété indique le mode de connexion de l’utilisateur.

- Utilisateur invité : Cet utilisateur a été invité, mais n’a pas encore utilisé une invitation.

- Azure Active Directory externe : Cet utilisateur est hébergé dans une organisation externe et s’authentifie à l’aide d’un compte Azure AD qui appartient à l’autre organisation. Ce type de connexion correspond à l’état 1.

- Compte Microsoft : Cet utilisateur est hébergé dans un compte Microsoft et s’authentifie à l’aide d’un compte Microsoft. Ce type de connexion correspond à l’état 2.

- Windows Server AD : cet utilisateur est connecté à partir de l’instance Active Directory locale qui appartient à cette organisation. Ce type de connexion correspond à l’état 3.

- Azure Active Directory : Cet utilisateur s’authentifie à l’aide d’un compte Azure AD qui appartient à cette organisation. Ce type de connexion correspond à l’état 4.
  > [!NOTE]
  > Les propriétés Source et UserType sont indépendantes. Une valeur Source donnée n’implique pas une valeur UserType spécifique.

## <a name="can-azure-ad-b2b-users-be-added-as-members-instead-of-guests"></a>Des utilisateurs Azure AD B2B peuvent-ils être ajoutés en tant que membres plutôt qu’en tant qu’invités ?
En règle générale, un utilisateur Azure AD B2B et un utilisateur invité sont synonymes. Par conséquent, un utilisateur Azure AD B2B Collaboration est ajouté par défaut en tant qu’utilisateur avec la propriété UserType = Invité. Toutefois, dans certains cas, l’organisation partenaire est un membre d’une organisation plus vaste à laquelle appartient également l’organisation hôte. Il est alors possible que l’organisation hôte veuille traiter les utilisateurs de l’organisation partenaire comme membres plutôt que comme invités. Utilisez les API du Gestionnaire d’invitations Azure AD B2B pour ajouter ou inviter un utilisateur de l’organisation partenaire dans l’organisation hôte en tant que membre.

## <a name="filter-for-guest-users-in-the-directory"></a>Filtrer les utilisateurs invités dans l’annuaire

![Capture d’écran montrant le filtre pour les utilisateurs invités](media/user-properties/filter-guest-users.png)

## <a name="convert-usertype"></a>Convertir la propriété UserType
Les utilisateurs peuvent convertir la valeur Membre de la propriété UserType en valeur Invité, et inversement, en utilisant PowerShell. Toutefois, la propriété UserType représente la relation de l’utilisateur avec l’organisation. Vous ne devez donc changer cette propriété que si la relation de l’utilisateur avec l’organisation change. Le changement de cette relation de l’utilisateur nécessite-t-il le changement du nom d’utilisateur principal (UPN) ? L’utilisateur doit-il continuer à avoir accès aux mêmes ressources ? Une boîte aux lettres doit-elle être attribuée ? 

## <a name="remove-guest-user-limitations"></a>Supprimer des limitations pour les utilisateurs invités
Dans certains cas, vous souhaiterez peut-être donner aux utilisateurs invités des privilèges plus élevés. Vous pouvez ajouter un utilisateur invité à un rôle quelconque et même supprimer les restrictions d’utilisateur invité par défaut dans le répertoire afin d’attribuer à l’utilisateur les mêmes privilèges que les membres.

Il est possible de désactiver les limitations par défaut afin qu’un utilisateur invité dans l’annuaire de la société ait les mêmes autorisations qu’un utilisateur membre.

![Capture d’écran montrant l’option Utilisateurs externes dans les paramètres utilisateur](media/user-properties/remove-guest-limitations.png)

## <a name="can-i-make-guest-users-visible-in-the-exchange-global-address-list"></a>Puis-je rendre les utilisateurs invités visibles dans la liste d’adresses globale Exchange ?
Oui. Par défaut, les objets invités ne sont pas visibles dans la liste d’adresses globale de votre organisation, mais vous pouvez utiliser Azure Active Directory PowerShell pour les rendre visibles. Pour plus d’informations, consultez « Ajouter des invités à la liste d’adresses globale » dans l’[article relatif à l’accès invité par groupe dans Microsoft 365](/microsoft-365/solutions/per-group-guest-access).

## <a name="can-i-update-a-guest-users-email-address"></a>Puis-je mettre à jour l’adresse de messagerie d’un utilisateur invité ?

Si un utilisateur invité accepte votre invitation et qu’il modifie par la suite son adresse e-mail, le nouvel e-mail n’est pas automatiquement synchronisé avec l’objet utilisateur invité dans votre annuaire. La propriété de messagerie est créée via [Microsoft API Graph](/graph/api/resources/user). Vous pouvez mettre à jour la propriété de messagerie via l’API Microsoft Graph, le centre d’administration Exchange ou [Exchange Online PowerShell](/powershell/module/exchange/users-and-groups/set-mailuser). La modification sera reflétée dans l’objet utilisateur invité Azure AD.

## <a name="next-steps"></a>Étapes suivantes

* [Qu'est-ce que la collaboration B2B d'Azure AD ?](what-is-b2b.md)
* [Jetons utilisateur B2B Collaboration](user-token.md)
* [Mappage des revendications utilisateur B2B Collaboration](claims-mapping.md)
