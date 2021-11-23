---
title: Opérations de sécurité Azure Active Directory pour les comptes d’utilisateur
description: Conseils pour établir des lignes de base et comment surveiller et alerter sur les problèmes de sécurité potentiels des comptes d’utilisateurs.
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
ms.openlocfilehash: e386add0cab29ed0570f3b0b72d6e34cbb363849
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132292558"
---
# <a name="azure-active-directory-security-operations-for-user-accounts"></a>Opérations de sécurité Azure Active Directory pour les comptes d’utilisateur

L’identité de l’utilisateur est l’un des aspects les plus importants de la protection de votre organisation et de vos données. Cet article fournit des conseils pour la surveillance de la création, de la suppression et de l’utilisation de comptes. La première partie explique comment surveiller la création et la suppression de comptes inhabituels. La deuxième partie explique comment surveiller l’utilisation inhabituelle des comptes. 

Si vous n’avez pas encore lu la [vue d’ensemble des opérations de sécurité Azure Active Directory (Azure AD)](security-operations-introduction.md), nous vous recommandons de le faire avant de continuer.

Cet article couvre les comptes d’utilisateur généraux. Pour les comptes privilégiés, consultez Opérations de sécurité – comptes privilégiés.

## <a name="define-a-baseline"></a>Définir une ligne de base

Pour découvrir un comportement anormal, vous devez d’abord définir le comportement normal et attendu. Le fait de définir le comportement attendu pour votre organisation vous aide à déterminer quand un comportement inattendu se produit. La définition permet également de réduire le niveau de bruit de faux positifs lors de la surveillance et de la génération d’alertes. 

Une fois que vous avez défini ce que vous attendez, vous effectuez une analyse de ligne de base pour valider vos attentes. Grâce à ces informations, vous pouvez surveiller les journaux pour détecter tout ce qui sort des tolérances que vous avez définies. 

Utilisez les journaux d’audit Azure AD, les journaux de connexion Azure AD et les attributs d’annuaire comme sources de données pour les comptes créés en dehors des processus normaux. Voici quelques suggestions pour vous aider à réfléchir et à définir ce qui est normal pour votre organisation.

* **Création de compte d’utilisateur** : évaluez les éléments suivants :

   * Stratégie et principes pour les outils et les processus utilisés pour créer et gérer des comptes d’utilisateur. Par exemple, il s’agit d’attributs standard, de formats appliqués aux attributs de compte d’utilisateur. 

  * Sources approuvées pour la création de comptes. Par exemple, provenant d’Active Directory (AD), d’Azure Active Directory ou de systèmes RH tels que Workday.

  * Stratégie d’alerte pour les comptes créés en dehors des sources approuvées. Existe-t-il une liste contrôlée d’organisations avec lesquelles votre organisation collabore ?

  * Approvisionnement des comptes invités et des paramètres d’alerte pour les comptes créés en dehors de la gestion des droits d’utilisation ou d’autres processus normaux.

  * Paramètres de stratégie et d’alerte pour les comptes créés, modifiés ou désactivés par un compte qui n’est pas un administrateur d’utilisateur approuvé.

  * Stratégie de surveillance et d’alerte pour les comptes qui n’ont pas d’attributs standard, tels que l’ID d’employé ou qui ne suivent pas les conventions de nommage organisationnelles.

  * Stratégie, principes et processus pour la suppression et la rétention des comptes.

* **Comptes d’utilisateur locaux** : évaluez les éléments suivants pour les comptes synchronisés avec Azure AD Connect :

  * Les forêts, les domaines et les unités d’organisation (UO) dans l’étendue de la synchronisation. Qui sont les administrateurs autorisés qui peuvent modifier ces paramètres et à quelle fréquence l’étendue est-elle vérifiée ?

  * Les types de comptes synchronisés. Par exemple, comptes d’utilisateur et/ou comptes de service. 

  * Le processus de création de comptes privilégiés locaux et la manière dont la synchronisation de ce type de compte est contrôlée.

  * Le processus de création de comptes d’utilisateur locaux et la manière dont la synchronisation de ce type de compte est managée.

Pour plus d’informations sur la sécurisation et la surveillance des comptes locaux, consultez [Protection de Microsoft 365 contre les attaques locales](protect-m365-from-on-premises-attacks.md).

* **Comptes d’utilisateur cloud** : évaluez les éléments suivants :

  * Le processus d’approvisionnement et de gestion des comptes cloud directement dans Azure AD.

  * Le processus permettant de déterminer les types d’utilisateurs approvisionnés en tant que comptes cloud Azure AD. Par exemple, n’autorisez-vous que les comptes privilégiés ou autorisez-vous également les comptes d’utilisateurs ?

  * Le processus de création et de maintien d’une liste de personnes et/ou de processus de confiance censés créer et gérer les comptes d’utilisateurs du cloud.

  * Le processus pour créer et maintenir une stratégie d’alerte pour les comptes basés sur le cloud non approuvés. 

## <a name="where-to-look"></a>Emplacement des fichiers

Les fichiers journaux que vous pouvez utiliser pour l’investigation et la supervision sont les suivants : 

* [Journaux d’audit Azure AD](../reports-monitoring/concept-audit-logs.md)

* [Journaux de connexion](../reports-monitoring/concept-all-sign-ins.md)

* [Journaux d’audit Microsoft 365](/microsoft-365/compliance/auditing-solutions-overview) 

* [Journaux Azure Key Vault](../../key-vault/general/logging.md?tabs=Vault)

* [Journal des utilisateurs à risque](../identity-protection/howto-identity-protection-investigate-risk.md)

