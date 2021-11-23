---
title: Qu’est-ce qu’Azure Video Analyzer for Media (anciennement Video Indexer) ?
titleSuffix: Azure Video Analyzer for Media
description: Cette article offre une vue d’ensemble du service Azure Video Analyzer for Media (anciennement Video Indexer).
ms.topic: overview
ms.date: 07/15/2021
ms.author: juliako
ms.openlocfilehash: fddcb1cd3562fde14c38c68811b7272a7d710d2b
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132487491"
---
# <a name="what-is-video-analyzer-for-media"></a>Qu’est-ce que Video Analyzer for Media ?

[!INCLUDE [regulation](./includes/regulation.md)]

Azure Video Analyzer for Media (anciennement Video Indexer) est une application cloud qui fait partie d’Azure Applied AI Services et qui est basée sur Azure Media Services et Azure Cognitive Services (au même titre que Visage, Traducteur, Vision par ordinateur et Speech). Elle vous permet d’extraire les insights de vos vidéos à l’aide de modèles vidéo et audio Video Analyzer for Media.

Pour commencer à extraire des insights avec Video Analyzer for Media, vous devez créer un compte et charger des vidéos. Quand vous chargez vos vidéos sur Video Analyzer for Media, ce service analyse les éléments audio-visuels en exécutant différents modèles d’IA. À mesure que Video Analyzer for Media analyse votre vidéo, les insights sont extraits par les modèles IA.

Lorsque vous créez un compte Video Analyzer for Media et que vous le connectez à Media Services, les fichiers multimédias et de métadonnées sont stockés dans le compte de stockage Azure associé à ce compte Media Services. Pour plus d’informations, consultez [Créer un compte Video Analyzer for Media connecté à Azure](connect-to-azure.md).

Le diagramme suivant est une illustration et non une explication technique de la façon dont Video Analyzer for Media fonctionne dans le back-end.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/video-indexer-overview/model-chart.png" alt-text="Diagramme de flux de Video Analyzer for Media":::

## <a name="compliance-privacy-and-security"></a>Conformité, confidentialité et sécurité

Rappelez-vous que vous devez respecter toutes les lois applicables dans le cadre de votre utilisation de Video Analyzer for Media, et que vous n’êtes pas autorisé à l’utiliser ou à utiliser tout autre service Azure d’une façon qui porte atteinte aux droits d’autrui ou qui soit préjudiciable pour autrui.

Avant de charger une vidéo ou une image vers Video Analyzer for Media, vous devez disposer de tous les droits appropriés pour utiliser la vidéo ou l’image, y compris, lorsque cela est requis par la Loi, avoir obtenu tous les consentements nécessaires de chaque personne (le cas échéant) apparaissant dans la vidéo ou l’image, pour autoriser l’utilisation, le traitement et le stockage de leurs données dans Video Analyzer for Media et Azure. Certaines juridictions peuvent imposer des obligations légales spéciales pour la collecte, le traitement en ligne et le stockage de catégories de données particulières, comme des données biométriques. Avant d’utiliser Video Analyzer for Media et Azure pour le traitement et le stockage de données soumises à des exigences légales spéciales, veillez à respecter toutes les exigences légales applicables.

