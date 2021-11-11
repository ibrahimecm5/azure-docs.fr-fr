---
title: Activation de l’authentification dans une API web avec Azure Active Directory B2C
description: Cet article explique comment utiliser Azure Active Directory B2C pour protéger une API web.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 10/26/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: 38a60dee9d4a5b7c0516b8e3278e848e0be26c2e
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131424436"
---
# <a name="enable-authentication-in-your-own-web-api-by-using-azure-ad-b2c"></a>Activation de l’authentification dans une API web avec Azure AD B2C

Pour autoriser l’accès à une API web, traitez uniquement les demandes incluant un jeton d’accès valide émis par Azure Active Directory B2C (Azure AD B2C). Cet article explique comment activer l’autorisation Azure AD B2C sur une API web. Une fois que vous aurez suivi la procédure de cet article, seuls les utilisateurs qui auront obtenu un jeton d’accès valide sont autorisés à appeler vos points de terminaison d’API web.  

## <a name="prerequisites"></a>Configuration requise

Avant de commencer, lisez l’un des articles suivants, qui expliquent comment configurer l’authentification pour les applications qui appellent des API web. Ensuite, suivez la procédure de cet article pour remplacer l’exemple d’API web par la vôtre.   

- [Configuration de l’authentification dans un exemple d’application ASP.NET Core](configure-authentication-sample-web-app-with-api.md)
- [Configuration de l’authentification dans une application monopage (SPA, Single-Page Application)](configure-authentication-sample-spa-app.md)

## <a name="overview"></a>Vue d'ensemble

L’authentification par jeton garantit que les demandes adressées à une API web sont accompagnées d’un jeton d’accès valide. 

L’application effectue les opérations suivantes :

1. Elle authentifie les utilisateurs auprès d’Azure AD B2C.
1. Elle obtient un jeton d’accès doté des autorisations requises (étendues) pour le point de terminaison de l’API web.
1. Elle transmet le jeton d’accès comme jeton du porteur dans l’en-tête d’authentification de la requête HTTP au format suivant : 

    ```http
    Authorization: Bearer <token>
    ```    

L’API web effectue les opérations suivantes :

1. Elle lit le jeton du porteur à partir de l’en-tête d’authentification dans la requête HTTP.

1. Elle valide le jeton. 
1. Elle valide les autorisations (étendues) dans le jeton.
1. Elle lit les revendications encodées dans le jeton (facultatif).
1. Elle répond à la requête HTTP. 

### <a name="app-registration-overview"></a>Vue d’ensemble de l’inscription de l’application

Pour permettre à votre application de se connecter avec Azure AD B2C et d’appeler une API web, vous devez inscrire deux applications dans le répertoire d’Azure AD B2C.  

- L’inscription de *l’application web, mobile ou SPA* permet à votre application de se connecter avec Azure AD B2C. Ce processus génère un *ID d’application*, également appelé *ID client*, qui sert d’identificateur unique de l’application (par exemple *ID d’application : 1*).

- L’inscription de *l’API web* permet à votre application d’appeler une API web protégée. Elle expose les autorisations de l’API web (étendues). Ce processus génère un *ID d’application*, qui sert d’identificateur unique de l’API web (par exemple *ID d’application : 2*). Accordez à votre application (ID d’application : 1) des autorisations sur les étendues de l’API web (ID d’application : 2). 

Les inscriptions et l’architecture des applications sont décrites dans le diagramme suivant :

![Diagramme des inscriptions et de l’architecture d’une application avec l’API web.](./media/enable-authentication-web-api/app-with-api-architecture.png) 

## <a name="prepare-your-development-environment"></a>Préparer votre environnement de développement  

Dans les sections suivantes, vous allez créer un projet d’API web. Sélectionnez votre langage de programmation : ASP.NET Core ou Node.js. Vérifiez que votre ordinateur exécute l’un des éléments suivants : 

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/csharpclient)

