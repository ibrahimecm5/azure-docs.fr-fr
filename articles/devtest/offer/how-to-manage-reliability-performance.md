---
title: Gérer la fiabilité et le niveau de performance avec les abonnements Azure Dev/Test
description: Assurez la fiabilité de vos applications grâce aux abonnements Dev/Test.
author: jamestramel
ms.author: jametra
ms.prod: visual-studio-windows
ms.topic: how-to
ms.date: 10/12/2021
ms.custom: devtestoffer
ms.openlocfilehash: fe34eb3b5fff32ab59c72c90041b095a82806e9b
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131098160"
---
# <a name="reliability-management"></a>Gestion de la fiabilité  

Bien que les services de production ne seront pas dans un abonnement Dev/Test, vous pouvez utiliser d’autres étapes dans votre abonnement Azure Dev/Test pour garantir la fiabilité en production.  

Lorsque vous utilisez les abonnements Dev/Test de votre organisation, vous devez décider de la façon dont vous allez :  

- données de contrôle  
- Contrôler la sécurité et l’accès  
- Gérer la durée de bon fonctionnement de ce système de production  

En général, vous passez par différentes étapes de déploiement avant la production : partage, assurance qualité, intégration, mise en lots et basculement. Selon la façon dont votre entreprise définit ces étapes, votre utilisation d’un abonnement Dev/Test d’entreprise peut changer.  

Si vous exécutez des services stratégiques comme des applications destinées aux clients, n’utilisez pas d’abonnement Dev/Test. Les abonnements Dev/Test ne sont pas assortis d’un Contrat de niveau de service garanti financièrement. Ces abonnements sont destinés aux tests de préproduction et au développement.  

## <a name="site-reliability-engineering-sre"></a>Ingénierie de fiabilité des sites (SRE)  

Pour en savoir plus sur l’ingénierie et la gestion de la fiabilité, envisagez la gestion de la fiabilité des sites, une discipline d’ingénierie qui aide les organisations à atteindre une fiabilité appropriée et durable de leurs systèmes, services et produits.  

La différence entre SRE et DevOps fait encore l’objet de discussions dans le domaine. Voici quelques différences qui font l’objet d’un large consensus :  

- La SRE est une discipline d’ingénierie axée sur la fiabilité. Le DevOps est un mouvement culturel qui a émergé de l’envie de briser les silos associés aux organisations de développement et d’exploitation.  
- La SRE peut être le nom d’un rôle, comme dans « Je suis un ingénieur de fiabilité des sites (SRE) ». Ce n’est pas le cas du DevOps.  
- La SRE a tendance à être normative. Le DevOps ne l’est volontairement pas. L’adoption quasi universelle de l’intégration continue et de la livraison continue et les principes agiles sont ce qui s’approche le plus du DevOps.  

Si vous souhaitez en savoir plus sur la pratique de la SRE, consultez les liens suivants :  

- [SRE en contexte](/learn/modules/intro-to-site-reliability-engineering/3-sre-in-context.md)  
- [Principes et pratiques clés de la SRE : cycles vertueux](/learn/modules/intro-to-site-reliability-engineering/4-key-principles-1-virtuous-cycles.md)  
- [Principes et pratiques clés de la SRE : l’aspect humain de la SRE](/learn/modules/intro-to-site-reliability-engineering/5-key-principles-2-human-side-of-sre.md)  
- [Prise en main de la SRE](/learn/modules/intro-to-site-reliability-engineering/6-getting-started.md)  

## <a name="service-level-agreements"></a>Contrats de niveau de service  

Enterprise Dev/Test est exclusivement destiné au développement et au test de vos applications. L’utilisation de l’abonnement n’est pas assortie d’un Contrat de niveau de service (SLA) garanti financièrement.  

## <a name="learn-to-use-different-types-of-devtest-subscriptions"></a>Apprendre à utiliser les différents types d’abonnements Dev/Test  

