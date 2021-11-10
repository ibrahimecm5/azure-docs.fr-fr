---
title: 'Tutoriel : Archiver des journaux d’annuaire dans un compte de stockage | Microsoft Docs'
description: Découvrez comment configurer Diagnostics Azure pour envoyer des journaux Azure Active Directory sur un compte de stockage
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: karenhoran
editor: ''
ms.assetid: 045f94b3-6f12-407a-8e9c-ed13ae7b43a3
ms.service: active-directory
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 05/05/2021
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: f170cd4f3530ea7391d2fbd039263c29540321f8
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2021
ms.locfileid: "131995461"
---
# <a name="tutorial-archive-azure-ad-logs-to-an-azure-storage-account"></a>Tutoriel : Archiver des journaux Azure AD dans un compte de stockage Azure

Dans ce didacticiel, vous découvrez comment configurer les paramètres de diagnostic Azure Monitor pour acheminer des journaux d’activité Azure Active Directory (Azure AD) vers un compte de stockage Azure.

## <a name="prerequisites"></a>Prérequis 

Pour utiliser cette fonctionnalité, vous avez besoin des éléments suivants :

* Un abonnement et un compte de stockage Azure. Si vous n’en avez pas, vous pouvez demander un [essai gratuit](https://azure.microsoft.com/free/).
* Un locataire Azure AD.
* Utilisateur considéré comme *administrateur général* ou *administrateur de la sécurité* pour un locataire Azure AD.

## <a name="archive-logs-to-an-azure-storage-account"></a>Archiver les journaux d’activité dans un compte de stockage Azure

1. Connectez-vous au [portail Azure](https://portal.azure.com). 

2. Sélectionnez **Azure Active Directory** > **Supervision** > **Journaux d’audit**. 

3. Sélectionnez **Exporter les paramètres de données**. 

4. Dans le volet **Paramètres de diagnostic**, effectuez l’une des opérations suivantes :
    1. Pour modifier un paramètre existant, sélectionnez **Modifier le paramètre** en regard du paramètre de diagnostic que vous souhaitez mettre à jour.
    1. Pour ajouter de nouveaux paramètres, sélectionnez **Ajouter un paramètre de diagnostic**.  

    Vous pouvez avoir jusqu’à trois paramètres.

     ![Paramètres d’exportation](./media/quickstart-azure-monitor-route-logs-to-storage-account/ExportSettings.png)

5. Une fois dans le volet **Paramètres de diagnostic**, si vous créez un nouveau paramètre, attribuez un nom au paramètre pour vous rappeler son objectif (par exemple, *Envoyer au compte de stockage Azure*). Vous ne pouvez pas changer le nom d’un paramètre existant.

6. Sous **Détails de la destination**, activez la case à cocher **Archiver dans un compte de stockage**. 

7. Sélectionnez l’abonnement Azure dans le menu déroulant **Abonnement** et le compte de stockage dans le menu déroulant **Compte de stockage** dans lequel vous souhaitez acheminer les journaux.

8. Sélectionnez toutes les catégories appropriées sous **Détails de la catégorie** :

    Effectuez une ou plusieurs des actions suivantes :
    1. Cochez la case **AuditLogs** pour envoyer les journaux d’audit vers le compte de stockage.
    
    1. Cochez la case **SignInLogs** pour envoyer les journaux d’activité de connexion vers le compte de stockage.

    ![Paramètres de diagnostic](./media/quickstart-azure-monitor-route-logs-to-storage-account/DiagnosticSettings.png)

9. Une fois les catégories sélectionnées, dans le champ **Jours de rétention**, tapez le nombre de jours de rétention dont vous avez besoin pour vos données de journal. Par défaut, cette valeur est *0*, ce qui signifie que les journaux d’activité sont conservés indéfiniment dans le compte de stockage. Si vous définissez une valeur différente, les événements plus anciens que le nombre de jours indiqué sont automatiquement effacés.
 
10. Sélectionnez **Enregistrer** pour enregistrer le paramètre.

11. Fermez la fenêtre pour revenir au volet Paramètres de diagnostic.

## <a name="next-steps"></a>Étapes suivantes

* [Interpréter le schéma des journaux d’audit dans Azure Monitor](./overview-reports.md)
* [Interpréter le schéma des journaux d’activité de connexion dans Azure Monitor](reference-azure-monitor-sign-ins-log-schema.md)
* [Questions fréquentes et problèmes connus](concept-activity-logs-azure-monitor.md#frequently-asked-questions)
