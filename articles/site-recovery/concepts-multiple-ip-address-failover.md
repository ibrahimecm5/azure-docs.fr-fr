---
title: Configurer le basculement de plusieurs adresses IP avec Azure Site Recovery
description: Décrit comment configurer le basculement des configurations IP secondaires pour les machines virtuelles Azure
services: site-recovery
author: rishjai-msft
manager: gaggupta
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/01/2021
ms.author: rishjai
ms.openlocfilehash: a7437650c11797d5a00c7c8684138632555b05a2
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131479169"
---
# <a name="configure-failover-of-multiple-ip-addresses-with-azure-site-recovery"></a>Configurer le basculement de plusieurs adresses IP avec Azure Site Recovery

Une ou plusieurs configurations IP sont associées à chaque carte réseau attachée à une machine virtuelle. Une adresse IP privée statique ou dynamique est affectée à chaque configuration. Une ressource d’adresse IP publique peut également être associée à chaque configuration. Une adresse IP publique statique ou dynamique est affectée à une ressource d’adresse IP publique. Pour plus d’informations sur les [adresses IP dans Azure](../virtual-network/ip-services/public-ip-addresses.md), consultez l’article Adresses IP dans Azure.

Aujourd’hui, Site Recovery configure automatiquement le basculement de la configuration IP principale. Cet article décrit comment configurer le basculement des configurations IP secondaires avec Site Recovery. Notez que cela est pris en charge uniquement pour les machines virtuelles Azure.

## <a name="configure-secondary-ip-address-failover-via-azure-portal"></a>Configurer le basculement d’adresse IP secondaire via le portail Azure

Site Recovery configure automatiquement le basculement de votre configuration IP principale lorsque vous activez la réplication pour la machine virtuelle. Les configurations IP secondaires doivent être configurées manuellement une fois l’activation de la réplication accomplie. Pour cela, vous avez besoin d’une machine virtuelle protégée disposant d’une ou plusieurs configurations IP secondaires.

Suivez les étapes décrites :
1. Accédez au panneau **réseau** sur la page Éléments répliqués.

    :::image type="content" source="./media/concepts-multiple-ip-address-failover/network-tab.png" alt-text="Panneau Éléments répliqués":::
    

2. Le texte en surbrillance suivant s’affiche.  Cliquez sur **Modifier** pour le modifier.
 
    :::image type="content" source="./media/concepts-multiple-ip-address-failover/network-edit.png" alt-text="Mode d’édition de l’onglet Réseau" lightbox="./media/concepts-multiple-ip-address-failover/network-edit-expanded.png":::    

3. Cliquez sur « + Configurations IP ». Deux options s’affichent pour ajouter toutes les configurations IP ou ajouter des configurations IP de manière sélective.

    :::image type="content" source="./media/concepts-multiple-ip-address-failover/add-ip-configurations.png" alt-text="Ajouter des configurations IP":::

4. Lorsque vous cliquez sur **Ajouter toutes les configurations IP secondaires**, toutes les configurations s’affichent dans la grille ci-dessous, que vous pouvez configurer comme vous le souhaitez.

    :::image type="content" source="./media/concepts-multiple-ip-address-failover/add-all-ip-configurations.png" alt-text="Ajouter toutes les configurations IP" lightbox="./media/concepts-multiple-ip-address-failover/add-all-ip-configurations-expanded.png":::    

5. Sinon, lorsque vous cliquez sur **Sélectionner et ajouter des configurations IP secondaires**, un panneau s’ouvre, dans lequel vous pouvez sélectionner et ajouter des configurations IP que vous souhaitez configurer pour le basculement.

    :::image type="content" source="./media/concepts-multiple-ip-address-failover/select-and-add-ip-configurations.png" alt-text="Sélectionner et ajouter des configurations IP":::

À présent, pour chaque configuration IP que vous avez ajoutée, vous pouvez configurer séparément les valeurs d’adresse IP privée, d’adresse IP publique et de pool principal pour le basculement et le test de basculement. Cela fait, n’oubliez pas d’enregistrer les modifications.


## <a name="next-steps"></a>Étapes suivantes
- En savoir plus sur [Traffic Manager avec Azure Site Recovery](../site-recovery/concepts-traffic-manager-with-site-recovery.md)
- En savoir plus sur les [méthodes de routage](../traffic-manager/traffic-manager-routing-methods.md)de Traffic Manager
- En savoir plus sur les [plans de récupération](site-recovery-create-recovery-plans.md) pour automatiser le basculement d’application
