---
title: Présentation de Reconnaissance de l’orateur – Service Speech
titleSuffix: Azure Cognitive Services
description: Le service Reconnaissance de l’orateur fournit des algorithmes qui vérifient et identifient les orateurs d’après leurs caractéristiques vocales propres en utilisant la biométrie vocale. Le service Reconnaissance de l’orateur est utilisé pour répondre à la question « qui parle ? ». Cet article est une présentation des avantages et des capacités du service Reconnaissance de l’orateur.
services: cognitive-services
author: eric-urban
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/02/2020
ms.author: eur
ms.custom: cog-serv-seo-aug-2020, ignite-fall-2021
keywords: speaker recognition, voice biometry
ms.openlocfilehash: 4096e04678c3ce0b10ac54a190f3074525469b54
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131509454"
---
# <a name="what-is-speaker-recognition"></a>Qu’est-ce que la reconnaissance de l’orateur ?

La fonction Reconnaissance de l’orateur est utilisée pour déterminer qui parle dans un clip audio. Le service peut vérifier et identifier les intervenants par leurs caractéristiques vocales uniques à l’aide de la biométrie vocale. Vous fournissez des données d’entraînement audio pour un seul orateur, ce qui crée un profil d’inscription basé sur les caractéristiques uniques de la voix de l’orateur. Vous pouvez ensuite effectuer une vérification croisée des échantillons audio de la voix par rapport à ce profil pour vérifier que l’orateur est la même personne (vérification de l’orateur) ou par rapport à un *groupe* de profils d’orateur inscrits, pour voir s’ils correspondent à un profil du groupe (identification de l’orateur).

