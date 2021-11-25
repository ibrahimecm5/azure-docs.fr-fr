---
title: Disponibilité des fonctionnalités cloud des services Azure pour les clients du secteur public américain
description: Présente les fonctionnalités disponibles pour les services de sécurité Azure, tels que Microsoft Sentinel pour les clients du secteur public américain
author: TerryLanfear
ms.author: terrylan
ms.service: security
ms.topic: reference
ms.date: 09/13/2021
ms.openlocfilehash: 9d2b984d78abd09e1fe4baf7e0c443e83ad8dc13
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132523103"
---
# <a name="cloud-feature-availability-for-us-government-customers"></a>Disponibilité des fonctionnalités cloud pour les clients du secteur public américain

Cet article décrit la disponibilité des fonctionnalités des clouds Microsoft Azure et Azure Government pour les services de sécurité suivants :

- [Azure Information Protection](#azure-information-protection)
- [Microsoft Defender pour le cloud](#microsoft-defender-for-cloud)
- [Microsoft Sentinel](#microsoft-sentinel)
- [Microsoft Defender pour IoT](#microsoft-defender-for-iot)
- [Azure Attestation](#azure-attestation)

> [!NOTE]
> D'autres services de sécurité seront bientôt ajoutés à cet article.
>

## <a name="azure-government"></a>Azure Government

Azure Government utilise les mêmes technologies sous-jacentes qu'Azure (parfois appelées Azure Commercial ou Azure Public). Celles-ci comprennent les composants de base des modèles IaaS (infrastructure-as-a-service), PaaS (Platform-as-a-service) et SaaS (Software-as-a-service). Azure et Azure Government disposent de contrôles de sécurité complets, et Microsoft s'engage à assurer la protection des données client.

Azure Government est un environnement cloud physiquement isolé dédié au secteur public des États-Unis (autorités fédérales, étatiques, locales et tribales) ainsi qu'à leurs partenaires. Les deux environnements cloud sont conformes au niveau d'impact FedRAMP High, mais Azure Government offre une couche supplémentaire de protection aux clients par le biais d'engagements contractuels en matière de stockage des données client aux États-Unis et en limitant l'accès potentiel aux systèmes traitant les données client à des personnes spécifiques basées aux États-Unis. Ces engagements peuvent intéresser les clients qui utilisent le cloud pour stocker ou traiter des données soumises aux réglementations américaines sur le contrôle des exportations, comme les réglementations EAR, ITAR et DoE 10 CFR Part 810.

Pour plus d'informations sur Azure Government, consultez [Qu'est-ce qu'Azure Government ?](../../azure-government/documentation-government-welcome.md)

## <a name="microsoft-365-integration"></a>Intégration de Microsoft 365

Les intégrations entre produits reposent sur l'interopérabilité entre les plateformes Azure et Office. Les offres hébergées dans l'environnement Azure sont accessibles à partir des plateformes Microsoft 365 Entreprise et Microsoft 365 Government. Office 365 et Office 365 GCC sont associés à Azure Active Directory (Azure AD) dans Azure. Office 365 GCC High et Office 365 DoD sont associés à Azure AD dans Azure Government.

Le diagramme suivant présente la hiérarchie des clouds Microsoft et leurs relations les uns avec les autres.

:::image type="content" source="media/microsoft-365-cloud-integration.png" alt-text="Intégration du cloud Microsoft 365.":::

L'environnement Office 365 GCC aide les clients à se conformer aux exigences du secteur public américain, comme les normes FedRAMP High, CJIS et IRS 1075. Les environnements Office 365 GCC High et DoD prennent en charge les clients qui ont besoin de se conformer aux normes DoD IL4/5, DFARS 7012, NIST 800-171 et ITAR.

Pour plus d'informations sur les environnements Office 365 US Government, consultez :

- [Office 365 GCC](/office365/servicedescriptions/office-365-platform-service-description/office-365-us-government/gcc)
- [Office 365 GCC High et DoD](/office365/servicedescriptions/office-365-platform-service-description/office-365-us-government/gcc-high-and-dod)

Les sections suivantes répertorient les services qui disposent d'une intégration à Microsoft 365 et indiquent la disponibilité des fonctionnalités pour Office 365 GCC, Office 365 High et Office 365 DoD.

## <a name="azure-information-protection"></a>Azure Information Protection

Azure Information Protection (AIP) est une solution cloud qui permet aux organisations de découvrir, de classifier et de protéger leurs documents et leurs e-mails en appliquant des étiquettes au contenu.

AIP fait partie de la solution Microsoft Information Protection (MIP) et étend les fonctionnalités d’[étiquetage](/microsoft-365/compliance/sensitivity-labels) et de [classification](/microsoft-365/compliance/data-classification-overview) fournies par Microsoft 365.

Pour plus d’informations, consultez la [documentation produit d’Azure Information Protection](/azure/information-protection/).

- Office 365 GCC est associé à Azure Active Directory (Azure AD) dans Azure. Office 365 GCC High et Office 365 DoD sont associés à Azure AD dans Azure Government. Veillez à prêter attention à l'environnement Azure pour déterminer où l'[interopérabilité est possible](#microsoft-365-integration). Dans le tableau suivant, l'interopérabilité qui n'est *pas* possible est signalée par un tiret (-) pour indiquer que la prise en charge n'est pas pertinente.

- Des configurations supplémentaires sont requises pour les clients GCC High et DoD. Pour plus d’informations, consultez [Description du service Azure Information Protection Premium pour le gouvernement](/enterprise-mobility-security/solutions/ems-aip-premium-govt-service-description).

> [!NOTE]
> Des informations supplémentaires sur la prise en charge des clients gouvernementaux sont répertoriées dans les notes de bas de page ci-dessous.
>
> Des étapes supplémentaires sont requises pour la configuration d’Azure Information Protection pour public les clients GCC High et DoD. Pour plus d’informations, consultez la [Description du service Azure Information Protection Premium pour le gouvernement](/enterprise-mobility-security/solutions/ems-aip-premium-govt-service-description).
>

|Fonctionnalité/Service  |Azure  |Azure Government  |
|---------|---------|---------|
|**[Scanneur Azure Information Protection](/azure/information-protection/deploy-aip-scanner)** <sup>[1](#aipnote1)</sup>       |         |         |
| - Office 365 GCC | GA | - |
| - Office 365 GCC High | - | GA |
| - Office 365 DoD | - | GA |
|**Administration**     |         |         |
|[Portail Azure Information Protection pour l’administration du scanneur](/azure/information-protection/deploy-aip-scanner-configure-install?tabs=azure-portal-only)     |         |         |
| - Office 365 GCC | GA | - |
| - Office 365 GCC High | - | GA |
| - Office 365 DoD | - | GA |
| **Classification et étiquetage** <sup>[2](#aipnote2)</sup>   |         |         |
| [Scanneur AIP pour appliquer une *étiquette par défaut* à tous les fichiers dans un serveur de fichiers/référentiel local](/azure/information-protection/deploy-aip-scanner-configure-install?tabs=azure-portal-only)    |         |         |
| - Office 365 GCC | GA | - |
| - Office 365 GCC High | - | GA |
| - Office 365 DoD | - | GA |
| [Scanneur AIP pour la classification, l’étiquetage et la protection automatisés des fichiers locaux pris en charge](/azure/information-protection/deploy-aip-scanner)    |         |         |
| - Office 365 GCC | GA | - |
| - Office 365 GCC High | - | GA |
| - Office 365 DoD | - | GA |
| |  |  |

<sup><a name="aipnote1"></a> 1</sup> le scanneur peut fonctionner sans Office 365 pour analyser les fichiers uniquement. Le scanneur ne peut pas appliquer d’étiquettes aux fichiers sans Office 365.

<sup><a name="aipnote2"></a> 2</sup> Le complément Classification et étiquetage est uniquement pris en charge pour les clients gouvernementaux avec Microsoft 365 Apps (version 9126.1001 ou ultérieure), y compris les versions Professional Plus (ProPlus) et C2R (C2R). Office 2010, Office 2013 et les autres versions d’Office 2016 ne sont pas pris en charge.

### <a name="office-365-features"></a>Fonctionnalités d’Office 365

|Fonctionnalité/Service  |Office 365 GCC  |Office 365 GCC High |Office 365 DoD  |
|---------|---------|---------|---------|
|**Administration**     |         |         | |
|- [Administration du service PowerShell pour RMS](/powershell/module/aipservice/)      |  GA       |    GA     |   GA      |
|- [PowerShell pour les opérations en bloc du client UL d’AIP](/powershell/module/azureinformationprotection/)      |         |         |         |
|**Kit SDK**     |         |         |         |
|- [Kit de développement logiciel (SDK) MIP et AIP](/information-protection/develop/)     |     GA       |    GA     |   GA  |
|**Personnalisations**     |         |         |         |
|- [Suivi et révocation de documents](/azure/information-protection/rms-client/track-and-revoke-admin)      |   GA      |  Non disponible       |     Non disponible    |
|**Gestion des clés**      |         |         |         |
|- [BYOK (Bring Your Own Key)](/azure/information-protection/byok-price-restrictions)      |   GA       |    GA     |   GA   |
|- [Chiffrement à double clé (DKE)](/azure/information-protection/plan-implement-tenant-key)     |    GA       |    GA     |   GA    |
|**Fichiers Office** <sup>[3](#aipnote6)</sup>      |         |         |         |
|- [Protection pour Microsoft Exchange Online, Microsoft Office SharePoint Online et Microsoft OneDrive Entreprise](/azure/information-protection/requirements-applications)      |     GA    |  GA <sup>[4](#aipnote3)</sup>       |   GA <sup>[4](#aipnote3)</sup>      |
|- [Protection du contenu Exchange et SharePoint local via le connecteur Rights Management](/azure/information-protection/deploy-rms-connector)     |    GA <sup>[5](#aipnote5)</sup>      |  Non disponible       |     Non disponible         |
|- [Chiffrement de messages Office 365](/microsoft-365/compliance/set-up-new-message-encryption-capabilities)      |     GA       |    GA     |   GA        |
|- [Définir des étiquettes pour appliquer automatiquement la protection M/MIME préconfigurée dans Outlook](/azure/information-protection/rms-client/clientv2-admin-guide-customizations)      |         GA       |    GA     |   GA        |
|- [Contrôler le surpartage d’informations lors de l’utilisation d’Outlook](/azure/information-protection/rms-client/clientv2-admin-guide-customizations)     |      GA   |  GA <sup>[6](#aipnote6)</sup>        |    GA <sup>[6](#aipnote6)</sup>      |
|**FAQ sur la classification et l’étiquetage** <sup>[2](#aipnote2) / [7](#aipnote7)</sup>      |         |         |         |
|- Modèles personnalisés, y compris les modèles départementaux     |     GA       |    GA     |   GA         |
|- Classification de documents manuelle, par défaut et obligatoire     |       GA       |    GA     |   GA       |
|- Configurer des conditions pour la classification automatique et recommandée en disponibilité générale       |    GA     |   GA        |
|- [Protection des formats de fichier non Microsoft Office, notamment PTXT, PJPG et PFILE (protection générique)](/azure/information-protection/rms-client/clientv2-admin-guide-file-types)     |        GA       |    GA     |   GA       |
|     |         |         |         |

<sup><a name="aipnote3"></a> 3</sup> L’extension Appareils mobiles pour AD RMS n’est actuellement pas disponible pour les clients gouvernementaux.

<sup><a name="aipnote4"></a> 4</sup> La Gestion des droits relatifs à l’information (IRM) avec SharePoint Online (sites et bibliothèques protégés par IRM) n’est pas disponible pour le moment.

<sup><a name="aipnote5"></a> 5</sup> La Gestion des droits relatifs à l’information (IRM) est prise en charge uniquement pour Microsoft 365 Apps (version 9126.1001 ou ultérieure), y compris les versions Professional Plus (ProPlus) et Click-to-Run (C2R). Office 2010, Office 2013 et les autres versions d’Office 2016 ne sont pas pris en charge.

<sup><a name="aipnote6"></a> 6</sup> Le partage des documents et e-mails protégés des clouds gouvernementaux avec les utilisateurs du cloud commercial n’est pas disponible actuellement. Comprend les utilisateurs Microsoft 365 Apps dans le cloud commercial, les utilisateurs non Microsoft 365 Apps dans le cloud commercial et les utilisateurs disposant d’une licence RMS pour les particuliers.

<sup><a name="aipnote7"></a>7</sup> Le nombre de [Types d’informations sensibles](/microsoft-365/compliance/sensitive-information-type-entity-definitions) dans votre Centre de sécurité et de conformité Microsoft 365 peut varier en fonction de la région.

## <a name="microsoft-defender-for-cloud"></a>Microsoft Defender pour le cloud

Microsoft Defender pour le cloud est un système unifié d’administration de la sécurité de l’infrastructure qui renforce la posture de sécurité de vos centres de données et fournit une protection avancée contre les menaces pour vos charges de travail hybrides dans le cloud (dans Azure ou non), ainsi qu’en local.

Pour plus d’informations, consultez la [documentation de Microsoft Defender pour le cloud](../../defender-for-cloud/defender-for-cloud-introduction.md).

Le tableau suivant présente la disponibilité actuelle des fonctionnalités Defender pour le cloud dans Azure et Azure Government.

| Fonctionnalité/Service                                                                                                                                                                      | Azure          | Azure Government               |
|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------|--------------------------------|
| **Fonctionnalités gratuites de Microsoft Defender pour le cloud**                                                                                                                                       |                |                                |
| <li> [Exportation continue](../../defender-for-cloud/continuous-export.md)                                                                                                                 | GA             | GA                             |
| <li> [Automatisation de workflow](../../defender-for-cloud/workflow-automation.md)                                                                                                               | GA             | GA                             |
| <li> [Règles d’exemption de recommandation](../../defender-for-cloud/exempt-resource.md)                                                                                                      | Version préliminaire publique | Non disponible                  |
| <li> [Règles de suppression d’alerte](../../defender-for-cloud/alerts-suppression-rules.md)                                                                                                    | GA             | GA                             |
| <li> [Notifications par e-mail pour les alertes de sécurité](../../defender-for-cloud/configure-email-notifications.md)                                                            | GA             | GA                             |
| <li> [Approvisionnement automatique pour les agents et les extensions](../../defender-for-cloud/enable-data-collection.md)                                                                  | GA             | GA                             |
| <li> [Inventaire des ressources](../../defender-for-cloud/asset-inventory.md)                                                                                                                     | GA             | GA                             |
| <li> [Rapports de classeurs Azure Monitor dans la galerie de classeurs de Microsoft Defender pour le cloud](../../defender-for-cloud/custom-dashboards-azure-workbooks.md)                               | GA             | GA                             |
| **Plans et extensions Microsoft Defender**                                                                                                                                          |                |                                |
| <li> [Microsoft Defender pour les serveurs](../../defender-for-cloud/defender-for-servers-introduction.md)                                                                                    | GA             | GA                             |
| <li> [Microsoft Defender pour App Service](../../defender-for-cloud/defender-for-app-service-introduction.md)                                                                            | GA             | Non disponible                  |
| <li> [Microsoft Defender pour DNS](../../defender-for-cloud/defender-for-dns-introduction.md)                                                                                            | GA             | GA                             |
| <li> [Microsoft Defender pour les registres de conteneurs](../../defender-for-cloud/defender-for-container-registries-introduction.md)<sup>[1](#footnote1)</sup>                               | GA             | GA  <sup>[2](#footnote2)</sup> |
| <li> [Microsoft Defender pour les registres de conteneurs analysant des images dans les flux de travail CI/CD](../../defender-for-cloud/defender-for-container-registries-cicd.md)<sup>[3](#footnote3)</sup> | Version préliminaire publique | Non disponible                  |
| <li> [Microsoft Defender pour Kubernetes](../../defender-for-cloud/defender-for-kubernetes-introduction.md)<sup>[4](#footnote4)</sup>                                                   | GA             | GA                             |
| <li> [Extension Defender pour les clusters Kubernetes avec Azure Arc](../../defender-for-cloud/defender-for-kubernetes-azure-arc.md)<sup>[5](#footnote5)</sup>                           | Version préliminaire publique | Non disponible                  |
| <li> [Microsoft Defender pour les serveurs de base de données Azure SQL](../../defender-for-cloud/defender-for-sql-introduction.md)                                                                     | GA             | GA                             |
| <li> [Microsoft Defender pour les serveurs SQL sur les machines](../../defender-for-cloud/defender-for-sql-introduction.md)                                                                        | GA             | GA                             |
| <li> [Microsoft Defender pour les bases de données relationnelles open source](../../defender-for-cloud/defender-for-databases-introduction.md)                                                         | GA             | Non disponible                  |
| <li> [Microsoft Defender pour Key Vault](../../defender-for-cloud/defender-for-key-vault-introduction.md)                                                                                | GA             | Non disponible                  |
| <li> [Microsoft Defender pour Resource Manager](../../defender-for-cloud/defender-for-resource-manager-introduction.md)                                                                  | GA             | GA                             |
| <li> [Microsoft Defender pour stockage](../../defender-for-cloud/defender-for-storage-introduction.md)<sup>[6](#footnote6)</sup>                                                         | GA             | GA                             |
| <li> [Protection contre les menaces pour Cosmos DB](../../defender-for-cloud/other-threat-protections.md#threat-protection-for-azure-cosmos-db-preview)                                              | Version préliminaire publique | Non disponible                  |
| <li> [Protection de charge de travail Kubernetes](../../defender-for-cloud/kubernetes-workload-protections.md)                                                                                      | GA             | GA                             |
| <li> [Synchronisation d’alerte bidirectionnelle avec Sentinel](../../sentinel/connect-azure-security-center.md)                                                                           | Version préliminaire publique | Non disponible                  |
| **Fonctionnalités de Microsoft Defender pour les serveurs** <sup>[7](#footnote7)</sup>                                                                                                               |                |                                |
| <li> [Accès juste-à-temps aux machines virtuelles](../../defender-for-cloud/just-in-time-access-overview.md)                                                                                                 | GA             | GA                             |
| <li> [Supervision de l’intégrité des fichiers](../../defender-for-cloud/file-integrity-monitoring-overview.md)                                                                                 | GA             | GA                             |
| <li> [Contrôles d’application adaptative](../../defender-for-cloud/adaptive-application-controls.md)                                                                                  | GA             | GA                             |
| <li> [Durcissement réseau adaptatif](../../defender-for-cloud/adaptive-network-hardening.md)                                                                               | GA             | Non disponible                  |
| <li> [Sécurisation renforcée de l’hôte Docker](../../defender-for-cloud/harden-docker-hosts.md)                                                                                                           | GA             | GA                             |
| <li> [Évaluation des vulnérabilités intégrée pour les machines](../../defender-for-cloud/deploy-vulnerability-assessment-vm.md)                                                                 | GA             | Non disponible                  |
| <li> [Tableau de bord et rapports de conformité réglementaire](../../defender-for-cloud/regulatory-compliance-dashboard.md)<sup>[8](#footnote8)</sup>                                           | GA             | GA                             |
| <li> [Déploiement de Microsoft Defender pour point de terminaison et licence intégrée](../../defender-for-cloud/integration-defender-for-endpoint.md)                                                             | GA             | GA                             |
| <li> [Connecter un compte AWS](../../defender-for-cloud/quickstart-onboard-aws.md)                                                                                                          | GA             | Non disponible                  |
| <li> [Connecter un compte GCP](../../defender-for-cloud/quickstart-onboard-gcp.md)                                                                                                          | GA             | Non disponible                  |
|                                                                                                                                                                                      |                |                                |

<sup><a name="footnote1"></a>1</sup> Partiellement GA : la possibilité de désactiver des résultats spécifiques à partir d’analyses de vulnérabilités est disponible en préversion publique.

<sup><a name="footnote2"></a>2</sup> Les analyses de vulnérabilité des registres de conteneurs sur Azure Gov ne peuvent uniquement être effectuées avec la fonctionnalité d’analyse à l’envoi (push).

<sup><a name="footnote3"></a>3</sup> Requiert Microsoft Defender pour les registres de conteneurs.

<sup><a name="footnote4"></a>4</sup> Partiellement GA : la prise en charge des clusters avec Azure Arc est en préversion publique et n’est pas disponible sur Azure Government.

<sup><a name="footnote5"></a>5</sup> Requiert Microsoft Defender pour Kubernetes.

<sup><a name="footnote6"></a>6</sup> Partiellement en disponibilité générale : certaines des alertes de protection contre les menaces de Microsoft Defender pour Stockage sont disponibles en préversion publique.

<sup><a name="footnote7"></a>7</sup> Ces fonctionnalités requièrent toutes [Microsoft Defender pour les serveurs](../../defender-for-cloud/defender-for-servers-introduction.md).

<sup><a name="footnote8"></a>8</sup> Des différences sont possible en termes de normes offertes par type de cloud.

<a name="azure-sentinel"></a>

## <a name="microsoft-sentinel"></a>Microsoft Sentinel

Microsoft Sentinel est une solution native Cloud et évolutive de type SIEM (Security Information and Event Management) et SOAR (Security Orchestrated Automated Response). Microsoft Sentinel assure une analyse de sécurité intelligente et fournit des renseignements sur les menaces dans l’ensemble de l’entreprise. Elle constitue une solution unique pour la détection des alertes, la visibilité des menaces, la chasse proactive et la réponse aux menaces.

Pour plus d’informations, consultez la [documentation produit de Microsoft Sentinel](../../sentinel/overview.md).

Les tableaux suivants indiquent la disponibilité actuelle des fonctionnalités Microsoft Sentinel dans Azure et Azure Government.

| Fonctionnalité | Azure | Azure Government  |
| ----- | ----- | ---- |
|- [Règles d’automatisation](../../sentinel/automate-incident-handling-with-automation-rules.md) | Version préliminaire publique | Version préliminaire publique |
|- [BYO-ML (Bring Your Own ML)](../../sentinel/bring-your-own-ml.md) | Version préliminaire publique | Version préliminaire publique |
| - [Vue des incidents inter-clients/inter-espaces de travail](../../sentinel/multiple-workspace-view.md) |Version préliminaire publique | Version préliminaire publique |
| - [Entity Insights](../../sentinel/enable-entity-behavior-analytics.md) | GA | Version préliminaire publique |
| - [Fusion](../../sentinel/fusion.md)<br>Détection avancée des attaques en plusieurs étapes <sup>[1](#footnote1)</sup> | GA | GA |
| - [Chasse](../../sentinel/hunting.md) | GA | GA |
|- [Notebooks](../../sentinel/notebooks.md) | GA | GA |
|- [Métriques d'audit des incidents SOC](../../sentinel/manage-soc-with-incident-metrics.md) | GA | GA |
|- [Watchlists](../../sentinel/watchlists.md) | GA | GA |
| **Support Threat Intelligence** | | |
| - [Connecteur de données Threat Intelligence – TAXII](../../sentinel/understand-threat-intelligence.md)  | GA | GA |
| - [Connecteur de données Threat Intelligence Platform](../../sentinel/understand-threat-intelligence.md)  | Version préliminaire publique | Non disponible |
| - [Panneau de recherche Threat Intelligence](https://techcommunity.microsoft.com/t5/azure-sentinel/what-s-new-threat-intelligence-menu-item-in-public-preview/ba-p/1646597)  | GA | GA |
| - [URL Detonation](https://techcommunity.microsoft.com/t5/azure-sentinel/using-the-new-built-in-url-detonation-in-azure-sentinel/ba-p/996229) | Version préliminaire publique | Non disponible |
| - [Classeur Threat Intelligence](/azure/architecture/example-scenario/data/sentinel-threat-intelligence)  | GA | GA |
|**Support lié à la détection** | | |
| - [Détection d'un accès anormal au partage de fichiers de Windows](../../sentinel/fusion.md)  | Version préliminaire publique | Non disponible |
| - [Détection de connexions RDP anormales](../../sentinel/connect-windows-security-events.md#configure-the-security-events--windows-security-events-connector-for-anomalous-rdp-login-detection)<br>Détection ML intégrée | Version préliminaire publique | Non disponible |
| - [Détection de connexions SSH anormales](../../sentinel/connect-syslog.md#configure-the-syslog-connector-for-anomalous-ssh-login-detection)<br>Détection ML intégrée | Version préliminaire publique | Non disponible |
| **Connecteurs de service Azure** |  |  |
| - [Journaux d'activité Azure](../../sentinel/data-connectors-reference.md#azure-activity) | GA | GA |
| - [Azure Active Directory](../../sentinel/connect-azure-active-directory.md) | GA | GA |
| - [Azure ADIP](../../sentinel/data-connectors-reference.md#azure-active-directory-identity-protection) | GA | GA |
| - [Azure DDoS Protection](../../sentinel/data-connectors-reference.md#azure-ddos-protection) | GA | GA |
| - [Microsoft Defender pour le cloud](../../sentinel/connect-azure-security-center.md) | GA | GA |
| - [Microsoft Defender pour IoT](../../sentinel/data-connectors-reference.md#microsoft-defender-for-iot) | Version préliminaire publique | Non disponible |
| - [Pare-feu Azure ](../../sentinel/data-connectors-reference.md#azure-firewall) | GA | GA |
| - [Azure Information Protection](../../sentinel/data-connectors-reference.md#azure-information-protection) | Version préliminaire publique | Non disponible |
| - [Azure Key Vault ](../../sentinel/data-connectors-reference.md#azure-key-vault) | Version préliminaire publique | Non disponible |
| - [Azure Kubernetes Services (AKS)](../../sentinel/data-connectors-reference.md#azure-kubernetes-service-aks) | Version préliminaire publique | Non disponible |
| - [Azure SQL Databases](../../sentinel/data-connectors-reference.md#azure-sql-databases) | GA | GA |
| - [Pare-feu d'applications web Azure](../../sentinel/data-connectors-reference.md#azure-web-application-firewall-waf) | GA | GA |
| **Connecteurs Windows** |  |  |
| - [Pare-feu Windows](../../sentinel/data-connectors-reference.md#windows-firewall) | GA | GA |
| - [Événements de sécurité Windows](../../sentinel/connect-windows-security-events.md) | GA | GA |
| **Connecteurs externes** |  |  |
| - [Agari Phishing Defense and Brand Protection](../../sentinel/data-connectors-reference.md#agari-phishing-defense-and-brand-protection-preview) | Version préliminaire publique | Version préliminaire publique |
| - [AI Analyst Darktrace](../../sentinel/connect-data-sources.md) | Version préliminaire publique | Version préliminaire publique |
| - [AI Vectra Detect](../../sentinel/data-connectors-reference.md#ai-vectra-detect-preview) | Version préliminaire publique | Version préliminaire publique |
| - [Akamai Security Events](../../sentinel/data-connectors-reference.md#akamai-security-events-preview) | Version préliminaire publique | Version préliminaire publique |
| - [Alcide kAudit](../../sentinel/data-connectors-reference.md#alcide-kaudit) | Version préliminaire publique | Non disponible |
| - [Alsid pour Active Directory](../../sentinel/data-connectors-reference.md#alsid-for-active-directory) | Version préliminaire publique | Non disponible |
| - [Apache HTTP Server](../../sentinel/data-connectors-reference.md#apache-http-server) | Version préliminaire publique | Non disponible |
| - [Aruba ClearPass](../../sentinel/data-connectors-reference.md#aruba-clearpass-preview) | Version préliminaire publique | Version préliminaire publique |
| - [AWS](../../sentinel/connect-data-sources.md) | GA | GA |
| - [Barracuda CloudGen Firewall](../../sentinel/data-connectors-reference.md#barracuda-cloudgen-firewall) | GA | GA |
| - [Barracuda Web App Firewall](../../sentinel/data-connectors-reference.md#barracuda-waf) | GA | GA |
| - [BETTER Mobile Threat Defense MTD](../../sentinel/data-connectors-reference.md#better-mobile-threat-defense-mtd-preview) | Version préliminaire publique | Non disponible |
| - [Beyond Security beSECURE](../../sentinel/data-connectors-reference.md#beyond-security-besecure) | Version préliminaire publique | Non disponible |
| - [Blackberry CylancePROTECT](../../sentinel/connect-data-sources.md) | Version préliminaire publique | Version préliminaire publique |
| - [Broadcom Symantec DLP](../../sentinel/data-connectors-reference.md#broadcom-symantec-data-loss-prevention-dlp-preview) | Version préliminaire publique | Version préliminaire publique |
| - [Check Point](../../sentinel/data-connectors-reference.md#check-point) | GA | GA |
| - [Cisco ASA](../../sentinel/data-connectors-reference.md#cisco-asa) | GA | GA |
| - [Cisco Meraki](../../sentinel/data-connectors-reference.md#cisco-meraki-preview) | Version préliminaire publique | Version préliminaire publique |
| - [Cisco Umbrella](../../sentinel/data-connectors-reference.md#cisco-umbrella-preview) | Version préliminaire publique | Version préliminaire publique |
| - [Cisco UCS](../../sentinel/data-connectors-reference.md#cisco-unified-computing-system-ucs-preview) | Version préliminaire publique | Version préliminaire publique |
| - [Cisco Firepower EStreamer](../../sentinel/connect-data-sources.md) | Version préliminaire publique | Version préliminaire publique |
| - [Citrix Analytics WAF](../../sentinel/data-connectors-reference.md#citrix-web-app-firewall-waf-preview) | GA | GA |
| - [Common Event Format (CEF)](../../sentinel/connect-common-event-format.md) | GA | GA |
| - [CyberArk Enterprise Password Vault (EPV) Events](../../sentinel/data-connectors-reference.md#cyberark-enterprise-password-vault-epv-events-preview) | Version préliminaire publique | Version préliminaire publique |
| - [ESET Enterprise Inspector](../../sentinel/connect-data-sources.md)                       | Version préliminaire publique | Non disponible      |
| - [Eset Security Management Center](../../sentinel/connect-data-sources.md)                  | Version préliminaire publique | Non disponible      |
| - [ExtraHop Reveal(x)](../../sentinel/data-connectors-reference.md#extrahop-revealx)                               | GA             | GA             |
| - [F5 BIG-IP ](../../sentinel/data-connectors-reference.md#f5-big-ip)                                       | GA             | GA             |
| - [F5 Networks](../../sentinel/data-connectors-reference.md#f5-networks-asm)                                     | GA             | GA             |
| - [Forcepoint NGFW](../../sentinel/data-connectors-reference.md#forcepoint-cloud-access-security-broker-casb-preview)                                  | Version préliminaire publique | Version préliminaire publique |
| - [Forcepoint CASB](../../sentinel/data-connectors-reference.md#forcepoint-cloud-access-security-broker-casb-preview)                                  | Version préliminaire publique | Version préliminaire publique |
| - [Forcepoint DLP ](../../sentinel/data-connectors-reference.md#forcepoint-data-loss-prevention-dlp-preview)                                   | Version préliminaire publique | Non disponible      |
| - [ForgeRock Common Audit for CEF](../../sentinel/connect-data-sources.md)                  | Version préliminaire publique | Version préliminaire publique |
| - [Fortinet](../../sentinel/data-connectors-reference.md#fortinet)                                         | GA             | GA             |
| - [Google Workspace (G Suite) ](../../sentinel/data-connectors-reference.md#google-workspace-g-suite-preview)                      | Version préliminaire publique | Non disponible      |
| - [Illusive Attack Management System](../../sentinel/data-connectors-reference.md#illusive-attack-management-system-ams-preview)                | Version préliminaire publique | Version préliminaire publique |
| - [Imperva WAF Gateway](../../sentinel/data-connectors-reference.md#imperva-waf-gateway-preview)                             | Version préliminaire publique | Version préliminaire publique |
| - [Infoblox NIOS](../../sentinel/data-connectors-reference.md#infoblox-network-identity-operating-system-nios-preview)                                    | Version préliminaire publique | Version préliminaire publique |
| - [Juniper SRX](../../sentinel/data-connectors-reference.md#juniper-srx-preview)                                      | Version préliminaire publique | Version préliminaire publique |
| - [Morphisec UTPP](../../sentinel/connect-data-sources.md)                                   | Version préliminaire publique | Version préliminaire publique |
| - [Netskope](../../sentinel/connect-data-sources.md)                                         | Version préliminaire publique | Version préliminaire publique |
| - [NXLog Windows DNS](../../sentinel/data-connectors-reference.md#nxlog-dns-logs-preview)                                             | Version préliminaire publique | Non disponible      |
| - [NXLog LinuxAudit](../../sentinel/data-connectors-reference.md#nxlog-linuxaudit-preview)                                 | Version préliminaire publique | Non disponible      |
| - [Okta Single Sign On](../../sentinel/data-connectors-reference.md#okta-single-sign-on-preview)                              | Version préliminaire publique | Version préliminaire publique |
| - [Onapsis Platform](../../sentinel/connect-data-sources.md)                                 | Version préliminaire publique | Version préliminaire publique |
| - [One Identity Safeguard](../../sentinel/data-connectors-reference.md#one-identity-safeguard-preview)                          | GA             | GA             |
| - [Orca Security Alerts](../../sentinel/data-connectors-reference.md#orca-security-preview)                            | Version préliminaire publique | Non disponible      |
| - [Palo Alto Networks](../../sentinel/data-connectors-reference.md#palo-alto-networks)                               | GA             | GA             |
| - [Perimeter 81 Activity Logs](../../sentinel/data-connectors-reference.md#perimeter-81-activity-logs-preview)                      | GA             | Non disponible      |
| - [Proofpoint On Demand Email Security](../../sentinel/data-connectors-reference.md#proofpoint-on-demand-pod-email-security-preview)             | Version préliminaire publique | Non disponible      |
| - [Proofpoint TAP](../../sentinel/data-connectors-reference.md#proofpoint-targeted-attack-protection-tap-preview)                                   | Version préliminaire publique | Version préliminaire publique |
| - [Pulse Connect Secure](../../sentinel/data-connectors-reference.md#proofpoint-targeted-attack-protection-tap-preview)                             | Version préliminaire publique | Version préliminaire publique |
| - [Qualys Vulnerability Management](../../sentinel/data-connectors-reference.md#qualys-vulnerability-management-vm-preview)                  | Version préliminaire publique | Version préliminaire publique |
| - [Salesforce Service Cloud](../../sentinel/data-connectors-reference.md#salesforce-service-cloud-preview)                         | Version préliminaire publique | Non disponible      |
| - [SonicWall Firewall ](../../sentinel/data-connectors-reference.md#sophos-cloud-optix-preview)                              | Version préliminaire publique | Version préliminaire publique |
| - [Sophos Cloud Optix](../../sentinel/data-connectors-reference.md#sophos-cloud-optix-preview)                               | Version préliminaire publique | Non disponible      |
| - [Sophos XG Firewall](../../sentinel/data-connectors-reference.md#sophos-xg-firewall-preview)                               | Version préliminaire publique | Version préliminaire publique |
| - [Squadra Technologies secRMM](../../sentinel/data-connectors-reference.md#squadra-technologies-secrmm)               | GA             | GA             |
| - [Squid Proxy](../../sentinel/data-connectors-reference.md#squid-proxy-preview)                                      | Version préliminaire publique | Non disponible      |
| - [Symantec Integrated Cyber Defense Exchange](../../sentinel/data-connectors-reference.md#symantec-integrated-cyber-defense-exchange-icdx)       | GA             | GA             |
| - [Symantec ProxySG](../../sentinel/data-connectors-reference.md#symantec-proxysg-preview)                                | Version préliminaire publique | Version préliminaire publique |
| - [Symantec VIP](../../sentinel/data-connectors-reference.md#symantec-vip-preview)                                     | Version préliminaire publique | Version préliminaire publique |
| - [Syslog](../../sentinel/connect-syslog.md)                                           | GA             | GA             |
| - [Thycotic Secret Server](../../sentinel/data-connectors-reference.md#thycotic-secret-server-preview)                          | Version préliminaire publique | Version préliminaire publique |
| - [Trend Micro Deep Security](../../sentinel/data-connectors-reference.md#trend-micro-deep-security)                       | GA             | GA             |
| - [Trend Micro TippingPoint](../../sentinel/data-connectors-reference.md#trend-micro-tippingpoint-preview)                         | Version préliminaire publique | Version préliminaire publique |
| - [Trend Micro XDR](../../sentinel/connect-data-sources.md)                                  | Version préliminaire publique | Non disponible      |
| - [VMware Carbon Black Endpoint Standard](../../sentinel/data-connectors-reference.md#vmware-carbon-black-endpoint-standard-preview)           | Version préliminaire publique | Version préliminaire publique |
| - [VMware ESXi](../../sentinel/data-connectors-reference.md#vmware-esxi-preview)                                      | Version préliminaire publique | Version préliminaire publique |
| - [WireX Network Forensics Platform](../../sentinel/data-connectors-reference.md#wirex-network-forensics-platform-preview)                | Version préliminaire publique | Version préliminaire publique |
| - [Zimperium Mobile Threat Defense](../../sentinel/data-connectors-reference.md#zimperium-mobile-thread-defense-preview)                  | Version préliminaire publique | Non disponible      |
| - [Zscaler](../../sentinel/data-connectors-reference.md#zscaler)                                         | GA             | GA             |
| | | |

<sup><a name="footnote1"></a>1</sup> Les détections SSH et RDP ne sont pas prises en charge pour les clouds souverains car la plateforme Databricks ML n'est pas disponible.

### <a name="microsoft-365-data-connectors"></a>Connecteurs de données Microsoft 365

Office 365 GCC est associé à Azure Active Directory (Azure AD) dans Azure. Office 365 GCC High et Office 365 DoD sont associés à Azure AD dans Azure Government.

> [!TIP]
> Veillez à prêter attention à l'environnement Azure pour déterminer où l'[interopérabilité est possible](#microsoft-365-integration). Dans le tableau suivant, l'interopérabilité qui n'est *pas* possible est signalée par un tiret (-) pour indiquer que la prise en charge n'est pas pertinente.
>

| Connecteur | Azure | Azure Government |
|--|--|--|
| **[Dynamics365](../../sentinel/data-connectors-reference.md#dynamics-365)** |  |  |
| - Office 365 GCC | Version préliminaire publique | - |
| - Office 365 GCC High | - | Non disponible |
| - Office 365 DoD | - | Non disponible |
| **[Microsoft 365 Defender](../../sentinel/connect-microsoft-365-defender.md)** |  |  |
| - Office 365 GCC | Version préliminaire publique | - |
| - Office 365 GCC High | - | Non disponible |
| - Office 365 DoD | - | Non disponible |
| **[Microsoft Defender for Cloud Apps](../../sentinel/data-connectors-reference.md#microsoft-defender-for-cloud-apps)** |  |  |
| - Office 365 GCC | GA | - |
| - Office 365 GCC High | - | GA |
| - Office 365 DoD | - | GA |
| **[Microsoft Defender for Cloud Apps](../../sentinel/data-connectors-reference.md#microsoft-defender-for-cloud-apps)** <br>Journaux d'informatique fantôme |  |  |
| - Office 365 GCC | Version préliminaire publique | - |
| - Office 365 GCC High | - | Version préliminaire publique |
| - Office 365 DoD | - | Version préliminaire publique |
| **[Microsoft Defender for Cloud Apps](../../sentinel/data-connectors-reference.md#microsoft-defender-for-cloud-apps)**                  <br>Alertes |  |  |
| - Office 365 GCC | Version préliminaire publique | - |
| - Office 365 GCC High | - | Version préliminaire publique |
| - Office 365 DoD | - | Version préliminaire publique |
| **[Microsoft Defender for Endpoint](../../sentinel/data-connectors-reference.md#microsoft-defender-for-endpoint)** |  |  |
| - Office 365 GCC | GA | - |
| - Office 365 GCC High | - | GA |
| - Office 365 DoD | - | GA |
| **[Microsoft Defender pour Identity](../../sentinel/data-connectors-reference.md#microsoft-defender-for-identity)** |  |  |
| - Office 365 GCC | Version préliminaire publique | - |
| - Office 365 GCC High | - | Non disponible |
| - Office 365 DoD | - | Non disponible |
| **[Microsoft Defender pour Office 365](../../sentinel/data-connectors-reference.md#microsoft-defender-for-office-365)** |  |  |
| - Office 365 GCC | Version préliminaire publique | - |
| - Office 365 GCC High | - | Non disponible |
| - Office 365 DoD | - | Non disponible |
| **[Office 365](../../sentinel/data-connectors-reference.md#microsoft-office-365)** |  |  |
| - Office 365 GCC | GA | - |
| - Office 365 GCC High | - | GA |
| - Office 365 DoD | - | GA |
|  |  |

<a name="azure-defender-for-iot"></a>

## <a name="microsoft-defender-for-iot"></a>Microsoft Defender pour IoT

Microsoft Defender pour IoT vous permet d’accélérer l’innovation IoT/OT avec une sécurité complète sur l’ensemble de vos appareils IoT/OT.Pour les organisations d’utilisateurs finaux, Microsoft Defender pour IoT offre une sécurité sans agent, au niveau de la couche réseau, qui est rapidement déployée, fonctionne avec divers équipements industriels et interagit avec Microsoft Sentinel et d’autres outils SOC. La solution peut être déployée localement ou dans des environnements connectés à Azure.Pour les fabricants d’appareils IoT, les agents de sécurité de Microsoft Defender pour IoT vous permettent d’intégrer la sécurité directement dans vos nouveaux appareils IoT et projets Azure IoT. Le micro-agent dispose d’options de déploiement souples, notamment la possibilité de le déployer sous forme de package binaire ou de modifier le code source. Le micro-agent est également disponible pour les systèmes d’exploitation IoT standard tels que Linux et Azure RTOS. Pour plus d’informations, consultez la [documentation de Microsoft Defender pour IoT](../../defender-for-iot/index.yml).

Le tableau suivant présente la disponibilité actuelle des fonctionnalités de Microsoft Defender pour IoT dans Azure et Azure Government.

### <a name="for-organizations"></a>Pour les entreprises

| Fonctionnalité | Azure | Azure Government |
|--|--|--|
| [Inventaire et détection des appareils locaux](../../defender-for-iot/how-to-investigate-all-enterprise-sensor-detections-in-a-device-inventory.md) | GA | GA |
| [Gestion des vulnérabilités](../../defender-for-iot/how-to-create-risk-assessment-reports.md) | GA | GA |
| [Détection des menaces avec IoT et analyse comportementale OT](../../defender-for-iot/how-to-work-with-alerts-on-your-sensor.md) | GA | GA |
| [Mises à jour manuelles et automatiques de Threat Intelligence](../../defender-for-iot/how-to-work-with-threat-intelligence-packages.md) | GA | GA |
| **Unifier la sécurité OT et IT avec SIEM, SOAR et XDR** |  |  |
| [Active Directory](../../defender-for-iot/organizations/how-to-create-and-manage-users.md#integrate-with-active-directory-servers) | GA | GA |
| [ArcSight](../../defender-for-iot/organizations/how-to-accelerate-alert-incident-response.md#accelerate-incident-workflows-by-using-alert-groups) | GA | GA |
| [ClearPass (alertes et inventaire)](../../defender-for-iot/organizations/how-to-install-software.md#attach-a-span-virtual-interface-to-the-virtual-switch) | GA | GA |
| [CyberArk PSM](../../defender-for-iot/organizations/concept-key-concepts.md#integrations) | GA | GA |
| [E-mail](../../defender-for-iot/organizations/how-to-forward-alert-information-to-partners.md#email-address-action) | GA | GA |
| [FortiGate](../../defender-for-iot/organizations/tutorial-fortinet.md) | GA | GA |
| [FortiSIEM](../../defender-for-iot/organizations/tutorial-fortinet.md) | GA | GA |
| [Microsoft Sentinel](../../defender-for-iot/organizations/how-to-configure-with-sentinel.md) | Version préliminaire publique | Version préliminaire publique |
| [NetWitness](../../defender-for-iot/organizations/how-to-forward-alert-information-to-partners.md#netwitness-action) | GA | GA |
| [Palo Alto NGFW](../../defender-for-iot/organizations/tutorial-palo-alto.md) | GA | GA |
| [Palo Alto Panorama](../../defender-for-iot/organizations/tutorial-palo-alto.md) | GA | GA |
| [ServiceNow (alertes et inventaire)](../../defender-for-iot/organizations/tutorial-servicenow.md) | GA | GA |
| [Analyse SNMP MIB](../../defender-for-iot/organizations/how-to-set-up-snmp-mib-monitoring.md) | GA | GA |
| [Splunk](../../defender-for-iot/organizations/tutorial-splunk.md) | GA | GA |
| [Serveur SYSLOG (format CEF)](../../defender-for-iot/organizations/how-to-forward-alert-information-to-partners.md#syslog-server-actions) | GA | GA |
| [Serveur SYSLOG (format LEEF)](../../defender-for-iot/organizations/how-to-forward-alert-information-to-partners.md#syslog-server-actions) | GA | GA |
| [Serveur SYSLOG (objet)](../../defender-for-iot/organizations/how-to-forward-alert-information-to-partners.md#syslog-server-actions) | GA | GA |
| [Serveur SYSLOG (SMS)](../../defender-for-iot/organizations/how-to-forward-alert-information-to-partners.md#syslog-server-actions) | GA | GA |
| [Rappel web (webhook)](../../defender-for-iot/organizations/how-to-forward-alert-information-to-partners.md#webhook-server-action) | GA | GA |

### <a name="for-device-builders"></a>Pour les fabricants d’appareils

| Fonctionnalité | Azure | Azure Government |
|--|--|--|
| [Micro-agent pour Azure RTOS](../../defender-for-iot/iot-security-azure-rtos.md) | GA | GA |
| [Configurer Sentinel avec Microsoft Defender pour IoT](../../defender-for-iot/how-to-configure-with-sentinel.md) | Version préliminaire publique | Version préliminaire publique |
| **Micro-agent autonome pour Linux** |  |  |
| [Installation du fichier binaire de l’agent autonome](../../defender-for-iot/quickstart-standalone-agent-binary-installation.md) | Version préliminaire publique | Version préliminaire publique |

## <a name="azure-attestation"></a>Azure Attestation

Microsoft Azure Attestation est une solution unifiée permettant de vérifier à distance la fiabilité d’une plateforme et l’intégrité des ressources binaires qui s’y exécutent. Le service reçoit des preuves de la plateforme, les valide avec les normes de sécurité, les évalue par rapport à des stratégies configurables et génère un jeton d’attestation pour les applications basées sur les revendications (par exemple, les parties de confiance, les autorités d’audit). 

Azure Attestation est actuellement disponible dans plusieurs régions dans les clouds publics et gouvernementaux Azure. Dans Azure Government, le service est disponible en préversion sur Gouvernement américain - Virginie et Gouvernement des États-Unis – Arizona. 

Pour plus d’informations, consultez la [documentation publique sur Azure Attestation](/azure/attestation/overview). 

| Fonctionnalité | Azure | Azure Government |
|--|--|--|
| [Expérience du portail](/azure/attestation/quickstart-portal) pour effectuer des opérations de plan de contrôle et de plan de données | GA | - |
| [Expérience PowerShell](/azure/attestation/quickstart-powershell) pour effectuer des opérations de plan de contrôle et de plan de données  | GA | GA |
| Application de TLS 1.2   | GA | GA |
| Prise en charge de la continuité d’activité et reprise d’activité   | GA | - |
| [Intégration des étiquettes de service](/azure/virtual-network/service-tags-overview) | GA | GA |
| [Stockage de journal immuable](/azure/attestation/audit-logs) | GA | GA |
| Isolement réseau à l’aide d’une liaison privée | Version préliminaire publique | - |
| [Certification FedRAMP High](/azure/azure-government/compliance/azure-services-in-fedramp-auditscope) | GA | - |
| Customer Lockbox | GA | - |

## <a name="next-steps"></a>Étapes suivantes

- Découvrez le modèle de [responsabilité partagée](shared-responsibility.md) et apprenez à distinguer les tâches de sécurité qui dépendent du fournisseur de cloud de celles qui vous incombent.
- Découvrez les fonctionnalités [Azure Government Cloud](../../azure-government/documentation-government-welcome.md) ainsi que la conception et la sécurité sans faille utilisées pour prendre en charge la conformité applicable aux organisations gouvernementales fédérales, d'état et locales et à leurs partenaires.
- Découvrez le [plan Office 365 Government](/office365/servicedescriptions/office-365-platform-service-description/office-365-us-government/office-365-us-government#about-office-365-government-environments).
- Découvrez la [conformité Azure](../../compliance/index.yml) pour les normes légales et réglementaires.
