---
title: Exclusion de plages d’adresses IP et de ports
description: Implémenter l’exclusion de plages d’adresses IP et de ports
services: container-service
ms.topic: article
ms.date: 10/8/2021
ms.custom: mvc, devx-track-azurecli
ms.author: kochhars
ms.openlocfilehash: b2f43fc1ce78a10079bc37cdf77097caaa208bd6
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131066707"
---
# <a name="implement-ip-and-span-classx-x-first-x-lastport-range-exclusionspan"></a>Implémenter l’<span class="x x-first x-last">exclusion de plages d’adresses IP et de ports</span>

Le trafic TCP sortant des applications est intercepté par défaut à l’aide des règles `iptables` programmées par OSM et redirigé vers le proxy side-car Envoy. OSM fournit un moyen de spécifier une liste de ports et de plages d’adresses IP à exclure de l’interception du trafic, si nécessaire. Pour obtenir des conseils sur la façon d’exclure des adresses IP et des plages de ports, reportez-vous à [cette documentation](https://release-v0-11.docs.openservicemesh.io/docs/guides/traffic_management/iptables_redirection/).

> [!NOTE]
>
> - Pour le module complémentaire Open Service Mesh AKS, **l’exclusion de port ne peut être implémentée qu’après l’installation à l’aide de `kubectl patch` et non pendant l’installation à l’aide de l’indicateur `--set` de l’interface de commande (CLI) OSM.**
> - Si les pods d’application qui font partie du maillage doivent accéder à IMDS, à Azure DNS ou au serveur d’API Kubernetes, l’utilisateur doit ajouter explicitement ces adresses IP à la liste des plages d’adresses IP sortantes globales à l’aide de la commande ci-dessus. Consultez [ici](https://release-v0-11.docs.openservicemesh.io/docs/guides/app_onboarding/#onboard-services)un exemple d’exclusion de ports du serveur de l’API Kubernetes.
