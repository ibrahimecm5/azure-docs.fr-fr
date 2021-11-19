---
title: Supprimer Microsoft Sentinel | Microsoft Docs
description: Comment supprimer votre instance Microsoft Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 4e811cfb24505c00f521121ce846cc58da9cff2f
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132517346"
---
# <a name="remove-microsoft-sentinel-from-your-workspace"></a>Supprimer Microsoft Sentinel de votre espace de travail

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Si vous ne voulez plus utiliser Microsoft Sentinel, cet article explique comment le supprimer de votre espace de travail.

## <a name="how-to-remove-microsoft-sentinel"></a>Comment supprimer Microsoft Sentinel

Suivez ce processus pour supprimer Microsoft Sentinel de votre espace de travail :

1. Accédez à **Microsoft Sentinel**, puis **Paramètres**, puis sélectionnez l’onglet **Supprimer Microsoft Sentinel**.

1. Avant de supprimer Microsoft Sentinel, veuillez utiliser les cases à cocher pour nous indiquer pourquoi vous le supprimez.

1. Sélectionnez **Supprimer Microsoft Sentinel de votre espace de travail**.
    
    ![Supprimer la solution SecurityInsights](media/offboard/delete-solution.png)

## <a name="what-happens-behind-the-scenes"></a>Que se passe-t-il en arrière-plan ?

Quand vous supprimez la solution, l’exécution de la première phase du processus de suppression par Microsoft Sentinel prend jusqu’à 48 heures.

Une fois la déconnexion identifiée, le processus d’annulation de l’intégration commence.

**La configuration de ces connecteurs est supprimée :**
-   Office 365

-   AWS

-   Alertes de sécurité des services Microsoft : Microsoft Defender pour Identity (*anciennement Azure ATP*), Microsoft Defender pour les applications Cloud, y compris Cloud Discovery Shadowi IT reporting, Azure AD Identity Protection, Microsoft Defender pour point de terminaison (*anciennement Microsoft Defender ATP*), alertes de sécurité de Microsoft Defender pour le Cloud

-   Informations sur les menaces

-   Journaux de sécurité courants (y compris les journaux basés sur CEF, Barracuda et Syslog) (si vous recevez des alertes de sécurité de Microsoft Defender pour le Cloud, ces journaux continueront à être collectés).

-   Événements de sécurité Windows (si vous recevez des alertes Microsoft Defender pour le Cloud, ces journaux continueront d’être collectés).

Au cours des 48 premières heures, les règles de données et d’analyse (y compris la configuration de l’automatisation en temps réel) ne seront plus accessibles ou interrogeables dans Microsoft Sentinel.

**Au bout de 30 jours, ces ressources sont supprimées :**

-   Incidents (dont les métadonnées d’enquête)

-   Règles analytiques

-   Signets

Vos playbooks, classeurs enregistrés, requêtes de repérage enregistrées et notebooks ne sont pas supprimés. **Certains peuvent cesser de fonctionner en raison des données supprimées. Vous pouvez les supprimer manuellement.**

Une fois que vous avez supprimé le service, il y a une période de grâce de 30 jours pendant laquelle vous pouvez réactiver la solution. Vos données et règles d’analyse sont alors restaurées, mais les connecteurs configurés qui étaient déconnectés doivent être reconnectés.

> [!NOTE]
> Si vous supprimez la solution, votre abonnement reste inscrit auprès du fournisseur de ressources Microsoft Sentinel. **Vous pouvez le supprimer manuellement.**




## <a name="next-steps"></a>Étapes suivantes
Dans ce document, vous avez appris à supprimer le service Microsoft Sentinel. Si vous changez d’avis et que vous voulez le réinstaller :
- Commencez à utiliser l'[intégration de Microsoft Sentinel](quickstart-onboard.md).
