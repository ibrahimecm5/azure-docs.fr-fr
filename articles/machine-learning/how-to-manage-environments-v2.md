---
title: Gérer les environnements Azure Machine Learning avec l’interface CLI (v2)
titleSuffix: Azure Machine Learning
description: Découvrez comment gérer les environnements Azure ML à l’aide de l’extension Azure CLI pour Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
author: mx-iao
ms.author: minxia
ms.date: 10/21/2021
ms.reviewer: laobri
ms.custom: devx-track-azurecli, devplatv2
ms.openlocfilehash: 2c88c8954e334b5c6ffc80a60b2bd85cd98bda7a
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132055528"
---
# <a name="manage-azure-machine-learning-environments-with-the-cli-v2-preview"></a>Gérer les environnements Azure Machine Learning avec l’interface CLI (v2) (préversion)

Les environnements Azure Machine Learning définissent les environnements d’exécution pour vos travaux ou déploiements et encapsulent les dépendances de votre code. Azure ML utilise la spécification de l’environnement pour créer le conteneur Docker dans lequel votre code d’apprentissage ou de scoring s’exécute sur la cible de calcul spécifiée. Vous pouvez définir un environnement à partir d’une spécification Conda, d’une image Docker ou d’un contexte de build Docker.

Dans cet article, découvrez comment créer et gérer des environnements Azure ML à l’aide de l’interface CLI (v2).

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="prerequisites"></a>Prérequis

