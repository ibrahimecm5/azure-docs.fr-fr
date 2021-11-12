---
title: Utiliser des points de terminaison de traitement de lots pour le scoring par lots
titleSuffix: Azure Machine Learning
description: Dans cet article, vous allez apprendre à créer un point de terminaison de traitement de lots pour effectuer un scoring par lots continu de données volumineuses.
services: machine-learning
ms.service: machine-learning
ms.subservice: mlops
ms.topic: conceptual
author: tracych
ms.author: tracych
ms.reviewer: laobri
ms.date: 10/21/2021
ms.custom: how-to, devplatv2
ms.openlocfilehash: 481227a747fca327f107049734a69008aa3c3bcf
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132063776"
---
# <a name="use-batch-endpoints-preview-for-batch-scoring"></a>Utiliser des points de terminaison de traitement de lots (préversion) pour le scoring par lots

Apprenez à utiliser des points de terminaison de traitement de lots (préversion) pour effectuer un scoring par lots. Les points de terminaison de traitement de lots simplifient le processus d’hébergement de vos modèles pour le scoring par lots, ce qui vous permet de vous concentrer sur le machine learning, et non sur l’infrastructure. Pour plus d’informations, consultez [Que sont les points de terminaison Azure Machine Learning (préversion) ?](concept-endpoints.md).

Cet article vous montre comment effectuer les tâches suivantes :

> [!div class="checklist"]
> * Créer un point de terminaison de lot et un déploiement par lots par défaut
> * Démarrer un travail de scoring par lots à l’aide de l’interface Azure CLI
> * Surveiller la progression de l’exécution du travail de scoring par lots et vérifier les résultats du scoring
> * Déployer un nouveau modèle MLflow avec le code et l’environnement générés automatiquement sur un point de terminaison existant sans affecter le processus existant
> * Tester le nouveau déploiement et le définir en tant que déploiement par défaut
> * Supprimer le point de terminaison et le déploiement non utilisés

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="prerequisites"></a>Prérequis

