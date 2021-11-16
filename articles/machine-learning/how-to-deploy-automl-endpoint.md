---
title: Déployer un modèle AutoML avec un point de terminaison en ligne géré (préversion)
titleSuffix: Azure Machine Learning
description: Découvrez comment déployer votre modèle AutoML en tant que service web automatiquement managé par Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: ssambare
ms.reviewer: laobri
author: shivanissambare
ms.date: 10/21/2021
ms.topic: how-to
ms.custom: how-to, devplatv2
ms.openlocfilehash: 5bc5208de912a6f11271681b754736bfafe17e8b
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131566839"
---
# <a name="how-to-deploy-an-automl-model-to-an-online-endpoint-preview"></a>Comment déployer un modèle AutoML sur un point de terminaison en ligne (préversion)

Dans cet article, vous allez découvrir comment déployer un modèle Machine Learning formé par AutoML sur un point de terminaison en ligne. Le Machine Learning automatisé, également appelé ML automatisé ou AutoML, est le processus d’automatisation des tâches fastidieuses et itératives de développement d’un modèle Machine Learning. Pour plus d’informations, consultez [Qu’est-ce que le Machine Learning automatisé (AutoML) ?](concept-automated-ml.md).

Dans cet article, vous allez découvrir comment déployer un modèle Machine Learning formé par AutoML sur des points de terminaison en ligne à l’aide de : 

- Azure Machine Learning Studio
- Interface CLI 2.0 Azure Machine Learning

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="prerequisites"></a>Prérequis

Modèle Machine Learning formé par AutoML. Pour plus d’informations, consultez [Tutoriel : apprentissage d’un modèle de classification avec AutoML sans code dans Azure Machine Learning studio](tutorial-first-experiment-automated-ml.md)[Tutoriel : prévoir la demande avec le Machine Learning automatisé](tutorial-automated-ml-forecast.md).

## <a name="deploy-from-azure-machine-learning-studio-and-no-code"></a>Effectuer le déploiement à partir d’Azure Machine Learning studio et sans code

Le déploiement d’un modèle formé par AutoML à partir de la page ML automatisé est une expérience sans code. Cela signifie que vous n’avez pas besoin de préparer de script ni un environnement de scoring, ils sont tous les deux générés automatiquement. 

1. Accéder à la page ML automatisé dans studio
1. Sélectionner et exécuter votre expérience
1. Choisir l’onglet Modèles
1. Sélectionner le modèle que vous souhaitez déployer 
1. Une fois que vous avez sélectionné un modèle, le bouton Déployer s’active avec un menu déroulant
1. Sélectionner l’option *Déployer sur le point de terminaison en temps réel (préversion)*

   :::image type="content" source="media/how-to-deploy-automl-endpoint/deploy-button.png" lightbox="media/how-to-deploy-automl-endpoint/deploy-button.png" alt-text="Capture d’écran montrant le menu déroulant du bouton Déployer":::

   Le système génère le modèle et l’environnement nécessaires au déploiement. 

   :::image type="content" source="media/how-to-deploy-automl-endpoint/model.png" lightbox="media/how-to-deploy-automl-endpoint/model.png" alt-text="Capture d’écran montrant le modèle généré":::

   :::image type="content" source="media/how-to-deploy-automl-endpoint/environment.png" lightbox="media/how-to-deploy-automl-endpoint/environment.png" alt-text="Capture d’écran montrant l’environnement généré":::

5. Exécuter l’assistant pour déployer le modèle sur le point de terminaison en temps réel

 :::image type="content" source="media/how-to-deploy-automl-endpoint/complete-wizard.png" lightbox="media/how-to-deploy-automl-endpoint/complete-wizard.png"  alt-text="Capture d’écran montrant la page Vérifier et créer":::


## <a name="deploy-manually-from-the-studio-or-command-line"></a>Déployer manuellement à partir de studio ou de la ligne de commande

Si vous souhaitez mieux contrôler le déploiement, vous pouvez télécharger les artefacts d’apprentissage et les déployer. 

Pour télécharger les composants dont vous avez besoin pour le déploiement :

1. Accéder à votre expérience ML automatisé et l’exécuter dans votre espace de travail Machine Learning
1. Choisir l’onglet Modèles
1. Sélectionnez le modèle que vous souhaitez utiliser. Une fois que vous avez sélectionné un modèle, le bouton *Télécharger* est activé
1. Choisir *Télécharger*

:::image type="content" source="media/how-to-deploy-automl-endpoint/download-model.png" lightbox="media/how-to-deploy-automl-endpoint/download-model.png" alt-text="Capture d’écran montrant la sélection du modèle et du bouton de téléchargement":::

Vous recevez un fichier zip contenant les éléments suivants :
* Un fichier de spécification de l’environnement Conda nommé `conda_env_<VERSION>.yml`
* Un fichier de scoring Python nommé `scoring_file_<VERSION>.py`
* Le modèle lui-même, dans un fichier Python `.pkl` nommé `model.pkl`

Pour effectuer un déploiement à l’aide de ces fichiers, vous pouvez utiliser studio ou l’interface de ligne de commande Azure.

# <a name="studio"></a>[Studio](#tab/Studio)

1. Accéder à la page Modèles dans Azure Machine Learning studio

1. Cliquer sur l’option + Inscrire le modèle

