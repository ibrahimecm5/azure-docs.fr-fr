---
title: Azure Bastion | Microsoft Docs
description: Découvrez Azure Bastion, qui offre une connectivité RDP/SSH sécurisée et transparente à vos machines virtuelles sans exposer les ports RDP/SSH en externe.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: overview
ms.date: 10/12/2021
ms.author: cherylmc
ms.custom: contperf-fy2q1-portal, ignite-fall-2021
ms.openlocfilehash: 6fbfd04283e5bf89be32c89294ad5d26fb6e5af2
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131426693"
---
# <a name="what-is-azure-bastion"></a>Qu’est-ce qu’Azure Bastion ?

Azure Bastion est un service que vous déployez et qui vous permet de vous connecter à une machine virtuelle à l’aide de votre navigateur et du portail Azure. Le service Azure Bastion est un service PaaS complètement managé par la plateforme que vous provisionnez au sein de votre réseau virtuel. Il fournit une connectivité RDP/SSH sécurisée et fluide à vos machines virtuelles, directement à partir du portail Azure via TLS. Quand vous vous connectez via Azure Bastion, vos machines virtuelles n’ont pas besoin d’adresse IP publique, d’agent ou de logiciel client spécial.

Bastion fournit une connectivité RDP et SSH sécurisée à toutes les machines virtuelles du réseau virtuel dans lequel il est provisionné. Azure Bastion protège vos machines virtuelles contre l’exposition des ports RDP/SSH au monde extérieur, tout en fournissant un accès sécurisé à l’aide de RDP/SSH.

:::image type="content" source="./media/bastion-overview/architecture.png" alt-text="Diagramme montrant l’architecture Azure Bastion.":::

## <a name="key-benefits"></a><a name="key"></a>Principaux avantages

