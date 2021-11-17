---
title: Présentation du service Visage Azure
titleSuffix: Azure Cognitive Services
description: Le service Visage Azure fournit les algorithmes d’intelligence artificielle que vous utilisez pour détecter, reconnaître et analyser les visages humains dans des images.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: overview
ms.date: 09/27/2021
ms.author: pafarley
ms.custom: cog-serv-seo-aug-2020
keywords: reconnaissance faciale, logiciel de reconnaissance faciale, analyse faciale, correspondance de visage, application de reconnaissance faciale, recherche de visage par image, recherche de reconnaissance faciale
ms.openlocfilehash: 370d6c17dfaf2d3544506e4e2ab4218f1a48d1fa
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132370799"
---
# <a name="what-is-the-azure-face-service"></a>Présentation du service Visage Azure

> [!WARNING]
> Le 11 juin 2020, Microsoft a annoncé qu’il ne vendra pas de technologie de reconnaissance faciale aux services de police des États-Unis tant qu’un règlement strict fondé sur les droits de l’homme n’aura pas été promulgué. Par conséquent, les clients ne doivent pas utiliser les fonctionnalités de reconnaissance faciale ni les fonctionnalités comprises dans les services Azure comme Visage ou Video Indexer, s’ils représentent, ou autorisent l’utilisation de ces services par ou pour, un service de police des États-Unis. Lorsque vous créez une ressource Visage, vous devez, dans le portail Azure, indiquer que vous avez compris et que vous acceptez de ne pas utiliser le service par ou pour un service de police aux États-Unis et que vous avez consulté la [documentation sur l’IA responsable (RAI)](../cognitive-services-apis-create-account-cli.md#prerequisites) et que vous utiliserez ce service conformément à cette documentation.

Le service Visage Azure fournit les algorithmes d’intelligence artificielle qui détectent, reconnaissent et analysent les visages humains dans des images. Le logiciel de reconnaissance faciale est important dans de nombreux scénarios, comme la vérification de l’identité, le contrôle d’accès sans contact et le floutage des visages à des fins de confidentialité.

Vérification de l’identité : vérifie l’identité d’une personne sur une carte d’identité émise par un gouvernement, comme un passeport ou un permis de conduire ou toute autre image d’inscription pour accorder l’accès à des services numériques ou physiques ou récupérer un compte. Les scénarios d’accès spécifiques incluent l’ouverture d’un nouveau compte, le contrôle d’un employé ou l’administration d’une évaluation en ligne. La vérification de l’identité peut être effectuée une seule fois quand une personne est intégrée, et à plusieurs reprises quand une personne accède à un service numérique ou physique.

Contrôle d’accès sans contact : par rapport aux méthodes actuelles comme les cartes ou les tickets, l’identification des visages par abonnement offre une expérience de contrôle d’accès améliorée tout en réduisant les risques liés à l’hygiène et à la sécurité, comme la perte, le vol ou le prêt d’une carte. La reconnaissance faciale aide le processus d’enregistrement incluant une personne physique pour l’enregistrement dans les aéroports, les stades, les parcs à thème ou les bâtiments, ainsi que les comptoirs d’accueil dans des bureaux, des hôpitaux, des gymnases, des clubs ou des écoles.

Édition des visages : éditer ou flouter les visages détectés de personnes enregistrées dans une vidéo afin de protéger leur confidentialité.

Cette documentation contient les types d’articles suivants :
* Les [guides de démarrage rapide](./Quickstarts/client-libraries.md) sont des instructions pas à pas qui vous permettent d’effectuer des appels au service et d’obtenir des résultats en peu de temps. 
* Les [guides patiques](./Face-API-How-to-Topics/HowtoDetectFacesinImage.md) contiennent des instructions sur l’utilisation du service de manière plus spécifique ou personnalisée.
* Les [articles conceptuels](./concepts/face-detection.md) fournissent des explications approfondies sur les fonctions et fonctionnalités du service.
* Les [tutoriels](./enrollment-overview.md) sont des guides plus longs qui montrent comment utiliser ce service en tant que composant dans des solutions métier plus larges.

## <a name="face-detection-and-analysis"></a>Détection et analyse des visages

La détection des visages est requise comme première étape dans tous les autres scénarios. L’API Détecter détecte les visages humains dans une image et retourne les coordonnées du rectangle de leur emplacement. Elle retourne également un ID unique qui représente les données de visage stockées, qui est utilisé dans les opérations ultérieures pour identifier ou vérifier les visages.

La détection des visages peut également permettre d’extraire un ensemble d’attributs associés au visage tels que la posture de la tête, l’âge, l’émotion, la pilosité du visage et les lunettes. Ces attributs sont des prédictions générales, pas des classifications réelles. Certains attributs sont utiles pour s’assurer que votre application obtient des données de visage de haute qualité lorsque les utilisateurs s’ajoutent à un service Visage (par exemple, votre application peut conseiller aux utilisateurs de retirer leurs lunettes de soleil s’ils en portent).

> [!NOTE]
> La fonctionnalité de détection des visages est également disponible par le biais du [service Vision par ordinateur](../computer-vision/overview.md). Toutefois, si vous voulez utiliser d’autres opérations Visage, comme Identifier, Vérifier, Rechercher semblables ou Regrouper, vous devez utiliser ce service à la place.

Pour plus d’informations sur la détection et l’analyse des visages, consultez l’article sur les concepts de la [détection des visages](concepts/face-detection.md). Consultez également la documentation de référence de l’[API de détection](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).


## <a name="identity-verification"></a>Vérification d’identité

Les entreprises et les applications modernes peuvent utiliser les opérations d’identification des visages et de vérification des visages pour vérifier qu’un utilisateur est bien celui qu’il prétend être. 

### <a name="identification"></a>Identification

L’identification de visage peut s’employer à résoudre une correspondance « un-à-plusieurs » d’un visage sur une image comparé à un ensemble de visages dans un référentiel sécurisé. Les candidats correspondants sont retournés en fonction de la similarité de leurs données de visage et le visage faisant l’objet de la requête. Ce scénario est utilisé pour octroyer des privilèges d’accès dans un bâtiment ou un aéroport à un certain groupe de personnes, ou pour vérifier l’identité de l’utilisateur d’un appareil.

L’image suivante est un exemple de base de données nommée `"myfriends"`. Chaque groupe peut contenir jusqu’à 1 million d’objets Personne différents. Chaque objet Personne peut avoir jusqu’à 248 visages enregistrés.

![Une grille avec 3 colonnes pour les différentes personnes, chacune avec 3 lignes d’images de visages](./Images/person.group.clare.jpg)

Après avoir créé et formé un groupe, vous pouvez effectuer l’identification par rapport au groupe avec un nouveau visage détecté. Si le visage est identifié en tant qu’une personne du groupe, l’objet Personne est renvoyé.

### <a name="verification"></a>Vérification

L’opération de vérification répond à la question « Ces deux visages appartiennent-ils à la même personne ? ». 

La vérification consiste également à établir une correspondance « unique » entre un visage sur une image et un seul visage provenant d’un dépôt sécurisé ou d’une photo pour vérifier qu’il s’agit de la même personne. La vérification peut être utilisée à des fins de vérification d’identité. Il peut s’agir par exemple d’une application bancaire qui permet aux utilisateurs d’ouvrir un compte à distance en prenant un selfie et en photographiant une pièce d’identité avec photo. 

Pour plus d’informations sur la vérification de l’identité, consultez le guide relatif aux concepts de la [reconnaissance faciale](concepts/face-recognition.md) ou la documentation de référence de l’API [Identifier](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) et [Vérifier](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).


## <a name="find-similar-faces"></a>Rechercher des visages semblables

L’opération Rechercher des visages semblables permet de mettre en correspondance un visage cible avec un ensemble de visages candidats, en cherchant à obtenir un plus petit ensemble de visages ressemblant au visage cible. Cette API s’avère utile pour effectuer une recherche de visage par image. 

Le service prend en charge deux modes de travail, **matchPerson** et **matchFace**. Le mode **matchPerson** renvoie des visages semblables après avoir filtré les résultats pour la même personne à l’aide de l’[API de vérification](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a). Le mode **matchFace** ignore le filtre de correspondance de personne. Il renvoie la liste des visages candidats semblables susceptibles ou non d’appartenir à la même personne.

L’exemple suivant montre le visage cible :

![Une femme qui sourit](./Images/FaceFindSimilar.QueryFace.jpg)

Et ces images sont les visages candidats :

![Cinq images de personnes qui sourient. Les images A et B correspondent à la même personne.](./Images/FaceFindSimilar.Candidates.jpg)

Pour rechercher quatre visages semblables, le mode **matchPerson** renvoie A et B, qui représentent le même visage que la cible. Le mode **matchFace** renvoie A, B, C et D, soit exactement quatre candidats, même si certains n’appartiennent pas à la même personne que le visage cible ou s’ils présentent une faible similarité. Pour plus d’informations, consultez le guide relatif aux concepts de la [reconnaissance faciale](concepts/face-recognition.md) ou la documentation de référence de l’[API de recherche de visages semblables](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237).

## <a name="group-faces"></a>Regrouper les visages

L’opération Regrouper divise un ensemble de visages inconnus en plusieurs petits groupes en fonction de leurs similarités. Chaque groupe est un sous-ensemble distinct de l’ensemble de visages d’origine. Elle retourne également un seul tableau « messyGroup » qui contient les ID de visage pour lesquels aucune similarité n’a été trouvée.

Tous les visages d’un groupe retourné sont susceptibles d’appartenir à la même personne, mais il peut y avoir plusieurs groupes pour une même personne. Ces groupes sont différenciés par un autre facteur, comme l’expression. Pour plus d’informations, consultez le guide relatif aux concepts de la [reconnaissance faciale](concepts/face-recognition.md) ou la documentation de référence de l’[API de groupe](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238).

## <a name="sample-app"></a>Exemple d'application

Les exemples d’applications suivants montrent différentes façons d’utiliser le service Visage :

- L’[application FamilyNotes UWP](https://github.com/Microsoft/Windows-appsample-familynotes) est une application Universal Windows Platform (UWP) qui utilise l’identification des visages, ainsi que la reconnaissance vocale, Cortana, l’écriture manuscrite et la caméra dans un scénario de partage de notes en famille.

## <a name="data-privacy-and-security"></a>Sécurité et confidentialité des données

Comme avec toutes les ressources Cognitive Services, les développeurs utilisant le service Visage doivent connaître les politiques de Microsoft relatives aux données client. Pour en savoir plus, consultez la [page Cognitive Services](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) dans le Centre de gestion de la confidentialité Microsoft.

## <a name="next-steps"></a>Étapes suivantes

Suivez un guide de démarrage rapide pour coder les composants de base d’une application de reconnaissance faciale dans le langage de votre choix.

- [Démarrage rapide : Bibliothèque de client](quickstarts/client-libraries.md).
