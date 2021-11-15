---
title: Créer des clusters de calcul
titleSuffix: Azure Machine Learning
description: Découvrez comment créer des clusters de calcul dans votre espace de travail Azure Machine Learning. Utilisez le cluster de calcul en tant que cible de calcul pour l’entraînement ou l’inférence.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.custom: devx-track-azurecli
ms.author: sgilley
author: sdgilley
ms.reviewer: sgilley
ms.date: 07/09/2021
ms.openlocfilehash: e658a8ed30b15327a68ce1671c19e55253ad6b06
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2021
ms.locfileid: "132026707"
---
# <a name="create-an-azure-machine-learning-compute-cluster"></a>Créer un cluster de calcul Azure Machine Learning

Découvrez comment créer et gérer un [cluster de calcul](concept-compute-target.md#azure-machine-learning-compute-managed) dans votre espace de travail Azure Machine Learning.

Vous pouvez utiliser un cluster de calcul Azure Machine Learning pour distribuer un processus d’entraînement ou d’inférence en lots sur un cluster de nœuds de calcul de processeur ou de GPU dans le cloud. Pour plus d’informations sur les tailles de machine virtuelle qui incluent des GPU, consultez [Tailles de machine virtuelle à GPU optimisé](../virtual-machines/sizes-gpu.md). 

Dans cet article, découvrez comment :

* Créer un cluster de calcul
*  Réduire le coût de votre cluster de calcul
* Configurer une [identité managée](../active-directory/managed-identities-azure-resources/overview.md) pour le cluster

## <a name="prerequisites"></a>Prérequis

* Un espace de travail Azure Machine Learning. Pour plus d’informations, voir la page [Créer un espace de travail Azure Machine Learning](how-to-manage-workspace.md).

* L’[extension Azure CLI pour Machine Learning service](reference-azure-machine-learning-cli.md), le [SDK Azure Machine Learning pour Python](/python/api/overview/azure/ml/intro) ou l’[extension Azure Machine Learning pour Visual Studio Code](how-to-setup-vs-code.md).

* Si vous utilisez le SDK Python, [configurez votre environnement de développement avec un espace de travail](how-to-configure-environment.md).  Une fois votre environnement configuré, attachez-le à l’espace de travail dans votre script Python :

    ```python
    from azureml.core import Workspace
    
    ws = Workspace.from_config() 
    ```

## <a name="what-is-a-compute-cluster"></a>Qu’est-ce qu’un cluster de calcul ?

Le cluster de calcul Azure Machine Learning est une infrastructure de capacité de calcul managée qui vous permet de créer facilement une capacité de calcul à un ou plusieurs nœuds. Le cluster de calcul est une ressource qui peut être partagée avec d’autres utilisateurs dans votre espace de travail. La cible de calcul monte en puissance automatiquement quand un travail est soumis, et peut être placée dans un réseau virtuel Azure. Le cluster de calcul ne prend en charge aucun déploiement d’**IP public (préversion)** ou dans un réseau virtuel. La cible de calcul s’exécute dans un environnement conteneurisé et empaquète les dépendances de votre modèle dans un [conteneur Docker](https://www.docker.com/why-docker).

Les clusters de calcul peuvent exécuter des travaux de manière sécurisée dans un [environnement de réseau virtuel](how-to-secure-training-vnet.md), sans qu’il soit nécessaire pour les entreprises d’ouvrir des ports SSH. Le travail s’exécute dans un environnement conteneurisé et empaquette les dépendances de votre modèle dans un conteneur Docker. 

## <a name="limitations"></a>Limites

* Certains des scénarios présentés dans ce document présentent la mention __préversion__. Les fonctionnalités en préversion sont fournies sans contrat de niveau de service et ne sont pas recommandées pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

* Les clusters de calcul peuvent être créés dans une région différente de celle de votre espace de travail. Cette fonctionnalité est en __préversion__ et n’est disponible que pour les __clusters de calcul__ et pas les instances de calcul. Cette préversion n’est pas disponible si vous utilisez un espace de travail prenant en charge les points de terminaison privés. 

    > [!WARNING]
    > Lorsque vous utilisez un cluster de calcul dans une région différente de celle de votre espace de travail ou de vos magasins de données, vous pouvez constater des coûts accrus de latence du réseau et de transfert de données. La latence et les coûts peuvent survenir lors de la création du cluster et lors de l’exécution de travaux sur celui-ci.

* Nous prenons actuellement en charge uniquement la création (et non la mise à jour) de clusters via des [modèles ARM](/azure/templates/microsoft.machinelearningservices/workspaces/computes). Pour mettre à jour le calcul, nous vous recommandons d’utiliser le Kit de développement logiciel (SDK), Azure CLI ou UX pour le moment.

* Capacité de calcul Azure Machine Learning comporte des limites par défaut, par exemple le nombre de cœurs qui peuvent être alloués. Pour plus d’informations, consultez [Gérer et demander des quotas pour les ressources Azure](how-to-manage-quotas.md).

* Azure vous permet de placer des _verrous_ sur les ressources, afin qu’elles ne puissent pas être supprimées ou restent en lecture seule. __N’appliquez pas de verrous aux ressources du groupe de ressources qui contient votre espace de travail__. L’application d’un verrou au groupe de ressources contenant votre espace de travail empêchera les opérations de mise à l’échelle pour les clusters de calcul Azure ML. Pour plus d’informations, consultez [Verrouiller les ressources pour empêcher les modifications inattendues](../azure-resource-manager/management/lock-resources.md).

> [!TIP]
> Les clusters peuvent généralement effectuer un scale-up jusqu’à 100 nœuds tant que vous disposez d’un quota suffisant pour le nombre de cœurs requis. Par défaut, les clusters sont configurés de manière à permettre la communication entre les nœuds du cluster, pour prendre en charge les travaux MPI par exemple. Toutefois, vous pouvez mettre à l’échelle vos clusters sur des milliers de nœuds simplement en [soumettant un ticket de support](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) et en demandant à ajouter votre abonnement, un espace de travail ou un cluster spécifique à une liste verte pour désactiver la communication entre nœuds.


## <a name="create"></a>Créer

**Durée estimée** : 5 minutes environ.

Une Capacité de calcul Azure Machine Learning peut être réutilisée pour plusieurs exécutions. Le calcul peut être partagé avec d’autres utilisateurs dans l’espace de travail et est conservé entre les exécutions, ce qui permet d’augmenter ou de diminuer automatiquement le nombre de nœuds en fonction du nombre d’exécutions soumises et du paramètre max_nodes défini sur votre cluster. Le paramètre min_nodes contrôle le nombre minimal de nœuds disponibles.

Le quota de cœurs dédiés par région par famille de machine virtuelle et le quota régional total, qui s’appliquent à la création d’un cluster de calcul, sont unifiés et partagés avec le quota d’instances de calcul d’entraînement Azure Machine Learning. 

[!INCLUDE [min-nodes-note](../../includes/machine-learning-min-nodes.md)]

La capacité de calcul diminue en puissance, et passe automatiquement à zéro nœud quand elle n’est pas utilisée.   Des machines virtuelles dédiées sont créées pour exécuter vos travaux en fonction des besoins.
    
# <a name="python"></a>[Python](#tab/python)


Pour créer une ressource de capacité de calcul Azure Machine Learning persistante dans Python, spécifiez les propriétés **vm_size** et **max_nodes**. Azure Machine Learning utilise ensuite des valeurs calculées par défaut pour les autres propriétés.
    
* **vm_size** : famille de machines virtuelles des nœuds créés par Capacité de calcul Azure Machine Learning.
* **max_nodes** : nombre maximal de nœuds pour la mise à l’échelle automatique lors de l’exécution d’un travail sur une capacité de calcul Azure Machine Learning.

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=cpu_cluster)]

