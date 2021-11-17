---
title: 'Démarrage rapide : Form Recognizer v3.0 avec kit SDK Python | Préversion'
titleSuffix: Azure Applied AI Services
description: Traitement de formulaires et de documents, extraction de données et analyse à l’aide de Form Recognizer v3.0 avec les kits SDK de la bibliothèque de client Python (préversion)
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 11/02/2021
ms.author: lajanuar
recommendations: false
ms.custom: ignite-fall-2021
ms.openlocfilehash: b2383b52184d4cdd66aaeebe2ec34c1ed4b62a1d
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132371692"
---
# <a name="quickstart-python-client-library-sdk-v30--preview"></a>Démarrage rapide : SDK de bibliothèque cliente Python v3.0 | Préversion

>[!NOTE]
> Form Recognizer v3.0 est actuellement en préversion publique. Certaines fonctionnalités peuvent ne pas être prises en charge ou avoir des capacités limitées. 

[Documentation de référence](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-formrecognizer/latest/azure.ai.formrecognizer.html) | [Code source de la bibliothèque](https://github.com/Azure/azure-sdk-for-python/tree/main/sdk/formrecognizer/azure-ai-formrecognizer/azure/ai/formrecognizer) | [Package (PyPi)](https://pypi.org/project/azure-ai-formrecognizer/) | [Exemples](https://github.com/Azure/azure-sdk-for-python/tree/main/sdk/formrecognizer/azure-ai-formrecognizer/samples)

 Démarrez avec Azure Form Recognizer en utilisant le langage de programmation Python. Azure Form Recognizer est un service Azure Applied AI Service qui utilise le machine learning pour extraire et analyser les champs de formulaire, le texte et les tableaux de vos documents. Vous pouvez facilement appeler des modèles Form Recognizer en intégrant les kits SDK de notre bibliothèque de client dans vos workflows et applications. Nous vous recommandons d’utiliser le service gratuit pendant que vous apprenez la technologie. N’oubliez pas que le nombre de pages gratuites est limité à 500 par mois.

Pour en savoir plus sur les fonctionnalités Form Recognizer et les options de développement, visitez notre page de [présentation](../overview.md#form-recognizer-features-and-development-options).

Dans ce guide de démarrage rapide, vous allez utiliser les fonctionnalités suivantes pour analyser et extraire les données et les valeurs de formulaires et de documents :

* [🆕 **Document général**](#try-it-general-document-model) – Analysez et extrayez le texte, les tableaux, la structure, les paires clé-valeur et les entités nommées.|

* [**Disposition**](#try-it-layout-model) – Analysez et extrayez les tableaux, les lignes, les mots et les marques de sélection telles que les cases d’option et les cases à cocher dans des formulaires, sans avoir besoin d’entraîner un modèle.

* [**Facture prédéfinie**](#try-it-prebuilt-model)Analysez et extrayez les champs courants des factures en utilisant un modèle de facture préentraîné.

## <a name="prerequisites"></a>Prérequis

* Abonnement Azure - [En créer un gratuitement](https://azure.microsoft.com/free/cognitive-services)

* [Python 3.x](https://www.python.org/)

  * Votre installation Python doit inclure [pip](https://pip.pypa.io/en/stable/). Vous pouvez vérifier si pip est installé en exécutant `pip --version` sur la ligne de commande. Procurez-vous pip en installant la dernière version de Python.

* Une ressource Cognitive Services ou Form Recognizer. Une fois que vous avez votre abonnement Azure, créez une ressource Form Recognizer [monoservice](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) ou [multiservice](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne) dans le portail Azure pour obtenir votre clé et votre point de terminaison. Vous pouvez utiliser le niveau tarifaire Gratuit (`F0`) pour tester le service, puis passer par la suite à un niveau payant pour la production.

> [!TIP]
> Créez une ressource Cognitive Services si vous envisagez d’accéder à plusieurs services Cognitive Services sous un seul point de terminaison/clé. Pour l’accès à Form Recognizer uniquement, créez une ressource Form Recognizer. Notez que vous avez besoin d’une ressource monoservice si vous avez l’intention d’utiliser l’[authentification Azure Active Directory](../../../active-directory/authentication/overview-authentication.md).

* Après le déploiement de votre ressource, sélectionnez **Accéder à la ressource**. Vous avez besoin de la clé et du point de terminaison de la ressource que vous créez pour connecter votre application à l’API Form Recognizer. Vous collerez votre clé et votre point de terminaison dans le code ci-dessous plus loin dans le guide de démarrage rapide :

  :::image type="content" source="../media/containers/keys-and-endpoint.png" alt-text="Capture d’écran : clés et emplacement du point de terminaison dans le portail Azure":::

## <a name="set-up"></a>Configurer

Ouvrez une fenêtre de terminal dans votre environnement local et installez la bibliothèque de client Azure Form Recognizer pour Python avec pip :

```console
pip install azure-ai-formrecognizer --pre

```

### <a name="create-a-new-python-application"></a>Créer une application Python

Créez une application Python nommée **form_recognizer_quickstart.py** dans votre éditeur ou IDE favori. Importez ensuite les bibliothèques suivantes :

```python
import os
from azure.core.exceptions import ResourceNotFoundError
from azure.ai.formrecognizer import DocumentAnalysisClient
from azure.core.credentials import AzureKeyCredential
```

### <a name="create-variables-for-your-azure-resource-endpoint-and-key"></a>Créer des variables pour le point de terminaison et la clé de votre ressource Azure

```python
endpoint = "YOUR_FORM_RECOGNIZER_ENDPOINT"
key = "YOUR_FORM_RECOGNIZER_SUBSCRIPTION_KEY"
```

À ce stade, votre application Python doit contenir les lignes de code suivantes :

```python
import os
from azure.core.exceptions import ResourceNotFoundError
from azure.ai.formrecognizer import DocumentAnalysisClient
from azure.core.credentials import AzureKeyCredential

endpoint = "YOUR_FORM_RECOGNIZER_ENDPOINT"
key = "YOUR_FORM_RECOGNIZER_SUBSCRIPTION_KEY"

```

### <a name="select-a-code-sample-to-copy-and-paste-into-your-application"></a>Sélectionnez un exemple de code à copier-coller dans votre application :

* [**Document général**](#try-it-general-document-model)

* [**Layout**](#try-it-layout-model)

* [**Facture prédéfinie**](#try-it-prebuilt-model)

> [!IMPORTANT]
>
> N’oubliez pas de supprimer la clé de votre code une fois que vous avez terminé, et ne la postez jamais publiquement. En production, utilisez des méthodes sécurisées pour stocker vos informations d’identification et y accéder. Pour plus d’informations, consultez l’article sur la [sécurité](../../../cognitive-services/cognitive-services-security.md) de Cognitive Services.

## <a name="try-it-general-document-model"></a>**Essayez** : modèle de document général

> [!div class="checklist"]
>
> * Pour cet exemple, vous aurez besoin d’un **fichier de formulaire au niveau d’un URI**. Vous pouvez utiliser notre [exemple de formulaire](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-layout.pdf) pour ce guide de démarrage rapide.
> * Pour analyser un fichier donné au niveau d’un URI, vous utilisez la méthode `begin_analyze_document` et passez `prebuilt-document` comme ID de modèle. La valeur retournée est un objet `result` contenant des données sur le document envoyé.
> * Nous avons ajouté la valeur de l’URI du fichier à la variable `formUrl` en haut du fichier.
> * Par souci de simplicité, tous les champs d’entité retournés par le service ne sont pas indiqués ici. Pour afficher la liste de tous les champs pris en charge et les types correspondants, consultez notre page conceptuelle [Document général](../concept-general-document.md#named-entity-recognition-ner-categories).

### <a name="add-the-following-code-to-your-general-document-application-on-the-line-below-the-key-variable"></a>Ajouter le code suivant à votre application de document général sur la ligne située sous la variable `key`

```python

def format_bounding_region(bounding_regions):
    if not bounding_regions:
        return "N/A"
    return ", ".join("Page #{}: {}".format(region.page_number, format_bounding_box(region.bounding_box)) for region in bounding_regions)

def format_bounding_box(bounding_box):
    if not bounding_box:
        return "N/A"
    return ", ".join(["[{}, {}]".format(p.x, p.y) for p in bounding_box])


def analyze_general_documents():

    formUrl = "https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-layout.pdf"

    document_analysis_client = DocumentAnalysisClient(
        endpoint=endpoint, credential=AzureKeyCredential(key)
    )

    poller = document_analysis_client.begin_analyze_document_from_url(
            "prebuilt-document", formUrl)
    result = poller.result()

    for style in result.styles:
        if style.is_handwritten:
            print("Document contains handwritten content: ")
            print(",".join([result.content[span.offset:span.offset + span.length] for span in style.spans]))

    print("----Key-value pairs found in document----")
    for kv_pair in result.key_value_pairs:
        if kv_pair.key:
            print(
                    "Key '{}' found within '{}' bounding regions".format(
                        kv_pair.key.content,
                        format_bounding_region(kv_pair.key.bounding_regions),
                    )
                )
        if kv_pair.value:
            print(
                    "Value '{}' found within '{}' bounding regions\n".format(
                        kv_pair.value.content,
                        format_bounding_region(kv_pair.value.bounding_regions),
                    )
                )

    print("----Entities found in document----")
    for entity in result.entities:
        print("Entity of category '{}' with sub-category '{}'".format(entity.category, entity.sub_category))
        print("...has content '{}'".format(entity.content))
        print("...within '{}' bounding regions".format(format_bounding_region(entity.bounding_regions)))
        print("...with confidence {}\n".format(entity.confidence))

    for page in result.pages:
        print("----Analyzing document from page #{}----".format(page.page_number))
        print(
            "Page has width: {} and height: {}, measured with unit: {}".format(
                page.width, page.height, page.unit
            )
        )

        for line_idx, line in enumerate(page.lines):
            print(
                "...Line # {} has text content '{}' within bounding box '{}'".format(
                    line_idx,
                    line.content,
                    format_bounding_box(line.bounding_box),
                )
            )

        for word in page.words:
            print(
                "...Word '{}' has a confidence of {}".format(
                    word.content, word.confidence
                )
            )

        for selection_mark in page.selection_marks:
            print(
                "...Selection mark is '{}' within bounding box '{}' and has a confidence of {}".format(
                    selection_mark.state,
                    format_bounding_box(selection_mark.bounding_box),
                    selection_mark.confidence,
                )
            )

    for table_idx, table in enumerate(result.tables):
        print(
            "Table # {} has {} rows and {} columns".format(
                table_idx, table.row_count, table.column_count
            )
        )
        for region in table.bounding_regions:
            print(
                "Table # {} location on page: {} is {}".format(
                    table_idx,
                    region.page_number,
                    format_bounding_box(region.bounding_box),
                )
            )
        for cell in table.cells:
            print(
                "...Cell[{}][{}] has content '{}'".format(
                    cell.row_index,
                    cell.column_index,
                    cell.content,
                )
            )
            for region in cell.bounding_regions:
                print(
                    "...content on page {} is within bounding box '{}'\n".format(
                        region.page_number,
                        format_bounding_box(region.bounding_box),
                    )
                )
    print("----------------------------------------")


if __name__ == "__main__":
    analyze_general_documents()
```

## <a name="try-it-layout-model"></a>**Essayez** : modèle de disposition

> [!div class="checklist"]
>
> * Pour cet exemple, vous aurez besoin d’un **fichier de formulaire au niveau d’un URI**. Vous pouvez utiliser notre [exemple de formulaire](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-layout.pdf) pour ce guide de démarrage rapide.
> * Nous avons ajouté la valeur de l’URI du fichier à la variable `formUrl` en haut du fichier.
> * Pour analyser un fichier donné au niveau d’un URI, vous utilisez la méthode `begin_analyze_document` et passez `prebuilt-layout` comme ID de modèle. La valeur retournée est un objet `result` contenant des données sur le document envoyé.

### <a name="add-the-following-code-to-your-layout-application-on-the-line-below-the-key-variable"></a>Ajouter le code suivant à votre application de disposition sur la ligne située sous la variable `key`

```python

def format_bounding_box(bounding_box):
    if not bounding_box:
        return "N/A"
    return ", ".join(["[{}, {}]".format(p.x, p.y) for p in bounding_box])


def analyze_layout():
    # sample form document
    formUrl = "https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-layout.pdf"

    document_analysis_client = DocumentAnalysisClient(
        endpoint=endpoint, credential=AzureKeyCredential(key)
    )

    poller = document_analysis_client.begin_analyze_document_from_url(
            "prebuilt-layout", formUrl)
    result = poller.result()

    for idx, style in enumerate(result.styles):
        print(
            "Document contains {} content".format(
                "handwritten" if style.is_handwritten else "no handwritten"
            )
        )

```

## <a name="try-it-prebuilt-model"></a>**Essayez** : Modèle prédéfini

Cet exemple montre comment analyser les données de certains types de documents courants avec un modèle préentraîné, à l’aide d’une facture à titre d’exemple.

> [!div class="checklist"]
>
> * Pour cet exemple, nous analysons un document de facture à l’aide d’un modèle prédéfini. Vous pouvez utiliser notre [exemple de facture](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-invoice.pdf) pour ce guide de démarrage rapide.
> * Nous avons ajouté la valeur de l’URI du fichier à la variable `string fileUri` en haut du fichier.
> * Pour analyser un fichier donné au niveau d’un URI, vous utilisez la méthode `begin_analyze_document` et passez `prebuilt-invoice` comme ID de modèle. La valeur retournée est un objet `result` contenant des données sur le document envoyé.
> * Par souci de simplicité, toutes les paires clé-valeur retournées par le service ne sont pas indiquées ici. Pour afficher la liste de tous les champs pris en charge et les types correspondants, consultez notre page conceptuelle [Facture](../concept-invoice.md#field-extraction).

### <a name="choose-the-invoice-prebuilt-model-id"></a>Choisir l’ID de modèle de facture prédéfinie

Vous n’êtes pas limité aux factures – Vous avez le choix entre plusieurs modèles prédéfinis, chacun ayant son propre ensemble de champs pris en charge. Le modèle à utiliser pour l’opération d’analyse dépend du type de document à analyser. Voici les ID de modèle pour les modèles prédéfinis actuellement pris en charge par le service Form Recognizer :

* [**Facture prédéfinie**](../concept-invoice.md) : extrait le texte, les marques de sélection, les tableaux, les paires clé-valeur et les informations clés des factures.
* [**Reçu prédéfini**](../concept-receipt.md) : extrait le texte et les informations clés des reçus.
* [**Document d’identité prédéfini**](../concept-id-document.md) : extrait le texte et les informations clés des permis de conduire et des passeports internationaux.
* [**Carte de visite prédéfinie**](../concept-business-card.md) : extrait le texte et les informations clés des cartes de visite.

### <a name="add-the-following-code-to-your-prebuilt-invoice-application-below-the-key-variable"></a>Ajouter le code suivant à votre application de facture prédéfinie sous la variable `key`

```python

def format_bounding_region(bounding_regions):
    if not bounding_regions:
        return "N/A"
    return ", ".join("Page #{}: {}".format(region.page_number, format_bounding_box(region.bounding_box)) for region in bounding_regions)

def format_bounding_box(bounding_box):
    if not bounding_box:
        return "N/A"
    return ", ".join(["[{}, {}]".format(p.x, p.y) for p in bounding_box])


def analyze_invoice():

    formUrl = "https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-invoice.pdf"

    document_analysis_client = DocumentAnalysisClient(
        endpoint=endpoint, credential=AzureKeyCredential(key)
    )

    poller = document_analysis_client.begin_analyze_document_from_url(
            "prebuilt-invoice", formUrl)
    invoices = poller.result()

    for idx, invoice in enumerate(invoices.documents):
        print("--------Recognizing invoice #{}--------".format(idx + 1))
        vendor_name = invoice.fields.get("VendorName")
        if vendor_name:
            print(
                "Vendor Name: {} has confidence: {}".format(
                    vendor_name.value, vendor_name.confidence
                )
            )
        vendor_address = invoice.fields.get("VendorAddress")
        if vendor_address:
            print(
                "Vendor Address: {} has confidence: {}".format(
                    vendor_address.value, vendor_address.confidence
                )
            )
        vendor_address_recipient = invoice.fields.get("VendorAddressRecipient")
        if vendor_address_recipient:
            print(
                "Vendor Address Recipient: {} has confidence: {}".format(
                    vendor_address_recipient.value, vendor_address_recipient.confidence
                )
            )
        customer_name = invoice.fields.get("CustomerName")
        if customer_name:
            print(
                "Customer Name: {} has confidence: {}".format(
                    customer_name.value, customer_name.confidence
                )
            )
        customer_id = invoice.fields.get("CustomerId")
        if customer_id:
            print(
                "Customer Id: {} has confidence: {}".format(
                    customer_id.value, customer_id.confidence
                )
            )
        customer_address = invoice.fields.get("CustomerAddress")
        if customer_address:
            print(
                "Customer Address: {} has confidence: {}".format(
                    customer_address.value, customer_address.confidence
                )
            )
        customer_address_recipient = invoice.fields.get("CustomerAddressRecipient")
        if customer_address_recipient:
            print(
                "Customer Address Recipient: {} has confidence: {}".format(
                    customer_address_recipient.value,
                    customer_address_recipient.confidence,
                )
            )
        invoice_id = invoice.fields.get("InvoiceId")
        if invoice_id:
            print(
                "Invoice Id: {} has confidence: {}".format(
                    invoice_id.value, invoice_id.confidence
                )
            )
        invoice_date = invoice.fields.get("InvoiceDate")
        if invoice_date:
            print(
                "Invoice Date: {} has confidence: {}".format(
                    invoice_date.value, invoice_date.confidence
                )
            )
        invoice_total = invoice.fields.get("InvoiceTotal")
        if invoice_total:
            print(
                "Invoice Total: {} has confidence: {}".format(
                    invoice_total.value, invoice_total.confidence
                )
            )
        due_date = invoice.fields.get("DueDate")
        if due_date:
            print(
                "Due Date: {} has confidence: {}".format(
                    due_date.value, due_date.confidence
                )
            )
        purchase_order = invoice.fields.get("PurchaseOrder")
        if purchase_order:
            print(
                "Purchase Order: {} has confidence: {}".format(
                    purchase_order.value, purchase_order.confidence
                )
            )
        billing_address = invoice.fields.get("BillingAddress")
        if billing_address:
            print(
                "Billing Address: {} has confidence: {}".format(
                    billing_address.value, billing_address.confidence
                )
            )
        billing_address_recipient = invoice.fields.get("BillingAddressRecipient")
        if billing_address_recipient:
            print(
                "Billing Address Recipient: {} has confidence: {}".format(
                    billing_address_recipient.value,
                    billing_address_recipient.confidence,
                )
            )
        shipping_address = invoice.fields.get("ShippingAddress")
        if shipping_address:
            print(
                "Shipping Address: {} has confidence: {}".format(
                    shipping_address.value, shipping_address.confidence
                )
            )
        shipping_address_recipient = invoice.fields.get("ShippingAddressRecipient")
        if shipping_address_recipient:
            print(
                "Shipping Address Recipient: {} has confidence: {}".format(
                    shipping_address_recipient.value,
                    shipping_address_recipient.confidence,
                )
            )
        print("Invoice items:")
        for idx, item in enumerate(invoice.fields.get("Items").value):
            print("...Item #{}".format(idx + 1))
            item_description = item.value.get("Description")
            if item_description:
                print(
                    "......Description: {} has confidence: {}".format(
                        item_description.value, item_description.confidence
                    )
                )
            item_quantity = item.value.get("Quantity")
            if item_quantity:
                print(
                    "......Quantity: {} has confidence: {}".format(
                        item_quantity.value, item_quantity.confidence
                    )
                )
            unit = item.value.get("Unit")
            if unit:
                print(
                    "......Unit: {} has confidence: {}".format(
                        unit.value, unit.confidence
                    )
                )
            unit_price = item.value.get("UnitPrice")
            if unit_price:
                print(
                    "......Unit Price: {} has confidence: {}".format(
                        unit_price.value, unit_price.confidence
                    )
                )
            product_code = item.value.get("ProductCode")
            if product_code:
                print(
                    "......Product Code: {} has confidence: {}".format(
                        product_code.value, product_code.confidence
                    )
                )
            item_date = item.value.get("Date")
            if item_date:
                print(
                    "......Date: {} has confidence: {}".format(
                        item_date.value, item_date.confidence
                    )
                )
            tax = item.value.get("Tax")
            if tax:
                print(
                    "......Tax: {} has confidence: {}".format(tax.value, tax.confidence)
                )
            amount = item.value.get("Amount")
            if amount:
                print(
                    "......Amount: {} has confidence: {}".format(
                        amount.value, amount.confidence
                    )
                )
        subtotal = invoice.fields.get("SubTotal")
        if subtotal:
            print(
                "Subtotal: {} has confidence: {}".format(
                    subtotal.value, subtotal.confidence
                )
            )
        total_tax = invoice.fields.get("TotalTax")
        if total_tax:
            print(
                "Total Tax: {} has confidence: {}".format(
                    total_tax.value, total_tax.confidence
                )
            )
        previous_unpaid_balance = invoice.fields.get("PreviousUnpaidBalance")
        if previous_unpaid_balance:
            print(
                "Previous Unpaid Balance: {} has confidence: {}".format(
                    previous_unpaid_balance.value, previous_unpaid_balance.confidence
                )
            )
        amount_due = invoice.fields.get("AmountDue")
        if amount_due:
            print(
                "Amount Due: {} has confidence: {}".format(
                    amount_due.value, amount_due.confidence
                )
            )
        service_start_date = invoice.fields.get("ServiceStartDate")
        if service_start_date:
            print(
                "Service Start Date: {} has confidence: {}".format(
                    service_start_date.value, service_start_date.confidence
                )
            )
        service_end_date = invoice.fields.get("ServiceEndDate")
        if service_end_date:
            print(
                "Service End Date: {} has confidence: {}".format(
                    service_end_date.value, service_end_date.confidence
                )
            )
        service_address = invoice.fields.get("ServiceAddress")
        if service_address:
            print(
                "Service Address: {} has confidence: {}".format(
                    service_address.value, service_address.confidence
                )
            )
        service_address_recipient = invoice.fields.get("ServiceAddressRecipient")
        if service_address_recipient:
            print(
                "Service Address Recipient: {} has confidence: {}".format(
                    service_address_recipient.value,
                    service_address_recipient.confidence,
                )
            )
        remittance_address = invoice.fields.get("RemittanceAddress")
        if remittance_address:
            print(
                "Remittance Address: {} has confidence: {}".format(
                    remittance_address.value, remittance_address.confidence
                )
            )
        remittance_address_recipient = invoice.fields.get("RemittanceAddressRecipient")
        if remittance_address_recipient:
            print(
                "Remittance Address Recipient: {} has confidence: {}".format(
                    remittance_address_recipient.value,
                    remittance_address_recipient.confidence,
                )
            )

if __name__ == "__main__":
    analyze_invoice()
```

## <a name="run-your-application"></a>Exécuter votre application

1. Accédez au dossier contenant votre fichier **form_recognizer_quickstart.py**.

1. Tapez la commande suivante dans votre terminal :

```console
python form_recognizer_quickstart.py
```

Félicitations ! Dans ce guide de démarrage rapide, vous avez utilisé Form Recognizer avec le kit SDK Python pour analyser divers formulaires de différentes manières. Explorez à présent la documentation de référence pour en savoir plus sur l’API Form Recognizer v3.0.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Documentation de référence de l’API REST v3.0](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument)

> [!div class="nextstepaction"]
> [Bibliothèque de référence Python Form Recognizer](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-formrecognizer/3.2.0b1/index.html)
