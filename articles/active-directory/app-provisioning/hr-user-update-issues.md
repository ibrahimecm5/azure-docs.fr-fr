---
title: Résoudre des problèmes de mise à jour d’utilisateur avec l’approvisionnement RH
description: Découvrez comment résoudre des problèmes de mise à jour d’utilisateur avec l’approvisionnement RH
author: kenwith
manager: karenh444
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: troubleshooting
ms.workload: identity
ms.date: 10/27/2021
ms.author: kenwith
ms.reviewer: chmutali
ms.openlocfilehash: c983429508fa55f14bb8450c58a7a106df00278f
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132342525"
---
# <a name="troubleshoot-hr-user-update-issues"></a>Résoudre des problèmes de mise à jour d’utilisateur RH

## <a name="null-and-empty-values-not-processed-as-expected"></a>Les valeurs Null et vides ne sont pas traitées comme prévu
**S’applique à :**
* Approvisionnement d’utilisateurs de Workday vers Active Directory local
* Approvisionnement d’utilisateurs de Workday vers Azure Active Directory
* Approvisionnement d’utilisateurs de SAP SuccessFactors vers Active Directory local
* Approvisionnement d’utilisateurs de SAP SuccessFactors vers Active Directory

| Résolution des problèmes | Détails |
|-- | -- |
| **Problème** | Vous avez correctement configuré l’application d’approvisionnement entrant. Vous recevez une valeur Null ou vide de l’application RH. Vous vous attendez à ce que le service d’approvisionnement efface la valeur de l’attribut cible correspondant dans Active Directory / Azure AD local. Mais l’opération échoue avec le message d’erreur suivant : `InvalidAttributeSyntax-LdapErr: The syntax is invalid. The parameter is incorrect. Error in attribute conversion operation, data 0, v3839` |
| **Cause** | Le service d’approvisionnement n’a pas de logique par défaut pour le traitement des valeurs Null. Lorsque le service d’approvisionnement reçoit une chaîne vide de l’application source, il tente de transmettre la valeur telle quelle à l’application cible. Dans ce cas, l’Active Directory local ne prend pas en charge la définition de valeurs de chaîne vides et vous voyez l’erreur ci-dessus. |
| **Résolution :** | Vérifiez les journaux d’approvisionnement. Identifiez les attributs de l’Active Directory cible qui reçoivent des valeurs de chaîne Null ou vides. Mettez à jour le mappage pour ces attributs afin d’utiliser un mappage d’expression. Consultez les résolutions recommandées ci-dessous. |

**Résolutions recommandées**

  Supposons que l’attribut `BusinessTitle` mappé à l’attribut AD `jobTitle` peut avoir une valeur Null ou vide dans Workday. 
  * Option 1 : définissez une expression pour vérifier la présence de valeurs vides ou Null à l’aide de fonctions telles que [IIF](functions-for-customizing-application-data.md#iif), [IsNullOrEmpty](functions-for-customizing-application-data.md#isnullorempty), [Coalesce](functions-for-customizing-application-data.md#coalesce) ou [IsPresent](functions-for-customizing-application-data.md#ispresent), et transmettez une valeur littérale non vide. 
  
     `IIF(IsNullOrEmpty([BusinessTitle]),"N/A",[BusinessTitle])`

  * Option 2 : utilisez la fonction [IgnoreFlowIfNullOrEmpty](functions-for-customizing-application-data.md#ignoreflowifnullorempty) pour annuler les attributs vides ou Null dans la charge utile envoyée à l’Active Directory/Azure AD local. 
  
     `IgnoreFlowIfNullOrEmpty([BusinessTitle])` 

## <a name="some-workday-attribute-updates-are-missing"></a>Certaines mises à jour d’attribut Workday sont manquantes
**S’applique à :**
* Approvisionnement d’utilisateurs de Workday vers Active Directory local
* Approvisionnement d’utilisateurs de Workday vers Azure Active Directory

| Résolution des problèmes | Détails |
|-- | -- |
| **Problème** | Vous avez correctement configuré l’application d’approvisionnement entrante Workday et vous êtes connecté à l’URL de locataire Workday. Vous observez qu’il y a un délai dans le flux de certaines mises à jour d’attribut dans Workday ou, dans certains cas, les modifications d’attributs dans Workday ne sont pas transmises comme prévu lors de la synchronisation incrémentielle. |
| **Cause** | Pendant la synchronisation incrémentielle, l’application d’approvisionnement interroge le journal des transactions Workday pour rechercher les modifications apportées à l’entité Worker principale et seules les modifications suivies par le journal des transactions de Workday sont traitées. <br> Si les modifications apportées à un attribut Workday dans votre installation ne sont pas suivies par le journal des transactions de Workday, Azure AD ne pourra pas récupérer (fetch) cette modification. Par exemple : l’attribut Workday *LocalReference* fait partie du mappage d’attributs par défaut et a `wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Local_Reference/wd:ID[@wd:type='Locale_ID']/text()` XPATH. Notez que cet attribut fait partie de l’entité *Business_Site_Summary_Data*. Une modification de la valeur de cet attribut dans Workday n’apparaît pas dans le journal des transactions Workday. Ainsi, pendant la synchronisation incrémentielle, la nouvelle valeur de cet attribut s’affiche uniquement si un attribut associé à l’entité Worker principale change également pendant l’intervalle de synchronisation. |
| **Résolution :** | Si vous remarquez ce comportement fréquemment, lorsque les modifications apportées à certains attributs Workday ne circulent pas, nous vous recommandons d’exécuter régulièrement une synchronisation complète hebdomadaire ou mensuelle. |

## <a name="user-match-with-extensionattribute-not-working"></a>L’utilisateur a trouvé un extensionAttribute qui ne fonctionne pas
**S’applique à :**
* Approvisionnement d’utilisateurs de Workday vers Azure Active Directory
* Approvisionnement d’utilisateurs de SAP SuccessFactors vers Active Directory

| Résolution des problèmes | Détails |
|-- | -- |
| **Problème** | Supposons que vous utilisez *extensionAttribute3* dans Azure AD pour stocker l’ID de l’employé et que vous l’avez mappé à l’attribut *WorkerID* Workday ou *personIdExternal* SuccessFactors pour la correspondance de l’utilisateur. Avec cette configuration, l’étape correspondante dans le processus d’approvisionnement échoue. Ce problème a un impact sur la création et les mises à jour de l’utilisateur. |
| **Cause** | *OnPremisesExtensionAttributes* Azure AD (`extensionAttributes1-15`) ne peut pas être utilisé en tant qu’attribut correspondant, car le paramètre `$filter` d’**Azure AD API Graph** ne [prend pas en charge le filtrage par extensionAttributes](/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-supported-queries-filters-and-paging-options#filter). |
| **Résolution :** | N’utilisez pas Azure AD *OnPremisesExtensionAttributes* (`extensionAttributes1-15`) dans la paire d’attributs correspondante. Utilisez employeeID. |


## <a name="next-steps"></a>Étapes suivantes

* [En savoir plus sur les scénarios d’intégration d’Azure AD et de Workday ainsi que sur les appels de service web](workday-integration-reference.md)
* [Découvrez comment consulter les journaux d’activité et obtenir des rapports sur l’activité d’approvisionnement](check-status-user-account-provisioning.md)
