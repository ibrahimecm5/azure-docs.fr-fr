---
title: Nœuds d’informatique confidentielle sur AKS (Azure Kubernetes Service)
description: Nœuds d’informatique confidentielle sur AKS
services: virtual-machines
author: agowdamsft
ms.service: container-service
ms.subservice: confidential-computing
ms.topic: overview
ms.date: 11/04/2021
ms.author: amgowda
ms.custom: ignite-fall-2021
ms.openlocfilehash: f2a0a6452b978b6924574f1499916a0781413a24
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132290226"
---
# <a name="confidential-computing-nodes-on-azure-kubernetes-service"></a>Nœuds d’informatique confidentielle sur Azure Kubernetes Service

L’[informatique confidentielle Azure](overview.md) vous permet de protéger vos données sensibles pendant leur utilisation. L’infrastructure d’informatique confidentielle sous-jacente protège ces données des autres applications, administrateurs et fournisseurs de cloud avec un environnement de conteneur d’exécution de confiance basé sur le matériel. L’ajout de nœuds d’informatique confidentielle permet de cibler l’application de conteneur pour qu’elle s’exécute dans un environnement isolé, bénéficiant d’une protection matérielle et pouvant être attesté.

## <a name="overview"></a>Vue d’ensemble

Azure Kubernetes Service (AKS) prend en charge l’ajout de [nœuds d’informatique confidentielle DCsv2](confidential-computing-enclaves.md) reposant sur la technologie Intel SGX. Ces nœuds vous permettent d’exécuter des charges de travail sensibles au sein d’un environnement d’exécution de confiance (environnement TEE) basé sur le matériel. L’environnement TEE permet au code utilisateur des conteneurs d’allouer des régions de mémoire privées pour exécuter directement le code avec le processeur. Ces régions de mémoire privées qui s’exécutent directement avec le processeur sont appelées enclaves. Les enclaves aident à protéger la confidentialité des données, l’intégrité des données et l’intégrité du code contre les autres processus en cours d’exécution sur les mêmes nœuds. De plus, le modèle d’exécution Intel SGX supprime les couches intermédiaires du système d’exploitation invité, du système d’exploitation hôte et de l’hyperviseur, réduisant ainsi la surface d’exposition aux attaques. Le modèle d’*exécution isolée par conteneur basée sur le matériel* dans un nœud permet aux applications de s’exécuter directement avec le processeur, tout en conservant le bloc spécial de mémoire chiffré par conteneur. Les nœuds d’informatique confidentielle avec des conteneurs confidentiels sont un excellent ajout à votre stratégie de conteneur avec défense en profondeur et de planification d’une sécurité Confiance Zéro.

:::image type="content" source="./media/confidential-nodes-aks-overview/sgx-aks-node.png" alt-text="Graphique du nœud de calcul confidentiel AKS avec des conteneurs confidentiels comprenant du code et des données sécurisés.":::

## <a name="aks-confidential-nodes-features"></a>Fonctionnalités des nœuds confidentiels AKS

- Isolement des conteneurs au niveau des processus, basée sur le matériel, par le biais de l’environnement TEE Intel SGX 
- Clusters de pools de nœuds hétérogènes (combinaison de pools de nœuds confidentiels et non confidentiels)
- Planification de pods basée sur la mémoire EPC (Encrypted Page Cache) (nécessite un module complémentaire)
- Pilote Intel SGX DCAP préinstallé
- Mise à l’échelle automatique des clusters et mise à l’échelle automatique des pods horizontaux en fonction de la consommation processeur
- Prise en charge des conteneurs Linux par le biais de nœuds Worker sur les machines virtuelles Ubuntu 18.04 de deuxième génération

## <a name="confidential-computing-add-on-for-aks"></a>Module complémentaire d’informatique confidentielle pour AKS
La fonctionnalité de module complémentaire active des fonctionnalités supplémentaires sur AKS lors de l’exécution de pools de nœuds d’informatique confidentielle sur le cluster. Ce module complémentaire active les fonctionnalités ci-dessous.

