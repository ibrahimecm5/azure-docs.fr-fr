---
title: À propos des environnements Azure Machine Learning
titleSuffix: Azure Machine Learning
description: Découvrez les environnements d’apprentissage automatique qui permettent des définitions de dépendance d’apprentissage automatique reproductibles, auditables et portables sur diverses cibles de calcul.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: larryfr
author: Blackmist
ms.date: 09/23/2021
ms.openlocfilehash: e798893b28eaaa6aabb1d3f3623aea60df7bc2c6
ms.sourcegitcommit: 96deccc7988fca3218378a92b3ab685a5123fb73
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131576146"
---
# <a name="what-are-azure-machine-learning-environments"></a>Présentation des environnements Azure Machine Learning

Les environnements Azure Machine Learning sont une encapsulation de l’environnement dans lequel votre formation Machine Learning se produit. Ils spécifient les packages, variables d’environnement et paramètres logiciels Python autour de vos scripts d’apprentissage et de scoring. Ils spécifient également les temps d’exécution (Python, Spark ou Docker). Les environnements sont des entités managées et avec version dans votre espace de travail Machine Learning qui permettent de bénéficier de workflows Machine Learning reproductibles, pouvant être audités et portables parmi une variété de cibles de calcul.

Vous pouvez utiliser un objet `Environment` sur votre calcul local pour :
* Développer votre script d’apprentissage.
* Réutiliser le même environnement sur la capacité de calcul Azure Machine Learning pour l’apprentissage du modèle à l’échelle.
* Déployer votre modèle avec le même environnement.
* Revisitez l’environnement dans lequel un modèle existant a été formé.

Le diagramme suivant montre comment vous pouvez utiliser un seul objet `Environment` à la fois dans votre configuration d’exécution (pour l’apprentissage) et votre configuration d’inférence et de déploiement (pour les déploiements de services web).

![Diagramme d’environnement dans un workflow Machine Learning](./media/concept-environments/ml-environment.png)

L’environnement, la cible de calcul et le script de formation forment la configuration d’exécution : la spécification complète d’une exécution d’apprentissage.

## <a name="types-of-environments"></a>Types d’environnements

Les environnements se répartissent globalement en trois catégories : *organisés*, *gérés par l’utilisateur* et *gérés par le système*.

Les environnements organisés sont fournis par Azure Machine Learning et sont disponibles dans votre espace de travail par défaut. Prévus pour être utilisés tels quels, ils contiennent des collections de packages et paramètres Python destinés à vous aider à prendre en main diverses infrastructures de Machine Learning. Ces environnements précréés offrent également un temps de déploiement plus rapide. Pour obtenir une liste complète, consultez [l’article sur les environnements organisés](resource-curated-environments.md).

Dans un environnement géré par l’utilisateur, vous êtes responsable de la configuration de votre environnement et de l’installation de chaque package dont votre script d’apprentissage a besoin sur la cible de calcul. Veillez également à inclure toutes les dépendances nécessaires pour le modèle de déploiement.