* [Journal UserRiskEvents](../identity-protection/howto-identity-protection-investigate-risk.md)

Dans le Portail Azure, vous pouvez afficher les journaux d’audit Azure AD et les télécharger sous forme de fichiers CSV ou JSON. Le Portail Azure offre plusieurs méthodes d’intégration entre les journaux Azure AD et d’autres outils, ce qui permet une plus grande automatisation du monitoring et des alertes :

* **[Microsoft Sentinel](../../sentinel/overview.md)**  : permet une analytique de sécurité intelligente au niveau de l’entreprise en fournissant des fonctionnalités d’informations de sécurité et gestion d’événements (SIEM, Security Information and Event Management). 

* **[Azure Monitor](../../azure-monitor/overview.md)** – Permet de créer des alertes et supervisions automatisées de diverses conditions. Peut créer ou utiliser des classeurs pour combiner des données provenant de différentes sources.

* **[Azure Event Hubs](../../event-hubs/event-hubs-about.md) avec intégration SIEM**- [Les journaux Azure AD peuvent être intégrés à d’autres SIEM](../reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md) comme Splunk, ArcSight, QRadar et Sumo Logic via l’intégration Azure Event Hub Hub.

* **[Microsoft Defender for Cloud Apps](/cloud-app-security/what-is-cloud-app-security)**  : permet de découvrir et de gérer les applications, de gouverner toutes les applications et ressources, et de vérifier la conformité des applications cloud. 

La plupart des éléments qui font l’objet d’une supervision et d’alertes sont déterminés par vos stratégies d’accès conditionnel. Vous pouvez utiliser le [classeur Rapports et insights sur l’accès conditionnel](../conditional-access/howto-conditional-access-insights-reporting.md) pour examiner les effets d’une ou de plusieurs stratégies d’accès conditionnel sur vos connexions, ainsi que les résultats des stratégies, y compris l’état de l’appareil. Ce classeur permet de voir un résumé de l’impact et d’identifier l’incidence sur une période donnée. Vous pouvez également vous en servir pour examiner les connexions d’un utilisateur spécifique. 

 Le reste de cet article comprend des recommandations concernant la supervision et les alertes, qui sont organisées par type de menace. Lorsqu’il existe des solutions prédéfinies spécifiques, nous en indiquons le lien ou donnons des exemples après le tableau. Sinon, vous pouvez créer des alertes à l’aide des outils précédents. 

## <a name="account-creation"></a>Création d’un compte

La création d’un compte anormal peut indiquer un problème de sécurité. Les comptes à courte durée de vie, les comptes qui ne suivent pas les normes d’affectation de noms et les comptes créés en dehors des processus normaux doivent être examinés.

### <a name="short-lived-accounts"></a>Comptes à courte durée de vie

La création et la suppression de comptes en dehors des processus de gestion des identités normaux doivent être surveillées dans Azure AD. Les comptes à courte durée de vie sont des comptes créés et supprimés dans une courte période. Ce type de création et de suppression rapide de compte peut signifier qu’un intervenant malveillant tente d’éviter la détection en créant des comptes, en les utilisant, puis en supprimant le compte.

Les modèles de compte à courte durée de vie peuvent indiquer que des personnes ou des processus non approuvés peuvent avoir le droit de créer et de supprimer des comptes qui se trouvent en dehors des processus et stratégies établis. Ce type de comportement supprime les marqueurs visibles du répertoire. 

Si la trace de données pour la création et la suppression de comptes n’est pas découverte rapidement, les informations requises pour examiner un incident peuvent ne plus exister. Par exemple, les comptes peuvent être effacés, puis supprimés définitivement de la corbeille. Les journaux d’audit sont conservés pendant 30 jours. Toutefois, vous pouvez exporter vos journaux vers Azure Monitor ou une solution SIEM (Security information and Event Management) pour la rétention à plus long terme. 

| Éléments à analyser                                                  | Niveau de risque | Where               | Filtre/Sous-filtre                                                                                                                                         | Notes                                                                                                                               |
|------------------------------------------------------------------|------------|---------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------|
| Événements de création et de suppression de comptes dans un délai d’exécution rapproché.  | Élevé       | Journaux d’audit Azure AD | Activité : ajouter un utilisateur<br>État = réussite<br>-et-<br>Activité : supprimer un utilisateur<br>État = réussite<br>                                                          | Recherchez les événements UPN (User Principal Name). Recherchez les comptes créés puis supprimés en moins de 24 heures.                          |
| Comptes créés et supprimés par des utilisateurs ou des processus non approuvés. | Moyenne     | Journaux d’audit Azure AD | Initié par (intervenant) : USER PRINCIPAL NAME<br>-et-<br>Activité : ajouter un utilisateur<br>État = réussite<br>and-or<br>Activité : supprimer un utilisateur<br>État = réussite      | Si les intervenants sont des utilisateurs non approuvés, configurez pour envoyer une alerte.                                                                    |
| Comptes provenant de sources non approuvées.                              | Moyenne     | Journaux d’audit Azure AD | Activité : ajouter un utilisateur<br>État = réussite<br>Cible(s) = USER PRINCIPAL NAME                                                                                 | Si l’entrée ne provient pas d’un domaine approuvé ou s’il s’agit d’un domaine bloqué connu, configurez pour envoyer une alerte.                               |
| Comptes affectés à un rôle privilégié.                          | Élevé       | Journaux d’audit Azure AD | Activité : ajouter un utilisateur<br>État = réussite<br>-et-<br>Activité : supprimer un utilisateur<br>État = réussite<br>-et-<br>Activité : ajout d’un membre au rôle<br>État = réussite | Si le compte est affecté à un rôle Azure AD, un rôle Azure ou l’appartenance à un groupe privilégié, alertez et hiérarchisez l’investigation. |

