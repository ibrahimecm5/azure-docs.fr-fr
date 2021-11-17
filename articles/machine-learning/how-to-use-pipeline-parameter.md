---
title: Utiliser les paramètres des pipelines pour créer des pipelines polyvalents
titleSuffix: Azure Machine Learning
description: Découvrez comment utiliser des paramètres de pipeline dans le concepteur Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: mlops
ms.author: keli19
author: likebupt
ms.date: 10/21/2021
ms.topic: how-to
ms.custom: designer
ms.openlocfilehash: d600f7ad651b58f3cf194c8f5dace8f6dc4c7695
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131560337"
---
# <a name="use-pipeline-parameters-in-the-designer-to-build-versatile-pipelines"></a>Utiliser des paramètres de pipeline dans le concepteur pour créer des pipelines polyvalents

Utilisez des paramètres de pipeline pour créer des pipelines flexibles dans le concepteur. Les paramètres de pipeline vous permettent de définir dynamiquement des valeurs au moment de l’exécution afin d’encapsuler la logique du pipeline et de réutiliser les ressources.

Les paramètres de pipeline sont particulièrement utiles lors de la soumission d’une exécution de pipeline, du [réentraînement de modèle](how-to-retrain-designer.md) ou de l’[exécution de prédictions par lots](how-to-run-batch-predictions-designer.md).

Cet article, vous allez découvrir comment :

> [!div class="checklist"]
> * Créer des paramètres de pipeline.
> * Supprimer et gérer des paramètres de pipeline.
> * Déclencher des exécutions de pipeline lors du réglage des paramètres de pipeline.

## <a name="prerequisites"></a>Prérequis

* Un espace de travail Azure Machine Learning. Consultez [Créer un espace de travail Microsoft Azure Machine Learning](how-to-manage-workspace.md).

* Pour une présentation guidée du concepteur, suivez le [tutoriel du concepteur](tutorial-designer-automobile-price-train-score.md). 

[!INCLUDE [machine-learning-missing-ui](../../includes/machine-learning-missing-ui.md)]

## <a name="create-pipeline-parameter"></a>Créer un paramètre de pipeline

Il existe trois façons de créer un paramètre de pipeline dans le concepteur :
- Créez un paramètre de pipeline dans le panneau Paramètres et liez-le à un composant.
- Promouvoir un paramètre de composant en paramètre de pipeline.
- Promouvoir un jeu de données en paramètre de pipeline

> [!NOTE]
> Les paramètres de pipeline prennent uniquement en charge les types de données de base tels que `int`, `float` et `string`.

### <a name="option-1-create-a-pipeline-parameter-in-the-settings-panel"></a>Option 1 : Créer un paramètre de pipeline dans le panneau Paramètres

Dans cette section, vous allez créer un paramètre de pipeline dans le panneau Paramètres.

Dans cet exemple, vous créez un paramètre de pipeline qui définit comment un pipeline renseigne des données manquantes à l’aide du composant **Nettoyer les données manquantes**.

1. En regard du nom de votre brouillon de pipeline, sélectionnez l’**icône d’engrenage** pour ouvrir le panneau **Paramètres**.

1. Dans la section **Paramètres du pipeline**, sélectionnez l’icône **+** .

1.  Entrez un nom et une valeur par défaut pour le paramètre. 

    Par exemple, entrez `replace-missing-value` comme nom de paramètre et `0` comme valeur par défaut.

   ![Capture d’écran montrant comment créer un paramètre de pipeline](media/how-to-use-pipeline-parameter/create-pipeline-parameter.png)


