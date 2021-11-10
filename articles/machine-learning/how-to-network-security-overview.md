---
title: Sécuriser les ressources d’espace de travail à l’aide de réseaux virtuels
titleSuffix: Azure Machine Learning
description: Sécurisez les ressources d’espace de travail Azure Machine Learning et les environnements de calcul à l’aide d’un réseau virtuel Azure isolé.
services: machine-learning
ms.service: machine-learning
ms.subservice: enterprise-readiness
ms.reviewer: larryfr
ms.author: peterlu
author: peterclu
ms.date: 10/29/2021
ms.topic: how-to
ms.custom: devx-track-python, references_regions, contperf-fy21q1,contperf-fy21q4,FY21Q4-aml-seo-hack, security
ms.openlocfilehash: 9d47a19e4890ac6e81a86aeb04e6a139be555599
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132061189"
---
<!-- # Virtual network isolation and privacy overview -->
# <a name="secure-azure-machine-learning-workspace-resources-using-virtual-networks-vnets"></a>Sécuriser les ressources d’espace de travail Azure Machine Learning à l’aide de réseaux virtuels

Sécurisez les ressources d’espace de travail Azure Machine Learning et les environnements de calcul à l’aide de réseaux virtuels. Cet article utilise un exemple de scénario pour vous montrer comment configurer un réseau virtuel complet.

> [!TIP]
> Cet article fait partie d’une série sur la sécurisation d’un workflow Azure Machine Learning. Consultez les autres articles de cette série :
>
> * [Sécuriser les ressources d’espace de travail](how-to-secure-workspace-vnet.md)
> * [Sécuriser l’environnement d’entraînement](how-to-secure-training-vnet.md)
> * [Sécuriser l’environnement d’inférence](how-to-secure-inferencing-vnet.md)
> * [Activer les fonctionnalités du studio](how-to-enable-studio-virtual-network.md)
> * [Utiliser le DNS personnalisé](how-to-custom-dns.md)
> * [Utiliser un pare-feu](how-to-access-azureml-behind-firewall.md)

## <a name="prerequisites"></a>Prérequis

Cet article part du principe que vous connaissez déjà les sujets suivants :
+ [Réseaux virtuels Azure](../virtual-network/virtual-networks-overview.md)
+ [Réseaux IP](../virtual-network/ip-services/public-ip-addresses.md)
+ [Espace de travail Azure Machine Learning avec point de terminaison privé](how-to-configure-private-link.md)
+ [Groupes de sécurité réseau (NSG)](../virtual-network/network-security-groups-overview.md)
+ [Pare-feu de réseau](../firewall/overview.md)
## <a name="example-scenario"></a>Exemple de scénario

Dans cette section, vous allez découvrir comment on peut configurer un scénario de réseau courant pour sécuriser la communication Azure Machine Learning avec des adresses IP privées.

Le tableau suivant compare l’accès des services aux différentes parties d’un réseau Azure Machine Learning avec et sans VNet :

| Scénario | Espace de travail | Ressources associées | Environnement de calcul pour l’entraînement | Environnement de calcul pour l’inférence |
|-|-|-|-|-|-|
|**Aucun réseau virtuel**| Adresse IP publique | Adresse IP publique | Adresse IP publique | Adresse IP publique |
|**Espace de travail public, toutes les autres ressources d’un réseau virtuel** | Adresse IP publique | Adresse IP publique (point de terminaison de service) <br> **- ou -** <br> Adresse IP privée (point de terminaison privé) | IP privée | IP privée  |
|**Protéger les ressources dans un réseau virtuel**| Adresse IP privée (point de terminaison privé) | Adresse IP publique (point de terminaison de service) <br> **- ou -** <br> Adresse IP privée (point de terminaison privé) | IP privée | IP privée  | 

* **Espace de travail** - Créer un point de terminaison privé pour votre espace de travail. Le point de terminaison privé connecte l’espace de travail au réseau virtuel par le biais de plusieurs adresses IP privées.
    * **Accès public** : vous pouvez activer l’accès public pour un espace de travail sécurisé (facultatif).
