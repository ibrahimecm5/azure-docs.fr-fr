---
title: Vue d’ensemble des serveurs avec Azure Arc
description: Découvrez comment utiliser les serveurs avec Azure Arc afin de gérer les serveurs hébergés en dehors d’Azure comme une ressource Azure.
ms.date: 09/30/2021
ms.topic: overview
ms.openlocfilehash: c390cbbb6f08f4f9082b0764125ab9a14407de95
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132287264"
---
# <a name="what-is-azure-arc-enabled-servers"></a>Qu’est-ce qu’un serveur avec Azure Arc ?

Les serveurs avec Azure Arc vous permettent de gérer vos machines virtuelles et vos serveurs physiques Windows et Linux hébergés *en dehors* d’Azure sur votre réseau d’entreprise ou un autre fournisseur de cloud. Cette expérience de gestion est conçue pour être cohérente avec la manière dont vous gérez les machines virtuelles Azure natives. Quand une machine hybride est connectée à Azure, elle devient une machine connectée et est traitée comme une ressource dans Azure. Chaque machine connectée a un ID de ressource qui permet d’inclure la machine dans un groupe de ressources. Vous bénéficiez désormais de constructions Azure standard, comme Azure Policy et l’application d’étiquettes. Les fournisseurs de services qui gèrent l’infrastructure locale d’un client peuvent gérer ses machines hybrides (comme ils le font déjà avec les ressources Azure natives) dans différents environnements clients avec [Azure Lighthouse](../../lighthouse/how-to/manage-hybrid-infrastructure-arc.md).

Pour bénéficier de cette expérience avec vos machines hybrides, vous devez installer l’agent Azure Connected Machine sur chaque machine. Cet agent ne fournit aucune autre fonctionnalité et ne remplace pas l’agent Azure [Log Analytics](../../azure-monitor/agents/log-analytics-agent.md). L’agent Log Analytics pour Windows et Linux est requis dans les situations suivantes :

* Vous souhaitez superviser de manière proactive le système d’exploitation et les charges de travail en cours d’exécution sur la machine.
* Vous le gérez avec des runbooks Automation ou des solutions comme Update Management.
* Utilisez d’autres services Azure comme [Microsoft Defender pour le cloud](../../security-center/security-center-introduction.md).

## <a name="supported-cloud-operations"></a>Opérations cloud prises en charge 

Quand vous connectez votre machine à des serveurs avec Azure Arc, elle vous permet d’effectuer les fonctions opérationnelles suivantes, comme décrit dans le tableau ci-dessous.

