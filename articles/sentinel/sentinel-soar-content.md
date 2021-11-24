---
title: Catalogue de contenu SOAR Azure Sentinel | Microsoft Docs
description: Cet article affiche et détaille le contenu fourni par Azure Sentinel pour l’orchestration de sécurité, l’automatisation et la réponse (SOAR), y compris les règles et les connecteurs de Logic Apps.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.assetid: ''
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 10/18/2021
ms.author: yelevin
ms.openlocfilehash: 5f9c68a63c63c3b4cc27b44d697f7577e4dafc3c
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132517973"
---
# <a name="azure-sentinel-soar-content-catalog"></a>Catalogue de contenu SOAR Azure Sentinel

Azure Sentinel offre une large gamme de règles et de connecteurs pour l’orchestration de la sécurité, l’automatisation et la réponse (SOAR), afin que vous puissiez facilement intégrer Azure Sentinel à tout produit ou service de votre environnement.

Les intégrations listées ci-dessous peuvent inclure certains ou l’ensemble des composants suivants :

| Type de composant | Objectif | Cas d’utilisation et instructions liées |
| -------------- | ------- | -------------------------------- |
| **Modèles de playbook** | Flux de travail automatisé | Utilisez des modèles de manuel pour déployer des règles prédéfinies pour répondre automatiquement aux menaces.<br><br>[Automatiser la réponse aux menaces à l’aide de playbooks dans Azure Sentinel](automate-responses-with-playbooks.md) |
| **Connecteurs managés Azure Logic Apps** | Blocs de construction pour la création de playbooks | Les playbooks utilisent des connecteurs gérés pour communiquer avec des services Microsoft et non Microsoft.<br><br>[Liste de tous les connecteurs Logic Apps et leur documentation](/connectors/connector-reference/) |
| **Connecteur personnalisé Azure Logic Apps** | Blocs de construction pour la création de playbooks | Vous pourriez souhaiter communiquer avec des services qui ne sont pas disponibles en tant que connecteurs prédéfinis. Les connecteurs personnalisés répondent à ce cas de figure en vous permettant de créer (et même de partager) un connecteur et de définir ses propres déclencheurs et actions.<br><ul><li>[Vue d’ensemble des connecteurs personnalisés](/connectors/custom-connectors/)<li>[Créer vos propres connecteurs Logic Apps personnalisés](/connectors/custom-connectors/create-logic-apps-connector) |
|

Vous pouvez trouver des intégrations SOAR et leurs composants aux emplacements suivants :

- Solutions Azure Sentinel
- Panneau d’automatisation Azure Sentinel, onglet des modèles de playbook
- Logic Apps Designer (pour les connecteurs Logic Apps managés)
- Dépôt Github Azure Sentinel

> [!TIP]
> - De nombreuses intégrations SOAR peuvent également être déployées dans le cadre d’une [solution Azure Sentinel](sentinel-solutions.md), ainsi que des connecteurs de données, des règles d’analyse et des classeurs associés. Pour plus d’informations, consultez le [catalogue de solutions Azure Sentinel](sentinel-solutions-catalog.md).
> - D’autres intégrations sont fournies par la communauté Azure Sentinel et se trouvent dans le référentiel GitHub.
> - Si vous avez un produit ou un service qui n’est pas listé ou actuellement pris en charge, envoyez une demande de fonctionnalité.  
> Vous pouvez également créer les vôtres à l’aide des outils suivants :
>    - Connecteur personnalisé Logic Apps
>    - Fonctions Azure
>    - Appels HTTP Logic Apps


## <a name="atlassian"></a>Atlassian

| Produit | Composants d’intégration | Pris en charge par | Scénarios |
| --- | --- | --- | --- |
| **Jira** | [Connecteur Logic Apps géré](/connectors/jira/)<br><br>Playbooks | Microsoft<br><br>Communauté | Synchroniser les incidents |
|

## <a name="check-point"></a>Check Point

