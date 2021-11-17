---
title: Accéder aux ressources Azure à partir d’un point de terminaison géré
titleSuffix: Azure Machine Learning
description: Bénéficiez d’un accès sécurisé aux ressources Azure pour votre modèle de déploiement Machine Learning à partir d’un point de terminaison en ligne géré avec une identité managée affectée par le système ou affectée par l’utilisateur.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: seramasu
ms.reviewer: laobri
author: rsethur
ms.date: 10/21/2021
ms.topic: how-to
ms.custom: devplatv2
ms.openlocfilehash: ff8778dfa69552deaf26dd13438ac73fed260658
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131566780"
---
# <a name="access-azure-resources-from-a-managed-online-endpoint-preview-with-a-managed-identity"></a>Accéder aux ressources Azure à partir d’un point de terminaison en ligne géré avec une identité managée (préversion) 

Découvrez comment accéder aux ressources Azure à partir de votre script de scoring avec un point de terminaison en ligne géré, ainsi qu’avec une identité managée affectée par le système ou une identité managée affectée par l’utilisateur. 

Les points de terminaison gérés (préversion) permettent à Azure Machine Learning de prendre en charge le provisionnement de vos ressources de calcul et le déploiement de votre modèle Machine Learning. En général, votre modèle doit accéder aux ressources Azure, comme Azure Container Registry ou votre stockage Blob pour l’inférence. Avec une identité managée, vous pouvez accéder à ces ressources sans avoir à gérer les informations d’identification dans votre code. [En savoir plus sur les identités managées](../active-directory/managed-identities-azure-resources/overview.md).

