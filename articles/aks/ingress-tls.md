---
title: Créer une entrée avec certification TLS automatique
titleSuffix: Azure Kubernetes Service
description: Découvrez comment installer et configurer un contrôleur d’entrée NGINX qui utilise Let’s Encrypt pour la génération automatique de certificats TLS dans un cluster Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 04/23/2021
ms.openlocfilehash: 9de0d9e0c85afe7608f59a17ce28f2ba1f50c1bd
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132553109"
---
# <a name="create-an-https-ingress-controller-on-azure-kubernetes-service-aks"></a>Créer un contrôleur d’entrée HTTPS dans Azure Kubernetes Service (AKS)

Un contrôleur d’entrée est un logiciel qui fournit un proxy inversé, un routage du trafic configurable et un arrêt TLS pour les services Kubernetes. Des ressources d’entrée Kubernetes sont utilisées pour configurer les règles d’entrée et les itinéraires des services Kubernetes individuels. En utilisant un contrôleur d’entrée et des règles d’entrée, une seule adresse IP peut être utilisée pour router le trafic vers plusieurs services dans un cluster Kubernetes.

Cet article montre comment déployer le [contrôleur d’entrée NGINX][nginx-ingress] dans un cluster Azure Kubernetes Service (AKS). Le projet [cert-manager][cert-manager] est utilisé pour générer et configurer automatiquement les certificats [Let’s Encrypt][lets-encrypt]. Enfin, plusieurs applications sont exécutées dans le cluster AKS, chacune étant accessible via une seule adresse IP.

Vous pouvez également :

- [Créer un contrôleur d’entrée dans Azure Kubernetes Service (AKS)][aks-ingress-basic]
- [Routage d’applications HTTP][aks-http-app-routing]
- [Create an ingress controller to an internal virtual network in Azure Kubernetes Service (AKS)][aks-ingress-internal] (Créer un contrôleur d’entrée pour un réseau virtuel interne dans Azure Kubernetes Service (AKS))
- [Créer un contrôleur d’entrée qui utilise vos propres certificats TLS][aks-ingress-own-tls]
- [Créer un contrôleur d’entrée qui utilise Let’s Encrypt pour générer automatiquement des certificats TLS avec une adresse IP publique statique][aks-ingress-static-tls]

## <a name="before-you-begin"></a>Avant de commencer

Cet article suppose que vous avez un cluster AKS existant. Si vous avez besoin d’un cluster AKS, consultez le guide de démarrage rapide d’AKS [avec Azure CLI][aks-quickstart-cli]ou avec le [Portail Azure][aks-quickstart-portal].

Cet article suppose également que vous disposez d’[un domaine personnalisé][custom-domain] avec une [zone DNS][dns-zone] dans le même groupe de ressources que votre cluster AKS.

Cet article utilise [Helm 3][helm] pour installer le contrôleur d’entrée Nginx sur une [version prise en charge de Kubernetes][aks-supported versions]. Assurez-vous que vous utilisez la version la plus récente de Helm et que vous avez accès aux référentiels Helm `ingress-nginx` et `jetstack`. Les étapes décrites dans cet article peuvent ne pas être compatibles avec les versions précédentes du Helm Chart, NGINX Ingres Controller ou Kubernetes.

Pour plus d’informations sur la configuration et l’utilisation de Helm, consultez [Installer des applications avec Helm dans Azure Kubernetes Service (AKS)][use-helm]. Pour obtenir des instructions de mise à niveau, consultez la [documentation d’installation de Helm][helm-install].

Pour les besoins de cet article, vous devez également exécuter Azure CLI version 2.0.64 ou ultérieure. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, voir [Installer Azure CLI][azure-cli-install].

En outre, cet article suppose que vous disposez d’un cluster AKS avec un ACR intégré. Pour plus d’informations sur la création d’un cluster AKS avec un ACR intégré, consultez [S’authentifier auprès d’Azure Container Registry à partir du service Kubernetes Azure][aks-integrated-acr].

## <a name="import-the-images-used-by-the-helm-chart-into-your-acr"></a>Importer les images utilisées par le graphique Helm dans votre ACR

Cet article utilise le [graphique Helm du contrôleur d’entrée NGINX][ingress-nginx-helm-chart], lequel repose sur trois images de conteneurs. Utilisez `az acr import` pour importer ces images dans votre ACR.

