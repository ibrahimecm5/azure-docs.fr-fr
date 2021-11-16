---
title: Utiliser la connexion de cluster pour vous connecter aux clusters Kubernetes avec Azure Arc
services: azure-arc
ms.service: azure-arc
ms.date: 10/31/2021
ms.topic: article
author: shashankbarsin
ms.author: shasb
description: Utiliser la connexion de cluster pour vous connecter de manière sécurisée aux clusters Kubernetes avec Azure Arc
ms.openlocfilehash: e585b67495aef1fb094d157c584c6744d993b135
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2021
ms.locfileid: "131997910"
---
# <a name="use-cluster-connect-to-connect-to-azure-arc-enabled-kubernetes-clusters"></a>Utiliser la connexion de cluster pour vous connecter aux clusters Kubernetes avec Azure Arc

Avec la connexion de cluster, vous pouvez vous connecter de manière sécurisée aux clusters Kubernetes avec Azure Arc sans avoir besoin d’activer un port entrant sur le pare-feu. L’accès au `apiserver` du cluster Kubernetes avec Azure Arc rend les scénarios suivants possibles :
* Activer le débogage et la résolution des problèmes de manière interactive.
* Permettre aux services Azure d’accéder au cluster pour les [emplacements personnalisés](custom-locations.md) et les autres ressources créées par-dessus.

Une vue d’ensemble conceptuelle de cette fonctionnalité est disponible dans l’article [Connexion de cluster - Kubernetes avec Azure Arc](conceptual-cluster-connect.md).

## <a name="prerequisites"></a>Prérequis   

- [Procédez à l'installation ou à la mise à niveau d'Azure CLI](/cli/azure/install-azure-cli) vers la version >= 2.16.0

- Installez l’extension Azure CLI `connectedk8s` dont la version est >= 1.1.0 :

    ```console
    az extension add --name connectedk8s
    ```
  
    Si vous avez déjà installé l’extension `connectedk8s`, mettez-la à jour vers la dernière version :
    
    ```console
    az extension update --name connectedk8s
    ```

