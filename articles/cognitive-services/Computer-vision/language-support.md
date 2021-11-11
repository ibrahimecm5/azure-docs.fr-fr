---
title: Prise en charge des langues – Vision par ordinateur
titleSuffix: Azure Cognitive Services
description: Cet article donne la liste des langages naturels pris en charge par les fonctionnalités Vision par ordinateur, OCR et Analyse d’image.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 10/27/2021
ms.author: pafarley
ms.openlocfilehash: a79954ba0a96f6db6f12af13c6c74d7386e88231
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131473411"
---
# <a name="language-support-for-computer-vision"></a>Prise en charge des langues pour la Vision par ordinateur

Certaines des fonctionnalités de la Vision par ordinateur prennent en charge plusieurs langues ; celles qui ne sont pas mentionnées ici ne gèrent que l’anglais.

## <a name="optical-character-recognition-ocr"></a>Reconnaissance optique des caractères (OCR)

Les API OCR Vision par ordinateur prennent en charge de nombreuses langues. L’API Read peut extraire du texte à partir d’images et de documents avec des langues mixtes, y compris sur une même ligne de texte, sans avoir besoin d’un paramètre de langue. Consultez la [Vue d’ensemble de la reconnaissance optique de caractères (OCR)](overview-ocr.md) pour plus d’informations.


> [!NOTE]
> **Code de langue facultatif**
>
> Les modèles universels basés sur le Deep Learning de l’OCR Read extraient tout le texte multilingue de vos documents, y compris les lignes de texte contenant plusieurs langues, et ne nécessitent pas la spécification d’un code de langue. N’indiquez pas de code de langue en tant que paramètre, sauf si vous êtes sûr de la langue et si vous souhaitez forcer le service à appliquer uniquement le modèle approprié. Sinon, le service pourrait retourner du texte incomplet et incorrect.

