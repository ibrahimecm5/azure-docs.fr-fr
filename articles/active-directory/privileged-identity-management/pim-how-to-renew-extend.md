---
title: Renouveler des affectations de rôles Azure AD dans PIM - Azure Active Directory | Microsoft Docs
description: Découvrez comment étendre ou renouveler des affectations de rôles Azure Active Directory dans Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: KarenH444
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: pim
ms.date: 10/19/2021
ms.author: curtand
ms.reviewer: shaunliu
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 51385ce98da9e163be0d70d0c0662f51094094a3
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130222511"
---
# <a name="extend-or-renew-azure-ad-role-assignments-in-privileged-identity-management"></a>Étendre ou renouveler des attributions de rôle Azure AD dans Privileged Identity Management

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) propose des contrôles permettant de gérer le cycle de vie d’accès et d’attribution des rôles Azure AD. Les administrateurs peuvent attribuer des rôles à l’aide de propriétés de date et d’heure de début et de fin. À l’approche de la fin de l’attribution, Privileged Identity Management envoie des notifications par e-mail aux utilisateurs ou aux groupes concernés ainsi qu’aux administrateurs Azure AD pour garantir le maintien de l’accès approprié. Les attributions peuvent être renouvelées et rester visibles à l’état expiré pendant 30 jours, même si l’accès n’est pas étendu.

## <a name="who-can-extend-and-renew"></a>Qui peut étendre et renouveler ?

Seuls les administrateurs généraux ou les administrateurs de rôles privilégiés peuvent étendre ou renouveler les attributions de rôles Azure AD. L’utilisateur ou le groupe concerné peut demander l’extension de rôles arrivant à expiration et le renouvellement de rôles ayant déjà expiré.

## <a name="when-are-notifications-sent"></a>Quand des notifications sont-elles envoyées ?

Privileged Identity Management envoie des notifications par e-mail aux administrateurs et aux utilisateurs ou groupes affectés des rôles arrivant à expiration dans les 14 jours et un jour avant l’expiration. Un autre e-mail est envoyé lorsqu’une attribution expire officiellement.

Les administrateurs reçoivent des notifications lorsqu’un utilisateur ou groupe affecté à un rôle arrivant à expiration ou ayant expiré demande une extension ou un renouvellement. Lorsqu’un administrateur résout une requête approuvée ou refusée, tous les autres administrateurs sont informés de la décision. Celle-ci est ensuite communiquée à l’utilisateur ou au groupe à l’origine de la demande.

## <a name="extend-role-assignments"></a>Étendre des attributions de rôles

Les étapes suivantes décrivent la procédure de demande, de résolution et d’administration de l’extension ou du renouvellement d’une attribution de rôle.

### <a name="self-extend-expiring-assignments"></a>Étendre automatiquement les attributions arrivant à expiration

Les utilisateurs affectés à un rôle peuvent prolonger des attributions de rôles arrivant à expiration directement sous l’onglet **Éligible** ou **Actif** de la page **Mes rôles** soit sous **Rôles Azure AD** ou depuis le niveau supérieur de la page **Mes rôles** du portail Privileged Identity Management. Dans le portail, les utilisateurs peuvent demander de prolonger des rôles éligibles ou actifs (attribués) qui expirent au cours des 14 prochains jours.

![Rôles Azure AD : page Mes rôles répertoriant les rôles éligibles avec une colonne d’action](./media/pim-how-to-renew-extend/pim-extend-link-in-portal.png)

Lorsque la date/heure de fin de l’attribution se situe 14 jours plus tard ou moins, le bouton **Étendre** devient un lien actif dans l’interface utilisateur. Dans l’exemple ci-dessous, supposons que la date actuelle est le 27 mars.

>[!Note]
>Pour un groupe affecté à un rôle, le lien **Étendre** n’est jamais disponible afin qu’un utilisateur avec une affectation héritée ne puisse pas étendre l’affectation du groupe.

![Colonne d’action avec des liens d’activation et d’extension](./media/pim-how-to-renew-extend/pim-extend-within-fourteen.png)

Pour demander une extension de cette attribution de rôle, sélectionnez **Étendre** afin d’ouvrir le formulaire de demande.

