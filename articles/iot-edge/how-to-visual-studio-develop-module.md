---
title: Développer et déboguer des modules dans Visual Studio – Azure IoT Edge
description: Utilisez Visual Studio avec les outils Azure IoT pour développer un module IoT Edge C ou C# et l’envoyer de votre IoT Hub à un appareil IoT, comme configuré par un manifeste de déploiement.
services: iot-edge
author: kgremban
manager: lizross
ms.author: kgremban
ms.date: 08/24/2021
ms.topic: conceptual
ms.service: iot-edge
ms.openlocfilehash: 5f546acb48a84ddddeb822601d9284818d2211fb
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131423068"
---
# <a name="use-visual-studio-2019-to-develop-and-debug-modules-for-azure-iot-edge"></a>Utiliser Visual Studio 2019 pour développer et déboguer des modules pour Azure IoT Edge

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Cet article explique comment utiliser Visual Studio 2019 pour développer et déboguer des modules Azure IoT Edge.

L’extension Azure IoT Edge Tools pour Visual Studio vous offre les possibilités suivantes :

* Créer, modifier, générer, exécuter et déboguer des solutions et des modules Azure IoT Edge sur votre ordinateur de développement local.
* Déployer votre solution IoT Edge sur un appareil IoT Edge via Azure IoT Hub.
* Codez vos modules Azure IoT en C ou C# tout en bénéficiant de tous les avantages du développement Visual Studio.
* Gérer des appareils et modules IoT Edge via une interface utilisateur.

Cet article vous explique comment utiliser Azure IoT Edge Tools pour Visual Studio 2019 pour développer vos modules IoT Edge. Vous y découvrez également comment déployer votre projet sur un appareil IoT Edge. Actuellement, Visual Studio 2019 prend en charge les modules écrits en C et C#. Les architectures d’appareils prises en charge sont Windows x64 et Linux x64 ou ARM32. Pour plus d’informations sur les systèmes d’exploitation, les langages et les architectures pris en charge, consultez [Prise en charge des langages et architectures](module-development.md#language-and-architecture-support).
  
## <a name="prerequisites"></a>Prérequis

Cet article part du principe que vous utilisez un ordinateur exécutant Windows pour le développement. Sur les ordinateurs Windows, vous pouvez développer des modules Windows ou Linux.

* Pour développer des modules avec des **conteneurs Windows**, utilisez un ordinateur Windows exécutant la version 1809/build 17763 ou une version plus récente.
* Pour développer des modules avec des **conteneurs Linux**, utilisez un ordinateur Windows présentant la [configuration requise pour Docker Desktop](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install).

Installez Visual Studio Code sur votre machine de développement. Veillez à inclure les charges de travail **Développement Azure** et de **Développement Desktop en C++** lorsque vous installez Visual Studio 2019. Vous pouvez [modifier Visual Studio 2019](/visualstudio/install/modify-visual-studio?view=vs-2019&preserve-view=true) pour ajouter les charges de travail requises.

Une fois votre Visual Studio 2019 prêt, vous avez également besoin des outils et composants suivants :

* Téléchargez et installez les [outils Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) à partir de la place de marché Visual Studio pour créer un projet IoT Edge dans Visual Studio 2019.

  > [!TIP]
  > Si vous utilisez Visual Studio 2017, téléchargez et installez [Azure IoT Edge Tools pour VS 2017](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) à partir de la Place de marché Visual Studio

