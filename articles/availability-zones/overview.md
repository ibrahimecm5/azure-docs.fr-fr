---
title: Résilience dans Azure
description: Découvrez la résilience dans Azure.
author: prsandhu
ms.service: azure
ms.topic: conceptual
ms.date: 10/21/2021
ms.author: rarco
ms.reviewer: cynthn
ms.custom: ''
ms.openlocfilehash: 8956555eb5393919b1e951fd13d2de17013744cc
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131858633"
---
# <a name="resiliency-in-azure"></a>Résilience dans Azure

La **résilience** est la capacité d’un système à récupérer après des défaillances et à continuer de fonctionner. Il s’agit non seulement d’éviter les défaillances, mais également d’y répondre de manière à réduire le temps d’arrêt et la perte de données. Dans la mesure où les défaillances peuvent se produire à différents niveaux, il est important de disposer d’une protection pour tous les types en fonction des exigences de disponibilité du service. La résilience dans Azure prend en charge et étend les fonctionnalités qui répondent aux pannes en temps réel afin de garantir une protection continue des services et données pour les applications stratégiques qui exigent un temps d’arrêt quasi nul et une confiance élevée des clients.

Azure propose des services de résilience intégrés que vous pouvez exploiter et gérer en fonction des besoins de votre entreprise. Quelle que soit l’ampleur de la défaillance (un seul nœud matériel, tout un rack, un centre de donnée ou une région à grande échelle), Azure fournit des solutions qui améliorent la résilience. Par exemple, les groupes à haute disponibilité garantissent que les machines virtuelles déployées sur Azure sont distribuées sur plusieurs nœuds matériels isolés dans un cluster. Les zones de disponibilité protègent les applications et les données des clients contre toute défaillance du centre de données dans plusieurs emplacements physiques au sein d’une région. Les **régions** et les **zones de disponibilité** sont au centre de votre stratégie de conception et de résilience des applications. Elles sont abordées plus en détail plus loin dans cet article.

## <a name="resiliency-requirements"></a>Exigences en matière de résilience

Le niveau de résilience requis pour une solution Azure dépend de plusieurs éléments. Ce sont le contrat de niveau de service (SLA, Service-Level Agreement) de disponibilité et de latence ainsi que d’autres exigences métier qui déterminent les choix architecturaux et le niveau de résilience. Ils doivent donc être considérés en premier. Les exigences en matière de disponibilité couvrent le temps d’arrêt acceptable (ainsi que le coût pour votre entreprise) ainsi que la quantité d’argent et le temps que vous pouvez de manière réaliste investir pour rendre une application hautement disponible.  

La création de systèmes résilients sur Azure constitue une **responsabilité partagée**. Microsoft est responsable de la fiabilité de la plateforme cloud, et notamment de son réseau global et de ses centres de données. Les clients et partenaires Azure assument la responsabilité de la résilience de leurs applications cloud, en appliquant les meilleures pratiques architecturales en fonction des besoins de chaque charge de travail. Bien qu’Azure s’efforce continuellement de garantir la résilience la plus élevée possible dans le contrat SLA de la plateforme cloud, vous devez définir vos propres objectifs SLA pour chacune des charges de travail de votre solution. Un contrat SLA permet d’évaluer si l’architecture répond aux exigences de l’entreprise. Plus vous vous efforcez d’obtenir des ratios élevés de disponibilité garantie par contrat SLA, plus le coût et la complexité associés augmentent. Une durée de bon fonctionnement de 99,99 % se traduit par environ 5 minutes de temps d’arrêt total par mois. Ce niveau justifie-t-il une augmentation de la complexité et du coût ? La réponse dépend des exigences de chaque entreprise. Lorsque vous décidez des engagements SLA finaux, prenez connaissance des contrats SLA pris en charge par Microsoft. Chaque service Azure possède le sien. 

## <a name="building-resiliency"></a>Création de résilience

