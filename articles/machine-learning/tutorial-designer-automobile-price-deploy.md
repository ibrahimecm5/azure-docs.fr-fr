---
title: 'Tutoriel : (concepteur) Déployer des modèles sans code'
titleSuffix: Azure Machine Learning
description: Déployez un modèle de Machine Learning pour prédire les prix des voitures avec le concepteur d’Azure Machine Learning.
author: likebupt
ms.author: keli19
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 10/21/2021
ms.custom: designer, FY21Q4-aml-seo-hack, contperf-fy21q4
ms.openlocfilehash: 3348bddec5700ba2a5ddf1112ad20f9319e04d79
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131554884"
---
# <a name="tutorial-designer---deploy-a-machine-learning-model"></a>Tutoriel : (concepteur) Déployer un modèle Machine Learning

Utilisez le concepteur pour déployer un modèle de Machine Learning afin de prédire le prix des voitures. Ce tutoriel est le deuxième d’une série de deux.


Dans la [première partie du tutoriel](tutorial-designer-automobile-price-train-score.md), vous avez formé un modèle de régression linéaire sur les prix des voitures. Dans la deuxième partie, vous déployez le modèle pour permettre aux autres utilisateurs de l’utiliser. Dans ce tutoriel, vous allez :

> [!div class="checklist"]
> * Créez un pipeline d’inférence en temps réel.
> * Créez un cluster d’inférence.
> * Déployez le point de terminaison en temps réel.
> * Testez le point de terminaison en temps réel.

## <a name="prerequisites"></a>Prérequis

Suivez la [première partie du tutoriel](tutorial-designer-automobile-price-train-score.md) pour découvrir comment entraîner et évaluer un modèle Machine Learning dans le concepteur.

[!INCLUDE [machine-learning-missing-ui](../../includes/machine-learning-missing-ui.md)]

## <a name="create-a-real-time-inference-pipeline"></a>Créer un pipeline d’inférence en temps réel

Pour déployer votre pipeline, vous devez d’abord convertir le pipeline d’entraînement en pipeline d’inférence en temps réel. Ce processus supprime les composants de formation et ajoute les entrées et sorties de service web pour gérer les demandes.

### <a name="create-a-real-time-inference-pipeline"></a>Créer un pipeline d’inférence en temps réel

1. Au-dessus du canevas de pipeline, sélectionnez **Create inference pipeline** > **Real-time inference pipeline** (Créer un pipeline d’inférence > Pipeline d’inférence en temps réel).

    :::image type="content" source="./media/tutorial-designer-automobile-price-deploy/tutorial2-create-inference-pipeline.png" alt-text="Capture d’écran montrant l’emplacement du bouton pour créer un pipeline":::

    Votre pipeline doit maintenant se présenter comme suit : 

   ![Capture d’écran montrant la configuration attendue du pipeline après sa préparation pour le déploiement](./media/tutorial-designer-automobile-price-deploy/real-time-inference-pipeline.png)

    Quand vous sélectionnez **Créer un pipeline d’inférence**, plusieurs choses se produisent :
    
    * Le modèle formé est stocké sous la forme d’un composant **Jeux de données** dans la palette de composants. Vous pouvez le trouver sous **Mes modèles**.
    * Des composants de formation, comme **Effectuer l’apprentissage du modèle** et **Fractionner les données** sont supprimés.
    * Le modèle entraîné enregistré est rajouté au pipeline.
    * Les composants **Entrée du service web** et **Sortie du service web** sont ajoutés. Ces composants montrent l’emplacement où les données utilisateur entrent dans le pipeline ainsi que l’emplacement où ces données sont renvoyées.

    > [!NOTE]
    > Par défaut, le composant **Entrée du service web** attend le même schéma de données que les données de sortie du composant qui se connectent au même port en aval qu’elle. Dans cet exemple, le composant **Entrée du service web** et le jeu de données **Données sur le prix des véhicules automobiles (brutes)** se connectent au même composant en aval. Par conséquent, le composant **Entrée du service web** attend le même schéma de données que le jeu de données **Données sur le prix des véhicules automobiles (brutes)** et la colonne variable cible `price` est incluse dans le schéma.
    > Toutefois, quand vous attribuez un score aux données, vous ne connaissez pas les valeurs des variables cibles. Dans ce cas, vous pouvez supprimer la colonne variable cible dans le pipeline d’inférence à l’aide du composant **Sélectionner des colonnes dans le jeu de données**. Vérifiez que la sortie du composant **Sélectionner des colonnes dans le jeu de données** supprimant la colonne variable cible est connectée au même port que la sortie du composant **Entrée du service web**.

