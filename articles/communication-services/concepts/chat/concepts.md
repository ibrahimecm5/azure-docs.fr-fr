---
title: Concepts relatifs aux conversations dans Azure Communication Services
titleSuffix: An Azure Communication Services concept document
description: Découvrez les concepts relatifs aux conversations dans Communication Services.
author: knvsl
manager: chpalm
services: azure-communication-services
ms.author: rifox
ms.date: 06/30/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.subservice: chat
ms.openlocfilehash: f9b5ab547171af33d459cd0a52b1cbb2dfc2511c
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132308462"
---
# <a name="chat-concepts"></a>Concepts relatifs aux conversations

Les kits SDK Conversation Azure Communication Services peuvent être utilisés pour ajouter la fonctionnalité de conversation textuelle en temps réel à vos applications. Cette page récapitule les principaux concepts et fonctionnalités relatifs aux conversations.

Pour en savoir plus sur les langages et les fonctionnalités du kit SDK, consultez [Vue d’ensemble du kit SDK Conversation Communication Services](./sdk-features.md).

## <a name="chat-overview"></a>Présentation de la fonctionnalité de conversation

Les conversations ont lieu au sein de **fils de conversation**. Les fils de conversation ont les propriétés suivantes :

- Un fil de conversation est identifié de manière unique par son `ChatThreadId`. 
- Les fils de conversation peuvent compter un ou plusieurs utilisateurs, considérés comme participants autorisés à leur envoyer des messages. 
- Un utilisateur peut faire partie d’un ou de plusieurs fils de conversation. 
- Seuls les participants aux fils de conversation ont accès à un fil de conversation donné, et ils sont les seuls à pouvoir effectuer des opérations sur les fils de conversation. Ces opérations incluent l’envoi et la réception de messages, l’ajout de participants et la suppression de participants. 
- Les utilisateurs sont automatiquement ajoutés en tant que participants aux fils de conversation qu’ils créent.

### <a name="user-access"></a>Accès utilisateur
En général, le créateur et les participants du fil de conversation ont le même niveau d’accès au fil de conversation ; ils peuvent exécuter toutes les opérations associées, disponibles dans le kit SDK, y compris la suppression du fil. Un participant ne dispose pas de l’accès en écriture aux messages envoyés par d’autres participants, ainsi seul l’expéditeur du message peut mettre à jour ou supprimer les messages qu’il envoie. Si un autre participant tente de le faire, il obtiendra une erreur. 

### <a name="chat-data"></a>Données de conversation 
Communication Services stocke l’historique de conversation jusqu’à ce qu’il soit explicitement supprimé. Les participants aux fils de conversation peuvent utiliser `ListMessages` pour afficher l’historique des messages d’un fil particulier. Les utilisateurs supprimés d’un fil de conversation peuvent afficher l’historique des messages précédents, mais ils ne peuvent pas envoyer ni recevoir de nouveaux messages au sein de ce fil de conversation. Un fil totalement inactif, sans aucun participant, est automatiquement supprimé au bout de 30 jours. Pour en savoir plus sur les données stockées par Communication Services, consultez la documentation sur la [confidentialité](../privacy.md).  

### <a name="service-limits"></a>Limites du service
- Le nombre maximal de participants autorisés dans un fil de conversation est de 250.
- La taille maximale autorisée pour un message est d’environ 28 Ko.
- Pour les fils de conversations comprenant plus de 20 participants, les caractéristiques de confirmations de lecture et d’indicateur de saisie sont désactivés.

## <a name="chat-architecture"></a>Architecture des conversations

L’architecture des conversations comporte deux parties principales : 1) un service approuvé et 2) une application cliente.

:::image type="content" source="../../media/chat-architecture.png" alt-text="Diagramme montrant l’architecture des conversations dans Communication Services":::

 - **Service approuvé :** Pour gérer correctement une session de conversation, vous avez besoin d’un service vous permettant de vous connecter à Communication Services à l’aide de votre chaîne de connexion de ressource. Ce service est responsable de la création de fils de conversation, de l’ajout et de la suppression de participants et de l’émission de jetons d’accès pour les utilisateurs. Pour plus d’informations sur l’obtention des jetons d’accès, consultez notre guide de démarrage rapide sur les [jetons d’accès](../../quickstarts/access-tokens.md).
 - **Application cliente :** L’application cliente se connecte à votre service approuvé et reçoit les jetons d’accès qui sont utilisés par les utilisateurs pour se connecter directement à Communication Services. À partir du moment où votre service approuvé a créé le fil de conversation et ajouté des utilisateurs en tant que participants, ceux-ci peuvent utiliser l’application cliente pour se connecter au fil de conversation et envoyer des messages. Utilisez la fonctionnalité de notifications en temps réel (nous allons l’aborder plus bas) dans votre application cliente pour vous abonner à des mises à jour de fils de conversation et de messages provenant d’autres participants.


## <a name="message-types"></a>Types de messages

Dans les limites de l’historique des messages, la fonctionnalité Conversation partage les messages créés par l’utilisateur ainsi que ceux générés par le système. Les messages système sont générés lorsqu’un fil de conversation est mis à jour, ils peuvent aider à identifier le moment auquel un participant a été ajouté ou supprimé, ou quand le sujet du fil de conversation a été mise à jour. Lorsque vous appelez `List Messages` ou `Get Messages` sur un fil de conversation, le résultat contient les deux types de messages, classés par ordre chronologique.

