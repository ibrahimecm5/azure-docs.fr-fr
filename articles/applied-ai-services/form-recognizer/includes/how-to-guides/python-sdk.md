---
title: Utiliser la bibliothèque de client Form Recognizer pour Python
description: Utilisez la bibliothèque de client Form Recognizer pour Python afin de créer une application de traitement des formulaires qui extrait des paires clé/valeur et des données de table de vos documents personnalisés.
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 11/02/2021
ms.author: lajanuar
ms.custom: ignite-fall-2021
ms.openlocfilehash: d60a7dcbbc753ea0e311b51f24f7477139698ae5
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131096456"
---
<!-- markdownlint-disable MD024 -->
<!-- markdownlint-disable MD033 -->
<!-- markdownlint-disable MD034 -->

> [!IMPORTANT]
>
> * Ce projet cible la version **2.1** de l’API REST du module Form Recognizer.

[Documentation de référence](/python/api/azure-ai-formrecognizer) | [Code source de la bibliothèque](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/azure/ai/formrecognizer) | [Package (PyPi)](https://pypi.org/project/azure-ai-formrecognizer/) | [Exemples](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples)

## <a name="prerequisites"></a>Prérequis

* Abonnement Azure - [En créer un gratuitement](https://azure.microsoft.com/free/cognitive-services)
* [Python 3.x](https://www.python.org/)
  * Votre installation Python doit inclure [pip](https://pip.pypa.io/en/stable/). Vous pouvez vérifier si pip est installé en exécutant `pip --version` sur la ligne de commande. Procurez-vous pip en installant la dernière version de Python.
* Un blob Stockage Azure qui contient un jeu de données d’apprentissage. Consultez [Créer un jeu de données d’entraînement pour un modèle personnalisé](../../build-training-data-set.md) pour obtenir des conseils et des options pour constituer votre jeu de données d’entraînement. Vous pouvez utiliser les fichiers disponibles dans le dossier **Effectuer l'apprentissage** de [l’exemple de jeu de données](https://go.microsoft.com/fwlink/?linkid=2090451) (téléchargez et extrayez *sample_data.zip*).
* Une fois que vous avez votre abonnement Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title="créez une ressource Form Recognizer"  target="_blank">créer une ressource Form Recognizer</a> sur le portail Azure pour obtenir votre clé et votre point de terminaison. À la fin du déploiement, sélectionnez **Accéder à la ressource**.
  * Vous aurez besoin de la clé et du point de terminaison de la ressource que vous créez pour connecter votre application à l’API Form Recognizer. Collez votre clé et votre point de terminaison dans le code ci-dessous.
  * Vous pouvez utiliser le niveau tarifaire Gratuit (`F0`) pour tester le service, puis passer par la suite à un niveau payant pour la production.

## <a name="setting-up"></a>Configuration

### <a name="install-the-client-library"></a>Installer la bibliothèque de client

Après avoir installé Python, vous pouvez installer la dernière version de la bibliothèque cliente Form Recognizer avec :

```console
pip install azure-ai-formrecognizer 
```

### <a name="create-a-new-python-application"></a>Créer une application Python

Créez une application Python appelée `form-recognizer.py` dans votre éditeur ou IDE préféré. Importez ensuite les bibliothèques suivantes.

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_imports)]

Créez des variables pour le point de terminaison et la clé Azure de votre ressource.

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_creds)]

## <a name="object-model"></a>Modèle objet

Avec Form Recognizer, vous pouvez créer deux types de client différents. Le premier, `form_recognizer_client`, sert à interroger le service pour reconnaître les champs et le contenu de formulaires. Le deuxième, `form_training_client`, sert à créer et à gérer des modèles personnalisés pour améliorer la reconnaissance.

### <a name="formrecognizerclient"></a>FormRecognizerClient

`form_recognizer_client` propose des opérations pour :

* Reconnaître les champs et le contenu de formulaires à l’aide de modèles personnalisés entraînés pour analyser vos formulaires personnalisés.
* Reconnaître le contenu des formulaires, notamment les tableaux, les lignes et les mots, sans avoir à entraîner un modèle.
* Reconnaître les champs communs des tickets de caisse, à l’aide d’un modèle de ticket de caisse préentraîné dans le service Form Recognizer.