Vous pouvez aussi configurer plusieurs propriétés avancées lors de la création d’une capacité de calcul Azure Machine Learning. Ces propriétés vous permettent de créer un cluster persistant de taille fixe, ou au sein d’un réseau virtuel Azure existant dans votre abonnement.  Pour plus de détails, voir la [classe AmlCompute](/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute).

> [!WARNING]
> Lorsque vous définissez le paramètre `location`, s’il s’agit d’une région différente de celle de votre espace de travail ou de vos magasins de données, vous pouvez constater des coûts accrus de latence du réseau et de transfert de données. La latence et les coûts peuvent survenir lors de la création du cluster et lors de l’exécution de travaux sur celui-ci.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)


```azurecli-interactive
az ml computetarget create amlcompute -n cpu --min-nodes 1 --max-nodes 1 -s STANDARD_D3_V2 --location westus2
```

> [!WARNING]
> Lorsque vous utilisez un cluster de calcul dans une région différente de celle de votre espace de travail ou de vos magasins de données, vous pouvez constater des coûts accrus de latence du réseau et de transfert de données. La latence et les coûts peuvent survenir lors de la création du cluster et lors de l’exécution de travaux sur celui-ci.

Pour plus d’informations, consultez [aaz ml computetarget create amlcompute](/cli/azure/ml(v1)/computetarget/create#az_ml_computetarget_create_amlcompute).

# <a name="studio"></a>[Studio](#tab/azure-studio)

Pour plus d’informations sur la création d’un cluster de calcul dans le studio, consultez [Créer des cibles de calcul dans le studio Azure Machine Learning](how-to-create-attach-compute-studio.md#amlcompute).

---

 ## <a name="lower-your-compute-cluster-cost"></a><a id="low-pri-vm"></a> Réduire le coût de votre cluster de calcul

Vous pouvez également choisir d’utiliser [des machines virtuelles de faible priorité](how-to-manage-optimize-cost.md#low-pri-vm) pour exécuter une partie ou la totalité de vos charges de travail. Ces machines virtuelles n’ont pas de disponibilité garantie et peuvent être anticipées en cours d’utilisation. Vous devrez redémarrer une tâche anticipée. 

Utilisez l’une des méthodes suivantes pour spécifier une machine virtuelle de faible priorité :
    
# <a name="python"></a>[Python](#tab/python)
    
```python
compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                            vm_priority='lowpriority',
                                                            max_nodes=4)
```
    
# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Définissez `vm-priority` :
    
```azurecli-interactive
az ml computetarget create amlcompute --name lowpriocluster --vm-size Standard_NC6 --max-nodes 5 --vm-priority lowpriority
```

# <a name="studio"></a>[Studio](#tab/azure-studio)

Dans Studio, choisissez **Basse priorité** lorsque vous créez une machine virtuelle.

--- 

## <a name="set-up-managed-identity"></a><a id="managed-identity"></a>Configurer une identité managée

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-intro.md)]

# <a name="python"></a>[Python](#tab/python)

* Configurez l’identité managée dans votre configuration de provisionnement :  

    * Identité managée affectée par le système créée dans un espace de travail nommé `ws`
        ```python
        # configure cluster with a system-assigned managed identity
        compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                                max_nodes=5,
                                                                identity_type="SystemAssigned",
                                                                )
        cpu_cluster_name = "cpu-cluster"
        cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)
        ```
    
    * Identité managée affectée par l’utilisateur créée dans un espace de travail nommé `ws`
    
        ```python
        # configure cluster with a user-assigned managed identity
        compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                                max_nodes=5,
                                                                identity_type="UserAssigned",
                                                                identity_id=['/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'])
    
        cpu_cluster_name = "cpu-cluster"
        cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)
        ```

* Ajoutez une identité managée à un cluster de calcul existant nommé `cpu_cluster`
    
    * Identité managée affectée par le système :
    
        ```python
        # add a system-assigned managed identity
        cpu_cluster.add_identity(identity_type="SystemAssigned")
        ````
    
    * Identité managée affectée par l’utilisateur :
    
        ```python
        # add a user-assigned managed identity
        cpu_cluster.add_identity(identity_type="UserAssigned", 
                                    identity_id=['/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'])
        ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

* Créer un cluster de calcul géré avec une identité managée

  * Identité managée affectée par l’utilisateur

    ```azurecli
    az ml computetarget create amlcompute --name cpu-cluster --vm-size Standard_NC6 --max-nodes 5 --assign-identity '/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'
    ```

  * Identité managée affectée par le système

    ```azurecli
    az ml computetarget create amlcompute --name cpu-cluster --vm-size Standard_NC6 --max-nodes 5 --assign-identity '[system]'
    ```
* Ajouter une identité managée à un cluster existant :

    * Identité managée affectée par l’utilisateur
        ```azurecli
        az ml computetarget amlcompute identity assign --name cpu-cluster '/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'
        ```
    * Identité managée affectée par le système

        ```azurecli
        az ml computetarget amlcompute identity assign --name cpu-cluster '[system]'
        ```

# <a name="studio"></a>[Studio](#tab/azure-studio)

Consultez [Configurer une identité managée dans le studio](how-to-create-attach-compute-studio.md#managed-identity).

---

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-note.md)]

### <a name="managed-identity-usage"></a>Utilisation de l’identité managée

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-default.md)]

## <a name="troubleshooting"></a>Résolution des problèmes

Il peut arriver que des utilisateurs ayant créé leur espace de travail Azure Machine Learning sur le portail Azure avant la disponibilité générale ne puissent pas créer de capacité AmlCompute dans cet espace de travail. Vous pouvez créer une demande de support auprès du service ou créer un espace de travail sur le Portail ou avec le Kit de développement logiciel (SDK) pour vous débloquer sans délai.

Si votre cluster de calcul Azure Machine Learning semble bloqué au niveau du redimensionnement (0 -> 0) pour l’état du nœud, cela peut être dû à des verrous de ressources Azure.

[!INCLUDE [resource locks](../../includes/machine-learning-resource-lock.md)]

## <a name="next-steps"></a>Étapes suivantes

Utilisez votre cluster de calcul pour :

* [Soumettre une exécution d’entraînement](how-to-set-up-training-targets.md) 
* [Exécuter une inférence par lots](./tutorial-pipeline-batch-scoring-classification.md)
