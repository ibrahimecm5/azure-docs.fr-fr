---
title: 'Démarrage : Utiliser la bibliothèque de client Form Recognizer pour Java v2.1'
description: Utilisez le kit SDK Form Recognizer pour Java afin de créer une application de traitement des formulaires qui extrait des paires clé/valeur et des données de tableau de vos documents personnalisés.
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 11/02/2021
ms.author: lajanuar
recommendations: false
ms.custom: ignite-fall-2021
ms.openlocfilehash: 4e93e5b157cc7bb17eb2e66a97770f4af0d915c2
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131030227"
---
> [!IMPORTANT]
>
> Ce guide de démarrage rapide cible Azure Form Recognizer version **2.1**.

[Documentation de référence](/java/api/overview/azure/ai-formrecognizer-readme) | [Code source de la bibliothèque](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src) | [Package (Maven)](https://mvnrepository.com/artifact/com.azure/azure-ai-formrecognizer) | [Exemples](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src/samples/README.md)

Dans ce démarrage rapide, vous allez utiliser les API suivantes pour extraire des données structurées de formulaires et de documents :

* [Disposition](#try-it-layout-model)

* [Facture](#try-it-prebuilt-model)

## <a name="prerequisites"></a>Prérequis

* Abonnement Azure : [créez-en un gratuitement](https://azure.microsoft.com/free/cognitive-services/).
* Un [Kit de développement Java (JDK)](/java/azure/jdk/?view=azure-java-stable&preserve-view=true
), version 8 ou ultérieure
* Une ressource Cognitive Services ou Form Recognizer. Une fois que vous avez votre abonnement Azure, créez une ressource Form Recognizer [monoservice](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) ou [multiservice](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne) dans le portail Azure pour obtenir votre clé et votre point de terminaison. Vous pouvez utiliser le niveau tarifaire Gratuit (`F0`) pour tester le service, puis passer par la suite à un niveau payant pour la production.

> [!TIP]
> Créez une ressource Cognitive Services si vous envisagez d’accéder à plusieurs services Cognitive Services sous un seul point de terminaison/clé. Pour l’accès à Form Recognizer uniquement, créez une ressource Form Recognizer. Notez que vous avez besoin d’une ressource monoservice si vous avez l’intention d’utiliser l’[authentification Azure Active Directory](/azure/active-directory/authentication/overview-authentication).

* Une fois que votre ressource est déployée, cliquez sur **Accéder à la ressource**. Vous avez besoin de la clé et du point de terminaison de la ressource que vous créez pour connecter votre application à l’API Form Recognizer. Vous collerez votre clé et votre point de terminaison dans le code ci-dessous plus loin dans ce guide de démarrage rapide :

  :::image type="content" source="../../media/containers/keys-and-endpoint.png" alt-text="Capture d’écran : clés et emplacement du point de terminaison dans le portail Azure":::

## <a name="set-up"></a>Configurer

### <a name="create-a-new-gradle-project"></a>Créer un projet Gradle

Dans une fenêtre de console (par exemple, cmd, PowerShell ou Bash), créez le répertoire **form-recognizer-app** pour votre application et accédez-y.

```console
mkdir form-recognizer-app && form-recognizer-app
```

1. Exécutez la commande `gradle init` à partir de votre répertoire de travail. Cette commande crée des fichiers de build essentiels pour Gradle, notamment *build.gradle.kts*, qui est utilisé au moment de l’exécution pour créer et configurer votre application.

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
    implementation(group = "com.azure", name = "azure-ai-formrecognizer", version = "3.1.1")
}
```

### <a name="create-a-java-file"></a>Créer un fichier Java

Entrez la commande suivante à partir de votre répertoire de travail :

```console
mkdir -p src/main/java
```

Vous créez la structure de répertoires suivante :

:::image type="content" source="../../media/quickstarts/java-directories.png" alt-text="Capture d’écran : structure de répertoires Java":::

Accédez au nouveau dossier et créez un fichier sous le nom *FormRecognizer.java*.  Ouvrez-le dans votre éditeur ou IDE par défaut et ajoutez la déclaration de package et les instructions `import` suivantes :

```java
import com.azure.ai.formrecognizer.*;
import com.azure.ai.formrecognizer.models.*;

import java.util.concurrent.atomic.AtomicReference;
import java.util.List;
import java.util.Map;
import java.time.LocalDate;

import com.azure.core.credential.AzureKeyCredential;
import com.azure.core.http.rest.PagedIterable;
import com.azure.core.util.Context;
import com.azure.core.util.polling.SyncPoller;
```

### <a name="select-a-code-sample-to-copy-and-paste-into-your-applications-main-method"></a>Sélectionnez un exemple de code à copier-coller dans la méthode main de votre application :

* [**Layout**](#try-it-layout-model)

* [**Facture prédéfinie**](#try-it-prebuilt-model)

> [!IMPORTANT]
>
> N’oubliez pas de supprimer la clé de votre code une fois que vous avez terminé, et ne la postez jamais publiquement. En production, utilisez des méthodes sécurisées pour stocker vos informations d’identification et y accéder. Pour plus d’informations, consultez l’article sur la [sécurité](/azure/cognitive-services/cognitive-services-security.md) de Cognitive Services.

## <a name="try-it-layout-model"></a>**Essayez** : modèle de disposition

Extrayez le texte, les marques de sélection, les styles de texte et les structures de tableaux, ainsi que les coordonnées de leurs cadres de limitation, à partir de documents.

> [!div class="checklist"]
>
> * Pour cet exemple, vous aurez besoin d’un **fichier de formulaire au niveau d’un URI**. Vous pouvez utiliser notre [exemple de formulaire](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-layout.pdf) pour ce guide de démarrage rapide.
> * Pour analyser un fichier donné au niveau d’un URI, vous utiliserez la méthode `beginRecognizeContentFromUrl`.
> * Nous avons ajouté la valeur de l’URI du fichier à la variable `formUrl` en haut de la méthode principale.

Mettez à jour la classe **FormRecognizer** de votre application avec le code suivant (veillez à mettre à jour les variables de point de terminaison et de clé avec les valeurs de votre instance Form Recognizer dans le portail Azure) :

```java

static final String key = "PASTE_YOUR_FORM_RECOGNIZER_SUBSCRIPTION_KEY_HERE";
static final String endpoint = "PASTE_YOUR_FORM_RECOGNIZER_ENDPOINT_HERE";

public static void main(String[] args) {FormRecognizerClient recognizerClient = new FormRecognizerClientBuilder()
                .credential(new AzureKeyCredential(key)).endpoint(endpoint).buildClient();

    String formUrl = "https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-layout.pdf";

    System.out.println("Get form content...");
        GetContent(recognizerClient, formUrl);
  }
    private static void GetContent(FormRecognizerClient recognizerClient, String invoiceUri) {
        String analyzeFilePath = invoiceUri;
        SyncPoller<FormRecognizerOperationResult, List<FormPage>> recognizeContentPoller = recognizerClient
                .beginRecognizeContentFromUrl(analyzeFilePath);

        List<FormPage> contentResult = recognizeContentPoller.getFinalResult();
        // </snippet_getcontent_call>
        // <snippet_getcontent_print>
        contentResult.forEach(formPage -> {
            // Table information
            System.out.println("----Recognizing content ----");
            System.out.printf("Has width: %f and height: %f, measured with unit: %s.%n", formPage.getWidth(),
                    formPage.getHeight(), formPage.getUnit());
            formPage.getTables().forEach(formTable -> {
                System.out.printf("Table has %d rows and %d columns.%n", formTable.getRowCount(),
                        formTable.getColumnCount());
                formTable.getCells().forEach(formTableCell -> {
                    System.out.printf("Cell has text %s.%n", formTableCell.getText());
                });
                System.out.println();
            });
        });
    }

```

## <a name="try-it-prebuilt-model"></a>**Essayez** : Modèle prédéfini

Cet exemple montre comment analyser les données de certains types de documents courants avec des modèles préentraînés, à l’aide d’une facture à titre d’exemple.

> [!div class="checklist"]
>
> * Pour cet exemple, nous analysons un document de facture à l’aide d’un modèle prédéfini. Vous pouvez utiliser notre [exemple de facture](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-invoice.pdf) pour ce guide de démarrage rapide.
> * Pour analyser un fichier donné au niveau d’un URI, vous utiliserez la `beginRecognizeInvoicesFromUrl`.
> * Nous avons ajouté la valeur de l’URI du fichier à la variable `invoiceUrl` en haut de la méthode principale.
> * Par souci de simplicité, tous les champs d’entité retournés par le service ne sont pas indiqués ici. Pour afficher la liste de tous les champs pris en charge et les types correspondants, consultez notre page conceptuelle [Facture](../../concept-invoice.md#field-extraction).

### <a name="choose-a-prebuilt-model"></a>Choisir un modèle prédéfini

Vous n’êtes pas limité aux factures – Vous avez le choix entre plusieurs modèles prédéfinis, chacun ayant son propre ensemble de champs pris en charge. Le modèle à utiliser pour l’opération d’analyse dépend du type de document à analyser. Voici les modèles prédéfinis actuellement pris en charge par le service Form Recognizer :

* [**Facture**](../../concept-invoice.md) : extrait le texte, les marques de sélection, les tableaux, les champs et les informations clés des factures.
* [**Reçu**](../../concept-receipt.md) : extrait le texte et les informations clés des reçus.
* [**Document d’identité**](../../concept-id-document.md) : extrait le texte et les informations clés des permis de conduire et des passeports internationaux.
* [**Carte de visite**](../../concept-business-card.md) : extrait le texte et les informations clés des cartes de visite.

Mettez à jour la classe **FormRecognizer** de votre application avec le code suivant (veillez à mettre à jour les variables de point de terminaison et de clé avec les valeurs de votre instance Form Recognizer dans le portail Azure) :

```java

static final String key = "PASTE_YOUR_FORM_RECOGNIZER_SUBSCRIPTION_KEY_HERE";
static final String endpoint = "PASTE_YOUR_FORM_RECOGNIZER_ENDPOINT_HERE";

public static void main(String[] args) {
    FormRecognizerClient recognizerClient = new FormRecognizerClientBuilder().credential(new AzureKeyCredential(key)).endpoint(endpoint).buildClient();

    String invoiceUrl = "https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-invoice.pdf";

    System.out.println("Analyze invoice...");
        AnalyzeInvoice(recognizerClient, invoiceUrl);
  }
    private static void AnalyzeInvoice(FormRecognizerClient recognizerClient, String invoiceUrl) {
      SyncPoller < FormRecognizerOperationResult,
        List < RecognizedForm >> recognizeInvoicesPoller = recognizerClient.beginRecognizeInvoicesFromUrl(invoiceUrl);
      List < RecognizedForm > recognizedInvoices = recognizeInvoicesPoller.getFinalResult();

      for (int i = 0; i < recognizedInvoices.size(); i++) {
        RecognizedForm recognizedInvoice = recognizedInvoices.get(i);
        Map < String,
        FormField > recognizedFields = recognizedInvoice.getFields();
        System.out.printf("----------- Recognized invoice info for page %d -----------%n", i);
        FormField vendorNameField = recognizedFields.get("VendorName");
        if (vendorNameField != null) {
            if (FieldValueType.STRING == vendorNameField.getValue().getValueType()) {
                String merchantName = vendorNameField.getValue().asString();
                System.out.printf("Vendor Name: %s, confidence: %.2f%n", merchantName, vendorNameField.getConfidence());
            }
        }

        FormField vendorAddressField = recognizedFields.get("VendorAddress");
        if (vendorAddressField != null) {
            if (FieldValueType.STRING == vendorAddressField.getValue().getValueType()) {
                String merchantAddress = vendorAddressField.getValue().asString();
                System.out.printf("Vendor address: %s, confidence: %.2f%n", merchantAddress, vendorAddressField.getConfidence());
            }
        }

        FormField customerNameField = recognizedFields.get("CustomerName");
        if (customerNameField != null) {
            if (FieldValueType.STRING == customerNameField.getValue().getValueType()) {
                String merchantAddress = customerNameField.getValue().asString();
                System.out.printf("Customer Name: %s, confidence: %.2f%n", merchantAddress, customerNameField.getConfidence());
            }
        }

        FormField customerAddressRecipientField = recognizedFields.get("CustomerAddressRecipient");
        if (customerAddressRecipientField != null) {
            if (FieldValueType.STRING == customerAddressRecipientField.getValue().getValueType()) {
                String customerAddr = customerAddressRecipientField.getValue().asString();
                System.out.printf("Customer Address Recipient: %s, confidence: %.2f%n", customerAddr, customerAddressRecipientField.getConfidence());
            }
        }

        FormField invoiceIdField = recognizedFields.get("InvoiceId");
        if (invoiceIdField != null) {
            if (FieldValueType.STRING == invoiceIdField.getValue().getValueType()) {
                String invoiceId = invoiceIdField.getValue().asString();
                System.out.printf("Invoice Id: %s, confidence: %.2f%n", invoiceId, invoiceIdField.getConfidence());
            }
        }

        FormField invoiceDateField = recognizedFields.get("InvoiceDate");
        if (customerNameField != null) {
            if (FieldValueType.DATE == invoiceDateField.getValue().getValueType()) {
                LocalDate invoiceDate = invoiceDateField.getValue().asDate();
                System.out.printf("Invoice Date: %s, confidence: %.2f%n", invoiceDate, invoiceDateField.getConfidence());
            }
        }

        FormField invoiceTotalField = recognizedFields.get("InvoiceTotal");
        if (customerAddressRecipientField != null) {
            if (FieldValueType.FLOAT == invoiceTotalField.getValue().getValueType()) {
                Float invoiceTotal = invoiceTotalField.getValue().asFloat();
                System.out.printf("Invoice Total: %.2f, confidence: %.2f%n", invoiceTotal, invoiceTotalField.getConfidence());
            }
        }
    }
}

```

## <a name="build-and-run-your-application"></a>générer et exécuter votre application

Revenez à votre répertoire de projet principal : **form-recognizer-app**.

1. Générez votre application avec la commande `build` :

```console
gradle build
```

1. Exécutez votre application avec la commande `run` :

```console
gradle run
```

Félicitations ! Dans ce guide de démarrage rapide, vous avez utilisé Form Recognizer avec le SDK Java pour analyser divers formulaires et documents de différentes manières. Explorez à présent la documentation de référence pour découvrir plus en détail l’API Form Recognizer.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Documentation de référence de l’API REST v2.1](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/5ed8c9843c2794cbb1a96291)

> [!div class="nextstepaction"]
> [Informations de référence de la bibliothèque Java pour Form Recognizer](/java/api/overview/azure/ai-formrecognizer-readme?view=azure-java-stable&preserve-view=true)
