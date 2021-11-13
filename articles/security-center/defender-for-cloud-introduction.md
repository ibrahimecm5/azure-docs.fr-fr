---
title: Microsoft Defender pour le cloud – Présentation
description: Utilisez Microsoft Defender pour le cloud afin de protéger vos charges de travail et ressources Azure hybrides et multiclouds.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: overview
ms.custom: mvc
ms.date: 11/04/2021
ms.author: memildin
ms.openlocfilehash: 2b6c756b0dd752d3e4de349b624f17342fad1c9d
ms.sourcegitcommit: 96deccc7988fca3218378a92b3ab685a5123fb73
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131576450"
---
# <a name="what-is-microsoft-defender-for-cloud"></a>Qu’est-ce que Microsoft Defender pour le cloud ?

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Defender pour le cloud est un outil destiné à la gestion de la sécurité et à la protection contre les menaces. Il renforce la sécurité de vos ressources cloud et, avec ses plans intégrés, il protège les charges de travail s’exécutant sur des plateformes Azure, hybrides et cloud.

Defender pour le cloud fournit les outils nécessaires pour renforcer vos ressources, suivre votre situation de sécurité, vous protéger contre les cyberattaques et rationaliser la gestion de la sécurité. Defender pour le cloud étant intégré en mode natif, son déploiement est facile. Vous disposez ainsi d’un approvisionnement automatique simple pour sécuriser vos ressources par défaut.

Defender pour le cloud répond à trois besoins essentiels lorsque vous gérez la sécurité de vos ressources et charges de travail dans le cloud et localement :

:::image type="content" source="media/defender-for-cloud-introduction/defender-for-cloud-synopsis.png" alt-text="Compréhension des fonctionnalités de base de Microsoft Defender pour le cloud.":::


|Exigence de sécurité  | Solution Defender pour le cloud|
|---------|---------|
|**Évaluation continue** : vous permet de comprendre votre situation de sécurité actuelle.   | **Niveau de sécurité** : score vous permettant de déterminer d’un coup d’œil votre situation de sécurité actuelle ; plus il est élevé, plus le niveau de risque identifié est faible.       |
|**Sécurisation** : renforce la sécurité de l’ensemble des ressources et services connectés. | **Recommandations de sécurité** : tâches de sécurisation renforcée personnalisées et hiérarchisées pour améliorer votre situation. Pour implémenter une recommandation, vous devez suivre les étapes de correction détaillées qu’elle fournit. Pour de nombreuses recommandations, Defender pour le cloud propose un bouton « Corriger » qui automatise l’implémentation.|
|**Défense** : détection et résolution de menaces pesant sur ces ressources et services. | **Alertes de sécurité** : avec les fonctionnalités de sécurité renforcée activées, Defender pour le cloud détecte les menaces pesant sur vos ressources et charges de travail. Ces alertes apparaissent dans le portail Azure, et Defender pour le cloud peut également les envoyer par e-mail au personnel concerné au sein de votre organisation. Des alertes peuvent également être diffusées vers des solutions SIEM (gestion des informations et événements de sécurité), SOAR (orchestration, automatisation et réponse aux incidents de sécurité) ou de gestion des services informatiques selon les besoins. |

## <a name="posture-management-and-workload-protection"></a>Gestion de la situation de sécurité et protection des charges de travail

Les fonctionnalités de Microsoft Defender pour le cloud couvrent les deux grands piliers de la sécurité cloud : la gestion de la posture de sécurité cloud et la protection des charges de travail cloud.

### <a name="cloud-security-posture-management-cspm"></a>Gestion de la posture de sécurité cloud (CSPM)

Dans Defender pour le cloud, les fonctionnalités de gestion de la situation de sécurité offrent les avantages suivants :

- **Visibilité** : vous aide à comprendre votre situation de sécurité actuelle.
- **Sécurisation renforcée** : vous aide à améliorer rapidement et efficacement votre sécurité.

