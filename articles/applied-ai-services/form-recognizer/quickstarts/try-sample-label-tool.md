---
title: 'Démarrage rapide : Étiqueter des formulaires, effectuer l’apprentissage d’un modèle et analyser des formulaires à l’aide de l’outil d’étiquetage des exemples : Form Recognizer'
titleSuffix: Azure Applied AI Services
description: Dans ce guide de démarrage rapide, vous allez utiliser l’outil d’étiquetage des exemples Form Recognizer pour étiqueter manuellement des documents de formulaire. Ensuite, vous allez effectuer l’apprentissage d’un modèle de traitement de documents personnalisé, et utiliser le modèle pour extraire des paires clé/valeur.
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 11/02/2021
ms.author: lajanuar
ms.custom: cog-serv-seo-may-2021, ignite-fall-2021
keywords: traitement de documents
ms.openlocfilehash: 5848fff7fab23d561bf79f1afbc2d21d4d7c3875
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131026767"
---
<!-- markdownlint-disable MD001 -->
<!-- markdownlint-disable MD024 -->
<!-- markdownlint-disable MD033 -->
<!-- markdownlint-disable MD034 -->
<!-- markdownlint-disable MD029 -->
# <a name="get-started-with-the-sample-labeling-tool"></a>Prise en main de l’outil d’étiquetage des exemples

Azure Form Recognizer est un service cloud d’Azure Applied AI Services qui utilise des modèles Machine Learning pour extraire et analyser les champs de formulaire, le texte et les tableaux de vos documents. Vous pouvez utiliser Form Recognizer pour automatiser le traitement des données dans les applications et les workflows, pour améliorer les stratégies pilotées par les données et pour enrichir les fonctionnalités de recherche de documents. 

L’outil d’étiquetage des exemples Form Recognizer est un outil open source qui vous permet de tester les fonctionnalités les plus récentes des services de reconnaissance de formulaire et de reconnaissance optique de caractères (OCR) Azure :

