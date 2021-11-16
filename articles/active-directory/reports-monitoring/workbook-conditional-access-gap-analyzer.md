---
title: Classeur Analyseur des différences d’accès conditionnel dans Azure AD | Microsoft Docs
description: Découvrez comment utiliser le classeur Analyseur des différences d’accès conditionnel.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: karenho
editor: ''
ms.service: active-directory
ms.topic: reference
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/05/2021
ms.author: markvi
ms.reviewer: sarbar
ms.collection: M365-identity-device-management
ms.openlocfilehash: bd62dbd1fc279e2ed26472b1c0a0464b56be92c0
ms.sourcegitcommit: 5af89a2a7b38b266cc3adc389d3a9606420215a9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2021
ms.locfileid: "131990566"
---
# <a name="conditional-access-gap-analyzer-workbook"></a>Analyseur des différences d’accès conditionnel

Dans Azure AD, vous pouvez protéger l’accès à vos ressources en configurant des stratégies d’accès conditionnel.
En tant qu’administrateur informatique, vous souhaitez vous assurer que vos stratégies d’accès conditionnel fonctionnent comme prévu pour garantir que vos ressources sont correctement protégées. Le classeur Analyseur des différences d’accès conditionnel vous permet de détecter les écarts dans votre implémentation de l’accès conditionnel.  

Cet article vous fournit une vue d’ensemble de ce classeur.


## <a name="description"></a>Description

![Catégorie de classeur](./media/workbook-conditional-access-gap-analyzer/workbook-category.png)

En tant qu’administrateur informatique, vous souhaitez vous assurer que seules les personnes appropriées peuvent accéder à vos ressources. L’accès conditionnel Azure AD vous aide à atteindre cet objectif.  

Le classeur Analyseur des différences d’accès conditionnel vous permet de vérifier que vos stratégies d’accès conditionnel fonctionnent comme prévu.

**Ce classeur :**

- Met en surbrillance les connexions utilisateur pour lesquelles aucune stratégie d’accès conditionnel n’est appliquée. 
- S’assure qu’aucun utilisateur, application ou emplacement n’a été exclu intentionnellement des stratégies d’accès conditionnel.  

 

## <a name="sections"></a>Sections


Le classeur comporte quatre sections :  

- Utilisateurs se connectant à l'aide de l'authentification héritée 

- Nombre de connexions par applications qui ne sont pas affectées par les stratégies d’accès conditionnel 

- Événements de connexion à haut risque ignorant les stratégies d’accès conditionnel 

- Nombre de connexions par emplacement qui n’ont pas été affectées par les stratégies d’accès conditionnel 


![Couverture d’accès conditionnel par localisation](./media/workbook-conditional-access-gap-analyzer/conditianal-access-by-location.png)

Chacune de ces tendances offre une répartition des connexions au niveau utilisateur, afin que vous puissiez voir quels utilisateurs par scénario contournent l’accès conditionnel. 

## <a name="filters"></a>Filtres

Ce classeur prend en charge la définition d’un filtre d’intervalle de temps.

![Filtre d’intervalle de temps](./media/workbook-conditional-access-gap-analyzer/time-range.png)



## <a name="best-practices"></a>Meilleures pratiques

Utilisez ce classeur pour vous assurer que votre locataire est configuré sur les meilleures pratiques suivantes en matière d’accès conditionnel :  

- Bloquer toutes les connexions avec l’authentification héritée 

- Appliquer au moins une stratégie d’accès conditionnel à chaque application 

- Bloquer toutes les connexions à haut risque  

- Bloquer les connexions provenant d’emplacements non approuvés  

 





## <a name="next-steps"></a>Étapes suivantes

- [Guide pratique pour utiliser des classeurs Azure AD](howto-use-azure-monitor-workbooks.md)
