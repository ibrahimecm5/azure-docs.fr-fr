---
title: Informations de référence sur les schémas de source de données Microsoft Sentinel
description: Cet article répertorie les schémas de sources de données Azure et tiers pris en charge par Microsoft Sentinel, avec les liens vers leur documentation de référence.
author: batamig
ms.author: bagol
manager: rkarlin
ms.assetid: ''
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.topic: reference
ms.custom: ignite-fall-2021
ms.date: 11/09/2021
ms.openlocfilehash: 5b2c03c90fc17d2226de9f364e5ff4d3e5447307
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132521678"
---
# <a name="data-source-schema-reference"></a>Informations de référence sur les schémas de source de données

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Cet article répertorie les schémas de sources de données Azure et tiers pris en charge, avec les liens vers leur documentation de référence.

## <a name="azure-data-sources"></a>Sources de données Azure

| Type                             | Source de données             | Nom de table Log Analytics | Informations de référence sur les schémas |
| -------------------------------- | ---------------------- | ---------------------- | ---------------- |
| **Microsoft Azure**                            | Azure Active Directory | SigninEvents           | [Propriétés de connexion des rapports d’activité Azure AD](/graph/api/resources/signin#properties) |
| **Microsoft Azure**                            | Azure Active Directory | AuditLogs              | [Informations de référence AuditLogs Azure Monitor](/azure/azure-monitor/reference/tables/auditlogs) |
| **Microsoft Azure**                            | Azure Active Directory | AzureActivity          | [Informations de référence AzureActivity Azure Monitor](/azure/azure-monitor/reference/tables/azureactivity) |
| **Microsoft Azure**                            | Office                 | OfficeActivity         | Schémas de l’API Activité de gestion Office 365 : <br>- [Schéma commun ](/office/office-365-management-api/office-365-management-activity-api-schema#common-schema)   <br>- [Schéma de l’administreur Exchange ](/office/office-365-management-api/office-365-management-activity-api-schema#exchange-admin-schema) <br>- [Schéma de la boîte aux lettres Exchange](/office/office-365-management-api/office-365-management-activity-api-schema#exchange-mailbox-schema)  <br>- [Schéma SharePoint Base](/office/office-365-management-api/office-365-management-activity-api-schema#sharepoint-base-schema)   <br>- [Opérations sur les fichiers SharePoint](/office/office-365-management-api/office-365-management-activity-api-schema#sharepoint-file-operations) |
| **Microsoft Azure**                            | Azure Key Vault         | AzureDiagnostics       | [Informations de référence AzureDiagnostics Azure Monitor](/azure/azure-monitor/reference/tables/azurediagnostics) |
| **Hôte**                             | Linux                  | syslog                 | [Informations de référence Syslog Azure Monitor](/azure/azure-monitor/reference/tables/syslog) |
| **Réseau**                          | Journaux d’activité IIS               | W3CIISLog              | [Informations de référence W3CIISLog Azure Monitor](/azure/azure-monitor/reference/tables/w3ciislog) |
| **Réseau**                          | VMinsights             | VMConnection           | [Informations de référence VMConnection Azure Monitor](/azure/azure-monitor/reference/tables/vmconnection) |
| **Réseau**                          | Solution Wire Data     | WireData               | [Informations de référence Azure Monitor WireData](/azure/azure-monitor/reference/tables/wiredata) |
| **Réseau**                          | Journaux de flux NSG          | AzureNetworkAnalytics  | [Schéma et agrégation de données dans Traffic Analytics](../network-watcher/traffic-analytics-schema.md) |
| | | | |

> [!NOTE]
> Pour plus d'informations, consultez l'intégralité de la [référence de données Azure Monitor](/azure/azure-monitor/reference/).
>
## <a name="3rd-party-vendor-data-sources"></a>Sources de données de fournisseurs tiers

Le tableau suivant répertorie les fournisseurs tiers pris en charge et leur documentation Syslog ou Common Event Format (CEF) pour différents types de journaux pris en charge, contenant des mappages de champs CEF et des exemples de journaux pour chaque type de catégorie.

| Type |    Fournisseur |    Produit | Nom de table Log Analytics | Référence du mappage de champs CEF  |
| ----- | ----- | ----- | ----- |----- |
| **Réseau** | Palo Alto   | PAN OS    | CommonSecurityLog |   [Guide d’intégration PAN-OS 9.0 Common Event Format](https://docs.paloaltonetworks.com/content/dam/techdocs/en_US/pdf/cef/pan-os-90-cef-configuration-guide.pdf) (rechercher des *formats de journal de style CEF*) |
| **Réseau** | Check Point  |ALL   | CommonSecurityLog | [Description des champs du journal](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk109795)       |
| **Réseau** | Fortigate   | ALL   | CommonSecurityLog | [Structure du schéma du journal](https://docs.fortinet.com/document/fortigate/6.2.3/fortios-log-message-reference/738142/log-schema-structure)         |
| **Réseau** | Barracuda | Pare-feu d’application web |  CommonSecurityLog   | [Configuration de Syslog et d’autres journaux](https://campus.barracuda.com/product/webapplicationfirewall/doc/4259935/how-to-configure-syslog-and-other-logs/)  |
| **Réseau** | Cisco | ASA | CommonSecurityLog | [Messages Syslog de la série Cisco ASA](https://www.cisco.com/c/en/us/td/docs/security/asa/syslog/b_syslog/about.html)    |
| **Réseau** | Cisco | Firepower   | CommonSecurityLog | [Messages Syslog de Cisco Firepower Threat Defense](https://www.cisco.com/c/en/us/td/docs/security/firepower/Syslogs/b_fptd_syslog_guide.html)    |
| **Réseau** | Cisco   | Umbrella  | Table des journaux personnalisés  | [Formats des journaux et contrôle de version](https://docs.umbrella.com/deployment-umbrella/docs/log-formats-and-versioning)   |
| **Réseau**   | Cisco | Meraki    | CommonSecurityLog |   [Exemples de types d’événements et de journaux Syslog](https://documentation.meraki.com/zGeneral_Administration/Monitoring_and_Reporting/Syslog_Event_Types_and_Log_Samples)    |
| **Réseau**   | Zscaler | Nano Streaming Service (NSS)|   CommonSecurityLog | [Mise en forme des flux NSS](https://help.zscaler.com/zia/documentation-knowledgebase/analytics/nss/nss-feeds/formatting-nss-feeds) (Web, pare-feu, DNS et journaux de tunnel uniquement) |
| **Réseau**   |F5 | BigIP LTM|    CommonSecurityLog|  [Messages d’événement et types d’attaques](https://techdocs.f5.com/kb/en-us/products/big-ip_ltm/manuals/product/bigip-external-monitoring-implementations-13-0-0/15.html)  |
| **Réseau** | F5  | BigIP ASM|    CommonSecurityLog|  [Événements de sécurité de l’application de journalisation](https://techdocs.f5.com/kb/en-us/products/big-ip_asm/manuals/product/asm-implementations-13-1-0/14.html)                                                           |
| **Réseau** | Citrix  |Pare-feu d’application web   | CommonSecurityLog|    [Prise en charge de la journalisation CEF (Common Event format) dans le pare-feu d’application](https://support.citrix.com/article/CTX136146) <br>  [Informations de référence sur les messages Syslog NetScaler 12.0](https://developer-docs.citrix.com/projects/netscaler-syslog-message-reference/en/12.0/)   |
|**Hôte** |Symantec | Symantec Endpoint Protection Manager (SEPM) | CommonSecurityLog|[Paramètres de journalisation externe et niveaux de gravité des événements de journal pour Endpoint Protection Manager](https://support.symantec.com/us/en/article.tech171741.html)|
|**Hôte** |Trend Micro |Tous |CommonSecurityLog | [Mappage de contenu Syslog - CEF](https://docs.trendmicro.com/en-us/enterprise/control-manager-70/appendices/syslog-mapping-cef.aspx) |
| | | | | |

> [!NOTE]
> Pour plus d’informations, consultez également [Mappage de champs CEF et CommonSecurityLog](cef-name-mapping.md).
> 
## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les connecteurs Microsoft Sentinel pris en charge, notamment les connecteurs CEF, Syslog, les connecteurs directs, les connecteurs d’agent et les connecteurs personnalisés :

- [Connecter des sources de données](connect-data-sources.md)

- [Microsoft Sentinel Syslog, CEF et autres connecteurs tiers](https://techcommunity.microsoft.com/t5/azure-sentinel/azure-sentinel-syslog-cef-and-other-3rd-party-connectors-grand/ba-p/803891)
