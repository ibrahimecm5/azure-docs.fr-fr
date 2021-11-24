---
title: Reconnaissance de mot clé - Service Speech
titleSuffix: Azure Cognitive Services
description: Vue d’ensemble des fonctionnalités, caractéristiques et restrictions de la reconnaissance de mot clé à l’aide du SDK Speech.
services: cognitive-services
author: hasyashah
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/30/2021
ms.author: hasshah
ms.custom: devx-track-csharp, ignite-fall-2021
ms.openlocfilehash: a91f3e6e59647d6817f05137e284fbee4400dbd8
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132491783"
---
# <a name="keyword-recognition"></a>Reconnaissance de mot clé

La reconnaissance de mot clé détecte un mot ou une phrase brève dans un flux de données audio. On parle également de détection de mot clé. 

Le cas d’usage le plus courant de la reconnaissance de mot clé est l’activation vocale des assistants virtuels. Par exemple, « Hey Cortana » est le mot clé de l’assistant Cortana. Lors de la reconnaissance du mot clé, une action propre au scénario est effectuée. Pour les scénarios d’assistant virtuel, une action courante est la reconnaissance vocale de l’audio qui suit le mot clé.

En règle générale, les assistants virtuels sont toujours en train d’écouter. La reconnaissance de mot clé joue le rôle d’une limite de confidentialité pour l’utilisateur. Une exigence de mot clé agit comme un obstacle qui empêche l’audio utilisateur non lié de traverser l’appareil local pour atteindre le cloud.

Pour trouver l’équilibre entre précision, latence et complexité de calcul, la reconnaissance de mot clé est implémentée en tant que système en plusieurs étapes. Pour toutes les étapes au-delà de la première, l’audio est traité uniquement si l’étape qui les précède pense avoir reconnu le mot clé en question.

Le système actuel est conçu avec plusieurs étapes qui couvrent la périphérie et le cloud :

![Plusieurs étapes de reconnaissance de mot clé à la périphérie et dans le cloud.](media/custom-keyword/kw-recognition-multi-stage.png)

La précision de la reconnaissance de mot clé se mesure à l’aide des métriques suivantes :
* **Taux d’acceptations correctes** : mesure la capacité du système à reconnaître le mot clé quand il est prononcé par un utilisateur final. Cette métrique est également appelée taux de vrais positifs. 
* **Taux d’acceptations erronées** : mesure la capacité du système à exclure l’audio qui ne correspond pas au mot clé prononcé par un utilisateur final. Cette métrique est également appelée taux de faux positifs.

L’objectif est d’optimiser le taux d’acceptations correctes tout en minimisant le taux d’acceptations erronées. Le système actuel est conçu pour détecter un mot clé ou une expression précédés d’un court silence. La détection d’un mot clé au milieu d’une phrase ou d’un énoncé n’est pas prise en charge.

## <a name="custom-keyword-for-on-device-models"></a>Service Mot clé personnalisé pour des modèles sur appareil