> [!IMPORTANT]
> Microsoft limite l’accès à Reconnaissance de l’orateur. Demandez à l'utiliser par le biais de l'examen d'accès limité d'[Azure Cognitive Services Speaker Recognition](https://aka.ms/azure-speaker-recognition). Après approbation, vous pouvez accéder aux API Reconnaissance de l’orateur. Pour plus d’informations, consultez [Accès limité pour Reconnaissance de l’orateur](/legal/cognitive-services/speech-service/speaker-recognition/limited-access-speaker-recognition).

## <a name="speaker-verification"></a>Vérification de l’orateur

La vérification de l’orateur simplifie le processus de vérification de l’identité d’un orateur inscrit avec des phrases secrètes ou une entrée vocale de forme libre. Elle peut servir à vérifier l’identité des individus pour assurer la sécurité et le bon déroulement des engagements clients dans une variété de solutions (de la vérification d’identité des clients dans les centres d’appels à l’accès aux installations sans contact).

### <a name="how-does-speaker-verification-work"></a>Comment fonctionne le service Vérification de l’orateur ?

:::image type="content" source="media/speaker-recognition/speaker-rec.png" alt-text="Organigramme de vérification de l'orateur.":::

La vérification de l’orateur peut dépendre ou non du texte. La vérification **dépendante du texte** signifie que les orateurs doivent choisir une phrase secrète qu’ils utilisent dans les phases d’inscription et de vérification. La vérification **indépendante du texte** signifie que les orateurs peuvent utiliser le langage de tous les jours dans les phases d’inscription et de vérification.

Pour la vérification **dépendante du texte**, la voix de l’orateur est inscrite en prononçant une phrase secrète choisie dans un ensemble d’expressions prédéfinies. Les caractéristiques de la voix sont extraites de l’enregistrement audio pour constituer une signature vocale unique, activée dès que la phrase secrète choisie par l’utilisateur est reconnue. Conjointement, la signature vocale et la phrase secrète sont utilisées pour vérifier l’identité de l’orateur. 

La vérification **indépendante du texte** n’a pas de restrictions sur ce que l’orateur affirme au cours de l’inscription, en plus de la phrase d’activation initiale pour activer l’inscription. Il n’a aucune restriction sur l’échantillon audio à vérifier, car il extrait uniquement les fonctionnalités vocales pour noter la similarité. 

Les API ne permettent pas de déterminer si l’audio provient d’une personne en direct, ou s’il s’agit d’une imitation ou d’un enregistrement de la voix d’un utilisateur inscrit. 

## <a name="speaker-identification"></a>Identification de l’orateur

Identification de l’orateur est utilisé pour déterminer l’identité d’un orateur inconnu au sein d’un groupe d’orateurs inscrits. Identification de l’orateur vous permet d’attribuer un discours à des orateurs individuels et de déverrouiller la valeur dans des scénarios avec plusieurs orateurs, par exemple :

* Prendre en charge des solutions pour la productivité des réunions à distance 
* Créer une personnalisation sur les appareils multiutilisateurs

### <a name="how-does-speaker-identification-work"></a>Comment fonctionne le service Identification de l’orateur ?

L’inscription à l’identification de l’orateur est **indépendante du texte**, ce qui signifie qu’aucune restriction ne s’applique au texte prononcé par l’orateur dans l’audio., hormis la phase d’activation initiale pour activer l’inscription. Tout comme pour la vérification de l’orateur, la voix de l’orateur est enregistrée dans la phase d’inscription et ses caractéristiques vocales sont extraites pour former une signature vocale unique. Dans la phase d’identification, l’exemple de voix en entrée est comparé à une liste spécifiée de voix inscrites (jusqu’à 50 dans chaque demande).

## <a name="data-security-and-privacy"></a>Sécurité et confidentialité des données

Les données d’inscription de l’orateur sont stockées dans un système sécurisé, y compris le discours audio pour l’inscription et les caractéristiques de la signature vocale. Le discours audio pour l’inscription est utilisé seulement pendant la mise à niveau de l’algorithme, et les caractéristiques doivent être de nouveau extraites. Ce service ne conserve ni l’enregistrement vocal ni les caractéristiques vocales extraites qu’il envoie au service pendant la phase de reconnaissance. 

Vous contrôlez la durée pendant laquelle les données doivent être conservées. Vous pouvez créer, mettre à jour et supprimer les données d’inscription de chaque orateur en effectuant des appels d’API. Lorsqu’une inscription est supprimée, toutes les données d’inscription de l’utilisateur lui étant associées sont aussi supprimées. 

Comme avec toutes les ressources Cognitive Services, les développeurs utilisant le service Reconnaissance de l’orateur doivent connaître les politiques de Microsoft relatives aux données client. Vous devez vérifier que vous avez reçu les autorisations appropriées de la part des utilisateurs du service Reconnaissance de l’orateur. Vous trouverez plus d’informations sur [les données et la confidentialité pour Reconnaissance de l’orateur](/legal/cognitive-services/speech-service/speaker-recognition/data-privacy-speaker-recognition). Pour plus d’informations, consultez la  [page Cognitive Services](https://azure.microsoft.com/support/legal/cognitive-services-compliance-and-privacy/)  dans le Centre de gestion de la confidentialité Microsoft. 

## <a name="common-questions-and-solutions"></a>Questions courantes et solutions

| Question | Solution |
|---------|----------|
| Pour quels scénarios la reconnaissance de l’orateur est-elle utile ? | Centre d’appels de vérification du client, vérification des patients basés sur la voix, transcription des réunions, personnalisation des appareils multi-utilisateurs|
| Quelle est la différence entre l’identification et la vérification ? | L’identification est le processus de détection d’un membre du groupe d’orateurs. La vérification est l’acte qui consiste à confirmer qu’un orateur correspond à une voix **inscrite** (connue).|
| Quelle est la différence entre la vérification dépendante du texte et la vérification indépendante du texte ? | La vérification dépendante du texte requiert une phrase secrète spécifique pour l’inscription et la reconnaissance. La vérification indépendante du texte nécessite un échantillon de voix plus long qui doit commencer par une expression d’activation particulière pour l’inscription, mais tout peut être parlé, y compris pendant la reconnaissance.|
| Quelles sont les langues prises en charge ? | Anglais, français, espagnol, chinois, allemand, italien, japonais et portugais |
| Quelles sont les régions Azure prises en charge ? | La reconnaissance de l’orateur est un service en version préliminaire, qui est actuellement disponible uniquement dans la région USA Ouest.|
| Quels formats audio sont pris en charge ? | WAV mono 16 bits, 16 kHz encodé en PCM |
| Les fonctionnalités **Accepter** et **Rejeter** les réponses ne sont pas précises, comment ajuster le seuil ? | Étant donné que le seuil optimal varie fortement selon les scénarios, l’API décide s’il faut « Accepter » ou « Rejeter » simplement en fonction du seuil par défaut, soit 0,5. Il est recommandé aux utilisateurs expérimentés de remplacer la décision par défaut et d’affiner le résultat en fonction de votre propre scénario. |
| Pouvez-vous inscrire plusieurs fois un orateur ? | Oui, pour la vérification dépendante du texte, vous pouvez inscrire un orateur jusqu’à 50 fois. Pour une vérification indépendante du texte ou une identification de l’orateur, vous pouvez vous inscrire avec un maximum de 300 secondes d’audio. |
| Quelles données sont stockées dans Azure ? | L’audio des inscriptions est stocké dans le service jusqu’à ce que le profil vocal soit [supprimé](./get-started-speaker-recognition.md#deleting-voice-profile-enrollments). Les échantillons audio de reconnaissance ne sont pas conservés ou stockés. |

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> * Consultez l'[article sur les bases](./get-started-speaker-recognition.md) de la reconnaissance de l’orateur pour obtenir un aperçu des modèles de conception courants que vous pouvez utiliser dans vos applications.
> * Consultez le [tutoriel vidéo](https://azure.microsoft.com/resources/videos/speaker-recognition-text-independent-verification-developer-tutorial/) sur la vérification de l’orateur indépendante du texte.

## <a name="responsible-use-of-ai"></a>Utilisation responsable de l’IA
> [!div class="ResponsibleUseOfAI"]
> * [Note de transparence pour Reconnaissance de l’orateur](/legal/cognitive-services/speech-service/speaker-recognition/transparency-note-speaker-recognition)
> * [Caractéristiques et limitations pour Reconnaissance de l’orateur](/legal/cognitive-services/speech-service/speaker-recognition/characteristics-and-limitations-speaker-recognition)
> * [Accès limité pour Reconnaissance de l’orateur](/legal/cognitive-services/speech-service/speaker-recognition/limited-access-speaker-recognition)
> * [Aide pour l’intégration et l’utilisation responsable avec Reconnaissance de l’orateur](/legal/cognitive-services/speech-service/speaker-recognition/guidance-integration-responsible-use-speaker-recognition)
> * [Données et confidentialité pour la Reconnaissance de l’orateur](/legal/cognitive-services/speech-service/speaker-recognition/data-privacy-speaker-recognition)
