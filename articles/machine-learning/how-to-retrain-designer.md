---
title: Utiliser des paramètres de pipeline pour réentraîner des modèles dans le concepteur
titleSuffix: Azure Machine Learning
description: Réentraînez des modèles avec des pipelines publiés et des paramètres de pipeline dans le concepteur Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: mlops
ms.author: keli19
author: likebupt
ms.date: 10/21/2021
ms.topic: how-to
ms.custom: designer
ms.openlocfilehash: 6b79b80f666019cb5a8a59901bf12e9277287a12
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131557411"
---
# <a name="use-pipeline-parameters-to-retrain-models-in-the-designer"></a>Utiliser des paramètres de pipeline pour réentraîner des modèles dans le concepteur


Ce guide pratique explique comment utiliser le concepteur Azure Machine Learning pour réentraîner un modèle Machine Learning à l’aide de paramètres de pipeline. Vous allez utiliser des pipelines publiés pour automatiser votre flux de travail et définir des paramètres pour effectuer l’apprentissage de votre modèle sur de nouvelles données. Les paramètres de pipeline vous permettent de réutiliser des pipelines existants pour différents travaux.  

Dans cet article, vous apprendrez comment :

> [!div class="checklist"]
> * Entraîner un modèle Machine Learning
> * Créer un paramètre de pipeline
> * Publier le pipeline d’entraînement
> * Ré-effectuer l’apprentissage de votre modèle avec de nouveaux paramètres.

## <a name="prerequisites"></a>Prérequis

* Espace de travail Azure Machine Learning
* Complétez la partie 1 de cette série de procédures, [Transformer des données dans le concepteur](how-to-designer-transform-data.md).

[!INCLUDE [machine-learning-missing-ui](../../includes/machine-learning-missing-ui.md)]

Cet article présuppose également que vous disposez de connaissances en matière de création de pipelines dans le concepteur. Pour une présentation guidée, suivez le [tutoriel](tutorial-designer-automobile-price-train-score.md). 

### <a name="sample-pipeline"></a>Exemple de pipeline

