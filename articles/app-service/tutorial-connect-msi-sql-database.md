---
title: 'Tutoriel : Accéder aux données avec une identité managée'
description: Découvrez comment sécuriser la connectivité de la base de données à l’aide d’une identité managée, et comment appliquer cette technique à d’autres services Azure.
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 04/27/2021
ms.custom: devx-track-csharp, mvc, cli-validate, devx-track-azurecli
ms.openlocfilehash: 01a80fe6860f40827dee1c3367ec19a7eaf9589b
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131096608"
---
# <a name="tutorial-secure-azure-sql-database-connection-from-app-service-using-a-managed-identity"></a>Tutoriel : Sécuriser la connexion Azure SQL Database à partir d’App Service à l’aide d’une identité managée

[App Service](overview.md) offre un service d’hébergement web hautement évolutif appliquant des mises à jour correctives automatiques dans Azure. Il offre également une [identité managée](overview-managed-identity.md) pour votre application, qui constitue une solution clé en main permettant de sécuriser l’accès à [Azure SQL Database](/azure/sql-database/) et à d’autres services Azure. Les identités managées dans App Service sécurisent votre application en en éliminant les secrets, par exemple les informations d’identification dans les chaînes de connexion. Dans ce tutoriel, vous allez ajouter l’identité managée à l’exemple d’application web que vous avez créé dans l’un des tutoriels suivants : 

- [Tutoriel : Générer une application ASP.NET dans Azure avec Azure SQL Database](app-service-web-tutorial-dotnet-sqldatabase.md)
- [Tutoriel : Générer une application ASP.NET Core et Azure SQL Database dans Azure App Service](tutorial-dotnetcore-sqldb-app.md)

Lorsque vous aurez terminé, votre exemple d’application se connectera à SQL Database en toute sécurité sans nécessiter aucun nom d’utilisateur ni mot de passe.

![Diagramme d’architecture pour le scénario de didacticiel.](./media/tutorial-connect-msi-sql-database/architecture.png)

> [!NOTE]
> Les étapes décrites dans ce tutoriel prennent en charge les versions suivantes :
> 
> - .NET Framework 4.7.2 et ultérieur
> - .NET Core 2.2 et ultérieur
>

Contenu :

> [!div class="checklist"]
> * Activer les identités managées
> * Accorder à l’identité managée un accès à SQL Database
> * Configurer Entity Framework pour utiliser l’authentification Azure AD avec SQL Database
> * Se connecter à SQL Database à partir de Visual Studio à l’aide de l’authentification Azure AD

> [!NOTE]
>L’authentification Azure AD est _différente_ de [l’authentification Windows intégrée](/previous-versions/windows/it-pro/windows-server-2003/cc758557(v=ws.10)) dans Active Directory (AD DS) local. AD DS et Azure AD utilisent des protocoles d’authentification totalement différents. Pour plus d’informations, consultez la [documentation sur Azure AD Domain Services](../active-directory-domain-services/index.yml).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prérequis

Cet article se poursuit là où vous l'avez laissé dans [Tutoriel : Créer une application ASP.NET dans Azure avec SQL Database](app-service-web-tutorial-dotnet-sqldatabase.md) ou [Tutoriel : Créer une application ASP.NET Core et SQL Database dans Azure App Service](tutorial-dotnetcore-sqldb-app.md). Si vous ne l’avez pas déjà fait, suivez d’abord l’un des deux tutoriels. Vous pouvez également adapter la procédure à votre propre application .NET avec SQL Database.

