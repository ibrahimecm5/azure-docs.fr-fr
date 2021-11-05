---
title: Observabilité OSM OSS
description: Comment configurer l’observabilité open source pour Open Service Mesh
services: container-service
ms.topic: article
ms.date: 8/26/2021
ms.custom: mvc, devx-track-azurecli
ms.author: pgibson
ms.openlocfilehash: 5b8e056cd360a66c42324292d7e40e8fb25ce668
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131066878"
---
# <a name="manually-deploy-prometheus-grafana-and-jaeger-to-view-open-service-mesh-osm-metrics-for-observability"></a>Déployer manuellement Prometheus, Grafana et Jaeger afin d’afficher les métriques d’Open Service Mesh (OSM) pour l’observabilité

> [!WARNING]
> L’installation de Prometheus, Grafana et Jaeger est fournie à titre d’aide générale pour montrer la façon dont ces outils peuvent être utilisés pour visualiser les données des métriques d’OSM. L’aide à l’installation ne doit pas être utilisée pour une configuration de production. Reportez-vous à la documentation de chaque outil pour savoir comment paramétrer au mieux leur installation selon vos besoins. Le plus remarquable est l’absence de stockage persistant, ce qui signifie que toutes les données sont perdues une fois que des pods Prometheus, Grafana ou Jaeger sont terminés.

Open Service Mesh (OSM) génère des métriques détaillées relatives à l’ensemble du trafic au sein du maillage. Ces métriques donnent un aperçu du comportement des applications dans le maillage, ce qui aide les utilisateurs à dépanner, à gérer et à analyser leurs applications.

À l’heure actuelle, OSM collecte les métriques directement à partir des proxys side-car (Envoy). OSM fournit de nombreuses métriques du trafic entrant et sortant pour tous les services du maillage. Grâce à ces métriques, l’utilisateur peut obtenir des informations sur le volume global du trafic, les erreurs dans le trafic et le temps de réponse aux demandes.

OSM utilise Prometheus pour recueillir et stocker des métriques et des statistiques cohérentes sur le trafic pour toutes les applications exécutées dans le maillage. Prometheus est une boîte à outils de surveillance et d’alerte open source qui est couramment utilisée, notamment sur les environnements Kubernetes et de maillage de services.

Chaque application faisant partie du maillage s’exécute dans un pod contenant un side-car Envoy qui expose les métriques (métriques du proxy) au format Prometheus. En outre, chaque pod faisant partie du maillage possède des annotations Prometheus, ce qui permet au serveur Prometheus de scrapper l’application de manière dynamique. Ce mécanisme active automatiquement le scraping des métriques chaque fois qu’un nouvel espace de noms/pod/service est ajouté au maillage.

Les métriques d’OSM peuvent être visualisées à l’aide de Grafana, un logiciel de visualisation et d’analyse open source. Ce logiciel vous permet d’interroger, de visualiser, d’alerter et d’explorer vos métriques.

Ce didacticiel présente les procédures suivantes :

> [!div class="checklist"]
>
> - Créer et déployer une instance Prometheus
> - Mettre à jour la `Configmap` Prometheus
> - Créer et déployer une instance Grafana
> - Configurer Grafana avec la source de données Prometheus
> - Activer les métriques Prometheus pour un espace de noms d’utilisateur
> - Importer le tableau de bord OSM pour Grafana
> - Créer et déployer une instance Jaeger
> - Configurer le traçage de Jaeger pour OSM

## <a name="before-you-begin"></a>Avant de commencer

Les ressources suivantes doivent être installées :

- Azure CLI, version 2.20.0 ou ultérieure
- OSM version 0.11.1 ou ultérieure
- Processeur JSON « jq » version 1.6+

## <a name="deploy-and-configure-a-prometheus-instance-for-osm"></a>Déployer et configurer une instance Prometheus pour OSM

