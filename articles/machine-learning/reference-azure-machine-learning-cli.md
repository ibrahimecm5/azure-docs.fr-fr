---
title: Installer et utiliser l’interface de ligne de commande d’Azure Machine Learning
description: Découvrez comment utiliser l’extension Azure CLI pour Machine Learning qui permet de créer et gérer des ressources, telles que votre espace de travail, des banques de données, des jeux de données, des pipelines, des modèles et des déploiements.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.author: jordane
author: jpe316
ms.date: 04/02/2021
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 41f12d9421a49cb926a666469beda97cd8f53eac
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131559729"
---
# <a name="install--use-the-cli-extension-for-azure-machine-learning"></a>Installer et utiliser l’extension CLI pour Azure Machine Learning

[!INCLUDE [cli-version-info](../../includes/machine-learning-cli-version-1-only.md)]

L'interface CLI Azure Machine Learning est une extension pour l'[interface Azure](/cli/azure/), une interface de ligne de commande multiplateforme pour la plateforme Azure. Cette extension fournit les commandes à utiliser avec Azure Machine Learning. Il vous permet d’automatiser vos activités Machine Learning. La liste suivante fournit des exemples d’actions que vous pouvez effectuer avec l’extension CLI :

+ Exécuter des expérimentations pour créer des modèles Machine Learning

+ Inscrire des modèles Machine Learning pour l’usage des clients

+ Empaqueter, déployer et suivre le cycle de vie de vos modèles Machine Learning

L’interface CLI ne remplace en rien le kit de développement logiciel (SDK) Azure. Il s’agit d’un outil complémentaire optimisé pour gérer les tâches hautement paramétrables qui conviennent parfaitement à l’automatisation.

## <a name="prerequisites"></a>Prérequis

