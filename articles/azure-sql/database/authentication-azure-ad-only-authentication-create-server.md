---
title: Créer un serveur avec l’authentification Azure Active Directory uniquement activée
description: Cet article vous guide tout au long de la création d’un serveur logique Azure SQL ou d’une instance gérée avec l’authentification Azure Active Directory (Azure AD) uniquement activée, ce qui désactive la connectivité à l’aide de l’authentification SQL
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
ms.service: sql-db-mi
ms.subservice: security
ms.topic: how-to
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 11/02/2021
ms.openlocfilehash: 845ecacf97887ef3488d1fd80b40f9424234e257
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132290481"
---
# <a name="create-server-with-azure-ad-only-authentication-enabled-in-azure-sql"></a>Créer un serveur avec l’authentification Azure AD uniquement activée dans Azure SQL

[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]


Ce guide pratique décrit les étapes de création d’un [serveur logique](logical-servers.md) pour Azure SQL Database ou d’une instance [Azure SQL Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md) avec l’[authentification Azure AD uniquement](authentication-azure-ad-only-authentication.md) activée pendant le provisionnement. La fonctionnalité d’authentification Azure AD uniquement empêche les utilisateurs de se connecter au serveur ou à l’instance gérée à l’aide de l’authentification SQL, et autorise uniquement la connexion à l’aide de l’authentification Azure AD.

## <a name="prerequisites"></a>Configuration requise

