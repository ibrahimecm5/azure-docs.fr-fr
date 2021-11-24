---
title: Gérer l’accès aux données de Microsoft Sentinel par ressources | Microsoft Docs
description: Cet article explique comment gérer l’accès aux données de Microsoft Sentinel par les ressources auxquelles un utilisateur peut accéder. Gérer l’accès par ressource vous permet d’accorder un accès uniquement à des données spécifiques, plutôt qu’à la totalité de l’expérience Microsoft Sentinel. Cette méthode est également appelée contrôle d’accès en fonction du rôle (RBAC) dans le contexte de la ressource.
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
ms.topic: conceptual
ms.date: 11/09/2021
ms.author: bagol
ms.custom: ignite-fall-2021
ms.openlocfilehash: 38fd7f30dc412872ccdb6817ae1f502b78a77641
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132522495"
---
# <a name="manage-access-to-microsoft-sentinel-data-by-resource"></a>Gérer l’accès aux données de Microsoft Sentinel par ressources

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Généralement, les utilisateurs qui ont accès à un espace de travail Microsoft Sentinel ont également accès à toutes les données de l’espace de travail, y compris au contenu de sécurité. Les administrateurs peuvent utiliser des [rôles Azure](roles.md) pour configurer l’accès à des fonctionnalités spécifiques dans Microsoft Sentinel, en fonction des exigences d’accès de leur équipe.

Toutefois, il se peut que certains utilisateurs n’aient besoin d’accéder qu’à des données spécifiques dans votre espace de travail Microsoft Sentinel, mais ne doivent pas avoir accès à tout l’environnement Microsoft Sentinel. Par exemple, vous pouvez autoriser une équipe d’opérations autres que de sécurité à accéder aux données d’événements Windows pour les serveurs dont elle est propriétaire.

Dans ce cas, nous vous recommandons de configurer votre contrôle d’accès en fonction du rôle (RBAC) en fonction des ressources auxquelles vos utilisateurs sont autorisés à accéder, au lieu de leur donner accès à l’espace de travail Microsoft Sentinel ou à des fonctionnalités spécifiques de Microsoft Sentinel. Cette méthode est également appelée configuration de **RBAC dans le contexte de la ressource**.

Quand des utilisateurs ont accès à des données de Microsoft Sentinel via les ressources auxquelles ils peuvent accéder au lieu de l’espace de travail Microsoft Sentinel, ils peuvent consulter les journaux et les classeurs à l’aide des méthodes suivantes :

- **Via la ressource elle-même**, par exemple, une machine virtuelle Azure. Utilisez cette méthode pour afficher uniquement les journaux et classeurs d’une ressource spécifique.

- **Via Azure Monitor**. Utilisez cette méthode quand vous souhaitez créer des requêtes qui s’étendent sur plusieurs ressources et/ou groupes de ressources. Quand vous accédez à des journaux et classeurs dans Azure Monitor, définissez votre étendue sur un ou plusieurs groupes de ressources ou ressources spécifiques.

Activez un RBAC dans le contexte de la ressource dans Azure Monitor. Pour plus d’informations, consultez [Gérer l’accès aux données du journal et aux espaces de travail dans Azure Monitor](../azure-monitor/logs/manage-access.md).

