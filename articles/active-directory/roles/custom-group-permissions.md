---
title: Autorisations de gestion de groupes pour les rôles personnalisés Azure AD (préversion) – Azure Active Directory
description: Autorisations de gestion de groupes pour les rôles personnalisés Azure AD (préversion) dans le portail Azure, PowerShell ou l’API Microsoft Graph.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: reference
ms.date: 10/26/2021
ms.author: rolyon
ms.reviewer: ''
ms.custom: it-pro
ms.openlocfilehash: 5e768d9661a7030fb75d8089a6a49e9d5022d612
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131057207"
---
# <a name="group-management-permissions-for-azure-ad-custom-roles-preview"></a>Autorisations de gestion de groupes pour les rôles personnalisés Azure AD (préversion)

> [!IMPORTANT]
> Les autorisations de gestion de groupes pour les rôles personnalisés Azure AD sont actuellement en PRÉVERSION.
> Pour connaître les conditions juridiques qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou plus généralement non encore en disponibilité générale, consultez [l’Avenant aux conditions d’utilisation des préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Les autorisations de gestion de groupes peuvent être utilisées dans les définitions de rôles personnalisés dans Azure Active Directory (Azure AD) pour accorder un accès précis comme suit :

- Gérer les propriétés des groupes, comme le nom et la description
- Gérer les membres et les propriétaires
- Créer ou supprimer des groupes
- Lire les journaux d’audit
- Gérer un type spécifique de groupe

Cet article répertorie les autorisations que vous pouvez utiliser dans vos rôles personnalisés pour différents scénarios de gestion de groupes. Pour plus d’informations sur la création de rôles personnalisés, consultez [Créer et attribuer un rôle personnalisé](custom-create.md).

> [!NOTE]
> L’attribution de rôles personnalisés dans l’étendue d’un groupe en utilisant le portail Azure est actuellement disponible **seulement** pour Azure AD Premium P1.

## <a name="how-to-interpret-group-management-permissions"></a>Interprétation des autorisations de gestion de groupes

Pour interpréter les autorisations de gestion de groupes, il est utile de comprendre ce que signifient les différents sous-types d’autorisation.

> [!div class="mx-tableFixed"]
> | Sous-type d’autorisation | Description du sous-type d’autorisation |
> | --- | --- |
> | groups | Gérer des groupes de sécurité et des groupes Microsoft 365, à l’exception des groupes assignables à un rôle |
> | groups.unified | Gérer les groupes Microsoft 365 des types d’appartenance dynamique et attribué, à l’exclusion des groupes assignables à un rôle |
> | groups.unified.assignedMembership | Gérer les groupes Microsoft 365 uniquement du type d’appartenance attribué, à l’exclusion des groupes assignables à un rôle |
> | groups.security | Gérer les groupes de sécurité des types d’appartenance dynamique et attribué, à l’exclusion des groupes assignables à un rôle |
> | groups.security.assignedMembership | Gérer les groupes de sécurité uniquement du type d’appartenance attribué, à l’exclusion des groupes assignables à un rôle |

Le tableau suivant contient des exemples d’autorisations pour la mise à jour des membres d’un groupe de différents sous-types. 

> [!div class="mx-tableFixed"]
> | Exemple d’autorisation | Description de l’autorisation |
> | --- | --- |
> | microsoft.directory/**groups**/members/update | Actualiser la propriété membres des groupes de Sécurité et des groupes Microsoft 365, à l’exception des groupes à attribution de rôle |
> | microsoft.directory/**groups.unified**/members/update | Actualiser les membres de groupes Microsoft 365, à l’exclusion des groupes à attribution de rôle |
> | microsoft.directory/**groups.unified.assignedMembership**/members/update | Actualiser les membres de groupes Microsoft 365 du type d’appartenance attribué, à l’exclusion des groupes assignables à un rôle |
> | microsoft.directory/**groups.security**/members/update | Actualiser les membres de groupes de Sécurité, à l’exclusion des groupes à attribution de rôle |
> | microsoft.directory/**groups.security.assignedMembership**/members/update | Actualiser les membres de groupes de sécurité du type d’appartenance attribué, à l’exclusion des groupes assignables à un rôle |

## <a name="read-group-information"></a>Lire les informations de groupes

Les autorisations suivantes sont disponibles pour lire les propriétés, les membres et les propriétaires des groupes.

> [!div class="mx-tableFixed"]
> | Autorisation | Description |
> | ---------- | ----------- |
> | microsoft.directory/groups/allProperties/read | Lire toutes les propriétés (notamment les propriétés privilégiées) des groupes de Sécurité et des groupes Microsoft 365, notamment les groupes à attribution de rôle |
> | microsoft.directory/groups/standard/read | Lire les propriétés standard des groupes de Sécurité et des groupes Microsoft 365, notamment les groupes à attribution de rôle |
> | microsoft.directory/groups/members/read | Lire la propriété membres des groupes de Sécurité et des groupes Microsoft 365, notamment les groupes à attribution de rôle |
> | microsoft.directory/groups/memberOf/read | Lire la propriété memberOf des groupes de Sécurité et des groupes Microsoft 365, notamment les groupes à attribution de rôle |
> | microsoft.directory/groups/owners/read | Lire la propriété propriétaires des groupes de Sécurité et des groupes Microsoft 365, notamment les groupes à attribution de rôle |

## <a name="create-groups"></a>Créer des groupes

Les autorisations suivantes sont disponibles pour créer des groupes de différents types.

> [!div class="mx-tableFixed"]
> | Autorisation | Description |
> | ---------- | ----------- |
> | microsoft.directory/groups/create | Créer des groupes de Sécurité et des groupes Microsoft 365, à l’exception des groupes à attribution de rôle |
> | microsoft.directory/groups.unified/create | Créer des groupes Microsoft 365, à l’exclusion des groupes à attribution de rôle |
> | microsoft.directory/groups.unified.assignedMembership/create | Créer des groupes Microsoft 365 du type d’appartenance attribué, à l’exclusion des groupes assignables à un rôle |
> | microsoft.directory/groups.security/create | Créer des groupes de Sécurité, à l’exclusion des groupes à attribution de rôle |
> | microsoft.directory/groups.security.assignedMembership/create | Créer des groupes de sécurité du type d’appartenance attribué, à l’exclusion des groupes assignables à un rôle |
> | microsoft.directory/groups/createAsOwner | Créer des groupes de sécurité et des groupes Microsoft 365, à l’exception des groupes assignables à un rôle. Le créateur est ajouté en tant que premier propriétaire. |
> | microsoft.directory/groups.unified/createAsOwner | Créer des groupes Microsoft 365, à l’exclusion des groupes assignables à un rôle. Le créateur est ajouté en tant que premier propriétaire. |
> | microsoft.directory/groups.unified.assignedMembership/createAsOwner | Créer des groupes Microsoft 365 du type d’appartenance attribué, à l’exclusion des groupes assignables à un rôle. Le créateur est ajouté en tant que premier propriétaire. |
> | microsoft.directory/groups.security/createAsOwner | Créer des groupes de Sécurité, à l’exclusion des groupes à attribution de rôle Le créateur est ajouté en tant que premier propriétaire. |
> | microsoft.directory/groups.security.assignedMembership/createAsOwner | Créer des groupes de sécurité du type d’appartenance attribué, à l’exclusion des groupes assignables à un rôle. Le créateur est ajouté en tant que premier propriétaire. |

## <a name="update-group-information"></a>Actualiser les informations des groupes

Les autorisations suivantes sont disponibles pour mettre à jour les propriétés et les membres des groupes.

> [!div class="mx-tableFixed"]
> | Autorisation | Description |
> | ---------- | ----------- |
> | microsoft.directory/groups/allProperties/update | Actualiser toutes les propriétés (notamment les propriétés privilégiées) des groupes de sécurité et des groupes Microsoft 365, à l’exclusion des groupes assignables à un rôle |
> | microsoft.directory/groups.unified/allProperties/update | Actualiser toutes les propriétés (notamment les propriétés privilégiées) des groupes Microsoft 365, à l’exclusion des groupes assignables à un rôle |
> | microsoft.directory/groups.unified.assignedMembership/allProperties/update | Actualiser toutes les propriétés (notamment les propriétés privilégiées) des groupes Microsoft 365 du type d’appartenance attribué, à l’exception des groupes assignables à un rôle |
> | microsoft.directory/groups.security/allProperties/update | Actualiser toutes les propriétés (notamment les propriétés privilégiées) des groupes de sécurité, à l’exclusion des groupes assignables à un rôle |
> | microsoft.directory/groups.security.assignedMembership/allProperties/update | Actualiser toutes les propriétés (notamment les propriétés privilégiées) des groupes de sécurité du type d’appartenance attribué, à l’exception des groupes assignables à un rôle |
> | microsoft.directory/groups/basic/update | Actualiser les propriétés de base des groupes de Sécurité et des groupes Microsoft 365, à l’exception des groupes à attribution de rôle |
> | microsoft.directory/groups.unified/basic/update | Actualiser les propriétés de base sur les groupes Microsoft 365, à l’exclusion des groupes à attribution de rôle |
> | microsoft.directory/groups.unified.assignedMembership/basic/update | Actualiser les propriétés de base des groupes Microsoft 365 du type d’appartenance attribué, à l’exclusion des groupes assignables à un rôle |
> | microsoft.directory/groups.security/basic/update | Actualiser les propriétés de base de groupes de Sécurité, à l’exclusion des groupes à attribution de rôle |
> | microsoft.directory/groups.security.assignedMembership/basic/update | Actualiser les propriétés de base des groupes de sécurité du type d’appartenance attribué, à l’exclusion des groupes assignables à un rôle |
> | microsoft.directory/groups/classification/update | Actualiser les propriétés de classification des groupes de Sécurité et des groupes Microsoft 365, à l’exception des groupes à attribution de rôle |
> | microsoft.directory/groups.unified/classification/update | Actualiser la propriété de classification des groupes Microsoft 365, à l’exclusion des groupes assignables à un rôle |
> | microsoft.directory/groups.unified.assignedMembership/classification/update | Actualiser la propriété de classification des groupes Microsoft 365 du type d’appartenance attribué, à l’exclusion des groupes assignables à un rôle |
> | microsoft.directory/groups.security/classification/update | Actualiser la propriété de classification de groupes de Sécurité, à l’exclusion des groupes à attribution de rôle |
> | microsoft.directory/groups.security.assignedMembership/classification/update | Actualiser la propriété de classification des groupes de sécurité du type d’appartenance attribué, à l’exclusion des groupes assignables à un rôle |
> | microsoft.directory/groups/dynamicMembershipRule/update | Actualiser la règle d’adhésion dynamique des groupes de Sécurité et des groupes Microsoft 365, à l’exception des groupes à attribution de rôle |
> | microsoft.directory/groups.unified/dynamicMembershipRule/update | Actualiser la règle d’adhésion dynamique des groupes Microsoft 365, à l’exception des groupes assignables à un rôle |
> | microsoft.directory/groups.security/dynamicMembershipRule/update | Actualiser la règle d’adhésion dynamique des groupes de sécurité, à l’exclusion des groupes assignables à un rôle |
> | microsoft.directory/groups/members/update | Actualiser la propriété membres des groupes de Sécurité et des groupes Microsoft 365, à l’exception des groupes à attribution de rôle |
> | microsoft.directory/groups.unified/members/update | Actualiser les membres de groupes Microsoft 365, à l’exclusion des groupes à attribution de rôle |
> | microsoft.directory/groups.unified.assignedMembership/members/update | Actualiser les membres de groupes Microsoft 365 du type d’appartenance attribué, à l’exclusion des groupes assignables à un rôle |
> | microsoft.directory/groups.security/members/update | Actualiser les membres de groupes de Sécurité, à l’exclusion des groupes à attribution de rôle |
> | microsoft.directory/groups.security.assignedMembership/members/update | Actualiser les membres de groupes de sécurité du type d’appartenance attribué, à l’exclusion des groupes assignables à un rôle |

## <a name="update-members-of-different-group-types"></a>Actualiser les membres de différents types de groupes

Les autorisations suivantes sont disponibles pour actualiser les membres de différents types de groupes.

> [!div class="mx-tableFixed"]
> | Autorisation | Description |
> | ---------- | ----------- |
> | microsoft.directory/groups/members/update | Actualiser la propriété membres des groupes de Sécurité et des groupes Microsoft 365, à l’exception des groupes à attribution de rôle |
> | microsoft.directory/groups.unified/members/update | Actualiser les membres de groupes Microsoft 365, à l’exclusion des groupes à attribution de rôle |
> | microsoft.directory/groups.unified.assignedMembership/members/update | Actualiser les membres de groupes Microsoft 365 du type d’appartenance attribué, à l’exclusion des groupes assignables à un rôle |
> | microsoft.directory/groups.security/members/update | Actualiser les membres de groupes de Sécurité, à l’exclusion des groupes à attribution de rôle |
> | microsoft.directory/groups.security.assignedMembership/members/update | Actualiser les membres de groupes de sécurité du type d’appartenance attribué, à l’exclusion des groupes assignables à un rôle |
 
## <a name="delete-groups"></a>Supprimer des groupes

Les autorisations suivantes sont disponibles pour supprimer des groupes.

> [!div class="mx-tableFixed"]
> | Autorisation | Description |
> | ---------- | ----------- |
> | microsoft.directory/groups/delete | Supprimer des groupes de Sécurité et des groupes Microsoft 365, à l’exception des groupes à attribution de rôle |
> | microsoft.directory/groups.unified/members/update | Actualiser les membres de groupes Microsoft 365, à l’exclusion des groupes à attribution de rôle |
> | microsoft.directory/groups.unified.assignedMembership/members/update | Actualiser les membres de groupes Microsoft 365 du type d’appartenance attribué, à l’exclusion des groupes assignables à un rôle |
> | microsoft.directory/groups.security/members/update | Actualiser les membres de groupes de Sécurité, à l’exclusion des groupes à attribution de rôle |
> | microsoft.directory/groups.security.assignedMembership/members/update | Actualiser les membres de groupes de sécurité du type d’appartenance attribué, à l’exclusion des groupes assignables à un rôle |

## <a name="next-steps"></a>Étapes suivantes

- [Créer et attribuer un rôle personnalisé dans Azure Active Directory](custom-create.md)
- [Répertorier les attributions de rôle Azure AD](view-assignments.md)