1. Sélectionnez **Envoyer**, puis utilisez la même cible de calcul et la même expérience que durant la première partie.

    Si c’est la première fois, l’exécution de votre pipeline peut prendre jusqu’à 20 minutes. Les paramètres de calcul par défaut ont une taille de nœud minimale de 0, ce qui signifie que le concepteur doit allouer des ressources après une période d’inactivité. Les exécutions de pipeline répétées prennent moins de temps dans la mesure où les ressources de calcul sont déjà allouées. Par ailleurs, le concepteur utilise les résultats mis en cache pour chaque composant afin d’améliorer l’efficacité.

1. Sélectionnez **Déployer**.

## <a name="create-an-inferencing-cluster"></a>Créer un cluster d’inférence

Dans la boîte de dialogue qui s’affiche, vous pouvez sélectionner n’importe quel cluster AKS (Azure Kubernetes Service) existant sur lequel déployer votre modèle. Si vous n’avez pas de cluster AKS, utilisez les étapes suivantes pour en créer un.

1. Dans la boîte de dialogue qui s’affiche, sélectionnez **Calculer** pour accéder à la page **Calculer**.

1. Dans le ruban de navigation, sélectionnez **Clusters d’inférence** >  **+ Nouveau**.

    ![Capture d’écran montrant comment accéder au volet Nouveau cluster d’inférence](./media/tutorial-designer-automobile-price-deploy/new-inference-cluster.png)
   
1. Dans le volet Cluster d’inférence, configurez un nouveau service Kubernetes.

1. Entrez *aks-compute* pour **Nom du calcul**.
    
1. Sélectionnez une région proche disponible pour **Région**.

1. Sélectionnez **Create** (Créer).

    > [!NOTE]
    > La création d’un service AKS prend environ 15 minutes. Vous pouvez vérifier l’état de provisionnement dans la page **Clusters d’inférence**.
    >

## <a name="deploy-the-real-time-endpoint"></a>Déployer le point de terminaison en temps réel

Une fois le provisionnement du service AKS terminé, revenez au pipeline d’inférence en temps réel pour terminer le déploiement.

1. Sélectionnez **Déployer** au-dessus du canevas.

1. Sélectionnez **Déployer un nouveau point de terminaison en temps réel**. 

1. Sélectionnez le cluster AKS que vous avez créé.

    :::image type="content" source="./media/tutorial-designer-automobile-price-deploy/setup-endpoint.png" alt-text="Capture d’écran montrant comment configurer un nouveau point de terminaison en temps réel":::

    Vous pouvez également changer le paramètre **Avancé** pour votre point de terminaison en temps réel.
    
    |Paramètre avancé|Description|
    |---|---|
    |Activer les diagnostics et la collecte de données Application Insights| Indique s’il faut activer Azure Application Insights pour collecter des données à partir des points de terminaison déployés. </br> Par défaut : false |
    |Délai d’expiration du scoring| Délai d’expiration en millisecondes à appliquer pour les appels de scoring au service web.</br>Par défaut : 60000|
    |Mise à l’échelle automatique activée|   Indique s’il faut activer la mise à l’échelle automatique pour le service Web.</br>Par défaut : true|
    |Nb min. de réplicas| Nombre minimal de conteneurs à utiliser lors de la mise à l’échelle automatique de ce service web.</br>Par défaut : 1|
    |Nb max. de réplicas| Nombre maximal de conteneurs à utiliser lors de la mise à l’échelle automatique de ce service web.</br> Par défaut : 10|
    |Utilisation cible|Utilisation cible (en pourcentage sur 100) que la mise à l’échelle automatique doit tenter de gérer pour ce service web.</br> Par défaut : 70|
    |Période d’actualisation|Fréquence (en secondes) à laquelle la mise à l’échelle automatique tente de mettre à l’échelle ce service web.</br> Par défaut : 1|
    |Capacité de réserve de processeur|Nombre de cœurs de processeur à allouer pour ce service web.</br> Par défaut : 0.1|
    |Capacité de réserve de mémoire|Quantité de mémoire (en Go) à allouer à ce service web.</br> Par défaut : 0.5|
        

