---
title: Fonctionnalités FHIR prises en charge dans le service FHIR
description: Cet article explique les fonctionnalités de la spécification FHIR qui sont implémentées dans les API de santé.
services: healthcare-apis
author: caitlinv39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 11/11/2021
ms.author: cavoeg
ms.openlocfilehash: 4c1b9efce8ad29eeb9ebca6285849760f20fbc17
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132399898"
---
# <a name="supported-fhir-features"></a>Fonctionnalités FHIR prises en charge

> [!IMPORTANT]
> Les API Azure Healthcare sont actuellement en version préliminaire. L’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) contient des conditions légales supplémentaires qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou pas encore en disponibilité générale.

Le &reg; service FHIR dans les API de santé Azure (par défaut appelé le service FHIR) fournit un déploiement entièrement managé du [serveur FHIR Open source](https://github.com/microsoft/fhir-server) et est une implémentation de la norme [FHIR](https://hl7.org/fhir) . Ce document répertorie les principales fonctionnalités du service FHIR.

## <a name="fhir-version"></a>Version FHIR

Dernière version prise en charge : `4.0.1`

Versions antérieures également prises en charge : `3.0.2`

## <a name="rest-api"></a>API REST

Vous trouverez ci-dessous un résumé des fonctionnalités RESTful prises en charge. Pour plus d’informations sur l’implémentation de ces fonctionnalités, consultez fonctionnalités de l' [API REST FHIR](fhir-rest-api-capabilities.md). 

| API    | API Azure pour FHIR | Service FHIR dans les API de santé | Commentaire |
|--------|--------------------|---------------------------------|---------|
| lire   | Oui                | Oui                             |         |
| vread  | Oui                | Oui                             |         |
| update | Oui                | Oui                             |         | 
| update with optimistic locking | Oui       | Oui       |
| update (conditional)           | Oui       | Oui       |
| patch                          | Oui       | Oui       | Prise en charge du [correctif JSON](../../healthcare-apis/fhir/fhir-rest-api-capabilities.md#patch-and-conditional-patch) uniquement. |
| correctif (conditionnel)            | Oui       | Oui       |
| history                        | Oui       | Oui       |
| create                         | Oui       | Oui       | Prend en charge POST et PUT |
| create (conditional)           | Oui       | Oui       | Problème [no 1382](https://github.com/microsoft/fhir-server/issues/1382) |
| recherche                         | Partiel   | Partiel   | Consultez [vue d’ensemble de la recherche de FHIR](overview-of-search.md). |
| recherche chaînée                 | Oui       | Oui       | |
| recherche chaînée inversée         | Oui       | Oui       | |
| lot                          | Oui       | Oui       |
| transaction                    | Non        | Oui       |
| paging                         | Partiel   | Partiel   | `self` et `next` sont pris en charge                     |
| intermediaries                 | Non        | Non        |

## <a name="extended-operations"></a>Opérations étendues

Toutes les opérations prises en charge qui étendent l’API REST.

| Type de paramètre de recherche | API Azure pour FHIR | Service FHIR dans les API de santé| Commentaire |
|------------------------|-----------|-----------|---------|
| [$Export](../../healthcare-apis/data-transformation/export-data.md) (système entier) | Oui       | Oui       | Prend en charge les systèmes, les groupes et les patients. |
| [$convert-data](../../healthcare-apis/data-transformation/convert-data.md)          | Oui       | Oui       |         |
| [$validate](validation-against-profiles.md)              | Oui       | Oui       |         |
| [$member-correspondance](tutorial-member-match.md)          | Oui       | Oui       |         |
| [$patient-tout](patient-everything.md)    | Oui       | Oui       |         |
| historique des $purge         | Oui       | Oui       |         |

## <a name="role-based-access-control"></a>Contrôle d’accès en fonction du rôle

le service FHIR utilise [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) pour le contrôle d’accès. 

## <a name="service-limits"></a>Limites du service

* **Taille de bundle** : chaque bundle est limité à 500 éléments.

* **Limite d’abonnement** : par défaut, chaque abonnement est limité à un maximum de 10 services FHIR. La limite peut être utilisée dans un ou plusieurs espaces de travail. 

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez lu les fonctionnalités FHIR prises en charge dans le service FHIR. Pour plus d’informations sur le déploiement du service FHIR, consultez.
 
>[!div class="nextstepaction"]
>[Déployer le service FHIR](fhir-portal-quickstart.md)
