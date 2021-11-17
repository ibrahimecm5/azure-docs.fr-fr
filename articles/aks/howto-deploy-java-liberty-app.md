---
title: Déployer une application Java avec Open Liberty ou WebSphere Liberty sur un cluster Azure Kubernetes Service (AKS)
recommendations: false
description: Déployez une application Java avec Open Liberty ou WebSphere Liberty sur un cluster Azure Kubernetes Service (AKS).
author: majguo
ms.author: jiangma
ms.service: container-service
ms.topic: conceptual
ms.date: 02/01/2021
keywords: java, jakartaee, javaee, microprofile, open-liberty, websphere-liberty, aks, kubernetes
ms.custom: devx-track-java, devx-track-javaee, devx-track-javaee-liberty, devx-track-javaee-liberty-aks
ms.openlocfilehash: f43f0a6038c9bd362f948722534c27e0906261c7
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132551422"
---
# <a name="deploy-a-java-application-with-open-liberty-or-websphere-liberty-on-an-azure-kubernetes-service-aks-cluster"></a>Déployer une application Java avec Open Liberty ou WebSphere Liberty sur un cluster Azure Kubernetes Service (AKS)

Cet article montre comment :  
* Exécutez votre application Java, Java EE, Jakarta EE ou MicroProfile sur le runtime Open Liberty ou WebSphere Liberty.
* Générez l’image Docker de l’application à l’aide d’images conteneur Open Liberty.
* Déployez l’application en conteneur sur un cluster AKS à l’aide de l’opérateur Open Liberty.   

L’Opérateur Open Liberty simplifie le déploiement et la gestion des applications s’exécutant sur des clusters Kubernetes. Avec l’Opérateur Open Liberty, vous pouvez également effectuer des opérations plus avancées, telles que la collecte des traces et des vidages. 

