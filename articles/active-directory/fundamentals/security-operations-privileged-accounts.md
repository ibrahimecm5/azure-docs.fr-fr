---
title: Opérations de sécurité Azure Active Directory pour les comptes privilégiés
description: Apprenez à définir des lignes de base, puis à superviser et signaler les problèmes de sécurité potentiels avec les comptes privilégiés dans Azure Active Directory.
services: active-directory
author: BarbaraSelden
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 07/15/2021
ms.author: baselden
ms.collection: M365-identity-device-management
ms.openlocfilehash: a10d5f1e21f741382ad0d82c06c3f60b19e2c1c4
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132519322"
---
# <a name="security-operations-for-privileged-accounts"></a>Opérations de sécurité pour les comptes privilégiés

La sécurité des ressources professionnelles dépend de l’intégrité des comptes privilégiés qui administrent vos systèmes informatiques. Les pirates informatiques dérobent des informations d’identification et utilisent d’autres méthodes pour cibler les comptes privilégiés et accéder aux données sensibles.

En général, la sécurité d’une organisation est axée sur les points d’entrée et de sortie d’un réseau faisant office de périmètre de sécurité. Toutefois, en raison de l’utilisation d’applications SaaS et d’appareils personnels sur Internet, cette approche a perdu en efficacité. 

Azure Active Directory (Azure AD) utilise la gestion des identités et des accès (IAM) comme plan de contrôle. Dans la couche d’identité de votre organisation, les utilisateurs affectés à des rôles administratifs privilégiés ont le contrôle. Les comptes utilisés pour l’accès doivent être protégés, que l’environnement soit local, dans le cloud ou dans un environnement hybride.

Vous êtes entièrement responsable de toutes les couches de sécurité de votre environnement informatique local. Lorsque vous utilisez les services Azure, la prévention et la réponse aux problèmes sont des responsabilités qui sont partagées entre Microsoft en tant que fournisseur de services cloud, et vous en tant que client.

* Pour plus d’informations sur le modèle de responsabilité partagée, consultez [Responsabilité partagée dans le cloud](../../security/fundamentals/shared-responsibility.md).

* Pour plus d’informations sur la sécurisation de l’accès des utilisateurs privilégiés, consultez [Sécurisation de l’accès privilégié pour les déploiements hybrides et cloud dans Azure AD](../roles/security-planning.md).

* Pour obtenir une large gamme de vidéos, de guides pratiques et de contenu concernant les concepts clés des identités privilégiées, consultez la [documentation Privileged Identity Management](../privileged-identity-management/index.yml).

## <a name="where-to-look"></a>Emplacement des fichiers

Les fichiers journaux que vous pouvez utiliser pour l’investigation et la supervision sont les suivants : 

* [Journaux d’audit Azure AD](../reports-monitoring/concept-audit-logs.md)

* [Journaux d’audit Microsoft 365](/microsoft-365/compliance/auditing-solutions-overview) 

* [Insights Azure Key Vault](../../azure-monitor/insights/key-vault-insights-overview.md)

Sur le Portail Azure, vous pouvez afficher les journaux d’audit Azure AD et les télécharger sous forme de fichiers CSV (valeurs séparées par des virgules) ou JSON (JavaScript Object Notation). Le Portail Azure offre plusieurs méthodes d’intégration entre les journaux Azure AD et d’autres outils, ce qui permet une plus grande automatisation du monitoring et des alertes :

* [Microsoft Sentinel](../../sentinel/overview.md) : permet une analytique de sécurité intelligente au niveau de l’entreprise en fournissant des fonctionnalités d’informations de sécurité et gestion d’événements (SIEM, Security Information and Event Management). 

* [Azure Monitor](../../azure-monitor/overview.md) : permet un monitoring et des alertes automatisés de divers états. Peut créer ou utiliser des classeurs pour combiner des données provenant de différentes sources.

* [Azure Event Hubs](../../event-hubs/event-hubs-about.md) avec intégration SIEM[ : permet de transmettre les journaux Azure AD à d’autres systèmes SIEM](../reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md) comme Splunk, ArcSight, QRadar et Sumo Logic grâce à l’intégration Azure Event Hub.