|Fonction d’opérations |Description | 
|--------------------|------------|
|**Gouvernance** ||
| Azure Policy |Attribuez des [configurations d’invité Azure Policy](../../governance/policy/concepts/guest-configuration.md) pour auditer les paramètres à l’intérieur de la machine. Pour comprendre le coût de l’utilisation de stratégies Guest Configuration dans Azure Policy avec des serveurs Arc, consultez le [guide des tarifs](https://azure.microsoft.com/pricing/details/azure-policy/) d’Azure Policy.|
|**Protéger** ||
| Microsoft Defender pour le cloud | Protégez les serveurs non-Azure avec [Microsoft Defender pour point de terminaison](/microsoft-365/security/defender-endpoint), compris dans [Microsoft Defender pour le cloud](../../security-center/defender-for-servers-introduction.md), afin de détecter les menaces, gérer les vulnérabilités et monitorer de manière proactive les menaces de sécurité potentielles. Microsoft Defender pour le cloud présente les alertes et les suggestions de correction en fonction des menaces détectées. |
| Microsoft Sentinel | Les machines connectées aux serveurs avec Arc peuvent être [configurées avec Microsoft Sentinel](scenario-onboard-azure-sentinel.md) pour collecter des événements liés à la sécurité et les mettre en corrélation avec d’autres sources de données. |
|**Configurer** ||
| Azure Automation |Automatisez les tâches de gestion fréquentes et chronophages à l’aide de [runbooks](../../automation/automation-runbook-execution.md)PowerShell et Python.<br> Évaluez les changements de configuration relatifs aux logiciels installés, aux services Microsoft, au registre et aux fichiers Windows ainsi qu’aux démons Linux avec [Suivi des modifications et inventaire](../../automation/change-tracking/overview.md).<br> Utilisez la fonctionnalité [Update Management](../../automation/update-management/overview.md) pour gérer les mises à jour du système d’exploitation de vos serveurs Windows et Linux. |
| Azure Automanage (préversion) | Automatisez l’intégration et la configuration d’un ensemble de services Azure quand vous utilisez [Automanage pour les machines pour les serveurs avec Arc](../../automanage/automanage-arc.md). |
| Extensions de machine virtuelle | Fournit des tâches d’automatisation et de configuration post-déploiement avec des [extensions de machines virtuelles pour les serveurs avec Arc](manage-vm-extensions.md) prises en charge pour votre machine Windows ou Linux non-Azure. |
|**Surveiller**|
| Azure Monitor | Supervisez les performances du système d’exploitation invité de la machine connectée, et découvrez les composants de l’application pour superviser leurs processus et dépendances avec d’autres ressources à l’aide de [VM Insights](../../azure-monitor/vm/vminsights-overview.md). Collectez d’autres données de journal, telles que des données de performances et des événements, à partir du système d’exploitation ou des charges de travail en cours d’exécution sur la machine avec l’[agent Log Analytics](../../azure-monitor/agents/agents-overview.md#log-analytics-agent). Ces données sont stockées dans un [espace de travail Log Analytics](../../azure-monitor/logs/design-logs-deployment.md). |

> [!NOTE]
> À ce stade, l’activation d’Azure Automation Update Management, directement à partir d’un serveur avec Azure Arc, n’est pas prise en charge. Pour connaître les conditions requises et la façon de l’activer pour votre serveur, consultez [Activer Update Management à partir de votre compte Automation](../../automation/update-management/enable-from-automation-account.md).

Les données de journal collectées et stockées dans un espace de travail Log Analytics à partir de la machine hybride contiennent désormais des propriétés spécifiques de la machine, telles qu’un ID de ressource, pour prendre en charge l’accès aux journaux [dans le contexte de la ressource](../../azure-monitor/logs/design-logs-deployment.md#access-mode).

[!INCLUDE [azure-lighthouse-supported-service](../../../includes/azure-lighthouse-supported-service.md)]

Pour en savoir plus sur la façon dont les serveurs avec Azure Arc peuvent être utilisés pour implémenter les services de supervision, de sécurité et de mise à jour Azure dans des environnements hybrides et multiclouds, visionnez la vidéo suivante.

> [!VIDEO https://www.youtube.com/embed/mJnmXBrU1ao]

## <a name="supported-regions"></a>Régions prises en charge

Pour obtenir la liste définitive des régions prises en charge avec les serveurs avec Azure Arc, consultez la page [Produits Azure par région](https://azure.microsoft.com/global-infrastructure/services/?products=azure-arc).

Dans la plupart des cas, l’emplacement que vous sélectionnez au moment de créer le script d’installation doit être la région Azure géographiquement la plus proche de l’emplacement de votre ordinateur. Les données au repos sont stockées dans la zone géographique Azure englobant la région que vous spécifiez, ce qui peut aussi affecter votre choix de région si vous avez des exigences concernant la résidence des données. Si la région Azure à laquelle votre machine se connecte subit une panne, la machine connectée n’est pas affectée, mais les opérations de gestion effectuées avec Azure risquent de ne pas aboutir. En cas de panne régionale et si vous avez plusieurs emplacements qui prennent en charge un service géographiquement redondant, l’idéal est de connecter les machines de chaque emplacement à une région Azure distincte.

Les métadonnées suivantes concernant la machine connectée sont collectées et stockées dans la région où la ressource machine Azure Arc est configurée :

- Nom et version du système d’exploitation
- Nom de l'ordinateur
- Nom de domaine complet (FQDN) de l’ordinateur
- Version de l’agent Machine connectée

Par exemple, si la machine est inscrite auprès d’Azure Arc dans la région USA Est, ces données sont stockées dans la région USA.

## <a name="supported-environments"></a>Environnements pris en charge

Les serveurs avec Azure Arc prennent en charge la gestion des serveurs physiques et des machines virtuelles hébergés *en dehors* d’Azure. Pour plus d’informations sur les environnements de cloud hybride hébergeant des machines virtuelles qui sont pris en charge, consultez [Prérequis de l’agent Connected Machine](agent-overview.md#supported-environments).

> [!NOTE]
> Les serveurs avec Azure Arc ne sont pas conçus ni pris en charge pour permettre la gestion des machines virtuelles s’exécutant dans Azure.

## <a name="agent-status"></a>État de l’agent

L’agent Connected Machine envoie des messages de pulsation au service de façon régulière (toutes les 5 minutes). Si le service cesse de recevoir ces messages de pulsation d’une machine, cette machine est considérée comme étant hors connexion, et l’état dans le portail est automatiquement remplacé par **Déconnectée** au bout de 15 à 30 minutes. À la prochaine réception d’un message de pulsation de l’agent Connected Machine, son état devient automatiquement **Connecté**.

## <a name="service-limits"></a>Limites du service

Les serveurs avec Azure Arc ont une limite pour le nombre d’instances pouvant être créées dans chaque groupe de ressources. Ils n’ont pas de limite au niveau de l’abonnement ou du service. Pour en savoir plus sur les limites de type de ressource qui existent, consultez l’article [Limite des instances de ressource](../../azure-resource-manager/management/resources-without-resource-group-limit.md#microsofthybridcompute).

## <a name="next-steps"></a>Étapes suivantes

* Avant d’évaluer ou d’activer des serveurs avec Azure Arc sur plusieurs machines hybrides, consultez [Vue d’ensemble de l’agent Machine connectée](agent-overview.md) pour en savoir plus sur les spécifications et les détails techniques relatifs à l’agent, ainsi que sur les méthodes de déploiement.

* Consultez le [Guide de planification et de déploiement](plan-at-scale-deployment.md) pour planifier le déploiement de serveurs avec Azure Arc à n’importe quelle échelle et implémenter la gestion et la supervision centralisées.