```azurecli
REGISTRY_NAME=<REGISTRY_NAME>
SOURCE_REGISTRY=k8s.gcr.io
CONTROLLER_IMAGE=ingress-nginx/controller
CONTROLLER_TAG=v1.0.4
PATCH_IMAGE=ingress-nginx/kube-webhook-certgen
PATCH_TAG=v1.1.1
DEFAULTBACKEND_IMAGE=defaultbackend-amd64
DEFAULTBACKEND_TAG=1.5
CERT_MANAGER_REGISTRY=quay.io
CERT_MANAGER_TAG=v1.5.4
CERT_MANAGER_IMAGE_CONTROLLER=jetstack/cert-manager-controller
CERT_MANAGER_IMAGE_WEBHOOK=jetstack/cert-manager-webhook
CERT_MANAGER_IMAGE_CAINJECTOR=jetstack/cert-manager-cainjector

az acr import --name $REGISTRY_NAME --source $SOURCE_REGISTRY/$CONTROLLER_IMAGE:$CONTROLLER_TAG --image $CONTROLLER_IMAGE:$CONTROLLER_TAG
az acr import --name $REGISTRY_NAME --source $SOURCE_REGISTRY/$PATCH_IMAGE:$PATCH_TAG --image $PATCH_IMAGE:$PATCH_TAG
az acr import --name $REGISTRY_NAME --source $SOURCE_REGISTRY/$DEFAULTBACKEND_IMAGE:$DEFAULTBACKEND_TAG --image $DEFAULTBACKEND_IMAGE:$DEFAULTBACKEND_TAG
az acr import --name $REGISTRY_NAME --source $CERT_MANAGER_REGISTRY/$CERT_MANAGER_IMAGE_CONTROLLER:$CERT_MANAGER_TAG --image $CERT_MANAGER_IMAGE_CONTROLLER:$CERT_MANAGER_TAG
az acr import --name $REGISTRY_NAME --source $CERT_MANAGER_REGISTRY/$CERT_MANAGER_IMAGE_WEBHOOK:$CERT_MANAGER_TAG --image $CERT_MANAGER_IMAGE_WEBHOOK:$CERT_MANAGER_TAG
az acr import --name $REGISTRY_NAME --source $CERT_MANAGER_REGISTRY/$CERT_MANAGER_IMAGE_CAINJECTOR:$CERT_MANAGER_TAG --image $CERT_MANAGER_IMAGE_CAINJECTOR:$CERT_MANAGER_TAG
```

> [!NOTE]
> En plus d’importer des images de conteneur dans votre ACR, vous pouvez également importer des graphiques Helm dans votre ACR. Pour plus d’informations, consultez [Envoyer (push) et tirer (pull) des graphiques Helm vers un registre de conteneurs Azure][acr-helm].

## <a name="create-an-ingress-controller"></a>Créer un contrôleur d’entrée

Pour créer le contrôleur d’entrée, utilisez la commande `helm` pour installer *nginx-ingress*. Pour renforcer la redondance, deux réplicas des contrôleurs d’entrée NGINX sont déployés avec le paramètre `--set controller.replicaCount`. Pour tirer pleinement parti de l’exécution de réplicas des contrôleurs d’entrée, vérifiez que votre cluster AKS comprend plusieurs nœuds.

Le contrôleur d’entrée doit également être planifié sur un nœud Linux. Les nœuds Windows Server ne doivent pas exécuter le contrôleur d’entrée. Un sélecteur de nœud est spécifié en utilisant le paramètre `--set nodeSelector` pour que le planificateur Kubernetes exécute le contrôleur d’entrée NGINX sur un nœud Linux.

> [!TIP]
> L’exemple suivant crée un espace de noms Kubernetes pour les ressources d’entrée *ingress-basic* et est destiné à fonctionner dans cet espace de noms. Spécifiez un espace de noms de votre propre environnement, si besoin.

> [!TIP]
> Si vous souhaitez activer la [préservation de l’adresse IP source du client][client-source-ip] pour les requêtes aux conteneurs de votre cluster, ajoutez `--set controller.service.externalTrafficPolicy=Local` à la commande d’installation Helm. L’IP source du client est stockée dans l’en-tête de la requête sous *X-Forwarded-For*. Lors de l’utilisation d’un contrôleur d’entrée pour lequel la conservation de l’adresse IP source du client est activée, un transfert direct TLS ne fonctionne pas.

