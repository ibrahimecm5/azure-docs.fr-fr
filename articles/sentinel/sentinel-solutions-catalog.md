---
title: Catalogue du hub de contenu Microsoft Sentinel | Microsoft Docs
description: Cet article montre et détaille les packages du hub de contenu Microsoft Sentinel disponibles.
services: sentinel
cloud: na
documentationcenter: na
author: batamig
manager: rkarlin
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 11/09/2021
ms.author: bagol
ms.custom: ignite-fall-2021
ms.openlocfilehash: 375f6150c9d08f06c09f51ab18276a5ec97f576c
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132516917"
---
# <a name="microsoft-sentinel-content-hub-catalog"></a>Catalogue du hub de contenu Microsoft Sentinel

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

> [!IMPORTANT]
>
> L’expérience du hub de contenu Microsoft Sentinel est en **PRÉVERSION**, tout comme les packages de solutions individuels. Consultez l’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) pour connaître les conditions juridiques supplémentaires s’appliquant aux fonctionnalités Azure sont en version bêta, en préversion ou non encore en disponibilité générale.

Les [solutions Microsoft Sentinel](sentinel-solutions.md) offrent un moyen consolidé d’acquérir du contenu Microsoft Sentinel, par exemple des connecteurs de données, des classeurs, des analyses et des automatisations, dans votre espace de travail grâce à un déploiement en une seule étape.

Cet article liste les solutions et les connecteurs de données Microsoft Sentinel prêts à l’emploi (intégrés) et à la demande que vous pouvez déployer dans votre espace de travail. Le déploiement d’une solution positionne tout contenu de sécurité inclus, tel que les connecteurs de données, les playbooks, les classeurs ou les règles, dans la zone adéquate de Microsoft Sentinel. 

Pour plus d’informations, consultez [Découvrir et déployer de manière centralisée du contenu et des solutions Microsoft Sentinel prêts à l’emploi](sentinel-solutions-deploy.md).

## <a name="domain-solutions"></a>Solutions de domaine

