---
title: 'Tutoriel : Application ASP.NET avec Azure SQL Database'
description: Découvrez comment déployer une application ASP.NET C# sur Azure et Azure SQL Database.
ms.assetid: 03c584f1-a93c-4e3d-ac1b-c82b50c75d3e
ms.devlang: csharp
ms.topic: tutorial
ms.date: 11/08/2021
ms.custom: devx-track-csharp, mvc, devcenter, vs-azure, seodec18
ms.openlocfilehash: 72ea37f483055ae3bcb97520efa7f2a4fb704037
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132058860"
---
# <a name="tutorial-deploy-an-aspnet-app-to-azure-with-azure-sql-database"></a>Tutoriel : Déployer une application ASP.NET sur Azure avec Azure SQL Database

[Azure App Service](overview.md) offre un service d’hébergement web hautement évolutif appliquant des mises à jour correctives automatiques. Ce tutoriel montre comment déployer une application ASP.NET basée sur des données dans App Service et comment la connecter à [Azure SQL Database](../azure-sql/database/sql-database-paas-overview.md). Quand vous avez terminé, vous disposez d’une application ASP.NET s’exécutant dans Azure et connectée à SQL Database.

![Application ASP.NET publiée dans Azure App Service](./media/app-service-web-tutorial-dotnet-sqldatabase/azure-app-in-browser.png)

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
>
> * Créer une base de données dans Azure SQL Database.
> * Connecter une application ASP.NET à SQL Database
> * Déploiement de l’application dans Azure
> * Mise à jour du modèle de données et redéploiement de l’application
> * Diffuser des journaux d’activité à partir d’Azure vers votre terminal
> * Gérer l’application dans le portail Azure

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prérequis

Pour suivre ce tutoriel :

Installez <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2022</a> avec la charge de travail **Développement ASP.NET et web**.

Si vous avez déjà installé Visual Studio, ajoutez les charges de travail dans Visual Studio en cliquant sur **Outils** > **Obtenir des outils et des fonctionnalités**.

## <a name="download-the-sample"></a>Télécharger l’exemple

