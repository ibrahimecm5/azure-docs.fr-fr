---
title: Rôles que vous ne pouvez pas gérer dans Privileged Identity Management - Azure Active Directory | Microsoft Docs
description: Décrit les rôles que vous ne pouvez pas gérer dans Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: KarenH444
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 10/07/2021
ms.author: curtand
ms.reviewer: shaunliu
ms.custom: pim ; H1Hack27Feb2017;oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 89cd7e3cfac71e194d8f32ae8b15c26da7f0615d
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132336465"
---
# <a name="roles-you-cant-manage-in-privileged-identity-management"></a>Rôles que vous ne pouvez pas gérer dans Privileged Identity Management

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) vous permet de gérer tous les [rôles Azure AD](../roles/permissions-reference.md) et tous les [rôles Azure](../../role-based-access-control/built-in-roles.md). Les rôles Azure peuvent également inclure vos rôles personnalisés, attachés à vos groupes d’administration, abonnements, groupes de ressources et ressources. Toutefois, il y a quelques rôles que vous ne pouvez pas gérer dans PIM. Cet article décrit les rôles que vous ne pouvez pas gérer dans Privileged Identity Management (PIM).

## <a name="classic-subscription-administrator-roles"></a>Rôles d’administrateur d’abonnements classique

Vous ne pouvez pas gérer les rôles d’administrateur d’abonnements classiques suivants dans Privileged Identity Management :

- Administrateur de comptes
- Administrateur de services
- Coadministrateur

Pour plus d’informations sur les rôles d’administrateur d’abonnements classiques, consultez [Rôles d’administrateur d’abonnement classique, rôles Azure et rôles d’administrateur Azure AD](../../role-based-access-control/rbac-and-directory-admin-roles.md).

## <a name="what-about-microsoft-365-admin-roles"></a>Qu’en est-il des rôles d’administrateur Microsoft 365 ?

Nous prenons en charge tous les rôles Microsoft 365 dans l’expérience de portail Rôles et administrateurs Azure AD, tels que l’administrateur Exchange et l’administrateur SharePoint, mais nous ne prenons pas en charge les rôles spécifiques dans les sessions RBAC Exchange ou RBAC SharePoint. Pour plus d’informations sur ces services Microsoft 365, consultez [Rôles d’administrateur Microsoft 365](/office365/admin/add-users/about-admin-roles).

> [!NOTE]
> - Les utilisateurs éligibles pour le rôle Administrateur SharePoint, le rôle Administrateur de périphériques, ainsi que tous les rôles tentant d’accéder au Centre de sécurité et de conformité Microsoft peuvent rencontrer des retards pouvant atteindre plusieurs heures après l’activation de leur rôle. Nous travaillons en collaboration avec ces équipes pour résoudre les problèmes.
> - Pour plus d’informations sur les retards d’activation du rôle Administrateur local d’appareil joint Azure AD, consultez [Guide pratique pour gérer le groupe Administrateurs local sur des appareils joints Azure AD](../devices/assign-local-admin.md#manage-the-device-administrator-role).

## <a name="next-steps"></a>Étapes suivantes

- [Attribuer des rôles dans Azure AD dans Privileged Identity Management](pim-how-to-add-role-to-user.md)
- [Attribuer des rôles de ressources Azure dans Privileged Identity Management](pim-resource-roles-assign-roles.md)