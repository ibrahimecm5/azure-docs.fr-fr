---
title: 'Tutoriel : Concepteur – effectuer l’apprentissage d’un modèle de régression sans code'
titleSuffix: Azure Machine Learning
description: Effectuer l’apprentissage d’un modèle de régression qui prédit les prix des voitures à l’aide du concepteur Azure Machine Learning.
author: peterclu
ms.author: peterlu
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 10/21/2021
ms.custom: designer, FY21Q4-aml-seo-hack, contperf-fy21q4
ms.openlocfilehash: 15edab4bc16067b866912e1fca899e844ff6e7e0
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131554925"
---
# <a name="tutorial-designer---train-a-no-code-regression-model"></a>Tutoriel : Concepteur – effectuer l’apprentissage d’un modèle de régression sans code

Effectuer l’apprentissage d’un modèle de régression linéaire qui prédit les prix des voitures à l’aide du concepteur Azure Machine Learning. Ce tutoriel est le premier d’une série de deux.

Ce tutoriel utilise le concepteur Azure Machine Learning. Pour plus d’informations, consultez [Qu’est-ce que le concepteur Azure Machine Learning ?](concept-designer.md)

Dans la première partie du tutoriel, vous allez :

> [!div class="checklist"]
> * Créer un pipeline
> * Importer des données
> * Préparer les données
> * Entraîner un modèle Machine Learning
> * Évaluer un modèle Machine Learning

Dans la [deuxième partie](tutorial-designer-automobile-price-deploy.md) du tutoriel, vous déployez votre modèle en tant que point de terminaison d’inférence en temps réel pour prédire le prix de n’importe quel véhicule en fonction des caractéristiques techniques que vous lui envoyez. 

> [!NOTE]
>Une version complète de ce tutoriel est disponible en tant qu’exemple de pipeline.
>
>Pour le trouver, accédez au concepteur dans votre espace de travail. Dans la section **Nouveau pipeline**, sélectionnez **Exemple 1 - Régression : Prédiction du prix de véhicules automobiles (de base)** .

[!INCLUDE [machine-learning-missing-ui](../../includes/machine-learning-missing-ui.md)]

## <a name="create-a-new-pipeline"></a>Créer un pipeline

Les pipelines Azure Machine Learning organisent plusieurs étapes de machine learning et de traitement de données en une même ressource. Les pipelines vous permettent d’organiser, de gérer et de réutiliser des workflows de Machine Learning complexes entre des projets et des utilisateurs.

Pour créer un pipeline Azure Machine Learning, vous devez disposer d’un espace de travail Azure Machine Learning. Dans cette section, vous découvrez comment créer ces deux ressources.

### <a name="create-a-new-workspace"></a>Créer un espace de travail

Vous devez avoir un espace de travail Azure Machine Learning pour utiliser le concepteur. L’espace de travail est la ressource de niveau supérieur pour Azure Machine Learning. Il fournit un emplacement centralisé où vous interagissez avec tous les artefacts que vous créez dans Azure Machine Learning. Pour savoir comment créer un espace de travail, consultez [Créer et gérer les espaces de travail Azure Machine Learning](how-to-manage-workspace.md).

> [!NOTE]
> Si votre espace de travail utilise un réseau virtuel, vous devez effectuer des étapes de configuration supplémentaires pour utiliser le concepteur. Pour plus d’informations, consultez [Utiliser le studio Azure Machine Learning dans un réseau virtuel Azure](how-to-enable-studio-virtual-network.md)

### <a name="create-the-pipeline"></a>Créer le pipeline

1. Connectez-vous à <a href="https://ml.azure.com?tabs=jre" target="_blank">ml.azure.com</a>, puis sélectionnez l’espace de travail à utiliser.

1. Sélectionnez **Concepteur**.

    ![Capture d’écran de l’espace de travail visuel montrant comment accéder au concepteur](./media/tutorial-designer-automobile-price-train-score/launch-designer.png)

1. Sélectionnez **Composants prédéfinis faciles à utiliser**.

1. En haut du canevas, sélectionnez le nom de pipeline par défaut **Pipeline-Created-on**. Renommez-le *Automobile price prediction*. Le nom n’a pas besoin d’être unique.

## <a name="set-the-default-compute-target"></a>Définir la cible de calcul par défaut

Un pipeline s’exécute sur une cible de calcul, qui est une ressource de calcul attachée à votre espace de travail. Une fois que vous avez créé une cible de calcul, vous pouvez la réutiliser pour d’autres exécutions ultérieures.

