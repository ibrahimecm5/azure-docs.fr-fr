---
title: Sécurité de bout en bout dans Azure | Microsoft Docs
description: Cet article fournit une carte des services Azure qui vous permettent de sécuriser et protéger vos ressources cloud, ainsi que de détecter et d’enquêter sur les menaces.
services: security
documentationcenter: na
author: TerryLanfear
manager: rkarlin
ms.assetid: a5a7f60a-97e2-49b4-a8c5-7c010ff27ef8
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 4/07/2021
ms.author: terrylan
ms.openlocfilehash: eb96e21644f6318320ee8a5c7c33b83a73a59c94
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132335496"
---
# <a name="end-to-end-security-in-azure"></a>Sécurité de bout en bout dans Azure
L’une des meilleures raisons d’utiliser Azure pour vos applications et services est de tirer parti de sa large gamme de fonctionnalités et outils de sécurité. Ces outils et fonctionnalités permettent de créer des solutions sécurisées sur la plateforme Azure sécurisée. Microsoft Azure assure la confidentialité, l’intégrité et la disponibilité des données client, tout en permettant la gestion transparente des responsabilités.

Le diagramme et la documentation qui suivent vous présentent les services de sécurité dans Azure. Ces services de sécurité vous aident à répondre aux besoins de votre entreprise en matière de sécurité et de protection de vos utilisateurs, appareils, ressources, données et applications dans le cloud.

## <a name="microsoft-security-services-map"></a>Carte des services de sécurité Microsoft

La carte des services de sécurité organise les services selon les ressources qu’ils protègent (colonne). Le diagramme regroupe également les services dans les catégories suivantes (ligne) :

- Sécuriser et protéger : Les services qui vous permettent d’implémenter une stratégie de défense en profondeur et à plusieurs couches sur l’identité, les hôtes, les réseaux et les données. Cette collection de services et de fonctionnalités de sécurité permet de comprendre et d’améliorer votre posture de sécurité dans l’ensemble de votre environnement Azure.
- Détecter les menaces : Les services qui identifient les activités suspectes et facilitent l’atténuation de la menace.
- Examiner et répondre : Les services qui extraient les données de journalisation afin que vous puissiez évaluer une activité suspecte et y répondre.

Le diagramme inclut le programme Benchmark de sécurité Azure, un ensemble de recommandations de sécurité à fort impact qui peuvent vous permettre de sécuriser les services que vous utilisez dans Azure.

:::image type="content" source="media/end-to-end/security-diagram.svg" alt-text="Diagramme montrant les services de sécurité de bout en bout dans Azure." border="false":::

## <a name="security-controls-and-baselines"></a>Contrôles de sécurité et lignes de base
Le programme [Benchmark de sécurité Azure](../benchmarks/introduction.md) comprend un ensemble de recommandations de sécurité à fort impact qui peuvent vous permettre de sécuriser les services que vous utilisez dans Azure :

- Contrôles de sécurité - Ces recommandations s’appliquent généralement à votre locataire Azure et à vos services Azure. Chaque recommandation identifie une liste des parties prenantes qui sont généralement impliquées dans la planification, l’approbation ou l’implémentation du benchmark.
- Lignes de base de service - Elles appliquent les contrôles à des services Azure individuels pour fournir des recommandations sur la configuration de la sécurité de ces services.

## <a name="secure-and-protect"></a>Sécuriser et protéger

:::image type="content" source="media/end-to-end/secure-and-protect.svg" alt-text="Diagramme montrant les services Azure qui vous aident à sécuriser et protéger vos ressources cloud." border="false":::

