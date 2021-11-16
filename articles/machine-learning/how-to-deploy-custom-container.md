---
title: Déployer un conteneur personnalisé comme point de terminaison en ligne managé
titleSuffix: Azure Machine Learning
description: Découvrez comment utiliser un conteneur personnalisé pour utiliser des serveurs open source dans Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: mlops
ms.author: ssambare
author: shivanissambare
ms.reviewer: larryfr
ms.date: 10/21/2021
ms.topic: how-to
ms.custom: deploy, devplatv2
ms.openlocfilehash: 9b36a5799444b6da011693693b58f6574b135799
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131553629"
---
# <a name="deploy-a-tensorflow-model-served-with-tf-serving-using-a-custom-container-in-a-managed-online-endpoint-preview"></a>Déployer un modèle TensorFlow servi avec TF Serving en utilisant un conteneur personnalisé dans un point de terminaison en ligne managé (préversion)

Découvrez comment déployer un conteneur personnalisé en tant que point de terminaison en ligne managé dans Azure Machine Learning.

Les déploiements de conteneurs personnalisés peuvent utiliser des serveurs web autres que le serveur Flask Python par défaut utilisé par Azure Machine Learning. Les utilisateurs de ces déploiements peuvent toujours tirer parti de la surveillance, de la mise à l’échelle, des alertes et de l’authentification intégrées d’Azure Machine Learning.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

> [!WARNING]
> Microsoft peut ne pas être en mesure de vous aider à résoudre les problèmes dus à une image personnalisée. Si vous rencontrez des problèmes, vous pouvez être invité à utiliser l’image par défaut ou l’une des images fournies par Microsoft pour voir si le problème est spécifique à votre image.

## <a name="prerequisites"></a>Configuration requise

* Installez et configurez Azure CLI et l’extension ML. Pour plus d’informations, consultez [Installer, configurer et utiliser l’interface CLI (v2) [préversion]](how-to-configure-cli.md). 

* Vous devez avoir un groupe de ressources Azure dans lequel vous (ou le principal de service que vous utilisez) devez disposer d’un accès `Contributor`. Vous disposez d’un tel groupe de ressources si vous avez configuré votre extension ML conformément à l’article ci-dessus. 

* Vous devez avoir un espace de travail Azure Machine Learning. Vous disposez d’un tel espace de travail si vous avez configuré votre extension ML conformément à l’article ci-dessus.

* Si vous n’avez pas encore défini les paramètres par défaut pour Azure CLI, vous devez enregistrer vos paramètres par défaut. Pour éviter d’avoir à transmettre plusieurs fois les valeurs, exécutez :

   ```azurecli
   az account set --subscription <subscription id>
   az configure --defaults workspace=<azureml workspace name> group=<resource group>

* To deploy locally, you must have [Docker engine](https://docs.docker.com/engine/install/) running locally. This step is **highly recommended**. It will help you debug issues.

## Download source code

To follow along with this tutorial, download the source code below.

```azurecli
git clone https://github.com/Azure/azureml-examples --depth 1
cd azureml-examples/cli
```

## <a name="initialize-environment-variables"></a>Initialiser des variables d’environnement

Définissez des variables d’environnement :

:::code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-tfserving.sh" id="initialize_variables":::

## <a name="download-a-tensorflow-model"></a>Télécharger un modèle TensorFlow

Téléchargez et décompressez un modèle qui divise une entrée en deux et ajoute « 2 » au résultat :

:::code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-tfserving.sh" id="download_and_unzip_model":::

## <a name="run-a-tf-serving-image-locally-to-test-that-it-works"></a>Exécuter une image TF Serving localement pour tester son fonctionnement

Utilisez Docker pour exécuter votre image localement à des fins de test :

:::code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-tfserving.sh" id="run_image_locally_for_testing":::

### <a name="check-that-you-can-send-liveness-and-scoring-requests-to-the-image"></a>Vérifier que vous pouvez envoyer des requêtes liveness et scoring à l’image

Tout d’abord, vérifiez que le conteneur est « vivant », c’est-à-dire que le processus à l’intérieur du conteneur est toujours en cours d’exécution. Vous devez obtenir une réponse 200 (OK).

:::code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-tfserving.sh" id="check_liveness_locally":::

Ensuite, vérifiez que vous pouvez obtenir des prédictions sur les données sans étiquette :

:::code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-tfserving.sh" id="check_scoring_locally":::

### <a name="stop-the-image"></a>Arrêter l’image

Maintenant que vous l’avez testée localement, arrêtez l’image :

:::code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-tfserving.sh" id="stop_image":::

## <a name="create-a-yaml-file-for-your-endpoint-and-deployment"></a>Créer un fichier YAML pour votre point de terminaison et votre déploiement

Vous pouvez configurer votre déploiement cloud à l’aide de YAML. Jetez un coup d’œil à l’échantillon YAML pour cet exemple :

__tfserving-endpoint.yml__

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/endpoints/online/custom-container/tfserving-endpoint.yml":::

__tfserving-deployment.yml__

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/endpoints/online/custom-container/tfserving-deployment.yml":::

Voici quelques concepts importants à noter dans ce YAML :

### <a name="readiness-route-vs-liveness-route"></a>Itinéraire readiness et itinéraire liveness

Un serveur HTTP définit des chemins d’accès pour _liveness_ et _readiness_. Un itinéraire liveness est utilisé pour vérifier si le serveur est en cours d’exécution. Un itinéraire readiness est utilisé pour vérifier si le serveur est prêt à effectuer le travail. Dans l’inférence d’apprentissage automatique, un serveur peut répondre 200 (OK) à une requête liveness avant de charger un modèle. Le serveur peut répondre à 200 (OK) à une requête readiness uniquement une fois que le modèle a été chargé en mémoire.

Consultez la [documentation de Kubernetes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/) pour plus d’informations sur les probes liveness et readiness.

Notez que ce déploiement utilise le même chemin d’accès pour liveness et readiness, car TF Serving définit uniquement un itinéraire liveness.

### <a name="locating-the-mounted-model"></a>Localisation du modèle monté

Lorsque vous déployez un modèle en tant que point de terminaison en temps réel, Azure Machine Learning _monte_ votre modèle sur votre point de terminaison. Le montage du modèle vous permet de déployer de nouvelles versions du modèle sans avoir à créer une nouvelle image Docker. Par défaut, un modèle inscrit avec le nom *foo* et la version *1* se trouve sous le chemin suivant à l’intérieur de votre conteneur déployé : `/var/azureml-app/azureml-models/foo/1`.

Par exemple, si vous avez une structure de répertoire `/azureml-examples/cli/endpoints/online/custom-container` sur votre ordinateur local, où le modèle est nommé `half_plus_two` :

:::image type="content" source="./media/how-to-deploy-custom-container/local-directory-structure.png" alt-text="Diagramme montrant une arborescence de la structure de répertoire locale.":::

et que `tfserving-deployment.yml` contient :

```yaml
model:
    name: tfserving-mounted
    version: 1
    local_path: ./half_plus_two
