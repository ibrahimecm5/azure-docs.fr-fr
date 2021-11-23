---
title: 'Meilleures pratiques d’Azure Monitor : Configurer la collecte de données'
description: Aide et recommandations relatives à la configuration de la collecte de données dans Azure Monitor.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/18/2021
ms.openlocfilehash: b5522cb26f7f8fe486b4b690938a23c7e75c746a
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132315955"
---
# <a name="azure-monitor-best-practices---configure-data-collection"></a>Meilleures pratiques d’Azure Monitor : Configurer la collecte de données
Cet article fait partie du scénario [Recommandations pour la configuration d’Azure Monitor](best-practices.md). Il décrit les étapes recommandées pour configurer la collecte des données nécessaires à l’activation des fonctionnalités d’Azure Monitor pour vos applications et ressources Azure et hybrides.

> [!IMPORTANT]
> Les fonctionnalités d’Azure Monitor et leur configuration varient selon les besoins de votre entreprise, en fonction du coût des fonctionnalités activées. Chaque étape ci-dessous indiquera s’il existe des coûts potentiels, et vous devez évaluer ces coûts avant de poursuivre. Pour obtenir des détails sur la tarification, consultez [Tarification Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/).

## <a name="create-log-analytics-workspace"></a>Créer un espace de travail Log Analytics
Vous avez besoin d’au moins un espace de travail Log Analytics pour activer les [Journaux Azure Monitor](logs/data-platform-logs.md), requis pour la collecte de ces données sous forme de journaux à partir de ressources Azure, la collecte de données à partir du système d’exploitation invité des machines virtuelles Azure et la plupart des Azure Monitor Insights. D’autres services, tels que Microsoft Sentinel et Microsoft Defender pour le cloud, utilisent également un espace de travail Log Analytics et peuvent partager le même que celui utilisé pour Azure Monitor. Vous pouvez commencer avec un seul espace de travail pour prendre en charge cette analyse, mais consultez [Conception de votre déploiement de journaux Azure Monitor](logs/design-logs-deployment.md) pour obtenir des conseils sur l’utilisation de plusieurs espaces de travail.

Il n’y a aucun coût pour créer un espace de travail Log Analytics, mais il y a des frais potentiels une fois que vous avez configuré les données à collecter dans celui-ci. Pour plus d'informations, consultez [Gérer l’utilisation et les coûts avec les journaux Azure Monitor](logs/manage-cost-storage.md).  

Pour créer un espace de travail Log Analytics, consultez [Créer un espace de travail Log Analytics dans le portail Azure](logs/quick-create-workspace.md). Voir [Gérer l’accès aux données de journal et aux espaces de travail dans Azure Monitor](logs/manage-access.md) pour configurer l’accès. Vous pouvez utiliser des méthodes évolutives, telles que des modèles Resource Manager, pour configurer les espaces de travail. Toutefois, cela n’est généralement pas nécessaire, car la plupart des environnements n’en requièrent qu’un nombre minimal.

## <a name="collect-data-from-azure-resources"></a>Recueillir des données à partir de ressources Azure
Une analyse des ressources Azure est disponible automatiquement sans aucune configuration requise, tandis que vous devez effectuer des étapes de configuration pour collecter des données d’analyse supplémentaires. Le tableau suivant illustre les étapes de configuration requises pour collecter toutes les données disponibles à partir de vos ressources Azure, notamment les données d’étape envoyées aux métriques Azure Monitor et aux journaux Azure Monitor. Chaque étape est décrite en détail dans les sections ci-dessous.

