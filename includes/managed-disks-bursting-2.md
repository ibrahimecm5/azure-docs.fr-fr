---
title: Fichier include
description: Fichier Include
services: virtual-machines
author: albecker1
ms.service: virtual-machines
ms.topic: include
ms.date: 11/09/2021
ms.author: albecker1
ms.custom: include file
ms.openlocfilehash: 7b1ac1f3b2fcb8c999276fecec495cf496108c12
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132134929"
---
### <a name="on-demand-bursting"></a>Bursting à la demande

Les disques SSD Premium utilisant le modèle de bursting à la demande peuvent pousser leurs capacités au-delà des cibles prévues à l’origine, aussi souvent que leur charge de travail l’exige et dans la limite de la capacité de bursting maximale. Par exemple, pour un disque P30 de 1 Tio, l’IOPS configurée est de 5 000 IOPS. Lorsque le bursting de disque est activé sur ce disque, vos charges de travail peuvent générer des E/S sur ce disque dans la limite des performances de bursting maximales, soit 30 000 IOPS et 1 000 Mbits/s. Pour les cibles de rafale max sur chaque disque pris en charge, consultez [Objectifs d’évolutivité et de performances pour les disques de machine virtuelle](../articles/virtual-machines/disks-scalability-targets.md#premium-ssd-managed-disks-per-disk-limits).

Si vous souhaitez que vos charges de travail dépassent fréquemment les capacités de performances prévues, le bursting de disque n’est pas la solution la plus rentable. Dans ce cas, nous vous recommandons de changer le niveau de performance de votre disque et de passer à un [niveau supérieur](../articles/virtual-machines/disks-performance-tiers.md) pour améliorer les performances de base. Consultez les détails de vos factures et analysez-les par rapport au modèle de trafic de vos charges de travail.

Avant d’activer le bursting à la demande, vous devez comprendre les points suivants :

[!INCLUDE [managed-disk-bursting-regions-limitations](managed-disk-bursting-regions-limitations.md)]

#### <a name="regional-availability"></a>Disponibilité régionale

[!INCLUDE [managed-disk-bursting-availability](managed-disk-bursting-availability.md)]

#### <a name="billing"></a>Facturation

Les disques SSD Premium utilisant le modèle de bursting à la demande sont facturés selon un forfait horaire d’activation de bursting et des frais de transaction s’appliquent à toutes les transactions de bursting qui dépassent les capacités cibles configurées. Les frais de transaction sont facturés à l’aide du modèle de paiement à l’utilisation en fonction des E/S de disque non mises en cache, y compris les lectures et les écritures qui dépassent les capacités cibles configurées. Voici un exemple de modèles de trafic de disque sur une heure de facturation :

Configuration du disque : SSD Premium – 1 Tio (P30), bursting de disque activé.

- 00:00:00 – 00:10:00 IOPS du disque inférieures à la capacité cible configurée de 5 000 IOPS 
- 00:10:01 – 00:10:10 L’application a émis un programme de traitement par lots, provoquant le bursting de l’IOPS du disque à 6 000 IOPS pendant 10 secondes 
- 00:10:11 – 00:59:00 IOPS du disque inférieures à la capacité cible configurée de 5 000 IOPS 
- 00:59:01 – 01:00:00 L’application a émis un programme de traitement par lots, provoquant le bursting de l’IOPS du disque à 7 000 IOPS pendant 60 secondes 

Au cours de cette heure de facturation, le coût du bursting comprend deux types de frais :

La première somme correspond au forfait de X $ (en fonction de votre région) relatif à l’activation du bursting. Ce forfait est toujours facturé sur le disque, sans tenir compte du statut d’attachement et jusqu’à ce qu’il soit désactivé. 

La seconde somme correspond au coût de transaction de bursting. Le bursting de disque s’effectue en deux temps. De 00:10:01 à 00:10:10, la transaction de bursting cumulée est de (6 000 - 5 000) x 10 = 10 000. De 00:59:01 à 01:00:00, la transaction de bursting cumulée est de (7 000 - 5 000) x 60 = 120 000. Le nombre total de transactions de bursting est de 10 000 + 120 000 = 130 000. Le coût de transaction de bursting est facturé au tarif de Y $ sur la base de 13 unités de 10 000 transactions (selon la tarification régionale).

Le coût total du bursting de disque de cette facturation est donc égal à X $ + Y $. Ce même calcul s’applique au bursting au-delà de la capacité cible configurée des Mbits/s. Nous traduisons le dépassement de Mo en transactions ayant une taille d’E/S de 256 ko. Si le trafic de votre disque dépasse à la fois les capacités cibles configurées d’IOPS et de Mbits/s, vous pouvez consulter l’exemple ci-dessous pour calculer les transactions de bursting. 

Configuration du disque : SSD Premium – 1 To (P30), bursting de disque activé.

- 00:00:01 – 00:00:05 L’application a émis un programme de traitement par lots, provoquant le bursting de l’IOPS du disque à 10 000 IOPS et 300 Mbits/s pendant 5 secondes.
- 00:00:06 – 00:00:10 L’application a émis un travail de récupération, provoquant le bursting de l’IOPS du disque à 6 000 IOPS et 600 Mbits/s pendant 5 secondes.

La transaction de bursting est comptabilisée comme le nombre maximum de transactions à partir du bursting de l’IOPS ou de celui des Mbits/s. De 00:00:01 à 00:00:05, la transaction de bursting cumulée est au maximum de ((10 000 - 5 000), (300 - 200) * 1024 / 256)) * 5 = 25 000 transactions. De 00:00:06 à 00:00:10, la transaction de bursting cumulée est au maximum de ((6 000 - 5 000), (600 - 200) * 1024 / 256)) * 5 = 8 000 transactions. En plus de cela, vous incluez le forfait d’activation du bursting pour obtenir le coût total de l’activation du bursting de disque à la demande. 

