---
title: Préparer des données pour Custom Speech - Service Speech
titleSuffix: Azure Cognitive Services
description: Lors du test de la précision de la reconnaissance vocale Microsoft ou de l’apprentissage de vos modèles personnalisés, vous aurez besoin de données audio et texte. Dans cette page, nous nous intéressons aux types de données, à la façon dont ils sont utilisés et à leur gestion.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/08/2021
ms.author: pafarley
ms.custom: ignite-fall-2021
ms.openlocfilehash: d73f17d3e3eb8d5511dcb98c6a074a73120eaf06
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131080609"
---
# <a name="prepare-data-for-custom-speech"></a>Préparer des données pour Custom Speech

Lors du test de la précision de la reconnaissance vocale Microsoft ou de l’apprentissage de vos modèles personnalisés, vous aurez besoin de données audio et texte. Dans cette page, nous abordons les types de données requis par un modèle Custom Speech.

## <a name="data-diversity"></a>Diversité des données

Le texte et l’audio utilisés pour tester et entraîner un modèle personnalisé doivent inclure des échantillons issus d’un ensemble diversifié de haut-parleurs et de scénarios que votre modèle doit reconnaître.
Tenez compte des facteurs suivants lors de la collecte de données pour le test et l’entraînement de modèles personnalisés :

* Vos données texte et audio doivent couvrir les types d’instructions verbales que vos utilisateurs prononceront lorsqu’ils interagiront avec votre modèle. Par exemple, un modèle chargé d’élever et d’abaisser la température nécessite un entraînement sur des instructions possibles permettant de demander de telles modifications.
* Vos données doivent inclure toutes les variantes de langage que votre modèle doit reconnaître. De nombreux facteurs peuvent faire varier l’audio, y compris les accents, les dialectes, le mélange de langues, l’âge, le sexe, la hauteur de la voix, le degré de stress et l’heure de la journée.
* Vous devez inclure des échantillons provenant de différents environnements (en intérieur, en extérieur, avec bruits de route) où votre modèle sera utilisé.
* Le contenu audio doit être collecté à l’aide des périphériques matériels que le système de production utilisera. Si votre modèle doit identifier la parole enregistrée sur des appareils d’enregistrement de diverses qualités, les données audio que vous fournissez pour entraîner votre modèle doivent également être caractéristiques de ces différents scénarios.
* Vous pouvez ajouter ultérieurement des données à votre modèle, mais veillez à utiliser un jeu de données diversifié et représentatif des besoins de votre projet.
* L’insertion de données qui *ne figurent pas* dans les besoins de reconnaissance de modèle personnalisé peut nuire à la qualité globale de la reconnaissance. N’insérez donc pas de données dont votre modèle n’a pas besoin pour transcrire.

Un modèle entraîné dans le cadre d’un sous-ensemble de scénarios ne peut fonctionner correctement que dans ces scénarios. Choisissez soigneusement les données qui représentent l’étendue complète des scénarios que votre modèle personnalisé doit reconnaître.

> [!TIP]
> Commencez avec de petits ensembles d’exemples de données correspondant à la langue et à l’acoustique que votre modèle rencontrera.
> Par exemple, enregistrez un petit échantillon représentatif du contenu audio sur le même matériel et dans le même environnement acoustique que votre modèle rencontrera dans les scénarios de production.
> De petits jeux de données représentatifs peuvent exposer des problèmes avant que vous ayez investi dans la collecte de jeux de données beaucoup plus volumineux à des fins d’entraînement.
>
> Pour commencer rapidement, vous pouvez utiliser des exemples de données. Consultez ce dépôt GitHub pour un <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/sampledata/customspeech" target="_target">exemple de données Custom Speech </a>

## <a name="data-types"></a>Types de données

Ce tableau liste les types de données acceptés, les cas d’utilisation pour chacun d’eux ainsi que la quantité recommandée. Pour créer un modèle, vous n’avez pas besoin de chaque type de données. Les données nécessaires varient selon que vous créez un test ou entraînez un modèle.