* [Microsoft Defender for Cloud Apps](/cloud-app-security/what-is-cloud-app-security) : permet de découvrir et de gérer les applications, de gouverner toutes les applications et ressources et de vérifier la conformité des applications cloud. 

* Microsoft Graph : vous pouvez exporter les données et utiliser MS Graph pour effectuer d’autres analyses. Pour plus d’informations sur MS Graph, consultez [Kit de développement logiciel (SDK) Microsoft Graph PowerShell et Azure Active Directory Identity Protection](../identity-protection/howto-identity-protection-graph-api.md). 

* [Identity Protection](../identity-protection/overview-identity-protection.md) : génère trois rapports clés que vous pouvez utiliser dans le cadre de votre investigation :

   * Utilisateurs à risque : contient des informations sur les utilisateurs à risque, des détails sur les détections, l’historique de toutes les connexions risquées et l’historique des risques.

   * Connexions risquées : contient des informations concernant le contexte d’une connexion potentiellement suspecte. Pour plus d’informations sur l’investigation des informations de ce rapport, consultez [Procédure : Examiner les risques](../identity-protection/howto-identity-protection-investigate-risk.md). 

   * Détections de risques : contient des informations sur les autres risques déclenchés lorsqu’un risque est détecté et d’autres informations pertinentes, telles que l’emplacement de connexion et les détails de Microsoft Defender for Cloud Apps.

 

Bien que nous déconseillons cette pratique, les comptes privilégiés peuvent bénéficier de droits d’administration permanents. Si vous choisissez d’utiliser des privilèges permanents et que le compte est compromis, cela peut avoir un impact très négatif. Nous vous recommandons de classer par ordre de priorité les comptes privilégiés de monitoring et d’inclure les comptes dans votre configuration Privileged Identity Management (PIM). Pour plus d’informations sur PIM, consultez [Commencer à utiliser Privileged Identity Management](../privileged-identity-management/pim-getting-started.md). En outre, nous vous recommandons de valider que les comptes administrateur :

* Sont requis.

* Disposent des privilèges minimum pour exécuter les activités requises.

* Sont protégées avec MFA au minimum.

* Sont exécutés à partir de stations de travail à accès privilégié ou de stations de travail d’administration sécurisée (SAW). 

Le reste de cet article comprend des recommandations concernant la supervision et les alertes, qui sont organisées par type de menace. Lorsqu’il existe des solutions prédéfinies spécifiques, nous en indiquons le lien après le tableau. Sinon, vous pouvez créer des alertes à l’aide des outils précédents. Plus spécifiquement, cet article fournit des informations sur la création de lignes de base, l’audit des connexions, l’utilisation des comptes privilégiés, et les outils et ressources que vous pouvez utiliser pour préserver l’intégrité de vos comptes privilégiés. Le contenu est organisé par thème :

* Comptes d’accès d’urgence 

* Connexion à un compte privilégié

* Modifications des comptes privilégiés

* Groupes privilégiés

* Attribution et élévation de privilèges

## <a name="emergency-access-accounts"></a>Comptes d’accès d’urgence

Il est primordial que vous empêchiez le verrouillage accidentel de votre locataire Azure Active Directory (Azure AD). Vous pouvez pallier l’impact d’un verrouillage accidentel en créant des comptes d’accès d’urgence dans votre organisation. Les comptes d’accès d’urgence sont également appelés comptes de « secours » et peuvent être comparés à l’utilisation des panneaux « briser la glace en cas d’urgence » sur des équipements de sécurité physiques comme les alarmes incendie.

Les comptes d’accès d’urgence sont hautement privilégiés et ne sont pas attribués à des personnes spécifiques. Les comptes d’accès d’urgence sont limités aux scénarios d’urgence où il est impossible d’utiliser des comptes privilégiés normaux. Par exemple, lorsqu’une stratégie d’accès conditionnel est mal configurée et verrouille tous les comptes d’administration normaux. Limitez l’utilisation du compte d’accès d’urgence aux seuls moments où cela est absolument nécessaire.

