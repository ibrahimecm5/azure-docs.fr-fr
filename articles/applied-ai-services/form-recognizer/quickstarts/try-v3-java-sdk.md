---
title: 'Démarrage rapide : Form Recognizer v3.0 avec SDK Java | Préversion'
titleSuffix: Azure Applied AI Services
description: Traitement, extraction des données et analyse des formulaires et des documents à l’aide de Form Recognizer v3.0 avec les SDK de la bibliothèque de client Java (préversion)
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 11/02/2021
ms.author: lajanuar
recommendations: false
ms.custom: ignite-fall-2021
ms.openlocfilehash: 23d7ef3bf6eb29e81723cb102427ef32d8bc83b0
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131028871"
---
# <a name="quickstart-java-client-library-sdk-v30--preview"></a>Démarrage rapide : SDK de la bibliothèque de client Java v3.0 | Préversion

>[!NOTE]
> Form Recognizer v3.0 est actuellement en préversion publique. Certaines fonctionnalités peuvent ne pas être prises en charge ou avoir des capacités limitées.

[Documentation de référence](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-ai-formrecognizer/4.0.0-beta.1/index.html) | [Code source de la bibliothèque](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src) | [Package (Maven)](https://mvnrepository.com/artifact/com.azure/azure-ai-formrecognizer) | [Exemples](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src/samples/README.md)

Bien démarrer avec Azure Form Recognizer en utilisant le langage de programmation Java. Azure Form Recognizer est un service Azure Applied AI Service qui utilise le machine learning pour extraire et analyser les champs de formulaire, le texte et les tableaux de vos documents. Vous pouvez facilement appeler des modèles Form Recognizer en intégrant les kits SDK de notre bibliothèque de client dans vos workflows et applications. Nous vous recommandons d’utiliser le service gratuit pendant que vous apprenez la technologie. N’oubliez pas que le nombre de pages gratuites est limité à 500 par mois.

Pour en savoir plus sur les fonctionnalités Form Recognizer et les options de développement, visitez notre page de [présentation](../overview.md#form-recognizer-features-and-development-options).

Dans ce guide de démarrage rapide, vous allez utiliser les fonctionnalités suivantes pour analyser et extraire les données et les valeurs de formulaires et de documents :

* [🆕 **Document général**](#try-it-general-document-model) – Analysez et extrayez le texte, les tableaux, la structure, les paires clé-valeur et les entités nommées.|

* [**Disposition**](#try-it-layout-model) – Analysez et extrayez les tableaux, les lignes, les mots et les marques de sélection telles que les cases d’option et les cases à cocher dans des formulaires, sans avoir besoin d’entraîner un modèle.

* [**Facture prédéfinie**](#try-it-prebuilt-model)Analysez et extrayez les champs courants des factures en utilisant un modèle de facture préentraîné.

## <a name="prerequisites"></a>Prérequis

* Abonnement Azure : [créez-en un gratuitement](https://azure.microsoft.com/free/cognitive-services/).
* Un [Kit de développement Java (JDK)](/java/azure/jdk/?view=azure-java-stable&preserve-view=true
), version 8 ou ultérieure
* Une ressource Cognitive Services ou Form Recognizer. Une fois que vous avez votre abonnement Azure, créez une ressource Form Recognizer [monoservice](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) ou [multiservice](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne) dans le portail Azure pour obtenir votre clé et votre point de terminaison. Vous pouvez utiliser le niveau tarifaire Gratuit (`F0`) pour tester le service, puis passer par la suite à un niveau payant pour la production.

> [!TIP] 
> Créez une ressource Cognitive Services si vous envisagez d’accéder à plusieurs services Cognitive Services sous un seul point de terminaison/clé. Pour l’accès à Form Recognizer uniquement, créez une ressource Form Recognizer. Notez que vous avez besoin d’une ressource monoservice si vous avez l’intention d’utiliser l’[authentification Azure Active Directory](/azure/active-directory/authentication/overview-authentication).

* Une fois que votre ressource est déployée, cliquez sur **Accéder à la ressource**. Vous avez besoin de la clé et du point de terminaison de la ressource que vous créez pour connecter votre application à l’API Form Recognizer. Vous collerez votre clé et votre point de terminaison dans le code ci-dessous plus loin dans ce guide de démarrage rapide :

  :::image type="content" source="../media/containers/keys-and-endpoint.png" alt-text="Capture d’écran : clés et emplacement du point de terminaison dans le portail Azure":::

## <a name="set-up"></a>Configurer

### <a name="create-a-new-gradle-project"></a>Créer un projet Gradle

Dans une fenêtre de console (par exemple, cmd, PowerShell ou Bash), créez le répertoire **form-recognizer-app** pour votre application et accédez-y.

```console
mkdir form-recognizer-app && form-recognizer-app
```

Exécutez la commande `gradle init` à partir de votre répertoire de travail. Cette commande crée des fichiers de build essentiels pour Gradle, notamment *build.gradle.kts*, qui est utilisé au moment de l’exécution pour créer et configurer votre application.

```console
gradle init --type basic
```

1. Quand vous êtes invité à choisir un **DSL**, sélectionnez **Kotlin**.

1. Acceptez le nom de projet par défaut (form-recognizer-app)

### <a name="install-the-client-library"></a>Installer la bibliothèque de client

Ce guide de démarrage rapide utilise le gestionnaire de dépendances Gradle. Vous trouverez la bibliothèque de client et des informations concernant d’autres gestionnaires de dépendances sur le [référentiel central Maven](https://mvnrepository.com/artifact/com.azure/azure-ai-formrecognizer).

Dans le fichier *build.gradle.kts* de votre projet, incluez la bibliothèque de client sous la forme d’une instruction `implementation`, avec les plug-ins et les paramètres obligatoires.

```kotlin
plugins {
    java
    application
}
application {
    mainClass.set("FormRecognizer")
}
repositories {
    mavenCentral()
}
dependencies {
    implementation(group = "com.azure", name = "azure-ai-formrecognizer", version = "4.0.0-beta.1")
}
```

### <a name="create-a-java-file"></a>Créer un fichier Java

Entrez la commande suivante à partir de votre répertoire de travail :

```console
mkdir -p src/main/java
```

Vous créez la structure de répertoires suivante :

:::image type="content" source="../media/quickstarts/java-directories.png" alt-text="Capture d’écran : structure de répertoires Java":::

Accédez au nouveau dossier et créez le fichier *FormRecognizer.java* (créé pendant l’exécution de la commande `gradle init`). Ouvrez-le dans votre éditeur ou IDE par défaut et ajoutez la déclaration de package et les instructions `import` suivantes :

```java
package com.azure.ai.formrecognizer;

import com.azure.ai.formrecognizer.models.AnalyzeDocumentOptions;
import com.azure.ai.formrecognizer.models.AnalyzedDocument;
import com.azure.core.credential.AzureKeyCredential;
import com.azure.core.http.HttpPipeline;
import com.azure.core.http.HttpPipelineBuilder;
import com.azure.core.util.Context;

import java.io.ByteArrayInputStream;
import java.io.File;
import java.io.IOException;
import java.io.InputStream;
import java.nio.file.Files;
import java.util.Arrays;
```

### <a name="select-a-code-sample-to-copy-and-paste-into-your-applications-main-method"></a>Sélectionnez un exemple de code à copier-coller dans la méthode main de votre application :

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
> * Pour analyser un fichier donné au niveau d’un URI, vous utilisez la méthode `beginAnalyzeDocumentFromUrl` et passez `prebuilt-document` comme ID de modèle. La valeur retournée est un objet `AnalyzeResult` contenant des données sur le document envoyé.
> * Nous avons ajouté la valeur de l’URI du fichier à la variable `documentUrl` en haut de la méthode main.
> * Par souci de simplicité, tous les champs d’entité retournés par le service ne sont pas indiqués ici. Pour afficher la liste de tous les champs pris en charge et les types correspondants, consultez notre page conceptuelle [Document général](../concept-general-document.md#named-entity-recognition-ner-categories).

Mettez à jour la classe **FormRecognizer** de votre application avec le code suivant (veillez à mettre à jour les variables de point de terminaison et de clé avec les valeurs de votre instance Form Recognizer dans le portail Azure) :

```java
public class FormRecognizer {

    static final String key = "PASTE_YOUR_FORM_RECOGNIZER_SUBSCRIPTION_KEY_HERE";
    static final String endpoint = "PASTE_YOUR_FORM_RECOGNIZER_ENDPOINT_HERE";

    public static void main(String[] args) {

        DocumentAnalysisClient documentAnalysisClient = new DocumentAnalysisClientBuilder()
            .credential(new AzureKeyCredential("{key}"))
            .endpoint("{endpoint}")
            .buildClient();

        String documentUrl = "https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-layout.pdf";
        String modelId = "prebuilt-document";
        SyncPoller < DocumentOperationResult, AnalyzeResult > analyzeDocumentPoller =
            documentAnalysisClient.beginAnalyzeDocumentFromUrl(modelId, documentUrl);

        AnalyzeResult analyzeResult = analyzeDocumentPoller.getFinalResult();

        for (int i = 0; i < analyzeResult.getDocuments().size(); i++) {
            final AnalyzedDocument analyzedDocument = analyzeResult.getDocuments().get(i);
            System.out.printf("----------- Analyzing document %d -----------%n", i);
            System.out.printf("Analyzed document has doc type %s with confidence : %.2f%n",
                analyzedDocument.getDocType(), analyzedDocument.getConfidence());
        }

        analyzeResult.getPages().forEach(documentPage - > {
            System.out.printf("Page has width: %.2f and height: %.2f, measured with unit: %s%n",
                documentPage.getWidth(),
                documentPage.getHeight(),
                documentPage.getUnit());

            // lines
            documentPage.getLines().forEach(documentLine - >
                System.out.printf("Line %s is within a bounding box %s.%n",
                    documentLine.getContent(),
                    documentLine.getBoundingBox().toString()));

            // words
            documentPage.getWords().forEach(documentWord - >
                System.out.printf("Word %s has a confidence score of %.2f%n.",
                    documentWord.getContent(),
                    documentWord.getConfidence()));
        });

        // tables
        List < DocumentTable > tables = analyzeResult.getTables();
        for (int i = 0; i < tables.size(); i++) {
            DocumentTable documentTable = tables.get(i);
            System.out.printf("Table %d has %d rows and %d columns.%n", i, documentTable.getRowCount(),
                documentTable.getColumnCount());
            documentTable.getCells().forEach(documentTableCell - > {
                System.out.printf("Cell '%s', has row index %d and column index %d.%n",
                    documentTableCell.getContent(),
                    documentTableCell.getRowIndex(), documentTableCell.getColumnIndex());
            });
            System.out.println();
        }

        // Entities
        analyzeResult.getEntities().forEach(documentEntity - > {
            System.out.printf("Entity category : %s, sub-category %s%n: ",
                documentEntity.getCategory(), documentEntity.getSubCategory());
            System.out.printf("Entity content: %s%n: ", documentEntity.getContent());
            System.out.printf("Entity confidence: %.2f%n", documentEntity.getConfidence());
        });

        // Key-value
        analyzeResult.getKeyValuePairs().forEach(documentKeyValuePair - > {
            System.out.printf("Key content: %s%n", documentKeyValuePair.getKey().getContent());
            System.out.printf("Key content bounding region: %s%n",
                documentKeyValuePair.getKey().getBoundingRegions().toString());

            System.out.printf("Value content: %s%n", documentKeyValuePair.getValue().getContent());
            System.out.printf("Value content bounding region: %s%n", documentKeyValuePair.getValue().getBoundingRegions().toString());
        });
        Build a custom document analysis model
        In 3. x.x, creating a custom model required specifying useTrainingLabels to indicate whether to use labeled data when creating the custom model with the beginTraining method.
        In 4. x.x, we introduced the new general document model(prebuilt - document) to replace the train without labels functionality from 3. x.x which extracts entities, key - value pairs, and layout from a document with the beginBuildModel method.In 4. x.x the beginBuildModel always returns labeled data otherwise.
        Train a custom model using 3. x.x beginTraining:

            String trainingFilesUrl = "{SAS_URL_of_your_container_in_blob_storage}";
        SyncPoller < FormRecognizerOperationResult, CustomFormModel > trainingPoller =
            formTrainingClient.beginTraining(trainingFilesUrl,
                false,
                new TrainingOptions()
                .setModelName("my model trained without labels"),
                Context.NONE);

        CustomFormModel customFormModel = trainingPoller.getFinalResult();

        // Model Info
        System.out.printf("Model Id: %s%n", customFormModel.getModelId());
        System.out.printf("Model name given by user: %s%n", customFormModel.getModelName());
        System.out.printf("Model Status: %s%n", customFormModel.getModelStatus());
        System.out.printf("Training started on: %s%n", customFormModel.getTrainingStartedOn());
        System.out.printf("Training completed on: %s%n%n", customFormModel.getTrainingCompletedOn());

        System.out.println("Recognized Fields:");
        // looping through the subModels, which contains the fields they were trained on
        // Since the given training documents are unlabeled we still group them but, they do not have a label.
        customFormModel.getSubmodels().forEach(customFormSubmodel - > {
            System.out.printf("Submodel Id: %s%n: ", customFormSubmodel.getModelId());
            // Since the training data is unlabeled, we are unable to return the accuracy of this model
            customFormSubmodel.getFields().forEach((field, customFormModelField) - >
                System.out.printf("Field: %s Field Label: %s%n",
                    field, customFormModelField.getLabel()));
        });

        System.out.println();
        customFormModel.getTrainingDocuments().forEach(trainingDocumentInfo - > {
            System.out.printf("Document name: %s%n", trainingDocumentInfo.getName());
            System.out.printf("Document status: %s%n", trainingDocumentInfo.getStatus());
            System.out.printf("Document page count: %d%n", trainingDocumentInfo.getPageCount());
            if (!trainingDocumentInfo.getErrors().isEmpty()) {
                System.out.println("Document Errors:");
                trainingDocumentInfo.getErrors().forEach(formRecognizerError - >
                    System.out.printf("Error code %s, Error message: %s%n", formRecognizerError.getErrorCode(),
                        formRecognizerError.getMessage()));
            }
        });
    }
```

## <a name="try-it-layout-model"></a>**Essayez** : modèle de disposition

Extrayez le texte, les marques de sélection, les styles de texte et les structures de tableaux, ainsi que les coordonnées de leurs cadres de limitation, à partir de documents.

> [!div class="checklist"]
>
> * Pour cet exemple, vous aurez besoin d’un **fichier de formulaire au niveau d’un URI**. Vous pouvez utiliser notre [exemple de formulaire](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-layout.pdf) pour ce guide de démarrage rapide.
> * Pour analyser un fichier donné au niveau d’un URI, vous utilisez la méthode `beginAnalyzeDocumentFromUrl` et passez `prebuilt-layout` comme ID de modèle. La valeur retournée est un objet `AnalyzeResult` contenant des données sur le document envoyé.
> * Nous avons ajouté la valeur de l’URI du fichier à la variable `documentUrl` en haut de la méthode main.

Mettez à jour la classe **FormRecognizer** de votre application avec le code suivant (veillez à mettre à jour les variables de point de terminaison et de clé avec les valeurs de votre instance Form Recognizer dans le portail Azure) :

```java
public class FormRecognizer {

    static final String key = "PASTE_YOUR_FORM_RECOGNIZER_SUBSCRIPTION_KEY_HERE";
    static final String endpoint = "PASTE_YOUR_FORM_RECOGNIZER_ENDPOINT_HERE";

    public static void main(String[] args) {

        DocumentAnalysisClient documentAnalysisClient = new DocumentAnalysisClientBuilder()
            .credential(new AzureKeyCredential("{key}"))
            .endpoint("{endpoint}")
            .buildClient();

        String documentUrl = "https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-layout.pdf"
        String modelId = "prebuilt-layout";

        SyncPoller < DocumentOperationResult, AnalyzeResult > analyzeLayoutResultPoller =
            documentAnalysisClient.beginAnalyzeDocument(modelId, documentUrl);

        AnalyzeResult analyzeLayoutResult = analyzeLayoutResultPoller.getFinalResult();

        // pages
        analyzeLayoutResult.getPages().forEach(documentPage - > {
            System.out.printf("Page has width: %.2f and height: %.2f, measured with unit: %s%n",
                documentPage.getWidth(),
                documentPage.getHeight(),
                documentPage.getUnit());

            // lines
            documentPage.getLines().forEach(documentLine - >
                System.out.printf("Line %s is within a bounding box %s.%n",
                    documentLine.getContent(),
                    documentLine.getBoundingBox().toString()));

            // selection marks
            documentPage.getSelectionMarks().forEach(documentSelectionMark - >
                System.out.printf("Selection mark is %s and is within a bounding box %s with confidence %.2f.%n",
                    documentSelectionMark.getState().toString(),
                    documentSelectionMark.getBoundingBox().toString(),
                    documentSelectionMark.getConfidence()));
        });

        // tables
        List < DocumentTable > tables = analyzeLayoutResult.getTables();
        for (int i = 0; i < tables.size(); i++) {
            DocumentTable documentTable = tables.get(i);
            System.out.printf("Table %d has %d rows and %d columns.%n", i, documentTable.getRowCount(),
                documentTable.getColumnCount());
            documentTable.getCells().forEach(documentTableCell - > {
                System.out.printf("Cell '%s', has row index %d and column index %d.%n", documentTableCell.getContent(),
                    documentTableCell.getRowIndex(), documentTableCell.getColumnIndex());
            });
            System.out.println();
        }
    }
```

## <a name="try-it-prebuilt-model"></a>**Essayez** : Modèle prédéfini

Cet exemple montre comment analyser les données de certains types de documents courants avec des modèles préentraînés, à l’aide d’une facture à titre d’exemple.

> [!div class="checklist"]
>
> * Pour cet exemple, nous analysons un document de facture à l’aide d’un modèle prédéfini. Vous pouvez utiliser notre [exemple de facture](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-invoice.pdf) pour ce guide de démarrage rapide.
> * Pour analyser un fichier donné au niveau d’un URI, vous utilisez la méthode `beginAnalyzeDocumentFromUrl` et passez `prebuilt-invoice` comme ID de modèle. La valeur retournée est un objet `AnalyzeResult` contenant des données sur le document envoyé.
> * Nous avons ajouté la valeur de l’URI du fichier à la variable `invoiceUrl` en haut de la méthode main.
> * Par souci de simplicité, toutes les paires clé-valeur retournées par le service ne sont pas indiquées ici. Pour afficher la liste de tous les champs pris en charge et les types correspondants, consultez notre page conceptuelle [Facture](../concept-invoice.md#field-extraction).

### <a name="choose-the-invoice-prebuilt-model-id"></a>Choisir l’ID de modèle de facture prédéfinie

Vous n’êtes pas limité aux factures – Vous avez le choix entre plusieurs modèles prédéfinis, chacun ayant son propre ensemble de champs pris en charge. Le modèle à utiliser pour l’opération d’analyse dépend du type de document à analyser. Voici les ID de modèle pour les modèles prédéfinis actuellement pris en charge par le service Form Recognizer :

* [**Facture prédéfinie**](../concept-invoice.md) : extrait le texte, les marques de sélection, les tableaux, les paires clé-valeur et les informations clés des factures.
* [**Reçu prédéfini**](../concept-receipt.md) : extrait le texte et les informations clés des reçus.
* [**Document d’identité prédéfini**](../concept-id-document.md) : extrait le texte et les informations clés des permis de conduire et des passeports internationaux.
* [**Carte de visite prédéfinie**](../concept-business-card.md) : extrait le texte et les informations clés des cartes de visite.

Mettez à jour la classe **FormRecognizer** de votre application avec le code suivant (veillez à mettre à jour les variables de point de terminaison et de clé avec les valeurs de votre instance Form Recognizer dans le portail Azure) :

```java

public class FormRecognizer {

    static final String key = "PASTE_YOUR_FORM_RECOGNIZER_SUBSCRIPTION_KEY_HERE";
    static final String endpoint = "PASTE_YOUR_FORM_RECOGNIZER_ENDPOINT_HERE";

    public static void main(String[] args) {

        DocumentAnalysisClient documentAnalysisClient = new DocumentAnalysisClientBuilder()
            .credential(new AzureKeyCredential("{key}"))
            .endpoint("{endpoint}")
            .buildClient();

        String invoiceUrl = "https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-invoice.pdf"
        String modelId = "prebuilt-invoice";

        PollerFlux < DocumentOperationResult, AnalyzeResult > analyzeInvoicePoller = client.beginAnalyzeDocumentFromUrl("prebuilt-invoice", invoiceUrl);

        Mono < AnalyzeResult > analyzeInvoiceResultMono = analyzeInvoicePoller
            .last()
            .flatMap(pollResponse - > {
                if (pollResponse.getStatus().isComplete()) {
                    System.out.println("Polling completed successfully");
                    return pollResponse.getFinalResult();
                } else {
                    return Mono.error(new RuntimeException("Polling completed unsuccessfully with status:" +
                        pollResponse.getStatus()));
                }
            });

        analyzeInvoiceResultMono.subscribe(analyzeInvoiceResult - > {
            for (int i = 0; i < analyzeInvoiceResult.getDocuments().size(); i++) {
                AnalyzedDocument analyzedInvoice = analyzeInvoiceResult.getDocuments().get(i);
                Map < String, DocumentField > invoiceFields = analyzedInvoice.getFields();
                System.out.printf("----------- Analyzing invoice  %d -----------%n", i);
                DocumentField vendorNameField = invoiceFields.get("VendorName");
                if (vendorNameField != null) {
                    if (DocumentFieldType.STRING == vendorNameField.getType()) {
                        String merchantName = vendorNameField.getValueString();
                        System.out.printf("Vendor Name: %s, confidence: %.2f%n",
                            merchantName, vendorNameField.getConfidence());
                    }
                }

                DocumentField vendorAddressField = invoiceFields.get("VendorAddress");
                if (vendorAddressField != null) {
                    if (DocumentFieldType.STRING == vendorAddressField.getType()) {
                        String merchantAddress = vendorAddressField.getValueString();
                        System.out.printf("Vendor address: %s, confidence: %.2f%n",
                            merchantAddress, vendorAddressField.getConfidence());
                    }
                }

                DocumentField customerNameField = invoiceFields.get("CustomerName");
                if (customerNameField != null) {
                    if (DocumentFieldType.STRING == customerNameField.getType()) {
                        String merchantAddress = customerNameField.getValueString();
                        System.out.printf("Customer Name: %s, confidence: %.2f%n",
                            merchantAddress, customerNameField.getConfidence());
                    }
                }

                DocumentField customerAddressRecipientField = invoiceFields.get("CustomerAddressRecipient");
                if (customerAddressRecipientField != null) {
                    if (DocumentFieldType.STRING == customerAddressRecipientField.getType()) {
                        String customerAddr = customerAddressRecipientField.getValueString();
                        System.out.printf("Customer Address Recipient: %s, confidence: %.2f%n",
                            customerAddr, customerAddressRecipientField.getConfidence());
                    }
                }

                DocumentField invoiceIdField = invoiceFields.get("InvoiceId");
                if (invoiceIdField != null) {
                    if (DocumentFieldType.STRING == invoiceIdField.getType()) {
                        String invoiceId = invoiceIdField.getValueString();
                        System.out.printf("Invoice ID: %s, confidence: %.2f%n",
                            invoiceId, invoiceIdField.getConfidence());
                    }
                }

                DocumentField invoiceDateField = invoiceFields.get("InvoiceDate");
                if (customerNameField != null) {
                    if (DocumentFieldType.DATE == invoiceDateField.getType()) {
                        LocalDate invoiceDate = invoiceDateField.getValueDate();
                        System.out.printf("Invoice Date: %s, confidence: %.2f%n",
                            invoiceDate, invoiceDateField.getConfidence());
                    }
                }

                DocumentField invoiceTotalField = invoiceFields.get("InvoiceTotal");
                if (customerAddressRecipientField != null) {
                    if (DocumentFieldType.FLOAT == invoiceTotalField.getType()) {
                        Float invoiceTotal = invoiceTotalField.getValueFloat();
                        System.out.printf("Invoice Total: %.2f, confidence: %.2f%n",
                            invoiceTotal, invoiceTotalField.getConfidence());
                    }
                }

                DocumentField invoiceItemsField = invoiceFields.get("Items");
                if (invoiceItemsField != null) {
                    System.out.printf("Invoice Items: %n");
                    if (DocumentFieldType.LIST == invoiceItemsField.getType()) {
                        List < DocumentField > invoiceItems = invoiceItemsField.getValueList();
                        invoiceItems.stream()
                            .filter(invoiceItem - > DocumentFieldType.MAP == invoiceItem.getType())
                            .map(formField - > formField.getValueMap())
                            .forEach(formFieldMap - > formFieldMap.forEach((key, formField) - > {
                                // See a full list of fields found on an invoice here:
                                // https://aka.ms/formrecognizer/invoicefields
                                if ("Description".equals(key)) {
                                    if (DocumentFieldType.STRING == formField.getType()) {
                                        String name = formField.getValueString();
                                        System.out.printf("Description: %s, confidence: %.2fs%n",
                                            name, formField.getConfidence());
                                    }
                                }
                                if ("Quantity".equals(key)) {
                                    if (DocumentFieldType.FLOAT == formField.getType()) {
                                        Float quantity = formField.getValueFloat();
                                        System.out.printf("Quantity: %f, confidence: %.2f%n",
                                            quantity, formField.getConfidence());
                                    }
                                }
                                if ("UnitPrice".equals(key)) {
                                    if (DocumentFieldType.FLOAT == formField.getType()) {
                                        Float unitPrice = formField.getValueFloat();
                                        System.out.printf("Unit Price: %f, confidence: %.2f%n",
                                            unitPrice, formField.getConfidence());
                                    }
                                }
                                if ("ProductCode".equals(key)) {
                                    if (DocumentFieldType.FLOAT == formField.getType()) {
                                        Float productCode = formField.getValueFloat();
                                        System.out.printf("Product Code: %f, confidence: %.2f%n",
                                            productCode, formField.getConfidence());
                                    }
                                }
                            }));
                    }
                }
            }
        });
    }
}

```

## <a name="build-and-run-your-application"></a>générer et exécuter votre application

Revenez à votre répertoire de projet principal : **form-recognizer-app**.

1. Générez votre application avec la commande `build` :

```console
gradle build
```

1. Exécutez votre application avec la commande `run` :

```console
gradle run
```

Félicitations ! Dans ce guide de démarrage rapide, vous avez utilisé Form Recognizer avec le SDK Java pour analyser divers formulaires et documents de différentes manières. Explorez à présent la documentation de référence pour découvrir plus en détail l’API Form Recognizer.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Documentation de référence de l’API REST v3.0](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument)

> [!div class="nextstepaction"]
> [Informations de référence de la bibliothèque Java pour Form Recognizer](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-ai-formrecognizer/4.0.0-beta.1/index.html)
