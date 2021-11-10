---
title: Envoyer des paramètres vers App Configuration avec Azure Pipelines
description: Découvrez comment utiliser Azure Pipelines pour envoyer des paires clé-valeur à un magasin App Configuration.
services: azure-app-configuration
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.topic: how-to
ms.date: 02/23/2021
ms.author: alkemper
ms.openlocfilehash: 2f5ac3579489637e5adcdfaa40fac90b5ff6e8b4
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131427149"
---
# <a name="push-settings-to-app-configuration-with-azure-pipelines"></a>Envoyer des paramètres vers App Configuration avec Azure Pipelines

La tâche [Azure App Configuration Push](https://marketplace.visualstudio.com/items?itemName=AzureAppConfiguration.azure-app-configuration-task-push) envoie les paires clé-valeur d’un fichier de configuration dans votre magasin App Configuration. Cette tâche permet de bénéficier d’une fonctionnalité circulaire complète dans le pipeline, car vous pouvez désormais extraire des paramètres du magasin App Configuration et envoyer des paramètres au magasin App Configuration.

## <a name="prerequisites"></a>Prérequis

- Abonnement Azure : [créez-en un gratuitement](https://azure.microsoft.com/free/)
- Ressource App Configuration : créez-en une gratuitement dans le [portail Azure](https://portal.azure.com)
- Projet Azure DevOps : [créez-en un gratuitement](https://go.microsoft.com/fwlink/?LinkId=2014881)
- Tâche Azure App Configuration Push : téléchargez-la gratuitement à partir de [Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=AzureAppConfiguration.azure-app-configuration-task-push)
- [Nœud 10](https://nodejs.org/en/blog/release/v10.21.0/) : pour les utilisateurs qui exécutent la tâche sur des agents auto-hébergés.

## <a name="create-a-service-connection"></a>Créer une connexion de service

[!INCLUDE [azure-app-configuration-service-connection](../../includes/azure-app-configuration-service-connection.md)]

## <a name="add-role-assignment"></a>Ajouter une attribution de rôle

[!INCLUDE [azure-app-configuration-role-assignment](../../includes/azure-app-configuration-role-assignment.md)]

## <a name="use-in-builds"></a>Utiliser dans les builds

Cette section explique comment utiliser la tâche Azure App Configuration Push dans un pipeline de build Azure DevOps.

1. Accédez à la page de pipeline de build en cliquant sur **Pipelines** > **Pipelines**. Vous trouverez la documentation sur les pipelines de build [ici](/azure/devops/pipelines/create-first-pipeline?tabs=tfs-2018-2).
      - Si vous créez un pipeline de build, à la dernière étape du processus, sous l’onglet **Révision**, sélectionnez **Afficher l’Assistant** sur le côté droit du pipeline.
      ![Capture d’écran montrant le bouton Afficher l’Assistant pour un nouveau pipeline.](./media/new-pipeline-show-assistant.png)
      - Si vous utilisez un pipeline de build existant, cliquez sur le bouton **Modifier** en haut à droite.
      ![Capture d’écran montrant le bouton Modifier pour un pipeline existant.](./media/existing-pipeline-show-assistant.png)
1. Recherchez la tâche **Azure App Configuration Push**.
![Capture d’écran montrant la boîte de dialogue Ajouter une tâche avec Azure App Configuration Push dans la zone de recherche.](./media/add-azure-app-configuration-push-task.png)
1. Configurez les paramètres nécessaires pour que la tâche envoie les paires clé-valeur du fichier de configuration vers le magasin App Configuration. Vous trouverez des explications sur les paramètres dans la section **Paramètres** ci-dessous ainsi que dans les info-bulles en regard de chaque paramètre.
![Capture d’écran montrant les paramètres de la tâche Azure App Configuration Push.](./media/azure-app-configuration-push-parameters.png)
1. Enregistrez et mettez en file d’attente une build. Le journal de génération affiche les erreurs qui se sont produites pendant l’exécution de la tâche.

## <a name="use-in-releases"></a>Utiliser dans les mises en production

Cette section explique comment utiliser la tâche Azure App Configuration Push dans des pipelines de mise en production Azure DevOps.

1. Accédez à la page de pipeline de mise en production en sélectionnant **Pipelines** > **Mises en production**. La documentation sur les pipelines de mise en production est disponible [ici](/azure/devops/pipelines/release).
1. Choisissez un pipeline de mise en production existant. Si vous n’en avez pas, sélectionnez **+ Nouveau** pour en créer un.
1. Sélectionnez le bouton **Modifier** dans le coin supérieur droit pour modifier le pipeline de mise en production.
1. Dans la liste déroulante **Tâches**, choisissez l’**Étape** à laquelle vous souhaitez ajouter la tâche. Vous trouverez des informations supplémentaires sur les phases [ici](/azure/devops/pipelines/release/environments).
![Capture d’écran montrant l’étape sélectionnée dans la liste déroulante Tâches.](./media/pipeline-stage-tasks.png)
1. Cliquez sur **+** en regard du travail auquel vous souhaitez ajouter une nouvelle tâche.
![Capture d’écran montrant le bouton Plus en regard du travail.](./media/add-task-to-job.png)
1. Dans la boîte de dialogue **Ajouter des tâches**, dans la zone de recherche, tapez **Azure App Configuration Push**, puis sélectionnez cette tâche.
1. Configurez les paramètres nécessaires dans la tâche afin d’envoyer les paires clé-valeur du fichier de configuration vers votre magasin App Configuration. Vous trouverez des explications sur les paramètres dans la section **Paramètres** ci-dessous ainsi que dans les info-bulles en regard de chaque paramètre.
1. Enregistrez et mettez en file d’attente une mise en production. Le journal de mise en production affiche les erreurs rencontrées pendant l’exécution de la tâche.

## <a name="parameters"></a>Paramètres

Les paramètres suivants sont utilisés par la tâche App Configuration Push :

- **Azure subscription** : liste déroulante contenant vos connexions de service Azure disponibles. Pour mettre à jour et actualiser votre liste de connexions de service Azure disponibles, appuyez sur le bouton **Refresh Azure subscription** (Actualiser l’abonnement Azure) à droite de la zone de texte.
- **App Configuration Name** : liste déroulante qui charge vos magasins de configuration disponibles sous l’abonnement sélectionné. Pour mettre à jour et actualiser votre liste de magasins de configuration disponibles, appuyez sur le bouton **Refresh App Configuration Name** (Actualiser le nom App Configuration) à droite de la zone de texte.
- **Configuration File Path** : chemin de votre fichier de configuration. Le paramètre de **chemin du fichier de configuration**  commence à la racine du référentiel de fichiers. Vous pouvez parcourir votre artefact de build pour sélectionner un fichier de configuration. (bouton `...` à droite de la zone de texte). Les formats de fichier pris en charge sont les suivants : yaml, json, properties. Voici un exemple de fichier de configuration au format json.
    ```json
    {
        "TestApp:Settings:BackgroundColor":"#FFF",
        "TestApp:Settings:FontColor":"#000",
        "TestApp:Settings:FontSize":"24",
        "TestApp:Settings:Message": "Message data"
    }
    ```
- **Separator** : séparateur utilisé pour aplatir les fichiers .JSON et .yml.
- **Depth** : profondeur à laquelle les fichiers .JSON et .yml seront aplatis.
- **Prefix** : chaîne ajoutée au début de chaque clé envoyée au magasin App Configuration.
- **Label** : chaîne ajoutée à chaque paire clé-valeur en tant qu’étiquette dans le magasin App Configuration.
- **Content Type** : chaîne ajoutée à chaque paire clé-valeur en tant que type de contenu dans le magasin App Configuration.
- **Tags** : objet JSON au format `{"tag1":"val1", "tag2":"val2"}`, qui définit les étiquettes ajoutées à chaque paire clé-valeur envoyée à votre magasin App Configuration.
- **Delete all other Key-Values in store with the specified prefix and label** : la valeur par défaut est **Désactivé**.
  - **Activé** : supprime toutes les paires clé-valeur dans le magasin App Configuration qui correspondent à la fois au préfixe et à l’étiquette spécifiés avant d’envoyer de nouvelles paires clé-valeur à partir du fichier de configuration.
  - **Désactivé** : envoie toutes les paires clé-valeur du fichier de configuration dans le magasin App Configuration, et laisse intact tout le reste dans le magasin App Configuration.



## <a name="troubleshooting"></a>Dépannage

Si une erreur inattendue se produit, les journaux de débogage peuvent être activés en affectant la valeur `true` à la variable de pipeline `system.debug`.

## <a name="faq"></a>Questions fréquentes (FAQ)

**Comment faire pour charger plusieurs fichiers de configuration ?**

Créez plusieurs instances de la tâche Azure App Configuration Push dans le même pipeline pour envoyer plusieurs fichiers de configuration au magasin App Configuration.

**Comment créer des références Key Vault à l’aide de cette tâche ?**

Pour créer des références Key Vault, définissez le paramètre « Type de contenu » sur *application/vnd.microsoft.appconfig.keyvaultref+json;charset=utf-8*. Si toutes les valeurs de clé d’un fichier de configuration ne sont pas des références Key Vault, placez les références Key Vault et les valeurs de clé normales dans des fichiers de configuration distincts, puis transmettez-les séparément.

**Pourquoi est-ce que je reçois une erreur 409 lors de la tentative d’envoi de paires clé-valeur vers mon magasin de configuration ?**

Une erreur de conflit 409 se produit si la tâche tente de supprimer ou de remplacer une paire clé-valeur qui est verrouillée dans le magasin App Configuration.