Consultez également nos conseils sur la procédure à suivre en cas d’urgence pour [les administrateurs Azure AD](../roles/security-planning.md).

Envoyez une alerte haute priorité chaque fois qu’un compte d’accès d’urgence est utilisé.

### <a name="discovery"></a>Découverte

Étant donné que les comptes de secours sont utilisés uniquement en cas de situation d’urgence, votre analyse ne doit pas détecter d’activité de compte. Envoyez une alerte haute priorité chaque fois qu’un compte d’accès d’urgence est utilisé ou modifié.  L’un des événements suivants peut indiquer qu’un mauvais intervenant tente de compromettre vos environnements.

* Compte utilisé : surveillez et signalez toute activité utilisant ce type de compte, y compris :

* Se connecter

* Modification du mot de passe du compte 

* Modification des autorisations/rôles du compte 

* Ajout ou modification des informations d’identification ou de la méthode d’authentification 

Pour plus d’informations sur la gestion des comptes d’accès d’urgence, consultez [Gérer les comptes d’administration de l’accès d’urgence dans Azure AD](../roles/security-emergency-access.md). Pour plus d’informations sur la création d’une alerte pour un compte d’accès d’urgence, consultez [Créer une règle d’alerte](../roles/security-emergency-access.md). 

## <a name="privileged-account-sign-in"></a>Connexion à un compte privilégié

Surveillez l’activité de connexion de toutes les comptes privilégiés en utilisant les journaux de connexion Azure AD comme source de données. Outre les informations de réussite et d’échec de connexion, les journaux contiennent les informations suivantes :

* Interruptions
* Appareil
* Emplacement
* Risque
* Application
* Date et heure
* Le compte est-il désactivé ?
* Verrouillage.
* Fraude MFA
* Défaillance de l’autorité de certification

### <a name="things-to-monitor"></a>Éléments à surveiller

Vous pouvez surveiller les événements de connexion à un compte privilégié dans les journaux de connexion Azure AD. Signalez et menez des investigations sur les événements suivants pour les comptes privilégiés.

