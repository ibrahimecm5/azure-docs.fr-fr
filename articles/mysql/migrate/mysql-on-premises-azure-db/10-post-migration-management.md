---
title: 'Migrer MySQL local vers Azure Database pour MySQL : gestion post-migration'
description: Une fois la migration terminée, la phase suivante permet de gérer les nouvelles ressources de charges de travail des données informatiques.
ms.service: mysql
ms.subservice: migration-guide
ms.topic: how-to
author: arunkumarthiags
ms.author: arthiaga
ms.reviewer: maghan
ms.custom: ''
ms.date: 06/21/2021
ms.openlocfilehash: 5c7ef5f12cf4ec1f6776abbc405cf5145c27c0f9
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132297446"
---
# <a name="migrate-mysql-on-premises-to-azure-database-for-mysql-post-migration-management"></a>Migrer MySQL local vers Azure Database pour MySQL : gestion post-migration

[!INCLUDE[applies-to-mysql-single-flexible-server](../../includes/applies-to-mysql-single-flexible-server.md)]

## <a name="prerequisites"></a>Prérequis

[Migration des données avec MySQL Workbench](09-data-migration-with-mysql-workbench.md)

## <a name="monitoring-and-alerts"></a>Supervision et alertes

Une fois la migration terminée, la phase suivante permet de gérer les nouvelles ressources de charges de travail des données informatiques. Les opérations de gestion comprennent les activités du plan de contrôle et du plan de données. Les activités du plan de contrôle sont celles liées aux ressources Azure par opposition au plan de données, qui se trouve **à l’intérieur** de la ressource Azure (dans ce cas, MySQL).

Azure Database pour MySQL offre la possibilité de surveiller ces deux types d’activités opérationnelles à l’aide d’outils Azure, par exemple [Azure Monitor](../../../azure-monitor/overview.md), [Log Analytics](../../../azure-monitor/logs/design-logs-deployment.md) et [Microsoft Sentinel](../../../sentinel/overview.md). Outre les outils basés sur Azure, les systèmes d’informations de sécurité et de gestion d’événements (SIEM) peuvent également être configurés pour consommer ces journaux.

Quel que soit l’outil utilisé pour surveiller les nouvelles charges de travail informatiques, des alertes doivent être créées pour avertir les administrateurs Azure et les Administrateurs de base de données de toute activité suspecte. Si un événement d’alerte particulier a un chemin de correction bien défini, les alertes peuvent déclencher des [runbooks Azure](../../../automation/learn/powershell-runbook-managed-identity.md) automatisés pour traiter l’événement.

La première étape de la création d’un environnement entièrement surveillé consiste à permettre aux données de journal MySQL d’être transmises à Azure Monitor. Pour plus d’informations, consultez [Configurer et consulter les journaux d’audit Azure Database pour MySQL dans le portail Azure](../../howto-configure-audit-logs-portal.md).

Une fois que les données de journal sont transmises, utilisez le langage de requête [KQL (Kusto Query Language)](/azure/data-explorer/kusto/query/) pour interroger les différentes informations de journal. Les administrateurs qui ne sont pas familiarisés avec le langage KQL peuvent trouver une aide-mémoire SQL vers KQL [ici](/azure/data-explorer/kusto/query/sqlcheatsheet) ou sur la page [Bien démarrer avec les requêtes de journal dans Azure Monitor](../../../azure-monitor/logs/get-started-queries.md).

Par exemple, pour connaître l’utilisation de la mémoire d’Azure Database pour MySQL :

```
AzureMetrics
| where TimeGenerated \> ago(15m)
| limit 10
| where ResourceProvider == "MICROSOFT.DBFORMYSQL"
| where MetricName == "memory\_percent"
| project TimeGenerated, Total, Maximum, Minimum, TimeGrain, UnitName 
| top 1 by TimeGenerated
```
Pour connaître l’utilisation de l’UC :

```
AzureMetrics
| where TimeGenerated \> ago(15m)
| limit 10
| where ResourceProvider == "MICROSOFT.DBFORMYSQL"
| where MetricName == "cpu\_percent"
| project TimeGenerated, Total, Maximum, Minimum, TimeGrain, UnitName 
| top 1 by TimeGenerated
```
Une fois que vous avez créé la requête KQL, vous pouvez ensuite créer des [alertes de journal](../../../azure-monitor/alerts/alerts-unified-log.md) en fonction de ces requêtes.

## <a name="server-parameters"></a>Paramètres de serveur

Dans le cadre de la migration, il est probable que les [paramètres de serveur](../../concepts-server-parameters.md) locaux aient été modifiés pour prendre en charge une sortie rapide. De même, des modifications ont été apportées aux paramètres d’Azure Database pour MySQL afin de prendre en charge une entrée rapide. Après la migration, les paramètres de serveur Azure doivent être redéfinis sur leurs valeurs d’origine optimisées pour les charges de travail locales.