Pour les messages créés par l’utilisateur, le type de message peut être défini dans `SendMessageOptions` lors de l’envoi d’un message au fil de conversation. Si aucune valeur n’est fournie, Communication Services utilise par défaut le type `text`. La définition de cette valeur est importante lors de l’envoi de code HTML. Quand `html` est spécifié, Communication Services assainit le contenu pour s’assurer qu’il est restitué en toute sécurité sur les appareils clients.
 - `text` : message en texte brut composé et envoyé par un utilisateur dans le cadre d’un fil de conversation. 
 - `html` : message mis en forme avec le langage HTML, composé et envoyé par un utilisateur dans le cadre d’un fil de conversation. 

Types de messages système : 
 - `participantAdded` : Message système qui indique qu’un ou plusieurs participants ont été ajoutés au fil de conversation.
 - `participantRemoved` : Message système qui indique qu’un participant a été supprimé du fil de conversation.
 - `topicUpdated` : message système qui indique que le sujet du thread a été mis à jour.

## <a name="real-time-notifications"></a>Notifications en temps réel

Certains kits SDK (comme le kit SDK Conversation JavaScript) prennent en charge les notifications en temps réel. Cette fonctionnalité permet aux clients d’écouter Communication Services à la recherche de mises à jour en temps réel et de messages entrants sur un fil de conversation sans avoir à interroger les API. L’application cliente peut s’abonner aux événements suivants :
 - `chatMessageReceived` : lorsqu’un nouveau message est envoyé à un fil de conversation par un participant.
 - `chatMessageEdited` : lorsqu’un message est modifié dans un fil de conversation.
 - `chatMessageDeleted` : lorsqu’un message est supprimé d’un fil de conversation.
 - `typingIndicatorReceived` : lorsqu’un autre participant envoie un indicateur de frappe au fil de conversation.
 - `readReceiptReceived` : lorsqu’un autre participant envoie une confirmation de lecture pour un message qu’il a lu.
 - `chatThreadCreated` : lorsqu’un utilisateur Communication Services crée un fil de conversation.
 - `chatThreadDeleted` : lorsqu’un utilisateur Communication Services supprime un fil de conversation.
 - `chatThreadPropertiesUpdated` : lorsque les propriétés du fil de conversation sont mises à jour ; pour l’instant, seule la mise à jour du sujet du fil est prise en charge.
 - `participantsAdded` : lorsqu’un utilisateur est ajouté en tant que participant à un fil de conversation.
 - `participantsRemoved` : lorsqu’un participant existant est supprimé du fil de conversation.

Les notifications en temps réel peuvent être utilisées pour fournir une expérience de conversation en temps réel à vos utilisateurs. Pour envoyer des notifications push par rapport aux messages manqués par vos utilisateurs pendant leur absence, Communication Services s’intègre à Azure Event Grid pour publier des événements liés aux conversations (opération post), ce qui peut être incorporé à votre service de notification d’application personnalisé. Pour plus d’informations, consultez [Événements de serveur](../../../event-grid/event-schema-communication-services.md?bc=https%3a%2f%2fdocs.microsoft.com%2fen-us%2fazure%2fbread%2ftoc.json&toc=https%3a%2f%2fdocs.microsoft.com%2fen-us%2fazure%2fcommunication-services%2ftoc.json).


## <a name="build-intelligent-ai-powered-chat-experiences"></a>Générer des expériences de conversation intelligente, intégrant l’IA

Vous pouvez utiliser les [API Azure Cognitive](../../../cognitive-services/index.yml) avec le kit SDK Conversation pour créer des cas d’usage tels que :

- Permettre aux utilisateurs de discuter les uns avec les autres dans différentes langues
- Aider un agent du support à hiérarchiser les tickets en détectant un sentiment négatif dans le message entrant d’un client
- Analyser les messages entrants à des fins de détection des clés et de reconnaissance d’entité, et demander des informations pertinentes à l’utilisateur dans votre application en fonction du contenu du message

Pour ce faire, vous pouvez faire en sorte que votre service approuvé agisse en tant que participant d’un fil de conversation. Supposons que vous souhaitiez activer la fonctionnalité de traduction. Ce service est chargé d’écouter les messages échangés par d’autres participants [1], d’appeler les API cognitives pour traduire le contenu vers la langue souhaitée [2, 3] et d’envoyer le résultat traduit sous la forme d’un message dans le fil de conversation [4].

De cette façon, l’historique des messages contient à la fois les messages d’origine et les messages traduits. Dans l’application cliente, vous pouvez ajouter une logique pour afficher le message d’origine ou le message traduit. Pour savoir comment utiliser les API cognitives dans le but de traduire du texte en différentes langues, consultez [ce guide de démarrage rapide](../../../cognitive-services/translator/quickstart-translator.md). 

:::image type="content" source="../media/chat/cognitive-services.png" alt-text="Diagramme montrant Cognitive Services qui interagit avec Communication Services":::

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Bien démarrer avec les conversations](../../quickstarts/chat/get-started.md)

Les documents suivants peuvent vous intéresser :
- Se familiariser avec le [kit SDK Conversation](sdk-features.md)
