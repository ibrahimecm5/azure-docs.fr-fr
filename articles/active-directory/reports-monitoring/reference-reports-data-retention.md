---
title: Pendant combien de temps les données de rapport sont-elles conservées par Azure AD ? | Microsoft Docs
description: Découvrez la durée pendant laquelle Azure stocke les différents types de données de rapport.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: karenhoran
editor: ''
ms.assetid: 183e53b0-0647-42e7-8abe-3e9ff424de12
ms.service: active-directory
ms.devlang: ''
ms.topic: reference
ms.tgt_pltfrm: ''
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/05/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: dc9658cddb17bf948fe562d806916d20bd28ba31
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2021
ms.locfileid: "131995366"
---
# <a name="how-long-does-azure-ad-store-reporting-data"></a>Pendant combien de temps les données de rapport sont-elles conservées par Azure AD ?


Dans cet article, vous allez en savoir plus sur les stratégies de rétention des données pour les différents rapports d’activité dans Azure Active Directory. 

### <a name="when-does-azure-ad-start-collecting-data"></a>Quand Azure AD commence-t-il à collecter des données ?

| Édition d’Azure AD | Début de la collection |
| :--              | :--   |
| Azure AD Premium P1 <br /> Azure AD Premium P2 | Lorsque vous souscrivez un abonnement |
| Azure AD Gratuit| La première fois que vous ouvrez le [panneau Azure Active Directory](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) ou utilisez les [API de création de rapports](./overview-reports.md)  |

---

### <a name="when-is-the-activity-data-available-in-the-azure-portal"></a>Quand les données d’activité sont-elles disponibles dans le portail Azure ?

- **Immédiatement** si vous avez déjà travaillé avec des rapports dans le portail Azure.
- **Dans les 2 heures** si vous n’avez pas encore activé la création de rapports dans le portail Azure.

---

### <a name="how-soon-can-i-see-activities-data-after-getting-a-premium-license"></a>Au bout de combien de temps puis-je consulter les données d'activité après avoir obtenu une licence Premium ?

Si vous avez déjà des données d'activités avec votre licence gratuite, vous pouvez les voir immédiatement lors de la mise à niveau. Si vous n'avez pas encore de données, il faudra jusqu’à trois jours pour que les données apparaissent dans les rapports après la mise à niveau vers une licence Premium.

---

### <a name="when-does-azure-ad-start-collecting-security-signal-data"></a>Quand Azure AD commence-t-il à collecter des données de signaux de sécurité ?  

Pour les signaux de sécurité, le processus de collection démarre lorsque vous choisissez d’utiliser **Identity Protection Center**. 

---

### <a name="how-long-does-azure-ad-store-the-data"></a>Pendant combien de temps les données sont-elles conservées par Azure AD ?

**Rapports d’activité**    

| Rapport                 | Azure AD Gratuit | Azure AD Premium P1 | Azure AD Premium P2 |
| :--                    | :--           | :--                 | :--                 |
| Journaux d’audit             | Sept jours        | 30 jours             | 30 jours             |
| Connexions               | Sept jours        | 30 jours             | 30 jours             |
| Utilisation d’Azure AD MFA        | 30 jours       | 30 jours             | 30 jours             |

Vous pouvez conserver les données d'activité d'audit et de connexion au-delà de la période de conservation par défaut décrite ci-dessus en les acheminant vers un compte de stockage Azure à l'aide d'Azure Monitor. Pour plus d’informations, consultez [Archiver des journaux d’activité Azure AD sur un compte de stockage Azure](quickstart-azure-monitor-route-logs-to-storage-account.md).

**Signaux de sécurité**

| Rapport         | Azure AD Gratuit | Azure AD Premium P1 | Azure AD Premium P2 |
| :--            | :--           | :--                 | :--                 |
| Utilisateurs à risque    | Aucune limite      | Aucune limite            | Aucune limite            |
| Connexions risquées | 7 jours        | 30 jours             | 90 jours             |

> [!NOTE]
> Les utilisateurs à risque ne sont pas supprimés tant que le risque n’a pas été corrigé.

---

### <a name="can-i-see-last-months-data-after-getting-an-azure-ad-premium-license"></a>Puis-je voir les données du mois dernier après avoir obtenu une licence Azure AD Premium ?

**Non**, c’est impossible. Azure stocke jusqu’à sept jours de données d’activité pour une version gratuite. Cela signifie que, lorsque vous passez d’une version gratuite à une version Premium, vous ne pouvez voir que sept jours de données au maximum.

---
