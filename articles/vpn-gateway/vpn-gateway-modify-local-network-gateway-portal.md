---
title: 'Modifier les paramètres d’adresse IP de passerelle : portail Azure'
titleSuffix: Azure VPN Gateway
description: Découvrez comment modifier les préfixes d’adresses IP pour votre passerelle réseau locale à partir du portail Azure.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/28/2021
ms.author: cherylmc
ms.openlocfilehash: d52d7bb4134c23192c083a61b2aa1d86d472f6ce
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131448573"
---
# <a name="modify-local-network-gateway-settings-using-the-azure-portal"></a>Modifier les paramètres de la passerelle du réseau local à l’aide du portail Azure

Parfois, les paramètres de la passerelle de réseau local AddressPrefix ou GatewayIPAddress changent. Cet article vous montre comment modifier les paramètres de passerelle de votre réseau local. Vous pouvez également modifier ces paramètres à l’aide d’une méthode différente en sélectionnant une autre option dans la liste suivante :

> [!div class="op_single_selector"]
> * [Azure portal](vpn-gateway-modify-local-network-gateway-portal.md)
> * [PowerShell](vpn-gateway-modify-local-network-gateway.md)
> * [Azure CLI](vpn-gateway-modify-local-network-gateway-cli.md)
>

>[!NOTE]
> Le fait de modifier une passerelle de réseau local qui dispose d’une connexion peut entraîner des déconnexions et temps d’arrêt du tunnel.
>

## <a name="local-network-gateway-configuration"></a><a name="configure-lng"></a>Configuration de la passerelle de réseau local

La capture d’écran ci-dessous montre la page **Configuration** d’une ressource de passerelle de réseau local utilisant un point de terminaison d’adresse IP publique :

:::image type="content" source="./media/vpn-gateway-modify-local-network-gateway-portal/settings.png" alt-text="Paramètres d’adresse IP" lightbox="./media/vpn-gateway-modify-local-network-gateway-portal/settings-expand.png":::

Il s’agit de la page de configuration avec un point de terminaison de nom de domaine complet :

:::image type="content" source="./media/vpn-gateway-modify-local-network-gateway-portal/name.png" alt-text="Paramètres de nom de domaine complet":::

## <a name="to-modify-the-gateway-ip-address-or-fqdn"></a><a name="ip"></a>Pour modifier l’adresse IP ou le nom de domaine complet de la passerelle

> [!NOTE]
> Vous ne pouvez pas modifier une passerelle réseau locale entre un point de terminaison de nom de domaine complet et un point de terminaison d’adresse IP. Vous devez supprimer toutes les connexions associées à cette passerelle de réseau local, en créer une nouvelle avec le nouveau point de terminaison (adresse IP ou nom de domaine complet), puis recréer les connexions.
>

Si le périphérique VPN auquel vous souhaitez vous connecter a changé son IP publique, modifiez la passerelle réseau locale en procédant comme suit :

1. Sur la ressource de passerelle de réseau local, dans la section **Paramètres**, sélectionnez **Configuration**.
2. Dans la zone **Adresse IP**, modifiez l’adresse IP.
3. Sélectionnez **Enregistrer** pour enregistrer les paramètres.

Si le périphérique VPN auquel vous souhaitez vous connecter a changé son nom de domaine complet (FQDN), modifiez la passerelle réseau locale en procédant comme suit :

1. Sur la ressource de passerelle de réseau local, dans la section **Paramètres**, sélectionnez **Configuration**.
2. Dans la zone **Nom de domaine complet**, modifiez le nom de domaine.
3. Sélectionnez **Enregistrer** pour enregistrer les paramètres.

## <a name="to-modify-ip-address-prefixes"></a><a name="ipaddprefix"></a>Pour modifier des préfixes d’adresse IP

Pour ajouter des préfixes d’adresses :

1. Sur la ressource de passerelle de réseau local, dans la section **Paramètres**, sélectionnez **Configuration**.
2. Ajoutez l’espace d’adressage IP dans la zone *Ajouter une autre plage d’adresses*.
3. Sélectionnez **Save** (Enregistrer) pour enregistrer vos paramètres.

Pour supprimer des préfixes d’adresses :

1. Sur la ressource de passerelle de réseau local, dans la section **Paramètres**, sélectionnez **Configuration**.
2. Sélectionnez **« … »** sur la ligne contenant le préfixe à supprimer.
3. Sélectionnez **Supprimer**.
4. Sélectionnez **Save** (Enregistrer) pour enregistrer vos paramètres.

## <a name="to-modify-bgp-settings"></a><a name="bgp"></a>Pour modifier les paramètres de BGP

Pour ajouter ou mettre à jour les paramètres de BGP :

1. Sur la ressource de passerelle de réseau local, dans la section **Paramètres**, sélectionnez **Configuration**.
2. Sélectionnez **Configurer les paramètres de BGP** pour afficher ou mettre à jour les configurations BGP pour cette passerelle de réseau local
3. Ajouter ou mettre à jour le numéro de système autonome ou l’adresse IP du pair BGP dans les champs correspondants
4. Sélectionnez **Save** (Enregistrer) pour enregistrer vos paramètres.

Pour supprimer les paramètres BGP :

1. Sur la ressource de passerelle de réseau local, dans la section **Paramètres**, sélectionnez **Configuration**.
2. Désactivez **« Configurer les paramètres de BGP »** pour supprimer l’adresse IP existante de l’ASN BGP et du pair BGP.
3. Sélectionnez **Save** (Enregistrer) pour enregistrer vos paramètres.

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez vérifier votre connexion à la passerelle. Consultez [Vérifier la connexion à une passerelle](vpn-gateway-verify-connection-resource-manager.md).
