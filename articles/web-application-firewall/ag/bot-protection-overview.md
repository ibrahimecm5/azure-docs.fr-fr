---
title: Vue d'ensemble de la protection bot du WAF sur Azure Application Gateway
titleSuffix: Azure Web Application Firewall
description: Cet article fournit une vue d'ensemble de la protection bot du pare-feu d'applications web (WAF) sur Application Gateway
services: web-application-firewall
author: winthrop28
ms.service: web-application-firewall
ms.date: 07/30/2021
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: 7cc82630b2f65bdd94e02e71b3c2521fc5734a9e
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132315040"
---
# <a name="azure-web-application-firewall-on-azure-application-gateway-bot-protection-overview"></a>Vue d'ensemble de la protection bot du pare-feu d'applications web (WAF) sur Azure Application Gateway

Environ 20 % de l'ensemble du trafic Internet provient de mauvais bots. Ceux-ci effectuent des opérations telles que la capture, l'analyse et la recherche de vulnérabilités dans votre application web. Lorsque ces bots sont arrêtés au niveau du pare-feu d'applications Web (WAF), ils ne peuvent pas vous attaquer. Ils ne peuvent pas non plus utiliser vos ressources et services, comme vos serveurs principaux et autres infrastructures sous-jacentes.

Vous pouvez activer un ensemble de règles de protection bot managées pour votre WAF afin de bloquer ou de journaliser des requêtes provenant d’adresses IP malveillantes. Ces adresses IP proviennent du flux Microsoft Threat Intelligence. Intelligent Security Graph alimente le renseignement sur les menaces de Microsoft et est utilisé par plusieurs services, notamment Microsoft Defender pour le cloud.

## <a name="use-with-owasp-rulesets"></a>Utiliser avec des ensembles de règles OWASP

Vous pouvez utiliser l'ensemble de règles de protection bot avec n'importe quel ensemble de règles OWASP (2.2.9, 3.0 et 3.1). Vous ne pouvez utiliser qu'un seul ensemble de règles OWASP à la fois. L'ensemble de règles de protection bot contient une règle supplémentaire qui apparaît dans son propre ensemble de règles. Celui-ci est intitulé **Microsoft_BotManagerRuleSet_0.1** et vous pouvez l'activer ou le désactiver comme les autres règles OWASP.

![Ensemble de règles de bot](../media/bot-protection-overview/bot-ruleset.png)

## <a name="ruleset-update"></a>Mise à jour d'un ensemble de règles

La liste des mauvaises adresses IP connues des ensembles de règles d'atténuation de bots est mise à jour plusieurs fois par jour à partir du flux Microsoft Threat Intelligence pour rester synchronisé avec les bots. Vos applications web sont protégées en permanence, même lorsque les vecteurs d'attaque des bots changent.

## <a name="log-example"></a>Exemple de journaux

Voici un exemple d’entrée de journal pour la protection bot :

```
{
        "timeStamp": "0000-00-00T00:00:00+00:00",
            "resourceId": "appgw",
            "operationName": "ApplicationGatewayFirewall",
            "category": "ApplicationGatewayFirewallLog",
            "properties": {
            "instanceId": "vm1",
                "clientIp": "1.2.3.4",
                "requestUri": "/hello.php?arg1=aaaaaaabccc",
                "ruleSetType": "MicrosoftBotProtection",
                "message": "IPReputationTriggered",
                "action": "Blocked",
                "hostname": "example.com",
                "transactionId": "abc",
                "policyId": "waf policy 1",
                "policyScope": "Global",
                "policyScopeName": "Default Policy",
                "engine": "Azwaf"
        }
    }
```

## <a name="next-steps"></a>Étapes suivantes

- [Configurer la protection bot pour le pare-feu d'applications web (WAF) sur Azure Application Gateway](bot-protection.md)
