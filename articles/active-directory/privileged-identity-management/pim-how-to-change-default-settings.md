---
title: Configurer les paramètres des rôles Azure AD dans PIM - Azure AD | Microsoft Docs
description: Découvrez comment configurer les paramètres des rôles Azure AD dans Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: KarenH444
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 11/12/2021
ms.author: curtand
ms.reviewer: shaunliu
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5dc783125641ef71d1df974bc7d8cb01c488329a
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132484733"
---
# <a name="configure-azure-ad-role-settings-in-privileged-identity-management"></a>Configurer les paramètres des rôles Azure AD dans Privileged Identity Management

Un administrateur de rôle privilégié peut personnaliser Privileged Identity Management (PIM) dans son organisation Azure Active Directory (Azure AD), notamment modifier l’expérience d’un utilisateur qui active une attribution de rôle admissible. Pour plus d’informations sur les événements PIM qui déclenchent des notifications et sur les administrateurs qui les reçoivent, consultez [Notifications par e-mail dans Privileged Identity Management](pim-email-notifications.md#notifications-for-azure-ad-roles)

## <a name="open-role-settings"></a>Ouvrir les paramètres de rôle

Suivez ces étapes pour ouvrir les paramètres d’un rôle Azure AD.

1. Connectez-vous au [portail Azure](https://portal.azure.com/) à l’aide d’un utilisateur avec le rôle [Administrateur de rôle privilégié](../roles/permissions-reference.md#privileged-role-administrator).

1. Ouvrez **Azure AD Privileged Identity Management** &gt; **Rôles Azure AD** &gt; **Paramètres du rôle**.

    ![Page Paramètres de rôle répertoriant les rôles Azure AD](./media/pim-how-to-change-default-settings/role-settings.png)

1. Sélectionnez le rôle dont vous souhaitez configurer les paramètres.

    ![Page Détails des paramètres de rôle répertoriant plusieurs paramètres d’affectation et d’activation](./media/pim-how-to-change-default-settings/role-settings-page.png)

1. Sélectionnez **Modifier** pour ouvrir la page Paramètres de rôle.

    ![Modifier la page des paramètres de rôle avec des options permettant de mettre à jour les paramètres d’affectation et d’activation](./media/pim-how-to-change-default-settings/role-settings-edit.png)

    Dans le volet Paramètres de rôle de chaque rôle, vous pouvez configurer plusieurs paramètres.

## <a name="assignment-duration"></a>Durée de l’attribution

Vous pouvez choisir entre deux options de durée d’attribution pour chaque type d’attribution (éligible et actif) lorsque vous configurez les paramètres d’un rôle. Ces options deviennent la durée maximale par défaut lorsqu’un utilisateur est attribué au rôle dans Privileged Identity Management.

Vous pouvez choisir l’une de ces options de durée d’attribution **éligible** :

| Paramètre | Description |
| --- | --- |
| Autoriser une attribution éligible permanente | Les Administrateurs généraux et les Administrateurs de rôle privilégié peuvent assigner une attribution éligible permanente. |
| Faire expirer les attributions éligibles après | Les Administrateurs généraux et les Administrateurs de rôle privilégié peuvent exiger que toutes les attributions éligibles aient une date de début et une date de fin spécifiées. |

Vous pouvez choisir l’une de ces options de durée d’attribution **active** :

| Paramètre | Description |
| --- | --- |
| Autoriser une attribution active permanente | Les Administrateurs généraux et les Administrateurs de rôle privilégié peuvent assigner une attribution active permanente. |
| Faire expirer les attributions actives après | Les Administrateurs généraux et les Administrateurs de rôle privilégié peuvent exiger que toutes les attributions actives aient une date de début et une date de fin spécifiées. |

> [!NOTE]
> Toutes les attributions qui ont une date de fin spécifiée peuvent être renouvelées par les Administrateurs généraux et les Administrateurs de rôle privilégié. De plus, les utilisateurs peuvent lancer des demandes en libre-service afin d’[étendre ou renouveler des attributions de rôles](pim-resource-roles-renew-extend.md).

## <a name="require-multifactor-authentication"></a>Exiger l’authentification multifacteur

Privileged Identity Management permet l’application d’Azure AD Multifactor Authentication lors de l’activation et de l’attribution active.

### <a name="on-activation"></a>Lors de l’activation

Vous pouvez exiger des utilisateurs éligibles à un rôle qu'ils s'authentifient à l'aide d'Azure AD Multi-Factor Authentication pour effectuer l'activation. L’authentification multifacteur garantit, avec une certitude raisonnable, que l’utilisateur est bien celui qu’il prétend être. L’application de cette option permet de protéger les ressources critiques au cas où le compte d’utilisateur pourrait être compromis.

Pour exiger une authentification multifacteur afin d’activer l’attribution de rôle, sélectionnez l’option **Lors de l’activation, exiger Azure Multifactor Authentication** dans l’onglet Activation de **Modifier le paramètre du rôle**.

### <a name="on-active-assignment"></a>Lors de l’attribution active

Avec cette option, les administrateurs doivent effectuer une authentification multifacteur avant de créer une attribution de rôle active (par opposition à éligible). La Gestion de l’identité privilégiée ne peut pas appliquer l’authentification multifacteur lorsque l’utilisateur utilise son attribution de rôle, car il est déjà actif dans le rôle depuis l’attribution.

Pour exiger une authentification multifacteur lorsque l’attribution est active, sélectionnez l’option **Exiger l’Authentification multifacteur Azure lors de l’attribution active** sous l’onglet Attribution de l’option **Modifier le paramètre du rôle**.

Pour plus d’informations, consultez [Authentification multifacteur et Privileged Identity Management](pim-how-to-require-mfa.md).

## <a name="activation-maximum-duration"></a>Durée maximum d’activation

Utilisez le curseur **Durée maximale d’activation** pour définir la durée maximale, en heures, pendant laquelle une requête d’activation pour une attribution de rôle reste active avant d’expirer. Cette valeur peut être comprise entre 1 et 24 heures.

## <a name="require-justification"></a>Demander une justification

Vous pouvez exiger que les utilisateurs saisissent une justification métier lorsqu’ils s’activent. Pour demander une justification, cochez la case **Demander une justification lors de l'affectation active** ou la case **Demander une justification lors de l’activation**.

## <a name="require-approval-to-activate"></a>Demander une approbation pour activation

Si vous définissez plusieurs approbateurs, l’approbation est accomplie dès que l’un d’eux approuve ou refuse. Vous ne pouvez pas forcer l’approbation d’un deuxième ou d’un approbateur suivant. Pour exiger l’approbation pour activer un rôle, procédez comme suit.

1. Cochez la case **Exiger une approbation pour activer**.

1. Sélectionnez **Sélectionner des approbateurs**.

    ![Sélectionner un volet d’utilisateur ou de groupe pour sélectionner les approbateurs](./media/pim-resource-roles-configure-role-settings/resources-role-settings-select-approvers.png)

1. Sélectionnez au moins un utilisateur, puis cliquez sur **Sélectionner**. Sélectionnez au moins un approbateur. Si aucun approbateur spécifique n’est sélectionné, les administrateurs à rôle privilégié/administrateurs généraux deviendront les approbateurs par défaut.

1. Sélectionnez **Mettre à jour** pour enregistrer vos modifications.

## <a name="next-steps"></a>Étapes suivantes

- [Attribuer des rôles dans Azure AD dans Privileged Identity Management](pim-how-to-add-role-to-user.md)
- [Configurer les alertes de sécurité pour les rôles Azure AD dans Privileged Identity Management](pim-how-to-configure-security-alerts.md)
