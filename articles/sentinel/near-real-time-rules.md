---
title: Détecter rapidement les menaces avec des règles d’analytique en quasi temps réel (NRT) dans Azure Sentinel | Microsoft Docs
description: Cet article explique comment les nouvelles règles d’analytique en quasi temps réel (NRT, near-real-time) peuvent vous aider à détecter rapidement les menaces dans Azure Sentinel.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/29/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: f8b4b534cd6725f7c19ee4f3f77a6d7ed500889c
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131096689"
---
# <a name="detect-threats-quickly-with-near-real-time-nrt-analytics-rules-in-azure-sentinel"></a>Détecter rapidement les menaces avec des règles d’analytique en quasi temps réel (NRT) dans Azure Sentinel

> [!IMPORTANT]
>
> - Les règles NRT sont en **PRÉVERSION**. Consultez l’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) pour connaître les conditions juridiques supplémentaires s’appliquant aux fonctionnalités Azure sont en version bêta, en préversion ou non encore en disponibilité générale.

## <a name="what-are-near-real-time-nrt-analytics-rules"></a>Présentation des règles d’analytique NRT

Quand vous êtes confronté à des menaces de sécurité, le temps et la vitesse sont vitaux. Vous devez être conscient des menaces au fur et à mesure de leur matérialisation afin de pouvoir les analyser et y répondre rapidement pour les contenir. Les règles d’analytique NRT d’Azure Sentinel offrent une détection plus rapide des menaces, plus proche de celle d’un dispositif SIEM local, et la possibilité de raccourcir les temps de réponse dans des scénarios spécifiques.

Les [règles d’analytique en quasi temps réel](detect-threats-built-in.md#nrt) d’Azure Sentinel fournissent une détection des menaces prête à l’emploi à la minute près. Ce type de règle a été conçu pour être très réactif en exécutant sa requête à des intervalles d’une minute seulement.

## <a name="how-do-they-work"></a>Comment elles fonctionnent ?

Les règles NRT sont codées en dur pour s’exécuter une fois toutes les minutes et capturent les événements ingérés au cours de la minute précédente, afin de vous fournir, dans la mesure du possible, des informations à la minute près.

Contrairement aux règles planifiées standard qui s’exécutent sur un délai de cinq minutes intégré pour tenir compte du délai d’ingestion, les règles NRT s’exécutent sur un délai de deux minutes seulement, ce qui permet de résoudre le problème de délai d’ingestion en interrogeant l’heure d’ingestion des événements au lieu de l’heure de leur génération à la source (champ TimeGenerated). La fréquence et la précision de vos détections s’en trouvent améliorées. (Pour mieux comprendre ce problème, consultez [Planification des requêtes et seuil d’alerte](detect-threats-custom.md#query-scheduling-and-alert-threshold) et l’article associé qui y est mentionné, [Gestion du délai d’ingestion dans les règles d’alerte planifiées Azure Sentinel](https://techcommunity.microsoft.com/t5/azure-sentinel/handling-ingestion-delay-in-azure-sentinel-scheduled-alert-rules/ba-p/2052851).)

Les règles NRT ont de nombreuses fonctionnalités telles que les règles d’analytique planifiées. Le jeu complet de fonctionnalités d’enrichissement des alertes est disponible : vous pouvez mapper des entités, exposer des détails personnalisés et configurer le contenu dynamique des détails des alertes. Vous pouvez choisir la façon dont les alertes sont regroupées en incidents, supprimer temporairement l’exécution d’une requête une fois qu’elle a généré un résultat et définir des playbooks et des règles d’automatisation à exécuter en réponse aux alertes et incidents générés à partir de la règle.

Ces modèles ont une application limitée comme indiqué ci-dessous, mais la technologie évolue rapidement.

## <a name="considerations"></a>Considérations
Les limitations suivantes régissent l’utilisation des règles NRT :

1. Vous ne pouvez pas définir plus de 20 règles par client pour l’instant.

1. Comme ce type de règle est nouveau, sa syntaxe est limitée, mais elle est appelée à évoluer progressivement. Ainsi, à ce stade, les restrictions suivantes sont appliquées :

    1. La requête définie dans une règle NRT ne peut référencer **qu’une seule table**. Toutefois, les requêtes peuvent faire référence à plusieurs watchlists et à des flux de renseignement sur les menaces.

    1. Vous ne pouvez pas utiliser des unions ou des jointures.

    1. Ce type de règle étant quasiment en temps réel, nous avons réduit le délai intégré à un minimum (deux minutes).

    1. Étant donné que les règles NRT utilisent l’heure d’ingestion plutôt que l’heure de génération de l’événement (représentée par le champ TimeGenerated), vous pouvez ignorer de manière sécurisée le délai à la source de données et la latence de la durée d’ingestion (voir ci-dessus).

    1. Les requêtes ne peuvent s’exécuter que dans un seul espace de travail. Il n’existe aucune fonctionnalité inter-espaces de travail.

    1. Il n’y a aucun regroupement d’événements. Les règles NRT produisent une seule alerte qui regroupe tous les événements applicables.

## <a name="next-steps"></a>Étapes suivantes

Dans ce document, vous avez appris comment fonctionnent les règles NRT dans Azure Sentinel.

- Découvrez comment [créer des règles NRT](create-nrt-rules.md).
- En savoir plus sur les [autres types de règles d’analyse](detect-threats-built-in.md).
