---
title: 'Tutoriel : Accéder aux données dans Cosmos DB en utilisant Mongoose avec Azure Static Web Apps'
description: Découvrez comment accéder aux données dans Cosmos DB en utilisant Mongoose à partir d’une fonction API Azure Static Web Apps.
author: GeekTrainer
ms.author: chrhar
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 01/25/2021
ms.openlocfilehash: 4956bf409190b560824f57e2f79e94ae5360e99a
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130258066"
---
# <a name="tutorial-access-data-in-cosmos-db-using-mongoose-with-azure-static-web-apps"></a>Tutoriel : Accéder aux données dans Cosmos DB en utilisant Mongoose avec Azure Static Web Apps

[Mongoose](https://mongoosejs.com/) est le client ODM (Object Document Mapping) le plus populaire pour Node.js. En vous permettant de concevoir une structure de données et d’appliquer la validation, Mongoose vous apporte tous les outils nécessaires pour interagir avec les bases de données qui prennent en charge l’API Mongoose. [Cosmos DB](../cosmos-db/mongodb-introduction.md) prend en charge les API Mongoose nécessaires et est disponible en tant qu’option de serveur back-end sur Azure.

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> - Créer un compte Cosmos DB serverless
> - Créer des applications Azure Static Web Apps
> - Mettre à jour les paramètres d’application pour stocker la chaîne de connexion

Si vous n’avez pas d’abonnement Azure, créez un [compte d’essai gratuit](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Prérequis

- un [compte Azure](https://azure.microsoft.com/free/) ;
- Un [compte GitHub](https://github.com/join)

## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous au [portail Azure](https://portal.azure.com).

## <a name="create-a-cosmos-db-serverless-database"></a>Créer une base de données Cosmos DB serverless

Commencez par créer un compte [Cosmos DB serverless](../cosmos-db/serverless.md). En utilisant un compte serverless, vous payez uniquement pour les ressources au fur et à mesure de leur utilisation, et vous n’avez pas à créer une infrastructure complète.

1. Accédez à [https://portal.azure.com](https://portal.azure.com)
2. Cliquez sur **Créer une ressource**
3. Entrez **Azure Cosmos DB** dans la zone de recherche
4. Cliquez sur **Azure Cosmos DB**
5. Cliquez sur **Créer**
6. Si vous y êtes invité, sous **API Azure Cosmos DB pour MongoDB**, sélectionnez **Créer**
7. Configurez votre compte Azure Cosmos DB avec les informations suivantes
    - Abonnement : choisissez l’abonnement à utiliser
    - Ressource : cliquez sur **Créer**, puis indiquez le nom **aswa-mongoose**
    - Nom du compte : une valeur unique est nécessaire
    - Localisation : **USA Ouest 2**
    - Mode de capacité : **Serverless (préversion)**
    - Version : **4.0**
:::image type="content" source="media/add-mongoose/cosmos-db.png" alt-text="Créer une instance de Cosmos DB":::
8. Cliquez sur **Vérifier + créer**.
9. Cliquez sur **Créer**

Le processus de création prend quelques minutes. Les prochaines étapes vous font retourner à la base de données pour recueillir la chaîne de connexion.

## <a name="create-a-static-web-app"></a>Créer une application web statique

Ce tutoriel utilise un dépôt de modèles GitHub pour vous aider à créer votre application.

1. Accédez au [modèle de démarrage](https://github.com/login?return_to=/staticwebdev/mongoose-starter/generate)
2. Choisissez le **propriétaire** (si vous utilisez une organisation différente de votre compte principal)
3. Nommez votre dépôt **aswa-mongoose-tutorial**
4. Cliquez sur **Create repository from template** (Créer un dépôt à partir du modèle)
5. Retournez au [portail Azure](https://portal.azure.com)
6. Cliquez sur **Créer une ressource**
7. Tapez **application web statique** dans la zone de recherche
8. Sélectionnez **Static Web App**
9. Cliquez sur **Créer**
10. Configurez votre application Azure Static Web Apps avec les informations suivantes
    - Abonnement : choisissez le même abonnement qu’auparavant
    - Groupe de ressources : sélectionnez **aswa-mongoose**
    - Nom : **aswa-mongoose-tutorial**
    - Région : **USA Ouest 2**
    - Cliquez sur **Se connecter avec GitHub**
    - Cliquez sur **Autoriser** si vous êtes invité à autoriser Azure Static Web Apps à créer la fonctionnalité GitHub Actions pour activer le déploiement
    - Organisation : Nom de votre compte GitHub
    - Dépôt : **aswa-mongoose-tutorial**
    - Branche : **main**
    - Options prédéfinies de build : choisissez **Personnalisé**
    - Emplacement de l’application : **/public**
    - Emplacement de l’API : **api**
    - Emplacement de la sortie : *Laissez vide*
    :::image type="content" source="media/add-mongoose/azure-static-web-apps.png" alt-text="Formulaire Azure Static Web Apps renseigné":::
11. Cliquez sur **Vérifier et créer**
12. Cliquez sur **Créer**
13. Le processus de création prend quelques minutes. Cliquez sur **Accéder à la ressource** une fois l’application web statique provisionnée

## <a name="configure-database-connection-string"></a>Configurer la chaîne de connexion de base de données

Pour permettre à l’application web de communiquer avec la base de données, la chaîne de connexion de base de données est stockée en tant que [paramètre d’application](application-settings.md). Les valeurs des paramètres sont accessibles dans Node.js à l’aide de l’objet `process.env`.

1. Cliquez sur **Accueil** dans l’angle supérieur gauche du portail Azure (ou revenez à [https://portal.azure.com](https://portal.azure.com))
2. Cliquer sur **Groupes de ressources**
3. Cliquez sur **aswa-mongoose**
4. Cliquez sur le nom de votre compte de base de données. Son type est **API Azure Cosmos DB pour Mongo DB**
5. Sous **Paramètres**, cliquez sur **Chaîne de connexion**
6. Copiez la chaîne de connexion listée sous **CHAÎNE DE CONNEXION PRINCIPALE**
7. Dans la barre d’adresses, cliquez sur **aswa-mongoose**
8. Cliquez sur **aswa-mongoose-tutorial** pour retourner à l’instance du site web
9. Sous **Paramètres**, cliquez sur **Configuration**
10. Cliquez sur **Ajouter**, puis créez un paramètre d’application avec les valeurs suivantes
    - Nom : **CHAÎNE_CONNEXION**
    - Valeur : collez la chaîne de connexion que vous avez copiée plus tôt
11. Cliquez sur **OK**
12. Cliquez sur **Enregistrer**.

## <a name="navigate-to-your-site"></a>Accéder à votre site

Vous pouvez à présent explorer l’application web statique.

1. Cliquez sur **Vue d’ensemble**
1. Cliquez sur l’URL affichée dans le coin supérieur droit
    1. Elle ressemble à `https://calm-pond-05fcdb.azurestaticapps.net`
1. Cliquez sur **Connectez-vous pour voir votre liste de tâches**
1. Cliquez sur **Donner son consentement** pour accéder à l’application
1. Créez une tâche en tapant un titre et en cliquant sur **Ajouter une tâche**
1. Vérifiez que la tâche s’affiche (cela peut prendre un moment)
1. Marquez la tâche comme étant effectuée en **cochant la case**
1. **Actualisez la page** pour confirmer l’utilisation d’une base de données

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous ne comptez pas continuer à utiliser cette application, supprimez le groupe de ressources en effectuant les étapes suivantes :

1. Retournez au [portail Azure](https://portal.azure.com)
2. Cliquer sur **Groupes de ressources**
3. Cliquez sur **aswa-mongoose**
4. Cliquez sur **Supprimer le groupe de ressources**
5. Tapez **aswa-mongoose** dans la zone de texte
6. Cliquez sur **Supprimer**

## <a name="next-steps"></a>Étapes suivantes

Passez à l’article suivant pour découvrir comment configurer un développement local...
> [!div class="nextstepaction"]
> [Configurer le développement local](./local-development.md)