* [Analyser les documents avec l’API Layout](#analyze-layout). Essayez l’API Layout pour extraire du texte, des tableaux, des marques de sélection et une structure à partir de documents.

* [Analyser les documents à l’aide d’un modèle prédéfini](#analyze-using-a-prebuilt-model). Commencez avec un modèle prédéfini pour extraire des données des factures, des reçus, des documents d’identité ou des cartes de visite.

* [Entraîner et analyser un formulaire personnalisé](#train-a-custom-form-model). Utilisez un modèle personnalisé pour extraire des données de documents spécifiques pour distinguer les données d’entreprise et les cas d’usage.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous aurez besoin des éléments suivants :

* Un abonnement Azure : [vous pouvez en créer un gratuitement](https://azure.microsoft.com/free/cognitive-services/)

* Une ressource Cognitive Services ou Form Recognizer. Une fois que vous avez votre abonnement Azure, créez une ressource Form Recognizer [monoservice](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) ou [multiservice](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne) dans le portail Azure pour obtenir votre clé et votre point de terminaison. Vous pouvez utiliser le niveau tarifaire Gratuit (`F0`) pour tester le service, puis passer par la suite à un niveau payant pour la production.

    > [!TIP]
    > Créez une ressource Cognitive Services si vous envisagez d’accéder à plusieurs services Cognitive Services sous un seul point de terminaison/clé. Pour l’accès à Form Recognizer uniquement, créez une ressource Form Recognizer. Notez que vous avez besoin d’une ressource de service unique si vous avez l’intention d’utiliser l’[authentification Azure Active Directory](/azure/active-directory/authentication/overview-authentication).

## <a name="create-a-form-recognizer-resource"></a>Créer une ressource Form Recognizer

[!INCLUDE [create resource](../includes/create-resource.md)]

 :::image type="content" source="../media/containers/keys-and-endpoint.png" alt-text="Capture d’écran : clés et emplacement du point de terminaison dans le portail Azure":::

## <a name="analyze-using-a-prebuilt-model"></a>Analyser à l’aide d’un modèle prédéfini

Le module Form Recognizer propose plusieurs modèles prédéfinis. Chaque modèle possède son propre ensemble de champs pris en charge. Le modèle à utiliser pour l’opération d’analyse dépend du type de document à analyser. Voici les modèles prédéfinis actuellement pris en charge par le service Form Recognizer :

* [**Facture**](../concept-invoice.md) : extrait le texte, les marques de sélection, les tableaux, les paires clé-valeur et les informations clés des factures.
* [**Reçu**](../concept-receipt.md) : extrait le texte et les informations clés des reçus.
* [**Document d’identité**](../concept-id-document.md) : extrait le texte et les informations clés des permis de conduire et des passeports internationaux.
* [**Carte de visite**](../concept-business-card.md) : extrait le texte et les informations clés des cartes de visite.

1. Accédez à l’[outil d’étiquetage des exemples Form Recognizer](https://fott-2-1.azurewebsites.net/).

1. Dans la page d’accueil de l’outil d’étiquetage des exemples, sélectionnez **Utiliser un modèle prédéfini pour obtenir des données**.

    :::image type="content" source="../media/label-tool/prebuilt-1.jpg" alt-text="Analyser les résultats de la disposition Form Recognizer":::

1. Sélectionnez le **type de formulaire** que vous souhaitez analyser dans la fenêtre déroulante.

1. Choisissez un URL pour le fichier que vous souhaitez analyser à partir des options ci-dessous :

    * [**Exemple de facture**](https://raw.githubusercontent.com/Azure/azure-sdk-for-python/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms/forms/Invoice_1.pdf).
    * [**Exemple de document d’identité**](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/DriverLicense.png).
    * [**Exemple de reçu**](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/contoso-allinone.jpg).
    * [**Exemple de carte de visite**](https://raw.githubusercontent.com/Azure/azure-sdk-for-python/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms/business_cards/business-card-english.jpg).

1. Dans le champ **Source : URL**, collez l’URL sélectionnée et cliquez sur le bouton **Extraire**.

1. Dans le champ **Point de terminaison de service Form Recognizer**, collez le point de terminaison que vous avez obtenu avec votre abonnement Form Recognizer.

1. Dans le champ **Clé API**, collez la clé d’abonnement que vous avez obtenue avec votre ressource Form Recognizer.

    :::image type="content" source="../media/fott-select-form-type.png" alt-text="Capture d’écran : fenêtre déroulante Sélectionner un type de formulaire.":::

1. Cliquez sur **Exécuter l’analyse**. L’outil d’étiquetage des exemples Form Recognizer appelle alors l’API Analyze Prebuilt (Analyser des éléments prédéfinis) et analyse le document.

1. Afficher les résultats : consultez les paires clé/valeur extraites, les éléments de ligne, le texte mis en évidence extrait et les tableaux détectés.

    :::image type="content" source="../media/label-tool/prebuilt-2.jpg" alt-text="Analyser les résultats du modèle de facture Form Recognizer":::

1. Téléchargez le fichier de sortie JSON pour consulter les résultats détaillés.

    * Le nœud « readResults » contient chaque ligne de texte avec sa position de cadre englobant respective dans la page.
    * Le nœud « selectionMarks » affiche chaque marque de sélection (case à cocher, case d’option) et indique si son état est « sélectionné » ou « non sélectionné ».
    * La section « pageResults » comprend les tableaux extraits. Pour chaque tableau, le texte, l’index de ligne et de colonne, l’étendue de ligne et de colonne, le cadre englobant, etc. sont extraits.
    * Le champ « documentResults » contient les informations sur les paires clé/valeur et sur les éléments de ligne pour les parties les plus pertinentes du document.

## <a name="analyze-layout"></a>Analyser la disposition

L’API de disposition d’Azure Form Recognizer extrait du texte, des tableaux, des marques de sélection et des informations de structure à partir de documents (PDF, TIFF) et d’images (JPG, PNG, BMP).

1. Accédez à l’[outil d’étiquetage des exemples Form Recognizer](https://fott-2-1.azurewebsites.net/).

1. Dans la page d’accueil de l’outil d’étiquetage des exemples, sélectionnez **Utiliser Disposition pour obtenir du texte, des tableaux et des marques de sélection**.

     :::image type="content" source="../media/label-tool/layout-1.jpg" alt-text="Paramètres de connexion pour l’outil Layout (Disposition) de Form Recognizer.":::

1. Dans le champ **Point de terminaison de service Form Recognizer**, collez le point de terminaison que vous avez obtenu avec votre abonnement Form Recognizer.

1. Dans le champ **Clé API**, collez la clé d’abonnement que vous avez obtenue avec votre ressource Form Recognizer.

1. Dans le champ **Source : URL**, collez l’URL `https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/layout-page-001.jpg` suivante et cliquez sur le bouton **Extraire**.

1. Sélectionnez **Exécuter la requête**. L’outil d’étiquetage des exemples Form Recognizer appelle alors l’API Analyze Layout (Analyser la disposition) et analyse le document.

    :::image type="content" source="../media/fott-layout.png" alt-text="Capture d’écran : fenêtre déroulante Disposition.":::

1. Afficher les résultats : consultez le texte extrait mis en évidence, les marques de sélection détectées et les tableaux détectés.

    :::image type="content" source="../media/label-tool/layout-3.jpg" alt-text="Paramètres de connexion pour l’outil Form Recognizer.":::

1. Téléchargez le fichier de sortie JSON pour afficher les résultats détaillés de la disposition.
     * Le nœud `readResults` contient chaque ligne de texte avec sa position de cadre englobant respectif dans la page.
     * Le nœud `selectionMarks` affiche chaque marque de sélection (case à cocher ou case d’option) et indique si son état est `selected` ou `unselected`.
     * La section `pageResults` comprend les tableaux extraits. Pour chaque tableau, le texte, l’index de ligne et de colonne, l’étendue de ligne et de colonne, le cadre englobant, etc. sont extraits.

## <a name="train-a-custom-form-model"></a>Entraîner un modèle de formulaire personnalisé

Effectuez l’apprentissage d’un modèle personnalisé pour analyser et extraire les données de formulaires et de documents spécifiques à votre entreprise. L’API est un programme de Machine Learning dont l’apprentissage a pour but de reconnaître les champs de formulaire dans vos contenus spécifiques et d’extraire des paires clé-valeur et des données de table. Vous avez seulement besoin de cinq exemples du même type de formulaire pour commencer. L’apprentissage de votre modèle personnalisé peut s’effectuer avec ou sans jeux de données étiquetés.

### <a name="prerequisites-for-training-a-custom-form-model"></a>Prérequis pour entraîner un modèle de formulaire personnalisé

* Un conteneur d’objets blob du Stockage Azure qui contient un jeu de données d’entraînement. Vérifiez que tous les documents d’entraînement ont le même format. Si vous avez des formulaires dans plusieurs formats, organisez-les en sous-dossiers en fonction du format. Pour ce projet, vous pouvez utiliser notre [exemple de jeu de données](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/sample_data_without_labels.zip).

* Configuration de CORS

    [CORS (Cross Origin Resource Sharing)](/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) doit être configuré sur votre compte Stockage Azure pour qu’il soit accessible à partir de Form Recognizer Studio. Pour configurer CORS dans le portail Azure, vous devez accéder au panneau CORS de votre compte de stockage.

    :::image type="content" source="../media/quickstarts/storage-cors-example.png" alt-text="Capture d’écran montrant la configuration de CORS pour un compte de stockage.":::

    1. Sélectionnez le panneau CORS pour le compte de stockage.

    1. Commencez par créer une entrée CORS dans le service BLOB.

    1. Affectez à **Origines autorisées** la valeur **https://formrecognizer.appliedai.azure.com** .

    1. Sélectionnez les 8 options disponibles pour **Méthodes autorisées**.

    1. Approuvez tous les **En-têtes autorisés** et tous les **En-têtes exposés** en entrant un * dans chaque champ.

    1. Affectez à **Âge maximal** la valeur 120 secondes ou toute autre valeur acceptable.

    1. Cliquez en haut de la page sur le bouton Enregistrer pour enregistrer les changements apportés.

    CORS doit à présent être configuré pour utiliser le compte de stockage à partir de Form Recognizer Studio.

### <a name="use-the-sample-labeling-tool"></a>Utiliser l’outil d’étiquetage des exemples

1. Accédez à l’[outil d’étiquetage des exemples Form Recognizer](https://fott-2-1.azurewebsites.net/).

1. Dans la page d’accueil de l’outil d’étiquetage des exemples, sélectionnez **Utiliser un formulaire personnalisé pour entraîner un modèle avec des étiquettes et obtenir des paires clé/valeur**.

    :::image type="content" source="../media/label-tool/custom-1.jpg" alt-text="Entraîner un modèle personnalisé.":::

1. Sélectionnez **Nouveau projet**

    :::image type="content" source="../media/fott-new-project.png" alt-text="Capture d’écran : invite sélectionner un nouveau projet.":::

#### <a name="create-a-new-project"></a>Création d'un projet

Configurez les **Paramètres du projet** en renseignant les champs avec les valeurs suivantes :

1. **Nom d’affichage**. Nommez votre projet.

1. **Jeton de sécurité**. Chaque projet génère automatiquement un jeton de sécurité qui peut être utilisé pour chiffrer/déchiffrer les paramètres de projet sensibles. Vous pouvez accéder aux jetons de sécurité dans les paramètres de l’application, en sélectionnant l’icône d’engrenage en bas de la barre de navigation gauche.

1. **Connexion source**. L’outil d’étiquetage des exemples se connecte à une source (vos formulaires chargés d’origine) et à une cible (données de sortie et étiquettes créées). Les connexions peuvent être configurées et partagées entre les projets. Elles utilisent un modèle de fournisseur extensible, ce qui vous permet d’ajouter facilement de nouveaux fournisseurs sources/cibles.

    * Créez une connexion en cliquant sur le bouton **Ajouter une connexion**. Renseignez les champs avec les valeurs suivantes :

    > [!div class="checklist"]
    >
    > * **Nom d’affichage**. Nommez la connexion.
    > * **Description**. Ajoutez une brève description.
    > * **URL SAS**. Collez l’URL de signature d’accès partagé (SAS) de votre conteneur Stockage Blob Azure.

    * Si vous souhaitez récupérer l’URL SAS pour vos données d’entraînement de modèle personnalisé, accédez à votre ressource de stockage dans le portail Azure, puis sélectionnez l’onglet **Explorateur Stockage**. Accédez à votre conteneur, cliquez avec le bouton droit, puis sélectionnez **Obtenir une signature d’accès partagé**. Il est important d’obtenir la signature d’accès partagé de votre conteneur, et non celle du compte de stockage. Vérifiez que les autorisations de **lecture**, d’**écriture**, de **suppression** et de **liste** sont cochées, puis cliquez sur **Créer**. Copiez ensuite la valeur de la section **URL** dans un emplacement temporaire. Il doit avoir le format : `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`.

       :::image type="content" source="../media/quickstarts/get-sas-url.png" alt-text="Emplacement SAS.":::

1. **Chemin d’accès au dossier** (facultatif).  Si vos formulaires sources se trouvent dans un dossier dans le conteneur d’objets Blob, spécifiez le nom du dossier.

1. **Form Recognizer Service Uri** (URI du service Form Recognizer) : votre URL de point de terminaison Form Recognizer.

1. **Clé API**. Votre clé d’abonnement Form Recognizer.

1. **Version d’API**. Conservez la valeur v2.1 (valeur par défaut).

1. **Description** (facultatif). Décrivez votre projet.

    :::image type="content" source="../media/label-tool/connections.png"  alt-text="Paramètres de connexion":::

#### <a name="label-your-forms"></a>Étiqueter vos formulaires

  :::image type="content" source="../media/label-tool/new-project.png"  alt-text="Page du nouveau projet":::

Quand vous créez ou ouvrez un projet, la fenêtre principale de l’éditeur d’étiquettes s’ouvre. L’éditeur d’étiquettes est composé de trois parties :

* Un volet de visualisation redimensionnable, qui contient une liste déroulante de formulaires à partir de la connexion source.
* Le volet principal de l’éditeur, qui vous permet d’appliquer des étiquettes.
* Le volet de l’éditeur d’étiquettes, qui permet aux utilisateurs de modifier, de verrouiller, de réorganiser et de supprimer des étiquettes.

##### <a name="identify-text-and-tables"></a>Identifier du texte et des tables

Sélectionnez **Run OCR on all files** (Exécuter l’OCR sur tous les fichiers) dans le volet gauche pour obtenir les informations sur la disposition du texte et des tables pour chaque document. L’outil d’étiquetage dessine des rectangles englobants autour de chaque élément de texte.

L’outil d’étiquetage indique aussi les tables qui ont été automatiquement extraites. Sélectionnez l’icône de table/grille à gauche du document pour voir la table extraite. Le contenu des tables étant automatiquement extrait, nous n’étiquèterons pas le contenu des tables et ferons confiance à l’extraction automatisée.

  :::image type="content" source="../media/label-tool/table-extraction.png" alt-text="Visualisation d’une table dans l’outil d’étiquetage des exemples.":::

##### <a name="apply-labels-to-text"></a>Appliquer des étiquettes à du texte

Vous allez ensuite créer des balises (étiquettes) et les appliquer aux éléments de texte que vous voulez que le modèle analyse. Notez que les exemples de données d’étiquette incluent déjà des champs étiquetés auxquels nous allons ajouter un autre champ.

Utilisez le volet de l’éditeur d’étiquettes pour créer une étiquette que vous souhaitez identifier :

1. Sélectionnez le signe plus **+** pour créer une nouvelle étiquette.

1. Entrez le nom « total » pour l’étiquette.

1. Appuyez sur **Entrée** pour enregistrer l’étiquette.

1. Dans l’éditeur principal, sélectionnez la valeur totale parmi les éléments de texte mis en évidence.

1. Sélectionnez l’étiquette Total que vous voulez appliquer à la valeur, ou appuyez sur la touche du clavier correspondante. Les touches numériques sont affectées comme touches d’accès rapide pour les 10 premières étiquettes. Vous pouvez réorganiser vos étiquettes à l’aide des icônes de flèches haut et bas dans le volet de l’éditeur d’étiquettes.

    > [!Tip]
    > Gardez à l’esprit les conseils suivants quand vous étiquetez vos formulaires :
    >
    > * Vous ne pouvez appliquer qu’une seule étiquette à chaque élément de texte sélectionné.
    >
    > * Chaque étiquette ne peut être appliquée qu’une seule fois par page. Si une valeur apparaît plusieurs fois sur le même formulaire, créez des étiquettes différentes pour chaque instance, par exemple « facture n° 1 », « facture n° 2 », etc.
    > * Les étiquettes ne peuvent pas s’étendre sur plusieurs pages.
    > * Étiquetez les valeurs telles qu’elles apparaissent sur le formulaire ; n’essayez pas de fractionner une valeur en deux parties avec deux étiquettes différentes. Par exemple, un champ d’adresse doit être étiqueté avec une étiquette unique, même s’il s’étend sur plusieurs lignes.
    > * N’incluez pas de clés dans vos champs étiquetés&mdash;uniquement les valeurs.
    > * Les données du tableau doivent être détectées automatiquement et seront disponibles dans le fichier JSON de sortie final dans la section « pageResults ». Toutefois, si le modèle ne parvient pas à détecter toutes les données de votre tableau, vous pouvez également étiqueter et entraîner un modèle pour détecter les tableaux. Consultez [Entraîner un modèle personnalisé | Étiqueter vos formulaires](../label-tool.md#label-your-forms)
    > * Servez-vous des boutons situés à droite de **+** pour rechercher, renommer, réorganiser et supprimer vos étiquettes.
    > * Pour supprimer une étiquette appliquée sans supprimer l’étiquette proprement dite, sélectionne lez rectangle étiqueté dans la vue du document et appuyez sur la touche de suppression.
    >

1. Poursuivez pour effectuer les étapes ci-dessus afin d’étiqueter les cinq formulaires de l’exemple de jeu de données.

  :::image type="content" source="../media/label-tool/custom-1.jpg" alt-text="Étiqueter les exemples.":::

#### <a name="train-a-custom-model"></a>Entraîner un modèle personnalisé

Choisissez l’icône d’entraînement dans le volet gauche pour ouvrir la page Training (Entraînement). Sélectionnez ensuite le bouton **Train** pour commencer l’entraînement du modèle. Une fois le processus d’entraînement terminé, les informations suivantes s’affichent :

* **Model ID** : ID du modèle qui a été créé et entraîné. Chaque appel d’entraînement crée un modèle avec son propre ID. Copiez cette chaîne dans un emplacement sûr. Vous en aurez besoin si vous souhaitez effectuer des appels de prédiction via l’[API REST](./try-sdk-rest-api.md?pivots=programming-language-rest-api) pi une [bibliothèque de client](./try-sdk-rest-api.md).

* **Average Accuracy** : justesse moyenne du modèle. Vous pouvez améliorer la justesse du modèle en étiquetant des formulaires supplémentaires et en effectuant un nouvel entraînement pour créer un modèle. Nous vous recommandons de commencer par étiqueter cinq formulaires en analysant et en testant les résultats, puis d’ajouter, si nécessaire, d’autres formulaires.
* Liste des étiquettes et justesse estimée par étiquette.

    :::image type="content" source="../media/label-tool/custom-3.jpg" alt-text="Outil d’affichage de l’entraînement.":::

#### <a name="analyze-a-custom-form"></a>Analyser un formulaire personnalisé

1. Sélectionnez l’icône **Analyser** (ampoule) située à gauche pour tester votre modèle. 

1. Sélectionnez un **fichier local** source et recherchez un fichier dans l’exemple de jeu de données que vous avez décompressez dans le dossier de test. 

1. Choisissez ensuite le bouton **Exécuter l’analyse** pour obtenir des paires clé/valeur, du texte et des prédictions de tableaux pour le formulaire. L’outil applique des étiquettes dans les cadres englobants et signale la confiance de chaque étiquette.

   :::image type="content" source="../media/analyze.png" alt-text="Vue de l’entraînement.":::

Et voilà ! Vous avez appris à utiliser l’outil d’étiquetage des exemples Form Recognizer pour les modèles prédéfinis, de mise en page et personnalisés du module Form Recognizer. Vous avez également appris à analyser un formulaire personnalisé avec des données étiquetées manuellement. Vous pouvez maintenant essayer le SDK de la bibliothèque de client ou l’API REST de Form Recognizer.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [ explorer le SDK de la bibliothèque de client Form Recognizer ou l’API REST](../quickstarts/get-started-sdk-rest-api.md)
