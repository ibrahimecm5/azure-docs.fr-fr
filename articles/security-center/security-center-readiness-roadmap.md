---
title: Feuille de route de préparation de Defender pour le cloud | Microsoft Docs
description: Ce document fournit une feuille de route de préparation pour vous préparer à l’évolution de Defender pour le cloud.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 04/03/2018
ms.author: memildin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 045dbd56f1177f334916850f481c7fa4f9f48f4c
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131009507"
---
# <a name="defender-for-cloud-readiness-roadmap"></a>Feuille de route de préparation de Defender pour le cloud

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Ce document fournit une feuille de route de préparation qui vous aidera à prendre en main Defender pour le cloud.

## <a name="understanding-defender-for-cloud"></a>Comprendre Defender pour le cloud
Defender pour le cloud fournit une gestion unifiée de la sécurité et une protection avancée contre les menaces pour les charges de travail s’exécutant dans Azure, en local et dans d’autres clouds. 

Utilisez les ressources suivantes pour prendre en main Defender pour le cloud.

Articles
- [Présentation de Defender pour le cloud](defender-for-cloud-introduction.md)
- [Guide de démarrage rapide de Defender pour le cloud](get-started.md)

Videos
- [Vidéo de présentation rapide](https://azure.microsoft.com/resources/videos/introduction-to-azure-security-center/)
- [Vue d’ensemble des fonctionnalités de prévention, de détection et de réponse de Defender pour le cloud](https://azure.microsoft.com/resources/videos/azurecon-2015-new-azure-security-center-helps-you-prevent-detect-and-respond-to-threats/)

## <a name="planning-and-operations"></a>Planification et fonctionnement

Pour tirer pleinement parti de Defender pour le cloud, il est important de comprendre comment les différents utilisateurs ou équipes de votre entreprise utilisent ce service afin de répondre aux besoins de sécurisation des opérations, de supervision, de gouvernance et de réponse aux incidents.

Utilisez les ressources suivantes pour vous aider lors des processus de planification et de fonctionnement.

- [Guide des opérations et planification de Defender pour le cloud](security-center-planning-and-operations-guide.md)


### <a name="onboarding-computers-to-defender-for-cloud"></a>Intégration d’ordinateurs à Defender pour le cloud
Defender pour le cloud détecte automatiquement les abonnements et espaces de travail Azure non protégés par Azure Defender, à savoir les abonnements Azure utilisant Defender pour le cloud gratuitement et les espaces de travail dans lesquels la solution de sécurité n’est pas activée.

Utilisez les ressources suivantes pour vous aider lors des processus d’intégration.

- [Intégrer des ordinateurs autres qu’Azure](quickstart-onboard-machines.md)
- [Defender pour le cloud hybride : Vue d’ensemble](https://youtu.be/NMa4L_M597k)

## <a name="mitigating-security-issues-using-defender-for-cloud"></a>Atténuation des problèmes de sécurité à l’aide de Defender pour le cloud
Defender pour le cloud collecte, analyse et intègre automatiquement les données de journaux de vos ressources Azure, du réseau et des solutions partenaires connectées, telles que les solutions de protection des points de terminaison et de pare-feu, afin de détecter les menaces réelles et de réduire le nombre de faux positifs.

Utilisez les ressources suivantes pour vous aider à gérer les alertes de sécurité et à protéger vos ressources.

Articles    
- [Protection de votre réseau dans Defender pour le cloud](./protect-network-resources.md)
- [Protection du service et des données Azure SQL dans Defender pour le cloud](./implement-security-recommendations.md)


Vidéo    
- [Atténuation des problèmes de sécurité à l’aide de Defender pour le cloud](https://channel9.msdn.com/Blogs/Azure-Security-Videos/Mitigating-Security-Issues-using-Azure-Security-Center)

### <a name="defender-for-cloud-for-incident-response"></a>Defender pour le cloud pour la réponse aux incidents
Pour réduire les coûts et les dommages, il est important de disposer d’un plan de réponse aux incidents avant qu’une attaque ne survienne. Vous pouvez utiliser Defender pour le cloud à différentes étapes d’une réponse à un incident.

Utilisez les ressources suivantes pour comprendre comment Defender pour le cloud peut être intégré dans votre processus de réponse aux incidents.

Vidéos    
* [Defender pour le cloud dans la réponse aux incidents](https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Security-Center-in-Incident-Response)
* [Répondre rapidement aux menaces avec des opérations et des investigations de sécurité de nouvelle génération](https://youtu.be/e8iFCz5RM4g)

Articles    
* [Utilisation de Defender pour le cloud pour la réponse à un incident](./tutorial-security-incident.md)
* [Utiliser l’automatisation pour répondre aux déclencheurs Defender pour le cloud](workflow-automation.md)

## <a name="advanced-cloud-defense"></a>Défense de cloud avancée

Les machines virtuelles Azure peuvent profiter des fonctionnalités de défense de cloud avancées dans Defender pour le cloud. Ces fonctionnalités incluent l’accès juste-à-temps à la machine virtuelle ainsi que des contrôles d’application adaptatifs.

Utilisez les ressources suivantes pour savoir comment utiliser ces fonctionnalités dans Defender pour le cloud.

Vidéos    
* [Defender pour le cloud : Accès juste-à-temps aux machines virtuelles](https://youtu.be/UOQb2FcdQnU)
* [Defender pour le cloud : Contrôles d’application adaptatifs](https://youtu.be/wWWekI1Y9ck)

Articles    
* [Gérer l’accès juste-à-temps aux machines virtuelles](./just-in-time-access-usage.md)
* [Contrôles d’application adaptatifs dans Defender pour le cloud](./adaptive-application-controls.md)

## <a name="hands-on-activities"></a>Travaux pratiques

* [Laboratoire pratique Defender pour le cloud](https://www.microsoft.com/handsonlabs/SelfPacedLabs/?storyGuid=78871abf-6f35-4aa0-840f-d801f5cdbd72)
* [Playbook de recommandations du pare-feu d’applications web (WAF) dans Defender pour le cloud](https://gallery.technet.microsoft.com/ASC-Playbook-Protect-38bd47ff)
* [Playbook Defender pour le cloud : Alertes de sécurité](https://gallery.technet.microsoft.com/Azure-Security-Center-f621a046)

## <a name="additional-resources"></a>Ressources supplémentaires
* [Page de documentation Defender pour le cloud](./index.yml)
* [Page de documentation de l’API REST Defender pour le cloud](/previous-versions/azure/reference/mt704034(v=azure.100))
* [Forum aux questions sur Defender pour le cloud](./faq-general.yml)
* [Page des tarifs](https://azure.microsoft.com/pricing/details/security-center/)
* [Meilleures pratiques relatives à la sécurité d’identité](../security/fundamentals/identity-management-best-practices.md)
* [Meilleures pratiques en matière de sécurité réseau](../security/fundamentals/network-best-practices.md)
* [Recommandations de PaaS](../security/fundamentals/paas-deployments.md)
* [Conformité](https://www.microsoft.com/trustcenter/compliance/due-diligence-checklist)
* [Les clients Log Analytics peuvent désormais utiliser Defender pour le cloud pour protéger leurs charges de travail cloud hybrides](/archive/blogs/msoms/oms-customers-can-now-use-azure-security-center-to-protect-their-hybrid-cloud-workloads)