Le [portail Mot clé personnalisé sur Speech Studio](https://speech.microsoft.com/customkeyword) vous permet de générer des modèles de reconnaissance de mot clé qui s’exécutent à la périphérie en spécifiant un mot ou une expression courte. Vous pouvez encore personnaliser votre modèle de mot clé en choisissant les bonnes prononciations.

### <a name="pricing"></a>Tarifs

Aucun coût n’est facturé pour l’utilisation du service Mot clé personnalisé pour générer des modèles, notamment les modèles De base et Avancé. Aucun coût n’est facturé non plus pour l’exécution de modèles sur appareil avec le SDK Speech.

### <a name="types-of-models"></a>Types de modèles

Le service Mot clé personnalisé vous permet de générer deux types de modèles sur appareil pour tout mot clé.

| Type de modèle | Description |
| ---------- | ----------- |
| De base | Adapté aux démonstrations ou prototypages rapides. Les modèles sont générés avec un modèle de base commun et peuvent nécessiter jusqu’à 15 minutes de préparation. Les caractéristiques de précision de ces modèles peuvent ne pas être optimales. |
| Avancé | Adapté à l’intégration des produits. Les modèles sont générés avec adaptation d’un modèle de base commun à l’aide de données d’entraînement simulées pour améliorer les caractéristiques de précision. La préparation de ces modèles peut prendre jusqu’à 48 heures. |

> [!NOTE]
> Vous pouvez afficher la liste des régions qui prennent en charge le type de modèle **Avancé** dans la documentation de [prise en charge dans les régions de la reconnaissance de mot clé](keyword-recognition-region-support.md). 

Aucun de ces types de modèles ne vous oblige à charger des données d’entraînement. Le service Mot clé personnalisé gère entièrement la génération de données et l’entraînement du modèle.

### <a name="pronunciations"></a>Prononciations

Lors de la création d’un modèle, le service Mot clé personnalisé génère automatiquement les prononciations possibles du mot clé fourni. Vous pouvez écouter chaque prononciation et choisir tout ce qui représente fidèlement la manière dont les utilisateurs finaux vont prononcer le mot clé. Toutes les autres prononciations ne doivent pas être sélectionnées.

Il est important de bien réfléchir aux prononciations que vous sélectionnez pour garantir les meilleures caractéristiques de précision. Par exemple, si vous choisissez plus de prononciations que nécessaire, vous risquez d’obtenir des taux d’acceptations erronées plus élevés. Si vous choisissez un nombre insuffisant de prononciations, qui ne couvre pas toutes les variantes attendues, vous risquez d’obtenir des taux d’acceptations correctes plus faibles.

### <a name="testing-models"></a>Test des modèles

Une fois les modèles sur appareil générés par le service Mot clé personnalisé, vous pouvez les tester directement sur le portail. Le portail vous permet de parler directement dans votre navigateur et d’obtenir des résultats de reconnaissance de mot clé.

## <a name="keyword-verification"></a>Vérification du mot clé

Vérification du mot clé est un service cloud qui réduit l’impact des acceptations erronées issues des modèles sur appareil à l’aide de modèles robustes s’exécutant sur Azure. Aucun réglage ni entraînement ne sont nécessaires pour que le service Vérification du mot clé fonctionne avec votre mot clé. Des mises à jour de modèles incrémentielles sont déployées en continu sur le service pour améliorer la précision et la latence, de façon entièrement transparente pour les applications clientes.

### <a name="pricing"></a>Tarifs

Le service Vérification du mot clé est toujours utilisé en combinaison avec le service de reconnaissance vocale. Aucun coût n’est facturé pour l’utilisation du service Vérification du mot clé en plus du coût du service de reconnaissance vocale.

### <a name="keyword-verification-and-speech-to-text"></a>Vérification du mot clé et reconnaissance vocale

Le service Vérification du mot clé est toujours utilisé conjointement au service de reconnaissance vocale. Les deux services s’exécutent en parallèle. Cela signifie que l’audio est envoyé aux deux services à des fins de traitement simultané.

![Traitement en parallèle des services Vérification du mot clé et Reconnaissance vocale.](media/custom-keyword/kw-verification-parallel-processing.png)

L’exécution en parallèle du service Vérification du mot clé et de la reconnaissance vocale offre les avantages suivants :
* **Aucune latence supplémentaire sur les résultats de la reconnaissance vocale** : L’exécution en parallèle signifie que le service Vérification du mot clé n’ajoute aucune latence et que le client reçoit les résultats de la reconnaissance vocale aussi rapidement. Si le service Vérification du mot clé détermine que le mot clé n’était pas présent dans l’audio, le traitement de la reconnaissance vocale s’arrête, ce qui évite tout traitement de reconnaissance vocale inutile. Toutefois, le traitement du modèle cloud et du réseau augmente la latence de l’activation vocale perçue par l’utilisateur. Pour plus d’informations, consultez les [recommandations et instructions](keyword-recognition-guidelines.md).
* **Préfixe de mot clé forcé dans les résultats de la reconnaissance vocale** : Le traitement de la reconnaissance vocale garantit que les résultats envoyés au client sont préfixés avec le mot clé. Cela permet une plus grande précision dans les résultats de la reconnaissance vocale pour ce qui est prononcé après le mot clé.
* **Délai d’expiration accru de la reconnaissance vocale** : En raison de la présence attendue du mot clé en début d’audio, la reconnaissance vocale autorise une pause plus longue allant jusqu’à 5 secondes après le mot clé, avant de déterminer la fin de l’énoncé et l’arrêt du traitement de la reconnaissance vocale. Cela garantit que l’expérience de l’utilisateur final est correctement gérée pour à la fois les commandes intermédiaires ( *\<keyword> \<pause> \<command>* ) et les commandes chaînées ( *\<keyword> \<command>* ). 

### <a name="keyword-verification-responses-and-latency-considerations"></a>Réponses du service Vérification du mot clé et considérations sur la latence

Pour chaque demande qui lui est adressée, le service Vérification du mot clé retourne l’une des deux réponses suivantes : Accepté ou Rejeté. La latence du traitement varie en fonction de la longueur du mot clé et de la longueur du segment audio censé contenir le mot clé. La latence du traitement n’inclut pas le coût réseau entre le client et les services Azure Speech.

| Réponse du service Vérification du mot clé | Description |
| ----------------------------- | ----------- |
| Accepté | Indique que le service a supposé que le mot clé était présent dans le flux audio fourni dans le cadre de la demande. |
| Rejeté | Indique que le service a supposé que le mot clé n’était pas présent dans le flux audio fourni dans le cadre de la demande. |

Les cas rejetés produisent souvent des latences plus élevées, car le service traite plus d’audio que de cas acceptés. Par défaut, le service Vérification du mot clé va traiter un maximum de deux secondes d’audio pour rechercher le mot clé. Si le mot clé est déterminé absent dans les deux secondes, le service expire et signale une réponse rejetée au client.

### <a name="using-keyword-verification-with-on-device-models-from-custom-keyword"></a>Utilisation du service Vérification du mot clé avec des modèles sur appareil issus du service Mot clé personnalisé

Le SDK Speech facilite l’utilisation de modèles sur appareil générés à l’aide du service Mot clé personnalisé avec les services Vérification du mot clé et Reconnaissance vocale. Il gère de manière transparente ce qui suit :
* Régulation de l’audio vers les services Vérification du mot clé et Reconnaissance vocale en fonction du résultat du modèle sur appareil.
* Communication du mot clé au service Vérification du mot clé.
* Communication de toutes les métadonnées supplémentaires au cloud pour l’orchestration du scénario de bout en bout. 

Vous n’avez pas besoin de spécifier explicitement des paramètres de configuration. Toutes les informations nécessaires sont automatiquement extraites du modèle sur appareil généré par le service Mot clé personnalisé.

Les échantillons et tutoriels dont les liens sont donnés ci-dessous montrent comment utiliser le SDK Speech :
 * [Exemples d’assistant vocal sur GitHub](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant)
 * [Tutoriel : Activation vocale de votre assistant créé à l’aide d’Azure Bot Service avec le kit de développement logiciel (SDK) Speech C#](./tutorial-voice-enable-your-bot-speech-sdk.md)
 * [Tutoriel : Création d’une application Commandes personnalisées avec des commandes vocales simples](./how-to-develop-custom-commands-application.md)

## <a name="speech-sdk-integration-and-scenarios"></a>Scénarios et intégration du SDK Speech

Le SDK Speech facilite l’utilisation de modèles de reconnaissance de mots clés sur appareil personnalisés générés avec le service Mot clé personnalisé et le service Vérification du mot clé. Pour veiller à satisfaire les besoins de votre produit, le SDK prend en charge deux scénarios :

| Scénario | Description | Exemples |
| -------- | ----------- | ------- |
| Reconnaissance de mot clé de bout en bout avec reconnaissance vocale | Adapté aux produits qui vont utiliser un modèle de mot clé sur appareil personnalisé issu du service Mot clé personnalisé avec les services Vérification du mot clé et Reconnaissance vocale d’Azure Speech. Il s’agit du scénario le plus courant. | <ul><li>[Exemple de code d’assistant vocal.](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant)</li><li>[Tutoriel : Activation vocale de votre assistant créé à l’aide d’Azure Bot Service avec le SDK Speech C#.](./tutorial-voice-enable-your-bot-speech-sdk.md)</li><li>[Tutoriel : Création d’une application Commandes personnalisées avec des commandes vocales simples.](./how-to-develop-custom-commands-application.md)</li></ul> |
| Reconnaissance de mot clé hors connexion | Adapté aux produits sans connectivité réseau qui vont utiliser un modèle de mot clé sur appareil personnalisé issu du service Mot clé personnalisé. | <ul><li>[Exemple C# sur la plateforme Windows universelle.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/csharp/uwp/keyword-recognizer)</li><li>[Exemple Java sur Android.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/android/keyword-recognizer)</li></ul>

## <a name="next-steps"></a>Étapes suivantes

* [Lisez le guide de démarrage rapide pour générer des modèles de reconnaissance de mot clé sur appareil à l’aide du service Mot clé personnalisé.](custom-keyword-basics.md)
* [Découvrez-en plus sur les assistants vocaux.](voice-assistants.md)
