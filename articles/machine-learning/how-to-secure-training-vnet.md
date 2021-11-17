---
title: Sécuriser les environnements d’entraînement à l’aide de réseaux virtuels
titleSuffix: Azure Machine Learning
description: Utilisez un réseau virtuel Azure isolé pour sécuriser votre environnement d’entraînement Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: enterprise-readiness
ms.topic: how-to
ms.reviewer: larryfr
ms.author: jhirono
author: jhirono
ms.date: 11/05/2021
ms.custom: contperf-fy20q4, tracking-python, contperf-fy21q1, references_regions
ms.openlocfilehash: ec3a5feefa41a1bd0bb0448321fc87f7e11a55d9
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132136288"
---
# <a name="secure-an-azure-machine-learning-training-environment-with-virtual-networks"></a>Sécuriser un environnement d’entraînement Azure Machine Learning à l’aide de réseaux virtuels

Dans cet article, vous allez apprendre à sécuriser les environnements d’entraînement à l’aide d’un réseau virtuel dans Azure Machine Learning.

> [!TIP]
> Cet article fait partie d’une série sur la sécurisation d’un workflow Azure Machine Learning. Consultez les autres articles de cette série :
>
> * [Présentation du réseau virtuel](how-to-network-security-overview.md)
> * [Sécuriser les ressources d’espace de travail](how-to-secure-workspace-vnet.md)
> * [Sécuriser l’environnement d’inférence](how-to-secure-inferencing-vnet.md)
> * [Activer les fonctionnalités de Studio](how-to-enable-studio-virtual-network.md)
> * [Utiliser le DNS personnalisé](how-to-custom-dns.md)
> * [Utiliser un pare-feu](how-to-access-azureml-behind-firewall.md)
>
> Pour obtenir un tutoriel sur la création d’un espace de travail sécurisé, d’un cluster de calcul et d’une instance de calcul, consultez [Tutoriel : Créer un espace de travail sécurisé](tutorial-create-secure-workspace.md).

Dans cet article, vous découvrirez comment sécuriser les ressources de calcul d’entraînement dans un réseau virtuel :
> [!div class="checklist"]
> - Cluster de calcul Azure Machine Learning
> - Instance de calcul Azure Machine Learning
> - Azure Databricks
> - Machine virtuelle
> - Cluster HDInsight

## <a name="prerequisites"></a>Prérequis

+ Lisez l’article [Vue d’ensemble de la sécurité réseau](how-to-network-security-overview.md) pour comprendre les scénarios courants des réseaux virtuels et l’architecture globale des réseaux virtuels.

+ Un réseau virtuel et un sous-réseau existants à utiliser avec vos ressources de calcul.