| Service | Description |
|------|--------|
| [Microsoft Defender pour le cloud](../../security-center/security-center-introduction.md)| Un système de gestion de la sécurité de l’infrastructure unifié qui renforce la posture de sécurité de vos centres de données et fournit une protection avancée contre les menaces pour vos charges de travail hybrides dans le cloud (dans Azure ou non), ainsi qu’en local. |
| **Identité&nbsp;&&nbsp;Accès&nbsp;Gestion** | |
| [Azure Active Directory (AD)](../../active-directory/fundamentals/active-directory-whatis.md)| Le service Microsoft basé sur le cloud qui gère les identités et les accès.  |
|  | L’[accès conditionnel](../../active-directory/conditional-access/overview.md) est l’outil utilisé par Azure AD pour réunir des signaux d’identité, prendre des décisions et appliquer des stratégies d’organisation. |
|  | [Domain Services](../../active-directory-domain-services/overview.md) est l’outil utilisé par Azure AD pour fournir des services de domaine managé, comme la jonction de domaine, la stratégie de groupe, le protocole LDAP et l’authentification Kerberos/NTLM. |
|  | [Privileged Identity Management (PIM)](../../active-directory/privileged-identity-management/pim-configure.md) est un service dans Azure AD qui vous permet de gérer, de contrôler et de superviser l’accès aux ressources importantes de votre organisation. |
|  | [Multi-Factor Authentication](../../active-directory/authentication/concept-mfa-howitworks.md) est l’outil utilisé par Azure AD pour protéger l’accès aux données et aux applications en exigeant une deuxième forme d’authentification. |
| [Azure AD Identity Protection](../../active-directory/identity-protection/overview-identity-protection.md) | Un outil qui permet aux organisations d’automatiser la détection et la correction des risques sur la base de l’identité, d’examiner les risques à l’aide des données du portail et d’exporter les données de détection des risques vers des utilitaires tiers pour une analyse plus poussée. |
| **Infrastructure&nbsp;&&nbsp;Network** |  |
| [Passerelle VPN](../../vpn-gateway/vpn-gateway-about-vpngateways.md) | Une passerelle de réseau virtuel qui est utilisée pour envoyer du trafic chiffré entre un réseau virtuel Azure et un emplacement local sur l’Internet public et pour envoyer le trafic chiffré entre réseaux virtuels Azure sur le réseau Microsoft. |
| [Azure DDoS Protection Standard](../../ddos-protection/ddos-protection-overview.md) | Offre des fonctionnalités améliorées d’atténuation DDoS pour la défense contre les attaques DDoS. Cette solution s’adapte automatiquement pour protéger vos ressources Azure spécifiques dans un réseau virtuel. |
| [Azure Front Door](../../frontdoor/front-door-overview.md) | Un point d’entrée mondial scalable qui utilise le réseau de périphérie mondial de Microsoft pour créer des applications web rapides, sécurisées et très scalables. |
| [Pare-feu Azure](../../firewall/overview.md) | Service de sécurité réseau cloud managé qui protège vos ressources Réseau virtuel Azure. Il s’agit d’un service de pare-feu avec état intégral, doté d’une haute disponibilité intégrée et d’une scalabilité illimitée dans le cloud. |
| [Azure Key Vault](../../key-vault/general/overview.md) | Un magasin de secrets sécurisé pour les jetons, les mots de passe, les certificats, les clés API et d’autres secrets. Key Vault simplifie la création et le contrôle des clés de chiffrement utilisées pour chiffrer vos données. |
| [HSM managé Key Vault](../../key-vault/managed-hsm/overview.md) | Un service cloud complètement managé, à haut niveau de disponibilité et monolocataire qui vous permet de protéger les clés de chiffrement de vos applications cloud à l’aide de HSM certifiés FIPS 140-2 de niveau 3. |
| [Azure Private Link](../../private-link/private-link-overview.md) | Vous permet d’accéder aux services Azure PaaS (par exemple Stockage Azure et SQL Database) ainsi qu’aux services de partenaires ou de clients hébergés par Azure sur un point de terminaison privé dans votre réseau virtuel. |
| [Application Gateway Azure](../../application-gateway/overview.md) | Un équilibreur de charge avancé pour le trafic web qui vous permet de gérer le trafic vers vos applications web. Application Gateway peut prendre des décisions de routage basées sur des attributs supplémentaires d’une requête HTTP, par exemple des en-têtes d’hôte ou le chemin d’un URI. |
| [Azure Service Bus](../../service-bus-messaging/service-bus-messaging-overview.md) | Un répartiteur de messages d’entreprise complètement managé, avec des files d’attente de messages et des rubriques de publication/abonnement. Service Bus est utilisé pour découpler les applications et les services les uns des autres. |
| [Pare-feu d’applications web](../../web-application-firewall/overview.md) | Protège de manière centralisée vos applications web contre les vulnérabilités et attaques courantes. WAF peut être déployé avec Azure Application Gateway et Azure Front Door. |
| **Données et application** |  |
| [Azure Backup](../../backup/backup-overview.md) | Fournit des solutions simples, sécurisées et rentables pour sauvegarder vos données et les récupérer à partir du cloud Microsoft Azure. |
| [Chiffrement du service de stockage Azure](../../storage/common/storage-service-encryption.md) | Chiffre automatiquement les données avant leur stockage et déchiffre automatiquement les données lorsque vous les récupérez. |
| [Azure Information Protection](/azure/information-protection/what-is-information-protection) | Une solution basée sur le cloud qui permet aux organisations de découvrir, classifier et protéger les documents et les e-mails en appliquant des étiquettes au contenu. |
| [Gestion des API](../../api-management/api-management-key-concepts.md) | Une méthode permettant de créer des passerelles API cohérentes et modernes pour des services back-end existants. |
| [Informatique confidentielle Azure](../../confidential-computing/overview.md) | Vous permet d’isoler vos données sensibles pendant qu’elles sont traitées dans le cloud. |
| [Azure DevOps](/azure/devops/user-guide/what-is-azure-devops) | Vos projets de développement bénéficient de plusieurs couches de technologies de sécurité et de gouvernance, de pratiques opérationnelles et de stratégies de conformité lorsqu’ils sont stockés dans Azure DevOps. |
| **Accès client** |  |
| [Azure AD External Identities](../../active-directory/external-identities/compare-with-b2c.md) | Avec les identités externes dans Azure AD, vous pouvez autoriser des personnes extérieures à votre organisation à accéder à vos applications et ressources tout en leur permettant de se connecter à l'aide de l'identité de leur choix. |
|  | Vous pouvez partager vos applications et vos ressources avec des utilisateurs externes via la collaboration [Azure AD B2B](../../active-directory/external-identities/what-is-b2b.md). |
|  | [Azure AD B2C](../../active-directory-b2c/overview.md) vous permet de prendre en charge des millions d’utilisateurs et des milliards d’authentifications par jour, de surveiller et de gérer automatiquement les menaces, comme les attaques par déni de service, la pulvérisation de mots de passe ou les attaques par force brute. |

