---
title: Didacticiel-da Vinci PDex-API de santé Azure (version préliminaire)
description: ce didacticiel explique comment configurer le service FHIR pour passer des tests pour le Guide d’implémentation de l’Exchange de données du payeur de vinci Da.
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.author: cavoeg
author: caitlinv39
ms.date: 11/12/2021
ms.openlocfilehash: 12f7cbac225cfe4e85d2df21c363d4c08fb0b4c6
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132494452"
---
# <a name="da-vinci-pdex"></a>Da Vinci PDex

> [!IMPORTANT]
> Les API Azure Healthcare sont actuellement en version préliminaire. L’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) contient des conditions légales supplémentaires qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou pas encore en disponibilité générale.

dans ce didacticiel, nous allons passer en revue la configuration du service FHIR dans les api de santé Azure (par le biais du service FHIR) pour transmettre les tests [Touchstone](https://touchstone.aegis.net/touchstone/) pour le Guide d’implémentation de l' [Exchange de données du payeur de vinci Da](http://hl7.org/fhir/us/davinci-pdex/toc.html) (PDex gi).

> [!NOTE]
> Le service FHIR prend uniquement en charge JSON. Le service FHIR Open source de Microsoft prend en charge JSON et XML, et dans Open source, vous pouvez utiliser le paramètre _format pour afficher l’instruction de fonctionnalité XML : `GET {fhirurl}/metadata?_format=xml`

## <a name="touchstone-capability-statement"></a>Instruction de fonctionnalité Touchstone

Le premier ensemble de tests sur lequel nous allons nous concentrer consiste à tester le service FHIR par rapport à l’instruction de fonctionnalité PDex GI. Cela comprend trois tests :

* Le premier test valide l’instruction de fonctionnalité de base par rapport aux exigences de l’IG et passera sans aucune mise à jour.

* Le deuxième test valide tous les profils qui ont été ajoutés pour le Centre des États-Unis. Ce test passera sans mises à jour, mais inclura un ensemble d’avertissements. Pour supprimer ces avertissements, vous devez [charger les profils de base US](validation-against-profiles.md). Nous avons créé un [exemple de fichier http](https://github.com/microsoft/fhir-server/blob/main/docs/rest/PayerDataExchange/USCore.http) qui vous guide dans la création de tous les profils. Vous pouvez également obtenir les [profils](http://hl7.org/fhir/us/core/STU3.1.1/profiles.html#profiles) directement à partir du site HL7, qui disposera des versions les plus récentes.

* Le troisième test vérifie que l' [opération $patient-Everything](patient-everything.md) est prise en charge.

:::image type="content" source="media/centers-medicare-services-tutorials/davinci-pdex-test-script-failed.png" alt-text="Échec de l’exécution de l’PDex da Vinci.":::

## <a name="touchstone-member-match-test"></a>Touchstone $member-test de correspondance

le [deuxième test](https://touchstone.aegis.net/touchstone/testdefinitions?selectedTestGrp=/FHIRSandbox/DaVinci/FHIR4-0-1-Test/PDEX/PayerExchange/01-Member-Match&activeOnly=false&contentEntry=TEST_SCRIPTS) de la section Exchange de données du payeur teste l’existence de l' [opération de correspondance de $member](http://hl7.org/fhir/us/davinci-hrex/2020Sep/OperationDefinition-member-match.html). Pour plus d’informations sur l’opération de correspondance de $member, consultez la [vue d’ensemble de l’opération $Member-match](tutorial-member-match.md).

Dans ce test, vous devrez charger des exemples de données pour que le test réussisse. Nous [disposons d’un fichier Rest](https://github.com/microsoft/fhir-server/blob/main/docs/rest/PayerDataExchange/membermatch.http) avec le patient et la couverture dont vous aurez besoin pour le test. Une fois ces données chargées, vous serez en mesure de réussir ce test. Si les données ne sont pas chargées, vous recevrez une réponse 422 en raison de la non-correspondance exacte.

:::image type="content" source="media/centers-medicare-services-tutorials/davinci-pdex-test-script-passed.png" alt-text="Le script de test da Vinci PDex a réussi.":::

## <a name="touchstone-patient-by-reference"></a>Touchstone patient par référence

Les tests suivants que nous allons examiner sont le [patient par des tests de référence](https://touchstone.aegis.net/touchstone/testdefinitions?selectedTestGrp=/FHIRSandbox/DaVinci/FHIR4-0-1-Test/PDEX/PayerExchange/02-PatientByReference&activeOnly=false&contentEntry=TEST_SCRIPTS) . Cet ensemble de tests vous permet de vérifier que vous pouvez trouver un patient en fonction de différents critères de recherche. La meilleure façon de tester le patient par référence consiste à tester vos propres données, mais nous avons chargé un exemple de fichier de [ressources](https://github.com/microsoft/fhir-server/blob/main/docs/rest/PayerDataExchange/PDex_Sample_Data.http) que vous pouvez charger pour utiliser également.

:::image type="content" source="media/centers-medicare-services-tutorials/davinci-pdex-test-execution-passed.png" alt-text="Exécution de da Vinci PDex réussie.":::

## <a name="touchstone-patienteverything-test"></a>Test TouchStone patient/$everything

Le test final que nous allons examiner est le test de tous les patients. Pour ce test, vous devez charger un patient, puis utiliser l’ID de ce patient pour vérifier que vous pouvez utiliser l’opération de $everything pour extraire toutes les données relatives au patient.

:::image type="content" source="media/centers-medicare-services-tutorials/davinci-pdex-test-patient-everything.png" alt-text="Touchstone patient/$everything test réussi.":::

## <a name="next-steps"></a>Étapes suivantes

dans ce didacticiel, nous avons parcouru la manière de passer le payeur Exchange les tests dans Touchstone. Ensuite, vous pouvez apprendre à tester le Guide d’implémentation de da Vinci PDEX payer Network (plan-net).

>[!div class="nextstepaction"]
>[Da Vinci Plan Net](davinci-plan-net.md)  