### <a name="formtrainingclient"></a>FormTrainingClient

`form_training_client` propose des opérations pour :

* Entraîner les modèles personnalisés à analyser tous les champs et les valeurs rencontrés dans vos formulaires personnalisés. Consultez la [documentation du service sur l’apprentissage d’un modèle sans étiquettes](#train-a-model-without-labels).
* Entraîner les modèles personnalisés à analyser des champs et des valeurs spécifiques que vous spécifiez en étiquetant vos formulaires personnalisés. Consultez la [documentation du service sur l’apprentissage d’un modèle avec étiquettes](#train-a-model-with-labels).
* Gérer les modèles créés dans votre compte.
* Copier un modèle personnalisé d’une ressource Form Recognizer vers une autre.

> [!NOTE]
> Les modèles peuvent aussi être entraînés à partir d’une interface graphique utilisateur comme l’[outil d’étiquetage Form Recognizer](../../label-tool.md).

## <a name="authenticate-the-client"></a>Authentifier le client

Ici, vous allez authentifier deux objets clients à l’aide des variables d’abonnement que vous avez définies ci-dessus. Vous allez utiliser un objet **AzureKeyCredential** pour que, le cas échéant, vous puissiez mettre à jour la clé d’API sans créer de nouveaux objets clients.

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_auth)]

## <a name="get-assets-for-testing"></a>Obtenir des ressources à des fins de test

Vous devrez ajouter des références aux URL pour vos données d’entraînement et de test.

* [!INCLUDE [get SAS URL](../../includes/sas-instructions.md)]

   :::image type="content" source="../../media/quickstarts/get-sas-url.png" alt-text="Récupération d’URL SAS":::

* Utilisez l’exemple de formulaire et d’images de reçu fourni avec les exemples ci-dessous (également disponibles sur [GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms). Vous pouvez également utiliser les étapes ci-dessus pour obtenir l’URL SAS d’un document individuel dans le stockage d’objets blob.

> [!NOTE]
> Les extraits de code présentés dans ce projet utilisent des formulaires distants accessibles par URL. Si vous voulez traiter des documents de formulaire locaux, consultez les méthodes correspondantes dans la [documentation de référence](/python/api/azure-ai-formrecognizer) et les [exemples](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples).

## <a name="analyze-layout"></a>Analyser la disposition

Vous pouvez utiliser Form Recognizer pour analyser les tables, les lignes et les mots dans les documents, sans avoir besoin d’entraîner un modèle. Pour plus d’informations sur l’extraction d’une disposition, consultez le [guide conceptuel des dispositions](../../concept-layout.md).

Pour analyser le contenu d’un fichier à une URL donnée, utilisez la méthode `begin_recognize_content_from_url`. La valeur retournée est une collection d’objets `FormPage` : un pour chaque page du document envoyé. Le code suivant itère au sein de ces objets et imprime les paires clé/valeur extraites et les données de table.

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_getcontent)]

> [!TIP]
> Vous pouvez également récupérer du contenu à partir d’images locales avec les méthodes [FormRecognizerClient](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient), par exemple `begin_recognize_content`. 

### <a name="output"></a>Output

```console
Table found on page 1:
Cell text: Invoice Number
Location: [Point(x=0.5075, y=2.8088), Point(x=1.9061, y=2.8088), Point(x=1.9061, y=3.3219), Point(x=0.5075, y=3.3219)]
Confidence score: 1.0

Cell text: Invoice Date
Location: [Point(x=1.9061, y=2.8088), Point(x=3.3074, y=2.8088), Point(x=3.3074, y=3.3219), Point(x=1.9061, y=3.3219)]
Confidence score: 1.0

Cell text: Invoice Due Date
Location: [Point(x=3.3074, y=2.8088), Point(x=4.7074, y=2.8088), Point(x=4.7074, y=3.3219), Point(x=3.3074, y=3.3219)]
Confidence score: 1.0

Cell text: Charges
Location: [Point(x=4.7074, y=2.8088), Point(x=5.386, y=2.8088), Point(x=5.386, y=3.3219), Point(x=4.7074, y=3.3219)]
Confidence score: 1.0
...

```

