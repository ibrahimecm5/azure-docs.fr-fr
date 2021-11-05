---
title: Résoudre les problèmes du module complémentaire Open Service Mesh
description: Résolution des problèmes du module complémentaire Open Service Mesh
services: container-service
ms.topic: article
ms.date: 8/26/2021
ms.custom: mvc, devx-track-azurecli
ms.author: pgibson
ms.openlocfilehash: 27a553bee765dd1369490cd9f6825cc44c48c59a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131065662"
---
# <a name="open-service-mesh-osm-aks-add-on-troubleshooting-guides"></a>Guides de résolution des problèmes du module complémentaire Open Service Mesh (OSM) AKS

Lorsque vous déployez le module complémentaire OSM AKS, vous risquez de rencontrer des problèmes liés à la configuration de la maille du service. Le guide suivant vous aidera à résoudre les erreurs et les problèmes courants.

## <a name="verifying-and-troubleshooting-osm-components"></a>Vérification et résolution des problèmes des composants OSM

### <a name="check-osm-controller-deployment-pod-and-service"></a>Vérifier le déploiement, le pod et le service du contrôleur OSM

```azurecli-interactive
kubectl get deployment,pod,service -n kube-system --selector app=osm-controller
```

Un contrôleur OSM sain ressemble à ceci :

```Output
NAME                             READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/osm-controller   2/2     2            2           3m4s

NAME                                  READY   STATUS    RESTARTS   AGE
pod/osm-controller-65bd8c445c-zszp4   1/1     Running   0          2m
pod/osm-controller-65bd8c445c-xqhmk   1/1     Running   0          16s

NAME                     TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)                       AGE
service/osm-controller   ClusterIP   10.96.185.178   <none>        15128/TCP,9092/TCP,9091/TCP   3m4s
service/osm-validator    ClusterIP   10.96.11.78     <none>        9093/TCP                      3m4s
```

> [!NOTE]
> Pour les services osm-controller, CLUSTER-IP est différent. Les propriétés NAME et PORT(S) du service doivent être les mêmes que dans l’exemple ci-dessus.

### <a name="check-osm-injector-deployment-pod-and-service"></a>Vérifier le déploiement, le pod et le service de l’injecteur OSM

```azurecli-interactive
kubectl get deployment,pod,service -n kube-system --selector app=osm-injector
```

Un injecteur OSM sain ressemble à ceci :

```Output
NAME                           READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/osm-injector   2/2     2            2           4m37s

NAME                                READY   STATUS    RESTARTS   AGE
pod/osm-injector-5c49bd8d7c-b6cx6   1/1     Running   0          4m21s
pod/osm-injector-5c49bd8d7c-dx587   1/1     Running   0          4m37s

NAME                   TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)    AGE
service/osm-injector   ClusterIP   10.96.236.108   <none>        9090/TCP   4m37s
```

### <a name="check-osm-bootstrap-deployment-pod-and-service"></a>Vérifier le déploiement, le pod et le service du démarrage OSM

```azurecli-interactive
kubectl get deployment,pod,service -n kube-system --selector app=osm-bootstrap
```

Un démarrage OSM sain ressemble à ceci :

```Output
NAME                            READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/osm-bootstrap   1/1     1            1           5m25s

NAME                                 READY   STATUS    RESTARTS   AGE
pod/osm-bootstrap-594ffc6cb7-jc7bs   1/1     Running   0          5m25s

NAME                    TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)             AGE
service/osm-bootstrap   ClusterIP   10.96.250.208   <none>        9443/TCP,9095/TCP   5m25s
```

### <a name="check-validating-and-mutating-webhooks"></a>Vérifier les webhooks Validating et Mutating

```azurecli-interactive
kubectl get ValidatingWebhookConfiguration --selector app=osm-controller
```

Un webhook Validating OSM sain ressemble à ceci :

```Output
NAME              WEBHOOKS   AGE
aks-osm-validator-mesh-osm   1      81m
```

```azurecli-interactive
kubectl get MutatingWebhookConfiguration --selector app=osm-injector
```

Un webhook Mutating OSM sain ressemble à ceci :

```Output
NAME              WEBHOOKS   AGE
aks-osm-webhook-osm   1      102m
```

