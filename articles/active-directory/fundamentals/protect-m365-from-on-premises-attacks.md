---
title: Protéger Microsoft 365 des attaques locales
description: Aide sur la façon de garantir qu’une attaque locale soit sans incidence sur Microsoft 365.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 12/22/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2c8b611b9edb3e4c53b2d40231a6df6c3ccd8a1e
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132346493"
---
# <a name="protecting-microsoft-365-from-on-premises-attacks"></a>Protéger Microsoft 365 des attaques locales

Beaucoup de clients connectent leurs réseaux d'entreprise privés à Microsoft 365 pour permettre à leurs utilisateurs, appareils et applications d'en bénéficier. Toutefois, ces réseaux privés peuvent être compromis de nombreuses façons bien documentées. Étant donné que Microsoft 365 agit comme une sorte de système nerveux pour de nombreuses organisations, il est essentiel de le protéger des atteintes à la sécurité de l’infrastructure locale.

Cet article vous explique comment configurer vos systèmes pour protéger votre environnement cloud Microsoft 365 de toute atteinte à la sécurité locale. Nous nous concentrons principalement sur : 

- Les paramètres de configuration du locataire Azure Active Directory (Azure AD).
- La façon dont les locataires Azure AD peuvent être connectés en toute sécurité aux systèmes locaux.
- Les compromis requis pour faire fonctionner vos systèmes de manière à protéger vos systèmes cloud contre les compromissions locales.

Nous vous recommandons vivement de suivre ces conseils pour sécuriser votre environnement cloud Microsoft 365.

> [!NOTE]
> Cet article a initialement été publié sous forme de billet de blog. Il a été déplacé vers son emplacement actuel à des fins de longévité et de maintenance.
>
> Pour créer une version hors connexion de cet article, utilisez la fonctionnalité d’impression Enregistrer au format PDF de votre navigateur. Revenez régulièrement sur cette page pour consulter les mises à jour.

## <a name="primary-threat-vectors-from-compromised-on-premises-environments"></a>Principaux vecteurs de menace liés aux environnements locaux compromis

Votre environnement cloud Microsoft 365 bénéficie d'une infrastructure de surveillance et de sécurité étendue. À l’aide de l’apprentissage automatique et de l’intelligence humaine, Microsoft 365 examine le trafic mondial. Il peut rapidement détecter les attaques et vous permettre de modifier votre configuration presque en temps réel. 

Dans les déploiements hybrides qui connectent l'infrastructure locale à Microsoft 365, de nombreuses organisations délèguent l'approbation à des composants locaux pour les décisions critiques liés à l'authentification et à la gestion de l'état des objets d'annuaire. Malheureusement, lors d’une atteinte à la sécurité de l’environnement local, ces relations de confiance deviennent des moyens pour un attaquant de compromettre votre environnement Microsoft 365.

Les deux principaux vecteurs de menace sont les *relations d'approbation de fédération* et la *synchronisation des comptes.* Ces deux vecteurs peuvent octroyer à un attaquant un accès administratif à votre cloud.

* Les **relations d’approbation de fédération**, telles que l’authentification SAML, vous permettent de vous authentifier auprès de Microsoft 365 par le biais de votre infrastructure d’identité locale. Si un certificat de signature de jetons SAML est compromis, la fédération permet à toute personne en possession de ce certificat d’emprunter l’identité de n’importe quel utilisateur de votre cloud. *Si possible, nous vous recommandons de désactiver les relations d'approbation de fédération pour l'authentification auprès de Microsoft 365.*

* La **synchronisation des comptes** peut être utilisée pour modifier des utilisateurs privilégiés (ainsi que leurs informations d’identification) ou des groupes disposant de privilèges administratifs dans Microsoft 365. *Veillez à ce que les objets synchronisés ne détiennent aucun privilège dépassant le champ d’action d’un utilisateur Microsoft 365,* que ce soit directement ou par l’inclusion dans des rôles ou des groupes approuvés. Assurez-vous que ces objets ne disposent d'aucune attribution directe ou imbriquée dans des rôles ou groupes cloud approuvés.

## <a name="protecting-microsoft-365-from-on-premises-compromise"></a>Protéger Microsoft 365 des atteintes à la sécurité locales

Pour lutter contre les vecteurs de menace décrits précédemment, nous vous recommandons de vous conformer aux principes illustrés dans le schéma suivant :

