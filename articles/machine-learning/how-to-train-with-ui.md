---
title: Créer un travail d’apprentissage avec l’interface utilisateur de création de travail
titleSuffix: Azure Machine Learning
description: Découvrez comment utiliser l’interface utilisateur de création de travail dans Azure Machine Learning Studio pour créer un travail d’apprentissage.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.custom: devplatv2
author: wenxwei
ms.author: wenxwei
ms.date: 10/21/2021
ms.reviewer: laobri
ms.openlocfilehash: a626c789737b8af53c18946450c9be7ee5f91176
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131562007"
---
# <a name="create-a-training-job-with-the-job-creation-ui-preview"></a>Créer un travail d’apprentissage avec l’interface utilisateur de création de travail (préversion)

Il existe de nombreuses façons de créer un travail d’apprentissage avec Azure Machine Learning. Vous pouvez utiliser l’interface CLI (voir [Entraîner des modèles (créer des travaux) avec l’interface CLI (v2) (préversion)](how-to-train-cli.md), l’API REST (voir [Entraîner des modèles avec REST (préversion)](how-to-train-with-rest.md)) ou utiliser l’interface utilisateur pour créer directement un travail d’apprentissage. Dans cet article, vous allez apprendre à utiliser vos propres données et code pour effectuer l’apprentissage d’un modèle Machine Learning avec l’interface utilisateur de création de travail dans Azure Machine Learning Studio.

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, créez un compte gratuit avant de commencer. Essayez la [version gratuite ou payante d’Azure Machine Learning](https://aka.ms/AMLFree) dès aujourd’hui.

* Un espace de travail Azure Machine Learning. Consultez [Créer un espace de travail Microsoft Azure Machine Learning](how-to-manage-workspace.md). 

* Compréhension de ce qu’est un travail dans Azure Machine Learning. Consultez [Guide pratique pour effectuer l’apprentissage des modèles avec l’interface CLI (v2)](how-to-train-cli.md).

## <a name="get-started"></a>Bien démarrer

1. Connectez-vous à [Azure Machine Learning Studio](https://ml.azure.com). 

1. Sélectionnez votre abonnement et votre espace de travail.
 
* Vous pouvez accéder à l’interface utilisateur de création d’un travail à partir de la page d’accueil. Cliquez sur **Créer**, puis sélectionnez **Travail**. 
[![Page d’accueil Azure Machine Learning Studio](media/how-to-train-with-ui/home-entry.png)](media/how-to-train-with-ui/home-entry.png)

* Vous pouvez également accéder à la création de travail à partir du volet de gauche. Cliquez sur **+Nouveau**, puis sélectionnez **Travail**. 
[![Volet de navigation de gauche Azure Machine Learning Studio](media/how-to-train-with-ui/left-nav-entry.png)](media/how-to-train-with-ui/left-nav-entry.png)

* En outre, si vous êtes dans la page expérience, vous pouvez accéder à l’onglet **Toutes les exécutions**, puis cliquer sur **Créer un travail**. 
[![Entrée de page d’expérience pour l’interface utilisateur de création de travail](media/how-to-train-with-ui/experiment-entry.png)](media/how-to-train-with-ui/experiment-entry.png)

Ces options vous permettent d’accéder au panneau de création de travail, qui comporte un Assistant pour la configuration et la création d’un travail d’apprentissage. 

## <a name="select-compute-resources"></a>Sélectionner les ressources de calcul

La première étape de l’interface utilisateur de création d’un travail consiste à sélectionner la cible de calcul sur laquelle vous souhaitez exécuter votre travail. L’interface utilisateur de création d’un travail prend en charge plusieurs types de calcul :

| Type de calcul | Introduction | 
| --- | --- | 
| Instance de calcul | [Qu’est-ce qu’une instance de calcul Azure Machine Learning ?](concept-compute-instance.md) | 
| Cluster de calcul | [Qu’est-ce qu’un cluster de calcul ?](how-to-create-attach-compute-cluster.md#what-is-a-compute-cluster) | 
| Cluster Kubernetes attaché | [Configuration du Machine Learning avec Azure Arc (préversion)](how-to-attach-arc-kubernetes.md). | 

1. Sélectionner un type de calcul
1. Sélectionnez une ressource de calcul existante. La liste déroulante affiche les informations de nœud et le type de référence SKU pour faciliter votre choix.
1. Pour un cluster de calcul ou un cluster Kubernetes, vous pouvez également spécifier le nombre de nœuds que vous souhaitez pour le travail dans **Nombre d’instances**. Le nombre d’instances par défaut est 1. 
1. Lorsque vous êtes satisfait de vos choix, sélectionnez **Suivant**. 
 [![Sélectionner un cluster de calcul](media/how-to-train-with-ui/compute-cluster.png)](media/how-to-train-with-ui/compute-cluster.png)

Si vous utilisez Azure Machine Learning pour la première fois, vous verrez une liste vide ainsi qu’un lien permettant de créer un calcul. 

 [![Créer une instance de calcul](media/how-to-train-with-ui/create-new-compute.png)](media/how-to-train-with-ui/create-new-compute.png)

Pour plus d’informations sur la création des différents types, consultez :

| Type de calcul | Guides pratiques | 
| --- | --- | 
| Instance de calcul | [Créer et gérer une instance de calcul Azure Machine Learning](how-to-create-manage-compute-instance.md) | 
| Cluster de calcul | [Créer un cluster de calcul Azure Machine Learning](how-to-create-attach-compute-cluster.md) | 
| Cluster Kubernetes attaché | [Attacher un cluster Kubernetes avec Azure Arc](how-to-attach-arc-kubernetes.md) | 

## <a name="specify-the-necessary-environment"></a>Spécifier l’environnement nécessaire

Après avoir sélectionné une cible de calcul, vous devez spécifier l’environnement d’exécution de votre travail. L’interface utilisateur de création d’un travail prend en charge trois types d’environnements :

* Environnements organisés
* Environnements personnalisés
* Image de registre de conteneurs 

### <a name="curated-environments"></a>Environnements organisés

Les environnements organisés sont des collections définies par Azure des packages Python utilisés dans les charges de travail ML courantes. Les environnements organisés sont disponibles dans votre espace de travail par défaut. Ces environnements s’appuient sur des images Docker mises en cache, ce qui réduit la surcharge de préparation de l’exécution. Les cartes affichées dans la page « Environnements organisés » affichent les détails de chaque environnement. Pour en savoir plus, consultez [les environnements organisés dans Azure Machine Learning](resource-curated-environments.md).

 [![Environnements organisés](media/how-to-train-with-ui/curated-env.png)](media/how-to-train-with-ui/curated-env.png)

### <a name="custom-environments"></a>Environnements personnalisés

Les environnements personnalisés sont des environnements que vous avez spécifiés vous-même. Vous pouvez spécifier un environnement ou réutiliser un environnement que vous avez déjà créé. Pour plus d’informations, consultez [Gérer des environnements logiciels dans Azure Machine Learning Studio (préversion)](how-to-manage-environments-in-studio.md#create-an-environment). 

### <a name="container-registry-image"></a>Image de registre de conteneurs

Si vous ne souhaitez pas utiliser les environnements organisés Azure Machine Learning ou si vous spécifiez votre propre environnement personnalisé, vous pouvez utiliser une image Docker à partir d’un registre de conteneurs public, tel que [Docker Hub](https://hub.docker.com/). Si l’image se trouve dans un conteneur privé, activez **Il s’agit d’un registre de conteneurs privé**. Pour les registres privés, vous devrez entrer un nom d’utilisateur et un mot de passe valides pour permettre à Azure d’accéder à l’image. 
[![Image de registre de conteneurs](media/how-to-train-with-ui/container-registry-image.png)](media/how-to-train-with-ui/container-registry-image.png)

## <a name="configure-your-job"></a>Configurer votre travail

Après avoir spécifié l’environnement, vous pouvez configurer votre travail avec davantage de paramètres. 

|Champ| Description|
|------| ------|
|Nom du travail| Le champ Nom du travail est utilisé pour identifier votre travail de façon unique. Il est également utilisé comme nom d’affichage pour votre travail. La définition de ce champ est facultative ; Azure génère un nom GUID pour le travail si vous n’entrez rien. Remarque : le nom du travail doit être unique.|
|Nom de l'expérience| Il permet d’organiser le travail dans Azure Machine Learning Studio. L’enregistrement d’exécution de chaque travail est organisé sous l’expérience correspondante dans l’onglet « Expérience » de Studio. Par défaut, Azure met le travail dans l’expérience **Par défaut**.|
|Code| Vous pouvez télécharger un fichier de code ou un dossier à partir de votre ordinateur, ou télécharger un fichier de code à partir du stockage d’objets blob par défaut de l’espace de travail. Azure affiche les fichiers à charger une fois votre sélection effectuée. |
|Commande| Commande à exécuter. Les arguments de ligne de commande peuvent être écrits explicitement dans la commande ou déduits à partir d’autres sections, en particulier des **entrées**, en utilisant une notation d’accolades, comme indiqué dans la section suivante|
|Entrées| Spécifiez la liaison d’entrée. Nous prenons en charge trois types d’entrées : 1) jeu de données inscrit Azure Machine Learning ; 2) stockage d’objets blob par défaut de l’espace de travail ; 3) chargement d’un fichier local. Vous pouvez ajouter plusieurs entrées. |
|Variables d'environnement| Définir des variables d’environnement vous permet de fournir une configuration dynamique du travail. Vous pouvez ajouter la variable et la valeur ici.|
|Étiquettes| Ajoutez des balises à votre travail pour faciliter l’organisation.|

### <a name="specify-code-and-inputs-in-the-command-box"></a>Spécifier le code et les entrées dans la zone de commande

#### <a name="code"></a>Code

La commande est exécutée à partir du répertoire racine du dossier de code téléchargé. Après avoir sélectionné votre fichier de code ou dossier, vous pouvez afficher les fichiers à charger. Copiez le chemin d’accès relatif au code contenant votre point d’entrée, puis collez-le dans la zone intitulée **Entrer la commande pour démarrer le travail**. 

Si le code se trouve dans le répertoire racine, vous pouvez y faire directement référence dans la commande. Par exemple, `python main.py`.

Si le code ne se trouve pas dans le répertoire racine, vous devez utiliser le chemin d’accès relatif. Par exemple, la structure du [modèle de langage Word](https://github.com/Azure/azureml-examples/tree/main/cli/jobs/train/pytorch/word-language-model) est la suivante :

```tree
.
├── job.yml
├── data
└── src
    └── main.py
```
Ici, le code source se trouve dans le sous-répertoire `src`. La commande correspond à `python ./src/main.py` (plus d’autres arguments de ligne de commande).

[![Faire référence à du code dans la commande](media/how-to-train-with-ui/code-command.png)](media/how-to-train-with-ui/code-command.png)

#### <a name="inputs"></a>Entrées

Vous pouvez effectuer une liaison d’entrée de deux façons : 

* Nom d’entrée : lorsque vous utilisez une entrée dans la commande, vous devez spécifier le nom de l’entrée. Pour indiquer une variable d’entrée, utilisez le formulaire `{inputs.input_name}`. Par exemple, `{inputs.wiki}`. Vous pouvez ensuite y faire référence dans la commande, par exemple, `--data {inputs.wiki}`.
[![Faire référence au nom d’entrée dans la commande](media/how-to-train-with-ui/input-command-name.png)](media/how-to-train-with-ui/input-command-name.png)

* Chemin d’accès : vous pouvez utiliser `--data .path` pour spécifier un emplacement cloud. Le chemin d’accès correspond à ce que vous entrez dans le champ **Chemin d’accès au calcul**.
[![Faire référence au chemin d’entrée dans la commande](media/how-to-train-with-ui/input-command-path.png)](media/how-to-train-with-ui/input-command-path.png)

>[!NOTE] 
>Dans la commande  **pour démarrer le travail**, vous devez ajouter un point à la valeur **Chemin d’accès au cacul** . Par exemple : `/data/wikitext-2` devient `./data/wikitext-2`.

## <a name="review-and-create"></a>Vérifier et créer 

Après avoir configuré votre travail, sélectionnez **Suivant** pour accéder à la page **Vérifier**. Pour modifier un paramètre, sélectionnez l’icône en forme de crayon et apportez la modification souhaitée. 

Vous pouvez choisir **Afficher la spécification YAML** pour vérifier et télécharger le fichier YAML généré par cette configuration du travail. Ce fichier YAML du travail peut être utilisé pour envoyer le travail à partir de l’interface CLI (v2). (Consultez [Entraîner des modèles (créer des travaux) avec l’interface CLI (v2) (préversion)](how-to-train-cli.md).) [![Afficher la spécification YAML](media/how-to-train-with-ui/view-yaml.png)](media/how-to-train-with-ui/view-yaml.png)
[![Spécification YAML](media/how-to-train-with-ui/yaml-spec.png)](media/how-to-train-with-ui/yaml-spec.png)

Pour lancer le travail, sélectionnez **Créer**. Une fois le travail crée, Azure affiche la page Détails de l’exécution, dans laquelle vous pouvez surveiller et gérer votre travail d’apprentissage. 

## <a name="next-steps"></a>Étapes suivantes

* [Déployer et scorer un modèle Machine Learning avec un point de terminaison en ligne managé (préversion)](how-to-deploy-managed-online-endpoints.md)

* [Entraîner des modèles (créer des travaux) avec l’interface CLI (v2) (préversion)](how-to-train-cli.md)
