---
title: Sécuriser l’accès externe avec des groupes dans Azure Active Directory et Microsoft 365
description: Les groupes Azure Active Directory et Microsoft 365 peuvent être utilisés pour renforcer la sécurité lorsque des utilisateurs externes accèdent à vos ressources.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: c134f657e4037627fc39f321d9e579c8682a6b58
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131067077"
---
# <a name="securing-external-access-with-groups"></a>Sécurisation de l’accès externe avec des groupes 

Les groupes constituent une part essentielle de toute stratégie de contrôle d’accès. Les groupes de sécurité Azure Active Directory (Azure AD) et les groupes Microsoft 365 peuvent servir de base pour sécuriser l’accès aux ressources.

Les groupes sont la meilleure option à utiliser comme base pour les mécanismes de contrôle d’accès suivants :

* Stratégies d’accès conditionnel

* Packages d’accès à la gestion des droits d’utilisation 

* Accès aux ressources Microsoft 365, à Microsoft Teams et aux sites SharePoint

Les groupes ont les rôles suivants :

* Propriétaires – Les propriétaires d’un groupe gèrent les paramètres du groupe et son appartenance.

* Membres – Les membres héritent des autorisations et de l’accès affectés au groupe.

* Invités – Les invités sont des membres extérieurs à votre organisation. 

## <a name="determine-your-group-strategy"></a>Déterminer votre stratégie de groupe

Au fur et à mesure que vous développez votre stratégie de groupe pour sécuriser l’accès externe à vos ressources, prenez en compte [votre posture de sécurité souhaitée](1-secure-access-posture.md) pour déterminer les choix suivants.

* **Qui doit être en mesure de créer des groupes ?** Voulez-vous que seuls les administrateurs créent des groupes ou souhaitez-vous que les employés et ou les utilisateurs externes créent également ces groupes ?

   * *Par défaut, tout membre de locataire peut créer des groupes de sécurité Azure AD*. 

      * Vous pouvez [limiter l’accès au portail pour les non-administrateurs](../develop/howto-restrict-your-app-to-a-set-of-users.md) et désactiver la capacité de création de groupes dans [PowerShell.](../enterprise-users/groups-troubleshooting.md) 

      * Vous pouvez également [configurer la gestion de groupes en libre-service dans Azure Active Directory](../enterprise-users/groups-self-service-management.md). 

   * *Par défaut, tous les utilisateurs peuvent créer des groupes Microsoft 365 et les groupes sont ouverts à tous les utilisateurs (internes et externes) figurant dans votre locataire*. 

      * [Vous pouvez limiter la création de groupes Microsoft 365](/microsoft-365/solutions/manage-creation-of-groups) aux membres d’un groupe de sécurité particulier. Utilisez Windows PowerShell pour configurer ce paramètre. 

* **Qui doit être en mesure d’inviter des personnes dans des groupes ?** Tous les membres d’un groupe peuvent-ils ajouter d’autres membres ou seuls les propriétaires de groupe doivent-ils être en mesure d’ajouter des membres ?

* **Qui peut être invité dans des groupes ?** Par défaut, les utilisateurs externes peuvent être ajoutés dans des groupes. 

### <a name="assign-users-to-groups"></a>Assigner des utilisateurs à des groupes

Les utilisateurs peuvent être affectés à des groupes manuellement en fonction des attributs utilisateur dans l’objet utilisateur ou selon d’autres critères. Les utilisateurs ne peuvent être affectés aux groupes que de manière dynamique, en fonction de leurs attributs.

Par exemple, vous pouvez affecter des utilisateurs à des groupes en fonction de leur :

* poste ou service spécifique

* organisation partenaire à laquelle ils appartiennent (manuellement ou via des organisations connectées)

* type d’utilisateur (Membre ou Invité)

* participation à un projet spécifique (manuellement)

* location

Les groupes dynamiques peuvent contenir des utilisateurs ou des appareils, mais pas les deux. Vous ajoutez des requêtes basées sur les attributs utilisateur pour affecter des utilisateurs au groupe dynamique. L’exemple ci-dessous montre des requêtes qui ajoutent des utilisateurs au groupe s’ils sont membres (et non invités) et dans le service financier.

![Capture d’écran montrant la configuration de règles d’adhésion dynamiques](media/secure-external-access/4-dynamic-membership-rules.png)

Pour plus d’informations sur les groupes dynamiques, consultez [Créer ou mettre à jour un groupe dynamique dans Azure Active Directory.](../enterprise-users/groups-create-rule.md)