## <a name="analyze-receipts"></a>Analyser les reçus

Cette section montre comment analyser et extraire les champs communs de tickets de caisse émis aux États-Unis à l’aide d’un modèle de ticket préentraîné. Pour plus d’informations sur l’analyse des tickets de caisse, consultez le [guide conceptuel des tickets de caisse](../../concept-receipt.md). Pour analyser les tickets de caisse à partir d’une URL, utilisez la méthode `begin_recognize_receipts_from_url`.

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_receipts)]

> [!TIP]
> Vous pouvez également analyser des images locales de tickets de caisse avec les méthodes [FormRecognizerClient](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient), par exemple `begin_recognize_receipts`. 

### <a name="output"></a>Output

```console
ReceiptType: Itemized has confidence 0.659
MerchantName: Contoso Contoso has confidence 0.516
MerchantAddress: 123 Main Street Redmond, WA 98052 has confidence 0.986
MerchantPhoneNumber: None has confidence 0.99
TransactionDate: 2019-06-10 has confidence 0.985
TransactionTime: 13:59:00 has confidence 0.968
Receipt Items:
...Item #1
......Name: 8GB RAM (Black) has confidence 0.916
......TotalPrice: 999.0 has confidence 0.559
...Item #2
......Quantity: None has confidence 0.858
......Name: SurfacePen has confidence 0.858
......TotalPrice: 99.99 has confidence 0.386
Subtotal: 1098.99 has confidence 0.964
Tax: 104.4 has confidence 0.713
Total: 1203.39 has confidence 0.774
```

## <a name="analyze-business-cards"></a>Analyser les cartes de visite

Cette section montre comment analyser et extraire les champs courants des cartes de visite en anglais en utilisant un modèle préentraîné. Pour plus d’informations sur l’analyse des cartes de visite, consultez le [guide conceptuel des cartes de visite](../../concept-business-card.md). 

Pour analyser des cartes de visite à partir d’une URL, utilisez la méthode `begin_recognize_business_cards_from_url`.

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart-preview.py?name=snippet_bc)]

> [!TIP]
 > Vous pouvez également analyser des images locales de cartes de visite avec les méthodes [FormRecognizerClient](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient), par exemple `begin_recognize_business_cards`. 

## <a name="analyze-invoices"></a>Analyser les factures

Cette section montre comment analyser et extraire les champs communs de factures de vente en utilisant un modèle préentraîné. Pour plus d’informations sur l’analyse des factures, consultez le [guide conceptuel des factures](../../concept-invoice.md). 

Pour analyser des factures à partir d’une URL, utilisez la méthode `begin_recognize_invoices_from_url`.

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart-preview.py?name=snippet_invoice)]

> [!TIP]
> Vous pouvez également analyser des images locales de factures avec les méthodes [FormRecognizerClient](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient), par exemple `begin_recognize_invoices`. 

## <a name="analyze-id-documents"></a>Analyser des documents d’identité

Cette section montre comment analyser et extraire des informations clés de documents d’identité gouvernementaux (passeports internationaux et permis de conduire émis aux États-Unis) à l’aide du modèle de document d’identification prédéfini Form Recognizer. Pour plus d’informations sur l’analyse des documents d’identité, consultez notre [guide conceptuel sur le modèle d’identification prédéfini](../../concept-id-document.md).

Pour analyser des documents d’identité à partir d’une URL, utilisez la méthode `begin_recognize_id_documents_from_url`.

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart-preview.py?name=snippet_id)]

