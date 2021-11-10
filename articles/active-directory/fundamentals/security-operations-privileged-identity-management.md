---
title: Opérations de sécurité Azure Active Directory pour Privileged Identity Management
description: Conseils pour établir des bases de référence et utiliser Azure Active Directory Privileged Identity Management (PIM) à des fins de supervision et de création d’alertes concernant des problèmes potentiels liés aux comptes régis par PIM.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 07/15/2021
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: bdca219a352527114123cdf0179a5eb7fc486852
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131435888"
---
# <a name="azure-active-directory-security-operations-for-privileged-identity-management-pim"></a>Opérations de sécurité Azure Active Directory pour Privileged Identity Management (PIM)

La sécurité des ressources professionnelles dépend de l’intégrité des comptes privilégiés qui administrent vos systèmes informatiques. Des pirates informatiques s’efforcent de dérober des informations d’identification permettant de cibler des comptes administrateur ou d’autres comptes disposant d’un accès privilégié dans le but d’accéder à des données sensibles.

Pour les services cloud, la prévention et la réponse sont de la responsabilité conjointe du fournisseur de services cloud et du client. 

En général, la sécurité d’une organisation est axée sur les points d’entrée et de sortie d’un réseau faisant office de périmètre de sécurité. Toutefois, en raison de l’utilisation d’applications SaaS et d’appareils personnels, cette approche a perdu en efficacité. Dans Azure Active Directory (Azure AD), nous remplaçons le périmètre de sécurité réseau par l’authentification dans la couche d’identité de l’organisation. Lorsque les utilisateurs se voient attribuer des rôles d’administration privilégiés, leur accès doit être protégé dans des environnements locaux, cloud et hybrides.

Vous êtes entièrement responsable de toutes les couches de sécurité de votre environnement informatique local. Lorsque vous utilisez les services cloud Azure, la prévention et la réponse aux problèmes sont des responsabilités qui sont partagées entre Microsoft en tant que fournisseur de services cloud, et vous en tant que client. 

* Pour plus d’informations sur le modèle de responsabilité partagée, consultez [Responsabilité partagée dans le cloud](../../security/fundamentals/shared-responsibility.md).

* Pour plus d’informations sur la sécurisation de l’accès des utilisateurs privilégiés, consultez [Sécurisation de l’accès privilégié pour les déploiements hybrides et cloud dans Azure AD](../roles/security-planning.md).

* Pour bénéficier d’un large choix de vidéos, de guides pratiques et de contenu concernant les concepts clés des identités privilégiées, consultez la [documentation Privileged Identity Management](../privileged-identity-management/index.yml). 

Privileged Identity Management (PIM) est un service Azure AD qui vous permet de gérer, de contrôler et de superviser l’accès aux ressources importantes de votre organisation. Ces ressources incluent des ressources dans Azure AD et Azure ainsi que d’autres services Microsoft Online Services tels que Microsoft 365 ou Microsoft Intune. Vous pouvez utiliser PIM pour atténuer les risques en :

* Réduisant le nombre de personnes qui ont accès à des informations ou à des ressources sécurisées.

* Détectant les autorisations d’accès excessives, inutiles ou inutilisées sur les ressources sensibles.

* Réduisant les possibilités qu’un acteur malveillant accède à des informations ou à des ressources sécurisées.

* Réduisant les possibilités qu’un utilisateur non autorisé altère accidentellement des ressources sensibles.

Cet article fournit des conseils sur la création de bases de référence, l’audit des connexions, l’utilisation des comptes privilégiés, ainsi que sur la source des journaux d’audit que vous pouvez utiliser pour préserver l’intégrité de vos comptes privilégiés. 

## <a name="where-to-look"></a>Où regarder ?

Les fichiers journaux que vous pouvez utiliser pour l’investigation et la supervision sont les suivants : 

* [Journaux d’audit Azure AD](../reports-monitoring/concept-audit-logs.md)

* [Journaux de connexion](../reports-monitoring/concept-all-sign-ins.md)

* [Journaux d’audit Microsoft 365](/microsoft-365/compliance/auditing-solutions-overview) 

* [Journaux Azure Key Vault](../../key-vault/general/logging.md?tabs=Vault)

