---
title: Résoudre les problèmes courants liés à Kubernetes avec Azure Arc
services: azure-arc
ms.service: azure-arc
ms.date: 05/21/2021
ms.topic: article
description: Résolution des problèmes courants liés aux clusters Kubernetes avec Azure Arc.
keywords: Kubernetes, Arc, Azure, conteneurs
ms.openlocfilehash: acbc8293f5aed737dba6ed3992fc59cac0a4fd55
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132059238"
---
# <a name="azure-arc-enabled-kubernetes-troubleshooting"></a>Résolution des problèmes liés à Kubernetes avec Azure Arc

Ce document fournit des guides pour la résolution des problèmes liés à la connectivité, aux autorisations et aux agents.

## <a name="general-troubleshooting"></a>Résolution générale des problèmes

### <a name="azure-cli"></a>Azure CLI

Avant d’utiliser les commandes CLI `az connectedk8s` ou `az k8s-configuration`, vérifiez qu’Azure CLI est configuré pour fonctionner avec l’abonnement Azure approprié.

```azurecli
az account set --subscription 'subscriptionId'
az account show
```

### <a name="azure-arc-agents"></a>Agents Azure Arc

Tous les agents pour Kubernetes avec Azure Arc sont déployés en tant que pods dans l’espace de noms `azure-arc`. Tous les pods doivent être en cours d’exécution et réussir leurs contrôles d’intégrité.

Tout d’abord, vérifiez la version de Helm pour Azure Arc :

```console
$ helm --namespace default status azure-arc
NAME: azure-arc
LAST DEPLOYED: Fri Apr  3 11:13:10 2020
NAMESPACE: default
STATUS: deployed
REVISION: 5
TEST SUITE: None
```

Si la version de Helm est introuvable ou manquante, essayez de [reconnecter le cluster à Azure Arc](./quickstart-connect-cluster.md).

Si la version de Helm est présente avec `STATUS: deployed`, vérifiez l’état des agents à l’aide de `kubectl` :

```console
$ kubectl -n azure-arc get deployments,pods
NAME                                       READY  UP-TO-DATE  AVAILABLE  AGE
deployment.apps/clusteridentityoperator     1/1       1          1       16h
deployment.apps/config-agent                1/1       1          1       16h
deployment.apps/cluster-metadata-operator   1/1       1          1       16h
deployment.apps/controller-manager          1/1       1          1       16h
deployment.apps/flux-logs-agent             1/1       1          1       16h
deployment.apps/metrics-agent               1/1       1          1       16h
deployment.apps/resource-sync-agent         1/1       1          1       16h

NAME                                            READY   STATUS  RESTART  AGE
pod/cluster-metadata-operator-7fb54d9986-g785b  2/2     Running  0       16h
pod/clusteridentityoperator-6d6678ffd4-tx8hr    3/3     Running  0       16h
pod/config-agent-544c4669f9-4th92               3/3     Running  0       16h
pod/controller-manager-fddf5c766-ftd96          3/3     Running  0       16h
pod/flux-logs-agent-7c489f57f4-mwqqv            2/2     Running  0       16h
pod/metrics-agent-58b765c8db-n5l7k              2/2     Running  0       16h
pod/resource-sync-agent-5cf85976c7-522p5        3/3     Running  0       16h
```

