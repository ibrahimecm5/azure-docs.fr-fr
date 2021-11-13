---
title: 'Tutoriel : AutoML – Entraînement de modèles de classification sans code'
titleSuffix: Azure Machine Learning
description: Entraînez un modèle de classification sans écrire une seule ligne de code avec le ML automatisé Azure Machine Learning dans l’interface utilisateur du studio.
services: machine-learning
ms.service: machine-learning
ms.subservice: automl
ms.topic: tutorial
author: cartacioS
ms.author: sacartac
ms.reviewer: nibaccam
ms.date: 10/21/2021
ms.custom: automl, FY21Q4-aml-seo-hack, contperf-fy21q4
ms.openlocfilehash: 6c080e89dab3193f373b0a1e194cb18661132bbd
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131559615"
---
# <a name="tutorial-train-a-classification-model-with-no-code-automl-in-the-azure-machine-learning-studio"></a>Tutoriel : Entraînement d’un modèle de classification avec AutoML sans code dans Azure Machine Learning studio

Découvrez comment entraîner un modèle de classification avec AutoML sans code en utilisant le ML automatisé Azure Machine Learning dans Azure Machine Learning studio. Ce modèle de classification prédit si un client va souscrire à un compte à terme auprès d’une institution financière.

Avec le Machine Learning automatisé, vous pouvez automatiser les tâches fastidieuses. Le machine learning automatisé itère rapidement sur de nombreuses combinaisons d’algorithmes et d’hyperparamètres pour vous aider à trouver le meilleur modèle basé sur une métrique de réussite de votre choix.

Dans ce tutoriel, vous n’allez pas écrire de code. Vous allez utiliser l’interface du studio pour effectuer l’entraînement.  Vous allez apprendre à effectuer les tâches suivantes :

> [!div class="checklist"]
> * Créez un espace de travail Machine Learning.
> * Exécuter une expérience de machine learning automatisé.
> * Explorez les détails du modèle.
> * Déployer le modèle recommandé.

Essayez également le machine learning automatisé pour ces autres types de modèles :

* Pour obtenir un exemple sans code de prévision, consultez [Tutoriel : Prévision de la demande et AutoML](tutorial-automated-ml-forecast.md).
* Pour un exemple Code First d’un modèle de régression, consultez [Tutoriel : Modèle de régression avec AutoML](tutorial-auto-train-models.md).

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/).

* Téléchargez le fichier de données [**bankmarketing_train.csv**](https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_train.csv). La colonne **y** indique si un client a souscrit à un compte à terme, qui est ensuite identifié comme colonne cible pour les prédictions de ce tutoriel. 

## <a name="create-a-workspace"></a>Créer un espace de travail

Un espace de travail Azure Machine Learning est une ressource fondamentale du cloud que vous utilisez pour expérimenter, entraîner et déployer des modèles Machine Learning. Il lie votre abonnement Azure et votre groupe de ressources à un objet facile à consommer dans le service. 

Il existe de nombreuses [façons de créer un espace de travail](how-to-manage-workspace.md). Dans ce tutoriel, vous allez créer un espace de travail par le biais du portail Azure, une console web pour la gestion de vos ressources Azure.

[!INCLUDE [aml-create-portal](../../includes/aml-create-in-portal.md)]

>[!IMPORTANT] 
> Prenez note de votre **espace de travail** et de votre **abonnement**. Vous en aurez besoin pour être sûr de créer votre expérience au bon endroit. 

## <a name="sign-in-to-the-studio"></a>Se connecter au studio

Vous effectuez les étapes de configuration et d’exécution d’expérience suivantes via Azure Machine Learning Studio, accessible à l’adresse https://ml.azure.com. Cette interface web centralisée comprend des outils de machine learning permettant de mettre en œuvre des scénarios de science des données pour les utilisateurs de science des données de tous niveaux de compétence. Studio n’est pas prise en charge par les navigateurs Internet Explorer.

