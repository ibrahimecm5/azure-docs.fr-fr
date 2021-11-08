---
title: Classeurs Azure Sentinel courants | Microsoft Docs
description: Découvrez les classeurs les plus courants pour utiliser des ressources Azure Sentinel populaires et prêtes à l’emploi.
services: sentinel
cloud: na
documentationcenter: na
author: batamig
manager: rkarlin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 03/07/2021
ms.author: bagol
ms.custom: ignite-fall-2021
ms.openlocfilehash: a8e96969dc4edd5a79e1ca85ac32529c10b8a343
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131019549"
---
# <a name="commonly-used-azure-sentinel-workbooks"></a>Classeurs Azure Sentinel courants

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Le tableau suivant présente les classeurs Azure Sentinel intégrés les plus courants.

Accédez aux classeurs dans Azure Sentinel sous **Gestion des menaces** > **Classeurs** à gauche, puis recherchez le classeur que vous souhaitez utiliser. Pour plus d’informations, consultez [Visualiser et superviser vos données](monitor-your-data.md).

> [!TIP]
> Nous vous recommandons de déployer tous les workbooks associés aux données que vous ingérez. Les workbooks permettent d’obtenir une supervision et des investigations plus poussées en fonction des données collectées.
>
> Pour plus d’informations, consultez [Connecter des sources de données](connect-data-sources.md) et [Découvrir et déployer de manière centralisée du contenu et des solutions Azure Sentinel prêts à l’emploi](sentinel-solutions-deploy.md).
>

