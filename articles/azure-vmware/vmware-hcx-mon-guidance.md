---
title: Guide sur VMware HCX Mobility Optimized Networking (MON)
description: Découvrez des cas d’usage spécifiques à Azure VMware Solution pour Mobility Optimized Networking (MON).
ms.topic: reference
ms.date: 10/04/2021
ms.openlocfilehash: 34840fedbf2ae2dbf32711f4dfee307fe3a3a5ca
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132373324"
---
# <a name="vmware-hcx-mobility-optimized-networking-mon-guidance"></a>Guide sur VMware HCX Mobility Optimized Networking (MON)

>[!NOTE]
>
>HCX Mobility Optimized Networking est officiellement pris en charge par VMware et Azure VMware Solution à partir de la version 4.1.0 de HCX. 

>[!IMPORTANT] 
>
>Avant d’activer HCX MON, lisez ci-dessous les limitations et les configurations non prises en charge :
>
>[Configurations sources non prises en charge pour HCX NE](https://docs.vmware.com/en/VMware-HCX/4.2/hcx-user-guide/GUID-DBDB4D1B-60B6-4D16-936B-4AC632606909.html)
> 
>[Limitations pour tout déploiement HCX, y compris MON](https://docs.vmware.com/en/VMware-HCX/4.2/hcx-user-guide/GUID-BEC26054-D560-46D0-98B4-7FF09501F801.html)


[HCX Mobility Optimized Networking (MON)](https://docs.vmware.com/en/VMware-HCX/4.2/hcx-user-guide/GUID-0E254D74-60A9-479C-825D-F373C41F40BC.html) est une fonctionnalité optionnelle à activer lors de l’utilisation des [extensions réseau HCX (NE)](configure-hcx-network-extension.md). MON assure un routage optimal du trafic dans certains scénarios pour éviter l’effet tromboning du réseau entre les ressources locales et informatiques sur les réseaux étendus. 

Tout au long du cycle de migration, MON optimise la mobilité des applications pour :

- Optimiser la communication L2 entre machines virtuelles (VM) lors de l’utilisation de réseaux étendus 

- Optimiser et prévenir les flux de trafic asymétriques entre l’environnement local, Azure VMware Solution et Azure


Dans cet article, vous allez découvrir les cas d’usage spécifiques à Azure VMware Solution pour MON.


## <a name="optimize-traffic-flows-across-standard-and-stretched-segments-on-the-private-cloud-side"></a>Optimiser les flux de trafic entre les segments standard et étendus du côté du cloud privé 

Dans ce scénario, VM1 est migré vers le cloud à l’aide des NE, ce qui offre une latence optimale entre machines virtuelles. Par conséquent, VM1 a besoin d’une faible latence pour VM3 sur le segment Azure VMware Solution local. Nous migrons la passerelle VM1 de l’environnement local vers Azure VMware Solution (cloud) pour garantir un chemin d’accès optimal pour le trafic (ligne bleue). Si la passerelle reste locale (ligne rouge), un effet de tromboning et une latence plus élevée sont observés. 

>[!NOTE]
>Lorsque vous activez MON sans migrer la passerelle de machine virtuelle vers le côté cloud, cela ne garantit pas un chemin d’accès optimal pour le flux de trafic.  Cela ne permet pas non plus l’évaluation des itinéraires de stratégie.

:::image type="content" source="media/tutorial-vmware-hcx/hcx-mon-user-case-diagram-1.png" alt-text="Diagramme montrant l’optimisation de la communication L2 entre machines virtuelles lors de l’utilisation de réseaux étendus." border="false":::



## <a name="optimize-and-avoid-asymmetric-traffic-flows"></a>Optimiser et éviter les flux de trafic asymétriques 

Dans ce scénario, nous partons du principe qu’une machine virtuelle locale a été migrée vers Azure VMware Solution et participe aux flux de trafic L2 et L3 de retour à l’environnement local pour accéder aux services. Nous supposons également que certaines communications de machine virtuelle à partir d’Azure (dans le réseau virtuel connecté à Azure VMware Solution) peuvent atteindre le cloud privé Azure VMware Solution.

>[!IMPORTANT]
>Le point essentiel ici consiste à planifier et éviter les flux de trafic asymétriques avec soin. 

Par défaut et sans utiliser MON, une machine virtuelle dans Azure VMware Solution sur un réseau étendu sans MON peut communiquer en retour à l’environnement local à l’aide du chemin d’accès ExpressRoute par défaut. Idéalement, et en fonction du cas d’usage des clients, il est recommandé d’évaluer la façon dont une machine virtuelle sur un segment étendu Azure VMware Solution avec MON activé doit traverser en retour à l’environnement local sur les NE ou la passerelle T0 via ExpressRoute, mais en conservant des flux de trafic symétriques.

Si vous choisissez le chemin d’accès NE, par exemple, les itinéraires de stratégie MON doivent traiter spécifiquement le sous-réseau du côté local. Dans le cas contraire, l’itinéraire 0.0/0 est utilisé. Vous pouvez trouver des itinéraires de stratégie sous le segment NE, en sélectionnant Avancé. Par défaut, toutes les adresses IP RFC1918 sont incluses dans la définition des itinéraires de stratégie MON. 

:::image type="content" source="media/tutorial-vmware-hcx/default-hcx-mon-policy-based-routes.png" alt-text="Capture d’écran montrant les itinéraires par défaut basés sur des stratégies.":::

Les itinéraires de stratégie sont évalués uniquement si la passerelle de machine virtuelle est migrée vers le cloud. Cette configuration fait que tous les sous-réseaux correspondants pour la destination sont acheminés par le biais de l’appliance NE.  S’ils ne correspondent pas, ils sont routés via la passerelle T0.

>[!NOTE]
>Pour utiliser MON dans Azure VMware Solution, il faut veiller à donner les itinéraires /32 sur BGP à ses homologues ; cela comprend l’environnement local et Azure sur la connexion ExpressRoute. Par exemple, une machine virtuelle dans Azure apprend le chemin d’accès à une machine virtuelle Azure VMware Solution sur un segment Azure VMware Solution avec MON activé. Une fois que le trafic de retour est renvoyé à T0 comme prévu, si le sous-réseau de retour est une correspondance RFC1918, le trafic est forcé sur les NE au lieu de T0.  Ensuite, il sort sur ExpressRoute en retour vers Azure du côté local.  Cela peut entraîner une confusion pour les pare-feu avec état dans le comportement de routage du milieu et asymétrique. Il peut également être utile de déterminer la façon dont les machines virtuelles sur les segments NE MON devront accéder à Internet, soit via T0 dans Azure VMware Solution, soit uniquement via les NE en retour vers l’environnement local.

:::image type="content" source="media/tutorial-vmware-hcx/hcx-mon-user-case-diagram-3.png" alt-text="Diagramme montrant la sortie RFC1918 et le flux de trafic de sortie." border="false":::

Comme indiqué dans le diagramme ci-dessus, l’important est de faire correspondre un itinéraire de stratégie à chaque sous-réseau requis. Dans le cas contraire, le trafic est routé sur T0 et non sur les NE.

 
Pour en savoir plus sur les itinéraires de stratégie, consultez [Itinéraires de stratégie de Mobility Optimized Networking](https://docs.vmware.com/en/VMware-HCX/4.1/hcx-user-guide/GUID-F45B1DB5-C640-4A75-AEC5-45C58B1C9D63.html).

