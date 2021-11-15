---
title: Valider des règles d’appartenance de groupe dynamique (préversion) - Azure AD | Microsoft Docs
description: Guide pratique pour tester des membres par rapport à une règle d’appartenance pour un groupe dynamique dans Azure Active Directory.
services: active-directory
documentationcenter: ''
author: curtand
manager: KarenH444
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
ms.date: 09/02/2021
ms.author: curtand
ms.reviewer: yukarppa
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9b12c1c69f160a51fbcf5650a3ebe30045584106
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131471247"
---
# <a name="validate-a-dynamic-group-membership-rule-preview-in-azure-active-directory"></a>Valider une règle d’appartenance à un groupe dynamique (préversion) dans Azure Active Directory

Azure Active Directory (Azure AD) offre désormais un moyen de valider les règles de groupe dynamiques (en préversion publique). Sous l’onglet **Valider les règles**, vous pouvez valider votre règle dynamique par rapport à des exemples de membres du groupe pour vérifier que la règle fonctionne comme prévu. Lors de la création ou de la mise à jour des règles de groupe dynamiques, les administrateurs veulent savoir si un utilisateur ou un appareil sera membre du groupe. Ceci permet d’évaluer si l’utilisateur ou l’appareil répond aux critères de la règle et facilite la résolution des problèmes quand l’appartenance n’est pas attendue.

## <a name="prerequisites"></a>Prérequis
Pour utiliser la fonctionnalité évaluer l’appartenance à une règle de groupe dynamique, l’administrateur doit avoir l’une des règles suivantes affectées directement : administrateur général, administrateur de groupes ou administrateur Intune.

> [!TIP]
> L’attribution de l’un des rôles requis via l’appartenance à un groupe indirect n’est pas encore prise en charge.
>

## <a name="step-by-step-walk-through"></a>Procédure pas à pas

Pour commencer, accédez à **Azure Active Directory** > **Groupes**. Sélectionnez un groupe dynamique existant ou créez un groupe dynamique, puis cliquez sur Règles d’appartenance dynamique. Vous pouvez alors voir l’onglet **Valider les règles**.

![Rechercher l’onglet Valider les règles et démarrer avec une règle existante](./media/groups-dynamic-rule-validation/validate-tab.png)

Sous l’onglet **Valider les règles**, vous pouvez sélectionner des utilisateurs pour valider leurs appartenances. Vous pouvez sélectionner 20 utilisateurs ou appareils en même temps.

![Ajouter des utilisateurs par rapport auxquels valider la règle existante](./media/groups-dynamic-rule-validation/validate-tab-add-users.png)

Une fois que vous avez choisi les utilisateurs ou les appareils dans le sélecteur et cliqué sur **Sélectionner**, la validation démarre et les résultats de la validation apparaissent.

![Visualiser les résultats de la validation](./media/groups-dynamic-rule-validation/validate-tab-results.png)

Les résultats indiquent si un utilisateur est membre ou non du groupe. Si la règle n’est pas valide ou s’il existe un problème réseau, le résultat s’affiche comme étant **Inconnu**. Dans le cas de **Inconnu**, le message d’erreur détaillé décrit le problème et les actions nécessaires.

![Visualiser les détails des résultats de la validation](./media/groups-dynamic-rule-validation/validate-tab-view-details.png)

Vous pouvez modifier la règle : la validation des appartenances sera déclenchée. Pour voir pourquoi un utilisateur n’est pas membre du groupe, cliquez sur « Afficher les détails » : les détails de la vérification montrent alors le résultat de chaque expression composant la règle. Cliquez sur **OK** pour quitter.

## <a name="next-steps"></a>Étapes suivantes

- [Règles d’appartenance dynamique pour les groupes](groups-dynamic-membership.md)
