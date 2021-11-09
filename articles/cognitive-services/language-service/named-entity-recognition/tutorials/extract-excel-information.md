---
title: Extraire des informations dans Excel à l’aide de Power Automate
titleSuffix: Azure Cognitive Services
description: Découvrez comment extraire du texte Excel sans avoir à écrire du code, avec Reconnaissance d’entité nommée et Power Automate.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: article
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-ner, ignite-fall-2021
ms.openlocfilehash: a53ff3e69842bcd2f42e8463f3f84638f512450f
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131096712"
---
# <a name="extract-information-in-excel-using-named-entity-recognitionner-and-power-automate"></a>Extraire des informations dans Excel à l’aide de Reconnaissance d’entité nommée (NER) et Power Automate 

Dans ce tutoriel, vous allez créer un flux Power Automate pour extraire du texte dans une feuille de calcul Excel sans avoir à écrire du code. 

Ce flux prend une feuille de calcul des problèmes signalés à propos d’un immeuble d’habitation et les classifie en deux catégories : plomberie et autres. Il extrait également les noms et numéros de téléphone des locataires qui les ont envoyés. Enfin, le flux ajoute ces informations à la feuille Excel. 

Ce didacticiel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Utiliser Power Automate pour créer un flux
> * Charger des données Excel depuis OneDrive Entreprise
> * Extraire du texte d’Excel et l’envoyer pour Reconnaissance d’entité nommée (NER) 
> * Utiliser les informations de l’API pour mettre à jour une feuille Excel.

## <a name="prerequisites"></a>Prérequis

