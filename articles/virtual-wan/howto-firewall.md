---
title: Installer un Pare-feu Azure dans un hub de réseau étendu virtuel
titleSuffix: Azure Virtual WAN
description: Apprenez à configurer le Pare-feu Azure dans un hub Virtual WAN.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 05/26/2021
ms.author: cherylmc
ms.openlocfilehash: bf3aae99eb62a76885040589560c505ba2a8a2c3
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132487944"
---
# <a name="configure-azure-firewall-in-a-virtual-wan-hub"></a>Configurer un Pare-feu Azure dans un hub de réseau étendu virtuel

Un **hub sécurisé** est un hub Azure Virtual WAN doté d’un Pare-feu Azure. Cet article vous guide tout au long des étapes de conversion d’un hub de réseau étendu virtuel en hub sécurisé en installant le Pare-feu Azure directement à partir des pages du portail Azure Virtual WAN.

## <a name="before-you-begin"></a>Avant de commencer

Les étapes décrites dans cet article supposent que vous avez déjà déployé un réseau étendu virtuel avec un ou plusieurs hubs.

Pour créer un nouveau réseau étendu virtuel et un nouveau hub, suivez les étapes décrites dans les articles suivants :

* [Créer un réseau étendu virtuel](virtual-wan-site-to-site-portal.md#openvwan)
* [Créer un hub](virtual-wan-site-to-site-portal.md#hub)

## <a name="view-virtual-hubs"></a>Afficher les hubs virtuels

La page **Vue d’ensemble** de votre réseau étendu virtuel affiche une liste des hubs virtuels et de hubs sécurisés. L’illustration suivante montre un réseau étendu virtuel sans hub sécurisé.

:::image type="content" source="./media/howto-firewall/overview.png" alt-text="Capture d'écran de la page de présentation d'un réseau étendu virtuel, avec une liste de hubs virtuels." lightbox="./media/howto-firewall/overview.png":::

## <a name="convert-to-secured-hub"></a>Convertir en hub sécurisé

1. Sur la page **Vue d’ensemble** de votre réseau étendu virtuel, sélectionnez le hub que vous souhaitez convertir en hub sécurisé. Sur la page du hub virtuel, vous voyez deux options pour déployer le Pare-feu Azure dans ce hub. Sélectionnez l’une ou l’autre de ces options.

   :::image type="content" source="./media/howto-firewall/security.png" alt-text="Capture d'écran de la page de présentation de votre réseau étendu virtuel, sur laquelle vous pouvez sélectionner Convertir en hub sécurisé ou Pare-feu Azure." lightbox="./media/howto-firewall/security.png":::

1. Après avoir sélectionné l’une des options, vous voyez la page **Convertir en hub sécurisé**. Sélectionnez un hub à convertir, puis sélectionnez **suivant : Pare-feu Azure** en bas de la page.

   :::image type="content" source="./media/howto-firewall/select-hub.png" alt-text="Capture d'écran de la page Convertir en hub sécurisé lorsqu'un hub est sélectionné." lightbox="./media/howto-firewall/select-hub.png":::
1. Une fois le flux de travail terminé, sélectionnez **Confirmer**.

   :::image type="content" source="./media/howto-firewall/confirm.png" alt-text="Capture d'écran du volet Convertir en hub sécurisé, dans lequel Confirmer est sélectionné." lightbox="./media/howto-firewall/confirm.png":::
1. Une fois le hub converti en hub sécurisé, vous pouvez l’afficher sur la page **Vue d’ensemble** du réseau étendu virtuel.

   :::image type="content" source="./media/howto-firewall/secured-hub.png" alt-text="Capture d'écran de l'affichage d'un hub sécurisé." lightbox="./media/howto-firewall/secured-hub.png":::

## <a name="view-hub-resources"></a>Afficher les ressources d’un hub

Dans la page **Vue d’ensemble** du réseau étendu virtuel, sélectionnez le hub sécurisé. Dans la page du hub, vous pouvez afficher toutes les ressources du hub virtuel, dont le Pare-feu Azure.

Pour afficher les paramètres du Pare-feu Azure à partir du hub sécurisé, sous **Sécurité**, sélectionnez **Paramètres du hub virtuel sécurisé**.

:::image type="content" source="./media/howto-firewall/hub-settings.png" alt-text="Capture d'écran de Paramètres du hub virtuel sécurisé." lightbox="./media/howto-firewall/hub-settings.png":::

## <a name="configure-additional-settings"></a>Configurer des paramètres supplémentaires

Pour configurer des paramètres de Pare-feu Azure supplémentaires pour le hub virtuel, sélectionnez le lien vers **Azure Firewall Manager**. Pour plus d’informations sur les stratégies de pare-feu, consultez [Azure Firewall Manager](../firewall-manager/secure-cloud-network.md#create-a-firewall-policy-and-secure-your-hub).

:::image type="content" source="./media/howto-firewall/additional-settings.png" alt-text="Capture d'écran de la page de présentation sur laquelle est sélectionnée l'option de gestion des paramètres d'itinéraire du fournisseur de sécurité pour ce hub virtuel sécurisé dans Azure Firewall Manager." lightbox="./media/howto-firewall/additional-settings.png":::

Pour revenir à la page **Vue d’ensemble** du hub, vous pouvez revenir en arrière en cliquant sur le chemin d’accès, comme indiqué par la flèche dans l’illustration suivante.

:::image type="content" source="./media/howto-firewall/arrow.png" alt-text="Capture d'écran montrant comment revenir à la page de présentation." lightbox="./media/howto-firewall/arrow.png":::

## <a name="upgrade-to-azure-firewall-premium"></a>Mise à niveau vers le Pare-feu Azure Premium
À tout moment, il est possible de procéder à une mise à niveau d’Azure Firewall Standard vers Premium en suivant ces [instructions](https://docs.microsoft.com/azure/firewall/premium-migrate#migrate-a-secure-hub-firewall). Cette opération nécessite une fenêtre de maintenance, car des temps d’arrêt minimes sont générés. 

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur Virtual WAN, consultez la [FAQ](virtual-wan-faq.md).
