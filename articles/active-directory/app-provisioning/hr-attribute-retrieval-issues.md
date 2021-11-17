---
title: Résoudre les problèmes de récupération des attributs avec l’approvisionnement RH
description: Découvrez comment résoudre les problèmes de récupération des attributs avec l’approvisionnement RH
author: kenwith
manager: karenh444
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: troubleshooting
ms.workload: identity
ms.date: 10/27/2021
ms.author: kenwith
ms.reviewer: chmutali
ms.openlocfilehash: dd1d69bf170f8213aac052a51350265bec08c991
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132283199"
---
# <a name="troubleshoot-hr-attribute-retrieval-issues"></a>Résoudre les problèmes de récupération des attributs RH

## <a name="provisioning-app-is-not-fetching-all-workday-attributes"></a>L’application d’approvisionnement n’extrait pas tous les attributs Workday
**S’applique à :**
* Approvisionnement d’utilisateurs de Workday vers Active Directory local
* Approvisionnement d’utilisateurs de Workday vers Azure Active Directory

| Résolution des problèmes | Détails |
|-- | -- |
| **Problème** | Vous venez de configurer l’application d’approvisionnement entrante Workday et vous êtes connecté à l’URL de locataire Workday. Vous avez exécuté une synchronisation de test et vous avez remarqué que l’application d’approvisionnement ne récupère pas tous les attributs de Workday. Seuls certains attributs sont lus et approvisionnés sur la cible. |
| **Cause** | Par défaut, l’application de provisionnement Workday est fournie avec le mappage d’attributs et les définitions XPATH qui fonctionnent avec les services web Workday (WWS) v21.1. Lors de la configuration de la connectivité à Workday dans l’application d’approvisionnement, si vous avez spécifié explicitement la version de l’API WWS (exemple : `https://wd3-impl-services1.workday.com/ccx/service/contoso4/Human_Resources/v34.0`), vous pouvez rencontrer ce problème en raison de l’incompatibilité entre la version de l’API WWS et les définitions XPATH.  |
| **Résolution :** | * *Option 1* : Supprimez les informations de version de l’API WWS de l’URL et utilisez la version d’API WWS par défaut v21.1 <br> * *Option 2* : Mettez à jour manuellement les expressions d’API XPATH afin qu’elles soient compatibles avec votre version d’API WWS préférée. Mettre à jour les **expressions de l'API XPATH** sous **Mappage d’attribut-> Options avancées-> Modifier la liste d’attributs pour Workday** faisant référence à la section [Référence d’attribut Workday](../app-provisioning/workday-attribute-reference.md#xpath-values-for-workday-web-services-wws-api-v30)  |

## <a name="provisioning-app-is-not-fetching-workday-integration-system-attributes--calculated-fields"></a>L’application d’approvisionnement ne récupère pas les attributs système d’intégration / champs calculés Workday
**S’applique à :**
* Approvisionnement d’utilisateurs de Workday vers Active Directory local
* Approvisionnement d’utilisateurs de Workday vers Azure Active Directory

| Résolution des problèmes | Détails |
|-- | -- |
| **Problème** | Vous venez de configurer l’application d’approvisionnement entrante Workday et vous êtes connecté à l’URL de locataire Workday. Vous disposez d’un système d’intégration configuré dans Workday et vous avez configuré des XPATH qui pointent vers des attributs dans le système d’intégration Workday. Toutefois, l’application d’approvisionnement Azure AD n’extrait pas les valeurs associées à ces attributs ou champs calculés du système d’intégration. |
| **Cause** | Il s'agit d'une limitation connue. L’application d’approvisionnement Workday ne prend pas en charge l’extraction de champs calculés/attributs du système d’intégration.  |
| **Résolution :** | Il n’existe aucune solution de contournement pour cette limitation. |

## <a name="next-steps"></a>Étapes suivantes

* [En savoir plus sur les scénarios d’intégration d’Azure AD et de Workday ainsi que sur les appels de service web](workday-integration-reference.md)
* [Découvrez comment consulter les journaux d’activité et obtenir des rapports sur l’activité d’approvisionnement](check-status-user-account-provisioning.md)