Tous les pods doivent indiquer que le `STATUS` est `Running` avec `3/3` ou `2/2` sous la colonne `READY`. Récupérez les journaux et décrivez les pods qui retournent `Error` ou `CrashLoopBackOff`. Si des pods sont bloqués à l’état `Pending`, cela est peut-être dû à un manque de ressources sur les nœuds de cluster. [Effectuez un scale-up de votre cluster](https://kubernetes.io/docs/tasks/administer-cluster/) pour permettre à ces pods de passer à l’état `Running`.

## <a name="connecting-kubernetes-clusters-to-azure-arc"></a>Connexion de clusters Kubernetes à Azure Arc

La connexion de clusters à Azure nécessite à la fois l’accès à un abonnement Azure et l’accès `cluster-admin` à un cluster cible. Si vous ne pouvez pas joindre le cluster ou si vous disposez d’autorisations insuffisantes, la connexion du cluster à Azure Arc ne pourra pas s’effectuer.

### <a name="azure-cli-is-unable-to-download-helm-chart-for-azure-arc-agents"></a>Azure CLI ne peut pas télécharger le chart Helm pour les agents Azure Arc

Si vous utilisez Helm 3.7.0 ou une version ultérieure, vous rencontrerez l’erreur suivante lors de l’exécution de `az connectedk8s connect` pour connecter le cluster à Azure Arc :

```console
$ az connectedk8s connect -n AzureArcTest -g AzureArcTest

Unable to pull helm chart from the registry 'mcr.microsoft.com/azurearck8s/batch1/stable/azure-arc-k8sagents:1.4.0': Error: unknown command "chart" for "helm"
Run 'helm --help' for usage.
```

Dans ce cas, vous devez installer une version antérieure de [Helm 3](https://helm.sh/docs/intro/install/), où la version &lt; 3.7.0. Après cela, réexécutez la commande `az connectedk8s connect` pour connecter le cluster à Azure Arc.

### <a name="insufficient-cluster-permissions"></a>Autorisations du cluster insuffisantes

Si le fichier kubeconfig fourni ne dispose pas des autorisations suffisantes pour installer les agents Azure Arc, la commande Azure CLI retourne une erreur.

```azurecli
$ az connectedk8s connect --resource-group AzureArc --name AzureArcCluster
Ensure that you have the latest helm version installed before proceeding to avoid unexpected errors.
This operation might take a while...

Error: list: failed to list: secrets is forbidden: User "myuser" cannot list resource "secrets" in API group "" at the cluster scope
```

L’utilisateur qui connecte le cluster à Azure Arc doit se voir attribuer le rôle `cluster-admin` sur le cluster.


### <a name="unable-to-connect-openshift-cluster-to-azure-arc"></a>Impossible de connecter le cluster OpenShift à Azure Arc

Si `az connectedk8s connect` arrive à expiration et échoue lors de la connexion d’un cluster OpenShift à Azure Arc, vérifiez les éléments suivants :

1. Le cluster OpenShift doit respecter les conditions préalables de version : 4.5.41+, 4.6.35+ ou 4.7.18+.

1. Avant l’exécution de `az connectedk8s connnect`, la commande suivante doit être exécutée sur le cluster :

    ```console
    oc adm policy add-scc-to-user privileged system:serviceaccount:azure-arc:azure-arc-kube-aad-proxy-sa
    ```

### <a name="installation-timeouts"></a>Délai d’installation

La connexion d’un cluster Kubernetes à Kubernetes avec Azure Arc nécessite l’installation d’agents Azure Arc sur le cluster. Si le cluster s’exécute sur une connexion Internet lente, l’extraction de l’image conteneur pour les agents peut dépasser le délai d’expiration d’Azure CLI.

```azurecli
$ az connectedk8s connect --resource-group AzureArc --name AzureArcCluster
Ensure that you have the latest helm version installed before proceeding to avoid unexpected errors.
This operation might take a while...
```

### <a name="helm-issue"></a>Problème Helm

La version `v3.3.0-rc.1` de Helm présente un [problème](https://github.com/helm/helm/pull/8527) : l’installation/la mise à niveau de Helm (utilisée par l’extension CLI `connectedk8s`) entraîne l’exécution de tous les hooks, ce qui donne lieu à l’erreur suivante :

```console
$ az connectedk8s connect -n AzureArcTest -g AzureArcTest
Ensure that you have the latest helm version installed before proceeding.
This operation might take a while...

Please check if the azure-arc namespace was deployed and run 'kubectl get pods -n azure-arc' to check if all the pods are in running state. A possible cause for pods stuck in pending state could be insufficientresources on the kubernetes cluster to onboard to arc.
ValidationError: Unable to install helm release: Error: customresourcedefinitions.apiextensions.k8s.io "connectedclusters.arc.azure.com" not found
```

Pour résoudre ce problème, procédez comme suit :

1. Supprimez la ressource Kubernetes avec Azure Arc dans le portail Azure.
2. Exécutez les commandes suivantes sur votre ordinateur :
    
    ```console
    kubectl delete ns azure-arc
    kubectl delete clusterrolebinding azure-arc-operator
    kubectl delete secret sh.helm.release.v1.azure-arc.v1
    ```

3. [Installez une version stable](https://helm.sh/docs/intro/install/) de Helm 3 sur votre ordinateur à la place de la version Release Candidate.
4. Exécutez la commande `az connectedk8s connect` avec les valeurs appropriées pour connecter le cluster à Azure Arc.

## <a name="configuration-management"></a>Gestion des configurations

### <a name="general"></a>Général
Pour permettre la résolution des problèmes liés à la ressource de configuration, exécutez les commandes az avec le paramètre `--debug` spécifié.

```console
az provider show -n Microsoft.KubernetesConfiguration --debug
az k8s-configuration create <parameters> --debug
```

### <a name="create-configurations"></a>Créer des configurations

Les autorisations d’accès en écriture sur la ressource Kubernetes avec Azure Arc (`Microsoft.Kubernetes/connectedClusters/Write`) sont nécessaires et suffisantes pour créer des configurations sur ce cluster.

### <a name="configuration-remains-pending"></a>La configuration reste à l’état `Pending`

```console
kubectl -n azure-arc logs -l app.kubernetes.io/component=config-agent -c config-agent
$ k -n pending get gitconfigs.clusterconfig.azure.com  -o yaml
apiVersion: v1
items:
- apiVersion: clusterconfig.azure.com/v1beta1
  kind: GitConfig
  metadata:
    creationTimestamp: "2020-04-13T20:37:25Z"
    generation: 1
    name: pending
    namespace: pending
    resourceVersion: "10088301"
    selfLink: /apis/clusterconfig.azure.com/v1beta1/namespaces/pending/gitconfigs/pending
    uid: d9452407-ff53-4c02-9b5a-51d55e62f704
  spec:
    correlationId: ""
    deleteOperator: false
    enableHelmOperator: false
    giturl: git@github.com:slack/cluster-config.git
    helmOperatorProperties: null
    operatorClientLocation: azurearcfork8s.azurecr.io/arc-preview/fluxctl:0.1.3
    operatorInstanceName: pending
    operatorParams: '"--disable-registry-scanning"'
    operatorScope: cluster
    operatorType: flux
  status:
    configAppliedTime: "2020-04-13T20:38:43.081Z"
    isSyncedWithAzure: true
    lastPolledStatusTime: ""
    message: 'Error: {exit status 1} occurred while doing the operation : {Installing
      the operator} on the config'
    operatorPropertiesHashed: ""
    publicKey: ""
    retryCountPublicKey: 0
    status: Installing the operator
kind: List
metadata:
  resourceVersion: ""
  selfLink: ""
```

## <a name="monitoring"></a>Surveillance

Azure Monitor pour conteneurs exige que son DaemonSet soit exécuté en mode privilégié. Pour configurer correctement un cluster Kubernetes Charmed Canonical pour la supervision, exécutez la commande suivante :

```console
juju config kubernetes-worker allow-privileged=true
```

## <a name="enable-custom-locations-using-service-principal"></a>Activer les emplacements personnalisés à l’aide du principal de service

Lorsque vous connectez votre cluster à Azure Arc ou lorsque vous activez la fonctionnalité d’emplacement personnalisé sur un cluster existant, vous pouvez recevoir l’avertissement suivant :

```console
Unable to fetch oid of 'custom-locations' app. Proceeding without enabling the feature. Insufficient privileges to complete the operation.
```

L’avertissement ci-dessus est émis lorsque vous avez utilisé un principal de service pour vous connecter à Azure et que ce principal de service ne dispose pas des autorisations nécessaires pour obtenir les informations de l’application utilisée par le service Azure Arc. Pour empêcher cette erreur, exécutez les étapes suivantes :

1. Récupérez l’ID d’objet de l’application Azure AD utilisée par le service Azure Arc :

    ```console
    az ad sp show --id 'bc313c14-388c-4e7d-a58e-70017303ee3b' --query objectId -o tsv
    ```

1. Utilisez la valeur `<objectId>` de l’étape ci-dessus pour activer la fonctionnalité Emplacements personnalisés sur le cluster :
    - Si vous activez la fonctionnalité Emplacements personnalisés dans le cadre de la connexion du cluster à Azure Arc, exécutez la commande suivante :

        ```console
        az connectedk8s connect -n <cluster-name> -g <resource-group-name> --custom-locations-oid <objectId>   
        ```

    - Si vous activez la fonctionnalité Emplacements personnalisés sur un cluster Kubernetes avec Azure Arc existant, exécutez la commande suivante :

        ```console
        az connectedk8s enable-features -n <cluster-name> -g <resource-group-name> --custom-locations-oid <objectId> --features cluster-connect custom-locations
        ```

Une fois que les autorisations ci-dessus sont accordées, vous pouvez [activer la fonctionnalité d’emplacement personnalisé](custom-locations.md#enable-custom-locations-on-cluster) sur le cluster.

## <a name="azure-arc-enabled-open-service-mesh"></a>Open Service Mesh avec Azure Arc

Les étapes de dépannage suivantes fournissent une aide concernant la validation du déploiement de tous les composants de l’extension Open Service Mesh sur votre cluster.

### <a name="1-check-osm-controller-deployment"></a>1. Vérifier le **déploiement** du contrôleur OSM
```bash
kubectl get deployment -n arc-osm-system --selector app=osm-controller
```

Si le contrôleur OSM est sain, vous obtiendrez une sortie similaire à la sortie suivante :
```
NAME             READY   UP-TO-DATE   AVAILABLE   AGE
osm-controller   1/1     1            1           59m
```

### <a name="2-check-the-osm-controller-pod"></a>2. Vérifier le **pod** du contrôleur OSM
```bash
kubectl get pods -n arc-osm-system --selector app=osm-controller
```

Si le contrôleur OSM est sain, vous obtiendrez une sortie similaire à la sortie suivante :
```
NAME                            READY   STATUS    RESTARTS   AGE
osm-controller-b5bd66db-wglzl   0/1     Evicted   0          61m
osm-controller-b5bd66db-wvl9w   1/1     Running   0          31m
```

Même si un contrôleur a été _expulsé_ à un moment donné, un autre est `READY 1/1` et `Running` avec `0` redémarrage.
Si la colonne `READY` indique un résultat différent de `1/1`, le maillage de services est en panne.
La colonne `READY` avec `0/1` indique que le conteneur du plan de contrôle se bloque. Nous devons récupérer les journaux. Consultez la section `Get OSM Controller Logs from Azure Support Center` ci-dessous.
La colonne `READY` avec un nombre supérieur à 1 après le signe `/` indique que des side-cars sont installés. Le contrôleur OSM ne fonctionnera probablement pas si des side-cars lui sont attachés.

### <a name="3-check-osm-controller-service"></a>3. Vérifier le **service** du contrôleur OSM
```bash
kubectl get service -n arc-osm-system osm-controller
```

Si le contrôleur OSM est sain, vous obtiendrez la sortie suivante :
```
NAME             TYPE        CLUSTER-IP    EXTERNAL-IP   PORT(S)              AGE
osm-controller   ClusterIP   10.0.31.254   <none>        15128/TCP,9092/TCP   67m
```

> Remarque : Le `CLUSTER-IP` sera différent. Les `NAME` et `PORT(S)` du service doivent être identiques à ceux affichés dans la sortie.

### <a name="4-check-osm-controller-endpoints"></a>4. Vérifier les **points de terminaison** du contrôleur OSM
```bash
kubectl get endpoints -n arc-osm-system osm-controller
```

Si le contrôleur OSM est sain, vous obtiendrez une sortie similaire à la sortie suivante :
```
NAME             ENDPOINTS                              AGE
osm-controller   10.240.1.115:9092,10.240.1.115:15128   69m
```

Si le cluster de l’utilisateur n’a pas de `ENDPOINTS` pour `osm-controller`, cela indique que le plan de contrôle n’est pas sain. Cela peut être dû à un plantage du pod du contrôleur OSM ou au fait qu’il n’a jamais été déployé correctement.

### <a name="5-check-osm-injector-deployment"></a>5. Vérifier le **déploiement** de l’injecteur OSM
```bash
kubectl get deployments -n arc-osm-system osm-injector
```

Si l’injecteur OSM est sain, vous obtiendrez une sortie similaire à la sortie suivante :
```
NAME           READY   UP-TO-DATE   AVAILABLE   AGE
osm-injector   1/1     1            1           73m
```

### <a name="6-check-osm-injector-pod"></a>6. Vérifier le **pod** de l’injecteur OSM
```bash
kubectl get pod -n arc-osm-system --selector app=osm-injector
```

Si l’injecteur OSM est sain, vous obtiendrez une sortie similaire à la sortie suivante :
```
NAME                            READY   STATUS    RESTARTS   AGE
osm-injector-5986c57765-vlsdk   1/1     Running   0          73m
```

La colonne `READY` doit être `1/1`. Toute autre valeur indiquerait un pod osm-injector non sain.

### <a name="7-check-osm-injector-service"></a>7. Vérifier le **service** de l’injecteur OSM
```bash
kubectl get service -n arc-osm-system osm-injector
```

Si l’injecteur OSM est sain, vous obtiendrez une sortie similaire à la sortie suivante :
```
NAME           TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)    AGE
osm-injector   ClusterIP   10.0.39.54   <none>        9090/TCP   75m
```

Vérifiez que l’adresse IP indiquée pour le service `osm-injector` est `9090`. Il ne doit y avoir aucun `EXTERNAL-IP`.

### <a name="8-check-osm-injector-endpoints"></a>8. Vérifier les **points de terminaison** de l’injecteur OSM
```bash
kubectl get endpoints -n arc-osm-system osm-injector
```

Si l’injecteur OSM est sain, vous obtiendrez une sortie similaire à la sortie suivante :
```
NAME           ENDPOINTS           AGE
osm-injector   10.240.1.172:9090   75m
```

Pour qu’OSM fonctionne, il doit y avoir au moins un point de terminaison pour `osm-injector`. L’adresse IP de vos points de terminaison d’injecteur OSM sera différente. Le port `9090` doit être le même.


### <a name="9-check-validating-and-mutating-webhooks"></a>9. Vérifier les webhooks **Validating** et **Mutating** :
```bash
kubectl get ValidatingWebhookConfiguration --selector app=osm-controller
```

Si le webhook Validating est sain, vous obtiendrez une sortie similaire à la sortie suivante :
```
NAME              WEBHOOKS   AGE
arc-osm-webhook-osm   1      81m
```

```bash
kubectl get MutatingWebhookConfiguration --selector app=osm-controller
```


Si le webhook Mutating est sain, vous obtiendrez une sortie similaire à la sortie suivante :
```
NAME              WEBHOOKS   AGE
arc-osm-webhook-osm   1      102m
```

Recherchez le service et le pack de l’autorité de certification du webhook **Validating** :
```
kubectl get ValidatingWebhookConfiguration arc-osm-webhook-osm -o json | jq '.webhooks[0].clientConfig.service'
```

Un webhook Validating bien configuré aura la sortie suivante :
```json
{
  "name": "osm-config-validator",
  "namespace": "arc-osm-system",
  "path": "/validate-webhook",
  "port": 9093
}
```

Recherchez le service et le pack de l’autorité de certification du webhook **Mutating** :
```bash
kubectl get MutatingWebhookConfiguration arc-osm-webhook-osm -o json | jq '.webhooks[0].clientConfig.service'
```

Un webhook Mutating bien configuré aura la sortie suivante :
```
{
  "name": "osm-injector",
  "namespace": "arc-osm-system",
  "path": "/mutate-pod-creation",
  "port": 9090
}
```


Vérifiez si le contrôleur OSM a donné au webhook Validating (or Mutating) un pack d’autorité de certification à l’aide de la commande suivante :

```bash
kubectl get ValidatingWebhookConfiguration arc-osm-webhook-osm -o json | jq -r '.webhooks[0].clientConfig.caBundle' | wc -c
```

```bash
kubectl get MutatingWebhookConfiguration arc-osm-webhook-osm -o json | jq -r '.webhooks[0].clientConfig.caBundle' | wc -c
```

Exemple de sortie :
```bash
1845
```
Le nombre dans la sortie indique le nombre d’octets ou la taille du pack de l’autorité de certification. S’il est vide, égal à 0 ou inférieur à 1000, cela signifie que le pack de l’autorité de certification n’est pas correctement approvisionné. Sans un pack de l’autorité de certification correct, le webhook Validating génère une erreur et vous interdit d’apporter des modifications au ConfigMap `osm-config` dans l’espace de noms `arc-osm-system`.

Examinons un exemple d’erreur lorsque le pack de l’autorité de certification est incorrect :
- Une tentative de modification du ConfigMap `osm-config` :
  ```bash
  kubectl patch ConfigMap osm-config -n arc-osm-system --type merge --patch '{"data":{"config_resync_interval":"2m"}}'
  ```
- Sortie d’erreur :
  ```bash
  Error from server (InternalError): Internal error occurred: failed calling webhook "osm-config-webhook.k8s.io": Post https://osm-config-validator.arc-osm-system.svc:9093/validate-webhook?timeout=30s: x509: certificate signed by unknown authority
  ```

Utilisez l’une des solutions de contournement suivantes lorsque la configuration du webhook **Validating** a un certificat incorrect :
- Option 1. Redémarrer le contrôleur OSM. Cette option permet de redémarrer le contrôleur OSM. Au démarrage, il remplacera le pack de l’autorité de certification des webhooks Mutating et Validating.
  ```bash
  kubectl rollout restart deployment -n arc-osm-system osm-controller
  ```

- Option 2. Supprimer le webhook Validating. La suppression du webhook Validating invalide les mutations du ConfigMap `osm-config`. Tous les patchs seront concernés. Il peut être nécessaire de redémarrer le contrôleur OSM pour réécrire rapidement le pack de l’autorité de certification.
   ```bash
   kubectl delete ValidatingWebhookConfiguration arc-osm-webhook-osm
   ```

- Option 3. Supprimer et corriger. La commande suivante permet de supprimer le webhook Validating, ce qui vous permet d’ajouter des valeurs et de tenter immédiatement d’appliquer un patch.
  ```bash
  kubectl delete ValidatingWebhookConfiguration arc-osm-webhook-osm; kubectl patch ConfigMap osm-config -n arc-osm-system --type merge --patch '{"data":{"config_resync_interval":"15s"}}'
  ```


### <a name="10-check-the-osm-config-configmap"></a>10. Vérifier le **ConfigMap** `osm-config`

>[!Note]
>Le contrôleur OSM ne requiert pas que le ConfigMap `osm-config` soit présent dans l’espace de noms `arc-osm-system`. Le contrôleur a des valeurs par défaut raisonnables pour la configuration et peut fonctionner sans lui.

Vérifiez son existence :
```bash
kubectl get ConfigMap -n arc-osm-system osm-config
```

Vérifiez le contenu du ConfigMap `osm-config` :
```bash
kubectl get ConfigMap -n arc-osm-system osm-config -o json | jq '.data'     
```
Vous obtiendrez la sortie suivante :
```json
{
  "egress": "false",
  "enable_debug_server": "false",
  "enable_privileged_init_container": "false",
  "envoy_log_level": "error",
  "permissive_traffic_policy_mode": "true",
  "prometheus_scraping": "true",
  "service_cert_validity_duration": "24h",
  "tracing_enable": "false",
  "use_https_ingress": "false",
}
```

Référez-vous à la [documentation relative au ConfigMap OSM](https://release-v0-8.docs.openservicemesh.io/docs/osm_config_map/) pour comprendre les valeurs du ConfigMap `osm-config`.

### <a name="11-check-namespaces"></a>11. Vérifier les espaces de noms

>[!Note]
>L’espace de noms arc-osm-system ne fait jamais partie d’un maillage de services et n’est jamais étiqueté ou annoté avec les clés ou valeurs ci-dessous.

Nous utilisons la commande `osm namespace add` pour joindre des espaces de noms à un maillage de services donné.
Lorsqu’un espace de noms kubernetes fait partie du maillage, les conditions suivantes doivent être remplies :

Affichez les annotations de l’espace de noms `bookbuyer` :
```bash
kc get namespace bookbuyer -o json | jq '.metadata.annotations'
```

L’annotation suivante doit être présente :
```
{
  "openservicemesh.io/sidecar-injection": "enabled"
}
```


Affichez les étiquettes de l’espace de noms `bookbuyer` :
```bash
kc get namespace bookbuyer -o json | jq '.metadata.labels'
```

L’étiquette suivante doit être présente :
```
{
  "openservicemesh.io/monitored-by": "osm"
}
```
Notez que si vous n’utilisez pas l’interface CLI `osm`, vous pouvez également ajouter manuellement ces annotations à vos espaces de noms. Si un espace de noms n’est pas annoté avec `"openservicemesh.io/sidecar-injection": "enabled"` ou n’a pas l’étiquette `"openservicemesh.io/monitored-by": "osm"`, l’injecteur OSM n’ajoutera pas les side-cars Envoy.

>[!Note]
>Une fois que `osm namespace add` est appelé, seuls les **nouveaux** pods sont injectés avec un side-car Envoy. Les pods existants doivent être redémarrés à l’aide de la commande `kubectl rollout restard deployment`.


### <a name="12-verify-the-smi-crds"></a>12. Vérifier les CRD SMI
Vérifiez si le cluster possède les CRD requis :
```bash
kubectl get crds
```

Assurez-vous que le CRD correspondent à la même version en amont d’OSM. Par exemple, si vous utilisez la version 0.8.4, assurez-vous que les CRD correspondent à celles qui sont disponibles dans la branche de mise en production v0.8.4 du [projet OSS d’OSM](https://docs.openservicemesh.io/). Référez-vous aux [notes de publication d’OSM](https://github.com/openservicemesh/osm/releases).

Obtenez les versions des CRD installées à l’aide de la commande suivante :
```bash
for x in $(kubectl get crds --no-headers | awk '{print $1}' | grep 'smi-spec.io'); do
    kubectl get crd $x -o json | jq -r '(.metadata.name, "----" , .spec.versions[].name, "\n")'
done
```

S’il manque des CRD, utilisez les commandes suivantes pour les installer sur le cluster. Veillez à remplacer la version dans la commande.
```bash
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8.2/charts/osm/crds/access.yaml

kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8.2/charts/osm/crds/specs.yaml

kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8.2/charts/osm/crds/split.yaml
```