| Éléments à analyser | Niveau de risque |  Where |  Filtre/Sous-filtre | Notes |
| - | - | - | - | - |
| Échec de connexion, seuil de mot de passe incorrect | Élevé | Journal de connexions Azure AD | État = échec<br>-et-<br>Code d’erreur = 50126 | Définissez un seuil de ligne de base, puis surveillez et ajustez-le pour l’adapter aux comportements de votre organisation et limiter la génération de fausses alertes. |
| Échec en raison de l’exigence de l’autorité de certification |Élevé | Journal de connexions Azure AD | État = échec<br>-et-<br>Code d’erreur = 53003<br>-et-<br>Raison de l’échec = bloqué par l’autorité de certification | Cela peut indiquer qu’un attaquant tente d’accéder au compte. |
| Comptes privilégiés qui ne suivent pas la stratégie d’affectation de noms.| | Abonnement Azure | [Répertorier les attribution de rôle Azure à l’aide du portail Azure – Azure RBAC](../../role-based-access-control/role-assignments-list-portal.md)| Répertoriez les attributions de rôles pour les abonnements et envoyez des alertes lorsque le nom de connexion ne correspond pas au format de votre organisation. Par exemple, ADM_ en tant que préfixe. |
| Interruption |  Élevé/Moyen | Connexions Azure AD | État = Interrompu<br>-et-<br>Code d’erreur = 50074<br>-et-<br>Raison de l’échec = authentification forte requise<br>État = Interrompu<br>-et-<br>Code d’erreur = 500121<br>Raison de l’échec = échec de l’authentification lors d’une requête d’authentification forte. | Cela peut indiquer qu’un attaquant a le mot de passe du compte, mais ne peut pas réussir la stimulation MFA. | 
| Comptes privilégiés qui ne suivent pas la stratégie d’affectation de noms.| Élevé | Annuaire Azure AD | [Répertorier les attributions de rôle Azure AD](../roles/view-assignments.md)| Répertoriez les attributions de rôles Azure AD et signalez les rôles dont l’UPN ne correspond pas au format de votre organisation. Par exemple, ADM_ en tant que préfixe. |
| Détectez les comptes privilégiés non inscrits à MFA. | Élevé | API Microsoft Graph| Exécutez la requête IsMFARegistered = false pour les comptes administrateur. [Lister credentialUserRegistrationDetails - Microsoft Graph version bêta](/graph/api/reportroot-list-credentialuserregistrationdetails?view=graph-rest-beta&preserve-view=true&tabs=http) | Auditez et vérifiez si c’est intentionnel ou si c’est un oubli. |
| Verrouillage de compte | Élevé | Journal de connexions Azure AD | État = échec<br>-et-<br>Code d’erreur = 50053 | Définissez un seuil de ligne de base, puis surveillez et ajustez-le pour l’adapter aux comportements de votre organisation et limiter la génération de fausses alertes. |
| Compte désactivé/bloqué pour les connexions | Faible | Journal de connexions Azure AD | État = échec<br>-et-<br>Cible = UPN de l’utilisateur<br>-et-<br>Code d’erreur = 50057 | Cela peut indiquer qu’un utilisateur tente d’accéder à un compte une fois qu’il a quitté une organisation. Même si le compte est bloqué, il est important d’enregistrer et de signaler cette activité. |
| Alerte de fraude MFA/bloquer | Élevé | Journal des connexions Azure AD/Azure Log Analytics | Connexions>Détails de l’authentification Détails des résultats = MFA refusé, code fraude entré | L’utilisateur privilégié a indiqué qu’il n’a pas provoqué l’invite MFA et peut indiquer qu’un attaquant a le mot de passe du compte. |
| Alerte de fraude MFA/bloquer | Élevé | Journal d’audit Azure AD/Azure Log Anaylitics | Type d’activité = Fraude signalée - l’utilisateur ne peut pas utiliser MFA ou Fraude signalée - aucune action entreprise (selon les paramètres de niveau de locataire pour le rapport des fraudes) | L’utilisateur privilégié a indiqué qu’il n’a pas provoqué l’invite MFA et peut indiquer qu’un attaquant a le mot de passe du compte. |
| Connexions à des comptes privilégiés en dehors des contrôles attendus. |  | Journal de connexions Azure AD | État = échec<br>UserPricipalName = \<Admin account\><br>Emplacement = \<unapproved location\><br>Adresse IP = \<unapproved IP\><br>Informations sur l’appareil= \<unapproved Browser, Operating System\> | Surveillez et signalez les entrées que vous avez définies comme non approuvées. |
| En dehors des heures de connexion normales | Élevé | Journal de connexions Azure AD | État = réussite<br>-et-<br>Emplacement =<br>-et-<br>Heure = en dehors des heures de travail | Surveillez et signalez les connexions qui se produisent en dehors des périodes attendues. Il est important de trouver le modèle de travail normal pour chaque compte privilégié et de signaler les modifications non planifiées en dehors des périodes de travail normales. Les connexions en dehors des heures de travail normales peuvent indiquer une compromission ou des menaces potentielles provenant d’initiés. | 
| Risque Identity Protection | Élevé | Journaux Identity Protection | État du risque = risqué<br>-et-<br>Niveau de risque = faible/moyen/élevé<br>-et-<br>Activité = connexion/TOR inhabituels, etc. | Cela indique qu’il existe une anomalie détectée avec la connexion pour le compte et que vous devez recevoir une alerte à ce sujet. | 
| Modification de mot de passe | Élevé | Journaux d’audit Azure AD | Intervenant de l’activité = admin/libre-service<br>-et-<br>Cible = Utilisateur<br>-et-<br>État = réussite/échec | Signalez les modifications de mot de passe de compte d’administrateur, en particulier pour les administrateurs généraux, les administrateurs d’utilisateurs, les administrateurs d’abonnements et les comptes d’accès d’urgence. Écrivez une requête ciblée sur tous les comptes privilégiés. | 
| Modifier le protocole d’authentification hérité | Élevé | Journal de connexions Azure AD | Application cliente = autre client, IMAP, POP3, MAPI, SMTP, etc.<br>-et-<br>Nom d’utilisateur = UPN<br>-et-<br>Application = Exchange (exemple) | De nombreuses attaques utilisent l’authentification héritée et, par conséquent, s’il y a une modification dans le protocole d’authentification pour l’utilisateur, cela peut indiquer une attaque. |
| Nouvel appareil ou emplacement | Élevé | Journal de connexions Azure AD | Informations sur l’appareil = ID de l’appareil<br>-et-<br>Browser<br>-et-<br>Système d''exploitation<br>-et-<br>Conforme/géré<br>-et-<br>Cible = Utilisateur<br>-et-<br>Emplacement | La plupart des activités d’administration doivent provenir d’[appareils à accès privilégié](/security/compass/privileged-access-devices), à partir d’un nombre limité d’emplacements. Par conséquent, signalez les nouveaux appareils ou emplacements. |
| Le paramètre d’alerte d’audit est modifié. | Élevé | Journaux d’audit Azure AD | Service = PIM<br>-et-<br>Catégorie = Gestion des rôles<br>-et-<br>Activité = désactiver l’alerte PIM<br>-et-<br>État = réussite | Les modifications apportées à une alerte principale doivent être signalées si elles sont inattendues. |


