---
title: Créer des flux de travail avec Azure Logic Apps monolocataire (standard) dans Visual Studio Code
description: Créez des flux de travail automatisés pour intégrer des applications, des données, des services et des systèmes avec le service Azure Logic Apps monolocataire (standard) dans Visual Studio Code.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 09/13/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: b71edfc5f57779bc96b165f8bdef2436ec9d16b3
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132552562"
---
# <a name="create-an-integration-workflow-with-single-tenant-azure-logic-apps-standard-in-visual-studio-code"></a>Créer un flux de travail d’intégration avec Azure Logic Apps monolocataire (standard) dans Visual Studio Code

Cet article explique comment créer un exemple de flux de travail d’intégration automatisé qui s’exécute dans l’*environnement Azure Logic Apps monolocataire* à l’aide de Visual Studio Code avec l’extension **Azure Logic Apps (standard)** . L’application logique que vous créez avec cette extension est basée sur le type de ressource **Logic Apps (standard)** , qui offre les fonctionnalités suivantes :

* Vous pouvez exécuter et tester localement des flux de travail dans l’environnement de développement Visual Studio Code.

* Votre application logique peut inclure plusieurs [flux de travail avec et sans état](single-tenant-overview-compare.md#stateful-stateless).

* Les flux de travail dans les mêmes application logique et locataire s’exécutent dans le même processus que le runtime Azure Logic Apps, de sorte qu’ils partagent les mêmes ressources et offrent de meilleures performances.

* Vous pouvez déployer le type de ressource **application logique (standard)** directement dans l’environnement Azure Logic Apps monolocataire ou partout où Azure Functions peut s’exécuter, y compris les conteneurs, en raison du runtime conteneurisé d’Azure Logic Apps.

Pour plus d’informations sur l’offre d’Azure Logic Apps monolocataire, passez en revue les [environnements monolocataires ou multilocataires et les services d’intégration](single-tenant-overview-compare.md).

Bien que l’exemple de flux de travail soit basé sur le cloud et ne comporte que deux étapes, vous pouvez créer des flux de travail à partir de centaines d’opérations qui peuvent connecter un vaste éventail d’applications, de données, de services et de systèmes dans des environnements cloud, locaux et hybrides. L’exemple de flux de travail démarre avec le déclencheur de demande intégré et enchaîne avec une action Office 365 Outlook. Le déclencheur crée un point de terminaison pouvant être appelé pour le flux de travail, et attend une demande HTTPS entrante en provenance d’un appelant quelconque. Lorsque le déclencheur reçoit une demande et se déclenche, l’action suivante s’exécute en envoyant un e-mail à l’adresse e-mail spécifiée avec des sorties sélectionnées à partir du déclencheur.

> [!TIP]
> Si vous n’avez pas de compte Office 365, vous pouvez utiliser toute autre action disponible pouvant envoyer des messages à partir de votre compte e-mail, par exemple, Outlook.com.
>
> Pour créer cet exemple de flux de travail à l’aide du portail Azure à la place, suivez les étapes décrites dans [Créer des flux de travail d’intégration à l’aide du service Azure Logic Apps monolocataire et du portail Azure](create-single-tenant-workflows-azure-portal.md). 
> Les deux options permettent de développer, d’exécuter et de déployer des flux de travail d’application logique dans les mêmes genres d’environnements. 
> Toutefois, avec Visual Studio Code, vous pouvez développer, tester et exécuter *localement* des flux de travail dans votre environnement de développement.

![Capture d’écran montrant Visual Studio Code, un projet d’application logique et un workflow.](./media/create-single-tenant-workflows-visual-studio-code/visual-studio-code-logic-apps-overview.png)

À mesure que vous allez progresser, vous allez effectuer les tâches de haut niveau suivantes :

* Créez un projet pour votre application logique et un [flux de travail *avec état*](single-tenant-overview-compare.md#stateful-stateless) vide.
* Ajoutez un déclencheur et une action.
* Exécutez, testez, déboguez et passez en revue l’historique des exécutions localement.
* Recherchez les détails relatifs au nom de domaine pour l’accès au pare-feu.
* Déployez sur Azure, ce qui implique éventuellement l’activation d’Application Insights.
* Gérez votre application logique déployée dans Visual Studio Code et le portail Azure.
* Activez l’historique des exécutions pour les workflows sans état.
* Activez ou ouvrez Application Insights après le déploiement.

## <a name="prerequisites"></a>Prérequis

### <a name="access-and-connectivity"></a>Accès et connectivité

* Accédez à Internet afin de pouvoir télécharger la configuration requise, connectez-vous à partir de Visual Studio Code à votre compte Azure, et publiez à partir de Visual Studio Code sur Azure.

* Un compte et un abonnement Azure. Si vous n’avez pas encore d’abonnement, vous pouvez [vous inscrire pour obtenir un compte Azure gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Pour créer le même exemple de flux de travail que celui présenté dans cet article, vous avez besoin d’un compte de courrier Office 365 Outlook qui utilise un compte Microsoft professionnel ou scolaire pour se connecter.

  Si vous choisissez un [autre connecteur de courrier](/connectors/connector-reference/connector-reference-logicapps-connectors), tel que Outlook.com, vous pouvez toujours suivre l’exemple. Les étapes globales générales sont les mêmes. Toutefois, vos options peuvent différer de plusieurs manières. Par exemple, si vous utilisez le connecteur Outlook.com, utilisez votre compte Microsoft personnel au lieu de vous connecter.

<a name="storage-requirements"></a>

### <a name="storage-requirements"></a>Exigences de stockage

Pour un développement local dans Visual Studio Code, vous devez configurer un magasin de données local que votre projet d’application logique et vos flux de travail puissent utiliser pour l’exécution dans votre environnement de développement local. Vous pouvez utiliser et exécuter l’émulateur de stockage Azurite comme magasin de données local.

1. Téléchargez et installez [azurite 3.12.0 ou une version ultérieure](https://www.npmjs.com/package/azurite).
1. Avant d’exécuter votre application logique, veillez à démarrer l’émulateur.

Pour plus d’informations, consultez la [documentation Azurite](https://github.com/Azure/Azurite#azurite-v3).

### <a name="tools"></a>Outils

* [Visual Studio Code](https://code.visualstudio.com/) qui est gratuit. Téléchargez et installez aussi ces outils pour Visual Studio Code si vous ne les avez pas encore :

  * [Extension de compte Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account), qui offre une expérience Azure commune de connexion et de filtrage des abonnements pour toutes les autres extensions Azure dans Visual Studio Code.

  * [Extension C# pour Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp), qui permet à la fonctionnalité F5 d’exécuter votre application logique.

  * [Azure Functions Core Tools, version 3.x](https://github.com/Azure/azure-functions-core-tools/releases/tag/3.0.3904) en utilisant la version Microsoft Installer (MSI), à savoir `func-cli-X.X.XXXX-x*.msi`. N’installez pas la version 4. x, qui n’est pas prise en charge et ne fonctionnera pas.

    Ces outils incluent une version du même runtime qui optimise le runtime d’Azure Functions que l’extension Azure Logic Apps (standard) utilise dans Visual Studio Code.

    > [!IMPORTANT]
    > Si vous disposez d’une installation antérieure à ces versions, commencez par la désinstaller, ou assurez-vous que la variable d’environnement PATH pointe vers la version que vous téléchargez et installez.

  * [Extension Azure Logic Apps (standard) pour Visual Studio Code](https://go.microsoft.com/fwlink/p/?linkid=2143167).

    > [!IMPORTANT]
    > Les projets créés avec des extensions en préversion antérieures ne fonctionnent plus. Pour continuer, désinstallez toutes les versions antérieures, puis recréez vos projets d’application logique.

    Pour installer l’extension **Azure Logic Apps (standard)** , procédez comme suit :

    1. Dans Visual Studio Code, dans la barre d’outils de gauche, sélectionnez **Extensions**.

    1. Dans la zone de recherche d’extensions, entrez `azure logic apps standard`. Dans la liste des résultats, sélectionnez **Azure Logic Apps (standard)** **>** **Installer**.

       Une fois l’installation terminée, l’extension apparaît dans la liste **Extensions : Installées**.

       ![Capture d’écran montrant Visual Studio Code avec l’extension Azure Logic Apps (standard) installée](./media/create-single-tenant-workflows-visual-studio-code/azure-logic-apps-extension-installed.png)

       > [!TIP]
       > Si l’extension n’apparaît pas dans la liste des extensions installées, essayez de redémarrer Visual Studio Code.

    À l’heure actuelle, vous pouvez installer en même temps les extensions consommation (multilocataire) et standard (monolocataure). Les expériences de développement diffèrent les unes des autres à certains égards, mais votre abonnement Azure peut inclure des types d’applications logiques tant standard et que consommation. Visual Studio Code affiche toutes les applications logiques déployées dans votre abonnement Azure, mais organise vos applications sous chaque extension, **Azure Logic Apps (consommation)** et **Azure Logic Apps (standard)** .

* Pour utiliser l’[action Opérations de code inlined](../logic-apps/logic-apps-add-run-inline-code.md) qui exécute JavaScript, installez [Node.js versions 10.x.x, 11.x.x ou 12.x.x](https://nodejs.org/en/download/releases/).

  > [!TIP]
  > Pour Windows, téléchargez la version MSI. Si vous utilisez plutôt la version ZIP, vous devez rendre Node.js disponible manuellement à l’aide d’une variable d’environnement PATH pour votre système d’exploitation.

* Pour exécuter localement des déclencheurs et des actions basés sur des webhooks, tels que le [déclencheur Webhook HTTP intégré](../connectors/connectors-native-webhook.md), dans Visual Studio Code, vous devez [configurer le transfert pour l’URL de rappel](#webhook-setup).

* Pour tester l’exemple de flux de travail dans cet article, vous avez besoin d’un outil pouvant envoyer des appels au point de terminaison créé par le déclencheur de demande. Si vous n’avez pas d’outil de ce type, vous pouvez télécharger, installer et utiliser l’application [Postman](https://www.postman.com/downloads/).

* Si vous créez vos ressources d’application logique avec des paramètres qui prennent en charge l’utilisation d’[Application Insights](../azure-monitor/app/app-insights-overview.md), vous pouvez choisir d’activer la journalisation et le suivi des diagnostics pour votre application logique. Vous pouvez le faire lorsque vous créez votre application logique ou après le déploiement. Vous devez disposer d’une instance d’Application Insights, mais vous pouvez créer cette ressource [à l’avance](../azure-monitor/app/create-workspace-resource.md), lorsque vous créez votre application logique, ou après le déploiement.

<a name="set-up"></a>

## <a name="set-up-visual-studio-code"></a>Configurer Visual Studio Code

1. Pour vous assurer que toutes les extensions sont correctement installées, rechargez ou redémarrez Visual Studio Code.

1. Vérifiez que Visual Studio Code détecte et installe automatiquement les mises à jour d’extensions, afin que toutes vos extensions reçoivent les dernières mises à jour. Dans le cas contraire, vous devez désinstaller manuellement la version obsolète et installer la version la plus récente.

   1. Dans le menu **Fichier**, accédez à **Préférences** **>** **Paramètres**.

   1. Sous l’onglet **Utilisateur**, accédez à **Fonctionnalités** **>** **Extensions**.

   1. Vérifiez que les options **Vérification automatique des mises à jour** et **Mise à jour automatique** sont sélectionnées.

Par défaut, les paramètres suivants sont activés et définis pour l’extension Azure Logic Apps (standard) :

* **Azure Logic Apps Standard : Project Runtime**, qui est défini sur la version **~3**

  > [!NOTE]
  > Cette version est obligatoire pour utiliser les [actions Opérations de code inlined](../logic-apps/logic-apps-add-run-inline-code.md).

* **Azure Logic Apps Standard : Experimental View Manager**, qui active le dernier concepteur dans Visual Studio Code. Si vous rencontrez des problèmes sur le concepteur, notamment pour déplacer et déposer des éléments, désactivez ce paramètre.

Pour rechercher et confirmer ces paramètres, effectuez ces étapes :

1. Dans le menu **Fichier**, accédez à **Préférences** **>** **Paramètres**.

1. Sous l’onglet **Utilisateur**, accédez à **>** **Extensions** **>** **Azure Logic Apps (standard)** .

   Par exemple, vous pouvez trouver le paramètre **Azure Logic Apps Standard : Project Runtime** ici ou utiliser la zone de recherche pour rechercher d’autres paramètres :

   ![Capture d’écran montrant les paramètres de Visual Studio Code pour l’extension « Azure Logic Apps (standard) ».](./media/create-single-tenant-workflows-visual-studio-code/azure-logic-apps-settings.png)

<a name="connect-azure-account"></a>

## <a name="connect-to-your-azure-account"></a>Se connecter au compte Azure

1. Dans la barre d’activités de Visual Studio Code, sélectionnez l’icône Azure.

   ![Capture d’écran montrant la barre d’activités de Visual Studio Code et l’icône Azure sélectionnée.](./media/create-single-tenant-workflows-visual-studio-code/visual-studio-code-azure-icon.png)

1. Dans le volet Azure, sous **Azure : Logic Apps (standard)** , sélectionnez **Se connecter à Azure**. Quand la page d’authentification de Visual Studio Code s’affiche, connectez-vous avec votre compte Azure.

   ![Capture d’écran montrant le volet Azure et le lien sélectionné pour la connexion à Azure.](./media/create-single-tenant-workflows-visual-studio-code/sign-in-azure-subscription.png)

   Une fois que vous être connecté, le volet Azure affiche les abonnements dans votre compte Azure. Si vous disposez également de l’extension publiquement publiée, vous trouverez toutes les applications logiques que vous avez créées avec cette extension dans la section **Logic Apps**, et non dans la section **Logic Apps (standard)** .

   Si les abonnements attendus n’apparaissent pas, ou si vous souhaitez que le volet affiche uniquement des abonnements spécifiques, procédez comme suit :

   1. Dans la liste des abonnements, positionnez votre pointeur à côté du premier abonnement jusqu’à ce que le bouton **Sélectionner les abonnements** (icône de filtre) s’affiche. Sélectionnez l’icône de filtre.

      ![Capture d’écran montrant le volet Azure et l’icône de filtre sélectionnée.](./media/create-single-tenant-workflows-visual-studio-code/filter-subscription-list.png)

      Ou, dans la barre d’état de Visual Studio Code, sélectionnez votre compte Azure. 

   1. Quand une autre liste d’abonnements s’affiche, sélectionnez les abonnements de votre choix, puis n’oubliez pas de sélectionner **OK**.

<a name="create-project"></a>

## <a name="create-a-local-project"></a>Créer un projet local

Avant de pouvoir créer votre application logique, créez un projet local afin de pouvoir gérer, exécuter et déployer votre application logique à partir de Visual Studio Code. Le projet sous-jacent est similaire à un projet Azure Functions, également appelé projet d’application de fonction. Toutefois, ces types de projets sont distincts les uns des autres. Par conséquent, les applications logiques et les applications de fonction ne peuvent pas exister dans le même projet.

1. Sur votre ordinateur, créez un dossier local *vide* à utiliser pour le projet que vous créerez par la suite dans Visual Studio Code.

1. Dans Visual Studio Code, fermez tous les dossiers ouverts.

1. Dans le volet Azure, en regard de **Azure : Logic Apps (standard)** , sélectionnez **Créer un projet** (icône représentant un dossier et un éclair).

   ![Capture d’écran montrant la barre d’outils du volet Azure avec l’option « Créer un projet » activée.](./media/create-single-tenant-workflows-visual-studio-code/create-new-project-folder.png)

1. Si le pare-feu Windows Defender vous invite à accorder l’accès au réseau pour `Code.exe`, c’est-à-dire Visual Studio Code, ainsi que pour `func.exe`, c’est-à-dire Azure Functions Core Tools, sélectionnez **Réseaux privés, tels qu’un réseau domestique ou un réseau d’entreprise** **>** **Autoriser l’accès**.

1. Accédez à l’emplacement où vous avez créé votre dossier de projet, sélectionnez ce dossier et continuez.

   ![Capture d’écran montrant la boîte de dialogue « Sélectionner un dossier » avec un dossier de projet nouvellement créé et le bouton « Sélectionner » sélectionné.](./media/create-single-tenant-workflows-visual-studio-code/select-project-folder.png)

1. Dans la liste des modèles qui s’affiche, sélectionnez **Flux de travail avec état** ou **Flux de travail sans état**. Cet exemple sélectionne **Flux de travail avec état**.

   ![Capture d’écran montrant la liste des modèles de flux de travail avec l’option « Flux de travail avec état » sélectionnée.](./media/create-single-tenant-workflows-visual-studio-code/select-stateful-stateless-workflow.png)

1. Spécifiez un nom pour le workflow, puis appuyez sur Entrée. L’exemple suivant utilise `Fabrikam-Stateful-Workflow` en tant que nom.

   ![Capture d’écran montrant la zone « Créer un workflow avec état (3/4) » et « Fabrikam-Stateful-Workflow » comme nom de workflow.](./media/create-single-tenant-workflows-visual-studio-code/name-your-workflow.png)

   Visual Studio Code termine la création de votre projet et ouvre le fichier **workflow.json** pour votre workflow dans l’éditeur de code.

   > [!NOTE]
   > Si vous êtes invité à sélectionner le mode d’ouverture de votre projet, sélectionnez **Ouvrir dans la fenêtre active** si vous voulez ouvrir votre projet dans la fenêtre Visual Studio Code active. Pour ouvrir une nouvelle instance de Visual Studio Code, sélectionnez **Ouvrir dans une nouvelle fenêtre**.

1. À partir de la barre d’outils de Visual Studio, ouvrez le volet de l’Explorateur, s’il n’est pas déjà ouvert.

   Le volet de l’Explorateur affiche votre projet, qui comprend désormais les fichiers de projet générés automatiquement. Par exemple, le projet contient un dossier qui affiche le nom de votre workflow. À l’intérieur de ce dossier, le fichier **workflow.json** contient la définition JSON sous-jacente de votre workflow.

   ![Capture d’écran montrant le volet de l’Explorateur avec le dossier de projet, le dossier de workflow et le fichier « workflow.json ».](./media/create-single-tenant-workflows-visual-studio-code/local-project-created.png)

   [!INCLUDE [Visual Studio Code - logic app project structure](../../includes/logic-apps-single-tenant-project-structure-visual-studio-code.md)]

<a name="enable-built-in-connector-authoring"></a>

## <a name="enable-built-in-connector-authoring"></a>Activer la création de connecteurs intégrés

Vous pouvez créer vos propres connecteurs intégrés pour tous les services dont vous avez besoin à l’aide du [framework d’extensibilité Azure Logic Apps monolocataire](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-built-in-connector/ba-p/1921272). Comme pour les connecteurs intégrés tels qu’Azure Service Bus et SQL Server, ces connecteurs offrent un débit plus élevé, une faible latence, une connectivité locale, et s’exécutent en mode natif dans le même processus que le runtime d’Azure Logic Apps monolocataire.

Actuellement, la fonctionnalité de création est uniquement disponible dans Visual Studio Code, mais elle n’est pas activée par défaut. Pour créer ces connecteurs, vous devez d’abord convertir votre projet de l’extension (Node.js) en projet basé sur un package NuGet (.NET).

> [!IMPORTANT]
> Cette action relève d’une opération unidirectionnelle irréversible.

1. Dans le volet Explorateur, à la racine de votre projet, placez le pointeur de la souris sur une zone vide sous tous les autres fichiers et dossiers, ouvrez le menu contextuel, puis sélectionnez **Convertir en projet d’application logique NuGet**.

   ![Capture d’écran montrant le volet Explorateur avec le menu contextuel du projet ouvert à partir d’une zone vide dans la fenêtre du projet.](./media/create-single-tenant-workflows-visual-studio-code/convert-logic-app-project.png)

1. Lorsque l’invite s’affiche, confirmez la conversion du projet.

1. Pour continuer, passez en revue et suivez les étapes décrites dans l’article [Azure Logic Apps s’exécutant partout – Extensibilité de connecteur intégré](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-built-in-connector/ba-p/1921272).

<a name="open-workflow-definition-designer"></a>

## <a name="open-the-workflow-definition-file-in-the-designer"></a>Ouvrir le fichier de définition du workflow dans le concepteur

1. Vérifiez les versions installées sur votre ordinateur en exécutant cette commande :

   `..\Users\{yourUserName}\dotnet --list-sdks`

   Si vous disposez du kit SDK .NET Core 5.x, cette version peut vous empêcher d’ouvrir la définition de workflow sous-jacente de l’application logique dans le concepteur. Au lieu de désinstaller cette version, dans le dossier racine de votre projet, créez un fichier **global.json** qui fait référence à la version 3.x du runtime de .NET Core que vous avez et qui est ultérieure à la version 3.1.201, par exemple :

   ```json
   {
      "sdk": {
         "version": "3.1.8",
         "rollForward": "disable"
      }
   }
   ```

   > [!IMPORTANT]
   > Veillez à ajouter explicitement le fichier **global.json** dans le dossier racine de votre projet à partir de Visual Studio Code. Sinon, le concepteur ne s’ouvre pas.

1. Développez le dossier du projet pour votre flux de travail. Ouvrez le menu contextuel du fichier **workflow.json**, puis sélectionnez **Ouvrir dans le concepteur**.

   ![Capture d’écran montrant le volet de l’Explorateur et la fenêtre contextuelle du fichier workflow.json avec l’option « Ouvrir dans le concepteur » sélectionnée.](./media/create-single-tenant-workflows-visual-studio-code/open-definition-file-in-designer.png)

1. Dans la liste **Enable connectors in Azure** (Activer les connecteurs dans Azure), sélectionnez l’option **Use connectors from Azure** (Utiliser les connecteurs d’Azure) qui s’applique à tous les connecteurs managés disponibles et déployés dans Azure, pas seulement aux connecteurs destinés à des services Azure.

   ![Capture d’écran montrant le volet de l’Explorateur avec la liste « Activer les connecteurs dans Azure » ouverte et l’option « Utiliser les connecteurs d’Azure » sélectionnée.](./media/create-single-tenant-workflows-visual-studio-code/use-connectors-from-azure.png)

   > [!NOTE]
   > Actuellement, les flux de travail sans état prennent en charge uniquement les *actions* pour les [connecteurs gérés](../connectors/managed.md) déployés dans Azure, pas pour des déclencheurs. Bien que vous ayez la possibilité d’activer des connecteurs dans Azure pour votre flux de travail sans état, le concepteur n’affiche aucun déclencheur de connecteur managé à sélectionner.

1. Dans la liste **Sélectionner un abonnement**, sélectionnez l’abonnement Azure à utiliser pour votre projet d’application logique.

   ![Capture d’écran montrant le volet de l’Explorateur avec la zone « Sélectionner un abonnement » et votre abonnement sélectionné.](./media/create-single-tenant-workflows-visual-studio-code/select-azure-subscription.png)

1. Dans la liste des groupes de ressources, sélectionnez **Créer un groupe de ressources**.

   ![Capture d’écran montrant le volet de l’Explorateur avec une liste de groupes de ressources et l’option « Créer un groupe de ressources » sélectionnée.](./media/create-single-tenant-workflows-visual-studio-code/create-select-resource-group.png)

1. Spécifiez un nom pour votre groupe de ressources, puis appuyez sur Entrée. Cet exemple utilise `Fabrikam-Workflows-RG`.

   ![Capture d’écran montrant le volet de l’Explorateur et la zone du nom du groupe de ressources.](./media/create-single-tenant-workflows-visual-studio-code/enter-name-for-resource-group.png)

1. Dans la liste des emplacements, sélectionnez la région Azure à utiliser lors de la création de votre groupe de ressources et de vos ressources. Cet exemple utilise la région **USA Centre-Ouest**.

   ![Capture d’écran montrant le volet de l’Explorateur avec la liste des emplacements et la région « USA Centre-Ouest » sélectionnée.](./media/create-single-tenant-workflows-visual-studio-code/select-azure-region.png)

   Une fois cette étape accomplie, Visual Studio Code ouvre le concepteur de workflow.

   > [!NOTE]
   > Quand Visual Studio Code démarre l’API de workflow au moment de la conception, vous pouvez recevoir un message indiquant que le démarrage peut prendre quelques secondes. Vous pouvez ignorer ce message ou sélectionner **OK**.
   >
   > Si le concepteur ne s’ouvre pas, consultez la section de résolution des problèmes, [Échec de l’ouverture du concepteur](#designer-fails-to-open).

   Une fois le concepteur affiché, l’invite **Choisir une opération** apparaît sur le concepteur. Elle est sélectionnée par défaut, ce qui a pour effet d’afficher le volet **Ajouter une action**.

   ![Capture d’écran illustrant le concepteur de workflow.](./media/create-single-tenant-workflows-visual-studio-code/workflow-designer.png)

1. Ensuite, [ajoutez un déclencheur et des actions](#add-trigger-actions) à votre flux de travail.

<a name="add-trigger-actions"></a>

## <a name="add-a-trigger-and-actions"></a>Ajouter un déclencheur et des actions

Une fois que vous avez ouvert le concepteur, l’invite **Choisir une opération** s’affiche sur le concepteur et est sélectionnée par défaut. Vous pouvez maintenant commencer à créer votre flux de travail en ajoutant un déclencheur et des actions.

Dans cet exemple, le workflow utilise le déclencheur et les actions suivantes :

* [Déclencheur de demande](../connectors/connectors-native-reqres.md) intégré, **Lors de la réception d’une requête HTTP**, qui reçoit les appels ou requêtes entrants, et crée un point de terminaison que d’autres services ou applications logiques peuvent appeler.

* [Action Office 365 Outlook](../connectors/connectors-create-api-office365-outlook.md), **Envoyer un e-mail**.

* [Action Réponse](../connectors/connectors-native-reqres.md) intégrée, qui permet d’envoyer une réponse et de retourner des données à l’appelant.

### <a name="add-the-request-trigger"></a>Ajout du déclencheur Request

1. En regard du concepteur, dans le volet **Ajouter un déclencheur**, dans la zone de recherche **Choisir une opération**, assurez-vous que l’option **Intégrée** est activée de sorte que vous pouvez sélectionner un déclencheur qui s’exécute en mode natif.

1. Dans la zone de recherche **Choisir une opération**, entrez `when a http request`, puis sélectionnez le déclencheur de requête intégré nommé **Lors de la réception d’une requête HTTP**.

   ![Capture d’écran illustrant le concepteur de workflow et le volet **Ajouter un déclencheur** avec le déclencheur « Lors de la réception d’une requête HTTP » sélectionné.](./media/create-single-tenant-workflows-visual-studio-code/add-request-trigger.png)

   Lorsque le déclencheur s’affiche sur le concepteur, le volet d’informations du déclencheur s’ouvre pour afficher les propriétés, les paramètres et d’autres actions du déclencheur.

   ![Capture d’écran illustrant le concepteur de workflow avec le déclencheur « Lors de la réception d’une requête HTTP » sélectionné et le volet d’informations du déclencheur ouvert.](./media/create-single-tenant-workflows-visual-studio-code/request-trigger-added-to-designer.png)

   > [!TIP]
   > Si le volet d’informations ne s’affiche pas, assurez-vous que le déclencheur est sélectionné sur le concepteur.

1. Si vous devez supprimer un élément du concepteur, [procédez comme suit](#delete-from-designer).

### <a name="add-the-office-365-outlook-action"></a>Ajouter l’action Office 365 Outlook

1. Dans le concepteur, sous le déclencheur que vous avez ajouté, sélectionnez le signe plus ( **+** ) > **Ajouter une action**.

   L’invite **Choisir une opération** s’affiche sur le concepteur, et le volet **Ajouter une action** se rouvre pour vous permettre de sélectionner l’action suivante.

1. Dans le volet **Ajouter une action**, dans la zone de recherche **Choisir une opération**, sélectionnez **Azure** afin de pouvoir sélectionner une action pour un connecteur managé déployé dans Azure.

   Cet exemple sélectionne et utilise l’action Office 365 Outlook **Envoyer un e-mail (V2)** .

   ![Capture d’écran montrant le concepteur de workflow et le volet **Ajouter une action** avec l’action Office 365 Outlook « Envoyer un e-mail » sélectionnée.](./media/create-single-tenant-workflows-visual-studio-code/add-send-email-action.png)

1. Dans le volet d’informations de l’action, sélectionnez **Se connecter** pour pouvoir créer une connexion à votre compte de courrier.

   ![Capture d’écran montrant le concepteur de workflow et le volet **Envoyer un e-mail (V2)** avec l’option « Se connecter » sélectionnée.](./media/create-single-tenant-workflows-visual-studio-code/send-email-action-sign-in.png)

1. Lorsque Visual Studio Code vous invite à donner votre consentement pour l’accès à votre compte de courrier, sélectionnez **Ouvrir**.

   ![Capture d’écran montrant l’invite de Visual Studio Code pour autoriser l’accès.](./media/create-single-tenant-workflows-visual-studio-code/visual-studio-code-open-external-website.png)

   > [!TIP]
   > Pour éviter les invites futures, sélectionnez **Configurer les domaines approuvés** afin de pouvoir ajouter la page d’authentification en tant que domaine approuvé.

1. Suivez les invites suivantes pour vous connecter, autoriser l’accès et autoriser le retour à Visual Studio Code.

   > [!NOTE]
   > Si trop de temps s’écoule avant que vous répondiez aux invites, le processus d’authentification expire et échoue. Dans ce cas, revenez au concepteur et réessayez de vous connecter pour créer la connexion.

1. Lorsque l’extension Azure Logic Apps (standard) vous invite à donner votre consentement pour l’accès à votre compte de messagerie, sélectionnez **Ouvrir**. Suivez l’invite suivante pour autoriser l’accès.

   ![Capture d’écran montrant l’invite d’extension pour autoriser l’accès.](./media/create-single-tenant-workflows-visual-studio-code/allow-extension-open-uri.png)

   > [!TIP]
   > Pour éviter les invites futures, sélectionnez **Ne plus demander cette extension**.

   Une fois que Visual Studio Code a créé votre connexion, certains connecteurs affichent le message `The connection will be valid for {n} days only`. Cette limite de temps s’applique uniquement à la durée pendant laquelle vous créez votre application logique dans Visual Studio Code. Après le déploiement, cette limite ne s’applique plus, car votre application logique peut s’authentifier au moment de l’exécution à l’aide de son [identité managée affectée par le système](../logic-apps/create-managed-service-identity.md) qui est activée automatiquement. Cette identité managée diffère des informations d’identification d’authentification ou de la chaîne de connexion que vous utilisez lors de la création d’une connexion. Si vous désactivez cette identité managée affectée par le système, les connexions ne fonctionneront pas au moment de l’exécution.

1. Dans le concepteur, si l’action **Envoyer un e-mail** n’est pas sélectionnée, sélectionnez-la.

1. Dans le volet d’informations de l’action, sous l’onglet **Paramètres**, fournissez les informations requises pour l’action, par exemple :

   ![Capture d’écran montrant le concepteur de workflow avec les détails de l’action Office 365 Outlook « Envoyer un e-mail ».](./media/create-single-tenant-workflows-visual-studio-code/send-email-action-details.png)

   | Propriété | Obligatoire | Valeur | Description |
   |----------|----------|-------|-------------|
   | **To** | Oui | <*your-email-address*> | Destinataire de l’e-mail, qui peut être votre adresse de messagerie à des fins de test. Cet exemple utilise l’e-mail fictif `sophiaowen@fabrikam.com`. |
   | **Subject** | Oui | `An email from your example workflow` | L’objet de l’e-mail |
   | **Corps** | Oui | `Hello from your example workflow!` | Contenu du corps de l’e-mail. |
   ||||

   > [!NOTE]
   > Si vous souhaitez apporter des modifications dans le volet d’informations sous l’onglet **Paramètres**, **Résultat statique** ou **Exécuter après**, veillez à sélectionner **Terminé** pour valider ces modifications avant de changer d’onglet ou de passer au concepteur. Dans le cas contraire, Visual Studio Code ne conserve pas vos modifications.

1. Dans le concepteur, sélectionnez **Enregistrer**.

> [!IMPORTANT]
> Pour exécuter localement un workflow qui utilise un déclencheur ou des actions basés sur un webhook, tels que le [déclencheur ou l’action intégré Webhook HTTP](../connectors/connectors-native-webhook.md), vous devez activer cette capacité en [configurant le transfert pour l’URL de rappel du webhook](#webhook-setup).

<a name="webhook-setup"></a>

## <a name="enable-locally-running-webhooks"></a>Activer les webhooks exécutés localement

Quand vous utilisez un déclencheur ou une action basé sur un webhook, par exemple **Webhook HTTP**, avec une application logique s’exécutant dans Azure, le runtime de Logic Apps s’abonne au point de terminaison de service en générant et en inscrivant une URL de rappel avec ce point de terminaison. Le déclencheur ou l’action attend ensuite que le point de terminaison de service appelle l’URL. Toutefois, lorsque vous travaillez dans Visual Studio Code, l’URL de rappel générée commence par `http://localhost:7071/...`. Cette URL est destinée à votre serveur localhost, qui est privé, de sorte que le point de terminaison de service ne peut pas appeler cette URL.

Pour exécuter localement des déclencheurs et des actions basés sur un webhook dans Visual Studio Code, vous devez configurer une URL publique qui expose votre serveur localhost et transfère en toute sécurité les appels du point de terminaison de service à l’URL de rappel du webhook. Vous pouvez utiliser un service de transfert et un outil comme [**ngrok**](https://ngrok.com/), qui ouvre un tunnel HTTP sur votre port localhost, ou vous pouvez utiliser votre propre outil équivalent.

#### <a name="set-up-call-forwarding-using-ngrok"></a>Configurer le transfert d’appel à l’aide de **ngrok**

1. [Inscrivez-vous pour obtenir un compte **ngrok**](https://dashboard.ngrok.com/signup) si vous n’en avez pas. Sinon, [connectez-vous à votre compte](https://dashboard.ngrok.com/login).

1. Récupérez votre jeton d’authentification personnel, dont votre client **ngrok** a besoin pour se connecter et authentifier l’accès à votre compte.

   1. Pour trouver votre [page de jeton d’authentification](https://dashboard.ngrok.com/auth/your-authtoken), dans le menu du tableau de bord de votre compte, développez **Authentication** (Authentification), puis sélectionnez **Your Authtoken** (Votre jeton d’authentification).

   1. À partir de la zone **Your Authtoken** (Votre jeton d’authentification), copiez le jeton dans un emplacement sûr.

1. À partir de la [page de téléchargement **ngrok**](https://ngrok.com/download) ou du [tableau de bord de votre compte](https://dashboard.ngrok.com/get-started/setup), téléchargez la version **ngrok** de votre choix et extrayez le fichier .zip. Pour plus d'informations, consultez [Étape 1 : Unzip to install](https://ngrok.com/download) (Décompressez pour installer).

1. Sur votre ordinateur, ouvrez l’outil d’invite de commandes. Accédez à l’emplacement où se trouve le fichier **ngrok.exe**.

1. Connectez le client **ngrok** à votre compte **ngrok** en exécutant la commande suivante. Pour plus d'informations, consultez [Étape 2 : Connect your account](https://ngrok.com/download) (Connectez votre compte).

   `ngrok authtoken <your_auth_token>`

1. Ouvrez le tunnel HTTP sur le port localhost 7071 en exécutant la commande suivante. Pour plus d'informations, consultez [Étape 3 : Fire it up](https://ngrok.com/download) (Lancez le logiciel).

   `ngrok http 7071`

1. Dans la sortie, recherchez la ligne suivante :

   `http://<domain>.ngrok.io -> http://localhost:7071`

1. Copiez et enregistrez l’URL qui a le format suivant : `http://<domain>.ngrok.io`.

#### <a name="set-up-the-forwarding-url-in-your-app-settings"></a>Configurer l’URL de transfert dans les paramètres de votre application

1. Dans Visual Studio Code, dans le concepteur, ajoutez le déclencheur ou l’action **HTTP + Webhook**.

1. Quand l’invite s’affiche pour l’emplacement du point de terminaison hôte, entrez l’URL de transfert (redirection) que vous avez créée précédemment.

   > [!NOTE]
   > Si vous ignorez l’invite, un avertissement s’affiche pour vous indiquer que vous devez fournir l’URL de transfert. Sélectionnez alors **Configurer**, puis entrez l’URL. Cette étape terminée, l’invite ne réapparaît plus pour les déclencheurs ou actions webhook ultérieurs que vous êtes éventuellement amené à ajouter.
   >
   > Pour que l’invite réapparaisse, au niveau de la racine de votre projet, ouvrez le menu contextuel du fichier **local.settings.js**, puis sélectionnez **Configurer le point de terminaison de redirection de webhook**. L’invite s’affiche alors pour vous permettre de fournir l’URL de transfert.

   Visual Studio Code ajoute l’URL de transfert au fichier **local.settings.json** situé dans le dossier racine de votre projet. Dans l’objet `Values`, la propriété qui est nommée `Workflows.WebhookRedirectHostUri` apparaît alors. Elle est définie sur l’URL de transfert, par exemple :

   ```json
   {
      "IsEncrypted": false,
      "Values": {
         "AzureWebJobsStorage": "UseDevelopmentStorage=true",
         "FUNCTIONS_WORKER_RUNTIME": "node",
         "FUNCTIONS_V2_COMPATIBILITY_MODE": "true",
         <...>
         "Workflows.WebhookRedirectHostUri": "http://xxxXXXXxxxXXX.ngrok.io",
         <...>
      }
   }
   ```

La première fois que vous démarrez une session de débogage locale ou que vous exécutez le workflow sans débogage, le runtime de Logic Apps inscrit le workflow auprès du point de terminaison de service et s’abonne à ce point de terminaison pour notifier les opérations du webhook. La prochaine fois que votre workflow sera exécuté, le runtime ne s’inscrira pas ni ne se réabonnera, car l’inscription de l’abonnement existe déjà dans le stockage local.

Lorsque vous arrêtez la session de débogage pour une exécution de workflow qui utilise des déclencheurs ou des actions basés sur un webhook exécuté localement, les inscriptions d’abonnement existantes ne sont pas supprimées. Pour les désinscrire, vous devez supprimer manuellement les inscriptions d’abonnement.

> [!NOTE]
> Une fois que votre workflow commence à s’exécuter, la fenêtre de terminal peut afficher des erreurs telles que l’exemple suivant :
>
> `message='Http request failed with unhandled exception of type 'InvalidOperationException' and message: 'System.InvalidOperationException: Synchronous operations are disallowed. Call ReadAsync or set AllowSynchronousIO to true instead.`
>
> Dans ce cas, ouvrez le fichier **local.settings.json** situé dans le dossier racine de votre projet et vérifiez que la propriété est définie sur `true` :
>
> `"FUNCTIONS_V2_COMPATIBILITY_MODE": "true"`

<a name="manage-breakpoints"></a>

## <a name="manage-breakpoints-for-debugging"></a>Gérer les points d’arrêt pour le débogage

Avant d’exécuter et de tester le workflow de votre application logique en démarrant une session de débogage, vous pouvez définir des [points d’arrêt](https://code.visualstudio.com/docs/editor/debugging#_breakpoints) dans le fichier **workflow.json** pour chaque workflow. Aucune autre configuration n’est requise. 

À l’heure actuelle, les points d’arrêt sont pris en charge uniquement pour les actions, et non pour les déclencheurs. Chaque définition d’action comporte ces emplacements de point d’arrêt :

* Définissez le point d’arrêt de départ sur la ligne qui indique le nom de l’action. Lorsque ce point d’arrêt est atteint pendant la session de débogage, vous pouvez examiner les entrées de l’action avant qu’elles soient évaluées.

* Définissez le point d’arrêt final sur la ligne qui indique l’accolade fermante de l’action ( **}** ). Lorsque ce point d’arrêt est atteint pendant la session de débogage, vous pouvez examiner les résultats de l’action avant que l’action se termine.

Pour ajouter un point d’arrêt, procédez comme suit :

1. Ouvrez le fichier **workflow.json** du workflow que vous souhaitez déboguer.

1. Sélectionnez dans la colonne de gauche la ligne où vous souhaitez définir le point d’arrêt. Pour supprimer le point d’arrêt, sélectionnez-le.

   Lorsque vous démarrez votre session de débogage, la vue Exécution apparaît sur le côté gauche de la fenêtre de code, tandis que la barre d’outils de débogage apparaît en haut.

   > [!NOTE]
   > Si la vue Exécution ne s’affiche pas automatiquement, appuyez sur Ctrl+Maj+D.

1. Pour passer en revue les informations disponibles lorsqu’un point d’arrêt est atteint, dans la vue Exécution, examinez le volet **Variables**.

1. Pour continuer l’exécution du workflow, dans la barre d’outils de débogage, sélectionnez **Continuer** (bouton Lecture).

Vous pouvez ajouter et supprimer des points d’arrêt à tout moment pendant l’exécution du workflow. Toutefois, si vous mettez à jour le fichier **workflow.json** après le démarrage de l’exécution, les points d’arrêt ne sont pas automatiquement mis à jour. Pour mettre à jour les points d’arrêt, redémarrez l’application logique.

Pour obtenir des informations générales, consultez [Points d’arrêt – Visual Studio Code](https://code.visualstudio.com/docs/editor/debugging#_breakpoints).

<a name="run-test-debug-locally"></a>

## <a name="run-test-and-debug-locally"></a>Exécuter, tester et déboguer localement

Pour tester votre application logique, procédez comme suit pour démarrer une session de débogage et rechercher l’URL du point de terminaison créé par le déclencheur Requête. Vous avez besoin de cette URL pour pouvoir envoyer ultérieurement une requête à ce point de terminaison.

1. Pour faciliter le débogage d’un workflow sans état, vous pouvez [activer l’historique des exécutions pour ce workflow](#enable-run-history-stateless).

1. Dans la barre d’activités de Visual Studio Code, ouvrez le menu **Exécuter**, puis sélectionnez **Démarrer le débogage** (F5).

   La fenêtre **Terminal** s’ouvre pour vous permettre d’examiner la session de débogage.

   > [!NOTE]
   > Si vous obtenez l’erreur **« Une erreur existe après l’exécution de preLaunchTask 'generateDebugSymbols' »** », consultez la section de résolution des problèmes, [Échec du démarrage de la session de débogage](#debugging-fails-to-start).

1. À présent, recherchez l’URL de rappel du point de terminaison sur le déclencheur de demande.

   1. Rouvrez le volet de l’Explorateur afin de pouvoir afficher votre projet.

   1. Dans le menu contextuel du fichier **workflow.json**, sélectionnez **Vue d’ensemble**.

      ![Capture d’écran montrant le volet de l’Explorateur et la fenêtre contextuelle du fichier workflow.json avec l’option « vue d’ensemble » sélectionnée.](./media/create-single-tenant-workflows-visual-studio-code/open-workflow-overview.png)

   1. Recherchez la valeur **URL de rappel**, qui ressemble à cette URL pour l’exemple de déclencheur de demande :

      `http://localhost:7071/api/<workflow-name>/triggers/manual/invoke?api-version=2020-05-01-preview&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=<shared-access-signature>`

      ![Capture d’écran montrant la page de présentation de votre flux de travail avec l’URL de rappel.](./media/create-single-tenant-workflows-visual-studio-code/find-callback-url.png)

1. Pour tester l’URL de rappel en déclenchant le flux de travail de l’application logique, ouvrez [Postman](https://www.postman.com/downloads/) ou votre outil préféré pour la création et l’envoi de requêtes.

   Cet exemple se poursuit en utilisant Postman. Pour plus d’informations, consultez [Prise en main de Postman](https://learning.postman.com/docs/getting-started/introduction/).

   1. Dans la barre d’outils de Postman, sélectionnez **Nouveau**.

      ![Capture d’écran montrant Postman avec le bouton Nouveau sélectionné.](./media/create-single-tenant-workflows-visual-studio-code/postman-create-request.png)

   1. Dans le volet **Créer**, sous **Blocs de construction**, sélectionnez **Requête**.

   1. Dans la fenêtre **Enregistrer la requête**, sous **Nom de la requête**, entrez un nom pour la requête, par exemple `Test workflow trigger`.

   1. Sous **Sélectionner une collection ou un dossier dans lesquels enregistrer**, sélectionnez **Créer une collection**.

   1. Sous **Toutes les collections**, fournissez un nom pour la collection à créer afin d’organiser vos demandes, appuyez sur Entrée, puis sélectionnez **Enregistrer dans <*nom-collection*>** . Cet exemple utilise `Logic Apps requests` comme nom de collection.

      Dans Postman, le volet de demande s’ouvre pour vous permettre d’envoyer une demande à l’URL de rappel du déclencheur de demande.

      ![Capture d’écran montrant Postman avec le volet de demande ouvert](./media/create-single-tenant-workflows-visual-studio-code/postman-request-pane.png)

   1. Revenez à Visual Studio Code. À partir de la page de présentation du flux de travail, copiez la valeur de la propriété **URL de rappel**.

   1. Revenez à Postman. Dans le volet de demande, dans la liste de méthodes suivante qui affiche actuellement la méthode de demande par défaut **GET**, collez l’URL de rappel précédemment copiée dans la zone d’adresse, puis sélectionnez **Envoyer**.

      ![Capture d’écran montrant Postman et l’URL de rappel dans la zone d’adresse avec le bouton Envoyer sélectionné.](./media/create-single-tenant-workflows-visual-studio-code/postman-test-call-back-url.png)

      L’exemple de flux de travail d’application logique envoie un e-mail qui ressemble à ceci :

      ![Capture d’écran montrant l’e-mail Outlook décrit dans l’exemple.](./media/create-single-tenant-workflows-visual-studio-code/workflow-app-result-email.png)

1. Dans Visual Studio Code, revenez à la page de présentation de votre flux de travail.

   Si vous avez créé un flux de travail avec état, une fois que la demande envoyée déclenche le flux de travail, la page de présentation affiche l’historique et l’état d’exécution du flux de travail.

   > [!TIP]
   > Si l’état d’exécution ne s’affiche pas, essayez d’actualiser la page de présentation en sélectionnant **Actualiser**. Aucune exécution ne se produit pour un déclencheur ignoré en raison de critères non respectés ou d’une absence de données.

   ![Capture d’écran montrant la page de présentation du flux de travail avec l’état et l’historique d’exécution](./media/create-single-tenant-workflows-visual-studio-code/post-trigger-call.png)

   | État de l’exécution | Description |
   |------------|-------------|
   | **Abandonné** | L’exécution s’est arrêtée ou ne s’est pas terminée en raison de problèmes externes, par exemple, une panne système ou un abonnement Azure expiré. |
   | **Annulé** | L’exécution a été déclenchée et a démarré, mais a reçu une demande d’annulation. |
   | **Échec** | Au moins une action de l’exécution a échoué. Aucune action subséquente n’était configurée dans le flux de travail pour gérer l’échec. |
   | **Exécution** | L’exécution a été déclenchée et est en cours, mais cet état peut également apparaître pour une exécution limitée en raison de [limites d’action](logic-apps-limits-and-config.md) ou du [plan de tarification actuel](https://azure.microsoft.com/pricing/details/logic-apps/). <p><p>**Conseil** : Si vous configurez la [journalisation des diagnostics](monitor-logic-apps-log-analytics.md), vous pouvez obtenir des informations sur tous les événements de limitation qui se produisent. |
   | **Réussi** | L’exécution a réussi. Si une action a échoué, une action subséquente dans le flux de travail a géré cet échec. |
   | **Délai dépassé** | L’exécution a expiré parce qu’elle a dépassé la limite de durée d’exécution qui est contrôlée par le [paramètre **Run history retention in days** (rétention de l’historique d’exécution en jours)](logic-apps-limits-and-config.md#run-duration-retention-limits). La durée d’une exécution est calculée en utilisant l’heure de début et la limite de durée d’exécution à cette heure. <p><p>**Remarque** : Si la durée de l’exécution dépasse aussi la *limite actuelle de rétention de l’historique d’exécution*, qui est également contrôlée par le paramètre [**Run history retention in days** (Rétention de l’historique d’exécution en jours)](logic-apps-limits-and-config.md#run-duration-retention-limits), l’exécution est effacée de l’historique d’exécution par un travail de nettoyage quotidien. Que l’exécution expire ou se termine, la période de rétention est toujours calculée sur la base de l’heure de début de l’exécution et de la limite de rétention *actuelle*. Par conséquent, si vous réduisez la limite de durée pour une exécution en cours, l’exécution expire. Toutefois, l’exécution reste dans l’historique des exécutions ou est effacée de celui-ci selon que la durée de l’exécution a ou non dépassé la limite de rétention. |
   | **En attente** | L’exécution n’a pas démarré ou est suspendue, par exemple, en raison d’une instance de flux de travail antérieure qui est toujours en cours d’exécution. |
   |||

1. Pour examiner les états de chaque étape d’une exécution spécifique, ainsi que les entrées et sorties de l’étape, sélectionnez le bouton ( **...** ) correspondant à cette exécution, puis sélectionnez **Afficher l’exécution**.

   ![Capture d’écran montrant la ligne de l’historique d’exécution de votre flux de travail avec le bouton (...) et l’option « Afficher l’exécution » sélectionnée.](./media/create-single-tenant-workflows-visual-studio-code/show-run-history.png)

   Visual Studio Code ouvre la vue d’analyse et affiche l’état de chaque étape de l’exécution.

   ![Capture d’écran montrant chaque étape de l’exécution du flux de travail et son état](./media/create-single-tenant-workflows-visual-studio-code/run-history-action-status.png)

   > [!NOTE]
   > Si une exécution a échoué et qu’une étape de l’affichage d’analyse affiche l’erreur `400 Bad Request`, ce problème peut provenir d’un nom de déclencheur ou d’un nom d’action plus long qui entraîne le dépassement de la limite de caractères par défaut pour la valeur Uniform Resource Identifier (URI) sous-jacente. Pour plus d’informations, consultez [« 400 Demande incorrecte »](#400-bad-request).

   Voici les états possibles que chaque étape du flux de travail :

   | État d’action | Description |
   |---------------|-------------|
   | **Abandonné** | L’action a été arrêtée ou ne s’est pas terminée en raison de problèmes externes, par exemple, une panne du système ou l’expiration d’un abonnement Azure. |
   | **Annulé** | L’action était en cours d’exécution mais a reçu une demande d’annulation. |
   | **Échec** | L’action a échoué. |
   | **Exécution** | L’action est en cours d’exécution. |
   | **Ignoré** | L’action a été ignorée parce qu’elle précède immédiatement l’action qui a échoué. Une action a une condition `runAfter` qui exige que l’action précédente se termine correctement avant que l’action en cours puisse s’exécuter. |
   | **Réussi** | L’action a réussi. |
   | **Réussie avec de nouvelles tentatives** | L’action a réussi, mais uniquement après une ou plusieurs tentatives. Pour consulter l’historique des tentatives, dans la vue des détails de l’historique des exécutions, sélectionnez cette action afin de voir les entrées et sorties. |
   | **Délai dépassé** | L’action a été arrêtée en raison du délai d’expiration spécifié par les paramètres de cette action. |
   | **En attente** | S’applique à une action de webhook qui attend une demande entrante d’un appelant. |
   |||

   [aborted-icon]: ./media/create-single-tenant-workflows-visual-studio-code/aborted.png
   [cancelled-icon]: ./media/create-single-tenant-workflows-visual-studio-code/cancelled.png
   [failed-icon]: ./media/create-single-tenant-workflows-visual-studio-code/failed.png
   [running-icon]: ./media/create-single-tenant-workflows-visual-studio-code/running.png
   [skipped-icon]: ./media/create-single-tenant-workflows-visual-studio-code/skipped.png
   [succeeded-icon]: ./media/create-single-tenant-workflows-visual-studio-code/succeeded.png
   [succeeded-with-retries-icon]: ./media/create-single-tenant-workflows-visual-studio-code/succeeded-with-retries.png
   [timed-out-icon]: ./media/create-single-tenant-workflows-visual-studio-code/timed-out.png
   [waiting-icon]: ./media/create-single-tenant-workflows-visual-studio-code/waiting.png

1. Pour afficher les entrées et sorties de chaque étape, développez celle que vous souhaitez inspecter.

   ![Capture d’écran montrant l’état de chaque étape du flux de travail, ainsi que les entrées et sorties de l’action « Envoyer un e-mail » développée.](./media/create-single-tenant-workflows-visual-studio-code/run-history-details.png)

1. Pour examiner plus en détail les entrées et sorties brutes de cette étape, sélectionnez **Afficher les entrées brutes** ou **Afficher les sorties brutes**.

1. Pour arrêter la session de débogage, dans le menu **Exécuter**, sélectionnez **Arrêter le débogage** (Maj + F5).

<a name="return-response"></a>

## <a name="return-a-response"></a>Retourner une réponse

Pour retourner une réponse à l’appelant ayant envoyé une demande à votre application logique, vous pouvez utiliser l’[action Réponse](../connectors/connectors-native-reqres.md) intégrée pour un workflow qui démarre avec le déclencheur Requête.

1. Dans le concepteur de workflow, sous l’action **Envoyer un e-mail**, sélectionnez le signe plus ( **+** ) > **Ajouter une action**.

   L’invite **Choisir une opération** s’affiche sur le concepteur, et le volet **Ajouter une action** se rouvre pour vous permettre de sélectionner l’action suivante.

1. Dans le volet **Ajouter une action**, dans la zone de recherche **Choisir une action**, assurez-vous que l’option **Intégrée** est sélectionnée. Dans la zone de recherche, entrez `response`, puis sélectionnez l’action **Réponse**.

   ![Capture d’écran montrant le concepteur de workflow avec l’action Réponse sélectionnée.](./media/create-single-tenant-workflows-visual-studio-code/add-response-action.png)

   Lorsque l’action **Réponse** s’affiche dans le concepteur, le volet d’informations de l’action s’ouvre automatiquement.

   ![Capture d’écran montrant le concepteur de workflow avec le volet d’informations de l’action « Réponse » ouvert et la propriété « Corps » définie sur la valeur de la propriété « Corps » de l’action « Envoyer un e-mail ».](./media/create-single-tenant-workflows-visual-studio-code/response-action-details.png)

1. Sous l’onglet **Paramètres**, entrez les informations requises pour la fonction que vous souhaitez appeler.

   Cet exemple retourne la valeur de la propriété **Corps** qui est la sortie de l’action **Envoyer un e-mail**.

   1. Cliquez à l’intérieur de la zone de propriété **Corps** afin que la liste de contenu dynamique s’affiche et indique les valeurs de sortie disponibles à partir des actions et du déclencheur précédents dans le flux de travail.

      ![Capture d’écran montrant le volet d’informations de l’action « Réponse » avec le pointeur de la souris à l’intérieur de la propriété « Corps » afin que la liste de contenu dynamique s’affiche.](./media/create-single-tenant-workflows-visual-studio-code/open-dynamic-content-list.png)

   1. Dans la liste contenu dynamique, sous **Envoyer un e-mail**, sélectionnez **Corps**.

      ![Capture d’écran montrant la liste de contenu dynamique ouverte. Dans la liste, sous l’en-tête « Envoyer un e-mail », la valeur de sortie « Corps » est sélectionnée.](./media/create-single-tenant-workflows-visual-studio-code/select-send-email-action-body-output-value.png)

      Lorsque vous avez terminé, la propriété **Corps** de l’action Réponse est désormais définie sur la valeur de sortie **Corps** de l’action **Envoyer un e-mail**.

      ![Capture d’écran montrant l’état de chaque étape du flux de travail, ainsi que les entrées et sorties de l’action « Réponse » développée.](./media/create-single-tenant-workflows-visual-studio-code/response-action-details-body-property.png)

1. Dans le concepteur, sélectionnez **Enregistrer**.

<a name="retest-workflow"></a>

## <a name="retest-your-logic-app"></a>Tester votre application logique

Une fois que vous avez mis à jour votre application logique, vous pouvez effectuer un autre test en réexécutant le débogueur dans Visual Studio et en envoyant une autre requête pour déclencher votre application logique mise à jour, comme décrit dans [Exécuter, tester et déboguer localement](#run-test-debug-locally).

1. Dans la barre d’activités de Visual Studio Code, ouvrez le menu **Exécuter**, puis sélectionnez **Démarrer le débogage** (F5).

1. Dans Postman ou dans votre outil de création et d’envoi de demandes, envoyez une autre demande pour déclencher votre flux de travail.

1. Si vous avez créé un flux de travail avec état, sur la page de présentation du flux de travail, vérifiez l’état de la dernière exécution. Pour afficher l’état, les entrées et les sorties de chaque étape de cette exécution, sélectionnez le bouton ( **...** ) correspondant à cette exécution, puis sélectionnez **Afficher l’exécution**.

   Par exemple, voici l’état pas à pas d’une exécution une fois que l’exemple de flux de travail a été mis à jour avec l’action Réponse.

   ![Capture d’écran montrant l’état de chaque étape du flux de travail mis à jour, ainsi que les entrées et sorties de l’action « Réponse » développée.](./media/create-single-tenant-workflows-visual-studio-code/run-history-details-rerun.png)

1. Pour arrêter la session de débogage, dans le menu **Exécuter**, sélectionnez **Arrêter le débogage** (Maj + F5).

<a name="firewall-setup"></a>

## <a name="find-domain-names-for-firewall-access"></a>Rechercher les noms de domaine pour l’accès au pare-feu

Avant de déployer et d’exécuter le workflow de votre application logique dans le portail Azure, si votre environnement présente des exigences réseau strictes ou des pare-feu qui limitent le trafic, vous devez définir des autorisations pour les connexions de déclencheur ou d’action présentes dans votre workflow.

Pour rechercher les noms de domaine complets (FQDN) de ces connexions, procédez comme suit :

1. Dans votre projet d’application logique, ouvrez le fichier **connections.json** créé après avoir ajouté le premier déclencheur ou action basé sur une connexion à votre workflow, et recherchez l'objet `managedApiConnections`.

1. Pour chaque connexion que vous avez créée, copiez et enregistrez la valeur de la propriété `connectionRuntimeUrl` dans un lieu sûr afin de configurer votre pare-feu à l’aide de ces informations.

   Cet exemple de fichier **connections.json** contient deux connexions, une connexion AS2 et une connexion Office 365 avec les valeurs `connectionRuntimeUrl` suivantes :

   * AS2 : `"connectionRuntimeUrl": https://9d51d1ffc9f77572.00.common.logic-{Azure-region}.azure-apihub.net/apim/as2/11d3fec26c87435a80737460c85f42ba`

   * Office 365 : `"connectionRuntimeUrl": https://9d51d1ffc9f77572.00.common.logic-{Azure-region}.azure-apihub.net/apim/office365/668073340efe481192096ac27e7d467f`

   ```json
   {
      "managedApiConnections": {
         "as2": {
            "api": {
               "id": "/subscriptions/{Azure-subscription-ID}/providers/Microsoft.Web/locations/{Azure-region}/managedApis/as2"
            },
            "connection": {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Web/connections/{connection-resource-name}"
            },
            "connectionRuntimeUrl": https://9d51d1ffc9f77572.00.common.logic-{Azure-region}.azure-apihub.net/apim/as2/11d3fec26c87435a80737460c85f42ba,
            "authentication": {
               "type":"ManagedServiceIdentity"
            }
         },
         "office365": {
            "api": {
               "id": "/subscriptions/{Azure-subscription-ID}/providers/Microsoft.Web/locations/{Azure-region}/managedApis/office365"
            },
            "connection": {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Web/connections/{connection-resource-name}"
            },
            "connectionRuntimeUrl": https://9d51d1ffc9f77572.00.common.logic-{Azure-region}.azure-apihub.net/apim/office365/668073340efe481192096ac27e7d467f,
            "authentication": {
               "type":"ManagedServiceIdentity"
            }
         }
      }
   }
   ```

<a name="deploy-azure"></a>

## <a name="deploy-to-azure"></a>Déployer dans Azure

À partir de Visual Studio Code, vous pouvez publier directement votre projet sur Azure, ce qui a pour effet de déployer votre application logique à l’aide du type de ressource **Application logique (standard)** . Vous pouvez publier votre application logique en tant que nouvelle ressource, ce qui a pour effet de créer automatiquement toutes les ressources nécessaires, par exemple, un [compte de stockage Azure, similaire à la configuration requise des applications de fonction](../azure-functions/storage-considerations.md). Vous pouvez également publier votre application logique sur une ressource **Application logique (standard)** précédemment déployée, ce qui remplace cette application logique.

Le déploiement du type de ressource **Application logique (standard)** requiert un plan d’hébergement et un niveau tarifaire que vous sélectionnez pendant le déploiement. Pour plus d’informations, consultez [Plans d’hébergement et niveaux tarifaires](logic-apps-pricing.md#standard-pricing).

<a name="publish-new-logic-app"></a>

### <a name="publish-to-a-new-logic-app-standard-resource"></a>Publier dans une nouvelle ressource Application logique (standard)

1. Dans la barre d’activités de Visual Studio Code, sélectionnez l’icône Azure.

1. Dans la barre d’outils du volet **Azure : Logic Apps (standard)** , sélectionnez **Déployer sur Logic Apps**.

   ![Capture d’écran montrant le volet « Azure : Logic Apps (standard) » et la barre d’outils du volet avec l’option « Déployer sur Logic Apps » sélectionnée.](./media/create-single-tenant-workflows-visual-studio-code/deploy-to-logic-app.png)

1. En cas d’invite, sélectionnez l’abonnement Azure à utiliser pour votre déploiement d’application logique.

1. Dans la liste qu’ouvre Visual Studio Code, sélectionnez l’une des options suivantes :

   * **Créer une application logique (standard) dans Azure** (rapide)
   * **Créer une application logique (standard) dans Advanced Azure**
   * Une ressource **Application logique (standard)** précédemment déployée éventuelle

   Nous poursuivons cet exemple avec l’option **Créer une ressource Application logique (standard) dans Azure Advanced**.

   ![Capture d’écran montrant le volet « Azure : Logic Apps (standard) » avec une liste dans laquelle l’option « Créer une ressource Application logique (standard) dans Azure » est sélectionnée.](./media/create-single-tenant-workflows-visual-studio-code/select-create-logic-app-options.png)

1. Pour créer votre ressource **Application logique (standard)** , procédez comme suit :

   1. Fournissez un nom global unique pour votre nouvelle application logique, qui est le nom à utiliser pour la ressource **Application logique (standard)** . Cet exemple utilise `Fabrikam-Workflows-App`.

      ![Capture d’écran montrant le volet « Azure : Logic Apps (standard) » et une invite à fournir un nom pour la nouvelle application logique à créer.](./media/create-single-tenant-workflows-visual-studio-code/enter-logic-app-name.png)

   1. Sélectionnez un plan d’hébergement pour votre nouvelle application logique. Créez un nom pour votre plan ou sélectionnez un plan existant. Cet exemple sélectionne **Créer un plan App Service**.

      ![Capture d’écran montrant le volet « Azure : Logic Apps (standard) » et une invite à « Créer un Plan App Service » ou à sélectionner un Plan App Service existant.](./media/create-single-tenant-workflows-visual-studio-code/create-app-service-plan.png)

   1. Entrez un nom pour votre plan d’hébergement, puis sélectionnez un niveau tarifaire pour votre plan sélectionné.

      Pour plus d’informations, consultez [Plans d’hébergement et niveaux tarifaires](logic-apps-pricing.md#standard-pricing).

   1. Pour des performances optimales, sélectionnez le même groupe de ressources que votre projet pour le déploiement.

      > [!NOTE]
      > Bien que vous puissiez créer ou utiliser un groupe de ressources différent, cela peut affecter les performances. Si vous créez ou choisissez un groupe de ressources différent, mais annulez l’opération après l’affichage de l’invite de confirmation, votre déploiement est également annulé.

   1. Pour les flux de travail avec état, sélectionnez **Créer un compte de stockage** ou choisissez un compte de stockage existant.

      ![Capture d’écran montrant le volet « Azure : Logic Apps (standard) » et une invite à créer ou sélectionner un compte de stockage.](./media/create-single-tenant-workflows-visual-studio-code/create-storage-account.png)

   1. Si les paramètres de création et de déploiement de votre application logique prennent en charge l’utilisation d’[Application Insights](../azure-monitor/app/app-insights-overview.md), vous pouvez activer en option la journalisation et le suivi des diagnostics pour votre application logique. Vous pouvez le faire au moment de déployer votre application logique à partir de Visual Studio Code ou après le déploiement. Vous devez disposer d’une instance d’Application Insights, mais vous pouvez créer cette ressource [à l’avance](../azure-monitor/app/create-workspace-resource.md), lorsque vous déployez votre application logique, ou après le déploiement.

      Pour activer la journalisation et le suivi maintenant, procédez comme suit :

      1. Sélectionnez une ressource Application Insights existante ou **créez une ressource Application Insights**.

      1. Dans le [portail Azure](https://portal.azure.com), accédez à la ressource Application Insights.

      1. Dans le menu des ressources, sélectionnez **Vue d’ensemble**. Recherchez et copiez la valeur de **Clé d’instrumentation**.

      1. Dans Visual Studio Code, dans le dossier racine de votre projet, ouvrez le fichier **local.settings.json**.

      1. Dans l’objet `Values`, ajoutez la propriété `APPINSIGHTS_INSTRUMENTATIONKEY` et définissez la valeur sur la clé d’instrumentation, par exemple :

         ```json
         {
            "IsEncrypted": false,
            "Values": {
               "AzureWebJobsStorage": "UseDevelopmentStorage=true",
               "FUNCTIONS_WORKER_RUNTIME": "dotnet",
               "APPINSIGHTS_INSTRUMENTATIONKEY": <instrumentation-key>
            }
         }
         ```

         > [!TIP]
         > Vous pouvez vérifier si les noms des déclencheurs et des actions s’affichent correctement dans votre instance d’Application Insights.
         >
         > 1. Dans le portail Azure, accédez à la ressource Application Insights.
         >
         > 2. Dans le menu des ressources, sous **Examiner**, sélectionnez **Cartographie d’application**.
         >
         > 3. Examinez les noms des opérations qui apparaissent dans la cartographie.
         >
         > Certaines demandes entrantes provenant de déclencheurs intégrés peuvent apparaître en doublons dans la cartographie d’application. 
         > Au lieu d’utiliser le format `WorkflowName.ActionName`, ces doublons utilisent le nom du workflow comme nom d’opération et proviennent de l’hôte Azure Functions.

      1. Ensuite, si vous le souhaitez, vous pouvez ajuster le niveau de gravité des données de suivi collectées et envoyées par votre application logique à votre instance d’Application Insights.

         Chaque fois qu’un événement relatif au workflow se produit, par exemple lorsqu’un workflow est déclenché ou qu’une action est exécutée, le runtime émet plusieurs traces. Ces traces couvrent la durée de vie du workflow et incluent notamment les types d’événements suivants :

         * Activité relative au service, telle que le démarrage, l’arrêt et les erreurs.
         * Activité aux travaux et au répartiteur.
         * Activité relative au workflow, telle qu’un déclencheur, une action et une exécution.
         * Activité relative à une demande de stockage, comme la réussite ou l’échec.
         * Activité relative à une requête HTTP, par exemple : entrante, sortante, réussite et échec.
         * Toutes les traces de développement, comme les messages de débogage.

         Un niveau de gravité est attribué à chaque type d’événement. Par exemple, le niveau `Trace` capture les messages les plus détaillés, tandis que le niveau `Information` capture l’activité générale dans votre workflow, par exemple lorsque une application logique, un workflow, un déclencheur et des actions démarrent et s’arrêtent. Ce tableau décrit les niveaux de gravité et leurs types de trace :

         | Niveau de gravité | Type de trace |
         |----------------|------------|
         | Critique | Journaux qui décrivent un échec irrécupérable dans votre application logique. |
         | Débogage | Journaux que vous pouvez utiliser pour examen pendant le développement, par exemple les appels HTTP entrants et sortants. |
         | Error | Journaux qui indiquent un échec dans l’exécution du workflow, mais pas un échec général de votre application logique. |
         | Information | Journaux qui effectuent le suivi de l’activité générale dans votre application logique ou dans votre workflow, par exemple : <p><p>– Quand un déclencheur, une action ou une exécution démarre et se termine. <br>– Lorsque votre application logique démarre ou se termine. |
         | Trace | Journaux qui contiennent les messages les plus détaillés, par exemple, les demandes de stockage ou l’activité relative au répartiteur, ainsi que tous les messages liés à l’activité d’exécution du workflow. |
         | Avertissement | Journaux qui mettent en évidence un état anormal dans votre application logique, mais qui n’empêche pas son exécution. |
         |||

         Pour définir le niveau de gravité, à la racine de votre projet, ouvrez le fichier **host.json** et recherchez l’objet `logging`. Cet objet contrôle le filtrage des journaux pour tous les workflows de votre application logique et suit le [layout d’ASP.NET Core pour le filtrage du type de journal](/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1&preserve-view=true#log-filtering).

         ```json
         {
            "version": "2.0",
            "logging": {
               "applicationInsights": {
                  "samplingExcludedTypes": "Request",
                  "samplingSettings": {
                     "isEnabled": true
                  }
               }
            }
         }
         ```

         Si l’objet `logging` ne contient pas d’objet `logLevel` incluant la propriété `Host.Triggers.Workflow`, ajoutez ces éléments. Définissez la propriété sur le niveau de gravité du type de trace que vous souhaitez, par exemple :

         ```json
         {
            "version": "2.0",
            "logging": {
               "applicationInsights": {
                  "samplingExcludedTypes": "Request",
                  "samplingSettings": {
                     "isEnabled": true
                  }
               },
               "logLevel": {
                  "Host.Triggers.Workflow": "Information"
               }
            }
         }
         ```

   Lorsque vous avez terminé les étapes de déploiement, Visual Studio Code commence à créer et à déployer les ressources nécessaires à la publication de votre application logique.

1. Pour examiner et surveiller le processus de déploiement, dans le menu **Afficher**, sélectionnez **Sortie**. Dans la liste des barres d’outils de la fenêtre Sortie, sélectionnez **Azure Logic Apps**.

   ![Capture d’écran montrant la fenêtre Sortie avec « Azure Logic Apps » sélectionné dans la liste des barres d’outils, ainsi que la progression et les états du déploiement.](./media/create-single-tenant-workflows-visual-studio-code/logic-app-deployment-output-window.png)

   Lorsque Visual Studio Code termine le déploiement de votre application logique sur Azure, le message suivant s’affiche :

   ![Capture d’écran montrant un message indiquant que le déploiement sur Azure s’est terminé correctement.](./media/create-single-tenant-workflows-visual-studio-code/deployment-to-azure-completed.png)

   Félicitations, votre application logique est maintenant active dans Azure et activée par défaut.

Ensuite, vous pouvez apprendre à effectuer ces tâches :

* [Ajouter un workflow vide à votre projet](#add-workflow-existing-project).

* [Gérer les applications logiques déployées dans Visual Studio Code](#manage-deployed-apps-vs-code) ou en utilisant le [portail Azure](#manage-deployed-apps-portal).

* [Activer l’historique des exécutions sur les workflows sans état](#enable-run-history-stateless).

* [Activer l’affichage Analyse dans le portail Azure pour une application logique déployée](#enable-monitoring).

<a name="add-workflow-existing-project"></a>

## <a name="add-blank-workflow-to-project"></a>Ajouter un workflow vide à un projet

Vous pouvez avoir plusieurs workflows dans votre projet d’application logique. Pour ajouter un workflow vide à votre projet, procédez comme suit :

1. Dans la barre d’activités de Visual Studio Code, sélectionnez l’icône Azure.

1. Dans le volet Azure, en regard de **Azure : Logic Apps (standard)** , sélectionnez **Créer un workflow** (icône pour Azure Logic Apps).

1. Sélectionnez le type de workflow que vous voulez ajouter : **Avec état** ou **Sans état**

1. Spécifiez un nom pour votre workflow.

Lorsque vous avez terminé, un nouveau dossier de workflow s’affiche dans votre projet avec un fichier **workflow.json** pour la définition de workflow.

<a name="manage-deployed-apps-vs-code"></a>

## <a name="manage-deployed-logic-apps-in-visual-studio-code"></a>Gérer les applications logiques déployées dans Visual Studio Code

Dans Visual Studio Code, vous pouvez afficher toutes les applications logiques déployées dans votre abonnement Azure, qu’il s’agisse du type de ressource **Logic Apps** d’origine ou **Application logique (standard)** , puis sélectionnez les tâches qui vous aident à gérer ces applications logiques. Toutefois, pour accéder aux deux types de ressources, vous avez besoin des extensions **Azure Logic Apps** et **Azure Logic Apps (standard)** pour Visual Studio Code.

1. Dans la barre d’outils de gauche, sélectionnez l’icône Azure. Dans le volet **Azure : Logic Apps (standard)** , développez votre abonnement qui affiche toutes les applications logiques déployées pour cet abonnement.

1. Ouvrez l’application logique à gérer. À partir du menu contextuel de l’application logique, sélectionnez la tâche à effectuer.

   Par exemple, vous pouvez sélectionner des tâches telles que l’arrêt, le démarrage, le redémarrage ou la suppression de votre application logique déployée. Vous pouvez [désactiver ou activer un workflow à partir du portail Azure](create-single-tenant-workflows-azure-portal.md#disable-enable-workflows).

   > [!NOTE]
   > Les opérations d’arrêt et de suppression d’application logique affectent les instances de workflow de différentes manières. Pour plus d’informations, consultez [Considérations relatives à l’arrêt des applications logiques](#considerations-stop-logic-apps) et [Considérations relatives à la suppression des applications logiques](#considerations-delete-logic-apps).

   ![Capture d’écran montrant Visual Studio Code avec le volet d’extension « Azure Logic Apps (standard) » ouvert et le flux de travail déployé.](./media/create-single-tenant-workflows-visual-studio-code/find-deployed-workflow-visual-studio-code.png)

1. Pour afficher tous les workflows de l’application logique, développez votre application logique, puis développez le nœud **Workflows**.

1. Pour afficher un workflow spécifique, ouvrez le menu contextuel du workflow, puis sélectionnez **Ouvrir dans le concepteur**, ce qui ouvre le workflow en mode lecture seule.

   Pour modifier le workflow, vous disposez des options suivantes :

   * Dans Visual Studio Code, ouvrez le fichier **workflow.json** de votre projet dans le concepteur de workflows, apportez vos modifications, puis redéployez votre application logique dans Azure.

   * Dans le Portail Azure, [ouvrez votre application logique](#manage-deployed-apps-portal). Vous pouvez ensuite ouvrir, modifier et enregistrer votre workflow.

1. Pour ouvrir l’application logique déployée dans le portail Azure, ouvrez le menu contextuel de l’application logique, puis sélectionnez **Ouvrir dans le portail**.

   Le portail Azure s’ouvre dans votre navigateur, vous connecte automatiquement au portail si vous êtes connecté à Visual Studio Code, et affiche votre application logique.

   ![Capture d’écran montrant la page du portail Azure de votre application logique dans Visual Studio Code.](./media/create-single-tenant-workflows-visual-studio-code/deployed-workflow-azure-portal.png)

   Vous pouvez également vous connecter séparément au portail Azure, utiliser la zone de recherche du portail pour rechercher votre application logique, puis sélectionner votre application logique dans la liste des résultats.

   ![Capture d’écran montrant le portail Azure et la barre de recherche avec des résultats de recherche pour l’application logique déployée qui apparaît sélectionnée.](./media/create-single-tenant-workflows-visual-studio-code/find-deployed-workflow-azure-portal.png)

<a name="considerations-stop-logic-apps"></a>

### <a name="considerations-for-stopping-logic-apps"></a>Considérations relatives à l’arrêt des applications logiques

L’arrêt d’une application logique affecte les instances de workflow de différentes manières :

* Le service Logic Apps annule immédiatement toutes les exécutions en cours et en attente.

* Le service Logic Apps ne crée ni n’exécute de nouvelles instances de workflow.

* Les déclencheurs ne sont pas activés la prochaine fois que les conditions sont remplies. Cependant, les états des déclencheurs mémorisent les points auxquels l’application logique a été arrêtée. Ainsi, si vous redémarrez l’application logique, les déclencheurs se lancent pour tous les éléments non traités depuis la dernière exécution.

  Pour empêcher un déclencheur de se lancer pour les éléments non traités depuis la dernière exécution, effacez l’état du déclencheur avant de redémarrer l’application logique :

  1. Dans la barre d’outils gauche de Visual Studio Code, sélectionnez l’icône Azure. 
  1. Dans le volet **Azure : Logic Apps (standard)** , développez votre abonnement qui affiche toutes les applications logiques déployées pour cet abonnement.
  1. Développez votre application logique, puis le nœud nommé **Workflows**.
  1. Ouvrez un workflow, puis modifiez une partie du déclencheur de ce workflow.
  1. Enregistrez vos modifications. Cette étape réinitialise l’état actuel du déclencheur.
  1. Répétez cette opération pour chaque workflow.
  1. Quand vous avez terminé, redémarrez votre application logique.

<a name="considerations-delete-logic-apps"></a>

### <a name="considerations-for-deleting-logic-apps"></a>Considérations relatives à la suppression des applications logiques

Voici de quelles manières la suppression d’une application logique affecte les instances de workflow :

* Le service Logic Apps annule immédiatement les exécutions en cours et en attente, mais n’exécute pas de tâches de nettoyage au niveau du stockage utilisé par l’application.

* Le service Logic Apps ne crée ni n’exécute de nouvelles instances de workflow.

* Si, après avoir supprimé un workflow, vous recréez le même, les métadonnées de ce dernier sont différentes de celles du workflow supprimé. Pour actualiser les métadonnées, vous devez enregistrer de nouveau les workflows qui appelaient le workflow supprimé. L’appelant obtient ainsi les bonnes informations sur le workflow recréé. Dans le cas contraire, les appels au workflow recréé échouent avec une erreur `Unauthorized`. Ce comportement vaut également pour les workflows qui utilisent des artefacts dans des comptes d’intégration et des workflows qui appellent des fonctions Azure.

<a name="manage-deployed-apps-portal"></a>

## <a name="manage-deployed-logic-apps-in-the-portal"></a>Gérer les applications logiques déployées dans le portail

Une fois que vous avez déployé une application logique sur le portail Azure à partir de Visual Studio Code, vous pouvez afficher toutes les applications logiques déployées qui se trouvent dans votre abonnement Azure, qu’il s’agisse du type de ressource **Logic Apps** d’origine ou du type de ressource **Application logique (standard)** . Actuellement, chaque type de ressource est organisé et géré en tant que catégorie distincte dans Azure. Pour rechercher les applications logiques dont le type de ressource est **Application logique (standard)** , procédez comme suit :

1. Dans la zone de recherche du portail Azure, entrez `logic apps`. Lorsque la liste des résultats s’affiche, sous **Services**, sélectionnez **Applications logiques**.

   ![Capture d’écran montrant la zone de recherche du portail Azure contenant le texte de recherche « logic apps ».](./media/create-single-tenant-workflows-visual-studio-code/portal-find-logic-app-resource.png)

1. Dans le volet **Application logique (standard)** , sélectionnez l’application logique que vous avez déployée à partir de Visual Studio Code.

   ![Capture d’écran montrant le portail Azure et les ressources Application logique (standard) déployées dans Azure.](./media/create-single-tenant-workflows-visual-studio-code/logic-app-resources-pane.png)

   Le portail Azure ouvre la page de ressource individuelles pour l’application logique sélectionnée.

   ![Capture d’écran montrant la page de ressources du flux de travail de votre application logique dans le portail Azure.](./media/create-single-tenant-workflows-visual-studio-code/deployed-workflow-azure-portal.png)

1. Pour afficher les flux de travail de cette application logique, dans le menu de celle-ci, sélectionnez **Flux de travail**.

   Le volet **Flux de travail** affiche tous les flux de travail de l’application logique actuelle. Cet exemple montre le flux de travail que vous avez créé dans Visual Studio Code.

   ![Capture d’écran montrant une page de ressources « Application logique (standard) » avec le volet « flux de travail » ouvert et le flux de travail déployé](./media/create-single-tenant-workflows-visual-studio-code/deployed-logic-app-workflows-pane.png)

1. Pour afficher un flux de travail, dans le volet **Flux de travail**, sélectionnez ce flux de travail.

   Le volet Flux de travail s’ouvre, affichant des informations supplémentaires, ainsi que des tâches que vous pouvez effectuer sur ce flux de travail.

   Par exemple, pour afficher les étapes du flux de travail, sélectionnez **Concepteur**.

   ![Capture d’écran montrant le volet « Vue d’ensemble » du flux de travail sélectionné, tandis que le menu du flux de travail affiche la commande « Concepteur » sélectionnée.](./media/create-single-tenant-workflows-visual-studio-code/workflow-overview-pane-select-designer.png)

   Le concepteur de workflow s’ouvre, affichant le workflow que vous avez créé dans Visual Studio Code. Vous pouvez maintenant apporter des modifications à ce flux de travail dans le portail Azure.

   ![Capture d’écran montrant le concepteur de workflow et le workflow déployé à partir de Visual Studio Code.](./media/create-single-tenant-workflows-visual-studio-code/opened-workflow-designer.png)

<a name="add-workflow-portal"></a>

## <a name="add-another-workflow-in-the-portal"></a>Ajouter un autre workflow dans le portail

Le portail Azure vous permet d’ajouter des workflows vides à une ressource **Application logique (standard)** que vous avez déployée à partir de Visual Studio Code et de créer ces flux de travail.

1. Dans le [portail Azure](https://portal.azure.com), sélectionnez votre ressource **Application logique (standard)** déployée.

1. Dans le menu de l’application logique, sélectionnez **Workflows**. Dans le volet **Flux de travail**, sélectionnez **Ajouter**.

   ![Capture d’écran montrant le volet et la barre d’outils « Flux de travail » de l’application logique choisie avec la commande « Ajouter » sélectionnée.](./media/create-single-tenant-workflows-visual-studio-code/add-new-workflow.png)

1. Dans le volet **Nouveau flux de travail**, entrez le nom du flux de travail. Sélectionnez **Avec état** ou **Sans état** **>** **Créer**.

   Une fois qu’Azure a déployé votre nouveau workflow, qui apparaît dans le volet **Workflows**, sélectionnez ce workflow afin de pouvoir le gérer et effectuer d’autres tâches telles que l’ouverture du concepteur ou du mode code.

   ![Capture d’écran montrant le flux de travail sélectionné avec les options de gestion et de révision.](./media/create-single-tenant-workflows-visual-studio-code/view-new-workflow.png)

   Par exemple, l’ouverture du concepteur pour un nouveau flux de travail a pour effet d’afficher un canevas vide. Vous pouvez maintenant générer ce flux de travail dans le portail Azure.

   ![Capture d’écran montrant le concepteur de workflow et un workflow vide.](./media/create-single-tenant-workflows-visual-studio-code/opened-blank-workflow-designer.png)

<a name="enable-run-history-stateless"></a>

## <a name="enable-run-history-for-stateless-workflows"></a>Activer l’historique des exécutions pour les workflows sans état

Pour déboguer plus facilement un workflow sans état, vous pouvez activer l’historique des exécutions du workflow en question, puis le désactiver lorsque vous avez terminé. Procédez comme suit pour Visual Studio Code ou, si vous travaillez dans le portail Azure, consultez [Créer des workflows monolocataires dans le portail Azure](create-single-tenant-workflows-azure-portal.md#enable-run-history-stateless).

1. Dans votre projet Visual Studio Code, développez le dossier nommé **workflow-designtime**, puis ouvrez le fichier **local.settings.json**.

1. Ajoutez la propriété `Workflows.{yourWorkflowName}.operationOptions` et définissez la valeur sur `WithStatelessRunHistory`, par exemple :

   **Windows**

   ```json
   {
      "IsEncrypted": false,
      "Values": {
         "AzureWebJobsStorage": "UseDevelopmentStorage=true",
         "FUNCTIONS_WORKER_RUNTIME": "dotnet",
         "Workflows.{yourWorkflowName}.OperationOptions": "WithStatelessRunHistory"
      }
   }
   ```

   **macOS ou Linux**

   ```json
   {
      "IsEncrypted": false,
      "Values": {
         "AzureWebJobsStorage": "DefaultEndpointsProtocol=https;AccountName=fabrikamstorageacct; \
             AccountKey=<access-key>;EndpointSuffix=core.windows.net",
         "FUNCTIONS_WORKER_RUNTIME": "dotnet",
         "Workflows.{yourWorkflowName}.OperationOptions": "WithStatelessRunHistory"
      }
   }
   ```

1. Pour désactiver l’historique des exécutions lorsque vous avez terminé, supprimez la propriété `Workflows.{yourWorkflowName}.OperationOptions` et sa valeur, ou définissez-la sur `None`.

<a name="enable-monitoring"></a>

## <a name="enable-monitoring-view-in-the-azure-portal"></a>Activer l’affichage Analyse dans le portail Azure

Une fois que vous avez déployé une ressource **Application logique (standard)** de Visual Studio Code à Azure, vous pouvez consulter l’historique des exécutions et les détails disponibles pour un flux de travail de cette ressource à l’aide du portail Azure et de l’expérience **Analyse** pour ce flux de travail. Toutefois, vous devez d’abord activer la capacité d’affichage **Analyse** sur cette ressource d’application logique.

1. Dans le [portail Azure](https://portal.azure.com), sélectionnez la ressource **Application logique (standard)** déployée.

1. Dans le menu de cette ressource, sous **API**, sélectionnez **CORS**.

1. Dans le volet **CORS**, sous **Origines autorisées**, ajoutez le caractère générique (*).

1. Lorsque c’est chose faite, dans la barre d’outils **CORS**, sélectionnez **Enregistrer**.

   ![Capture d’écran montrant le portail Azure avec une ressource Application logique (standard) déployée. Dans le menu de ressources, « CORS » est sélectionné avec une nouvelle entrée pour « Origines autorisées » définie sur le caractère générique « * ».](./media/create-single-tenant-workflows-visual-studio-code/enable-run-history-deployed-logic-app.png)

<a name="enable-open-application-insights"></a>

## <a name="enable-or-open-application-insights-after-deployment"></a>Activer ou ouvrir Application Insights après le déploiement

Pendant l’exécution du workflow, votre application logique émet des données de télémétrie ainsi que d’autres événements. Vous pouvez utiliser ces données de télémétrie pour obtenir une meilleure visibilité sur l’exécution de votre workflow et sur les différentes façons dont fonctionne le runtime de Logic Apps. Vous pouvez surveiller votre workflow à l’aide d’[Application Insights](../azure-monitor/app/app-insights-overview.md), qui fournit des données de télémétrie en quasi-temps réel (métriques en temps réel). Cette capacité vous permet d’examiner plus facilement les défaillances et les problèmes de performances lorsque vous utilisez ces données pour diagnostiquer des problèmes, configurer des alertes et créer des graphiques.

Si les paramètres de création et de déploiement de votre application logique prennent en charge l’utilisation d’[Application Insights](../azure-monitor/app/app-insights-overview.md), vous pouvez activer en option la journalisation et le suivi des diagnostics pour votre application logique. Vous pouvez le faire au moment de déployer votre application logique à partir de Visual Studio Code ou après le déploiement. Vous devez disposer d’une instance d’Application Insights, mais vous pouvez créer cette ressource [à l’avance](../azure-monitor/app/create-workspace-resource.md), lorsque vous déployez votre application logique, ou après le déploiement.

Pour activer Application Insights sur une application logique déployée ou pour passer en revue les données Application Insights quand la fonctionnalité est déjà activée, procédez comme suit :

1. Dans le portail Azure, recherchez votre application logique déployée.

1. Dans le menu de l’application logique, sous **Paramètres**, sélectionnez **Application Insights**.

1. Si la fonctionnalité Application Insights n’est pas activée, dans le volet **Application Insights**, sélectionnez **Activer Application Insights**. Une fois le volet mis à jour, dans la partie inférieure, sélectionnez **Appliquer**.

   Si la fonctionnalité Application Insights est activée, dans le volet **Application Insights**, sélectionnez **Afficher les données Application Insights**.

Après l’ouverture d’Application Insights, vous pouvez passer en revue les différentes métriques de votre application logique. Pour plus d’informations, consultez les rubriques suivantes :

* [Azure Logic Apps s’exécutant partout – Superviser avec Application Insights (première partie)](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-monitor-with-application/ba-p/1877849)
* [Azure Logic Apps s’exécutant partout – Superviser avec Application Insights (deuxième partie)](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-monitor-with-application/ba-p/2003332)

<a name="delete-from-designer"></a>

## <a name="delete-items-from-the-designer"></a>Supprimer des éléments du concepteur

Pour supprimer un élément de votre workflow à partir du concepteur, procédez de l’une des façons suivantes :

* Sélectionnez l’élément, ouvrez le menu contextuel de l’élément (Maj+F10), puis sélectionnez **Supprimer**. Pour confirmer, sélectionnez **OK**.

* Sélectionnez l’élément, puis appuyez sur la touche Suppr. Pour confirmer, sélectionnez **OK**.

* Sélectionnez l’élément afin que le volet d’informations le concernant s’ouvre. Dans l’angle supérieur droit du volet, ouvrez le menu de sélection ( **…** ), puis sélectionnez **Supprimer**. Pour confirmer, sélectionnez **OK**.

  ![Capture d’écran montrant un élément sélectionné dans le concepteur avec le volet d’informations ouvert, ainsi que le bouton de sélection et la commande « Supprimer » sélectionnés.](./media/create-single-tenant-workflows-visual-studio-code/delete-item-from-designer.png)

  > [!TIP]
  > Si le menu de sélection n’est pas visible, développez la fenêtre de Visual Studio Code suffisamment pour que le volet d’informations affiche le bouton de sélection ( **…** ) dans le coin supérieur droit.

<a name="troubleshooting"></a>

## <a name="troubleshoot-errors-and-problems"></a>Résoudre les erreurs et les problèmes

<a name="designer-fails-to-open"></a>

### <a name="designer-fails-to-open"></a>Échec de l’ouverture du concepteur

Quand vous essayez d’ouvrir le concepteur, vous recevez cette erreur : **« Impossible de démarrer le design-time du workflow »** . Si vous avez auparavant essayé d’ouvrir le concepteur, puis interrompu ou supprimé votre projet, le pack de l’extension ne se télécharge peut-être pas correctement. Pour vérifier si cette cause explique le problème, effectuez ces étapes :

  1. Dans Visual Studio Code, ouvrez la fenêtre Sortie. Dans le menu **Affichage**, sélectionnez **Sortie**.

  1. Dans la liste de la barre de titre de la fenêtre Sortie, sélectionnez **Azure Logic Apps (standard)** pour pouvoir consulter la sortie de l’extension, par exemple :

     ![Capture d’écran montrant la fenêtre Sortie avec l’option « Azure Logic Apps » sélectionnée.](./media/create-single-tenant-workflows-visual-studio-code/check-output-window-azure-logic-apps.png)

  1. Examinez la sortie et vérifiez si ce message d’erreur s’affiche :

     ```text
     A host error has occurred during startup operation '{operationID}'.
     System.Private.CoreLib: The file 'C:\Users\{userName}\AppData\Local\Temp\Functions\
     ExtensionBundles\Microsoft.Azure.Functions.ExtensionBundle.Workflows\1.1.7\bin\
     DurableTask.AzureStorage.dll' already exists.
     Value cannot be null. (Parameter 'provider')
     Application is shutting down...
     Initialization cancellation requested by runtime.
     Stopping host...
     Host shutdown completed.
     ```

   Pour résoudre cette erreur, supprimez le dossier **ExtensionBundles** à cet emplacement **...\Users\{votre-nom-utilisateur}\AppData\Local\Temp\Functions\ExtensionBundles**, puis réessayez d’ouvrir le fichier **workflow.json** dans le concepteur.

<a name="missing-triggers-actions"></a>

### <a name="new-triggers-and-actions-are-missing-from-the-designer-picker-for-previously-created-workflows"></a>De nouveaux déclencheurs et actions sont absents du sélecteur de concepteur pour les workflows créés précédemment

Le service Azure Logic Apps monolocataire prend en charge les actions intégrées pour Azure Function Operations, Liquid Operations et XML Operations, telles que **Validation XML** et **Transformer au format XML**. Toutefois, pour les applications logiques créées précédemment, ces actions peuvent ne pas apparaître dans le sélecteur de concepteur pour vous indiquer si Visual Studio Code utilise une version obsolète du pack d’extension, `Microsoft.Azure.Functions.ExtensionBundle.Workflows`.

En outre, le connecteur **Azure Function Operations** et les actions n’apparaissent pas dans le sélecteur de concepteur, sauf si vous avez activé ou sélectionné **Utiliser les connecteurs d’Azure** quand vous avez créé votre application logique. Si vous n’avez pas activé les connecteurs déployés par Azure au moment de la création de l’application, vous pouvez les activer à partir de votre projet dans Visual Studio Code. Ouvrez le menu contextuel **workflow.json**, puis sélectionnez **Utiliser les connecteurs d’Azure**.

Pour corriger le pack obsolète, supprimez la version obsolète en procédant comme suit, ce qui fait que Visual Studio Code met automatiquement à jour le pack d’extension vers la version la plus récente.

> [!NOTE]
> Cette solution s’applique uniquement aux applications logiques que vous créez et déployez à l’aide de Visual Studio Code avec l’extension Azure Logic Apps (standard), et non aux applications logiques que vous avez créées à l’aide du portail Azure. Voir [Des déclencheurs et actions pris en charge sont absents du concepteur dans le portail Azure](create-single-tenant-workflows-azure-portal.md#missing-triggers-actions).

1. Enregistrez tout travail que vous ne souhaitez pas perdre et fermez Visual Studio.

1. Sur votre ordinateur, accédez au dossier suivant, qui contient les dossiers de version du pack existant :

   `...\Users\{your-username}\.azure-functions-core-tools\Functions\ExtensionBundles\Microsoft.Azure.Functions.ExtensionBundle.Workflows`

1. Supprimez le dossier de version du pack précédent. Par exemple, si vous avez un dossier pour la version 1.1.3, supprimez-le.

1. À présent, accédez au dossier suivant, qui contient les dossiers de version du package NuGet requis :

   `...\Users\{your-username}\.nuget\packages\microsoft.azure.workflows.webjobs.extension`

1. Supprimez le dossier de version pour le package existant.

1. Rouvrez Visual Studio Code, votre projet et le fichier **workflow.json** dans le concepteur.

Les déclencheurs et les actions manquants s’affichent désormais dans le concepteur.

<a name="400-bad-request"></a>

### <a name="400-bad-request-appears-on-a-trigger-or-action"></a>« 400 Demande incorrecte » apparaît sur un déclencheur ou une action

Lorsqu’une exécution échoue et que vous l’examinez dans l’affichage d’analyse, cette erreur peut apparaître avec un déclencheur ou une action dont le nom est plus long, ce qui entraîne le dépassement de la limite de caractères par défaut de la valeur Uniform Resource Identifier (URI) sous-jacente.

Pour résoudre ce problème et utiliser l’URI plus long, modifiez les clés de Registre `UrlSegmentMaxCount` et `UrlSegmentMaxLength` sur votre ordinateur en suivant les étapes ci-dessous. Les valeurs par défaut de cette clé sont décrites dans cette rubrique, [Paramètres de Registre Http.sys pour Windows](/troubleshoot/iis/httpsys-registry-windows).

> [!IMPORTANT]
> Avant de commencer, vérifiez que vous avez enregistré votre travail. Cette solution nécessite de redémarrer votre ordinateur une fois que vous avez terminé afin d’appliquer les modifications.

1. Sur votre ordinateur, ouvrez la fenêtre **Exécuter**, puis exécutez la commande `regedit`, qui ouvre l’éditeur du Registre.

1. Dans la zone **Contrôle de compte d'utilisateur**, sélectionnez **Oui** pour autoriser les modifications sur votre ordinateur.

1. Dans le volet gauche, sous **Ordinateur**, développez les nœuds du chemin d’accès, **HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\HTTP\Parameters**, puis sélectionnez **Paramètres**.

1. Dans le volet droit, recherchez les clés de Registre `UrlSegmentMaxCount` et `UrlSegmentMaxLength`.

1. Augmentez suffisamment ces valeurs de clé pour que les URI puissent prendre en charge les noms que vous souhaitez utiliser. Si ces clés n’existent pas, ajoutez-les au dossier **Parameters** en procédant comme suit :

   1. Dans le menu contextuel **Paramètres**, sélectionnez **Nouveau** > **Valeur DWORD 32 bits**.

   1. Dans la zone d’édition qui apparaît, entrez `UrlSegmentMaxCount` comme nom de la nouvelle clé.

   1. Ouvrez le menu contextuel de la nouvelle clé, puis sélectionnez **Modifier**.

   1. Dans la zone **Modification de la chaîne** qui apparaît, entrez la valeur de clé **Données de la valeur**  souhaitée au format hexadécimal ou décimal. Par exemple, `400` au format hexadécimal équivaut à `1024` au format décimal.

   1. Pour ajouter la valeur de clé `UrlSegmentMaxLength`, répétez ces étapes.

   Une fois que vous avez augmenté ou ajouté ces valeurs de clés, l’éditeur du Registre se présente comme suit :

   ![Capture d’écran montrant l’éditeur du Registre.](media/create-single-tenant-workflows-visual-studio-code/edit-registry-settings-uri-length.png)

1. Lorsque vous êtes prêt, redémarrez votre ordinateur pour appliquer les modifications.

<a name="debugging-fails-to-start"></a>

### <a name="debugging-session-fails-to-start"></a>Échec du démarrage de la session de débogage

Quand vous essayez de démarrer une session de débogage, vous recevez l’erreur : **« Une erreur existe après l’exécution de preLaunchTask 'generateDebugSymbols' »** . Pour résoudre ce problème, modifiez le fichier **tasks.json** dans votre projet pour ignorer la génération de symboles.

1. Dans votre projet, développez le dossier nommé **.vscode**, puis ouvrez le fichier **tasks.json**.

1. Dans la tâche suivante, supprimez la ligne, `"dependsOn: "generateDebugSymbols"`, ainsi que la virgule qui termine la ligne précédente, par exemple :

   Avant :

   ```json
    {
      "type": "func",
      "command": "host start",
      "problemMatcher": "$func-watch",
      "isBackground": true,
      "dependsOn": "generateDebugSymbols"
    }
   ```

   Après :

   ```json
    {
      "type": "func",
      "command": "host start",
      "problemMatcher": "$func-watch",
      "isBackground": true
    }
   ```

## <a name="next-steps"></a>Étapes suivantes

Nous aimerions que vous nous fassiez part de votre expérience avec l’extension Azure Logic Apps (standard).

* Pour tout bogue ou problème que vous rencontreriez, [créez un problème dans GitHub](https://github.com/Azure/logicapps/issues).
* Pour toute question, demande, commentaire ou retour d’expérience, utilisez de [formulaire de commentaires](https://aka.ms/lafeedback).
