---
title: Intégration de Microsoft 365 Defender avec Microsoft Sentinel | Microsoft Docs
description: Découvrez comment l’utilisation de Microsoft 365 Defender conjointement avec Microsoft Sentinel vous permet d’utiliser Microsoft Sentinel comme file d’attente d’incidents universelle tout en appliquant de manière transparente les forces de Microsoft 365 Defender pour vous aider à examiner les incidents de sécurité Microsoft 365. Découvrez également comment ingérer les données de chasse avancées d’un composant Defender dans Microsoft Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: a5a969397ce63568c95891206d95275af5caf6a8
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132520481"
---
# <a name="microsoft-365-defender-integration-with-microsoft-sentinel"></a>Intégration de Microsoft 365 Defender à Microsoft Sentinel

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

> [!IMPORTANT]
> Le connecteur Microsoft 365 Defender est actuellement en **PRÉVERSION**. Consultez l’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) pour connaître les conditions juridiques supplémentaires s’appliquant aux fonctionnalités Azure sont en version bêta, en préversion ou non encore en disponibilité générale.

> [!IMPORTANT]
>
> **Microsoft 365 Defender** était anciennement connu sous le nom de **Protection Microsoft contre les menaces** ou **MTP**.
>
> **Microsoft Defender for Endpoint** était précédemment appelé **Microsoft Defender Advanced Threat Protection**, ou **MDATP**.
>
> **Microsoft Defender pour Office 365** était anciennement connu sous le nom **Office 365 Advanced Threat Protection**.
>
> Vous pouvez voir les anciens noms encore en cours d’utilisation pendant un certain temps.

## <a name="incident-integration"></a>Intégration d’incidents

L’intégration d’incidents [Microsoft 365 Defender](/microsoft-365/security/mtp/microsoft-threat-protection) de Microsoft Sentinel vous permet de diffuser tous les incidents Microsoft 365 Defender dans Microsoft Sentinel et de les garder synchronisés entre les deux portails. Les incidents de Microsoft 365 Defender (anciennement Protection Microsoft contre les menaces) incluent l’ensemble des alertes, entités et informations pertinentes associées, ce qui vous offre suffisamment de contexte pour effectuer un triage et une investigation préliminaire dans Microsoft Sentinel. Une fois dans Sentinel, les incidents restent synchronisés de manière bidirectionnelle avec Microsoft 365 Defender, ce qui vous permet de tirer parti des avantages des deux portails dans votre investigation sur l’incident.

Cette intégration confère aux incidents de sécurité Microsoft 365 la visibilité nécessaire pour leur gestion à partir de Microsoft Sentinel dans le cadre de la file d’attente des incidents principale de toute l’organisation, ce qui vous permet de voir les incidents Microsoft 365, ainsi que de les corréler avec ceux de tous vos autres systèmes locaux et cloud. En même temps, elle vous permet de bénéficier de la puissance et des capacités uniques de Microsoft 365 Defender pour des investigations approfondies, ainsi que d’une expérience spécifique de Microsoft 365 dans tout l’écosystème de Microsoft 365. Microsoft 365 Defender enrichit et regroupe les alertes de plusieurs produits Microsoft 365, ce qui a pour effet de réduire la taille de la file d’attente des incidents de SOC et de raccourcir le temps de résolution. Les services de composants qui font partie de la pile Microsoft 365 Defender sont les suivants :

- **Microsoft Defender pour point de terminaison** (anciennement Microsoft Defender ATP)
- **Microsoft Defender for Identity** (anciennement Azure ATP)
- **Microsoft Defender pour Office 365** (anciennement Office 365 ATP)
- **Microsoft Defender for Cloud Apps**

En plus de collecter des alertes à partir de ces composants, Microsoft 365 Defender génère ses propres alertes. Il crée des incidents à partir de toutes ces alertes et les envoie à Microsoft Sentinel.

