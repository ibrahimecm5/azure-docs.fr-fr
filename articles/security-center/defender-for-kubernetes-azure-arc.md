---
title: Protéger des déploiements Kubernetes hybrides et multicloud avec Microsoft Defender pour Kubernetes
description: Utiliser Microsoft Defender pour Kubernetes avec vos clusters Kubernetes locaux et multicloud
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 11/02/2021
ms.author: memildin
ms.openlocfilehash: 5a0e5fb157d978c84eb8315b77f9474a51cdf418
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131422498"
---
# <a name="defend-azure-arc-enabled-kubernetes-clusters-running-in-on-premises-and-multi-cloud-environments"></a>Défendre des clusters Kubernetes avec Azure Arc s’exécutant dans des environnements locaux et multicloud

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

L’**extension de cluster Microsoft Defender pour Kubernetes** peut défendre vos clusters locaux avec les mêmes fonctionnalités de détection des menaces que celles offertes pour les clusters Azure Kubernetes Service. Activez [Kubernetes avec Azure Arc](../azure-arc/kubernetes/overview.md) sur vos clusters et déployez l’extension comme décrit dans cette page. 

L’extension peut également protéger des clusters Kubernetes sur d’autres fournisseurs de cloud, mais pas sur leurs services Kubernetes managés.

