---
title: Groupes de placement de proximité Azure pour les applications SAP | Microsoft Docs
description: Décrit des scénarios de déploiement SAP avec des groupes de placement de proximité Azure
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/14/2021
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: bccbced3ce40856f858fa7e0028a27c1e1c59460
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132484683"
---
# <a name="azure-proximity-placement-groups-for-optimal-network-latency-with-sap-applications"></a>Groupes de placement de proximité Azure pour une latence réseau optimale avec les applications SAP

> [!IMPORTANT]
> En novembre 2021, nous avons apporté des modifications importantes à la manière d’utiliser les groupes de placement de proximité avec la charge de travail SAP dans les déploiements de zone. 

Les applications SAP basées sur l’architecture SAP NetWeaver ou SAP S/4HANA sont sensibles à la latence réseau entre la couche Application SAP et la couche Base de données SAP. Cette sensibilité est due au fait que la plupart de la logique métier s’exécute dans la couche Application. Étant donné que la couche Application SAP exécute la logique métier, elle émet des requêtes à la base de données à une fréquence élevée (plusieurs milliers ou dizaines de milliers par seconde). Dans la plupart des cas, la nature de ces requêtes est simple. Elles peuvent souvent être exécutées sur la couche Base de données en 500 microsecondes ou moins.