1. Sélectionnez **Déployer**. 

    Une notification de réussite s’affiche au-dessus du canevas, une fois le déploiement effectué. Cela peut prendre quelques minutes.

> [!TIP]
> Vous pouvez également effectuer le déploiement sur **Azure Container Instance** (ACI) si vous sélectionnez **Azure Container Instances** comme **Type de calcul** dans la zone des paramètres de point de terminaison en temps réel.
> Le paramètre Azure Container Instances est utilisé à des fins de test ou de développement. Utilisez ACI pour les charges de travail à faible échelle basées sur le processeur qui nécessitent moins de 48 Go de RAM.

## <a name="test-the-real-time-endpoint"></a>Tester le point de terminaison en temps réel

Une fois le déploiement effectué, vous pouvez afficher votre point de terminaison en temps réel en accédant à la page **Points de terminaison**.

1. Dans la page **Points de terminaison**, sélectionnez le point de terminaison que vous avez déployé.

    Sous l’onglet **Détails**, vous pouvez obtenir plus d’informations, telles que l’URI REST, la définition Swagger, l’état et les étiquettes.

    Sous l’onglet **Consommer**, vous trouverez un exemple de code de consommation et des clés de sécurité, et pouvez définir des méthodes d’authentification.

    Sous l’onglet **Journaux de déploiement**, vous trouverez les journaux de déploiement détaillés de votre point de terminaison en temps réel.

1. Pour tester votre point de terminaison, accédez à l’onglet **Tester**. À partir de là, vous pouvez entrer des données de test et sélectionner **Tester** afin de vérifier la sortie de votre point de terminaison.

Pour plus d’informations sur l’utilisation de votre service web, consultez [Consommer un modèle déployé en tant que service web](how-to-consume-web-service.md).

## <a name="limitations"></a>Limites

### <a name="update-inference-pipeline"></a>Mettre à jour le pipeline d’inférence

Si vous modifiez votre pipeline d’entraînement, vous devez soumettre à nouveau le pipeline d’entraînement, **mettre à jour** le pipeline d’inférence et réexécuter le pipeline d’inférence.

Notez que seuls les modèles entraînés seront mis à jour dans le pipeline d’inférence, alors que la transformation de données ne sera pas mise à jour.

Pour utiliser la transformation mise à jour dans le pipeline d’inférence, vous devez inscrire la sortie de transformation du composant de transformation en tant que jeu de données.

![Capture d’écran montrant comment inscrire le jeu de données de transformation](./media/tutorial-designer-automobile-price-deploy/register-transformation-dataset.png)

Ensuite, remplacez manuellement le composant **TD-** dans le pipeline d’inférence par le jeu de données inscrit.

![Capture d’écran montrant comment remplacer le composant de transformation](./media/tutorial-designer-automobile-price-deploy/replace-td-module.png)

Vous pouvez ensuite envoyer le pipeline d’inférence avec le modèle et la transformation mis à jour, puis déployer.

### <a name="deploy-real-time-endpoint"></a>Déployer un point de terminaison en temps réel

En raison de la limitation de l’accès au magasin de données, si votre pipeline d’inférence contient un composant **Importer des données**  ou **Exporter des données**, il est automatiquement supprimé lors du déploiement sur le point de terminaison en temps réel.

## <a name="clean-up-resources"></a>Nettoyer les ressources

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez découvert les étapes clés de la création, du déploiement et de la consommation d’un modèle Machine Learning dans le concepteur. Pour en savoir plus sur la façon dont vous pouvez utiliser le concepteur, consultez les liens suivants :

+ [Exemples du concepteur](samples-designer.md) : Découvrez comment utiliser le concepteur pour résoudre d’autres types de problèmes.
+ [Utiliser le studio Azure Machine Learning dans un réseau virtuel Azure](how-to-enable-studio-virtual-network.md).