```console
# Add the ingress-nginx repository
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx

# Set variable for ACR location to use for pulling images
ACR_URL=<REGISTRY_URL>

# Use Helm to deploy an NGINX ingress controller
helm install nginx-ingress ingress-nginx/ingress-nginx \
    --namespace ingress-basic --create-namespace \
    --set controller.replicaCount=2 \
    --set controller.nodeSelector."kubernetes\.io/os"=linux \
    --set controller.image.registry=$ACR_URL \
    --set controller.image.image=$CONTROLLER_IMAGE \
    --set controller.image.tag=$CONTROLLER_TAG \
    --set controller.image.digest="" \
    --set controller.admissionWebhooks.patch.nodeSelector."kubernetes\.io/os"=linux \
    --set controller.admissionWebhooks.patch.image.registry=$ACR_URL \
    --set controller.admissionWebhooks.patch.image.image=$PATCH_IMAGE \
    --set controller.admissionWebhooks.patch.image.tag=$PATCH_TAG \
    --set controller.admissionWebhooks.patch.image.digest="" \
    --set defaultBackend.nodeSelector."kubernetes\.io/os"=linux \
    --set defaultBackend.image.registry=$ACR_URL \
    --set defaultBackend.image.image=$DEFAULTBACKEND_IMAGE \
    --set defaultBackend.image.tag=$DEFAULTBACKEND_TAG \
    --set defaultBackend.image.digest=""
```

Pendant l’installation, une adresse IP publique Azure est créée pour le contrôleur d’entrée. Cette adresse IP publique est statique pour la durée de vie du contrôleur d’entrée. Si vous supprimez le contrôleur d’entrée, l’attribution d’adresse IP publique est perdue. Si vous créez ensuite un contrôleur d’entrée supplémentaires, une nouvelle adresse IP publique est attribuée. Si vous souhaitez conserver l’utilisation de l’adresse IP publique, vous pouvez au lieu de cela [créer un contrôleur d’entrée avec une adresse IP publique statique][aks-ingress-static-tls].

Pour obtenir l’adresse IP publique, utilisez la commande `kubectl get service`. L’affectation de l’adresse IP au service peut prendre quelques minutes.

```console
$ kubectl --namespace ingress-basic get services -o wide -w nginx-ingress-ingress-nginx-controller

NAME                                     TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)                      AGE   SELECTOR
nginx-ingress-ingress-nginx-controller   LoadBalancer   10.0.74.133   EXTERNAL_IP     80:32486/TCP,443:30953/TCP   44s   app.kubernetes.io/component=controller,app.kubernetes.io/instance=nginx-ingress,app.kubernetes.io/name=ingress-nginx
```

Aucune règle d’entrée n’a encore été créée. Si vous accédez à l’adresse IP publique, la page 404 par défaut du contrôleur d’entrée NGINX s’affiche.

## <a name="add-an-a-record-to-your-dns-zone"></a>Ajouter un enregistrement A à votre zone DNS

Ajoutez un enregistrement *A* à votre zone DNS avec l’adresse IP externe du service NGINX à l’aide de la commande [az network dns record-set a add-record][az-network-dns-record-set-a-add-record].

```console
az network dns record-set a add-record \
    --resource-group myResourceGroup \
    --zone-name MY_CUSTOM_DOMAIN \
    --record-set-name "*" \
    --ipv4-address MY_EXTERNAL_IP
```

### <a name="configure-an-fqdn-for-the-ingress-controller"></a>Configurer un nom de domaine complet pour le contrôleur d’entrée
Si vous le souhaitez, vous pouvez configurer un nom de domaine complet pour l’adresse IP du contrôleur d’entrée au lieu d’un domaine personnalisé.  Votre nom de domaine complet se présente sous la forme `<CUSTOM LABEL>.<AZURE REGION NAME>.cloudapp.azure.com`.

Il existe deux méthodes pour cette configuration, décrites ci-dessous.

#### <a name="method-1-set-the-dns-label-using-the-azure-cli"></a>Méthode 1 : Définir l’étiquette DNS à l’aide d’Azure CLI
Notez que cet exemple s'applique à un interpréteur de commandes Bash.

