---
title: Utiliser l’API REST pour gérer les utilisateurs et les rôles dans Azure IoT Central
description: Comment utiliser l’API REST IoT Central pour gérer les utilisateurs et les rôles dans une application
author: dominicbetts
ms.author: dobett
ms.date: 08/30/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: d9892efecf010ea698e9f628cd92308e9ac8608b
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131078500"
---
# <a name="how-to-use-the-iot-central-rest-api-to-manage-users-and-roles"></a>Comment utiliser l’API REST IoT Central pour gérer les utilisateurs et les rôles

L’API REST IoT Central vous permet de développer des applications clientes qui s’intègrent à des applications IoT Central. Vous pouvez utiliser l’API REST pour gérer les utilisateurs et les rôles dans votre application IoT Central.

Chaque appel d’API REST IoT Central nécessite un en-tête d’autorisation. Pour plus d’informations, consultez [Comment authentifier et autoriser les appels d’API REST d’IoT Central](howto-authorize-rest-api.md).

Pour obtenir la documentation de référence sur l’API REST IoT Central, consultez [Informations de référence sur l’API REST d’Azure IoT Central](/rest/api/iotcentral/).

> [!TIP]
> L'[API en préversion](/rest/api/iotcentral/1.1-previewdataplane/users) comprend la prise en charge de la nouvelle [fonctionnalité organisations](howto-create-organizations.md).

## <a name="manage-roles"></a>Gérer les rôles

L’API REST vous permet de répertorier les rôles définis dans votre application IoT Central. Utilisez la requête suivante pour récupérer une liste d’ID de rôle à partir de votre application :

```http
GET https://{your app subdomain}.azureiotcentral.com/api/roles?api-version=1.0
```

La réponse à cette demande ressemble à l’exemple suivant, qui comprend les trois rôles intégrés et un rôle personnalisé :

```json
{
  "value": [
    {
      "id": "ca310b8d-2f4a-44e0-a36e-957c202cd8d4",
      "displayName": "Administrator"
    },
    {
      "id": "ae2c9854-393b-4f97-8c42-479d70ce626e",
      "displayName": "Operator"
    },
    {
      "id": "344138e9-8de4-4497-8c54-5237e96d6aaf",
      "displayName": "Builder"
    },
    {
      "id": "16f8533f-6b82-478f-8ba8-7e676b541b1b",
      "displayName": "Example custom role"
    }
  ]
}
```

## <a name="manage-users"></a>Gestion des utilisateurs

L’API REST vous permet de :

- Répertorier les utilisateurs dans une application
- Récupérer les détails d’un utilisateur individuel
- Créer un utilisateur
- Modifier un utilisateur
- Supprimer un utilisateur

### <a name="list-users"></a>Répertorier les utilisateurs

Utilisez la requête suivante pour récupérer une liste d’utilisateurs à partir de votre application :

```http
GET https://{your app subdomain}.azureiotcentral.com/api/users?api-version=1.0
```

La réponse à cette demande se présente comme dans l’exemple suivant. Les valeurs de rôle identifient l’ID de rôle auquel l’utilisateur est associé :

```json
{
  "value": [
    {
      "id": "91907508-04fe-4349-91b5-b872f3055a95",
      "type": "email",
      "roles": [
        {
          "role": "ca310b8d-2f4a-44e0-a36e-957c202cd8d4"
        }
      ],
      "email": "user1@contoso.com"
    },
    {
      "id": "dc1c916b-a652-49ea-b128-7c465a54c759",
      "type": "email",
      "roles": [
        {
          "role": "ae2c9854-393b-4f97-8c42-479d70ce626e"
        }
      ],
      "email": "user2@contoso.com"
    },
    {
      "id": "3ab9375e-d2d9-42da-b419-6ae86a938321",
      "type": "email",
      "roles": [
        {
          "role": "344138e9-8de4-4497-8c54-5237e96d6aaf"
        }
      ],
      "email": "user3@contoso.com"
    },
    {
      "id": "fc5a250b-83fb-433d-892c-e0a144f68c2b",
      "type": "email",
      "roles": [
        {
          "role": "16f8533f-6b82-478f-8ba8-7e676b541b1b"
        }
      ],
      "email": "user4@contoso.com"
    }
  ]
}
```

