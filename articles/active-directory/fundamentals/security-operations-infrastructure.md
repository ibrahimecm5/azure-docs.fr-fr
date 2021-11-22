---
title: Opérations de sécurité Azure Active Directory pour l’infrastructure
description: Découvrez comment surveiller et alerter les composants d’infrastructure pour identifier les menaces de sécurité.
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
ms.openlocfilehash: c96362b522100d27757618e330f45514e4d442df
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132349240"
---
# <a name="security-operations-for-infrastructure"></a>Opérations de sécurité pour l’infrastructure

L’infrastructure a de nombreux composants dans lesquels des vulnérabilités peuvent se produire si elles ne sont pas correctement configurées. Dans le cadre de votre stratégie de surveillance et d’alerte pour les événements d’infrastructure, de surveillance et d’alerte dans les domaines suivants :

* Authentification et autorisation

* Composants d’authentification hybride, y compris les serveurs de Fédération

* Stratégies 

* Abonnements

La surveillance et la génération d’alertes pour les composants de votre infrastructure d’authentification sont critiques. Toute compromission peut entraîner une compromission complète de l’ensemble de l’environnement. De nombreuses entreprises qui utilisent Azure AD fonctionnent dans un environnement d’authentification hybride. Cela signifie que les composants Cloud et locaux doivent être inclus dans votre stratégie de surveillance et d’alerte. Le fait de disposer d’un environnement d’authentification hybride introduit également un autre vecteur d’attaque pour votre environnement.

Nous recommandons que tous les composants soient considérés comme des ressources de plan de contrôle/niveau 0, ainsi que les comptes utilisés pour les gérer. Reportez-vous à la section [Sécurisation des biens privilégiés](/security/compass/overview) (SPA) pour obtenir des conseils sur la conception et la mise en œuvre de votre environnement. Ce guide comprend des suggestions pour chacun des composants d’authentification hybride qui pourraient potentiellement être utilisés pour un locataire Azure AD.

