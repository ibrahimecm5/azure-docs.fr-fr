---
title: Renouveler des attributions de rôles de ressources Azure dans PIM - Azure AD | Microsoft Docs
description: Découvrez comment étendre ou renouveler des affectations de rôles de ressources Azure dans Azure AD Privileged Identity Management (PIM).
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
ms.openlocfilehash: 87b8fb2eb7f6301762fcba9931dfd9da6c54b679
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130253673"
---
# <a name="extend-or-renew-azure-resource-role-assignments-in-privileged-identity-management"></a>Étendre ou renouveler des attributions de rôle de ressources Azure dans Privileged Identity Management

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) propose des contrôles permettant de gérer le cycle de vie d’accès et d’attribution des ressources Azure. Les administrateurs peuvent attribuer des rôles à l’aide de propriétés de date et d’heure de début et de fin. À l’approche de la fin de l’attribution, Privileged Identity Management envoie des notifications par e-mail aux utilisateurs ou aux groupes concernés ainsi qu’aux administrateurs de la ressource pour garantir le maintien de l’accès approprié. Les attributions peuvent être renouvelées et rester visibles à l’état expiré pendant 30 jours, même si l’accès n’est pas étendu.

## <a name="who-can-extend-and-renew"></a>Qui peut étendre et renouveler ?

Seuls les administrateurs de la ressource peuvent étendre ou renouveler des attributions de rôles. L’utilisateur ou le groupe concerné peut demander l’extension de rôles arrivant à expiration et le renouvellement de rôles ayant déjà expiré.

## <a name="when-are-notifications-sent"></a>Quand des notifications sont-elles envoyées ?

Privileged Identity Management envoie des notifications par e-mail aux administrateurs et aux utilisateurs ou groupes affectés des rôles arrivant à expiration dans les 14 jours et un jour avant l’expiration. Un autre e-mail est envoyé lorsqu’une attribution expire officiellement.

Les administrateurs reçoivent des notifications lorsqu’un utilisateur ou groupe affecté à un rôle arrivant à expiration ou ayant expiré demande une extension ou un renouvellement. Lorsqu’un administrateur traite la demande, tous les autres administrateurs sont informés de la décision de résolution (approuvée ou refusée). Celle-ci est ensuite communiquée à l’utilisateur ou au groupe à l’origine de la demande.

## <a name="extend-role-assignments"></a>Étendre des attributions de rôles

Les étapes suivantes décrivent la procédure de demande, de résolution et d’administration de l’extension ou du renouvellement d’une attribution de rôle.

### <a name="self-extend-expiring-assignments"></a>Étendre automatiquement les attributions arrivant à expiration

Les utilisateurs affectés à un rôle peuvent prolonger des attributions de rôles arrivant à expiration directement sous l’onglet **Éligible** ou **Actif** de la page **Mes rôles** d’une ressource et depuis la page de premier niveau **Mes rôles** du portail Privileged Identity Management. Dans le portail, les utilisateurs peuvent demander de prolonger des rôles éligibles ou actifs (attribués) qui expirent au cours des 14 prochains jours.

![Ressources Azure : page Mes rôles répertoriant les rôles éligibles avec une colonne d’action](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-ui.png)

Lorsque la date/heure de fin de l’attribution est dans les 14 jours, le lien vers **Étendre** devient actif dans le portail Azure. Dans l’exemple ci-dessous, supposons que la date actuelle est le 27 mars.

>[!Note]
>Pour un groupe affecté à un rôle, le lien **Étendre** n’est jamais disponible afin qu’un utilisateur avec une affectation héritée ne puisse pas étendre l’affectation du groupe.

![Colonne d’action avec des liens d’activation et d’extension](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-within-14.png)

Pour demander une extension de cette attribution de rôle, sélectionnez **Étendre** afin d’ouvrir le formulaire de demande.

![Étendre le volet d’attribution des rôles avec une zone Raison](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-role-assignment-request.png)

Pour consulter les informations relatives à l’attribution d’origine, développez **Détails de l’attribution**. Entrez le motif de la demande d’extension, puis sélectionnez **Étendre**.

>[!NOTE]
>Nous vous recommandons de préciser en détail la raison pour laquelle l’extension est nécessaire, ainsi que la durée de l’extension demandée (si vous la connaissez).

![Étendre le volet d’attribution des rôles avec une page Détails de l’attribution étendue](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-form-complete.png)

Quelques instants après, les administrateurs de ressources reçoivent une notification par e-mail leur demandant d’examiner la demande d’extension. Si une demande d’extension a déjà été envoyée, une notification Azure apparaît dans le portail.

![Notification expliquant qu’une extension d’attribution de rôle en cours existe déjà](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-failed-existing-request.png)

Accédez à la page **Demandes en attente** pour connaître l’état de votre demande ou l’annuler.

![Ressources Azure : page Demandes en attente répertoriant toutes les demandes en attente et présentant un lien d’annulation](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-cancel-request.png)

### <a name="admin-approved-extension"></a>Extensions approuvées par l’administrateur

Lorsqu’un utilisateur ou groupe envoie une demande d’extension d’une attribution de rôle, les administrateurs de ressources reçoivent une notification par e-mail contenant les détails de l’attribution d’origine et le motif de la demande. La notification inclut un lien direct vers la requête pour que l’administrateur puisse l’approuver ou la refuser.

Pour approuver ou rejeter les demandes, les administrateurs peuvent suivre le lien contenu dans l’e-mail ou accéder au portail d’administration de Privileged Identity Management et sélectionner **Approuver les demandes** dans le menu de gauche.

