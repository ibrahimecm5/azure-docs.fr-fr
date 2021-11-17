---
title: Utiliser des étiquettes de service
titleSuffix: Azure Web PubSub service
description: Utiliser des étiquettes de service pour autoriser le trafic sortant vers votre instance de service Azure Web PubSub
author: ArchangelSDY
ms.service: azure-web-pubsub
ms.topic: article
ms.date: 10/21/2021
ms.author: dayshen
ms.openlocfilehash: 2422fb24fd3608d035fe374bd08dcae49321f4f2
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130270936"
---
# <a name="use-service-tags-for-azure-web-pubsub-service"></a>Utiliser des étiquettes de service pour le service Azure Web PubSub

Vous pouvez utiliser des [étiquettes de service](../virtual-network/network-security-groups-overview.md#service-tags) pour le service Azure Web PubSub lors de la configuration du [groupe de sécurité réseau](../virtual-network/network-security-groups-overview.md#network-security-groups). Cela vous permet de définir une règle de sécurité réseau entrante/sortante pour les points de terminaison du service Azure Web PubSub, sans devoir coder en dur des adresses IP.

Le service Azure Web PubSub gère ces étiquettes de service. Vous ne pouvez pas créer votre propre étiquette de service ou modifier une étiquette existante. Microsoft gère ces préfixes d'adresse correspondant à l'étiquette de service, et met automatiquement à jour l'étiquette de service lorsque les adresses changent.

> [!Note]
> À partir du 15 août 2021, le service Azure Web PubSub prend en charge les étiquettes de service bidirectionnelles pour le trafic entrant et sortant.

## <a name="use-service-tag-via-azure-cli"></a>Utiliser l’étiquette de service via Azure CLI

### <a name="configure-outbound-traffic"></a>Configurer le trafic sortant

Vous pouvez autoriser le trafic sortant vers le service Azure Web PubSub en ajoutant une nouvelle règle de sécurité réseau de trafic sortant :

```azurecli-interactive
az network nsg rule create -n <rule-name> --nsg-name <nsg-name> -g <resource-group> --priority 100 --direction Outbound --destination-address-prefixes AzureWebPubSub
```

### <a name="configure-inbound-traffic"></a>Configurer le trafic entrant

Si vous utilisez un [gestionnaire d'événements](concept-service-internals.md#event_handler), vous pouvez également autoriser le trafic entrant à partir du service Azure Web PubSub en ajoutant une nouvelle règle de sécurité réseau de trafic entrant :

```azurecli-interactive
az network nsg rule create -n <rule-name> --nsg-name <nsg-name> -g <resource-group> --priority 100 --direction Inbound --source-address-prefixes AzureWebPubSub
```

## <a name="next-steps"></a>Étapes suivantes

- [Groupes de sécurité réseau - Étiquettes de service](../virtual-network/network-security-groups-overview.md#security-rules)