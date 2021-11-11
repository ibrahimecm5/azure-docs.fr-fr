---
title: 'ML Studio (classique) : Migrer vers Azure Machine Learning'
description: Migrez de Studio (classique) vers Azure Machine Learning pour bénéficier d’une plateforme moderne de science des données.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: xiaoharper
ms.author: zhanxia
ms.date: 10/21/2021
ms.openlocfilehash: 384b1b11e272cab8386a5a4f3344c36e99959254
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131561931"
---
# <a name="migrate-to-azure-machine-learning"></a>Migrer vers Azure Machine Learning 

> [!IMPORTANT]
> Le support de Machine Learning Studio (classique) prend fin le 31 août 2024. Nous vous recommandons de passer à [Azure Machine Learning](./overview-what-is-azure-machine-learning.md) avant cette date.
>
> À partir du 1er décembre 2021, vous ne pourrez plus créer de nouvelles ressources Machine Learning Studio (classique). Jusqu’au 31 août 2024, vous pouvez continuer à utiliser les ressources Machine Learning Studio (classique) existantes.  
>
> La documentation ML Studio (classique) est en cours de retrait et ne sera probablement plus mise à jour.

Découvrez comment migrer de Studio (classique) vers Azure Machine Learning. Azure Machine Learning fournit une plateforme moderne de science des données qui combine les approches sans code et Code First.

