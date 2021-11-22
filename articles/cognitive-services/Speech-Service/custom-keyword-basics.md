---
title: Guide de démarrage rapide sur la création de mot clé - Service de reconnaissance vocale
titleSuffix: Azure Cognitive Services
description: Votre appareil est toujours à l’écoute d’un mot (ou d’une phrase) clé. Lorsqu’un utilisateur prononce le mot clé, votre appareil envoie sa diction au cloud, jusqu’à ce que l’utilisateur cesse de parler. Pour différencier votre appareil et renforcer votre marque, vous pouvez personnaliser votre mot clé.
services: cognitive-services
author: eric-urban
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/12/2021
ms.author: eur
ms.custom: devx-track-csharp, ignite-fall-2021
zone_pivot_groups: keyword-quickstart
ms.openlocfilehash: 59756ec624bfc4d716bcf222195f53029be05420
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132493180"
---
# <a name="get-started-with-custom-keyword"></a>Bien démarrer avec Mot clé personnalisé

Dans ce guide de démarrage rapide, vous allez apprendre les bases de l’utilisation de mots clés personnalisés. Un mot clé est un mot ou une expression courte qui permet d’activer la fonction vocale de votre produit. Vous créez des modèles de mots clés dans Speech Studio. Exportez ensuite un fichier de modèle que vous utilisez avec le kit de développement logiciel (SDK) Speech dans vos applications.

## <a name="prerequisites"></a>Prérequis

Les étapes décrites dans cet article nécessitent un abonnement Speech et le kit SDK Speech. Si vous n’avez pas encore d’abonnement, [essayez gratuitement le service Speech](overview.md#try-the-speech-service-for-free). Pour obtenir le kit SDK, consultez le [guide d’installation](quickstarts/setup-platform.md) pour votre plateforme.

## <a name="create-a-keyword-in-speech-studio"></a>Créer un mot clé dans Speech Studio

Avant de pouvoir utiliser un mot clé personnalisé, vous devez créer ce mot clé dans la page [Mot clé personnalisé](https://aka.ms/sdsdk-wakewordportal), dans [Speech Studio](https://aka.ms/sdsdk-speechportal). Une fois que vous avez fourni un mot clé, le logiciel génère un fichier `.table` que vous pouvez utiliser avec le kit SDK Speech.

> [!IMPORTANT]
> Les modèles de mots clés personnalisés et les fichiers `.table` résultants peuvent **uniquement** être créés dans Speech Studio.
> Vous ne pouvez pas créer de mots clés personnalisés à partir du kit SDK ni avec des appels REST.

1. Connectez-vous à [Speech Studio](https://aka.ms/sdsdk-speechportal), puis cliquez sur **Se connecter**. Si vous n’avez pas d’abonnement Speech, accédez à [**Créer des services vocaux**](https://go.microsoft.com/fwlink/?linkid=2086754).

1. Sur la page [Mot clé personnalisé](https://aka.ms/sdsdk-wakewordportal), sélectionnez **Créer un projet**. 

1. Entrez un **nom**, une **description** et une **langue** pour votre projet de mot clé personnalisé. Vous ne pouvez choisir qu’une seule langue par projet, et la prise en charge est actuellement limitée à l’anglais (États-Unis) et au chinois (mandarin, simplifié). 

    ![Décrire votre projet de mot clé](media/custom-keyword/custom-kw-portal-new-project.png)

1. Sélectionnez le nom de votre projet dans la liste. 

    :::image type="content" source="media/custom-keyword/custom-kw-portal-project-list.png" alt-text="Sélectionnez votre projet de mot clé.":::

1. Pour créer un mot clé personnalisé pour votre assistant virtuel, sélectionnez **Créer un modèle**.

1. Entrez un **nom** pour le modèle, une **description** et le **mot clé** de votre choix, puis sélectionnez **Suivant**. Consultez les [instructions](keyword-recognition-guidelines.md#choosing-an-effective-keyword) sur le choix d’un mot clé efficace.

    ![Entrez votre mot clé](media/custom-keyword/custom-kw-portal-new-model.png)

1. Le portail crée des propositions de prononciation pour votre mot clé. Écoutez chaque proposition en sélectionnant les boutons de lecture et supprimez les coches en regard des prononciations incorrectes. Sélectionnez toutes les prononciations correspondant à la façon dont vous pensez que vos utilisateurs disent le mot clé, puis sélectionnez **Suivant** pour commencer à générer le modèle de mot clé. 

    :::image type="content" source="media/custom-keyword/custom-kw-portal-choose-prons.png" alt-text="Capture d’écran montrant où vous choisissez les bonnes prononciations.":::

1. Sélectionnez un type de modèle, puis choisissez **Créer**. Vous pouvez afficher la liste des régions qui prennent en charge le type de modèle **Avancé** dans la documentation de [prise en charge dans les régions de la reconnaissance des mots clés](keyword-recognition-region-support.md). 

1. La génération du modèle peut prendre jusqu’à 30 minutes. La liste de mots clés passe de **Traitement en cours** à **Terminé** lorsque le modèle est terminé. 

    :::image type="content" source="media/custom-keyword/custom-kw-portal-review-keyword.png" alt-text="Vérifiez votre mot clé.":::

1. Dans le menu réductible sur la gauche, sélectionnez **Régler** pour accéder aux options pour régler et télécharger votre modèle. Le fichier téléchargé est une archive `.zip`. Extrayez l’archive et un fichier avec l’extension `.table` apparaît. Si vous utilisez le fichier `.table` avec le kit de développement logiciel (SDK), veillez à noter son chemin d’accès.

    :::image type="content" source="media/custom-keyword/custom-kw-portal-download-model.png" alt-text="Téléchargez votre table de modèle.":::


## <a name="use-a-keyword-model-with-the-speech-sdk"></a>Utiliser un modèle de mot clé avec le SDK Speech

::: zone pivot="programming-language-csharp"
[!INCLUDE [C# Basics include](includes/how-to/keyword-recognition/keyword-basics-csharp.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Python Basics include](includes/how-to/keyword-recognition/keyword-basics-python.md)]
::: zone-end

::: zone pivot="programming-languages-objectivec-swift"
[!INCLUDE [ObjectiveC/Swift Basics include](includes/how-to/keyword-recognition/keyword-basics-objc.md)]
::: zone-end

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Obtenir le kit SDK Speech](speech-sdk.md)
