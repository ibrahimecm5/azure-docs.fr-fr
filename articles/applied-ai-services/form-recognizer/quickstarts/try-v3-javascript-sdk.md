---
title: 'Démarrage rapide : Form Recognizer v3.0 avec SDK JavaScript | Préversion'
titleSuffix: Azure Applied AI Services
description: Traitement, extraction des données et analyse des formulaires et des documents à l’aide de Form Recognizer v3.0 avec les SDK de la bibliothèque de client JavaScript (préversion)
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 11/02/2021
ms.author: lajanuar
recommendations: false
ms.custom: ignite-fall-2021
ms.openlocfilehash: 364eea405653ac54032787a14a612fbc57de03db
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131030200"
---
# <a name="quickstart-form-recognizer-javascript-client-library-sdks-v30--preview"></a>Démarrage rapide : Form Recognizer v3.0 avec les kits SDK de la bibliothèque de client JavaScript | Préversion

>[!NOTE]
> Form Recognizer v3.0 est actuellement en préversion publique. Certaines fonctionnalités peuvent ne pas être prises en charge ou avoir des capacités limitées.

[Documentation de référence](https://azuresdkdocs.blob.core.windows.net/$web/javascript/azure-ai-form-recognizer/4.0.0-beta.1/index.html) | [Code source de la bibliothèque](https://github.com/Azure/azure-sdk-for-js/tree/main/sdk/formrecognizer/ai-form-recognizer/src) | [Package (NuGet)](https://www.nuget.org/packages/Azure.AI.FormRecognizer) | [Exemples](https://github.com/Azure/azure-sdk-for-js/blob/%40azure/ai-form-recognizer_4.0.0-beta.1/sdk/formrecognizer/ai-form-recognizer/README.md)

Bien démarrer avec Azure Form Recognizer en utilisant le langage de programmation JavaScript. Azure Form Recognizer est un service Azure Applied AI Service qui utilise le machine learning pour extraire et analyser les champs de formulaire, le texte et les tableaux de vos documents. Vous pouvez facilement appeler des modèles Form Recognizer en intégrant les kits SDK de notre bibliothèque de client dans vos workflows et applications. Nous vous recommandons d’utiliser le service gratuit pendant que vous apprenez la technologie. N’oubliez pas que le nombre de pages gratuites est limité à 500 par mois.

Pour en savoir plus sur les fonctionnalités Form Recognizer et les options de développement, visitez notre page de [présentation](../overview.md#form-recognizer-features-and-development-options).

Dans ce guide de démarrage rapide, vous allez utiliser les fonctionnalités suivantes pour analyser et extraire les données et les valeurs de formulaires et de documents :

* [🆕 **Document général**](#try-it-general-document-model) – Analysez et extrayez le texte, les tableaux, la structure, les paires clé-valeur et les entités nommées.|

* [**Disposition**](#try-it-layout-model) – Analysez et extrayez les tableaux, les lignes, les mots et les marques de sélection telles que les cases d’option et les cases à cocher dans des formulaires, sans avoir besoin d’entraîner un modèle.

* [**Facture prédéfinie**](#try-it-prebuilt-model)Analysez et extrayez les champs courants des factures en utilisant un modèle de facture préentraîné.

## <a name="prerequisites"></a>Prérequis

* Abonnement Azure : [créez-en un gratuitement](https://azure.microsoft.com/free/cognitive-services/).

* Dernière version de [Visual Studio Code](https://code.visualstudio.com/) ou de votre IDE préféré. 

* Dernière version LTS de [Node.js](https://nodejs.org/about/releases/)

* Une ressource Cognitive Services ou Form Recognizer. Une fois que vous avez votre abonnement Azure, créez une ressource Form Recognizer [monoservice](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) ou [multiservice](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne) dans le portail Azure pour obtenir votre clé et votre point de terminaison. Vous pouvez utiliser le niveau tarifaire Gratuit (`F0`) pour tester le service, puis passer par la suite à un niveau payant pour la production.

    > [!TIP]
    > Créez une ressource Cognitive Services si vous envisagez d’accéder à plusieurs services Cognitive Services sous un seul point de terminaison/clé. Pour l’accès à Form Recognizer uniquement, créez une ressource Form Recognizer. Notez que vous avez besoin d’une ressource monoservice si vous avez l’intention d’utiliser l’[authentification Azure Active Directory](/azure/active-directory/authentication/overview-authentication).

* Après le déploiement de votre ressource, sélectionnez **Accéder à la ressource**. Vous avez besoin de la clé et du point de terminaison de la ressource que vous créez pour connecter votre application à l’API Form Recognizer. Vous collerez votre clé et votre point de terminaison dans le code ci-dessous plus loin dans ce guide de démarrage rapide :

  :::image type="content" source="../media/containers/keys-and-endpoint.png" alt-text="Capture d’écran : clés et emplacement du point de terminaison dans le portail Azure":::

## <a name="set-up"></a>Configurer

1. Créez une application Node.js. Dans une fenêtre de console (telle que cmd, PowerShell ou bash), créez un répertoire pour votre application et accédez-y.

    ```console
    mkdir form-recognizer-app && cd form-recognizer-app
    ```

1. Exécutez la commande `npm init` pour créer une application de nœud avec un fichier `package.json`.

    ```console
    npm init
    ```

1. Installez le package NPM de la bibliothèque cliente `ai-form-recognizer` :

    ```console
    npm install @azure/ai-form-recognizer@4.0.0-beta.1 @azure/identity
    ```

    * Le fichier `package.json` de votre application sera mis à jour avec les dépendances.

1. Créez un fichier nommé `index.js`, ouvrez-le et ajoutez les bibliothèques suivantes :

    ```javascript
   const { AzureKeyCredential, DocumentAnalysisClient } = require("@azure/ai-form-recognizer");
    ```

1. Créez des variables pour le point de terminaison et la clé Azure de votre ressource :

    ```javascript
    const apiKey = "PASTE_YOUR_FORM_RECOGNIZER_SUBSCRIPTION_KEY_HERE";
    const endpoint = "PASTE_YOUR_FORM_RECOGNIZER_ENDPOINT_HERE";
    ```

À ce stade, votre application JavaScript doit contenir les lignes de code suivantes :

```javascript
const { AzureKeyCredential, DocumentAnalysisClient } = require("@azure/ai-form-recognizer");

const endpoint = "PASTE_YOUR_FORM_RECOGNIZER_ENDPOINT_HERE";
const apiKey = "PASTE_YOUR_FORM_RECOGNIZER_SUBSCRIPTION_KEY_HERE";
```

### <a name="select-a-code-sample-to-copy-and-paste-into-your-application"></a>Sélectionnez un exemple de code à copier-coller dans votre application :

* [**Document général**](#try-it-general-document-model)

* [**Layout**](#try-it-layout-model)

* [**Facture prédéfinie**](#try-it-prebuilt-model)

> [!IMPORTANT]
>
> N’oubliez pas de supprimer la clé de votre code une fois que vous avez terminé, et ne la postez jamais publiquement. En production, utilisez des méthodes sécurisées pour stocker vos informations d’identification et y accéder. Pour plus d’informations, consultez notre article sur la [sécurité](../../../cognitive-services/cognitive-services-security.md) de Cognitive Services.

## <a name="try-it-general-document-model"></a>**Essayez** : modèle de document général

> [!div class="checklist"]
>
> * Pour cet exemple, vous aurez besoin d’un **fichier de formulaire au niveau d’un URI**. Vous pouvez utiliser notre [exemple de formulaire](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-layout.pdf) pour ce guide de démarrage rapide.
> * Pour analyser un fichier donné au niveau d’un URI, vous utiliserez la méthode `beginExtractGenericDocument`.
> * Nous avons ajouté la valeur de l’URI du fichier à la variable `formUrl` en haut du fichier.
> * Pour afficher la liste de tous les champs pris en charge et les types correspondants, consultez notre page conceptuelle [Document général](../concept-general-document.md#named-entity-recognition-ner-categories).

### <a name="add-the-following-code-to-your-general-document-application-on-the-line-below-the-apikey-variable"></a>Ajouter le code suivant à votre application de document général sur la ligne située sous la variable `apiKey`

```javascript

const formUrl = "https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-layout.pdf"

async function main() {
    const client = new DocumentAnalysisClient(endpoint, new DefaultAzureCredential(apiKey));

    const poller = await client.beginExtractGenericDocument(formUrl);

    const {
        keyValuePairs,
        entities
    } = await poller.pollUntilDone();

    if (keyValuePairs.length <= 0) {
        console.log("No key-value pairs were extracted from the document.");
    } else {
        console.log("Key-Value Pairs:");
        for (const {
                key,
                value,
                confidence
            } of keyValuePairs) {
            console.log("- Key  :", `"${key.content}"`);
            console.log("  Value:", `"${value?.content ?? "<undefined>"}" (${confidence})`);
        }
    }

    if (entities.length <= 0) {
        console.log("No entities were extracted from the document.");
    } else {
        console.log("Entities:");
        for (const entity of entities) {
            console.log(
                `- "${entity.content}" ${entity.category} - ${entity.subCategory ?? "<none>"} (${
          entity.confidence
        })`
            );
        }
    }
}

main().catch((error) => {
    console.error("An error occurred:", error);
    process.exit(1);
});
```

## <a name="try-it-layout-model"></a>**Essayez** : modèle de disposition

> [!div class="checklist"]
>
> * Pour cet exemple, vous aurez besoin d’un **fichier de formulaire au niveau d’un URI**. Vous pouvez utiliser notre [exemple de formulaire](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-layout.pdf) pour ce guide de démarrage rapide.
> * Nous avons ajouté la valeur de l’URI du fichier à la variable `formUrl` en haut du fichier.
> * Pour analyser un fichier donné au niveau d’un URI, vous utiliserez la méthode `beginExtractLayout`.

### <a name="add-the-following-code-to-your-layout-application-on-the-line-below-the-apikey-variable"></a>Ajouter le code suivant à votre application de disposition sur la ligne située sous la variable `apiKey`

```javascript

const formUrl = "https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-layout.pdf"

async function main() {
    const client = new DocumentAnalysisClient(endpoint, new AzureKeyCredential(apiKey));

    const poller = await client.beginExtractLayout(formUrl);

    const {
        pages,
        tables
    } = await poller.pollUntilDone();

    if (pages.length <= 0) {
        console.log("No pages were extracted from the document.");
    } else {
        console.log("Pages:");
        for (const page of pages) {
            console.log("- Page", page.pageNumber, `(unit: ${page.unit})`);
            console.log(`  ${page.width}x${page.height}, angle: ${page.angle}`);
            console.log(`  ${page.lines.length} lines, ${page.words.length} words`);
        }
    }

    if (tables.length <= 0) {
        console.log("No tables were extracted from the document.");
    } else {
        console.log("Tables:");
        for (const table of tables) {
            console.log(
                `- Extracted table: ${table.columnCount} columns, ${table.rowCount} rows (${table.cells.length} cells)`
            );
        }
    }
}

main().catch((error) => {
    console.error("An error occurred:", error);
    process.exit(1);
});

```

## <a name="try-it-prebuilt-model"></a>**Essayez** : Modèle prédéfini

Cet exemple montre comment analyser les données de certains types de documents courants avec un modèle préentraîné, à l’aide d’une facture à titre d’exemple.

> [!div class="checklist"]
>
> * Pour cet exemple, nous analysons un document de facture à l’aide d’un modèle prédéfini. Vous pouvez utiliser notre [exemple de facture](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-invoice.pdf) pour ce guide de démarrage rapide.
> * Nous avons ajouté la valeur de l’URI du fichier à la variable `invoiceUrl` en haut du fichier.
> * Pour analyser un fichier donné au niveau d’un URI, vous utilisez la méthode `beginAnalyzeDocuments` et passez `PrebuiltModels.Invoice` comme ID de modèle. La valeur retournée est un objet `result` contenant des données sur le document envoyé.
> * Par souci de simplicité, toutes les paires clé-valeur retournées par le service ne sont pas indiquées ici. Pour afficher la liste de tous les champs pris en charge et les types correspondants, consultez notre page conceptuelle [Facture](../concept-invoice.md#field-extraction).

### <a name="choose-the-invoice-prebuilt-model-id"></a>Choisir l’ID de modèle de facture prédéfinie

Vous n’êtes pas limité aux factures – Vous avez le choix entre plusieurs modèles prédéfinis, chacun ayant son propre ensemble de champs pris en charge. Le modèle à utiliser pour l’opération d’analyse dépend du type de document à analyser. Voici les ID de modèle pour les modèles prédéfinis actuellement pris en charge par le service Form Recognizer :

* [**Facture prédéfinie**](../concept-invoice.md) : extrait le texte, les marques de sélection, les tableaux, les paires clé-valeur et les informations clés des factures.
* [**Reçu prédéfini**](../concept-receipt.md) : extrait le texte et les informations clés des reçus.
* [**Document d’identité prédéfini**](../concept-id-document.md) : extrait le texte et les informations clés des permis de conduire et des passeports internationaux.
* [**Carte de visite prédéfinie**](../concept-business-card.md) : extrait le texte et les informations clés des cartes de visite.

### <a name="add-the-following-code-to-your-prebuilt-invoice-application-below-the-apikey-variable"></a>Ajouter le code suivant à votre application de facture prédéfinie sous la variable `apiKey`

```javascript

const {PreBuiltModels} = require("@azure/ai-form-recognizer");

// Use of PrebuiltModels.Receipt above (rather than the raw model ID), adds strong typing of the model's output

const invoiceUrl = "https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-invoice.pdf";

async function main() {

    const client = new DocumentAnalysisClient(endpoint, new AzureKeyCredential(apiKey));

    const poller = await client.beginAnalyzeDocuments(PrebuiltModels.Invoice, invoiceUrl);

    const {
        documents: [result]
    } = await poller.pollUntilDone();

    if (result) {
        const invoice = result.fields;

        console.log("Vendor Name:", invoice.vendorName?.value);
        console.log("Customer Name:", invoice.customerName?.value);
        console.log("Invoice Date:", invoice.invoiceDate?.value);
        console.log("Due Date:", invoice.dueDate?.value);

        console.log("Items:");
        for (const {
                properties: item
            } of invoice.items?.values ?? []) {
            console.log("-", item.productCode?.value ?? "<no product code>");
            console.log("  Description:", item.description?.value);
            console.log("  Quantity:", item.quantity?.value);
            console.log("  Date:", item.date?.value);
            console.log("  Unit:", item.unit?.value);
            console.log("  Unit Price:", item.unitPrice?.value);
            console.log("  Tax:", item.tax?.value);
            console.log("  Amount:", item.amount?.value);
        }

        console.log("Subtotal:", invoice.subTotal?.value);
        console.log("Previous Unpaid Balance:", invoice.previousUnpaidBalance?.value);
        console.log("Tax:", invoice.totalTax?.value);
        console.log("Amount Due:", invoice.amountDue?.value);
    } else {
        throw new Error("Expected at least one receipt in the result.");
    }
}

main().catch((error) => {
    console.error("An error occurred:", error);
    process.exit(1);
});

```

## <a name="run-your-application"></a>Exécuter votre application

1. Accédez au dossier dans lequel vous avez votre application de reconnaissance de formulaire (Form-Recognizer-App).

1. Tapez la commande suivante dans votre terminal :

```console
node index.js
```

Félicitations ! Dans ce guide de démarrage rapide, vous avez utilisé Form Recognizer avec le kit SDK JavaScript pour analyser divers formulaires de différentes manières. Explorez à présent la documentation de référence pour en savoir plus sur l’API Form Recognizer v3.0.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Documentation de référence de l’API REST v3.0](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument)

> [!div class="nextstepaction"]
> [Bibliothèque de référence JavaScript de Form Recognizer](https://azuresdkdocs.blob.core.windows.net/$web/javascript/azure-ai-form-recognizer/4.0.0-beta.1/index.html)
