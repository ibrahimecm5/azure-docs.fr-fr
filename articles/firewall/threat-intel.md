---
title: Fonctionnalité de filtrage basé sur la Threat Intelligence du Pare-feu Azure
description: Le filtrage basé sur Threat Intelligence peut être activé pour votre pare-feu pour donner l’alerte et rejeter le trafic depuis ou vers des adresses IP et des domaines malveillants connus.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/04/2021
ms.author: victorh
ms.openlocfilehash: 5f318eabe0b1793856de040402c82951a36e00f2
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132278243"
---
# <a name="azure-firewall-threat-intelligence-based-filtering"></a>Fonctionnalité de filtrage basé sur la Threat Intelligence du Pare-feu Azure

Le filtrage basé sur Threat Intelligence peut être activé pour votre pare-feu pour donner l’alerte et rejeter le trafic depuis ou vers des adresses IP et des domaines malveillants connus. Les adresses IP et les domaines proviennent du flux de renseignements sur les menaces Microsoft, qui comprend plusieurs sources, dont l’équipe Cybersécurité Microsoft. [Intelligent Security Graph](https://www.microsoft.com/security/operations/intelligence) alimente le renseignement sur les menaces Microsoft et est utilisé par plusieurs services, notamment Microsoft Defender pour le cloud.<br>
<br>

:::image type="content" source="media/threat-intel/firewall-threat.png" alt-text="Renseignement sur les menaces du pare-feu" border="false":::

Si vous avez activé le filtrage basé sur la Threat Intelligence, les règles associées sont traitées avant les règles NAT, les règles de réseau ou les règles d’application.

Vous pouvez vous contenter d'enregistrer une alerte lorsqu'une règle est déclenchée, ou vous pouvez choisir le mode d'alerte et le mode de refus.

Par défaut, le filtrage basé sur la Threat Intelligence est activé en mode d'alerte. Vous ne pouvez pas désactiver cette fonctionnalité ni changer de mode tant que l'interface du portail n'est pas disponible dans votre région.

:::image type="content" source="media/threat-intel/threat-intel-ui.png" alt-text="Interface du portail de filtrage basé sur le renseignement sur les menaces":::

## <a name="logs"></a>Journaux d’activité

L'extrait de journal suivant montre une règle déclenchée :

```json
{
    "category": "AzureFirewallNetworkRule",
    "time": "2018-04-16T23:45:04.8295030Z",
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
    "operationName": "AzureFirewallThreatIntelLog",
    "properties": {
         "msg": "HTTP request from 10.0.0.5:54074 to somemaliciousdomain.com:80. Action: Alert. ThreatIntel: Bot Networks"
    }
}
```

## <a name="testing"></a>Test

- **Test sortant** : les alertes de trafic sortant doivent être exceptionnelles, car elles signifient que votre environnement a été compromis. Pour tester le fonctionnement des alertes de trafic sortant, un nom de domaine complet de test a été créé afin de déclencher une alerte. Utilisez `testmaliciousdomain.eastus.cloudapp.azure.com` pour vos tests sortants.

- **Test entrant** : vous pouvez vous attendre à recevoir des alertes de trafic entrant si des règles DNAT sont configurées sur le pare-feu, même si seules certaines sources sont autorisées sur la règle DNAT et que le trafic est autrement refusé. Le Pare-feu Azure n’alerte pas sur tous les scanneurs de ports connus ; il alerte uniquement sur les scanneurs connus pour leurs activités malveillantes.

## <a name="next-steps"></a>Étapes suivantes

- Consultez [Exemples Log Analytics pour le Pare-feu Azure](./firewall-workbook.md)
- Apprenez à [déployer et configurer un Pare-feu Azure](tutorial-firewall-deploy-portal.md)
- Consultez le [Rapport des renseignements de sécurité de Microsoft](https://www.microsoft.com/en-us/security/operations/security-intelligence-report)