Dans le portail Azure, vous pouvez afficher les journaux d’audit Azure AD et les télécharger sous forme de fichiers CSV ou JSON. Le portail Azure offre plusieurs moyens d’intégrer des journaux Azure AD aux autres outils, ce qui permet une plus grande automatisation de la supervision et des alertes :

* [**Azure Sentinel**](../../sentinel/overview.md) – Permet une analytique de sécurité intelligente au niveau de l’entreprise en fournissant des fonctionnalités d’informations de sécurité et de gestion d’événements management (SIEM). 

* [**Azure Monitor**](../../azure-monitor/overview.md) – Permet de créer des alertes et supervisions automatisées de diverses conditions. Peut créer ou utiliser des workbooks pour combiner des données provenant de différentes sources.

* [**Azure Event Hubs**](../../event-hubs/event-hubs-about.md) **avec intégration SIEM**- [Les journaux Azure AD peuvent être intégrés à d’autres SIEM](../reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md) comme Splunk, ArcSight, QRadar et Sumo Logic via l’intégration Azure Event Hub Hub.

* [**Microsoft Cloud App Security (MCAS)**](/cloud-app-security/what-is-cloud-app-security) : permet de découvrir et de gérer les applications, de gouverner toutes les applications et ressources et de vérifier la conformité des applications cloud. 

Le reste de cet article fournit des recommandations concernant la définition d’une base de référence pour la supervision et les alertes, qui sont organisées par niveau. Les liens vers les solutions prédéfinies sont listés après le tableau. Vous pouvez également créer des alertes à l’aide des outils précédents. Le contenu est organisé par thème PIM :

* Lignes de base

* Attribution de rôles Azure AD 

* Paramètres des alertes concernant les rôles Azure AD

* Attribution de rôles pour les ressources Azure

* Gestion de l’accès pour les ressources Azure

* Élévation des privilèges pour gérer les abonnements Azure

## <a name="baselines"></a>Lignes de base

Voici les paramètres recommandés pour la base de référence :

| Éléments à analyser| Niveau de risque| Recommandation| Rôles| Notes |
| - |- |- |- |- |
| Attribution de rôles Azure AD| Élevé| <li>Exiger une justification pour l’activation.<li>Exiger une approbation pour l’activation.<li>Définir un processus d’approbation à deux niveaux.<li>Lors de l’activation, exiger l’authentification multifacteur Azure Active Directory (MFA).<li>Configurer la durée d’élévation maximale sur 8 heures.| <li>Administration des rôles privilégiés<li>Administrateur général| Un administrateur de rôle privilégié peut personnaliser PIM dans son organisation Azure AD, notamment modifier l’expérience des utilisateurs qui activent une attribution de rôle éligible. |
| Configuration des rôles de ressources Azure| Élevé| <li>Exiger une justification pour l’activation.<li>Exiger une approbation pour l’activation.<li>Définir un processus d’approbation à deux niveaux.<li>Lors de l’activation, exiger Azure MFA.<li>Configurer la durée d’élévation maximale sur 8 heures.| <li>Propriétaire<li>Administrateur de ressources<li>Accès utilisateur <li>Administrateur<li>Administrateur général<li>Security Administrator| Investiguez immédiatement en cas de modification non planifiée. Ce paramètre peut permettre à un attaquant d’accéder aux abonnements Azure de votre environnement. |


## <a name="azure-ad-roles-assignment"></a>Attribution de rôles Azure AD

Un administrateur de rôle privilégié peut personnaliser PIM dans son organisation Azure AD. Cela comprend la modification de l’expérience d’un utilisateur qui active une attribution de rôle éligible de la façon suivante :

* Empêcher un acteur malveillant de supprimer les exigences d’Azure MFA pour activer l’accès privilégié.

* Empêcher les utilisateurs malveillants de contourner la justification et l’approbation concernant l’activation de l’accès privilégié.

