---
title: Déploiement sécurisé pour les points de terminaison en ligne
titleSuffix: Azure Machine Learning
description: Déployez des versions plus récentes de modèles ML sans perturbation.
services: machine-learning
ms.service: machine-learning
ms.subservice: mlops
ms.author: seramasu
ms.reviewer: laobri
author: rsethur
ms.date: 10/21/2021
ms.topic: how-to
ms.custom: how-to, devplatv2
ms.openlocfilehash: 813ef955a202f5645d8e4881efb3b2d083fa4d63
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132063586"
---
# <a name="safe-rollout-for-online-endpoints-preview"></a>Déploiement sécurisé pour les points de terminaison en ligne (préversion)

Vous avez un modèle existant déployé en production et voulez déployer une nouvelle version du modèle. Comment déployer votre nouveau modèle ML sans provoquer de perturbation ? Une bonne réponse est le déploiement blue-green (bleu-vert), une approche dans laquelle une nouvelle version d’un service web est introduite en production en déployant la modification sur une petite partie d’utilisateurs/de demandes avant de la déployer complètement. Cet article part du principe que vous utilisez des points de terminaison en ligne. Pour plus d’informations, consultez [Que sont les points de terminaison Azure Machine Learning (préversion) ?](concept-endpoints.md).

Dans cet article, vous allez apprendre à effectuer les opérations suivantes :

> [!div class="checklist"]
> * Déployer un nouveau point de terminaison en ligne appelé « blue » qui fait fonctionner la version 1 du modèle
> * Mettre à l’échelle ce déploiement afin qu’il puisse gérer plus de demandes
> * Déployer la version 2 du modèle sur un point de terminaison appelé « green », qui n’accepte aucun trafic dynamique
> * Tester le déploiement green de façon isolée 
> * Envoyer 10 % du trafic dynamique au déploiement green
> * Transférer entièrement tout le trafic dynamique au déploiement green
> * Supprimer le déploiement blue de la version 1 maintenant inutilisée

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="prerequisites"></a>Prérequis

