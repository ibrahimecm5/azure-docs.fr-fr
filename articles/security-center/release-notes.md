---
title: Notes de publication pour Microsoft Defender pour le cloud
description: Description des nouveautés et des modifications apportées à Microsoft Defender pour le cloud
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: reference
ms.date: 11/04/2021
ms.author: memildin
ms.openlocfilehash: 0e93c254a28aa62ce84f60daa02f91b247893a5b
ms.sourcegitcommit: 96deccc7988fca3218378a92b3ab685a5123fb73
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131577153"
---
# <a name="whats-new-in-microsoft-defender-for-cloud"></a>Quelles sont les nouveautés de Microsoft Defender pour le cloud ?

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Defender pour le cloud fait l’objet d’un développement actif et bénéficie d’améliorations en permanence. Pour vous tenir au courant des développements les plus récents, cette page fournit des informations sur les nouvelles fonctionnalités, les correctifs de bogues et les fonctionnalités déconseillées.

Cette page étant mise à jour fréquemment, nous vous invitons donc à la consulter souvent. 

Pour en savoir plus sur les changements *planifiés* qui seront bientôt disponibles dans Defender pour le cloud, consultez [Changements importants à venir dans Microsoft Defender pour le cloud](upcoming-changes.md). 

> [!TIP]
> Si vous recherchez des éléments datant de plus de six mois, vous les trouverez dans l’[Archive des nouveautés de Microsoft Defender pour le cloud](release-notes-archive.md).


## <a name="november-2021"></a>Novembre 2021

Notre version d’Ignite comprend les éléments suivants :

