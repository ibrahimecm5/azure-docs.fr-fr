---
author: cherylmc
ms.author: cherylmc
ms.date: 10/15/2021
ms.service: virtual-wan
ms.topic: include
ms.openlocfilehash: 5cac86b599e7e52cbc6018de6d97392bf28e4608
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131520227"
---
1. Ouvrez le WAN virtuel que vous avez créé.

1. Sélectionnez un hub virtuel associé au WAN virtuel pour ouvrir la page du hub.

1. Supprimez toutes les entités de passerelle dans l’ordre ci-dessous pour le type de passerelle. Cette opération peut prendre 30 minutes.

    **VPN :**  
   1. Déconnecter les sites VPN  
   1. Supprimer les connexions VPN  
   1. Créer des passerelles VPN  

    **ExpressRoute :**  
   1. Supprimer des connexions ExpressRoute  
   1. Supprimer des passerelles ExpressRoute  

1. Vous pouvez supprimer le hub à ce stade ou le supprimer ultérieurement en même temps que vous supprimez le groupe de ressources.

1. Répétez cette opération pour tous les hubs associés au WAN virtuel.

1. Accédez au groupe de ressources sur le portail Azure.

1. Sélectionnez **Supprimer le groupe de ressources**. Tous les éléments du groupe de ressources sont alors supprimés, y compris les hubs et le WAN virtuel.