## <a name="changes-by-privileged-accounts"></a>Modifications par des comptes privilégiés 

Surveillez tous les changements effectués et tentés par un compte privilégié. Cela vous permet d’établir quelle est l’activité normale pour chaque compte privilégié et de signaler l’activité qui dévie de celle attendue. Les journaux d’audit Azure AD sont utilisés pour enregistrer ce type d’événement. Pour plus d’informations sur les journaux d’audit Azure AD, consultez [Journaux d’audit dans Azure Active Directory](../reports-monitoring/concept-audit-logs.md). 

### <a name="azure-active-directory-domain-services"></a>Azure Active Directory Domain Services 

Les comptes privilégiés auxquels des autorisations ont été attribuées dans Azure Active Directory Domain Services peuvent effectuer des tâches pour Azure AD DS qui affectent la position de sécurité de vos machines virtuelles hébergées par Azure qui utilisent Azure AD Domain Services. Activez les audits de sécurité sur les machines virtuelles et surveillez les journaux. Pour plus d’informations sur l’activation des audits Azure AD DS et pour obtenir la liste des privilèges sensibles, consultez les ressources suivantes.

* [Activer les audits de sécurité pour Azure Active Directory Domain Services](../../active-directory-domain-services/security-audit-events.md)

* [Auditer l’utilisation de privilèges sensibles](/windows/security/threat-protection/auditing/audit-sensitive-privilege-use)

| Éléments à analyser                                                         | Niveau de risque | Where               | Filtre/Sous-filtre                                                                                                                                                                                                                                                                                                                                                                                                                                                                    | Notes                                                                                                                                                                                                                                                                                                                                                                                                                             |
|-------------------------------------------------------------------------|------------|---------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Modifications tentées et terminées                                  | Élevé       | Journaux d’audit Azure AD | Date et heure<br>-et-<br>Service<br>-et-<br>Catégorie et nom de l’activité (laquelle)<br>-et-<br>État = réussite ou échec<br>-et-<br>Cible<br>-et-<br>Initiateur/intervenant (qui)                                                                                                                                                                                                                                                                                                | Toutes les modifications non planifiées doivent être signalées par une alerte immédiatement. Ces journaux doivent être conservés pour faciliter l’investigation. Toutes les modifications apportées au niveau du locataire doivent être examinées immédiatement (lien vers la documentation Infra) pour réduire la position de sécurité de votre locataire. Par exemple : exclure des comptes de l’authentification MFA ou de l’accès conditionnel. Signalez les [ajouts ou modifications apportés à des applications](security-operations-applications.md). |
| **EXEMPLE**<br>Tentative de modification ou modification effectuée sur des applications ou des services de grande valeur | Élevé       | Journal d’audit           | Service<br>-et-<br>Catégorie et nom de l’activité                                                                                                                                                                                                                                                                                                                                                                                                                                | <li>Date et heure <li>Service <li>Catégorie et nom de l’activité <li>État = réussite ou échec <li>Cible <li>Initiateur/intervenant (qui)                                                                                                                                                                                                                                                                                        |
| Modifications privilégiées dans Azure AD DS                                             | Élevé       | AD DS               | Rechercher l’événement [4673](/windows/security/threat-protection/auditing/event-4673) | [Activer les audits de sécurité pour Azure Active Directory Domain Services](../../active-directory-domain-services/security-audit-events.md)<br>[Auditer l’utilisation de privilèges sensibles](/windows/security/threat-protection/auditing/audit-sensitive-privilege-use) Consultez l’article pour obtenir la liste de tous les événements privilégiés.                                                                                                                            |