* **RDP et SSH directement dans le Portail Azure :** vous pouvez accéder directement à la session RDP et SSH dans le Portail Azure en un simple clic.
* **Session à distance sur TLS et traversée de pare-feu pour RDP/SSH :** Azure Bastion utilise un client web basé sur HTML5 automatiquement diffusé en continu sur votre appareil local. Vous bénéficiez d’une session RDP/SSH sur TLS sur le port 443, ce qui vous permet de traverser les pare-feu d’entreprise en toute sécurité.
* **Aucune adresse IP publique n’est requise sur la machine virtuelle Azure :** Azure Bastion ouvre la connexion RDP/SSH à votre machine virtuelle Azure en utilisant une adresse IP privée sur votre machine virtuelle. Vous n’avez pas besoin d’une adresse IP publique sur votre machine virtuelle.
* **Gestion aisée des groupes de sécurité réseau** : Azure Bastion est un service PaaS de plateforme complètement managé d’Azure, renforcé en interne pour vous fournir une connectivité RDP/SSH sécurisée. Vous n’avez pas besoin d’appliquer des groupes de sécurité réseau sur le sous-réseau Azure Bastion. Comme Azure Bastion se connecte à vos machines virtuelles par le biais d’une adresse IP privée, vous pouvez configurer vos groupes de sécurité réseau pour autoriser RDP/SSH depuis Azure Bastion uniquement. Vous n’avez plus à gérer les groupes de sécurité réseau chaque fois que vous devez vous connecter de manière sécurisée à vos machines virtuelles. Pour plus d’informations sur les groupes de sécurité réseau, consultez [Groupes de sécurité réseau](../virtual-network/network-security-groups-overview.md#security-rules).
* **Protection contre l’analyse des ports :** parce que vous n’avez pas besoin d’exposer vos machines virtuelles à l’Internet public, celles-ci sont protégées contre l’analyse des ports par des utilisateurs malveillants situés en dehors de votre réseau virtuel.
* **Protégez-vous contre les exploits du jour zéro. Renforcement de la sécurité dans un seul endroit :** Azure Bastion est un service PaaS complètement managé par la plateforme. Comme il se trouve au périmètre de votre réseau virtuel, vous n’avez pas besoin de vous soucier du renforcement de la sécurité de chacune des machines virtuelles de votre réseau virtuel. La plateforme Azure protège contre les exploits du jour zéro en assurant une sécurité durcie permanente et à jour pour Azure Bastion.

## <a name="skus"></a><a name="sku"></a>Références SKU

Azure Bastion présente deux références SKU disponibles : de base et standard. Pour plus d’informations, notamment sur la mise à niveau d’une référence SKU, consultez l’article [Paramètres de configuration](configuration-settings.md#skus).

La table suivante présente les fonctionnalités et les références SKU correspondantes.

[!INCLUDE [Azure Bastion SKUs](../../includes/bastion-sku.md)]

## <a name="architecture"></a><a name="architecture"></a>Architecture

Azure Bastion est déployé sur un réseau virtuel et prend en charge l’appairage de réseaux virtuels. Plus précisément, Azure Bastion gère la connectivité RDP/SSH aux machines virtuelles créées dans les réseaux virtuels locaux ou homologués.

RDP et SSH font partie des moyens fondamentaux par lesquels vous pouvez vous connecter à vos charges de travail exécutées dans Azure. L’exposition des ports RDP/SSH sur Internet est déconseillée car considérée comme une surface de menace importante. Cela est surtout dû aux vulnérabilités du protocole. Pour contenir cette surface de menace, vous pouvez déployer des hôtes Bastion (également appelés serveurs de saut) du côté public de votre réseau périphérique. Les serveurs hôte Bastion sont conçus et configurés pour faire face aux attaques. Les serveurs Bastion fournissent également une connectivité RDP et SSH aux charges de travail situées derrière le bastion, ainsi qu’à l’intérieur du réseau.

:::image type="content" source="./media/bastion-overview/architecture.png" alt-text="Diagramme montrant l’architecture Azure Bastion.":::

Cette figure représente l’architecture d’un déploiement Azure Bastion. Dans ce diagramme :

* L’hôte Bastion est déployé dans le réseau virtuel qui contient le sous-réseau AzureBastionSubnet avec un préfixe minimum /26.
* L’utilisateur se connecte au portail Azure à l’aide de n’importe quel navigateur HTML5.
* L’utilisateur sélectionne la machine virtuelle à laquelle se connecter.
* D’un simple clic, la session RDP/SSH s’ouvre dans le navigateur.
* Aucune adresse IP publique n’est requise sur la machine virtuelle Azure.

## <a name="host-scaling"></a><a name="host-scaling"></a>Mise à l’échelle de l’hôte

Azure Bastion prend en charge la mise à l’échelle manuelle des hôtes. Vous pouvez configurer le nombre d’instances d’hôte (unités d’échelle) afin de gérer le nombre de connexions RDP/SSH simultanées qu’Azure Bastion peut prendre en charge. Le fait d’augmenter le nombre d’instances de l’hôte permet à Azure Bastion de gérer davantage de sessions simultanées. La diminution du nombre d’instances réduit le nombre de sessions simultanées prises en charge. Azure Bastion prend en charge jusqu’à 50 instances d’hôte. Cette fonctionnalité est disponible uniquement pour la référence SKU Azure Bastion standard.

Pour plus d’informations, consultez l’article [Paramètres de configuration](configuration-settings.md#instance).

## <a name="pricing"></a><a name="pricing"></a>Tarifs

Le tarif d’Azure Bastion implique une combinaison de tarifs horaires basés sur la référence SKU, les unités d’échelle et les taux de transfert de données. Pour des informations sur les prix, consultez la page [Tarification](https://azure.microsoft.com/pricing/details/azure-bastion) .

## <a name="whats-new"></a><a name="new"></a>Nouveautés

Abonnez-vous au flux RSS, puis consultez les dernières mises à jour des fonctionnalités Azure Bastion dans la page [Mises à jour Azure](https://azure.microsoft.com/updates/?category=networking&query=Azure%20Bastion).

## <a name="bastion-faq"></a>FAQ Bastion

Pour accéder aux questions fréquentes (FAQ), consultez la [FAQ](bastion-faq.md)Bastion.

## <a name="next-steps"></a>Étapes suivantes

* [Tutoriel : Créer un hôte Azure Bastion et se connecter à une machine virtuelle Windows](tutorial-create-host-portal.md).
* [Module Learn : présentation d’Azure Bastion](/learn/modules/intro-to-azure-bastion/).
* Découvrez certaines des autres [fonctionnalités de réseau](../networking/fundamentals/networking-overview.md) clés d’Azure.