```bash
# Public IP address of your ingress controller
IP="MY_EXTERNAL_IP"

# Name to associate with public IP address
DNSNAME="demo-aks-ingress"

# Get the resource-id of the public ip
PUBLICIPID=$(az network public-ip list --query "[?ipAddress!=null]|[?contains(ipAddress, '$IP')].[id]" --output tsv)

# Update public ip address with DNS name
az network public-ip update --ids $PUBLICIPID --dns-name $DNSNAME

# Display the FQDN
az network public-ip show --ids $PUBLICIPID --query "[dnsSettings.fqdn]" --output tsv
 ```

#### <a name="method-2-set-the-dns-label-using-helm-chart-settings"></a>Méthode 2 : Définir l’étiquette DNS à l’aide des paramètres du chart Helm
Vous pouvez transmettre un paramètre d'annotation à la configuration de votre graphique de barre en utilisant le `--set controller.service.annotations."service\.beta\.kubernetes\.io/azure-dns-label-name"`paramètre.  Ce paramètre peut être défini lors du premier déploiement du contrôleur d’entrée ou être configuré ultérieurement.
L’exemple suivant montre comment mettre à jour ce paramètre après le déploiement du contrôleur.

```
DNS_LABEL="demo-aks-ingress"
NAMESPACE="nginx-basic"

helm upgrade ingress-nginx ingress-nginx/ingress-nginx \
  --namespace $NAMESPACE \
  --set controller.service.annotations."service\.beta\.kubernetes\.io/azure-dns-label-name"=$DNS_LABEL

```

## <a name="install-cert-manager"></a>Installer cert-manager

Le contrôleur d’entrée NGINX prend en charge l’arrêt TLS. Il existe plusieurs façons de récupérer et de configurer des certificats pour le protocole HTTPS. Cet article montre comment utiliser [cert-manager][cert-manager], qui permet de générer et de gérer automatiquement des certificats [Let’s Encrypt][lets-encrypt].

Pour installer le contrôleur cert-manager :

```console
# Label the ingress-basic namespace to disable resource validation
kubectl label namespace ingress-basic cert-manager.io/disable-validation=true

# Add the Jetstack Helm repository
helm repo add jetstack https://charts.jetstack.io

# Update your local Helm chart repository cache
helm repo update

# Install the cert-manager Helm chart
helm install cert-manager jetstack/cert-manager \
  --namespace ingress-basic \
  --version $CERT_MANAGER_TAG \
  --set installCRDs=true \
  --set nodeSelector."kubernetes\.io/os"=linux \
  --set image.repository=$ACR_URL/$CERT_MANAGER_IMAGE_CONTROLLER \
  --set image.tag=$CERT_MANAGER_TAG \
  --set webhook.image.repository=$ACR_URL/$CERT_MANAGER_IMAGE_WEBHOOK \
  --set webhook.image.tag=$CERT_MANAGER_TAG \
  --set cainjector.image.repository=$ACR_URL/$CERT_MANAGER_IMAGE_CAINJECTOR \
  --set cainjector.image.tag=$CERT_MANAGER_TAG
```

Pour plus d’informations sur la configuration cert-manager, voir le [projet cert-manager][cert-manager].

## <a name="create-a-ca-cluster-issuer"></a>Créer un émetteur de cluster d’autorité de certification

Cert-manager a besoin d’une ressource [Issuer][cert-manager-issuer] ou [ClusterIssuer][cert-manager-cluster-issuer] pour pouvoir émettre des certificats. Ces ressources Kubernetes sont identiques du point de vue des fonctionnalités, mais `Issuer` s’applique à un seul espace de noms, tandis que `ClusterIssuer` couvre tous les espaces de noms. Pour plus d’informations, voir la documentation de [l’émetteur cert-manager][cert-manager-issuer].

Créez un émetteur de cluster, tel que `cluster-issuer.yaml`, avec l’exemple de manifeste suivant. Remplacez l’adresse e-mail par une adresse valide de votre organisation :

```yaml
apiVersion: cert-manager.io/v1
kind: ClusterIssuer
metadata:
  name: letsencrypt
spec:
  acme:
    server: https://acme-v02.api.letsencrypt.org/directory
    email: MY_EMAIL_ADDRESS
    privateKeySecretRef:
      name: letsencrypt
    solvers:
    - http01:
        ingress:
          class: nginx
          podTemplate:
            spec:
              nodeSelector:
                "kubernetes.io/os": linux
```