Pour vous permettre d’atteindre ces objectifs, Defender pour le cloud offre une fonctionnalité centrale appelée **niveau de sécurité**. Defender pour le cloud évalue continuellement vos ressources, vos abonnements et votre organisation pour y rechercher d’éventuels problèmes de sécurité. Il agrège ensuite toutes ses découvertes sous la forme d’un score qui vous permet de déterminer d’un coup d’œil votre niveau de sécurité actuel : plus le score est élevé, plus le niveau de risque identifié est faible. 

Lorsque vous ouvrez Defender pour le cloud pour la première fois, il répond aux objectifs de visibilité et de sécurisation comme suit :

1. Il **affiche un niveau de sécurité** pour vos abonnements en fonction de l’évaluation de vos ressources connectées, par rapport aux recommandations du [Benchmark de sécurité Azure](/security/benchmark/azure/overview). Utilisez ce score pour comprendre votre situation de sécurité et le tableau de bord de conformité pour examiner votre conformité par rapport au benchmark intégré. Lorsque vous avez activé les fonctionnalités de sécurité renforcée, vous pouvez personnaliser les normes utilisées pour évaluer votre conformité, ainsi qu’ajouter d’autres réglementations (telles que NIST et Azure CIS) ou des exigences de sécurité spécifiques de votre organisation.

1. Il **fournit des recommandations en matière de sécurisation renforcée** en fonction des erreurs de configuration et des faiblesses de sécurité identifiées. Utilisez ces recommandations de sécurité pour renforcer la situation de sécurité des ressources Azure, hybrides et multiclouds de votre organisation.

[En savoir plus sur le degré de sécurisation](secure-score-security-controls.md).

### <a name="cloud-workload-protection-cwp"></a>Protection de charge de travail cloud (PCTC)

