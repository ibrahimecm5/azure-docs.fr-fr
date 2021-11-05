---
title: Guide des opérations de sécurité dans Azure Active Directory
description: Découvrez comment superviser, identifier et créer des alertes pour les problèmes de sécurité liés aux comptes, aux applications, aux appareils et à l’infrastructure
services: active-directory
author: BarbaraSelden
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 07/15/2021
ms.author: baselden
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: a0e35c11a94e1c0c6e86964d68c784c5cb3e7741
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131060190"
---
# <a name="azure-active-directory-security-operations-guide"></a>Guide des opérations de sécurité dans Azure Active Directory

Microsoft utilise une approche robuste de la [sécurité Confiance Zéro](https://aka.ms/Zero-Trust). Celle-ci suit les principes de la [Défense en profondeur](https://us-cert.cisa.gov/bsi/articles/knowledge/principles/defense-in-depth) qui utilise l’identité comme plan de contrôle. Les organisations étant de plus en plus nombreuses à adopter des charges de travail hybrides pour bénéficier de la mise à l’échelle, d’une réduction de coûts et d’une meilleure sécurité, Azure Active Directory (Azure AD) joue un rôle pivot dans votre stratégie de gestion des identités. Récemment, les actualités relatives aux usurpations d’identité et aux violations de sécurité au sein des entreprises ont amené les services informatiques à voir leur posture sur la sécurité des identités comme un moyen de mesurer la réussite de leurs systèmes de protection.

De plus en plus, les organisations doivent adopter une combinaison d’applications locales et cloud, auxquelles les utilisateurs accèdent à l’aide de comptes locaux et de comptes cloud uniquement. La gestion des utilisateurs, des applications et des appareils, à la fois en local et dans le cloud, donne lieu à des scénarios qui ne sont pas simples.

Azure Active Directory crée une identité d’utilisateur unique commune pour l’authentification et l’autorisation d’accès à toutes les ressources, où qu’elles soient. Nous appelons cette identité « identité hybride ». 

Pour obtenir l’identité hybride avec Azure AD, l'une des trois méthodes d’authentification peut être utilisée, selon vos scénarios. Ces trois méthodes sont les suivantes :

* [Synchronisation de hachage de mot de passe (PHS)](../hybrid/whatis-phs.md)

* [Authentification directe (PTA)](../hybrid/how-to-connect-pta.md)

* [Fédération (AD FS)](../hybrid/whatis-fed.md)

Lorsque vous auditez vos opérations de sécurité actuelles ou établissez des opérations de sécurité pour votre environnement Azure, nous vous recommandons d’effectuer ce qui suit :

* Lire certaines sections du guide de sécurité Microsoft pour établir une base de connaissances sur la sécurisation de votre environnement Azure hybride ou basé sur le cloud.

* Auditer votre stratégie de compte et de mot de passe, ainsi que les méthodes d’authentification pour prévenir les vecteurs d’attaque les plus courants.

* Créer une stratégie pour la supervision continue et la génération d’alertes concernant les activités qui peuvent constituer une menace pour la sécurité.

## <a name="audience"></a>Public visé

Le guide Azure AD SecOps est destiné aux équipes responsables des identités et de la sécurité informatique, ainsi qu’aux fournisseurs de services managés qui doivent contrer les menaces grâce à de meilleurs profils de configuration et de supervision de la sécurité des identités. Ce guide s’adresse tout particulièrement aux administrateurs informatiques et aux architectes d’identités qui conseillent les équipes chargées des tests d’intrusion au sein du Centre des opérations de sécurité, dans le but d’améliorer et de conserver leur posture sur la sécurité des identités. 

## <a name="scope"></a>Étendue

Cette introduction fournit des suggestions de lectures préalables, ainsi que des recommandations sur l’audit des mots de passe et les stratégies. Cet article présente également les outils disponibles pour les environnements Azure hybrides, ainsi que pour les environnements Azure basés sur le cloud. Enfin, nous fournissons une liste de sources de données que vous pouvez utiliser pour la supervision et la génération d’alertes, ainsi que pour configurer votre environnement et votre stratégie SIEM. La suite de ce guide présente des stratégies de supervision et de génération d’alertes pour les domaines suivants :

* [Comptes d’utilisateur](security-operations-user-accounts.md) : Conseils sur les comptes d’utilisateur non privilégiés sans privilèges administratifs, notamment en cas de création et d’utilisation de comptes anormaux, ainsi que de connexions inhabituelles.

* [Comptes privilégiés](security-operations-privileged-accounts.md) : Conseils sur les comptes d’utilisateur privilégiés qui ont des autorisations élevées pour effectuer des tâches d’administration, y compris des attributions de rôles Azure AD, des attributions de rôles de ressources Azure et la gestion des accès pour les ressources et les abonnements Azure.

* [Privileged Identity Management (PIM)](security-operations-privileged-identity-management.md) : Conseils sur l’utilisation de PIM pour gérer, contrôler et superviser l’accès aux ressources. 

* [Applications](security-operations-applications.md) : Conseils sur les comptes utilisés pour fournir l’authentification aux applications. 

* [Appareils](security-operations-devices.md) : Conseils sur la supervision et la génération d’alertes en cas d’appareils inscrits ou joints en dehors des stratégies, d’utilisation non conforme, de gestion des rôles d’administration des appareils et de connexions aux machines virtuelles.

*  [Infrastructure](security-operations-infrastructure.md) : Conseils sur la supervision et la génération d’alertes concernant les menaces pesant sur vos environnements hybrides et purement basés sur le cloud.

## <a name="important-reference-content"></a>Contenu de référence important

Microsoft propose de nombreux produits et services qui vous permettent de personnaliser votre environnement informatique en fonction de vos besoins. Nous vous recommandons, dans le cadre de votre stratégie de supervision et de génération d’alertes, de passer en revue les conseils suivants qui s’appliquent à votre environnement d’exploitation :

* Systèmes d’exploitation Windows

   * [Informations de référence sur la supervision et l’audit de sécurité dans Windows 10 et Windows Server 2016](https://www.microsoft.com/download/details.aspx?id=52630)

   * [Base de référence de sécurité (FINALE) pour Windows 10 v1909 et Windows Server v1909](https://techcommunity.microsoft.com/t5/microsoft-security-baselines/security-baseline-final-for-windows-10-v1909-and-windows-server/ba-p/1023093)

   * [Base de référence de sécurité pour Windows 11](https://techcommunity.microsoft.com/t5/microsoft-security-baselines/windows-11-security-baseline/ba-p/2810772)
   
   * [Base de référence de sécurité pour Windows Server 2022](https://techcommunity.microsoft.com/t5/microsoft-security-baselines/windows-server-2022-security-baseline/ba-p/2724685)
   
* Environnements locaux

   * [Architecture de Microsoft Defender pour Identity](/defender-for-identity/architecture)

   * [Démarrage rapide : Connecter Microsoft Defender pour Identity à Active Directory](/defender-for-identity/install-step2)

   * [Base de référence de sécurité Azure pour Microsoft Defender pour Identity](/defender-for-identity/security-baseline)

   * [Supervision d’Active Directory pour détecter des signes de compromission](/windows-server/identity/ad-ds/plan/security-best-practices/monitoring-active-directory-for-signs-of-compromise)

* Environnements Azure basés sur le cloud


   * [Superviser les connexions à l’aide du journal des connexions Azure AD](../reports-monitoring/concept-all-sign-ins.md)

   * [Rapports d’activité d’audit dans le portail Azure Active Directory](../reports-monitoring/concept-audit-logs.md)

   * [Investiguer les risques avec Azure Active Directory Identity Protection](../identity-protection/howto-identity-protection-investigate-risk.md) 

   * [Connecter les données Azure AD Identity Protection à Azure Sentinel](../../sentinel/data-connectors-reference.md#azure-active-directory-identity-protection)

* Active Directory Domain Services (AD DS)

   * [Recommandations en matière de stratégie d’audit](/windows-server/identity/ad-ds/plan/security-best-practices/audit-policy-recommendations)

* Active Directory Federation Services (AD FS)

   * [Résolution des problèmes AD FS - Audit des événements et de la journalisation](/windows-server/identity/ad-fs/troubleshooting/ad-fs-tshoot-logging)

## <a name="data-sources"></a>Sources de données 

Les fichiers journaux que vous pouvez utiliser pour l’investigation et la supervision sont les suivants :

* [Journaux d’audit Azure AD](../reports-monitoring/concept-audit-logs.md)

* [Journaux de connexion](../reports-monitoring/concept-all-sign-ins.md)

* [Journaux d’audit Microsoft 365](/microsoft-365/compliance/auditing-solutions-overview)

* [Journaux Azure Key Vault](../../key-vault/general/logging.md?tabs=Vault)

Dans le portail Azure, vous pouvez afficher les journaux d’audit Azure AD et les télécharger sous forme de fichiers CSV ou JSON. Le Portail Azure offre plusieurs méthodes d’intégration entre les journaux Azure AD et d’autres outils, ce qui permet une plus grande automatisation du monitoring et des alertes :

* **[Azure Sentinel](../../sentinel/overview.md)** – Permet une analytique de sécurité intelligente au niveau de l’entreprise en fournissant des fonctionnalités d’informations de sécurité et de gestion d’événements management (SIEM). 

* **[Azure Monitor](../../azure-monitor/overview.md)** – Permet de créer des alertes et supervisions automatisées de diverses conditions. Peut créer ou utiliser des classeurs pour combiner des données provenant de différentes sources.

* **[Azure Event Hubs](../../event-hubs/event-hubs-about.md) avec intégration SIEM**- [Les journaux Azure AD peuvent être intégrés à d’autres SIEM](../reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md) comme Splunk, ArcSight, QRadar et Sumo Logic via l’intégration Azure Event Hub Hub.

* **[Microsoft Cloud App Security (MCAS)](/cloud-app-security/what-is-cloud-app-security)**  : permet de découvrir et de gérer les applications, de gouverner toutes les applications et ressources, et de vérifier la conformité des applications cloud.

La plupart des éléments qui font l’objet d’une supervision et d’alertes sont déterminés par vos stratégies d’accès conditionnel. Vous pouvez utiliser le [classeur Rapports et insights sur l’accès conditionnel](../conditional-access/howto-conditional-access-insights-reporting.md) pour examiner les effets d’une ou de plusieurs stratégies d’accès conditionnel sur vos connexions, ainsi que les résultats des stratégies, y compris l’état de l’appareil. Ce classeur permet de voir un résumé de l’impact et d’identifier l’incidence sur une période donnée. Vous pouvez également vous en servir pour examiner les connexions d’un utilisateur spécifique. 

Le reste de cet article comprend des recommandations concernant la supervision et les alertes, qui sont organisées par type de menace. Lorsqu’il existe des solutions prédéfinies spécifiques, nous en indiquons le lien ou donnons des exemples après le tableau. Sinon, vous pouvez créer des alertes à l’aide des outils précédents. 

* **[Identity Protection](../identity-protection/overview-identity-protection.md)**  : génère trois rapports clés que vous pouvez utiliser dans le cadre de votre investigation :

   * **Utilisateurs à risque** : contient des informations sur les utilisateurs à risque, des détails sur les détections, l’historique de toutes les connexions risquées et l’historique des risques.

   * **Connexions risquées** : contient des informations concernant le contexte d’une connexion potentiellement suspecte. Pour plus d’informations sur l’investigation des informations de ce rapport, consultez [Procédure : Examiner les risques](../identity-protection/howto-identity-protection-investigate-risk.md). 

   *  **Détection des risques** : contient des informations sur les signaux détectés par Azure AD Identity Protection qui indiquent les utilisateurs à risque et les connexions risquées. Pour plus d’informations, consultez le [Guide des opérations de sécurité Azure AD pour les comptes d’utilisateur](security-operations-user-accounts.md).

### <a name="data-sources-for-domain-controller-monitoring"></a>Sources de données pour la supervision des contrôleurs de domaine

Pour des résultats optimaux, nous vous recommandons de superviser vos contrôleurs de domaine à l’aide de Microsoft Defender pour Identity. Cela vous permettra de bénéficier des meilleures fonctionnalités d’automatisation et de détection. Suivez les instructions fournies dans :

* [Architecture de Microsoft Defender pour Identity](/defender-for-identity/architecture)

* [Démarrage rapide : Connecter Microsoft Defender pour Identity à Active Directory](/defender-for-identity/install-step2)

Si vous ne prévoyez pas d’utiliser Microsoft Defender pour Identity, vous pouvez [superviser vos contrôleurs de domaine soit à l’aide des messages du journal des événements](/windows-server/identity/ad-ds/plan/security-best-practices/monitoring-active-directory-for-signs-of-compromise), soit [en exécutant des applets de commande PowerShell](/windows-server/identity/ad-ds/deploy/troubleshooting-domain-controller-deployment). 

## <a name="components-of-hybrid-authentication"></a>Composants de l’authentification hybride

Dans le cadre d’un environnement hybride Azure, les éléments suivants doivent faire l’objet d’une base de référence et être inclus dans votre stratégie de supervision et de génération d’alertes. 

* **Agent PTA** : l’agent d’authentification directe est utilisé pour permettre une authentification directe, et il est installé en local. Pour savoir comment vérifier votre version de l’agent et connaître les étapes à suivre, consultez [Agent d’authentification directe Azure AD : Historique de publication des versions](../hybrid/reference-connect-pta-version-history.md). 

* **AD FS/WAP** : les services de fédération Active Directory Azure (Azure AD FS) et le proxy d’application web (WAP) permettent le partage sécurisé des identités numériques et des droits d’utilisation entre les différentes limites de sécurité et les différents services de l’entreprise. Pour plus d’informations sur les meilleures pratiques en matière de sécurité, consultez [Meilleures pratiques pour la sécurisation des services de fédération Active Directory (AD FS)](/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs). 

* **Agent Azure AD Connect Health** : agent utilisé dans le but de fournir une liaison de communication pour Azure AD Connect Health. Pour plus d’informations sur l’installation de l’agent, consultez [Installation de l’agent Azure AD Connect Health](../hybrid/how-to-connect-health-agent-install.md). 

* **Moteur de synchronisation Azure AD Connect** : composant local également appelé « moteur de synchronisation ». Pour plus d’informations sur cette fonctionnalité, consultez [Fonctionnalités du service de synchronisation Azure AD Connect](../hybrid/how-to-connect-syncservice-features.md).

* **Agent DC de protection par mot de passe** : l’agent DC de protection par mot de passe Azure permet de superviser les messages du journal des événements et de créer des rapports à partir de ces messages. Pour plus d’informations, consultez [Appliquer la fonctionnalité Protection par mot de passe Azure AD en local pour Active Directory Domain Services](../authentication/concept-password-ban-bad-on-premises.md). 

* **DLL du filtre de mot de passe** : la DLL de filtre de mot de passe de l’agent DC reçoit les demandes de validation de mot de passe utilisateur du système d’exploitation. Le filtre les transmet au service de l’agent du contrôleur de domaine qui s’exécute localement sur le contrôleur de domaine. Pour plus d’informations sur l’utilisation de la DLL, consultez [Appliquer la fonctionnalité Protection de mots de passe Azure AD localement pour Active Directory Domain Services](../authentication/concept-password-ban-bad-on-premises.md). 

* **Agent de réécriture de mot de passe** : la réécriture du mot de passe est une fonctionnalité qui est activée avec [Azure AD Connect](../hybrid/whatis-hybrid-identity.md) et qui permet aux modifications de mot de passe dans le cloud d’être réécrites dans un annuaire local existant en temps réel. Pour plus d’informations sur cette fonctionnalité, consultez [Comment fonctionne la réécriture de la réinitialisation de mot de passe en libre-service dans Azure Active Directory ?](../authentication/concept-sspr-writeback.md).

* **Application Proxy Connector Azure AD** : agents légers présents en local et qui facilitent la connexion sortante vers le service de proxy d’application. Pour plus d’informations, consultez [Présentation des connecteurs de proxy d’application Azure ADF](../app-proxy/application-proxy-connectors.md). 

## <a name="components-of-cloud-based-authentication"></a>Composants de l’authentification basée sur le cloud

Dans le cadre d’un environnement Azure basé sur le cloud, les éléments suivants doivent faire l’objet d’une base de référence et être inclus dans votre stratégie de supervision et de génération d’alertes.

* **Proxy d’application Azure AD** : ce service cloud offre un accès à distance sécurisé pour les applications web locales. Pour plus d’informations, consultez [Accès à distance aux applications locales via le service Proxy d’application Azure AD](../app-proxy/application-proxy-connectors.md). 

* **Azure AD Connect** : services utilisés pour une solution Azure AD Connect. Pour plus d’informations, consultez [Qu’est-ce qu’Azure AD Connect ?](../hybrid/whatis-azure-ad-connect.md).

* **Azure AD Connect Health** : Service Health fournit un tableau de bord personnalisable qui suit l’intégrité de vos services Azure dans les régions où vous les utilisez. Pour plus d’informations, consultez [Azure AD Connect Health](../hybrid/whatis-azure-ad-connect.md).

* **Azure MFA** : l’authentification multifacteur Azure AD nécessite qu’un utilisateur fournisse plusieurs formes de preuves pour s’authentifier. Cela peut constituer une première étape proactive pour sécuriser votre environnement. Pour plus d’informations, consultez [Fonctionnement : Authentification multifacteur Azure AD](../authentication/concept-mfa-howitworks.md).

* **Groupes dynamiques** : la configuration dynamique de l’appartenance à un groupe de sécurité pour les administrateurs Azure Active Directory (Azure AD) permet de définir des règles d’ajout d’utilisateurs aux groupes créés dans Azure AD en fonction des attributs utilisateur. Pour plus d’informations sur les groupes dynamiques, consultez [Groupes dynamiques et Azure Active Directory B2B Collaboration](../external-identities/use-dynamic-groups.md).

* **Accès conditionnel** : l’accès conditionnel est l’outil utilisé par Azure Active Directory pour réunir des signaux, prendre des décisions et appliquer des stratégies d’organisation. L’accès conditionnel est au cœur du nouveau plan de contrôle basé sur les identités. Pour plus d’informations, consultez [Qu’est-ce que l’accès conditionnel ?](../conditional-access/overview.md).

* **Identity Protection** : outil qui permet aux organisations d’automatiser la détection et la correction des risques liés à l’identité, d’investiguer les risques à l’aide des données du portail et d’exporter les données de détection des risques vers votre SIEM. Pour plus d’informations, consultez [Qu’est-ce qu’Identity Protection ?](../identity-protection/overview-identity-protection.md).

* **Gestion des licences par groupe** : les licences peuvent être attribuées à des groupes plutôt que directement à des utilisateurs. Azure AD stocke des informations sur les états d’affectation de licence pour les utilisateurs. 

* **Service de provisionnement** : le provisionnement correspond à la création d’identités utilisateur et de rôles dans les applications cloud auxquelles les utilisateurs ont besoin d’accéder. En plus de créer des identités utilisateur, l’approvisionnement automatique comprend la maintenance et la suppression d’identités utilisateur en cas de modification de l’état ou des rôles. Pour plus d’informations, consultez [Fonctionnement de l’approvisionnement d’applications dans Azure Active Directory](../app-provisioning/how-provisioning-works.md).

* **API Graph** : l’API Microsoft Graph est une API web RESTful qui vous permet d’accéder aux ressources des services cloud Microsoft. Une fois que vous avez inscrit votre application et obtenu les jetons d’authentification d’un utilisateur ou d’un service, vous pouvez adresser des demandes à l’API Microsoft Graph. Pour plus d’informations, consultez [Présentation de Microsoft Graph](/graph/overview).

* **Service de domaine** : Azure Active Directory Domain Services (AD DS) fournit des services de domaine managés, comme la jonction de domaine ou la stratégie de groupe. Pour plus d’informations, consultez [Présentation d’Azure Active Directory Domain Services](../../active-directory-domain-services/overview.md).

* **Azure Resource Manager** : Azure Resource Manager est le service de déploiement et de gestion d’Azure. Il fournit une couche de gestion qui vous permet de créer, de mettre à jour et de supprimer des ressources dans votre compte Azure. Pour plus d’informations, consultez [Qu’est-ce qu’Azure Resource Manager ?](../../azure-resource-manager/management/overview.md).

* **Identités managées** : les identités managées permettent aux développeurs de ne plus avoir à gérer les informations d’identification. Les identités managées fournissent une identité utilisée par les applications lorsqu'elles se connectent à des ressources qui prennent en charge l'authentification Azure Active Directory (Azure AD). Pour plus d’informations, consultez [Que sont les identités managées pour les ressources Azure ?](../managed-identities-azure-resources/overview.md)

* **Privileged Identity Management** : Privileged Identity Management (PIM) est un service Azure Active Directory (Azure AD) qui vous permet de gérer, de contrôler et de superviser l’accès aux ressources importantes de votre organisation. Pour plus d’informations, consultez [Qu’est-ce qu’Azure AD Privileged Identity Management ?](../privileged-identity-management/pim-configure.md).

* **Révisions d’accès** : les révisions d’accès Azure Active Directory (Azure AD) permettent aux organisations de gérer efficacement les appartenances à des groupes, les accès aux applications d’entreprise et les attributions de rôles. L’accès des utilisateurs peut être passé en revue régulièrement pour vérifier que seules les personnes appropriées continuent de bénéficier d’un accès. Pour plus d’informations, consultez [Présentation des révisions d’accès Azure AD](../governance/access-reviews-overview.md).

* **Gestion des droits d’utilisation** : la gestion des droits d’utilisation Azure Active Directory (Azure AD) est une fonctionnalité de [gouvernance des identités](../governance/identity-governance-overview.md) qui permet aux organisations de gérer le cycle de vie des identités et des accès à grande échelle, en automatisant les workflows de demande d’accès, les attributions d’accès, les révisions et l’expiration. Pour plus d’informations, voir [Qu’est-ce que la gestion des droits d’utilisation Azure AD ?](../governance/entitlement-management-overview.md)

* **Journal d’activité** : le journal d’activité est un [journal de plateforme](../../azure-monitor/essentials/platform-logs-overview.md) Azure qui fournit des insights pour tous les événements concernant les abonnements. Les informations qu’il contient indiquent par exemple à quel moment une ressource a été modifiée ou une machine virtuelle a été démarrée. Pour plus d’informations, consultez [Journal d’activité Azure](../../azure-monitor/essentials/activity-log.md).

* **Service de réinitialisation de mot de passe en libre-service** : la réinitialisation de mot de passe en libre-service (SSPR) Azure Active Directory (Azure AD) permet aux utilisateurs de changer ou de réinitialiser leur mot de passe sans intervention d’un administrateur ou d’un agent du support technique. Pour plus d’informations, découvrez le [fonctionnement de ce processus : Réinitialisation de mot de passe en libre-service Azure AD](../authentication/concept-sspr-howitworks.md).

* **Services d’appareils** : la gestion des identités d’appareils est à la base de l’[accès conditionnel basé sur les appareils](../conditional-access/require-managed-devices.md). Avec les stratégies d’accès conditionnel basé sur les appareils, vous pouvez faire en sorte que l’accès aux ressources de votre environnement soit possible seulement avec des appareils managés. Pour plus d’informations, consultez [Qu’est-ce qu’une identité d’appareil ?](../devices/overview.md).

* **Gestion de groupes en libre-service** : vous pouvez autoriser les utilisateurs à créer et à gérer leurs propres groupes de sécurité ou Microsoft 365 dans Azure Active Directory (Azure AD). Le propriétaire du groupe peut approuver ou refuser des demandes d’appartenance, et peut déléguer le contrôle de l’appartenance au groupe. Les fonctionnalités de gestion de groupes en libre-service ne sont pas disponibles pour les groupes de sécurité activés pour la messagerie électronique ou les listes de distribution. Pour plus d’informations, consultez [Configurer la gestion de groupes en libre-service dans Azure Active Directory](../enterprise-users/groups-self-service-management.md).

* **Détection de risque** : contient des informations sur les risques supplémentaires qui sont engendrés lorsqu’un risque est détecté, ainsi que d’autres informations pertinentes, telles que l’emplacement de connexion et les détails relatifs à Microsoft Cloud App Security (MCAS).

## <a name="next-steps"></a>Étapes suivantes

Consultez les articles suivants sur les opérations de sécurité :

[Vue d’ensemble des opérations de sécurité Azure AD](security-operations-introduction.md)

[Opérations de sécurité pour les comptes d’utilisateur](security-operations-user-accounts.md)

[Opérations de sécurité pour les comptes privilégiés](security-operations-privileged-accounts.md)

[Opérations de sécurité pour Privileged Identity Management](security-operations-privileged-identity-management.md)

[Opérations de sécurité pour les applications](security-operations-applications.md)

[Opérations de sécurité pour les appareils](security-operations-devices.md)

 
[Opérations de sécurité pour l’infrastructure](security-operations-infrastructure.md)