Nous allons utiliser Helm pour déployer l’instance Prometheus. Exécutez les commandes suivantes pour installer Prometheus via Helm :

```azurecli-interactive
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
helm install stable prometheus-community/prometheus
```

Si l’installation s’est déroulée avec succès, vous devriez voir un résultat similaire à celui ci-dessous. Notez le port du serveur Prometheus et le nom DNS du cluster. Ces informations seront utilisées ultérieurement pour configurer Prometheus comme source de données pour Grafana.

```Output
NAME: stable
LAST DEPLOYED: Fri Mar 26 13:34:51 2021
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
The Prometheus server can be accessed via port 80 on the following DNS name from within your cluster:
stable-prometheus-server.default.svc.cluster.local


Get the Prometheus server URL by running these commands in the same shell:
  export POD_NAME=$(kubectl get pods --namespace default -l "app=prometheus,component=server" -o jsonpath="{.items[0].metadata.name}")
  kubectl --namespace default port-forward $POD_NAME 9090


The Prometheus alertmanager can be accessed via port 80 on the following DNS name from within your cluster:
stable-prometheus-alertmanager.default.svc.cluster.local


Get the Alertmanager URL by running these commands in the same shell:
  export POD_NAME=$(kubectl get pods --namespace default -l "app=prometheus,component=alertmanager" -o jsonpath="{.items[0].metadata.name}")
  kubectl --namespace default port-forward $POD_NAME 9093
#################################################################################
######   WARNING: Pod Security Policy has been moved to a global property.  #####
######            use .Values.podSecurityPolicy.enabled with pod-based      #####
######            annotations                                               #####
######            (e.g. .Values.nodeExporter.podSecurityPolicy.annotations) #####
#################################################################################


The Prometheus PushGateway can be accessed via port 9091 on the following DNS name from within your cluster:
stable-prometheus-pushgateway.default.svc.cluster.local


Get the PushGateway URL by running these commands in the same shell:
  export POD_NAME=$(kubectl get pods --namespace default -l "app=prometheus,component=pushgateway" -o jsonpath="{.items[0].metadata.name}")
  kubectl --namespace default port-forward $POD_NAME 9091

For more information on running Prometheus, visit:
https://prometheus.io/
```

### <a name="update-the-prometheus-configmap"></a>Mettre à jour le Configmap Prometheus

Par défaut, Prometheus est défini pour scraper les composants OSM. L’installation par défaut de Prometheus contiendra deux `configmaps` Kubernetes. Vous pouvez afficher la liste des `configmaps` de Prometheus à l’aide de la commande suivante.

```azurecli-interactive
kubectl get configmap | grep prometheus
```

```Output
stable-prometheus-alertmanager   1      4h34m
stable-prometheus-server         5      4h34m
```

Nous devrons remplacer la configuration prometheus.yml située dans le `configmap` **stable-prometheus-server** par la configuration OSM suivante. Il existe plusieurs techniques de modification de fichiers pour accomplir cette tâche. Une méthode simple et sûre consiste à exporter le `configmap`, à en créer une copie pour la sauvegarde, puis à le modifier à l’aide d’un éditeur tel que Visual Studio Code.