### <a name="check-for-the-service-and-the-ca-bundle-of-the-validating-webhook"></a>Rechercher le service et le pack de l’autorité de certification du webhook Validating

```azurecli-interactive
kubectl get ValidatingWebhookConfiguration aks-osm-webhook-osm -o json | jq '.webhooks[0].clientConfig.service'
```

Un webhook Validating bien configuré doit ressembler à ceci :

```json
{
  "name": "osm-config-validator",
  "namespace": "kube-system",
  "path": "/validate-webhook",
  "port": 9093
}
```

### <a name="check-for-the-service-and-the-ca-bundle-of-the-mutating-webhook"></a>Rechercher le service et le pack de l’autorité de certification du webhook Mutating

```azurecli-interactive
kubectl get MutatingWebhookConfiguration aks-osm-webhook-osm -o json | jq '.webhooks[0].clientConfig.service'
```

Un webhook Mutating bien configuré doit ressembler à ceci :

```json
{
  "name": "osm-injector",
  "namespace": "kube-system",
  "path": "/mutate-pod-creation",
  "port": 9090
}
```

### <a name="check-the-osm-mesh-config-resource"></a>Vérifiez la `osm-mesh-config` ressource

Vérifiez son existence :

```azurecli-interactive
kubectl get meshconfig osm-mesh-config -n kube-system
```

Vérifier le contenu de la MeshConfig OSM

```azurecli-interactive
kubectl get meshconfig osm-mesh-config -n kube-system -o yaml
```

```
apiVersion: config.openservicemesh.io/v1alpha1
kind: MeshConfig
metadata:
  creationTimestamp: "0000-00-00A00:00:00A"
  generation: 1
  name: osm-mesh-config
  namespace: kube-system
  resourceVersion: "2494"
  uid: 6c4d67f3-c241-4aeb-bf4f-b029b08faa31
spec:
  certificate:
    serviceCertValidityDuration: 24h
  featureFlags:
    enableEgressPolicy: true
    enableMulticlusterMode: false
    enableWASMStats: true
  observability:
    enableDebugServer: true
    osmLogLevel: info
    tracing:
      address: jaeger.kube-system.svc.cluster.local
      enable: false
      endpoint: /api/v2/spans
      port: 9411
  sidecar:
    configResyncInterval: 0s
    enablePrivilegedInitContainer: false
    envoyImage: mcr.microsoft.com/oss/envoyproxy/envoy:v1.18.3
    initContainerImage: mcr.microsoft.com/oss/openservicemesh/init:v0.9.1
    logLevel: error
    maxDataPlaneConnections: 0
    resources: {}
  traffic:
    enableEgress: true
    enablePermissiveTrafficPolicyMode: true
    inboundExternalAuthorization:
      enable: false
      failureModeAllow: false
      statPrefix: inboundExtAuthz
      timeout: 1s
    useHTTPSIngress: false
```

`osm-mesh-config` Valeurs de ressources :

