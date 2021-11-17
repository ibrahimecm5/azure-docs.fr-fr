---
title: 'Tutoriel : Ajouter une authentification et des autorisations à votre application lors de l’utilisation d’Azure Web PubSub'
description: Découvrez comment ajouter une authentification et des autorisations à votre application lors de l’utilisation d’Azure Web PubSub.
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: tutorial
ms.date: 11/01/2021
ms.openlocfilehash: 027cd7197167a3667748c2c470e17b83aa3bf03d
ms.sourcegitcommit: 96deccc7988fca3218378a92b3ab685a5123fb73
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131578722"
---
# <a name="tutorial-add-authentication-and-permissions-to-your-application-when-using-azure-web-pubsub"></a>Tutoriel : Ajouter une authentification et des autorisations à votre application lors de l’utilisation d’Azure Web PubSub

Dans [Créer une application de conversation](./tutorial-build-chat.md), vous avez appris à utiliser les API WebSocket pour envoyer et recevoir des données avec Azure Web PubSub. Vous avez peut-être remarqué que, pour simplifier, aucune authentification n’est exigée. Même si Azure Web PubSub exige un jeton d’accès pour se connecter, l’API `negotiate` utilisée dans le tutoriel pour générer le jeton d’accès n’a pas besoin d’authentification. N’importe qui peut donc appeler cette API pour obtenir un jeton d’accès.

Dans une application réelle, il est généralement préférable que l’utilisateur se connecte d’abord, avant de pouvoir utiliser l’application. Dans ce tutoriel, vous allez apprendre à intégrer Web PubSub au système d’authentification et d’autorisation de votre application, afin de la rendre plus sécurisée.

L’exemple de code complet de ce tutoriel est disponible sur [GitHub][code].

Dans ce tutoriel, vous apprenez à effectuer les opérations suivantes :

> [!div class="checklist"]
> * Activer l’authentification GitHub
> * Ajouter un middleware (intergiciel) d’authentification à votre application
> * Ajouter des autorisations aux clients

## <a name="add-authentication-to-the-chat-room-app"></a>Ajouter l’authentification à l’application de la salle de conversation

Ce tutoriel réutilise l’application de conversation créée dans [Créer une application de conversation](./tutorial-build-chat.md). Vous pouvez également cloner l’exemple de code complet de l’application de conversation à partir de [GitHub][chat-js]. 

Dans ce tutoriel, vous ajoutez une authentification à l’application de conversation et vous l’intégrez à Web PubSub.

En premier lieu, ajoutez l’authentification GitHub à la salle de conversation, afin que l’utilisateur puisse utiliser un compte GitHub pour se connecter.

1.  Installez des dépendances.

    ```bash
    npm install --save cookie-parser
    npm install --save express-session
    npm install --save passport
    npm install --save passport-github2
    ```

