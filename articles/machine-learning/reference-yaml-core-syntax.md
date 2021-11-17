---
title: Syntaxe YAML fondamentale de CLI (v2)
titleSuffix: Azure Machine Learning
description: Vue d’ensemble de la syntaxe YAML fondamentale de CLI (v2).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: mx-iao
ms.author: minxia
ms.date: 10/21/2021
ms.reviewer: laobri
ms.openlocfilehash: 943de3f7327c02e4bdb20bb364f530685bce2965
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131566793"
---
# <a name="cli-v2-core-yaml-syntax"></a>Syntaxe YAML fondamentale de CLI (v2)

Chaque entité Azure Machine Learning a une représentation YAML schématisée. Vous pouvez créer une entité à partir d’un fichier de configuration YAML avec une extension `.yml` ou `.yaml`.

Cet article fournit une vue d’ensemble des concepts de la syntaxe fondamentale que vous rencontrerez lors de la configuration de ces fichiers YAML.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="referencing-an-azure-ml-entity"></a>Référencement d’une entité Azure Machine Learning

Azure Machine Learning fournit une syntaxe de référence (en formats abrégé et long) pour référencer une entité Azure Machine Learning existante lors de la configuration d’un fichier YAML. Par exemple, vous pouvez référencer un environnement enregistré existant dans votre espace de travail afin de l’utiliser comme environnement pour un travail.

### <a name="shorthand"></a>Forme abrégée

La syntaxe courte est la suivante :

* Pour les éléments multimédias : `azureml:<asset-name>:<asset-version>`
* Pour les ressources : `azureml:<resource-name>`

Azure Machine Learning résout cette référence en l’élément multimédia ou la ressource spécifiés dans l’espace de travail.

### <a name="longhand"></a>Forme longue

La syntaxe longue se compose du préfixe `azureml:` et de l’ID de ressource ARM de l’entité :

```
azureml:/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>/environments/<environment-name>/versions/<environment-version>
```

## <a name="azure-ml-data-reference-uri"></a>URI de référence des données Azure Machine Learning

Azure Machine Learning offre un format d’URI de référence de données pratique pour pointer vers des données dans un service de stockage Azure. Celui-ci peut être utilisé dans des scénarios où vous devez spécifier un emplacement de stockage cloud dans votre fichier YAML, par exemple, lorsque vous créez un modèle Azure Machine Learning à partir de fichiers dans le stockage, ou pointez vers des données à transmettre en tant qu’entrée à un travail.

Pour utiliser ce format d’URI de données, le service de stockage que vous souhaitez référencer doit être préalablement enregistré en tant que magasin de données dans votre espace de travail. Azure Machine Learning gère l’accès aux données à l’aide des informations d’identification que vous avez fournies lors de la création du magasin de données.

Le format se compose d’un magasin de données dans l’espace de travail actuel, et du chemin d’accès dans ce magasin de données au fichier ou dossier vers lesquels vous souhaitez pointer :

```
azureml://datastores/<datastore-name>/paths/<path-on-datastore>/
```

Par exemple :

* `azureml://datastores/workspaceblobstore/paths/example-data/`
* `azureml://datastores/workspaceblobstore/paths/example-data/iris.csv`

En plus de l’URI de référence de données Azure Machine Learning, Azure Machine Learning prend en charge les protocoles d’URI de stockage direct `https` , `wasbs` , `abfss` et `adl`, ainsi que les URI `http` et `https` publics.

## <a name="expression-syntax-for-configuring-azure-ml-jobs-and-components"></a>Syntaxe d’expression pour la configuration des travaux et composants Azure Machine Learning

Les fichiers YAML v2 de travail et de composant permettent d’utiliser des expressions pour la liaison à des contextes dans différents scénarios. Le cas d’usage essentiel consiste à utiliser une expression pour une valeur qui peut ne pas être connue au moment de la création de la configuration, mais qui doit être résolue au moment de l’exécution.

Utilisez la syntaxe suivante pour indiquer à Azure Machine Learning d’évaluer une expression au lieu de la traiter comme une chaîne :

`${{ <expression> }}`

Les scénarios pris en charge sont décrits ci-dessous.

### <a name="parameterizing-the-command-with-the-inputs-and-outputs-contexts-of-a-job"></a>Paramétrage de la `command` avec les contextes `inputs` et `outputs` d’un travail

