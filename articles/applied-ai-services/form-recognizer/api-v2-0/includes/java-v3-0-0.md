---
title: 'Informations de référence : Bibliothèque de client Form Recognizer 3.1.1 pour Java'
description: Utilisez la bibliothèque de client Form Recognizer pour Java afin de créer une application de traitement des formulaires avec l’extension Docs Markdown de Docs Authoring Pack pour VS Code pour extraire des paires clé/valeur et des données de table de vos documents personnalisés.
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 11/02/2021
ms.author: lajanuar
ms.custom: ignite-fall-2021
ms.openlocfilehash: 504cc328d7336c829c30222f0ff95c11291c1955
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131253045"
---
<!-- markdownlint-disable MD001 -->
<!-- markdownlint-disable MD024 -->
<!-- markdownlint-disable MD033 -->
<!-- markdownlint-disable MD034 -->

[Documentation de référence](/java/api/overview/azure/ai-formrecognizer-readme) | [Code source de la bibliothèque](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src) | [Package (Maven)](https://mvnrepository.com/artifact/com.azure/azure-ai-formrecognizer) | [Exemples](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src/samples/README.md)

## <a name="prerequisites"></a>Prérequis

* Abonnement Azure - [En créer un gratuitement](https://azure.microsoft.com/free/cognitive-services)
* La version actuelle du [JDK (Java Development Kit)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* L’[outil de génération Gradle](https://gradle.org/install/) ou un autre gestionnaire de dépendances.
* Une fois que vous avez votre abonnement Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title="créez une ressource Form Recognizer"  target="_blank">créer une ressource Form Recognizer</a> sur le portail Azure pour obtenir votre clé et votre point de terminaison. À la fin du déploiement, sélectionnez **Accéder à la ressource**.
  * Vous aurez besoin de la clé et du point de terminaison de la ressource que vous créez pour connecter votre application à l’API Form Recognizer. Collez votre clé et votre point de terminaison dans le code ci-dessous, plus loin dans le guide de démarrage rapide.
  * Vous pouvez utiliser le niveau tarifaire Gratuit (`F0`) pour tester le service, puis passer par la suite à un niveau payant pour la production.
* Un blob Stockage Azure qui contient un jeu de données d’apprentissage. Voir [Créer un jeu de données d’apprentissage pour un modèle personnalisé](../../build-training-data-set.md). Dans le cadre de ce guide de démarrage rapide, vous pouvez utiliser les fichiers disponibles dans le dossier **Train** de l’[exemple de jeu de données](https://go.microsoft.com/fwlink/?linkid=2090451) (téléchargez et extrayez *sample_data.zip*).

## <a name="setting-up"></a>Configuration

### <a name="create-a-new-gradle-project"></a>Créer un projet Gradle

Dans une fenêtre de console (telle que cmd, PowerShell ou bash), créez un répertoire pour votre application et accédez-y.

```console
mkdir myapp && cd myapp
```

Exécutez la commande `gradle init` à partir de votre répertoire de travail. Cette commande crée des fichiers de build essentiels pour Gradle, notamment *build.gradle.kts*, qui est utilisé au moment de l’exécution pour créer et configurer votre application.

```console
gradle init --type basic
```

Quand vous êtes invité à choisir un **DSL**, sélectionnez **Kotlin**.

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
    implementation(group = "com.azure", name = "azure-ai-formrecognizer", version = "3.1.0-beta.3")
}
```

> [!NOTE]
> Le SDK Form Recognizer 3.0.0 cible la version 2.0 de l’API

### <a name="create-a-java-file"></a>Créer un fichier Java

Entrez la commande suivante à partir de votre répertoire de travail :

```console
mkdir -p src/main/java
```

Accédez au nouveau dossier et créez un fichier sous le nom *FormRecognizer.java*. Ouvrez-le dans votre éditeur ou IDE habituel et ajoutez les instructions `import` suivantes :

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_imports)]

> [!TIP]
> Vous trouverez la totalité du fichier avec les exemples de code de ce guide de démarrage rapide sur [**GitHub**](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/FormRecognizer/FormRecognizer.java).

Dans la classe **FormRecognizer** de l’application, créez des variables pour la clé et le point de terminaison de votre ressource.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_creds)]

> [!IMPORTANT]
> Accédez au portail Azure. Si la ressource Form Recognizer que vous avez créée dans la section **Prérequis** a été déployée, cliquez sur le bouton **Accéder à la ressource** sous **Étapes suivantes**. La clé et le point de terminaison se trouvent dans la page **Clé et point de terminaison** de la ressource, sous **Gestion des ressources**.
>
> N’oubliez pas de supprimer la clé de votre code une fois que vous avez terminé, et ne la postez jamais publiquement. En production, utilisez des méthodes sécurisées pour stocker vos informations d’identification et y accéder. Pour plus d’informations, _consultez_ [Sécurité](../../../../cognitive-services/cognitive-services-security.md) Cognitive Services.

Dans la méthode **main** de l’application, ajoutez des appels pour les méthodes utilisées dans ce guide de démarrage rapide. Vous définissez ces méthodes plus tard. Vous devrez aussi ajouter des références aux URL pour vos données d’entraînement et de test.

* [!INCLUDE [get SAS URL](../../includes/sas-instructions.md)]

   :::image type="content" source="../../media/quickstarts/get-sas-url.png" alt-text="Récupération d’URL SAS":::
* Pour obtenir l’URL d’un formulaire à tester, vous pouvez utiliser les étapes ci-dessus pour obtenir l’URL SAS d’un document individuel dans le stockage d’objets blob. Vous pouvez aussi prendre l’URL d’un document situé ailleurs.
* Employez également la méthode ci-dessus pour obtenir l’URL d’une image de ticket de caisse.
<!-- markdownlint-disable MD024 -->

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_mainvars)]

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_maincalls)]

## <a name="object-model"></a>Modèle objet

Avec Form Recognizer, vous pouvez créer deux types de client différents. Le premier, `FormRecognizerClient`, sert à interroger le service pour reconnaître les champs et le contenu de formulaires. Le deuxième, `FormTrainingClient`, sert à créer et à gérer des modèles personnalisés pour améliorer la reconnaissance.

### <a name="formrecognizerclient"></a>FormRecognizerClient

`FormRecognizerClient` propose des opérations pour :

* Reconnaître les champs et le contenu de formulaires, à l’aide de modèles personnalisés entraînés pour analyser vos formulaires personnalisés.  Ces valeurs sont retournées dans une collection d’objets `RecognizedForm`. Consultez l’exemple [Analyser les formulaires personnalisés](#analyze-forms-with-a-custom-model).
* Reconnaître le contenu des formulaires, notamment les tableaux, les lignes et les mots, sans avoir à entraîner un modèle.  Le contenu des formulaires est retourné dans une collection d’objets `FormPage`. Consultez l’exemple [Analyser la disposition](#analyze-layout).
* Reconnaître les champs courants des reçus, cartes de visite, factures et documents d’identité émis aux États-Unis à l’aide d’un modèle préentraîné dans le service Form Recognizer.

### <a name="formtrainingclient"></a>FormTrainingClient

`FormTrainingClient` propose des opérations pour :

* Entraîner les modèles personnalisés à analyser tous les champs et les valeurs rencontrés dans vos formulaires personnalisés.  Un `CustomFormModel` est retourné indiquant les types de formulaire que le modèle va analyser et les champs qu’il va extraire pour chaque type de formulaire.
* Entraîner les modèles personnalisés à analyser des champs et des valeurs spécifiques que vous spécifiez en étiquetant vos formulaires personnalisés.  Un `CustomFormModel` est retourné indiquant les champs que le modèle va extraire ainsi que la précision estimée pour chaque champ.
* Gérer les modèles créés dans votre compte.
* Copier un modèle personnalisé d’une ressource Form Recognizer vers une autre.

> [!NOTE]
> Les modèles peuvent aussi être entraînés à partir d’une interface graphique utilisateur comme l’[outil d’étiquetage Form Recognizer](../../label-tool.md).

## <a name="code-examples"></a>Exemples de code

Ces extraits de code montrent comment effectuer les tâches suivantes avec la bibliothèque de client Form Recognizer pour Java :
<!-- markdownlint-disable MD001 -->

* [Authentifier le client](#authenticate-the-client)
* [Analyser la disposition](#analyze-layout)
* [Analyser les reçus](#analyze-receipts)
* [Entraîner un modèle personnalisé](#train-a-custom-model)
* [Analyser les formulaires avec un modèle personnalisé](#analyze-forms-with-a-custom-model)
* [Gérer vos modèles personnalisés](#manage-your-custom-models)

## <a name="authenticate-the-client"></a>Authentifier le client

En haut de votre méthode **main**, ajoutez le code suivant. Ici, vous allez authentifier deux objets clients à l’aide des variables d’abonnement que vous avez définies ci-dessus. Vous allez utiliser un objet **AzureKeyCredential** pour que, le cas échéant, vous puissiez mettre à jour la clé d’API sans créer de nouveaux objets clients.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_auth)]

## <a name="analyze-layout"></a>Analyser la disposition

Vous pouvez utiliser Form Recognizer pour analyser les tables, les lignes et les mots dans les documents, sans avoir besoin d’entraîner un modèle. Pour plus d’informations sur l’extraction d’une disposition, consultez le [guide conceptuel des dispositions](../../concept-layout.md).

Pour analyser le contenu d’un fichier à une URL donnée, utilisez la méthode **beginRecognizeContentFromUrl**.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_getcontent_call)]

> [!TIP]
> Vous pouvez également obtenir le contenu à partir d’un fichier local. Consultez les méthodes [FormRecognizerClient](/java/api/com.azure.ai.formrecognizer.formrecognizerclient), telles que **beginRecognizeContent**. Ou consultez l’exemple de code sur [GitHub](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src/samples/README.md) pour obtenir des scénarios impliquant des images locales.

La valeur retournée est une collection d’objets **FormPage** : un pour chaque page du document envoyé. Le code suivant itère au sein de ces objets et imprime les paires clé/valeur extraites et les données de table.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_getcontent_print)]

### <a name="output"></a>Output

```console
Get form content...
----Recognizing content ----
Has width: 8.500000 and height: 11.000000, measured with unit: inch.
Table has 2 rows and 6 columns.
Cell has text Invoice Number.
Cell has text Invoice Date.
Cell has text Invoice Due Date.
Cell has text Charges.
Cell has text VAT ID.
Cell has text 458176.
Cell has text 3/28/2018.
Cell has text 4/16/2018.
Cell has text $89,024.34.
Cell has text ET.
```

## <a name="analyze-receipts"></a>Analyser les reçus

Cette section montre comment analyser et extraire les champs communs de tickets de caisse émis aux États-Unis à l’aide d’un modèle de ticket préentraîné. Pour plus d’informations sur l’analyse des tickets de caisse, consultez le [guide conceptuel des tickets de caisse](../../concept-receipt.md).

Pour analyser les tickets à partir d’un URI, utilisez la méthode **beginRecognizeReceiptsFromUrl**.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_receipts_call)]

> [!TIP]
> Vous pouvez également analyser les images de tickets de caisse locales. Consultez les méthodes [FormRecognizerClient](/java/api/com.azure.ai.formrecognizer.formrecognizerclient), telles que **beginRecognizeReceipts**. Ou consultez l’exemple de code sur [GitHub](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src/samples/README.md) pour obtenir des scénarios impliquant des images locales.

La valeur retournée est une collection d’objets **RecognizedReceipt** : un pour chaque page du document envoyé. Le bloc de code suivant itère au sein des tickets de caisse et en imprime les détails dans la console.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_receipts_print)]

Le bloc de code suivant itère au sein des éléments individuels détectés sur le ticket et en imprime les détails dans la console.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_receipts_print_items)]

### <a name="output"></a>Output

```console
Analyze receipt...
----------- Recognized Receipt page 0 -----------
Merchant Name: Contoso Contoso, confidence: 0.62
Merchant Address: 123 Main Street Redmond, WA 98052, confidence: 0.99
Transaction Date: 2020-06-10, confidence: 0.90
Receipt Items:
Name: Cappuccino, confidence: 0.96s
Quantity: null, confidence: 0.957s]
Total Price: 2.200000, confidence: 0.95
Name: BACON & EGGS, confidence: 0.94s
Quantity: null, confidence: 0.927s]
Total Price: null, confidence: 0.93
```

## <a name="train-a-custom-model"></a>Entraîner un modèle personnalisé

Cette section montre comment entraîner un modèle avec vos propres données. Un modèle formé peut restituer des données structurées qui incluent les relations clé-valeur du formulaire d’origine. Après avoir effectué l’apprentissage du modèle, testez-le et effectuez à nouveau son apprentissage, le cas échéant. Enfin, utilisez-le pour extraire de manière fiable des données d’autres formulaire selon vos besoins.

> [!NOTE]
> Vous pouvez aussi entraîner des modèles à l’aide d’une interface graphique utilisateur telle que l’[outil d’étiquetage des exemples Form Recognizer](../../label-tool.md).

### <a name="train-a-model-without-labels"></a>Entraîner un modèle sans étiquettes

Entraînez les modèles personnalisés à analyser tous les champs et valeurs rencontrés dans vos formulaires personnalisés sans étiqueter manuellement les documents d’entraînement.

La méthode suivante entraîne un modèle sur un ensemble donné de documents et imprime l’état du modèle dans la console.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_train_call)]

L’objet **CustomFormModel** retourné contient des informations sur les types de formulaires que le modèle peut analyser et les champs qu’il peut extraire de chaque type de formulaire. Le bloc de code suivant imprime ces informations dans la console.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_train_print)]

Enfin, cette méthode retourne l’ID unique du modèle.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_train_return)]

### <a name="output"></a>Output

```console
Train Model with training data...
Model Id: 20c3544d-97b4-49d9-b39b-dc32d85f1358
Model Status: ready
Training started on: 2020-08-31T16:52:09Z
Training completed on: 2020-08-31T16:52:23Z

Recognized Fields:
The subModel has form type form-0
The model found field 'field-0' with label: Address:
The model found field 'field-1' with label: Charges
The model found field 'field-2' with label: Invoice Date
The model found field 'field-3' with label: Invoice Due Date
The model found field 'field-4' with label: Invoice For:
The model found field 'field-5' with label: Invoice Number
The model found field 'field-6' with label: VAT ID
```

### <a name="train-a-model-with-labels"></a>Entraîner un modèle avec des étiquettes

Vous pouvez aussi entraîner les modèles personnalisés en étiquetant manuellement les documents d’entraînement. L’entraînement avec étiquettes offre de meilleures performances dans certains scénarios. Pour entraîner avec des étiquettes, vous devez disposer de fichiers d’informations spéciaux sur les étiquettes ( *\<filename\>.pdf.labels.json*) dans votre conteneur de stockage d’objets blob, en même temps que les documents d’entraînement. L’[outil d’étiquetage des exemples Form Recognizer](../../label-tool.md) propose une interface utilisateur qui facilite la création de ces fichiers d’étiquettes. Une fois ceux-ci à disposition, vous pouvez appeler la méthode **beginTraining** avec le paramètre *useTrainingLabels* défini sur `true`.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_trainlabels_call)]

Le **CustomFormModel** retourné indique les champs que le modèle peut extraire ainsi qu’une estimation de sa justesse dans chaque champ. Le bloc de code suivant imprime ces informations dans la console.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_trainlabels_print)]

### <a name="output"></a>Output

```console
Train Model with training data...
Model Id: 20c3544d-97b4-49d9-b39b-dc32d85f1358
Model Status: ready
Training started on: 2020-08-31T16:52:09Z
Training completed on: 2020-08-31T16:52:23Z

Recognized Fields:
The subModel has form type form-0
The model found field 'field-0' with label: Address:
The model found field 'field-1' with label: Charges
The model found field 'field-2' with label: Invoice Date
The model found field 'field-3' with label: Invoice Due Date
The model found field 'field-4' with label: Invoice For:
The model found field 'field-5' with label: Invoice Number
The model found field 'field-6' with label: VAT ID
```

## <a name="analyze-forms-with-a-custom-model"></a>Analyser les formulaires avec un modèle personnalisé

Cette section montre comment extraire des informations clé/valeur et d’autres contenus de vos types de formulaires personnalisés à l’aide de modèles que vous avez entraînés avec vos propres formulaires.

> [!IMPORTANT]
> Pour implémenter ce scénario, vous devez avoir déjà entraîné un modèle pour pouvoir passer son ID dans la méthode ci-dessous. Consultez la section [Entraîner un modèle](#train-a-model-without-labels).

Vous allez utiliser la méthode **beginRecognizeCustomFormsFromUrl**.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_analyze_call)]

> [!TIP]
> Vous pouvez également analyser un fichier local. Consultez les méthodes [FormRecognizerClient](/java/api/com.azure.ai.formrecognizer.formrecognizerclient), telles que **beginRecognizeCustomForms**. Ou consultez l’exemple de code sur [GitHub](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src/samples/README.md) pour obtenir des scénarios impliquant des images locales.

La valeur retournée est une collection d’objets **RecognizedForm** : un pour chaque page du document envoyé. Le code suivant imprime les résultats de l’analyse dans la console. Il imprime chaque champ reconnu et la valeur correspondante ainsi qu’un score de confiance.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_analyze_print)]

### <a name="output"></a>Output

```console
Analyze PDF form...
----------- Recognized custom form info for page 0 -----------
Form type: form-0
Field 'field-0' has label 'Address:' with a confidence score of 0.91.
Field 'field-1' has label 'Invoice For:' with a confidence score of 1.00.
Field 'field-2' has label 'Invoice Number' with a confidence score of 1.00.
Field 'field-3' has label 'Invoice Date' with a confidence score of 1.00.
Field 'field-4' has label 'Invoice Due Date' with a confidence score of 1.00.
Field 'field-5' has label 'Charges' with a confidence score of 1.00.
Field 'field-6' has label 'VAT ID' with a confidence score of 1.00.
```

## <a name="manage-custom-models"></a>Gérer des modèles personnalisés

Cette section explique comment gérer les modèles personnalisés stockés dans votre compte. Le code suivant effectue toutes les tâches de gestion de modèles dans une même méthode, à titre d’exemple. Commencez par copier la signature de méthode ci-dessous :

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_manage)]

### <a name="check-the-number-of-models-in-the-formrecognizer-resource-account"></a>Vérifier le nombre de modèles dans le compte de ressource FormRecognizer

Le bloc de code suivant vérifie le nombre de modèles que vous avez enregistrés dans votre compte Form Recognizer et le compare à la limite du compte.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_manage_count)]

#### <a name="output"></a>Output

```console
The account has 12 custom models, and we can have at most 250 custom models
```

### <a name="list-the-models-currently-stored-in-the-resource-account"></a>Lister les modèles actuellement stockés dans le compte de ressource

Le bloc de code suivant liste les modèles actifs dans votre compte et imprime leurs détails dans la console.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_manage_list)]

#### <a name="output"></a>Output

Cette réponse a été tronquée dans un souci de lisibilité.

```console
We have following models in the account:
Model Id: 0b048b60-86cc-47ec-9782-ad0ffaf7a5ce
Model Id: 0b048b60-86cc-47ec-9782-ad0ffaf7a5ce
Model Status: ready
Training started on: 2020-06-04T18:33:08Z
Training completed on: 2020-06-04T18:33:10Z
Custom Model Form type: form-0b048b60-86cc-47ec-9782-ad0ffaf7a5ce
Custom Model Accuracy: 1.00
Field Text: invoice date
Field Accuracy: 1.00
Field Text: invoice number
Field Accuracy: 1.00
...
```

### <a name="delete-a-model-from-the-resource-account"></a>Supprimer un modèle du compte de ressource

Vous pouvez aussi supprimer un modèle de votre compte en référençant son ID.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_manage_delete)]

## <a name="run-the-application"></a>Exécution de l'application

Revenez à votre répertoire de projet principal. Générez ensuite l’application avec la commande suivante :

```console
gradle build
```

Exécutez l’application avec l’objectif `run` :

```console
gradle run
```

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous souhaitez nettoyer et supprimer un abonnement Cognitive Services, vous pouvez supprimer la ressource ou le groupe de ressources. La suppression du groupe de ressources efface également les autres ressources qui y sont associées.

* [Portail](../../../../cognitive-services/cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../../cognitive-services/cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="troubleshooting"></a>Dépannage

Les clients Form Recognizer lèvent des exceptions `ErrorResponseException`. Par exemple, si vous essayez de fournir une URL de source de fichier non valide, une exception `ErrorResponseException` est levée avec une erreur indiquant la cause de l’échec. Dans l’extrait de code suivant, l’erreur est gérée correctement via l’interception de l’exception et en affichant des informations supplémentaires sur l’erreur en question.

```java Snippet:FormRecognizerBadRequest
try {
    formRecognizerClient.beginRecognizeContentFromUrl("invalidSourceUrl");
} catch (ErrorResponseException e) {
    System.out.println(e.getMessage());
}
```

### <a name="enable-client-logging"></a>Activer la journalisation du client

Les kits SDK Azure pour Java proposent un scénario de journalisation cohérent pour faciliter le dépannage des erreurs d’application et accélérer leur résolution. Les journaux produits capturent le flux d’une application avant d’atteindre l’état terminal pour faciliter la localisation du problème racine. Consultez le [wiki sur la journalisation](https://github.com/Azure/azure-sdk-for-java/wiki/Logging-with-Azure-SDK) pour obtenir des indications sur l’activation de la journalisation.

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez utilisé la bibliothèque de client Java Form Recognizer pour entraîner des modèles et analyser des formulaires de différentes manières. Découvrez ensuite les astuces pour créer un jeu de données d’apprentissage plus performant et produire des modèles plus précis.

> [!div class="nextstepaction"]
> [Créer un jeu de données d’apprentissage](../../build-training-data-set.md)

* [Qu’est-ce que Form Recognizer ?](../../overview.md)
* Vous trouverez l’exemple de code de ce guide (et bien plus encore) sur [GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/src/samples).
