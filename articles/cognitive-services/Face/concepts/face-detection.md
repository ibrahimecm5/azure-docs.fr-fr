---
title: Concepts de détection et d’attributs de visage
titleSuffix: Azure Cognitive Services
description: Apprenez-en davantage sur la détection des visages. La détection des visages est l’action visant à localiser des visages humains dans une image et à retourner, si vous le souhaitez, différents types de données liées aux visages.
services: cognitive-services
author: PatrickFarley
manager: nitime
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 10/27/2021
ms.author: pafarley
ms.openlocfilehash: 262de9199d1572130147895e972355daf4ecafbc
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131450388"
---
# <a name="face-detection-and-attributes"></a>Détection et attributs de visage

Cet article explique les concepts de détection des visages et des données d’attribut de visage. La détection des visages est l’action visant à localiser des visages humains dans une image et à retourner, si vous le souhaitez, différents types de données liées aux visages.

Vous utilisez l’opération [Visage - Détecter](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) pour détecter les visages dans une image. Au minimum, chaque visage détecté correspond à un champ faceRectangle dans la réponse. Cet ensemble de coordonnées de pixels left (gauche), top (haut), width (largeur) et height (hauteur) marque le visage localisé. Ces coordonnées vous permettent d’obtenir l’emplacement du visage et sa taille. Dans la réponse de l’API, les visages sont répertoriés par ordre de taille, du plus grand au plus petit.

## <a name="face-id"></a>ID du visage

