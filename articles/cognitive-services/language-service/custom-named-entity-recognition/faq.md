---
title: FAQ sur la reconnaissance d’entité nommée (NER) personnalisée
titleSuffix: Azure Cognitive Services
description: En savoir plus sur les questions fréquemment posées lors de l’utilisation de la reconnaissance d’entité nommée personnalisée.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-ner, ignite-fall-2021
ms.openlocfilehash: 488d72987f97dd01842332f5d1d11739e878115d
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131439098"
---
# <a name="frequently-asked-questions-for-custom-named-entity-recognition"></a>FAQ sur la reconnaissance d’entités nommées personnalisées

Trouvez des réponses aux questions les plus fréquentes sur les concepts et les scénarios liés à la classification de NER dans Azure Cognitive Service for Language.

## <a name="how-do-i-get-started-with-the-service"></a>Comment faire pour prendre en main le service ?

Pour plus de détails, consultez le [guide de démarrage rapide](./quickstart.md) pour créer rapidement votre premier projet ou le [guide pratique](how-to/create-project.md).

## <a name="what-are-the-service-limits"></a>Quelles sont les limites du service ?

Pour plus d’informations, consultez l’article sur les [limites du service](service-limits.md).

## <a name="how-many-tagged-files-are-needed"></a>Combien de fichiers étiquetés sont nécessaires ?

En général, des [données étiquetées](how-to/tag-data.md) diverses et représentatives permettent d’obtenir de meilleurs résultats, à condition que l’étiquetage soit effectué de manière précise, cohérente et complète. Il n’existe aucun nombre défini d’instances étiquetées qui permettront à tous les modèles d’offrir de bonnes performances. Les performances dépendent fortement de votre schéma et de son ambiguïté. Les types d’entités ambigus nécessitent davantage de balises. Les performances dépendent également de la qualité de votre étiquetage. Le nombre recommandé d’instances étiquetées par entité est 50.

## <a name="training-is-taking-a-long-time-is-this-expected"></a>L’entraînement prend beaucoup de temps, est-ce normal ?

Le processus d’apprentissage peut prendre beaucoup de temps. Le temps d’apprentissage approximatif attendu pour des fichiers d’une longueur combinée de 12,8 millions de caractères est de 6 heures.

## <a name="how-do-i-build-my-custom-model-programmatically"></a>Comment faire pour générer mon modèle personnalisé par programmation ?

