---
title: Azure Communication Services - Activer Azure Monitor
titleSuffix: An Azure Communication Services concept document
description: Configurer les journaux et les métriques Communication Services avec les paramètres de diagnostic
author: timmitchell
services: azure-communication-services
ms.author: timmitchell
ms.date: 10/25/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.subservice: data
ms.openlocfilehash: f9de0567c3c07a10b780c4932a09e155f0e2ddea
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132138960"
---
# <a name="enable-logs-via-diagnostic-settings-in-azure-monitor"></a>Activer les journaux via Paramètres de diagnostic dans Azure Monitor

## <a name="overview"></a>Vue d’ensemble

Les services de communication fournissent des fonctionnalités de supervision et d’analyse via la [vue d’ensemble des journaux Azure Monitor](/azure/azure-monitor/logs/data-platform-logs) et les [métriques Azure Monitor](/azure/azure-monitor/essentials/data-platform-metrics). Chaque ressource Azure requiert son propre paramètre de diagnostic, qui définit les critères suivants :

- Catégories de journaux et données de métriques envoyées aux destinations définies dans le paramètre. Les catégories disponibles varient en fonction des types de ressources.
- Une ou plusieurs destinations auxquelles envoyer les journaux. Les destinations actuelles sont l’espace de travail Log Analytics, Event Hubs et le Stockage Azure.
- Un seul paramètre de diagnostic ne peut pas définir plus d’une destination. Si vous souhaitez envoyer des données à plus d’un type de destination (par exemple, deux espaces de travail Log Analytics), créez plusieurs paramètres. Chaque ressource peut avoir jusqu’à cinq paramètres de diagnostic.

Vous trouverez ci-dessous des instructions sur la configuration de votre ressource Azure Monitor pour commencer à créer des journaux et des métriques pour vos services de communication. Pour obtenir une documentation détaillée sur l’utilisation de Paramètres de diagnostic dans toutes les ressources Azure, consultez [Paramètres de diagnostic](/azure/azure-monitor/essentials/diagnostic-settings).

Ces instructions s’appliquent aux journaux Communication Services suivants :

- [Résumé des appels et aperçu des journaux d’appel](call-logs-azure-monitor.md)

## <a name="access-diagnostic-settings"></a>Accéder aux paramètres de diagnostic

Pour accéder aux Paramètres de diagnostic pour vos services de communication, commencez par accéder à votre page d’accueil Communication Services dans le Portail Azure :

:::image type="content" source="media\enable-logging\portal-home-go-to-acs-resource.png" alt-text="Ressource Communication Services":::

À partir de là, cliquez sur « Paramètres de diagnostic » dans la section Supervision du volet de navigation de gauche :

:::image type="content" source="media\enable-logging\resource-diagnostic-settings-nav.png" alt-text="Paramètres de diagnostic dans la navigation":::

Cliquez sur le lien « Ajouter un paramètre de diagnostic » (notez les différents journaux et sources de métriques disponibles pour Communication Services) :

:::image type="content" source="media\enable-logging\diagnostic-setting-add.png" alt-text="Détails de la catégorie Paramètres de diagnostic":::

## <a name="adding-a-diagnostic-setting"></a>Ajout d’un paramètre de diagnostic

Vous serez ensuite invité à choisir un nom pour votre paramètre de diagnostic, ce qui est utile si vous avez de nombreuses ressources Azure à analyser. Vous serez également invité à sélectionner les sources de données de journaux et de métriques que vous souhaitez analyser en tant que journaux ou métriques. Pour plus d’informations sur la différence, consultez [Plateforme de données Azure Monitor](/azure/azure-monitor/data-platform).

:::image type="content" source="media\enable-logging\diagnostic-setting-categories-details-acs.png" alt-text="Ajout d’un paramètre de diagnostic":::

## <a name="choose-destinations"></a>Choisir des destinations

Vous serez également invité à sélectionner une destination de stockage des journaux. Les journaux et les métriques de la plate-forme peuvent être envoyés aux destinations indiquées dans le tableau suivant, qui est également repris dans la documentation d’Azure Monitor de manière plus détaillée : [Créer des paramètres de diagnostic pour envoyer des journaux et des métriques de plateforme à différentes destinations](/azure/azure-monitor/essentials/diagnostic-settings?tabs=CMD)

| Destination | Description |
|:------------|:------------|
| [Espace de travail Log Analytics](/azure/azure-monitor/logs/design-logs-deployment) | L’envoi de journaux et de métriques vers un espace de travail Log Analytics vous permet de les analyser avec d’autres données de supervision collectées par Azure Monitor à l’aide de requêtes de journal puissantes, ainsi que d’utiliser d’autres fonctionnalités d’Azure Monitor, telles que les alertes et les visualisations. |
| [Hubs d'événements](/azure/event-hubs/) | L’envoi de journaux et de métriques à Event Hubs vous permet de transmettre en continu des données à des systèmes externes tels que des solutions SIEM tierces et d’autres solutions d’analytique des journaux d’activité. |
| [Compte Azure Storage](/azure/storage/blobs/) | L’archivage des journaux et des métriques dans un compte de stockage Azure est utile à des fins d’audit, d’analyse statique ou de sauvegarde. Par rapport aux journaux d’activité Azure Monitor et à l’espace de travail Log Analytics, le stockage Azure est moins onéreux et les journaux peuvent y être conservés indéfiniment. |

Les paramètres suivants constituent un exemple de ce que vous pouvez voir dans votre ressource Azure Communication Services :

:::image type="content" source="media\enable-logging\diagnostic-setting-destination-acs.png" alt-text="Détails de la destination des paramètres de diagnostic":::

Il s’agit d’options viables et flexibles qui peuvent s’adapter à vos besoins spécifiques en matière de stockage. Toutefois, nous fournissons d’autres fonctionnalités et intégrées d’insights analytiques lorsque l’option Espace de travail Log Analytics est sélectionnée.

## <a name="log-analytics-workspace-for-additional-analytics-features"></a>Espace de travail Log Analytics pour les fonctionnalités d’analytique supplémentaires

En choisissant d’envoyer vos journaux vers une destination [d’espace de travail Log Analytics](/azure/azure-monitor/logs/log-analytics-overview), vous activez davantage de fonctionnalités dans Azure Monitor en général et pour vos services de communication. Log Analytics est un outil du Portail Azure permettant de créer, modifier et exécuter des [requêtes](/azure/azure-monitor/logs/queries) avec des données de vos journaux Azure Monitor, des métriques et des [classeurs](/azure/azure-monitor/visualize/workbooks-overview), des [alertes](/azure/azure-monitor/alerts/alerts-log), des [actions de notification](/azure/azure-monitor/alerts/action-groups), un [accès d’API REST](https://dev.loganalytics.io/), entre autres.

Pour les journaux Communication Services, nous avons fourni un [pack de requêtes par défaut](/azure/azure-monitor/logs/query-packs#default-query-pack) utile pour fournir un ensemble initial d’insights dans le cadre de l’analyse et de la compréhension rapides de vos données. Ces packs de requêtes sont décrits ici : [Analytique des journaux d'activité pour Communication Services](log-analytics.md). Nous avons également créé de nombreuses analyses et visualisations à l’aide de classeurs, qui sont décrits dans : [Classeurs pour les journaux Communication Services](insights.md).
