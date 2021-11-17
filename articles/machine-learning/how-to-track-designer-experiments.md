---
title: Journaliser des métriques dans le concepteur
titleSuffix: Azure Machine Learning
description: Surveillez les expériences de votre concepteur Azure ML. Activez la journalisation avec le composant Exécuter un script Python et visualisez les résultats journalisés dans le studio.
services: machine-learning
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.service: machine-learning
ms.subservice: core
ms.date: 10/21/2021
ms.topic: how-to
ms.custom: designer
ms.openlocfilehash: ca78f86734c29e0aa6104e43ae759b666df6d290
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131560736"
---
# <a name="enable-logging-in-azure-machine-learning-designer-pipelines"></a>Activer la journalisation dans les pipelines du concepteur Azure Machine Learning


Dans cet article, vous allez apprendre à ajouter du code de journalisation aux pipelines du concepteur. Vous allez également apprendre à afficher ces journaux à l’aide du portail web de Azure Machine Learning Studio.

Pour plus d’informations sur la journalisation des métriques à l’aide de l’expérience de création du Kit de développement logiciel (SDK), consultez [Surveiller les exécutions et les métriques des expériences Azure Machine Learning](how-to-log-view-metrics.md).

## <a name="enable-logging-with-execute-python-script"></a>Activer la journalisation avec Exécuter un script Python

Utilisez le composant [Exécuter un script Python](./algorithm-module-reference/execute-python-script.md) pour activer la journalisation dans les pipelines du concepteur. Bien qu’il soit possible de consigner n’importe quelle valeur avec ce flux de travail, il est particulièrement utile de journaliser les métriques du composant __Évaluer un modèle__ pour effectuer un suivi du niveau de performance du modèle dans les différentes exécutions.

L’exemple suivant montre comment journaliser l’erreur carrée moyenne de deux modèles entraînés avec les composants Évaluer un modèle et Exécuter un script Python.

1. Connectez un composant __Exécuter un script Python__ à la sortie du composant __Évaluer un modèle__.

    ![Connexion du composant Exécuter un script Python au composant Évaluer un modèle](./media/how-to-log-view-metrics/designer-logging-pipeline.png)

1. Collez le code suivant dans l’éditeur de code __Exécuter un script Python__ pour journaliser l’erreur absolue moyenne de votre modèle formé : Vous pouvez utiliser un modèle similaire pour journaliser toute autre valeur dans le concepteur :

    ```python
    # dataframe1 contains the values from Evaluate Model
    def azureml_main(dataframe1=None, dataframe2=None):
        print(f'Input pandas.DataFrame #1: {dataframe1}')
    
        from azureml.core import Run
    
        run = Run.get_context()
    
        # Log the mean absolute error to the parent run to see the metric in the run details page.
        # Note: 'run.parent.log()' should not be called multiple times because of performance issues.
        # If repeated calls are necessary, cache 'run.parent' as a local variable and call 'log()' on that variable.
        parent_run = Run.get_context().parent
        
        # Log left output port result of Evaluate Model. This also works when evaluate only 1 model.
        parent_run.log(name='Mean_Absolute_Error (left port)', value=dataframe1['Mean_Absolute_Error'][0])
        # Log right output port result of Evaluate Model. The following line should be deleted if you only connect one Score component to the` left port of Evaluate Model component.
        parent_run.log(name='Mean_Absolute_Error (right port)', value=dataframe1['Mean_Absolute_Error'][1])

        return dataframe1,
    ```
    
Ce code utilise le Kit de développement logiciel (SDK) Python d’Azure Machine Learning pour journaliser des valeurs. Il utilise Run. get_context() pour récupérer le contexte de l’exécution actuelle. Il journalise ensuite les valeurs dans ce contexte à l’aide de la méthode run.parent.log(). Il utilise `parent` pour journaliser les valeurs dans l’exécution de pipeline parente plutôt que dans l’exécution du composant.

Pour plus d’informations sur l’utilisation du Kit de développement logiciel (SDK) Python pour journaliser des valeurs, consultez [Activer la journalisation dans les exécutions de formation Azure Machine Learning](how-to-log-view-metrics.md).

## <a name="view-logs"></a>Afficher les journaux d’activité

Une fois l’exécution du pipeline terminée, vous pouvez voir l’erreur *Mean_Absolute_Error* dans la page Expériences.

1. Accédez à la section **Expériences**.
1. Sélectionner votre expérience.
1. Sélectionnez l’exécution dans votre expérience que vous souhaitez afficher.
1. Sélectionnez **Métriques**.

    ![Afficher les métriques d’exécution dans le studio](./media/how-to-log-view-metrics/experiment-page-metrics-across-runs.png)

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à utiliser des journaux dans le concepteur. Pour connaître les étapes suivantes, consultez les articles connexes :


* Découvrez comment dépanner des pipelines de concepteur en consultant [Déboguer et dépanner des pipelines ML](how-to-debug-pipelines.md#azure-machine-learning-designer).
* Découvrez comment utiliser le Kit de développement logiciel (SDK) Python pour journaliser des métriques dans l’expérience de création du Kit de développement logiciel (SDK). pour plus d’informations en consultant [Activer la journalisation dans les exécutions de formation Azure Machine Learning](how-to-log-view-metrics.md).
* Découvrez comment utiliser le module [Exécuter un script Python](./algorithm-module-reference/execute-python-script.md) dans le concepteur.