Définissez les exigences de disponibilité de votre application au début de la planification. De nombreuses applications n’ont pas besoin d’une haute disponibilité de 100 %. Sachant cela, vous pourrez peut-être optimiser les coûts pendant les périodes non critiques. Identifiez les types de défaillances possibles d’une application, ainsi que leurs effets potentiels. Un plan de récupération doit couvrir tous les services critiques en finalisant la stratégie de récupération au niveau de chaque composant et au niveau de l’application globale. Concevez votre stratégie de récupération de façon à vous protéger contre les défaillances zonales, régionales et applicatives. Effectuez également des tests de l’environnement d’application de bout en bout pour mesurer la résilience des applications et la récupération en cas de défaillance inattendue.  

La liste de vérification suivante couvre l’étendue de la planification de la résilience. 

| **Planification de la résilience** |
| --- | 
| **Définissez** des objectifs de disponibilité et de récupération répondant aux besoins de l’entreprise. | 
| **Concevez** les fonctionnalités de résilience de vos applications en fonction des exigences de disponibilité. |
| **Alignez** les plateformes d’applications et de données sur vos besoins de fiabilité. | 
| **Configurez** les chemins de connexion de façon à promouvoir la disponibilité. | 
| **Utilisez** si nécessaire les zones de disponibilité et la planification de la récupération d’urgence pour améliorer la fiabilité et optimiser les coûts. |
| **Rendez** l’architecture de vos applications résiliente par rapport aux défaillances. | 
| **Analysez** ce qui se passe si les exigences SLA ne sont pas satisfaites. |
| **Identifiez** les points de défaillance possibles dans le système. La conception des applications doit tolérer les échecs de dépendance en déployant le coupe-circuit. | 
| **Concevez** des applications qui fonctionnent sans leurs dépendances. | 

## <a name="regions-and-availability-zones"></a>Régions et zones de disponibilité

Les régions et les Zones de disponibilité constituent une partie importante de l’équation de résilience. Les régions comportent plusieurs Zones de disponibilité physiquement séparées et connectées par un réseau haute performance présentant une latence inférieure à 2 ms entre les zones physiques pour aider vos données à rester synchronisées et accessibles en cas de problème. Vous pouvez tirer parti de cette infrastructure de manière stratégique lorsque vous concevez des applications et une infrastructure de données qui se répliquent automatiquement et fournissent des services ininterrompus entre les zones et les régions. Choisissez la région la plus adaptée à vos besoins en fonction de considérations techniques et réglementaires (fonctionnalités du service, résidence des données, exigences de conformité et latence) pour progresser dans votre stratégie de résilience.

Les services Microsoft Azure prennent en charge les zones de disponibilité. Ils permettent de piloter vos opérations cloud à une haute disponibilité optimale tout en répondant aux besoins de votre stratégie de continuité d’activité et reprise d’activité. Choisissez la région la plus adaptée à vos besoins en fonction de considérations techniques et réglementaires (fonctionnalités du service, résidence des données, exigences de conformité et latence) pour progresser dans votre stratégie de résilience. Pour plus d’informations, consultez [Régions et zones de disponibilité Azure](az-overview.md).

## <a name="shared-responsibility"></a>Responsabilité partagée

La création de systèmes résilients sur Azure constitue une responsabilité partagée. Microsoft est responsable de la fiabilité de la plateforme cloud, et notamment de son réseau global et de ses centres de données. Les clients et partenaires Azure assument la responsabilité de la résilience de leurs applications cloud, en appliquant les meilleures pratiques architecturales en fonction des besoins de chaque charge de travail. Pour plus d’informations, consultez [Programme de gestion de la continuité d’activité dans Azure](business-continuity-management-program.md). 

## <a name="next-steps"></a>Étapes suivantes

- [Régions et zones de disponibilité dans Azure](az-overview.md)
- [Services Azure prenant en charge les zones de disponibilité](az-region.md)
- [Livre blanc sur la résilience dans Azure](https://azure.microsoft.com/mediahandler/files/resourcefiles/resilience-in-azure-whitepaper/Resilience%20in%20Azure.pdf)
- [Azure Well-Architected Framework](https://www.aka.ms/WellArchitected/Framework)
- [Conseils sur l’architecture Azure](/architecture/high-availability/building-solutions-for-high-availability)
