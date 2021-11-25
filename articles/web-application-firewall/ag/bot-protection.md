---
title: Configurer la protection bot pour le pare-feu d’applications web (WAF)
description: Découvrez comment configurer la protection bot pour le pare-feu d’applications web (WAF) sur Azure Application Gateway.
services: web-application-firewall
ms.topic: article
author: vhorne
ms.service: web-application-firewall
ms.date: 07/30/2021
ms.author: victorh
ms.openlocfilehash: a13784f722648f3639bcca7eece46d5128a689de
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132344880"
---
# <a name="configure-bot-protection-for-web-application-firewall-on-azure-application-gateway"></a>Configurer la protection bot pour Web Application Firewall sur Azure Application Gateway

Cet article explique comment configurer une règle de protection bot dans le pare-feu d’applications Web (WAF) Azure pour Application Gateway à l’aide du Portail Azure. 

Vous pouvez activer un ensemble de règles de protection bot managées pour votre WAF afin de bloquer ou de journaliser des requêtes provenant d’adresses IP malveillantes. Ces adresses IP proviennent du flux Microsoft Threat Intelligence. Intelligent Security Graph alimente le renseignement sur les menaces de Microsoft et est utilisé par plusieurs services, notamment Microsoft Defender pour le cloud.

## <a name="prerequisites"></a>Prérequis

Créez une stratégie de WAF de base pour Application Gateway en suivant les instructions décrites dans  [Créer des stratégies de pare-feu d’applications web pour Application Gateway](create-waf-policy-ag.md).

## <a name="enable-bot-protection-rule-set"></a>Activer un ensemble de règles de protection bot

1. Dans la page de la stratégie **de base** que vous avez créée précédemment, sous **Paramètres**, sélectionnez **Règles**.  

2. Dans la page de détails, sous la section  **Gérer les règles** , dans le menu déroulant, cochez la case située devant la règle Protection bot, puis sélectionnez **Enregistrer**.

> [!div class="mx-imgBorder"]
> ![Protection bot](../media/bot-protection/bot-protection.png)

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les règles personnalisées, consultez [Règles personnalisées pour un pare-feu d’applications web v2 sur Azure Application Gateway](custom-waf-rules-overview.md).
