---
title: Diffuser en continu les journaux d’activité Azure Active Directory vers les journaux d’activité Azure Monitor | Microsoft Docs
description: Découvrez comment intégrer des journaux d’activité Azure Active Directory aux journaux d’activité Azure Monitor
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: karenhoran
editor: ''
ms.assetid: 2c3db9a8-50fa-475a-97d8-f31082af6593
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 07/09/2021
ms.author: markvi
ms.reviewer: besiler
ms.custom: has-adal-ref
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6e171720b3cf36d7ad2b074adadaa0e514779c3b
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2021
ms.locfileid: "131995385"
---
# <a name="integrate-azure-ad-logs-with-azure-monitor-logs"></a>Intégrer des journaux d’activité Azure AD aux journaux d’activité Azure Monitor

Suivez les étapes dans cet article pour intégrer des journaux d’activité Azure Active Directory (Azure AD) à Azure Monitor.

Utilisez l’intégration des journaux d’activité d’Azure AD aux journaux d’activité Azure Monitor pour effectuer des tâches telles que :

 * Comparer vos journaux d’activité de connexion Azure AD aux journaux d’activité de sécurité publiés par Azure Security Center.
  
 * Résoudre les goulots d’étranglement de performances sur la page de connexion de votre application en mettant en corrélation les données de performances d’application à partir d’Azure Application Insights.

 * Analyser les utilisateur à risque en matière de protection d’identité et les journaux d'activité de détection de risque pour détecter les menaces dans votre environnement (préversion publique)
 
 * Identifiez les connexions à partir d’applications qui utilisent la Bibliothèque d’authentification Active Directory (ADAL) pour l’authentification. [Le support pour ADAL arrive à sa fin](../develop/msal-migration.md).

Cette vidéo Microsoft Ignite présente les avantages d’utiliser des journaux d’activité Azure Monitor pour les journaux d'activité Azure AD dans des scénarios pratiques :

