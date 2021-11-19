---
title: Notes de publication d’Azure Video Analyzer for Media (anciennement Video Indexer) | Microsoft Docs
description: Pour vous informer des développements les plus récents, cet article détaille les toutes dernières mises à jour concernant Azure Video Analyzer for Media (anciennement Video Indexer).
ms.topic: article
ms.custom: references_regions
ms.date: 08/01/2021
ms.author: juliako
ms.openlocfilehash: 7341112e545e4fc0c74de8d32e9c2d54d3461057
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132491918"
---
# <a name="video-analyzer-for-media-release-notes"></a>Notes de publication de Video Analyzer for Media

>Soyez notifié de la disponibilité des mises à jour sur cette page en faisant un copier-coller de cette URL : `https://docs.microsoft.com/api/search/rss?search=%22Azure+Media+Services+Video+Indexer+release+notes%22&locale=en-us` dans votre lecteur de flux RSS.

Cet article vise à vous informer sur les derniers développements d’Azure Video Analyzer for Media (anciennement Video Indexer). Vous y trouverez les informations suivantes :

* Versions les plus récentes
* Problèmes connus
* Résolution des bogues
* Fonctionnalités dépréciées

## <a name="november-2021"></a>Novembre 2021
 
### <a name="public-preview-of-azure-video-analyzer-for-media-account-management-based-on-arm"></a>Préversion publique de la gestion de compte Azure Video Analyzer for Media sur ARM

Azure Video Analyzer for Media introduit une préversion publique de la gestion de compte basée sur Azure Resource Manager (ARM). Vous pouvez tirer parti des API basées sur ARM pour créer, modifier et supprimer un compte dans le portail Azure.

