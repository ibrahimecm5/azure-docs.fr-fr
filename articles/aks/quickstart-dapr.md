---
title: Déployer une application avec l’extension de cluster Dapr (préversion) pour Azure Kubernetes Service (AKS)
description: Utilisez l’extension de cluster Dapr (préversion) pour Azure Kubernetes Service (AKS) pour déployer une application.
author: nickomang
ms.author: nickoman
ms.service: container-service
ms.topic: quickstart
ms.date: 11/01/2021
ms.custom: template-quickstart
ms.openlocfilehash: 1590bcc48260e4b6729cd12cb80b047ba9fe4acb
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131479070"
---
# <a name="quickstart-deploy-an-application-using-the-dapr-cluster-extension-preview-for-azure-kubernetes-service-aks"></a>Démarrage rapide : Déployer une application avec l’extension de cluster Dapr (préversion) pour Azure Kubernetes Service (AKS)

Dans ce démarrage rapide, vous allez vous familiariser avec l’utilisation de l’[extension de cluster Dapr][dapr-overview] dans un cluster AKS. Vous allez déployer un exemple Hello World, constitué d’une application Python qui génère des messages et d’une application Node qui les consomme et les conserve.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, vous pouvez créer un [compte gratuit](https://azure.microsoft.com/free).
* [Azure CLI](/cli/azure/install-azure-cli).
* Un cluster AKS pour lequel l’[extension de cluster Dapr][dapr-overview] est activée.

## <a name="clone-the-repository"></a>Cloner le référentiel

Pour obtenir les fichiers que vous utiliserez pour déployer l’exemple d’application, clonez le [référentiel Quickstarts][hello-world-gh] et passez dans le répertoire `hello-kubernetes` :

```bash
git clone https://github.com/dapr/quickstarts.git
cd quickstarts/hello-kubernetes
```

## <a name="create-and-configure-a-state-store"></a>Créer et configurer un magasin d’état

Dapr peut utiliser un certain nombre de magasins d’état différents (Redis, CosmosDB, DynamoDB, Cassandra, etc.) pour conserver et récupérer l’état. Pour cet exemple, nous utiliserons Redis.

### <a name="create-a-redis-store"></a>Créer un magasin Redis

1. Ouvrez le [portail Azure][azure-portal-cache] pour démarrer le flux de création d’Azure Redis Cache.
2. Renseignez les informations nécessaires.
3. Cliquez sur « Créer » pour lancer le déploiement de votre instance Redis.
4. Notez le nom d’hôte de votre instance Redis, que vous pouvez retrouver dans la section « Vue d’ensemble » dans Azure. Il doit ressembler à `xxxxxx.redis.cache.windows.net:6380`.
5. Une fois que votre instance est créée, vous devez récupérer votre clé d’accès. Accédez à « Clés d’accès » sous « Paramètres » et créez un secret Kubernetes pour stocker votre mot de passe Redis :

```bash
kubectl create secret generic redis --from-literal=redis-password=<your-redis-password>
```

### <a name="configure-the-dapr-components"></a>Configurer les composants Dapr

Une fois votre magasin créé, vous devrez ajouter les clés au fichier redis.yaml dans le répertoire deploy du référentiel Hello World. Remplacez la valeur `redisHost` par votre propre adresse maître Redis, et `redisPassword` par votre propre secret. Vous pouvez en savoir plus [ici][dapr-component-secrets].

Vous devrez également ajouter les deux lignes suivantes sous `redisPassword` pour activer la connexion via TLS :

```yml
- name: redisPassword
    secretKeyRef:
      name: redis
      key: redis-password
- name: enableTLS
  value: true
```

### <a name="apply-the-configuration"></a>Appliquer la configuration

Appliquez le fichier `redis.yaml` :

```bash
kubectl apply -f ./deploy/redis.yaml
``` 

Et vérifiez que votre magasin d’état a été correctement configuré dans la sortie :

```bash
component.dapr.io/statestore created
```

## <a name="deploy-the-nodejs-app-with-the-dapr-sidecar"></a>Déployer l’application Node.js avec le side-car Dapr

Appliquez le déploiement de l’application Node.js à votre cluster :

```bash
kubectl apply -f ./deploy/node.yaml
```

> [!NOTE]
> Les déploiements Kubernetes sont asynchrones. Cela signifie que vous devrez attendre que le déploiement soit terminé avant de passer aux étapes suivantes. Pour ce faire, utilisez la commande suivante :
> ```bash
> kubectl rollout status deploy/nodeapp
> ```

Ceci va déployer l’application Node.js sur Kubernetes. Le plan de contrôle Dapr injectera automatiquement le side-car Dapr dans le pod. Si vous jetez un coup d’œil au fichier `node.yaml`, vous verrez que l’option Dapr est activée pour ce déploiement :

* `dapr.io/enabled: true` : indique au plan de contrôle Dapr d’injecter un side-car dans ce déploiement.

* `dapr.io/app-id: nodeapp` : attribue un ID ou un nom unique à l’application Dapr, afin qu’elle puisse recevoir des messages et communiquer avec d’autres applications Dapr.

Pour accéder à votre service, obtenez et notez le `EXTERNAL-IP` via `kubectl` :

```bash
kubectl get svc nodeapp
```

### <a name="verify-the-service"></a>Vérifier le service

Pour appeler le service, exécutez :

```bash
curl $EXTERNAL_IP/ports
```

Vous devez obtenir une sortie similaire à la suivante :

```bash
{"DAPR_HTTP_PORT":"3500","DAPR_GRPC_PORT":"50001"}
```

Ensuite, envoyez un ordre à l’application :

```bash
curl --request POST --data "@sample.json" --header Content-Type:application/json $EXTERNAL_IP/neworder
```

Confirmez la persistance de l’ordre en le demandant :

```bash
curl $EXTERNAL_IP/order
```

Vous devez obtenir une sortie similaire à la suivante :

```bash
{ "orderId": "42" }
```

> [!TIP]
> C’est le bon moment pour se familiariser avec le tableau de bord de Dapr, une interface pratique pour vérifier l’état, les informations et les journaux des applications s’exécutant sur Dapr. La commande suivante le rendra disponible à l’adresse `http://localhost:8080/` :
> ```bash
> kubectl port-forward svc/dapr-dashboard -n dapr-system 8080:8080
> ```

## <a name="deploy-the-python-app-with-the-dapr-sidecar"></a>Déployer l’application Python avec le side-car Dapr

Jetez un coup d’œil à l’application Python. Accédez au répertoire de l’application Python dans le démarrage rapide `hello-kubernetes` et ouvrez `app.py`.

Il s’agit d’une application Python de base qui publie des messages JSON sur `localhost:3500`, qui est le port d’écoute par défaut de Dapr. Vous pouvez appeler le point de terminaison `neworder` de l’application Node.js en publiant sur `v1.0/invoke/nodeapp/method/neworder`. Le message contient des données avec un `orderId` qui s’incrémente une fois par seconde :

```python
n = 0
while True:
    n += 1
    message = {"data": {"orderId": n}}

    try:
        response = requests.post(dapr_url, json=message)
    except Exception as e:
        print(e)

    time.sleep(1)
```

Déployez l’application Python sur votre cluster Kubernetes :

```bash
kubectl apply -f ./deploy/python.yaml
```

> [!NOTE]
> Comme ci-dessus, la commande suivante attendra que le déploiement soit terminé :
> ```bash
> kubectl rollout status deploy/pythonapp
> ```

## <a name="observe-messages-and-confirm-persistence"></a>Observer les messages et confirmer la persistance

Maintenant que les applications Node.js et Python sont déployées, observez les messages qui arrivent.

Obtenez les journaux de l’application Node.js :

```bash
kubectl logs --selector=app=node -c node --tail=-1
```

Si les déploiements ont réussi, vous devez voir les journaux comme suit :

```bash
Got a new order! Order ID: 1
Successfully persisted state
Got a new order! Order ID: 2
Successfully persisted state
Got a new order! Order ID: 3
Successfully persisted state
```

Appelez le point de terminaison d’ordre de l’application Node.js pour obtenir le dernier ordre. Récupérez l’adresse IP externe que vous avez enregistrée auparavant, ajoutez « /order » et effectuez une requête GET (entrez-la dans votre navigateur, utilisez Postman ou l’interface `curl`) :

```bash
curl $EXTERNAL_IP/order
{"orderID":"42"}
```

Vous devez voir le code JSON le plus récent dans la réponse.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Utilisez la commande [az group delete][az-group-delete] pour supprimer le groupe de ressources, le cluster AKS, l’espace de noms ainsi que toutes les ressources associées.

```azurecli-interactive
az group delete --name MyResourceGroup
```

## <a name="next-steps"></a>Étapes suivantes

Après avoir correctement déployé cet exemple d’application :
> [!div class="nextstepaction"]
> [En savoir plus sur les autres extensions de cluster][cluster-extensions]

<!-- LINKS -->
<!-- INTERNAL -->
[cluster-extensions]: ./cluster-extensions.md
[dapr-overview]: ./dapr.md
[az-group-delete]: /cli/azure/group#az_group_delete

<!-- EXTERNAL -->
[hello-world-gh]: https://github.com/dapr/quickstarts/tree/v1.4.0/hello-kubernetes
[azure-portal-cache]: https://ms.portal.azure.com/#create/Microsoft.Cache
[dapr-component-secrets]: https://docs.dapr.io/operations/components/component-secrets/