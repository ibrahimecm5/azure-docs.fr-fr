---
title: Opérations de sécurité Azure Active Directory pour les applications
description: Découvrez comment superviser les applications et déclencher des alertes en cas de menaces de sécurité.
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
ms.openlocfilehash: 1a139ffb47ad9f92cdb275191fc3eb983523c72a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131046329"
---
# <a name="azure-active-directory-security-operations-guide-for-applications"></a>Guide sur les opérations de sécurité Azure Active Directory pour les applications

Les applications fournissent une surface d’attaque pour les violations de sécurité et doivent donc être surveillées. Même si elles ne sont pas ciblées aussi souvent que les comptes d’utilisateurs, des violations peuvent se produire. Étant donné que les applications s’exécutent souvent sans intervention humaine, les attaques peuvent être plus difficiles à détecter.

Cet article fournit des conseils pour superviser les événements d’application et créer des alertes les concernant. Il est régulièrement mis à jour pour vous permettre de faire ce qui suit :

* Empêcher les applications malveillantes d’accéder aux données de manière non autorisée.

* Empêcher les applications existantes d’être compromises par des acteurs malveillants.

* Rassembler des insights qui vous permettront de créer et de configurer de nouvelles applications de manière plus sécurisée.

Si vous n’êtes pas familiarisé avec le fonctionnement des applications dans Azure Active Directory (Azure AD), consultez [Applications et principaux de service dans Azure AD](../develop/app-objects-and-service-principals.md).

> [!NOTE]
> Si vous n’avez pas encore lu le [Guide des opérations de sécurité Azure Active Directory](security-operations-introduction.md), nous vous conseillons de le faire maintenant.

## <a name="what-to-look-for"></a>Ce que vous devez surveiller

Lorsque vous supervisez les journaux des applications afin d’y détecter d’éventuels incidents de sécurité, passez en revue les éléments suivants pour différencier les activités normales des activités malveillantes. Les événements suivants peuvent indiquer des problèmes de sécurité. Chacun d’eux est abordé dans la suite de cet article.

* Tout changement qui se produit en dehors des processus et planifications habituels de l’entreprise.

* Modification des informations d’identification des applications

* Autorisations de l’application

   * Principal de service affecté à un rôle Azure AD ou à un rôle RBAC Azure.

   * Applications qui reçoivent des autorisations avec privilèges élevés.

   * Modifications d’Azure Key Vault.

   * Utilisateur final accordant son consentement à une application.

   * Blocage du consentement de l’utilisateur en raison du niveau de risque.

* Modifications de la configuration de l’application

   * L’URI a été modifié ou n’est pas standard.

   * Modifications apportées aux propriétaires d’applications.

   * Modification des URL de déconnexion.

## <a name="where-to-look"></a>Où regarder ?

Les fichiers journaux que vous pouvez utiliser pour l’investigation et la supervision sont les suivants :

* [Journaux d’audit Azure AD](../reports-monitoring/concept-audit-logs.md)

* [Journaux de connexion](../reports-monitoring/concept-all-sign-ins.md)

* [Journaux d’audit Microsoft 365](/microsoft-365/compliance/auditing-solutions-overview)

* [Journaux Azure Key Vault](../../key-vault/general/logging.md)

Sur le Portail Azure, vous pouvez afficher les journaux d’audit Azure AD et les télécharger sous forme de fichiers CSV (valeurs séparées par des virgules) ou JSON (JavaScript Object Notation). Le Portail Azure offre plusieurs méthodes d’intégration entre les journaux Azure AD et d’autres outils, ce qui permet une plus grande automatisation du monitoring et des alertes :

* **[Azure Sentinel](../../sentinel/overview.md)** – Permet une analytique de sécurité intelligente au niveau de l’entreprise en fournissant des fonctionnalités d’informations de sécurité et de gestion d’événements management (SIEM). 

* **[Azure Monitor](../../azure-monitor/overview.md)** – Permet de créer des alertes et supervisions automatisées de diverses conditions. Peut créer ou utiliser des classeurs pour combiner des données provenant de différentes sources.

* **[Azure Event Hubs](../../event-hubs/event-hubs-about.md) avec intégration SIEM**- [Les journaux Azure AD peuvent être intégrés à d’autres SIEM](../reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md) comme Splunk, ArcSight, QRadar et Sumo Logic via l’intégration Azure Event Hub Hub.

* **[Microsoft Cloud App Security (MCAS)](/cloud-app-security/what-is-cloud-app-security)**  : permet de découvrir et de gérer les applications, de gouverner toutes les applications et ressources, et de vérifier la conformité des applications cloud.