Les comptes privilégiés et non privilégiés doivent être surveillés et alertés. Toutefois, étant donné que les comptes privilégiés disposent d’autorisations d’administration, ils doivent avoir une priorité plus élevée dans vos processus de surveillance, d’alerte et de réponse. 

### <a name="accounts-not-following-naming-policies"></a>Comptes qui ne suivent pas les stratégies d’attribution de noms

Les comptes d’utilisateurs qui ne suivent pas les stratégies d’attribution de noms ont peut-être été créés en dehors des stratégies organisationnelles. 

Une meilleure pratique consiste à avoir une stratégie d’attribution de noms pour les objets utilisateur. Le fait de disposer d’une stratégie d’attribution de noms facilite la gestion et aide à assurer la cohérence. La stratégie peut également vous aider à découvrir le moment où les utilisateurs ont été créés en dehors des processus approuvés. Un intervenant malveillant peut ne pas être conscient de vos normes d’affectation de noms et peut faciliter la détection d’un compte approvisionné en dehors de vos processus organisationnels.

Les organisations ont tendance à avoir des formats et des attributs spécifiques qui sont utilisés pour créer des comptes d’utilisateurs et/ou des comptes privilégiés. Par exemple :

* UPN compte d’administrateur = ADM_firstname.lastname@tenant.onmicrosoft.com

* UPN compte d’utilisateur = Firstname.Lastname@contoso.com

Les comptes d’utilisateur ont également souvent un attribut qui identifie un utilisateur réel. Par exemple, EMPID = XXXNNN. Vous trouverez ci-dessous des suggestions pour vous aider à réfléchir et à définir ce qui est normal pour votre organisation, ainsi que des éléments à prendre en compte lorsque vous définissez votre ligne de base pour les entrées de journal où les comptes ne suivent pas la convention d’affectation de noms de votre organisation :

* Comptes qui ne respectent pas la convention d’affectation de noms. Par exemple, `nnnnnnn@contoso.com` comparé à `firstname.lastname@contoso.com`. 

* Les comptes dont les attributs standard ne sont pas renseignés ou dont le format n’est pas correct. Par exemple, vous ne disposez pas d’un ID d’employé valide.

| Éléments à analyser| Niveau de risque| Where| Filtre/Sous-filtre| Notes |
| - | - | - | - | - |
| Comptes d’utilisateur pour lesquels aucun attribut attendu n’est défini.| Faible| Journaux d’audit Azure AD| Activité : ajouter un utilisateur<br>État = réussite| Recherchez les comptes avec vos attributs standard Null ou dans un format incorrect. Par exemple, EmployeeID |
| Comptes d’utilisateur créés à l’aide d’un format d’affectation de noms incorrect.| Faible| Journaux d’audit Azure AD| Activité : ajouter un utilisateur<br>État = réussite| Recherchez les comptes avec un UPN qui ne suit pas votre stratégie d’affectation de noms. |
| Comptes privilégiés qui ne suivent pas la stratégie d’affectation de noms.| Élevé| Abonnement Azure| [Répertorier les attribution de rôle Azure à l’aide du portail Azure – Azure RBAC](../../role-based-access-control/role-assignments-list-portal.md)| Répertoriez les attributions de rôles pour les abonnements et alertez lorsque le nom de connexion ne correspond pas au format de votre organisation. Par exemple, ADM_ en tant que préfixe. |
| Comptes privilégiés qui ne suivent pas la stratégie d’affectation de noms.| Élevé| Annuaire Azure AD| [Répertorier les attributions de rôle Azure AD](../roles/view-assignments.md)| Répertoriez les attributions de rôles Azure AD pour les alertes des rôles où l’UPN ne correspond pas au format de votre organisation. Par exemple, ADM_ en tant que préfixe. |



Pour plus d’informations sur les analyses, consultez :

* Pour les journaux d’audit Azure AD - [Analyser des données de texte dans les journaux Azure Monitor](../../azure-monitor/logs/parse-text.md)

* Pour les abonnements Azure - [Lister les attributions de rôle Azure avec Azure PowerShell](../../role-based-access-control/role-assignments-list-powershell.md)

* Pour Azure Active Directory - [Lister les affectations de rôles Azure AD](../roles/view-assignments.md) 

### <a name="accounts-created-outside-normal-processes"></a>Comptes créés en dehors des processus normaux

Il est important de disposer de processus standard pour créer des utilisateurs et des comptes privilégiés afin de pouvoir contrôler en toute sécurité le cycle de vie des identités. Si les utilisateurs sont approvisionnés et déprovisionnés en dehors des processus établis, cela peut introduire des risques de sécurité. Le fonctionnement en dehors des processus établis peut également introduire des problèmes de gestion des identités. Les risques potentiels sont les suivants :

* Les comptes d’utilisateurs et privilégiés peuvent ne pas être régis par des stratégies organisationnelles. Cela peut donner lieu à une surface d’attaque plus étendue sur les comptes qui ne sont pas managés correctement.

* Cela devient plus difficile à détecter lorsque des intervenants malveillants créent des comptes à des fins malveillantes. En ayant des comptes valides créés en dehors des procédures établies, il devient plus difficile de détecter à quel moment les comptes sont créés ou les autorisations modifiées à des fins malveillantes. 

