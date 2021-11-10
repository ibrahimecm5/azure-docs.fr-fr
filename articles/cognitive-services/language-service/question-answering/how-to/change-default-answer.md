---
title: 'Recevoir la réponse par défaut : réponses aux questions personnalisées'
description: La réponse par défaut est retournée quand il n’existe aucune correspondance avec la question. Vous souhaiterez peut-être modifier la réponse par défaut dans vos réponses aux questions personnalisées.
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: how-to
ms.date: 11/02/2021
author: mrbullwinkle
ms:author: mbullwin
ms.custom: language-service-question-answering, ignite-fall-2021
ms.openlocfilehash: 44f2ba3d3a1ae3e05cace797349a2680584a9aff
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131476563"
---
# <a name="change-default-answer-for-question-answering"></a>Modifier la réponse par défaut pour les réponses aux questions

La réponse par défaut d’un projet doit être retournée quand aucune réponse n’a été trouvée. Si vous utilisez une application cliente, telle que le [service Azure Bot](/azure/bot-service/bot-builder-howto-qna), celle-ci peut également avoir sa propre réponse par défaut, indiquant qu’aucune réponse n’a atteint le seuil de score.

## <a name="default-answer"></a>Réponse par défaut


|Réponse par défaut|Description de la réponse|
|--|--|
|Réponse de la base de connaissances lorsqu’aucune réponse n’est déterminée|`No good match found in KB.`Lorsque l’API de réponses aux questions ne trouve aucune réponse correspondante à la question, elle affiche une réponse textuelle par défaut. Dans les réponses aux questions, vous pouvez définir ce texte dans les **Paramètres** de votre projet. |

### <a name="client-application-integration"></a>Intégration d’applications clientes

Pour une application cliente, telle qu’un bot avec le **service bot Azure**, vous pouvez choisir parmi les scénarios courants suivants :

* Utiliser le paramètre de votre projet/de la base de connaissances
* Utiliser un autre texte dans l’application cliente pour distinguer les cas où une réponse est retournée, mais n’atteint pas le seuil de score. Ce texte peut être du texte statique stocké dans le code ou il peut être stocké dans la liste des paramètres de l’application cliente.

## <a name="change-default-answer-in-language-studio"></a>Modifier la réponse par défaut dans Language Studio

La réponse par défaut de la base de connaissances est retournée quand aucune réponse n’est retournée à partir du service réponses aux questions.

1. Connectez-vous au [Language Studio](https://language.azure.com) et sélectionnez votre projet dans la liste.
1. Dans la barre de navigation gauche, sélectionnez **Paramètres**.
1. Modifiez la valeur de la **Réponse par défaut quand aucune réponse n’a été trouvée** > sélectionnez **Enregistrer**.

> [!div class="mx-imgBorder"]
> ![Capture d’écran des paramètres de projet avec une zone rouge autour de la réponse par défaut](../media/change-default-answer/settings.png)

## <a name="next-steps"></a>Étapes suivantes

* [Créer une base de connaissances](manage-knowledge-base.md)