[![Déployer une surveillance des ressources Azure](media/best-practices-data-collection/best-practices-azure-resources.png)](media/best-practices-data-collection/best-practices-azure-resources.png#lightbox)

### <a name="collect-tenant-and-subscription-logs"></a>Collecter les journaux des locataires et des abonnements
Tandis que les journaux [Azure Active Directory](../active-directory/reports-monitoring/overview-reports.md) pour votre locataire et que le [journal d’activité](essentials/platform-logs-overview.md) pour votre abonnement sont collectées automatiquement, leur envoi à un espace de travail Log Analytics vous permet d’analyser ces événements avec d’autres données de journal à l’aide des requêtes de journal dans Log Analytics. Cela vous permet également de créer des alertes de requête de journal qui sont le seul moyen d’alerter sur les journaux Azure Active Directory et de fournir une logique plus complexe que les alertes du journal d’activité.

Il n’y a aucun coût pour envoyer le journal d’activité à un espace de travail, mais il existe une ingestion de données et des frais de rétention pour les journaux Azure Active Directory. 

Consultez [Intégrer des journaux Azure AD avec des journaux Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) et [Créer des paramètres de diagnostic pour envoyer des journaux et des métriques de plateforme vers différentes destinations](essentials/diagnostic-settings.md) pour créer un paramètre de diagnostic pour votre locataire et votre abonnement afin d’envoyer des entrées de journal à votre espace de travail Log Analytics. 




### <a name="collect-resource-logs-and-platform-metrics"></a>Collecter les journaux de ressources et les métriques de plateforme
Les ressources dans Azure génèrent automatiquement des [journaux de ressources](essentials/platform-logs-overview.md) qui fournissent des détails sur les opérations effectuées au sein de la ressource. Contrairement aux métriques de plateforme, vous devez cependant configurer les journaux de ressources à collecter. Créez un paramètre de diagnostic pour les envoyer à un espace de travail Log Analytics et les combiner avec les autres données utilisées par les journaux d’activité Azure Monitor. Le même paramètre de diagnostic peut être utilisé pour envoyer également les métriques de la plateforme pour la plupart des ressources vers le même espace de travail, ce qui vous permet d’analyser les données de métriques à l’aide de requêtes de journal avec d’autres données collectées.

La collecte des journaux de ressources dans votre espace de travail Log Analytics a un coût, aussi ne sélectionnez que les catégories de journaux contenant des données précieuses. La collecte de toutes les catégories entraînera des coûts pour une collecte de données de faible valeur. Consultez la documentation relative à la surveillance pour chaque service Azure pour obtenir une description des catégories et des recommandations à collecter. Consultez également [Gérer l’utilisation et les coûts avec les journaux Azure Monitor](logs/manage-cost-storage.md) pour plus d’informations sur l’optimisation du coût de votre collection de journaux.

Pour créer des paramètres de diagnostic dans une ressource Azure, consultez [Créer un paramètre de diagnostic pour collecter les journaux et les métriques des ressources dans Azure](essentials/diagnostic-settings.md#create-in-azure-portal). 

Sachant qu’un paramètre de diagnostic doit être créé pour chaque ressource Azure, utilisez Azure Policy pour qu’un paramètre de diagnostic soit créé automatiquement à chaque création de ressource. Chaque type de ressource Azure est constitué d’un ensemble unique de catégories qui doivent être listées dans le paramètre de diagnostic. Pour cette raison, chaque type de ressource a besoin d’une définition de stratégie distincte. Certains types de ressources comportent des définitions de stratégie intégrées que vous pouvez affecter sans modification. Pour les autres types de ressources, vous devez créer une définition personnalisée.

Consultez [Créer à grande échelle à l’aide d’Azure Policy](essentials/diagnostic-settings.md#create-at-scale-using-azure-policy) pour découvrir un processus de création de définitions de stratégie pour un service Azure particulier et connaître les détails relatifs à la création de paramètres de diagnostic à grande échelle.

### <a name="enable-insights"></a>Activer les insights
Les insights offrent une expérience de supervision spécialisée pour un service particulier. Ils utilisent les mêmes données déjà collectées, telles que les métriques de plateforme et les journaux de ressources, mais ils fournissent des classeurs personnalisés qui vous aident à identifier et à analyser les données les plus critiques. La plupart des insights seront disponibles dans le portail Azure sans aucune configuration requise autre que la collecte des journaux de ressources pour ce service. Consultez la documentation relative à la surveillance de chaque service Azure pour déterminer s’il dispose d’insights et s’il nécessite une configuration.

Les insights sont gratuits, mais les données qu’ils collectent peuvent vous être facturées.

Voir [Qu’est-ce qui est surveillé par Azure Monitor ?](monitor-reference.md) pour obtenir la liste des analyses et des solutions disponibles dans Azure Monitor. Reportez-vous à la documentation de chacune pour obtenir des informations de configuration ou de tarification spécifiques.

> [!IMPORTANT]
> Les insights suivants sont nettement plus complexes que les autres et font l’objet d’une aide supplémentaire pour leur configuration.
> 
> - [VM Insights](#monitor-virtual-machines)
> - [Container Insights](#monitor-containers)
> - [Surveiller les applications](#monitor-applications)


## <a name="monitor-virtual-machines"></a>Surveillance des machines virtuelles
Les machines virtuelles génèrent des données similaires à celles des autres ressources Azure, mais elles nécessitent un agent pour collecter les données du système d’exploitation invité. Les machines virtuelles ont également des exigences uniques en matière de surveillance en raison des différentes charges de travail qui s’exécutent sur elles. Pour accéder à un scénario dédié à la surveillance des machines virtuelles avec Azure Monitor, consultez [Surveillance des machines virtuelles Azure avec Azure Monitor](vm/monitor-vm-azure.md).

## <a name="monitor-containers"></a>Analyser les conteneurs
Les machines virtuelles génèrent des données similaires à celles des autres ressources Azure, mais elles nécessitent une version conteneurisée de l’agent de Log Analytics pour collecter les données requises. Container Insights vous aide à préparer votre environnement conteneurisé pour la surveillance et fonctionne conjointement avec des outils tiers afin de fournir une surveillance complète d’AKS et des flux de travail qu’il prend en charge. Pour accéder à un scénario dédié à la surveillance d’AKS avec Azure Monitor, consultez [Surveillance d’Azure Kubernetes Service (AKS) avec Azure Monitor](../aks/monitor-aks.md?toc=/azure/azure-monitor/toc.json).

## <a name="monitor-applications"></a>Surveillance des applications
Azure Monitor surveille vos applications personnalisées à l’aide d’[Application Insights](app/app-insights-overview.md), que vous devez configurer pour chaque application que vous souhaitez analyser. Le processus de configuration varie selon le type d’application surveillé et le type d’analyse que vous souhaitez effectuer. Les données collectées par Application Insights sont stockées dans les mesures Azure Monitor, les journaux Azure Monitor et le stockage d’objets BLOB Azure, selon la fonctionnalité. Les données de performances sont stockées à la fois dans les métriques Azure Monitor et dans les journaux Azure Monitor sans aucune configuration supplémentaire.

### <a name="create-an-application-resource"></a>Créer une ressource d’application
Application Insights est la fonctionnalité d’Azure Monitor permettant de surveiller vos applications natives Cloud et hybrides.

Vous devez créer une ressource dans Application Insights pour chaque application que vous allez analyser. Les données de journal collectées par Application Insights sont stockées dans des journaux d’activité Azure Monitor pour une application basée sur un espace de travail. Les données de journal pour les applications classiques sont stockées séparément de votre espace de travail Log Analytics, comme décrit dans la section [Structure des données](logs/data-platform-logs.md#data-structure).

 Lorsque vous créez l’application, vous devez choisir entre Classique ou Basée sur un espace de travail. Consultez [Créer une ressource Application Insights](app/create-new-resource.md) pour créer une application classique. Pour créer une application basée sur un espace de travail, consultez [Ressources Application Insights basées sur un espace de travail (version préliminaire)](app/create-workspace-resource.md).


 Une décision fondamentale en matière de conception est de savoir s’il faut utiliser une ressource d’application distincte ou unique pour plusieurs applications. Des ressources distinctes permettent de réduire les coûts et d’éviter de mélanger les données de différentes applications, mais une ressource unique peut simplifier votre surveillance en regroupant toutes les télémétries pertinentes. Pour connaître les critères détaillés de cette décision de conception, consultez [Combien de ressources Application Insights déployer](app/separate-resources.md).



### <a name="configure-codeless-or-code-based-monitoring"></a>Configurer la surveillance sans code ou basée sur le code
Pour activer l’analyse pour une application, vous devez décider si vous allez utiliser l’analyse sans code ou basée sur le code. Le processus de configuration varie en fonction de cette décision et du type d’application que vous allez surveiller.

**La surveillance sans code** est plus facile à implémenter et peut être configurée après le développement de votre code. Elle ne nécessite aucune mise à jour de votre code. Pour plus d’informations sur l’activation de la surveillance en fonction de votre application, consultez les ressources suivantes.

- [Applications hébergées sur Azure Web Apps](app/azure-web-apps.md)
- [Applications Java](app/java-in-process-agent.md)
- [Applications ASP.NET hébergées dans IIS sur une machine virtuelle Azure ou un groupe de machines virtuelles identiques Azure](app/azure-vm-vmss-apps.md)
- [Applications ASP.NET hébergées par IIS localement](app/status-monitor-v2-overview.md)


**La surveillance basée sur le code** est plus personnalisable et collecte des données de télémétrie supplémentaires, mais elle nécessite l’ajout d’une dépendance à votre code sur les packages NuGet du kit de développement logiciel (SDK) Application Insights. Pour plus d’informations sur l’activation de la surveillance en fonction de votre application, consultez les ressources suivantes.

- [Applications ASP.NET](app/asp-net.md)
- [Applications ASP.NET Core](app/asp-net-core.md)
- [Applications de console .NET](app/console.md)
- [Java](app/java-in-process-agent.md)
- [Node.JS](app/nodejs.md)
- [Python](app/opencensus-python.md)
- [autres plateformes](app/platforms.md)

### <a name="configure-availability-testing"></a>Configurer un test de disponibilité
Les tests de disponibilité dans Application Insights sont des tests périodiques qui surveillent la disponibilité et la réactivité de votre application à intervalles réguliers à partir de points situés dans le monde entier. Vous pouvez créer un test ping simple gratuit ou créer une séquence de requêtes Web pour simuler des transactions utilisateur associées à un coût. 

Consultez [Surveiller la disponibilité de tout site Web](app/monitor-web-app-availability.md) pour résumer les différents types de test et les détails de leur création.

### <a name="configure-profiler"></a>Configurer Profiler
Profiler dans Application Insights fournit des traces de performances pour les applications .NET. Profiler vous aide à identifier le chemin de code « chaud » qui prend le plus de temps pendant le traitement d’une demande web particulière. Le processus de configuration de Profiler varie selon le type d’application. 

Voir [Applications de production Profile dans Azure avec Application Insights](app/profiler-overview.md) pour plus de détails sur la configuration de Profiler.

### <a name="configure-snapshot-debugger"></a>Configurer le Débogueur de capture instantanée
Le débogueur de capture instantanée dans Application Insights surveille la télémétrie des exceptions à partir de votre application .NET et recueille des instantanés de vos exceptions les plus fréquentes afin que vous disposiez des informations dont vous avez besoin pour diagnostiquer les problèmes en production. Le processus de configuration du Débogueur de capture instantanée varie selon le type d’application. 

Pour plus d’informations sur la configuration du Débogueur de capture instantanée, consultez [Instantanés de débogage sur les exceptions dans les applications .NET](app/snapshot-debugger.md).

## <a name="next-steps"></a>Étapes suivantes

- Une fois la collecte de données configurée pour toutes vos ressources Azure, consultez [Analyser et visualiser les données](best-practices-analysis.md) pour connaître les options d’analyse de ces données. 
