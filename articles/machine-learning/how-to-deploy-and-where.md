---
title: Déployer des modèles Machine Learning
titleSuffix: Azure Machine Learning
description: Découvrez comment et où déployer des modèles Machine Learning. Déployez sur Azure Container Instances, Azure Kubernetes Service et FPGA.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.date: 04/21/2021
ms.topic: how-to
ms.custom: devx-track-python, deploy, devx-track-azurecli, contperf-fy21q2, contperf-fy21q4, mktng-kw-nov2021
adobe-target: true
ms.openlocfilehash: 78abc8d3dba3090198f8856d71f91a9087237641
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131853317"
---
# <a name="deploy-machine-learning-models-to-azure"></a>Déployer des modèles Machine Learning sur Azure 

Découvrez comment déployer votre modèle Machine Learning ou Deep Learning en tant que service web dans le cloud Azure.

> [!TIP]
> Les points de terminaison en ligne managés (préversion) offrent un moyen de déployer votre modèle entraîné sans avoir à créer et gérer l’infrastructure sous-jacente. Pour plus d’informations, consultez [Déployer et scorer un modèle Machine Learning avec un point de terminaison en ligne managé (préversion)](how-to-deploy-managed-online-endpoints.md).

## <a name="workflow-for-deploying-a-model"></a>Flux de travail pour le déploiement d’un modèle

Le workflow est le même, quel que soit l’endroit où vous déployez votre modèle :

1. Inscrire le modèle.
1. Préparer un script d’entrée.
1. Préparer une configuration d’inférence.
1. Déployer le modèle localement pour vous assurer que tout fonctionne.
1. Choisir une cible de calcul.
1. Redéployer le modèle dans le cloud.
1. Tester le service web qui en résulte.

Pour plus d’informations sur les concepts impliqués dans le workflow du déploiement Machine Learning, consultez [Déployer, gérer et superviser des modèles avec Azure Machine Learning](concept-model-management-and-deployment.md).

[!INCLUDE [endpoints-option](../../includes/machine-learning-endpoints-preview-note.md)]

## <a name="prerequisites"></a>Prérequis

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

