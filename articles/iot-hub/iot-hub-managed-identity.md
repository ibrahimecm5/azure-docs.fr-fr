---
title: identité managée Azure IoT Hub | Microsoft Docs
description: Comment utiliser des identités managées pour autoriser la connectivité de sortie de votre hub IoT vers d’autres ressources Azure.
author: miag
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 09/02/2021
ms.author: miag
ms.openlocfilehash: b6f050c99d57e077e9e60aeb70d0acb11d200adb
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131072723"
---
# <a name="iot-hub-support-for-managed-identities"></a>Prise en charge des identités managées par IoT Hub 

Les identités managées fournissent aux services Azure une identité managée automatiquement dans Azure AD, de manière sécurisée. Ainsi, les développeurs n’ont plus à gérer les informations d’identification en fournissant une identité. Il existe deux types d’identités managées : celles affectées par le système et celles affectées par l’utilisateur. IoT Hub prend en charge les deux. 

Dans IoT Hub, les identités managées peuvent être utilisées pour la connectivité de sortie de IoT Hub à d’autres services Azure pour des fonctionnalités telles que le [routage des messages](iot-hub-devguide-messages-d2c.md), le [chargement de fichiers](iot-hub-devguide-file-upload.md) et l’[importation/exportation d’appareils en bloc](iot-hub-bulk-identity-mgmt.md). Dans cet article, vous allez apprendre à utiliser des identités managées affectées par le système et affectées par l’utilisateur dans votre hub IoT pour différentes fonctionnalités.

## <a name="prerequisites"></a>Prérequis

- Lisez la documentation des [identités managées pour les ressources Azure](./../active-directory/managed-identities-azure-resources/overview.md) pour comprendre les différences entre les identités managées affectées par le système et celles affectées par l’utilisateur.

- Si vous n’avez pas de hub IoT, [créez-en un](iot-hub-create-through-portal.md) avant de continuer.

## <a name="system-assigned-managed-identity"></a>Identité managée affectée par le système

### <a name="add-and-remove-a-system-assigned-managed-identity-in-azure-portal"></a>Ajouter et supprimer une identité managée affectée par le système dans Portail Azure

1. Connectez-vous au portail Azure et accédez au hub IoT de votre choix.
2. Accédez à **Identité** sur votre portail IoT Hub.
3. Sous l’onglet **Affectée par le système**, sélectionnez **Activé**, puis cliquez sur **Enregistrer**.
4. Pour supprimer l’identité managée affectée par le système d’un hub IoT, sélectionnez **Désactivé**, puis cliquez sur **Enregistrer**.

    :::image type="content" source="./media/iot-hub-managed-identity/system-assigned.png" alt-text="Capture d’écran montrant où activer l’identité managée affectée par le système pour un hub IoT.":::

### <a name="enable-system-assigned-managed-identity-at-hub-creation-time-using-arm-template"></a>Activer l’identité managée affectée par le système au moment de la création du hub en utilisant un modèle ARM

