---
title: Microsoft Defender pour DNS – Avantages et fonctionnalités
description: Découvrez les avantages et les fonctionnalités de Microsoft Defender pour DNS.
author: memildin
ms.author: memildin
ms.date: 10/20/2021
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 5e9c316576187db360c2db9d5c6f51929d7c7c3f
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131467641"
---
# <a name="introduction-to-microsoft-defender-for-dns"></a>Présentation de Microsoft Defender pour DNS

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Microsoft Defender pour DNS fournit une couche de protection supplémentaire aux ressources qui utilisent la fonctionnalité de [résolution de noms Azure](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#azure-provided-name-resolution) d’Azure DNS. 

Dans Azure DNS, Defender pour DNS supervise les requêtes provenant de ces ressources et détecte les activités suspectes sans avoir besoin d’agents supplémentaires sur vos ressources.

## <a name="availability"></a>Disponibilité

|Aspect|Détails|
|----|:----|
|État de sortie :|Disponibilité générale|
|Prix :|**Microsoft Defender pour DNS** est facturé comme indiqué dans la [page des tarifs](https://azure.microsoft.com/pricing/details/security-center/)|
|Clouds :|:::image type="icon" source="./media/icons/yes-icon.png"::: Clouds commerciaux<br>:::image type="icon" source="./media/icons/yes-icon.png"::: Azure China 21Vianet<br>:::image type="icon" source="./media/icons/yes-icon.png":::Azure Government|
|||

## <a name="what-are-the-benefits-of-microsoft-defender-for-dns"></a>Quels sont les avantages de Microsoft Defender pour DNS ?

Microsoft Defender pour DNS détecte les activités suspectes et anormales, par exemple :

- **Exfiltration de données** à partir de vos ressources Azure via le tunneling DNS
- Communication de **programmes malveillants** avec les serveurs de commande et de contrôle
- **Attaques DNS** - Communication avec des programmes de résolution DNS malveillants 
- **Communication avec des domaines utilisés pour des activités malveillantes** telles que l’hameçonnage et le minage de cryptomonnaie

La liste complète des alertes fournie par Microsoft Defender pour DNS se trouve dans la [page de référence des alertes](alerts-reference.md#alerts-dns).

## <a name="dependencies"></a>Les dépendances

Microsoft Defender pour DNS n’utilise pas d’agent. 

Afin de protéger votre couche DNS, activez Microsoft Defender pour DNS sur chacun de vos abonnements, comme cela est décrit dans [Activer les protections renforcées](enable-enhanced-security.md).


## <a name="respond-to-microsoft-defender-for-dns-alerts"></a>Répondre aux alertes de Microsoft Defender pour DNS

Lorsque vous recevez une alerte de Microsoft Defender pour DNS, nous vous recommandons de l’investiguer et d’y répondre de la façon suivante. Microsoft Defender pour DNS protège toutes les ressources connectées. Ainsi, même si vous connaissez l’application ou l’utilisateur qui a déclenché l’alerte, il est important de vérifier la situation dans laquelle s’inscrit cette dernière.  


### <a name="step-1-contact"></a>Étape 1. Contact

1. Contactez le propriétaire de la ressource pour déterminer si le comportement était attendu ou intentionnel.
1. Si l’activité est conforme à ce qui était prévu, ignorez l’alerte.
1. Si l’activité est inattendue, traitez la ressource comme potentiellement compromise et atténuez les risques comme décrit à l’étape suivante.

### <a name="step-2-immediate-mitigation"></a>Étape 2. Atténuation immédiate 

1. Isolez la ressource du réseau pour empêcher tout mouvement latéral.
1. Exécutez une analyse complète du logiciel anti-programme malveillant sur la ressource en suivant les conseils de correction qui en résultent.
1. Examinez les logiciels installés et en cours d’exécution sur la ressource en supprimant les packages inconnus ou indésirables.
1. Rétablissez la machine à un bon état de fonctionnement connu en réinstallant le système d’exploitation, le cas échéant, et en restaurant les logiciels à partir d’une source vérifiée et exempte de programmes malveillants.
1. Appliquez les recommandations de Microsoft Defender pour le cloud concernant la machine, en corrigeant les problèmes de sécurité mis en évidence afin d’éviter de futures violations.


## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez découvert Microsoft Defender pour DNS. 

> [!div class="nextstepaction"]
> [Activer les protections renforcées](enable-enhanced-security.md)

Pour des informations connexes, consultez l’article suivant : 

- Des alertes de sécurité peuvent être générées par Defender pour le cloud ou d’autres produits de sécurité. Pour exporter toutes ces alertes vers Microsoft Sentinel, un système SIEM tiers ou tout autre outil externe, suivez les instructions indiquées dans [Exportation d’alertes vers un système SIEM](continuous-export.md).