* **Ressource associée** - Utilisez des points de terminaison de service ou des points de terminaison privés pour vous connecter aux ressources de l'espace de travail comme Azure storage, Azure Key Vault. Pour Azure Container Services, utilisez un point de terminaison privé.
    * Les **points de terminaison de service** fournissent l’identité de votre réseau virtuel au service Azure. Une fois que vous avez activé les points de terminaison de service dans votre réseau virtuel, vous pouvez ajouter une règle de réseau virtuel afin de sécuriser les ressources du service Azure pour votre réseau virtuel. Les points de terminaison de service utilisent des adresses IP publiques.
    * Les **points de terminaison privés** sont des interfaces réseau qui vous connectent de manière sécurisée à un service alimenté par Azure Private Link. Le point de terminaison privé utilise une adresse IP privée de votre réseau virtuel, plaçant de fait le service dans votre réseau virtuel.
* **Accès au calcul de formation** : accédez aux cibles de calcul de formation comme l’instance de calcul Azure Machine Learning et les clusters de calcul Azure Machine Learning en toute sécurité avec des adresses IP publiques (préversion).
* **Accès au calcul d’inférence** : accédez à des clusters de calcul Azure Kubernetes Services (AKS) avec des adresses IP privées.


Les sections suivantes vous montrent comment sécuriser le scénario réseau décrit ci-dessus. Pour sécuriser votre réseau, vous devez :