## <a name="changes-to-privileged-accounts"></a>Modifications apportées à des comptes privilégiés

Examinez les modifications apportées aux privilèges et règles d’authentification des comptes privilégiés, en particulier si la modification offre une capacité ou un privilège plus élevé pour effectuer des tâches dans votre environnement Azure AD. 

| Éléments à analyser| Niveau de risque| Where| Filtre/Sous-filtre| Notes |
| - | - | - | - | - |
| Création d’un compte privilégié.| Moyenne| Journaux d’audit Azure AD| Service = répertoire principal<br>-et-<br>Catégorie = gestion des utilisateurs<br>-et-<br>Type d’activité = Ajouter un utilisateur<br>-corrélation avec –<br>Type de catégorie = gestion des rôles<br>-et-<br>Type d’activité = ajout d’un membre au rôle<br>-et-<br>Propriétés modifiées = Role.DisplayName| Surveillez la création de comptes privilégiés. Recherchez la corrélation entre la création et la suppression de comptes. |
| Modifications apportées aux méthodes d’authentification.| Élevé| Journaux d’audit Azure AD| Service = méthode d’authentification<br>-et-<br>Type d’activité = informations de sécurité inscrites par l’utilisateur<br>-et-<br>Catégorie = gestion des utilisateurs| Cela peut être le cas d’une personne malveillante qui ajoute une méthode d’authentification au compte afin d’obtenir un accès en continu. |
| Signalez les modifications apportées aux autorisations de compte privilégié.| Élevé| Journaux d’audit Azure AD| Catégorie = Gestion des rôles<br>-et-<br>Type d’activité – Ajout d’un membre éligible (permanent)<br>-et-<br>Type d’activité – Ajout d’un membre éligible (éligible)<br>-et-<br>État = Réussite/Échec<br>-et-<br>Propriétés modifiées = Role.DisplayName| Cela est particulièrement vrai pour les comptes auxquels les rôles attribués sont inconnus ou en dehors de leurs responsabilités normales. |
| Comptes privilégiés inutilisés.| Moyenne| Révisions d’accès Azure AD| | Passez en revue tous les mois les comptes d’utilisateurs privilégiés inactifs. |
| Comptes exemptés de l’accès conditionnel| Élevé| Journaux Azure Monitor<br>-ou-<br>Révisions d’accès| Insights et rapports sur l’accès conditionnel| Tout compte exempté de l’autorité de certification contourne probablement les contrôles de sécurité et est plus vulnérable à la compromission. Les comptes de secours sont exemptés. Consultez les informations sur la façon de surveiller les comptes de secours dans une section ultérieure de cet article.|


Pour plus d’informations sur la surveillance des exceptions pour les stratégies d’accès conditionnel, consultez [Insights et reporting sur l’accès conditionnel](../conditional-access/howto-conditional-access-insights-reporting.md).

Pour plus d’informations sur la découverte des comptes privilégiés inutilisés, consultez [Créer une révision d’accès des rôles Azure AD dans Privileged Identity Management](../privileged-identity-management/pim-create-azure-ad-roles-and-resource-roles-review.md).

 
## <a name="assignment-and-elevation"></a>Attribution et élévation

Le fait de disposer de comptes privilégiés configurés en continu avec des capacités élevées peut augmenter la surface d’attaque et les risques liés à votre limite de sécurité. Préférez à cela un accès juste-à-temps à l’aide d’une procédure d’élévation. Ce type de système vous permet d’attribuer des droits pour les rôles privilégiés, et les administrateurs élèvent leurs privilèges sur ces rôles uniquement lorsqu’ils effectuent des tâches qui en ont besoin. L’utilisation d’un processus d’élévation vous permet de surveiller les élévations et l’absence d’utilisation de comptes privilégiés. 

