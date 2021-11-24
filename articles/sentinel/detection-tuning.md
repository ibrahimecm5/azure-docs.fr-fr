---
title: Obtenir des recommandations de paramétrage précis pour vos règles d’analyse dans Microsoft Sentinel
description: Apprenez à affiner vos règles de détection des menaces dans Microsoft Sentinel en utilisant des recommandations générées automatiquement afin de réduire les faux positifs tout en conservant la couverture de la détection des menaces.
author: yelevin
ms.author: yelevin
ms.service: microsoft-sentinel
ms.topic: how-to
ms.date: 11/09/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 1b1d71f2c8a6a5f74d68d9480b918743a707cc83
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132521507"
---
# <a name="get-fine-tuning-recommendations-for-your-analytics-rules-in-microsoft-sentinel"></a>Obtenir des recommandations de paramétrage précis pour vos règles d’analyse dans Microsoft Sentinel

> [!IMPORTANT]
>
> Le réglage de la da détection de menaces est actuellement en **PRÉVERSION**. Consultez l’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) pour connaître les conditions juridiques supplémentaires s’appliquant aux fonctionnalités Azure sont en version bêta, en préversion ou non encore en disponibilité générale.

Le réglage des règles de détection des menaces dans votre SIEM peut être un processus difficile, délicat et continu afin d’arriver à un équilibre entre l’optimisation de la couverture de détection des menaces et la réduction du taux de faux positifs. Microsoft Sentinel simplifie et optimise ce processus grâce au Machine Learning pour analyser les milliards de signaux de vos sources de données, ainsi que vos réponses aux incidents au fil du temps, afin d’en déduire des modèles et de vous fournir des recommandations et des informations exploitables qui réduisent considérablement le temps de paramétrage et vous permettent de vous concentrer sur la détection des menaces réelles et la réponse à ces dernières.

Les recommandations et les informations de paramétrage sont désormais intégrées à vos règles d’analyse. Cet article explique ce que ces informations présentent et comment vous pouvez mettre en œuvre les recommandations.

## <a name="view-rule-insights-and-tuning-recommendations"></a>Afficher les informations et les recommandations de réglage

Pour voir si Microsoft Sentinel propose des recommandations de paramétrage pour l’une de vos règles d’analyse, sélectionnez **Analyse** dans le menu de navigation de Microsoft Sentinel.

Toutes les règles qui comportent des recommandations affichent une icône d’ampoule, comme illustré ici :

:::image type="content" source="media/detection-tuning/rule-list-with-insight.png" alt-text="Capture d’écran de la liste des règles d’analyse avec l’indicateur de recommandation." lightbox="media/detection-tuning/rule-list-with-insight.png":::

Modifiez la règle pour voir les recommandations avec les autres informations. Elles s’affichent ensemble sous l’onglet **définir la logique de la règle** de l’assistant de règle d’analyse, sous **Simulation des résultats**.

:::image type="content" source="media/detection-tuning/tuning-insights.png" alt-text="Capture d’écran des informations de paramétrage de la règle d’analyse." lightbox="media/detection-tuning/tuning-insights.png":::

### <a name="types-of-insights"></a>Types d’informations

L’affichage des **informations de paramétrage** est constitué de plusieurs volets que vous pouvez faire défiler ou balayer, chacun d’entre eux présentant des différences. Le laps de temps de 14 jours pour lequel les informations sont affichées est affiché en haut du cadre.

1. Le premier volet affiche des informations statistiques : le nombre moyen d’alertes par incident, le nombre d’incidents ouverts et le nombre d’incidents fermés, regroupés par classification (vrai/faux positif). Cette information vous aide à déterminer la charge de cette règle et à déterminer si un paramétrage est nécessaire, par exemple si les paramètres de regroupement doivent être ajustés.

    :::image type="content" source="media/detection-tuning/rule-efficiency.png" alt-text="Capture d’écran de des informations sur l’efficacité de la règle.":::

    Cette information est le résultat d’une requête Log Analytics. La sélection des **alertes moyennes par incident** vous permet d’arriver à la requête Log Analytics qui a produit cette information. En sélectionnant **Incidents ouverts**, vous accédez au panneau **Incidents**.

1. Le deuxième volet vous recommande une liste d’[entités](entities-in-azure-sentinel.md) à exclure. Ces entités sont étroitement corrélées aux incidents que vous avez fermés et classés comme **faux positifs**. Sélectionnez le signe plus en regard de chaque entité de la liste pour l’exclure de la requête lors des prochaines exécutions de cette règle. 

    :::image type="content" source="media/detection-tuning/entity-exclusion.png" alt-text="Capture d’écran de la recommandation d’exclusion d’entités.":::

    Cette recommandation est générée par les modèles d’apprentissage automatique et de science des données de Microsoft. L’inclusion de ce volet dans l’affichage des **informations de paramétrage** dépend de toutes les recommandations à afficher.

1. Le troisième volet d’information présente les quatre entités mappées les plus fréquentes sur toutes les alertes générées par cette règle. Le mappage des entités doit être configuré sur la règle pour que cette fonction produise des résultats. Cette information peut vous aider à vous familiariser avec les entités le plus évidentes et qui en cachent d’autres. Vous pouvez gérer ces entités séparément dans une autre règle ou vous pouvez décider qu’il s’agit de faux positifs ou de bruit et les exclure de la règle.

    :::image type="content" source="media/detection-tuning/top-entities.png" alt-text="Capture d’écran des premières entités.":::

    Cette information est le résultat d’une requête Log Analytics. La sélection d’une des entités vous permet d’arriver à la requête Log Analytics qui a produit cette information.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d'informations, consultez les pages suivantes :
- [Gérer les faux positifs dans Microsoft Azure Sentinel](false-positives.md)
- [Utiliser les données UEBA pour analyser les faux positifs](investigate-with-ueba.md#use-ueba-data-to-analyze-false-positives)
- [Créer des règles d’analytique personnalisées pour détecter des menaces](detect-threats-custom.md)