Vous pouvez spécifier des valeurs littérales, des chemins d’accès d’URI et des jeux de données Azure Machine Learning comme entrées d’un travail. La `command` peut ensuite être paramétrée avec des références à ces entrées à l’aide de la syntaxe `${{inputs.<input-name>}}`. Les références aux entrées littérales seront résolues en la valeur littérale lors du runtime, tandis que les références à l’URI de données ou aux entrées du jeu de données Azure Machine Learning seront résolues en chemin de téléchargement ou chemin de montage (selon le `mode` spécifié).

De même, les sorties vers le travail peuvent également être référencées dans la `command`. Pour chaque sortie nommée spécifiée dans le dictionnaire `outputs`, Azure ML génère automatiquement un emplacement de sortie sur le magasin de données par défaut dans lequel vous pouvez écrire des fichiers. L’emplacement de sortie de chaque sortie nommée est basé sur le chemin d’accès modélisé suivant : `<default-datastore>/azureml/<job-name>/<output-name>/`. Le paramétrage de la `command` avec la syntaxe `${{outputs.<output-name>}}` permet de résoudre cette référence en chemin d’accès généré automatiquement, afin que votre script puisse écrire des fichiers dans cet emplacement à partir du travail.

Dans l’exemple ci-dessous, pour un fichier YAML de travail de commande, la `command` est paramétrée avec deux entrées, une entrée littérale et une entrée d’URI, ainsi qu’une sortie. Lors du runtime, l’expression `${{inputs.learning_rate}}` est résolue en `0.01`, et l’expression `${{inputs.iris}}` en chemin de téléchargement du fichier `iris.csv`. `${{outputs.model_dir}}` sera résolu en chemin d’accès de montage de l’emplacement de sortie généré automatiquement.

```yaml
$schema: https://azuremlschemas.azureedge.net/latest/commandJob.schema.json
code:
  local_path: ./src
command: python train.py --lr ${{inputs.learning_rate}} --training-data ${{inputs.iris}} --model-dir ${{outputs.model_dir}}
environment: azureml:AzureML-Minimal:1
compute: azureml:cpu-cluster
inputs:
  learning_rate: 0.01
  iris:
    file: https://azuremlexamples.blob.core.windows.net/datasets/iris.csv
    mode: download
outputs:
  model_dir:
```

### <a name="parameterizing-the-command-with-the-search_space-context-of-a-sweep-job"></a>Paramétrage de la `command` avec le contexte `search_space` d’un travail de balayage

Vous utiliserez également cette syntaxe d’expression lorsque vous effectuerez un réglage d’hyperparamètre via un travail de balayage, car les valeurs réelles des hyperparamètres ne sont pas connues au moment de la création du travail. Quand vous exécutez un travail de balayage, Azure Machine Learning sélectionne des valeurs d’hyperparamètres pour chaque évaluation basée sur le `search_space`. Pour accéder à ces valeurs dans votre script d’apprentissage, vous devez les passer via les arguments de ligne de commande du script. À cette fin, utilisez la syntaxe `${{search_space.<hyperparameter>}}` dans la `trial.command`.

Dans l’exemple ci-dessous pour un fichier YAML de travail de balayage, les références `${{search_space.learning_rate}}` et `${{search_space.boosting}}` dans `trial.command` seront résolues en valeurs d’hyperparamètres réelles sélectionnées pour chaque évaluation lors de la soumission du travail d’évaluation pour exécution.

```yaml
$schema: https://azuremlschemas.azureedge.net/latest/sweepJob.schema.json
type: sweep
sampling_algorithm: random
search_space:
  learning_rate:
    type: uniform
    min_value: 0.01
    max_value: 0.9
  boosting:
    type: choice
    values: ["gbdt", "dart"]
objective:
  goal: minimize
  primary_metric: test-multi_logloss
trial:
  code: 
    local_path: src 
  command: >-
    python train.py 
    --training-data ${{inputs.iris}}
    --lr ${{search_space.learning_rate}}
    --boosting ${{search_space.boosting}}
  environment: azureml:AzureML-Minimal:1
inputs:
  iris:
    file: https://azuremlexamples.blob.core.windows.net/datasets/iris.csv
    mode: download
compute: azureml:cpu-cluster
```

### <a name="binding-inputs-and-outputs-between-steps-in-a-pipeline-job"></a>Liaison d’entrées et de sorties entre les étapes d’un travail de pipeline

Des expressions sont également utilisées pour lier des entrées et sorties entre les étapes d’un travail de pipeline. Par exemple, vous pouvez lier l’entrée d’un travail (travail 2) dans un pipeline à la sortie d’un autre travail (travail 1). Cette utilisation signale à Azure Machine Learning le flux de dépendance du graphique de pipeline, et le travail 2 est exécuté après le travail 1 parce que la sortie du travail 1 est requise comme entrée pour le travail 2.

