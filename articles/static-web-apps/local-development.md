---
title: Configurer le développement local pour Azure Static Web Apps
description: Apprendre à définir votre environnement de développement local pour Azure Static Web Apps
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: how-to
ms.date: 10/21/2021
ms.author: cshoe
ms.custom: devx-track-js
ms.openlocfilehash: 7419eea1503d8d0692bd1b112226c8ce626d9776
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130261573"
---
# <a name="set-up-local-development-for-azure-static-web-apps"></a>Configurer le développement local pour Azure Static Web Apps

Lorsqu'il est publié dans le cloud, un site Azure Static Web Apps englobe de nombreux services qui fonctionnent ensemble comme s'il s'agissait de la même application. Ces services comprennent :

- L'application web statique
- L'API Azure Functions
- Les services d'authentification et d'autorisation
- Les services de routage et de configuration

Ces services doivent communiquer entre eux, et Azure static Web Apps gère cette intégration pour vous dans le cloud.

Lorsqu'ils sont exécutés localement, en revanche, ces services ne sont pas automatiquement liés entre eux.

Pour offrir une expérience similaire à celle dont vous bénéficiez dans Azure, l'[interface de ligne de commande (CLI) Azure Static Web Apps](https://github.com/Azure/static-web-apps-cli) fournit les services suivants :

- Un serveur local de sites statiques
- Un proxy vers le serveur de développement de l'infrastructure frontale
- Un proxy vers vos points de terminaison d'API, disponible via Azure Functions Core Tools
- Un serveur d'authentification et d'autorisation fictif
- La mise en œuvre des itinéraires et paramètres de configuration locaux

> [!NOTE]
> Souvent, les sites créés avec une infrastructure frontale requièrent un paramètre de configuration de proxy pour gérer correctement les demandes sous l’itinéraire `api`. En cas d’utilisation de la CLI d’Azure Static Web Apps, la valeur de l’emplacement du proxy est `/api` et, sans la CLI, la valeur est `http://localhost:7071/api`.

## <a name="how-it-works"></a>Fonctionnement

Le graphique suivant montre comment les demandes sont gérées localement.

:::image type="content" source="media/local-development/cli-conceptual.png" alt-text="Flux de demandes et de réponses de l'interface CLI Azure Static Web App":::

> [!IMPORTANT]
> Naviguez jusqu'à `http://localhost:4280` pour accéder à l'application servie par l'interface CLI.

- Les **demandes** adressées au port `4280` sont transférées vers le serveur approprié en fonction de leur type.

- Les demandes de **contenu statique**, telles que HTML ou CSS, sont gérées par le serveur de contenu statique de l'interface CLI interne ou par le serveur de l'infrastructure frontale à des fins de débogage.

- Les demandes **d'authentification et d'autorisation** sont gérées par un émulateur, qui fournit un profil d'identité factice à votre application.

- Le **runtime Functions Core Tools** gère les demandes envoyées à l'API du site.

- Les **réponses** de tous les services sont renvoyées au navigateur comme s'il s’agissait d'une seule et même application.

## <a name="prerequisites"></a>Prérequis