| Clé | Type | Valeur par défaut | Exemples de commandes patch Kubectl |
|-----|------|---------------|--------------------------------|
| spec.traffic.enableEgress | bool | `true` | `kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"traffic":{"enableEgress":true}}}'  --type=merge` |
| spec.traffic.enablePermissiveTrafficPolicyMode | bool | `true` | `kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"traffic":{"enablePermissiveTrafficPolicyMode":true}}}'  --type=merge` |
| spec.traffic.useHTTPSIngress | bool | `false` | `kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"traffic":{"useHTTPSIngress":true}}}'  --type=merge` |
| spec.traffic.outboundPortExclusionList | tableau | `[]` | `kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"traffic":{"outboundPortExclusionList":[6379,8080]}}}'  --type=merge` |
| spec.traffic.outboundIPRangeExclusionList | tableau | `[]` | `kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"traffic":{"outboundIPRangeExclusionList":["10.0.0.0/32","1.1.1.1/24"]}}}'  --type=merge` |
| spec.traffic.inboundPortExclusionList | tableau | `[]` | `kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"traffic":{"inboundPortExclusionList":[6379,8080]}}}'  --type=merge` |
| spec.certificate.serviceCertValidityDuration | string | `"24h"` | `kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"certificate":{"serviceCertValidityDuration":"24h"}}}'  --type=merge` |
| spec.observability.enableDebugServer | bool | `true` | `kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"observability":{"enableDebugServer":true}}}'  --type=merge` |
| spec.observability.tracing.enable | bool | `false` | `kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"observability":{"tracing":{"enable":true}}}}'  --type=merge` |
| spec.observability.tracing.enable | string | `"jaeger.kube-system.svc.cluster.local"`| `kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"observability":{"tracing":{"address": "jaeger.kube-system.svc.cluster.local"}}}}'  --type=merge` |
| spec.observability.tracing.endpoint | string | `"/api/v2/spans"` | `kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"observability":{"tracing":{"endpoint":"/api/v2/spans"}}}}'  --type=merge' --type=merge` |
| spec.observability.tracing.port | int | `9411`| `kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"observability":{"tracing":{"port":9411}}}}'  --type=merge` |
| spec.observability.tracing.osmLogLevel | string | `"info"`| `kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"observability":{"tracing":{"osmLogLevel": "info"}}}}'  --type=merge` |
| spec.sidecar.enablePrivilegedInitContainer | bool | `false` | `kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"sidecar":{"enablePrivilegedInitContainer":true}}}'  --type=merge` |
| spec.sidecar.logLevel | string | `"error"` | `kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"sidecar":{"logLevel":"error"}}}'  --type=merge` |
| spec.sidecar.maxDataPlaneConnections | int | `0` | `kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"sidecar":{"maxDataPlaneConnections":"error"}}}'  --type=merge` |
| spec.sidecar.envoyImage | string | `"mcr.microsoft.com/oss/envoyproxy/envoy:v1.19.1"` | `kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"sidecar":{"envoyImage":"mcr.microsoft.com/oss/envoyproxy/envoy:v1.19.1"}}}'  --type=merge` |
| spec.sidecar.initContainerImage | string | `"mcr.microsoft.com/oss/openservicemesh/init:v0.11.1"` | `kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"sidecar":{"initContainerImage":"mcr.microsoft.com/oss/openservicemesh/init:v0.11.1"}}}' --type=merge` |
| spec.sidecar.configResyncInterval | string | `"0s"` | `kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"sidecar":{"configResyncInterval":"30s"}}}'  --type=merge` |
| spec.featureFlags.enableWASMStats | bool | `"true"` | `kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"featureFlags":{"enableWASMStats":"true"}}}'  --type=merge` |
| spec.featureFlags.enableEgressPolicy | bool | `"true"` | `kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"featureFlags":{"enableEgressPolicy":"true"}}}'  --type=merge` |
| spec.featureFlags.enableMulticlusterMode | bool | `"false"` | `kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"featureFlags":{"enableMulticlusterMode":"false"}}}'  --type=merge` |
| spec.featureFlags.enableSnapshotCacheMode | bool | `"false"` | `kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"featureFlags":{"enableSnapshotCacheMode":"false"}}}'  --type=merge` |
| spec.featureFlags.enableAsyncProxyServiceMapping | bool | `"false"` | `kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"featureFlags":{"enableAsyncProxyServiceMapping":"false"}}}'  --type=merge` |
| spec.featureFlags.enableIngressBackendPolicy | bool | `"true"` | `kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"featureFlags":{"enableIngressBackendPolicy":"true"}}}'  --type=merge` |
| spec.featureFlags.enableEnvoyActiveHealthChecks | bool | `"false"` | `kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"featureFlags":{"enableEnvoyActiveHealthChecks":"false"}}}'  --type=merge` |


### <a name="check-namespaces"></a>Vérifier les espaces de noms

> [!NOTE]
> L’espace de noms kube-system ne fait jamais partie d’un maillage de services et n’est jamais étiqueté ou annoté avec les clés/valeurs ci-dessous.

Nous utilisons la commande `osm namespace add` pour joindre des espaces de noms à un maillage de services donné.
Lorsqu’un espace de noms k8s fait partie du maillage (ou pour qu’il fasse partie du maillage), les conditions suivantes doivent être remplies :

Affichez les annotations à l’aide de la commande suivante :

```azurecli-interactive
kubectl get namespace bookbuyer -o json | jq '.metadata.annotations'
```

L’annotation suivante doit être présente :

```Output
{
  "openservicemesh.io/sidecar-injection": "enabled"
}
```

