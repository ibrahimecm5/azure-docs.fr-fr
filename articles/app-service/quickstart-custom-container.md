---
title: 'Démarrage rapide : Exécuter un conteneur personnalisé sur App Service'
description: Commencez à utiliser des conteneurs sur Azure App Service en déployant votre premier conteneur personnalisé.
author: msangapu-msft
ms.author: msangapu
ms.date: 06/30/2021
ms.topic: quickstart
ms.custom: devx-track-csharp
zone_pivot_groups: app-service-containers-windows-linux
ms.openlocfilehash: 02d9f115a5fbeb364719a2fc6cafb22e6ea03cf7
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131465855"
---
# <a name="run-a-custom-container-in-azure"></a>Exécuter un conteneur personnalisé dans Azure

::: zone pivot="container-windows"
[Azure App Service](overview.md) fournit des piles d’applications prédéfinies sur Windows, par exemple ASP.NET ou Node.js, exécuté sur IIS. Toutefois, les piles d’applications préconfigurées [verrouillent le système d’exploitation et empêchent un accès de bas niveau](operating-system-functionality.md). Les conteneurs Windows personnalisés n’ont pas ces restrictions et permettent aux développeurs de personnaliser entièrement les conteneurs et de fournir aux applications conteneurisées un accès complet aux fonctionnalités de Windows. 

