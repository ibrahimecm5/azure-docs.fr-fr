---
title: Ensembles d’événements de sécurité Windows qui peuvent être envoyés à Azure Sentinel | Microsoft Docs
description: Découvrez les ensembles prédéfinis d’événements de sécurité Windows que vous pouvez collecter et diffuser à partir de vos systèmes Windows vers votre espace de travail Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: d51d2e09-a073-41c8-b396-91d60b057e6a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/12/2021
ms.author: yelevin
ms.openlocfilehash: 58503ee618666d990c8d23b7c42df5b277f53af4
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131478987"
---
# <a name="windows-security-event-sets-that-can-be-sent-to-azure-sentinel"></a>Ensembles d’événements de sécurité Windows qui peuvent être envoyés à Azure Sentinel

Lors de l’ingestion des événements de sécurité des appareils Windows à l’aide du [connecteur de données d’événements de sécurité Windows](data-connectors-reference.md#windows-security-events-via-ama) (y compris la [version héritée](data-connectors-reference.md#security-events-via-legacy-agent-windows)), vous pouvez choisir les événements à collecter parmi les ensembles suivants :

- **Tous les événements** : tous les événements AppLocker et de sécurité Windows.

- **Courant** : un ensemble d’événements à des fins d’audit. Une piste d’audit utilisateur complète est fournie dans cet ensemble. Par exemple, il contient les événements de connexion et de déconnexion de l’utilisateur (ID d’événement 4624, 4634). Il existe également des actions d’audit, telles que les modifications de groupe de sécurité, les opérations Kerberos du contrôleur de domaine clé et les autres événements alignés avec les meilleures pratiques acceptées.

    L’ensemble d’événements **Common** peut contenir certains types d’événements qui ne sont pas si courants.  Cela est dû au fait que le point principal de l’ensemble **Common** est de réduire le volume d’événements à un niveau plus gérable, tout en conservant la fonctionnalité de piste d’audit complète.

- **Minimal** : un petit ensemble d’événements qui peuvent indiquer des menaces potentielles. Cet ensemble ne contient pas de piste d’audit complète. Il couvre uniquement les événements qui peuvent indiquer une violation avérée et d’autres événements importants qui ont un taux d’occurrence très faible. Par exemple, il contient des connexions utilisateur ayant réussi et ayant échoué (ID d’événement 4624, 4625), mais il ne contient pas d’informations de déconnexion (4634), qui sont importantes pour l’audit, mais pas pour la détection de violation, et dont le volume est relativement élevé. Le plus gros du volume de données de cet ensemble est constitué d’événements de connexion et d’événements de création de processus (ID d’événement 4688).

- **Personnalisé** : ensemble d’événements déterminés par vous, par l’utilisateur et définis dans une règle de collecte de données à l’aide de requêtes XPath. [En savoir plus sur les règles de collecte de données](../azure-monitor/agents/data-collection-rule-azure-monitor-agent.md#limit-data-collection-with-custom-xpath-queries).

## <a name="event-id-reference"></a>Référence de ID de l'événement

La liste suivante fournit le détail complet des ID d’événement App Locker et de sécurité pour chaque ensemble :

| Ensemble d’événements | ID d’événements collectés |
| --- | --- |
| **Minimal** | 1102, 4624, 4625, 4657, 4663, 4688, 4700, 4702, 4719, 4720, 4722, 4723, 4724, 4727, 4728, 4732, 4735, 4737, 4739, 4740, 4754, 4755, 4756, 4767, 4799, 4825, 4946, 4948, 4956, 5024, 5033, 8001, 8002, 8003, 8004, 8005, 8006, 8007, 8222 |
| **Commun** | 1, 299, 300, 324, 340, 403, 404, 410, 411, 412, 413, 431, 500, 501, 1100, 1102, 1107, 1108, 4608, 4610, 4611, 4614, 4622, 4624, 4625, 4634, 4647, 4648, 4649, 4657, 4661, 4662, 4663, 4665, 4666, 4667, 4688, 4670, 4672, 4673, 4674, 4675, 4689, 4697, 4700, 4702, 4704, 4705, 4716, 4717, 4718, 4719, 4720, 4722, 4723, 4724, 4725, 4726, 4727, 4728, 4729, 4733, 4732, 4735, 4737, 4738, 4739, 4740, 4742, 4744, 4745, 4746, 4750, 4751, 4752, 4754, 4755, 4756, 4757, 4760, 4761, 4762, 4764, 4767, 4768, 4771, 4774, 4778, 4779, 4781, 4793, 4797, 4798, 4799, 4800, 4801, 4802, 4803, 4825, 4826, 4870, 4886, 4887, 4888, 4893, 4898, 4902, 4904, 4905, 4907, 4931, 4932, 4933, 4946, 4948, 4956, 4985, 5024, 5033, 5059, 5136, 5137, 5140, 5145, 5632, 6144, 6145, 6272, 6273, 6278, 6416, 6423, 6424, 8001, 8002, 8003, 8004, 8005, 8006, 8007, 8222, 26401, 30004 |
|

## <a name="next-steps"></a>Étapes suivantes

Dans ce document, vous avez appris à filtrer la collection d’événements Windows dans Azure Sentinel.

- En savoir plus sur la [collecte d’événements de sécurité Windows](connect-windows-security-events.md).
- Commencez à détecter les menaces avec Azure Sentinel à l’aide de règles [intégrées](detect-threats-built-in.md) ou [personnalisées](detect-threats-custom.md).

