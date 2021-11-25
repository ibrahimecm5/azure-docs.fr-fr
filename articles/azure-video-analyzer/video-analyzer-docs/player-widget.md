---
title: Utiliser le widget de lecteur Video Analyzer
description: Cet article explique comment ajouter un widget de lecteur Video Analyzer à votre application.
ms.service: azure-video-analyzer
ms.topic: how-to
ms.date: 11/12/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 8dbf85609196a930d7f51a713753f725b3016396
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132487475"
---
# <a name="use-the-azure-video-analyzer-player-widget"></a>Utiliser le widget du lecteur d’Azure Video Analyzer

Ce tutoriel explique comment utiliser un widget du lecteur dans votre application. Ce code est un widget facile à incorporer, qui permet à vos utilisateurs de lire des vidéos et de naviguer dans les différentes parties d’un fichier vidéo segmenté. Pour le mettre en œuvre, vous allez générer une page HTML statique avec le widget incorporé et tous les éléments pour le faire fonctionner.

Ce didacticiel présente les procédures suivantes :

> [!div class="checklist"]
> * Créer une page avec le lecteur
> * Répertorier les vidéos
> * Passer un point de terminaison de streaming et un jeton au lecteur
> * Ajouter un lecteur Dessinateur de zone
> * Voir des vidéos tronquées selon les heures de début et de fin spécifiées

## <a name="prerequisites"></a>Prérequis

Les éléments suivants sont requis pour suivre ce tutoriel :

