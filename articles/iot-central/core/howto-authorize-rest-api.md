---
title: Autoriser l’API REST dans Azure IoT Central
description: Comment authentifier et autoriser les appels d’API REST IoT Central
author: dominicbetts
ms.author: dobett
ms.date: 08/25/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 28efc06f2d7c3b5a9fca8d96dda7fa885a98f088
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131070334"
---
# <a name="how-to-authenticate-and-authorize-iot-central-rest-api-calls"></a>Comment authentifier et autoriser les appels d’API REST IoT Central

L’API REST IoT Central vous permet de développer des applications clientes qui s’intègrent aux applications IoT Central. Utilisez l’API REST pour utiliser les ressources de votre application IoT Central, telles que les modèles d’appareils, les appareils, les travaux, les utilisateurs et les rôles.

Chaque appel d’API REST IoT Central nécessite un en-tête d’autorisation dont se sert IoT Central pour déterminer l’identité de l’appelant et les autorisations qui lui ont été accordées dans l’application.

Cet article décrit les types de jetons que vous pouvez utiliser dans l’en-tête d’autorisation et la façon de les obtenir.

## <a name="token-types"></a>Types de jetons

Vous pouvez utiliser le jeton du porteur de l’utilisateur lorsque vous effectuez des appels d’automatisation/test/API vous-même. Vous pouvez utiliser le jeton du porteur du SPN lors de l’automatisation/création de scripts pour votre environnement de développement (par ex. devops). Le jeton d’API peut être utilisé dans les deux cas, mais il risque d’arriver à expiration et à fuire, donc nous vous recommandons d’utiliser le porteur chaque fois que cela est possible. Cela a-t-il un sens ? 

Pour accéder à une application IoT Central à l’aide de l’API REST, vous pouvez utiliser :

- _Un jeton du porteur Azure Active Directory_. Un jeton du porteur est associé à un compte d’utilisateur Azure Active Directory ou un principal de service. Le jeton accorde à l’appelant les mêmes autorisations que celles dont l’utilisateur ou le principal de service dispose dans l’application IoT Central.
- Un jeton d’API IoT Central. Un jeton d’API est associé à un rôle dans votre application IoT Central.

Pour plus d’informations sur les utilisateurs et les rôles dans IoT Central, consultez [Gérer les utilisateurs et rôles dans votre application IOT Central](howto-manage-users-roles.md).

## <a name="get-a-bearer-token"></a>Obtenir un jeton du porteur

Pour obtenir un jeton du porteur pour votre compte d’utilisateur Azure Active Directory, utilisez les commandes Azure CLI suivantes :

```azurecli
az login
az account get-access-token --resource https://apps.azureiotcentral.com
```

> [!IMPORTANT]
> La commande `az login` est nécessaire même si vous utilisez Cloud Shell.

La sortie JSON de la commande précédente ressemble à l’exemple suivant :

```json
{
  "accessToken": "eyJ0eX...fNQ",
  "expiresOn": "2021-03-22 11:11:16.072222",
  "subscription": "{your subscription id}",
  "tenant": "{your tenant id}",
  "tokenType": "Bearer"
}
```

Le jeton du porteur est valide pendant environ une heure. Après quoi, vous devez en créer un nouveau.

