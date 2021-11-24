---
title: Créer une expérience qui utilise une erreur AKS Chaos Mesh à l’aide d’Azure Chaos Studio avec Azure CLI
description: Créer une expérience qui utilise une erreur AKS Chaos Mesh avec Azure CLI
author: johnkemnetz
ms.topic: how-to
ms.date: 11/11/2021
ms.author: johnkem
ms.service: chaos-studio
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: c47a6fa58a9361dc9ab26e119346951e1eef9764
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132373517"
---
# <a name="create-a-chaos-experiment-that-uses-a-chaos-mesh-fault-with-the-azure-cli"></a>Créer une expérience de chaos qui utilise une erreur Chaos Mesh avec Azure CLI

Vous pouvez utiliser une expérience de chaos pour vérifier que votre application résiste aux défaillances en les provoquant dans un environnement contrôlé. Dans ce guide, vous allez provoquer des défaillances périodiques du pod Azure Kubernetes Service sur un espace de noms à l’aide d’une expérience de chaos et d’Azure Chaos Studio. L’exécution de cette expérience peut vous aider à vous défendre contre l’indisponibilité du service en cas de défaillances sporadiques.

Azure Chaos Studio utilise la [Chaos Mesh](https://chaos-mesh.org/), une plateforme d’ingénierie chaos libre et open source pour Kubernetes qui permet d’injecter des erreurs dans un cluster AKS. Les erreurs Chaos Mesh sont des erreurs de [service-direct](chaos-studio-tutorial-aks-portal.md) qui nécessitent l’installation de Chaos Mesh sur le cluster AKS. Ces mêmes étapes peuvent être utilisées pour configurer et exécuter une expérience pour toute erreur AKS Chaos Mesh.

## <a name="prerequisites"></a>Prérequis

- Un abonnement Azure. [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 
- Un cluster AKS. Si vous ne disposez pas de cluster AKS, vous pouvez [suivre ces étapes pour en créer un](../aks/kubernetes-walkthrough-portal.md).

## <a name="launch-azure-cloud-shell"></a>Lancement d’Azure Cloud Shell

Azure Cloud Shell est un interpréteur de commandes interactif et gratuit que vous pouvez utiliser pour exécuter les étapes de cet article. Il contient des outils Azure courants préinstallés et configurés pour être utilisés avec votre compte. 

Pour ouvrir Cloud Shell, sélectionnez simplement **Essayer** en haut à droite d’un bloc de code. Vous pouvez également ouvrir Cloud Shell dans un onglet distinct du navigateur en accédant à [https://shell.azure.com/bash](https://shell.azure.com/bash). Sélectionnez **Copier** pour copier les blocs de code, collez-les dans Cloud Shell et sélectionnez **Entrée** pour les exécuter.

Si vous préférez installer et utiliser l’interface de ligne de commande localement, vous aurez besoin d’Azure CLI version 2.0.30 ou ultérieure pour ce tutoriel. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, voir [Installer Azure CLI]( /cli/azure/install-azure-cli).

## <a name="set-up-chaos-mesh-on-your-aks-cluster"></a>Configurer Chaos Mesh sur votre cluster AKS

Avant de pouvoir exécuter des erreurs Chaos Mesh dans Chaos Studio, vous devez installer Chaos Mesh sur votre cluster AKS.

1. Exécutez les commandes suivantes dans une fenêtre [Azure Cloud Shell](../cloud-shell/overview.md) dans laquelle l’abonnement actif est défini comme étant l’abonnement dans lequel votre cluster AKS est déployé. Remplacez `$RESOURCE_GROUP` et `$CLUSTER_NAME` par le groupe de ressources et le nom de votre ressource de cluster.

   ```azurecli-interactive
   az aks get-credentials -g $RESOURCE_GROUP -n $CLUSTER_NAME
   helm repo add chaos-mesh https://charts.chaos-mesh.org
   helm repo update
   kubectl create ns chaos-testing
   helm install chaos-mesh chaos-mesh/chaos-mesh --namespace=chaos-testing --version 2.0.3 --set chaosDaemon.runtime=containerd --set chaosDaemon.socketPath=/run/containerd/containerd.sock
   ```

2. Vérifiez que les pods Chaos Mesh sont installés en exécutant la commande suivante :

   ```azurecli-interactive
   kubectl get po -n chaos-testing
   ```

La sortie doit ressembler à ce qui suit (un chaos-controller-manager et un ou plusieurs chaos-daemons) :

```bash
NAME                                        READY   STATUS    RESTARTS   AGE
chaos-controller-manager-69fd5c46c8-xlqpc   1/1     Running   0          2d5h
chaos-daemon-jb8xh                          1/1     Running   0          2d5h
chaos-dashboard-98c4c5f97-tx5ds             1/1     Running   0          2d5h
```

Vous pouvez également [utiliser les instructions d’installation sur le site Web de Chaos Mesh](https://chaos-mesh.org/docs/production-installation-using-helm/).


## <a name="enable-chaos-studio-on-your-aks-cluster"></a>Activer Chaos Studio sur votre cluster AKS

Chaos Studio ne peut pas injecter d’erreurs sur une ressource, sauf si elle a d’abord été intégrée à Chaos Studio. Vous intégrez une ressource à Chaos Studio en créant une [cible et des fonctionnalités](chaos-studio-targets-capabilities.md) sur la ressource. Les clusters AKS ne possèdent qu’un seul type de cible (service-direct), mais d’autres ressources peuvent avoir jusqu’à deux types cibles, l’un pour les erreurs de service-direct et l’autre pour les erreurs basées sur l’agent. Chaque type d’erreur de Chaos Mesh est représenté sous la forme d’une fonctionnalité (PodChaos, NetworkChaos, IOChaos, etc.).

1. Créez une cible en remplaçant `$RESOURCE_ID` par l’ID de ressource du cluster AKS que vous intégrez :

    ```azurecli-interactive
    az rest --method put --url "https://management.azure.com/$RESOURCE_ID/providers/Microsoft.Chaos/targets/Microsoft-AzureKubernetesServiceChaosMesh?api-version=2021-09-15-preview" --body "{\"properties\":{}}"
    ```

2. Créez les capacités sur la cible en remplaçant `$RESOURCE_ID` par l’ID de ressource du cluster AKS que vous intégrez et `$CAPABILITY` par le [nom de la capacité d’erreur que vous activez](chaos-studio-fault-library.md).
    
    ```azurecli-interactive
    az rest --method put --url "https://management.azure.com/$RESOURCE_ID/providers/Microsoft.Chaos/targets/Microsoft-AzureKubernetesServiceChaosMesh/capabilities/$CAPABILITY?api-version=2021-09-15-preview"  --body "{\"properties\":{}}"
    ```

    Par exemple, si vous activez la capacité PodChaos :

    ```azurecli-interactive
    az rest --method put --url "https://management.azure.com/subscriptions/b65f2fec-d6b2-4edd-817e-9339d8c01dc4/resourceGroups/myRG/providers/Microsoft.ContainerService/managedClusters/myCluster/providers/Microsoft.Chaos/targets/Microsoft-AzureKubernetesServiceChaosMesh/capabilities/PodChaos-2.1?api-version=2021-09-15-preview"  --body "{\"properties\":{}}"
    ```

    Cela doit être fait pour chaque capacité que vous souhaitez activer sur le cluster.

Vous avez maintenant correctement intégré votre cluster AKS à Chaos Studio.

## <a name="create-an-experiment"></a>Créer une expérience
Une fois votre cluster AKS intégré, vous pouvez créer votre expérience. Une expérience de chaos définit les actions que vous souhaitez effectuer sur les ressources cibles, organisées en étapes, qui s’exécutent de manière séquentielle, et les branches qui s’exécutent en parallèle.

1. Créez un jsonSpec Chaos Mesh :
    1. Consultez la documentation Chaos Mesh pour un type d’erreur, [par exemple, le type PodChaos](https://chaos-mesh.org/docs/simulate-pod-chaos-on-kubernetes/#create-experiments-using-yaml-configuration-files).
    2. Formulez la configuration YAML pour ce type d’erreur à l’aide de la documentation Chaos Mesh.

        ```yaml
        apiVersion: chaos-mesh.org/v1alpha1
        kind: PodChaos
        metadata:
          name: pod-failure-example
          namespace: chaos-testing
        spec:
          action: pod-failure
          mode: all
          duration: '600s'
          selector:
            namespaces:
              - default
        ```
    3. Supprimez toute configuration YAML en dehors de `spec` (y compris le nom de la propriété Spec) et supprimez la mise en retrait des détails de la spécification.

        ```yaml
        action: pod-failure
        mode: all
        duration: '600s'
        selector:
          namespaces:
            - default
        ```
    4. Utilisez un [convertisseur YAML vers JSON comme celui-ci](https://www.convertjson.com/yaml-to-json.htm) pour convertir la configuration YAML de Chaos Mesh en JSON et la réduire.

        ```json
        {"action":"pod-failure","mode":"all","duration":"600s","selector":{"namespaces":["default"]}}
        ```
    5. Utilisez un [outil d’échappement de chaîne JSON comme celui-ci](https://www.freeformatter.com/json-escape.html) pour échapper la spécification JSON.
    
        ```json
        {\"action\":\"pod-failure\",\"mode\":\"all\",\"duration\":\"600s\",\"selector\":{\"namespaces\":[\"default\"]}}
        ```

2. Créez votre JSON d’expérience à partir de l’exemple de JSON ci-dessous. Modifiez le JSON pour qu’il corresponde à l’expérience que vous souhaitez exécuter à l’aide de l’[API de création d’expérience](/rest/api/chaosstudio/experiments/create-or-update), de la [bibliothèque d’erreurs](chaos-studio-fault-library.md) et du jsonSpec créé à l’étape précédente.

    ```json
    {
      "location": "centralus",
      "identity": {
        "type": "SystemAssigned"
      },
      "properties": {
        "steps": [
          {
            "name": "AKS pod kill",
            "branches": [
              {
                "name": "AKS pod kill",
                "actions": [
                  {
                    "type": "continuous",
                    "selectorId": "Selector1",
                    "duration": "PT10M",
                    "parameters": [
                      {
                          "key": "jsonSpec",
                          "value": "{\"action\":\"pod-failure\",\"mode\":\"all\",\"duration\":\"600s\",\"selector\":{\"namespaces\":[\"default\"]}}"
                      }
                    ],
                    "name": "urn:csci:microsoft:azureKubernetesServiceChaosMesh:podChaos/2.1"
                  }
                ]
              }
            ]
          }
        ],
        "selectors": [
          {
            "id": "Selector1",
            "type": "List",
            "targets": [
              {
                "type": "ChaosTarget",
                "id": "/subscriptions/b65f2fec-d6b2-4edd-817e-9339d8c01dc4/resourceGroups/myRG/providers/Microsoft.ContainerService/managedClusters/myCluster/providers/Microsoft.Chaos/targets/Microsoft-AzureKubernetesServiceChaosMesh"
              }
            ]
          }
        ]
      }
    }
    ```
    
2. Créez l’expérience à l’aide d’Azure CLI, en remplaçant `$SUBSCRIPTION_ID`, `$RESOURCE_GROUP` et `$EXPERIMENT_NAME` par les propriétés de votre expérience. Assurez-vous que vous avez enregistré et chargé votre JSON d’expérience, puis mettez à jour `experiment.json` avec votre nom de fichier JSON.

    ```azurecli-interactive
    az rest --method put --uri https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/resourceGroups/$RESOURCE_GROUP/providers/Microsoft.Chaos/experiments/$EXPERIMENT_NAME?api-version=2021-09-15-preview --body @experiment.json
    ```

    Chaque expérience crée une identité managée affectée par le système correspondante. Notez le `principalId` pour cette identité dans la réponse en vue de l’étape suivante.

## <a name="give-experiment-permission-to-your-aks-cluster"></a>Accorder une autorisation d’expérience à votre cluster AKS
Lorsque vous créez une expérience de chaos, Chaos Studio crée une identité managée affectée par le système qui exécute des erreurs sur vos ressources cibles. Cette identité doit avoir les [autorisations appropriées](chaos-studio-fault-providers.md) sur la ressource cible pour que l’expérience s’exécute correctement.

Donnez à l’expérience un accès à vos ressources à l’aide de la commande ci-dessous, en remplaçant `$EXPERIMENT_PRINCIPAL_ID` par le principalId de l’étape précédente et `$RESOURCE_ID` par l’ID de ressource de la ressource cible (en l’occurrence, l’ID de ressource du cluster AKS). Exécutez cette commande pour chaque ressource ciblée dans votre expérience. 

```azurecli-interactive
az role assignment create --role "Azure Kubernetes Cluster User Role" --assignee-object-id $EXPERIMENT_PRINCIPAL_ID --scope $RESOURCE_ID
```

## <a name="run-your-experiment"></a>Exécuter votre expérience
Vous pouvez désormais exécuter votre service. Pour voir l’impact, nous vous recommandons d’ouvrir la vue d’ensemble de votre cluster AKS et d’accéder à **Informations** dans un autre onglet. Les données actives pour le **Nombre de Pod actif** indiquent l’impact de l’exécution de votre expérience.

1. Démarrez l’expérience à l’aide d’Azure CLI, en remplaçant `$SUBSCRIPTION_ID`, `$RESOURCE_GROUP` et `$EXPERIMENT_NAME` par les propriétés de votre expérience.

    ```azurecli-interactive
    az rest --method post --uri https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/resourceGroups/$RESOURCE_GROUP/providers/Microsoft.Chaos/experiments/$EXPERIMENT_NAME/start?api-version=2021-09-15-preview
    ```

2. La réponse comprend une URL d’état que vous pouvez utiliser pour interroger l’état de l’expérience pendant l’exécution de celle-ci.

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous avez exécuté une expérience service-direct AKS Chaos Mesh, vous êtes prêt à :
- [Créer une expérience qui utilise des erreurs basées sur un agent](chaos-studio-tutorial-agent-based-portal.md)
- [Choisir votre expérience](chaos-studio-run-experiment.md)