* Un compte Azure disposant d’un abonnement actif. Si vous n’en avez pas déjà un, [créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Visual Studio Code](https://code.visualstudio.com/) ou un autre éditeur pour le fichier HTML.
* Exécuter les topologies du tutoriel [Enregistrement et lecture de vidéo continue](edge/use-continuous-video-recording.md) ou du guide de démarrage rapide [Détecter les mouvements et enregistrer des vidéos sur des appareils de périphérie](./detect-motion-record-video-clips-cloud.md)
* Créer un [jeton](./access-policies.md#creating-a-token)
* Créer une [stratégie d’accès](./access-policies.md#creating-an-access-policy)


## <a name="create-a-web-page-with-a-video-player"></a>Créer une page web avec un lecteur vidéo

Utilisez l’exemple de code ci-dessous pour créer une page web.

```html
<html>
<head>
<title>Video Analyzer Player Widget Demo</title>
</head>
<script async type="module" src="https://unpkg.com/@azure/video-analyzer-widgets"></script>
<body>
Client API endpoint URL: <input type="text" id="clientApiEndpointUrl" /><br><br>
JWT Auth Token for Client API: <input type="text" id="token" /><br><br>
<button type="submit" onclick="getVideos()">Get Videos</button><br><br>
<textarea rows="20" cols="100" id="videoList"></textarea><br><br>
Video name: <input type="text" id="videoName" /><br><br>
<button type="submit" onclick="playVideo()">Play Video</button><br><br>
</body>
</body>
</html>
```
## <a name="list-video-resources"></a>Lister les ressources vidéo

Ensuite, générez une liste des ressources vidéo. Vous effectuez un appel REST au point de terminaison de compte que vous avez utilisé précédemment et vous vous authentifiez avec le jeton que vous avez généré.

Il existe de nombreuses façons d’envoyer une requête GET à une API REST. En l’occurrence, vous allez utiliser une fonction JavaScript. Le code ci-dessous utilise une requête [XMLHttpRequest](https://www.w3schools.com/xml/ajax_xmlhttprequest_create.asp) couplée aux valeurs que vous stockez dans les champs `clientApiEndpointUrl` et `token` de la page pour envoyer une requête `GET` synchrone. Il prend ensuite la liste des vidéos obtenue et stocke celles-ci dans la zone de texte `videoList` que vous avez configurée sur la page.

L’extrait de code suivant permet de demander la liste de vidéos.

```javascript
function getVideos()
{
    var xhttp = new XMLHttpRequest();
    var getUrl = document.getElementById("clientApiEndpointUrl").value + "/videos?api-version=2021-05-01-preview";
    xhttp.open("GET", getUrl, false);
    xhttp.setRequestHeader("Authorization", "Bearer " + document.getElementById("token").value);
    xhttp.send();
    document.getElementById("videoList").value = xhttp.responseText.toString();
}
```
   > [!NOTE]
   >Le `clientApiEndPoint` et le `token` sont ceux traités dans la section [Création d’un jeton](./access-policies.md#creating-a-token).

## <a name="add-the-video-analyzer-player-component"></a>Ajouter le composant de lecteur de Video Analyzer

Vous disposez à présent d’une URL de point de terminaison d’API client, d’un jeton et d’un nom de vidéo. Vous pouvez ajouter le lecteur à la page.

1. Incluez le module lecteur proprement dit en ajoutant le package directement à votre page. Vous pouvez inclure la direction du package NPM dans votre application ou faire en sorte de l’incorporer de façon dynamique au moment de l’exécution comme ici :
   ```html
   <script async type="module" src="https://unpkg.com/@azure/video-analyzer-widgets"></script>
   ```
1. Ajouter un élément `AVA-Player` au document :
   ```html
   <ava-player width="720px" id="avaPlayer"></ava-player>
   ```
1. Obtenez un lien vers le widget du lecteur de Video Analyzer qui se trouve dans la page :
   ```javascript
   const avaPlayer = document.getElementById("videoPlayer");
   ```
1. Pour configurer le lecteur avec les valeurs dont vous disposez, vous devez configurer celles-ci en tant qu’objet comme ici :
   ```javascript
   avaPlayer.configure( {
      token: document.getElementById("token").value,
      clientApiEndpointUrl: document.getElementById("clientApiEndpointUrl").value,
      videoName: document.getElementById("videoName").value
   } );
   ```
1. Chargez la vidéo dans le lecteur pour commencer :
   ```javascript
   avaPlayer.load();
   ```
   
## <a name="add-the-zone-drawer-component"></a>Ajouter le composant de dessinateur de zone

Le composant de dessinateur de zone vous permet de dessiner des lignes et des polygones sur le lecteur Video Analyzer. 

1. Ajoutez un élément AVA-Zone-Drawer au document :
   ```html
   <ava-zone-drawer width="720px" id="zoneDrawer">
        <ava-player id="videoPlayer2"></ava-player>
   </ava-zone-drawer>
   ```
1. Récupérez un lien vers le widget de lecteur Video Analyzer avec lequel s’effectuera la lecture dans le dessinateur de zone :
   ```javascript
   const avaPlayer2 = document.getElementById("videoPlayer2");
   ```
1. Configurez le lecteur avec lequel s’effectuera la lecture dans le dessinateur de zone :
   ```javascript
   avaPlayer2.configure( {
      token: document.getElementById("token").value,
      clientApiEndpointUrl: document.getElementById("clientApiEndpointUrl").value,
      videoName: document.getElementById("videoName").value
   } );
   ```
1. Chargez la vidéo dans le lecteur dans le dessinateur de zone :
   ```javascript
   avaPlayer2.load();
   ```
1. Récupérez un lien vers le dessinateur de zone qui se trouve dans la page :
   ```javascript
   const zoneDrawer = document.getElementById("zoneDrawer");
   ```
1. Chargez le dessinateur de zone dans le lecteur :
   ```javascript
   zoneDrawer.load();
   ```
1. Configurez le dessinateur de zone :
   ```javascript
   zoneDrawer.configure();
   ```
1. Pour créer et enregistrer des zones, vous devez ajouter des écouteurs d’événements ici :
   ```javascript
   zoneDrawer.addEventListener('ZONE_DRAWER_ADDED_ZONE', (event) => {
            console.log(event);
            document.getElementById("zoneList").value = JSON.stringify(event.detail);
        });

        zoneDrawer.addEventListener('ZONE_DRAWER_SAVE', (event) => {
            console.log(event);
            document.getElementById("zoneList").value = JSON.stringify(event.detail);
        });
   ```

## <a name="put-it-all-together"></a>Assemblage

En associant les éléments web précédents, vous obtenez la page HTML statique suivante. Cette page vous permet d’utiliser un point de terminaison de compte et un jeton pour voir une vidéo.

```html
<html>
<head>
<title>Video Analyzer Player Widget Demo</title>
</head>
<script async type="module" src="https://unpkg.com/@azure/video-analyzer-widgets"></script>
<body>
<script>
    function getVideos()
    {
        var xhttp = new XMLHttpRequest();
        var getUrl = document.getElementById("clientApiEndpointUrl").value + "/videos?api-version=2021-05-01-preview";
        xhttp.open("GET", getUrl, false);
        xhttp.setRequestHeader("Authorization", "Bearer " + document.getElementById("token").value);
        xhttp.send();
        document.getElementById("videoList").value = xhttp.responseText.toString();
    }
    function playVideo() {
        const avaPlayer = document.getElementById("videoPlayer");
        avaPlayer.configure( {
            token: document.getElementById("token").value,
            clientApiEndpointUrl: document.getElementById("clientApiEndpointUrl").value,
            videoName: document.getElementById("videoName").value
        } );
        avaPlayer.load();
        
        const avaPlayer2 = document.getElementById("videoPlayer2");
        avaPlayer2.configure( {
            token: document.getElementById("token").value,
            clientApiEndpointUrl: document.getElementById("clientApiEndpointUrl").value,
            videoName: document.getElementById("videoName").value
        } );
        avaPlayer2.load();
    
        const zoneDrawer = document.getElementById("zoneDrawer");
        zoneDrawer.load();
        zoneDrawer.configure();

        zoneDrawer.addEventListener('ZONE_DRAWER_ADDED_ZONE', (event) => {
            console.log(event);
            document.getElementById("zoneList").value = JSON.stringify(event.detail);
        });

        zoneDrawer.addEventListener('ZONE_DRAWER_SAVE', (event) => {
            console.log(event);
            document.getElementById("zoneList").value = JSON.stringify(event.detail);
        });
    }
</script>
Client API endpoint URL: <input type="text" id="clientApiEndpointUrl" /><br><br>
JWT Auth Token for Client API: <input type="text" id="token" /><br><br>
<button type="submit" onclick="getVideos()">Get Videos</button><br><br>
<textarea rows="20" cols="100" id="videoList"></textarea><br><br>
<button type="submit" onclick="playVideo()">Play Video</button><br><br>
Video name: <input type="text" id="videoName" /><br><br>
<div id="container" style="width:720px" class="widget-container">
    <ava-player width="720px" id="videoPlayer"></ava-player>
</div>
<textarea rows="5" cols="100" id="zoneList"></textarea><br><br>
<ava-zone-drawer width="720px" id="zoneDrawer">
    <ava-player id="videoPlayer2"></ava-player>
</ava-zone-drawer>
</body>
</html>
```

## <a name="host-the-page"></a>Héberger la page

Vous pouvez tester cette page localement, mais vous souhaiterez peut-être tester une version hébergée. Si vous n’avez pas de moyen rapide d’héberger une page, voici des instructions sur la façon de le faire en utilisant des [sites web statiques](../../storage/blobs/storage-blob-static-website.md) avec Stockage Azure. La procédure qui suit est une version condensée de ces [instructions plus complètes](../../storage/blobs/storage-blob-static-website-how-to.md). Ces étapes sont adaptées aux fichiers que vous utilisez dans ce tutoriel.

1. Créez un compte de stockage.
1. Sous **Gestion des données**, sélectionnez **Site web statique**.
1. Activez le site web statique sur le compte de stockage.
1. Pour **Nom du document d’index**, entrez **index.html**.
1. Pour **Chemin du document d’erreur**, entrez **404.html**.
1. Sélectionnez **Enregistrer**.
1. Notez le **point de terminaison principal** qui s’affiche. Il s’agira de votre site web.
1. Au-dessus de **Point de terminaison principal**, sélectionnez **$web**.
1. À l’aide du bouton **Charger** situé en haut, chargez votre page HTML statique en tant que **index.html**.

### <a name="play-a-video"></a>Lire une vidéo

La page est à présent hébergée. Accédez-y et suivez les étapes pour lire une vidéo.

1. Renseignez les valeurs **Client API endpoint URL** (URL de point de terminaison d’API client) et **Token**.
1. Sélectionnez **Get videos**.
1. Dans la liste de vidéos, sélectionnez un nom de vidéo et entrez-le dans le champ **Video name**.
1. Sélectionnez **Play video**.

### <a name="live-video-playback"></a>Lecture de vidéo en direct

Si votre livePipeline est à l’état `activated` et que la vidéo est en cours d’enregistrement, le lecteur charge automatiquement la vue **EN DIRECT**. Cette lecture vidéo est en quasi-temps réel et présentera une faible latence d’environ 2 secondes.

Dans la vue **EN DIRECT** :
1. Vous voyez la vidéo en quasi-temps réel.
1. Vous ne voyez pas la chronologie.
1. Vous pouvez cliquer sur l’icône de **cadre** pour afficher les cadres englobants, s’ils existent.

> [!Tip]
> Pour basculer vers la vue dans laquelle vous pouvez voir tous les clips précédemment enregistrés, cliquez sur le bouton **EN DIRECT**.
 
### <a name="capture-lines-and-zones"></a>Capturer des lignes et des zones

1. Accédez au lecteur **Dessinateur de zone**
1. Cliquez sur la première icône située dans le coin supérieur gauche pour dessiner des zones.
1. Pour dessiner des zones et des lignes, vous devez simplement cliquer sur les points sur lesquels vous souhaitez placer les points de terminaison. Il n’existe pas de fonctionnalité de glissement pour dessiner les zones et les lignes.
1. Vous voyez les zones et les lignes créées dans la section de droite du lecteur.
1. Pour obtenir les coordonnées des lignes et des zones, cliquez sur le bouton **Enregistrer**.
1. En procédant ainsi, vous afficherez la réponse JSON avec les coordonnées de point, que vous pouvez utiliser avec les topologies appropriées.

### <a name="video-clips"></a>Clips vidéo
Vous permet de créer des clips vidéo en sélectionnant une heure de début et une heure de fin.

Le widget de lecteur vidéo Video Analyzer prend en charge la lecture de clips vidéo avec spécification d’une date et d’une heure de début et de fin comme indiqué ci-dessous :

> [!Note] 
> Le widget de lecteur vidéo Video Analyzer utilise la norme de temps universel coordonné (UTC). L’heure de début et l’heure de fin sélectionnées doivent donc être converties dans ce format.

Utilisez le code ci-dessous dans votre fichier HTML pour ouvrir un lecteur vidéo qui chargera une vidéo selon l’heure de début (startTime) et l’heure de fin (endTime) spécifiées.

```javascript
    const avaPlayer = document.getElementById("videoPlayer");
    const startUTCDate = new Date(Date.UTC(selectedClip.start.getFullYear(), selectedClip.start.getMonth(), selectedClip.start.getDate(), selectedClip.start.getHours(), selectedClip.start.getMinutes(), selectedClip.start.getSeconds()));
    const endUTCDate = new Date(Date.UTC(selectedClip.end.getFullYear(), selectedClip.end.getMonth(), selectedClip.end.getDate(), selectedClip.end.getHours(), selectedClip.end.getMinutes(), selectedClip.end.getSeconds()));
    avaPlayer.load({ startTime: startUTCDate, endTime: endUTCDate });
``` 

## <a name="additional-details"></a>Informations supplémentaires

Les sections suivantes contiennent des informations supplémentaires importantes, que vous devez connaître.
### <a name="refresh-the-access-token"></a>Actualiser le jeton d’accès

Le lecteur utilise le jeton d’accès que vous avez généré précédemment pour obtenir un jeton d’autorisation de lecture. Les jetons expirent périodiquement et doivent être actualisés. Il existe deux manières d’actualiser le jeton d’accès pour le lecteur après en avoir généré un :

* En appelant activement la méthode de widget `setAccessToken`
    ```typescript
    avaPlayer.setAccessToken('<NEW-ACCESS-TOKEN>');
    ```
* En agissant sur l’événement `TOKEN_EXPIRED` en écoutant cet événement
    ```typescript
    avaPlayer.addEventListener(PlayerEvents.TOKEN_EXPIRED, () => {
        avaPlayer.setAccessToken('<YOUR-NEW-TOKEN>');
    });
    ```

L’événement `TOKEN_EXPIRED` se produit 5 secondes avant l’expiration du jeton. Si vous définissez un détecteur d’événements, vous devez le faire avant d’appeler la fonction `load` sur le widget du lecteur.

### <a name="configuration-details"></a>Détails de configuration

La configuration du lecteur ci-dessus est simple, mais vous pouvez utiliser un éventail d’options plus large pour les valeurs de configuration. Les champs pris en charge sont les suivants :

| Name   | Type             | Description                         |
| ------ | ---------------- | ----------------------------------- |
| `token`  | string | Votre jeton JWT pour le widget |
| `videoName` | string | Nom de la ressource vidéo  |
| `clientApiEndpointUrl` | string | URL de point de terminaison pour l’API client |

### <a name="alternate-ways-to-load-the-code-into-your-application"></a>Autres façons de charger le code dans votre application

Le package utilisé pour récupérer le code dans votre application est un [package NPM](https://www.npmjs.com/package/@azure/video-analyzer-widgets). Dans l’exemple précédent, la version la plus récente a été chargée au moment de l’exécution directement à partir du référentiel. Toutefois, vous pouvez également télécharger et installer le package localement à l’aide de la commande suivante :

```bash
npm install @azure/video-analyzer/widgets
```

Vous pouvez aussi l’importer dans le code de votre application en utilisant le code suivant pour TypeScript :

```typescript
import { Player } from '@azure/video-analyzer-widgets';
import { ZoneDrawer } from '@azure/video-analyzer-widgets';
```

Si vous souhaitez créer un widget de lecteur dynamiquement, vous pouvez utiliser le code suivant pour JavaScript :
```javascript
<script async type="module" src="https://unpkg.com/@azure/video-analyzer-widgets@latest/dist/global.min.js"></script>
```


Si vous utilisez cette méthode pour importer, vous devrez créer les objets dessinateur de zone et lecteur par programmation une fois l’importation terminée.  Dans l’exemple précédent, vous avez ajouté le module à la page à l’aide de la balise HTML `ava-player`. Pour créer un objet dessinateur de zone et un objet lecteur par le biais du code, vous pouvez procéder comme suit dans JavaScript :


```javascript
const zoneDrawer = new window.ava.widgets.zoneDrawer();
document.firstElementChild.appendChild(zoneDrawer);
const playerWidget = new window.ava.widgets.player();
zoneDrawer.appendChild(playerWidget);
```

Ou dans TypeScript :

```typescript
const avaPlayer = new Player();
const zoneDrawer = new ZoneDrawer();
```

Ensuite, vous devez l’ajouter au code HTML :

```javascript
document.firstElementChild.appendChild(zoneDrawer);
zoneDrawer.appendChild(playerWidget);
```

## <a name="next-steps"></a>Étapes suivantes

* Essayez nos [exemples de lecture avec des widgets](https://github.com/Azure-Samples/video-analyzer-iot-edge-csharp/tree/main/src/video-player).
* Pour découvrir comment les différentes fonctionnalités de widget peuvent être implémentées, consultez notre [dépôt de widgets](https://github.com/Azure/video-analyzer-widgets).
