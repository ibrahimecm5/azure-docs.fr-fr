---
author: cherylmc
ms.author: cherylmc
ms.date: 07/02/2021
ms.service: bastion
ms.topic: include
ms.openlocfilehash: 8a56b564d9126197b76d6e96cc56bb51eca980eb
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131521260"
---
* La plus petite taille de sous-réseau AzureBastionSubnet que vous pouvez créer est de /26. Nous vous recommandons de créer une taille /26 ou supérieure pour prendre en charge la mise à l’échelle de l’hôte. 
   * Pour plus d’informations sur la mise à l’échelle, consultez [Paramètres de configuration - Mise à l'échelle de l’hôte](../articles/bastion/configuration-settings.md#instance).
   * Pour plus d’informations sur les paramètres, consultez [Paramètres de configuration - AzureBastionSubnet](../articles/bastion/configuration-settings.md#instance).
* Créez le sous-réseau **AzureBastionSubnet** sans tables de routage ou délégations. 
* Si vous utilisez des groupes de sécurité réseau sur le sous-réseau **AzureBastionSubnet**, reportez-vous à l’article [Utiliser des groupes de sécurité réseau](../articles/bastion/bastion-nsg.md).