Pour activer l’identité managée affectée par le système dans votre hub IoT au moment de l’approvisionnement des ressources, utilisez le modèle Azure Resource Manager (ARM) ci-dessous. 

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": 
    {
      "iotHubName": {
        "type": "string",
        "metadata": {
          "description": "Name of iothub resource"
        }
      },
      "skuName": {
        "type": "string",
        "defaultValue": "S1",
        "metadata": {
          "description": "SKU name of iothub resource, by default is Standard S1"
        }
      },
      "skuTier": {
        "type": "string",
        "defaultValue": "Standard",
        "metadata": {
          "description": "SKU tier of iothub resource, by default is Standard"
        }
      },
      "location": {
        "type": "string",
        "defaultValue": "[resourceGroup().location]",
        "metadata": {
          "description": "Location of iothub resource. Please provide any of supported-regions of iothub"
        }
      }
    },
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-10-01",
      "name": "createIotHub",
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "resources": [
            {
              "type": "Microsoft.Devices/IotHubs",
              "apiVersion": "2021-03-31",
              "name": "[parameters('iotHubName')]",
              "location": "[parameters('location')]",
              "identity": {
                "type": "SystemAssigned"
              },
              "sku": {
              "name": "[parameters('skuName')]",
              "tier": "[parameters('skuTier')]",
              "capacity": 1
              }
            }
          ] 
        }
      }
    }
  ]
}
```

Après avoir substitué les valeurs de votre ressource `name`, `location`, `SKU.name` et `SKU.tier`, vous pouvez utiliser l’interface de ligne de commande Azure pour déployer la ressource dans un groupe de ressources existant en utilisant :

```azurecli-interactive
az deployment group create --name <deployment-name> --resource-group <resource-group-name> --template-file <template-file.json> --parameters iotHubName=<valid-iothub-name> skuName=<sku-name> skuTier=<sku-tier> location=<any-of-supported-regions>
```

Une fois la ressource créée, vous pouvez récupérer l'identité affectée par le système à votre hub en utilisant l’interface de ligne de commande Azure :

```azurecli-interactive
az resource show --resource-type Microsoft.Devices/IotHubs --name <iot-hub-resource-name> --resource-group <resource-group-name>
```

## <a name="user-assigned-managed-identity"></a>Identité managée affectée par l’utilisateur

Dans cette section, vous allez découvrir comment ajouter une identité managée affectée par l’utilisateur sur un hub IoT et comment l’en supprimer en utilisant le portail Azure.

1. Tout d’abord, vous devez créer une identité managée affectée par l’utilisateur en tant que ressource autonome. Pour ce faire, vous pouvez suivre les instructions fournies dans [Créer une identité managée affectée par l’utilisateur](./../active-directory/managed-identities-azure-resources/how-manage-user-assigned-managed-identities.md#create-a-user-assigned-managed-identity).
2. Accédez à votre hub IoT, puis à l’**identité** dans le portail IoT Hub.
3. Sous l’onglet **Affectée par l’utilisateur**, cliquez sur **Associer une identité managée affectée par l’utilisateur**. Choisissez l’identité managée affectée par l’utilisateur que vous souhaitez ajouter à votre hub, puis cliquez sur **Sélectionner**. 
4. Vous pouvez supprimer une identité affectée par l’utilisateur d’un hub IoT. Choisissez l’identité affectée par l’utilisateur que vous souhaitez supprimer, puis cliquez sur le bouton **Supprimer**. Notez que vous le supprimez uniquement du hub IoT, et que cette suppression ne supprime pas l’identité affectée par l’utilisateur en tant que ressource. Pour supprimer l’identité affectée par l’utilisateur en tant que ressource, suivez les instructions fournies dans [Supprimer une identité managée affectée par l’utilisateur](./../active-directory/managed-identities-azure-resources/how-manage-user-assigned-managed-identities.md#delete-a-user-assigned-managed-identity).

    :::image type="content" source="./media/iot-hub-managed-identity/user-assigned.png" alt-text="Capture d’écran montrant comment ajouter une identité managée affectée par l’utilisateur pour un hub IoT.":::

### <a name="enable-user-assigned-managed-identity-at-hub-creation-time-using-arm-template"></a>Activer l’identité managée affectée par l'utilisateur au moment de la création du hub en utilisant un modèle ARM

L’exemple de modèle suivant peut être utilisé pour créer un hub avec une identité managée affectée par l'utilisateur. Ce modèle crée une identité affectée par l’utilisateur portant le nom *[iothub-name-provided]-identity* et destinée au hub IoT créé. Si besoin, vous pouvez modifier ce modèle afin d’ajouter plusieurs identités affectées par l’utilisateur.
 
```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "iotHubName": {
      "type": "string",
      "metadata": {
        "description": "Name of iothub resource"
      }
    },
  "skuName": {
    "type": "string",
    "defaultValue": "S1",
    "metadata": {
      "description": "SKU name of iothub resource, by default is Standard S1"
    }
  },
  "skuTier": {
    "type": "string",
    "defaultValue": "Standard",
    "metadata": {
      "description": "SKU tier of iothub resource, by default is Standard"
    }
  },
  "location": {
    "type": "string",
    "defaultValue": "[resourceGroup().location]",
    "metadata": {
      "description": "Location of iothub resource. Please provide any of supported-regions of iothub"
    }
  }
},
  "variables": {
    "identityName": "[concat(parameters('iotHubName'), '-identity')]"
  },
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-10-01",
      "name": "createIotHub",
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "resources": [
            {
              "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
              "name": "[variables('identityName')]",
              "apiVersion": "2018-11-30",
              "location": "[resourceGroup().location]"
            },
            {
              "type": "Microsoft.Devices/IotHubs",
              "apiVersion": "2021-03-31",
              "name": "[parameters('iotHubName')]",
              "location": "[parameters('location')]",
              "identity": {
                "type": "UserAssigned",
                "userAssignedIdentities": {
                  "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('identityName'))]": {}
                }
              },
              "sku": {
                "name": "[parameters('skuName')]",
                "tier": "[parameters('skuTier')]",
                "capacity": 1
              },
              "dependsOn": [
                "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('identityName'))]"
              ]
            }
          ]
        }
      }
    }
  ]
}
```

```azurecli-interactive
az deployment group create --name <deployment-name> --resource-group <resource-group-name> --template-file <template-file.json> --parameters iotHubName=<valid-iothub-name> skuName=<sku-name> skuTier=<sku-tier> location=<any-of-supported-regions>
```

Une fois la ressource créée, vous pouvez récupérer l'identité managée affectée par l'utilisateur dans votre hub en utilisant l’interface de ligne de commande Azure :

```azurecli-interactive
az resource show --resource-type Microsoft.Devices/IotHubs --name <iot-hub-resource-name> --resource-group <resource-group-name>
```

## <a name="egress-connectivity-from-iot-hub-to-other-azure-resources"></a>Connectivité entrante d’IoT Hub vers d'autres ressources Azure

Les identités managées peuvent être utilisées pour la connectivité de sortie de IoT Hub à d’autres services Azure pour le [routage des messages](iot-hub-devguide-messages-d2c.md), le [chargement de fichiers](iot-hub-devguide-file-upload.md) et l’[importation/exportation d’appareils en bloc](iot-hub-bulk-identity-mgmt.md). Vous pouvez choisir l’identité managée à utiliser pour chaque connectivité de sortie d’IoT Hub aux points de terminaison appartenant au client, y compris les comptes de stockage, les Event Hubs et les points de terminaison Service Bus.

> [!NOTE]
> Utilisez une identité managée affectée par le système pour accéder aux ressources privées.

## <a name="configure-message-routing-with-managed-identities"></a>Configurer le routage des messages avec des identités managées

Dans cette section, nous utilisons le [routage des messages](iot-hub-devguide-messages-d2c.md) vers un point de terminaison personnalisé de l’Event Hub en guise d’exemple. L’exemple s’applique à d’autres points de terminaison personnalisés de routage. 

1. Tout d’abord, nous devons accéder à votre Event Hub dans Portail Azure pour attribuer l’accès approprié à l’identité managée. Dans votre Event Hub, accédez à l’onglet **Contrôle d’accès (IAM)** et cliquez sur **Ajouter**, puis sur **Ajouter une attribution de rôle**. Si vous n’avez pas les autorisations pour attribuer des rôles, l’option Ajouter une attribution de rôle sera désactivée.

2. Sélectionnez **Expéditeur de données Event Hubs en tant que rôle**.

    > [!NOTE]
    > Pour le compte de stockage, sélectionnez **Contributeur aux données Blob du stockage** ([et *non* Contributeur ou Contributeur de compte de stockage](../storage/blobs/assign-azure-role-data-access.md)) en tant que **rôle**. Pour Service Bus, sélectionnez **Expéditeur de données Service Bus** en tant que **rôle**.

3. Pour l’identité affectée par l’utilisateur, choisissez **Identité managée affectée par l’utilisateur** sous **Attribuer l’accès à**. Sélectionnez votre abonnement et votre identité managée affectée par l’utilisateur dans la liste déroulante. Cliquez sur le bouton **Enregistrer** .

    :::image type="content" source="./media/iot-hub-managed-identity/eventhub-iam-user-assigned.png" alt-text="Capture d’écran montrant le routage des messages avec affectation par l’utilisateur.":::

4. Pour l’identité affectée par le système, sous **Attribuer l’accès à**, choisissez **Utilisateur, groupe ou principal de service**, puis sélectionnez le nom de ressource de votre hub IoT dans la liste déroulante. Cliquez sur **Enregistrer**.

    :::image type="content" source="./media/iot-hub-managed-identity/eventhub-iam-system-assigned.png" alt-text="Capture d’écran montrant le routage des messages avec affectation par le système.":::

    Si vous devez restreindre la connectivité à votre point de terminaison personnalisé par le biais d’un réseau virtuel, vous devez activer l’exception des premiers tiers Microsoft approuvés pour permettre à votre hub IoT d’accéder au point de terminaison spécifique. Par exemple, si vous ajoutez un point de terminaison personnalisé d’Event Hub, accédez à l’onglet **Pare-feux et réseaux virtuels** dans votre Event Hub et activez l’option **Autoriser l’accès à partir des réseaux sélectionnés**. Sous la liste **Exceptions**, cochez la case **Autoriser les services Microsoft approuvés à accéder aux hubs d’événements**. Cliquez sur le bouton **Enregistrer** . Cela s’applique également au compte de stockage et à Service Bus. En savoir plus sur la [prise en charge des réseaux virtuels par IoT Hub](./virtual-network-support.md).

    > [!NOTE]
    > Vous devez effectuer les étapes ci-dessus pour attribuer le bon accès à l’identité managée avant d’ajouter l’Event Hub comme point de terminaison personnalisé dans IoT Hub. Patientez quelques minutes pour que l’attribution de rôle se propage.

5. Ensuite, accédez à votre hub IoT. Dans votre hub, accédez à **Routage des messages**, puis cliquez sur **Points de terminaison personnalisés**. Cliquez sur **Ajouter** et choisissez le type de point de terminaison que vous souhaitez utiliser. Dans cette section, nous utilisons Event Hub comme exemple.

6. En bas de la page, choisissez votre **type d’authentification** par défaut. Dans cette section, nous utilisons l’identité **Affectée par l’utilisateur** comme exemple. Dans la liste déroulante, sélectionnez l’identité managée affectée par l’utilisateur par défaut, puis cliquez sur **Créer**.

    :::image type="content" source="./media/iot-hub-managed-identity/eventhub-routing-endpoint.png" alt-text="Capture d’écran montrant le hub d’événement avec affectation par l’utilisateur.":::

7. Point de terminaison personnalisé correctement créé.

8. Après la création, vous pouvez toujours modifier le type d’authentification. Sélectionnez **Routage des messages** dans le volet de navigation gauche, puis **Points de terminaison personnalisés**. Sélectionnez le point de terminaison personnalisé dont vous souhaitez modifier le type d’authentification, puis cliquez sur **Modifier le type d’authentification**.

9. Choisissez le nouveau type d’authentification à mettre à jour pour ce point de terminaison, puis cliquez sur **Enregistrer**.

## <a name="configure-file-upload-with-managed-identities"></a>Configurer le chargement de fichiers avec des identités managées

La fonctionnalité de [chargement de fichiers](iot-hub-devguide-file-upload.md) d’IoT Hub permet aux appareils de charger des fichiers sur un compte de stockage appartenant au client. Pour que le chargement de fichiers puisse fonctionner, IoT Hub doit être connecté au compte de stockage. À l’instar du routage des messages, vous pouvez choisir le type d’authentification et l’identité managée de votre choix pour la connectivité de sortie d’IoT Hub à votre compte de stockage Azure. 

1. Dans le portail Azure, accédez à l'onglet **Contrôle d'accès (IAM)** de votre compte de stockage, puis cliquez sur **Ajouter** dans la section **Ajouter une attribution de rôle**.
2. Sélectionnez **Contributeur aux données Blob du stockage** (et non Contributeur ou Contributeur de compte de stockage) en tant que rôle.
3. Pour l’identité affectée par l’utilisateur, choisissez **Identité managée affectée par l’utilisateur** sous Attribuer l’accès à. Sélectionnez votre abonnement et votre identité managée affectée par l’utilisateur dans la liste déroulante. Cliquez sur le bouton **Enregistrer** .
4. Pour l’identité affectée par le système, sous **Attribuer l’accès à**, choisissez **Utilisateur, groupe ou principal de service**, puis sélectionnez le nom de ressource de votre hub IoT dans la liste déroulante. Cliquez sur **Enregistrer**.

    Si vous devez restreindre la connectivité à votre compte de stockage par le biais d’un réseau virtuel, vous devez activer l’exception des premiers tiers Microsoft approuvés pour permettre à votre hub IoT d’accéder au compte de stockage. Sur la page des ressources de votre compte de stockage, accédez à l’onglet **Pare-feux et réseaux virtuels** et activez l’option **Autoriser l’accès à partir des réseaux sélectionnés**. Sous la liste **Exceptions**, cochez la case **Autoriser les services Microsoft approuvés à accéder à ce compte de stockage**. Cliquez sur le bouton **Enregistrer** . En savoir plus sur la [prise en charge des réseaux virtuels par IoT Hub](./virtual-network-support.md). 

    > [!NOTE]
    > Vous devez effectuer les étapes ci-dessus pour attribuer à l’identité managée le bon accès avant d’enregistrer le compte de stockage dans IoT Hub pour le chargement de fichiers à l’aide de l’identité managée. Patientez quelques minutes pour que l’attribution de rôle se propage.
5. Sur la page des ressources de votre hub IoT, accédez à l’onglet **Chargement de fichiers**.
6. Sur la page qui s’affiche, sélectionnez le conteneur que vous comptez utiliser dans votre stockage blob, puis configurez les **paramètres de notification de fichier, la durée de vie SAP, la durée de vie par défaut et le nombre maximal de distributions** comme vous le souhaitez. Choisissez le type d’authentification de votre choix, puis cliquez sur **Enregistrer**. Si vous recevez une erreur à cette étape, configurez temporairement votre compte de stockage afin d’autoriser l’accès à partir de **tous les réseaux**, puis réessayez. Vous pouvez configurer le pare-feu sur le compte de stockage une fois la configuration du chargement de fichiers terminée.

    :::image type="content" source="./media/iot-hub-managed-identity/file-upload.png" alt-text="Capture d’écran montrant le chargement de fichiers avec MSI.":::

    > [!NOTE]
    > Dans le scénario de chargement de fichiers, le hub et votre appareil doivent tous deux se connecter à votre compte de stockage. Les étapes ci-dessus permettent de connecter votre hub IoT à votre compte de stockage avec le type d’authentification souhaité. Vous devez toujours connecter votre appareil au stockage à l’aide de l’URI SAS. Actuellement, l’URI SAS est généré à l’aide d’une chaîne de connexion. Prochainement, l’URI SAS pourra également être généré à l’aide d’une identité managée. Suivez la procédure décrite dans [Chargement de fichiers](iot-hub-devguide-file-upload.md).

## <a name="configure-bulk-device-importexport-with-managed-identities"></a>Configurer l’importation/exportation d’appareils en bloc avec des identités managées

IoT Hub prend en charge la fonctionnalité permettant d’[importer/exporter](iot-hub-bulk-identity-mgmt.md) en bloc les informations des appareils depuis/vers un stockage de blobs fourni par le client. Cette fonctionnalité nécessite une connectivité entre IoT Hub et le compte de stockage. 

1. Dans le portail Azure, accédez à l'onglet **Contrôle d'accès (IAM)** de votre compte de stockage, puis cliquez sur **Ajouter** dans la section **Ajouter une attribution de rôle**.
2. Sélectionnez **Contributeur aux données Blob du stockage** (et non Contributeur ou Contributeur de compte de stockage) en tant que rôle.
3. Pour l’identité affectée par l’utilisateur, choisissez **Identité managée affectée par l’utilisateur** sous Attribuer l’accès à. Sélectionnez votre abonnement et votre identité managée affectée par l’utilisateur dans la liste déroulante. Cliquez sur le bouton **Enregistrer** .
4. Pour l’identité affectée par le système, sous **Attribuer l’accès à**, choisissez **Utilisateur, groupe ou principal de service**, puis sélectionnez le nom de ressource de votre hub IoT dans la liste déroulante. Cliquez sur **Enregistrer**.

### <a name="using-rest-api-or-sdk-for-import-and-export-jobs"></a>Utilisation de l’API REST ou du Kit de développement logiciel (SDK) pour les travaux d’importation et d’exportation

Vous pouvez désormais utiliser les API REST d’Azure IoT pour créer des travaux d’importation et d’exportation. Vous devrez fournir les propriétés suivantes dans le corps de la demande :

- **storageAuthenticationType** : Définissez la valeur sur **identityBased**. 
- **inputBlobContainerUri** : Définissez cette propriété uniquement dans le travail d’importation.
- **outputBlobContainerUri** : Définissez cette propriété pour les travaux d’importation et d’exportation.
- **identity** : Définissez la valeur sur l’identité managée à utiliser.

Les kits SDK IoT Hub Azure prennent également en charge cette fonctionnalité dans le gestionnaire de registre du client du service. L’extrait de code suivant indique comment créer une tâche d’importation ou d’exportation en utilisant le SDK C#.

**Extrait de code C#**

```csharp
    // Create an export job
 
    using RegistryManager srcRegistryManager = RegistryManager.CreateFromConnectionString(hubConnectionString);
 
    JobProperties jobProperties = JobProperties.CreateForExportJob(
        outputBlobContainerUri: blobContainerUri,
        excludeKeysInExport: false,
        storageAuthenticationType: StorageAuthenticationType.IdentityBased,
        identity: new ManagedIdentity
        {
            userAssignedIdentity = userDefinedManagedIdentityResourceId
        });
