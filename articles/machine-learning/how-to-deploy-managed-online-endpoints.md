---
title: Déployer un modèle ML en utilisant un point de terminaison en ligne (préversion)
titleSuffix: Azure Machine Learning
description: Découvrez comment déployer votre modèle Machine Learning en tant que service web sur Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: mlops
ms.author: seramasu
ms.reviewer: laobri
author: rsethur
ms.date: 10/21/2021
ms.topic: how-to
ms.custom: how-to, devplatv2, ignite-fall-2021
ms.openlocfilehash: dba2e849fb28dfb0f6667b496c65bdef8cbdf046
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131557544"
---
# <a name="deploy-and-score-a-machine-learning-model-by-using-an-online-endpoint-preview"></a>Déployer et noter un modèle Machine Learning en utilisant un point de terminaison en ligne (préversion)

Découvrez comment utiliser un point de terminaison en ligne (préversion) pour déployer votre modèle, de sorte à n’avoir ni à créer ni à gérer l’infrastructure sous-jacente. Vous allez commencer par déployer un modèle sur votre ordinateur local pour déboguer les erreurs éventuelles, puis vous allez le déployer et le tester dans Azure.

Vous allez également apprendre à afficher les journaux et à surveiller le contrat de niveau de service (SLA). Vous commencez avec un modèle et obtenez un point de terminaison HTTPS/REST évolutif qui peut être utilisé pour la notation en ligne et en temps réel. 

Pour plus d’informations, consultez [Que sont les points de terminaison Azure Machine Learning (préversion) ?](concept-endpoints.md).

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="prerequisites"></a>Prérequis

