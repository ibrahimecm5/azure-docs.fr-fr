---
title: Transformation de données dans le concepteur
titleSuffix: Azure Machine Learning
description: Découvrez comment importer et transformer des données dans le concepteur Azure Machine Learning pour créer vos propres jeux de données.
services: machine-learning
ms.service: machine-learning
ms.subservice: mldata
author: lgayhardt
ms.author: lagayhar
ms.date: 10/21/2021
ms.topic: how-to
ms.custom: designer
ms.openlocfilehash: 42f70e55bf8c2e8b8d6186d26f5a3a1ba08f6a63
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131557449"
---
# <a name="transform-data-in-azure-machine-learning-designer"></a>Transformer des données dans le concepteur Azure Machine Learning


Cet article explique comment transformer et enregistrer des jeux de données dans le concepteur Azure Machine Learning afin de pouvoir préparer vos propres données afin d’effectuer un apprentissage automatique (Machine Learning).

Vous allez utiliser l’exemple de jeu de données [Adult Census Income Binary Classification](./samples-designer.md) pour préparer deux jeux de données : l’un contenant des informations de recensement d’adultes natifs des USA, et l’autre contenant des informations de recensement d’adultes non natifs des USA.

Dans cet article, vous apprendrez comment :

1. Transformer un jeu de données pour le préparer en vue d’un apprentissage.
1. Exporter les jeux de données obtenus vers un magasin de données.
1. Afficher les résultats.

Nous vous recommandons de lire cet article avant l’article sur le [recyclage de modèles de concepteur](how-to-retrain-designer.md). Cet article explique comment utiliser les jeux de données transformés pour effectuer l’apprentissage de plusieurs modèles avec des paramètres de pipeline.

[!INCLUDE [machine-learning-missing-ui](../../includes/machine-learning-missing-ui.md)]

## <a name="transform-a-dataset"></a>Transformer un jeu de données

Cette section explique comment importer l’exemple de jeu de données et fractionner les données en deux jeux de données, US et non-US. Pour plus d’informations sur l’importation de vos propres données dans le concepteur, consultez [Comment importer des données](how-to-designer-import-data.md).

### <a name="import-data"></a>Importer des données

Procédez comme suit pour importer l’exemple de jeu de données.

1. Connectez-vous à <a href="https://ml.azure.com?tabs=jre" target="_blank">ml.azure.com</a>, puis sélectionnez l’espace de travail à utiliser.

1. Accédez au concepteur. Sélectionnez **Composants prédéfinis faciles à utiliser** pour créer un pipeline.

1. Sélectionnez une cible de calcul par défaut pour exécuter le pipeline.

1. À gauche du canevas du pipeline se trouve une palette de jeux de données et de composants. Sélectionnez **Jeux de données**. Affichez ensuite la section **Exemples**.

1. Glisser-déposez le jeu de données de **Adult Census Income Binary classification** sur le canevas.

1. Cliquez avec le bouton droit sur le composant du jeu de données **Adult Census Income**, puis sélectionnez **Visualiser** > **Sortie du jeu de données**.

1. Utilisez la fenêtre d’aperçu des données pour explorer le jeu de données. Notez en particulier les valeurs de la colonne « native-country ».

### <a name="split-the-data"></a>Fractionner les données

Dans cette section, vous allez utiliser le [composant Split Data](algorithm-module-reference/split-data.md) (Fractionner les données) pour identifier et fractionner les lignes contenant « United-States » dans la colonne « native-country ». 

1. Dans la palette de composants à gauche du canevas, développez la section **Data Transformation** (Transformation des données) et recherchez le composant **Split Data**.

1. Faites glisser le composant **Split Data** sur le canevas, puis déposez le composant sous le composant du jeu de données.

1. Connectez le composant du jeu de données au composant **Split Data**.

1. Sélectionnez le composant **Split Data**.

1. Dans le volet d’informations du composant à droite du canevas, définissez **Splitting mode** (Mode de fractionnement) sur **Regular Expression** (Expression régulière).

1. Entrez l’**expression régulière** : `\"native-country" United-States`.

    Le mode **Regular expression** (Expression régulière) teste une seule colonne pour une valeur. Pour plus d’informations sur le composant Split Data, consultez la [page de référence du composant d’algorithme](algorithm-module-reference/split-data.md) associée.

Votre pipeline doit se présenter comme suit :

:::image type="content" source="./media/how-to-designer-transform-data/split-data.png" alt-text="Capture d’écran montrant comment configurer le pipeline et le composant Split Data":::


## <a name="save-the-datasets"></a>Enregistrer les jeux de données

Maintenant que votre pipeline est configuré pour fractionner les données, vous devez spécifier où conserver les jeux de données. Pour cet exemple, utilisez le composant **Export Data** (Exporter des données) pour enregistrer votre jeu de données dans un magasin de données. Pour plus d’informations sur les magasins de données, consultez [Se connecter aux services de stockage Azure](how-to-access-data.md).

1. Dans la palette de composants à gauche du canevas, développez la section **Data Input and Output** (Entrée et sortie de données), puis recherchez le composant **Export Data**.

1. Faites glisser-déposer deux composants **Export Data** sous le composant **Split Data**.