> [!NOTE]
> Si vos données ne sont pas des ressources Azure, telles que des données Syslog, CEF ou AAD, ou des données recueillies par un collecteur personnalisé, vous devez configurer manuellement l’ID de ressource utilisé pour identifier les données et autoriser l’accès à celles-ci. Pour plus d’informations, consultez [Configurer explicitement un RBAC dans le contexte de la ressource](#explicitly-configure-resource-context-rbac).
>
> En outre, les [fonctions](../azure-monitor/logs/functions.md) et les recherches enregistrées ne sont pas prises en charge dans les contextes centrés sur les ressources. Par conséquent, les fonctionnalités de Microsoft Sentinel telles que l’analyse et la [normalisation](normalization.md) ne sont pas prises en charge pour le RBAC dans le contexte de la ressource dans Microsoft Sentinel.
> 

## <a name="scenarios-for-resource-context-rbac"></a>Scénarios pour un RBAC dans le contexte de la ressource

Le tableau suivant met en évidence les scénarios dans lesquels un RBAC dans le contexte de la ressource est le plus utile. Notez les différences d’exigences d’accès entre équipes SOC et non-SOC.

| Type d’exigence |Équipe SOC  |Équipe non-SOC  |
|---------|---------|---------|
|**autorisations**     | L’espace de travail entier        |   Ressources spécifiques uniquement      |
|**Accès aux données**     |  Toutes les données de l’espace de travail       | Uniquement les données de ressources auxquelles l’équipe est autorisée à accéder        |
|**Expérience**     |  Expérience Microsoft Sentinel complète, éventuellement limitée par les [autorisations fonctionnelles](roles.md) attribuées à l’utilisateur       |  Requêtes de journal et classeurs uniquement       |
|     |         |         |

Si votre équipe a des exigences d’accès similaires à celles de l’équipe non-SOC décrites dans le tableau ci-dessus, un RBAC dans le contexte de la ressource peut être une bonne solution pour votre organisation.

## <a name="alternative-methods-for-implementing-resource-context-rbac"></a>Autres méthodes pour implémenter un RBAC dans le contexte de la ressource

Selon les autorisations requises dans votre organisation, il se peut que l’utilisation d’un RBAC dans le contexte de la ressource n’offre pas une solution complète.

La liste suivante décrit des situations dans lesquelles d’autres solutions d’accès aux données peuvent mieux répondre à vos besoins :

|Scénario  |Solution  |
|---------|---------|
|**Une filiale a une équipe SOC qui requiert une expérience Microsoft Sentinel complète**.     |  Dans ce cas, utilisez une architecture à plusieurs espaces de travail pour séparer vos autorisations de données. <br><br>Pour plus d'informations, consultez les pages suivantes : <br>- [Étendre Microsoft Sentinel dans les espaces de travail et les locataires](extend-sentinel-across-workspaces-tenants.md)<br>    - [Utiliser les incidents dans plusieurs espaces de travail simultanément](multiple-workspace-view.md)          |
|**Vous souhaitez donner accès à un type d’événement spécifique**.     |  Par exemple, accorder à un administrateur Windows l’accès aux événements de sécurité Windows dans tous les systèmes. <br><br>Dans de tels cas, utilisez un [RBAC au niveau des tables](https://techcommunity.microsoft.com/t5/azure-sentinel/table-level-rbac-in-azure-sentinel/ba-p/965043) afin de définir des autorisations pour chaque table.       |
| **Limiter l’accès à un niveau plus granulaire, soit non basé sur la ressource, soit uniquement à un sous-ensemble des champs d’un événement**   |   Par exemple, vous pourriez souhaiter limiter l’accès aux journaux Office 365 en fonction de la filiale d’un utilisateur. <br><br>Dans ce cas, vous pouvez donner accès aux données en utilisant l’intégration avec les [tableaux de bord et rapports Power bi](../azure-monitor/logs/log-powerbi.md).      |
| | |

## <a name="explicitly-configure-resource-context-rbac"></a>Configurer explicitement un RBAC dans le contexte de la ressource

Suivez les étapes suivantes si vous souhaitez configurer un RBAC dans le contexte de la ressource tandis que vos données ne sont pas des ressources Azure.

Par exemple, les données dans votre espace de travail Microsoft Sentinel qui ne sont pas des ressources Azure incluent des données Syslog, CEF ou AAD, ou des données recueillies par un collecteur personnalisé.

**Pour configurer explicitement un RBAC dans le contexte de la ressource** :

1. Assurez-vous que vous avez [activé un RBAC dans le contexte de la ressource](../azure-monitor/logs/manage-access.md) dans Azure Monitor. 

1. [Créez un groupe de ressources](../azure-resource-manager/management/manage-resource-groups-portal.md) pour chaque équipe d’utilisateurs qui a besoin d’accéder à vos ressources, mais pas à l’intégralité de l’environnement Microsoft Sentinel.

    Attribuez des [autorisations de lecture du journal](../azure-monitor/logs/manage-access.md#resource-permissions) à chacun des membres de l’équipe.

1. Attribuez des ressources aux groupes d’équipes de ressources que vous avez créés et balisez les événements avec les ID de ressource appropriés.

    Quand des ressources Azure envoient des données à Microsoft Sentinel, les enregistrements de journal sont automatiquement marqués avec l’ID de ressource de la source de données.

    > [!TIP]
    > Nous vous recommandons de regrouper les ressources auxquelles vous accordez l’accès sous un groupe de ressources spécifique créé à cet effet.
    >
    > Si ce n’est pas possible, assurez-vous que votre équipe dispose d’autorisations de lecture de journal directement sur les ressources auxquelles vous souhaitez qu’elle puisse accéder.
    >

    Pour plus d’informations sur les ID de ressources, consultez les rubriques suivantes :

    - [ID de ressources avec transfert de journal](#resource-ids-with-log-forwarding)
    - [ID de ressources avec collecte Logstash](#resource-ids-with-logstash-collection)
    - [ID de ressources avec la collecte de l’API Log Analytics](#resource-ids-with-the-log-analytics-api-collection)

### <a name="resource-ids-with-log-forwarding"></a>ID de ressources avec transfert de journal

Quand les événements sont collectés à l’aide de [Common Event Format (CEF)](connect-common-event-format.md) ou de [Syslog](connect-syslog.md), un transfert de journal est utilisé pour collecter les événements de plusieurs systèmes sources.

Par exemple, quand une machine virtuelle de transfert CEF ou Syslog écoute les sources qui envoient des événements Syslog et les transfère à Microsoft Sentinel, l’ID de ressource de la machine virtuelle de transfert de journal est attribué à tous les événements qu’elle transfère.

Si vous avez plusieurs équipes, assurez-vous que vous avez des machines virtuelles de transfert de journal distinctes traitant les événements pour chaque équipe.

Par exemple, la séparation de vos machines virtuelles garantit que les événements Syslog qui appartiennent à l’équipe A sont collectés à l’aide de la machine virtuelle collecteur A.

> [!TIP]
> - Quand vous utilisez une machine virtuelle locale ou une autre machine virtuelle dans le cloud, telle que AWS, en tant que redirecteur de journal, assurez-vous qu’elle possède un ID de ressource en implémentant [Azure Arc](../azure-arc/servers/overview.md).
> - Pour mettre à l’échelle votre environnement de machine virtuelle de transfert de journal, pensez à créer un [groupe de machines virtuelles identiques](https://techcommunity.microsoft.com/t5/azure-sentinel/scaling-up-syslog-cef-collection/ba-p/1185854) pour collecter vos journaux CEF et Syslog.


### <a name="resource-ids-with-logstash-collection"></a>ID de ressource avec collecte Logstash

Si vous collectez vos données à l’aide du [plug-in de sortie Logstash](connect-logstash.md) de Microsoft Sentinel, utilisez le champ **azure_resource_id** pour configurer votre collecteur personnalisé de sorte qu’il inclue l’ID de ressource dans votre sortie.

Si vous utilisez un RBAC dans le contexte de la ressource et souhaitez que les événements collectés par l’API soient disponibles pour des utilisateurs spécifiques, utilisez l’ID de ressource du groupe de ressources que vous avez [créé pour vos utilisateurs](#explicitly-configure-resource-context-rbac).

Par exemple, le code suivant illustre un exemple de fichier de configuration Logstash :

``` ruby
 input {
     beats {
         port => "5044"
     }
 }
 filter {
 }
 output {
     microsoft-logstash-output-azure-loganalytics {
       workspace_id => "4g5tad2b-a4u4-147v-a4r7-23148a5f2c21" # <your workspace id>
       workspace_key => "u/saRtY0JGHJ4Ce93g5WQ3Lk50ZnZ8ugfd74nk78RPLPP/KgfnjU5478Ndh64sNfdrsMni975HJP6lp==" # <your workspace key>
       custom_log_table_name => "tableName"
       azure_resource_id => "/subscriptions/wvvu95a2-99u4-uanb-hlbg-2vatvgqtyk7b/resourceGroups/contosotest" # <your resource ID>   
     }
 }
```

> [!TIP]
> Vous pouvez ajouter plusieurs sections `output` pour différencier les balises appliquées à différents événements.
>
### <a name="resource-ids-with-the-log-analytics-api-collection"></a>ID de ressource avec collecte de l’API Log Analytics

Quand vous collectez à l’aide de l’[API de collecteur de données Log Analytics](../azure-monitor/logs/data-collector-api.md), vous pouvez attribuer à des événements un ID de ressource à l’aide de l’en-tête de requête HTTP [*x-ms-AzureResourceId*](../azure-monitor/logs/data-collector-api.md#request-headers).

Si vous utilisez un RBAC dans le contexte de la ressource et souhaitez que les événements collectés par l’API soient disponibles pour des utilisateurs spécifiques, utilisez l’ID de ressource du groupe de ressources que vous avez [créé pour vos utilisateurs](#explicitly-configure-resource-context-rbac).



## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez [Autorisations dans Microsoft Sentinel](roles.md).
