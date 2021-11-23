---
title: Calcul de périphérie multi-accès privé Azure
description: Découvrez la solution Calcul de périphérie multi-accès privé Azure (MEC) qui regroupe un portefeuille de services de calcul, de mise en réseau et d’applications Microsoft managés à partir du cloud.
author: niravi-msft
ms.service: private-multi-access-edge-compute-mec
ms.topic: overview
ms.date: 06/16/2021
ms.author: kumud
ms.openlocfilehash: 6d85a42b3ff23f4bead8abd3187b9a899ebb12c3
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132277939"
---
# <a name="what-is-azure-private-multi-access-edge-compute"></a>Qu’est-ce que le calcul de périphérie multi-accès privé Azure ?

Calcul de périphérie multi-accès privé Azure (MEC) est une solution qui regroupe un portefeuille de services de calcul, de mise en réseau et d’applications Microsoft managés à partir du cloud. La solution permet aux opérateurs et aux intégrateurs système de fournir des performances élevées, une connectivité à faible latence et des applications IoT à la périphérie de l’entreprise pour la prochaine vague de transformation numérique de l’entreprise. 

Le MEC privé Azure est une évolution de la zone de périphérie privée, développant l’étendue des possibilités d’une plate-forme et d’un service unique à une solution qui tire parti de plusieurs plateformes et fonctionnalités. Ces fonctionnalités incluent les services et les applications de périphérie, les fonctions de réseau de périphérie, les options de calcul de périphérie et les périphériques et radios de périphérie. En traitant les données plus près du périphérique final, ces fonctionnalités permettent d’améliorer les scénarios d’utilisateurs sensibles à la latence et au débit, tels que l’analyse vidéo, la robotique en temps réel et les cas d’utilisation mixtes de l’IoT à l’échelle mondiale. Les clients et les partenaires peuvent bénéficier d’un ensemble complet de services Azure et de composants de technologie de l’écosystème pour générer, déployer et gérer rapidement des solutions avec simplicité. 

## <a name="benefits-to-customers-and-partners"></a>Avantages pour les clients et partenaires
- Clients Entreprise :
    - Accès à l’ensemble du portefeuille des services Azure, des ISV et écosystème des développeurs.
    - Choix de solutions approuvées et adaptées à l’industrie.
    - Pile technologique entièrement intégrée à partir d’une empreinte 1U.

- Partenaires Telco et intégrateur système :
    - Facile à mettre en place, déployer, gérer et monétiser la 5G et Edge pour leur client.
    - Choix d’un écosystème organisé de produits, de services cloud et d’applications.
    - Accès à l’écosystème développeur Azure.

- ISV de l’application :
    - Plateforme complète pour développer des applications à latence très faible. 
    - Expérience développeur cohérente avec les outils et les ressources établis.
    - Mettre à l’échelle la distribution via la Place de marché Microsoft

## <a name="microsoft-capabilities"></a>Fonctionnalités Microsoft
Le MEC privé Azure comprend plusieurs fonctionnalités de Microsoft. Celles-ci incluent une combinaison de produits de fonction réseau, de services de gestion et d’infrastructure matérielle et services. 

### <a name="azure-network-functions-offered-via-marketplace"></a>Fonctions réseau Azure offertes via la Place de marché

**Base de fusion Metaswitch** : la base de fusion est une solution de base entièrement conteneurisée 5G, qui prend en charge toutes les fonctions réseau nécessaires à la connectivité entre les appareils IoT connectés via la radio le 4G ou le 5G et le réseau de données. La solution offre certains des principaux avantages suivants :
 - déploiement et approvisionnement faciles à partir du Portail Place de marché Azure sur Azure Stack Edge.
 - hautes performances de 25 Gbits/s de débit dans une empreinte de calcul exceptionnellement peu élevée de quatre cœurs physiques.
 - prise en charge des types d’accès autonome 4G et 5G.
 - outils intégrés pour les tableaux de bord d’indicateurs de performance clés et d’assurance de service de l’entreprise. 
 
La base de fusion permet aux fournisseurs de logiciels indépendants de déployer des applications sur le même nœud Azure Stack Edge pour les applications IoT Edge telles que l’analytique vidéo en direct. 

**Service réseau privé affirmé** : le service réseau privé affirmé est une Place de marché Azure qui offre un service réseau privé managé pour les opérateurs de réseau mobile et le fournisseur de services gérés qui souhaitent fournir des offres de service managées de 4G et 5G aux entreprises. APNS permet aux opérateurs de fournir aux entreprises un réseau mobile privé de classe opérateur qui leur permet d’exécuter et d’exploiter des applications stratégiques nécessitant une faible latence, une bande passante élevée et une sécurité de bout en bout. Il s’agit d’un opérateur de réseau mobile intégré qui offre une mobilité totale entre les réseaux d’opérateurs privés et publics. Grâce à son automatisation et à ses opérations simplifiées, APNS offre une évolutivité entre des milliers d’emplacements de périphérie d’entreprise et utilise Azure pour offrir une sécurité renforcée sur les réseaux privés et les applications d’entreprise. Il offre la flexibilité de déploiement de l’ensemble du cœur mobile à la périphérie du réseau, tout cela dans le cloud ou en mode hybride avec le plan de contrôle sur le cloud et le plan utilisateur à la périphérie de l’entreprise. 

