---
title: Configuration de la Threat Intelligence du Pare-feu Azure
description: Découvrez comment configurer le filtrage basé sur le renseignement sur les menaces de manière à ce que votre stratégie Pare-feu Azure donne l’alerte et rejette le trafic depuis ou vers des adresses IP et des domaines malveillants connus.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: article
ms.date: 06/30/2020
ms.author: victorh
ms.openlocfilehash: 819509e08a5604957f01452e5c6ec33c20de2b2a
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132297484"
---
# <a name="azure-firewall-threat-intelligence-configuration"></a>Configuration de la Threat Intelligence du Pare-feu Azure

Le filtrage basé sur Threat Intelligence peut être configuré de manière à ce que votre stratégie de pare-feu Azure donne l’alerte et rejette le trafic depuis ou vers des adresses IP et des domaines malveillants connus. Ces adresses IP et domaines proviennent du flux Microsoft Threat Intelligence. [Intelligent Security Graph](https://www.microsoft.com/security/operations/intelligence) alimente le renseignement sur les menaces Microsoft et est utilisé par plusieurs services, notamment Microsoft Defender pour le cloud.<br>

Si vous avez configuré le filtrage basé sur la Threat Intelligence, les règles associées sont traitées avant les règles NAT, les règles de réseau ou les règles d’application.

:::image type="content" source="media/threat-intelligence-settings/threat-intelligence-policy.png" alt-text="Stratégie d’informations sur les menaces":::

## <a name="threat-intelligence-mode"></a>Mode Renseignement sur les menaces

Vous pouvez configurer le renseignement sur les menaces selon un des trois modes décrits dans le tableau suivant. Par défaut, le filtrage basé sur la Threat Intelligence est activé en mode d'alerte.

|Mode |Description  |
|---------|---------|
|`Off`     | La fonctionnalité de renseignement sur les menaces n’est pas activée pour votre pare-feu. |
|`Alert only`     | Vous allez recevoir des alertes à haut niveau de confiance pour le trafic qui traverse votre pare-feu en provenance ou à destination d’adresses IP ou de domaines malveillants connus. |
|`Alert and deny`     | Le trafic est bloqué et vous allez recevoir des alertes à haut niveau de confiance quand du trafic tente de traverser votre pare-feu en provenance ou à destination d’adresses IP ou de domaines malveillants connus. |

> [!NOTE]
> Le mode Renseignement sur les menaces est hérité des stratégies parentes par les stratégies enfants. Une stratégie enfant doit être configurée avec le même mode ou un mode plus strict que la stratégie parente.

## <a name="allowlist-addresses"></a>Adresses de la liste d’autorisations

Le renseignement sur les menaces peut déclencher des faux positifs et bloquer du trafic qui est en fait valide. Vous pouvez configurer une liste d’adresses IP autorisées afin que les informations sur les menaces ne filtrent pas les adresses, les plages ou les sous-réseaux que vous spécifiez.  

![Adresses de la liste d’autorisations](media/threat-intelligence-settings/allow-list.png)

Vous pouvez mettre à jour la liste d’autorisations avec plusieurs entrées à la fois en chargeant un fichier CSV. Le fichier CSV peut contenir seulement des adresses IP et des plages. Le fichier ne peut pas contenir d’en-têtes.

> [!NOTE]
> Les adresses de la liste d’autorisations du renseignement sur les menaces sont héritées des stratégies parentes par les stratégies enfants. Toute adresse IP ou plage ajoutée à une stratégie parente s’applique également à toutes les stratégies enfants.

## <a name="logs"></a>Journaux d’activité

L’extrait de journal suivant montre une règle déclenchée pour le trafic sortant vers un site malveillant :

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

- **Test sortant** : les alertes de trafic sortant doivent être exceptionnelles, car elles signifient que votre environnement a été compromis. Pour tester le fonctionnement des alertes de trafic sortant, un nom de domaine complet de test a été créé afin de déclencher une alerte. Utilisez **testmaliciousdomain.eastus.cloudapp.azure.com** pour vos tests sortants.

- **Test entrant** : vous pouvez vous attendre à recevoir des alertes de trafic entrant si des règles DNAT sont configurées sur le pare-feu, même si seules certaines sources sont autorisées sur la règle DNAT et que le trafic est autrement refusé. Le Pare-feu Azure n’alerte pas sur tous les scanneurs de ports connus ; il alerte uniquement sur les scanneurs connus pour leurs activités malveillantes.

## <a name="next-steps"></a>Étapes suivantes

- Consultez le [Rapport des renseignements de sécurité de Microsoft](https://www.microsoft.com/en-us/security/operations/security-intelligence-report)