> [!TIP]
> Nous avons mis en place des exemples de fichiers destinés à faciliter le processus d’installation dans [Exemples d’installation sur GitHub](https://aka.ms/kubernetes-extension-installation-examples).

## <a name="availability"></a>Disponibilité

| Aspect | Détails |
|--------|---------|
| État de publication | **Préversion**<br>[!INCLUDE [Legalese](../../includes/security-center-preview-legal-text.md)]|
| Rôles et autorisations obligatoires | L’[administrateur de sécurité](../role-based-access-control/built-in-roles.md#security-admin) peut ignorer les alertes<br>Le [Lecteur de sécurité](../role-based-access-control/built-in-roles.md#security-reader) peut afficher les résultats |
| Tarifs | Gratuit (pendant la préversion) |
| Distributions Kubernetes prises en charge | [Service Azure Kubernetes sur Azure Stack HCI](/azure-stack/aks-hci/overview)<br>[Kubernetes](https://kubernetes.io/docs/home/)<br> [Moteur AKS](https://github.com/Azure/aks-engine)<br> [Azure Red Hat OpenShift](https://azure.microsoft.com/services/openshift/)<br> [Red Hat OpenShift](https://www.openshift.com/learn/topics/kubernetes/) (version 4.6 ou plus récente)<br> [VMware Tanzu Kubernetes Grid](https://tanzu.vmware.com/kubernetes-grid)<br> [Rancher Kubernetes Engine](https://rancher.com/docs/rke/latest/en/) |
| Limites | Kubernetes avec Azure Arc et l’extension Defender **ne prennent pas en charge** les offres Kubernetes managées, telles que Google Kubernetes Engine et Elastic Kubernetes Service. [Defender pour le cloud est disponible en mode natif pour Azure Kubernetes Service (AKS)](defender-for-kubernetes-introduction.md), et ne nécessite pas de connexion du cluster à Azure Arc. |
| Environnements et régions | La disponibilité de cette extension est identique à celle de [Kubernetes avec Azure Arc](../azure-arc/kubernetes/overview.md)|

## <a name="architecture-overview"></a>Présentation de l'architecture

Pour tous les clusters Kubernetes autres que AKS, vous devez connecter votre cluster à Azure Arc. Une fois le cluster connecté, Microsoft Defender pour Kubernetes peut être déployé sur des ressources [Kubernetes Azure avec Azure Arc](../azure-arc/kubernetes/overview.md) en tant qu’[extension de cluster](../azure-arc/kubernetes/extensions.md).

Les composants d’extension collectent les données des journaux d’audit Kubernetes de tous les nœuds de plan de contrôle du cluster, et les envoient au serveur principal Microsoft Defender pour Kubernetes dans le cloud à des fins d’analyse plus approfondie. L’extension est inscrite auprès d’un espace de travail Log Analytics utilisé comme pipeline de données, mais les données du journal d’audit ne sont pas stockées dans l’espace de travail Log Analytics.

Ce diagramme illustre l’interaction entre Microsoft Defender pour Kubernetes et le cluster Kubernetes avec Azure Arc :

:::image type="content" source="media/defender-for-kubernetes-azure-arc/defender-for-kubernetes-architecture-overview.png" alt-text="Diagramme d’architecture de haut niveau présentant l’interaction entre Microsoft Defender pour Kubernetes et un cluster Kubernetes avec Azure Arc." lightbox="media/defender-for-kubernetes-azure-arc/defender-for-kubernetes-architecture-overview.png":::

## <a name="prerequisites"></a>Prérequis

Avant de déployer l’extension, effectuez les opérations suivantes :
- [Connectez le cluster Kubernetes à Azure Arc](../azure-arc/kubernetes/quickstart-connect-cluster.md)
- Suivez les [prérequis indiqués dans la documentation sur les extensions de cluster génériques](../azure-arc/kubernetes/extensions.md#prerequisites).
- Configurez le **port 443** sur les points de terminaison suivants pour l’accès sortant :
    - Pour les clusters du cloud Azure Government :
        - *.ods.opinsights.azure.us
        - *.oms.opinsights.azure.us
        - :::no-loc text="login.microsoftonline.us":::
    - Pour les clusters des autres déploiements cloud Azure :
        - *.ods.opinsights.azure.com
        - *.oms.opinsights.azure.com
        - :::no-loc text="login.microsoftonline.com":::

## <a name="deploy-the-defender-extension"></a>Déployer l’extension Defender

Vous pouvez déployer l’extension Defender à l’aide de différentes méthodes. Pour obtenir des instructions détaillées, sélectionnez l’onglet approprié.

### <a name="azure-portal"></a>[**Portail Azure**](#tab/k8s-deploy-asc)

### <a name="use-the-fix-button-from-the-defender-for-cloud-recommendation"></a>Utiliser le bouton Corriger à partir de la recommandation Defender pour le cloud

Une recommandation dédiée à Microsoft Defender pour le cloud offre les éléments suivants :

- **Visibilité** sur les clusters sur lesquels l’extension Defender pour Kubernetes est déployée
- Bouton **Corriger** pour opérer un déploiement sur ces clusters sans l’extension

1. À partir de la page de recommandations Microsoft Defender pour le cloud, ouvrez le contrôle de sécurité **Activer la sécurité renforcée**.

1. Utilisez le filtre pour trouver la recommandation nommée **Les clusters Kubernetes avec Azure Arc doivent avoir l’extension Defender installée**.

    :::image type="content" source="media/defender-for-kubernetes-azure-arc/extension-recommendation.png" alt-text="Recommandation Microsoft Defender pour le cloud relative au déploiement de l’extension Defender pour les clusters Kubernetes avec Azure Arc." lightbox="media/defender-for-kubernetes-azure-arc/extension-recommendation.png":::

    > [!TIP]
    > Notez l’icône **Corriger** dans la colonne actions

1. Sélectionnez l’extension pour afficher les détails des ressources saines et non saines (clusters avec et sans extension).

1. Dans la liste ressources non saines, sélectionnez un cluster, choisissez **Corriger** pour ouvrir le volet contenant les options de correction.

1. Sélectionnez l’espace de travail Log Analytics approprié, puis choisissez **Corriger la ressource x**.

    :::image type="content" source="media/defender-for-kubernetes-azure-arc/security-center-deploy-extension.gif" alt-text="Déployez l’extension Defender pour Azure Arc avec Defender pour l’option de correctif de Defender pour le cloud.":::


### <a name="azure-cli"></a>[**Azure CLI**](#tab/k8s-deploy-cli)

### <a name="use-azure-cli-to-deploy-the-defender-extension"></a>Utiliser Azure CLI pour déployer l’extension Defender

1. Connexion à Azure :

    ```azurecli
    az login
    az account set --subscription <your-subscription-id>
    ```

    > [!IMPORTANT]
    > Veillez à utiliser le même ID d’abonnement pour ``<your-subscription-id>`` que celui utilisé lors de la connexion de votre cluster à Azure Arc.

1. Exécutez la commande suivante pour déployer l’extension sur votre cluster Kubernetes avec Azure Arc :

    ```azurecli
    az k8s-extension create --name microsoft.azuredefender.kubernetes --cluster-type connectedClusters --cluster-name <cluster-name> --resource-group <resource-group> --extension-type microsoft.azuredefender.kubernetes
    ```

    Vous trouverez ci-dessous une description de tous les paramètres de configuration pris en charge sur le type d’extension Defender :

    | Propriété | Description |
    |----------|-------------|
    | logAnalyticsWorkspaceResourceID | **Facultatif**. ID de ressource complet de votre propre espace de travail Log Analytics.<br>Quand il n’est pas fourni, l’espace de travail par défaut de la région est utilisé.<br><br>Pour obtenir l’ID de ressource complet, exécutez la commande suivante pour afficher la liste des espaces de travail dans vos abonnements au format JSON par défaut :<br>```az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json```<br><br>La syntaxe de L’ID de ressource de l’espace de travail Log Analytics est la suivante :<br>/subscriptions/{id de votre abonnement}/resourceGroups/{votre groupe de ressources}/providers/Microsoft.OperationalInsights/workspaces/{nom de votre espace de travail}. <br>En savoir plus sur les [Espaces de travail Log Analytics](../azure-monitor/logs/data-platform-logs.md#log-analytics-and-workspaces) |
    | auditLogPath |**Facultatif**. Chemin d’accès complet aux fichiers journaux d’audit.<br>Quand il n’est pas fourni, le chemin d’accès par défaut ``/var/log/kube-apiserver/audit.log`` est utilisé.<br>Pour le moteur AKS, le chemin d’accès standard est ``/var/log/kubeaudit/audit.log`` |

    La commande ci-dessous montre un exemple d’utilisation de tous les champs facultatifs :

    ```azurecli
    az k8s-extension create --name microsoft.azuredefender.kubernetes --cluster-type connectedClusters --cluster-name <your-connected-cluster-name> --resource-group <your-rg> --extension-type microsoft.azuredefender.kubernetes --configuration-settings logAnalyticsWorkspaceResourceID=<log-analytics-workspace-resource-id> auditLogPath=<your-auditlog-path>
    ```

### <a name="resource-manager"></a>[**Gestionnaire de ressources**](#tab/k8s-deploy-resource-manager)

### <a name="use-azure-resource-manager-to-deploy-the-defender-extension"></a>Utiliser Azure Resource Manager pour déployer l’extension Defender

Pour utiliser Azure Resource Manager afin de déployer l’extension Defender, vous avez besoin d’un espace de travail Log Analytics sur votre abonnement. En savoir plus sur les [Espaces de travail Log Analytics](../azure-monitor/logs/data-platform-logs.md#log-analytics-and-workspaces).

Vous pouvez utiliser le modèle Resource Manager **azure-defender-extension-arm-template.json** figurant dans les [exemples d’installation](https://aka.ms/kubernetes-extension-installation-examples) de Defender pour le cloud.

> [!TIP]
> Si vous ne connaissez pas les modèles Resource Manager, commencez ici : [Que sont les modèles Azure Resource Manager ?](../azure-resource-manager/templates/overview.md)

### <a name="rest-api"></a>[**API REST**](#tab/k8s-deploy-api)

### <a name="use-rest-api-to-deploy-the-defender-extension"></a>Utiliser l’API REST pour déployer l’extension Defender 

Pour utiliser l’API REST afin de déployer l’extension Defender, vous avez besoin d’un espace de travail Log Analytics sur votre abonnement. En savoir plus sur les [Espaces de travail Log Analytics](../azure-monitor/logs/data-platform-logs.md#log-analytics-and-workspaces).

> [!TIP]
> La façon la plus simple d’utiliser l’API pour déployer l’extension Defender consiste à utiliser l’exemple **JSON de collection Postman** fourni parmi les [exemples d’installation](https://aka.ms/kubernetes-extension-installation-examples) de Defender pour le cloud.
- Pour modifier le JSON de collection Postamn, ou pour déployer manuellement l’extension avec l’API REST, exécutez la commande PUT suivante :

    ```rest
    PUT https://management.azure.com/subscriptions/{{Subscription Id}}/resourcegroups/{{Resource Group}}/providers/Microsoft.Kubernetes/connectedClusters/{{Cluster Name}}/providers/Microsoft.KubernetesConfiguration/extensions/microsoft.azuredefender.kubernetes?api-version=2020-07-01-preview
    ```

    Où :

    | Nom            | Dans   | Obligatoire | Type   | Description                                  |
    |-----------------|------|----------|--------|----------------------------------------------|
    | Identifiant d’abonnement | path | True     | string | ID d’abonnement de votre ressource Kubernetes avec Azure Arc |
    | Groupe de ressources  | path | True     | string | Nom du groupe de ressources contenant votre ressource Kubernetes avec Azure Arc |
    | Nom du cluster    | path | True     | string | Nom de votre ressource Kubernetes avec Azure Arc  |


    Pour l’**authentification**, votre en-tête doit avoir un jeton du porteur (comme pour d’autres API Azure). Pour obtenir un jeton du porteur, exécutez la commande suivante :

    `az account get-access-token --subscription <your-subscription-id>` Utilisez la structure suivante pour le corps de votre message :
    ```json
    { 
    "properties": { 
        "extensionType": "microsoft.azuredefender.kubernetes",
    "con    figurationSettings": { 
            "logAnalytics.workspaceId":"YOUR-WORKSPACE-ID"
        // ,    "auditLogPath":"PATH/TO/AUDITLOG"
        },
        "configurationProtectedSettings": {
            "logAnalytics.key":"YOUR-WORKSPACE-KEY"
        }
        }
    } 
    ```

    La description des propriétés figure ci-dessous :

    | Propriété | Description | 
    | -------- | ----------- |
    | logAnalytics.workspaceId | ID d’espace de travail de la ressource Log Analytics |
    | logAnalytics.key         | Clé de la ressource Log Analytics | 
    | auditLogPath             | **Facultatif**. Chemin d’accès complet aux fichiers journaux d’audit. La valeur par défaut est ``/var/log/kube-apiserver/audit.log``. |

---

## <a name="verify-the-deployment"></a>Vérifier le déploiement

Pour vérifier que l’extension Defender est installée sur votre cluster, suivez les étapes de l’un des onglets ci-dessous :

### <a name="azure-portal---defender-for-cloud"></a>[**Portail Azure : Defender pour le cloud**](#tab/k8s-verify-asc)

### <a name="use-defender-for-cloud-recommendation-to-verify-the-status-of-your-extension"></a>Utiliser une recommandation Defender pour le cloud pour vérifier l’état de votre extension

1. À partir de la page de recommandations Microsoft Defender pour le cloud, ouvrez le contrôle de sécurité **Activer Azure Defender**.

1. Sélectionnez la recommandation nommée **L’extension d’Azure Defender doit être installée sur les clusters Kubernetes avec Azure Arc**.

    :::image type="content" source="media/defender-for-kubernetes-azure-arc/extension-recommendation.png" alt-text="Recommandation Microsoft Defender pour le cloud relative au déploiement de l’extension Defender pour les clusters Kubernetes avec Azure Arc." lightbox="media/defender-for-kubernetes-azure-arc/extension-recommendation.png":::

1. Vérifiez que le cluster sur lequel vous avez déployé l’extension est répertorié comme **Sain**.


### <a name="azure-portal---azure-arc"></a>[**Portail Azure – Azure Arc**](#tab/k8s-verify-arc)

### <a name="use-the-azure-arc-pages-to-verify-the-status-of-your-extension"></a>Utiliser les pages Azure Arc pour vérifier l’état de votre extension

1. Dans le portail Azure, ouvrez **Azure Arc**.
1. Dans la liste d’infrastructures, sélectionnez **Clusters Kubernetes**, puis choisissez le cluster spécifique.
1. Ouvrez la page Extensions. Les extensions sur le cluster sont répertoriées. Vérifiez la colonne **État de l’installation** pour vérifier que l’extension Defender a été correctement installée.

    :::image type="content" source="media/defender-for-kubernetes-azure-arc/extension-installed-clusters-page.png" alt-text="Page Azure Arc pour vérifier l’état de toutes les extensions installées sur un cluster Kubernetes." lightbox="media/defender-for-kubernetes-azure-arc/extension-installed-clusters-page.png":::

1. Pour plus d’informations, sélectionnez l’extension.

    :::image type="content" source="media/defender-for-kubernetes-azure-arc/extension-details-page.png" alt-text="Détails complets d’une extension Azure Arc sur un cluster Kubernetes.":::


### <a name="azure-cli"></a>[**Azure CLI**](#tab/k8s-verify-cli)

### <a name="use-azure-cli-to-verify-that-the-extension-is-deployed"></a>Utiliser Azure CLI pour vérifier que l’extension est déployée

1. Exécutez la commande suivante sur Azure CLI :

    ```azurecli
    az k8s-extension show --cluster-type connectedClusters --cluster-name <your-connected-cluster-name> --resource-group <your-rg> --name microsoft.azuredefender.kubernetes
    ```

1. Dans la réponse, recherchez « extensionType »: « microsoft.azuredefender.kubernetes » et « installState »: « Installed ».

    > [!NOTE]
    > La réponse peut afficher « installState » : « Pending » pour les premières minutes.
    
1. Si l’état est **Installed**, exécutez la commande suivante sur votre machine avec le fichier `kubeconfig` pointé vers votre cluster pour vérifier qu’un pod appelé « azuredefender-xxxxx » est dans l’état « Running » :
    
    ```console
    kubectl get pods -n azuredefender
    ```

### <a name="rest-api"></a>[**API REST**](#tab/k8s-verify-api)

### <a name="use-the-rest-api-to-verify-that-the-extension-is-deployed"></a>Utiliser l’API REST pour vérifier que l’extension est déployée

Pour confirmer la réussite du déploiement ou valider l’état de votre extension à tout moment :

1. Exécutez la commande GET suivante :

    ```rest
    GET https://management.azure.com/subscriptions/{{Subscription Id}}/resourcegroups/{{Resource Group}}/providers/Microsoft.Kubernetes/connectedClusters/{{Cluster Name}}/providers/Microsoft.KubernetesConfiguration/extensions/microsoft.azuredefender.kubernetes?api-version=2020-07-01-preview
    ```

1. Dans la réponse, recherchez, dans « extensionType »: « microsoft.azuredefender.kubernetes », « installState »: « Installed ».

    > [!TIP]
    > La réponse peut afficher « installState » : « Pending » pour les premières minutes.

1. Si l’état est **Installed**, exécutez la commande suivante sur votre machine avec le fichier `kubeconfig` pointé vers votre cluster pour vérifier qu’un pod appelé « azuredefender-xxxxx » est dans l’état « Running » :

    ```console
    kubectl get pods -n azuredefender
    ```

---

## <a name="simulate-security-alerts-from-microsoft-defender-for-kubernetes"></a>Simuler des alertes de sécurité de Microsoft Defender pour Kubernetes

La liste complète des alertes prises en charge est disponible dans le [tableau de référence de toutes les alertes de sécurité dans Microsoft Defender pour le cloud](alerts-reference.md#alerts-k8scluster).

1. Pour simuler une alerte de sécurité, exécutez la commande suivante :

    ```console
    kubectl get pods --namespace=asc-alerttest-662jfi039n
    ```

    La réponse attendue est « Aucune ressource trouvée ».

    Dans les 30 minutes, Defender pour le cloud détectera cette activité et déclenchera une alerte de sécurité.

1. Dans le Portail Azure, ouvrez la page des alertes de sécurité de Microsoft Defender pour le cloud et recherchez l’alerte sur la ressource concernée :

    :::image type="content" source="media/defender-for-kubernetes-azure-arc/sample-kubernetes-security-alert.png" alt-text="Exemple d’alerte de Microsoft Defender pour Kubernetes." lightbox="media/defender-for-kubernetes-azure-arc/sample-kubernetes-security-alert.png":::

## <a name="removing-the-defender-extension"></a>Suppression de l’extension Defender

Vous pouvez supprimer l’extension à l’aide du portail Azure, d’Azure CLI ou de l’API REST, comme expliqué dans les onglets ci-dessous.

### <a name="azure-portal---arc"></a>[**Azure portal – Arc**](#tab/k8s-remove-arc)

### <a name="use-azure-portal-to-remove-the-extension"></a>Utiliser le portail Azure pour supprimer l’extension

1. Dans le portail Azure, ouvrez Azure Arc.
1. Dans la liste d’infrastructures, sélectionnez **Clusters Kubernetes**, puis choisissez le cluster spécifique.
1. Ouvrez la page Extensions. Les extensions sur le cluster sont répertoriées.
1. Sélectionnez le cluster, puis choisissez **Désinstaller**.

    :::image type="content" source="media/defender-for-kubernetes-azure-arc/extension-uninstall-clusters-page.png" alt-text="Suppression d’une extension de votre cluster Kubernetes avec Azure Arc." lightbox="media/defender-for-kubernetes-azure-arc/extension-uninstall-clusters-page.png":::

### <a name="azure-cli"></a>[**Azure CLI**](#tab/k8s-remove-cli)

### <a name="use-azure-cli-to-remove-the-defender-extension"></a>Utiliser Azure CLI pour supprimer l’extension Defender

1. Supprimez l’extension Microsoft Defender pour Kubernetes avec Azure Arc à l’aide des commandes suivantes :

    ```azurecli
    az login
    az account set --subscription <subscription-id>
    az k8s-extension delete --cluster-type connectedClusters --cluster-name <your-connected-cluster-name> --resource-group <your-rg> --name microsoft.azuredefender.kubernetes --yes
    ```

    La suppression de l’extension peut prendre quelques minutes. Nous vous recommandons d’attendre avant d’essayer de vérifier qu’elle a réussi.

1. Pour vérifier que l’extension a été supprimée avec succès, exécutez les commandes suivantes :

    ```azurecli
    az k8s-extension show --cluster-type connectedClusters --cluster-name <your-connected-cluster-name> --resource-group <your-rg> --name microsoft.azuredefender.kubernetes
    ```

    La suppression de la ressource d’extension d’Azure Resource Manager devrait être immédiate. Après cela, vérifiez qu’aucun pod nommé « azuredefender-XXXXX » ne figure sur le cluster en exécutant la commande suivante avec le fichier `kubeconfig` pointé vers votre cluster : 

    ```console
    kubectl get pods -n azuredefender
    ```

    La suppression des pods peut prendre quelques minutes.

### <a name="rest-api"></a>[**API REST**](#tab/k8s-remove-api)

### <a name="use-rest-api-to-remove-the-defender-extension"></a>Utiliser l’API REST pour supprimer l’extension Defender 

Pour supprimer l’extension à l’aide de l’API REST, exécutez la commande DELETE suivante :

```rest
DELETE https://management.azure.com/subscriptions/{{Subscription Id}}/resourcegroups/{{Resource Group}}/providers/Microsoft.Kubernetes/connectedClusters/{{Cluster Name}}/providers/Microsoft.KubernetesConfiguration/extensions/microsoft.azuredefender.kubernetes?api-version=2020-07-01-preview
```

| Nom            | Dans   | Obligatoire | Type   | Description                                           |
|-----------------|------|----------|--------|-------------------------------------------------------|
| Identifiant d’abonnement | path | True     | string | ID d’abonnement de votre cluster Kubernetes avec Azure Arc |
| Groupe de ressources  | path | True     | string | Groupe de ressources de votre cluster Kubernetes avec Azure Arc  |
| Nom du cluster    | path | True     | string | Nom de votre cluster Kubernetes avec Azure Arc            |

Pour l’**authentification**, votre en-tête doit avoir un jeton du porteur (comme pour d’autres API Azure). Pour obtenir un jeton du porteur, exécutez la commande suivante :

```azurecli
az account get-access-token --subscription <your-subscription-id>
```

L’exécution de la demande peut prendre plusieurs minutes.

---

## <a name="next-steps"></a>Étapes suivantes

Cette page vous a expliqué comment déployer l’extension Defender pour des clusters Kubernetes avec Azure Arc. En savoir plus sur les fonctionnalités de sécurité des conteneurs Microsoft Defender pour le cloud dans les pages suivantes :

- [Sécurité des conteneurs dans Defender pour le cloud](container-security.md)
- [Présentation de Microsoft Defender pour Kubernetes](defender-for-kubernetes-introduction.md)
- [Protéger vos charges de travail Kubernetes](kubernetes-workload-protections.md)
