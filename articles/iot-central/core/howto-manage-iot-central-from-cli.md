---
title: Gérer IoT Central à partir d’Azure CLI ou PowerShell | Microsoft Docs
description: Cet article explique comment créer et gérer une application IoT Central avec Azure CLI ou PowerShell. Vous pouvez afficher, modifier et supprimer l’application à l’aide de ces outils. Vous pouvez également configurer une identité système managée que vous pouvez utiliser pour configurer l’exportation de données sécurisée.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 07/06/2021
ms.topic: how-to
ms.custom:
- devx-track-azurecli
- devx-track-azurepowershell
ms.openlocfilehash: 979da680c3a5a9b70973fa7da00613f7ffbcf86b
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131088068"
---
# <a name="manage-iot-central-from-azure-cli-or-powershell"></a>Gérer IoT Central à partir d’Azure CLI ou PowerShell

Au lieu de créer et de gérer des applications IoT Central dans le site web du [Gestionnaire d’applications Azure IoT Central](https://aka.ms/iotcentral), vous pouvez utiliser [Azure CLI](/cli/azure/) ou [Azure PowerShell](/powershell/azure/) pour gérer vos applications.

Si vous préférez utiliser un langage tel que JavaScript, Python, C#, Ruby ou Go, reportez-vous au référentiel [Exemples SDK ARM Azure IoT Central](/samples/azure-samples/azure-iot-central-arm-sdk-samples/azure-iot-central-arm-sdk-samples/) pour les exemples de code qui montrent comment créer, mettre à jour, lister et supprimer les applications Azure IoT Central.

## <a name="prerequisites"></a>Prérequis

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[!INCLUDE [azure-cli-prepare-your-environment-no-header](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[!INCLUDE [azure-powershell-requirements-no-header](../../../includes/azure-powershell-requirements-no-header.md)]

> [!TIP]
> Si vous devez exécuter vos commandes PowerShell dans un autre abonnement Azure, consultez [Modifier l’abonnement actif](/powershell/azure/manage-subscriptions-azureps#change-the-active-subscription).

Exécutez la commande suivante pour vérifier que le [module IoT Central](/powershell/module/az.iotcentral/) est bien installé dans votre environnement PowerShell :

```powershell
Get-InstalledModule -name Az.I*
```

Si la liste des modules installés n’inclut pas **Az.IotCentral**, exécutez la commande suivante :

```powershell
Install-Module Az.IotCentral
```

---

[!INCLUDE [Warning About Access Required](../../../includes/iot-central-warning-contribitorrequireaccess.md)]

## <a name="create-an-application"></a>Créer une application

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Utilisez la commande [az iot central app create](/cli/azure/iot/central/app#az_iot_central_app_create) pour créer une application IoT Central dans votre abonnement Azure. Par exemple :

```azurecli-interactive
# Create a resource group for the IoT Central application
az group create --location "East US" \
    --name "MyIoTCentralResourceGroup"
```

```azurecli-interactive
# Create an IoT Central application
az iot central app create \
  --resource-group "MyIoTCentralResourceGroup" \
  --name "myiotcentralapp" --subdomain "mysubdomain" \
  --sku ST1 --template "iotc-pnp-preview" \
  --display-name "My Custom Display Name"
```

Ces commandes créent d’abord un groupe de ressources dans la région USA Est pour l’application. Le tableau suivant décrit les paramètres utilisés avec la commande **az iot central app create** :

| Paramètre         | Description |
| ----------------- | ----------- |
| resource-group    | Groupe de ressources qui contient l’application. Ce groupe de ressources doit déjà exister dans votre abonnement. |
| location          | Par défaut, cette commande utilise l’emplacement du groupe de ressources. Actuellement, vous pouvez créer une application IoT Central dans les régions : **Australie Est**, **USA Centre**, **USA Est**, **USA Est 2**, **Japon Est**, **Europe Nord**, **Asie Sud-Est**, **Royaume-Uni Sud**, **Europe Ouest** et **USA Ouest**. |
| name              | Nom de l’application dans le portail Azure. Évitez les caractères spéciaux. Utilisez plutôt des lettres minuscules (a-z), des chiffres (0-9) et des tirets (-).|
| subdomain         | Sous-domaine dans l’URL de l’application. Dans l’exemple, l’URL de l’application est `https://mysubdomain.azureiotcentral.com`. |
| sku               | Vous pouvez utiliser **ST1** ou **ST2**. Consultez [Tarifs Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/). |
| template          | Modèle d’application à utiliser. Pour plus d’informations, consultez le tableau suivant. |
| display-name      | Nom de l’application tel qu’il est affiché dans l’interface utilisateur. |

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Utilisez l’applet de commande [New-AzIotCentralApp](/powershell/module/az.iotcentral/New-AzIotCentralApp) pour créer une application IoT Central dans votre abonnement Azure. Par exemple :

```powershell
# Create a resource group for the IoT Central application
New-AzResourceGroup -ResourceGroupName "MyIoTCentralResourceGroup" `
  -Location "East US"
```

```powershell
# Create an IoT Central application
New-AzIotCentralApp -ResourceGroupName "MyIoTCentralResourceGroup" `
  -Name "myiotcentralapp" -Subdomain "mysubdomain" `
  -Sku "ST1" -Template "iotc-pnp-preview" `
  -DisplayName "My Custom Display Name"
```

Le script crée d’abord un groupe de ressources dans la région USA Est pour l’application. Le tableau suivant décrit les paramètres utilisés avec la commande **New-AzIotCentralApp** :

|Paramètre         |Description |
|------------------|------------|
|ResourceGroupName |Groupe de ressources qui contient l’application. Ce groupe de ressources doit déjà exister dans votre abonnement. |
|Emplacement |Par défaut, cette applet de commande utilise l’emplacement du groupe de ressources. Actuellement, vous pouvez créer une application IoT Central dans les régions : **Australie Est**, **USA Centre**, **USA Est**, **USA Est 2**, **Japon Est**, **Europe Nord**, **Asie Sud-Est**, **Royaume-Uni Sud**, **Europe Ouest** et **USA Ouest**. |
|Nom              |Nom de l’application dans le portail Azure. Évitez les caractères spéciaux. Utilisez plutôt des lettres minuscules (a-z), des chiffres (0-9) et des tirets (-). |
|Sous-domaine         |Sous-domaine dans l’URL de l’application. Dans l’exemple, l’URL de l’application est `https://mysubdomain.azureiotcentral.com`. |
|Sku               |Vous pouvez utiliser **ST1** ou **ST2**. Consultez [Tarifs Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/). |
|Modèle          | Modèle d’application à utiliser. Pour plus d’informations, consultez le tableau suivant. |
|DisplayName       |Nom de l’application tel qu’il est affiché dans l’interface utilisateur. |

---

### <a name="application-templates"></a>Modèles d’application

[!INCLUDE [iot-central-template-list](../../../includes/iot-central-template-list.md)]

Si vous avez créé votre propre modèle d’application, vous pouvez l’utiliser pour créer une nouvelle application. Quand vous êtes invité à spécifier un modèle d’application, entrez l’ID d’application indiqué dans le lien partageable d’URL de l’application exportée sous la section [Exportation de modèle d’application](howto-create-iot-central-application.md#create-and-use-a-custom-application-template) de votre application.

## <a name="view-applications"></a>Afficher les applications

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Utilisez la commande [az iot central app list](/cli/azure/iot/central/app#az_iot_central_app_list) pour lister vos applications IoT Central et afficher les métadonnées.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Utilisez l’applet de commande [Get-AzIotCentralApp](/powershell/module/az.iotcentral/Get-AzIotCentralApp) pour lister vos applications IoT Central et afficher les métadonnées.

---

## <a name="modify-an-application"></a>Modifier une application

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Utilisez la commande [az iot central app update](/cli/azure/iot/central/app#az_iot_central_app_update) pour mettre à jour les métadonnées d’une application IoT Central. Par exemple, pour modifier le nom complet de votre application :

```azurecli-interactive
az iot central app update --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup \
  --set displayName="My new display name"
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Utilisez l’applet de commande [Set-AzIotCentralApp](/powershell/module/az.iotcentral/set-aziotcentralapp) pour mettre à jour les métadonnées d’une application IoT Central. Par exemple, pour modifier le nom complet de votre application :

```powershell
Set-AzIotCentralApp -Name "myiotcentralapp" `
  -ResourceGroupName "MyIoTCentralResourceGroup" `
  -DisplayName "My new display name"
```

---

## <a name="delete-an-application"></a>Supprimer une application

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Utilisez la commande [az iot central app delete](/cli/azure/iot/central/app#az_iot_central_app_delete) pour supprimer une application IoT Central. Par exemple :

```azurecli-interactive
az iot central app delete --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Utilisez l’applet de commande [Remove-AzIotCentralApp](/powershell/module/az.iotcentral/Remove-AzIotCentralApp) pour supprimer les métadonnées d’une application IoT Central. Par exemple :

```powershell
Remove-AzIotCentralApp -ResourceGroupName "MyIoTCentralResourceGroup" `
 -Name "myiotcentralapp"
```

---

## <a name="configure-a-managed-identity"></a>Configurer une identité managée

Une application IoT Central peut utiliser une [identité managée](../../active-directory/managed-identities-azure-resources/overview.md) affectée par le système pour sécuriser la connexion à une [destination d’exportation de données](howto-export-data.md#connection-options).

Pour activer l’identité managée, utilisez le [portail Azure : Configurer une identité managée](howto-manage-iot-central-from-portal.md#configure-a-managed-identity) ou l’[API REST](howto-manage-iot-central-with-rest-api.md) :

:::image type="content" source="media/howto-manage-iot-central-from-cli/managed-identity.png" alt-text="Capture d’écran montrant l’identité managée dans Portail Azure.":::

Après avoir activé l’identité managée, vous pouvez utiliser l’interface CLI pour configurer les attributions de rôle.

Pour créer une attribution de rôle, utilisez la commande [az role assignment create](/cli/azure/role/assignment#az_role_assignment_create). Par exemple, les commandes suivantes récupère d’abord l’ID du principal de l’identité managée. La deuxième commande attribue le rôle `Azure Event Hubs Data Sender` à l’ID du principal dans l’étendue du groupe de ressources `MyIoTCentralResourceGroup` :

```azurecli-interactive
spID=$(az resource list -n myiotcentralapp --query [*].identity.principalId --out tsv)
az role assignment create --assignee $spID --role "Azure Event Hubs Data Sender" \
  --scope /subscriptions/<your subscription id>/resourceGroups/MyIoTCentralResourceGroup
```

Pour en savoir plus sur les attributions de rôle, consultez :

- [Rôles intégrés pour Azure Event Hubs](../../event-hubs/authenticate-application.md#built-in-roles-for-azure-event-hubs)
- [Rôles Azure intégrés pour Service Bus](../../service-bus-messaging/authenticate-application.md#azure-built-in-roles-for-azure-service-bus)
- [Rôles intégrés pour les services Stockage Azure](/rest/api/storageservices/authorize-with-azure-active-directory#manage-access-rights-with-rbac)

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris à gérer des applications Azure IoT Central dans Azure CLI ou PowerShell, voici l’étape suivante suggérée :

> [!div class="nextstepaction"]
> [Administrer votre application](howto-administer.md)
