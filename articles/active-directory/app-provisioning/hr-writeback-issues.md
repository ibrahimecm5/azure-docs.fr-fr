---
title: Résoudre les problèmes d’écriture différée avec l’approvisionnement RH
description: Découvrez comment résoudre les problèmes de mise à jour de gestionnaire avec l’approvisionnement RH
services: active-directory
author: kenwith
manager: karenh444
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: troubleshooting
ms.workload: identity
ms.date: 10/27/2021
ms.author: kenwith
ms.reviewer: chmutali
ms.openlocfilehash: ff6f9d291b52b7028fbccdc001bc10d60c8c1450
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131479202"
---
# <a name="troubleshoot-hr-write-back-issues"></a>Résoudre les problèmes d’écriture différée RH

## <a name="null-and-empty-values-not-processed-as-expected"></a>Les valeurs Null et vides ne sont pas traitées comme prévu
**S’applique à :**
* Workday Writeback
* Écriture différée de SAP SuccessFactors

| | |
|-- | -- |
| **Problème** | Vous avez correctement configuré l’application Writeback. Vous obtenez une valeur Null ou vide à partir de Azure AD. Vous vous attendez à ce que le service d’approvisionnement efface la valeur d’e-mail ou de numéro de téléphone correspondante dans l’application RH. Mais l'opération échoue. |
| **Cause** | Le service d’approvisionnement n’a pas de logique par défaut pour le traitement des valeurs Null. Lorsque le service d’approvisionnement reçoit une chaîne vide de l’application source, il tente de transmettre la valeur telle quelle à l’application cible. Si Workday ou SuccessFactors ne peuvent pas traiter des valeurs vides, une erreur est retournée. |
| **Résolution :** | Mettez à jour le mappage d’attributs pour utiliser les mappages d’expressions comme recommandé ci-dessous. |

**Résolutions recommandées**

  Supposons que l’attribut `telephoneNumber` mappé à l’attribut SAP SuccessFactors `businessPhoneNumber` peut avoir la valeur Null ou être vide dans Azure AD. 
  * Option 1 : Définissez une expression pour vérifier la présence de valeurs vides ou Null à l’aide de fonctions telles que [IIF](functions-for-customizing-application-data.md#iif), [IsNullOrEmpty](functions-for-customizing-application-data.md#isnullorempty), [Coalesce](functions-for-customizing-application-data.md#coalesce) ou [IsPresent](functions-for-customizing-application-data.md#ispresent) et transmettez une valeur littérale non vide (exemple : 000-000-0000 dans le cas présent). 
  
     `IIF(IsNullOrEmpty([telephoneNumber]),"000-000-0000",[telephoneNumber])`

  * Option 2 : Utilisez la fonction [IgnoreFlowIfNullOrEmpty](functions-for-customizing-application-data.md#ignoreflowifnullorempty) pour déposer des attributs vides ou Null dans la charge utile envoyée à SuccessFactors. 
  
     `IgnoreFlowIfNullOrEmpty([telephoneNumber])` 



## <a name="next-steps"></a>Étapes suivantes

* [En savoir plus sur les scénarios d’intégration d’Azure AD et de Workday ainsi que sur les appels de service web](workday-integration-reference.md)
* [Découvrez comment consulter les journaux d’activité et obtenir des rapports sur l’activité d’approvisionnement](check-status-user-account-provisioning.md)
