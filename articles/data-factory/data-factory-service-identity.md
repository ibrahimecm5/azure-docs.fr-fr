---
title: Identité managée
titleSuffix: Azure Data Factory & Azure Synapse
description: En savoir plus sur l’utilisation des identités managées dans Azure Data Factory et Azure Synapse.
author: nabhishek
ms.service: data-factory
ms.subservice: security
ms.topic: conceptual
ms.date: 07/19/2021
ms.author: abnarain
ms.custom: devx-track-azurepowershell, synapse
ms.openlocfilehash: b775c3873e28fbd6f59b8686800ccf733fb34ae6
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131457902"
---
# <a name="managed-identity-for-azure-data-factory-and-azure-synapse"></a>Identité managée pour Azure Data Factory et Azure Synapse 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Cet article vous aide à comprendre l’identité managée (anciennement Managed Service Identity/MSI) et son fonctionnement dans Azure Data Factory et Azure Synapse.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Vue d'ensemble

Les identités managées éliminent la nécessité de gérer les informations d’identification. Les identités managées fournissent une identité pour l’instance de service lors de la connexion à des ressources qui prennent en charge l’authentification Azure Active Directory (Azure AD). Par exemple, le service peut utiliser une identité managée pour accéder à des ressources comme [Azure Key Vault](../key-vault/general/overview.md), où les administrateurs de données peuvent stocker des informations d’identification ou accéder à des comptes de stockage de manière sécurisée. Le service utilise l’identité managée pour obtenir des jetons Azure AD.

Il existe deux types d’identités managées prises en charge : 

- **Affecté par le système** : vous pouvez activer une identité managée directement sur une instance de service. Quand vous autorisez une identité managée affectée par le système durant la création du service, une identité est créée dans Azure AD ; cette identité est liée au cycle de vie de l’instance de service. Par défaut, seule cette ressource Azure peut utiliser cette identité pour demander des jetons à Azure AD. Ainsi, quand la ressource est supprimée, Azure supprime automatiquement l’identité. Azure Synapse Analytics exige qu’une identité managée affectée par le système soit créée avec l’espace de travail Synapse.
- **Affectées par l’utilisateur** : vous pouvez également créer une identité managée en tant que ressource Azure autonome. Vous pouvez [créer une identité managée affectée par l’utilisateur](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) et l’attribuer à une ou plusieurs instances d’une fabrique de données ou d’un espace de travail Synapse. Une identité managée affectée par l’utilisateur est gérée séparément des ressources qui l’utilisent.

L’identité managée offre les avantages suivants :

- [Stocker des informations d’identification dans Azure Key Vault](store-credentials-in-key-vault.md), pour laquelle l’identité managée est utilisée pour l’authentification auprès d’Azure Key Vault.
- Accédez aux magasins de données ou aux calculs à l’aide de l’authentification d’identité managée, y compris le stockage Blob Azure, Azure Data Explorer, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Azure SQL Database, Azure SQL Managed Instance, Azure Synapse Analytics, REST, une activité Databricks, une activité web, etc. Pour plus d’informations, consultez les articles relatifs au connecteur et à l’activité.
- L’identité managée est également utilisée pour chiffrer/déchiffrer les métadonnées au moyen de la clé managée par le client qui est stockée dans Azure Key Vault, offrant ainsi un chiffrement double. 

## <a name="system-assigned-managed-identity"></a>Identité managée affectée par le système 

>[!NOTE]
> L’identité managée affectée par le système est également appelée « identité managée » ailleurs dans la documentation et dans l’interface utilisateur de Data Factory Studio et de Synapse Studio à des fins de compatibilité descendante. Nous indiquerons explicitement « identité managée affectée par l’utilisateur » lorsque nous ferons référence à cette notion. 

### <a name="generate-system-assigned-managed-identity"></a><a name="generate-managed-identity"></a> Générer l'identité managée affectée par le système

L’identité managée affectée par le système est générée comme suit :

