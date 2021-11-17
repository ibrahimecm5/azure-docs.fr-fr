---
title: Optimiser les charges de travail Azure à l’aide d’Advisor Score
description: Utilisez Advisor Score pour tirer le meilleur parti d’Azure.
ms.topic: article
ms.date: 09/09/2020
ms.openlocfilehash: fc9bcee7d19d9e8ed7b550e7298d615d397c6f8b
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132484717"
---
# <a name="optimize-azure-workloads-by-using-advisor-score"></a>Optimiser les charges de travail Azure à l’aide d’Advisor Score

## <a name="introduction-to-advisor-score"></a>Présentation d’Advisor Score

Azure Advisor fournit des recommandations sur les bonnes pratiques en matière de charges de travail. Ces recommandations sont personnalisées et actionnables pour vous aider à :

* Améliorer la posture de vos charges de travail et optimiser vos déploiements Azure.
* Prévenir de manière proactive les principaux problèmes en respectant les bonnes pratiques.
* Évaluer vos charges de travail Azure par rapport aux cinq piliers de [Microsoft Azure Well-Architected Framework](/azure/architecture/framework/).

En tant que fonctionnalité de base d’Advisor, Advisor Score peut vous aider à atteindre efficacement ces objectifs.

Pour tirer le meilleur parti d’Azure, il est essentiel de comprendre où vous vous trouvez dans votre parcours d’optimisation des charges de travail. Vous devez connaître les services ou les ressources qui sont bien utilisés et ceux qui ne le sont pas. En outre, vous souhaiterez savoir comment hiérarchiser vos actions, en fonction des recommandations, afin d’optimiser les résultats.

Il est également important de suivre et de rapporter la progression durant ce parcours d’optimisation. Avec Advisor Score, vous pouvez facilement effectuer toutes ces opérations avec notre nouvelle expérience de gamification.

En tant que consultant cloud personnalisé, Azure Advisor évalue continuellement vos données de télémétrie d’utilisation et la configuration des ressources afin de vérifier que les bonnes pratiques du secteur d’activité sont respectées. Advisor regroupe ensuite ses résultats en un seul score. Avec ce score, vous savez, en un clin d’œil, si vous prenez les mesures nécessaires pour créer des solutions fiables, sécurisées et rentables.

Le score d’Advisor est constitué d’un score global, qui peut être divisé en cinq scores de catégorie. Un score pour chaque catégorie d’Advisor représente les cinq piliers d’une infrastructure bien conçue.

Vous pouvez suivre la progression dans le temps en affichant votre score global et votre score de catégorie avec une tendance quotidienne, hebdomadaire et mensuelle. Vous pouvez également définir des points de référence pour vous aider à atteindre vos objectifs.

 ![Capture d’écran montrant la page Advisor Score.](./media/advisor-score-1.png)

## <a name="interpret-an-advisor-score"></a>Interpréter un score Advisor

Advisor affiche le score Advisor global et la répartition pour les catégories Advisor en pourcentages. Un score de 100 % dans une catégorie signifie que toutes vos ressources évaluées par Advisor respectent les bonnes pratiques recommandées par Advisor. À l’autre extrémité du spectre, un score de 0 % signifie qu’aucune de vos ressources évaluées par Advisor ne respecte les recommandations d’Advisor. Vous bénéficiez ainsi des scores suivants :

* **Advisor Score** vous aide à définir une base de référence des performances de vos charges de travail ou abonnements. Vous pouvez également voir les tendances historiques afin de mieux comprendre la tendance.
* Les **scores par catégorie**  des différentes recommandations vous indiquent les recommandations en suspens qui amélioreront le plus votre score. Ces valeurs reflètent à la fois l’importance de la recommandation et la facilité d’implémentation prédite. Ces facteurs permettent de s’assurer que vous pouvez tirer le meilleur parti de votre temps. Ils vous aident également à définir des priorités.
* L’**impact sur le score par catégorie** de chaque recommandation vous aide à hiérarchiser vos actions correctives pour chaque catégorie.

La contribution de chaque recommandation à votre score par catégorie est clairement visible dans la page **Advisor Score** du portail Azure. Vous pouvez augmenter chaque score par catégorie du pourcentage indiqué dans la colonne d’**Amélioration potentielle par score** par catégorie. Cette valeur reflète à la fois le poids de la recommandation au sein de la catégorie et la facilité d’implémentation prévue des éventuelles opportunités à portée de main. Le fait de vous concentrer sur les recommandations présentant l’impact le plus élevé vous aidera à progresser le plus avec le temps.

![Capture d’écran montrant l’impact du score d’Advisor.](./media/advisor-score-2.png)