Vous pouvez définir une **cible de calcul par défaut** pour le pipeline entier si vous souhaitez que tous les composants utilisent la même cible de calcul par défaut. Toutefois, vous pouvez définir des cibles de calcul différentes selon les modules.

1. À côté du nom du pipeline, sélectionnez l’**icône d’engrenage** ![Capture d’écran de l’icône d’engrenage](./media/tutorial-designer-automobile-price-train-score/gear-icon.png) en haut du canevas pour ouvrir le volet **Paramètres**.

1. Dans le volet **Paramètres** à droite du canevas, sélectionnez **Sélectionner une cible de calcul**.

    Si vous avez déjà une cible de calcul, vous pouvez la sélectionner pour exécuter ce pipeline.

    > [!NOTE]
    > Le concepteur peut uniquement exécuter des expérimentations d’entraînement sur des instances de calcul Azure Machine Learning. Les autres cibles de calcul n’apparaîtront pas.

1. Entrez un nom pour la ressource de calcul.

1. Sélectionnez **Enregistrer**.

    > [!NOTE]
    > La création d’une ressource de calcul prend environ cinq minutes. Une fois que la ressource a été créée, vous pouvez la réutiliser pour les exécutions ultérieures, ce qui vous évite ce temps d’attente.
    >
    > Afin de réduire vos coûts, la ressource de calcul est automatiquement mise à l’échelle à zéro nœud quand elle est inactive. Quand vous la réutilisez après un temps d’inactivité, vous pourriez encore avoir à attendre environ cinq minutes pendant le scale-up de la ressource de calcul.

## <a name="import-data"></a>Importer des données

Un certain nombre d’exemples de jeux de données que vous pouvez expérimenter sont inclus dans le concepteur. Pour les besoins de ce tutoriel, vous allez utiliser **Automobile price data (Raw)** (Données sur le prix des véhicules automobiles [brutes]). 

1. À gauche du canevas du pipeline se trouve une palette de jeux de données et de composants. Sélectionnez **Exemples de jeux de données** pour voir les exemples de jeux de données disponibles.

1. Sélectionnez le jeu de données **Automobile price data (raw)** (Données sur le prix des automobiles (brut)), puis faites-le glisser vers le canevas.

   ![Faites glisser les données jusqu’au canevas](./media/tutorial-designer-automobile-price-train-score/drag-data.gif)

### <a name="visualize-the-data"></a>Visualiser les données

Vous pouvez visualiser les données pour comprendre le jeu de données que vous allez utiliser.

1. Cliquez avec le bouton droit sur **Données sur le prix des véhicules automobiles (brutes)** , puis sélectionnez **Visualiser** > **Sortie de jeu de données**.

1. Cliquez sur différentes colonnes dans la fenêtre de données pour visualiser des informations les concernant.

    Chaque ligne représente un véhicule automobile et chaque colonne représente une variable associée au véhicule automobile. Ce jeu de données contient 205 lignes et 26 colonnes.

## <a name="prepare-data"></a>Préparer les données

Les jeux de données nécessitent généralement un prétraitement avant l’analyse. Vous avez peut-être remarqué qu’il manquait des valeurs durant l’inspection du jeu de données. Ces valeurs manquantes doivent être nettoyées pour que le modèle puisse analyser les données correctement.

### <a name="remove-a-column"></a>Supprimer une colonne

Quand vous entraînez un modèle, vous devez traiter le problème des données manquantes. Dans ce jeu de données, la colonne **normalized-losses** (pertes normalisées) a de nombreuses valeurs manquantes : vous allez donc l’exclure du modèle.

1. Dans la palette des composants à gauche du canevas, développez la section **Transformation des données** et recherchez le composant **Sélectionner des colonnes dans le jeu de données**.

1. Faites glisser le composant **Sélectionner des colonnes dans le jeu de données** sur le canevas. Déposez le composant sous le composant du jeu de données.

1. Connectez le jeu de données **Données sur le prix des véhicules automobiles (brutes)** au composant **Sélectionner des colonnes dans le jeu de données**. Faites-le glisser depuis le port de sortie du jeu de données, qui est le petit cercle situé en bas du jeu de données sur le canevas, jusqu’au port d’entrée de **Sélectionner des colonnes dans le jeu de données**, qui est le petit cercle en haut du composant.

    > [!TIP]
    > Vous créez un flux de données dans votre pipeline lorsque vous connectez le port de sortie d’un composant au port d’entrée d’un autre.
    >

    ![Connecter des composants](./media/tutorial-designer-automobile-price-train-score/connect-modules.gif)