Vous pouvez consulter la [page de tarification de Disques managés](https://azure.microsoft.com/pricing/details/managed-disks/) pour obtenir plus d’informations sur la tarification et utiliser la [calculatrice de prix Azure](https://azure.microsoft.com/pricing/calculator/?service=storage) pour évaluer votre charge de travail. 


Pour activer le bursting à la demande, consultez [Activer le bursting à la demande](../articles/virtual-machines/disks-enable-bursting.md).

### <a name="credit-based-bursting"></a>Bursting basé sur les crédits

Pour les disques SSD Premium, le bursting basé sur les crédits est disponible pour les tailles de disque P20 et plus petites. Pour les disques SSD Standard, le bursting basé sur les crédits est disponible pour les tailles de disque E30 et plus petites. Pour les disques SSD Standard et Premium, le bursting basé sur les crédits est disponible dans toutes les régions des clouds Azure public, Azure Government et Azure China 21Vianet. Par défaut, le bursting de disque est activé par défaut sur tous les déploiements nouveaux et existants des tailles de disque prises en charge. Le bursting au niveau des machines virtuelles utilise uniquement le bursting basé sur les crédits.

## <a name="virtual-machine-level-bursting"></a>Bursting au niveau des machines virtuelles

Le bursting au niveau des machines virtuelles utilise uniquement le modèle basé sur les crédits pour le bursting, il est activé par défaut pour toutes les machines virtuelles qui le prennent en charge.

Le bursting au niveau des machines virtuelles est activé dans toutes les régions du cloud public Azure sur les tailles prises en charge suivantes : 
- [Série Dsv4](../articles/virtual-machines/dv4-dsv4-series.md)
- [Dasv4-series](../articles/virtual-machines/dav4-dasv4-series.md)
- [Série Ddsv4](../articles/virtual-machines/ddv4-ddsv4-series.md)
- [Série Dasv5](../articles/virtual-machines/dasv5-dadsv5-series.md)
- [Série Dadsv5](../articles/virtual-machines/dasv5-dadsv5-series.md)
- [Série Esv4](../articles/virtual-machines/ev4-esv4-series.md)
- [Easv4-series](../articles/virtual-machines/eav4-easv4-series.md)
- [Série Edsv4](../articles/virtual-machines/edv4-edsv4-series.md)
- [Série Easv5](../articles/virtual-machines/easv5-eadsv5-series.md)
- [Série Eadsv5](../articles/virtual-machines/easv5-eadsv5-series.md)
- [Série B](../articles/virtual-machines/sizes-b-series-burstable.md)
- [Série Fsv2](../articles/virtual-machines/fsv2-series.md)
- [Dsv3-series](../articles/virtual-machines/dv3-dsv3-series.md)
- [Série Esv3](../articles/virtual-machines/ev3-esv3-series.md)
- [Série Lsv2](../articles/virtual-machines/lsv2-series.md)

## <a name="bursting-flow"></a>Flux de rafale

Le système de crédits de bursting s’applique de la même manière au niveau de la machine virtuelle et au niveau du disque. Votre ressource, qu’il s’agisse d’une machine virtuelle ou d’un disque, démarre avec un stock complet de crédits dans son compartiment de bursting. Ces crédits vous permettent de pousser les capacités pendant 30 minutes maximum au débit de bursting maximal. Vous accumulez des crédits chaque fois que l’IOPS ou les Mbits/s de la ressource sont utilisés en deçà de la cible de performances de la ressource. Si votre ressource a accumulés des crédits de bursting et que votre charge de travail a besoin de performances supplémentaires, votre ressource peut utiliser ces crédits pour dépasser ses limites de performance et augmenter ses performances afin de répondre aux demandes de la charge de travail.

![Diagramme d’un compartiment de bursting.](media/managed-disks-bursting/bucket-diagram.jpg)

La façon dont vous utilisez vos crédits disponibles vous appartient. Vous pouvez utiliser vos 30 minutes de crédits de bursting consécutivement ou de manière sporadique tout au long de la journée. Lorsque les ressources sont déployées, elles sont assorties d’une allocation complète de crédits. Lorsque celle-ci est épuisée, il faut moins d’une journée pour la reconstituer. Les crédits peuvent être utilisés à votre discrétion ; il n’est pas nécessaire que le compartiment de bursting soit saturé pour que les ressources activent cette capacité. L’accumulation du bursting varie en fonction de chaque ressource, car elle est basée sur les IOPS inutilisées et les Mo/s en deçà de leurs cibles de performances. Des ressources ayant une ligne de base plus performante peuvent accumuler leurs crédits de bursting plus rapidement que les ressources ayant une ligne de base moins performante. Par exemple, un disque P1 inactif accumulera 120 IOPS par seconde, alors qu’un disque P20 inactif accumulera 2 300 IOPS par seconde.

## <a name="bursting-states"></a>États de rafale
Lorsque le mode rafale est activé, votre ressource peut être dans trois états :
- **Accumulation** : le trafic d’e/s de la ressource utilise moins de capacités que la cible de performance. L’accumulation de crédits de rafale est effectuée séparément pour les IOPS et les Mo/s. Votre ressource peut accumuler des crédits d’IOPS et utiliser des crédits de Mo/s ou inversement.
- **Rafale** : le trafic de la ressource utilise plus de capacités que la cible de performance. Le trafic en rafale consomme indépendamment les crédits de l’IOPS ou de la bande passante.
- **Constant** : le trafic de la ressource est exactement au niveau de la cible de performance.

## <a name="bursting-examples"></a>Exemples de bursting

Les exemples suivants illustrent le fonctionnement du bursting avec différentes combinaisons de machines virtuelles et de disques. Pour que les exemples soient faciles à suivre, nous allons nous concentrer sur les Mo/s, mais la même logique s’applique aux IOPS.

### <a name="non-burstable-virtual-machine-with-burstable-disks"></a>Machine virtuelle non Burstable avec des disques Burstables
**Combinaison de machines virtuelles et de disques :** 
- Standard_D8as_v4 
    - Mo/s non mis en cache : 192
- Disque de système d’exploitation P4
    - Mo/s approvisionnés : 25
    - Maximum de Mo/s en rafale : 170 
- 2 disques de données P10 
    - Mo/s approvisionnés : 100
    - Maximum de Mo/s en rafale : 170

 Lorsque la machine virtuelle démarre, elle récupère les données sur le disque du système d’exploitation. Étant donné que ce disque fait partie d’une machine virtuelle en cours de démarrage, il dispose de tous les crédits de bursting disponibles. Ces crédits permettent au disque du système d’exploitation de pousser sa vitesse de démarrage à 170 Mo/s.

![La machine virtuelle envoie une requête au disque du système d’exploitation demandant un débit de 192 Mo/s, et le disque du système d’exploitation répond en envoyant des données à 170 Mo/s.](media/managed-disks-bursting/nonbursting-vm-bursting-disk/nonbursting-vm-bursting-disk-startup.jpg)

Une fois le démarrage terminé, une application est exécutée sur la machine virtuelle, dont la charge de travail n’est pas critique. Cette charge de travail nécessite un débit de 15 Mo/s qui est réparti uniformément entre tous les disques.

![L’application envoie une requête à la machine virtuelle demandant un débit de 15 Mo/s. Cette machine virtuelle accepte la requête et envoie à son tour une requête à chacun de ses disques demandant un débit de 5 Mo/s. Chaque disque renvoie alors 5 Mo/s, et la machine virtuelle renvoie 15 Mo/s à l’application.](media/managed-disks-bursting/nonbursting-vm-bursting-disk/nonbursting-vm-bursting-disk-idling.jpg)

L’application doit ensuite effectuer un traitement par lots nécessitant 192 Mo/s. Un débit de 2 Mo/s est utilisé par le disque du système d’exploitation, le reste étant réparti uniformément entre les disques de données.

![L’application envoie une requête à la machine virtuelle demandant un débit de 192 Mo/s. Cette machine virtuelle accepte puis envoie la majeure partie de sa requête aux disques de données (95 Mo/s chacun) et 2 Mo/s au disque du système d’exploitation. Les disques de données poussent leurs capacités au maximum pour répondre à la demande et renvoient tous le débit demandé à la machine virtuelle, qui le renvoie à l’application.](media/managed-disks-bursting/nonbursting-vm-bursting-disk/nonbursting-vm-bursting-disk-bursting.jpg)

### <a name="burstable-virtual-machine-with-non-burstable-disks"></a>Machine virtuelle pouvant utiliser le mode rafale avec des disques ne pouvant pas utiliser le mode rafale
**Combinaison de machines virtuelles et de disques :** 
- Standard_L8s_v2 
    - Mo/s non mis en cache : 160
    - Maximum de Mo/s en rafale : 1 280
- Disque de système d’exploitation P50
    - Mo/s approvisionnés : 250 
- 2 disques de données P50 
    - Mo/s approvisionnés : 250

 Après le démarrage initial, une application est exécutée sur la machine virtuelle, dont la charge de travail n’est pas critique. Cette charge de travail nécessite un débit de 30 Mo/s qui est réparti uniformément entre tous les disques.
![L’application envoie une requête à la machine virtuelle demandant un débit de 30 Mo/s. Cette machine virtuelle accepte la requête et envoie à son tour une requête à chacun de ses disques demandant un débit de 10 Mo/s. Chaque disque renvoie alors 10 Mo/s, et la machine virtuelle renvoie 30 Mo/s à l’application.](media/managed-disks-bursting/bursting-vm-nonbursting-disk/burst-vm-nonbursting-disk-normal.jpg)

L’application doit ensuite effectuer un traitement par lots nécessitant 600 Mo/s. La machine virtuelle Standard_L8s_v2 pousse ses capacités au maximum pour traiter cette demande, puis les requêtes adressées aux disques sont réparties uniformément entre les disques P50.

![L’application envoie une requête à la machine virtuelle demandant un débit de 600 Mo/s. Cette machine virtuelle pousse ses capacités au maximum pour accepter la requête et envoie à son tour une requête à chacun de ses disques demandant un débit de 200 Mo/s. Chaque disque renvoie alors 200 Mo/s, et la machine virtuelle pousse à nouveau ses capacités au maximum pour renvoyer 600 Mo/s à l’application.](media/managed-disks-bursting/bursting-vm-nonbursting-disk/burst-vm-nonbursting-disk-bursting.jpg)
### <a name="burstable-virtual-machine-with-burstable-disks"></a>Machine virtuelle Burstable avec des disques Burstables
**Combinaison de machines virtuelles et de disques :** 
- Standard_L8s_v2 
    - Mo/s non mis en cache : 160
    - Maximum de Mo/s en rafale : 1 280
- Disque de système d’exploitation P4
    - Mo/s approvisionnés : 25
    - Maximum de Mo/s en rafale : 170 
- 2 disques de données P4 
    - Mo/s approvisionnés : 25
    - Maximum de Mo/s en rafale : 170 

Lorsque la machine virtuelle démarre, elle pousse ses capacités au maximum pour demander à atteindre sa limite de bursting de 1 280 Mo/s au disque du système d’exploitation, lequel répond avec des performances de bursting de 170 Mo/s.

![Au démarrage, la machine virtuelle pousse ses capacités au maximum pour envoyer une requête au disque du système d’exploitation demandant un débit de 1 280 Mo/s, et le disque du système d’exploitation pousse ses capacités au maximum pour renvoyer 1 280 Mo/s.](media/managed-disks-bursting/bursting-vm-bursting-disk/burst-vm-burst-disk-startup.jpg)

Après le démarrage, vous démarrez une application qui a une charge de travail non critique. L’application nécessite un débit de 15 Mo/s qui est réparti uniformément entre tous les disques.

![L’application envoie une requête à la machine virtuelle demandant un débit de 15 Mo/s. Cette machine virtuelle accepte la requête et envoie à son tour une requête à chacun de ses disques demandant un débit de 5 Mo/s. Chaque disque répond alors avec un débit de 5 Mo/s, et la machine virtuelle renvoie 15 Mo/s à l’application.](media/managed-disks-bursting/bursting-vm-bursting-disk/burst-vm-burst-disk-idling.jpg)

L’application doit ensuite effectuer un traitement par lots nécessitant 360 Mo/s. La machine virtuelle Standard_L8s_v2 passe en mode rafale pour traiter cette demande, puis les requêtes. Le disque du système d’exploitation ne nécessite uniquement 20 Mo/s. Les 340 Mo/s restants sont traités par les disques de données P4 en mode bursting.

![L’application envoie une requête à la machine virtuelle demandant un débit de 360 Mo/s. Cette machine virtuelle pousse ses capacités au maximum pour accepter la requête et envoie à son tour une requête à chacun de ses disques et au disque du système d’exploitation leur demandant un débit de 170 Mo/s et de 20 Mo/s respectivement. Chaque disque renvoie le débit demandé, et la machine virtuelle pousse à nouveau ses capacités au maximum pour renvoyer 360 Mo/s à l’application.](media/managed-disks-bursting/bursting-vm-bursting-disk/burst-vm-burst-disk-bursting.jpg)