Pour créer l’émetteur, utilisez la commande `kubectl apply`.

```console
kubectl apply -f cluster-issuer.yaml
```

## <a name="run-demo-applications"></a>Exécuter des applications de démonstration

Un contrôleur d’entrée et une solution de gestion de certificats ont été configurés. Maintenant, nous allons exécuter deux applications de démonstration dans le cluster AKS. Pour cet exemple, Helm est utilisé pour déployer deux instances d’une simple application *Hello world*.

Pour voir le contrôleur d’entrée à l’œuvre, exécutons deux applications de démonstration dans votre cluster AKS. Pour cet exemple, vous utilisez `kubectl apply` pour déployer deux instances d’une simple application *Hello world*.

Créez un fichier *aks-helloworld-one.yaml* et copiez-le dans l’exemple de YAML suivant :

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: aks-helloworld-one
spec:
  replicas: 1
  selector:
    matchLabels:
      app: aks-helloworld-one
  template:
    metadata:
      labels:
        app: aks-helloworld-one
    spec:
      containers:
      - name: aks-helloworld-one
        image: mcr.microsoft.com/azuredocs/aks-helloworld:v1
        ports:
        - containerPort: 80
        env:
        - name: TITLE
          value: "Welcome to Azure Kubernetes Service (AKS)"
---
apiVersion: v1
kind: Service
metadata:
  name: aks-helloworld-one
spec:
  type: ClusterIP
  ports:
  - port: 80
  selector:
    app: aks-helloworld-one
```

Créez un fichier *aks-helloworld-two.yaml* et copiez-le dans l’exemple de YAML suivant :

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: aks-helloworld-two
spec:
  replicas: 1
  selector:
    matchLabels:
      app: aks-helloworld-two
  template:
    metadata:
      labels:
        app: aks-helloworld-two
    spec:
      containers:
      - name: aks-helloworld-two
        image: mcr.microsoft.com/azuredocs/aks-helloworld:v1
        ports:
        - containerPort: 80
        env:
        - name: TITLE
          value: "AKS Ingress Demo"
---
apiVersion: v1
kind: Service
metadata:
  name: aks-helloworld-two
spec:
  type: ClusterIP
  ports:
  - port: 80
  selector:
    app: aks-helloworld-two
```

Exécutez les deux applications de démonstration avec `kubectl apply` :

```console
kubectl apply -f aks-helloworld-one.yaml --namespace ingress-basic
kubectl apply -f aks-helloworld-two.yaml --namespace ingress-basic
```

## <a name="create-an-ingress-route"></a>Créer un itinéraire d’entrée

Les deux applications sont maintenant en cours d’exécution sur votre cluster Kubernetes. Toutefois, elles sont configurées avec un service de type `ClusterIP` et ne sont pas accessibles à partir d’Internet. Pour les rendre disponibles publiquement, créez une ressource d’entrée Kubernetes. La ressource d’entrée configure les règles qui acheminent le trafic vers l’une des deux applications.

Dans l’exemple suivant, le trafic vers l’adresse *hello-world-ingress.MY_CUSTOM_DOMAIN* est acheminé vers le service *aks-helloworld-one*. Le trafic vers l’adresse *hello-world-ingress.MY_CUSTOM_DOMAIN/hello-world-two* est routé vers le service *aks-helloworld-two*. Le trafic vers *hello-world-ingress.MY_CUSTOM_DOMAIN/static* est acheminé vers le service nommé *aks-helloworld-one* pour les ressources statiques.

> [!NOTE]
> Si vous avez configuré un nom de domaine complet pour l’adresse IP du contrôleur d’entrée au lieu d’un domaine personnalisé, utilisez le nom de domaine complet au lieu de *hello-world-ingress.MY_CUSTOM_DOMAIN*. Par exemple, si votre nom de domaine complet est *demo-aks-ingress.eastus.cloudapp.azure.com*, remplacez *hello-world-ingress.MY_CUSTOM_DOMAIN* par *demo-aks-ingress.eastus.cloudapp.azure.com* dans `hello-world-ingress.yaml`.