La plupart des éléments qui font l’objet d’une supervision et d’alertes sont déterminés par vos stratégies d’accès conditionnel. Vous pouvez utiliser le [classeur Rapports et insights sur l’accès conditionnel](../conditional-access/howto-conditional-access-insights-reporting.md) pour examiner les effets d’une ou de plusieurs stratégies d’accès conditionnel sur vos connexions, ainsi que les résultats des stratégies, y compris l’état de l’appareil. Ce classeur permet de voir un résumé de l’impact et d’identifier l’incidence sur une période donnée. Vous pouvez également vous en servir pour examiner les connexions d’un utilisateur spécifique. 

 Le reste de cet article comprend des recommandations concernant la supervision et les alertes, qui sont organisées par type de menace. Lorsqu’il existe des solutions prédéfinies spécifiques, nous en indiquons le lien ou donnons des exemples après le tableau. Sinon, vous pouvez créer des alertes à l’aide des outils précédents. 

## <a name="application-credentials"></a>Informations d’identification d’application

De nombreuses applications utilisent des informations d’identification pour s’authentifier auprès d’Azure AD. Les informations d’identification supplémentaires ajoutées en dehors des processus attendus peuvent être le résultat d’un acteur malveillant. Nous vous recommandons fortement d’utiliser des certificats X509 émis par des autorités approuvées ou des identités managées, plutôt que des secrets clients. Toutefois, si vous devez utiliser des secrets clients, suivez les bonnes pratiques pour sécuriser les applications. Remarque : Les mises à jour des applications et des principaux de service sont journalisées sous la forme de deux entrées dans le journal d’audit. 

* Supervisez les applications pour identifier celles dont les délais d’expiration d’informations d’identification sont particulièrement longs.

* Remplacez les informations d’identification à longue durée de vie par des informations d’identification à courte durée de vie. Vérifiez que les informations d’identification ne sont pas validées dans des dépôts de code et qu’elles sont stockées de manière sécurisée.


| Éléments à analyser| Niveau de risque| Where| Filtre/Sous-filtre| Notes |
| -|-|-|-|-|
| Ajout d’informations d’identification à des applications existantes| Élevé| Journaux d’audit Azure AD| Répertoire Service-Core, Category-ApplicationManagement <br>Activité : Mise à jour de la gestion des certificats d’application et des secrets<br>-et-<br>Activité : Mise à jour du principal de service ou de l’application| Déclenchez une alerte lorsque les informations d’identification sont :<li> Ajoutées en dehors des heures de travail ou des workflows habituels.<li> D’un type non utilisé dans votre environnement.<li> Ajoutées à un flux non SAML qui prend en charge le principal de service. |
| Informations d’identification dont la durée de vie est supérieure à celle autorisée par vos stratégies.| Moyenne| Microsoft Graph| État et date de fin des informations d’identification de la clé d’application<br>-et-<br>Informations d’identification de mot de passe d’application| Vous pouvez utiliser l’API Graph Microsoft pour rechercher la date de début et la date de fin des informations d’identification, et évaluer celles dont la durée de vie est plus longue que celle autorisée. Consultez le script PowerShell situé sous ce tableau. |

 Les fonctionnalités prédéfinies de supervision et d’alertes suivantes sont disponibles.

* Azure Sentinel – [Alerte lors de l’ajout de nouvelles informations d’identification d’application ou de principal de service](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/AuditLogs/NewAppOrServicePrincipalCredential.yaml) 

* Azure Monitor : [classeur Azure AD qui vous aide à évaluer les risques Solorigate - Microsoft Tech Community](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/azure-ad-workbook-to-help-you-assess-solorigate-risk/ba-p/2010718)

* MCAS – [Guide d’investigation des alertes concernant les anomalies détectées avec Cloud App Security](/cloud-app-security/investigate-anomaly-alerts)