Pour en savoir plus sur la conformité, la confidentialité et la sécurité dans Video Analyzer for Media, visitez le [Centre de confidentialité](https://www.microsoft.com/TrustCenter/CloudServices/Azure/default.aspx) Microsoft. Pour connaître les obligations de Microsoft relatives à la confidentialité et les bonnes pratiques de gestion et de conservation de vos données, consultez la [Déclaration de confidentialité](https://privacy.microsoft.com/PrivacyStatement), les [Conditions d’utilisation des services en ligne](https://www.microsoft.com/licensing/product-licensing/products?rtc=1) (« OST ») et l’[Avenant au traitement des données](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=67) (« DPA ») de Microsoft. En utilisant Video Analyzer for Media, vous acceptez de respecter les OST, le DPA et la Déclaration de confidentialité.

## <a name="what-can-i-do-with-video-analyzer-for-media"></a>Que puis-je faire avec Video Analyzer for Media ?

Les insights de Video Analyzer for Media peuvent être appliqués à de nombreux scénarios, parmi lesquels :

* *Recherche profonde* : Utilisez les insights extraits de la vidéo pour améliorer l’expérience de recherche au sein d’une bibliothèque vidéo. Par exemple, l’indexation des visages et des mots prononcés peut permettre de trouver les moments spécifiques d’une vidéo où une personne a prononcé certains mots ou le moment où deux personnes se sont vues. La recherche basée sur de tels aperçus de vidéos s’applique aux agences de presse, aux instituts de formation, aux diffuseurs, aux propriétaires de contenu de divertissement, aux LOB d’entreprise et d’une façon générale à n’importe quel secteur d’activité possédant une bibliothèque vidéo au sein de laquelle les utilisateurs doivent faire des recherches.
* *Création de contenu* : créez des bandes-annonces, des séquences de meilleurs moments, du contenu de réseaux sociaux ou des clips d’actualité basés sur des insights Video Analyzer for Media extraits de votre contenu. Les images clés, les marqueurs de scènes et les timestamps pour les apparences des étiquettes et des personnes rendent le processus de création beaucoup plus lisse et plus simple, et vous permettent d’accéder aux parties de la vidéo dont vous avez besoin pour le contenu que vous créez.
* *Accessibilité* : si vous souhaitez que votre contenu soit disponible pour les personnes handicapées ou qu’il soit distribué dans différentes régions en différentes langues, vous pouvez utiliser la transcription et la traduction fournies par Video Analyzer for Media dans plusieurs langues.
* *Monétisation* : Video Analyzer for Media peut contribuer à augmenter la valeur des vidéos. Par exemple, les secteurs d’activité s’appuyant sur le chiffre d’affaires publicitaire (es médias d’information, les médias sociaux, et ainsi de suite), peuvent fournir des publicités pertinentes en utilisant les insights extraits en tant que signaux supplémentaires pour le serveur publicitaire.
* *Modération du contenu* : Utilisez des modèles de modération de contenu textuel et visuel afin de protéger vos utilisateurs contre le contenu inapproprié et de vous assurer que le contenu que vous publiez correspond aux valeurs de votre organisation. Vous pouvez bloquer automatiquement certaines vidéos ou alerter vos utilisateurs au sujet du contenu.
* *Suggestions* : les insights vidéo peuvent être utilisés pour améliorer l’engagement utilisateur en soulignant les moments intéressants d’une vidéo. En marquant chaque vidéo avec des métadonnées supplémentaires, vous pouvez recommander aux utilisateurs les vidéos les plus pertinentes et mettre en évidence la partie de la vidéo qui répondra à leurs besoins.

## <a name="features"></a>Fonctionnalités

La liste suivante présente les insights que vous pouvez récupérer à partir de vos vidéos à l’aide des modèles vidéo et audio de Video Analyzer for Media :

### <a name="video-insights"></a>Insights vidéo

* **Détection de visage** : Détecte et regroupe les visages qui apparaissent dans la vidéo.
* **Identification de célébrités** : Video Analyzer for Media identifie automatiquement plus de 1 million de célébrités : dirigeants, acteurs et actrices, athlètes, chercheurs, dirigeants d’entreprise et spécialistes techniques du monde entier. Les données relatives à ces célébrités figurent également sur plusieurs sites Web (tels que IMDB, Wikipedia, etc.).
* **Identification des visages basée sur le compte** : Video Analyzer for Media entraîne un modèle pour un compte spécifique. Il reconnaît ensuite les visages dans la vidéo en fonction du modèle formé. Pour plus d’informations, consultez [Personnaliser un modèle de personne à partir du site web Video Analyzer for Media](customize-person-model-with-website.md) et [Personnaliser un modèle de personne avec l’API Video Analyzer for Media](customize-person-model-with-api.md).
* **Extraction de miniatures pour les visages (« meilleurs visages »)**  : Identifie automatiquement le meilleur visage capturé dans chaque groupe de visages (en fonction de la qualité, de la taille et de la position frontale) et l’extrait en tant qu’actif d’image.
* **Reconnaissance visuelle du texte** (OCR) : Extrait le texte qui s’affiche visuellement dans la vidéo.
* **Modération du contenu visuel** : Détecte les contenus choquants et/ou destinés aux adultes.
* **Identification des étiquettes** : Identifie les objets visuels et les actions affichés.
* **Segmentation de scène** : Détermine quand une scène change dans la vidéo via des signaux visuels. Une scène représente un événement unique et est composée d’une série de captures consécutives, qui sont sémantiquement liées.
* **Détection de plan** : Détermine quand un plan change dans la vidéo via des signaux visuels. Un plan est une série d’images prises par la même caméra. Pour plus d’informations, consultez [Scènes, plans et images clés](scenes-shots-keyframes.md).
* **Détection de cadre noir** : Identifie les cadres noirs présents dans la vidéo.
* **Extraction d’images clés** : Détecte les images clés stables dans une vidéo.
* **Déploiement de crédits** : Identifie le début et la fin des crédits à la fin des émissions de télévision et des films.
* **Détection de personnages animés** (préversion) : Détection, regroupement et reconnaissance de personnages dans le contenu animé via l’intégration avec [Cognitive Services Custom Vision](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/). Pour plus d’informations, consultez [Détection de personnages animés](animated-characters-recognition.md).
* **Détection du type de plan éditorial** : Marquage de plans en fonction de leur type (par exemple, plan large, plan moyen, gros plan, très gros plan, deux plans, plusieurs personnes, extérieur et intérieur, etc.). Pour plus d’informations, consultez [Détection du type de plan éditorial](scenes-shots-keyframes.md#editorial-shot-type-detection).
* **Suivi des personnes observées** (préversion) : détecte les personnes observées dans les vidéos et fournit des informations comme la position de la personne dans le cadre de la vidéo (en utilisant des zones englobantes) et l’horodatage exact (début, fin) ainsi que le score de confiance quand une personne apparaît. Pour plus d’informations, consultez [Suivre des personnes dans une vidéo](observed-people-tracing.md).
    * **Vêtements détectés sur les personnes** : détecte le type de vêtement des personnes apparaissant dans la vidéo et fournit des informations pour décrire, par exemple, s’il s’agit de manches longues ou courtes, de pantalons longs ou courts, et d’une jupe ou d’une robe. Les vêtements détectés sont associés aux personnes qui les portent, et l’horodatage exact (début, fin) ainsi qu’un niveau de confiance pour la détection sont fournis.

### <a name="audio-insights"></a>Insights audio

* **Transcription audio** : procède à la reconnaissance vocale dans 50 langues et permet les extensions. Les langues prises en charge incluent anglais (États-Unis), anglais (Royaume-Uni), anglais (Australie), espagnol, espagnol (Mexique), français, français (Canada), allemand, italien, chinois (cantonais, traditionnel), chinois (simplifié), japonais, russe, portugais, hindi, tchèque, néerlandais, polonais, danois, norvégien, fin, suédois, thaï, turc, coréen, arabe (Égypte), arabe (République arabe syrienne), arabe (Israël), arabe (Irak), arabe (Jordanie), arabe (Koweït), arabe (Liban), arabe (Oman), arabe (Qatar), arabe (Arabie saoudite), arabe (Émirats Arabes Unis), arabe (Autorité palestinienne) et arabe moderne standard (Bahreïn).
* **Détection de langue automatique** : Identifie automatiquement la langue parlée dominante. Les langues prises en charge incluent l’anglais, l’espagnol, le français, l’allemand, l’italien, le chinois (mandarin), le japonais, le russe et le portugais. Si la langue ne peut pas être identifiée en toute confiance, Video Analyzer for Media suppose que la langue parlée est l’anglais. Pour plus d'informations, voir [Modèle d’identification de langues](language-identification-model.md).
* **Identification et transcription de discours en plusieurs langues** : Identifie automatiquement la langue parlée dans les segments différents de l’audio. Il envoie chaque segment du fichier multimédia à transcrire, puis regroupe la transcription dans une transcription unifiée. Pour plus d’informations, consultez [Identifier et transcrire automatiquement un contenu multilingue](multi-language-identification-transcription.md).
* **Sous-titrage** : Crée un sous-titrage dans trois formats : VTT, TTML, SRT.
* **Traitement en deux canaux** : Détecte automatiquement, sépare la transcription et fusionne en une chronologie unique.
* **Réduction du bruit** : Nettoie les enregistrements contenant du bruit ou de l’audio de téléphonie (en fonction des filtres Skype).
* **Personnalisation de la transcription** (CRIS) : Entraîne des modèles de reconnaissance vocale personnalisés pour créer des transcriptions spécifiques au secteur. Pour plus d’informations, consultez [Personnaliser un modèle de langue à partir du site web Video Analyzer for Media](customize-language-model-with-website.md) et [Personnaliser un modèle de langue avec les API Video Analyzer for Media](customize-language-model-with-api.md).
* **Énumération de l’orateur** : Mappe et comprend quel orateur a prononcé tels mots et à quel moment. Seize intervenants peuvent être détectés dans un seul fichier audio.
* **Statistiques de l’orateur** : Fournit des statistiques concernant les ratios de parole des orateurs.
* **Modération du contenu textuel** : Détecte le texte explicite dans la transcription audio.
* **Effets audio** (préversion) : Détecte les effets audio suivants dans les segments non vocaux du contenu : coup de feu, verre brisé, alarme, sirène, explosion, aboiement, cri, rire, réactions de foule (encouragements, applaudissements et huées) et silence. Remarque : Le jeu complet d’événements est disponible uniquement lorsque vous choisissez « Analyse audio avancée » dans les préréglages de chargement. Si ce n’est pas le cas, seules les options « Silence » et « Réactions de foule » seront disponibles.
* **Détection d’émotions** : Identifie des émotions en fonction de la voix (ce qui est dit) et de la tonalité (la façon de parler). L’émotion peut être le bonheur, la tristesse, la colère ou la peur.
* **Traduction** : Crée des traductions de la transcription audio en 54 langues différentes.
* **Détection des effets audio** (préversion) : Détecte divers événements acoustiques et les classifie dans différentes catégories (par exemple, coup de feu, cri, réaction de foule, etc.). Les événements acoustiques détectés se trouvent dans le fichier de sous-titres. Vous pouvez télécharger le fichier à partir du portail Video Analyzer for Media. Pour plus d’informations, consultez [Détection des effets audio](audio-effects-detection.md).

### <a name="audio-and-video-insights-multi-channels"></a>Insights audio et vidéo (plusieurs canaux)

Lors de l’indexation d’un canal, le résultat partiel pour ces modèles sera disponible.

* **Extraction de mots-clés** : Extrait les mots clés du texte visuel ou vocal.
* **Extraction des entités nommées** : extrait des marques, des emplacements et des personnes à partir de la reconnaissance vocale et du texte visuel via le traitement en langage naturel (NLP).
* **Inférence de la rubrique** : Fait des inférences des principales rubriques à partir de transcriptions. La taxonomie IPTC de second niveau est incluse.
* **Artefacts** : Extrait un ensemble complet d’artefacts d’un « niveau suivant de détails » pour chacun des modèles.
* **Analyse des sentiments** : Identifie les sentiments positifs, négatifs et neutres à partir de la reconnaissance vocale et visuelle du texte.

## <a name="how-can-i-get-started-with-video-analyzer-for-media"></a>Comment bien démarrer avec Video Analyzer for Media ?

Vous pouvez accéder aux fonctionnalités de Video Analyzer for Media de trois façons :

* Portail Video Analyzer for Media : Une solution facile à utiliser qui vous permet d’évaluer le produit, de gérer le compte et de personnaliser les modèles.

    Pour plus d’informations sur le portail, consultez [Bien démarrer avec le site web Video Analyzer for Media](video-indexer-get-started.md).  

* Intégration d’API : Toutes les fonctionnalités de Video Analyzer for Media sont disponibles par le biais d’une API REST, qui vous permet d’intégrer la solution dans vos applications et votre infrastructure.

    Pour bien démarrer en tant que développeur, consultez  [Utiliser l’API REST Video Analyzer for Media](video-indexer-use-apis.md).

* Widget incorporable : Vous permet d’incorporer les expériences d’insights, de lecteur et d’éditeur Video Analyzer for Media dans votre application.

    Pour plus d’informations, consultez  [Incorporer des widgets Video Indexer dans vos applications](video-indexer-embed-widgets.md).

Si vous utilisez le site web, les insights sont ajoutés en tant que métadonnées et sont visibles dans le portail. Si vous utilisez des API, les insights sont disponibles sous forme de fichier JSON.

## <a name="supported-browsers"></a>Navigateurs pris en charge

La liste suivante répertorie les navigateurs pris en charge que vous pouvez utiliser pour le site web Video Analyzer for Media et vos applications qui incorporent les widgets. La liste affiche également la version minimale prise en charge du navigateur :

- Edge, version : 16
- Firefox, version : 54
- Chrome, version : 58
- Safari, version : 11
- Opera, version : 44
- Opera Mobile, version : 59
- Navigateur Android, version : 81
- Navigateur Samsung, version : 7
- Chrome pour Android, version : 87
- Firefox pour Android, version : 83

## <a name="next-steps"></a>Étapes suivantes

Vous êtes prêt à vous lancer avec Video Analyzer for Media. Pour plus d’informations, consultez les articles suivants :

- [Bien démarrer avec le site web Video Analyzer for Media](video-indexer-get-started.md).
- [Traiter du contenu avec l’API REST Video Analyzer for Media](video-indexer-use-apis.md).
- [Incorporer des widgets visuels dans votre application](video-indexer-embed-widgets.md).