![Ressources Azure : page Approuver les demandes répertoriant les requêtes et les liens en attente d’approbation ou de refus](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-grid.png)

Lorsqu’un administrateur sélectionne **Approuver** ou **Rejeter**, les détails de la demande s’affichent, ainsi qu’un champ permettant de donner une justification professionnelle pour les journaux d’audit.

![Demande d’approbation d’attribution de rôle avec la raison du demandeur, le type d’attribution, l’heure de début, l’heure de fin et la raison](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-blade.png)

Lorsqu’ils approuvent une demande d’extension d’attribution de rôle, les administrateurs de ressources peuvent choisir de nouvelles dates de début et de fin et un nouveau type d’attribution. Une modification du type d’attribution peut être nécessaire s’ils souhaitent accorder un accès limité afin d’effectuer une tâche spécifique (un jour, par exemple). Dans cet exemple, l’administrateur peut modifier l’attribution de **Éligible** à **Actif**, ce qui signifie qu’il peut donner l’accès au demandeur sans l’obliger à s’activer.

### <a name="admin-initiated-extension"></a>Extension initiée par l’administrateur

Si un utilisateur affecté à un rôle ne demande pas d’extension pour une attribution de rôle, un administrateur peut étendre celle-ci au nom de l’utilisateur. Les extensions administratives d’attribution de rôle ne nécessitent pas d’approbation, mais des notifications sont envoyées à tous les autres administrateurs une fois le rôle étendu.

Pour étendre une attribution de rôle, accédez à la vue de l’attribution ou du rôle de ressource dans Privileged Identity Management. Trouvez l’attribution qui a besoin d’une extension. Ensuite, sélectionnez **Étendre** dans la colonne d’action.

![Ressources Azure : page Attributions répertoriant les rôles éligibles avec des liens d’extension](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-extend.png)

## <a name="renew-role-assignments"></a>Renouveler des attributions de rôles

Bien que semblable d’un point de vue conceptuel au processus de demande d’extension, le processus de renouvellement d’une attribution de rôle arrivée à expiration est différent. Les étapes suivantes permettent aux attributions et aux administrateurs de renouveler si nécessaire l’accès à des rôles ayant expiré.

### <a name="self-renew"></a>Renouvellement autonome

Les utilisateurs qui n’ont plus accès aux ressources peuvent accéder à 30 jours d’historique d’attributions arrivées à expiration. Pour cela, ils accèdent à **Mes rôles** dans le volet de gauche, puis sélectionnent l’onglet **Rôles arrivés à expiration** dans la section Rôles des ressources Azure.

![Page Mes rôles : onglets Rôles arrivés à expiration](media/pim-resource-roles-renew-extend/aadpim-rbac-renew-from-myroles.png)

La liste des rôles affiche par défaut les **Rôles éligibles**. Utilisez le menu déroulant pour basculer entre les rôles attribués éligibles et actifs.

Pour demander le renouvellement d’une des attributions de rôles de la liste, sélectionnez l’action **Renouveler**. Ensuite, précisez le motif de la demande. Il est utile d’indiquer une durée en plus d’un contexte supplémentaire ou d’une justification professionnelle pour aider l’administrateur de la ressource à l’approuver ou la rejeter.

![Volet Renouveler une attribution de rôle montrant la zone Raison](media/pim-resource-roles-renew-extend/aadpim-rbac-renew-request-form.png)

Une fois que la demande de renouvellement d’une attribution de rôle a été soumise, les administrateurs de ressources sont informés de la présence d’une demande en attente.

### <a name="admin-approves"></a>Approbations d’administrateur

Les administrateurs de ressources peuvent accéder à la demande de renouvellement à partir du lien contenu dans la notification par e-mail ou en accédant à Privileged Identity Management sur le Portail Azure et en sélectionnant **Approuver les demandes** dans le menu de gauche.

![Ressources Azure : page Approuver les demandes répertoriant les requêtes et les liens en attente d’approbation ou de refus](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-grid.png)

Lorsqu’un administrateur sélectionne **Approuver** ou **Rejeter**, les détails de la demande s’affichent, ainsi qu’un champ permettant de donner une justification professionnelle pour les journaux d’audit.

![Demande d’approbation d’attribution de rôle avec la raison du demandeur, le type d’attribution, l’heure de début, l’heure de fin et la raison](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-blade.png)

Lorsqu’ils approuvent une demande de renouvellement d’attribution de rôle, les administrateurs de ressources doivent entrer de nouvelles dates de début et de fin et un nouveau type d’attribution.

### <a name="admin-renew"></a>Renouvellement d’administrateur

Les administrateurs de ressources peuvent renouveler les attributions de rôles ayant expiré dans l’onglet **Membres** du menu de navigation de gauche d’une ressource, ou dans l’onglet **Expiré** des rôles d’une ressource.

Pour voir la liste de toutes les attributions de rôles ayant expiré, sélectionnez **Rôles ayant expiré** sur l’écran **Membres**.

![Ressources Azure : page Membres répertoriant les rôles expirés avec des liens de renouvellement](media/pim-resource-roles-renew-extend/aadpim-rbac-renew-from-member-blade.png)

## <a name="next-steps"></a>Étapes suivantes

- [Approuver ou rejeter des demandes de rôles de ressources Azure dans Privileged Identity Management](pim-resource-roles-approval-workflow.md)
- [Configurer les paramètres des rôles de ressources Azure dans Privileged Identity Management](pim-resource-roles-configure-role-settings.md)