Créez un fichier nommé `hello-world-ingress.yaml` en utilisant l’exemple de code YAML ci-dessous. Mettez à jour les valeurs de *hosts* et de *host* avec le nom DNS que vous avez créé à l’étape précédente.

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: hello-world-ingress
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/rewrite-target: /$1
    nginx.ingress.kubernetes.io/use-regex: "true"
    cert-manager.io/cluster-issuer: letsencrypt
spec:
  tls:
  - hosts:
    - hello-world-ingress.MY_CUSTOM_DOMAIN
    secretName: tls-secret
  rules:
  - host: hello-world-ingress.MY_CUSTOM_DOMAIN
    http:
      paths:
      - path: /hello-world-one(/|$)(.*)
        pathType: Prefix
        backend:
          service:
            name: aks-helloworld-one
            port:
              number: 80
      - path: /hello-world-two(/|$)(.*)
        pathType: Prefix
        backend:
          service:
            name: aks-helloworld-two
            port:
              number: 80
      - path: /(.*)
        pathType: Prefix
        backend:
          service:
            name: aks-helloworld-one
            port:
              number: 80
---
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: hello-world-ingress-static
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/rewrite-target: /static/$2
    nginx.ingress.kubernetes.io/use-regex: "true"
    cert-manager.io/cluster-issuer: letsencrypt
spec:
  tls:
  - hosts:
    - hello-world-ingress.MY_CUSTOM_DOMAIN
    secretName: tls-secret
  rules:
  - host: hello-world-ingress.MY_CUSTOM_DOMAIN
    http:
      paths:
      - path:
        pathType: Prefix
        backend:
          service:
            name: aks-helloworld-one
            port: 
              number: 80
        path: /static(/|$)(.*)
```

Créez la ressource d’entrée avec la commande `kubectl apply`.

```console
kubectl apply -f hello-world-ingress.yaml --namespace ingress-basic
```

## <a name="verify-a-certificate-object-has-been-created"></a>Vérifier qu’un objet de certificat a été créé

Il faut ensuite créer une ressource de certificat. Elle définit le certificat X.509 souhaité. Pour plus d’informations, consultez [Certificats cert-manager][cert-manager-certificates]. cert-manager a automatiquement créé un objet de certificat à l’aide d’ingress-shim ; le certificat est automatiquement déployé avec cert-manager depuis la version 0.2.2. Pour plus d’informations, consultez la [documentation d’ingress-shim][ingress-shim].

Pour vérifier que le certificat a été créé avec succès, utilisez la commande `kubectl get certificate --namespace ingress-basic` et vérifiez que *READY* a la valeur *True*, ce qui peut prendre plusieurs minutes.

```console
$ kubectl get certificate --namespace ingress-basic

NAME         READY   SECRET       AGE
tls-secret   True    tls-secret   11m
```

## <a name="test-the-ingress-configuration"></a>Tester la configuration d’entrée

Ouvrez un navigateur web sur le domaine *hello-world-ingress.MY_CUSTOM_DOMAIN* de votre contrôleur d’entrée Kubernetes. Notez que vous êtes redirigé pour utiliser HTTPS, que le certificat est approuvé et que l’application de démonstration est affichée dans le navigateur web. Ajoutez le chemin */hello-world-two* et notez que la deuxième application de démonstration dont le titre est personnalisé est affichée.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Cet article vous a montré comment utiliser Helm pour installer les composants d’entrée, les certificats et les exemples d’applications. Quand vous déployez un graphique Helm, une série de ressources Kubernetes est créée. Ces ressources incluent des pods, des déploiements et des services. Pour nettoyer ces ressources, vous pouvez supprimer l’espace de noms exemple en entier ou des ressources individuelles.

### <a name="delete-the-sample-namespace-and-all-resources"></a>Supprimer l’espace de noms exemple et toutes les ressources

Pour supprimer l’espace de noms exemple en entier, utilisez la commande `kubectl delete` et spécifiez le nom de votre espace de noms. Toutes les ressources de l’espace de noms sont supprimées.

```console
kubectl delete namespace ingress-basic
```

### <a name="delete-resources-individually"></a>Supprimer les ressources individuellement

Sinon, une approche plus précise consiste à supprimer les ressources individuelles créées. Tout d’abord, supprimez les ressources de l’émetteur de cluster :

```console
kubectl delete -f cluster-issuer.yaml --namespace ingress-basic
```

Listez les versions de Helm avec la commande `helm list`. Recherchez les graphiques nommés *nginx* et *cert-manager*, comme illustré dans l’exemple de sortie suivant :

```console
$ helm list --namespace ingress-basic