- L’utilisation d’Azure CLI nécessite la version 2.26.1 ou ultérieure. Pour plus d’informations sur l’installation et la dernière version, voir [Installer Azure CLI](/cli/azure/install-azure-cli).
- Le module [Az 6.1.0](https://www.powershellgallery.com/packages/Az/6.1.0) ou une version ultérieure est nécessaire lors de l’utilisation de PowerShell.
- Si vous approvisionnez une instance gérée à l’aide d’Azure CLI, de PowerShell ou de l’API REST, vous devez créer un réseau virtuel et un sous-réseau avant de commencer. Pour plus d’informations, voir [Créer un réseau virtuel pour Azure SQL Managed Instance](../managed-instance/virtual-network-subnet-create-arm-template.md).

## <a name="permissions"></a>Autorisations

Pour provisionner un serveur logique ou une instance managée, vous devez disposer des autorisations appropriées pour créer ces ressources. Les utilisateurs Azure disposant d’autorisations plus élevées, telles que les [propriétaires](../../role-based-access-control/built-in-roles.md#owner)d’abonnement, les [contributeurs](../../role-based-access-control/built-in-roles.md#contributor), les [administrateurs de service](../../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles) et les [coadministrateurs](../../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles), ont le privilège de créer un serveur SQL ou une instance gérée. Pour créer ces ressources avec le rôle RBAC Azure le moins privilégié, utilisez le rôle [Contributeur SQL Server](../../role-based-access-control/built-in-roles.md#sql-server-contributor) pour SQL Database et [Contributeur SQL Managed Instance](../../role-based-access-control/built-in-roles.md#sql-managed-instance-contributor) pour Managed Instance.

Le rôle RBAC Azure [SQL Security Manager](../../role-based-access-control/built-in-roles.md#sql-security-manager) ne dispose pas des autorisations suffisantes pour créer un serveur ou une instance sur lequel l’authentification Azure AD uniquement est activée. Le rôle [gestionnaire de sécurité SQL](../../role-based-access-control/built-in-roles.md#sql-security-manager) sera requis pour gérer la fonctionnalité d’authentification Azure AD uniquement après la création du serveur ou de l’instance.

## <a name="provision-with-azure-ad-only-authentication-enabled"></a>Approvisionner un serveur avec l’authentification Azure AD seule activée

La section suivante fournit des exemples et des scripts sur la création d’un serveur logique ou d’une instance managée avec un groupe d’administration Azure AD pour le serveur ou l’instance, et l’activation de l’authentification Azure AD uniquement lors de la création du serveur. Pour plus d’informations sur la fonctionnalité, consultez [Authentification Azure AD uniquement](authentication-azure-ad-only-authentication.md).

Dans nos exemples, nous activons l’authentification Azure AD uniquement lors de la création d’un serveur ou d’une instance gérée, avec un administrateur de serveur et un mot de passe affectés par le système. Cela empêchera l’accès de l’administrateur du serveur lorsque l’authentification Azure AD uniquement est activée, et autorise uniquement l’administrateur Azure AD à accéder à la ressource. Il est facultatif d’ajouter des paramètres aux API pour inclure votre propre administrateur de serveur et votre propre mot de passe lors de la création du serveur. Toutefois, le mot de passe ne peut pas être réinitialisé tant que vous n’avez pas désactivé l’authentification Azure AD uniquement.

Pour modifier les propriétés existantes après la création du serveur ou de l’instance gérée, vous devez utiliser d’autres API existantes. Pour plus d’informations, consultez [Gestion de l'authentification Azure AD uniquement à l’aide d’API](authentication-azure-ad-only-authentication.md#managing-azure-ad-only-authentication-using-apis) et [Configuration et gestion de l’authentification Azure AD avec Azure SQL](authentication-aad-configure.md).

> [!NOTE]
> Si l’authentification Azure AD uniquement est définie sur false, ce qui est le cas par défaut, un administrateur de serveur et un mot de passe devront être inclus dans toutes les API lors de la création du serveur ou de l’instance gérée.

## <a name="azure-sql-database"></a>Azure SQL Database

# <a name="portal"></a>[Portail](#tab/azure-portal)

1. Accédez à la page [Sélectionner l’option de déploiement SQL](https://portal.azure.com/#create/Microsoft.AzureSQL) dans le portail Azure.

1. Si vous n’êtes pas déjà connecté au portail Azure, connectez-vous lorsque vous y êtes invité.

1. Sous **Bases de données SQL**, laissez **Type de ressource** défini sur **Base de données unique**, puis sélectionnez **Créer**.

1. Sous l’onglet **De base** du formulaire **Créer une base de données SQL**, sous **Détails du projet**, sélectionnez l’**Abonnement** Azure souhaité.

1. Pour **Groupe de ressources**, sélectionnez **Créer**, entrez un nom pour votre groupe de ressources, puis sélectionnez **OK**.

1. Pour **Nom de base de données**, entrez un nom pour votre base de données.

1. Pour **Serveur**, sélectionnez **Créer**, puis remplissez le formulaire de nouveau serveur avec les valeurs suivantes :

   - **Nom du serveur** : Entrez un nom de serveur unique. Les noms de serveur doivent être uniques au niveau mondial pour tous les serveurs Azure, et pas seulement au sein d'un abonnement. Entrez une valeur et le portail Azure vous indiquera si elle est disponible ou non.
   - **Emplacement** : sélectionnez un emplacement dans la liste déroulante
   - **Méthode d’authentification** : sélectionnez **Utiliser uniquement l’authentification Azure Active Directory (Azure AD)** .
   - Sélectionnez **Définir l’administrateur**, ce qui affiche un menu pour sélectionner un principal Azure AD comme administrateur Azure AD de serveur logique. Lorsque vous avez terminé, utilisez le bouton **Sélectionner** pour définir votre administrateur.

   :::image type="content" source="media/authentication-azure-ad-only-authentication/azure-ad-portal-create-server.png" alt-text="Capture d’écran de la création d’un serveur avec l’authentification Azure AD uniquement activée":::
    
1. Sélectionnez **Suivant : Réseau** en bas de la page.

1. Sous l’onglet **Réseau**, pour **Méthode de connectivité**, sélectionnez **Point de terminaison public**.

1. Pour **Règles de pare-feu**, affectez la valeur **Oui** à **Ajouter l’adresse IP actuelle du client**. Laissez **Autoriser les services et les ressources Azure à accéder à ce serveur** avec la valeur **Non**. 

1. Laissez la **Stratégie de connexion** et les paramètres de **Version minimale de TLS** aux valeurs par défaut.

1. Sélectionnez **Suivant : Sécurité** en bas de la page. Configurez un des paramètres entre **Microsoft Defender pour SQL**, **Registre**, **Identité** et **Chiffrement transparent des données** pour votre environnement. Vous pouvez également ignorer ces paramètres.

   > [!NOTE]
   > L’utilisation d’une identité managée affectée par l'utilisateur (UMI) n’est pas prise en charge avec l’authentification Azure AD uniquement. Ne définissez pas l’identité du serveur dans la section **Identité** comme UMI.

1. Au bas de la page, sélectionnez **Examiner et créer**.

1. Dans la page **Vérifier + créer**, après vérification, sélectionnez **Créer**.

# <a name="the-azure-cli"></a>[L’interface de ligne de commande Microsoft Azure](#tab/azure-cli)

La commande Azure CLI `az sql server create` permet de provisionner un nouveau serveur logique. La commande ci-dessous met en service un nouveau serveur avec l’authentification Azure AD uniquement activée.

La connexion de l’administrateur du serveur SQL est automatiquement créée et le mot de passe est défini sur un mot de passe aléatoire. Étant donné que la connectivité de l’authentification SQL est désactivée lors de la création de ce serveur, la connexion administrateur SQL ne sera pas utilisée.

Le serveur Azure AD admin est le compte que vous définissez pour `<AzureADAccount>` et peut être utilisé pour gérer le serveur.

Remplacez les valeurs suivantes dans l’exemple :

- `<AzureADAccount>` : Peut être un utilisateur ou un groupe Azure AD. Par exemple, `DummyLogin`
- `<AzureADAccountSID>` : L’ID d’objet Azure AD pour l’utilisateur
- `<ResourceGroupName>` : nom du groupe de ressources pour votre serveur logique
- `<ServerName>` : utiliser un nom de serveur logique unique

```azurecli
az sql server create --enable-ad-only-auth --external-admin-principal-type User --external-admin-name <AzureADAccount> --external-admin-sid <AzureADAccountSID> -g <ResourceGroupName> -n <ServerName>
```

Pour plus d’informations, voir [az sql server create](/cli/azure/sql/server#az_sql_server_create).

Pour vérifier l’état du serveur après sa création, consultez la commande suivante :

```azurecli
az sql server show --name <ServerName> --resource-group <ResourceGroupName> --expand-ad-admin
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

La commande PowerShell `New-AzSqlServer` permet de configurer un nouveau serveur logique Azure SQL. La commande ci-dessous met en service un nouveau serveur avec l’authentification Azure AD uniquement activée. 

La connexion de l’administrateur du serveur SQL est automatiquement créée et le mot de passe est défini sur un mot de passe aléatoire. Étant donné que la connectivité de l’authentification SQL est désactivée lors de la création de ce serveur, la connexion administrateur SQL ne sera pas utilisée.

Le serveur Azure AD admin est le compte que vous définissez pour `<AzureADAccount>` et peut être utilisé pour gérer le serveur.

Remplacez les valeurs suivantes dans l’exemple :

- `<ResourceGroupName>` : nom du groupe de ressources pour votre serveur logique
- `<Location>` : emplacement du serveur, tel que `West US` ou `Central US`
- `<ServerName>` : utiliser un nom de serveur logique unique
- `<AzureADAccount>` : Peut être un utilisateur ou un groupe Azure AD. Par exemple, `DummyLogin`

```powershell
New-AzSqlServer -ResourceGroupName "<ResourceGroupName>" -Location "<Location>" -ServerName "<ServerName>" -ServerVersion "12.0" -ExternalAdminName "<AzureADAccount>" -EnableActiveDirectoryOnlyAuthentication
```

Pour plus d’informations, consultez [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver).

# <a name="rest-api"></a>[API REST](#tab/rest-api)

L’API Rest [Serveurs - Créer ou mettre à jour](/rest/api/sql/2020-11-01-preview/servers/create-or-update) peut être utilisée pour créer un serveur logique avec l’authentification Azure AD uniquement activée pendant le provisionnement. 

Le script ci-dessous configure un serveur logique, définit l’administrateur Azure AD en tant que `<AzureADAccount>`, et active l’authentification Azure AD uniquement. La connexion de l’administrateur du serveur SQL est aussi automatiquement créée et le mot de passe est défini sur un mot de passe aléatoire. Étant donné que la connectivité de l’authentification SQL est désactivée lors de cet approvisionnement, la connexion administrateur SQL ne sera pas utilisée.

L’administrateur Azure AD `<AzureADAccount>` peut être utilisé pour gérer le serveur lorsque l’approvisionnement est terminé.

Remplacez les valeurs suivantes dans l’exemple :

- `<tenantId>` : accédez à votre ressource **Azure Active Directory** dans le [portail Azure](https://portal.azure.com). Dans le volet **Vue d’ensemble**, vous devez voir votre **ID de locataire**.
- `<subscriptionId>` : votre ID d’abonnement, disponible dans le Portail Azure
- `<ServerName>` : utiliser un nom de serveur logique unique
- `<ResourceGroupName>` : nom du groupe de ressources pour votre serveur logique
- `<AzureADAccount>` : Peut être un utilisateur ou un groupe Azure AD. Par exemple, `DummyLogin`
- `<Location>` : emplacement du serveur, tel que `westus2` ou `centralus`
- `<objectId>` : accédez à votre ressource **Azure Active Directory** dans le [portail Azure](https://portal.azure.com). Dans le volet **utilisateur**, recherchez l’utilisateur Azure AD et recherchez son **ID d’objet**.

```rest
Import-Module Azure
Import-Module MSAL.PS

$tenantId = '<tenantId>'
$clientId = '1950a258-227b-4e31-a9cf-717495945fc2' # Static Microsoft client ID used for getting a token
$subscriptionId = '<subscriptionId>'
$uri = "urn:ietf:wg:oauth:2.0:oob" 
$authUrl = "https://login.windows.net/$tenantId"
$serverName = "<ServerName>"
$resourceGroupName = "<ResourceGroupName>"

Login-AzAccount -tenantId $tenantId

# login as a user with SQL Server Contributor role or higher 

# Get a token

$result = Get-MsalToken -RedirectUri $uri -ClientId $clientId -TenantId $tenantId -Scopes "https://management.core.windows.net/.default"

#Authetication header
$authHeader = @{
'Content-Type'='application\json; '
'Authorization'=$result.CreateAuthorizationHeader()
}

# Enable Azure AD-only auth 
# No server admin is specified, and only Azure AD admin and Azure AD-only authentication is set to true
# Server admin (login and password) is generated by the system

# Authentication body
# The sid is the Azure AD Object ID for the user 

$body = '{ 
"location": "<Location>",
"properties": { "administrators":{ "login":"<AzureADAccount>", "sid":"<objectId>", "tenantId":"<tenantId>", "principalType":"User", "azureADOnlyAuthentication":true }
  }
}'

# Provision the server

Invoke-RestMethod -Uri https://management.azure.com/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Sql/servers/$serverName/?api-version=2020-11-01-preview -Method PUT -Headers $authHeader -Body $body -ContentType "application/json"
```

Pour vérifier l’état du serveur, vous pouvez utiliser le script suivant :

```rest
$uri = 'https://management.azure.com/subscriptions/'+$subscriptionId+'/resourceGroups/'+$resourceGroupName+'/providers/Microsoft.Sql/servers/'+$serverName+'?api-version=2020-11-01-preview&$expand=administrators/activedirectory'

$responce=Invoke-WebRequest -Uri $uri -Method PUT -Headers $authHeader -Body $body -ContentType "application/json"

$responce.statuscode

$responce.content
```

# <a name="arm-template"></a>[Modèle ARM](#tab/arm-template)

Pour plus d’informations et découvrir les modèles ARM, consultez [Modèles Azure Resource Manager pour Azure SQL Database et Azure SQL Managed Instance](arm-templates-content-guide.md).

Pour configurer un serveur logique avec un groupe d’administration Azure AD pour le serveur et l’authentification Azure AD uniquement activée à l’aide d’un modèle ARM, consultez notre modèle de démarrage rapide [Serveur logique Azure SQL avec authentification Azure AD uniquement](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.sql/sql-logical-server-aad-only-auth).

Vous pouvez également utiliser le modèle suivant. Utilisez un [déploiement personnalisé dans le portail Azure](https://portal.azure.com/#create/Microsoft.Template) et **créez votre propre modèle dans l’éditeur**. Ensuite, **enregistrez** la configuration une fois que vous avez collé l’exemple.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.1",
    "parameters": {
        "server": {
            "type": "string",
            "defaultValue": "[uniqueString('sql', resourceGroup().id)]",
            "metadata": {
                "description": "The name of the logical server."
            }
        },
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
                "description": "Location for all resources."
            }
        },
        "aad_admin_name": {
            "type": "String",
            "metadata": {
                "description": "The name of the Azure AD admin for the SQL server."
            }
        },
        "aad_admin_objectid": {
            "type": "String",
            "metadata": {
                "description": "The Object ID of the Azure AD admin."
            }
        },
        "aad_admin_tenantid": {
            "type": "String",
            "defaultValue": "[subscription().tenantId]",
            "metadata": {
                "description": "The Tenant ID of the Azure Active Directory"
            }
        },
        "aad_admin_type": {
            "defaultValue": "User",
            "allowedValues": [
                "User",
                "Group",
                "Application"
            ],
            "type": "String"
        },
        "aad_only_auth": {
            "defaultValue": true,
            "type": "Bool"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Sql/servers",
            "apiVersion": "2020-11-01-preview",
            "name": "[parameters('server')]",
            "location": "[parameters('location')]",
            "properties": {
                "administrators": {
                    "login": "[parameters('aad_admin_name')]",
                    "sid": "[parameters('aad_admin_objectid')]",
                    "tenantId": "[parameters('aad_admin_tenantid')]",
                    "principalType": "[parameters('aad_admin_type')]",
                    "azureADOnlyAuthentication": "[parameters('aad_only_auth')]"
                }
            }
        }
    ]
}
```

---

## <a name="azure-sql-managed-instance"></a>Azure SQL Managed Instance

# <a name="portal"></a>[Portail](#tab/azure-portal)

1. Accédez à la page [Sélectionner l’option de déploiement SQL](https://portal.azure.com/#create/Microsoft.AzureSQL) dans le portail Azure.

1. Si vous n’êtes pas déjà connecté au portail Azure, connectez-vous lorsque vous y êtes invité.

1. Sous **Instances SQL managées**, laissez **Type de ressource** défini sur **Instance unique**, puis sélectionnez **Créer**.

1. Renseignez les informations obligatoires requises sous l’onglet **Informations de base** pour **Détails du projet** et **Détails de l’instance gérée**. Il s’agit d’informations de base qui sont obligatoires pour provisionner une instance managée SQL.

   :::image type="content" source="media/authentication-azure-ad-only-authentication/azure-ad-only-managed-instance-create-basic.png" alt-text="Capture d’écran du Portail Azure montrant l’onglet de base Créer une instance gérée":::

   Pour plus d’informations sur les options de configuration, consultez [Démarrage rapide : Créer une instance gérée Azure SQL Managed Instance](../managed-instance/instance-create-quickstart.md).

1. Sous **Authentification**, sélectionnez **Utiliser uniquement l’authentification Azure Active Directory (Azure AD)** comme **méthode d’authentification**.

1. Sélectionnez **Définir l’administrateur**, ce qui affiche un menu pour sélectionner un principal Azure AD comme administrateur Azure AD d’instance gérée. Lorsque vous avez terminé, utilisez le bouton **Sélectionner** pour définir votre administrateur.

   :::image type="content" source="media/authentication-azure-ad-only-authentication/azure-ad-only-managed-instance-create-basic-choose-authentication.png" alt-text="Capture d’écran du Portail Azure montrant l’onglet de base Créer une instance gérée et la sélection de l’authentification Azure AD uniquement":::

1. Vous pouvez laisser le reste des paramètres par défaut. Pour plus d’informations sur la **Mise en réseau**, la **Sécurité** ou d’autres onglets et paramètres, suivez le guide de [Démarrage rapide : Créer une instance gérée Azure SQL](../managed-instance/instance-create-quickstart.md).

1. Une fois que vous avez fini de configurer vos paramètres, sélectionnez **Vérifier + créer** pour continuer. Sélectionnez **Créer** pour commencer le provisionnement de l’instance managée.

# <a name="the-azure-cli"></a>[L’interface de ligne de commande Microsoft Azure](#tab/azure-cli)

La commande Azure CLI `az sql mi create` permet de configurer une nouvelle instance Azure SQL Managed Instance. La commande ci-dessous met en service une nouvelle instance gérée avec l’authentification Azure AD uniquement activée.

> [!NOTE]
> Le script requiert la création d’un réseau virtuel et d’un sous-réseau comme condition préalable.

La connexion de l’administrateur SQL de l’instance gérée est automatiquement créée et le mot de passe est défini sur un mot de passe aléatoire. Étant donné que la connectivité de l’authentification SQL est désactivée lors de cet approvisionnement, la connexion administrateur SQL ne sera pas utilisée.

L’administrateur Azure AD est le compte que vous définissez pour `<AzureADAccount>` et peut être utilisé pour gérer l’instance lorsque l’approvisionnement est terminé.

Remplacez les valeurs suivantes dans l’exemple :

- `<AzureADAccount>` : Peut être un utilisateur ou un groupe Azure AD. Par exemple, `DummyLogin`
- `<AzureADAccountSID>` : L’ID d’objet Azure AD pour l’utilisateur
- `<managedinstancename>` : Nommer l’instance gérée que vous souhaitez créer
- `<ResourceGroupName>` : Nom du groupe de ressources de votre instance managée Le groupe de ressources doit également inclure le réseau virtuel et le sous-réseau créés.
- Le paramètre `subnet` doit être mis à jour avec les paramètres `<Subscription ID>`, `<ResourceGroupName>`, `<VNetName>` et `<SubnetName>`. Votre ID d’abonnement est disponible dans le portail Azure.

```azurecli
az sql mi create --enable-ad-only-auth --external-admin-principal-type User --external-admin-name <AzureADAccount> --external-admin-sid <AzureADAccountSID> -g <ResourceGroupName> -n <managedinstancename> --subnet /subscriptions/<Subscription ID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/virtualNetworks/<VNetName>/subnets/<SubnetName>
```

Pour plus d’informations, voir [az sql mi create](/cli/azure/sql/mi#az_sql_mi_create).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

La commande PowerShell `New-AzSqlInstance` permet de configurer une nouvelle instance Azure SQL Managed Instance. La commande ci-dessous met en service une nouvelle instance gérée avec l’authentification Azure AD uniquement activée.

> [!NOTE]
> Le script requiert la création d’un réseau virtuel et d’un sous-réseau comme condition préalable.

La connexion de l’administrateur SQL de l’instance gérée est automatiquement créée et le mot de passe est défini sur un mot de passe aléatoire. Étant donné que la connectivité de l’authentification SQL est désactivée lors de cet approvisionnement, la connexion administrateur SQL ne sera pas utilisée.

L’administrateur Azure AD est le compte que vous définissez pour `<AzureADAccount>` et peut être utilisé pour gérer l’instance lorsque l’approvisionnement est terminé.

Remplacez les valeurs suivantes dans l’exemple :

- `<managedinstancename>` : Nommer l’instance gérée que vous souhaitez créer
- `<ResourceGroupName>` : Nom du groupe de ressources de votre instance managée Le groupe de ressources doit également inclure le réseau virtuel et le sous-réseau créés.
- `<Location>` : emplacement du serveur, tel que `West US` ou `Central US`
- `<AzureADAccount>` : Peut être un utilisateur ou un groupe Azure AD. Par exemple, `DummyLogin`
- Le paramètre `SubnetId` doit être mis à jour avec les paramètres `<Subscription ID>`, `<ResourceGroupName>`, `<VNetName>` et `<SubnetName>`. Votre ID d’abonnement est disponible dans le portail Azure.


```powershell
New-AzSqlInstance -Name "<managedinstancename>" -ResourceGroupName "<ResourceGroupName>" -ExternalAdminName "<AzureADAccount>" -EnableActiveDirectoryOnlyAuthentication -Location "<Location>" -SubnetId "/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/virtualNetworks/<VNetName>/subnets/<SubnetName>" -LicenseType LicenseIncluded -StorageSizeInGB 1024 -VCore 16 -Edition "GeneralPurpose" -ComputeGeneration Gen4
```

Pour plus d’informations, consultez [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance).

# <a name="rest-api"></a>[API REST](#tab/rest-api)

L’API Rest [Instances gérées - Créer ou mettre à jour](/rest/api/sql/2020-11-01-preview/managed-instances/create-or-update) peut être utilisée pour créer une instance gérée avec l’authentification Azure AD uniquement activée pendant la configuration.

> [!NOTE]
> Le script requiert la création d’un réseau virtuel et d’un sous-réseau comme condition préalable.

Le script ci-dessous configure une instance gérée, définit l’administrateur Azure AD en tant que `<AzureADAccount>` et active l’authentification Azure AD uniquement. La connexion de l’administrateur SQL de l’instance est aussi automatiquement créée et le mot de passe est défini sur un mot de passe aléatoire. Étant donné que la connectivité de l’authentification SQL est désactivée lors de cet approvisionnement, la connexion administrateur SQL ne sera pas utilisée.

L’administrateur Azure AD `<AzureADAccount>` peut être utilisé pour gérer l’instance lorsque l’approvisionnement est terminé.

Remplacez les valeurs suivantes dans l’exemple :

- `<tenantId>` : accédez à votre ressource **Azure Active Directory** dans le [portail Azure](https://portal.azure.com). Dans le volet **Vue d’ensemble**, vous devez voir votre **ID de locataire**.
- `<subscriptionId>` : votre ID d’abonnement, disponible dans le Portail Azure
- `<instanceName>` : Utiliser un nom d’instance gérée unique
- `<ResourceGroupName>` : nom du groupe de ressources pour votre serveur logique
- `<AzureADAccount>` : Peut être un utilisateur ou un groupe Azure AD. Par exemple, `DummyLogin`
- `<Location>` : emplacement du serveur, tel que `westus2` ou `centralus`
- `<objectId>` : accédez à votre ressource **Azure Active Directory** dans le [portail Azure](https://portal.azure.com). Dans le volet **utilisateur**, recherchez l’utilisateur Azure AD et recherchez son **ID d’objet**.
- Le paramètre `subnetId` doit être mis à jour avec les paramètres `<ResourceGroupName>`, `Subscription ID`, `<VNetName>` et `<SubnetName>`.


```rest
Import-Module Azure
Import-Module MSAL.PS

$tenantId = '<tenantId>'
$clientId = '1950a258-227b-4e31-a9cf-717495945fc2' # Static Microsoft client ID used for getting a token
$subscriptionId = '<subscriptionId>'
$uri = "urn:ietf:wg:oauth:2.0:oob" 
$instanceName = "<instanceName>"
$resourceGroupName = "<ResourceGroupName>"
$scopes ="https://management.core.windows.net/.default"

Login-AzAccount -tenantId $tenantId

# Login as an Azure AD user with permission to provision a managed instance

$result = Get-MsalToken -RedirectUri $uri -ClientId $clientId -TenantId $tenantId -Scopes $scopes

$authHeader = @{
'Content-Type'='application\json; '
'Authorization'=$result.CreateAuthorizationHeader()
}

$body = '{
"name": "<instanceName>", "type": "Microsoft.Sql/managedInstances", "identity": { "type": "SystemAssigned"},"location": "<Location>", "sku": {"name": "GP_Gen5", "tier": "GeneralPurpose", "family":"Gen5","capacity": 8},
"properties": {"administrators":{ "login":"<AzureADAccount>", "sid":"<objectId>", "tenantId":"<tenantId>", "principalType":"User", "azureADOnlyAuthentication":true },
"subnetId": "/subscriptions/<subscriptionId>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/virtualNetworks/<VNetName>/subnets/<SubnetName>",
"licenseType": "LicenseIncluded", "vCores": 8, "storageSizeInGB": 2048, "collation": "SQL_Latin1_General_CP1_CI_AS", "proxyOverride": "Proxy", "timezoneId": "UTC", "privateEndpointConnections": [], "storageAccountType": "GRS", "zoneRedundant": false 
  }
}'

# To provision the instance, execute the `PUT` command

Invoke-RestMethod -Uri https://management.azure.com/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Sql/managedInstances/$instanceName/?api-version=2020-11-01-preview -Method PUT -Headers $authHeader -Body $body -ContentType "application/json"

```

Pour vérifier les résultats, exécutez la commande `GET` :

```rest
Invoke-RestMethod -Uri https://management.azure.com/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Sql/managedInstances/$instanceName/?api-version=2020-11-01-preview -Method GET -Headers $authHeader  | Format-List
```

# <a name="arm-template"></a>[Modèle ARM](#tab/arm-template)

Pour approvisionner une nouvelle instance gérée, un réseau virtuel et un sous-réseau, avec un groupe d’administration Azure AD pour l’instance et l’authentification Azure AD uniquement activée, utilisez le modèle suivant.

Utilisez un [déploiement personnalisé dans le portail Azure](https://portal.azure.com/#create/Microsoft.Template) et **créez votre propre modèle dans l’éditeur**. Ensuite, **enregistrez** la configuration une fois que vous avez collé l’exemple.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.1",
    "parameters": {
        "managedInstanceName": {
            "type": "String",
            "metadata": {
                "description": "Enter managed instance name."
            }
        },
        "aad_admin_name": {
            "type": "String",
            "metadata": {
                "description": "The name of the Azure AD admin for the SQL managed instance."
            }
        },
        "aad_admin_objectid": {
            "type": "String",
            "metadata": {
                "description": "The Object ID of the Azure AD admin."
            }
        },
        "aad_admin_tenantid": {
            "type": "String",
            "defaultValue": "[subscription().tenantId]",
            "metadata": {
                "description": "The Tenant ID of the Azure Active Directory"
            }
        },
        "aad_admin_type": {
            "defaultValue": "User",
            "allowedValues": [
                "User",
                "Group",
                "Application"
            ],
            "type": "String"
        },
        "aad_only_auth": {
            "defaultValue": true,
            "type": "Bool"
        },
        "location": {
            "defaultValue": "[resourceGroup().location]",
            "type": "String",
            "metadata": {
                "description": "Enter location. If you leave this field blank resource group location would be used."
            }
        },
        "virtualNetworkName": {
            "type": "String",
            "defaultValue": "SQLMI-VNET",
            "metadata": {
                "description": "Enter virtual network name. If you leave this field blank name will be created by the template."
            }
        },
        "addressPrefix": {
            "defaultValue": "10.0.0.0/16",
            "type": "String",
            "metadata": {
                "description": "Enter virtual network address prefix."
            }
        },
        "subnetName": {
            "type": "String",
            "defaultValue": "ManagedInstances",
            "metadata": {
                "description": "Enter subnet name. If you leave this field blank name will be created by the template."
            }
        },
        "subnetPrefix": {
            "defaultValue": "10.0.0.0/24",
            "type": "String",
            "metadata": {
                "description": "Enter subnet address prefix."
            }
        },
        "skuName": {
            "defaultValue": "GP_Gen5",
            "allowedValues": [
                "GP_Gen5",
                "BC_Gen5"
            ],
            "type": "String",
            "metadata": {
                "description": "Enter sku name."
            }
        },
        "vCores": {
            "defaultValue": 16,
            "allowedValues": [
                8,
                16,
                24,
                32,
                40,
                64,
                80
            ],
            "type": "Int",
            "metadata": {
                "description": "Enter number of vCores."
            }
        },
        "storageSizeInGB": {
            "defaultValue": 256,
            "minValue": 32,
            "maxValue": 8192,
            "type": "Int",
            "metadata": {
                "description": "Enter storage size."
            }
        },
        "licenseType": {
            "defaultValue": "LicenseIncluded",
            "allowedValues": [
                "BasePrice",
                "LicenseIncluded"
            ],
            "type": "String",
            "metadata": {
                "description": "Enter license type."
            }
        }
    },
    "variables": {
        "networkSecurityGroupName": "[concat('SQLMI-', parameters('managedInstanceName'), '-NSG')]",
        "routeTableName": "[concat('SQLMI-', parameters('managedInstanceName'), '-Route-Table')]"
    },
    "resources": [
        {
            "type": "Microsoft.Network/networkSecurityGroups",
            "apiVersion": "2020-06-01",
            "name": "[variables('networkSecurityGroupName')]",
            "location": "[parameters('location')]",
            "properties": {
                "securityRules": [
                    {
                        "name": "allow_tds_inbound",
                        "properties": {
                            "description": "Allow access to data",
                            "protocol": "Tcp",
                            "sourcePortRange": "*",
                            "destinationPortRange": "1433",
                            "sourceAddressPrefix": "VirtualNetwork",
                            "destinationAddressPrefix": "*",
                            "access": "Allow",
                            "priority": 1000,
                            "direction": "Inbound"
                        }
                    },
                    {
                        "name": "allow_redirect_inbound",
                        "properties": {
                            "description": "Allow inbound redirect traffic to Managed Instance inside the virtual network",
                            "protocol": "Tcp",
                            "sourcePortRange": "*",
                            "destinationPortRange": "11000-11999",
                            "sourceAddressPrefix": "VirtualNetwork",
                            "destinationAddressPrefix": "*",
                            "access": "Allow",
                            "priority": 1100,
                            "direction": "Inbound"
                        }
                    },
                    {
                        "name": "deny_all_inbound",
                        "properties": {
                            "description": "Deny all other inbound traffic",
                            "protocol": "*",
                            "sourcePortRange": "*",
                            "destinationPortRange": "*",
                            "sourceAddressPrefix": "*",
                            "destinationAddressPrefix": "*",
                            "access": "Deny",
                            "priority": 4096,
                            "direction": "Inbound"
                        }
                    },
                    {
                        "name": "deny_all_outbound",
                        "properties": {
                            "description": "Deny all other outbound traffic",
                            "protocol": "*",
                            "sourcePortRange": "*",
                            "destinationPortRange": "*",
                            "sourceAddressPrefix": "*",
                            "destinationAddressPrefix": "*",
                            "access": "Deny",
                            "priority": 4096,
                            "direction": "Outbound"
                        }
                    }
                ]
            }
        },
        {
            "type": "Microsoft.Network/routeTables",
            "apiVersion": "2020-06-01",
            "name": "[variables('routeTableName')]",
            "location": "[parameters('location')]",
            "properties": {
                "disableBgpRoutePropagation": false
            }
        },
        {
            "type": "Microsoft.Network/virtualNetworks",
            "apiVersion": "2020-06-01",
            "name": "[parameters('virtualNetworkName')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[variables('routeTableName')]",
                "[variables('networkSecurityGroupName')]"
            ],
            "properties": {
                "addressSpace": {
                    "addressPrefixes": [
                        "[parameters('addressPrefix')]"
                    ]
                },
                "subnets": [
                    {
                        "name": "[parameters('subnetName')]",
                        "properties": {
                            "addressPrefix": "[parameters('subnetPrefix')]",
                            "routeTable": {
                                "id": "[resourceId('Microsoft.Network/routeTables', variables('routeTableName'))]"
                            },
                            "networkSecurityGroup": {
                                "id": "[resourceId('Microsoft.Network/networkSecurityGroups', variables('networkSecurityGroupName'))]"
                            },
                            "delegations": [
                                {
                                    "name": "miDelegation",
                                    "properties": {
                                        "serviceName": "Microsoft.Sql/managedInstances"
                                    }
                                }
                            ]
                        }
                    }
                ]
            }
        },
        {
            "type": "Microsoft.Sql/managedInstances",
            "apiVersion": "2020-11-01-preview",
            "name": "[parameters('managedInstanceName')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[parameters('virtualNetworkName')]"
            ],
            "sku": {
                "name": "[parameters('skuName')]"
            },
            "identity": {
                "type": "SystemAssigned"
            },
            "properties": {
                "subnetId": "[resourceId('Microsoft.Network/virtualNetworks/subnets', parameters('virtualNetworkName'), parameters('subnetName'))]",
                "storageSizeInGB": "[parameters('storageSizeInGB')]",
                "vCores": "[parameters('vCores')]",
                "licenseType": "[parameters('licenseType')]",
                "administrators": {
                    "login": "[parameters('aad_admin_name')]",
                    "sid": "[parameters('aad_admin_objectid')]",
                    "tenantId": "[parameters('aad_admin_tenantid')]",
                    "principalType": "[parameters('aad_admin_type')]",
                    "azureADOnlyAuthentication": "[parameters('aad_only_auth')]"
                }
            }
        }
    ]
}
```

---

### <a name="grant-directory-readers-permissions"></a>Accorder des autorisations de lecteur de répertoire

Une fois le déploiement terminé pour votre instance gérée, vous pouvez remarquer que le Managed Instance a besoin d’autorisations de **lecture** pour accéder à Azure Active Directory. Les autorisations de lecture peuvent être accordées par une personne disposant de privilèges suffisants en cliquant sur le message affiché dans le portail Azure. Pour plus d’informations, consultez [Rôle Lecteurs d’annuaires dans Azure Active Directory pour Azure SQL](authentication-aad-directory-readers-role.md).

:::image type="content" source="media/authentication-azure-ad-only-authentication/azure-ad-portal-read-permissions.png" alt-text="capture d’écran du menu d’administration Active Directory dans Portail Azure montrant les autorisations de lecture nécessaires":::

## <a name="limitations"></a>Limites

- Pour réinitialiser le mot de passe de l’administrateur du serveur, l’authentification Azure AD uniquement doit être désactivée.
- Si l’authentification Azure AD uniquement est désactivée, vous devez créer un serveur avec un mot de passe et un administrateur de serveur lors de l’utilisation de toutes les API.

## <a name="next-steps"></a>Étapes suivantes

- Si vous disposez déjà d’un serveur SQL ou d’une instance gérée et que vous souhaitez simplement activer l’authentification Azure AD uniquement, consultez [Didacticiel : activer l’authentification Azure Active Directory uniquement avec Azure SQL](authentication-azure-ad-only-authentication-tutorial.md).
- Pour plus d’informations sur la fonctionnalité d’authentification Azure AD uniquement, consultez [Authentification Azure ad uniquement avec Azure SQL](authentication-azure-ad-only-authentication.md).
- Si vous souhaitez appliquer la création du serveur avec l’authentification Azure AD uniquement activée, consultez [Azure Policy pour l’authentification Azure Active Directory uniquement pour Azure SQL](authentication-azure-ad-only-authentication-policy.md)