Nous vous recommandons de créer des comptes d’utilisateur et privilégiés uniquement en suivant les stratégies de votre organisation. Par exemple, un compte doit être créé avec les normes d’affectation de noms correctes, les informations organisationnelles et dans le cadre de la gouvernance d’identité appropriée. Les organisations doivent avoir des contrôles rigoureux pour qui dispose des droits nécessaires pour créer, gérer et supprimer des identités. Les rôles permettant de créer ces comptes doivent être gérés de manière stricte et les droits ne doivent être disponibles qu’après avoir suivi un workflow établi pour approuver et obtenir ces autorisations.

| Éléments à analyser| Niveau de risque| Where| Filtre/Sous-filtre| Notes |
| - | - | - | - | - |
| Comptes d’utilisateur créés et supprimés par des utilisateurs ou des processus non approuvés.| Moyenne| Journaux d’audit Azure AD| Activité : ajouter un utilisateur<br>État = réussite<br>and-or-<br>Activité : supprimer un utilisateur<br>État = réussite<br>-et-<br>Initié par (intervenant) = USER PRINCIPAL NAME| Alerte sur les comptes créés par des utilisateurs ou des processus non approuvés. Classer par ordre de priorité les comptes créés avec des privilèges renforcés. |
| Comptes d’utilisateur créés ou supprimés de sources non approuvées.| Moyenne| Journaux d’audit Azure AD| Activité : ajouter un utilisateur<br>État = réussite<br>-ou-<br>Activité : supprimer un utilisateur<br>État = réussite<br>-et-<br>Cible(s) = USER PRINCIPAL NAME| Alerte lorsque le domaine est un domaine non approuvé ou un domaine bloqué connu. |


## <a name="unusual-sign-ins"></a>Connexions inhabituelles

L’affichage des échecs pour l’authentification de l’utilisateur est normal. Toutefois, le fait de voir des modèles ou des blocs de défaillances peut indiquer qu’un événement se produit avec l’identité d’un utilisateur. Par exemple, dans le cas d’attaques par pulvérisation de mots de passe ou par force brute, ou lorsqu’un compte d’utilisateur est compromis. Il est essentiel que vous surveilliez et alertiez lorsque des modèles émergent. Cela permet de garantir que vous pouvez protéger l’utilisateur et les données de votre organisation. 

La réussite semble dire que tout va bien. Toutefois, cela peut signifier qu’un intervenant malveillant a réussi à accéder à un service. La surveillance des connexions réussies vous aide à détecter les comptes d’utilisateurs qui obtiennent un accès mais qui ne sont pas des comptes d’utilisateurs qui devraient avoir un accès. Les réussites de l’authentification utilisateur sont des entrées normales dans les journaux de connexion Azure AD. Nous vous recommandons de surveiller et d’alerter pour détecter l’émergence de modèles. Cela permet de garantir que vous pouvez protéger les comptes d’utilisateur et les données de votre organisation. 


Lors de la conception et de la mise en œuvre d’une stratégie de surveillance et d’alerte des journaux, tenez compte des outils disponibles dans le Portail Azure. Identity Protection vous permet d’automatiser la détection, la protection et la correction des risques basés sur l’identité. Identity Protection utilise des systèmes heuristiques et d’apprentissage automatique alimentés par l’intelligence pour détecter les risques et attribuer un score de risque aux utilisateurs et aux connexions. Les clients peuvent configurer des stratégies basées sur un niveau de risque pour autoriser ou refuser l’accès ou permettre à l’utilisateur d’effectuer une correction automatique en cas de risque. Les fonctionnalités de détection des risques d’Identity Protection suivantes vous informent des niveaux de risque aujourd’hui :

