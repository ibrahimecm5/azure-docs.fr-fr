---
title: Exporter et utiliser des données Azure Active Directory Identity Protection
description: Découvrez comment investiguer les risques dans Azure Active Directory Identity Protection
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: how-to
ms.date: 07/30/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: f723b6bf700b1dc18bea90b4ed0be00356c716a1
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132300743"
---
# <a name="how-to-export-risk-data"></a>Comment : Exporter les données liées aux risques

Azure AD stocke les rapports et les signaux de sécurité pendant un laps de temps défini. Lorsqu’il s’agit d’informations sur les risques, ce laps de temps peut ne pas suffire.

| Rapport/Signal | Azure AD Gratuit | Azure AD Premium P1 | Azure AD Premium P2 |
| --- | --- | --- | --- |
| Journaux d’audit | 7 jours | 30 jours | 30 jours |
| Connexions | 7 jours | 30 jours | 30 jours |
| Utilisation d’Azure AD MFA | 30 jours | 30 jours | 30 jours |
| Connexions risquées | 7 jours | 30 jours | 30 jours |

Les organisations peuvent choisir de stocker des données pour des périodes plus longues en modifiant les paramètres de diagnostic dans Azure AD, de manière à envoyer les données **RiskyUsers** et **UserRiskEvents** à un espace de travail Log Analytics, archiver des données dans un compte de stockage, envoyer en streaming des données vers un hub d’événements ou envoyer des données à une solution partenaire. Ces options se trouvent dans le **portail Azure** > **Azure Active Directory**, **Paramètres de diagnostic** > **Modifier le paramètre**. Si vous n’avez pas de paramètre de diagnostic, suivez les instructions de l’article [Créer des paramètres de diagnostic pour envoyer des journaux et des métriques de plateforme à différentes destinations](../../azure-monitor/essentials/diagnostic-settings.md) pour en créer un.

>[!NOTE]
>Les paramètres de diagnostic pour RiskyUsers et UserRiskEvents sont en préversion publique.

[ ![Écran des paramètres de diagnostic dans Azure AD montrant la configuration existante](./media/howto-export-risk-data/change-diagnostic-setting-in-portal.png) ](./media/howto-export-risk-data/change-diagnostic-setting-in-portal.png#lightbox)

## <a name="log-analytics"></a>Log Analytics

Log Analytics permet aux organisations d’interroger des données à l’aide de requêtes intégrées ou de requêtes Kusto personnalisées. Pour plus d’informations, consultez [Bien démarrer avec les requêtes de journal dans Azure Monitor](../../azure-monitor/logs/get-started-queries.md).

Après l’activation, vous pourrez accéder à Log Analytics dans le **portail Azure** > **Azure AD** > **Log Analytics**. Les tables les plus intéressantes pour les administrateurs Identity Protection sont **AADRiskyUsers** et **AADUserRiskEvents**.

- AADRiskyUsers : fournit des données comme le rapport **Utilisateurs à risque** dans Identity Protection.
- AADUserRiskEvents : fournit des données comme le rapport **Détections de risque** dans Identity Protection.

[ ![Vue de Log Analytics montrant une requête exécutée sur la table AADUserRiskEvents où apparaissent les 5 premiers événements](./media/howto-export-risk-data/log-analytics-view-query-user-risk-events.png) ](./media/howto-export-risk-data/log-analytics-view-query-user-risk-events.png#lightbox)

Dans l’image ci-dessus, la requête suivante a été exécutée pour montrer les cinq détections de risques les plus récentes. 

```kusto
AADUserRiskEvents
| take 5
```

Une autre option consiste à interroger la table AADRiskyUsers pour voir tous les utilisateurs à risque.

```kusto
AADRiskyUsers
```

> [!NOTE]
> Log Analytics n’a une visibilité sur les données que lorsque celles-ci sont envoyées en streaming. Les événements antérieurs à l’activation de l’envoi d’événements à partir d’Azure AD n’apparaissent pas.

## <a name="storage-account"></a>Compte de stockage

Si vous routez les journaux vers un compte de stockage Azure, vous pouvez les conserver plus longtemps que la période de conservation par défaut. Pour plus d’informations, consultez l’article [Tutoriel : Archiver des journaux d’activité Azure AD sur un compte de stockage Azure](../reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md).

## <a name="azure-event-hubs"></a>Hubs d'événements Azure

Azure Event Hubs peut examiner les données entrantes provenant de sources comme Azure AD Identity Protection, et fournir une analyse et une corrélation en temps réel. Pour plus d’informations, consultez l’article [Tutoriel : Diffuser en continu des journaux Azure Active Directory sur un hub d’événements Azure](../reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md).

## <a name="other-options"></a>Autres options

Les organisations peuvent choisir de [connecter les données Azure AD à Microsoft Sentinel](../../sentinel/data-connectors-reference.md#azure-active-directory-identity-protection) pour un traitement approfondi.

Les organisations peuvent utiliser l’[API Microsoft Graph pour interagir par programmation avec les événements à risque](howto-identity-protection-graph-api.md).

## <a name="next-steps"></a>Étapes suivantes

- [Présentation de la supervision d’Azure Active Directory](../reports-monitoring/overview-monitoring.md)
- [Installer et utiliser les vues Log Analytics pour Azure Active Directory](../reports-monitoring/howto-install-use-log-analytics-views.md)
- [Connecter les données à partir de Azure Active Directory (Azure AD) Identity Protection](../../sentinel/data-connectors-reference.md#azure-active-directory-identity-protection)
- [Azure Active Directory Identity Protection et Kit de développement logiciel (SDK) Microsoft Graph PowerShell](howto-identity-protection-graph-api.md)
- [Tutoriel : Diffuser en continu des journaux Azure Active Directory sur un hub d’événements Azure](../reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md)