> [!NOTE]
> Si Visual Studio Code n’est pas installé, vous pouvez le télécharger [ici](https://code.visualstudio.com/Download) et l’installer.

Exportons d’abord le configmap **stable-prometheus-server** et faisons-en une copie pour la sauvegarde.

```azurecli-interactive
kubectl get configmap stable-prometheus-server -o yaml > cm-stable-prometheus-server.yml
cp cm-stable-prometheus-server.yml cm-stable-prometheus-server.yml.copy
```

Ensuite, ouvrons le fichier à l’aide de Visual Studio Code pour le modifier.

```azurecli-interactive
code cm-stable-prometheus-server.yml
```

Une fois que vous avez ouvert le `configmap` dans l’éditeur Visual Studio Code, remplacez le fichier prometheus.yml par la configuration OSM ci-dessous, puis enregistrez le fichier.

> [!WARNING]
> Il est extrêmement important de veiller à conserver la structure de mise en retrait du fichier YAML. Toute modification apportée à la structure du fichier YAML peut rendre impossible d’appliquer à nouveau le configmap.

```OSM Prometheus Configmap Configuration
prometheus.yml: |
    global:
      scrape_interval: 10s
      scrape_timeout: 10s
      evaluation_interval: 1m

    scrape_configs:
      - job_name: 'kubernetes-apiservers'
        kubernetes_sd_configs:
        - role: endpoints
        scheme: https
        tls_config:
          ca_file: /var/run/secrets/kubernetes.io/serviceaccount/ca.crt
          # TODO need to remove this when the CA and SAN match
          insecure_skip_verify: true
        bearer_token_file: /var/run/secrets/kubernetes.io/serviceaccount/token
        metric_relabel_configs:
        - source_labels: [__name__]
          regex: '(apiserver_watch_events_total|apiserver_admission_webhook_rejection_count)'
          action: keep
        relabel_configs:
        - source_labels: [__meta_kubernetes_namespace, __meta_kubernetes_service_name, __meta_kubernetes_endpoint_port_name]
          action: keep
          regex: default;kubernetes;https

      - job_name: 'kubernetes-nodes'
        scheme: https
        tls_config:
          ca_file: /var/run/secrets/kubernetes.io/serviceaccount/ca.crt
        bearer_token_file: /var/run/secrets/kubernetes.io/serviceaccount/token
        kubernetes_sd_configs:
        - role: node
        relabel_configs:
        - action: labelmap
          regex: __meta_kubernetes_node_label_(.+)
        - target_label: __address__
          replacement: kubernetes.default.svc:443
        - source_labels: [__meta_kubernetes_node_name]
          regex: (.+)
          target_label: __metrics_path__
          replacement: /api/v1/nodes/${1}/proxy/metrics

      - job_name: 'kubernetes-pods'
        kubernetes_sd_configs:
        - role: pod
        metric_relabel_configs:
        - source_labels: [__name__]
          regex: '(envoy_server_live|envoy_cluster_upstream_rq_xx|envoy_cluster_upstream_cx_active|envoy_cluster_upstream_cx_tx_bytes_total|envoy_cluster_upstream_cx_rx_bytes_total|envoy_cluster_upstream_cx_destroy_remote_with_active_rq|envoy_cluster_upstream_cx_connect_timeout|envoy_cluster_upstream_cx_destroy_local_with_active_rq|envoy_cluster_upstream_rq_pending_failure_eject|envoy_cluster_upstream_rq_pending_overflow|envoy_cluster_upstream_rq_timeout|envoy_cluster_upstream_rq_rx_reset|^osm.*)'
          action: keep
        relabel_configs:
        - source_labels: [__meta_kubernetes_pod_annotation_prometheus_io_scrape]
          action: keep
          regex: true
        - source_labels: [__meta_kubernetes_pod_annotation_prometheus_io_path]
          action: replace
          target_label: __metrics_path__
          regex: (.+)
        - source_labels: [__address__, __meta_kubernetes_pod_annotation_prometheus_io_port]
          action: replace
          regex: ([^:]+)(?::\d+)?;(\d+)
          replacement: $1:$2
          target_label: __address__
        - source_labels: [__meta_kubernetes_namespace]
          action: replace
          target_label: source_namespace
        - source_labels: [__meta_kubernetes_pod_name]
          action: replace
          target_label: source_pod_name
        - regex: '(__meta_kubernetes_pod_label_app)'
          action: labelmap
          replacement: source_service
        - regex: '(__meta_kubernetes_pod_label_osm_envoy_uid|__meta_kubernetes_pod_label_pod_template_hash|__meta_kubernetes_pod_label_version)'
          action: drop
        # for non-ReplicaSets (DaemonSet, StatefulSet)
        # __meta_kubernetes_pod_controller_kind=DaemonSet
        # __meta_kubernetes_pod_controller_name=foo
        # =>
        # workload_kind=DaemonSet
        # workload_name=foo
        - source_labels: [__meta_kubernetes_pod_controller_kind]
          action: replace
          target_label: source_workload_kind
        - source_labels: [__meta_kubernetes_pod_controller_name]
          action: replace
          target_label: source_workload_name
        # for ReplicaSets
        # __meta_kubernetes_pod_controller_kind=ReplicaSet
        # __meta_kubernetes_pod_controller_name=foo-bar-123
        # =>
        # workload_kind=Deployment
        # workload_name=foo-bar
        # deplyment=foo
        - source_labels: [__meta_kubernetes_pod_controller_kind]
          action: replace
          regex: ^ReplicaSet$
          target_label: source_workload_kind
          replacement: Deployment
        - source_labels:
          - __meta_kubernetes_pod_controller_kind
          - __meta_kubernetes_pod_controller_name
          action: replace
          regex: ^ReplicaSet;(.*)-[^-]+$
          target_label: source_workload_name

      - job_name: 'smi-metrics'
        kubernetes_sd_configs:
        - role: pod
        relabel_configs:
        - source_labels: [__meta_kubernetes_pod_annotation_prometheus_io_scrape]
          action: keep
          regex: true
        - source_labels: [__meta_kubernetes_pod_annotation_prometheus_io_path]
          action: replace
          target_label: __metrics_path__
          regex: (.+)
        - source_labels: [__address__, __meta_kubernetes_pod_annotation_prometheus_io_port]
          action: replace
          regex: ([^:]+)(?::\d+)?;(\d+)
          replacement: $1:$2
          target_label: __address__
        metric_relabel_configs:
        - source_labels: [__name__]
          regex: 'envoy_.*osm_request_(total|duration_ms_(bucket|count|sum))'
          action: keep
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_(\d{3})_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_total
          target_label: response_code
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_(.*)_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_total
          target_label: source_namespace
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_.*_source_kind_(.*)_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_total
          target_label: source_kind
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_.*_source_kind_.*_source_name_(.*)_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_total
          target_label: source_name
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_(.*)_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_total
          target_label: source_pod
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_(.*)_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_total
          target_label: destination_namespace
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_(.*)_destination_name_.*_destination_pod_.*_osm_request_total
          target_label: destination_kind
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_(.*)_destination_pod_.*_osm_request_total
          target_label: destination_name
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_(.*)_osm_request_total
          target_label: destination_pod
        - source_labels: [__name__]
          action: replace
          regex: .*(osm_request_total)
          target_label: __name__

        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_(.*)_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_duration_ms_(bucket|sum|count)
          target_label: source_namespace
        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_.*_source_kind_(.*)_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_duration_ms_(bucket|sum|count)
          target_label: source_kind
        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_.*_source_kind_.*_source_name_(.*)_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_duration_ms_(bucket|sum|count)
          target_label: source_name
        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_(.*)_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_duration_ms_(bucket|sum|count)
          target_label: source_pod
        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_(.*)_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_duration_ms_(bucket|sum|count)
          target_label: destination_namespace
        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_(.*)_destination_name_.*_destination_pod_.*_osm_request_duration_ms_(bucket|sum|count)
          target_label: destination_kind
        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_(.*)_destination_pod_.*_osm_request_duration_ms_(bucket|sum|count)
          target_label: destination_name
        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_(.*)_osm_request_duration_ms_(bucket|sum|count)
          target_label: destination_pod
        - source_labels: [__name__]
          action: replace
          regex: .*(osm_request_duration_ms_(bucket|sum|count))
          target_label: __name__

      - job_name: 'kubernetes-cadvisor'
        scheme: https
        tls_config:
          ca_file: /var/run/secrets/kubernetes.io/serviceaccount/ca.crt
        bearer_token_file: /var/run/secrets/kubernetes.io/serviceaccount/token
        kubernetes_sd_configs:
        - role: node
        metric_relabel_configs:
        - source_labels: [__name__]
          regex: '(container_cpu_usage_seconds_total|container_memory_rss)'
          action: keep
        relabel_configs:
        - action: labelmap
          regex: __meta_kubernetes_node_label_(.+)
        - target_label: __address__
          replacement: kubernetes.default.svc:443
        - source_labels: [__meta_kubernetes_node_name]
          regex: (.+)
          target_label: __metrics_path__
          replacement: /api/v1/nodes/${1}/proxy/metrics/cadvisor
```

Appliquez le fichier YAML mis à jour du `configmap` à l’aide de la commande suivante.

```azurecli-interactive
kubectl apply -f cm-stable-prometheus-server.yml
```

```Output
configmap/stable-prometheus-server configured
```

> [!NOTE]
> Vous pouvez recevoir un message concernant l’absence d’une annotation Kubernetes nécessaire. Ce message peut être ignoré pour le moment.

### <a name="verify-prometheus-is-configured-to-scrape-the-osm-mesh-and-api-endpoints"></a>Vérifier que Prometheus est configuré pour scraper le maillage OSM et les points de terminaison d’API

Pour vérifier que Prometheus est correctement configuré pour scraper le maillage OSM et les points de terminaison d’API, nous allons rediriger le port vers le pod Prometheus et afficher la configuration cible. Exécutez les commandes suivantes :

```azurecli-interactive
PROM_POD_NAME=$(kubectl get pods -l "app=prometheus,component=server" -o jsonpath="{.items[0].metadata.name}")
kubectl --namespace <promNamespace> port-forward $PROM_POD_NAME 9090
```

Ouvrez un navigateur et accédez à `http://localhost:9090/targets`.

Si vous faites défiler la page vers le bas, vous devriez voir tous les points de terminaison des métriques SMI à l’état **UP**, ainsi que d’autres métriques OSM définies comme illustré ci-dessous.

![Image de l’interface utilisateur, métriques cibles de Prometheus OSM](./media/aks-osm-addon/osm-prometheus-smi-metrics-target-scrape.png)

## <a name="deploy-and-configure-a-grafana-instance-for-osm"></a>Déployer et configurer une instance Grafana pour OSM

Nous allons utiliser Helm pour déployer l’instance Grafana. Exécutez les commandes suivantes pour installer Grafana via Helm :

```
helm repo add grafana https://grafana.github.io/helm-charts
helm repo update
helm install osm-grafana grafana/grafana
```

Nous allons ensuite récupérer le mot de passe Grafana par défaut pour nous connecter au site de Grafana.

```azurecli-interactive
kubectl get secret --namespace default osm-grafana -o jsonpath="{.data.admin-password}" | base64 --decode ; echo
```

Notez le mot de passe Grafana.

Nous allons ensuite récupérer le pod Grafana afin de rediriger le port vers le tableau de bord Grafana pour la connexion.

```azurecli-interactive
GRAF_POD_NAME=$(kubectl get pods -l "app.kubernetes.io/name=grafana" -o jsonpath="{.items[0].metadata.name}")
kubectl port-forward $GRAF_POD_NAME 3000
```

Ouvrez un navigateur et accédez à `http://localhost:3000`.

Sur l’écran de connexion illustré ci-dessous, entrez **admin** comme nom d’utilisateur et utilisez le mot de passe Grafana capturé précédemment.

![Image de l’interface utilisateur, page de connexion à Grafana OSM](./media/aks-osm-addon/osm-grafana-ui-login.png)

### <a name="configure-the-grafana-prometheus-data-source"></a>Configurer la source de données Prometheus pour Grafana

Une fois que vous êtes correctement connecté à Grafana, l’étape suivante consiste à ajouter Prometheus comme sources de données pour Grafana. Pour ce faire, accédez à l’icône de configuration dans le menu de gauche et sélectionnez Data Sources (Sources de données) comme indiqué ci-dessous.

![Image de l’interface utilisateur, page Sources de données de Grafana OSM](./media/aks-osm-addon/osm-grafana-ui-datasources.png)

Cliquez sur le bouton **Add data source** (Ajouter une source de données) et sélectionnez Prometheus sous « Time series databases » (Bases de données de série chronologique).

![Image de l’interface utilisateur, page Sélection des sources de données de Grafana OSM](./media/aks-osm-addon/osm-grafana-ui-datasources-select-prometheus.png)

Dans la page **Configure your Prometheus data source below** (Configurez votre source de données Prometheus ci-dessous), entrez le nom de domaine complet du cluster Kubernetes pour le service Prometheus pour le paramètre URL HTTP. Le nom de domaine complet par défaut doit être `stable-prometheus-server.default.svc.cluster.local`. Une fois que vous avez entré ce point de terminaison de service Prometheus, faites défiler la page vers le bas et sélectionnez **Save & Test** (Enregistrer et tester). Vous devez recevoir une coche verte indiquant que la source de données fonctionne.

### <a name="enable-prometheus-metrics-for-a-user-namespace"></a>Activer les métriques Prometheus pour un espace de noms d’utilisateur
Pour configurer Prometheus afin de scraper les métriques d’un espace de noms d’application, exécutez la commande suivante.
```azurecli-interactive
osm metrics enable --namespace <app-namespace>
```

### <a name="importing-osm-dashboards"></a>Importer des tableaux de bord OSM

Les tableaux de bord OSM sont disponibles à la fois via :

- [notre référentiel](https://github.com/openservicemesh/osm/tree/release-v0.11/charts/osm/grafana/dashboards), et peuvent être importés sous forme de blobs json via le portail d’administration web ; ou
- [en ligne à l’adresse grafana.com](https://grafana.com/grafana/dashboards/14145).

Pour importer un tableau de bord, recherchez le signe `+` dans le menu de gauche et sélectionnez `import`.
Vous pouvez importer directement les tableaux de bord par leur ID sur `Grafana.com`. Par exemple, notre tableau de bord `OSM Mesh Details` utilise l’ID `14145`. Vous pouvez utiliser l’ID directement sur le formulaire et sélectionner `import` :

![Image de l’interface utilisateur, page Importer un tableau de bord Grafana OSM](./media/aks-osm-addon/osm-grafana-dashboard-import.png)

Dès que vous sélectionnez l’option d’importation, le tableau de bord importé s’affiche automatiquement.

![Image de l’interface utilisateur, page Détails du maillage du tableau de bord Grafana OSM](./media/aks-osm-addon/osm-grafana-mesh-dashboard-details.png)

## <a name="deploy-and-configure-a-jaeger-operator-on-kubernetes-for-osm"></a>Déployer et configurer un opérateur Jaeger sur Kubernetes pour OSM

[Jaeger](https://www.jaegertracing.io/) est un système de traçage open source utilisé pour la surveillance et la résolution des problèmes des systèmes distribués. Il peut être déployé avec OSM comme nouvelle instance, ou vous pouvez apporter votre propre instance. Les instructions suivantes déploient une nouvelle instance de Jaeger dans l’espace de noms `jaeger` sur le cluster AKS.

### <a name="deploy-jaeger-to-the-aks-cluster"></a>Déployer Jaeger sur le cluster AKS

Tout d’abord, créez un espace de noms Jaeger :

```azurecli-interactive
kubectl create namespace jaeger
```

Appliquez le manifeste suivant pour installer Jaeger :

```azurecli-interactive
kubectl apply -f - <<EOF
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: jaeger
  namespace: jaeger
  labels:
    app: jaeger
spec:
  replicas: 1
  selector:
    matchLabels:
      app: jaeger
  template:
    metadata:
      labels:
        app: jaeger
    spec:
      containers:
        - name: jaeger
          image: jaegertracing/all-in-one
          args:
          - --collector.zipkin.host-port=9411
          imagePullPolicy: IfNotPresent
          ports:
          - containerPort: 9411
          resources:
            limits:
              cpu: 500m
              memory: 512M
            requests:
              cpu: 100m
              memory: 256M
---
kind: Service
apiVersion: v1
metadata:
  name: jaeger
  namespace: jaeger
  labels:
    app: jaeger
spec:
  selector:
    app: jaeger
  ports:
  - protocol: TCP
    # Service port and target port are the same
    port: 9411
  type: ClusterIP
EOF
```

```Output
deployment.apps/jaeger created
service/jaeger created
```

### <a name="add-rbac-for-jaeger-sa"></a>Ajouter RBAC pour Jaeger SA

Appliquez le RBAC suivant pour accorder au compte de service Jaeger le rôle de cluster spécifié :

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  labels:
    app: jaeger
  name: jaeger
  namespace: jaeger
---
kind: ClusterRole
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  labels:
    app: jaeger
  name: jaeger
---
kind: ClusterRoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: jaeger
  labels:
    app: jaeger
subjects:
  - kind: ServiceAccount
    name: jaeger
    namespace: jaeger
roleRef:
  kind: ClusterRole
  name: jaeger
  apiGroup: rbac.authorization.k8s.io
```

### <a name="enable-tracing-for-the-osm-add-on"></a>Activer le traçage pour le module complémentaire OSM

Ensuite, nous devons activer le traçage pour le module complémentaire OSM.

Exécutez la commande suivante pour activer le traçage pour le module complémentaire OSM :

```azurecli-interactive
kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"observability":{"tracing":{"enable":true, "address": "jaeger.jaeger.svc.cluster.local"}}}}' --type=merge
```

```Output
meshconfig.config.openservicemesh.io/osm-mesh-config patched
```

### <a name="view-the-jaeger-ui-with-port-forwarding"></a>Afficher l’interface utilisateur Jaeger avec la redirection de port

L’interface utilisateur de Jaeger s’exécute sur le port 16686. Pour afficher l’interface utilisateur web, vous pouvez utiliser « kubectl port-forward » :

```azurecli-interactive
JAEGER_POD=$(kubectl get pods -n jaeger --no-headers  --selector app=jaeger | awk 'NR==1{print $1}')
kubectl port-forward -n jaeger $JAEGER_POD  16686:16686
http://localhost:16686/
```

Dans le navigateur, vous devriez voir une liste déroulante Service, qui vous permet de choisir parmi les différentes applications déployées par la version de démonstration de bookstore. Sélectionnez un service pour afficher toutes les durées de celui-ci. Par exemple, si vous sélectionnez `bookbuyer` avec un Lookback (Recherche arrière) d’une heure, vous pouvez voir ses interactions avec bookstore-v1 et bookstore-v2 triées par heure.

![Image de l’interface utilisateur, page Traçage Jaeger OSM](./media/aks-osm-addon/osm-jaeger-trace-view-ui.png)

Sélectionnez un élément pour l’afficher plus en détail. Sélectionnez plusieurs éléments pour comparer les traces. Par exemple, vous pouvez comparer les interactions de `bookbuyer` avec bookstore et bookstore-v2 à un moment donné.

Vous pouvez également sélectionner l’onglet System Architecture (Architecture du système) pour afficher un graphique illustrant la manière dont les différentes applications interagissent/communiquent. Cela vous donne une idée de la façon dont le trafic circule entre les applications.

![Image de l’interface utilisateur, architecture du système Jaeger OSM](./media/aks-osm-addon/osm-jaeger-sys-arc-view-ui.png)