### <a name="common-use-cases-and-scenarios"></a>Cas d’utilisation et scénarios courants

- Connexion en un clic d’incidents Microsoft 365 Defender, y compris toutes les alertes et entités des composants Microsoft 365 Defender, dans Microsoft Sentinel.

- Synchronisation bidirectionnelle entre les incidents Sentinel et Microsoft 365 Defender sur l’état, le propriétaire et le motif de clôture.

- Application des fonctionnalités de regroupement et d’enrichissement des alertes Microsoft 365 Defender dans Microsoft Sentinel, permettant de réduire le temps de résolution.

- Lien ciblé en contexte entre un incident Microsoft Sentinel et un incident Microsoft 365 Defender parallèle pour faciliter les investigations sur les deux portails.

### <a name="connecting-to-microsoft-365-defender"></a>Connexion à Microsoft 365 Defender

Une fois que vous avez activé le connecteur de données Microsoft 365 Defender pour [collecter les incidents et les alertes](connect-microsoft-365-defender.md), les incidents Microsoft 365 Defender s’affichent dans la file d’attente des incidents de Microsoft Sentinel, avec **Microsoft 365 Defender** dans le champ **Nom du produit**, peu après qu’ils ont été générés dans Microsoft 365 Defender.
- Jusqu’à 10 minutes peuvent s’écouler entre le moment où un incident est généré dans Microsoft 365 Defender et le moment où il apparaît dans Microsoft Sentinel.

- Les incidents sont ingérés et synchronisés sans frais supplémentaires.

Une fois que l’intégration de Microsoft 365 Defender est connectée, tous les connecteurs d’alerte de composant (Defender pour point de terminaison, Defender pour Identity, Defender pour Office 365, Defender pour les applications cloud) seront automatiquement connectés en arrière-plan s’ils ne l’étaient pas déjà. Si des licences de composants ont été achetées après la connexion de Microsoft 365 Defender, les alertes et les incidents du nouveau produit sont toujours transmis à Microsoft Sentinel sans que cela nécessite une configuration ou des frais supplémentaires.

### <a name="microsoft-365-defender-incidents-and-microsoft-incident-creation-rules"></a>Incidents Microsoft 365 Defender et règles de création d’incident Microsoft

- Les incidents générés par Microsoft 365 Defender, en fonction des alertes provenant des produits de sécurité Microsoft 365, sont créés à l’aide d’une logique Microsoft 365 Defender personnalisée.

- Les règles de création d’incidents Microsoft dans Microsoft Sentinel créent également des incidents à partir des mêmes alertes, en utilisant une logique Microsoft Sentinel personnalisée (différente).

- L’utilisation conjointe des deux mécanismes est entièrement prise en charge, et cette configuration permet de faciliter la transition vers la nouvelle logique de création d’incident de Microsoft 365 Defender. Toutefois, cela crée des **incidents en double** pour les mêmes alertes.

- Pour éviter de créer des incidents en double pour les mêmes alertes, nous recommandons aux clients de désactiver toutes les **règles de création d’incidents Microsoft** pour les produits Microsoft 365 (Defender pour Endpoint, Defender pour Identity, Defender pour Office 365 et Defender pour les applications cloud) lors de la connexion à Microsoft 365 Defender. Pour ce faire, vous pouvez désactiver la création d’un incident dans la page du connecteur. N’oubliez pas que, si vous procédez de la sorte, les filtres appliqués par les règles de création d’incident ne seront pas appliqués à l’intégration d’incidents Microsoft 365 Defender.

    > [!NOTE]
    > Tous les types d’alertes Defender pour les applications cloud sont désormais intégrés à Microsoft 365 Defender.

### <a name="working-with-microsoft-365-defender-incidents-in-microsoft-sentinel-and-bi-directional-sync"></a>Utilisation d’incidents Microsoft 365 Defender dans Microsoft Sentinel et synchronisation bidirectionnelle

