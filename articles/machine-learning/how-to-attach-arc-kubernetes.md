---
title: Machine Learning avec Azure Arc (préversion)
description: Configurer des clusters Azure Kubernetes Service et Kubernetes avec Azure Arc pour l’entraînement et l’inférence des modèles Machine Learning dans Azure Machine Learning
titleSuffix: Azure Machine Learning
author: luisquintanilla
ms.author: luquinta
ms.service: machine-learning
ms.subservice: core
ms.date: 10/21/2021
ms.topic: how-to
ms.custom: ignite-fall-2021
ms.openlocfilehash: 635153d510b18bc0ce97033094abf21a3b6d3d74
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132491625"
---
# <a name="configure-kubernetes-clusters-for-machine-learning-preview"></a>Configurer des clusters Kubernetes pour Machine Learning (préversion)

Découvrez comment configurer les clusters Azure Kubernetes Service (AKS) et Kubernetes avec Azure Arc pour l’entraînement et l’inférence des charges de travail Machine Learning.

## <a name="what-is-azure-arc-enabled-machine-learning"></a>Qu’est-ce que le machine learning avec Azure Arc ?

Azure Arc permet d’exécuter des services Azure dans n’importe quel environnement Kubernetes, qu’il soit local, multicloud ou situé à la périphérie.

Le Machine Learning avec Azure Arc vous permet de configurer et d’utiliser des clusters Kubernetes avec Azure Arc ou Azure Kubernetes Service pour l’entraînement, l’inférence et la gestion des modèles Machine Learning dans Azure Machine Learning.

## <a name="machine-learning-on-azure-kubernetes-service"></a>Machine Learning avec Azure Kubernetes Service

Pour utiliser des clusters Azure Kubernetes Service pour les charges de travail d’inférence et d’entraînement Azure Machine Learning, vous n’êtes pas obligé de les connecter à Azure Arc.