Si des recommandations d’Advisor ne sont pas pertinentes pour une ressource individuelle, vous pouvez reporter ou ignorer ces recommandations. Ils seront exclus du calcul du score avec la prochaine actualisation. Advisor utilisera également cette entrée comme une suggestion supplémentaire pour améliorer le modèle.

## <a name="how-is-an-advisor-score-calculated"></a>Comment un score Advisor est-il calculé ?

Advisor affiche vos scores de catégorie et votre score Advisor global sous forme de pourcentages. Un score de 100 % dans une catégorie signifie que toutes vos ressources *évaluées par Advisor* respectent les bonnes pratiques recommandées par Advisor. À l’autre extrémité du spectre, un score de 0 % signifie qu’aucune de vos ressources évaluées par Advisor ne respecte les recommandations d’Advisor.

**Chacune des cinq catégories a un score potentiel le plus élevé de 100.** Votre score Advisor global est calculé sous la forme d’une somme de chaque score de catégorie applicable, divisée par la somme des scores potentiels les plus élevés de toutes les catégories applicables. Pour la plupart des abonnements, cela signifie qu’Advisor ajoute le score de chaque catégorie et divise le total par 500. Toutefois, *chaque score de catégorie est calculé uniquement si vous utilisez des ressources évaluées par Advisor*.

### <a name="advisor-score-calculation-example"></a>Exemple de calcul du score Advisor

* **Score pour un abonnement :** Cet exemple montre la moyenne simple de tous les scores par catégorie Advisor pour votre abonnement. Si les scores de la catégorie Advisor sont- **Coût** = 73, **Fiabilité** = 85, **Excellence opérationnelle** = 77 et **Performance** = 100, le score Advisor est (73 + 85 + 77 + 100)/(4x100) = 0,84 % ou 84 %.
* **Score pour plusieurs abonnements :** Lorsque plusieurs abonnements sont sélectionnés, le score Advisor global que nous générons correspond au total des scores par catégorie pondérés. Ici, chaque score par catégorie Advisor est cumulé en fonction des ressources consommées par les abonnements. Une fois qu’Advisor a obtenu les scores de catégorie agrégés pondérés, Advisor effectue un calcul simple pour vous fournir un score global pour les abonnements.

### <a name="scoring-methodology"></a>Méthodologie de scoring

Le calcul du score Advisor peut être résumé en quatre étapes :

1. Advisor calcule le *coût de vente des ressources affectées*. Ces ressources sont celles de vos abonnements qui ont au moins une recommandation dans Advisor.
1. Le conseiller calcule la *coût de vente des ressources évaluées*. Ces ressources sont celles analysées par Advisor, qu’elles aient ou non des recommandations.
1. Pour chaque type de recommandation, Advisor calcule le *taux de ressources saines*. Ce ratio est le coût au détail des ressources affectées, divisé par le coût de vente des ressources évaluées.
1. Advisor applique trois pondérations supplémentaires au taux de ressources saines dans chaque catégorie :

   * Les recommandations ayant un impact plus important sont pondérées plus lourdement que les recommandations ayant un faible impact.
   * Les ressources ayant des recommandations à long terme sont davantage prises en compte dans le calcul du score.
   * Les ressources que vous reportez ou ignorez dans Advisor sont retirées entièrement du calcul de votre score.

