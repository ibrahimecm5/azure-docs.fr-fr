---
title: Tableau de bord de la protection de charge de travail de Microsoft Defender pour le cloud et ses fonctionnalités
description: Découvrez les fonctionnalités du tableau de bord de la protection de charge de travail de Microsoft Defender pour le cloud
author: memildin
ms.author: memildin
ms.date: 11/02/2021
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.custom: ignite-fall-2021
ms.openlocfilehash: db2f01d390cc9503abd0b0a26650e2861dc059c1
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131096183"
---
# <a name="the-workload-protections-dashboard"></a>Tableau de bord de la protection de charge de travail

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Ce tableau de bord fournit les éléments suivants :

- Visibilité de la couverture de Microsoft Defender sur les différents types de ressources
- Liens pour configurer les fonctionnalités de protection avancée contre les menaces
- L’état d’intégration et l’installation de l’agent
- Alertes de détection des menaces 

Pour accéder au tableau de bord de la protection de charge de travail, sélectionnez **Protections de charge de travail** dans la section Sécurité du cloud du menu de Defender pour le cloud.

## <a name="whats-shown-on-the-dashboard"></a>Éléments affichés dans le tableau de bord

:::image type="content" source="./media/workload-protections-dashboard/sample-defender-dashboard-numbered.png" alt-text="Exemple de tableau de bord de protection de charge de travail de Defender pour le cloud." lightbox="./media/workload-protections-dashboard/sample-defender-dashboard-numbered.png":::

Le tableau de bord inclut les sections suivantes :

1. **Couverture de Microsoft Defender** : ici, vous pouvez voir les types de ressources figurant dans votre abonnement et éligibles pour la protection par Defender pour le cloud. Le cas échéant, vous aurez également la possibilité de procéder à une mise à niveau. Si vous souhaitez mettre à niveau toutes les ressources éligibles possibles, sélectionnez **Tout mettre à niveau**.

2. **Alertes de sécurité** : quand Defender pour le cloud détecte une menace dans n’importe quelle zone de votre environnement, il génère une alerte. Ces alertes décrivent les détails des ressources affectées, les étapes de correction suggérées et, dans certains cas, l’option permettant de déclencher une application logique en réponse. Sélectionnez n’importe quel emplacement dans ce graphique pour ouvrir la **page d’alertes de sécurité**.

3. **Protection avancée** : Defender pour le cloud comprend de nombreuses fonctionnalités de protection avancée contre les menaces pour les machines virtuelles, les bases de données SQL, les conteneurs, les applications web, votre réseau, et bien plus encore. Dans cette section de protection avancée, vous pouvez voir l’état des ressources dans les abonnements sélectionnés pour chacune de ces protections. Sélectionnez l’une d’entre elles pour accéder directement à la zone de configuration de ce type de protection.

4. **Insights** : ce volet déroulant de nouvelles, de lectures suggérées et d’alertes de haute priorité fournit des insights de Defender pour le cloud sur les questions de sécurité qui sont pertinentes pour vous et votre abonnement. Qu’il s’agisse d’une liste de CVE de gravité élevée détectés sur vos machines virtuelles par un outil d’analyse des vulnérabilités, ou d’un nouveau billet de blog d’un membre de l’équipe Defender pour le cloud, vous le trouverez ici dans le volet Insights.




## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez découvert le tableau de bord de la protection de charge de travail. 

> [!div class="nextstepaction"]
> [Activer les protections renforcées](enable-enhanced-security.md)

Pour plus d’informations sur les protections renforcées de Microsoft Defender, consultez [Présentation de Microsoft Defender pour le cloud](defender-for-cloud-introduction.md).
