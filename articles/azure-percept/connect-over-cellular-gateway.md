---
title: Connecter Azure Percept DK par le biais de réseaux 5G et LTE à l’aide d’une passerelle
description: Cet article explique comment connecter Azure Percept DK par le biais de réseaux 5G et LTE à l’aide d’une passerelle cellulaire.
author: juhaluoto
ms.author: amiyouss
ms.service: azure-percept
ms.topic: how-to
ms.date: 09/23/2021
ms.custom: template-how-to
ms.openlocfilehash: 5b66885afdec8e32b938c735625bd48c9c62535a
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131439965"
---
# <a name="connect-azure-percept-dk-over-5g-and-lte-networks-by-using-a-gateway"></a>Connecter Azure Percept DK par le biais de réseaux 5G et LTE à l’aide d’une passerelle

Une méthode simple pour connecter Azure Percept à Internet consiste à utiliser une passerelle qui se connecte à Internet via un réseau 5G ou LTE et fournit des ports Ethernet. Dans ce cas, Azure Percept n’a même pas conscience qu’il est connecté à un réseau 5G ou LTE. Il « sait » uniquement que son port Ethernet dispose d’une connectivité et qu’il route tout le trafic via ce port.  


## <a name="overview-of-5g-and-lte-gateway-topology"></a>Vue d’ensemble de la topologie de passerelle LTE et 5G

Le diagramme suivant montre comment une passerelle 5G ou LTE peut être facilement appairée avec Azure Percept DK (kit de développement).

:::image type="Image" source="media/connect-over-cellular/topology-v2.png" alt-text="Diagramme montrant comment Azure Percept DK se connecte à une passerelle 5G ou LTE via Ethernet." lightbox="media/connect-over-cellular/topology-expanded-v2.png":::

## <a name="if-youre-connecting-to-a-5g-or-lte-gateway"></a>Si vous vous connectez à une passerelle 5G ou LTE

Si vous connectez l’Azure Percept DK à une passerelle 5G ou LTE, tenez compte des points importants suivants :
- Configurez d’abord la passerelle, puis vérifiez que celle-ci reçoit bien une connexion via la carte SIM. Le fait de suivre cet ordre facilite la résolution des problèmes que vous pourriez rencontrer lorsque vous connectez Azure Percept DK.
- Vérifiez que les deux extrémités du câble Ethernet sont fermement connectées à la passerelle et à Azure Percept DK.
- Suivez les [instructions par défaut](./how-to-connect-over-ethernet.md) pour la connexion d’Azure Percept DK via Ethernet.
- Si votre plan 5G ou LTE a un quota, nous vous recommandons d’optimiser la quantité de données envoyées par vos modèles Azure Percept DK vers le cloud.
- Veillez à disposer d’un [pare-feu correctement configuré](./concept-security-configuration.md) qui bloque le trafic entrant externe.

## <a name="if-youre-connecting-to-the-dev-kit-via-ssh-protocol"></a>Si vous vous connectez au kit de développement via le protocole SSH

Si vous utilisez le protocole réseau SSH (Secure Shell) pour vous connecter au kit de développement via une passerelle Ethernet 5G ou LTE, utilisez l’une des options suivantes :
- **Utiliser le point d’accès Wi-Fi du kit de développement** : si vous avez désactivé le Wi-Fi, vous pouvez le réactiver en redémarrant votre kit de développement. À partir de là, vous pouvez vous connecter au point d’accès Wi-Fi du kit de développement et suivre les instructions fournies dans [Établir une connexion à Azure Percept DK par le biais de SSH](./how-to-ssh-into-percept-dk.md).
- **Utiliser une connexion Ethernet à un réseau local (LAN)**  : avec cette option, vous débranchez votre kit de développement de la passerelle 5G ou LTE et vous le connectez à un routeur LAN. Pour plus d’informations, consultez [Établir une connexion à Azure Percept DK par le biais d’Ethernet](./how-to-connect-over-ethernet.md). 
- **Utiliser les fonctionnalités d’accès à distance de la passerelle** : de nombreuses passerelles 5G et LTE incluent des gestionnaires d’accès à distance qui peuvent être utilisés pour se connecter à des appareils du réseau via une connexion SSH. Contactez le fabricant de la passerelle 5G ou LTE pour vérifier si elle offre cette fonctionnalité. Pour obtenir un exemple de gestionnaire d’accès à distance, consultez [Passerelles 5G et LTE Cradlepoint](https://customer.cradlepoint.com/s/article/NCM-Remote-Connect-LAN-Manager).
- **Utiliser le port série du kit de développement** : Azure Percept DK comprend un port de connexion série qui peut être utilisé pour se connecter directement à l’appareil. Pour plus d’informations, consultez [Établir une connexion à Azure Percept DK par le biais d’un câble série](./how-to-connect-to-percept-dk-over-serial.md).

## <a name="next-steps"></a>Étapes suivantes
En fonction de l’appareil mobile auquel vous avez accès, vous pouvez vous connecter de l’une des deux manières suivantes :

* [Établir une connexion avec un modem USB](./connect-over-cellular-usb.md)
* [Établir une connexion 5G ou LTE](./connect-over-cellular.md)