1. Sécuriser l’[**espace de travail et les ressources associées**](#secure-the-workspace-and-associated-resources).
1. Sécuriser l’[**environnement d’entraînement**](#secure-the-training-environment).
1. Sécuriser l’[**environnement d’inférence**](#secure-the-inferencing-environment).
1. Facultatif : [**activer la fonctionnalité studio**](#optional-enable-studio-functionality).
1. Configurer les [**paramètres de pare-feu**](#configure-firewall-settings).
1. Configurer la [**résolution de noms DNS**](#custom-dns).

## <a name="public-workspace-and-secured-resources"></a>Espace de travail public et ressources sécurisées

Si vous souhaitez accéder à l’espace de travail via l’Internet public en maintenant toutes les ressources associées sécurisées dans un réseau virtuel, procédez comme suit :

1. Créez un [réseau virtuel Azure](../virtual-network/virtual-networks-overview.md) qui contiendra les ressources utilisées par l’espace de travail.
1. Utilisez l'__une__ des options suivantes pour créer un espace de travail accessible publiquement :

    * Créez un espace de travail Azure Machine Learning qui __n’utilise pas__ le réseau virtuel. Pour plus d’informations, consultez [Gestion des espaces de travail Azure Machine Learning](how-to-manage-workspace.md).
    * Créez un [espace de travail avec Private Link activé](how-to-secure-workspace-vnet.md#secure-the-workspace-with-private-endpoint) pour activer la communication entre votre réseau virtuel et votre espace de travail. Puis [activez l’accès public à l’espace de travail](#optional-enable-public-access).

1. Ajoutez les services suivants au réseau virtuel en utilisant un __point de terminaison de service__ _ou_ un __point de terminaison privé__. Autorisez également les services Microsoft approuvés à accéder à ces services :

    | Service | Informations sur le point de terminaison | Autoriser les informations approuvées |
    | ----- | ----- | ----- |
    | __Azure Key Vault__| [Point de terminaison de service](../key-vault/general/overview-vnet-service-endpoints.md)</br>[Point de terminaison privé](../key-vault/general/private-link-service.md) | [Autoriser les services Microsoft approuvés à contourner ce pare-feu](how-to-secure-workspace-vnet.md#secure-azure-key-vault) |
    | __Compte Stockage Azure__ | [Service et point de terminaison privé](how-to-secure-workspace-vnet.md?tabs=se#secure-azure-storage-accounts)</br>[Point de terminaison privé](how-to-secure-workspace-vnet.md?tabs=pe#secure-azure-storage-accounts) | [Accorder l’accès aux services Azure approuvés](../storage/common/storage-network-security.md#grant-access-to-trusted-azure-services) |
    | __Azure Container Registry__ | [Point de terminaison privé](../container-registry/container-registry-private-link.md) | [Autoriser les services approuvés](../container-registry/allow-access-trusted-services.md) |

1. Dans les propriétés du ou des comptes Stockage Azure de votre espace de travail, ajoutez l’adresse IP de votre client à la liste verte des paramètres du pare-feu. Pour plus d’informations, consultez [Configurer des pare-feu et réseaux virtuels](/azure/storage/common/storage-network-security#configuring-access-from-on-premises-networks).

## <a name="secure-the-workspace-and-associated-resources"></a>Sécuriser l’espace de travail et les ressources associées

Utilisez la procédure ci-dessous pour sécuriser votre espace de travail et les ressources associées. Ces étapes permettent à vos services de communiquer dans le réseau virtuel.

1. Créez un [réseau virtuel Azure](../virtual-network/virtual-networks-overview.md) qui contiendra l’espace de travail et les autres ressources.
1. Créez un [espace de travail avec Private Link activé](how-to-secure-workspace-vnet.md#secure-the-workspace-with-private-endpoint) pour activer la communication entre votre réseau virtuel et votre espace de travail.
1. Ajoutez les services suivants au réseau virtuel en utilisant un __point de terminaison de service__ _ou_ un __point de terminaison privé__. Autorisez également les services Microsoft approuvés à accéder à ces services :

    | Service | Informations sur le point de terminaison | Autoriser les informations approuvées |
    | ----- | ----- | ----- |
    | __Azure Key Vault__| [Point de terminaison de service](../key-vault/general/overview-vnet-service-endpoints.md)</br>[Point de terminaison privé](../key-vault/general/private-link-service.md) | [Autoriser les services Microsoft approuvés à contourner ce pare-feu](how-to-secure-workspace-vnet.md#secure-azure-key-vault) |
    | __Compte Stockage Azure__ | [Service et point de terminaison privé](how-to-secure-workspace-vnet.md?tabs=se#secure-azure-storage-accounts)</br>[Point de terminaison privé](how-to-secure-workspace-vnet.md?tabs=pe#secure-azure-storage-accounts) | [Accorder l’accès à partir d’instances de ressource Azure](../storage/common/storage-network-security.md#grant-access-from-azure-resource-instances-preview)</br>**or**</br>[Accorder l’accès aux services Azure approuvés](../storage/common/storage-network-security.md#grant-access-to-trusted-azure-services) |
    | __Azure Container Registry__ | [Point de terminaison privé](../container-registry/container-registry-private-link.md) | [Autoriser les services approuvés](../container-registry/allow-access-trusted-services.md) |


![Diagramme d’architecture représentant la manière dont l’espace de travail et les ressources associées communiquent entre eux par le biais des points de terminaison de service ou privés au sein d’un réseau virtuel](./media/how-to-network-security-overview/secure-workspace-resources.png)

Retrouvez les instructions détaillées relatives à cette procédure dans l’article [Sécuriser un espace de travail Azure Machine Learning](how-to-secure-workspace-vnet.md). 

### <a name="limitations"></a>Limites

La sécurisation de votre espace de travail et des ressources associées dans un réseau virtuel présente les limitations suivantes :
- Toutes les ressources doivent se trouver derrière le même réseau virtuel. Toutefois, des sous-réseaux peuvent être utilisés au sein d’un même réseau virtuel.

## <a name="secure-the-training-environment"></a>Sécuriser l’environnement d’entraînement

Dans cette section, vous allez voir comment sécuriser l’environnement d’entraînement dans Azure Machine Learning. Vous découvrirez également comment Azure Machine Learning effectue un travail d’entraînement pour comprendre le fonctionnement des configurations réseau.

Pour sécuriser l’environnement d’entraînement, procédez comme suit :

1. Créez une [instance de calcul et un cluster de calcul Azure Machine Learning dans le réseau virtuel](how-to-secure-training-vnet.md#compute-cluster) pour exécuter le travail d’entraînement.
1. [Autorisez les communications entrantes](how-to-secure-training-vnet.md#required-public-internet-access) pour permettre aux services de gestion de soumettre des tâches à vos ressources de calcul. 

![Diagramme d’architecture représentant la sécurisation des clusters et instances de calcul managés](./media/how-to-network-security-overview/secure-training-environment.png)

Retrouvez les instructions détaillées relatives à cette procédure dans l’article [Sécuriser un environnement d’entraînement](how-to-secure-training-vnet.md). 

### <a name="example-training-job-submission"></a>Envoi d’un exemple de travail d’entraînement 

Dans cette section, vous allez voir comment Azure Machine Learning communique de manière sécurisée entre les services pour soumettre un travail d’entraînement. Vous découvrirez comment toutes vos configurations s’associent pour sécuriser la communication.

1. Le client charge des scripts et des données d’entraînement vers des comptes de stockage sécurisés au moyen d’un point de terminaison de service ou privé.

1. Le client envoie un travail d’entraînement à l’espace de travail Azure Machine Learning via le point de terminaison privé.

1. Le service Azure Batch reçoit la tâche depuis l’espace de travail. Il soumet ensuite la tâche de formation à l’environnement de calcul via l’équilibreur de charge public pour la ressource de calcul. 

1. La ressource de calcul reçoit le travail et commence l’entraînement. La ressource de calcul accède aux comptes de stockage sécurisés pour télécharger les fichiers d’entraînement et charger la sortie.

### <a name="limitations"></a>Limites

- L’instance et les clusters Azure Compute doivent se trouver dans le même réseau virtuel, la même région et le même abonnement que l’espace de travail et les ressources associées. 

## <a name="secure-the-inferencing-environment"></a>Sécuriser l’environnement d’inférence

Dans cette section, vous allez découvrir les options disponibles pour sécuriser un environnement d’inférence. Nous vous recommandons d’utiliser des clusters Azure Kubernetes Services (AKS) pour les déploiements de production à grande échelle.

Deux options sont disponibles pour les clusters AKS dans un réseau virtuel :

- Déployer ou attacher un cluster AKS par défaut dans votre réseau virtuel.
- Attacher un cluster AKS privé à votre réseau virtuel.

Les **clusters AKS par défaut** ont un plan de contrôle avec des adresses IP publiques. Vous pouvez ajouter un cluster AKS par défaut à votre réseau virtuel pendant le déploiement ou attacher un cluster une fois celui-ci créé.

Les **clusters AKS privés** ont un plan de contrôle qui n’est accessible que par adresse IP privée. Les clusters AKS privés doivent être attachés une fois le cluster créé.

Retrouvez les instructions détaillées concernant l’ajout de clusters par défaut et privés dans l’article [Sécuriser un environnement d’inférence](how-to-secure-inferencing-vnet.md). 

Le diagramme de réseau suivant représente un espace de travail Azure Machine Learning sécurisé avec un cluster AKS privé attaché au réseau virtuel.

![Diagramme d’architecture représentant l’attachement d’un cluster AKS privé au réseau virtuel. Le plan de contrôle AKS est placé en dehors du réseau virtuel client](./media/how-to-network-security-overview/secure-inferencing-environment.png)

### <a name="limitations"></a>Limites

- L’espace de travail doit avoir un point de terminaison privé dans le même réseau virtuel que le cluster AKS. Par exemple, lors de l’utilisation de plusieurs points de terminaison privés avec l’espace de travail, un point de terminaison privé peut se trouver dans le réseau virtuel AKS et un autre dans le réseau virtuel qui contient des services de dépendance pour l’espace de travail.

## <a name="optional-enable-public-access"></a>Facultatif : Activer l’accès public

Vous pouvez sécuriser l’espace de travail derrière un réseau virtuel en utilisant un point de terminaison privé tout en autorisant l’accès via l’internet public. La configuration initiale est la même que pour [la sécurisation de l’espace de travail et des ressources associées](#secure-the-workspace-and-associated-resources). 

Après avoir sécurisé l’espace de travail avec un point de terminaison privé, procédez comme suit pour permettre aux clients de se développer à distance à l’aide du kit de développement logiciel (SDK) ou d’Azure Machine Learning studio :

1. [Activez l’accès public](how-to-configure-private-link.md#enable-public-access) à l’espace de travail.
1. [Configurez le pare-feu stockage Azure](../storage/common/storage-network-security.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#grant-access-from-an-internet-ip-range) pour autoriser la communication avec l’adresse IP des clients qui se connectent via l’internet public.

## <a name="optional-enable-studio-functionality"></a>Facultatif : activer la fonctionnalité studio

[Sécuriser l’espace de travail](#secure-the-workspace-and-associated-resources) > [Sécuriser l’environnement d’entraînement](#secure-the-training-environment) > [Sécuriser l’environnement d’inférence](#secure-the-inferencing-environment) > **Activer la fonctionnalité studio** > [Configurer les paramètres du pare-feu](#configure-firewall-settings)

Si votre stockage se trouve dans un réseau virtuel, vous devez suivre des étapes de configuration supplémentaires pour activer toutes les fonctionnalités dans Studio. Par défaut, les fonctionnalités suivantes sont désactivées :

* Aperçu des données dans Studio
* Visualisation des données dans le concepteur
* Déploiement d’un modèle dans le concepteur
* Envoi d’une expérience AutoML
* Démarrage d’un projet d’étiquetage

Pour activer toutes les fonctionnalités Studio, consultez [Utiliser le studio Azure Machine Learning dans un réseau virtuel](how-to-enable-studio-virtual-network.md).

### <a name="limitations"></a>Limites

[L’étiquetage des données assisté par ML](how-to-create-image-labeling-projects.md#use-ml-assisted-data-labeling) ne prend pas en charge les comptes de stockage par défaut derrière un réseau virtuel. À la place, utilisez un compte de stockage différent de celui par défaut pour l’étiquetage des données assisté par ML. 

> [!TIP]
> Tant qu’il ne s’agit pas du compte de stockage par défaut, le compte utilisé par l’étiquetage des données peut être sécurisé derrière le réseau virtuel. 

## <a name="configure-firewall-settings"></a>Configurer les paramètres de pare-feu

Configurez votre pare-feu pour contrôler le trafic entre les ressources de votre espace de travail Azure Machine Learning et l’Internet public. Nous recommandons d’utiliser le pare-feu Azure, mais vous pouvez utiliser d’autres produits de pare-feu. 

Pour plus d’informations sur les paramètres de pare-feu, consultez [Utiliser un espace de travail derrière un pare-feu](how-to-access-azureml-behind-firewall.md).

## <a name="custom-dns"></a>Système DNS personnalisé

Si vous devez utiliser une solution DNS personnalisée pour votre réseau virtuel, vous devez ajouter des enregistrements d’hôte pour votre espace de travail.

Pour plus d’informations sur les noms de domaine et les adresses IP requis, consultez [Comment utiliser un espace de travail avec un serveur DNS personnalisé](how-to-custom-dns.md).

## <a name="next-steps"></a>Étapes suivantes

Cet article fait partie d’une série sur la sécurisation d’un workflow Azure Machine Learning. Consultez les autres articles de cette série :

* [Sécuriser les ressources d’espace de travail](how-to-secure-workspace-vnet.md)
* [Sécuriser l’environnement d’entraînement](how-to-secure-training-vnet.md)
* [Sécuriser l’environnement d’inférence](how-to-secure-inferencing-vnet.md)
* [Activer les fonctionnalités du studio](how-to-enable-studio-virtual-network.md)
* [Utiliser le DNS personnalisé](how-to-custom-dns.md)
* [Utiliser un pare-feu](how-to-access-azureml-behind-firewall.md)