Pour un fichier YAML de travail de pipeline, les sections `inputs` et `outputs` de chaque travail enfant sont évaluées dans le contexte parent (travail de pipeline de niveau supérieur). En revanche, la `command` est résolue en contexte actuel (travail enfant).

Il existe deux façons de lier des entrées et des sorties dans un travail de pipeline :

**1) Lier aux entrées et sorties de niveau supérieur du travail de pipeline**

Vous pouvez lier les entrées ou sorties d’un travail enfant aux entrées/sorties du travail de pipeline parent de niveau supérieur à l’aide de la syntaxe suivante : `${{inputs.<input-name>}}` ou `${{outputs.<output-name>}}`. Cette référence est résolue en contexte parent, donc les entrées et sorties de niveau supérieur. 

Dans l’exemple ci-dessous, la sortie (`model_dir`) de l’étape `train` finale est liée à la sortie du travail de pipeline de niveau supérieur via `${{outputs.trained_model}}`

**2) lier aux entrées et sorties d’un autre travail enfant (étape)**

Pour lier les entrées et sorties d’une étape aux entrées et sorties d’une autre étape, utilisez la syntaxe suivante : `${{jobs.<step-name>.inputs.<input-name>}}` ou `${{jobs.<step-name>.outputs.<outputs-name>}}`. Là encore, cette référence est résolue en contexte parent, de sorte que le contexte commence par `jobs.<step-name>`.

Dans l’exemple ci-dessous, l’entrée (`clean_data`) de l’étape `train` est liée à la sortie (`prep_data`) de l’étape `prep` via `${{jobs.prep.outputs.prep_data}}`. Les données préparées à l’étape `prep` seront utilisées comme données d’apprentissage pour l’étape `train`.

En revanche, les références de contexte dans les propriétés `command` seront résolues en contexte actuel. Par exemple, la référence `${{inputs.raw_data}}` dans la `command` de l’étape `prep` sera résolue en entrées du contexte actuel qui est le travail enfant `prep`. La recherche sera effectuée sur `prep.inputs`. Une entrée nommée `raw_data` doit y être définie.

```yaml
$schema: https://azuremlschemas.azureedge.net/latest/pipelineJob.schema.json
type: pipeline
inputs:
outputs:
  trained_model:
jobs:
  prep:
    type: command
    inputs:
      raw_data:
        folder:
        mode: rw_mount
    outputs:
      prep_data: 
        mode: upload
    code:
      local_path: src/prep
    environment: azureml:AzureML-Minimal:1
    command: >-
      python prep.py 
      --raw-data ${{inputs.raw_data}} 
      --prep-data ${{outputs.prep_data}}
    compute: azureml:cpu-cluster
  train:
    type: command
    inputs: 
      clean_data: ${{jobs.prep.outputs.prep_data}}
    outputs:
      model_dir: $${{outputs.trained_model}}
    code: 
      local_path: src/train
    environment: azureml:AzureML-Minimal:1
    compute: azureml:gpu-cluster
    command: >-
      python train.py 
      --training-data ${{inputs.clean_data}} 
      --model-output ${{outputs.model_dir}}
```

### <a name="parameterizing-the-command-with-the-inputs-and-outputs-contexts-of-a-component"></a>Paramétrage de la `command` avec les contextes `inputs` et `outputs` d’un composant

Comme la `command` pour un travail, la `command` pour un composant peut être paramétrée avec des références aux contextes `inputs` et `outputs`. Dans ce cas, la référence a trait aux entrées et sorties du composant. Quand le composant est exécuté dans un travail, Azure ML résout ces références en valeurs d’entrée et de sortie du runtime de travail spécifiées pour les entrées et sorties de composant correspondantes. Voici un exemple d’utilisation de la syntaxe de contexte pour une spécification YAML de composant de commande.

```yaml
$schema: https://azuremlschemas.azureedge.net/latest/commandComponent.schema.json
type: command
code:
  local_path: ./src
command: python train.py --lr ${{inputs.learning_rate}} --training-data ${{inputs.iris}} --model-dir ${{outputs.model_dir}}
environment: azureml:AzureML-Minimal:1
inputs:
  learning_rate:
    type: number
    default: 0.01
    optional: true
  iris:
    type: path
outputs:
  model_dir:
    type: path
```

## <a name="next-steps"></a>Étapes suivantes

* [Installer et utiliser l’interface CLI (v2)](how-to-configure-cli.md)
* [Effectuer l’apprentissage de modèles avec l’interface CLI (v2)](how-to-train-cli.md)
* [Schémas YAML CLI (v2)](reference-yaml-overview.md)
