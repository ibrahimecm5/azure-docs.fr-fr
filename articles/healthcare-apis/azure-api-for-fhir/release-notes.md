---
title: Mises à jour mensuelles de l’API Azure pour FHIR
description: Cet article fournit des détails sur les améliorations et les fonctionnalités mensuelles de l’API Azure pour FHIR.
services: healthcare-apis
author: caitlinv39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 11/15/2021
ms.custom: references_regions
ms.author: cavoeg
ms.openlocfilehash: 0acbf2efdb65ff5376da9be918eaead2169cd5d6
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132547909"
---
# <a name="release-notes-azure-api-for-fhir"></a>Notes de publication : API Azure pour FHIR

L’API Azure pour FHIR fournit un déploiement complètement managé du serveur FHIR Microsoft pour Azure. Le serveur est une implémentation de la [FHIR](https://hl7.org/fhir) standard. Ce document fournit des informations sur les fonctionnalités et les améliorations apportées à l’API Azure pour FHIR.


## <a name="october-2021"></a>Octobre 2021

### <a name="bug-fixes"></a>**Résolution des bogues**

| Bogue de boucle infinie | Informations connexes          |
| ----------------- | ----------------------------: |
|Correction du problème où la [suppression conditionnelle](./././../azure-api-for-fhir/fhir-rest-api-capabilities.md#conditional-delete) pouvait entraîner une boucle infinie. | [#2269](https://github.com/microsoft/fhir-server/pull/2269) |

## <a name="september-2021"></a>Septembre 2021 

### <a name="features-and-enhancements"></a>**Fonctionnalités et améliorations**

|Améliorations &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |Informations connexes |
|------------------- | --------------- |

|Ajout de la prise en charge du correctif conditionnel | [Correctif conditionnel](././../azure-api-for-fhir/fhir-rest-api-capabilities.md#patch-and-conditional-patch)|
| ----------------------------------- | ------: |
|Correctif conditionnel |[#2163](https://github.com/microsoft/fhir-server/pull/2163) |
|Ajout d’un événement d’audit de correctif conditionnel. |[#2213](https://github.com/microsoft/fhir-server/pull/2213) |

|Autoriser le correctif JSON dans les offres groupées | [Correctif JSON dans les offres groupées](././../azure-api-for-fhir/fhir-rest-api-capabilities.md#patch-in-bundles)|
| ----------------------------------- | ------: |
|Permet des regroupements d’historique de recherche avec des demandes de correctif. |[#2156](https://github.com/microsoft/fhir-server/pull/2156) | 
|Correction du correctif JSON dans les lots à l’aide de ressources binaires. |[#2143](https://github.com/microsoft/fhir-server/pull/2143) |

|Nouveaux sous-types d’événements d’audit |Informations connexes |
| ----------------------------------- | ---------------: |
|Nouveaux sous- [types audit NomOpération](././../azure-api-for-fhir/enable-diagnostic-logging.md#audit-log-details)ajoutés.| [#2170](https://github.com/microsoft/fhir-server/pull/2170) |

|[Améliorations de la réindexation](how-to-run-a-reindex.md) |Informations connexes |
| ----------------------------------- | ---------------: |
|Ajout [de limites pour les paramètres de réindexation](how-to-run-a-reindex.md) . |[#2103](https://github.com/microsoft/fhir-server/pull/2103)|
|Met à jour le message d’erreur pour les limites de paramètres de réindexation. |[#2109](https://github.com/microsoft/fhir-server/pull/2109)|
|Vérification du nombre de réindexs final ajoutée. |[#2099](https://github.com/microsoft/fhir-server/pull/2099)|


### <a name="bug-fixes"></a>**Résolution des bogues**

|Bogues résolus du correctif |Informations connexes |
| :----------------------------------- | ---------------: |
|Plus grande détection des exceptions lors de l’application d’un correctif. |[#2192](https://github.com/microsoft/fhir-server/pull/2192)|
|Corrige l’historique avec PATCH dans STU3.| [#2177](https://github.com/microsoft/fhir-server/pull/2177)|

|Bogues de recherche personnalisée |Informations connexes |
| ----------------------------------- | ---------------: |
|Résout l’échec de suppression avec les paramètres de recherche personnalisés. | [#2133](https://github.com/microsoft/fhir-server/pull/2133)|
|Ajout d’une logique de nouvelle tentative lors de la suppression du paramètre de recherche. | [#2121](https://github.com/microsoft/fhir-server/pull/2121)|
|Définissez nombre maximal d’éléments dans les options de recherche dans SearchParameterDefinitionManager. | [#2141](https://github.com/microsoft/fhir-server/pull/2141)|
|Fournit une meilleure exception s’il existe une expression incorrecte dans le paramètre de recherche. |[#2157](https://github.com/microsoft/fhir-server/pull/2157)|

|Erreur de tentative 503 résolue |Informations connexes |
| ----------------------------------- | ---------------: |
|retentez l’erreur 503 à partir de Cosmos DB. |[#2106](https://github.com/microsoft/fhir-server/pull/2106)|
|Résout le traitement des 429s à partir de StoreProcedures. |[#2165](https://github.com/microsoft/fhir-server/pull/2165)|

|problèmes de GitHub fermés |Informations connexes |
| ----------------------------------- | ---------------: |
|Impossible de créer un paramètre de recherche personnalisé pour l’appareil médical CarePlan. |[#2146](https://github.com/microsoft/fhir-server/issues/2146) |
|Message d’erreur non clair pour la création conditionnelle sans ID. | [#2168](https://github.com/microsoft/fhir-server/issues/2168)|

### <a name="iot-connector-for-fhir-preview"></a>Connecteur IoT pour FHIR (version préliminaire)

|Résolutions de bogues &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|Informations connexes |
| ----------------------------------- | ---------------: |
|Correction d’un lien rompu.| Lien mis à jour vers la documentation Azure du connecteur IoT dans l’API Azure pour le portail FHIR. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les fonctionnalités et les correctifs de bogues dans les API Azure Healthcare (service FHIR, service DICOM et connecteur IoT), consultez.

>[!div class="nextstepaction"]
>[Notes de publication : API de santé Azure](../release-notes.md)