Affichez les étiquettes à l’aide de la commande suivante :

```azurecli-interactive
kubectl get namespace bookbuyer -o json | jq '.metadata.labels'
```

L’étiquette suivante doit être présente :

```Output
{
  "openservicemesh.io/monitored-by": "osm"
}
```

Si un espace de noms n’est pas annoté avec `"openservicemesh.io/sidecar-injection": "enabled"` ou n’a pas l’étiquette `"openservicemesh.io/monitored-by": "osm"`, l’injecteur OSM n’ajoutera pas les side-cars Envoy.

> [!NOTE]
> Une fois que `osm namespace add` est appelé, seuls les **nouveaux** pods sont injectés avec un side-car Envoy. Les pods existants doivent être redémarrés à l’aide de la commande `kubectl rollout restart deployment ...`.

### <a name="verify-osm-crds"></a>Vérifiez les CRD OSM :

Vérifiez si le cluster possède les CRD requis :

```azurecli-interactive
kubectl get crds
```

Les éléments suivants doivent être installés sur le cluster :

- egresses.policy.openservicemesh.io
- httproutegroups.specs.smi-spec.io 
- ingressbackends.policy.openservicemesh.io
- meshconfigs.config.openservicemesh.io
- multiclusterservices.config.openservicemesh.io
- tcproutes.specs.smi-spec.io
- trafficsplits.split.smi-spec.io
- traffictargets.access.smi-spec.io

Récupérez les versions des CRD SMI installés avec cette commande :

```azurecli-interactive
osm mesh list
```

Sortie attendue :

```
MESH NAME   MESH NAMESPACE   VERSION   ADDED NAMESPACES
osm         kube-system      v0.11.1

MESH NAME   MESH NAMESPACE   SMI SUPPORTED
osm         kube-system      HTTPRouteGroup:v1alpha4,TCPRoute:v1alpha4,TrafficSplit:v1alpha2,TrafficTarget:v1alpha3

To list the OSM controller pods for a mesh, please run the following command passing in the mesh's namespace
        kubectl get pods -n <osm-mesh-namespace> -l app=osm-controller
```

Le contrôleur OSM v0.11.1 nécessite les versions suivantes :

- traffictargets.access.smi-spec.io : [v1alpha3](https://github.com/servicemeshinterface/smi-spec/blob/v0.6.0/apis/traffic-access/v1alpha3/traffic-access.md)
- httproutegroups.specs.smi-spec.io : [v1alpha4](https://github.com/servicemeshinterface/smi-spec/blob/v0.6.0/apis/traffic-specs/v1alpha4/traffic-specs.md#httproutegroup)
- tcproutes.specs.smi-spec.io : [v1alpha4](https://github.com/servicemeshinterface/smi-spec/blob/v0.6.0/apis/traffic-specs/v1alpha4/traffic-specs.md#tcproute)
- udproutes.specs.smi-spec.io : Non pris en charge
- trafficsplits.split.smi-spec.io : [v1alpha2](https://github.com/servicemeshinterface/smi-spec/blob/v0.6.0/apis/traffic-split/v1alpha2/traffic-split.md)
- \*.metrics.smi-spec.io : [v1alpha1](https://github.com/servicemeshinterface/smi-spec/blob/v0.6.0/apis/traffic-metrics/v1alpha1/traffic-metrics.md)


### <a name="certificate-management"></a>Gestion des certificats

Vous trouverez des informations sur la façon dont OSM émet et gère les certificats pour les proxies Envoy fonctionnant sur des pods d’application sur le [site de documentation d’OpenServiceMesh](https://docs.openservicemesh.io/docs/guides/certificates/).

### <a name="upgrading-envoy"></a>Mise à niveau d’Envoy

Lorsqu’un nouveau pod est créé dans un espace de noms surveillé par le module complémentaire, OSM injecte un [proxy side-car Envoy](https://docs.openservicemesh.io/docs/guides/app_onboarding/sidecar_injection/) dans ce pod. Pour plus d’informations sur la mise à jour de la version d’Envoy, consultez le [guide de mise à niveau](https://release-v0-11.docs.openservicemesh.io/docs/getting_started/upgrade/#envoy) sur le site de documentation d’OpenServiceMesh.
