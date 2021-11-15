---
title: Résoudre des problèmes de mise à jour de gestionnaire avec l’approvisionnement RH
description: Découvrez comment résoudre des problèmes de mise à jour de gestionnaire avec l’approvisionnement RH
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
ms.openlocfilehash: 21635b7c7df6639745916aecd207780e6ae5df52
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131479205"
---
# <a name="troubleshoot-hr-manager-update-issues"></a>Résoudre des problèmes de mise à jour de gestionnaire RH

**S’applique à :**
* Approvisionnement d’utilisateurs de Workday vers Active Directory local
* Approvisionnement d’utilisateurs de Workday vers Azure Active Directory
* Approvisionnement d’utilisateurs de SAP SuccessFactors vers Active Directory local
* Approvisionnement d’utilisateurs de SAP SuccessFactors vers Active Directory

## <a name="understanding-how-manager-reference-resolution-works"></a>Compréhension du fonctionnement de la résolution de référence de gestionnaire
Le service d’approvisionnement Azure AD met automatiquement à jour les informations du gestionnaire afin que la relation utilisateur-gestionnaire dans Azure AD soit toujours synchronisée avec vos données RH. Il utilise un processus nommé *résolution de référence de gestionnaire* pour mettre à jour l’attribut *manager* avec précision. Avant d’aborder les détails du processus, il est important de comprendre comment les informations relatives au gestionnaire sont stockées dans Azure AD et l’Active Directory local. 

* Dans l’**Active Directory local**, l’attribut *manager* stocke le *distinguishedName (dn)* du compte du gestionnaire dans AD. 
* Dans **Azure AD**, l’attribut *manager* est une propriété de navigation DirectoryObject dans Azure AD. Lorsque vous affichez l’enregistrement d’utilisateur dans le portail Azure, il affiche le *DisplayName* de l’enregistrement du gestionnaire dans Azure AD. 

La *résolution de référence de gestionnaire* est un processus en deux étapes : 
* Étape 1 : liez l’enregistrement source RH du gestionnaire à l’enregistrement du compte cible du gestionnaire à l’aide d’une paire d’attributs appelés *source anchor* (ancre source) et *target anchor* (ancre cible). 
* Étape 2 : utilisez les attributs de référence de gestionnaire définis dans le schéma pour mettre à jour l’attribut manager dans la cible au format requis. 

Les attributs anchor par défaut et les attributs de référence pour chaque application sont répertoriés ci-dessous. 

| Nom de l’application | Attribut d’ancrage | Attribut de référence dans le profil utilisateur | 
|--|--|--| 
| Workday | WID | ManagerReference (qui pointe vers le WID de l’enregistrement du gestionnaire) |
| SAP SuccessFactors | personIdExternal | manager (qui pointe vers l’attribut personIdExternal de l’enregistrement du gestionnaire) |
| Active Directory local | objectGUID | manager (qui pointe vers le DN de l’enregistrement du gestionnaire) |
| Azure AD | objectId | manager (qui pointe vers l’enregistrement Azure AD du gestionnaire) |

## <a name="prerequisites-for-successful-manager-update"></a>Conditions préalables pour une mise à jour réussie du gestionnaire
Pour que la *résolution de référence de gestionnaire* fonctionne correctement, les conditions préalables suivantes doivent être remplies : 
* Votre application d’approvisionnement doit être configurée pour utiliser les ancres source et cible par défaut indiquées ci-dessus. Ne modifiez pas les propriétés de métadonnées (type de données, expression d’API) associées à ces attributs d’ancre et de référence. 
* Les expressions d’API (XPATH pour Workday et JSONPath pour SuccessFactors) associées à l’attribut manager sont résolues en une valeur non Null valide. 
   * Expression d’API XPATH par défaut Workday ManagerReference : `wd:Worker/wd:Worker_Data/wd:Management_Chain_Data/wd:Worker_Supervisory_Management_Chain_Data[position()=1]/wd:Management_Chain_Data[last()=position()]/wd:Manager_Reference/wd:ID[@wd:type='WID']/text()`
   * Expression d’API JSONPath par défaut SuccessFactors manager : `$.employmentNav.results[0].userNav.manager.empInfo.personIdExternal`
* L’enregistrement du gestionnaire doit également figurer dans l’étendue du travail d’approvisionnement. 
* L’application d’approvisionnement doit avoir traité l’enregistrement du gestionnaire avant de traiter l’enregistrement de l’utilisateur. 

## <a name="provision-on-demand-does-not-update-manager-attribute"></a>L’approvisionnement à la demande ne met pas à jour l’attribut manager
| | |
|--|--|
| **Problème** | Vous avez correctement configuré l’application d’approvisionnement entrant. Vous testez la synchronisation avec l’approvisionnement à la demande. Elle ne met pas à jour l’attribut manager et vous recevez un message d’erreur *« valeur non valide »* .  |
| **Cause** | Votre travail d’approvisionnement ne répond pas à l’une des [conditions préalables pour une mise à jour réussie du gestionnaire](#prerequisites-for-successful-manager-update)  |
| **Résolution :** | * Si vous avez modifié le mappage de l’attribut manager par défaut, restaurez le mappage par défaut. <br> * Assurez-vous que l’enregistrement du gestionnaire figure dans l’étendue, et que l’expression d’API du gestionnaire correspond à une valeur valide. <br> * Exécutez l’approvisionnement à la demande pour l’enregistrement du gestionnaire, puis exécutez l’approvisionnement à la demande pour l’enregistrement de l’utilisateur.  |

## <a name="full-sync-does-not-update-manager-attribute"></a>La synchronisation complète ne met pas à jour l’attribut manager
| | |
|--|--|
| **Problème** | Vous avez correctement configuré l’application d’approvisionnement entrant. Vous utilisez un filtre d’étendue pour traiter uniquement certains enregistrements RH. Vous constatez que la résolution du gestionnaire ne se produit pas pour certains utilisateurs.  |
| **Cause** | Si vous utilisez des filtres d’étendue, le plus probable est que l’enregistrement du gestionnaire ne figure pas dans l’étendue.  |
| **Résolution :** | Mettez à jour le filtre d’étendue pour ajouter l’enregistrement du gestionnaire dans l’étendue  |

## <a name="next-steps"></a>Étapes suivantes

* [En savoir plus sur les scénarios d’intégration d’Azure AD et de Workday ainsi que sur les appels de service web](workday-integration-reference.md)
* [Découvrez comment consulter les journaux d’activité et obtenir des rapports sur l’activité d’approvisionnement](check-status-user-account-provisioning.md)