| Éléments à analyser | Niveau de risque | Where | Filtre/Sous-filtre | Notes |
| - | - | - | - | - |
| Détection des risques de l’utilisateur des informations d’identification fuitées| Élevé| Journaux de détection des risques Azure AD| UX : Informations d’identification fuitées <br><br>API : consultez [type de ressource riskDetection - Microsoft Graph version bêta](/graph/api/resources/riskdetection?view=graph-rest-beta&preserve-view=true)| Consultez [Qu’est-ce que le risque ? Azure AD Identity Protection](../identity-protection/concept-identity-protection-risks.md) |
| Détection des risques des utilisateurs Azure AD Threat Intelligence| Élevé| Journaux de détection des risques Azure AD| UX : Azure AD Threat Intelligence <br><br>API : consultez [type de ressource riskDetection - Microsoft Graph version bêta](/graph/api/resources/riskdetection?view=graph-rest-beta&preserve-view=true)| Consultez [Qu’est-ce que le risque ? Azure AD Identity Protection](../identity-protection/concept-identity-protection-risks.md) |
| Détection des risques de connexion à une adresse IP anonyme| Variable| Journaux de détection des risques Azure AD| UX : adresse IP anonyme <br><br>API : consultez [type de ressource riskDetection - Microsoft Graph version bêta](/graph/api/resources/riskdetection?view=graph-rest-beta&preserve-view=true)| Consultez [Qu’est-ce que le risque ? Azure AD Identity Protection](../identity-protection/concept-identity-protection-risks.md) |
| Détection des risques de connexion de voyage atypique| Variable| Journaux de détection des risques Azure AD| UX : voyage atypique <br><br>API : consultez [type de ressource riskDetection - Microsoft Graph version bêta](/graph/api/resources/riskdetection?view=graph-rest-beta&preserve-view=true)| Consultez [Qu’est-ce que le risque ? Azure AD Identity Protection](../identity-protection/concept-identity-protection-risks.md) |
| Jeton anormal| Variable| Journaux de détection des risques Azure AD| UX : jeton anormal <br><br>API : consultez [type de ressource riskDetection - Microsoft Graph version bêta](/graph/api/resources/riskdetection?view=graph-rest-beta&preserve-view=true)| Consultez [Qu’est-ce que le risque ? Azure AD Identity Protection](../identity-protection/concept-identity-protection-risks.md) |
| Détection du risque de connexion par adresse IP liée à un programme malveillant| Variable| Journaux de détection des risques Azure AD| UX : adresse IP liée à un programme malveillant <br><br>API : consultez [type de ressource riskDetection - Microsoft Graph version bêta](/graph/api/resources/riskdetection?view=graph-rest-beta&preserve-view=true)| Consultez [Qu’est-ce que le risque ? Azure AD Identity Protection](../identity-protection/concept-identity-protection-risks.md) |
| Détection de risque de connexion à un navigateur suspect| Variable| Journaux de détection des risques Azure AD| UX : navigateur suspect <br><br>API : consultez [type de ressource riskDetection - Microsoft Graph version bêta](/graph/api/resources/riskdetection?view=graph-rest-beta&preserve-view=true)| Consultez [Qu’est-ce que le risque ? Azure AD Identity Protection](../identity-protection/concept-identity-protection-risks.md) |
| Détection des risques de connexion avec des propriétés de connexion inhabituelles| Variable| Journaux de détection des risques Azure AD| UX : propriétés de connexion inhabituelles <br><br>API : consultez [type de ressource riskDetection - Microsoft Graph version bêta](/graph/api/resources/riskdetection?view=graph-rest-beta&preserve-view=true)| Consultez [Qu’est-ce que le risque ? Azure AD Identity Protection](../identity-protection/concept-identity-protection-risks.md) |
| Détection des risques de connexion à une adresse IP malveillante| Variable| Journaux de détection des risques Azure AD| UX : adresse IP malveillante<br><br>API : consultez [type de ressource riskDetection - Microsoft Graph version bêta](/graph/api/resources/riskdetection?view=graph-rest-beta&preserve-view=true)| Consultez [Qu’est-ce que le risque ? Azure AD Identity Protection](../identity-protection/concept-identity-protection-risks.md) |
| Détection des risques de connexion liés aux règles suspectes de manipulation de boîte de réception| Variable| Journaux de détection des risques Azure AD| UX : règles suspectes de manipulation de boîte de réception<br><br>API : consultez [type de ressource riskDetection - Microsoft Graph version bêta](/graph/api/resources/riskdetection?view=graph-rest-beta&preserve-view=true)| Consultez [Qu’est-ce que le risque ? Azure AD Identity Protection](../identity-protection/concept-identity-protection-risks.md) |
| Détection des risques de connexion par pulvérisation de mots de passe| Élevé| Journaux de détection des risques Azure AD| UX : pulvérisation de mots de passe<br><br>API : consultez [type de ressource riskDetection - Microsoft Graph version bêta](/graph/api/resources/riskdetection?view=graph-rest-beta&preserve-view=true)| Consultez [Qu’est-ce que le risque ? Azure AD Identity Protection](../identity-protection/concept-identity-protection-risks.md) |
| Détection des risques de connexion de voyage impossible| Variable| Journaux de détection des risques Azure AD| UX : voyage impossible<br><br>API : consultez [type de ressource riskDetection - Microsoft Graph version bêta](/graph/api/resources/riskdetection?view=graph-rest-beta&preserve-view=true)| Consultez [Qu’est-ce que le risque ? Azure AD Identity Protection](../identity-protection/concept-identity-protection-risks.md) |
| Détection des risques de connexion du nouveau pays| Variable| Journaux de détection des risques Azure AD| UX : nouveau pays<br><br>API : consultez [type de ressource riskDetection - Microsoft Graph version bêta](/graph/api/resources/riskdetection?view=graph-rest-beta&preserve-view=true)| Consultez [Qu’est-ce que le risque ? Azure AD Identity Protection](../identity-protection/concept-identity-protection-risks.md) |
| Détection des risques de connexion liés à une activité depuis une adresse IP anonyme| Variable| Journaux de détection des risques Azure AD| UX : activité depuis une adresse IP anonyme<br><br>API : consultez [Type de ressource riskDetection - Microsoft Graph version bêta](/graph/api/resources/riskdetection?view=graph-rest-beta&preserve-view=true)| Consultez [Qu’est-ce que le risque ? Azure AD Identity Protection](../identity-protection/concept-identity-protection-risks.md) |
| Détection des risques de connexion de transfert de boîte de réception suspect| Variable| Journaux de détection des risques Azure AD| UX : transfert de boîte de réception suspect<br><br>API : consultez [Type de ressource riskDetection - Microsoft Graph version bêta](/graph/api/resources/riskdetection?view=graph-rest-beta&preserve-view=true)| Consultez [Qu’est-ce que le risque ? Azure AD Identity Protection](../identity-protection/concept-identity-protection-risks.md) |
| Détection des risques de connexion Azure AD Threat Intelligence| Élevé| Journaux de détection des risques Azure AD| UX : Azure AD Threat Intelligence<br>API : consultez [Type de ressource riskDetection - Microsoft Graph version bêta](/graph/api/resources/riskdetection?view=graph-rest-beta&preserve-view=true)| Consultez [Qu’est-ce que le risque ? Azure AD Identity Protection](../identity-protection/concept-identity-protection-risks.md) |

Pour plus d’informations, visitez [Qu’est-ce qu’Identity Protection ?](../identity-protection/overview-identity-protection.md). 


### <a name="what-to-look-for"></a>Ce que vous devez surveiller

Configurez l’analyse des données dans les journaux de connexion Azure AD pour vous assurer que les alertes sont émises et respectent les stratégies de sécurité de votre organisation. Quelques exemples :