- Un espace de travail Azure Machine Learning. Pour plus d’informations, voir la page [Créer un espace de travail Azure Machine Learning](how-to-manage-workspace.md).
- Un modèle Si vous n’avez pas de modèle entraîné, vous pouvez utiliser les fichiers de modèle et de dépendance fournis dans [ce tutoriel](https://aka.ms/azml-deploy-cloud).
- L’[extension de l’interface de ligne de commande (CLI) Azure pour le service Machine Learning](reference-azure-machine-learning-cli.md).
- Une machine qui peut exécuter Docker, comme une [instance de calcul](how-to-create-manage-compute-instance.md).

# <a name="python"></a>[Python](#tab/python)

- Un espace de travail Azure Machine Learning. Pour plus d’informations, voir la page [Créer un espace de travail Azure Machine Learning](how-to-manage-workspace.md).
- Un modèle Si vous n’avez pas de modèle entraîné, vous pouvez utiliser les fichiers de modèle et de dépendance fournis dans [ce tutoriel](https://aka.ms/azml-deploy-cloud).
- Le [Kit de développement logiciel (SDK) Azure Machine Learning pour Python](/python/api/overview/azure/ml/intro).
- Une machine qui peut exécuter Docker, comme une [instance de calcul](how-to-create-manage-compute-instance.md).
---

## <a name="connect-to-your-workspace"></a>Se connecter à un espace de travail

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

Pratiques conseillées

```azurecli-interactive
az login
az account set -s <my subscription>
az ml workspace list --resource-group=<my resource group>
```

pour afficher les espaces de travail auxquels vous avez accès.

# <a name="python"></a>[Python](#tab/python)

```python
from azureml.core import Workspace
ws = Workspace(subscription_id="<subscription_id>",
               resource_group="<resource_group>",
               workspace_name="<workspace_name>")
```

Pour plus d’informations sur l’utilisation du kit SDK pour se connecter à un espace de travail, consultez la documentation sur le [kit SDK Azure Machine Learning pour Python](/python/api/overview/azure/ml/intro#workspace).


---

## <a name="register-the-model"></a><a id="registermodel"></a> Inscrire le modèle

Une situation courante pour un service Machine Learning déployé est que vous avez besoin des composants suivants :
    
 + les ressources représentant le modèle spécifique que vous souhaitez déployer (par exemple un fichier de modèle pytorch)
 + Le code que vous allez exécuter dans le service, qui exécute le modèle sur une entrée donnée

Azure Machine Learning vous permet de séparer le déploiement en deux composants distincts, afin que vous puissiez conserver le même code et simplement mettre à jour le modèle. Nous définissons le mécanisme par lequel vous chargez un modèle _séparément_ de votre code comme « inscription du modèle ».

Lorsque vous inscrivez un modèle, nous chargeons le modèle dans le cloud (dans le compte de stockage par défaut de votre espace de travail), puis nous le montons dans le même calcul que celui où est exécuté votre service web.

Les exemples suivants montrent comment inscrire un modèle.

[!INCLUDE [trusted models](../../includes/machine-learning-service-trusted-model.md)]

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

### <a name="register-a-model-from-a-local-file"></a>Inscrire un modèle à partir d’un fichier local

[!notebook-python[] (~/azureml-examples-main/python-sdk/tutorials/deploy-local/2.deploy-local-cli.ipynb?name=register-model-from-local-file-code)]

Définissez `-p` sur le chemin d’accès d’un dossier ou d’un fichier que vous souhaitez inscrire.

Pour plus d’informations sur `az ml model register`, consultez la [documentation de référence](/cli/azure/ext/azure-cli-ml/ml/model).

### <a name="register-a-model-from-an-azure-ml-training-run"></a>Inscrire un modèle à partir d’une exécution de formation Azure ML

```azurecli-interactive
az ml model register -n bidaf_onnx --asset-path outputs/model.onnx --experiment-name myexperiment --run-id myrunid --tag area=qna
```

[!INCLUDE [install extension](../../includes/machine-learning-service-install-extension.md)]

Le paramètre `--asset-path` fait référence à l’emplacement cloud du modèle. Dans cet exemple, le chemin d’un fichier unique est utilisé. Pour inclure plusieurs fichiers dans l’inscription du modèle, définissez `--asset-path` avec le chemin d’un dossier contenant les fichiers.

Pour plus d’informations sur `az ml model register`, consultez la [documentation de référence](/cli/azure/ml/model).

# <a name="python"></a>[Python](#tab/python)

### <a name="register-a-model-from-a-local-file"></a>Inscrire un modèle à partir d’un fichier local

Vous pouvez inscrire un modèle en indiquant son chemin local. Vous pouvez fournir le chemin d’un dossier ou d’un seul fichier sur votre machine locale.
<!-- pyhton nb call -->
[!notebook-python[] (~/azureml-examples-main/python-sdk/tutorials/deploy-local/1.deploy-local.ipynb?name=register-model-from-local-file-code)]


Pour inclure plusieurs fichiers dans l’inscription du modèle, définissez `model_path` avec le chemin d’un dossier contenant les fichiers.

Pour plus d’informations, consultez la documentation sur la [classe Model](/python/api/azureml-core/azureml.core.model.model).


### <a name="register-a-model-from-an-azure-ml-training-run"></a>Inscrire un modèle à partir d’une exécution de formation Azure ML

  Quand vous utilisez le kit SDK pour entraîner un modèle, vous pouvez recevoir un objet [Run](/python/api/azureml-core/azureml.core.run.run) ou [AutoMLRun](/python/api/azureml-train-automl-client/azureml.train.automl.run.automlrun) en fonction de la façon dont vous avez entraîné le modèle. Chaque objet peut être utilisé pour inscrire un modèle créé par un exécution d’expérimentation.

  + Inscrire un modèle à partir d’un objet `azureml.core.Run` :
 
    ```python
    model = run.register_model(model_name='bidaf_onnx',
                               tags={'area': 'qna'},
                               model_path='outputs/model.onnx')
    print(model.name, model.id, model.version, sep='\t')
    ```

    Le paramètre `model_path` fait référence à l’emplacement cloud du modèle. Dans cet exemple, le chemin d’un fichier unique est utilisé. Pour inclure plusieurs fichiers dans l’inscription du modèle, définissez `model_path` avec le chemin d’un dossier contenant les fichiers. Pour plus d’informations, consultez la documentation sur [Run.register_model](/python/api/azureml-core/azureml.core.run.run#register-model-model-name--model-path-none--tags-none--properties-none--model-framework-none--model-framework-version-none--description-none--datasets-none--sample-input-dataset-none--sample-output-dataset-none--resource-configuration-none----kwargs-).

  + Inscrire un modèle à partir d’un objet `azureml.train.automl.run.AutoMLRun` :

    ```python
        description = 'My AutoML Model'
        model = run.register_model(description = description,
                                   tags={'area': 'qna'})

        print(run.model_id)
    ```

    Dans cet exemple, les paramètres `metric` et `iteration` n’étant pas spécifiés, l’itération avec la meilleure métrique principale est inscrite. La valeur `model_id` retournée par l’exécution est utilisée à la place d’un nom de modèle.

    Pour plus d’informations, consultez la documentation sur [AutoMLRun.register_model](/python/api/azureml-train-automl-client/azureml.train.automl.run.automlrun#register-model-model-name-none--description-none--tags-none--iteration-none--metric-none-).

    Pour déployer un modèle inscrit à partir d’un `AutoMLRun`, nous vous recommandons de le faire via le [bouton de déploiement en un clic dans Azure Machine Learning Studio](how-to-use-automated-ml-for-ml-models.md#deploy-your-model). 

---

## <a name="define-a-dummy-entry-script"></a>Définir un script d’entrée factice

[!INCLUDE [write entry script](../../includes/machine-learning-dummy-entry-script.md)]


## <a name="define-an-inference-configuration"></a>Définir une configuration d’inférence

Une configuration d’inférence décrit le conteneur et les fichiers Docker à utiliser lors de l’initialisation de votre service web. Tous les fichiers de votre répertoire source, y compris les sous-répertoires, seront compressés et chargés dans le cloud lorsque vous déploierez votre service web.

La configuration d’inférence ci-dessous spécifie que le déploiement de Machine Learning utilisera le fichier `echo_score.py` dans le répertoire `./source_dir` pour traiter les demandes entrantes, et utilisera l’image Docker avec les packages Python spécifiés dans l’environnement `project_environment`.

Vous pouvez utiliser n’importe quel [environnement d’inférence Azure Machine Learning organisé](concept-prebuilt-docker-images-inference.md#list-of-prebuilt-docker-images-for-inference) comme image Docker de base lors de la création de votre environnement de projet. Nous allons aussi installer les dépendances requises et stocker l’image Docker qui en résulte dans le référentiel associé à votre espace de travail.

> [!NOTE]
> Le chargement du [répertoire source de l’inférence](/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py#constructor&preserve-view=true) de l’apprentissage automatique Azure ne respecte pas **.gitignore** ni **.amlignore**.

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

Voici une configuration d’inférence minimale possible :

:::code language="json" source="~/azureml-examples-main/python-sdk/tutorials/deploy-local/dummyinferenceconfig.json":::

Enregistrez le fichier sous le nom `dummyinferenceconfig.json`.


Pour une discussion plus approfondie sur les configurations d’inférence, [consultez cet article](./reference-azure-machine-learning-cli.md#inference-configuration-schema). 

# <a name="python"></a>[Python](#tab/python)

L’exemple suivant montre comment créer un environnement minimal sans dépendances PIP, en utilisant le script de scoring factice que vous avez défini ci-dessus.

[!notebook-python[] (~/azureml-examples-main/python-sdk/tutorials/deploy-local/1.deploy-local.ipynb?name=inference-configuration-code)]

Pour plus d’informations sur les environnements , consultez [Créer et gérer des environnements pour la formation et le déploiement](how-to-use-environments.md).

Pour plus d’informations sur la configuration des inférences, consultez la documentation sur la classe [InferenceConfig](/python/api/azureml-core/azureml.core.model.inferenceconfig).

---


## <a name="define-a-deployment-configuration"></a>Définir une configuration de déploiement

Une configuration de déploiement spécifie la quantité de mémoire et de cœurs à réserver pour votre service web, qui sera nécessaire pour l’exécution, ainsi que les détails de configuration du service web sous-jacent. Par exemple, une configuration de déploiement vous permet de spécifier que votre service nécessite 2 gigaoctets de mémoire, 2 cœurs d’UC, 1 cœur GPU et que vous souhaitez activer la mise à l’échelle automatique.

Les options disponibles pour une configuration de déploiement varient en fonction de la cible de calcul que vous choisissez. Dans un déploiement local, tout ce que vous pouvez spécifier est le port sur lequel votre service web sera traité.

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

[!INCLUDE [aml-local-deploy-config](../../includes/machine-learning-service-local-deploy-config.md)]

Pour plus d’informations, consultez [cette référence](./reference-azure-machine-learning-cli.md#deployment-configuration-schema).

# <a name="python"></a>[Python](#tab/python)

Pour créer une configuration de déploiement local, procédez comme suit :

[!notebook-python[] (~/azureml-examples-main/python-sdk/tutorials/deploy-local/1.deploy-local.ipynb?name=deployment-configuration-code)]

---

## <a name="deploy-your-machine-learning-model"></a>Déployer votre modèle Machine Learning

Vous êtes maintenant prêt à déployer votre modèle. 

[!INCLUDE [aml-deploy-service](../../includes/machine-learning-deploy-service.md)]


## <a name="call-into-your-model"></a>Appeler votre modèle

Vérifions que votre modèle d’écho a été correctement déployé. Vous deviez être en mesure d’effectuer une requête d’activité simple, ainsi qu’une requête de scoring :

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)
<!-- cli nb call -->

[!notebook-python[] (~/azureml-examples-main/python-sdk/tutorials/deploy-local/2.deploy-local-cli.ipynb?name=call-into-model-code)]

# <a name="python"></a>[Python](#tab/python)
<!-- python nb call -->
[!notebook-python[] (~/azureml-examples-main/python-sdk/tutorials/deploy-local/1.deploy-local.ipynb?name=call-into-model-code)]

---

## <a name="define-an-entry-script"></a>Définir un script d’entrée

Il est maintenant temps de charger votre modèle. Tout d’abord, modifiez votre script d’entrée :


:::code language="python" source="~/azureml-examples-main/python-sdk/tutorials/deploy-local/source_dir/score.py":::

Enregistrez ce fichier en tant que `score.py` à l’intérieur de `source_dir`.

Notez l’utilisation de la variable d’environnement `AZUREML_MODEL_DIR` pour rechercher votre modèle inscrit. Vous avez maintenant ajouté des packages pip.

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

:::code language="json" source="~/azureml-examples-main/python-sdk/tutorials/deploy-local/inferenceconfig.json":::

Enregistrez ce fichier sous `inferenceconfig.json`. 

# <a name="python"></a>[Python](#tab/python)

```python
env = Environment(name='myenv')
python_packages = ['nltk', 'numpy', 'onnxruntime']
for package in python_packages:
    env.python.conda_dependencies.add_pip_package(package)

inf_config = InferenceConfig(environment=env, source_directory='./source_dir', entry_script='./score.py')
```

Pour plus d’informations, consultez la documentation sur [LocalWebservice](/python/api/azureml-core/azureml.core.webservice.local.localwebservice), [Model.deploy()](/python/api/azureml-core/azureml.core.model.model#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) et [Webservice](/python/api/azureml-core/azureml.core.webservice.webservice).

---
Deploy your service again: null
---

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

Remplacez `bidaf_onnx:1` par le nom et le numéro de version de votre modèle.

[!notebook-python[] (~/azureml-examples-main/python-sdk/tutorials/deploy-local/2.deploy-local-cli.ipynb?name=re-deploy-model-code)]

# <a name="python"></a>[Python](#tab/python)

[!notebook-python[] (~/azureml-examples-main/python-sdk/tutorials/deploy-local/1.deploy-local.ipynb?name=re-deploy-model-code)]

[!notebook-python[] (~/azureml-examples-main/python-sdk/tutorials/deploy-local/1.deploy-local.ipynb?name=re-deploy-model-print-logs)]

Pour plus d'informations, consultez la documentation consacrée à [Model.deploy()](/python/api/azureml-core/azureml.core.model.model#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) et [Webservice](/python/api/azureml-core/azureml.core.webservice.webservice).

---
Ensuite, assurez-vous que vous pouvez envoyer une demande de publication au service :

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

[!notebook-python[] (~/azureml-examples-main/python-sdk/tutorials/deploy-local/2.deploy-local-cli.ipynb?name=send-post-request-code)]

# <a name="python"></a>[Python](#tab/python)

[!notebook-python[] (~/azureml-examples-main/python-sdk/tutorials/deploy-local/1.deploy-local.ipynb?name=send-post-request-code)]

---

## <a name="choose-a-compute-target"></a>Choisir une cible de calcul

Reportez-vous au diagramme ci-dessous lorsque vous choisissez une cible de calcul.

[![Comment choisir une cible de calcul](./media/how-to-deploy-and-where/how-to-choose-target.png)](././media/how-to-deploy-and-where/how-to-choose-target.png#lightbox)

[!INCLUDE [aml-deploy-target](../../includes/aml-compute-target-deploy.md)]

## <a name="re-deploy-to-cloud"></a>Redéploiement dans le cloud

Une fois que vous avez confirmé que votre service fonctionne localement et choisi une cible de calcul à distance, vous êtes prêt à effectuer le déploiement dans le cloud. 

Modifiez votre configuration de déploiement pour qu’elle corresponde à la cible de calcul que vous avez choisie, dans ce cas Azure Container Instances :

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

Les options disponibles pour une configuration de déploiement varient en fonction de la cible de calcul que vous choisissez.

:::code language="json" source="~/azureml-examples-main/python-sdk/tutorials/deploy-local/re-deploymentconfig.json":::

Enregistrez ce fichier en tant que `re-deploymentconfig.json`.

Pour plus d’informations, consultez [cette référence](./reference-azure-machine-learning-cli.md#deployment-configuration-schema).

# <a name="python"></a>[Python](#tab/python)

[!notebook-python[] (~/azureml-examples-main/python-sdk/tutorials/deploy-local/1.deploy-local.ipynb?name=deploy-model-on-cloud-code)]

---

Déployez votre service :


# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

Remplacez `bidaf_onnx:1` par le nom et le numéro de version de votre modèle.



[!notebook-python[] (~/azureml-examples-main/python-sdk/tutorials/deploy-local/2.deploy-local-cli.ipynb?name=deploy-model-on-cloud-code)]

# <a name="python"></a>[Python](#tab/python)


[!notebook-python[] (~/azureml-examples-main/python-sdk/tutorials/deploy-local/1.deploy-local.ipynb?name=re-deploy-service-code)]

[!notebook-python[] (~/azureml-examples-main/python-sdk/tutorials/deploy-local/1.deploy-local.ipynb?name=re-deploy-service-print-logs)]

Pour plus d'informations, consultez la documentation consacrée à [Model.deploy()](/python/api/azureml-core/azureml.core.model.model#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) et [Webservice](/python/api/azureml-core/azureml.core.webservice.webservice).

---


## <a name="call-your-remote-webservice"></a>Appeler votre service d’accès à distance

Lorsque vous déployez à distance, l’authentification de clé peut être activée. L’exemple ci-dessous montre comment obtenir votre clé de service avec Python afin d’effectuer une demande d’inférence.

[!notebook-python[] (~/azureml-examples-main/python-sdk/tutorials/deploy-local/1.deploy-local.ipynb?name=call-remote-web-service-code)]

[!notebook-python[] (~/azureml-examples-main/python-sdk/tutorials/deploy-local/1.deploy-local.ipynb?name=call-remote-webservice-print-logs)]



Consultez l’article sur les [applications clientes pour utiliser des services web](how-to-consume-web-service.md) pour d’autres exemples de clients dans d’autres langues.

### <a name="understanding-service-state"></a>Fonctionnement de l’état du service

Pendant le déploiement du modèle, vous pouvez voir le changement de l’état du service lors de son déploiement complet.

Le tableau ci-après décrit les différents états de service :

| État du service web | Description | État final ?
| ----- | ----- | ----- |
| Transition | Le service est en cours de déploiement. | Non |
| Unhealthy | Le service a été déployé, mais est actuellement inaccessible.  | Non |
| Non planifiable | Le service ne peut pas être déployé pour l’instant en raison d’un manque de ressources. | Non |
| Échec | Le déploiement du service a échoué en raison d’une erreur ou d’un plantage. | Oui |
| Healthy | Le service est sain et le point de terminaison est disponible. | Oui |

> [!TIP]
> Lors du déploiement, les images Docker pour les cibles de calcul sont créées et chargées à partir d’Azure Container Registry (ACR). Par défaut, Azure Machine Learning crée un registre ACR du niveau de service *De base*. Un passage au niveau Standard ou Premium du registre ACR de l’espace de travail est susceptible de réduire le temps nécessaire à la génération et au déploiement des images dans les cibles de calcul. Pour plus d’informations, consultez [Niveaux de service pour Azure Container Registry](../container-registry/container-registry-skus.md).

> [!NOTE]
> Si vous déployez un modèle sur Azure Kubernetes Service (AKS), nous vous conseillons d’activer [Azure Monitor](../azure-monitor/containers/container-insights-enable-existing-clusters.md) pour ce cluster. Vous pourrez ainsi mieux appréhender dans leur globalité l’intégrité du cluster et l’utilisation des ressources. Voici également quelques ressources pouvant vous être utiles :
>
> * [Rechercher les événements Resource Health qui ont un impact sur votre cluster AKS](../aks/aks-resource-health.md)
> * [Diagnostics d’Azure Kubernetes Service](../aks/concepts-diagnostics.md)
>
> Si vous essayez de déployer un modèle sur un cluster défectueux ou surchargé, des problèmes sont à prévoir. Si vous avez besoin d’aide pour résoudre des problèmes de cluster AKS, contactez le support technique AKS.

## <a name="delete-resources"></a>Supprimer des ressources

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)


[!notebook-python[] (~/azureml-examples-main/python-sdk/tutorials/deploy-local/2.deploy-local-cli.ipynb?name=delete-resource-code)]

[!notebook-python[] (~/azureml-examples-main/python-sdk/tutorials/deploy-local/2.deploy-local-cli.ipynb?name=delete-your-resource-code)]

Pour supprimer un service web déployé, utilisez `az ml service delete <name of webservice>`.

Pour supprimer de votre espace de travail un modèle inscrit, utilisez `az ml model delete <model id>`

Apprenez-en davantage sur [la suppression d’un service web](/cli/azure/ml(v1)/computetarget/create#az_ml_service_delete) et [la suppression d’un modèle](/cli/azure/ml/model#az_ml_model_delete).

# <a name="python"></a>[Python](#tab/python)

[!notebook-python[] (~/azureml-examples-main/python-sdk/tutorials/deploy-local/1.deploy-local.ipynb?name=delete-resource-code)]

Pour supprimer un service web déployé, utilisez `service.delete()`.
Pour supprimer un modèle inscrit, utilisez `model.delete()`.

Pour plus d’informations, consultez la documentation sur [WebService.delete()](/python/api/azureml-core/azureml.core.webservice%28class%29#delete--) et [Model.delete()](/python/api/azureml-core/azureml.core.model.model#delete--).

---

## <a name="next-steps"></a>Étapes suivantes

* [Résoudre des problèmes d’échec de déploiement](how-to-troubleshoot-deployment.md)
* [Mettre à jour un service web](how-to-deploy-update-web-service.md)
* [Déploiement en un clic pour les exécutions de ML automatisé dans Azure Machine Learning studio](how-to-use-automated-ml-for-ml-models.md#deploy-your-model)
* [Utiliser TLS pour sécuriser un service web par le biais d’Azure Machine Learning](how-to-secure-web-service.md)
* [Superviser vos modèles Azure Machine Learning avec Application Insights](how-to-enable-app-insights.md)
* [Créer des alertes d’événement et des déclencheurs pour les déploiements de modèle](how-to-use-event-grid.md)
