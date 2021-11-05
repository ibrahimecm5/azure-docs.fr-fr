---
title: Qu’est-ce qu’un registre connecté ?
description: Vue d’ensemble et scénarios de la fonctionnalité de registre connecté d’Azure Container Registry
ms.author: memladen
ms.service: container-registry
ms.topic: overview
ms.date: 10/21/2021
ms.custom: references_regions, ignite-fall-2021
ms.openlocfilehash: ccc80f2ce5fb8393824686101d2069beee58efa4
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131028979"
---
# <a name="what-is-a-connected-registry"></a>Qu’est-ce qu’un registre connecté ? 

Dans cet article, vous découvrez la fonctionnalité de *registre connecté* d’[Azure Container Registry](container-registry-intro.md). Un registre connecté est un réplica local ou distant qui synchronise des images conteneur et d’autres artefacts OCI avec votre registre de conteneurs Azure basé sur le cloud. Utilisez un registre connecté pour accélérer l’accès aux artefacts de registre locaux et pour créer des scénarios avancés, par exemple, qui utilisent des appareils [IoT Edge imbriqués](../iot-edge/tutorial-nested-iot-edge.md).

> [!NOTE]
> Le registre connecté est une fonctionnalité en préversion du niveau de service **Premium** des registres de conteneurs, qui est soumise à des [limitations](#limitations). Pour plus d’informations sur les niveaux de service et les limites de registre, consultez [Niveaux de service d’Azure Container Registry](container-registry-skus.md).

## <a name="available-regions"></a>Régions disponibles

* Asie de l’Est
* Europe Nord
* Europe Ouest
* USA Est

## <a name="scenarios"></a>Scénarios

Un registre de conteneurs Azure basé sur le cloud fournit des [fonctionnalités](container-registry-intro.md#key-features) comme la géoréplication, la sécurité intégrée, le stockage géré par Azure, et l’intégration aux pipelines de développement et de déploiement Azure. En même temps, les clients étendent leurs investissements cloud à leurs solutions locales et de terrain.

Les charges de travail de conteneur ont besoin que les images conteneur et les artefacts associés soient disponibles à proximité pour bénéficier des performances et de la fiabilité nécessaires dans les environnements locaux ou distants. Le registre connecté fournit une solution de registre locale et performante qui synchronise régulièrement le contenu avec un registre de conteneurs Azure basé sur le cloud.

Les scénarios pour un registre connecté sont les suivants :

* Usines connectées
* Points de vente au détail
* Transport maritime, exploitation pétrolière, exploitation minière et autres environnements connectés occasionnellement

## <a name="how-does-the-connected-registry-work"></a>Comment le registre connecté fonctionne-t-il ?

L’illustration suivante montre un modèle de déploiement classique du registre connecté.

:::image type="content" source="media/intro-connected-registry/connected-registry-overview.svg" alt-text="Vue d’ensemble du registre connecté":::

### <a name="deployment"></a>Déploiement

Chaque registre connecté est une ressource que vous gérez à l’aide d’un registre de conteneurs Azure basé sur le cloud. Le parent supérieur dans la hiérarchie de registre connecté est un registre de conteneurs Azure dans un cloud Azure ou un déploiement privé d’[Azure Stack Hub](/azure-stack/operator/azure-stack-overview).

Utilisez des outils Azure pour installer localement le registre connecté sur un serveur ou un appareil, ou dans un environnement qui prend en charge les charges de travail de conteneur locales, comme [Azure IoT Edge](../iot-edge/tutorial-nested-iot-edge.md).

L’*état d’activation* du registre connecté indique s’il est déployé localement.

* **Actif** : le registre connecté est actuellement déployé localement. Il ne peut pas être redéployé s’il n’est pas désactivé. 
* **Inactif** : le registre connecté n’est pas déployé localement. Il peut actuellement être déployé.  
 
### <a name="content-synchronization"></a>Synchronisation de contenu

Le registre connecté accède régulièrement au registre cloud pour synchroniser les images conteneur et les artefacts OCI. 

Il peut également être configuré pour synchroniser un sous-ensemble des dépôts du registre cloud, ou pour effectuer une synchronisation uniquement à certains intervalles afin de réduire le trafic entre le cloud et les locaux.

### <a name="modes"></a>Modes

Un registre connecté peut fonctionner dans l’un des deux modes suivants : *ReadWrite* ou *ReadOnly*

- **Mode ReadWrite** : le mode par défaut permet aux clients de tirer et de pousser des artefacts (lire et écrire) dans le registre connecté. Les artefacts qui sont poussés vers le registre connecté sont synchronisés avec le registre cloud. 
        
  Le mode ReadWrite est utile quand un environnement de développement local est en place. Les images sont poussées vers le registre connecté local, puis synchronisées dans le cloud.

- **Mode ReadOnly** : quand le registre connecté est en mode ReadOnly, les clients peuvent uniquement tirer (lire) des artefacts. Cette configuration est utilisée pour les scénarios d’appareils IoT Edge imbriqués, ou d’autres scénarios où les clients ont besoin de tirer une image conteneur pour fonctionner.

### <a name="registry-hierarchy"></a>Hiérarchie de registre

Chaque registre connecté doit être connecté à un parent. Le parent supérieur est le registre cloud. Pour les scénarios hiérarchiques comme les appareils [IoT Edge imbriqués](overview-connected-registry-and-iot-edge.md), vous pouvez imbriquer les registres connectés dans l’un des deux modes. Le parent connecté au registre cloud peut fonctionner dans l’un ou l’autre des deux modes. 

Les registres enfants doivent être compatibles avec les fonctionnalités de leur parent. Ainsi, les registres connectés en mode ReadWrite et ReadOnly peuvent être les enfants d’un registre connecté fonctionnant en mode ReadWrite, mais seul un registre en mode ReadOnly peut être l’enfant d’un registre connecté fonctionnant en mode ReadOnly.  

## <a name="client-access"></a>Accès client

Les clients locaux utilisent des outils standard comme Docker CLI pour pousser ou tirer du contenu dans un registre connecté. Pour gérer l’accès client, vous créez des [jetons][repository-scoped-permissions] Azure Container Registry afin d’accéder à chaque registre connecté. Vous pouvez limiter les jetons clients (pour l’accès au tirage ou au poussage) sur un ou plusieurs dépôts dans le registre.

Chaque registre connecté doit également communiquer régulièrement avec son registre parent. À cet effet, le registre reçoit un *jeton de synchronisation* du registre cloud. Le jeton permet au registre de s’authentifier sur son registre parent pour les opérations de synchronisation et de gestion.

Pour plus d’informations, consultez [Gérer l’accès à un registre connecté][overview-connected-registry-access].

## <a name="limitations"></a>Limitations

- Le nombre de jetons et de mappages d’étendue est [limité](container-registry-skus.md) à 20 000 par registre de conteneurs. Cette restriction limite indirectement le nombre de registres connectés pour un registre cloud, car chaque registre connecté a besoin d’un jeton de synchronisation et d’un jeton client.
- Le nombre d’autorisations de dépôt dans un mappage d’étendue est limité à 500.
- Le nombre de clients du registre connecté est actuellement limité à 20.
- Le [verrouillage d’image](container-registry-image-lock.md) en utilisant des métadonnées de dépot/manifeste/étiquette n’est actuellement pas pris en charge pour les registres connectés.
- La [suppression de dépôt](container-registry-delete.md) n’est pas prise en charge sur les registres connectés en mode ReadOnly.
- Les [journaux de ressource](monitor-service-reference.md#resource-logs) des registres connectés ne sont pas pris en charge actuellement.
- Le registre connecté est associé au point de terminaison de données de la région d’accueil du registre. La migration automatique pour la [géoréplication](container-registry-geo-replication.md) n’est pas prise en charge.
- La suppression d’un registre connecté nécessite la suppression manuelle des conteneurs locaux ainsi que la suppression du mappage d’étendue ou des jetons respectifs dans le cloud.
- Les limitations de synchronisation de registre connecté sont les suivantes :
  - Pour la synchronisation continue :
    - `minMessageTtl` est de 1 jour
    - `maxMessageTtl` est de 90 jours
  - Pour les scénarios occasionnellement connectés, où vous voulez spécifier une fenêtre de synchronisation :
    - `minSyncWindow` est d’1 h
    - `maxSyncWindow` est de 7 jours

## <a name="next-steps"></a>Étapes suivantes

Dans cette vue d’ensemble, vous avez découvert le registre connecté et certains concepts de base. Passez à l’un des articles suivants pour en savoir plus sur les scénarios spécifiques où vous pouvez utiliser le registre connecté.

> [!div class="nextstepaction"]
> [Vue d’ensemble : accès au registre connecté][overview-connected-registry-access]
> 
> [!div class="nextstepaction"]
> [Vue d’ensemble : registre connecté et IoT Edge][overview-connected-registry-and-iot-edge]

<!-- LINKS - internal -->
[overview-connected-registry-access]:overview-connected-registry-access.md
[overview-connected-registry-and-iot-edge]:overview-connected-registry-and-iot-edge.md
[repository-scoped-permissions]: container-registry-repository-scoped-permissions.md