1.  Activez l’authentification GitHub en ajoutant le code suivant à `server.js` :

    ```javascript
    const app = express();

    const users = {};
    passport.use(
      new GitHubStrategy({
        clientID: process.argv[3],
        clientSecret: process.argv[4]
      },
      (accessToken, refreshToken, profile, done) => {
        users[profile.id] = profile;
        return done(null, profile);
      }
    ));

    passport.serializeUser((user, done) => {
      done(null, user.id);
    });

    passport.deserializeUser((id, done) => {
      if (users[id]) return done(null, users[id]);
      return done(`invalid user id: ${id}`);
    });

    app.use(cookieParser());
    app.use(session({
      resave: false,
      saveUninitialized: true,
      secret: 'keyboard cat'
    }));
    app.use(passport.initialize());
    app.use(passport.session());
    app.get('/auth/github', passport.authenticate('github', { scope: ['user:email'] }));
    app.get('/auth/github/callback', passport.authenticate('github', { successRedirect: '/' }));
    ```

    Le code précédent utilise [Passport.js](http://www.passportjs.org/) pour activer l’authentification GitHub. Voici une illustration simple de son fonctionnement :

    1. `/auth/github` redirige vers github.com pour la connexion.
    1. Une fois que vous êtes connecté, GitHub vous redirige vers `/auth/github/callback` avec un code pour que votre application achève l’authentification. (Pour voir comment le profil retourné par GitHub est vérifié et conservé sur le serveur, consultez le rappel de vérification dans `passport.use()`.)
    1. Dès l’authentification effectuée, vous êtes redirigé vers la page d’accueil (`/`) du site.
 
    Pour plus d’informations sur GitHub OAuth et Passport.js, consultez les articles suivants :

    - [Autorisation des applications OAuth](https://docs.github.com/en/developers/apps/authorizing-oauth-apps)
    - [Documentation Passport.js](http://www.passportjs.org/docs/)

    Pour tester cela, vous devez d’abord créer une application GitHub OAuth :

    1. Accédez à https://www.github.com, ouvrez votre profil et sélectionnez **Settings (Paramètres)**  > **Developer settings (Paramètres de développement)** .
    1. Accédez à OAuth Apps (Applications OAuth) et sélectionnez **New OAuth App** (Nouvelle application OAuth).
    1. Renseignez le nom de l’application et l’URL de la page d’accueil (l’URL peut être ce que vous voulez), puis définissez **Authorization callback URL** (URL de rappel d’autorisation) sur `http://localhost:8080/auth/github/callback`. Cette URL correspond à l’API de rappel que vous avez exposée sur le serveur.
    1. Une fois l’application inscrite, copiez l’ID client et sélectionnez **Generate a new client secret** (Générer un nouveau secret client).

    Exécutez la commande ci-dessous pour tester les paramètres. N’oubliez pas de remplacer `<connection-string>`, `<client-id>` et `<client-secret>` par vos valeurs.

    ```bash
    export WebPubSubConnectionString="<connection-string>"
    export GitHubClientId="<client-id>"
    export GitHubClientSecret="<client-secret>"
    node server
    ```
    
    À présent, ouvrez `http://localhost:8080/auth/github`. Vous êtes redirigé vers GitHub pour vous connecter. Une fois connecté, vous êtes redirigé vers l’application de conversation.

1.  Mettez à jour la salle de conversation pour utiliser l’identité obtenue à partir de GitHub, plutôt que de demander à l’utilisateur un nom d’utilisateur.

    Mettez à jour `public/index.html` pour appeler directement `/negotiate` sans passer un identifiant utilisateur.

    ```javascript
    let messages = document.querySelector('#messages');
    let res = await fetch(`/negotiate`);
    if (res.status === 401) {
      let m = document.createElement('p');
      m.innerHTML = 'Not authorized, click <a href="/auth/github">here</a> to login';
      messages.append(m);
      return;
    }
    let data = await res.json();
    let ws = new WebSocket(data.url);
    ```

    Quand un utilisateur est connecté, la requête utilise automatiquement l’identité de l’utilisateur par le biais d’un cookie. Vous devez simplement vérifier si l’utilisateur existe dans l’objet `req`, et ajouter le nom d’utilisateur au jeton d’accès Web PubSub :

    ```javascript
    app.get('/negotiate', async (req, res) => {
      if (!req.user || !req.user.username) {
        res.status(401).send('missing user id');
        return;
      }
      let options = {
        userId: req.user.username
      };
      let token = await serviceClient.getClientAccessToken(options);
      res.json({
        url: token.url
      });
    });
    ```

    À présent, réexécutez le serveur ; un message de type « non autorisé » s’affiche la première fois que vous ouvrez la salle de conversation. Sélectionnez le lien de connexion pour vous connecter, vous verrez alors que tout fonctionne comme avant.

## <a name="work-with-permissions"></a>Utiliser des autorisations

Dans les tutoriels précédents, vous avez appris à utiliser `WebSocket.send()` pour publier directement des messages sur d’autres clients en utilisant un sous-protocole. Dans une application réelle, vous n’avez peut-être pas envie que le client soit en mesure de publier sur un groupe ou de s’y abonner, sans contrôle d’autorisation. Dans cette section, vous allez apprendre à contrôler les clients en utilisant le système d’autorisation de Web PubSub.

Dans Web PubSub, un client peut effectuer les types d’opérations suivants avec un sous-protocole :

- Envoyer des événements au serveur.
- Publier des messages dans un groupe.
- Se joindre (s’abonner) à un groupe.

L’envoi d’un événement au serveur est l’opération par défaut du client. Aucun protocole n’étant utilisé, il est donc toujours autorisé. Pour publier sur un groupe et s’y abonner, le client doit obtenir une autorisation. Il existe deux façons pour le serveur d’accorder une autorisation aux clients :

- Spécifier des rôles quand un client est connecté (le *rôle* est un concept permettant de représenter les autorisations initiales quand un client est connecté).
- Utiliser une API pour accorder une autorisation à un client après sa connexion.

Une fois qu’il en a obtenu l’autorisation, le client doit rejoindre le groupe en utilisant le message « Rejoindre le groupe ». Sinon, le serveur peut aussi utiliser une API pour ajouter le client à un groupe, même s’il ne dispose pas de l’autorisation de le rejoindre.

Utilisons maintenant ce système d’autorisation pour ajouter une nouvelle fonctionnalité à la salle de conversation. Vous allez ajouter un nouveau type d’utilisateur appelé *administrateur* à la salle de conversation. Vous allez autoriser l’administrateur à envoyer des messages système (des messages commençant par « [SYSTEM] ») directement à partir du client.

Tout d’abord, vous devez diviser les messages système et les messages utilisateur en deux groupes distincts afin de pouvoir contrôler leurs autorisations séparément.

Changez `server.js` pour envoyer les différents messages aux différents groupes :

```javascript
let handler = new WebPubSubEventHandler(hubName, {
  path: '/eventhandler',
  handleConnect: (req, res) => {
    res.success({
      groups: ['system', 'message'],
    });
  },
  onConnected: req => {
    console.log(`${req.context.userId} connected`);
    serviceClient.group('system').sendToAll(`${req.context.userId} joined`, { contentType: 'text/plain' });
  },
  handleUserEvent: (req, res) => {
    if (req.context.eventName === 'message') {
      serviceClient.group('message').sendToAll({
        user: req.context.userId,
        message: req.data
      });
    }
    res.success();
  }
});
```

Le code précédent utilise `WebPubSubServiceClient.group().sendToAll()` au lieu du concentrateur pour envoyer le message à un groupe.

Comme le message est désormais envoyé à des groupes, vous devez ajouter les clients aux groupes, pour qu’ils puissent continuer à recevoir des messages. Utilisez le gestionnaire `handleConnect` pour ajouter des clients à des groupes.

> [!Note]
> `handleConnect` est déclenché quand un client tente de se connecter à Web PubSub. Dans ce gestionnaire, vous pouvez retourner des groupes et des rôles, si bien que le service peut ajouter une connexion à des groupes, ou accorder des rôles, dès que la connexion est établie. Le service peut également utiliser `res.fail()` pour refuser la connexion.
>

Pour déclencher `handleConnect`, accédez aux paramètres du gestionnaire d’événements dans le portail Azure et sélectionnez **connect** (connecter) dans les événements système.

Vous devez également mettre à jour le code HTML du client, car le serveur envoie désormais des messages JSON au lieu de texte brut :

```javascript
let ws = new WebSocket(data.url, 'json.webpubsub.azure.v1');
ws.onopen = () => console.log('connected');

ws.onmessage = event => {
  let m = document.createElement('p');
  let message = JSON.parse(event.data);
  switch (message.type) {
    case 'message':
      if (message.group === 'system') m.innerText = `[SYSTEM] ${message.data}`;
      else if (message.group === 'message') m.innerText = `[${message.data.user}] ${message.data.message}`;
      break;
  }
  messages.appendChild(m);
};

let message = document.querySelector('#message');
message.addEventListener('keypress', e => {
  if (e.charCode !== 13) return;
  ws.send(JSON.stringify({
    type: 'event',
    event: 'message',
    dataType: 'text',
    data: message.value
  }));
  message.value = '';
});
```

Changez ensuite le code client à envoyer au groupe système quand l’utilisateur sélectionne **message système** :

```html
<button id="system">system message</button>
...
<script>
  (async function() {
    ...
    let system = document.querySelector('#system');
    system.addEventListener('click', e => {
      ws.send(JSON.stringify({
        type: 'sendToGroup',
        group: 'system',
        dataType: 'text',
        data: message.value
      }));
      message.value = '';
    });
  })();
</script>
```

Par défaut, le client n’a pas l’autorisation d’envoi à un groupe. Mettez à jour le code du serveur pour accorder l’autorisation à l’utilisateur administrateur (par souci de simplicité, l’ID de l’administrateur est fourni sous forme d’argument de ligne de commande).

```javascript
app.get('/negotiate', async (req, res) => {
  ...
  if (req.user.username === process.argv[2]) options.claims = { role: ['webpubsub.sendToGroup.system'] };
  let token = await serviceClient.getClientAccessToken(options);
});
```

À présent, exécutez `node server <admin-id>`. Vous verrez que vous pouvez envoyer un message système à chaque client quand vous vous connectez en tant que `<admin-id>`.

Toutefois, si vous vous connectez en tant qu’utilisateur différent, quand vous sélectionnez **message système**, rien ne se produit. Vous pouvez vous attendre à ce que le service vous signale une erreur pour vous informer que l’opération n’est pas autorisée. Pour fournir ce commentaire, vous pouvez définir `ackId` au moment où vous publiez le message. Chaque fois que `ackId` est spécifié, Web PubSub retourne un message avec un `ackId` correspondant pour indiquer si l’opération a réussi ou non.

Remplacez le code d’envoi d’un message système par le code suivant :

```javascript
let ackId = 0;
system.addEventListener('click', e => {
  ws.send(JSON.stringify({
    type: 'sendToGroup',
    group: 'system',
    ackId: ++ackId,
    dataType: 'text',
    data: message.value
    }));
  message.value = '';
});
```

Changez également le code de traitement des messages de manière à gérer un message `ack` :

```javascript
ws.onmessage = event => {
  ...
  switch (message.type) {
    case 'ack':
      if (!message.success && message.error.name === 'Forbidden') m.innerText = 'No permission to send system message';
      break;
  }
};
```

À présent, réexécutez le serveur et connectez-vous sous un autre nom d’utilisateur. Un message d’erreur s’affiche lorsque vous essayez d’envoyer un message système.

L’exemple de code complet de ce tutoriel est disponible sur [GitHub][code].

## <a name="next-steps"></a>Étapes suivantes

Ce tutoriel vous donne une première idée de la procédure à suivre pour se connecter au service Web PubSub et publier des messages sur les clients connectés en utilisant un sous-protocole.

Pour en apprendre davantage sur l’utilisation du service Web PubSub, consultez les autres tutoriels disponibles dans la documentation.

> [!div class="nextstepaction"]
> [Explorer d’autres exemples Azure Web PubSub](https://aka.ms/awps/samples)

[code]: https://github.com/Azure/azure-webpubsub/tree/main/samples/javascript/githubchat/
[chat-js]: https://github.com/Azure/azure-webpubsub/tree/main/samples/javascript/chatapp
