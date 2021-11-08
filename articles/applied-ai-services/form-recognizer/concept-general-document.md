---
title: Modèle de document général Form Recognizer | Préversion
titleSuffix: Azure Applied AI Services
description: Concepts englobant l’extraction et l’analyse de données à l’aide du modèle prédéfini de document général
author: vkurpad
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 10/07/2021
ms.author: lajanuar
recommendations: false
ms.custom: ignite-fall-2021
ms.openlocfilehash: 63d381f96a9781f2f3ab1abfd45d03c968d6dad8
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131027515"
---
<!-- markdownlint-disable MD033 -->

# <a name="form-recognizer-general-document-model-preview"></a>Modèle de document général Form Recognizer (préversion)

Le modèle Document général combine de puissantes capacités de reconnaissance optique de caractères (OCR) à des modèles Deep Learning pour extraire des paires clé-valeur et des entités des documents. Document général est disponible uniquement avec l’API de préversion (v3.0).  Pour plus d’informations sur l’utilisation de l’API de préversion (v3.0), consultez notre [guide de migration](v3-migration-guide.md).

L’API de document général prend en charge la plupart des types de formulaires. Elle analyse vos documents et associe des valeurs aux clés et des entrées aux tableaux qu’elle découvre. Elle est idéale pour extraire les paires clé-valeur courantes des documents. Vous pouvez utiliser le modèle de document général comme alternative à la [formation d’un modèle personnalisé sans étiquettes](compose-custom-models.md#train-without-labels).

## <a name="general-document-features"></a>Fonctionnalités Document général

* Il n’est pas nécessaire d’effectuer l’apprentissage d’un modèle personnalisé pour extraire des paires clé-valeur.

* Une seule API est utilisée pour extraire des paires clé-valeur, des entités, du texte, des tableaux et la structure de documents.

* Il s’agit d’un modèle préformé qui sera régulièrement formé sur de nouvelles données pour améliorer la couverture et la précision.

* Le modèle de document général prend en charge les données structurées, semi-structurées et non structurées.

***Exemple de document traité dans Form Recognizer Studio***

:::image type="content" source="media/studio/general-document-analyze.png" alt-text="Capture d’écran : analyse d’un document général dans Form Recognizer Studio.":::

## <a name="development-options"></a>Options de développement

Les ressources suivantes sont prises en charge par Form Recognizer v3.0 :

| Fonctionnalité | Ressources |
|----------|-------------------------|
|🆕 **Modèle de document général**|<ul ><li>[**Form Recognizer Studio**](https://formrecognizer.appliedai.azure.com)</li><li>[**API REST**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument)</li><li>[**Kit de développement logiciel (SDK) C#**](quickstarts/try-v3-csharp-sdk.md)</li><li>[**Kit de développement logiciel (SDK) Python**](quickstarts/try-v3-python-sdk.md)</li><li>[**Kit SDK Java**](quickstarts/try-v3-java-sdk.md)</li><li>[**Kit de développement logiciel (SDK) JavaScript**](quickstarts/try-v3-javascript-sdk.md)</li></ul>|

### <a name="try-form-recognizer"></a>Essayer Form Recognizer

Découvrez comment les données, y compris les tables, les valeurs et les entités, sont extraites des formulaires et des documents à l’aide de Form Recognizer Studio ou de notre outil d’étiquetage d’exemples. Vous aurez besoin des éléments suivants :

* Un abonnement Azure : [vous pouvez en créer un gratuitement](https://azure.microsoft.com/free/cognitive-services/)

* Une [instance Form Recognizer](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) dans le portail Azure. Vous pouvez utiliser le niveau tarifaire gratuit (`F0`) pour tester le service. Une fois votre ressource déployée, sélectionnez **Accéder à la ressource** pour accéder à la clé et au point de terminaison de votre API.

 :::image type="content" source="media/containers/keys-and-endpoint.png" alt-text="Capture d’écran : clés et emplacement du point de terminaison dans le portail Azure":::

#### <a name="form-recognizer-studio-preview"></a>Form Recognizer Studio (préversion)

> [!NOTE]
> Form Recognizer Studio et le modèle de document général sont disponibles avec l’API de préversion (v3.0).

1. Dans la page d’accueil Form Recognizer Studio, sélectionnez **Documents généraux**

1. Vous pouvez analyser l’exemple de document ou sélectionner le bouton **+ Ajouter** pour charger votre propre exemple.

1. Sélectionnez le bouton **Analyser** :

    :::image type="content" source="media/studio/general-document-analyze-1.png" alt-text="Capture d’écran : menu analyser le document général.":::

    > [!div class="nextstepaction"]
    > [Essayer Form Recognizer Studio](https://formrecognizer.appliedai.azure.com/studio/prebuilt?formType=document)

## <a name="key-value-pairs"></a>Paires clé-valeur

Les paires clé-valeur sont des portions spécifiques dans le document qui identifient une étiquette ou une clé, ainsi que la réponse ou la valeur associée. Dans un formulaire structuré, il peut s’agir de l’étiquette et de la valeur que l’utilisateur a saisie pour ce champ. Dans un document non structuré, il peut s’agir de la date d’exécution d’un contrat en fonction du texte d’un paragraphe.  Le modèle d’IA est formé à l’extraction des clés et des valeurs identifiables à partir d’une grande variété de types, de formats et de structures de documents.

Les clés peuvent également exister de manière isolée lorsque le modèle détecte qu’une clé existe sans valeur associée ou lors du traitement de champs facultatifs. Par exemple, le champ du second prénom peut être laissé vide sur un formulaire dans certains cas. Les paires clé-valeur sont toujours des portions de texte contenues dans le document et, si vous avez des documents où la même valeur est décrite de différentes manières, par exemple un client ou un utilisateur, la clé associée sera soit client soit utilisateur en fonction du contenu du document. 

## <a name="entities"></a>Entités

Les modèles de traitement du langage naturel peuvent identifier des parties du message et classer chaque élément ou mot. Le modèle de reconnaissance d’entités nommées est capable d’identifier des entités telles que des personnes, des lieux et des dates afin d’offrir une expérience plus riche. L’identification des entités vous permet de distinguer les types de clients, par exemple, un individu ou une organisation.
Le modèle d’extraction de paires clé-valeur et le modèle d’identification des entités sont exécutés en parallèle sur l’ensemble du document et pas seulement sur les valeurs des paires clé-valeur extraites. Cela garantit que les structures complexes où une clé ne peut pas être identifiée sont tout de même enrichies par l’identification des entités référencées. Vous pouvez toujours faire correspondre des clés ou des valeurs à des entités en fonction des décalages des portions identifiées.

* Le document général est un modèle préformé qui peut être appelé directement par le biais de l’API REST. 

* Le modèle de document général prend en charge la reconnaissance d’entité nommée (NER) pour plusieurs catégories d’entités. La reconnaissance d’entité nommée est la capacité d’identifier différentes entités dans du texte et de les catégoriser en classes ou types prédéfinis tels que : personne, lieu, événement, produit et organisation. L’extraction d’entités peut être utile dans les scénarios où vous souhaitez valider des valeurs extraites. Les entités sont extraites de l’ensemble du contenu et pas seulement des valeurs extraites.

## <a name="general-document-model-data-extraction"></a>Extraction de données du modèle de document général

| **Modèle**   | **Extraction de texte** |**Paires clé-valeur** |**Marques de sélection**   | **Tables**   |**Entités** |
| --- | :---: |:---:| :---: | :---: |:---: |
|Document général  | ✓  |  ✓ | ✓  | ✓  | ✓  |

## <a name="input-requirements"></a>Critères des entrées

* Pour de meilleurs résultats, fournissez une photo nette ou une copie de qualité par document.
* Formats de fichier pris en charge : JPEG, PNG, BMP, TIFF et PDF (texte incorporé ou numérisé). Les PDF avec du texte incorporé sont préférables pour éviter tout risque d’erreur au niveau de l’extraction et de l’emplacement des caractères.
* Pour PDF et TIFF, il est possible de traiter jusqu’à 2 000 pages (avec un abonnement gratuit, seules les deux premières pages sont traitées).
* La taille du fichier doit être inférieure à 50 Mo.
* Les dimensions des images doivent être comprises entre 50 x 50 et 10 000 x 10 000 pixels.
* Les dimensions des PDF vont jusqu’à 17x17 pouces, ce qui correspond au format papier Legal, A3 ou plus petit.
* La taille totale des données d’entraînement doit être de 500 pages maximum.
* Si vos fichiers PDF sont verrouillés par mot de passe, vous devez supprimer le verrou avant leur envoi.
* Pour l’apprentissage non supervisé (sans données étiquetées) :
  * Les données doivent contenir des clés et des valeurs.
  * Les clés doivent apparaître au-dessus ou à gauche des valeurs, pas en dessous ni à droite.

## <a name="supported-languages-and-locales"></a>Langues et régions prises en charge

| Modèle | Langue : code de paramètres régionaux | Default |
|--------|:----------------------|:---------|
|Document général| <ul><li>Anglais (États-Unis) : en-US</li></ul>| Anglais (États-Unis) : en-US|

### <a name="named-entity-recognition-ner-categories"></a>Catégories de reconnaissance d’entité nommée (NER)

| Category | Type | Description |
|-----------|-------|--------------------|
| Personne | String | Nom partiel ou complet d’une personne. |
| PersonType | String | Type d’emploi ou rôle d’une personne.  |
| Emplacement | String | Points de repère, structures et caractéristiques géographiques et entités géopolitiques naturels et créés par l’homme. |
| Organisation | String | Sociétés, partis politiques, groupes de musique, clubs de sport, organismes gouvernementaux et organisations publiques. |
| Événement | String | Événements historiques, sociaux et naturels. |
| Produit | String |Objets physiques de différentes catégories. |
| Compétence | String | Une capacité, une compétence ou une expertise. |
| Adresse | String | Adresses postales complètes. |
| Numéro de téléphone | String| Numéros de téléphone | 
| Courrier | String | Adresse e-mail. |
| URL | String | URL et liens de sites web. |
| Adresse IP | String | Adresses IP du réseau. |
| DateTime | String | Dates et heures du jour. |
| Quantité | String | Mesures et unités numériques. |

## <a name="considerations"></a>Considérations

* L’extraction d’entités peut être utile dans les scénarios où vous souhaitez valider des valeurs extraites. Les entités sont extraites sur l’ensemble du contenu des documents et pas seulement sur les valeurs extraites.

* Les clés sont des portions de texte extraites du document. Pour les documents semi-structurés, il peut être nécessaire de faire correspondre les clés à un dictionnaire de clés existant.

* Attendez-vous à voir des paires clé-valeur avec une clé, mais sans valeur. Par exemple, si un utilisateur a choisi de ne pas fournir d’adresse e-mail sur le formulaire.

## <a name="next-steps"></a>Étapes suivantes

* Suivez notre [**guide de migration Form Recognizer v3.0**](v3-migration-guide.md) pour apprendre à utiliser la préversion dans vos applications et workflows.

* Découvrez notre [**API REST (préversion)**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument) pour en savoir plus sur la préversion et les nouvelles fonctionnalités.

> [!div class="nextstepaction"]
> [Essayer Form Recognizer Studio](https://formrecognizer.appliedai.azure.com/studio)