1. Sélectionnez le composant **Sélectionner des colonnes dans le jeu de données**.

1. Dans le volet d’informations du composant à droite du canevas, sélectionnez **Modifier la colonne**.

1. Développez la liste déroulante **Column names** (Noms des colonnes) à côté de **Include** (Inclure), puis sélectionnez **All columns** (Toutes les colonnes).

1. Sélectionnez le signe **+** pour ajouter une règle.

1. Dans les menus déroulants, sélectionnez **Exclude** (Exclure) et **Column names** (Noms des colonnes).
    
1. Entrez *normalized-losses* (pertes normalisées) dans la zone de texte.

1. En bas à droite, sélectionnez **Enregistrer** pour fermer le sélecteur de colonne.

    ![Exclure une colonne](./media/tutorial-designer-automobile-price-train-score/exclude-column.png)

1. Sélectionnez le composant **Sélectionner des colonnes dans le jeu de données**. 

1. Dans le volet d’informations du composant à droite du canevas, sélectionnez la zone de texte **Commentaire** et entrez *Exclure les pertes normalisées*.

    Les commentaires sont affichés sur le graphe pour vous aider à organiser votre pipeline.

### <a name="clean-missing-data"></a>Nettoyage des données manquantes

Il manque encore des valeurs dans votre jeu de données après la suppression de la colonne **normalized-losses**. Vous pouvez supprimer les données manquantes restantes à l’aide du composant **Nettoyage des données manquantes**.

> [!TIP]
> Le nettoyage des valeurs manquantes dans les données d’entrée est une condition préalable à l’utilisation de la plupart des composants du concepteur.

1. Dans la palette des composants à gauche du canevas, développez la section **Transformation des données** et recherchez le composant **Nettoyage des données manquantes**.

1. Faites glisser le composant **Nettoyage des données manquantes** jusqu’au canevas du pipeline. Connectez-le au composant **Sélectionner des colonnes dans le jeu de données**. 

1. Sélectionnez le composant **Clean Missing Data**.

1. Dans le volet d’informations du composant à droite du canevas, sélectionnez **Modifier la colonne**.

1. Dans la fenêtre **Columns to be cleaned** (Colonnes à nettoyer) qui s’affiche, développez le menu déroulant en regard d’**Include** (inclure). Sélectionnez **All columns** (Toutes les colonnes).

1. Sélectionnez **Enregistrer**.

1. Dans le volet d’informations du composant à droite du canevas, sélectionnez **Supprimer la ligne entière** sous **Mode de nettoyage**.

1. Dans le volet d’informations du composant à droite du canevas, sélectionnez la zone de texte **Commentaire** et entrez *Supprimer les lignes de valeurs manquantes*. 

    Votre pipeline doit maintenant se présenter comme ceci :

    :::image type="content" source="./media/tutorial-designer-automobile-price-train-score/pipeline-clean.png" alt-text="Select-column":::

## <a name="train-a-machine-learning-model"></a>Entraîner un modèle Machine Learning

Une fois les composants en place pour traiter les données, vous pouvez configurer les composants d’entraînement.

Comme vous voulez prédire un prix, à savoir un nombre, vous pouvez utiliser un algorithme de régression. Pour cet exemple, vous utilisez un modèle de régression linéaire.

### <a name="split-the-data"></a>Fractionner les données

Le fractionnement des données est une tâche courante de Machine Learning. Vous allez diviser vos données en deux jeux de données distincts. Un jeu de données entraîne le modèle et l’autre teste les performances du modèle.

1. Dans la palette de composants, développez la section **Transformation des données** et recherchez le composant **Fractionner les données**.

1. Faites glisser le composant **Fractionner les données** jusqu’au canevas du pipeline.

1. Connectez le port gauche du composant **Nettoyage des données manquantes** au composant **Fractionner les données**.

    > [!IMPORTANT]
    > Vérifiez que le port de sortie de gauche de **Clean Missing Data** est connecté à **Split Data**. Le port de gauche contient les données nettoyées. Le port de droite contient les données abandonnées.

1. Sélectionnez le composant **Split Data**.

