---
title: Principal de service Azure Active Directory avec Azure SQL
description: Utiliser des applications AD (principaux de service) qui prennent en charge la création d’utilisateurs Azure AD dans Azure SQL Database et Azure SQL Managed Instance
ms.service: sql-db-mi
ms.subservice: security
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 10/21/2021
ms.openlocfilehash: 3d72b151e73b9adc39f71cd89362027ae2cd9e0f
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130256232"
---
# <a name="azure-active-directory-service-principal-with-azure-sql"></a>Principal de service Azure Active Directory avec Azure SQL

[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Azure Active Directory (Azure AD) prend en charge la création d’utilisateurs dans Azure SQL Database (SQL DB) pour le compte des applications Azure AD (principaux de service).

> [!NOTE]
> Cette fonctionnalité est déjà prise en charge pour SQL Managed Instance.

## <a name="service-principal-azure-ad-applications-support"></a>Prise en charge du principal de service (applications Azure AD)

Cet article s’applique aux applications intégrées à Azure AD et qui font partie de l’inscription d’Azure AD. Ces applications ont souvent besoin d’un accès d’authentification et d’autorisation à Azure SQL pour effectuer diverses tâches. Cette fonctionnalité permet désormais aux principaux de service de créer des utilisateurs Azure AD dans SQL Database. La limitation qui empêchait la création d’objets Azure AD pour le compte d’applications Azure AD a été supprimée.

Quand une application Azure AD est inscrite à l’aide du portail Azure ou d’une commande PowerShell, deux objets sont créés dans le locataire Azure AD :

- un objet application ;
- un objet principal du service.

Pour plus d’informations sur les applications Azure AD, consultez [Objets application et principal du service dans Azure Active Directory](../../active-directory/develop/app-objects-and-service-principals.md) et [Créer un principal de service Azure avec Azure PowerShell](/powershell/azure/create-azure-service-principal-azureps).

SQL Database et SQL Managed Instance prennent en charge les objets Azure AD suivants :

- Utilisateurs Azure AD (managés, fédérés et invités)
- Groupes Azure AD (managés et fédérés)
- Applications Azure AD 

La commande T-SQL `CREATE USER [Azure_AD_Object] FROM EXTERNAL PROVIDER` pour le compte d’une application Azure AD est maintenant prise en charge pour SQL Database.

## <a name="functionality-of-azure-ad-user-creation-using-service-principals"></a>Fonctionnalité de création d’utilisateurs Azure AD à l’aide de principaux de service

La prise en charge de cette fonctionnalité est utile dans les processus d’automatisation des applications Azure AD où les objets Azure AD sont créés et conservés dans SQL Database sans intervention humaine. Les principaux de service peuvent être un administrateur Azure AD pour le serveur logique SQL, en tant que groupe ou utilisateur individuel. L’application peut automatiser la création d’objets Azure AD dans SQL Database lorsqu’elle est exécutée en tant qu’administrateur système et ne nécessite pas de privilèges SQL supplémentaires. Cela permet une automatisation complète de la création d’un utilisateur de base de données. Cette fonctionnalité prend également en charge l’identité managée attribuée par l’utilisateur et l’identité managée attribuée par le système Azure AD qui peuvent être créées en tant qu’utilisateurs dans SQL Database pour le compte des principaux de service. Pour plus d’informations, consultez [Que sont les identités managées pour les ressources Azure ?](../../active-directory/managed-identities-azure-resources/overview.md)

## <a name="enable-service-principals-to-create-azure-ad-users"></a>Permettre aux principaux de service de créer des utilisateurs Azure AD

Pour permettre la création d’un objet Azure AD dans SQL Database pour le compte d’une application Azure AD, les paramètres suivants sont requis :

1. Attribuez l’identité du serveur. L’identité du serveur attribuée représente l’identité MSI (Managed Service Identity). Actuellement, l’identité du serveur pour Azure SQL ne prend pas en charge les identités managées affectées par l’utilisateur.
    - Pour un nouveau serveur logique Azure SQL, exécutez la commande PowerShell suivante :
    
    ```powershell
    New-AzSqlServer -ResourceGroupName <resource group> -Location <Location name> -ServerName <Server name> -ServerVersion "12.0" -SqlAdministratorCredentials (Get-Credential) -AssignIdentity
    ```

    Pour plus d’informations, consultez la commande [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver), ou la commande [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance) pour SQL Managed Instance.

    - Pour les serveurs logiques Azure SQL existants, exécutez la commande suivante :
    
    ```powershell
    Set-AzSqlServer -ResourceGroupName <resource group> -ServerName <Server name> -AssignIdentity
    ```

    Pour plus d’informations, consultez la commande [Set-AzSqlServer](/powershell/module/az.sql/set-azsqlserver), ou la commande [Set-AzSqlInstance](/powershell/module/az.sql/set-azsqlinstance) pour SQL Managed Instance.

    - Pour vérifier si l’identité du serveur est attribuée au serveur, exécutez la commande Get-AzSqlServer.

    > [!NOTE]
    > L’identité du serveur peut également être attribuée à l’aide de commandes CLI et de l’API REST. Pour plus d’informations, consultez la page sur les commandes [az sql server create](/cli/azure/sql/server#az_sql_server_create), [az sql server update](/cli/azure/sql/server#az_sql_server_update) et [Serveurs - API REST](/rest/api/sql/2020-08-01-preview/servers).

2. Accordez l’autorisation [**Lecteurs de répertoire**](../../active-directory/roles/permissions-reference.md#directory-readers) Azure AD à l’identité du serveur créée ou attribuée au serveur.
    - Pour accorder cette autorisation, suivez la description utilisée pour SQL Managed Instance qui est disponible dans l’article suivant : [Approvisionner un administrateur Azure AD (SQL Managed Instance)](authentication-aad-configure.md?tabs=azure-powershell#provision-azure-ad-admin-sql-managed-instance).
    - L’utilisateur Azure AD qui accorde cette autorisation doit faire partie du rôle **Administrateur général** ou **Administrateur de rôle privilégié** d’Azure AD.

> [!IMPORTANT]
> Les étapes 1 et 2 doivent être exécutées dans l’ordre indiqué ci-dessus. Tout d’abord, créez ou attribuez l’identité du serveur, puis accordez-lui ’autorisation [**Lecteurs de répertoire**](../../active-directory/roles/permissions-reference.md#directory-readers). Si vous omettez l’une de ces étapes, ou les deux, une erreur d’exécution se produira lors de la création d’un objet Azure AD dans Azure SQL pour le compte d’une application Azure AD.
>
> Vous pouvez affecter le rôle **Lecteurs de répertoire** à un groupe dans Azure AD. Les propriétaires du groupe peuvent ensuite ajouter l’identité managée en tant que membre de ce groupe, évitant ainsi qu’un **Administrateur général** ou **Administrateur de rôles privilégiés** accorde le rôle **Lecteurs de répertoire**. Pour plus d’informations sur cette fonctionnalité, consultez [Rôle Lecteurs d’annuaires dans Azure Active Directory pour Azure SQL](authentication-aad-directory-readers-role.md).

## <a name="troubleshooting-and-limitations"></a>Résolution des problèmes et limitations

- Lorsque vous créez des objets Azure AD dans Azure SQL pour le compte d’une application Azure AD sans activer l’identité du serveur ni accorder l’autorisation **Lecteurs de répertoire**, l’opération échoue avec les erreurs possibles suivantes. L’exemple d’erreur ci-dessous concerne l’exécution d’une commande PowerShell pour créer un utilisateur SQL Database `myapp` dans l’article [Didacticiel : Créer des utilisateurs Azure AD avec des applications Azure AD](authentication-aad-service-principal-tutorial.md).
    - `Exception calling "ExecuteNonQuery" with "0" argument(s): "'myapp' is not a valid login or you do not have permission. Cannot find the user 'myapp', because it does not exist, or you do not have permission."`
    - `Exception calling "ExecuteNonQuery" with "0" argument(s): "Principal 'myapp' could not be resolved. Error message:
    'Server identity is not configured. Please follow the steps in "Assign an Azure AD identity to your server and add
    Directory Reader permission to your identity" (https://aka.ms/sqlaadsetup)'"`
      - Pour l’erreur ci-dessus, suivez les étapes pour [attribuer une identité au serveur logique Azure SQL](authentication-aad-service-principal-tutorial.md#assign-an-identity-to-the-azure-sql-logical-server) et [attribuer une autorisation Lecteurs de répertoires à l’identité du serveur logique SQL](authentication-aad-service-principal-tutorial.md#assign-directory-readers-permission-to-the-sql-logical-server-identity).
    - La configuration du principal de service (application Azure AD) en tant qu’administrateur Azure AD pour SQL Database est prise en charge à l’aide du portail Azure, de [PowerShell](authentication-aad-configure.md?tabs=azure-powershell#powershell-for-sql-database-and-azure-synapse), de [l’API REST](/rest/api/sql/2020-08-01-preview/servers) et des commandes [CLI](authentication-aad-configure.md?tabs=azure-cli#powershell-for-sql-database-and-azure-synapse).
- L’utilisation d’une application Azure AD avec le principal de service d’un autre locataire Azure AD échoue lors de l’accès à l’instance SQL Database ou SQL Managed Instance créée dans un autre locataire. Un principal de service attribué à cette application doit provenir du même locataire que le serveur logique SQL ou que l’instance gérée.
- Le module [Az.Sql 2.9.0](https://www.powershellgallery.com/packages/Az.Sql/2.9.0) (ou version supérieure) pour utiliser PowerShell afin de configurer une application Azure AD individuelle en tant qu’administrateur Azure AD pour Azure SQL. Assurez-vous que vous avez mis à niveau le module vers la version la plus récente.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Tutoriel : Créer des utilisateurs Azure AD avec des applications Azure AD](authentication-aad-service-principal-tutorial.md)
