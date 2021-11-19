---
title: Créer et exécuter des pipelines ML basés sur des composants (CLI)
titleSuffix: Azure Machine Learning
description: Créez et exécutez des pipelines de machine learning avec l’interface CLI d’Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: laobri
author: lobrien
ms.date: 10/21/2021
ms.topic: how-to
ms.custom: devplatv2
ms.openlocfilehash: 7dafb069419c1ff42d2ec5358fbd8eb37465c88f
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132552338"
---
# <a name="create-and-run-machine-learning-pipelines-using-components-with-the-azure-machine-learning-cli-preview"></a>Créer et exécuter des pipelines Machine Learning à l’aide de composants avec l’interface CLI d’Azure Machine Learning (préversion)

Dans cet article, vous allez apprendre à créer et à exécuter des [pipelines Machine Learning](concept-ml-pipelines.md) à l’aide d’Azure CLI et de composants (pour plus d’informations, consultez [Qu’est-ce qu’un composant Azure Machine Learning ?](concept-component.md)). Vous pouvez [créer des pipelines sans utiliser de composants](how-to-train-cli.md#build-a-training-pipeline), mais les composants offrent le plus de flexibilité et de possibilités de réutilisation. Vous pouvez définir des pipelines AzureML en YAML et les exécuter à partir de l’interface CLI, les créer dans Python ou les composer dans le concepteur AzureML Studio avec une interface utilisateur par glisser-déplacer. Ce document porte sur l’interface CLI.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="prerequisites"></a>Prérequis

* Si vous n’avez pas d’abonnement Azure, créez un compte gratuit avant de commencer. Essayez la [version gratuite ou payante d’Azure Machine Learning](https://azure.microsoft.com/free/).

* Vous aurez besoin d’un [espace de travail Azure Machine Learning](how-to-manage-workspace.md) pour vos pipelines et les ressources associées

* [Installer et configurer l’extension Azure CLI pour Machine Learning](how-to-configure-cli.md)

* Clonez le référentiel d’exemples :

    ```azurecli-interactive
    git clone https://github.com/Azure/azureml-examples --depth 1
    cd azureml-examples/cli/jobs/pipelines-with-components/
    ```

## <a name="introducing-machine-learning-pipelines"></a>Présentation des pipelines de machine learning

Les pipelines dans AzureML vous permettent de séquencer une collection de tâches de Machine Learning dans un workflow. Les scientifiques des données effectuent généralement des itérations avec des scripts qui se concentrent sur des tâches individuelles, comme la préparation des données, la formation, le scoring, etc. Lorsque tous ces scripts sont prêts, les pipelines permettent de connecter un ensemble de ces scripts à des processus de qualité en production qui sont les suivants :

| Avantage | Description |
| --- | --- |
| Autonome | Les pipelines peuvent s’exécuter de manière autonome pendant des heures, voire des jours, en utilisant des données en amont, en les traitant et en les passant à des scripts ultérieurs sans aucune intervention manuelle. |
| Puissant | Les pipelines peuvent s’exécuter sur des clusters de calcul de grande taille hébergés dans le cloud qui ont la capacité de traiter des jeux de données volumineux ou d’effectuer des milliers de balayages pour trouver les meilleurs modèles. | 
| Répétables et reproductibles | Les pipelines peuvent être planifiés pour exécuter et traiter de nouvelles données et mettre à jour les modèles de ML, ce qui rend les workflows ML reproductibles. | 
| Reproductibles | Les pipelines peuvent générer des résultats reproductibles en enregistrant toutes les activités et en conservant toutes les sorties, y compris les données intermédiaires dans le cloud, afin de répondre aux exigences de conformité et d’audit. |

Azure propose d’autres types de pipelines : les pipelines Azure Data Factory ont une prise en charge renforcée des pipelines de données à données, tandis qu’Azure Pipelines constitue le meilleur choix pour l’automatisation de l’intégration continue et de la livraison continue. [Comparez les pipelines Machine Learning avec ces différents pipelines](concept-ml-pipelines.md#which-azure-pipeline-technology-should-i-use).

## <a name="create-your-first-pipeline"></a>Créer votre premier pipeline

Dans le répertoire `cli/jobs/pipelines-with-components/basics` du [référentiel `azureml-examples`](https://github.com/Azure/azureml-examples), accédez au sous-répertoire `3a_basic_pipeline` (les exemples précédents de ce répertoire affichent des pipelines non-composants). Répertoriez vos ressources de calcul disponibles à l’aide de la commande suivante : 

```azurecli
az ml compute list
```

Si vous n’en avez pas, créez un cluster appelé `cpu-cluster` en exécutant :

```azurecli
az ml compute create -n cpu-cluster --type amlcompute --min-instances 0 --max-instances 10
```

À présent, créez un travail de pipeline avec la commande suivante :

```azurecli
az ml job create --file pipeline.yml
```

Vous devriez recevoir un dictionnaire JSON contenant des informations sur le travail de pipeline, notamment :

| Clé | Description | 
| --- | --- | 
| `name` | Le nom du travail basé sur le GUID. | 
| `experiment_name` | Le nom sous lequel les travaux seront organisés dans Studio. | 
| `services.Studio.endpoint` | L’URL pour la surveillance et l’examen du travail de pipeline. | 
| `status` | État du travail. Il s’agira probablement de `Preparing` à ce stade. |

### <a name="review-a-component"></a>Réviser un composant 

 Examinez rapidement le code source Python dans `componentA_src`, `componentB_src` et `componentC_src`. Comme vous pouvez le voir, chacun de ces répertoires contient un programme « Hello World » légèrement différent en Python. 

Ouvrez `ComponentA.yaml` pour voir comment le premier composant est défini : 

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/pipelines-with-components/basics/3a_basic_pipeline/componentA.yml":::

Dans la préversion actuelle, seuls les composants de type `command` sont pris en charge. `name` est l’identificateur unique et est utilisé dans Studio pour décrire le composant, et `display_name` est utilisé pour fournir un nom convivial. La paire clé-valeur `version` vous permet de faire évoluer vos composants de pipeline tout en conservant la reproductibilité avec les versions antérieures. 

Tous les fichiers de la valeur `code.local_path` seront téléchargés vers Azure à des fins de traitement. 

La section `environment` vous permet de spécifier l’environnement logiciel dans lequel le composant s’exécute. Dans ce cas, le composant utilise une image Docker de base, comme spécifié dans `environment.image`. Pour plus d’informations, consultez [Créer et utiliser des environnements logiciels dans Azure Machine Learning](how-to-use-environments.md). 

Enfin, la clé `command` est utilisée pour spécifier la commande à exécuter.

> [!NOTE]
> La valeur de `command` commence avec `>-`, ce qui est le « style de repli avec blocs rongés » de YAML. Cela vous permet d’écrire votre commande sur plusieurs lignes de texte pour plus de clarté.

Pour plus d’informations sur les composants et leurs spécifications, consultez [Qu’est-ce qu’un composant Azure Machine Learning ?](concept-component.md).

### <a name="review-the-pipeline-specification"></a>Vérifier la spécification du pipeline

Dans le répertoire de l’exemple, le fichier `pipeline.yaml` ressemble au code suivant :

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/pipelines-with-components/basics/3a_basic_pipeline/pipeline.yml":::

Si vous ouvrez l’URL du travail dans Studio (la valeur de `services.Studio.endpoint` à partir de la commande `job create` lors de la création d’un travail ou `job show` après la création du travail), vous verrez une représentation graphique de votre pipeline :

:::image type="content" source="media/how-to-create-component-pipelines-cli/pipeline-graph.png" lightbox="media/how-to-create-component-pipelines-cli/pipeline-graph.png" alt-text="Représentation graphique du pipeline dans Studio":::

Il n’existe aucune dépendance entre les composants dans ce pipeline. En règle générale, les pipelines ont des dépendances, et cette page les affiche visuellement. Étant donné que ces composants ne sont pas dépendants les uns des autres et que le `cpu-cluster` a suffisamment de nœuds, ils ont été exécutés simultanément. 

Si vous double-cliquez sur un composant dans le graphique de pipeline, vous pouvez afficher les détails de l’exécution enfant du composant. 

:::image type="content" source="media/how-to-create-component-pipelines-cli/component-details.png" alt-text="Capture d’écran montrant les détails et les sorties de l’exécution enfant d’un composant" lightbox="media/how-to-create-component-pipelines-cli/component-details.png"::: 

## <a name="upload-and-use-data"></a>Télécharger et utiliser des données

L’exemple `3b_pipeline_with_data` montre comment définir le stockage du flux de données d’entrée et de sortie dans les pipelines. 

Vous définissez les répertoires de données d’entrée pour votre pipeline dans le fichier YAML de pipeline à l’aide du chemin d’accès `inputs`. Vous définissez les répertoires de données de sortie et intermédiaires à l’aide du chemin d’accès `outputs`. Vous utilisez ces définitions dans les chemins d’accès `jobs.<JOB_NAME>.inputs` et `jobs.<JOB_NAME>.outputs`, comme indiqué dans l’image suivante :

:::image type="content" source="media/how-to-create-component-pipelines-cli/inputs-and-outputs.png" alt-text="Image illustrant la façon dont les chemins d’accès d’entrée et de sortie sont mappés aux entrées de travaux et aux chemins de sortie" lightbox="media/how-to-create-component-pipelines-cli/inputs-and-outputs.png":::

1. Le chemin d’accès `inputs.pipeline_sample_input_data` crée un identificateur de clé et charge les données d’entrée à partir du répertoire `local_path`. Cet identificateur `${{inputs.pipeline_sample_input_data}}` est ensuite utilisé comme valeur de la clé `jobs.componentA_job.inputs.componentA_input`. 
1. Le chemin d’accès `jobs.componentA_job.outputs.componentA_output` en tant qu’identificateur (`${{jobs.componentA_job.outputs.componentA_output`}}) utilisé comme valeur pour la clé `jobs.componentB_job.inputs.componentB_input` de l’étape suivante. 
1. Comme pour le composant A, la sortie du composant B est utilisée comme entrée pour le composant C.
1. La clé `outputs.final_pipeline_output` du pipeline est la source de l’identificateur utilisé comme valeur de la clé `jobs.componentC_job.outputs.componentC_output`. En d’autres termes, la sortie du composant C correspond à la sortie finale du pipeline.

La visualisation de ce pipeline par Studio ressemble à ceci : 

:::image type="content" source="media/how-to-create-component-pipelines-cli/pipeline-graph-dependencies.png" alt-text="Capture d’écran montrant la vue graphique de Studio d’un pipeline avec des dépendances de données" lightbox="media/how-to-create-component-pipelines-cli/pipeline-graph-dependencies.png":::

Vous pouvez voir que `inputs.pipeline_sample_input_data` est représenté sous la forme de `Dataset`. Les clés des chemins `jobs.<COMPONENT_NAME>.inputs` et `outputs` s’affichent sous la forme de flux de données entre les composants de pipeline.

Vous pouvez exécuter cet exemple en basculant vers le sous-répertoire `3b_pipeline_with_data` du référentiel d’exemples et en exécutant :

`az ml job create --file pipeline.yaml`

### <a name="access-data-in-your-script"></a>Accéder aux données dans votre script

Les chemins d’accès aux répertoires d’entrée et de sortie d’un composant sont passés à votre script en tant qu’arguments. Le nom de l’argument sera la clé que vous avez spécifiée dans le fichier YAML dans le chemin d’accès `inputs` ou `outputs`. Exemple :

```python
import argparse

parser = argparse.ArgumentParser()
parser.add_argument("--componentA_input", type=str)
parser.add_argument("--componentA_output", type=str)

args = parser.parse_args()

print("componentA_input path: %s" % args.componentA_input)
print("componentA_output path: %s" % args.componentA_output)

```

Pour les entrées, l’orchestrateur de pipeline télécharge (ou monte) les données à partir du magasin cloud et le rend disponible en tant que dossier local à lire pour le script qui s’exécute dans chaque travail. Ce comportement signifie que le script n’a pas besoin d’être modifié entre l’exécution locale et l’exécution sur le calcul cloud. De même, pour les sorties, le script écrit dans un dossier local monté et synchronisé dans le magasin cloud ou est téléchargé une fois le script terminé. Vous pouvez utiliser le mot clé `mode` pour spécifier le téléchargement ou le montage pour les entrées et le chargement ou le montage pour les sorties. 

## <a name="create-a-preparation-train-evaluate-pipeline"></a>Créer un pipeline de préparation-apprentissage-évaluation

L’un des scénarios les plus courants pour les pipelines de Machine Learning comporte trois phases majeures :

1. Préparation des données
1. Entrainement
1. Évaluation du modèle

Chacune de ces phases peut avoir plusieurs composants. Par exemple, la phase de préparation des données peut avoir des étapes distinctes pour le chargement et la transformation des données d’apprentissage. Le référentiel d’exemples contient un exemple de pipeline de bout en bout dans le répertoire `cli/jobs/pipelines-with-components/nyc_taxi_data_regression`. 

`job.yml` commence par la paire clé-valeur `type: pipeline` obligatoire. Ensuite, il définit les entrées et les sorties comme suit :

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/pipelines-with-components/nyc_taxi_data_regression/job.yml" range="5-22":::

Comme décrit précédemment, ces entrées spécifient les données d’entrée pour le pipeline, dans le cas présent, le jeu de données dans `./data`, et les sorties intermédiaires et finales du pipeline, qui sont stockées dans des chemins d’accès distincts. Les noms figurant dans ces entrées d’entrée et de sortie deviennent des valeurs dans les entrées `inputs` et `outputs` des travaux individuelles : 

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/pipelines-with-components/nyc_taxi_data_regression/job.yml" range="26-72":::

Notez comment `jobs.train_job.outputs.model_output` est utilisé comme entrée pour le travail de prédiction et le travail de scoring, comme indiqué dans le diagramme suivant : 

:::image type="content" source="media/how-to-create-component-pipelines-cli/regression-graph.png" alt-text="Graphique de pipeline de la tâche de prédiction des tarifs de taxi" lightbox="media/how-to-create-component-pipelines-cli/regression-graph.png":::

## <a name="caching--reuse"></a>Mise en cache et réutilisation  

Par défaut, seuls les composants dont les entrées ont été modifiées sont réexécutés. Vous pouvez modifier ce comportement en affectant à la clé `is_deterministic` de la spécification de composant YAML la valeur `False`. Un besoin courant pour cela est un composant qui charge des données qui ont pu être mises à jour à partir d’un emplacement fixe ou d’une URL. 

## <a name="faq"></a>Forum aux questions

### <a name="how-do-i-change-the-location-of-the-outputs-generated-by-the-pipeline"></a>Comment modifier l’emplacement des sorties générées par le pipeline ?
Vous pouvez utiliser la section `settings` du travail de pipeline pour spécifier un magasin de données de travail différent pour tous les travaux dans le pipeline (voir la ligne 25-26 dans [cet exemple](https://github.com/Azure/azureml-examples/blob/main/cli/jobs/pipelines-with-components/basics/1a_e2e_local_components/pipeline.yml)). La spécification d’un autre magasin de données pour un travail spécifique ou une sortie spécifique n’est pas prise en charge pour le moment. La spécification de chemins d’accès où les sorties sont inscrites sur le magasin de données n’est pas non plus prise en charge actuellement.

### <a name="how-do-i-specify-a-compute-that-can-be-used-by-all-jobs"></a>Comment spécifier un calcul qui peut être utilisé par tous les travaux ?
Vous pouvez spécifier un calcul au niveau du travail de pipeline, qui sera utilisé par les travaux qui ne mentionnent pas explicitement un calcul. (Voir la ligne 28 dans [cet exemple](https://github.com/Azure/azureml-examples/blob/main/cli/jobs/pipelines-with-components/basics/1a_e2e_local_components/pipeline.yml).)

### <a name="what-job-types-are-supported-in-the-pipeline-job"></a>Quels sont les types de travaux pris en charge dans le travail de pipeline ?
La version actuelle prend en charge les types de travaux de commande, de composant et de balayage.

### <a name="what-are-the-different-modes-that-i-use-with-inputs-or-outputs"></a>Quels sont les différents modes utilisés avec les entrées ou les sorties ?
| Category | Modes autorisés | Default |
| --- | --- | --- |
| Entrées de jeu de données | `ro_mount` et `download` | `ro_mount` |
| Entrées d’URI | `ro_mount`, `rw_mount` et `download` | `ro_mount` | 
| Sorties | `rw_mount`, `upload` | `rw_mount` | 

### <a name="when-do-i-use-command-jobs-vs-component-jobs"></a>Quand dois-je utiliser les travaux de commande et les travaux de composant ?
Vous pouvez effectuer une itération rapide avec les travaux de commande, puis les connecter à un pipeline. Toutefois, cela rend le pipeline monolithique. Si une personne doit utiliser l’une des étapes du pipeline dans un pipeline différent, elle doit copier la définition du travail, les scripts, l’environnement, etc. Si vous souhaitez que les étapes individuelles soient réutilisables entre les pipelines et faciles à comprendre et à utiliser pour les autres membres de votre équipe, les étapes supplémentaires de création et d’inscription sont logiques. L’autre raison pour laquelle vous devez envisager d’utiliser des composants est l’utilisation de l’interface utilisateur du concepteur en glisser-déplacer pour générer des pipelines. Étant donné que les travaux ne sont pas inscrits avec l’espace de travail, vous ne pouvez pas les glisser-déplacer dans le canevas du concepteur.

### <a name="im-doing-distributed-training-in-my-component-the-component-which-is-registered-specifies-distributed-training-settings-including-node-count-how-can-i-change-the-number-of-nodes-used-during-runtime-the-optimal-number-of-nodes-is-best-determined-at-runtime-so-i-dont-want-to-update-the-component-and-register-a-new-version"></a>Je fais une formation distribuée dans mon composant. Le composant, qui est inscrit, spécifie les paramètres de formation distribués, y compris le nombre de nœuds. Comment modifier le nombre de nœuds utilisés lors de l’exécution ? Le nombre optimal de nœuds est mieux déterminé au moment de l’exécution, donc je ne souhaite pas mettre à jour le composant et enregistrer une nouvelle version.

Vous pouvez utiliser la section Remplacements du travail de composant pour modifier les paramètres de ressource et de distribution. Consultez [cet exemple utilisant TensorFlow](https://github.com/Azure/azureml-examples/tree/main/cli/jobs/pipelines-with-components/basics/6a_tf_hello_world) ou [cet exemple utilisant PyTorch](https://github.com/Azure/azureml-examples/tree/main/cli/jobs/pipelines-with-components/basics/6c_pytorch_hello_world).  

### <a name="how-can-i-define-an-environment-with-conda-dependencies-inside-a-component"></a>Comment puis-je définir un environnement avec des dépendances Conda dans un composant ?
Consultez [cet exemple](https://github.com/Azure/azureml-examples/tree/main/cli/jobs/pipelines-with-components/basics/5c_env_conda_file).
 

## <a name="next-steps"></a>Étapes suivantes

- Pour partager votre pipeline avec des collègues ou des clients, consultez [Publier des pipelines Machine Learning](how-to-deploy-pipelines.md)
- Utilisez [ces notebooks Jupyter sur GitHub](https://aka.ms/aml-pipeline-readme) pour explorer plus en détail les pipelines Machine Learning
- Consultez l’aide de référence du SDK pour les packages [azureml-pipelines-core](/python/api/azureml-pipeline-core/) et [azureml-pipelines-steps](/python/api/azureml-pipeline-steps/)
- Pour obtenir des conseils sur le débogage et la résolution des problèmes, consultez [Résolution des problèmes de pipeline Machine Learning](how-to-debug-pipelines.md)
- Découvrez comment exécuter des notebooks dans l’article [Utiliser des notebooks Jupyter pour explorer ce service](samples-notebooks.md).