```

votre modèle se trouvera alors sous `/var/azureml-app/azureml-models/tfserving-deployment/1` dans votre déploiement :

:::image type="content" source="./media/how-to-deploy-custom-container/deployment-location.png" alt-text="Diagramme montrant une arborescence de la structure de répertoire de déploiement.":::

Vous pouvez éventuellement configurer votre `model_mount_path`. Cela vous permet de modifier le chemin d’accès de l’emplacement où le modèle est monté. Par exemple, vous pouvez avoir le paramètre `model_mount_path` dans votre _tfserving-deployment.yml_ :

> [!IMPORTANT]
> Le `model_mount_path` doit être un chemin d’accès absolu valide dans Linux (le système d’exploitation de l’image conteneur).

```YAML
name: tfserving-deployment
endpoint_name: tfserving-endpoint
model:
  name: tfserving-mounted
  version: 1
  local_path: ./half_plus_two
model_mount_path: /var/tfserving-model-mount
.....
```

Votre modèle se trouvera alors sera situé à l’emplacement `/var/tfserving-model-mount/tfserving-deployment/1` dans votre déploiement. Notez qu’il ne figure plus dans le chemin d’accès `azureml-app/azureml-models`, mais dans le chemin de montage que vous avez spécifié :

:::image type="content" source="./media/how-to-deploy-custom-container/mount-path-deployment-location.png" alt-text="Diagramme montrant une arborescence de la structure de répertoire de déploiement lors de l’utilisation de mount_model_path.":::

### <a name="create-your-endpoint-and-deployment"></a>Créer votre point de terminaison et votre déploiement

Maintenant que vous avez compris comment le YAML a été construit, créez votre point de terminaison.

```azurecli
az ml online-endpoint create --name tfserving-endpoint -f endpoints/online/custom-container/tfserving-endpoint.yml
```

La création d’un déploiement peut prendre quelques minutes.



```azurecli
az ml online-deployment create --name tfserving-deployment -f endpoints/online/custom-container/tfserving-deployment.yml
```

### <a name="invoke-the-endpoint"></a>Appeler le point de terminaison

Une fois votre déploiement terminé, vérifiez si vous pouvez effectuer une requête scoring sur le point de terminaison déployé.

:::code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-tfserving.sh" id="invoke_endpoint":::

### <a name="delete-endpoint-and-model"></a>Supprimer le point de terminaison et le modèle

Maintenant que vous avez réussi à établir un score de votre point de terminaison, vous pouvez le supprimer :

```azurecli
az ml online-endpoint delete --name tfserving-endpoint
```

```azurecli
az ml model delete -n tfserving-mounted --version 1
```

## <a name="next-steps"></a>Étapes suivantes

- [Déploiement sûr pour les points de terminaison en ligne (préversion)](how-to-safely-rollout-managed-endpoints.md)
- [Résolution des problèmes de déploiement de points de terminaison en ligne managés](./how-to-troubleshoot-online-endpoints.md)
- [Exemple de Torch serve](https://github.com/Azure/azureml-examples/blob/main/cli/deploy-torchserve.sh)