---
title: Erreurs et actions dans Azure Chaos Studio
description: Découvrez ce que sont les erreurs et les actions dans Azure Chaos Studio. Quelle est la différence entre une erreur et une action ? Comment définir une erreur ?
author: johnkemnetz
ms.author: johnkem
ms.service: chaos-studio
ms.topic: conceptual
ms.date: 11/01/2021
ms.custom: template-concept, ignite-fall-2021
ms.openlocfilehash: 70d212a7d9677c0068670d2954897d7e1f27b137
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132372432"
---
# <a name="faults-and-actions-in-azure-chaos-studio"></a>Erreurs et actions dans Azure Chaos Studio

Dans Chaos Studio, chaque activité qui se produit dans le cadre d’une expérience est appelée une **action** et le type d’action le plus courant est une **erreur**. Cet article décrit les actions et les erreurs ainsi que les propriétés correspondantes.

## <a name="experiment-actions"></a>Actions d’expérimentation

Une action est une activité orchestrée dans le cadre d’une expérience de chaos. Les actions sont organisées en étapes et en branches, ce qui permet de les exécuter de façon séquentielle ou en parallèle. À chaque action sont associées les propriétés suivantes :
* **Nom** : action spécifique exécutée. Le nom prend généralement la forme d’un URN pour l’action, par exemple, `urn:
* **Type** : la manière dont l’action s’exécute. Les actions peuvent être *continues*, ce qui signifie que l’action s’exécute sans interruption sur une période donnée (par exemple, en appliquant la sollicitation du processeur pendant 10 minutes), ou *discrète*, ce qui signifie que l’action ne se produit qu’une seule fois (par exemple, en redémarrant une instance de Cache Redis).

## <a name="types-of-actions"></a>Types d’actions

Il existe deux sortes d’actions dans Chaos Studio :
- **Erreurs** : cette action entraîne une perturbation sur une ou plusieurs ressources.
- **Temporisations** : cette action applique un délai d’attente sans impact sur les ressources. Elle est utile pour appliquer une pause entre les erreurs afin d’attendre qu’un système soit impacté par l’erreur précédente.

## <a name="faults"></a>Pannes

Les erreurs représentent l’action la plus courante dans Chaos Studio. Les erreurs provoquent une perturbation dans un système, ce qui vous permet de vérifier que le système la gère efficacement sans impact sur la disponibilité. Les erreurs peuvent être destructrices (elles peuvent tuer un processus, par exemple), appliquer une sollicitation (par exemple, ajouter une sollicitation de la mémoire virtuelle), ajouter de la latence ou entraîner une modification de la configuration. En plus d’un nom et d’un type, les erreurs peuvent également avoir une *durée* (si elles sont continues) et des *paramètres*. Les paramètres décrivent comment l’erreur doit être appliquée et sont spécifiques au nom de l’erreur. Par exemple, la région de lecture qui sera promue en région d’écriture durant la panne de la région d’écriture est un paramètre pour l’erreur de basculement de Cosmos DB. Certains paramètres sont requis, tandis que d’autres sont facultatifs.

Les erreurs sont *basées sur des agents* ou de type *service-direct* selon le type de cible. Une erreur basée sur un agent nécessite l’installation de l’agent Chaos Studio sur une machine virtuelle ou un groupe de machines virtuelles identiques. L’agent est disponible pour Windows et Linux, mais toutes les erreurs ne sont pas disponibles sur les deux systèmes d’exploitation. Pour obtenir des informations sur les erreurs prises en charge sur chaque système d’exploitation, consultez la [bibliothèque d’erreurs](chaos-studio-fault-library.md). Les erreurs service-direct ne nécessitent pas d’agent : elles s’exécutent directement sur une ressource Azure.

Les erreurs incluent également le nom du sélecteur qui décrit les ressources sur lesquelles l’erreur s’exécutera. Pour en apprendre davantage sur les sélecteurs, consultez l’[article sur les expériences de chaos](chaos-studio-chaos-experiments.md). Une erreur peut impacter une ressource uniquement si la ressource a été intégrée comme cible et que la fonctionnalité d’erreur correspondante est activée sur la ressource.

## <a name="next-steps"></a>Étapes suivantes
Vous savez à présent ce que sont les actions et les erreurs. Vous êtes prêt à :
- [Créer et exécuter votre première expérience](chaos-studio-tutorial-service-direct-portal.md)