- Lors de la création d’une fabrique de données ou d’un espace de travail Synapse via le **portail Azure ou PowerShell**, l’identité managée est toujours créée automatiquement.
- Lorsque vous créez une fabrique de données ou un espace de travail via le kit de développement logiciel (**SDK**), l’identité managée est créée uniquement si vous spécifiez « Identity = New FactoryIdentity() » dans l’objet de fabrique ou « Identity = New ManagedIdentity» dans l’objet d’espace de travail Synapse pour la création.  Consultez l’exemple dans [Démarrage rapide .NET - Créer une fabrique de données](quickstart-create-data-factory-dot-net.md#create-a-data-factory).
- Lors de la création d’une fabrique de données ou d’un espace de travail Synapse grâce à l’**API REST**, l’identité managée n’est créée que si vous le spécifiez la section « identity » dans le corps de la requête. Consultez l’exemple dans [Démarrage rapide REST - Créer une fabrique de données](quickstart-create-data-factory-rest-api.md#create-a-data-factory).

Si vous constatez que votre instance de service n’est pas associée à une identité managée après l’instruction [retrieve managed identity](#retrieve-managed-identity), vous pouvez en générer une explicitement par programmation, en la mettant à jour avec l’initiateur d’identité :

- [Générer l’identité managée à l’aide de PowerShell](#generate-system-assigned-managed-identity-using-powershell)
- [Générer l’identité managée à l’aide de REST API](#generate-system-assigned-managed-identity-using-rest-api)
- [Générer l’identité managée avec un modèle Azure Resource Manager](#generate-system-assigned-managed-identity-using-an-azure-resource-manager-template)
- [Générer l’identité managée à l’aide du SDK](#generate-system-assigned-managed-identity-using-sdk)

>[!NOTE]
>
>- L’identité managée ne peut pas être modifiée. La mise à jour d’une instance de service qui possède déjà une identité managée n’aura aucun effet et l’identité managée restera inchangée.
>- Si vous mettez à jour une instance de service qui dispose déjà d’une identité managée, sans spécifier le paramètre « identity » dans l’objet fabrique ou espace de travail, ou sans spécifier la section « identity » dans le corps de la requête REST, vous recevez un message d’erreur.
>- Lorsque vous supprimez une instance de service, l’identité managée associée est également supprimée.

#### <a name="generate-system-assigned-managed-identity-using-powershell"></a>Générer l’identité managée affectée par le système en utilisant PowerShell

# <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory).

Appelez la commande **Set-AzDataFactoryV2**, vous verrez alors les champs « identity » qui viennent d’être générés :

```powershell
PS C:\> Set-AzDataFactoryV2 -ResourceGroupName <resourceGroupName> -Name <dataFactoryName> -Location <region>

DataFactoryName   : ADFV2DemoFactory
DataFactoryId     : /subscriptions/<subsID>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/ADFV2DemoFactory
ResourceGroupName : <resourceGroupName>
Location          : East US
Tags              : {}
Identity          : Microsoft.Azure.Management.DataFactory.Models.FactoryIdentity
ProvisioningState : Succeeded
```
# <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

Appelez la commande **New-AzSynapseWorkspace**, puis affichez les champs « Identity » qui viennent d’être générés :

```powershell
PS C:\> $creds = New-Object System.Management.Automation.PSCredential ("ContosoUser", $password)
PS C:\> New-AzSynapseWorkspace -ResourceGroupName <resourceGroupName> -Name <workspaceName> -Location <region> -DefaultDataLakeStorageAccountName <storageAccountName> -DefaultDataLakeStorageFileSystem <fileSystemName> -SqlAdministratorLoginCredential $creds

DefaultDataLakeStorage           : Microsoft.Azure.Commands.Synapse.Models.PSDataLakeStorageAccountDetails
ProvisioningState                : Succeeded
SqlAdministratorLogin            : ContosoUser
VirtualNetworkProfile            :
Identity                         : Microsoft.Azure.Commands.Synapse.Models.PSManagedIdentity
ManagedVirtualNetwork            :
PrivateEndpointConnections       : {}
WorkspaceUID                     : <workspaceUid>
ExtraProperties                  : {[WorkspaceType, Normal], [IsScopeEnabled, False]}
ManagedVirtualNetworkSettings    :
Encryption                       : Microsoft.Azure.Commands.Synapse.Models.PSEncryptionDetails
WorkspaceRepositoryConfiguration :
Tags                             :
TagsTable                        :
Location                         : <region>
Id                               : /subscriptions/<subsID>/resourceGroups/<resourceGroupName>/providers/
                                   Microsoft.Synapse/workspaces/<workspaceName>
Name                             : <workspaceName>
Type                             : Microsoft.Synapse/workspaces
```
---

#### <a name="generate-system-assigned-managed-identity-using-rest-api"></a>Générer l’identité managée affectée par le système en utilisant l’API REST

# <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory).

> [!NOTE]
> Si vous tentez de mettre à jour une instance de service qui possède déjà une identité managée sans spécifier le paramètre **Identity** dans l’objet de fabrique ou fournir de section **Identity** dans le corps de la requête REST, vous obtiendrez une erreur.

Appelez l’API ci-dessous avec la section « identity » dans le corps de la demande :

```
PATCH https://management.azure.com/subscriptions/<subsID>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<data factory name>?api-version=2018-06-01
```

**Corps de la requête** : add "identity": { "type": "SystemAssigned" }.

```json
{
    "name": "<dataFactoryName>",
    "location": "<region>",
    "properties": {},
    "identity": {
        "type": "SystemAssigned"
    }
}
```

**Réponse** : l’identité managée est créée automatiquement, et la section « identity » est remplie en conséquence.

```json
{
    "name": "<dataFactoryName>",
    "tags": {},
    "properties": {
        "provisioningState": "Succeeded",
        "loggingStorageAccountKey": "**********",
        "createTime": "2017-09-26T04:10:01.1135678Z",
        "version": "2018-06-01"
    },
    "identity": {
        "type": "SystemAssigned",
        "principalId": "765ad4ab-XXXX-XXXX-XXXX-51ed985819dc",
        "tenantId": "72f988bf-XXXX-XXXX-XXXX-2d7cd011db47"
    },
    "id": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<dataFactoryName>",
    "type": "Microsoft.DataFactory/factories",
    "location": "<region>"
}
```
# <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

> [!NOTE]
> Si vous tentez de mettre à jour une instance de service qui possède déjà une identité managée sans spécifier le paramètre **Identity** dans l’objet d’espace de travail ou fournir de section **Identity** dans le corps de la requête REST, vous obtiendrez une erreur.

Appelez l’API ci-dessous avec la section « identity » dans le corps de la demande :

```
PATCH https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Synapse/workspaces/{workspaceName}?api-version=2018-06-01
```

**Corps de la requête** : add "identity": { "type": "SystemAssigned" }.

```json
{
    "name": "<workspaceName>",
    "location": "<region>",
    "properties": {},
    "identity": {
        "type": "SystemAssigned"
    }
}
```

**Réponse** : l’identité managée est créée automatiquement, et la section « identity » est remplie en conséquence.

```json
{
    "name": "<workspaceName>",
    "tags": {},
    "properties": {
        "provisioningState": "Succeeded",
        "loggingStorageAccountKey": "**********",
        "createTime": "2021-09-26T04:10:01.1135678Z",
        "version": "2018-06-01"
    },
    "identity": {
        "type": "SystemAssigned",
        "principalId": "765ad4ab-XXXX-XXXX-XXXX-51ed985819dc",
        "tenantId": "72f988bf-XXXX-XXXX-XXXX-2d7cd011db47"
    },
    "id": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Synapse/workspaces/<workspaceName>",
    "type": "Microsoft.Synapse/workspaces",
    "location": "<region>"
}
```
---

#### <a name="generate-system-assigned-managed-identity-using-an-azure-resource-manager-template"></a>Générer l’identité managée affectée par le système en utilisant un modèle Azure Resource Manager

# <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory).
**Modèle** : add "identity": { "type": "SystemAssigned" }.

```json
{
    "contentVersion": "1.0.0.0",
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "resources": [{
        "name": "<dataFactoryName>",
        "apiVersion": "2018-06-01",
        "type": "Microsoft.DataFactory/factories",
        "location": "<region>",
        "identity": {
            "type": "SystemAssigned"
        }
    }]
}
```
# <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)
**Modèle** : add "identity": { "type": "SystemAssigned" }.

```json
{
    "contentVersion": "1.0.0.0",
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "resources": [{
        "name": "<workspaceName>",
        "apiVersion": "2018-06-01",
        "type": "Microsoft.Synapse/workspaces",
        "location": "<region>",
        "identity": {
            "type": "SystemAssigned"
        }
    }]
}
```
---

#### <a name="generate-system-assigned-managed-identity-using-sdk"></a>Générer l’identité managée affectée par le système en utilisant le SDK

# <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory).
Appelez la fonction create_or_update avec Identity=new FactoryIdentity(). Exemple de code utilisant NET :

```csharp
Factory dataFactory = new Factory
{
    Location = <region>,
    Identity = new FactoryIdentity()
};
client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, dataFactory);
```
# <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)
```csharp
Workspace workspace = new Workspace
{
    Identity = new ManagedIdentity
    {
        Type = ResourceIdentityType.SystemAssigned
    },
    DefaultDataLakeStorage = new DataLakeStorageAccountDetails
    {
        AccountUrl = <defaultDataLakeStorageAccountUrl>,
        Filesystem = <DefaultDataLakeStorageFilesystem>
    },
    SqlAdministratorLogin = <SqlAdministratorLoginCredentialUserName>
    SqlAdministratorLoginPassword = <SqlAdministratorLoginCredentialPassword>,
    Location = <region>
};
client.Workspaces.CreateOrUpdate(resourceGroupName, workspaceName, workspace);
```
---

### <a name="retrieve-system-assigned-managed-identity"></a><a name="retrieve-managed-identity"></a> Récupérer l’identité managée affectée par le système

Vous pouvez récupérer l’identité managée à partir du portail Azure ou par programmation. Les sections suivantes vous montrent quelques exemples.

>[!TIP]
> Si vous ne voyez pas l’identité managée, [générez l’identité managée](#generate-managed-identity) en mettant à jour votre instance de service.

#### <a name="retrieve-system-assigned-managed-identity-using-azure-portal"></a>Récupérer l’identité managée affectée par le système en utilisant le portail Azure

Les informations relatives à l’identité managée sont disponibles sur le portail Azure sous : votre fabrique de données ou espace de travail Synapse -> Propriétés.

- ID d’objet de l’identité managée
- Locataire de l’identité managée (applicable uniquement à Azure Data Factory)

Les informations relatives à l'identité managée apparaissent également lorsque vous créez un service lié qui prend en charge l'authentification de l'identité managée, comme Azure Blob, Azure Data Lake Storage, Azure Key Vault, etc.

Lors de l’octroi de l’autorisation, sous l’onglet Access Control (IAM) de la ressource Azure -> Ajouter une attribution de rôle -> Affecter l’accès à -> sélectionnez Data Factory sous Identité managée par le système -> sélectionnez par nom de fabrique. En général, vous pouvez utiliser l’ID d’objet ou le nom de la fabrique de données (comme nom d’identité managée) pour trouver cette identité. Si vous avez besoin d’obtenir l’ID d’application de l’identité managée, vous pouvez utiliser PowerShell.

#### <a name="retrieve-system-assigned-managed-identity-using-powershell"></a>Récupérer l’identité managée affectée par le système en utilisant PowerShell

# <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory).
L’ID de principal et l’ID de locataire de l’identité managée seront renvoyés lorsque vous aurez obtenu une instance de service spécifique, comme illustré ci-dessous. Utilisez le paramètre **PrincipalId** pour octroyer l'accès :

```powershell
PS C:\> (Get-AzDataFactoryV2 -ResourceGroupName <resourceGroupName> -Name <dataFactoryName>).Identity

PrincipalId                          TenantId
-----------                          --------
765ad4ab-XXXX-XXXX-XXXX-51ed985819dc 72f988bf-XXXX-XXXX-XXXX-2d7cd011db47
```

Pour obtenir l'ID de l'application, copiez l'ID du principal, puis exécutez la commande Azure Active Directory ci-dessous avec l'ID du principal comme paramètre.

```powershell
PS C:\> Get-AzADServicePrincipal -ObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc

ServicePrincipalNames : {76f668b3-XXXX-XXXX-XXXX-1b3348c75e02, https://identity.azure.net/P86P8g6nt1QxfPJx22om8MOooMf/Ag0Qf/nnREppHkU=}
ApplicationId         : 76f668b3-XXXX-XXXX-XXXX-1b3348c75e02
DisplayName           : ADFV2DemoFactory
Id                    : 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
Type                  : ServicePrincipal
```
# <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)
L’ID de principal et l’ID de locataire de l’identité managée seront renvoyés lorsque vous aurez obtenu une instance de service spécifique, comme illustré ci-dessous. Utilisez le paramètre **PrincipalId** pour octroyer l'accès :

```powershell
PS C:\> (Get-AzSynapseWorkspace -ResourceGroupName <resourceGroupName> -Name <workspaceName>).Identity

IdentityType   PrincipalId                          TenantId                            
------------   -----------                          --------                            
SystemAssigned cadadb30-XXXX-XXXX-XXXX-ef3500e2ff05 72f988bf-XXXX-XXXX-XXXX-2d7cd011db47
```

Pour obtenir l'ID de l'application, copiez l'ID du principal, puis exécutez la commande Azure Active Directory ci-dessous avec l'ID du principal comme paramètre.

```powershell
PS C:\> Get-AzADServicePrincipal -ObjectId cadadb30-XXXX-XXXX-XXXX-ef3500e2ff05

ServicePrincipalNames : {76f668b3-XXXX-XXXX-XXXX-1b3348c75e02, https://identity.azure.net/P86P8g6nt1QxfPJx22om8MOooMf/Ag0Qf/nnREppHkU=}
ApplicationId         : 76f668b3-XXXX-XXXX-XXXX-1b3348c75e02
DisplayName           : <workspaceName>
Id                    : cadadb30-XXXX-XXXX-XXXX-ef3500e2ff05
Type                  : ServicePrincipal
```
---

#### <a name="retrieve-managed-identity-using-rest-api"></a>Récupérer l’identité managée à l’aide d’API REST

# <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory).
L’ID de principal et l’ID de locataire de l’identité managée seront renvoyés lorsque vous aurez obtenu une instance de service spécifique, comme illustré ci-dessous.

Appeler l’API ci-dessous dans la requête :

```
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataFactory/factories/{factoryName}?api-version=2018-06-01
```

**Réponse**: Vous obtiendrez une réponse comme indiqué dans l’exemple ci-dessous. La section « identité » est remplie en conséquence.

```json
{
    "name":"<dataFactoryName>",
    "identity":{
        "type":"SystemAssigned",
        "principalId":"554cff9e-XXXX-XXXX-XXXX-90c7d9ff2ead",
        "tenantId":"72f988bf-XXXX-XXXX-XXXX-2d7cd011db47"
    },
    "id":"/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<dataFactoryName>",
    "type":"Microsoft.DataFactory/factories",
    "properties":{
        "provisioningState":"Succeeded",
        "createTime":"2020-02-12T02:22:50.2384387Z",
        "version":"2018-06-01",
        "factoryStatistics":{
            "totalResourceCount":0,
            "maxAllowedResourceCount":0,
            "factorySizeInGbUnits":0,
            "maxAllowedFactorySizeInGbUnits":0
        }
    },
    "eTag":"\"03006b40-XXXX-XXXX-XXXX-5e43617a0000\"",
    "location":"<region>",
    "tags":{

    }
}
```

> [!TIP] 
> Pour récupérer l’identité managée à partir d’un modèle ARM, ajoutez une section **sorties** dans le JSON ARM :

```json
{
    "outputs":{
        "managedIdentityObjectId":{
            "type":"string",
            "value":"[reference(resourceId('Microsoft.DataFactory/factories', parameters('<dataFactoryName>')), '2018-06-01', 'Full').identity.principalId]"
        }
    }
}
```
# <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)
L’ID de principal et l’ID de locataire de l’identité managée seront renvoyés lorsque vous aurez obtenu une instance de service spécifique, comme illustré ci-dessous.

Appeler l’API ci-dessous dans la requête :

```
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Synapse/workspaces/{workspaceName}?api-version=2018-06-01
```

**Réponse**: Vous obtiendrez une réponse comme indiqué dans l’exemple ci-dessous. La section « identité » est remplie en conséquence.

```json
{
  "properties": {
    "defaultDataLakeStorage": {
      "accountUrl": "https://exampledatalakeaccount.dfs.core.windows.net",
      "filesystem": "examplefilesystem"
    },
    "encryption": {
      "doubleEncryptionEnabled": false
    },
    "provisioningState": "Succeeded",
    "connectivityEndpoints": {
      "web": "https://web.azuresynapse.net?workspace=%2fsubscriptions%2{subscriptionId}%2fresourceGroups%2f{resourceGroupName}%2fproviders%2fMicrosoft.Synapse%2fworkspaces%2f{workspaceName}",
      "dev": "https://{workspaceName}.dev.azuresynapse.net",
      "sqlOnDemand": "{workspaceName}-ondemand.sql.azuresynapse.net",
      "sql": "{workspaceName}.sql.azuresynapse.net"
    },
    "managedResourceGroupName": "synapseworkspace-managedrg-f77f7cf2-XXXX-XXXX-XXXX-c4cb7ac3cf4f",
    "sqlAdministratorLogin": "sqladminuser",
    "privateEndpointConnections": [],
    "workspaceUID": "e56f5773-XXXX-XXXX-XXXX-a0dc107af9ea",
    "extraProperties": {
      "WorkspaceType": "Normal",
      "IsScopeEnabled": false
    },
    "publicNetworkAccess": "Enabled",
    "cspWorkspaceAdminProperties": {
      "initialWorkspaceAdminObjectId": "3746a407-XXXX-XXXX-XXXX-842b6cf1fbcc"
    },
    "trustedServiceBypassEnabled": false
  },
  "type": "Microsoft.Synapse/workspaces",
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Synapse/workspaces/{workspaceName}",
  "location": "eastus",
  "name": "{workspaceName}",
  "identity": {
    "type": "SystemAssigned",
    "tenantId": "72f988bf-XXXX-XXXX-XXXX-2d7cd011db47",
    "principalId": "cadadb30-XXXX-XXXX-XXXX-ef3500e2ff05"
  },
  "tags": {}
}
```

> [!TIP] 
> Pour récupérer l’identité managée à partir d’un modèle ARM, ajoutez une section **sorties** dans le JSON ARM :

```json
{
    "outputs":{
        "managedIdentityObjectId":{
            "type":"string",
            "value":"[reference(resourceId('Microsoft.Synapse/workspaces', parameters('<workspaceName>')), '2018-06-01', 'Full').identity.principalId]"
        }
    }
}
```
---

## <a name="user-assigned-managed-identity"></a>Identité managée affectée par l’utilisateur

Vous pouvez créer, supprimer et gérer les identités managées affectées par l’utilisateur dans Azure Active Directory. Pour plus d’informations, consultez [Créer, lister, supprimer et attribuer un rôle à une identité managée affectée par l’utilisateur à l’aide du portail Azure](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md). 

Pour pouvoir utiliser une identité managée affectée par l’utilisateur, vous devez d’abord [créer des informations d’identification](credentials.md) dans votre instance de service pour l’identité managée affectée par l’utilisateur.

## <a name="next-steps"></a>Étapes suivantes

- [Créer des informations d’identification](credentials.md).

Consultez les rubriques suivantes, qui expliquent quand et comment utiliser l’identité managée :

- [Stocker des informations d’identification dans Azure Key Vault](store-credentials-in-key-vault.md).
- [Copier des données vers ou depuis Azure Data Lake Store à l’aide d’Azure Data Factory](connector-azure-data-lake-store.md).

Pour plus d’informations sur les identités managées pour les ressources Azure, sur lesquelles l’identité managée dans Azure Data Factory et Azure Synapse est basée, consultez [Que sont les identités managées pour les ressources Azure ?](../active-directory/managed-identities-azure-resources/overview.md).