* Pour utiliser Azure Machine Learning, vous devez disposer d’un abonnement Azure. Si vous n’avez pas d’abonnement Azure, créez un compte gratuit avant de commencer. Essayez la [version gratuite ou payante d’Azure Machine Learning](https://azure.microsoft.com/free/).

* Installer et configurer Azure CLI ainsi que l’extension `ml` pour l’interface CLI Azure. Pour plus d’informations, consultez [Installer, configurer et utiliser l’interface CLI (v2) (préversion)](how-to-configure-cli.md). 

* Vous devez avoir un groupe de ressources Azure dans lequel vous (ou le principal de service que vous utilisez) devez disposer d’un accès Contributeur. Un groupe de ressources est créé lors de [l’installation, de la configuration et de l’utilisation de l’interface CLI (v2) (préversion)](how-to-configure-cli.md). 

* Vous devez avoir un espace de travail Azure Machine Learning. Un espace de travail est crée dans [Installer, configurer et utiliser l’interface CLI (v2) (préversion)](how-to-configure-cli.md).

* Si vous n’avez pas encore défini les paramètres par défaut pour l’interface CLI Azure, enregistrez vos paramètres par défaut. Pour éviter de transmettre plusieurs fois les valeurs de votre abonnement, de votre espace de travail et de votre groupe de ressources, exécutez le code suivant :

   ```azurecli
   az account set --subscription <subscription ID>
   az configure --defaults workspace=<Azure Machine Learning workspace name> group=<resource group>
   ```

* (Facultatif) Pour déployer localement, vous devez [installer Moteur Docker](https://docs.docker.com/engine/install/) sur votre ordinateur local. Nous *recommandons fortement* cette option pour déboguer les problèmes plus facilement.

> [!IMPORTANT]
> Les exemples cités dans ce document partent du principe que vous utilisez un interpréteur de commandes Bash. Par exemple, à partir d’un système Linux ou d’un [Sous-système Windows pour Linux](/windows/wsl/about). 

## <a name="prepare-your-system"></a>Préparer votre système

Pour suivre cet article, commencez par cloner le référentiel d’exemples (azureml-examples). Ensuite, exécutez le code suivant pour accéder au répertoire d’exemples :

```azurecli
git clone https://github.com/Azure/azureml-examples
cd azureml-examples
cd cli
```

Pour définir le nom de votre point de terminaison, choisissez l’une des commandes suivantes, en fonction de votre système d’exploitation (remplacez `YOUR_ENDPOINT_NAME` par un nom unique).

Pour UNIX, exécutez la commande suivante :

:::code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-local-endpoint.sh" ID="set_endpoint_name":::

> [!NOTE]
> Nous avons récemment modifié l’interface CLI : auparavant, nous avions à la fois `endpoint` et `deployment` sous `az ml endpoint`, mais nous les avons séparés en `az ml online-endpoint` et `az ml online-deployment`.  Cela facilitera l’utilisation des points de terminaison dans les scripts CI/CD.

> [!NOTE]
> Les noms de points de terminaison doivent être uniques au sein d’une région Azure. Par exemple, dans la région Azure `westus2`, il ne peut y avoir qu’un seul point de terminaison avec le nom `my-endpoint`. 

## <a name="review-the-endpoint-and-deployment-configurations"></a>Réviser les configurations de point de terminaison et de déploiement

L’extrait de code suivant montre le fichier *endpoints/online/managed/sample/endpoint.yml* : 

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/endpoints/online/managed/sample/endpoint.yml":::

> [!NOTE]
> Pour obtenir une description complète des YAML, consultez [Informations de référence YAML sur les points de terminaison en ligne managés (préversion)](reference-yaml-endpoint-managed-online.md).

La référence pour le format YAML du point de terminaison est décrite dans le tableau suivant. Pour savoir comment spécifier ces attributs, consultez l’exemple YAML dans [Préparez votre système](#prepare-your-system) ou la [référence YAML du point de terminaison en ligne](reference-yaml-endpoint-managed-online.md). Pour plus d’informations sur les limites associées aux points de terminaison managés, consultez [Gérer et augmenter les quotas pour les ressources avec Azure Machine Learning](how-to-manage-quotas.md#azure-machine-learning-managed-online-endpoints-preview).

| Clé | Description |
| --- | --- |
| `$schema`    | (Facultatif) Schéma YAML. Pour visualiser toutes les options disponibles dans le fichier YAML, vous pouvez visualiser le schéma dans l’exemple précédent dans un navigateur.|
| `name`       | Nom du point de terminaison. Il doit être unique au sein de la région Azure.|
| `traffic` | Pourcentage du trafic depuis le point de terminaison à détourner vers chaque déploiement. La somme des valeurs de trafic doit être 100. |
| `auth_mode` | Utilisez `key` pour l’authentification basée sur les clés. Utilisez `aml_token` pour l’authentification Azure Machine Learning basée sur les jetons. `key` n’expire pas mais `aml_token` expire. (Récupérez le jeton le plus récent avec la commande `az ml online-endpoint get-credentials`.) |

L’exemple contient tous les fichiers nécessaires pour déployer un modèle sur un point de terminaison en ligne. Pour déployer un modèle, vous devez disposer des éléments suivants :

- Fichiers de modèle (ou le nom et la version d’un modèle déjà inscrit dans votre espace de travail). Dans l’exemple, nous avons un modèle scikit-learn qui effectue une régression.
- Code requis pour évaluer le modèle. Dans ce cas, nous disposons d’un fichier *score.py*.
- Environnement dans lequel votre modèle s’exécute. Comme vous le verrez, l’environnement peut être une image Docker avec les dépendances Conda, ou il peut s’agir d’un fichier Dockerfile.
- Paramètres pour spécifier le type d’instance et la capacité de mise à l’échelle.

L’extrait de code suivant montre le fichier *endpoints/online/managed/sample/blue-deployment.yml*, avec toutes les entrées requises : 

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/endpoints/online/managed/sample/blue-deployment.yml":::

Le tableau décrit les attributs d’un `deployment` :

| Clé | Description |
| --- | --- |
| `name`  | Le nom du déploiement. |
| `model` | Dans cet exemple, nous spécifions les propriétés de modèle inline : `local_path`. Les fichiers de modèle sont automatiquement téléchargés et enregistrés avec un nom généré automatiquement. Pour connaître les meilleures pratiques associées, consultez les conseils dans la section suivante. |
| `code_configuration.code.local_path` | Répertoire qui contient tout le code source Python pour le scoring du modèle. Vous pouvez utiliser des répertoires et des packages imbriqués. |
| `code_configuration.scoring_script` | Fichier Python dans le répertoire de notation `code_configuration.code.local_path`. Ce code Python doit avoir une fonction `init()` et une fonction `run()`. La fonction `init()` sera appelée une fois le modèle créé ou mis à jour (vous pouvez l’utiliser pour mettre en cache le modèle en mémoire, par exemple). La fonction `run()` est appelée à chaque appel du point de terminaison pour effectuer la notation et la prédiction réelles. |
| `environment` | Contient les détails de l’environnement pour héberger le modèle et le code. Dans cet exemple, nous avons des définitions inline qui incluent le `path`. Nous utiliserons `environment.docker.image` pour l’image. Les dépendances `conda_file` seront installées au-dessus de l’image. Pour en savoir plus, consultez les conseils dans la section suivante. |
| `instance_type` | Référence SKU de machine virtuelle pour héberger vos instances de déploiement. Pour plus d’informations, consultez [Références SKU de machine virtuelle prises en charge par des points de terminaison en ligne managés](reference-managed-online-endpoints-vm-sku-list.md). |
| `instance_count` | Nombre d’instances dans le déploiement. Basez la valeur sur la charge de travail que vous attendez. Pour une haute disponibilité, nous vous recommandons de définir `scale_settings.instance_count` sur au moins `3`. |

Pour plus d’informations sur le schéma YAML, consultez le document [Référence YAML sur les points de terminaison en ligne](reference-yaml-endpoint-managed-online.md).

> [!NOTE]
> Pour utiliser Kubernetes comme cible de calcul à la place des points de terminaison gérés :
> 1. Créez et attachez votre cluster Kubernetes comme cible de calcul à votre espace de travail Azure Machine Learning avec [Azure Machine Learning Studio](how-to-attach-arc-kubernetes.md?&tabs=studio#attach-arc-cluster).
> 1. Utilisez le [code YAML de point de terminaison](https://github.com/Azure/azureml-examples/blob/main/cli/endpoints/online/aks/simple-flow/1-create-aks-endpoint-with-blue.yml) pour cibler Kubernetes à la place du code YAML de point de terminaison géré. Vous devrez modifier le code YAML pour remplacer la valeur de `target` par le nom de votre cible de calcul inscrite.
>
> Toutes les commandes utilisées dans cet article (à l’exception de la surveillance du contrat SLA facultative et de l’intégration d’Azure Log Analytics) peuvent être utilisées avec des points de terminaison gérés ou avec des points de terminaison Kubernetes.

### <a name="register-your-model-and-environment-separately"></a>Enregistrement séparé de votre modèle et de votre environnement

Dans cet exemple, nous spécifions le `local_path` (l’emplacement à partir duquel charger les fichiers) inline. L’interface CLI charge automatiquement les fichiers et inscrit le modèle et l’environnement. En guise de bonne pratique pour la production, vous devez inscrire le modèle et l’environnement et spécifier séparément le nom et la version inscrits dans le code YAML. Utilisez le formulaire `model: azureml:my-model:1` ou `environment: azureml:my-env:1`.

Pour effectuer l’inscription, vous pouvez extraire les définitions YAML de `model` et `environment` dans des fichiers YAML distincts et utiliser les commandes `az ml model create` et `az ml environment create`. Pour en savoir plus sur ces commandes, exécutez `az ml model create -h` et `az ml environment create -h`.

### <a name="use-different-cpu-and-gpu-instance-types"></a>Utilisation de différents types d’instances de processeur et GPU

Le code YAML ci-dessus utilise un type à usage général (`Standard_F2s_v2`) et une image Docker non GPU (dans le code YAML, reportez-vous à l’attribut `image`). Pour le calcul GPU, choisissez une référence SKU de type de calcul GPU et une image Docker GPU.

Pour connaître les types d’instances à usage général et GPU pris en charge, consultez les [SKU de machine virtuelle prises en charge par des points de terminaison en ligne managés](reference-managed-online-endpoints-vm-sku-list.md). Pour obtenir la liste des images de base de processeur et GPU d’Azure ML, consultez [Images de base Azure Machine Learning](https://github.com/Azure/AzureML-Containers).

### <a name="use-more-than-one-model"></a>Utilisation de plusieurs modèles

Actuellement, vous pouvez spécifier un seul modèle par déploiement dans le code YAML. Si vous avez plusieurs modèles, quand vous inscrivez le modèle, copiez tous les modèles sous forme de fichiers ou de sous-répertoires dans un dossier que vous utilisez pour l’inscription. Dans votre script de notation, utilisez la variable d’environnement `AZUREML_MODEL_DIR` pour récupérer le chemin d’accès au dossier racine du modèle. La structure de répertoire sous-jacente est conservée.

## <a name="understand-the-scoring-script"></a>Comprendre le script de scoring

> [!TIP]
> Le format du script de scoring pour les points de terminaison en ligne est le même que celui utilisé dans la version précédente de l’interface CLI et dans le SDK Python.

Comme spécifié plus tôt, le `code_configuration.scoring_script` doit avoir une fonction `init()` et une fonction `run()`. Cet exemple utilise le [fichier score.py](https://github.com/Azure/azureml-examples/blob/main/cli/endpoints/online/model-1/onlinescoring/score.py). La fonction `init()` est appelée lorsque le conteneur est initialisé ou démarré. Cette initialisation se produit généralement peu après la création ou la mise à jour du déploiement. Écrivez la logique ici pour effectuer des opérations d’initialisation globales telles que la mise en cache du modèle en mémoire (comme dans cet exemple). La fonction `run()` est appelée à chaque appel du point de terminaison et doit effectuer la notation et la prédiction réelles. Dans l’exemple, nous extrayons les données de l’entrée JSON, appelons la méthode `predict()` du modèle scikit-learn et retournons le résultat.

## <a name="deploy-and-debug-locally-by-using-local-endpoints"></a>Déployer et déboguer localement à l’aide de points de terminaison locaux

Pour gagner du temps lors du débogage, il est *fortement recommandé* d’effectuer une série de tests localement sur votre point de terminaison. Pour plus d’informations, consultez [Déboguer les points de terminaison en ligne gérés localement dans Visual Studio Code](how-to-debug-managed-online-endpoints-visual-studio-code.md).

> [!NOTE]
> * Pour déployer localement, vous devez avoir installé le [moteur Docker](https://docs.docker.com/engine/install/).
> * Le moteur Docker doit être en cours d’exécution. Le moteur Docker démarre généralement au démarrage de l’ordinateur. Si ce n’est pas le cas, vous pouvez [dépanner le Moteur Docker](https://docs.docker.com/config/daemon/#start-the-daemon-manually).

> [!IMPORTANT]
> L’objectif d’un déploiement de points de terminaison local consiste à valider et déboguer votre code et votre configuration avant le déploiement sur Azure. Un déploiement local présente les limitations suivantes :
> - Les points de terminaison locaux *ne prennent pas* en charge les règles de trafic, l’authentification, les paramètres de mise à l’échelle ni les paramètres de sonde. 
> - Les points de terminaison locaux ne prennent en charge qu’un seul déploiement par point de terminaison. 

### <a name="deploy-the-model-locally"></a>Déployer le modèle localement

Commencez par créer le point de terminaison. Si vous le souhaitez, pour un point de terminaison local, vous pouvez ignorer cette étape et créer directement le déploiement (étape suivante), qui créera ensuite les métadonnées requises. Cela est utile à des fins de développement et de test.

:::code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-local-endpoint.sh" ID="create_endpoint":::

À présent, créez un déploiement nommé `blue` sous le point de terminaison.

:::code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-local-endpoint.sh" ID="create_deployment":::

L’indicateur `--local` oriente l’interface CLI pour déployer le point de terminaison dans l’environnement Docker.

> [!TIP]
> Utilisez Visual Studio Code pour tester et déboguer vos points de terminaison localement. Pour plus d’informations, consultez [Déboguer les points de terminaison en ligne gérés localement dans Visual Studio Code](how-to-debug-managed-online-endpoints-visual-studio-code.md).

### <a name="verify-the-local-deployment-succeeded"></a>Vérifier si le déploiement local a réussi

Vérifiez l’état pour voir si le modèle a été déployé sans erreur :

:::code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-local-endpoint.sh" ID="get_status":::

Le résultat doit ressembler au JSON suivant. Notez que `provisioning_state` est `Succeeded`.

```json
{
  "auth_mode": "key",
  "location": "local",
  "name": "docs-endpoint",
  "properties": {},
  "provisioning_state": "Succeeded",
  "scoring_uri": "http://localhost:49158/score",
  "tags": {},
  "traffic": {}
}
```

### <a name="invoke-the-local-endpoint-to-score-data-by-using-your-model"></a>Appeler le point de terminaison local pour effectuer la notation des données avec votre modèle

Appelez le point de terminaison pour effectuer la notation du modèle à l’aide de la commande pratique `invoke` et en transmettant les paramètres de requête stockés dans un fichier JSON :

:::code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-local-endpoint.sh" ID="test_endpoint":::

Si vous souhaitez utiliser un client REST (par exemple, une boucle), vous devez disposer de l’URI de notation. Pour récupérer l’URI de notation, exécutez `az ml online-endpoint show --local -n $ENDPOINT_NAME`. Dans les données retournées, recherchez l’attribut `scoring_uri`. Des exemples de commandes curl sont disponibles plus loin dans ce document.

### <a name="review-the-logs-for-output-from-the-invoke-operation"></a>Rechercher dans les journaux la sortie de l’opération d’appel

Dans l’exemple fichier *score.py*, la méthode `run()` journalise une sortie dans la console. Vous pouvez afficher cette sortie en réutilisant la commande `get-logs` :

:::code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-local-endpoint.sh" ID="get_logs":::

##  <a name="deploy-your-managed-online-endpoint-to-azure"></a>Déployer votre point de terminaison en ligne managé dans Azure

Puis, déployez votre point de terminaison en ligne managé dans Azure.

### <a name="deploy-to-azure"></a>Déployer sur Azure

Pour créer le point de terminaison dans le cloud, exécutez le code suivant :

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint.sh" ID="create_endpoint" :::

Pour créer le déploiement nommé `blue` sous le point de terminaison, exécutez le code suivant :

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint.sh" ID="create_deployment" :::

Ce déploiement peut prendre jusqu’à 15 minutes selon que l’environnement ou l’image sous-jacent(e) est généré(e) pour la première fois ou non. Les déploiements suivants qui utilisent le même environnement termineront le traitement plus rapidement.

> [!Important]
> L’indicateur --all-traffic dans `az ml online-deployment create` ci-dessus alloue 100 % du trafic au point de terminaison pour le déploiement nouvellement créé. Bien que cela soit utile à des fins de développement et de test, pour la production, vous souhaiterez peut-être ouvrir le trafic vers le nouveau déploiement via une commande explicite. Par exemple : `az ml online-endpoint update -n $ENDPOINT_NAME --traffic "blue=100"` 

> [!TIP]
> * Si vous préférez ne pas bloquer votre console CLI, vous pouvez ajouter l’indicateur `--no-wait` à la commande. Toutefois, cela arrêtera l’affichage interactif de l’état du déploiement.
>
> * Utilisez la [résolution des problèmes de déploiement de points de terminaison en ligne managés (préversion)](./how-to-troubleshoot-online-endpoints.md) pour déboguer les erreurs.

### <a name="check-the-status-of-the-deployment"></a>Vérifier l’état du déploiement

La commande `show` contient des informations dans `provisioning_status` pour le point de terminaison et le déploiement :

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint.sh" ID="get_status" :::

Vous pouvez répertorier tous les points de terminaison dans l’espace de travail sous la forme d’un tableau à l’aide de la commande `list` :

```azurecli
az ml online-endpoint list --output table
```

### <a name="check-the-status-of-the-cloud-deployment"></a>Vérifier l’état de déploiement du Cloud

Vérifiez les journaux pour voir si le modèle a été déployé sans erreur :

:::code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint.sh" ID="get_logs" :::

Par défaut, les journaux sont extraits du serveur d’inférence. Pour afficher les journaux de l’initialiseur de stockage (lequel monte des ressources telles que le modèle et le code dans le conteneur), ajoutez l’indicateur `--container storage-initializer`.

### <a name="invoke-the-endpoint-to-score-data-by-using-your-model"></a>Appeler le point de terminaison pour effectuer la notation des données avec votre modèle

Vous pouvez utiliser la commande `invoke` ou un client REST de votre choix pour appeler le point de terminaison et effectuer le scoring de certaines données : 

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint.sh" ID="test_endpoint" :::

L’exemple suivant montre comment récupérer la clé utilisée pour l’authentification auprès du point de terminaison :

:::code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint.sh" ID="test_endpoint_using_curl_get_key":::

Ensuite, utilisez la boucle pour noter les données.

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint.sh" ID="test_endpoint_using_curl" :::

Notez que nous utilisons les commandes `show` et `get-credentials` pour récupérer les informations d’authentification. Notez également que nous utilisons l’indicateur `--query` pour filtrer les attributs sur les attributs nécessaires uniquement. Pour en apprendre davantage sur `--query` consultez [Interroger la sortie de commande Azure CLI](/cli/azure/query-azure-cli).

Pour visualiser les journaux d’appel, exécutez `get-logs` de nouveau.

### <a name="optional-update-the-deployment"></a>(Facultatif) Mettre à jour le déploiement

Si vous souhaitez mettre à jour le code, le modèle, l’environnement ou vos paramètres de mise à l’échelle, mettez à jour le fichier YAML et exécutez la commande `az ml online-endpoint update`. 

> [!Note]
> Si vous mettez à jour le nombre d’instances, ainsi que d’autres paramètres de modèle (code, modèle ou environnement) dans une même commande `update` : la première opération de mise à l’échelle est effectuée, puis les autres mises à jour sont appliquées. Dans l’environnement de production, il est conseillé d’effectuer ces opérations séparément.

Pour comprendre comment `update` fonctionne :

1. Ouvrez le fichier *online/model-1/onlinescoring/score.py*.
1. Modifiez la dernière ligne de la fonction `init()` : après `logging.info("Init complete")`, ajoutez `logging.info("Updated successfully")`. 
1. Enregistrez le fichier .
1. Exécutez cette commande :

    ```azurecli
    az ml online-deployment update -n blue --endpoint $ENDPOINT_NAME -f endpoints/online/managed/sample/blue-deployment.yml
    ```

    > [!Note]
    > La mise à jour à l’aide de YAML est déclarative. Autrement dit, les modifications apportées dans le fichier YAML seront reflétées dans les ressources du Gestionnaire de ressource Azure sous-jacentes (points de terminaison et déploiements). Une approche déclarative facilite les [GitOps](https://www.atlassian.com/git/tutorials/gitops) : *Toutes* les modifications apportées aux points de terminaison et déploiements passent (même `instance_count`) par le fichier YAML. Vous pouvez effectuer des mises à jour sans utiliser le YAML à l’aide de l’indicateur `--set`.
    
1. Comme vous avez modifié la fonction `init()` (`init()`qui s’exécute lors de la création ou de la mise à jour du point de terminaison), le message `Updated successfully` figurera dans les journaux. Récupérez les journaux en exécutant :

    :::code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint.sh" ID="get_logs" :::

La commande `update` fonctionne également avec les déploiements locaux. Utilisez la même commande `az ml online-deployment update` avec l’indicateur `--local`.

> [!TIP]
> Avec la commande `update`, vous pouvez utiliser le paramètre [`--set` disponible dans l’interface CLI Azure](/cli/azure/use-cli-effectively#generic-update-arguments) pour remplacer des attributs dans votre code YAML *ou* pour définir des attributs spécifiques sans transmettre le fichier YAML. L’utilisation de `--set` pour les attributs uniques est particulièrement importante dans les scénarios de développement et de test. Par exemple, pour effectuer un scale-up de la valeur `instance_count` pour le premier déploiement, vous pouvez utiliser l’indicateur `--set instance_count=2`. Toutefois, étant donné que le code YAML n’est pas mis à jour, cette technique ne facilite pas les [GitOps](https://www.atlassian.com/git/tutorials/gitops).
> [!Note]
> L’exemple ci-dessus illustre la mise à jour propagée InPlace : autrement dit, le même déploiement est mis à jour avec la nouvelle configuration, avec 20 % de nœuds à la fois. Si le déploiement comporte 10 nœuds, 2 nœuds à la fois seront mis à jour. Pour l’utilisation en production, vous souhaiterez peut-être envisager un [déploiement bleu-vert](how-to-safely-rollout-managed-endpoints.md), qui offre une alternative plus sûre.
### <a name="optional-configure-autoscaling"></a>(Facultatif) Configurer la mise à l’échelle automatique

La mise à l’échelle automatique exécute automatiquement la quantité appropriée de ressources pour gérer la charge sur votre application. Les points de terminaison gérés en ligne prennent en charge la mise à l’échelle automatique via l’intégration à la fonctionnalité de mise à l’échelle automatique d’Azure Monitor. Pour configurer la mise à l’échelle automatique, consultez [Mise à l’échelle automatique des points de terminaison en ligne](how-to-autoscale-endpoints.md).

### <a name="optional-monitor-sla-by-using-azure-monitor"></a>(Facultatif) Surveiller le contrat SLA à l’aide d’Azure Monitor

Pour afficher les mesures et définir des alertes en fonction de votre contrat SLA, effectuez les étapes décrites dans [surveiller les points de terminaison managés en ligne](how-to-monitor-online-endpoints.md).

### <a name="optional-integrate-with-log-analytics"></a>(Facultatif) Intégrer à Log Analytics

La commande `get-logs` ne fournit que les quelques dernières centaines de lignes de journaux d’une instance sélectionnée automatiquement. Toutefois, Log Analytics offre un moyen de stocker et d’analyser durablement les journaux. 

En premier lieu, créez un espace de travail Log Analytics en suivant les étapes décrites dans [Créer un espace de travail Log Analytics dans le Portail Azure](../azure-monitor/logs/quick-create-workspace.md#create-a-workspace).

Ensuite, dans le portail Azure :

1. Accédez au groupe de ressources.
1. Sélectionnez votre point de terminaison.
1. Sélectionnez la **page de ressources ARM**.
1. Sélectionnez **Paramètres de diagnostic**.
1. Sélectionnez **Ajouter des paramètres**.
1. Sélectionner pour activer l’envoi des journaux de diagnostic vers un espace de travail Log Analytics.

La connexion des journaux peut prendre jusqu’à une heure. Passée une heure, envoyez des demandes de notation, puis vérifiez les journaux en procédant comme suit :

1. Ouvrez l’espace de travail Log Analytics. 
1. Dans le menu de gauche, sélectionnez **Journaux**.
1. Fermez la fenêtre contextuelle **Requêtes** qui s’ouvre automatiquement.
1. Double cliquez sur **AmlOnlineEndpointConsoleLog**.
1. Sélectionnez **Exécuter**.

## <a name="delete-the-endpoint-and-the-deployment"></a>Supprimez le point de terminaison et le déploiement

Si vous n’envisagez pas d’utiliser le déploiement, vous devez le supprimer en exécutant le code suivant (cela supprime le point de terminaison et tous les déploiements sous-jacents) :

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint.sh" ID="delete_endpoint" :::

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus, consultez les articles suivants :

- [Déployer des modèles avec REST (préversion)](how-to-deploy-with-rest.md)
- [Créer et utiliser des points de terminaison en ligne managés (préversion) dans le studio](how-to-use-managed-online-endpoint-studio.md)
- [Déploiement sûr pour les points de terminaison en ligne (préversion)](how-to-safely-rollout-managed-endpoints.md)
- [Comment mettre à l’échelle automatiquement des points de terminaison en ligne gérés](how-to-autoscale-endpoints.md)
- [Utiliser des points de terminaison de traitement de lots (préversion) pour le scoring par lots](how-to-use-batch-endpoint.md)
- [Afficher les coûts d’un point de terminaison en ligne managé Azure Machine Learning (préversion)](how-to-view-online-endpoints-costs.md)
- [Accéder aux ressources Azure avec un point de terminaison en ligne managé et une identité managée (préversion)](how-to-access-resources-from-endpoints-managed-identities.md)
- [Résolution des problèmes de déploiement de points de terminaison en ligne managés](how-to-troubleshoot-online-endpoints.md)