1. Dans le volet d’informations du composant à droite du canevas, définissez l’option **Fraction de lignes dans le premier jeu de données de sortie** sur la valeur 0,7.

    Cette option permet de diviser les données afin d’en utiliser 70 % pour entraîner le modèle et 30 % pour tester ce dernier. Le jeu de données comprenant 70 % des données est accessible par le biais du port de sortie de gauche. Les données restantes sont disponibles par le biais du port de sortie de droite.

1. Dans le volet d’informations du composant à droite du canevas, sélectionnez la zone de texte **Commentaire** et entrez *Diviser le jeu de données en un jeu d’entraînement (0,7) et un jeu de test (0,3)* .

### <a name="train-the-model"></a>Effectuer l’apprentissage du modèle

Entraînez le modèle en lui fournissant un jeu de données incluant le prix. L’algorithme construit un modèle qui explique la relation entre les caractéristiques et le prix dans les données d’entraînement.

1. Dans la palette des composants, développez **Algorithmes Machine Learning**.
    
    Cette option affiche plusieurs catégories de composants qui vous permettent d’initialiser les algorithmes d’apprentissage.

1. Sélectionnez **Regression** > **Linear Regression** (Régression > Régression linéaire), puis faites glisser le module vers le canevas du pipeline.

1. Dans la palette des composants, développez la section **Entraînement de module**, puis faites glisser le composant **Effectuer l’apprentissage du modèle** jusqu’au canevas.

1. Connectez la sortie du composant **Régression linéaire** à l’entrée gauche du composant **Effectuer l’apprentissage du modèle**.

1. Connectez la sortie des données d’entraînement (port gauche) du composant **Fractionner les données** à l’entrée droite du composant **Effectuer l’apprentissage du modèle**.
    
    > [!IMPORTANT]
    > Vérifiez que le port de sortie de gauche de **Split Data** est connecté à **Train Model**. Le port de gauche contient le jeu d’entraînement. Le port de droite contient le jeu de test.

    :::image type="content" source="./media/tutorial-designer-automobile-price-train-score/pipeline-train-model.png" alt-text="Capture d’écran montrant la configuration correcte du composant Effectuer l’apprentissage du modèle. Le composant Régression linéaire se connecte au port gauche du composant Effectuer l’apprentissage du modèle et le composant Fractionner les données se connecte au port droit du composant Effectuer l’apprentissage du modèle.":::

1. Sélectionnez le composant **Effectuer l’apprentissage du modèle**.

1. Dans le volet d’informations du composant à droite du canevas, sélectionnez le sélecteur de **Modifier la colonne**.

1. Dans la boîte de dialogue **Label column** (Étiqueter une colonne), développez le menu déroulant, puis sélectionnez **Column names** (Noms de colonnes). 

1. Dans la zone de texte, entrez *price* pour spécifier la valeur que votre modèle va prédire.

    >[!IMPORTANT]
    > Veillez à entrer le nom de colonne tel qu’indiqué. Ne mettez pas **price** en majuscules. 

    Votre pipeline doit se présenter comme suit :

    :::image type="content" source="./media/tutorial-designer-automobile-price-train-score/pipeline-train-graph.png" alt-text="Capture d’écran montrant la configuration correcte du pipeline après l’ajout du composant Effectuer l’apprentissage du modèle.":::

### <a name="add-the-score-model-component"></a>Ajoutez le composant Noter le modèle.

Une fois que vous avez entraîné votre modèle à l’aide de 70 % des données, vous pouvez l’utiliser pour attribuer un score aux 30 % de données restants, et vérifier ainsi son bon fonctionnement.

1. Entrez *noter le modèle* dans la zone de recherche pour trouver le composant **Noter le modèle**. Faites glisser le composant jusqu’au canevas du pipeline. 

1. Connectez la sortie du composant **Effectuer l’apprentissage du modèle** au port d’entrée gauche du composant **Noter le modèle**. Connectez la sortie des données de test (port droit) du composant **Fractionner les données** au port d’entrée droit du composant **Noter le modèle**.

### <a name="add-the-evaluate-model-component"></a>Ajouter le composant Évaluer le modèle

Utilisez le composant **Évaluer le modèle** pour évaluer le score attribué par votre modèle au jeu de données de test.

1. Entrez *évaluer* dans la zone de recherche pour trouver le composant **Évaluer le modèle**. Faites glisser le composant jusqu’au canevas du pipeline. 

