---
title: Sélection du chemin d’accès avec Azure Route Server
description: En savoir plus sur la façon dont Azure Route Server permet la sélection de chemins d’accès pour votre appliance virtuelle réseau.
services: route-server
author: duongau
ms.service: route-server
ms.topic: conceptual
ms.date: 11/09/2021
ms.author: duau
ms.openlocfilehash: 011156427bfaed238679d4fc4b4e6d51e566ec6e
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132350514"
---
# <a name="path-selection-with-azure-route-server"></a>Sélection du chemin d’accès avec Azure Route Server

Azure Route Server vous permet de contrôler le trafic pour router votre appliance virtuelle réseau SDWAN sur Internet vers votre réseau local. Dans cet article, nous allons parler de la façon dont Azure Route Server permet de sélectionner les chemins d’accès, ce qui vous permet de configurer votre appliance virtuelle réseau SNWAN de manière à disposer d’une [préférence de routage](../virtual-network/ip-services/routing-preference-overview.md) lors de la communication avec votre réseau local.

## <a name="how-does-it-work"></a>Comment cela fonctionne-t-il ?

:::image type="content" source="./media/path-selection/routing-preference.png" alt-text="Diagramme d’Azure Route Server et d’un SDWAN avec IP de routage Internet.":::

### <a name="cold-potato-routing"></a>Routage « cold potato »

Lorsque vous déployez une appliance virtuelle réseau SDWAN dans le même réseau virtuel que le serveur Azure Route Server, elle est configurée avec une adresse IP réseau Microsoft. Le chemin d’accès à votre site local utilise le réseau global Microsoft et quitte le réseau Microsoft le plus proche de la destination. Le routage à partir de votre site local entre dans le réseau Microsoft le plus proche de l’utilisateur sur le chemin d’accès de retour. Cette méthode de routage a pour but l’optimisation des performances, fournissant ainsi la meilleure expérience possible à un certain coût. 

### <a name="hot-potato-routing"></a>Routage « hot potato »

Pour optimiser les coûts, une deuxième méthode de routage est introduite en attribuant votre appliance virtuelle réseau SDWAN à une adresse IP Internet. Lorsque le trafic est routé vers votre site local, il quitte le réseau Microsoft dans la région où le service est hébergé. Ensuite, il est routé via Internet à l’aide du réseau du fournisseur Internet. Le routage à partir de l’environnement local entre dans le réseau Microsoft le plus proche de la région du service hébergé. Cette méthode de routage fournit le meilleur prix global lors de l’exécution d’une tâche telle que le transfert d’une grande quantité de données.

## <a name="how-to-enable-routing-preference"></a>Comment activer la préférence de routage ?

Lorsque vous créez une adresse IP publique, sélectionnez **Internet** comme *préférence de routage*. Attribuez ensuite l’adresse IP publique à l’appliance virtuelle réseau SDWAN.

:::image type="content" source="./media/path-selection/internet-ip.png" alt-text="Capture d’écran de la préférence de routage Internet pour une adresse IP publique.":::

Microsoft recommande d’implémenter une solution de connectivité à l’aide du réseau Microsoft et d’Internet pour fournir à votre environnement une couche supplémentaire de résilience.

## <a name="next-steps"></a>Étapes suivantes

- Apprenez-en davantage sur [Azure Route Server](route-server-faq.md).
- Découvrez comment [configurer Azure Route Server](quickstart-configure-route-server-powershell.md).