Pour obtenir un jeton du porteur pour un principal du service, consultez [Authentification du principal du service](/rest/api/iotcentral/authentication#service-principal-authentication).

## <a name="get-an-api-token"></a>Obtenir un jeton d’API

Pour obtenir un jeton d’API, vous pouvez utiliser l’interface utilisateur IoT Central ou un appel d’API REST. Les administrateurs associés à l’organisation racine et les utilisateurs affectés au rôle correct peuvent créer des jetons d’API.

Dans l’interface utilisateur IoT Central :

1. Accédez à **Administration > Jetons d’API**.
1. Sélectionnez **+ Créer le jeton**.
1. Entrez un nom pour le jeton et sélectionnez un rôle et une [organisation](howto-create-organizations.md).
1. Sélectionnez **Générer**.
1. IoT Central affiche le jeton qui ressemble à l’exemple suivant :

    `SharedAccessSignature sr=5782ed70...&sig=dvZZE...&skn=operator-token&se=1647948035850`

    C’est la seule fois que le jeton d’API s’affiche à l’écran. Si vous le perdez, vous devez en générer un nouveau.

Un jeton d’API est valide pendant environ un an. Vous pouvez en générer pour les rôles intégrés et personnalisés dans votre application IoT Central. L’organisation que vous choisissez lors de la création du jeton d’API détermine à quels appareils l’API a accès. Tous les jetons d’API créés avant l’ajout d’organisations à votre application sont associés à l’organisation racine.

Si vous avez besoin de révoquer un accès, vous pouvez supprimer des jetons d’API dans l’interface utilisateur IoT Central.

Utilisation de l’API REST :

1. Utilisez l’API REST pour récupérer la liste d’ID de rôle de votre application :

    ```http
    GET https://{your app subdomain}.azureiotcentral.com/api/roles?api-version=1.0
    ```

    La réponse à cette demande ressemble à l’exemple suivant :

    ```json
    {
      "value": [
        {
          "displayName": "Administrator",
          "id": "ca310b8d-2f4a-44e0-a36e-957c202cd8d4"
        },
        {
          "displayName": "Operator",
          "id": "ae2c9854-393b-4f97-8c42-479d70ce626e"
        },
        {
          "displayName": "Builder",
          "id": "344138e9-8de4-4497-8c54-5237e96d6aaf"
        }
      ]
    }
    ```

1. Utilisez l’API REST pour créer un jeton d’API pour un rôle. Par exemple, pour créer un jeton d’API appelé `operator-token` pour le rôle d’opérateur :

    ```http
    PUT https://{your app subdomain}.azureiotcentral.com/api/roles/operator-token?api-version=1.0
    ```

    Corps de la requête :

    ```json
    {
      "roles": [
        {
          "role": "ae2c9854-393b-4f97-8c42-479d70ce626e"
        }
      ]
    }
    ```

    La réponse à la commande précédente ressemble à la sortie JSON suivante :

    ```json
    {
      "expiry": "2022-03-22T12:01:27.889Z",
      "id": "operator-token",
      "roles": [
        {
          "role": "ae2c9854-393b-4f97-8c42-479d70ce626e"
        }
      ],
      "token": "SharedAccessSignature sr=e8a...&sig=jKY8W...&skn=operator-token&se=1647950487889"
    }
    ```

    Cette réponse est le seul moment où vous avez accès au jeton d’API. Si vous le perdez, vous devez en générer un nouveau.

Vous pouvez utiliser l’API REST pour lister et supprimer les jetons d’API d’une application.

> [!TIP]
> L'[API en préversion](/rest/api/iotcentral/1.1-previewdataplane/api-tokens) comprend la prise en charge de la nouvelle [fonctionnalité organisations](howto-create-organizations.md).

## <a name="use-a-bearer-token"></a>Utiliser un jeton du porteur

Pour utiliser un jeton du porteur au moment d’effectuer un appel d’API REST, votre en-tête d’autorisation ressemble à l’exemple suivant :

`Authorization: Bearer eyJ0eX...fNQ`

## <a name="use-an-api-token"></a>Utiliser un jeton d’API

Pour utiliser un jeton d’API au moment d’effectuer un appel d’API REST, votre en-tête d’autorisation ressemble à l’exemple suivant :

`Authorization: SharedAccessSignature sr=e8a...&sig=jKY8W...&skn=operator-token&se=1647950487889`

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous savez comment autoriser les appels d’API REST, nous vous suggérons à présent d’apprendre à [utiliser l’API REST IoT Central pour interroger des requêtes](howto-query-with-rest-api.md).
