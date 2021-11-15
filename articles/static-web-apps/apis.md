---
title: Prise en charge des API dans Azure Static Web Apps avec Azure Functions
description: Découvrez les fonctionnalités d’API prises en charge parAzure Static Web Apps
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 11/08/2021
ms.author: cshoe
ms.openlocfilehash: b7ab83ddf6d3f20c28d030d48b50ebb88d6676ae
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2021
ms.locfileid: "132027467"
---
# <a name="api-support-in-azure-static-web-apps-with-azure-functions"></a>Prise en charge des API dans Azure Static Web Apps avec Azure Functions

Azure Static Web Apps fournit des points de terminaison d’API serverless via [Azure Functions](../azure-functions/functions-overview.md). Grâce à Azure Functions, les API sont mises à l’échelle dynamiquement en fonction de la demande et incluent les fonctionnalités suivantes :

- **Sécurité intégrée** avec accès direct aux données [d’authentification et d’autorisation basées sur les rôles](user-information.md).

- **Routage transparent** qui met l’itinéraire _api_ à la disposition de l’application web en toute sécurité sans nécessiter de règles CORS personnalisées.

Les API Azure Static Web Apps sont prises en charge par deux configurations possibles en fonction du [plan d’hébergement](plans.md#features) :

- **Fonctions managées** : Par défaut, l’API d’une application web statique est une application Azure Functions gérée et déployée par Azure Static Web Apps associée à certaines restrictions.

- **Apportez vos propres fonctions** : Si vous le souhaitez, vous pouvez [fournir une application Azure Functions existante](functions-bring-your-own.md), quel que soit le type de plan, qui est accompagnée de toutes les fonctionnalités d’Azure Functions. Dans cette configuration, vous avez la responsabilité de gérer un déploiement distinct pour l’application Azure Functions.

Le tableau suivant compare les différences entre l’utilisation de fonctions gérées et celle de fonctions existantes.

| Fonctionnalité | Fonctions managées | Apportez vos propres fonctions |
| --- | --- | --- |
| Accès aux [déclencheurs](../azure-functions/functions-triggers-bindings.md#supported-bindings) Azure Functions | HTTP uniquement | Tous |
| Prise en charge des [runtimes](../azure-functions/supported-languages.md#languages-by-runtime-version) Azure Functions | Node.js 12<br>.NET Core 3.1<br>.NET 6.0<br>Python 3.8 | Tous |
| Prise en charge des [plans d’hébergement](../azure-functions/functions-scale.md) Azure Functions | Consommation | Consommation<br>Premium<br>Dédié |
| [Sécurité intégrée](user-information.md) avec accès direct aux données d’authentification des utilisateurs et d’autorisation basées sur les rôles | ✔ | ✔ |
| [Intégration du routage](./configuration.md?#routes) qui met l’itinéraire _api_ à la disposition de l’application web en toute sécurité sans nécessiter de règles CORS personnalisées | ✔ | ✔ |
| Modèle de programmation [Durable Functions](../azure-functions/durable/durable-functions-overview.md) | ✕ | ✔ |
| [Identité gérée](../app-service/overview-managed-identity.md) | ✕ | ✔ |
| Gestion des jetons [d’authentification et d’autorisation Azure App Service](../app-service/configure-authentication-provider-aad.md) | ✕ | ✔ |
| Fonctions d’API disponibles en dehors d’Azure Static Web Apps | ✕ | ✔ |
| [Références Key Vault](../app-service/app-service-key-vault-references.md) | ✕ | ✔ |

## <a name="configuration"></a>Configuration

Les points de terminaison d’API sont disponibles pour l’application web via l’itinéraire _api_.

| Fonctions managées | Amener vos propres fonctions |
| --- | --- |
| Bien que l’itinéraire _api_ soit fixe, vous contrôlez l’emplacement du dossier du code source de l’application de fonction managée. Vous pouvez modifier cet emplacement en [modifiant le fichier YAML de flux de travail](build-configuration.md) situé dans le dossier _. GitHub/workflows_ de votre référentiel. | Les requêtes adressées à l’itinéraire _api_ sont envoyées à votre application Azure Functions existante. |

## <a name="troubleshooting-and-logs"></a>Résolution des problèmes et journaux

Les journaux ne sont disponibles que si vous ajoutez [Application Insights](monitor.md).

| Fonctions managées | Amener vos propres fonctions |
| --- | --- |
| Activez la journalisation en activant Application Insights sur votre application web statique. | Activez la journalisation en activant Application Insights sur votre application Azure Functions. |

## <a name="constraints"></a>Contraintes

- Le préfixe d’itinéraire de l’API doit être _api_.
- Les règles de routage pour les fonctions d’API prennent uniquement en charge les [redirections](configuration.md#defining-routes) et la [sécurisation](configuration.md#securing-routes-with-roles) des itinéraires avec des rôles.

| Fonctions managées | Amener vos propres fonctions |
| --- | --- |
| <ul><li>Les déclencheurs sont limités à [HTTP](../azure-functions/functions-bindings-http-webhook.md).</li><li>L’application Azure Functions doit être une application Node.js 12, .NET Core 3.1, NET 6.0 ou Python 3.8.</li><li>Certains paramètres d’application sont gérés par le service. Par conséquent, les préfixes suivants sont réservés par le runtime :<ul><li>*APPSETTING\_, AZUREBLOBSTORAGE\_, AZUREFILESSTORAGE\_, AZURE_FUNCTION\_, CONTAINER\_, DIAGNOSTICS\_, DOCKER\_, FUNCTIONS\_, IDENTITY\_, MACHINEKEY\_, MAINSITE\_, MSDEPLOY\_, SCMSITE\_, SCM\_, WEBSITES\_, WEBSITE\_, WEBSOCKET\_, AzureWeb*</li></ul></li></ul> | <ul><li>Vous êtes responsable de la gestion du déploiement de l’application Azure Functions.</li></ul> |

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Ajouter une API](add-api.md)
