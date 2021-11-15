---
title: Activer l’authentification Azure Active Directory uniquement
description: Cet article vous guide tout au long de l’activation de la fonctionnalité d’authentification Azure Active Directory (Azure AD) uniquement avec Azure SQL Database et Azure SQL Managed Instance.
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
ms.service: sql-db-mi
ms.subservice: security
ms.topic: tutorial
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 11/02/2021
ms.openlocfilehash: 09e855391a591e39d31c1eb939bce6c2dc1cc6b2
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131473620"
---
# <a name="tutorial-enable-azure-active-directory-only-authentication-with-azure-sql"></a>Tutoriel : Activer l’authentification Azure Active Directory uniquement avec Azure SQL

[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Cet article vous guide tout au long de l’activation de la fonctionnalité d’[authentification Azure AD uniquement](authentication-azure-ad-only-authentication.md) dans Azure SQL Database et Azure SQL Managed Instance. Si vous cherchez à approvisionner une base de données SQL ou une instance gérée SQL avec l’authentification Azure AD uniquement activée, consultez [Créer un serveur avec l’authentification Azure AD uniquement activée dans Azure SQL](authentication-azure-ad-only-authentication-create-server.md).

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> - Affecter un rôle pour activer l’authentification Azure AD uniquement
> - Activer l’authentification Azure AD uniquement à l’aide du Portail Azure, d’Azure CLI ou de PowerShell
> - Vérifier si l’authentification Azure AD uniquement est activée
> - Tester la connexion à Azure SQL
> - Désactiver l’authentification Azure AD uniquement à l’aide du Portail Azure, d’Azure CLI ou de PowerShell


## <a name="prerequisites"></a>Prérequis

- Une instance Azure AD. Pour plus d’informations, consultez [Configurer et gérer l’authentification Azure AD avec Azure SQL](authentication-aad-configure.md).
- SQL Database ou SQL Managed Instance avec une base de données, ainsi que des connexions ou des utilisateurs. Consultez [Démarrage rapide : créer une base de données unique Azure SQL Database](single-database-create-quickstart.md) si vous n’avez pas encore créé de base de données Azure SQL Database ou [Démarrage rapide : créer une instance gérée avec SQL Azure Managed Instance](../managed-instance/instance-create-quickstart.md).

## <a name="assign-role-to-enable-azure-ad-only-authentication"></a>Affecter un rôle pour activer l’authentification Azure AD uniquement

Pour activer ou désactiver l’authentification Azure AD uniquement, les rôles intégrés sélectionnés sont requis pour les utilisateurs d’Azure AD qui exécutent les opérations de ce tutoriel. Nous allons attribuer le rôle de [gestionnaire de sécurité SQL](../../role-based-access-control/built-in-roles.md#sql-security-manager) à l’utilisateur dans ce tutoriel.

Pour plus d’informations sur l’attribution de rôles pour un compte Azure AD, consultez [Attribuer des rôles administrateur et non-administrateur aux utilisateurs avec Azure Active Directory](../../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md)

Pour plus d’informations sur l’autorisation requise pour activer ou désactiver l’authentification Azure AD uniquement, consultez la section [Autorisations de l’article sur l'authentification Azure AD uniquement](authentication-azure-ad-only-authentication.md#permissions).

1. Dans notre exemple, nous attribuons le rôle de **gestionnaire de sécurité SQL** à l’utilisateur `UserSqlSecurityManager@contoso.onmicrosoft.com`. À l’aide d’un utilisateur privilégié qui peut affecter des rôles de Azure AD, connectez-vous au [portail Azure](https://portal.azure.com/).
1. Accédez à la ressource de votre serveur SQL, puis sélectionnez **Contrôle d’accès (IAM)** dans le menu. Sélectionnez le bouton **Ajouter**, puis **Ajouter une attribution de rôle** dans le menu déroulant.

   :::image type="content" source="media/authentication-azure-ad-only-authentication/azure-ad-only-authentication-access-control.png" alt-text="Volet Contrôle d’accès dans le Portail Azure":::

1. Dans le volet **Ajouter une attribution de rôle**, sélectionnez le rôle **Gestionnaire de sécurité SQL**, puis sélectionnez l’utilisateur qui pourra activer ou désactiver l’authentification Azure AD uniquement.

   :::image type="content" source="media/authentication-azure-ad-only-authentication/azure-ad-only-authentication-access-control-add-role.png" alt-text="Volet Ajouter une attribution de rôle dans le portail Azure":::

1. Cliquez sur **Enregistrer**.

## <a name="enable-azure-ad-only-authentication"></a>Activer l’authentification Azure AD seul

# <a name="portal"></a>[Portail](#tab/azure-portal)

## <a name="enable-in-sql-database-using-azure-portal"></a>Activer dans SQL Database à l’aide de Portail Azure

Pour activer l’authentification par Azure AD uniquement dans le Portail Azure, consultez les étapes ci-dessous.

1. À l’aide de l’utilisateur avec le rôle [Gestionnaire de sécurité SQL](../../role-based-access-control/built-in-roles.md#sql-security-manager), accédez au [Portail Azure](https://portal.azure.com/).
1. Accédez à la ressource de votre serveur SQL, puis sélectionnez **Azure Active Directory** dans le menu **Paramètres**.

   :::image type="content" source="media/authentication-azure-ad-only-authentication/azure-ad-only-authentication-portal.png" alt-text="Menu Activer l’authentification Azure AD uniquement":::

1. Si vous n’avez pas ajouté d'**administrateur Azure Active Directory**, vous devez le définir avant de pouvoir activer l’authentification Azure AD uniquement.
1. Activez la case à cocher **Prise en charge de l’authentification Azure Active Directory uniquement pour ce serveur**.
1. Le menu contextuel **Activer l’authentification Azure AD uniquement** s’affiche. Cliquez sur **Oui** pour activer la fonctionnalité, puis sur **Enregistrer**.

## <a name="enable-in-sql-managed-instance-using-azure-portal"></a>Activer dans SQL Managed Instance à l’aide de Portail Azure

Pour activer l’authentification par Azure AD uniquement dans le Portail Azure, consultez les étapes ci-dessous.

1. À l’aide de l’utilisateur avec le rôle [Gestionnaire de sécurité SQL](../../role-based-access-control/built-in-roles.md#sql-security-manager), accédez au [Portail Azure](https://portal.azure.com/).
1. Accédez à votre ressource d’**instance gérée SQL** et sélectionnez **Administrateur Active Directory** dans le menu **Paramètres**.

1. Si vous n’avez pas ajouté d'**administrateur Azure Active Directory**, vous devez le définir avant de pouvoir activer l’authentification Azure AD uniquement.
1. Cochez la case **Prise en charge de l’authentification Azure Active Directory uniquement pour cette instance gérée**.
1. Le menu contextuel **Activer l’authentification Azure AD uniquement** s’affiche. Cliquez sur **Oui** pour activer la fonctionnalité, puis sur **Enregistrer**.

# <a name="the-azure-cli"></a>[L’interface de ligne de commande Microsoft Azure](#tab/azure-cli)

## <a name="enable-in-sql-database-using-azure-cli"></a>Activer dans SQL Database à l’aide d’Azure CLI

Pour activer l’authentification Azure AD uniquement dans Azure SQL Database à l’aide d’Azure CLI, consultez les commandes ci-dessous. [Installez la dernière version d’Azure CLI](/cli/azure/install-azure-cli-windows). Vous devez disposer d’Azure CLI **2.14.2** ou version ultérieure. Pour plus d’informations sur ces commandes, consultez [az sql server ad-only-auth](/cli/azure/sql/server/ad-only-auth).

Pour plus d’informations sur la gestion de l’authentification Azure AD uniquement à l’aide d’API, consultez [Gestion de l'authentification Azure AD uniquement à l’aide d’API](authentication-azure-ad-only-authentication.md#managing-azure-ad-only-authentication-using-apis).

> [!NOTE]
> L’administrateur Azure AD doit être défini pour le serveur avant d’activer l’authentification Azure AD uniquement. Sinon, la commande Azure CLI échoue.
>
> Pour obtenir les autorisations et les actions requises de l’utilisateur qui exécute ces commandes pour activer l’authentification Azure AD uniquement, consultez l’article [Authentification Azure AD uniquement](authentication-azure-ad-only-authentication.md#permissions).

1. [Connectez-vous à Azure](/cli/azure/authenticate-azure-cli) à l’aide du compte ayant le rôle de [Gestionnaire de sécurité SQL](../../role-based-access-control/built-in-roles.md#sql-security-manager).

   ```azurecli
   az login
   ```

1. Exécutez la commande suivante, en remplaçant `<myserver>` par le nom de votre serveur SQL et `<myresource>` par votre ressource Azure qui contient le serveur SQL.

   ```azurecli
   az sql server ad-only-auth enable --resource-group <myresource> --name <myserver>
   ```

## <a name="enable-in-sql-managed-instance-using-azure-cli"></a>Activer dans SQL Managed Instance à l’aide de Azure CLI

Pour activer l’authentification Azure AD uniquement dans Azure SQL Managed Instance à l’aide d’Azure CLI, consultez les commandes ci-dessous. [Installez la dernière version d’Azure CLI](/cli/azure/install-azure-cli-windows). 

1. [Connectez-vous à Azure](/cli/azure/authenticate-azure-cli) à l’aide du compte ayant le rôle de [Gestionnaire de sécurité SQL](../../role-based-access-control/built-in-roles.md#sql-security-manager).

   ```azurecli
   az login
   ```

1. Exécutez la commande suivante, en remplaçant `<myserver>` par le nom de votre serveur SQL et `<myresource>` par votre ressource Azure qui contient le serveur SQL.

   ```azurecli
   az sql mi ad-only-auth enable --resource-group <myresource> --name <myserver>
   ```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

## <a name="enable-in-sql-database-using-powershell"></a>Activer dans SQL Database à l’aide de PowerShell

Pour activer l’authentification Azure AD uniquement dans Azure SQL Database à l’aide de PowerShell, consultez les commandes ci-dessous. Le module [Az.Sql 2.10.0](https://www.powershellgallery.com/packages/Az.Sql/2.10.0) ou une version ultérieure est requis pour exécuter ces commandes. Pour plus d’informations sur ces commandes, consultez [Enable-AzSqlInstanceActiveDirectoryOnlyAuthentication](/powershell/module/az.sql/enable-azsqlinstanceactivedirectoryonlyauthentication).

Pour plus d’informations sur la gestion de l’authentification Azure AD uniquement à l’aide d’API, consultez [Gestion de l'authentification Azure AD uniquement à l’aide d’API](authentication-azure-ad-only-authentication.md#managing-azure-ad-only-authentication-using-apis)

> [!NOTE]
> L’administrateur Azure AD doit être défini pour le serveur avant d’activer l’authentification Azure AD uniquement. Sinon, la commande PowerShell échoue.
>
> Pour obtenir les autorisations et les actions requises de l’utilisateur qui exécute ces commandes pour activer l’authentification Azure AD uniquement, consultez l’article [Authentification Azure AD uniquement](authentication-azure-ad-only-authentication.md#permissions). Si l’utilisateur ne dispose pas des autorisations suffisantes, vous obtiendrez l’erreur suivante :
>
> ```output
> Enable-AzSqlServerActiveDirectoryOnlyAuthentication : The client
> 'UserSqlServerContributor@contoso.onmicrosoft.com' with object id
> '<guid>' does not have authorization to perform
> action 'Microsoft.Sql/servers/azureADOnlyAuthentications/write' over scope
> '/subscriptions/<guid>...'
> ```

1. [Connectez-vous à Azure](/powershell/azure/authenticate-azureps) à l’aide du compte ayant le rôle de [Gestionnaire de sécurité SQL](../../role-based-access-control/built-in-roles.md#sql-security-manager).

   ```powershell
   Connect-AzAccount
   ```

1. Exécutez la commande suivante, en remplaçant `<myserver>` par le nom de votre serveur SQL et `<myresource>` par votre ressource Azure qui contient le serveur SQL.

   ```powershell
   Enable-AzSqlServerActiveDirectoryOnlyAuthentication -ServerName <myserver>  -ResourceGroupName <myresource>
   ```

## <a name="enable-in-sql-managed-instance-using-powershell"></a>Activer dans SQL Managed Instance à l’aide de PowerShell

Pour activer l’authentification Azure AD uniquement dans Azure SQL Managed Instance à l’aide de PowerShell, consultez les commandes ci-dessous. Le module [Az.Sql 2.10.0](https://www.powershellgallery.com/packages/Az.Sql/2.10.0) ou une version ultérieure est requis pour exécuter ces commandes. 

Pour plus d’informations sur la gestion de l’authentification Azure AD uniquement à l’aide d’API, consultez [Gestion de l'authentification Azure AD uniquement à l’aide d’API](authentication-azure-ad-only-authentication.md#managing-azure-ad-only-authentication-using-apis).


1. [Connectez-vous à Azure](/powershell/azure/authenticate-azureps) à l’aide du compte ayant le rôle de [Gestionnaire de sécurité SQL](../../role-based-access-control/built-in-roles.md#sql-security-manager).

   ```powershell
   Connect-AzAccount
   ```

1. Exécutez la commande suivante, en remplaçant `<myinstance>` par le nom de votre instance gérée SQL et `<myresource>` par votre ressource Azure qui contient l’**instance gérée SQL**.

   ```powershell
   Enable-AzSqlInstanceActiveDirectoryOnlyAuthentication -InstanceName <myinstance> -ResourceGroupName <myresource>
   ```

---

## <a name="check-the-azure-ad-only-authentication-status"></a>Vérifier l’état de l’authentification Azure AD uniquement

Vérifiez si l’authentification Azure AD uniquement est activée pour votre serveur ou instance.

# <a name="portal"></a>[Portail](#tab/azure-portal)

## <a name="check-status-in-sql-database"></a>Vérifier l’état dans SQL Database

Accédez à la ressource sur votre **serveur SQL** dans le [Portail Azure](https://portal.azure.com/). Sélectionnez **Azure Active Directory** dans le menu **Paramètres**.

## <a name="check-status-in-sql-managed-instance"></a>Vérifier l’état dans SQL Managed Instance

Accédez à votre ressource d’**instance gérée SQL** dans le [portail Azure](https://portal.azure.com/). Sous le menu **Paramètres**, sélectionnez **Administrateur Active Directory**.

# <a name="the-azure-cli"></a>[L’interface de ligne de commande Microsoft Azure](#tab/azure-cli)

Ces commandes peuvent être utilisées pour vérifier si l’authentification Azure AD uniquement est activée pour votre [serveur logique](logical-servers.md) pour Azure SQL Database ou SQL Managed Instance. Les membres des rôles [Contributeur SQL Server](../../role-based-access-control/built-in-roles.md#sql-server-contributor) et [Contributeur SQL Managed Instance](../../role-based-access-control/built-in-roles.md#sql-managed-instance-contributor) peuvent utiliser ces commandes pour vérifier l’état de l’authentification Azure AD uniquement, mais ne peuvent pas activer ou désactiver la fonctionnalité.

## <a name="check-status-in-sql-database"></a>Vérifier l’état dans SQL Database

1. [Connectez-vous à Azure](/cli/azure/authenticate-azure-cli) à l’aide du compte ayant le rôle de [Gestionnaire de sécurité SQL](../../role-based-access-control/built-in-roles.md#sql-security-manager). Pour plus d’informations sur la gestion de l’authentification Azure AD uniquement à l’aide d’API, consultez [Gestion de l'authentification Azure AD uniquement à l’aide d’API](authentication-azure-ad-only-authentication.md#managing-azure-ad-only-authentication-using-apis)

   ```azurecli
   az login
   ```

1. Exécutez la commande suivante, en remplaçant `<myserver>` par le nom de votre serveur SQL et `<myresource>` par votre ressource Azure qui contient le serveur SQL.

   ```azurecli
   az sql server ad-only-auth get --resource-group <myresource> --name <myserver>
   ```

1. Vous devez normalement voir la sortie suivante.

   ```json
   {
    "azureAdOnlyAuthentication": true,
    "/subscriptions/<guid>/resourceGroups/mygroup/providers/Microsoft.Sql/servers/myserver/azureADOnlyAuthentications/Default",
    "name": "Default",
    "resourceGroup": "myresource",
    "type": "Microsoft.Sql/servers"
   }
   ```

## <a name="check-status-in-sql-managed-instance"></a>Vérifier l’état dans SQL Managed Instance

1. [Connectez-vous à Azure](/cli/azure/authenticate-azure-cli) à l’aide du compte ayant le rôle de [Gestionnaire de sécurité SQL](../../role-based-access-control/built-in-roles.md#sql-security-manager).

   ```azurecli
   az login
   ```

1. Exécutez la commande suivante, en remplaçant `<myserver>` par le nom de votre serveur SQL et `<myresource>` par votre ressource Azure qui contient le serveur SQL.

   ```azurecli
   az sql mi ad-only-auth get --resource-group <myresource> --name <myserver>
   ```

1. Vous devez normalement voir la sortie suivante.

   ```json
   {
    "azureAdOnlyAuthentication": true,
    "id": "/subscriptions/<guid>/resourceGroups/myresource/providers/Microsoft.Sql/managedInstances/myinstance/azureADOnlyAuthentications/Default",
    "name": "Default",
    "resourceGroup": "myresource",
    "type": "Microsoft.Sql/managedInstances"
   }
   ```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Ces commandes peuvent être utilisées pour vérifier si l’authentification Azure AD uniquement est activée pour votre [serveur logique](logical-servers.md) pour Azure SQL Database ou SQL Managed Instance. Les membres des rôles [Contributeur SQL Server](../../role-based-access-control/built-in-roles.md#sql-server-contributor) et [Contributeur SQL Managed Instance](../../role-based-access-control/built-in-roles.md#sql-managed-instance-contributor) peuvent utiliser ces commandes pour vérifier l’état de l’authentification Azure AD uniquement, mais ne peuvent pas activer ou désactiver la fonctionnalité.

L’état retourne la valeur **True** si la fonctionnalité est activée, et la valeur **False** si elle est désactivée.

## <a name="check-status-in-sql-database"></a>Vérifier l’état dans SQL Database

1. [Connectez-vous à Azure](/powershell/azure/authenticate-azureps) à l’aide du compte ayant le rôle de [Gestionnaire de sécurité SQL](../../role-based-access-control/built-in-roles.md#sql-security-manager). Pour plus d’informations sur la gestion de l’authentification Azure AD uniquement à l’aide d’API, consultez [Gestion de l'authentification Azure AD uniquement à l’aide d’API](authentication-azure-ad-only-authentication.md#managing-azure-ad-only-authentication-using-apis)

   ```powershell
   Connect-AzAccount
   ```

1. Exécutez la commande suivante, en remplaçant `<myserver>` par le nom de votre serveur SQL et `<myresource>` par votre ressource Azure qui contient le serveur SQL.

   ```powershell
   Get-AzSqlServerActiveDirectoryOnlyAuthentication  -ServerName <myserver> -ResourceGroupName <myresource>
   ```

## <a name="check-status-in-sql-managed-instance"></a>Vérifier l’état dans SQL Managed Instance

1. [Connectez-vous à Azure](/powershell/azure/authenticate-azureps) à l’aide du compte ayant le rôle de [Gestionnaire de sécurité SQL](../../role-based-access-control/built-in-roles.md#sql-security-manager).

   ```powershell
   Connect-AzAccount
   ```

1. Exécutez la commande suivante, en remplaçant `<myinstance>` par le nom de votre instance gérée SQL et `<myresource>` par votre ressource Azure qui contient l’**instance gérée SQL**.

   ```powershell
   Get-AzSqlInstanceActiveDirectoryOnlyAuthentication -InstanceName <myinstance> -ResourceGroupName <myresource>
   ```

---

## <a name="test-sql-authentication-with-connection-failure"></a>Tester l’authentification SQL avec échec de connexion

Après l’activation de l’authentification Azure AD uniquement, essayez avec [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) de [vous connecter à votre base de données SQL ou à votre instance gérée SQL](connect-query-ssms.md). Utilisez l'authentification SQL pour la connexion.

Un message d’erreur similaire à celui qui suit doit s’afficher :

```output
Cannot connect to <myserver>.database.windows.net.
Additional information:
  Login failed for user 'username'. Reason: Azure Active Directory only authentication is enabled.
  Please contact your system administrator. (Microsoft SQL Server, Error: 18456)
```

## <a name="disable-azure-ad-only-authentication"></a>Désactiver l’authentification Azure AD uniquement

En désactivant la fonctionnalité d’authentification Azure AD uniquement, vous autorisez l’authentification SQL et l’authentification Azure AD pour Azure SQL.

# <a name="portal"></a>[Portail](#tab/azure-portal)

## <a name="disable-in-sql-database-using-azure-portal"></a>Désactiver dans SQL Database à l’aide de Portail Azure

1. À l’aide de l’utilisateur avec le rôle [Gestionnaire de sécurité SQL](../../role-based-access-control/built-in-roles.md#sql-security-manager), accédez au [Portail Azure](https://portal.azure.com/).
1. Accédez à la ressource de votre serveur SQL, puis sélectionnez **Azure Active Directory** dans le menu **Paramètres**.
1. Pour désactiver la fonctionnalité d’authentification Azure AD uniquement, désactivez la case à cocher **Prendre en charge l’authentification Azure Active Directory uniquement pour ce serveur** et cliquez sur **Enregistrer**.

## <a name="disable-in-sql-managed-instance-using-azure-portal"></a>Désactiver dans SQL Managed Instance à l’aide de Portail Azure

1. À l’aide de l’utilisateur avec le rôle [Gestionnaire de sécurité SQL](../../role-based-access-control/built-in-roles.md#sql-security-manager), accédez au [Portail Azure](https://portal.azure.com/).
1. Accédez à votre ressource d’**instance gérée SQL** et sélectionnez **Administrateur Active Directory** dans le menu **Paramètres**.
1. Pour désactiver la fonctionnalité d’authentification Azure AD uniquement, décochez la case **Prise en charge de l’authentification Azure Active Directory uniquement pour cette instance gérée** et cliquez sur **Enregistrer**.

# <a name="the-azure-cli"></a>[L’interface de ligne de commande Microsoft Azure](#tab/azure-cli)

## <a name="disable-in-sql-database-using-azure-cli"></a>Désactiver dans SQL Database à l’aide d’Azure CLI

Pour désactiver l’authentification Azure AD uniquement dans Azure SQL Database à l’aide d’Azure CLI, consultez les commandes ci-dessous. 

1. [Connectez-vous à Azure](/cli/azure/authenticate-azure-cli) à l’aide du compte ayant le rôle de [Gestionnaire de sécurité SQL](../../role-based-access-control/built-in-roles.md#sql-security-manager).

   ```azurecli
   az login
   ```

1. Exécutez la commande suivante, en remplaçant `<myserver>` par le nom de votre serveur SQL et `<myresource>` par votre ressource Azure qui contient le serveur SQL.

   ```azurecli
   az sql server ad-only-auth disable --resource-group <myresource> --name <myserver>
   ```

1. Après la désactivation de l’authentification Azure AD uniquement, vous devez obtenir ses résultats lorsque vous vérifiez l’état :

   ```json
   {
    "azureAdOnlyAuthentication": false,
    "/subscriptions/<guid>/resourceGroups/mygroup/providers/Microsoft.Sql/servers/myserver/azureADOnlyAuthentications/Default",
    "name": "Default",
    "resourceGroup": "myresource",
    "type": "Microsoft.Sql/servers"
   }
   ```

## <a name="disable-in-sql-managed-instance-using-azure-cli"></a>Désactiver dans SQL Managed Instance à l’aide de Azure CLI

Pour désactiver l’authentification Azure AD uniquement dans Azure SQL Managed Instance à l’aide d’Azure CLI, consultez les commandes ci-dessous. 

1. [Connectez-vous à Azure](/cli/azure/authenticate-azure-cli) à l’aide du compte ayant le rôle de [Gestionnaire de sécurité SQL](../../role-based-access-control/built-in-roles.md#sql-security-manager).

   ```azurecli
   az login
   ```

1. Exécutez la commande suivante, en remplaçant `<myserver>` par le nom de votre serveur SQL et `<myresource>` par votre ressource Azure qui contient le serveur SQL.

   ```azurecli
   az sql mi ad-only-auth disable --resource-group <myresource> --name <myserver>
   ```

1. Après la désactivation de l’authentification Azure AD uniquement, vous devez obtenir ses résultats lorsque vous vérifiez l’état :

   ```json
   {
    "azureAdOnlyAuthentication": false,
    "id": "/subscriptions/<guid>/resourceGroups/myresource/providers/Microsoft.Sql/managedInstances/myinstance/azureADOnlyAuthentications/Default",
    "name": "Default",
    "resourceGroup": "myresource",
    "type": "Microsoft.Sql/managedInstances"
   }
   ```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

## <a name="disable-in-sql-database-using-powershell"></a>Désactiver dans SQL Database à l’aide de PowerShell

Pour désactiver l’authentification Azure AD uniquement dans Azure SQL Database à l’aide de PowerShell, consultez les commandes ci-dessous.

1. [Connectez-vous à Azure](/powershell/azure/authenticate-azureps) à l’aide du compte ayant le rôle de [Gestionnaire de sécurité SQL](../../role-based-access-control/built-in-roles.md#sql-security-manager).

   ```powershell
   Connect-AzAccount
   ```

1. Exécutez la commande suivante, en remplaçant `<myserver>` par le nom de votre serveur SQL et `<myresource>` par votre ressource Azure qui contient le serveur SQL.

   ```powershell
   Disable-AzSqlServerActiveDirectoryOnlyAuthentication -ServerName <myserver>  -ResourceGroupName <myresource>
   ```

## <a name="disable-in-sql-managed-instance-using-powershell"></a>Désactiver dans SQL Managed Instance à l’aide de PowerShell

Pour désactiver l’authentification Azure AD uniquement dans Azure SQL Managed Instance à l’aide de PowerShell, consultez les commandes ci-dessous.

1. [Connectez-vous à Azure](/powershell/azure/authenticate-azureps) à l’aide du compte ayant le rôle de [Gestionnaire de sécurité SQL](../../role-based-access-control/built-in-roles.md#sql-security-manager).

   ```powershell
   Connect-AzAccount
   ```

1. Exécutez la commande suivante, en remplaçant `<myinstance>` par le nom de votre instance gérée SQL et `<myresource>` par votre ressource Azure qui contient l’instance gérée.

   ```powershell
   Disable-AzSqlInstanceActiveDirectoryOnlyAuthentication -InstanceName <myinstance> -ResourceGroupName <myresource>
   ```

---

## <a name="test-connecting-to-azure-sql-again"></a>Tester à nouveau la connexion à Azure SQL

Après la désactivation de l’authentification Azure AD uniquement, testez la connexion en vous connectant via l’authentification SQL. Vous devez maintenant être en mesure de vous connecter à votre serveur ou instance.

## <a name="next-steps"></a>Étapes suivantes

- [Authentification Azure AD uniquement avec Azure SQL](authentication-azure-ad-only-authentication.md)
- [Créer un serveur avec l’authentification Azure AD uniquement activée dans Azure SQL](authentication-azure-ad-only-authentication-create-server.md)
- [Utilisation d’Azure Policy pour appliquer l’authentification Azure Active Directory uniquement avec Azure SQL](authentication-azure-ad-only-authentication-policy-how-to.md)