Ce guide part du principe que vous ne disposez pas d’une identité gérée, d’un compte de stockage ni d’un point de terminaison en ligne géré. Si vous disposez déjà de ces composants, passez à la section [Accorder l’autorisation d’accès à l’identité gérée](#give-access-permission-to-the-managed-identity). 

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="prerequisites"></a>Prérequis

* Pour utiliser Azure Machine Learning, vous devez disposer d’un abonnement Azure. Si vous n’avez pas d’abonnement Azure, créez un compte gratuit avant de commencer. Essayez la [version gratuite ou payante d’Azure Machine Learning](https://azure.microsoft.com/free/) dès aujourd’hui.

* Installez et configurez Azure CLI et l’extension ML. Pour plus d’informations, consultez [Installer, configurer et utiliser l’interface CLI 2.0 (préversion)](how-to-configure-cli.md).

* Un groupe de ressources Azure dans lequel vous (ou le principal de service que vous utilisez) devez disposer d’un accès `User Access Administrator` et `Contributor`. Vous disposez d’un tel groupe de ressources si vous avez configuré votre extension ML conformément à l’article ci-dessus.

* Un espace de travail Azure Machine Learning. Vous disposez d’un espace de travail si vous avez configuré votre extension ML conformément à l’article ci-dessus.

* Un modèle Machine Learning entraîné prêt pour le scoring et le déploiement. Si vous suivez la procédure décrite dans l’article, un modèle est fourni.

* Si vous n’avez pas encore défini les paramètres par défaut pour l’interface CLI Azure, enregistrez vos paramètres par défaut. Pour éviter de transmettre plusieurs fois les valeurs de votre abonnement, de votre espace de travail et de votre groupe de ressources, exécutez le code suivant :

   ```azurecli
   az account set --subscription <subscription ID>
   az configure --defaults workspace=<Azure Machine Learning workspace name> group=<resource group>
   ```

* Pour suivre l’exemple, cloner le référentiel d’exemples

    ```azurecli
    git clone https://github.com/Azure/azureml-examples --depth 1
    cd azureml-examples/cli
    ```

## <a name="define-configuration-yaml-file-for-deployment"></a>Définir le fichier YAML de configuration pour votre déploiement

Pour déployer un point de terminaison géré avec l’interface CLI, vous devez définir la configuration dans un fichier YAML. Pour plus d’informations sur le schéma YAML, consultez le document [Référence YAML sur les points de terminaison en ligne](reference-yaml-endpoint-managed-online.md).

Les fichiers YAML dans les exemples suivants sont utilisés pour créer des points de terminaison en ligne. 

# <a name="system-assigned-managed-identity"></a>[Identité managée affectée par le système](#tab/system-identity)

L’exemple YAML suivant se trouve à l’emplacement `endpoints/online/managed/managed-identities/1-sai-create-endpoint`. Le fichier, 

* Définit le nom par lequel vous souhaitez faire référence au point de terminaison, `my-sai-endpoint`.
* Spécifie le type d’autorisation à utiliser pour accéder au point de terminaison, `auth-mode: key`.

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/endpoints/online/managed/managed-identities/1-sai-create-endpoint.yml":::

Cet exemple YAML, `2-sai-deployment.yml`,

* Spécifie que le type de point de terminaison que vous souhaitez créer est un point de terminaison `online`.
* Indique que le point de terminaison est associé à un déploiement nommé `blue`.
* Configure les détails du déploiement, tels que le modèle à déployer ainsi que l’environnement et le script de scoring à utiliser.

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/endpoints/online/managed/managed-identities/2-sai-deployment.yml":::

# <a name="user-assigned-managed-identity"></a>[Identité managée affectée par l’utilisateur](#tab/user-identity)

L’exemple YAML suivant se trouve à l’emplacement `endpoints/online/managed/managed-identities/1-uai-create-endpoint`. Le fichier, 

* Définit le nom par lequel vous souhaitez faire référence au point de terminaison, `my-uai-endpoint`.
* Spécifie le type d’autorisation à utiliser pour accéder au point de terminaison, `auth-mode: key`.
* Indique le type d’identité à utiliser, `type: user_assigned`

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/endpoints/online/managed/managed-identities/1-uai-create-endpoint.yml":::

Cet exemple YAML, `2-sai-deployment.yml`,

* Spécifie que le type de point de terminaison que vous souhaitez créer est un point de terminaison `online`.
* Indique que le point de terminaison est associé à un déploiement nommé `blue`.
* Configure les détails du déploiement, tels que le modèle à déployer ainsi que l’environnement et le script de scoring à utiliser.

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/endpoints/online/managed/managed-identities/2-uai-deployment.yml":::

---

## <a name="configure-variables-for-deployment"></a>Configurer des variables pour le déploiement

Configurez les noms de variables pour l’espace de travail, l’emplacement de l’espace de travail et le point de terminaison que vous souhaitez créer pour les utiliser dans votre déploiement.

# <a name="system-assigned-managed-identity"></a>[Identité managée affectée par le système](#tab/system-identity)

Le code suivant exporte ces valeurs en tant que variables d’environnement dans votre point de terminaison :

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="set_variables" :::

Ensuite, spécifiez les noms que vous voulez donner à vos compte Stockage Blob, conteneur d’objets blob et fichier. Ces noms de variables sont définis ici et sont référencés dans les commandes `az storage account create` et `az storage container create` dans la section suivante.

Le code suivant exporte ces valeurs en tant que variables d’environnement :

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="configure_storage_names" :::

Une fois ces variables exportées, créez un fichier texte localement. Quand le point de terminaison est déployé, le script de scoring accède à ce fichier texte à l’aide de l’identité managée affectée par le système qui est générée lors de la création du point de terminaison.

# <a name="user-assigned-managed-identity"></a>[Identité managée affectée par l’utilisateur](#tab/user-identity)

Choisissez le nom du point de terminaison, de l’espace de travail, de l’emplacement de l’espace de travail et exportez cette valeur en tant que variable d’environnement :

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-uai.sh" id="set_variables" :::

Ensuite, spécifiez les noms que vous voulez donner à vos compte Stockage Blob, conteneur d’objets blob et fichier. Ces noms de variables sont définis ici et sont référencés dans les commandes `az storage account create` et `az storage container create` dans la section suivante.

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-uai.sh" id="configure_storage_names" :::

Une fois ces variables exportées, créez un fichier texte localement. Quand le point de terminaison est déployé, le script de scoring accède à ce fichier texte à l’aide de l’identité managée affectée par l’utilisateur utilisée dans le point de terminaison. 

Choisissez le nom de l’identité de l’utilisateur et exportez cette valeur en tant que variable d’environnement :

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-uai.sh" id="set_user_identity_name" :::

---

## <a name="create-the-managed-identity"></a>Créer l’identité managée 
Pour accéder aux ressources Azure, créez une identité managée affectée par l’utilisateur ou affectée par le système. 

# <a name="system-assigned-managed-identity"></a>[Identité managée affectée par le système](#tab/system-identity)

Quand vous [créez un point de terminaison en ligne](#create-a-managed-online-endpoint), une identité managée affectée par le système est générée automatiquement pour vous, il n’est donc pas nécessaire d’en créer une distincte. 

# <a name="user-assigned-managed-identity"></a>[Identité managée affectée par l’utilisateur](#tab/user-identity)

Pour créer une identité managée affectée par l’utilisateur, utilisez les éléments suivants :

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-uai.sh" id="create_user_identity" :::

---

## <a name="create-storage-account-and-container"></a>Créer un compte de stockage et un conteneur

Pour cet exemple, créez un compte Stockage Blob et un conteneur d’objets blob, puis charger le fichier texte créé auparavant dans le conteneur d’objets blob. Il s’agit du compte de stockage et du conteneur d’objets blob que vous allez autoriser à accéder au point de terminaison et à l’identité managée. 

# <a name="system-assigned-managed-identity"></a>[Identité managée affectée par le système](#tab/system-identity)

En premier lieu, créez un compte de stockage.

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="create_storage_account" :::

Ensuite, créez le conteneur d’objets blob dans le compte de stockage.

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="create_storage_container" :::

Chargez alors votre fichier texte dans le conteneur d’objets blob.

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="upload_file_to_storage" :::

# <a name="user-assigned-managed-identity"></a>[Identité managée affectée par l’utilisateur](#tab/user-identity)

En premier lieu, créez un compte de stockage.  

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-uai.sh" id="create_storage_account" :::

Vous pouvez également récupérer un ID de compte de stockage existant avec les éléments suivants. 

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-uai.sh" id="get_storage_account_id" :::

Ensuite, créez le conteneur d’objets blob dans le compte de stockage. 

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-uai.sh" id="create_storage_container" :::

Chargez ensuite le fichier dans le conteneur. 

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-uai.sh" id="upload_file_to_storage" :::

---

## <a name="create-a-managed-online-endpoint"></a>Créer un point de terminaison en ligne managé

Le code suivant crée un point de terminaison en ligne managé sans spécifier de déploiement. 

# <a name="system-assigned-managed-identity"></a>[Identité managée affectée par le système](#tab/system-identity)
Lorsque vous créez un point de terminaison managé, une identité managée affectée par le système est créée par défaut pour le point de terminaison.

>[!IMPORTANT]
> Les identités managées affectées par le système sont immuables et ne peuvent pas être modifiées une fois créées.

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="create_endpoint" :::

Vérifiez l’état du point de terminaison avec les éléments suivants.

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="check_endpoint_Status" :::

En cas de problème, consultez [Résolution des problèmes de déploiement et de scoring de points de terminaison en ligne managés (préversion)](how-to-troubleshoot-managed-online-endpoints.md).

# <a name="user-assigned-managed-identity"></a>[Identité managée affectée par l’utilisateur](#tab/user-identity)

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-uai.sh" id="create_endpoint" :::

Vérifiez l’état du point de terminaison avec les éléments suivants.

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-uai.sh" id="check_endpoint_Status" :::

En cas de problème, consultez [Résolution des problèmes de déploiement et de scoring de points de terminaison en ligne managés (préversion)](how-to-troubleshoot-managed-online-endpoints.md).

---

## <a name="give-access-permission-to-the-managed-identity"></a>Accorder l’autorisation d’accès à l’identité managée

>[!IMPORTANT] 
> Les points de terminaison en ligne nécessitent l’autorisation de tirage (pull) Azure Container Registry, l’autorisation AcrPull, sur le registre de conteneurs et l’autorisation Lecteur des données Storage Blob sur le magasin de données par défaut de l’espace de travail.

Vous pouvez permettre au point de terminaison géré d’accéder à votre stockage via son identité managée affectée par le système ou permettre à l’identité managée affectée par l’utilisateur d’accéder au compte de stockage créé dans la section précédente.

# <a name="system-assigned-managed-identity"></a>[Identité managée affectée par le système](#tab/system-identity)

Récupérez l’identité managée affectée par le système qui a été créée pour votre point de terminaison.

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="get_system_identity" :::

À partir de là, vous pouvez accorder à l’identité managée affectée par le système l’autorisation d’accéder à votre stockage.

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="give_permission_to_user_storage_account" :::

# <a name="user-assigned-managed-identity"></a>[Identité managée affectée par l’utilisateur](#tab/user-identity)

Récupérez l’ID client de l’identité managée affectée par l’utilisateur.

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-uai.sh" id="get_user_identity_client_id" :::

Récupérez l’ID de l’identité managée affectée par l’utilisateur.

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-uai.sh" id="get_user_identity_id" :::

Obtient le registre de conteneurs associé à l’espace de travail.

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-uai.sh" id="get_container_registry_id" :::

Récupérez le stockage par défaut de l’espace de travail.

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-uai.sh" id="get_workspace_storage_id" :::

Accordez l’autorisation de compte de stockage à identité gérée affectée à l’utilisateur.  

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-uai.sh" id="give_permission_to_user_storage_account" :::

Accordez au registre de conteneurs l’autorisation d’identité managée affectée par l’utilisateur.

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-uai.sh" id="give_permission_to_container_registry" :::

Accordez au stockage d’espace de travail par défaut l’autorisation d’identité managée affectée par l’utilisateur.

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-uai.sh" id="give_permission_to_workspace_storage_account" :::

---

## <a name="scoring-script-to-access-azure-resource"></a>Script de scoring pour accéder à la ressource Azure

Reportez-vous au script suivant pour comprendre comment utiliser votre jeton d’identité pour accéder aux ressources Azure. Dans ce scénario, il s’agit du compte de stockage créé dans les sections précédentes. 

:::code language="python" source="~/azureml-examples-cli-preview/cli/endpoints/online/model-1/onlinescoring/score_managedidentity.py":::

## <a name="create-a-deployment-with-your-configuration"></a>Créer un déploiement avec votre configuration

Créez un déploiement associé au point de terminaison managé. [En savoir plus sur le déploiement sur des points de terminaison en ligne gérés](how-to-deploy-managed-online-endpoints.md).

>[!WARNING]
> Ce déploiement peut prendre approximativement entre 8 et 14 minutes selon que l’environnement/l’image sous-jacent est généré pour la première fois ou non. Les déploiements ultérieurs avec le même environnement seront plus rapides.

# <a name="system-assigned-managed-identity"></a>[Identité managée affectée par le système](#tab/system-identity)

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="deploy" :::

>[!NOTE]
> La valeur de l’argument `--name` peut écraser la clé `name` dans le fichier YAML.

Vérifiez l’état du déploiement.

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="check_deploy_Status" :::

Pour affiner la requête ci-dessus afin de retourner uniquement des données spécifiques, consultez [Interroger la sortie de commande Azure CLI](/cli/azure/query-azure-cli).

> [!NOTE]
> La méthode init du script de scoring lit le fichier à partir de votre compte de stockage à l’aide du jeton d’identité managée affectée par le système.

Pour vérifier la sortie de la méthode init, consultez le journal de déploiement avec le code suivant. 

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="check_deployment_log" :::


# <a name="user-assigned-managed-identity"></a>[Identité managée affectée par l’utilisateur](#tab/user-identity)

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-uai.sh" id="create_endpoint" :::

>[!Note]
> La valeur de l’argument `--name` peut écraser la clé `name` dans le fichier YAML.

Une fois que la commande s’exécute, vous pouvez vérifier l’état du déploiement.

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-uai.sh" id="check_endpoint_Status" :::

Pour affiner la requête ci-dessus afin de retourner uniquement des données spécifiques, consultez [Interroger la sortie de commande Azure CLI](/cli/azure/query-azure-cli).

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-uai.sh" id="check_deployment_log" :::

> [!NOTE]
> La méthode init du script de scoring lit le fichier à partir de votre compte de stockage à l’aide du jeton d’identité managée affectée par le système.

Pour vérifier la sortie de la méthode init, consultez le journal de déploiement avec le code suivant. 

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-uai.sh" id="check_deployment_log" :::

---

Une fois votre déploiement effectué, le modèle, l’environnement et le point de terminaison sont inscrits auprès de votre espace de travail Azure Machine Learning.

## <a name="confirm-your-endpoint-deployed-successfully"></a>Confirmer le déploiement correct de votre point de terminaison

Une fois votre point de terminaison déployé, confirmez son fonctionnement. Les détails de l’inférence varient d’un modèle à l’autre. Pour ce guide, les paramètres de requête JSON se présentent comme suit : 

:::code language="json" source="~/azureml-examples-cli-preview/cli/endpoints/online/model-1/sample-request.json" :::

Pour appeler votre point de terminaison, exécutez :

# <a name="system-assigned-managed-identity"></a>[Identité managée affectée par le système](#tab/system-identity)

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="test_endpoint" :::

# <a name="user-assigned-managed-identity"></a>[Identité managée affectée par l’utilisateur](#tab/user-identity)

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-uai.sh" id="test_endpoint" :::

---

## <a name="delete-the-endpoint-and-storage-account"></a>Supprimer le point de terminaison et le compte de stockage

Si vous n’envisagez pas de continuer à utiliser le point de terminaison déployé et le stockage, supprimez-les pour réduire les coûts. Lorsque vous supprimez le point de terminaison, tous ses déploiements associés sont également supprimés.

# <a name="system-assigned-managed-identity"></a>[Identité managée affectée par le système](#tab/system-identity)
 
::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="delete_endpoint" :::
::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="delete_storage_account" :::

# <a name="user-assigned-managed-identity"></a>[Identité managée affectée par l’utilisateur](#tab/user-identity)

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-uai.sh" id="delete_endpoint" :::
::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-uai.sh" id="delete_storage_account" :::
::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-uai.sh" id="delete_user_identity" :::

---

## <a name="next-steps"></a>Étapes suivantes

* [Déployez et effectuez le scoring d’un modèle d’apprentissage automatique en utilisant un point de terminaison en ligne géré (préversion)](how-to-deploy-managed-online-endpoints.md).
* Pour plus d’informations sur les déploiements, consultez [Déploiement sécurisé pour les points de terminaison en ligne (préversion)](how-to-safely-rollout-managed-endpoints.md).
* Pour plus d’informations sur l’utilisation de l’interface CLI, consultez [Utiliser l’extension CLI pour Azure Machine Learning](reference-azure-machine-learning-cli.md).
* Pour connaître les ressources de calcul que vous pouvez utiliser, consultez [Liste des références SKU pour les points de terminaison en ligne managés (préversion)](reference-managed-online-endpoints-vm-sku-list.md).
* Pour plus d’informations sur les coûts, consultez [Afficher les coûts d’un point de terminaison en ligne managé Azure Machine Learning (préversion)](how-to-view-online-endpoints-costs.md).
* Pour plus d’informations sur la supervision des points de terminaison, consultez [Superviser des points de terminaison en ligne managés (préversion)](how-to-monitor-online-endpoints.md).
* Pour connaître les limitations concernant les points de terminaison gérés, consultez [Gérer et augmenter les quotas pour les ressources avec Azure Machine Learning](how-to-manage-quotas.md#azure-machine-learning-managed-online-endpoints-preview).