![Étendre le volet d’attribution des rôles avec une zone Raison](./media/pim-how-to-renew-extend/extend-role-assignment-request.png)

Entrez le motif de la demande d’extension, puis sélectionnez **Étendre**.

>[!NOTE]
>Nous vous recommandons de préciser en détail la raison pour laquelle l’extension est nécessaire, ainsi que la durée de l’extension demandée (si vous la connaissez).

Les administrateurs reçoivent une notification par courrier électronique pour passer en revue la demande d’extension. Si une demande d’extension a déjà été envoyée, une notification Azure apparaît dans le portail.

![Notification expliquant qu’une extension d’attribution de rôle en cours existe déjà](./media/pim-how-to-renew-extend/extend-notification.png)

Accédez à la page **Demandes en attente** pour connaître l’état de votre demande ou l’annuler.

![Rôles Azure AD : page Demandes en attente répertoriant toutes les demandes en attente et présentant un lien d’annulation](./media/pim-how-to-renew-extend/pending-requests.png)

### <a name="admin-approved-extension"></a>Extensions approuvées par l’administrateur

Quand un utilisateur ou un groupe envoie une demande d’extension d’une affectation de rôle, les administrateurs reçoivent une notification par e-mail contenant les détails de l’affectation d’origine et le motif de la demande. La notification inclut un lien direct vers la requête pour que l’administrateur puisse l’approuver ou la refuser.

Pour approuver ou rejeter les demandes, les administrateurs peuvent suivre le lien contenu dans l’e-mail ou accéder au portail d’administration de Privileged Identity Management et sélectionner **Approuver les demandes** dans le menu de gauche.

![Rôles Azure AD : page Approuver les demandes répertoriant les requêtes et les liens en attente d’approbation ou de refus](./media/pim-how-to-renew-extend/extend-admin-approve-list.png)

Lorsqu’un administrateur sélectionne **Approuver** ou **Rejeter**, les détails de la demande s’affichent, ainsi qu’un champ permettant de donner une justification professionnelle pour les journaux d’audit.

![Demande d’approbation d’attribution de rôle avec la raison du demandeur, le type d’attribution, l’heure de début, l’heure de fin et la raison](./media/pim-how-to-renew-extend/extend-admin-approve-form.png)

Lorsqu’ils approuvent une demande d’extension d’attribution de rôle, les administrateurs peuvent choisir de nouvelles dates de début et de fin et un nouveau type d’attribution. Une modification du type d’attribution peut être nécessaire s’ils souhaitent accorder un accès limité afin d’effectuer une tâche spécifique (un jour, par exemple). Dans cet exemple, l’administrateur peut modifier l’attribution de **Éligible** à **Actif**, ce qui signifie qu’il peut donner l’accès au demandeur sans l’obliger à s’activer.

### <a name="admin-initiated-extension"></a>Extension initiée par l’administrateur

Si un utilisateur affecté à un rôle ne demande pas d’extension pour une attribution de rôle, un administrateur peut étendre celle-ci au nom de l’utilisateur. Les extensions administratives d’attribution de rôle ne nécessitent pas d’approbation, mais des notifications sont envoyées à tous les autres administrateurs une fois le rôle étendu.

Pour étendre une attribution de rôle, accédez à la vue de l’attribution ou du rôle dans Privileged Identity Management. Trouvez l’attribution qui a besoin d’une extension. Ensuite, sélectionnez **Étendre** dans la colonne d’action.

![Rôles Azure AD : page Attributions répertoriant les rôles éligibles avec des liens d’extension](./media/pim-how-to-renew-extend/extend-admin-extend.png)

## <a name="extend-role-assignments-using-graph-api"></a>Étendre les attributions de rôles à l’aide de l’API Graph

Étendez une attribution active à l’aide de l’API Graph.

#### <a name="http-request"></a>Demande HTTP

````HTTP
POST https://graph.microsoft.com/beta/roleManagement/directory/roleAssignmentScheduleRequests 
 
