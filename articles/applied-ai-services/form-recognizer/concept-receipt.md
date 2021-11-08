---
title: Modèle de reçu Form Recognizer
titleSuffix: Azure Applied AI Services
description: Concepts englobant l’extraction et l’analyse de données à l’aide du modèle prédéfini de reçu
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: lajanuar
recommendations: false
ms.custom: ignite-fall-2021
ms.openlocfilehash: 111dcc2ab07c83e164e054395b0804f46c07d748
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131027061"
---
<!-- markdownlint-disable MD033 -->

# <a name="form-recognizer-receipt-model"></a>Modèle de reçu Form Recognizer

Le modèle de reçu combine de puissantes capacités de reconnaissance optique de caractères (OCR) à des modèles Deep Learning pour analyser et extraire des informations clés des reçus de vente. Les reçus peuvent être de différents formats et qualités, y compris les reçus imprimés et manuscrits. L’API extrait les informations clés telles que le nom du commerçant, le numéro de téléphone du commerçant, la date de la transaction, la taxe et le total de la transaction et renvoie une représentation structurée des données JSON.

***Exemple de reçu traité avec l’[outil d’étiquetage des exemples Form Recognizer](https://fott-2-1.azurewebsites.net/)*** :

:::image type="content" source="media/studio/overview-receipt.png" alt-text="exemple de ticket de caisse" lightbox="media/overview-receipt.jpg":::

## <a name="development-options"></a>Options de développement

Les ressources suivantes sont prises en charge par Form Recognizer v2.1 :

| Fonctionnalité | Ressources |
|----------|-------------------------|
|**Modèle de reçu**| <ul><li>[**Outil d’étiquetage Form Recognizer**](https://fott-2-1.azurewebsites.net/prebuilts-analyze)</li><li>[**API REST**](quickstarts/try-sdk-rest-api.md?pivots=programming-language-rest-api#analyze-receipts)</li><li>[**Kit SDK Bibliothèque de client**](quickstarts/try-sdk-rest-api.md)</li><li>[**Conteneur Docker de Form Recognizer**](containers/form-recognizer-container-install-run.md?tabs=receipt#run-the-container-with-the-docker-compose-up-command)</li></ul>|

Les ressources suivantes sont prises en charge par Form Recognizer v3.0 :

| Fonctionnalité | Ressources | ID de modèle |
|----------|-------------|-----------|
|**Modèle de reçu**| <ul><li>[**Form Recognizer Studio**](https://formrecognizer.appliedai.azure.com)</li><li>[**API REST**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument)</li><li>[**Kit de développement logiciel (SDK) C#**](quickstarts/try-v3-csharp-sdk.md)</li><li>[**Kit de développement logiciel (SDK) Python**](quickstarts/try-v3-python-sdk.md)</li></ul>|**prebuilt-receipt**|

### <a name="try-form-recognizer"></a>Essayer Form Recognizer

Découvrez comment les données, y compris l’heure et la date des transactions, les informations marchandes et les totaux des montants, sont extraites des reçus à l’aide de Form Recognizer Studio ou de notre outil d’étiquetage des exemples. Vous aurez besoin des éléments suivants :

* Un abonnement Azure : [vous pouvez en créer un gratuitement](https://azure.microsoft.com/free/cognitive-services/)

* Une [instance Form Recognizer](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) dans le portail Azure. Vous pouvez utiliser le niveau tarifaire gratuit (`F0`) pour tester le service. Une fois votre ressource déployée, sélectionnez **Accéder à la ressource** pour accéder à la clé et au point de terminaison de votre API.

 :::image type="content" source="media/containers/keys-and-endpoint.png" alt-text="Capture d’écran : clés et emplacement du point de terminaison dans le portail Azure":::

#### <a name="form-recognizer-studio-preview"></a>Form Recognizer Studio (préversion)

> [!NOTE]
> Form Recognizer Studio est disponible avec l’API (v 3.0) en préversion.

1. Dans la page d’accueil de Form Recognizer Studio, sélectionnez **Reçus**

1. Vous pouvez analyser l’exemple de reçu ou sélectionner le bouton **+ Ajouter** pour charger votre propre exemple.

1. Sélectionnez le bouton **Analyser** :

    :::image type="content" source="media/studio/receipt-analyze.png" alt-text="Capture d’écran : menu analyser le reçu.":::

    > [!div class="nextstepaction"]
    > [Essayer Form Recognizer Studio](https://formrecognizer.appliedai.azure.com/studio/prebuilt?formType=receipt)

#### <a name="sample-labeling-tool"></a>Outil d’étiquetage d’exemples

Vous aurez besoin d’un document de reçu. Vous pouvez utiliser notre [exemple de reçu](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/contoso-receipt.png).

1. Dans la page d’accueil de l’outil d’étiquetage des exemples, sélectionnez **Utiliser un modèle prédéfini pour obtenir des données**.

1. Sélectionnez **Reçu** dans le menu déroulant **Type de formulaire** :

      :::image type="content" source="media/try-receipt.png" alt-text="Capture d’écran : menu déroulant de sélection du modèle prédéfini de l’outil d’étiquetage des exemples.":::

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

## <a name="supported-languages-and-locales-v21"></a>Langues et paramètres régionaux pris en charge v2.1

>[!NOTE]
 > Il n’est pas nécessaire de spécifier de paramètres régionaux. Il s'agit d'un paramètre facultatif. La technologie d’apprentissage profond de Form Recognizer détecte automatiquement la langue du texte figurant dans votre image.

| Modèle | Langue : code de paramètres régionaux | Default |
|--------|:----------------------|:---------|
|Réception| <ul><li>Anglais (États-Unis) : en-US</li><li> Anglais (Australie) : en-AU</li><li>Anglais (Canada) : en-CA</li><li>Anglais (Royaume-Uni) : en-GB</li><li>Anglais (Inde) : en-IN</li></ul>  | Détection automatique |

## <a name="field-extraction"></a>Extraction de champ

|Nom| Type | Description | Sortie standardisée |
|:-----|:----|:----|:----|
| ReceiptType | String | Type de reçu |  Itemized |
| MerchantName | String | Nom du commerçant émettant le reçu |  |
| MerchantPhoneNumber | phoneNumber | Numéro de téléphone de la liste de commerçants | +1 xxx xxx xxxx |
| MerchantAddress | String | Adresse répertoriée du commerçant |   |
| TransactionDate | Date | Date d’émission du reçu | aaaa-mm-jj |
| TransactionTime | Temps | Heure d’émission du reçu | hh-mm-ss (24 heures)  |
| Total | Nombre (USD)| Total du reçu | Virgule flottante à deux décimales|
| Sous-total | Nombre (USD) | Sous-total du reçu, souvent avant application des taxes | Virgule flottante à deux décimales|
| Taxe | Nombre (USD) | Taxe sur le reçu (TVA ou équivalent) | Virgule flottante à deux décimales |
| Conseil | Nombre (USD) | Pourboire inclus par l’acheteur | Virgule flottante à deux décimales|
| Éléments | Tableau d’objets | Lignes extraites, avec le nom, la quantité, le prix unitaire et le prix total extraits | |
| Nom | String | Nom de l’élément | |
| Quantité | Number | Quantité de chaque élément | Integer |
| Price | Number | Prix individuel de chaque unité d’article| Virgule flottante à deux décimales |
| Prix total | Number | Prix total de la ligne de facturation | Virgule flottante à deux décimales |

## <a name="form-recognizer-preview-v30"></a>Form Recognizer préversion v3.0

 La version préliminaire de Form Recognizer introduit plusieurs nouvelles fonctionnalités. Le modèle de **reçu** prend en charge le traitement des reçus d’hôtel d’une seule page.

### <a name="hotel-receipt-field-extraction"></a>Extraction du champ du reçu de l’hôtel

|Nom| Type | Description | Sortie standardisée |
|:-----|:----|:----|:----|
| ArrivalDate | Date | Date d’arrivée | aaaa-mm-jj |
| Devise | Devise | Unité monétaire des montants du reçu. Par exemple, USD, EUR ou MIXED si plusieurs valeurs sont trouvées ||
| DepartureDate | Date | Date de départ | aaaa-mm-jj |
| Éléments | Array | | |
| Items.*.Category | String | Catégorie d’élément, par exemple : chambre, taxe, etc. |  |
| Items.*.Date | Date | Date de l’élément | aaaa-mm-jj |
| Items.*.Description | String | Description de l’élément | |
| Items.*.TotalPrice |  Number | Prix total de l’élément | Integer |
| Paramètres régionaux | String | Paramètres régionaux du reçu, par exemple, en-US. | Code ISO langue-pays   |
| MerchantAddress | String | Adresse répertoriée du commerçant | |
| MerchantAliases | Array| | |
| MerchantAliases.* | String | Autre nom du commerçant |  |
| MerchantName | String | Nom du commerçant émettant le reçu | |
| MerchantPhoneNumber | phoneNumber | Numéro de téléphone de la liste de commerçants | +1 xxx xxx xxxx|
| ReceiptType | String | Type de reçu, par exemple : hôtel, détail | |
| Total | Number | Total du reçu | Virgule flottante à deux décimales |

### <a name="hotel-receipt-supported-languages-and-locales"></a>Langues et paramètres régionaux pris en charge pour les reçus d’hôtel

| Modèle | Langue : code de paramètres régionaux | Default |
|--------|:----------------------|:---------|
|Reçu (hôtel) | <ul><li>Anglais (États-Unis) : en-US</li></ul>| Anglais (États-Unis) : en-US|

### <a name="migration-guide-and-rest-api-v30"></a>Guide de migration et API REST v 3.0

* Suivez notre [**guide de migration Form Recognizer v3.0**](v3-migration-guide.md) pour apprendre à utiliser la préversion dans vos applications et workflows.

* Découvrez notre [**API REST (préversion)**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument) pour en savoir plus sur la préversion et les nouvelles fonctionnalités.

## <a name="next-steps"></a>Étapes suivantes

* Suivre un démarrage rapide Form Recognizer :

  > [!div class="nextstepaction"]
  > [Démarrage rapide Form Recognizer](quickstarts/try-sdk-rest-api.md)

* Découvrir notre API REST :

    > [!div class="nextstepaction"]
    > [API Form Recognizer v2.1](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeReceiptAsync)
