---
title: Qu’est-ce que le concepteur Azure Machine Learning ?
titleSuffix: Azure Machine Learning
description: Découvrez le concepteur Azure Machine Learning et les tâches pour lesquelles vous pouvez l’utiliser. L’interface utilisateur par glisser-déposer permet d’entraîner et de déployer un modèle.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: peterlu
author: peterclu
ms.date: 10/21/2021
ms.custom: designer, FY21Q4-aml-seo-hack, contperf-fy21q4
ms.openlocfilehash: 07132095c3f64aa4df3f6ec728894a625bac2b74
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131561704"
---
# <a name="what-is-azure-machine-learning-designer"></a>Qu'est-ce que le concepteur Azure Machine Learning ? 

Le concepteur Azure Machine Learning est une interface par glisser-déposer utilisée pour entraîner et déployer des modèles dans Azure Machine Learning. Cet article décrit les tâches que vous pouvez effectuer dans le concepteur.

 - Pour commencer avec le concepteur, consultez [Tutoriel : Entraîner un modèle de régression sans code](tutorial-designer-automobile-price-train-score.md). 
 - Pour découvrir les composants disponibles dans le concepteur, consultez les [informations de référence sur les algorithmes et les composants](./algorithm-module-reference/module-reference.md).

![Exemple de concepteur Azure Machine Learning](./media/concept-designer/designer-drag-and-drop.gif)

Le concepteur utilise votre [espace de travail](concept-workspace.md) Azure Machine Learning pour organiser des ressources partagées telles que :

