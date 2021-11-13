---
title: 'ML Studio (classique) : Migrer vers Azure Machine Learning – Reconstruire une expérience'
description: Reconstruisez des expériences Studio (classique) dans le concepteur d’Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: xiaoharper
ms.author: zhanxia
ms.date: 10/21/2021
ms.openlocfilehash: fd0bb1394ce2ce8b8cf8d58ece5834f08e538eaa
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131556195"
---
# <a name="rebuild-a-studio-classic-experiment-in-azure-machine-learning"></a>Reconstruire une expérience Studio (classique) dans Azure Machine Learning

[!INCLUDE [ML Studio (classic) retirement](../../includes/machine-learning-studio-classic-deprecation.md)]

Dans cet article, vous allez apprendre à reconstruire une expérience ML Studio (classique) dans Azure Machine Learning. Pour plus d’informations sur la migration à partir de Studio (classique), consultez [l’article de présentation sur la migration](migrate-overview.md).

Les **expériences** Studio (classique) sont similaires aux **pipelines** dans Azure Machine Learning. Toutefois, dans Azure Machine Learning, les pipelines sont générés sur le même serveur principal qui alimente le Kit de développement logiciel (SDK). Cela signifie que deux options s’offrent à vous pour le développement Machine Learning : le concepteur de type « glisser-déplacer » ou les SDK de type « Code First ».

Pour plus d’informations sur la création de pipelines avec le SDK, consultez [Présentation des pipelines Azure Machine Learning](concept-ml-pipelines.md#building-pipelines-with-the-python-sdk).


## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Un espace de travail Azure Machine Learning. [Créez un espace de travail Azure Machine Learning](how-to-manage-workspace.md#create-a-workspace).
- Une expérience Studio (classique) à migrer.
- [Chargez votre jeu de données](migrate-register-dataset.md) sur Azure Machine Learning.

## <a name="rebuild-the-pipeline"></a>Reconstruire le pipeline

Après avoir [migré votre jeu de données vers Azure Machine Learning](migrate-register-dataset.md), vous êtes prêt à recréer votre expérience.

Dans Azure Machine Learning, le graphique visuel est appelé un **brouillon de pipeline**. Dans cette section, vous recréez votre expérience classique sous forme de brouillon de pipeline.

1. Accédez à Azure Machine Learning studio ([ml.azure.com](https://ml.azure.com)).
1. Dans le volet de navigation de gauche, sélectionnez **Concepteur** > **Modules prédéfinis faciles à utiliser**. ![Capture d’écran montrant comment créer un brouillon de pipeline.](./media/tutorial-designer-automobile-price-train-score/launch-designer.png)

1. Regénérez manuellement votre expérience avec des composants du concepteur.
    
    Consultez le [tableau de correspondance des modules](migrate-overview.md#studio-classic-and-designer-component-mapping) pour rechercher les modules de remplacement. Plusieurs des modules les plus populaires de Studio (classique) ont des versions identiques dans le concepteur.

    > [!Important]
    > Si votre expérience utilise le module Exécuter un script R, vous devez effectuer des étapes supplémentaires pour migrer votre expérience. Pour plus d’informations, consultez [Migrer des modules de script R](migrate-execute-r-script.md).

1. Ajustez les paramètres.
    
    Sélectionnez chaque module et ajustez les paramètres dans le panneau des paramètres du module à droite. Utilisez les paramètres pour recréer les fonctionnalités de votre expérience Studio (classique). Pour plus d’informations sur chaque module, consultez la [référence relative aux modules](./component-reference/component-reference.md).

## <a name="submit-a-run-and-check-results"></a>Envoyer une exécution et vérifier les résultats

Après avoir recréé votre expérience Studio (classique), il est temps de soumettre une **exécution de pipeline**.

Une exécution de pipeline s’exécute sur une **cible de calcul** rattachée à votre espace de travail. Vous pouvez définir une cible de calcul par défaut pour l’ensemble du pipeline, ou vous pouvez spécifier des cibles de calcul pour chaque module.

Lorsque vous envoyez une exécution à partir d’un brouillon de pipeline, elle se transforme en **exécution de pipeline**. Chaque exécution de pipeline est enregistrée et consignée Azure Machine Learning.

Pour définir une cible de calcul par défaut pour l’ensemble du pipeline :
1. Sélectionnez l’**icône en forme d’engrenage** ![Icône en forme d’engrenage dans le concepteur](./media/tutorial-designer-automobile-price-train-score/gear-icon.png) à côté du nom du pipeline.
1. Cliquez sur **Sélectionner une cible de calcul**.
1. Sélectionnez un calcul existant ou créez un nouveau calcul en suivant les instructions à l’écran.

Maintenant que votre cible de calcul est définie, vous pouvez envoyer une exécution de pipeline :

1. En haut du canevas, sélectionnez **Envoyer**.
1. Sélectionnez **Créer nouveau** pour créer une nouvelle expérience.
    
    Les expériences organisent les exécutions de pipeline similaires ensemble. Si vous exécutez un pipeline plusieurs fois, vous pouvez sélectionner la même expérience pour les exécutions successives. Cela est utile pour la journalisation et le suivi.
1. Entrez un nom d’expérience. Puis, sélectionnez **Envoyer**.

    La première exécution peut prendre jusqu’à 20 minutes. Comme les paramètres de calcul par défaut ont une taille de nœud minimale de 0, le concepteur doit allouer des ressources après une période d’inactivité. Les exécutions ultérieures prendront moins de temps, puisque les nœuds sont déjà alloués. Pour accélérer le temps d’exécution, vous pouvez créer une ressource de calcul avec une taille de nœud minimale de 1 ou plus.

Une fois l’exécution terminée, vous pouvez vérifier les résultats de chaque module :

1. Cliquez avec le bouton droit sur le module dont vous voulez voir la sortie.
1. Sélectionnez **Visualiser**, **Afficher la sortie** ou **Afficher le journal**.

    - **Visualiser** : Affiche un aperçu du jeu de données de résultats.
    - **Afficher la sortie** : Ouvre un lien vers l’emplacement de stockage de la sortie. À utiliser pour explorer ou télécharger la sortie. 
    - **Afficher le journal** : Affiche les journaux du pilote et du système. Utilisez le **70_driver_log** pour voir les informations relatives à votre script envoyé par l’utilisateur, telles que les erreurs et les exceptions.

> [!IMPORTANT]
> Les composants du concepteur utilisent des packages Python open source, alors qu’il s’agit de packages C# dans Studio (classique). Par conséquence, la sortie du module peut varier légèrement entre le concepteur et Studio (classique). 


## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à reconstruire une expérience Studio (classique) dans Azure Machine Learning. L’étape suivante consiste à [reconstruire les services web dans Azure Machine Learning](migrate-rebuild-web-service.md).


Consultez les autres articles de la série sur la migration Studio (classique) :

1. [Vue d’ensemble de la migration](migrate-overview.md).
1. [Migrer un jeu de données](migrate-register-dataset.md).
1. **Reconstruire un pipeline de formation Studio (classique)**.
1. [Reconstruire un service web Studio (classique)](migrate-rebuild-web-service.md).
1. [Intégrer un service web Azure Machine Learning à des applications clientes](migrate-rebuild-integrate-with-client-app.md).
1. [Migrer Exécuter un script R](migrate-execute-r-script.md).