### <a name="do-not-use-groups-for-multiple-purposes"></a>Ne pas utiliser de groupes pour plusieurs objectifs

Lorsque vous utilisez des groupes pour des raisons de sécurité ou d’accès aux ressources, il est important qu’ils aient une seule fonction. Si un groupe est utilisé pour accorder l’accès aux ressources, il ne doit pas être utilisé à d’autres fins. Si un groupe est utilisé à des fins génériques, telles que la définition d’un emplacement ou l’appartenance à une équipe, il ne doit pas être également utilisé pour sécuriser l’accès. 

Nous recommandons d’utiliser une convention d’affectation de noms pour les groupes de sécurité qui clarifie l’objectif. Par exemple :

* *Secure_access_finance_apps*

* *Team_membership_finance_team*

* *Location_finance_building*



### <a name="types-of-groups"></a>Types de groupes

Vous pouvez créer des groupes de sécurité Azure AD et des groupes Microsoft 365 à partir du portail Azure AD ou du portail d’administration Microsoft 365. Ces deux types peuvent être utilisés comme base pour sécuriser l’accès externe :

| Considérations | Groupes de sécurité Azure AD (manuels et dynamiques)| Groupes Microsoft 365 |
| - | - | - |
| Que peut contenir le groupe ?| Utilisateurs<br>Groupes<br>Principaux de service<br>Appareils| Utilisateurs uniquement |
| Où le groupe est-il créé ?| Portail Azure AD<br>Portail Microsoft 365 (s’il doit être à extension messagerie)<br>PowerShell<br>Microsoft Graph<br>Portail de l’utilisateur final| Portail Microsoft 365<br>Portail Azure AD<br>PowerShell<br>Microsoft Graph<br>Dans les applications Microsoft 365 |
| Qui le crée par défaut ?| Administrateurs <br>Utilisateurs| Administrateurs<br>Utilisateurs |
| Qui peut être ajouté par défaut ?| Utilisateurs internes (membres abonnés)| Membres de locataire et invités de toute organisation |
| À quoi accorde-t-il l’accès ?| Aux seules ressources auxquelles il est affecté.| À toutes les ressources associées au groupe :<br>(boîte aux lettres de groupe, site, équipe, conversations et autres ressources Microsoft 365 incluses)<br>À toutes les autres ressources auxquelles le groupe est ajouté |
| Peut être utilisé avec| Accès conditionnel<br>Gestion des droits d’utilisation<br>Licences de groupe| Accès conditionnel<br>Gestion des droits d’utilisation<br>Étiquette de confidentialité |



Utilisez les groupes Microsoft 365 pour créer et gérer un ensemble de ressources Microsoft 365, telles qu’une équipe et les sites et le contenu associés. Ils constituent un choix judicieux pour un effort basé sur un projet. 

 

## <a name="azure-ad-security-groups"></a>Groupes de sécurité Azure AD 

Les [groupes de sécurité Azure AD](./active-directory-manage-groups.md) peuvent contenir des utilisateurs ou des appareils, et peuvent être utilisés pour gérer l’accès 

* aux ressources Azure, telles que les applications Microsoft 365, les applications personnalisées et les applications SaaS (Software as a Service) comme ServiceNow de Dropbox.

* aux données et abonnements Azure.

* aux services Azure.

Les groupes de sécurité Azure AD peuvent également être utilisés pour :

* attribuer des licences pour des services tels que Microsoft 365, Dynamics 365 et Enterprise Mobility et Security. Pour plus d’informations, consultez [Gestion des licences par groupe](./active-directory-licensing-whatis-azure-portal.md).

* affecter des autorisations élevées. Pour plus d’informations, consultez [Utiliser des groupes cloud pour gérer les attributions de rôles dans Azure AD](../roles/groups-concept.md). 

Pour créer un groupe [dans le portail Azure](./active-directory-groups-create-azure-portal.md), accédez à Azure Active Directory, puis à Groupes. Vous pouvez également créer des groupes de sécurité Azure AD à l’aide des [applets de commande PowerShell](../enterprise-users/groups-settings-v2-cmdlets.md). 

> [!NOTE]
> Un groupe de sécurité peut être utilisé pour l’attribution de jusqu’à 1 500 applications, mais pas plus. 

![Capture d’écran montrant la création d’un groupe de sécurité](media/secure-external-access/4-create-security-group.png)