- Pour utiliser l'interface de ligne de commande, vous devez disposer d'un abonnement Azure. Si vous n’avez pas d’abonnement Azure, créez un compte gratuit avant de commencer. Essayez la [version gratuite ou payante d’Azure Machine Learning](https://azure.microsoft.com/free/) dès aujourd’hui.
- [Installer et configurer l’extension Azure CLI pour Machine Learning](how-to-configure-cli.md)

> [!TIP]
> Pour profiter d’un environnement de développement complet, utilisez Visual Studio Code et l’[extension Azure Machine Learning](how-to-setup-vs-code.md) pour [gérer les ressources Azure Machine Learning](how-to-manage-resources-vscode.md) et [entraîner des modèles de machine learning](tutorial-train-deploy-image-classification-model-vscode.md).

### <a name="clone-examples-repository"></a>Cloner un dépôt d’exemples

Pour exécuter les exemples d’entraînement, commencez par cloner le dépôt d’exemples et placez-le dans le répertoire `cli` :

:::code language="azurecli" source="~/azureml-examples-main/cli/misc.sh" id="git_clone":::

Notez que `--depth 1` clone uniquement le dernier commit dans le dépôt, ce qui réduit le temps nécessaire à l’exécution de l’opération.

## <a name="curated-environments"></a>Environnements organisés

Il existe deux types d’environnements dans Azure ML : les environnements organisés et les environnements personnalisés. Les environnements organisés sont des environnements prédéfinis contenant des outils et infrastructures ML populaires. Les environnements personnalisés sont définis par l’utilisateur et peuvent être créés via `az ml environment create`.

Les environnements organisés sont fournis par Azure ML et sont disponibles dans votre espace de travail par défaut. Azure ML met régulièrement à jour ces environnements vers les versions les plus récentes de l’infrastructure et effectue la maintenance pour appliquer les correctifs de bogues et les correctifs de sécurité. Ils s’appuient sur des images Docker en cache, ce qui réduit le coût de préparation des travaux et le temps de déploiement des modèles.

Vous pouvez utiliser ces environnements organisés prêts à l’emploi pour l’apprentissage ou le déploiement en référençant un environnement spécifique à l’aide de la syntaxe `azureml:<curated-environment-name>:<version>`. Vous pouvez également les utiliser comme référence pour vos propres environnements personnalisés en modifiant les fichiers Dockerfiles sur lesquels s’appuient ces environnements organisés.

Vous pouvez voir l’ensemble des environnements organisés disponibles dans l’interface utilisateur d’Azure ML studio ou à l’aide de l’interface CLI (v2) via `az ml environments list`.

## <a name="create-an-environment"></a>Créer un environnement

Vous pouvez définir un environnement à partir d’une spécification Conda, d’une image Docker ou d’un contexte de build Docker. Configurez l’environnement à l’aide d’un fichier de spécification YAML et créez l’environnement à l’aide de la commande CLI suivante :

```cli
az ml environment create --file my_environment.yml
```

Pour obtenir la documentation de référence YAML pour les environnements Azure ML, consultez [Schéma YAML de l’environnement CLI (v2)](reference-yaml-environment.md).

### <a name="create-an-environment-from-a-docker-image"></a>Créer un environnement à partir d’une image Docker

Pour définir un environnement à partir d’une image Docker, fournissez l’URI de l’image hébergée dans un registre, comme Docker Hub ou Azure Container Registry. 

L’exemple suivant est un fichier de spécification YAML pour un environnement défini à partir d’une image Docker. Une image du référentiel PyTorch officiel sur Docker Hub est spécifiée via la propriété `image` dans le fichier YAML.

:::code language="yaml" source="~/azureml-examples-main/cli/assets/environment/docker-image.yml":::

Pour créer l’environnement :

```cli
az ml environment create --file assets/environment/docker-image.yml
```

> [!TIP]
> Azure ML assure la maintenance d’un ensemble d’images de base de processeur et GPU Ubuntu Linux avec des dépendances système communes. Par exemple, les images de GPU contiennent Miniconda, OpenMPI, CUDA, cuDNN et NCCL. Vous pouvez utiliser ces images pour vos environnements ou utiliser leur fichiers Dockerfiles correspondants comme référence lors de la création de vos propres images personnalisées.
>  
> Pour obtenir l’ensemble des images de base et les fichiers Dockerfiles correspondants, consultez le [référentiel AzureML-Containers](https://github.com/Azure/AzureML-Containers).

### <a name="create-an-environment-from-a-docker-build-context"></a>Créer un environnement à partir d’un contexte de build Docker

Au lieu de définir un environnement à partir d’une image prédéfinie, vous pouvez également en définir un à partir d’un [contexte de build](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/#understand-build-context) Docker. Pour ce faire, spécifiez le répertoire utilisé comme contexte de génération. Ce répertoire doit contenir un fichier Dockerfile et tous les autres fichiers nécessaires pour générer l’image.

L’exemple suivant est un fichier de spécification YAML pour un environnement défini à partir d’un contexte de build. Le chemin local d’accès au dossier de contexte de génération est spécifié dans le champ `build.local_path`. Le chemin relatif d’accès au fichier Dockerfile dans ce dossier de contexte de build est spécifié dans le champ `build.dockerfile_path`. Si `build.dockerfile_path` est omis dans le fichier YAML, Azure ML recherche un fichier Dockerfile nommé `Dockerfile` à la racine du contexte de build.

Dans cet exemple, le contexte de build contient un fichier Dockerfile nommé `Dockerfile` et un fichier `requirements.txt` référencé dans le fichier Dockerfile pour l’installation des packages Python.

:::code language="yaml" source="~/azureml-examples-main/cli/assets/environment/docker-context.yml":::

Pour créer l’environnement :

```cli
az ml environment create --file assets/environment/docker-context.yml
```

Azure ML commence à générer l’image à partir du contexte de génération lors de la création de l’environnement. Vous pouvez superviser l’état de la build et afficher les journaux de génération dans l’interface utilisateur de studio.

### <a name="create-an-environment-from-a-conda-specification"></a>Créer un environnement à partir d’une spécification Conda

Vous pouvez définir un environnement à l’aide d’un fichier de configuration standard Conda YAML qui comprend les dépendances de l’environnement Conda. Pour plus d’informations sur ce format standard, consultez [Création manuelle d’un environnement](https://conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html#creating-an-environment-file-manually).

Vous devez également spécifier une image Docker de base pour cet environnement. Azure ML crée l’environnement Conda au-dessus de l’image Docker fournie.

L’exemple suivant est un fichier de spécification YAML pour un environnement défini à partir d’une spécification Conda. Le chemin d’accès relatif au fichier Conda à partir du fichier YAML de l’environnement Azure ML y est spécifié via la propriété `conda_file`. Vous pouvez également définir la spécification Conda inline à l’aide de la propriété `conda_file`, plutôt que de la définir dans un fichier distinct.

:::code language="yaml" source="~/azureml-examples-main/cli/assets/environment/docker-image-plus-conda.yml":::

Pour créer l’environnement :

```cli
az ml environment create --file assets/environment/docker-image-plus-conda.yml
```

Azure ML génère l’image Docker finale à partir de cette spécification de l’environnement quand l’environnement est utilisé dans un travail ou un déploiement. Vous pouvez également déclencher manuellement une génération de l’environnement dans l’interface utilisateur de studio.

## <a name="manage-environments"></a>Gérer les environnements

L’interface CLI (v2) fournit un ensemble de commandes sous `az ml environment` pour gérer le cycle de vie de vos ressources d’environnement Azure ML.

### <a name="list"></a>List

Répertoriez l’ensemble des environnements dans votre espace de travail :

```cli
az ml environment list
```

Répertoriez l’ensemble des versions de l’environnement sous un nom donné :

```cli
az ml environment list --name docker-image-example
```

### <a name="show"></a>Afficher

Obtenez les détails d’un environnement spécifique :

```cli
az ml environment list --name docker-image-example --version 1
```

### <a name="update"></a>Update

Mettez à jour les propriétés mutables d’un environnement spécifique :

```cli
az ml environment update --name docker-image-example --version 1 --set description="This is an updated description."
```

> [!IMPORTANT]
> Pour les environnements, seuls `description` et `tags` peuvent être mis à jour. Toutes les autres propriétés sont immuables. Si vous avez besoin de modifier l’une de ces propriétés, vous devez créer une nouvelle version de l’environnement.

### <a name="delete"></a>Supprimer

Supprimez un environnement spécifique :

```cli
az ml environment delete --name docker-image-example --version 1
```

## <a name="use-environments-for-training"></a>Utiliser des environnements pour l'apprentissage

Pour utiliser un environnement pour un travail d’apprentissage, spécifiez le champ `environment` de la configuration YAML du travail. Vous pouvez référencer un environnement Azure ML inscrit existant via `environment: azureml:<environment-name>:<environment-version>` ou définir une spécification d’environnement inline. Si vous définissez un environnement inline, ne spécifiez pas les champs `name` ni `version`, car ces environnements sont traités comme des environnements « anonymes » et ne sont pas suivis dans le registre des ressources de votre environnement.

Quand vous soumettez un travail d’apprentissage, la génération d’un nouvel environnement peut prendre quelques minutes. La durée dépend de la taille des dépendances requises. Les environnements sont mis en cache par le service. Ainsi, tant que la définition de l'environnement reste inchangée, vous ne subissez l'intégralité du temps d'installation qu'une seule fois.

Pour plus d’informations sur l’utilisation des environnements dans les travaux, consultez [Effectuer l’apprentissage des modèles avec l’interface CLI (v2)](how-to-train-cli.md).

## <a name="use-environments-for-model-deployments"></a>Utiliser des environnements pour les modèles de déploiement

Vous pouvez également utiliser des environnements pour vos modèles de déploiement pour effectuer le scoring en ligne et par lot. Pour ce faire, spécifiez le champ `environment` dans la configuration YAML de déploiement.

Pour plus d’informations sur l’utilisation des environnements dans les déploiements, consultez [Déployer et effectuer le scoring d’un modèle d’apprentissage automatique en utilisant un point de terminaison en ligne géré](how-to-deploy-managed-online-endpoints.md).

## <a name="next-steps"></a>Étapes suivantes

- [Entraîner des modèles (créer des travaux) avec l’interface CLI (v2)](how-to-train-cli.md)
- [Déployer et effectuer le scoring d’un modèle d’apprentissage automatique en utilisant un point de terminaison en ligne géré](how-to-deploy-managed-online-endpoints.md)
- [Référence de schéma YAML de l’environnement](reference-yaml-environment.md)
