---
title: Configurer le pilote CSI du magasin de secrets pour activer le contrôleur d’entrée NGINX avec TLS sur Azure Kubernetes Service
description: Comment configurer le pilote CSI du magasin de secrets pour activer le contrôleur d’entrée NGINX avec TLS sur Azure Kubernetes Service (AKS).
author: nickomang
ms.author: nickoman
ms.service: container-service
ms.topic: how-to
ms.date: 10/19/2021
ms.custom: template-how-to
ms.openlocfilehash: dd6945da0b3c4170082d20e5481d06048b7c3dde
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132284794"
---
# <a name="set-up-secrets-store-csi-driver-to-enable-nginx-ingress-controller-with-tls"></a>Configurer le pilote CSI du magasin de secrets pour activer le contrôleur d’entrée NGINX avec TLS

Cet article vous guide tout au long du processus de sécurisation d’un contrôleur d’entrée NGINX avec TLS en utilisant un cluster Azure Kubernetes Service (AKS) et une instance Azure Key Vault (AKV). Pour plus d’informations, consultez [TLS dans Kubernetes][kubernetes-ingress-tls].

L’importation du certificat TLS d’entrée dans le cluster peut être effectuée avec l’une des deux méthodes suivantes :

- **Application** : le manifeste de déploiement de l’application déclare et monte le volume du fournisseur. Le certificat est mis à disposition dans le cluster seulement quand l’application est déployée et le secret est également supprimé quand l’application est supprimée. Ce scénario convient aux équipes de développement responsables de l’infrastructure de sécurité de l’application et de son intégration au cluster.
- **Contrôleur d’entrée** : le déploiement d’entrée est modifié pour déclarer et monter le volume du fournisseur. Le secret est importé pendant la création des pods d’entrée. Les pods de l’application n’ont pas accès au certificat TLS. Ce scénario convient quand une équipe (par ex., le service informatique) gère et provisionne les composants de l’infrastructure et du réseau (y compris les certificats TLS HTTPS), et que d’autres équipes gèrent le cycle de vie des applications. Dans ce cas, l’entrée est propre à un seul espace de noms ou à une seule charge de travail, et déployé dans le même espace de noms que l’application.

## <a name="prerequisites"></a>Prérequis

- Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.
- Avant de commencer, vérifiez que votre version d’Azure CLI est >= `2.30.0` ou [installez la dernière version](/cli/azure/install-azure-cli).
- Un cluster AKS avec le pilote CSI du magasin de secrets configuré.
- Une instance Azure Key Vault.

## <a name="generate-a-tls-certificate"></a>Générer un certificat TLS

```bash
export CERT_NAME=ingresscert
openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
    -out ingress-tls.crt \
    -keyout ingress-tls.key \
    -subj "/CN=demo.test.com/O=ingress-tls"
```

### <a name="import-the-certificate-to-akv"></a>Importer le certificat dans AKV

```bash
export AKV_NAME="[YOUR AKV NAME]"
openssl pkcs12 -export -in ingress-tls.crt -inkey ingress-tls.key  -out $CERT_NAME.pfx
# skip Password prompt
```

```azurecli-interactive
az keyvault certificate import --vault-name $AKV_NAME -n $CERT_NAME -f $CERT_NAME.pfx
```

## <a name="deploy-a-secretproviderclass"></a>Déployer une SecretProviderClass

Commencez par créer un espace de noms :

```bash
export NAMESPACE=ingress-test
```

```azurecli-interactive
kubectl create ns $NAMESPACE
```

Sélectionnez un [mécanisme pour fournir une identité d’accès][csi-ss-identity-access] et configurez votre YAML de SecretProviderClass en conséquence. En outre :

- Veillez à utiliser `objectType=secret`, qui est la seule façon d’obtenir la clé privée et le certificat d’AKV.
- Définissez `kubernetes.io/tls` comme `type` dans votre section `secretObjects`.

Pour obtenir un exemple de ce à quoi peut ressembler votre SecretProviderClass, consultez ce qui suit :

