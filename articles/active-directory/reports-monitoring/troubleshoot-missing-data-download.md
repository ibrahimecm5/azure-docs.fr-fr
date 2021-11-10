---
title: 'Résolution des problèmes : Données manquantes dans les journaux d’activité téléchargés | Microsoft Docs'
description: Fournit une résolution au problème de données manquantes dans les journaux d’activité Azure Active Directory téléchargés.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: karenhoran
editor: ''
ms.assetid: ffce7eb1-99da-4ea7-9c4d-2322b755c8ce
ms.service: active-directory
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9084d30ad8fe978defa7f336030535f7b4a97a1a
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2021
ms.locfileid: "131995404"
---
# <a name="i-cant-find-all-the-data-in-the-azure-active-directory-activity-logs-i-downloaded"></a>Je ne parviens pas à trouver toutes les donnée dans les journaux d’activité Azure Active Directory que j’ai téléchargés

## <a name="symptoms"></a>Symptômes

J’ai téléchargé les journaux d’activité (d’audit ou de connexion) et tous les enregistrements correspondant à la période choisie n’apparaissent pas. Pourquoi ? 

 ![Signalement](./media/troubleshoot-missing-data-download/01.png)
 
## <a name="cause"></a>Cause

Lorsque vous téléchargez des journaux d'activité sur le portail Azure, nous limitons l'échelle à 250 000 enregistrements, classés dans l'ordre du plus récent au moins récent. 

## <a name="resolution"></a>Résolution

Vous pouvez tirer parti des [API de création de rapports Azure AD](concept-reporting-api.md) pour extraire jusqu’à un million d’enregistrements pour un point donné.

## <a name="next-steps"></a>Étapes suivantes

* [FAQ sur les rapports Azure Active Directory](reports-faq.yml)