|Nom du classeur  |Description  |
|---------|---------|
|**Efficacité analytique**     |  Fournit des insights sur l’efficacité de vos règles d’analytique pour vous aider à obtenir un meilleur niveau de performance du centre SOC. <br><br>Pour plus d’informations, consultez [Kit de ressources pour les centres SOC pilotés par les données](https://techcommunity.microsoft.com/t5/azure-sentinel/the-toolkit-for-data-driven-socs/ba-p/2143152).|
|**Activité Azure**     |     Fournit des insights complets sur l’activité Azure de votre organisation en analysant et en mettant en corrélation tous les événements et opérations utilisateurs. <br><br>Pour plus d’informations, consultez [Audit avec les journaux d’activité Azure](audit-sentinel-data.md#auditing-with-azure-activity-logs).    |
|**Journaux d’audit Azure AD**     |  Utilise les journaux d’audit Azure Active Directory pour fournir des insights dans les scénarios Azure AD. <br><br>Pour plus d’informations, consultez [Démarrage rapide : Bien démarrer avec Azure Sentinel](get-visibility.md).     |
|**Journaux d’audit, d’activité et de connexion Azure AD**     |   Fournit des insights sur les données d’audit, d’activité et de connexion Azure Active Directory avec un seul classeur. Affiche l’activité telle que les connexions par emplacement, l’appareil, la raison de l’échec, l’action de l’utilisateur, etc. <br><br> Ce classeur peut être utilisé par les administrateurs de la sécurité et les administrateurs Azure.   |
|**Journaux de connexion Azure AD**     | Utilise les journaux de connexion Azure AD pour fournir des insights dans les scénarios Azure AD.        |
|**CMMC (Cybersecurity Maturity Model Certification)**     |   Fournit un mécanisme permettant d’afficher les requêtes de journal alignées sur les contrôles CMMC dans le portefeuille Microsoft, notamment les offres de sécurité Microsoft, Office 365, Teams, Intune, Windows Virtual Desktop, etc. <br><br>Pour plus d’informations, consultez [Classeur CMMC (Cybersecurity Maturity Model Certification) en préversion publique](https://techcommunity.microsoft.com/t5/azure-sentinel/what-s-new-cybersecurity-maturity-model-certification-cmmc/ba-p/2111184).|
|**Supervision de l’intégrité de la collecte de données** / **Supervision de l’utilisation**     |  Fournit des insights sur l’état d’ingestion des données de votre espace de travail, par exemple la taille de l’ingestion, la latence et le nombre de journaux par source. Supervisez et détectez les anomalies pour mieux évaluer l’intégrité de la collecte de données de votre espace de travail. <br><br>Pour plus d’informations, consultez [Supervision de l’intégrité de connecteurs de données avec ce classeur Azure Sentinel](monitor-data-connector-health.md).    |
|**Analyseur d’événements**     |  Permet d’explorer, d’auditer et d’accélérer l’analyse du journal des événements Windows, y compris tous les détails et attributs des événements, comme la sécurité, l’application, le système, la configuration, le service d’annuaire, le système DNS, etc.       |
|**Exchange Online**     |Fournit des insights sur Microsoft Exchange Online en traçant et en analysant toutes les opérations Exchange et les activités des utilisateurs.         |
|**Identité et accès**     |   Fournit des insights sur les opérations d’identité et d’accès dans l’utilisation des produits Microsoft, au moyen de journaux de sécurité qui incluent des journaux d’audit et de connexion.     |
|**Vue d’ensemble des incidents**     |   Facilite le triage et l’investigation en fournissant des informations détaillées sur un incident, notamment des informations générales, des données d’entité, le temps de triage, le temps d’atténuation et des commentaires. <br><br>Pour plus d’informations, consultez [Kit de ressources pour les centres SOC pilotés par les données](https://techcommunity.microsoft.com/t5/azure-sentinel/the-toolkit-for-data-driven-socs/ba-p/2143152).      |
|<a name="investigation-insights"></a>**Insights d’investigation**     | Fournit aux analystes des insights sur les données relatives aux incidents, aux signets et aux entités. Les requêtes courantes et les visualisations détaillées peuvent les aider à examiner les activités suspectes.     |
|**Microsoft Cloud App Security – Journaux de découverte**     |   Fournit des détails sur les applications cloud utilisées dans votre organisation, ainsi que des insights tirés des tendances d’utilisation et des données d’exploration pour des utilisateurs et des applications spécifiques.  <br><br>Pour plus d’informations, consultez [Connexion de données à partir de Microsoft Cloud App Security](./data-connectors-reference.md#microsoft-cloud-app-security-mcas).|
|**Classeur MITRE ATT&CK**     |   Fournit des données sur la couverture MITRE ATT&CK pour Azure Sentinel.      |
|**Office 365**     | Fournit des insights sur Office 365 en traçant et en analysant toutes les opérations et activités. Explorez les données SharePoint, OneDrive, Teams et Exchange.       |
|**alertes de sécurité**     |  Fournit un tableau de bord des alertes de sécurité de l’environnement Azure Sentinel. <br><br>Pour plus d’informations, consultez [Créer automatiquement des incidents à partir d’alertes de sécurité Microsoft](create-incidents-from-alerts.md).      |
|**Efficacité des opérations de sécurité**     |  Permet aux responsables de centre des opérations de sécurité (SOC) de voir des métriques et des mesures d’efficacité globales concernant le niveau de performance de leur équipe. <br><br>Pour plus d’informations, consultez [Meilleure gestion du centre SOC avec les métriques relatives aux incidents](manage-soc-with-incident-metrics.md).  |
|**Renseignement sur les menaces**     | Fournit des insights sur les indicateurs de menace, notamment le type et la gravité des menaces, l’activité des menaces dans le temps et la corrélation avec d’autres sources de données, notamment Office 365 et les pare-feu.  <br><br>Pour plus d’informations, consultez [Comprendre les renseignements sur les menaces dans Azure Sentinel](understand-threat-intelligence.md).      |
|**Confiance Zéro (TIC 3.0)**     |  Fournit une visualisation automatisée des principes de Confiance Zéro, transmise à l'[infrastructure des connexions Internet de confiance (TIC)](https://www.cisa.gov/trusted-internet-connections).   <br><br>Pour plus d’informations, consultez le [blog de l'annonce du classeur Confiance Zéro (TIC 3.0)](https://techcommunity.microsoft.com/t5/public-sector-blog/announcing-the-azure-sentinel-zero-trust-tic3-0-workbook/ba-p/2313761).  |