NAME                    NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                   APP VERSION
cert-manager            ingress-basic   1               2020-01-15 10:23:36.515514 -0600 CST    deployed        cert-manager-v0.13.0    v0.13.0    
nginx                   ingress-basic   1               2020-01-15 10:09:45.982693 -0600 CST    deployed        nginx-ingress-1.29.1    0.27.0  
```

Désinstallez les versions avec la commande `helm uninstall`. L’exemple suivant désinstalle les déploiements d’entrée et de gestionnaire de certificats NGINX.

```console
$ helm uninstall cert-manager nginx --namespace ingress-basic

release "cert-manager" uninstalled
release "nginx" uninstalled
```

Ensuite, supprimez les deux exemples d’applications :

```console
kubectl delete -f aks-helloworld-one.yaml --namespace ingress-basic
kubectl delete -f aks-helloworld-two.yaml --namespace ingress-basic
```

Supprimez la route d’entrée qui a dirigé le trafic vers les exemples d’applications :

```console
kubectl delete -f hello-world-ingress.yaml --namespace ingress-basic
```

Pour finir, vous pouvez supprimer l’espace de noms lui-même. Utilisez la commande `kubectl delete` et spécifiez le nom de votre espace de noms :

```console
kubectl delete namespace ingress-basic
```

## <a name="next-steps"></a>Étapes suivantes

Cet article a mentionné certains composants qui n’appartiennent pas à AKS. Pour plus d’informations sur ces composants, consultez les pages projet suivantes :

- [Interface CLI Helm][helm-cli]
- [Contrôleur d’entrée NGINX][nginx-ingress]
- [cert-manager][cert-manager]

Vous pouvez également :

- [Créer un contrôleur d’entrée dans Azure Kubernetes Service (AKS)][aks-ingress-basic]
- [Routage d’applications HTTP][aks-http-app-routing]
- [Create an ingress controller to an internal virtual network in Azure Kubernetes Service (AKS)][aks-ingress-internal] (Créer un contrôleur d’entrée pour un réseau virtuel interne dans Azure Kubernetes Service (AKS))
- [Créer un contrôleur d’entrée qui utilise vos propres certificats TLS][aks-ingress-own-tls]
- [Créer un contrôleur d’entrée qui utilise Let’s Encrypt pour générer automatiquement des certificats TLS avec une adresse IP publique statique][aks-ingress-static-tls]

<!-- LINKS - external -->
[az-network-dns-record-set-a-add-record]: /cli/azure/network/dns/record-set/#az_network_dns_record_set_a_add_record
[custom-domain]: ../app-service/manage-custom-dns-buy-domain.md#buy-an-app-service-domain
[dns-zone]: ../dns/dns-getstarted-cli.md
[helm]: https://helm.sh/
[helm-cli]: ./kubernetes-helm.md
[cert-manager]: https://github.com/jetstack/cert-manager
[cert-manager-certificates]: https://cert-manager.io/docs/concepts/certificate/
[ingress-shim]: https://cert-manager.io/docs/usage/ingress/
[cert-manager-cluster-issuer]: https://cert-manager.io/docs/concepts/issuer/
[cert-manager-issuer]: https://cert-manager.io/docs/concepts/issuer/
[lets-encrypt]: https://letsencrypt.org/
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx
[helm-install]: https://docs.helm.sh/using_helm/#installing-helm
[ingress-nginx-helm-chart]: https://github.com/kubernetes/ingress-nginx/tree/main/charts/ingress-nginx

<!-- LINKS - internal -->
[use-helm]: kubernetes-helm.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az_aks_show
[az-network-public-ip-create]: /cli/azure/network/public-ip#az_network_public_ip_create
[aks-ingress-internal]: ingress-internal-ip.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-ingress-basic]: ingress-basic.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-own-tls]: ingress-own-tls.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[client-source-ip]: concepts-network.md#ingress-controllers
[install-azure-cli]: /cli/azure/install-azure-cli
[aks-supported versions]: supported-kubernetes-versions.md
[aks-integrated-acr]: cluster-container-registry-integration.md?tabs=azure-cli#create-a-new-aks-cluster-with-acr-integration
[acr-helm]: ../container-registry/container-registry-helm-repos.md
