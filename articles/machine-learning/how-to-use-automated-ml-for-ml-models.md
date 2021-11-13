---
title: Configurer l’AutoML avec l’interface utilisateur de Studio
titleSuffix: Azure Machine Learning
description: Découvrez comment configurer l’exécution de l’apprentissage AutoML sans une seule ligne de code avec Azure Machine Learning ML automatisé dans Azure Machine Learning Studio.
services: machine-learning
ms.service: machine-learning
ms.subservice: automl
ms.author: nibaccam
author: cartacioS
ms.reviewer: nibaccam
ms.date: 10/21/2021
ms.topic: how-to
ms.custom: automl, FY21Q4-aml-seo-hack, contperf-fy21q4
ms.openlocfilehash: d7bf32faabd6b0a9d2037ad5599a5b2cceb70053
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131556252"
---
# <a name="set-up-no-code-automl-training-with-the-studio-ui"></a>Configurer l’apprentissage AutoML sans code avec l’interface utilisateur de Studio 

Dans cet article, vous allez apprendre à configurer l’exécution de l’apprentissage AutoML sans une seule ligne de code à l’aide d’Azure Machine Learning ML automatisé dans [Azure Machine Learning Studio](overview-what-is-machine-learning-studio.md).

Le Machine Learning automatisé, ou AutoML, est un processus dans lequel le meilleur algorithme de Machine Learning à utiliser pour vos données spécifiques est automatiquement sélectionné. Ce processus vous permet de générer rapidement des modèles Machine Learning. [En savoir plus sur la façon dont Azure Machine Learning implémente le Machine Learning automatisé](concept-automated-ml.md).
 
Pour obtenir un exemple de bout en bout, essayez le [Tutoriel : AutoML - former des modèles de classification sans code](tutorial-first-experiment-automated-ml.md). 

