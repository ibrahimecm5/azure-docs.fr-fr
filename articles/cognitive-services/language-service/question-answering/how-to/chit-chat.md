---
title: Ajout d’un chit-chat à une question personnalisée de la base de connaissances
titleSuffix: Azure Cognitive Services
description: L’ajout d’échanges de chit-chat personnels à votre bot rend ses conversations plus naturelles et engageantes quand vous créez un projet. Les réponses aux questions personnalisées vous permettent d’ajouter facilement un ensemble prédéfini du chit-chat supérieur dans vos bases de connaissances.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.custom: language-service-question-answering, ignite-fall-2021
ms.openlocfilehash: 2e46b7b877e3ce8e82782356339261ff5204bf26
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131096339"
---
# <a name="use-chitchat-with-a-knowledge-base"></a>Utiliser un chit-chat avec une base de connaissances

L’ajout du chit-chat de conversation à votre bot rend ses conversations plus naturelles et engageantes. La fonctionnalité de chit-chat de réponses aux questions personnalisées vous permet d’ajouter facilement un ensemble prédéfini de chit-chat supérieur dans votre base de connaissances (KB). Cela peut constituer un point de départ pour la personnalité de votre bot, et vous permettra d’économiser le temps et les efforts qui seraient nécessaires à leur écriture à partir de zéro.

Ce jeu de données compte environ 100 scénarios d’échanges de conversation avec la voix de plusieurs personnes : le professionnel, l’ami et le comique. Choisissez la personnalité qui se rapproche le plus de la voix de votre bot. Pour une requête utilisateur donnée, les réponses aux questions tentent de la faire correspondre avec la question et la réponse du chit-chat connues les plus proches.

Voici quelques exemples des différentes personnalités. Vous pouvez voir tous les [jeux de données](https://github.com/microsoft/botframework-cli/blob/main/packages/qnamaker/docs/chit-chat-dataset.md) de la personnalité, ainsi que les détails des personnalités.

Pour la requête utilisateur de `When is your birthday?`, chaque personnalité a une réponse qui lui est propre :

<!-- added quotes so acrolinx doesn't score these sentences -->
|Personnalité|Exemple|
|--|--|
|Professionnel|L’âge ne me concerne pas réellement.|
|Convivial|Je n’ai pas vraiment d’âge.|
|Comique|Je n’ai pas d’âge.|
|Attentif|Je n’ai pas d’âge.|
|Enthousiaste|Je suis un bot, donc je n’ai pas d’âge.|
||

## <a name="language-support"></a>Support multilingue

Les jeux de données du chit-chat sont pris en charge dans les langues suivantes :

|Langage|
|--|
|Chinois|
|Anglais|
|Français|
|Allemagne|
|Italien|
|Japonais|
|Coréen|
|Portugais|
|Espagnol|

## <a name="add-chitchat-source"></a>Ajouter une source de chit-chat
Après avoir créé votre projet, vous pouvez ajouter des sources à partir d’URL, de fichiers, ainsi que de chit-chat à partir du volet **Gérer les sources**.

> [!div class="mx-imgBorder"]
> ![Ajouter une source de chit-chat](../media/chit-chat/add-source.png)

Choisissez la personnalité que vous souhaitez utiliser comme base pour le chit-chat.

> [!div class="mx-imgBorder"]
> ![Menu de différentes personnalités chit-chat](../media/chit-chat/personality.png)

## <a name="edit-your-chitchat-questions-and-answers"></a>Modifier vos questions et réponses de chit-chat

Quand vous modifierez votre base de connaissances, vous verrez une nouvelle source pour le chit-chat, basée sur la personnalité sélectionnée. Vous pouvez maintenant ajouter des questions modifiées, ou modifier les réponses, comme avec toute autre source.

> [!div class="mx-imgBorder"]
> ![Modifier les paires de questions de chit-chat](../media/chit-chat/edit-chit-chat.png)

Pour activer et désactiver les vues pour le contexte et les métadonnées, sélectionnez **Afficher les colonnes** dans la barre d’outils.

## <a name="add-more-chitchat-questions-and-answers"></a>Ajouter d’autres questions et réponses au chit-chat

Vous pouvez ajouter une nouvelle paire de questions au chit-chat qui ne se trouve pas dans le jeu de données prédéfini. Veillez à ne pas dupliquer une paire de questions déjà couverte dans le jeu du chit-chat. Lorsque vous ajoutez une nouvelle paire de questions du chit-chat, elle est ajoutée à votre source **éditoriale**. Pour être sûr que la fonctionnalité de classement comprend qu’il s’agit d’un chit-chat, ajoutez la paire clé/valeur de métadonnées « Editorial: chitchat », comme indiqué dans l’image suivante :

:::image type="content" source="../media/chit-chat/add-new-chit-chat.png" alt-text="Ajouter des paires de réponses à la question du chit-chat" lightbox="../media/chit-chat/add-new-chit-chat.png":::

## <a name="delete-chitchat-from-your-project"></a>Supprimer des chit-chat de votre projet

Sélectionnez le volet **gérer les sources**, puis choisissez votre source de chit-chat. Votre source de chit-chat spécifique est indiquée en tant que fichier TSV, avec le nom de personnalité sélectionné. Sélectionnez **Supprimer** dans la barre d’outils.

> [!div class="mx-imgBorder"]
> ![Supprimer la source du chit-chat](../media/chit-chat/delete-chit-chat.png)

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Importer une base de connaissances](./migrate-knowledge-base.md)
