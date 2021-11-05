---
title: Notes de publication pour Microsoft Defender pour le cloud
description: Description des nouveautés et des modifications apportées à Microsoft Defender pour le cloud
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: reference
ms.date: 10/17/2021
ms.author: memildin
ms.custom: ignite-fall-2021
ms.openlocfilehash: a287709d7fb84bece1a0b8777f13297c963f3ace
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131004667"
---
# <a name="whats-new-in-microsoft-defender-for-cloud"></a>Quelles sont les nouveautés de Microsoft Defender pour le cloud ?

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Defender pour le cloud fait l’objet d’un développement actif et bénéficie d’améliorations en permanence. Pour vous tenir au courant des développements les plus récents, cette page fournit des informations sur les nouvelles fonctionnalités, les correctifs de bogues et les fonctionnalités déconseillées.

Cette page étant mise à jour fréquemment, nous vous invitons donc à la consulter souvent. 

Pour en savoir plus sur les changements *planifiés* qui seront bientôt disponibles dans Defender pour le cloud, consultez [Changements importants à venir dans Microsoft Defender pour le cloud](upcoming-changes.md). 

> [!TIP]
> Si vous recherchez des éléments datant de plus de six mois, vous les trouverez dans l’[Archive des nouveautés de Microsoft Defender pour le cloud](release-notes-archive.md).


## <a name="october-2021"></a>Octobre 2021

Les mises à jour d’octobre sont les suivantes :

