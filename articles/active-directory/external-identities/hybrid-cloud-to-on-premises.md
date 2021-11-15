---
title: Accorder aux utilisateurs B2B dans Azure AD l’accès à vos applications locales - Azure AD
description: Montre comment accorder aux utilisateurs B2B cloud l’accès aux applications locales avec Azure AD B2B Collaboration.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 11/05/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7ebaa3c9b6a931a01eb65a28cd48cf29e74c13b3
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2021
ms.locfileid: "131892397"
---
# <a name="grant-b2b-users-in-azure-ad-access-to-your-on-premises-applications"></a>Accorder aux utilisateurs B2B dans Azure AD l’accès à vos applications locales

Si votre organisation utilise des fonctionnalités d’Azure Active Directory (Azure AD) B2B Collaboration pour inviter des utilisateurs d’organisations partenaires à votre instance Azure AD, vous pouvez désormais fournir à ces utilisateurs B2B un accès aux applications locales. Ces applications locales peuvent utiliser l’authentification basée SAML ou l’Authentification Windows intégrée (IWA) avec la délégation contrainte Kerberos (KCD).

## <a name="access-to-saml-apps"></a>Accès aux applications SAML

Si votre application locale utilise l’authentification basée sur SAML, vous pouvez facilement rendre ces applications accessibles pour les utilisateurs Azure AD B2B Collaboration via le portail Azure à l’aide du proxy d’application Azure AD.

Vous devez effectuer les opérations suivantes :

- Activez le proxy d’application et installez un connecteur. Pour des instructions, consultez [Publication d’applications à l’aide du proxy d’application Azure AD](../app-proxy/application-proxy-add-on-premises-application.md).
- Publiez l’application SAML locale via le proxy d’application Azure AD en suivant les instructions fournies dans [Authentification unique SAML pour les applications locales avec le proxy d’application](../app-proxy/application-proxy-configure-single-sign-on-on-premises-apps.md).
- Affectez des utilisateurs Azure AD B2B à l’application SAML.

Une fois toutes les étapes ci-dessus effectuées, votre application doit être opérationnelle. Pour tester l’accès à Azure AD B2B :
1.  Ouvrez un navigateur et accédez à l’URL externe que vous avez créée lors de la publication de l’application.
2.  Connectez-vous au compte Azure AD B2B que vous avez attribué à l’application. Vous devez pouvoir ouvrir l’application et y accéder avec l’authentification unique.

## <a name="access-to-iwa-and-kcd-apps"></a>Accès aux applications IWA et KCD

Pour fournir aux utilisateurs B2B l’accès aux applications locales sécurisées avec l’Authentification Windows intégrée et la délégation contrainte Kerberos, vous avez besoin des composants suivants :

