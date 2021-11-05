---
title: Protection de vos ressources réseau dans Microsoft Defender pour le cloud
description: Ce document traite des suggestions dans Microsoft Defender pour le cloud qui peuvent vous aider à protéger vos ressources réseau Azure et à rester en conformité avec les stratégies de sécurité.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 04/05/2019
ms.author: memildin
ms.openlocfilehash: be9597fd1ee8b9231ec2c41b214e233dd92e1aa0
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131422289"
---
# <a name="protect-your-network-resources"></a>Protéger vos ressources réseau

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Microsoft Defender pour le cloud analyse en continu l’état de la sécurité de vos ressources Azure en guise de bonnes pratiques pour la sécurité réseau. Lorsque Defender pour le cloud identifie des failles de sécurité potentielles, il crée des recommandations qui vous guident tout au long du processus de configuration des contrôles nécessaires pour renforcer et protéger vos ressources.

Pour obtenir la liste complète des recommandations relatives à la mise en réseau, consultez [Recommandations relatives à la mise en réseau](recommendations-reference.md#recs-networking).

Cet article traite des suggestions relatives à la sécurité réseau qui s’appliquent à vos ressources Azure. Les suggestions relatives aux réseaux concernent les pare-feux nouvelle génération, les groupes de sécurité réseau, les règles de trafic entrant trop permissives concernant l’accès juste-à-temps aux machines virtuelles et plus encore. Pour obtenir la liste des recommandations sur les réseaux et des actions correctives, consultez [Gestion des recommandations de sécurité dans Microsoft Defender pour le cloud.](review-security-recommendations.md)

Les fonctionnalités **réseau** de Defender pour le cloud comprennent : 

- Carte réseau (nécessite Microsoft Defender pour les serveurs)
- [Renforcement de réseau adaptatif](adaptive-network-hardening.md) (nécessite Microsoft Defender pour les serveurs)
- Suggestions en matière de sécurité réseau
 
## <a name="view-your-networking-resources-and-their-recommendations"></a>Afficher vos ressources de mise en réseau et leurs recommandations

Dans la page [Inventaire des ressources](asset-inventory.md), utilisez le filtre de type de ressource pour sélectionner les ressources de mise en réseau que vous souhaitez examiner :

:::image type="content" source="./media/protect-network-resources/network-filters-inventory.png" alt-text="Types de ressources du réseau dans l’inventaire des ressources." lightbox="./media/protect-network-resources/network-filters-inventory.png":::


## <a name="network-map"></a>Mappage réseau

Le mappage réseau interactif fournit un affichage graphique avec des superpositions de sécurité qui vous proposent des suggestions et des aperçus pour renforcer vos ressources réseau. À l’aide du mappage, vous pouvez voir la topologie du réseau de vos charges de travail Azure et des connexions entre vos machines virtuelles et sous-réseaux. Vous pouvez voir la capacité à examiner au niveau du détail certaines ressources ainsi que leurs suggestions à partir du mappage.

Pour ouvrir le mappage réseau :

1. Dans le menu de Defender pour le cloud, ouvrez le tableau de bord **Protections des charges de travail**.

1. Sélectionnez **Carte réseau**.

    :::image type="content" source="./media/protect-network-resources/opening-network-map.png" alt-text="Ouverture de la carte réseau à partir des protections des charges de travail." lightbox="./media/protect-network-resources/opening-network-map.png":::

1. Sélectionnez le menu **Couches**, puis **Topologie**.
 
La valeur par défaut du mappage de la topologie affiche :

- Abonnements actuellement sélectionnés : La carte est optimisée pour les abonnements que vous avez sélectionnés dans le portail. Si vous modifiez votre sélection, la carte est regénérée avec les nouvelles sélections.
- Machines virtuelles, sous-réseaux et réseaux virtuels du type de ressource Resource Manager (les ressources Azure Classic ne sont pas prises en charge)
- Réseaux virtuels appairés
- Uniquement les ressources disposant de [suggestions de réseau](review-security-recommendations.md) avec un niveau de gravité élevé ou moyen
- Ressources accessibles sur Internet

:::image type="content" source="./media/protect-network-resources/network-map-info.png" alt-text="Capture d’écran de la carte de la topologie de réseau de Defender pour le cloud." lightbox="./media/protect-network-resources/network-map-info.png":::

## <a name="understanding-the-network-map"></a>Compréhension du mappage réseau

Le mappage réseau peut afficher vos ressources Azure dans un affichage **Topologie** et un affichage **Trafic**. 

### <a name="the-topology-view"></a>Affichage Topologie

Dans l’affichage **Topologie** du mappage réseau, vous pouvez afficher les aperçus suivants concernant vos ressources de mise en réseau :

- Dans le cercle intérieur, vous pouvez voir tous les réseaux virtuels au sein de vos abonnements sélectionnés. Le cercle suivant contient tous les sous-réseaux. Le cercle extérieur contient toutes les machines virtuelles.
- Les lignes qui connectent vos ressources dans le mappage vous permettent de savoir quelles ressources sont associées entre elles et de connaître la structure de votre réseau Azure. 
- Utilisez les indicateurs de niveau de gravité pour obtenir rapidement une vue d’ensemble des ressources qui ont des recommandations ouvertes de Defender pour le cloud.
- Vous pouvez cliquer sur les ressources pour les explorer au niveau du détail et afficher les détails et suggestions directement ainsi que dans le contexte du mappage réseau.  
- Si trop de ressources sont affichées sur la carte, Microsoft Defender pour le cloud utilise son algorithme propriétaire pour regrouper vos ressources de manière intelligente, en mettant en évidence celles qui sont dans l’état le plus critique et qui ont des recommandations avec la gravité la plus élevée.

Étant donné que le mappage est interactif et dynamique, chaque nœud est interactif, et l’affichage peut changer en fonction des filtres :

1. Vous pouvez modifier ce que vous voyez sur le mappage réseau à l’aide de filtres en haut. Vous pouvez centrer le mappage sur :

   -  **Intégrité de la sécurité** : vous pouvez filtrer le mappage selon la gravité (haute, moyenne, faible) de vos ressources Azure.
   - **Suggestions** : vous pouvez sélectionner les ressources qui sont affichées selon les suggestions actives sur ces ressources. Par exemple, vous pouvez afficher uniquement les ressources pour lesquels Defender pour le cloud vous recommande d’activer des groupes de sécurité réseau.
   - **Zones de réseau** : par défaut, le mappage affiche uniquement les ressources accessibles sur Internet. Vous pouvez également sélectionner les machines virtuelles internes.
 
2. Vous pouvez cliquer sur **Réinitialiser** dans le coin supérieur gauche à tout moment pour retourner le mappage à son état par défaut.

Pour explorer une ressource au niveau du détail :

1. Lorsque vous sélectionnez une ressource spécifique sur le mappage, le volet droit s’ouvre et vous donne des informations générales sur la ressource, les solutions de sécurité connectées, s’il y en a, et les suggestions relatives à la ressource. Vous aurez le même type de comportement pour chaque type de ressource que vous sélectionnez. 
2. Lorsque vous pointez un nœud du mappage avec la souris, vous pouvez afficher des informations générales sur la ressource, y compris l’abonnement, le type de ressource et le groupe de ressources.
3. Utilisez le lien pour zoomer sur l’info-bulle et recentrer le mappage sur ce nœud spécifique. 
4. Pour recentrer le mappage sur un autre élément que ce nœud spécifique, effectuez un zoom arrière.

### <a name="the-traffic-view"></a>Affichage Trafic

L’affichage **Trafic** vous offre un mappage de tout les trafics possibles entre vos ressources. Il vous un mappage visuel de toutes les règles que vous avez configurées et qui définissent quelles ressources peuvent communiquer avec qui. Cela permet de voir la configuration existante des groupes de sécurité réseau ainsi que d’identifier rapidement les configurations à risque possibles au sein de vos charges de travail.

### <a name="uncover-unwanted-connections"></a>Découvrir les connexions indésirables

Le point fort de cet affichage se situe dans sa capacité à vous montrer ces connexions autorisées avec les vulnérabilités qui peuvent exister. Vous pouvez alors utiliser cette combinaison de données pour effectuer le renforcement nécessaire sur vos ressources. 

Par exemple, vous pouvez détecter deux machines pour lesquelles vous ignoriez qu’elles pouvaient communiquer. Cela vous permet de mieux isoler les charges de travail et les sous-réseaux.

### <a name="investigate-resources"></a>Examiner les ressources

Pour explorer une ressource au niveau du détail :

1. Lorsque vous sélectionnez une ressource spécifique sur le mappage, le volet droit s’ouvre et vous donne des informations générales sur la ressource, les solutions de sécurité connectées, s’il y en a, et les suggestions relatives à la ressource. Vous aurez le même type de comportement pour chaque type de ressource que vous sélectionnez. 
2. Cliquez sur **Trafic** pour afficher la liste des trafics entrant et sortant possibles sur la ressource. Il s’agit d’une liste complète de ceux qui peuvent communiquer avec la ressource, et inversement, ainsi que des protocoles et ports utilisés pour communiquer. Par exemple, quand vous sélectionnez une machine virtuelle, toutes les machines virtuelles avec lesquelles elle peut communiquer sont montrées, et quand vous sélectionnez un sous-réseau, tous les sous-réseaux avec lesquels elle peut communiquer sont montrés.

**Ces données sont basées sur les analyses de groupes de sécurité réseau, ainsi que sur des algorithmes de Machine Learning avancés qui analysent plusieurs règles pour en comprendre les croisements et les interactions.** 

[![Mappage de trafic réseau.](./media/protect-network-resources/network-map-traffic.png)](./media/protect-network-resources/network-map-traffic.png#lightbox)


## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les recommandations qui s’appliquent à d’autres types de ressources Azure, consultez les rubriques suivantes :

- [Protection de vos machines et applications dans Microsoft Defender pour le cloud](./asset-inventory.md)
