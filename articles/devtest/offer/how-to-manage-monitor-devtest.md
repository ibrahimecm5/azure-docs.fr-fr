---
title: Gestion et supervision de vos abonnements dev/test Azure
description: Gérez vos abonnements dev/test Azure avec la flexibilité de l’environnement cloud d’Azure. Ce guide couvre également Azure Monitor pour aider à optimiser la disponibilité et les performances des applications et des services.
author: jamestramel
ms.author: jametra
ms.prod: visual-studio-windows
ms.topic: how-to
ms.date: 10/12/2021
ms.custom: devtestoffer
ms.openlocfilehash: 22fa6c747d7c15cebe1d36228965c3119fea25ea
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131097617"
---
# <a name="managing-azure-devtest-subscriptions"></a>Gestion des abonnements Azure DevTest

La gestion de vos abonnements dev/test Azure est importante. La gestion des coûts, la supervision des étapes de production, les ressources que vous déployez et les processus que vous configurez sont primordiaux dans la gestion des abonnements.  

L’environnement cloud d’Azure vous offre la flexibilité nécessaire pour gérer les coûts et les charges de travail. Dans la gestion locale, vous êtes limité à gérer les fonctionnalités par rapport aux dépenses.  

Les services au sein des niveaux d’abonnement et de groupe de ressources n’ont aucun coût : seules les ressources elles-mêmes ont un coût.  

![Diagramme des organisations et de la gouvernance Azure](media/how-to-manage-monitor-devtest/orgs-and-governance.png "Organisations et gouvernance Azure.")

Lors de la gestion du point de vue des dépenses opérationnelles, vous payez uniquement pour ce que vous utilisez. Il existe plusieurs outils avec les abonnements dev/test Azure qui vous aident à gérer le coût au cours du déploiement.  

## <a name="monitoring-through-a-different-lens"></a>Supervision sous un autre angle

[Azure Monitor](../../azure-monitor/overview.md) aide à optimiser la disponibilité et les performances pour vos applications et services. Fournissez des solutions complètes pour collecter, analyser et exploiter des données de vos environnements cloud et locaux. Comprenez les performances de vos applications. Azure Monitor identifie de manière proactive les problèmes affectant vos applications et les ressources dont elles dépendent.  

Dans Azure, utilisez la supervision pour accélérer le délai de commercialisation et garantir les données de performances dans vos services de production. Vous pouvez agréger et analyser les métriques, les journaux et les traces. Grâce à la supervision, vous pouvez également déclencher des alertes et envoyer des notifications ou appeler des solutions automatisées.  

Azure Monitor vous permet d’utiliser les avantages en matière de développement et de test pour optimiser le délai de commercialisation vos applications et la fourniture de celles-ci.  

La supervision vous permet d’optimiser vos avantages en matière de développement et de test avec les applications entièrement nouvelles et les applications existantes.  

- Pré-production avec les applications entièrement nouvelles  : avec les nouvelles applications, vous créez et activez des métriques personnalisées avec Log Analytics ou des alertes intelligentes en pré-production que vous utiliserez en production. La supervision précoce affine vos données de performances pour vos services de production.  
- Post-production avec les applications existantes : lors du déploiement de nouvelles fonctionnalités ou de l’ajout de nouvelles fonctionnalités avec une API à des applications existantes, vous pouvez déployer cette fonctionnalité en pré-production et ajuster votre supervision pour garantir des flux de données corrects de manière précoce. L’utilisation du suivi dans la pré-production de la nouvelle fonctionnalité vous offre une visibilité claire et vous permet de fusionner cette supervision avec votre système de supervision global après la production. Intègre les données de performances récentes aux journaux existants pour garantir l’utilisation de la supervision.  
- La supervision des différentes étapes de déploiement hors production reflète la supervision pendant la production. Vous gérez vos coûts et analysez vos dépenses avant la production et en post-production.  

## <a name="cost-management"></a>Cost Management