Vous devez configurer le trafic réseau entrant et sortant. Pour plus d’informations, consultez [Configurer le trafic du réseau entrant et sortant (AKS)](how-to-access-azureml-behind-firewall.md#azure-kubernetes-services-1).

Pour déployer l’extension Azure Machine Learning sur des clusters Azure Kubernetes Service, consultez la section [Déployer une extension Azure Machine Learning](#deploy-azure-machine-learning-extension).

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure. Si vous n’en possédez pas, [créez un compte gratuit](https://azure.microsoft.com/free) avant de commencer.
* Cluster Kubernetes avec Azure Arc. Pour plus d’informations, consultez [Guide de démarrage de la connexion d’un cluster Kubernetes existant à Azure Arc](../azure-arc/kubernetes/quickstart-connect-cluster.md).

    > [!NOTE]
    > Pour les clusters AKS, la connexion à Azure Arc est **facultative**.

* Répondre à la [Configuration requise du réseau Azure Arc](/azure/azure-arc/kubernetes/quickstart-connect-cluster?tabs=azure-cli#meet-network-requirements)

    > [!IMPORTANT]
    > Les clusters qui s’exécutent derrière un serveur proxy ou un pare-feu sortant ont besoin de configurations réseau supplémentaires.
    >
    > Pour plus d’informations, consultez [Configurer le trafic du réseau entrant et sortant (Kubernetes avec Azure Arc)](how-to-access-azureml-behind-firewall.md#arc-kubernetes).

* Exécutez les [Prérequis des extensions de cluster Kubernetes avec Azure Arc](../azure-arc/kubernetes/extensions.md#prerequisites).
  * Azure CLI version 2.24.0 ou ultérieure
  * Extension k8s-extension d’Azure CLI version 1.0.0 ou ultérieure

* Un espace de travail Azure Machine Learning. [Créez un espace de travail](how-to-manage-workspace.md?tabs=python) avant de commencer si vous n’en possédez pas.
  * Kit SDK Python Azure Machine Learning version 1.30 ou ultérieure
* Connectez-vous à Azure via Azure CLI

    ```azurecli
    az login
    az account set --subscription <your-subscription-id>
    ```  

* **Azure RedHat OpenShift service (ARO) et OpenShift Container Platform (OCP) uniquement**

    * Un cluster Kubernetes ARO ou OCP est opérationnel. Pour plus d’informations, consultez [Créer un cluster ARO Kubernetes](/azure/openshift/tutorial-create-cluster) et [Créer un cluster OCP Kubernetes](https://docs.openshift.com/container-platform/4.6/installing/installing_platform_agnostic/installing-platform-agnostic.html)
    * Accordez un accès privilégié aux comptes de service AzureML.

        Exécutez `oc edit scc privileged` et ajoutez les éléments suivants 

        * ```system:serviceaccount:azure-arc:azure-arc-kube-aad-proxy-sa```
        * ```system:serviceaccount:azureml:{EXTENSION NAME}-kube-state-metrics``` **(Remarque :** ```{EXTENSION NAME}```**ici doit correspondre au nom d’extension utilisé à** ```az k8s-extension create --name``` **l’étape)**
        * ```system:serviceaccount:azureml:cluster-status-reporter```
        * ```system:serviceaccount:azureml:prom-admission```
        * ```system:serviceaccount:azureml:default```
        * ```system:serviceaccount:azureml:prom-operator```
        * ```system:serviceaccount:azureml:csi-blob-node-sa```
        * ```system:serviceaccount:azureml:csi-blob-controller-sa```
        * ```system:serviceaccount:azureml:load-amlarc-selinux-policy-sa```
        * ```system:serviceaccount:azureml:azureml-fe```
        * ```system:serviceaccount:azureml:prom-prometheus```

## <a name="deploy-azure-machine-learning-extension"></a>Déploiement de l’extension Azure Machine Learning

Kubernetes avec Azure Arc a une fonctionnalité d’extension de cluster permettant d’installer divers agents, notamment des définitions Azure Policy, la supervision, le machine learning, etc. Azure Machine Learning impose d’utiliser l’extension de cluster *Microsoft.AzureML.Kubernetes* pour déployer l’agent Azure Machine Learning sur le cluster Kubernetes. Une fois l’extension Azure Machine Learning installée, vous pouvez attacher le cluster à un espace de travail Azure Machine Learning et l’utiliser pour les scénarios suivants :

* [Entrainement](#training)
* [Inférence en temps réel uniquement](#inferencing)
* [Apprentissage et inférence](#training-inferencing)

> [!TIP]
> Les clusters d’apprentissage prennent également en charge l’inférence par lot dans le cadre des pipelines Azure Machine Learning.

Utilisez la commande [`create`](/cli/azure/k8s-extension?view=azure-cli-latest&preserve-view=true) de l’extension Azure CLI `k8s-extension` pour déployer l’extension Azure Machine Learning sur votre cluster Kubernetes avec Azure Arc.

> [!IMPORTANT]
> Affectez au paramètre `--cluster-type` la valeur `managedClusters` pour déployer l’extension d’Azure Machine Learning sur les clusters AKS.

La liste suivante répertorie les paramètres de configuration disponibles à utiliser pour différents scénarios de déploiement d’extension d’Azure Machine Learning.

Vous pouvez utiliser ```--config``` ou ```--config-protected``` pour spécifier la liste des paires clé-valeur pour les configurations de déploiement d’Azure Machine Learning.

> [!TIP]
> Affectez au paramètre `openshift` la valeur `True` pour déployer l’extension d’Azure Machine Learning sur les clusters Kubernetes ARO et OCP.

| Nom de la clé du paramètre de configuration  | Description  | Entrainement | Inférence | Apprentissage et inférence |
|---|---|---|---|---|
| ```enableTraining``` | Par défaut, `False`. Définissez-le sur `True` pour créer une instance d’extension servant à l’apprentissage des modèles Machine Learning. |  **&check;** | N/A |  **&check;** |
|```logAnalyticsWS```  | Par défaut, `False`. L’extension Azure Machine Learning s’intègre à l’espace de travail Azure Log Analytics. Définissez-le sur `True` pour fournir des fonctionnalités de visionnage et d’analyse des journaux dans l’espace de travail Log Analytics. Des coûts associés à l’espace de travail Log Analytics peuvent s’appliquer. | Facultatif | Facultatif | Facultatif |
|```installNvidiaDevicePlugin```  | Par défaut, `True`. Le plug-in d’appareil NVIDIA est requis pour l’apprentissage et l’inférence sur du matériel GPU NVIDIA. L’extension Azure Machine Learning installe ce plug-in par défaut lors de la création de l’instance Azure Machine Learning, que le cluster Kubernetes dispose ou non de matériel GPU. Définissez-le sur `False` si vous ne prévoyez pas d’utiliser de GPU ou si le plug-in d’appareil NVIDIA est déjà installé.  | Facultatif |Facultatif | Facultatif |
| ```enableInference``` | Par défaut, `False`.  Définissez-le sur `True` pour créer une instance d’extension servant à l’inférence des modèles Machine Learning. | N/A | **&check;** |  **&check;** |
| ```allowInsecureConnections``` | Par défaut, `False`. Affectez à la valeur `True` pour le déploiement d’extension d’Azure Machine Learning avec prise en charge du point de terminaison HTTP pour l’inférence, lorsque ```sslCertPemFile``` et ne ```sslKeyPemFile``` sont pas fournis. | N/A | Facultatif |  Facultatif |
| ```sslCertPemFile``` & ```ssKeyPMFile``` | Chemin d’accès au fichier de clé et au certificat SSL (encodé en PEM). Requis pour le déploiement de l’extension AzureML avec prise en charge du point de terminaison HTTPS pour l’inférence. | N/A | Facultatif |  Facultatif |
| ```privateEndpointNodeport``` | Par défaut, `False`.  Affectez à la valeur `True` pour le déploiement de l'extension Azure Machine Learning avec la prise en charge des terminaux privés d'inférence de Machine Learning à l'aide de NodePort. | N/A | Facultatif |  Facultatif |
| ```privateEndpointILB``` | Par défaut, `False`.  Affectez à la valeur `True` pour le déploiement de l'extension Azure Machine Learning avec la prise en charge des terminaux privés d'inférence de Machine Learning à l'aide de l’équilibreur de charge interne serviceType | N/A| Facultatif |  Facultatif |
| ```inferenceLoadBalancerHA``` | Par défaut, `True`. Par défaut, l’extension d’Azure Machine Learning déploie plusieurs réplicas de contrôleur d’entrée pour la haute disponibilité. Affectez la valeur `False` si vous avez des ressources de cluster limitées ou si vous souhaitez déployer l’extension d’Azure Machine Learning à des fins de développement et de test uniquement. Si vous n’utilisez pas un équilibreur de charge à haute disponibilité, vous ne déployez qu’un seul réplica de contrôleur d’entrée. | N/A | Facultatif |  Facultatif |
|```openshift``` | Par défaut, `False`. Affectez la valeur `True` pour le déploiement de l’extension Azure Machine Learning sur le cluster ARO ou OCP. Le processus de déploiement compile automatiquement un package de stratégie et un package de stratégie de chargement sur chaque nœud afin que l’opération des services Azure Machine Learning puisse fonctionner correctement. | Facultatif | Facultatif |  Facultatif |

> [!WARNING]
> Si vous réinstallez le plug-in d’appareil NVIDIA, alors qu’il est déjà installé dans votre cluster, il peut se produire une erreur d’installation de l’extension. Affectez la valeur `installNvidiaDevicePlugin` à `False` pour empêcher les erreurs de déploiement.

### <a name="deploy-extension-for-training-workloads"></a>Déployer l’extension pour les charges de travail d’apprentissage <a id="training"></a>

Utilisez la commande Azure CLI suivante pour déployer l’extension Azure Machine Learning et activer les charges de travail d’apprentissage sur votre cluster Kubernetes :

```azurecli
az k8s-extension create --name arcml-extension --extension-type Microsoft.AzureML.Kubernetes --config enableTraining=True --cluster-type connectedClusters --cluster-name <your-connected-cluster-name> --resource-group <resource-group> --scope cluster
```

### <a name="deploy-extension-for-real-time-inferencing-workloads"></a>Déployer une extension pour les charges de travail d’inférence en temps réel <a id="inferencing"></a>

En fonction de la configuration de votre réseau, de la variante de distribution Kubernetes et de l’emplacement où votre cluster Kubernetes est hébergé (en local ou dans le cloud), choisissez l’une des options suivantes pour déployer l’extension Azure Machine Learning et activer les charges de travail d’inférence sur votre cluster Kubernetes.

#### <a name="public-endpoints-support-with-public-load-balancer"></a>Prise en charge des points de terminaison publics avec l’équilibreur de charge public

* **HTTPS**

    ```azurecli
    az k8s-extension create --name arcml-extension --extension-type Microsoft.AzureML.Kubernetes --cluster-type connectedClusters --cluster-name <your-connected-cluster-name> --config enableInference=True --config-protected sslCertPemFile=<path-to-the-SSL-cert-PEM-ile> sslKeyPemFile=<path-to-the-SSL-key-PEM-file> --resource-group <resource-group> --scope cluster
    ```

* **HTTP**

    > [!WARNING]
    > La prise en charge des points de terminaison HTTP publics avec l’équilibreur de charge public est la solution la moins sécurisée pour déployer l’extension Azure Machine Learning pour les scénarios d’inférence en temps réel et n’est donc **pas** recommandée.

    ```azurecli
    az k8s-extension create --name arcml-extension --extension-type Microsoft.AzureML.Kubernetes --cluster-type connectedClusters --cluster-name <your-connected-cluster-name>  --configuration-settings enableInference=True allowInsecureConnections=True --resource-group <resource-group> --scope cluster
    ```

#### <a name="private-endpoints-support-with-internal-load-balancer"></a>Prise en charge des points de terminaison privés avec l’équilibreur de charge interne

* **HTTPS**

    ```azurecli
    az k8s-extension create --name amlarc-compute --extension-type Microsoft.AzureML.Kubernetes --cluster-type connectedClusters --cluster-name <your-connected-cluster-name> --config enableInference=True privateEndpointILB=True --config-protected sslCertPemFile=<path-to-the-SSL-cert-PEM-ile> sslKeyPemFile=<path-to-the-SSL-key-PEM-file> --resource-group <resource-group> --scope cluster
    ```

* **HTTP**

   ```azurecli
   az k8s-extension create --name arcml-extension --extension-type Microsoft.AzureML.Kubernetes --cluster-type connectedClusters --cluster-name <your-connected-cluster-name> --config enableInference=True privateEndpointILB=True allowInsecureConnections=True --resource-group <resource-group> --scope cluster
   ```

#### <a name="endpoints-support-with-nodeport"></a>Prise en charge des points de terminaison avec NodePort

L’utilisation d’un NodePort vous donne la possibilité de configurer votre propre solution d’équilibrage de charge, de configurer des environnements qui ne sont pas entièrement pris en charge par Kubernetes, ou même d’exposer directement une ou plusieurs adresses IP de nœuds.

Lorsque vous procédez à un déploiement avec le service NodePort, l’URL de notation (ou l’URL Swagger) est remplacée par l’une des adresses IP de nœud (par exemple ```http://<NodeIP><NodePort>/<scoring_path>```) et reste inchangée même si le nœud n’est pas disponible. Mais vous pouvez la remplacer par n’importe quelle autre adresse IP de nœud.

* **HTTPS**

    ```azurecli
    az k8s-extension create --name arcml-extension --extension-type Microsoft.AzureML.Kubernetes --cluster-type connectedClusters --cluster-name <your-connected-cluster-name> --resource-group <resource-group> --scope cluster --config enableInference=True privateEndpointNodeport=True --config-protected sslCertPemFile=<path-to-the-SSL-cert-PEM-ile> sslKeyPemFile=<path-to-the-SSL-key-PEM-file>
    ```

* **HTTP**

   ```azurecli
   az k8s-extension create --name arcml-extension --extension-type Microsoft.AzureML.Kubernetes --cluster-type connectedClusters --cluster-name <your-connected-cluster-name> --config enableInference=True privateEndpointNodeport=True allowInsecureConnections=Ture --resource-group <resource-group> --scope cluster
   ```

### <a name="deploy-extension-for-training-and-inferencing-workloads"></a>Déployer l’extension pour les charges de travail d’apprentissage et d’inférence <a id="training-inferencing"></a>

Utilisez la commande Azure CLI suivante pour déployer l’extension Azure Machine Learning et activer l’inférence en temps réel du cluster, l’inférence de lots et les charges de travail de formation sur votre cluster Kubernetes.

```azurecli
az k8s-extension create --name arcml-extension --extension-type Microsoft.AzureML.Kubernetes --cluster-type connectedClusters --cluster-name <your-connected-cluster-name> --config enableTraining=True enableInference=True --config-protected sslCertPemFile=<path-to-the-SSL-cert-PEM-ile> sslKeyPemFile=<path-to-the-SSL-key-PEM-file>--resource-group <resource-group> --scope cluster
```

## <a name="resources-created-during-deployment"></a>Ressources créées pendant le déploiement

Une fois l’extension Azure Machine Learning déployée, les ressources suivantes sont créées dans Azure, ainsi que dans votre cluster Kubernetes, en fonction des charges de travail que vous exécutez sur votre cluster.

|Nom de la ressource  |Type de ressource |Entrainement |Inférence |Apprentissage et inférence|
|---|---|---|---|---|
|Azure ServiceBus|Ressource Azure|**&check;**|**&check;**|**&check;**|
|Azure Relay|Ressource Azure|**&check;**|**&check;**|**&check;**|
|{EXTENSION-NAME}|Ressource Azure|**&check;**|**&check;**|**&check;**|
|aml-operator|Déploiement Kubernetes|**&check;**|N/A|**&check;**|
|{EXTENSION-NAME}-kube-state-metrics|Déploiement Kubernetes|**&check;**|**&check;**|**&check;**|
|{EXTENSION-NAME}-prometheus-operator|Déploiement Kubernetes|**&check;**|**&check;**|**&check;**|
|amlarc-identity-controller|Déploiement Kubernetes|N/A|**&check;**|**&check;**|
|amlarc-identity-proxy|Déploiement Kubernetes|N/A|**&check;**|**&check;**|
|azureml-fe|Déploiement Kubernetes|N/A|**&check;**|**&check;**|
|inference-operator-controller-manager|Déploiement Kubernetes|N/A|**&check;**|**&check;**|
|metrics-controller-manager|Déploiement Kubernetes|**&check;**|**&check;**|**&check;**|
|relayserver|Déploiement Kubernetes|**&check;**|**&check;**|**&check;**|
|cluster-status-reporter|Déploiement Kubernetes|**&check;**|**&check;**|**&check;**|
|nfd-master|Déploiement Kubernetes|**&check;**|N/A|**&check;**|
|passerelle|Déploiement Kubernetes|**&check;**|**&check;**|**&check;**|
|csi-blob-controller|Déploiement Kubernetes|**&check;**|N/A|**&check;**|
|csi-blob-node|Daemonset Kubernetes|**&check;**|N/A|**&check;**|
|fluent-bit|Daemonset Kubernetes|**&check;**|**&check;**|**&check;**|
|k8s-host-device-plugin-daemonset|Daemonset Kubernetes|**&check;**|**&check;**|**&check;**|
|nfd-worker|Daemonset Kubernetes|**&check;**|N/A|**&check;**|
|prometheus-prom-prometheus|Statefulset Kubernetes|**&check;**|**&check;**|**&check;**|
|frameworkcontroller|Statefulset Kubernetes|**&check;**|N/A|**&check;**|

> [!IMPORTANT]
> Les ressources Azure ServiceBus et Azure Relay se trouvent sous le même groupe de ressources que la ressource de cluster Arc. Ces ressources sont utilisées pour communiquer avec le cluster Kubernetes et leur modification va rompre les cibles de calcul attachées.

> [!NOTE]
> **{EXTENSION-NAME}** est le nom d’extension spécifié par la ```az k8s-extension create --name``` commande Azure CLI.

## <a name="verify-your-azureml-extension-deployment"></a>Vérifiez le déploiement de votre extension AzureML.

```azurecli
az k8s-extension show --name arcml-extension --cluster-type connectedClusters --cluster-name <your-connected-cluster-name> --resource-group <resource-group>
```

Dans la réponse, recherchez `"extensionType": "arcml-extension"` et `"installState": "Installed"`. Il est à noter qu’elle peut afficher `"installState": "Pending"` les premières minutes.

Lorsque `installState` indique **Installé**, exécutez la commande suivante sur votre ordinateur en faisant pointer le fichier kubeconfig sur votre cluster pour vérifier que tous les pods situés sous l’espace de noms *azureml* se trouvent à l’état *En cours d’exécution* :

```bash
kubectl get pods -n azureml
```

## <a name="attach-arc-cluster"></a>Attacher un cluster Arc

### <a name="studio"></a>[Studio](#tab/studio)

En attachant un cluster Kubernetes avec Azure Arc vous le rendez disponible dans votre espace de travail pour l’entraînement.

1. Accédez à [Azure Machine Learning Studio](https://ml.azure.com).
1. Sous **Gérer**, sélectionnez **Calcul**.
1. Sélectionnez l’onglet **Calculs attachés**.
1. Sélectionnez **+Nouveau > Kubernetes (préversion)** .

   ![Attachement d’un cluster Kubernetes](./media/how-to-attach-arc-kubernetes/attach-kubernetes-cluster.png)

1. Entrez un nom de calcul et sélectionnez votre cluster Kubernetes avec Azure Arc dans la liste déroulante.

   **(Facultatif)** Assigner l’identité managée attribuée par le système ou par l’utilisateur. Les identités managées permettent aux développeurs de ne plus avoir à gérer les informations d'identification. Pour plus d’informations, consultez [Vue d’ensemble des identités managées](/azure/active-directory/managed-identities-azure-resources/overview).

   ![Configuration du cluster Kubernetes](./media/how-to-attach-arc-kubernetes/configure-kubernetes-cluster-2.png)

1. Sélectionnez **Attacher**.

    Dans l’onglet Calculs attachés, l’état initial du cluster est *En cours de création*. Si l’attachement du cluster aboutit, l’état passe à *Réussi*. Sinon, il devient *Échec*.

    ![Provisionner les ressources](./media/how-to-attach-arc-kubernetes/provision-resources.png)

### <a name="python-sdk"></a>[Kit de développement logiciel (SDK) Python](#tab/sdk)

Vous pouvez utiliser le kit de développement logiciel (SDK) Python Azure Machine Learning pour attacher des clusters Kubernetes avec Azure Arc en tant que cibles de calcul à l’aide de la méthode [`attach_configuration`](/python/api/azureml-core/azureml.core.compute.kubernetescompute.kubernetescompute?view=azure-ml-py&preserve-view=true).

Le code Python suivant montre comment attacher un cluster Kubernetes avec Azure Arc et l’utiliser comme cible de calcul avec l’identité managée activée.

Les identités managées permettent aux développeurs de ne plus avoir à gérer les informations d'identification. Pour plus d’informations, consultez [Vue d’ensemble des identités managées](/azure/active-directory/managed-identities-azure-resources/overview).

```python
from azureml.core.compute import KubernetesCompute
from azureml.core.compute import ComputeTarget
from azureml.core.workspace import Workspace
import os

ws = Workspace.from_config()

# Specify a name for your Kubernetes compute
amlarc_compute_name = "<COMPUTE_CLUSTER_NAME>"

# resource ID for the Kubernetes cluster and user-managed identity
resource_id = "/subscriptions/<sub ID>/resourceGroups/<RG>/providers/Microsoft.Kubernetes/connectedClusters/<cluster name>"

user_assigned_identity_resouce_id = ['subscriptions/<sub ID>/resourceGroups/<RG>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<identity name>']

ns = "default" 

if amlarc_compute_name in ws.compute_targets:
    amlarc_compute = ws.compute_targets[amlarc_compute_name]
    if amlarc_compute and type(amlarc_compute) is KubernetesCompute:
        print("found compute target: " + amlarc_compute_name)
else:
   print("creating new compute target...")


# assign user-assigned managed identity
amlarc_attach_configuration = KubernetesCompute.attach_configuration(resource_id = resource_id, namespace = ns,  identity_type ='UserAssigned',identity_ids = user_assigned_identity_resouce_id) 

# assign system-assigned managed identity
# amlarc_attach_configuration = KubernetesCompute.attach_configuration(resource_id = resource_id, namespace = ns,  identity_type ='SystemAssigned') 

amlarc_compute = ComputeTarget.attach(ws, amlarc_compute_name, amlarc_attach_configuration)
amlarc_compute.wait_for_completion(show_output=True)

# get detailed compute description containing managed identity principle ID, used for permission access. 
print(amlarc_compute.get_status().serialize())
```

Utilisez le `identity_type` paramètre pour activer `SystemAssigned` ou `UserAssigned` gérer les identités.

### <a name="cli"></a>[INTERFACE DE LIGNE DE COMMANDE](#tab/cli)

Vous pouvez attacher un cluster Kubernetes AKS ou Azure Arc activé à l’aide de l’interface de commande Azure Machine Learning 2.0 (version préliminaire).

Utilisez la commande CLI Azure Machine Learning [`attach`](/cli/azure/ml/compute?view=azure-cli-latest&preserve-view=true) et affectez à l’argument la valeur `--type``kubernetes` pour attacher votre cluster Kubernetes à l’aide de l’interface cli Azure Machine Learning 2.0.

> [!NOTE]
> La prise en charge de l’attachement de calcul pour les clusters Kubernetes AKS ou Azure arc requiert une version de l'extension Azure CLI `ml` >= 2.0.1a4. Pour plus d’informations, consultez [Installer et configurer et l’interface CLI (v2)](how-to-configure-cli.md).

La commande suivante montre comment attacher un cluster Kubernetes avec Azure Arc et l’utiliser comme cible de calcul avec l’identité managée activée.

**AKS**

```azurecli
az ml compute attach --resource-group <resource-group-name> --workspace-name <workspace-name> --name amlarc-compute --resource-id "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Kubernetes/managedclusters/<cluster-name>" --type kubernetes --identity-type UserAssigned --user-assigned-identities "subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<identity-name>" --no-wait
```

**Kubernetes compatible avec Azure Arc**

```azurecli
az ml compute attach --resource-group <resource-group-name> --workspace-name <workspace-name> --name amlarc-compute --resource-id "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Kubernetes/connectedClusters/<cluster-name>" --type kubernetes --user-assigned-identities "subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<identity-name>" --no-wait
```

Utilisez l’argument `identity_type` pour activer `SystemAssigned` ou `UserAssigned` gérer les identités.

> [!IMPORTANT]
> `--user-assigned-identities` est requis uniquement pour les `UserAssigned` identités gérées. Bien que vous puissiez fournir une liste d’identités gérées par des utilisateurs séparés par des virgules, seule la première est utilisée lorsque vous attachez votre cluster.

---

## <a name="next-steps"></a>Étapes suivantes

- [Créer et sélectionner différents types d’instances pour les charges de travail d’apprentissage et d’inférence](how-to-kubernetes-instance-type.md)
- [Entraîner des modèles avec CLI (v2)](how-to-train-cli.md)
- [Configurer et soumettre des exécutions d’entraînement](how-to-set-up-training-targets.md)
- [Optimiser les hyperparamètres](how-to-tune-hyperparameters.md)
- [Apprentissage d’un modèle avec Scikit-learn](how-to-train-scikit-learn.md)
- [Entraîner un modèle TensorFlow](how-to-train-tensorflow.md)
- [Apprentissage d’un modèle PyTorch](how-to-train-pytorch.md)
- [Apprentissage avec des pipelines Azure Machine Learning](how-to-create-machine-learning-pipelines.md)
- [Apprentissage d’un modèle local avec un serveur proxy sortant](../azure-arc/kubernetes/quickstart-connect-cluster.md#4a-connect-using-an-outbound-proxy-server)