* **Échecs d’authentification** : comme tout être humain, nos mots de passe sont incorrects de temps en temps. Cependant, de nombreux échecs d’authentification peuvent indiquer qu’un intervenant malveillant tente d’obtenir un accès. Les attaques diffèrent en férocité mais peuvent aller de quelques tentatives par heure à un taux beaucoup plus élevé. Par exemple, la pulvérisation de mots de passe s’attaque normalement aux mots de passe les plus faciles sur de nombreux comptes, tandis que la force brute tente d’obtenir de nombreux mots de passe sur des comptes ciblés. 

* **Authentifications interrompues** : une interruption dans Azure AD représente une injection d’un processus supplémentaire pour satisfaire l’authentification, par exemple lors de l’application d’un contrôle dans une stratégie d’autorité de certification. Il s’agit d’un événement normal qui peut se produire lorsque les applications ne sont pas configurées correctement. Toutefois, lorsque vous voyez de nombreuses interruptions pour un compte d’utilisateur, cela peut indiquer un problème avec ce compte. 

  * Par exemple, si vous avez filtré un utilisateur dans les journaux de connexion et que vous voyez un volume important d’états de connexion = interruption et accès conditionnel = échec. En allant plus loin, les détails de l’authentification peuvent indiquer que le mot de passe est correct, mais qu’une authentification forte est nécessaire. Cela peut signifier que l’utilisateur n’effectue pas l’authentification multifacteur (MFA), ce qui peut indiquer que le mot de passe de l’utilisateur est compromis et que l’intervenant malveillant n’est pas en mesure d’exécuter la MFA.

* **Verrouillage intelligent** : Azure AD fournit un service de verrouillage intelligent qui introduit le concept d’emplacements familiers et non familiers pour le processus d’authentification. Un compte d’utilisateur visitant un emplacement familier peut s’authentifier avec succès, alors qu’un intervenant malveillant qui ne connaît pas le même emplacement est bloqué après plusieurs tentatives. Recherchez les comptes qui ont été verrouillés et effectuez des recherches plus poussées. 

* **Modifications IP** : il est normal de voir les utilisateurs provenant de différentes adresses IP. Toutefois, les états de Confiance Zéro n’approuvent jamais et vérifient toujours. L’affichage d’un volume important d’adresses IP et de connexions ayant échoué peut être un indicateur d’intrusion. Recherchez un modèle de nombreuses authentifications ayant échoué à partir de plusieurs adresses IP. Notez que les connexions de réseau privé virtuel (VPN) peuvent provoquer des faux positifs. Quels que soient les défis, nous vous recommandons de surveiller les modifications d’adresses IP et, si possible, d’utiliser Azure AD Identity Protection pour détecter et atténuer automatiquement ces risques.

* **Emplacements** : en général, vous vous attendez à ce qu’un compte d’utilisateur se trouve dans le même emplacement géographique. Vous vous attendez également à des connexions à partir d’emplacements où vous avez des employés ou des relations commerciales. Lorsque le compte d’utilisateur provient d’un autre emplacement international en moins de temps que celui qu’il faudrait pour se déplacer, cela peut indiquer que le compte d’utilisateur fait l’objet d’un abus. Notez que les VPN pouvant entraîner des faux positifs, nous vous recommandons de surveiller les comptes d’utilisateur qui se connectent à partir d’emplacements éloignés géographiquement et, si possible, d’utiliser Azure AD Identity Protection pour détecter et atténuer automatiquement ces risques.

Pour cette zone à risque, nous vous recommandons de surveiller les comptes d’utilisateur standard et les comptes privilégiés, mais de classer par ordre de priorité les recherches de comptes privilégiés. Les comptes privilégiés sont les comptes les plus importants d’un locataire Azure AD. Pour des conseils spécifiques sur les comptes privilégiés, consultez Opérations de sécurité – comptes privilégiés. 

### <a name="how-to-detect"></a>Mode de détection

Vous utilisez Azure Identity Protection et les journaux de connexion Azure AD pour vous aider à détecter les menaces indiquées par des caractéristiques de connexion inhabituelles. Des informations sur Identity Protection sont disponibles dans [Présentation d’Identity Protection](../identity-protection/overview-identity-protection.md). Vous pouvez également répliquer les données vers Azure Monitor ou SIEM à des fins de surveillance et d’alerte. Pour définir la normale pour votre environnement et pour définir une ligne de base, déterminez les éléments suivants :

* Les paramètres que vous considérez comme normaux pour votre base d’utilisateurs.

* Le nombre moyen d’essais d’un mot de passe sur une période donnée avant que l’utilisateur n’appelle le service d’assistance ou n’effectue une réinitialisation de mot de passe en libre-service.

* Le nombre de tentatives infructueuses que vous souhaitez autoriser avant d’émettre une alerte, et si ce nombre sera différent pour les comptes d’utilisateurs et les comptes privilégiés.

* Le nombre de tentatives MFA que vous souhaitez autoriser avant d’émettre une alerte, et si ce nombre sera différent pour les comptes d’utilisateurs et les comptes privilégiés.

* Si l’authentification héritée est activée et votre feuille de route pour l’arrêt de l’utilisation. 

* Les adresses IP de sortie connues sont destinées à votre organisation.

* Les pays à partir desquels vos utilisateurs opèrent.

* Si des groupes d’utilisateurs restent stationnaires au sein d’un emplacement réseau ou d’un pays.

* Identifiez tous les autres indicateurs des connexions inhabituelles qui sont spécifiques à votre organisation. Par exemple, les jours ou les moments de la semaine ou de l’année où votre organisation n’est pas opérationnelle.