### <a name="establish-a-baseline"></a>Établir une ligne de base

Pour surveiller les exceptions, vous devez d’abord créer une ligne de base. Déterminez ce qui suit :

* Comptes d’administration 

   * Votre stratégie de compte privilégié

   * Utilisation de comptes locaux pour administrer des ressources locales.

   * Utilisation de comptes basés sur le cloud pour administrer des ressources basées sur le cloud.

   * Approche de la séparation et de la surveillance des autorisations administratives pour les ressources locales et basées sur le cloud.

* Protection des rôles privilégiés 

   * Stratégie de protection pour les rôles dotés de privilèges administratifs.

   * Stratégie organisationnelle pour l’utilisation de comptes privilégiés.

   * Stratégie et principes pour le maintien de privilèges permanents ou l’octroi d’un accès approuvé et limité dans le temps.

 

Les concepts et les informations suivants vous aideront à déterminer les stratégies.

* Principes d’administration juste-à-temps : utilisez les journaux Azure AD pour capturer des informations sur l’exécution de tâches d’administration communes à votre environnement. Déterminez le temps d’exécution habituel des tâches. 

* Principes d’administration juste-assez : [déterminez le rôle le moins privilégié](../roles/delegate-by-task.md), qui peut être un rôle personnalisé, nécessaire pour les tâches d’administration. 

* Établissement d’une stratégie d’élévation : une fois que vous avez un aperçu du type de privilège élevé et de la durée de chaque tâche, créez des stratégies qui reflètent l’utilisation de privilèges élevés pour votre environnement. Par exemple, la définition d’une stratégie pour limiter l’accès administrateur général à 1 heure.

 Une fois que vous avez établi votre ligne de base et défini la stratégie, vous pouvez configurer la surveillance pour détecter et signaler toute utilisation en dehors de la stratégie. 

### <a name="discovery"></a>Découverte

Nous vous recommandons de prêter une attention particulière aux modifications apportées à l’attribution et à l’élévation des privilèges. 

### <a name="things-to-monitor"></a>Éléments à surveiller

Vous pouvez surveiller les modifications de comptes privilégiés à l’aide de journaux d’audit Azure AD et de journaux Azure Monitor. Plus précisément, nous vous recommandons d’inclure les éléments suivants dans votre processus de surveillance. 