La première étape pour pouvoir détecter les événements inattendus et les attaques potentielles consiste à établir une ligne de base. Pour tous les composants locaux énumérés dans cet article, consultez [Déploiement de l'accès privilégié](/security/compass/privileged-access-deployment), qui fait partie du guide sécurisation des ressources privilégiées (SPA).

## <a name="where-to-look"></a>Emplacement des fichiers

Les fichiers journaux que vous pouvez utiliser pour l’investigation et la supervision sont les suivants : 

* [Journaux d’audit Azure AD](../reports-monitoring/concept-audit-logs.md)

* [Journaux de connexion](../reports-monitoring/concept-all-sign-ins.md)

* [Journaux d’audit Microsoft 365](/microsoft-365/compliance/auditing-solutions-overview) 

* [Journaux Azure Key Vault](../../key-vault/general/logging.md?tabs=Vault)

Dans le portail Azure, vous pouvez afficher les journaux d’audit Azure AD et les télécharger sous forme de fichiers CSV ou JSON. Le Portail Azure offre plusieurs méthodes d’intégration entre les journaux Azure AD et d’autres outils, ce qui permet une plus grande automatisation du monitoring et des alertes :

* [Microsoft Sentinel](../../sentinel/overview.md) : permet une analytique de sécurité intelligente au niveau de l’entreprise en fournissant des fonctionnalités d’informations de sécurité et gestion d’événements (SIEM, Security Information and Event Management). 

* [Azure Monitor](../../azure-monitor/overview.md) : permet un monitoring et des alertes automatisés de divers états. Peut créer ou utiliser des classeurs pour combiner des données provenant de différentes sources.

* [Azure Event Hubs](../../event-hubs/event-hubs-about.md) avec intégration SIEM[ : permet d’intégrer les journaux Azure AD à d’autres systèmes SIEM](../reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md) comme Splunk, ArcSight, QRadar et Sumo Logic grâce à l’intégration Azure Event Hub.

* [Microsoft Defender for Cloud Apps](/cloud-app-security/what-is-cloud-app-security) : permet de découvrir et de gérer les applications, de gouverner toutes les applications et ressources, et de vérifier la conformité des applications cloud. 

Le reste de cet article comprend des recommandations concernant la supervision et les alertes, qui sont organisées par type de menace. Lorsqu’il existe des solutions prédéfinies spécifiques, vous trouverez des liens vers ceux-ci à la suite du tableau. Sinon, vous pouvez créer des alertes à l’aide des outils précédents.

## <a name="authentication-infrastructure"></a>Infrastructure d’authentification

Dans les environnements hybrides qui contiennent des comptes et des ressources basés sur le Cloud et locaux, l’infrastructure Active Directory est un élément clé de la pile d’authentification. La pile est également une cible pour les attaques, et doit être configurée pour maintenir un environnement sécurisé et doit être analysée correctement. Des exemples de types actuels d’attaques utilisées sur votre infrastructure d’authentification utilisent des techniques de pulvérisation de mot de passe et de Solorigate. Vous trouverez ci-dessous des liens vers des articles que nous recommandons :

* [Présentation de la sécurisation de l’accès privilégié](/security/compass/overview) : cet article fournit une vue d’ensemble des techniques actuelles à l’aide de techniques de confiance nulle pour créer et maintenir un accès privilégié sécurisé.

* [Activités de domaine surveillées par Microsoft Defender pour Identity](/defender-for-identity/monitored-activities) : cet article fournit une liste complète des activités pour surveiller et définir des alertes pour. 

* [Didacticiel sur les alertes de sécurité Microsoft Defender pour Identity](/defender-for-identity/understanding-security-alerts) : cet article fournit des conseils sur la création et l’implémentation d’une stratégie d’alerte de sécurité.

Vous trouverez ci-dessous des liens vers des articles spécifiques qui se concentrent sur la surveillance et l’alerte de votre infrastructure d’authentification :

* [Comprendre et utiliser les chemins de mouvement latéral avec Microsoft Defender pour Identity](/defender-for-identity/use-case-lateral-movement-path) : cet article décrit les techniques de détection que vous pouvez utiliser pour identifier les cas où des comptes non sensibles sont utilisés pour accéder à des comptes sensibles sur l’ensemble du réseau.

* [Utilisation des alertes de sécurité dans Microsoft Defender pour Identity](/defender-for-identity/working-with-suspicious-activities) : cet article décrit comment examiner et gérer les alertes une fois qu’elles sont journalisées. 

 Les éléments suivants sont spécifiques à Rechercher :

| Éléments à analyser| Niveau de risque| Where| Notes |
| - | - | - | - |
| Tendances de verrouillage extranet| Élevé| Azure AD Connect Health| Utilisez les informations de[l’analyse AD FS à l’aide de Azure AD Connect Health](../hybrid/how-to-connect-health-adfs.md) pour obtenir des outils et des techniques permettant de détecter les tendances de verrouillage extranet. |
| Connexions ayant échoué|Élevé | Connecter Health Portal| Exportez ou téléchargez le rapport Risky IP et suivez les conseils donnés dans le rapport [Risky IP (aperçu public)](../hybrid/how-to-connect-health-adfs-risky-ip.md) pour les prochaines étapes. |
| Conformité aux réglementations relatives à la confidentialité| Faible| Azure AD Connect Health| Configurez Azure AD Connect Health pour désactiver les collectes de données et la surveillance à l’aide de l’article [Confidentialité de l’utilisateur et Azure AD Connect Health](../hybrid/reference-connect-health-user-privacy.md). |
| Attaque par force brute potentielle sur LDAP| Moyenne| Microsoft Defender pour Identity| Utilisez le capteur pour détecter les attaques par force brute potentielles contre LDAP. |
| Reconnaissance d’énumération de compte| Moyenne| Microsoft Defender pour Identity| Utilisez le capteur pour faciliter la reconnaissance de l’énumération des comptes. |
| Corrélation générale entre Azure AD et Azure AD FS|Moyenne | Microsoft Defender pour Identity| Utilisez les fonctionnalités pour mettre en corrélation les activités entre vos environnements Azure AD et Azure AD FS. |


 

### <a name="pass-through-authentication-monitoring"></a>Surveillance d’authentification directe 

L'authentification directe d'Azure Active Directory (Azure AD) permet de connecter les utilisateurs en validant leurs mots de passe directement par rapport à Active Directory sur site. 

Les éléments suivants sont spécifiques à Rechercher :

| Éléments à analyser| Niveau de risque| Where| Filtre/Sous-filtre| Notes |
| - | - | - | - | - |
| Erreurs d’authentification directe Azure AD|Moyenne | Application and ‎Service Logs\Microsoft\A‎zureAdConnec‎t\Authenticatio‎nAgent\Adm| AADSTS80001 – Impossible de se connecter à Active Directory| Assurez-vous que les serveurs des agents sont membres de la même forêt Active Directory que les utilisateurs dont les mots de passe doivent être validés, et qu’ils peuvent se connecter à Active Directory. |
| Erreurs d’authentification directe Azure AD| Moyenne| Application and ‎Service Logs\Microsoft\A‎zureAdConnec‎t\Authenticatio‎nAgent\Adm| AADSTS8002 - Délai d’attente dépassé lors de la connexion à Active Directory| Vérifiez qu’Active Directory est disponible et répond aux demandes des agents. |
| Erreurs d’authentification directe Azure AD|Moyenne | Application and ‎Service Logs\Microsoft\A‎zureAdConnec‎t\Authenticatio‎nAgent\Adm| AADSTS80004 - Le nom d’utilisateur envoyé à l’agent n’était pas valide| Vérifiez que l’utilisateur tente de se connecter avec le nom d’utilisateur correct. |
| Erreurs d’authentification directe Azure AD|Moyenne | Application and ‎Service Logs\Microsoft\A‎zureAdConnec‎t\Authenticatio‎nAgent\Adm| AADSTS80005 - La validation a rencontré une WebException imprévisible| Erreur temporaire. Relancez la requête. Si l’erreur se reproduit, contactez le Support Microsoft. |
| Erreurs d’authentification directe Azure AD| Moyenne| Application and ‎Service Logs\Microsoft\A‎zureAdConnec‎t\Authenticatio‎nAgent\Adm| AADSTS80007 - Une erreur s’est produite lors de la communication avec Active Directory| Consultez les journaux d’activité de l’agent pour plus d’informations, et vérifiez qu’Active Directory fonctionne comme prévu. |
| Erreurs d’authentification directe Azure AD|Élevé | API de fonction Win32 LogonUserA| Événements de connexion 4624(s) : un compte a été correctement connecté<br>-corrélation avec –<br>4625(F) : Échec de connexion d’un compte| Utilisez avec les noms d’utilisateur suspects sur le contrôleur de domaine qui authentifie les demandes. Aide sur [fonction LogonUserA (winbase.h)](/windows/win32/api/winbase/nf-winbase-logonusera) |
| Erreurs d’authentification directe Azure AD| Moyenne| Script PowerShell du contrôleur de domaine| Consultez le tableau suivant de la requête. | Utilisez les informations de [Azure AD Connect : Dépannage de l'authentification directe](../hybrid/tshoot-connect-pass-through-authentication.md) pour obtenir des conseils supplémentaires. |

```Kusto

<QueryList>

<Query Id="0" Path="Security">

<Select Path="Security">*[EventData[Data[@Name='ProcessName'] and (Data='C:\Program Files\Microsoft Azure AD Connect Authentication Agent\AzureADConnectAuthenticationAgentService.exe')]]</Select>

</Query>

</QueryList>
```

### <a name="appproxy-connector"></a>Connecteur AppProxy

Cet article décrit comment Azure AD et le proxy d’application Azure AD offrent aux utilisateurs distants une expérience d’authentification unique (SSO). Les utilisateurs se connectent de manière sécurisée aux applications locales sans réseau privé virtuel (VPN), ni serveurs à double hébergement et règles de pare-feu. Si votre serveur de connecteur de proxy d’application Azure AD est compromis, les attaquants peuvent modifier l’expérience de l’authentification unique ou modifier l’accès aux applications publiées. 

Pour configurer la surveillance pour le proxy d’application, consultez [résoudre les problèmes de proxy d’application et de messages d’erreur](../app-proxy/application-proxy-troubleshoot.md). Le fichier de données qui consigne les informations est disponible dans applications et services Logs\Microsoft\AadApplicationProxy\Connector\Admin. Pour obtenir un guide de référence complet sur l’activité d’audit, consultez [référence de l’activité d’audit Azure AD](../reports-monitoring/reference-audit-activities.md). Éléments spécifiques à vérifier :

| Éléments à analyser| Niveau de risque| Where| Filtre/Sous-filtre| Notes |
| - | - | - | - | - |
| Erreurs Kerberos| Moyenne | Outils divers| Moyenne | Aide sur l’authentification Kerberos sous erreurs Kerberos sur la [Résolution des problèmes de proxy d’application et des messages d’erreur](../app-proxy/application-proxy-troubleshoot.md). |
| Problèmes de sécurité DC| Élevé| Journaux d’audit de sécurité DC| ID d’événement 4742(S) : un compte d’ordinateur a été modifié<br>-et-<br>Indicateur - Approuvé pour la délégation<br>-ou-<br>Indicateur - Approuvé pour authentifier la délégation| Examinez tout changement d’indicateur. |
| Attaques de type « Pass-the-ticket »| Élevé| | | Suivez les conseils dans :<li>[Reconnaissance de principal de sécurité (LDAP) (ID externe 2038)](/defender-for-identity/reconnaissance-alerts)<li>[Tutoriel : Alertes indiquant des informations d’identification compromises](/defender-for-identity/compromised-credentials-alerts)<li> [Présentation et utilisation des chemins de mouvement latéral avec Microsoft Defender pour Identity](/defender-for-identity/use-case-lateral-movement-path)<li> [Présentation des profils d’entité](/defender-for-identity/entity-profiles) |


### <a name="legacy-authentication-settings"></a>Paramètres d’authentification héritée

Pour que l’authentification multifacteur (MFA) soit efficace, vous devez également bloquer l’authentification héritée. Vous devez ensuite surveiller votre environnement et alerter sur toute utilisation de l’authentification héritée. En effet, les protocoles d'authentification traditionnels tels que POP, SMTP, IMAP et MAPI ne peuvent pas appliquer l’authentification multifacteur. Ces protocoles sont donc des points d’entrée préférés pour les attaquants de votre organisation. Pour plus d’informations sur les outils que vous pouvez utiliser pour bloquer l’authentification héritée, consultez [Nouveaux outils pour bloquer l’authentification héritée dans votre organisation](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/new-tools-to-block-legacy-authentication-in-your-organization/ba-p/1225302). 

L’authentification héritée est capturée dans le journal des connexions Azure AD dans le cadre des détails de l’événement. Vous pouvez utiliser le classeur Azure Monitor pour faciliter l’identification de l’utilisation de l’authentification héritée. Pour plus d'informations, consultez la section [Connexions à l'aide de l'authentification traditionnelle](../reports-monitoring/howto-use-azure-monitor-workbooks.md), qui fait partie de la section [Comment utiliser des classeurs Azure Monitor pour créer des rapports Azure Active Directory](../reports-monitoring/howto-use-azure-monitor-workbooks.md). Vous pouvez également utiliser le classeur protocoles non sécurisés pour Microsoft Sentinel. Pour plus d’informations, consultez [Guide d’implémentation des classeurs des protocoles non sécurisés Microsoft Sentinel](https://techcommunity.microsoft.com/t5/azure-sentinel/azure-sentinel-insecure-protocols-workbook-implementation-guide/ba-p/1197564). Les activités spécifiques à surveiller sont les suivantes :

| Éléments à analyser| Niveau de risque| Where| Filtre/Sous-filtre| Notes |
| - | - | - | - | - |
| Authentifications héritées|Élevé | Journal de connexions Azure AD| ClientApp : POP<br>ClientApp : IMAP<br>ClientApp : MAPI<br>ClientApp : SMTP<br>ClientApp : ActiveSync, accédez à EXO<br>Autres Clients = SharePoint et EWS| Dans les environnements de domaine fédérés, les authentifications ayant échoué ne sont pas enregistrées et n’apparaissent donc pas dans le journal. |


## <a name="azure-ad-connect"></a>Azure AD Connect

Azure AD Connect fournit un emplacement centralisé qui permet la synchronisation des comptes et des attributs entre votre environnement local et Azure AD basé sur le cloud. L’outil Microsoft Azure AD Connect a été conçu pour vous permettre d’atteindre et de remplir vos objectifs en matière d’identité hybride. Elle fournit les fonctionnalités suivantes :

* [Synchronisation de hachage de mot de passe](../hybrid/whatis-phs.md) : méthode d’authentification qui synchronise un hachage du mot de passe AD local d’un utilisateur avec Azure AD.

* [Synchronisation](../hybrid/how-to-connect-sync-whatis.md) : ce composant est chargé de créer des utilisateurs, des groupes et d’autres objets, et également de s’assurer que les informations d’identité relatives aux utilisateurs et aux groupes dans votre environnement local correspondent à celles qui se trouvent dans le cloud. Cette synchronisation inclut également des hachages de mot de passe.

* [Analyse du fonctionnement](../hybrid/whatis-azure-ad-connect.md) : Azure AD Connect Health peut assurer une supervision robuste et offrir un emplacement central dans le Portail Azure pour la visualisation de cette activité.

La synchronisation de l’identité entre votre environnement local et votre environnement Cloud introduit une nouvelle surface d’attaque pour votre environnement local et basé sur le Cloud. Nous recommandons les actions suivantes :

* Vous traitez vos serveurs principaux et intermédiaires Azure AD Connect en tant que systèmes de niveau 0 dans votre plan de contrôle. 

* Vous suivez un ensemble standard de stratégies qui régissent chaque type de compte et son utilisation dans votre environnement.

*  Vous installez Azure AD Connect et Connect Health. Celles-ci fournissent principalement des données opérationnelles pour l’environnement. 

La journalisation des opérations de Azure AD Connect se produit de différentes manières :

* L’assistant Azure AD Connect enregistre les données dans \ProgramData\AADConnect. Chaque fois que l'assistant est invoqué, un fichier de trace horodaté est créé. Le journal des traces peut être importé dans Sentinel ou d'<sup data-htmlnode="">autres</sup> outils Siem (Security information and Event Management) pour l’analyse.

* Certaines opérations lancent un script PowerShell pour capturer les informations de journalisation. Pour collecter ces données, vous devez vérifier que la journalisation des blocs de script est activée.

### <a name="monitoring-configuration-changes"></a>Supervision des modifications de configuration

Azure AD utilise Microsoft SQL Server moteur de données ou SQL pour stocker des informations de configuration Azure AD Connect. Par conséquent, la surveillance et l’audit des fichiers journaux associés à la configuration doivent être inclus dans votre stratégie de surveillance et d’audit. En particulier, incluez les tables suivantes dans votre stratégie de surveillance et d’alerte.

| Éléments à analyser| Where| Notes |
| - | - | - |
| mms_management_agent| Enregistrements d’audit du service SQL| Voir [Enregistrements SQL Server Audit](/sql/relational-databases/security/auditing/sql-server-audit-records) |
| mms_partition| Enregistrements d’audit du service SQL| Voir [Enregistrements SQL Server Audit](/sql/relational-databases/security/auditing/sql-server-audit-records) |
| mms_run_profile| Enregistrements d’audit du service SQL| Voir [Enregistrements SQL Server Audit](/sql/relational-databases/security/auditing/sql-server-audit-records) |
| mms_server_configuration| Enregistrements d’audit du service SQL| Voir [Enregistrements SQL Server Audit](/sql/relational-databases/security/auditing/sql-server-audit-records) |
| mms_synchronization_rule| Enregistrements d’audit du service SQL| Voir [Enregistrements SQL Server Audit](/sql/relational-databases/security/auditing/sql-server-audit-records) |


Pour plus d’informations sur la façon dont les informations de configuration doivent être surveillées, consultez :

* Pour le serveur SQL, voir [Enregistrements SQL Server Audit](/sql/relational-databases/security/auditing/sql-server-audit-records).

* Pour Microsoft Sentinel, consultez [Se connecter aux serveurs Windows pour collecter des événements de sécurité](/sql/relational-databases/security/auditing/sql-server-audit-records). 

* Pour plus d’informations sur la configuration et l’utilisation de Azure AD Connect, voir [Qu’est-ce que Azure AD Connect ?](../hybrid/whatis-azure-ad-connect.md)

### <a name="monitoring-and-troubleshooting-synchronization"></a>Supervision et résolution des problèmes

 L’une des fonctions de Azure AD Connect consiste à synchroniser la synchronisation de hachage entre le mot de passe local et le Azure AD d’un utilisateur. Si les mots de passe ne sont pas synchronisés comme prévu, la synchronisation peut affecter un sous-ensemble d'utilisateurs ou tous les utilisateurs. Utilisez les éléments suivants pour vous aider à vérifier le bon fonctionnement ou à résoudre les problèmes :

* Informations pour vérifier et résoudre les problèmes de synchronisation de hachage, consultez [Résoudre les problèmes de synchronisation de hachage de mot de passe avec Azure AD Connect sync](../hybrid/tshoot-connect-password-hash-synchronization.md). 

* Modifications apportées aux espaces de connecteur, consultez [Résoudre les problèmes liés aux objets et attributs Azure AD Connect](/troubleshoot/azure/active-directory/troubleshoot-aad-connect-objects-attributes). 

**Ressources importantes sur la surveillance**

| Éléments à analyser | Ressources |
| - | - |
| Validation de la synchronisation de hachage|Voir [Résoudre les problèmes de synchronisation du hachage de mot de passe avec Azure AD Connect Sync](../hybrid/tshoot-connect-password-hash-synchronization.md) |
 Modifications apportées aux espaces de connecteur|Voir [Résolution des problèmes de bout en bout des objets et attributs Azure AD Connect](/troubleshoot/azure/active-directory/troubleshoot-aad-connect-objects-attributes) |
| Modifications apportées aux règles que vous avez configurées| Plus précisément, surveillez les modifications de filtrage, les modifications de domaine et d’UO, les modifications d’attributs et les modifications basées sur les groupes. |
| Modifications de SQL et de MSDE | Modifications apportées aux paramètres de journalisation et à l’ajout de fonctions personnalisées |

**Surveillez les aspects suivants** : 

| Éléments à analyser| Niveau de risque| Where| Filtre/Sous-filtre| Notes |
| - | - | - | - | - |
| Modifications du planificateur|Élevé | PowerShell| Set-ADSyncScheduler| Rechercher les modifications à planifier |
| Modifications apportées aux tâches planifiées| Élevé | Journaux d’audit Azure AD| Activité = 4699(S) : une tâche planifiée a été supprimée<br>-ou-<br>Activité = 4701(S) : une tâche planifiée a été désactivée<br>-ou-<br>Activité = 4701(S) : une tâche planifiée a été mise à jour| Surveiller tous les journaux d’activité |



* Pour plus d’informations sur la journalisation des opérations de script PowerShell, consultez Activation de la [journalisation de blocs de script](/powershell/module/microsoft.powershell.core/about/about_logging_windows), qui fait partie de la documentation de référence de PowerShell.

* Pour plus d’informations sur la configuration de la journalisation PowerShell pour l’analyse par Splunk, consultez [Obtenir des données dans Splunk User Behavior Analytics](https://docs.splunk.com/Documentation/UBA/5.0.4.1/GetDataIn/AddPowerShell).

### <a name="monitoring-seamless-single-sign-on"></a>Surveillance de l’authentification unique transparente

L’authentification unique transparente (Seamless SSO) Azure Active Directory (Azure AD) connecte automatiquement les utilisateurs lorsque leurs ordinateurs d’entreprise sont connectés au réseau de l’entreprise. Seamless SSO offre à vos utilisateurs un accès facilité à vos applications dans le cloud sans nécessiter de composants sur site supplémentaires. SSO utilise l’authentification directe et les fonctionnalités de synchronisation de hachage de mot de passe fournies par Azure AD Connect.

La surveillance de l’authentification unique et de l’activité Kerberos peut vous aider à détecter les modèles d’attaques générales de vol d’informations d’identification. Surveillez les informations suivantes :

| Éléments à analyser| Niveau de risque| Where| Filtre/Sous-filtre| Notes |
| - | - | - | - | - |
| Erreurs associées à l’authentification unique et aux échecs de validation Kerberos|Moyenne | Journal de connexions Azure AD| | Liste des codes d'erreur de l'authentification unique à [l'authentification unique](../hybrid/tshoot-connect-sso.md). |
| Requête pour dépanner les erreurs|Moyenne | PowerShell| Consultez le tableau suivant de la requête. Vérifiez dans chaque forêt l’authentification unique activée.| Vérifiez dans chaque forêt l’authentification unique activée. |
| Événements liés à Kerberos|Élevé | Surveillance de Microsoft Defender pour Identity| | Passez en revue les conseils disponibles sur [Microsoft Defender pour identifier les chemins de mouvement latéral d’identité (LMPs)](/defender-for-identity/use-case-lateral-movement-path) |

```kusto
<QueryList>

<Query Id="0" Path="Security">

<Select Path="Security">*[EventData[Data[@Name='ServiceName'] and (Data='AZUREADSSOACC$')]]</Select>

</Query>

</QueryList>
```
## <a name="password-protection-policies"></a>Stratégies de protection par mot de passe

Si vous déployez la protection de mot de passe Azure AD, la surveillance et la génération de rapports sont des tâches essentielles. Ces liens détaillés vous aident à comprendre plusieurs techniques de supervision, notamment en vous expliquant où chaque service journalise les informations et en vous montrant comment créer des rapports sur l’utilisation de la protection par mot de passe Azure AD. 

L'agent de contrôleur de domaine (DC) et les services proxy consignent tous deux les messages du journal des événements. Toutes les cmdlets PowerShell décrites ci-dessous sont uniquement disponibles sur le serveur proxy (voir le module AzureADPasswordProtection PowerShell). Le logiciel de l'agent DC n'installe pas de module PowerShell.

Des informations détaillées sur la planification et la mise en œuvre de la protection des mots de passe locale sont disponibles dans [Planifier et déployer la protection des mots de passe Azure Active Directory locale](../authentication/howto-password-ban-bad-on-premises-deploy.md). Pour plus d’informations sur l’analyse, consultez [Supervision de la protection par mot de passe Azure AD en local](../authentication/howto-password-ban-bad-on-premises-monitor.md). Sur chaque contrôleur de domaine, le logiciel du service d’agent DC écrit les résultats de chacune des opérations de validation de mot de passe (et un autre état) dans le journal d’événements local suivant :

* \Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Admin

* \Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Operational

* \Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Trace

Le journal d’administration de l’agent DC est la principale source d’informations pour superviser le comportement du logiciel. Par défaut, le journal des traces est désactivé et doit être activé pour que les données soient enregistrées. Pour résoudre les problèmes de proxy d’application et les messages d’erreur, des informations détaillées sont disponibles dans [Résoudre les problèmes du proxy d’application Azure Active Directory](../app-proxy/application-proxy-troubleshoot.md). Les informations relatives à ces événements sont consignées :

* Applications and Services Logs\Microsoft\AadApplicationProxy\Connector\Admin

* Journal d'audit Azure AD, proxy d’application de catégorie

Des informations de référence complètes sur les activités d’audit de Azure AD sont disponibles dans la [Référence d’activité d’audit Azure Active Directory (Azure AD)](../reports-monitoring/reference-audit-activities.md). 

## <a name="next-steps"></a>Étapes suivantes


Consultez ces articles supplémentaires concernant les opérations de sécurité :

[Vue d’ensemble des opérations de sécurité Azure AD](security-operations-introduction.md)

[Opérations de sécurité pour les comptes d’utilisateur](security-operations-user-accounts.md)

[Opérations de sécurité pour les comptes privilégiés](security-operations-privileged-accounts.md)

[Opérations de sécurité pour Privileged Identity Management](security-operations-privileged-identity-management.md)

[Opérations de sécurité pour les applications](security-operations-applications.md)

[Opérations de sécurité pour les appareils](security-operations-devices.md)
 
[Opérations de sécurité pour l’infrastructure](security-operations-infrastructure.md)


 

  
‎
