---
title: Modèle de facture Form Recognizer
titleSuffix: Azure Applied AI Services
description: Concepts englobant l’extraction et l’analyse de données à l’aide d’un modèle prédéfini de facture
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: lajanuar
recommendations: false
ms.custom: ignite-fall-2021
ms.openlocfilehash: 0f9c2f1603a87e30b0db32041f7d7aeff259600e
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131027350"
---
<!-- markdownlint-disable MD033 -->

# <a name="form-recognizer-invoice-model"></a>Modèle de facture Form Recognizer

 Le modèle de facture combine de puissantes capacités de reconnaissance optique de caractères (OCR) à des modèles Deep Learning pour analyser et extraire les champs clés et les éléments de ligne des factures de vente.  Les factures peuvent être de formats et de qualités divers, notamment des images capturées par téléphone, des documents numérisés et des fichiers PDF. L’API analyse le texte de la facture, extrait les informations clés, telles que le nom du client, l’adresse de facturation, la date d’échéance et le montant dû, et renvoie une représentation structurée des données JSON.

**Exemple de facture traitée avec l’[outil d’étiquetage des exemples Form Recognizer](https://formrecognizer.appliedai.azure.com/studio/prebuilt?formType=invoice)** :

:::image type="content" source="media/studio/overview-invoices.png" alt-text="Exemple de facture" lightbox="media/overview-invoices-big.jpg":::

## <a name="development-options"></a>Options de développement

Les ressources suivantes sont prises en charge par Form Recognizer v2.1 :

| Fonctionnalité | Ressources |
|----------|-------------------------|
|**Modèle de facture**| <ul><li>[**Outil d’étiquetage Form Recognizer**](https://fott-2-1.azurewebsites.net/prebuilts-analyze)</li><li>[**API REST**](quickstarts/try-sdk-rest-api.md?pivots=programming-language-rest-api#analyze-invoices)</li><li>[**Kit SDK Bibliothèque de client**](quickstarts/try-sdk-rest-api.md)</li><li>[**Conteneur Docker de Form Recognizer**](containers/form-recognizer-container-install-run.md?tabs=invoice#run-the-container-with-the-docker-compose-up-command)</li></ul>|

Les ressources suivantes sont prises en charge par Form Recognizer v3.0 :

| Fonctionnalité | Ressources | ID de modèle |
|----------|-------------|-----------|
|**Modèle de facture** | <ul><li>[**Form Recognizer Studio**](https://formrecognizer.appliedai.azure.com)</li><li>[**API REST**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument)</li><li>[**Kit de développement logiciel (SDK) C#**](quickstarts/try-v3-csharp-sdk.md)</li><li>[**Kit de développement logiciel (SDK) Python**](quickstarts/try-v3-python-sdk.md)</li><li>[**Kit SDK Java**](quickstarts/try-v3-java-sdk.md)</li><li>[**Kit de développement logiciel (SDK) JavaScript**](quickstarts/try-v3-javascript-sdk.md)</li></ul>|**Facture prédéfinie**|

### <a name="try-form-recognizer"></a>Essayer Form Recognizer

Découvrez comment les données, y compris les informations sur les clients, les détails du fournisseur et les articles, sont extraites des factures à l’aide de Form Recognizer Studio ou de notre outil d’étiquetage des exemples. Vous aurez besoin des éléments suivants :

* Un abonnement Azure : [vous pouvez en créer un gratuitement](https://azure.microsoft.com/free/cognitive-services/)

* Une [instance Form Recognizer](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) dans le portail Azure. Vous pouvez utiliser le niveau tarifaire gratuit (`F0`) pour tester le service. Une fois votre ressource déployée, sélectionnez **Accéder à la ressource** pour accéder à la clé et au point de terminaison de votre API.

 :::image type="content" source="media/containers/keys-and-endpoint.png" alt-text="Capture d’écran : clés et emplacement du point de terminaison dans le portail Azure":::

#### <a name="form-recognizer-studio-preview"></a>Form Recognizer Studio (préversion)

1. Dans la page d’accueil de Form Recognizer Studio, sélectionnez **Factures**.

1. Vous pouvez analyser l’exemple de facture ou sélectionner le bouton **+ Ajouter** pour charger votre propre exemple.

1. Sélectionnez le bouton **Analyser** :

    :::image type="content" source="media/studio/invoice-analyze.png" alt-text="Capture d’écran : menu analyser la facture.":::

> [!div class="nextstepaction"]
> [Essayer Form Recognizer Studio](https://formrecognizer.appliedai.azure.com/studio/prebuilt?formType=invoice)

#### <a name="sample-labeling-tool"></a>Outil d’étiquetage d’exemples

Vous aurez besoin d’une facture. Vous pouvez utiliser notre [exemple de facture](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-invoice.pdf).

1. Dans la page d’accueil de l’outil d’étiquetage des exemples, sélectionnez **Utiliser un modèle prédéfini pour obtenir des données**.

1. Sélectionnez **Facture** dans le menu déroulant **Type de formulaire** :

    :::image type="content" source="media/try-invoice.png" alt-text="Capture d’écran : menu déroulant de sélection du modèle prédéfini de l’outil d’étiquetage des exemples.":::

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

## <a name="supported-languages-and-locales"></a>Langues et régions prises en charge

| Modèle | Langue : code de paramètres régionaux | Default |
|--------|:----------------------|:---------|
|Facture| <ul><li>Anglais (États-Unis) : en-US</li></ul>| Anglais (États-Unis) : en-US|

## <a name="field-extraction"></a>Extraction de champ

|Nom| Type | Description | Sortie standardisée |
|:-----|:----|:----|:---:|
| CustomerName | String | Client facturé| |
| CustomerId | String | ID de référence client | |
| PurchaseOrder | String | Numéro de référence du bon de commande | |
| InvoiceId | String | ID de cette facture (souvent appelé « numéro de facture ») | |
| InvoiceDate | Date | Date d’émission de la facture | aaaa-mm-jj|
| DueDate | Date | Date d’échéance du paiement de cette facture | aaaa-mm-jj|
| VendorName | String | Nom du fournisseur |  |
| VendorAddress | String |  Adresse postale du vendeur|  |
| VendorAddressRecipient | String | Nom associé à l’adresse postale du fournisseur |  |
| CustomerAddress | String | Adresse postale du client | |
| CustomerAddressRecipient | String | Nom associé à l’adresse postale du client | |
| BillingAddress | String | Adresse de facturation explicite du client |  |
| BillingAddressRecipient | String | Nom associé à l’adresse de facturation | |
| ShippingAddress | String | Adresse d’expédition explicite du client | |
| ShippingAddressRecipient | String | Nom associé à l’adresse d’expédition |  |
| SubTotal | Number | Champ de sous-total identifié sur cette facture | Integer |
| TotalTax | Number | Champ du total des taxes identifié sur cette facture | Integer |
| InvoiceTotal | Nombre (USD) | Total des nouveaux frais associés à cette facture | Integer |
| AmountDue |  Nombre (USD) | Montant total dû au fournisseur | Integer |
| ServiceAddress | String | Adresse du service ou de la propriété explicite du client | |
| ServiceAddressRecipient | String | Nom associé à l’adresse du service |  |
| RemittanceAddress | String | Adresse d’envoi du paiement explicite pour le client |   |
| RemittanceAddressRecipient | String | Nom associé à l’adresse d’envoi du paiement explicite |  |
| ServiceStartDate | Date | Date de début de la période de service (par exemple, pour une période de service de facturation d’utilitaire) | aaaa-mm-jj |
| ServiceEndDate | Date | Date de fin de la période de service (par exemple, pour une période de service de facturation d’utilitaire) | aaaa-mm-jj|
| PreviousUnpaidBalance | Number | Solde précédent impayé explicite | Integer |

### <a name="line-items"></a>Éléments de ligne

Voici les élément de ligne extraits d’une facture dans la réponse de la sortie JSON (la sortie ci-dessous utilise cet [exemple de facture](media/sample-invoice.jpg)) :

|Name| Type | Description | Texte (élément de ligne no 1) | Valeur (sortie standardisée) |
|:-----|:----|:----|:----| :----|
| Éléments | String | Texte complet de l’élément de ligne | 04/03/2021 A123 Services de conseil 2 heures 30,00 $ 10 % 60,00 $ | |
| Montant | Number | Montant de l’élément de ligne | 60,00 $ | 100 |
| Description | String | Description textuelle de l’élément de ligne de la facture | Service de conseil | Service de conseil |
| Quantité | Number | Quantité pour cet élément de ligne de la facture | 2 | 2 |
| UnitPrice | Number | Prix net ou brut (en fonction du paramètre de facturation brute de la facture) d’une unité de cet élément | 30,00 $ | 30 |
| ProductCode | String| Code du produit, numéro de produit ou référence SKU associé à l’élément de ligne spécifique | A123 | |
| Unité | String| L’unité de l’élément de ligne (par exemple : kg, lb, etc.) | Heures | |
| Date | Date| Date correspondant à chaque élément de ligne. Il s’agit souvent de la date d’expédition de l’élément de ligne | 04/03/2021| 04-03-2021 |
| Taxe | Number | Taxe associée à chaque élément de ligne. Les valeurs possibles incluent le montant des taxes, le pourcentage de la taxe et la valeur d’application de la taxe (O/N) | 10 % | |

Les paires clé-valeur et les éléments de ligne de facture extraits se trouvent dans la section `documentResults` de la sortie JSON. 

## <a name="form-recognizer-preview-v30"></a>Form Recognizer préversion v3.0

 La version préliminaire de Form Recognizer introduit plusieurs nouvelles fonctionnalités.

* Suivez notre [**guide de migration Form Recognizer v3.0**](v3-migration-guide.md) pour apprendre à utiliser la préversion dans vos applications et workflows.

* Découvrez notre [**API REST (préversion)**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument) pour en savoir plus sur la préversion et les nouvelles fonctionnalités.

## <a name="next-steps"></a>Étapes suivantes

* Suivre un démarrage rapide Form Recognizer :

  > [!div class="nextstepaction"]
  > [Démarrage rapide Form Recognizer](quickstarts/try-sdk-rest-api.md)

* Découvrir notre API REST :

    > [!div class="nextstepaction"]
    > [API Form Recognizer v2.1](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/5ed8c9843c2794cbb1a96291)