L’ID de visage est une chaîne d’identification unique pour chaque visage détecté dans une image. Vous pouvez demander un ID de visage dans votre appel d’API [Visage - Détecter](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

## <a name="face-landmarks"></a>Points de repère du visage

Les points de repère de visage sont un ensemble de points faciles à trouver sur un visage, tels que les pupilles ou la pointe du nez. Il existe 27 points de repères prédéfinis par défaut. La figure suivante montre l’ensemble des 27 points :

![Schéma de visage avec les 27 points de repère étiquetés](../Images/landmarks.1.jpg)

Les coordonnées des points sont retournées en unités de pixels.

Le modèle Detection_03 a actuellement la détection de points de repère la plus précise. Les points de repère des yeux et des pupilles qu’il renvoie sont suffisamment précis pour permettre le suivi du regard sur le visage.

## <a name="attributes"></a>Attributs

Les attributs sont un ensemble de fonctionnalités qui peuvent éventuellement être détectées par l’API [Visage - Détecter](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236). Les attributs suivants peuvent être détectés :

* **Accessories**. Indique si le visage donné porte des accessoires. Cet attribut retourne les accessoires possibles, y compris casques, lunettes et masques, avec un score de confiance entre zéro et un pour chaque accessoire.
* **age**. L’âge estimé en années d’un visage particulier.
* **blur**. L’aspect flouté du visage dans l’image. Cet attribut retourne une valeur comprise entre 0 et 1, ainsi qu’une évaluation informelle low (faible), medium (moyenne) ou high (élevée).
* **emotion**. Une liste d’émotions avec leur confiance de détection pour le visage donné. Les scores de confiance sont normalisés et la somme des scores de toutes les émotions est égale à 1. Les émotions détectées sont le bonheur, la tristesse, la neutralité, la colère, le mépris, le dégoût, la surprise et la peur.
* **exposure**. L’exposition du visage dans l’image. Cet attribut retourne une valeur comprise entre 0 et 1, ainsi qu’une évaluation informelle underExposure (sous-exposition), goodExposure (bonne exposition) ou overExposure (surexposition).
* **facialHair**. La présence estimée de pilosité faciale et sa longueur pour le visage donné.
* **gender**. Le sexe estimé de la personne détentrice du visage donné. Les valeurs possibles sont male (homme), female (femme) et genderless (sexe indéterminé).
* **glasses**. Indique si le visage donné porte des lunettes. Les valeurs possibles sont NoGlasses (pas de lunettes), ReadingGlasses (lunettes de lecture), Sunglasses (lunettes de soleil) et Swimming Goggles (lunettes de natation).
* **hair**. Type de cheveux du visage. Cet attribut indique si les cheveux sont visibles, si une calvitie est détectée et les couleurs de cheveux détectées.
* **headPose**. Orientation du visage dans l’espace 3D. Cet attribut est décrit par les angles d’inclinaison latérale (roll), de lacet (yaw) et d’inclinaison longitudinale (pitch), en degrés, qui sont définis en fonction de la [règle de droite](https://en.wikipedia.org/wiki/Right-hand_rule). L’ordre de trois angles est inclinaison latérale-lacet-inclinaison longitudinale, et la plage de valeurs de chaque angle est comprise entre -180 degrés et 180 degrés. L’orientation 3D du visage est estimée par les angles d’inclinaison latérale (roll), de lacet (yaw) et d’inclinaison longitudinale, dans cet ordre. Consultez le diagramme suivant pour les mappages des angles :

    ![Tête avec les axes d’inclinaison longitudinale (pitch), d’inclinaison latérale (roll) et de lacet (yaw) étiquetés](../Images/headpose.1.jpg)
* **makeup**. Indique si le visage comporte du maquillage. Cet attribut retourne une valeur booléenne pour eyeMakeup (maquillage des yeux) et lipMakeup (maquillage des lèvres).
* **mask**.  Indique si le visage porte un masque. Cet attribut retourne un type de masque possible et une valeur booléenne pour indiquer si le nez et la bouche sont couverts.
* **noise**. Le bruit visuel détecté dans l’image du visage. Cet attribut retourne une valeur comprise entre 0 et 1, ainsi qu’une évaluation informelle low (faible), medium (moyenne) ou high (élevée).
* **occlusion**. Indique si des objets obstruent des parties du visage. Cet attribut retourne une valeur booléenne pour eyeOccluded (obstruction des yeux), foreheadOccluded (obstruction du front) et mouthOccluded (obstruction de la bouche).
* **smile**. L’expression de sourire du visage donné. Cette valeur est comprise entre 0 pour aucun sourire et 1 pour un sourire clair.
* **QualityForRecognition** Qualité d’image globale indiquant si l’image utilisée dans la détection est d’une qualité suffisante pour tenter une reconnaissance faciale. La valeur est une évaluation informelle de faible, moyenne ou élevée. Seules des images de qualité « élevée » sont recommandées pour l’inscription de personnes, et une qualité supérieure à « moyenne » est recommandée pour les scénarios d’identification.
    >[!NOTE]
    > La disponibilité de chaque attribut dépend du modèle de détection spécifié. L’attribut QualityForRecognition dépend également du modèle de reconnaissance, car il n’est actuellement disponible que lors de l’utilisation d’une combinaison de modèle de détection detection_01 ou detection_03, et du modèle de reconnaissance recognition_03 ou recognition_04.

> [!IMPORTANT]
> Les attributs de visage sont prévus via l’utilisation d’algorithmes statistiques. Ils peuvent ne pas toujours être exacts. Soyez prudent lorsque vous prenez des décisions basées sur les données d’attribut.

## <a name="input-data"></a>Données d’entrée

Utilisez les conseils suivants pour vous assurer que vos images d’entrée fournissent les résultats de détection les plus précis :

* Les formats d’image d’entrée pris en charge sont JPEG, PNG, GIF (la première image) et BMP. 
* La taille du fichier image ne doit pas dépasser 6 Mo.
* La taille de visage minimale détectable est de 36 x 36 pixels dans une image dont la taille n’est pas supérieure à 1920 x 1080 pixels. Les images dont la taille est supérieure à 1920 x 1080 pixels ont une taille de visage minimale proportionnellement supérieure. La réduction de la taille du visage peut entraîner la non-détection de certains visages, même si leur taille est supérieure à la taille de visage minimale détectable.
* La taille de visage maximale détectable est de 4096 x 4096 pixels.
* Les visages dont la taille n’est pas comprise entre 36 x 36 et 4096 x 4096 pixels ne sont pas détectés.
* Certains visages peuvent ne pas être reconnus en raison de défis techniques, tels que :
  * Des images avec un éclairage extrême, par exemple, un contre-jour marqué.
  * Des obstacles cachant un œil ou les deux yeux.
  * Des différences de type de cheveux ou de pilosité faciale.
  * Des changements d’apparence du visage dus à l’âge.
  * Une très grande expressivité du visage.

### <a name="input-data-with-orientation-information"></a>Données d’entrée avec les informations d’orientation :

Certaines images d’entrée au format JPEG peuvent contenir des informations d’orientation dans les métadonnées EXIF (Exchangeable Image File Format). Si l’orientation EXIF est disponible, les images sont automatiquement pivotées dans l’orientation appropriée avant d’être envoyées pour une détection de visages. Le rectangle de visage, les points de repère et la posture de tête de chaque visage détecté sont estimés en fonction de l’image pivotée.

Pour afficher correctement le rectangle de visage et les points de repère, vous devez vérifier que l’image est correctement pivotée. La plupart des outils de visualisation d’image effectuent par défaut une rotation automatique de l’image en fonction de son orientation EXIF. Pour d’autres outils, vous devrez peut-être appliquer la rotation à l’aide de votre propre code. Les exemples suivants montrent un rectangle de visage sur une image pivotée (à gauche) et une image non pivotée (à droite).

![Deux images de visage avec et sans rotation](../Images/image-rotation.png)

### <a name="video-input"></a>Entrée vidéo

Si vous détectez les visages à partir d’un flux vidéo, vous pouvez éventuellement améliorer les performances en ajustant certains paramètres de votre caméra vidéo :

* **Lissage** : De nombreuses caméras vidéo appliquent un effet de lissage. Vous devez désactiver cette option si vous le pouvez, car elle crée un effet de flou entre les images et réduit la clarté.
* **Vitesse d’obturation** : Une plus grande vitesse d’obturation réduit l’ampleur des mouvements entre les images et rend chaque image plus claire. Nous vous recommandons une vitesse d’obturation de 1/60 seconde ou plus.
* **Angle d’obturation** : Certaines caméras spécifient un angle d’obturation à la place d’une vitesse d’obturation. Vous devez utiliser un angle d’obturation inférieur si possible. Cela génère des images vidéo plus claires.

    >[!NOTE]
    > Une caméra avec un angle d’obturation inférieur recevra moins de lumière pour chaque image et l’image sera plus sombre. Vous devrez déterminer le niveau approprié à utiliser.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous êtes familiarisé avec les concepts de détection des visages, découvrez comment écrire un script pour détecter les visages dans une image donnée.

* [Appeler l’API de détection](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md)
