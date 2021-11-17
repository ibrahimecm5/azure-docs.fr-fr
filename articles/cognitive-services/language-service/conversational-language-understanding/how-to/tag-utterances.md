---
title: Baliser des énoncés dans Language Understanding de conversation
titleSuffix: Azure Cognitive Services
description: Utilisez cet article pour baliser vos énoncés dans des projets de Language Understanding de conversation
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: overview
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-clu, ignite-fall-2021
ms.openlocfilehash: 73ab3df8a7f13ec2c734206772f55bee626b871a
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132339931"
---
# <a name="how-to-tag-utterances"></a>Baliser des énoncés

Une fois que vous avez [créé un schéma](build-schema.md) pour votre projet, vous devez ajouter des énoncés d’apprentissage à votre projet. Les énoncés doivent être similaires à ce que vos utilisateurs utiliseront lors de l’interaction avec le projet. Lorsque vous ajoutez un énoncé, vous devez lui assigner l’intention à laquelle il appartient. Une fois l’énoncé ajouté, balisez les mots de votre énoncé avec les entités de votre projet. Vos balises pour les entités doivent être cohérentes entre les différents énoncés.

Le balisage est le processus qui consiste à assigner vos énoncés à des intentions et à les étiqueter avec des entités. Vous souhaiterez passer du temps à baliser vos énoncés, en introduisant et en affinant les données qui vont former les modèles de Machine Learning sous-jacents de votre projet. Les modèles de Machine Learning généralisent en fonction des exemples que vous fournissez. Plus vous fournissez d’exemples, plus il faut de points de données pour améliorer les généralisations.

> [!NOTE]
>  Les composants appris d’une entité sont définis uniquement lorsque vous balisez des énoncés pour cette entité. Vous pouvez également avoir des entités qui incluent _uniquement_ des composants de liste ou prédéfinis sans baliser les composants appris. Pour plus d’informations, consultez l’article [Composants d’entité](../concepts/entity-components.md).

Lorsque vous activez plusieurs langues dans votre projet, vous devez également spécifier la langue de l’énoncé que vous ajoutez. Dans le cadre des fonctionnalités multilingues de Language Understanding de conversation, vous pouvez former votre projet dans un langage dominant, puis prédire dans les autres langages disponibles. L’ajout d’exemples à d’autres langages augmente les performances du modèle dans ces langues si vous déterminez que cela ne fonctionne pas bien, mais évitez de dupliquer vos données dans toutes les langues que vous souhaitez prendre en charge. 

Par exemple, pour améliorer les performances d’un bot de calendrier avec les utilisateurs, un développeur peut ajouter des exemples en anglais en anglais, et quelques-uns, en espagnol ou en français. Ils peuvent ajouter des énoncés tels que :

* « _Définissez une réunion avec **Matt** et **Kevin** **demain** à **12h00** »._ (Anglais)
* « _Répondre comme **tentative** à la réunion des **mises à jour hebdomadaires** »._ (Anglais)
* "_Cancelar mi **próxima** reunión_." (Espagnol)

Dans les projets de workflow d’orchestration, les données utilisées pour l’apprentissage des intentions connectées ne sont pas fournies dans le projet. Au lieu de cela, le projet extrait les données du service connecté (telles que les applications LUIS connectées, les projets de Language Understanding conversationnel ou les bases de connaissances personnalisées répondant aux questions) au cours de l’apprentissage. Toutefois, si vous créez des intentions qui ne sont pas connectées à un service, vous devez toujours ajouter des énoncés à ces intentions.

Par exemple, un développeur peut créer une intention pour chacune de ses compétences et le connecter à un projet de calendrier, à un projet de courrier électronique et à la base de connaissances FAQ de l’entreprise. 

## <a name="tag-utterances"></a>Étiqueter les énoncés

:::image type="content" source="../media/tag-utterances.png" alt-text="Capture d’écran de la page de balisage des énoncés dans Language Studio." lightbox="../media/tag-utterances.png":::

La *section 1* est l’endroit où vous ajoutez vos énoncés. Vous devez sélectionner l’une des intentions dans la liste déroulante, la langue de l’énoncé (le cas échéant) et l’énoncé proprement dit. Appuyez sur la touche *Entrée* dans la zone de texte de l’énoncé pour ajouter l’énoncé.

La *section 2* comprend les entités de votre projet. Vous pouvez sélectionner n’importe quelle entité que vous avez ajoutée, puis pointer sur le texte pour baliser les entités dans vos énoncés, comme indiqué dans la *section 3*. Vous pouvez également ajouter de nouvelles entités ici en appuyant sur le bouton **+ Ajouter une entité**. Vous pouvez également masquer les balises de cette entité dans vos énoncés. 

La *section 3* comprend les énoncés que vous avez ajoutés. Vous pouvez faire glisser le texte que vous souhaitez baliser et un menu contextuel des entités s’affiche.

:::image type="content" source="../media/label-utterance-menu.png" alt-text="Exemple de sélection de texte pour ajouter une balise." lightbox="../media/label-utterance-menu.png":::

> [!NOTE]
> Contrairement à LUIS, vous ne pouvez pas baliser les entités qui se chevauchent. Les mêmes caractères ne peuvent pas être balisés par plus d’une entité.

## <a name="filter-utterances"></a>Filtrer les énoncés

Cliquer sur **Filtrer** vous permet d’afficher uniquement les énoncés associés aux intentions et aux entités que vous sélectionnez dans le volet Filtre.
Lorsque vous cliquez sur une intention dans la page [schéma de la build](./build-schema.md), vous êtes déplacé vers la page des **Énoncés de balises**, avec cet intention filtrée automatiquement. 

## <a name="next-steps"></a>Étapes suivantes
* [Effectuer l'apprentissage et évaluer des modèles](./train-model.md)