Que vous ayez besoin de [crédits Azure mensuels pour les abonnés à Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), d’[abonnements Enterprise Dev/Test](https://azure.microsoft.com/offers/ms-azr-0148p/) ou d’un [abonnement Dev/Test - Paiement à l’utilisation](https://azure.microsoft.com/offers/ms-azr-0023p/) (PAYG), vous pouvez facilement trouver des offres qui conviennent aux particuliers ou à une équipe.  

## <a name="managing-individual-credit-subscriptions"></a>Gestion des abonnements individuels de crédit  

Les crédits Azure de Visual Studio sont un avantage individuel, pour le développement individuel de Dev/Test et de la boucle interne. Vous ne pouvez pas regrouper les crédits entre développeurs. Les abonnements de crédit sont toujours des abonnements Azure, mais ils sont surtout une offre Azure spécifique. Gérez vos abonnements de crédit de la même façon que vous gérez d’autres abonnements Azure afin de pouvoir travailler au sein de groupes et d’équipes. Vous pouvez supprimer les limites de dépense individuelles avec une carte de crédit ou si votre abonnement Enterprise Dev/Test passe par la méthode d’approvisionnement choisie par votre entreprise.  

Les activités de la boucle interne des développeurs utilisent souvent des crédits, mais passent ensuite aux abonnements Azure Dev/Test d’entreprise ou d’organisation, y compris le paiement à l’utilisation. Ainsi, lorsque vous suivez les processus DevOps, vous pouvez effectuer une boucle interne avec votre abonnement individuel de crédit. Dans la boucle externe DevOps, les cibles de non-production se trouvent dans Enterprise Dev/Test : la production va à la production.  

Gérez vos abonnements de crédit, vos abonnements Enterprise Dev/Test et vos abonnements PAYG et segmentez vos développeurs à l’aide de [groupes d’administration](../../governance/management-groups/how-to/protect-resource-hierarchy.md) ayant chacun une hiérarchie unique.  

## <a name="using-your-organization-azure-devtest-offers"></a>Utilisation des offres Azure Dev/Test de votre organisation  

Si vous avez besoin d’un abonnement Azure Dev/Test d’organisation, vous avez le choix entre deux offres.  

- [Dev/Test - Paiement à l’utilisation (PAYG) [0023P]](https://azure.microsoft.com/offers/ms-azr-0023p/-)  
- [Enterprise Dev/Test (0148P)](https://azure.microsoft.com/offers/ms-azr-0148p/)  

Chacune est assortie de son propre ensemble de remises et nécessite un abonnement à Visual Studio.  

Chaque offre d’abonnement vous permet de rendre votre équipe opérationnelle avec des environnements de dev/test dans le cloud à l’aide de machines virtuelles préconfigurées. Créez plusieurs abonnements Azure et gérez-les à partir d’un seul compte. Vous pouvez avoir des environnements isolés et une facture distincte pour différents projets ou différentes équipes.  

Les abonnements Enterprise Dev/Test requièrent un Contrat Entreprise (EA). Les abonnements Dev/Test - Paiement à l’utilisation ne nécessitent pas d’EA, mais ils peuvent être utilisés avec un compte EA.  

## <a name="why-would-i-use-payg-offers-vs-enterprise-devtest-offers"></a>Pourquoi utiliser des offres PAYG plutôt que des offres Enterprise Dev/Test ?  

Une offre Dev/Test - PAYG peut être la bonne solution pour un abonné à Visual Studio. Contrairement aux abonnements de crédit pour une utilisation individuelle, les offres PAYG sont idéales pour le développement en équipe et vous permettent d’avoir plusieurs utilisateurs au sein d’un même abonnement. Une offre Dev/Test - PAYG pourrait vous convenir si :  

- Vous n’avez pas de Contrat entreprise. Dans ce cas, vous pouvez créer un compte PAYG uniquement avec une licence Visual Studio.  
- Vous êtes en train de créer un Contrat entreprise, mais vous devez configurer un abonnement qui n’utilise pas le contrat de votre organisation. Vous avez peut-être un projet unique qui nécessite son propre abonnement ou requiert de créer un environnement isolé facturé séparément pour les projets ou les équipes.  
- Vous préférez garder les identités isolées. Vous pouvez avoir besoin que certaines identités restent séparées des autres pour protéger l’accès aux données, aux ressources et aux applications.  
