---
title: Utiliser des groupes Azure AD pour gérer les attributions de rôle – Azure Active Directory
description: Utilisez des groupes Azure AD pour simplifier la gestion des attributions de rôle dans Azure Active Directory.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: article
ms.date: 09/28/2021
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: e3826c7a2cc50e90b42ff1a4f0047dc5a6ca1cf6
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132520728"
---
# <a name="use-azure-ad-groups-to-manage-role-assignments"></a>Utiliser des groupes Azure AD pour gérer les attributions de rôle

Azure Active Directory (Azure AD) vous permet de cibler des groupes Azure AD pour les attributions de rôle. L’attribution de rôles à des groupes peut simplifier la gestion des attributions de rôles dans Azure AD avec un effort minimal de la part de vos administrateurs généraux et administrateurs de rôles privilégiés.

## <a name="why-assign-roles-to-groups"></a>Pourquoi attribuer des rôles à des groupes ?

Prenons l’exemple de l’entreprise Contoso qui a recruté des personnes dans plusieurs zones géographiques afin de gérer et réinitialiser les mots de passe des employés de son organisation Azure AD. Plutôt que de demander à un administrateur de rôles privilégiés ou à un administrateur général d’attribuer le rôle Administrateur du support technique à chaque personne individuellement, elle peut créer un groupe Contoso_Helpdesk_Administrators et attribuer le rôle au groupe. Lorsque des personnes rejoignent le groupe, elles se voient attribuer le rôle indirectement. Votre flux de travail de gouvernance actuel peut alors prendre en charge le processus d’approbation et l’audit de l’appartenance du groupe pour s’assurer que seuls les utilisateurs légitimes sont membres du groupe et donc affectés au rôle Administrateur du support technique.

## <a name="how-role-assignments-to-groups-work"></a>Fonctionnement des attributions de rôle aux groupes

Pour attribuer un rôle à un groupe, vous devez créer un nouveau groupe de sécurité ou Microsoft 365 avec la propriété `isAssignableToRole` définie sur `true`. Dans le portail Azure, définissez l’option **Des rôles Azure AD peuvent être attribués au groupe** sur **Oui**. Dans les deux cas, vous pouvez attribuer un ou plusieurs rôles Azure AD au groupe de la même façon que vous attribuez des rôles aux utilisateurs.

![Capture d’écran de la page Rôles et administrateurs](./media/groups-concept/role-assignable-group.png)

## <a name="restrictions-for-role-assignable-groups"></a>Restrictions pour les groupes assignables à un rôle

Les groupes assignables à un rôle présentent les restrictions suivantes :

- Vous pouvez uniquement définir la propriété `isAssignableToRole` ou l’option **Des rôles Azure AD peuvent être attribués au groupe** pour les nouveaux groupes.
- La propriété `isAssignableToRole` est **immuable**. Une fois qu’un groupe a été créé avec cette propriété définie, il ne peut plus être modifié.
- Vous ne pouvez pas faire d’un groupe existant un groupe assignable à un rôle.
- Un maximum de 400 groupes pouvant se voir attribuer des rôles peuvent être créés dans une même organisation Azure AD (locataire).

## <a name="how-are-role-assignable-groups-protected"></a>Comment les groupes assignables à un rôle sont-ils protégés ?

Si un rôle est attribué à un groupe, tout administrateur informatique qui peut gérer l’appartenance au groupe peut également gérer indirectement l’appartenance à ce rôle. Par exemple, supposons qu’un groupe nommé Contoso_User_Administrators se voit attribuer le rôle Administrateur d’utilisateurs. Un Administrateur Exchange qui peut modifier l’appartenance au groupe peut s’ajouter au groupe Contoso_User_Administrators et devenir ainsi Administrateur d’utilisateurs. Comme vous pouvez le voir, un administrateur peut élever ses privilèges d’une manière que vous n’aviez pas prévue.

Seuls les groupes dont la propriété `isAssignableToRole` est définie sur `true` au moment de la création peuvent se voir attribuer un rôle. La propriété est immuable. Une fois qu’un groupe a été créé avec cette propriété définie, il ne peut plus être modifié. Vous ne pouvez pas définir la propriété sur un groupe existant.

Les groupes assignables à un rôle sont conçus pour aider à prévenir les violations potentielles en respectant les restrictions suivantes :

