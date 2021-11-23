---
title: Mises à jour mensuelles des API Azure Healthcare
description: Cet article fournit des détails sur les fonctionnalités et améliorations mensuelles des API Azure Healthcare.
services: healthcare-apis
author: caitlinv39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 11/22/2021
ms.author: cavoeg
ms.openlocfilehash: 304fdc3ceea4c26e861ba54e2dd90f98fd7bc225
ms.sourcegitcommit: 3d04177023a3136832adb561da831ccc8e9910c7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/23/2021
ms.locfileid: "132936521"
---
# <a name="release-notes-azure-healthcare-apis"></a>Notes de publication : API de santé Azure

> [!IMPORTANT]
> Les API Azure Healthcare sont actuellement en version préliminaire. L’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) contient des conditions légales supplémentaires qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou pas encore en disponibilité générale. 

Les API Azure Healthcare sont un ensemble de services d’API gérés basés sur des normes et infrastructures ouvertes pour le secteur de la santé. Elles vous permettent de créer des solutions de santé évolutives et sécurisées en regroupant les jeux de données d’informations d’intégrité protégés (PHI) et en les connectant de bout en bout avec les outils de Machine Learning, d’analyse et d’intelligence artificielle. Ce document fournit des informations sur les fonctionnalités et les améliorations apportées aux API de santé Azure, y compris les différents types de services (service FHIR, service DICOM et connecteur IoT) qui fonctionnent en toute transparence les uns avec les autres.

## <a name="october-2021"></a>Octobre 2021

### <a name="azure-healthcare-apis-feature-enhancements"></a>Améliorations des fonctionnalités des API Azure Healthcare

