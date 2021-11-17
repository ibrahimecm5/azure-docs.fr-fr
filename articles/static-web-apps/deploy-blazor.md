---
title: 'Tutoriel : Créer une application web statique avec Blazor dans Azure Static Web Apps'
description: Apprenez à créer un site web Azure Static Web Apps avec Blazor.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 11/08/2021
ms.author: cshoe
ms.openlocfilehash: ac22d5ef720fb0701cb126e6fa0cb627614dd09c
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2021
ms.locfileid: "132027163"
---
# <a name="tutorial-building-a-static-web-app-with-blazor-in-azure-static-web-apps"></a>Tutoriel : Créer une application web statique avec Blazor dans Azure Static Web Apps

Azure Static Web Apps publie un site web dans un environnement de production en compilant les applications à partir d’un dépôt GitHub. Dans ce tutoriel, vous allez déployer une application web dans Azure Static Web Apps à partir du portail Azure.

Si vous n’avez pas d’abonnement Azure, [créez un compte d’essai gratuit](https://azure.microsoft.com/free).

## <a name="prerequisites"></a>Prérequis

- [GitHub](https://github.com)
- Compte [Azure](https://portal.azure.com)

## <a name="application-overview"></a>Vue d’ensemble de l’application

Azure Static Web Apps vous permet de créer des applications web statiques prises en charge par un back-end serverless. Le tutoriel suivant explique comment déployer une application Blazor WebAssembly C# qui affiche les données météorologiques retournées par une API serverless.

:::image type="content" source="./media/deploy-blazor/blazor-app-complete.png" alt-text="Application Blazor complète":::

L'application présentée dans ce tutoriel est constituée de trois projets Visual Studio différents :

- **Api** : application Azure Functions C# implémentant le point de terminaison d’API qui fournit les informations météorologiques à l’application Blazor WebAssembly. **WeatherForecastFunction** retourne un tableau d’objets `WeatherForecast`.

- **Client** : projet Blazor WebAssembly front-end. Une [route de secours](#fallback-route) est implémentée pour garantir le bon fonctionnement du routage côté client.

- **Partagé** : contient les classes communes référencées par les projets API et Client, ce qui permet aux données de circuler du point de terminaison d'API vers l'application web frontale. La classe [`WeatherForecast`](https://github.com/staticwebdev/blazor-starter/blob/main/Shared/WeatherForecast.cs) est partagée entre les deux applications.

Ces projets sont tous nécessaires à la création d’une application Blazor WebAssembly exécutée dans le navigateur pris en charge par un back-end d’API Azure Functions.

## <a name="fallback-route"></a>Itinéraire de secours

L'application expose des URL telles que _/counter_ et _/fetchdata_ qui correspondent à des itinéraires spécifiques de l'application. Dans la mesure où cette application est implémentée sous forme d'application monopage, chaque itinéraire reçoit le fichier _index.html_. Pour garantir que les requêtes de chemin retournent _index.html_, une [route de secours](./configuration.md#fallback-routes) est implémentée dans le fichier _staticwebapp.config.json_ situé dans le dossier racine du projet Client.

```json
{
  "navigationFallback": {
    "rewrite": "/index.html"
  }
}
```

Grâce à la configuration ci-dessus, les requêtes adressées à tout itinéraire associé à l'application renvoient la page _index.html_.

## <a name="create-a-repository"></a>Créer un référentiel

Cet article utilise un dépôt de modèles GitHub pour vous permettre de démarrer facilement. Le modèle comprend une application de démarrage qui peut être déployée sur Azure Static Web Apps.

1. Veillez à être connecté à GitHub et accédez à l’emplacement suivant pour créer un dépôt :
   - [https://github.com/staticwebdev/blazor-starter/generate](https://github.com/login?return_to=/staticwebdev/blazor-starter/generate)
1. Nommez votre référentiel **my-first-static-blazor-app**.

## <a name="create-a-static-web-app"></a>Créer une application web statique

Maintenant que le référentiel est créé, créez une application web statique à partir du portail Azure.

1. Accédez au [portail Azure](https://portal.azure.com).
1. Sélectionnez **Créer une ressource**.
1. Recherchez **Static Web Apps**.
1. Sélectionnez **Static Web Apps**.
1. Sélectionnez **Create** (Créer).
1. Sous l’onglet _Informations de base_, entrez les valeurs suivantes.

    | Propriété | Valeur |
    | --- | --- |
    | _Abonnement_ | Le nom de votre abonnement Azure. |
    | _Groupe de ressources_ | **my-blazor-group**  |
    | _Nom_ | **my-first-static-blazor-app** |
    | _Type de plan_ | **Gratuit** |
    | _Région de l’API Azure Functions et des environnements intermédiaires_ | Sélectionnez la région la plus proche de vous. |
    | _Source_ | **GitHub** |

1. Sélectionnez **Se connecter avec GitHub** et authentifiez-vous auprès de GitHub.

1. Entrez les valeurs GitHub suivantes.

    | Propriété | Valeur |
    | --- | --- |
    | _Organisation_ | Sélectionnez l’organisation GitHub de votre choix. |
    | _Dépôt_ | Sélectionnez **my-first-static-blazor-app**. |
    | _Branche_ | Sélectionnez **principal**. |

1. Dans la section _Détails de build_, sélectionnez **Blazor** dans la liste déroulante _Présélections de build_. Les valeurs suivantes sont remplies.

    | Propriété | Valeur | Description |
    | --- | --- | --- |
    | Emplacement de l’application | **Client** | Dossier contenant l’application Blazor WebAssembly |
    | Emplacement de l’API | **Api** | Dossier contenant l’application Azure Functions |
    | Emplacement de sortie | **wwwroot** | Dossier dans la sortie de la build contenant l’application Blazor WebAssembly publiée |
    
### <a name="review-and-create"></a>Examiner et créer

1. Pour vérifier que les informations sont correctes, sélectionnez le bouton **Vérifier + créer**.

1. Pour démarrer la création de l’application web statique App Service et le provisionnement d’une action GitHub pour le déploiement, sélectionnez **Créer**.

1. Une fois le déploiement terminé, cliquez sur **Accéder à la ressource**.

1. Sélectionnez **Accéder à la ressource**.

   :::image type="content" source="media/deploy-blazor/resource-button.png" alt-text="Accédez au bouton de ressources":::

## <a name="view-the-website"></a>Voir le site web

Le déploiement d’une application statique comporte deux aspects. Le premier provisionne les ressources Azure sous-jacentes qui composent votre application. Le second est un workflow GitHub Actions qui génère et publie votre application.

Avant de pouvoir accéder à votre nouvelle application web statique, la build de déploiement doit d’abord finir de s’exécuter.

La fenêtre de vue d’ensemble de Static Web Apps présente des liens qui vous permettent d’interagir avec votre application web.

:::image type="content" source="./media/deploy-blazor/overview-window.png" alt-text="Fenêtre Vue d’ensemble":::

1. Cliquez sur la bannière intitulée _Cliquez ici pour vérifier l’état de vos exécutions GitHub Actions_ afin d’accéder aux actions GitHub en cours d’exécution sur votre dépôt. Une fois que vous avez vérifié que le travail de déploiement est terminé, vous pouvez accéder à votre site web à l’aide de l’URL générée.

2. Une fois le flux de travail GitHub Actions terminé, vous pouvez sélectionner l'_URL_ pour ouvrir le site web dans un nouvel onglet.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous ne prévoyez pas de continuer à utiliser cette application, vous pouvez supprimer l’instance Azure Static Web Apps en effectuant les étapes suivantes :

1. Ouvrez le [portail Azure](https://portal.azure.com).
1. Recherchez **my-blazor-group** dans la barre de recherche supérieure.
1. Sélectionnez le nom du groupe.
1. Sélectionnez le bouton **Supprimer**.
1. Sélectionnez **Oui** pour confirmer la suppression.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Authentification et autorisation](./authentication-authorization.md)
