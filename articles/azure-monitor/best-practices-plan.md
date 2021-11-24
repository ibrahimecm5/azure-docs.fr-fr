---
title: 'Meilleures pratiques d’Azure Monitor : Planification'
description: Aide et recommandations relatives à la planification et à la conception avant le déploiement d’Azure Monitor.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/18/2021
ms.openlocfilehash: b04b73cfd7cd2482e14485556df5a477e47a9334
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132347971"
---
# <a name="azure-monitor-best-practices---planning-your-monitoring-strategy-and-configuration"></a>Meilleures pratiques d’Azure Monitor : Planification de la stratégie et de la configuration de la surveillance
Cet article fait partie du scénario [Recommandations pour la configuration d’Azure Monitor](best-practices.md). Il décrit la planification que vous devez prendre en compte avant de commencer votre implémentation. Cela permet de garantir que les options de configuration que vous choisissez répondent aux exigences particulières de votre entreprise.

Si vous n’êtes pas encore familiarisé avec les concepts de surveillance, commencez par consulter le [guide de supervision du cloud](/azure/cloud-adoption-framework/manage/monitor) qui fait partie de l’infrastructure [Microsoft Cloud Adoption Framework pour Azure](/cloud-adoption-framework/). Ce guide définit les concepts de haut niveau de la surveillance et fournit une aide pour définir les conditions requises pour votre environnement d’analyse et les processus de prise en charge. Cet article fait référence à des sections de ce guide qui concernent des étapes particulières de la planification.
## <a name="understand-azure-monitor-costs"></a>Comprendre les coûts d’Azure Monitor
Un objectif essentiel de votre stratégie de surveillance est de réduire les coûts. Certaines collectes de données et fonctionnalités d’Azure Monitor sont gratuites, tandis que d’autres ont un coût qui dépend de leur configuration particulière, de la quantité de données collectées ou de leur fréquence d’exécution. Les articles de ce scénario identifient les recommandations qui ont un coût, mais vous devez vous familiariser avec la tarification d’Azure Monitor lorsque vous concevez votre implémentation afin d’optimiser les coûts. Pour plus d’informations et pour obtenir une aide concernant la tarification d’Azure Monitor, consultez les articles suivants :

- [Tarification Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/)
- [Surveiller l’utilisation et l’estimation des coûts dans Azure Monitor](usage-estimated-costs.md)
- [Gérer l’utilisation et les coûts avec les journaux Azure Monitor](logs/manage-cost-storage.md)
- [Gérer l’utilisation et les coûts pour Application Insights](app/pricing.md)

## <a name="define-strategy"></a>Définition de la stratégie
Avant de concevoir et d’implémenter une solution de surveillance, vous devez établir une stratégie de surveillance afin de comprendre les objectifs et les exigences de votre plan. La stratégie définit vos exigences particulières, la configuration qui répond le mieux à ces exigences et les processus permettant de tirer parti de l’environnement d’analyse pour maximiser les performances et la fiabilité de vos applications. Les options de configuration que vous choisissez pour Azure Monitor doivent être cohérentes avec votre stratégie.

Consultez [Guide de supervision du cloud : Élaborer une stratégie de supervision](/azure/cloud-adoption-framework/strategy/monitoring-strategy) pour connaître un certain nombre de facteurs que vous devez prendre en compte lors de l’élaboration d’une stratégie de surveillance. Vous devez également vous reporter à [Stratégie de supervision pour les modèles de déploiement cloud](/azure/cloud-adoption-framework/manage/monitor/cloud-models-monitor-overview) qui vous aidera à comparer la surveillance entièrement basée sur le cloud à un modèle hybride. 

## <a name="gather-required-information"></a>Collecte des informations nécessaire
Avant de déterminer les détails de votre implémentation, vous devez rassembler les informations nécessaires pour définir ces détails. Les sections suivantes décrivent les informations généralement requises pour une implémentation complète d’Azure Monitor.

 ### <a name="what-needs-to-be-monitored"></a>Quels éléments sont à surveiller ?
 Vous ne configurerez pas nécessairement une surveillance complète de toutes vos ressources cloud, mais vous concentrerez plutôt sur vos applications critiques et les composants dont elles dépendent. Cela permettra non seulement de réduire vos coûts de surveillance, mais aussi de réduire la complexité de votre environnement d’analyse. Consultez [Guide de supervision du cloud : Collecter les données appropriées](/azure/cloud-adoption-framework/manage/monitor/data-collection) pour obtenir de l’aide sur la définition des données dont vous avez besoin.

### <a name="who-needs-to-have-access-and-be-notified"></a>Qui doit avoir accès et être informé
Lorsque vous configurez votre environnement d’analyse, vous devez déterminer quels utilisateurs doivent avoir accès aux données de surveillance et quels utilisateurs doivent être avertis lorsqu’un problème est détecté. Il peut s’agir de propriétaires d’applications et de ressources, ou vous pouvez avoir une équipe de surveillance centralisée. Ces informations déterminent la façon dont vous configurez les autorisations d’accès aux données et les notifications d’alertes. Vous pouvez également avoir besoin de classeurs personnalisés pour présenter des ensembles particuliers d’informations à différents utilisateurs.