Pour une expérience basée sur du code Python, [configurez vos expériences de machine learning automatisé](how-to-configure-auto-train.md) avec le SDK Azure Machine Learning.

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, créez un compte gratuit avant de commencer. Essayez la [version gratuite ou payante d’Azure Machine Learning](https://azure.microsoft.com/free/) dès aujourd’hui.

* Un espace de travail Azure Machine Learning. Consultez [Créer un espace de travail Microsoft Azure Machine Learning](how-to-manage-workspace.md). 

## <a name="get-started"></a>Bien démarrer

1. Connectez-vous à [Azure Machine Learning Studio](https://ml.azure.com). 

1. Sélectionnez votre abonnement et votre espace de travail. 

1. Accédez au volet gauche. Sélectionnez **Automated ML** (ML automatisé) sous la section **Author** (Auteur).

[![Volet de navigation d’Azure Machine Learning Studio](media/how-to-use-automated-ml-for-ml-models/nav-pane.png)](media/how-to-use-automated-ml-for-ml-models/nav-pane-expanded.png)

 S’il s’agit de votre première expérience, vous verrez une liste vide et des liens vers la documentation. 

Dans le cas contraire, vous verrez une liste de vos expériences récentes de ML automatisé, y compris celles créées avec le kit de développement logiciel (SDK). 

## <a name="create-and-run-experiment"></a>Créer et exécuter une expérience

1. Sélectionnez **+ Nouvelle exécution de ML automatisé** et remplissez le formulaire.

1. Sélectionnez un jeu de données à partir de votre conteneur de stockage ou créez un nouveau jeu de données. Les jeux de données peuvent être créés à partir de fichiers locaux, d’URL Web, de magasins de données ou de Azure Open Datasets. En savoir plus sur la [création de jeux de données](how-to-create-register-datasets.md).  

    >[!Important]
    > Configuration requise pour les données de formation :
    >* Les données doivent être sous forme tabulaire.
    >* La valeur que vous souhaitez prédire (colonne cible) doit être présente dans les données.

    1. Pour créer un jeu de données à partir d’un fichier sur votre ordinateur local, sélectionnez **+Créer un jeu de données**, puis sélectionnez **À partir d’un fichier local**. 

    1. Dans le formulaire **Informations de base**, donnez un nom unique à votre jeu de données et indiquez éventuellement une description. 

    1. Sélectionnez **Suivant** pour ouvrir le **formulaire Sélection d’un magasin de données et de fichiers**. Sur ce formulaire, vous sélectionnez l’emplacement où charger votre jeu de données : choisissez le conteneur de stockage par défaut qui est automatiquement créé avec votre espace de travail ou un conteneur de stockage que vous voulez utiliser pour l’expérience. 
    
        1. Si vos données se trouvent derrière un réseau virtuel, vous devez activer la fonction permettant d’**ignorer la validation** pour vous assurer que l’espace de travail peut accéder à vos données. Pour plus d’informations, consultez [Utiliser Azure Machine Learning Studio dans un réseau virtuel Azure](how-to-enable-studio-virtual-network.md). 
    
    1. Sélectionnez **Parcourir** pour charger le fichier de données de votre jeu de données. 

    1. Vérifiez l’exactitude du formulaire **Settings and preview** (Paramètres et aperçu). Le formulaire est rempli intelligemment en fonction du type de fichier. 

        Champ| Description
        ----|----
        Format de fichier| Définit la disposition et le type des données stockées dans un fichier.
        Délimiteur| Un ou plusieurs caractères utilisés pour spécifier la limite entre des régions indépendantes et séparées dans du texte brut ou d’autres flux de données.
        Encodage| Identifie la table de schéma bits/caractères à utiliser pour lire votre jeu de données.
        En-têtes de colonne| Indique la façon dont les éventuels en-têtes du jeu de données sont traités.
        Ignorer les lignes | Indique le nombre éventuel de lignes ignorées dans le jeu de données.
    
        Sélectionnez **Suivant**.

    1. Le formulaire **Schéma** est rempli intelligemment en fonction des sélections effectuées dans le formulaire **Paramètres et aperçu**. Ici, configurez le type de données pour chaque colonne, passez en revue les noms des colonnes et sélectionnez celles à **ne pas inclure** dans votre expérience. 
            
        Sélectionnez **Next** (Suivant).

    1. Le formulaire **Confirmer les détails** est un résumé des informations précédemment renseignées sur les formulaires **Informations de base** et **Paramètres et aperçu**. Vous avez également la possibilité de créer un profil de données pour votre jeu de données à l’aide d’un calcul activé pour le profilage. En savoir plus sur le [profilage des données](how-to-connect-data-ui.md#profile).

        Sélectionnez **Suivant**.
1. Sélectionnez votre jeu de données récemment créé une fois qu’il apparaît. Vous pouvez également afficher un aperçu du jeu de données et des exemples de statistiques. 

1. Dans le formulaire **Configurer l’exécution**, sélectionnez **Créer** , puis entrez **didacticiel-automl-deploy** pour le nom de l’expérience.

1. Sélectionnez une colonne cible ; il s’agit de la colonne sur laquelle vous voulez effectuer des prédictions.

1. Sélectionnez un type de calcul pour le travail de profilage d’entraînement des données. Vous pouvez sélectionner un [cluster de calcul](concept-compute-target.md#azure-machine-learning-compute-managed) ou une [instance de calcul](concept-compute-instance.md). 
    
1. Sélectionnez un calcul dans la liste déroulante contenant vos calculs existants.  Pour créer un calcul, suivez les instructions de l’étape 8.

1. Sélectionnez **Créer un nouveau calcul** afin de configurer votre contexte de calcul pour cette expérience.

    Champ|Description
    ---|---
    Nom du calcul| Entrez un nom unique qui identifie votre contexte de calcul.
    Priorité de machine virtuelle| Les machines virtuelles basse priorité sont moins chères, mais ne garantissent pas les nœuds de calcul. 
    Type de machine virtuelle| Sélectionnez l’UC ou le GPU pour le type de machine virtuelle.
    Taille de la machine virtuelle| Sélectionnez la taille de la machine virtuelle pour votre calcul.
    Nombre minimal/maximal de nœuds| Pour profiler des données, vous devez spécifier un ou plusieurs nœuds. Entrez le nombre maximal de nœuds pour votre calcul. La valeur par défaut est de 6 nœuds pour un calcul AML.
    Paramètres avancés | Ces paramètres vous permettent de configurer un compte d’utilisateur et un réseau virtuel existant pour votre expérience. 
    
    Sélectionnez **Create** (Créer). La création d’un calcul peut prendre quelques minutes.

    >[!NOTE]
    > Le nom de votre calcul indique si le *profilage est activé* pour le calcul que vous sélectionnez/créez. (Pour plus d’informations, consultez la section sur le [profilage des données](how-to-connect-data-ui.md#profile)).

    Sélectionnez **Suivant**.

1. Dans le formulaire **Type de tâche et paramètres**, sélectionnez le type de tâche : classification, régression ou prévision. Pour plus d’informations, consultez [Types de tâches pris en charge](concept-automated-ml.md#when-to-use-automl-classification-regression-forecasting--computer-vision).

    1. Pour la **classification**, vous pouvez également activer le deep learning.
    
        Si le deep learning est activé, la validation est limitée à la _division train_validation_. [Découvrez-en plus sur les options de validation](how-to-configure-cross-validation-data-splits.md).


    1. Pour les **prévisions** : 
    
        1. Activez le deep learning.
    
        1. Sélectionnez la *colonne d'heure* : cette colonne contient les données d'heure à utiliser.

        1. Sélectionnez l'*horizon de prévision* : Indiquez le nombre d’unités de temps (minutes/heures/jours/semaines/mois/années) que le modèle sera en mesure de prédire. Plus le modèle doit prédire dans un avenir lointain, moins il sera précis. [En savoir plus sur les prévisions et l'horizon de prévision](how-to-auto-train-forecast.md).

1. (Facultatif) Voir des paramètres de configuration supplémentaires : paramètres supplémentaires que vous pouvez utiliser pour mieux contrôler la tâche d’entraînement. Sinon, les valeurs par défaut sont appliquées en fonction de la sélection de l’expérience et des données. 

    Configurations supplémentaires|Description
    ------|------
    Métrique principale| Métrique principale utilisée pour évaluer votre modèle. [En savoir plus sur les métriques du modèle](how-to-configure-auto-train.md#primary-metric).
    Expliquer le meilleur modèle | Sélectionnez cette option pour activer ou désactiver l’affichage d’explications du meilleur modèle recommandé. <br> Cette fonctionnalité n’est actuellement pas disponible pour [certains algorithmes de prévision](how-to-machine-learning-interpretability-automl.md#interpretability-during-training-for-the-best-model). 
    Algorithme bloqué| Sélectionnez les algorithmes que vous souhaitez exclure du travail de formation. <br><br> L’autorisation des algorithmes est disponible uniquement pour les [expériences SDK](how-to-configure-auto-train.md#supported-models). <br> Consultez les [modèles pris en charge pour chaque type de tâche](/python/api/azureml-automl-core/azureml.automl.core.shared.constants.supportedmodels).
    Critère de sortie| Quand l’un de ces critères est satisfait, le travail d’entraînement s’arrête. <br> *Durée du travail de formation (heures)*  : Délai d'exécution du travail de formation. <br> *Seuil de score de métrique* :  Score de métrique minimal pour tous les pipelines. Ainsi, si vous avez défini une métrique cible que vous souhaitez atteindre, vous ne passez pas plus de temps sur le travail de formation que nécessaire.
    Validation| Sélectionnez une des options de validation croisée à utiliser dans le travail de formation. <br> [En savoir plus sur la validation croisée](how-to-configure-cross-validation-data-splits.md#prerequisites).<br> <br>Les prévisions prennent uniquement en charge la validation croisée k-fold.
    Accès concurrentiel| *Nombre maximal d'itérations simultanées* : Nombre maximal de pipelines (itérations) à tester dans le travail de formation. Le travail ne s'exécutera pas au-delà du nombre d’itérations spécifié. Apprenez-en davantage sur la manière dont le Machine Learning automatisé effectue [plusieurs exécutions enfants sur des clusters](how-to-configure-auto-train.md#multiple-child-runs-on-clusters).

1. (Facultatif) Afficher les paramètres de caractérisation : si vous choisissez d’activer **Caractérisation automatique** dans le formulaire **Paramètres de configuration supplémentaires** formulaire, les techniques caractérisation par défaut sont appliquées. Dans **Afficher les paramètres de caractérisation**, vous pouvez modifier ces valeurs par défaut et les personnaliser en conséquence. Découvrez comment [personnaliser la caractérisation](#customize-featurization). 

    ![Capture d’écran représentant la boîte de dialogue Sélectionner le type de tâche dans laquelle l’option View featurization settings (Voir les paramètres de caractérisation) est affichée.](media/how-to-use-automated-ml-for-ml-models/view-featurization-settings.png)

## <a name="customize-featurization"></a>Personnaliser la caractérisation

Dans le formulaire **Caractérisation**, vous pouvez activer/désactiver la caractérisation automatique et personnaliser les paramètres correspondants pour votre expérience. Pour ouvrir ce formulaire, reportez-vous à l’étape 10 de la section [Créer et exécuter une expérience](#create-and-run-experiment). 

Le tableau suivant récapitule les personnalisations actuellement disponibles via le studio. 

Colonne| Personnalisation
---|---
Inclus | Spécifie les colonnes à inclure pour la formation.
Type de caractéristique| Modifiez le type valeur de la colonne sélectionnée.
Imputer avec| Sélectionnez la valeur à imputer aux valeurs manquantes dans vos données.

![Caractérisation personnalisée d’Azure Machine Learning Studio](media/how-to-use-automated-ml-for-ml-models/custom-featurization.png)

## <a name="run-experiment-and-view-results"></a>Exécuter une expérience et afficher les résultats

Sélectionnez **Terminer** pour exécuter votre expérience. Le processus de préparation de l’expérience peut prendre jusqu’à 10 minutes. Les travaux de formation peuvent prendre 2 à 3 minutes supplémentaires pour que chaque pipeline se termine.

> [!NOTE]
> Les algorithmes utilisés par le ML automatisé présentent un fonctionnement aléatoire inhérent qui peut provoquer de légères variations dans un score de métrique final de modèle recommandé, comme la précision. Le ML automatisé exécute également des opérations au niveau des données, telles que le fractionnement de test de formation, le fractionnement de validation de formation ou la validation croisée, le cas échéant. Par conséquent, si vous exécutez plusieurs fois une expérience avec les mêmes paramètres de configuration et la même métrique principale, vous constaterez probablement des variations dans chaque score de métrique finale d’expériences, en raison de ces facteurs. 

### <a name="view-experiment-details"></a>Afficher les détails de l'expérience

L’écran **Détails de l’exécution** ouvre l’onglet **Détails**. Cet écran vous montre un récapitulatif de l’exécution de l’expérience, notamment une barre d’état en haut à côté du numéro de l’exécution. 

L’onglet **Modèles** contient une liste des modèles créés affichés selon leur score de métrique. Par défaut, le modèle qui obtient la valeur la plus élevée d’après la métrique choisie figure en haut de la liste. À mesure que le travail de formation essaie plus de modèles, ceux-ci sont ajoutés à la liste. Utilisez cela pour obtenir une comparaison rapide des métriques des modèles déjà produits.

![Détails de l’exécution](./media/how-to-use-automated-ml-for-ml-models/explore-models.gif)

### <a name="view-training-run-details"></a>Afficher les détails de l'exécution de formation

Explorez les modèles terminés pour afficher les détails de l’exécution de l’entraînement. Sous l’onglet **Modèle**, affichez les détails comme un résumé du modèle et les hyperparamètres utilisés pour le modèle sélectionné. 

[![Détails des hyperparamètres](media/how-to-use-automated-ml-for-ml-models/hyperparameter-button.png)](media/how-to-use-automated-ml-for-ml-models/hyperparameter-details.png)

 Vous pouvez également consulter les graphiques de métriques de performances propres au modèle sous l’onglet **Métriques**. [Découvrez-en plus sur les graphiques](how-to-understand-automated-ml.md).

![Détails d'itération](media/how-to-use-automated-ml-for-ml-models/iteration-details-expanded.png)

Dans l’onglet Transformation des données, vous pouvez voir un diagramme des techniques de prétraitement des données, d’ingénierie des caractéristiques et de mise à l’échelle ainsi que l’algorithme Machine Learning qui ont été appliqués pour générer ce modèle.

>[!IMPORTANT]
> L’onglet Transformation des données est en préversion. Cette fonctionnalité doit être considérée comme [expérimentale](/python/api/overview/azure/ml/#stable-vs-experimental) et peut changer à tout moment.

![Transformation des données](./media/how-to-use-automated-ml-for-ml-models/data-transformation.png)

## <a name="model-explanations-preview"></a>Explications de modèle (préversion)

Pour mieux comprendre votre modèle, vous pouvez regarder les caractéristiques des données (brutes ou traitées) qui ont influencé les prédictions du modèle avec le tableau de bord des explications de modèle. 

Le tableau de bord des explications de modèle fournit une analyse globale du modèle qui a fait l'objet de l'apprentissage, avec ses prédictions et explications. Il vous permet également d'explorer un point de données particulier et l'importance des caractéristiques individuelles. [En savoir plus sur les visualisations du tableau de bord des explications](how-to-machine-learning-interpretability-aml.md#visualizations).

Pour obtenir des explications sur un modèle particulier : 

1. Sous l'onglet **Modèles**, sélectionnez le modèle que vous souhaitez comprendre. 
1. Sélectionnez le bouton **Expliquer le modèle** et fournissez un calcul utilisable pour générer les explications.
1. Consultez l'état dans l'onglet **Exécutions enfants**. 
1. Accédez ensuite à l'onglet **Explications (préversion)** qui contient le tableau de bord des explications. 

    ![Tableau de bord des explications de modèle](media/how-to-use-automated-ml-for-ml-models/model-explanation-dashboard.png)

## <a name="deploy-your-model"></a>Déployer votre modèle

Une fois le meilleur modèle disponible, déployez-le en tant que service web pour prédire de nouvelles données.

>[!TIP]
> Si vous cherchez à déployer un modèle qui a été généré par le biais du package `automl` avec le Kit de développement logiciel (SDK) Python, vous devez [enregistrer votre modèle](how-to-deploy-and-where.md?tabs=python#register-a-model-from-an-azure-ml-training-run-1) dans l’espace de travail. 
>
> Une fois que vous avez enregistré le modèle, recherchez-le dans Studio en sélectionnant **Modèles** dans le volet gauche. Une fois que vous avez ouvert votre modèle, vous pouvez sélectionner le bouton **Déployer** en haut de l’écran, puis suivre les instructions décrites à l’**étape 2** de la section **Déployer votre modèle**.

Machine Learning automatisé vous aide à déployer le modèle sans écrire de code :

1. Vous disposez de plusieurs options pour le déploiement. 

    + Option 1 : Déployez le meilleur modèle en fonction des critères de mesure que vous avez définis. 
        1. Une fois l’expérience terminée, revenez à la page d’exécution du parent en sélectionnant **Exécution 1** en haut de votre écran. 
        1.  Sélectionnez le modèle figurant dans la section **Résumé du meilleur modèle**. 
        1. Sélectionnez **Déployer** en haut à gauche de la fenêtre. 

    + Option n°2 : Pour déployer une itération de modèle spécifique à partir de cette expérience.
        1. Sélectionner le modèle souhaité sous l’onglet **Modèles**
        1. Sélectionnez **Déployer** en haut à gauche de la fenêtre.

1. Renseignez le volet **Déployer le modèle**.

    Champ| Valeur
    ----|----
    Nom| Entrez un nom unique pour votre déploiement.
    Description| Entrez une description pour mieux identifier le but de ce déploiement.
    Type de capacité de calcul| Sélectionnez le type de point de terminaison que vous voulez déployer : *Azure Kubernetes Service (AKS)* ou *Azure Container Instance (ACI)* .
    Nom du calcul| *S’applique uniquement à AKS :* Sélectionnez le nom du cluster AKS sur lequel vous voulez effectuer le déploiement.
    Activer l’authentification | Sélectionnez cette option pour l’authentification basée sur des jetons ou sur des clés.
    Utiliser les ressources d’un déploiement personnalisé| Activez cette fonctionnalité si vous voulez télécharger votre propre script de scoring et votre propre fichier d’environnement. [Découvrez plus d’informations sur les scripts de scoring](how-to-deploy-and-where.md).

    >[!Important]
    > Les noms de fichiers sont limités à 32 caractères et doivent commencer et se terminer par des caractères alphanumériques. Ils peuvent inclure des tirets, des traits de soulignement, des points et des caractères alphanumériques. Les espaces ne sont pas autorisés.

    Le menu *Avancé* offre des fonctionnalités de déploiement par défaut, comme la [collecte de données](how-to-enable-app-insights.md) et des paramètres d’utilisation des ressources. Si vous souhaitez remplacer ces valeurs par défaut, faites-le dans ce menu.

1. Sélectionnez **Déployer**. Le déploiement peut prendre environ 20 minutes.
    Une fois le déploiement commencé, l’onglet **Résumé du modèle** s’affiche. Consultez la progression du déploiement sous la section **État du déploiement**. 

Vous disposez maintenant d’un service web opérationnel pour générer des prédictions ! Vous pouvez tester les prédictions en interrogeant le service à partir du [support Azure Machine Learning intégré de Power BI](/power-bi/connect-data/service-aml-integrate?context=azure%2fmachine-learning%2fcontext%2fml-context).

## <a name="next-steps"></a>Étapes suivantes

* [En savoir plus sur l'utilisation d'un service web](how-to-consume-web-service.md).
* [Comprendre les résultats du Machine Learning](how-to-understand-automated-ml.md).
* [En savoir plus sur Machine Learning automatisé](concept-automated-ml.md) et Azure Machine Learning.
