---
title: Authentification Azure Active Directory uniquement
description: Cet article fournit des informations sur la fonctionnalité d’authentification Azure AD (Azure Active Directory) uniquement avec Azure SQL Database et Azure SQL Managed Instance.
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
ms.service: sql-db-mi
ms.subservice: security
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 11/03/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 050a9690f22ef34b39d89a5cd0cf4d8a6c7d340b
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131503050"
---
# <a name="azure-ad-only-authentication-with-azure-sql"></a>Authentification Azure AD uniquement avec Azure SQL

[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

L’authentification avec Azure AD uniquement est une fonction dans [Azure SQL](../azure-sql-iaas-vs-paas-what-is-overview.md) qui permet au service de ne prendre en charge que l’authentification Azure AD. Elle est prise en charge pour [Azure SQL Database](sql-database-paas-overview.md) et [Azure SQL Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md). L’authentification SQL est désactivée lors de l’activation de l’authentification Azure AD uniquement dans l’environnement Azure SQL, y compris les connexions des administrateurs de serveur SQL, les connexions et les utilisateurs du serveur. Seuls les utilisateurs se servant de l’[authentification Azure AD](authentication-aad-overview.md) sont autorisés à se connecter au serveur ou à la base de données.

L’authentification Azure AD uniquement peut être activée ou désactivée via le portail Azure, Azure CLI, PowerShell ou l’API REST. L’authentification Azure AD uniquement peut également être configurée lors de la création du serveur avec un modèle ARM.

Pour plus d’informations sur l’authentification Azure SQL, consultez [Authentification et autorisation](logins-create-manage.md#authentication-and-authorization).

## <a name="feature-description"></a>Description de la fonctionnalité

Lors de l’activation de l’authentification Azure AD uniquement, l’[authentification SQL](logins-create-manage.md#authentication-and-authorization) est désactivée au niveau du serveur ou de l’instance managée et empêche toute authentification basée sur les informations d’identification d’authentification SQL. Les utilisateurs de l’authentification SQL ne pourront pas se connecter au [serveur logique](logical-servers.md) pour Azure SQL Database ni à une instance managée, y compris toutes ses bases de données. Bien que l’authentification SQL soit désactivée, de nouvelles connexions et utilisateurs d’authentification SQL peuvent toujours être créés par les comptes Azure AD disposant des autorisations appropriées. Les comptes d’authentification SQL récemment créés ne sont pas autorisés à se connecter au serveur. L’activation de l’authentification Azure AD uniquement ne supprime pas les comptes d’utilisateur et de connexion d’authentification SQL existants. La fonctionnalité empêche uniquement ces comptes de se connecter au serveur et à toute base de données créée pour ce serveur.

Vous pouvez également forcer la création de serveurs avec l’authentification Azure AD uniquement activée à l’aide d’Azure Policy. Pour plus d’informations, consultez [Azure Policy pour l’authentification Azure AD uniquement](authentication-azure-ad-only-authentication-policy.md).

## <a name="permissions"></a>Autorisations

L’authentification Azure AD uniquement peut être activée ou désactivée par les utilisateurs Azure AD qui sont membres de [rôles intégrés Azure AD](../../role-based-access-control/built-in-roles.md) à privilège élevé, tels que les [propriétaires](../../role-based-access-control/built-in-roles.md#owner) d’abonnement Azure, les [contributeurs](../../role-based-access-control/built-in-roles.md#contributor) et les [administrateurs globaux](../../active-directory/roles/permissions-reference.md#global-administrator). De plus, le rôle [Responsable de sécurité SQL](../../role-based-access-control/built-in-roles.md#sql-security-manager) peut également activer ou désactiver la fonctionnalité d’authentification Azure AD uniquement.

Les rôles [Contributeur SQL](../../role-based-access-control/built-in-roles.md#sql-server-contributor) et [Contributeur SQL Managed Instance](../../role-based-access-control/built-in-roles.md#sql-managed-instance-contributor) ne seront pas autorisés à activer ou désactiver la fonctionnalité d’authentification Azure AD uniquement. Cela est cohérent avec l’approche de la [séparation des tâches](security-best-practice.md#implement-separation-of-duties), où les utilisateurs qui peuvent créer un serveur Azure SQL ou créer un administrateur Azure AD ne peuvent pas activer ou désactiver les fonctionnalités de sécurité.

### <a name="actions-required"></a>Actions obligatoires

Les actions suivantes sont ajoutées au rôle [gestionnaire de sécurité SQL](../../role-based-access-control/built-in-roles.md#sql-security-manager) pour permettre la gestion de la fonctionnalité d’authentification Azure AD uniquement.

- Microsoft.Sql/servers/azureADOnlyAuthentications/*
- Microsoft.Sql/servers/administrators/read - requis uniquement pour les utilisateurs qui accèdent au menu **Azure Active Directory** du portail Azure
- Microsoft.Sql/managedInstances/azureADOnlyAuthentications/*
- Microsoft.Sql/managedInstances/read

Les actions ci-dessus peuvent également être ajoutées à un rôle personnalisé pour gérer l’authentification Azure AD uniquement. Pour plus d’informations, consultez [Création et attribution d’un rôle personnalisé dans Azure Active Directory](../../active-directory/roles/custom-create.md).

## <a name="managing-azure-ad-only-authentication-using-apis"></a>Gestion de l’authentification Azure AD uniquement à l’aide d’API

> [!IMPORTANT]
> L’administrateur Azure AD doit être défini avant l’activation de l’authentification Azure AD uniquement.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Vous devez disposer d’Azure CLI **2.14.2** ou version ultérieure.

`name` correspond au préfixe du nom du serveur ou de l’instance (par exemple, `myserver`) et `resource-group` correspond à la ressource à laquelle le serveur appartient (par exemple, `myresource`).

## <a name="azure-sql-database"></a>Azure SQL Database

Pour plus d’informations, voir [az sql server ad-only-auth](/cli/azure/sql/server/ad-only-auth).

### <a name="enable-or-disable-in-sql-database"></a>Activer ou désactiver la base de données SQL

**Activer**

```azurecli
az sql server ad-only-auth enable --resource-group myresource --name myserver
```

**Disable**

```azurecli
az sql server ad-only-auth disable --resource-group myresource --name myserver
```

### <a name="check-the-status-in-sql-database"></a>Vérifier l’état dans SQL Database

```azurecli
az sql server ad-only-auth get --resource-group myresource --name myserver
```

## <a name="azure-sql-managed-instance"></a>Azure SQL Managed Instance

Pour plus d’informations, voir [az sql mi ad-only-auth](/cli/azure/sql/mi/ad-only-auth).

**Activer**

```azurecli
az sql mi ad-only-auth enable --resource-group myresource --name myserver
```

**Disable**

```azurecli
az sql mi ad-only-auth disable --resource-group myresource --name myserver
```

### <a name="check-the-status-in-sql-managed-instance"></a>Vérifier l’état dans SQL Managed Instance

```azurecli
az sql mi ad-only-auth get --resource-group myresource --name myserver
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Le module [Az.Sql 2.10.0](https://www.powershellgallery.com/packages/Az.Sql/2.10.0) ou une version ultérieure est requis.

`ServerName` ou `InstanceName` correspond au préfixe du nom du serveur (par exemple, `myserver` ou `myinstance`) et `ResourceGroupName` correspond à la ressource à laquelle le serveur appartient (par exemple, `myresource`).

## <a name="azure-sql-database"></a>Azure SQL Database

### <a name="enable-or-disable-in-sql-database"></a>Activer ou désactiver la base de données SQL

**Activer**

Pour plus d’informations, voir [Enable-AzSqlServerActiveDirectoryOnlyAuthentication](/powershell/module/az.sql/enable-azsqlserveractivedirectoryonlyauthentication). Vous pouvez également exécuter `get-help Enable-AzSqlServerActiveDirectoryOnlyAuthentication -full`.

```powershell
Enable-AzSqlServerActiveDirectoryOnlyAuthentication -ServerName myserver -ResourceGroupName myresource
```

Vous pouvez également utiliser la commande suivante :

```powershell
Get-AzSqlServer -ServerName myserver | Enable-AzSqlServerActiveDirectoryOnlyAuthentication
```

**Désactiver**

Pour plus d’informations, voir [Disable-AzSqlServerActiveDirectoryOnlyAuthentication](/powershell/module/az.sql/disable-azsqlserveractivedirectoryonlyauthentication).

```powershell
Disable-AzSqlServerActiveDirectoryOnlyAuthentication -ServerName myserver -ResourceGroupName myresource
```

### <a name="check-the-status-in-sql-database"></a>Vérifier l’état dans SQL Database

```powershell
Get-AzSqlServerActiveDirectoryOnlyAuthentication  -ServerName myserver -ResourceGroupName myresource
```

Vous pouvez également utiliser la commande suivante :

```powershell
Get-AzSqlServer -ServerName myserver | Get-AzSqlServerActiveDirectoryOnlyAuthentication
```

## <a name="azure-sql-managed-instance"></a>Azure SQL Managed Instance

### <a name="enable-or-disable-in-sql-managed-instance"></a>Activer ou désactiver dans SQL Managed Instance

**Activer**

Pour plus d’informations, consultez [Enable-AzSqlInstanceActiveDirectoryOnlyAuthentication](/powershell/module/az.sql/enable-azsqlinstanceactivedirectoryonlyauthentication).

```powershell
Enable-AzSqlInstanceActiveDirectoryOnlyAuthentication -InstanceName myinstance -ResourceGroupName myresource
```

Vous pouvez également utiliser la commande suivante :

```powershell
Get-AzSqlInstance -InstanceName myinstance | Enable-AzSqlInstanceActiveDirectoryOnlyAuthentication
```

Pour plus d’informations sur ces commandes PowerShell, exécutez `get-help  Enable-AzSqlInstanceActiveDirectoryOnlyAuthentication -full`.

**Désactiver**

Pour plus d’informations, consultez [Disable-AzSqlInstanceActiveDirectoryOnlyAuthentication](/powershell/module/az.sql/disable-azsqlinstanceactivedirectoryonlyauthentication).

```powershell
Disable-AzSqlInstanceActiveDirectoryOnlyAuthentication -InstanceName myinstance -ResourceGroupName myresource
```

### <a name="check-the-status-in-sql-managed-instance"></a>Vérifier l’état dans SQL Managed Instance

```powershell
Get-AzSqlInstanceActiveDirectoryOnlyAuthentication -InstanceName myinstance -ResourceGroupName myresource
```

Vous pouvez également utiliser la commande suivante :

```powershell
Get-AzSqlInstance -InstanceName myinstance | Get-AzSqlInstanceActiveDirectoryOnlyAuthentication
```

# <a name="rest-api"></a>[REST API](#tab/rest-api)

Les paramètres suivants doivent être spécifiés :

- `<subscriptionId>` est accessible dans **Abonnement** sur le portail Azure.
- `<myserver>` correspond au préfixe du nom du serveur ou de l’instance (par exemple, `myserver`).
- `<myresource>` correspond à la ressource à laquelle le serveur appartient (par exemple, `myresource`)

Pour utiliser la dernière version de MSAL, téléchargez-la à partir de https://www.powershellgallery.com/packages/MSAL.PS.

```rest
$subscriptionId = '<subscriptionId>'
$serverName = "<myserver>"
$resourceGroupName = "<myresource>"
```

## <a name="azure-sql-database"></a>Azure SQL Database

Pour plus d’informations, consultez la documentation de l’API REST, [Authentifications Server Azure AD uniquement](/rest/api/sql/2021-02-01-preview/serverazureadonlyauthentications).

### <a name="enable-or-disable-in-sql-database"></a>Activer ou désactiver la base de données SQL

**Activer**

```rest
$body = @{ properties = @{ azureADOnlyAuthentication = 1 } } | ConvertTo-Json
Invoke-RestMethod -Uri "https://management.azure.com/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Sql/servers/$serverName/azureADOnlyAuthentications/default?api-version=2020-02-02-preview" -Method PUT -Headers $authHeader -Body $body -ContentType "application/json"
```

**Disable**

```rest
$body = @{ properties = @{ azureADOnlyAuthentication = 0 } } | ConvertTo-Json
Invoke-RestMethod -Uri "https://management.azure.com/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Sql/servers/$serverName/azureADOnlyAuthentications/default?api-version=2020-02-02-preview" -Method PUT -Headers $authHeader -Body $body -ContentType "application/json"
```

### <a name="check-the-status-in-sql-database"></a>Vérifier l’état dans SQL Database

```rest
Invoke-RestMethod -Uri "https://management.azure.com/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Sql/servers/$serverName/azureADOnlyAuthentications/default?api-version=2020-02-02-preview" -Method GET -Headers $authHeader  | Format-List
```

## <a name="azure-sql-managed-instance"></a>Azure SQL Managed Instance

Pour plus d’informations, consultez la documentation de l’API REST, [Authentifications Managed Instance Azure AD uniquement](/rest/api/sql/2021-02-01-preview/managedinstanceazureadonlyauthentications).

### <a name="enable-or-disable-in-sql-managed-instance"></a>Activer ou désactiver dans SQL Managed Instance

**Activer**

```rest
$body = @{   properties = @{  azureADOnlyAuthentication = 1 }  } | ConvertTo-Json 
Invoke-RestMethod -Uri "https://management.azure.com/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Sql/managedInstances/$serverName/azureADOnlyAuthentications/default?api-version=2020-02-02-preview" -Method PUT -Headers $authHeader -Body $body -ContentType "application/json"
```

**Disable**

```rest
$body = @{   properties = @{  azureADOnlyAuthentication = 0 }  } | ConvertTo-Json 
Invoke-RestMethod -Uri "https://management.azure.com/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Sql/managedInstances/$serverName/azureADOnlyAuthentications/default?api-version=2020-02-02-preview" -Method PUT -Headers $authHeader -Body $body -ContentType "application/json"
```

### <a name="check-the-status-in-sql-managed-instance"></a>Vérifier l’état dans SQL Managed Instance

```rest
Invoke-RestMethod -Uri "https://management.azure.com/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Sql/managedInstances/$serverName/azureADOnlyAuthentications/default?api-version=2020-02-02-preview" -Method GET -Headers $authHeader  | Format-List
```

# <a name="arm-template"></a>[Modèle ARM](#tab/arm)

- Spécifiez l’administrateur Azure AD pour le déploiement. Pour trouver l’ID d’objet utilisateur, accédez au [portail Azure](https://portal.azure.com) puis à votre ressource **Azure Active Directory**. Sous **Gérer**, sélectionnez **Utilisateurs**. Recherchez l’utilisateur que vous souhaitez définir en tant qu’administrateur Azure AD de votre serveur Azure SQL. Sélectionnez l’utilisateur et, sur sa page **Profil**, vous verrez l’**ID d’objet**.
- L’ID de locataire figure sur la page **Vue d’ensemble** de votre ressource **Azure Active Directory**.

## <a name="azure-sql-database"></a>Azure SQL Database

### <a name="enable"></a>Activer

Le modèle ARM ci-dessous active l’authentification Azure AD uniquement dans votre instance Azure SQL Database. Pour désactiver l’authentification Azure AD uniquement, définissez la propriété `azureADOnlyAuthentication` sur `false`.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.1",
    "parameters": {
        "sqlServer_name": {
            "type": "String"
        },
        "aad_admin_name": {
            "type": "String"
        },
        "aad_admin_objectid": {
            "type": "String"
        },
        "aad_admin_tenantid": {
            "type": "String"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Sql/servers/administrators",
            "apiVersion": "2020-02-02-preview",
            "name": "[concat(parameters('sqlServer_name'), '/ActiveDirectory')]",
            "properties": {
                "administratorType": "ActiveDirectory",
                "login": "[parameters('aad_admin_name')]",
                "sid": "[parameters('aad_admin_objectid')]",
                "tenantId": "[parameters('aad_admin_tenantId')]"
            }
        },        
        {
            "type": "Microsoft.Sql/servers/azureADOnlyAuthentications",
            "apiVersion": "2020-02-02-preview",
            "name": "[concat(parameters('sqlServer_name'), '/Default')]",
            "dependsOn": [
                "[resourceId('Microsoft.Sql/servers/administrators', parameters('sqlServer_name'), 'ActiveDirectory')]"
            ],
            "properties": {
                "azureADOnlyAuthentication": true
            }
        }
    ]
}
```

Pour plus d’informations, voir [Microsoft.Sql servers/azureADOnlyAuthentications](/azure/templates/microsoft.sql/servers/azureadonlyauthentications).

## <a name="azure-sql-managed-instance"></a>Azure SQL Managed Instance

### <a name="enable"></a>Activer

Le modèle ARM ci-dessous active l’authentification Azure AD uniquement dans votre instance Azure SQL Managed Instance. Pour désactiver l’authentification Azure AD uniquement, définissez la propriété `azureADOnlyAuthentication` sur `false`.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.1",
    "parameters": {
        "instance": {
            "type": "String"
        },
        "aad_admin_name": {
            "type": "String"
        },
        "aad_admin_objectid": {
            "type": "String"
        },
        "aad_admin_tenantid": {
            "type": "String"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Sql/managedInstances/administrators",
            "apiVersion": "2020-02-02-preview",
            "name": "[concat(parameters('instance'), '/ActiveDirectory')]",
            "properties": {
                "administratorType": "ActiveDirectory",
                "login": "[parameters('aad_admin_name')]",
                "sid": "[parameters('aad_admin_objectid')]",
                "tenantId": "[parameters('aad_admin_tenantId')]"
            }
        },
        {
            "type": "Microsoft.Sql/managedInstances/azureADOnlyAuthentications",
            "apiVersion": "2020-02-02-preview",
            "name": "[concat(parameters('instance'), '/Default')]",
            "dependsOn": [
                "[resourceId('Microsoft.Sql/managedInstances/administrators', parameters('instance'), 'ActiveDirectory')]"
            ],
            "properties": {
                "azureADOnlyAuthentication": true
            }
        }
    ]
}

```

Pour plus d’informations, voir [Microsoft.Sql managedInstances/azureADOnlyAuthentications](/azure/templates/microsoft.sql/managedinstances/azureadonlyauthentications).

---

### <a name="checking-azure-ad-only-authentication-using-t-sql"></a>Vérification de l’authentification Azure AD uniquement à l’aide de T-SQL

La propriété [SEVERPROPERTY](/sql/t-sql/functions/serverproperty-transact-sql) `IsExternalAuthenticationOnly` a été ajoutée pour vérifier si l’authentification Azure AD uniquement est activée pour votre serveur ou instance gérée. `1` indique que la fonctionnalité est activée et `0` qu’elle est désactivée.

```sql
SELECT SERVERPROPERTY('IsExternalAuthenticationOnly') 
```

## <a name="remarks"></a>Remarques

- Un [contributeur SQL Server](../../role-based-access-control/built-in-roles.md#sql-server-contributor) peut définir ou supprimer un administrateur Azure AD mais ne peut pas définir le paramètre **Authentification Azure Active Directory uniquement**. Un [responsable de la sécurité SQL](../../role-based-access-control/built-in-roles.md#sql-security-manager) ne peut pas définir ou supprimer un administrateur Azure AD mais peut définir le paramètre **Authentification Azure Active Directory uniquement**. Seuls les comptes ayant des rôles Azure RBAC ou des rôles personnalisés plus élevés qui contiennent des autorisations peuvent définir ou supprimer un administrateur Azure AD et définir le paramètre **Authentification Azure Active Directory uniquement**. Le rôle [Contributeur](../../role-based-access-control/built-in-roles.md#contributor) est l’un de ces rôles.
- Après l’activation ou la désactivation de la fonctionnalité **Authentification Azure Active Directory uniquement** sur le portail Azure, une entrée **Journal d’activité** apparaît dans le menu **SQL server**.
    :::image type="content" source="media/authentication-azure-ad-only-authentication/azure-ad-only-authentication-portal-sql-server-activity-log.png" alt-text="Entrée du journal d’activité dans le portail Azure":::
- Le paramètre **Authentification Azure Active Directory uniquement** ne peut être activé ou désactivé que par des utilisateurs disposant des autorisations appropriées si l’**administrateur Azure Active Directory** est spécifié. Si l’administrateur Azure AD n’est pas défini, le paramètre **Authentification Azure Active Directory uniquement** demeure inactif et ne peut pas être activé ou désactivé. L’utilisation d’API pour activer l’authentification Azure AD uniquement échoue également si l’administrateur Azure AD n’a pas été défini.
- Le changement d’administrateur Azure AD lorsque l’authentification Azure AD uniquement est activée est pris en charge pour les utilisateurs disposant des autorisations appropriées.
- Le changement d’administrateur Azure AD et l’activation ou la désactivation de l’authentification Azure AD uniquement sont autorisés sur le portail Azure pour les utilisateurs disposant des autorisations appropriées. Les deux opérations peuvent être effectuées à l’aide de l’option **Enregistrer** sur le portail Azure. L’administrateur Azure AD doit être défini pour activer l’authentification Azure AD uniquement.
- La suppression d’un administrateur Azure AD lorsque la fonctionnalité d’authentification Azure AD uniquement est activée n’est pas prise en charge. L’utilisation d’une API pour supprimer un administrateur Azure AD échoue si l’authentification Azure AD uniquement est activée.
    - Si le paramètre **Authentification Azure Active Directory uniquement** est activé, le bouton **Supprimer admin** est inactif sur le portail Azure.
- La suppression d’un administrateur Azure AD et la désactivation du paramètre **Authentification Azure Active Directory uniquement\** sont autorisées, mais nécessitent l’autorisation utilisateur appropriée pour effectuer les opérations. Les deux opérations peuvent être effectuées à l’aide de l’option **Enregistrer** sur le portail Azure.
- Les utilisateurs Azure AD disposant des autorisations appropriées peuvent emprunter l’identité des utilisateurs SQL existants.
    - L’emprunt d’identité continue de fonctionner entre les utilisateurs de l’authentification SQL même lorsque la fonctionnalité d’authentification Azure AD uniquement est activée.

### <a name="limitations-for-azure-ad-only-authentication-in-sql-database"></a>Limitations relatives à l’authentification Azure AD uniquement dans SQL Database

Quand l’authentification Azure AD uniquement est activée pour SQL Database, les fonctionnalités suivantes ne sont pas prises en charge :

- [Rôles serveur Azure SQL Database](security-server-roles.md)
- [Tâches élastiques](job-automation-overview.md)
- [Synchronisation des données SQL](sql-data-sync-data-sql-server-sql-database.md)
- [Capture des changements de données](/sql/relational-databases/track-changes/about-change-data-capture-sql-server) - Si vous créez une base de données dans Azure SQL Database en tant qu’utilisateur Azure AD et que vous activez la capture des changements de données dessus, un utilisateur SQL n’est pas en mesure de désactiver ou de modifier les artefacts de la capture des changements de données. Toutefois, un autre utilisateur Azure AD peut activer ou désactiver la capture des changements de données sur la même base de données. De même, si vous créez une base de données Azure SQL Database en tant qu’utilisateur SQL, l’activation ou la désactivation de la capture des changements de données en tant qu’utilisateur Azure AD ne fonctionne pas.
- [Réplication transactionnelle](/azure/azure-sql/managed-instance/replication-transactional-overview) - Dans la mesure où l’authentification SQL est nécessaire pour établir la connectivité entre les participants à la réplication, lorsque l’authentification Azure AD uniquement est activée, la réplication transactionnelle n’est pas prise en charge pour SQL Database dans les scénarios où elle est utilisée pour envoyer (push) les modifications apportées à une instance managée Azure SQL, un serveur SQL local ou une instance SQL de machine virtuelle Azure à une base de données dans Azure SQL Database.
- [Insights SQL](/azure/azure-monitor/insights/sql-insights-overview)
- Instruction EXEC AS pour les comptes de membres de groupe Azure AD

### <a name="limitations-for-azure-ad-only-authentication-in-managed-instance"></a>Limitations relatives à l’authentification Azure AD uniquement dans Managed Instance

Quand l’authentification Azure AD uniquement est activée pour Managed Instance, les fonctionnalités suivantes ne sont pas prises en charge :

- [Réplication transactionnelle](/azure/azure-sql/managed-instance/replication-transactional-overview) 
- Les [travaux SQL Agent dans Managed Instance](../managed-instance/job-automation-managed-instance.md) prennent en charge l’authentification Azure AD uniquement. Toutefois, l’utilisateur Azure AD qui est membre d’un groupe Azure AD ayant accès à l’instance managée ne peut pas posséder de travaux SQL Agent.
- [Insights SQL](/azure/azure-monitor/insights/sql-insights-overview)
- Instruction EXEC AS pour les comptes de membres de groupe Azure AD

Pour connaître les autres limitations, consultez [Différences T-SQL entre SQL Server et Azure SQL Managed Instance](../managed-instance/transact-sql-tsql-differences-sql-server.md#logins-and-users).

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Tutoriel : Activer l’authentification Azure Active Directory uniquement avec Azure SQL](authentication-azure-ad-only-authentication-tutorial.md)

> [!div class="nextstepaction"]
> [Créer un serveur avec l’authentification Azure AD uniquement activée dans Azure SQL](authentication-azure-ad-only-authentication-create-server.md)
