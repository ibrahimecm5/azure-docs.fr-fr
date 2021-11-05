---
title: Utilisation d’Azure Monitor et Application Insights
description: Utilisation d’Azure Monitor et Application Insights avec Open Service Mesh
services: container-service
ms.topic: article
ms.date: 8/26/2021
ms.custom: mvc, devx-track-azurecli
ms.author: pgibson
ms.openlocfilehash: 139c2dbd201791fd7960e66564a293545c3ca524
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131066726"
---
# <a name="open-service-mesh-osm-monitoring-and-observability-using-azure-monitor-and-applications-insights"></a>Surveillance et observabilité d’Open Service Mesh (OSM) à l’aide d’Azure Monitor et Applications Insights

Azure Monitor et Azure Application Insights aident à optimiser la disponibilité et les performances de vos applications et services. Ces services offrent une solution complète pour collecter, analyser et exploiter les données de télémétrie de vos environnements cloud et locaux.

Le module complémentaire OSM AKS sera profondément intégré à ces deux services Azure et fournira une expérience Azure transparente permettant de visualiser les indicateurs clés de performance essentiels fournis par les métriques d’OSM et d’y répondre.

## <a name="enable-azure-monitor"></a>Activer Azure Monitor

Une fois que le module complémentaire AKS OSM a été activé sur le cluster AKS, Azure Monitor doit être activé dans le cluster via le portail Azure. Cliquez sur le cluster AKS, accédez à l’onglet « Informations » sous « Supervision », puis sélectionnez « Activer ».

Une fois Azure Monitor activé, vous devriez être en mesure de voir les pods suivants dans l’espace de noms kube-system :

```
kube-system     omsagent-5pn4c                        1/1     Running   0          24m
kube-system     omsagent-6r6zt                        1/1     Running   0          24m
kube-system     omsagent-j8xrh                        1/1     Running   0          24m
kube-system     omsagent-rs-74b8f7dfd8-rp5vx          1/1     Running   1          24m
```

## <a name="enable-metrics-in-osm-monitored-namespaces"></a>Activer les métriques dans les espaces de noms analysés par OSM

Pour les métriques à récupérer à partir d’un espace de noms spécifique surveillé par la maille, la commande suivante doit être exécutée :

```sh
osm metrics enable --namespace <namespace>
```

Par exemple, si vous exécutez la [démonstration Bookstore](https://release-v0-11.docs.openservicemesh.io/docs/getting_started/quickstart/manual_demo/), vous devez exécuter la commande `osm metrics enable` sur les espaces de noms suivants :

```sh
osm metrics enable --namespace bookbuyer
osm metrics enable --namespace bookstore
osm metrics enable --namespace bookthief
osm metrics enable --namespace bookwarehouse
```

## <a name="apply-configmap"></a>Appliquer ConfigMap

Créez l’élément ConfigMap suivant dans `kube-system`, ce qui indiquera à AzMon quels espaces de noms doivent être surveillés. Par exemple, pour la démonstration Bookbuyer/Bookstore, l’élément ConfigMap se présente comme suit :

```yaml
kind: ConfigMap
apiVersion: v1
data:
  schema-version: v1
  config-version: ver1
  osm-metric-collection-configuration: |-
    # OSM metric collection settings
    [osm_metric_collection_configuration]
      [osm_metric_collection_configuration.settings]
          # Namespaces to monitor
          monitor_namespaces = ["bookstore", "bookbuyer", "bookthief", "bookwarehouse"]
metadata:
  name: container-azm-ms-osmconfig
  namespace: kube-system
```

## <a name="view-metrics-in-the-azure-portal"></a>Afficher les métriques dans le portail Azure

Dans le portail Azure, sélectionnez le cluster Kubernetes, puis l’onglet « Journaux » sous « Supervision ». Vous devriez maintenant être en mesure d’interroger la table `InsightsMetrics` pour afficher les métriques dans les espaces de noms activés. Par exemple, si vous souhaitez afficher les métriques d’envoi pour `bookbuyer`, vous devez utiliser la requête suivante :

```sh
InsightsMetrics
| where Name contains "envoy"
| extend t=parse_json(Tags)
| where t.app == "bookbuyer"
```

## <a name="additional-information"></a>Informations supplémentaires

Pour plus d’informations sur l’activation et la configuration d’Azure Monitor et Azure Application Insights pour le module complémentaire OSM AKS, consultez la page [Azure Monitor pour OSM](https://aka.ms/azmon/osmpreview).