## <a name="detect-threats"></a>Détecter les menaces

:::image type="content" source="media/end-to-end/detect-threats.svg" alt-text="Diagramme montrant les services Azure qui détectent les menaces." border="false":::

| Service | Description |
|------|--------|
| [Microsoft Defender pour le cloud](../../security-center/azure-defender.md) | Offre une protection avancée et intelligente pour vos charges de travail et vos ressources Azure et hybrides. Le tableau de bord de la protection de charge de travail dans Defender pour le cloud offre une visibilité et un contrôle des fonctionnalités de la protection de charge de travail cloud pour votre environnement. |
| [Microsoft Sentinel](../../sentinel/overview.md) | Une solution native cloud et évolutive de type SIEM (Security Information and Event Management) et SOAR (Security Orchestrated Automated Response). Sentinel assure une analyse de sécurité intelligente et fournit des informations sur les menaces dans l’ensemble de l’entreprise. Elle constitue une solution unique pour la détection des alertes, la visibilité des menaces, la chasse proactive et la réponse face aux menaces. |
| **Identité&nbsp;&&nbsp;Accès&nbsp;Gestion** |  |
| [Microsoft 365 Defender](/microsoft-365/security/defender/microsoft-365-defender) | Une suite unifiée de défense d’entreprise efficace avant ou après une violation, qui coordonne en mode natif la détection, la prévention, l’investigation et la réponse sur l’ensemble des points de terminaison, des identités, des e-mails et des applications afin d’offrir une protection intégrée contre les attaques sophistiquées. |
|  | [Microsoft Defender pour point de terminaison](/microsoft-365/security/defender-endpoint/microsoft-defender-endpoint) est une plateforme de sécurité des points de terminaison d’entreprise conçue pour aider les réseaux d’entreprise à prévenir, détecter, enquêter et répondre aux menaces avancées. |
|  | [Microsoft Defender pour Identity](/defender-for-identity/what-is) est une solution de sécurité cloud qui s’appuie sur vos signaux Active Directory locaux pour identifier, détecter et investiguer les menaces avancées, les identités compromises et les actions des utilisateurs internes malveillants dirigées contre votre entreprise. |
| [Azure AD Identity Protection](../../active-directory/identity-protection/howto-identity-protection-configure-notifications.md) | Envoie deux types d’e-mails de notification automatisés pour vous aider à gérer les risques des utilisateurs et les détections de risques : les e-mails d’utilisateurs à risque détectés et les e-mails de synthèse hebdomadaire. |
| **Infrastructure et réseau** |  |
| [Microsoft Defender pour IoT](../../defender-for-iot/overview.md) | Une solution de sécurité unifiée pour l’identification des appareils, des vulnérabilités et des menaces de l’IoT/OT. Elle vous permet de sécuriser l’ensemble de votre environnement IoT/OT, que vous ayez besoin de protéger des appareils IoT/OT existants ou de renforcer la sécurité de nouvelles innovations IoT. |
| [Azure Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md) | Fournit des outils permettant de surveiller, de diagnostiquer et d’afficher des métriques, ainsi que d’activer et de désactiver les journaux des ressources se trouvant dans un réseau virtuel Azure. Network Watcher est conçu pour superviser et réparer l’intégrité réseau des produits IaaS, y compris les machines virtuelles, les réseaux virtuels, les passerelles d’application et les équilibreurs de charge. |
| [Journalisation d’audit Azure Policy](../../governance/policy/overview.md) | Aide à appliquer les normes organisationnelles et à évaluer la conformité à grande échelle. Azure Policy utilise les journaux d’activité, qui sont automatiquement activés, pour inclure la source de l’événement, la date, l’utilisateur, l’horodatage, les adresses sources, les adresses de destination et d’autres éléments utiles. |
| **Données et application** |  |
| [Microsoft Defender pour les registres de conteneurs](../../security-center/defender-for-container-registries-introduction.md) | Inclut un scanner de vulnérabilité pour analyser les images dans vos registres Azure Container Registry basés sur Azure Resource Manager et fournir une visibilité plus poussée des vulnérabilités de vos images. |
| [Microsoft Defender pour Kubernetes](../../security-center/defender-for-kubernetes-introduction.md) | Fournit une protection contre les menaces au niveau du cluster en supervisant vos services managés par AKS, via les journaux récupérés par Azure Kubernetes Service (AKS). |
| [Microsoft Defender for Cloud Apps](/cloud-app-security/what-is-cloud-app-security) | Une instance Cloud Access Security Broker (CASB) qui fonctionne sur plusieurs clouds. Il offre une grande visibilité, un contrôle sur les déplacements des données et une analytique avancée pour identifier et combattre les cybermenaces sur l'ensemble de vos services cloud. |