| Éléments à analyser| Niveau de risque| Where| Filtre/Sous-filtre| Notes |
| - |- |- |- |- |
| Alerte lors de l’ajout de modifications aux autorisations de compte privilégié| Élevé| Journaux d’audit Azure AD| Catégorie = Gestion des rôles<br>-et-<br>Type d’activité – Ajout d’un membre éligible (permanent) <br>-et-<br>Type d’activité – Ajout d’un membre éligible (éligible) <br>-et-<br>État = Réussite/Échec<br>-et-<br>Propriétés modifiées = Role.DisplayName| Supervisez et signalez systématiquement toute modification apportée aux rôles Administrateur de rôle privilégié et Administrateur général. <li>Cela peut indiquer qu’un attaquant tente d’obtenir un privilège pour modifier les paramètres d’attribution de rôle.<li> Si vous n’avez pas défini de seuil, déclenchez une alerte si 4 modifications ont lieu sur une période de 60 minutes dans les comptes utilisateurs, et si 2 modifications ont lieu en 60 minutes dans les comptes privilégiés. |
| Alerte lors de suppressions en bloc des autorisations de compte privilégié| Élevé| Journaux d’audit Azure AD| Catégorie = Gestion des rôles<br>-et-<br>Type d’activité – Suppression d’un membre éligible (permanent) <br>-et-<br>Type d’activité – Suppression d’un membre éligible (éligible) <br>-et-<br>État = Réussite/Échec<br>-et-<br>Propriétés modifiées = Role.DisplayName| Investiguez immédiatement en cas de modification non planifiée. Ce paramètre peut permettre à un attaquant d’accéder aux abonnements Azure de votre environnement. |
| Modifications apportées aux paramètres PIM| Élevé| Journaux d’audit Azure AD| Service = PIM<br>-et-<br>Catégorie = Gestion des rôles<br>-et-<br>Type d’activité = Mise à jour du paramètre de rôle dans PIM<br>-et-<br>Raison de l’état = MFA à l’activation désactivée (exemple)| Supervisez et signalez systématiquement toute modification apportée aux rôles Administrateur de rôle privilégié et Administrateur général. <li>Cela peut indiquer qu’un attaquant a déjà obtenu l’accès et peut modifier les paramètres d’attribution de rôle.<li>L’une de ces actions est susceptible de réduire le niveau de sécurité de l’élévation PIM et permettre aux attaquants d’obtenir facilement un compte privilégié. |
| Approbations et refus des demandes d’élévation| Élevé| Journaux d’audit Azure AD| Service = Révision de l’accès<br>-et-<br>Catégorie = UserManagement<br>-et-<br>Type d’activité = Demande approuvée/refusée<br>-et-<br>Initié par (acteur) = UPN| Toutes les élévations doivent être supervisées. Journalisez toutes les élévations, car celles-ci peuvent vous donner une indication claire de la chronologie d’une attaque. |
| Désactivation d’un paramètre d’alerte.| Élevé| Journaux d’audit Azure AD| Service = PIM<br>-et-<br>Catégorie = Gestion des rôles<br>-et-<br>Type d’activité = Désactivation de l’alerte PIM<br>-et-<br>État = Réussite/Échec| Toujours déclencher une alerte. <li>Permet de détecter lorsqu’un acteur malveillant supprime les alertes concernant les exigences Azure MFA dans le but d’activer l’accès privilégié.<li>Permet de détecter les activités suspectes ou non sûres. |


Pour plus d’informations sur la détection des modifications apportées aux paramètres de rôle dans le journal d’audit Azure AD, consultez [Afficher l’historique d’audit des rôles Azure AD dans Privileged Identity Management](../privileged-identity-management/pim-how-to-use-audit-log.md). 

## <a name="azure-resource-role-assignment"></a>Attribution de rôles pour les ressources Azure

La supervision des attributions de rôles de ressources Azure vous permet de voir l’activité et les activations des rôles de ressources. Celles-ci peuvent être utilisées pour créer une surface d’attaque dans une ressource. Lorsque vous supervisez ce type d’activité, vous pouvez détecter les éléments suivants :

* Les requêtes d’attributions de rôles envoyées à des ressources spécifiques

* Les attributions de rôle pour toutes les ressources enfants

* Toutes les modifications d’attribution de rôles actifs et éligibles

