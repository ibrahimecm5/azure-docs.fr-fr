---
title: Réseau virtuel Azure – Concepts et meilleures pratiques
description: Découvrez les concepts et les meilleures pratiques du Réseau virtuel Azure.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/03/2020
ms.author: kumud
ms.openlocfilehash: be9bae5d67b7ce341e7444a183788ab1f158ce72
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130217446"
---
# <a name="azure-virtual-network-concepts-and-best-practices"></a>Concepts et meilleures pratiques relatifs au Réseau virtuel Azure

Cet article décrit les concepts et meilleures pratiques clés relatifs au Réseau virtuel Azure.

## <a name="vnet-concepts"></a>Concepts de réseau virtuel

- **Espace d’adressage :** Lors de la création d’un réseau virtuel, vous devez spécifier un espace d’adressage IP privé personnalisé à l’aide d’adresses (RFC 1918) publiques et privées. Azure attribue aux ressources d’un réseau virtuel une adresse IP privée à partir de l’espace d’adressage que vous attribuez. Par exemple, si vous déployez une machine virtuelle dans un réseau virtuel avec l’espace d’adressage 10.0.0.0/16, la machine virtuelle reçoit une adresse IP privée telle que 10.0.0.4.
- **Sous-réseaux :** Les sous-réseaux vous permettent de segmenter le réseau virtuel en sous-réseaux, et d’allouer une partie de l’espace d’adressage du réseau virtuel à chaque sous-réseau. Vous pouvez ensuite déployer des ressources Azure dans un sous-réseau spécifique. Comme dans un réseau traditionnel, les sous-réseaux vous permettent de segmenter votre espace d’adressage de réseau virtuel en segments appropriées pour le réseau interne de l’organisation. Cela améliore également l’efficacité l’allocation d’adresse. Vous pouvez sécuriser des ressources au sein de sous-réseaux à l’aide de Groupes de sécurité réseau. Pour plus d’informations, consultez [Groupes de sécurité réseau](./network-security-groups-overview.md).
- **Régions** : Un réseau virtuel s’étend à une seule région/zone. Toutefois, plusieurs réseaux virtuels de différentes régions peuvent être connectés à l’aide du peering de réseau virtuel.
- **Abonnement :** Un réseau virtuel est limité à un abonnement. Vous pouvez implémenter plusieurs réseaux virtuels au sein de chaque [abonnement](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) Azure et de chaque [région](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#region) Azure.

## <a name="best-practices"></a>Meilleures pratiques

Lorsque vous créez votre réseau dans Azure, il est important de garder à l’esprit les principes de conception universels suivants :

- Vérifiez que les espaces d’adressage ne se chevauchent pas. Assurez-vous que votre espace d’adressage de réseau virtuel (bloc CIDR) ne chevauche pas d’autres plages réseau de votre organisation.
- Vos sous-réseaux ne doivent pas couvrir l’espace d’adressage entier du réseau virtuel. Planifiez et réserver de l’espace d’adressage pour l’avenir.
- Il est recommandé d’avoir un petit nombre de grands réseaux virtuels plutôt qu’un grand nombre de petits réseaux virtuels. Cela empêche la surcharge de gestion.
- Sécurisez vos réseaux virtuels en affectant des groupes de sécurité réseau (NSG) aux sous-réseaux situés en dessous. Pour plus d’informations sur les concepts de sécurité du réseau, consultez [Vue d’ensemble de la sécurité réseau Azure](../security/fundamentals/network-overview.md).

## <a name="next-steps"></a>Étapes suivantes

 Pour commencer à utiliser un réseau virtuel, créez-en un, déployez-y plusieurs machines virtuelles et communiquez entre les machines virtuelles. Pour en savoir plus, consultez l’article de démarrage rapide [Créer un réseau virtuel](quick-create-portal.md).
