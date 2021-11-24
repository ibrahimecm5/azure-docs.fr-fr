---
title: Informations de référence sur les données de surveillance App Service
description: Matériel de référence important nécessaire pour analyser App Service
author: msangapu-msft
ms.topic: reference
ms.author: msangapu
ms.service: app-service
ms.custom: subject-monitoring
ms.date: 04/16/2021
ms.openlocfilehash: 914587d60b5932d2f9af6eef400a1a5067974652
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132290549"
---
# <a name="monitoring-app-service-data-reference"></a>Informations de référence sur les données de surveillance App Service

Cette référence s’applique à l’utilisation d’Azure Monitor pour la surveillance d’App Service. Pour plus d’informations sur la collecte et l’analyse des données de supervision concernant App Service, consultez [Supervision d’App Service](monitor-app-service.md).

## <a name="metrics"></a>Mesures

Cette section répertorie toutes les métriques de plateforme collectées automatiquement pour App Service.  

|Type de métrique | Fournisseur de ressources / espace de noms du type<br/> et lien vers des métriques individuelles |
|-------|-----|
| Plans App Service | [Microsoft.Web/serverfarms](../azure-monitor/essentials/metrics-supported.md#microsoftwebserverfarms)
| Applications web | [Microsoft.Web/sites](../azure-monitor/essentials/metrics-supported.md#microsoftwebsites) |
| Emplacements intermédiaires | [Microsoft.Web/sites/slots](../azure-monitor/essentials/metrics-supported.md#microsoftwebsitesslots) 
| Environnement App Service | [Microsoft.Web/hostingEnvironments](../azure-monitor/essentials/metrics-supported.md#microsoftwebhostingenvironments)
| Serveur frontal d’App Service Environment | [Microsoft.Web/hostingEnvironments/multiRolePools](../azure-monitor/essentials/metrics-supported.md#microsoftwebhostingenvironmentsmultirolepools)


Pour plus d’informations, consultez la liste de [toutes les métriques de plateforme prises en charge dans Azure Monitor](../azure-monitor/essentials/metrics-supported.md).


## <a name="metric-dimensions"></a>Dimensions de métrique

Pour plus d’informations sur les dimensions de métrique, consultez [Métriques multidimensionnelles](../azure-monitor/essentials/data-platform-metrics.md#multi-dimensional-metrics).

App Service n’a pas de métriques qui contiennent des dimensions.

## <a name="resource-logs"></a>Journaux d’activité de ressources

Cette section répertorie les types de journaux de ressources que vous pouvez collecter pour App Service. 

| Type de journal | Windows | Conteneur Windows | Linux | Conteneur Linux | Description |
|-|-|-|-|-|-|
| AppServiceConsoleLogs | Java SE et Tomcat | Oui | Oui | Oui | Sortie standard et erreur standard |
| AppServiceHTTPLogs | Oui | Oui | Oui | Oui | Journaux d’activité des serveurs Web |
| AppServiceEnvironmentPlatformLogs | Oui | N/A | Oui | Oui | App Service Environment : mise à l’échelle, modifications de configuration et journaux d’état|
| AppServiceAuditLogs | Oui | Oui | Oui | Oui | Activité de connexion via FTP et Kudu |
| AppServiceFileAuditLogs | Oui | Oui | À confirmer | À confirmer | Modifications de fichier apportées au contenu du site ; **disponible seulement pour le niveau Premium et supérieur** |
| AppServiceAppLogs | ASP.NET | ASP.NET | Images Java SE et Tomcat<sup>1</sup> | Java SE & Tomcat Blessed Images <sup>1</sup> | Journaux d’activité d’application |
| AppServiceIPSecAuditLogs  | Oui | Oui | Oui | Oui | Demandes à partir de règles IP |
| AppServicePlatformLogs  | À confirmer | Oui | Oui | Oui | journaux des opérations du conteneur |
| AppServiceAntivirusScanAuditLogs | Oui | Oui | Oui | Oui | [Journaux de l’analyse antivirus](https://azure.github.io/AppService/2020/12/09/AzMon-AppServiceAntivirusScanAuditLogs.html) avec Microsoft Defender pour le cloud ; **disponibles seulement pour le niveau Premium** | 

<sup>1</sup> Pour les applications Java SE, ajoutez « $WEBSITE_AZMON_PREVIEW_ENABLED » aux paramètres de l’application et définissez-le sur 1 ou true.

Pour référence, consultez la liste de [tous les types de catégories de journaux de ressources pris en charge dans Azure Monitor](../azure-monitor/essentials/resource-logs-schema.md).

## <a name="azure-monitor-logs-tables"></a>Tables Azure Monitor Logs

Azure App Service utilise les tables Kusto des journaux d’activité Azure Monitor. Vous pouvez interroger ces tables à l’aide de Log Analytics. Pour obtenir une liste des tables App Service utilisées par Kusto, consultez la [référence de table des journaux d’activité Azure Monitor – tables App Service](/azure/azure-monitor/reference/tables/tables-resourcetype#app-services). 

## <a name="activity-log"></a>Journal d’activité

Le tableau suivant répertorie les opérations courantes relatives à App Service qui peuvent être créées dans le journal d’activité. Cette liste est non exhaustive.

| Opération | Description |
|:---|:---|
|Créer ou mettre à jour une application web| L’application a été créée ou mise à jour|
|Supprimer une application web| L’application a été supprimée |
|Créer une sauvegarde d’application web| Sauvegarde d’une application|
|Obtenir le profil de publication d’une application web| Téléchargement du profil de publication |
|Publier une application web| Application déployée |
|Redémarrer l’application web| Application redémarrée|
|Démarrer l’application web| Application démarrée |
|Arrêter l’application Web| Application arrêtée|
|Échanger des emplacements d’application web| Les emplacements ont été échangés|
|Obtenir les différences entre les emplacements d’application web| Différences entre les emplacements|
|Appliquer la configuration de l’application web| Application des modifications de configuration|
|Réinitialiser la configuration de l’application web| Réinitialisation des modifications de la configuration|
|Approuve les connexions de point de terminaison privé| Approbation des connexions de point de terminaison privés|
|Tracer le réseau des applications web| Démarrage de la trace du réseau|
|Nouveau mot de passe d’applications web| Nouveau mot de passe créé |
|Obtenir des journaux de conteneurs zippés pour une application web| Obtenir les journaux de conteneur |
|Restaurer une application web à partir d’un blob de sauvegarde| Application restaurée à partir d’une sauvegarde|

Pour plus d’informations sur le schéma des entrées du journal d’activité, consultez [Schéma du journal d’activité](../azure-monitor/essentials/activity-log-schema.md). 

## <a name="see-also"></a>Voir aussi

- Consultez [Supervision d’Azure App Service](monitor-app-service.md) pour obtenir une description de la supervision d’Azure App Service.
- Pour plus d’informations sur le monitoring des ressources Azure, voir [Monitoring des ressources Azure avec Azure Monitor](../azure-monitor/essentials/monitor-azure-resource.md).