```yml
apiVersion: secrets-store.csi.x-k8s.io/v1
kind: SecretProviderClass
metadata:
  name: azure-tls
spec:
  provider: azure
  secretObjects:                            # secretObjects defines the desired state of synced K8s secret objects
  - secretName: ingress-tls-csi
    type: kubernetes.io/tls
    data: 
    - objectName: $CERT_NAME
      key: tls.key
    - objectName: $CERT_NAME
      key: tls.crt
  parameters:
    usePodIdentity: "false"
    keyvaultName: $AKV_NAME                 # the name of the AKV instance
    objects: |
      array:
        - |
          objectName: $CERT_NAME
          objectType: secret
    tenantId: $TENANT_ID                    # the tenant ID of the AKV instance
```

Appliquer la SecretProviderClass à votre cluster Kubernetes :

```bash
kubectl apply -f secretProviderClass.yaml -n $NAMESPACE
```

## <a name="deploy-the-ingress-controller"></a>Déployer le contrôleur d’entrée

### <a name="add-the-official-ingress-chart-repository"></a>Ajouter le dépôt de graphiques d’entrée officiel

```bash
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
helm repo update
```

### <a name="configure-and-deploy-the-nginx-ingress"></a>Configurer et déployer l’entrée NGINX

Comme indiqué ci-dessus, selon votre scénario, vous pouvez choisir de lier le certificat à l’application ou au contrôleur d’entrée. Suivez les instructions ci-dessous en fonction de votre sélection :

#### <a name="bind-certificate-to-application"></a>Lier le certificat à l’application

Le déploiement de l’application référence le fournisseur Azure Key Vault du pilote CSI du magasin de secrets.

```bash
helm install ingress-nginx/ingress-nginx --generate-name \
    --namespace $NAMESPACE \
    --set controller.replicaCount=2 \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux
```

#### <a name="bind-certificate-to-ingress-controller"></a>Lier le certificat au contrôleur d’entrée

Le déploiement du contrôleur d’entrée référence le fournisseur Azure Key Vault du pilote CSI du magasin de secrets.

> [!NOTE]
> Si vous n’utilisez pas l’identité de pod Azure Active Directory (AAD) comme mécanisme d’accès, supprimez la ligne qui contient `--set controller.podLabels.aadpodidbinding=$AAD_POD_IDENTITY_NAME`

```bash
helm install ingress-nginx/ingress-nginx --generate-name \
    --namespace $NAMESPACE \
    --set controller.replicaCount=2 \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set controller.podLabels.aadpodidbinding=$AAD_POD_IDENTITY_NAME \
    -f - <<EOF
controller:
  extraVolumes:
      - name: secrets-store-inline
        csi:
          driver: secrets-store.csi.k8s.io
          readOnly: true
          volumeAttributes:
            secretProviderClass: "azure-tls"
  extraVolumeMounts:
      - name: secrets-store-inline
        mountPath: "/mnt/secrets-store"
        readOnly: true
EOF
```

Vérifiez que le secret Kubernetes a été créé :

```bash
kubectl get secret -n $NAMESPACE

NAME                                             TYPE                                  DATA   AGE
ingress-tls-csi                                  kubernetes.io/tls                     2      1m34s
```

## <a name="deploy-the-application"></a>Déployer l’application

Là encore, selon votre scénario, les instructions changent légèrement. Suivez les instructions correspondant au scénario que vous avez sélectionné jusqu’à présent :

### <a name="deploy-the-application-using-an-application-reference"></a>Déployer l’application à l’aide d’une référence d’application

Créez un fichier nommé `deployment.yaml` avec le contenu suivant :

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: busybox-one
  labels:
    app: busybox-one
spec:
  replicas: 1
  selector:
    matchLabels:
      app: busybox-one
  template:
    metadata:
      labels:
        app: busybox-one
    spec:
      containers:
      - name: busybox
        image: k8s.gcr.io/e2e-test-images/busybox:1.29-1
        command:
          - "/bin/sleep"
          - "10000"
        volumeMounts:
        - name: secrets-store-inline
          mountPath: "/mnt/secrets-store"
          readOnly: true
      volumes:
        - name: secrets-store-inline
          csi:
            driver: secrets-store.csi.k8s.io
            readOnly: true
            volumeAttributes:
              secretProviderClass: "azure-tls"