Ceci est un guide décrivant comment opérer une migration « lift-and-shift ». Si vous souhaitez optimiser un flux de travail ML existant ou moderniser une plateforme de Machine Learning, consultez l’[infrastructure d’adoption d’Azure Machine Learning](https://aka.ms/mlstudio-classic-migration-repo) pour obtenir des ressources supplémentaires, dont des outils d’enquête numérique, des feuilles de calcul et des modèles de planification.

![Infrastructure d’adoption d’Azure Machine Learning](./media/migrate-overview/aml-adoption-framework.png)

## <a name="recommended-approach"></a>Approche recommandée

Pour effectuer la migration vers Azure Machine Learning, nous vous recommandons l’approche suivante :

> [!div class="checklist"]
> * Étape 1 : Évaluer Azure Machine Learning
> * Étape 2 : Définir une stratégie et planifier
> * Étape 3 : Régénérer des expériences et des services web
> * Étape 4 : Intégrer des applications clientes
> * Étape 5 : Nettoyer les ressources Studio (classique)
> * Étape 6 : Examiner et développer des scénarios


## <a name="step-1-assess-azure-machine-learning"></a>Étape 1 : Évaluer Azure Machine Learning
1. Apprenez-en davantage sur [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/), ses avantages, ses coûts et son architecture.

1. Comparez les capacités d’Azure Machine Learning et de Studio (classique).

    >[!NOTE]
    > La caractéristique du **concepteur** dans Azure Machine Learning offre une expérience de glisser-déplacer similaire à celle de Studio (classique). Toutefois, Azure Machine Learning propose également des [workflows de Code First](concept-model-management-and-deployment.md) robustes comme alternative. Cette série sur la migration est axée sur le concepteur, car c’est ce qui ressemble le plus à l’expérience de Studio (classique).

    [!INCLUDE [aml-compare-classic](../../includes/machine-learning-compare-classic-aml.md)]

3. Vérifiez que vos modules Studio (classique) essentiels sont pris en charge dans le concepteur d’Azure Machine Learning. Pour plus d’informations, consultez le tableau [Correspondance des composants de Studio (classique) et du concepteur](#studio-classic-and-designer-component-mapping) ci-dessous.

4. [Créez un espace de travail Azure Machine Learning](how-to-manage-workspace.md?tabs=azure-portal).

## <a name="step-2-define-a-strategy-and-plan"></a>Étape 2 : Définir une stratégie et planifier

1. Définissez les justifications métier et les résultats attendus.
1. Aligner un plan d’adoption d’Azure Machine Learning actionnable sur les résultats métier.
1. Préparez les personnes, les processus et les environnements au changement.

Consultez l’[Infrastructure d’adoption d’Azure Machine Learning](https://aka.ms/mlstudio-classic-migration-repo) pour la planification des ressources, dont un modèle de document de planification. 

## <a name="step-3-rebuild-your-first-model"></a>Étape 3 : Régénérer votre premier modèle

Après avoir défini une stratégie, migrez votre premier modèle.

1. [Migrez des jeux de données vers Azure Machine Learning](migrate-register-dataset.md).
1. Utilisez le concepteur pour [reconstruire des expériences](migrate-rebuild-experiment.md).
1. Utilisez le concepteur pour [redéployer des services web](migrate-rebuild-web-service.md).

    >[!NOTE]
    > Azure Machine Learning prend également en charge les flux de travail de type « Code First » pour les [jeux de données](how-to-create-register-datasets.md), la [formation](how-to-set-up-training-targets.md) et le [déploiement](how-to-deploy-and-where.md).

## <a name="step-4-integrate-client-apps"></a>Étape 4 : Intégrer des applications clientes

1. Modifiez les applications clientes qui appellent des services web Studio (classique) pour qu’elles appellent vos nouveaux [points de terminaison Azure Machine Learning](migrate-rebuild-integrate-with-client-app.md).

## <a name="step-5-cleanup-studio-classic-assets"></a>Étape 5 : Nettoyer les ressources Studio (classique)

1. [Nettoyez les ressources Studio (classique)](./classic/export-delete-personal-data-dsr.md) pour éviter les frais supplémentaires. Vous souhaiterez peut-être conserver les ressources en secours jusqu’à ce que vous ayez validé les charges de travail Azure Machine Learning.

## <a name="step-6-review-and-expand-scenarios"></a>Étape 6 : Examiner et développer des scénarios

1. Examinez la migration du modèle pour découvrir les meilleures pratiques et valider les charges de travail.
1. Développez des scénarios et migrez des charges de travail supplémentaires vers Azure Machine Learning.


## <a name="studio-classic-and-designer-component-mapping"></a>Correspondance des composants de Studio (classique) et du concepteur

Consultez le tableau suivant pour voir les modules à utiliser lors de la reconstruction d’expériences Studio (classique) dans le concepteur.


> [!IMPORTANT]
> Le concepteur implémente des modules par le biais de packages Python open source plutôt que de packages C# tels que Studio (classique). En raison de cette différence, la sortie des composants du concepteur peut varier légèrement de leurs équivalents dans Studio (classique).


|Catégorie|Module Studio (classique)|Composant du concepteur de remplacement|
|--------------|----------------|--------------------------------------|
|Entrée et sortie de données|- Entrer des données manuellement </br> - Exporter des données </br> - Importer des données </br> - Charger un modèle formé </br> - Décompresser des jeux de données compressés|- Entrer des données manuellement </br> - Exporter des données </br> - Importer des données|
|Conversion de formats de données|- Convertir au format CSV </br> - Convertir en jeu de données </br> - Convertir au format ARFF </br> - Convertir au format SVMLight </br> - Convertir au format TSV|- Convertir au format CSV </br> - Convertir en jeu de données|
|Transformation de données – Manipulation|- Ajouter des colonnes</br> - Ajouter des lignes </br> - Appliquer une transformation SQL </br> - Nettoyer les données manquantes </br> - Convertir en valeurs d’indicateur </br> - Modifier des métadonnées </br> - Joindre des données </br> - Supprimer les lignes en double </br> - Sélectionner des colonnes dans le jeu de données </br> - Sélectionner une transformation de colonnes </br> - SMOTE </br> - Grouper les valeurs catégorielles|- Ajouter des colonnes</br> - Ajouter des lignes </br> - Appliquer une transformation SQL </br> - Nettoyer les données manquantes </br> - Convertir en valeurs d’indicateur </br> - Modifier des métadonnées </br> - Joindre des données </br> - Supprimer les lignes en double </br> - Sélectionner des colonnes dans le jeu de données </br> - Sélectionner une transformation de colonnes </br> - SMOTE|
|Transformation des données – Mise à l’échelle et réduction |- Détourer les valeurs </br> - Grouper des données dans des compartiments </br> - Normaliser des données </br>- Analyse des composants principaux |- Détourer les valeurs </br> - Grouper des données dans des compartiments </br> - Normaliser des données|
|Transformation des données – Échantillon et fractionnement|- Partition et échantillon </br> - Fractionner les données|- Partition et échantillon </br> - Fractionner les données|
|Transformation de données – Filtre |- Appliquer un filtre </br> - Filtre FIR </br> - Filtre IIR </br> - Filtre médian </br> - Filtre de moyenne mobile </br> - Filtre de seuil </br> - Filtre défini par l’utilisateur||
|Transformation de données – Apprentissage avec comptages |- Créer une transformation de comptage </br> - Exporter une table de comptage </br> - Importer une table de comptage </br> - Fusionner une transformation de comptage</br>  - Modifier les paramètres de table de comptage||
|Sélection de caractéristiques |- Sélection de caractéristiques par filtrage </br> - Analyse discriminante linéaire de Fisher  </br> - Importance d’une caractéristique de permutation |- Sélection de caractéristiques par filtrage </br>  - Importance d’une caractéristique de permutation|
| Modèle – Classification| - Forêt d’arbres de décision multiclasse </br> - Jungle d’arbres de décision multiclasse  </br> - Régression logistique multiclasse  </br>- Réseau neuronal multiclasse  </br>- One-vs-All Multiclass </br>-Perceptron moyenné à deux classes </br>- Machine à points de Bayes à deux classes </br>- Arbre de décision optimisé à deux classes  </br> - Forêt d’arbres de décision à deux classes  </br> - Jungle d’arbres de décision à deux classes  </br> - SVM localement profonde à deux classes </br> - Régression logistique à deux classes  </br> - Réseau neuronal à deux classes </br> - Machine à vecteurs de support à deux classes  | - Forêt d’arbres de décision multiclasse </br>  - Arbre de décision optimisé multiclasse  </br> - Régression logistique multiclasse </br> - Réseau neuronal multiclasse </br> - One-vs-All Multiclass  </br> -Perceptron moyenné à deux classes  </br> - Arbre de décision optimisé à deux classes  </br> - Forêt d’arbres de décision à deux classes </br>- Régression logistique à deux classes </br> - Réseau neuronal à deux classes </br>- Machine à vecteurs de support à deux classes  |
| Modèle – Clustering| - Clustering k-moyennes| - Clustering k-moyennes|
| Modèle – Régression| - Régression linéaire bayésienne  </br> - Régression d’arbre de décision optimisé  </br>- Régression de forêt d’arbres de décision  </br> - Régression quantile de forêt rapide  </br> - Régression linéaire </br> - Régression de réseau neuronal </br> - Régression ordinale - Régression de Poisson| - Régression d’arbre de décision optimisé  </br>- Régression de forêt d’arbres de décision  </br> - Régression quantile de forêt rapide </br> - Régression linéaire  </br> - Régression de réseau neuronal </br> - Régression de Poisson|
| Modèle – Détection d’anomalie| - SVM à une classe  </br> - Détection d’anomalie reposant sur l’ACP | - Détection d’anomalie reposant sur l’ACP|
| Apprentissage automatique – Évaluation  | - Modèle de validation croisée  </br>- Évaluer le modèle  </br>- Évaluer le générateur de recommandations | - Modèle de validation croisée  </br>- Évaluer le modèle </br> - Évaluer le générateur de recommandations|
| Apprentissage automatique – Formation| - Nettoyer le clustering  </br> - Effectuer l’apprentissage d’un modèle de détection d’anomalie </br>- Effectuer l’apprentissage d’un modèle de clustering  </br> - Effectuer l’apprentissage du générateur de recommandations Matchbox</br> Entraîner le modèle  </br>- Régler les hyperparamètres du modèle| - Effectuer l’apprentissage d’un modèle de détection d’anomalie  </br> - Effectuer l’apprentissage d’un modèle de clustering </br> - Effectuer l’apprentissage d’un modèle</br> - Effectuer l’apprentissage d’un modèle PyTorch  </br>- Effectuer l’apprentissage du générateur de recommandations SVD  </br>- Effectuer l’apprentissage du générateur de recommandations Wide and Deep </br>- Régler les hyperparamètres du modèle|
| Apprentissage automatique – Score| - Appliquer une transformation  </br>- Attribuer des données à des clusters  </br>- Effectuer le scoring du générateur de recommandations Matchbox </br> - Effectuer le scoring d’un modèle|- Appliquer une transformation  </br> - Attribuer des données à des clusters </br> - Effectuer le scoring d’un modèle d’image  </br> - Effectuer le scoring d’un modèle </br>- Effectuer le scoring du générateur de recommandations SVD </br> \- Effectuer le scoring du générateur de recommandations Wide and Deep|
| Modules de la bibliothèque OpenCV| - Importer des images </br>- Classification préformée d’images en cascade | |
| Modules de langage Python| - Exécuter un script Python| - Exécuter un script Python  </br> - Créer un modèle Python |
| Modules linguistiques R  | - Exécuter un script R  </br> - Créer un modèle R| - Exécuter un script R|
| Fonctions statistiques | - Appliquer une opération mathématique </br>- Calculer des statistiques élémentaires  </br>- Calculer une corrélation linéaire  </br>- Évaluer une fonction de probabilité  </br>- Remplacer des valeurs discrètes  </br>- Résumer les données  </br>- Tester une hypothèse avec le test de Student| - Appliquer une opération mathématique  </br>- Résumer les données|
| Analyse de texte| - Détecter des langues  </br>- Extraire les phrases clés d’un texte  </br>- Extraire les caractéristiques de n-grammes du texte  </br>- Hachage des caractéristiques </br>- Allocation de Dirichlet latente  </br>- Reconnaissance d’entité nommée </br>- Pré-traiter le texte  </br>- Effectuer le scoring d’un modèle Vowpal Wabbit version 7 à 10  </br>- Effectuer le scoring d’un modèle Vowpal Wabbit version 8 </br>- Effectuer l’apprentissage d’un modèle Vowpal Wabbit version 7 à 10  </br>- Effectuer l’apprentissage d’un modèle Vowpal Wabbit version 8 |- Vectoriser des mots </br> - Extraire les caractéristiques de n-grammes du texte </br>- Hachage des caractéristiques  </br>- Allocation de Dirichlet latente </br>- Pré-traiter le texte  </br>- Effectuer le scoring d’un modèle Vowpal Wabbit </br> - Effectuer l’apprentissage d’un modèle Vowpal Wabbit|
| Série chronologique| - Détection d’anomalie de série chronologique | |
| Service Web | - Entrée </br> - Sortie | - Entrée </br>  Sortie|
| Vision par ordinateur| | - Appliquer une transformation d’image </br> - Convertir en répertoire d’images </br> - Lancer une transformation d’image </br> - Fractionner un répertoire d’images  </br> - Classification d’images DenseNet   </br>- Classification d’images ResNet |

Pour plus d’informations sur l’utilisation des différents composants du concepteur, consultez la [référence relative aux composants du concepteur](./component-reference/component-reference.md).

### <a name="what-if-a-designer-component-is-missing"></a>Que se passe-t-il si un composant de concepteur est manquant ?

Le concepteur d’Azure Machine Learning contient les modules les plus populaires de Studio (classique). Il comprend également de nouveaux modules qui tirent parti des dernières techniques de Machine Learning. 

Si votre migration est bloquée en raison de modules manquants dans le concepteur, contactez-nous en [créant un ticket de support](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="example-migration"></a>Exemple de migration

La migration d’expérience suivante met en évidence certaines des différences entre Studio (classique) et Azure Machine Learning.

### <a name="datasets"></a>Groupes de données

Dans Studio (classique), les **jeux de données** ont été enregistrés dans votre espace de travail et peuvent uniquement être utilisés par Studio (classique).

![automobile-price-classic-dataset](./media/migrate-overview/studio-classic-dataset.png)

Dans Azure Machine Learning, les **jeux de données** sont inscrits dans l’espace de travail et peuvent être utilisés sur l’ensemble d’Azure Machine Learning. Pour plus d’informations sur les avantages des jeux de données Azure Machine Learning, consultez [Sécuriser l’accès aux données](concept-data.md#reference-data-in-storage-with-datasets).

![automobile-price-aml-dataset](./media/migrate-overview/aml-dataset.png)

### <a name="pipeline"></a>Pipeline

Dans Studio (classique), les **expériences** contenaient la logique de traitement de votre travail. Vous avez créé des expériences avec des modules de glisser-déplacer.

![automobile-price-classic-experiment](./media/migrate-overview/studio-classic-experiment.png)

Dans Azure Machine Learning, les **pipelines** contiennent la logique de traitement de votre travail. Vous pouvez créer des pipelines à l’aide de modules de glisser-déplacer ou en écrivant du code.

![automobile-price-aml-pipeline](./media/migrate-overview/aml-pipeline.png)

### <a name="web-service-endpoint"></a>Point de terminaison de service Web

Studio (classique) utilisait l’**API REQUEST/RESPOND** pour la prédiction en temps réel et l’**API BATCH EXECUTION** pour le réentraînement ou la prédiction par lot.

![automobile-price-classic-webservice](./media/migrate-overview/studio-classic-web-service.png)

Azure Machine Learning utilise des **points de terminaison en temps réel** pour la prédiction en temps réel et des **points de terminaison de pipeline** pour le réentraînement ou la prédiction par lot.

![automobile-price-aml-endpoint](./media/migrate-overview/aml-endpoint.png)

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez découvert les principales exigences requises pour effectuer une migration vers Azure Machine Learning. Pour obtenir des instructions détaillées, consultez les autres articles de la série sur la migration Studio (classique) :

1. **Vue d’ensemble de la migration**.
1. [Migrer un jeu de données](migrate-register-dataset.md).
1. [Reconstruire un pipeline de formation Studio (classique)](migrate-rebuild-experiment.md).
1. [Reconstruire un service web Studio (classique)](migrate-rebuild-web-service.md).
1. [Intégrer un service web Azure Machine Learning à des applications clientes](migrate-rebuild-integrate-with-client-app.md).
1. [Migrer Exécuter un script R](migrate-execute-r-script.md).

Consultez l’[ infrastructure d’adoption d’Azure Machine Learning](https://aka.ms/mlstudio-classic-migration-repo) pour obtenir des ressources de migration supplémentaires.