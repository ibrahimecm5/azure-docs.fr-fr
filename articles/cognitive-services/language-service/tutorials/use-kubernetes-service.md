---
title: Déployer un conteneur d’extraction d’expressions clés dans le service Azure Kubernetes
titleSuffix: Azure Cognitive Services
description: Déployez une image conteneur d’extraction d’expressions clés dans le service Azure Kubernetes et testez-la dans un navigateur Web.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: ignite-fall-2021
ms.openlocfilehash: a71910bde7718c0a60eda87ea51f6c49e473304a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131097125"
---
# <a name="deploy-a-key-phrase-extraction-container-to-azure-kubernetes-service"></a>Déployer un conteneur d’extraction d’expressions clés dans le service Azure Kubernetes

Découvrez comment déployer une image de [conteneur d’extraction d’expressions clés](../key-phrase-extraction/how-to/use-containers.md) dans Azure Kubernetes service (AKS). Cette procédure indique comment créer une ressource de langue, comment associer une image de conteneur et comment exercer cette orchestration des deux à partir d’un navigateur. L’utilisation de conteneurs peut détourner l’attention des développeurs de la gestion de l’infrastructure, pour les faire se concentrer sur le développement d’applications. Bien que cet article utilise le conteneur d’extraction d’expressions clés comme exemple, vous pouvez utiliser ce processus pour d’autres conteneurs proposés par Azure cognitive service pour le langage

## <a name="prerequisites"></a>Prérequis

Cette procédure nécessite plusieurs outils qui doivent être installés et exécutés localement. N’utilisez pas Azure Cloud Shell. Vous avez besoin des éléments suivants :

* Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/cognitive-services) avant de commencer.
* Un éditeur de code, par exemple [Visual Studio Code](https://code.visualstudio.com/download).
* [Azure CLI](/cli/azure/install-azure-cli) installé.
* The [Kubernetes CLI](https://kubernetes.io/docs/tasks/tools/install-kubectl/) installé.
* Une ressource Azure avec le niveau de tarification approprié. Certains niveaux tarifaires ne fonctionnent pas avec ce conteneur :
    * Ressource de **langage Azure** avec des niveaux tarifaires F0 ou standard uniquement.
    * La ressource **Azure Cognitive Services** avec un niveau tarifaire S0.

[!INCLUDE [Create a Cognitive Services Language resource](../includes/containers/create-text-analytics-resource.md)]

[!INCLUDE [Create a language container on Azure Kubernetes Service (AKS)](../../containers/includes/create-aks-resource.md)]


## <a name="deploy-the-key-phrase-extraction-container-to-an-aks-cluster"></a>Déployer le conteneur Extraction de phrases clés vers un cluster AKS

1. Ouvrir Azure CLI et se connecter à Azure.

    ```azurecli
    az login
    ```

1. Connectez-vous au cluster AKS. Remplacez `your-cluster-name` et `your-resource-group` par les valeurs appropriées.

    ```azurecli
    az aks get-credentials -n your-cluster-name -g -your-resource-group
    ```

    Une fois cette commande exécutée, elle signale un message similaire au message suivant :

    ```output
    Merged "your-cluster-name" as current context in /home/username/.kube/config
    ```

    > [!WARNING]
    > Si plusieurs abonnements sont disponibles sur votre compte Azure et que la commande `az aks get-credentials` renvoie une erreur, le problème peut être que vous n’utilisez pas l’abonnement approprié. Définissez le contexte de votre session Azure CLI pour utiliser le même abonnement avec lequel vous avez créé les ressources, puis réessayez.
    > ```azurecli
    >  az account set -s subscription-id
    > ```

1. Ouvrez l’éditeur de texte de votre choix. L’exemple suivant utilise Visual Studio Code.

    ```console
    code .
    ```

1. Dans l’éditeur de texte, créez un nouveau fichier nommé *keyphrase.yaml* et collez-y le fichier YAML suivant. Assurez-vous de remplacer les valeurs `billing/value` et `apikey/value` par vos propres informations.

    ```yaml
    apiVersion: apps/v1beta1
    kind: Deployment
    metadata:
      name: keyphrase
    spec:
      template:
        metadata:
          labels:
            app: keyphrase-app
        spec:
          containers:
          - name: keyphrase
            image: mcr.microsoft.com/azure-cognitive-services/keyphrase
            ports:
            - containerPort: 5000
            resources:
              requests:
                memory: 2Gi
                cpu: 1
              limits:
                memory: 4Gi
                cpu: 1
            env:
            - name: EULA
              value: "accept"
            - name: billing
              value: # {ENDPOINT_URI}
            - name: apikey
              value: # {API_KEY}
     
    --- 
    apiVersion: v1
    kind: Service
    metadata:
      name: keyphrase
    spec:
      type: LoadBalancer
      ports:
      - port: 5000
      selector:
        app: keyphrase-app
    ```

1. Enregistrez le fichier et fermez l’éditeur de texte.
1. Exécutez la commande Kubernetes `apply` avec le fichier *keyphrase.yaml* comme cible :

    ```console
    kubectl apply -f keyphrase.yaml
    ```

    Une fois la configuration de déploiement correctement appliquée par la commande, un message similaire à la sortie suivante s’affiche :

    ```output
    deployment.apps "keyphrase" created
    service "keyphrase" created
    ```
1. Vérifiez que le pod a été déployé :

    ```console
    kubectl get pods
    ```

    Vous obtiendrez ainsi l’état d’exécution du pod :

    ```output
    NAME                         READY     STATUS    RESTARTS   AGE
    keyphrase-5c9ccdf575-mf6k5   1/1       Running   0          1m
    ```

1. Vérifiez que le service est disponible et obtenez l’adresse IP.

    ```console
    kubectl get services
    ```

    Sortie de l’état d’exécution du service *KeyPhrase* dans le pod :

    ```output
    NAME         TYPE           CLUSTER-IP    EXTERNAL-IP      PORT(S)          AGE
    kubernetes   ClusterIP      10.0.0.1      <none>           443/TCP          2m
    keyphrase    LoadBalancer   10.0.100.64   168.61.156.180   5000:31234/TCP   2m
    ```

## <a name="verify-the-key-phrase-extraction-container-instance"></a>Vérifier l’instance de conteneur Extraction de phrases clés

1. Sélectionnez l’onglet **Vue d’ensemble** et copiez l’adresse IP.
1. Ouvrez un nouvel onglet de navigateur et entrez l’adresse IP. Par exemple, entrez `http://<IP-address>:5000 (http://55.55.55.55:5000`. La page d’accueil du conteneur s’affiche, vous informant que le conteneur est en cours d’exécution.

    ![Affichez la page d’accueil du conteneur pour vérifier qu’elle est en cours d’exécution](../media/swagger-container-documentation.png)

1. Veuillez sélectionner le lien **Description de l’API de service** pour accéder à la page Swagger des conteneurs.

1. Choisissez une des API **POST** et sélectionnez **Essayer**. Les paramètres sont affichés, y compris cet exemple d’entrée :

    ```json
    {
      "documents": [
        {
          "id": "1",
          "text": "Hello world"
        },
        {
          "id": "2",
          "text": "Bonjour tout le monde"
        },
        {
          "id": "3",
          "text": "La carretera estaba atascada. Había mucho tráfico el día de ayer."
        },
        {
          "id": "4",
          "text": ":) :( :D"
        }
      ]
    }
    ```

1. Remplacez l’entrée par le contenu JSON suivant :

    ```json
    {
      "documents": [
        {
          "language": "en",
          "id": "7",
          "text": "I was fortunate to attend the KubeCon Conference in Barcelona, it is one of the best conferences I have ever attended. Great people, great sessions and I thoroughly enjoyed it!"
        }
      ]
    }
    ```

1. Définissez **showStats** sur `true`.

1. Sélectionnez **Exécuter** pour déterminer le sentiment du texte.

    Le modèle empaqueté dans le conteneur génère un score compris entre 0 et 1, où 0 est négatif et 1 est positif.

    La réponse JSON retournée inclut le sentiment pour l’entrée de texte mise à jour :

    ```json
    {
      "documents": [
        {
          "id": "7",
          "keyPhrases": [
            "Great people",
            "great sessions",
            "KubeCon Conference",
            "Barcelona",
            "best conferences"
          ],
          "statistics": {
            "charactersCount": 176,
            "transactionsCount": 1
          }
        }
      ],
      "errors": [],
      "statistics": {
        "documentsCount": 1,
        "validDocumentsCount": 1,
        "erroneousDocumentsCount": 0,
        "transactionsCount": 1
      }
    }
    ```

Nous pouvons maintenant corréler le document `id` des données JSON de la charge utile de réponse avec le document `id` de la charge utile de la requête d’origine. Le document résultant possède un tableau `keyPhrases` qui contient la liste des phrases clés extraites du document d’entrée correspondant. De plus, il existe différentes statistiques, telles que `characterCount` et `transactionCount`, pour chaque document résultant.

## <a name="next-steps"></a>Étapes suivantes

* Utiliser davantage de [conteneurs Cognitive Services](../../cognitive-services-container-support.md)
* [Présentation de l’extraction de phrases clés](../overview.md)