Après avoir créé un paramètre de pipeline, vous devez [l’attacher au paramètre de composant](#attach-component-parameter-to-pipeline-parameter) que vous souhaitez définir de manière dynamique.

### <a name="option-2-promote-a-component-parameter"></a>Option 2 : Promouvoir un paramètre de composant

La façon la plus simple de créer un paramètre de pipeline pour une valeur de composant consiste à promouvoir un paramètre de composant. Pour promouvoir un paramètre de composant en paramètre de pipeline, procédez comme suit :

1. Sélectionnez le composant auquel vous souhaitez attacher un paramètre de pipeline.
1. Dans le volet de détails du composant, placez le pointeur de la souris sur le paramètre que vous souhaitez spécifier.
1. Sélectionnez les points de suspension ( **...** ) qui s’affichent.
1. Sélectionnez **Ajouter au paramètre de pipeline**.

    ![Capture d’écran montrant comment promouvoir le paramètre de composant en paramètre1 du pipeline](media/how-to-use-pipeline-parameter/promote-module-para-to-pipeline-para.png)

1. Entrez un nom de paramètre et une valeur par défaut.
1. Sélectionnez **Enregistrer**.

Vous pouvez maintenant spécifier de nouvelles valeurs pour ce paramètre à chaque fois que vous soumettez ce pipeline.

### <a name="option-3-promote-a-dataset-to-a-pipeline-parameter"></a>Option 3 : Promouvoir un jeu de données en paramètre de pipeline

Si vous souhaitez soumettre votre pipeline avec des jeux de données variables, vous devez promouvoir votre jeu de données en paramètre de pipeline :

1. Sélectionnez le jeu de données que vous souhaitez convertir en paramètre de pipeline.

1. Dans le volet de détails du jeu de données, cochez **Définir en tant que paramètre de pipeline**.

   ![Capture d’écran montrant comment définir le jeu de données en tant que paramètre de pipeline](media/how-to-use-pipeline-parameter/set-dataset-as-pipeline-parameter.png)

Vous pouvez maintenant spécifier un autre jeu de données à l’aide du paramètre de pipeline lors de la prochaine exécution du pipeline.

## <a name="attach-and-detach-component-parameter-to-pipeline-parameter"></a>Attacher un paramètre de composant à un paramètre de pipeline et l’en détacher 

Cette section explique comment attacher un paramètre de composant à un paramètre de pipeline et l’en détacher.

### <a name="attach-component-parameter-to-pipeline-parameter"></a>Attacher un paramètre de composant à un paramètre de pipeline

Vous pouvez attacher les mêmes paramètres de composant de composants dupliqués au même paramètre de pipeline si vous souhaitez modifier la valeur lors du déclenchement de l’exécution du pipeline.

L’exemple suivant comporte un composant **Nettoyer les données manquantes** dupliqué. Pour chaque composant **Nettoyer les données manquantes**, attachez la **Valeur de remplacement** au paramètre de pipeline **replace-missing-value** :

1. Sélectionnez le composant **Clean Missing Data**.

1. Dans le volet de détails du composant, à droite du canevas, définissez le **Mode de nettoyage** sur « Valeur de substitution personnalisée ».

1. Placez le pointeur de la souris sur le champ **Valeur de remplacement**.

1. Sélectionnez les points de suspension ( **...** ) qui s’affichent.

1. Sélectionnez le paramètre de pipeline `replace-missing-value`.

   ![Capture d’écran montrant comment attacher un paramètre de pipeline](media/how-to-use-pipeline-parameter/attach-replace-value-to-pipeline-parameter.png)

Vous avez correctement attaché le champ **Valeur de remplacement** à votre paramètre de pipeline. 


### <a name="detach-component-parameter-to-pipeline-parameter"></a>Détacher un paramètre de composant d’un paramètre de pipeline

Une fois que vous avez attaché la **valeur de remplacement** au paramètre de pipeline, elle n’est pas exploitable.

Vous pouvez détacher un paramètre de composant d’un paramètre de pipeline en cliquant sur les points de suspension ( **...** ) en regard du paramètre de composant, puis sélectionner **Détacher du paramètre de pipeline**.

 ![Capture d’écran montrant le caractère non-actionnable après l’attachement au paramètre de pipeline](media/how-to-use-pipeline-parameter/non-actionable-module-parameter.png)

## <a name="update-and-delete-pipeline-parameters"></a>Mettre à jour et supprimer des paramètres de pipeline

Dans cette section, vous allez découvrir comment mettre à jour et supprimer des paramètres de pipeline.

### <a name="update-pipeline-parameters"></a>Mettre à jour des paramètres de pipeline

Pour mettre à jour un paramètre de pipeline de composant, procédez comme suit :

1. En haut du canevas, sélectionnez l’icône d’engrenage.
1. Dans la section **Paramètres du pipeline**, vous pouvez afficher et mettre à jour le nom et la valeur par défaut de tous vos paramètres de pipeline.

### <a name="delete-a-dataset-pipeline-parameter"></a>Supprimer un paramètre de pipeline de jeu de données

Pour supprimer un paramètre de pipeline de jeu de données, effectuez les étapes suivantes :

1. Sélectionnez le composant Jeu de données.
1. Décochez l’option **Définir en tant que paramètre de pipeline**.


### <a name="delete-component-pipeline-parameters"></a>Supprimer des paramètres de pipeline de composant

Pour supprimer un paramètre de pipeline de composant, procédez comme suit :

1. En haut du canevas, sélectionnez l’icône d’engrenage.

1. Sélectionnez les points de suspension ( **...** ) en regard du paramètre de pipeline.

    Cet affichage montre les composants auxquels le paramètre de pipeline est attaché.

    ![Capture d’écran montrant le paramètre de pipeline actif appliqué à un composant](media/how-to-use-pipeline-parameter/delete-pipeline-parameter2.png)

1. Sélectionnez **Supprimer le paramètre** pour supprimer le paramètre de pipeline.

    > [!NOTE]
    > La suppression d’un paramètre de pipeline entraîne le détachement de tous les paramètres de composant attachés, et la valeur des paramètres de composant détachés conservera la valeur actuelle du paramètre de pipeline.     

## <a name="trigger-a-pipeline-run-with-pipeline-parameters"></a>Déclencher une exécution de pipeline avec des paramètres de pipeline 

Dans cette section, vous allez découvrir comment soumettre une exécution de pipeline lors de la définition de paramètres de pipeline.

### <a name="resubmit-a-pipeline-run"></a>Resoumettre une exécution de pipeline

Après avoir soumis un pipeline avec des paramètres de pipeline, vous pouvez resoumettre une exécution de pipeline avec des paramètres différents :

1. Accédez à la page de détails du pipeline. Dans la fenêtre **Vue d’ensemble de l’exécution du pipeline**, vous pouvez vérifier les paramètres et les valeurs du pipeline actif.

1. Sélectionnez **Soumettre à nouveau**.
1. Dans **Configurer l’exécution de pipeline**, spécifiez vos nouveaux paramètres de pipeline. 

![Capture d’écran montrant la nouvelle soumission de pipeline avec des paramètres de pipeline](media/how-to-use-pipeline-parameter/resubmit-pipeline-run.png)

### <a name="use-published-pipelines"></a>Utiliser des pipelines publiés

Vous pouvez également publier un pipeline afin d’utiliser ses paramètres de pipeline. Un **pipeline publié** est un pipeline qui a été déployé sur une ressource de calcul, que les applications clientes peuvent appeler par le biais d’un point de terminaison REST.

Les points de terminaison publiés sont particulièrement utiles pour les scénarios de réentraînement et de prédiction par lot. Pour plus d’informations, consultez [Guide pratique pour réentraîner des modèles dans le concepteur](how-to-retrain-designer.md) ou [Exécuter des prédictions par lots dans le concepteur](how-to-run-batch-predictions-designer.md).

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez découvert comment créer des paramètres de pipeline dans le concepteur. Maintenant, découvrez comment utiliser des paramètres de pipeline pour [réentraîner des modèles](how-to-retrain-designer.md) ou effectuer des [prédictions par lots](how-to-run-batch-predictions-designer.md).

Vous pouvez également découvrir comment [utiliser des pipelines par programmation avec le kit SDK](how-to-deploy-pipelines.md).