- Seuls les administrateurs généraux et les administrateurs de rôles privilégiés peuvent créer un groupe assignable à un rôle.
- Le type d’appartenance pour les groupes assignables à un rôle doit être « Affecté » et ne peut pas être un groupe dynamique Azure AD. L’alimentation automatisée de groupes dynamiques peut entraîner l’ajout d’un compte indésirable au groupe et don son affectation au rôle.
- Par défaut, seuls les Administrateurs généraux et les Administrateurs de rôle privilégié peuvent gérer l’appartenance à un groupe attribuable à un rôle, mais vous pouvez déléguer la gestion des groupes attribuables à un rôle en leur ajoutant des propriétaires de groupe.
- L’autorisation Microsoft Graph RoleManagement.ReadWrite.All est nécessaire pour pouvoir gérer l’appartenance à de tels groupes ; Group.ReadWrite.All ne fonctionne pas.
- Pour empêcher une élévation des privilèges, seul un Administrateur de rôle privilégié ou un Administrateur général peut modifier les informations d’identification ou réinitialiser l’authentification multifacteur pour les membres et propriétaires d’un groupe attribuable à un rôle.
- L’imbrication des groupes n’est pas prise en charge. Un groupe ne peut pas être ajouté en tant que membre d’un groupe assignable à un rôle.

## <a name="use-pim-to-make-a-group-eligible-for-a-role-assignment"></a>Utiliser PIM pour rendre un groupe qualifié pour à une attribution de rôle

Si vous ne souhaitez pas que les membres du groupe disposent d’un accès permanent à un rôle, vous pouvez utiliser [Azure AD Privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md) pour rendre un groupe qualifié pour à une attribution de rôle. Chaque membre du groupe est alors qualifié pour activer l’attribution de rôle pour une durée déterminée.

> [!NOTE]
> Pour les groupes d’accès privilégiés utilisés pour l’élévation des rôles Azure AD, nous recommandons de demander un processus d’approbation pour les attributions de membres éligibles. Les attributions qui peuvent être activées sans approbation peuvent créer un risque de sécurité pour les administrateurs qui ont un niveau d’autorisations inférieur. Par exemple, l’administrateur du support technique a l’autorisation de réinitialiser le mot de passe d’un utilisateur éligible.

## <a name="scenarios-not-supported"></a>Scénarios non pris en charge

Les scénarios suivants ne sont pas pris en charge :  

- Attribuer des rôles Azure AD (intégrés ou personnalisés) à des groupes locaux.

## <a name="known-issues"></a>Problèmes connus

Voici les problèmes connus concernant les groupes assignables à un rôle :

- *Clients titulaires d’une licence Azure AD P2 uniquement :* même après avoir supprimé le groupe, celui-ci apparaît toujours comme membre qualifié du rôle dans l’interface utilisateur PIM. Fonctionnellement, il n’y a aucun problème ; il s’agit simplement d’un problème de cache dans le portail Azure.  
- Utilisez le nouveau [centre d’administration Exchange](https://admin.exchange.microsoft.com/) pour les attributions de rôle via l’appartenance de groupe. L’ancien centre d’administration Exchange ne prend pas encore en charge cette fonctionnalité. Les cmdlets Exchange PowerShell fonctionnent normalement.
- Le portail Azure Information Protection (le portail classique) ne reconnaît pas encore l’appartenance au rôle par le biais d’un groupe. Vous pouvez [migrer vers la plateforme unifiée d’étiquetage de confidentialité](/azure/information-protection/configure-policy-migrate-labels), puis utiliser le centre Security & Compliance d’Office 365 pour gérer les rôles à l’aide des affectations de groupe.
- La plateforme [Apps admin center](https://config.office.com/) ne prend pas encore en charge cette fonctionnalité. Attribuez les utilisateurs directement au rôle Administrateur d’applications Office.

## <a name="license-requirements"></a>Conditions de licence :

L'utilisation de cette fonctionnalité nécessite une licence Azure AD Premium P1. En outre, l’utilisation de Privileged Identity Management à des fins d’activation de rôle juste-à-temps requiert une licence Azure AD Premium P2. Pour trouver la licence appropriée à vos besoins, consultez [Comparaison des fonctionnalités mises à la disposition générale des éditions Gratuite et Premium](https://www.microsoft.com/security/business/identity-access-management/azure-ad-pricing).

## <a name="next-steps"></a>Étapes suivantes

- [Créer un groupe assignable à un rôle](groups-create-eligible.md)
- [Attribuer des rôles Azure AD aux groupes](groups-assign-role.md)