* Téléchargez et installez [Docker Community Edition](https://docs.docker.com/install/) sur votre machine de développement pour générer et exécuter vos images de module. Vous devez configurer Docker CE pour qu’il s’exécute en mode conteneur Linux ou Windows, selon le type de modules que vous développez.

* Configurez votre environnement de développement local pour le débogage, l’exécution et le test de votre solution IoT Edge en installant l’[outil de développement Azure IoT EdgeHub](https://pypi.org/project/iotedgehubdev/). Installez [Python (3.5/3.6/3.7/3.8) et Pip](https://www.python.org/), puis installez le package **iotedgehubdev** en exécutant la commande suivante sur votre terminal. Vérifiez que votre version de l’outil de développement Azure IoT pour EdgeHub est supérieure à 0.3.0.

   ```cmd
   pip install --upgrade iotedgehubdev
   ```

* Installez le gestionnaire de bibliothèque Vcpkg, puis installez le **package azure-iot-sdk-c** pour Windows.

  ```cmd
  git clone https://github.com/Microsoft/vcpkg
  cd vcpkg
  bootstrap-vcpkg.bat
  ```

  ```cmd
  vcpkg.exe install azure-iot-sdk-c:x64-windows
  vcpkg.exe --triplet x64-windows integrate install
  ```

* Créer une instance d’[Azure Container Registry](../container-registry/index.yml) ou de [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags) pour stocker vos images de module.

  > [!TIP]
  > Vous pouvez utiliser un registre Docker local pour le prototype et à des fins de test, au lieu d’un registre cloud.

* Pour tester votre module sur un appareil, vous devez disposer d’un hub IoT actif avec au moins un appareil IoT Edge. Pour créer rapidement un appareil IoT Edge à des fins de test, suivez les étapes du démarrage rapide pour [Linux](quickstart-linux.md) ou [Windows](quickstart.md). Si vous exécutez le démon IoT Edge sur votre machine de développement, il peut être nécessaire d’arrêter EdgeHub et EdgeAgent avant de commencer à développer dans Visual Studio.

### <a name="check-your-tools-version"></a>Vérifier la version des outils

1. Dans le menu **Extensions**, sélectionnez **Gérer les extensions**. Développez **Installé > Outils** pour trouver **Azure IoT Edge Tools pour Visual Studio** et **Cloud Explorer pour Visual Studio**.

1. Notez la version installée. Vous pouvez comparer cette version avec la version la plus récente sur Visual Studio Marketplace ([Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS2019), [Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools))

1. Si votre version est antérieure à ce qui est disponible sur Visual Studio Marketplace, mettez à jour vos outils dans Visual Studio, comme indiqué dans la section suivante.

### <a name="update-your-tools"></a>Mettre à jour les outils

1. Dans la fenêtre **Gérer les extensions**, développez **Mises à jour > Visual Studio Marketplace**, sélectionnez **Azure IoT Edge Tools** ou **Cloud Explorer pour Visual Studio**, puis choisissez **Mettre à jour**.

1. Une fois la mise à jour des outils téléchargée, fermez Visual Studio pour déclencher la mise à jour des outils à l’aide du programme d’installation VSIX.

1. Dans le programme d’installation, sélectionnez **OK** pour démarrer, puis **Modifier** pour mettre à jour les outils.

1. Une fois la mise à jour terminée, choisissez **Fermer**, puis redémarrez Visual Studio.

## <a name="create-an-azure-iot-edge-project"></a>Créer un projet Azure IoT Edge

Le modèle de projet IoT Edge dans Visual Studio crée une solution qui peut être déployée sur des appareils IoT Edge. Vous commencez par créer une solution Azure IoT Edge, puis vous y générez le premier module. Chaque solution IoT Edge peut contenir plusieurs modules.

> [!TIP]
> La structure de projet IoT Edge créée par Visual Studio n’est pas la même que dans Visual Studio Code.

1. Dans Visual Studio, créez un projet.

1. Sur la page **Créer un projet**, recherchez **Azure IoT Edge**. Sélectionnez le projet correspondant à la plateforme et l’architecture de votre appareil IoT Edge, puis cliquez sur **suivant**.

   :::image type="content" source="./media/how-to-visual-studio-develop-module/create-new-project.png" alt-text="Créer un projet":::

1. Dans la page **Configurer votre nouveau projet**, entrez un nom pour votre projet, spécifiez l’emplacement, puis sélectionnez **Créer**.

1. Dans la fenêtre **Ajouter un module**, sélectionnez le type de module que vous souhaitez développer. Vous pouvez également sélectionner **Module existant** pour ajouter un module de IOT Edge existant à votre déploiement. Spécifiez le nom de votre module et son référentiel d’images.

   Visual Studio remplit automatiquement l’URL du référentiel avec **localhost:5000/<nom du module\>** . Si vous utilisez un registre Docker local à des fins de test, **localhost** convient. Si vous utilisez Azure Container Registry, remplacez **localhost:5000** par le serveur de connexion pour les paramètres de votre registre. Le serveur de connexion ressemble à **_\<registry name\>_ . azurecr.io**. Le résultat final doit ressembler à **\<*registry name*\>. azurecr.io/ _\<module name\>_**.

   Sélectionnez **Ajouter** pour ajouter votre module au projet.

   ![Ajouter un application et un module](./media/how-to-visual-studio-develop-csharp-module/add-module.png)

Vous disposez maintenant d’un projet IoT Edge et d’un module IoT Edge dans votre solution Visual Studio.

Le dossier du module contient un fichier pour le code de votre module, nommé `program.cs` ou `main.c` selon le langage que vous avez choisi. Ce dossier contient également un fichier nommé `module.json` décrivant les métadonnées de votre module. Différents fichiers Docker fournissent les informations nécessaires pour générer votre module en tant que conteneur Windows ou Linux.

Le dossier du projet contient la liste de tous les modules inclus dans ce projet. À ce stade, il ne devrait afficher qu’un seul module, mais vous pouvez en ajouter d’autres. Pour plus d’informations sur l’ajout de modules à un projet, consultez la section [Générer et déboguer plusieurs modules](#build-and-debug-multiple-modules) plus loin dans cet article.

Le dossier du projet contient également un fichier nommé `deployment.template.json`. Ce fichier est un modèle de manifeste de déploiement IoT Edge définissant tous les modules qui s’exécuteront sur un appareil, ainsi que la façon dont ils communiqueront entre eux. Pour plus d’informations sur les manifestes de déploiement, consultez [Découvrir comment déployer des modules et établir des routes](module-composition.md). Si vous ouvrez ce modèle de déploiement, vous constatez que les deux modules de runtime, **edgeAgent** et **edgeHub**, sont inclus, ainsi que le module personnalisé que vous avez créé dans ce projet Visual Studio. Un quatrième module nommé **SimulatedTemperatureSensor** est également inclus. Ce module par défaut génère des données simulées que vous pouvez utiliser pour tester vos modules, ou les supprimer s’ils ne sont pas nécessaires. Pour voir comment fonctionne le capteur de température simulé, consultez le [code source SimulatedTemperatureSensor.csproj](https://github.com/Azure/iotedge/tree/master/edge-modules/SimulatedTemperatureSensor).

### <a name="set-iot-edge-runtime-version"></a>Définir la version du runtime IoT Edge

L’extension IoT Edge est par défaut la dernière version stable du runtime IoT Edge lors de la création de vos ressources de déploiement. Actuellement, la dernière version stable est la version 1.2. Si vous développez des modules pour des appareils qui exécutent la version de support à long terme 1.1 ou la version 1.0 antérieure, mettez à jour la version du runtime IoT Edge dans Visual Studio pour qu’elle corresponde.

1. Dans l’Explorateur de solutions, cliquez avec le bouton de droite sur le nom de votre projet, puis sélectionnez **Définir la version du runtime IoT Edge**.

   :::image type="content" source="./media/how-to-visual-studio-develop-module/set-iot-edge-runtime-version.png" alt-text="Cliquez avec le bouton de droite sur le nom de votre projet et sélectionnez Définir la version IoT Edge du runtime.":::

1. Utilisez le menu déroulant pour choisir la version du runtime que vos appareils IoT Edge exécutent, puis sélectionnez **OK** pour enregistrer vos modifications.

1. Générez à nouveau le manifeste de déploiement avec la nouvelle version du runtime. Cliquez avec le bouton de droite sur le nom de votre projet, puis sélectionnez **Générer un déploiement pour IoT Edge**.

## <a name="develop-your-module"></a>Développer votre module

Lorsque vous ajoutez un nouveau module, celui-ci est fourni avec un code par défaut prêt pour génération et déploiement sur un appareil afin que vous puissiez commencer à tester sans écrire de code. Le code du module se trouve dans le dossier de celui-ci, dans un fichier nommé `Program.cs` (pour C#) ou `main.c` (pour C).

La solution par défaut est générée de façon à ce que les données simulées du module **SimulatedTemperatureSensor** soient acheminées vers votre module qui prend l’entrée, puis l’envoie à IoT Hub.

Lorsque vous êtes prêt à personnaliser le modèle de module avec votre propre code, utilisez les kits [SDK Azure IoT Hub](../iot-hub/iot-hub-devguide-sdks.md) pour générer des modules répondant aux besoins des solutions IoT, tels que la sécurité, la gestion des appareils et la fiabilité.

## <a name="set-up-the-iotedgehubdev-testing-tool"></a>Configurer l’outil de test iotedgehubdev

L’outil de développement IoT EdgeHub fournit une expérience de développement et de débogage locale. L’outil aide à démarrer des modules IoT Edge sans le runtime IoT Edge de façon à pouvoir créer, développer, tester, exécuter et déboguer des modules et solutions IoT Edge localement. Vous n’êtes pas obligé d’envoyer des images à un registre de conteneurs et de les déployer sur un appareil à des fins de test.

Pour plus d’informations, consultez [Azure IoT EdgeHub Dev Tool](https://pypi.org/project/iotedgehubdev/).

Pour initialiser l’outil, fournissez une chaîne de connexion d’appareil IoT Edge à partir d’IoT Hub.

1. Récupérez la chaîne de connexion d’un appareil IoT Edge à partir du portail Azure, d’Azure CLI ou de Visual Studio Cloud Explorer. 

1. Dans le menu **Outils**, sélectionnez **Azure IoT Edge Tools** > **Configurer un simulateur IoT Edge**.

1. Collez la chaîne de connexion, puis cliquez sur **OK**.

> [!NOTE]
> Vous ne devez suivre ces étapes qu’une seule fois sur votre ordinateur de développement, car les résultats sont automatiquement appliqués à toutes les solutions Azure IoT Edge suivantes. Vous pouvez suivre cette procédure à nouveau si vous devez changer de chaîne de connexion.

## <a name="build-and-debug-a-single-module"></a>Générer et déboguer un seul module

En général, vous voulez tester et déboguer chaque module avant de l’exécuter au sein d’une solution entière avec plusieurs modules.

>[!TIP]
>Vérifiez que vous avez basculé vers le bon mode de conteneur Docker : soit Linux, soit Windows, selon le type de module IoT Edge que vous développez. Dans le menu Bureau de Docker, vous pouvez basculer entre les deux types de modes. Sélectionnez **Basculer vers les conteneurs Windows** pour utiliser des conteneurs Windows ou **Basculer vers les conteneurs Linux** pour utiliser des conteneurs Linux. 

1. Dans **Explorateur de solutions**, cliquez avec le bouton droit sur le dossier du module, puis sélectionnez **Définir comme projet de démarrage**.

   ![Définir le projet de démarrage](./media/how-to-visual-studio-develop-csharp-module/module-start-up-project.png)

1. Pour exécuter le module, appuyez sur **F5** ou cliquez sur le bouton d’exécution dans la barre d’outils. La première fois que vous procédez de la sorte, l’opération peut prendre de 10 à 20 secondes.

   ![Exécuter le module](./media/how-to-visual-studio-develop-csharp-module/run-module.png)

1. Si le module a été initialisé avec succès, vous devriez voir démarrer une application console .NET Core.

1. Définissez un point d’arrêt pour inspecter le module.

   * Si vous développez en C#, définissez un point d’arrêt dans la fonction `PipeMessage()` dans **Program.cs**.
   * Si vous utilisez C, définissez un point d’arrêt dans la fonction `InputQueue1Callback()` dans **main.c**.

1. Tester le module en envoyant un message à l’aide de la commande suivante dans un interpréteur de commandes **Git Bash** ou **WSL Bash**. (Vous ne pouvez pas exécuter la commande `curl` à partir de PowerShell ou d’une invite de commandes.)

    ```bash
    curl --header "Content-Type: application/json" --request POST --data '{"inputName": "input1","data":"hello world"}' http://localhost:53000/api/v1/messages
    ```

   ![Déboguer un seul module](./media/how-to-visual-studio-develop-csharp-module/debug-single-module.png)

   Le point d’arrêt doit être déclenché. Vous pouvez regarder les variables dans la fenêtre **Variables locales** de Visual Studio.

   > [!TIP]
   > Vous pouvez aussi utiliser [PostMan](https://www.getpostman.com/) ou d’autres outils d’API pour envoyer des messages à la place de `curl`.

1. Appuyez sur **Ctrl+F5** ou cliquez sur le bouton Arrêter pour arrêter le débogage.

## <a name="build-and-debug-multiple-modules"></a>Générer et déboguer plusieurs modules

Une fois que nous avons fini de développer un module, nous pouvons exécuter et déboguer une solution entière avec plusieurs modules.

1. Dans **Explorateur de solutions**, ajoutez un deuxième module à la solution en cliquant avec le bouton droit sur le dossier du projet. Dans le menu, sélectionnez **Ajouter** > **Nouveau module IoT Edge**.

   ![Ajouter un nouveau module à un projet de IoT Edge existant](./media/how-to-visual-studio-develop-csharp-module/add-new-module.png)

1. Ouvrez le fichier `deployment.template.json`. Vous verrez que le nouveau module a été ajouté dans la section **modules**. Un nouvel itinéraire a également été ajouté à la section **routes** pour envoyer des messages du nouveau module à IoT Hub. Si vous souhaitez envoyer des données du capteur de température simulé au nouveau module, ajoutez un autre itinéraire comme dans l’exemple suivant : 

    ```json
   "sensorTo<NewModuleName>": "FROM /messages/modules/SimulatedTemperatureSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/<NewModuleName>/inputs/input1\")"
    ```

1. Cliquez avec le bouton droit sur le dossier du projet, puis, dans le menu contextuel, sélectionnez **Définir comme projet de démarrage**.

1. Créez vos points d’arrêt, puis appuyez sur **F5** pour exécuter et déboguer plusieurs modules simultanément. Vous devriez voir plusieurs fenêtres d’applications console .NET Core, chacune d’elles représentant un module différent.

   ![Déboguer plusieurs modules](./media/how-to-visual-studio-develop-csharp-module/debug-multiple-modules.png)

1. Appuyez sur **Ctrl+F5** ou sélectionnez le bouton Arrêter pour arrêter le débogage.

## <a name="build-and-push-images"></a>Générer et envoyer (push) des images

1. Assurez-vous que le projet de IoT Edge est le projet de démarrage, pas l’un des modules individuels. Sélectionnez **Déboguer** ou **Publier** comme configuration pour générer vos images de module.

    > [!NOTE]
    > Lorsque vous choisissez **Déboguer**, Visual Studio utilise `Dockerfile.(amd64|windows-amd64).debug` pour générer des images Docker. Ceci inclut le débogueur en ligne de commande .NET Core (VSDBG) dans votre image conteneur lors de sa génération. Pour obtenir des modules IoT Edge prêts pour la production, nous vous recommandons d’utiliser la configuration **Publier** qui utilise `Dockerfile.(amd64|windows-amd64)` sans VSDBG.

1. Si vous utilisez un registre privé tel qu’Azure Container Registry (ACR), servez-vous de la commande Docker suivante pour vous y connecter.  Vous pouvez récupérer le nom d’utilisateur et le mot de passe à partir de la page **Clés d’accès** de votre registre dans le Portail Azure. Si vous utilisez un registre local, vous pouvez [exécuter un registre local](https://docs.docker.com/registry/deploying/#run-a-local-registry).

    ```cmd
    docker login -u <ACR username> -p <ACR password> <ACR login server>
    ```

1. Si vous utilisez un registre privé tel qu’Azure Container Registry, vous devez ajouter vos informations de connexion au registre aux paramètres de runtime figurant dans le fichier `deployment.template.json`. Remplacez les espaces réservés par le nom d’utilisateur, le mot de passe et le nom de registre de votre administrateur ACR réel.

    ```json
          "settings": {
            "minDockerVersion": "v1.25",
            "loggingOptions": "",
            "registryCredentials": {
              "registry1": {
                "username": "<username>",
                "password": "<password>",
                "address": "<registry name>.azurecr.io"
              }
            }
          }
    ```

   >[!NOTE]
   >Cet article utilise les informations d’identification de l’administrateur pour Azure Container Registry, qui conviennent pour des scénarios de développement et de test. Lorsque vous êtes prêt pour les scénarios de production, nous vous recommandons d’utiliser l’option d’authentification de moindre privilège, comme les principaux de service. Pour plus d’informations, consultez [Gérer l’accès au registre de conteneurs](production-checklist.md#manage-access-to-your-container-registry).

1. Dans **Explorateur de solutions**, cliquez avec le bouton droit sur le projet de dossier, puis sélectionnez **Générer et envoyer (push) les modules IoT Edge** afin de générer et envoyer l’image Docker pour chaque module.

## <a name="deploy-the-solution"></a>Déployer la solution

Dans l’article de démarrage rapide que vous avez utilisé pour configurer votre appareil IoT Edge, vous avez déployé un module à l’aide du Portail Azure. Vous pouvez également déployer des modules avec Cloud Explorer pour Visual Studio. Vous avez déjà un manifeste de déploiement préparé pour votre scénario, le fichier `deployment.json`. Il vous suffit de sélectionner un appareil pour recevoir le déploiement.

1. Ouvrez **Cloud Explorer** en cliquant sur **Afficher** > **Cloud Explorer**. Assurez-vous que vous êtes connecté à Visual Studio 2019.

1. Dans **Cloud Explorer**, développez votre abonnement, recherchez votre hub Azure IoT et l’appareil Azure IoT Edge que vous voulez déployer.

1. Cliquez avec le bouton droit sur l’appareil IoT Edge afin de créer un déploiement pour celui-ci. Accédez au manifeste de déploiement configuré pour votre plateforme qui se trouve dans le dossier **config** de votre solution Visual Studio, par exemple `deployment.arm32v7.json`.

1. Cliquez sur le bouton Actualiser pour voir les nouveaux modules en cours d’exécution, ainsi que le module **SimulatedTemperatureSensor**, et **$edgeAgent** et **$edgeHub**.

## <a name="view-generated-data"></a>Afficher les données générées

1. Afin de surveiller le message D2C pour un appareil IoT Edge spécifique, sélectionnez ce dernier dans votre hub IoT dans **Cloud Explorer**, puis, dans la fenêtre **Action**, cliquez sur **Démarrer la supervision du point de terminaison d’événement intégré**.

1. Pour arrêter la surveillance des données, sélectionnez **Arrêter la supervision du point de terminaison d’événement intégré** dans la fenêtre **Action**.

## <a name="next-steps"></a>Étapes suivantes

Pour développer des modules personnalisés pour vos appareils IoT Edge, voir [Comprendre et utiliser les kits Azure IoT Hub SDK](../iot-hub/iot-hub-devguide-sdks.md).