Le modèle en préversion comprend toutes les améliorations apportées à la version en disponibilité générale de l’API actuelle. Consultez [Comment spécifier la version du modèle](./Vision-API-How-to-Topics/call-read-api.md#determine-how-to-process-the-data-optional) pour utiliser les langages et fonctionnalités en préversion. 

### <a name="handwritten-languages"></a>Langues manuscrites

Le tableau suivant répertorie les langues manuscrites prises en charge par Read.

|Langage| Code langue (facultatif) | Lire |
|:-----|:----:|:-----|
|Anglais|`en`|✅ |
|Chinois (simplifié) |`zh-Hans`|✅ préversion |
|Français|`fr`|✅ préversion|
|Allemand |`de`|✅ préversion |
|Italien|`it`|✅ préversion |
|Portugais |`pt`|✅ préversion |
|Espagnol |`es`|✅ préversion |

### <a name="print-languages"></a>Langues d’impression

Le tableau suivant répertorie les langues d’impression prises en charge par les API d’OCR.

|Langage| Code langue (facultatif) | Lire | OCR |
|:-----|:----:|:-----|:---:|
|Afrikaans|`af`|✅ | |
|Albanais |`sq`|✅ | |
|Arabe | `ar`|  | ✅ |
|Asturien |`ast`|✅ | |
|Azerbaïdjanais (Latin) | `az` | ✅ préversion | |
|Basque  |`eu`| ✅ | |
|Biélorusse (cyrillique) | `be` |✅ préversion | |
|Biélorusse (latin) | `be` |✅ préversion | |
|Bichelamar   |`bi`|✅ | |
|Bosniaque (latin)   |`bs`|✅ préversion | |
|Breton    |`br`|✅ | |
|Bulgare |`bg`|✅ préversion | |
|Buryat (cyrillique)|`bua`|✅ préversion | |
|Catalan    |`ca`|✅ | |
|Cebuano    |`ceb`|✅ | |
|Chamorro  |`ch`|✅| |
|Chinois (simplifié) | `zh-Hans`|✅ |✅ |
|Chinois traditionnel | `zh-Hant`|✅ |✅ |
|Cornique     |`kw`|✅ | |
|Corse      |`co`|✅ | |
|Tatar de Crimée (latin)|`crh`| ✅ | |
|Croate |`hr`|✅ préversion | |
|Tchèque | `cs` |✅ | ✅ |
|Danois | `da` |✅ | ✅ |
|Néerlandais | `nl` |✅ |✅ |
|Anglais | `en` |✅ |✅|
|Erzya (Cyrillique) |`myv`|✅ préversion | |
|Estonien  |`et`|✅ | |
|Féroïen |`fo`|✅ préversion | |
|Fidjien |`fj`|✅ | |
|Filipino  |`fil`|✅ | |
|Finnois | `fi` |✅ |✅ |
|Français | `fr` |✅ |✅ |
|Frioulan  | `fur` |✅ | |
|Gagauz (Latin) |`gag`|✅ préversion | |
|Galicien   | `gl` |✅ | |
|Allemand | `de` |✅ |✅ |
|Gilbertin    | `gil` |✅ | |
|Grec | `el` | |✅ |
|Groenlandais   | `kl` |✅ | |
|Créole haïtien  | `ht` |✅ | |
|Hani  | `hni` |✅ | |
|Hawaïen |`haw`|✅ préversion | |
|Hmong daw (latin)| `mww` | ✅ | |
|Hongrois | `hu` | ✅ |✅ |
|Islandais |`is`|✅ préversion | |
|Inari Sami |`smn`|✅ préversion | |
|Indonésien   | `id` |✅ | |
|Interlingua  | `ia` |✅ | |
|Inuktitut (Latin) | `iu` | ✅ | |
|Irlandais    | `ga` |✅ | |
|Italien | `it` |✅ |✅ |
|Japonais | `ja` |✅ |✅ |
|Javanais | `jv` |✅ | |
|Quiché  | `quc` |✅ | |
|Créole du Cap-Vert | `kea` |✅ | |
|Kachin (latin) | `kac` |✅ | |
|Kara-Kalpak (Latin) | `kaa` | ✅ | |
|Kara-Kalpak (Cyrillique) | `kaa-cyrl` | ✅ préversion | |
|Karachay-Balkar |`krc`|✅ préversion | |
|Cachoube | `csb` |✅ | |
|Kazakh (Cyrillique) |`kk-cyrl`|✅ préversion | |
|Kazakh (Latin) |`kk-latn`|✅ préversion | |
|Khasi  | `kha` | ✅ | |
|Coréen | `ko` |✅ |✅ |
|Koryak |`kpy`|✅ préversion | |
|Kosrae |`kos`|✅ préversion | |
|Kumyk (Cyrillique) |`kum`|✅ préversion | |
|Kurde (latin)| `ku` |✅ | |
|Kirghiz (cyrillique) |`ky`|✅ préversion | |
|Lakota |`lkt`|✅ préversion | |
|Latin|`la`|✅ préversion | |
|Lituanien|`lt`|✅ préversion | |
|Bas sorabe|`dsb`|✅ préversion | |
|Lule Sami|`smj`|✅ préversion | |
|Luxembourgeois  | `lb` | ✅ | |
|Malais (latin) | `ms` | ✅ | |
|Maltais|`mt`|✅ préversion | |
|Mannois  | `gv` | ✅ | |
|Maori|`mi`|✅ préversion | |
|Mongole (cyrillique)|`mn`|✅ préversion | |
|Monténégrin (cyrillique)|`cnr-cyrl`|✅ préversion | |
|Montenegrin (latin)|`cnr-latn`|✅ préversion | |
|Napolitain   | `nap` | ✅ | |
|Niué|`niu`|✅ préversion | |
|Nogay|`nog`|✅ préversion | |
|Same du Nord (latin)|`sme`|✅ préversion | |
|Norvégien | `no` | ✅ | |
|Occitan | `oc` | ✅ | |
|Ossète|`os`|✅ préversion | |
|Polonais | `pl` | ✅ |✅ |
|Portugais | `pt` |✅ |✅ |
|Francique ripuaire|`ksh`|✅ préversion | |
|Roumain | `ro` | ✅ préversion | ✅|
|Romanche  | `rm` | ✅ | |
|Russe | `ru` |✅ préversion |✅ |
|Samoan (latin)|`sm`|✅ préversion | |
|Scots  | `sco` | ✅ | |
|Gaélique écossais  | `gd` |✅ | |
|Serbe (cyrillique) | `sr-cyrl` | |✅ |
|Serbe (latin) | `sr-latn` | ✅ préversion |✅ |
|Same de Skolt|`sms`|✅ préversion | |
|Slovaque | `sk` | ✅ préversion |✅ |
|Slovène  | `sl` | ✅ ||
|Same du Sud|`sma`|✅ préversion | |
|Espagnol | `es` |✅ |✅ |
|Swahili (latin)  | `sw` |✅ | |
|Suédois | `sv` |✅ |✅ |
|Tadjik (cyrillique)|`tg`|✅ préversion | |
|Tatar (latin)  | `tt` | ✅ |
|Tétoum    | `tet` |✅ |  |
|Tonga|`to`|✅ préversion | |
|Turc | `tr` |✅ | ✅ |
|Turkmène (latin)|`tk`|✅ préversion | |
|Touvain|`tyv`|✅ préversion | |
|Haut sorabe  | `hsb` |✅ |  |
|Ouzbek (cyrillique)  | `uz-cyrl` |✅ |  |
|Ouzbek (latin)     | `uz` |✅ |  |
|Volapük   | `vo` | ✅ | |
|Haut-valaisan    | `wae` | ✅ | |
|Gallois     | `cy` |✅ préversion |  |
|Frison occidental | `fy` | ✅ | |
|Yucatec Maya | `yua` | ✅ | |
|Zhuang | `za` |✅ |  |
|Zoulou  | `zu` | ✅ | |

## <a name="image-analysis"></a>Analyse d’image

Certaines fonctionnalités de l’API [Analyser : Image](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f21b) peuvent retourner des résultats dans d’autres langues, spécifiées avec le paramètre de requête `language`. D’autres donnent des résultats en anglais indépendamment de la langue spécifiée ; les dernières lèvent une exception pour les langues non prises en charge. Les actions sont spécifiées avec les paramètres de requête `visualFeatures` et `details` ; pour connaître la liste des actions possibles avec l’analyse d’image, voir la [Vue d’ensemble](overview-image-analysis.md). Les langues de marquage sont disponibles uniquement dans la version 3.2 ou ultérieure de l’API.

|Langage | Code langue | Catégories | Balises | Description | Adulte | Marques | Couleur | Visages | ImageType | Objets | Célébrités | Points de repère |
|:---|:---:|:----:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
|Arabe |`ar`| | ✅| |||||| |||
|Azeri (Azerbaijani) |`az`| | ✅| |||||| |||
|Bulgare |`bg`| | ✅| |||||| |||
|Bosniaque latin |`bs`| | ✅| |||||| |||
|Catalan |`ca`| | ✅| |||||| |||
|Tchèque |`cs`| | ✅| |||||| |||
|Gallois |`cy`| | ✅| |||||| |||
|Danois |`da`| | ✅| |||||| |||
|Allemand |`de`| | ✅| |||||| |||
|Grec |`el`| | ✅| |||||| |||
|Anglais |`en`|✅ | ✅| ✅|✅|✅|✅|✅|✅|✅|✅|✅|
|Espagnol |`es`|✅ | ✅| ✅|||||| |✅|✅|
|Estonien |`et`| | ✅| |||||| |||
|Basque |`eu`| | ✅| |||||| |||
|Finnois |`fi`| | ✅| |||||| |||
|Français |`fr`| | ✅| |||||| |||
|Irlandais |`ga`| | ✅| |||||| |||
|Galicien |`gl`| | ✅| |||||| |||
|Hébreu |`he`| | ✅| |||||| |||
|Hindi |`hi`| | ✅| |||||| |||
|Croate |`hr`| | ✅| |||||| |||
|Hongrois |`hu`| | ✅| |||||| |||
|Indonésien |`id`| | ✅| |||||| |||
|Italien |`it`| | ✅| |||||| |||
|Japonais  |`ja`|✅ | ✅| ✅|||||| |✅|✅|
|Kazakh |`kk`| | ✅| |||||| |||
|Coréen |`ko`| | ✅| |||||| |||
|Lituanien |`It`| | ✅| |||||| |||
|Letton |`Iv`| | ✅| |||||| |||
|Macédonien |`mk`| | ✅| |||||| |||
|Malais (Malaisie) |`ms`| | ✅| |||||| |||
|Norvégien (bokmål) |`nb`| | ✅| |||||| |||
|Néerlandais |`nl`| | ✅| |||||| |||
|Polonais |`pl`| | ✅| |||||| |||
|Dari |`prs`| | ✅| |||||| |||
| Portugais (Brésil)|`pt-BR`| | ✅| |||||| |||
| Portugais (Portugal) |`pt`/`pt-PT`|✅ | ✅| ✅|||||| |✅|✅|
|Roumain |`ro`| | ✅| |||||| |||
|Russe |`ru`| | ✅| |||||| |||
|Slovaque |`sk`| | ✅| |||||| |||
|Slovène |`sl`| | ✅| |||||| |||
|Serbe (cyrillique) |`sr-Cryl`| | ✅| |||||| |||
|Serbe (Latin) |`sr-Latn`| | ✅| |||||| |||
|Suédois |`sv`| | ✅| |||||| |||
|Thaï |`th`| | ✅| |||||| |||
|Turc |`tr`| | ✅| |||||| |||
|Ukrainien |`uk`| | ✅| |||||| |||
|Vietnamien |`vi`| | ✅| |||||| |||
|Chinois (simplifié) |`zh`/ `zh-Hans`|✅ | ✅| ✅|||||| |✅|✅|
|Chinois traditionnel |`zh-Hant`| | ✅| |||||| |||