- [Ajout de Gestion des menaces et des vulnérabilités de Microsoft comme solution d’évaluation des vulnérabilités (en préversion)](#microsoft-threat-and-vulnerability-management-added-as-vulnerability-assessment-solution-in-preview)
- [Les solutions d’évaluation des vulnérabilités peuvent désormais être activées automatiquement (en préversion)](#vulnerability-assessment-solutions-can-now-be-auto-enabled-in-preview)
- [Ajout de filtres d’inventaire logiciel à l’inventaire des ressources (en préversion)](#software-inventory-filters-added-to-asset-inventory-in-preview)
- [Modification du préfixe de certains types d’alerte de « ARM_ » à « VM_ »](#changed-prefix-of-some-alert-types-from-arm_-to-vm_)
- [Modifications apportées à la logique d’une recommandation de sécurité pour les clusters Kubernetes](#changes-to-the-logic-of-a-security-recommendation-for-kubernetes-clusters)
- [Les pages de détails sur les recommandations affichent maintenant les recommandations associées](#recommendations-details-pages-now-show-related-recommendations)


### <a name="microsoft-threat-and-vulnerability-management-added-as-vulnerability-assessment-solution-in-preview"></a>Ajout de Gestion des menaces et des vulnérabilités de Microsoft comme solution d’évaluation des vulnérabilités (en préversion)

Nous avons étendu l’intégration entre [Azure defender pour les serveurs](defender-for-servers-introduction.md) et Microsoft Defender pour point de terminaison afin de prendre en charge une nouvelle évaluation des vulnérabilités fournie pour vos machines : [Gestion des menaces et des vulnérabilités de Microsoft](/microsoft-365/security/defender-endpoint/next-gen-threat-and-vuln-mgt). 

Utilisez la **gestion des menaces et des vulnérabilités** pour découvrir les vulnérabilités et les erreurs de configuration en quasi-temps réel grâce à l’[intégration à Microsoft Defender pour point de terminaison](integration-defender-for-endpoint.md), et sans avoir besoin d’agents supplémentaires ou d’analyses périodiques. Gestion des menaces et des vulnérabilités hiérarchise les vulnérabilités en fonction du paysage des menaces et des détections dans votre organisation.

Utilisez la recommandation de sécurité « [Une solution d’évaluation des vulnérabilités doit être activée sur vos machines virtuelles](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/ffff0522-1e88-47fc-8382-2a80ba848f5d) » pour faire apparaître les vulnérabilités détectées par Gestion des menaces et des vulnérabilités pour vos [machines prises en charge](/microsoft-365/security/defender-endpoint/tvm-supported-os?view=o365-worldwide&preserve-view=true). 

Pour faire apparaître automatiquement les vulnérabilités sur les machines existantes et nouvelles sans avoir besoin de corriger manuellement la recommandation, consultez [Les solutions d’évaluation des vulnérabilités peuvent désormais être activées automatiquement (en préversion)](#vulnerability-assessment-solutions-can-now-be-auto-enabled-in-preview).

En savoir plus dans [Investiguer les faiblesses avec la gestion des menaces et des vulnérabilités de Microsoft Defender pour point de terminaison](deploy-vulnerability-assessment-tvm.md).

### <a name="vulnerability-assessment-solutions-can-now-be-auto-enabled-in-preview"></a>Les solutions d’évaluation des vulnérabilités peuvent désormais être activées automatiquement (en préversion)

La page d’approvisionnement automatique de Security Center comprend désormais l’option permettant d’activer automatiquement une solution d’évaluation des vulnérabilités sur les machines virtuelles Azure et les machines Azure Arc sur des abonnements protégés par [Azure Defender pour les serveurs](defender-for-servers-introduction.md).

En outre, si l’[intégration à Microsoft Defender pour point de terminaison](integration-defender-for-endpoint.md) est activée, vous aurez le choix entre plusieurs solutions d’évaluation des vulnérabilités :

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

Pour clarifier les relations entre les différentes recommandations, nous avons ajouté une zone **Recommendations associées** aux pages de détails de nombreuses recommandations. 

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

Évidemment, Security Center ne peut pas vous avertir des vulnérabilités découvertes, à moins de trouver une solution d’évaluation des vulnérabilités prise en charge.

Par conséquent :

 - Recommandation #1 est une condition préalable à la recommandation #2
 - La recommandation #2 dépend de la recommandation #1

:::image type="content" source="media/release-notes/related-recommendations-solution-not-found.png" alt-text="Capture d’écran de la recommandation de déploiement de la solution d’évaluation des vulnérabilités.":::

:::image type="content" source="media/release-notes/related-recommendations-vulnerabilities-found.png" alt-text="Capture d’écran de la recommandation pour résoudre les vulnérabilités découvertes.":::



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

Avec cette mise à jour, vous pouvez désormais définir Security Center pour approvisionner automatiquement cette extension sur tous les ordinateurs pris en charge. 

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

En mai 2021, nous avons mis à jour l’API Évaluation avec deux nouveaux champs, **FirstEvaluationDate** et **StatusChangeDate**. Pour plus d’informations, consultez [Développement de l’API Évaluations avec deux nouveaux champs](#assessments-api-expanded-with-two-new-fields).

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


## <a name="may-2021"></a>Mai 2021

Les mises à jour du mois de mai incluent :

- [Azure Defender pour DNS et Azure Defender pour Resource Manager en disponibilité générale](#azure-defender-for-dns-and-azure-defender-for-resource-manager-released-for-general-availability-ga)
- [Azure Defender pour les bases de données relationnelles open source en disponibilité générale](#azure-defender-for-open-source-relational-databases-released-for-general-availability-ga)
- [Nouvelles alertes pour Azure Defender pour Resource Manager](#new-alerts-for-azure-defender-for-resource-manager)
- [Analyse des vulnérabilités CI/CD des images conteneur avec les workflows GitHub et Azure Defender (préversion)](#cicd-vulnerability-scanning-of-container-images-with-github-workflows-and-azure-defender-preview)
- [Plus de requêtes Resource Graph disponibles pour certaines recommandations](#more-resource-graph-queries-available-for-some-recommendations)
- [Changement de la gravité de la recommandation de classification des données SQL](#sql-data-classification-recommendation-severity-changed)
- [Nouvelles recommandations pour activer les fonctionnalités de lancement fiable (en préversion)](#new-recommendations-to-enable-trusted-launch-capabilities-in-preview)
- [Nouvelles recommandations pour renforcer les clusters Kubernetes (en préversion)](#new-recommendations-for-hardening-kubernetes-clusters-in-preview)
- [Développement de l’API Évaluations avec deux nouveaux champs](#assessments-api-expanded-with-two-new-fields)
- [L’inventaire des ressources obtient un filtre d’environnement cloud](#asset-inventory-gets-a-cloud-environment-filter)


### <a name="azure-defender-for-dns-and-azure-defender-for-resource-manager-released-for-general-availability-ga"></a>Azure Defender pour DNS et Azure Defender pour Resource Manager en disponibilité générale

Ces deux plans de protection contre les menaces au niveau cloud natif sont désormais en disponibilité générale.

Ces nouvelles protections améliorent nettement votre résilience face aux attaques des acteurs des menaces et accroissent considérablement le nombre de ressources Azure protégées par Azure Defender.

- **Azure Defender pour Ressource Manager** – Supervise automatiquement toutes les opérations de gestion de ressources effectuées dans votre organisation. Pour plus d'informations, consultez les pages suivantes :
    - [Présentation d’Azure Defender pour Resource Manager](defender-for-resource-manager-introduction.md)
    - [Répondre aux alertes Azure Defender pour Resource Manager](defender-for-resource-manager-usage.md)
    - [Liste des alertes fournies par Azure Defender pour Resource Manager](alerts-reference.md#alerts-resourcemanager)

- **Azure Defender pour DNS** – Supervise en continu toutes les requêtes DNS émanant de vos ressources Azure. Pour plus d'informations, consultez les pages suivantes :
    - [Présentation d’Azure Defender pour DNS](defender-for-dns-introduction.md)
    - [Répondre aux alertes Azure Defender pour DNS](defender-for-dns-usage.md)
    - [Liste des alertes fournies par Azure Defender pour DNS](alerts-reference.md#alerts-dns)

Pour simplifier le processus d’activation de ces plans, utilisez les recommandations suivantes :

- **Azure Defender pour Resource Manager doit être activé**
- **Azure Defender pour DNS doit être activé**

> [!NOTE]
> L’activation des plans Azure Defender engendre des frais. Découvrez-en plus sur les détails des prix par région dans la page des tarifs de Security Center : https://aka.ms/pricing-security-center.


### <a name="azure-defender-for-open-source-relational-databases-released-for-general-availability-ga"></a>Azure Defender pour les bases de données relationnelles open source en disponibilité générale

Azure Security Center étend son offre de protection SQL avec un nouveau bundle pour couvrir vos bases de données relationnelles open source :

- **Azure Defender pour serveurs de base de données Azure SQL** : défend vos serveurs Azure SQL natifs
- **Azure Defender pour les serveurs SQL sur machines** : étend les mêmes protections à vos serveurs SQL dans les environnements hybrides, multicloud et locaux
- **Azure Defender pour les bases de données relationnelles open source** : protège vos serveurs uniques Azure Database pour MySQL, pour PostgreSQL et for MariaDB

Azure Defender pour les bases de données relationnelles open source supervise en permanence vos serveurs à la recherche de menaces de sécurité et détecte les activités anormales de base de données indiquant des menaces potentielles envers Azure Database pour MySQL, pour PostgreSQL et for MariaDB. Quelques exemples :

- **Détection précise des attaques par force brute** : Azure Defender pour les bases de données relationnelles open source fournit des informations détaillées sur les attaques par force brute tentées et réussies. Cela vous permet d’investiguer et de répondre avec une meilleure compréhension de la nature et de l’état de l’attaque sur votre environnement.
- **Détection des alertes comportementales** : Azure Defender pour les bases de données relationnelles open source vous avertit des comportements suspects et inattendus sur vos serveurs, tels que les modifications apportées au modèle d’accès à votre base de données.
- **Détection basée sur le renseignement sur les menaces** : Azure Defender utilise le renseignement sur les menaces de Microsoft et la vaste base de connaissances pour exposer les alertes de menace afin que vous puissiez agir sur ces dernières.

Pour en savoir plus, consultez [Présentation d’Azure Defender pour les bases de données relationnelles open source](defender-for-databases-introduction.md).

### <a name="new-alerts-for-azure-defender-for-resource-manager"></a>Nouvelles alertes pour Azure Defender pour Resource Manager

Pour développer les protections contre les menaces fournies par Azure Defender pour Resource Manager, nous avons ajouté les alertes suivantes :

| Alerte (type d’alerte)                                                                                                                                                | Description                                                                                                                                                                                                                                                                                                                                                                                                                              | Tactiques MITRE | severity |
|-------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:-------------------------------------:|----------|
|**Autorisations accordées à un rôle RBAC de manière inhabituelle pour votre environnement Azure (préversion)**<br>(ARM_AnomalousRBACRoleAssignment)|Azure Defender pour Resource Manager a détecté une attribution de rôle RBAC qui est inhabituelle par rapport à d’autres attributions effectuées par le même attributeur / pour le même attributaire / dans votre locataire en raison des anomalies suivantes : heure d’attribution, adresse de l’attributeur, attributeur, méthode d’authentification, entités attribuées, logiciel client utilisé, extension d’attribution. Cette opération a probablement été effectuée par un utilisateur légitime de votre organisation. Elle peut également indiquer qu’un compte de votre organisation a été violé et que l’acteur de menaces tente d’accorder des autorisations à un autre compte d’utilisateur dont il est propriétaire.|Mouvement latéral, évasion de la défense|Moyenne|
|**Rôle personnalisé privilégié créé pour votre abonnement de façon suspecte (préversion)**<br>(ARM_PrivilegedRoleDefinitionCreation)|Azure Defender pour Resource Manager a détecté une création suspecte de définition de rôle personnalisé privilégié dans votre abonnement. Cette opération a probablement été effectuée par un utilisateur légitime de votre organisation. Elle peut également indiquer qu’un compte de votre organisation a été violé et que l’acteur de menaces tente de créer un rôle privilégié qu’il utilisera pour s’évader de la détection.|Mouvement latéral, évasion de la défense|Faible|
|**Opération Azure Resource Manager depuis une adresse IP suspecte (préversion)**<br>(ARM_OperationFromSuspiciousIP)|Azure Defender pour Resource Manager a détecté une opération depuis une adresse IP qui a été marquée comme suspecte dans les flux de renseignement sur les menaces.|Exécution|Moyenne|
|**Opération Azure Resource Manager depuis une adresse IP proxy suspecte (préversion)**<br>(ARM_OperationFromSuspiciousProxyIP)|Azure Defender pour Resource Manager a détecté une opération de gestion des ressources depuis une adresse IP associée à des services proxy comme TOR. Bien que ce comportement puisse être légitime, il est souvent observé dans le cadre d’activités malveillantes, lorsque les acteurs de menaces tentent de dissimuler leur IP source.|Évasion de défense|Moyenne|
||||

Pour plus d'informations, consultez les pages suivantes :
- [Présentation d’Azure Defender pour Resource Manager](defender-for-resource-manager-introduction.md)
- [Répondre aux alertes Azure Defender pour Resource Manager](defender-for-resource-manager-usage.md)
- [Liste des alertes fournies par Azure Defender pour Resource Manager](alerts-reference.md#alerts-resourcemanager)


### <a name="cicd-vulnerability-scanning-of-container-images-with-github-workflows-and-azure-defender-preview"></a>Analyse des vulnérabilités CI/CD des images conteneur avec les workflows GitHub et Azure Defender (préversion)

Azure Defender pour les registres de conteneurs offre désormais aux équipes DevSecOps une observabilité des workflows d’action GitHub.

La nouvelle fonctionnalité d’analyse des vulnérabilités pour les images conteneur, utilisant Trivy, aide vos développeurs à rechercher les vulnérabilités courantes dans leurs images conteneur *avant* d’envoyer les images vers des registres de conteneurs.

Les rapports d’analyse de conteneur sont résumés dans Azure Security Center, ce qui offre aux équipes de sécurité un meilleur insight et une meilleure compréhension de la source des images conteneur vulnérables ainsi que des workflows et dépôts desquels elles proviennent.

Apprenez-en davantage dans [Identifier les images conteneur vulnérables dans vos workflows CI/CD](defender-for-container-registries-cicd.md).

### <a name="more-resource-graph-queries-available-for-some-recommendations"></a>Plus de requêtes Resource Graph disponibles pour certaines recommandations

Toutes les recommandations de Security Center permettent d’afficher les informations relatives à l’état des ressources affectées en utilisant Azure Resource Graph à partir du bouton **Ouvrir une requête**. Pour obtenir tous les détails sur cette fonctionnalité puissante, consultez [Examiner les données de recommandation dans l’Explorateur Azure Resource Graph (ARG)](review-security-recommendations.md#review-recommendation-data-in-azure-resource-graph-explorer-arg).

Security Center comporte des analyseurs de vulnérabilités intégrés pour analyser vos machines virtuelles, vos serveurs SQL et leurs hôtes ainsi que les registres de conteneurs pour les vulnérabilités de sécurité. Les résultats sont retournés sous forme de recommandations avec toutes les découvertes individuelles pour chaque type de ressource rassemblées dans une vue unique. Les recommandations sont les suivantes :

- Les vulnérabilités dans les images Azure Container Registry doivent être corrigées (avec Qualys)
- Les vulnérabilités de vos machines virtuelles doivent être corrigées
- Les résultats des vulnérabilités des bases de données SQL doivent être résolus
- Les résultats des vulnérabilités des serveurs SQL Server sur les machines doivent être résolus

Une fois cette modification apportée, vous pouvez aussi utiliser le bouton **Ouvrir une requête** pour ouvrir la requête qui affiche les résultats de la sécurité.

:::image type="content" source="media/release-notes/open-query-menu-security-findings.png" alt-text="Le bouton Ouvrir une requête propose désormais des options pour une requête plus détaillée présentant les résultats de la sécurité pour les recommandations relatives à l’analyseur de vulnérabilités.":::

Le bouton **Ouvrir une requête** offre des options supplémentaires pour d’autres recommandations, le cas échéant.

Découvrez plus d’informations sur les analyseurs de vulnérabilités de Security Center :

- [Analyseur de vulnérabilités intégré Qualys d’Azure Defender pour les machines Azure et hybrides](deploy-vulnerability-assessment-vm.md)
- [Analyseur d’évaluation des vulnérabilités intégré d’Azure Defender pour les serveurs SQL](defender-for-sql-on-machines-vulnerability-assessment.md)
- [Analyseur d’évaluation des vulnérabilités intégré d’Azure Defender pour les registres de conteneurs](defender-for-container-registries-usage.md)

### <a name="sql-data-classification-recommendation-severity-changed"></a>Changement de la gravité de la recommandation de classification des données SQL

La gravité de la recommandation **Les données sensibles de vos bases de données SQL doivent être classifiées** est passée d’un niveau **élevé** à un niveau **faible**.

Ce changement s’inscrit dans le cadre des changements apportés à cette recommandation annoncés dans [Améliorations apportées à la recommandation de classification des données sensibles dans les bases de données SQL](upcoming-changes.md#enhancements-to-recommendation-to-classify-sensitive-data-in-sql-databases).

### <a name="new-recommendations-to-enable-trusted-launch-capabilities-in-preview"></a>Nouvelles recommandations pour activer les fonctionnalités de lancement fiable (en préversion)

Azure propose le lancement fiable pour améliorer de manière fluide la sécurité des machines virtuelles de [2e génération](../virtual-machines/generation-2.md). Le lancement fiable protège contre les techniques d’attaque avancées et persistantes. Le lancement fiable se compose de plusieurs technologies d’infrastructure coordonnées qui peuvent être activées indépendamment. Chaque technologie offre une couche de défense supplémentaire contre les menaces sophistiquées. Apprenez-en davantage dans [Lancement fiable pour les machines virtuelles Azure](../virtual-machines/trusted-launch.md).

> [!IMPORTANT]
> Le lancement fiable requiert la création de nouvelles machines virtuelles. Vous ne pouvez pas activer le lancement fiable sur les machines virtuelles existantes qui ont été créées initialement sans cette fonctionnalité.
> 
> Le lancement fiable est actuellement disponible en préversion publique. La préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.

La recommandation de Security Center, **vTPM doit être activé sur les machines virtuelles prises en charge**, garantit que vos machines virtuelles Azure utilisent un vTPM. Cette version virtualisée d’un module de plateforme sécurisée matériel permet d’effectuer l’attestation en mesurant la chaîne de démarrage complète de votre machine virtuelle (UEFI, système d’exploitation, système et pilotes).

Lorsque le vTPM est activé, l’**extension Attestation d’invité** peut valider à distance le démarrage sécurisé. Les recommandations suivantes garantissent le déploiement de cette extension :

- **Le démarrage sécurisé doit être activé sur les machines virtuelles Windows prises en charge**
- **L’extension Attestation d’invité doit être installée sur les machines virtuelles Windows prises en charge**
- **L’extension Attestation d’invité doit être installée sur les groupes de machines virtuelles identiques Windows pris en charge**
- **L’extension Attestation d’invité doit être installée sur les machines virtuelles Linux prises en charge**
- **L’extension Attestation d’invité doit être installée sur les groupes de machines virtuelles identiques Linux pris en charge**

Apprenez-en davantage dans [Lancement fiable pour les machines virtuelles Azure](../virtual-machines/trusted-launch.md). 

### <a name="new-recommendations-for-hardening-kubernetes-clusters-in-preview"></a>Nouvelles recommandations pour renforcer les clusters Kubernetes (en préversion)

Les recommandations suivantes vous permettent de renforcer davantage vos clusters Kubernetes

- **Les clusters Kubernetes ne doivent pas utiliser l’espace de noms par défaut** : pour éviter tout accès non autorisé aux types de ressources ConfigMap, Pod, Secret, Service et ServiceAccount, empêchez l’utilisation de l’espace de noms par défaut dans les clusters Kubernetes.
- **Les clusters Kubernetes doivent désactiver le montage automatique des informations d’identification d’API** : pour empêcher une ressource Pod potentiellement compromise d’exécuter des commandes API sur des clusters Kubernetes, désactivez le montage automatique des informations d’identification de l’API.
- **Les clusters Kubernetes ne doivent pas octroyer de fonctionnalités de sécurité CAPSYSADMIN**

Découvrez comment Security Center peut protéger vos environnements conteneurisés dans [Sécurité des conteneurs dans Security Center](container-security.md).

### <a name="assessments-api-expanded-with-two-new-fields"></a>Développement de l’API Évaluations avec deux nouveaux champs

Nous avons ajouté les deux champs suivants à l’[API REST Évaluations](/rest/api/securitycenter/assessments) :

- **FirstEvaluationDate** : Heure à laquelle la recommandation a été créée et évaluée pour la première fois. Retournée sous forme de temps universel coordonné (UTC) au format ISO 8601.
- **StatusChangeDate** : Heure de la dernière modification de l’état de la recommandation. Retournée sous forme de temps universel coordonné (UTC) au format ISO 8601.

La valeur initiale par défaut de ces champs (pour toutes les recommandations) est `2021-03-14T00:00:00+0000000Z`.

Pour accéder à ces informations, vous pouvez utiliser l’une des méthodes indiquées dans le tableau ci-dessous.

| Outil                 | Détails                                                                                                                                                                |
|----------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Appel d’API REST        | GET https://management.azure.com/subscriptions/<SUBSCRIPTION_ID>/providers/Microsoft.Security/assessments?api-version=2019-01-01-preview& $expand=statusEvaluationDates |
| Azure Resource Graph | `securityresources`<br>`where type == "microsoft.security/assessments"`                                                                                                |
| Exportation continue    | Les deux champs dédiés seront disponibles dans les données de l’espace de travail Log Analytics                                                                                            |
| [Exportation CSV](continuous-export.md#manual-one-time-export-of-alerts-and-recommendations) | Les deux champs sont inclus dans les fichiers CSV.                                                        |
|                      |                                                                                                                                                                        |


Découvrez-en plus sur l’[API REST Évaluations](/rest/api/securitycenter/assessments).


### <a name="asset-inventory-gets-a-cloud-environment-filter"></a>L’inventaire des ressources obtient un filtre d’environnement cloud

La page d’inventaire des ressources de Security Center propose de nombreux filtres pour affiner rapidement la liste des ressources affichées. Pour en savoir plus, consultez [Explorer et gérer vos ressources avec l’inventaire des ressources](asset-inventory.md).

Un nouveau filtre offre la possibilité d’affiner la liste en fonction des comptes cloud que vous avez connectés avec les fonctionnalités multiclouds de Security Center :

:::image type="content" source="media/asset-inventory/filter-environment.png" alt-text="Filtre d’environnement de l’inventaire":::

Découvrez plus d’informations sur les fonctionnalités multiclouds :

- [Connecter vos comptes AWS à Azure Security Center](quickstart-onboard-aws.md)
- [Connectez vos comptes GCP à Azure Security Center](quickstart-onboard-gcp.md)