| Éléments à analyser| Niveau de risque| Where| Filtre/Sous-filtre| Notes |
| - |- |- |- |- |
| Alerte d’audit concernant le journal des audits de ressources pour Activités de compte privilégié| Élevé| Dans PIM, sous Ressources Azure, Audit des ressources| Action : Ajout de membre éligible au rôle dans PIM terminé (durée maximale) <br>-et-<br>Cible principale <br>-et-<br>Type d’utilisateur<br>-et-<br>État = Réussite<br>| Toujours déclencher une alerte. Permet de détecter lorsqu’un acteur malveillant ajoute des rôles éligibles pour gérer toutes les ressources présentes dans Azure. |
| Alerte d’audit concernant l’audit des ressources pour Désactivation de l’alerte| Moyenne| Dans PIM, sous Ressources Azure, Audit des ressources| Action : Désactivation de l’alerte<br>-et-<br>Cible principale : Trop de propriétaires affectés à une ressource<br>-et-<br>État = Réussite| Permet de détecter lorsqu’un acteur malveillant désactive les alertes dans le volet Alertes pour empêcher l’investigation des activités malveillantes. |
| Alerte d’audit concernant l’audit des ressources pour Désactivation de l’alerte| Moyenne| Dans PIM, sous Ressources Azure, Audit des ressources| Action : Désactivation de l’alerte<br>-et-<br>Cible principale : Trop de propriétaires permanents affectés à une ressource<br>-et-<br>État = Réussite| Empêche l’acteur malveillant de désactiver les alertes dans le volet Alertes dans le but d’empêcher l’investigation des activités malveillantes. |
| Alerte d’audit concernant l’audit des ressources pour Désactivation de l’alerte| Moyenne| Dans PIM, sous Ressources Azure, Audit des ressources| Action : Désactivation de l’alerte<br>-et-<br>Cible principale - Rôle créé en double<br>-et-<br>État = Réussite| Empêche l’acteur malveillant de désactiver les alertes dans le volet Alertes dans le but d’empêcher l’investigation des activités malveillantes. |


Pour plus d’informations sur la configuration des alertes et l’audit des rôles de ressources Azure, consultez :

* [Configurer les alertes de sécurité pour les rôles de ressources Azure dans Privileged Identity Management](../privileged-identity-management/pim-resource-roles-configure-alerts.md)

* [Afficher le rapport d’audit pour les rôles de ressources Azure dans Privileged Identity Management (PIM)](../privileged-identity-management/azure-pim-resource-rbac.md)

## <a name="access-management-for-azure-resources-and-subscriptions"></a>Gestion de l’accès pour les ressources et les abonnements Azure

Les utilisateurs ou membres d’un groupe affecté aux rôles d’abonnement Propriétaire ou Administrateur d’accès utilisateur, ainsi que les administrateurs généraux Azure AD qui activent la gestion des abonnements dans Azure AD, disposent des droits d’administrateur de ressources par défaut. Ces administrateurs peuvent affecter des rôles, configurer des paramètres de rôle et examiner les accès à l’aide de Privileged Identity Management (PIM) pour les ressources Azure. 

Un utilisateur disposant d’autorisations d’administrateur de ressources peut gérer PIM pour les ressources. Le risque que cela implique et qui nécessite une supervision et une atténuation de votre part, est que cette fonctionnalité peut être utilisée pour permettre aux acteurs malveillants d’avoir un accès privilégié aux ressources d’abonnement Azure, telles que les machines virtuelles ou les comptes de stockage.

| Éléments à analyser| Niveau de risque| Where| Filtre/Sous-filtre| Notes |
| - |- |- |- |- |
| Élévations| Élevé| Azure AD, sous Gérer, Propriétés| Vérifiez régulièrement le paramètre.<br>Gestion de l’accès pour les ressources Azure| Les administrateurs généraux peuvent élever les autorisations en activant la gestion des accès pour les ressources Azure.<br>Vérifiez que des acteurs malveillants n’ont pas obtenu les autorisations permettant d’attribuer des rôles dans tous les abonnements et groupes d’administration Azure associés à Active Directory. |


Pour plus d’informations, consultez [Attribuer des rôles de ressources Azure dans Privileged Identity Management](../privileged-identity-management/pim-resource-roles-assign-roles.md).

## <a name="next-steps"></a>Étapes suivantes
Consultez les articles suivants sur les opérations de sécurité :

[Vue d’ensemble des opérations de sécurité Azure AD](security-operations-introduction.md)

[Opérations de sécurité pour les comptes d’utilisateur](security-operations-user-accounts.md)

[Opérations de sécurité pour les comptes privilégiés](security-operations-privileged-accounts.md)

[Opérations de sécurité pour Privileged Identity Management](security-operations-privileged-identity-management.md)

[Opérations de sécurité pour les applications](security-operations-applications.md)

[Opérations de sécurité pour les appareils](security-operations-devices.md)
 
[Opérations de sécurité pour l’infrastructure](security-operations-infrastructure.md)