- [Azure Security Center et Azure Defender deviennent Microsoft Defender pour le cloud](#azure-security-center-and-azure-defender-become-microsoft-defender-for-cloud)
- [Outils CSPM natifs pour AWS et protection contre les menaces pour Amazon EKS et AWS EC2](#native-cspm-for-aws-and-threat-protection-for-amazon-eks-and-aws-ec2)
- [Classer par ordre de priorité les actions de sécurité en fonction du niveau de sensibilité des données (avec la technologie Azure Purview) (en préversion)](#prioritize-security-actions-by-data-sensitivity-powered-by-azure-purview-in-preview)
- [Évaluations étendues des contrôles de sécurité avec le benchmark de sécurité Azure v3](#expanded-security-control-assessments-with-azure-security-benchmark-v3)
- [Synchronisation bidirectionnelle (facultative) des alertes du connecteur Microsoft Sentinel en disponibilité générale (GA)](#microsoft-sentinel-connectors-optional-bi-directional-alert-synchronization-released-for-general-availability-ga)
- [Nouvelle recommandation pour l’envoi (push) des journaux AKS (Azure Kubernetes Service) vers Sentinel](#new-recommendation-to-push-azure-kubernetes-service-aks-logs-to-sentinel)

Autres changements apportés en novembre :

- [Ajout de la solution Gestion des menaces et des vulnérabilités Microsoft en tant que solution d’évaluation des vulnérabilités - En disponibilité générale (GA)](#microsoft-threat-and-vulnerability-management-added-as-vulnerability-assessment-solution---released-for-general-availability-ga)
- [Microsoft Defender pour point de terminaison pour Linux est désormais pris en charge par Microsoft Defender pour les serveurs - En disponibilité générale (GA)](#microsoft-defender-for-endpoint-for-linux-now-supported-by-microsoft-defender-for-servers---released-for-general-availability-ga)
- [Exportation de captures instantanées pour les recommandations et les résultats de sécurité (en préversion)](#snapshot-export-for-recommendations-and-security-findings-in-preview)
- [Provisionnement automatique des solutions d’évaluation des vulnérabilités - En disponibilité générale (GA)](#auto-provisioning-of-vulnerability-assessment-solutions-released-for-general-availability-ga)
- [Filtres d’inventaire logiciel dans l’inventaire des ressources - En disponibilité générale (GA)](#software-inventory-filters-in-asset-inventory-released-for-general-availability-ga)

### <a name="azure-security-center-and-azure-defender-become-microsoft-defender-for-cloud"></a>Azure Security Center et Azure Defender deviennent Microsoft Defender pour le cloud

D’après le [rapport sur l’état du cloud en 2021](https://info.flexera.com/CM-REPORT-State-of-the-Cloud#download), 92 % des organisations ont désormais une stratégie multicloud. Chez Microsoft, notre objectif est de centraliser la sécurité dans ces environnements et d’aider les équipes de sécurité à travailler plus efficacement.

**Microsoft Defender pour le cloud** (anciennement Azure Security Center et Azure Defender) est une solution CSPM (gestion de la posture de sécurité cloud) et CWP (protection de charge de travail cloud). Elle détecte les faiblesses de votre configuration cloud, contribue à renforcer l’ensemble de la posture de sécurité de votre environnement et protège les charges de travail dans les environnements multiclouds et hybrides.

Dans Ignite 2019, nous avons partagé notre vision consistant à créer l’approche la plus complète possible pour la sécurisation de votre patrimoine numérique et l’intégration des technologies XDR sous la marque Microsoft Defender. L’unification d’Azure Security Center et d’Azure Defender sous le nouveau nom **Microsoft Defender pour le cloud** reflète les fonctionnalités intégrées de notre offre de sécurité ainsi que notre capacité à prendre en charge n’importe quelle plateforme cloud.


### <a name="native-cspm-for-aws-and-threat-protection-for-amazon-eks-and-aws-ec2"></a>Outils CSPM natifs pour AWS et protection contre les menaces pour Amazon EKS et AWS EC2

Une nouvelle page **Paramètres de l’environnement** offre une visibilité et un contrôle accrus sur vos groupes d’administration, vos abonnements et vos comptes AWS. La page est conçue pour intégrer les comptes AWS à grande échelle : connectez votre **compte de gestion** AWS, et vous intégrerez automatiquement les comptes existants et futurs. 

:::image type="content" source="media/release-notes/add-aws-account.png" alt-text="Utilisez la nouvelle page des paramètres d’environnement pour connecter vos comptes AWS.":::

Une fois que vous avez ajouté vos comptes AWS, Defender pour le cloud protège vos ressources AWS avec tout ou partie des plans suivants :

- Les **fonctionnalités CSPM de Defender pour le cloud** s’étendent à vos ressources AWS. Ce plan sans agent évalue vos ressources AWS conformément aux recommandations de sécurité spécifiques à AWS. Celles-ci sont incluses dans votre niveau de sécurité. Les ressources sont également évaluées par rapport à leur conformité aux standards intégrés spécifiques à AWS (AWS CIS, AWS PCI DSS et AWS Foundational Security Best Practices). La [page d’inventaire des ressources](asset-inventory.md) de Defender pour le cloud est une fonctionnalité multicloud qui vous permet de gérer vos ressources AWS avec vos ressources Azure.
- **Microsoft Defender pour Kubernetes** étend sa détection des menaces contre les conteneurs et ses défenses avancées à vos **clusters Amazon EKS Linux**.
- **Microsoft Defender pour les serveurs** ajoute la détection des menaces et les défenses avancées à vos instances EC2 Linux et Windows. Ce plan comprend la licence intégrée de Microsoft Defender pour point de terminaison, des bases de référence de sécurité et des évaluations au niveau de l’OS, l’analyse de l’évaluation des vulnérabilités, les contrôles d’application adaptatifs (AAC), le monitoring de l’intégrité des fichiers (FIM), etc.

Découvrez plus en détail la [connexion de vos comptes AWS à Microsoft Defender pour le cloud](quickstart-onboard-aws.md).


### <a name="prioritize-security-actions-by-data-sensitivity-powered-by-azure-purview-in-preview"></a>Classer par ordre de priorité les actions de sécurité en fonction du niveau de sensibilité des données (avec la technologie Azure Purview) (en préversion)
Les ressources de données restent une cible populaire pour les auteurs de menaces. Il est donc crucial pour les équipes de sécurité d’identifier, de classer par ordre de priorité et de sécuriser les ressources de données sensibles dans leurs environnements cloud.

Pour résoudre ce problème, Microsoft Defender pour le cloud intègre désormais les informations sur la sensibilité d’[Azure Purview](../purview/overview.md). Azure Purview est un service de gouvernance unifiée des données qui fournit des insights riches sur la sensibilité de vos données dans les charges de travail multiclouds et locales.

L’intégration à Azure Purview étend votre visibilité de la sécurité dans Defender pour le cloud, du niveau de l’infrastructure aux données, offrant ainsi une toute nouvelle façon de classer par ordre de priorité les ressources et les activités de sécurité pour vos équipes de sécurité.

Découvrez davantage d’informations dans [Classer par ordre de priorité les actions de sécurité en fonction du degré de sensibilité des données](information-protection.md).


### <a name="expanded-security-control-assessments-with-azure-security-benchmark-v3"></a>Évaluations étendues des contrôles de sécurité avec le benchmark de sécurité Azure v3
Les recommandations de sécurité de Microsoft Defender pour le cloud sont activées et prises en charge par le benchmark de sécurité Azure. 

Le [benchmark de sécurité Azure](../security/benchmarks/introduction.md) constitue l’ensemble des recommandations spécifiques à Azure et créées par Microsoft concernant les bonnes pratiques de sécurité et de conformité basées sur les frameworks de conformité courants. Ce benchmark, largement respecté et centré sur le cloud, est basé sur les contrôles du [CIS (Center for Internet Security)](https://www.cisecurity.org/benchmark/azure/) et du [NIST (National Institute of Standards and Technology)](https://www.nist.gov/).

À partir d’Ignite 2021, le benchmark de sécurité Azure **v3** est disponible dans le [tableau de bord de conformité réglementaire de Defender pour le cloud](update-regulatory-compliance-packages.md) et activé en tant que nouvelle initiative par défaut pour tous les abonnements Azure protégés par Microsoft Defender pour le cloud. 

Les améliorations de la version v3 incluent les éléments suivants : 

- Mappages supplémentaires aux frameworks du secteur [PCI-DSS v3.2.1](https://www.pcisecuritystandards.org/documents/PCI_DSS_v3-2-1.pdf) et [CIS Controls v8](https://www.cisecurity.org/controls/v8/).
- Conseils d’aide plus précis et plus actionnables pour les contrôles avec l’introduction des éléments suivants :
    - **Principes de sécurité** - Fournit un insight des objectifs de sécurité globaux qui constituent le fondement de nos recommandations.
    - **Conseils Azure** - « Guide pratique » technique permettant d’atteindre ces objectifs.

Les nouveaux contrôles incluent la sécurité DevOps pour les problèmes tels que la modélisation des menaces et la sécurité de la chaîne d’approvisionnement des logiciels ainsi que la gestion des clés et des certificats pour les bonnes pratiques dans Azure.

Apprenez-en davantage dans [Présentation du Benchmark de sécurité Azure](/security/benchmark/azure/introduction).


### <a name="microsoft-sentinel-connectors-optional-bi-directional-alert-synchronization-released-for-general-availability-ga"></a>Synchronisation bidirectionnelle (facultative) des alertes du connecteur Microsoft Sentinel en disponibilité générale (GA)

En juillet, [nous avons annoncé](#azure-sentinel-connector-now-includes-optional-bi-directional-alert-synchronization-in-preview) une fonctionnalité en préversion, la **synchronisation bidirectionnelle des alertes**, pour le connecteur intégré de [Microsoft Sentinel](../sentinel/index.yml) (solution native cloud SIEM et SOAR de Microsoft). Cette fonctionnalité est désormais en disponibilité générale (GA).

Quand vous connectez Microsoft Defender pour le cloud à Microsoft Sentinel, l’état des alertes de sécurité est synchronisé entre les deux services. Ainsi, par exemple, lorsqu’une alerte est fermée dans Defender pour le cloud, cette alerte s’affiche également comme étant fermée dans Microsoft Sentinel. Le changement d’état d’une alerte dans Defender pour le cloud n’affecte pas l’état des **incidents** Microsoft Sentinel qui contiennent l’alerte Microsoft Sentinel synchronisée, mais uniquement l’état de l’alerte synchronisée.

Quand vous activez la **synchronisation bidirectionnelle des alertes**, vous synchronisez automatiquement l’état des alertes Defender pour le cloud d’origine avec les incidents Microsoft Sentinel qui contiennent les copies de ces alertes Defender pour le cloud. Ainsi, par exemple, quand un incident Microsoft Sentinel contenant une alerte Defender pour le cloud est fermé, Defender pour le cloud ferme automatiquement l’alerte d’origine correspondante.

Découvrez davantage d’informations dans [Connecter les alertes Azure Defender à partir d’Azure Security Center](../sentinel/connect-azure-security-center.md) et [Envoyer en streaming les alertes vers Azure Sentinel](export-to-siem.md#stream-alerts-to-microsoft-sentinel).


### <a name="new-recommendation-to-push-azure-kubernetes-service-aks-logs-to-sentinel"></a>Nouvelle recommandation pour l’envoi (push) des journaux AKS (Azure Kubernetes Service) vers Sentinel

Afin d’améliorer davantage la valeur combinée de Defender pour le cloud et de Microsoft Sentinel, nous allons désormais mettre en évidence les instances d’Azure Kubernetes Service qui n’envoient pas de données de journal à Microsoft Sentinel.

Les équipes SecOps peuvent choisir l’espace de travail Microsoft Sentinel approprié directement à partir de la page des détails des recommandations et activer immédiatement le streaming des journaux bruts. Cette connexion transparente entre les deux produits permet aux équipes de sécurité de garantir une couverture complète de la journalisation des charges de travail pour rester maîtres de l’ensemble de leur environnement.

La nouvelle recommandation « Les journaux de diagnostic du service Kubernetes doivent être activés » comprend l’option « Corriger » pour une correction plus rapide.

Nous avons également amélioré la recommandation « L’audit sur SQL Server doit être activé » avec les mêmes fonctionnalités de streaming que celles de Sentinel. 


### <a name="microsoft-threat-and-vulnerability-management-added-as-vulnerability-assessment-solution---released-for-general-availability-ga"></a>Ajout de la solution Gestion des menaces et des vulnérabilités Microsoft en tant que solution d’évaluation des vulnérabilités - En disponibilité générale (GA)

En octobre, [nous avons annoncé](#microsoft-threat-and-vulnerability-management-added-as-vulnerability-assessment-solution-in-preview) une extension de l’intégration entre [Microsoft Defender pour les serveurs](defender-for-servers-introduction.md) et Microsoft Defender pour point de terminaison afin de permettre la prise en charge d’un nouveau fournisseur d’évaluation des vulnérabilités pour vos machines : [Gestion des menaces et des vulnérabilités Microsoft](/microsoft-365/security/defender-endpoint/next-gen-threat-and-vuln-mgt). Cette fonctionnalité est désormais en disponibilité générale (GA).

Utilisez la **gestion des menaces et des vulnérabilités** pour découvrir les vulnérabilités et les erreurs de configuration en quasi-temps réel grâce à l’[intégration à Microsoft Defender pour point de terminaison](integration-defender-for-endpoint.md), et sans avoir besoin d’agents supplémentaires ou d’analyses périodiques. Gestion des menaces et des vulnérabilités hiérarchise les vulnérabilités en fonction du paysage des menaces et des détections dans votre organisation.

Utilisez la recommandation de sécurité « [Une solution d’évaluation des vulnérabilités doit être activée sur vos machines virtuelles](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/ffff0522-1e88-47fc-8382-2a80ba848f5d) » pour faire apparaître les vulnérabilités détectées par Gestion des menaces et des vulnérabilités pour vos [machines prises en charge](/microsoft-365/security/defender-endpoint/tvm-supported-os?view=o365-worldwide&preserve-view=true). 

Pour faire apparaître automatiquement les vulnérabilités sur les machines existantes et nouvelles sans avoir besoin de corriger manuellement la recommandation, consultez [Les solutions d’évaluation des vulnérabilités peuvent désormais être activées automatiquement (en préversion)](#vulnerability-assessment-solutions-can-now-be-auto-enabled-in-preview).

En savoir plus dans [Investiguer les faiblesses avec la gestion des menaces et des vulnérabilités de Microsoft Defender pour point de terminaison](deploy-vulnerability-assessment-tvm.md).

### <a name="microsoft-defender-for-endpoint-for-linux-now-supported-by-microsoft-defender-for-servers---released-for-general-availability-ga"></a>Microsoft Defender pour point de terminaison pour Linux est désormais pris en charge par Microsoft Defender pour les serveurs - En disponibilité générale (GA)

En août, [nous avons annoncé](#microsoft-defender-for-endpoint-for-linux-now-supported-by-azure-defender-for-servers-in-preview) la prise en charge en préversion du déploiement du capteur [Defender pour point de terminaison pour Linux](/microsoft-365/security/defender-endpoint/microsoft-defender-endpoint-linux) sur les machines Linux compatibles. Cette fonctionnalité est désormais en disponibilité générale (GA).

[Microsoft Defender pour les serveurs](defender-for-servers-introduction.md) comprend une licence intégrée pour [Microsoft Defender pour point de terminaison](https://www.microsoft.com/microsoft-365/security/endpoint-defender). Ensemble, ils offrent des fonctionnalités EDR (protection évolutive des points de terminaison) complètes.

Quand Microsoft Defender pour point de terminaison détecte une menace, il déclenche une alerte. L’alerte s’affiche dans Defender pour le cloud. À partir de Defender pour le cloud, vous pouvez également accéder à la console Defender pour point de terminaison et effectuer un examen détaillé pour découvrir l’étendue de l’attaque.

En savoir plus dans [Protéger vos points de terminaison avec la solution EDR intégrée de Security Center : Microsoft Defender for Endpoint](integration-defender-for-endpoint.md).


### <a name="snapshot-export-for-recommendations-and-security-findings-in-preview"></a>Exportation de captures instantanées pour les recommandations et les résultats de sécurité (en préversion)

Defender pour le cloud génère des alertes et des recommandations de sécurité détaillées. Vous pouvez les afficher dans le portail ou au moyen d’outils de programmation. Si nécessaire, vous pouvez également exporter en partie ou en totalité ces informations à des fins de suivi avec d’autres outils de supervision dans votre environnement.

La fonctionnalité d’**exportation continue** de Defender pour le cloud vous permet de personnaliser entièrement *ce* qui doit être exporté et *où* cela doit être exporté. Découvrez davantage d’informations dans [Exporter en continu les données Microsoft Defender pour le cloud](continuous-export.md).

Même s’il s’agit d’une fonctionnalité *en continu*, il existe également une option permettant d’exporter des captures instantanées hebdomadaires. Jusqu’à présent, ces captures instantanées hebdomadaires se limitaient aux données liées au niveau de sécurité et à la conformité réglementaire. Nous avons ajouté l’exportation des recommandations et des résultats de sécurité.

### <a name="auto-provisioning-of-vulnerability-assessment-solutions-released-for-general-availability-ga"></a>Provisionnement automatique des solutions d’évaluation des vulnérabilités - En disponibilité générale (GA)

En octobre, [nous avons annoncé](#vulnerability-assessment-solutions-can-now-be-auto-enabled-in-preview) l’ajout de solutions d’évaluation des vulnérabilités à la page de provisionnement automatique de Defender pour le cloud. Cela concerne les machines virtuelles Azure et les machines Azure Arc des abonnements protégés par [Azure Defender pour les serveurs](defender-for-servers-introduction.md). Cette fonctionnalité est désormais en disponibilité générale (GA).

Si l’[intégration à Microsoft Defender pour point de terminaison](integration-defender-for-endpoint.md) est activée, Defender pour le cloud offre un choix de solutions d’évaluation des vulnérabilités :

- (**NOUVEAU**) Le module Gestion des menaces et des vulnérabilités de Microsoft de Microsoft Defender pour point de terminaison (voir [la note de publication](#microsoft-threat-and-vulnerability-management-added-as-vulnerability-assessment-solution---released-for-general-availability-ga))
- L’agent Qualys intégré

La solution que vous avez choisie sera automatiquement activée sur les machines prises en charge.

En savoir plus dans [Configurer automatiquement l’évaluation des vulnérabilités pour vos machines](auto-deploy-vulnerability-assessment.md).

### <a name="software-inventory-filters-in-asset-inventory-released-for-general-availability-ga"></a>Filtres d’inventaire logiciel dans l’inventaire des ressources - En disponibilité générale (GA)

En octobre, [nous avons annoncé](#software-inventory-filters-added-to-asset-inventory-in-preview) de nouveaux filtres pour la page d’[inventaire des ressources](asset-inventory.md) afin de sélectionner les machines exécutant des logiciels spécifiques, et même de spécifier les versions souhaitées. Cette fonctionnalité est désormais en disponibilité générale (GA).

Vous pouvez interroger les données d’inventaire logiciel dans l’**Afficheur Azure Resource Graph**.

Pour utiliser ces fonctionnalités, vous devez activer l’[intégration à Microsoft Defender pour point de terminaison](integration-defender-for-endpoint.md). 

Pour plus de détails, y compris des exemples de requêtes Kusto pour Azure Resource Graph, consultez [Accéder à un inventaire logiciel](asset-inventory.md#access-a-software-inventory).

### <a name="new-aks-security-policy-added-to-default-initiative--for-use-by-private-preview-customers-only"></a>Ajout d’une nouvelle stratégie de sécurité AKS à l’initiative par défaut pour les clients de la préversion privée uniquement

Afin de garantir la sécurité par défaut des charges de travail Kubernetes, Defender pour le cloud inclut des stratégies et des recommandations de durcissement au niveau de Kubernetes, notamment des options de mise en œuvre avec contrôle d’admission Kubernetes.

Dans le cadre de ce projet, nous avons ajouté une stratégie et une recommandation (désactivées par défaut) pour limiter le déploiement sur les clusters Kubernetes. La stratégie se trouve dans l’initiative par défaut, mais elle ne concerne que les organisations qui s’inscrivent à la préversion privée associée.

Vous pouvez ignorer sans problème les stratégies ainsi que la recommandation (« Les clusters Kubernetes doivent limiter le déploiement d’images vulnérables »). Cela n’a aucun impact sur votre environnement. 

Si vous souhaitez participer à la préversion privée, vous devez être membre de la boucle de préversion privée. Si vous n’êtes pas encore membre, envoyez une demande [ici](https://aka.ms/atscale). Les membres sont notifiés au lancement de la préversion.

## <a name="october-2021"></a>Octobre 2021

Les mises à jour d’octobre sont les suivantes :

- [Ajout de Gestion des menaces et des vulnérabilités de Microsoft comme solution d’évaluation des vulnérabilités (en préversion)](#microsoft-threat-and-vulnerability-management-added-as-vulnerability-assessment-solution-in-preview)
- [Les solutions d’évaluation des vulnérabilités peuvent désormais être activées automatiquement (en préversion)](#vulnerability-assessment-solutions-can-now-be-auto-enabled-in-preview)
- [Ajout de filtres d’inventaire logiciel à l’inventaire des ressources (en préversion)](#software-inventory-filters-added-to-asset-inventory-in-preview)
- [Modification du préfixe de certains types d’alerte de « ARM_ » à « VM_ »](#changed-prefix-of-some-alert-types-from-arm_-to-vm_)
- [Modifications apportées à la logique d’une recommandation de sécurité pour les clusters Kubernetes](#changes-to-the-logic-of-a-security-recommendation-for-kubernetes-clusters)
- [Les pages de détails sur les recommandations affichent maintenant les recommandations associées](#recommendations-details-pages-now-show-related-recommendations)
- [Nouvelles alertes relatives à Azure Defender pour Kubernetes (préversion)](#new-alerts-for-azure-defender-for-kubernetes-in-preview)


### <a name="microsoft-threat-and-vulnerability-management-added-as-vulnerability-assessment-solution-in-preview"></a>Ajout de Gestion des menaces et des vulnérabilités de Microsoft comme solution d’évaluation des vulnérabilités (en préversion)

Nous avons étendu l’intégration entre [Azure Defender pour les serveurs](defender-for-servers-introduction.md) et Microsoft Defender pour point de terminaison afin de permettre la prise en charge d’un nouveau fournisseur d’évaluation des vulnérabilités pour vos machines : [Gestion des menaces et des vulnérabilités Microsoft](/microsoft-365/security/defender-endpoint/next-gen-threat-and-vuln-mgt). 

Utilisez la **gestion des menaces et des vulnérabilités** pour découvrir les vulnérabilités et les erreurs de configuration en quasi-temps réel grâce à l’[intégration à Microsoft Defender pour point de terminaison](integration-defender-for-endpoint.md), et sans avoir besoin d’agents supplémentaires ou d’analyses périodiques. Gestion des menaces et des vulnérabilités hiérarchise les vulnérabilités en fonction du paysage des menaces et des détections dans votre organisation.

Utilisez la recommandation de sécurité « [Une solution d’évaluation des vulnérabilités doit être activée sur vos machines virtuelles](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/ffff0522-1e88-47fc-8382-2a80ba848f5d) » pour faire apparaître les vulnérabilités détectées par Gestion des menaces et des vulnérabilités pour vos [machines prises en charge](/microsoft-365/security/defender-endpoint/tvm-supported-os?view=o365-worldwide&preserve-view=true). 

Pour faire apparaître automatiquement les vulnérabilités sur les machines existantes et nouvelles sans avoir besoin de corriger manuellement la recommandation, consultez [Les solutions d’évaluation des vulnérabilités peuvent désormais être activées automatiquement (en préversion)](#vulnerability-assessment-solutions-can-now-be-auto-enabled-in-preview).

En savoir plus dans [Investiguer les faiblesses avec la gestion des menaces et des vulnérabilités de Microsoft Defender pour point de terminaison](deploy-vulnerability-assessment-tvm.md).

### <a name="vulnerability-assessment-solutions-can-now-be-auto-enabled-in-preview"></a>Les solutions d’évaluation des vulnérabilités peuvent désormais être activées automatiquement (en préversion)

La page d’approvisionnement automatique de Security Center comprend désormais l’option permettant d’activer automatiquement une solution d’évaluation des vulnérabilités sur les machines virtuelles Azure et les machines Azure Arc sur des abonnements protégés par [Azure Defender pour les serveurs](defender-for-servers-introduction.md).

Si l’[intégration à Microsoft Defender pour point de terminaison](integration-defender-for-endpoint.md) est activée, Defender pour le cloud offre un choix de solutions d’évaluation des vulnérabilités :

- (**NOUVEAU**) Le module Gestion des menaces et des vulnérabilités de Microsoft de Microsoft Defender pour point de terminaison (voir [la note de publication](#microsoft-threat-and-vulnerability-management-added-as-vulnerability-assessment-solution-in-preview))
- L’agent Qualys intégré

:::image type="content" source="media/deploy-vulnerability-assessment-tvm/auto-provision-vulnerability-assessment-agent.png" alt-text="Configurer l’approvisionnement automatique de Gestion des menaces et des vulnérabilités de Microsoft à partir d’Azure Security Center.":::

La solution que vous avez choisie sera automatiquement activée sur les machines prises en charge.

En savoir plus dans [Configurer automatiquement l’évaluation des vulnérabilités pour vos machines](auto-deploy-vulnerability-assessment.md).

### <a name="software-inventory-filters-added-to-asset-inventory-in-preview"></a>Ajout de filtres d’inventaire logiciel à l’inventaire des ressources (en préversion)

La page [Inventaire des ressources](asset-inventory.md) comprend désormais un filtre permettant de sélectionner les machines exécutant un logiciel spécifique, et même de spécifier les versions qui vous intéressent. 

En outre, vous pouvez interroger les données d’inventaire logiciel dans **Azure Resource Graph Explorer**.

Pour utiliser ces nouvelles fonctionnalités, vous devez activer l’[intégration à Microsoft Defender pour point de terminaison](integration-defender-for-endpoint.md). 

Pour plus de détails, y compris des exemples de requêtes Kusto pour Azure Resource Graph, consultez [Accéder à un inventaire logiciel](asset-inventory.md#access-a-software-inventory).

:::image type="content" source="media/deploy-vulnerability-assessment-tvm/software-inventory.png" alt-text="Si vous avez activé la solution de gestion des menaces et des vulnérabilités, l’inventaire des ressources de Security Center offre un filtre permettant de sélectionner les ressources en fonction de leurs logiciels installés.":::

### <a name="changed-prefix-of-some-alert-types-from-arm_-to-vm_"></a>Modification du préfixe de certains types d’alerte de « ARM_ » à « VM_ » 

En juillet 2021, nous avons annoncé une [réorganisation logique d’Azure Defender pour les alertes Resource Manager](release-notes.md#logical-reorganization-of-azure-defender-for-resource-manager-alerts) 

Dans le cadre d’une réorganisation logique de certains des plans Azure Defender, nous avons déplacé 21 alertes d’[Azure Defender pour Resource Manager](defender-for-resource-manager-introduction.md) vers [Azure Defender pour les serveurs](defender-for-servers-introduction.md).

Avec cette mise à jour, nous avons modifié les préfixes de ces alertes pour qu’elles correspondent à cette réaffectation et remplacé « ARM_ » par « VM_ », comme indiqué dans le tableau suivant :

| Nom d’origine                                  | À partir de cette modification                              |
|------------------------------------------------|-----------------------------------------------|
| ARM_AmBroadFilesExclusion                      | VM_AmBroadFilesExclusion                      |
| ARM_AmDisablementAndCodeExecution              | VM_AmDisablementAndCodeExecution              |
| ARM_AmDisablement                              | VM_AmDisablement                              |
| ARM_AmFileExclusionAndCodeExecution            | VM_AmFileExclusionAndCodeExecution            |
| ARM_AmTempFileExclusionAndCodeExecution        | VM_AmTempFileExclusionAndCodeExecution        |
| ARM_AmTempFileExclusion                        | VM_AmTempFileExclusion                        |
| ARM_AmRealtimeProtectionDisabled               | VM_AmRealtimeProtectionDisabled               |
| ARM_AmTempRealtimeProtectionDisablement        | VM_AmTempRealtimeProtectionDisablement        |
| ARM_AmRealtimeProtectionDisablementAndCodeExec | VM_AmRealtimeProtectionDisablementAndCodeExec |
| ARM_AmMalwareCampaignRelatedExclusion          | VM_AmMalwareCampaignRelatedExclusion          |
| ARM_AmTemporarilyDisablement                   | VM_AmTemporarilyDisablement                   |
| ARM_UnusualAmFileExclusion                     | VM_UnusualAmFileExclusion                     |
| ARM_CustomScriptExtensionSuspiciousCmd         | VM_CustomScriptExtensionSuspiciousCmd         |
| ARM_CustomScriptExtensionSuspiciousEntryPoint  | VM_CustomScriptExtensionSuspiciousEntryPoint  |
| ARM_CustomScriptExtensionSuspiciousPayload     | VM_CustomScriptExtensionSuspiciousPayload     |
| ARM_CustomScriptExtensionSuspiciousFailure     | VM_CustomScriptExtensionSuspiciousFailure     |
| ARM_CustomScriptExtensionUnusualDeletion       | VM_CustomScriptExtensionUnusualDeletion       |
| ARM_CustomScriptExtensionUnusualExecution      | VM_CustomScriptExtensionUnusualExecution      |
| ARM_VMAccessUnusualConfigReset                 | VM_VMAccessUnusualConfigReset                 |
| ARM_VMAccessUnusualPasswordReset               | VM_VMAccessUnusualPasswordReset               |
| ARM_VMAccessUnusualSSHReset                    | VM_VMAccessUnusualSSHReset                    |
|||

En savoir plus sur les plans [Azure Defender pour Resource Manager](defender-for-resource-manager-introduction.md) et [Azure Defender pour les serveurs](defender-for-servers-introduction.md).

### <a name="changes-to-the-logic-of-a-security-recommendation-for-kubernetes-clusters"></a>Modifications apportées à la logique d’une recommandation de sécurité pour les clusters Kubernetes

La recommandation « Les clusters Kubernetes ne doivent pas utiliser l’espace de noms par défaut » empêche l’utilisation de l’espace de noms par défaut pour une plage de types de ressource. Deux des types de ressource inclus dans cette recommandation ont été supprimés : ConfigMap et Secret. 

Pour en savoir plus sur cette recommandation et sur le renforcement de vos clusters Kubernetes, consultez [Comprendre Azure Policy pour les clusters Kubernetes](../governance/policy/concepts/policy-for-kubernetes.md).

### <a name="recommendations-details-pages-now-show-related-recommendations"></a>Les pages de détails sur les recommandations affichent maintenant les recommandations associées

Pour clarifier les relations entre les différentes recommandations, nous avons ajouté une zone **Recommandations associées** aux pages de détails de nombreuses recommandations. 

Les trois types de relations qui sont affichés sur ces pages sont les suivants :

- **Condition préalable** : recommandation qui doit être complétée avant la recommandation sélectionnée
- **Alternative** : recommandation différente qui offre une autre façon d’atteindre les objectifs de la recommandation sélectionnée
- **Dépendante** : recommandation pour laquelle la recommandation sélectionnée est une condition préalable

Pour chaque recommandation associée, le nombre de ressources défectueuses s’affiche dans la colonne « Ressources affectées ».

> [!TIP]
> Si une recommandation associée est grisée, sa dépendance n’est pas encore terminée et n’est donc pas disponible.

Voici un exemple de recommandations associées :

1. Security Center vérifie la présence de solutions d’évaluation des vulnérabilités prises en charge sur vos ordinateurs :<br>
    **Une solution d’évaluation des vulnérabilités doit être activée sur vos machines virtuelles**

1. Si une vulnérabilité est détectée, vous serez informé des vulnérabilités découvertes :<br>
    **Les vulnérabilités de vos machines virtuelles doivent être corrigées**

Bien entendu, Security Center ne peut pas vous notifier les vulnérabilités découvertes, sauf s’il trouve une solution d’évaluation des vulnérabilités prise en charge.

Par conséquent :

 - Recommandation #1 est une condition préalable à la recommandation #2
 - La recommandation #2 dépend de la recommandation #1

:::image type="content" source="media/release-notes/related-recommendations-solution-not-found.png" alt-text="Capture d’écran de la recommandation de déploiement de la solution d’évaluation des vulnérabilités.":::

:::image type="content" source="media/release-notes/related-recommendations-vulnerabilities-found.png" alt-text="Capture d’écran de la recommandation pour résoudre les vulnérabilités découvertes.":::



### <a name="new-alerts-for-azure-defender-for-kubernetes-in-preview"></a>Nouvelles alertes relatives à Azure Defender pour Kubernetes (préversion)

Pour étendre les protections contre les menaces fournies par Azure Defender pour Kubernetes, nous avons ajouté deux alertes en préversion.

Ces alertes sont générées à partir d’un nouveau modèle Machine Learning et des fonctionnalités analytiques avancées de Kubernetes, qui mesurent plusieurs attributs de déploiement et d’attribution de rôles par rapport aux activités antérieures dans le cluster ainsi que dans tous les clusters faisant l’objet d’un monitoring par Azure Defender.

| Alerte (type d’alerte)                                                                 | Description                                                                                                                                                                                                                                                                                                                                                      | Tactique MITRE | severity |
|------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:--------------------------------------------:|----------|
| **Déploiement anormal d’un pod (préversion)**<br>(K8S_AnomalousPodDeployment)             | L’analyse du journal d’audit Kubernetes a détecté un déploiement de pod anormal compte tenu de l’activité antérieure de déploiement de pod. Cette activité est considérée comme une anomalie lorsque l’on prend en compte la façon dont les différentes fonctionnalités vues dans l’opération de déploiement sont dans les relations entre elles. Les fonctionnalités surveillées par cette analyse incluent le registre d’images de conteneur utilisé, le compte qui effectue le déploiement, le jour de la semaine, la fréquence à laquelle ce compte effectue des déploiements de pod, l’agent utilisateur utilisé dans l’opération ou d’autres fonctionnalités. Il est également possible de déterminer s’il s’agit d’un espace de noms dans lequel le déploiement du pod se produit souvent. Les principales raisons justifiant le déclenchement de cette alerte en tant qu’activité anormale sont détaillées dans les propriétés étendues de l’alerte. | Exécution | Moyenne |
| **Autorisations de rôle excessives affectées dans le cluster Kubernetes (préversion)**<br>(K8S_ServiceAcountPermissionAnomaly) | L’analyse des journaux d’audit Kubernetes a détecté une attribution de rôle d’autorisations excessive à votre cluster. D’après l’examen des attributions de rôles, les autorisations répertoriées sont rares pour le compte de service spécifique. Cette détection prend en compte les attributions de rôles antérieures au même compte de service sur les clusters contrôlés par Azure, le volume par autorisation et l’impact de l’autorisation spécifique. Le modèle de détection d’anomalies utilisé pour cette alerte prend en compte la façon dont cette autorisation est utilisée sur tous les clusters analysés par Azure Defender. | Réaffectation de privilèges | Faible |
|||

Pour obtenir la liste complète des alertes Kubernetes, consultez [Alertes pour les clusters Kubernetes](alerts-reference.md#alerts-k8scluster).

## <a name="september-2021"></a>Septembre 2021

En septembre, la mise à jour suivante a été publiée :

### <a name="two-new-recommendations-to-audit-os-configurations-for-azure-security-baseline-compliance-in-preview"></a>Deux nouvelles recommandations pour auditer les configurations de système d’exploitation pour la conformité de la ligne de base de sécurité Azure (en préversion)

Les deux recommandations suivantes ont été publiées pour évaluer la conformité de vos machines à la [Base de référence de sécurité Windows](../governance/policy/samples/guest-configuration-baseline-windows.md) et à la [Base de référence de sécurité Linux](../governance/policy/samples/guest-configuration-baseline-linux.md) :

- Pour les machines Windows, [les vulnérabilités dans la configuration de la sécurité sur vos machines Windows doivent être corrigées (avec Guest Configuration)](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/1f655fb7-63ca-4980-91a3-56dbc2b715c6)
- Pour les machines Linux, [les vulnérabilités dans la configuration de la sécurité sur vos machines Linux doivent être corrigées (avec Guest Configuration)](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/8c3d9ad0-3639-4686-9cd2-2b2ab2609bda)

Ces recommandations utilisent la fonctionnalité de configuration d’invité d’Azure Policy pour comparer la configuration du système d’exploitation d’une machine à la ligne de base définie dans le [Benchmark de sécurité Azure](/security/benchmark/azure/overview).

Pour en savoir plus sur l’utilisation de ces recommandations, consultez [Renforcer la configuration du système d’exploitation d’une machine à l’aide de la configuration d’invité](apply-security-baseline.md).

## <a name="august-2021"></a>Août 2021

Les mises à jour en août sont les suivantes :

- [Microsoft Defender pour point de terminaison pour Linux est désormais pris en charge par Azure Defender pour les serveurs (en préversion)](#microsoft-defender-for-endpoint-for-linux-now-supported-by-azure-defender-for-servers-in-preview)
- [Deux nouvelles recommandations pour la gestion des solutions de protection de point de terminaison (en préversion)](#two-new-recommendations-for-managing-endpoint-protection-solutions-in-preview)
- [Dépannage et conseils intégrés pour la résolution des problèmes courants](#built-in-troubleshooting-and-guidance-for-solving-common-issues)
- [Rapports d’audit Azure du tableau de bord de conformité réglementaire publiés pour la mise à la disposition générale](#regulatory-compliance-dashboards-azure-audit-reports-released-for-general-availability-ga)
- [Dépréciation de la recommandation « Les problèmes d’intégrité de l’agent Log Analytics doivent être résolus sur vos machines »](#deprecated-recommendation-log-analytics-agent-health-issues-should-be-resolved-on-your-machines)
- [Azure Defender pour les registres de conteneurs analyse désormais les vulnérabilités dans les registres protégés avec Azure Private Link](#azure-defender-for-container-registries-now-scans-for-vulnerabilities-in-registries-protected-with-azure-private-link)
- [Security Center peut désormais approvisionner automatiquement l’extension de configuration d’invité d’Azure Policy (en préversion)](#security-center-can-now-auto-provision-the-azure-policys-guest-configuration-extension-in-preview)
- [Les recommandations pour activer les plans Azure defender prennent désormais en charge « Appliquer »](#recommendations-to-enable-azure-defender-plans-now-support-enforce)
- [Exportations CSV de données de recommandation désormais limitées à 20 Mo](#csv-exports-of-recommendation-data-now-limited-to-20-mb)
- [La page des recommandations compte désormais plusieurs vues](#recommendations-page-now-includes-multiple-views)

### <a name="microsoft-defender-for-endpoint-for-linux-now-supported-by-azure-defender-for-servers-in-preview"></a>Microsoft Defender pour point de terminaison pour Linux est désormais pris en charge par Azure Defender pour les serveurs (en préversion)

[Azure Defender pour les serveurs](defender-for-servers-introduction.md) comprend une licence intégrée pour [Microsoft Defender pour point de terminaison](https://www.microsoft.com/microsoft-365/security/endpoint-defender). Ensemble, ils offrent des fonctionnalités EDR (protection évolutive des points de terminaison) complètes.

Quand Microsoft Defender pour point de terminaison détecte une menace, il déclenche une alerte. L’alerte s’affiche dans Security Center. À partir de Security Center, vous pouvez également accéder à la console Microsoft Defender pour point de terminaison et effectuer un examen détaillé pour découvrir l’étendue de l’attaque.

Pendant la période de préversion, vous allez déployer le capteur [Defender pour point de terminaison pour Linux](/microsoft-365/security/defender-endpoint/microsoft-defender-endpoint-linux) sur vos machines Linux de deux manières, selon que vous les avez déjà déployées sur vos machines Windows :

- [Utilisateurs existants pour lesquels les fonctionnalités de sécurité renforcée de Defender pour le cloud sont activées et disposant de Microsoft Defender pour point de terminaison pour Windows](integration-defender-for-endpoint.md#existing-users-with-defender-for-clouds-enhanced-security-features-enabled-and-microsoft-defender-for-endpoint-for-windows)
- [Nouveaux utilisateurs qui n’ont jamais activé l’intégration avec Microsoft Defender pour point de terminaison pour Windows](integration-defender-for-endpoint.md?tabs=linux#new-users-whove-never-enabled-the-integration-with-microsoft-defender-for-endpoint-for-windows)

En savoir plus dans [Protéger vos points de terminaison avec la solution EDR intégrée de Security Center : Microsoft Defender for Endpoint](integration-defender-for-endpoint.md).

### <a name="two-new-recommendations-for-managing-endpoint-protection-solutions-in-preview"></a>Deux nouvelles recommandations pour la gestion des solutions de protection de point de terminaison (en préversion)

Nous avons ajouté deux recommandations **en préversion** pour déployer et gérer les solutions de protection des points de terminaison sur vos machines. Ces deux recommandations incluent la prise en charge des machines virtuelles Azure et des machines connectées aux serveurs Azure Arc.

|Recommandation |Description |Gravité |
|---|---|---|
|[Endpoint Protection doit être installé sur vos machines](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/4fb67663-9ab9-475d-b026-8c544cced439) |Pour protéger vos machines contre les menaces et les vulnérabilités, installez une solution de protection des points de terminaison prise en charge.  <br> <a href="/azure/security-center/endpoint-protection-recommendations-technical">En savoir plus sur la façon dont est évalué Endpoint Protection pour les machines</a><br />(Stratégie associée : [Superviser la solution Endpoint Protection manquante dans Azure Security Center](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2faf6cd1bd-1635-48cb-bde7-5b15693900b9)) |Élevé |
|[Les problèmes d’intégrité de la protection du point de terminaison doivent être résolus sur vos machines](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/37a3689a-818e-4a0e-82ac-b1392b9bb000) |Résolvez les problèmes d’intégrité concernant la protection des points de terminaison de vos machines virtuelles pour les protéger des menaces et des vulnérabilités les plus récentes. Les solutions de protection des points de terminaison Azure Security Center prises en charge sont documentées [ici](./supported-machines-endpoint-solutions-clouds.md?tabs=features-windows). L’évaluation de la protection des points de terminaison est documentée <a href='/azure/security-center/endpoint-protection-recommendations-technical'>ici</a>.<br />(Stratégie associée : [Superviser la solution Endpoint Protection manquante dans Azure Security Center](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2faf6cd1bd-1635-48cb-bde7-5b15693900b9)) |Medium |
|||

> [!NOTE]
> Les recommandations indiquent un intervalle d’actualisation de 8 heures, mais dans certains scénarios, cela peut prendre beaucoup plus de temps. Par exemple, lorsqu’une machine locale est supprimée, Security Center met 24 heures à identifier la suppression. Ensuite, l’évaluation prend jusqu’à 8 heures pour renvoyer les informations. Dans cette situation spécifique, il peut falloir 32 heures pour que la machine soit supprimée de la liste des ressources affectées.
>
> :::image type="content" source="media/release-notes/freshness-interval.png" alt-text="Indicateur d’intervalle d’actualisation pour ces deux nouvelles recommandations de Security Center":::

### <a name="built-in-troubleshooting-and-guidance-for-solving-common-issues"></a>Dépannage et conseils intégrés pour la résolution des problèmes courants

Une nouvelle zone dédiée des pages Security Center dans le portail Azure fournit un ensemble classé et qui ne cesse de s’allonger de documents d’aide autonome pour relever les défis courants liés à Security Center et Azure Defender.

Lorsque vous êtes confronté à un problème ou que vous recherchez des conseils auprès de notre équipe de support technique, la page **Diagnostiquer et résoudre les problèmes** peut vous aider à trouver la solution :

:::image type="content" source="media/release-notes/solve-problems.png" alt-text="Page « Diagnostiquer et résoudre les problèmes » de Security Center":::
 

### <a name="regulatory-compliance-dashboards-azure-audit-reports-released-for-general-availability-ga"></a>Rapports d’audit Azure du tableau de bord de conformité réglementaire publiés pour la mise à la disposition générale

La barre d’outils du tableau de bord de conformité réglementaire offre des rapports de certification Azure et Dynamics pour les normes appliquées à vos abonnements. 

:::image type="content" source="media/release-notes/audit-reports-regulatory-compliance-dashboard.png" alt-text="Barre d’outils du tableau de bord de conformité réglementaire présentant le bouton de génération de rapports d’audit.":::

Vous pouvez sélectionner l’onglet pour les types de rapports appropriés (PCI, SOC, ISO et autres) et utiliser des filtres pour rechercher les rapports spécifiques dont vous avez besoin.

Pour plus d’informations, consultez [Générer des rapports d’état de conformité et des certificats](regulatory-compliance-dashboard.md#generate-compliance-status-reports-and-certificates).

:::image type="content" source="media/release-notes/audit-reports-list-regulatory-compliance-dashboard-ga.png" alt-text="Listes avec onglets de rapports d’audit Azure disponibles. Les onglets sont affichés pour les rapports ISO, SOC, PCI, etc.":::

### <a name="deprecated-recommendation-log-analytics-agent-health-issues-should-be-resolved-on-your-machines"></a>Dépréciation de la recommandation « Les problèmes d’intégrité de l’agent Log Analytics doivent être résolus sur vos machines »

Nous avons constaté que la recommandation **Les problèmes d’intégrité de l’agent Log Analytics doivent être résolus sur vos machines** influe sur les scores de sécurité de manière incohérente avec le focus CSPM (Cloud Security Posture Management) de Security Center. En règle générale, CSPM est associé à l’identification des erreurs de configuration de sécurité. Les problèmes d’intégrité de l’agent n’entrent pas dans cette catégorie de problèmes.

En outre, la recommandation est une anomalie par rapport aux autres agents liés à Security Center : il s’agit du seul agent présentant une recommandation relative aux problèmes d’intégrité.

La recommandation a été dépréciée.

Suite à cette dépréciation, nous avons apporté des modifications mineures aux recommandations relatives à l’installation de l’agent de Log Analytics (**L’agent Log Analytics doit être installé sur...** ).

Il est probable que cette modification influe sur vos scores de sécurité. Pour la plupart des abonnements, nous pensons que cette modification se traduira par un score plus élevé, mais il est possible que les mises à jour apportées à la recommandation d’installation se traduisent par un score moins élevé dans certains cas.

> [!TIP]
> La page [Inventaire des ressources](asset-inventory.md) est également concernée par cette modification, car elle affiche l’état de surveillance des machines (surveillées, non surveillées ou partiellement surveillées, un état qui fait référence à un agent avec problèmes d’intégrité).


### <a name="azure-defender-for-container-registries-now-scans-for-vulnerabilities-in-registries-protected-with-azure-private-link"></a>Azure Defender pour les registres de conteneurs analyse désormais les vulnérabilités dans les registres protégés avec Azure Private Link
Azure Defender pour les registres de conteneurs comprend un analyseur de vulnérabilités pour examiner les images de vos registres Azure Container Registry. Découvrez comment analyser vos registres et corriger les résultats dans [Analyse des vulnérabilités dans les images avec Azure Defender pour les registres de conteneurs](defender-for-container-registries-usage.md).

Pour limiter l’accès à un registre hébergé dans Azure Container Registry, attribuez des adresses IP privées de réseau virtuel aux points de terminaison du registre et utilisez Azure Private Link, comme expliqué dans [Établir une connexion privée à un registre de conteneurs Azure à l’aide d’Azure Private Link](../container-registry/container-registry-private-link.md).

Dans le cadre de nos efforts continus visant à prendre en charge des environnements et des cas d’utilisation supplémentaires, Azure Defender analyse également les registres de conteneur protégés avec [Azure Private Link](../private-link/private-link-overview.md).


### <a name="security-center-can-now-auto-provision-the-azure-policys-guest-configuration-extension-in-preview"></a>Security Center peut désormais approvisionner automatiquement l’extension de configuration d’invité d’Azure Policy (en préversion)
Azure Policy peut vérifier les paramètres à l’intérieur d’une machine, tant pour les machines s’exécutant dans Azure que pour les machines connectées à Arc. La validation est effectuée par le client et l’extension de configuration d’invité. Pour en savoir plus, consultez [Présentation de la fonctionnalité de configuration des invités d'Azure Policy](../governance/policy/concepts/guest-configuration.md).

Avec cette mise à jour, vous pouvez désormais configurer Security Center pour provisionner automatiquement l’extension sur toutes les machines prises en charge. 

:::image type="content" source="media/release-notes/auto-provisioning-guest-configuration.png" alt-text="Activez le déploiement automatique de l’extension de configuration d’invité.":::

Apprenez-en davantage sur le fonctionnement de l’approvisionnement automatique dans [Configurer l’approvisionnement automatique d’agents et d’extensions](enable-data-collection.md).

### <a name="recommendations-to-enable-azure-defender-plans-now-support-enforce"></a>Les recommandations pour activer les plans Azure defender prennent désormais en charge « Appliquer »
Security Center comprend deux fonctionnalités qui permettent de s’assurer que les ressources nouvellement créées sont approvisionnées de manière sécurisée : **appliquer** et **refuser**. Lorsqu’une recommandation offre ces options, vous pouvez vous assurer que vos exigences en matière de sécurité sont satisfaites chaque fois qu’un utilisateur tente de créer une ressource :

- **Refuser** empêche la création de ressources non saines
- **Appliquer** corrige automatiquement les ressources non conformes créées

Avec cette mise à jour, l’option Appliquer est désormais disponible dans les recommandations pour activer les plans Azure Defender (comme **Azure Defender pour App Service doit être activé**, **Azure Defender pour Key Vault doit être activé**, **Azure Defender pour Stockage doit être activé**).

Pour plus d’informations sur ces options, consultez [Empêcher des configurations incorrectes à l’aide des recommandations Appliquer/Refuser](prevent-misconfigurations.md).

### <a name="csv-exports-of-recommendation-data-now-limited-to-20-mb"></a>Exportations CSV de données de recommandation désormais limitées à 20 Mo

Nous créons une limite de 20 Mo lors de l’exportation des données de recommandations Security Center .

:::image type="content" source="media/upcoming-changes/download-csv-report.png" alt-text="Bouton « Télécharger le rapport CSV » de Security Center pour exporter les données de recommandation.":::

S’il vous faut exporter de plus grands volumes de données, utilisez les filtres disponibles avant sélection ou sélectionnez des sous-ensembles de vos abonnements et téléchargez les données par lots.

:::image type="content" source="media/upcoming-changes/filter-subscriptions.png" alt-text="Filtrage des abonnements dans le portail Azure.":::

Apprenez-en davantage sur [l’exécution d’une exportation CSV de vos recommandations de sécurité](continuous-export.md#manual-one-time-export-of-alerts-and-recommendations).



### <a name="recommendations-page-now-includes-multiple-views"></a>La page des recommandations compte désormais plusieurs vues

La page des recommandations compte désormais deux onglets vous permettant d’afficher les recommandations relatives à vos ressources :

- **Recommandations relatives aux scores de sécurité** : utilisez cet onglet pour afficher la liste des recommandations regroupées par contrôle de sécurité. Pour plus d’informations sur ces contrôles, consultez [Contrôles de sécurité et recommandations](secure-score-security-controls.md#security-controls-and-their-recommendations).
- **Toutes les recommandations** : utilisez cet onglet pour afficher la liste des recommandations sous forme de liste simple. Cet onglet permet également de comprendre l’initiative (y compris les normes de conformité réglementaire) qui a généré la recommandation. Pour plus d’informations sur les initiatives et leurs relations avec les recommandations, consultez [Présentation des stratégies de sécurité, des initiatives et des recommandations](security-policy-concept.md).

:::image type="content" source="media/release-notes/recommendations-tabs.png" alt-text="Onglets pour modifier l’affichage de la liste des recommandations dans Azure Security Center.":::

## <a name="july-2021"></a>Juillet 2021

Les mises à jour du mois de juillet incluent :

- [Le connecteur Azure Sentinel comprend désormais une synchronisation d’alerte bidirectionnelle facultative (en préversion)](#azure-sentinel-connector-now-includes-optional-bi-directional-alert-synchronization-in-preview)
- [Réorganisation logique d’Azure Defender pour les alertes Gestionnaire des ressources](#logical-reorganization-of-azure-defender-for-resource-manager-alerts)                                           
- [Améliorations apportées à la recommandation pour activer Azure Disk Encryption (ADE)](#enhancements-to-recommendation-to-enable-azure-disk-encryption-ade)                                     
- [Exportation continue du score de sécurité et des données de conformité réglementaire publiées pour la disponibilité générale (GA)](#continuous-export-of-secure-score-and-regulatory-compliance-data-released-for-general-availability-ga)
- [Les automatisations de workflow peuvent être déclenchées par des modifications apportées aux évaluations de conformité réglementaire (disponibilité générale)](#workflow-automations-can-be-triggered-by-changes-to-regulatory-compliance-assessments-ga)
- [Le champ API Évaluations « FirstEvaluationDate » et « StatusChangeDate » est désormais disponible dans les schémas d’espace de travail et les applications logiques](#assessments-api-field-firstevaluationdate-and-statuschangedate-now-available-in-workspace-schemas-and-logic-apps)
- [Modèle de classeur « conformité au fil du temps » ajouté à la Galerie de classeurs Azure Monitor](#compliance-over-time-workbook-template-added-to-azure-monitor-workbooks-gallery)

### <a name="azure-sentinel-connector-now-includes-optional-bi-directional-alert-synchronization-in-preview"></a>Le connecteur Azure Sentinel comprend désormais une synchronisation d’alerte bidirectionnelle facultative (en préversion)

Security Center s’intègre en mode natif avec [Azure Sentinel](../sentinel/index.yml), la solution SIEM et SOAR cloud native d’Azure. 

Azure Sentinel comprend des connecteurs intégrés pour Azure Security Center au niveau de l’abonnement et du locataire. Pour plus d’informations, consultez [Diffuser en continu des alertes vers Azure Sentinel](export-to-siem.md#stream-alerts-to-microsoft-sentinel).

Lorsque vous connectez Azure Defender à Azure Sentinel, l’état des alertes Azure Defender qui sont ingérées dans Azure Sentinel est synchronisé entre les deux services. Ainsi, par exemple, lorsqu’une alerte est fermée dans Azure Defender, cette alerte s’affiche également comme étant fermée dans Azure Sentinel. La modification de l’état d’une alerte dans Azure Defender n’affecte pas l’état des **incidents** Azure Sentinel qui contiennent l’alerte Azure Sentinel synchronisée, uniquement celui de l’alerte synchronisée.

L’activation de cette fonctionnalité d’évaluation, **synchronisation d’alerte bidirectionnelle**, synchronise automatiquement l’état des alertes Azure Defender d’origine avec les incidents Azure Sentinel qui contiennent les copies de ces alertes Azure Defender. Ainsi, par exemple, lorsqu’un incident Azure Sentinel contenant une alerte Azure Defender est fermé, Azure Defender ferme automatiquement l’alerte d’origine correspondante.

Apprenez-en davantage dans [Connecter les alertes Azure Defender à partir d’Azure Security Center](../sentinel/connect-azure-security-center.md).

### <a name="logical-reorganization-of-azure-defender-for-resource-manager-alerts"></a>Réorganisation logique d’Azure Defender pour les alertes Gestionnaire des ressources

Les alertes énumérées ci-dessous ont été fournies dans le cadre du plan [Azure Defender pour Resource Manager](defender-for-resource-manager-introduction.md).

Dans le cadre d’une réorganisation logique de certains des plans Azure Defender, nous avons déplacé certaines alertes d’**Azure Defender pour Resource Manager** vers **Azure Defender pour les serveurs**.

Les alertes sont organisées en fonction de deux principes essentiels :

- Les alertes qui fournissent une protection de plan de contrôle, sur de nombreux types de ressources Azure, font partie d’Azure Defender pour Resource Manager
- Les alertes qui protègent des charges de travail spécifiques font partie du plan Azure Defender associé à la charge de travail correspondante.

Voici les alertes qui faisaient partie d’Azure Defender pour Resource Manager, et qui, suite à cette modification, font désormais partie d’Azure Defender pour les serveurs :

- ARM_AmBroadFilesExclusion
- ARM_AmDisablementAndCodeExecution
- ARM_AmDisablement
- ARM_AmFileExclusionAndCodeExecution
- ARM_AmTempFileExclusionAndCodeExecution
- ARM_AmTempFileExclusion
- ARM_AmRealtimeProtectionDisabled
- ARM_AmTempRealtimeProtectionDisablement
- ARM_AmRealtimeProtectionDisablementAndCodeExec
- ARM_AmMalwareCampaignRelatedExclusion
- ARM_AmTemporarilyDisablement
- ARM_UnusualAmFileExclusion
- ARM_CustomScriptExtensionSuspiciousCmd
- ARM_CustomScriptExtensionSuspiciousEntryPoint
- ARM_CustomScriptExtensionSuspiciousPayload
- ARM_CustomScriptExtensionSuspiciousFailure
- ARM_CustomScriptExtensionUnusualDeletion
- ARM_CustomScriptExtensionUnusualExecution
- ARM_VMAccessUnusualConfigReset
- ARM_VMAccessUnusualPasswordReset
- ARM_VMAccessUnusualSSHReset

Apprenez-en davantage sur les plans [Azure Defender pour Resource Manager](defender-for-resource-manager-introduction.md) et [Azure Defender pour les serveurs](defender-for-servers-introduction.md).


### <a name="enhancements-to-recommendation-to-enable-azure-disk-encryption-ade"></a>Améliorations apportées à la recommandation pour activer Azure Disk Encryption (ADE)

Suite aux commentaires des utilisateurs, nous avons renommé la recommandation **Le chiffrement de disque doit être appliqué sur les machines virtuelles**.

La nouvelle recommandation utilise le même ID d’évaluation et est appelée **Les machines virtuelles doivent chiffrer les disques temporaires, les caches et les flux de données entre les ressources de calcul et de stockage**.

La description a également été mise à jour pour mieux expliquer l’objectif de cette recommandation de sécurisation renforcée :

| Recommandation                                                                                               | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             | Gravité |
|--------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:--------:|
| **Les machines virtuelles doivent chiffrer les disques temporaires, les caches et les flux de données entre les ressources de calcul et de stockage** | Par défaut, le système d’exploitation et les disques de données d’une machine virtuelle sont chiffrés au repos à l’aide de clés gérées par la plateforme. Les disques temporaires et les caches de données ne sont pas chiffrés, de même que les données lors de leur transmission entre des ressources de calcul et de stockage. Pour une comparaison des différentes technologies de chiffrement de disque dans Azure, consultez https://aka.ms/diskencryptioncomparison.<br>Utilisez Azure Disk Encryption pour chiffrer toutes ces données. Ignorez cette recommandation si : (1) vous utilisez la fonctionnalité de chiffrement sur l’hôte, ou (2) le chiffrement côté serveur sur Managed Disks répond à vos besoins en matière de sécurité. Découvrez-en plus sur le chiffrement côté serveur du Stockage sur disque Azure. | Élevé     |
|                                                                                                              |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |          |


### <a name="continuous-export-of-secure-score-and-regulatory-compliance-data-released-for-general-availability-ga"></a>Exportation continue du score de sécurité et des données de conformité aux réglementations publiées pour la disponibilité générale (GA)

L'[exportation continue](continuous-export.md) fournit le mécanisme d’exportation de vos alertes de sécurité et des recommandations de suivi avec d’autres outils de surveillance dans votre environnement.

Lorsque vous configurez l’exportation continue, vous configurez les éléments exportés ainsi que leur emplacement. Apprenez-en davantage dans la [présentation de l’exportation continue](continuous-export.md).

Nous avons amélioré et développé cette fonctionnalité au fil du temps :

- En novembre 2020, nous avons ajouté l’option **en préversion** pour diffuser les modifications apportées à votre **score de sécurité**.<br/>Pour plus d’informations, consultez [Le score de sécurité est désormais disponible dans l’exportation continue (préversion)](release-notes-archive.md#secure-score-is-now-available-in-continuous-export-preview).

- En décembre 2020, nous avons ajouté l’option **en préversion** pour transmettre en continu les modifications apportées à vos **données d’évaluation de conformité réglementaire**.<br/>Pour plus d’informations, consultez [L’exportation continue permet d’obtenir de nouveaux types de données (préversion)](release-notes-archive.md#continuous-export-gets-new-data-types-and-improved-deployifnotexist-policies).

Avec cette mise à jour, ces deux options sont en disponibilité générale (GA). 


### <a name="workflow-automations-can-be-triggered-by-changes-to-regulatory-compliance-assessments-ga"></a>Les automatisations de workflow peuvent être déclenchées par des modifications apportées aux évaluations de conformité réglementaire (disponibilité générale)

En février 2021, nous avons ajouté un troisième type de données **en préversion** aux options de déclencheur de vos automatisations de workflow : les modifications apportées aux évaluations de conformité réglementaire. Pour plus d’informations, consultez [Les automatisations de workflow peuvent être déclenchées par des modifications apportées aux évaluations de conformité réglementaire](release-notes-archive.md#workflow-automations-can-be-triggered-by-changes-to-regulatory-compliance-assessments-in-preview).

Avec cette mise à jour, cette option de déclencheur est disponible en disponibilité générale.

Découvrez comment utiliser les outils d’automatisation des workflows dans [Automatiser les réponses aux déclencheurs Security Center](workflow-automation.md).

:::image type="content" source="media/release-notes/regulatory-compliance-triggers-workflow-automation.png" alt-text="Utilisation des modifications apportées aux évaluations de conformité réglementaire pour déclencher une automatisation de workflow." lightbox="media/release-notes/regulatory-compliance-triggers-workflow-automation.png":::

### <a name="assessments-api-field-firstevaluationdate-and-statuschangedate-now-available-in-workspace-schemas-and-logic-apps"></a>Le champ d’API d’évaluation « FirstEvaluationDate » et « StatusChangeDate » est désormais disponible dans les schémas d’espace de travail et les applications logiques

En mai 2021, nous avons mis à jour l’API Évaluation avec deux nouveaux champs, **FirstEvaluationDate** et **StatusChangeDate**. Pour plus d’informations, consultez [Développement de l’API Évaluations avec deux nouveaux champs](release-notes-archive.md#assessments-api-expanded-with-two-new-fields).

Ces champs étaient accessibles via l’API REST, Azure Resource Graph, l’exportation continue et les exportations CSV.

Avec cette modification, nous mettons à disposition les informations dans le schéma d’espace de travail Log Analytics et à partir d’applications logiques.


### <a name="compliance-over-time-workbook-template-added-to-azure-monitor-workbooks-gallery"></a>Modèle de classeur « conformité dans le temps » ajouté à la Galerie de classeurs Azure Monitor

En mars, nous avons annoncé l’expérience des classeurs Azure Monitor intégrés dans Security Center (voir [Classeurs Azure Monitor intégrés dans Security Center et trois modèles fournis](release-notes-archive.md#azure-monitor-workbooks-integrated-into-security-center-and-three-templates-provided)).

La version initiale comprenait trois modèles pour générer des rapports dynamiques et visuels relatifs à la posture de sécurité de votre organisation.

Nous avons maintenant ajouté un classeur dédié au suivi de la conformité d’un abonnement avec les normes réglementaires ou sectorielles qui s’y appliquent. 

Apprenez-en davantage sur l’utilisation de ces rapports ou la création de vos propres rapports dans [Créer des rapports interactifs et riches de données Security Center](custom-dashboards-azure-workbooks.md).

:::image type="content" source="media/custom-dashboards-azure-workbooks/compliance-over-time-details.png" alt-text="Classeur de conformité de Azure Security Center au fil du temps":::


## <a name="june-2021"></a>Juin 2021

Les mises à jour du mois de juin incluent :

- [Nouvelle alerte pour Azure Defender pour Key Vault](#new-alert-for-azure-defender-for-key-vault)
- [Recommandations pour chiffrer avec les clés gérées par le client (clés CMK) désactivées par défaut](#recommendations-to-encrypt-with-customer-managed-keys-cmks-disabled-by-default)
- [Préfixe des alertes Kubernetes « AKS_ » remplacé par « K8S_ »](#prefix-for-kubernetes-alerts-changed-from-aks_-to-k8s_)
- [Deux recommandations du contrôle de sécurité « Appliquer les mises à jour système » dépréciées](#deprecated-two-recommendations-from-apply-system-updates-security-control)


### <a name="new-alert-for-azure-defender-for-key-vault"></a>Nouvelle alerte pour Azure Defender pour Key Vault

Pour développer les protections contre les menaces fournies par Azure Defender pour Key Vault, nous avons ajouté l’alerte suivante :

| Alerte (type d’alerte)                                                                 | Description                                                                                                                                                                                                                                                                                                                                                      | Tactique MITRE | severity |
|------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:--------------------------------------------:|----------|
| Accès à partir d’une adresse IP suspecte à un coffre de clés<br>(KV_SuspiciousIPAccess)  | Un coffre de clés a fait l’objet d’un accès réussi par une IP qui a été identifiée par Microsoft Threat Intelligence comme adresse IP suspecte. Cela peut indiquer que votre infrastructure a été compromise. Nous recommandons d’investiguer plus en profondeur. En savoir plus sur les [capacités de renseignement sur les menaces de Microsoft](https://go.microsoft.com/fwlink/?linkid=2128684). | Accès aux informations d’identification                            | Moyenne   |
|||

Pour plus d'informations, consultez les pages suivantes :
- [Présentation d’Azure Defender pour Key Vault](defender-for-resource-manager-introduction.md)
- [Répondre aux alertes Azure Defender pour Key Vault](defender-for-key-vault-usage.md)
- [Liste des alertes fournies par Azure Defender pour Key Vault](alerts-reference.md#alerts-azurekv)


### <a name="recommendations-to-encrypt-with-customer-managed-keys-cmks-disabled-by-default"></a>Recommandations pour chiffrer avec les clés gérées par le client (clés CMK) désactivées par défaut

Security Center comprend plusieurs recommandations pour chiffrer les données au repos avec des clés gérées par le client, telles que :

- Les registres de conteneurs doivent être chiffrés avec une clé gérée par le client (CMK)
- Les comptes Azure Cosmos DB doivent utiliser des clés gérées par le client pour chiffrer les données au repos
- Les espaces de travail Azure Machine Learning doivent être chiffrés avec une clé gérée par le client (CMK)

Dans Azure, les données sont chiffrées automatiquement à l’aide de clés gérées par la plateforme. Par conséquent, l’utilisation de clés gérées par le client doit être appliquée uniquement lorsque cela est nécessaire pour la conformité à une stratégie spécifique que votre organisation choisit de mettre en vigueur.

Avec cette modification, les recommandations d’utilisation de clés CMK sont désormais **désactivées par défaut**. Si cela est pertinent pour votre organisation, vous pouvez les activer en remplaçant le paramètre d’*effet* de la stratégie de sécurité correspondante par **AuditIfNotExists** ou **Appliquer**. Apprenez-en davantage dans [Activer une stratégie de sécurité](tutorial-security-policy.md#enable-a-security-policy).

Cette modification est reflétée dans le nom de la recommandation avec un nouveau préfixe, **[Activer si nécessaire]** , comme indiqué dans les exemples suivants :

- [Activer si nécessaire] Les comptes de stockage doivent utiliser une clé gérée par le client pour chiffrer les données au repos
- [Activer si nécessaire] Les registres de conteneurs doivent être chiffrés avec une clé gérée par le client (CMK)
- [Activer si nécessaire] Les comptes Azure Cosmos DB doivent utiliser des clés gérées par le client pour chiffrer les données au repos

:::image type="content" source="media/upcoming-changes/customer-managed-keys-disabled.png" alt-text="Les recommandations concernant les clés CMK de Security Center seront désactivées par défaut." lightbox="media/upcoming-changes/customer-managed-keys-disabled.png":::


### <a name="prefix-for-kubernetes-alerts-changed-from-aks_-to-k8s_"></a>Préfixe des alertes Kubernetes « AKS_ » remplacé par « K8S_ »

Azure Defender pour Kubernetes s’est récemment développé pour protéger les clusters Kubernetes hébergés localement et dans les environnements multiclouds. Découvrez-en plus dans [Utiliser Azure Defender pour Kubernetes afin de protéger les déploiements Kubernetes hybrides et multiclouds (en préversion)](release-notes-archive.md#use-azure-defender-for-kubernetes-to-protect-hybrid-and-multi-cloud-kubernetes-deployments-in-preview).

Pour signifier que les alertes de sécurité fournies par Azure Defender pour Kubernetes ne sont plus limitées aux clusters sur Azure Kubernetes Service, nous avons remplacé le préfixe des types d’alertes « AKS_ » par « K8S_ ». Le cas échéant, les noms et les descriptions ont également été mis à jour. Par exemple, cette alerte :

|Alerte (type d’alerte)|Description|
|----|----|
|Détection d’un outil de test d’intrusion Kubernetes<br>(**AKS** _PenTestToolsKubeHunter)|L’analyse du journal d’audit de Kubernetes a détecté l’utilisation de l’outil de test d’intrusion Kubernetes dans le cluster **AKS**. Bien que ce comportement puisse être légitime, des attaquants peuvent utiliser ces outils publics à des fins malveillantes.
|||

a été remplacée par :

|Alerte (type d’alerte)|Description|
|----|----|
|Détection d’un outil de test d’intrusion Kubernetes<br>(**K8S** _PenTestToolsKubeHunter)|L’analyse du journal d’audit de Kubernetes a détecté l’utilisation de l’outil de test d’intrusion Kubernetes dans le cluster **Kubernetes**. Bien que ce comportement puisse être légitime, des attaquants peuvent utiliser ces outils publics à des fins malveillantes.|
|||

Toutes les règles de suppression qui font référence à des alertes commençant par « AKS_ » ont été automatiquement converties. Si vous avez configuré des exportations SIEM ou des scripts d’automatisation personnalisés qui font référence à des alertes Kubernetes par type d’alerte, vous avez besoin de les mettre à jour avec les nouveaux types d’alerte.

Pour obtenir la liste complète des alertes Kubernetes, consultez [Alertes pour les clusters Kubernetes](alerts-reference.md#alerts-k8scluster).

### <a name="deprecated-two-recommendations-from-apply-system-updates-security-control"></a>Deux recommandations du contrôle de sécurité « Appliquer les mises à jour système » dépréciées

Les deux recommandations suivantes ont été dépréciées :

- **La version du système d'exploitation (SE) doit être mise à jour pour vos rôles service cloud**. Par défaut, Azure met régulièrement à jour votre SE invité vers la dernière image prise en charge d’un produit de la famille de SE que vous avez spécifiée dans votre configuration de service (.cscfg), tel que Windows Server 2016.
- **Les services Kubernetes doivent être mis à niveau vers une version Kubernetes non vulnérable**. Les évaluations de cette recommandation ne sont pas aussi étendues que nous le voulions. Nous prévoyons de remplacer la recommandation par une version améliorée qui est mieux adaptée à vos besoins en matière de sécurité.