|Nom    |Includes  |Catégories |Pris en charge par  |
|---------|---------|---------|---------|
|**Gestion des risques internes Microsoft** |[Connecteur de données](data-connectors-reference.md#microsoft-365-insider-risk-management-irm-preview), workbook, règles d’analytique, requêtes de chasse |Sécurité - Menace interne | Microsoft|
| **Solution Microsoft MITRE ATT&CK pour le cloud**| Workbooks, règles d’analytique, requêtes de chasse|Sécurité - Protection contre les menaces, sécurité - Autres |Microsoft |
| **Microsoft Sentinel Deception** | [Classeurs, règles d’analytique, watchlists](monitor-key-vault-honeytokens.md)  | Sécurité - Protection contre les menaces  |Microsoft |
|**Confiance Zéro** (TIC 3.0) |Workbooks |Identité, sécurité - Autres |Microsoft |
| | | | |

## <a name="arista-networks"></a>Arista Networks

|Nom    |Includes  |Catégories |Pris en charge par  |
|---------|---------|---------|---------|
|**Arista Networks** (Awake Security) |Connecteur de données, workbooks, règles d’analytique | Sécurité - Réseau |[Arista - Awake Security](https://awakesecurity.com/) |
| | | | |


## <a name="armorblox"></a>Armorblox

|Nom    |Includes  |Catégories |Pris en charge par  |
|---------|---------|---------|---------|
|**Armorblox - Sentinel** |Connecteur de données | Sécurité - Protection contre les menaces |[Armorblox](https://www.armorblox.com/contact/) |
| | | | |




## <a name="azure"></a>Azure

|Nom    |Includes  |Catégories |Pris en charge par  |
|---------|---------|---------|---------|
|**Solution de pare-feu Azure pour Sentinel**| [Connecteur de données](data-connectors-reference.md#azure-firewall), workbook, règles d’analytique, playbooks, requêtes de chasse, connecteur Logic Apps personnalisé |Sécurité - Sécurité réseau, réseau | Communauté|
|**Microsoft Sentinel pour SQL PaaS**     |  [Connecteur de données](data-connectors-reference.md#azure-sql-databases), workbook, règles d’analytique, playbooks, requêtes de chasse     | Application        |      Communauté   |
|**Laboratoire de formation Microsoft Sentinel** |Workbook, règles d’analytique, playbooks, requêtes de chasse | Formations et tutoriels |Microsoft |
|**Azure SQL** | [Connecteur de données](data-connectors-reference.md#azure-sql-databases), workbook, analytique, playbooks, requêtes de chasse  | Application |Microsoft  |
| | | | |


## <a name="box"></a>Box

|Nom   |Includes  |Catégories |Pris en charge par  |
|------------------|---------|---------|---------|
|**Solution Box**| Connecteur de données, workbook, règles d’analytique, requêtes de chasse, analyseur |  Stockage, application  | Microsoft|
| | | | |


## <a name="check-point"></a>Check Point

|Nom   |Includes  |Catégories |Pris en charge par  |
|------------------|---------|---------|---------|
|**Solutions Microsoft Sentinel par Check Point**   |[Connecteur de données](data-connectors-reference.md#check-point), playbooks, connecteur Logic Apps personnalisé  | Sécurité - Automatisation (SOAR) | [Point de contrôle](https://www.checkpoint.com/support-services/contact-support/)|
| | | | |


## <a name="cisco"></a>Cisco

|Nom    |Includes  |Catégories |Pris en charge par  |
|---------|---------|---------|---------|
|**Cisco ACI** |Connecteur de données, analyseur |Sécurité - Réseau |Microsoft |
|**Cisco ASA** |[Connecteur de données](data-connectors-reference.md#cisco-asa), playbooks, connecteur Logic Apps personnalisé |Sécurité - Automatisation (SOAR) |Microsoft |
|**Cisco Duo Security** |Connecteur de données, analyseur | Identité|Microsoft |
|**Cisco ISE**  |Connecteur de données, workbooks, règles d’analytique, playbooks, requêtes de chasse, analyseur, connecteur Logic Apps personnalisé |Mise en réseau, sécurité - Autres | Microsoft |
|**Cisco Meraki** |[Connecteur de données](data-connectors-reference.md#cisco-meraki-preview), playbooks, connecteur Logic Apps personnalisé |Sécurité - Réseau |Microsoft |
|**Passerelle de messagerie Cisco sécurisée/ESA** |Connecteur de données, analyseur |Sécurité - Protection contre les menaces |Microsoft |
|**Cisco StealthWatch** |Connecteur de données, analyseur |Sécurité - Réseau | Microsoft|
|**Cisco Umbrella** |[Connecteur de données](data-connectors-reference.md#cisco-umbrella-preview), workbooks, règles d’analytique, playbooks, requêtes de chasse, analyseur, connecteur Logic Apps personnalisé |Sécurité - Sécurité du cloud |Microsoft |
|**Cisco Web Security Appliance (WSA)** | Connecteur de données, analyseur|Sécurité - Réseau |Microsoft |
| | | | |


## <a name="cloudflare"></a>Cloudflare


|Nom    |Includes  |Catégories |Pris en charge par  |
|---------|---------|---------|---------|
|**Solution Cloudflare**|Connecteur de données, workbooks, règles d’analytique, requêtes de chasse, analyseur| Sécurité - Réseau, mise en réseau |Microsoft |
| | | | |


## <a name="contrast-security"></a>Contrast Security



|Nom    |Includes  |Catégories |Pris en charge par  |
|---------|---------|---------|---------|
|**Solution Contrast Protect Microsoft Sentinel**|Connecteur de données, workbooks, règles d’analytique |Sécurité - Protection contre les menaces |Microsoft  |
| | | | |

## <a name="crowdstrike"></a>CrowdStrike


|Nom    |Includes  |Catégories |Pris en charge par  |
|---------|---------|---------|---------|
|**Solution CrowdStrike Falcon Endpoint Protection**| Connecteur de données, workbooks, règles d’analytique, playbooks, analyseur| Sécurité - Protection contre les menaces| Microsoft|
| | | | |

## <a name="digital-guardian"></a>Digital Guardian


|Nom    |Includes  |Catégories |Pris en charge par  |
|---------|---------|---------|---------|
|**Digital Guardian** |Connecteur de données, analyseur |Sécurité - Protection des informations |Microsoft |
| | | |

## <a name="falconforce"></a>FalconForce

|Nom    |Includes  |Catégories |Pris en charge par  |
|---------|---------|---------|---------|
|**Contenu FalconFriday - Falcon Friday** |Règles analytiques |Comportement de l’utilisateur (UEBA) - Menace interne | [FalconForce](https://www.falconforce.nl/en/)|
| | | |

## <a name="fireeye-nx-network-security"></a>FireEye NX (sécurité réseau)

|Nom    |Includes  |Catégories |Pris en charge par  |
|---------|---------|---------|---------|
|**FireEye NX (Network Security)** |Connecteur de données, analyseur |Sécurité - Réseau| Microsoft|
| | | |

## <a name="flare-systems-firework"></a>Flare Systems Firework

|Nom    |Includes  |Catégories |Pris en charge par  |
|---------|---------|---------|---------|
|**Flare Systems Firework** |Connecteur de données |Sécurité - Protection contre les menaces |Microsoft|
| | | |

## <a name="forescout"></a>Forescout

|Nom    |Includes  |Catégories |Pris en charge par  |
|---------|---------|---------|---------|
|**Forescout** |Connecteur de données, analyseur |Sécurité - Réseau | Microsoft|
| | | |

## <a name="fortinet-fortigate"></a>Fortinet Fortigate

|Nom    |Includes  |Catégories |Pris en charge par  |
|---------|---------|---------|---------|
|**Fortinet Fortigate** |[Connecteur de données](data-connectors-reference.md#fortinet), playbooks, connecteur Logic Apps personnalisé|Sécurité - Automatisation (SOAR) | Microsoft|
| | | |


## <a name="google"></a>Google

|Nom    |Includes  |Catégories |Pris en charge par  |
|---------|---------|---------|---------|
|**Google Cloud Platform DNS Solution** |Connecteur de données, analyseur |Fournisseur cloud, réseau |Microsoft |
|**Solution de surveillance Google Cloud Platform Cloud**|Connecteur de données, analyseur |Fournisseur de cloud | Microsoft|
|**Solution IAM (Identity and Access Management) Google Cloud Platform**|Connecteur de données, workbook, règles d’analytique, playbooks, requêtes de chasse, analyseur, connecteur Logic Apps personnalisé|Fournisseur de cloud, identité |Microsoft |
| | | | |


## <a name="hyas"></a>HYAS

|Nom    |Includes  |Catégories |Pris en charge par  |
|---------|---------|---------|---------|
|**Galerie Solutions HYAS Insight pour Microsoft Sentinel**| Playbooks| Sécurité - Renseignement sur les menaces, sécurité - Automatisation (SOAR) |Microsoft |
| | | | |

## <a name="imperva"></a>Imperva

|Nom    |Includes  |Catégories |Pris en charge par  |
|---------|---------|---------|---------|
|**Imperva Cloud WAF** (auparavant Imperva Incapsula)| [Connecteur de données](data-connectors-reference.md#imperva-waf-gateway-preview), analyseur| Sécurité - Réseau | Microsoft|
| | | | |

## <a name="infoblox"></a>InfoBlox

|Nom    |Includes  |Catégories |Pris en charge par  |
|---------|---------|---------|---------|
|**InfoBlox Threat Defense / InfoBlox Cloud Data Connector**| [Connecteur de données](data-connectors-reference.md#infoblox-network-identity-operating-system-nios-preview), workbook, règles d’analytique| Sécurité - Protection contre les menaces | Microsoft|
| | | | |


## <a name="ironnet"></a>IronNet

|Nom    |Includes  |Catégories |Pris en charge par  |
|---------|---------|---------|---------|
|**IronNet CyberSecurity Iron Defense – Microsoft Sentinel** | |Sécurité - Réseau |Microsoft |
| | | |



## <a name="juniper"></a>Juniper

|Nom    |Includes  |Catégories |Pris en charge par  |
|---------|---------|---------|---------|
|**Juniper IDP** |Connecteur de données, analyseur|Sécurité - Réseau |Microsoft |
| | | | |


## <a name="kaspersky"></a>Kaspersky

|Nom    |Includes  |Catégories |Pris en charge par  |
|---------|---------|---------|---------|
|**Kaspersky AntiVirus** |Connecteur de données, analyseur   | Sécurité - Protection contre les menaces|Microsoft |
| | | | |


## <a name="lookout"></a>Lookout

|Nom    |Includes  |Catégories |Pris en charge par  |
|---------|---------|---------|---------|
|**Lookout Mobile Threat Defense pour Microsoft Sentinel**| [Connecteur de données](data-connectors-reference.md#lookout-mobile-threat-defense-preview)|Sécurité - Réseau |[Lookout](https://www.lookout.com/support) |
| | | |

## <a name="mcafee"></a>McAfee

|Nom    |Includes  |Catégories |Pris en charge par  |
|---------|---------|---------|---------|
|**Solution McAfee ePolicy Orchestrator**| Connecteur de données, workbook, règles d’analytique, playbooks, requêtes de chasse, analyseur, connecteur Logic Apps personnalisé| Sécurité - Protection contre les menaces| Microsoft |
|**McAfee Network Security Platform Solution** (Intrushield) + AntiVirus Information (T1 minus Logic apps) |Connecteur de données, workbooks, règles d’analytique, requêtes de chasse, analyseur |Sécurité - Protection contre les menaces | Microsoft|
| | | | |

## <a name="microsoft"></a>Microsoft

|Nom    |Includes  |Catégories |Pris en charge par  |
|---------|---------|---------|---------|
|**Microsoft Sentinel 4 Microsoft Dynamics 365**     |   [Connecteur de données](data-connectors-reference.md#dynamics-365), workbooks, règles d’analytique et requêtes de chasse |      Application   |Microsoft         |
|**Microsoft Sentinel pour Teams**     | Connecteur de données, règles d’analytique, playbooks, requêtes de chasse      |   Application      |    Communauté     |
| | | | |


## <a name="oracle"></a>Oracle


|Nom    |Includes  |Catégories |Pris en charge par  |
|---------|---------|---------|---------|
|**Oracle Cloud Infrastructure** |Connecteur de données, analyseur | Fournisseur de cloud | Microsoft|
|**Solution Oracle Database Audit** | Connecteur de données, workbook, règles d’analytique, requêtes de chasse, analyseur| Application|Microsoft |
| | | | |

## <a name="palo-alto"></a>Palo Alto

|Nom    |Includes  |Catégories |Pris en charge par  |
|---------|---------|---------|---------|
|**Palo Alto PAN-OS**|[Connecteur de données](#palo-alto), playbooks, connecteur Logic Apps personnalisé |Sécurité - Automatisation (SOAR), sécurité - Réseau |Microsoft |
|**Solution Palo Alto Prisma**|[Connecteur de données](#palo-alto), workbooks, règles d’analytique, requêtes de chasse, analyseur |Sécurité - Sécurité du cloud |Microsoft |
| | | | |

## <a name="ping-identity"></a>Identité Ping

|Nom    |Includes  |Catégories |Pris en charge par  |
|---------|---------|---------|---------|
|**Solution PingFederate** |Connecteur de données, workbooks, règles d’analytique, requêtes de chasse, analyseur| Identité|Microsoft |
| | | | |

## <a name="proofpoint"></a>Proofpoint

|Nom    |Includes  |Catégories |Pris en charge par  |
|---------|---------|---------|---------|
|**Solution Proofpoint POD** |[Connecteur de données](data-connectors-reference.md#proofpoint-on-demand-pod-email-security-preview), workbook, règles d’analytique, requêtes de chasse, analyseur| Sécurité - Protection contre les menaces|Microsoft |
|**Solution Proofpoint TAP** | Workbooks, règles d’analytique, playbooks, connecteur Logic Apps personnalisé|Sécurité - Automatisation (SOAR), sécurité - Protection contre les menaces |Microsoft |
| | | |

## <a name="qualys"></a>Qualys

|Nom    |Includes  |Catégories |Pris en charge par  |
|---------|---------|---------|---------|
|**Solution Qualys VM** |Workbooks, règles d’analytique |Sécurité - Gestion des vulnérabilités |Microsoft |
| | | | |

## <a name="rapid7"></a>Rapid7

|Nom    |Includes  |Catégories |Pris en charge par  |
|---------|---------|---------|---------|
|**Solution Rapid7 InsightVM CloudAPI** |Connecteur de données, analyseur|Sécurité - Gestion des vulnérabilités |Microsoft |
| | | | |

## <a name="reversinglabs"></a>ReversingLabs

|Nom    |Includes  |Catégories |Pris en charge par  |
|---------|---------|---------|---------|
|**Solution ReversingLabs TitaniumCloud File Enrichment**|Playbooks |Sécurité - Renseignement sur les menaces |[ReversingLabs](https://support.reversinglabs.com/hc/en-us) |
| | | | |

## <a name="riskiq"></a>RiskIQ

|Nom    |Includes  |Catégories |Pris en charge par  |
|---------|---------|---------|---------|
|**Playbooks de renseignements sur la sécurité RiskIQ**|Playbooks |Sécurité - Renseignement sur les menaces, sécurité - Automatisation (SOAR) |[RiskIQ](https://www.riskiq.com/integrations/microsoft/) |
| | | | |

## <a name="rsa"></a>RSA

|Nom    |Includes  |Catégories |Pris en charge par  |
|---------|---------|---------|---------|
|**RSA SecurID** |Connecteur de données, analyseur |Sécurité - Autres, identité |Microsoft |
| | | |



## <a name="sap"></a>SAP

|Nom    |Includes  |Catégories |Pris en charge par  |
|---------|---------|---------|---------|
|**Monitoring continu des menaces pour SAP**|[Connecteur de données](sap-deploy-solution.md), [workbooks, règles d’analytique, watchlists](sap-solution-security-content.md) | Application  |Communauté |
| | | | |

## <a name="semperis"></a>Semperis

|Nom    |Includes  |Catégories |Pris en charge par  |
|---------|---------|---------|---------|
|**Semperis**|Connecteur de données, workbooks, règles d’analytique, analyseur | Sécurité - Protection contre les menaces, identité  |[Semperis](https://www.semperis.com/contact-us/) |
| | | | |

## <a name="senserva-pro"></a>Senserva Pro

|Nom    |Includes  |Catégories |Pris en charge par  |
|---------|---------|---------|---------|
|**Offre Senserva pour Microsoft Sentinel** |Connecteur de données, workbooks, règles d’analytique, requêtes de chasse |Conformité |[Senserva](https://www.senserva.com/contact/) |
| | | | |


## <a name="sonrai-security"></a>Sonrai Security

|Nom    |Includes  |Catégories |Pris en charge par  |
|---------|---------|---------|---------|
|**Sonrai Security – Microsoft Sentinel** |Connecteur de données, workbooks, règles d’analytique   | Conformité|Sonrai Security |
| | | | |

## <a name="slack"></a>Slack

|Nom    |Includes  |Catégories |Pris en charge par  |
|---------|---------|---------|---------|
|**Solution Slack Audit**|Connecteur de données, workbooks, règles d’analytique, requêtes de chasse, analyseur |Application| Microsoft|
| | | | |


## <a name="sophos"></a>Sophos

|Nom    |Includes  |Catégories |Pris en charge par  |
|---------|---------|---------|---------|
|**Solution Sophos Endpoint Protection** |Connecteur de données, analyseur| Sécurité - Protection contre les menaces |Microsoft |
|**Solution Sophos XG Firewall**| Workbooks, règles d’analytique, analyseur |Sécurité - Réseau |Microsoft |
| | | | |


## <a name="symantec"></a>Symantec

|Nom    |Includes  |Catégories |Pris en charge par  |
|---------|---------|---------|---------|
|**Symantec Endpoint**|Connecteur de données, workbook, règles d’analytique, playbooks, requêtes de chasse, analyseur| Sécurité - Protection contre les menaces|Microsoft |
|**Solution Symantec ProxySG**|Workbooks, règles d’analytique |Sécurité - Réseau |Symantec |
| | | | |

## <a name="tenable"></a>Tenable

|Nom    |Includes  |Catégories |Pris en charge par  |
|---------|---------|---------|---------|
|**Tenable Nessus Scanner / Rapports VM IO pour le cloud**  | Connecteur de données, analyseur| Sécurité - Gestion des vulnérabilités| Microsoft |
| | | | |


## <a name="trend-micro"></a>Trend Micro

|Nom    |Includes  |Catégories |Pris en charge par  |
|---------|---------|---------|---------|
|**Solution Trend Micro Apex One**  | Connecteur de données, requêtes de chasse, analyseur| Sécurité - Protection contre les menaces|Microsoft |
| | | | |




## <a name="ubiquiti"></a>Ubiquiti

|Nom    |Includes  |Catégories |Pris en charge par  |
|---------|---------|---------|---------|
|**Solution Ubiquiti UniFi**|Connecteur de données, workbooks, règles d’analytique, requêtes de chasse, analyseur |Sécurité - Réseau |Microsoft |
| | | | |


## <a name="varmour"></a>vArmour

|Nom    |Includes  |Catégories |Pris en charge par  |
|---------|---------|---------|---------|
|**Solution vArmour Application Controller et Microsoft Sentinel**|Connecteur de données, workbook, règles d’analytique |Opérations IT |[vArmour](https://www.varmour.com/contact-us/) |
| | | | |

## <a name="vectra"></a>Vectra

|Nom    |Includes  |Catégories |Pris en charge par  |
|---------|---------|---------|---------|
|**Solution Vectra Stream** |Connecteur de données, requêtes de chasse, analyseur |Sécurité - Réseau |Microsoft |
| | | |


## <a name="vmware"></a>VMware

|Nom    |Includes  |Catégories |Pris en charge par  |
|---------|---------|---------|---------|
|**Solution VMware Carbon Black**|Workbooks, règles d’analytique| Sécurité - Protection contre les menaces| Microsoft|
| | | | |

## <a name="zeek-network"></a>Zeek Network

|Nom    |Includes  |Catégories |Pris en charge par  |
|---------|---------|---------|---------|
|**Corelight pour Microsoft Sentinel**|Connecteur de données, workbooks, règles d’analytique, requêtes de chasse, analyseur | Opérations informatiques, sécurité - Réseau | [Zeek Network](https://support.corelight.com/)|
| | | | |


## <a name="next-steps"></a>Étapes suivantes

Dans ce document, vous avez découvert les solutions Microsoft Sentinel et appris à les rechercher et à les déployer.

- En savoir plus sur les [solutions Microsoft Sentinel](sentinel-solutions.md).
- [Rechercher et déployer des solutions Microsoft Sentinel](sentinel-solutions-deploy.md).
