---
title: Résoudre des problèmes de création d’utilisateurs avec un approvisionnement RH
description: Découvrez comment résoudre des problèmes de création d’utilisateurs avec un approvisionnement RH
author: kenwith
manager: karenh444
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: troubleshooting
ms.workload: identity
ms.date: 10/27/2021
ms.author: kenwith
ms.reviewer: chmutali
ms.openlocfilehash: 0c660221c230f884b43252cef25626e3d2b1d700
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132324571"
---
# <a name="troubleshoot-hr-user-creation-issues"></a>Résoudre des problèmes de création d’utilisateurs RH

## <a name="creation-fails-due-to-null--empty-values"></a>La création échoue en raison de valeurs Null/vides 

**S’applique à :**
* Approvisionnement d’utilisateurs de Workday vers Active Directory local
* Approvisionnement d’utilisateurs de Workday vers Azure Active Directory
* Approvisionnement d’utilisateurs de SAP SuccessFactors vers Active Directory local
* Approvisionnement d’utilisateurs de SAP SuccessFactors vers Active Directory

| Résolution des problèmes | Détails |
|-- | -- |
| **Problème** | Vous avez correctement configuré l’application d’approvisionnement entrant. Vous recevez une valeur Null ou vide de l’application RH. L’opération de création échoue avec le message d’erreur `InvalidAttributeSyntax-LdapErr: The syntax is invalid. The parameter is incorrect. Error in attribute conversion operation, data 0, v3839` |
| **Cause** | Le service d’approvisionnement n’a pas de logique par défaut pour le traitement des valeurs Null. Lorsque le service d’approvisionnement reçoit une chaîne vide de l’application source, il tente de transmettre la valeur telle quelle à l’application cible. Dans ce cas, l’Active Directory local ne prend pas en charge la définition de valeurs de chaîne vides et vous voyez l’erreur ci-dessus. |
| **Résolution :** | Vérifiez les journaux d’approvisionnement. Identifiez les attributs de l’Active Directory cible qui reçoivent des valeurs de chaîne Null ou vides. Mettez à jour le mappage pour ces attributs afin d’utiliser un mappage d’expression. Consultez les résolutions recommandées ci-dessous. |

**Résolutions recommandées**

  Supposons que l’attribut `BusinessTitle` mappé à l’attribut AD `jobTitle` peut avoir une valeur Null ou vide dans Workday. 
  * Option 1 : définissez une expression pour vérifier la présence de valeurs vides ou Null à l’aide de fonctions telles que [IIF](functions-for-customizing-application-data.md#iif), [IsNullOrEmpty](functions-for-customizing-application-data.md#isnullorempty), [Coalesce](functions-for-customizing-application-data.md#coalesce) ou [IsPresent](functions-for-customizing-application-data.md#ispresent), et transmettez une valeur littérale non vide. 
  
     `IIF(IsNullOrEmpty([BusinessTitle]),"N/A",[BusinessTitle])`

  * Option 2 : utilisez la fonction [IgnoreFlowIfNullOrEmpty](functions-for-customizing-application-data.md#ignoreflowifnullorempty) pour annuler les attributs vides ou Null dans la charge utile envoyée à l’Active Directory/Azure AD local. 
  
     `IgnoreFlowIfNullOrEmpty([BusinessTitle])` 


## <a name="next-steps"></a>Étapes suivantes

* [En savoir plus sur les scénarios d’intégration d’Azure AD et de Workday ainsi que sur les appels de service web](workday-integration-reference.md)
* [Découvrez comment consulter les journaux d’activité et obtenir des rapports sur l’activité d’approvisionnement](check-status-user-account-provisioning.md)