| Type de données | Utilisé pour le test | Quantité recommandée | Utilisé pour l’entraînement | Quantité recommandée |
|-----------|-----------------|----------|-------------------|----------|
| [Audio](#audio-data-for-testing) | Oui<br>Utilisé pour l’inspection visuelle | 5 fichiers audio et plus | Non | N/A |
| [Transcriptions audio + étiquetées à la main](#audio--human-labeled-transcript-data-for-trainingtesting) | Oui<br>Utilisé pour évaluer la précision | 0,5 - 5 heures d’audio | Oui | 1 à 20 heures d’audio |
| [Texte brut](#plain-text-data-for-training) | Non | n/a | Oui | 1 – 200 Mo de texte associé |
| [Texte structuré](#structured-text-data-for-training-public-preview) (préversion publique) | Non | n/a | Oui | Jusqu’à 20 classes avec jusqu’à 2 000 éléments et jusqu’à 50 000 phrases d’apprentissage |
| [Prononcer](#pronunciation-data-for-training) | Non | n/a | Oui | 1 ko à 1 Mo de texte de prononciation |

Les fichiers doivent être regroupées par type dans un jeu de données et chargés sous forme de fichier .zip. Chaque jeu de données ne peut contenir qu’un seul type de données.

> [!TIP]
> Lorsque vous effectuez l’apprentissage d’un nouveau modèle, commencez par des données de texte brut ou de structuré. Ces données permettront d’améliorer la reconnaissance de termes et phrases spécifiques. L’apprentissage avec du texte est beaucoup plus rapide que l’apprentissage avec audio (quelques minutes au lieu de quelques jours).

> [!NOTE]
> Tous les modèles de base ne prennent pas en charge l’audio. Si un modèle de base ne le prend pas en charge, le service vocal utilise uniquement le texte des transcriptions et ignore l’audio. Pour obtenir la liste des modèles de base prenant en charge l’entraînement avec des données audio, consultez les informations relatives à la [prise en charge des langues](language-support.md#speech-to-text). Même si un modèle de base prend en charge l’apprentissage avec des données audio, il est possible que le service n’utilise qu’une partie de l’audio. Il utilisera néanmoins toutes les transcriptions.
>
> Si vous changez le modèle de base utilisé pour l’entraînement et si vous avez du contenu audio dans le jeu de données d’entraînement, vérifiez *toujours* si le nouveau modèle de base sélectionné [prend en charge l’entraînement avec des données audio](language-support.md#speech-to-text). Si le modèle de base utilisé jusqu’à maintenant ne prend pas en charge l’entraînement avec des données audio, et si le jeu de données d’entraînement contient de l’audio, le temps d’entraînement du nouveau modèle de base va **considérablement** augmenter. Il peut facilement passer de plusieurs heures à plusieurs jours, voire davantage. Cela est particulièrement vrai si votre abonnement au service Speech ne se situe **pas** dans une [région disposant du matériel dédié](custom-speech-overview.md#set-up-your-azure-account) à l’entraînement.
>
> Si vous êtes confronté au problème décrit dans le paragraphe ci-dessus, vous pouvez rapidement faire baisser le temps d’entraînement en réduisant la quantité du contenu audio dans le jeu de données, ou en supprimant complètement le contenu audio pour ne garder que le texte. Cette dernière option est fortement recommandée si votre abonnement au service Speech ne se situe **pas** dans une [région disposant du matériel dédié](custom-speech-overview.md#set-up-your-azure-account) à l’entraînement.
>
> Dans les régions avec un matériel dédié pour l’apprentissage, le service Speech utilise jusqu’à 20 heures d’audio pour l’apprentissage. Dans d’autres régions, il n’utilise que jusqu’à 8 heures d’audio.

> [!NOTE]
> L’apprentissage avec du texte structuré est pris en charge uniquement pour les paramètres régionaux en-US, en-UK, en-IN, de-DE, fr-FR, fr-CA, es-ES, es-MX, et vous devez utiliser le modèle de base le plus récent pour ces paramètres régionaux. 
>
> Pour les paramètres régionaux qui ne prennent pas en charge la formation avec du texte structuré, le service prend toutes les phrases de formation qui ne font pas référence à des classes dans le cadre de l’apprentissage avec des données de texte brut.

## <a name="upload-data"></a>Charger des données

Pour charger vos données, accédez à [Speech Studio ](https://aka.ms/speechstudio/customspeech). Après avoir créé un projet, accédez à l’onglet **Jeux de données Speech**, puis cliquez sur **Charger des données** pour lancer l’Assistant et créer votre premier jeu de données. Sélectionnez un type de données vocal pour votre jeu de données, puis chargez vos données.

> [!NOTE]
> Si la taille de fichier de votre jeu de données dépasse 128 Mo, vous ne pouvez le charger qu’en utilisant l’option *Azure Blob ou emplacement partagé*. Vous pouvez également utiliser l’[API REST de reconnaissance vocale v3.0](rest-speech-to-text.md#speech-to-text-rest-api-v30) pour charger un jeu de données de [n’importe quelle taille autorisée](speech-services-quotas-and-limits.md#model-customization). Pour plus d’informations, consultez [la section suivante](#upload-data-using-speech-to-text-rest-api-v30).

Tout d’abord, vous devez spécifier si le jeu de données doit être utilisé pour l’**apprentissage** ou le **test**. Il existe de nombreux types de données qui peuvent être chargés et utilisés à des fins d’**apprentissage** ou de **test**. Chaque jeu de données que vous chargez doit être correctement formaté et doit respecter les exigences associées au type de données que vous choisissez. Les exigences sont listées dans les sections suivantes.

Une fois que votre jeu de données est chargé, vous disposez de plusieurs options :

* Vous pouvez accéder à l’onglet **Effectuer l’apprentissage de modèles personnalisés** pour effectuer l’apprentissage d’un modèle personnalisé.
* Vous pouvez accéder à l’onglet **Tester des modèles** pour inspecter visuellement la qualité avec des données audio uniquement ou évaluer la précision avec des données audio + transcription étiquetée à la main.

### <a name="upload-data-using-speech-to-text-rest-api-v30"></a>Charger des données à l’aide de l’API REST de reconnaissance vocale v3.0

Vous pouvez utiliser l’[API REST de reconnaissance vocale v3.0](rest-speech-to-text.md#speech-to-text-rest-api-v30) pour automatiser toutes les opérations liées à vos modèles personnalisés. En particulier, vous pouvez l’utiliser pour charger un jeu de données. Cette fonction est particulièrement utile lorsque le fichier de votre jeu de données dépasse 128 Mo, car les fichiers de cette taille ne peuvent pas être chargés à l’aide de l’option *Fichier local* dans Speech Studio. (Vous pouvez également utiliser l’option *Azure Blob ou emplacement partagé* dans Speech Studio dans le même but, comme décrit dans la section précédente.)

Utilisez l’une des requêtes suivantes pour créer et charger un jeu de données :
* [Créer un jeu de données](https://centralus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/CreateDataset)
* [Créer un jeu de données à partir d’un formulaire](https://centralus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/UploadDatasetFromForm)

**Jeux de données créés par l’API REST et projets Speech Studio**

Un jeu de données créé avec l’API REST de reconnaissance vocale v3.0 ne sera connecté à *aucun* des projets Speech Studio, sauf si un paramètre spécial est spécifié dans le corps de la demande (voir ci-dessous). La connexion à un projet Speech Studio n’est *pas* requise pour les opérations de personnalisation de modèle si elles sont effectuées via l’API REST.

Lorsque vous vous connectez à Speech Studio, son interface utilisateur vous avertit en cas de découverte d’un objet non connecté (comme des jeux de données chargés via l’API REST sans référence à un projet) et vous propose de connecter ces objets à un projet existant. 

Pour connecter le nouveau jeu de données à un projet existant dans Speech Studio pendant son chargement, utilisez [Créer un jeu de données](https://centralus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/CreateDataset) ou [Créer un jeu de données à partir d’un formulaire](https://centralus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/UploadDatasetFromForm) et renseignez le corps de la demande en respectant le format suivant :
```json
{
  "kind": "Acoustic",
  "contentUrl": "https://contoso.com/mydatasetlocation",
  "locale": "en-US",
  "displayName": "My speech dataset name",
  "description": "My speech dataset description",
  "project": {
    "self": "https://westeurope.api.cognitive.microsoft.com/speechtotext/v3.0/projects/c1c643ae-7da5-4e38-9853-e56e840efcb2"
  }
}
```

L’URL du projet requise pour l’élément `project` peut être obtenue à l’aide de la requête [Get Projects](https://westeurope.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetProjects).

## <a name="audio--human-labeled-transcript-data-for-trainingtesting"></a>Données audio + transcription étiquetée à la main pour l’apprentissage/le test

Les données audio + transcription étiquetée à la main peuvent être utilisées à des fins de formation et de test. Pour améliorer les aspects acoustiques, tels que les légers accents, les styles d’élocution, les bruits de fond, ou pour mesurer la précision de la reconnaissance vocale de Microsoft lors du traitement de vos fichiers audio, vous devez fournir des transcriptions étiquetées à la main (mot à mot) à des fins de comparaison. Si la transcription étiquetée à la main prend souvent beaucoup de temps, elle est nécessaire pour évaluer la précision et entraîner le modèle pour vos cas d’usage. Gardez à l’esprit que les améliorations de la reconnaissance seront proportionnelles à la qualité des données fournies. C’est pourquoi il est important de charger uniquement des transcriptions de grande qualité.

Les fichiers audio peuvent avoir un silence au début et à la fin de l’enregistrement. Si possible, incluez au moins une demi-seconde de silence avant et après Speech dans chaque exemple de fichier. Bien que les données audio avec un faible volume d’enregistrement ou un bruit d’arrière-plan perturbateur ne soient pas utiles, elles ne doivent pas nuire à votre modèle personnalisé. Envisagez toujours de mettre à niveau vos micro et votre matériel de traitement du signal avant de rassembler les échantillons audio.

| Propriété                 | Valeur                               |
|--------------------------|-------------------------------------|
| Format de fichier              | RIFF (WAV)                          |
| Échantillonnage              | 8 000 Hz ou 16 000 Hz               |
| Canaux                 | 1 (mono)                            |
| Longueur maximale par fichier audio | 2 heures (test) /60 s (entraînement) |
| Format d’échantillonnage            | PCM, 16 bits                         |
| Format d’archive           | .zip                                |
| Taille maximale de zip         | 2 Go                                |

[!INCLUDE [supported-audio-formats](includes/supported-audio-formats.md)]

> [!NOTE]
> Lors du chargement de données de formation et de test, la taille du fichier .zip ne peut pas dépasser 2 Go. Vous ne pouvez effectuer des tests qu’à partir d’un *seul* jeu de données : veillez donc à ce que sa taille de fichier reste appropriée. De plus, chaque fichier d’entraînement ne peut pas dépasser 60 secondes. Dans le cas contraire, il génère une erreur.

Pour résoudre les problèmes comme la suppression ou la substitution de mots, une quantité importante de données est nécessaire pour améliorer la reconnaissance. En règle générale, il est recommandé de fournir des transcriptions mot par mot pour environ 1 à 20 heures d’audio. Toutefois, même 30 minutes peuvent contribuer à améliorer les résultats de la reconnaissance. Les transcriptions de tous les fichiers WAV doivent se trouver dans un seul fichier en texte brut. Chaque ligne du fichier de transcription doit contenir le nom d’un des fichiers audio, suivi de la transcription correspondante. Le nom de fichier et la transcription doivent être séparés par une tabulation (\t).

Par exemple :

<!-- The following example contains tabs. Don't accidentally convert these into spaces. -->

```input
speech01.wav    speech recognition is awesome
speech02.wav    the quick brown fox jumped all over the place
speech03.wav    the lazy dog was not amused
```

> [!IMPORTANT]
> La transcription doit être encodée au format UTF-8 marque d'ordre d'octet (BOM).

Les transcriptions doivent être en texte normalisé pour pouvoir être traitées par le système. Une normalisation importante doit cependant être effectuée par l’utilisateur avant de charger les données dans le Studio Speech. Pour savoir quelle langue utiliser pour la préparation de vos transcriptions, consultez [Guide pratique pour créer une transcription étiquetée à la main](how-to-custom-speech-human-labeled-transcriptions.md).

Une fois que vous avez regroupé vos fichiers audio et les transcriptions correspondantes, vous devez les packager dans un seul fichier .zip avant de charger ce dernier vers <a href="https://speech.microsoft.com/customspeech" target="_blank">Speech Studio </a>. Voici un exemple de jeu de données constitué de trois fichiers audio et d’un fichier de transcriptions étiquetées à la main :

> [!div class="mx-imgBorder"]
> ![Sélectionner du contenu audio à partir du portail Speech](./media/custom-speech/custom-speech-audio-transcript-pairs.png)

Consultez [Configurer votre compte Azure](custom-speech-overview.md#set-up-your-azure-account) pour obtenir la liste des régions recommandées pour vos abonnements au service Speech. La configuration des abonnements Speech dans une de ces régions permet de réduire le temps nécessaire à la formation du modèle. Dans ces régions, l’apprentissage peut traiter environ 10 heures d’audio par jour, contre 1 heure par jour dans d’autres régions. Si l’apprentissage du modèle ne peut pas être effectué en une semaine, le modèle est marqué comme ayant échoué.

Tous les modèles de base ne prennent pas en charge les données audio. Si le modèle de base ne les prend pas en charge, le service ignore l’audio et effectue simplement un apprentissage avec le texte des transcriptions. Dans ce cas, l’apprentissage est le même que celui avec du texte associé. Pour obtenir la liste des modèles de base prenant en charge l’entraînement avec des données audio, consultez les informations relatives à la [prise en charge des langues](language-support.md#speech-to-text).

## <a name="plain-text-data-for-training"></a>Données de texte brut pour la formation

Vous pouvez utiliser des phrases relatives à un domaine pour améliorer la précision de la reconnaissance des noms de produits ou du jargon spécifique à un secteur. Fournissez des phrases dans un fichier texte unique. Pour améliorer la précision, utilisez des données texte plus proches des énoncés prononcés attendus. 

La formation avec du texte brut se termine généralement en quelques minutes.

Pour créer un modèle personnalisé en utilisant des phrases, vous devez fournir une liste d’exemples d’énoncés. Ces énoncés ne doivent _pas_ nécessairement être des phrases complètes ou grammaticalement correctes. En revanche, elles doivent refléter précisément l’entrée parlée que vous attendez en production. Pour donner plus de poids à certains termes, ajoutez plusieurs phrases contenant ces termes spécifiques.

En règle générale, l’adaptation du modèle est plus efficace quand le texte d’entraînement est le plus proche possible du texte réel attendu en production. Le jargon et les expressions spécifiques à un domaine que vous voulez améliorer doivent être inclus dans le texte d’entraînement. Dans la mesure du possible, essayez d’avoir une phrase ou un mot clé contrôlé sur une ligne distincte. Pour les mots clés et les expressions qui sont importants pour vous (par exemple des noms de produits), vous pouvez les copier plusieurs fois. Gardez cependant à l’esprit qu’il ne faut pas les copier un trop grand nombre de fois, car cela pourrait affecter le taux de reconnaissance global.

Servez-vous de ce tableau pour vérifier que votre fichier de données associées présente le bon format pour les énoncés :

| Propriété | Valeur |
|----------|-------|
| Encodage de texte | UTF-8 BOM |
| Nb d’énoncés par ligne | 1 |
| Taille maximale du fichier | 200 Mo |

Par ailleurs, vous devez prendre en compte les restrictions suivantes :

* Évitez de répéter des caractères, des mots ou des groupes de mots plus de trois fois. Par exemple : « aaaa », « oui oui oui oui » ou « c’est tout c’est tout c’est tout c’est tout ». Le service Speech peut supprimer des lignes avec un trop grand nombre de répétitions.
* N’utilisez pas de caractères spéciaux ou de caractères UTF-8 au-delà de `U+00A1`.
* Les URI sont rejetés.
* Pour certaines langues (le japonais ou le coréen par exemple), l’importation de grandes quantités de données texte peut prendre beaucoup de temps ou expirer. Envisagez de diviser les données chargées dans des fichiers texte jusqu’à 20 000 lignes chacun.

## <a name="structured-text-data-for-training-public-preview"></a>Données de texte structuré pour l’apprentissage (préversion publique)

Souvent, les énoncés attendus suivent un certain modèle. Un modèle courant est que les énoncés diffèrent uniquement par des mots ou des expressions extraits d’une liste. Il peut s’agir, par exemple, de « J’ai une question sur `product` », où `product` est une liste de produits possibles. Ou « Colorier `object` en `color` » où `object` est une liste de formes géométriques, et `color` une liste de couleurs. Pour simplifier la création de données d’apprentissage et permettre une meilleure modélisation dans le Modèle de langage personnalisé, vous pouvez utiliser un texte structuré de format Markdown pour définir des listes d’éléments, puis référencer celles-ci à l’intérieur de vos énoncés d’apprentissage. En outre, le format Markdown prend également en charge la spécification de la prononciation phonétique des mots. Le format Markdown est partagé avec le format Markdown _.lu_ utilisé pour l’apprentissage des modèles de compréhension du langage (LUIS), en particulier les entités de liste et les exemples d’énoncés. Pour plus d’informations sur le format Markdown _.lu_ complet, consultez <a href="https://docs.microsoft.com/azure/bot-service/file-format/bot-builder-lu-file-format?view=azure-bot-service-4.0" target="_blank">Format de fichier. lu</a>. 

Voici un exemple de format Markdown :

```markdown
// This is a comment

// Here are three separate lists of items that can be referenced in an example sentence. You can have up to 20 of these
@ list food =
- pizza
- burger
- ice cream
- soda

@ list pet =
- cat
- dog

@ list sports =
- soccer
- tennis
- cricket
- basketball
- baseball
- football

// This is a list of phonetic pronunciations. 
// This adjusts the pronunciation of every instance of these word in both a list or example training sentences 
@ speech:phoneticlexicon
- cat/k ae t
- cat/f i l ai n

// Here are example training sentences. They are grouped into two sections to help organize the example training sentences.
// You can refer to one of the lists we declared above by using {@listname} and you can refer to multiple lists in the same training sentence
// A training sentence does not have to refer to a list.
# SomeTrainingSentence
- you can include sentences without a class reference
- what {@pet} do you have
- I like eating {@food} and playing {@sports}
- my {@pet} likes {@food}

# SomeMoreSentence
- you can include more sentences without a class reference
- or more sentences that have a class reference like {@pet} 
```

À l’instar du texte brut, l’apprentissage avec du texte structuré prend généralement quelques minutes. En outre, vos exemples de phrases et de listes doivent refléter le type d’entrée parlée que vous attendez en production.
Pour les entrées de prononciation, consultez la description du [Jeu de phonèmes universel](phone-sets.md).

Le tableau ci-dessous spécifie les limites et d’autres propriétés pour le format Markdown :

| Propriété | Valeur |
|----------|-------|
| Encodage de texte | UTF-8 BOM |
| Taille maximale du fichier | 200 Mo |
| Nombre maximal d’exemples de phrases | 50 000 |
| Nombre maximal de classes de liste | 10 |
| Nombre maximal d’éléments dans une classe de liste | 4 000 |
| Nombre maximal d’entrées speech:phoneticlexicon | 15000 |
| Nombre maximal de prononciations par mot | 2 |


## <a name="pronunciation-data-for-training"></a>Données de prononciation pour la formation

Si vos utilisateurs sont appelés à rencontrer ou à utiliser des termes peu communs qui se prononcent d’une façon particulière, vous pouvez fournir un fichier de prononciation personnalisé pour améliorer la reconnaissance. Pour obtenir la liste des langues qui prennent en charge la prononciation personnalisée, consultez **prononciation** dans la colonne **personnalisations** de [la table Speech-to-Text](language-support.md#speech-to-text).

> [!IMPORTANT]
> Il est déconseillé d’utiliser des fichiers de prononciation personnalisée pour altérer la prononciation des mots communs.

> [!NOTE]
> Vous ne pouvez pas combiner ce type de fichier de prononciation avec des données d’apprentissage de texte structuré. Pour les données de texte structurées, utilisez la fonctionnalité de prononciation phonétique incluse dans le format Markdown de texte structuré.

Fournissez des prononciations dans un fichier texte unique. Ce tableau contient des exemples d’énoncés oraux et une prononciation personnalisée pour chacun d’eux :

| Forme reconnue/affichée | Forme orale (en anglais) |
|--------------|--------------------------|
| 3CPO | trois c p o |
| CNTK (Computational Network Toolkit de Microsoft Research) | c n t k |
| IEEE | i triple e |

La forme orale est la séquence phonétique décomposée. Elle peut être constituée de lettres, de mots, de syllabes ou d’une combinaison des trois.

Utilisez le tableau suivant pour vérifier que votre fichier de données associées est correctement mis en forme. Les fichiers de prononciation sont petits et doivent se limiter à quelques Ko.

| Propriété | Valeur |
|----------|-------|
| Encodage de texte | UTF-8 BOM (ANSI est également pris en charge pour l’anglais) |
| Nbre de prononciations par ligne | 1 |
| Taille maximale du fichier | 1 Mo (1 Ko pour le niveau gratuit) |

## <a name="audio-data-for-testing"></a>Données audio pour tester

Les données audio sont optimales pour tester la précision du modèle de reconnaissance vocale de référence de Microsoft ou d’un modèle personnalisé. Gardez à l’esprit que les données audio sont utilisées pour inspecter la précision de la reconnaissance vocale par rapport à un modèle spécifique. Si vous souhaitez quantifier la précision d’un modèle, utilisez des [transcriptions audio + étiquetées à la main](#audio--human-labeled-transcript-data-for-trainingtesting).

Custom Speech nécessite des fichiers audio avec les propriétés suivantes :

| Property                 | Valeur                 |
|--------------------------|-----------------------|
| Format de fichier              | RIFF (WAV)            |
| Échantillonnage              | 8 000 Hz ou 16 000 Hz |
| Canaux                 | 1 (mono)              |
| Longueur maximale par fichier audio | 2 heures               |
| Format d’échantillonnage            | PCM, 16 bits           |
| Format d’archive           | .zip                  |
| Taille d’archive maximale     | 2 Go                  |

[!INCLUDE [supported-audio-formats](includes/supported-audio-formats.md)]

> [!NOTE]
> Lors du chargement de données de formation et de test, la taille du fichier .zip ne peut pas dépasser 2 Go. Si vous avez besoin de plus de données pour l’entraînement, divisez-le en plusieurs fichiers .zip et chargez-les séparément. Plus tard, vous pouvez choisir d’effectuer l’entraînement à partir de *plusieurs* jeux de données. Cependant, vous ne pouvez tester qu’à partir d’un *seul* jeu de données.

Utilisez <a href="http://sox.sourceforge.net" target="_blank" rel="noopener">SoX</a> pour vérifier les propriétés audio ou pour convertir les données audio existantes aux formats appropriés. Voici quelques exemples de commandes SoX :

| Activité | Commande SoX |
|---------|-------------|
| Vérifiez le format du fichier audio. | `sox --i <filename>` |
| Convertissez le fichier audio en un canal unique, 16 bits, 16 KHz. | `sox <input> -b 16 -e signed-integer -c 1 -r 16k -t wav <output>.wav` |

## <a name="next-steps"></a>Étapes suivantes

* [Inspecter les données](how-to-custom-speech-inspect-data.md)
* [Évaluer les données](how-to-custom-speech-evaluate-data.md)
* [Effectuer l’apprentissage d’un modèle personnalisé](how-to-custom-speech-train-model.md)
* [Déployer un modèle](./how-to-custom-speech-train-model.md)
