---
title: Rechercher votre connecteur de données Azure Sentinel | Microsoft Docs
description: Apprenez-en davantage sur les étapes de configuration spécifiques pour les connecteurs de données Azure Sentinel.
services: sentinel
documentationcenter: na
author: batamig
ms.service: azure-sentinel
ms.topic: reference
ms.date: 10/11/2021
ms.author: bagol
ms.custom: ignite-fall-2021
ms.openlocfilehash: 9dddb8118cc0bd328e19d73f05d2dd0eb01ebd4f
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131452753"
---
# <a name="find-your-azure-sentinel-data-connector"></a>Rechercher votre connecteur de données Azure Sentinel

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Cet article explique comment déployer des connecteurs de données dans Azure Sentinel, en répertoriant tous les connecteurs de données prêts à l’emploi et pris en charge, ainsi que des liens vers des procédures de déploiement génériques et des étapes supplémentaires nécessaires pour des connecteurs spécifiques.

> [!TIP]
> Certains connecteurs de données ne sont déployés que via des solutions. Pour plus d’informations, consultez le [catalogue de solutions Azure Sentinel](sentinel-solutions-catalog.md). Vous trouverez également d’autres connecteurs de données créés par la communauté dans le [Référentiel GitHub Azure Sentinel](https://github.com/Azure/Azure-Sentinel/tree/master/DataConnectors).
>

## <a name="how-to-use-this-guide"></a>Comment utiliser ce guide

1. Tout d’abord, recherchez et sélectionnez le connecteur correspondant à votre produit, service ou appareil dans le menu d’en-têtes à droite.

    La première information que vous verrez pour chaque connecteur est sa **méthode d’ingestion des données**. La méthode qui s’affiche est un lien vers l’une des procédures de déploiement génériques suivantes, qui contiennent la plupart des informations dont vous avez besoin pour connecter vos sources de données à Azure Sentinel :

    | Méthode d’ingestion des données | Article lié avec des instructions |
    | --- | --- |
    | **Intégration de service à service Azure** | [Connecter aux services Azure, Windows, Microsoft et Amazon](connect-azure-windows-microsoft-services.md) |
    | **Common Event format (CEF) sur Syslog** | [Recevoir des journaux au format CEF à partir de votre appareil ou de votre appliance dans Azure Sentinel](connect-common-event-format.md) |
    | **API du Collecteur de données Azure Sentinel** | [Connecter votre source de données à l’API du Collecteur de données Azure Sentinel pour l’ingestion des données](connect-rest-api-template.md) |
    | **Azure Functions et l’API REST** | [Utiliser Azure Functions pour connecter Azure Sentinel à votre source de données](connect-azure-functions-template.md) |
    | **Syslog** | [Collecter des données de sources Linux à l’aide de Syslog](connect-syslog.md) |
    | **Journaux d’activité personnalisés** | [Collecter des données dans des formats de journaux personnalisés vers Azure Sentinel avec l’agent Log Analytics](connect-custom-logs.md) |
    |

    > [!NOTE]
    > La méthode d’ingestion des données **Intégration de service à service Azure** est liée à trois sections différentes de son article, en fonction du type de connecteur. La section de chaque connecteur ci-dessous spécifie la section de cet article à laquelle elle est liée.

1. Lorsque vous déployez un connecteur spécifique, choisissez l’article approprié lié à sa **méthode d’ingestion des données**, puis utilisez les informations et instructions supplémentaires dans la section correspondante ci-dessous pour compléter les informations contenues dans cet article.

> [!TIP]
> - De nombreux connecteurs de données peuvent également être déployés dans le cadre d’une [solution Azure Sentinel](sentinel-solutions.md), ainsi que des règles d’analyse, des classeurs et des playbooks associés. Pour plus d’informations, consultez le [catalogue de solutions Azure Sentinel](sentinel-solutions-catalog.md).
>
>
> - D’autres connecteurs de données sont fournis par la communauté Azure Sentinel et se trouvent dans la Place de marché Azure. La documentation relative aux connecteurs de données de la communauté relève de la responsabilité de l’organisation qui a créé le connecteur.
>
>
> - Si vous avez une source de données qui n’est pas répertoriée ou n’est pas actuellement prise en charge, vous pouvez également créer votre propre connecteur personnalisé. Pour plus d’informations, consultez [Ressources pour la création de connecteurs Azure Sentinel personnalisés](create-custom-connector.md).
>

> [!IMPORTANT]
> Notez que les connecteurs de données Azure Sentinel sont actuellement en **préversion**. Les [Conditions d’utilisation supplémentaires des préversions Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluent des conditions légales supplémentaires qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou pas encore disponibles dans la version en disponibilité générale.

## <a name="agari-phishing-defense-and-brand-protection-preview"></a>Agari Phishing Defense and Brand Protection (préversion)

| Attribut du connecteur | Description|
| --- | --- |
| **Méthode d’ingestion des données** | [**Azure Functions et l’API REST**](connect-azure-functions-template.md) <br><br>**Avant le déploiement** : [Activez l’API Security (facultatif)](#enable-the-security-graph-api-optional). <br>**Après le déploiement** : [Attribuez les autorisations nécessaires à votre application de fonction](#assign-necessary-permissions-to-your-function-app)|
| **Table(s) Log Analytics** | agari_bpalerts_log_CL<br>agari_apdtc_log_CL<br>agari_apdpolicy_log_CL |
| **Code d’application de fonction Azure** | https://aka.ms/sentinel-agari-functionapp |
| **Informations d'identification de l’API** | <li>ID client<li>Clé secrète client<li>(Facultatif : ID de locataire Graph, ID de client Graph, clé secrète client Graph) |
| **Documentation/<br>instructions d’installation du fournisseur** | <li>[Démarrage rapide](https://developers.agari.com/agari-platform/docs/quick-start)<li>[Site des développeurs Agari](https://developers.agari.com/agari-platform) |
| **Instructions de déploiement du connecteur** | <li>[Déploiement en un clic](connect-azure-functions-template.md?tabs=ARM) avec un modèle Azure Resource Manager (ARM)<li>[Déploiement manuel](connect-azure-functions-template.md?tabs=MPS) |
| **Paramètres d’application** | <li>clientID<li>clientSecret<li>workspaceID<li>workspaceKey<li>enableBrandProtectionAPI (true/false)<li>enablePhishingResponseAPI (true/false)<li>enablePhishingDefenseAPI (true/false)<li>resGroup (entrer le groupe de ressources)<li>functionName<li>subId (entrer l’ID d’abonnement)<li>enableSecurityGraphSharing (true/false ; voir ci-dessous)<br>Obligatoire si enableSecurityGraphSharing est défini sur true (voir ci-dessous) :<li>GraphTenantId<li>GraphClientId<li>GraphClientSecret<li>logAnalyticsUri (facultatif) |
| **Pris en charge par** | [Agari](https://support.agari.com/hc/en-us/articles/360000645632-How-to-access-Agari-Support) |
| | |

### <a name="enable-the-security-graph-api-optional"></a>Activer l’API Security Graph (facultatif)

> [!IMPORTANT]
> Si vous effectuez cette étape, faites-le avant de déployer votre connecteur de données.
>
L’application de fonction Agari permet de partager des informations sur les menaces avec Azure Sentinel via l’API Security Graph. Pour utiliser cette fonctionnalité, vous devez activer le [connecteur Sentinel Threat Intelligence Platforms](./connect-threat-intelligence-tip.md) et [inscrire une application](/graph/auth-register-app-v2) dans Azure Active Directory.

À l’issue de ce processus, vous disposerez de trois informations à utiliser lors du [déploiement de l’application de fonction](connect-azure-functions-template.md) : l’**ID de locataire Graph**, l’**ID de client Graph** et la **Clé secrète client Graph** (voir les *Paramètres d'application* dans le tableau ci-dessus).

### <a name="assign-necessary-permissions-to-your-function-app"></a>Attribuer les autorisations nécessaires à votre application de fonction

Le connecteur Agari utilise une variable d’environnement pour stocker les horodateurs d’accès aux journaux. Des autorisations doivent être attribuées à l’identité affectée par le système pour que l’application puisse écrire dans cette variable.

1. Sur le Portail Azure, accédez à **Application de fonction**.
1. Dans la page **Application de fonction**, sélectionnez votre application de fonction dans la liste, puis **Identité** sous **Paramètres** dans le menu de navigation de l’application de fonction.
1. Dans l’onglet **Affectée par le système**, définissez **État** sur **Activé**.
1. Sélectionnez **Enregistrer** : un bouton **Attributions de rôle Azure** s’affiche. Sélectionnez-le.
1. Sur l’écran **Attributions de rôle Azure**, sélectionnez **Ajouter une attribution de rôle**. Définissez la **Portée** sur **Abonnement**, sélectionnez votre abonnement dans la liste déroulante **Abonnement**, puis définissez **Rôle** sur **Propriétaire des données App Configuration**.
1. Sélectionnez **Enregistrer**.


## <a name="ai-analyst-aia-by-darktrace-preview"></a>AI Analyst (AIA) par Darktrace (préversion)

| Attribut du connecteur | Description |
| --- | --- |
| **Méthode d’ingestion des données** | **[Common Event format (CEF)](connect-common-event-format.md) sur Syslog** <br><br>[Configurer le transfert de journaux CEF pour AI Analyst](#configure-cef-log-forwarding-for-ai-analyst) |
| **Table(s) Log Analytics** | CommonSecurityLog |
| **Pris en charge par** | [Darktrace](https://customerportal.darktrace.com/) |
| | |

### <a name="configure-cef-log-forwarding-for-ai-analyst"></a>Configurer le transfert de journaux CEF pour AI Analyst

Configurez Darktrace pour transférer les messages Syslog au format CEF à votre espace de travail Azure par le biais de l’agent Log Analytics.

1. Dans le Darktrace Threat Visualizer, accédez à la page **Configuration du système** dans le menu principal sous **Admin**.
1. Dans le menu de gauche, sélectionnez **Modules** et choisissez **Azure Sentinel** parmi les **intégrations de workflow** disponibles.
1. Une fenêtre de configuration s’ouvre. Recherchez **Azure Sentinel Syslog CEF** et sélectionnez **Nouveau** pour afficher les paramètres de configuration s’ils ne sont pas déjà exposés.
1. Dans le champ **Configuration du serveur**, entrez l’emplacement du redirecteur de journal. Vous pouvez également modifier le port de communication si vous le souhaitez. Assurez-vous que le port sélectionné est défini sur 514 et qu’il est autorisé par les pare-feu intermédiaires.
1. Configurez des seuils d’alerte, des décalages de temps ou des paramètres supplémentaires si nécessaire.
1. Passez en revue toutes les options de configuration supplémentaires que vous pouvez souhaiter activer pour modifier la syntaxe Syslog.
1. Activez **Envoyer des alertes** et enregistrez vos modifications.

## <a name="ai-vectra-detect-preview"></a>AI Vectra Detect (préversion)

| Attribut du connecteur | Description |
| --- | --- |
| **Méthode d’ingestion des données** | **[Common Event format (CEF)](connect-common-event-format.md) sur Syslog** <br><br>[Configurer le transfert de journaux CEF pour AI Vectra Detect](#configure-cef-log-forwarding-for-ai-vectra-detect)|
| **Table(s) Log Analytics** | CommonSecurityLog |
| **Pris en charge par** | [Vectra AI](https://www.vectra.ai/support) |
| | |

### <a name="configure-cef-log-forwarding-for-ai-vectra-detect"></a>Configurer le transfert de journaux CEF pour AI Vectra Detect

Configurez l’agent Vectra (X Series) pour transférer les messages Syslog au format CEF à votre espace de travail Azure Sentinel par le biais de l’agent Log Analytics.

À partir de l’interface Vectra, accédez à Paramètres > Notifications, puis choisissez Modifier la configuration Syslog. Suivez les instructions ci-dessous pour configurer la connexion :

- Ajoutez une nouvelle destination (le nom d’hôte du redirecteur de journal)
- Définissez le port sur **514**
- Définissez le protocole sur **UDP**
- Définissez le format sur **CEF**
- Définissez les types de journaux (sélectionnez tous les types de journaux disponibles)
- Sélectionnez **Enregistrer**.

Vous pouvez sélectionner le bouton **Test** pour forcer l’envoi de certains événements de test au redirecteur de journal.

Pour plus d’informations, reportez-vous au Guide du Syslog Cognito Detect qui peut être téléchargé à partir de la page des ressources dans l’interface utilisateur de Detect.

## <a name="akamai-security-events-preview"></a>Akamai Security Events (préversion)

| Attribut du connecteur | Description |
| --- | --- |
| **Méthode d’ingestion des données** | **[Common Event Format (CEF)](connect-common-event-format.md) sur Syslog**, avec un analyseur de fonction Kusto |
| **Table(s) Log Analytics** | CommonSecurityLog |
| **Alias de fonction Kusto :** | AkamaiSIEMEvent |
| **URL de fonction Kusto :** | https://aka.ms/sentinel-akamaisecurityevents-parser |
| **Documentation/<br>instructions d’installation du fournisseur** | [Configurer l’intégration de SIEM (Security Information and Event Management)](https://developer.akamai.com/tools/integrations/siem)<br>[Configurer un connecteur CEF](https://developer.akamai.com/tools/integrations/siem/siem-cef-connector). |
| **Pris en charge par** | [Akamai](https://www.akamai.com/us/en/support/) |
| | |

## <a name="alcide-kaudit"></a>Alcide kAudit

| Attribut du connecteur | Description |
| --- | --- |
| **Méthode d’ingestion des données** | [**API du Collecteur de données Azure Sentinel**](connect-rest-api-template.md) |
| **Table(s) Log Analytics** | alcide_kaudit_activity_1_CL - Journaux d’activité Alcide kAudit<br>alcide_kaudit_detections_1_CL - Détections Alcide kAudit<br>alcide_kaudit_selections_count_1_CL - Nombres d’activités Alcide kAudit<br>alcide_kaudit_selections_details_1_CL - Détails des activités Alcide kAudit |
| **Documentation/<br>instructions d’installation du fournisseur** | [Guide d’installation d’Alcide kAudit](https://awesomeopensource.com/project/alcideio/kaudit?categoryPage=29#before-installing-alcide-kaudit) |
| **Pris en charge par** | [Alcide](https://www.alcide.io/company/contact-us/) |
| | |

## <a name="alsid-for-active-directory"></a>Alsid for Active Directory

| Attribut du connecteur | Description |
| --- | --- |
| **Méthode d’ingestion des données** | [**Agent Log Analytics : journaux personnalisés**](connect-custom-logs.md) <br><br>[Configuration supplémentaire pour Alsid](#extra-configuration-for-alsid)|
| **Table(s) Log Analytics** | AlsidForADLog_CL |
| **Alias de fonction Kusto :** | afad_parser |
| **URL de fonction Kusto :** | https://aka.ms/sentinel-alsidforad-parser |
| **Pris en charge par** | [Alsid](https://www.alsid.com/contact-us/) |
| | |

### <a name="extra-configuration-for-alsid"></a>Configuration supplémentaire pour Alsid

1. **Configurer le serveur Syslog**

    Tout d’abord, vous avez besoin d’un serveur **Syslog Linux** auquel Alsid pour AD enverra les journaux. Généralement, vous pouvez exécuter **rsyslog** sur **Ubuntu**. 

    Vous pouvez ensuite configurer ce serveur comme vous le souhaitez, mais nous vous recommandons de pouvoir émettre les journaux AFAD dans un fichier distinct. Vous pouvez également utiliser un [modèle de démarrage rapide](https://azure.microsoft.com/resources/templates/alsid-syslog-proxy/) qui déploie le serveur Syslog et l’agent Microsoft pour vous. Si vous utilisez le modèle, vous pouvez ignorer les [instructions d’installation de l’agent](connect-custom-logs.md#install-the-log-analytics-agent).

1. **Configurer Alsid pour envoyer les journaux à votre serveur Syslog**

    Sur votre portail **Alsid pour AD**, accédez à **Système**, **Configuration**, puis **Syslog**. À partir de là, vous pouvez créer une alerte Syslog destinée à votre serveur Syslog.

    Après avoir créé une alerte Syslog, vérifiez que les journaux sont correctement collectés sur votre serveur dans un fichier séparé. Par exemple, pour vérifier vos journaux, vous pouvez utiliser le bouton *Tester la configuration* dans la configuration d’alerte Syslog dans AFAD. Si vous avez utilisé le modèle de démarrage rapide, le serveur Syslog écoute par défaut le port 514 dans UDP et 1514 dans TCP, sans TLS.

## <a name="amazon-web-services---cloudtrail"></a>Amazon Web Services - CloudTrail

| Attribut du connecteur | Description |
| --- | --- |
| **Méthode d’ingestion des données** | **Intégration de service à service Azure : <br>[Connecter AWS CloudTrail à Azure Sentinel](connect-aws.md)** (Article du premier connecteur) |
| **Table(s) Log Analytics** | AWSCloudTrail |
| **Pris en charge par** | Microsoft |
| | |

## <a name="apache-http-server"></a>Apache HTTP Server

| Attribut du connecteur | Description |
| --- | --- |
| **Méthode d’ingestion des données** | [**Agent Log Analytics : journaux personnalisés**](connect-custom-logs.md) |
| **Table(s) Log Analytics** | ApacheHTTPServer_CL |
| **Alias de fonction Kusto :** | ApacheHTTPServer |
| **URL de fonction Kusto :** | https://aka.ms/sentinel-apachehttpserver-parser |
| **Exemple de fichier journal personnalisé :** | access.log ou error.log |
| | |

## <a name="apache-tomcat"></a>Apache Tomcat

| Attribut du connecteur | Description |
| --- | --- |
| **Méthode d’ingestion des données** | [**Agent Log Analytics : journaux personnalisés**](connect-custom-logs.md) |
| **Table(s) Log Analytics** | Tomcat_CL |
| **Alias de fonction Kusto :** | TomcatEvent |
| **URL de fonction Kusto :** | https://aka.ms/sentinel-ApacheTomcat-parser |
| **Exemple de fichier journal personnalisé :** | access.log ou error.log |
| | |

## <a name="aruba-clearpass-preview"></a>Aruba ClearPass (préversion)

| Attribut du connecteur | Description |
| --- | --- |
| **Méthode d’ingestion des données** | **[Common Event Format (CEF)](connect-common-event-format.md) sur Syslog**, avec un analyseur de fonction Kusto |
| **Table(s) Log Analytics** | CommonSecurityLog |
| **Alias de fonction Kusto :** | ArubaClearPass |
| **URL de fonction Kusto :** | https://aka.ms/sentinel-arubaclearpass-parser |
| **Documentation/<br>instructions d’installation du fournisseur** | Suivez les instructions d’Aruba pour [configurer ClearPass](https://www.arubanetworks.com/techdocs/ClearPass/6.7/PolicyManager/Content/CPPM_UserGuide/Admin/syslogExportFilters_add_syslog_filter_general.htm). |
| **Pris en charge par** | Microsoft |
| | |

## <a name="atlassian-confluence-audit-preview"></a>Atlassian Confluence Audit (préversion)

| Attribut du connecteur | Description |
| --- | --- |
| **Méthode d’ingestion des données** | [**Azure Functions et l’API REST**](connect-azure-functions-template.md) |
| **Table(s) Log Analytics** | Confluence_Audit_CL |
| **Code d’application de fonction Azure** | https://aka.ms/sentinel-confluenceauditapi-functionapp |
| **Informations d'identification de l’API** | <li>ConfluenceAccessToken<li>ConfluenceUsername<li>ConfluenceHomeSiteName |
| **Documentation/<br>instructions d’installation du fournisseur** | <li>[Documentation de l’API](https://developer.atlassian.com/cloud/confluence/rest/api-group-audit/)<li>[Exigences et instructions pour l’obtention des informations d’identification](https://developer.atlassian.com/cloud/confluence/rest/intro/#auth)<li>[Afficher le journal d’audit](https://support.atlassian.com/confluence-cloud/docs/view-the-audit-log/) |
| **Instructions de déploiement du connecteur** | <li>[Déploiement en un clic](connect-azure-functions-template.md?tabs=ARM) avec un modèle Azure Resource Manager (ARM)<li>[Déploiement manuel](connect-azure-functions-template.md?tabs=MPY) |
| **Alias de fonction Kusto** | ConfluenceAudit |
| **Instructions de configuration de l’URL de fonction Kusto/<br>analyseur** | https://aka.ms/sentinel-confluenceauditapi-parser |
| **Paramètres d’application** | <li>ConfluenceUsername<li>ConfluenceAccessToken<li>ConfluenceHomeSiteName<li>WorkspaceID<li>WorkspaceKey<li>logAnalyticsUri (facultatif) |
| **Pris en charge par** | Microsoft |
| | |

## <a name="atlassian-jira-audit-preview"></a>Atlassian Jira Audit (préversion)

| Attribut du connecteur | Description |
| --- | --- |
| **Méthode d’ingestion des données** | [**Azure Functions et l’API REST**](connect-azure-functions-template.md) |
| **Table(s) Log Analytics** | Jira_Audit_CL |
| **Code d’application de fonction Azure** | https://aka.ms/sentinel-jiraauditapi-functionapp |
| **Informations d'identification de l’API** | <li>JiraAccessToken<li>JiraUsername<li>JiraHomeSiteName |
| **Documentation/<br>instructions d’installation du fournisseur** | <li>[Documentation de l’API : enregistrements d’audit](https://developer.atlassian.com/cloud/jira/platform/rest/v3/api-group-audit-records/)<li>[Exigences et instructions pour l’obtention des informations d’identification](https://developer.atlassian.com/cloud/jira/platform/rest/v3/intro/#authentication) |
| **Instructions de déploiement du connecteur** | <li>[Déploiement en un clic](connect-azure-functions-template.md?tabs=ARM) avec un modèle Azure Resource Manager (ARM)<li>[Déploiement manuel](connect-azure-functions-template.md?tabs=MPY) |
| **Alias de fonction Kusto** | JiraAudit |
| **Instructions de configuration de l’URL de fonction Kusto/<br>analyseur** | https://aka.ms/sentinel-jiraauditapi-parser |
| **Paramètres d’application** | <li>JiraUsername<li>JiraAccessToken<li>JiraHomeSiteName<li>WorkspaceID<li>WorkspaceKey<li>logAnalyticsUri (facultatif) |
| **Pris en charge par** | Microsoft |
| | |

## <a name="azure-active-directory"></a>Azure Active Directory

| Attribut du connecteur | Description |
| --- | --- |
| **Méthode d’ingestion des données** | **Intégration de service à service Azure : <br>[Connecter des données Azure Active Directory à Azure Sentinel](connect-azure-active-directory.md)** (Article du premier connecteur) |
| **Licences requises/<br>informations de coût** | <li>Licence Azure Active Directory P1 ou P2 pour les journaux de connexion<li>Toute licence Azure AD (Gratuit/O365/P1/P2) pour les autres types de journaux<br>D’autres frais d'opérateur peuvent s'appliquer |
| **Table(s) Log Analytics** | SigninLogs<br>AuditLogs<br>AADNonInteractiveUserSignInLogs<br>AADServicePrincipalSignInLogs<br>AADManagedIdentitySignInLogs<br>AADProvisioningLogs<br>ADFSSignInLogs |
| **Pris en charge par** | Microsoft |
| | |

## <a name="azure-active-directory-identity-protection"></a>Azure Active Directory Identity Protection

| Attribut du connecteur | Description |
| --- | --- |
| **Méthode d’ingestion des données** | **Intégration de service à service Azure: <br>[connexions basées sur l’API](connect-azure-windows-microsoft-services.md#api-based-connections)** |
| **Licences requises/<br>informations de coût** | [Abonnement Azure AD Premium P2](https://azure.microsoft.com/pricing/details/active-directory/)<br>D’autres frais d'opérateur peuvent s'appliquer |
| **Table(s) Log Analytics** | SecurityAlert |
| **Pris en charge par** | Microsoft |
| | |

## <a name="azure-activity"></a>Activité Azure

| Attribut du connecteur | Description |
| --- | --- |
| **Méthode d’ingestion des données** | **Intégration de service à service Azure : <br>[connexions basées sur les paramètres de diagnostic, gérées par Azure Policy](connect-azure-windows-microsoft-services.md?tabs=AP#diagnostic-settings-based-connections)**<br><br>[Mettre à niveau vers le nouveau connecteur Azure Activity](#upgrade-to-the-new-azure-activity-connector) |
| **Table(s) Log Analytics** | AzureActivity |
| **Pris en charge par** | Microsoft |
| | |

### <a name="upgrade-to-the-new-azure-activity-connector"></a>Mettre à niveau vers le nouveau connecteur Azure Activity

#### <a name="data-structure-changes"></a>Modifications de la structure de données

Ce connecteur a récemment modifié son mécanisme principal pour la collecte des événements du journal d’activité. Il utilise désormais le pipeline des **paramètres de diagnostic**. Si vous utilisez encore l’ancienne méthode pour ce connecteur, nous vous encourageons *fortement à passer* à la nouvelle version, qui offre une meilleure fonctionnalité et une plus grande cohérence avec les journaux des ressources. Voir les instructions ci-dessous.

La méthode **Paramètres de diagnostic** envoie les mêmes données que l’ancienne méthode envoyait à partir du service de journal des activités, bien que certaines [modifications aient été apportées à la structure](../azure-monitor/essentials/activity-log.md#data-structure-changes) de la table **AzureActivity**.

Voici quelques-unes des améliorations clés résultant de la migration vers le pipeline de paramètres de diagnostic :
- Latence d’ingestion améliorée (ingestion des événements dans les 2-3 minutes à compter de l’occurrence au lieu de 15-20 minutes).
- Amélioration de la fiabilité.
- Performances améliorées.
- Prise en charge de toutes les catégories d’événements journalisés par le service de journal d’activité (le mécanisme hérité ne prend en charge qu’un sous-ensemble, par exemple, aucune prise en charge des événements Service Health).
- Gestion à l’échelle avec Azure Policy.

Consultez la [Documentation d’Azure Monitor](../azure-monitor/logs/data-platform-logs.md) pour un traitement plus approfondi du [Journal d’activité d’Azure](../azure-monitor/essentials/activity-log.md) et du [Pipeline de paramètres de diagnostic](../azure-monitor/essentials/diagnostic-settings.md).

#### <a name="disconnect-from-old-pipeline"></a>Se déconnecter de l’ancien pipeline

Avant de configurer le nouveau connecteur de Azure Activity, vous devez déconnecter les abonnements existants de la méthode héritée.

1. Dans le menu de navigation d’Azure Sentinel, sélectionnez **Connecteurs de données**. Dans la liste des connecteurs, sélectionnez **Activité Azure**, puis le bouton **Ouvrir la page du connecteur** en bas à droite.

1. Sous l’onglet **Instructions**, dans la section **Configuration**, à l’étape 1, examinez la liste de vos abonnements existants qui sont connectés à l’ancienne méthode (afin de savoir lesquels ajouter à la nouvelle), et déconnectez-les tous en une seule fois en cliquant sur le bouton **Déconnecter tout** ci-dessous.

1. Continuez la configuration du nouveau connecteur en suivant les [instructions associées dans le tableau ci-dessus](connect-azure-windows-microsoft-services.md#diagnostic-settings-based-connections).

## <a name="azure-ddos-protection"></a>Protection DDoS dans Azure

| Attribut du connecteur | Description |
| --- | --- |
| **Méthode d’ingestion des données** | **Intégration de service à service Azure : <br>[connexions basées sur les paramètres de diagnostic](connect-azure-windows-microsoft-services.md?tabs=SA#diagnostic-settings-based-connections)** |
| **Licences requises/<br>informations de coût** | <li>Vous devez avoir configuré un [plan de protection Azure DDoS Standard](../ddos-protection/manage-ddos-protection.md#create-a-ddos-protection-plan).<li>Vous devez avoir configuré un [réseau virtuel sur lequel Azure DDoS Standard est activé](../ddos-protection/manage-ddos-protection.md#enable-ddos-protection-for-a-new-virtual-network)<br>D’autres frais d'opérateur peuvent s'appliquer |
| **Table(s) Log Analytics** | AzureDiagnostics |
| **Diagnostics recommandés** | DDoSProtectionNotifications<br>DDoSMitigationFlowLogs<br>DDoSMitigationReports |
| **Pris en charge par** | Microsoft |
| | |

## <a name="azure-defender"></a>Azure Defender

| Attribut du connecteur | Description |
| --- | --- |
| **Méthode d’ingestion des données** | **Intégration de service à service Azure : <br>[Connecter des alertes Azure Defender à partir d’Azure Security Center](connect-azure-security-center.md)** (Article du premier connecteur) |
| **Table(s) Log Analytics** | SecurityAlert |
| **Pris en charge par** | Microsoft |
| | |

## <a name="azure-defender-for-iot"></a>Azure Defender pour IoT

| Attribut du connecteur | Description |
| --- | --- |
| **Méthode d’ingestion des données** | **Intégration de service à service Azure: <br>[connexions basées sur l’API](connect-azure-windows-microsoft-services.md#api-based-connections)** |
| **Table(s) Log Analytics** | SecurityAlert |
| **Pris en charge par** | Microsoft |
| | |

## <a name="azure-firewall"></a>Pare-feu Azure

| Attribut du connecteur | Description |
| --- | --- |
| **Méthode d’ingestion des données** | **Intégration de service à service Azure : <br>[connexions basées sur les paramètres de diagnostic](connect-azure-windows-microsoft-services.md?tabs=SA#diagnostic-settings-based-connections)** |
| **Table(s) Log Analytics** | AzureDiagnostics |
| **Diagnostics recommandés** | AzureFirewallApplicationRule<br>AzureFirewallNetworkRule<br>AzureFirewallDnsProxy |
| **Pris en charge par** | Microsoft |
| | |

## <a name="azure-information-protection"></a>Azure Information Protection

| Attribut du connecteur | Description |
| --- | --- |
| **Méthode d’ingestion des données** | [**Intégration de service à service Azure**](connect-azure-windows-microsoft-services.md) |
| **Table(s) Log Analytics** | InformationProtectionLogs_CL |
| **Pris en charge par** | Microsoft |
| | |

Pour plus d’informations, consultez la [documentation d’Azure Information Protection](/azure/information-protection/reports-aip#how-to-modify-the-reports-and-create-custom-queries).

## <a name="azure-key-vault"></a>Azure Key Vault

| Attribut du connecteur | Description |
| --- | --- |
| **Méthode d’ingestion des données** | **Intégration de service à service Azure : <br>[connexions basées sur les paramètres de diagnostic, gérées par Azure Policy](connect-azure-windows-microsoft-services.md?tabs=AP#diagnostic-settings-based-connections)** |
| **Table(s) Log Analytics** | KeyVaultData |
| **Pris en charge par** | Microsoft |
| | |

## <a name="azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS)

| Attribut du connecteur | Description |
| --- | --- |
| **Méthode d’ingestion des données** | **Intégration de service à service Azure : <br>[connexions basées sur les paramètres de diagnostic, gérées par Azure Policy](connect-azure-windows-microsoft-services.md?tabs=AP#diagnostic-settings-based-connections)** |
| **Table(s) Log Analytics** | kube-apiserver<br>kube-audit<br>kube-audit-admin<br>kube-controller-manager<br>kube-scheduler<br>cluster-autoscaler<br>guard |
| **Pris en charge par** | Microsoft |
| | |

## <a name="azure-sql-databases"></a>Bases de données SQL Azure

| Attribut du connecteur | Description |
| --- | --- |
| **Méthode d’ingestion des données** | **Intégration de service à service Azure : <br>[connexions basées sur les paramètres de diagnostic, gérées par Azure Policy](connect-azure-windows-microsoft-services.md?tabs=AP#diagnostic-settings-based-connections)** <br><br>Également disponible dans [Azure SQL et Azure Sentinel pour les solutions PaaS SQL](sentinel-solutions-catalog.md#azure)|
| **Table(s) Log Analytics** | SQLSecurityAuditEvents<br>SQLInsights<br>AutomaticTuning<br>QueryStoreWaitStatistics<br>Errors<br>DatabaseWaitStatistics<br>Délais d'attente<br>Blocs<br>Blocages<br>De base<br>InstanceAndAppAdvanced<br>WorkloadManagement<br>DevOpsOperationsAudit |
| **Pris en charge par** | Microsoft |
| | |

## <a name="azure-storage-account"></a>Compte Stockage Azure

| Attribut du connecteur | Description |
| --- | --- |
| **Méthode d’ingestion des données** | **Intégration de service à service Azure : <br>[connexions basées sur les paramètres de diagnostic](connect-azure-windows-microsoft-services.md?tabs=SA#diagnostic-settings-based-connections)**<br><br>[Remarques sur la configuration des paramètres de diagnostic du compte de stockage](#notes-about-storage-account-diagnostic-settings-configuration) |
| **Table(s) Log Analytics** | StorageBlobLogs<br>StorageQueueLogs<br>StorageTableLogs<br>StorageFileLogs |
| **Diagnostics recommandés** | **Ressource de compte**<li>Transaction<br>**Ressources d’objets blob/file d’attente/table/fichier**<br><li>StorageRead<li>StorageWrite<li>StorageDelete<li>Transaction |
| **Pris en charge par** | Microsoft |
| | |

### <a name="notes-about-storage-account-diagnostic-settings-configuration"></a>Remarques sur la configuration des paramètres de diagnostic du compte de stockage

La ressource de compte de stockage (parent) comprend d’autres ressources (enfants) pour chaque type de stockage : fichiers, tables, files d’attente et objets blob.

Lors de la configuration des diagnostics pour un compte de stockage, vous devez sélectionner et configurer à son tour les éléments suivants :
- La ressource de compte parent, en exportant la métrique de **Transaction**.
- Chacune des ressources de type de stockage enfant, en exportant tous les journaux et métriques (voir le tableau ci-dessus).

Vous ne verrez que les types de stockage pour lesquels vous avez réellement défini des ressources.

## <a name="azure-web-application-firewall-waf"></a>Pare-feu d’applications web (WAF) Azure

| Attribut du connecteur | Description |
| --- | --- |
| **Méthode d’ingestion des données** | **Intégration de service à service Azure : <br>[connexions basées sur les paramètres de diagnostic](connect-azure-windows-microsoft-services.md?tabs=SA#diagnostic-settings-based-connections)** |
| **Table(s) Log Analytics** | AzureDiagnostics |
| **Diagnostics recommandés** | **Application Gateway**<br><li>ApplicationGatewayAccessLog<li>ApplicationGatewayFirewallLog<br>**Front Door**<li>FrontdoorAccessLog<li>FrontdoorWebApplicationFirewallLog<br>**Stratégie WAF de CDN**<li>WebApplicationFirewallLogs |
| **Pris en charge par** | Microsoft |
| | |


## <a name="barracuda-cloudgen-firewall"></a>Barracuda CloudGen Firewall

| Attribut du connecteur | Description |
| --- | --- |
| **Méthode d’ingestion des données** | [**Syslog**](connect-syslog.md) |
| **Table(s) Log Analytics** | syslog |
| **Alias de fonction Kusto :** | CGFWFirewallActivity |
| **URL de fonction Kusto :** | https://aka.ms/sentinel-barracudacloudfirewall-function |
| **Documentation/<br>instructions d’installation du fournisseur** | https://aka.ms/sentinel-barracudacloudfirewall-connector |
| **Pris en charge par** | [Barracuda](https://www.barracuda.com/support) |
| | |

## <a name="barracuda-waf"></a>WAF Barracuda

| Attribut du connecteur | Description |
| --- | --- |
| **Méthode d’ingestion des données** | [**Agent Log Analytics : journaux personnalisés**](connect-custom-logs.md) |
| **Table(s) Log Analytics** | syslog |
| **Documentation/<br>instructions d’installation du fournisseur** | https://aka.ms/asi-barracuda-connector |
| **Pris en charge par** | [Barracuda](https://www.barracuda.com/support) |
| | |

## <a name="better-mobile-threat-defense-mtd-preview"></a>BETTER Mobile Threat Defense (MTD) (préversion)

| Attribut du connecteur | Description |
| --- | --- |
| **Méthode d’ingestion des données** | [**API du Collecteur de données Azure Sentinel**](connect-rest-api-template.md) |
| **Table(s) Log Analytics** | BetterMTDDeviceLog_CL<br>BetterMTDIncidentLog_CL<br>BetterMTDAppLog_CL<br>BetterMTDNetflowLog_CL |
| **Documentation/<br>instructions d’installation du fournisseur** | [Documentation de BETTER MTD](https://mtd-docs.bmobi.net/integrations/azure-sentinel/setup-integration)<br><br>Configuration de la stratégie de menace, qui définit les incidents signalés à Azure Sentinel :<br><ol><li>Dans la **console de Better MTD**, sélectionnez **Stratégies** dans la barre latérale.<li>Sélectionnez le bouton **Modifier** de la stratégie que vous utilisez.<li>Pour chaque type d’Incident que vous souhaitez consigner, accédez au champ **Envoyer vers les intégrations** et sélectionnez **Sentinel**. |
| **Pris en charge par** | [Better Mobile](mailto:support@better.mobi) |
| | |


## <a name="beyond-security-besecure"></a>Beyond Security beSECURE

| Attribut du connecteur | Description |
| --- | --- |
| **Méthode d’ingestion des données** | [**API du Collecteur de données Azure Sentinel**](connect-rest-api-template.md) |
| **Table(s) Log Analytics** | beSECURE_ScanResults_CL<br>beSECURE_ScanEvents_CL<br>beSECURE_Audit_CL |
| **Documentation/<br>instructions d’installation du fournisseur** | Accédez au menu **Intégration** :<br><ol><li>Sélectionnez l’option de menu **Plus**.<li>Sélectionnez **Serveur**<li>Sélectionnez **Intégration**<li>Activer Azure Sentinel<li>Collez les valeurs **ID d’espace de travail** et **Clé primaire** dans la configuration de beSECURE.<li>Sélectionnez **Modifier**. |
| **Pris en charge par** | [Beyond Security](https://beyondsecurity.freshdesk.com/support/home) |
| | |


## <a name="blackberry-cylanceprotect-preview"></a>BlackBerry CylancePROTECT (préversion)

| Attribut du connecteur | Description |
| --- | --- |
| **Méthode d’ingestion des données** | [**Syslog**](connect-syslog.md) |
| **Table(s) Log Analytics** | syslog |
| **Alias de fonction Kusto :** | CylancePROTECT |
| **URL de fonction Kusto :** | https://aka.ms/sentinel-cylanceprotect-parser |
| **Documentation/<br>instructions d’installation du fournisseur** | [Guide du Syslog Cylance](https://docs.blackberry.com/content/dam/docs-blackberry-com/release-pdfs/en/cylance-products/syslog-guides/Cylance%20Syslog%20Guide%20v2.0%20rev12.pdf) |
| **Pris en charge par** | Microsoft |
| | |

## <a name="broadcom-symantec-data-loss-prevention-dlp-preview"></a>Broadcom Symantec Data Loss Prevention (DLP) (préversion)

| Attribut du connecteur | Description |
| --- | --- |
| **Méthode d’ingestion des données** | **[Common Event Format (CEF)](connect-common-event-format.md) sur Syslog**, avec un analyseur de fonction Kusto |
| **Table(s) Log Analytics** | CommonSecurityLog |
| **Alias de fonction Kusto :** | SymantecDLP |
| **URL de fonction Kusto :** | https://aka.ms/sentinel-symantecdlp-parser |
| **Documentation/<br>instructions d’installation du fournisseur** | [Configuration du journal sur une action de serveur Syslog](https://help.symantec.com/cs/DLP15.7/DLP/v27591174_v133697641/Configuring-the-Log-to-a-Syslog-Server-action?locale=EN_US) |
| **Pris en charge par** | Microsoft |
| | |

## <a name="check-point"></a>Check Point

| Attribut du connecteur | Description |
| --- | --- |
| **Méthode d’ingestion des données** | **[Common Event format (CEF)](connect-common-event-format.md) sur Syslog** <br><br>Disponible à partir de la [solution Check Point](sentinel-solutions-catalog.md#check-point)|
| **Table(s) Log Analytics** | CommonSecurityLog |
| **Documentation/<br>instructions d’installation du fournisseur** | [Exportateur de journaux : exportation de journaux Check Point](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk122323) |
| **Pris en charge par** | [Check Point](https://www.checkpoint.com/support-services/contact-support/) |
| | |


## <a name="cisco-asa"></a>Cisco ASA

| Attribut du connecteur | Description |
| --- | --- |
| **Méthode d’ingestion des données** | **[Common Event format (CEF)](connect-common-event-format.md) sur Syslog** <br><br>Disponible dans la [solution Cisco ASA](sentinel-solutions-catalog.md#cisco)|
| **Table(s) Log Analytics** | CommonSecurityLog |
| **Documentation/<br>instructions d’installation du fournisseur** | [Guide de configuration de l’interface de ligne de commande de la série Cisco ASA](https://www.cisco.com/c/en/us/support/docs/security/pix-500-series-security-appliances/63884-config-asa-00.html) |
| **Pris en charge par** | Microsoft |
| | |


## <a name="cisco-firepower-estreamer-preview"></a>Cisco Firepower eStreamer (préversion)

| Attribut du connecteur | Description |
| --- | --- |
| **Méthode d’ingestion des données** | **[Common Event format (CEF)](connect-common-event-format.md) sur Syslog** <br><br>[Configuration supplémentaire pour Cisco Firepower eStreamer](#extra-configuration-for-cisco-firepower-estreamer)|
| **Table(s) Log Analytics** | CommonSecurityLog |
| **Documentation/<br>instructions d’installation du fournisseur** | [eStreamer eNcore pour le guide des opérations Sentinel](https://www.cisco.com/c/en/us/td/docs/security/firepower/670/api/eStreamer_enCore/eStreamereNcoreSentinelOperationsGuide_409.html) |
| **Pris en charge par** | [Cisco](https://www.cisco.com/c/en/us/support/index.html)
| | |

### <a name="extra-configuration-for-cisco-firepower-estreamer"></a>Configuration supplémentaire pour Cisco Firepower eStreamer

1. **Installer le client Firepower eNcore**  
Installez et configurez le client Firepower eNcore eStreamer. Pour plus d’informations, consultez le [guide d’installation complet de Cisco](https://www.cisco.com/c/en/us/td/docs/security/firepower/670/api/eStreamer_enCore/eStreamereNcoreSentinelOperationsGuide_409.html).

1. **Télécharger le connecteur Firepower à partir de GitHub**  
Téléchargez la dernière version du connecteur Firepower eNcore pour Azure Sentinel à partir du [référentiel GitHub de Cisco](https://github.com/CiscoSecurity/fp-05-microsoft-sentinel-connector). Si vous envisagez d’utiliser python3, utilisez le [connecteur eStreamer python3](https://github.com/CiscoSecurity/fp-05-microsoft-sentinel-connector/tree/python3).

1. **Créer un fichier pkcs12 à l’aide de l’adresse IP Azure/de machine virtuelle**  
Créez un certificat pkcs12 à l’aide de l’adresse IP publique de l’instance de machine virtuelle dans Firepower sous **Système > Intégration > eStreamer**. Pour plus d’informations, consultez le [Guide d’installation](https://www.cisco.com/c/en/us/td/docs/security/firepower/670/api/eStreamer_enCore/eStreamereNcoreSentinelOperationsGuide_409.html#_Toc527049443).

1. **Tester la connectivité entre le client Azure/de machine virtuelle et FMC**  
Copiez le fichier pkcs12 de FMC vers l’instance Azure/de machine virtuelle et exécutez l’utilitaire de test (test ./encore.sh) pour vous assurer qu’une connexion peut être établie. Pour plus d’informations, consultez le [guide de configuration](https://www.cisco.com/c/en/us/td/docs/security/firepower/670/api/eStreamer_enCore/eStreamereNcoreSentinelOperationsGuide_409.html#_Toc527049430).

1. **Configurer eNcore pour diffuser en continu des données vers l’agent**  
Configurez eNcore pour diffuser en continu des données via TCP vers l’agent Log Analytics. Cette configuration doit être activée par défaut, mais des ports et des protocoles de diffusion en continu supplémentaires peuvent être configurés en fonction de la situation de sécurité de votre réseau. Il est également possible d’enregistrer les données dans le système de fichiers. Pour plus d’informations, consultez [Configurer eNcore](https://www.cisco.com/c/en/us/td/docs/security/firepower/670/api/eStreamer_enCore/eStreamereNcoreSentinelOperationsGuide_409.html#_Toc527049433).


## <a name="cisco-meraki-preview"></a>Cisco Meraki (préversion)

| Attribut du connecteur | Description |
| --- | --- |
| **Méthode d’ingestion des données** | [**Syslog**](connect-syslog.md)<br><br> Disponible dans la [solution Cisco ISE](sentinel-solutions-catalog.md#cisco)|
| **Table(s) Log Analytics** | syslog |
| **Alias de fonction Kusto :** | CiscoMeraki |
| **URL de fonction Kusto :** | https://aka.ms/sentinel-ciscomeraki-parser |
| **Documentation/<br>instructions d’installation du fournisseur** | [Documentation sur les rapports d’appareils Meraki](https://documentation.meraki.com/General_Administration/Monitoring_and_Reporting/Meraki_Device_Reporting_-_Syslog%2C_SNMP_and_API) |
| **Pris en charge par** | Microsoft |
| | |


## <a name="cisco-umbrella-preview"></a>Cisco Umbrella (préversion)

| Attribut du connecteur | Description |
| --- | --- |
| **Méthode d’ingestion des données** | [**Azure Functions et l’API REST**](connect-azure-functions-template.md) <br><br> Disponible dans la [solution Cisco Umbrella](sentinel-solutions-catalog.md#cisco)|
| **Table(s) Log Analytics** | Cisco_Umbrella_dns_CL<br>Cisco_Umbrella_proxy_CL<br>Cisco_Umbrella_ip_CL<br>Cisco_Umbrella_cloudfirewall_CL |
| **Code d’application de fonction Azure** | https://aka.ms/sentinel-CiscoUmbrellaConn-functionapp |
| **Informations d'identification de l’API** | <li>ID de clé d'accès AWS<li>Clé d’accès au secret AWS<li>Nom de compartiment S3 AWS |
| **Documentation/<br>instructions d’installation du fournisseur** | <li>[Connexion à Amazon S3](https://docs.umbrella.com/deployment-umbrella/docs/log-management#section-logging-to-amazon-s-3) |
| **Instructions de déploiement du connecteur** | <li>[Déploiement en un clic](connect-azure-functions-template.md?tabs=ARM) avec un modèle Azure Resource Manager (ARM)<li>[Déploiement manuel](connect-azure-functions-template.md?tabs=MPY) |
| **Alias de fonction Kusto** | Cisco_Umbrella |
| **Instructions de configuration de l’URL de fonction Kusto/<br>analyseur** | https://aka.ms/sentinel-ciscoumbrella-function |
| **Paramètres d’application** | <li>WorkspaceID<li>WorkspaceKey<li>S3Bucket<li>AWSAccessKeyId<li>AWSSecretAccessKey<li>logAnalyticsUri (facultatif) |
| **Pris en charge par** | Microsoft |
| | |

## <a name="cisco-unified-computing-system-ucs-preview"></a>Cisco Unified Computing System (UCS) (préversion)

| Attribut du connecteur | Description |
| --- | --- |
| **Méthode d’ingestion des données** | [**Syslog**](connect-syslog.md) |
| **Table(s) Log Analytics** | syslog |
| **Alias de fonction Kusto :** | CiscoUCS |
| **URL de fonction Kusto :** | https://aka.ms/sentinel-ciscoucs-function |
| **Documentation/<br>instructions d’installation du fournisseur** | [Configurer Syslog pour Cisco UCS - Cisco](https://www.cisco.com/c/en/us/support/docs/servers-unified-computing/ucs-manager/110265-setup-syslog-for-ucs.html#configsremotesyslog) |
| **Pris en charge par** | Microsoft |
| | |


## <a name="citrix-analytics-security"></a>Citrix Analytics (Sécurité)

| Attribut du connecteur | Description |
| --- | --- |
| **Méthode d’ingestion des données** | [**API du Collecteur de données Azure Sentinel**](connect-rest-api-template.md) |
| **Table(s) Log Analytics** | CitrixAnalytics_SAlerts_CL |
| **Documentation/<br>instructions d’installation du fournisseur** | [Connecter Citrix à Azure Sentinel](https://aka.ms/Sentinel-Citrix-Connector) |
| **Pris en charge par** | [Systèmes Citrix](https://www.citrix.com/support/) |
| | |

## <a name="citrix-web-app-firewall-waf-preview"></a>Citrix Web App Firewall (WAF) (préversion)

| Attribut du connecteur | Description |
| --- | --- |
| **Méthode d’ingestion des données** | **[Common Event format (CEF)](connect-common-event-format.md) sur Syslog** |
| **Table(s) Log Analytics** | CommonSecurityLog |
| **Documentation/<br>instructions d’installation du fournisseur** | Pour configurer WAF, consultez la page [WIKI de support - Configuration du pare-feu d’applications web avec NetScaler](https://support.citrix.com/article/CTX234174).<br><br>Pour configurer les journaux CEF, consulter [Prise en charge de la journalisation CEF dans le pare-feu d’application](https://support.citrix.com/article/CTX136146).<br><br>Pour transférer les journaux vers le proxy, consultez [Configuration de l’appliance Citrix ADC pour la journalisation d’audit](https://docs.citrix.com/en-us/citrix-adc/current-release/system/audit-logging/configuring-audit-logging.html). |
| **Pris en charge par** | [Systèmes Citrix](https://www.citrix.com/support/) |
| | |


## <a name="cognni-preview"></a>Cognni (préversion)

| Attribut du connecteur | Description |
| --- | --- |
| **Méthode d’ingestion des données** | [**API du Collecteur de données Azure Sentinel**](connect-rest-api-template.md) |
| **Table(s) Log Analytics** | CognniIncidents_CL |
| **Documentation/<br>instructions d’installation du fournisseur** | **Se connecter à Cognni**<br><ol><li>Accédez à la [page d’intégrations Cognni](https://intelligence.cognni.ai/integrations).<li>Sélectionnez **Connecter** dans la zone Azure Sentinel.<li>Collez **workspaceId** et **sharedKey** (Clé primaire) dans les champs sur l’écran d’intégrations de Cognni.<li>Sélectionnez le bouton **Connecter** pour terminer la configuration. |
| **Pris en charge par** | [Cognni](https://cognni.ai/contact-support/)
| | |

## <a name="continuous-threat-monitoring-for-sap-preview"></a>Surveillance continue des menaces pour SAP (préversion)

| Attribut du connecteur | Description |
| --- | --- |
| **Méthode d’ingestion des données** | Disponible uniquement après l’installation de la [solution Surveillance continue des menaces pour SAP](sentinel-solutions-catalog.md#sap)|
| **Table(s) Log Analytics** | Consultez [Informations de référence sur les journaux de la solution SAP Azure Sentinel](sap-solution-log-reference.md) |
| **Documentation/<br>instructions d’installation du fournisseur** | [Déployer une surveillance continue des menaces SAP](sap-deploy-solution.md) |
| **Pris en charge par** | Microsoft |
| | |


## <a name="cyberark-enterprise-password-vault-epv-events-preview"></a>Événements CyberArk Enterprise Password Vault (EPV) (préversion)

| Attribut du connecteur | Description |
| --- | --- |
| **Méthode d’ingestion des données** | **[Common Event format (CEF)](connect-common-event-format.md) sur Syslog** |
| **Table(s) Log Analytics** | CommonSecurityLog |
| **Documentation/<br>instructions d’installation du fournisseur** | [Applications Security Information and Event Management (SIEM)](https://docs.cyberark.com/Product-Doc/OnlineHelp/PAS/Latest/en/Content/PASIMP/DV-Integrating-with-SIEM-Applications.htm) |
| **Pris en charge par** | [CyberArk](https://www.cyberark.com/customer-support/) |
| | |


## <a name="cyberpion-security-logs-preview"></a>Journaux de sécurité Cyberpion (préversion)

| Attribut du connecteur | Description |
| --- | --- |
| **Méthode d’ingestion des données** | [**API du Collecteur de données Azure Sentinel**](connect-rest-api-template.md) |
| **Table(s) Log Analytics** | CyberpionActionItems_CL |
| **Documentation/<br>instructions d’installation du fournisseur** | [Obtenir un abonnement Cyberpion](https://azuremarketplace.microsoft.com/en/marketplace/apps/cyberpion1597832716616.cyberpion)<br>[Intégrer les alertes de sécurité Cyberpion à Azure Sentinel](https://www.cyberpion.com/resource-center/integrations/azure-sentinel/) |
| **Pris en charge par** | [Cyberpion](https://www.cyberpion.com/) |
| | |



## <a name="dns-preview"></a>DNS (préversion)

**Consultez [Serveur DNS Windows (préversion)](#windows-dns-server-preview).**

## <a name="dynamics-365"></a>Dynamics 365

| Attribut du connecteur | Description |
| --- | --- |
| **Méthode d’ingestion des données** | **Intégration de service à service Azure: <br>[connexions basées sur l’API](connect-azure-windows-microsoft-services.md#api-based-connections)** <br><br> Également disponible dans le cadre de la [solution Azure Sentinel 4 Dynamics 365](sentinel-solutions-catalog.md#azure)|
| **Licences requises/<br>informations de coût** | <li>[Licence de production Microsoft Dynamics 365](/office365/servicedescriptions/microsoft-dynamics-365-online-service-description). Non disponible pour les environnements de bac à sable.<li>Un abonnement Microsoft 365 Entreprise [E3 ou E5](/power-platform/admin/enable-use-comprehensive-auditing#requirements) est requis pour la journalisation des activités.<br>D’autres frais d'opérateur peuvent s'appliquer |
| **Table(s) Log Analytics** | Dynamics365Activity |
| **Pris en charge par** | Microsoft |
| | |

## <a name="eset-enterprise-inspector-preview"></a>ESET Enterprise Inspector (préversion)

| Attribut du connecteur | Description |
| --- | --- |
| **Méthode d’ingestion des données** | [**Azure Functions et l’API REST**](connect-azure-functions-template.md)<br><br>[Créer un utilisateur d’API](#create-an-api-user) |
| **Table(s) Log Analytics** | ESETEnterpriseInspector_CL |
| **Informations d'identification de l’API** | <li>Nom d’utilisateur EEI<li>Mot de passe EEI<li>URL de base |
| **Documentation/<br>instructions d’installation du fournisseur** | <li>[Documentation sur l’API REST ESET Enterprise Inspector](https://help.eset.com/eei/1.5/en-US/api.html) |
| **Instructions de déploiement du connecteur** | [Déploiement en un clic](connect-azure-functions-template.md?tabs=ARM) avec un modèle Azure Resource Manager (ARM) |
| **Pris en charge par** | [ESET](https://support.eset.com/en) |
| | |
### <a name="create-an-api-user"></a>Créer un utilisateur d’API

1. Connectez-vous à ESET Security Management Center / la console ESET PROTECT avec un compte administrateur, sélectionnez l’onglet **Plus**, puis le sous-onglet **Utilisateurs**.
1. Sélectionnez le bouton **AJOUTER NOUVEAU** et ajoutez un **utilisateur natif**.
1. Créez un nouvel utilisateur pour le compte d’API. **Facultatif :** sélectionnez un **groupe de base** autre que **Tous** pour limiter les détections qui sont ingérées.
1. Sous l’onglet **Ensembles d’autorisations**, attribuez l’ensemble d’autorisations **Réviseur Enterprise Inspector**.
1. Déconnectez-vous du compte administrateur et connectez-vous à la console avec les nouvelles informations d’identification de l’API pour validation, puis déconnectez-vous du compte d’API.

## <a name="eset-security-management-center-smc-preview"></a>ESET Security Management Center (SMC) (préversion)

| Attribut du connecteur | Description |
| --- | --- |
| **Méthode d’ingestion des données** | [**Syslog**](connect-syslog.md)<br><br>[Configurer les journaux ESET SMC à collecter](#configure-the-eset-smc-logs-to-be-collected) <br>[Configurer l’agent OMS pour qu’il passe les données Eset SMC au format API](#configure-oms-agent-to-pass-eset-smc-data-in-api-format)<br>[Changer la configuration de l'agent OMS pour saisir l'étiquette oms.api.eset et analyser les données structurées](#change-oms-agent-configuration-to-catch-tag-omsapieset-and-parse-structured-data)<br>[Désactiver la configuration automatique et redémarrer l’agent](#disable-automatic-configuration-and-restart-agent)|
| **Table(s) Log Analytics** | eset_CL |
| **Documentation/<br>instructions d’installation du fournisseur** | [Documentation du serveur Syslog ESET](https://help.eset.com/esmc_admin/70/en-US/admin_server_settings_syslog.html) |
| **Pris en charge par** | [ESET](https://support.eset.com/en) |
| | |

### <a name="configure-the-eset-smc-logs-to-be-collected"></a>Configurer les journaux ESET SMC à collecter

Configurez rsyslog pour accepter les journaux provenant de votre adresse IP Eset SMC.

```bash
    sudo -i
    # Set ESET SMC source IP address
    export ESETIP={Enter your IP address}

    # Create rsyslog configuration file
    cat > /etc/rsyslog.d/80-remote.conf << EOF
    \$ModLoad imudp
    \$UDPServerRun 514
    \$ModLoad imtcp
    \$InputTCPServerRun 514
    \$AllowedSender TCP, 127.0.0.1, $ESETIP
    \$AllowedSender UDP, 127.0.0.1, $ESETIP user.=alert;user.=crit;user.=debug;user.=emerg;user.=err;user.=info;user.=notice;user.=warning  @127.0.0.1:25224
    EOF

    # Restart rsyslog
    systemctl restart rsyslog
```

### <a name="configure-oms-agent-to-pass-eset-smc-data-in-api-format"></a>Configurer l’agent OMS pour qu’il passe les données Eset SMC au format API

Pour reconnaître facilement les données Eset, envoyez-les (push) à une table distincte et analysez au niveau de l’agent pour simplifier et accélérer votre requête Azure Sentinel. 

Dans le fichier **/etc/opt/microsoft/omsagent/{REPLACEyourworkspaceid}/conf/omsagent.conf**, modifiez la section `match oms.**` pour envoyer les données en tant qu’objets d’API, en remplaçant le type par `out_oms_api`.
    
Le code suivant est un exemple de la section `match oms.**` complète :

```bash
    <match oms.** docker.**>
      type out_oms_api
      log_level info
      num_threads 5
      run_in_background false

      omsadmin_conf_path /etc/opt/microsoft/omsagent/{REPLACEyourworkspaceid}/conf/omsadmin.conf
      cert_path /etc/opt/microsoft/omsagent/{REPLACEyourworkspaceid}/certs/oms.crt
      key_path /etc/opt/microsoft/omsagent/{REPLACEyourworkspaceid}/certs/oms.key

      buffer_chunk_limit 15m
      buffer_type file
      buffer_path /var/opt/microsoft/omsagent/{REPLACEyourworkspaceid}/state/out_oms_common*.buffer

      buffer_queue_limit 10
      buffer_queue_full_action drop_oldest_chunk
      flush_interval 20s
      retry_limit 10
      retry_wait 30s
      max_retry_wait 9m
    </match>
```

### <a name="change-oms-agent-configuration-to-catch-tag-omsapieset-and-parse-structured-data"></a>Changer la configuration de l'agent OMS pour saisir l'étiquette oms.api.eset et analyser les données structurées

Modifiez le fichier **/etc/opt/microsoft/omsagent/{REPLACEyourworkspaceid}/conf/omsagent.d/syslog.conf**. 

Par exemple :

```bash
    <source>
      type syslog
      port 25224
      bind 127.0.0.1
      protocol_type udp
      tag oms.api.eset
    </source>

    <filter oms.api.**>
      @type parser
      key_name message
      format /(?<message>.*?{.*})/
    </filter>

    <filter oms.api.**>
      @type parser
      key_name message
      format json
    </filter>
```
### <a name="disable-automatic-configuration-and-restart-agent"></a>Désactiver la configuration automatique et redémarrer l’agent

Par exemple : 

```bash
    # Disable changes to configuration files from Portal
    sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/OMS_MetaConfigHelper.py --disable'

    # Restart agent
    sudo /opt/microsoft/omsagent/bin/service_control restart

    # Check agent logs
    tail -f /var/opt/microsoft/omsagent/log/omsagent.log
```

### <a name="configure-eset-smc-to-send-logs-to-connector"></a>Configurer Eset SMC pour envoyer des journaux au connecteur

Configurez les journaux Eset en utilisant le style BSD et le format JSON.

- Accédez à la configuration du serveur Syslog pour configurer Host (votre connecteur), Format BSD et Transport TCP.
- Accédez à la section de journalisation et activez JSON.

Pour plus d’informations, consultez la documentation d’Eset.

## <a name="exabeam-advanced-analytics-preview"></a>Exabeam Advanced Analytics (préversion)

| Attribut du connecteur | Description |
| --- | --- |
| **Méthode d’ingestion des données** | [**Syslog**](connect-syslog.md) |
| **Table(s) Log Analytics** | syslog |
| **Alias de fonction Kusto :** | ExabeamEvent |
| **URL de fonction Kusto :** | https://aka.ms/sentinel-Exabeam-parser |
| **Documentation/<br>instructions d’installation du fournisseur** | [Configurer les notifications d’activité du système Advanced Analytics](https://docs.exabeam.com/en/advanced-analytics/i54/advanced-analytics-administration-guide/113254-configure-advanced-analytics.html#UUID-7ce5ff9d-56aa-93f0-65de-c5255b682a08) |
| **Pris en charge par** | Microsoft |
| | |

## <a name="extrahop-revealx"></a>ExtraHop Reveal(x)

| Attribut du connecteur | Description |
| --- | --- |
| **Méthode d’ingestion des données** | **[Common Event format (CEF)](connect-common-event-format.md) sur Syslog** |
| **Table(s) Log Analytics** | CommonSecurityLog |
| **Documentation/<br>instructions d’installation du fournisseur** | [Connecteur SIEM ExtraHop Detection](https://aka.ms/asi-syslog-extrahop-forwarding) |
| **Pris en charge par** | [ExtraHop](https://www.extrahop.com/support/) |
| | |

## <a name="f5-big-ip"></a>F5 BIG-IP

| Attribut du connecteur | Description |
| --- | --- |
| **Méthode d’ingestion des données** | [**API du Collecteur de données Azure Sentinel**](connect-rest-api-template.md) |
| **Table(s) Log Analytics** | F5Telemetry_LTM_CL<br>F5Telemetry_system_CL<br>F5Telemetry_ASM_CL |
| **Documentation/<br>instructions d’installation du fournisseur** | [Intégration de F5 BIG-IP à Azure Sentinel](https://aka.ms/F5BigIp-Integrate) |
| **Pris en charge par** | [F5 Networks](https://support.f5.com/csp/home) |
| | |
## <a name="f5-networks-asm"></a>F5 Networks (ASM)

| Attribut du connecteur | Description |
| --- | --- |
| **Méthode d’ingestion des données** | **[Common Event format (CEF)](connect-common-event-format.md) sur Syslog** |
| **Table(s) Log Analytics** | CommonSecurityLog |
| **Documentation/<br>instructions d’installation du fournisseur** | [Configuration de la journalisation des événements de sécurité des applications](https://aka.ms/asi-syslog-f5-forwarding) |
| **Pris en charge par** | [F5 Networks](https://support.f5.com/csp/home) |
| | |



## <a name="forcepoint-cloud-access-security-broker-casb-preview"></a>Forcepoint Cloud Access Security Broker (CASB) (préversion)

| Attribut du connecteur | Description |
| --- | --- |
| **Méthode d’ingestion des données** | **[Common Event format (CEF)](connect-common-event-format.md) sur Syslog** |
| **Table(s) Log Analytics** | CommonSecurityLog |
| **Documentation/<br>instructions d’installation du fournisseur** | [Forcepoint CASB et Azure Sentinel](https://forcepoint.github.io/docs/casb_and_azure_sentinel/) |
| **Pris en charge par** | [Forcepoint](https://support.forcepoint.com/) |
| | |

## <a name="forcepoint-cloud-security-gateway-csg-preview"></a>Forcepoint Cloud Security Gateway (CSG) (préversion)

| Attribut du connecteur | Description |
| --- | --- |
| **Méthode d’ingestion des données** | **[Common Event format (CEF)](connect-common-event-format.md) sur Syslog** |
| **Table(s) Log Analytics** | CommonSecurityLog |
| **Documentation/<br>instructions d’installation du fournisseur** | [Forcepoint Cloud Security Gateway et Azure Sentinel](https://forcepoint.github.io/docs/csg_and_sentinel/) |
| **Pris en charge par** | [Forcepoint](https://support.forcepoint.com/) |
| | |

## <a name="forcepoint-data-loss-prevention-dlp-preview"></a>Forcepoint Data Loss Prevention (DLP) (préversion)

| Attribut du connecteur | Description |
| --- | --- |
| **Méthode d’ingestion des données** | [**API du Collecteur de données Azure Sentinel**](connect-rest-api-template.md) |
| **Table(s) Log Analytics** | ForcepointDLPEvents_CL |
| **Documentation/<br>instructions d’installation du fournisseur** | [Forcepoint Data Loss Prevention et Azure Sentinel](https://forcepoint.github.io/docs/dlp_and_azure_sentinel/) |
| **Pris en charge par** | [Forcepoint](https://support.forcepoint.com/) |
| | |

## <a name="forcepoint-next-generation-firewall-ngfw-preview"></a>Forcepoint Next Generation Firewall (NGFW) (préversion)

| Attribut du connecteur | Description |
| --- | --- |
| **Méthode d’ingestion des données** | **[Common Event format (CEF)](connect-common-event-format.md) sur Syslog** |
| **Table(s) Log Analytics** | CommonSecurityLog |
| **Documentation/<br>instructions d’installation du fournisseur** | [Forcepoint Next-Gen Firewall et Azure Sentinel](https://forcepoint.github.io/docs/ngfw_and_azure_sentinel/) |
| **Pris en charge par** | [Forcepoint](https://support.forcepoint.com/) |
| | |



## <a name="forgerock-common-audit-caud-for-cef-preview"></a>ForgeRock Common Audit (CAUD) pour CEF (préversion)

| Attribut du connecteur | Description |
| --- | --- |
| **Méthode d’ingestion des données** | **[Common Event format (CEF)](connect-common-event-format.md) sur Syslog** |
| **Table(s) Log Analytics** | CommonSecurityLog |
| **Documentation/<br>instructions d’installation du fournisseur** | [Installez-le en premier ! ForgeRock Common Audit (CAUD) pour Azure Sentinel](https://github.com/javaservlets/SentinelAuditEventHandler) |
| **Pris en charge par** | [ForgeRock](https://www.forgerock.com/support) |
| | |

## <a name="fortinet"></a>Fortinet

| Attribut du connecteur | Description |
| --- | --- |
| **Méthode d’ingestion des données** | **[Common Event format (CEF)](connect-common-event-format.md) sur Syslog** <br><br>[Envoyer les journaux Fortinet au redirecteur de journal](#send-fortinet-logs-to-the-log-forwarder) <br<br>Disponible dans la [solution Fortinet FortiGate](sentinel-solutions-catalog.md#fortinet-fortigate)|
| **Table(s) Log Analytics** | CommonSecurityLog |
| **Documentation/<br>instructions d’installation du fournisseur** | [Bibliothèque de documents Fortinet](https://aka.ms/asi-syslog-fortinet-fortinetdocumentlibrary)<br>Choisissez votre version et utilisez le manuel d’utilisation (*Handbook*) et les PDF de référence des messages de journaux (*Log Message Reference*). |
| **Pris en charge par** | [Fortinet](https://support.fortinet.com/) |
| | |

### <a name="send-fortinet-logs-to-the-log-forwarder"></a>Envoyer les journaux Fortinet au redirecteur de journal

Ouvrez l’interface CLI sur votre appliance Fortinet et exécutez les commandes suivantes :

```Console
config log syslogd setting
set status enable
set format cef
set port 514
set server <ip_address_of_Forwarder>
end
```

- Remplacez l’**adresse IP** du serveur par l’adresse IP du redirecteur de journal.
- Affectez au **port Syslog** la valeur **514** ou la valeur du port défini sur le démon Syslog sur le redirecteur.
- Pour activer le format CEF dans les premières versions de FortiOS, vous devrez peut-être exécuter le jeu de commandes **csv disable**.



## <a name="google-workspace-g-suite-preview"></a>Google Workspace (G-Suite) (préversion)

| Attribut du connecteur | Description |
| --- | --- |
| **Méthode d’ingestion des données** | [**Azure Functions et l’API REST**](connect-azure-functions-template.md)<br><br>[Configuration supplémentaire de l’API de rapports Google](#extra-configuration-for-the-google-reports-api) |
| **Table(s) Log Analytics** | GWorkspace_ReportsAPI_admin_CL<br>GWorkspace_ReportsAPI_calendar_CL<br>GWorkspace_ReportsAPI_drive_CL<br>GWorkspace_ReportsAPI_login_CL<br>GWorkspace_ReportsAPI_mobile_CL<br>GWorkspace_ReportsAPI_token_CL<br>GWorkspace_ReportsAPI_user_accounts_CL<br> |
| **Code d’application de fonction Azure** | https://aka.ms/sentinel-GWorkspaceReportsAPI-functionapp |
| **Informations d'identification de l’API** | <li>GooglePickleString |
| **Documentation/<br>instructions d’installation du fournisseur** | <li>[Documentation de l’API](https://developers.google.com/admin-sdk/reports/v1/reference/activities)<li>Obtenez des informations d’identification dans [Effectuer une délégation d’autorité à l’échelle du domaine Google Workspace](https://developers.google.com/admin-sdk/reports/v1/guides/delegation)<li>[Convertir le fichier token.pickle en chaîne pickle](https://aka.ms/sentinel-GWorkspaceReportsAPI-functioncode) |
| **Instructions de déploiement du connecteur** | <li>[Déploiement en un clic](connect-azure-functions-template.md?tabs=ARM) avec un modèle Azure Resource Manager (ARM)<li>[Déploiement manuel](connect-azure-functions-template.md?tabs=MPY) |
| **Alias de fonction Kusto** | GWorkspaceActivityReports |
| **Instructions de configuration de l’URL de fonction Kusto/<br>analyseur** | https://aka.ms/sentinel-GWorkspaceReportsAPI-parser |
| **Paramètres d’application** | <li>GooglePickleString<li>WorkspaceID<li>workspaceKey<li>logAnalyticsUri (facultatif) |
| **Pris en charge par** | Microsoft |
| | |

### <a name="extra-configuration-for-the-google-reports-api"></a>Configuration supplémentaire de l’API de rapports Google

Ajoutez http://localhost:8081/ sous **URI de redirection autorisées** lors de la création des [informations d'identification d’application web](https://developers.google.com/workspace/guides/create-credentials#web).

1. [Suivez les instructions](https://developers.google.com/admin-sdk/reports/v1/quickstart/python) pour obtenir credentials.json.
1. Pour obtenir la chaîne pickle Google, exécutez [ce script Python](https://aka.ms/sentinel-GWorkspaceReportsAPI-functioncode) (dans le même chemin d’accès que credentials.json).
1. Copiez la sortie de chaîne pickle entre guillemets simples et enregistrez. Elle sera nécessaire pour déployer l’application de fonction.


## <a name="illusive-attack-management-system-ams-preview"></a>Illusive Attack Management System (AMS) (préversion)

| Attribut du connecteur | Description |
| --- | --- |
| **Méthode d’ingestion des données** | **[Common Event format (CEF)](connect-common-event-format.md) sur Syslog** |
| **Table(s) Log Analytics** | CommonSecurityLog |
| **Documentation/<br>instructions d’installation du fournisseur** | [Guide d’administration d’Illusive Networks](https://support.illusivenetworks.com/hc/en-us/sections/360002292119-Documentation-by-Version) |
| **Pris en charge par** | [Illusive Networks](https://www.illusivenetworks.com/technical-support/) |
| | |

## <a name="imperva-waf-gateway-preview"></a>Imperva WAF Gateway (préversion)

| Attribut du connecteur | Description |
| --- | --- |
| **Méthode d’ingestion des données** | **[Common Event format (CEF)](connect-common-event-format.md) sur Syslog** <br><br>Disponible dans la [solution Imperva Cloud WAF](sentinel-solutions-catalog.md#imperva)|
| **Table(s) Log Analytics** | CommonSecurityLog |
| **Documentation/<br>instructions d’installation du fournisseur** | [Procédure à suivre pour activer la journalisation des alertes Imperva WAF Gateway dans Azure Sentinel](https://community.imperva.com/blogs/craig-burlingame1/2020/11/13/steps-for-enabling-imperva-waf-gateway-alert) |
| **Pris en charge par** | [Imperva](https://www.imperva.com/support/technical-support/) |
| | |


## <a name="infoblox-network-identity-operating-system-nios-preview"></a>Infoblox Network Identity Operating System (NIOS) (préversion)

| Attribut du connecteur | Description |
| --- | --- |
| **Méthode d’ingestion des données** | [**Syslog**](connect-syslog.md)<br><br> Disponible dans la [solution InfoBlox Threat Defense](sentinel-solutions-catalog.md#infoblox) |
| **Table(s) Log Analytics** | syslog |
| **Alias de fonction Kusto :** | InfobloxNIOS |
| **URL de fonction Kusto :** | https://aka.ms/sentinelgithubparsersinfoblox |
| **Documentation/<br>instructions d’installation du fournisseur** | [Guide de déploiement de NIOS SNMP et Syslog](https://www.infoblox.com/wp-content/uploads/infoblox-deployment-guide-slog-and-snmp-configuration-for-nios.pdf) |
| **Pris en charge par** | Microsoft |
| | |




## <a name="juniper-srx-preview"></a>Juniper SRX (préversion)

| Attribut du connecteur | Description |
| --- | --- |
| **Méthode d’ingestion des données** | [**Syslog**](connect-syslog.md) |
| **Table(s) Log Analytics** | syslog |
| **Alias de fonction Kusto :** | JuniperSRX |
| **URL de fonction Kusto :** | https://aka.ms/sentinel-junipersrx-parser |
| **Documentation/<br>instructions d’installation du fournisseur** | [Configurer la journalisation du trafic (journaux de stratégie de sécurité) pour les appareils de branche SRX](https://kb.juniper.net/InfoCenter/index?page=content&id=KB16509&actp=METADATA)<br>[Configurer la journalisation du système](https://kb.juniper.net/InfoCenter/index?page=content&id=kb16502) |
| **Pris en charge par** | [Juniper Networks](https://support.juniper.net/support/) |
| | |

## <a name="lookout-mobile-threat-defense-preview"></a>Lookout Mobile Threat Defense (préversion)

| Attribut du connecteur | Description |
| --- | --- |
| **Méthode d’ingestion des données** | [**Azure Functions et l’API REST**](connect-azure-functions-template.md) <br><br>Disponible uniquement après l’installation de la solution [Lookout Mobile Threat Defense for Azure Sentinel](sentinel-solutions-catalog.md#lookout) |
| **Table(s) Log Analytics** | Lookout_CL |
| **Informations d'identification de l’API** | <li>Clé d’application Lookout |
| **Documentation/<br>instructions d’installation du fournisseur** | <li>[Guide d’installation](https://esupport.lookout.com/s/article/Lookout-with-Azure-Sentinel) (connexion requise)<li>[Documentation de l’API](https://esupport.lookout.com/s/article/Mobile-Risk-API-Guide) (connexion requise)<li>[Lookout Mobile Endpoint Security](https://www.lookout.com/products/mobile-endpoint-security) |
| **Pris en charge par** | [Lookout](https://www.lookout.com/support) |
| | |



## <a name="microsoft-365-defender"></a>Microsoft 365 Defender

| Attribut du connecteur | Description |
| --- | --- |
| **Méthode d’ingestion des données** | **Intégration de service à service Azure : <br>[Connecter des données de Microsoft 365 Defender à Azure Sentinel](connect-microsoft-365-defender.md)** (Article du premier connecteur) |
| **Licences requises/<br>informations de coût** | [Licence valide pour Microsoft 365 Defender](/microsoft-365/security/mtp/prerequisites)
| **Table(s) Log Analytics** | **Alertes :**<br>SecurityAlert<br>SecurityIncident<br>**Événements Defender pour point de terminaison :**<br>DeviceEvents<br>DeviceFileEvents<br>DeviceImageLoadEvents<br>DeviceInfo<br>DeviceLogonEvents<br>DeviceNetworkInfo<br>DeviceNetworkInfo<br>DeviceProcessEvents<br>DeviceRegistryEvents<br>DeviceFileCertificateInfo<br>**Événements Defender pour Office 365 :**<br>EmailAttachmentInfo<br>EmailUrlInfo<br>EmailEvents<br>EmailPostDeliveryEvents |
| **Pris en charge par** | Microsoft |
| | |

## <a name="microsoft-365-insider-risk-management-irm-preview"></a>Gestion des risques internes (IRM) Microsoft 365 (préversion)

| Attribut du connecteur | Description |
| --- | --- |
| **Méthode d’ingestion des données** | **Intégration de service à service Azure: <br>[connexions basées sur l’API](connect-azure-windows-microsoft-services.md#api-based-connections)**<br><br>Également disponible dans la [solution Gestion des risques internes Microsoft 365](sentinel-solutions-catalog.md#domain-solutions) |
| **Licence et autres conditions préalables** | <ul><li>Abonnement valide pour Microsoft 365 E5/A5/G5 ou les modules complémentaires de conformité ou IRM connexes.<li>[Gestion des risques internes (IRM) Microsoft 365](/microsoft-365/compliance/insider-risk-management) totalement intégré et [stratégies IRM](/microsoft-365/compliance/insider-risk-management-policies) définies et générant des alertes.<li>[IRM Microsoft 365 configurée](/microsoft-365/compliance/insider-risk-management-settings#export-alerts-preview) pour activer l’exportation d’alertes IRM vers l’API Activité de gestion Office 365 afin de recevoir les alertes via le connecteur Azure Sentinel.
| **Table(s) Log Analytics** | SecurityAlert |
| **Filtre de requête de données** | `SecurityAlert`<br>`\| where ProductName == "Microsoft 365 Insider Risk Management"` |
| **Pris en charge par** | Microsoft |
| | |

## <a name="microsoft-cloud-app-security-mcas"></a>Microsoft Cloud App Security (MCAS)

| Attribut du connecteur | Description |
| --- | --- |
| **Méthode d’ingestion des données** | **Intégration de service à service Azure: <br>[connexions basées sur l’API](connect-azure-windows-microsoft-services.md#api-based-connections)**<br><br>Pour les journaux Cloud Discovery, [activez Azure Sentinel comme SIEM dans Microsoft Cloud App Security](/cloud-app-security/siem-sentinel) |
| **Table(s) Log Analytics** | SecurityAlert : pour les alertes<br>McasShadowItReporting : pour les journaux Cloud Discovery |
| **Pris en charge par** | Microsoft |
| | |

## <a name="microsoft-defender-for-endpoint"></a>Microsoft Defender for Endpoint

| Attribut du connecteur | Description |
| --- | --- |
| **Méthode d’ingestion des données** | **Intégration de service à service Azure: <br>[connexions basées sur l’API](connect-azure-windows-microsoft-services.md#api-based-connections)** |
| **Licences requises/<br>informations de coût** | [Licence valide pour Microsoft Defender pour point de terminaison](/microsoft-365/security/defender-endpoint/production-deployment)
| **Table(s) Log Analytics** | SecurityAlert |
| **Pris en charge par** | Microsoft |
| | |

## <a name="microsoft-defender-for-identity"></a>Microsoft Defender pour Identity

| Attribut du connecteur | Description |
| --- | --- |
| **Méthode d’ingestion des données** | **Intégration de service à service Azure: <br>[connexions basées sur l’API](connect-azure-windows-microsoft-services.md#api-based-connections)** |
| **Table(s) Log Analytics** | SecurityAlert |
| **Pris en charge par** | Microsoft |
| | |

## <a name="microsoft-defender-for-office-365"></a>Microsoft Defender pour Office 365

| Attribut du connecteur | Description |
| --- | --- |
| **Méthode d’ingestion des données** | **Intégration de service à service Azure: <br>[connexions basées sur l’API](connect-azure-windows-microsoft-services.md#api-based-connections)** |
| **Licences requises/<br>informations de coût** | Vous devez avoir une licence valide pour [Office 365 ATP Plan 2](/microsoft-365/security/office-365-security/office-365-atp#office-365-atp-plan-1-and-plan-2)
| **Table(s) Log Analytics** | SecurityAlert |
| **Pris en charge par** | Microsoft |
| | |

## <a name="microsoft-office-365"></a>Microsoft Office 365

| Attribut du connecteur | Description |
| --- | --- |
| **Méthode d’ingestion des données** | **Intégration de service à service Azure: <br>[connexions basées sur l’API](connect-azure-windows-microsoft-services.md#api-based-connections)** |
| **Licences requises/<br>informations de coût** | Votre déploiement Office 365 doit se trouver sur le même locataire que votre espace de travail Azure Sentinel.<br>D’autres frais d'opérateur peuvent s'appliquer |
| **Table(s) Log Analytics** | OfficeActivity |
| **Pris en charge par** | Microsoft |
| | |



## <a name="morphisec-utpp-preview"></a>Morphisec UTPP (préversion)

| Attribut du connecteur | Description |
| --- | --- |
| **Méthode d’ingestion des données** | **[Common Event Format (CEF)](connect-common-event-format.md) sur Syslog**, avec un analyseur de fonction Kusto |
| **Table(s) Log Analytics** | CommonSecurityLog |
| **Alias de fonction Kusto :** | Morphisec |
| **URL de fonction Kusto** | https://aka.ms/sentinel-Morphiescutpp-parser |
| **Pris en charge par** | [Morphisec](https://support.morphisec.com/support/home) |
| | |


## <a name="netskope-preview"></a>Netskope (préversion)

| Attribut du connecteur | Description |
| --- | --- |
| **Méthode d’ingestion des données** | [**Azure Functions et l’API REST**](connect-azure-functions-template.md) |
| **Table(s) Log Analytics** | Netskope_CL |
| **Code d’application de fonction Azure** | https://aka.ms/sentinel-netskope-functioncode |
| **Informations d'identification de l’API** | <li>Jeton d’API Netskope |
| **Documentation/<br>instructions d’installation du fournisseur** | <li>[Netskope Cloud Security Platform](https://www.netskope.com/platform)<li>[Documentation de l'API Netskope](https://innovatechcloud.goskope.com/docs/Netskope_Help/en/rest-api-v1-overview.html)<li>[Obtenir un jeton d’API](https://innovatechcloud.goskope.com/docs/Netskope_Help/en/rest-api-v2-overview.html) |
| **Instructions de déploiement du connecteur** | <li>[Déploiement en un clic](connect-azure-functions-template.md?tabs=ARM) avec un modèle Azure Resource Manager (ARM)<li>[Déploiement manuel](connect-azure-functions-template.md?tabs=MPS) |
| **Alias de fonction Kusto** | Netskope |
| **Instructions de configuration de l’URL de fonction Kusto/<br>analyseur** | https://aka.ms/sentinel-netskope-parser |
| **Paramètres d’application** | <li>apikey<li>workspaceID<li>workspaceKey<li>URI (dépend de la région, suit le schéma : `https://<Tenant Name>.goskope.com`) <li>timeInterval (défini sur 5)<li>logTypes<li>logAnalyticsUri (facultatif) |
| **Pris en charge par** | Microsoft |
| | |

## <a name="nginx-http-server-preview"></a>NGINX HTTP Server (préversion)

| Attribut du connecteur | Description |
| --- | --- |
| **Méthode d’ingestion des données** | [**Agent Log Analytics : journaux personnalisés**](connect-custom-logs.md) |
| **Table(s) Log Analytics** | NGINX_CL |
| **Alias de fonction Kusto :** | NGINXHTTPServer |
| **URL de fonction Kusto** | https://aka.ms/sentinel-NGINXHTTP-parser |
| **Documentation/<br>instructions d’installation du fournisseur** | [Module ngx_http_log_module](https://nginx.org/en/docs/http/ngx_http_log_module.html) |
| **Exemple de fichier journal personnalisé :** | access.log ou error.log |
| **Pris en charge par** | Microsoft |
| | |

## <a name="nxlog-basic-security-module-bsm-macos-preview"></a>NXLog Basic Security Module (BSM) macOS (préversion)

| Attribut du connecteur | Description |
| --- | --- |
| **Méthode d’ingestion des données** | [**API du Collecteur de données Azure Sentinel**](connect-rest-api-template.md) |
| **Table(s) Log Analytics** | BSMmacOS_CL |
| **Documentation/<br>instructions d’installation du fournisseur** | [Guide de l’utilisateur NXLog Azure Sentinel](https://nxlog.co/documentation/nxlog-user-guide/sentinel.html) |
| **Pris en charge par** | [NXLog](https://nxlog.co/community-forum) |
| | |


## <a name="nxlog-dns-logs-preview"></a>NXLog DNS Logs (préversion)

| Attribut du connecteur | Description |
| --- | --- |
| **Méthode d’ingestion des données** | [**API du Collecteur de données Azure Sentinel**](connect-rest-api-template.md) |
| **Table(s) Log Analytics** | DNS_Logs_CL |
| **Documentation/<br>instructions d’installation du fournisseur** | [Guide de l’utilisateur NXLog Azure Sentinel](https://nxlog.co/documentation/nxlog-user-guide/sentinel.html) |
| **Pris en charge par** | [NXLog](https://nxlog.co/community-forum) |
| | |


## <a name="nxlog-linuxaudit-preview"></a>NXLog LinuxAudit (préversion)

| Attribut du connecteur | Description |
| --- | --- |
| **Méthode d’ingestion des données** | [**API du Collecteur de données Azure Sentinel**](connect-rest-api-template.md) |
| **Table(s) Log Analytics** | LinuxAudit_CL |
| **Documentation/<br>instructions d’installation du fournisseur** |  [Guide de l’utilisateur NXLog Azure Sentinel](https://nxlog.co/documentation/nxlog-user-guide/sentinel.html) |
| **Pris en charge par** | [NXLog](https://nxlog.co/community-forum) |
| | |


## <a name="okta-single-sign-on-preview"></a>Okta Single Sign-On (préversion)

| Attribut du connecteur | Description |
| --- | --- |
| **Méthode d’ingestion des données** | [**Azure Functions et l’API REST**](connect-azure-functions-template.md) |
| **Table(s) Log Analytics** | Okta_CL |
| **Code d’application de fonction Azure** | https://aka.ms/sentineloktaazurefunctioncodev2 |
| **Informations d'identification de l’API** | <li>Jeton d'API |
| **Documentation/<br>instructions d’installation du fournisseur** | <li>[Documentation de l’API Okta System Log](https://developer.okta.com/docs/reference/api/system-log/)<li>[Créer un jeton d'API](https://developer.okta.com/docs/guides/create-an-api-token/create-the-token/)<li>[Connecter Okta SSO à Azure Sentinel](#okta-single-sign-on-preview) |
| **Instructions de déploiement du connecteur** | <li>[Déploiement en un clic](connect-azure-functions-template.md?tabs=ARM) avec un modèle Azure Resource Manager (ARM)<li>[Déploiement manuel](connect-azure-functions-template.md?tabs=MPS) |
| **Paramètres d’application** | <li>apiToken<li>workspaceID<li>workspaceKey<li>URI (suit le schéma `https://<OktaDomain>/api/v1/logs?since=`. [Identifiez votre espace de noms de domaine](https://developer.okta.com/docs/reference/api-overview/#url-namespace).) <li>logAnalyticsUri (facultatif) |
| **Pris en charge par** | Microsoft |
| | |


## <a name="onapsis-platform-preview"></a>Onapsis Platform (préversion)

| Attribut du connecteur | Description |
| --- | --- |
| **Méthode d’ingestion des données** | **[Common Event Format (CEF)](connect-common-event-format.md) sur Syslog**, avec une fonction de recherche et d’enrichissement Kusto<br><br>[Configurer Onapsis pour envoyer les journaux CEF au redirecteur de journal](#configure-onapsis-to-send-cef-logs-to-the-log-forwarder) |
| **Table(s) Log Analytics** | CommonSecurityLog |
| **Alias de fonction Kusto :** | incident_lookup |
| **URL de fonction Kusto** | https://aka.ms/sentinel-Onapsis-parser |
| **Pris en charge par** | [Onapsis](https://onapsis.force.com/s/login/) |
| | |

### <a name="configure-onapsis-to-send-cef-logs-to-the-log-forwarder"></a>Configurer Onapsis pour envoyer les journaux CEF au redirecteur de journal

Reportez-vous à l’aide d’Onapsis dans le produit pour configurer le transfert de journaux vers l’agent Log Analytics.

1. Accédez à **Configuration > Intégrations tierces > Alarmes Defend** et suivez les instructions pour Azure Sentinel.
1. Assurez-vous que votre console Onapsis peut accéder à l’ordinateur du redirecteur de journal sur lequel l’agent est installé. Les journaux doivent être envoyés au port 514 à l’aide du protocole TCP.

## <a name="one-identity-safeguard-preview"></a>One Identity Safeguard (préversion)

| Attribut du connecteur | Description |
| --- | --- |
| **Méthode d’ingestion des données** | **[Common Event format (CEF)](connect-common-event-format.md) sur Syslog** |
| **Table(s) Log Analytics** | CommonSecurityLog |
| **Documentation/<br>instructions d’installation du fournisseur** | [Guide d’administration de One Identity Safeguard pour les sessions privilégiées](https://aka.ms/sentinel-cef-oneidentity-forwarding) |
| **Pris en charge par** | [One Identity](https://support.oneidentity.com/) |
| | |



## <a name="oracle-weblogic-server-preview"></a>Oracle WebLogic Server (préversion)

| Attribut du connecteur | Description |
| --- | --- |
| **Méthode d’ingestion des données** | [**Agent Log Analytics : journaux personnalisés**](connect-custom-logs.md) |
| **Table(s) Log Analytics** | OracleWebLogicServer_CL |
| **Alias de fonction Kusto :** | OracleWebLogicServerEvent |
| **URL de fonction Kusto :** | https://aka.ms/sentinel-OracleWebLogicServer-parser |
| **Documentation/<br>instructions d’installation du fournisseur** | [Documentation d’Oracle WebLogic Server](https://docs.oracle.com/en/middleware/standalone/weblogic-server/14.1.1.0/index.html) |
| **Exemple de fichier journal personnalisé :** | server.log |
| **Pris en charge par** | Microsoft |
| | |

## <a name="orca-security-preview"></a>Orca Security (préversion)

| Attribut du connecteur | Description |
| --- | --- |
| **Méthode d’ingestion des données** | [**API du Collecteur de données Azure Sentinel**](connect-rest-api-template.md) |
| **Table(s) Log Analytics** | OrcaAlerts_CL |
| **Documentation/<br>instructions d’installation du fournisseur** | [Intégration d’Azure Sentinel](https://orcasecurity.zendesk.com/hc/en-us/articles/360043941992-Azure-Sentinel-configuration) |
| **Pris en charge par** | [Orca Security](http://support.orca.security/) |
| | |


## <a name="ossec-preview"></a>OSSEC (préversion)

| Attribut du connecteur | Description |
| --- | --- |
| **Méthode d’ingestion des données** | **[Common Event Format (CEF)](connect-common-event-format.md) sur Syslog**, avec un analyseur de fonction Kusto |
| **Table(s) Log Analytics** | CommonSecurityLog |
| **Alias de fonction Kusto :** | OSSECEvent |
| **URL de fonction Kusto :** | https://aka.ms/sentinel-OSSEC-parser |
| **Documentation/<br>instructions d’installation du fournisseur** | [Documentation d’OSSEC](https://www.ossec.net/docs/)<br>[Envoi d’alertes via Syslog](https://www.ossec.net/docs/docs/manual/output/syslog-output.html) |
| **Pris en charge par** | Microsoft |
| | |


## <a name="palo-alto-networks"></a>Palo Alto Networks

| Attribut du connecteur | Description |
| --- | --- |
| **Méthode d’ingestion des données** | **[Common Event format (CEF)](connect-common-event-format.md) sur Syslog** <br><br>Également disponible dans les [solutions Palo Alto Pan-OS et Prisma](sentinel-solutions-catalog.md#palo-alto)|
| **Table(s) Log Analytics** | CommonSecurityLog |
| **Documentation/<br>instructions d’installation du fournisseur** | [Guides de configuration de Common Event format (CEF)](https://aka.ms/asi-syslog-paloalto-forwarding)<br>[Configurer la surveillance Syslog](https://aka.ms/asi-syslog-paloalto-configure) |
| **Pris en charge par** | [Palo Alto Networks](https://www.paloaltonetworks.com/company/contact-support) |
| | |


## <a name="perimeter-81-activity-logs-preview"></a>Perimeter 81 Activity Logs (préversion)

| Attribut du connecteur | Description |
| --- | --- |
| **Méthode d’ingestion des données** | [**API du Collecteur de données Azure Sentinel**](connect-rest-api-template.md) |
| **Table(s) Log Analytics** | Perimeter81_CL |
| **Documentation/<br>instructions d’installation du fournisseur** | [Documentation de Perimeter 81](https://support.perimeter81.com/docs/360012680780) |
| **Pris en charge par** | [Perimeter 81](https://support.perimeter81.com/) |
| | |



## <a name="proofpoint-on-demand-pod-email-security-preview"></a>Proofpoint On Demand (POD) Email Security (préversion)

| Attribut du connecteur | Description |
| --- | --- |
| **Méthode d’ingestion des données** | [**Azure Functions et l’API REST**](connect-azure-functions-template.md) <br><br>Également disponible dans la [solution Proofpoint POD](sentinel-solutions-catalog.md#proofpoint) |
| **Table(s) Log Analytics** | ProofpointPOD_message_CL<br>ProofpointPOD_maillog_CL |
| **Code d’application de fonction Azure** | https://aka.ms/sentinel-proofpointpod-functionapp |
| **Informations d'identification de l’API** | <li>ProofpointClusterID<li>ProofpointToken |
| **Documentation/<br>instructions d’installation du fournisseur** | <li>[Se connecter à Proofpoint Community](https://proofpointcommunities.force.com/community/s/article/How-to-request-a-Community-account-and-gain-full-customer-access?utm_source=login&utm_medium=recommended&utm_campaign=public)<li>[Documentation et instructions de l’API Proofpoint](https://proofpointcommunities.force.com/community/s/article/Proofpoint-on-Demand-Pod-Log-API) |
| **Instructions de déploiement du connecteur** | <li>[Déploiement en un clic](connect-azure-functions-template.md?tabs=ARM) avec un modèle Azure Resource Manager (ARM)<li>[Déploiement manuel](connect-azure-functions-template.md?tabs=MPY) |
| **Alias de fonction Kusto** | ProofpointPOD |
| **Instructions de configuration de l’URL de fonction Kusto/<br>analyseur** | https://aka.ms/sentinel-proofpointpod-parser |
| **Paramètres d’application** | <li>ProofpointClusterID<li>ProofpointToken<li>WorkspaceID<li>WorkspaceKey<li>logAnalyticsUri (facultatif) |
| **Pris en charge par** | Microsoft |
| | |

## <a name="proofpoint-targeted-attack-protection-tap-preview"></a>Proofpoint Targeted Attack Protection (TAP) (préversion)

| Attribut du connecteur | Description |
| --- | --- |
| **Méthode d’ingestion des données** | [**Azure Functions et l’API REST**](connect-azure-functions-template.md) <br><br>Également disponible dans la [solution Proofpoint TAP](sentinel-solutions-catalog.md#proofpoint) |
| **Table(s) Log Analytics** | ProofPointTAPClicksPermitted_CL<br>ProofPointTAPClicksBlocked_CL<br>ProofPointTAPMessagesDelivered_CL<br>ProofPointTAPMessagesBlocked_CL |
| **Code d’application de fonction Azure** | https://aka.ms/sentinelproofpointtapazurefunctioncode |
| **Informations d'identification de l’API** | <li>Nom d’utilisateur d’API<li>Mot de passe d’API |
| **Documentation/<br>instructions d’installation du fournisseur** | <li>[Documentation sur l’API SIEM Proofpoint](https://help.proofpoint.com/Threat_Insight_Dashboard/API_Documentation/SIEM_API) |
| **Instructions de déploiement du connecteur** | <li>[Déploiement en un clic](connect-azure-functions-template.md?tabs=ARM) avec un modèle Azure Resource Manager (ARM)<li>[Déploiement manuel](connect-azure-functions-template.md?tabs=MPS) |
| **Paramètres d’application** | <li>apiUsername<li>apiUsername<li>URI (défini sur `https://tap-api-v2.proofpoint.com/v2/siem/all?format=json&sinceSeconds=300`)<li>WorkspaceID<li>WorkspaceKey<li>logAnalyticsUri (facultatif) |
| **Pris en charge par** | Microsoft |
| | |

## <a name="pulse-connect-secure-preview"></a>Pulse Connect Secure (préversion)

| Attribut du connecteur | Description |
| --- | --- |
| **Méthode d’ingestion des données** | [**Syslog**](connect-syslog.md) |
| **Table(s) Log Analytics** | syslog |
| **Alias de fonction Kusto :** | PulseConnectSecure |
| **URL de fonction Kusto :** | https://aka.ms/sentinelgithubparserspulsesecurevpn |
| **Documentation/<br>instructions d’installation du fournisseur** | [Configuration de Syslog](https://docs.pulsesecure.net/WebHelp/Content/PCS/PCS_AdminGuide_8.2/Configuring%20Syslog.htm) |
| **Pris en charge par** | Microsoft |
| | |


## <a name="qualys-vm-knowledgebase-kb-preview"></a>Base de connaissances (KB) Qualys VM (préversion)

| Attribut du connecteur | Description |
| --- | --- |
| **Méthode d’ingestion des données** | [**Azure Functions et l’API REST**](connect-azure-functions-template.md)<br><br>[Configuration supplémentaire pour la base de connaissances de Qualys VM](#extra-configuration-for-the-qualys-vm-kb) <br><br>Également disponible dans la [solution Qualys VM](sentinel-solutions-catalog.md#qualys)|
| **Table(s) Log Analytics** | QualysKB_CL |
| **Code d’application de fonction Azure** | https://aka.ms/sentinel-qualyskb-functioncode |
| **Informations d'identification de l’API** | <li>Nom d’utilisateur d’API<li>Mot de passe d’API |
| **Documentation/<br>instructions d’installation du fournisseur** | <li>[Guide de l’utilisateur de l’API QualysVM](https://www.qualys.com/docs/qualys-api-vmpc-user-guide.pdf) |
| **Instructions de déploiement du connecteur** | <li>[Déploiement en un clic](connect-azure-functions-template.md?tabs=ARM) avec un modèle Azure Resource Manager (ARM)<li>[Déploiement manuel](connect-azure-functions-template.md?tabs=MPS) |
| **Alias de fonction Kusto** | QualysKB |
| **Instructions de configuration de l’URL de fonction Kusto/<br>analyseur** | https://aka.ms/sentinel-qualyskb-parser |
| **Paramètres d’application** | <li>apiUsername<li>apiUsername<li>URI (par région ; voir la [liste de serveurs d’API](https://www.qualys.com/docs/qualys-api-vmpc-user-guide.pdf#G4.735348). Suit le schéma `https://<API Server>/api/2.0`.<li>WorkspaceID<li>WorkspaceKey<li>filterParameters (ajouter à la fin de l’URI, délimité par `&`. Sans d’espaces.)<li>logAnalyticsUri (facultatif) |
| **Pris en charge par** | Microsoft |
| | |

### <a name="extra-configuration-for-the-qualys-vm-kb"></a>Configuration supplémentaire pour la base de connaissances de Qualys VM

1. Connectez-vous à la console Qualys Vulnerability Management avec un compte administrateur, sélectionnez l’onglet **Utilisateurs**, puis le sous-onglet **Utilisateurs**.
1. Sélectionnez le menu déroulant **Nouveau**, puis **Utilisateurs**.
1. Créez un nom d’utilisateur et un mot de passe pour le compte d’API.
1. Dans l’onglet **Rôles d'utilisateur**, assurez-vous que le rôle de compte est défini sur **Manager** et que l’accès à la **GUI** et à l’**API** est autorisé
1. Déconnectez-vous du compte administrateur et connectez-vous à la console avec les nouvelles informations d’identification de l’API pour validation, puis déconnectez-vous du compte d’API.
1. Reconnectez-vous à la console à l’aide d’un compte administrateur et modifiez les rôles d’utilisateur de comptes d’API, en supprimant l’accès à **GUI**.
1. Enregistrez toutes les modifications.

## <a name="qualys-vulnerability-management-vm-preview"></a>Qualys Vulnerability Management (VM) (préversion)

| Attribut du connecteur | Description |
| --- | --- |
| **Méthode d’ingestion des données** | [**Azure Functions et l’API REST**](connect-azure-functions-template.md)<br><br>[Configuration supplémentaire pour Qualys VM](#extra-configuration-for-the-qualys-vm) <br>[Déploiement manuel : après avoir configuré l’application de fonction](#manual-deployment---after-configuring-the-function-app)|
| **Table(s) Log Analytics** | QualysHostDetection_CL |
| **Code d’application de fonction Azure** | https://aka.ms/sentinelqualysvmazurefunctioncode |
| **Informations d'identification de l’API** | <li>Nom d’utilisateur d’API<li>Mot de passe d’API |
| **Documentation/<br>instructions d’installation du fournisseur** | <li>[Guide de l’utilisateur de l’API QualysVM](https://www.qualys.com/docs/qualys-api-vmpc-user-guide.pdf) |
| **Instructions de déploiement du connecteur** | <li>[Déploiement en un clic](connect-azure-functions-template.md?tabs=ARM) avec un modèle Azure Resource Manager (ARM)<li>[Déploiement manuel](connect-azure-functions-template.md?tabs=MPS) |
| **Paramètres d’application** | <li>apiUsername<li>apiUsername<li>URI (par région ; voir la [liste de serveurs d’API](https://www.qualys.com/docs/qualys-api-vmpc-user-guide.pdf#G4.735348). Suit le schéma `https://<API Server>/api/2.0/fo/asset/host/vm/detection/?action=list&vm_processed_after=`.<li>WorkspaceID<li>WorkspaceKey<li>filterParameters (ajouter à la fin de l’URI, délimité par `&`. Sans d’espaces.)<li>timeInterval (défini sur 5. Si vous modifiez, modifiez le déclencheur de retardateur d’application de fonction en conséquence.)<li>logAnalyticsUri (facultatif) |
| **Pris en charge par** | Microsoft |
| | |

### <a name="extra-configuration-for-the-qualys-vm"></a>Configuration supplémentaire pour Qualys VM

1. Connectez-vous à la console Qualys Vulnerability Management avec un compte administrateur, sélectionnez l’onglet **Utilisateurs**, puis le sous-onglet **Utilisateurs**.
1. Sélectionnez le menu déroulant **Nouveau**, puis **Utilisateurs**.
1. Créez un nom d’utilisateur et un mot de passe pour le compte d’API.
1. Dans l’onglet **Rôles d'utilisateur**, assurez-vous que le rôle de compte est défini sur **Manager** et que l’accès à la **GUI** et à l’**API** est autorisé
1. Déconnectez-vous du compte administrateur et connectez-vous à la console avec les nouvelles informations d’identification de l’API pour validation, puis déconnectez-vous du compte d’API.
1. Reconnectez-vous à la console à l’aide d’un compte administrateur et modifiez les rôles d’utilisateur de comptes d’API, en supprimant l’accès à **GUI**.
1. Enregistrez toutes les modifications.

### <a name="manual-deployment---after-configuring-the-function-app"></a>Déploiement manuel : après avoir configuré l’application de fonction

**Configurer le fichier host.json**

En raison de la quantité potentiellement importante des données de détection de l’hôte Qualys ingérée, le temps d’exécution peut dépasser la valeur de délai d’expiration d’application de fonction par défaut de cinq minutes. Augmentez la durée du délai d’expiration par défaut à un maximum de dix minutes, sous le plan de consommation, afin de laisser plus de temps à l’exécution de l’application de fonction.

1. Dans l’application de fonction, sélectionnez le nom de l’application de fonction, puis la page **Éditeur App Service**.
1. Sélectionnez **Go** pour ouvrir l’éditeur, puis sélectionnez le fichier **host.json** sous le répertoire **wwwroot**.
1. Ajoutez la ligne `"functionTimeout": "00:10:00",` au-dessus de la ligne `managedDependancy`.
1. Vérifiez que **ENREGISTRÉ** apparaît en haut à droite de l’éditeur, puis quittez l’éditeur.

Si une durée de délai d’expiration plus longue est requise, envisagez une mise à niveau vers un [Plan App service](../azure-functions/functions-scale.md).



## <a name="salesforce-service-cloud-preview"></a>Salesforce Service Cloud (préversion)

| Attribut du connecteur | Description |
| --- | --- |
| **Méthode d’ingestion des données** | [**Azure Functions et l’API REST**](connect-azure-functions-template.md) |
| **Table(s) Log Analytics** | SalesforceServiceCloud_CL |
| **Code d’application de fonction Azure** | https://aka.ms/sentinel-SalesforceServiceCloud-functionapp |
| **Informations d'identification de l’API** | <li>Nom d’utilisateur de l’API Salesforce<li>Mot de passe de l’API Salesforce<li>Jeton de sécurité de Salesforce<li>Clé de consommateur de Salesforce<li>Secret de consommateur de Salesforce |
| **Documentation/<br>instructions d’installation du fournisseur** | [Guide du développeur de l’API REST Salesforce](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/quickstart.htm)<br>Sous **Configurer l’autorisation**, utilisez la méthode **ID de session** au lieu de OAuth. |
| **Instructions de déploiement du connecteur** | <li>[Déploiement en un clic](connect-azure-functions-template.md?tabs=ARM) avec un modèle Azure Resource Manager (ARM)<li>[Déploiement manuel](connect-azure-functions-template.md?tabs=MPY) |
| **Alias de fonction Kusto** | SalesforceServiceCloud |
| **Instructions de configuration de l’URL de fonction Kusto/<br>analyseur** | https://aka.ms/sentinel-SalesforceServiceCloud-parser |
| **Paramètres d’application** | <li>SalesforceUser<li>SalesforcePass<li>SalesforceSecurityToken<li>SalesforceConsumerKey<li>SalesforceConsumerSecret<li>WorkspaceID<li>WorkspaceKey<li>logAnalyticsUri (facultatif) |
| **Pris en charge par** | Microsoft |
| | |


## <a name="security-events-via-legacy-agent-windows"></a>Événements de sécurité via l’ancien agent (Windows)

| Attribut du connecteur | Description |
| --- | --- |
| **Méthode d’ingestion des données** | **Intégration de service à service Azure: <br>[connexions basées sur l’agent Log Analytics](connect-azure-windows-microsoft-services.md?tabs=LAA#windows-agent-based-connections)** |
| **Table(s) Log Analytics** | SecurityEvents |
| **Pris en charge par** | Microsoft |
| | |


Pour plus d’informations, consultez [Configuration du classeur de protocoles non sécurisés](./get-visibility.md#use-built-in-workbooks).

Voir aussi : [**Événements de sécurité Windows via agent Azure Monitor**](#windows-security-events-via-ama) connecteur basé sur l’agent Azure Monitor (AMA)

[Configurez le **connecteur Événements de sécurité / Événements de sécurité Windows** pour la **détection de connexion RDP anormale**](#configure-the-security-events--windows-security-events-connector-for-anomalous-rdp-login-detection).

## <a name="sentinelone-preview"></a>SentinelOne (préversion)

| Attribut du connecteur | Description |
| --- | --- |
| **Méthode d’ingestion des données** | [**Azure Functions et l’API REST**](connect-azure-functions-template.md) <br><br>[Configuration supplémentaire pour SentinelOne](#extra-configuration-for-sentinelone)|
| **Table(s) Log Analytics** | SentinelOne_CL |
| **Code d’application de fonction Azure** | https://aka.ms/sentinel-SentinelOneAPI-functionapp |
| **Informations d'identification de l’API** | <li>SentinelOneAPIToken<li>SentinelOneUrl (`https://<SOneInstanceDomain>.sentinelone.net`) |
| **Documentation/<br>instructions d’installation du fournisseur** | <li>https://`<SOneInstanceDomain>`.sentinelone.net/api-doc/overview<li>Voir les instructions ci-dessous |
| **Instructions de déploiement du connecteur** | <li>[Déploiement en un clic](connect-azure-functions-template.md?tabs=ARM) avec un modèle Azure Resource Manager (ARM)<li>[Déploiement manuel](connect-azure-functions-template.md?tabs=MPY) |
| **Alias de fonction Kusto** | SentinelOne |
| **Instructions de configuration de l’URL de fonction Kusto/<br>analyseur** | https://aka.ms/sentinel-SentinelOneAPI-parser |
| **Paramètres d’application** | <li>SentinelOneAPIToken<li>SentinelOneUrl<li>WorkspaceID<li>WorkspaceKey<li>logAnalyticsUri (facultatif) |
| **Pris en charge par** | Microsoft |
| | |

### <a name="extra-configuration-for-sentinelone"></a>Configuration supplémentaire pour SentinelOne

Suivez les instructions pour obtenir les informations d'identification.

1. Connectez-vous à la console de gestion de SentinelOne avec les informations d’identification de l’utilisateur administrateur.
1. Dans la console de gestion, sélectionnez **Paramètres**.
1. Dans la vue **PARAMÈTRES**, sélectionnez **UTILISATEURS**
1. Sélectionnez **Nouvel utilisateur**.
1. Entrez les informations pour le nouvel utilisateur de la console.
1. Dans Rôle, sélectionnez **Administrateur**.
1. Sélectionnez **ENREGISTRER**
1. Enregistrez les informations d’identification du nouvel utilisateur à utiliser dans le connecteur de données.



## <a name="sonicwall-firewall-preview"></a>SonicWall Firewall (préversion)

| Attribut du connecteur | Description |
| --- | --- |
| **Méthode d’ingestion des données** | **[Common Event format (CEF)](connect-common-event-format.md) sur Syslog** |
| **Table(s) Log Analytics** | CommonSecurityLog |
| **Documentation/<br>instructions d’installation du fournisseur** | [Journal > Syslog](http://help.sonicwall.com/help/sw/eng/7020/26/2/3/content/Log_Syslog.120.2.htm)<br>Sélectionnez installation local4 et ArcSight comme format Syslog.  |
| **Pris en charge par** | [SonicWall](https://www.sonicwall.com/support/) |
| | | 


## <a name="sophos-cloud-optix-preview"></a>Sophos Cloud Optix (préversion)

| Attribut du connecteur | Description |
| --- | --- |
| **Méthode d’ingestion des données** | [**API du Collecteur de données Azure Sentinel**](connect-rest-api-template.md) |
| **Table(s) Log Analytics** | SophosCloudOptix_CL |
| **Documentation/<br>instructions d’installation du fournisseur** | [Intégrer à Azure Sentinel](https://docs.sophos.com/pcg/optix/help/en-us/pcg/optix/tasks/IntegrateAzureSentinel.html), en ignorant la première étape.<br>[Exemples de requêtes Sophos](https://docs.sophos.com/pcg/optix/help/en-us/pcg/optix/concepts/ExampleAzureSentinelQueries.html) |
| **Pris en charge par** | [Sophos](https://secure2.sophos.com/en-us/support.aspx) |
| | |




## <a name="sophos-xg-firewall-preview"></a>Sophos XG Firewall (préversion)

| Attribut du connecteur | Description |
| --- | --- |
| **Méthode d’ingestion des données** | [**Syslog**](connect-syslog.md) |
| **Table(s) Log Analytics** | syslog |
| **Alias de fonction Kusto :** | SophosXGFirewall |
| **URL de fonction Kusto :** | https://aka.ms/sentinelgithubparserssophosfirewallxg |
| **Documentation/<br>instructions d’installation du fournisseur** | [Ajouter un serveur Syslog](https://docs.sophos.com/nsg/sophos-firewall/18.5/Help/en-us/webhelp/onlinehelp/nsg/tasks/SyslogServerAdd.html) |
| **Pris en charge par** | Microsoft |
| | |

## <a name="squadra-technologies-secrmm"></a>Squadra Technologies secRMM

| Attribut du connecteur | Description |
| --- | --- |
| **Méthode d’ingestion des données** | [**API du Collecteur de données Azure Sentinel**](connect-rest-api-template.md) |
| **Table(s) Log Analytics** | secRMM_CL |
| **Documentation/<br>instructions d’installation du fournisseur** | [Guide de l’administrateur d’Azure Sentinel secRMM](https://www.squadratechnologies.com/StaticContent/ProductDownload/secRMM/9.9.0.0/secRMMAzureSentinelAdministratorGuide.pdf) |
| **Pris en charge par** | [Squadra Technologies](https://www.squadratechnologies.com/Contact.aspx) |
| | |


## <a name="squid-proxy-preview"></a>Squid Proxy (préversion)

| Attribut du connecteur | Description |
| --- | --- |
| **Méthode d’ingestion des données** | [**Agent Log Analytics : journaux personnalisés**](connect-custom-logs.md) |
| **Table(s) Log Analytics** | SquidProxy_CL |
| **Alias de fonction Kusto :** | SquidProxy |
| **URL de fonction Kusto** | https://aka.ms/sentinel-squidproxy-parser |
| **Exemple de fichier journal personnalisé :** | access.log ou cache.log |
| **Pris en charge par** | Microsoft |
| | |

## <a name="symantec-integrated-cyber-defense-exchange-icdx"></a>Symantec Integrated Cyber Defense Exchange (ICDx)

| Attribut du connecteur | Description |
| --- | --- |
| **Méthode d’ingestion des données** | [**API du Collecteur de données Azure Sentinel**](connect-rest-api-template.md) |
| **Table(s) Log Analytics** | SymantecICDx_CL |
| **Documentation/<br>instructions d’installation du fournisseur** | [Configuration des redirecteurs Microsoft Azure Sentinel (Log Analytics)](https://techdocs.broadcom.com/us/en/symantec-security-software/integrated-cyber-defense/integrated-cyber-defense-exchange/1-4-3/Forwarders/configuring-forwarders-v131944722-d2707e17438.html) |
| **Pris en charge par** | [Broadcom Symantec](https://support.broadcom.com/security) |
| | |


## <a name="symantec-proxysg-preview"></a>Symantec ProxySG (préversion)

| Attribut du connecteur | Description |
| --- | --- |
| **Méthode d’ingestion des données** | [**Syslog**](connect-syslog.md) |
| **Table(s) Log Analytics** | syslog |
| **Alias de fonction Kusto :** | SymantecProxySG |
| **URL de fonction Kusto :** | https://aka.ms/sentinelgithubparserssymantecproxysg |
| **Documentation/<br>instructions d’installation du fournisseur** | [Envoi de journaux d’accès à un serveur Syslog](https://knowledge.broadcom.com/external/article/166529/sending-access-logs-to-a-syslog-server.html) |
| **Pris en charge par** | Microsoft |
| | |


## <a name="symantec-vip-preview"></a>Symantec VIP (préversion)

| Attribut du connecteur | Description |
| --- | --- |
| **Méthode d’ingestion des données** | [**Syslog**](connect-syslog.md) |
| **Table(s) Log Analytics** | syslog |
| **Alias de fonction Kusto :** | SymantecVIP |
| **URL de fonction Kusto :** | https://aka.ms/sentinelgithubparserssymantecvip |
| **Documentation/<br>instructions d’installation du fournisseur** | [Configuration de Syslog](https://help.symantec.com/cs/VIP_EG_INSTALL_CONFIG/VIP/v134652108_v128483142/Configuring-syslog?locale=EN_US) |
| **Pris en charge par** | Microsoft |
| | |



## <a name="thycotic-secret-server-preview"></a>Thycotic Secret Server (préversion)

| Attribut du connecteur | Description |
| --- | --- |
| **Méthode d’ingestion des données** | **[Common Event format (CEF)](connect-common-event-format.md) sur Syslog** |
| **Table(s) Log Analytics** | CommonSecurityLog |
| **Documentation/<br>instructions d’installation du fournisseur** | [Journalisation Syslog/CEF sécurisée](https://thy.center/ss/link/syslog) |
| **Pris en charge par** | [Thycotic](https://thycotic.force.com/support/s/) |
| | |


## <a name="trend-micro-deep-security"></a>Trend Micro Deep Security

| Attribut du connecteur | Description |
| --- | --- |
| **Méthode d’ingestion des données** | **[Common Event Format (CEF)](connect-common-event-format.md) sur Syslog**, avec un analyseur de fonction Kusto |
| **Table(s) Log Analytics** | CommonSecurityLog |
| **Alias de fonction Kusto :** | TrendMicroDeepSecurity |
| **URL de fonction Kusto** | https://aka.ms/TrendMicroDeepSecurityFunction |
| **Documentation/<br>instructions d’installation du fournisseur** | [Transférer des événements Deep Security à un serveur Syslog ou SIEM](https://aka.ms/Sentinel-trendMicro-connectorInstructions) |
| **Pris en charge par** | [Trend Micro](https://success.trendmicro.com/technical-support) |
| | |

## <a name="trend-micro-tippingpoint-preview"></a>Trend Micro TippingPoint (préversion)

| Attribut du connecteur | Description |
| --- | --- |
| **Méthode d’ingestion des données** | **[Common Event Format (CEF)](connect-common-event-format.md) sur Syslog**, avec un analyseur de fonction Kusto |
| **Table(s) Log Analytics** | CommonSecurityLog |
| **Alias de fonction Kusto :** | TrendMicroTippingPoint |
| **URL de fonction Kusto** | https://aka.ms/sentinel-trendmicrotippingpoint-function |
| **Documentation/<br>instructions d’installation du fournisseur** | Envoyer des messages Syslog au format ArcSight CEF format v 4.2. |
| **Pris en charge par** | [Trend Micro](https://success.trendmicro.com/technical-support) |
| | |

## <a name="trend-micro-vision-one-xdr-preview"></a>Trend Micro Vision One (XDR) (préversion)

| Attribut du connecteur | Description |
| --- | --- |
| **Méthode d’ingestion des données** | [**Azure Functions et l’API REST**](connect-azure-functions-template.md) |
| **Table(s) Log Analytics** | TrendMicro_XDR_CL |
| **Informations d'identification de l’API** | <li>Jeton d'API |
| **Documentation/<br>instructions d’installation du fournisseur** | <li>[API Trend Micro Vision One](https://automation.trendmicro.com/xdr/home)<li>[Obtention de clés d’API pour l’accès tiers](https://docs.trendmicro.com/en-us/enterprise/trend-micro-xdr-help/ObtainingAPIKeys) |
| **Instructions de déploiement du connecteur** | [Déploiement en un clic](connect-azure-functions-template.md?tabs=ARM) avec un modèle Azure Resource Manager (ARM) |
| **Pris en charge par** | [Trend Micro](https://success.trendmicro.com/technical-support) |
| | |



## <a name="vmware-carbon-black-endpoint-standard-preview"></a>VMware Carbon Black Endpoint Standard (préversion)

| Attribut du connecteur | Description |
| --- | --- |
| **Méthode d’ingestion des données** | [**Azure Functions et l’API REST**](connect-azure-functions-template.md) |
| **Table(s) Log Analytics** | CarbonBlackEvents_CL<br>CarbonBlackAuditLogs_CL<br>CarbonBlackNotifications_CL |
| **Code d’application de fonction Azure** | https://aka.ms/sentinelcarbonblackazurefunctioncode |
| **Informations d'identification de l’API** | **Niveau d'accès API** (pour les journaux d’*audit* et des *événements*) :<li>ID d’API<li>Clé d’API<br><br>**Niveau d’accès SIEM** (pour les événements de *notification*) :<li>ID d’API SIEM<li>Clé d’API SIEM |
| **Documentation/<br>instructions d’installation du fournisseur** | <li>[Documentation de l’API Carbon Black](https://developer.carbonblack.com/reference/carbon-black-cloud/cb-defense/latest/rest-api/)<li>[Création d’une clé d’API](https://developer.carbonblack.com/reference/carbon-black-cloud/authentication/#creating-an-api-key) |
| **Instructions de déploiement du connecteur** | <li>[Déploiement en un clic](connect-azure-functions-template.md?tabs=ARM) avec un modèle Azure Resource Manager (ARM)<li>[Déploiement manuel](connect-azure-functions-template.md?tabs=MPS) |
| **Paramètres d’application** | <li>apiId<li>apiKey<li>WorkspaceID<li>WorkspaceKey<li>URI (par région ; [voir la liste des options](https://community.carbonblack.com/t5/Knowledge-Base/PSC-What-URLs-are-used-to-access-the-APIs/ta-p/67346). Suit le schéma : `https://<API URL>.conferdeploy.net`.)<li>timeInterval (défini sur 5)<li>SIEMapiId (en cas d’ingestion d’événements de *notification*)<li>SIEMapiKey (en cas d’ingestion d’événements de *notification*)<li>logAnalyticsUri (facultatif) |
| **Pris en charge par** | Microsoft |
| | |

## <a name="vmware-esxi-preview"></a>VMware ESXi (préversion)

| Attribut du connecteur | Description |
| --- | --- |
| **Méthode d’ingestion des données** | [**Syslog**](connect-syslog.md) |
| **Table(s) Log Analytics** | syslog |
| **Alias de fonction Kusto :** | VMwareESXi |
| **URL de fonction Kusto :** | https://aka.ms/sentinel-vmwareesxi-parser |
| **Documentation/<br>instructions d’installation du fournisseur** | [Activation de Syslog sur ESXi 3.5 et 4.x](https://kb.vmware.com/s/article/1016621)<br>[Configurer Syslog sur les hôtes ESXi](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.monitoring.doc/GUID-9F67DB52-F469-451F-B6C8-DAE8D95976E7.html) |
| **Pris en charge par** | Microsoft |
| | |

## <a name="watchguard-firebox-preview"></a>WatchGuard Firebox (préversion)

| Attribut du connecteur | Description |
| --- | --- |
| **Méthode d’ingestion des données** | [**Syslog**](connect-syslog.md) |
| **Table(s) Log Analytics** | syslog |
| **Alias de fonction Kusto :** | WatchGuardFirebox |
| **URL de fonction Kusto :** | https://aka.ms/sentinel-watchguardfirebox-parser |
| **Documentation/<br>instructions d’installation du fournisseur** | [Guide d’intégration de Microsoft Azure Sentinel](https://www.watchguard.com/help/docs/help-center/en-US/Content/Integration-Guides/General/Microsoft%20Azure%20Sentinel.html) |
| **Pris en charge par** | [WatchGuard Technologies](https://www.watchguard.com/wgrd-support/overview) |
| | |

## <a name="wirex-network-forensics-platform-preview"></a>WireX Network Forensics Platform (préversion)

| Attribut du connecteur | Description |
| --- | --- |
| **Méthode d’ingestion des données** | **[Common Event format (CEF)](connect-common-event-format.md) sur Syslog** |
| **Table(s) Log Analytics** | CommonSecurityLog |
| **Documentation/<br>instructions d’installation du fournisseur** | Contactez le [support WireX](https://wirexsystems.com/contact-us/) pour configurer votre NFP de sorte qu’elle envoie les messages Syslog au format CEF. |
| **Pris en charge par** | [WireX Systems](mailto:support@wirexsystems.com) |
| | |


## <a name="windows-dns-server-preview"></a>Serveur DNS Windows (préversion)

| Attribut du connecteur | Description |
| --- | --- |
| **Méthode d’ingestion des données** | **Intégration de service à service Azure: <br>[connexions basées sur l’agent Log Analytics](connect-azure-windows-microsoft-services.md?tabs=LAA#windows-agent-based-connections)** |
| **Table(s) Log Analytics** | DnsEvents<br>DnsInventory |
| **Pris en charge par** | Microsoft |
| | |

## <a name="windows-forwarded-events-preview"></a>Événements transférés par Windows (préversion)

| Attribut du connecteur | Description |
| --- | --- |
| **Méthode d’ingestion des données** | **Intégration de service à service Azure : <br>[connexions basées sur l’agent Azure Monitor](connect-azure-windows-microsoft-services.md?tabs=AMA#windows-agent-based-connections)**<br><br>[instructions supplémentaires pour le déploiement du connecteur Événements transférés par Windows](#additional-instructions-for-deploying-the-windows-forwarded-events-connector) |
| **Composants requis** | La Collection d’événements Windows (WEC) doit être activée et en cours d’exécution.<br>Installez l’agent Azure Monitor sur la machine WEC. |
| **Préfixe des requêtes xPath** | « ForwardedEvents!* » |
| **Table(s) Log Analytics** | WindowsEvents |
| **Pris en charge par** | Microsoft |
| | |

### <a name="additional-instructions-for-deploying-the-windows-forwarded-events-connector"></a>Instructions supplémentaires pour le déploiement du connecteur Événements transférés par Windows

Nous vous recommandons d’installer les analyseurs [ASIM (Azure Sentinel Information Model)](normalization.md) pour garantir une prise en charge complète de la normalisation des données. Vous pouvez déployer ces analyseurs à partir du [Référentiel GitHub Azure Sentinel](https://github.com/Azure/Azure-Sentinel/tree/master/Parsers/ASim%20WindowsEvent) à l’aide du bouton **Déployer sur Azure**.

## <a name="windows-firewall"></a>Pare-feu Windows

| Attribut du connecteur | Description |
| --- | --- |
| **Méthode d’ingestion des données** | **Intégration de service à service Azure: <br>[connexions basées sur l’agent Log Analytics](connect-azure-windows-microsoft-services.md?tabs=LAA#windows-agent-based-connections)** |
| **Table(s) Log Analytics** | WindowsFirewall |
| **Pris en charge par** | Microsoft |
| | |

## <a name="windows-security-events-via-ama"></a>Événements de sécurité Windows via AMA

| Attribut du connecteur | Description |
| --- | --- |
| **Méthode d’ingestion des données** | **Intégration de service à service Azure : <br>[connexions basées sur l’agent Azure Monitor](connect-azure-windows-microsoft-services.md?tabs=AMA#windows-agent-based-connections)** |
| **Préfixe des requêtes xPath** | « Sécurité* » |
| **Table(s) Log Analytics** | SecurityEvents |
| **Pris en charge par** | Microsoft |
| | |


Voir aussi : connecteur [**Événements de sécurité via l’ancien agent**](#security-events-via-legacy-agent-windows).

### <a name="configure-the-security-events--windows-security-events-connector-for-anomalous-rdp-login-detection"></a>Configurer le connecteur d’événements de sécurité / d’événements de sécurité Windows pour la détection de connexion RDP anormale

> [!IMPORTANT]
> La détection de connexion Remote Desktop Protocol (RDP) anormale est actuellement en préversion publique.
> Cette fonctionnalité est fournie sans contrat de niveau de service et est déconseillée pour les charges de travail de production.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure Sentinel peut appliquer du Machine Learning (ML) aux données Événements de sécurité pour identifier une activité de connexion RDP anormale. Il s’agit entre autres des scénarios suivants :

- **IP inhabituelle** : l’adresse IP a rarement ou n’a jamais été observée au cours des 30 derniers jours

- **Emplacement géographique inhabituel** : l’adresse IP, la ville, le pays et l’ASN ont rarement ou n’ont jamais été observés au cours des 30 derniers jours

- **Nouvel utilisateur** : un nouvel utilisateur se connecte à partir d’une adresse IP et d’un emplacement géographique, dont les deux ou l’un des deux n’étaient pas censés être vus sur la base des données des 30 jours précédents.

**Instructions de configuration**

1. Vous devez collecter les données de connexion RDP (ID d’événement 4624) par le biais du connecteur de données des **événements de sécurité** ou des **événements de sécurité Windows**. Assurez-vous que vous avez sélectionné un [jeu d’événements](windows-security-event-id-reference.md) en plus de « Aucun », ou que vous avez créé une règle de collecte de données qui comprend cet ID d’événement, afin de transmettre en continu dans Azure Sentinel.

1. Dans le portail Azure Sentinel, cliquez sur **Analytics**, puis sur l’onglet **Modèles de règles**. Choisissez la règle **Détection de connexion RDP anormale (préversion)** et déplacez le curseur **État** sur **Activé**.

    > [!NOTE]
    > Comme l’algorithme de Machine Learning nécessite 30 jours de données pour établir un profil de base du comportement de l’utilisateur, vous devez autoriser la collecte de 30 jours de données d’événements de sécurité Windows avant que tout incident puisse être détecté.

## <a name="workplace-from-facebook-preview"></a>Workplace from Facebook (pré)

| Attribut du connecteur | Description |
| --- | --- |
| **Méthode d’ingestion des données** | [**Azure Functions et l’API REST**](connect-azure-functions-template.md)<br><br>[Configurer des webhooks](#configure-webhooks) <br>[Ajouter l’URL de rappel à la configuration du webhook](#add-callback-url-to-webhook-configuration)|
| **Table(s) Log Analytics** | Workplace_Facebook_CL |
| **Code d’application de fonction Azure** | https://aka.ms/sentinel-WorkplaceFacebook-functionapp |
| **Informations d'identification de l’API** | <li>WorkplaceAppSecret<li>WorkplaceVerifyToken |
| **Documentation/<br>instructions d’installation du fournisseur** | <li>[Configurer des webhooks](https://developers.facebook.com/docs/workplace/reference/webhooks)<li>[Configurer les autorisations](https://developers.facebook.com/docs/workplace/reference/permissions) |
| **Instructions de déploiement du connecteur** | <li>[Déploiement en un clic](connect-azure-functions-template.md?tabs=ARM) avec un modèle Azure Resource Manager (ARM)<li>[Déploiement manuel](connect-azure-functions-template.md?tabs=MPY) |
| **Alias de fonction Kusto** | Workplace_Facebook |
| **Instructions de configuration de l’URL de fonction Kusto/<br>analyseur** | https://aka.ms/sentinel-WorkplaceFacebook-parser |
| **Paramètres d’application** | <li>WorkplaceAppSecret<li>WorkplaceVerifyToken<li>WorkspaceID<li>WorkspaceKey<li>logAnalyticsUri (facultatif) |
| **Pris en charge par** | Microsoft |
| | |

### <a name="configure-webhooks"></a>Configurer des webhooks

1. Connectez-vous à l’espace de travail avec les informations d’identification de l’utilisateur administrateur.
1. Dans le panneau Administrateur, sélectionnez **Intégrations**.
1. Dans la vue **Toutes les intégrations**, sélectionnez **Créer une intégration personnalisée**.
1. Entrez un nom et une description, puis sélectionnez **Créer**.
1. Dans le panneau **Détails de l’intégration**, affichez le **Secret d’application** et copiez-le.
1. Dans le panneau **Autorisations d’intégration**, définissez toutes les autorisations de lecture. Pour plus d’informations, consultez la [page d’autorisation](https://developers.facebook.com/docs/workplace/reference/permissions).

### <a name="add-callback-url-to-webhook-configuration"></a>Ajouter l’URL de rappel à la configuration du webhook

1. Ouvrez la page de votre application de fonction, accédez à la liste **Fonctions**, sélectionnez **Obtenir l’URL de fonction** et copiez-la.
1. Revenez à **Workplace from Facebook**. Dans le panneau **Configurer des webhooks**, définissez, dans chaque onglet, l’**URL de rappel** sur l’URL de fonction que vous avez copiée à l’étape précédente, ainsi que **Vérifier le jeton** sur la valeur que vous avez reçue lors du déploiement automatique ou entrée lors du déploiement manuel.
1. Sélectionnez **Enregistrer**.



## <a name="zimperium-mobile-thread-defense-preview"></a>Zimperium Mobile Thread Defense (préversion)

Le connecteur de données Zimperium Mobile Threat Defense connecte le journal des menaces Zimperium à Azure Sentinel pour afficher des tableaux de bord, créer des alertes personnalisées et améliorer l’investigation. Ce connecteur vous donne plus d’informations sur le paysage des menaces mobiles de votre organisation et améliore vos capacités d’opération de sécurité.

Pour plus d’informations, consultez [Connecter Zimperium à Azure Sentinel](#zimperium-mobile-thread-defense-preview).

| Attribut du connecteur | Description |
| --- | --- |
| **Méthode d’ingestion des données** | [**API du Collecteur de données Azure Sentinel**](connect-rest-api-template.md)<br><br>[Configurer et connecter Zimperium MTD](#configure-and-connect-zimperium-mtd) |
| **Table(s) Log Analytics** | ZimperiumThreatLog_CL<br>ZimperiumMitigationLog_CL |
| **Documentation/<br>instructions d’installation du fournisseur** | [Portail du service clientèle de Zimperium](https://support.zimperium.com/) (connexion requise) |
| **Pris en charge par** | [Zimperium](https://www.zimperium.com/support) |
| | |

### <a name="configure-and-connect-zimperium-mtd"></a>Configurer et connecter Zimperium MTD

1. Dans zConsole, sélectionnez **Gérer** dans la barre de navigation.
1. Sélectionnez l’onglet **Intégrations**.
1. Sélectionnez le bouton **Rapports sur les menaces**, puis sur le bouton **Ajouter des intégrations**.
1. Créez l’intégration :
    1. Dans les intégrations disponibles, sélectionnez **Microsoft Azure Sentinel**.
    1. Entrez votre *ID d’espace de travail* et votre *clé primaire*, puis sélectionnez **Suivant**.
    1. Donnez un nom à votre intégration Azure Sentinel.
    1. Sélectionnez un **Niveau de filtre** pour les données de menace à transmettre à Azure Sentinel.
    1. Sélectionnez **Terminer**.

## <a name="zoom-reports-preview"></a>Zoom Reports (préversion)

| Attribut du connecteur | Description |
| --- | --- |
| **Méthode d’ingestion des données** | [**Azure Functions et l’API REST**](connect-azure-functions-template.md) |
| **Table(s) Log Analytics** | Zoom_CL |
| **Code d’application de fonction Azure** | https://aka.ms/sentinel-ZoomAPI-functionapp |
| **Informations d'identification de l’API** | <li>ZoomApiKey<li>ZoomApiSecret |
| **Documentation/<br>instructions d’installation du fournisseur** | <li>[Obtenir les informations d’identification à l’aide de JWT avec Zoom](https://marketplace.zoom.us/docs/guides/auth/jwt) |
| **Instructions de déploiement du connecteur** | <li>[Déploiement en un clic](connect-azure-functions-template.md?tabs=ARM) avec un modèle Azure Resource Manager (ARM)<li>[Déploiement manuel](connect-azure-functions-template.md?tabs=MPY) |
| **Alias de fonction Kusto** | Zoom |
| **Instructions de configuration de l’URL de fonction Kusto/<br>analyseur** | https://aka.ms/sentinel-ZoomAPI-parser |
| **Paramètres d’application** | <li>ZoomApiKey<li>ZoomApiSecret<li>WorkspaceID<li>WorkspaceKey<li>logAnalyticsUri (facultatif) |
| **Pris en charge par** | Microsoft |
| | |

## <a name="zscaler"></a>Zscaler

| Attribut du connecteur | Description |
| --- | --- |
| **Méthode d’ingestion des données** | **[Common Event format (CEF)](connect-common-event-format.md) sur Syslog** |
| **Table(s) Log Analytics** | CommonSecurityLog |
| **Documentation/<br>instructions d’installation du fournisseur** | [Guide de déploiement de Zscaler et Microsoft Azure Sentinel](https://aka.ms/ZscalerCEFInstructions) |
| **Pris en charge par** | [Zscaler](https://help.zscaler.com/submit-ticket-links) |
| | |


## <a name="zscaler-private-access-zpa-preview"></a>Zscaler Private Access (ZPA) (préversion)


| Attribut du connecteur | Description |
| --- | --- |
| **Méthode d’ingestion des données** | [**Agent Log Analytics : journaux personnalisés**](connect-custom-logs.md)<br><br>[Configuration supplémentaire pour Zscaler Private Access](#extra-configuration-for-zscaler-private-access) |
| **Table(s) Log Analytics** | ZPA_CL |
| **Alias de fonction Kusto :** | ZPAEvent |
| **URL de fonction Kusto** | https://aka.ms/sentinel-zscalerprivateaccess-parser |
| **Documentation/<br>instructions d’installation du fournisseur** | [Documentation de Zscaler Private Access](https://help.zscaler.com/zpa)<br>Voir également ci-dessous |
| **Pris en charge par** | Microsoft |
| | |

### <a name="extra-configuration-for-zscaler-private-access"></a>Configuration supplémentaire pour Zscaler Private Access

Suivez les étapes de configuration ci-dessous pour accéder aux journaux Zscaler Private Access dans Azure Sentinel. Pour plus d’informations, consultez la [Documentation Azure Monitor](../azure-monitor/agents/data-sources-json.md). Les journaux Zscaler Private Access sont fournis par le biais du service de streaming des journaux (LSS). Pour plus d’informations, consultez la [Documentation de LSS](https://help.zscaler.com/zpa/about-log-streaming-service).

1. Configurez des [Récepteurs de journaux](https://help.zscaler.com/zpa/configuring-log-receiver). Lors de la configuration d’un récepteur de journaux, choisissez **JSON** comme **Modèle de journal**.
1. Téléchargez le fichier config [zpa.conf](https://aka.ms/sentinel-zscalerprivateaccess-conf).

    ```bash
    wget -v https://aka.ms/sentinel-zscalerprivateaccess-conf -O zpa.conf
    ```

1. Connectez-vous au serveur sur lequel vous avez installé l’agent Azure Log Analytics.
1. Copiez zpa.conf dans le dossier /etc/opt/microsoft/omsagent/`workspace_id`/conf/omsagent.d/.
1. Modifiez zpa.conf comme suit :
    1. Spécifiez le port que vous avez défini auquel vos récepteurs de journaux Zscaler doivent transférer les journaux (ligne 4)
    1. Remplacez `workspace_id` par la valeur réelle d’ID de votre espace de travail (lignes 14, 15, 16, 19)
1. Enregistrez les modifications et redémarrez l’agent Azure Log Analytics pour le service Linux à l’aide de la commande suivante :

    ```bash
    sudo /opt/microsoft/omsagent/bin/service_control restart
    ```
Vous pouvez trouver la valeur d’ID de votre espace de travail sur la page du connecteur ZScaler Private Access ou sur la page de gestion de vos agents d’espace de travail Log Analytics.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d'informations, consultez les pages suivantes :

- [Catalogue de solutions Azure Sentinel](sentinel-solutions-catalog.md)
- [Intégration des renseignements sur les menaces dans Azure Sentinel](threat-intelligence-integration.md)
