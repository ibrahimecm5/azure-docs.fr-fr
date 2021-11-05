---
title: Guide pratique pour sauvegarder et récupérer des ressources de clients de la reconnaissance vocale
titleSuffix: Azure Cognitive Services
description: Apprenez à préparer les interruptions de service avec Custom Speech et Custom Voice.
services: cognitive-services
author: masaki-itagaki
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/28/2021
ms.author: mitagaki
ms.custom: ignite-fall-2021
ms.openlocfilehash: aff4093863b1813e22fc6050ff4050cf4133bdd6
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131508360"
---
# <a name="back-up-and-recover-speech-customer-resources"></a>Sauvegarder et récupérer des ressources de clients de la reconnaissance vocale

Le service Speech est [disponible dans différentes régions](./regions.md). Les clés d’abonnement de service sont liées à une seule région. Lorsque vous acquérez une clé, vous sélectionnez une région spécifique dans laquelle résident vos données, votre modèle et vos déploiements.

Des jeux de données pour les ressources de données créées par le client, telles que des modèles vocaux personnalisés, des polices de la voix personnalisées et des profils vocaux de reconnaissance de l’orateur, sont également **disponibles uniquement dans la région de déploiement du service**. Ces ressources sont les suivantes :

**Custom Speech**
-   Données audio/texte d’apprentissage
-   Données audio/texte de test
-   Modèles vocaux personnalisés
-   Données de journal

**Custom Voice**
-   Données audio/texte d’apprentissage
-   Données audio/texte de test
-   Polices de la voix personnalisées

**Reconnaissance de l’orateur**
- Audio de l’inscription de l’orateur
- Signature vocale de l’orateur

Bien que certains clients utilisent nos points de terminaison par défaut pour transcrire des voix audio ou standard à des fins de synthèse vocale, les autres clients créent des ressources pour la personnalisation.

Ces ressources étant sauvegardées régulièrement et automatiquement par les référentiels eux-mêmes, **aucune perte de données ne se produit** si une région devient indisponible. Toutefois, vous devez prendre des dispositions pour garantir la continuité du service en cas de panne régionale.

## <a name="how-to-monitor-service-availability"></a>Comment surveiller la disponibilité du service

Si vous utilisez nos points de terminaison par défaut, vous devez configurer votre code client pour surveiller les erreurs et, si les erreurs persistent, être prêt à rediriger vers une autre région de votre choix où vous disposez d’un abonnement au service.

Pour configurer votre client afin de surveiller les erreurs, procédez comme suit :

1.  Recherchez la [liste des points de terminaison disponibles dans notre documentation](./rest-speech-to-text.md).
2.  Sélectionnez un serveur principal et une ou plusieurs régions secondaires/de sauvegarde dans la liste.
3. À partir de Portail Azure, créez des ressources de service Speech pour chaque région.
    -  Si vous avez fixé un quota spécifique, vous pouvez également envisager de fixer le même quota dans les régions de sauvegarde. Pour plus de détails, consultez [Quotas et limites du service Batch](./speech-services-quotas-and-limits.md).

4.  Notez que chaque région a son propre service de jeton STS. Pour la région primaire et toutes les régions de sauvegarde, votre fichier de configuration du client doit disposer des informations suivantes :
    -  Points de terminaison de service Speech régionaux
    -  [Clé d’abonnement régional et code de région](./rest-speech-to-text.md)