### <a name="get-a-user"></a>Obtenir un utilisateur

Utilisez la requête suivante pour récupérer les détails d’un utilisateur individuel à partir de votre application :

```http
GET https://{your app subdomain}.azureiotcentral.com/api/users/dc1c916b-a652-49ea-b128-7c465a54c759?api-version=1.0
```

La réponse à cette demande se présente comme dans l’exemple suivant. La valeur de rôle identifie l’ID de rôle auquel l’utilisateur est associé :

```json
{
  "id": "dc1c916b-a652-49ea-b128-7c465a54c759",
  "type": "email",
  "roles": [
    {
      "role": "ae2c9854-393b-4f97-8c42-479d70ce626e"
    }
  ],
  "email": "user2@contoso.com"
}
```

### <a name="create-a-user"></a>Créer un utilisateur

Utilisez la requête suivante pour créer un utilisateur dans votre application. L’ID et l’e-mail doivent être uniques dans l’application :

```http
PUT https://{your app subdomain}.azureiotcentral.com/api/users/user-001?api-version=1.0
```

Dans le corps de la requête suivante, la valeur `role` est pour le rôle d’opérateur que vous avez récupéré précédemment :

```json
{
  "id": "user-001",
  "type": "email",
  "roles": [
    {
      "role": "ae2c9854-393b-4f97-8c42-479d70ce626e"
    }
  ],
  "email": "user5@contoso.com"
}
```

La réponse à cette demande se présente comme dans l’exemple suivant. La valeur de rôle identifie le rôle auquel l’utilisateur est associé :

```json
{
  "id": "user-001",
  "type": "email",
  "roles": [
    {
      "role": "ae2c9854-393b-4f97-8c42-479d70ce626e"
    }
  ],
  "email": "user5@contoso.com"
}
```

Vous pouvez également ajouter un utilisateur de principal de service. C’est utile si vous devez utiliser l’authentification du principal du service pour les appels de l’API REST. Pour en savoir plus, consultez [Ajouter ou mettre à jour un utilisateur de principal de service](/rest/api/iotcentral/1.0dataplane/users/create#add-or-update-a-service-principal-user).

### <a name="change-the-role-of-a-user"></a>Modifier le rôle d’un utilisateur

Utilisez la demande suivante pour modifier le rôle attribué à l’utilisateur. Cet exemple utilise l’ID du rôle Générateur que vous avez récupéré précédemment :

```http
PATCH https://{your app subdomain}.azureiotcentral.com/api/users/user-001?api-version=1.0
```

Corps de la demande. La valeur est pour le rôle Générateur que vous avez récupéré précédemment :

```json
{
  "roles": [
    {
      "role": "344138e9-8de4-4497-8c54-5237e96d6aaf"
    }
  ]
}
```

La réponse à cette demande ressemble à l’exemple suivant :

```json
{
  "id": "user-001",
  "type": "email",
  "roles": [
    {
      "role": "344138e9-8de4-4497-8c54-5237e96d6aaf"
    }
  ],
  "email": "user5@contoso.com"
}
```

### <a name="delete-a-user"></a>Supprimer un utilisateur

Pour supprimer un utilisateur, utilisez la requête suivante :

```http
DELETE https://{your app subdomain}.azureiotcentral.com/api/users/user-001?api-version=1.0
```

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous savez comment gérer les utilisateurs et les rôles dans l’API REST, nous vous suggérons d’apprendre à [utiliser l’API REST IoT Central pour gérer les exportations de données](howto-manage-data-export-with-rest-api.md).