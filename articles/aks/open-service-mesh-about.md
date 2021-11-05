---
title: Open Service Mesh
description: Open Service Mesh (OSM) dans Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 3/12/2021
ms.custom: mvc, devx-track-azurecli
ms.author: pgibson
ms.openlocfilehash: d03aead2056ef3394c35f5e9679edd28e47e3fd0
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131066897"
---
# <a name="open-service-mesh-aks-add-on"></a>Module complémentaire Open Service Mesh AKS

[Open Service Mesh (OSM)](https://docs.openservicemesh.io/) est un maillage de services natif Cloud léger et extensible qui permet aux utilisateurs de gérer et de sécuriser uniformément les environnements de microservices hautement dynamiques et de bénéficier de fonctionnalités d’observabilité prêtes à l’emploi.

OSM exécute un plan de contrôle basé sur un envoi sur Kubernetes, peut être configuré avec des API [SMI](https://smi-spec.io/) et fonctionne en injectant un proxy Envoy en tant que conteneur side-car à côté de chaque instance de votre application. Le proxy Envoy contient et exécute des règles concernant les stratégies de contrôle d’accès, implémente la configuration de routage et capture les métriques. Le plan de contrôle configure continuellement les proxys pour garantir que les stratégies et les règles d’acheminement sont à jour et que les proxys sont intègres.

Le projet OSM a été généré par Microsoft. Il a depuis été remis gracieusement et est régi par la [Cloud Native Computing Foundation (CNCF)](https://www.cncf.io/). Le projet OSM open source continue d’être une collaboration menée par la communauté relative aux fonctionnalités, et les contributions au projet sont accueillies et encouragées. Consultez notre guide [Échelle de contributeur](https://github.com/openservicemesh/osm/blob/main/CONTRIBUTOR_LADDER.md) pour plus d’informations sur la façon dont vous pouvez vous impliquer.

## <a name="capabilities-and-features"></a>Capacités et fonctionnalités

OSM offre l’ensemble des capacités et fonctionnalités suivantes pour fournir un maillage de services natif Cloud pour vos clusters Azure Kubernetes Service (AKS) :

- OSM a été intégré au service AKS pour fournir une expérience de maillage de service entièrement prise en charge et gérée avec la commodité du module complémentaire Fonctionnalités d’AKS

- Sécurisation des communications entre services grâce à l’activation de mTLS

- Intégration facile des applications dans le maillage grâce à l’injection automatique de side-car du proxy Envoy

- Configurations faciles et transparentes pour le déplacement du trafic sur les déploiements

- Possibilité de définir et d’exécuter des stratégies de contrôle d’accès affinées pour les services

- Possibilité d’observer et de comprendre les métriques des applications pour le débogage et la surveillance des services

- Intégration avec des services/solutions externes de gestion des certificats grâce à une interface enfichable

## <a name="scenarios"></a>Scénarios

OSM peut aider vos déploiements AKS dans les scénarios suivants :

- Mise en place de communications chiffrées entre des points de terminaison de service déployés dans le cluster

- Autorisation du trafic HTTP/HTTPS et TCP dans le maillage

- Configuration de contrôles du trafic pondéré entre deux services ou plus pour les déploiements A/B ou Canary

- Collecte et visualisation des indicateurs de performance clés du trafic d’application

## <a name="osm-service-quotas-and-limits-preview"></a>Quotas et limites de service d’OSM (préversion)

Les limitations de la préversion d’OSM en ce qui concerne les quotas et les limites de service sont disponibles sur la [page Quotas et limites régionales](./quotas-skus-regions.md) d’AKS.

<!-- LINKS - internal -->

[kubernetes-service]: concepts-network.md#services
[az-feature-register]: /cli/azure/feature?view=azure-cli-latest&preserve-view=true#az_feature_register
[az-feature-list]: /cli/azure/feature?view=azure-cli-latest&preserve-view=true#az_feature_list
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest&preserve-view=true#az_provider_register