1. Connectez la sortie du composant **Noter le modèle** à l’entrée gauche du composant **Évaluer le modèle**. 

    Le pipeline final doit maintenant se présenter comme ceci :

    :::image type="content" source="./media/tutorial-designer-automobile-price-train-score/pipeline-final-graph.png" alt-text="Capture d’écran montrant la configuration correcte du pipeline.":::

## <a name="submit-the-pipeline"></a>Envoyer le pipeline

Quand vous avez terminé la configuration de votre pipeline, vous pouvez lancer son exécution pour entraîner le modèle Machine Learning. Vous pouvez à tout moment envoyer une exécution de pipeline valide qui peut être utilisée pour examiner les modifications apportées à votre pipeline pendant le développement.

1. En haut du canevas, sélectionnez **Envoyer**.

1. Dans la boîte de dialogue **Configurer une exécution de pipeline**, sélectionnez **Créer**.

    > [!NOTE]
    > Les expériences regroupent les exécutions de pipeline similaires. Si vous exécutez un pipeline plusieurs fois, vous pouvez sélectionner la même expérience pour les exécutions successives.

    1. Pour **Nom de la nouvelle expérience**, entrez **Tutorial-CarPrices**.

    1. Sélectionnez **Envoyer**.
    
    Vous pouvez voir l’état et les détails de l’exécution en haut à droite du canevas.
    
    Si c’est la première fois, l’exécution de votre pipeline peut prendre jusqu’à 20 minutes. Les paramètres de calcul par défaut ont une taille de nœud minimale de 0, ce qui signifie que le concepteur doit allouer des ressources après une période d’inactivité. Les exécutions de pipeline répétées prennent moins de temps dans la mesure où les ressources de calcul sont déjà allouées. Par ailleurs, le concepteur utilise les résultats mis en cache pour chaque composant afin d’améliorer l’efficacité.

### <a name="view-scored-labels"></a>Afficher les étiquettes de score

Une fois l’exécution terminée, vous pouvez voir les résultats de l’exécution du pipeline. Tout d’abord, examinez les prédictions générées par le modèle de régression.

1. Cliquez avec le bouton droit sur le composant **Noter le modèle** et sélectionnez **Visualiser** > **Jeu de données noté** pour afficher sa sortie.

    Vous pouvez voir ici les prix prédits et les prix réels des données à partir des données de test.

    :::image type="content" source="./media/tutorial-designer-automobile-price-train-score/score-result.png" alt-text="Capture d’écran de la visualisation de la sortie mettant en évidence la colonne d’étiquettes notées":::

### <a name="evaluate-models"></a>Évaluer les modèles

Utilisez **Evaluate Model** pour voir ce que donne le modèle entraîné sur le jeu de données de test.

1. Cliquez avec le bouton droit sur le composant **Évaluer le modèle** et sélectionnez **Visualiser** > **Résultats de l’évaluation** pour afficher sa sortie.

Les statistiques suivantes s’affichent pour votre modèle :

* **Erreur absolue moyenne** : Moyenne des erreurs absolues. Une erreur est la différence entre la valeur prédite et la valeur réelle.
* **Racine carrée de l’erreur quadratique moyenne** : la racine carrée de la moyenne des erreurs carrées des prévisions effectuées sur le jeu de données de test.
* **Erreur absolue relative**: la moyenne des erreurs absolues relative à la différence absolue entre les valeurs réelles et la moyenne de toutes les valeurs réelles.
* **Erreur carrée relative** : la moyenne des erreurs carrées relative à la différence carrée entre les valeurs réelles et la moyenne de toutes les valeurs réelles.
* **Coefficient de détermination** : Également connue sous le nom de valeur R au carré, cette métrique statistique indique dans quelle mesure un modèle correspond aux données.

Pour chacune des statistiques liées aux erreurs, les valeurs les plus petites sont privilégiées. En effet, une valeur plus petite indique que les prédictions sont plus près des valeurs réelles. Plus la valeur du coefficient de détermination est proche de un (1,0), plus les prévisions sont correctes.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Ignorez cette section si vous souhaitez passer à la deuxième partie du tutoriel sur le [déploiement de modèles](tutorial-designer-automobile-price-deploy.md).

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans la deuxième partie, vous allez découvrir comment déployer votre modèle en tant que point de terminaison en temps réel.

> [!div class="nextstepaction"]
> [Continuer avec le déploiement des modèles](tutorial-designer-automobile-price-deploy.md)