Defender pour le cloud offre des alertes de sécurité optimisées par les [renseignements sur les menaces Microsoft](https://go.microsoft.com/fwlink/?linkid=2128684). Il inclut également une série de protections avancées et intelligentes pour vos charges de travail. Les protections des charges de travail sont fournies au travers de plans Microsoft Defender spécifiques des types de ressources incluses dans vos abonnements. Par exemple, vous pouvez activer **Microsoft Defender pour le stockage** afin de recevoir des alertes sur les activités suspectes liées à vos comptes de stockage Azure. 


## <a name="azure-hybrid-and-multi-cloud-protections"></a>Protections Azure, hybrides et multiclouds

Étant donné que Defender pour le cloud est un service natif d’Azure, bon nombre de services Azure sont surveillés et protégés sans qu’un déploiement soit nécessaire.

Le cas échéant, Defender pour cloud peut déployer automatiquement un agent Log Analytics pour collecter des données liées à la sécurité. Pour les machines Azure, le déploiement est géré directement. Pour les environnements hybrides et multiclouds, les plans Microsoft Defender sont étendus aux machines non-Azure avec l’aide d’[Azure Arc](https://azure.microsoft.com/services/azure-arc/). Les fonctionnalités CSPM sont étendues aux machines multiclouds sans agent (voir [Défendre des ressources s’exécutant sur d’autres clouds](#defend-resources-running-on-other-clouds)).


### <a name="azure-native-protections"></a>Protections natives d’Azure

Defender pour le cloud vous aide à détecter les menaces pesant sur les ressources suivantes :

- **Services PaaS Azure** : détecte les menaces ciblant des services Azure, dont Azure App Service, Azure SQL, Stockage Microsoft Azure et d’autres services de données. Vous pouvez également détecter les anomalies dans vos journaux d’activité Azure en tirant parti de l’intégration native avec Microsoft Defender pour les applications cloud (anciennement Microsoft Cloud App Security).

- **Services de données Azure** : Defender pour le cloud inclut des fonctionnalités qui vous permettent de classer automatiquement vos données dans Azure SQL. Vous pouvez également obtenir des évaluations pour les vulnérabilités potentielles sur les services Stockage et Azure SQL, ainsi que des recommandations pour les atténuer.

- **Réseaux** : Defender pour le cloud vous aide à limiter votre exposition aux attaques par force brute. En réduisant l’accès aux ports de la machine virtuelle, à l’aide de l’accès à la machine virtuelle juste-à-temps, vous pouvez renforcer votre réseau en empêchant les accès inutiles. Vous pouvez définir des stratégies d’accès sécurisées sur les ports sélectionnés, juste pour les utilisateurs autorisés, les adresses IP ou les plages d’adresses IP sources autorisées et pour une durée limitée. 


### <a name="defend-your-hybrid-resources"></a>Défendre vos ressources hybrides
Outre la protection de votre environnement Azure, vous pouvez ajouter les fonctionnalités de Defender pour le cloud à votre environnement cloud hybride pour protéger vos serveurs non-Azure. Pour vous aider à vous concentrer sur l’essentiel, vous bénéficiez de renseignements personnalisés sur les menaces et d’alertes hiérarchisées en fonction de votre environnement.

Pour étendre la protection à des machines locales, déployez [Azure Arc](https://azure.microsoft.com/services/azure-arc/) et activez les fonctionnalités de sécurité renforcée de Defender pour le cloud. En savoir plus dans [Ajouter des machines non-Azure avec Azure Arc](quickstart-onboard-machines.md#add-non-azure-machines-with-azure-arc).

### <a name="defend-resources-running-on-other-clouds"></a>Protéger des ressources s’exécutant sur d’autres clouds 

Defender pour le cloud peut protéger des ressources dans d’autres clouds (tels que AWS et GCP). 

Par exemple, si vous avez [connecté un compte Amazon Web Services (AWS)](quickstart-onboard-aws.md) à un abonnement Azure, vous pouvez activer l’une de ces protections :

- Les **fonctionnalités CSPM de Defender pour le cloud** s’étendent à vos ressources AWS. Ce plan sans agent évalue vos ressources AWS conformément aux recommandations de sécurité propres à AWS, et celles-ci sont ajoutées à votre degré de sécurisation. Les ressources sont également évaluées pour voir si elles sont conformes aux normes intégrées propres à AWS (AWS CIS, AWS PCI DSS et bonnes pratiques de sécurité de base AWS). La [page d’inventaire des ressources](asset-inventory.md) de Defender pour le cloud est une fonctionnalité multicloud qui vous permet de gérer vos ressources AWS avec vos ressources Azure.
- **Microsoft Defender pour Kubernetes** étend la détection des menaces contre les conteneurs ainsi que des défenses avancées à vos **clusters Amazon EKS Linux**.
- **Microsoft Defender pour les serveurs** ajoute la détection des menaces et des défenses avancées à vos instances EC2 Linux et Windows. Ce plan comprend la licence intégrée de Microsoft Defender pour point de terminaison, des bases de référence de sécurité et des évaluations de niveau système d’exploitation, l’analyse de l’évaluation des vulnérabilités, des contrôles d’application adaptatifs (AAC), le monitoring de l’intégrité des fichiers (FIM), etc.

En savoir plus sur la connexion de vos comptes [AWS](quickstart-onboard-aws.md) et [GCP](quickstart-onboard-gcp.md) à Microsoft Defender pour le cloud.

## <a name="vulnerability-assessment-and-management"></a>Gestion et évaluation des vulnérabilités

:::image type="content" source="media/defender-for-cloud-introduction/defender-for-cloud-expanded-assess.png" alt-text="Concentrez-vous sur les fonctionnalités d’évaluation de Microsoft Defender pour le cloud.":::

Defender pour le cloud comprend des solutions d’évaluation des vulnérabilités pour vos machines virtuelles, vos registres de conteneurs et vos serveurs SQL dans le cadre des fonctionnalités de sécurité renforcée. Certains des scanneurs sont optimisés par Qualys. Vous n’avez cependant pas besoin d’une licence Qualys, ou même d’un compte Qualys : tout est géré sans heurt dans Defender pour le cloud.

Azure Defender pour les serveurs inclut une intégration automatique native avec Microsoft Defender pour point de terminaison. Pour en savoir plus, consultez [Protéger vos points de terminaison avec la solution EDR intégrée de Defender pour le cloud : Microsoft Defender pour point de terminaison](integration-defender-for-endpoint.md). Avec cette intégration activée, vous avez accès aux résultats des recherches de vulnérabilités de la fonctionnalité de **gestion des menaces et des vulnérabilités Microsoft**. Pour plus d’informations, consultez [Examen des faiblesses de la gestion des menaces et des vulnérabilités de Microsoft Defender pour point de terminaison](deploy-vulnerability-assessment-tvm.md).

Examinez les résultats de ces scanners de vulnérabilité et répondez-y au sein de Defender pour le cloud. En vertu de cette approche globale, Defender pour le cloud est en passe de devenir le cœur tous vos efforts en matière de sécurité du cloud.

Pour plus d’informations, consultez les pages suivantes :

- [Scanneur Qualys intégré de Defender pour le cloud pour les machines Azure et hybrides](deploy-vulnerability-assessment-vm.md)
- [Identification des vulnérabilités dans les images des registres de conteneurs Azure Container Registry](defender-for-container-registries-usage.md#identify-vulnerabilities-in-images-in-other-container-registries)


## <a name="optimize-and-improve-security-by-configuring-recommended-controls"></a>Optimiser et améliorer la sécurité en configurant les contrôles recommandés

:::image type="content" source="media/defender-for-cloud-introduction/defender-for-cloud-expanded-secure.png" alt-text="Concentrez-vous sur les fonctionnalités de sécurisation de Microsoft Defender pour le cloud.":::

Garantir la sécurité de vos charges de travail est une règle de base en matière de sécurité, et cela commence par la mise en place de stratégies de sécurité personnalisées. Étant donné que les stratégies dans Defender pour le cloud reposent sur des contrôles Azure Policy, vous obtenez l’éventail complet et la flexibilité totale offerts par une **solution de stratégie de niveau international**. Dans Defender pour le cloud, vous pouvez définir vos stratégies de façon à ce qu’elles s’exécutent sur des groupes d’administration, dans l’ensemble des abonnements et même pour un locataire entier.

Defender pour le cloud découvre continuellement de nouvelles ressources déployées au sein de vos charges de travail, et évalue si celles-ci sont configurées conformément aux meilleures pratiques en matière de sécurité. Si ce n’est pas le cas, elles sont marquées et vous recevez une liste hiérarchisée de recommandations concernant ce que vous devez corriger. Les recommandations vous aident à réduire la surface d’attaque de chacune de vos ressources.

La liste des recommandations est activée et prise en charge par le Benchmark de sécurité Azure. Ce benchmark spécifique d’Azure créé par Microsoft fournit un ensemble de directives relatives aux meilleures pratiques de sécurité et de conformité basées sur les cadres de conformité courants. Apprenez-en davantage dans [Présentation du Benchmark de sécurité Azure](/security/benchmark/azure/introduction).

Ainsi, Defender pour le cloud vous permet non seulement de définir des stratégies de sécurité, mais aussi d’*appliquer des normes de configuration sécurisées à vos ressources*.

:::image type="content" source="./media/defender-for-cloud-introduction/recommendation-example.png" alt-text="Exemple de recommandation de Defender pour le cloud.":::

Pour vous aider à comprendre l’importance de chaque recommandation pour votre situation de sécurité globale, Defender pour le cloud regroupe les recommandations dans des contrôles de sécurité, et ajoute une valeur de **niveau de sécurité** à chaque contrôle. Cela est essentiel pour vous permettre de **prioriser votre travail de sécurité**.

:::image type="content" source="./media/defender-for-cloud-introduction/sc-secure-score.png" alt-text="Niveau de sécurité de Defender pour le cloud.":::

## <a name="defend-against-threats"></a>Protéger contre les menaces

:::image type="content" source="media/defender-for-cloud-introduction/defender-for-cloud-expanded-defend.png" alt-text="Concentrez-vous sur les fonctionnalités de protection de Microsoft Defender pour le cloud.":::

Defender pour le cloud offre ce qui suit :

- **Alertes de sécurité** : quand Defender pour le cloud détecte une menace une zone quelconque de votre environnement, il génère une alerte de sécurité. Ces alertes décrivent les détails des ressources affectées, les étapes de correction suggérées et, dans certains cas, l’option permettant de déclencher une application logique en réponse. Si Defender pour le cloud génère une alerte ou en reçoit une d’un produit de sécurité intégré, vous pouvez l’exporter. Pour exporter vos alertes vers Microsoft Sentinel, un système SIEM tiers ou tout autre outil externe, suivez les instructions fournies dans [Diffuser des alertes vers un système SIEM, SOAR ou une solution de gestion des services informatiques](export-to-siem.md). La protection contre les menaces de Defender pour le cloud inclut l’analyse de la chaîne de destruction de fusion, qui corrèle automatiquement les alertes dans votre environnement en fonction de l’analyse de la chaîne de destruction cyber, pour vous aider à mieux comprendre l’historique d’une campagne d’attaque, de son point de départ jusqu’au type d’impact sur vos ressources. [Les intentions de la chaîne de destruction prises en charge de Defender pour le cloud sont basées sur la version 7 de la matrice MITRE ATT&CK](alerts-reference.md#intentions).

- Les **fonctionnalités avancées de protection contre les menaces** pour les machines virtuelles, les bases de données SQL, les conteneurs, les applications web, votre réseau et autres protections incluent la sécurisation des ports de gestion de vos machines virtuelles avec un [accès juste-à-temps](just-in-time-access-overview.md) et des [contrôles d’application adaptatifs](adaptive-application-controls.md) pour créer des listes vertes d’applications devant fonctionner sur vos machines.

La page **Plans Defender** de Microsoft Defender pour le cloud propose les plans suivants pour des défenses complètes des couches de calcul, de données et de service de votre environnement :

- [Microsoft Defender pour les serveurs](defender-for-servers-introduction.md)
- [Microsoft Defender pour App Service](defender-for-app-service-introduction.md)
- [Microsoft Defender pour Stockage](defender-for-storage-introduction.md)
- [Microsoft Defender pour SQL](defender-for-sql-introduction.md)
- [Microsoft Defender pour Kubernetes](defender-for-kubernetes-introduction.md)
- [Microsoft Defender pour les registres de conteneurs](defender-for-container-registries-introduction.md)
- [Microsoft Defender pour Key Vault](defender-for-key-vault-introduction.md)
- [Microsoft Defender pour Resource Manager](defender-for-resource-manager-introduction.md)
- [Microsoft Defender pour DNS](defender-for-dns-introduction.md)
- [Microsoft Defender pour les bases de données relationnelles open source](defender-for-databases-introduction.md)

Utilisez les vignettes de protection avancée du [tableau de bord des protections des charges de travail](workload-protections-dashboard.md) pour superviser et configurer chacune de ces protections. 

> [!TIP]
> Microsoft Defender pour IoT (préversion) est un produit distinct. Tous les détails figurent dans [Présentation d’Azure Defender pour IoT (préversion)](../defender-for-iot/overview.md). 


## <a name="next-steps"></a>Étapes suivantes

- Pour commencer à utiliser Defender pour le cloud, vous devez disposer d’un abonnement à Microsoft Azure. Si vous n’avez pas d’abonnement, [inscrivez-vous à un essai gratuit](https://azure.microsoft.com/free/).

- Le plan gratuit de Defender pour le cloud est activé sur tous vos abonnements Azure actuels quand vous consultez les pages de Defender pour le cloud dans le portail Azure pour la première fois, ou s’il est activé programmatiquement avec l’API REST. Pour tirer parti des fonctionnalités avancées de gestion de la sécurité et de détection des menaces, vous devez activer les fonctionnalités de sécurité avancées. Ces fonctionnalités sont gratuites pendant les 30 premiers jours. [Apprenez-en davantage sur la tarification](https://azure.microsoft.com/pricing/details/security-center/).

- Si vous êtes prêt à activer les fonctionnalités de sécurité avancée, suivez la procédure décrite dans [Activer les fonctionnalités de sécurité avancées](enable-enhanced-security.md).

> [!div class="nextstepaction"]
> [Activer des plans Microsoft Defender](enable-enhanced-security.md)
