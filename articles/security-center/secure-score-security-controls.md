---
title: Degré de sécurisation dans Microsoft Defender pour le cloud
description: Description du degré de sécurisation de Microsoft Defender pour le cloud et de ses contrôles de sécurité
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: article
ms.date: 09/01/2021
ms.author: memildin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 6cc1cf4aac9b8f363770495d976ad65b6ff79b49
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131014319"
---
# <a name="secure-score-in-microsoft-defender-for-cloud"></a>Degré de sécurisation dans Microsoft Defender pour le cloud

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

## <a name="introduction-to-secure-score"></a>Présentation du degré de sécurisation

Microsoft Defender pour le cloud a deux objectifs principaux : 

- Vous aider à comprendre votre situation actuelle en matière de sécurité
- Vous aider à améliorer efficacement votre sécurité

Pour vous permettre d’atteindre ces objectifs, Defender pour le cloud met à votre disposition une fonctionnalité appelée **degré de sécurisation**.

Defender pour le cloud évalue continuellement vos ressources, vos abonnements et votre organisation pour y rechercher d’éventuels problèmes de sécurité. Il agrège ensuite toutes ses découvertes sous la forme d’un score qui vous permet de déterminer d’un coup d’œil votre niveau de sécurité actuel : plus le score est élevé, plus le niveau de risque identifié est faible.

Sur les pages du portail Azure, le niveau de sécurité est indiqué sous forme de pourcentage, mais les valeurs sous-jacentes sont également clairement présentées :

:::image type="content" source="./media/secure-score-security-controls/single-secure-score-via-ui.png" alt-text="Degré de sécurisation global comme indiqué dans le portail.":::

Pour renforcer votre sécurité, consultez la page des recommandations de Defender pour le cloud. Vous y trouverez des conseils sur les actions à prendre pour améliorer votre degré de sécurisation. Chaque recommandation comprend des instructions pour vous aider à résoudre un problème spécifique.

Les recommandations sont regroupées en **contrôles de sécurité**. Chaque contrôle est un groupe logique de recommandations de sécurité associées, et reflète les surfaces d'attaque vulnérables. Votre degré de sécurisation n’augmente que si vous avez suivi *toutes* les recommandations fournies pour une même ressource au sein d’un contrôle. Pour connaître le degré de sécurisation de chacune des surfaces d'attaque de votre organisation, examinez le niveau de sécurité de chaque contrôle de sécurité.