- Un compte Microsoft Azure [Créez un compte gratuit](https://azure.microsoft.com/free/cognitive-services/) ou [connectez-vous](https://portal.azure.com/).
- Une ressource de langue. Si vous n’en avez pas, vous pouvez [en créer une dans le portail Azure](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) et utiliser le niveau gratuit pour suivre ce tutoriel.
- [Clé et point de terminaison](../../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) générés automatiquement pendant l’inscription.
- Feuille de calcul contenant les problèmes des locataires. Des exemples de données sont fournis sur GitHub
- Microsoft 365, avec OneDrive Entreprise.

## <a name="add-the-excel-file-to-onedrive-for-business"></a>Ajouter le fichier Excel à OneDrive Entreprise

Téléchargez l’exemple de fichier Excel à partir de [GitHub](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/TextAnalytics/sample-data/ReportedIssues.xlsx). Ce fichier doit être stocké dans votre compte OneDrive Entreprise.

:::image type="content" source="../media/tutorials/excel/example-data.png" alt-text="Exemples provenant du fichier Excel" lightbox="../media/tutorials/excel/example-data.png":::

Les problèmes sont signalés en texte brut. Nous allons utiliser la fonctionnalité NER pour extraire le nom de la personne et le numéro de téléphone. Le flux recherche ensuite le mot « plomberie » dans la description pour catégoriser les problèmes. 

## <a name="create-a-new-power-automate-workflow"></a>Créer un workflow Power Automate

Accédez au [site Power Automate](https://preview.flow.microsoft.com/) et connectez-vous. Cliquez ensuite sur **Créer**, puis sur **Flux planifié**.

:::image type="content" source="../media/tutorials/excel/flow-creation.png" alt-text="Écran de la création du workflow" lightbox="../media/tutorials/excel/flow-creation.png":::

Dans la page **Créer un flux de cloud planifié**, initialisez votre flux avec les champs suivants :

|Champ |Valeur  |
|---------|---------|
|**Nom du flux**     | **Révision prévue** ou un autre nom.         |
|**Démarrage en cours**     |  Entrez les date et heure actuelles.       |
|**Intervalle de récurrence**     | **1 heure**        |

## <a name="add-variables-to-the-flow"></a>Ajouter des variables au flux

Créez des variables représentant les informations qui seront ajoutées au fichier Excel. Cliquez sur **Nouvelle étape** et recherchez **Initialiser la variable**. Effectuez cette opération quatre fois, pour créer quatre variables.

:::image type="content" source="../media/tutorials/excel/initialize-variables.png" alt-text="Étape d’initialisation des variables" lightbox="../media/tutorials/excel/initialize-variables.png":::


Ajoutez les informations suivantes aux variables que vous avez créées. Elles représentent les colonnes du fichier Excel. Si des variables sont réduites, vous pouvez cliquer dessus pour les développer.

| Action |Nom   | Type | Valeur |
|---------|---------|---|---|
| Initialiser la variable | var_person | String | Personne |
| Initialiser la variable 2 | var_phone | String | Numéro de téléphone |
| Initialiser la variable 3 | var_plumbing | String | plomberie |
| Initialiser la variable 4 | var_other | String | Autres | 

:::image type="content" source="../media/tutorials/excel/flow-variables.png" alt-text="informations contenues dans les variables de flux" lightbox="../media/tutorials/excel/flow-variables.png":::

## <a name="read-the-excel-file"></a>Lire le fichier Excel

Cliquez sur **Nouvelle étape** et tapez **Excel**, puis sélectionnez **Lister les lignes présentes dans un tableau** dans la liste des actions.

:::image type="content" source="../media/tutorials/excel/list-excel-rows.png" alt-text="Ajouter des lignes Excel dans le flux" lightbox="../media/tutorials/excel/list-excel-rows.png":::

Ajoutez le fichier Excel au flux en renseignant les champs de cette action. Ce tutoriel nécessite que le fichier soit chargé sur OneDrive Entreprise.

:::image type="content" source="../media/tutorials/excel/list-excel-rows-options.png" alt-text="Remplir les lignes Excel dans le flux" lightbox="../media/tutorials/excel/list-excel-rows-options.png":::

Cliquez sur **Nouvelle étape** et ajoutez une action **Appliquer à chacun**.

:::image type="content" source="../media/tutorials/excel/add-apply-action.png" alt-text="Ajouter une action Appliquer à chacun" lightbox="../media/tutorials/excel/add-apply-action.png":::

Cliquez sur **Sélectionnez un résultat à partir des étapes précédentes**. Dans la zone de contenu dynamique qui s’affiche, sélectionnez **valeur**.

:::image type="content" source="../media/tutorials/excel/select-output.png" alt-text="Sélectionner un résultat dans le fichier Excel" lightbox="../media/tutorials/excel/select-output.png":::

## <a name="send-a-request-for-entity-recognition"></a>Envoyer une demande de reconnaissance d’entité

Si ce n’est déjà fait, vous devez créer une [ressource Langue](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) dans le portail Azure.

### <a name="create-a-language-service-connection"></a>Créer une connexion au service Langue

Dans **Appliquer à chacun**, cliquez sur **Ajouter une action**. Accédez à la page relative **à la clé et au point de terminaison** de votre ressource Langue dans le portail Azure, et obtenez la clé et le point de terminaison pour votre ressource Langue.

Dans votre flux, entrez les informations suivantes pour créer une connexion à la ressource Langue.

> [!NOTE]
> Si vous avez déjà créé une connexion à la ressource Langue et voulez changer les détails de la connexion, cliquez sur les points de suspension dans le coin supérieur droit, puis sur **+ Ajouter une nouvelle connexion**.

| Champ           | Valeur                                                                                                             |
|-----------------|-------------------------------------------------------------------------------------------------------------------|
| Nom de connexion | Nom de la connexion à votre ressource Langue. Par exemple : `TAforPowerAutomate`. |
| Clé de compte     | Clé pour votre ressource Language.                                                                                   |
| URL du site        | Point de terminaison de votre ressource Langue.                                                       |

:::image type="content" source="../media/tutorials/excel/add-credentials.png" alt-text="Ajouter des informations d’identification de ressource Langue au flux" lightbox="../media/tutorials/excel/add-credentials.png":::


## <a name="extract-the-excel-content"></a>Extraire le contenu Excel 

Une fois la connexion créée, recherchez **Analyse de texte** et sélectionnez **Reconnaissance d’entité nommée**. Cela permet d’extraire des informations de la colonne de description du problème.

:::image type="content" source="../media/tutorials/excel/extract-info.png" alt-text="Extraire les entités de la feuille Excel" lightbox="../media/tutorials/excel/extract-info.png":::

Cliquez dans le champ **Texte** et sélectionnez **Description** dans la fenêtre de contenu dynamique qui s’affiche. Entrez `en` comme langue et un nom unique comme ID de document (vous devrez peut-être cliquer sur **Afficher les options avancées**).

:::image type="content" source="../media/tutorials/excel/description-from-dynamic-content.png" alt-text="Obtenir le texte de la colonne Description dans la feuille Excel" lightbox="../media/tutorials/excel/description-from-dynamic-content.png":::


Dans **Appliquer à chacun**, cliquez sur **Ajouter une action**, puis créez une autre action **Appliquer à chacun**. Cliquez dans la zone de texte et sélectionnez **Documents** dans la fenêtre de contenu dynamique qui s’affiche.

:::image type="content" source="../media/tutorials/excel/apply-to-each-documents.png" alt-text="Créez une autre action Appliquer à chacun." lightbox="../media/tutorials/excel/apply-to-each-documents.png":::


## <a name="extract-the-person-name"></a>Extraire le nom de la personne

Nous allons ensuite trouver le type d’entité de la personne dans la sortie NER. Dans **Appliquer à chacun 2**, cliquez sur **Ajouter une action**, puis créez une autre action **Appliquer à chacun**. Cliquez dans la zone de texte et sélectionnez **Entités** dans la fenêtre de contenu dynamique qui s’affiche.

:::image type="content" source="../media/tutorials/excel/add-apply-action-2.png" alt-text="Rechercher l’entité de la personne dans la sortie NER" lightbox="../media/tutorials/excel/add-apply-action-2.png":::


Dans la nouvelle action **Appliquer à chacun 3**, cliquez sur **Ajouter une action**, puis ajoutez un contrôle **Condition**.

:::image type="content" source="../media/tutorials/excel/create-condition.png" alt-text="Ajouter un contrôle Condition à l’action Appliquer à chacun 3" lightbox="../media/tutorials/excel/create-condition.png":::


Dans la fenêtre Condition, cliquez sur la première zone de texte. Dans la fenêtre de contenu dynamique, recherchez **Catégorie** et sélectionnez cette option.

:::image type="content" source="../media/tutorials/excel/choose-entities-value.png" alt-text="Ajouter la catégorie au contrôle Condition" lightbox="../media/tutorials/excel/choose-entities-value.png":::


Vérifiez que la seconde zone a la valeur **est égal à**. Sélectionnez ensuite la troisième zone, puis recherchez `var_person` dans la fenêtre de contenu dynamique. 

:::image type="content" source="../media/tutorials/excel/choose-variable-value.png" alt-text="Ajouter la variable person" lightbox="../media/tutorials/excel/choose-variable-value.png":::


Dans la condition **Si Oui**, tapez Excel, puis sélectionnez **Mettre à jour une ligne**.

:::image type="content" source="../media/tutorials/excel/yes-column-action.png" alt-text="Mettre à jour la condition Oui" lightbox="../media/tutorials/excel/yes-column-action.png":::

Entrez les informations Excel, puis mettez à jour les champs **Colonne clé**, **Valeur de la clé** et **PersonName**. Cela permet d’ajouter le nom détecté par l’API à la feuille Excel. 

:::image type="content" source="../media/tutorials/excel/yes-column-action-options.png" alt-text="Ajouter les informations Excel" lightbox="../media/tutorials/excel/yes-column-action-options.png":::

## <a name="get-the-phone-number"></a>Obtenir le numéro de téléphone

Réduisez l’action **Appliquer à chacun 3** en cliquant sur le nom. Ajoutez ensuite une autre action **Appliquer à chacun** à **Appliquer à chacun 2**, comme vous l’avez fait précédemment. Elle sera nommée **Appliquer à chacun 4**. Sélectionnez la zone de texte et ajoutez **Entités** comme sortie pour cette action. 

:::image type="content" source="../media/tutorials/excel/add-apply-action-phone.png" alt-text="Ajouter les entités de la sortie NER à une autre action Appliquer à chacun." lightbox="../media/tutorials/excel/add-apply-action-phone.png":::

Dans **Appliquer à chacun 4**, ajoutez un contrôle **Condition**. Il sera nommé **Condition 2**. Dans la première zone de texte, recherchez **Catégorie** et ajoutez des catégories à partir de la fenêtre de contenu dynamique. Vérifiez que la zone centrale a la valeur **est égal à**. Ensuite, dans la zone de texte de droite, entrez `var_phone`. 

:::image type="content" source="../media/tutorials/excel/condition-2-options.png" alt-text="Ajouter un deuxième contrôle Condition" lightbox="../media/tutorials/excel/condition-2-options.png":::

Dans la condition **Si Oui**, ajoutez une action **Mettre à jour une ligne**. Entrez ensuite les informations comme ci-dessus pour la colonne des numéros de téléphone de la feuille Excel. Cela permet d’ajouter le numéro de téléphone détecté par l’API à la feuille Excel. 

:::image type="content" source="../media/tutorials/excel/condition-2-yes-column.png" alt-text="Ajouter les informations Excel à la seconde condition Si Oui" lightbox="../media/tutorials/excel/condition-2-yes-column.png":::

## <a name="get-the-plumbing-issues"></a>Obtenir les problèmes de plomberie

Réduisez **Appliquer à chacun 4** en cliquant sur le nom. Créez ensuite une autre action **Appliquer à chacun** dans l’action parente. Sélectionnez la zone de texte et ajoutez **Entités** comme sortie pour cette action à partir de la fenêtre de contenu dynamique. 

:::image type="content" source="../media/tutorials/excel/add-apply-action-plumbing.png" alt-text="Créer une autre action Appliquer à chacun" lightbox="../media/tutorials/excel/add-apply-action-plumbing.png":::

Le flux vérifie ensuite si la description du problème à partir de la ligne du tableau Excel contient le mot « plomberie ». Si c’est le cas, il ajoute « plomberie » dans la colonne IssueType. Sinon, nous allons entrer « autre ».

Dans l’action **Appliquer à chacun 4**, ajoutez un contrôle **Condition**. Il sera nommé **Condition 3**. Dans la première zone de texte, recherchez et ajoutez **Description** à partir du fichier Excel en utilisant la fenêtre de contenu dynamique. Vérifiez que la zone centrale indique **contient**. Ensuite, dans la zone de texte de droite, recherchez et sélectionnez `var_plumbing`. 

:::image type="content" source="../media/tutorials/excel/condition-3-options.png" alt-text="Créer un autre contrôle Condition" lightbox="../media/tutorials/excel/condition-3-options.png":::

Dans la condition **Si Oui**, cliquez sur **Ajouter une action**, puis sélectionnez **Mettre à jour une ligne**. Entrez ensuite les informations comme précédemment. Dans la colonne IssueType, sélectionnez `var_plumbing`. Cela permet d’appliquer une étiquette « plomberie » à la ligne.

Dans la condition **Si Non**, cliquez sur **Ajouter une action**, puis sélectionnez **Mettre à jour une ligne**. Entrez ensuite les informations comme précédemment. Dans la colonne IssueType, sélectionnez `var_other`. Cela permet d’appliquer une étiquette « autres » à la ligne.

:::image type="content" source="../media/tutorials/excel/plumbing-issue-condition.png" alt-text="Ajouter des informations aux deux conditions" lightbox="../media/tutorials/excel/plumbing-issue-condition.png":::

## <a name="test-the-workflow"></a>Tester le flux de travail

Dans le coin supérieur droit de l’écran, cliquez sur **Enregistrer**, puis **Tester**. Sous **Tester le flux**, sélectionnez **manuellement**. Ensuite, cliquez sur **Tester** et sur **Exécuter le flux**.

Le fichier Excel sera mis à jour dans votre compte OneDrive. Il ressemble à ce qui suit.

:::image type="content" source="../media/tutorials/excel/updated-excel-sheet.png" alt-text="Tester le workflow et afficher la sortie" lightbox="../media/tutorials/excel/updated-excel-sheet.png":::

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Appeler NER à l’aide de l’API REST ou de la bibliothèque de client](../quickstart.md)
