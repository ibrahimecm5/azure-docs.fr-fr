---
title: Qu’est un composant (préversion) ?
titleSuffix: Azure Machine Learning
description: Utilisez des composants Azure Machine Learning pour générer des pipelines de Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: keli19
author: likebupt
ms.reviewer: laobri
ms.date: 10/21/2021
ms.topic: conceptual
ms.openlocfilehash: 5846a84df6c11364fed6fa65b852b55c42cd5364
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131566903"
---
# <a name="what-is-an-azure-machine-learning-component-preview"></a>Qu’est-ce qu’un composant Azure Machine Learning (préversion) ?

Un composant Azure Machine Learning (précédemment appelé module) est un élément de code autonome qui effectue une étape dans un pipeline Machine Learning. Les composants sont les blocs de construction des pipelines de Machine Learning avancés (consultez [Créer et exécuter des pipelines de Machine Learning avec l’interface CLI Azure Machine Learning](how-to-create-component-pipelines-cli.md)). Les composants peuvent effectuer des tâches comme le traitement des données, l’apprentissage du modèle, le scoring du modèle, etc.

Un composant est analogue à une fonction : il dispose d’un nom et de paramètres, il attend une entrée et retourne une sortie. Pour plus d’informations sur la création d’un composant, consultez [Créer un composant](#define-a-component-preview).

## <a name="why-should-i-use-a-component-preview"></a>Pourquoi utiliser un composant (préversion) ?

Les composants vous permettent de gérer et de réutiliser la logique commune entre les pipelines.

- **Composable** : les composants permettent aux développeurs de masquer la logique complexe derrière une interface simple. Les utilisateurs des composants n’ont pas à se soucier de la logique sous-jacente, ils doivent uniquement fournir des paramètres.

- **Partage et réutilisation** : les composants sont automatiquement partagés avec les utilisateurs dans le même espace de travail. Vous pouvez réutiliser les composants dans des pipelines, des environnements, des espaces de travail et des abonnements. Le suivi de version intégré vous permet d’effectuer le suivi des modifications et de reproduire les résultats.

- **Prise en charge de l’interface CLI** : utilisez les composants pour créer des pipelines dans l’interface CLI (v2).


## <a name="define-a-component-preview"></a>Définir un composant (préversion)

Pour définir un composant Azure Machine Learning, vous devez fournir deux fichiers :

- Une spécification de composant au [format de spécification de composant YAML](reference-yaml-component-command.md) valide. Ce fichier spécifie les informations suivantes :
  - Métadonnées : nom, nom d’affichage, version, type, etc.
  - Interface : entrées et sorties
  - Commande, code et environnement : la commande, le code et l’environnement utilisés pour exécuter le composant
- Script pour fournir la logique d’exécution réelle.

### <a name="component-specification"></a>Spécification du composant

Le fichier de spécification du composant définit les métadonnées et les paramètres d’exécution d’un composant. Les spécifications du composant indiquent au service Azure Machine Learning comment exécuter le script Python que vous fournissez.

L’exemple suivant est une spécification de composant pour un composant d’apprentissage.

```yaml
name: Example_Train
display_name: Example Train
version: 20
type: command
description: Example of a torchvision training component
tags: {category: Component Tutorial, contact: user@contoso.com}
inputs:
  training_data: 
    type: path
    description: Training data organized in torchvision structure
  max_epochs:
    type: integer
    description: Maximum epochs for training
  learning_rate: 
    type: number
    description: Learning rate, default is 0.01
    default: 0.01
  learning_rate_schedule: 
    type: string
    default: time-based 
outputs:
  model_output:
    type: path
code:
  local_path: ./train_src
environment: azureml:AzureML-Minimal:1
command: >-
  python train.py 
  --training_data ${{inputs.training_data}} 
  --max_epochs ${{inputs.max_epochs}}   
  --learning_rate ${{inputs.learning_rate}} 
  --learning_rate_schedule ${{inputs.learning_rate_schedule}} 
  --model_output ${{outputs.model_output}}
```

Le tableau suivant décrit les champs de l’exemple. Pour obtenir la liste complète des champs disponibles, consultez la [page de référence de la spécification du composant YAML](reference-yaml-component-command.md).

| Nom                | Type                                                     | Obligatoire | Description                                                  |
| ------------------- | -------------------------------------------------------- | -------- | ------------------------------------------------------------ |
| name                | string                                                   | Oui      | Nom du composant. Il doit s’agir d’un identificateur unique du composant. Doit commencer par un chiffre ou une lettre et doit contenir uniquement des lettres, des chiffres, `_` et `-`. La longueur maximale est de 255 caractères.|
| version             | string                                                   | Oui      | Numéro de version du composant. Doit être une chaîne. |
| display_name        | string                                                   | Non       | Nom d’affichage du composant. La valeur par défaut est identique à `name`. |
| type                | string                                                   | Non       | Type du composant. Actuellement, cette valeur doit être `command`.|
| description         | string                                                   | Non       | Description détaillée du composant. |
| tags                | Dictionary&lt;string&gt;                                       | Non | Liste de paires clé-valeur pour décrire différentes perspectives du composant. La clé et la valeur de chaque étiquette doivent être un mot ou une expression courte, par exemple `Product:Office`, `Domain:NLP`, `Scenario:Image Classification`. |
| is_deterministic    | boolean                                                  | Non       | Détermine si le composant génère toujours le même résultat quand il reçoit les mêmes données d’entrée. Par défaut, il s’agit de `True`. Doit être défini sur `False` pour les composants qui vont charger des données à partir de ressources externes, par exemple pour importer des données à partir d’une URL donnée, puisque les données sont susceptibles d’être mises à jour. |
| inputs              | Dictionary&lt;string, Input&gt; | Non       | Définit les ports d’entrée et les paramètres du composant. La clé de chaîne est le nom de l’entrée, qui doit être un nom de variable Python valide. |
| outputs             | Dictionary&lt;string, Output&gt;                    | Non       | Définit les ports de sortie du composant. La clé de chaîne est le nom de la sortie, qui doit être un nom de variable Python valide. |
| code                | string                                                   | Non       | Chemin du code source. |
| Environnement         | Environnement                              | Non       | Environnement d’exécution du composant à exécuter. |
| command             | string                                             | Non    | Commande permettant d’exécuter le code du composant.         |

### <a name="python-script"></a>Script Python

Votre script Python contient la logique exécutable de votre composant. Votre script informe Azure Machine Learning des actions que vous souhaitez effectuer via le composant.

Pour exécuter, vous devez faire correspondre les arguments de votre script Python et les arguments que vous avez définis dans la spécification YAML. L’exemple suivant est un script d’apprentissage Python qui correspond à la spécification YAML de la section précédente.

```python
## Required imports 
import argparse
import os
## Import other dependencies your script needs
from pathlib import Path
from uuid import uuid4
from datetime import datetime

## Define an argument parser that matches the arguments from the components specification file
parser = argparse.ArgumentParser("train")
parser.add_argument("--training_data", type=str, help="Path to training data")
parser.add_argument("--max_epochs", type=int, help="Max # of epochs for the training")
parser.add_argument("--learning_rate", type=float, help="Learning rate")
parser.add_argument("--learning_rate_schedule", type=str, help="Learning rate schedule")
parser.add_argument("--model_output", type=str, help="Path of output model")

args = parser.parse_args()

## Implement your custom logic (in this case a training script)
print ("hello training world...")

lines = [
    f'Training data path: {args.training_data}',
    f'Max epochs: {args.max_epochs}',
    f'Learning rate: {args.learning_rate}',
    f'Learning rate: {args.learning_rate_schedule}',
    f'Model output path: {args.model_output}',
]

for line in lines:
    print(line)

print("mounted_path files: ")
arr = os.listdir(args.training_data)
print(arr)

for filename in arr:
    print ("reading file: %s ..." % filename)
    with open(os.path.join(args.training_data, filename), 'r') as handle:
        print (handle.read())

## Do the train and save the trained model as a file into the output folder.
## Here only output a dummy data for example.
curtime = datetime.now().strftime("%b-%d-%Y %H:%M:%S")
model = f"This is a dummy model with id: {str(uuid4())} generated at: {curtime}\n"
(Path(args.model_output) / 'model.txt').write_text(model)

```

:::image type="content" source="media/concept-component/component-introduction.png" lightbox="media/concept-component/component-introduction.png" alt-text="Document conceptuel présentant le mappage entre les éléments de code source et l’interface utilisateur du composant." :::

## <a name="create-a-component"></a>Créer un composant

### <a name="create-a-component-using-cli-v2"></a>Créer un composant à l’aide de l’interface CLI (v2)

Une fois que vous avez défini votre spécification de composant et les fichiers de script Python, après avoir [installé l’interface CLI (v2) avec succès](how-to-configure-cli.md), vous pouvez créer le composant dans vos espaces de travail à l’aide de :

```azurecli
az ml component create --file my_component.yml --version 1 --resource-group my-resource-group --workspace-name my-workspace
```

Utilisez `az ml component create --help`pour plus d’informations sur la commande `create`.

### <a name="create-a-component-in-the-studio-ui"></a>Créer un composant dans l’interface utilisateur de studio

Vous pouvez créer un composant dans la page **Composants** de l’interface utilisateur de studio.

1. Dans la page des composants, cliquez sur **Nouveau composant**.

    :::image type="content" source="./media/concept-component/ui-create-component.png" lightbox="./media/concept-component/ui-create-component.png" alt-text="Capture d’écran montrant le bouton Nouveau composant":::.

1. Suivez les étapes de l’Assistant pour terminer le processus de création.

## <a name="use-components-to-build-ml-pipelines"></a>Utiliser des composants pour créer des pipelines de ML

Vous pouvez utiliser l’interface Azure CLI (v2) pour créer un travail de pipeline. Consultez [Créer et exécuter des pipelines ML (CLI)](how-to-create-component-pipelines-cli.md).

## <a name="manage-components"></a>Gérer les composants

Vous pouvez vérifier les détails du composant et gérer le composant à l’aide de l’interface CLI (v2). Utilisez `az ml component -h` pour obtenir des instructions détaillées sur la commande de composant.

### <a name="list-components"></a>Répertorier les composants

Vous pouvez utiliser `az ml component list` pour répertorier l’ensemble des composants d’un espace de travail.

Vous pouvez voir l’ensemble des composants créés dans votre espace de travail dans la page **Composants** de l’interface utilisateur de studio.

### <a name="show-details-for-a-component"></a>Afficher les détails d’un composant

Vous pouvez utiliser `az ml component show --name <COMPONENT_NAME>` pour afficher les détails d’un composant.

Vous pouvez également vérifier les détails du composant dans la page **Composants** de l’interface utilisateur de studio.


### <a name="upgrade-a-component"></a>Mettre à niveau un composant

Vous pouvez utiliser `az ml component create --file <NEW_VERSION.yaml>` pour mettre à niveau un composant.

Vous pouvez également cliquer sur **Mettre à niveau** dans la page Détails du composant pour mettre à niveau une nouvelle version du composant.

:::image type="content" source="./media/concept-component/upgrade-component.png" lightbox="./media/concept-component/upgrade-component.png" alt-text="Capture d’écran montrant le bouton de mise à niveau.":::

### <a name="delete-a-component"></a>Suppression d'un composant

Vous pouvez utiliser `az ml component delete --name <COMPONENT_NAME>` pour supprimer un composant. 

Vous pouvez également sélectionner un composant et l’archiver.

:::image type="content" source="./media/concept-component/archive-component.png" alt-text="Capture d’écran montrant l’option d’archivage pour un composant.":::

## <a name="next-steps"></a>Étapes suivantes

- [Référence YAML du composant](reference-yaml-component-command.md)
- [Créer et exécuter des pipelines ML (CLI)](how-to-create-component-pipelines-cli.md)
- [Générer des pipelines de Machine Learning dans le concepteur](tutorial-designer-automobile-price-train-score.md)