1. [Téléchargez l’exemple de projet](https://github.com/Azure-Samples/dotnet-sqldb-tutorial/archive/master.zip).

1. Extrayez (décompressez) le fichier *dotnet-sqldb-tutorial-master.zip*.

Cet exemple de projet contient une simple application CRUD (Create-Read-Update-Delete) [ASP.NET MVC](https://www.asp.net/mvc) basée sur [Entity Framework Code First](/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application).

### <a name="run-the-app"></a>Exécuter l’application

1. Ouvrez le fichier *dotnet-sqldb-tutorial-master/DotNetAppSqlDb.sln* dans Visual Studio.

1. Entrez `Ctrl+F5` pour exécuter l’application sans débogage. L’application s’affiche dans votre navigateur par défaut.

1. Sélectionnez le lien **Create New** et créez quelques éléments *to-do*.

    ![Boîte de dialogue New ASP.NET Project](media/app-service-web-tutorial-dotnet-sqldatabase/local-app-in-browser.png)

1. Testez les liens **Edit**, **Details** et **Delete**.

L’application utilise un contexte de base de données pour se connecter à la base de données. Dans cet exemple, le contexte de base de données utilise une chaîne de connexion appelée `MyDbConnection`. Cette chaîne de connexion est définie dans le fichier *Web.config* et référencée dans le fichier *Models\MyDatabaseContext.cs*. Utilisé plus loin dans ce tutoriel, le nom de chaîne de connexion permet de connecter l’application Azure à une base de données Azure SQL.

## <a name="publish-aspnet-application-to-azure"></a>Publier l’application ASP.NET sur Azure

1. Dans **l’Explorateur de solutions**, cliquez avec le bouton droit sur le projet **DotNetAppSqlDb**, puis sélectionnez **Publier**.

    ![Publier à partir de l’Explorateur de solutions](./media/app-service-web-tutorial-dotnet-sqldatabase/solution-explorer-publish.png)

1. Sélectionnez **Azure** comme cible, puis cliquez sur **Suivant**.

1. Vérifiez que **Azure App Service (Windows)** est sélectionné et cliquez sur **Suivant**.

#### <a name="sign-in-and-add-an-app"></a>Se connecter et ajouter une application

1. Dans la boîte de dialogue **Publier**, cliquez sur **Se connecter**.

1. Connectez-vous à votre abonnement Azure. Si vous êtes déjà connecté à un compte Microsoft, assurez-vous que le compte conserve votre abonnement Azure. Si le compte Microsoft auquel vous êtes connecté ne comporte pas votre abonnement Azure, cliquez dessus pour ajouter le compte approprié.

1. Dans le volet **Instances App Service**, cliquez sur **+** .

    ![Connexion à Azure](./media/app-service-web-tutorial-dotnet-sqldatabase/sign-in-azure.png)

#### <a name="configure-the-web-app-name"></a>Configurer le nom de l’application web

Vous pouvez conserver le nom de l’application web généré ou le remplacer par un autre nom unique (les caractères valides sont `a-z`, `0-9` et `-`). Le nom de l’application web est utilisé dans l’URL par défaut de votre application (`<app_name>.azurewebsites.net`, où `<app_name>` est le nom de votre application web). Le nom de l’application web doit être unique parmi toutes les applications dans Azure.

> [!NOTE]
> Ne sélectionnez pas encore **Créer**.

![Boîte de dialogue Créer App Service](media/app-service-web-tutorial-dotnet-sqldatabase/wan.png)

#### <a name="create-a-resource-group"></a>Créer un groupe de ressources

[!INCLUDE [resource-group](../../includes/resource-group.md)]

1. À côté de **Groupe de ressources**, cliquez sur **Nouveau**.

   ![À côté de Groupe de ressources, cliquez sur Nouveau.](media/app-service-web-tutorial-dotnet-sqldatabase/new_rg2.png)

1. Attribuez au groupe de ressources le nom **myResourceGroup**.

#### <a name="create-an-app-service-plan"></a>Créer un plan App Service

[!INCLUDE [app-service-plan](../../includes/app-service-plan.md)]

1. À côté de **Plan d’hébergement**, cliquez sur **Nouveau**.

1. Dans la boîte de dialogue **Configurer le plan App Service**, configurez le nouveau plan App Service avec les paramètres suivants, puis cliquez sur **OK** :

   | Paramètre  | Valeur suggérée | Informations supplémentaires |
   | ----------------- | ------------ | ----|
   |**Plan App Service**| myAppServicePlan | [Plans App Service](../app-service/overview-hosting-plans.md) |
   |**Lieu**| Europe Ouest | [Régions Azure](https://azure.microsoft.com/regions/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) |
   |**Taille**| Gratuit | [Niveaux de tarification](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)|

   ![Créer un plan App Service](./media/app-service-web-tutorial-dotnet-sqldatabase/configure-app-service-plan.png)

1. Cliquez sur **Créer** et attendez que les ressources Azure soient créées.

1. La boîte de dialogue **Publier** affiche les ressources que vous avez configurées. Cliquez sur **Terminer**.

   ![les ressources que vous avez créées](media/app-service-web-tutorial-dotnet-sqldatabase/app_svc_plan_done.png)


#### <a name="create-a-server-and-database"></a>Créer un serveur et une base de données

Avant de créer une base de données, vous avez besoin d’un [serveur SQL logique](../azure-sql/database/logical-servers.md). Un serveur SQL logique est une construction logique qui contient un groupe de bases de données gérées en tant que groupe.

1. Dans la boîte de dialogue **Publier**, faites défiler jusqu’à la section **Dépendances du service**. À côté de **Base de données SQL Server**, cliquez sur **Configurer**.

    > [!NOTE]
    > Veillez à configurer la base de données SQL dans la page **Publier** au lieu de la page **Services connectés**.

   ![Configurer une référence SQL Database](media/app-service-web-tutorial-dotnet-sqldatabase/configure-sqldb-dependency.png)

1. Sélectionnez **Azure SQL Database**, puis cliquez sur **Suivant**.

1. Dans la boîte de dialogue **Configurer Azure SQL Database**, cliquez sur **+**  :

1. À côté de **Serveur de base de données**, cliquez sur **Nouveau**.

   Le nom du serveur est utilisé dans l’URL par défaut de votre serveur, `<server_name>.database.windows.net`. Il doit être unique parmi tous les serveurs dans Azure SQL. Remplacez le nom du serveur par une valeur de votre choix.

1. Ajoutez un nom d’utilisateur administrateur et un mot de passe. Pour plus d’informations sur la complexité requise des mots de passe, consultez [Stratégie de mot de passe](/sql/relational-databases/security/password-policy).

   Gardez en tête ce nom d'utilisateur et ce mot de passe. Vous en aurez besoin pour gérer le serveur ultérieurement.

   ![Créer un serveur](media/app-service-web-tutorial-dotnet-sqldatabase/configure-sql-database-server.png)

   > [!IMPORTANT]
   > Même si votre mot de passe dans les chaînes de connexion est masqué (dans Visual Studio et également dans App Service), le fait qu’il soit conservé quelque part augmente la surface d’attaque de votre application. App Service peut utiliser des [identités de service managées ](overview-managed-identity.md) pour éliminer ce risque en supprimant toute nécessité de conserver les secrets dans votre configuration de code ou d’application. Pour plus d’informations, consultez [Étapes suivantes](#next-steps).

1. Cliquez sur **OK**.

1. Dans la boîte de dialogue **Azure SQL Database**, conservez le **Nom de la base de données** généré par défaut. Sélectionnez **Créer** et attendez que la ressource de base de données soit créée.

    ![Configurer la base de données](media/app-service-web-tutorial-dotnet-sqldatabase/configure-sql-database.png)

#### <a name="configure-database-connection"></a>Configurer la chaîne de connexion de base de données

1. Lorsque l’Assistant a terminé de créer les ressources de base de données, cliquez sur **Suivant**.

1. Dans **Nom de la chaîne de connexion de base de données**, tapez _MyDbConnection_. Ce nom doit correspondre à la chaîne de connexion référencée dans _Models/MyDatabaseContext.cs_.

1. Dans **Nom d’utilisateur de connexion de base de données** et **Mot de passe de connexion de base de données**, tapez le nom d’utilisateur et le mot de passe de l’administrateur que vous avez utilisés dans [Créer un serveur](#create-a-server-and-database).

1. Vérifiez que **Paramètres d’application Azure** est sélectionné et cliquez sur **Terminer**.

    > [!NOTE]
    > Si vous voyez **Fichiers des secrets de l’utilisateur local** à la place, vous avez dû configurer la base de données SQL dans la page **Services connectés** au lieu de le faire dans la page **Publier**.

    ![Configurer la chaîne de connexion de base de données](media/app-service-web-tutorial-dotnet-sqldatabase/configure-sql-database-connection.png)

1. Attendez la fin de l’Assistant Configuration, puis cliquez sur **Fermer**.

#### <a name="deploy-your-aspnet-app"></a>Déployer votre application ASP.NET

1. Sous l’onglet **Publier**, faites défiler la liste vers le haut et cliquez sur **Publier**. Une fois votre application ASP.NET déployée sur Azure. Votre navigateur par défaut démarre et se connecte à l’URL de l’application déployée.

1. Ajoutez quelques tâches.

    ![Application ASP.NET publiée dans une application Azure](./media/app-service-web-tutorial-dotnet-sqldatabase/azure-app-in-browser.png)

    Félicitations ! Votre application ASP.NET orientée données s’exécute dans Azure App Service.

## <a name="access-the-database-locally"></a>Accéder à la base de données localement

Visual Studio vous permet d’explorer et de gérer facilement votre nouvelle base de données dans Azure à l’aide de l’**Explorateur d’objets SQL Server**. La nouvelle base de données a déjà ouvert son pare-feu sur l’application App Service que vous avez créée, mais pour y accéder à partir de votre ordinateur local (par exemple à partir de Visual Studio), vous devez ouvrir un pare-feu pour l’adresse IP publique de votre ordinateur local. Si votre fournisseur de services Internet change votre adresse IP publique, vous devez reconfigurer le pare-feu pour accéder à nouveau à la base de données Azure.

#### <a name="create-a-database-connection"></a>Créer une connexion à la base de données

1. Dans le menu **Vue**, sélectionnez **Explorateur d’objets SQL Server**.

1. En haut de **l’Explorateur d’objets SQL Server**, cliquez sur le bouton **Ajouter SQL Server**.

#### <a name="configure-the-database-connection"></a>Configurer la connexion à la base de données

1. Dans la boîte de dialogue **Se connecter**, développez le nœud **Azure**. Toutes vos instances SQL Database dans Azure sont répertoriées ici.

1. Sélectionnez la base de données que vous avez créée. La connexion que vous avez créée apparaît automatiquement en bas de la page.

1. Entrez le mot de passe de l’administrateur de base de données que vous avez créé, puis cliquez sur **Se connecter**.

    ![Configurer la connexion à la base de données à partir de Visual Studio](./media/app-service-web-tutorial-dotnet-sqldatabase/connect-to-sql-database.png)

#### <a name="allow-client-connection-from-your-computer"></a>Autoriser une connexion client à partir de votre ordinateur

La boîte de dialogue **Créer une nouvelle règle de pare-feu** s’ouvre. Par défaut, un serveur autorise les connexions à ses bases de données uniquement à partir de services Azure, comme votre application Azure. Pour vous connecter à votre base de données en dehors d’Azure, créez une règle de pare-feu au niveau du serveur. Cette règle de pare-feu autorise l’adresse IP publique de votre ordinateur local.

La boîte de dialogue contient déjà l’adresse IP de votre ordinateur.

1. Assurez-vous que la case **Ajouter mon adresse IP cliente** est cochée, puis cliquez sur **OK**.

    ![Créer une règle de pare-feu](./media/app-service-web-tutorial-dotnet-sqldatabase/sql-set-firewall.png)

    Lorsque Visual Studio a créé le paramètre de pare-feu pour votre instance SQL Database, votre connexion s’affiche dans l’**Explorateur d’objets SQL Server**.

    Ici, vous pouvez effectuer les opérations de base de données les plus courantes, par exemple exécuter des requêtes, créer des vues et des procédures stockées, etc.

1. Développez votre connexion > **Bases de données** >  **&lt;votre base de données >**  > **Tables**. Cliquez avec le bouton droit sur la table `Todoes`, puis sélectionnez **Afficher les données**.

    ![Explorer les objets SQL Database](./media/app-service-web-tutorial-dotnet-sqldatabase/explore-sql-database.png)

## <a name="update-app-with-code-first-migrations"></a>Mettre à jour l’application avec les Code First Migrations

Vous pouvez utiliser les outils que vous connaissez dans Visual Studio pour mettre à jour votre base de données et votre application dans Azure. Dans cette étape, vous allez utiliser des Code First Migrations dans Entity Framework pour apporter une modification à votre schéma de base de données et la publier sur Azure.

Pour plus d’informations sur l’utilisation de Migrations Entity Framework Code First, consultez [Getting Started with Entity Framework 6 Code First using MVC 5 (Mise en route avec Entity Framework 6 Code First à l’aide de MVC 5)](/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application).

#### <a name="update-your-data-model"></a>Mettre à jour votre modèle de données

Ouvrez _Models\Todo.cs_ dans l’éditeur de code. Ajoutez la propriété suivante à la classe `ToDo` :

```csharp
public bool Done { get; set; }
```
    
#### <a name="run-code-first-migrations-locally"></a>Exécuter la fonction Code First Migrations en local

Exécutez quelques commandes pour mettre à jour votre base de données locale.

1. Dans le menu **Outils**, cliquez sur **Gestionnaire de package NuGet** > **Console du Gestionnaire de package**.

1. Dans la fenêtre de Console du Gestionnaire de package, activez les Migrations Code First :

    ```powershell
    Enable-Migrations
    ```
    
1. Ajoutez une migration :

    ```powershell
    Add-Migration AddProperty
    ```
    
1. Mettez à jour la base de données locale :

    ```powershell
    Update-Database
    ```
    
1. Entrez `Ctrl+F5` pour exécuter l’application. Testez les liens de modification, de détails et de création.

Si l’application se charge sans erreur, cela signifie que la fonction Code First Migrations a réussi. Votre page garde cependant le même aspect car votre logique d’application n’utilise pas encore cette nouvelle propriété.

#### <a name="use-the-new-property"></a>Utiliser la nouvelle propriété

Apportez quelques modifications à votre code pour utiliser la propriété `Done`. Pour plus de simplicité dans ce didacticiel, vous allez uniquement modifier les vues `Index` et `Create` pour voir la propriété en action.

1. Ouvrez _Controllers\TodosController.cs_.

1. Recherchez la méthode `Create()` à la ligne 52 et ajoutez `Done` à la liste des propriétés dans l’attribut `Bind`. Lorsque vous avez terminé, la signature de votre méthode `Create()` doit ressembler à ceci :

    ```csharp
    public ActionResult Create([Bind(Include = "Description,CreatedDate,Done")] Todo todo)
    ```
    
1. Ouvrez _Views\Todos\Create.cshtml_.

1. Dans le code Razor, vous devriez voir un élément `<div class="form-group">` qui utilise `model.Description` et un autre élément `<div class="form-group">` qui utilise `model.CreatedDate`. Juste après ces deux éléments, ajoutez un autre élément `<div class="form-group">` qui utilise `model.Done` :

    ```csharp
    <div class="form-group">
        @Html.LabelFor(model => model.Done, htmlAttributes: new { @class = "control-label col-md-2" })
        <div class="col-md-10">
            <div class="checkbox">
                @Html.EditorFor(model => model.Done)
                @Html.ValidationMessageFor(model => model.Done, "", new { @class = "text-danger" })
            </div>
        </div>
    </div>
    ```
    
1. Ouvrez _Views\Todos\Index.cshtml_.

1. Recherchez l’élément `<th></th>` vide. Juste au-dessus de cet élément, ajoutez le code Razor suivant :

    ```csharp
    <th>
        @Html.DisplayNameFor(model => model.Done)
    </th>
    ```
    
1. Recherchez l’élément `<td>` contenant les méthodes d’assistance `Html.ActionLink()`. _Au-dessus de_ ce `<td>`, ajoutez un autre élément `<td>` avec le code Razor suivant :

    ```csharp
    <td>
        @Html.DisplayFor(modelItem => item.Done)
    </td>
    ```
    
    Voilà tout ce que vous avez à faire pour voir les modifications dans les vues `Index` et `Create`.

1. Entrez `Ctrl+F5` pour exécuter l’application.

Vous pouvez maintenant ajouter un élément de tâche et cocher **Terminé**. Cette tâche devrait ensuite apparaître dans votre page d’accueil comme un élément terminé. N’oubliez pas que la vue `Edit` n’affiche pas le champ `Done`, car vous n’avez pas modifié la vue `Edit`.

#### <a name="enable-code-first-migrations-in-azure"></a>Activer Code First Migrations dans Azure

Maintenant que votre modification de code fonctionne, y compris la migration de la base de données, vous allez la publier dans votre application Azure et mettre également à jour votre instance SQL Database avec les Migrations Code First.

1. Comme précédemment, cliquez avec le bouton droit sur votre projet et sélectionnez **Publier**.

1. Cliquez sur **Autres actions** > **Modifier** pour ouvrir les paramètres de publication.

    ![Ouvrir les paramètres de publication](./media/app-service-web-tutorial-dotnet-sqldatabase/publish-settings.png)

1. Dans la liste déroulante **MyDatabaseContext**, sélectionnez la connexion de base de données pour votre base de données Azure SQL.

1. Sélectionnez **Exécuter les migrations Code First (s’exécute au démarrage de l’application)** , puis cliquez sur **Enregistrer**.

    ![Activer les Migrations Code First dans l’application Azure](./media/app-service-web-tutorial-dotnet-sqldatabase/enable-migrations.png)

#### <a name="publish-your-changes"></a>Publier vos modifications

Maintenant que vous avez activé les Migrations Code First dans votre application Azure, publiez vos modifications du code.

1. Dans la page de publication, cliquez sur **Publier**.

1. Essayez d’ajouter à nouveau des tâches et de sélectionner **Terminé** : elles doivent apparaître dans votre page d’accueil comme éléments terminés.

    ![Application Azure après l’activation des Migrations Code First](./media/app-service-web-tutorial-dotnet-sqldatabase/this-one-is-done.png)

Toutes les tâches existantes sont toujours affichées. Lorsque vous republiez votre application ASP.NET, les données existantes dans votre instance SQL Database ne sont pas perdues. En outre, Code First Migrations modifie uniquement le schéma de données, sans toucher à vos données existantes.

## <a name="stream-application-logs"></a>Diffuser les journaux d’activité d’applications

Vous pouvez diffuser des messages de suivi directement entre votre application Azure et Visual Studio.

Ouvrez _Controllers\TodosController.cs_.

Chaque action commence par une méthode `Trace.WriteLine()`. Ce code est ajouté pour vous montrer comment ajouter des messages de suivi à votre application Azure.

#### <a name="enable-log-streaming"></a>Activer la diffusion de journaux

1. Dans le menu **Affichage**, sélectionnez **Cloud Explorer**.

1. Dans **Cloud Explorer**, développez l’abonnement Azure qui contient votre application et développez **App Service**.

1. Cliquez avec le bouton droit sur votre application Azure et sélectionnez **Afficher les journaux d’activité de streaming**.

    ![Activer la diffusion de journaux](./media/app-service-web-tutorial-dotnet-sqldatabase/stream-logs.png)

    Les journaux d’activité sont maintenant transmis à la fenêtre **Sortie**.

    ![Diffusion des journaux dans la fenêtre Sortie](./media/app-service-web-tutorial-dotnet-sqldatabase/log-streaming-pane.png)

    Vous ne pourrez cependant pas visualiser les messages de suivi à ce stade. La raison est simple : quand vous sélectionnez **Afficher les journaux d’activité de streaming**, votre application Azure définit le niveau de suivi sur `Error`, ce qui enregistre uniquement les événements d’erreur (avec la méthode `Trace.TraceError()`).

#### <a name="change-trace-levels"></a>Modifier les niveaux de suivi

1. Pour changer les niveaux de suivi afin de générer d’autres messages de suivi, revenez à **Cloud Explorer**.

1. Cliquez à nouveau avec le bouton droit sur votre application, puis sélectionnez **Ouvrir dans le portail**.

1. Dans la page de gestion de votre application du portail, dans le menu de gauche, sélectionnez **Journaux App Service**.

1. Sous **Journal des applications (système de fichiers)** , sélectionnez **Commentaires** dans **Niveau**. Cliquez sur **Enregistrer**.

    ![Définir le niveau de suivi sur Détaillé](./media/app-service-web-tutorial-dotnet-sqldatabase/trace-level-verbose.png)

    > [!TIP]
    > Vous pouvez expérimenter différents niveaux de suivi pour connaître les types de messages qui s’affichent pour chaque niveau. Par exemple, le niveau **Informations** inclut tous les journaux d’activité créés par `Trace.TraceInformation()`, `Trace.TraceWarning()`, et `Trace.TraceError()`, mais pas les journaux d’activité créés par `Trace.WriteLine()`.

1. Dans votre navigateur, accédez à nouveau à votre application à *http://&lt;nom_de_votre_application>.azurewebsites.net*, puis essayez de cliquer autour de l’application de la liste des tâches dans Azure. Les messages de suivi sont maintenant diffusés vers la fenêtre **Sortie** dans Visual Studio.

    ```console
    Application: 2017-04-06T23:30:41  PID[8132] Verbose     GET /Todos/Index
    Application: 2017-04-06T23:30:43  PID[8132] Verbose     GET /Todos/Create
    Application: 2017-04-06T23:30:53  PID[8132] Verbose     POST /Todos/Create
    Application: 2017-04-06T23:30:54  PID[8132] Verbose     GET /Todos/Index
    ```
    
#### <a name="stop-log-streaming"></a>Arrêter la diffusion de journaux

Pour arrêter le service de diffusion de journaux, cliquez sur le bouton **Arrêter l’analyse** situé dans la fenêtre **Sortie**.

![Arrêter la diffusion de journaux](./media/app-service-web-tutorial-dotnet-sqldatabase/stop-streaming.png)

## <a name="manage-your-azure-app"></a>Gérer votre application Azure

Accédez au [Portail Azure](https://portal.azure.com) pour gérer l’application web. Recherchez et sélectionnez **App Services**.

![Rechercher Azure App Services](./media/app-service-web-tutorial-dotnet-sqldatabase/azure-portal-navigate-app-services.png)

Sélectionnez le nom de votre application Azure.

![Navigation au sein du portail pour accéder à l’application Azure](./media/app-service-web-tutorial-dotnet-sqldatabase/access-portal.png)

Vous accédez à la page de votre application.

Par défaut, le portail affiche la page **Vue d’ensemble**. Cette page propose un aperçu de votre application. Ici, vous pouvez également effectuer des tâches de gestion de base (parcourir, arrêter, démarrer, redémarrer et supprimer des éléments, par exemple). Les onglets sur le côté gauche de la page affichent les différentes pages de configuration que vous pouvez ouvrir.

![Page App Service du Portail Azure](./media/app-service-web-tutorial-dotnet-sqldatabase/web-app-blade.png)

[!INCLUDE [Clean up section](../../includes/clean-up-section-portal-web-app.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à :

> [!div class="checklist"]
>
> * Créer une base de données dans Azure SQL Database.
> * Connecter une application ASP.NET à SQL Database
> * Déploiement de l’application dans Azure
> * Mise à jour du modèle de données et redéploiement de l’application
> * Diffuser des journaux d’activité à partir d’Azure vers votre terminal
> * Gérer l’application dans le portail Azure

Passez au tutoriel suivant pour apprendre à améliorer facilement la sécurité de votre connexion à la base de données Azure SQL.

> [!div class="nextstepaction"]
> [Accéder à Azure SQL Database en toute sécurité à l’aide d’identités managées pour les ressources Azure](tutorial-connect-msi-sql-database.md)

Autres ressources :

> [!div class="nextstepaction"]
> [Configurer une application ASP.NET](configure-language-dotnet-framework.md)

Vous souhaitez optimiser et réduire vos coûts de cloud ?

> [!div class="nextstepaction"]
> [Démarrer l’analyse des coûts avec Cost Management](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)