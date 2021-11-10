---
title: Attribuer des rôles Azure AD dans PIM - Azure Active Directory | Microsoft Docs
description: Découvrez comment attribuer des rôles Azure AD dans Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: KarenH444
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 10/07/2021
ms.author: curtand
ms.reviewer: shaunliu
ms.collection: M365-identity-device-management
ms.custom: subject-rbac-steps
ms.openlocfilehash: 3ea66bbd5708e5964bb2c258feaa1fc44fcecff1
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131444190"
---
# <a name="assign-azure-ad-roles-in-privileged-identity-management"></a>Attribuer des rôles dans Azure AD dans Privileged Identity Management

Avec Azure Active Directory (Azure AD), un administrateur général peut effectuer des attributions de rôles d'administrateur Azure AD **permanentes**. Ces attributions de rôles peuvent être créées via le [portail Azure](../roles/permissions-reference.md) ou à l’aide de [commandes PowerShell](/powershell/module/azuread#directory_roles).

Le service Azure AD Privileged Identity Management (PIM) permet également aux Administrateurs de rôle privilégié d’établir des attributions permanentes du rôle Administrateur. En outre, les Administrateurs de rôle privilégié peuvent rendre les utilisateurs **éligibles** pour les rôles Administrateur d’Azure AD. Un administrateur éligible peut activer le rôle lorsqu’il en a besoin, puis l’autorisation expirera lorsqu’il aura terminé.

Privileged Identity Management prend en charge les rôles Azure AD personnalisés et intégrés. Pour plus d’informations sur les rôles personnalisés Azure AD, consultez [ Contrôle d’accès en fonction du rôle dans Azure Active Directory](../roles/custom-overview.md).

## <a name="assign-a-role"></a>Attribuer un rôle

Suivez ces étapes pour rendre un utilisateur éligible pour un rôle d'administrateur Azure AD.