- Cluster Kubernetes avec Azure Arc connecté.
    - Si vous n’avez pas encore connecté de cluster, utilisez notre [Guide de démarrage rapide](quickstart-connect-cluster.md).
    - [Mettez à niveau vos agents](agent-upgrade.md#manually-upgrade-agents) vers une version >= 1.1.0.

- Activez les points de terminaison ci-dessous pour l’accès sortant en plus de ceux mentionnés dans la [connexion d’un cluster Kubernetes à Azure Arc](quickstart-connect-cluster.md#meet-network-requirements) :

    | Point de terminaison | Port |
    |----------------|-------|
    |`*.servicebus.windows.net` | 443 |
    |`guestnotificationservice.azure.com`, `*.guestnotificationservice.azure.com` | 443 |

- Remplacez les espaces réservés et exécutez la commande ci-dessous pour définir les variables d’environnement utilisées dans ce document :

    ```console
    CLUSTER_NAME=<cluster-name>
    RESOURCE_GROUP=<resource-group-name>
    ARM_ID_CLUSTER=$(az connectedk8s show -n $CLUSTER_NAME -g $RESOURCE_GROUP --query id -o tsv)
    ```


## <a name="enable-cluster-connect-feature"></a>Activer la fonctionnalité de connexion de cluster

Vous pouvez activer la connexion de cluster sur un cluster Kubernetes avec Azure Arc en exécutant la commande suivante sur une machine où le fichier `kubeconfig` pointe vers le cluster concerné :

```console
az connectedk8s enable-features --features cluster-connect -n $CLUSTER_NAME -g $RESOURCE_GROUP
```

## <a name="azure-active-directory-authentication-option"></a>Option Authentification Azure Active Directory

1. Récupérez l’élément `objectId` associé à votre entité Azure AD :

    - Pour un compte d’utilisateur Azure AD :

        ```console
        AAD_ENTITY_OBJECT_ID=$(az ad signed-in-user show --query objectId -o tsv)
        ```

    - Pour l’application Azure AD :

        ```console
        AAD_ENTITY_OBJECT_ID=$(az ad sp show --id <id> --query objectId -o tsv)
        ```

1. Autorisez l’entité AAD avec les autorisations appropriées :

    - Si vous utilisez ClusterRoleBinding ou RoleBinding en mode natif dans Kubernetes pour les vérifications d’autorisation sur le cluster, avec le fichier `kubeconfig` pointant vers le `apiserver` de votre cluster pour un accès direct, vous pouvez en créer un mappé à l’entité Azure AD (principal du service ou utilisateur) qui doit accéder à ce cluster. Exemple :
    
        ```console
        kubectl create clusterrolebinding admin-user-binding --clusterrole cluster-admin --user=$AAD_ENTITY_OBJECT_ID
        ```

    - Si vous utilisez Azure RBAC pour les contrôles d’autorisation sur le cluster, vous pouvez créer une attribution de rôle Azure mappée à l’entité Azure AD. Exemple :

        ```console
        az role assignment create --role "Azure Arc Kubernetes Viewer" --assignee $AAD_ENTITY_OBJECT_ID --scope $ARM_ID_CLUSTER
        ```

## <a name="service-account-token-authentication-option"></a>Option d’authentification du jeton du compte de service

1. Faites pointer le fichier `kubeconfig` vers le `apiserver` de votre cluster Kubernetes, créez un compte de service dans un espace de noms (la commande suivante permet de le créer dans l’espace de noms par défaut) :

    ```console
    kubectl create serviceaccount admin-user
    ```

1. Créez ClusterRoleBinding ou RoleBinding afin d’octroyer à ce [compte de service les autorisations appropriées pour le cluster](https://kubernetes.io/docs/reference/access-authn-authz/rbac/#kubectl-create-rolebinding). Exemple :

    ```console
    kubectl create clusterrolebinding admin-user-binding --clusterrole cluster-admin --serviceaccount default:admin-user
    ```

1. Récupérez le jeton du compte de service à l’aide des commandes suivantes

    ```console
    SECRET_NAME=$(kubectl get serviceaccount admin-user -o jsonpath='{$.secrets[0].name}')
    ```

    ```console
    TOKEN=$(kubectl get secret ${SECRET_NAME} -o jsonpath='{$.data.token}' | base64 -d | sed $'s/$/\\\n/g')
    ```

## <a name="access-your-cluster"></a>Accéder à votre cluster

1. Configurez le fichier kubeconfig de connexion de cluster nécessaire pour accéder à votre cluster en fonction de l’option d’authentification utilisée :

    - Si vous utilisez l’option d’authentification Azure Active Directory, après vous être connecté à Azure CLI à l’aide de l’entité Azure AD appropriée, récupérez la connexion de cluster `kubeconfig` nécessaire pour communiquer avec le cluster depuis n’importe quel emplacement (même en dehors du pare-feu entourant le cluster) :

        ```console
        az connectedk8s proxy -n $CLUSTER_NAME -g $RESOURCE_GROUP
        ```

    - Si vous utilisez l’option d’authentification du compte de service, procurez-vous l’élément `kubeconfig` de connexion de cluster pour communiquer avec le cluster depuis n’importe où :

        ```console
        az connectedk8s proxy -n $CLUSTER_NAME -g $RESOURCE_GROUP --token $TOKEN
        ```

1. Utilisez `kubectl` pour envoyer les requêtes au cluster :

    ```console
    kubectl get pods
    ```
    
    Vous devez voir une réponse du cluster contenant la liste de tous les pods présents dans l’espace de noms `default`.

## <a name="known-limitations"></a>Limitations connues

Quand des requêtes sont adressées au cluster Kubernetes, si l’entité Azure AD utilisée fait partie de plus de 200 groupes, l’erreur suivante se produit, car il s’agit d’une limitation connue :

```console
You must be logged in to the server (Error:Error while retrieving group info. Error:Overage claim (users with more than 200 group membership) is currently not supported. 
```

Pour contourner cette erreur :
1. Créez un [principal de service](/cli/azure/create-an-azure-service-principal-azure-cli), qui est moins susceptible d’être membre de plus de 200 groupes.
1. [Connectez-vous](/cli/azure/create-an-azure-service-principal-azure-cli#sign-in-using-a-service-principal) à Azure CLI avec le principal de service avant d’exécuter la commande `az connectedk8s proxy`.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> Configurer [Azure AD RBAC](azure-rbac.md) sur vos clusters