- **Authentification via le Proxy d’application Azure AD**. Les utilisateurs B2B doivent être en mesure de s’authentifier sur l’application locale. Pour ce faire, vous devez publier l’application locale via le proxy d’application Azure AD. Pour plus d’informations, consultez [Didacticiel : Ajouter des applications locales pour un accès à distance via le service Proxy d’application](../app-proxy/application-proxy-add-on-premises-application.md).
- **Autorisation via un objet utilisateur B2B dans le répertoire local**. L’application doit être en mesure d’effectuer des contrôles d’accès utilisateur et d’accorder l’accès aux ressources appropriées. Pour finaliser cette autorisation, IWA et KCD exigent un objet utilisateur dans l’annuaire Windows Server Active Directory. Comme décrit dans [Fonctionnement de l’authentification unique avec KCD](../app-proxy/application-proxy-configure-single-sign-on-with-kcd.md#how-single-sign-on-with-kcd-works), le Proxy d’application a besoin de cet objet utilisateur pour emprunter l’identité de l’utilisateur et obtenir un jeton Kerberos pour l’application. 

   > [!NOTE]
   > Lorsque vous configurez le proxy d’application Azure AD, assurez-vous que l’option **Identité de connexion déléguée** est définie sur **Nom principal de l’utilisateur** (valeur par défaut) dans la configuration de l’authentification unique pour l’Authentification Windows intégrée (IWA).

   Dans le scénario de l’utilisateur B2B, il existe deux méthodes que vous pouvez utiliser pour créer les objets utilisateur invité requis pour l’autorisation dans le répertoire local :

   - Microsoft Identity Manager (MIM) et l’agent de gestion MIM pour Microsoft Graph.
   - Un script PowerShell, qui est une solution plus simple qui ne nécessite pas MIM.

Le diagramme suivant fournit une vue d’ensemble globale du Proxy d’application Azure AD et de la génération de l’objet utilisateur B2B dans le répertoire local qui permettent d’accorder l’accès aux utilisateurs B2B à vos applications IWA et KCD locales. La procédure pas à pas est décrite en détail sous le diagramme.

![Diagramme des solutions de script MIM et B2B](media/hybrid-cloud-to-on-premises/MIMScriptSolution.PNG)

1.  Un utilisateur d’une organisation partenaire (le locataire Fabrikam) est invité par le locataire Contoso.
2.  Un objet utilisateur invité est créé dans le locataire Contoso (par exemple, un objet utilisateur avec un UPN guest_fabrikam.com#EXT#@contoso.onmicrosoft.com).
3.  L’invité Fabrikam est importé à partir de Contoso via MIM ou via le script PowerShell B2B.
4.  Une représentation ou « empreinte » de l’objet d’utilisateur invité Fabrikam (Guest#EXT#) est créée dans le répertoire local, Contoso.com, via MIM ou via le script PowerShell B2B.
5.  L’utilisateur invité accède à l’application locale, app.contoso.com.
6.  La demande d’authentification est autorisée par le Proxy d’application, à l’aide de la délégation contrainte Kerberos. 
7.  L’objet d’utilisateur invité existant en local, l’authentification réussit.

### <a name="lifecycle-management-policies"></a>Stratégies de gestion du cycle de vie

Vous pouvez gérer les objets utilisateur B2B locaux par le biais de stratégies de gestion du cycle de vie. Par exemple :

- Vous pouvez définir des stratégies d’authentification multifacteur (MFA) pour l’utilisateur invité afin que l’authentification multifacteur soit utilisée lors de l’authentification du Proxy d’application. Pour plus d’informations, voir [Accès conditionnel pour les utilisateurs de collaboration B2B](conditional-access.md).
- Les parrainages, révisions d’accès, vérifications de compte, etc. effectués pour l’utilisateur B2B cloud s’appliquent aux utilisateurs locaux. Par exemple, si l’utilisateur cloud est supprimé via vos stratégies de gestion du cycle de vie, l’utilisateur local est également supprimé par la synchronisation MIM ou via la synchronisation Azure AD Connect. Pour plus d’informations, consultez [Gérer l’accès invité avec les révisions d’accès Azure AD](../governance/manage-guest-access-with-access-reviews.md).

### <a name="create-b2b-guest-user-objects-through-mim"></a>Créer des objets utilisateur invité B2B via MIM

Pour en savoir plus sur l’utilisation de MIM 2016 Service Pack 1 et de l’agent de gestion MIM pour Microsoft Graph afin de créer les objets utilisateur invité dans le répertoire local, consultez [Collaboration interentreprises (B2B) Azure AD avec Microsoft Identity Manager (MIM) 2016 SP1 et le Proxy d’application Azure](/microsoft-identity-manager/microsoft-identity-manager-2016-graph-b2b-scenario).

## <a name="license-considerations"></a>Remarques sur la licence

Assurez-vous que vous disposez des licences d’accès client (CAL) appropriées pour les utilisateurs invités externes qui accèdent aux applications locales. Pour plus d’informations, consultez la section « Connecteurs externes » de [Licences d’accès client et licences de gestion](https://www.microsoft.com/licensing/product-licensing/client-access-license.aspx). Pour vos besoins particuliers de licences, consultez votre représentant ou votre revendeur local Microsoft.

## <a name="next-steps"></a>Étapes suivantes

- Voir également [Azure Active Directory B2B Collaboration pour les organisations hybrides](hybrid-organizations.md)

- Pour obtenir une vue d’ensemble d’Azure AD Connect, consultez [Intégrer des répertoires locaux à Azure Active Directory](../hybrid/whatis-hybrid-identity.md).
