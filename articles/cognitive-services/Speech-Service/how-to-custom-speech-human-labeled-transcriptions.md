---
title: Recommandations concernant les transcriptions étiquetées à la main – Service Speech
titleSuffix: Azure Cognitive Services
description: Pour améliorer l’exactitude de la reconnaissance vocale, vous utilisez des transcriptions étiquetées à la main avec vos données audio. Cela est particulièrement utile lorsque des mots sont supprimés ou remplacés de manière incorrecte.
services: cognitive-services
author: eric-urban
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/12/2021
ms.author: eur
ms.openlocfilehash: 913d8e0ef9a0ae74db2530167e99cefc92e75bb5
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132397241"
---
# <a name="how-to-create-human-labeled-transcriptions"></a>Comment créer des transcriptions étiquetées à la main

Les transcriptions étiquetées à la main sont des transcriptions mot pour mot d’un fichier audio. Vous utilisez des transcriptions étiquetées à la main pour améliorer l’exactitude de la reconnaissance, en particulier lorsque les mots sont supprimés ou remplacés de manière incorrecte.

Un grand échantillon de données de transcription est nécessaire pour améliorer la reconnaissance. Nous vous suggérons de fournir entre 1 et 20 heures de données de transcription. Le service Speech utilisera jusqu’à 20 heures d’audio pour l’apprentissage. Dans cette page, nous allons passer en revue les principes à respecter pour créer des transcriptions de grande qualité. Ce guide est divisé par paramètres régionaux, avec des sections pour l’anglais des États-Unis, le chinois mandarin et l’allemand.

