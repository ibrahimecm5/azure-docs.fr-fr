---
title: Fonctionnalités de Microsoft Defender pour le cloud selon le système d’exploitation, le type de machine et le cloud
description: Découvrez la disponibilité des fonctionnalités de Microsoft Defender pour le cloud selon le système d’exploitation, le type de machine et le déploiement cloud.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: overview
ms.date: 08/18/2021
ms.custom: references_regions
ms.author: memildin
ms.openlocfilehash: b12590890ec818e03c3d4097d2da904c17121674
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131457066"
---
# <a name="feature-coverage-for-machines"></a>Couverture des fonctionnalités pour les machines

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Les deux **onglets** ci-dessous présentent les fonctionnalités de Microsoft Defender pour le cloud disponibles pour les machines Windows et Linux.

## <a name="supported-features-for-virtual-machines-and-servers"></a>Fonctionnalités prises en charge pour les machines virtuelles et les serveurs <a name="vm-server-features"></a>

### <a name="windows-machines"></a>[**Machines Windows**](#tab/features-windows)

|**Fonctionnalité**|**Machines virtuelles Azure**|**Groupes de machines virtuelles identiques Azure**|**Machines avec Azure Arc**|**Fonctionnalités de sécurité renforcée requises**
|----|:----:|:----:|:----:|:----:|
|[Intégration de Microsoft Defender for Endpoint](integration-defender-for-endpoint.md)|✔</br>(sur les versions prises en charge)|✔</br>(sur les versions prises en charge)|✔|Oui|
|[Analytique comportementale des machines virtuelles (et alertes de sécurité)](alerts-reference.md)|✔|✔|✔|Oui|
|[Alertes de sécurité sans fichier](alerts-reference.md#alerts-windows)|✔|✔|✔|Oui|
|[Alertes de sécurité réseau](other-threat-protections.md#network-layer)|✔|✔|-|Oui|
|[Accès juste-à-temps aux machines virtuelles](just-in-time-access-usage.md)|✔|-|-|Oui|
|[Analyseur de vulnérabilités intégré Qualys](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner)|✔|-|✔|Oui|
|[Supervision de l’intégrité des fichiers](file-integrity-monitoring-overview.md)|✔|✔|✔|Oui|
|[Contrôles d’application adaptative](adaptive-application-controls.md)|✔|-|✔|Oui|
|[Mappage réseau](protect-network-resources.md#network-map)|✔|✔|-|Oui|
|[Durcissement réseau adaptatif](adaptive-network-hardening.md)|✔|-|-|Oui|
|[Tableau de bord et rapports de conformité réglementaire](regulatory-compliance-dashboard.md)|✔|✔|✔|Oui|
|[Sécurisation renforcée de l’hôte Docker](./harden-docker-hosts.md)|-|-|-|Oui|
|Évaluation des correctifs de système d’exploitation manquants|✔|✔|✔|Azure : Non<br><br>Avec Azure Arc : oui|
|Évaluation des erreurs de configuration de la sécurité|✔|✔|✔|Azure : Non<br><br>Avec Azure Arc : oui|
|[Évaluation de la protection des points de terminaison](supported-machines-endpoint-solutions-clouds.md#supported-endpoint-protection-solutions-)|✔|✔|✔|Azure : Non<br><br>Avec Azure Arc : oui|
|Évaluation du chiffrement des disques|✔</br>(pour les [scénarios pris en charge](../virtual-machines/windows/disk-encryption-windows.md#unsupported-scenarios))|✔|-|Non|
|Évaluation des vulnérabilités tierces|✔|-|✔|Non|
|[Évaluation de la sécurité réseau](protect-network-resources.md)|✔|✔|-|Non|
||||||

### <a name="linux-machines"></a>[**Machines Linux**](#tab/features-linux)

| **Fonctionnalité**                                                                                                               | **Machines virtuelles Azure**                                                                                      | **Groupes de machines virtuelles identiques Azure** | **Machines avec Azure Arc** | **Fonctionnalités de sécurité renforcée requises**       |
|---------------------------------------------------------------------------------------------------------------------------|:---------------------------------------------------------------------------------------------------------------:|:------------------------------------:|:------------------------------:|:---------------------------------:|
| [Intégration de Microsoft Defender for Endpoint](integration-defender-for-endpoint.md)                                                   | ✔                                                                                                               | -                                    | ✔                              | Oui                              |
| [Analytique comportementale des machines virtuelles (et alertes de sécurité)](./azure-defender.md)                                         | ✔</br>(sur les versions prises en charge)                                                                                  | ✔</br>(sur les versions prises en charge)        | ✔                             | Oui                               |
| [Alertes de sécurité sans fichier](alerts-reference.md#alerts-windows)                                                            | -                                                                                                               | -                                    | -                              | Oui                               |
| [Alertes de sécurité réseau](other-threat-protections.md#network-layer)                                                | ✔                                                                                                              | ✔                                    | -                              | Oui                               |
| [Accès juste-à-temps aux machines virtuelles](just-in-time-access-usage.md)                                                                 | ✔                                                                                                              | -                                    | -                              | Oui                               |
| [Analyseur de vulnérabilités intégré Qualys](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner) | ✔                                                                                                              | -                                    | ✔                             | Oui                               |
| [Supervision de l’intégrité des fichiers](file-integrity-monitoring-overview.md)                                                 | ✔                                                                                                              | ✔                                    | ✔                             | Oui                               |
| [Contrôles d’application adaptative](adaptive-application-controls.md)                                                  | ✔                                                                                                              | -                                    | ✔                             | Oui                               |
| [Mappage réseau](protect-network-resources.md#network-map)                                                     | ✔                                                                                                              | ✔                                    | -                              | Oui                               |
| [Durcissement réseau adaptatif](adaptive-network-hardening.md)                                               | ✔                                                                                                              | -                                    | -                              | Oui                               |
| [Tableau de bord et rapports de conformité réglementaire](regulatory-compliance-dashboard.md)                                      | ✔                                                                                                              | ✔                                    | ✔                             | Oui                               |
| [Sécurisation renforcée de l’hôte Docker](./harden-docker-hosts.md)                                                                         | ✔                                                                                                              | ✔                                    | ✔                             | Oui                               |
| Évaluation des correctifs de système d’exploitation manquants                                                                                             | ✔                                                                                                              | ✔                                    | ✔                             | Azure : Non<br><br>Avec Azure Arc : oui |
| Évaluation des erreurs de configuration de la sécurité                                                                                     | ✔                                                                                                              | ✔                                    | ✔                             | Azure : Non<br><br>Avec Azure Arc : oui |
| [Évaluation de la protection des points de terminaison](supported-machines-endpoint-solutions-clouds.md#supported-endpoint-protection-solutions-)                    | -                                                                                                               | -                                    | -                              | Non                                |
| Évaluation du chiffrement des disques                                                                                                | ✔</br>(pour les [scénarios pris en charge](../virtual-machines/windows/disk-encryption-windows.md#unsupported-scenarios)) | ✔                                    | -                              | Non                                |
| Évaluation des vulnérabilités tierces                                                                                      | ✔                                                                                                              | -                                    | ✔                             | Non                                |
| [Évaluation de la sécurité réseau](protect-network-resources.md)                                                 | ✔                                                                                                              | ✔                                    | -                              | Non                                |
||||||

--- 


> [!TIP]
>Pour expérimenter les fonctionnalités disponibles uniquement avec les fonctionnalités de sécurité renforcée activées, vous pouvez vous inscrire à un essai de 30 jours. Pour plus d’informations, consultez la [page relative aux prix appliqués](https://azure.microsoft.com/pricing/details/security-center/).


## <a name="supported-endpoint-protection-solutions"></a>Solutions de protection du point de terminaison prises en charge <a name="endpoint-supported"></a>

Le tableau suivant fournit une matrice des solutions de protection de point de terminaison prises en charge, et indique si vous pouvez utiliser Microsoft Defender pour le cloud pour installer chaque solution.

Pour plus d’informations sur le moment où les recommandations sont générées pour chacune de ces solutions, consultez [Évaluation de la protection de point de terminaison et recommandations](endpoint-protection-recommendations-technical.md).

| Solution                                                            | Plateformes prises en charge          | Installation de Defender pour le cloud |
|---------------------------------------------------------------------|------------------------------|---------------------------------|
| Antivirus Microsoft Defender                                        | Windows Server 2016 ou version ultérieure | Non (intégré au système d’exploitation)              |
| System Center Endpoint Protection (logiciel anti-programme malveillant de Microsoft)           | Windows Server 2012 R2       | Via l’extension                   |
| Trend Micro - Deep Security                                         | Windows Server (tous)         | Non                              |
| Symantec v12.1.1100+                                                | Windows Server (tous)         | Non                              |
| McAfee v10+                                                         | Windows Server (tous)         | Non                              |
| McAfee v10+                                                         | Linux (préversion)              | Non                              |
| Microsoft Defender pour point de terminaison pour Linux<sup>[1](#footnote1)</sup> | Linux (préversion)              | Via l’extension                   |
| Sophos V9+                                                          | Linux (préversion)              | Non                              |
|                                                                     |                              |                                 |

<sup><a name="footnote1" /></a> 1</sup> il ne suffit pas d’avoir Microsoft Defender pour point de terminaison sur la machine Linux. Celle-ci n’apparaîtra saine que si la fonctionnalité d’analyse Always On (également appelée protection en temps réel ou RTP) est active. Par défaut, la fonctionnalité RTP est **désactivée** pour éviter tout conflit avec d’autres logiciels AV.




## <a name="feature-support-in-government-and-sovereign-clouds"></a>Prise en charge des fonctionnalités dans les clouds du secteur public et souverains

| Fonctionnalité/Service                                                                                                                                           | Azure          | Azure Government               | Azure China 21Vianet   |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------|----------------|--------------------------------|---------------|
| **Fonctionnalités gratuites de Defender pour le Cloud**                                                                                                                         |                |                                |               |
| - [Exportation continue](./continuous-export.md)                                                                                                             | GA             | GA                             | GA            |
| - [Automatisation des workflows](./workflow-automation.md)                                                                                                           | GA             | GA                             | GA            |
| - [Règles d’exemption de recommandation](./exempt-resource.md)                                                                                                  | Version préliminaire publique | Non disponible                  | Non disponible |
| - [Règles de suppression d’alerte](./alerts-suppression-rules.md)                                                                                                | GA             | GA                             | GA            |
| - [Notifications par e-mail pour les alertes de sécurité](./configure-email-notifications.md)                                                        | GA             | GA                             | GA            |
| - [Approvisionnement automatique pour les agents et les extensions](./enable-data-collection.md)                                                              | GA             | GA                             | GA            |
| - [Inventaire des ressources](./asset-inventory.md)                                                                                                                 | GA             | GA                             | GA            |
| - [Rapports de classeurs Azure Monitor dans la galerie de classeurs de Microsoft Defender pour le cloud](./custom-dashboards-azure-workbooks.md)                                  | GA             | GA                             | GA            |
| **Plans et extensions Microsoft Defender**                                                                                                                   |                |                                |               |
| - [Microsoft Defender pour les serveurs](./defender-for-servers-introduction.md)                                                                                    | GA             | GA                             | GA            |
| - [Microsoft Defender pour App Service](./defender-for-app-service-introduction.md)                                                                            | GA             | Non disponible                  | Non disponible |
| - [Microsoft Defender pour DNS](./defender-for-dns-introduction.md)                                                                                            | GA             | GA                             | GA            |
| - [Microsoft Defender pour les registres de conteneurs](./defender-for-container-registries-introduction.md) <sup>[1](#footnote1)</sup>                               | GA             | GA  <sup>[2](#footnote2)</sup> | GA  <sup>[2](#footnote2)</sup> |
| - [Microsoft Defender pour les registres de conteneurs analyse des images dans les flux de travail CI/CD](./defender-for-container-registries-cicd.md) <sup> [3](#footnote3)</sup> | Version préliminaire publique | Non disponible                  | Non disponible |
| - [Microsoft Defender pour Kubernetes](./defender-for-kubernetes-introduction.md)<sup>[4](#footnote4)</sup>                                                   | GA             | GA                             | GA            |
| - [Extension Defender pour les clusters Kubernetes avec Azure Arc](./defender-for-kubernetes-azure-arc.md) <sup>[5](#footnote5)</sup>                 | Version préliminaire publique | Non disponible                  | Non disponible |
| - [Microsoft Defender pour les serveurs de base de données Azure SQL](./defender-for-sql-introduction.md)                                                                     | GA             | GA                             | GA  <sup>[9](#footnote9)</sup> |
| - [Microsoft Defender pour les serveurs SQL sur les machines](./defender-for-sql-introduction.md)                                                                        | GA             | GA                             | Non disponible |
| - [Microsoft Defender pour les bases de données relationnelles open source](./defender-for-databases-introduction.md)                                                         | GA             | Non disponible                  | Non disponible |
| - [Microsoft Defender pour Key Vault](./defender-for-key-vault-introduction.md)                                                                                | GA             | Non disponible                  | Non disponible |
| - [Microsoft Defender pour Resource Manager](./defender-for-resource-manager-introduction.md)                                                                  | GA             | GA                             | GA            |
| - [Microsoft Defender pour stockage](./defender-for-storage-introduction.md) <sup>[6](#footnote6)</sup>                                                         | GA             | GA                             | Non disponible |
| - [Protection contre les menaces pour Cosmos DB](./other-threat-protections.md#threat-protection-for-azure-cosmos-db-preview)                                          | Version préliminaire publique | Non disponible                  | Non disponible |
| - [Protection de charge de travail Kubernetes](./kubernetes-workload-protections.md)                                                                                  | GA             | GA                             | GA            |
| - [Synchronisation d’alerte bidirectionnelle avec Sentinel](../sentinel/connect-azure-security-center.md)                                                      | Version préliminaire publique | Non disponible                  | Non disponible |
| **Fonctionnalités de Microsoft Defender pour les serveurs** <sup>[7](#footnote7)</sup>                                                                                        |                |                                |               |
| - [Accès juste-à-temps aux machines virtuelles](./just-in-time-access-usage.md)                                                                                             | GA             | GA                             | GA            |
| - [Supervision de l’intégrité des fichiers](./file-integrity-monitoring-overview.md)                                                                             | GA             | GA                             | GA            |
| - [Contrôles d’application adaptatifs](./adaptive-application-controls.md)                                                                              | GA             | GA                             | GA            |
| - [Sécurisation renforcée du réseau adaptative](./adaptive-network-hardening.md)                                                                           | GA             | Non disponible                  | Non disponible |
| - [Sécurisation renforcée de l’hôte Docker](./harden-docker-hosts.md)                                                                                                       | GA             | GA                             | GA            |
| - [Analyseur de vulnérabilités intégré Qualys](./deploy-vulnerability-assessment-vm.md)                                                             | GA             | Non disponible                  | Non disponible |
| - [Tableau de bord et rapports de conformité réglementaire](./regulatory-compliance-dashboard.md) <sup>[8](#footnote8)</sup>                                       | GA             | GA                             | GA            |
| - [Déploiement de Microsoft Defender pour point de terminaison et licence intégrée](./integration-defender-for-endpoint.md)                                                         | GA             | GA                             | Non disponible |
| - [Connecter un compte AWS](./quickstart-onboard-aws.md)                                                                                                      | GA             | Non disponible                  | Non disponible |
| - [Connecter un compte GCP](./quickstart-onboard-gcp.md)                                                                                                      | GA             | Non disponible                  | Non disponible |
|                                                                                                                                                           |                |                                |               |

<sup><a name="footnote1" /></a>1</sup> Partiellement GA : la possibilité de désactiver des résultats spécifiques à partir d’analyses de vulnérabilités est disponible en préversion publique.

<sup><a name="footnote2" /></a>2</sup> Les analyses de vulnérabilité des registres de conteneurs sur le cloud Azure Government ne peuvent uniquement être effectuées avec la fonctionnalité d’analyse à l’envoi (push).

<sup><a name="footnote3" /></a>3</sup> Requiert Microsoft Defender pour les registres de conteneurs.

<sup><a name="footnote4" /></a>4</sup> Partiellement GA : la prise en charge des clusters avec Azure Arc est en préversion publique et n’est pas disponible sur Azure Government.

<sup><a name="footnote5" /></a>5</sup> Requiert Microsoft Defender pour Kubernetes.

<sup><a name="footnote6" /></a>6</sup> Partiellement en disponibilité générale : certaines des alertes de protection contre les menaces de Microsoft Defender pour Stockage sont disponibles en préversion publique.

<sup><a name="footnote7" /></a>7</sup> Ces fonctionnalités requièrent toutes [Microsoft Defender pour les serveurs](./defender-for-servers-introduction.md).

<sup><a name="footnote8" /></a>8</sup> Des différences sont possible en termes de normes offertes par type de cloud.
 
<sup><a name="footnote9" /></a>9</sup> Partiellement GA : sous-ensemble d’alertes et évaluation des vulnérabilités pour les serveurs SQL. Les protections contre les menaces comportementales ne sont pas disponibles.

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [Defender pour le cloud collecte des données avec l’agent Log Analytics](enable-data-collection.md).
- Découvrez comment [Defender pour cloud gère et protège les données](data-security.md).
- Passez en revue les [plateformes qui prennent en charge Defender pour le cloud](security-center-os-coverage.md).