Vous utilisez des environnements gérés par le système quand vous souhaitez que [Conda](https://conda.io/docs/) gère l’environnement Python pour vous. Un nouvel environnement Conda est matérialisé à partir de votre spécification Conda sur une image Docker de base.

## <a name="create-and-manage-environments"></a>Créer et gérer des environnements

Vous pouvez créer des environnements à partir de clients tels que le Kit de développement logiciel (SDK) Python AzureML, l’interface CLI d’Azure Machine Learning, la page Environnements dans Azure Machine Learning Studio, et l’[extension VS Code](how-to-manage-resources-vscode.md#create-environment). Chaque client vous permet de personnaliser l’image de base, le Dockerfile et la couche Python, si nécessaire.

Pour des exemples de code spécifiques, consultez la section « Créer un environnement » de [Comment utiliser les environnements](how-to-use-environments.md#create-an-environment). 

Les environnements sont également facilement gérés via votre espace de travail, ce qui vous permet d’effectuer les opérations suivantes :

* Inscrire des environnements.
* Extraire des environnements de votre espace de travail à des fins d’apprentissage ou de déploiement.
* Créer une instance d’un environnement en modifiant une instance existante.
* Afficher les modifications apportées à vos environnements au fil du temps, ce qui garantit la reproductibilité.
* Créer automatiquement des images Docker à partir de vos environnements.

Les environnements «anonymes» sont automatiquement inscrits dans votre espace de travail lorsque vous soumettez une expérience. Ils ne sont pas listés, mais peuvent être récupérés par version.

Pour des exemples de code, consultez la section « Gérer les environnements » de [Comment utiliser les environnements](how-to-use-environments.md#manage-environments).

## <a name="environment-building-caching-and-reuse"></a>Création, mise en cache et réutilisation d’environnement

Azure Machine Learning génère des définitions d’environnement dans des images Docker et des environnements Conda. Il met également en cache les environnements afin qu’ils puissent être réutilisés lors des exécutions d’entraînement et des déploiements de points de terminaison de service ultérieurs. L’exécution d’un script d’apprentissage à distance requiert la création d’une image Docker, mais une exécution locale peut utiliser directement un environnement Conda. 

### <a name="submitting-a-run-using-an-environment"></a>Envoi d’une exécution à l’aide d’un environnement

Quand vous envoyez pour la première fois une exécution à distance à l’aide d’un environnement, le service Azure Machine Learning appelle une [tâche de génération ACR](../container-registry/container-registry-tasks-overview.md) sur l’Azure Container Registry (ACR) associé à l’espace de travail. L’image Docker génrée est ensuite mise en cache sur l’ACR Espace de travail. Les environnements organisés sont sauvegardés par des images Docker mises en cache dans l’ACR global. Au début de l’exécution, l’image est récupérée par la cible de calcul de l’ACR pertinent.

Pour les exécutions locales, un environnement Docker ou Conda est créé en fonction de la définition de l’environnement. Les scripts sont ensuite exécutés sur le calcul cible (un environnement d’exécution local ou un moteur Docker local).

### <a name="building-environments-as-docker-images"></a>Génération d’environnements en tant qu’images Docker

Si l’image pour une définition d’environnement particulière n’existe pas encore dans l’ACR de l’espace de travail, une nouvelle image est générée. La génération d’image comprend deux étapes :

 1. Téléchargement d’une image de base et exécution des étapes Docker
 2. Génération d’un environnement Conda en fonction des dépendances Conda spécifiées dans la définition d’environnement

La deuxième étape est omise si vous spécifiez des [dépendances gérées par l’utilisateur](/python/api/azureml-core/azureml.core.environment.pythonsection). Dans ce cas, vous êtes responsable de l’installation des packages Python, en les incluant dans votre image de base, ou de la spécification des étapes Docker personnalisées lors de la première étape. Vous devez aussi spécifier l’emplacement correct de l’exécutable Python. Il est également possible d’utiliser une [image de base Docker personnalisée](./how-to-deploy-custom-container.md).

### <a name="image-caching-and-reuse"></a>Mise en cache et réutilisation des images

Si vous utilisez la même définition d’environnement pour une autre exécution, Azure Machine Learning réutilise l’image mise en cache à partir de l’ACR de l’espace de travail pour gagner du temps.

Pour afficher les détails d’une image mise en cache, consultez la page Environnements dans Azure Machine Learning Studio, ou utilisez la méthode [`Environment.get_image_details`](/python/api/azureml-core/azureml.core.environment.environment#get-image-details-workspace-).

Pour déterminer s’il faut réutiliser une image mise en cache ou en créer une, AzureML calcule une [valeur de hachage](https://en.wikipedia.org/wiki/Hash_table) à partir de la définition d’environnement et la compare aux hachages d’environnements existants. Le hachage est basé sur la définition de l’environnement :
 
 * Base image
 * Étapes Docker personnalisées
 * Packages Python
 * Packages Spark

Le hachage n’est pas affecté par le nom ou la version de l’environnement. Si vous renommez votre environnement, ou créez un environnement avec les mêmes paramètres et packages qu’un autre, la valeur de hachage reste la même. Cependant, les changements de définition d’environnement, tels que l’ajout ou la suppression d’un package Python ou la modification de la version d’un package, entraînent la modification de la valeur de hachage. La modification de l’ordre des dépendances ou des canaux dans un environnement modifie également le hachage, et nécessite une nouvelle génération d’image. De même, toute modification d’un environnement organisé entraîne la création d’un nouvel environnement « non organisé ». 

> [!NOTE]
> Vous ne pouvez pas soumettre des modifications locales d’un environnement organisé sans modifier son nom. Les préfixes « AzureML- » et « Microsoft » sont réservés exclusivement aux environnements organisés, et votre soumission de travail échoue si le nom commence par l’un des deux.

La valeur de hachage calculée de l’environnement est comparée aux valeurs dans l’espace de travail et de l’ACR global, ou sur la cible de calcul (exécutions locales uniquement). En cas de correspondance, l’image mise en cache est extraite et utilisée. Autrement, une génération d’image est déclenchée.

Le diagramme suivant montre trois définitions d’environnement. Deux d’entre elles ont des noms et versions différents, mais des images de base et des packages Python identiques, produisant les mêmes hachage et image mise en cache. Le troisième environnement a des packages et versions Python différents qui produisent un hachage et une image mise en cache différents.

![Diagramme de mise en cache d’environnement et d’images Docker](./media/concept-environments/environment-caching.png)

Les images mises en cache réelles dans votre ACR d’espace de travail ont des noms tels que `azureml/azureml_e9607b2514b066c851012848913ba19f` avec le hachage apparaissant à la fin.

>[!IMPORTANT]
> * Si vous créez un environnement avec une dépendance de package non épinglée (par exemple, `numpy`), l’environnement utilise la version de package qui était *disponible lors de sa création*. Tout environnement futur utilisant une définition correspondante utilisera la version d’origine. 
>
>   Pour mettre à jour le package, spécifiez un numéro de version afin de forcer la régénération de l’image. Un exemple de cela serait de changer `numpy` en `numpy==1.18.1`. De nouvelles dépendances, notamment imbriquées, seront installées et pourraient nuire au bon fonctionnement d’un scénario précédemment opérationnel.
>
> * L’utilisation d’une image de base non épinglée comme `mcr.microsoft.com/azureml/openmpi3.1.2-ubuntu18.04` dans votre définition d’environnement entraîne la régénération de l’image chaque fois que l’étiquette `latest` est mise à jour. Cela permet à l’image de recevoir les derniers correctifs et mises à jour système.

> [!WARNING]
>  La méthode [`Environment.build`](/python/api/azureml-core/azureml.core.environment.environment#build-workspace--image-build-compute-none-) régénérera l’image mise en cache, avec comme effet secondaire possible la mise à jour des packages désépinglés et la rupture de la reproductibilité pour toutes les définitions d’environnement correspondant à cette image mise en cache.

### <a name="image-patching"></a>Mise à jour corrective d’image

Microsoft est responsable de la mise à jour corrective des images de base pour les vulnérabilités de sécurité connues. Les mises à jour des images prises en charge sont publiées toutes les deux semaines, avec l’engagement que la version la plus récente de l’image ne contienne aucune vulnérabilité non corrigée datant de plus de 30 jours. Les images corrigées sont publiées avec une nouvelle étiquette immuable, et l’étiquette `:latest` est mise à jour vers la dernière version de l’image corrigée. 

Si vous fournissez vos propres images, vous êtes chargé de les mettre à jour.

Pour plus d’informations sur les images de base, consultez les liens suivants :

* Dépôt GitHub d’[images de base Azure Machine Learning](https://github.com/Azure/AzureML-Containers).
* [Effectuer l’apprentissage d’un modèle à l’aide d’une image personnalisée](how-to-train-with-custom-image.md).
* [Déployer un modèle TensorFlow à l’aide d’un conteneur personnalisé](how-to-deploy-custom-container.md)

## <a name="next-steps"></a>Étapes suivantes

* Découvrez comment [créer et utiliser des environnements](how-to-use-environments.md) dans Azure Machine Learning.
* Consultez la documentation de référence du kit de développement logiciel (SDK) Python pour la [classe d’environnement](/python/api/azureml-core/azureml.core.environment%28class%29).
