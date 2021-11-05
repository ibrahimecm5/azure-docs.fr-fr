---
title: Entraîner et déployer un modèle Custom Speech - Service Speech
titleSuffix: Azure Cognitive Services
description: Découvrez comment effectuer l’apprentissage de modèles Custom Speech et déployer ceux-ci. L’apprentissage d’un modèle de reconnaissance vocale peut améliorer l’exactitude de reconnaissance du modèle de référence Microsoft ou d’un modèle personnalisé.
services: cognitive-services
author: eric-urban
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/12/2021
ms.author: eur
ms.custom: ignite-fall-2021
ms.openlocfilehash: a7f0e4f45659d77400f21d6ff056c1b8d5131fca
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131509173"
---
# <a name="train-and-deploy-a-custom-speech-model"></a>Entraîner et déployer un modèle Custom Speech

Cet article explique comment effectuer l’apprentissage de modèles Custom Speech et déployer ceux-ci. L’apprentissage d’un modèle de reconnaissance vocale peut améliorer l’exactitude de reconnaissance du modèle de référence Microsoft. Vous utilisez des transcriptions étiquetées à la main et un texte associé pour effectuer l’apprentissage d’un modèle. Ces jeux de données, associés à des données audio chargées précédemment, permettent d’affiner le modèle de reconnaissance vocale et d’en effectuer l’apprentissage.

## <a name="use-training-to-resolve-accuracy-problems"></a>Utiliser un apprentissage pour résoudre les problèmes d’exactitude

Si vous rencontrez des problèmes de reconnaissance avec un modèle de base, vous pouvez utiliser des transcriptions étiquetées à la main et des données associées pour effectuer l’apprentissage d’un modèle personnalisé afin d’améliorer l’exactitude. Servez-vous de ce tableau pour déterminer le jeu de données à utiliser pour résoudre vos problèmes :

| Cas d’utilisation | Type de données |
| -------- | --------- |
| Améliorez l’exactitude de reconnaissance du vocabulaire ou de la grammaire spécifiques d’un secteur, par exemple, de la terminologie médicale ou du jargon informatique. | Données de texte brut ou de texte structuré |
| Définissez les formes phonétique et affichée d’un mot ou d’un terme dont la prononciation n’est pas standard, par exemple, de noms de produits ou d’acronymes. | Données de prononciation ou prononciation phonétique dans du texte structuré |
| Améliorez la précision de la reconnaissance des styles de discours, des accents ou des bruits de fond spécifiques | Transcriptions audio + étiquetées à la main |

## <a name="train-and-evaluate-a-model"></a>Entraîner et évaluer un modèle

La première étape pour entraîner un modèle consiste à charger des données d’entraînement. Consultez [Préparer et tester les données](./how-to-custom-speech-test-and-train.md) pour obtenir des instructions pas à pas sur la manière de préparer des transcriptions étiquetées à la main et du texte associé (énoncés et prononciations). Après avoir chargé des données d’apprentissage, suivez les instructions suivantes pour commencer à effectuer l’apprentissage de votre modèle :