* PowerShell – [Exemple de script PowerShell permettant de rechercher la durée de vie des informations d’identification](https://github.com/madansr7/appCredAge).

## <a name="application-permissions"></a>Autorisations de l’application

Tout comme les comptes Administrateur, les applications peuvent se voir attribuer des rôles privilégiés. Les applications peuvent se voir attribuer des rôles Azure AD, tels que celui d’administrateur général, ou des rôles RBAC Azure, tels que celui de propriétaire d’abonnement. Étant donné qu’elles peuvent s’exécuter sans qu’un utilisateur soit présent et en tant que service d’arrière-plan, soyez particulièrement attentif lorsqu’une application reçoit un rôle ou une autorisation à privilèges élevés. 

### <a name="service-principal-assigned-to-a-role"></a>Principal de service affecté à un rôle


| Éléments à analyser| Niveau de risque| Where| Filtre/Sous-filtre| Notes |
|-|-|-|-|-|
| Application affectée au rôle RBAC Azure ou au rôle Azure AD| Élevé à moyen| Journaux d’audit Azure AD| Type : Principal de service<br>Activité : « Ajouter un membre à un rôle » ou « Ajouter un membre éligible à un rôle »<br>-ou-<br>« Ajouter un membre inclus dans une étendue à un rôle ».| Pour les rôles avec privilèges élevés, comme le rôle Administrateur général, le risque est élevé. Pour les rôles qui ont des privilèges moins élevés, le risque est moyen. Déclenchez une alerte chaque fois qu’une application se voit attribuer un rôle Azure ou Azure AD en dehors des procédures habituelles de configuration ou de gestion des changements. |

### <a name="application-granted-highly-privileged-permissions"></a>Applications recevant des autorisations avec privilèges élevés

Les applications doivent également suivre le principe des privilèges minimum. Investiguez les autorisations de l’application pour vérifier qu’elles sont réellement nécessaires. Vous pouvez créer un [rapport sur les consentements accordés aux applications](https://aka.ms/getazureadpermissions) afin d’identifier plus facilement les applications existantes et mettre en évidence les autorisations privilégiées.

| Éléments à analyser|Niveau de risque|Where| Filtre/Sous-filtre| Notes|
|-|-|-|-|-|
| Application se voyant accorder des autorisations à privilèges élevés, telles que des autorisations «  *.All » (Directory.ReadWrite.All) ou des autorisations étendues (Mail.* )| Élevé |Journaux d’audit Azure AD| « Ajouter une attribution de rôle d’application à un principal de service » <br>-où-<br> Target(s) identifie une API avec des données sensibles (par exemple Microsoft Graph) <br>-et-<br>AppRole.Value identifie une autorisation d’application à privilèges élevés (rôle d’application).| Applications se voyant accorder des autorisations générales comme «  *.All » (Directory.ReadWrite.All) ou des autorisations étendues (Mail.* ) |
| Administrateur accordant des autorisations d’application (rôles d’application) ou des autorisations déléguées à privilèges élevés |Élevé| Portail Microsoft 365| « Ajouter une attribution de rôle d’application à un principal de service » <br>-où-<br>Target(s) identifie une API avec des données sensibles (par exemple Microsoft Graph)<br>« Ajout de l’octroi de permissions déléguées » <br>-où-<br>Target(s) identifie une API avec des données sensibles (par exemple Microsoft Graph) <br>-et-<br>DelegatedPermissionGrant.Scope comprend des autorisations à privilèges élevés.| Déclenchez une alerte quand un administrateur général, un administrateur d’application ou un administrateur d’application cloud accorde son consentement à une application. Recherchez en particulier les consentements accordés en dehors des activités et des procédures de modification habituelles. |
| L’application se voit accorder des autorisations pour Microsoft Graph, Exchange, SharePoint ou Azure AD. |Élevé| Journaux d’audit Azure AD| « Ajout de l’octroi de permissions déléguées » <br>-ou-<br>« Ajouter une attribution de rôle d’application à un principal de service » <br>-où-<br>Target(s) identifie une API avec des données sensibles (par exemple Microsoft Graph, Exchange Online, etc.)| Déclenchez une alerte comme à la ligne précédente. |
| Des autorisations d’application (rôles d’application) sont accordées pour d’autres API. |Moyenne| Journaux d’audit Azure AD| « Ajouter une attribution de rôle d’application à un principal de service » <br>-où-<br>Target(s) identifie toute autre API.| Déclenchez une alerte comme à la ligne précédente. |
| Des autorisations déléguées à privilèges élevés sont accordées pour le compte de tous les utilisateurs |Élevé| Journaux d’audit Azure AD| « Ajouter l’octroi d’autorisations déléguées », où Target(s) identifie une API avec des données sensibles (par exemple Microsoft Graph) <br> DelegatedPermissionGrant.Scope comprend des autorisations à privilèges élevés. <br>-et-<br>DelegatedPermissionGrant.ConsentType est « AllPrincipals ».| Déclenchez une alerte comme à la ligne précédente. |

Pour plus d’informations sur la supervision des autorisations d’application, consultez ce tutoriel : [Investiguer et corriger les applications OAuth à risque](/cloud-app-security/investigate-risky-oauth).

### <a name="azure-key-vault"></a>Azure Key Vault

Azure Key Vault peut être utilisé pour stocker les secrets de votre locataire. Nous vous recommandons de prêter une attention toute particulière aux modifications qui sont apportées à la configuration et aux activités Key Vault. 

| Éléments à analyser| Niveau de risque| Where| Filtre/Sous-filtre| Notes |
|-|-|-|-|-|
| Qui accède à vos coffres de clés, de quelle façon et à quel moment.| Moyenne| [Journaux Azure Key Vault](../../key-vault/general/logging.md?tabs=Vault)| Type de ressource : coffres de clés| Recherche <li> Tout accès à un coffre de clés en dehors des processus et des horaires habituels. <li> Toute modification apportée à la liste de contrôle d’accès Key Vault. |

Après avoir configuré Azure Key Vault, [activez la journalisation](../../key-vault/general/howto-logging.md?tabs=azure-cli) pour voir [quand et comment les utilisateurs accèdent à vos coffres de clés](../../key-vault/general/logging.md?tabs=Vault), puis [configurez des alertes](../../key-vault/general/alert.md) pour les coffres de clés afin d’avertir les utilisateurs et les listes de distribution par e-mail, par téléphone, par SMS ou à l’aide d’une notification [Event Grid](../../key-vault/general/event-grid-overview.md) si l’intégrité est impactée. En outre, le fait de configurer la [supervision](../../key-vault/general/alert.md) pour les insights Key Vault vous donnera un aperçu des demandes, des performances, des défaillances et de la latence associées à Key Vault. [Log Analytics](../../azure-monitor/logs/log-analytics-overview.md) comprend également des [exemples de requêtes](../../azure-monitor/logs/queries.md) pour Azure Key Vault auxquels vous pouvez accéder en sélectionnant votre coffre de clés, puis en sélectionnant « Journaux » sous « Supervision ».

### <a name="end-user-consent"></a>Consentement de l’utilisateur

| Éléments à analyser| Niveau de risque| Where| Filtre/Sous-filtre| Notes |
|-|-|-|-|-|
| Consentement de l’utilisateur pour une application| Faible| Journaux d’audit Azure AD| Activité : Consentement accordé à une application / ConsentContext.IsAdminConsent = false| Rechercher : <li>Des comptes à profil élevé ou à privilèges élevés.<li> Des applications demandant des autorisations à haut risque<li>Des applications avec des noms suspects, comme des noms génériques ou mal orthographiés. |


Le fait d’accorder son consentement à une application n’est pas en soi malveillant. Toutefois, vous devez investiguer tous les nouveaux consentements d’utilisateur afin d’y rechercher d’éventuelles applications suspectes. Vous pouvez [limiter les opérations de consentement de l’utilisateur](../../security/fundamentals/steps-secure-identity.md).

Pour plus d’informations sur les opérations de consentement, reportez-vous aux ressources suivantes :

* [Gestion du consentement pour les applications et évaluation des demandes de consentement dans Azure Active Directory](../manage-apps/manage-consent-requests.md)

* [Détecter et corriger les octrois de consentement illicites - Office 365](/microsoft-365/security/office-365-security/detect-and-remediate-illicit-consent-grants)

* [Guide opérationnel sur les réponses aux incidents - Investigation d’octroi de consentement d’application](/security/compass/incident-response-playbook-app-consent)

### <a name="end-user-stopped-due-to-risk-based-consent"></a>Consentement de l’utilisateur bloqué en raison d’un consentement risqué 

| Éléments à analyser| Niveau de risque| Where| Filtre/Sous-filtre| Notes |
|-|-|-|-|-|
| Consentement de l’utilisateur bloqué en raison d’un consentement risqué| Moyenne| Journaux d’audit Azure AD| Annuaire principal/ApplicationManagement/Consentement pour une application<br> Raison de l’échec = Microsoft.online.Security.userConsent<br>BlockedForRiskyAppsExceptions| Supervisez et analysez chaque fois qu’un consentement est bloqué à cause des risques. Rechercher :<li>Des comptes à profil élevé ou à privilèges élevés.<li> Des applications demandant des autorisations à haut risque<li>Des applications avec des noms suspects, comme des noms génériques ou mal orthographiés. |

## <a name="application-configuration-changes"></a>Modifications de la configuration de l’application

Supervisez les modifications apportées à la configuration d’une application, plus particulièrement les modifications apportées à la configuration de l’URI, de la propriété et de l’URL de déconnexion.

### <a name="dangling-uri-and-redirect-uri-changes"></a>Modification des URI non résolus et de redirection 

| Éléments à analyser| Niveau de risque| Where| Filtre/Sous-filtre| Notes |
|-|-|-|-|-|
| URI non résolu| Élevé| Journaux Azure AD et inscription d’application| Annuaire Service-Core, Category-ApplicationManagement<br>Activité : Mise à jour de l’application<br>Réussite : nom de propriété AppAddress| Recherchez des URI non résolus, par exemple, qui pointent vers un nom de domaine qui n’existe plus ou qui ne vous appartient pas explicitement. |
| Modification de la configuration des URI de redirection| Élevé| Journaux Azure AD| Annuaire Service-Core, Category-ApplicationManagement<br>Activité : Mise à jour de l’application<br>Réussite : nom de propriété AppAddress| Recherchez les URI qui n’utilisent pas HTTPS*, qui comportent des caractères génériques à la fin ou le domaine de l’URL, qui ne sont PAS uniques par rapport à l’application ou qui pointent vers un domaine que vous ne contrôlez pas. |

Déclenchez une alerte chaque fois que ces modifications sont détectées.

### <a name="appid-uri-added-modified-or-removed"></a>Ajout, modification ou suppression des URI AppID


| Éléments à analyser| Niveau de risque| Where| Filtre/Sous-filtre| Notes |
|-|-|-|-|-|
| Modifications apportées à l’URI AppID| Élevé| Journaux Azure AD| Annuaire Service-Core, Category-ApplicationManagement<br>Activité : Mise à jour<br>Application<br>Activité : Mise à jour du principal de service| Recherchez les modifications d’URI AppID, telles que l’ajout, la modification ou la suppression de l’URI. |


Déclenchez une alerte chaque fois que ces modifications sont détectées en dehors des procédures approuvées de gestion des changements.

### <a name="new-owner"></a>Nouveau propriétaire


| Éléments à analyser| Niveau de risque| Where| Filtre/Sous-filtre| Notes |
|-|-|-|-|-|
| Modifications apportées à la propriété des applications| Moyenne| Journaux Azure AD| Annuaire Service-Core, Category-ApplicationManagement<br>Activité : Ajouter un propriétaire à une application| Recherchez les ajouts d’utilisateurs en tant que propriétaires d’une application en dehors des activités habituelles de gestion des changements. |

### <a name="logout-url-modified-or-removed"></a>Modification ou suppression d’une URL de déconnexion

| Éléments à analyser| Niveau de risque| Where| Filtre/Sous-filtre| Notes |
|-|-|-|-|-|
| Modifications apportées à l’URL de déconnexion| Faible| Journaux Azure AD| Annuaire Service-Core, Category-ApplicationManagement<br>Activité : Mise à jour de l’application<br>-et-<br>Activité : Mise à jour du principal de service| Recherchez toute modification apportée à une URL de déconnexion. Les entrées vides ou les entrées correspondant à des emplacements inexistants empêchent un utilisateur de mettre fin à une session. |

## <a name="additional-resources"></a>Ressources supplémentaires

Vous trouverez ci-dessous des liens vers des ressources utiles :

* Github Azure AD Toolkit - [https://github.com/microsoft/AzureADToolkit](https://github.com/microsoft/AzureADToolkit)

* Vue d’ensemble et conseils pour la sécurité Azure Key Vault - [Vue d’ensemble de la sécurité Azure Key Vault](../../key-vault/general/security-features.md)

* Informations et outils sur les risques Solorgate – [classeur Azure AD qui vous aide à accéder aux risques Solorigate](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/azure-ad-workbook-to-help-you-assess-solorigate-risk/ba-p/2010718)

* Conseils pour la détection des attaques OAuth - [Ajout inhabituel d’informations d’identification à une application OAuth](/cloud-app-security/investigate-anomaly-alerts)

Informations sur la configuration de la supervision Azure AD pour les SIEM - [Outils partenaires avec intégration d’Azure Monitor](../..//azure-monitor/essentials/stream-monitoring-data-event-hubs.md)

 ## <a name="next-steps"></a>Étapes suivantes

Consultez les articles suivants sur les opérations de sécurité :

[Vue d’ensemble des opérations de sécurité Azure AD](security-operations-introduction.md)

[Opérations de sécurité pour les comptes d’utilisateur](security-operations-user-accounts.md)

[Opérations de sécurité pour les comptes privilégiés](security-operations-privileged-accounts.md)

[Opérations de sécurité pour Privileged Identity Management](security-operations-privileged-identity-management.md)

[Opérations de sécurité pour les applications](security-operations-applications.md)

[Opérations de sécurité pour les appareils](security-operations-devices.md)

 
[Opérations de sécurité pour l’infrastructure](security-operations-infrastructure.md)