* [Visual Studio Code](https://code.visualstudio.com/download)
* [C# pour Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) (dernière version)
* [Kit SDK .NET 5.0](https://dotnet.microsoft.com/download/dotnet)

# <a name="nodejs"></a>[Node.JS](#tab/nodejsgeneric)

* [Visual Studio Code](https://code.visualstudio.com/) ou un autre éditeur de code
* [Runtime Node.js](https://nodejs.org/en/download/)

---

## <a name="step-1-create-a-protected-web-api"></a>Étape 1 : Créer une API web protégée

Créez un projet d’API web. Tout d’abord, sélectionnez le langage de programmation que vous souhaitez utiliser : **ASP.NET Core** ou **Node.js**.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/csharpclient)

Utilisez la commande [`dotnet new`](/dotnet/core/tools/dotnet-new). La commande `dotnet new` crée un dossier nommé *TodoList* comportant les ressources du projet d’API web. Ouvrez le répertoire, puis [Visual Studio Code](https://code.visualstudio.com/). 

```dotnetcli
dotnet new webapi -o TodoList
cd TodoList
code . 
```

Lorsqu’il vous est demandé d’« ajouter les ressources requises au projet », sélectionnez **Oui**.

# <a name="nodejs"></a>[Node.JS](#tab/nodejsgeneric)

Utilisez [Express](https://expressjs.com/) pour [Node.js](https://nodejs.org/) afin de créer une API web comme suit :

1. Créez un dossier nommé *TodoList*. 
1. Dans le dossier *TodoList*, créez un fichier nommé *app.js*.
1. Dans une interface de commande, exécutez `npm init -y`. Cette commande crée un fichier *package.json* par défaut pour votre projet Node.js.
1. Dans l’interpréteur de commandes, exécutez `npm install express`. Cette commande installe le framework Express.

--- 

## <a name="step-2-install-the-dependencies"></a>Étape 2 : Installer les dépendances

Ajoutez la bibliothèque d’authentification à votre projet d’API web. Elle analyse l’en-tête d’authentification HTTP, valide le jeton et extrait les revendications. Pour plus d’informations, consultez la documentation de la bibliothèque.


# <a name="aspnet-core"></a>[ASP.NET Core](#tab/csharpclient)

Pour ajouter la bibliothèque d’authentification, installez le package en exécutant la commande suivante :

```dotnetcli
dotnet add package Microsoft.Identity.Web
```

# <a name="nodejs"></a>[Node.JS](#tab/nodejsgeneric)

Pour ajouter la bibliothèque d’authentification, installez les packages en exécutant la commande suivante :

```
npm install passport
npm install passport-azure-ad
npm install morgan
```
 
Le [package morgen](https://www.npmjs.com/package/morgan) est un intergiciel (middleware) de journalisation de requêtes HTTP pour Node.js.

---

## <a name="step-3-initiate-the-authentication-library"></a>Étape 3 : Lancer la bibliothèque d’authentification

Ajoutez le code nécessaire pour lancer la bibliothèque d’authentification.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/csharpclient)

Ouvrez *Startup.cs*, puis ajoutez les déclarations `using` suivantes au début de la classe :

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;
using Microsoft.Identity.Web;
```

Recherchez la fonction `ConfigureServices(IServiceCollection services)`. Ensuite, ajoutez l’extrait de code suivant avant la ligne de code `services.AddControllers();` :


```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Adds Microsoft Identity platform (Azure AD B2C) support to protect this Api
    services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
            .AddMicrosoftIdentityWebApi(options =>
    {
        Configuration.Bind("AzureAdB2C", options);

        options.TokenValidationParameters.NameClaimType = "name";
    },
    options => { Configuration.Bind("AzureAdB2C", options); });
    // End of the Microsoft Identity platform block    

    services.AddControllers();
}
```

Recherchez la fonction `Configure`. Ensuite, ajoutez l’extrait de code suivant juste après la ligne de code `app.UseRouting();` :


```csharp
app.UseAuthentication();
```

Après modification, votre code se présente ainsi :

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
    }

    app.UseHttpsRedirection();

    app.UseRouting();
    
    // Add the following line 
    app.UseAuthentication();
    // End of the block you add
    
    app.UseAuthorization();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapControllers();
    });
}
```

# <a name="nodejs"></a>[Node.JS](#tab/nodejsgeneric)

Ajoutez le code JavaScript suivant à votre fichier *app.js*.

```javascript
// Import the required libraries
const express = require('express');
const morgan = require('morgan');
const passport = require('passport');
const config = require('./config.json');

// Import the passport Azure AD library
const BearerStrategy = require('passport-azure-ad').BearerStrategy;

// Set the Azure AD B2C options
const options = {
    identityMetadata: `https://${config.credentials.tenantName}.b2clogin.com/${config.credentials.tenantName}.onmicrosoft.com/${config.policies.policyName}/${config.metadata.version}/${config.metadata.discovery}`,
    clientID: config.credentials.clientID,
    audience: config.credentials.clientID,
    issuer: config.credentials.issuer,
    policyName: config.policies.policyName,
    isB2C: config.settings.isB2C,
    scope: config.resource.scope,
    validateIssuer: config.settings.validateIssuer,
    loggingLevel: config.settings.loggingLevel,
    passReqToCallback: config.settings.passReqToCallback
}

// Instantiate the passport Azure AD library with the Azure AD B2C options
const bearerStrategy = new BearerStrategy(options, (token, done) => {
        // Send user info using the second argument
        done(null, { }, token);
    }
);

// Use the required libraries
const app = express();

app.use(morgan('dev'));

app.use(passport.initialize());

passport.use(bearerStrategy);

//enable CORS (for testing only -remove in production/deployment)
app.use((req, res, next) => {
    res.header('Access-Control-Allow-Origin', '*');
    res.header('Access-Control-Allow-Headers', 'Authorization, Origin, X-Requested-With, Content-Type, Accept');
    next();
});
```
--- 

## <a name="step-4-add-the-endpoints"></a>Étape 4 : Ajouter les points de terminaison

Ajoutez deux points de terminaison à votre API web :

- Point de terminaison `/public` anonyme : il retourne la date et l’heure actuelles. Utilisez-le pour déboguer votre API web avec des appels anonymes.
- Point de terminaison `/hello` protégé : il retourne la valeur de la revendication `name` dans le jeton d’accès.

**Ajout du point de terminaison anonyme**

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/csharpclient)

Dans le dossier */Controllers*, ajoutez un fichier *PublicController.cs* auquel vous intégrez l’extrait de code suivant :

```csharp
using System;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Logging;

namespace TodoList.Controllers
{
    [ApiController]
    [Route("[controller]")]
    public class PublicController : ControllerBase
    {
        private readonly ILogger<PublicController> _logger;

        public PublicController(ILogger<PublicController> logger)
        {
            _logger = logger;
        }

        [HttpGet]
        public ActionResult Get()
        {
            return Ok( new {date = DateTime.UtcNow.ToString()});
        }
    }
}
```

# <a name="nodejs"></a>[Node.JS](#tab/nodejsgeneric)

Dans le fichier *app.js*, ajoutez le code JavaScript suivant :


```javascript
// API anonymous endpoint
app.get('/public', (req, res) => res.send( {'date': new Date() } ));
```

--- 

**Ajout du point de terminaison protégé**

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/csharpclient)

Dans le dossier */Controllers*, ajoutez un fichier *HelloController.cs* auquel vous intégrez le code suivant :

```csharp
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Logging;
using Microsoft.Identity.Web.Resource;

namespace TodoList.Controllers
{
    [Authorize]
    [RequiredScope("tasks.read")]
    [ApiController]
    [Route("[controller]")]
    public class HelloController : ControllerBase
    {

        private readonly ILogger<HelloController> _logger;
        private readonly IHttpContextAccessor _contextAccessor;

        public HelloController(ILogger<HelloController> logger, IHttpContextAccessor contextAccessor)
        {
            _logger = logger;
            _contextAccessor = contextAccessor;
        }

        [HttpGet]
        public ActionResult Get()
        {
            return Ok( new { name = User.Identity.Name});
        }
    }
}
```

Le contrôleur `HelloController` est doté de l’attribut [AuthorizeAttribute](/aspnet/core/security/authorization/simple), qui limite l’accès aux utilisateurs authentifiés uniquement. 

Le contrôleur comprend également `[RequiredScope("tasks.read")]`. [RequiredScopeAttribute](/dotnet/api/microsoft.identity.web.resource.requiredscopeattribute.-ctor) vérifie que l’API web est appelée avec les bonnes étendues, `tasks.read`. 

# <a name="nodejs"></a>[Node.JS](#tab/nodejsgeneric)

Dans le fichier *app.js*, ajoutez le code JavaScript suivant : 

```javascript
// API protected endpoint
app.get('/hello',
    passport.authenticate('oauth-bearer', {session: false}),
    (req, res) => {
        console.log('Validated claims: ', req.authInfo);
        
        // Service relies on the name claim.  
        res.status(200).json({'name': req.authInfo['name']});
    }
);
```

Le point de terminaison `/hello` appelle d’abord la fonction `passport.authenticate()`. La fonction d’authentification limite l’accès aux utilisateurs authentifiés uniquement. 

La fonction d’authentification vérifie également que l’API web est appelée avec les bonnes étendues. Les étendues autorisées se trouvent dans le [fichier de configuration](#step-6-configure-the-web-api). 

--- 

## <a name="step-5-configure-the-web-server"></a>Étape 5 : Configurer le serveur web

Dans un environnement de développement, définissez l’API web de sorte qu’elle écoute sur le numéro de port des requêtes HTTP ou HTTPS entrantes. Dans cet exemple, utilisez le port HTTP 6000 et le port HTTPS 6001. L’URI de base de l’API web est `http://localhost:6000` pour HTTP et `https://localhost:6001` pour HTTPS.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/csharpclient)

Ajoutez l’extrait de code JSON suivant au fichier *appsettings.json*. 

```json
"Kestrel": {
    "EndPoints": {
      "Http": {
        "Url": "http://localhost:6000"
      },
      "Https": {
         "Url": "https://localhost:6001"   
        }
    }
  }
```

# <a name="nodejs"></a>[Node.JS](#tab/nodejsgeneric)

Ajoutez le code JavaScript suivant au fichier *app.js*. Il est également possible de [configurer des points de terminaison HTTP et HTTPS pour l’application Node](https://github.com/expressjs/express/wiki/Migrating-from-2.x-to-3.x#application-function). 

```javascript
// Starts listening on port 6000
const port = process.env.PORT || 6000;

app.listen(port, () => {
    console.log('Listening on port ' + port);
});
```

---

## <a name="step-6-configure-the-web-api"></a>Étape 6 : Configurer l’API web

Ajoutez des configurations à un fichier de configuration. Ce fichier contient des informations sur votre fournisseur d’identité Azure AD B2C. L’application API web les utilise pour valider le jeton d’accès transmis comme jeton du porteur par l’application web.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/csharpclient)

Dans le dossier racine du projet, ouvrez le fichier *appsettings.json*, puis ajoutez les paramètres suivants :

```json
{
  "AzureAdB2C": {
    "Instance": "https://contoso.b2clogin.com",
    "Domain": "contoso.onmicrosoft.com",
    "ClientId": "<web-api-app-application-id>",
    "SignedOutCallbackPath": "/signout/<your-sign-up-in-policy>",
    "SignUpSignInPolicyId": "<your-sign-up-in-policy>"
  },
  // More settings here
}
```

Dans le fichier *appsettings.json*, mettez à jour les propriétés suivantes : 

|Section  |Clé  |Valeur  |
|---------|---------|---------|
|AzureAdB2C|Instance| La première partie du [nom de locataire](tenant-management.md#get-your-tenant-name) Azure AD B2C (par exemple `https://contoso.b2clogin.com`).|
|AzureAdB2C|Domain| [Nom du locataire](tenant-management.md#get-your-tenant-name) complet Azure AD B2C (par exemple `contoso.onmicrosoft.com`).|
|AzureAdB2C|ClientId| ID de l’application API web. Dans le [diagramme précédent](#app-registration-overview), il s’agit de l’application *ID d’application : 2*. Pour savoir comment obtenir l’ID d’inscription de votre application API web, consultez [Prérequis](#prerequisites). |
|AzureAdB2C|SignUpSignInPolicyId|Flux d’utilisateurs ou stratégie personnalisée. Pour savoir comment obtenir votre flux d’utilisateurs ou votre stratégie, consultez [Prérequis](#prerequisites).  |

# <a name="nodejs"></a>[Node.JS](#tab/nodejsgeneric)

Dans le dossier racine du projet, créez un fichier *config.json* auquel vous ajoutez l’extrait de code JSON suivant :  

```json
{
    "credentials": {
        "tenantName": "<your-tenant-name>",
        "clientID": "<your-webapi-application-ID>",
        "issuer": "https://<your-tenant-name>.b2clogin.com/<your-tenant-ID>/v2.0/"
    },
    "policies": {
        "policyName": "b2c_1_susi"
    },
    "resource": {
        "scope": ["tasks.read"]
    },
    "metadata": {
        "discovery": ".well-known/openid-configuration",
        "version": "v2.0"
    },
    "settings": {
        "isB2C": true,
        "validateIssuer": true,
        "passReqToCallback": false,
        "loggingLevel": "info"
    }
}
```

Dans le fichier *config.json*, mettez à jour les propriétés suivantes :

|Section  |Clé  |Valeur  |
|---------|---------|---------|
| credentials | tenantName | Première partie du [nom de locataire](tenant-management.md#get-your-tenant-name) Azure AD B2C (par exemple `contoso`).|
| credentials |clientID | ID de l’application API web. Dans le [diagramme précédent](#app-registration-overview), il s’agit de l’application *ID d’application : 2*. Pour savoir comment obtenir l’ID d’inscription de votre application API web, consultez [Prérequis](#prerequisites). |
| credentials | émetteur| Valeur de la revendication `iss` de l’émetteur de jeton. Par défaut, Azure AD B2C retourne le jeton au format `https://<your-tenant-name>.b2clogin.com/<your-tenant-ID>/v2.0/`. Remplacez `<your-tenant-name>` par la première partie de votre [nom de locataire](tenant-management.md#get-your-tenant-name) Azure AD B2C, et `<your-tenant-ID>` par votre [ID de locataire Azure AD B2C](tenant-management.md#get-your-tenant-id). |
| stratégies | policyName | Flux d’utilisateurs ou stratégie personnalisée. Pour savoir comment obtenir votre flux d’utilisateurs ou votre stratégie, consultez [Prérequis](#prerequisites).|
| resource | scope | Étendues de l’inscription de votre application API web. Pour savoir comment obtenir l’étendue de votre API web, consultez [Prérequis](#prerequisites).|

---

## <a name="step-7-run-and-test-the-web-api"></a>Étape 7 : Exécuter et tester l’API web

Enfin, exécutez l’API web avec vos paramètres d’environnement Azure AD B2C. 

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/csharpclient)

Dans l’interface de commande, lancez l’application web en exécutant la commande suivante :

```bush
 dotnet run
```

La sortie suivante s’affiche, ce qui signifie que votre application est opérationnelle et prête à recevoir des demandes.

```
Now listening on: http://localhost:6000
```

Pour arrêter le programme, sélectionnez Ctrl+C dans l’interface de commande. Vous pouvez réexécuter l’application avec la commande `node app.js`.

> [!TIP]
> Pour exécuter la commande `dotnet run`, vous pouvez également utiliser le [débogueur Visual Studio Code](https://code.visualstudio.com/docs/editor/debugging). Ce débogueur intégré permet d’accélérer la boucle de modification, de compilation et de débogage.

Ouvrez un navigateur et accédez à `http://localhost:6000/public`. Dans la fenêtre du navigateur, le texte suivant s’affiche, ainsi que la date et l’heure actuelles.

# <a name="nodejs"></a>[Node.JS](#tab/nodejsgeneric)

Dans l’interface de commande, lancez l’application web en exécutant la commande suivante :

```bush
node app.js
```

La sortie suivante s’affiche, ce qui signifie que votre application est opérationnelle et prête à recevoir des demandes.

```
Example app listening on port 6000!
```

Pour arrêter le programme, sélectionnez Ctrl+C dans l’interface de commande. Vous pouvez réexécuter l’application avec la commande `node app.js`.

> [!TIP]
> Pour exécuter la commande `node app.js`, vous pouvez également utiliser le [débogueur Visual Studio Code](https://code.visualstudio.com/docs/editor/debugging). Ce débogueur intégré permet d’accélérer la boucle de modification, de compilation et de débogage.

Ouvrez un navigateur et accédez à `http://localhost:6000/public`. Dans la fenêtre du navigateur, le texte suivant s’affiche, ainsi que la date et l’heure actuelles.

---

## <a name="step-8-call-the-web-api-from-your-app"></a>Étape 8 : Appeler l’API web à partir de l’application

Essayez d’appeler le point de terminaison d’API web protégé sans jeton d’accès. Ouvrez un navigateur et accédez à `http://localhost:6000/hello`. L’API web renvoie un message d’erreur HTTP non autorisé, ce qui confirme qu’elle est bien protégée par un jeton de porteur.

Continuez à configurer votre application pour appeler l’API web. Pour plus d’informations, consultez la section [Prérequis](#prerequisites).

Regardez cette vidéo pour en savoir plus sur certaines des meilleures pratiques à connaître lorsque vous intégrez Azure AD B2C avec une API.

>[!Video https://www.youtube.com/embed/wuUu71RcsIo]

## <a name="next-steps"></a>Étapes suivantes

Retrouvez l’exemple complet sur GitHub :

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/csharpclient)
* Procurez-vous l’API web avec la [Bibliothèque d’identités Microsoft](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/4-WebApp-your-API/4-2-B2C/TodoListService).

# <a name="nodejs"></a>[Node.JS](#tab/nodejsgeneric)
* Procurez-vous l’API web avec la [Bibliothèque Passport.js](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi).