Le temps passé sur le réseau à envoyer une telle requête de la couche Application à la couche Base de données et à recevoir le jeu de résultats a un impact majeur sur le temps nécessaire à l’exécution des processus métier. Cette sensibilité à la latence réseau est la raison pour laquelle vous souhaiterez peut-être atteindre une latence réseau minimale donnée dans les projets de déploiement SAP. Voir la [Note de support SAP 1100926 - FAQ : Performances réseau](https://launchpad.support.sap.com/#/notes/1100926/E) pour obtenir des instructions sur la façon de classifier la latence réseau.

Dans de nombreuses régions Azure, le nombre de centres de données a augmenté. En même temps, les clients, en particulier pour les systèmes SAP haut de gamme, utilisent des familles de machines virtuelles spéciales, telles que la famille Mv2, ou, dans de rares cas, de grandes instances HANA. Ces types de machines virtuelles Azure ne sont pas toujours disponibles dans chacun des centres de données qui collecte des données dans une région Azure. Ces faits peuvent créer des opportunités d’optimisation de la latence du réseau entre la couche application SAP et la couche SGBD SAP.

Pour vous permettre d’optimiser la latence du réseau, Azure propose des [groupes de placement de proximité](../../co-location.md). Les groupes de placement de proximité permettent de forcer le regroupement de différents types de machines virtuelles sous une branche centrale réseau qui offre une faible latence de réseau suffisante entre ces différents types de machines virtuelles, ce qui n’a pas encore été fait jusqu’à présent. Lors du déploiement de la première machine virtuelle dans un groupe de placement de proximité, la machine virtuelle est liée à une branche centrale réseau spécifique. Comme toutes les autres machines virtuelles qui vont être déployées dans le même groupe de placement de proximité, ces machines virtuelles sont regroupées sous la même branche centrale réseau. Comme c’est le cas pour ce prospect, l’utilisation de la construction introduit également des restrictions et des pièges :

- Vous ne pouvez pas supposer que tous les types de machines virtuelles Azure sont disponibles dans tous les centres de données Azure et sous chaque branche centrale réseau. Par conséquent, la combinaison de différents types de machines virtuelles dans un groupe de placement de proximité peut être sérieusement restreinte. Ces restrictions surviennent parce que le matériel hôte nécessaire à l'exécution d'un certain type de machine virtuelle peut ne pas être présent dans le centre de données ou sous la branche centrale réseau auquel le groupe de placement de proximité a été attribué.
- Lorsque vous redimensionnez des parties de machines virtuelles qui se trouvent dans un groupe de placement de proximité, vous ne pouvez pas supposer automatiquement que, dans tous les cas, le nouveau type de machine virtuelle est disponible dans le même centre de données ou sous la même branche centrale réseau que les autres machines virtuelles qui sont affectées au groupe de placement de proximité.
- Comme Azure désactive le matériel, il peut forcer certaines machines virtuelles d’un groupe de placement de proximité dans un autre centre de donnés Azure ou sous une autre branche centrale réseau. Pour plus d’informations sur ce cas, consultez les [Groupes de placement de proximité](../../co-location.md#planned-maintenance-and-proximity-placement-groups) du document  

> [!IMPORTANT]
> En raison de restrictions potentielles, seuls les groupes de placement de proximité doivent être utilisés :
>
> - Si nécessaire dans certains scénarios (voir plus loin)
> - Lorsque la latence du réseau entre la couche application et la couche SGBD est trop élevée et a un impact sur la charge de travail
> - Uniquement sur la granularité d’un seul système SAP et non pour l’ensemble du paysage système ou SAP complet
> - Pour conserver les différents types de machines virtuelles et un nombre minimum de machines virtuelles dans un groupe de placement de proximité


Les scénarios où vous avez utilisé des groupes de positionnement de proximité jusqu’à présent :

- Déploiement de la charge de travail SAP avec des groupes à haute disponibilité. Où la couche base de données SAP, la couche application SAP et les machines virtuelles ASCS/SCS ont été regroupées dans trois groupes à haute disponibilité différents. Dans ce cas, vous souhaitez vous assurer que les groupes à haute disponibilité n’ont pas été réparties dans l’ensemble de la région Azure, car cela peut, en fonction de la région Azure, entraîner une latence du réseau susceptible d’avoir un impact négatif sur la charge de travail SAP.
- Vous vouliez déployer les ressources critiques de votre charge de travail SAP dans différentes zones de disponibilité et, d’autre part, vous vouliez vous assurer que les machines virtuelles de la couche Application dans chacune des zones seraient réparties dans différents domaines d’erreur en utilisant des groupes à haute disponibilité. Dans ce cas, comme décrit plus loin dans le document, les groupes de placement de proximité sont le ciment nécessaire.
- Vous avez utilisé des groupes de placement de proximité pour regrouper des machines virtuelles afin d’atteindre une latence réseau optimale entre les services hébergés sur les machines virtuelles.

Comme pour le scénario de déploiement n° 1, dans de nombreuses régions, en particulier les régions sans zones de disponibilité et la plupart des régions avec zones de disponibilité, la latence du réseau indépendamment de l’implémentation des machines virtuelles est acceptable. Bien qu’il existe certaines régions d’Azure qui ne peuvent pas fournir une expérience suffisamment bonne sans colocaliser les trois différents groupes à haute disponibilité avec l’utilisation des groupes à haute disponibilité. À partir du scénario de déploiement n° 2, nous allons recommander une manière différente d’utiliser les groupes de placement de proximité dans les sections suivantes de ce document.



## <a name="what-are-proximity-placement-groups"></a>Définition des groupes de placement de proximité 
Un groupe de placement de proximité Azure est une construction logique. Lorsqu’un groupe de placement de proximité est défini, il est lié à une région Azure et à un groupe de ressources Azure. Quand des machines virtuelles sont déployées, un groupe de placement de proximité est référencé par :

- La première machine virtuelle Azure déployée sous une branche centrale réseau avec de nombreuses unités de calcul Azure et une faible latence réseau. Une telle branche centrale réseau correspond souvent à un seul centre de données Azure. Vous pouvez considérer la première machine virtuelle comme une « machine virtuelle d’étendue » qui est déployée dans une unité d’échelle de calcul basée sur les algorithmes d’allocation Azure qui sont finalement combinés avec les paramètres de déploiement.
- Toutes les machines virtuelles suivantes déployées qui référencent le groupe de placement de proximité vont être déployées sous la même branche centrale réseau que la première machine virtuelle.

> [!NOTE]
> Si aucun matériel hôte pouvant exécuter un type de machine virtuelle spécifique sous la branche centrale réseau que celui où la première machine virtuelle a été placée n’est déployé, le déploiement du type de machine virtuelle demandé échoue. Vous obtenez un message d’échec d’allocation qui indique que la machine virtuelle ne peut pas être prise en charge dans le périmètre du groupe de placement de proximité.

Plusieurs groupes de placement de proximité peuvent être affectés à un seul et même [groupe de ressources Azure](../../../azure-resource-manager/management/manage-resources-portal.md). En revanche, un groupe de placement de proximité ne peut être affecté qu’à un seul groupe de ressources Azure.


## <a name="proximity-placement-groups-with-sap-systems-that-use-only-azure-vms"></a>Groupes de placement de proximité avec des systèmes SAP qui utilisent uniquement des machines virtuelles Azure
Dans cette section, nous allons parcourir les architectures de déploiement utilisées jusqu’à présent et les nouvelles recommandations

### <a name="proximity-placement-groups-with-zonal-deployments"></a>Groupes de placement de proximité avec des déploiements de zone
Pour les déploiements qui n’utilisent pas de grandes instances HANA, il est important de fournir une latence réseau raisonnablement faible entre la couche Application SAP et la couche SGBD. Pour activer une latence de réseau de ce type raisonnablement faible pour un ensemble limité de scénarios, vous pouvez définir un groupe de placement de proximité Azure pour un système SAP.

Évitez de regrouper plusieurs systèmes de non-production ou de production SAP dans un même groupe de placement de proximité. Évitez les regroupements de systèmes SAP, car plus vous regroupez de systèmes dans un groupe de placement de proximité, plus la probabilité est élevée :

- Vous avez besoin d’un type de machine virtuelle qui n’est pas disponible sous la branche centrale réseau dans laquelle le groupe de placement de proximité a été affecté.
- Que les ressources des machines virtuelles non courantes, telles que les machines virtuelles de la série M, soient finalement non satisfaites si vous avez besoin d’augmenter le nombre de machines virtuelles dans un groupe de placement de proximité au fil du temps.

L’utilisation du groupe de placement de proximité que nous avons recommandée jusqu’à présent, est illustrée par ce graphique

![Anciens groupes de placement de proximité avec zones](./media/sap-proximity-placement-scenarios/vm-ppg-zone-old.png)

Vous avez créé un groupe de placement de proximité (PPG) dans chacune des deux zones de disponibilité dans lesquelles vous avez déployé votre système SAP. Toutes les machines virtuelles d’une zone particulière font partie du groupe de placement de proximité individuel de cette zone particulière. Vous avez démarré dans chaque zone avec le déploiement de la machine virtuelle SGBD pour étendre le PPG, puis vous avez déployé la machine virtuelle ASCS dans la même zone et dans le PPG. Dans une troisième étape, vous avez créé un groupe à haute disponibilité Azure, affecté le groupe à haute disponibilité au PPG dans l’étendue et déployé la couche Application SAP dans celui-ci. Cette configuration avait pour avantage d’aligner parfaitement tous les composants sous la même branche centrale réseau. Le gros inconvénient réside dans le fait que la flexibilité du redimensionnement des machines virtuelles peut être limitée.


En s’appuyant sur de nombreuses améliorations déployées par Microsoft dans les régions Azure pour réduire la latence du réseau au sein d'une zone de disponibilité Azure, les nouvelles directives de déploiement pour les déploiements zonaux, ressemblent à ceci :

![Nouveaux groupes de placement de proximité avec zones](./media/sap-proximity-placement-scenarios/vm-ppg-zone.png)

La différence avec la recommandation donnée jusqu’à présent réside dans le fait que les machines virtuelles de base de données des deux zones ne font plus partie des groupes de placement de proximité. Les groupes de placement de proximité par zone sont désormais étendus avec le déploiement de la machine virtuelle exécutant les instances SAP ASCS/SCS. Cela signifie également que pour les régions où les zones de disponibilité sont collectées par plusieurs centres de données, l’instance ASCS/SCS et la couche Application peuvent s’exécuter sous une branche centrale réseau et les machines virtuelles de base de données sous une autre branche centrale réseau. Toutefois, avec les améliorations apportées au réseau, la latence du réseau entre la couche application SAP et la couche SGBD devrait toujours être suffisante pour des performances et un débit de qualité suffisante. Grâce à cette nouvelle configuration, vous disposez d’une plus grande flexibilité pour redimensionner les machines virtuelles ou passer aux nouveaux types de machines virtuelles avec la couche SGBD ou la couche application du système SAP. 


### <a name="proximity-placement-groups-with-availability-set-deployments"></a>Groupes de placement de proximité avec des déploiements de groupe à haute disponibilité
Dans ce cas, l’objectif est d’utiliser des groupes de placement de proximité pour colocaliser les machines virtuelles déployées par le biais de groupes à haute disponibilité différents. Dans ce scénario d’utilisation, vous n’utilisez pas un déploiement contrôlé sur différentes zones de disponibilité dans une région. Au lieu de cela, vous souhaitez déployer le système SAP à l’aide de groupes à haute disponibilité. Par conséquent, vous disposez au moins d’un groupe à haute disponibilité pour les machines virtuelles SGBD, les machines virtuelles ASCS/SCS et les machines virtuelles de la couche application. Étant donné que vous ne pouvez pas spécifier au moment du déploiement d’une machine virtuelle un groupe à haute disponibilité et une zone de disponibilité, vous ne pouvez pas contrôler l’emplacement d’allocation des machines virtuelles dans les différents groupes à haute disponibilité. Ainsi, dans certaines régions Azure, la latence réseau entre les différentes machines virtuelles pourrait être trop élevée pour offrir des performances suffisantes. Par conséquent, l’architecture qui en résulte ressemble à ce qui suit :


![Groupes de placement de proximité avec AvSets](./media/sap-proximity-placement-scenarios/vm-ppg-avsets.png)

Dans ce graphique, un seul groupe de placement de proximité serait affecté à un seul système SAP. Ce PPG est affecté aux trois groupes à haute disponibilité. Le groupe de placement de proximité est ensuite étendu en déployant les premières machines virtuelles de la couche base de données dans le groupe à haute disponibilité SGBD. Cette recommandation d’architecture colocalisera toutes les machines virtuelles sous la même branche centrale réseau. Elle présente les restrictions mentionnées plus haut dans cet article. Par conséquent, l’architecture de groupe de placement de proximité doit être utilisée de manière éparse.


## <a name="proximity-placement-groups-and-hana-large-instances"></a>Groupes de placement de proximité et grandes instances HANA
Si certains de vos systèmes SAP s’appuient sur de [grandes instances HANA](./hana-overview-architecture.md) pour la couche Application, vous pouvez bénéficier d’améliorations importantes de la latence réseau entre l’unité de grandes instances HANA et les machines virtuelles Azure quand vous utilisez des unités de grandes instances HANA déployées dans des [tampons ou lignes de la révision 4](./hana-network-architecture.md#networking-architecture-for-hana-large-instance). L’une des améliorations est que les unités de grandes instances HANA, une fois déployées, sont déployées avec un groupe de placement de proximité. Vous pouvez utiliser ce groupe de placements de proximité pour déployer vos machines virtuelles de couche Application. Ainsi, ces machines virtuelles seront déployées dans le même centre de données que celui qui héberge votre unité de grandes instances HANA.

Pour déterminer si votre unité de grandes instances HANA est déployée dans un tampon ou une ligne de la révision 4, consultez l’article [Contrôle des grandes instances Azure HANA à l’aide du portail Azure](./hana-li-portal.md#look-at-attributes-of-single-hli-unit). Dans la vue d’ensemble des attributs de votre unité de grandes instances HANA, vous pouvez également déterminer le nom du groupe de placement de proximité car il a été créé au moment du déploiement de votre unité de grandes instances HANA. Le nom affiché dans la vue d’ensemble des attributs est le nom du groupe de placement de proximité dans lequel vous devez déployer vos machines virtuelles de couche Application.

Par rapport aux systèmes SAP qui utilisent uniquement des machines virtuelles Azure, quand vous utilisez de grandes instances HANA, vous disposez de moins de souplesse quant au nombre de [groupes de ressources Azure](../../../azure-resource-manager/management/manage-resources-portal.md) à utiliser. Toutes les unités de grandes instances HANA d’un [locataire de grandes instances HANA](./hana-know-terms.md) sont regroupées dans un seul groupe de ressources, comme décrit dans [cet article](./hana-li-portal.md#display-of-hana-large-instance-units-in-the-azure-portal). À moins d’effectuer un déploiement dans plusieurs locataires, par exemple pour séparer les systèmes de production et de non-production ou d’autres systèmes, toutes vos unités de grandes instances HANA seront déployées dans un locataire de grandes instances HANA. Ce locataire a une relation un-à-un avec un groupe de ressources. Toutefois, un groupe de placement de proximité distinct sera défini pour chacune des unités.

Ainsi, les relations entre les groupes de ressources Azure et les groupes de placement de proximité pour un même locataire seront les suivantes :

![Groupes de placement de proximité et grandes instances HANA](./media/sap-proximity-placement-scenarios/ppg-for-hana-large-instance-units.png)

## <a name="example-of-deployment-with-proximity-placement-groups"></a>Exemple de déploiement avec des groupes de placement de proximité
Voici quelques commandes PowerShell que vous pouvez utiliser pour déployer vos machines virtuelles avec des groupes de placement de proximité Azure.

La première étape, une fois connecté à [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/), consiste à vérifier si vous êtes dans l’abonnement Azure que vous souhaitez utiliser pour le déploiement :

<pre><code>
Get-AzureRmContext
</code></pre>

Si vous devez changer d’abonnement, vous pouvez le faire en exécutant la commande suivante :

<pre><code>
Set-AzureRmContext -Subscription "PPG test subscription"
</code></pre>

Créez un groupe de ressources Azure en exécutant la commande suivante :

<pre><code>
New-AzResourceGroup -Name "ppgexercise" -Location "westus2"
</code></pre>

Créez le groupe de placement de proximité en exécutant la commande suivante :

<pre><code>
New-AzProximityPlacementGroup -ResourceGroupName "ppgexercise" -Name "collocate" -Location "westus2"
</code></pre>

Déployez votre première machine virtuelle dans le groupe de placement de proximité à l’aide d’une commande telle que celle-ci :

<pre><code>
New-AzVm -ResourceGroupName "ppgexercise" -Name "ppgscopevm" -Location "westus2" -OpenPorts 80,3389 -ProximityPlacementGroup "collocate" -Size "Standard_E16s_v4"
</code></pre>

La commande précédente déploie une machine virtuelle Windows. Une fois le déploiement de la machine virtuelle effectué, l’étendue de la branche centrale réseau du groupe de placement de proximité est définie dans la région Azure. Tous les déploiements de machines virtuelles ultérieurs qui référencent le groupe de placement de proximité, comme indiqué dans la commande précédente, seront effectués sous la même branche centrale réseau tant que le type de machine virtuelle peut être hébergé sur le matériel placé sous cette branche centrale réseau et que de la capacité est disponible pour ce type de machine virtuelle.

## <a name="combine-availability-sets-and-availability-zones-with-proximity-placement-groups"></a>Combiner des groupes à haute disponibilité et des zones de disponibilité avec des groupes de placement de proximité
L’un des problèmes que présente l’utilisation de zones de disponibilité pour des déploiements de systèmes SAP est que vous ne pouvez pas déployer la couche Application SAP au moyen de groupes à haute disponibilité dans la zone de disponibilité spécifique. Vous souhaitez que la couche Application SAP soit déployée dans les mêmes zones que les machines virtuelles SAP ASCS/SCS. Le référencement d’une zone de disponibilité et d’un groupe à haute disponibilité lors du déploiement d’une machine virtuelle unique n’est pas possible à ce stade. Cependant, par le simple fait de déployer une machine virtuelle qui demande une zone de disponibilité, vous ne pouvez plus vous assurer que les machines virtuelles de la couche application sont réparties sur des domaines de mise à jour et d’erreur différents.

En utilisant des groupes de placement de proximité, vous pouvez contourner cette restriction. Voici la séquence de déploiement :

- Créez un groupe de placement de proximité.
- Déployez votre machine virtuelle d’ancrage, recommandée comme machine virtuelle ASCS/SCS, en référençant une zone de disponibilité.
- Créez un groupe à haute disponibilité qui référence le groupe de proximité Azure. (Voir la commande plus loin dans cet article.)
- Déployez les machines virtuelles de la couche Application en référençant le groupe à haute disponibilité et le groupe de placement de proximité.

Au lieu de déployer la première machine virtuelle comme illustré dans la section précédente, vous référencez une zone de disponibilité et le groupe de placement de proximité lors du déploiement de la machine virtuelle :

<pre><code>
New-AzVm -ResourceGroupName "ppgexercise" -Name "centralserviceszone1" -Location "westus2" -OpenPorts 80,3389 -Zone "1" -ProximityPlacementGroup "collocate" -Size "Standard_E8s_v4"
</code></pre>

Un déploiement réussi de cette machine virtuelle hébergerait l’instance ASCS/SCS du système SAP dans une zone de disponibilité. L’étendue du groupe de placement de proximité est fixée à l’une des branches centrales réseau de la zone de disponibilité que vous avez définie.

À l’étape suivante, vous devez créer les groupes à haute disponibilité que vous voulez utiliser pour la couche Application de votre système SAP.

Définissez et créez le groupe de placement de proximité. La commande permettant de créer le groupe à haute disponibilité nécessite une référence supplémentaire à l’ID de groupe de placements de proximité (et non pas à son nom). Vous pouvez obtenir l’ID du groupe de placement de proximité à l’aide de cette commande :

<pre><code>
Get-AzProximityPlacementGroup -ResourceGroupName "ppgexercise" -Name "collocate"
</code></pre>

Quand vous créez le groupe à haute disponibilité, vous devez prendre en compte des paramètres supplémentaires lors de l’utilisation de disques managés (par défaut, sauf indication contraire) et des groupes de placement de proximité :

<pre><code>
New-AzAvailabilitySet -ResourceGroupName "ppgexercise" -Name "ppgavset" -Location "westus2" -ProximityPlacementGroupId "/subscriptions/my very long ppg id string" -sku "aligned" -PlatformUpdateDomainCount 3 -PlatformFaultDomainCount 2 
</code></pre>

Dans l’idéal, vous devez utiliser trois domaines d’erreur. Toutefois, le nombre de domaines d’erreur pris en charge peut varier d’une région à l’autre. Dans ce cas, le nombre maximal de domaines d’erreur possibles pour les régions spécifiques est de deux. Pour déployer vos machines virtuelles de couche Application, vous devez ajouter une référence au nom de votre groupe à haute disponibilité et au nom du groupe de placement de proximité, comme indiqué ici :

<pre><code>
New-AzVm -ResourceGroupName "ppgexercise" -Name "appinstance1" -Location "westus2" -OpenPorts 80,3389 -AvailabilitySetName "myppgavset" -ProximityPlacementGroup "collocate" -Size "Standard_E16s_v4"
</code></pre>

Le résultat de ce déploiement est le suivant :
- Services centraux de votre système SAP situés dans une zone de disponibilité ou des zones de disponibilité spécifiques.
- Couche d’application SAP qui se trouve dans des groupes à haute disponibilité dans la même branche centrale réseau que la machine virtuelle ou les machines virtuelles SAP Central (ASCS/SCS).

> [!NOTE]
> Étant donné que vous déployez des machines virtuelles SGBD et ASCS/SCS dans une zone et d’autres machines de mêmes types dans une autre zone pour créer des configurations de haute disponibilité, vous aurez besoin d’un groupe de placement de proximité différent pour chacune des zones. Il en va de même pour tout groupe à haute disponibilité que vous utilisez.

## <a name="change-proximity-placement-group-configurations-of-an-existing-system"></a>Modifier les configurations de groupe de placement de proximité d’un système existant
Si vous avez implémenté des groupes de placement de proximité à partir des recommandations fournies jusqu’à présent, et que vous souhaitez vous adapter à la nouvelle configuration, vous pouvez le faire avec les méthodes décrites dans les articles suivants :

- [Déployer des machines virtuelles dans des groupes de placements avec Azure CLI](../../linux/proximity-placement-groups.md)
- [Déployer des machines virtuelles dans des groupes de placements avec PowerShell](../../windows/proximity-placement-groups.md) 

Vous pouvez également utiliser ces commandes dans les cas où vous obtenez des erreurs d’allocation si vous ne pouvez pas passer à un nouveau type de machine virtuelle avec une machine virtuelle existante dans le groupe de placement de proximité.

## <a name="next-steps"></a>Étapes suivantes
Consultez la documentation :

- [Check-list relative à la planification et au déploiement de la charge de travail SAP sur Azure](./sap-deployment-checklist.md)
- [Déployer des machines virtuelles dans des groupes de placements avec Azure CLI](../../linux/proximity-placement-groups.md)
- [Déployer des machines virtuelles dans des groupes de placements avec PowerShell](../../windows/proximity-placement-groups.md)
- [Facteurs à prendre en compte pour le déploiement SGBD des machines virtuelles Azure pour la charge de travail SAP](./dbms_guide_general.md)