Pour plus d’informations, consultez [Créer un compte Video Analyzer for Media](https://techcommunity.microsoft.com/t5/azure-ai/azure-video-analyzer-for-media-is-now-available-as-an-azure/ba-p/2912422).

### <a name="peoples-clothing-detection"></a>Détection des vêtements des personnes

Lorsque vous indexez une vidéo via les paramètres vidéo avancés, vous pouvez afficher la nouvelle fonctionnalité de **détection des vêtements** des personnes. Si des personnes sont détectées dans votre fichier multimédia, vous pouvez maintenant afficher le type de vêtements qu’ils utilisent dans le lecteur multimédia. 

## <a name="october-2021"></a>Octobre 2021

### <a name="embed-widgets-in-your-app-using-azure-video-analyzer-for-media-package"></a>Incorporer des widgets dans votre application à l’aide du package Azure Video Analyzer for Media

Utilisez le nouveau package npm Azure Video Analyzer for Media (AVAM) `@azure/video-analyzer-for-media-widgets` pour ajouter des widgets `insights` à votre application et personnaliser celle-ci en fonction de vos besoins.

Le nouveau package AVAM vous permet d’incorporer nos widgets et votre application, ainsi que de gérer la communication entre eux en toute facilité au lieu d’ajouter un élément `iframe` pour incorporer le widget Insights. Pour plus d’informations, consultez [Incorporer et personnaliser des widgets Video Analyzer for Media dans votre application](https://techcommunity.microsoft.com/t5/azure-media-services/embed-and-customize-azure-video-analyzer-for-media-widgets-in/ba-p/2847063). 

## <a name="august-2021"></a>Août 2021

### <a name="re-index-video-or-audio-files"></a>Réindexer des fichiers audio ou vidéo

Il existe désormais une option permettant de réindexer les fichiers audio ou vidéo qui ont échoué pendant le processus d’indexation.

### <a name="improve-accessibility-support"></a>Amélioration de la prise en charge de l’accessibilité

Correction des bogues liés à CSS, à leur thème et à l’accessibilité :

* contraste élevé
* Affichages des paramètres de compte et des Insights dans le [portail](https://www.videoindexer.ai).  

## <a name="july-2021"></a>Juillet 2021

### <a name="automatic-scaling-of-media-reserved-units"></a>Mise à l’échelle automatique des unités réservées Multimédias
 
Depuis le 1er août 2021, Azure Video Analyzer for Media (anciennement Video Indexer) permet la mise à l’échelle automatique des [unités réservée Multimédias](../../media-services/latest/concept-media-reserved-units.md) par [Azure Media Services](../../media-services/latest/media-services-overview.md). Ainsi, vous n’avez pas besoin de les gérer avec Azure Video Analyzer for Media. Ceci permettra d’optimiser les prix. Par exemple, vous bénéficierez, dans de nombreux cas, d’une réduction de prix en fonction de vos besoins métier, de par la mise à l’échelle automatique.

## <a name="june-2021"></a>Juin 2021
 
### <a name="video-analyzer-for-media-deployed-in-six-new-regions"></a>Déploiement de Video Analyzer for Media dans six nouvelles régions
 
Vous pouvez maintenant créer un compte payant Video Analyzer for Media dans les régions France Centre, USA Centre, Brésil Sud, USA Centre-Ouest, Corée Centre et Japon Ouest.
  
## <a name="may-2021"></a>Mai 2021

### <a name="new-source-languages-support-for-speech-to-text-stt-translation-and-search"></a>Prise en charge de nouvelles langues sources pour la reconnaissance vocale, la traduction et la recherche

Video Analyzer for Media prend désormais en charge la reconnaissance vocale, la traduction et la recherche dans les langues suivantes : chinois (cantonais) [« zh-HK »] ; néerlandais (Pays-Bas) [« Nl-NL »] ; tchèque [« Cs-CZ »] ; polonais [« Pl-PL »] ; suédois (Suède) [« Sv-SE »] ; norvégien [« nb-NO »] ; finnois [« fi-FI »] ; français canadien [« fr-CA »] ; thaï [« th-TH »] ; arabe : (Émirats Arabes Unis) [« ar-AE », « ar-EG »], (Irak) [« ar-IQ »], (Jordanie) [« ar-JO »], (Koweït) [« ar-KW »], (Liban) [« ar-LB »], (Oman) [« ar-OM »], (Qatar) [« ar-QA »], (Autorité palestinienne) [« ar-PS »], (Syrie) [« ar-SY »] ; et turc [« TR-TR »]. 

Ces langues sont disponibles dans l’API et sur le site web de Video Analyzer for Media. Sélectionnez la langue dans la liste déroulante sous **Langue source de la vidéo**.

### <a name="new-theme-for-azure-video-analyzer-for-media"></a>Nouveau thème pour Azure Video Analyzer for Media

Un nouveau thème est disponible, « Azure », ainsi que les thèmes « Clair » et « Sombre ». Pour sélectionner un thème, cliquez sur l’icône en forme d’engrenage dans le coin supérieur droit du site web, puis recherchez les thèmes sous **Paramètres utilisateur**.
 
### <a name="new-open-source-code-you-can-leverage"></a>Nouveau code open source dont vous pouvez tirer profit 

Trois nouveaux projets GitHub sont disponibles dans notre [référentiel GitHub](https://github.com/Azure-Samples/media-services-video-indexer) :

* Code pour vous aider à tirer parti de la nouvelle [personnalisation des widgets](https://github.com/Azure-Samples/media-services-video-indexer/tree/master/Embedding%20widgets).
* Solution pour vous aider à ajouter une [recherche personnalisée](https://github.com/Azure-Samples/media-services-video-indexer/tree/master/VideoSearchWithAutoMLVision) à vos vidéothèques.
* Solution pour vous aider à ajouter la [déduplication](https://github.com/Azure-Samples/media-services-video-indexer/commit/6b828f598f5bf61ce1b6dbcbea9e8b87ba11c7b1) à vos vidéothèques.
 
### <a name="new-option-to-toggle-bounding-boxes-for-observed-people-on-the-player"></a>Nouvelle option permettant d’activer ou de désactiver les rectangles englobants (pour les personnes observées) sur le lecteur  

Lorsque vous indexez une vidéo par l’intermédiaire de nos paramètres vidéo avancés, vous pouvez voir nos nouvelles capacités de personnes observées. Si des personnes sont détectées dans votre fichier multimédia, vous pouvez activer un rectangle englobant sur la personne détectée par le biais du lecteur multimédia.

## <a name="april-2021"></a>Avril 2021

Le service Video Indexer a été renommé en Azure Video Analyzer for Media.

### <a name="improved-upload-experience-in-the-portal"></a>Amélioration de l’expérience de chargement dans le portail
 
Video Analyzer for Media a une nouvelle expérience de chargement dans le [portail](https://www.videoindexer.ai). Pour télécharger votre fichier multimédia, sélectionnez le bouton **Charger** dans l’onglet **Fichiers multimédias**.

### <a name="new-developer-portal-in-available-in-gov-cloud"></a>Nouveau portail des développeurs disponible dans le cloud pour le secteur public
 
Un [portail des développeurs pour Video Analyzer for Media](https://api-portal.videoindexer.ai) est désormais disponible dans Azure for US Government.

### <a name="observed-people-tracing-preview"></a>Suivi des personnes observées (préversion)  

Azure Video Analyzer for Media détecte les personnes observées dans les vidéos et fournit des informations comme l’emplacement d’une personne dans l’image vidéo et l’horodatage exact (début, fin) indiquant à quel moment elle apparaît. L’API retourne les coordonnées du cadre englobant (en pixels) chaque personne détectée avec son indice de confiance. 

Par exemple, si une vidéo contient une personne, l’opération de détection liste les apparitions de la personne avec les coordonnées dans les images vidéo. Vous pouvez utiliser cette fonctionnalité pour déterminer le parcours de la personne dans une vidéo. Elle vous permet aussi de déterminer s’il existe plusieurs occurrences de la même personne dans une vidéo. 

Vous pouvez accéder à la fonctionnalité de suivi des personnes observées nouvellement ajoutée pendant que vous indexez votre fichier en choisissant la présélection **Option avancée** -> **Vidéo avancé** ou **Vidéo + audio avancé** (sous Indexation vidéo + audio). Les niveaux de présélection d’indexation Standard et De base n’incluent pas ce nouveau modèle avancé. 

Quand vous choisissez d’afficher les Insights de votre vidéo sur le site web Video Analyzer for Media, le suivi des personnes observées s’affiche dans la page avec les miniatures de toutes les personnes détectées. Vous pouvez choisir la miniature d’une personne et voir où elle apparaît dans le lecteur vidéo.  

Cette fonctionnalité est également disponible dans le fichier JSON généré par Video Analyzer for Media. Pour plus d’informations, consultez [Suivre des personnes dans une vidéo](observed-people-tracing.md).

### <a name="detected-acoustic-events-with-audio-effects-detection-preview"></a>Événements acoustiques détectés avec le composant **Détection des effets audio** (préversion)

Vous pouvez à présent voir les événements acoustiques détectés dans le fichier de sous-titres. Le fichier peut être téléchargé à partir du portail Video Analyzer for Media et est disponible en tant qu’artefact dans l’API GetArtifact.

Le composant **Détection des effets audio** (préversion) détecte divers événements acoustiques et les classifie dans différentes catégories (par exemple, coup de feu, cri, réaction de foule, etc.). Pour plus d’informations, consultez [Détection des effets audio](audio-effects-detection.md).

## <a name="march-2021"></a>Mars 2021

### <a name="audio-analysis"></a>Analyse audio 

L’analyse audio est désormais disponible dans un nouveau pack supplémentaire de fonctionnalités audio à différents prix. Le nouveau préréglage d’analyse **Audio de base** est une option économique qui permet uniquement d’extraire les transcriptions de discours, les traductions et de mettre en forme des légendes et des sous-titres de sortie. Le préréglage **Audio de base** produira deux compteurs distincts sur votre facture, y compris une ligne de transcription et une ligne séparée pour la mise en forme des légendes et des sous-titres. Pour plus d’informations sur les tarifs, consultez la page [Tarifs d’Azure Media Services](https://azure.microsoft.com/pricing/details/media-services/).

Le pack nouvellement ajouté est disponible lors de l’indexation ou de la réindexation de votre fichier en choisissant le préréglage **Option avancée** -> **Audio de base** (sous la zone de liste déroulante **Vidéo + indexation audio**).

### <a name="new-developer-portal"></a>Nouvelle version du Developer Portal 

Video Analyzer for Media dispose d’une nouvelle version du [Developer Portal](https://api-portal.videoindexer.ai/). Essayez les nouvelles API Video Analyzer for Media et trouvez toutes les ressources pertinentes au même endroit : [dépôt GitHub](https://github.com/Azure-Samples/media-services-video-indexer), [Stack Overflow](https://stackoverflow.com/questions/tagged/video-indexer), [Communauté technique de Video Analyzer for Media](https://techcommunity.microsoft.com/t5/azure-media-services/bg-p/AzureMediaServices/label-name/Video%20Indexer) avec les billets de blog associés, [Foires aux questions (FAQ) sur Video Analyzer for Media](faq.yml), [User Voice](https://feedback.azure.com/d365community/forum/09041fae-0b25-ec11-b6e6-000d3a4f0858) pour envoyer vos commentaires et suggérer des fonctionnalités, et [lien « CodePen »](https://codepen.io/videoindexer) accompagné d’exemples de code de widgets. 
 
### <a name="advanced-customization-capabilities-for-insight-widget"></a>Fonctionnalités avancées de personnalisation pour le widget d’insight 

Le kit SDK est désormais disponible pour incorporer le widget d’insights de Video Analyzer for Media dans votre propre service ainsi que pour personnaliser son style et ses données. Le kit SDK prend en charge le widget d’insights Video Analyzer for Media standard et un widget d’insights entièrement personnalisable. Un exemple de code est disponible dans le [dépôt GitHub de Video Analyzer for Media](https://github.com/Azure-Samples/media-services-video-indexer/tree/master/Embedding%20widgets/widget-customization). Grâce à ces fonctionnalités de personnalisation avancées, le développeur de solutions peut appliquer un style personnalisé, utiliser les propres données d’IA du client et les présenter dans le widget d’insight (avec ou sans insights Video Analyzer for Media). 

### <a name="video-analyzer-for-media-deployed-in-the-us-north-central--us-west-and-canada-central"></a>Déploiement de Video Analyzer for Media dans les régions USA Centre Nord, USA Ouest et Canada Centre 

Vous pouvez désormais créer un compte payant Video Analyzer for Media dans les régions USA Centre Nord, USA Ouest et Canada Centre
 
### <a name="new-source-languages-support-for-speech-to-text-stt-translation-and-search"></a>Prise en charge de nouvelles langues sources pour la conversion de parole en texte, la traduction et la recherche 

Video Analyzer for Media prend désormais en charge la conversion de parole en texte, la traduction et la recherche en danois (« da-DK »), norvégien (« nb-NO »), suédois (« sv-SE »), finnois (« fi-FI »), français canadien (« fr-CA »), thaïlandais (« th-TH »), arabe (« ar-BH », « ar-EG », « ar-IQ », « ar-JO », « ar-KW », « ar-LB », « ar-OM », « ar-QA », « ar-S » et « ar-SY ») et turc (« tr-TR »). Ces langues sont disponibles dans l’API et sur le site web Video Analyzer for Media. 
 
### <a name="search-by-topic-in-video-analyzer-for-media-website"></a>Rechercher par rubrique dans le site web de Video Analyzer for Media 

Vous pouvez désormais utiliser la fonctionnalité de recherche, en haut de la page du [site web Video Analyzer for Media](https://www.videoindexer.ai/account/login), pour rechercher des vidéos sur des sujets spécifiques. 

## <a name="february-2021"></a>Février 2021

### <a name="multiple-account-owners"></a>Plusieurs propriétaires du compte 

Le rôle Propriétaire du compte a été ajouté à Video Analyzer for Media. Vous pouvez ajouter, modifier et supprimer des utilisateurs ainsi que modifier leur rôle. Pour plus d’informations sur le partage d’un compte, consultez [Inviter des utilisateurs](invite-users.md).

### <a name="audio-event-detection-public-preview"></a>Détection d’événements audio (préversion publique)

> [!NOTE]
> Cette fonctionnalité n’est disponible que pour les comptes en version d’essai. 

Video Analyzer for Media détecte maintenant les effets audio suivants dans les segments non vocaux du contenu : coup de feu, éclat de verre, alarme, sirène, explosion, aboiement, hurlement, rire, réactions de foule (encouragements, applaudissements et huées) et silence. 

La fonctionnalité des effets audio récemment ajoutée est disponible lors de l’indexation de votre fichier en choisissant le prégéglage **Option avancée** -> **Audio avancé** (sous l’indexation Vidéo + audio). L’indexation Standard inclut uniquement le **silence** et les **réactions de foule**. 

Le type d’événement **applaudissements** qui a été inclus dans le modèle d’effets audio précédent, est maintenant extrait une partie du type d’événement **réactions de foule**.

Lorsque vous choisissez d’afficher des **Insights** sur votre vidéo sur le site web [Video Analyzer for Media](https://www.videoindexer.ai/), les effets audio s’affichent sur la page.

:::image type="content" source="./media/release-notes/audio-detection.png" alt-text="Détection des événements audio":::

### <a name="named-entities-enhancement"></a>Amélioration des entités nommées  

La liste extraite des personnes et de l’emplacement a été étendue et mise à jour. 

De plus, le modèle comprend désormais des personnes et des emplacements dans le contexte qui ne sont pas célèbres, comme un « Sam » ou une « maison » dans la vidéo. 

## <a name="january-2021"></a>Janvier 2021

### <a name="video-analyzer-for-media-is-deployed-on-us-government-cloud"></a>Video Analyzer for Media est déployé sur le cloud du gouvernement des États-Unis (« US Government cloud ») 

Vous pouvez maintenant créer un compte Video Analyzer for Media payant sur le cloud du gouvernement des États-Unis dans les régions Virginie et Arizona. L’offre d’essai gratuit de Video Analyzer for Media n’est pas disponible dans la région mentionnée. Pour plus d’informations, consultez la documentation de Video Analyzer for Media. 

### <a name="video-analyzer-for-media-deployed-in-the-india-central-region"></a>Video Analyzer for Media est déployé dans la région Inde Centre 

Vous pouvez maintenant créer un compte Video Analyzer for Media payant dans la région Inde Centre. 

### <a name="new-dark-mode-for-the-video-analyzer-for-media-website-experience"></a>Nouveau mode sombre pour l’expérience du site web Video Analyzer for Media

L’expérience du site web Video Analyzer for Media est désormais disponible en mode sombre. Pour activer le mode sombre, ouvrez le panneau Paramètres et activez l’option **Mode sombre**. 

:::image type="content" source="./media/release-notes/dark-mode.png" alt-text="Paramètre du mode sombre":::

## <a name="december-2020"></a>Décembre 2020

### <a name="video-analyzer-for-media-deployed-in-the-switzerland-west-and-switzerland-north"></a>Video Analyzer for Media est déployé dans les régions Suisse Ouest et Suisse Nord

Vous pouvez maintenant créer un compte Video Analyzer for Media payant dans les régions Suisse Ouest et Suisse Nord.

## <a name="october-2020"></a>Octobre 2020

### <a name="animated-character-identification-improvements"></a>Améliorations de l’identification des caractères animés  

Video Analyzer for Media prend en charge la détection, le regroupement et la reconnaissance de personnages dans le contenu animé via l’intégration à Cognitive Services Custom Vision. Nous avons ajouté à cet algorithme IA une amélioration majeure au niveau de la détection et de la reconnaissance des caractères ; la précision des insights et les caractères identifiés s’en trouvent ainsi considérablement enrichis.

### <a name="planned-video-analyzer-for-media-website-authenticatication-changes"></a>Changements planifiés relatifs à l’authentification sur le site web Video Analyzer for Media

À partir du 1er mars 2021, vous ne pourrez plus vous inscrire ni vous connecter au [portail des développeurs](video-indexer-use-apis.md) du [site web Video Analyzer for Media](https://www.videoindexer.ai/) par l’intermédiaire de Facebook ou de LinkedIn.

Votre inscription et votre connexion s’effectueront au moyen de l’un de ces fournisseurs : Azure AD, Microsoft et Google.

> [!NOTE]
> Les comptes Video Analyzer for Media connectés à LinkedIn et Facebook ne seront plus accessibles après le 1er mars 2021. 
> 
> Vous devez [inviter](invite-users.md) une adresse e-mail Azure AD, Microsoft ou Google dont vous êtes propriétaire au compte Video Analyzer for Media afin d’y avoir toujours accès. Vous pouvez ajouter un propriétaire supplémentaire des fournisseurs pris en charge, comme décrit dans [inviter](invite-users.md). <br/>
> Vous pouvez également créer un compte payant et migrer les données.

## <a name="august-2020"></a>Août 2020

### <a name="mobile-design-for-the-video-analyzer-for-media-website"></a>Conception du site web Video Analyzer for Media à destination des appareils mobiles

L’expérience du site web Video Analyzer for Media prend désormais en charge les appareils mobiles. L’expérience utilisateur s’adapte à la taille de votre écran mobile (à l’exception des interfaces utilisateur de personnalisation). 

### <a name="accessibility-improvements-and-bug-fixes"></a>Améliorations de l’accessibilité et résolution des bogues 

Dans le cadre des directives WCAG (Web Content Accessibility Guidelines), l’expérience du site web Video Analyzer for Media est alignée avec la classe C, conformément aux normes d’accessibilité de Microsoft. Plusieurs bogues et améliorations liés à la navigation au clavier, à l’accès par programme et au lecteur d’écran ont été résolus. 

## <a name="july-2020"></a>Juillet 2020

### <a name="ga-for-multi-language-identification"></a>Disponibilité générale pour l’identification multilingue

L’identification multilingue passe du mode Préversion au mode Disponibilité générale et est prête pour une utilisation en production.

Ce passage du mode Préversion au mode Disponibilité générale n'a aucun impact sur les prix.

### <a name="video-analyzer-for-media-website-improvements"></a>Améliorations du site web Video Analyzer for Media

#### <a name="adjustments-in-the-video-gallery"></a>Réglages dans la galerie de vidéos

Une nouvelle barre de recherche pour la recherche d’insights avec des fonctionnalités de filtrage supplémentaires a été ajoutée. Les résultats de la recherche ont également été améliorés.

Nouvelle vue liste avec possibilité de trier et de gérer des archives vidéo avec plusieurs fichiers.

#### <a name="new-panel-for-easy-selection-and-configuration"></a>Nouveau panneau pour faciliter la sélection et la configuration

Un panneau latéral pour faciliter la sélection et la configuration utilisateur a été ajouté, ce qui permet de créer et de partager un compte, et de définir la configuration, simplement et rapidement.

Le panneau latéral est également utilisé pour les préférences utilisateur et l’aide.

## <a name="june-2020"></a>Juin 2020

### <a name="search-by-topics"></a>Rechercher par sujets

Vous pouvez maintenant utiliser l'API de recherche pour rechercher des vidéos traitant de sujets spécifiques (API uniquement).

Les sujets sont ajoutés dans le cadre du `textScope` (paramètre facultatif). Pour plus d'informations, consultez [API](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Search-Videos).  

### <a name="labels-enhancement"></a>Amélioration des étiquettes

Le créateur d'étiquettes a été mis à niveau et comporte désormais davantage d'étiquettes visuelles identifiables.

## <a name="may-2020"></a>Mai 2020

### <a name="video-analyzer-for-media-deployed-in-the-east-us"></a>Video Analyzer for Media est déployé dans la région USA Est

Vous pouvez maintenant créer un compte Video Analyzer for Media payant dans la région USA Est.
 
### <a name="video-analyzer-for-media-url"></a>URL du site Video Analyzer for Media

Les points de terminaison régionaux de Video Analyzer for Media ont tous été unifiés pour démarrer uniquement avec www. Aucun élément d’action n’est requis.

À partir de maintenant, vous atteignez www.videoindexer.ai, qu’il s’agisse d’incorporer des widgets ou de se connecter à des applications web Video Analyzer for Media.

En outre, wus.videoindexer.ai est redirigé vers www. Pour plus d’informations, consultez [Incorporer des widgets Video Analyzer for Media dans vos applications](video-indexer-embed-widgets.md).

## <a name="april-2020"></a>Avril 2020

### <a name="new-widget-parameters-capabilities"></a>Nouvelles capacités de paramètres pour les widgets

Le widget **Insights** comprend de nouveaux paramètres : `language` et `control`.

Le widget **Lecteur** a un nouveau paramètre `locale`. Les paramètres `locale` et `language` contrôlent la langue d’affichage du lecteur.

Pour plus d’informations, consultez la section [Types de widgets](video-indexer-embed-widgets.md#widget-types). 

### <a name="new-player-skin"></a>Nouvelle apparence de lecteur

Une nouvelle apparence de lecteur a été lancée avec une conception mise à jour.

### <a name="prepare-for-upcoming-changes"></a>Préparer les modifications à venir

* Aujourd’hui, les API suivantes retournent un objet de compte :

    * [Create-Paid-Account](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Create-Paid-Account)
    * [Get-Account](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Account)
    * [Get-Accounts-Authorization](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Accounts-Authorization)
    * [Get-Accounts-With-Token](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Accounts-With-Token)
 
    L’objet Compte contient un champ `Url` pointant vers l’emplacement du [site web Video Analyzer for Media](https://www.videoindexer.ai/).
Pour les comptes payants, le champ `Url` pointe actuellement vers une URL interne au lieu du site web public.
Dans les semaines à venir, nous changerons cela et renverrons l’URL du [site web Video Analyzer for Media](https://www.videoindexer.ai/) pour tous les comptes (version d’évaluation et payante).

    N’utilisez pas les URL internes, vous devez utiliser les [API publiques Video Analyzer for Media](https://api-portal.videoindexer.ai/).
* Si vous incorporez des URL Video Indexer dans vos applications et que celles-ci ne pointent pas vers le [site web Video Analyzer for Media](https://www.videoindexer.ai/) ou le point de terminaison d’API Video Analyzer for Media (`https://api.videoindexer.ai`), mais vers un point de terminaison régional (par exemple, `https://wus2.videoindexer.ai`), régénérez les URL.

   Pour ce faire, vous pouvez soit :

    * Remplacer l’URL par une URL pointant vers les API du widget Video Analyzer for Media (par exemple, le [widget Insights](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Video-Insights-Widget))
    * Utiliser le site web Video Analyzer for Media pour générer une nouvelle URL incorporée :
         
         Appuyez sur **Lire** pour accéder à la page de votre vidéo -> cliquez sur le bouton **&lt;/&gt;Incorporer** -> copiez l’URL dans votre application :
   
    Les URL régionales ne sont pas prises en charge et seront bloquées dans les semaines à venir.

## <a name="january-2020"></a>Janvier 2020
 
### <a name="custom-language-support-for-additional-languages"></a>Prise en charge linguistique personnalisée pour d’autres langues

Video Analyzer for Media prend désormais en charge des modèles de langage personnalisés pour les langues `ar-SY`, `en-UK` et `en-AU` (API uniquement).
 
### <a name="delete-account-timeframe-action-update"></a>Mise à jour de la plage de temps de l’action de suppression de compte

L’action de suppression de compte supprime désormais le compte dans les 90 jours au lieu des 48 heures.
 
### <a name="new-video-analyzer-for-media-github-repository"></a>Nouveau référentiel GitHub pour Video Analyzer for Media

Un nouveau dépôt GitHub pour Video Analyzer for Media avec différents projets, des guides de prise en main et des exemples de code est désormais disponible : https://github.com/Azure-Samples/media-services-video-indexer
 
### <a name="swagger-update"></a>Mise à jour du fichier Swagger

Unification des **authentifications** et des **operations** Video Analyzer for Media en une [spécification Video Analyzer for Media OpenAPI (swagger)](https://api-portal.videoindexer.ai/api-details#api=Operations&operation) unique. Les développeurs peuvent trouver les API dans [Video Analyzer for Media Developer Portal](https://api-portal.videoindexer.ai/).

## <a name="december-2019"></a>Décembre 2019

### <a name="update-transcript-with-the-new-api"></a>Mettre à jour la transcription avec la nouvelle API

Mettez à jour une section spécifique dans la transcription à l’aide de l’API [Update-Video-index](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Update-Video-Index).

### <a name="fix-account-configuration-from-the-video-analyzer-for-media-portal"></a>Corriger la configuration du compte à partir du portail Video Analyzer for Media

Vous pouvez maintenant mettre à jour la configuration de la connexion à Media Services pour favoriser la résolution autonome des problèmes tels que : 

* ressource Azure Media Services incorrecte
* modifications de mot de passe
* les ressources Media Services ont été déplacées dans un autre abonnement  

Pour corriger la configuration du compte, rendez-vous sur le portail Video Analyzer for Media et accédez à Paramètres > onglet Compte (en tant que propriétaire).

### <a name="configure-the-custom-vision-account"></a>Configurer le compte Custom Vision

Configurez le compte Custom Vision sur les comptes payants à l’aide du portail Video Analyzer for Media (auparavant pris en charge uniquement par l’API). Pour cela, connectez-vous au portail Video Analyzer for Media, choisissez Personnalisation du modèle > Personnages animés > Configurer. 

### <a name="scenes-shots-and-keyframes--now-in-one-insight-pane"></a>Scènes, plans et images clés, maintenant dans un seul volet d’informations

Les scènes, les plans et les images clés sont désormais fusionnés en un seul aperçu pour une consommation et une navigation plus faciles. Lorsque vous sélectionnez la scène souhaitée, vous pouvez voir les plans et les images clés qui la composent. 

### <a name="notification-about-a-long-video-name"></a>Notification concernant un nom de vidéo long

Lorsqu’un nom de vidéo contient plus de 80 caractères, Video Analyzer for Media affiche une erreur descriptive lors du chargement.

### <a name="streaming-endpoint-is-disabled-notification"></a>Notification de désactivation du point de terminaison de streaming

Lorsque le point de terminaison de streaming est désactivé, Video Analyzer for Media affiche une erreur descriptive sur la page du lecteur.

### <a name="error-handling-improvement"></a>Amélioration de la gestion des erreurs

Le code d’état 409 est désormais renvoyé à partir des API [Re-Index Video](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Re-Index-Video) (Réindexer une vidéo) et [Update Video Index](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Update-Video-Index) (Mettre à jour l’index d’une vidéo) en cas d’indexation active d’une vidéo, pour éviter de remplacer les modifications de réindexation en cours par accident.

## <a name="november-2019"></a>Novembre 2019
 
* Prise en charge des modèles de langage personnalisés en coréen

    Video Analyzer for Media prend désormais en charge les modèles de langage personnalisés coréens (`ko-KR`) à la fois dans l’API et le portail. 
* Nouvelles langues prises en charge pour la reconnaissance vocale (STT)

    Les API Video Analyzer for Media prennent désormais en charge STT en Arabe levantin (ar-SY), en dialecte anglais britannique (en-GB) et en dialecte anglais australien (en-AU).
    
    Pour le téléchargement de vidéos, nous avons remplacé zh-HANS par zh-CN. Les deux sont pris en charge, mais zh-CN est recommandé et plus précis.
    
## <a name="october-2019"></a>2 octobre 2019
 
* Rechercher des caractères animés dans la galerie

    Lorsque vous indexez des caractères animés, vous pouvez à présent les rechercher dans la galerie vidéo du compte. Pour plus d’informations, consultez [Reconnaissance de personnages animés](animated-characters-recognition.md).

## <a name="september-2019"></a>Septembre 2019
 
Plusieurs améliorations ont été annoncées au salon IBC 2019 :
 
* Reconnaissance de caractères animés (préversion publique)

    Possibilité de détecter, regrouper et reconnaître les caractères dans un contenu animé, par le biais de l’intégration à une vision personnalisée. Pour plus d’informations, consultez [Détection de personnages animés](animated-characters-recognition.md).
* Identification multilingue (préversion publique)

    Détectez les segments en plusieurs langues dans la piste audio et créez une transcription multilingue à partir de ces derniers. Prise en charge initiale : anglais, espagnol, allemand et français. Pour plus d’informations, consultez [Identifier et transcrire automatiquement un contenu multilingue](multi-language-identification-transcription.md).
* Extraction d’entité nommée pour les personnes et la localisation

    extrait des marques, des emplacements et des personnes à partir de la reconnaissance vocale et du texte visuel via le traitement en langage naturel (NLP).
* Classification des types de plans éditoriaux

    Étiquetage des plans avec des types éditoriaux tels que gros plan, plan moyen, deux plans, intérieur, extérieur, etc. Pour plus d’informations, consultez [Détection du type de plan éditorial](scenes-shots-keyframes.md#editorial-shot-type-detection).
* Amélioration de l’inférence de rubrique, avec à présent couverture du niveau 2
    
    Le modèle d’inférence de rubrique prend maintenant en charge la précision plus profonde de la taxonomie IPTC. Pour plus d’informations, consultez [Azure Media Services new AI-powered innovation](https://azure.microsoft.com/blog/azure-media-services-new-ai-powered-innovation/).

## <a name="august-2019"></a>Août 2019
 
### <a name="video-analyzer-for-media-deployed-in-uk-south"></a>Video Analyzer for Media est déployé dans la région Royaume-Uni Sud

Vous pouvez maintenant créer un compte Video Analyzer for Media payant dans la région Royaume-Uni Sud.

### <a name="new-editorial-shot-type-insights-available"></a>Nouvelles informations éditoriales disponibles

De nouvelles balises ajoutées aux séquences vidéo fournissent des « types de prises de vue » éditoriales pour les identifier à l'aide de phrases éditoriales courantes utilisées dans le processus de création de contenu, par exemple : gros plan extrême, gros plan, large, moyen, deux plans, extérieur, intérieur, face gauche et face droite (disponible dans le fichier JSON).

### <a name="new-people-and-locations-entities-extraction-available"></a>Extraction de nouvelles entités Personnes et Lieux disponible

Video Analyzer for Media identifie les lieux et les personnes nommés via le traitement automatique du langage naturel (TALN) à partir de la reconnaissance optique de caractères et de la transcription de la vidéo. Video Analyzer for Media utilise un algorithme d'apprentissage automatique pour reconnaître le moment où certains lieux (par exemple, la Tour Eiffel) ou certaines personnes (par exemple, Monsieur Dupont) sont appelés dans une vidéo.

### <a name="keyframes-extraction-in-native-resolution"></a>Extraction d'images clés en résolution native

Les images clés extraites par Video Analyzer for Media sont disponibles dans la résolution d'origine de la vidéo.
 
### <a name="ga-for-training-custom-face-models-from-images"></a>Lancement du mode Disponibilité générale pour la formation de modèles de visage personnalisés à partir d'images

Passage du mode Préversion au mode Disponibilité générale pour la formation des visages à partir d'images (disponible via l'API et sur le portail).

> [!NOTE]
> Ce passage du mode Préversion au mode Disponibilité générale n'a aucun impact sur les prix.

### <a name="hide-gallery-toggle-option"></a>Option Masquer la galerie

L'utilisateur peut choisir de masquer l'onglet de la galerie sur le portail (comme pour l'onglet des exemples).
 
### <a name="maximum-url-size-increased"></a>Augmentation de la taille maximale des URL

Prise en charge de la chaîne de requête d'URL de 4096 (au lieu de 2048) pour l'indexation d'une vidéo.
 
### <a name="support-for-multi-lingual-projects"></a>Prise en charge des projets multilingues

Les projets peuvent maintenant être créés à partir de vidéos indexées dans différents langages (API uniquement).

## <a name="july-2019"></a>Juillet 2019

### <a name="editor-as-a-widget"></a>Éditeur en tant que widget

L’Éditeur IA Video Analyzer for Media est désormais disponible en tant que widget pour être incorporé dans les applications clientes.

### <a name="update-custom-language-model-from-closed-caption-file-from-the-portal"></a>Mise à jour du modèle de langage personnalisé à partir du fichier de sous-titres à partir du portail

Les clients peuvent fournir des fichiers aux formats VTT, SRT et TTML comme entrée pour les modèles de langage dans la page de personnalisation du portail.

## <a name="june-2019"></a>Juin 2019

### <a name="video-analyzer-for-media-deployed-to-japan-east"></a>Video Analyzer for Media est déployé dans la région Japon Est

Vous pouvez maintenant créer un compte Video Analyzer for Media payant dans la région Japon Est.

### <a name="create-and-repair-account-api-preview"></a>API de création et de réparation de compte (préversion)

Ajout d’une nouvelle API qui vous permet de [mettre à jour la clé ou le point de terminaison de connexion Azure Media Services](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Update-Paid-Account-Azure-Media-Services).

### <a name="improve-error-handling-on-upload"></a>Amélioration de la gestion des erreurs lors du chargement 

Un message descriptif est retourné en cas de configuration incorrecte du compte Azure Media Services sous-jacent.

### <a name="player-timeline-keyframes-preview"></a>Affichage d’un aperçu des images clés dans la chronologie du lecteur 

Vous pouvez maintenant afficher un aperçu de l’image pour chaque instant sur la chronologie du lecteur.

### <a name="editor-semi-select"></a>Semi-sélection dans l’éditeur

Vous pouvez maintenant afficher un aperçu de tous les insights qui sont sélectionnés suite au choix d’une période d’insight spécifique dans l’éditeur.

## <a name="may-2019"></a>Mai 2019

### <a name="update-custom-language-model-from-closed-caption-file"></a>Mise à jour du modèle de langage personnalisé à partir du fichier de sous-titres

Les API de [création de modèle de langage personnalisé](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Create-Language-Model) et de [mise à jour des modèles de langage personnalisés](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Update-Language-Model) prennent désormais en charge les formats de fichier VTT, SRT et TTML comme entrée pour les modèles de langage.

Quand vous appelez l’[API Update Video Transcript](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Update-Video-Transcript), la transcription est ajoutée automatiquement. Le modèle d’entraînement associé à la vidéo est également mis à jour automatiquement. Pour plus d’informations sur la façon de personnaliser et d’entraîner vos modèles de langage, consultez [Personnaliser un modèle de langage avec Video Analyzer for Media](customize-language-model-overview.md).

### <a name="new-download-transcript-formats--txt-and-csv"></a>Nouveaux formats de téléchargement de transcription – TXT et CSV

Outre le format de sous-titrage déjà pris en charge (SRT VTT et TTML), Video Analyzer for Media prend désormais en charge le téléchargement de la transcription aux formats TXT et CSV.

## <a name="next-steps"></a>Étapes suivantes

[Vue d'ensemble](video-indexer-overview.md)
