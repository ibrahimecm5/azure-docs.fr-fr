---
title: Modèle de document d’identité Form Recognizer
titleSuffix: Azure Applied AI Services
description: Concepts englobant l’extraction et l’analyse de données à l’aide du modèle de document d’identité prédéfini
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: lajanuar
recommendations: false
ms.custom: ignite-fall-2021
ms.openlocfilehash: d598c3af52f8b62b23b49f9d661a79a2979574a0
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131027293"
---
<!-- markdownlint-disable MD033 -->

# <a name="form-recognizer-id-document-model"></a>Modèle de document d’identité Form Recognizer

Le modèle de document d’identité associe de puissantes fonctionnalités de reconnaissance optique de caractères (OCR) à des modèles de Deep Learning pour analyser et extraire des informations clés de permis de conduire américains (les 50 états et le District de Columbia) et de pages biographiques de passeports internationaux (à l’exclusion des visas et autres documents de voyage). L’API analyse les documents d’identité ; extrait des informations clés, telles que le prénom, le nom, l’adresse et la date de naissance ; et retourne une représentation des données JSON structurée.

***Exemple de permis de conduire américain traité avec Form Recognizer Studio***

:::image type="content" source="media/studio/drivers-license.png" alt-text="exemple de carte d’identité" lightbox="media/overview-id.jpg":::

## <a name="development-options"></a>Options de développement

Les ressources suivantes sont prises en charge par Form Recognizer v2.1 :

