---
title: Utiliser l’API REST pour gérer et surveiller des applications IoT Central | Microsoft Docs
description: Cet article explique comment créer et gérer vos applications IoT Central avec l’API REST. L’API REST vous permet également d’ajouter une identité managée affectée par le système à votre application.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 10/25/2021
ms.topic: how-to
ms.openlocfilehash: a4df2346346d048bcd4e01e898636c1668720c4c
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131098149"
---
# <a name="use-the-rest-api-to-create-and-manage-iot-central-applications"></a>Utiliser l’API REST pour créer et gérer des applications IoT Central

Vous pouvez utiliser l’[API REST du plan de contrôle](/rest/api/iotcentral/2021-06-01controlplane/apps) pour créer et gérer des applications IoT Central. Vous pouvez également utiliser l’API REST pour ajouter une identité managée à votre application.

Pour utiliser cette API, vous avez besoin d’un jeton du porteur pour la ressource `management.azure.com`. Pour obtenir un jeton du porteur, vous pouvez utiliser Azure CLI :

```azurecli
az account get-access-token --resource https://management.azure.com
```

## <a name="list-your-applications"></a>Répertorier vos applications

Pour obtenir la liste des applications IoT Central dans un abonnement :

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.IoTCentral/iotApps?api-version=2021-06-01
```

Pour obtenir la liste des applications IoT Central dans un groupe de ressources :

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.IoTCentral/iotApps?api-version=2021-06-01
```

Vous pouvez récupérer les détails d’une application individuelle :

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.IoTCentral/iotApps/{applicationName}?api-version=2021-06-01
```

## <a name="create-an-iot-central-application"></a>Créer une application IoT Central

Pour créer une application IoT Central avec une [identité managée](../../active-directory/managed-identities-azure-resources/overview.md) affectée par le système :

```http
PUT https://management.azure.com/subscriptions/<your subscription id>/resourceGroups/<your resource group name>/providers/Microsoft.IoTCentral/iotApps/<your application name>?api-version=2021-06-01
```

La charge utile suivante illustre la configuration de la nouvelle application, y compris l’identité managée :

```json
{
  "location": "eastus",
  "sku": {
    "name": "ST2"
  },
  "properties": {
    "displayName": "Contoso IoT Central App",
    "subdomain": "my-iot-central-app",
    "template": "iotc-pnp-preview@1.0.0"
  },
  "identity": {
    "type": "SystemAssigned"
  }
}
```

## <a name="modify-an-iot-central-application"></a>Modifier une application IoT Central

Vous pouvez modifier une application IoT Central existante. L’exemple suivant montre comment modifier le nom d’affichage et activer l’identité managée affectée par le système :

```http
PATCH https://management.azure.com/subscriptions/<your subscription id>/resourceGroups/<your resource group name>/providers/Microsoft.IoTCentral/iotApps/<your application name>?api-version=2021-06-01
```

Utilisez la charge utile suivante pour modifier le nom d’affichage et activer l’identité managée affectée par le système :

```json
{
  "properties": {
    "displayName": "Contoso IoT Central App"
  },
  "identity": {
    "type": "SystemAssigned"
  }
}
```

> [!NOTE]
> Vous pouvez uniquement ajouter une identité managée à une application IoT Central qui a été créée dans une région. Toutes les nouvelles applications sont créées dans une région. Pour en savoir plus, consultez [Mises à jour](https://azure.microsoft.com/updates/azure-iot-central-new-and-updated-features-august-2021/).

## <a name="delete-an-iot-central-application"></a>Supprimer une application IoT Central

Pour supprimer une application IoT Central, utilisez :

```http
DELETE https://management.azure.com/subscriptions/<your subscription id>/resourceGroups/<your resource group name>/providers/Microsoft.IoTCentral/iotApps/<your application name>?api-version=2021-06-01
```

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris à créer et à gérer des applications Azure IoT Central à l’aide de l’API REST, voici l’étape suivante suggérée :

> [!div class="nextstepaction"]
> [Comment utiliser l’API REST IoT Central pour gérer les utilisateurs et les rôles](howto-manage-users-roles-with-rest-api.md)