Ce guide de démarrage rapide montre comment déployer une application ASP.NET, dans une image Windows, sur [Docker Hub](https://hub.docker.com/) à partir de Visual Studio. Vous exécutez l’application dans un conteneur personnalisé au sein d’Azure App Service.

## <a name="prerequisites"></a>Prérequis

Pour suivre ce tutoriel :

- <a href="https://hub.docker.com/" target="_blank">Créez un compte Docker Hub</a>
- <a href="https://docs.docker.com/docker-for-windows/install/" target="_blank">Installez Docker pour Windows</a>.
- <a href="/virtualization/windowscontainers/quick-start/quick-start-windows-10" target="_blank">Basculez Docker pour exécuter des conteneurs Windows</a>.
- <a href="https://www.visualstudio.com/downloads/" target="_blank">Installez Visual Studio 2019</a> avec les charges de travail **Développement web et ASP.NET** et **Développement Azure**. Si vous avez déjà installé Visual Studio 2019 :

    - Installez les dernières mises à jour dans Visual Studio en sélectionnant **Aide** > **Rechercher les mises à jour**.
    - Ajoutez les charges de travail dans Visual Studio en sélectionnant **Outils** > **Obtenir des outils et des fonctionnalités**.

## <a name="create-an-aspnet-web-app"></a>Créez une application web ASP.NET

Créez une application web ASP.NET en effectuant les étapes suivantes :

1. Ouvrez Visual Studio, puis sélectionnez **Créer un projet**.

1. Dans **Créer un projet**, recherchez et choisissez une **application web ASP.NET (.NET Framework)** pour C#, puis sélectionnez **Suivant**.

1. Dans **Configurer votre nouveau projet**, nommez l’application _myfirstazurewebapp_, puis sélectionnez **Créer**.

   ![Configurer votre projet d’application web](./media/quickstart-custom-container/configure-web-app-project-container.png)

1. Vous pouvez déployer n’importe quel type d’application web ASP.NET dans Azure. Pour ce guide de démarrage rapide, choisissez le modèle **MVC**.

1. Sélectionnez **Prise en charge de Docker**, puis vérifiez que l’option d’authentification a la valeur **Aucune authentification**. Sélectionnez **Create** (Créer).

   ![Créer une application web ASP.NET](./media/quickstart-custom-container/select-mvc-template-for-container.png)

1. Si le fichier _Dockerfile_ ne s’ouvre pas automatiquement, ouvrez-le dans **l’Explorateur de solutions**.

1. Vous avez besoin d’une [image parent prise en charge](configure-custom-container.md#supported-parent-images). Changez l’image parente en remplaçant la ligne `FROM` par le code suivant et enregistrez le fichier :

   ```dockerfile
   FROM mcr.microsoft.com/dotnet/framework/aspnet:4.7.2-windowsservercore-ltsc2019
   ```

1. Dans le menu Visual Studio, sélectionnez **Déboguer** > **Démarrer sans débogage** pour exécuter l’application web localement.

   ![Exécuter l’application localement](./media/quickstart-custom-container/local-web-app.png)

## <a name="publish-to-docker-hub"></a>Publier sur Docker Hub

1. Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur le projet **myfirstazurewebapp**, puis sélectionnez **Publier**.

1. Choisissez **App Service**, puis sélectionnez **Publier**.

1. Dans **Choisir une cible de publication**, sélectionnez **Container Registry**, **Docker Hub**, puis cliquez sur **Publier**.

   ![Publier à partir de la page de présentation du projet](./media/quickstart-custom-container/publish-to-docker-vs2019.png)

1. Indiquez vos informations d’identification de compte Docker Hub, puis sélectionnez **Enregistrer**.

   Attendez la fin du déploiement. La page **Publier** affiche désormais le nom du dépôt à utiliser plus tard.

   ![Capture d’écran mettant en évidence le nom du dépôt.](./media/quickstart-custom-container/published-docker-repository-vs2019.png)

1. Copiez ce nom de dépôt pour une utilisation ultérieure.

## <a name="create-a-windows-container-app"></a>Créer une application de conteneur Windows

1. Connectez-vous au [portail Azure]( https://portal.azure.com).

1. Sélectionnez **Créer une ressource** dans le coin supérieur gauche du Portail Azure.

1. Sous **Services populaires**, sélectionnez **Créer** sous **Application web**.

1. Dans **Créer une application web**, choisissez votre abonnement et un **Groupe de ressources**. Vous pouvez créer un groupe de ressources, le cas échéant.

1. Indiquez un nom d’application, par exemple *win-container-demo*. Choisissez **Conteneur docker** pour **Publier** et **Windows** pour **Système d’exploitation**. Sélectionnez **Suivant : Docker** pour continuer.

   ![Créer une application web pour conteneurs](media/quickstart-custom-container/create-web-app-container.png)

1. Pour **Source d’image**, choisissez **Docker Hub** et pour **Image et étiquette**, entrez le nom de dépôt que vous avez copié dans [Publier sur Docker Hub](#publish-to-docker-hub).

   ![Configurer une application web pour conteneurs](media/quickstart-custom-container/configure-web-app-container.png)

    Si vous avez une image personnalisée ailleurs pour votre application web, comme dans [Azure Container Registry](../container-registry/index.yml) ou dans un autre référentiel privé, vous pouvez le configurer ici.

1. Sélectionnez **Vérifier et créer**, **Créer**, puis attendez qu’Azure crée les ressources nécessaires.

## <a name="browse-to-the-container-app"></a>Accédez à l’application de conteneur

Lorsque l’opération Azure est terminée, une zone de notification s’affiche.

![Déploiement réussi](media/quickstart-custom-container/portal-create-finished.png)

1. Cliquez sur **Accéder à la ressource**.

1. Dans la vue d’ensemble de cette ressource, suivez le lien situé à côté de **URL**.

Une nouvelle page de navigateur s’ouvre à la page suivante :

![Démarrage de l’application conteneur Windows](media/quickstart-custom-container/app-starting.png)

Attendez quelques minutes et réessayez, jusqu'à accéder à la page d’accueil ASP.NET par défaut :

![Exécution de l’application conteneur Windows](media/quickstart-custom-container/app-running-vs.png)

**Félicitations !** Vous exécutez votre premier conteneur Windows personnalisé dans Azure App Service.

## <a name="see-container-start-up-logs"></a>Consulter les journaux d’activité de démarrage du conteneur

Le chargement du conteneur Windows peut prendre un certain temps. Pour voir la progression, accédez à l’URL suivante en remplaçant *\<app_name>* par le nom de votre application.
```
https://<app_name>.scm.azurewebsites.net/api/logstream
```

Les journaux d’activité en continu ressemblent à ceci :

```
2018-07-27T12:03:11  Welcome, you are now connected to log-streaming service.
27/07/2018 12:04:10.978 INFO - Site: win-container-demo - Start container succeeded. Container: facbf6cb214de86e58557a6d073396f640bbe2fdec88f8368695c8d1331fc94b
27/07/2018 12:04:16.767 INFO - Site: win-container-demo - Container start complete
27/07/2018 12:05:05.017 INFO - Site: win-container-demo - Container start complete
27/07/2018 12:05:05.020 INFO - Site: win-container-demo - Container started successfully
```

## <a name="update-locally-and-redeploy"></a>Mise à jour locale et redéploiement

1. Dans Visual Studio, dans l’**Explorateur de solutions**, ouvrez **Vues** > **Accueil** > **Index.cshtml**.

1. Recherchez la balise HTML `<div class="jumbotron">` vers le début, puis remplacez la totalité de l’élément par le code suivant :

   ```html
   <div class="jumbotron">
       <h1>ASP.NET in Azure!</h1>
       <p class="lead">This is a simple app that we've built that demonstrates how to deploy a .NET app to Azure App Service.</p>
   </div>
   ```

1. Pour effectuer un redéploiement sur Azure, cliquez avec le bouton droit sur le projet **myfirstazurewebapp** dans l’**Explorateur de solutions**, puis choisissez **Publier**.

1. Dans la page de publication, sélectionnez **Publier** et attendez la fin de la publication.

1. Pour indiquer à App Service qu’il faut tirer la nouvelle image de Docker Hub, redémarrez l’application. De retour dans la page d’application du portail, cliquez sur **Redémarrer** > **Oui**.

   ![Redémarrer l’application web dans Azure](./media/quickstart-custom-container/portal-restart-app.png)

[Accédez de nouveau à l’application de conteneur](#browse-to-the-container-app). Quand vous actualisez la page web, l’application doit d’abord revenir à la page « Démarrage », puis afficher quelques minutes après la page web mise à jour.

![Application web mise à jour dans Azure](./media/quickstart-custom-container/azure-web-app-updated.png)

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Migrer vers un conteneur Windows dans Azure](tutorial-custom-container.md)

Ou consultez les autres ressources :

> [!div class="nextstepaction"]
> [Configurer un conteneur personnalisé](configure-custom-container.md)

::: zone-end  

::: zone pivot="container-linux"
App Service sur Linux fournit des piles d’applications prédéfinies sur Linux avec la prise en charge de langages comme .NET, PHP, Node.js, etc. Vous pouvez également utiliser une image Docker personnalisée pour exécuter votre application web sur une pile d’applications qui n’est pas encore définie dans Azure. Ce guide de démarrage rapide vous montre comment déployer une image à partir d’un [ACR (Azure Container Registry)](../container-registry/index.yml) vers App Service.

## <a name="prerequisites"></a>Prérequis

* un [compte Azure](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-docker-extension&mktingSource=vscode-tutorial-docker-extension) ;
* [Docker](https://www.docker.com/community-edition)
* [Visual Studio Code](https://code.visualstudio.com/)
* L’[extension Azure App Service pour VS Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice). Vous pouvez utiliser cette extension pour créer, gérer et déployer des applications web Linux sur la plateforme PaaS (Platform as a Service) Azure.
* L’[extension Docker pour VS Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-docker). Vous pouvez utiliser cette extension pour simplifier la gestion des images et commandes Docker locales, ainsi que pour déployer des images d’application générées sur Azure.

## <a name="create-a-container-registry"></a>Créer un registre de conteneurs

Ce guide de démarrage rapide utilise Azure Container Registry comme registre de choix. Vous pouvez utiliser d’autres registres, mais la procédure risque de légèrement différer.

Créez un registre de conteneurs en suivant les instructions de la section [Démarrage rapide : Créer un registre de conteneurs privé à l’aide du portail Azure](../container-registry/container-registry-get-started-portal.md).

> [!IMPORTANT]
> Veillez à définir l’option **Utilisateur administrateur**  sur **Activer** lorsque vous créez le registre de conteneurs. Vous pouvez également la définir à partir de la section **Clés d’accès** de votre page de registre dans le portail Azure. Ce paramètre est nécessaire pour l’accès à App Service. Pour l’identité managée, consultez le [tutoriel Déployer à partir d’ACR](tutorial-custom-container.md?pivots=container-linux#configure-app-service-to-deploy-the-image-from-the-registry).

## <a name="sign-in"></a>Se connecter

1. Lancez Visual Studio Code. 
1. Sélectionnez le logo **Azure** dans la [barre d’activités](https://code.visualstudio.com/docs/getstarted/userinterface), accédez à l’explorateur **APP SERVICE**, puis sélectionnez **Se connecter à Azure** et suivez les instructions qui s’affichent.

    ![se connecter à Azure](./media/quickstart-docker/sign-in.png)

1. Dans la [barre d’état](https://code.visualstudio.com/docs/getstarted/userinterface) en bas, vérifiez l’adresse e-mail de votre compte Azure. Dans l’explorateur **APP SERVICE**, votre abonnement doit s’afficher.

1. Dans la barre d’activités, sélectionnez le logo **Docker** . Dans l’explorateur **REGISTRES**, vérifiez que le registre de conteneurs que vous avez créé s’affiche.

    ![Capture d’écran montrant la valeur des registres avec Azure développé.](./media/quickstart-docker/registries.png)

## <a name="check-prerequisites"></a>Vérifier les conditions préalables

Vérifiez que Docker est installé et en cours d’exécution. La commande suivante affiche la version de Docker s’il est en cours d’exécution.

```bash
docker --version
```

## <a name="create-and-build-image"></a>Créer et générer une image

1. Dans Visual Studio Code, ouvrez un dossier vide et ajoutez un fichier appelé `Dockerfile`. Dans le fichier Dockerfile, collez le contenu en fonction de l’infrastructure de langage de votre choix :

# <a name="net"></a>[.NET](#tab/dotnet)

<!-- https://mcr.microsoft.com/v2/appsvc%2Fdotnetcore/tags/list -->
```dockerfile
FROM mcr.microsoft.com/appsvc/dotnetcore:lts

ENV PORT 8080
EXPOSE 8080

ENV ASPNETCORE_URLS "http://*:${PORT}"

ENTRYPOINT ["dotnet", "/defaulthome/hostingstart/hostingstart.dll"]
```

Dans ce fichier Dockerfile, l’image parente correspond à l’un des conteneurs .NET intégrés d’App Service. Les fichiers sources correspondants sont disponibles [dans le référentiel GitHub Azure-App-Service/ImageBuilder, sous GenerateDockerFiles/dotnetcore](https://github.com/Azure-App-Service/ImageBuilder/tree/master/GenerateDockerFiles/dotnetcore). Son [fichier Dockerfile](https://github.com/Azure-App-Service/ImageBuilder/blob/master/GenerateDockerFiles/dotnetcore/debian-9/Dockerfile) copie une application simple .NET dans `/defaulthome/hostingstart`. Votre fichier Dockerfile démarre simplement cette application.

# <a name="nodejs"></a>[Node.JS](#tab/node)

<!-- https://mcr.microsoft.com/v2/appsvc%2Fnode/tags/list -->
```dockerfile
FROM mcr.microsoft.com/appsvc/node:10-lts

ENV HOST 0.0.0.0
ENV PORT 8080
EXPOSE 8080

ENTRYPOINT ["pm2", "start", "--no-daemon", "/opt/startup/default-static-site.js"]
```

Dans ce fichier Dockerfile, l’image parente correspond à l’un des conteneurs Node.js intégrés d’App Service. Les fichiers sources correspondants sont disponibles [dans le référentiel GitHub Azure-App-Service/ImageBuilder, sous GenerateDockerFiles/node/node-template](https://github.com/Azure-App-Service/ImageBuilder/tree/master/GenerateDockerFiles/node/node-template). Son [fichier Dockerfile](https://github.com/Azure-App-Service/ImageBuilder/blob/master/GenerateDockerFiles/node/node-template/Dockerfile) copie une application simple Node.js dans `/opt/startup`. Votre fichier Dockerfile démarre simplement cette application à l’aide de PM2, qui est déjà installé par l’image parente.

# <a name="python"></a>[Python](#tab/python)

<!-- https://mcr.microsoft.com/v2/appsvc%2Fpython/tags/list -->
```dockerfile
FROM mcr.microsoft.com/appsvc/python:latest

ENV PORT 8080
EXPOSE 8080

ENTRYPOINT ["gunicorn", "--timeout", "600", "--access-logfile", "'-'", "--error-logfile", "'-'", "--chdir=/opt/defaultsite", "application:app"]
```

Dans ce fichier Dockerfile, l’image parente correspond à l’un des conteneurs Python intégrés d’App Service. Les fichiers sources correspondants sont disponibles [dans le référentiel GitHub Azure-App-Service/ImageBuilder, sous GenerateDockerFiles/python/template-3.9](https://github.com/Azure-App-Service/ImageBuilder/tree/master/GenerateDockerFiles/python/template-3.9). Son [fichier Dockerfile](https://github.com/Azure-App-Service/ImageBuilder/blob/master/GenerateDockerFiles/python/template-3.9/Dockerfile) copie une application simple Python dans `/opt/defaultsite`. Votre fichier Dockerfile démarre simplement cette application à l’aide de Gunicorn, qui est déjà installé par l’image parente.

# <a name="java"></a>[Java](#tab/java)

<!-- https://mcr.microsoft.com/v2/azure-app-service%2Fjava/tags/list -->
```dockerfile
FROM mcr.microsoft.com/azure-app-service/java:11-java11_stable

ENV PORT 80
EXPOSE 80

ENTRYPOINT ["java", "-Dserver.port=80", "-jar", "/tmp/appservice/parkingpage.jar"]
```

Dans ce fichier Dockerfile, l’image parente correspond à l’un des conteneurs Java intégrés d’App Service. Les fichiers sources correspondants sont disponibles [dans le référentiel GitHub Azure-App-Service/java, sous java/tree/dev/java11-alpine](https://github.com/Azure-App-Service/java/tree/dev/java11-alpine). Son [fichier Dockerfile](https://github.com/Azure-App-Service/java/blob/dev/java11-alpine/Dockerfile) copie une application simple Java dans `/tmp/appservice`. Votre fichier Dockerfile démarre simplement cette application.

-----

2. [Ouvrez la palette de commandes](https://code.visualstudio.com/docs/getstarted/userinterface#_command-palette), puis saisissez **Images Docker : Générer une image**. Appuyez sur **Entrée** pour exécuter la commande.

3. Dans la zone d’étiquette d’image, spécifiez l’étiquette souhaitée au format suivant : `<acr-name>.azurecr.io/<image-name>/<tag>`, où `<acr-name>` correspond au nom du registre de conteneurs que vous avez créé. Appuyez sur **Entrée**.

4. Une fois la génération de l’image terminée, cliquez sur **Actualiser** en haut de l’explorateur **IMAGES** et vérifiez que l’image a bien été générée.

    ![Capture d’écran montrant l’image générée avec l’étiquette.](./media/quickstart-docker/built-image.png)

## <a name="deploy-to-container-registry"></a>Déployer dans le registre de conteneurs

1. Dans la barre d’activité, cliquez sur l’icône **Docker**. Dans l’explorateur **IMAGES** , recherchez l’image que vous venez de générer.
1. Développez l’image, cliquez avec le bouton droit sur l’étiquette souhaitée, puis cliquez sur **Push (envoyer)** .
1. Assurez-vous que l’étiquette d’image commence par `<acr-name>.azurecr.io` et appuyez sur **Entrée**.
1. Quand Visual Studio Code finit d’envoyer (push) l’image vers votre registre de conteneurs, cliquez sur **Actualiser** en haut de l’explorateur **REGISTRES** et vérifiez que l’image a bien été envoyée (push).

    ![Capture d’écran montrant l’image déployée dans Azure Container Registry.](./media/quickstart-docker/image-in-registry.png)

## <a name="deploy-to-app-service"></a>Déployer sur App Service

1. Dans l’explorateur **REGISTRES**, développez l’image, cliquez avec le bouton droit sur l’étiquette, puis cliquez sur **Déployer l’image sur Azure App Service**.
1. Suivez les invites pour choisir un abonnement, un nom d’application global unique, un groupe de ressources et un plan App Service. Choisissez **B1 De base** comme niveau tarifaire et une région près de vous.

Une fois le déploiement effectué, votre application est disponible à l’adresse `http://<app-name>.azurewebsites.net`.

Un **groupe de ressources** est une collection nommée de toutes les ressources de votre application dans Azure. Par exemple, un groupe de ressources peut contenir une référence à un site web, une base de données et une fonction Azure.

Un **plan App Service** définit les ressources physiques qui seront utilisées pour héberger votre site web. Ce guide de démarrage rapide utilise un plan d’hébergement **De base** sur l’infrastructure **Linux**, ce qui signifie que le site sera hébergé sur une machine Linux avec d’autres sites web. Si vous commencez avec le plan **De base**, vous pouvez utiliser le portail Azure pour effectuer un scale-up afin que votre site soit le seul à s’exécuter sur un ordinateur. Pour plus d’informations sur la tarification, consultez [Tarification d’App Service](https://azure.microsoft.com/pricing/details/app-service/linux).

## <a name="browse-the-website"></a>Parcourir le site Web

Le panneau **Sortie** indique l’état des opérations de déploiement. Une fois l’opération terminée, cliquez sur **Ouvrir le site** dans la notification contextuelle pour ouvrir le site dans votre navigateur.

> [!div class="nextstepaction"]
> [J’ai rencontré un problème](https://www.research.net/r/PWZWZ52?tutorial=quickstart-docker&step=deploy-app)

## <a name="next-steps"></a>Étapes suivantes

Félicitations, vous avez terminé ce démarrage rapide.

L’application App Service extrait l’image à partir du registre de conteneurs à chaque fois qu’elle démarre. Pour regénérer votre image, il vous suffit de la transmettre à votre registre de conteneurs, après quoi l’application procédera à l’extraciton de l’image mise à jour lors au moment de son redémarrage. Pour indiquer à votre application d’extraire immédiatement l’image mise à jour, redémarrez-la.

> [!div class="nextstepaction"]
> [Configurer un conteneur personnalisé](configure-custom-container.md)

> [!div class="nextstepaction"]
> [Tutoriel relatif aux conteneurs personnalisés](tutorial-custom-container.md)

> [!div class="nextstepaction"]
> [Tutoriel relatif aux applications à plusieurs conteneurs](tutorial-multi-container-app.md)

Autres extensions Azure :

* [Cosmos DB](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)
* [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)
* [Outils Azure CLI](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli)
* [Outils Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
* Le pack d’extension [Azure Tools](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) comprend toutes les extensions ci-dessus.

::: zone-end