* Pour utiliser Azure Machine Learning, vous devez avoir un abonnement Azure. Si vous n’avez pas d’abonnement Azure, créez un compte gratuit avant de commencer. Essayez la [version gratuite ou payante d’Azure Machine Learning](https://azure.microsoft.com/free/) dès aujourd’hui.

* Vous devez installer et configurer Azure CLI et l’extension ML. Pour plus d’informations, consultez [Installer, configurer et utiliser l’interface CLI (v2) (préversion)](how-to-configure-cli.md). 

* Vous devez avoir un groupe de ressources Azure dans lequel vous (ou le principal de service que vous utilisez) devez disposer d’un accès `Contributor`. Vous disposez d’un tel groupe de ressources si vous avez configuré votre extension ML conformément à l’article ci-dessus. 

* Vous devez avoir un espace de travail Azure Machine Learning. Vous disposez d’un tel espace de travail si vous avez configuré votre extension ML conformément à l’article ci-dessus.

* Si vous n’avez pas encore défini les paramètres par défaut pour Azure CLI, vous devez enregistrer vos paramètres par défaut. Pour éviter d’avoir à transmettre plusieurs fois les valeurs, exécutez :

   ```azurecli
   az account set --subscription <subscription id>
   az configure --defaults workspace=<azureml workspace name> group=<resource group>
   ```

* Un point de terminaison et un déploiement en ligne existants. Cet article suppose que votre déploiement est tel que décrit dans [Déployer et scorer un modèle Machine Learning avec un point de terminaison en ligne managé (préversion)](how-to-deploy-managed-online-endpoints.md).

* Si vous n’avez pas défini la variable d’environnement $ENDPOINT _NAME, faites-le maintenant :

   :::code language="azurecli" source="~/azureml-examples-main/cli/deploy-safe-rollout-online-endpoints.sh" ID="set_endpoint_name":::

* (Recommandé) Clonez le dépôt d’exemples et accédez au répertoire `cli/` du dépôt : 

   ```azurecli
   git clone https://github.com/Azure/azureml-examples
   cd azureml-examples/cli
   ```

Les commandes de ce tutoriel se trouvent dans le fichier `deploy-safe-rollout-online-endpoints.sh` et les fichiers de configuration YAML se trouvent dans le sous-répertoire `endpoints/online/managed/sample/`.

## <a name="confirm-your-existing-deployment-is-created"></a>Vérifier que votre déploiement existant est créé

Vous pouvez afficher l’état de vos point de terminaison et déploiement existants en exécutant : 

```azurecli
az ml online-endpoint show --name $ENDPOINT_NAME 

az ml online-deployment show --name blue --endpoint $ENDPOINT_NAME 
```

Vous devez normalement voir le point de terminaison identifié par `$ENDPOINT_NAME` et un déploiement appelé `blue`. 

## <a name="scale-your-existing-deployment-to-handle-more-traffic"></a>Mettre à l’échelle votre déploiement existant pour gérer davantage de trafic

Dans le déploiement décrit dans [Déployer et scorer un modèle Machine Learning avec un point de terminaison en ligne managé (préversion)](how-to-deploy-managed-online-endpoints.md), vous définissez `instance_count` sur la valeur `1` dans le fichier yaml de déploiement. Vous pouvez effectuer un scale-out à l’aide de la commande `update` :

:::code language="azurecli" source="~/azureml-examples-main/cli/deploy-safe-rollout-online-endpoints.sh" ID="scale_blue" :::

> [!Note]
> Notez que, dans la commande ci-dessus, nous utilisons `--set` pour remplacer la configuration de déploiement. Vous pouvez également mettre à jour le fichier YAML et le transmettre en tant qu’entrée à la commande `update` en tapant `--file`.

## <a name="deploy-a-new-model-but-send-it-no-traffic-yet"></a>Déployer un nouveau modèle, mais sans lui envoyer encore aucun trafic

Créez un déploiement nommé `green` : 

:::code language="azurecli" source="~/azureml-examples-main/cli/deploy-safe-rollout-online-endpoints.sh" ID="create_green" :::

Étant donné que nous n’avons pas explicitement alloué de trafic à green, aucun trafic ne luis est alloué. Vous pouvez vérifier cela à l’aide de la commande :

:::code language="azurecli" source="~/azureml-examples-main/cli/deploy-safe-rollout-online-endpoints.sh" ID="get_traffic" :::

### <a name="test-the-new-deployment"></a>Tester le nouveau déploiement

Bien que 0 % du trafic soit alloué à `green`, vous pouvez l’appeler directement en spécifiant le nom `--deployment` :

:::code language="azurecli" source="~/azureml-examples-main/cli/deploy-safe-rollout-online-endpoints.sh" ID="test_green" :::

Si vous voulez utiliser un client REST pour appeler le déploiement directement sans passer par les règles de trafic, définissez l’en-tête HTTP suivant : `azureml-model-deployment: <deployment-name>`. L’extrait de code ci-dessous utilise `curl` pour appeler le déploiement directement. L’extrait de code doit fonctionner dans des environnements Unix/WSL :

:::code language="azurecli" source="~/azureml-examples-main/cli/deploy-safe-rollout-online-endpoints.sh" ID="test_green_using_curl" :::

## <a name="test-the-new-deployment-with-a-small-percentage-of-live-traffic"></a>Tester le nouveau déploiement avec un faible pourcentage du trafic dynamique

Après avoir testé votre déploiement `green`, allouez-lui un petit pourcentage de trafic :

:::code language="azurecli" source="~/azureml-examples-main/cli/deploy-safe-rollout-online-endpoints.sh" ID="green_10pct_traffic" :::

À présent, votre déploiement `green` va recevoir 10 % des demandes. 

## <a name="send-all-traffic-to-your-new-deployment"></a>Envoyer tout le trafic vers votre nouveau déploiement

Une fois que vous constatez que votre déploiement `green` est entièrement satisfaisant, basculez tout le trafic vers celui-ci.

:::code language="azurecli" source="~/azureml-examples-main/cli/deploy-safe-rollout-online-endpoints.sh" ID="green_100pct_traffic" :::

## <a name="remove-the-old-deployment"></a>Supprimer l’ancien déploiement

:::code language="azurecli" source="~/azureml-examples-main/cli/deploy-safe-rollout-online-endpoints.sh" ID="delete_blue" :::

## <a name="delete-the-endpoint-and-deployment"></a>Supprimer le point de terminaison et le déploiement

Si vous ne comptez pas utiliser le déploiement, vous devez le supprimer avec :

:::code language="azurecli" source="~/azureml-examples-main/cli/deploy-safe-rollout-online-endpoints.sh" ID="delete_endpoint" :::


## <a name="next-steps"></a>Étapes suivantes
- [Déployer des modèles avec REST (préversion)](how-to-deploy-with-rest.md)
- [Créer et utiliser des points de terminaison en ligne managés (préversion) dans le studio](how-to-use-managed-online-endpoint-studio.md)
- [Accéder aux ressources Azure avec un point de terminaison en ligne managé et une identité managée (préversion)](how-to-access-resources-from-endpoints-managed-identities.md)
- [Superviser des points de terminaison en ligne managés (préversion)](how-to-monitor-online-endpoints.md)
- [Gérer et augmenter les quotas pour les ressources avec Azure Machine Learning](how-to-manage-quotas.md#azure-machine-learning-managed-online-endpoints-preview)
- [Afficher les coûts d’un point de terminaison en ligne managé Azure Machine Learning (préversion)](how-to-view-online-endpoints-costs.md)
- [Liste des références SKU pour les points de terminaison en ligne managés (préversion)](reference-managed-online-endpoints-vm-sku-list.md)
- [Résolution des problèmes de déploiement et de scoring de points de terminaison en ligne managés (préversion)](how-to-troubleshoot-managed-online-endpoints.md)
- [Informations de référence YAML sur les points de terminaison en ligne managés (préversion)](reference-yaml-endpoint-managed-online.md)
