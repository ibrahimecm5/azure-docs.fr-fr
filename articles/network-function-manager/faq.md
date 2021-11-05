---
title: FAQ sur Network Function Manager
titleSuffix: Azure Network Function Manager
description: Découvrez les FAQ ayant trait à Network Function Manager.
author: prmitt
ms.service: network-function-manager
ms.topic: article
ms.date: 11/02/2021
ms.author: prmitt
ms.custom: references_regions, ignite-fall-2021
ms.openlocfilehash: 5cf6b9bdc9982adf2da86956ce41e7cd5e7458ec
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131004932"
---
# <a name="azure-network-function-manager-faq"></a>Questions fréquentes sur Azure Network Function Manager

## <a name="faqs"></a>Foire aux questions

### <a name="i-am-a-network-function-partner-and-want-to-onboard-to-network-function-manager-how-do-i-offer-my-network-function-with-nfm"></a>Je suis partenaire de fonction réseau et je souhaite intégrer Network Function Manager. Comment proposer ma fonction réseau avec NFM ?

Notre objectif consiste à fournir aux clients un écosystème complet de leur choix de fonctions réseau disponibles sur Azure Stack Edge. Envoyez-nous un e-mail à l’adresse **aznfmpartner@microsoft.com** pour discuter des exigences d’intégration.

### <a name="does-network-function-manager-support-other-azure-edge-devices-in-addition-to-azure-stack-edge-pro-with-gpu"></a>Network Function Manager (NFM) prend-il en charge d’autres appareils de périphérie Azure en plus d’Azure Stack Edge Pro avec processeur graphique (GPU) ?

NFM est actuellement proposé sur Azure Stack Edge Pro avec GPU en disponibilité générale. ASE Pro est une solution matérielle en tant que service conçu pour exécuter des fonctions réseau spécialisées, telles que le noyau de paquets mobiles et la périphérie SD-WAN. L’appareil est équipé de six ports physiques avec prise en charge de l’accélération réseau sur les ports 5 et 6. Consultez les [spécifications de l’interface réseau](../databox-online/azure-stack-edge-gpu-technical-specifications-compliance.md#network-interface-specifications) pour Azure Stack Edge Pro avec périphérique GPU. Les partenaires de fonction réseau peuvent tirer parti des fonctionnalités SR-IOV et DPDK pour doter leurs fonctions réseau de performances réseau supérieures.

### <a name="what-additional-capabilities-are-available-on-azure-stack-edge-pro-with-gpu-in-addition-to-running-network-functions"></a>Quelles sont les fonctionnalités supplémentaires disponibles sur Azure Stack Edge Pro avec GPU en plus de l’exécution des fonctions réseau ?

Azure Stack Edge (ASE) Pro avec GPU et Azure Network Function Manager font partie de la solution [Azure private MEC](../private-multi-access-edge-compute-mec/index.yml). Vous pouvez désormais exécuter un réseau mobile privé et une machine virtuelle ou une application de périphérie basée sur un conteneur sur votre appareil ASE. Cela vous permet de créer des solutions innovantes qui fournissent des contrats SLA prévisibles pour vos applications métier critiques. Azure Stack Edge Pro est également équipé d’un ou de deux [GPU](../databox-online/azure-stack-edge-gpu-technical-specifications-compliance.md#compute-acceleration-specifications) qui vous permettent de tirer parti de scénarios tels que l’inférence vidéo et la Machine Learning à la périphérie.

### <a name="what-is-the-pricing-for-network-function-manager"></a>Quel est le tarif de Network Function Manager ?

Azure Network Function Manager est proposé sans coût supplémentaire sur votre appareil Azure Stack Edge Pro. Les partenaires de fonction réseau peuvent avoir des frais distincts pour offrir leurs fonctions réseau avec le service NFM. Vérifiez auprès de votre partenaire de fonctions réseau les exigences spécifiques.

### <a name="if-my-azure-stack-edge-pro-device-is-in-a-disconnected-mode-or-partially-connected-mode-will-the-network-functions-already-deployed-stop-working"></a>Si mon appareil Azure Stack Edge Pro est en mode déconnecté ou partiellement connecté, les fonctions réseau déjà déployées cessent-elles de fonctionner ?

Le service Network Function Manager requiert une connectivité réseau vers l’appareil ASE pour les opérations de gestion afin de créer ou de supprimer des fonctions réseau, de surveiller et de dépanner les fonctions réseau en cours d’exécution sur votre appareil. Si la fonction réseau est déployée sur l’appareil ASE et que ce dernier est déconnecté ou partiellement connecté à la fonction réseau sous-jacente, les machines virtuelles doivent continuer à fonctionner sans interruption. Les fonctions réseau déployées sur ces machines virtuelles peuvent présenter des exigences différentes en fonction de la gestion de la configuration, ainsi que des exigences de connectivité réseau supplémentaires des partenaires de fonctions réseau. Contactez votre partenaire pour connaître les exigences de connectivité et modes de fonctionnement.

### <a name="which-regions-are-supported-for-nfm-will-you-add-support-for-additional-azure-regions"></a>Quelles sont les régions prises en charge pour NFM ? Envisagez-vous de prendre en charge d’autres régions Azure ?

Network Function Manager est disponible dans les régions suivantes :

[!INCLUDE [Available regions](../../includes/network-function-manager-regions-include.md)]

Azure Stack Edge Pro avec GPU est disponible dans plusieurs pays pour déployer et exécuter les fonctions réseau de votre choix. Pour obtenir la liste de tous les pays/régions où l’appareil Azure Stack Edge Pro avec GPU est disponible, accédez à la page [Tarification du GPU Azure Stack Edge Pro](https://azure.microsoft.com/pricing/details/azure-stack/edge/#azureStackEdgePro). Sous l’onglet **Azure Stack Edge Pro**, consultez la section  **Disponibilité** .

Vous pouvez inscrire l’appareil Azure Stack Edge et les ressources Network Function Manager selon vos exigences en matière de réglementation et de souveraineté des données. La région Azure associée aux ressources Network Function Manager est utilisée pour guider les opérations de gestion entre le service Cloud vers l’appareil physique.

### <a name="when-i-delete-the-managed-application-for-my-network-function-running-on-azure-stack-edge-will-the-billing-for-network-functions-automatically-stop"></a>Lorsque je supprime l’application gérée pour ma fonction réseau s’exécutant sur Azure Stack Edge, la facturation des fonctions réseau s’arrête-t-elle automatiquement ?

Contactez votre partenaire de fonctions réseau pour connaître le cycle de facturation des fonctions de réseau déployées à l’aide de Network Function Manager. Chaque partenaire dispose d’une stratégie de facturation qui lui est propre pour ses offres de fonction réseau.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d'informations, consultez la [Vue d’ensemble](overview.md).