### <a name="service-level-agreements"></a>Contrats de niveau de service 
Votre organisation peut avoir des Contrats de niveau de service (SLA) qui définissent vos engagements en matière de performances et de durée de bon fonctionnement de vos applications. Ces contrats SLA peuvent déterminer la manière dont vous devez configurer les fonctionnalités d’Azure Monitor sensibles au facteur temps, telles que les alertes. Vous devez également comprendre la [latence des données dans Azure Monitor](logs/data-ingestion-time.md) dans la mesure où cela affectera la réactivité des scénarios de surveillance et votre capacité à respecter les contrats SLA.

## <a name="identify-monitoring-services-and-products"></a>Identifier les services et les produits de surveillance
Azure Monitor est conçu pour gérer la surveillance de l’intégrité et de l’état. Une solution de surveillance complète implique généralement plusieurs services Azure et potentiellement d’autres produits. D’autres objectifs de surveillance, qui peuvent nécessiter des solutions supplémentaires, sont décrits dans le guide de supervision du cloud dans [Objectifs de surveillance principaux](/azure/cloud-adoption-framework/strategy/monitoring-strategy#formulate-monitoring-requirements). 

Les sections suivantes décrivent d’autres services et produits que vous pouvez utiliser conjointement avec Azure Monitor. Ce scénario n’inclut actuellement aucune aide relative à l’implémentation de ces solutions. Vous devez donc vous référer à leur documentation.

### <a name="security-monitoring"></a>Surveillance de la sécurité
Tandis que les données opérationnelles stockées dans Azure Monitor peuvent servir à l'examen des incidents de sécurité, d'autres services Azure permettent de surveiller la sécurité. La surveillance de la sécurité dans Azure est assurée par Microsoft Defender pour le cloud et Microsoft Sentinel.

- [Microsoft Defender pour le cloud](../security-center/security-center-introduction.md) collecte des informations sur les ressources Azure et les serveurs hybrides. Bien que Defender pour le cloud puisse collecter des événements de sécurité, il se concentre sur la collecte des données d’inventaire, des résultats des analyses d’évaluation et des audits des stratégies pour mettre en évidence les vulnérabilités et recommander des actions correctives. Parmi les fonctionnalités notables figurent une carte interactive du réseau, l'accès juste-à-temps aux machines virtuelles, le renforcement adaptatif du réseau et les contrôles d'application adaptatifs pour bloquer les exécutables suspects.

- [Microsoft Defender pour les serveurs](../security-center/azure-defender.md) est la solution d’évaluation des serveurs fournie par Defender pour le cloud. Defender pour les serveurs peut envoyer des événements de sécurité Windows à Log Analytics. Defender pour le cloud ne s’appuie pas sur les événements de sécurité Windows pour les alertes ou les analyses. Cette fonctionnalité permet un archivage centralisé des événements à des fins d'examen ou autres.

- [Microsoft Sentinel](../sentinel/overview.md) est une solution de type SIEM (Security Information and Event Management) et SOAR (Security Orchestrated Automated Response). Sentinel collecte des données de sécurité à partir d'un large éventail de sources Microsoft et tierces pour fournir des alertes, une visualisation et une automatisation. Cette solution vise à consolider le plus grand nombre possible de journaux de sécurité, y compris les événements de sécurité Windows. Microsoft Sentinel peut également collecter des journaux d’événements de sécurité Windows et partage généralement un espace de travail Log Analytics avec Defender pour le cloud. Les événements de sécurité peuvent uniquement être collectés à partir de Microsoft Sentinel ou Defender pour le cloud lorsqu’ils partagent le même espace de travail. Contrairement à Defender pour le cloud, les événements de sécurité sont un composant clé des alertes et des analyses dans Microsoft Sentinel.

- [Defender pour point de terminaison](/microsoft-365/security/defender-endpoint/microsoft-defender-endpoint) est une plateforme dédiée à la sécurité des points de terminaison d'entreprise conçue pour aider les réseaux d'entreprise à prévenir, détecter, examiner et traiter les menaces avancées. Il a été conçu en mettant l'accent sur la protection des appareils des utilisateurs de Windows. Defender pour point de terminaison surveille les stations de travail, les serveurs, les tablettes et les téléphones portables, tous systèmes d'exploitation confondus, pour détecter les problèmes de sécurité et les vulnérabilités. Defender pour point de terminaison s'appuie sur Microsoft Endpoint Manager pour collecter des données et fournir des évaluations de sécurité. La collecte des données repose principalement sur les journaux de suivi ETW, et celles-ci sont stockées dans un espace de travail isolé.


### <a name="system-center-operations-manager"></a>System Center Operations Manager
Vous avez peut-être déjà investi dans Operations Manager pour surveiller les ressources locales et les charges de travail exécutées sur vos machines virtuelles. Vous pouvez choisir de [migrer cette surveillance vers Azure Monitor](azure-monitor-operations-manager.md) ou de continuer à utiliser les deux produits ensemble dans une configuration hybride. Consultez [Guide de supervision du cloud : Vue d’ensemble des plateformes de supervision](/azure/cloud-adoption-framework/manage/monitor/platform-overview) pour une comparaison des deux produits. Pour accéder à l’aide relative à l’utilisation des deux dans une configuration hybride et à la détermination du modèle le plus approprié pour votre environnement, consultez [Stratégie de supervision pour les modèles de déploiement cloud](/azure/cloud-adoption-framework/manage/monitor/cloud-models-monitor-overview).



## <a name="next-steps"></a>Étapes suivantes

- Consultez [Configurer la collecte de données](best-practices-data-collection.md) pour connaître les étapes et les recommandations permettant de configurer la collecte de données dans Azure Monitor.