> [!TIP]
 > Vous pouvez également analyser des images locales de documents d’identité avec les méthodes [FormRecognizerClient](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python&preserve-view=true#methods), par exemple `begin_recognize_identity_documents`. 

## <a name="train-a-custom-model"></a>Entraîner un modèle personnalisé

Cette section montre comment entraîner un modèle avec vos propres données. Un modèle entraîné peut restituer des données structurées qui incluent les relations clé/valeur du document de formulaire d’origine. Une fois que le modèle est entraîné, vous pouvez le tester, le réentraîner et l’utiliser ensuite pour extraire de manière fiable des données d’autres formulaires, en fonction de vos besoins.

> [!NOTE]
> Vous pouvez aussi effectuer l’apprentissage des modèles à l’aide d’une interface graphique utilisateur comme [l’outil d’étiquetage des exemples Form Recognizer](../../label-tool.md).

### <a name="train-a-model-without-labels"></a>Entraîner un modèle sans étiquettes

Entraînez les modèles personnalisés à analyser tous les champs et valeurs rencontrés dans vos formulaires personnalisés sans étiqueter manuellement les documents d’entraînement.

Le code suivant utilise le client d’entraînement avec la fonction `begin_training` pour entraîner un modèle sur un ensemble de documents donné. L’objet `CustomFormModel` retourné contient des informations sur les types de formulaires que le modèle peut analyser et les champs qu’il peut extraire de chaque type de formulaire. Le bloc de code suivant imprime ces informations dans la console.

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_train)]

### <a name="output"></a>Output

Voici la sortie d’un modèle entraîné avec les données d’apprentissage disponibles dans le kit [SDK Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms/training).

```console
Model ID: 628739de-779c-473d-8214-d35c72d3d4f7
Status: ready
Training started on: 2020-08-20 23:16:51+00:00
Training completed on: 2020-08-20 23:16:59+00:00

Recognized fields:
The submodel with form type 'form-0' has recognized the following fields: Additional Notes:, Address:, Company Name:, Company Phone:, Dated As:, Details, Email:, Hero Limited, Name:, Phone:, Purchase Order, Purchase Order #:, Quantity, SUBTOTAL, Seattle, WA 93849 Phone:, Shipped From, Shipped To, TAX, TOTAL, Total, Unit Price, Vendor Name:, Website:
Document name: Form_1.jpg
Document status: succeeded
Document page count: 1
Document errors: []
Document name: Form_2.jpg
Document status: succeeded
Document page count: 1
Document errors: []
Document name: Form_3.jpg
Document status: succeeded
Document page count: 1
Document errors: []
Document name: Form_4.jpg
Document status: succeeded
Document page count: 1
Document errors: []
Document name: Form_5.jpg
Document status: succeeded
Document page count: 1
Document errors: []
```

### <a name="train-a-model-with-labels"></a>Entraîner un modèle avec des étiquettes

Vous pouvez aussi entraîner les modèles personnalisés en étiquetant manuellement les documents d’entraînement. L’entraînement avec étiquettes offre de meilleures performances dans certains scénarios. Le `CustomFormModel` retourné indique les champs que le modèle peut extraire ainsi qu’une estimation de sa justesse dans chaque champ. Le bloc de code suivant imprime ces informations dans la console.

> [!IMPORTANT]
> Pour effectuer un entraînement avec des étiquettes, vous devez disposer de fichiers d’informations spéciaux sur les étiquettes (`\<filename\>.pdf.labels.json`) dans votre conteneur de stockage d’objets blob, en même temps que les documents d’entraînement. [L’outil d’étiquetage des exemples Form Recognizer](../../label-tool.md) propose une interface utilisateur qui facilite la création de ces fichiers d’étiquettes. Une fois ceux-ci à disposition, vous pouvez appeler la méthode `begin_training` avec le paramètre *use_training_labels* défini sur `true`.

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_trainlabels)]

### <a name="output"></a>Output

Voici la sortie d’un modèle entraîné avec les données d’apprentissage disponibles dans le kit [SDK Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms/training).

```console
Model ID: ae636292-0b14-4e26-81a7-a0bfcbaf7c91

Status: ready
Training started on: 2020-08-20 23:20:56+00:00
Training completed on: 2020-08-20 23:20:57+00:00

Recognized fields:
The submodel with form type 'form-ae636292-0b14-4e26-81a7-a0bfcbaf7c91' has recognized the following fields: CompanyAddress, CompanyName, CompanyPhoneNumber, DatedAs, Email, Merchant, PhoneNumber, PurchaseOrderNumber, Quantity, Signature, Subtotal, Tax, Total, VendorName, Website
Document name: Form_1.jpg
Document status: succeeded
Document page count: 1
Document errors: []
Document name: Form_2.jpg
Document status: succeeded
Document page count: 1
Document errors: []
Document name: Form_3.jpg
Document status: succeeded
Document page count: 1
Document errors: []
Document name: Form_4.jpg
Document status: succeeded
Document page count: 1
Document errors: []
Document name: Form_5.jpg
Document status: succeeded
Document page count: 1
Document errors: []
```