---
apiVersion: v1
kind: Service
metadata:
  name: busybox-one
spec:
  type: ClusterIP
  ports:
  - port: 80
  selector:
    app: busybox-one
```

Ensuite, appliquez-le à votre cluster :

```bash
kubectl apply -f deployment.yaml -n $NAMESPACE
```

Vérifiez que le secret Kubernetes a été créé :

```bash
kubectl get secret -n $NAMESPACE

NAME                                             TYPE                                  DATA   AGE
ingress-tls-csi                                  kubernetes.io/tls                     2      1m34s
```

### <a name="deploy-the-application-using-an-ingress-controller-reference"></a>Déployer l’application à l’aide d’une référence de contrôleur d’entrée

Créez un fichier nommé `deployment.yaml` avec le contenu suivant :

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: busybox-one
  labels:
    app: busybox-one
spec:
  replicas: 1
  selector:
    matchLabels:
      app: busybox-one
  template:
    metadata:
      labels:
        app: busybox-one
    spec:
      containers:
      - name: busybox
        image: k8s.gcr.io/e2e-test-images/busybox:1.29-1
        command:
          - "/bin/sleep"
          - "10000"
---
apiVersion: v1
kind: Service
metadata:
  name: busybox-one
spec:
  type: ClusterIP
  ports:
  - port: 80
  selector:
    app: busybox-one
```

Ensuite, appliquez-le à votre cluster :

```bash
kubectl apply -f deployment.yaml -n $NAMESPACE
```

## <a name="deploy-an-ingress-resource-referencing-the-secret"></a>Déployer une ressource d’entrée qui référence le secret

Enfin, nous pouvons déployer une ressource d’entrée Kubernetes qui référence notre secret. Créez un fichier `ingress.yaml` avec le contenu suivant :

```yml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: ingress-tls
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/rewrite-target: /$1
spec:
  tls:
  - hosts:
    - demo.test.com
    secretName: ingress-tls-csi
  rules:
  - host: demo.test.com
    http:
      paths:
      - backend:
          service:
            name: busybox-one
            port:
              number: 80
        path: /(.*)
      - backend:
          service:
            name: busybox-two
            port:
              number: 80
        path: /two(/|$)(.*)
```

Notez la section `tls` qui référence le secret que nous avons créé précédemment et appliquez le fichier à votre cluster :

```bash
kubectl apply -f ingress.yaml -n $NAMESPACE
```

## <a name="obtain-the-external-ip-address-of-the-ingress-controller"></a>Obtenir l’adresse IP externe du contrôleur d’entrée

Utilisez `kubectl get service` pour obtenir l’adresse IP externe du contrôleur d’entrée.

```bash
 kubectl get service -l app=nginx-ingress --namespace $NAMESPACE

NAME                                       TYPE           CLUSTER-IP     EXTERNAL-IP      PORT(S)                      AGE
nginx-ingress-1588032400-controller        LoadBalancer   10.0.255.157   52.xx.xx.xx      80:31293/TCP,443:31265/TCP   19m
nginx-ingress-1588032400-default-backend   ClusterIP      10.0.223.214   <none>           80/TCP                       19m 
```

## <a name="test-ingress-secured-with-tls"></a>Tester l’entrée sécurisée avec TLS

Utilisez `curl` pour vérifier que votre entrée a été correctement configurée avec TLS. Veillez à utiliser l’IP externe que vous avez obtenue à l’étape précédente :

```bash
curl -v -k --resolve demo.test.com:443:52.xx.xx.xx https://demo.test.com

# You should see output similar to the following
*  subject: CN=demo.test.com; O=ingress-tls
*  start date: Oct 15 04:23:46 2021 GMT
*  expire date: Oct 15 04:23:46 2022 GMT
*  issuer: CN=demo.test.com; O=ingress-tls
*  SSL certificate verify result: self signed certificate (18), continuing anyway.
```

<!-- LINKS INTERNAL -->
[csi-ss-identity-access]: ./csi-secrets-store-identity-access.md
<!-- LINKS EXTERNAL -->
[kubernetes-ingress-tls]: https://kubernetes.io/docs/concepts/services-networking/ingress/#tls