1. Connectez-vous au [Portail Azure](https://portal.azure.com/) à l’aide d’un nom d’utilisateur qui est membre du rôle [Administrateur de rôle privilégié](../roles/permissions-reference.md#privileged-role-administrator).

1. Ouvrez **Azure AD Privileged Identity Management**.

1. Sélectionnez des **rôles Azure AD**.

1. Sélectionnez **Rôles** pour afficher la liste des rôles pour les autorisations Azure AD.

    ![Capture d’écran de la page « Rôles » avec l’action « Ajouter des affectations » sélectionnée.](./media/pim-how-to-add-role-to-user/roles-list.png)

1. Sélectionnez **Ajouter des affectations** pour ouvrir la page **Ajouter des affectations**.

1. Choisissez **Sélectionner un rôle** pour ouvrir la page **Sélectionner un rôle**.

    ![Volet Nouvelle affectation](./media/pim-how-to-add-role-to-user/select-role.png)

1. Sélectionnez un rôle, puis un membre à affecter à ce rôle, puis cliquez sur **Suivant**.

1. Dans la liste **Type d’affectation** du volet **Paramètres d’appartenance**, sélectionnez **Éligible** ou **Actif**.

    - Les attributions **éligibles** exigent des membres qu’ils effectuent une action pour utiliser ce rôle. Il peut s’agir de procéder à une vérification de l’authentification multifacteur (MFA), de fournir une justification professionnelle ou de demander une approbation aux approbateurs désignés.

    - Les attributions **actives** n’exigent pas des membres qu’ils effectuent une action pour utiliser ce rôle. Les membres attribués comme étant actifs détiennent à tout moment les privilèges affectés au rôle.

1. Pour spécifier une durée d’attribution spécifique, ajoutez des zones de date et heure de début et de fin. Lorsque vous avez terminé, sélectionnez **Affecter** pour créer la nouvelle attribution de rôle.

    - Les affectations **permanentes** n’ont pas de date d’expiration. Utilisez cette option pour les travailleurs permanents qui ont souvent besoin des autorisations de rôle.

    - Les affectations liées à la **durée** expireront à la fin d’une période spécifiée. Utilisez cette option avec des travailleurs temporaires ou sous contrat, par exemple, dont la date et l’heure de fin du projet sont connues.

    ![Paramètres des appartenances - date et heure](./media/pim-how-to-add-role-to-user/start-and-end-dates.png)

1. Une fois le rôle attribué, une notification d’état d’attribution s’affiche.

    ![Nouvelle affectation - Notification](./media/pim-how-to-add-role-to-user/assignment-notification.png)

## <a name="assign-a-role-with-restricted-scope"></a>Affecter un rôle avec une étendue restreinte

Pour certains rôles, l’étendue des autorisations accordées peut être limitée à une unité d’administration, un principal de service ou une application unique. Cette procédure est un exemple si vous attribuez un rôle qui a l’étendue d’une unité administrative. Pour obtenir la liste des rôles prenant en charge l’étendue via une unité administrative, voir [Attribuer des rôles dont l’étendue est délimitée à une unité administrative](../roles/admin-units-assign-roles.md). Cette fonctionnalité est actuellement déployée dans les organisations Azure AD.

1. connectez-vous au [Centre d'administration Azure Active Directory](https://aad.portal.azure.com) avec les autorisations Administrateur de rôle privilégié.

1. Sélectionnez **Azure Active Directory** > **Rôles et administrateurs**.

1. Sélectionnez **Administrateur d’utilisateurs**.

    ![La commande Ajouter une attribution est disponible lorsque vous ouvrez un rôle dans le portail](./media/pim-how-to-add-role-to-user/add-assignment.png)

1. Sélectionnez **Ajouter des affectations**.

    ![Quand un rôle prend en charge l’étendue, vous pouvez sélectionner une étendue](./media/pim-how-to-add-role-to-user/add-scope.png)

1. Sur la page **Ajouter des affectations**, vous pouvez :

   - Sélectionner un utilisateur ou un groupe à affecter au rôle
   - Sélectionner l’étendue du rôle (dans ce cas, unités administratives)
   - Sélectionner une unité administrative pour l’étendue

Pour plus d’informations sur la création d’unités administratives, voir [Ajouter et supprimer des unités administratives](../roles/admin-units-manage.md).

## <a name="assign-a-role-using-graph-api"></a>Attribuer un rôle à l’aide d’API Graph

Pour obtenir les autorisations requises pour utiliser l’API PIM, consultez [Comprendre les API de la Gestion de l’identité managée](pim-apis.md). 

### <a name="eligible-with-no-end-date"></a>Éligible sans date de fin

Voici un exemple de requête HTTP pour créer une attribution éligible sans date de fin. Pour plus d’informations sur les commandes d’API, y compris des exemples tels que C# et JavaScript, consultez [Créer unifiedRoleEligibilityScheduleRequest](/graph/api/unifiedroleeligibilityschedulerequest-post-unifiedroleeligibilityschedulerequests?view=graph-rest-beta&tabs=http&preserve-view=true).

#### <a name="http-request"></a>Demande HTTP

````HTTP
POST https://graph.microsoft.com/beta/rolemanagement/directory/roleEligibilityScheduleRequests 

    "action": "AdminAssign", 
    "justification": "abcde", 
    "directoryScopeId": "/", 
    "principalId": "<principal-ID-GUID>", 
    "roleDefinitionId": "<definition-ID-GUID>", 
    "scheduleInfo": { 
        "startDateTime": "2021-07-15T19:15:08.941Z", 
        "expiration": { 
            "type": "NoExpiration"        } 
    } 
{ 
} 
````

#### <a name="http-response"></a>Réponse HTTP

Voici un exemple de réponse. L’objet de réponse illustré ici peut être abrégé pour une meilleure lisibilité.

````HTTP
{ 
    "@odata.context": "https://graph.microsoft.com/beta/$metadata#roleManagement/directory/roleEligibilityScheduleRequests/$entity", 
    "id": "<schedule-ID-GUID>", 
    "status": "Provisioned", 
    "createdDateTime": "2021-07-15T19:47:41.0939004Z", 
    "completedDateTime": "2021-07-15T19:47:42.4376681Z", 
    "approvalId": null, 
    "customData": null, 
    "action": "AdminAssign", 
    "principalId": "<principal-ID-GUID>", 
    "roleDefinitionId": "<definition-ID-GUID>", 
    "directoryScopeId": "/", 
    "appScopeId": null, 
    "isValidationOnly": false, 
    "targetScheduleId": "<schedule-ID-GUID>", 
    "justification": "test", 
    "createdBy": { 
        "application": null, 
        "device": null, 
        "user": { 
            "displayName": null, 
            "id": "<user-ID-GUID>" 
        } 
    }, 
    "scheduleInfo": { 
        "startDateTime": "2021-07-15T19:47:42.4376681Z", 
        "recurrence": null, 
        "expiration": { 
            "type": "noExpiration", 
            "endDateTime": null, 
            "duration": null 
        } 
    }, 
    "ticketInfo": { 
        "ticketNumber": null, 
        "ticketSystem": null 
    } 
}   
````

### <a name="active-and-time-bound"></a>Actif et lié au temps

Voici un exemple de requête HTTP pour créer une attribution active qui est liée au temps. Pour plus d’informations sur les commandes d’API, y compris des exemples tels que C# et JavaScript, consultez [Créer unifiedRoleEligibilityScheduleRequest](/graph/api/unifiedroleeligibilityschedulerequest-post-unifiedroleeligibilityschedulerequests?view=graph-rest-beta&tabs=http&preserve-view=true).

#### <a name="http-request"></a>Demande HTTP

````HTTP
POST https://graph.microsoft.com/beta/roleManagement/directory/roleAssignmentScheduleRequests 

{ 
    "action": "AdminAssign", 
    "justification": "abcde", 
    "directoryScopeId": "/", 
    "principalId": "<principal-ID-GUID>", 
    "roleDefinitionId": "<definition-ID-GUID>", 
    "scheduleInfo": { 
        "startDateTime": "2021-07-15T19:15:08.941Z", 
        "expiration": { 
            "type": "AfterDuration", 
            "duration": "PT3H" 
        } 
    } 
} 
````

#### <a name="http-response"></a>Réponse HTTP

Voici un exemple de réponse. L’objet de réponse illustré ici peut être abrégé pour une meilleure lisibilité.

````HTTP
{ 
    "@odata.context": "https://graph.microsoft.com/beta/$metadata#roleManagement/directory/roleAssignmentScheduleRequests/$entity", 
    "id": "<schedule-ID-GUID>", 
    "status": "Provisioned", 
    "createdDateTime": "2021-07-15T19:15:09.7093491Z", 
    "completedDateTime": "2021-07-15T19:15:11.4437343Z", 
    "approvalId": null, 
    "customData": null, 
    "action": "AdminAssign", 
    "principalId": "<principal-ID-GUID>", 
    "roleDefinitionId": "<definition-ID-GUID>", 
    "directoryScopeId": "/", 
    "appScopeId": null, 
    "isValidationOnly": false, 
    "targetScheduleId": "<schedule-ID-GUID>", 
    "justification": "test", 
    "createdBy": { 
        "application": null, 
        "device": null, 
        "user": { 
            "displayName": null, 
            "id": "<user-ID-GUID>" 
        } 
    }, 
    "scheduleInfo": { 
        "startDateTime": "2021-07-15T19:15:11.4437343Z", 
        "recurrence": null, 
        "expiration": { 
            "type": "afterDuration", 
            "endDateTime": null, 
            "duration": "PT3H" 
        } 
    }, 
    "ticketInfo": { 
        "ticketNumber": null, 
        "ticketSystem": null 
    } 
} 
````

## <a name="update-or-remove-an-existing-role-assignment"></a>Mettre à jour ou supprimer une attribution de rôle existante

Suivez ces étapes pour mettre à jour ou supprimer une attribution de rôle existante. **Clients titulaires d’une licence Azure AD P2 uniquement :** n’affectez pas un groupe actif à un rôle à la fois par le biais d’Azure AD et de Privileged Identity Management (PIM). Pour une explication détaillée, consultez [Problèmes connus](../roles/groups-concept.md#known-issues).

1. Ouvrez **Azure AD Privileged Identity Management**.

1. Sélectionnez des **rôles Azure AD**.

1. Sélectionnez **Rôles** pour afficher la liste des rôles pour Azure AD.

1. Sélectionnez le rôle que vous souhaitez mettre à jour ou supprimer.

1. Recherchez l’attribution de rôle sous les onglets **Rôles éligibles** et **Rôles actifs**.

    ![Mettre à jour ou supprimer une attribution de rôle](./media/pim-how-to-add-role-to-user/remove-update-assignments.png)

1. Sélectionnez **Mettre à jour** ou **Supprimer** pour mettre à jour ou supprimer l’attribution de rôle.

## <a name="remove-eligible-assignment-via-api"></a>Supprimer l’attribution éligible via l’API

### <a name="request"></a>Requête

````HTTP
POST https://graph.microsoft.com/beta/roleManagement/directory/roleEligibilityScheduleRequests 

 

{ 
    "action": "AdminRemove", 
    "justification": "abcde", 
    "directoryScopeId": "/", 
    "principalId": "d96ea738-3b95-4ae7-9e19-78a083066d5b", 
    "roleDefinitionId": "88d8e3e3-8f55-4a1e-953a-9b9898b8876b" 
} 
````

### <a name="response"></a>response

````HTTP
{ 
    "@odata.context": "https://graph.microsoft.com/beta/$metadata#roleManagement/directory/roleEligibilityScheduleRequests/$entity", 
    "id": "fc7bb2ca-b505-4ca7-ad2a-576d152633de", 
    "status": "Revoked", 
    "createdDateTime": "2021-07-15T20:23:23.85453Z", 
    "completedDateTime": null, 
    "approvalId": null, 
    "customData": null, 
    "action": "AdminRemove", 
    "principalId": "d96ea738-3b95-4ae7-9e19-78a083066d5b", 
    "roleDefinitionId": "88d8e3e3-8f55-4a1e-953a-9b9898b8876b", 
    "directoryScopeId": "/", 
    "appScopeId": null, 
    "isValidationOnly": false, 
    "targetScheduleId": null, 
    "justification": "test", 
    "scheduleInfo": null, 
    "createdBy": { 
        "application": null, 
        "device": null, 
        "user": { 
            "displayName": null, 
            "id": "5d851eeb-b593-4d43-a78d-c8bd2f5144d2" 
        } 
    }, 
    "ticketInfo": { 
        "ticketNumber": null, 
        "ticketSystem": null 
    } 
} 
````

## <a name="next-steps"></a>Étapes suivantes

- [Configurer les paramètres des rôles d’administrateur Azure AD dans Privileged Identity Management](pim-how-to-change-default-settings.md)
- [Attribuer des rôles de ressources Azure dans Privileged Identity Management](pim-resource-roles-assign-roles.md)