Toutefois, veillez à examiner les paramètres de serveur et à les modifier en fonction de la charge de travail et de l’environnement. Certaines valeurs qui étaient idéales pour un environnement local peuvent ne pas être optimales pour un environnement informatique. En outre, lorsque vous envisagez de migrer les paramètres locaux actuels vers Azure, vérifiez qu’ils peuvent effectivement être définis.

Certains paramètres ne peuvent pas être modifiés dans Azure Database pour MySQL.

## <a name="powershell-module"></a>Module PowerShell

Le portail Azure et Windows PowerShell peuvent être utilisés pour gérer l’instance Azure Database pour MySQL. Pour commencer à utiliser PowerShell, installez les cmdlets Azure PowerShell pour MySQL à l’aide de la commande PowerShell suivante :

`Install-Module -Name Az.MySql`

Une fois les modules installés, référez-vous à des tutoriels tels que ceux qui suivent pour découvrir les façons dont vous pouvez tirer parti des scripts pour vos activités de gestion :

  - [Tutoriel : Concevoir une base de données Azure Database pour MySQL à l’aide de PowerShell](../../tutorial-design-database-using-powershell.md)

  - [Guide pratique pour sauvegarder et restaurer un serveur Azure Database pour MySQL à l’aide de PowerShell](../../howto-restore-server-powershell.md)

  - [Configurer les paramètres de serveur dans Azure Database pour MySQL à l’aide de PowerShell](../../howto-configure-server-parameters-using-powershell.md)

  - [Croissance automatique du stockage sur le serveur Azure Database pour MySQL à l’aide de PowerShell](../../howto-auto-grow-storage-powershell.md)

  - [Créer et gérer des réplicas en lecture dans Azure Database pour MySQL à l’aide de PowerShell](../../howto-read-replicas-powershell.md)

  - [Redémarrer un serveur Azure Database pour MySQL à l’aide de PowerShell](../../howto-restart-server-powershell.md)

## <a name="azure-database-for-mysql-upgrade-process"></a>Processus de mise à niveau d’Azure Database pour MySQL

Azure Database pour MySQL étant une offre PaaS, les administrateurs ne sont pas responsables de la gestion des mises à jour sur le système d’exploitation ou le logiciel MySQL. Toutefois, il est important de savoir que le processus de mise à niveau peut être aléatoire et que, lors de son déploiement, il peut arrêter les charges de travail du serveur MySQL. Prévoyez ces temps d’arrêt en redirigeant les charges de travail vers un réplica en lecture au cas où une instance en particulier passerait en mode maintenance.

> [!NOTE]
> Ce style d’architecture de basculement peut nécessiter des modifications de la couche de données des applications pour qu’elle prenne en charge ce type de scénario de basculement. Si le réplica en lecture est conservé en tant que réplica de lecture et n’est pas promu, l’application peut uniquement lire les données et est susceptible d’échouer quand une opération tente d’écrire des informations dans la base de données.

La fonctionnalité [Notification de maintenance planifiée](../../concepts-monitoring.md#planned-maintenance-notification) informe les propriétaires de ressources jusqu’à 72 heures avant l’installation d’une mise à jour ou d’un correctif de sécurité critique. Les Administrateurs de base de données peuvent être amenés à informer les utilisateurs de l’application de la maintenance planifiée ou non planifiée.

> [!NOTE]
> Les notifications de maintenance d’Azure Database pour MySQL sont extrêmement importantes. La maintenance de la base de données peut mettre hors service votre base de données et les applications connectées pendant un certain temps.

## <a name="wwi-scenario"></a>Scénario WWI

La société WWI a décidé d’utiliser les journaux d’activité Azure et d’activer la journalisation MySQL pour les transmettre à un [espace de travail Log Analytics](../../../azure-monitor/logs/design-logs-deployment.md). Cet espace de travail est configuré pour faire partie de [Microsoft Sentinel](../../../sentinel/index.yml) de manière à ce que tous les événements d’[analyse des menaces](../../concepts-security.md#threat-protection) soient signalés et que des incidents soient créés.

Les Administrateurs de base de données MySQL ont installé les [cmdlets Azure PowerShell](../../quickstart-create-mysql-server-database-using-azure-powershell.md) pour Azure Database pour MySQL afin d’automatiser la gestion du serveur MySQL au lieu de devoir se connecter au portail Azure à chaque fois.

## <a name="management-checklist"></a>Liste de vérification de gestion

  - Créez des alertes de ressource pour des éléments courants tels que l’UC et la mémoire.

  - Assurez-vous que les paramètres de serveur sont configurés pour la charge de travail de données cible après la migration.

  - Scriptez les tâches administratives courantes.

  - Configurez des notifications pour les événements de maintenance, tels que les mises à niveau et les patchs. Informez les utilisateurs le cas échéant.  


## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Optimisation](./11-optimization.md)