## <a name="analyze-forms-with-a-custom-model"></a>Analyser les formulaires avec un modèle personnalisé

Cette section montre comment extraire des informations clé/valeur et d’autres contenus de vos types de formulaires personnalisés à l’aide de modèles que vous avez entraînés avec vos propres formulaires.

> [!IMPORTANT]
> Pour implémenter ce scénario, vous devez avoir déjà entraîné un modèle pour pouvoir passer son ID dans la méthode ci-dessous. Consultez la section [Entraîner un modèle](#train-a-model-without-labels).

Vous allez utiliser la méthode `begin_recognize_custom_forms_from_url`. La valeur retournée est une collection d’objets `RecognizedForm` : un pour chaque page du document envoyé. Le code suivant imprime les résultats de l’analyse dans la console. Il imprime chaque champ reconnu et la valeur correspondante ainsi qu’un score de confiance.

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_analyze)]

> [!TIP]
> Vous pouvez également analyser des images locales. Consultez les méthodes [FormRecognizerClient](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient), telles que `begin_recognize_custom_forms`. Ou consultez l’exemple de code sur [GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples) pour obtenir des scénarios impliquant des images locales.

### <a name="output"></a>Output

En utilisant le modèle de l’exemple précédent, voici la sortie qui est fournie.

```console
Form type: form-ae636292-0b14-4e26-81a7-a0bfcbaf7c91
Field 'Merchant' has label 'Merchant' with value 'Invoice For:' and a confidence score of 0.116
Field 'CompanyAddress' has label 'CompanyAddress' with value '1 Redmond way Suite 6000 Redmond, WA' and a confidence score of 0.258
Field 'Website' has label 'Website' with value '99243' and a confidence score of 0.114
Field 'VendorName' has label 'VendorName' with value 'Charges' and a confidence score of 0.145
Field 'CompanyPhoneNumber' has label 'CompanyPhoneNumber' with value '$56,651.49' and a confidence score of 0.249
Field 'CompanyName' has label 'CompanyName' with value 'PT' and a confidence score of 0.245
Field 'DatedAs' has label 'DatedAs' with value 'None' and a confidence score of None
Field 'Email' has label 'Email' with value 'None' and a confidence score of None
Field 'PhoneNumber' has label 'PhoneNumber' with value 'None' and a confidence score of None
Field 'PurchaseOrderNumber' has label 'PurchaseOrderNumber' with value 'None' and a confidence score of None
Field 'Quantity' has label 'Quantity' with value 'None' and a confidence score of None
Field 'Signature' has label 'Signature' with value 'None' and a confidence score of None
Field 'Subtotal' has label 'Subtotal' with value 'None' and a confidence score of None
Field 'Tax' has label 'Tax' with value 'None' and a confidence score of None
Field 'Total' has label 'Total' with value 'None' and a confidence score of None
```

## <a name="manage-custom-models"></a>Gérer des modèles personnalisés

Cette section explique comment gérer les modèles personnalisés stockés dans votre compte.

### <a name="check-the-number-of-models-in-the-formrecognizer-resource-account"></a>Vérifier le nombre de modèles dans le compte de ressource FormRecognizer

Le bloc de code suivant vérifie le nombre de modèles que vous avez enregistrés dans votre compte Form Recognizer et le compare à la limite du compte.

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_manage_count)]

### <a name="output"></a>Output

```console
Our account has 5 custom models, and we can have at most 5000 custom models
```

### <a name="list-the-models-currently-stored-in-the-resource-account"></a>Lister les modèles actuellement stockés dans le compte de ressource

Le bloc de code suivant liste les modèles actifs dans votre compte et en imprime les détails dans la console. Il enregistre également une référence au premier modèle.

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_manage_list)]

