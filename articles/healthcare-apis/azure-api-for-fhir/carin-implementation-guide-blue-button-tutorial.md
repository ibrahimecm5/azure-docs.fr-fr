---
title: Guide d’implémentation du bouton bleu CARIN pour le bouton bleu-API Azure pour FHIR
description: Ce didacticiel vous guide tout au long des étapes de configuration de l’API Azure pour FHIR afin de passer les tests Touchstone du Guide d’implémentation CARIN pour le bouton bleu (C4BB GI).
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: matjazl
ms.author: cavoeg
author: caitlinv39
ms.date: 11/29/2021
ms.openlocfilehash: e89cc67df87d69d447dd4110c9bca5516fd5c464
ms.sourcegitcommit: 66b6e640e2a294a7fbbdb3309b4829df526d863d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/01/2021
ms.locfileid: "133361788"
---
# <a name="carin-implementation-guide-for-blue-button174-for-azure-api-for-fhir"></a>Guide d’implémentation de CARIN pour le bouton bleu&#174; pour l’API Azure pour FHIR

Dans ce didacticiel, nous allons passer en revue la configuration de l’API Azure pour FHIR afin de passer les tests [Touchstone](https://touchstone.aegis.net/touchstone/) du [Guide d’implémentation Carin pour le bouton bleu](https://build.fhir.org/ig/HL7/carin-bb/index.html) (C4BB GI).

## <a name="touchstone-capability-statement"></a>Instruction de fonctionnalité Touchstone

Le premier test sur lequel nous allons nous concentrer consiste à tester l’API Azure pour FHIR à l’aide de l' [instruction de fonctionnalité C4BB GI](https://touchstone.aegis.net/touchstone/testdefinitions?selectedTestGrp=/FHIRSandbox/CARIN/CARIN-4-BlueButton/00-Capability&activeOnly=false&contentEntry=TEST_SCRIPTS). Si vous exécutez ce test sur l’API Azure pour FHIR sans aucune mise à jour, le test échoue en raison de paramètres de recherche manquants et de profils manquants. 

### <a name="define-search-parameters"></a>Définir les paramètres de recherche

Dans le cadre de C4BB GI, vous devez définir trois [nouveaux paramètres de recherche](how-to-do-custom-search.md) pour la `ExplanationOfBenefit` ressource. Deux d’entre eux sont testés dans l’instruction Capability (type et service-date) et un est nécessaire pour les `_include` recherches (assureur).  

* [type](https://build.fhir.org/ig/HL7/carin-bb/SearchParameter-explanationofbenefit-type.json)
* [service-date](https://build.fhir.org/ig/HL7/carin-bb/SearchParameter-explanationofbenefit-service-date.json)
* [assureur](https://build.fhir.org/ig/HL7/carin-bb/SearchParameter-explanationofbenefit-insurer.json)

> [!NOTE]
> Dans le JSON brut pour ces paramètres de recherche, le nom est défini sur `ExplanationOfBenefit_<SearchParameter Name>` . Le test Touchstone s’attend à ce que le nom soit **type**, **service-date** et **assureur**.  
 
Le reste des paramètres de recherche nécessaires pour C4BB GI est défini par la spécification de base et est déjà disponible dans l’API Azure pour FHIR sans aucune mise à jour supplémentaire.
 
### <a name="store-profiles"></a>Stocker les profils

En dehors de la définition des paramètres de recherche, l’autre mise à jour que vous devez effectuer pour réussir ce test consiste à charger les [profils requis](validation-against-profiles.md). Huit profils sont définis dans C4BB GI. 

* [Couverture C4BB](https://build.fhir.org/ig/HL7/carin-bb/StructureDefinition-C4BB-Coverage.html) 
* [C4BB ExplanationOfBenefit inpatient](https://build.fhir.org/ig/HL7/carin-bb/StructureDefinition-C4BB-ExplanationOfBenefit-Inpatient-Institutional.html) 
* [C4BB ExplanationOfBenefit](https://build.fhir.org/ig/HL7/carin-bb/StructureDefinition-C4BB-ExplanationOfBenefit-Outpatient-Institutional.html) 
* [Pharmacie C4BB ExplanationOfBenefit](https://build.fhir.org/ig/HL7/carin-bb/StructureDefinition-C4BB-ExplanationOfBenefit-Pharmacy.html) 
* [C4BB ExplanationOfBenefit Professional](https://build.fhir.org/ig/HL7/carin-bb/StructureDefinition-C4BB-ExplanationOfBenefit-Professional-NonClinician.html) 
* [Organisation C4BB](https://build.fhir.org/ig/HL7/carin-bb/StructureDefinition-C4BB-Organization.html) 
* [Patient C4BB](https://build.fhir.org/ig/HL7/carin-bb/StructureDefinition-C4BB-Patient.html) 
* [C4BB-praticien](https://build.fhir.org/ig/HL7/carin-bb/StructureDefinition-C4BB-Practitioner.html) 

### <a name="sample-rest-file"></a>Exemple de fichier Rest

Pour faciliter la création de ces paramètres de recherche et de ces profils, nous avons un [exemple de fichier http](https://github.com/microsoft/fhir-server/blob/main/docs/rest/C4BB/C4BB.http) qui comprend toutes les étapes décrites ci-dessus dans un fichier unique. Une fois que vous avez téléchargé tous les profils et paramètres de recherche nécessaires, vous pouvez exécuter le test d’instruction de fonctionnalité dans Touchstone.

:::image type="content" source="media/cms-tutorials/capability-test-script-execution-results.png" alt-text="Résultats du test de capacité exécution du script.":::

## <a name="touchstone-read-test"></a>Test de lecture Touchstone

Après avoir testé l’instruction de fonctionnalités, nous testerons les [capacités de lecture](https://touchstone.aegis.net/touchstone/testdefinitions?selectedTestGrp=/FHIRSandbox/CARIN/CARIN-4-BlueButton/01-Read&activeOnly=false&contentEntry=TEST_SCRIPTS) dans l’API Azure pour FHIR sur C4BB GI. Ce test teste la conformité par rapport aux huit profils que vous avez chargés dans le premier test. Vous devez charger les ressources qui se conforment aux profils. Le meilleur moyen consiste à tester les ressources que vous avez déjà dans votre base de données, mais nous avons également un [fichier http](https://github.com/microsoft/fhir-server/blob/main/docs/rest/C4BB/C4BB_Sample_Resources.http) disponible avec les exemples de ressources tirés des exemples de la IG que vous pouvez utiliser pour créer les ressources et tester.

:::image type="content" source="media/cms-tutorials/test-execution-results-touchstone.png" alt-text="Touchstone lit les résultats de l’exécution des tests.":::

## <a name="touchstone-eob-query-test"></a>Test de requête Touchstone EOB

Le prochain test que nous allons examiner est le [test de requête EOB](https://touchstone.aegis.net/touchstone/testdefinitions?selectedTestGrp=/FHIRSandbox/CARIN/CARIN-4-BlueButton/02-EOBQuery&activeOnly=false&contentEntry=TEST_SCRIPTS). Si vous avez déjà effectué le test de lecture, vous avez toutes les données chargées dont vous avez besoin. Ce test permet de vérifier que vous pouvez rechercher des `Patient` ressources et spécifiques `ExplanationOfBenefit` à l’aide de différents paramètres.

:::image type="content" source="media/cms-tutorials/test-execution-touchstone-eob-query-test.png" alt-text="Résultats de l’exécution de la requête Touchstone EOB.":::

## <a name="touchstone-error-handling-test"></a>Test de gestion des erreurs Touchstone

Le test final que nous allons examiner est le test de la [gestion des erreurs](https://touchstone.aegis.net/touchstone/testdefinitions?selectedTestGrp=/FHIRSandbox/CARIN/CARIN-4-BlueButton/99-ErrorHandling&activeOnly=false&contentEntry=TEST_SCRIPTS). La seule étape consiste à supprimer une ressource ExplanationOfBenefit de votre base de données et à utiliser l’ID de la ressource supprimée `ExplanationOfBenefit` dans le test.

:::image type="content" source="media/cms-tutorials/test-execution-touchstone-error-handling.png" alt-text="Résultats de la gestion des erreurs Touchstone EOB.":::


## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, nous avons parcouru comment passer le CARIN GI pour les tests de bouton bleu dans Touchstone. Ensuite, vous pouvez examiner comment tester les tests de la formule da Vinci.

>[!div class="nextstepaction"]
>[DaVinci médicament Formulas](davinci-drug-formulary-tutorial.md)       
 
