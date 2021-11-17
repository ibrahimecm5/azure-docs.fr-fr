---
title: Recommandations et instructions relatives à la reconnaissance de mot clé - Service Speech
titleSuffix: Azure Cognitive Services
description: Vue d’ensemble des recommandations et instructions relatives à l’utilisation de la reconnaissance de mot clé.
services: cognitive-services
author: hasyashah
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/30/2021
ms.author: hasshah
ms.custom: devx-track-csharp
ms.openlocfilehash: a6b7a9c072951a7010403c9e54dff844eb910d95
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132490657"
---
# <a name="recommendations-and-guidelines-for-keyword-recognition"></a>Recommandations et instructions pour la reconnaissance de mot clé

Cet article explique comment choisir votre mot clé pour optimiser ses caractéristiques de précision et comment concevoir vos expériences utilisateur avec le service Vérification du mot clé. 

## <a name="choosing-an-effective-keyword"></a>Choix d’un mot clé efficace

Il est essentiel de créer un mot clé efficace pour que votre produit réponde bien avec logique et précision. Vous devez respecter les instructions suivantes lors du choix d’un mot clé.

> [!NOTE]
> Les exemples ci-dessous sont en anglais, mais les instructions s’appliquent à toutes les langues prises en charge par le service Mot clé personnalisé. Pour obtenir la liste de toutes les langues prises en charge, consultez [Prise en charge linguistique](language-support.md#custom-keyword-and-keyword-verification).

- Le/la prononcer ne doit pas prendre plus de deux secondes.
- Il est préférable d’utiliser un mot de quatre à sept syllabes. Par exemple, « Hey, Computer » est un bon mot clé, ce qui n’est pas le cas du mot « Hey » seul.
- Les mots clés doivent suivre des règles de prononciation communes propres à la langue maternelle de vos utilisateurs finaux.
- Un mot unique voire même inventé qui suit les règles de prononciation communes peut réduire les faux positifs. Par exemple, « computerama » pourrait être un bon mot clé.
- Ne choisissez pas un mot courant, comme « eat » et « go » qui sont prononcés très fréquemment dans les conversations. De tels mots peuvent entraîner un taux d’acceptations erronées plus élevé que voulu pour votre produit.
- Évitez d’utiliser un mot clé qui a plusieurs prononciations. Sinon, les utilisateurs devront connaître la « bonne » prononciation pour activer la reconnaissance vocale de leur produit. Par exemple, « 509 » peut se prononcer « five zero nine », « five oh nine » ou « five hundred and nine ». « R.E.I. » peut être prononcé « r-e-i » ou « ray ». « Live » peut être prononcé « /līv/ » ou « /liv/ ».
- N’utilisez pas de caractères spéciaux, de symboles ou de chiffres. Par exemple, « Go# » et « 20+ cats » peuvent être des mots clés problématiques. Cependant, « go sharp » ou « twenty plus cats » peuvent fonctionner. Pour rendre évidente la bonne prononciation, vous pouvez utiliser les symboles de votre marque et vous servir du marketing et de la documentation.


## <a name="user-experience-recommendations-with-keyword-verification"></a>Recommandations relatives à l’expérience utilisateur avec le service Vérification du mot clé

Avec un scénario de reconnaissance de mot clé en plusieurs étapes dans lequel le service [Vérification du mot clé](keyword-recognition-overview.md#keyword-verification) est utilisé, les applications peuvent choisir à quel moment l’utilisateur final est averti d’une détection de mot clé. Pour le rendu d’un indicateur visuel ou auditif, il est recommandé de s’appuyer sur les réponses faites par le service Vérification du mot clé :

![Instruction pour l’expérience utilisateur lors de l’optimisation de la précision.](media/custom-keyword/kw-verification-ux-accuracy.png)

Ainsi, une expérience optimale est garantie en termes de précision pour réduire l’impact perçu par l’utilisateur des acceptations erronées, mais une latence plus importante est induite.

Les applications qui nécessitent une optimisation de la latence peuvent fournir des indicateurs légers et discrets à l’utilisateur final basés sur la reconnaissance de mot clé sur appareil. Par exemple, le clignotement d’une LED ou la pulsation d’une icône. Les indicateurs peuvent continuer à exister si le service Vérification du mot clé répond par une acceptation ou être masqués si la réponse est un rejet :

![Instruction pour l’expérience utilisateur lors de l’optimisation de la latence.](media/custom-keyword/kw-verification-ux-latency.png)

## <a name="next-steps"></a>Étapes suivantes

* [Obtenir le SDK Speech.](speech-sdk.md)
* [En savoir plus sur les assistants vocaux.](voice-assistants.md)
