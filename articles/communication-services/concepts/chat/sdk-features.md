---
title: Vue d’ensemble du kit SDK Conversation pour Azure Communication Services
titleSuffix: An Azure Communication Services concept document
description: Découvrez le kit SDK Conversation Azure Communication Services.
author: knvsl
manager: chpalm
services: azure-communication-services
ms.author: rifox
ms.date: 06/30/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.subservice: chat
ms.openlocfilehash: 64d88922d849fc29ab5d33451d8d90bed6ed4028
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132308405"
---
# <a name="chat-sdk-overview"></a>Vue d’ensemble du kit SDK Conversation

Les kits SDK Conversation Azure Communication Services peuvent être utilisés pour ajouter la fonctionnalité de conversation en temps réel riche à vos applications.

## <a name="chat-sdk-capabilities"></a>Fonctionnalités du kit SDK Conversation

La liste suivante présente l’ensemble des fonctionnalités actuellement disponibles dans les kits SDK Conversation Communication Services.

| Groupe de fonctionnalités | Fonctionnalité | JavaScript  | Java | .NET | Python | iOS | Android |
|-----------------|-------------------|---|-----|----|-----|----|----|
| Fonctionnalités principales | Créer un fil de conversation entre plusieurs utilisateurs                                                     | ✔️   | ✔️  | ✔️    | ✔️   |  ✔️    | ✔️   |
|                   | Mettre à jour le sujet d’un fil de conversation                                                                              | ✔️   | ✔️ | ✔️    | ✔️   |  ✔️    | ✔️   |
|                   | Ajouter ou supprimer des participants dans un fil de conversation                                                                           | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |
|                   | Choisir si vous souhaitez partager l’historique des messages de conversation avec le participant en cours d’ajout                                   | ✔️   | ✔️   | ✔️    | ✔️  |  ✔️    | ✔️   |
|                   | Obtenir la liste des participants d’un fil de conversation                                                                          | ✔️   | ✔️  | ✔️ | ✔️ |  ✔️    | ✔️   |
|                   | Supprimer un fil de conversation                                                                                              | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |
|                   | Pour un utilisateur de communication, obtenir la liste des fils de conversation auxquels il participe                                           | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |
|                   | Obtenir des informations sur un fil de conversation                                                                              | ✔️   | ✔️  | ✔️ | ✔️ |  ✔️    | ✔️   |
|                   | Envoyer et recevoir des messages dans un fil de conversation                                                                            | ✔️   | ✔️   | ✔️    | ✔️  |  ✔️    | ✔️   |
|                   | Mettre à jour le contenu de votre message envoyé                                                                               | ✔️   | ✔️  | ✔️ | ✔️ |  ✔️    | ✔️   |
|                   | Supprimer un message que vous avez envoyé                                                                                                      | ✔️   | ✔️  | ✔️ | ✔️ |  ✔️    | ✔️   |
|                   | Confirmation de lecture des messages qui ont été lus par d’autres participants dans une conversation                                        | ✔️   | ✔️  | ✔️    | ✔️   |  ✔️    | ✔️   |
|                   | Recevoir une notification lorsque des participants tapent activement un message dans un fil de conversation                                         | ✔️   | ❌    | ❌  | ❌  | ✔️  | ✔️  |
|                   | Obtenir tous les messages d’un fil de conversation                                                                        | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |
|                   | Envoyer des emojis Unicode dans le contenu d’un message                                                                            | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |
|                   | Ajouter des métadonnées aux messages de conversation                                                                            | ✔️   | ✔️  | ✔️    | ✔️  |  ❌    | ✔️   |
|                   | Ajouter un nom complet à la notification de l’indicateur de saisie                                                                            | ✔️   | ✔️  | ✔️    | ✔️  |  ❌    | ✔️   |
|Notifications en temps réel (activées par le package de signalisation propriétaire**)|  Les clients de conversation peuvent s’abonner pour obtenir des mises à jour en temps réel sur les messages entrants et les autres opérations qui se produisent dans un fil de conversation. Si vous souhaitez voir la liste des mises à jour prises en charge pour les notifications en temps réel, consultez [Concepts de Conversation](concepts.md#real-time-notifications)                                     | ✔️   | ❌    | ❌  | ❌  | ✔️  | ✔️  |
| Intégration à Azure Event Grid             | Utilisez les événements de conversation disponibles dans Azure Event Grid pour connecter des services de notifications personnalisées, ou publier un événement particulier dans un Webhook de façon à exécuter une logique métier telle que la mise à jour des enregistrements CRM à la fin d’une conversation.   | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |
| Rapports </br>(Ces informations sont disponibles sous l’onglet Supervision de votre ressource Communication Services sur le portail Azure)      | Comprendre le trafic d’API à partir de votre application de conversation avec la supervision des métriques publiées dans Azure Metrics Explorer et définir des alertes pour détecter les anomalies     | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |
|                   | Superviser et déboguer votre solution Communication Services en activant la journalisation des diagnostics pour votre ressource    | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |


**Le package de signalisation propriétaire est implémenté en utilisant des sockets web. Il passe à l’interrogation longue si les sockets web ne sont pas pris en charge.

## <a name="javascript-chat-sdk-support-by-os-and-browser"></a>Prise en charge du kit SDK Conversation par le système d’exploitation et le navigateur

Le tableau suivant représente l’ensemble des navigateurs pris en charge et des versions actuellement disponibles.

|                                  | Windows          | macOS          | Ubuntu | Linux  | Android | iOS    | iPadOS|
|--------------------------------|----------------|--------------|-------|------|------|------|-------|
| **Kit SDK Conversation** | Firefox *, Chrome*, nouveau Edge | Firefox *, Chrome*, Safari* | Chrome*  | Chrome* | Chrome* | Safari* | Safari* |

*Notez que la dernière version est prise en charge en plus des deux versions précédentes.<br/>

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Bien démarrer avec les conversations](../../quickstarts/chat/get-started.md)

Les documents suivants peuvent vous intéresser :
- Familiarisez-vous avec les [concepts relatifs aux conversations](../chat/concepts.md).
- Comprendre comment les [tarifs](../pricing.md#chat) sont appliqués pour les conversations
