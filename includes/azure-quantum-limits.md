---
title: Inclut le fichier
description: Inclut le fichier
author: danielstocker
ms.service: azure-quantum
ms.topic: include
ms.date: 10/27/2021
ms.author: dasto
ms.openlocfilehash: 1811faeeb5de3e300a35f7107c5cdf23bf20151c
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131520882"
---
### <a name="provider-limits--quota"></a>Limites et quotas du fournisseur

Le service Azure Quantum prend en charge les fournisseurs de services internes et tiers. Les fournisseurs tiers possèdent leurs propres limites et quotas. Le portail Azure permet aux utilisateurs de consulter les offres et les limites lors de la configuration de fournisseurs tiers. 

Vous trouverez ci-dessous les limites de quota publiées pour le fournisseur de solutions d’optimisation interne de Microsoft. 

#### <a name="learn--develop-sku"></a>Référence SKU Apprendre et développer

| Ressource | Limite |
| --- | --- |
| Travaux simultanés basés sur le processeur | jusqu’à 5<sup>1</sup> travaux simultanés |
| Travaux simultanés basés sur les FPGA | jusqu’à 2<sup>1</sup> travaux simultanés |
| Heures du solveur basé sur le processeur | 20 heures par mois  |
| Heures du solveur basé sur les FPGA | 1 heure par mois  |

Lors de l’utilisation de la référence SKU Apprendre et développer, vous **ne pouvez pas** demander une augmentation de vos limites de quota. Vous êtes dès lors invité à opter pour la référence SKU Performances à grande échelle.

#### <a name="performance-at-scale-sku"></a>Référence SKU Performances à grande échelle

| Ressource | Limite par défaut | Limite maximale |
| --- | --- | --- |
| Travaux simultanés basés sur le processeur | jusqu’à 1 00<sup>1</sup> travaux simultanés | identique à la limite par défaut |
| Travaux simultanés basés sur les FPGA | jusqu’à 10<sup>1</sup> travaux simultanés | identique à la limite par défaut |
| Heures du solveur | 1 000 heures par mois  | jusqu’à 50 000 heures par mois |

Contactez le support Azure pour demander une augmentation de la limite.

Pour plus d’informations, consultez la [page de tarification Azure Quantum](https://aka.ms/AQ/Pricing).
Pour plus d’informations sur les offres tierces, consultez les pages de tarification des fournisseurs appropriées dans le Portail Azure.

<sup>1</sup> Décrit le nombre de travaux qui peuvent être mis en file d’attente en même temps.
