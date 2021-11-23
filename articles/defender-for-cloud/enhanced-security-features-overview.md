---
title: Comprendre les fonctionnalités de sécurité renforcée de Microsoft Defender pour le cloud
description: En savoir plus sur les avantages de l’activation de la sécurité renforcée dans Microsoft Defender pour le cloud
author: memildin
ms.author: memildin
manager: rkarlin
ms.service: defender-for-cloud
ms.topic: overview
ms.date: 11/14/2021
ms.openlocfilehash: 4f17d5228bdac13797aaef0fe4441f61d474042a
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132557333"
---
# <a name="microsoft-defender-for-clouds-enhanced-security-features"></a>Fonctionnalités de sécurité renforcée de Microsoft Defender pour le cloud

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Les fonctionnalités de sécurité renforcée sont gratuites pendant les 30 premiers jours. Une fois les 30  jours écoulés, si vous décidez de continuer à utiliser le service, votre utilisation est automatiquement facturée.

Vous pouvez procéder à une mise à niveau à partir de la page **Paramètres d’environnement**, comme décrit dans [Démarrage rapide :Activer les fonctionnalités de sécurité avancée](enable-enhanced-security.md). Pour plus d’informations sur la tarification dans la devise de votre choix et en fonction de votre région, consultez la [page sur les tarifs](https://azure.microsoft.com/pricing/details/security-center/).

:::image type="content" source="media/enhanced-security-features-overview/defender-plans-top.png" alt-text="Activation des fonctionnalités de sécurité renforcée de Microsoft Defender pour le cloud.":::

## <a name="what-are-the-benefits-of-enabling-enhanced-security-features"></a>Quels sont les avantages de l’activation des fonctionnalités de sécurité renforcée ?

Defender pour le cloud est proposé en deux modes :

- **Sans fonctionnalités de sécurité renforcée** (gratuit) : Defender pour le cloud est activé gratuitement sur tous vos abonnements Azure quand vous visitez le tableau de bord de protection de charge de travail dans le portail Azure pour la première fois, ou s’il est activé par programmation via l’API. Ce mode gratuit vous donne accès au niveau de sécurité et aux fonctionnalités associées : stratégie de sécurité, évaluation continue de la sécurité et à des recommandations de sécurité actionnables pour vous aider à protéger vos ressources Azure.

- **Defender pour le cloud avec toutes les fonctionnalités de sécurité renforcée** : l’activation de la sécurité renforcée étend les fonctionnalités du mode gratuit aux charges de travail exécutées dans des clouds privés et publics, pour une gestion unifiée de la sécurité et une protection contre les menaces sur l’ensemble des charges de travail cloud hybrides. Voici quelques-uns des principaux avantages :

    - **Microsoft Defender pour point de terminaison** : Microsoft Defender pour pour les serveurs comprend [Microsoft Defender pour point de terminaison](https://www.microsoft.com/microsoft-365/security/endpoint-defender) afin d’offrir une protection évolutive des points de terminaison (PEPT) totale. Apprenez-en davantage sur les avantages de l’utilisation de Microsoft Defender pour point de terminaison avec Azure Defender pour le cloud dans [Utiliser la solution PEPT intégrée de Defender pour le cloud](integration-defender-for-endpoint.md).
    - **Évaluation des vulnérabilités pour les machines virtuelles, les registres de conteneur et les ressources de SQL** : activez facilement les solutions d’évaluation des vulnérabilités pour découvrir, gérer et résoudre les vulnérabilités. Affichez, examinez et corrigez les résultats directement depuis Defender pour le cloud.
    - **sécurité multicloud** : connectez vos comptes à partir d’Amazon Web Services (AWS) et de Google Cloud Platform (GCP) pour protéger les ressources et les charges de travail sur ces plateformes grâce à une série de fonctionnalités de sécurité de Microsoft Defender pour le cloud.
    - **Sécurité hybride** : obtenez une vue unifiée de la sécurité sur l’ensemble de vos charges de travail cloud et locales. Appliquez des stratégies de sécurité et évaluez en continu la sécurité de vos charges de travail cloud hybrides pour garantir la conformité aux normes de sécurité. Collectez, recherchez et analysez des données de sécurité à partir d’un large éventail de sources, dont les pare-feu et autres solutions partenaires.
    - **Alertes de protection contre les menaces** : les analyses comportementales avancées et Microsoft Intelligent Security Graph offrent une longueur d’avance face à l’évolution des attaques informatiques. Les analytiques comportementales intégrées et le machine learning peuvent identifier les attaques et les vulnérabilités zero-day. Supervisez les réseaux, les machines, les magasins de données (serveurs SQL hébergés dans et en dehors d’Azure, bases de données Azure SQL, Azure SQL Managed Instance et Stockage Azure) et les services cloud pour les attaques entrantes et l’activité consécutive à une violation. Simplifiez l’investigation avec des outils interactifs et des informations sur les menaces contextuelles.
    - **Veiller au respect de la conformité au moyen d’une série de normes** : Defender pour le cloud évalue continuellement votre environnement cloud hybride afin d’analyser les facteurs de risque en fonction des contrôles et des bonnes pratiques du [Benchmark de sécurité Azure](/security/benchmark/azure/introduction). Quand vous activez les fonctionnalités de sécurité renforcée, vous pouvez appliquer d’autres normes industrielles, normes réglementaires et points de référence en fonction des besoins de votre organisation. Le [tableau de bord de conformité réglementaire](update-regulatory-compliance-packages.md) vous permet d’ajouter des normes et de veiller au respect de la conformité.
    - **Contrôles d’accès et d’application** : bloquez les programmes malveillants et les autres applications indésirables en appliquant des suggestions de mise en liste d’autorisation et de refus adaptées à vos charges de travail spécifiques et alimentées par Machine Learning. Réduisez la surface d’attaque réseau grâce à un accès contrôlé et juste à temps aux ports de gestion sur les machines virtuelles Azure. Les contrôles d’accès et d’application réduisent considérablement l’exposition à la force brute et à d’autres attaques réseau.
    - **Fonctionnalités de sécurité des conteneurs** : tirez parti de la gestion des vulnérabilités et de la protection contre les menaces en temps réel sur vos environnements en conteneur. Les frais sont basés sur le nombre d’images conteneur uniques transmises à votre registre connecté. Une fois qu’une image a été analysée une fois, elle ne sera plus facturée à moins qu’elle ne soit modifiée et transmise une fois de plus.
    - **Protection étendue contre les menaces pour les ressources connectées à Azure** : protection contre les menaces native cloud pour les services Azure communs à toutes vos ressources : Azure Resource Manager, Azure DNS, la couche réseau Azure et Azure Key Vault. Offrant une visibilité unique sur la couche de gestion Azure et la couche Azure DNS, Defender pour le cloud peut protéger les ressources cloud connectées à ces couches.


## <a name="faq---pricing-and-billing"></a>FAQ – Prix et facturation 

- [Comment suivre les personnes de mon organisation qui ont activé une offre Microsoft Defender dans Defender pour le cloud ?](#how-can-i-track-who-in-my-organization-enabled-a-microsoft-defender-plan-in-defender-for-cloud)
- [Quels sont les offres proposées par Defender pour le cloud ?](#what-are-the-plans-offered-by-defender-for-cloud)
- [Comment activer la sécurité renforcée de Defender pour le cloud pour mon abonnement ?](#how-do-i-enable-defender-for-clouds-enhanced-security-for-my-subscription)
- [Puis-je activer Microsoft Defender pour des serveurs sur un sous-ensemble de serveurs dans mon abonnement ?](#can-i-enable-microsoft-defender-for-servers-on-a-subset-of-servers-in-my-subscription)
- [Si j’ai déjà une licence Microsoft Defender pour point de terminaison, puis-je bénéficier d’une remise pour Defender pour les serveurs ?](#if-i-already-have-a-license-for-microsoft-defender-for-endpoint-can-i-get-a-discount-for-defender-for-servers)
- [Microsoft Defender pour les serveurs étant activé pour mon abonnement, dois-je payer pour les serveurs inactifs ?](#my-subscription-has-microsoft-defender-for-servers-enabled-do-i-pay-for-not-running-servers)
- [Les machines sur lesquelles l’agent Log Analytics n’est pas installé seront-elles facturées ?](#will-i-be-charged-for-machines-without-the-log-analytics-agent-installed)
- [Si un agent Log Analytics émet un rapport dans plusieurs espaces de travail, est-il facturé deux fois ?](#if-a-log-analytics-agent-reports-to-multiple-workspaces-will-i-be-charged-twice)
- [Si un agent Log Analytics émet un rapport dans plusieurs espaces de travail, l’ingestion de données gratuite de 500 Mo est-elle disponible sur chacun d’eux ?](#if-a-log-analytics-agent-reports-to-multiple-workspaces-is-the-500-mb-free-data-ingestion-available-on-all-of-them)
- [L’ingestion de données gratuite de 500 Mo est-elle calculée pour un espace de travail entier ou strictement machine par machine ?](#is-the-500-mb-free-data-ingestion-calculated-for-an-entire-workspace-or-strictly-per-machine)
- [Quels sont les types de données inclus dans l’allocation quotidienne de données de 500 Mo ?](#what-data-types-are-included-in-the-500-mb-data-daily-allowance)


### <a name="how-can-i-track-who-in-my-organization-enabled-a-microsoft-defender-plan-in-defender-for-cloud"></a>Comment suivre les personnes de mon organisation qui ont activé une offre Microsoft Defender dans Defender pour le cloud ?
Des abonnements Azure peuvent avoir plusieurs administrateurs autorisés à modifier les paramètres de tarification. Pour savoir quel utilisateur a effectué une modification, utilisez le journal d’activité Azure.

:::image type="content" source="media/enhanced-security-features-overview/logged-change-to-pricing.png" alt-text="Journal d’activité Azure montrant un événement de modification de tarif.":::

Si les informations de l’utilisateur ne sont pas listées dans la colonne **Événement lancé par**, explorez les données JSON de l’événement pour obtenir les détails correspondants.

:::image type="content" source="media/enhanced-security-features-overview/tracking-pricing-changes-in-activity-log.png" alt-text="Explorateur JSON du journal d’activité Azure.":::


### <a name="what-are-the-plans-offered-by-defender-for-cloud"></a>Quels sont les offres proposées par Defender pour le cloud ? 
L’offre gratuite de Microsoft Defender pour le Cloud inclut le niveau de sécurité et les outils associés. L’activation de la sécurité renforcée active toutes les offres Microsoft Defender pour offrir une série d’avantages en matière de sécurité pour toutes vos ressources dans les environnements Azure, hybrides et multiclouds.  

### <a name="how-do-i-enable-defender-for-clouds-enhanced-security-for-my-subscription"></a>Comment activer la sécurité renforcée de Defender pour le cloud pour mon abonnement ? 
Pour activer la sécurité renforcée pour votre abonnement, vous pouvez utiliser les méthodes suivantes  : 

| Méthode                                          | Instructions                                                                                                                                       |
|-------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------|
| Defender pour pages cloud du portail Azure    | [Activer les protections renforcées](enable-enhanced-security.md)                                                                                         |
| API REST                                        | [API pricings](/rest/api/securitycenter/pricings)                                                                                                  |
| Azure CLI                                       | [az security pricing](/cli/azure/security/pricing)                                                                                                 |
| PowerShell                                      | [Set-AzSecurityPricing](/powershell/module/az.security/set-azsecuritypricing)                                                                      |
| Azure Policy                                    | [Bundle Pricings](https://github.com/Azure/Azure-Security-Center/blob/master/Pricing%20%26%20Settings/ARM%20Templates/Set-ASC-Bundle-Pricing.json) |
|                                                 |                                                                                                                                                    |

### <a name="can-i-enable-microsoft-defender-for-servers-on-a-subset-of-servers-in-my-subscription"></a>Puis-je activer Microsoft Defender pour des serveurs sur un sous-ensemble de serveurs dans mon abonnement ?
Non. Lorsque vous activez [Microsoft Defender pour les serveurs](defender-for-servers-introduction.md) sur un abonnement, toutes les machines de l’abonnement sont protégés par Defender pour les serveurs.

Une alternative consiste à activer Microsoft Defender pour les serveurs au niveau de l’espace de travail Log Analytics. Dans ce cas, seuls les serveurs qui rapportent à cet espace de travail seront protégés et facturés. Toutefois, plusieurs fonctionnalités ne seront pas disponibles. Cela inclut l’accès juste-à-temps aux machines virtuelles, les détections réseau, la conformité réglementaire, le renforcement réseau adaptatif, le contrôle d’application adaptatif, etc. 

### <a name="if-i-already-have-a-license-for-microsoft-defender-for-endpoint-can-i-get-a-discount-for-defender-for-servers"></a>Si j’ai déjà une licence Microsoft Defender pour point de terminaison, puis-je bénéficier d’une remise pour Defender pour les serveurs ?
Si vous disposez déjà d’une licence pour Microsoft Defender pour point de terminaison, vous n’aurez pas à payer pour cette partie de votre licence Defender pour les serveurs.

Pour demander votre remise, contactez l’équipe de support de Defender pour le cloud et fournissez l’ID d’espace de travail, la région et le nombre de licences Microsoft Defender pour point de terminaison qui sont appliquées sur les ordinateurs de l’espace de travail donné.

La remise est effective à compter de la date d’approbation et ne sera pas rétroactive.

### <a name="my-subscription-has-microsoft-defender-for-servers-enabled-do-i-pay-for-not-running-servers"></a>Microsoft Defender pour les serveurs étant activé pour mon abonnement, dois-je payer pour les serveurs inactifs ? 
Non. Quand vous activez [Microsoft Defender pour les serveurs](defender-for-servers-introduction.md) sur un abonnement, vous n’êtes pas facturé pour les machines qui sont dans l’état d’alimentation désalloué. Les machines sont facturées en fonction de leur état d’alimentation comme indiqué dans le tableau suivant :

| State        | Description                                                                                                                                      | Facturation de l’utilisation de l’instance |
|--------------|--------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------|
| Démarrage en cours     | La machine virtuelle démarre.                                                                                                                               | Pas de facturation            |
| Exécution en cours      | État de fonctionnement normal d’une machine virtuelle                                                                                                                    | Facturation                |
| En cours d’arrêt     | Il s’agit d’un état transitoire. À l’issue de l’opération, l’état indiqué est Arrêté.                                                                           | Facturation                |
| Arrêté      | La machine virtuelle a été arrêtée à partir du système d’exploitation invité ou à l’aide des API PowerOff. Le matériel est toujours alloué à la machine virtuelle et demeure sur l’hôte. | Facturation                |
| Libération | État transitoire. À l’issue de l’opération, la machine virtuelle indique l’état Désalloué.                                                                             | Pas de facturation            |
| Libéré  | La machine virtuelle a été arrêtée et supprimée de l’hôte.                                                                                  | Pas de facturation            |

:::image type="content" source="media/enhanced-security-features-overview/deallocated-virtual-machines.png" alt-text="Machines virtuelles Azure montrant une machine désallouée.":::

### <a name="will-i-be-charged-for-machines-without-the-log-analytics-agent-installed"></a>Les machines sur lesquelles l’agent Log Analytics n’est pas installé seront-elles facturées ?
Oui. Lorsque vous activez [Microsoft Defender pour les serveurs](defender-for-servers-introduction.md) sur un abonnement, les machines de cet abonnement bénéficient d’une série de protections, même si vous n’avez pas installé l’agent Log Analytics. Cela s’applique aux machines virtuelles Azure, aux instances de groupe de machines virtuelles identiques Azure et aux serveurs Azure Arc.

### <a name="if-a-log-analytics-agent-reports-to-multiple-workspaces-will-i-be-charged-twice"></a>Si un agent Log Analytics émet un rapport dans plusieurs espaces de travail, est-il facturé deux fois ? 
Oui. Si vous avez configuré votre agent Log Analytics pour envoyer des données à deux espaces de travail de Log Analytics différents ou plus (multihébergement), chaque espace de travail sur lequel est installée une solution « sécurité » ou « logiciel anti-programme malveillant » vous est facturé. 

### <a name="if-a-log-analytics-agent-reports-to-multiple-workspaces-is-the-500-mb-free-data-ingestion-available-on-all-of-them"></a>Si un agent Log Analytics émet un rapport dans plusieurs espaces de travail, l’ingestion de données gratuite de 500 Mo est-elle disponible sur chacun d’eux ?
Oui. Si vous avez configuré votre agent Log Analytics pour envoyer des données à deux espaces de travail Log Analytics différents ou plus (multihébergement), vous obtenez une ingestion de données gratuite de 500 Mo. Cela est calculé par nœud, par espace de travail signalé, par jour et disponible pour chaque espace de travail sur lequel la solution « Sécurité » ou « Logiciel anti-programme malveillant » est installée. Toutes les données ingérées au-delà de la limite de 500 Mo vous seront facturées.

### <a name="is-the-500-mb-free-data-ingestion-calculated-for-an-entire-workspace-or-strictly-per-machine"></a>L’ingestion de données gratuite de 500 Mo est-elle calculée pour un espace de travail entier ou strictement machine par machine ?
Vous obtiendrez une ingestion de données gratuite de 500 Mo par jour, pour chaque machine Windows connectée à l’espace de travail. Spécifiquement pour les types de données de sécurité directement collectés par Defender pour le cloud. 

Ces données correspondent à un tarif journalier moyenné sur l’ensemble des nœuds. Par conséquent, même si certaines machines envoient 100 Mo et que d’autres envoient 800 Mo, si le total ne dépasse pas la limite de gratuité **[nombre de machines] x 500 Mo**, aucun supplément ne vous est facturé.

### <a name="what-data-types-are-included-in-the-500-mb-data-daily-allowance"></a>Quels sont les types de données inclus dans l’allocation quotidienne de données de 500 Mo ?
La facturation de Defender pour le cloud est étroitement liée à la facturation de Log Analytics. [Microsoft Defender pour les serveurs](defender-for-servers-introduction.md) alloue 500 Mo/nœud/jour pour les machines Windows par sous-ensemble de [types de données de sécurité](/azure/azure-monitor/reference/tables/tables-category#security) suivant :
- SecurityAlert
- SecurityBaseline
- SecurityBaselineSummary
- SecurityDetection
- SecurityEvent
- WindowsFirewall
- MaliciousIPCommunication
- SysmonEvent
- ProtectionStatus
- Les types de données Update et UpdateSummary sont activés lorsque la solution Update Management n’est pas exécutée sur l’espace de travail ou le ciblage de solution

Si l’espace de travail est au niveau tarifaire hérité Par nœud, les allocations de Defender pour le cloud et de Log Analytics sont combinées et appliquées conjointement avec toutes les données ingérées facturables.

## <a name="next-steps"></a>Étapes suivantes
Dans cet article, vous avez découvert les options de tarification de Defender pour le cloud. Consultez les documents connexes suivants :

- [Optimisation des coûts de votre charge de travail Azure](https://azure.microsoft.com/blog/how-to-optimize-your-azure-workload-costs/)
- [Détails de la tarification dans la devise de votre choix et en fonction de votre région](https://azure.microsoft.com/pricing/details/security-center/)
- Vous pouvez gérer vos coûts et limiter la quantité de données collectées pour une solution en limitant celle-ci à un ensemble spécifique d’agents. Utilisez le [ciblage de solution](../azure-monitor/insights/solution-targeting.md) pour appliquer une étendue à la solution et cibler un sous-ensemble d’ordinateurs dans l’espace de travail. Si vous utilisez le ciblage de solution, Defender pour le cloud indique que l’espace de travail n’a pas de solution.
