---
title: Créer un schéma de projet de Language Understanding conversationnel
titleSuffix: Azure Cognitive Services
description: Utilisez cet article pour commencer à créer un schéma de projet Language Understanding conversationnel
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: overview
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-clu, ignite-fall-2021
ms.openlocfilehash: 0a140457e67c24cc363d7c660f641a2c98a25939
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131098168"
---
# <a name="how-to-build-your-project-schema"></a>Générer votre schéma de projet
 
Dans un projet Language Understanding de conversation, le *schéma* est défini comme la combinaison d’intentions et d’entités dans votre projet. La conception de schéma est un élément essentiel du succès de votre projet. Lorsque vous créez un schéma, vous souhaitez réfléchir à la façon dont les intentions et les entités doivent être incluses dans votre projet.

## <a name="guidelines-and-recommendations"></a>Instructions et recommandations

Tenez compte des recommandations suivantes lors de la sélection des intentions pour votre projet :

  1. Créer des intentions distinctes et séparables. Une intention est mieux décrite en tant qu’action globale souhaitée de l’utilisateur. Imaginez le projet que vous créez et identifiez toutes les actions que vos utilisateurs peuvent effectuer lors de l’interaction avec votre projet. Envoyer, appeler et annuler sont toutes des actions qui sont le mieux représentées comme des intentions différentes. Annuler une commande et annuler un rendez-vous sont des actions très similaires, à la différence de *ce* qu’elles annulent. Ces deux actions doivent être représentées dans le même but.
  
  2. Créez des entités pour extraire des informations pertinentes dans votre texte. Les entités doivent être utilisées pour capturer les informations pertinentes nécessaires pour accomplir l’action de l’utilisateur. Par exemple, la *commande* ou le *rendez-vous* peut être différent des éléments qu’un utilisateur tente d’annuler, et vous devez créer une entité pour capturer cette information.

Vous pouvez *« envoyer »* un *message*, *« envoyer »* un *e-mail* ou *« envoyer »* un package. Créer une intention de capturer chacune de ces exigences n’est pas mise à l’échelle dans le temps, et vous devez utiliser des entités pour identifier *ce que* l’utilisateur a envoyé. La combinaison d’intentions et d’entités doit déterminer le déroulement de votre conversation. 

Par exemple, imaginons une entreprise dans laquelle les développeurs de bot ont identifié les trois actions les plus courantes prises par leurs utilisateurs dans un calendrier : 

* Configurer de nouvelles réunions 
* Répondre aux demandes de réunion 
* Annuler les réunions 

Ils peuvent créer une intention de représenter chacune de ces actions. Ils peuvent également inclure des entités permettant d’effectuer ces actions, telles que :

* Participants aux réunions
* Date
* Durée de la réunion


Pour les projets de **Workflow d’orchestration**, vous pouvez uniquement créer des intentions. Le projet de workflow d’orchestration est destiné à acheminer vers d’autres services cibles qui peuvent être activés avec l’extraction d’entité pour terminer le flux de conversation. Vous pouvez ajouter de nouvelles intentions qui sont connectées à d’autres services _ou_ créer des intentions qui ne sont pas connectées à un service (tentative déconnectée). 

En ajoutant une intention déconnectée, vous autorisez l’orchestrateur à acheminer vers cette intention et à retourner sans appeler dans un service supplémentaire. Vous devez fournir des exemples d’apprentissage pour les intentions déconnectées. Vous pouvez vous connecter uniquement à des projets appartenant à la même ressource Azure. 

En poursuivant l’exemple de la version antérieure, les développeurs d’un bot peuvent se rendre compte que pour chaque compétence de leur bot (y compris : actions de calendrier, actions de messagerie et FAQ de l’entreprise), elles ont besoin d’un objectif qui se connecte à chacune de ces compétences.  

## <a name="build-project-schema-for-conversation-projects"></a>Générer le schéma de projet pour les projets de conversation

Pour générer un schéma de projet de conversation :

1. Sélectionnez l’onglet **Intentions** ou **Entités** dans la page schéma de la build, puis sélectionnez **Ajouter**. Vous êtes invité à entrer un nom avant de terminer la création de l’intention ou de l’entité. 

2. Cliquez sur une intention pour atteindre la page des [énoncés de balises](tag-utterances.md), dans laquelle vous pouvez ajouter des exemples pour les intentions et les étiqueter avec des entités.

    :::image type="content" source="../media/build-schema-page.png" alt-text="Capture d’écran montrant la page de création de schéma pour les projets de conversation dans Language Studio." lightbox="../media/build-schema-page.png":::
    
3. Après avoir créé une entité, vous êtes dirigé vers la page de détails de l’entité. Chaque composant est défini par plusieurs composants. Vous pouvez étiqueter des exemples dans la page des énoncés de balises pour former un composant appris, ajouter une liste de valeurs à rechercher dans le composant de liste ou ajouter un ensemble de composants prédéfinis à partir de la bibliothèque disponible. Vous trouverez de plus amples informations sur les composants [ici](../concepts/entity-components.md)

    :::image type="content" source="../media/entity-details.png" alt-text="Capture d’écran montrant la page des détails des entités pour les projets de conversation dans Language Studio." lightbox="../media/entity-details.png":::

## <a name="build-project-schema-for-orchestration-workflow-projects"></a>Schéma de projet de génération pour les projets de workflow d’orchestration

Pour créer un schéma de projet de génération pour les projets de workflow d’orchestration : 

1. Sélectionnez **Ajouter** dans la page **Générer un schéma**. Vous serez invité à entrer un nom et à définir une connexion pour l’intention, le cas échéant. Si vous souhaitez vous connecter à une intention, vous devez fournir :
    1. **Type de service** : LUIS, Custom Question Answering (QnA) ou Language Understanding conversationnel.
    2. **Nom du projet** : projet auquel vous souhaitez que l’intention se connecte.
    3. **Version pour les énoncés** (uniquement pour LUIS) : quelle version de LUIS doit être utilisée pour entraîner le modèle de classification des orchestrateurs.

    :::image type="content" source="../media/orchestration-intent.png" alt-text="Capture d’écran montrant la création intentionnelle modale pour les projets d’orchestration dans Language Studio." lightbox="../media/orchestration-intent.png":::

> [!IMPORTANT]
> * Les intentions connectées ne peuvent pas être sélectionnées, car vous ne pouvez pas ajouter d’exemples d’apprentissage à une intention connectée, car elle utilise déjà les données du projet cible pour effectuer l’apprentissage de sa classification intentionnelle.
> * Vous ne pouvez vous connecter qu’aux services cibles appartenant à la même ressource.

## <a name="next-steps"></a>Étapes suivantes
* [Étiqueter les énoncés](tag-utterances.md)