* Pour utiliser l'interface de ligne de commande, vous devez disposer d'un abonnement Azure. Si vous n’avez pas d’abonnement Azure, créez un compte gratuit avant de commencer. Essayez la [version gratuite ou payante d’Azure Machine Learning](https://azure.microsoft.com/free/) dès aujourd’hui.

* Pour utiliser les commandes CLI dans ce document à partir de votre **environnement local**, vous avez besoin [d’Azure CLI](/cli/azure/install-azure-cli).

    Si vous utilisez [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/), l’interface CLI est accessible via le navigateur et réside dans le cloud.

## <a name="full-reference-docs"></a>Documents de référence complets

Recherchez les [documents de référence complets concernant l’extension azure-cli-ml d’Azure CLI](/cli/azure/ml(v1)/).

## <a name="connect-the-cli-to-your-azure-subscription"></a>Connexion de la CLI à votre abonnement Azure

> [!IMPORTANT]
> Si vous utilisez Azure Cloud Shell, vous pouvez ignorer cette section. Le shell cloud vous authentifie automatiquement à l’aide du compte avec lequel vous vous connectez à votre abonnement Azure.

Il existe plusieurs façons de vous authentifier auprès de votre abonnement Azure à partir de l’interface CLI. La plus simple consiste à s’authentifier de manière interactive à l’aide d’un navigateur. Pour vous authentifier de manière interactive, ouvrez une ligne de commande ou un terminal et utilisez la commande suivante :

```azurecli-interactive
az login
```

Si l’interface CLI peut ouvrir votre navigateur par défaut, elle le fera et chargera une page de connexion par la même occasion. Dans le cas contraire, vous devez ouvrir un navigateur et suivre les instructions de la ligne de commande. Les instructions impliquent de naviguer vers [https://aka.ms/devicelogin](https://aka.ms/devicelogin) et d’entrer un code d’autorisation.

[!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)]

Pour les autres méthodes d’authentification, consultez [Se connecter avec Azure CLI](/cli/azure/authenticate-azure-cli).

## <a name="install-the-extension"></a>Installer l’extension

Pour installer l’extension CLI (v1) :
```azurecli-interactive
az extension add -n azure-cli-ml
```

## <a name="update-the-extension"></a>Mettre à jour de l’extension

Pour mettre à jour l’extension d’interface de ligne de commande Machine Learning, utilisez la commande suivante :

```azurecli-interactive
az extension update -n azure-cli-ml
```

## <a name="remove-the-extension"></a>Supprimer l’extension

Pour supprimer l'extension CLI, utilisez la commande suivante :

```azurecli-interactive
az extension remove -n azure-cli-ml
```

## <a name="resource-management"></a>Gestion des ressources

Les commandes suivantes montrent comment utiliser l'interface de ligne de commande pour gérer les ressources utilisées par Azure Machine Learning.

+ Créez un groupe de ressources si vous n’en avez pas déjà un :

    ```azurecli-interactive
    az group create -n myresourcegroup -l westus2
    ```

+ Créez un espace de travail Azure Machine Learning :

    ```azurecli-interactive
    az ml workspace create -w myworkspace -g myresourcegroup
    ```

    Pour plus d’informations, consultez [az ml workspace create](/cli/azure/ml/workspace#az_ml_workspace_create).

+ Joignez la configuration d’un espace de travail à un dossier pour activer la prise en compte du contexte CLI.

    ```azurecli-interactive
    az ml folder attach -w myworkspace -g myresourcegroup
    ```

    Cette commande crée un sous-répertoire `.azureml` qui contient des exemples de fichiers d’environnement runconfig et conda. Il contient également un fichier `config.json` utilisé pour communiquer avec votre espace de travail Azure Machine Learning.

    Pour plus d’informations, consultez [az ml folder attach](/cli/azure/ml(v1)/folder#az_ml_folder_attach).

+ Joignez un conteneur d’objets blob Azure en tant que magasin de données.

    ```azurecli-interactive
    az ml datastore attach-blob  -n datastorename -a accountname -c containername
    ```

    Pour plus d’informations, consultez [az ml datastore attach-blob](/cli/azure/ml/datastore#az_ml_datastore_attach-blob).

+ Chargez les fichiers dans un magasin de données.

    ```azurecli-interactive
    az ml datastore upload  -n datastorename -p sourcepath
    ```

    Pour plus d’informations, voir [az ml datastore upload](/cli/azure/ml/datastore#az_ml_datastore_upload).

+ Joignez un cluster AKS en tant que cible de calcul.

    ```azurecli-interactive
    az ml computetarget attach aks -n myaks -i myaksresourceid -g myresourcegroup -w myworkspace
    ```

    Pour plus d’informations, consultez [az ml computetarget attach aks](/cli/azure/ml(v1)/computetarget/attach#az_ml_computetarget_attach-aks).

### <a name="compute-clusters"></a>Clusters de calcul

+ Créez un cluster de calcul géré.

    ```azurecli-interactive
    az ml computetarget create amlcompute -n cpu --min-nodes 1 --max-nodes 1 -s STANDARD_D3_V2
    ```



+ Créer un cluster de calcul géré avec une identité managée

  + Identité managée affectée par l’utilisateur

    ```azurecli
    az ml computetarget create amlcompute --name cpu-cluster --vm-size Standard_NC6 --max-nodes 5 --assign-identity '/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'
    ```

  + Identité managée affectée par le système

    ```azurecli
    az ml computetarget create amlcompute --name cpu-cluster --vm-size Standard_NC6 --max-nodes 5 --assign-identity '[system]'
    ```
+ Ajouter une identité managée à un cluster existant :

    + Identité managée affectée par l’utilisateur
        ```azurecli
        az ml computetarget amlcompute identity assign --name cpu-cluster '/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'
        ```
    + Identité managée affectée par le système

        ```azurecli
        az ml computetarget amlcompute identity assign --name cpu-cluster '[system]'
        ```

Pour plus d’informations, consultez [aaz ml computetarget create amlcompute](/cli/azure/ml(v1)/computetarget/create#az_ml_computetarget_create_amlcompute).

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-note.md)]

<a id="computeinstance"></a>

### <a name="compute-instance"></a>Instance de calcul
 Gérer des instances de calcul.  Dans tous les exemples ci-dessous, le nom de l’instance de calcul est **cpu**

+ Créer un nouveau computeinstance.

    ```azurecli-interactive
    az ml computetarget create computeinstance -n cpu -s "STANDARD_D3_V2" -v
    ```

    Pour plus d’informations, consultez [az ml computetarget create computeinstance](/cli/azure/ml(v1)/computetarget/create#az_ml_computetarget_create_computeinstance).

+ Arrêter un computeinstance.

    ```azurecli-interactive
    az ml computetarget computeinstance stop -n cpu -v
    ```

    Pour plus d’informations, consultez [az ml computetarget computeinstance stop](/cli/azure/ml(v1)/computetarget/computeinstance#az_ml_computetarget_computeinstance_stop).

+ Démarrer un computeinstance.

    ```azurecli-interactive
    az ml computetarget computeinstance start -n cpu -v
    ```

    Pour plus d’informations, consultez [az ml computetarget computeinstance start](/cli/azure/ml(v1)/computetarget/computeinstance#az_ml_computetarget_computeinstance_start).

+ Redémarrer un computeinstance.

    ```azurecli-interactive
    az ml computetarget computeinstance restart -n cpu -v
    ```

    Pour plus d’informations, consultez [az ml computetarget computeinstance restart](/cli/azure/ml(v1)/computetarget/computeinstance#az_ml_computetarget_computeinstance_restart).

+ Supprimer un computeinstance.

    ```azurecli-interactive
    az ml computetarget delete -n cpu -v
    ```

    Pour plus d’informations, consultez [az ml computetarget delete computeinstance](/cli/azure/ml(v1)/computetarget#az_ml_computetarget_delete).


## <a name="run-experiments"></a><a id="experiments"></a>Exécuter des expériences

* Exécutez votre expérimentation. Lorsque vous utilisez cette commande, spécifiez le nom du fichier runconfig (le texte avant \*.runconfig si vous regardez le système de fichiers) avec le paramètre - c.

    ```azurecli-interactive
    az ml run submit-script -c sklearn -e testexperiment train.py
    ```

    > [!TIP]
    > La commande `az ml folder attach` crée un sous-répertoire `.azureml` qui contient deux exemples de fichiers runconfig. 
    >
    > Si vous disposez d’un script Python qui crée un objet de configuration de série de tests par programmation, vous pouvez utiliser [RunConfig.save()](/python/api/azureml-core/azureml.core.runconfiguration#save-path-none--name-none--separate-environment-yaml-false-) pour l’enregistrer dans un fichier runconfig.
    >
    > Le schéma runconfig complet se trouve dans ce [fichier JSON](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json). Le schéma s’auto-documente à l’aide de la clé `description` de chaque objet. En outre, il existe des énumérations pour les valeurs possibles et un extrait de modèle à la fin.

    Pour plus d’informations, consultez [az ml run submit-script](/cli/azure/ml(v1)/run#az_ml_run_submit_script).

* Afficher une liste des expériences :

    ```azurecli-interactive
    az ml experiment list
    ```

    Pour plus d’informations, consultez [az ml experiment list](/cli/azure/ml(v1)/experiment#az_ml_experiment_list).

### <a name="hyperdrive-run"></a>Série de tests HyperDrive

Vous pouvez utiliser HyperDrive avec Azure CLI pour effectuer des séries de tests pour le réglage des paramètres. Commencez par créer un fichier de configuration HyperDrive au format suivant. Pour plus d’informations sur les paramètres de réglage des hyperparamètres, voir l’article [Optimiser les hyperparamètres pour votre modèle](how-to-tune-hyperparameters.md).

```yml
# hdconfig.yml
sampling: 
    type: random # Supported options: Random, Grid, Bayesian
    parameter_space: # specify a name|expression|values tuple for each parameter.
    - name: --penalty # The name of a script parameter to generate values for.
      expression: choice # supported options: choice, randint, uniform, quniform, loguniform, qloguniform, normal, qnormal, lognormal, qlognormal
      values: [0.5, 1, 1.5] # The list of values, the number of values is dependent on the expression specified.
policy: 
    type: BanditPolicy # Supported options: BanditPolicy, MedianStoppingPolicy, TruncationSelectionPolicy, NoTerminationPolicy
    evaluation_interval: 1 # Policy properties are policy specific. See the above link for policy specific parameter details.
    slack_factor: 0.2
primary_metric_name: Accuracy # The metric used when evaluating the policy
primary_metric_goal: Maximize # Maximize|Minimize
max_total_runs: 8 # The maximum number of runs to generate
max_concurrent_runs: 2 # The number of runs that can run concurrently.
max_duration_minutes: 100 # The maximum length of time to run the experiment before cancelling.
```

Ajoutez ce fichier aux fichiers de configuration de série de tests. Ensuite, soumettez une série de tests HyperDrive en utilisant :
```azurecli
az ml run submit-hyperdrive -e <experiment> -c <runconfig> --hyperdrive-configuration-name <hdconfig> my_train.py
```

Notez la section *arguments* dans le fichier runconfig et la section *parameter space* dans le fichier config HyperDrive. Elles contiennent les arguments de ligne de commande à passer au script d’apprentissage. La valeur dans le fichier runconfig reste la même pour chaque itération, tandis que la plage dans le fichier config HyperDrive fait l’objet d’une itération. Ne spécifiez pas le même argument dans les deux fichiers.

## <a name="dataset-management"></a>Gestion des jeux de données

Les commandes suivantes montrent comment utiliser des jeux de données dans Azure Machine Learning :

+ Inscrire un jeu de données :

    ```azurecli-interactive
    az ml dataset register -f mydataset.json
    ```

    Pour obtenir des informations sur le format du fichier JSON utilisé pour définir le jeu de données, exécutez la commande `az ml dataset register --show-template`.

    Pour plus d’informations, consultez [az ml dataset register](/cli/azure/ml(v1)/dataset#az_ml_dataset_register).

+ Lister tous les jeux de données dans un espace de travail :

    ```azurecli-interactive
    az ml dataset list
    ```

    Pour plus d’informations, consultez [az ml dataset list](/cli/azure/ml(v1)/dataset#az_ml_dataset_list).

+ Obtenir les détails d’un jeu de données :

    ```azurecli-interactive
    az ml dataset show -n dataset-name
    ```

    Pour plus d’informations, consultez [az ml dataset show](/cli/azure/ml(v1)/dataset#az_ml_dataset_show).

+ Désinscrire un jeu de données :

    ```azurecli-interactive
    az ml dataset unregister -n dataset-name
    ```

    Pour plus d’informations, consultez [az ml dataset unregister](/cli/azure/ml(v1)/dataset#az_ml_dataset_archive).

## <a name="environment-management"></a>Gestion de l’environnement

Les commandes suivantes montrent comment créer, inscrire et lister des [environnements](how-to-configure-environment.md) Azure Machine Learning pour votre espace de travail :

+ Créer des fichiers de génération de modèles automatique pour un environnement :

    ```azurecli-interactive
    az ml environment scaffold -n myenv -d myenvdirectory
    ```

    Pour plus d’informations, voir [az ml environment scaffold](/cli/azure/ml/environment#az_ml_environment_scaffold).

+ Inscrivez un environnement :

    ```azurecli-interactive
    az ml environment register -d myenvdirectory
    ```

    Pour plus d’informations, voir [az ml environment register](/cli/azure/ml/environment#az_ml_environment_register).

+ Répertoriez les environnements inscrits :

    ```azurecli-interactive
    az ml environment list
    ```

    Pour plus d’informations, voir [az ml environment list](/cli/azure/ml/environment#az_ml_environment_list).

+ Téléchargez un environnement inscrit :

    ```azurecli-interactive
    az ml environment download -n myenv -d downloaddirectory
    ```

    Pour en savoir plus, voir [az ml environment download](/cli/azure/ml/environment#az_ml_environment_download).

### <a name="environment-configuration-schema"></a>Schéma de configuration de l’environnement

Si vous avez utilisé la commande `az ml environment scaffold`, elle génère un fichier `azureml_environment.json` de modèle qui peut être modifié et utilisé pour créer des configurations d’environnement personnalisées avec l’interface CLI. L’objet de niveau supérieur est mappé librement à la classe [`Environment`](/python/api/azureml-core/azureml.core.environment%28class%29) dans le SDK Python. 

```json
{
    "name": "testenv",
    "version": null,
    "environmentVariables": {
        "EXAMPLE_ENV_VAR": "EXAMPLE_VALUE"
    },
    "python": {
        "userManagedDependencies": false,
        "interpreterPath": "python",
        "condaDependenciesFile": null,
        "baseCondaEnvironment": null
    },
    "docker": {
        "enabled": false,
        "baseImage": "mcr.microsoft.com/azureml/openmpi3.1.2-ubuntu18.04:20210615.v1",
        "baseDockerfile": null,
        "sharedVolumes": true,
        "shmSize": "2g",
        "arguments": [],
        "baseImageRegistry": {
            "address": null,
            "username": null,
            "password": null
        }
    },
    "spark": {
        "repositories": [],
        "packages": [],
        "precachePackages": true
    },
    "databricks": {
        "mavenLibraries": [],
        "pypiLibraries": [],
        "rcranLibraries": [],
        "jarLibraries": [],
        "eggLibraries": []
    },
    "inferencingStackVersion": null
}
```

Le tableau suivant détaille chaque champ de niveau supérieur dans le fichier JSON, son type et sa description. Si un type d’objet est lié à une classe du SDK Python, il existe une correspondance 1:1 libre entre chaque champ JSON et le nom de la variable publique dans la classe Python. Dans certains cas, le champ peut être mappé à un argument de constructeur plutôt qu’à une variable de classe. Par exemple, le champ `environmentVariables` est mappé à la variable `environment_variables` dans la classe [`Environment`](/python/api/azureml-core/azureml.core.environment%28class%29).

| Champ JSON | Type | Description |
|---|---|---|
| `name` | `string` | Nom de l’environnement. Ne commencez pas le nom avec **Microsoft** ou **AzureML**. |
| `version` | `string` | Version de l’environnement. |
| `environmentVariables` | `{string: string}` | Table de hachage des noms et valeurs de variables d’environnement. |
| `python` | [`PythonSection`](/python/api/azureml-core/azureml.core.environment.pythonsection)qui définit l’environnement et l’interpréteur Python à utiliser sur la ressource de calcul cible. |
| `docker` | [`DockerSection`](/python/api/azureml-core/azureml.core.environment.dockersection) | Définit des paramètres permettant de personnaliser l’image Docker construite pour les spécifications de l’environnement. |
| `spark` | [`SparkSection`](/python/api/azureml-core/azureml.core.environment.sparksection) | La section configure les paramètres Spark. Elle est utilisée uniquement quand le framework a la valeur PySpark. |
| `databricks` | [`DatabricksSection`](/python/api/azureml-core/azureml.core.databricks.databrickssection) | Configure les dépendances de bibliothèque Databricks. |
| `inferencingStackVersion` | `string` | Spécifie la version de pile d’inférence ajoutée à l’image. Pour éviter d’ajouter une pile d’inférence, laissez la valeur `null` dans ce champ. Valeur valide : « latest ». |

## <a name="ml-pipeline-management"></a>Gestion des pipelines ML

Les commandes suivantes montrent comment travailler avec des pipelines d’apprentissage automatique :

+ Créez un pipeline d’apprentissage automatique :

    ```azurecli-interactive
    az ml pipeline create -n mypipeline -y mypipeline.yml
    ```

    Pour plus d’informations, consultez [az ml pipeline create](/cli/azure/ml(v1)/pipeline#az_ml_pipeline_create).

    Pour plus d’informations sur le fichier YAML du pipeline, consultez [Define machine learning pipelines in YAML (Définir des pipelines d’apprentissage automatique YAML)](reference-pipeline-yaml.md).

+ Exécutez un pipeline :

    ```azurecli-interactive
    az ml run submit-pipeline -n myexperiment -y mypipeline.yml
    ```

    Pour plus d’informations, consultez [az ml run submit-pipeline](/cli/azure/ml(v1)/run#az_ml_run_submit_pipeline).

    Pour plus d’informations sur le fichier YAML du pipeline, consultez [Define machine learning pipelines in YAML (Définir des pipelines d’apprentissage automatique YAML)](reference-pipeline-yaml.md).

+ Planifiez un pipeline :

    ```azurecli-interactive
    az ml pipeline create-schedule -n myschedule -e myexperiment -i mypipelineid -y myschedule.yml
    ```

    Pour plus d’informations, consultez [az ml pipeline create-schedule](/cli/azure/ml(v1)/pipeline#az_ml_pipeline_create-schedule).

    Pour plus d’informations sur le fichier YAML de planification du pipeline, consultez [Define machine learning pipelines in YAML (Définir des pipelines d’apprentissage automatique YAML)](reference-pipeline-yaml.md#schedules).

## <a name="model-registration-profiling-deployment"></a>Inscription, profilage, déploiement du modèle

Les commandes suivantes montrent comment inscrire un modèle entraîné, puis le déployer en tant que service de production :

+ Inscrivez un modèle dans Azure Machine Learning :

    ```azurecli-interactive
    az ml model register -n mymodel -p sklearn_regression_model.pkl
    ```

    Pour plus d’informations, consultez [az ml model register](/cli/azure/ml/model#az_ml_model_register).

+ **FACULTATIF** Profilez votre modèle pour obtenir des valeurs de processeur et de mémoire optimales pour le déploiement.
    ```azurecli-interactive
    az ml model profile -n myprofile -m mymodel:1 --ic inferenceconfig.json -d "{\"data\": [[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]]}" -t myprofileresult.json
    ```

    Pour plus d’informations, consultez [az ml model profile](/cli/azure/ml/model#az_ml_model_profile).

+ Déployer votre modèle sur AKS
    ```azurecli-interactive
    az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json --ct akscomputetarget
    ```
    
    Pour plus d’informations sur le schéma du fichier de configuration d'inférence, consultez [Schéma de configuration de l’inférence](#inferenceconfig).
    
    Pour plus d’informations sur le schéma du fichier de configuration de déploiement, consultez [Schéma de configuration de déploiement](#deploymentconfig).

    Pour plus d’informations, consultez [az ml model deploy](/cli/azure/ml/model#az_ml_model_deploy).

<a id="inferenceconfig"></a>

## <a name="inference-configuration-schema"></a>Schéma de configuration de l’inférence

[!INCLUDE [inferenceconfig](../../includes/machine-learning-service-inference-config.md)]

<a id="deploymentconfig"></a>

## <a name="deployment-configuration-schema"></a>Schéma de configuration de déploiement

### <a name="local-deployment-configuration-schema"></a>Schéma de configuration de déploiement local

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-local-deploy-config.md)]

### <a name="azure-container-instance-deployment-configuration-schema"></a>Schéma de configuration de déploiement d’Azure Container Instance 

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aci-deploy-config.md)]

### <a name="azure-kubernetes-service-deployment-configuration-schema"></a>Schéma de configuration de déploiement d’Azure Kubernetes Service

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aks-deploy-config.md)]

## <a name="next-steps"></a>Étapes suivantes

* [Informations de référence sur les commandes de l’extension CLI Machine Learning](/cli/azure/ml).

* [Former et déployer des modèles Machine Learning à l’aide d’Azure Pipelines](/azure/devops/pipelines/targets/azure-machine-learning)
