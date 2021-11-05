---
title: Compréhension de l’ingénierie du chaos et de la résilience avec Azure Chaos Studio
description: Découvrez les concepts d’ingénierie du chaos et de résilience.
services: chaos-studio
author: johnkemnetz
ms.topic: article
ms.date: 11/01/2021
ms.author: johnkem
ms.service: chaos-studio
ms.custom: ignite-fall-2021
ms.openlocfilehash: 75a225031986b595f3e3d83b729b73588726a465
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131096513"
---
# <a name="understanding-chaos-engineering-and-resilience"></a>Présentation de l’ingénierie du chaos et de la résilience

Avant de commencer à utiliser Chaos Studio, il est utile de comprendre les principaux concepts d’ingénierie de fiabilité des sites appliqués.

## <a name="what-is-resilience"></a>Qu’est-ce que la résilience ?

La création d’applications distribuées à grande échelle n’a jamais été aussi simple. L’infrastructure est hébergée dans le cloud, la prise en charge des langages de programmation est variée et la génération peut s’appuyer sur une multitude de composants et services open source et hébergés. Malheureusement, il n’existe aucune garantie de fiabilité pour ces composants et dépendances sous-jacents, ou pour les systèmes qui reposent dessus. L’infrastructure peut passer en mode hors connexion, et des interruptions de service ou des pannes peuvent se produire à tout moment. Des interruptions mineures dans une zone peuvent être amplifiées et avoir des effets secondaires prolongés dans une autre. 

Les applications et services doivent prévoir les interruptions de service, les perturbations de dépendances connues et inconnues, une charge inattendue soudaine et les latences dans le système, et s’y adapter. Les applications et services doivent être conçus pour gérer les défaillances et être renforcés contre les perturbations. 

Les applications et services qui traitent correctement les contraintes et les problèmes sont **résilients**. La fiabilité des composants individuels est bonne, mais la **résilience est une propriété de l’ensemble du système**. La résilience du système de bout en bout doit être validée dans un environnement intégré de type production, avec les conditions et la charge qui seront rencontrées en production.

## <a name="what-are-chaos-engineering-and-fault-injection"></a>Que sont l’ingénierie du chaos et l’injection d’erreurs ?

L’**ingénierie du chaos** est la pratique qui consiste à soumettre des applications et des services à des contraintes et des défaillances réelles afin de générer et de valider la résilience à des conditions peu fiables et à des dépendances manquantes. L’**injection d’erreurs** consiste à introduire une erreur dans un système. Différentes erreurs, telles que la latence du réseau ou la perte de l’accès au stockage, peuvent être utilisées pour cibler des composants système, ce qui provoque des scénarios qu’une application ou un service doit être en mesure de gérer ou à partir desquels ils doivent pouvoir récupérer. Une expérience de chaos est l’application d’erreurs individuellement, en parallèle et/ou séquentiellement à une ou plusieurs ressources d’abonnement ou dépendances avec l’objectif de superviser le comportement et l’intégrité du système, et d’agir sur les problèmes qui surviennent. Une expérience peut représenter un scénario réel, tel qu’une panne de courant dans un centre de données ou une latence du réseau sur un serveur DNS. Elle peut également être utilisée pour simuler des conditions extrêmes qui se produisent avec les frénésie d’achats du Black Friday, ou lorsque des tickets de concert sont en vente pour un groupe de chanteurs populaire.