Vous pouvez utiliser les [API REST](https://aka.ms/ct-authoring-swagger) pour générer vos modèles personnalisés. Suivez ce [démarrage rapide](quickstart.md?pivots=rest-api) pour commencer à créer un projet et un modèle à l’aide d’API, afin obtenir des exemples d’appel de l’API de création.

## <a name="what-is-the-recommended-cicd-process"></a>Quel est le processus de CI/CD recommandé ?

Vous pouvez effectuer l’apprentissage de plusieurs modèles sur le même jeu de données au sein du même projet. Une fois que vous avez correctement effectué l’apprentissage de votre modèle, vous pouvez [afficher son évaluation](how-to/view-model-evaluation.md). Vous pouvez [déployer et tester](quickstart.md#deploy-your-model) votre modèle dans [Language Studio](https://aka.ms/languageStudio). Vous pouvez ajouter ou supprimer des étiquettes de données, puis effectuer l’apprentissage d’un **nouveau** modèle et le tester. Pour connaître le nombre maximal de modèles formés avec le même projet, consultez les [limites de service](service-limits.md). Lorsque vous effectuez l’apprentissage d’un nouveau modèle, votre jeu de données est [fractionné](how-to/train-model.md#data-split) de façon aléatoire en jeux d’apprentissage et de test. Il n’est donc pas garanti que l’évaluation du modèle reflétée a trait au même jeu de test, et les résultats ne sont pas comparables. Nous vous recommandons de développer votre propre jeu de test et de l’utiliser pour évaluer les deux modèles afin de pouvoir mesurer l’amélioration.

## <a name="does-a-low-or-high-model-score-guarantee-bad-or-good-performance-in-production"></a>Un score de modèle faible ou élevé garantit-il de mauvaises ou bonnes performances en production ?

L’évaluation du modèle n’est pas toujours complète. Cela dépend de ce qui suit :
* Si le **jeu de test** est trop petit, les scores, bons ou mauvais, ne sont pas représentatifs des performances réelles du modèle. De même, si un type d’entité est manquant ou sous-représenté dans votre jeu de test, cela affecte les performances du modèle.
* **Diversité des données** Si vos données ne couvrent que peu de scénarios/exemples de texte que vous attendez en production, votre modèle n’est pas exposé à tous les scénarios possibles et peut s’exécuter de façon médiocre sur les scénarios sur lesquels il n’a pas été formé.
* **Représentation des données** Si le jeu de données utilisé pour effectuer l’apprentissage du modèle n’est pas représentatif des données qui seraient introduites dans le modèle en production, cela affectera considérablement les performances du modèle.

Pour plus d’informations, consultez l’article [Sélection de données et conception de schéma](how-to/design-schema.md).

## <a name="how-do-i-improve-model-performance"></a>Comment faire pour améliorer les performances du modèle ?

* Consultez la [matrice de confusion](how-to/view-model-evaluation.md) du modèle. Si vous remarquez qu’un certain type d’entité n’est souvent pas prédit correctement, envisagez d’ajouter des instances étiquetées pour cette classe. Si vous remarquez que deux types d’entité sont souvent prédits l’un pour l’autre, cela signifie que le schéma est ambigu et que vous devez envisager de les fusionner dans un type d’entité afin d’obtenir de meilleures performances.

* [Examinez la distribution des données](how-to/improve-model.md#examine-data-distribution). Si un type d’entité comprend beaucoup plus d’instances étiquetées que les autres, il se peut que votre modèle soit biaisé vers ce type d’entité. Ajoutez des données aux autres types d’entité ou supprimez des exemples du type dominant.

* Pour en savoir plus, consultez [Sélection de données et conception de schéma](how-to/design-schema.md).

* [Examinez votre jeu de test](how-to/improve-model.md) pour voir les entités prédites et étiquetées côte à côte afin d’obtenir une meilleure idée des performances de votre modèle et de déterminer si des modifications sont nécessaires dans le schéma ou les étiquettes.

## <a name="why-do-i-get-different-results-when-i-retrain-my-model"></a>Pourquoi est-ce que j’obtiens des résultats différents lorsque je ré-effectue l’apprentissage de mon modèle ?

* Lorsque vous effectuez l’apprentissage d’un nouveau modèle, votre jeu de données est [fractionné](how-to/train-model.md#data-split) de façon aléatoire en jeux d’apprentissage et de test. Il n’est donc pas garanti que l’évaluation du modèle reflétée a trait au même jeu de test, et les résultats ne sont pas comparables.

* Si vous ré-effectuez l’apprentissage du même modèle, votre jeu de test sera le même, mais vous constaterez peut-être une légère modification des prédictions effectuées par le modèle. Cela est dû au fait que le modèle formé n’est pas suffisamment robuste, et constitue un facteur indiquant la façon dont les données sont représentatives et distinctes, ainsi que la qualité de vos données étiquetées.

## <a name="how-do-i-get-predictions-in-different-languages"></a>Comment obtenir des prédictions dans différentes langues ?

Tout d’abord, vous devez activer l’option multilingue lors de la [création de votre projet](how-to/create-project.md). Vous pouvez également l’activer ultérieurement à partir de la page des paramètres du projet. Après avoir effectué l’apprentissage de votre modèle, puis l’avoir déployé, vous pouvez commencer à l’interroger dans [plusieurs langues](language-support.md#multiple-language-support). Les résultats peuvent varier d’une langue à l’autre. Pour améliorer la précision d’une langue quelconque, ajoutez des instances étiquetées à votre projet afin d’élargir la syntaxe du modèle dans cette langue.

## <a name="i-trained-my-model-but-i-cant-test-it"></a>J’ai entraîné mon modèle, mais je ne peux pas le tester

Vous devez [déployer votre modèle](quickstart.md#deploy-your-model) avant de pouvoir le tester. 

## <a name="how-do-i-use-my-trained-model-for-the-prediction-api"></a>Comment faire utiliser mon modèle formé pour l’API de prédiction ?

Après avoir déployé votre modèle, vous [appelez l’API de prédiction](how-to/call-api.md). Pour plus d’informations, consultez la [Référence de l’API de prédiction](https://aka.ms/ct-runtime-swagger).

## <a name="data-privacy-and-security"></a>Sécurité et confidentialité des données

La reconnaissance d’entité nommée personnalisée (NER) est un processeur de données en lien avec le Règlement général sur la protection des données (RGPD). Conformément au RGPD, les utilisateurs de NER disposent d’un contrôle total sur l’affichage, l’exportation ou la suppression de tout contenu utilisateur via le [Language Studio](https://aka.ms/languageStudio) ou par programme à l’aide d’[API REST](https://aka.ms/ct-authoring-swagger).

Vos données sont stockées uniquement dans votre compte Stockage Azure. La NER personnalisée n’a accès à leur lecture qu’au cours de l’apprentissage.

## <a name="how-to-clone-my-project"></a>Comment faire pour cloner mon projet ?

Pour cloner votre projet, vous devez utiliser l’API d’exportation pour exporter les ressources du projet, puis les importer dans un nouveau projet. Pour les deux opérations, consultez la référence de l’[API REST](https://aka.ms/ct-authoring-swagger).

## <a name="next-steps"></a>Étapes suivantes

* [Vue d’ensemble de NER personnalisée](overview.md)
* [Démarrage rapide](quickstart.md)