```
 
```csharp
    // Create an import job
    
    using RegistryManager destRegistryManager = RegistryManager.CreateFromConnectionString(hubConnectionString);
 
    JobProperties jobProperties = JobProperties.CreateForImportJob(
        inputBlobContainerUri: blobContainerUri,
        outputBlobContainerUri: blobContainerUri,
        storageAuthenticationType: StorageAuthenticationType.IdentityBased,
        identity: new ManagedIdentity
        {
            userAssignedIdentity = userDefinedManagedIdentityResourceId
        });
```  

**Extrait de code Python**

```python
# see note below
iothub_job_manager = IoTHubJobManager("<IoT Hub connection string>")

# Create an import job
result = iothub_job_manager.create_import_export_job(JobProperties(
    type="import",
    input_blob_container_uri="<input container URI>",
    output_blob_container_uri="<output container URI>",
    storage_authentication_type="identityBased",
    identity=ManagedIdentity(
        user_assigned_identity="<resource ID of user assigned managed identity>"
    )
))

# Create an export job
result = iothub_job_manager.create_import_export_job(JobProperties(
    type="export",
    output_blob_container_uri="<output container URI>",
    storage_authentication_type="identityBased",
    exclude_keys_in_export=True,
    identity=ManagedIdentity(
        user_assigned_identity="<resource ID of user assigned managed identity>"
    ) 
))
```

> [!NOTE]
> - Si **storageAuthenticationType** est défini sur **identityBased** et que la propriété **userAssignedIdentity** n’a pas la valeur **Null**, les travaux utilisent l’identité managée affectée par l’utilisateur spécifiée.
> - Si le hub IoT n’est pas configuré avec l’identité managée affectée par l’utilisateur spécifiée dans **userAssignedIdentity**, le travail échoue.
> - Si **storageAuthenticationType** est défini sur **identityBased** et que la propriété **userAssignedIdentity** a la valeur Null, les travaux utilisent l’identité affectée par le système.
> - Si le hub IoT n’est pas configuré avec l’identité managée affectée par l’utilisateur, le travail échoue.
> - Si **storageAuthenticationType** est défini sur **identityBased** et que ni les identités managées **affectées par l’utilisateur** ni celles **affectées par le système** ne sont configurées sur le hub, le travail échoue.

## <a name="sdk-samples"></a>Exemples du Kit de développement logiciel (SDK)
- [Exemple du Kit de développement logiciel (SDK) .NET](https://aka.ms/iothubmsicsharpsample)
- [Exemple du Kit de développement logiciel (SDK) Java](https://aka.ms/iothubmsijavasample)
- [Exemple du Kit de développement logiciel (SDK) Python](https://aka.ms/iothubmsipythonsample)
- Exemples du Kit de développement logiciel (SDK) Node.js : [importation d’appareils en bloc](https://aka.ms/iothubmsinodesampleimport), [exportation d’appareils en bloc](https://aka.ms/iothubmsinodesampleexport)

## <a name="next-steps"></a>Étapes suivantes

Utilisez les liens ci-dessous pour en savoir plus sur les fonctionnalités IoT Hub :

* [Routage de messages](./iot-hub-devguide-messages-d2c.md)
* [Chargement de fichiers](./iot-hub-devguide-file-upload.md)
* [Importation/exportation d’appareils en bloc](./iot-hub-bulk-identity-mgmt.md)