> [!NOTE]
> Tous les modèles de base ne prennent pas en charge la personnalisation avec les fichiers audio. Si un modèle de base ne prend pas cela en charge, l’apprentissage utilise simplement le texte des transcriptions, comme lors de l’utilisation du texte associé. Pour obtenir la liste des modèles de base prenant en charge l’entraînement avec des données audio, consultez les informations relatives à la [prise en charge des langues](language-support.md#speech-to-text).

> [!NOTE]
> Si vous changez le modèle de base utilisé pour l’entraînement et si vous avez du contenu audio dans le jeu de données d’entraînement, vérifiez *toujours* si le nouveau modèle de base sélectionné [prend en charge l’entraînement avec des données audio](language-support.md#speech-to-text). Si le modèle de base utilisé jusqu’à maintenant ne prend pas en charge l’entraînement avec des données audio, et si le jeu de données d’entraînement contient de l’audio, le temps d’entraînement du nouveau modèle de base va **considérablement** augmenter. Il peut facilement passer de plusieurs heures à plusieurs jours, voire davantage. Cela est particulièrement vrai si votre abonnement au service Speech ne se situe **pas** dans une [région disposant du matériel dédié](custom-speech-overview.md#set-up-your-azure-account) à l’entraînement.
>
> Si vous êtes confronté au problème décrit dans le paragraphe ci-dessus, vous pouvez rapidement faire baisser le temps d’entraînement en réduisant la quantité du contenu audio dans le jeu de données, ou en supprimant complètement le contenu audio pour ne garder que le texte. Cette dernière option est fortement recommandée si votre abonnement au service Speech ne se situe **pas** dans une [région disposant du matériel dédié](custom-speech-overview.md#set-up-your-azure-account) à l’entraînement.

## <a name="us-english-en-us"></a>Anglais (États-Unis, en-US)

Les transcriptions étiquetées à la main pour le contenu audio en anglais doivent être fournies sous forme de texte brut, uniquement avec des caractères ASCII. Évitez d’utiliser des caractères de ponctuation Latin-1 ou Unicode. Ces caractères sont souvent ajoutés par inadvertance pendant la copie de texte dans une application de traitement de texte la capture des données dans des pages web. Si ces caractères sont présents, veillez à les mettre à jour par une substitution ASCII appropriée.

Voici quelques exemples :

| Caractères à éviter | Substitution | Notes |
| ------------------- | ------------ | ----- |
| “Hello world” | "Hello world" | Les guillemets typographiques ouvrants et fermants ont été substitués par des caractères ASCII appropriés. |
| John’s day | John's day | L’apostrophe a été substituée par le caractère ASCII approprié. |
| It was good—no, it was great! | it was good--no, it was great! | Le tiret cadratin a été substitué par deux traits d’union. |

### <a name="text-normalization-for-us-english"></a>Normalisation du texte pour l’anglais des États-Unis

La normalisation du texte consiste à transformer les mots dans un format cohérent utilisé pendant l’entraînement d’un modèle. Si certaines règles de normalisation sont automatiquement appliquées au texte, nous vous recommandons cependant de suivre ces recommandations pendant la phase de préparation de données de transcription étiquetées à la main :

- Développez les abréviations.
- Écrivez les chaînes numériques non standard en lettres (comme les termes de comptabilité).
- Les caractères non alphabétiques ou les caractères alphanumériques mixtes doivent être retranscrits comme ils se prononcent.
- Les abréviations prononcées comme des mots n’ont pas besoin d’être modifiés (par exemple, « radar », « laser », « RAM » ou « OTAN »).
- Écrivez les abréviations dont les lettres se prononcent en les séparant avec un espace.
- Si vous utilisez l’audio, transcrivez les nombres en mots correspondant à l’audio (par exemple, « 101 » peut être prononcé « un zéro un » ou « cent un »).
- Évitez de répéter des caractères, des mots ou des groupes de mots plus de trois fois, par exemple « oui oui oui oui ». Les lignes avec de telles répétitions peuvent être supprimées par le service Speech.

Voici quelques exemples de normalisation à effectuer au niveau de la transcription :

| Texte d’origine               | Texte après la normalisation (à la main)      |
| --------------------------- | ------------------------------------- |
| Dr. Bruce Banner            | Docteur Bruce Banner                   |
| James Bond, 007             | James Bond, double oh seven           |
| Ke$ha                       | Kesha                                 |
| How long is the 2x4         | How long is the two by four           |
| The meeting goes from 1-3pm | The meeting goes from one to three pm |
| My blood type is O+         | My blood type is O positive           |
| Water is H20                | Water is H 2 O                        |
| Play OU812 by Van Halen     | Play O U 8 1 2 by Van Halen           |
| UTF-8 with BOM              | U T F 8 with BOM                      |
| It costs \$3.14             | It costs three fourteen               |

Les règles de normalisation suivantes sont automatiquement appliquées aux transcriptions :

- Utilisation de lettres minuscules
- Suppression de tous les signes de ponctuation, à l’exception des apostrophes dans les mots
- Écriture des nombres en lettres/forme parlée, comme les montants en dollars

Voici quelques exemples de normalisation effectuée automatiquement au niveau de la transcription :

| Texte d’origine                          | Texte après la normalisation (automatique) |
| -------------------------------------- | --------------------------------- |
| "Holy cow!" said Batman.               | holy cow said batman              |
| "What?" said Batman’s sidekick, Robin. | what said batman’s sidekick robin |
| Go get -em!                            | go get em                         |
| I’m double-jointed                     | I'm double jointed                |
| 104 Elm Street                         | one oh four Elm street            |
| Tune to 102.7                          | tune to one oh two point seven    |
| Pi is about 3.14                       | pi is about three point one four  |

## <a name="mandarin-chinese-zh-cn"></a>Mandarin chinois (zh-CN)

Les transcriptions étiquetées à la main pour le contenu audio en chinois mandarin doivent être encodées en UTF-8 avec un indicateur d’ordre des octets. Évitez l’utilisation des signes de ponctuation de demi-largeur. Ces caractères peuvent être inclus par inadvertance pendant la préparation des données dans un programme de traitement ou la récupération de données dans des pages web. Si ces caractères sont présents, veillez à les mettre à jour par une substitution appropriée avec des caractères à pleine chasse.

Voici quelques exemples :

| Caractères à éviter | Substitution   | Notes |
| ------------------- | -------------- | ----- |
| "你好" | "你好" | Les guillemets typographiques ouvrants et fermants ont été substitués par des caractères appropriés. |
| 需要什么帮助? | 需要什么帮助？| Le point d’interrogation a été substitué par un caractère approprié. |

### <a name="text-normalization-for-mandarin-chinese"></a>Normalisation de texte pour le chinois Mandarin

La normalisation du texte consiste à transformer les mots dans un format cohérent utilisé pendant l’entraînement d’un modèle. Si certaines règles de normalisation sont automatiquement appliquées au texte, nous vous recommandons cependant de suivre ces recommandations pendant la phase de préparation de données de transcription étiquetées à la main :

- Développez les abréviations.
- Écrivez les chaînes numériques sous leur forme orale.

Voici quelques exemples de normalisation à effectuer au niveau de la transcription :

| Texte d’origine | Texte après la normalisation |
| ------------- | ------------------------ |
| 我今年 21 | 我今年二十一 |
| 3 号楼 504 | 三号 楼 五 零 四 |

Les règles de normalisation suivantes sont automatiquement appliquées aux transcriptions :

- Suppression de tous les signes de ponctuation
- Écriture des nombres en forme parlée
- Conversion des lettres à pleine chasse en lettres à demi-chasse
- Utilisation de lettres majuscules pour tous les mots anglais

Voici quelques exemples de normalisation automatique de la transcription :

| Texte d’origine | Texte après la normalisation |
| ------------- | ------------------------ |
| 3.1415 | 三 点 一 四 一 五 |
| ￥ 3.5 | 三 元 五 角 |
| w f y z | W F Y Z |
| 1992 年 8 月 8 日 | 一 九 九 二 年 八 月 八 日 |
| 你吃饭了吗? | 你 吃饭 了 吗 |
| 下午 5:00 的航班 | 下午 五点 的 航班 |
| 我今年 21 岁 | 我 今年 二十 一 岁 |

## <a name="german-de-de-and-other-languages"></a>Allemand (de-DE) et autres langages

Les transcriptions étiquetées à la main pour le contenu audio en allemand (et dans d’autres langues que l’anglais et le chinois mandarin) doivent être encodées en UTF-8 avec un indicateur d’ordre des octets. Une transcription étiquetée à la main doit être fournie pour chaque fichier audio.

### <a name="text-normalization-for-german"></a>Normalisation de texte pour l’allemand

La normalisation du texte consiste à transformer les mots dans un format cohérent utilisé pendant l’entraînement d’un modèle. Si certaines règles de normalisation sont automatiquement appliquées au texte, nous vous recommandons cependant de suivre ces recommandations pendant la phase de préparation de données de transcription étiquetées à la main :

- Écrire les décimales sous la forme « , » et non « . ».
- Écrire des séparateurs d’heure sous la forme « : » et non « . » (par exemple : 12:00 Uhr).
- Les abréviations telles que « ca ». ne sont pas remplacées. Nous vous recommandons d’utiliser la forme parlée complète.
- Les quatre principaux opérateurs mathématiques (+, -, \*, /) sont supprimés. Nous vous recommandons de les remplacer par leur forme écrite : « plus », « minus », « mal » et « geteilt ».
- Les opérateurs de comparaison sont supprimés (=, <, et >). Nous vous recommandons de les remplacer par « gleich », « kleiner als » et « grösser als ».
- Écrivez les fractions, telles que 3/4, sous forme écrite (par exemple, « drei viertel » au lieu de 3/4).
- Remplacez le symbole « € » par la forme écrite « Euro ».

Voici quelques exemples de normalisation à effectuer au niveau de la transcription :

| Texte d’origine    | Texte après la normalisation utilisateur | Texte après la normalisation système       |
| ---------------- | ----------------------------- | ------------------------------------- |
| Es ist 12.23 Uhr | Es ist 12:23 Uhr              | es ist zwölf uhr drei und zwanzig uhr |
| {12.45}          | {12,45}                       | zwölf komma vier fünf                 |
| 2 + 3 - 4        | 2 plus 3 minus 4              | zwei plus drei minus vier             |

Les règles de normalisation suivantes sont automatiquement appliquées aux transcriptions :

- Utilisation de lettres minuscules pour tout le texte
- Suppression de toute la ponctuation, notamment les différents types de guillemets ("test", ’test’, "test„ et «test» sont acceptés)
- Suppression des lignes contenant un des caractères spéciaux suivants : ¢ ¤ ¥ ¦ § © ª ¬ ® ° ± ² µ × ÿ Ø¬¬
- Écriture des nombres en forme parlée, y compris les montants en dollars ou en euros
- Acceptation des trémas uniquement pour a, o et u. Les autres seront remplacées par « th » ou ignorés.

Voici quelques exemples de normalisation effectuée automatiquement au niveau de la transcription :

| Texte d’origine    | Texte après la normalisation |
| ---------------- | ------------------------ |
| Frankfurter Ring | frankfurter ring         |
| ¡Eine Frage!     | eine frage               |
| Wir, haben       | wir haben                |

### <a name="text-normalization-for-japanese"></a>Normalisation du texte pour le japonais

En japonais (ja-JP), il y a une longueur maximale de 90 caractères pour chaque phrase. Les lignes dont les phrases sont plus longues seront ignorées. Pour ajouter du texte plus long, insérez un point entre deux segments de phrase.

## <a name="next-steps"></a>Étapes suivantes

- [Préparer et tester les données](./how-to-custom-speech-test-and-train.md)
- [Inspecter les données](how-to-custom-speech-inspect-data.md)
- [Évaluer les données](how-to-custom-speech-evaluate-data.md)
- [Entraîner un modèle](how-to-custom-speech-train-model.md)
- [Déployer un modèle](./how-to-custom-speech-train-model.md)