Advisor applique ce modèle au niveau de la catégorie Advisor pour fournir un score Advisor pour chaque catégorie. La **sécurité** utilise un [modèle de score sécurisé](../defender-for-cloud/secure-score-security-controls.md#introduction-to-secure-score). Une moyenne simple produit le score final d’Advisor.

## <a name="advisor-score-faqs"></a>FAQ sur le score Advisor

### <a name="how-often-is-my-score-refreshed"></a>Quelle est la fréquence de mise à jour de mon score ?

Votre score est actualisé au moins une fois par jour.

### <a name="why-do-some-recommendations-have-the-empty---value-in-the-category-score-impact-column"></a>Pourquoi certaines recommandations ont-elles une valeur « - » vide dans la colonne d’impact sur le score par catégorie ?

Advisor n’inclut pas immédiatement les nouvelles recommandations ou celles avec des changements récents dans le modèle de scoring. Après une brève période d’évaluation, en général quelques semaines, elles sont incluses dans le score.

### <a name="why-is-the-cost-score-impact-greater-for-some-recommendations-even-if-they-have-lower-potential-savings"></a>Pourquoi le score Coût a-t-il un impact plus important pour certaines recommandations même si elles présentent des économies potentielles inférieures ?

Votre score **Coût** reflète à la fois vos économies potentielles liées à des ressources sous-exploitées et la facilité d’implémentation prévue de ces recommandations. Par exemple, une pondération supplémentaire est appliquée aux ressources affectées qui sont restées inactives plus longtemps, même si les économies potentielles sont inférieures.

### <a name="why-dont-i-have-a-score-for-one-or-more-categories-or-subscriptions"></a>Pourquoi n’ai-je pas de score pour une ou plusieurs catégories ou pour un ou plusieurs abonnements ?

Advisor génère un score uniquement pour les catégories et les abonnements dont il évalue des ressources.

### <a name="what-if-a-recommendation-isnt-relevant"></a>Que se passe-t-il si une recommandation n’est pas pertinente ?

Si vous ignorez une recommandation d’Advisor, elle est omise du calcul de votre score. Le fait d’ignorer des recommandations aide également Advisor à améliorer la qualité des recommandations.

### <a name="why-did-my-score-change"></a>Pourquoi mon score a-t-il changé ?

Votre score peut changer si vous corrigez des ressources impactées en adoptant les bonnes pratiques recommandées par Advisor. Si vous ou toute personne disposant d’autorisations sur votre abonnement a modifié ou créé de nouvelles ressources, vous pouvez également voir des fluctuations de votre score. Votre score est basé sur un rapport des ressources ayant un impact sur le coût par rapport au coût total de toutes les ressources.

### <a name="how-does-advisor-calculate-the-retail-cost-of-resources-on-a-subscription"></a>Comment Advisor calcule-t-il le coût à l’achat des ressources sur un abonnement ?

Advisor utilise le paiement à l’utilisation basé sur la [tarification Azure](https://azure.microsoft.com/pricing/). Ces tarifs ne reflètent pas les remises applicables. Les tarifs sont ensuite multipliés par la quantité d’utilisation au cours du dernier jour de l’allocation de la ressource. Le fait d’omettre les remises dans le calcul du coût de la ressource rend le score Advisor comparable d’un abonnement, locataire ou inscription à un autre où les remises peuvent varier.

### <a name="do-i-need-to-view-the-recommendations-in-advisor-to-get-points-for-my-score"></a>Dois-je consulter les recommandations dans Advisor pour obtenir des points pour mon score ?

Non. Votre score indique si vous adoptez les bonnes pratiques recommandées par Advisor, même si vous adoptez ces bonnes pratiques de manière proactive sans jamais consulter les recommandations dans Advisor.

### <a name="does-the-scoring-methodology-differentiate-between-production-and-dev-test-workloads"></a>La méthodologie de scoring fait-elle la différence entre les charges de travail de production et de développement/test ?

Non, pas pour l’instant. Mais vous pouvez ignorer les recommandations sur certaines ressources si elles sont utilisées à des fins de développement et de test et si la recommandation n’est pas applicable.

### <a name="can-i-compare-scores-between-a-subscription-with-100-resources-and-a-subscription-with-100000-resources"></a>Puis-je comparer les scores entre un abonnement ayant 100 ressources et un abonnement ayant 100 000 ressources ?

La méthodologie de score est conçue pour contrôler le nombre de ressources sur un abonnement et une combinaison de services. Les abonnements avec moins de ressources peuvent avoir des scores supérieurs ou inférieurs à ceux des abonnements contenant davantage de ressources.

### <a name="what-does-it-mean-when-i-see-coming-soon-in-the-score-impact-column"></a>Que signifie la mention « Bientôt disponible » dans la colonne d’impact sur le score ?

Ce message signifie que la recommandation est nouvelle et que nous travaillons à la placer dans le modèle de score Advisor. Quand cette nouvelle recommandation sera prise en compte dans le calcul du score, vous verrez la valeur d’impact sur le score pour votre recommandation.

### <a name="does-my-score-depend-on-how-much-i-spend-on-azure"></a>Mon score dépend-t-il des sommes d’argent que je consacre à Azure ?

Non. Votre score n’est pas nécessairement un reflet de votre budget. Les dépenses inutiles entraînent un score **Coût** plus bas.

## <a name="access-advisor-score"></a>Accéder à Advisor Score

Advisor Score est en préversion publique dans le portail Azure. Dans le volet gauche, sous la section **Advisor**, consultez **Advisor Score**.

![Capture d’écran montrant le point d'entrée d’Advisor Score.](./media/advisor-score-3.png)

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les recommandations d’Advisor, consultez :

* [Présentation du conseiller](advisor-overview.md)
* [Prise en main d’Advisor](advisor-get-started.md)
* [Recommandations d’Advisor en matière de coûts](advisor-cost-recommendations.md)
* [Recommandations d’Advisor en matière de performances](advisor-performance-recommendations.md)
* [Recommandations d’Advisor en matière de sécurité](advisor-security-recommendations.md)
* [Recommandations d’Advisor en matière d’excellence opérationnelle](advisor-operational-excellence-recommendations.md)
