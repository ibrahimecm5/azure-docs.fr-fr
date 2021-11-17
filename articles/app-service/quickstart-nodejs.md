---
title: 'Démarrage rapide : Créer une application web Node.js'
description: Déployez votre premier programme Hello World Node.js dans Azure App Service en quelques minutes.
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.topic: quickstart
ms.date: 09/14/2021
ms.custom: mvc, devcenter, seodec18, mode-portal
zone_pivot_groups: app-service-vscode-cli-portal
ms.openlocfilehash: 70d98c6aed2e8f561aeb40e4e60fdeab603f58c6
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132524053"
---
# <a name="create-a-nodejs-web-app-in-azure"></a>Créer une application web Node.js dans Azure

Dans ce guide de démarrage rapide, vous allez apprendre à créer et déployer votre première application web Node.js ([Express](https://www.expressjs.com)) sur [Azure App Service](overview.md). App Service prend en charge diverses versions de Node.js à la fois sur Linux et Windows. 

Ce guide de démarrage rapide configure une application App Service dans le niveau **Gratuit** et n’implique aucun coût pour votre abonnement Azure.

## <a name="set-up-your-initial-environment"></a>Configurer votre environnement initial

:::zone target="docs" pivot="development-environment-vscode"

- Vous devez disposer d’un compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-app-service-extension&mktingSource=vscode-tutorial-app-service-extension).
- Installez [Node.js et npm](https://nodejs.org). Exécutez la commande `node --version` pour vérifier que Node.js est installé.
- Installez [Visual Studio Code](https://code.visualstudio.com/).
- [Extension Azure App Service](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice) pour Visual Studio Code.
 <!-- - <a href="https://git-scm.com/" target="_blank">Install Git</a> -->

::: zone-end

:::zone target="docs" pivot="development-environment-cli"

- Vous devez disposer d’un compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-app-service-extension&mktingSource=vscode-tutorial-app-service-extension).
- Installez [Node.js et npm](https://nodejs.org). Exécutez la commande `node --version` pour vérifier que Node.js est installé.
- Installez <a href="/cli/azure/install-azure-cli" target="_blank">Azure CLI</a>, avec laquelle vous exécutez des commandes dans n’importe quel interpréteur de commandes pour provisionner et configurer des ressources Azure.

::: zone-end


:::zone target="docs" pivot="development-environment-azure-portal"

- Vous devez disposer d’un compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-app-service-extension&mktingSource=vscode-tutorial-app-service-extension).
- Installez [Node.js et npm](https://nodejs.org). Exécutez la commande `node --version` pour vérifier que Node.js est installé.
- Disposez d’un client FTP (par exemple [FileZilla](https://filezilla-project.org)) pour vous connecter à votre application.

::: zone-end
## <a name="create-your-nodejs-application"></a>Créer votre application Node.js

Pendant cette étape, vous allez créer une application Node.js de démarrage et vérifier qu’elle s’exécute sur votre ordinateur.

> [!TIP]
> Si vous avez déjà terminé le [tutoriel Node.js](https://code.visualstudio.com/docs/nodejs/nodejs-tutorial), vous pouvez passer directement à la section [Déployer sur Azure](#deploy-to-azure).

1. Créez une application Node.js simple à l’aide du [Générateur Express](https://expressjs.com/starter/generator.html), installé par défaut avec Node.js et NPM.

    ```bash
    npx express-generator myExpressApp --view pug
    ```

1. Accédez au répertoire de l’application et installez les packages NPM.

    ```bash
    cd myExpressApp
    npm install
    ```

1. Démarrez le serveur de développement.

    ```bash
    npm start
    ```

1. Dans un navigateur, accédez à `http://localhost:3000`. Un résultat semblable à celui-ci doit s’afficher :

    ![Exécution de l’application Express](./media/quickstart-nodejs/express.png)

:::zone target="docs" pivot="development-environment-vscode"
> [!div class="nextstepaction"]
> [J’ai rencontré un problème](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=create-app)
::: zone-end

## <a name="deploy-to-azure"></a>Déployer dans Azure

Avant de continuer, assurez-vous que tous les composants requis ont bien été installés et configurés.

> [!NOTE]
> Pour que votre application Node.js s’exécute dans Azure, elle a besoin d’écouter le port fourni par la variable d’environnement `PORT`. Dans votre application Express générée, cette variable d’environnement est déjà utilisée dans le script de démarrage *bin/www* (recherchez `process.env.PORT`).
>

:::zone target="docs" pivot="development-environment-vscode"

#### <a name="sign-in-to-azure"></a>Connexion à Azure

1. Dans le terminal, veillez à être dans le dossier *myExpressApp*, puis démarrez Visual Studio Code à l’aide de la commande suivante :

    ```bash
    code .
    ```

1. Dans Visual Studio Code, dans la [barre d’activités](https://code.visualstudio.com/docs/getstarted/userinterface), sélectionnez le logo **Azure**.

1. Dans l’explorateur **App Service**, sélectionnez **Se connecter à Azure...** , puis suivez les instructions.

    Dans Visual Studio Code, vous devez voir votre adresse e-mail Azure dans la barre d’état et votre abonnement dans l’explorateur **AZURE APP SERVICE**.

    ![se connecter à Azure](./media/quickstart-nodejs/sign-in.png)

> [!div class="nextstepaction"]
> [J’ai rencontré un problème](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=getting-started)

#### <a name="configure-the-app-service-app-and-deploy-code"></a>Configurer l’application App Service et déployer du code

1. Dans l’explorateur **App Service**, sélectionnez l’icône **Déployer sur l’application web**.

    :::image type="content" source="media/quickstart-nodejs/deploy.png" alt-text="Capture d’écran d’Azure App Service dans Visual Studio Code montrant l’icône de flèche bleue sélectionnée.":::
        
1. Choisissez le dossier *myExpressApp*.

# <a name="deploy-to-linux"></a>[Déployer sur Linux](#tab/linux)

3. Sélectionnez **Créer une application Web**. Un conteneur Linux est utilisé par défaut.
1. Saisissez un nom unique pour votre application web et appuyez sur **Entrée**. Le nom doit être unique sur l’ensemble d’Azure et comporter uniquement des caractères alphanumériques (« A-Z », « a-z » et « 0-9 ») et des traits d’union (« - »).
1. Dans Sélectionner une pile d’exécution, sélectionnez la version de Node.js souhaitée. Une version **LTS** est recommandée.
1. Dans Sélectionner un niveau de tarification, sélectionnez **Gratuit (F1)** et attendez que les ressources soient provisionnées dans Azure.
1. Dans le menu contextuel **Toujours déployer l’espace de travail « myExpressApp » sur \<app-name>** , sélectionnez **Oui**. Ainsi, tant que vous êtes dans le même espace de travail, Visual Studio Code se déploie chaque fois sur la même application App Service.

    Pendant que Visual Studio Code provisionne les ressources Azure et déploie le code, des [notifications de progression](https://code.visualstudio.com/api/references/extension-guidelines#notifications) s’affichent.

1. Une fois le déploiement terminé, sélectionnez **Parcourir le site web** dans le message de notification. Le navigateur doit afficher la page par défaut Express.

# <a name="deploy-to-windows"></a>[Déployer sur Windows](#tab/windows)

3. Choisissez **Créer une application web... Avancé**.
1. Saisissez un nom unique pour votre application web et appuyez sur **Entrée**. Le nom doit être unique sur l’ensemble d’Azure et comporter uniquement des caractères alphanumériques (« A-Z », « a-z » et « 0-9 ») et des traits d’union (« - »).
1. Sélectionnez **Créer un groupe de ressources** et entrez un nom pour le groupe de ressources, par exemple *AppServiceQS-rg*.
1. Sélectionnez la version de Node.js souhaitée. Une version **LTS** est recommandée.
1. Sélectionnez **Windows** comme système d’exploitation.
1. Sélectionnez l’emplacement à partir duquel vous voulez servir votre application. Par exemple, *Europe Ouest*.
1. Sélectionnez **Créer un plan App Service**, entrez un nom pour le plan (par exemple, *AppServiceQS-plan*), puis sélectionnez le niveau tarifaire **F1 Gratuit**.
1. Pour **Sélectionner une ressource Application Insights pour votre application**, sélectionnez **Ignorer pour l’instant** et attendez que les ressources soient provisionnées dans Azure.
1. Dans le menu contextuel **Toujours déployer l’espace de travail « myExpressApp » sur \<app-name>** , sélectionnez **Oui**. Ainsi, tant que vous êtes dans le même espace de travail, Visual Studio Code se déploie chaque fois sur la même application App Service.

    Pendant que Visual Studio Code provisionne les ressources Azure et déploie le code, des [notifications de progression](https://code.visualstudio.com/api/references/extension-guidelines#notifications) s’affichent.

    > [!NOTE]
    > Une fois le déploiement terminé, votre application Azure ne s’exécute pas encore, car la racine de votre projet n’a pas de fichier *web.config*. Suivez les étapes restantes pour le générer automatiquement. Pour plus d’informations, consultez [Vous n’êtes pas autorisé à afficher ce répertoire ou cette page](configure-language-nodejs.md#you-do-not-have-permission-to-view-this-directory-or-page).

1. Dans l’explorateur **App Service** de Visual Studio Code, développez le nœud de la nouvelle application, cliquez avec le bouton droit sur **Paramètres de l’application**, puis sélectionnez **Ajouter un nouveau paramètre** :

    ![Commande d’ajout de paramètre d’application](media/quickstart-nodejs/add-setting.png)

1. Entrez `SCM_DO_BUILD_DURING_DEPLOYMENT` pour la clé du paramètre.
1. Entrez `true` pour la valeur du paramètre.

    Ce paramètre d’application permet d’automatiser la génération au moment du déploiement, ce qui détecte automatiquement le script de démarrage et génère le fichier *web.config* en même temps.

1. Dans l’explorateur **App Service**, sélectionnez encore l’icône **Déployer sur l’application web**, puis confirmez en recliquant sur **Déployer**.
1. Attendez la fin du déploiement, puis sélectionnez **Parcourir le site web** dans le message de notification. Le navigateur doit afficher la page par défaut Express.

-----

> [!div class="nextstepaction"]
> [J’ai rencontré un problème](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=deploy-app)

::: zone-end

:::zone target="docs" pivot="development-environment-cli"

Dans le terminal, vérifiez que vous êtes dans le répertoire *myExpressApp* et déployez le code dans votre dossier local (*myExpressApp*) à l’aide de la commande `az webapp up` :

# <a name="deploy-to-linux"></a>[Déployer sur Linux](#tab/linux)

```azurecli
az webapp up --sku F1 --name <app-name>
```

# <a name="deploy-to-windows"></a>[Déployer sur Windows](#tab/windows)

```azurecli
az webapp up --sku F1 --name <app-name> --os-type Windows
```

-----

- Si la commande `az` n’est pas reconnue, vérifiez qu’Azure CLI est installé, comme décrit dans [Configurer votre environnement initial](#set-up-your-initial-environment).
- Remplacez `<app_name>` par un nom unique sur l’ensemble d’Azure (*les caractères valides sont `a-z`, `0-9` et `-`* ). Un bon modèle consiste à utiliser une combinaison du nom de votre société et d’un identificateur d’application.
- L’argument `--sku F1` crée l’application web sur le niveau tarifaire Gratuit, qui n’entraîne aucun coût.
- Vous pouvez éventuellement inclure l’argument `--location <location-name>` où `<location_name>` est une région Azure disponible. Vous pouvez récupérer une liste de régions autorisées pour votre compte Azure en exécutant la commande [`az account list-locations`](/cli/azure/appservice#az_appservice_list_locations).
- La commande crée une application Linux pour Node.js par défaut. Pour créer plutôt une application Windows, utilisez l’argument `--os-type`. 
- Si vous voyez l’erreur « Impossible de détecter automatiquement la pile d’exécution de votre application », vérifiez que vous exécutez la commande dans le répertoire *myExpressApp* (consultez [Résolution des problèmes de détection automatique avec az webapp up](https://github.com/Azure/app-service-linux-docs/blob/master/AzWebAppUP/runtime_detection.md)).

La commande peut prendre quelques minutes. Lors de son exécution, elle fournit des messages sur la création du groupe de ressources, du plan App Service et de la ressource d’application, sur la configuration de la journalisation et sur le déploiement ZIP. Elle affiche ensuite le message « Vous pouvez lancer l’application sur http://&lt;app-name&gt;.azurewebsites.net », qui est l’URL de l’application sur Azure.

<pre>
The webapp '&lt;app-name>' doesn't exist
Creating Resource group '&lt;group-name>' ...
Resource group creation complete
Creating AppServicePlan '&lt;app-service-plan-name>' ...
Creating webapp '&lt;app-name>' ...
Configuring default logging for the app, if not already enabled
Creating zip with contents of dir /home/cephas/myExpressApp ...
Getting scm site credentials for zip deployment
Starting zip deployment. This operation can take a while to complete ...
Deployment endpoint responded with status code 202
You can launch the app at http://&lt;app-name>.azurewebsites.net
{
  "URL": "http://&lt;app-name>.azurewebsites.net",
  "appserviceplan": "&lt;app-service-plan-name>",
  "location": "centralus",
  "name": "&lt;app-name>",
  "os": "&lt;os-type>",
  "resourcegroup": "&lt;group-name>",
  "runtime_version": "node|10.14",
  "runtime_version_detected": "0.0",
  "sku": "FREE",
  "src_path": "//home//cephas//myExpressApp"
}
</pre>

[!include [az webapp up command note](../../includes/app-service-web-az-webapp-up-note.md)]

::: zone-end

:::zone target="docs" pivot="development-environment-azure-portal"
### <a name="sign-in-to-azure-portal"></a>Se connecter au portail Azure

Connectez-vous au portail Azure sur https://portal.azure.com.

### <a name="create-azure-resources"></a>Créer des ressources Azure

1. Saisissez **app services** dans la zone de recherche. Sous **Services**, sélectionnez **App Services**.

     :::image type="content" source="./media/quickstart-nodejs/portal-search.png?text=Azure portal search details" alt-text="Capture d’écran de la recherche dans le portail":::

1. Dans la page **App Services**, sélectionnez **Créer**.
1. Sous l’onglet **De base**, sous **Détails du projet**, vérifiez que l’abonnement approprié est sélectionné, puis choisissez **Créer** pour créer un groupe de ressources. Tapez *myResourceGroup* comme nom.

    :::image type="content" source="./media/quickstart-nodejs/project-details.png" alt-text="Capture d’écran de la section Détails du projet montrant où vous sélectionnez l’abonnement Azure et le groupe de ressources pour l’application web.":::

1. Sous **Détails de l’instance**, saisissez un nom global unique pour votre application web et sélectionnez **Code**. Choisissez la **pile d’exécution** *Nœud 14 LTS*, un **système d’exploitation** et une **région** à partir de laquelle vous souhaitez servir votre application.

    :::image type="content" source="./media/quickstart-nodejs/instance-details.png" alt-text="Capture d’écran de la section Détails de l’instance où vous spécifiez un nom pour la machine virtuelle et où vous sélectionnez sa région, son image et sa taille.":::

1. Sous **Plan App Service**, choisissez de **créer un nouveau** plan App Service. Saisissez *myAppServicePlan* comme nom. Pour passer au niveau Gratuit, cliquez sur **Modifier la taille**, sélectionnez l’onglet **Dev/Test**, **F1**, puis le bouton **Appliquer** au bas de la page.

    :::image type="content" source="./media/quickstart-nodejs/app-service-plan-details.png" alt-text="Capture d’écran de la section Compte d’administrateur où vous fournissez le nom d’utilisateur et le mot de passe de l’administrateur":::

1. Sélectionnez le bouton **Vérifier + créer** au bas de la page.

    :::image type="content" source="./media/quickstart-nodejs/review-create.png" alt-text="Capture d’écran montrant le bouton Vérifier + créer en bas de la page":::

1. Une fois la validation exécutez, sélectionnez le bouton **Créer** en bas de la page.

1. Une fois le déploiement effectué, sélectionnez **Accéder à la ressource**.

    :::image type="content" source="./media/quickstart-nodejs/next-steps.png" alt-text="Capture d’écran montrant l’étape suivante d’accès à la ressource":::

### <a name="get-ftp-credentials"></a>Obtenir les informations d’identification FTP

Azure App Service prend en charge [**deux types d’informations d’identification**](deploy-configure-credentials.md) pour le déploiement FTP/S. Ces informations d’identification ne sont pas les mêmes que les informations d’identification de votre abonnement Azure. Dans cette section, vous obtenez les *informations d’identification de l’application* à utiliser avec FileZilla.

1. Sur la page de l’application App Service, cliquez sur **Centre de déploiement** dans le menu de gauche et sélectionnez l’onglet **Informations d’identification FTPS**.

    :::image type="content" source="./media/quickstart-nodejs/ftps-deployment-credentials.png" alt-text="Informations d’identification de déploiement FTPS":::

1. Ouvrez **FileZilla** et créez un nouveau site.

1. À partir de l’onglet **Informations d’identification FTPS**, copiez le **point de terminaison FTPS**, le **nom d’utilisateur** et le **mot de passe** dans FileZilla.

    :::image type="content" source="./media/quickstart-nodejs/filezilla-ftps-connection.png" alt-text="Détails de la connexion FTPS":::

1. Cliquez sur **Connexion** dans FileZilla.
 
### <a name="deploy-files-with-ftp"></a>Déployer des fichiers avec FTP

1. Copiez tous les fichiers et répertoires dans le [répertoire **/site/wwwroot**](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure) dans Azure.
    
    :::image type="content" source="./media/quickstart-nodejs/filezilla-deploy-files.png" alt-text="Fichiers de déploiement FileZilla":::

1. Accédez à l’URL de votre application pour vérifier que l’application s’exécute correctement.

::: zone-end
## <a name="redeploy-updates"></a>Redéployer les mises à jour

Vous pouvez déployer des modifications apportées à cette application en effectuant des modifications dans Visual Studio Code et en enregistrant vos fichiers, avant de les redéployer sur votre application Azure. Par exemple :

1. À partir de l’exemple de projet, ouvrez *views/index.pug* et remplacez

    ```PUG
    p Welcome to #{title}
    ```

    to
    
    ```PUG
    p Welcome to Azure!
    ```

:::zone target="docs" pivot="development-environment-vscode"

2. Dans l’explorateur **App Service**, sélectionnez encore l’icône **Déployer sur l’application web**, puis confirmez en recliquant sur **Déployer**.

1. Attendez la fin du déploiement, puis sélectionnez **Parcourir le site web** dans le message de notification. Vous devez voir que le message `Welcome to Express` a été remplacé par `Welcome to Azure!`.

::: zone-end

:::zone target="docs" pivot="development-environment-cli"

2. Enregistrez vos modifications, puis redéployez l’application en réutilisant la commande `az webapp up` sans aucun argument :

    ```azurecli
    az webapp up
    ```
    
    Cette commande utilise des valeurs mises en cache localement dans le fichier *.azure/config*, notamment le nom de l’application, le groupe de ressources et le plan App Service.
    
1. Une fois le déploiement terminé, actualisez la page web `http://<app-name>.azurewebsites.net`. Vous devez voir que le message `Welcome to Express` a été remplacé par `Welcome to Azure!`.

::: zone-end

:::zone target="docs" pivot="development-environment-azure-portal"

2. Enregistrez vos modifications, puis redéployez l’application à l’aide de votre client FTP.
    
1. Une fois le déploiement terminé, actualisez la page web `http://<app-name>.azurewebsites.net`. Vous devez voir que le message `Welcome to Express` a été remplacé par `Welcome to Azure!`.

::: zone-end

## <a name="stream-logs"></a>Diffuser en continu des journaux

:::zone target="docs" pivot="development-environment-vscode"

Vous pouvez diffuser en streaming la sortie de journal (appels à `console.log()`) à partir de l’application Azure directement dans la fenêtre de sortie Visual Studio Code.

1. Dans l’explorateur **App Service**, cliquez avec le bouton droit sur le nœud de l’application, puis choisissez **Commencer le streaming des journaux**.

    ![Commencer le streaming des journaux](./media/quickstart-nodejs/view-logs.png)

1. Si vous êtes invité à redémarrer l’application, cliquez sur **Oui**. Une fois l’application redémarrée, la fenêtre de sortie Visual Studio Code s’ouvre avec une connexion au flux de journaux.

1. Après quelques secondes, la fenêtre de sortie affiche un message indiquant que vous êtes connecté au service de streaming de journaux. Vous pouvez générer plus d’activités de sortie en actualisant la page dans le navigateur.

    <pre>
    Connecting to log stream...
    2020-03-04T19:29:44  Welcome, you are now connected to log-streaming service. The default timeout is 2 hours.
    Change the timeout with the App Setting SCM_LOGSTREAM_TIMEOUT (in seconds).
    </pre>

> [!div class="nextstepaction"]
> [J’ai rencontré un problème](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=tailing-logs)

::: zone-end

:::zone target="docs" pivot="development-environment-cli"

Vous pouvez accéder aux journaux de la console générés à l’intérieur de l’application et au conteneur dans lequel elle s’exécute. Les journaux incluent toutes les sorties générées par les appels à `console.log()`.

Pour activer le streaming des journaux, exécutez la commande [az webapp log tail](/cli/azure/webapp/log#az_webapp_log_tail) :

```azurecli
az webapp log tail
```

La commande utilise le nom du groupe de ressources mis en cache dans le fichier *.azure/config*.

Vous pouvez aussi inclure le paramètre `--logs` dans la commande `az webapp up` pour ouvrir automatiquement le stream des journaux lors du déploiement.

Actualisez l’application dans le navigateur pour générer des journaux de console, qui incluent des messages décrivant les requêtes HTTP envoyées à l’application. Si aucune sortie ne s’affiche immédiatement, réessayez dans 30 secondes.

Pour arrêter le streaming des journaux à tout moment, appuyez sur **Ctrl**+**C** dans le terminal.

::: zone-end

:::zone target="docs" pivot="development-environment-azure-portal"

Vous pouvez accéder aux journaux de la console générés à l’intérieur de l’application et au conteneur dans lequel elle s’exécute. Vous pouvez transmettre en continu la sortie de journal (appels à `console.log()`) à partir de l’application Node.js directement dans le portail Azure.

1. Dans la même page **App Service** de votre application, utilisez le menu de gauche pour aller à la section *Supervision*, puis cliquez sur **Flux de journal**.

    :::image type="content" source="./media/quickstart-nodejs/log-stream.png" alt-text="Capture d’écran de Flux de journal dans Azure App Service.":::

1. Après quelques secondes, la fenêtre de sortie affiche un message indiquant que vous êtes connecté au service de streaming de journaux. Vous pouvez générer plus d’activités de sortie en actualisant la page dans le navigateur.

    <pre>
    Connecting...
    2021-10-26T21:04:14  Welcome, you are now connected to log-streaming service.
    Starting Log Tail -n 10 of existing logs ----
    /appsvctmp/volatile/logs/runtime/81b1b83b27ea1c3d598a1cdec28c71c4074ce66c735d0be57f15a8d07cb3178e.log
    2021-10-26T21:04:08.614384810Z: [INFO]
    2021-10-26T21:04:08.614393710Z: [INFO]  # Enter the source directory to make sure the script runs where the user expects
    2021-10-26T21:04:08.614399010Z: [INFO]  cd "/home/site/wwwroot"
    2021-10-26T21:04:08.614403210Z: [INFO]
    2021-10-26T21:04:08.614407110Z: [INFO]  export NODE_PATH=/usr/local/lib/node_modules:$NODE_PATH
    2021-10-26T21:04:08.614411210Z: [INFO]  if [ -z "$PORT" ]; then
    2021-10-26T21:04:08.614415310Z: [INFO]          export PORT=8080
    2021-10-26T21:04:08.614419610Z: [INFO]  fi
    2021-10-26T21:04:08.614423411Z: [INFO]
    2021-10-26T21:04:08.614427211Z: [INFO]  node /opt/startup/default-static-site.js
    Ending Log Tail of existing logs ---
    </pre>

::: zone-end

## <a name="clean-up-resources"></a>Nettoyer les ressources

:::zone target="docs" pivot="development-environment-vscode"

Au cours des étapes précédentes, vous avez créé des ressources Azure au sein d’un groupe de ressources. Les étapes de création indiquées dans ce guide de démarrage rapide placent toutes les ressources dans ce groupe de ressources. Pour effectuer un nettoyage, il vous suffit simplement de supprimer le groupe de ressources.


1. Dans l’extension Azure de Visual Studio, développez l’explorateur **Groupes de ressources**.

1. Développez l’abonnement, cliquez avec le bouton droit sur le groupe de ressources que vous avez créé précédemment, puis sélectionnez **Supprimer**.

    :::image type="content" source="media/quickstart-nodejs/clean-up.png" alt-text="Capture d’écran de la navigation dans Visual Studio Code pour supprimer une ressource qui contient des ressources App Service.":::

1. À l’invite, confirmez la suppression en entrant le nom du groupe de ressources que vous supprimez. Une fois que vous avez confirmé, le groupe de ressources est supprimé et une [notification](https://code.visualstudio.com/api/references/extension-guidelines#notifications) s’affiche une fois l’opération terminée.

> [!div class="nextstepaction"]
> [J’ai rencontré un problème](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=clean-up)

::: zone-end

:::zone target="docs" pivot="development-environment-cli"

Au cours des étapes précédentes, vous avez créé des ressources Azure au sein d’un groupe de ressources. Le groupe de ressources a un nom tel que « appsvc_rg_Linux_CentralUS » en fonction de votre emplacement.

Si vous ne pensez pas avoir besoin de ces ressources à l’avenir, supprimez le groupe de ressources en exécutant la commande suivante :

```azurecli
az group delete --no-wait
```

La commande utilise le nom du groupe de ressources mis en cache dans le fichier *.azure/config*.

Avec l’argument `--no-wait`, la commande peut retourner une sortie avant la fin de l’opération.

::: zone-end

:::zone target="docs" pivot="development-environment-azure-portal"

Quand vous n’en avez plus besoin, vous pouvez supprimer le groupe de ressources, l’instance App Service et toutes les ressources associées.

1. Dans la page *Vue d’ensemble* de votre instance App Service, cliquez sur le *groupe de ressources* que vous avez créé à l’étape [Créer des ressources Azure](#create-azure-resources).

    :::image type="content" source="./media/quickstart-nodejs/resource-group.png" alt-text="Groupe de ressources dans la page Vue d’ensemble d’App Service":::

1. Dans la page *Groupe de ressources*, sélectionnez **Supprimer un groupe de ressources**. Confirmez le nom du groupe de ressources pour terminer la suppression des ressources.

    Supprimer le groupe de ressources

::: zone-end

## <a name="next-steps"></a>Étapes suivantes

Félicitations, vous avez terminé ce démarrage rapide !

> [!div class="nextstepaction"]
> [Tutoriel : Application Node.js avec MongoDB](tutorial-nodejs-mongodb-app.md)

> [!div class="nextstepaction"]
> [Configurer une application Node.js](configure-language-nodejs.md)

Consultez les autres extensions Azure.

* [Cosmos DB](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)
* [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)
* [Outils Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
* [Outils Azure CLI](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli)
* [Outils Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)

Vous pouvez également tous les obtenir en installant le pack d’extension [Pack de nœuds pour Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack).