Pour déboguer votre application à l’aide de SQL Database en tant que back-end, assurez-vous que vous avez autorisé la connexion cliente à partir de votre machine. Si ce n’est pas le cas, ajoutez l’adresse IP cliente en suivant les étapes décrites dans [Gérer les règles de pare-feu IP au niveau du serveur avec le portail Azure](../azure-sql/database/firewall-configure.md#use-the-azure-portal-to-manage-server-level-ip-firewall-rules).

Préparez votre environnement pour l’interface Azure CLI.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="grant-database-access-to-azure-ad-user"></a>Accorder à l’utilisateur Azure AD un accès à la base de données

Tout d’abord, activez l’authentification Azure AD auprès de SQL Database en attribuant un utilisateur Azure AD en tant qu’administrateur Active Directory du serveur. Cet utilisateur est différent du compte Microsoft que vous avez utilisé pour vous inscrire à votre abonnement Azure. Il doit s’agir d’un utilisateur créé, importé, synchronisé ou invité à Azure AD. Pour plus d’informations sur les utilisateurs Azure AD autorisés, consultez [Fonctionnalités Azure AD et limitations dans SQL Database](../azure-sql/database/authentication-aad-overview.md#azure-ad-features-and-limitations).

1. Si votre locataire Azure AD n’a pas encore d’utilisateur, créez-en un en suivant les étapes de la section [Ajouter ou supprimer des utilisateurs à l’aide d’Azure Active Directory](../active-directory/fundamentals/add-users-azure-active-directory.md).

1. Recherchez l’ID d’objet de l’utilisateur Azure AD en utilisant [`az ad user list`](/cli/azure/ad/user#az_ad_user_list) et remplacez *\<user-principal-name>* par votre propre valeur. Le résultat est enregistré dans une variable.

    ```azurecli-interactive
    azureaduser=$(az ad user list --filter "userPrincipalName eq '<user-principal-name>'" --query [].objectId --output tsv)
    ```

    > [!TIP]
    > Pour afficher la liste de tous les noms d’utilisateur dans Azure AD, exécutez `az ad user list --query [].userPrincipalName`.
    >

1. Ajoutez cet utilisateur Azure AD en tant qu’administrateur Active Directory à l’aide de la commande [`az sql server ad-admin create`](/cli/azure/sql/server/ad-admin#az_sql_server_ad_admin_create) dans Cloud Shell. Dans la commande suivante, remplacez *\<server-name>* par le nom du serveur (sans le suffixe `.database.windows.net`).

    ```azurecli-interactive
    az sql server ad-admin create --resource-group myResourceGroup --server-name <server-name> --display-name ADMIN --object-id $azureaduser
    ```

Pour plus d’informations sur l’ajout d’un administrateur Active Directory, consultez [Provisionner un administrateur Azure Active Directory pour votre serveur](../azure-sql/database/authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance).

## <a name="set-up-visual-studio"></a>Configuration de Visual Studio

# <a name="windows-client"></a>[Client Windows](#tab/windowsclient)

1. Visual Studio pour Windows est intégré avec l’authentification Azure AD. Pour activer le développement et le débogage dans Visual Studio, ajoutez votre utilisateur Azure AD dans Visual Studio en sélectionnant **Fichier** > **Paramètres du compte** à partir du menu, puis cliquez sur **Ajouter un compte**.

1. Pour définir l’utilisateur Azure AD pour l’authentification de service Azure, sélectionnez **Outils** > **Options** dans le menu, puis sélectionnez **Authentification du service Azure** > **Sélection du compte**. Sélectionnez l’utilisateur Azure AD que vous avez ajouté, puis cliquez sur **OK**.

# <a name="macos-client"></a>[Client macOS](#tab/macosclient)

1. Visual Studio pour Mac n’est pas intégré avec l’authentification Azure AD. Cependant, la bibliothèque [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) que vous utiliserez par la suite peut utiliser des jetons à partir d’Azure CLI. Pour permettre le développement et le débogage dans Visual Studio, [installez Azure CLI](/cli/azure/install-azure-cli) sur votre ordinateur local.

1. Connectez-vous à Azure CLI à l’aide de la commande suivante avec votre utilisateur Azure AD :

    ```azurecli
    az login --allow-no-subscriptions
    ```

-----

Vous êtes maintenant prêt à développer et déboguer votre application avec SQL Database en tant que back-end, à l’aide de l’authentification Azure AD.

## <a name="modify-your-project"></a>Modifier votre projet

Les étapes que vous suivez pour votre projet varient selon qu’il s’agit d’un projet ASP.NET ou d’un projet ASP.NET Core.

# <a name="aspnet"></a>[ASP.NET](#tab/dotnet)

1. Dans Visual Studio, ouvrez la Console du gestionnaire de package et ajoutez le package NuGet [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) :

    ```powershell
    Install-Package Microsoft.Azure.Services.AppAuthentication -Version 1.4.0
    ```

1. Dans *Web.config*, depuis le début du fichier, apportez les modifications suivantes :

    - Dans `<configSections>`, ajoutez la déclaration de section suivante :
    
        ```xml
        <section name="SqlAuthenticationProviders" type="System.Data.SqlClient.SqlAuthenticationProviderConfigurationSection, System.Data, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" />
        ```
    
    - Sous la balise `</configSections>` de fermeture, ajoutez le code XML suivant pour `<SqlAuthenticationProviders>`.
    
        ```xml
        <SqlAuthenticationProviders>
          <providers>
            <add name="Active Directory Interactive" type="Microsoft.Azure.Services.AppAuthentication.SqlAppAuthenticationProvider, Microsoft.Azure.Services.AppAuthentication" />
          </providers>
        </SqlAuthenticationProviders>
        ```    
    
    - Recherchez la chaîne de connexion appelée `MyDbConnection` et remplacez sa valeur `connectionString` par `"server=tcp:<server-name>.database.windows.net;database=<db-name>;UID=AnyString;Authentication=Active Directory Interactive"`. Remplacez _\<server-name>_ et _\<db-name>_ par les noms de votre serveur et de votre base de données.
    
    > [!NOTE]
    > Le SqlAuthenticationProvider que vous venez d’inscrire est basé sur la bibliothèque AppAuthentication que vous avez installée précédemment. Par défaut, il utilise une identité affectée par le système. Pour tirer parti d’une identité affectée par l’utilisateur, vous devez procéder à une configuration supplémentaire. Pour la bibliothèque AppAuthentication, consultez [Prise en charge de chaînes de connexion](/dotnet/api/overview/azure/service-to-service-authentication#connection-string-support).

    C’est tout ce dont vous avez besoin pour vous connecter à SQL Database. Lors du débogage dans Visual Studio, votre code utilise l’utilisateur Azure AD que vous avez configuré dans [Configurer Visual Studio](#set-up-visual-studio). Vous configurerez SQL Database ultérieurement pour autoriser la connexion à partir de l’identité managée de votre application App Service.

1. Entrez `Ctrl+F5` pour réexécuter l’application. La même application CRUD dans votre navigateur se connecte maintenant à Azure SQL Database directement, à l’aide de l’authentification Azure AD. Cette configuration vous permet d’exécuter des migrations de base de données à partir de Visual Studio.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/dotnetcore)

> [!NOTE]
> L’utilisation de **Microsoft.Azure.Services.AppAuthentication** n’est plus recommandée avec le nouveau SDK Azure. Elle est remplacée par une nouvelle **bibliothèque de client d’identité Azure** disponible pour .NET, Java, TypeScript et Python, qui doit être utilisée pour tout nouveau développement. Vous trouverez des informations sur la migration `Azure Identity`ici : [Guide de migration d’AppAuthentication vers Azure.Identity](/dotnet/api/overview/azure/app-auth-migration).

1. Dans Visual Studio, ouvrez la console du gestionnaire de package et ajoutez le package NuGet [Azure.Identity](https://www.nuget.org/packages/Azure.Identity) :

    ```powershell
    Install-Package Microsoft.Data.SqlClient -Version 2.1.2
    Install-Package Azure.Identity -Version 1.4.0
    ```

1. Dans le [tutoriel ASP.NET Core et SQL Database](tutorial-dotnetcore-sqldb-app.md), la chaîne de connexion `MyDbConnection` n’est pas du tout utilisée, car l’environnement de développement local utilise un fichier de base de données SQLite, et l’environnement de production Azure, une chaîne de connexion depuis App Service. Avec l’authentification Active Directory, vous voulez que les deux environnements utilisent la même chaîne de connexion. Dans le fichier *appsettings. json*, remplacez la valeur de la chaîne de connexion `MyDbConnection` par :

    ```json
    "Server=tcp:<server-name>.database.windows.net;Authentication=Active Directory Device Code Flow; Database=<database-name>;"
    ```

    > [!NOTE]
    > Nous utilisons le type d’authentification `Active Directory Device Code Flow`, car il s’agit de l’option la plus proche possible. Dans l’idéal, un type `Custom Authentication` serait disponible. Si vous n’utilisez pas un terme à ce stade, nous utilisons `Device Code Flow`.
    >

1. Ensuite, vous devez créer une classe de fournisseur d’authentification personnalisée pour acquérir et fournir le contexte de base de données Entity Framework avec le jeton d’accès pour le SQL Database. Dans le répertoire de *données\\* , ajoutez une nouvelle classe `CustomAzureSQLAuthProvider.cs` avec le code suivant à l’intérieur :

    ```csharp
    public class CustomAzureSQLAuthProvider : SqlAuthenticationProvider
    {
        private static readonly string[] _azureSqlScopes = new[]
        {
            "https://database.windows.net//.default"
        };
    
        private static readonly TokenCredential _credential = new DefaultAzureCredential();
    
        public override async Task<SqlAuthenticationToken> AcquireTokenAsync(SqlAuthenticationParameters parameters)
        {
            var tokenRequestContext = new TokenRequestContext(_azureSqlScopes);
            var tokenResult = await _credential.GetTokenAsync(tokenRequestContext, default);
            return new SqlAuthenticationToken(tokenResult.Token, tokenResult.ExpiresOn);
        }
    
        public override bool IsSupported(SqlAuthenticationMethod authenticationMethod) => authenticationMethod.Equals(SqlAuthenticationMethod.ActiveDirectoryDeviceCodeFlow);
    }
    ```

1. Dans *Startup.cs*, mettez à jour la méthode `ConfigureServices()` en la remplaçant par le code suivant :

    ```csharp
    services.AddControllersWithViews();
    services.AddDbContext<MyDatabaseContext>(options =>
    {
        SqlAuthenticationProvider.SetProvider(
            SqlAuthenticationMethod.ActiveDirectoryDeviceCodeFlow, 
            new CustomAzureSQLAuthProvider());
        var sqlConnection = new SqlConnection(Configuration.GetConnectionString("MyDbConnection"));
        options.UseSqlServer(sqlConnection);
    });
    ```

    > [!NOTE]
    > Par souci de clarté et de simplicité, ce code de démonstration est synchrone.
    
    Le code précédent utilise la bibliothèque `Azure.Identity` afin qu’il puisse s’authentifier et récupérer un jeton d’accès pour la base de données, quel que soit l’emplacement d’exécution du code. Si vous l’exécutez sur votre ordinateur local, `DefaultAzureCredential()` parcourt un certain nombre d’options pour trouver un compte valide qui est connecté. Vous pouvez en savoir plus sur la [classe DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential).

    C’est tout ce dont vous avez besoin pour vous connecter à SQL Database. Lors du débogage dans Visual Studio, votre code utilise l’utilisateur Azure AD que vous avez configuré dans [Configurer Visual Studio](#set-up-visual-studio). Vous configurerez SQL Database ultérieurement pour autoriser la connexion à partir de l’identité managée de votre application App Service. La classe `DefaultAzureCredential` met en cache le jeton en mémoire et le récupère à partir d’Azure AD, juste avant l’expiration. Vous n’avez pas besoin de code personnalisé pour actualiser le jeton.

1. Entrez `Ctrl+F5` pour réexécuter l’application. La même application CRUD dans votre navigateur se connecte maintenant à Azure SQL Database directement, à l’aide de l’authentification Azure AD. Cette configuration vous permet d’exécuter des migrations de base de données à partir de Visual Studio.

-----

## <a name="use-managed-identity-connectivity"></a>Utiliser la connectivité de l’identité managée

Ensuite, configurez votre application App Service pour qu’elle se connecte à SQL Database avec une identité managée attribuée par le système.

> [!NOTE]
> Même si les instructions de cette section concernent une identité affectée par le système, vous pouvez tout à fait utiliser une identité affectée par l’utilisateur. Pour cela, vous devez changer la `az webapp identity assign command` pour affecter l’identité affectée à l’utilisateur souhaité. Ensuite, lorsque vous créez l’utilisateur SQL, veillez à utiliser le nom de la ressource d’identité affectée par l’utilisateur plutôt que le nom du site.

### <a name="enable-managed-identity-on-app"></a>Activer une identité managée sur l’application

Pour activer une identité managée pour votre application Azure, utilisez la commande [az webapp identity assign](/cli/azure/webapp/identity#az_webapp_identity_assign) dans Cloud Shell. Dans la commande suivante, remplacez *\<app-name>* par votre propre valeur.

```azurecli-interactive
az webapp identity assign --resource-group myResourceGroup --name <app-name>
```

> [!NOTE]
> Pour activer l’identité managée pour un [emplacement de déploiement](deploy-staging-slots.md), ajoutez `--slot <slot-name>` et utilisez le nom de l’emplacement dans *\<slot-name>* .

Voici un exemple de sortie :

<pre>
{
  "additionalProperties": {},
  "principalId": "21dfa71c-9e6f-4d17-9e90-1d28801c9735",
  "tenantId": "72f988bf-86f1-41af-91ab-2d7cd011db47",
  "type": "SystemAssigned"
}
</pre>

### <a name="grant-permissions-to-managed-identity"></a>Accorder des autorisations à une identité managée

> [!NOTE]
> Si vous le souhaitez, vous pouvez ajouter l’identité à un [groupe Azure AD](../active-directory/fundamentals/active-directory-manage-groups.md), puis accorder l’accès à SQL Database au groupe Azure AD et non à l’identité. Par exemple, les commandes suivantes ajoutent l’identité managée de l’étape précédente à un nouveau groupe appelé _myAzureSQLDBAccessGroup_ :
> 
> ```azurecli-interactive
> groupid=$(az ad group create --display-name myAzureSQLDBAccessGroup --mail-nickname myAzureSQLDBAccessGroup --query objectId --output tsv)
> msiobjectid=$(az webapp identity show --resource-group myResourceGroup --name <app-name> --query principalId --output tsv)
> az ad group member add --group $groupid --member-id $msiobjectid
> az ad group member list -g $groupid
> ```
>

1. Dans le Cloud Shell, connectez-vous à SQL Database en utilisant la commande SQLCMD. Remplacez _\<server-name>_ par le nom de votre serveur, _\<db-name>_ par le nom de la base de données que votre application utilise, et _\<aad-user-name>_ et _\<aad-password>_ par les informations d’identification de votre utilisateur Azure AD.

    ```bash
    sqlcmd -S <server-name>.database.windows.net -d <db-name> -U <aad-user-name> -P "<aad-password>" -G -l 30
    ```

1. À l’invite SQL de la base de données souhaitée, exécutez les commandes suivantes pour accorder les autorisations dont votre application a besoin. Par exemple, 

    ```sql
    CREATE USER [<identity-name>] FROM EXTERNAL PROVIDER;
    ALTER ROLE db_datareader ADD MEMBER [<identity-name>];
    ALTER ROLE db_datawriter ADD MEMBER [<identity-name>];
    ALTER ROLE db_ddladmin ADD MEMBER [<identity-name>];
    GO
    ```

    *\<identity-name>* est le nom de l’identité managée dans Azure AD. Si l’identité est attribuée par le système, le nom sera toujours identique à celui de votre application App Service. Pour un [emplacement de déploiement](deploy-staging-slots.md), le nom de son identité attribuée par le système est *\<app-name>/slots/\<slot-name>* . Pour accorder des autorisations pour un groupe Azure AD, utilisez à la place le nom d’affichage du groupe (par exemple, *myAzureSQLDBAccessGroup*).

1. Tapez `EXIT` pour revenir à l’invite Cloud Shell.

    > [!NOTE]
    > Les services back-end des identités managées [gèrent également un cache de jetons](overview-managed-identity.md#obtain-tokens-for-azure-resources) qui met à jour le jeton d’une ressource cible uniquement lorsque celle-ci expire. Si vous faites une erreur lors de la configuration de vos autorisations SQL Database puis essayez de modifier les autorisations *après* avoir essayé d’obtenir un jeton avec votre application, vous n’obtiendrez pas un nouveau jeton avec les autorisations mises à jour tant que le jeton mis en cache n’a pas expiré.

    > [!NOTE]
    > Les identités Azure Active Directory et managées ne sont pas prises en charge pour les serveurs SQL locaux. 

### <a name="modify-connection-string"></a>Modifier la chaîne de connexion

Rappelez-vous que les modifications apportées dans *Web.config* ou *appsettings.json* fonctionnent avec l’identité managée. Par conséquent, la seule chose à faire consiste à supprimer la chaîne de connexion existante d’App Service, que Visual Studio a créée lors du premier déploiement de votre application. Utilisez la commande suivante en remplaçant *\<app-name>* par le nom de votre application.

```azurecli-interactive
az webapp config connection-string delete --resource-group myResourceGroup --name <app-name> --setting-names MyDbConnection
```

## <a name="publish-your-changes"></a>Publier vos modifications

Il ne vous reste plus qu’à publier vos modifications sur Azure.

# <a name="aspnet"></a>[ASP.NET](#tab/dotnet)

1. **Si vous êtes parti du [Tutoriel : Créer une application ASP.NET dans Azure avec SQL Database](app-service-web-tutorial-dotnet-sqldatabase.md)** , publiez vos modifications dans Visual Studio. Dans **l’Explorateur de solutions**, cliquez avec le bouton droit sur le projet **DotNetAppSqlDb**, puis sélectionnez **Publier**.

    ![Publier à partir de l’Explorateur de solutions](./media/app-service-web-tutorial-dotnet-sqldatabase/solution-explorer-publish.png)

1. Dans la page de publication, cliquez sur **Publier**. 

    > [!IMPORTANT]
    > Vérifiez que le nom de votre service d’application ne correspond à aucune [inscription d’application](../active-directory/manage-apps/add-application-portal.md) existante. Vous éviterez ainsi d’éventuels conflits d’ID de principal.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/dotnetcore)

**Si vous êtes parti du [Tutoriel : Créer une application ASP.NET Core et SQL Database dans Azure App Service](tutorial-dotnetcore-sqldb-app.md)** , publiez vos modifications avec Git, au moyen des commandes suivantes :

```bash
git commit -am "configure managed identity"
git push azure main
```

-----

Lorsque la nouvelle page web affiche votre liste des tâches, votre application se connecte à la base de données à l’aide de l’identité managée.

![Application Azure après l’activation des Migrations Code First](./media/app-service-web-tutorial-dotnet-sqldatabase/this-one-is-done.png)

Vous devriez désormais être en mesure de modifier la liste des tâches comme auparavant.

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Étapes suivantes

Vous avez appris à effectuer les opérations suivantes :

> [!div class="checklist"]
> * Activer les identités managées
> * Accorder à l’identité managée un accès à SQL Database
> * Configurer Entity Framework pour utiliser l’authentification Azure AD avec SQL Database
> * Se connecter à SQL Database à partir de Visual Studio à l’aide de l’authentification Azure AD

> [!div class="nextstepaction"]
> [Mapper un nom DNS personnalisé existant à Azure App Service](app-service-web-tutorial-custom-domain.md)

> [!div class="nextstepaction"]
> [Didacticiel : Connecter aux services Azure qui ne prennent pas en charge les identités gérées (à l’aide de Key Vault)](tutorial-connect-msi-key-vault.md)

> [!div class="nextstepaction"]
> [Didacticiel : Isoler les communications back-end avec l’intégration du réseau virtuel](tutorial-networking-isolate-vnet.md)