Le pipeline utilisé dans cet article est une version modifiée d’un exemple de pipeline [Prédiction de revenus](samples-designer.md#classification) dans la page d’accueil du concepteur. Le pipeline utilise le composant [Importer des données](algorithm-module-reference/import-data.md) plutôt que l’exemple de jeu de données pour vous montrer comment effectuer l’apprentissage d’un modèle avec vos propres données.

![Capture d’écran montrant l’exemple de pipeline modifié avec le composant Importer des données encadré](./media/how-to-retrain-designer/modified-sample-pipeline.png)

## <a name="create-a-pipeline-parameter"></a>Créer un paramètre de pipeline

Les paramètres de pipeline sont utilisés pour créer des pipelines polyvalents qui peuvent par la suite être soumis de nouveau avec des valeurs de paramètre variables. Parmi les scénarios courants, on peut citer la mise à jour de jeux de données ou de certains hyperparamètres dans le cadre d’un réentraînement. Créez des paramètres de pipeline pour définir des variables de façon dynamique lors du runtime. 

Des paramètres peuvent être ajoutés aux paramètres de source de données ou de composant d’un pipeline. Lorsque le pipeline est soumis de nouveau, les valeurs de ces paramètres peuvent être spécifiées.

Pour cet exemple, vous allez convertir le chemin d’accès aux données d’apprentissage d’une valeur fixe en un paramètre, afin de pouvoir ré-effectuer l’apprentissage de votre modèle sur des données différentes. Vous pouvez également ajouter d’autres paramètres de composant comme paramètres de pipeline en fonction de votre cas d’usage.

1. Sélectionnez le composant **Importer des données**.

    > [!NOTE]
    > Cet exemple utilise le composant Importer des données pour accéder aux données d’un magasin de données inscrit. Vous cependant pouvez suivre une procédure similaire si vous utilisez d’autres modèles d’accès aux données.

1. Dans le volet Détails du composant, à droite du canevas, sélectionnez votre source de données.

1. Entrez le chemin de vos données. Vous pouvez également sélectionner **Parcourir le chemin** pour accéder à votre arborescence de fichiers. 

1. Pointez la souris sur le champ **Chemin d’accès** et sélectionnez les points de suspension situés au-dessus du champ **Chemin d’accès** qui s’affiche.

1. Sélectionnez **Ajouter au paramètre de pipeline**.

1. Indiquez un nom de paramètre et une valeur par défaut.

   ![Capture d’écran montrant comment créer un paramètre de pipeline](media/how-to-retrain-designer/add-pipeline-parameter.png)

1. Sélectionnez **Enregistrer**.

   > [!NOTE]
   > Vous pouvez également détacher un paramètre de composant du paramètre de pipeline dans le volet Détails du composant, comme pour l’ajout de paramètres de pipeline.
   >
   > Vous pouvez examiner et modifier vos paramètres de pipeline en sélectionnant l’icône d’engrenage **Paramètres** à côté du titre de votre brouillon de pipeline. 
   >    - Une fois détaché, le paramètre de pipeline peut être supprimé dans le volet **Paramètres**.
   >    - Vous pouvez également ajouter un paramètre de pipeline dans le volet **Paramètres**, puis l’appliquer à un paramètre de composant.

1. Soumettez l’exécution du pipeline.

## <a name="publish-a-training-pipeline"></a>Publier un pipeline d’entraînement

Publiez un pipeline sur un point de terminaison de pipeline pour réutiliser facilement vos pipelines à l’avenir. Un point de terminaison de pipeline crée un point de terminaison REST pour appeler le pipeline à l’avenir. Dans cet exemple, votre point de terminaison de pipeline vous permet de réutiliser votre pipeline pour ré-effectuer l’apprentissage d’un modèle sur des données différentes.

1. Sélectionnez **Publier** au-dessus du canevas du concepteur.
1. Sélectionnez ou créez un point de terminaison de pipeline.

   > [!NOTE]
   > Vous pouvez publier plusieurs pipelines sur un même point de terminaison. Chaque pipeline dans un point de terminaison reçoit un numéro de version que vous pouvez spécifier lorsque vous appelez le point de terminaison du pipeline.

1. Sélectionnez **Publier**.

## <a name="retrain-your-model"></a>Réentraîner le modèle

À présente que vous disposez d’un pipeline d’apprentissage publié, vous pouvez l’utiliser pour ré-effectuer l’apprentissage de votre modèle sur de nouvelles données. Vous pouvez envoyer des exécutions à partir d’un point de terminaison de pipeline de l’espace de travail Studio ou par programmation.

### <a name="submit-runs-by-using-the-studio-portal"></a>Envoyer des exécutions à l’aide du portail du studio

Suivez les étapes suivantes pour envoyer une exécution de point de terminaison de pipeline paramétré à partir du portail du studio :

1. Accédez à la page **Points de terminaison** de votre espace de travail Studio.
1. Sélectionnez l’onglet **Points de terminaison de pipeline**. Sélectionnez votre point de terminaison de pipeline.
1. Sélectionnez l’onglet **Pipelines publiés**. Ensuite, sélectionnez la version de pipeline que vous souhaitez exécuter.
1. Sélectionnez **Envoyer**.
1. Dans la boîte de dialogue de configuration, vous pouvez spécifier les valeurs des paramètres pour l’exécution. Pour cet exemple, mettez à jour le chemin d’accès des données pour effectuer l’apprentissage de votre modèle à l’aide d’un jeu de données non US.

![Capture d’écran montrant comment configurer une exécution de pipeline paramétrable dans le concepteur](./media/how-to-retrain-designer/published-pipeline-run.png)

### <a name="submit-runs-by-using-code"></a>Envoyer des exécutions à l’aide de code

Vous trouverez le point de terminaison REST d'un pipeline publié dans le panneau de vue d'ensemble. En appelant le point de terminaison, vous pouvez réentraîner le pipeline publié.

Pour effectuer un appel REST, vous devez disposer d’un en-tête d’authentification de type porteur OAuth 2.0. Pour plus d’informations concernant la configuration de l’authentification sur votre espace de travail et l’exécution d’un appel REST paramétrable, consultez [Créer un pipeline Azure Machine Learning pour le scoring par lots](tutorial-pipeline-batch-scoring-classification.md#publish-and-run-from-a-rest-endpoint).

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à créer un point de terminaison de pipeline d’apprentissage paramétré à l’aide du concepteur.

Pour une procédure pas à pas complète de déploiement d’un modèle destiné à effectuer des prédictions, consultez le [tutoriel du concepteur](tutorial-designer-automobile-price-train-score.md) pour former et déployer un modèle de régression.

Pour savoir comment publier et envoyer un point de terminaison de pipeline à l’aide du kit de développement logiciel (SDK), consultez [cet article](how-to-deploy-pipelines.md).