1. Connectez-vous au [portail Custom Speech](https://speech.microsoft.com/customspeech). Si vous envisagez d’effectuer l’apprentissage d’un modèle avec des jeux de données de transcription étiquetée à la main + audio, choisissez un abonnement Speech situé dans une [région avec un matériel dédié](custom-speech-overview.md#set-up-your-azure-account) pour l’apprentissage.
2. Accédez à **Synthèse vocale** > **Custom Speech** >  **[nom du projet]**  > **Formation**.
3. Sélectionnez **Effectuer l’apprentissage du modèle**.
4. Attribuez à votre apprentissage un **Nom** et une **Description**.
5. Dans la liste **Scénario et modèle de référence**, sélectionnez le scénario qui correspond le mieux à votre domaine. Si hésitez sur le choix du scénario, sélectionnez **Général**. Le modèle de référence est le point de départ de votre entraînement. Le modèle le plus récent est généralement le meilleur choix.
6. Dans la page **Sélectionner les données d’entraînement**, choisissez un ou plusieurs jeux de données de transcription étiquetée à la main + audio à utiliser pour l’apprentissage.

> [!NOTE]
> Quand vous entraînez un nouveau modèle, commencez par le texte associé. L’entraînement avec transcription étiquetée à la main + audio peut prendre plus de temps **(jusqu’à [plusieurs jours](how-to-custom-speech-evaluate-data.md#add-audio-with-human-labeled-transcripts)** ).

> [!NOTE]
> Tous les modèles de base ne prennent pas en charge l’audio. Si un modèle de base ne le prend pas en charge, le service vocal utilise uniquement le texte des transcriptions et ignore l’audio. Pour obtenir la liste des modèles de base prenant en charge l’entraînement avec des données audio, consultez les informations relatives à la [prise en charge des langues](language-support.md#speech-to-text).

> [!NOTE]
> Si vous changez le modèle de base utilisé pour l’entraînement et si vous avez du contenu audio dans le jeu de données d’entraînement, vérifiez *toujours* si le nouveau modèle de base sélectionné [prend en charge l’entraînement avec des données audio](language-support.md#speech-to-text). Si le modèle de base utilisé jusqu’à maintenant ne prend pas en charge l’entraînement avec des données audio, et si le jeu de données d’entraînement contient de l’audio, le temps d’entraînement du nouveau modèle de base va **considérablement** augmenter. Il peut facilement passer de plusieurs heures à plusieurs jours, voire davantage. Cela est particulièrement vrai si votre abonnement au service Speech ne se situe **pas** dans une [région disposant du matériel dédié](custom-speech-overview.md#set-up-your-azure-account) à l’entraînement.
>
> Si vous êtes confronté au problème décrit dans le paragraphe ci-dessus, vous pouvez rapidement faire baisser le temps d’entraînement en réduisant la quantité du contenu audio dans le jeu de données, ou en supprimant complètement le contenu audio pour ne garder que le texte. Cette dernière option est fortement recommandée si votre abonnement au service Speech ne se situe **pas** dans une [région disposant du matériel dédié](custom-speech-overview.md#set-up-your-azure-account) à l’entraînement.

7. Une fois l’apprentissage du modèle terminé, vous pouvez effectuer des tests d’exactitude sur celui-ci. Cette étape est facultative.
8. Sélectionnez **Create** pour générer votre modèle personnalisé.

La table **Formation** affiche une nouvelle entrée correspondant au nouveau modèle. Elle indique également l’état : **En cours de traitement**, **Réussite** ou **Échec**.

Consultez le [Guide pratique](how-to-custom-speech-evaluate-data.md) sur l’évaluation et l’amélioration de la justesse du modèle Custom Speech. Si vous choisissez de tester l’exactitude, il est important de sélectionner un jeu de données acoustique différent de celui que vous avez utilisé avec votre modèle pour vous faire une idée réaliste des performances du modèle.

> [!NOTE]
> Les modèles de base et les modèles personnalisés ne peuvent être utilisés que jusqu’à une certaine date (voir [Cycle de vie des modèles et des points de terminaison](./how-to-custom-speech-model-and-endpoint-lifecycle.md)). Speech Studio affiche cette date dans la colonne **Expiration** de chaque modèle et point de terminaison. Après cette demande, la demande envoyée à un point de terminaison ou à une transcription par lots peut échouer ou revenir au modèle de base.
>
> Réentraînez votre modèle à l’aide du modèle de base le plus récent pour tirer parti des améliorations de précision et pour éviter que votre modèle expire.

## <a name="deploy-a-custom-model"></a>Déployer un modèle personnalisé

Après avoir chargé et inspecté les données, évalué l’exactitude et effectué l’apprentissage d’un modèle personnalisé, vous pouvez déployer un point de terminaison personnalisé à utiliser avec vos applications, outils et produits. 

Pour créer un point de terminaison personnalisé, connectez-vous au [portail Custom Speech](https://speech.microsoft.com/customspeech). Sélectionnez **Déploiement** dans le menu **Custom Speech** en haut de la page. S’il s’agit de votre première exécution, vous remarquerez que la table ne contient aucun point de terminaison. Une fois que vous avez créé un point de terminaison, vous utilisez cette page pour effectuer le suivi de chaque point de terminaison déployé.

Ensuite, sélectionnez **Ajouter un point de terminaison** et entrez un **nom** et une **description** pour votre point de terminaison personnalisé. Sélectionnez ensuite le modèle personnalisé que vous souhaitez associer au point de terminaison.  Vous pouvez également activer la journalisation à partir de cette page. La journalisation vous permet de surveiller le trafic du point de terminaison. Si elle est désactivée, le trafic n’est pas enregistré.

![Capture d’écran montrant la page Nouveau point de terminaison.](./media/custom-speech/custom-speech-deploy-model.png)

> [!NOTE]
> N’oubliez pas d’accepter les conditions d’utilisation et la tarification.

Ensuite, sélectionnez **Créer**. Cette action vous renvoie à la page **Déploiement**. La table inclut désormais une entrée qui correspond à votre point de terminaison personnalisé. L’état du point de terminaison indique son état actuel. L’instanciation d’un nouveau point de terminaison avec vos modèles personnalisés peut prendre jusqu’à 30 minutes. Lorsque l’état du déploiement est **Complete**, le point de terminaison est prêt à être utilisé.

Une fois le point de terminaison déployé, son nom s’affiche sous forme de lien. Sélectionnez ce lien pour afficher les informations spécifiques de votre point de terminaison, telles que sa clé, son URL et un exemple de code. Prenez note de la date d’expiration et mettez à jour le modèle du point de terminaison avant cette date pour garantir un service continu.

## <a name="view-logging-data"></a>Afficher les données de journalisation

Les données de journalisation peuvent être exportées en accédant à la page du point de terminaison sous **Déploiements**.
> [!NOTE]
>Les données de journalisation sont disponibles pendant 30 jours sur le stockage détenu par Microsoft. Ensuite, elles seront supprimées. Si un compte de stockage appartenant à un client est lié à l’abonnement Cognitive Services, les données de journalisation ne seront pas automatiquement supprimées.

## <a name="next-steps"></a>Étapes suivantes

* [Découvrez comment utiliser votre modèle personnalisé](how-to-specify-source-language.md)

## <a name="additional-resources"></a>Ressources supplémentaires

- [Préparer et tester les données](./how-to-custom-speech-test-and-train.md)
- [Inspecter les données](how-to-custom-speech-inspect-data.md)
- [Évaluer les données](how-to-custom-speech-evaluate-data.md)
