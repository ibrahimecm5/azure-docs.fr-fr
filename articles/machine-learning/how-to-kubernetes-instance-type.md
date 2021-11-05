---
title: Créer et sélectionner des types d’instance Kubernetes (version préliminaire)
description: Créez et sélectionnez les types d’instances de cluster Kubernetes compatibles avec Azure Arc pour l’apprentissage et l’inférence des charges de travail dans Azure Machine Learning.
titleSuffix: Azure Machine Learning
author: luisquintanilla
ms.author: luquinta
ms.service: machine-learning
ms.subservice: mlops
ms.date: 10/21/2021
ms.topic: how-to
ms.custom: ignite-fall-2021
ms.openlocfilehash: 0bea2b0fe1b5d08f343fd31ffc14b9252039a49e
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131098158"
---
# <a name="create-and-select-kubernetes-instance-types-preview"></a>Créer et sélectionner des types d’instance Kubernetes (version préliminaire)

Découvrez comment créer et sélectionner des instances Kubernetes pour les charges de travail d’apprentissage et d’inférence Azure Machine Learning sur des clusters Kubernetes compatibles Azure Arc.

## <a name="what-are-instance-types"></a>Que sont les types d’instances ?

Les types d’instances sont un concept d’Azure Machine Learning qui permet de cibler certains types de nœuds de calcul pour les charges de travail d’apprentissage et d’inférence.  Pour une machine virtuelle Azure, un exemple de type d’instance est `STANDARD_D2_V3`.

Dans les clusters Kubernetes, les types d’instance sont définis par deux éléments :

