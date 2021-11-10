---
title: Conteneurs dans la préversion d’Azure Container Apps
description: Découvrez comment les conteneurs sont gérés et configurés dans Azure Container Apps.
services: container-apps
author: craigshoemaker
ms.service: container-apps
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: cshoe
ms.custom: ignite-fall-2021
ms.openlocfilehash: fdebbbae4cba1577b8f92e0f54821eb72737c2c3
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2021
ms.locfileid: "132026764"
---
# <a name="containers-in-azure-container-apps-preview"></a>Conteneurs dans la préversion d’Azure Container Apps

Azure Container Apps gère automatiquement les détails de Kubernetes et les orchestrations de conteneurs. Dans Azure Container Apps, les conteneurs peuvent utiliser le runtime, le langage de programmation et la pile de développement de votre choix.

:::image type="content" source="media/containers/azure-container-apps-containers.png" alt-text="Azure Container Apps : conteneurs":::

Azure Container Apps prend en charge les éléments suivants :

- N’importe quelle image conteneur Linux
- Les conteneurs de n’importe quel registre de conteneurs public ou privé

Les fonctionnalités supplémentaires sont les suivantes :

- Aucune image conteneur de base n’est requise.
- Les modifications apportées à la section de configuration ARM `template` déclenchent une nouvelle [révision de l’application conteneur](application-lifecycle-management.md).
- En cas d’incident, les conteneurs redémarrent automatiquement.

## <a name="configuration"></a>Configuration

L’exemple de configuration suivant présente les options disponibles lors de la configuration d’un conteneur.

```json
{
  ...
  "template": {
    "containers": [
      {
        "image": "myacr.azurecr.io/myrepo/api-service:v1",
        "name": "my-container-image",
        "command": ["/bin/queue"],
        "args": [],
        "env": [
          {
            "name": "HTTP_PORT",
            "value": "8080"
          }
        ],
        "resources": {
            "cpu": 1,
            "memory": "250Mb"
        }
    }]
  }
}
```

| Paramètre | Description | Notes |
|---|---|---|
| `image` | Nom de l’image conteneur de votre application conteneur. | Cette valeur prend la forme `repository/image-name:tag`. |
| `name` | Nom convivial du conteneur. | Utilisé pour la création de rapports et l’identification. |
| `command` | Commande de démarrage du conteneur. | Équivalente au champ [entrypoint](https://docs.docker.com/engine/reference/builder/) de Docker.  |
| `args` | Arguments de la commande de démarrage. | Les entrées du tableau sont jointes pour créer une liste de paramètres à transmettre à la commande de démarrage. |
| `env` | Tableau de paires clé/valeur qui définissent des variables d’environnement. | Utilisez `secretRef` à la place du champ `value` pour faire référence à un secret. |
| `resources.cpu` | Nombre de processeurs alloués au conteneur. | Les valeurs doivent respecter les règles suivantes : nombre décimal compris entre 0 et 2, avec au maximum une décimale. Par exemple, `1.1` est valide, `1.55` non. La valeur par défaut est de 0,5 processeur par conteneur. |
| `resources.memory` | Quantité de RAM allouée au conteneur. | Cette valeur peut atteindre `4Gi` au maximum. La seule unité autorisée est le [gibioctet](https://simple.wikipedia.org/wiki/Gibibyte) (`Gi`). Les valeurs doivent respecter les règles suivantes : nombre décimal compris entre 0 et `4Gi`, avec au maximum deux décimales. Par exemple, `1.25Gi` est valide, `1.555Gi` non. La valeur par défaut est de `1Gi` par conteneur.  |

La quantité totale de processeurs et de mémoire demandée pour tous les conteneurs d’une application de conteneur doit correspondre à l’une des combinaisons suivantes.

| Processeurs virtuels | Mémoire en Gio |
|---|---|
| 0.5 | 1.0 |
| 1.0 | 2,0 |
| 1.5 | 3,0 |
| 2,0 | 4,0 |

- Toutes les demandes de processeur de tous les conteneurs doivent correspondre à l’une des valeurs de la colonne Processeurs virtuels.
- Toutes les demandes de mémoire de tous les conteneurs doivent correspondre à la valeur de mémoire de la colonne Mémoire sur la même ligne que la colonne Processeur.

## <a name="multiple-containers"></a>Plusieurs conteneurs

Il est possible de définir plusieurs conteneurs dans une même application de conteneur. Les groupes de conteneurs sont appelés [pods](https://kubernetes.io/docs/concepts/workloads/pods). Les conteneurs d’un pod se partagent les ressources disque dur et réseau et connaissent le même [cycle de vie de l’application](application-lifecycle-management.md).

Pour exécuter plusieurs conteneurs ensemble, définissez-les dans le tableau `containers` de la configuration.

Voici quelques raisons d’exécuter des conteneurs ensemble dans un pod :

- Utiliser un conteneur comme annexe de l’application principale
- Utiliser un espace disque et un réseau virtuel partagés
- Partager les règles de mise à l’échelle entre les conteneurs
- Regrouper plusieurs conteneurs qui doivent toujours s’exécuter ensemble
- Permettre la communication directe entre les conteneurs situés sur le même hôte

## <a name="container-registries"></a>Registres de conteneurs

Vous pouvez déployer des images hébergées sur des registres privés dans lesquels les informations d’identification sont fournies par le biais de la configuration Container Apps.

Pour pouvoir utiliser un registre de conteneurs, vous devez d’abord définir les champs requis dans la section `registries` de la [configuration](azure-resource-manager-api-spec.md).

```json
{
  ...
  "registries": {
    "server": "docker.io",
    "username": "my-registry-user-name",
    "passwordSecretRef": "my-password-secretref-name"
  }
}
```

Avec cette configuration, les informations d’identification enregistrées peuvent être utilisées pour faire référence à une image conteneur dans une `image` dans le tableau `containers`.

L’exemple suivant montre comment déployer une application à partir d’Azure Container Registry.

```json
{
  ...
  "configuration": {
      "secrets": [
          {
              "name": "acr-password",
              "value": "my-acr-password"
          }
      ],
      "registries": [
          {
              "server": "myacr.azurecr.io",
              "username": "someuser",
              "passwordSecretRef": "acr-password"
          }
      ]
  }
}
```

## <a name="limitations"></a>Limitations

Azure Container Apps présente les limitations suivantes :

- **Conteneurs privilégiés** : les applications Azure Container ne peuvent pas exécuter des conteneurs privilégiés. Si votre programme tente d’exécuter un processus exigeant un accès racine, l’application située à l’intérieur du conteneur rencontre une erreur d’exécution.

- **Système d’exploitation** : des images conteneur Linux sont requises.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Environment](environment.md)