> [!VIDEO https://www.youtube.com/embed/MP5IaCTwkQg?start=1894]

## <a name="supported-reports"></a>Rapports pris en charge

Vous pouvez acheminer les journaux d’activité d’audit et authentifier des journaux d’activité vers les journaux d’activité Azure Monitor en vue d’une analyse ultérieure. 

* **Journaux d’audit**: Le [rapport d’activité des journaux d’audit](concept-audit-logs.md) vous permet de consulter l’historique de toutes les tâches effectuées dans votre locataire.
* **Journaux d’activité de connexion** : Le [rapport d’activité de connexion](concept-sign-ins.md) vous permet d’identifier qui a effectué les tâches consignées dans les journaux d’audit.
* **Journaux de provisionnement** : les [journaux de provisionnement](../app-provisioning/application-provisioning-log-analytics.md) vous permettent de savoir quels utilisateurs ont été créés, mis à jour et supprimés dans toutes vos applications tierces. 
* **Journaux d'activité d’utilisateur à risque (préversion publique)**  : avec les [journaux d’utilisateur à risque](../identity-protection/howto-identity-protection-investigate-risk.md#risky-users), vous pouvez surveiller les modifications relatives au niveau de risque de l’utilisateur et l’activité de correction. 
* **Journaux d'activité de détection de risque (préversion publique)**  : avec les [journaux d'activité de détection de risque](../identity-protection/howto-identity-protection-investigate-risk.md#risk-detections), vous pouvez surveiller les détection de risque de l’utilisateur et analyser les tendances dans l’activité à risque détectée dans votre organisation. 


## <a name="prerequisites"></a>Prérequis 

Pour utiliser cette fonctionnalité, vous avez besoin des éléments suivants :

* Un abonnement Azure. Si vous n’en avez pas, vous pouvez demander un [essai gratuit](https://azure.microsoft.com/free/).
* Un locataire Azure AD.
* Utilisateur considéré comme *administrateur général* ou *administrateur de la sécurité* pour un locataire Azure AD.
* Espace de travail Log Analytics dans votre abonnement Azure. Découvrez comment [créer un espace de travail Log Analytics](../../azure-monitor/logs/quick-create-workspace.md).

## <a name="licensing-requirements"></a>Exigences en termes de licence

Un locataire Azure AD Premium P1 ou P2 est nécessaire pour utiliser cette fonctionnalité. Vous pouvez trouver le type de licence de votre locataire sur la page **[Vue d’ensemble](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview)** dans **Azure Active Directory**.

![Informations sur le locataire](./media/howto-integrate-activity-logs-with-log-analytics/tenant-information.png)
 
Si vous souhaitez connaître la durée de stockage des données d’activité dans un locataire Premium, consultez : [Pendant combien de temps les données sont-elles conservées par Azure AD ?](reference-reports-data-retention.md#how-long-does-azure-ad-store-the-data)

## <a name="send-logs-to-azure-monitor"></a>Envoyer des journaux à Azure Monitor

1. Connectez-vous au [portail Azure](https://portal.azure.com). 

2. Sélectionnez **Azure Active Directory** > **Paramètres de diagnostic** -> **Ajouter le paramètre de diagnostic**. Vous pouvez également sélectionner **Exporter les paramètres** à partir de la page **Journaux d’audit** ou **Connexions** pour accéder à la page de configuration des paramètres de diagnostic.  
    
3. Dans le menu **Paramètres de diagnostic**, cochez la case **Envoyer vers l’espace de travail Log Analytics**, puis sélectionnez **Configurer**.

4. Sélectionnez l’espace de travail Log Analytics vers lequel vous souhaitez envoyer les journaux d’activité ou créez un nouvel espace de travail dans la boîte de dialogue fournie.  

5. Effectuez l’une ou toutes les actions suivantes :
    * Cochez la case **AuditLogs** pour envoyer les journaux d’audit vers l’espace de travail Log Analytics. 
    * Cochez la case **SignInLogs** pour envoyer les journaux d’activité de connexion vers l’espace de travail Log Analytics.
    * Pour envoyer les journaux d’activité de connexion utilisateur non-interactive à l’espace de travail Log Analytics, cochez la case **NonInteractiveUserSignInLogs**.
    * Pour envoyer les journaux d'activité de connexion au principal de service à l’espace de travail Log Analytics, cochez la case **ServicePrincipleSignInLogs**.
    * Pour envoyer les journaux d’activité de connexion à l’identité gérée à l’espace de travail Log Analytics, cochez la case **ManagedIdentitySignInLogs**.
    * Pour envoyer les journaux d’activité de provisionnement à l’espace de travail Log Analytics, cochez la case **ProvisioningLogs**.
    * Pour envoyer des journaux d’activité de connexion des services de fédération Active Directory (AD FS) à l’espace de travail Log Analytics, sélectionnez **ADFSSignInLogs**.
    * Pour envoyer des journaux d'activité d’utilisateur à risque à l’espace de travail Log Analytics, cochez la case **RiskyUsers**. (préversion publique)
    * Pour envoyer des journaux d'activité de détection de risque à l’espace de travail Log Analytics, cochez la case **UserRiskEvents**. (préversion publique)

6. Sélectionnez **Enregistrer** pour enregistrer le paramètre.

    ![Paramètres de diagnostic](./media/howto-integrate-activity-logs-with-log-analytics/Configure.png)

7. Au bout de 15 minutes, vérifiez que les événements sont diffusés dans votre espace de travail Log Analytics.

## <a name="next-steps"></a>Étapes suivantes

* [Analyser les journaux d’activité Azure AD avec les journaux d’activité Azure Monitor](howto-analyze-activity-logs-log-analytics.md)
* [Installer et utiliser les vues Log Analytics pour Azure Active Directory](howto-install-use-log-analytics-views.md)