1. Inscrire le modèle que vous avez téléchargé à partir de l’exécution de ML automatisé

1. Accédez à la page Environnements, sélectionnez Environnement personnalisé, puis sélectionnez l’option + Créer pour créer un environnement pour votre déploiement. Utiliser le fichier YAML Conda téléchargé pour créer un environnement personnalisé

1. Sélectionner le modèle, puis dans l’option de liste déroulante Déployer, sélectionner Déployer sur le point de terminaison en temps réel

1. Effectuer toutes les étapes de l’Assistant pour créer un point de terminaison de traitement en ligne et un déploiement

 
# <a name="cli"></a>[INTERFACE DE LIGNE DE COMMANDE](#tab/CLI)

## <a name="configure-the-cli"></a>Configuration de l’interface CLI 

Pour créer un déploiement à partir de l’interface CLI, vous avez besoin de l’interface Azure CLI avec l’extension ML v2. Exécutez la commande suivante pour confirmer que vous disposez des deux éléments :

:::code language="azurecli" source="~/azureml-examples-main/cli/misc.sh" id="az_version":::

Si vous recevez un message d’erreur ou que vous ne voyez pas `Extensions: ml` dans la réponse, procédez comme expliqué dans la section [Installer et configurer l’interface CLI (v2)](how-to-configure-cli.md).

Connexion :

:::code language="azurecli" source="~/azureml-examples-main/cli/misc.sh" id="az_login":::

Si vous avez accès à plusieurs abonnements Azure, vous pouvez définir votre abonnement actif :

:::code language="azurecli" source="~/azureml-examples-main/cli/misc.sh" id="az_account_set":::

Définissez le groupe de ressources et l’espace de travail par défaut où vous souhaitez créer le déploiement :

:::code language="azurecli" source="~/azureml-examples-main/cli/setup.sh" id="az_configure_defaults":::

## <a name="put-the-scoring-file-in-its-own-directory"></a>Placer le fichier de scoring dans son propre répertoire

Créez un répertoire nommé `src/` et placez-y le fichier de scoring que vous avez téléchargé. Ce répertoire est chargé dans Azure et contient tout le code source nécessaire pour effectuer l’inférence. Pour un modèle AutoML, il n’y a qu’un fichier de scoring unique. 

## <a name="create-the-endpoint-and-deployment-yaml-file"></a>Créer le point de terminaison et le fichier de déploiement YAML

Pour créer un point de terminaison en ligne géré à partir de la ligne de commande, vous devez créer des fichiers *endpoint.yml* et *deployment.yml*. Le code suivant extrait du [référentiel d’exemples Azure Machine Learning](https://github.com/Azure/azureml-examples) montre _endpoints/online/managed/sample/_ qui capture toutes les entrées requises :

__automl_endpoint.yml__

::: code language="yaml" source="~/azureml-examples-cli-preview/cli/endpoints/online/managed/sample/endpoint.yml" :::

__automl_deployment.yml__

::: code language="yaml" source="~/azureml-examples-cli-preview/cli/endpoints/online/managed/sample/blue-deployment.yml" :::

Vous devrez modifier ce fichier pour utiliser les fichiers que vous avez téléchargés à partir de la page des modèles AutoML.

1. Créez des fichiers `automl_endpoint.yml` et `automl_deployment.yml`, et collez le contenu de l’exemple ci-dessus.

1. Modifiez la valeur `name` du point de terminaison. Le nom du point de terminaison doit être unique dans la région Azure. Le nom d’un point de terminaison doit commencer par une lettre majuscule ou minuscule et contenir uniquement des caractères « - » et alphanumériques.

1. Dans le fichier `automl_deployment`, modifiez la valeur des clés au niveau des chemins suivants :

    | Chemin d’accès | Remplacer par |
    | --- | --- |
    | `model:local_path` | Chemin du fichier `model.pkl` que vous avez téléchargé. |
    | `code_configuration:code:local_path` | Répertoire dans lequel vous avez placé le fichier de scoring. | 
    | `code_configuration:scoring_script` | Nom du fichier de scoring Python (`scoring_file_<VERSION>.py`). |
    | `environment:conda_file` | URL de fichier pour le fichier d’environnement Conda téléchargé (`conda_env_<VERSION>.yml`). |

    > [!NOTE]
    > Pour obtenir une description complète des YAML, consultez [Informations de référence YAML sur les points de terminaison en ligne managés (préversion)](reference-yaml-endpoint-managed-online.md).

3. À partir de la ligne de commande, exécutez : 

    ```azurecli
    az ml online-endpoint create -f automl_endpoint.yml
    az ml online-deployment create -f automl_deployment.yml
    ```
    
---

Après avoir créé un déploiement, vous pouvez effectuer le scoring comme décrit à la section [Appeler le point de terminaison pour effectuer le scoring des données avec votre modèle](how-to-deploy-managed-online-endpoints.md#invoke-the-endpoint-to-score-data-by-using-your-model).

## <a name="next-steps"></a>Étapes suivantes

- [Résolution des problèmes de déploiement de points de terminaison en ligne managés](how-to-troubleshoot-managed-online-endpoints.md)
- [Déploiement sécurisé pour les points de terminaison en ligne](how-to-safely-rollout-managed-endpoints.md)
