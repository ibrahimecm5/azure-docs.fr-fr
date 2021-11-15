---
title: Exemples Media Services v3
description: Cet article contient une liste de tous les exemples disponibles pour Media Services v3, classés par méthode et par SDK.  Les exemples incluent .NET, Node.JS, Python et Java ainsi que REST avec Postman.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: overview
ms.date: 03/24/2021
ms.author: inhenkel
ms.openlocfilehash: 1f7397423eef2969e3a3e61b2201770a1e54a2ed
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131438345"
---
# <a name="media-services-v3-samples"></a>Exemples Media Services v3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Cet article contient une liste de tous les exemples disponibles pour Media Services, classés par méthode et par SDK.  Les exemples incluent .NET, Node.js (TypeScript), Python et Java ainsi que REST avec Postman.

## <a name="samples-by-sdk"></a>Exemples par SDK

Vous trouverez une description et des liens vers les exemples que vous pouvez rechercher dans chacun des onglets.

## <a name="net"></a>[.NET](#tab/net/)

| Exemple | Description |
|-------------|-------------|
| [Account/CreateAccount](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/Account/CreateAccount)|Cet exemple montre comment créer un compte Media Services et définir le compte de stockage principal, en plus des paramètres de configuration avancés, parmi lesquels la liste d’adresses IP autorisées pour la remise de clés, l’identité managée, l’authentification de stockage et la mise en place de votre propre clé de chiffrement.|
| [VideoEncoding/Encoding_PredefinedPreset](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/Encoding_PredefinedPreset)|Cet exemple montre comment envoyer un travail en utilisant un préréglage intégré et une entrée d’URL HTTP, publier un actif multimédia de sortie pour le streaming et télécharger les résultats à des fins de vérification.|
| [VideoEncoding/Encoding_H264_ContentAware](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/Encoding_H264_ContentAware) | Montre l’utilisation la plus simple de l’encodage sensible au contenu H.264 sans aucune contrainte |
| [VideoEncoding/Encoding_H264_ContentAware_Constrained](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/Encoding_H264_ContentAware_Constrained) | Montre comment utiliser la classe PresetConfigurations pour contraindre le comportement de sortie du préréglage|
| [VideoEncoding/Encoding_H264](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/Encoding_H264)|Cet exemple montre comment envoyer un travail en utilisant un préréglage d’encodage H.264 personnalisé et une entrée d’URL HTTP, publier un actif multimédia de sortie pour le streaming et télécharger les résultats à des fins de vérification.|
| [VideoEncoding/Encoding_HEVC_ContentAware](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/Encoding_HEVC_ContentAware) | Montre une utilisation de base du codec HEVC avec l’encodage sensible au contenu et sans aucune contrainte.  La classe PresetConfigurations est également prise en charge pour HEVC et peut être ajoutée à cet exemple|
| [VideoEncoding/Encoding_HEVC](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/Encoding_HEVC)|Cet exemple montre comment envoyer un travail en utilisant un préréglage d’encodage HEVC personnalisé et une entrée d’URL HTTP, publier un actif multimédia de sortie pour le streaming et télécharger les résultats à des fins de vérification.|
| [VideoEncoding/Encoding_StitchTwoAssets](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/Encoding_StitchTwoAssets)|Cet exemple montre comment envoyer un travail en utilisant JobInputSequence pour assembler 2 actifs multimédia ou plus qui peuvent être découpés par heure de début ou de fin. Le fichier encodé obtenu est une vidéo unique avec tous les actifs multimédia assemblés.  L’exemple publiera également un actif multimédia de sortie pour le streaming et téléchargera les résultats à des fins de vérification.|
| [VideoEncoding/Encoding_SpriteThumbnail](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/Encoding_SpriteThumbnail)|Cet exemple montre comment envoyer un travail en utilisant un préréglage personnalisé avec un sprite de miniature et une entrée d’URL HTTP, publier un actif multimédia de sortie pour le streaming et télécharger les résultats à des fins de vérification.|
| [Live/LiveEventWithDVR](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/Live/LiveEventWithDVR)|Cet exemple montre d’abord comment créer un événement LiveEvent avec une archive complète jusqu’à 25 heures et un filtre sur la ressource avec 5 minutes de fenêtre DVR, puis comment utiliser le filtre pour créer un localisateur pour le streaming.|
| [VideoAnalytics/VideoAnalyzer](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoAnalytics/VideoAnalyzer)|Cet exemple montre comment créer une transformation de l’analyseur vidéo, charger un fichier vidéo sur un actif multimédia d’entrée, envoyer un travail avec la transformation et télécharger les résultats à des fins de vérification.|
| [AudioAnalytics/AudioAnalyzer](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/AudioAnalytics/AudioAnalyzer)|Cet exemple montre comment créer une transformation de l’analyseur audio, charger un fichier multimédia sur un actif multimédia d’entrée, envoyer un travail avec la transformation et télécharger les résultats à des fins de vérification.|
| [ContentProtection/BasicAESClearKey](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/ContentProtection/BasicAESClearKey)|Cet exemple montre comment créer une transformation avec un préréglage AdaptiveStreaming intégré, envoyer un travail, créer une stratégie ContentKeyPolicy à l’aide d’une clé secrète, l’associer à StreamingLocator, obtenir un jeton et imprimer une URL pour la lecture dans Lecteur multimédia Azure. Lorsqu’un lecteur demande un flux, Media Services utilise la clé spécifiée pour chiffrer dynamiquement votre contenu avec AES-128 et Azure Media Player utilise le jeton pour le déchiffrer.|
| [ContentProtection/BasicWidevine](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/ContentProtection/BasicWidevine)|Cet exemple montre comment créer une transformation avec un préréglage AdaptiveStreaming intégré, envoyer un travail, créer une stratégie ContentKeyPolicy avec une configuration Widevine à l’aide d’une clé secrète, l’associer à StreamingLocator, obtenir un jeton et imprimer une URL pour la lecture dans un lecteur Widevine. Quand un utilisateur demande le contenu protégé par Widevine, l’application de lecteur demande une licence auprès du service de licence Media Services. Si l’application de lecteur est autorisée, le service de licence Media Services remet une licence au lecteur. Une licence Widevine contient la clé de déchiffrement qui peut être utilisée par le lecteur client pour déchiffrer et diffuser le contenu.|
| [ContentProtection/BasicPlayReady](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/ContentProtection/BasicPlayReady)|Cet exemple montre comment créer une transformation avec un préréglage AdaptiveStreaming intégré, envoyer un travail, créer une stratégie ContentKeyPolicy avec une configuration PlayReady à l’aide d’une clé secrète, l’associer à StreamingLocator, obtenir un jeton et imprimer une URL pour la lecture dans le Lecteur multimédia Azure. Quand un utilisateur demande le contenu protégé par PlayReady, l’application de lecteur demande une licence auprès du service de licence Media Services. Si l’application de lecteur est autorisée, le service de licence Media Services remet une licence au lecteur. Une licence PlayReady contient la clé de déchiffrement qui permet au lecteur client de déchiffrer le contenu et de le lire en continu.|
| [ContentProtection/OfflinePlayReadyAndWidevine](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/ContentProtection/OfflinePlayReadyAndWidevine)|Cet exemple montre comment chiffrer dynamiquement votre contenu avec PlayReady et Widevine DRM, et lire le contenu sans demander de licence auprès du service de licence. Il montre comment créer une transformation avec un préréglage AdaptiveStreaming intégré, envoyer un travail, créer une stratégie ContentKeyPolicy avec restriction ouverte et une configuration permanente PlayReady/Widevine, l’associer à StreamingLocator et imprimer une URL pour la lecture.|
| [Streaming/AssetFilters](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/Streaming/AssetFilters)|Cet exemple montre créer une transformation avec un préréglage AdaptiveStreaming intégré, envoyer un travail, créer un filtre d’actif multimédia et un filtre de compte, les associer aux localisateurs de streaming et imprimer des URL pour la lecture.|
| [Streaming/StreamHLSAndDASH](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/Streaming/StreamHLSAndDASH)|Cet exemple montre comment créer une transformation avec un préréglage AdaptiveStreaming intégré, envoyer un travail, publier un actif multimédia de sortie pour le streaming DASH et HLS.|
| [HighAvailabilityEncodingStreaming](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/HighAvailabilityEncodingStreaming/) | Cet exemple fournit des conseils et des bonnes pratiques pour un système de production utilisant l’analytique ou l’encodage à la demande. Les lecteurs doivent commencer par l’article associé [Haute disponibilité avec Media Services et VOD (vidéo à la demande)](architecture-high-availability-encoding-concept.md). Un fichier solution distinct est fourni pour l’exemple [HighAvailabilityEncodingStreaming](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/main/HighAvailabilityEncodingStreaming/README.md). |
| [Azure Functions pour Media Services](https://github.com/xpouyat/media-services-v3-dotnet-core-functions-integration/tree/main/Functions)|Ce projet contient des exemples Azure Functions qui se connectent à Azure Media Services v3 pour le traitement vidéo. Vous pouvez utiliser Visual Studio 2019 ou Visual Studio Code pour développer et exécuter les fonctions. Un modèle Azure Resource Manager (ARM) et un workflow GitHub Actions sont fournis pour le déploiement des ressources de fonction et pour permettre le déploiement continu.|

## <a name="nodejs"></a>[Node.JS](#tab/node/)

|Exemple|Description|
|---|---|
|[Hello World - Ressources de liste](https://github.com/Azure-Samples/media-services-v3-node-tutorials/blob/main/AMSv3Samples/HelloWorld-ListAssets/list-assets.ts)|Exemple de base pour la connexion et les ressources de liste |
|[Vidéo en flux continu](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/main/AMSv3Samples/Live/index.ts)| Exemple de base de streaming en direct. **AVERTISSEMENT** : Veillez à vérifier que toutes les ressources sont nettoyées et qu’elles ne sont plus facturées dans le portail lors du streaming en direct|
|[Télécharger et diffuser en continu HLS et DASH](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/main/AMSv3Samples/StreamFilesSample/index.ts)| Exemple de base pour le chargement d’un fichier local ou l’encodage à partir d’une URL source. L’exemple montre comment utiliser le SDK de stockage pour télécharger du contenu et comment diffuser en streaming sur un lecteur |
|[Télécharger et diffuser en continu HLS et DASH avec PlayReady et Widevine DRM](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/main/AMSv3Samples/StreamFilesWithDRMSample/index.ts)| Illustre comment encoder et diffuser en streaming à l’aide de Widevine et PlayReady DRM |
|[Télécharger et utiliser l’IA pour indexer des éléments vidéo et audio](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/main/AMSv3Samples/VideoIndexerSample/index.ts)| Exemple d’utilisation des présélections de l’Analyseur de vidéo et d'audio pour générer des métadonnées et des insights à partir d’un fichier vidéo ou audio |

## <a name="python"></a>[Python](#tab/python)

|Exemple|Description|
|---|---|
|[Encodage de base avec Python](https://github.com/Azure-Samples/media-services-v3-python)| Exemple de base pour le chargement d’un fichier local ou l’encodage à partir d’une URL source. L’exemple montre comment utiliser le SDK de stockage pour télécharger du contenu et comment diffuser en streaming sur un lecteur |
|[Édition des visages à l’aide d’événements et de fonctions](https://github.com/Azure-Samples/media-services-v3-python/tree/main/VideoAnalytics/FaceRedactorEventBased)| Il s’agit d’un exemple d’approche basée sur les événements qui déclenche une tâche Édition des visages Azure Media Services sur une vidéo dès son arrivée sur un compte Stockage Azure. Il utilise Azure Media Services, Azure Function, Event Grid et Stockage Azure pour la solution. Pour une description complète de la solution, consultez [README.md](https://github.com/Azure-Samples/media-services-v3-python/blob/main/VideoAnalytics/FaceRedactorEventBased/README.md)|


## <a name="java"></a>[Java](#tab/java)

|Exemple|Description|
|---|---|
|[AudioAnalytics/AudioAnalyzer/](https://github.com/Azure-Samples/media-services-v3-java/tree/master/AudioAnalytics/AudioAnalyzer)|Procédure pour analyser l’audio dans un fichier multimédia. |
|Protection du contenu|
|ContentProtection||
|[BasicAESClearKey](https://github.com/Azure-Samples/media-services-v3-java/tree/master/ContentProtection/BasicAESClearKey)|Procédure pour chiffrer dynamiquement votre contenu avec AES-128.|
|[BasicPlayReady](https://github.com/Azure-Samples/media-services-v3-java/tree/master/ContentProtection/BasicPlayReady)|Procédure pour chiffrer dynamiquement votre contenu avec PlayReady DRM.|
|[BasicWidevine](https://github.com/Azure-Samples/media-services-v3-java/tree/master/ContentProtection/BasicWidevine)|Procédure pour chiffrer dynamiquement votre contenu avec Widevine DRM.|
|[OfflineFairPlay](https://github.com/Azure-Samples/media-services-v3-java/tree/master/ContentProtection/OfflineFairPlay)|Procédure pour chiffrer dynamiquement votre contenu avec FairPlay DRM et lire le contenu sans demander de licence auprès du service de licence.|
|[OfflinePlayReadyAndWidevine](https://github.com/Azure-Samples/media-services-v3-java/tree/master/ContentProtection/OfflinePlayReadyAndWidevine)|Procédure pour chiffrer dynamiquement votre contenu avec PlayReady et Widevine DRM, et lire le contenu sans demander de licence auprès du service de licence.|
|DynamicPackagingVODContent||
|[AssetFilters](https://github.com/Azure-Samples/media-services-v3-java/tree/master/DynamicPackagingVODContent/AssetFilters)|Procédure pour filtrer le contenu à l’aide des filtres d’actif multimédia et de compte.|
|[StreamHLSAndDASH](https://github.com/Azure-Samples/media-services-v3-java/tree/master/DynamicPackagingVODContent/StreamHLSAndDASH)|Procédure pour empaqueter dynamiquement du contenu VOD en HLS/DASH pour le streaming.|
|[LiveIngest/LiveEventWithDVR](https://github.com/Azure-Samples/media-services-v3-java/tree/master/LiveIngest/LiveEventWithDVR)|Procédure pour créer un LiveEvent avec une archive complète jusqu’à 25 heures et un filtre sur l’actif multimédia avec une fenêtre DVR de 5 minutes, créer un localisateur pour le streaming et utiliser le filtre.|
|[VideoAnalytics/VideoAnalyzer](https://github.com/Azure-Samples/media-services-v3-java/tree/master/VideoAnalytics/VideoAnalyzer)|Procédure pour créer un LiveEvent avec une archive complète jusqu’à 25 heures et un filtre sur l’actif multimédia avec une fenêtre DVR de 5 minutes, créer un localisateur pour le streaming et utiliser le filtre.|
|VideoEncoding||
|[EncodingWithMESCustomPreset](https://github.com/Azure-Samples/media-services-v3-java/tree/master/VideoEncoding/EncodingWithMESCustomPreset)|Procédure pour créer une transformation d’encodage personnalisée à l’aide des paramètres StandardEncoderPreset.|
|[EncodingWithMESPredefinedPreset](https://github.com/Azure-Samples/media-services-v3-java/tree/master/VideoEncoding/EncodingWithMESPredefinedPreset)|Procédure pour envoyer un travail en utilisant un préréglage intégré et une entrée d’URL HTTP, publier un actif multimédia de sortie pour le streaming et télécharger les résultats à des fins de vérification.|

---

## <a name="rest-postman-collection"></a>Collection Postman REST

Les exemples [Postman REST](https://github.com/Azure-Samples/media-services-v3-rest-postman) comportent un environnement et une collection Postman que vous pouvez importer dans le client Postman. Nous vous recommandons d’utiliser les exemples de collection Postman pour vous familiariser avec la structure de l’API et son fonctionnement Azure Resource Management (ARM), ainsi que la structure des appels depuis les kits de développement logiciel (SDK) clients. 

[!INCLUDE [warning-rest-api-retry-policy.md](./includes/warning-rest-api-retry-policy.md)]
