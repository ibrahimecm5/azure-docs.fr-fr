---
title: Sécuriser le flux de trafic réseau
titleSuffix: Azure Machine Learning
description: Découvrez comment le trafic réseau circule entre les composants lorsque votre espace de travail Azure Machine Learning se trouve dans un réseau virtuel sécurisé.
services: machine-learning
ms.service: machine-learning
ms.subservice: enterprise-readiness
ms.topic: conceptual
ms.author: jhirono
author: jhirono
ms.reviewer: larryfr
ms.date: 11/09/2021
ms.openlocfilehash: d71d986ca975b9617af9b966c8795cd30d7db01d
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132134933"
---
# <a name="network-traffic-flow-when-using-a-secured-workspace"></a>Flux de trafic réseau lors de l’utilisation d’un espace de travail sécurisé

Lorsque votre espace de travail Azure Machine Learning et les ressources associées sont sécurisés dans un réseau virtuel Azure, cela affecte le trafic réseau entre les ressources. Sans réseau virtuel, le trafic réseau transite par l’Internet public ou au sein d’un centre de données Azure. Une fois qu’un réseau virtuel est introduit, vous pouvez également renforcer la sécurité du réseau. Par exemple, le blocage des communications entrantes et sortantes entre le réseau virtuel et l’Internet public. Toutefois, Azure Machine Learning nécessite l’accès à certaines ressources sur l’Internet public. Par exemple, la gestion des ressources Azure est utilisée pour les déploiements et les opérations de gestion.

Cet article répertorie le trafic requis de/vers l’Internet public. Il explique également comment le trafic réseau transite entre votre environnement de développement client et un espace de travail Azure Machine Learning sécurisé dans les scénarios suivants :

* Utilisation d’Azure Machine Learning __Studio__ pour travailler avec :

    * Votre espace de travail
    * AutoML
    * Designer
    * Jeux de données et magasins de données

    > [!TIP]
    > Azure Machine Learning studio est une interface utilisateur web qui s’exécute partiellement dans votre navigateur web, et qui effectue des appels aux services Azure pour effectuer des tâches telles que l’apprentissage d’un modèle, l’utilisation du concepteur ou l’affichage de jeux de données. Certains de ces appels utilisent un autre canal de communication que si vous utilisiez le kit de développement logiciel (SDK), l’interface CLI, l’API REST ou VS Code.

* Utiliser Azure Machine Learning __Studio__, le __SDK__, la __CLI__, ou l’__API REST__ pour travailler avec :

    * Les clusters et instances de calcul
    * Azure Kubernetes Service
    * Les images Docker gérées par Azure Machine Learning

> [!TIP]
> Si un scénario ou une tâche ne figure pas ici, le fonctionnement devrait être le même avec ou sans espace de travail sécurisé.

## <a name="assumptions"></a>Hypothèses

Cet article considère la configuration suivante :

* Un espace de travail Azure Machine Learning utilisant un point de terminaison privé pour communiquer avec le réseau virtuel.
* Les instances de Stockage Azure, Azure Key Vault et Azure Container Registry utilisées par l’espace de travail utilisent également un point de terminaison privé pour communiquer avec le réseau virtuel.
* Une passerelle VPN ou Express Route est utilisé par les stations de travail clientes pour accéder au réseau virtuel.

## <a name="inbound-and-outbound-requirements"></a>Exigences entrantes et sortantes