#### <a name="azure-device-plugin-for-intel-sgx"></a>Plug-in d’appareil Azure pour Intel SGX <a id="sgx-plugin"></a>

Le plug-in d’appareil implémente l’interface de plug-in d’appareil Kubernetes pour la mémoire EPC et expose les pilotes d’appareil à partir des nœuds. Concrètement, ce plug-in fait de la mémoire EPC un autre type de ressource dans Kubernetes. Les utilisateurs peuvent spécifier des limites sur cette ressource de la même façon que sur les autres ressources. En plus de la fonction de planification, le plug-in d’appareil facilite l’attribution d’autorisations de pilote d’appareil Intel SGX aux conteneurs de charges de travail confidentiels. Avec ce plug-in, un développeur n’a pas besoin de monter les volumes de pilote Intel SGX dans les fichiers de déploiement. Un exemple d’implémentation d’un déploiement basé sur la mémoire EPC (`kubernetes.azure.com/sgx_epc_mem_in_MiB`) est disponible [ici](https://github.com/Azure-Samples/confidential-computing/blob/main/containersamples/helloworld/helm/templates/helloworld.yaml)


## <a name="programming-models"></a>Modèles de programmation

### <a name="confidential-containers"></a>Conteneurs confidentiels

Les [conteneurs confidentiels](confidential-containers.md) vous permettent d’exécuter les applications conteneur non modifiées existantes de la plupart des runtimes des **langages de programmation les plus courants** (Python, Node, Java, etc.) de manière confidentielle. Ce modèle d’empaquetage n’a pas besoin de modifications de code source ou de recompilation. Il s’agit de la méthode la plus rapide pour la confidentialité, que vous pouvez obtenir en empaquetant vos conteneurs Docker standard avec des projets open source ou des solutions partenaires Azure. Dans ce modèle d’empaquetage et d’exécution, toutes les parties de l’application conteneur sont chargées dans la limite de confiance (enclave). Ce modèle fonctionne bien pour les applications conteneur prêtes à l’emploi disponibles sur le marché ou pour les applications personnalisées qui s’exécutent sur des nœuds à usage général.

### <a name="enclave-aware-containers"></a>Conteneurs reconnaissant les enclaves
Les nœuds d’informatique confidentielle sur AKS prennent également en charge les conteneurs qui sont programmés pour s’exécuter dans une enclave afin d’utiliser un **jeu d’instructions spécial** disponible à partir du processeur. Ce modèle de programmation permet un contrôle plus étroit de votre flux d’exécution et nécessite l’utilisation de kits SDK et de frameworks spéciaux. Ce modèle de programmation offre un contrôle maximal du flux d’application avec une base TCB (Trusted Computing Base) réduite. Le développement de conteneurs prenant en charge les enclaves implique des parties approuvées et non approuvées dans l’application conteneur, ce qui vous permet de gérer la mémoire normale et la mémoire EPC dans laquelle l’enclave est exécutée. [Découvrez-en plus](enclave-aware-containers.md) sur les conteneurs reconnaissant les enclaves.

## <a name="next-steps"></a>Étapes suivantes

[Déployer un cluster AKS avec des nœuds d’informatique confidentielle](./confidential-enclave-nodes-aks-get-started.md)

[Démarrer rapidement avec des exemples de conteneurs confidentiels](https://github.com/Azure-Samples/confidential-container-samples)

[Liste des références SKU des machines virtuelles DCsv2 Intel SGX Confidential](../virtual-machines/dcv2-series.md)

[Liste des références SKU des machines virtuelles DCsv3 Intel SGX Confidential](../virtual-machines/dcv3-series.md)

[Session de webinaire sur la défense en profondeur avec des conteneurs confidentiels](https://www.youtube.com/watch?reload=9&v=FYZxtHI_Or0&feature=youtu.be)

<!-- LINKS - external -->
[Azure Attestation]: ../attestation/index.yml


<!-- LINKS - internal -->
[DC Virtual Machine]: /confidential-computing/virtual-machine-solutions-sgx.md