![Architecture de référence pour assurer la protection de Microsoft 365.](media/protect-m365/protect-m365-principles.png)

1. **Isolez complètement vos comptes administrateur Microsoft 365.** Ceux-ci doivent être :

    * contrôlés dans Azure AD ;

     * authentifiés à l’aide de l’authentification multifacteur ;

     *  sécurisés par l’accès conditionnel Azure AD ;

     *  uniquement accessibles via les stations de travail gérées par Azure.

    Ces comptes administrateur sont des comptes à usage restreint. *Aucun compte local ne doit disposer de privilèges administratifs dans Microsoft 365.* 

    Pour plus d’informations, consultez la [présentation des rôles Administrateur Microsoft 365](/microsoft-365/admin/add-users/about-admin-roles). Consultez également [Rôles liés à Microsoft 365 dans Azure AD](../roles/m365-workload-docs.md).

1. **Gérez les appareils à partir de Microsoft 365.** Utilisez les fonctionnalités de jonction Azure AD et de gestion informatique des périphériques mobiles (GMP) pour éliminer les dépendances vis-à-vis de votre infrastructure locale de gestion des périphériques. Ces dépendances sont susceptibles de nuire aux contrôles des appareils et de la sécurité.

1. **Assurez-vous qu’aucun compte local ne dispose de privilèges élevés vis-à-vis de Microsoft 365.** Certains comptes accèdent à des applications locales qui requièrent l’authentification NTLM, LDAP ou Kerberos. Ces comptes doivent se trouver dans l’infrastructure d’identité locale de l’organisation. Assurez-vous que ces comptes, y compris les comptes de service, ne sont pas inclus dans les groupes ou rôles cloud privilégiés. Assurez-vous également que les modifications apportées à ces comptes ne peuvent pas altérer l’intégrité de votre environnement cloud. Les logiciels locaux privilégiés ne doivent pas avoir d’impact sur les comptes ou rôles Microsoft 365 privilégiés.

1. **Utilisez l'authentification cloud Azure AD** pour éliminer les dépendances sur vos informations d'identification locales. Ayez toujours recours à une authentification forte, telle que Windows Hello, FIDO, Microsoft Authenticator ou l’authentification multifacteur Azure AD.

## <a name="specific-security-recommendations"></a>Recommandations spécifiques en matière de sécurité

Les sections suivantes fournissent une aide spécifique sur la manière de mettre en œuvre les principes décrits précédemment.

### <a name="isolate-privileged-identities"></a>Isoler les identités privilégiées

Dans Azure AD, les utilisateurs disposant de rôles privilégiés, tels que les administrateurs, sont généralement ceux sur lesquels reposent la création et la gestion du reste de l’environnement. Conformez-vous aux pratiques suivantes pour réduire les effets d’une atteinte à la sécurité.

* Utilisez des comptes « cloud uniquement » pour les rôles privilégiés Azure AD et Microsoft 365.

