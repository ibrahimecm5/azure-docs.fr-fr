---
title: Didacticiel-da Vinci plan net-Azure API pour FHIR
description: ce didacticiel vous guide dans la configuration du service FHIR dans l’API Azure pour FHIR afin de passer des tests Touchstone pour le Guide d’implémentation de la Exchange de données du payeur de vinci.
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: matjazl
ms.author: cavoeg
author: modillon
ms.date: 11/29/2021
ms.openlocfilehash: 84dd99b8712eda5cff23bd7702f1336598ab26df
ms.sourcegitcommit: 66b6e640e2a294a7fbbdb3309b4829df526d863d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/01/2021
ms.locfileid: "133361776"
---
# <a name="da-vinci-plan-net-for-azure-api-for-fhir"></a>Da Vinci plan net pour l’API Azure pour FHIR

Dans ce didacticiel, nous allons passer en revue la configuration du service FHIR dans Azure API pour FHIR afin de transmettre les tests [Touchstone](https://touchstone.aegis.net/touchstone/) pour le Guide d’implémentation de da Vinci PDEX payed Network (plan-net).

## <a name="touchstone-capability-statement"></a>Instruction de fonctionnalité Touchstone

Le premier test sur lequel nous allons nous concentrer consiste à tester l’API Azure pour FHIR à l’aide de l' [instruction de capacité da Vinci Plan-Net](https://touchstone.aegis.net/touchstone/testdefinitions?selectedTestGrp=/FHIRSandbox/DaVinci/FHIR4-0-1-Test/PDEX/PlanNet/00-Capability&activeOnly=false&contentEntry=TEST_SCRIPTS). Si vous exécutez ce test sans aucune mise à jour, le test échoue en raison de paramètres de recherche manquants et de profils manquants.

## <a name="define-search-parameters"></a>Définir les paramètres de recherche

Dans le cadre de la Plan-Net IG de Vinci, vous devez définir six [nouveaux paramètres de recherche](how-to-do-custom-search.md) pour les ressources service de santé, plan d’assurance, rôle du praticien, organisation et affiliation à l’organisation. Les six sont testés dans l’instruction de fonctionnalité :

* [Zone de couverture du service de santé](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/SearchParameter-healthcareservice-coverage-area.html)
* [Zone couverture du plan Insurance](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/SearchParameter-insuranceplan-coverage-area.html)
* [Type de plan de plan d’assurance](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/SearchParameter-insuranceplan-plan-type.html)
* [Zone de couverture de l’Organisation](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/SearchParameter-organization-coverage-area.html)
* [Réseau d’affiliation d’organisation](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/SearchParameter-organizationaffiliation-network.html)
* [Réseau du rôle praticien](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/SearchParameter-practitionerrole-network.html)

> [!NOTE]
> Dans le JSON brut pour ces paramètres de recherche, le nom est défini sur `Plannet_sp_<Resource Name>_<SearchParameter Name>` . Le test Touchstone s’attend à ce que le nom de ceux-ci soit uniquement `SearchParameter Name` (couverture-zone, plan-type ou réseau).

Le reste des paramètres de recherche nécessaires pour le da Vinci Plan-Net GI sont définis par la spécification de base et sont déjà disponibles dans l’API Azure pour FHIR sans aucune mise à jour supplémentaire.

## <a name="store-profiles"></a>Stocker les profils

En dehors de la définition des paramètres de recherche, vous devez charger les [profils et extensions requis](./validation-against-profiles.md#storing-profiles) pour réussir ce test. Neuf profils sont utilisés dans le cadre de la da Vinci Plan-Net GI :

* [Plan-point de terminaison net](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/StructureDefinition-plannet-Endpoint.html)
* [Plan-service net Healthcare](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/StructureDefinition-plannet-HealthcareService.html)
* [Plan-net InsurancePlan](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/StructureDefinition-plannet-InsurancePlan.html) 
* [Plan-emplacement réseau](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/StructureDefinition-plannet-Location.html)
* [Plan-réseau net](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/StructureDefinition-plannet-Network.html)
* [Plan-organisation net](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/StructureDefinition-plannet-Organization.html)
* [Plan-net OrganizationAffiliation](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/StructureDefinition-plannet-OrganizationAffiliation.html)
* [Plan-praticien du réseau](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/StructureDefinition-plannet-Practitioner.html)
* [Plan-net PractitionerRole](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/StructureDefinition-plannet-PractitionerRole.html)

## <a name="sample-rest-file"></a>Exemple de fichier Rest

Pour faciliter la création de ces paramètres de recherche et de ces profils, nous avons un exemple de fichier http sur le site Open source qui comprend toutes les étapes décrites ci-dessus dans un fichier unique. Une fois que vous avez téléchargé tous les profils et paramètres de recherche nécessaires, vous pouvez exécuter le test d’instruction de fonctionnalité dans Touchstone.

:::image type="content" source="media/davinci-plan-net/davinci-plan-net-test-script-execution-passed.png" alt-text="Exemple de script d’exécution de test Rest du plan da Vinci":::

## <a name="touchstone-error-handling-test"></a>Test de gestion des erreurs Touchstone

Le deuxième test que nous allons examiner est le test de la [gestion des erreurs](https://touchstone.aegis.net/touchstone/testdefinitions?selectedTestGrp=/FHIRSandbox/DaVinci/FHIR4-0-1-Test/PDEX/PlanNet/01-Error-Codes&activeOnly=false&contentEntry=TEST_SCRIPTS). La seule étape que vous devez effectuer consiste à supprimer une ressource HealthcareService de votre base de données et à utiliser l’ID de la ressource HealthcareService supprimée dans le test. L’exemple de fichier [DaVinci_PlanNet. http](https://github.com/microsoft/fhir-server/blob/main/docs/rest/DaVinciPlanNet/DaVinci_PlanNet.http) dans le site Open source fournit un exemple de HealthcareService à poster et à supprimer pour cette étape.

:::image type="content" source="media/davinci-plan-net/davinci-test-script-execution-passed.png" alt-text="Le script d’exécution de test de l’erreur net Touchstone du plan da Vinci a réussi":::

## <a name="touchstone-query-test"></a>Test de requête Touchstone

Le prochain test que nous allons examiner est le [test des fonctionnalités de requête](https://touchstone.aegis.net/touchstone/testdefinitions?selectedTestGrp=/FHIRSandbox/DaVinci/FHIR4-0-1-Test/PDEX/PlanNet/03-Query&activeOnly=false&contentEntry=TEST_SCRIPTS). Ce test teste la conformité par rapport aux profils que vous avez chargés dans le premier test. Vous devez charger les ressources qui se conforment aux profils. Le meilleur chemin d’accès consiste à tester les ressources que vous avez déjà dans votre base de données, mais nous avons également le fichier [DaVinci_PlanNet_Sample_Resources. http](https://github.com/microsoft/fhir-server/blob/main/docs/rest/DaVinciPlanNet/DaVinci_PlanNet_Sample_Resources.http) avec des exemples de ressources tirés des exemples de la IG que vous pouvez utiliser pour créer les ressources et tester.  

:::image type="content" source="media/davinci-plan-net/touchstone-query-test-execution-failed.png" alt-text="Échec du test de requête réseau du plan de Vinci da":::

> [!NOTE]
> Avec les exemples de ressources fournis, vous devez vous attendre à un taux de réussite de 98% des tests de requête.
> il y a un problème d’GitHub ouvert sur le serveur FHIR qui entraîne l’échec de l’un de ces tests.
La ressource a été retournée plusieurs fois si elle répond à la fois à des critères de base et à des critères de _include. [#2037](https://github.com/microsoft/fhir-server/issues/2037) 

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, nous avons passé en revue la configuration de l’API Azure pour FHIR afin de réussir les tests Touchstone pour le Guide d’implémentation de da Vinci PDEX payeur réseau (plan-net). Pour plus d’informations sur les fonctionnalités prises en charge dans l’API Azure pour FHIR, consultez

>[!div class="nextstepaction"]
>[Fonctionnalités prises en charge](fhir-features-supported.md)
