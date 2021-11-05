---
title: Différences entre Azure Data Explorer et Azure Synapse Data Explorer (préversion)
description: Cet article décrit les différences entre Azure Data Explorer et Azure Synapse Data Explorer.
ms.topic: overview
ms.date: 11/02/2021
author: shsagir
ms.author: shsagir
ms.reviewer: maraheja
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: data-explorer
ms.custom: ignite-fall-2021
ms.openlocfilehash: a6368d0431d2ce388f2d5bed37d125a34b4d5094
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131028920"
---
# <a name="what-is-the-difference-between-azure-synapse-data-explorer-and-azure-data-explorer-preview"></a>Différences entre Azure Synapse Data Explorer et Azure Data Explorer (Préversion)

[Azure Data Explorer](/azure/data-explorer/data-explorer-overview) est un service autonome d’exploration de données rapide et hautement scalable pour les données des journaux et les données de télémétrie. La même technologie sous-jacente qui exécute le service est disponible dans Azure Synapse en tant que service analytique intégré pour compléter ses services SQL et Spark existants, destinés aux scénarios Machine Learning d’entrepôt de données et d’Engineering données.

L’intégration native des fonctionnalités d’exploration de données permet d’analyser les charges de travail de séries chronologiques et de journaux d’analyse sous la forme de pools Synapse Data Explorer. Ces pools offrent une intégration étroite avec les autres fonctionnalités de Synapse, ainsi qu’une gestion simplifiée de la connectivité. Le fait de disposer de toutes les données de votre ordinateur et de vos utilisateurs et de les rendre accessibles dans un même emplacement, et en tirant parti des fonctionnalités exploratoires en quasi-temps réel de Data Explorer, vous permet de créer des solutions qui boostent la valeur métier.

Nous vous recommandons de commencer par Synapse Data Explorer si vous recherchez une solution unifiée qui combine des fonctionnalités de Big Data et d’analytique.

## <a name="capability-support"></a>Prise en charge des fonctionnalités

| Category | Fonctionnalité | Explorateur de données Azure | Synapse Data Explorer |
|--|--|--|--|
| **Sécurité** | Réseau virtuel | Prend en charge l’injection de réseaux virtuels et Azure Private Link | Prise en charge d’Azure Private Link intégrée automatiquement dans le cadre du réseau virtuel géré Synapse |
|  | CMK | ✓ | Héritage automatique de la configuration de l’espace de travail Synapse |
|  | Pare-feu | ✗ | Héritage automatique de la configuration de l’espace de travail Synapse |
| **Continuité des activités** | Zones de disponibilité | Facultatif | Activé par défaut lorsque les zones de disponibilité sont disponibles |
| **Référence (SKU)** | Options de calcul | Plus de 22 références SKU de machine virtuelle Azure parmi lesquelles choisir | Simplifié pour proposer des références SKU de types de charges de travail Synapse |
| **Intégrations** | Pipelines d’ingestion intégrés | Event Hub, Event Grid, IoT Hub | Event Hub, Event Grid et IoT Hub pris en charge via le Portail Azure pour le réseau virtuel non géré |
|  | Intégration Spark | Service lié Azure Data Explorer : intégration de Kusto Spark intégrée avec prise en charge de l’authentification directe Azure Active Directory, du MSI de l’espace de travail Synapse et du principal du service | Intégration de connecteur Kusto Spark intégrée avec prise en charge de l’authentification directe Azure Active Directory, du MSI de l’espace de travail Synapse et du principal du service |
|  | Gestion des artefacts KQL | ✗ | Enregistrer des requêtes KQL et les intégrer à Git |
|  | Synchronisation des métadonnées | ✗ | Synchronisation des tables externes avec le metastore Synapse central à des fins de consommation par d’autres services Synapse |
| **Caractéristiques** | Requêtes KQL | ✓ | ✓ |
|  | API et SDK | ✓ | ✓ |
|  | Connecteurs | ✓ | ✓ |
|  | Outils de requête | ✓ | ✓ |
| **Tarification** | Modèle métier | Modèle Coût plus facturation avec plusieurs compteurs : majoration IP Azure Data Explorer, Compute, Stockage et Mise en réseau | Modèle de facturation Vcore avec deux compteurs : VCore et Stockage |
|  | Instances réservées | ✓ | ✗ |

## <a name="next-steps"></a>Étapes suivantes

[Data Explorer dans Azure Synapse Analytics](data-explorer-overview.md)