{ 
    "action": "AdminExtend", 
    "justification": "abcde", 
    "roleDefinitionId": "<definition-ID-GUID>", 
    "directoryScopeId": "/", 
    `"principalId": "<principal-ID-GUID>", 
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

````HTTP
{ 
    "@odata.context": "https://graph.microsoft.com/beta/$metadata#roleManagement/directory/roleAssignmentScheduleRequests/$entity", 
    "id": "<assignment-ID-GUID>", 
    "status": "Provisioned", 
    "createdDateTime": "2021-07-15T20:26:44.865248Z", 
    "completedDateTime": "2021-07-15T20:26:47.9434068Z", 
    "approvalId": null, 
    "customData": null, 
    "action": "AdminExtend", 
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
        "startDateTime": "2021-07-15T20:26:47.9434068Z", 
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

## <a name="renew-role-assignments"></a>Renouveler des attributions de rôles

Bien que semblable d’un point de vue conceptuel au processus de demande d’extension, le processus de renouvellement d’une attribution de rôle arrivée à expiration est différent. Les étapes suivantes permettent aux attributions et aux administrateurs de renouveler si nécessaire l’accès à des rôles ayant expiré.

### <a name="self-renew"></a>Renouvellement autonome

Les utilisateurs qui n’ont plus accès aux ressources peuvent accéder à 30 jours d’historique d’attributions arrivées à expiration. Pour cela, ils accèdent à **Mes rôles** dans le volet de gauche, puis sélectionnent l’onglet **Rôles arrivés à expiration** dans la section Rôles Azure AD.

![Page Mes rôles : onglets Rôles arrivés à expiration](./media/pim-how-to-renew-extend/renew-from-myroles.png)

La liste des rôles affiche par défaut les **Rôles éligibles**. Sélectionnez des rôles affectés **Éligibles** ou **Actifs**.

Pour demander le renouvellement d’une des attributions de rôles de la liste, sélectionnez l’action **Renouveler**. Ensuite, précisez le motif de la demande. Il est utile d’indiquer une durée en plus d’un contexte supplémentaire ou d’une justification professionnelle pour aider l’administrateur à décider de son approbation ou de son rejet.

![Volet Renouveler une attribution de rôle montrant la zone Raison](./media/pim-how-to-renew-extend/renew-request-form.png)

Une fois que la demande de renouvellement d’une attribution de rôle a été soumise, les administrateurs sont informés de la présence d’une demande en attente.

### <a name="admin-approves"></a>Approbations d’administrateur

Les administrateurs Azure AD peuvent accéder à la demande de renouvellement à partir du lien contenu dans la notification par e-mail ou en accédant à Privileged Identity Management sur le Portail Azure et en sélectionnant **Approuver les demandes** dans PIM.

![Rôles Azure AD : page Approuver les demandes répertoriant les requêtes et les liens en attente d’approbation ou de refus](./media/pim-how-to-renew-extend/extend-admin-approve-list.png)

Lorsqu’un administrateur sélectionne **Approuver** ou **Rejeter**, les détails de la demande s’affichent, ainsi qu’un champ permettant de donner une justification professionnelle pour les journaux d’audit.

![Demande d’approbation d’attribution de rôle avec la raison du demandeur, le type d’attribution, l’heure de début, l’heure de fin et la raison](./media/pim-how-to-renew-extend/extend-admin-approve-form.png)

Lorsqu’ils approuvent une demande de renouvellement d’attribution de rôle, les administrateurs doivent entrer de nouvelles dates de début et de fin et un nouveau type d’attribution.

### <a name="admin-renew"></a>Renouvellement d’administrateur

Ils peuvent également renouveler des affectations de rôles expirées à partir de l’onglet des rôles **Expirés** d’un rôle Azure AD. Pour voir la liste de toutes les attributions de rôles ayant expiré, sélectionnez **Rôles ayant expiré** sur l’écran **Attributions**.

![Rôles Azure AD : page Attributions répertoriant les rôles expirés avec des liens de renouvellement](./media/pim-how-to-renew-extend/renew-from-assignments-pane.png)

## <a name="next-steps"></a>Étapes suivantes

- [Approuver ou rejeter des requêtes de rôles Azure AD dans Privileged Identity Management](azure-ad-pim-approval-workflow.md)
- [Configurer les paramètres des rôles Azure AD dans Privileged Identity Management](pim-how-to-change-default-settings.md)