### <a name="output"></a>Output

Voici un exemple de sortie pour le compte de test.

```console
We have models with the following ids:
453cc2e6-e3eb-4e9f-aab6-e1ac7b87e09e
628739de-779c-473d-8214-d35c72d3d4f7
ae636292-0b14-4e26-81a7-a0bfcbaf7c91
b4b5df77-8538-4ffb-a996-f67158ecd305
c6309148-6b64-4fef-aea0-d39521452699
```

### <a name="get-a-specific-model-using-the-models-id"></a>Obtenir un modèle spécifique à l’aide de l’ID du modèle

Le bloc de code suivant utilise l’ID de modèle enregistré dans la section précédente et l’utilise pour récupérer des détails sur le modèle.

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_manage_getmodel)]

### <a name="output"></a>Output

Voici l’exemple de sortie pour le modèle personnalisé créé dans l’exemple précédent.

```console
Model ID: ae636292-0b14-4e26-81a7-a0bfcbaf7c91
Status: ready
Training started on: 2020-08-20 23:20:56+00:00
Training completed on: 2020-08-20 23:20:57+00:00
```

### <a name="delete-a-model-from-the-resource-account"></a>Supprimer un modèle du compte de ressource

Vous pouvez aussi supprimer un modèle de votre compte en référençant son ID. Ce code supprime le modèle utilisé dans la section précédente.

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_manage_delete)]

## <a name="run-the-application"></a>Exécution de l'application

Exécutez l’application avec la commande `python` ci-dessous :

```console
python form-recognizer.py
```

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous souhaitez nettoyer et supprimer un abonnement Cognitive Services, vous pouvez supprimer la ressource ou le groupe de ressources. La suppression du groupe de ressources efface également les autres ressources qui y sont associées.

* [Portail](../../../../cognitive-services/cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../../cognitive-services/cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="troubleshooting"></a>Dépannage

### <a name="general"></a>Général

La bibliothèque de client Form Recognizer lève les exceptions définies dans [Azure Core](https://aka.ms/azsdk-python-azure-core).

### <a name="logging"></a>Journalisation

Cette bibliothèque utilise la [bibliothèque de journalisation standard](https://docs.python.org/3/library/logging.html) pour la journalisation. Les informations de base sur les sessions HTTP (URL, en-têtes, etc.) sont journalisées au niveau INFO.

La journalisation détaillée de niveau DEBUG, comprenant le corps de la demande et/ou de la réponse et les en-têtes non rédigés, peut être activée sur un client à l’aide de l’argument de mot clé `logging_enable` :

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerLogging.py?name=snippet_logging)]

De la même façon, `logging_enable` peut activer la journalisation détaillée pour une seule opération, même quand elle n’est pas activée pour le client :

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerLogging.py?name=snippet_example)]

## <a name="rest-samples-on-github"></a>Exemples REST sur GitHub

* Extraire du texte, des marques de sélection et une structure de tableau à partir de documents
  * [Extraire des données de disposition - Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-layout.md)
* Entraîner des modèles personnalisés et extraire des données de formulaire
  * [Effectuer l’entraînement sans étiquettes - Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-train-extract.md)
  * [Effectuer l’entraînement avec des étiquettes - Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-labeled-data.md)
* Extraire des données dans des factures
  * [Extraire les données de factures - Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-invoices.md)
* Extraire les données des reçus de ventes
  * [Extraire des données de reçu - Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-receipts.md)
* Extraire des données à partir de cartes de visite
  * [Extraire des données de cartes de visite - Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-business-cards.md)

## <a name="next-steps"></a>Étapes suivantes

Dans ce projet, vous avez utilisé la bibliothèque de client Form Recognizer Python pour entraîner des modèles et analyser des formulaires de différentes manières. Découvrez ensuite les astuces pour créer un jeu de données d’apprentissage plus performant et produire des modèles plus précis.

> [!div class="nextstepaction"]
> [Créer un jeu de données d’apprentissage](../../build-training-data-set.md)

* [Qu’est-ce que Form Recognizer ?](../../overview.md)

* Vous trouverez l’exemple de code de ce projet sur [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/FormRecognizerQuickstart.py).