5.  Configurez votre code pour surveiller les erreurs de connectivité (en général, les délais d’expiration des connexions et les erreurs d’indisponibilité du service). Voici un exemple de code en C# : [GitHub : ajout d’un exemple montrant un candidat possible pour le basculement de régions](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/fa6428a0837779cbeae172688e0286625e340942/samples/csharp/sharedcontent/console/speech_recognition_samples.cs#L965).

    1.  Étant donné que les réseaux rencontrent des erreurs temporaires, pour les occurrences de problème de connectivité uniques, la suggestion est de réessayer.
    2.  Pour la persistance, redirigez le trafic vers le nouveau service de jeton STS et le point de terminaison du service Speech. Pour la conversion de texte par synthèse vocale, exemple de code de référence : [GitHub : région de commutation vocale publique TTS](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/speech_synthesis_samples.cs#L880).

La récupération des défaillances régionales pour ce type d’utilisation peut être instantanée et avoir un coût très bas. Tout ce qui est nécessaire est le développement de cette fonctionnalité côté client. La perte de données qui se produira en supposant une absence de sauvegarde du flux audio sera minime.

## <a name="custom-endpoint-recovery"></a>Récupération de point de terminaison personnalisé

Les ressources de données, les modèles ou les déploiements dans une région ne peuvent pas être rendus visibles ou accessibles dans une autre.

Vous devez créer des ressources de service Speech dans une région principale et une région secondaire en suivant les mêmes étapes que celles utilisées pour les points de terminaison par défaut.

### <a name="custom-speech"></a>Custom Speech

Le service Custom Speech ne prend pas en charge le basculement automatique. Nous vous suggérons de suivre les étapes suivantes pour préparer le basculement manuel ou automatique implémenté dans votre code client. Au cours de ces étapes, vous allez répliquer des modèles personnalisés dans une région secondaire. Avec cette préparation, votre code client peut basculer vers une région secondaire en cas de défaillance de la région primaire.

1.  Créez votre modèle personnalisé dans une région principale (primaire).
2.  Exécutez l’[API Copie de modèle](https://eastus2.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/CopyModelToSubscription) pour répliquer le modèle personnalisé dans toutes les régions préparées (secondaires).
3.  Accédez à Speech Studio pour charger le modèle copié et créer un nouveau point de terminaison dans la région secondaire. Pour découvrir comment déployer un nouveau modèle, consultez [Entraîner et déployer un modèle Custom Speech](./how-to-custom-speech-train-model.md).
    -  Si vous avez fixé un quota spécifique, envisagez également de fixer le même quota dans les régions de sauvegarde. Pour plus de détails, consultez [Quotas et limites du service Batch](./speech-services-quotas-and-limits.md).
4.  Configurez votre client pour qu’il bascule en cas d’erreurs persistantes comme avec l’utilisation des points de terminaison par défaut.

Votre code client peut surveiller la disponibilité des modèles déployés dans votre région primaire, et rediriger leur trafic audio vers la région secondaire en cas de défaillance de la région primaire. Si vous n’avez pas besoin d’un basculement en temps réel, vous pouvez toujours suivre ces étapes pour préparer un basculement manuel.

#### <a name="offline-failover"></a>Basculement hors connexion

Si vous n’avez pas besoin d’effectuer un basculement en temps réel, vous pouvez décider d’importer vos données, et créer et déployer vos modèles dans la région secondaire ultérieurement, en sachant que l’accomplissement de ces tâches prendra du temps.

#### <a name="failover-time-requirements"></a>Exigences de temps de basculement

Cette section fournit des indications générales sur le calcul du temps. Les temps ont été enregistrés afin d’estimer le basculement hors connexion à l’aide d’un [jeu de données de test représentatif](https://github.com/microsoft/Cognitive-Custom-Speech-Service).

-   Chargement des données dans une nouvelle région : **15 minutes**
-   Création de modèle acoustique/linguistique : **6 heures (en fonction du volume de données)**
-   Évaluation du modèle : **30 minutes**
-   Déploiement du point de terminaison : **10 minutes**
-   Appel d’API Copie de modèle : **10 minutes**
-   Reconfiguration et déploiement du code client : **selon le système client**

Il est néanmoins recommandé de créer des clés pour une région primaire et une région secondaire pour les modèles de production assortis d’exigences en matière de temps réel.

### <a name="custom-voice"></a>Custom Voice

Custom Voice ne prend pas en charge le basculement automatique. Gérez les échecs de synthèse en temps réel avec les deux options suivantes.

**Option 1 : basculer vers une voix publique dans la même région.**

En cas d’échec de la synthèse vocale personnalisée en temps réel, basculez vers une voix publique (exemple de code client : [GitHub : basculement vocal personnalisé vers une voix publique](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/speech_synthesis_samples.cs#L899)).

Vérifiez les [voix publiques disponibles](./language-support.md#neural-voices). Vous pouvez également modifier l’exemple de code ci-dessus si vous souhaitez basculer vers une autre voix ou dans une autre région.

**Option 2 : basculer vers une voix personnalisée sur une autre région.**

1.  Créez et déployez votre voix personnalisée dans une région principale (primaire).
2.  Copiez votre modèle de voix personnalisé vers une autre région (la région secondaire) dans [Speech Studio](https://speech.microsoft.com).
3.  Accédez à Speech Studio et basculez vers la ressource Speech dans la région secondaire. Chargez le modèle copié et créez un nouveau point de terminaison.
    -   Le déploiement du modèle vocal prend généralement **3 minutes**.
    -   Remarque : un point de terminaison supplémentaire occasionne des frais supplémentaires. [Consultez la tarification de l’hébergement de modèle ici](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

4.  Configurez votre client pour basculer vers la région secondaire. Consultez l’exemple de code en C# : [GitHub : basculement de voix personnalisée vers une région secondaire](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/speech_synthesis_samples.cs#L920).

### <a name="speaker-recognition"></a>Reconnaissance de l’orateur

La Reconnaissance de l’orateur utilise des [régions associées Azure](/azure/best-practices-availability-paired-regions) pour basculer automatiquement des opérations. Les inscriptions et les signatures vocales des orateurs sont sauvegardées régulièrement pour éviter toute perte de données et pour qu’elles puissent être utilisées en cas de panne.

Lors d’une panne, le service Reconnaissance de l’orateur bascule automatiquement vers une région associée et utilise les données sauvegardées pour poursuivre le traitement des demandes jusqu’à ce que la région principale soit de nouveau en ligne.