* Déployez des [appareils à accès privilégié](/security/compass/privileged-access-devices#device-roles-and-profiles) afin de disposer d'un accès privilégié pour gérer Microsoft 365 et Azure AD.

*  Déployez [Azure AD Privileged Identity Management](../privileged-identity-management/pim-configure.md) (PIM) pour l’accès juste-à-temps (JAT) à tous les comptes humains disposant de rôles privilégiés. Exigez une authentification forte pour activer les rôles.

* Fournissez des rôles Administrateur qui accordent le [moins de privilèges possible pour accomplir les tâches requises](../roles/delegate-by-task.md).

* Pour bénéficier d’une expérience d’attribution de rôle enrichie incluant la délégation et plusieurs rôles en même temps, envisagez d’utiliser des groupes de sécurité Azure AD ou des groupes Microsoft 365. Ces groupes sont collectivement appelés des *groupes cloud*. [Activez également le contrôle d’accès en fonction du rôle](../roles/groups-assign-role.md). Vous pouvez utiliser des [unités administratives](../roles/administrative-units.md) pour limiter l’étendue des rôles à une partie de l’organisation.

* Déployez des [comptes d’accès d’urgence](../roles/security-emergency-access.md). N’utilisez *pas* de coffres de mots de passe locaux pour stocker les informations d’identification.

Pour plus d’informations, consultez [Sécurisation de l’accès privilégié](/security/compass/overview). Consultez également [Pratiques d’accès sécurisé pour les administrateurs dans Azure AD](../roles/security-planning.md).

### <a name="use-cloud-authentication"></a>Utiliser l'authentification cloud 

Les informations d'identification constituent le premier vecteur d'attaque. Conformez-vous aux pratiques suivantes pour renforcer la sécurité des informations d’identification :

* [Déployez l’authentification sans mot de passe](../authentication/howto-authentication-passwordless-deployment.md). réduisez autant que possible l'utilisation de mots de passe en déployant des informations d'identification sans mot de passe. Ces informations d'identification sont gérées et validées en mode natif dans le cloud. Choisissez l’une des méthodes d’authentification suivantes :

   * [Windows Hello Entreprise](/windows/security/identity-protection/hello-for-business/passwordless-strategy)

   * [L’application Microsoft Authenticator](../authentication/howto-authentication-passwordless-phone.md)

   * [Clés de sécurité FIDO2](../authentication/howto-authentication-passwordless-security-key-windows.md)

* [Déployez l’authentification multifacteur](../authentication/howto-mfa-getstarted.md). Approvisionnez [plusieurs informations d’identification fortes en utilisant l’authentification multifacteur Azure AD](../fundamentals/resilience-in-credentials.md). Ainsi, l’accès aux ressources cloud nécessitera des informations d’identification gérées dans Azure AD en plus d’un mot de passe local qui peut être manipulé. Pour plus d’informations, consultez [Créer une stratégie de gestion du contrôle d’accès résiliente à l’aide d’Azure AD](./resilience-overview.md).

### <a name="limitations-and-tradeoffs"></a>Limitations et compromis

* La gestion des mots de passe des comptes hybrides nécessite des composants hybrides tels que des agents de protection par mot de passe et des agents de réécriture du mot de passe. Si votre infrastructure locale est compromise, des attaquants peuvent contrôler les ordinateurs sur lesquels ces agents résident. Cette vulnérabilité ne compromettra pas votre infrastructure cloud. Toutefois, vos comptes cloud ne protègent pas ces composants contre les compromissions locales.

*  Les comptes locaux synchronisés à partir d’Active Directory sont marqués pour ne jamais expirer dans Azure AD. Ce paramètre est généralement atténué par les paramètres de mot de passe Active Directory locaux. Toutefois, si votre instance locale d’Active Directory est compromise et que la synchronisation est désactivée, vous devez définir l’option [EnforceCloudPasswordPolicyForPasswordSyncedUsers](../hybrid/how-to-connect-password-hash-synchronization.md) pour forcer les modifications de mot de passe.

## <a name="provision-user-access-from-the-cloud"></a>Approvisionner l’accès utilisateur à partir du cloud

L’*approvisionnement* fait référence à la création de comptes et de groupes d’utilisateurs dans des applications ou des fournisseurs d’identité.

![Diagramme de l’architecture d’approvisionnement.](media/protect-m365/protect-m365-provision.png)

Nous vous recommandons d’utiliser les méthodes d’approvisionnement suivantes :

* **Approvisionnement entre des applications cloud de RH et Azure AD** : cet approvisionnement permet d’isoler une atteinte à la sécurité locale sans interrompre le cycle JML (Joiner-Mover-Leaver) entre vos applications cloud de RH et Azure AD.

* **Applications cloud** : dans la mesure du possible, déployez [l’approvisionnement d’application d’Azure AD](../app-provisioning/user-provisioning.md) plutôt que des solutions d’approvisionnement locales. Cette méthode protège certaines de vos applications SaaS des profils de pirates malveillants lors de violations locales. 

* **Identités externes** : Utilisez [Azure AD B2B Collaboration](../external-identities/what-is-b2b.md) Cette méthode réduit la dépendance vis-à-vis des comptes locaux pour la collaboration externe avec les partenaires, les clients et les fournisseurs. Évaluez avec soin toute fédération directe avec d'autres fournisseurs d'identité. Nous vous recommandons de limiter les comptes invités B2B de l’une des manières suivantes :

   * Limitez l'accès invité à des groupes de navigation et à d'autres propriétés de l'annuaire. Utilisez les paramètres de collaboration externe pour limiter la capacité des invités à lire des groupes dont ils ne sont pas membres. 

    * Bloquez l'accès au portail Azure. Vous pouvez raréfier les exceptions nécessaires.  Créez une stratégie d’accès conditionnel qui comprend tous les invités et utilisateurs externes. [Implémentez ensuite une stratégie pour bloquer l’accès](../../role-based-access-control/conditional-access-azure-management.md). 

* **Forêts déconnectées** : utilisez l’[approvisionnement cloud Azure AD](../cloud-sync/what-is-cloud-sync.md). Cette méthode vous permet de vous connecter à des forêts déconnectées, ce qui vous évite d’avoir à établir une connectivité ou des approbations entre forêts, lesquelles peuvent étendre l’effet d’une violation locale.
 
### <a name="limitations-and-tradeoffs"></a>Limitations et compromis

Lorsqu’il est utilisé pour approvisionner des comptes hybrides, le système « cloud de RH à Azure AD » s’appuie sur la synchronisation locale pour acheminer les données entre Active Directory Domain Services et Azure AD. Si la synchronisation est interrompue, les enregistrements des nouveaux employés ne sont pas disponibles dans Azure AD.

## <a name="use-cloud-groups-for-collaboration-and-access"></a>Utiliser des groupes cloud pour la collaboration et l'accès

Les groupes cloud vous permettent de dissocier votre collaboration et votre accès de votre infrastructure locale.

* **Collaboration** : utilisez des groupes Microsoft 365 et Microsoft Teams pour bénéficier d'une collaboration moderne. Désactivez les listes de distribution locales et procédez à une [mise à niveau des listes de distribution vers des groupes Microsoft 365 dans Outlook](/office365/admin/manage/upgrade-distribution-lists).

* **Accès** : utilisez des groupes de sécurité Azure AD ou des groupes Microsoft 365 pour autoriser l'accès aux applications dans Azure AD.

* **Licence Office 365** : utilisez une licence basée sur les groupes pour approvisionner Office 365 avec des groupes cloud uniquement. Cette méthode dissocie le contrôle de l’appartenance à un groupe de l’infrastructure locale.

Les propriétaires des groupes utilisés pour l’accès doivent être considérés comme des identités privilégiées afin d’éviter la prise de contrôle de l’appartenance à un groupe lors d’une atteinte à la sécurité locale. Une prise de contrôle comprendrait la manipulation directe de l’appartenance à un groupe au niveau local ou la manipulation d’attributs locaux qui peuvent modifier l’appartenance de groupe dynamique dans Microsoft 365.

## <a name="manage-devices-from-the-cloud"></a>Gérer des périphériques à partir du cloud

Utilisez les fonctionnalités d'Azure AD pour gérer les appareils en toute sécurité.

- **Utilisez des stations de travail Windows 10** : [déployez des appareils joints à Azure AD](../devices/azureadjoin-plan.md) avec des stratégies GPM. Activez [Windows AutoPilot](/mem/autopilot/windows-autopilot) pour une expérience d'approvisionnement entièrement automatisée.

    -   Déconseillez les ordinateurs sous Windows 8.1 et versions antérieures.

    -   Ne déployez pas d’ordinateurs avec SE serveur en tant que stations de travail.

    -   Utilisez [Microsoft Intune](https://www.microsoft.com/microsoft-365/enterprise-mobility-security/microsoft-intune) comme source d’autorité pour toutes les charges de travail de gestion des périphériques.

-   [**Déployer des appareils d’accès privilégié**](/security/compass/privileged-access-devices#device-roles-and-profiles) : utilisez l’accès privilégié pour gérer les Microsoft 365 et les Azure AD dans le cadre d’une approche complète de la [sécurisation de l’accès privilégié](/security/compass/overview).

## <a name="workloads-applications-and-resources"></a>Charges de travail, applications et ressources 

-   **Systèmes d’authentification unique locale** 

    Déconseillez toute infrastructure locale de fédération et de gestion de l’accès au web. Configurez les applications de manière à ce qu’elles utilisent Azure AD.  

-   **Applications SaaS et applications métier prenant en charge les protocoles d’authentification modernes** 

    [Utilisez Azure AD pour l’authentification unique](../manage-apps/what-is-single-sign-on.md). Plus vous configurez d’applications afin d’utiliser Azure AD pour l’authentification, moins le risque est grand lors d’une atteinte à la sécurité locale.


* **Applications héritées** 

   * Vous pouvez activer l’authentification, l’autorisation et l’accès à distance sur les applications héritées qui ne prennent pas en charge l’authentification moderne. Utilisez [Proxy d’application Azure AD](../app-proxy/application-proxy.md). Vous pouvez également les activer par le biais d’une solution de contrôle de livraison de réseau ou d’application en utilisant des [intégrations de partenaires d’accès hybride sécurisé](../manage-apps/secure-hybrid-access.md).   

   * Choisissez un fournisseur VPN qui prend en charge l’authentification moderne. Intégrez son authentification à Azure AD. Lors d’une atteinte à la sécurité locale, vous pouvez utiliser Azure AD pour désactiver ou bloquer l’accès en désactivant le VPN.

*  **Serveurs d'applications et de charges de travail**

   * Les applications ou les ressources qui nécessitent des serveurs peuvent être migrées vers des modèles IaaS Azure. Utilisez [Azure AD Domain Services](../../active-directory-domain-services/overview.md) (Azure AD DS) pour dissocier la confiance et la dépendance sur les instances locales d’Active Directory. Pour réaliser cette dissociation, assurez-vous que les réseaux virtuels utilisés pour Azure AD DS n’ont pas de connexion les reliant aux réseaux d’entreprise.

   * Suivez l’aide relative à la [hiérarchisation des informations d’identification](/security/compass/privileged-access-access-model#ADATM_BM). Les serveurs d’applications sont généralement considérés comme des ressources de niveau 1.

## <a name="conditional-access-policies"></a>Stratégies d’accès conditionnel

Utilisez l’accès conditionnel Azure AD pour interpréter les signaux et les utiliser pour prendre des décisions d’authentification. Pour plus d’informations, consultez le [plan de déploiement de l’accès conditionnel](../conditional-access/plan-conditional-access.md).

* Utilisez l’accès conditionnel pour [bloquer les protocoles d’authentification hérités](../conditional-access/howto-conditional-access-policy-block-legacy.md) lorsque cela est possible. En outre, désactivez les protocoles d’authentification hérités au niveau de l’application en utilisant une configuration spécifique à l’application.

   Pour plus d’informations, consultez [Protocoles d’authentification hérités](../fundamentals/auth-sync-overview.md). Vous pouvez également consulter les informations spécifiques à [Exchange Online](/exchange/clients-and-mobile-in-exchange-online/disable-basic-authentication-in-exchange-online#how-basic-authentication-works-in-exchange-online) et [SharePoint Online](/powershell/module/sharepoint-online/set-spotenant).

* Implémentez les [configurations recommandées pour l’identité et l’accès aux appareils](/microsoft-365/security/office-365-security/identity-access-policies).

* Si vous utilisez une version d’Azure AD qui n’inclut pas l’accès conditionnel, veillez à utiliser les [paramètres de sécurité par défaut d’Azure AD](../fundamentals/concept-fundamentals-security-defaults.md).

   Pour plus d’informations sur les licences des fonctionnalités Azure AD, consultez le [guide de tarification d’Azure AD](https://www.microsoft.com/security/business/identity-access-management/azure-ad-pricing).

## <a name="monitor"></a>Superviser 

Après avoir configuré votre environnement de façon à protéger Microsoft 365 de toute atteinte à la sécurité locale, vous devez [surveiller l’environnement de manière proactive](../reports-monitoring/overview-monitoring.md).
### <a name="scenarios-to-monitor"></a>Scénarios à superviser

Surveillez les scénarios clés suivants, en plus des scénarios propres à votre organisation. Par exemple, vous devez surveiller de manière proactive l'accès à vos applications et ressources stratégiques.

* **Activité suspecte** 

    Surveillez tous les [événements à risque Azure AD](../identity-protection/overview-identity-protection.md#risk-detection-and-remediation) pour détecter toute activité suspecte. [Azure AD Identity Protection](../identity-protection/overview-identity-protection.md) est intégré en mode natif à Microsoft Defender pour le cloud.

    Définissez les [emplacements nommés](../conditional-access/location-condition.md) du réseau pour éviter les détections bruyantes sur les signaux basés sur l'emplacement. 
*  **Alertes d’analyse comportementale des utilisateurs et des entités** 

    Utilisez l’analyse comportementale des utilisateurs et des entités pour obtenir des insights sur la détection d’anomalie.

    * Microsoft Defender for Cloud Apps fournit [une analyse comportementale des utilisateurs et des entités dans le cloud](/cloud-app-security/tutorial-ueba).

    * Vous pouvez [intégrer l’analyse comportementale locale des utilisateurs et des entités à partir d’Azure Advanced Threat Protection (ATP)](/defender-for-identity/install-step2). Defender for Cloud Apps lit les signaux d'Azure AD Identity Protection. 

* **Activité des comptes d’accès d’urgence** 

    Surveillez tout accès qui utilise des [comptes d’accès d’urgence](../roles/security-emergency-access.md). Créez des alertes pour les investigations. Cette surveillance doit englober ce qui suit : 

   * Connexions. 

   * Gestion des informations d'identification. 

   * Toutes les mises à jour relatives aux appartenances à des groupes. 

   * Attributions d’applications.

* **Activité des rôles privilégiés**

    Configurez et examinez les [alertes de sécurité générées par Azure AD Privileged Identity Management (PIM)](../privileged-identity-management/pim-how-to-configure-security-alerts.md?tabs=new#security-alerts). Surveillez l'attribution directe des rôles privilégiés en dehors de PIM en générant des alertes chaque fois qu'un utilisateur est directement attribué.

* **Configurations Azure AD à l’échelle des locataires**

    toute modification apportée aux configurations à l'échelle des locataires doit générer des alertes dans le système. Ces modifications incluent notamment les éléments suivants :

  *  Domaines personnalisés mis à jour.  

  * Modifications Azure AD B2B relatives aux listes d’autorisation et aux listes de refus.

  * Modifications Azure AD B2B relatives aux fournisseurs d’identité autorisés (fournisseurs d’identité SAML par fédération directe ou connexion aux réseaux sociaux).  

  * Modifications de la stratégie d’accès conditionnel ou de risque. 

* **Objets application et principal du service**
    
   * nouvelles applications ou nouveaux principaux de service susceptibles de nécessiter des stratégies d'accès conditionnel. 

   * Informations d’identification ajoutées aux principaux de service.
   * Activité de consentement d'application. 

* **Rôles personnalisés**
   * Mises à jour des définitions des rôles personnalisés. 

   * Rôles personnalisés nouvellement créés. 

### <a name="log-management"></a>Gestion du journal

Définissez une stratégie, une conception et une implémentation du stockage et de la rétention des journaux afin de faciliter la mise en place d’un ensemble d’outils cohérents. Par exemple, vous pouvez prendre en compte les systèmes SIEM (Informations de sécurité et gestion d’événements) comme Microsoft Sentinel, les requêtes courantes et les guides opérationnels d’investigation et de forensique.

* **Journaux Azure AD** : Ingérez les signaux et les journaux générés en respectant systématiquement les meilleures pratiques pour les paramètres tels que les diagnostics, la rétention des journaux et l’ingestion SIEM. 

    La stratégie de journalisation doit inclure les journaux Azure AD suivants :
   * Activité de connexion 

   * Journaux d’audit 

   * Événements à risque 

    Azure AD fournit une [intégration Azure Monitor](../reports-monitoring/concept-activity-logs-azure-monitor.md) pour le journal des activités de connexion et les journaux d'audit. Les événements à risque peuvent être ingérés via l’[API Microsoft Graph](/graph/api/resources/identityprotection-root). Vous pouvez [transmettre les journaux Azure AD en continu aux journaux d’activité Azure Monitor](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md).

* **Journaux de sécurité du système d’exploitation de l’infrastructure hybride** : Tous les journaux du système d’exploitation de l’infrastructure d’identité hybride doivent être archivés et soigneusement surveillés en tant que système de niveau 0, en raison des implications en termes de surface d’exposition. Incluez les éléments suivants : 

   *  Azure AD Connect. [Azure AD Connect Health](../hybrid/whatis-azure-ad-connect.md) doit être déployé pour surveiller la synchronisation des identités.

   *  Agents de Proxy d’application 


   * Agents de réécriture du mot de passe 

   * Ordinateurs avec passerelle de protection par mot de passe  

   * Serveurs de stratégie réseau (NPS) ayant l’extension RADIUS de l’authentification multifacteur Azure AD 

## <a name="next-steps"></a>Étapes suivantes
* [Créer une résilience dans la gestion des identités et des accès à l’aide d’Azure AD](resilience-overview.md)

* [Accès externe sécurisé aux ressources](secure-external-access-resources.md) 
* [Intégrer toutes vos applications à Azure AD](five-steps-to-full-application-integration-with-azure-ad.md)