Pour plus d’informations, consultez [Mode de calcul de votre degré de sécurisation](secure-score-security-controls.md#how-your-secure-score-is-calculated), ci-dessous. 

## <a name="how-your-secure-score-is-calculated"></a>Mode de calcul de votre degré de sécurisation 

La contribution de chaque contrôle de sécurité au degré de sécurisation global est indiquée clairement dans la page des recommandations.

:::image type="content" source="./media/secure-score-security-controls/security-controls.png" alt-text="Les contrôles de sécurité de Microsoft Defender pour le cloud et leur impact sur votre degré de sécurisation" lightbox="./media/secure-score-security-controls/security-controls.png":::

Pour obtenir le nombre maximal de points que peut avoir un contrôle de sécurité, toutes vos ressources doivent se conformer à l’intégralité des recommandations de sécurité fournies dans le contrôle de sécurité. Par exemple, Defender pour le cloud fournit plusieurs recommandations relatives à la sécurisation de vos ports de gestion. Vous devrez toutes les appliquer pour voir votre degré de sécurisation s’améliorer.

### <a name="example-scores-for-a-control"></a>Exemples de scores pour un contrôle

:::image type="content" source="./media/secure-score-security-controls/remediate-vulnerabilities-control.png" alt-text="Contrôle de sécurité Appliquer les mises à jour système." lightbox="./media/secure-score-security-controls/remediate-vulnerabilities-control.png":::


Dans cet exemple :

| #  | Nom                                           | Description                                                                                                                                                                                                                                                                                                                                                                                                                                |
|:-:|------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 1 | **Contrôle de sécurité Corriger les vulnérabilités** | Ce contrôle regroupe plusieurs recommandations relatives à la découverte et à la résolution de vulnérabilités connues.                                                                                                                                                                                                                                                                                                                                   |
| 2 | **Score maximal**                                  | Nombre maximal de points que vous pouvez gagner en appliquant toutes les recommandations d’un contrôle. Le score maximal d’un contrôle, qui est fixe pour chaque environnement, indique l’importance relative de ce contrôle. Utilisez les valeurs du score maximal pour que le triage des problèmes fonctionne en premier.<br>Pour obtenir la liste de tous les contrôles et leurs scores maximaux, consultez [Contrôles de sécurité et leurs recommandations](#security-controls-and-their-recommendations). |
| 3 | **Nombre de ressources**                        | 35 ressources sont affectées par ce contrôle.<br>Pour comprendre la contribution possible de chaque ressource, divisez le score maximal par le nombre de ressources.<br>Pour cet exemple : 6/35 = 0,1714<br>**Chaque ressource contribue pour 0,1714 point.**                                                                                                                                                                                          |
| 4 | **Score actuel**                              | Niveau de sécurité actuel de ce contrôle.<br>Score actuel = [Score par ressource] * [Nombre de ressources saines]<br> 0,1714 x 5 ressources saines = 0,86<br>Chaque contrôle contribue au score total. Dans cet exemple, le contrôle contribue pour 0,86 point au degré de sécurisation total actuel.                                                                                                                                               |
| 5 | **Augmentation potentielle du niveau de sécurité**                   | Points restants dont vous disposez dans le contrôle. Si vous appliquez toutes les recommandations de ce contrôle, votre score augmentera de 9 %.<br>Augmentation potentielle du niveau de sécurité = [Score par ressource] * [Nombre de ressources non saines]<br> 0,1714 x 30 ressources non saines = 5,14<br>                                                                                                                                                        |
|   |                                                |                                                                                                                                                                                                                                                                                                                                                                                                                                            |



### <a name="calculations---understanding-your-score"></a>Calculs : comprendre votre score

|Métrique|Formule et exemple|
|-|-|
|**Degré de sécurisation actuel du contrôle de sécurité**|<br>![Équation pour le calcul du degré de sécurisation d’un contrôle de sécurité.](media/secure-score-security-controls/secure-score-equation-single-control.png)<br><br>Chaque contrôle de sécurité individuel contribue au degré de sécurisation global. Chaque ressource concernée par une recommandation dans le contrôle contribue au degré de sécurisation actuel du contrôle. Le degré de sécurisation actuel de chaque contrôle correspond à la mesure de l’état des ressources *comprises* dans ce contrôle.<br>![Info-bulles présentant les valeurs utilisées lors du calcul du degré de sécurisation actuel du contrôle de sécurité](media/secure-score-security-controls/security-control-scoring-tooltips.png)<br>Dans cet exemple, le score (ou degré) maximal de 6 est divisé par 78, car il s’agit de la somme des ressources saines et non saines.<br>6 / 78 = 0,0769<br>En multipliant ce score par le nombre de ressources saines (4), vous obtenez le score actuel :<br>0,0769 \* 4 = **0,31**<br><br>|
|**Degré de sécurisation**<br>Abonnement unique|<br>![Équation pour le calcul du score sécurisé d’un abonnement](media/secure-score-security-controls/secure-score-equation-single-sub.png)<br><br>![Degré de sécurisation d’un abonnement avec tous les contrôles activés](media/secure-score-security-controls/secure-score-example-single-sub.png)<br>Dans cet exemple, vous pouvez voir un abonnement pour lequel tous les contrôles de sécurité sont disponibles (avec un degré de sécurisation maximal potentiel de 60 points). Le degré de sécurisation indique 28 points sur 60 points possibles, et les 32 points restants correspondent aux valeurs figurant dans la colonne « Potential score increase » (Augmentation potentielle du degré de sécurisation) des contrôles de sécurité.<br>![Liste des contrôles et augmentation potentielle du score](media/secure-score-security-controls/secure-score-example-single-sub-recs.png)|
|**Degré de sécurisation**<br>Abonnements multiples|<br>![Équation pour le calcul du degré de sécurisation de plusieurs abonnements.](media/secure-score-security-controls/secure-score-equation-multiple-subs.png)<br><br>Lors du calcul du score combiné pour plusieurs abonnements, Defender pour le cloud inclut un *poids* pour chaque abonnement. Les pondérations relatives de vos abonnements sont déterminées par Defender pour le cloud en fonction de facteurs tels que le nombre de ressources.<br>Le score actuel de chaque abonnement est calculé de la même façon que pour un abonnement unique, mais le poids est appliqué comme indiqué dans l’équation.<br>Quand vous consultez plusieurs abonnements, le degré de sécurisation évalue toutes les ressources de toutes les stratégies activées, et regroupe leur impact combiné sur le degré maximal de chaque contrôle de sécurité.<br>![Degré de sécurisation pour plusieurs abonnements avec tous les contrôles activés](media/secure-score-security-controls/secure-score-example-multiple-subs.png)<br>Le score combiné **n’est pas** une moyenne. Il s’agit plutôt d’une évaluation de l’état de toutes les ressources de tous les abonnements.<br>Ici aussi, si vous accédez à la page des recommandations et si vous ajoutez les points que vous pouvez potentiellement gagner, vous constaterez qu’il s’agit de la différence entre le score actuel (24) et le score maximal possible (60).|


### <a name="which-recommendations-are-included-in-the-secure-score-calculations"></a>Quelles sont les recommandations incluses dans les calculs du degré de sécurisation ?

Seules les recommandations intégrées ont un impact sur le degré de sécurisation.

Les recommandations marquées **Preview (Préversion)** ne sont pas incluses dans les calculs de votre degré de sécurisation. Elles doivent tout de même être corrigées dans la mesure du possible, ainsi lorsque la période de préversion se termine, elles seront prises en compte dans le calcul.

Exemple de recommandation de préversion :

:::image type="content" source="./media/secure-score-security-controls/example-of-preview-recommendation.png" alt-text="Recommandation portant l’indicateur de préversion.":::

## <a name="improve-your-secure-score"></a>Améliorer votre score de sécurité

Pour améliorer votre degré de sécurisation, appliquez les recommandations de sécurité qui figurent dans la liste des recommandations. Vous pouvez corriger chaque recommandation manuellement pour chaque ressource ou à l’aide de l’option **Corriger** (si disponible) pour résoudre rapidement un problème sur plusieurs ressources. Pour plus d’informations, consultez [Appliquer des recommandations](implement-security-recommendations.md).

Une autre façon d’améliorer votre score et de vous assurer que vos utilisateurs ne créent pas de ressources ayant un impact négatif sur votre score consiste à configurer les options « Appliquer » et « Refuser » sur les recommandations pertinentes. Pour plus d’informations, consultez [Empêcher des configurations incorrectes à l’aide des recommandations Appliquer/Refuser](prevent-misconfigurations.md).

## <a name="security-controls-and-their-recommendations"></a>Contrôles de sécurité et leurs recommandations

Le tableau ci-dessous liste les contrôles de sécurité dans Microsoft Defender pour le cloud. Pour chaque contrôle, vous pouvez voir le nombre maximal de points que vous pouvez ajouter à votre degré de sécurisation si vous appliquez *toutes* les recommandations indiquées dans le contrôle, pour *toutes* vos ressources. 

L’ensemble de recommandations de sécurité fournies par Defender pour le cloud est adapté aux ressources disponibles dans l’environnement de chaque organisation. Les recommandations peuvent être personnalisées davantage en [désactivant les stratégies](tutorial-security-policy.md#disable-security-policies-and-disable-recommendations) et en [exemptant des ressources spécifiques d’une recommandation](exempt-resource.md). 
 
Nous recommandons à chaque organisation d’examiner attentivement les initiatives Azure Policy qui lui ont été attribuées. 

> [!TIP]
> Pour plus d’informations sur l’examen et la modification de vos initiatives, consultez [Utilisation de stratégies de sécurité](tutorial-security-policy.md). 

Bien que l’initiative de sécurité par défaut de Defender pour le cloud soit basée sur les bonnes pratiques et les standards du secteur, il existe des scénarios dans lesquels les recommandations intégrées listées ci-dessous ne sont pas tout à fait adaptées à votre organisation. Il est donc parfois nécessaire de modifier l’initiative par défaut (sans compromettre la sécurité) pour garantir son alignement sur les propres stratégies, points de référence, normes et réglementations du secteur d’activité de votre organisation que vous êtes tenu de respecter.<br><br>

[!INCLUDE [security-center-controls-and-recommendations](../../includes/asc/security-control-recommendations.md)]



## <a name="faq---secure-score"></a>Questions fréquentes (FAQ) – Degré de sécurisation

### <a name="if-i-address-only-three-out-of-four-recommendations-in-a-security-control-will-my-secure-score-change"></a>Si je n’applique que trois recommandations sur quatre dans un contrôle de sécurité, mon degré de sécurisation changera-t-il ?
Non. Il ne changera pas tant que vous n’aurez pas appliqué toutes les recommandations fournies pour une même ressource. Pour obtenir le score maximal d’un contrôle, vous devez appliquer toutes les recommandations de l’ensemble des ressources.

### <a name="if-a-recommendation-isnt-applicable-to-me-and-i-disable-it-in-the-policy-will-my-security-control-be-fulfilled-and-my-secure-score-updated"></a>Si je désactive une recommandation dans ma stratégie car elle ne s’applique pas à mon cas, mon contrôle de sécurité sera-t-il respecté et mon degré de sécurisation sera-t-il mis à jour ?
Oui. Nous vous recommandons de désactiver les recommandations qui ne s’appliquent pas à votre environnement. Pour obtenir des instructions sur la désactivation d’une recommandation, consultez [Désactiver des stratégies de sécurité](./tutorial-security-policy.md#disable-security-policies-and-disable-recommendations).

### <a name="if-a-security-control-offers-me-zero-points-towards-my-secure-score-should-i-ignore-it"></a>Si un contrôle de sécurité n’ajoute aucun point à mon degré de sécurisation, dois-je l’ignorer ?
Dans certains cas, vous verrez un score de contrôle maximal supérieur à zéro, mais son impact est égal à zéro. Lorsque le score incrémentiel relatif à la correction de ressources est négligeable, celui-ci est arrondi à zéro. N’ignorez pas ces recommandations, car elles offrent malgré tout des améliorations au niveau de la sécurité. La seule exception concerne le contrôle « Bonne pratique supplémentaire ». L’application de ces recommandations n’augmentera pas votre score, mais elle améliorera votre sécurité globale.

## <a name="next-steps"></a>Étapes suivantes

Cet article a décrit le degré de sécurisation et les contrôles de sécurité inclus. 

> [!div class="nextstepaction"]
> [Accéder à votre degré de sécurisation et le suivre](secure-score-access-and-track.md)

Pour des informations connexes, consultez les articles suivants :

- [En savoir plus sur les différents éléments d’une recommandation](review-security-recommendations.md)
- [Découvrez comment appliquer les recommandations](implement-security-recommendations.md)
- [Consulter les outils basés sur GitHub pour travailler par programmation avec un score sécurisé](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score)