Pour plus d’informations sur Open Liberty, consultez [la page de projet Open Liberty](https://openliberty.io/). Pour plus d’informations sur IBM WebSphere Liberty, consultez [la page du produit WebSphere Liberty](https://www.ibm.com/cloud/websphere-liberty).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

* Cet article nécessite la dernière version d’Azure CLI. Si vous utilisez Azure Cloud Shell, la version la plus récente est déjà installée.
* Si vous exécutez les commandes de ce guide en local (et non via Azure Cloud Shell) :
  * Préparez un ordinateur local avec un système d’exploitation de type UNIX installé (par exemple, Ubuntu, macOS, sous-système Windows pour Linux).
  * Installez une implémentation de Java SE (par exemple, [AdoptOpenJDK OpenJDK 8 LTS/OpenJ9](https://adoptopenjdk.net/?variant=openjdk8&jvmVariant=openj9)).
  * Installez [Maven](https://maven.apache.org/download.cgi) 3.5.0 ou une version ultérieure.
  * Installez [Docker](https://docs.docker.com/get-docker/) pour votre système d’exploitation.

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Un groupe de ressources Azure est un groupe logique dans lequel des ressources Azure sont déployées et gérées.  

Créez un groupe de ressources appelé *java-liberty-project* à l’aide de la commande [az group create](/cli/azure/group#az_group_create) dans l’emplacement *eastus*. Ce groupe de ressources sera utilisé plus tard pour créer l’instance Azure Container Registry (ACR) et le cluster AKS. 

```azurecli-interactive
RESOURCE_GROUP_NAME=java-liberty-project
az group create --name $RESOURCE_GROUP_NAME --location eastus
```

## <a name="create-an-acr-instance"></a>Créer une instance ACR

Utilisez la commande [az acr create](/cli/azure/acr#az_acr_create) pour créer l’instance ACR. L’exemple suivant crée une instance ACR nommée *youruniqueacrname*. Assurez-vous que *youruniqueacrname* est unique dans Azure.

```azurecli-interactive
export REGISTRY_NAME=youruniqueacrname
az acr create --resource-group $RESOURCE_GROUP_NAME --name $REGISTRY_NAME --sku Basic --admin-enabled
```

Après un bref laps de temps, vous devriez voir la sortie JSON suivante :

```output
  "provisioningState": "Succeeded",
  "publicNetworkAccess": "Enabled",
  "resourceGroup": "java-liberty-project",
```

### <a name="connect-to-the-acr-instance"></a>Se connecter à l’instance ACR

Vous devrez vous connecter à l’instance ACR avant de pouvoir envoyer une image vers celle-ci. Exécutez les commandes suivantes pour vérifier la connexion :

```azurecli-interactive
export LOGIN_SERVER=$(az acr show -n $REGISTRY_NAME --query 'loginServer' -o tsv)
export USER_NAME=$(az acr credential show -n $REGISTRY_NAME --query 'username' -o tsv)
export PASSWORD=$(az acr credential show -n $REGISTRY_NAME --query 'passwords[0].value' -o tsv)

docker login $LOGIN_SERVER -u $USER_NAME -p $PASSWORD
```

Vous devez voir `Login Succeeded` à la fin de la sortie de la commande si vous êtes bien connecté à l’instance ACR.

## <a name="create-an-aks-cluster"></a>Créer un cluster AKS

Utilisez la commande [az aks create](/cli/azure/aks#az_aks_create) pour créer un cluster AKS. L’exemple suivant crée un cluster à un nœud nommé *myAKSCluster*. L’exécution de cette commande prend plusieurs minutes.

```azurecli-interactive
CLUSTER_NAME=myAKSCluster
az aks create --resource-group $RESOURCE_GROUP_NAME --name $CLUSTER_NAME --node-count 1 --generate-ssh-keys --enable-managed-identity
```

Au bout de quelques minutes, la commande se termine et retourne des informations au format JSON sur le cluster, dont ce qui suit :

```output
  "nodeResourceGroup": "MC_java-liberty-project_myAKSCluster_eastus",
  "privateFqdn": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "java-liberty-project",
```

### <a name="connect-to-the-aks-cluster"></a>Se connecter au cluster AKS

Pour gérer un cluster Kubernetes, vous utilisez [kubectl](https://kubernetes.io/docs/reference/kubectl/overview/), le client de ligne de commande Kubernetes. Si vous utilisez Azure Cloud Shell, `kubectl` est déjà installé. Pour installer `kubectl` en local, utilisez la commande [az aks install-cli](/cli/azure/aks#az_aks_install_cli) :

```azurecli-interactive
az aks install-cli
```

Pour configurer `kubectl` afin de vous connecter à votre cluster Kubernetes, exécutez la commande [az aks get-credentials](/cli/azure/aks#az_aks_get_credentials). Cette commande télécharge les informations d’identification et configure l’interface CLI Kubernetes pour les utiliser.

```azurecli-interactive
az aks get-credentials --resource-group $RESOURCE_GROUP_NAME --name $CLUSTER_NAME --overwrite-existing
```

> [!NOTE]
> La commande ci-dessus utilise l’emplacement par défaut pour le [fichier de configuration Kubernetes](https://kubernetes.io/docs/concepts/configuration/organize-cluster-access-kubeconfig/), à savoir `~/.kube/config`. Vous pouvez spécifier un autre emplacement pour votre fichier de configuration Kubernetes à l’aide de *--file*.

Pour vérifier la connexion à votre cluster, utilisez la commande [kubectl get]( https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get) pour retourner une liste des nœuds du cluster.

```azurecli-interactive
kubectl get nodes
```

L’exemple de sortie suivant montre le nœud unique créé au cours des étapes précédentes. Vérifiez que l’état du nœud est *Ready* :

```output
NAME                                STATUS   ROLES   AGE     VERSION
aks-nodepool1-xxxxxxxx-yyyyyyyyyy   Ready    agent   76s     v1.18.10
```

## <a name="install-open-liberty-operator"></a>Installer l’Opérateur Open Liberty

Une fois que vous avez créé le cluster et que vous y êtes connecté, installez l’[Opérateur Open Liberty](https://github.com/OpenLiberty/open-liberty-operator/tree/master/deploy/releases/0.7.1) en exécutant les commandes suivantes.

```azurecli-interactive
OPERATOR_NAMESPACE=default
WATCH_NAMESPACE='""'

# Install Custom Resource Definitions (CRDs) for OpenLibertyApplication
kubectl apply -f https://raw.githubusercontent.com/OpenLiberty/open-liberty-operator/master/deploy/releases/0.7.1/openliberty-app-crd.yaml

# Install cluster-level role-based access
curl -L https://raw.githubusercontent.com/OpenLiberty/open-liberty-operator/master/deploy/releases/0.7.1/openliberty-app-cluster-rbac.yaml \
      | sed -e "s/OPEN_LIBERTY_OPERATOR_NAMESPACE/${OPERATOR_NAMESPACE}/" \
      | kubectl apply -f -

# Install the operator
curl -L https://raw.githubusercontent.com/OpenLiberty/open-liberty-operator/master/deploy/releases/0.7.1/openliberty-app-operator.yaml \
      | sed -e "s/OPEN_LIBERTY_WATCH_NAMESPACE/${WATCH_NAMESPACE}/" \
      | kubectl apply -n ${OPERATOR_NAMESPACE} -f -
```

## <a name="build-application-image"></a>Créer une image d’application

Pour déployer et exécuter votre application Liberty sur un cluster AKS, conteneurisez votre application en tant qu’image Docker à l’aide d’[images conteneur Open Liberty](https://github.com/OpenLiberty/ci.docker) ou d’[images conteneur WebSphere Liberty](https://github.com/WASdev/ci.docker).

1. Clonez l’exemple de code de ce guide. L’exemple se trouve sur [GitHub](https://github.com/Azure-Samples/open-liberty-on-aks).
1. Changez le répertoire en `javaee-app-simple-cluster` de votre clone local.
1. Exécutez `mvn clean package` pour empaqueter l’application.
1. Exécutez `mvn liberty:dev` pour tester l'application. Vous devez voir `The defaultServer server is ready to run a smarter planet.` dans le résultat de la commande en cas de réussite. Utilisez `CTRL-C` pour arrêter l’application.
1. Récupérer des valeurs pour les propriétés `artifactId` et `version` définies dans le `pom.xml`.

   ```azurecli-interactive
   artifactId=$(mvn -q -Dexec.executable=echo -Dexec.args='${project.artifactId}' --non-recursive exec:exec)
   version=$(mvn -q -Dexec.executable=echo -Dexec.args='${project.version}' --non-recursive exec:exec)
   ```
1. Exécutez `cd target` pour basculer le répertoire vers le build de l’exemple.
1. Exécutez l’une des commandes suivantes pour générer l’image de l’application et l’envoyer à l’instance ACR.
   * Générez avec l’image de base Open Liberty si vous préférez utiliser Open Liberty comme runtime Java™ open source léger :

     ```azurecli-interactive
     # Build and tag application image. This will cause the ACR instance to pull the necessary Open Liberty base images.
     az acr build -t ${artifactId}:${version} -r $REGISTRY_NAME .
     ```

   * Générez avec l’image de base WebSphere Liberty si vous préférez utiliser une version commerciale d’Open Liberty :

     ```azurecli-interactive
     # Build and tag application image. This will cause the ACR instance to pull the necessary WebSphere Liberty base images.
     az acr build -t ${artifactId}:${version} -r $REGISTRY_NAME --file=Dockerfile-wlp .
     ```

## <a name="deploy-application-on-the-aks-cluster"></a>Déployer une application sur le cluster AKS

Suivez les étapes ci-dessous pour déployer l’application Liberty sur le cluster AKS.

1. Créez un secret d’extraction afin que le cluster AKS soit authentifié pour extraire l’image de l’instance ACR.

   ```azurecli-interactive
   kubectl create secret docker-registry acr-secret \
      --docker-server=${LOGIN_SERVER} \
      --docker-username=${USER_NAME} \
      --docker-password=${PASSWORD}
   ```

1. Vérifiez que le répertoire de travail actuel correspond au `javaee-app-simple-cluster/target` de votre clone local.
1. Exécutez les commandes suivantes pour déployer votre application Liberty avec 3 réplicas sur le cluster AKS. La sortie de commande est également affichée inlined.

   ```azurecli-interactive
   # Create OpenLibertyApplication "javaee-cafe-cluster"
   kubectl apply -f openlibertyapplication.yaml

   openlibertyapplication.openliberty.io/javaee-cafe-cluster created

   # Check if OpenLibertyApplication instance is created
   kubectl get openlibertyapplication ${artifactId}-cluster

   NAME                        IMAGE                                                   EXPOSED   RECONCILED   AGE
   javaee-cafe-cluster         youruniqueacrname.azurecr.io/javaee-cafe:1.0.25         True         59s

   # Check if deployment created by Operator is ready
   kubectl get deployment ${artifactId}-cluster --watch

   NAME                        READY   UP-TO-DATE   AVAILABLE   AGE
   javaee-cafe-cluster         0/3     3            0           20s
   ```

1. Attendez de voir `3/3` sous la colonne `READY` et `3` sous la colonne `AVAILABLE` et utilisez `CTRL-C` pour arrêter le processus de surveillance `kubectl`.

### <a name="test-the-application"></a>Test de l’application

Quand l’application s’exécute, un service Kubernetes d’équilibrage de charge expose le front-end de l’application sur Internet. Ce processus peut prendre un certain temps.

Pour surveiller la progression, utilisez la commande [kubectl get service](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get) avec l’argument `--watch`.

```azurecli-interactive
kubectl get service ${artifactId}-cluster --watch

NAME                        TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)          AGE
javaee-cafe-cluster         LoadBalancer   10.0.251.169   52.152.189.57   80:31732/TCP     68s
```

Quand l’adresse *EXTERNAL-IP* passe de l’état *pending* à une adresse IP publique réelle, utilisez `CTRL-C` pour arrêter le processus de surveillance `kubectl`.

Ouvrez un navigateur web sur l’adresse IP externe de votre service (`52.152.189.57` pour l’exemple ci-dessus) afin d’afficher la page d’accueil à l’application. Le nom de pod de vos réplicas d’application doit s’afficher en haut à gauche de la page. Attendez quelques minutes et actualisez la page. Vous verrez un autre nom de pod affiché en raison de l’équilibrage de charge fourni par le cluster AKS.

:::image type="content" source="./media/howto-deploy-java-liberty-app/deploy-succeeded.png" alt-text="L’application Liberty Java a été déployée avec succès sur AKS":::

>[!NOTE]
> - Actuellement, l’application n’utilise pas le protocole HTTPS. Il est recommandé d’[ACTIVER TLS avec vos propres certificats](ingress-own-tls.md).

## <a name="clean-up-the-resources"></a>Nettoyer les ressources

Pour éviter des frais Azure, vous devez nettoyer les ressources non nécessaires.  Lorsque vous n’avez plus besoin du cluster, utilisez la commande [az group delete](/cli/azure/group#az_group_delete) pour supprimer le groupe de ressources, le service conteneur, le registre de conteneurs et toutes les ressources associées.

```azurecli-interactive
az group delete --name $RESOURCE_GROUP_NAME --yes --no-wait
```

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez en savoir plus sur les références utilisées dans ce guide :

* [Azure Kubernetes Service](https://azure.microsoft.com/free/services/kubernetes-service/)
* [Open Liberty](https://openliberty.io/)
* [Opérateur Open Liberty](https://github.com/OpenLiberty/open-liberty-operator)
* [Configuration du serveur Open Liberty](https://openliberty.io/docs/ref/config/)
* [Plug-in Liberty Maven](https://github.com/OpenLiberty/ci.maven#liberty-maven-plugin)
* [Images conteneur Open Liberty](https://github.com/OpenLiberty/ci.docker)
* [Images conteneur WebSphere Liberty](https://github.com/WASdev/ci.docker)