+ [Pipelines](#pipeline)
+ [Groupes de données](#datasets)
+ [Ressources de calcul](#compute)
+ [Modèles inscrits](concept-azure-machine-learning-architecture.md#models)
+ [Pipelines publiés](#publish)
+ [Points de terminaison en temps réel](#deploy)

## <a name="model-training-and-deployment"></a>Formation et déploiement du modèle

Utilisez un canevas visuel pour créer un workflow Machine Learning de bout en bout. Entraînez, testez et déployez des modèles, le tout dans le concepteur :

+ Glisser-déplacer les [jeux de données](#datasets) et les [composants](#component) sur le canevas.
+ Connecter les composants pour former un [brouillon de pipeline](#pipeline-draft).
+ Envoyer une [exécution de pipeline](#pipeline-run) à l’aide des ressources de calcul de votre espace de travail Azure Machine Learning.
+ Convertir vos **pipelines de formation** en **pipelines d’inférence**.
+ [Publier](#publish) vos pipelines sur un **point de terminaison de pipeline** REST pour envoyer un nouveau pipeline qui s’exécute avec des paramètres et des jeux de données différents.
    + Publier un **pipeline de formation** pour réutiliser un pipeline unique afin d’effectuer l’apprentissage de plusieurs modèles tout en modifiant les paramètres et les jeux de données.
    + Publier un **pipeline d’inférence par lot** pour effectuer des prédictions sur de nouvelles données à l’aide d’un modèle préalablement formé.
1. [Déployer](#deploy) un **pipeline d’inférence en temps réel** vers un point de terminaison en temps réel pour effectuer des prédictions sur de nouvelles données en temps réel.

![Diagramme de flux de travail pour la formation, l’inférence par lot et l’inférence en temps réel dans le concepteur](./media/concept-designer/designer-workflow-diagram.png)

## <a name="pipeline"></a>Pipeline

Un [pipeline](concept-azure-machine-learning-architecture.md#ml-pipelines) se compose de jeux de données et de composants d’analyse qui sont connectés. Les pipelines ont de nombreuses utilisations : vous pouvez créer un pipeline qui effectue l’apprentissage d’un modèle unique ou un pipeline qui effectue l’apprentissage de plusieurs modèles. Vous pouvez créer un pipeline qui réalise des prédictions en temps réel ou par lot ou un pipeline qui nettoie uniquement les données. Les pipelines vous permettent de réutiliser votre travail et d’organiser vos projets.

### <a name="pipeline-draft"></a>Brouillon de pipeline

Lorsque vous modifiez un pipeline dans le concepteur, votre progression est enregistrée en tant que **brouillon de pipeline**. Vous pouvez modifier un brouillon de pipeline à tout moment en ajoutant ou en supprimant des composants, en configurant des cibles de calcul, en créant des paramètres et ainsi de suite.

Un pipeline valide a les caractéristiques suivantes :

* Les jeux de données peuvent uniquement se connecter à des composants.
* Les composants peuvent uniquement se connecter à des jeux de données ou à d’autres composants.
* Tous les ports d’entrée des composants doivent comporter une connexion au flux de données.
* Tous les paramètres obligatoires de chaque composant doivent être configurés.

Lorsque vous êtes prêt à exécuter votre brouillon de pipeline, vous soumettez une exécution de pipeline.

### <a name="pipeline-run"></a>Exécution du pipeline

Chaque fois que vous exécutez un pipeline, la configuration du pipeline et ses résultats sont stockés dans votre espace de travail sous la forme d’une **exécution de pipeline**. Vous pouvez revenir à n’importe quelle exécution de pipeline pour l’inspecter à des fins de résolution de problèmes ou d’audit. **Clonez** une exécution de pipeline pour créer un brouillon de pipeline que vous pouvez modifier.

Les exécutions de pipeline sont regroupées en [expériences](concept-azure-machine-learning-architecture.md#experiments) afin d’organiser l’historique des exécutions. Vous pouvez définir l’expérience pour chaque exécution de pipeline. 

## <a name="datasets"></a>Groupes de données

Un jeu de données Machine Learning facilite l’accès aux données et l’utilisation de ces dernières. Plusieurs [exemples de jeux de données](samples-designer.md#datasets) sont inclus dans le concepteur à des fins de test. Vous pouvez [inscrire](how-to-create-register-datasets.md) plusieurs jeux de données en fonction de vos besoins.

## <a name="component"></a>Composant

Un composant est un algorithme que vous appliquez à vos données. Le concepteur comporte plusieurs composants, allant de fonctions d’entrée des données à des processus d’entraînement, de scoring et de validation.

Un composant peut comporter un ensemble de paramètres utilisables pour configurer les algorithmes internes du composant. Quand vous sélectionnez un composant dans le canevas, ses paramètres sont affichés dans le volet Propriétés à droite du canevas. Vous pouvez modifier les paramètres figurant dans ce volet pour affiner votre modèle. Vous pouvez définir les ressources de calcul pour des composants individuels dans le concepteur. 

:::image type="content" source="./media/concept-designer/properties.png" alt-text="Propriétés de composant":::


Pour obtenir de l’aide sur la navigation dans la bibliothèque des algorithmes d’apprentissage automatique disponibles, consultez [Vue d’ensemble des informations de référence relatives aux algorithmes et aux composants](component-reference/component-reference.md). Pour obtenir de l’aide sur le choix d’un algorithme, consultez [Aide-mémoire de l’algorithme Machine Learning](algorithm-cheat-sheet.md).

## <a name="compute-resources"></a><a name="compute"></a> Ressources de calcul

Utilisez les ressources de calcul de votre espace de travail pour exécuter votre pipeline et héberger vos modèles déployés en tant que points de terminaison en temps réel ou points de terminaison de pipeline (pour l’inférence de lot). Les cibles de calcul prises en charge sont les suivantes :

| Cible de calcul | Entrainement | Déploiement |
| ---- |:----:|:----:|
| Capacité de calcul Azure Machine Learning | ✓ | |
| Azure Kubernetes Service | | ✓ |

Les cibles de calcul sont associées à votre [espace de travail Azure Machine Learning](concept-workspace.md). Vous gérez vos cibles de calcul dans votre espace de travail dans le [studio Azure Machine Learning](https://ml.azure.com).

## <a name="deploy"></a>Déployer

Pour effectuer une inférence en temps réel, vous devez déployer un pipeline en tant que **point de terminaison en temps réel**. Le point de terminaison en temps réel crée une interface entre une application externe et votre modèle de scoring. Un appel à un point de terminaison en temps réel renvoie les résultats de prédiction à l’application en temps réel. Pour générer un appel vers un point de terminaison en temps réel, vous transmettez la clé API créée au moment du déploiement du point de terminaison. Le point de terminaison s’appuie sur l’architecture populaire REST, souvent choisie pour les projets de programmation web.

Les points de terminaison en temps réel doivent être déployés sur un cluster Azure Kubernetes Service.

Pour savoir comment déployer votre modèle, consultez [Tutoriel : Déployez un modèle Machine Learning avec le concepteur](tutorial-designer-automobile-price-deploy.md).

[!INCLUDE [endpoints-option](../../includes/machine-learning-endpoints-preview-note.md)]

## <a name="publish"></a>Publish

Vous pouvez également publier un pipeline sur un **point de terminaison de pipeline**. Semblable à un point de terminaison en temps réel, un point de terminaison de pipeline vous permet d’envoyer de nouvelles exécutions de pipeline à partir d’applications externes à l’aide d’appels REST. Toutefois, vous ne pouvez pas envoyer ni recevoir de données en temps réel à l’aide d’un point de terminaison de pipeline.

Les pipelines publiés sont flexibles ; ils peuvent être utilisés pour effectuer ou réeffectuer l’entraînement de modèles, [effectuer des inférences par lot](how-to-run-batch-predictions-designer.md), traiter de nouvelles données et bien plus encore. Vous pouvez publier plusieurs pipelines sur un point de terminaison de pipeline unique et spécifier la version de pipeline à exécuter.

Un pipeline publié s’exécute sur les ressources de calcul que vous définissez dans le brouillon de pipeline pour chaque composant.

Le concepteur crée le même objet [PublishedPipeline](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.publishedpipeline) que le Kit de développement logiciel (SDK).

## <a name="next-steps"></a>Étapes suivantes

* Découvrez les principes fondamentaux de l’analytique prédictive et du Machine Learning à l’aide du [Tutoriel : Prédire le prix de voitures avec le concepteur](tutorial-designer-automobile-price-train-score.md)
* Découvrez comment modifier les [exemples de concepteur](samples-designer.md) pour les adapter à vos besoins.