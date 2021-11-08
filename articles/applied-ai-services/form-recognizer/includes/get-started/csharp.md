---
title: 'Démarrage : Utiliser la bibliothèque de client Form Recognizer pour .NET v2.1'
description: Utilisez le kit SDK Form Recognizer pour .NET afin de créer une application de traitement des formulaires qui extrait des paires clé/valeur et des données de tableau de vos documents personnalisés.
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 11/02/2021
ms.author: lajanuar
recommendations: false
ms.custom: ignite-fall-2021
ms.openlocfilehash: bface4c7df450e3123f8e4be73e5c942ff95a8cc
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131030224"
---
<!-- markdownlint-disable MD024 -->

<!-- markdownlint-disable MD033 -->
> [!IMPORTANT]
>
> Ce guide de démarrage rapide cible la version **2.1** de l’API REST Azure Form Recognizer et utilise cURL pour exécuter des appels d’API REST.
>
>* Par souci de simplicité, le code indiqué dans cet article utilise des méthodes synchrones et un stockage non sécurisé des informations d’identification.

[Documentation de référence](/dotnet/api/overview/azure/ai.formrecognizer-readme) | [Code source de la bibliothèque](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/src) | [Package (NuGet)](https://www.nuget.org/packages/Azure.AI.FormRecognizer) | [Exemples](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/README.md)

Dans ce démarrage rapide, vous allez utiliser les API suivantes pour extraire des données structurées de formulaires et de documents :

* [Disposition](#try-it-layout-model)

* [Facture](#try-it-prebuilt-model)

## <a name="prerequisites"></a>Prérequis

* Abonnement Azure : [créez-en un gratuitement](https://azure.microsoft.com/free/cognitive-services/).

* La version actuelle de l’[IDE Visual Studio](https://visualstudio.microsoft.com/vs/). <!-- or [.NET Core](https://dotnet.microsoft.com/download). -->

* Une ressource Cognitive Services ou Form Recognizer. Une fois que vous avez votre abonnement Azure, créez une ressource Form Recognizer [monoservice](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) ou [multiservice](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne) dans le portail Azure pour obtenir votre clé et votre point de terminaison. Vous pouvez utiliser le niveau tarifaire Gratuit (`F0`) pour tester le service, puis passer par la suite à un niveau payant pour la production.

    > [!TIP]
    > Créez une ressource Cognitive Services si vous envisagez d’accéder à plusieurs services Cognitive Services sous un seul point de terminaison/clé. Pour l’accès à Form Recognizer uniquement, créez une ressource Form Recognizer. Notez que vous avez besoin d’une ressource de service unique si vous avez l’intention d’utiliser l’[authentification Azure Active Directory](/azure/active-directory/authentication/overview-authentication).

* Une fois que votre ressource est déployée, cliquez sur **Accéder à la ressource**. Vous avez besoin de la clé et du point de terminaison de la ressource que vous créez pour connecter votre application à l’API Form Recognizer. Vous collerez votre clé et votre point de terminaison dans le code ci-dessous plus loin dans ce guide de démarrage rapide :

  :::image type="content" source="../../media/containers/keys-and-endpoint.png" alt-text="Capture d’écran : clés et emplacement du point de terminaison dans le portail Azure":::

## <a name="set-up"></a>Configurer

1. Démarrez Visual Studio 2019.

1. Dans la page de démarrage, choisissez Créer un projet.

    :::image type="content" source="../../media/quickstarts/start-window.png" alt-text="Capture d’écran : fenêtre de démarrage Visual Studio":::

1. Dans la page **Créer un nouveau projet**, entrez **console** dans la zone de recherche. Choisissez le modèle **Application console**, puis choisissez **Suivant**.

    :::image type="content" source="../../media/quickstarts/create-new-project.png" alt-text="Capture d’écran : page Créer un nouveau projet dans Visual Studio":::

1. Dans la fenêtre de dialogue **Configurer votre nouveau projet**, entrez `formRecognizer_quickstart` dans la zone Nom de projet. Ensuite, choisissez Suivant.

    :::image type="content" source="../../media/quickstarts/configure-new-project.png" alt-text="Capture d’écran : fenêtre de dialogue Configurer un nouveau projet dans Visual Studio":::

1. Dans la fenêtre de dialogue **Informations supplémentaires**, sélectionnez **.NET 5.0 (Actuel)** , puis sélectionnez **Créer**.

    :::image type="content" source="../../media/quickstarts/additional-information.png" alt-text="Capture d’écran : fenêtre de dialogue Informations supplémentaires dans Visual Studio":::

### <a name="install-the-client-library-with-nuget"></a>Installer la bibliothèque de client avec NuGet

 1. Cliquez avec le bouton droit sur votre projet **formRecognizer_quickstart** et sélectionnez **Gérer les packages NuGet**.

    :::image type="content" source="../../media/quickstarts/select-nuget-package.png" alt-text="Capture d’écran : select-nuget-package.png":::

 1. Sélectionnez l’onglet Parcourir et tapez Azure.AI.FormRecognizer.

     :::image type="content" source="../../media/quickstarts/azure-nuget-package.png" alt-text="Capture d’écran : select-form-recognizer-package.png":::

 1. Sélectionnez la version **3.1.1** dans le menu déroulant et sélectionnez **Installer**.

## <a name="build-your-application"></a>Générer votre application

Pour interagir avec le service Form Recognizer, vous devez créer une instance de la classe `FormRecognizerClient`. Pour ce faire, vous allez créer un `AzureKeyCredential` avec votre apiKey et une instance `FormRecognizerClient` avec le `AzureKeyCredential` et votre `endpoint` de Form Recognizer.

1. Ouvrez le fichier **Program.cs**.

1. Insérez les directives using suivantes :

```csharp
using System;
using Azure;
using Azure.AI.FormRecognizer;
using Azure.AI.FormRecognizer.Models;
using System.Threading.Tasks;
```

1. Définissez vos variables d’environnement `endpoint` et `apiKey`, et créez vos instances `AzureKeyCredential` et `FormRecognizerClient` :

```csharp
private static readonly string endpoint = "your-form-recognizer-endpoint";
private static readonly string apiKey = "your-api-key";
private static readonly AzureKeyCredential credential = new AzureKeyCredential(apiKey);
```

1. Supprimez la ligne `Console.Writeline("Hello World!");` et ajoutez les exemples de code **Essayez** dans la méthode **Main** du fichier **Program.cs** :

    :::image type="content" source="../../media/quickstarts/add-code-here.png" alt-text="Capture d’écran : ajout de l’exemple de code à la méthode Main":::

1. Sélectionnez un exemple de code à copier-coller dans la méthode Main de votre application :

    * [**Layout**](#try-it-layout-model)

    * [**Facture prédéfinie**](#try-it-prebuilt-model)

> [!IMPORTANT]
>
> N’oubliez pas de supprimer la clé de votre code une fois que vous avez terminé, et ne la postez jamais publiquement. En production, utilisez des méthodes sécurisées pour stocker vos informations d’identification et y accéder. Pour plus d’informations, consultez notre article Cognitive Services [security](.(/azure/cognitive-services/cognitive-services-security.md).

## <a name="try-it-layout-model"></a>**Essayez** : modèle de disposition

Extrayez le texte, les marques de sélection, les styles de texte et les structures de tableaux, ainsi que les coordonnées de leurs cadres de limitation, à partir de documents.

> [!div class="checklist"]
>
> * Pour cet exemple, vous aurez besoin d’un **fichier de formulaire au niveau d’un URI**. Vous pouvez utiliser notre [exemple de formulaire](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-layout.pdf) pour ce guide de démarrage rapide.
> * Nous avons ajouté la valeur de l’URI du fichier à la variable `formUri`.
> * Pour extraire la disposition d’un fichier donné au niveau d’un URI, utilisez la méthode `StartRecognizeContentFromUriAsync`.

### <a name="add-the-following-code-to-your-layout-application-main-method"></a>Ajouter le code suivant à la méthode **Principale** de votre application de disposition :

```csharp

FormRecognizerClient recognizerClient = AuthenticateClient();

Task recognizeContent = RecognizeContent(recognizerClient);
Task.WaitAll(recognizeContent);
```

### <a name="add-the-following-code-below-the-main-method"></a>Ajoutez le code suivant à la méthode **Principale** :

```csharp

private static FormRecognizerClient AuthenticateClient()
            {
                var credential = new AzureKeyCredential(apiKey);
                var client = new FormRecognizerClient(new Uri(endpoint), credential);
                return client;
            }

            private static async Task RecognizeContent(FormRecognizerClient recognizerClient)
        {
            string formUrl = "https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-layout.pdf";
            FormPageCollection formPages = await recognizerClient
        .StartRecognizeContentFromUri(new Uri(formUrl))
        .WaitForCompletionAsync();

            foreach (FormPage page in formPages)
            {
                Console.WriteLine($"Form Page {page.PageNumber} has {page.Lines.Count} lines.");

                for (int i = 0; i < page.Lines.Count; i++)
                {
                    FormLine line = page.Lines[i];
                    Console.WriteLine($"    Line {i} has {line.Words.Count} word{(line.Words.Count > 1 ? "s" : "")}, and text: '{line.Text}'.");
                }

                for (int i = 0; i < page.Tables.Count; i++)
                {
                    FormTable table = page.Tables[i];
                    Console.WriteLine($"Table {i} has {table.RowCount} rows and {table.ColumnCount} columns.");
                    foreach (FormTableCell cell in table.Cells)
                    {
                        Console.WriteLine($"    Cell ({cell.RowIndex}, {cell.ColumnIndex}) contains text: '{cell.Text}'.");
                    }
                }
            }
        }
    }
}

```

## <a name="try-it-prebuilt-model"></a>**Essayez** : Modèle prédéfini

Cet exemple montre comment analyser les données de certains types de documents courants avec des modèles préentraînés, à l’aide d’une facture à titre d’exemple.

> [!div class="checklist"]
>
> * Pour cet exemple, nous analysons un document de facture à l’aide d’un modèle prédéfini. Vous pouvez utiliser notre [exemple de facture](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-invoice.pdf) pour ce guide de démarrage rapide.
> * Nous avons ajouté la valeur de l’URI du fichier à la variable `invoiceUri` en haut de la méthode Main.
> * Pour analyser un fichier donné au niveau d’un URI, utilisez la méthode `StartRecognizeInvoicesFromUriAsync`.
> * Par souci de simplicité, tous les champs d’entité retournés par le service ne sont pas indiqués ici. Pour afficher la liste de tous les champs pris en charge et les types correspondants, consultez notre page conceptuelle [Facture](../../concept-invoice.md#field-extraction).

### <a name="choose-a-prebuilt-model"></a>Choisir un modèle prédéfini

Vous n’êtes pas limité aux factures – Vous avez le choix entre plusieurs modèles prédéfinis, chacun ayant son propre ensemble de champs pris en charge. Le modèle à utiliser pour l’opération d’analyse dépend du type de document à analyser. Voici les modèles prédéfinis actuellement pris en charge par le service Form Recognizer :

* [**Facture**](../../concept-invoice.md) : extrait le texte, les marques de sélection, les tableaux, les champs et les informations clés des factures.
* [**Reçu**](../../concept-receipt.md) : extrait le texte et les informations clés des reçus.
* [**Document d’identité**](../../concept-id-document.md) : extrait le texte et les informations clés des permis de conduire et des passeports internationaux.
* [**Carte de visite**](../../concept-business-card.md) : extrait le texte et les informations clés des cartes de visite.

### <a name="add-the-following-code-to-your-prebuilt-invoice-application-main-method"></a>Ajouter le code suivant à la méthode **Main** de votre application de facture prédéfinie

```csharp
FormRecognizerClient recognizerClient = AuthenticateClient();

            Task analyzeinvoice = AnalyzeInvoice(recognizerClient, invoiceUrl);
            Task.WaitAll(analyzeinvoice);
```

### <a name="add-the-following-code-below-the-main-method"></a>Ajoutez le code suivant à la méthode **Principale** :

```csharp
   private static FormRecognizerClient AuthenticateClient() {
     var credential = new AzureKeyCredential(apiKey);
     var client = new FormRecognizerClient(new Uri(endpoint), credential);
     return client;
   }

   static string invoiceUrl = "https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-invoice.pdf";

   private static async Task AnalyzeInvoice(FormRecognizerClient recognizerClient, string invoiceUrl) {
     var options = new RecognizeInvoicesOptions() {
       Locale = "en-US"
     };
     RecognizedFormCollection invoices = await recognizerClient.StartRecognizeInvoicesFromUriAsync(new Uri(invoiceUrl), options).WaitForCompletionAsync();

     RecognizedForm invoice = invoices[0];

     FormField invoiceIdField;
     if (invoice.Fields.TryGetValue("InvoiceId", out invoiceIdField)) {
       if (invoiceIdField.Value.ValueType == FieldValueType.String) {
         string invoiceId = invoiceIdField.Value.AsString();
         Console.WriteLine($ "    Invoice Id: '{invoiceId}', with confidence {invoiceIdField.Confidence}");
       }
     }

     FormField invoiceDateField;
     if (invoice.Fields.TryGetValue("InvoiceDate", out invoiceDateField)) {
       if (invoiceDateField.Value.ValueType == FieldValueType.Date) {
         DateTime invoiceDate = invoiceDateField.Value.AsDate();
         Console.WriteLine($ "    Invoice Date: '{invoiceDate}', with confidence {invoiceDateField.Confidence}");
       }
     }

     FormField dueDateField;
     if (invoice.Fields.TryGetValue("DueDate", out dueDateField)) {
       if (dueDateField.Value.ValueType == FieldValueType.Date) {
         DateTime dueDate = dueDateField.Value.AsDate();
         Console.WriteLine($ "    Due Date: '{dueDate}', with confidence {dueDateField.Confidence}");
       }
     }

     FormField vendorNameField;
     if (invoice.Fields.TryGetValue("VendorName", out vendorNameField)) {
       if (vendorNameField.Value.ValueType == FieldValueType.String) {
         string vendorName = vendorNameField.Value.AsString();
         Console.WriteLine($ "    Vendor Name: '{vendorName}', with confidence {vendorNameField.Confidence}");
       }
     }

     FormField vendorAddressField;
     if (invoice.Fields.TryGetValue("VendorAddress", out vendorAddressField)) {
       if (vendorAddressField.Value.ValueType == FieldValueType.String) {
         string vendorAddress = vendorAddressField.Value.AsString();
         Console.WriteLine($ "    Vendor Address: '{vendorAddress}', with confidence {vendorAddressField.Confidence}");
       }
     }

     FormField customerNameField;
     if (invoice.Fields.TryGetValue("CustomerName", out customerNameField)) {
       if (customerNameField.Value.ValueType == FieldValueType.String) {
         string customerName = customerNameField.Value.AsString();
         Console.WriteLine($ "    Customer Name: '{customerName}', with confidence {customerNameField.Confidence}");
       }
     }

     FormField customerAddressField;
     if (invoice.Fields.TryGetValue("CustomerAddress", out customerAddressField)) {
       if (customerAddressField.Value.ValueType == FieldValueType.String) {
         string customerAddress = customerAddressField.Value.AsString();
         Console.WriteLine($ "    Customer Address: '{customerAddress}', with confidence {customerAddressField.Confidence}");
       }
     }

     FormField customerAddressRecipientField;
     if (invoice.Fields.TryGetValue("CustomerAddressRecipient", out customerAddressRecipientField)) {
       if (customerAddressRecipientField.Value.ValueType == FieldValueType.String) {
         string customerAddressRecipient = customerAddressRecipientField.Value.AsString();
         Console.WriteLine($ "    Customer address recipient: '{customerAddressRecipient}', with confidence {customerAddressRecipientField.Confidence}");
       }
     }

     FormField invoiceTotalField;
     if (invoice.Fields.TryGetValue("InvoiceTotal", out invoiceTotalField)) {
       if (invoiceTotalField.Value.ValueType == FieldValueType.Float) {
         float invoiceTotal = invoiceTotalField.Value.AsFloat();
         Console.WriteLine($ "    Invoice Total: '{invoiceTotal}', with confidence {invoiceTotalField.Confidence}");
       }
     }
   }
 }
}
```

## <a name="run-your-application"></a>Exécuter votre application

Choisissez le bouton vert **Démarrer** en regard de formRecognizer_quickstart pour générer et exécuter votre programme, ou appuyez sur **F5**.

  :::image type="content" source="../../media/quickstarts/run-visual-studio.png" alt-text="Capture d’écran : exécutez votre programme Visual Studio":::

<!-- --- -->

Félicitations ! Dans ce guide de démarrage rapide, vous avez utilisé Form Recognizer avec le kit SDK C# pour analyser divers formulaires et documents de différentes manières. Explorez à présent la documentation de référence pour découvrir plus en détail l’API Form Recognizer.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Documentation de référence de l’API REST v2.1](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/5ed8c9843c2794cbb1a96291)

> [!div class="nextstepaction"]
> [Bibliothèque de référence C#/.NET Form Recognizer](/dotnet/api/overview/azure/AI.FormRecognizer-readme)