## <a name="investigate-and-respond"></a>Examiner et répondre

:::image type="content" source="media/end-to-end/investigate-and-respond.svg" alt-text="Diagramme montrant les services Azure qui vous aident à examiner les menaces et à y répondre." border="false":::

| Service | Description |
|------|--------|
| [Microsoft Sentinel](../../sentinel/hunting.md) | Des outils puissants dédiés à la recherche et la création de requêtes pour repérer les menaces de sécurité dans les sources de données de votre organisation. |
| [Azure&nbsp;Monitor&nbsp; : journaux&nbsp;et&nbsp;métriques](../../azure-monitor/overview.md) | Offre une solution complète pour collecter, analyser et exploiter des données de télémétrie de vos environnements cloud et locaux. Azure Monitor [collecte et agrège les données](../../azure-monitor/data-platform.md#observability-data-in-azure-monitor) à partir de diverses sources dans une plateforme de données commune où elles peuvent être utilisées à des fins d’analyse, de visualisation et d’alerte. |
| **Identité&nbsp;&&nbsp;Accès&nbsp;Gestion** |  |
| [Azure&nbsp;AD&nbsp; : rapports&nbsp;et&nbsp;surveillance](../../active-directory/reports-monitoring/index.yml) | Les [rapports Azure AD](../../active-directory/reports-monitoring/overview-reports.md) fournissent une vue complète de l’activité dans votre environnement. |
|  | La [surveillance Azure AD](../../active-directory/reports-monitoring/overview-monitoring.md) vous permet d’acheminer vos journaux d’activité Azure AD vers différents points de terminaison.|
| [Historique des audits Azure AD PIM](../../active-directory/privileged-identity-management/pim-how-to-use-audit-log.md) | Affiche toutes les activations et attributions de rôle au cours des 30 derniers jours pour tous les rôles privilégiés. |
| **Données et application** |  |
| [Microsoft Defender for Cloud Apps](/cloud-app-security/investigate) | Fournit des outils pour mieux comprendre ce qui se passe dans votre environnement cloud. |

## <a name="next-steps"></a>Étapes suivantes

- Comprendre votre [responsabilité partagée dans le cloud](shared-responsibility.md).

- Comprenez les [choix d’isolation dans le cloud Azure](isolation-choices.md) contre les utilisateurs malveillants et non malveillants.