| Améliorations &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | Informations connexes           |
|------------- | -----------------------------: |
|Outil de génération de données de test |nous avons mis à jour les api de santé GitHub exemples référentiel pour inclure un [outil de génération de données de Test](https://github.com/microsoft/healthcare-apis-samples/blob/main/docs/HowToRunPerformanceTest.md) à l’aide des données Synthea. Cet outil est une amélioration des [projets de test public](https://github.com/ShadowPic/PublicTestProjects)Open source, basés sur Apache Jmeter, qui peuvent être déployés sur Azure AKS pour les tests de performances. |

### <a name="fhir-service"></a>Service FHIR

#### <a name="feature-enhancements"></a>**Améliorations de la fonctionnalité**

|Améliorations | Informations connexes |
|------------------------ | -------------------------------: |
|Ajout de la prise en charge de [_sort](././../healthcare-apis/fhir/overview-of-search.md#search-result-parameters) sur les chaînes et DateTime. |[#2169](https://github.com/microsoft/fhir-server/pull/2169)  |

#### <a name="bug-fixes"></a>**Résolution des bogues**

|Résolution des bogues | Informations connexes |
|------------------------ | -------------------------------: |
|Correction du problème où la [suppression conditionnelle](././../healthcare-apis/fhir/fhir-rest-api-capabilities.md#conditional-delete) pouvait entraîner une boucle infinie. | [#2269](https://github.com/microsoft/fhir-server/pull/2269) |
|Erreur 500 résolue, peut-être provoquée par un corps de transaction mal formé dans une publication de bundle. Nous avons ajouté une vérification que l’URL est renseignée dans les demandes de [lot de transactions](././..//healthcare-apis/fhir/fhir-features-supported.md#rest-api) . | [#2255](https://github.com/microsoft/fhir-server/pull/2255) |

### <a name="dicom-service"></a>**Service DICOM**

|Ajout de prise en charge | Informations connexes |
|------------------------ | -------------------------------: |
|Régions | Sud du Brésil et centre du Canada. Pour plus d’informations sur les régions Azure et les zones de disponibilité, consultez [services Azure prenant en charge les zones de disponibilité](./../availability-zones/az-region.md). |
|Balises de requête étendues |Types de représentation de valeur DateTime (DT) et Time (TM) |

|Résolution des bogues | Informations connexes |
|------------------------ | -------------------------------: |
|Correction implémentée des noms d’espaces de travail. |Service DICOM activé pour fonctionner avec des espaces de travail dont les noms commencent par une lettre. |

## <a name="september-2021"></a>Septembre 2021

### <a name="fhir-service"></a>Service FHIR

#### <a name="feature-enhancements"></a>**Améliorations de la fonctionnalité**

|Améliorations | Informations connexes |
|:------------------- | -------------------------------: |

|Ajout de la prise en charge du correctif conditionnel | [Correctif conditionnel](./././azure-api-for-fhir/fhir-rest-api-capabilities.md#patch-and-conditional-patch)|
|:------------------- | -------------------------------:|
|Correctif conditionnel | [#2163](https://github.com/microsoft/fhir-server/pull/2163) |
|Ajout d’un événement d’audit de correctif conditionnel. | [#2213](https://github.com/microsoft/fhir-server/pull/2213) |

|Autoriser le correctif JSON dans les offres groupées | [Correctif JSON dans les offres groupées](./././azure-api-for-fhir/fhir-rest-api-capabilities.md#patch-in-bundles)|
|:------------------- | -------------------------------:|
|Permet des regroupements d’historique de recherche avec des demandes de correctif. |[#2156](https://github.com/microsoft/fhir-server/pull/2156) | 
|Correction du correctif JSON dans les lots à l’aide de ressources binaires. |[#2143](https://github.com/microsoft/fhir-server/pull/2143) |
|Ajout de nouveaux [sous-types](./././azure-api-for-fhir/enable-diagnostic-logging.md#audit-log-details) d’événement d’audit| [#2170](https://github.com/microsoft/fhir-server/pull/2170) |

| Exécution d’un travail de réindexation | [Améliorations de la réindexation](./././fhir/how-to-run-a-reindex.md)|
|:------------------- | -------------------------------:|
|Ajout [de limites pour les paramètres de réindexation](./././azure-api-for-fhir/how-to-run-a-reindex.md#performance-considerations) . |[#2103](https://github.com/microsoft/fhir-server/pull/2103)|
|Message d’erreur mis à jour pour les limites de paramètres de réindexation. |[#2109](https://github.com/microsoft/fhir-server/pull/2109)|
|Vérification du nombre de réindexs final ajoutée. |[#2099](https://github.com/microsoft/fhir-server/pull/2099)|

#### <a name="bug-fixes"></a>**Résolution des bogues**

|Bogues résolus du correctif | Informations connexes |
|:------------------- | --------------------------------: |

| Plus grand catch pour les exceptions pendant l’application du correctif | [#2192](https://github.com/microsoft/fhir-server/pull/2192)|
|:------------------- | -----------: |
|Corriger l’historique avec PATCH dans STU3 |[#2177](https://github.com/microsoft/fhir-server/pull/2177) |

|Bogues de recherche personnalisée | Informations connexes |
|:------------------- | -------------------------------: |
|Résout l’échec de suppression avec les paramètres de recherche personnalisés |[#2133](https://github.com/microsoft/fhir-server/pull/2133) |
|Ajout d’une logique de nouvelle tentative lors de la suppression du paramètre de recherche | [#2121](https://github.com/microsoft/fhir-server/pull/2121)|
|Définir le nombre maximal d’éléments dans les options de recherche dans SearchParameterDefinitionManager |[#2141](https://github.com/microsoft/fhir-server/pull/2141) |
|Meilleure exception s’il existe une expression incorrecte dans un paramètre de recherche |[#2157](https://github.com/microsoft/fhir-server/pull/2157) |

|résolu SQL réindexation par lot en cas d’échec d’une ressource | Informations connexes |
|:------------------- | -------------------------------: |
|mises à jour SQL logique de nouvelle tentative de réindexation par lot |[#2118](https://github.com/microsoft/fhir-server/pull/2118) |

|problèmes de GitHub fermés | Informations connexes |
|:------------------- | -------------------------------: |
|Message d’erreur non clair pour la création conditionnelle sans ID |[#2168](https://github.com/microsoft/fhir-server/issues/2168) |

### <a name="dicom-service"></a>**Service DICOM**

|Résolution des bogues | Informations connexes |
|:------------------- | -------------------------------: |

|Correctif implémenté pour résoudre les problèmes de classement de pagination QIDO |  [#989](https://github.com/microsoft/dicom-server/pull/989) |
|:------------------- | -------------------------------: |

### <a name="iot-connector"></a>**Connecteur IoT**

|Résolution des bogues | Informations connexes |
|:------------------- | -------------------------------: |
| Améliorations normalisées du connecteur IoT avec calculs pour prendre en charge et améliorer la normalisation des données d’intégrité. | Voir : [utiliser des mappages d’appareils](./../healthcare-apis/iot/how-to-use-device-mappings.md) et des [fonctions calculées](./../healthcare-apis/iot/how-to-use-calculated-functions-mappings.md)  |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les fonctionnalités et les correctifs de bogues dans l’API Azure pour FHIR, consultez

>[!div class="nextstepaction"]
>[Notes de publication : API Azure pour FHIR](./azure-api-for-fhir/release-notes.md)
