---
title: Fonctionnalités de FHIR prises en charge dans Azure - API Azure pour FHIR
description: Cet article détaille les fonctionnalités de la spécification de FHIR qui sont implémentées dans l’API Azure pour FHIR
services: healthcare-apis
author: caitlinv39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 11/11/2021
ms.author: cavoeg
ms.openlocfilehash: b6c170a7b2f46adeb4b287424601455bb078aba7
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132397297"
---
# <a name="features"></a>Fonctionnalités

L’API Azure pour FHIR fournit un déploiement complètement managé du serveur FHIR Microsoft pour Azure. Le serveur est une implémentation de la [FHIR](https://hl7.org/fhir) standard. Ce document répertorie les principales fonctionnalités du serveur FHIR.

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
| patch                          | Oui       | Oui       | Prise en charge du [correctif JSON](https://www.hl7.org/fhir/http.html#patch) uniquement. Nous avons inclus une solution de contournement pour utiliser le correctif JSON dans une offre groupée dans [ce PR](https://github.com/microsoft/fhir-server/pull/2143).|
| correctif (conditionnel)            | Oui       | Oui       |
| history                        | Oui       | Oui       |
| create                         | Oui       | Oui       | Prend en charge POST et PUT |
| create (conditional)           | Oui       | Oui       | Problème [no 1382](https://github.com/microsoft/fhir-server/issues/1382) |
| recherche                         | Partiel   | Partiel   | Consultez [vue d’ensemble de la recherche de FHIR](overview-of-search.md). |
| recherche chaînée                 | Oui       | Oui       | Voir la remarque ci-dessous. |
| recherche chaînée inversée         | Oui       | Oui       | Voir la remarque ci-dessous. |
| lot                          | Oui       | Oui       |
| transaction                    | Non        | Oui       |
| paging                         | Partiel   | Partiel   | `self` et `next` sont pris en charge                     |
| intermediaries                 | Non        | Non        |

> [!Note] 
> dans l’API Azure pour FHIR et le serveur FHIR open source sauvegardé par Cosmos, la recherche chaînée et la recherche chaînée par chaîne est une implémentation MVP. pour effectuer une recherche chaînée sur Cosmos DB, l’implémentation parcourt l’expression de recherche et émet des sous-requêtes pour résoudre les ressources correspondantes. Cette opération est effectuée pour chaque niveau de l’expression. Si une requête retourne plus de 1000 résultats, une erreur est générée.

## <a name="extended-operations"></a>Opérations étendues

Toutes les opérations prises en charge qui étendent l’API REST.

| Type de paramètre de recherche | API Azure pour FHIR | Service FHIR dans les API de santé| Commentaire |
|------------------------|-----------|-----------|---------|
| [$Export](../../healthcare-apis/data-transformation/export-data.md) (système entier) | Oui       | Oui       | Prend en charge les systèmes, les groupes et les patients.   |
| [$convert-data](convert-data.md)          | Oui       | Oui       |         |
| [$validate](validation-against-profiles.md)              | Oui       | Oui       |         |
| [$member-correspondance](tutorial-member-match.md)          | Oui       | Oui       |         |
| [$patient-tout](patient-everything.md)    | Oui       | Oui       |         |
| historique des $purge         | Oui       | Oui       |         |

## <a name="persistence"></a>Persistance

Le serveur FHIR Microsoft dispose d’un module de persistance enfichable (voir [`Microsoft.Health.Fhir.Core.Features.Persistence`](https://github.com/Microsoft/fhir-server/tree/master/src/Microsoft.Health.Fhir.Core/Features/Persistence)).

Le code open source de FHIR Server inclut une implémentation pour [Azure Cosmos DB](../../cosmos-db/index-overview.md) et [SQL Database](https://azure.microsoft.com/services/sql-database/).

Cosmos DB est une base de données multimodèle (SQL API, MongoDB API, etc.) distribuée à l’échelle mondiale. Il prend en charge différents [niveaux de cohérence](../../cosmos-db/consistency-levels.md). Le modèle de déploiement par défaut configure le serveur FHIR avec une cohérence `Strong`, mais la stratégie de cohérence peut être modifiée (généralement assouplie) en envoyant une demande individuelle à l’aide de l’en-tête de demande `x-ms-consistency-level`.

## <a name="role-based-access-control"></a>Contrôle d’accès en fonction du rôle

Le serveur FHIR utilise [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) pour contrôler l’accès. Plus précisément, le contrôle d’accès en fonction du rôle (RBAC) est appliqué, si le paramètre de configuration `FhirServer:Security:Enabled` a la valeur `true`. De plus, l’en-tête de requête `Authorization` de toutes les requêtes (à l’exception de `/metadata`) envoyées au serveur FHIR Server doit avoir la valeur `Bearer <TOKEN>`. Le jeton doit contenir au moins un rôle tel que défini dans la revendication `roles`. Une demande sera autorisée si le jeton contient un rôle qui permet l’application de l’action spécifiée sur la ressource spécifiée.

Actuellement, les actions autorisées pour un rôle donné sont appliquées *à l’ensemble* de l’API.

## <a name="service-limits"></a>Limites du service

* [**Unités de requête (RU)**](../../cosmos-db/concepts-limits.md) : vous pouvez configurer jusqu’à 10 000 unités de requête dans le portail pour l’API Azure pour FHIR. Vous aurez besoin d’un minimum de 400 ou de 40 unités de capacité/Go, selon la valeur la plus grande. Si vous avez besoin de plus de 10 000 unités de requête, vous pouvez mettre en place un ticket de support pour que le RUs soit augmenté. Le maximum disponible est 1 000 000.

* **Taille de bundle** : chaque bundle est limité à 500 éléments.

* **Taille des données** : les éléments Données/Documents doivent chacun être un peu inférieurs à 2 Mo.

* **Limite d’abonnement** : par défaut, chaque abonnement est limité à un maximum de 10 instances de serveur FHIR. Si vous avez besoin d’un plus grand nombre d’instances par abonnement, ouvrez un ticket de support et fournissez des informations détaillées sur vos besoins.

* **Connexions et instances simultanées** : par défaut, vous disposez de 15 connexions simultanées sur deux instances du cluster (pour un total de 30 demandes simultanées). Si vous avez besoin de plus de demandes simultanées, ouvrez un ticket de support et fournissez des informations détaillées sur vos besoins.

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez obtenu des informations sur les fonctionnalités FHIR prises en charge dans l’API Azure pour FHIR. Pour plus d’informations sur le déploiement de l’API Azure pour FHIR, consultez
 
>[!div class="nextstepaction"]
>[Déployer l’API Azure pour FHIR](fhir-paas-portal-quickstart.md)