| Produit | Composants d’intégration | Pris en charge par | Scénarios |
| --- | --- | --- | --- |
| **Check Point NGFW**<br>([Disponible en tant que solution](sentinel-solutions-catalog.md#check-point)) | Connecteur Logic Apps personnalisé<br><br>Playbooks | CheckPoint |  |
|

## <a name="cisco"></a>Cisco

| Produit | Composants d’intégration | Pris en charge par | Scénarios |
| --- | --- | --- | --- |
| **Cisco ASA,<br>Cisco Meraki** | Connecteur Logic Apps personnalisé<br><br>Playbooks | Communauté | Bloquer les adresses IP |
| **Cisco FirePower** | Connecteur Logic Apps personnalisé<br><br>Playbooks | Communauté | Bloquer les adresses IP et les URL |
| **Cisco ISE**<br>([Disponible en tant que solution](sentinel-solutions-catalog.md#cisco)) | Connecteur Logic Apps personnalisé<br><br>Playbooks | Microsoft |  |
| **Cisco Umbrella**<br>([Disponible en tant que solution](sentinel-solutions-catalog.md#cisco)) | Connecteur Logic Apps personnalisé<br><br>Playbooks | Microsoft | Bloquer les domaines, <br>gestion des stratégies, <br>gestion des listes de destinations, <br>enrichissement et investigation |
|

## <a name="crowdstrike"></a>CrowdStrike

| Produit | Composants d’intégration | Pris en charge par | Scénarios |
| --- | --- | --- | --- |
| **Protection de point de terminaison Falcon**<br>([Disponible en tant que solution](sentinel-solutions-catalog.md#crowdstrike)) | Playbooks | Microsoft | Enrichissement des points de terminaison,<br>isoler les points de terminaison |
|

## <a name="f5"></a>F5

| Produit | Composants d’intégration | Pris en charge par | Scénarios |
| --- | --- | --- | --- |
| **Big-IP** | Playbooks | Communauté | Bloquer les adresses IP et les URL |
|

## <a name="forcepoint"></a>Forcepoint

| Produit | Composants d’intégration | Pris en charge par | Scénarios |
| --- | --- | --- | --- |
| **Forcepoint NGFW** | Connecteur Logic Apps personnalisé<br><br>Playbooks | Communauté | Bloquer les adresses IP et les URL |
|

## <a name="fortinet"></a>Fortinet

| Produit | Composants d’intégration | Pris en charge par | Scénarios |
| --- | --- | --- | --- |
| **FortiGate**<br>([Disponible en tant que solution](sentinel-solutions-catalog.md#fortinet-fortigate)) | Connecteur Logic Apps personnalisé<br><br>Fonction Azure<br><br>Playbooks | Microsoft | Bloquer les adresses IP et les URL |
|

## <a name="freshdesk"></a>Freshdesk

| Produit | Composants d’intégration | Pris en charge par | Scénarios |
| --- | --- | --- | --- |
| **Freshdesk** | [Connecteur Logic Apps géré](/connectors/freshdesk/) |  | Synchroniser les incidents |
|


## <a name="have-i-been-pwned"></a>Have I Been Pwned (Est-ce que je me suis fait avoir ?)

| Produit | Composants d’intégration | Pris en charge par | Scénarios |
| --- | --- | --- | --- |
| **Have I Been Pwned** (Est-ce que je me suis fait avoir ?) | Connecteur Logic Apps personnalisé<br><br>Playbooks | Communauté |  |
|

## <a name="hyas"></a>HYAS

| Produit | Composants d’intégration | Pris en charge par | Scénarios |
| --- | --- | --- | --- |
| **HYAS Insight**<br>([Disponible en tant que solution](sentinel-solutions-catalog.md#hyas)) | [Connecteur Logic Apps géré](/connectors/hyasinsight/)<br><br>Playbooks | HYAS |  |
|

## <a name="ibm"></a>IBM

| Produit | Composants d’intégration | Pris en charge par | Scénarios |
| --- | --- | --- | --- |
| **Résilient** | Connecteur Logic Apps personnalisé<br><br>Playbooks | Communauté | Synchroniser les incidents |
|

## <a name="microsoft"></a>Microsoft

| Produit | Composants d’intégration | Pris en charge par | Scénarios |
| --- | --- | --- | --- |
| **Azure DevOps** | Connecteur Logic Apps géré<br><br>Playbooks | Microsoft<br><br>Communauté | Synchroniser les incidents |
| **Pare-feu Azure**<br>([Disponible en tant que solution](sentinel-solutions-catalog.md#azure)) | Connecteur Logic Apps personnalisé<br><br>Playbooks | Microsoft | Bloquer les adresses IP |
| **Azure AD Identity Protection** | [Connecteur Logic Apps géré](/connectors/azureadip/)<br><br>Playbooks | Microsoft<br><br>Communauté | Enrichissement des utilisateurs, <br>Correction des utilisateurs |
| **Azure AD** | [Connecteur Logic Apps géré](/connectors/azuread/)<br><br>Playbooks | Microsoft<br><br>Communauté | Enrichissement des utilisateurs, <br>Correction des utilisateurs |
| **Explorateur de données Azure** | [Connecteur Logic Apps géré](/connectors/kusto/) | Microsoft | Interroger et examiner |
| **Azure Log Analytics Data Collector** | [Connecteur Logic Apps géré](/connectors/azureloganalyticsdatacollector/) | Microsoft<br><br>Communauté | Interroger et examiner |
| **Microsoft Defender for Endpoint** | [Connecteur Logic Apps géré](/connectors/wdatp/)<br><br>Playbooks | Microsoft<br><br>Communauté | Enrichissement des points de terminaison, <br>isoler les points de terminaison |
| **Microsoft Teams** | [Connecteur Logic Apps géré](/connectors/teams/)<br><br>Playbooks | Microsoft<br><br>Communauté | Notifications, <br>Collaboration, <br>créer des réponses impliquées avec l’homme |
|

## <a name="okta"></a>Okta

| Produit | Composants d’intégration | Pris en charge par | Scénarios |
| --- | --- | --- | --- |
| **Okta** | Connecteur Logic Apps géré<br><br>Playbooks | Communauté | Enrichissement des utilisateurs, <br>Correction des utilisateurs |
|

## <a name="palo-alto"></a>Palo Alto

| Produit | Composants d’intégration | Pris en charge par | Scénarios |
| --- | --- | --- | --- |
| **Palo Alto PAN-OS**<br>([Disponible en tant que solution](sentinel-solutions-catalog.md#palo-alto)) | Connecteur Logic Apps personnalisé<br><br>Playbooks | Communauté | Bloquer les adresses IP et les URL |
| **Wildfire** | Connecteur Logic Apps personnalisé<br><br>Playbooks | Communauté | Enrichissement et réponse FileHash |
|

## <a name="proofpoint"></a>Proofpoint

| Produit | Composants d’intégration | Pris en charge par | Scénarios |
| --- | --- | --- | --- |
| **Proofpoint TAP**<br>([Disponible en tant que solution](sentinel-solutions-catalog.md#proofpoint)) | Connecteur Logic Apps personnalisé<br><br>Playbooks | Microsoft | Enrichissement des comptes |
|

## <a name="recorded-future"></a>Avenir enregistré

| Produit | Composants d’intégration | Pris en charge par | Scénarios |
| --- | --- | --- | --- |
| **Intelligence future enregistrée** | [Connecteur Logic Apps géré](/connectors/recordedfuture/)<br><br>Playbooks | Avenir enregistré | Enrichissement des entités |
|

## <a name="reversinglabs"></a>ReversingLabs

| Produit | Composants d’intégration | Pris en charge par | Scénarios |
| --- | --- | --- | --- |
| **TitaniumCloud File Enrichment**<br>([Disponible en tant que solution](sentinel-solutions-catalog.md#reversinglabs)) | [Connecteur Logic Apps géré](/connectors/reversinglabsintelligence/)<br><br>Playbooks | ReversingLabs | Enrichissement FileHash |
|

## <a name="riskiq"></a>RiskIQ

| Produit | Composants d’intégration | Pris en charge par | Scénarios |
| --- | --- | --- | --- |
| **RiskIQ Digital Footprint**<br>([Disponible en tant que solution](sentinel-solutions-catalog.md#riskiq)) | [Connecteur Logic Apps géré](/connectors/riskiqdigitalfootprint/)<br><br>Playbooks | RiskIQ | Enrichissement des entités |
| **RiskIQ Passive Total** | [Connecteur Logic Apps géré](/connectors/riskiqpassivetotal/)<br><br>Playbooks | RiskIQ | Enrichissement des entités |
| **Intelligence de sécurité RiskIQ**<br>([Disponible en tant que solution](sentinel-solutions-catalog.md#riskiq)) | [Connecteur Logic Apps géré](/connectors/riskiqintelligence/)<br><br>Playbooks | RiskIQ | Enrichissement des entités |
|

## <a name="servicenow"></a>ServiceNow

| Produit | Composants d’intégration | Pris en charge par | Scénarios |
| --- | --- | --- | --- |
| **ServiceNow** | [Connecteur Logic Apps géré](/connectors/service-now/)<br><br>Playbooks | Microsoft<br><br>Communauté | Synchroniser les incidents |
|

## <a name="slack"></a>Slack

| Produit | Composants d’intégration | Pris en charge par | Scénarios |
| --- | --- | --- | --- |
| **Slack** | [Connecteur Logic Apps géré](/connectors/slack/)<br><br>Playbooks | Microsoft<br><br>Communauté | Notification, <br>Collaboration |
|

## <a name="virus-total"></a>Nombre Total de virus

| Produit | Composants d’intégration | Pris en charge par | Scénarios |
| --- | --- | --- | --- |
| **Nombre Total de virus** | [Connecteur Logic Apps géré](/connectors/virustotal/)<br><br>Playbooks | Microsoft<br><br>Communauté | Enrichissement des entités |
|

## <a name="vmware"></a>VMware

| Produit | Composants d’intégration | Pris en charge par | Scénarios |
| --- | --- | --- | --- |
| **Cloud noir carbone**<br>([Disponible en tant que solution](sentinel-solutions-catalog.md#vmware)) | Connecteur Logic Apps personnalisé<br><br>Playbooks | Communauté | Enrichissement des points de terminaison, <br>isoler les points de terminaison |
|

## <a name="zendesk"></a>Zendesk

| Produit | Composants d’intégration | Pris en charge par | Scénarios |
| --- | --- | --- | --- |
| **Zendesk** | [Connecteur Logic Apps géré](/connectors/zendesk/)<br><br>Playbooks | Microsoft<br><br>Communauté | Synchroniser les incidents |
|

## <a name="zscaler"></a>Zscaler

| Produit | Composants d’intégration | Pris en charge par | Scénarios |
| --- | --- | --- | --- |
| **Zscaler** | Playbooks | Microsoft | Correction d’URL, <br>enrichissement des incidents |
|

## <a name="next-steps"></a>Étapes suivantes

Dans ce document, vous avez découvert les solutions Azure Sentinel et appris à les trouver et à les déployer.

- En savoir plus sur les [solutions Azure Sentinel](sentinel-solutions.md).
- [Rechercher et déployer des solutions Azure Sentinel](sentinel-solutions-deploy.md).