1. Connectez-vous à [Azure Machine Learning Studio](https://ml.azure.com).

1. Sélectionnez votre abonnement et l’espace de travail que vous avez créé.

1. Sélectionnez **Prise en main**.

1. Dans le volet gauche, sélectionnez **Automated ML** (ML automatisé) sous la section **Author** (Créer).

   Puisqu’il s’agit de votre première expérience de ML automatisé, vous verrez une liste vide et des liens vers la documentation.

   ![Page de prise en main](./media/tutorial-first-experiment-automated-ml/get-started.png)

1. Sélectionnez **+Nouvelle exécution de ML automatisé**. 

## <a name="create-and-load-dataset"></a>Créer et charger un jeu de données

Avant de configurer votre expérience, chargez votre fichier de données dans votre espace de travail sous la forme d’un jeu de données Azure Machine Learning. De cette façon, vous pouvez vérifier que la mise en forme de vos données convient à votre expérience.

1. Pour créer un jeu de données, sélectionnez **À partir de fichiers locaux** dans la liste déroulante **+ Créer un jeu de données**. 

    1. Dans le formulaire **Informations de base**, donnez un nom unique à votre jeu de données et indiquez éventuellement une description. L’interface de ML automatisé ne prend actuellement en charge que les jeux de données tabulaires. Le type de jeu de données par défaut doit donc être *Tabulaire*.

    1. Sélectionnez **Suivant** en bas à gauche.

    1. Dans le formulaire **Sélection d’un magasin de données et de fichiers**, sélectionnez le magasin de données par défaut qui a été automatiquement configuré durant la création de votre espace de travail : **workspaceblobstore (Stockage Blob Azure)** . C’est là que vous chargerez votre fichier de données pour le rendre accessible à votre espace de travail.

    1. Sélectionnez **Parcourir**.
    
    1. Choisissez le fichier **bankmarketing_train.csv** sur votre ordinateur local. Il s’agit du fichier que vous avez téléchargé en tant que [prérequis](https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_train.csv).

    1. Donnez un nom unique à votre jeu de données et indiquez éventuellement une description. 

    1. En bas à gauche, sélectionnez **Suivant** pour le charger dans le conteneur par défaut qui a été configuré automatiquement lors de la création de votre espace de travail.  
    
       Une fois le chargement terminé, le formulaire **Paramètres et aperçu** est prérenseigné en fonction du type de fichier. 
       
    1. Vérifiez que le formulaire **Settings and preview** est renseigné comme ci-dessous, puis sélectionnez **Next** (Suivant).
        
        Champ|Description| Valeur pour le tutoriel
        ---|---|---
        Format de fichier|Définit la disposition et le type des données stockées dans un fichier.| Delimited
        Délimiteur|Un ou plusieurs caractères utilisés pour spécifier la limite entre&nbsp; des régions distinctes et indépendantes dans du texte brut ou d’autres flux de données. |Comma
        Encodage|Identifie la table de schéma bits/caractères à utiliser pour lire votre jeu de données.| UTF-8
        En-têtes de colonne| Indique la façon dont les éventuels en-têtes du jeu de données sont traités.| Tous les fichiers ont les mêmes en-têtes
        Ignorer les lignes | Indique le nombre éventuel de lignes ignorées dans le jeu de données.| None

    1. Le formulaire **Schema** permet de configurer davantage vos données pour cette expérience. Pour cet exemple, sélectionnez le bouton bascule correspondant à **day_of_week**, afin de ne pas l’inclure. Sélectionnez **Suivant**.
         ![Formulaire de schéma](./media/tutorial-first-experiment-automated-ml/schema-tab-config.gif)
    1. Dans le formulaire **Confirmer les détails**, vérifiez que les informations correspondent à celles qui ont été précédemment renseignées sur les formulaires **Informations de base, magasin de données et sélection de fichiers** et **Paramètres et aperçu**.
    
    1. Sélectionnez **Créer** pour terminer la création de votre jeu de données.
    
    1. Sélectionnez votre jeu de données une fois qu’il apparaît dans la liste.
    
    1. Passez en revue l’**Aperçu des données** pour vérifier que vous n’avez pas inclus **day_of_week**, puis sélectionnez **Fermer**.

    1. Sélectionnez **Suivant**.

## <a name="configure-run"></a>Configurer l’exécution

Une fois que vous avez chargé et configuré vos données, vous pouvez configurer votre expérience. Cette configuration comprend des tâches de conception d’expérience, comme la sélection de la taille de votre environnement de calcul et la spécification de la colonne que vous voulez prédire. 

1. Sélectionnez la case d’option **Créer**.

1. Remplissez le formulaire **Configurer l’exécution** comme suit :
    1. Entrez le nom suivant pour l’expérience : `my-1st-automl-experiment`

    1. Sélectionnez **y** comme colonne cible, ce que vous souhaitez prédire. Cette colonne indique si le client a souscrit à un compte de dépôt à terme.
    
    1. Sélectionnez **Cluster de calcul** comme type de calcul. 
    
    1.  Sélectionnez **+Nouveau** pour configurer votre cible de calcul. Une cible de calcul est un environnement de ressources local ou informatique utilisé pour exécuter votre script d’entraînement ou pour héberger votre déploiement de service. Pour cette expérience, nous utilisons un calcul informatique. 
        1. Remplissez le formulaire **Sélectionner une machine virtuelle** pour configurer votre calcul.

            Champ | Description | Valeur pour le tutoriel
            ----|---|---
            Priorité&nbsp;de la machine&nbsp;virtuelle |Sélectionnez la priorité que doit avoir votre expérience| Dédié
            Type de&nbsp;machine&nbsp;virtuelle| Sélectionnez le type de machine virtuelle pour votre calcul.|Processeur (CPU)
            Taille de la&nbsp;machine&nbsp;virtuelle| Sélectionnez la taille de la machine virtuelle pour votre calcul. La liste des tailles recommandées qui est fournie dépend de vos données et du type de l’expérience. |Standard_DS12_V2
        
        1. Sélectionnez **Suivant** pour renseigner le formulaire **Configurer les paramètres**.
        
            Champ | Description | Valeur pour le tutoriel
            ----|---|---
            Nom du calcul |  Nom unique qui identifie votre contexte de calcul. | automl-compute
            Nombre minimal/maximal de nœuds| Pour profiler des données, vous devez spécifier un ou plusieurs nœuds.|Nœuds min. : 1<br>Nœuds max. : 6
            Secondes d’inactivité avant le scale-down | Durée d’inactivité avant que le cluster ne fasse l’objet d’un scale-down au nombre de nœuds minimal.|1800 (par défaut)
            Paramètres avancés | Paramètres pour configurer et autoriser un réseau virtuel pour votre expérience.| None               

        1. Sélectionnez **Créer** pour créer votre cible de calcul. 

            **Quelques minutes sont nécessaires pour achever l’opération**. 

             ![Page Paramètres](./media/tutorial-first-experiment-automated-ml/compute-settings.png)

        1. Une fois la création terminée, sélectionnez votre nouvelle cible de calcul dans la liste déroulante.

    1. Sélectionnez **Suivant**.

1. Dans le formulaire **Sélectionner la tâche et les paramètres**, effectuez la configuration pour votre expérience de Machine Learning automatisé en spécifiant les paramètres de configuration et le type de tâche de Machine Learning.
    
    1.  Sélectionnez **Classification** comme type de tâche de machine learning.

    1. Sélectionnez **Afficher des paramètres de configuration supplémentaires** et renseignez les champs comme suit. Ces paramètres permettent de mieux contrôler le travail d’entraînement. Sinon, les valeurs par défaut sont appliquées en fonction de la sélection de l’expérience et des données.

        Configurations&nbsp;supplémentaires|Description|Valeur&nbsp;pour&nbsp;le tutoriel
        ------|---------|---
        Métrique principale| Métrique d’évaluation selon laquelle l’algorithme de Machine Learning sera mesuré.|AUC_weighted
        Expliquer le meilleur modèle| Montre automatiquement l’explicabilité sur le meilleur modèle créé par le ML automatisé.| Activer
        Algorithmes bloqués | Algorithmes que vous souhaitez exclure du travail de formation| None
        Critère de sortie| Lorsqu’une condition est remplie, la tâche d’entraînement est arrêtée. |Durée&nbsp;de la tâche&nbsp;de formation : 1 <br> Seuil&nbsp;de score&nbsp;de métrique : None
        Validation | Choisissez un type de validation croisée et un nombre de tests.|Type de validation :<br>&nbsp;validation croisée&nbsp;k-fold <br> <br> Nombre de validations : 2
        Accès concurrentiel| Nombre maximal d’itérations parallèles exécutées par itération| Nombre maximal&nbsp;d’itérations&nbsp;simultanées : 5
        
        Sélectionnez **Enregistrer**.
    
1. Sélectionnez **Terminer** pour exécuter l’expérience. L’écran **Détails de l’exécution** s’ouvre et affiche l’**État de l’exécution** dans la partie supérieure au début de la préparation de l’expérience. Cet état est mis à jour à mesure que l’expérience progresse. Les notifications s’affichent également en haut à droite de Studio pour vous informer de l’état de votre expérience.

>[!IMPORTANT]
> La préparation nécessaire à l’exécution de l’expérience prend **10 à 15** minutes.
> Une fois que l’exécution a commencé, **2-3 minutes supplémentaires sont nécessaires pour chaque itération**.  <br> <br>
> Dans un environnement de production, cette durée est un peu plus longue. Toutefois, dans le cadre de ce tutoriel, nous vous suggérons de commencer à explorer les algorithmes testés dans l’onglet **Modèles** à mesure qu’ils se terminent, pendant que les autres sont encore en cours d’exécution. 

##  <a name="explore-models"></a>Explorer les modèles

Accédez à l’onglet **Modèles** pour voir les algorithmes (modèles) testés. Par défaut, les modèles sont classés par score de métrique à mesure qu’ils se terminent. Pour ce tutoriel, le modèle qui obtient le score le plus élevé d’après la métrique **AUC_weighted** choisie figure en haut de la liste.

En attendant que toutes les modèles d’expérience se terminent, sélectionnez le **Nom de l’algorithme** d’un modèle terminé pour explorer ses performances en détail. 

L’exemple suivant parcourt les onglets **Détails** et **Métriques** pour montrer les propriétés, les métriques et les graphiques de performances du modèle sélectionné. 

![Détails sur l’exécution de l’itération](./media/tutorial-first-experiment-automated-ml/run-detail.gif)

## <a name="model-explanations"></a>Explications de modèle

Pendant l’exécution des modèles, vous pouvez en profiter pour examiner les explications de modèle et constater quelles fonctionnalités de données (brutes ou générées) ont influencé les prédictions d’un modèle spécifique. 

Ces explications de modèle peuvent être générées à la demande et sont résumées dans le tableau de bord des explications de modèle contenu sous l’onglet **Explications (aperçu)** .

Pour générer des explications de modèle : 
 
1. Sélectionnez **Exécuter 1** en haut pour revenir à l’écran **Modèles**. 
1. Sélectionnez l’onglet **Modèles**.
1. Pour ce tutoriel, sélectionnez le premier modèle **MaxAbsScaler, LightGBM**.
1. Sélectionnez le bouton **Expliquer le modèle** en haut. À droite, le volet **Expliquer le modèle** s’affiche. 
1. Sélectionnez le cluster **automl-compute** que vous avez créé précédemment. Ce cluster de calcul lance une exécution enfant pour générer les explications du modèle.
1. Sélectionnez **Créer** en bas. Un message de réussite en vert s’affiche en haut de votre écran. 
    >[!NOTE]
    > L’exécution d’explicabilité dure généralement de 2 à 5 minutes.
1. Sélectionnez le bouton **Explications (aperçu)** . Cet onglet se remplit une fois que l’exécution d’explicabilité est effectuée.
1. Sur le côté gauche, développez le volet et sélectionnez la ligne « **RAW** » sous **Fonctionnalités**. 
1. Sélectionnez l’onglet **Agréger l’importance des caractéristiques** à droite. Ce graphique montre les fonctionnalités de données qui ont influencé les prédictions du modèle sélectionné. 

    Dans cet exemple, c’est la *durée* qui semble avoir le plus influencé les prédictions de ce modèle.
    
    ![Tableau de bord des explications de modèle](media/tutorial-first-experiment-automated-ml/model-explanation-dashboard.png)

## <a name="deploy-the-best-model"></a>Déployer le meilleur modèle

L’interface de machine learning automatisé vous permet de déployer le meilleur modèle comme service web en quelques étapes. Le déploiement consiste à intégrer le modèle pour qu’il puisse prédire de nouvelles données et identifier les domaines potentiels d’opportunités. 

Dans le cadre de cette expérience, le déploiement sur un service web signifie que l’établissement financier dispose désormais d’une solution web itérative et scalable pour identifier les clients potentiels d’un compte à terme. 

Vérifiez si l’exécution de votre expérience est terminée. Pour cela, revenez à la page d’exécution du parent en sélectionnant **Exécution 1** en haut de votre écran. Un état **Terminé** est affiché en haut à gauche de l’écran. 

Une fois l’expérience terminée, la page **Détails** est renseignée avec une section **Récapitulatif du meilleur modèle**. Dans ce contexte d’expérience, **VotingEnsemble** est considéré comme le meilleur modèle d’après la métrique **AUC_weighted**.  

Nous déployons ce modèle, mais nous vous informons que le déploiement prend 20 minutes environ. Le processus de déploiement comporte plusieurs étapes, notamment l’inscription du modèle, la génération de ressources et leur configuration pour le service web.

1. Sélectionnez **VotingEnsemble** pour ouvrir la page spécifique au modèle.

1. Sélectionnez le bouton **Déployer** en haut à gauche.

1. Renseignez le volet **Déployer un modèle** de la façon suivante :

    Champ| Valeur
    ----|----
    Nom du déploiement| my-automl-deploy
    Description du déploiement| Déploiement de ma première expérience de Machine Learning automatisé
    Type de capacité de calcul | Sélectionnez une instance de calcul Azure (ACI)
    Activer l’authentification| Désactivez. 
    Utiliser des déploiements personnalisés| Désactivez. Permet de générer automatiquement le fichier de pilote par défaut (script de scoring) et le fichier d’environnement. 
    
    Pour cet exemple, nous utilisons les valeurs par défaut fournies dans le menu *Avancé*. 

1. Sélectionnez **Déployer**.  

    Un message vert de réussite apparaît en haut de l’écran **Exécuter**, et dans le volet **Récapitulatif du modèle**, un message d’état s’affiche sous **État du déploiement**. Cliquez régulièrement sur **Actualiser** pour vérifier l’état du déploiement.
    
Vous disposez maintenant d’un service web opérationnel pour générer des prédictions. 

Passez aux [**Étapes suivantes**](#next-steps) pour en savoir plus sur l’utilisation de votre nouveau service web et tester vos prédictions à l’aide de la prise en charge d’Azure Machine Learning intégrée dans Power BI.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Les fichiers de déploiement sont plus volumineux que les fichiers de données et d’expérimentation. Le coût de leur stockage est donc plus élevé. Supprimez uniquement les fichiers de déploiement pour réduire les coûts associés à votre compte, ou si vous souhaitez conserver vos fichiers d’expérience et d’espace de travail. Dans le cas contraire, supprimez l’intégralité du groupe de ressources, si vous n’envisagez pas d’utiliser les fichiers.  

### <a name="delete-the-deployment-instance"></a>Supprimer l’instance de déploiement

Supprimez uniquement l’instance de déploiement d’Azure Machine Learning (accessible sur https:\//ml.azure.com/) si vous souhaitez conserver le groupe de ressources et l’espace de travail pour d’autres tutoriels et à des fins d’exploration. 

1. Accéder à [Azure Machine Learning](https://ml.azure.com/). Accédez à votre espace de travail et, à gauche dans le volet **Ressources**, sélectionnez **Points de terminaison**. 

1. Sélectionnez le déploiement à supprimer et sélectionnez **Supprimer**. 

1. Sélectionnez **Continuer**.

### <a name="delete-the-resource-group"></a>Supprimer le groupe de ressources

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel sur le machine learning automatisé, vous avez utilisé l’interface de ML automatisé d’Azure Machine Learning pour créer et déployer un modèle de classification. Pour plus d’informations et pour connaître les étapes suivantes, consultez ces articles :

> [!div class="nextstepaction"]
> [Utiliser un service web](/power-bi/connect-data/service-aml-integrate?context=azure%2fmachine-learning%2fcontext%2fml-context)

+ En savoir plus sur le [Machine Learning automatisé](concept-automated-ml.md).
+ Pour plus d’informations sur les métriques et les graphiques de classification, consultez l’article [Comprendre les résultats du Machine Learning automatisé](how-to-understand-automated-ml.md).
+ Découvrez-en plus sur la [caractérisation](how-to-configure-auto-features.md#featurization).
+ En savoir plus sur le [profilage des données](how-to-connect-data-ui.md#profile).


>[!NOTE]
> Ce jeu de données Bank Marketing est disponible sous licence [Creative Commons (CCO : domaine public)](https://creativecommons.org/publicdomain/zero/1.0/). Tous les droits du contenu individuel de la base de données sont concédés sous [licence du contenu de base de données](https://creativecommons.org/publicdomain/zero/1.0/) et sont disponibles sur [Kaggle](https://www.kaggle.com/janiobachmann/bank-marketing-dataset). Ce jeu de données était initialement disponible dans la [base de données Machine Learning UCI](https://archive.ics.uci.edu/ml/datasets/bank+marketing).<br><br>
> [Moro et al., 2014] S. Moro, P. Cortez et P. Rita. A Data-Driven Approach to Predict the Success of Bank Telemarketing. Decision Support Systems, Elsevier, 62:22-31, Juin 2014.
