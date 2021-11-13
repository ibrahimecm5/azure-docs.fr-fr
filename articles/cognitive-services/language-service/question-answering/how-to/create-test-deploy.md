---
title: Créer, tester et déployer votre projet de réponse aux questions
description: Vous pouvez créer un projet de réponse aux questions à partir de votre propre contenu, tel que des FAQ ou des manuels de produit. Cet article inclut un exemple de création de projet de réponse aux questions à partir d’une simple page web de questions fréquentes (FAQ), pour répondre à des questions.
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: quickstart
ms.date: 11/02/2021
ms.custom: language-service-question-answering, ignite-fall-2021
ms.openlocfilehash: f190c2aec509af157944fb11a110953c9e8bbdb0
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131476601"
---
# <a name="create-test-and-deploy-a-custom-question-answering-project"></a>Créer, tester et déployer un projet personnalisé de réponse aux questions

Vous pouvez créer un projet de réponse aux questions à partir de votre propre contenu, tel que des FAQ ou des manuels de produit. Cet article contient un exemple de création de projet de réponse aux questions à partir d’un manuel de produit, pour répondre à des questions.

## <a name="prerequisites"></a>Prérequis

> [!div class="checklist"]
> * Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/cognitive-services/) avant de commencer.
> * Une [ressource de langage](https://aka.ms/create-language-resource) avec la fonctionnalité Réponse aux questions personnalisée activée.

## <a name="create-your-first-question-answering-project"></a>Créer votre premier projet de réponse aux questions

1. Connectez-vous à [Language Studio](https://language.azure.com/) avec vos informations d’identification Azure.

2. Faites défiler jusqu’à la section **Answer questions** et sélectionnez **Open custom question answering**.

    > [!div class="mx-imgBorder"]
    > ![Open custom question answering](../media/create-test-deploy/open-custom-question-answering.png)

3. Si votre ressource n’est pas encore connectée à Recherche Azure, sélectionnez **Connect to Azure Search**. Un nouvel onglet de navigateur s’ouvre dans le volet **Fonctionnalités** de votre ressource dans le portail Azure.

    > [!div class="mx-imgBorder"]
    > ![Se connecter à la Recherche Azure](../media/create-test-deploy/connect-to-azure-search.png)

4. Sélectionnez **Enable custom question answering**, choisissez la ressource Recherche Azure à lier, puis sélectionnez **Appliquer**.

    > [!div class="mx-imgBorder"]
    > ![Enable custom question answering](../media/create-test-deploy/enable-custom-question-answering.png)

5. Revenez à l’onglet Language Studio. Vous devrez peut-être actualiser cette page pour qu’elle affiche la modification apportée à votre ressource. Sélectionnez **Create new project**.

6. Choisissez l’option **I want to set the language for all projects created in this resource** > sélectionnez **English** > **Next**.

7. Entrez le nom de projet **Exemple-de-projet**, la description **Mon premier projet de réponses aux questions** et laissez la réponse par défaut avec le paramètre **No answer found**.

8. Passez en revue vos choix, puis sélectionnez **Create project**.

9. Dans la page **Manage sources**, sélectionnez **Add source** > **URLS**.

10. Sélectionnez **Add url**, entrez les valeurs suivantes, puis sélectionnez **Add all** :

    |Nom de l’URL|Valeur de l’URL|
    |--------|---------|
    |Guide de l’utilisateur Surface Book |https://download.microsoft.com/download/7/B/1/7B10C82E-F520-4080-8516-5CF0D803EEE0/surface-book-user-guide-EN.pdf |

    Le processus d’extraction prend quelques minutes pour lire le document et identifier les questions et réponses. La question et la réponse déterminent si le contenu sous-jacent est structuré ou non.

    Après avoir ajouté la source, vous pouvez modifier son contenu pour ajouter d’autres jeux de réponses aux questions personnalisées.

## <a name="test-your-project"></a>Tester votre projet

1. Sélectionnez le lien vers votre source pour ouvrir la page de modification de la base de connaissances.

2. Sélectionnez **Test** dans la barre de menus > Entrez la question **Comment faire pour configurer mon Surface Book ?** . Une réponse sera générée en fonction des paires de réponses aux questions qui ont été automatiquement identifiées et extraites de votre URL source :

    > [!div class="mx-imgBorder"]
    > ![Tester l’interface de conversation de questions](../media/create-test-deploy/test-question.png)

    Si vous cochez la case pour **inclure une réponse brève**, vous verrez également une réponse précise, si elle est disponible, ainsi que le passage de la réponse dans le volet test lorsque vous posez une question.

3. Sélectionnez **Inspect** pour examiner la réponse plus en détail. La fenêtre de test est utilisée pour tester vos modifications apportées à votre projet avant de déployer votre projet.

    > [!div class="mx-imgBorder"]
    > ![Afficher l’intervalle de confiance](../media/create-test-deploy/inspect-test.png)

    À partir de l’interface **Inspect**, vous pouvez voir le niveau de confiance de cette réponse à la question, et modifier directement une paire de réponse question/réponse.

## <a name="deploy-your-project"></a>Déployez votre projet

1. Sélectionnez l’icône Déployer la base de connaissances pour accéder au menu de déploiement de la base de connaissances.

    > [!div class="mx-imgBorder"]
    > ![Déployer la base de connaissances](../media/create-test-deploy/deploy-knowledge-base.png)

    Lorsque vous déployez un projet, le contenu de votre projet passe de l’index `test` à un index `prod` dans Recherche Azure.

2. Sélectionnez **Deploy** > et, à l’invite, sélectionnez à nouveau **Déployer**.

    > [!div class="mx-imgBorder"]
    > ![Déploiement réussi](../media/create-test-deploy/successful-deployment.png)

    Votre projet a été déployé avec succès. Vous pouvez utiliser le point de terminaison pour répondre aux questions dans votre propre application personnalisée ou dans un bot.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous ne continuez pas à tester la réponse aux questions personnalisée, vous pouvez supprimer la ressource associée.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Ajouter des questions avec des métadonnées](../../../qnamaker/quickstarts/add-question-metadata-portal.md)