* Pour utiliser Azure Machine Learning, vous devez disposer d’un abonnement Azure. Si vous n’avez pas d’abonnement Azure, créez un compte gratuit avant de commencer. Essayez la [version gratuite ou payante d’Azure Machine Learning](https://azure.microsoft.com/free/) dès aujourd’hui.

* Installez Azure CLI et l’extension `ml`. Suivez les étapes d’installation décrites dans [Installer, configurer et utiliser l’interface CLI (v2) (préversion)](how-to-configure-cli.md).

* Créez un groupe de ressources Azure si vous n’en avez pas et que vous (ou le principal du service que vous utilisez) devez disposer de l’autorisation `Contributor`. Pour la création d’un groupe de ressources, consultez [Installer, configurer et utiliser l’interface CLI (v2) (préversion)](how-to-configure-cli.md). 

* Créez un espace de travail Azure Machine Learning si vous n’en avez pas déjà un. Pour la création d’un espace de travail, consultez [Installer, configurer et utiliser l’interface CLI (v2) (préversion)](how-to-configure-cli.md). 

* Configurez votre espace de travail et votre groupe de ressources par défaut pour Azure CLI. Les commandes CLI de Machine Learning nécessitent les paramètres `--workspace/-w` et `--resource-group/-g`. Configurez les valeurs par défaut pour éviter d’avoir à passer les valeurs plusieurs fois. Vous pouvez les remplacer sur la ligne de commande. Exécutez le code suivant pour configurer vos valeurs par défaut. Pour plus d’informations, consultez [Installer, configurer et utiliser l’interface CLI (v2) (préversion)](how-to-configure-cli.md).

```azurecli
az account set -s "<subscription ID>"
az configure --defaults group="<resource group>" workspace="<workspace name>" location="<location>"
```

### <a name="clone-the-example-repository"></a>Cloner le référentiel d’exemples

Exécutez les commandes suivantes pour cloner l’[exemple de référentiel AzureML](https://github.com/Azure/azureml-examples), et accédez au répertoire `cli`. Cet article utilise les ressources dans `/cli/endpoints/batch`, et l’exemple de travail de bout en bout est `/cli/batch-score.sh`.

```azurecli
git clone https://github.com/Azure/azureml-examples 
cd azureml-examples/cli
```

Définissez le nom de votre point de terminaison. Remplacez `YOUR_ENDPOINT_NAME` par un nom unique dans une région Azure.

Pour UNIX, exécutez la commande suivante :

:::code language="azurecli" source="~/azureml-examples-main/cli/batch-score.sh" ID="set_variables" :::

pour Windows, exécutez la commande suivante :

```azurecli
set ENDPOINT_NAME="<YOUR_ENDPOINT_NAME>"
```

> [!NOTE]
> Les noms de point de terminaison de lot doivent être uniques au sein d’une région Azure. Par exemple, il ne peut y avoir qu’un seul point de terminaison de lot avec le nom mybatchendpoint dans westus2.

### <a name="create-compute"></a>Créer une capacité de calcul

Le point de terminaison de lot s’exécute uniquement sur des ressources de cloud computing, pas localement. La ressource cloud computing est un cluster de machines virtuelles réutilisable. Exécutez le code suivant pour créer un cluster de calcul Azure Machine Learning. Les exemples suivants de cet article utilisent le calcul créé ici, nommé `batch-cluster`. Ajustez-les en fonction des besoins et référencez votre calcul à l’aide de `azureml:<your-compute-name>`.

:::code language="azurecli" source="~/azureml-examples-main/cli/batch-score.sh" ID="create_compute" :::

> [!NOTE]
> Vous n’êtes pas facturé pour le calcul à ce stade, car le cluster reste à 0 nœud jusqu’à ce qu’un point de terminaison de lot soit appelé et qu’un travail de scoring par lots soit envoyé. Apprenez-en davantage sur [la gestion et l’optimisation des coûts pour AmlCompute](how-to-manage-optimize-cost.md#use-azure-machine-learning-compute-cluster-amlcompute).

## <a name="understand-batch-endpoints-and-batch-deployments"></a>Comprendre les points de terminaison de lot et les déploiements par lots

Un point de terminaison de lot est un point de terminaison HTTPS que les clients peuvent appeler pour déclencher un travail de scoring par lots. Un travail de scoring par lots est un travail qui évalue plusieurs entrées (pour plus d’informations, consultez [Que sont les points de terminaison de lot ?](concept-endpoints.md#what-are-batch-endpoints-preview)). Un déploiement de lot est un ensemble de ressources de calcul hébergeant le modèle qui effectue le scoring réel. Un point de terminaison de traitement de lots peut avoir plusieurs déploiements de lot. 

> [!TIP]
> L’un des déploiements par lots servira de déploiement par défaut pour le point de terminaison. Le déploiement par défaut sera utilisé pour effectuer le scoring par lots réel lorsque le point de terminaison est appelé. En savoir plus sur [les points de terminaison de lot et le déploiement par lots](concept-endpoints.md#what-are-batch-endpoints-preview).

Le fichier YAML suivant définit un point de terminaison de lot, que vous pouvez inclure dans la commande CLI pour la création d’un [point de terminaison de lot](#create-a-batch-endpoint). Dans le référentiel, ce fichier se trouve à l’emplacement `/cli/endpoints/batch/batch-endpoint.yml`.

:::code language="yaml" source="~/azureml-examples-main/cli/endpoints/batch/batch-endpoint.yml":::

Le tableau suivant décrit les propriétés de clé du point de terminaison YAML. Pour le schéma YAML du point de terminaison de lot complet, consultez la page [Schéma YAML du point de terminaison de lot CLI (v2)](./reference-yaml-endpoint-batch.md).

| Clé | Description |
| --- | ----------- |
| `$schema` | [Facultatif] Schéma YAML. Vous pouvez afficher le schéma dans l’exemple ci-dessus dans un navigateur pour afficher toutes les options disponibles pour un fichier YAML de point de terminaison de traitement de lots. |
| `name` | Nom du point de terminaison de lot. Doit être unique au niveau de la région Azure.|
| `auth_mode` | Méthode d’authentification pour le point de terminaison de lot. Actuellement, seule l’authentification basée sur les jetons Azure Active Directory (`aad_token`) est prise en charge. |
| `defaults.deployment_name` | Nom du déploiement qui fera office de déploiement par défaut pour le point de terminaison. |

Pour créer un déploiement par lots, vous avez besoin de tous les éléments suivants :
* Des fichiers de modèle ou un modèle inscrit dans votre espace de travail référencé à l’aide de `azureml:<model-name>:<model-version>`. 
* Le code pour évaluer le modèle.
* L’environnement dans lequel le modèle s’exécute. Il peut s’agir d’une image Docker avec des dépendances Conda ou d’un environnement déjà inscrit dans votre espace de travail référencé à l’aide de `azureml:<environment-name>:<environment-version>`.
* Le calcul créé au préalable et référencé à l’aide de `azureml:<compute-name>` et des paramètres de ressource.

Pour plus d’informations sur la façon de référencer une entité Azure ML, consultez [Référencement d’une entité Azure ML](reference-yaml-core-syntax.md#referencing-an-azure-ml-entity).

L’exemple de référentiel contient tous les fichiers requis. Le fichier YAML suivant définit un déploiement par lots avec toutes les entrées et tous les paramètres facultatifs requis. Vous pouvez inclure ce fichier dans votre commande CLI pour [créer votre déploiement par lots](#create-a-batch-deployment). Dans le référentiel, ce fichier se trouve à l’emplacement `/cli/endpoints/batch/nonmlflow-deployment.yml`. 

:::code language="yaml" source="~/azureml-examples-main/cli/endpoints/batch/nonmlflow-deployment.yml":::

Le tableau suivant décrit les propriétés de clé du déploiement YAML. Pour le schéma YAML complet du déploiement par lots, consultez la page [Schéma YAML de déploiement par lots CLI (v2)](./reference-yaml-deployment-batch.md).

| Clé | Description |
| --- | ----------- |
| `$schema` | [Facultatif] Schéma YAML. Vous pouvez afficher le schéma dans l’exemple ci-dessus dans un navigateur pour afficher toutes les options disponibles pour un fichier YAML de déploiement de lot. |
| `name` | Le nom du déploiement. |
| `endpoint_name` | Nom du point de terminaison sous lequel créer le déploiement. |
| `model` | Modèle à utiliser pour le scoring par lots. L’exemple définit un modèle inclus à l’aide de `local_path`. Les fichiers de modèle seront automatiquement téléchargés et enregistrés avec un nom et une version générés automatiquement. Pour plus d’options, suivez le [Schéma de modèle](reference-yaml-model.md#yaml-syntax). En guise de meilleure pratique pour les scénarios de production, vous devez créer le modèle séparément et le référencer ici. Pour référencer un modèle existant, utilisez la syntaxe `azureml:<model-name>:<model-version>`. |
| `code_configuration.code.local_path` | Répertoire qui contient tout le code source Python de scoring du modèle. |
| `code_configuration.scoring_script` | Fichier Python dans le répertoire ci-dessus. Ce fichier doit avoir une fonction `init()` et une fonction `run()`. Utilisez la fonction `init()` pour toute préparation coûteuse ou courante (par exemple, charger le modèle en mémoire). `init()` est appelé une seule fois au début du processus. Utilisez `run(mini_batch)` pour effectuer le scoring de chaque entrée. La valeur de `mini_batch` est une liste de chemins de fichiers. La fonction `run()` doit retourner un dataframe pandas ou un tableau. Chaque élément retourné indique une exécution réussie d’un élément d’entrée dans le `mini_batch`. Assurez-vous que suffisamment de données sont incluses dans votre réponse `run()` pour mettre en corrélation l’entrée avec la sortie. |
| `environment` | L’environnement pour évaluer le modèle. L’exemple définit un environnement inline à l’aide de `conda_file` et `image`. Les dépendances `conda_file` seront installées au-dessus de `image`. L’environnement sera automatiquement inscrit avec un nom et une version générés automatiquement. Pour plus d’options, suivez le [schéma de l’environnement](reference-yaml-environment.md#yaml-syntax). En guise de meilleure pratique pour les scénarios de production, vous devez créer l’environnement séparément et le référencer ici. Pour référencer un environnement existant, utilisez la syntaxe `azureml:<environment-name>:<environment-version>`. |
| `compute` | Le calcul pour exécuter le scoring par lots. L’exemple utilise le `batch-cluster` créé au début et le référence à l’aide de la syntaxe `azureml:<compute-name>`. |
| `resources.instance_count` | Nombre d’instances à utiliser pour chaque travail de scoring par lots. |
| `max_concurrency_per_instance` | [Facultatif] Nombre maximal d’exécutions `scoring_script` parallèles par instance. |
| `mini_batch_size` | [Facultatif] Nombre de fichiers qu’un `scoring_script` peut traiter en un appel `run()`. |
| `output_action` | [Facultatif] Mode d’organisation de la sortie dans le fichier de sortie. `append_row` fusionne tous les résultats de sortie `run()` retournés dans un seul fichier nommé `output_file_name`. `summary_only` ne fusionne pas les résultats de sortie et calcule uniquement `error_threshold`. |
| `output_file_name` | [Facultatif] Nom du fichier de sortie de scoring par lots pour `append_row` `output_action`. |
| `retry_settings.max_retries` | [Facultatif] Nombre maximal de tentatives pour un échec d’un `scoring_script` `run()`. |
| `retry_settings.timeout` | [Facultatif] Délai d’attente, en secondes, pour un `scoring_script` `run()` de scoring d’un mini-lot. |
| `error_threshold` | [Facultatif] Nombre d’échecs de scoring de fichier d’entrée qui doivent être ignorés. Si le nombre d’erreurs présentes dans la totalité de l’entrée dépasse cette valeur, le travail de scoring par lots va se terminer. L’exemple utilise `-1`, qui indique que tout nombre d’échecs est autorisé sans terminer le travail de scoring par lots. | 
| `logging_level` | [Facultatif] Niveau de détail des journaux. Les valeurs permettant d’augmenter le niveau de détail sont : WARNING, INFO et DEBUG. |

###  <a name="understand-the-scoring-script"></a>Comprendre le script de scoring

Comme mentionné précédemment, `code_configuration.scoring_script` doit contenir deux fonctions :

- `init()` : Utilisez cette fonction pour toute préparation coûteuse ou courante. Par exemple, utilisez-la pour charger le modèle dans un objet global. Cette fonction est appelée une seule fois au début du processus.
-  `run(mini_batch)` : Cette fonction sera appelée pour chaque `mini_batch` et effectuera le scoring réel. 
    -  `mini_batch` : La valeur `mini_batch` est une liste de chemins de fichier.
    -  `response` : La méthode `run()` doit retourner un dataframe pandas ou un tableau. Chaque élément de sortie retourné indique une exécution réussie d’un élément d’entrée dans le `mini_batch`. Assurez-vous que suffisamment de données (par exemple, un identificateur de chaque élément d’entrée) sont inclues dans la réponse `run()` pour mettre en corrélation une entrée avec un résultat de sortie. 

L’exemple utilise `/cli/endpoints/batch/mnist/code/digit_identification.py`. Le modèle est chargé dans `init()` à partir de `AZUREML_MODEL_DIR`, qui est le chemin d’accès au dossier de modèle créé pendant le déploiement. `run(mini_batch)` itère chaque fichier dans `mini_batch`, effectue le scoring de modèle réel, puis retourne les résultats de sortie.

## <a name="deploy-with-batch-endpoints-and-run-batch-scoring"></a>Déployer avec des points de terminaison de lot et exécuter un scoring par lots

À présent, déployez le modèle avec des points de terminaison de lot et exécutez le scoring par lots.

### <a name="create-a-batch-endpoint"></a>Créer un point de terminaison de traitement de lots

La façon la plus simple de créer un point de terminaison de lot consiste à exécuter le code suivant en fournissant uniquement un `--name`.

:::code language="azurecli" source="~/azureml-examples-main/cli/batch-score.sh" ID="create_batch_endpoint" :::

Vous pouvez également créer un point de terminaison de lot à l’aide d’un fichier YAML. Ajoutez le paramètre `--file` dans la commande ci-dessus et spécifiez le chemin d’accès du fichier YAML.

### <a name="create-a-batch-deployment"></a>Créer un déploiement par lots

Exécutez le code suivant pour créer un déploiement par lots nommé `nonmlflowdp` sous le point de terminaison de lot et le définir comme déploiement par défaut. 

:::code language="azurecli" source="~/azureml-examples-main/cli/batch-score.sh" ID="create_batch_deployment_set_default" :::

> [!TIP]
> Le paramètre `--set-default` définit le déploiement nouvellement créé comme déploiement par défaut du point de terminaison. C’est un moyen pratique de créer un nouveau déploiement de point de terminaison par défaut, en particulier pour la première création d’un déploiement. En guise de meilleure pratique pour les scénarios de production, vous pouvez créer un nouveau déploiement sans le définir comme valeur par défaut, puis le vérifier et mettre à jour le déploiement par défaut ultérieurement. Pour plus d’informations, consultez la section [Déployer un nouveau modèle](#deploy-a-new-model).

### <a name="check-batch-endpoint-and-deployment-details"></a>Vérifier les détails du point de terminaison et du déploiement

Utilisez `show` pour vérifier les détails du point de terminaison et du déploiement.

Pour vérifier un déploiement par lots, exécutez le code suivant :

:::code language="azurecli" source="~/azureml-examples-main/cli/batch-score.sh" ID="check_batch_deployment_detail" :::

Pour vérifier un point de terminaison de lot, exécutez le code suivant. Lorsque le déploiement nouvellement créé est défini en tant que déploiement par défaut, vous devez voir `nonmlflowdp` dans `defaults.deployment_name` dans la réponse.

:::code language="azurecli" source="~/azureml-examples-main/cli/batch-score.sh" ID="check_batch_endpooint_detail" :::

### <a name="invoke-the-batch-endpoint-to-start-a-batch-scoring-job"></a>Appeler le point de terminaison de lot pour démarrer un travail de scoring par lot

Appeler un point de terminaison de lot déclenche un travail de scoring par lots. Un travail `name` est retourné à partir de la réponse à l’appel et peut être utilisé pour suivre la progression du scoring par lots. Le travail de scoring par lots s’exécute pendant un certain temps. Il fractionne les entrées entières en plusieurs `mini_batch` et les traite en parallèle sur le cluster de calcul. Un `scoring_scrip` `run()` prend un `mini_batch` et le traite avec un processus sur une instance. Les sorties du travail de scoring par lots sont stockées dans le stockage cloud, soit dans le stockage d’objets Blob par défaut de l’espace de travail, soit dans le stockage que vous avez spécifié.

#### <a name="invoke-the-batch-endpoint-with-different-input-options"></a>Appeler le point de terminaison de lot avec différentes options d’entrée

Vous pouvez utiliser l’interface CLI ou REST pour exécuter `invoke` sur le point de terminaison. Pour une expérience REST, consultez [Utiliser des points de terminaison de lot (préversion) avec REST](how-to-deploy-batch-with-rest.md)

Vous avez le choix entre trois options pour spécifier les entrées de données dans `invoke` de l’interface CLI.

* __Option 1 : Données dans le cloud__

    Utilisez `--input-path` pour spécifier un dossier (utilisez le préfixe `folder:`) ou un fichier (utilisez le préfixe `file:`) dans un magasin de données Azure Machine Learning inscrit. La syntaxe de l’URI de données est `folder:azureml://datastores/<datastore-name>/paths/<data-path>/` pour un dossier et `file:azureml://datastores/<datastore-name>/paths/<data-path>/<file-name>` pour un fichier spécifique. Pour plus d’informations sur l’URI de données, consultez [URI de référence de données Azure Machine Learning](reference-yaml-core-syntax.md#azure-ml-data-reference-uri).

    L’exemple utilise des données accessibles publiquement dans un dossier de `https://pipelinedata.blob.core.windows.net/sampledata/mnist`, qui contient des milliers de chiffres écrits manuellement. Le nom du travail de scoring par lots sera retourné à partir de la réponse de l’appel. Exécutez le code suivant pour appeler le point de terminaison de lot à l’aide de ces données. `--query name` est ajouté pour retourner uniquement le nom du travail à partir de la réponse d’appel, et il sera utilisé ultérieurement pour [Surveiller la progression de l’exécution du travail de scoring par lots](#monitor-batch-scoring-job-execution-progress) et [Vérifier les résultats du scoring par lots](#check-batch-scoring-results). Supprimez `--query name -o tsv` si vous souhaitez afficher la réponse complète de l’appel. Pour plus d’informations sur le paramètre `--query`, consultez [Interroger la sortie de commande Azure CLI](/cli/azure/query-azure-cli).

    :::code language="azurecli" source="~/azureml-examples-main/cli/batch-score.sh" ID="start_batch_scoring_job" :::

* __Option 2 : Jeu de données inscrit__

    Utilisez `--input-dataset` pour transmettre un jeu de données Azure Machine Learning inscrit. Pour créer un jeu de données, recherchez `az ml dataset create -h` pour l’instruction et suivez le [schéma du jeu de données](reference-yaml-dataset.md#yaml-syntax).

    > [!NOTE]
    > Un FileDataset créé à l’aide de la version précédente de l’interface CLI et du kit de développement logiciel (SDK) Python peut également être utilisé. TabularDataset n’est pas pris en charge. 

    ```azurecli
    az ml batch-endpoint invoke --name $ENDPOINT_NAME --input-dataset azureml:<dataset-name>:<dataset-version>
    ```

* __Option 3 : Données stockées localement__

    Utilisez `--input-local-path` pour passer des fichiers de données stockés localement. Les fichiers de données seront automatiquement téléchargés et enregistrés avec un nom et une version générés automatiquement.

    ```azurecli
    az ml batch-endpoint invoke --name $ENDPOINT_NAME --input-local-path <local-path>
    ```

#### <a name="configure-the-output-location-and-overwrite-settings"></a>Configurer l’emplacement de sortie et remplacer les paramètres

Les résultats de scoring par lots sont stockés par défaut dans le magasin d’objets blob par défaut de l’espace de travail, dans un dossier nommé par nom de travail (GUID généré par le système). Vous pouvez configurer l’emplacement où stocker les sorties de scoring lorsque vous appelez le point de terminaison de lot. Utilisez `--output-path` pour configurer tout `folder:` dans un magasin de données Azure Machine Learning inscrit. La syntaxe de `--output-path` `folder:` est identique à celle de `--input-path` `folder:`. Utilisez `--set output_file_name=<your-file-name>` pour configurer un nouveau nom de fichier de sortie si vous préférez avoir un fichier de sortie contenant tous les résultats de scoring (`output_action=append_row` spécifié dans votre YAML de déploiement).

> [!IMPORTANT]
> Vous devez utiliser un emplacement de sortie unique. Si le fichier de sortie existe, le travail de scoring par lots échouera. 

Certains paramètres peuvent être remplacés lorsqu’ils sont appelés pour tirer le meilleur parti des ressources de calcul et améliorer les performances : 

* Utilisez `--instance-count` pour remplacer `instance_count`. Par exemple, pour un plus grand volume d’entrées de données, vous souhaiterez peut-être utiliser plus d’instances pour accélérer le scoring par lots de bout en bout.
* Utilisez `--mini-batch-size` pour remplacer `mini_batch_size`. Le nombre de mini-lots est déterminé par le nombre total de fichiers d’entrée et mini_batch_size. Une plus petite mini_batch_size génère plus de mini-lots. Les mini-lots peuvent être exécutés en parallèle, mais il peut y avoir des surcharges de planification et d’invocation supplémentaires. 
* Utilisez `--set` pour remplacer d’autres paramètres, notamment `max_retries`, `timeout` et `error_threshold`. Ces paramètres peuvent avoir un impact sur la durée du scoring par lots de bout en bout pour différentes charges de travail.

Pour spécifier l’emplacement de sortie et remplacer les paramètres lors de l’appel, exécutez le code suivant. L’exemple stocke les sorties dans un dossier portant le même nom que le point de terminaison dans le stockage d’objets Blob par défaut de l’espace de travail, et utilise également un nom de fichier aléatoire pour garantir l’unicité de l’emplacement de sortie. Le code devrait fonctionner dans Unix. Remplacez par votre propre nom de fichier et de dossier unique.

:::code language="azurecli" source="~/azureml-examples-main/cli/batch-score.sh" ID="start_batch_scoring_job_configure_output_settings" :::

### <a name="monitor-batch-scoring-job-execution-progress"></a>Surveiller la progression de l’exécution du travail de scoring par lots

Les travaux de scoring par lots prennent généralement un certain temps pour traiter l’ensemble des entrées. 

Vous pouvez utiliser `job show` dans l’interface CLI pour afficher le travail. Exécutez le code suivant pour vérifier l’état du travail à partir de l’appel de point de terminaison précédent. Pour en savoir plus sur les commandes de travail, exécutez `az ml job -h`.

:::code language="azurecli" source="~/azureml-examples-main/cli/batch-score.sh" ID="check_job_status" :::

### <a name="check-batch-scoring-results"></a>Vérifier les résultats du scoring par lots

Suivez les étapes ci-dessous pour afficher les résultats du scoring dans l’Explorateur Stockage Azure lorsque le travail est terminé :

1. Exécutez le code suivant pour ouvrir le travail de scoring par lots dans Azure Machine Learning Studio. Le lien Studio du travail est également inclus dans la réponse de `invoke`, en tant que valeur de `interactionEndpoints.Studio.endpoint`.

    :::code language="azurecli" source="~/azureml-examples-main/cli/batch-score.sh" ID="show_job_in_studio" :::

1. Dans le graphique de l’exécution, sélectionnez l’étape `batchscoring`.
1. Sélectionnez l’onglet __Sorties + journaux__, puis sélectionnez **Afficher les sorties de données**.
1. À partir des __Sorties de données__, sélectionnez l’icône pour ouvrir l’__Explorateur Stockage__.

:::image type="content" source="media/how-to-use-batch-endpoint/view-data-outputs.png" alt-text="Capture d’écran de Studio montrant l’emplacement d’affichage des sorties de données" lightbox="media/how-to-use-batch-endpoint/view-data-outputs.png" :::

Les résultats du scoring dans l’Explorateur Stockage sont similaires à l’exemple de page suivant :

:::image type="content" source="media/how-to-use-batch-endpoint/scoring-view.png" alt-text="Capture d’écran de la sortie de scoring" lightbox="media/how-to-use-batch-endpoint/scoring-view.png":::

## <a name="deploy-a-new-model"></a>Déployer un nouveau modèle

Une fois que vous avez un point de terminaison de lot, vous pouvez continuer à affiner votre modèle et ajouter de nouveaux déploiements.

### <a name="create-a-new-batch-deployment-hosting-an-mlflow-model"></a>Créer un déploiement par lots hébergeant un modèle MLflow

Pour créer un déploiement par lots sous le point de terminaison de lot existant sans le définir comme déploiement par défaut, exécutez le code suivant :

:::code language="azurecli" source="~/azureml-examples-main/cli/batch-score.sh" ID="create_new_deployment_not_default" :::

Notez que `--set-default` n'est pas utilisé. Si vous réexécutez le point de terminaison de lot `show`, vous ne devriez voir aucun changement de `defaults.deployment_name`. 

L’exemple utilise un modèle (`/cli/endpoints/batch/autolog_nyc_taxi`) formé et suivi avec MLflow. `scoring_script` et `environment` peuvent être générés automatiquement à l’aide des métadonnées du modèle. Il n’est pas nécessaire de les spécifier dans le fichier YAML. Pour plus d’informations sur MLflow, consultez [Entraîner et suivre des modèles ML avec MLflow et Azure Machine Learning (préversion)](how-to-use-mlflow.md).

Voici le fichier YAML utilisé par l’exemple pour déployer un modèle MLflow, qui contient uniquement les propriétés requises minimales. Le fichier source dans le référentiel est `/cli/endpoints/batch/mlflow-deployment.yml`.

:::code language="yaml" source="~/azureml-examples-main/cli/endpoints/batch/mlflow-deployment.yml":::

> [!NOTE]
> La génération automatique de `scoring_script` et `environment` prend uniquement en charge la version du modèle de fonction Python et la signature de modèle basée sur les colonnes.

### <a name="test-a-non-default-batch-deployment"></a>Tester un déploiement par lots autre que celui par défaut

Pour tester le nouveau déploiement non défini par défaut, exécutez le code suivant. L’exemple utilise un modèle différent qui accepte un fichier CSV disponible publiquement à partir de `https://pipelinedata.blob.core.windows.net/sampledata/nytaxi/taxi-tip-data.csv`.

:::code language="azurecli" source="~/azureml-examples-main/cli/batch-score.sh" ID="test_new_deployment" :::

Notez que `--deployment-name` est utilisé pour spécifier le nouveau nom du déploiement. Ce paramètre vous permet d’effectuer `invoke` sur un déploiement autre que celui par défaut, et il ne met pas à jour le déploiement par défaut du point de terminaison de lot.

### <a name="update-the-default-batch-deployment"></a>Mettre à jour le déploiement par lots par défaut

Pour mettre à jour le déploiement par lots par défaut du point de terminaison, exécutez le code suivant :

:::code language="azurecli" source="~/azureml-examples-main/cli/batch-score.sh" ID="update_default_deployment" :::

Maintenant, si vous revenez au point de terminaison de lot `show`, vous devriez voir que `defaults.deployment_name` a la valeur `mlflowdp`. Vous pouvez exécuter `invoke` directement sur le point de terminaison de lot sans le paramètre `--deployment-name`.

### <a name="optional-update-the-deployment"></a>(Facultatif) Mettre à jour le déploiement

Si vous souhaitez mettre à jour le déploiement (par exemple, mettre à jour le code, le modèle, l’environnement ou les paramètres), mettez à jour le fichier YAML, puis exécutez `az ml batch-deployment update`. Vous pouvez également mettre à jour sans le fichier YAML à l’aide de `--set`. Pour plus d’informations, consultez `az ml batch-deployment update -h`.

## <a name="delete-the-batch-endpoint-and-the-deployment"></a>Supprimez le point de terminaison de lot et le déploiement

Si vous ne prévoyez pas d’utiliser l’ancien déploiement par lots, vous devez le supprimer en exécutant le code suivant. Utilisez `--yes` pour confirmer la suppression.

::: code language="azurecli" source="~/azureml-examples-main/cli/batch-score.sh" ID="delete_deployment" :::

Exécutez le code suivant pour supprimer le point de terminaison de lot et tous les déploiements sous-jacents. Les travaux de scoring par lots ne seront pas supprimés.

::: code language="azurecli" source="~/azureml-examples-main/cli/batch-score.sh" ID="delete_endpoint" :::

## <a name="next-steps"></a>Étapes suivantes

* [Points de terminaison de traintement de lots dans Studio](how-to-use-batch-endpoints-studio.md)
* [Déployer des modèles avec REST (préversion) pour le scoring par lot](how-to-deploy-batch-with-rest.md)
* [Résolution des problèmes de points de terminaison de traitement de lots](how-to-troubleshoot-batch-endpoints.md)