Une fois que vous avez défini ce qui est normal pour les types de comptes dans votre environnement, considérez les points suivants pour vous aider à déterminer les scénarios que vous voulez surveiller et pour lesquels vous voulez émettre des alertes et affiner celles-ci.

* Avez-vous besoin de surveiller et d’émettre des alertes si Identity Protection est configuré ?

* Existe-t-il des conditions plus strictes appliquées aux comptes privilégiés que vous pouvez utiliser pour surveiller et émettre des alertes ? Par exemple, la nécessité d’utiliser des comptes privilégiés uniquement à partir d’adresses IP approuvées.

* Les lignes de base que vous définissez sont-elles trop agressives ? Si vous avez trop d’alertes, vous risquez de les ignorer ou de les manquer.

Configurez Identity Protection pour vous assurer que la protection est en place et prend en charge vos stratégies de ligne de base de sécurité. Par exemple, bloquer les utilisateurs si risque = élevé. Ce niveau de risque indique avec un degré élevé de confiance qu’un compte d’utilisateur est compromis. Pour plus d’informations sur la configuration des stratégies de risque de connexion et des stratégies de risque d’utilisateur, consultez [Stratégies Identity Protection](../identity-protection/concept-identity-protection-policies.md). Pour plus d’informations sur la configuration de l’accès conditionnel, consultez [Accès conditionnel : accès conditionnel en fonction du risque de connexion](../conditional-access/howto-conditional-access-policy-risk.md).

Les éléments suivants sont répertoriés par ordre d’importance en fonction de l’impact et de la gravité des entrées.

### <a name="monitoring-for-failed-unusual-sign-ins"></a>Surveillance des connexions inhabituelles ayant échoué

| Éléments à analyser| Niveau de risque| Where| Filtre/Sous-filtre| Notes |
| - |- |- |- |- |
| Échecs de tentatives de connexion.| Moyen - si incident isolé<br>Élevé - si un certain nombre de comptes rencontrent le même modèle ou une adresse IP virtuelle.| Journal de connexions Azure AD| État = échec<br>-et-<br>Code d’erreur de connexion 50126 - <br>Erreur de validation des informations d’identification en raison d’un nom d’utilisateur ou d’un mot de passe non valide.| Définissez un seuil de ligne de base, puis surveillez et ajustez-le pour l’adapter aux comportements de votre organisation et limiter la génération de fausses alertes. |
| Événements de verrouillage intelligent.| Moyen - si incident isolé<br>Élevé - si un certain nombre de comptes rencontrent le même modèle ou une adresse IP virtuelle.| Journal de connexions Azure AD| État = échec<br>-et-<br>Code d’erreur de connexion = 50053 – IdsLocked| Définissez un seuil de ligne de base, puis surveillez et ajustez-le pour l’adapter aux comportements de votre organisation et limiter la génération de fausses alertes. |
| Interruptions| Moyen - si incident isolé<br>Élevé - si un certain nombre de comptes rencontrent le même modèle ou une adresse IP virtuelle.| Journal de connexions Azure AD| 500121, échec de l’authentification lors d’une requête d’authentification forte. <br>-ou-<br>50097, l’authentification de l’appareil est requise ou 50074, une authentification forte est requise. <br>-ou-<br>50155, DeviceAuthenticationFailed<br>-ou-<br>50158, ExternalSecurityChallenge : la vérification de sécurité externe n’a pas abouti<br>-ou-<br>53003 et raison de l’échec = bloqué par l’autorité de certification| Surveiller et alerter sur les interruptions.<br>Définissez un seuil de ligne de base, puis surveillez et ajustez-le pour l’adapter aux comportements de votre organisation et limiter la génération de fausses alertes. |


Les éléments suivants sont répertoriés par ordre d’importance en fonction de l’impact et de la gravité des entrées.

| Éléments à analyser| Niveau de risque| Where| Filtre/Sous-filtre| Notes |
| - |- |- |- |- |
| Alertes de fraude MFA.| Élevé| Journal de connexions Azure AD| État = échec<br>-et-<br>Détails = MFA refusé<br>| Surveiller et alerter sur n’importe quelle entrée. |
| Échec des authentifications provenant de pays où vous n’opérez pas.| Moyenne| Journal de connexions Azure AD| Emplacement = \<unapproved location\>| Surveiller et alerter sur n’importe quelle entrée. |
| Échec des authentifications pour les protocoles ou protocoles hérités qui ne sont pas utilisés.| Moyenne| Journal de connexions Azure AD| État = échec<br>-et-<br>Application cliente = autres clients, POP, IMAP, MAPI, SMTP, ActiveSync| Surveiller et alerter sur n’importe quelle entrée. |
| Échecs bloqués par l’autorité de certification.| Moyenne| Journal de connexions Azure AD| Code d’erreur = 53003 <br>-et-<br>Raison de l’échec = bloqué par l’autorité de certification| Surveiller et alerter sur n’importe quelle entrée. |
| Amélioration des authentifications ayant échoué de tout type.| Moyenne| Journal de connexions Azure AD| La capture augmente les échecs dans tous les domaines. Par exemple, le nombre total d’échecs pour aujourd’hui est >10 % le même jour de la semaine précédente.| Si vous n’avez pas de seuil défini, surveillez et signalez si les échecs augmentent de 10 % ou plus. |
| L’authentification se produit aux heures et aux jours de la semaine où les pays n’effectuent pas d’opérations commerciales normales.| Faible| Journal de connexions Azure AD| Capturez l’authentification interactive se produisant en dehors des jours/heures de fonctionnement normaux. <br>État = réussite<br>-et-<br>Emplacement = \<location\><br>-et-<br>Jour\heure = \<not normal working hours\>| Surveiller et alerter sur n’importe quelle entrée. |
| Compte désactivé/bloqué pour les connexions| Faible| Journal de connexions Azure AD| État = échec<br>-et-<br>Code d’erreur = 50057, le compte d’utilisateur est désactivé.| Cela peut indiquer qu’un utilisateur tente d’accéder à un compte une fois qu’il a quitté une organisation. Même si le compte est bloqué, il est important d’enregistrer et de signaler cette activité. |