* [nodeSelector](https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/#nodeselector) : `nodeSelector` vous permet de spécifier le nœud sur lequel un pod doit s’exécuter.  Le nœud doit avoir une étiquette correspondante.
* [ressources](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/) : la section `resources` définit les ressources de calcul (processeur, mémoire et GPU NVIDIA) du pod.

## <a name="create-instance-types"></a>Créer des types d’instance

Les types d’instances sont représentés dans une définition de ressource personnalisée (CRD) qui est installée avec l’extension Azure Machine Learning.  

### <a name="create-a-single-instance-type"></a>Créer un type d’instance unique

Pour créer un nouveau type d’instance, créez une ressource personnalisée pour le type d’instance CRD.  

Par exemple, prenons le CRD `my_instance_type.yaml` :

```yaml
apiVersion: amlarc.azureml.com/v1alpha1
kind: InstanceType
metadata:
  name: myinstancetypename
spec:
  nodeSelector:
    mylabel: mylabelvalue
  resources:
    limits:
      cpu: "1"
      nvidia.com/gpu: 1
      memory: "2Gi"
    requests:
      cpu: "700m"
      memory: "1500Mi"
```

Utilisez la commande `kubectl apply` pour créer un nouveau type d’instance.

```bash
kubectl apply -f my_instance_type.yaml
```

Cette opération crée un type d’instance avec les propriétés suivantes :

- Les pods sont planifiés uniquement sur les nœuds avec étiquette `mylabel: mylabelvalue`.
- Les demandes de ressources du processeur `700m` et de la mémoire `1500Mi` sont affectées aux pods. 
- Les ressources du processeur `1`, de la mémoire `2Gi` et du GPU NVIDIA `1` sont affectées aux pods.

> [!NOTE]
> Lorsque vous spécifiez votre CRD, prenez note des conventions suivantes :
> - Les ressources GPU NVIDIA sont uniquement spécifiées dans la section `limits`.  Pour plus d’informations, consultez la [documentation Kubernetes](https://kubernetes.io/docs/tasks/manage-gpus/scheduling-gpus/#using-device-plugins).
> - Les ressources processeur et mémoire sont des valeurs de chaîne.
> - Le processeur peut être spécifié dans millicœurs (`100m`) ou dans des nombres entiers (`"1"` ce qui équivaut à `1000m`).
> - La mémoire peut être spécifiée sous la forme d’un nombre entier + suffixe (`1024Mi` pour `1024 MiB`).

### <a name="create-multiple-instance-types"></a>Créer plusieurs types d’instances

Vous pouvez également utiliser les CRD pour créer plusieurs types d’instance à la fois.

Par exemple, prenons le CRD `my_instance_type.yaml` :

```yaml
apiVersion: amlarc.azureml.com/v1alpha1
kind: InstanceTypeList
items:
  - metadata:
      name: cpusmall
    spec:
      resources:
        limits:
          cpu: "100m"
          nvidia.com/gpu: 0
          memory: "10Mi"
        requests:
          cpu: "100m"
          memory: "1Gi"

  - metadata:
      name: defaultinstancetype
    spec:
      resources:
        limits:
          cpu: "1"
          nvidia.com/gpu: 0
          memory: "1Gi"
        requests:
          cpu: "1"
          memory: "1Gi" 
```

Utilisez la commande `kubectl apply` pour créer plusieurs types d’instance.

```bash
kubectl apply -f my_instance_type.yaml
``` 

Cette opération crée deux types d’instances, l’un nommé `defaultinstancetype` et l’autre `cpusmall` avec des spécifications de ressources différentes. Pour plus d’informations sur les types d’instance par défaut, consultez la section [types d’instance par défaut](#default-instance-types) de ce document.

## <a name="default-instance-types"></a>Types d’instance par défaut

Lorsqu’une charge de travail d’apprentissage ou d’inférence est soumise sans type d’instance, elle utilise le type d’instance par défaut.  

Pour spécifier un type d’instance par défaut pour un cluster Kubernetes, créez un type d’instance avec le nom `defaultinstancetype` et définissez les propriétés `nodeSelector` et respectives `resources` comme n’importe quel autre type d’instance.  Le type d’instance est automatiquement reconnu comme étant la valeur par défaut.

Si aucun type d’instance par défaut n’est défini, le comportement par défaut suivant s’applique :

* Aucun nodeSelector n’est appliqué, ce qui signifie que le pod peut être planifié sur n’importe quel nœud.
* Les ressources par défaut sont affectées aux pod de la charge de travail :

    ```yaml
    resources:
        limits:
        cpu: "0.6"
        memory: "1536Mi"
        requests:
        cpu: "0.6"
        memory: "1536Mi"
    ```

> [!IMPORTANT]
> Le type d’instance par défaut n’apparaît pas en tant que ressource personnalisée InstanceType dans le cluster, mais s’affiche dans tous les clients (Azure Machine Learning studio, Azure CLI, kit de développement logiciel (SDK) Python).

## <a name="select-an-instance-type-for-training-workloads"></a>Sélectionner un type d’instance pour les charges de travail d’apprentissage

Pour sélectionner un type d’instance pour un travail d’apprentissage à l’aide de Azure Machine Learning CLI 2.0, spécifiez son nom dans le cadre de la section `compute` du fichier de spécification YAML.  

```yaml
command: python -c "print('Hello world!')"
environment:
  docker:
    image: python
compute:
  target: azureml:<compute_target_name>
  instance_type: <instance_type_name>
```

Dans l’exemple ci-dessus, remplacez `<compute_target_name>` par le nom de votre cible de calcul Kubernetes et `<instance_type_name>` par le nom du type d’instance que vous souhaitez sélectionner.

> [!TIP]
> Le type d’instance par défaut utilise peu de ressources. Pour vous assurer que toutes les charges de travail de Machine Learning s’exécutent correctement avec les ressources adéquates, il est fortement recommandé de créer des types d’instances personnalisées.

## <a name="select-an-instance-type-for-inferencing-workloads"></a>Sélectionner un type d’instance pour les charges de travail d’inférence

Pour sélectionner un type d’instance pour les charges de travail d’inférence à l’aide de l’interface de commande Azure Machine Learning 2.0, spécifiez son nom dans le cadre de la section `deployments`.  Par exemple :

```yaml
type: online
auth_mode: key
target: azureml:<your compute target name>
traffic:
  blue: 100

deployments:
  - name: blue
    app_insights_enabled: true
    model: 
      name: sklearn_mnist_model
      version: 1
      local_path: ./model/sklearn_mnist_model.pkl
    code_configuration:
      code: 
        local_path: ./script/
      scoring_script: score.py
    instance_type: <instance_type_name>
    environment: 
      name: sklearn-mnist-env
      version: 1
      path: .
      conda_file: file:./model/conda.yml
      docker:
        image: mcr.microsoft.com/azureml/openmpi3.1.2-ubuntu18.04:20210727.v1
```

Dans l’exemple ci-dessus, remplacez `<instance_type_name>` par le nom du type d’instance que vous souhaitez sélectionner.

## <a name="next-steps"></a>Étapes suivantes

- [Configuration du Machine Learning avec Azure Arc (préversion)](how-to-attach-arc-kubernetes.md)
- [Entraîner des modèles (créer des travaux) avec l’interface CLI (v2)](how-to-train-cli.md)
- [Déployer et noter un modèle Machine Learning en utilisant un point de terminaison en ligne (préversion)](how-to-deploy-managed-online-endpoints.md)
