---
title: Vue d’ensemble de la recherche FHIR dans les API de santé Azure
description: Cet article décrit une vue d’ensemble de la recherche de FHIR qui est implémentée dans les API de santé Azure
author: CaitlinV39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 11/24/2021
ms.author: cavoeg
ms.openlocfilehash: dfdde4bb53624777d4e11cbd6652f749a6f3a394
ms.sourcegitcommit: 66b6e640e2a294a7fbbdb3309b4829df526d863d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/01/2021
ms.locfileid: "133368841"
---
# <a name="overview-of-fhir-search"></a>Vue d’ensemble de la recherche FHIR

> [!IMPORTANT]
> Les API Azure Healthcare sont actuellement en version préliminaire. L’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) contient des conditions légales supplémentaires qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou pas encore en disponibilité générale.

La spécification FHIR définit les notions de base de la recherche de ressources FHIR. Cet article vous guide tout au long des aspects clés de la recherche de ressources dans FHIR. Pour plus d’informations sur la recherche de ressources FHIR, reportez-vous à [recherche](https://www.hl7.org/fhir/search.html) dans la spécification HL7 FHIR. Tout au long de cet article, nous fournirons des exemples de syntaxe de recherche. Chaque recherche sera effectuée sur votre serveur FHIR, qui a généralement l’URL `https://<WORKSPACE NAME>-<ACCOUNT-NAME>.fhir.azurehealthcareapis.com` . Dans les exemples, nous allons utiliser l’espace réservé {{FHIR_URL}} pour cette URL. 

Les recherches FHIR peuvent être effectuées sur un type de ressource spécifique, un [compartiment](https://www.hl7.org/fhir/compartmentdefinition.html)spécifié ou toutes les ressources. La façon la plus simple d’exécuter une recherche dans FHIR consiste à utiliser une `GET` requête. Par exemple, si vous souhaitez extraire tous les patients de la base de données, vous pouvez utiliser la requête suivante : 

```rest
GET {{FHIR_URL}}/Patient
```

Vous pouvez également effectuer une recherche à l’aide `POST` de, ce qui est utile si la chaîne de requête est trop longue. Pour effectuer une recherche à l’aide de `POST` , les paramètres de recherche peuvent être envoyés en tant que corps de formulaire. Cela permet d’obtenir des séries plus longues et plus complexes de paramètres de requête qui peuvent être difficiles à voir et à comprendre dans une chaîne de requête.

Si la demande de recherche est réussie, vous recevrez une réponse de Bundle FHIR avec le type `searchset` . Si la recherche échoue, vous trouverez les détails de l’erreur dans le `OperationOutcome` pour vous aider à comprendre pourquoi la recherche a échoué.

Dans les sections suivantes, nous allons aborder les différents aspects impliqués dans la recherche. Une fois que vous avez consulté ces détails, reportez-vous à notre [page d’exemples](search-samples.md) qui contient des exemples de recherches que vous pouvez effectuer dans le service FHIR dans les API de santé Azure.

## <a name="search-parameters"></a>Paramètres de recherche

Lorsque vous effectuez une recherche, vous effectuez une recherche en fonction de divers attributs de la ressource. Ces attributs sont appelés paramètres de recherche. Chaque ressource a un ensemble de paramètres de recherche définis. Le paramètre de recherche doit être défini et indexé dans la base de données pour que vous parvenez à effectuer une recherche sur celui-ci.

Chaque paramètre de recherche a un [type de données](https://www.hl7.org/fhir/search.html#ptypes)défini. La prise en charge des différents types de données est présentée ci-dessous :


| **Type de paramètre de recherche**  | **Azure API pour FHIR** | **Service FHIR dans les API de santé Azure** | **Commentaire**|
| -------------------------  | -------------------- | ------------------------- | ------------|
|  nombre                    | Oui                  | Oui                       |
|  Date                      | Oui                  | Oui                       |
|  string                    | Oui                  | Oui                       |
|  token                     | Oui                  | Oui                       |
|  reference                 | Oui                  | Oui                       |
|  composites                 | Partiel              | Partiel                   | La liste des types composites pris en charge est décrite plus loin dans cet article. |
|  quantité                  | Oui                  | Oui                       |
|  URI                       | Oui                  | Oui                       |
|  spécial                   | Non                   | Non                        |

### <a name="common-search-parameters"></a>Paramètres de recherche communs

Il existe des [paramètres de recherche communs](https://www.hl7.org/fhir/search.html#all) qui s’appliquent à toutes les ressources. Celles-ci sont répertoriées ci-dessous, ainsi que leur prise en charge :

| **Paramètre de recherche courant** | **Azure API pour FHIR** | **Service FHIR dans les API de santé Azure** | **Commentaire**|
| -------------------------  | -------------------- | ------------------------- | ------------|
| _id                         | Oui                  | Oui                       
| _lastUpdated                | Oui                  | Oui                       |
| _tag                        | Oui                  | Oui                       |
| _type                       | Oui                  | Oui                       |
| _security                   | Oui                  | Oui                       |
| _profile                    | Oui                  | Oui                       |
| _has                        | Oui.                 | Oui                       |  |
| _query                      | Non                   | Non                        |
| _filter                     | Non                   | Non                        |
| _list                       | Non                   | Non                        |
| _text                       | Non                   | Non                        |
| _content                    | Non                   | Non                        |

### <a name="resource-specific-parameters"></a>Paramètres spécifiques à la ressource

Avec le service FHIR dans les API de santé Azure, nous prenons en charge presque tous les [paramètres de recherche spécifiques aux ressources](https://www.hl7.org/fhir/searchparameter-registry.html) définis par la spécification FHIR. Les seuls paramètres de recherche que nous ne prenons pas en charge sont disponibles dans les liens ci-dessous :

* [Paramètres de recherche non pris en charge par STU3](https://github.com/microsoft/fhir-server/blob/main/src/Microsoft.Health.Fhir.Core/Data/Stu3/unsupported-search-parameters.json)

* [R4 paramètres de recherche non pris en charge](https://github.com/microsoft/fhir-server/blob/main/src/Microsoft.Health.Fhir.Core/Data/R4/unsupported-search-parameters.json)

Vous pouvez également voir la prise en charge actuelle des paramètres de recherche dans l' [instruction de fonctionnalité FHIR](https://www.hl7.org/fhir/capabilitystatement.html) avec la requête suivante :

```rest
GET {{FHIR_URL}}/metadata
```

Pour afficher les paramètres de recherche dans l’instruction Capability, accédez à `CapabilityStatement.rest.resource.searchParam` pour afficher les paramètres de recherche de chaque ressource et `CapabilityStatement.rest.searchParam` Rechercher les paramètres de recherche pour toutes les ressources.

> [!NOTE]
> Le service FHIR dans les API de santé Azure ne crée ou n’indexe pas automatiquement les paramètres de recherche qui ne sont pas définis par la spécification FHIR. Toutefois, nous fournissons un support pour vous permettre de définir vos propres [paramètres de recherche](how-to-do-custom-search.md).

### <a name="composite-search-parameters"></a>Paramètres de recherche composite
La recherche composite vous permet d’effectuer des recherches sur des paires valeur. Par exemple, si vous recherchez une observation de hauteur dans laquelle la personne était de 60 pouces, vous souhaiterez vous assurer qu’un seul composant de l’observation contenait le code de la hauteur **et** la valeur de 60. Vous ne souhaitez pas obtenir une observation où un poids de 60 et une hauteur de 48 ont été stockés, même si l’observation a des entrées qui sont qualifiées pour la valeur de 60 et le code de la hauteur, juste dans des sections de composants différentes. 

Avec le service FHIR pour les API Azure Healthcare, nous prenons en charge les jumelages de types de paramètres de recherche suivants :

* Référence, jeton
* Jeton, date
* Token, Number, Number
* Jeton, quantité
* Jeton, chaîne
* Jeton, jeton

Pour plus d’informations, consultez les [paramètres de recherche composite](https://www.hl7.org/fhir/search.html#composite)HL7. 

> [!NOTE]
> Les paramètres de recherche composite ne prennent pas en charge les modificateurs selon la spécification FHIR.

 ### <a name="modifiers--prefixes"></a>Modificateurs & préfixes

Les [modificateurs](https://www.hl7.org/fhir/search.html#modifiers) vous permettent de modifier le paramètre de recherche. Vous trouverez ci-dessous une vue d’ensemble de tous les modificateurs FHIR et de la prise en charge : 

| **Modificateurs** | **Azure API pour FHIR** | **Service FHIR dans les API de santé Azure** | **Commentaire**|
| -------------------------  | -------------------- | ------------------------- | ------------|
|  : manquant     | Oui                  | Oui                       |
|  : exact       | Oui                  | Oui                       |
|  : contient    | Oui                  | Oui                       |
|  : texte        | Oui                  | Oui                       |
|  : type (référence) | Oui             | Oui                       |
|  : non         | Oui                  | Oui                       |
|  : ci-dessous (Uri) | Oui                  | Oui                       |
|  : ci-dessus (Uri) | Oui                  | Oui                       |
|  : in (jeton)  | Non                   | Non                        |
|  : ci-dessous (jeton) | Non                 | Non                        |
|  : ci-dessus (jeton) | Non                 | Non                        |
|  : not-in (jeton) | Non                | Non                        |

Pour les paramètres de recherche qui ont un ordre spécifique (nombres, dates et quantités), vous pouvez utiliser un [préfixe](https://www.hl7.org/fhir/search.html#prefix) sur le paramètre pour faciliter la recherche des correspondances. Le service FHIR dans les API Azure Healthcare prend en charge tous les préfixes.

 ### <a name="search-result-parameters"></a>Paramètres des résultats de la recherche
Pour faciliter la gestion des ressources retournées, vous pouvez utiliser des paramètres de résultat de recherche dans votre recherche. Pour plus d’informations sur l’utilisation de chacun des paramètres de résultat de la recherche, reportez-vous au site Web [HL7](https://www.hl7.org/fhir/search.html#return) . 

| **Paramètres des résultats de la recherche**  | **Azure API pour FHIR** | **Service FHIR dans les API de santé Azure** | **Commentaire**|
| -------------------------  | -------------------- | ------------------------- | ------------|
| _elements                     | Oui                  | Oui                       |
| _count                        | Oui                  | Oui                       | _count est limité à 1000 ressources. S’il est défini sur une valeur supérieure à 1000, seul 1000 est retourné et un avertissement est renvoyé dans le bundle.                               |
| _include                      | Oui                  | Oui                       | Les éléments inclus sont limités à 100. _include sur PaaS et OSS sur Cosmos DB n’incluez pas : prise en charge des itérations [(#2137)](https://github.com/microsoft/fhir-server/issues/2137).                               |
| _revinclude                   | Oui                  | Oui                       |Les éléments inclus sont limités à 100. _revinclude sur PaaS et OSS sur Cosmos DB n’incluez pas : prise en charge des itérations [(#2137)](https://github.com/microsoft/fhir-server/issues/2137). Il y a également un code d’état incorrect pour une demande incorrecte [#1319](https://github.com/microsoft/fhir-server/issues/1319)                            |
| _summary                      | Oui             | Oui                   |
| _total                        | Partiel              | Partiel                   | _total = None et _total = exact                               |
| _sort                         | Partiel              | Partiel                   | sort = _lastUpdated est pris en charge sur l’API Azure pour FHIR et le service FHIR. pour le service FHIR et les serveurs OSS SQL DB FHIR, le tri par chaînes et les champs dateTime sont pris en charge. pour l’API Azure pour FHIR et OSS Cosmos DB les bases de données créées après le 20 avril 2021, le tri est pris en charge pour le prénom, le nom, la date de naissance et la date clinique.             |
| _contained                    | Non                   | Non                        |
| _containedType                | Non                   | Non                        |
| _score                        | Non                   | Non                        |

> [!NOTE]
> Par défaut, `_sort` trie l’enregistrement dans l’ordre croissant. Vous pouvez utiliser le préfixe `'-'` pour trier dans l’ordre décroissant. En outre, le service FHIR et l’API Azure pour FHIR vous permettent uniquement de trier sur un seul champ à la fois.

Par défaut, le service FHIR dans les API de santé Azure est défini sur gestion stricte. Cela signifie que le serveur ignore les paramètres inconnus ou non pris en charge. Si vous souhaitez utiliser une gestion stricte, vous pouvez utiliser l’en-tête et le set **préférés** `handling=strict` .

 ## <a name="chained--reverse-chained-searching"></a>Chaîné & recherche chaînée inverse

Une [recherche chaînée](https://www.hl7.org/fhir/search.html#chaining) vous permet de rechercher à l’aide d’un paramètre de recherche sur une ressource référencée par une autre ressource. Par exemple, si vous souhaitez rechercher des éléments où le nom du patient est Jane, utilisez :

`GET {{FHIR_URL}}/Encounter?subject:Patient.name=Jane`

De même, vous pouvez effectuer une recherche chaînée par inversion. Cela vous permet d’accéder à des ressources où vous spécifiez des critères sur d’autres ressources qui y font référence. Pour obtenir plus d’exemples de recherches chaînées et enchaînées, reportez-vous à la page d' [exemples de recherche FHIR](search-samples.md) . 


## <a name="pagination"></a>Pagination

Comme indiqué ci-dessus, les résultats d’une recherche sont un bundle paginé. Par défaut, la recherche renverra 10 résultats par page, mais cela peut être augmenté (ou diminué) en spécifiant `_count` . Dans le bundle, il y aura un lien automatique qui contient le résultat actuel de la recherche. S’il existe des correspondances supplémentaires, le bundle contient un lien suivant. Vous pouvez continuer à utiliser le lien suivant pour obtenir les pages de résultats suivantes. `_count` est limité à 1000 éléments ou moins. 

Actuellement, le service FHIR dans les API de santé Azure prend uniquement en charge le lien suivant dans les offres groupées, et il ne prend pas en charge les liens First, Last ou Previous.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris les bases de la recherche, consultez la page Rechercher des exemples pour plus d’informations sur la recherche à l’aide de différents paramètres de recherche, modificateurs et autres scénarios de recherche de FHIR.

>[!div class="nextstepaction"]
>[Exemples de recherche FHIR](search-samples.md)
