---
title: 'Démarrage rapide : Form Recognizer Studio | Préversion'
titleSuffix: Azure Applied AI Services
description: Traitement, extraction des données et analyse des formulaires et des documents à l’aide de Form Recognizer Studio (préversion)
author: sanjeev3
manager: netahw
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 09/14/2021
ms.author: sajagtap
ms.custom: ignite-fall-2021
ms.openlocfilehash: dc4f54a6ab42089068c3db8b7fe64bfada59adf5
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132290682"
---
# <a name="get-started-form-recognizer-studio--preview"></a>Bien démarrer : Form Recognizer Studio | Préversion

>[!NOTE]
> Form Recognizer Studio est actuellement en préversion publique. Certaines fonctionnalités peuvent ne pas être prises en charge ou avoir des capacités limitées. 

[Form Recognizer Studio (préversion)](https://formrecognizer.appliedai.azure.com/) est un outil en ligne permettant d’explorer, de comprendre et d’intégrer visuellement des fonctionnalités du service Form Recognizer dans vos applications. Commencez à explorer les modèles préentraînés avec des exemples de documents ou vos propres documents. Créez des projets pour générer des modèles de formulaire personnalisés et référencez ces modèles dans vos applications à l’aide du [kit SDK Python (préversion)](try-v3-python-sdk.md) et d’autres guides de démarrage rapide.

## <a name="migrating-from-the-sample-labeling-tool"></a>Migration à partir de l’outil d’étiquetage des exemples

Si vous avez déjà utilisé l’[outil d’étiquetage des exemples](try-sample-label-tool.md), ignorez les prérequis et [**connectez-vous à la préversion de Studio**](try-v3-form-recognizer-studio.md#sign-into-the-form-recognizer-studio-preview) pour utiliser votre compte Azure existant et les ressources Form Recognizer ou Cognitive Services avec Studio. 

Pour migrer vos projets personnalisés existants vers Studio, passez directement à la section [**Bien démarrer avec un modèle personnalisé**](try-v3-form-recognizer-studio.md#custom-model-basics) pour créer un projet et le pointer vers le même emplacement de stockage Blob Azure, en supposant que vous y ayez accès dans Azure. Une fois que vous avez configuré un nouveau projet, Studio charge tous les documents et les fichiers intermédiaires pour l’étiquetage et l’entraînement.

## <a name="minimum-prerequisites-for-new-users"></a>Prérequis minimum pour les nouveaux utilisateurs

* Un [**compte Azure**](https://azure.microsoft.com/free/cognitive-services/) actif.  Si vous n’en avez pas, vous pouvez [**créer un compte gratuit**](https://azure.microsoft.com/free/).
* Une ressource [**Form Recognizer**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) ou [**multiservice Cognitive Services**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne) dans le portail Azure.

## <a name="additional-prerequisites-for-custom-projects"></a>Prérequis supplémentaires pour les projets personnalisés

En plus du compte Azure et d’une ressource Form Recognizer ou Cognitive Services, vous avez besoin des éléments suivants :

### <a name="azure-blob-storage-container"></a>Conteneur de stockage d'objets blob Azure

Un [**compte de Stockage blob Azure**](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM) de **performances standard**. Vous allez créer des conteneurs pour stocker et organiser vos données d’objet blob dans votre compte de stockage. Si vous ignorez comment créer un compte de stockage Azure avec un conteneur, suivez les démarrages rapides suivants :

  * [**Créer un compte de stockage**](../../../storage/common/storage-account-create.md). Lorsque vous créez votre compte de stockage, veillez à sélectionner performance **Standard** dans le champ **Détails de l’instance → Performance**.
  * [**Créer un conteneur**](../../../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container). Lors de la création de votre conteneur, définissez le champ **Niveau d’accès public** sur **Conteneur** (accès en lecture anonyme pour les conteneurs et les objets blob) dans la fenêtre **Nouveau conteneur** .

### <a name="configure-cors"></a>Configuration de CORS

[CORS (Cross Origin Resource Sharing)](/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) doit être configuré sur votre compte Stockage Azure pour qu’il soit accessible à partir de Form Recognizer Studio. Pour configurer CORS dans le portail Azure, vous devez accéder au panneau CORS de votre compte de stockage.

:::image type="content" source="../media/quickstarts/cors-updated-image.png" alt-text="Capture d’écran montrant la configuration de CORS pour un compte de stockage.":::

1. Sélectionnez le panneau CORS pour le compte de stockage.
2. Commencez par créer une entrée CORS dans le service BLOB.
3. Affectez à **Origines autorisées** la valeur **https://formrecognizer.appliedai.azure.com** .
4. Sélectionnez les 8 options disponibles pour **Méthodes autorisées**.
5. Approuvez tous les **En-têtes autorisés** et tous les **En-têtes exposés** en entrant un * dans chaque champ.
6. Affectez à **Âge maximal** la valeur 120 secondes ou toute autre valeur acceptable.
7. Cliquez en haut de la page sur le bouton Enregistrer pour enregistrer les changements apportés.

CORS doit à présent être configuré pour utiliser le compte de stockage à partir de Form Recognizer Studio.

### <a name="sample-documents-set"></a>Ensemble d’exemples de documents

1. Accédez au [portail Azure](https://ms.portal.azure.com/#home) et naviguez comme suit : **votre compte de stockage** → **Stockage des données** → **Conteneurs**

   :::image border="true" type="content" source="../media/sas-tokens/data-storage-menu.png" alt-text="Capture d’écran : menu de stockage des données dans le portail Azure.":::

1. Sélectionnez un **conteneur** dans la liste.

1. Sélectionnez **Télécharger** dans le menu en haut de la page.

    :::image border="true" type="content" source="../media/sas-tokens/container-upload-button.png" alt-text="Capture d’écran : bouton de chargement du conteneur dans le portail Azure.":::

1. La fenêtre **Télécharger l’objet blob** s’affiche.

1. Sélectionnez le ou les fichiers à charger.

    :::image border="true" type="content" source="../media/sas-tokens/upload-blob-window.png" alt-text="Capture d’écran : fenêtre télécharger l’objet blob dans le portail Azure.":::

> [!NOTE]
> Par défaut, Studio utilisera uniquement les formulaires situés à la racine de votre conteneur. Toutefois, vous pouvez utiliser des données organisées en dossiers si elles sont spécifiées dans les étapes de création du projet de formulaire personnalisé. *Consultez*[**Organiser vos données dans des sous-dossiers**](../build-training-data-set.md#organize-your-data-in-subfolders-optional)

## <a name="sign-into-the-form-recognizer-studio-preview"></a>Se connecter à Form Recognizer Studio (préversion)

Une fois que vous avez terminé les étapes préalables requises, accédez à [Form Recognizer Studio (préversion)](https://formrecognizer.appliedai.azure.com).

1. Sélectionnez une fonctionnalité du service Form Recognizer dans la page d’accueil de Studio.

1. Sélectionnez votre abonnement Azure, votre groupe de ressources et votre ressource. (Vous pouvez modifier les ressources à tout moment en sélectionnant « Paramètres » dans le menu supérieur.)

1. Passez en revue et confirmez vos sélections.

:::image border="true" type="content" source="../media/quickstarts/form-recognizer-studio-get-started-v2.gif" alt-text="Exemple de démarrage avec Form Recognizer Studio":::

## <a name="layout"></a>Layout

En mode Mise en page :

1. Sélectionnez la commande Analyser pour exécuter l’analyse de la disposition sur l’exemple de document ou essayez votre document en utilisant la commande Ajouter.

1. Observez le texte extrait mis en surbrillance, les icônes de tableau représentant les emplacements de tableau extraits et les marques de sélection mises en surbrillance.

1. Utilisez les contrôles au bas de l’écran pour effectuer un zoom avant et arrière et faire pivoter l’affichage du document.

1. Affichez et masquez les couches de texte, de tableaux et de marques de sélection pour vous concentrer sur chacune d’elles séparément.

1. Sous l’onglet Résultat de la section de sortie, parcourez la sortie JSON pour comprendre le format de la réponse du service. Copiez et téléchargez l’intégration JumpStart.

:::image border="true" type="content" source="../media/quickstarts/layout-get-started-v2.gif" alt-text="Exemple de disposition Form Recognizer":::

## <a name="prebuilt-models"></a>Modèles prédéfinis

Vous avez le choix entre plusieurs modèles prédéfinis, chacun ayant son propre ensemble de champs pris en charge. Le modèle à utiliser pour l’opération d’analyse dépend du type de document à analyser. Voici les modèles prédéfinis actuellement pris en charge par le service Form Recognizer :

* [🆕 **Document général**](https://formrecognizer.appliedai.azure.com/studio/prebuilt?formType=document) – Analysez et extrayez le texte, les tableaux, la structure, les paires clé-valeur et les entités nommées.|
* [**Facture**](https://formrecognizer.appliedai.azure.com/studio/prebuilt?formType=invoice) : extrait le texte, les marques de sélection, les tableaux, les paires clé-valeur et les informations clés des factures.
* [**Reçu**](https://formrecognizer.appliedai.azure.com/studio/prebuilt?formType=receipt) : extrait le texte et les informations clés des reçus.
* [**Document d’identité**](https://formrecognizer.appliedai.azure.com/studio/prebuilt?formType=idDocument) : extrait le texte et les informations clés des permis de conduire et des passeports internationaux.
* [**Carte de visite**](https://formrecognizer.appliedai.azure.com/studio/prebuilt?formType=businessCard) : extrait le texte et les informations clés des cartes de visite.

Dans la vue Prédéfinis :

1. Dans la page d’accueil de Studio, sélectionnez l’un des modèles prédéfinis. Dans cet exemple, nous utilisons le modèle Facture.

1. Sélectionnez la commande Analyser pour exécuter l’analyse sur l’exemple de document ou essayez de traiter votre facture en utilisant la commande Ajouter.

1. Dans la section de visualisation, observez les champs et les valeurs encadrés et les éléments de ligne de la facture. Tous les tableaux et le texte extraits sont également affichés.

1. Sous l’onglet Champs de la section de sortie, notez les champs et les valeurs listés, et sélectionnez les éléments de ligne à afficher dans un format de type tableau.

1. Sous l’onglet Résultat de la section de sortie, parcourez la sortie JSON pour comprendre le format de la réponse du service. Copiez et téléchargez l’intégration JumpStart.

:::image border="true" type="content" source="../media/quickstarts/prebuilt-get-started-v2.gif" alt-text="Exemple de vue Prédéfinis de Form Recognizer":::

## <a name="custom-model-basics"></a>Concepts de base des modèles personnalisés

### <a name="getting-started"></a>Prise en main

Pour créer des modèles personnalisés, vous commencez par configurer votre projet :

1. Dans la page d’accueil de Studio, sélectionnez le [projet Formulaire personnalisé](https://formrecognizer.appliedai.azure.com/studio/customform/projects) pour ouvrir la page d’accueil Formulaire personnalisé.

1. Utilisez la commande « Créer un projet » pour démarrer l’Assistant Nouvelle configuration de projet.

1. Entrez les détails du projet, sélectionnez l’abonnement Azure et la ressource, ainsi que le conteneur de stockage Blob Azure qui contient vos données.

1. Passez en revue et envoyez vos paramètres pour créer le projet.

:::image border="true" type="content" source="../media/quickstarts/1-custom-model-get-started-v2.gif" alt-text="Exemple de démarrage avec un projet personnalisé Form Recognizer":::

### <a name="basic-flow"></a>Flux de base

Après l’étape de création du projet, dans la phase de modèle personnalisé :

1. Dans la vue d’étiquetage, définissez les étiquettes et leurs types que vous souhaitez extraire.

1. Sélectionnez le texte dans le document et cliquez sur l’étiquette dans la liste déroulante ou dans le volet des étiquettes.

1. Étiquetez quatre documents supplémentaires pour obtenir au moins cinq documents étiquetés.

1. Sélectionnez la commande Entraîner, puis entrez le nom et la description du modèle pour commencer l’entraînement de votre modèle personnalisé.

1. Une fois que le modèle est prêt, utilisez la commande Tester pour le valider avec vos documents de test et observer les résultats.

:::image border="true" type="content" source="../media/quickstarts/2-custom-model-basic-steps-v2.gif" alt-text="Exemple de flux de travail élémentaire de projet personnalisé Form Recognizer":::

### <a name="other-features"></a>Autres fonctionnalités

Vous pouvez aussi voir tous vos modèles avec l’onglet Modèles sur la gauche. Dans la vue Liste, sélectionnez un ou des modèles pour effectuer les actions suivantes :

1. Testez le modèle à partir de la vue Liste.

1. Utilisez la commande Supprimer pour supprimer les modèles qui ne sont pas requis.

1. Téléchargez les détails du modèle pour les voir hors connexion.

1. Sélectionnez plusieurs modèles et combinez-les dans un nouveau modèle à utiliser dans vos applications.

## <a name="labeling-as-tables"></a>Étiquetage en tant que tableaux

Durant la création de vos modèles personnalisés, vous devrez peut-être extraire des collections de données de vos documents. Celles-ci peuvent apparaître sous plusieurs formats. Utilisation de tables en tant que modèle visuel :

* Nombre dynamique ou variable de valeurs (lignes) pour un ensemble donné de champs (colonnes)

* Collection spécifique de valeurs pour un ensemble donné de champs (colonnes et/ou lignes)

### <a name="label-as-dynamic-table"></a>Étiqueter en tant que tableau dynamique

Utilisez des tables dynamiques afin d’extraire le nombre variable de valeurs (lignes) pour un ensemble donné de champs (colonnes) :

1. Ajoutez une nouvelle étiquette de type « Tableau », sélectionnez le type « Tableau dynamique » et nommez votre étiquette.

1. Ajoutez le nombre de colonnes (champs) et de lignes (pour les données) dont vous avez besoin.

1. Sélectionnez le texte dans votre page, puis cliquez sur la cellule à affecter au texte. Répétez cette opération pour toutes les lignes et toutes les colonnes de toutes les pages de tous les documents.

:::image border="true" type="content" source="../media/quickstarts/custom-tables-dynamic.gif" alt-text="Exemple d’étiquetage en tant que tableau dynamique dans Form Recognizer":::

### <a name="label-as-fixed-table"></a>Étiqueter en tant que tableau fixe

Utilisez des tables fixes afin d’extraire une collection spécifique de valeurs pour un ensemble donné de champs (colonnes et/ou lignes) :

1. Créez une nouvelle étiquette de type « Tableau », sélectionnez le type « Tableau fixe » et nommez-le.

1. Ajoutez le nombre de colonnes et de lignes dont vous avez besoin qui doivent correspondre aux deux ensembles de champs.

1. Sélectionnez le texte dans votre page, puis cliquez sur la cellule pour l’affecter au texte. Répétez cette opération pour les autres documents.

:::image border="true" type="content" source="../media/quickstarts/custom-tables-fixed.gif" alt-text="Exemple d’étiquetage en tant que tableau fixe dans Form Recognizer":::

## <a name="labeling-for-signature-detection"></a>Étiquetage pour la détection de signature

Pour étiqueter pour une détection de signature :

1. Dans la vue d’étiquetage, créez une nouvelle étiquette de type « Signature » et nommez-la.

1. Utilisez la commande Région pour créer une zone rectangulaire à l’emplacement attendu de la signature.

1. Sélectionnez la région dessinée et cliquez sur l’étiquette de type Signature pour l’affecter à votre région dessinée. Répétez cette opération pour les autres documents.

:::image border="true" type="content" source="../media/quickstarts/custom-signature.gif" alt-text="Exemple d’étiquetage pour la détection de signature dans Form Recognizer":::

## <a name="next-steps"></a>Étapes suivantes

* Suivez notre [**Guide de migration Form Recognizer v3.0**](../v3-migration-guide.md) pour découvrir les différences par rapport à la version antérieure de l’API REST.
* Explorez nos [**guides de démarrage rapide des kits SDK (préversion)**](try-v3-python-sdk.md) pour tester les fonctionnalités en préversion dans vos applications à l’aide des nouveaux kits SDK.
* Consultez nos [**guides de démarrage rapide de l’API REST (préversion)**](try-v3-rest-api.md) pour tester les fonctionnalités en préversion à l’aide de la nouvelle API REST.

[Bien démarrer avec Form Recognizer Studio (préversion)](https://formrecognizer.appliedai.azure.com)