| __Scénario__ | __Entrant requis__ | __Sortant requis__ | __Configuration supplémentaire__ | 
| ----- | ----- | ----- | ----- |
| [Accéder à l’espace de travail à partir de Studio](#scenario-access-workspace-from-studio) | N/D | <ul><li>Azure Active Directory</li><li>Azure Front Door</li><li>Service Azure Machine Learning</li></ul> | Vous devrez peut-être utiliser un serveur DNS personnalisé. Pour plus d’informations, consultez [Utiliser votre espace de travail avec un DNS personnalisé](how-to-custom-dns.md). | 
| [Utiliser AutoML, le concepteur, le jeu de données et le magasin de données de Studio](#scenario-use-automl-designer-dataset-and-datastore-from-studio) | N/D | N/D | <ul><li>Configuration du principal de service de l’espace de travail</li><li>Autoriser l’accès à partir des services Azure approuvés</li></ul>Pour plus d’informations, consultez [Comment sécuriser un espace de travail dans un réseau virtuel](how-to-secure-workspace-vnet.md#secure-azure-storage-accounts). | 
| [Utiliser l’instance de calcul et le cluster de calcul](#scenario-use-compute-instance-and-compute-cluster) | <ul><li>Service Azure Machine Learning sur le port 44224</li><li>Service de gestion de Azure Batch sur les ports 29876-29877</li></ul> | <ul><li>Azure Active Directory</li><li>Azure Resource Manager</li><li>Service Azure Machine Learning</li><li>Compte Stockage Azure</li><li>Azure Key Vault</li></ul> | Si vous utilisez un pare-feu, créez des itinéraires définis par l’utilisateur. Pour plus d’informations, consultez [Configurer le trafic entrant et sortant](how-to-access-azureml-behind-firewall.md). | 
| [Utiliser Azure Kubernetes Service](#scenario-use-azure-kubernetes-service) | N/D | Pour plus d’informations sur la configuration sortante pour AKS, consultez [Comment déployer sur Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md#understand-connectivity-requirements-for-aks-inferencing-cluster). | Configurez l’équilibreur de charge interne. Pour plus d’informations, consultez [Guide pratique pour déployer sur Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md#understand-connectivity-requirements-for-aks-inferencing-cluster). | 
| [Utiliser des images Docker gérées par Azure Machine Learning](#scenario-use-docker-images-managed-by-azure-ml) | N/D | <ul><li>Registre de conteneurs Microsoft</li><li>Registre de conteneurs global `viennaglobal.azurecr.io`</li></ul> | Si Azure Container Registry pour votre espace de travail se trouve derrière le réseau virtuel, configurez l’espace de travail pour utiliser un cluster de calcul pour générer des images. Pour plus d’informations, consultez [Comment sécuriser un espace de travail dans un réseau virtuel](how-to-secure-workspace-vnet.md#enable-azure-container-registry-acr). | 

> [!IMPORTANT]
> Azure Machine Learning utilise plusieurs comptes de stockage. Chacun d’eux stocke des données différentes et a un objectif différent :
>
> * __Votre stockage__ : le ou les comptes Stockage Azure de votre abonnement Azure sont utilisés pour stocker vos données et artefacts tels que les modèles, les données d’apprentissage, les journaux de formation et les scripts Python. Par exemple, le compte de stockage _par défaut_ de votre espace de travail se trouve dans votre abonnement. L’instance de calcul Azure Machine Learning et les clusters de calcul accèdent aux données de __fichier__ et d’__objet blob__ dans ce stockage via les ports 445 (SMB) et 443 (HTTPS).
> 
>    Lors de l’utilisation d’une instance de calcul ou d’un cluster de calcul, votre compte de stockage est monté en tant que partage de fichiers à l’aide du protocole SMB. C’est ainsi que l’instance de calcul/cluster accède à vos données.
>
> * __Stockage Microsoft__ : l’instance de calcul et les clusters de calcul Azure Machine Learning s’appuient sur Azure Batch et sur l’accès au stockage situé dans un abonnement Microsoft. Ce stockage est utilisé uniquement pour la gestion du cluster ou de l’instance de calcul. Aucune de vos données n’est stockée ici. L’instance de calcul et le cluster de calcul accèdent aux données de l’__objet BLOB__, de la __table__ et de la __file d’attente__ dans ce stockage, à l’aide du port 443 (HTTPS).
>
> Machine Learning stocke également les métadonnées dans une instance d’Azure Cosmos DB. Par défaut, cette instance est hébergée dans un abonnement Microsoft et gérée par Microsoft. Vous pouvez éventuellement utiliser une instance de Azure Cosmos DB dans votre abonnement Azure. Pour plus d’informations, consultez [Chiffrement de données avec Azure Machine Learning](concept-data-encryption.md#azure-cosmos-db).

## <a name="scenario-access-workspace-from-studio"></a>Scénario : Accéder à l’espace de travail à partir de Studio

> [!NOTE]
> Les informations contenues dans cette section sont spécifiques à l’utilisation de l’espace de travail à partir d’Azure Machine Learning Studio. Si vous utilisez le kit de développement logiciel (SDK) Azure Machine Learning, l’API REST, l’interface CLI ou Visual Studio Code, les informations de cette section ne s’appliquent pas à vous.

Lorsque vous accédez à votre espace de travail à partir de Studio, les flux de trafic réseau sont les suivants :

* Pour s’authentifier aux ressources, __Azure Active Directory__ est utilisé.
* Pour les opérations de gestion et de déploiement, __Azure Resource Manager__ est utilisé.
* Pour les tâches spécifiques à Azure Machine Learning, le service __Azure Machine Learning__ est utilisé
* Pour accéder à Azure Machine Learning Studio (https://ml.azure.com), __Azure FrontDoor__ est utilisé.
* Pour la plupart des opérations de stockage, le trafic transite par le point de terminaison privé du stockage par défaut pour votre espace de travail. Les exceptions sont abordées dans la section [Utiliser AutoML, le concepteur, le jeu de données et le magasin de données](#scenario-use-automl-designer-dataset-and-datastore-from-studio).
* Vous devez également configurer une solution DNS qui vous permet de résoudre les noms des ressources au sein du réseau virtuel. Pour plus d’informations, consultez [Utiliser votre espace de travail avec un DNS personnalisé](how-to-custom-dns.md).

:::image type="content" source="./media/concept-secure-network-traffic-flow/workspace-traffic-studio.png" alt-text="Diagramme du trafic réseau entre le client et l’espace de travail lors de l’utilisation de Studio":::

## <a name="scenario-use-automl-designer-dataset-and-datastore-from-studio"></a>Scénario : Utiliser AutoML, le concepteur, le jeu de données et le magasin de données de Studio

Les fonctionnalités suivantes d’Azure Machine Learning Studio utilisent le _profilage des données_ :

* Jeu de données : Explorez le jeu de données depuis Studio.
* Concepteur : Visualisez les données de sortie du module.
* AutoML : Affichez un aperçu ou un profil de données et choisissez une colonne cible.
* L’étiquetage

le profilage des données dépend de la capacité du service géré Azure Machine Learning à accéder au compte Stockage Azure par défaut de votre espace de travail. Le service géré _n’existe pas dans votre réseau virtuel_, il ne peut donc pas accéder directement au compte de stockage dans le réseau virtuel. Au lieu de cela, l’espace de travail utilise un principal de service pour accéder au stockage.

> [!TIP]
> Vous pouvez fournir un principal de service lors de la création de l’espace de travail. Si vous ne le faites pas, un principal est créé pour vous et porte le même nom que votre espace de travail.

Pour autoriser l’accès au compte de stockage, configurez-le pour autoriser une __instance de ressource__ pour votre espace de travail, ou sélectionnez __Autoriser les services Azure de la liste des services approuvés à accéder à ce compte de stockage__. Ce paramètre permet au service géré d’accéder au stockage via le réseau du centre de données Azure. 

Ensuite, ajoutez le principal de service de l’espace de travail au rôle __Lecteur__ sur le point de terminaison privé du compte de stockage. Ce rôle est utilisé pour vérifier les informations de sous-réseau de stockage et d’espace de travail. Si les valeurs sont identiques, l’accès est autorisé. Enfin, le principal du service exige également l’accès au compte de stockage pour les __Contributeurs de données d’objet blob__.

Pour plus d’informations, consultez la section Compte Stockage Azure de [Comment sécuriser un espace de travail dans un réseau virtuel](how-to-secure-workspace-vnet.md#secure-azure-storage-accounts).

:::image type="content" source="./media/concept-secure-network-traffic-flow/storage-traffic-studio.png" alt-text="Diagramme du trafic entre le client, le profilage des données et le stockage":::

## <a name="scenario-use-compute-instance-and-compute-cluster"></a>Scénario : Utiliser une instance de calcul et un cluster de calcul

L’instance de calcul et le cluster de calcul Azure Machine Learning sont des services gérés hébergés par Microsoft. Ils s’appuient sur le service Azure Batch. Bien qu’ils existent dans un environnement géré Microsoft, ils sont également injectés dans votre réseau virtuel.

Lorsque vous créez une instance de calcul ou un cluster de calcul, les ressources suivantes sont également créées dans votre réseau virtuel :

* Un groupe de sécurité réseau avec les règles de trafic sortant requises. Ces règles autorisent l’accès __entrant__ depuis le service Azure Machine Learning (TCP sur le port 44224) et Azure Batch (TCP sur les ports 29876-29877).

    > [!IMPORTANT]
    > Si vous utilisez un pare-feu pour bloquer l’accès à Internet dans le réseau virtuel, vous devez configurer le pare-feu pour autoriser ce trafic. Par exemple, avec le pare-feu Azure, vous pouvez créer des itinéraires définis par l’utilisateur. Pour plus d’informations, consultez [Comment utiliser Azure Machine Learning avec un pare-feu](how-to-access-azureml-behind-firewall.md#inbound-configuration).

* Un équilibreur de charge avec une IP publique.

Autorisez également l’accès __sortant__ aux étiquettes de service suivantes. Pour chaque étiquette, remplacez `region` par la région Azure de votre instance de calcul :

* `Storage.region` : cet accès sortant est utilisé pour se connecter au compte Stockage Azure à l’intérieur du réseau virtuel géré par le service Azure Batch.
* `Keyvault.region` : cet accès sortant est utilisé pour se connecter au compte Azure Key Vault à l’intérieur du réseau virtuel géré par le service Azure Batch.

L’accès aux données à partir de votre instance ou cluster de calcul passe par le point de terminaison privé du compte Stockage de votre réseau virtuel.

Si vous utilisez Visual Studio Code sur une instance de calcul, vous devez autoriser le trafic sortant. Pour plus d’informations, consultez [Comment utiliser Azure Machine Learning avec un pare-feu](how-to-access-azureml-behind-firewall.md).

:::image type="content" source="./media/concept-secure-network-traffic-flow/compute-instance-and-cluster.png" alt-text="Diagramme du flux de trafic lors de l’utilisation d’une instance ou d’un cluster de calcul":::

## <a name="scenario-use-azure-kubernetes-service"></a>Scénario : Utiliser Azure Kubernetes Service

Pour plus d’informations sur la configuration sortante requise pour Azure Kubernetes Service, consultez la section relative à la configuration requise pour le [Déploiement dans Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md#understand-connectivity-requirements-for-aks-inferencing-cluster).

> [!NOTE]
> L’équilibreur de charge Azure Kubernetes Service n’est pas le même que l’équilibreur de charge créé par Azure Machine Learning. Si vous souhaitez héberger votre modèle en tant qu’application sécurisée, uniquement disponible sur le réseau virtuel, utilisez l’équilibreur de charge interne créé par Azure Machine Learning. Si vous souhaitez autoriser l’accès public, utilisez l’équilibreur de charge public créé par Azure Machine Learning.

Si votre modèle requiert une connectivité entrante ou sortante supplémentaire, par exemple à une source de données externe, utilisez un groupe de sécurité réseau ou votre pare-feu pour autoriser le trafic.

## <a name="scenario-use-docker-images-managed-by-azure-ml"></a>Scénario : Utiliser des images Docker gérées par Azure ML

Azure Machine Learning fournit des images Docker qui peuvent être utilisées pour former des modèles ou effectuer une inférence. Si vous ne spécifiez pas vos propres images, celles fournies par Azure Machine Learning sont utilisées. Ces images sont hébergées sur Microsoft Container Registry (MCR). Ils sont également hébergés sur une instance Azure Container Registry géorépliquée nommée `viennaglobal.azurecr.io`.

Si vous fournissez vos propres images Docker, par exemple sur une instance Azure Container Registry que vous fournissez, vous n’avez pas besoin de la communication sortante avec MCR ou `viennaglobal.azurecr.io`.

> [!TIP]
> Si votre Azure Container Registry est sécurisé dans le réseau virtuel, il ne peut pas être utilisé par Azure Machine Learning pour générer des images Docker. Au lieu de cela, vous devez désigner un cluster de calcul Azure Machine Learning pour générer des images. Pour plus d’informations, consultez [Comment sécuriser un espace de travail dans un réseau virtuel](how-to-secure-workspace-vnet.md#enable-azure-container-registry-acr).

:::image type="content" source="./media/concept-secure-network-traffic-flow/azure-machine-learning-docker-images.png" alt-text="Diagramme du flux de trafic lors de l’utilisation d’images Docker fournies":::
## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris comment le trafic réseau circule dans une configuration sécurisée, apprenez-en plus sur la sécurisation d’Azure ML dans un réseau virtuel en lisant l’article [Vue d’ensemble de l’isolement et de la confidentialité des réseaux virtuels](how-to-network-security-overview.md).

Pour plus d’informations sur les meilleures pratiques, consultez l’article [Bonnes pratiques pour la sécurité de l’entreprise avec Azure Machine Learning](/azure/cloud-adoption-framework/ready/azure-best-practices/ai-machine-learning-enterprise-security).