> [!IMPORTANT]
> **Pour créer un groupe de sécurité à extension messagerie, accédez au [Centre d’administration Microsoft 365](https://admin.microsoft.com/)** . Vous ne pouvez pas le créer dans le portail Azure AD. 
<br>Vous devez activer un groupe de sécurité pour la messagerie au moment de la création. Vous ne pouvez pas l’activer ultérieurement.

### <a name="hybrid-organizations-and-azure-ad-security-groups"></a>Organisations hybrides et groupes de sécurité Azure AD

Les organisations hybrides possèdent une infrastructure locale et une infrastructure cloud Azure AD. De nombreuses organisations hybrides qui utilisent Active Directory créent leurs groupes de sécurité localement et les synchronisent dans le cloud. Avec cette méthode, seuls les utilisateurs figurant dans l’environnement local peuvent être ajoutés aux groupes de sécurité.

**Protégez votre infrastructure locale de toute compromission, car une violation locale peut être utilisée pour accéder à votre locataire Microsoft 365**. Pour obtenir des conseils, consultez [Protection de Microsoft 365 contre les attaques locales](./protect-m365-from-on-premises-attacks.md).

## <a name="microsoft-365-groups"></a>Groupes Microsoft 365

Les [groupes Microsoft 365](/microsoft-365/admin/create-groups/office-365-groups) sont le service d’appartenance de base qui pilote tous les accès dans Microsoft 365. Ils peuvent être créés à partir du [portail Azure](https://portal.azure.com/) ou du [portail Microsoft 365](https://admin.microsoft.com/). Lorsqu’un groupe Microsoft 365 est créé, vous accordez l’accès à un groupe de ressources utilisé pour collaborer. Consultez [Vue d’ensemble des groupes Microsoft 365 pour les administrateurs](/microsoft-365/admin/create-groups/office-365-groups) pour obtenir la liste complète de ces ressources.

Les groupes Microsoft 365 ont les nuances suivantes pour leurs rôles :

* **Propriétaires** : les propriétaires de groupe peuvent ajouter ou supprimer des membres et disposent d’autorisations administratives uniques dans le groupe, telles que la possibilité de supprimer des conversations de la boîte de réception partagée ou de modifier les paramètres du groupe. Les propriétaires de groupe peuvent renommer le groupe, mettre à jour sa description ou l’image, etc.

* **Membres** : les membres du groupe peuvent accéder à tous les éléments du groupe, mais ils ne peuvent pas modifier les paramètres du groupe. Par défaut, les membres du groupe peuvent inviter des invités à rejoindre votre groupe. Vous pouvez [contrôler ce paramètre](/microsoft-365/admin/create-groups/manage-guest-access-in-groups).

* **Invités** – Les invités sont des membres extérieurs à votre organisation. Par défaut, les invités présentent certaines limites de fonctionnalités dans Teams.

 

### <a name="microsoft-365-group-settings"></a>Paramètres de groupe Microsoft 365

Vous sélectionnez un alias de messagerie, la confidentialité et si vous souhaitez activer le groupe pour Teams au moment de la configuration. 

![Capture d’écran de la modification des paramètres de groupe Microsoft 365](media/secure-external-access/4-edit-group-settings.png)

Après la configuration, vous ajoutez des membres, configurez les paramètres d’utilisation de la messagerie, etc.

### <a name="next-steps"></a>Étapes suivantes

Consultez les articles suivants sur la sécurisation de l’accès externe aux ressources. Nous vous recommandons d’effectuer les actions dans l’ordre indiqué.

1. [Déterminer votre posture de sécurité souhaitée pour l’accès externe](1-secure-access-posture.md)

2. [Découvrir votre état actuel](2-secure-access-current-state.md)

3. [Créer un plan de gouvernance](3-secure-access-plan.md)

4. [Utiliser des groupes pour la sécurité](4-secure-access-groups.md) (Vous êtes ici.)

5. [Transition vers Azure AD B2B](5-secure-access-b2b.md)

6. [Sécuriser l’accès avec la gestion des droits d’utilisation](6-secure-access-entitlement-managment.md)

7. [Sécuriser l’accès avec des stratégies d’Accès conditionnel](7-secure-access-conditional-access.md)

8. [Sécuriser l’accès avec des étiquettes de confidentialité](8-secure-access-sensitivity-labels.md)

9. [Sécuriser l’accès à Microsoft Teams, OneDrive et SharePoint](9-secure-access-teams-sharepoint.md)