1. Connectez chaque port de sortie du composant **Split Data** à un composant **Export Data** différent.

    Votre pipeline doit maintenant se présenter comme ceci :

    ![Capture d’écran montrant comment connecter les composants Export Data](media/how-to-designer-transform-data/export-data-pipeline.png).

1. Sélectionnez le composant **Export Data** connecté au port le plus à *gauche* du composant **Split Data**.

    L’ordre des ports de sortie a de l’importance pour le composant **Split Data**. Le premier port de sortie contient les lignes où l’expression régulière a la valeur true. Dans ce cas, le premier port contient des lignes pour les revenus aux USA et le deuxième pour les revenus hors USA.

1. Dans le volet d’informations du composant à droite du canevas, définissez les options suivantes :
    
    **Datastore type** (Type de magasin de données) : Stockage Blob Azure

    **Banque de données** : Sélectionnez un magasin de données existant ou sélectionnez « Nouveau magasin de banques » pour en créer un maintenant.

    **Path** (Chemin d’accès) : `/data/us-income`

    **File format** (Format de fichier ) : csv

    > [!NOTE]
    > Cet article part du principe que vous avez accès à un magasin de données inscrit dans l’espace de travail Azure Machine Learning actuel. Pour obtenir des instructions sur la configuration d’un magasin de données, consultez [Se connecter aux services de stockage Azure](how-to-connect-data-ui.md#create-datastores).

    Si vous n’avez pas de magasin de banques, vous pouvez en créer un maintenant. À titre d’exemple, cet article enregistre les jeux de données dans le compte de stockage d’objets blob par défaut associé à l’espace de travail. Il enregistre les jeux de données dans le conteneur `azureml` dans un nouveau dossier nommé `data`.

1.  Sélectionnez le composant **Export Data** connecté au port le plus à *droite* du composant **Split Data**.

1. Dans le volet d’informations du composant à droite du canevas, définissez les options suivantes :
    
    **Datastore type** (Type de magasin de données) : Stockage Blob Azure

    **Banque de données** : Sélectionnez le même magasin de données comme ci-dessus

    **Path** (Chemin) : `/data/non-us-income`

    **File format** (Format de fichier ) : csv

1. Vérifiez que le composant **Export Data** connecté au port de gauche de **Split Data** contient le **Path** (Chemin) `/data/us-income`.

1. Vérifiez que le composant **Export Data** connecté au port de droite contient le **Path** `/data/non-us-income`.

    Votre pipeline et vos paramètres doivent ressembler à ceci :
    
    ![Capture d’écran montrant comment configurer les composants Export Data](media/how-to-designer-transform-data/us-income-export-data.png).

### <a name="submit-the-run"></a>Soumettre l’exécution

Maintenant que votre pipeline est configuré pour fractionner et exporter les données, soumettez une exécution de pipeline.

1. En haut du canevas, sélectionnez **Envoyer**.

1. Dans la boîte de dialogue **Set up pipeline run** (Configurer l’exécution du pipeline), sélectionnez **Create new** (Créer une nouvelle) pour créer une expérience.

    Les expériences regroupent logiquement les exécutions de pipeline associées. Si vous exécutez ce pipeline à l’avenir, vous devrez utiliser la même expérience à des fins de journalisation et de suivi.

1. Fournissez un nom d’expérience descriptif tel que « fractionnement-données-recensement ».

1. Sélectionnez **Envoyer**.

## <a name="view-results"></a>Afficher les résultats

Une fois l’exécution du pipeline terminée, vous pouvez afficher vos résultats en accédant à votre stockage d’objets blob dans le portail Azure. Vous pouvez également afficher les résultats intermédiaires du composant **Split Data** pour vérifier que vos données ont été correctement fractionnées.

1. Sélectionnez le composant **Split Data**.

1. Dans le volet d’informations des composants à droite du canevas, sélectionnez **Sorties + journaux**. 

1. Sélectionnez l’icône Visualiser ![icône Visualiser](media/how-to-designer-transform-data/visualize-icon.png) en regard de **Results dataset1** (Résultats JeuDonnées1). 

1. Vérifiez que la colonne « native-Country » contient uniquement la valeur « United States ».

1. Sélectionnez l’icône Visualiser ![icône Visualiser](media/how-to-designer-transform-data/visualize-icon.png) en regard de **Results dataset2** (Résultats JeuDonnées2). 

1. Vérifiez que la colonne « native-Country » ne contient pas la valeur « United States ».

## <a name="clean-up-resources"></a>Nettoyer les ressources

Ignorez cette section si vous souhaitez passer à la deuxième partie de cette procédure, [Réentraîner des modèles avec le concepteur Azure Machine Learning](how-to-retrain-designer.md).

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Étapes suivantes

Cet article vous a montré comment transformer un jeu de données et l’enregistrer dans un magasin de données inscrit.

Passez à la partie suivante de cette procédure, [Réentraîner des modèles avec le concepteur Azure Machine Learning](how-to-retrain-designer.md) pour utiliser vos jeux de données transformés et paramètres de pipeline afin d’effectuer l’apprentissage des modèles Machine Learning.