[Azure Cost Management](../../cost-management-billing/cost-management-billing-overview.md) vous permet d’améliorer de manière significative les performances techniques de vos charges de travail métier. Il peut également réduire vos coûts et la surcharge requise pour gérer les ressources organisationnelles.  

Avec la supervision, vous pouvez utiliser des outils d’analyse des coûts en pré-production avant de déployer vos services afin d’exécuter une analyse de votre calcul de pré-production actuel pour prévoir les coûts de production et éventuellement réaliser des économies.  

## <a name="performance-management"></a>Gestion des performances

En plus d’effectuer des opérations de supervision et de gestion des coûts, vous souhaiterez tester les performances du calcul de pré-production pour les charges de travail attendues.  

En pré-production, vous pouvez configurer la mise à l’échelle en fonction de la charge. Quand vous testez votre application dans un environnement dev/test sous charge, vous obtenez de meilleurs chiffres de supervision et d’analyse des coûts. Vous êtes mieux à même de décider si vous devez démarrer à une échelle plus ou moins élevée.  

L’analyse sous charge et contrainte fournit d’autres données en pré-production, ce qui vous permet de continuer à optimiser votre délai de mise sur le marché et la livraison de votre application.  

Quand vous effectuez des tests sous charge et contrainte avec votre application ou service, la méthode de scale-up ou de scale-out dépend de vos charges de travail. Vous pouvez en savoir plus sur la mise à l’échelle de vos applications dans Azure :  

- [Montez une application en puissance dans Azure App Service](../../app-service/manage-scale-up.md)  
- [Bien démarrer avec la mise à l’échelle automatique dans Azure](../../azure-monitor/platform/autoscale-get-started.md?toc=/azure/app-service/toc.json)  

Activez la supervision de votre application avec [Application Insights](../../azure-monitor/app/app-insights-overview.md) afin de collecter des informations détaillées sur les vues de pages, les requêtes d’application et les exceptions.  

## <a name="azure-automation"></a>Azure Automation

[Azure Automation](../../automation/automation-intro.md) offre un service d’automatisation et de configuration cloud prenant en charge une gestion cohérente de vos environnements Azure et non-Azure. Cet outil vous offre un contrôle lors du déploiement, des opérations et de la désaffectation des charges de travail et des ressources. Azure Automation est toujours activé. Il fonctionne avec les ressources existantes. Azure Automation vous permet de créer des ressources ou des abonnements à la demande. Vous paierez uniquement pour ce que vous utiliserez.  

Exemple : Si vous suivez un déploiement de production dév/test, certaines des ressources et étapes doivent être opérationnelles en permanence. D’autres doivent uniquement être mises à jour et s’exécuter quelques fois par an.  

Azure Automation devient important dans ce scénario. Quand vous entrez dans un nouveau cycle de développement d’applications et que vous soumettez votre première demande de tirage (pull request), vous pouvez lancer un travail d’automatisation. Le travail déploie l’infrastructure en tant que code par le biais d’un modèle Azure Resource Manager (ARM) pour créer toutes vos ressources dans votre abonnement dev/test Azure pendant la pré-production.  

## <a name="azure-resource-manager"></a>Azure Resource Manager

Les [modèles Azure Resource Manager (ARM)](../../azure-resource-manager/templates/overview.md) implémentent une infrastructure en tant que code pour vos solutions Azure. Le modèle définit l’infrastructure et la configuration de votre projet. Vous pouvez automatiser vos déploiements.  

Vous pouvez déployer vos configurations autant de fois que vous le souhaitez pour mettre à jour l’environnement de pré-production et suivre vos coûts. Avec Azure Automation, vous pouvez exécuter et supprimer vos modèles ARM en fonction des besoins.  

Quand un service ou une ressource ne doit être mis à jour que deux fois par an, utilisez des outils DevOps pour déployer votre modèle ARM. Laissez le travail d’automatisation désactiver votre ressource, puis redéployez-la en fonction des besoins.  
