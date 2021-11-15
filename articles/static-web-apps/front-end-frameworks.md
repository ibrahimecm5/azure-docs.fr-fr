---
title: Configurer des infrastructures frontales avec Azure Static Web Apps
description: Paramètres pour les infrastructures frontales populaires nécessaires pour Azure static Web Apps
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 07/18/2020
ms.author: cshoe
ms.openlocfilehash: 95f7c35cc33c1d174cd228bd053dbfea6c850135
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131841693"
---
# <a name="configure-front-end-frameworks-and-libraries-with-azure-static-web-apps"></a>Configurer des infrastructures et bibliothèques frontales avec Azure Static Web Apps

Azure Static Web Apps nécessite que vous disposiez des valeurs de configuration appropriées dans le [fichier de configuration de build](build-configuration.md) pour votre infrastructure ou bibliothèque frontale.

## <a name="configuration"></a>Configuration

Le tableau suivant répertorie les paramètres d’une série d’infrastructures et de bibliothèques<sup>1</sup>.

Les éléments suivants expliquent la présence des colonnes du tableau :

- **Emplacement de sortie** : Répertorie la valeur de `output_location`, qui est le [dossier des versions générées des fichiers d’application](build-configuration.md).

- **Commande de compilation personnalisée** : Lorsque l’infrastructure requiert une commande différente de `npm run build` ou `npm run azure:build`, vous pouvez définir une [commande de compilation personnalisée](build-configuration.md#custom-build-commands).

| Framework | Emplacement de l’artefact de l’application | Commande de compilation personnalisée |
|--|--|--|
| [Alpine.js](https://github.com/alpinejs/alpine/) | `/` | n/a <sup>2</sup> |
| [Angular](https://angular.io/) | `dist/<APP_NAME>` | `npm run build -- --configuration production` |
| [Angular Universal](https://angular.io/guide/universal) | `dist/<APP_NAME>/browser` | `npm run prerender` |
| [Astro](https://astro.build) | `dist` | n/a |
| [Aurelia](https://aurelia.io/) | `dist` | n/a |
| [Backbone.js](https://backbonejs.org/) | `/` | n/a |
| [Blazor](https://dotnet.microsoft.com/apps/aspnet/web-apps/blazor) | `wwwroot` | n/a |
| [Ember](https://emberjs.com/) | `dist` | n/a |
| [Flutter](https://flutter.dev/) | `build/web` | `flutter build web` |
| [Framework7](https://framework7.io/) | `www` | `npm run build-prod` |
| [Glimmer](https://glimmerjs.com/) | `dist` | n/a |
| [HTML](https://developer.mozilla.org/docs/Web/HTML) | `/` | n/a |
| [Hyperapp](https://hyperapp.dev/) | `/` | n/a |
| [JavaScript](https://developer.mozilla.org/docs/Web/javascript) | `/` | n/a |
| [JQuery](https://jquery.com/) | `/` | n/a |
| [KnockoutJS](https://knockoutjs.com/) | `dist` | n/a |
| [LitElement](https://lit-element.polymer-project.org/) | `dist` | n/a |
| [Marko](https://markojs.com/) | `public` | n/a |
| [Meteor](https://www.meteor.com/) | `bundle` | n/a |
| [Mithril](https://mithril.js.org/) | `dist` | n/a |
| [Meri](https://www.polymer-project.org/) | `build/default` | n/a |
| [Preact](https://preactjs.com/) | `build` | n/a |
| [React](https://reactjs.org/) | `build` | n/a |
| [RedwoodJS](https://redwoodjs.com/) | `web/dist` | `yarn rw build` |
| [Stencil](https://stenciljs.com/) | `www` | n/a |
| [Svelte](https://svelte.dev/) | `public` | n/a |
| [Three.js](https://threejs.org/) | `/` | n/a |
| [TypeScript](https://www.typescriptlang.org/) | `dist` | n/a |
| [Vue.js](https://vuejs.org/) | `dist` | n/a |

<sup>1</sup> Le tableau ci-dessus n’est pas la liste exhaustive des infrastructures et des bibliothèques qui fonctionnent avec Azure Static Web Apps.

<sup>2</sup> Non applicable

## <a name="next-steps"></a>Étapes suivantes

- [Configuration de build et de workflow](build-configuration.md)