| Fonctionnalité | Ressources |
|----------|-------------------------|
|**Modèle de pièce d’identité**| <ul><li>[**Outil d’étiquetage Form Recognizer**](https://fott-2-1.azurewebsites.net/prebuilts-analyze)</li><li>[**API REST**](quickstarts/try-sdk-rest-api.md?pivots=programming-language-rest-api#analyze-identity-id-documents)</li><li>[**Kit SDK Bibliothèque de client**](quickstarts/try-sdk-rest-api.md)</li><li>[**Conteneur Docker de Form Recognizer**](containers/form-recognizer-container-install-run.md?tabs=id-document#run-the-container-with-the-docker-compose-up-command)</li></ul>|

Les ressources suivantes sont prises en charge par Form Recognizer v3.0 :

| Fonctionnalité | Ressources | ID de modèle |
|----------|-------------|-----------|
|**Modèle de pièce d’identité**|<ul><li> [**Form Recognizer Studio**](https://formrecognizer.appliedai.azure.com)</li><li>[**API REST**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument)</li><li>[**Kit de développement logiciel (SDK) C#**](quickstarts/try-v3-csharp-sdk.md)</li><li>[**Kit de développement logiciel (SDK) Python**](quickstarts/try-v3-python-sdk.md)</li><li>[**Kit SDK Java**](quickstarts/try-v3-java-sdk.md)</li><li>[**Kit de développement logiciel (SDK) JavaScript**](quickstarts/try-v3-javascript-sdk.md)</li></ul>|**prebuilt-idDocument**|

### <a name="try-form-recognizer"></a>Essayer Form Recognizer

Découvrez comment les données, y compris le nom, la date de naissance, la zone lisible par ordinateur et la date d’expiration, sont extraites de documents d’identité à l’aide de Form Recognizer Studio ou de notre outil d’étiquetage des exemples. Vous aurez besoin des éléments suivants :

* Un abonnement Azure : [vous pouvez en créer un gratuitement](https://azure.microsoft.com/free/cognitive-services/)

* Une [instance Form Recognizer](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) dans le portail Azure. Vous pouvez utiliser le niveau tarifaire gratuit (`F0`) pour tester le service. Une fois votre ressource déployée, sélectionnez **Accéder à la ressource** pour accéder à la clé et au point de terminaison de votre API.

 :::image type="content" source="media/containers/keys-and-endpoint.png" alt-text="Capture d’écran : clés et emplacement du point de terminaison dans le portail Azure":::

#### <a name="form-recognizer-studio-preview"></a>Form Recognizer Studio (préversion)

> [!NOTE]
> Form Recognizer Studio est disponible avec l’API (v 3.0) en préversion.

1. Dans la page d’accueil de Form Recognizer Studio, sélectionnez **Factures**.

1. Vous pouvez analyser l’exemple de facture ou sélectionner le bouton **+ Ajouter** pour charger votre propre exemple.

1. Sélectionnez le bouton **Analyser** :

    :::image type="content" source="media/studio/id-document-analyze.png" alt-text="Capture d’écran : menu analyser le document d’identité.":::

    > [!div class="nextstepaction"]
    > [Essayer Form Recognizer Studio](https://formrecognizer.appliedai.azure.com/studio/prebuilt?formType=idDocument)

#### <a name="sample-labeling-tool"></a>Outil d’étiquetage d’exemples

Vous aurez besoin d’un document d’identité. Vous pouvez utiliser notre [exemple de document d’identité](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/DriverLicense.png).

1. Dans la page d’accueil de l’outil d’étiquetage des exemples, sélectionnez **Utiliser un modèle prédéfini pour obtenir des données**.

1. Sélectionnez **Identifier des documents** dans le menu déroulant **Type de formulaire** :

    :::image type="content" source="media/try-id-document.png" alt-text="Capture d’écran : menu déroulant de sélection du modèle prédéfini de l’outil d’étiquetage des exemples.":::

   > [!div class="nextstepaction"]
   > [Essayer l’outil d’étiquetage d’exemples](https://fott-2-1.azurewebsites.net/prebuilts-analyze)

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

> [!NOTE]
> L’[outil d’étiquetage des exemples](https://fott-2-1.azurewebsites.net/) ne prend pas en charge le format de fichier BMP. Il s’agit d’une limitation de l’outil et non du service Form Recognizer.

## <a name="supported-languages-and-locales-v21"></a>Langues et régions prises en charge v2.1

| Modèle | Langue : code de paramètres régionaux | Default |
|--------|:----------------------|:---------|
|Document d’identité| <ul><li>Anglais (États-Unis) : en-US (permis de conduire)</li><li>Pages biographiques de passeports internationaux</br> (sauf visa et autres documents de voyage)</li></ul></br>|Anglais (États-Unis) : en-US|

## <a name="field-extraction"></a>Extraction de champ

|Nom| Type | Description | Sortie standardisée|
|:-----|:----|:----|:----|
|  CountryRegion | countryRegion | Code de pays ou de région conforme à la norme ISO 3166 |  |
|  DateOfBirth | Date | DOB | aaaa-mm-jj |
|  DateOfExpiration | Date | Date d’expiration DOB | aaaa-mm-jj |
|  DocumentNumber | String | Numéro de passeport, numéro de permis de conduire, etc. |  |
|  FirstName | String | Prénom et initiale du second prénom extraits, le cas échéant |  |
|  LastName | String | Nom de famille extrait |  |
|  Nationalité | countryRegion | Code de pays ou de région conforme à la norme ISO 3166 (passeport uniquement) |  |
|  Sex | String | Les valeurs extraites possibles sont « M », « F » et « X ». | |
|  MachineReadableZone | Object | MRZ du passeport extraite, y compris 2 lignes de 44 caractères chacun | « P<USABROOKS<<JENNIFER<<<<<<<<<<<<<<<<<<<<<<< 3400200135USA8001014F1905054710000307<715816 » |
|  DocumentType ; | String | Type de document, par exemple, passeport ou permis de conduire | « passeport » |
|  Adresse | String | Adresse extraite (permis de conduire uniquement) ||
|  Region | String | Région, État, province et autres informations extraites (permis de conduire uniquement) |  |

## <a name="form-recognizer-preview-v30"></a>Form Recognizer préversion v3.0

 La préversion de Form Recognizer introduit plusieurs nouvelles fonctionnalités :

* Le modèle **Document d’identité (v3.0)** prend en charge l’extraction des approbations, des restrictions et des classifications de véhicules à partir de permis de conduire américains.

### <a name="id-document-preview-field-extraction"></a>Extraction de champ de préversion de document d’identité

|Nom| Type | Description | Sortie standardisée|
|:-----|:----|:----|:----|
| 🆕 Approbations | String | Privilèges de conduite supplémentaires accordés à un conducteur comme Motocyclette ou Bus scolaire.  | |
| 🆕 Restrictions | String | Privilèges de conduite restreints applicables aux permis suspendus ou révoqués.| |
| 🆕VehicleClassification | String | Types de véhicules qui peuvent être pilotés par un conducteur. ||
|  CountryRegion | countryRegion | Code de pays ou de région conforme à la norme ISO 3166 |  |
|  DateOfBirth | Date | DOB | aaaa-mm-jj |
|  DateOfExpiration | Date | Date d’expiration DOB | aaaa-mm-jj |
|  DocumentNumber | String | Numéro de passeport, numéro de permis de conduire, etc. |  |
|  FirstName | String | Prénom et initiale du second prénom extraits, le cas échéant |  |
|  LastName | String | Nom de famille extrait |  |
|  Nationalité | countryRegion | Code de pays ou de région conforme à la norme ISO 3166 (passeport uniquement) |  |
|  Sex | String | Les valeurs extraites possibles sont « M », « F » et « X ». | |
|  MachineReadableZone | Object | MRZ du passeport extraite, y compris 2 lignes de 44 caractères chacun | « P<USABROOKS<<JENNIFER<<<<<<<<<<<<<<<<<<<<<<< 3400200135USA8001014F1905054710000307<715816 » |
|  DocumentType ; | String | Type de document, par exemple, passeport ou permis de conduire | « passeport » |
|  Adresse | String | Adresse extraite (permis de conduire uniquement) ||
|  Region | String | Région, État, province et autres informations extraites (permis de conduire uniquement) |  |

### <a name="migration-guide-and-rest-api-v30"></a>Guide de migration et API REST v 3.0

* Suivez notre [**guide de migration Form Recognizer v3.0**](v3-migration-guide.md) pour apprendre à utiliser la préversion dans vos applications et workflows.

* Découvrez notre [**API REST (préversion)**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument) pour en savoir plus sur la préversion et les nouvelles fonctionnalités.

## <a name="next-steps"></a>Étapes suivantes

* Suivre un démarrage rapide Form Recognizer :

  > [!div class="nextstepaction"]
  > [Démarrage rapide Form Recognizer](quickstarts/try-sdk-rest-api.md)

* Découvrir notre API REST :

    > [!div class="nextstepaction"]
    > [API Form Recognizer v2.1](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/5f74a7738978e467c5fb8707)
