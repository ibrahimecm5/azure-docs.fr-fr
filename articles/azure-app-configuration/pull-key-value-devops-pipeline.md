---
title: Extraire des paramètres d’App Configuration à l’aide d’Azure Pipelines
description: Découvrir comment utiliser Azure Pipelines pour extraire des paires clé-valeur d’un magasin App Configuration
services: azure-app-configuration
author: drewbatgit
ms.service: azure-app-configuration
ms.topic: how-to
ms.date: 11/17/2020
ms.author: drewbat
ms.openlocfilehash: 2bee0e603d3cde38e56146900d682cc5e2b40bdc
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132056470"
---
# <a name="pull-settings-to-app-configuration-with-azure-pipelines"></a>Extraire des paramètres d’App Configuration à l’aide d’Azure Pipelines

La tâche [Azure App Configuration](https://marketplace.visualstudio.com/items?itemName=AzureAppConfiguration.azure-app-configuration-task) extrait des paires clé-valeur de votre magasin App Configuration et les définit en tant que variables de pipeline Azure, lesquelles peuvent être consommées par les tâches à venir. Cette tâche complète la tâche [Azure App Configuration Push](https://marketplace.visualstudio.com/items?itemName=AzureAppConfiguration.azure-app-configuration-task-push) (Push Azure App Configuration), qui envoie les paires clé-valeur d’un fichier config vers votre magasin App Configuration. Pour plus d’informations, consultez [Envoyer des paramètres vers App Configuration avec Azure Pipelines](push-kv-devops-pipeline.md).

## <a name="prerequisites"></a>Prérequis

- Abonnement Azure : [créez-en un gratuitement](https://azure.microsoft.com/free/)
- Magasin App Configuration : créez-en un gratuitement dans le [portail Azure](https://portal.azure.com).
- Projet Azure DevOps : [créez-en un gratuitement](https://go.microsoft.com/fwlink/?LinkId=2014881)
- Tâche Azure App Configuration : téléchargez-la gratuitement à partir de [Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=AzureAppConfiguration.azure-app-configuration-task#:~:text=Navigate%20to%20the%20Tasks%20tab,the%20Azure%20App%20Configuration%20instance.). 
- [Nœud 10](https://nodejs.org/en/blog/release/v10.21.0/) : pour les utilisateurs qui exécutent la tâche sur des agents auto-hébergés. 

## <a name="create-a-service-connection"></a>Créer une connexion de service

[!INCLUDE [azure-app-configuration-service-connection](../../includes/azure-app-configuration-service-connection.md)]

## <a name="add-role-assignment"></a>Ajouter une attribution de rôle

[!INCLUDE [azure-app-configuration-role-assignment](../../includes/azure-app-configuration-role-assignment.md)]

## <a name="use-in-builds"></a>Utiliser dans les builds

Cette section explique comment utiliser la tâche Azure App Configuration dans un pipeline de build Azure DevOps.

1. Accédez à la page de pipeline de build en cliquant sur **Pipelines** > **Pipelines**. Pour accéder à la documentation sur les pipelines de build, consultez [Créer votre premier pipeline](/azure/devops/pipelines/create-first-pipeline?tabs=net%2Ctfs-2018-2%2Cbrowser).
      - Si vous créez un pipeline de build, à la dernière étape du processus, sous l’onglet **Révision**, sélectionnez **Afficher l’Assistant** sur le côté droit du pipeline.
      ![Capture d’écran montrant le bouton Afficher l’Assistant pour un nouveau pipeline.](./media/new-pipeline-show-assistant.png)
      - Si vous utilisez un pipeline de build existant, cliquez sur le bouton **Modifier** en haut à droite.
      ![Capture d’écran montrant le bouton Modifier pour un pipeline existant.](./media/existing-pipeline-show-assistant.png)
1. Recherchez la tâche **Azure App Configuration**.
![Capture d’écran montrant la boîte de dialogue Ajouter une tâche avec Azure App Configuration dans la zone de recherche.](./media/add-azure-app-configuration-task.png)
1. Configurez les paramètres nécessaires à la tâche pour extraire les paires clé-valeur du magasin App Configuration. Vous trouverez les descriptions des paramètres dans la section **Paramètres** ci-dessous ainsi que dans les info-bulles en regard de chaque paramètre.
      - Affectez au paramètre **Abonnement Azure** le nom de la connexion de service que vous avez créée à une étape précédente.
      - Affectez au paramètre **App Configuration name** (Nom App Configuration) le nom de ressource de votre magasin App Configuration.
      - Acceptez les valeurs par défaut des paramètres restants.
![Capture d’écran montrant les paramètres de la tâche Azure App Configuration.](./media/azure-app-configuration-parameters.png)
1. Enregistrez et mettez en file d’attente une build. Le journal de génération affiche les erreurs qui se sont produites pendant l’exécution de la tâche.

## <a name="use-in-releases"></a>Utiliser dans les mises en production

Cette section explique comment utiliser la tâche Azure App Configuration dans un pipeline de mise en production Azure DevOps.

1. Accédez à la page de pipeline de mise en production en sélectionnant **Pipelines** > **Mises en production**. Pour accéder à la documentation sur les pipelines de mise en production, consultez [Pipelines de mise en production](/azure/devops/pipelines/release).
1. Choisissez un pipeline de mise en production existant. Si vous n’en avez pas, cliquez sur **Nouveau pipeline** pour en créer un.
1. Sélectionnez le bouton **Modifier** dans le coin supérieur droit pour modifier le pipeline de mise en production.
1. Dans la liste déroulante **Tâches**, choisissez l’**Étape** à laquelle vous souhaitez ajouter la tâche. Vous trouverez des informations supplémentaires sur les phases [ici](/azure/devops/pipelines/release/environments).
![Capture d’écran montrant l’étape sélectionnée dans la liste déroulante Tâches.](./media/pipeline-stage-tasks.png)
1. Cliquez sur **+** en regard du travail auquel vous souhaitez ajouter une nouvelle tâche.
![Capture d’écran montrant le bouton Plus en regard du travail.](./media/add-task-to-job.png)
1. Recherchez la tâche **Azure App Configuration**.
![Capture d’écran montrant la boîte de dialogue Ajouter une tâche avec Azure App Configuration dans la zone de recherche.](./media/add-azure-app-configuration-task.png)
1. Configurez les paramètres nécessaires dans la tâche pour extraire vos paires clé-valeur du magasin App Configuration. Vous trouverez les descriptions des paramètres dans la section **Paramètres** ci-dessous ainsi que dans les info-bulles en regard de chaque paramètre.
      - Affectez au paramètre **Abonnement Azure** le nom de la connexion de service que vous avez créée à une étape précédente.
      - Affectez au paramètre **App Configuration name** (Nom App Configuration) le nom de ressource de votre magasin App Configuration.
      - Acceptez les valeurs par défaut des paramètres restants.
1. Enregistrez et mettez en file d’attente une mise en production. Le journal de mise en production affiche les erreurs rencontrées pendant l’exécution de la tâche.

## <a name="parameters"></a>Paramètres

Les paramètres suivants sont utilisés par la tâche Azure App Configuration :

- **Azure subscription** : liste déroulante contenant vos connexions de service Azure disponibles. Pour mettre à jour et actualiser votre liste de connexions de service Azure disponibles, appuyez sur le bouton **Refresh Azure subscription** (Actualiser l’abonnement Azure) à droite de la zone de texte.
- **App Configuration Name** : liste déroulante qui charge vos magasins de configuration disponibles sous l’abonnement sélectionné. Pour mettre à jour et actualiser votre liste de magasins de configuration disponibles, appuyez sur le bouton **Refresh App Configuration Name** (Actualiser le nom App Configuration) à droite de la zone de texte.
- **Filtre de clé** : Le filtre permet de sélectionner les paires clé-valeur demandées à partir d’Azure App Configuration. La valeur * permet de sélectionner toutes les paires clé-valeur. Pour plus d’informations, consultez [Interroger des paires clé-valeur](concept-key-value.md#query-key-values).
- **Étiquette** : Spécifie l’étiquette à utiliser au moment de la sélection des paires clé-valeur dans le magasin App Configuration. Si aucune étiquette n’est fournie, les paires clé-valeur sans étiquette sont récupérées. Les caractères suivants ne sont pas autorisés : « , » « * ».
- **Préfixes de clés à découper** : Spécifie un ou plusieurs préfixes de clés App Configuration à découper avant de définir ces clés en tant que variables. Vous pouvez séparer plusieurs préfixes par un caractère de nouvelle ligne.

## <a name="use-key-values-in-subsequent-tasks"></a>Utiliser des paires clé-valeur dans les tâches à venir

Les paires clé-valeur extraites d’App Configuration sont définies en tant que variables de pipeline, lesquelles sont accessibles en tant que variables d’environnement. La clé de la variable d’environnement est la clé de la paire clé-valeur récupérée à partir d’App Configuration après le découpage du préfixe, le cas échéant.

Par exemple, si une tâche à venir exécute un script PowerShell, elle peut consommer une paire clé-valeur avec la clé « myBuildSetting », comme ceci :
```powershell
echo "$env:myBuildSetting"
```
La valeur s’affiche ensuite sur la console.

> [!NOTE]
> Les références à Azure Key Vault dans App Configuration seront résolues et définies comme des [variables secrètes](/azure/devops/pipelines/process/variables#secret-variables). Dans les pipelines Azure, les variables secrètes sont masquées dans le journal. Elles ne sont pas transmises aux tâches en tant que variables d’environnement et doivent plutôt être transmises en tant qu’entrées. 

## <a name="troubleshooting"></a>Dépannage

Si une erreur inattendue se produit, les journaux de débogage peuvent être activés en affectant la valeur `true` à la variable de pipeline `system.debug`.

## <a name="faq"></a>Questions fréquentes (FAQ)

**Comment composer ma configuration à partir de plusieurs clés et étiquettes ?**

Il arrive que la configuration soit impérativement composée à partir de plusieurs étiquettes, par exemple default et dev. Vous pouvez utiliser plusieurs tâches App Configuration dans un pipeline pour implémenter ce scénario. Les paires clé-valeur extraites par une tâche au cours d’une étape à venir remplacent toutes les valeurs des étapes précédentes. Dans l’exemple ci-dessus, une tâche permet de sélectionner des paires clé-valeur avec l’étiquette default, et une deuxième tâche permet de sélectionner des paires clé-valeur avec l’étiquette dev. Les clés avec l’étiquette dev remplacent les mêmes clés avec l’étiquette default.