+ Pour déployer des ressources dans un réseau virtuel ou un sous-réseau, votre compte d’utilisateur doit disposer d’autorisations pour les actions suivantes dans le contrôle d’accès en fonction du rôle Azure (Azure RBAC) :

    - « Microsoft.Network/virtualNetworks/*/read » sur la ressource de réseau virtuel. Cela n’est pas nécessaire pour les déploiements de modèles Azure Resource Manager (ARM)
    - « Microsoft.Network/virtualNetworks/subnet/join/action » sur la ressource de sous-réseau virtuel.

    Pour plus d’informations sur Azure RBAC avec la mise en réseau, consultez [Rôles intégrés pour la mise en réseau](../role-based-access-control/built-in-roles.md#networking).

### <a name="azure-machine-learning-compute-clusterinstance"></a>Cluster/instance de calcul Azure Machine Learning

* Le réseau virtuel doit être dans les mêmes abonnements que l’espace de travail Azure Machine Learning.
* Le sous-réseau utilisé pour l’instance ou le cluster de calcul doit avoir suffisamment d’adresses IP non attribuées.

    * Un cluster de calcul peut être mis à l’échelle de manière dynamique. S’il n’y a pas assez d’adresses IP non attribuées, le cluster est alloué partiellement.
    * Une instance de calcul nécessite une seule adresse IP.

* Pour créer une instance ou un cluster de calcul [sans adresse IP publique](#no-public-ip) (fonctionnalité d’évaluation), votre espace de travail doit utiliser un point de terminaison privé pour se connecter au réseau virtuel. Pour plus d’informations, consultez [Configurer un point de terminaison privé pour un espace de travail Azure Machine Learning](how-to-configure-private-link.md).
* Assurez-vous qu’il n’existe pas de stratégie ou de verrous de sécurité qui restreignent les autorisations de gérer le réseau virtuel. Lors de la vérification des stratégies ou des verrous, examinez l’abonnement et le groupe de ressources du réseau virtuel.
* Vérifiez si vos stratégies ou verrous de sécurité sur l’abonnement ou le groupe de ressources du réseau virtuel restreignent les autorisations pour gérer le réseau virtuel. 
* Si vous prévoyez de sécuriser le réseau virtuel en limitant le trafic, consultez la section [Accès Internet public obligatoire](#required-public-internet-access).
* Le sous-réseau utilisé pour déployer l’instance/le cluster de calcul ne doit pas être délégué à un autre service. Par exemple, il ne doit pas être délégué à ACI.

### <a name="azure-databricks"></a>Azure Databricks

* Le réseau virtuel doit être dans les mêmes abonnement et région que l’espace de travail Azure Machine Learning.
* Si le ou les comptes de stockage Azure pour l’espace de travail sont également sécurisés dans un réseau virtuel, ils doivent se trouver dans le même réseau virtuel que le cluster Azure Databricks.

## <a name="limitations"></a>Limites

### <a name="azure-machine-learning-compute-clusterinstance"></a>Cluster/instance de calcul Azure Machine Learning

* Si vous placez plusieurs instances ou clusters de calcul sur un réseau virtuel, vous devrez peut-être demander une augmentation du quota pour une ou plusieurs de vos ressources. La capacité de calcul ou le cluster Machine Learning alloue automatiquement des ressources réseau supplémentaires au __groupe de ressources qui contient le réseau virtuel__. Pour chaque instance ou cluster de calcul, le service alloue les ressources suivantes :

    * Un groupe de sécurité réseau (NSG). Ce groupe de sécurité réseau contient les règles suivantes, qui sont spécifiques au cluster de calcul et à l’instance de calcul :

        * Autoriser le trafic TCP entrant sur les ports 29876-29877 depuis l’étiquette de service `BatchNodeManagement`.
        * Autoriser le trafic TCP entrant sur le port 44224 depuis l’étiquette de service `AzureMachineLearning`.

        La capture d’écran suivante présente un exemple de ces règles :

        :::image type="content" source="./media/how-to-secure-training-vnet/compute-instance-cluster-network-security-group.png" alt-text="Capture d’écran de NSG":::


        > [!TIP]
        > Si votre instance ou un cluster de calcul n’utilise pas d’adresse IP publique (fonctionnalité d’évaluation), ces règles de groupe de sécurité réseau entrantes ne sont pas requises. 
        
    * Pour l’instance ou le cluster de calcul, il est désormais possible de supprimer l’adresse IP publique (fonctionnalité d’évaluation). Si vous avez des affectations Azure Policy qui interdisent la création d’adresses IP publiques, le déploiement du cluster ou de l’instance de calcul réussira.

    * Un seul équilibreur de charge

    Pour les clusters de calcul, ces ressources sont supprimées chaque fois que le cluster effectue un scale-down à 0 nœud et créées chaque fois que le cluster effectue un scale-up.

    Pour une instance de calcul, ces ressources sont conservées jusqu’à ce que l’instance soit supprimée. L’arrêt de l’instance ne supprime pas les ressources. 

    > [!IMPORTANT]
    > Ces ressources sont limitées par les [quotas de ressources](../azure-resource-manager/management/azure-subscription-service-limits.md) de l’abonnement. Si le groupe de ressources du réseau virtuel est verrouillé, la suppression de l’instance/du cluster de calcul échoue. L’équilibreur de charge ne peut pas être supprimé tant que l’instance/le cluster de calcul n’a pas été supprimé. Vérifiez également qu’aucune affectation Azure Policy n’interdit la création de groupes de sécurité réseau.

* Si les comptes stockage Azure de l’espace de travail se trouvent également dans le réseau virtuel, suivez les instructions ci-dessous sur les limitations de sous-réseau :

    * Si vous prévoyez d’utiliser Azure Machine Learning __Studio__ pour consulter les données ou utiliser le concepteur, le compte de stockage doit se trouver __dans le même sous-réseau que l’instance ou le cluster de calcul__.
    * Si vous prévoyez d’utiliser le __SDK__, le compte de stockage peut se trouver dans un autre sous-réseau.

    > [!NOTE]
    > Le fait d’ajouter une instance de ressource pour votre espace de travail ou de cocher la case « Autoriser les services Microsoft approuvés à accéder à ce compte » ne suffit pas à autoriser la communication depuis le calcul.

* Lorsque votre espace de travail utilise un point de terminaison privé, l’instance de calcul est accessible uniquement depuis le réseau virtuel. Si vous utilisez un fichier DNS ou hosts personnalisé, ajoutez une entrée pour `<instance-name>.<region>.instances.azureml.ms`. Mappez cette entrée à l’adresse IP privée du point de terminaison privé de l’espace de travail. Pour plus d’informations, consultez l’article [DNS personnalisé](./how-to-custom-dns.md).
* Les stratégies de point de terminaison de service de réseau virtuel ne fonctionnent pas pour les comptes de stockage système de l’instance/du cluster de calcul.
* Si le stockage et l’instance de calcul se trouvent dans des régions différentes, vous pourriez voir des délais d’attente intermittents.
* Si Azure Container Registry pour votre espace de travail utilise un point de terminaison privé pour se connecter au réseau virtuel, vous ne pouvez pas utiliser une identité managée pour l’instance de calcul. Pour utiliser une identité managée avec l’instance de calcul, ne placez pas le registre de conteneurs dans le réseau virtuel.
* Si vous souhaitez utiliser des notebooks Jupyter sur une instance de calcul :

    * Ne désactivez pas la communication WebSocket. Assurez-vous que votre réseau autorise les communications WebSocket vers `*.instances.azureml.net` et `*.instances.azureml.ms`.
    * Vérifiez que votre notebook s’exécute sur une ressource de calcul derrière le même réseau virtuel et le même sous-réseau que vos données. Lorsque vous créez l’instance de calcul, utilisez **Paramètres avancés** > **Configurer le réseau virtuel** pour sélectionner le réseau et le sous-réseau.

* Les __clusters de calcul__ peuvent être créés dans une région différente de celle de votre espace de travail. Cette fonctionnalité est en __préversion__ et n’est disponible que pour les __clusters de calcul__ et pas les instances de calcul. Lorsque vous utilisez une autre région pour le cluster, les limitations suivantes s’appliquent :

    * Si les ressources associées à votre espace de travail, telles que le stockage, se trouvent dans un autre réseau virtuel que le cluster, configurez l’appairage de réseaux virtuels entre les réseaux. Pour en savoir plus, consultez [Peering de réseaux virtuels](../virtual-network/virtual-network-peering-overview.md).
    * Vous pouvez constater une augmentation de la latence du réseau et des coûts de transfert de données. La latence et les coûts peuvent survenir lors de la création du cluster et lors de l’exécution de travaux sur celui-ci.

    Des instructions telles que l’utilisation des règles de groupe de sécurité réseau, les itinéraires définis par l’utilisateur et les spécifications d’entrée/sortie s’appliquent normalement lors de l’utilisation d’une région différente de celle de l’espace de travail.

    > [!WARNING]
    > Si vous utilisez un __espace de travail avec point de terminaison privé__, la création du cluster dans une autre région __n’est pas prise en charge__.

### <a name="azure-databricks"></a>Azure Databricks

* Outre les sous-réseaux __databricks-private__ et __databricks-public__ utilisés par Azure Databricks, le sous-réseau __default__ créé pour le réseau virtuel est également requis.
* Azure Databricks n’utilise pas de point de terminaison privé pour communiquer avec le réseau virtuel.

Pour plus d’informations sur l’utilisation d’Azure Databricks dans un réseau virtuel, consultez [Déployer des Azure Databricks dans votre réseau virtuel Azure](/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject).

### <a name="azure-hdinsight-or-virtual-machine"></a>Azure HDInsight ou machine virtuelle

* Azure Machine Learning prend uniquement en charge les machines virtuelles exécutant Ubuntu.

## <a name="required-public-internet-access"></a>Accès Internet public obligatoire

[!INCLUDE [machine-learning-required-public-internet-access](../../includes/machine-learning-public-internet-access.md)]

Pour plus d’informations sur l’utilisation d’une solution de pare-feu, consultez [Utiliser un pare-feu avec Azure Machine Learning](how-to-access-azureml-behind-firewall.md).

## <a name="compute-clusters"></a><a name="compute-cluster"></a>Clusters de calcul

Utilisez les onglets ci-dessous pour choisir la façon dont vous envisagez de créer un cluster de calcul :

# <a name="studio"></a>[Studio](#tab/azure-studio)

Procédez comme suit pour créer un cluster de calcul dans Azure Machine Learning Studio :

1. Connectez-vous à [Azure Machine Learning Studio](https://ml.azure.com/), puis sélectionnez votre abonnement et votre espace de travail.
1. Sélectionnez __Calcul__ sur la gauche, __Clusters de calcul__ à partir du centre, puis sélectionnez __+ Nouveau__.

    :::image type="content" source="./media/how-to-enable-virtual-network/create-compute-cluster.png" alt-text="Capture d’écran de la création d’un cluster":::

1. Dans la boîte de dialogue __Créer un cluster de calcul__, sélectionnez la taille de machine virtuelle et la configuration dont vous avez besoin, puis sélectionnez __Suivant__.

    :::image type="content" source="./media/how-to-enable-virtual-network/create-compute-cluster-vm.png" alt-text="Capture d’écran de la configuration de la machine virtuelle":::

1. Dans la section __Paramètres de configuration__, définissez le __nom du calcul__, le __réseau virtuel__ et le __sous-réseau__.

    :::image type="content" source="media/how-to-enable-virtual-network/create-compute-cluster-config.png" alt-text="La capture d’écran illustre la définition du nom de calcul, du réseau virtuel et du sous-réseau.":::

    > [!TIP]
    > Si votre espace de travail utilise un point de terminaison privé pour se connecter au réseau virtuel, le champ de sélection __Réseau virtuel__ est grisé.
    > 

1. Sélectionnez __Créer__ pour créer le cluster de calcul.

# <a name="python"></a>[Python](#tab/python)

Le code suivant crée un cluster Capacité de calcul dans le sous-réseau `default` d’un réseau virtuel nommé `mynetwork` :

```python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# The Azure virtual network name, subnet, and resource group
vnet_name = 'mynetwork'
subnet_name = 'default'
vnet_resourcegroup_name = 'mygroup'

# Choose a name for your CPU cluster
cpu_cluster_name = "cpucluster"

# Verify that cluster does not exist already
try:
    cpu_cluster = ComputeTarget(workspace=ws, name=cpu_cluster_name)
    print("Found existing cpucluster")
except ComputeTargetException:
    print("Creating new cpucluster")

    # Specify the configuration for the new cluster
    compute_config = AmlCompute.provisioning_configuration(vm_size="STANDARD_D2_V2",
                                                           min_nodes=0,
                                                           max_nodes=4,
                                                           location="westus2",
                                                           vnet_resourcegroup_name=vnet_resourcegroup_name,
                                                           vnet_name=vnet_name,
                                                           subnet_name=subnet_name)

    # Create the cluster with the specified name and configuration
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)

    # Wait for the cluster to be completed, show the output log
    cpu_cluster.wait_for_completion(show_output=True)
```

---

Une fois le processus de création terminé, vous pouvez entraîner votre modèle en utilisant le cluster dans le cadre d’une expérience. Pour plus d’informations, consultez [Sélectionner et utiliser une cible de calcul pour l’entraînement](how-to-set-up-training-targets.md).

[!INCLUDE [low-pri-note](../../includes/machine-learning-low-pri-vm.md)]

### <a name="no-public-ip-for-compute-clusters-preview"></a><a name="no-public-ip-amlcompute"></a>Aucune adresse IP publique pour les clusters de calcul (préversion)

Lorsque vous **n’activez aucune adresse IP publique**, votre cluster de calcul n’utilise pas d’adresse IP publique pour la communication avec les dépendances. Au lieu de cela, elle communique uniquement au sein du réseau virtuel à l’aide de l’écosystème Azure Private Link, ainsi que des points de terminaison de service/privé, ce qui évite de recourir à une adresse IP publique. Aucune adresse IP publique ne supprime l’accès et la détectabilité des nœuds de cluster de calcul à partir d’Internet, ce qui élimine un vecteur de menace significatif. **Aucun cluster IP public** n’aide à se conformer aux stratégies IP publiques de nombreuses entreprises. 

Un cluster de calcul pour laquelle **aucune adresse IP publique** n’est activée n’a **aucune exigence de communication entrante** provenant d’Internet public par rapport à celles du cluster de calcul IP publique. Plus précisément, aucune règle de groupe de sécurité réseau entrante (`BatchNodeManagement`, `AzureMachineLearning`) n’est requise. Vous devez toujours autoriser le trafic provenant de **VirtualNetwork** et de tout port source ainsi que le trafic à destination de **VirtualNetwork** et des ports de destination **29876, 29877**.

**Aucun cluster d’adresses IP publiques** n’est dépendant de l’espace de travail [Azure Private Link](how-to-configure-private-link.md) pour Azure Machine Learning. Un cluster de calcul sans **adresse IP publique** vous oblige également à désactiver les stratégies réseau de point de terminaison privé et les stratégies réseau de service de liaison privée. Ces exigences proviennent du service Azure Private Link et des points de terminaison privés et ne sont pas spécifiques à Azure Machine Learning. Suivez les instructions de la [désactivation des stratégies réseau pour le service Private Link](../private-link/disable-private-link-service-network-policy.md) pour définir les paramètres `disable-private-endpoint-network-policies` et `disable-private-link-service-network-policies` sur le sous-réseau du réseau virtuel.

Pour que les **connexions sortantes** fonctionnent, vous devez configurer un pare-feu de sortie tel que le pare-feu Azure avec des itinéraires définis par l’utilisateur. Par exemple, vous pouvez utiliser un pare-feu configuré avec la [configuration entrante/sortante](how-to-access-azureml-behind-firewall.md) et acheminer le trafic ici en définissant une table de routage sur le sous-réseau dans lequel le cluster de calcul est déployé. L’entrée de table de routage peut définir le tronçon suivant de l’adresse IP privée du pare-feu avec le préfixe d’adresse 0.0.0.0/0.

Vous pouvez utiliser un point de terminaison de service ou un point de terminaison privé pour votre registre de conteneurs Azure et le stockage Azure dans le sous-réseau dans lequel le cluster est déployé.

Pour créer un cluster de calcul d’adresse IP non publique (fonctionnalité d’évaluation) dans Studio, cochez la case **Aucune adresse IP publique** dans la section du réseau virtuel.
Vous ne pouvez pas non plus créer de cluster de calcul IP publique via un modèle ARM. Dans le modèle ARM, définissez le paramètre enableNodePublicIP sur false.

[!INCLUDE [no-public-ip-info](../../includes/machine-learning-no-public-ip-availibility.md)]

**Dépannage**

* Si vous recevez ce message d’erreur lors de la création du cluster « The specified subnet has PrivateLinkServiceNetworkPolicies or PrivateEndpointNetworkEndpoints enabled » (« PrivateLinkServiceNetworkPolicies ou PrivateEndpointNetworkEndpoints est activé pour le sous-réseau »), suivez les instructions de la rubrique [Désactiver les stratégies réseau pour le service de liaison privée](../private-link/disable-private-link-service-network-policy.md) et [Désactiver les stratégies réseau pour le point de terminaison privé](../private-link/disable-private-endpoint-network-policy.md).

* Si l’exécution de la tâche échoue avec des problèmes de connexion à ACR ou à Stockage Azure, vérifiez que le client a ajouté ACR et le point de terminaison de service/les points de terminaison privés Stockage Azure au sous-réseau et que ACR/Stockage Azure autorise l’accès à partir du sous-réseau.

* Pour vous assurer que vous avez créé un cluster d’adresses IP publiques, dans Studio, lorsque vous examinez les détails du cluster, vous ne voyez aucune propriété d'**adresse IP publique** définie sur **true** sous les propriétés de la ressource.

## <a name="compute-instance"></a>Instance de calcul

Pour connaître les étapes de création d’une instance de calcul déployée dans un réseau virtuel, consultez [Créer et gérer une instance de calcul Azure Machine Learning](how-to-create-manage-compute-instance.md).

### <a name="no-public-ip-for-compute-instances-preview"></a><a name="no-public-ip"></a>Aucune adresse IP publique pour les instances de calcul (préversion)

Lorsque vous **n’activez aucune adresse IP publique**, votre instance de calcul n’utilise pas d’adresse IP publique pour la communication avec les dépendances. Au lieu de cela, elle communique uniquement au sein du réseau virtuel à l’aide de l’écosystème Azure Private Link, ainsi que des points de terminaison de service/privé, ce qui évite de recourir à une adresse IP publique. Aucune adresse IP publique ne supprime l’accès et la détectabilité du nœud d’instance de calcul à partir d’Internet, ce qui élimine un vecteur de menace significatif. Les instances de calcul effectuent également un filtrage de paquets pour rejeter tout le trafic provenant de l’extérieur du réseau virtuel. **Aucune instance d’adresse IP publique** n’est dépendante de l’espace de travail [Azure Private Link](how-to-configure-private-link.md) pour Azure Machine Learning. 

Pour que les **connexions sortantes** fonctionnent, vous devez configurer un pare-feu de sortie tel que le pare-feu Azure avec des itinéraires définis par l’utilisateur. Par exemple, vous pouvez utiliser un pare-feu configuré avec la [configuration entrante/sortante](how-to-access-azureml-behind-firewall.md) et acheminer le trafic ici en définissant une table de routage sur le sous-réseau dans lequel l’instance de calcul est déployée. L’entrée de table de routage peut définir le tronçon suivant de l’adresse IP privée du pare-feu avec le préfixe d’adresse 0.0.0.0/0.

Une instance de calcul pour laquelle **aucune adresse IP publique** n’est activée n’a **aucune exigence de communication entrante** provenant d’Internet public par rapport à celles de l’instance de calcul IP publique. Plus précisément, aucune règle de groupe de sécurité réseau entrante (`BatchNodeManagement`, `AzureMachineLearning`) n’est requise. Vous devez toujours autoriser le trafic provenant de **VirtualNetwork** et de tout port source ainsi que le trafic à destination de **VirtualNetwork** et des ports de destination **29876, 29877, 44224**.

Une instance de calcul sans **adresse IP publique** vous oblige également à désactiver les stratégies réseau de point de terminaison privé et les stratégies réseau de service de liaison privée. Ces exigences proviennent du service Azure Private Link et des points de terminaison privés et ne sont pas spécifiques à Azure Machine Learning. Suivez les instructions de la [désactivation des stratégies réseau pour l’adresse IP source du service Private Link](../private-link/disable-private-link-service-network-policy.md) pour définir les paramètres `disable-private-endpoint-network-policies` et `disable-private-link-service-network-policies` sur le sous-réseau du réseau virtuel.

Pour créer une instance de calcul d’adresse IP non publique (fonctionnalité d’évaluation) dans Studio, cochez la case **Aucune adresse IP publique** dans la section du réseau virtuel.
Vous ne pouvez pas non plus créer d’instance de calcul IP publique via un modèle ARM. Dans le modèle ARM, définissez le paramètre enableNodePublicIP sur false.

Étapes suivantes :
* [Utiliser le DNS personnalisé](how-to-custom-dns.md)
* [Utiliser un pare-feu](how-to-access-azureml-behind-firewall.md)

[!INCLUDE [no-public-ip-info](../../includes/machine-learning-no-public-ip-availibility.md)]

## <a name="inbound-traffic"></a>Trafic entrant

[!INCLUDE [udr info for computes](../../includes/machine-learning-compute-user-defined-routes.md)]

Pour plus d’informations sur les conditions requises en matière de trafic d’entrée et de sortie pour Azure Machine Learning, consultez [Utiliser un espace de travail derrière un pare-feu](how-to-access-azureml-behind-firewall.md).

## <a name="azure-databricks"></a>Azure Databricks

Pour obtenir des informations spécifiques sur l’utilisation d’Azure Databricks avec un réseau virtuel, consultez [Déployer des Azure Databricks dans votre réseau virtuel Azure](/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject).

<a id="vmorhdi"></a>

## <a name="virtual-machine-or-hdinsight-cluster"></a>Machine virtuelle ou cluster HDInsight

Dans cette section, vous allez apprendre à utiliser une machine virtuelle ou un cluster Azure HDInsight dans un réseau virtuel avec votre espace de travail.

### <a name="create-the-vm-or-hdinsight-cluster"></a>Créer la machine virtuelle ou le cluster HDInsight

Créez une machine virtuelle ou un cluster HDInsight à l’aide du portail Azure ou de l’interface de ligne de commande Azure, et placez le cluster dans un réseau virtuel Azure. Pour plus d’informations, consultez les articles suivants :
* [Créer et gérer des réseaux virtuels Azure pour des machines virtuelles Linux](../virtual-machines/linux/tutorial-virtual-network.md)

* [Étendre HDInsight à l’aide d’un réseau virtuel Azure](../hdinsight/hdinsight-plan-virtual-network-deployment.md)

### <a name="configure-network-ports"></a>Configurer des ports réseau 

Autorisez Azure Machine Learning à communiquer avec le port SSH sur la machine virtuelle ou le cluster, configurez une entrée source pour le groupe de sécurité réseau. Le port SSH est généralement le port 22. Pour autoriser le trafic provenant de cette source, effectuez les actions suivantes :

1. Dans la liste déroulante __Source__, sélectionnez __Balise de service__.

1. Dans la liste déroulante __Balise de service source__, sélectionnez __AzureMachineLearning__.

    ![Règles de trafic entrant pour effectuer des expériences sur une machine virtuelle ou un cluster HDInsight à l’intérieur d’un réseau virtuel](./media/how-to-enable-virtual-network/experimentation-virtual-network-inbound.png)

1. Dans la liste déroulante __Plages de port source__, sélectionnez __*__ .

1. Dans la liste déroulante __Destination__, sélectionnez __Tous__.

1. Dans la liste déroulante __Plages de port de destination__, sélectionnez __22__.

1. Sous __Protocole__, sélectionnez __Tous__.

1. Sous __Action__, sélectionnez __Autoriser__.

Conservez les règles de trafic sortant par défaut pour le groupe de sécurité réseau. Pour plus d’informations, consultez les règles de sécurité par défaut dans [Groupes de sécurité](../virtual-network/network-security-groups-overview.md#default-security-rules).

Si vous ne souhaitez pas utiliser les règles de trafic sortant par défaut et souhaitez limiter l’accès sortant de votre réseau virtuel, consultez la section [Accès Internet public obligatoire](#required-public-internet-access).

### <a name="attach-the-vm-or-hdinsight-cluster"></a>Joindre la machine virtuelle ou le cluster HDInsight

Attachez la machine virtuelle ou le cluster HDInsight à votre espace de travail Azure Machine Learning. Pour plus d’informations, consultez [Configurer des cibles de calcul pour l’entraînement des modèles](how-to-set-up-training-targets.md).

## <a name="next-steps"></a>Étapes suivantes

Cet article fait partie d’une série sur la sécurisation d’un workflow Azure Machine Learning. Consultez les autres articles de cette série :

* [Présentation du réseau virtuel](how-to-network-security-overview.md)
* [Sécuriser les ressources d’espace de travail](how-to-secure-workspace-vnet.md)
* [Sécuriser l’environnement d’inférence](how-to-secure-inferencing-vnet.md)
* [Activer les fonctionnalités de Studio](how-to-enable-studio-virtual-network.md)
* [Utiliser le DNS personnalisé](how-to-custom-dns.md)
* [Utiliser un pare-feu](how-to-access-azureml-behind-firewall.md)