### <a name="azure-management-services"></a>Services de gestion Azure

**Gestionnaire de fonctions réseau (NFM) Azure** : Azure NFM permet de déployer des fonctions réseau à la périphérie à l’aide d’outils et d’interfaces Azure cohérents. Utilisez ce service pour déployer des fonctions de réseau de base de paquets et SD-WAN sur Azure Stack Edge. Pour plus d’informations, consultez [Gestionnaire de fonctions réseau Azure](../network-function-manager/overview.md).

**Kubernetes avec Arc** : grâce à Kubernetes avec Azure Arc, vous pouvez joindre et configurer des clusters Kubernetes situés à l’intérieur ou à l’extérieur d’Azure. Vous pouvez surveiller et gérer à grande échelle à l’aide de déploiements basés sur des stratégies et appliquer, à grande échelle également des configurations de sécurité cohérentes. Kubernetes avec Azure Arc fonctionne pour tous les clusters Kubernetes certifiés CNCF (Cloud Native Computing Foundation). Pour plus d’informations, consultez [Azure Arc](https://azure.microsoft.com/services/azure-arc/).

### <a name="azure-stack-hardware-and-services"></a>Matériel et services Azure Stack
**Azure Stack Edge** : Azure Stack Edge offre un portefeuille d’appareils qui apportent des fonctions de calcul, de stockage et d’intelligence à la bonne périphérie, dans laquelle les données sont créées. Les appareils sont des appliances montables en rack 1U, livrées avec des GPU T4 1-2 NVIDIA. Azure IoT Edge vous permet de déployer et de gérer des conteneurs à partir de IoT Hub et de les intégrer à des solutions Azure IoT sur Azure Stack Edge. La référence SKU Azure Stack Edge Pro est certifiée pour exécuter des fonctions réseau à la périphérie. Pour plus d’informations, consultez [Azure Stack Edge](https://azure.microsoft.com/products/azure-stack/edge/).

**Azure Stack HCI** : Azure Stack HCI est un nouveau système d’exploitation de l’infrastructure hyper-convergée (HCI) fourni sous la forme d’un service Azure qui fournit les dernières mises à jour en matière de sécurité, de performances et de fonctionnalités. Déployez et exécutez des machines virtuelles Windows et Linux dans votre centre de données ou à l’aide de vos outils et processus existants. Étendez votre centre de données dans le cloud avec la Sauvegarde Azure, Azure Monitor et Microsoft Defender pour le cloud. Pour plus d’informations, consultez [Azure Stack HCI](https://azure.microsoft.com/products/azure-stack/hci/).

### <a name="application-services"></a>Services d’application

**Runtime Azure IoT Edge** : le runtime Azure IoT Edge permet de gérer et de déployer des charges de travail cloud sur des appliances de calcul de périphérie à l’aide des mêmes outils et postures de sécurité que les charges de travail natives du cloud. Pour plus d’informations, consultez [Runtime Azure IoT Edge](/windows/ai/windows-ml-container/iot-edge-runtime).

**Azure IoT Hub** : runtime Azure IoT Edge : le runtime Azure IoT Edge permet de gérer et de déployer des charges de travail cloud sur des appliances de calcul de périphérie à l’aide des mêmes outils et postures de sécurité que les charges de travail natives du cloud. Pour plus d’informations, consultez [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/).

**Azure IoT central** : Azure IoT Central est une plate-forme d’application managée qui permet la gestion des appareils et l’ingestion des données en tant que service avec un modèle de tarification prévisible et une mise à l’échelle globale intégrée. Pour plus d’informations, consultez [Azure IoT Central](https://azure.microsoft.com/services/iot-central/).

**Azure Digital Twins** : Azure Digital Twins permet aux capteurs d’appareils d’être modélisés dans leur contexte métier, en tenant compte des relations spatiales, des modèles d’utilisation et d’autres contextes métier qui transforment une flotte d’appareils en un réplica numérique d’une ressource ou d’un environnement physique. Pour plus d’informations, consultez [Azure Digital Twins](https://azure.microsoft.com/services/digital-twins/).

## <a name="next-steps"></a>Étapes suivantes
- En savoir plus sur [Base de fusion Metaswitch](metaswitch-fusion-core-overview.md)
- En savoir plus sur [Service réseau privé affirmé](affirmed-private-network-service-overview.md)
