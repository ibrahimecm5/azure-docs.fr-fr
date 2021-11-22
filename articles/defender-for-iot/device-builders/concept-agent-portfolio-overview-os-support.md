---
title: Vue d’ensemble du portefeuille d’agents et prise en charge des OS (préversion)
description: Microsoft Defender pour IoT fournit un vaste portefeuille d’agents en fonction du type d’appareil.
ms.date: 11/09/2021
ms.topic: conceptual
ms.openlocfilehash: fbad1c486a9bf553b7f7effffe8dcf5e12b5d03a
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132319013"
---
# <a name="agent-portfolio-overview-and-os-support-preview"></a>Vue d’ensemble du portefeuille d’agents et prise en charge des OS (préversion)

Microsoft Defender pour IoT fournit un vaste portefeuille d’agents en fonction du type d’appareil.

## <a name="standalone-agent"></a>Agent autonome

L’agent autonome couvre la plupart des systèmes d’exploitation Linux, qui peuvent être déployés sous la forme d’un package binaire ou d’un code source qui peut être incorporé dans le cadre du microprogramme, et autoriser la modification et la personnalisation en fonction des besoins des clients. Voici quelques exemples de systèmes d’exploitation pris en charge :

| Système d’exploitation | AMD64 | ARM32v7 | ARM64 |
|--|--|--|--|
| Debian 9 | ✓ | ✓ | |
| Ubuntu 18.04 | ✓ |  | ✓ |
| Ubuntu 20.04 | ✓ |  | |

Pour obtenir une vue plus granulaire des dépendances du système d’exploitation du micro-agent, consultez [Dépendances Linux](concept-micro-agent-linux-dependencies.md#linux-dependencies).

Pour plus d’informations, pour connaître les systèmes d’exploitation pris en charge ou pour demander l’accès au code source afin de pouvoir l’incorporer en tant que partie du microprogramme de l’appareil, contactez votre responsable de compte ou envoyez un e-mail à l’adresse <defender_micro_agent@microsoft.com>.

## <a name="azure-rtos-micro-agent"></a>Micro-agent Azure RTOS

Le micro-agent Microsoft Defender pour IoT fournit une solution de sécurité complète et légère pour les appareils qui utilisent Azure RTOS. Le micro-agent Microsoft Defender pour IoT permet de couvrir les menaces courantes et les activités malveillantes potentielles sur les appareils avec un système d’exploitation en temps réel (RTOS). Le micro-agent est intégré au composant Azure RTOS NetX Duo. Il supervise l’activité réseau de l’appareil.

Le micro-agent Microsoft Defender pour IoT est intégré au composant Azure RTOS NetX Duo. Il supervise l’activité réseau de l’appareil. Le micro-agent se compose d’une solution de sécurité complète et légère qui permet de couvrir les menaces courantes et les activités malveillantes potentielles sur les appareils avec un système d’exploitation en temps réel (RTOS).

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les [dépendances Linux pour le micro-agent (préversion)](concept-micro-agent-linux-dependencies.md).

Découvrez-en plus sur la [Vue d’ensemble du micro-agent autonome (préversion)](concept-standalone-micro-agent-overview.md).