| Éléments à analyser| Niveau de risque| Where| Filtre/Sous-filtre| Notes |
| - | - | - | - | - |
| Ajouté au rôle privilégié éligible.| Élevé| Journaux d’audit Azure AD| Service = PIM<br>-et-<br>Catégorie = Gestion des rôles<br>-et-<br>Type d’activité - Ajout de membre au rôle terminé (éligible)<br>-et-<br>État = Réussite/Échec<br>-et-<br>Propriétés modifiées = Role.DisplayName| Tout compte éligible à un rôle reçoit maintenant un accès privilégié. Si l’attribution est inattendue ou est appliquée à un rôle qui n’est pas la responsabilité du titulaire du compte, étudiez le problème. |
| Rôles attribués hors PIM.| Élevé| Journaux d’audit Azure AD| Service = PIM<br>-et-<br>Catégorie = Gestion des rôles<br>-et-<br>Type d’activité : Ajout d’un membre au rôle (permanent)<br>-et-<br>État = Réussite/Échec<br>-et-<br>Propriétés modifiées = Role.DisplayName| Celles-ci doivent être surveillées et signalées étroitement. Les utilisateurs ne doivent pas se voir attribuer des rôles en dehors de PIM dans la mesure du possible. |
| Élévations| Moyenne| Journaux d’audit Azure AD| Service = PIM<br>-et-<br>Catégorie = Gestion des rôles<br>-et-<br>Type d’activité - Ajout de membre au rôle terminé (activation PIM)<br>-et-<br>État = Réussite/Échec<br>-et-<br>Propriétés modifiées = Role.DisplayName| Une fois élevé, un compte privilégié peut apporter des modifications susceptibles d’avoir un impact sur la sécurité de votre locataire. Toutes les élévations doivent être journalisées et, si elles se produisent en dehors du modèle standard pour cet utilisateur, elles doivent être signalées et examinées si elles ne sont pas planifiées. |
| Approbations et refus des demandes d’élévation| Faible| Journaux d’audit Azure AD| Service = Révision de l’accès<br>-et-<br>Catégorie = UserManagement<br>-et-<br>Type d’activité = Demande approuvée/refusée<br>-et-<br>Initié par (acteur) = UPN| Surveillez toutes les élévations, car celles-ci peuvent vous donner une indication claire de la chronologie d’une attaque. |
| Modifications apportées aux paramètres PIM| Élevé| Journaux d’audit Azure AD| Service = PIM<br>-et-<br>Catégorie = Gestion des rôles<br>-et-<br>Type d’activité = Mise à jour du paramètre de rôle dans PIM<br>-et-<br>Raison de l’état = MFA à l’activation désactivé (exemple)| L’une de ces actions est susceptible de réduire le niveau de sécurité de l’élévation PIM et permettre aux attaquants d’obtenir facilement un compte privilégié. |
| L’élévation ne se produit pas sur SAW/PAW| Élevé| Journaux de connexion Azure AD| ID de périphérique<br>-et-<br>Browser<br>-et-<br>Système d''exploitation<br>-et-<br>Conforme/géré<br>Corrélation avec :<br>Service = PIM<br>-et-<br>Catégorie = Gestion des rôles<br>-et-<br>Type d’activité - Ajout de membre au rôle terminé (activation PIM)<br>-et-<br>État = Réussite/Échec<br>-et-<br>Propriétés modifiées = Role.DisplayName| Si cela est configuré, toute tentative d’élévation sur un appareil non-PAW/SAW doit faire l'objet d’une enquête immédiate car cela pourrait indiquer qu’un attaquant essaie d’utiliser le compte. |
| Élévation pour gérer tous les abonnements Azure| Élevé| Azure Monitor| Onglet Journal d’activité <br>Onglet Activité de l’annuaire <br> Nom de l’opération = Affecte l’appelant au rôle Administrateur de l’accès utilisateur <br> -et- <br> Catégorie d’événement = administration <br> -et-<br>État = réussite, démarrage, échec<br>-et-<br>Événement lancé par| Ceci doit être examiné immédiatement si aucune modification n’est planifiée. Ce paramètre peut permettre à un attaquant d’accéder aux abonnements Azure de votre environnement. |


Pour plus d’informations sur la gestion de l’élévation de privilège, consultez [Élever l’accès pour gérer tous les abonnements et groupes d’administration Azure](../../role-based-access-control/elevate-access-global-admin.md). Pour plus d’informations sur la surveillance des élévations de privilèges à l’aide des informations disponibles dans les journaux Azure AD, consultez la section [Journal d’activité Azure](../../azure-monitor/essentials/activity-log.md) de la documentation Azure Monitor.

Pour plus d’informations sur la configuration des alertes pour le rôle Azure, consultez [Configurer des alertes de sécurité pour les rôles de ressources Azure dans Privileged Identity Management](../privileged-identity-management/pim-resource-roles-configure-alerts.md). 

 ## <a name="next-steps"></a>Étapes suivantes

Consultez les articles suivants sur les opérations de sécurité :

[Vue d’ensemble des opérations de sécurité Azure AD](security-operations-introduction.md)

[Opérations de sécurité pour les comptes d’utilisateur](security-operations-user-accounts.md)

[Opérations de sécurité pour les comptes privilégiés](security-operations-privileged-accounts.md)

[Opérations de sécurité pour Privileged Identity Management](security-operations-privileged-identity-management.md)

[Opérations de sécurité pour les applications](security-operations-applications.md)

[Opérations de sécurité pour les appareils](security-operations-devices.md)

 
[Opérations de sécurité pour l’infrastructure](security-operations-infrastructure.md)