### <a name="monitoring-for-successful-unusual-sign-ins"></a>Surveillance des connexions inhabituelles réussies

 | Éléments à analyser| Niveau de risque| Where| Filtre/Sous-filtre| Notes |
| - |- |- |- |- |
| Authentifications de comptes privilégiés en dehors des contrôles attendus.| Élevé| Journal de connexions Azure AD| État = réussite<br>-et-<br>UserPricipalName = \<Admin account\><br>-et-<br>Emplacement = \<unapproved location\><br>-et-<br>Adresse IP = \<unapproved IP\><br>Informations sur l’appareil= \<unapproved Browser, Operating System\><br>| Surveiller et alerter en cas de réussite de l’authentification pour les comptes privilégiés en dehors des contrôles attendus. Trois contrôles communs sont répertoriés. |
| Lorsque seule l’authentification à facteur unique est requise.| Faible| Journal de connexions Azure AD| État = réussite<br>Exigence d’authentification = authentification à facteur unique| Surveillez cela périodiquement et assurez-vous que c’est le comportement attendu. |
| Détectez les comptes privilégiés non inscrits pour l’authentification multifacteur.| Élevé| API Azure Graph| Exécutez la requête IsMFARegistered = false pour les comptes d’administrateur. <br>[List credentialUserRegistrationDetails - Microsoft Graph version bêta | Microsoft Docs](/graph/api/reportroot-list-credentialuserregistrationdetails?view=graph-rest-beta&preserve-view=true&tabs=http)| Auditez et vérifiez si c’est intentionnel ou si c’est un oubli. |
| Authentifications réussies dans les pays où votre organisation n’opère pas.| Moyenne| Journal de connexions Azure AD| État = réussite<br>Emplacement = \<unapproved country\>| Surveillez et signalez toute entrée ne correspondant pas aux noms de ville que vous fournissez. |
| Authentification réussie, session bloquée par l’autorité de certification.| Moyenne| Journal de connexions Azure AD| État = réussite<br>-et-<br>code d’erreur = 53003 – raison de l’échec, bloqué par l’autorité de certification| Surveillez et examinez la réussite de l’authentification, mais la session est bloquée par l’autorité de certification. |
| Authentification réussie après la désactivation de l’authentification héritée.| Moyenne| Journal de connexions Azure AD| état = réussite <br>-et-<br>Application cliente = autres clients, POP, IMAP, MAPI, SMTP, ActiveSync| Si votre organisation a désactivé l’authentification héritée, surveillez et alertez lorsque l’authentification héritée a réussi. |


Nous vous recommandons de revoir périodiquement les authentifications aux applications à impact moyen sur l’activité (MBI) et à impact élevé sur l’activité (HBI) pour lesquelles seule une authentification à un facteur est requise. Pour chacune, vous souhaitez déterminer si l’authentification à un seul facteur était attendue ou non. De plus, l’examen pour une authentification réussie augmente ou se fait à des moments inattendus en fonction de l’emplacement. 

| Éléments à analyser| Niveau de risque| Where| Filtre/Sous-filtre| Notes |
| - | - |- |- |- |
| Authentifications de l’application MBI et HBI à l’aide de l’authentification à facteur unique.| Faible| Journal de connexions Azure AD| état = réussite<br>-et-<br>ID d’application = \<HBI app\> <br>-et-<br>Exigence d’authentification = authentification à facteur unique| Examinez et vérifiez si cette configuration est intentionnelle. |
| Authentifications à des jours et heures de la semaine ou de l’année où les pays n’effectuent pas d’opérations commerciales normales.| Faible| Journal de connexions Azure AD| Capturez l’authentification interactive se produisant en dehors des jours/heures de fonctionnement normaux. <br>État = réussite<br>Emplacement = \<location\><br>Date\heure = \<not normal working hours\>| Surveillez et signalez les authentifications à des jours et heures de la semaine ou de l’année où les pays n’effectuent pas d’opérations commerciales normales. |
| Augmentation mesurable des connexions réussies.| Faible| Journal de connexions Azure AD| La capture augmente le nombre d’authentifications réussies dans tous les domaines. Par exemple, le nombre total de réussites pour aujourd’hui est >10 % le même jour de la semaine précédente.| Si vous ne disposez pas d’un seuil défini, surveillez et signalez si les authentifications réussies augmentent de 10 % ou plus. |

## <a name="next-steps"></a>Étapes suivantes
Consultez les articles suivants sur les opérations de sécurité :

[Vue d’ensemble des opérations de sécurité Azure AD](security-operations-introduction.md)

[Opérations de sécurité pour les comptes d’utilisateur](security-operations-user-accounts.md)

[Opérations de sécurité pour les comptes privilégiés](security-operations-privileged-accounts.md)

[Opérations de sécurité pour Privileged Identity Management](security-operations-privileged-identity-management.md)

[Opérations de sécurité pour les applications](security-operations-applications.md)

[Opérations de sécurité pour les appareils](security-operations-devices.md)
 
[Opérations de sécurité pour l’infrastructure](security-operations-infrastructure.md)
