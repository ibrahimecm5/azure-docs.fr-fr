---
title: Ajouter une API dans Azure Static Web Apps avec Azure Functions
description: Prenez en main Azure Static Web Apps en ajoutant une API serverless à votre application web statique à l’aide d’Azure Functions.
services: static-web-apps
author: manekinekko
ms.service: static-web-apps
ms.topic: how-to
ms.date: 05/14/2021
ms.author: wachegha
ms.custom: devx-track-js
ms.openlocfilehash: be664a5bf29c95143e7706b2df2b49648959f584
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131013666"
---
# <a name="add-an-api-to-azure-static-web-apps-with-azure-functions"></a>Ajouter une API dans Azure Static Web Apps avec Azure Functions

Vous pouvez ajouter des API serverless à Azure Static Web Apps qui sont alimentées par Azure Functions. Cet article explique comment ajouter et déployer une API sur un site Azure Static Web Apps.

> [!NOTE]
> Les fonctions fournies par défaut dans Static Web Apps sont préconfigurées pour fournir des points de terminaison d’API sécurisés et ne prennent en charge que les fonctions déclenchées par HTTP. Pour plus d’informations sur les différences avec les applications Azure Functions autonomes, consultez [Prise en charge d’API avec Azure Functions](apis.md).

## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif.
  - Si vous ne possédez pas de compte, vous pouvez [créer un compte gratuit](https://azure.microsoft.com/free).
- [Visual Studio Code](https://code.visualstudio.com/)
- [Extension Azure Static Web Apps](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestaticwebapps) pour Visual Studio Code
- [Node.js](https://nodejs.org/download/) pour exécuter l’API et l’application front-end

## <a name="create-the-static-web-app"></a>Créer l’application web statique

Avant d’ajouter une API, créez et déployez une application front-end sur Azure Static Web Apps. Utilisez une application existante que vous avez déjà déployée ou créez-en une en suivant le guide de démarrage rapide [Créer votre premier site statique avec Azure Static Web Apps](getting-started.md).

Dans Visual Studio Code, ouvrez la racine du référentiel de votre application. La structure de dossiers contient la source de votre application front-end et le flux de travail GitHub Static Web Apps dans le dossier _.github/workflows_.

```Files
├── .github
│   └── workflows
│       └── azure-static-web-apps-<DEFAULT_HOSTNAME>.yml
│
└── (folders and files from your static web app)
```

## <a name="create-the-api"></a>Création de l’API

Vous créez un projet Azure Functions pour l’API de votre application web statique. Par défaut, l’extension Visual Studio Code Static Web Apps crée le projet dans un dossier nommé _api_ à la racine de votre référentiel.

1. Appuyez sur <kbd>F1</kbd> pour ouvrir la palette de commandes.

1. Sélectionnez **Azure Static Web Apps : créer une fonction HTTP...** . Si vous êtes invité à installer l’extension Azure Functions, installez-la et réexécutez cette commande.

1. Quand vous y êtes invité, entrez les valeurs suivantes :

    | Prompt | Valeur |
    | --- | --- |
    | Sélectionner une langue | **JavaScript** |
    | Fournir un nom de fonction | **message** |

    Un projet Azure Functions est généré avec une fonction déclenchée par HTTP. Votre application a maintenant une structure de projet similaire à celle de l’exemple suivant.

    ```Files
    ├── .github
    │   └── workflows
    │       └── azure-static-web-apps-<DEFAULT_HOSTNAME>.yml
    │
    ├── api
    │   ├── message
    │   │   ├── function.json
    │   │   └── index.js
    │   ├── host.json
    │   ├── local.settings.json
    │   └── package.json
    │
    └── (folders and files from your static web app)
    ```

1. Ensuite, modifiez la fonction `message` pour retourner un message au front-end. Mettez à jour la fonction dans _api/message/index.js_ avec le code suivant.

    ```javascript
    module.exports = async function (context, req) {
        context.res.json({
            text: "Hello from the API"
        });
    };
    ```

> [!TIP]
> Vous pouvez ajouter d’autres fonctions d’API en exécutant à nouveau la commande **Azure Static Web Apps : créer une fonction HTTP...** .

## <a name="update-the-frontend-app-to-call-the-api"></a>Mettre à jour l’application front-end pour appeler l’API

Mettez à jour votre application front-end pour appeler l’API dans `/api/message` et afficher le message de réponse.

Si vous avez utilisé les guides de démarrage rapide pour créer l’application, suivez les instructions ci-dessous pour appliquer les mises à jour.

# <a name="no-framework"></a>[Pas de framework](#tab/vanilla-javascript)

Mettez à jour le contenu du fichier _src/index.html_ à l’aide du code suivant pour récupérer le texte de la fonction API et l’afficher à l’écran.

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <link rel="stylesheet" href="styles.css">
    <title>Vanilla JavaScript App</title>
</head>

<body>
    <main>
    <h1>Vanilla JavaScript App</h1>
    <p>Loading content from the API: <b id="name">...</b></p>
    </main>

    <script>
    (async function() {
        const { text } = await( await fetch(`/api/message`)).json();
        document.querySelector('#name').textContent = text;
    }())
    </script>
</body>

</html>
```

# <a name="angular"></a>[Angular](#tab/angular)

1. Mettez à jour le contenu de _src/app/app.module.ts_ avec le code suivant pour activer `HttpClient` dans votre application.

    ```typescript
    import { BrowserModule } from "@angular/platform-browser";
    import { NgModule } from "@angular/core";
    import { HttpClientModule } from '@angular/common/http';
    
    import { AppComponent } from "./app.component";
    
    @NgModule({
      declarations: [AppComponent],
      imports: [BrowserModule, HttpClientModule],
      bootstrap: [AppComponent]
    })
    export class AppModule {}
    ```

1. Mettez à jour le contenu de _src/app/app.component.ts_ à l’aide du code suivant pour extraire le texte de la fonction API et l’afficher à l’écran.

    ```typescript
    import { HttpClient } from '@angular/common/http';
    import { Component } from '@angular/core';
    
    @Component({
      selector: 'app-root',
      template: `<div>{{message}}</div>`,
    })
    export class AppComponent {
      message = '';
    
      constructor(private http: HttpClient) {
        this.http.get('/api/message')
          .subscribe((resp: any) => this.message = resp.text);
      }
    }
    ```

# <a name="react"></a>[React](#tab/react)

Mettez à jour le contenu de _src/App.js_ à l’aide du code suivant pour extraire le texte de la fonction API et l’afficher à l’écran.

```javascript
import React, { useState, useEffect } from 'react';

function App() {
  const [data, setData] = useState('');

  useEffect(() => {
    (async function () {
      const { text } = await( await fetch(`/api/message`)).json();
      setData(text);
    })();
  });

  return <div>{data}</div>;
}

export default App;
```

# <a name="vue"></a>[Vue](#tab/vue)

Mettez à jour le contenu de _src/App.vue_ à l’aide du code suivant pour extraire le texte de la fonction API et l’afficher à l’écran.

```javascript
<template>
  <div>{{ message }}</div>
</template>

<script>
export default {
  name: "App",
  data() {
    return {
      message: ""
    };
  },
  async mounted() {
    const { text } = await (await fetch("/api/message")).json();
    this.message = text;
  }
};
</script>
```

---

## <a name="run-the-frontend-and-api-locally"></a>Exécuter le front-end et l’API localement

Pour exécuter votre application front-end et votre API ensemble localement, Azure Static Web Apps fournit une interface CLI qui émule l’environnement cloud. L’interface CLI utilise Azure Functions Core Tools pour exécuter l’API.

### <a name="install-command-line-tools"></a>Installer des outils en ligne de commande

Vérifiez que vous avez installé les outils en ligne de commande nécessaires.

1. Installez la CLI Azure Static Web Apps.
    ```bash
    npm install -g @azure/static-web-apps-cli
    ```

1. Installez Azure Functions Core Tools V3.
    ```bash
    npm install -g azure-functions-core-tools@3
    ```

### <a name="build-frontend-app"></a>Générer une application front-end

Si votre application utilise un framework, créez l’application pour générer la sortie avant d’exécuter l’interface CLI Static Web Apps.

# <a name="no-framework"></a>[Pas de framework](#tab/vanilla-javascript)

Il n’est pas nécessaire de générer l’application.

# <a name="angular"></a>[Angular](#tab/angular)

Générez l’application dans le dossier _dist/angular-basic_.

```bash
npm run build --prod
```

# <a name="react"></a>[React](#tab/react)

Générez l’application dans le dossier _build_.

```bash
npm run build
```

# <a name="vue"></a>[Vue](#tab/vue)

Générez l’application dans le dossier _dist_.

```bash
npm run build
```

---

### <a name="start-the-cli"></a>Démarrer l’interface de ligne de commande

Exécutez l’application front-end et l’API ensemble en démarrant l’application avec l’interface CLI Static Web Apps. L’exécution des deux parties de votre application permet à l’interface CLI de servir la sortie de génération de votre front-end à partir d’un dossier, et rend l’API accessible à l’application en cours d’exécution.

1. À la racine de votre référentiel, démarrez l’interface CLI Static Web Apps à l’aide de la commande `start`. Ajustez les arguments si votre application a une structure de dossiers différente.

    # <a name="no-framework"></a>[Pas de framework](#tab/vanilla-javascript)

    Transmettez le dossier actif (`src`) et le dossier d’API (`api`) à l’interface CLI.
     
    ```bash
    swa start src --api-location api
    ```

    # <a name="angular"></a>[Angular](#tab/angular)

    Transmettez le dossier de sortie de génération (`dist/angular-basic`) et le dossier d’API (`api`) à l’interface CLI.

    ```bash
    swa start dist/angular-basic --api-location api
    ```

    # <a name="react"></a>[React](#tab/react)

    Transmettez le dossier de sortie de génération (`build`) et le dossier d’API (`api`) à l’interface CLI.

    ```bash
    swa start build --api-location api
    ```

    # <a name="vue"></a>[Vue](#tab/vue)

    Transmettez le dossier de sortie de génération (`dist`) et le dossier d’API (`api`) à l’interface CLI.

    ```bash
    swa start dist --api-location api
    ```

    ---

1. Quand les processus de la CLI démarrent, accédez à votre application à l’adresse `http://localhost:4280/`. Notez que la page appelle l’API et affiche sa sortie, `Hello from the API`.

1. Pour arrêter l’interface CLI, tapez <kbd>Ctrl + C</kbd>.

## <a name="add-api-location-to-workflow"></a>Ajouter l’emplacement de l’API au flux de travail

Avant de pouvoir déployer votre application sur Azure, mettez à jour le flux de travail GitHub Actions de votre référentiel avec l’emplacement correct de votre dossier d’API.

1. Ouvrez votre flux de travail à l’adresse _.github/workflows/azure-static-web-apps-\<DEFAULT-HOSTNAME>.yml_.

1. Recherchez la propriété `api_location` et définissez la valeur sur `api`.
1. Enregistrez le fichier .

## <a name="deploy-changes"></a>Déployer les modifications

Pour publier les modifications apportées à votre application web statique dans Azure, validez et envoyez votre code au référentiel GitHub à distance.

1. Appuyez sur <kbd>F1</kbd> pour ouvrir la palette de commandes.

1. Sélectionnez la commande **Git: Commit All**.

1. Lorsque vous êtes invité à entrer un message de validation, entrez **ajouter l’API** et validez toutes les modifications apportées à votre référentiel git local.

1. Appuyez sur <kbd>F1</kbd> pour ouvrir la palette de commandes.

1. Sélectionnez la commande **Git: push**.

    Vos modifications sont envoyées (push) vers le référentiel à distance dans GitHub, déclenchant le flux de travail GitHub Actions Static Web Apps pour générer et déployer votre application.

1. Ouvrez votre référentiel dans GitHub pour superviser l’état d’exécution de votre flux de travail.

1. Une fois l’exécution du flux de travail terminée, visitez votre application web statique pour examiner vos modifications.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Configurer les paramètres d’application](./application-settings.md)