- **Site Azure Static Web Apps existant** : si vous n'en avez pas, commencez avec l'application de démarrage [vanilla-api](https://github.com/staticwebdev/vanilla-api/generate?return_to=/staticwebdev/vanilla-api/generate).
- **[Node.js](https://nodejs.org) avec npm** : exécutez la version [Node.js LTS](https://nodejs.org), qui inclut l'accès à [npm](https://www.npmjs.com/).
- **[Visual Studio Code](https://code.visualstudio.com/)**  : utilisé pour le débogage de l'application API, mais non requis pour l'interface CLI.
- **[Azure Functions Core Tools](https://github.com/Azure/azure-functions-core-tools#installing)**  : Nécessaires pour exécuter l’API localement.

## <a name="get-started"></a>Bien démarrer

Ouvrez un terminal dans le dossier racine de votre site Azure Static Web Apps existant.

1. Installez l'interface CLI.

    ```console
    npm install -g @azure/static-web-apps-cli
    ```

1. Si nécessaire, générez votre application.

    Exécutez `npm run build`, ou la commande équivalente de votre projet.

1. Accédez au répertoire de sortie de votre application. Les dossiers de sortie sont souvent nommés _build_ (ou nom similaire).

1. Démarrez l'interface CLI.

    ```console
    swa start
    ```

1. Accédez à `http://localhost:4280` pour afficher l'application dans le navigateur.

### <a name="other-ways-to-start-the-cli"></a>Autres façons de démarrer l'interface CLI

| Description | Commande |
|--- | --- |
| Servir un dossier spécifique | `swa start ./output-folder` |
| Utiliser un serveur de développement d'infrastructure en cours d'exécution | `swa start http://localhost:3000` |
| Démarrer une application Functions dans un dossier | `swa start ./output-folder --api-location ./api` |
| Utiliser une application Functions en cours d'exécution | `swa start ./output-folder --api-location http://localhost:7071` |

## <a name="authorization-and-authentication-emulation"></a>Émulation d'autorisation et d'authentification

L'interface CLI Static Web Apps émule le [flux de sécurité](./authentication-authorization.md) implémenté dans Azure. Lorsqu'un utilisateur se connecte, vous pouvez définir un profil d'identité factice renvoyé à l'application.

Par exemple, lorsque vous tentez d'accéder à `/.auth/login/github`, une page est renvoyée pour vous permettre de définir un profil d'identité.

> [!NOTE]
> L'émulateur fonctionne avec n'importe quel fournisseur de sécurité, pas seulement GitHub.

:::image type="content" source="media/local-development/auth-emulator.png" alt-text="Émulateur d'authentification et d'autorisation au niveau local":::

L'émulateur présente une page qui vous permet de fournir les valeurs suivantes pour le [principal de client](./user-information.md#client-principal-data) :

| Valeur | Description |
| --- | --- |
| **Nom d’utilisateur** | Nom du compte associé au fournisseur de sécurité. Cette valeur apparaît en tant que propriété `userDetails` dans le principal de client, et elle est générée automatiquement si vous ne fournissez pas de valeur. |
| **ID d'utilisateur** | Valeur générée automatiquement par l'interface CLI.  |
| **Rôles** | Liste de noms de rôles, chaque nom figurant sur une nouvelle ligne.  |

Une fois connecté :

- Vous pouvez utiliser le point de terminaison `/.auth/me` ou un point de terminaison de fonction pour récupérer le [principal de client](./user-information.md)de l'utilisateur.

- Accédez à `/.auth/logout` pour effacer le principal de client et déconnecter l'utilisateur fictif.

## <a name="debugging"></a>Débogage

Il existe deux contextes de débogage dans une application web statique. Le premier concerne le site de contenu statique et le second les fonctions API. Un débogage local est possible en permettant à l'interface CLI Static Web Apps d'utiliser des serveurs de développement pour l'un ou l'autre de ces contextes (ou les deux).

Les étapes suivantes présentent un scénario commun qui utilise des serveurs de développement pour les deux contextes de débogage.

1. Démarrez le serveur de développement de site statique. Cette commande est spécifique à l'infrastructure frontale que vous utilisez, mais elle est souvent fournie sous forme de commandes telles que `npm run build`, `npm start` ou `npm run dev`.

1. Ouvrez le dossier de l'application API dans Visual Studio Code et démarrez une session de débogage.

1. Démarrez l’interface CLI de Static Web Apps en utilisant la commande suivante.


    ```console
    swa start http://localhost:<DEV-SERVER-PORT-NUMBER> --api-location http://localhost:7071
    ```

    Remplacez `<DEV-SERVER-PORT-NUMBER>` par le numéro de port du serveur de développement.

Les captures d'écran suivantes illustrent les terminaux d'un scénario de débogage classique :

Le site de contenu statique s'exécute via `npm run dev`.

:::image type="content" source="media/local-development/run-dev-static-server.png" alt-text="Serveur de développement de site statique":::

L'application API Azure Functions exécute une session de débogage dans Visual Studio Code.

:::image type="content" source="media/local-development/visual-studio-code-debugging.png" alt-text="Débogage d'API dans Visual Studio Code":::

L'interface CLI Static Web Apps est lancée à l'aide des deux serveurs de développement.

:::image type="content" source="media/local-development/static-web-apps-cli-terminal.png" alt-text="Terminal CLI Azure Static Web Apps":::

Désormais, les demandes qui passent par le port `4280` sont acheminées vers le serveur de développement de contenu statique ou la session de débogage d'API.

Pour plus d'informations sur les différents scénarios de débogage, avec des conseils sur la personnalisation des ports et des adresses de serveur, consultez le [Référentiel de l'interface CLI Azure Static Web Apps](https://github.com/Azure/static-web-apps-cli).

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Configuration de votre application](configuration.md)