Les incidents Microsoft 365 Defender s’affichent dans la file d’attente des incidents de Microsoft Sentinel avec le nom de produit **Microsoft 365 Defender**, ainsi qu’avec des détails et fonctionnalités similaires à ceux d’autres incidents de Sentinel. Chaque incident contient un lien vers l’incident parallèle dans le portail Microsoft 365 Defender.

À mesure que l’incident évolue dans Microsoft 365 Defender et qu’un plus grand nombre d’alertes ou d’entités y est ajouté, l’incident Microsoft Sentinel est mis à jour en conséquence.

Les modifications apportées à l’état, au motif de clôture ou à l’affectation d’un incident Microsoft 365, tant dans Microsoft 365 Defender que dans Microsoft Sentinel, sont également mises à jour en conséquence dans la file d’attente des incidents de l’autre. La synchronisation aura lieu dans les deux portails immédiatement après l’application de la modification de l’incident. Une actualisation peut être nécessaire pour voir les dernières modifications.

Dans Microsoft 365 Defender, toutes les alertes d’un incident peuvent être transférées vers un autre, ce qui a pour effet de fusionner les incidents. Quand cette fusion se produit, les incidents Microsoft Sentinel reflètent les modifications. Un incident contiendra toutes les alertes des incidents d’origine, et l’autre sera automatiquement clos, avec ajout de l’étiquette « redirigé ».

> [!NOTE]
> Les incidents dans Microsoft Sentinel peuvent contenir jusqu’à 150 alertes. Les incidents Microsoft 365 Defender peuvent avoir plus que cela. Si un incident Microsoft 365 Defender contenant plus de 150 alertes est synchronisé sur Microsoft Sentinel, l’incident Sentinel indique la présence de « 150 + » alertes et fournit un lien vers l’incident parallèle dans Microsoft 365 Defender, où il est possible de voir l’ensemble complet d’alertes.

## <a name="advanced-hunting-event-collection"></a>Collecte d’événements de repérage avancé

Le connecteur Microsoft 365 Defender vous permet de diffuser des événements de **repérage avancé** (un type de données d’événement brutes) de Microsoft 365 Defender vers Microsoft Sentinel. Vous pouvez actuellement collecter des événements de [repérage avancé](/microsoft-365/security/defender/advanced-hunting-overview) de Microsoft Defender pour point de terminaison et *(à partir d’octobre 2021)* de Microsoft Defender pour Office 365, les diffuser directement vers des tables dédiées dans votre espace de travail Microsoft Sentinel. Ces tables sont générées sur le même schéma que celui utilisé dans le portail Microsoft 365 Defender, ce qui vous donne un accès complet à l’ensemble des événements de repérage avancé, et vous permet d’effectuer les opérations suivantes :

- Copiez facilement vos requêtes de repérage avancé Microsoft Defender pour point de terminaison/Office 365 existantes dans Microsoft Sentinel.

- Utilisez les journaux des événements bruts afin de fournir des insights supplémentaires pour vos alertes, la recherche de menaces et l’investigation, et mettez en corrélation les événements avec ceux provenant d’autres sources de données dans Microsoft Sentinel.

- Stockez les journaux avec une période de conservation accrue, au-delà de la durée par défaut de 30 jours de Microsoft Defender pour point de terminaison/Office 365 ou Microsoft 365 Defender. Pour ce faire, vous pouvez configurer la période de conservation de votre espace de travail ou la conservation par table dans Log Analytics.

## <a name="next-steps"></a>Étapes suivantes

Dans ce document, vous avez découvert comment profiter de l’utilisation de Microsoft 365 Defender conjointement avec Microsoft Sentinel, à l’aide du connecteur Microsoft 365 Defender.

- Obtenir des instructions pour [activer le connecteur Microsoft 365 Defender](connect-microsoft-365-defender.md).
- Créer des [alertes personnalisées](detect-threats-custom.md) et [investiguer sur des incidents](investigate-cases.md).
