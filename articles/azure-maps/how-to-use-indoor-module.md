---
title: Utilisation du module Indoor Maps d’Azure Maps avec les services Microsoft Creator
description: Découvrez comment utiliser le module Cartes d’intérieur d’Azure Maps pour afficher les cartes en incorporant les bibliothèques JavaScript du module.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/13/2021
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
ms.custom: devx-track-js
ms.openlocfilehash: 32df2b3805d5c79504380676a72d43467ff2092d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122531591"
---
# <a name="use-the-azure-maps-indoor-maps-module"></a>Utiliser le module Cartes d’intérieur d’Azure Maps

Le Kit de développement logiciel (SDK) web Azure Maps comprend le module *Cartes d’intérieur Azure*. Le module *Indoor Maps d’Azure Maps* permet d’afficher des cartes d’intérieur créées dans les services Azure Maps Creator.

## <a name="prerequisites"></a>Prérequis

1. [Créer un compte Azure Maps](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Créer une ressource de Créateur](how-to-manage-creator.md)
3. [Obtenir une clé d’abonnement principale](quick-demo-map-app.md#get-the-primary-key-for-your-account), également appelée clé primaire ou clé d’abonnement.
4. Procurez-vous un `tilesetId` et un `statesetId` en suivant le [tutoriel pour créer des cartes d’intérieur](tutorial-creator-indoor-maps.md).
 Vous devez utiliser ces identificateurs pour afficher des cartes d’intérieur avec le module Cartes d’intérieur d’Azure Maps.

## <a name="embed-the-indoor-maps-module"></a>Incorporer le module Cartes d’intérieur

Vous pouvez installer et incorporer le module *Cartes d’intérieur Azure* de l’une des deux façons suivantes.

Pour utiliser la version Azure Content Delivery Network hébergée dans le monde entier du module *Cartes d’intérieur Azure*, reportez-vous aux références JavaScript et de feuille de style suivantes dans l’élément `<head>` du fichier HTML :

```html
<link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/indoor/0.1/atlas-indoor.min.css" type="text/css"/>
<script src="https://atlas.microsoft.com/sdk/javascript/indoor/0.1/atlas-indoor.min.js"></script>
```

 Vous pouvez également télécharger le module *Cartes d’intérieur Azure*. Le module *Cartes d’intérieur Azure* contient une bibliothèque cliente permettant d’accéder aux services Azure Maps. Suivez les étapes ci-dessous pour installer et charger le module *Cartes d’intérieur* dans votre application web.  
  
  1. Installez le [package azure-maps-indoor](https://www.npmjs.com/package/azure-maps-indoor).
  
      ```powershell
      >npm install azure-maps-indoor
      ```

  2. Référencez le JavaScript et la feuille de style du module *Cartes d’intérieur Azure* dans l’élément `<head>` du fichier HTML :

      ```html
      <link rel="stylesheet" href="node_modules/azure-maps-indoor/dist/atlas-indoor.min.css" type="text/css" />
      <script src="node_modules/azure-maps-indoor/dist/atlas-indoor.min.js"></script>
      ```

## <a name="instantiate-the-map-object"></a>Instancier l’objet Map

Tout d’abord, créez un *objet Map*. L’*objet Map* sera utilisé à l’étape suivante pour instancier l’objet *Gestionnaire d’intérieur*.  Le code ci-dessous montre comment instancier l’*objet Map* :

```javascript
const subscriptionKey = "<Your Azure Maps Primary Subscription Key>";

const map = new atlas.Map("map-id", {
  //use your facility's location
  center: [-122.13203, 47.63645],
  //or, you can use bounds: [# west, # south, # east, # north] and replace # with your map's bounds
  style: "blank",
  view: 'Auto',
  authOptions: {
      authType: 'subscriptionKey',
      subscriptionKey: subscriptionKey
  },
  zoom: 19,
});
```

## <a name="instantiate-the-indoor-manager"></a>Instancier le Gestionnaire d’intérieur

Pour charger les tilesets d’intérieur et le style de la carte des vignettes, vous devez instancier le *Gestionnaire d’intérieur*. Instanciez le *Gestionnaire d’intérieur* en fournissant l’*objet Map* et l’ID `tilesetId` correspondant. Si vous souhaitez prendre en charge l’[application de style de carte dynamique](indoor-map-dynamic-styling.md), vous devez passer l’ID `statesetId`. Le nom de la variable `statesetId` respecte la casse. Votre code doit ressembler au code JavaScript ci-dessous.

```javascript
const tilesetId = "<tilesetId>";
const statesetId = "<statesetId>";

const indoorManager = new atlas.indoor.IndoorManager(map, {
    tilesetId: tilesetId,
    statesetId: statesetId // Optional
});
```

Pour activer le sondage de données d’état que vous fournissez, vous devez spécifier l’ID `statesetId` et appeler `indoorManager.setDynamicStyling(true)`. Le sondage de données d’état vous permet de mettre à jour de façon dynamique l’état de propriétés dynamiques ou *états*. Par exemple, une caractéristique telle qu’une salle peut avoir une propriété dynamique (*état*) nommée `occupancy`. Votre application peut interroger tout changement d’*état* pour refléter la modification à l’intérieur de la carte visuelle. Le code ci-dessous montre comment activer le sondage d’état :

```javascript
const tilesetId = "<tilesetId>";
const statesetId = "<statesetId>";

const indoorManager = new atlas.indoor.IndoorManager(map, {
    tilesetId: tilesetId,
    statesetId: statesetId // Optional
});

if (statesetId.length > 0) {
    indoorManager.setDynamicStyling(true);
}
```

## <a name="geographic-settings-optional"></a>Paramètres géographiques (facultatif)

Ce guide suppose que vous avez créé votre service Creator aux États-Unis. Si tel est le cas, vous pouvez ignorer cette section. Toutefois, si votre service Creator a été créé en Europe, ajoutez le code suivant :

```javascript
  indoorManager.setOptions({ geography: 'eu' });.
```

## <a name="indoor-level-picker-control"></a>Contrôle Sélecteur de niveau intérieur

 Le contrôle *Sélecteur de niveau intérieur* vous permet de modifier le niveau de la carte affichée. Vous pouvez également initialiser le contrôle *Sélecteur de niveau intérieur* via le *Gestionnaire d’intérieur*. Voici le code permettant d’initialiser le sélecteur de contrôle de niveau :

```javascript
const levelControl = new atlas.control.LevelControl({ position: "top-right" });
indoorManager.setOptions({ levelControl });
```

## <a name="indoor-events"></a>Événements d’intérieur

 Le module *Cartes d’intérieur d’Azure* prend en charge les événements d’*objet Map*. Les écouteurs d’événements d’*objet Map* sont appelés quand un niveau ou un bâtiment a changé. Si vous souhaitez exécuter du code lors de la modification d’un niveau ou d’un bâtiment, placez votre code à l’intérieur de l’écouteur d’événements. Le code ci-dessous montre comment ajouter des écouteurs d’événements à l’*objet Map*.

```javascript
map.events.add("levelchanged", indoorManager, (eventData) => {

  //code that you want to run after a level has been changed
  console.log("The level has changed: ", eventData);
});

map.events.add("facilitychanged", indoorManager, (eventData) => {

  //code that you want to run after a facility has been changed
  console.log("The facility has changed: ", eventData);
});
```

La variable `eventData` contient des informations sur le niveau ou le bâtiment qui a appelé l’événement `levelchanged` ou `facilitychanged`. Quand un niveau change, l’objet `eventData` contient l’ID `facilityId`, la nouvelle valeur `levelNumber` et d’autres métadonnées. Quand un bâtiment change, l’objet `eventData` contient l’ID `facilityId`, la nouvelle valeur `levelNumber` et d’autres métadonnées.

## <a name="example-use-the-indoor-maps-module"></a>Exemple : Utiliser le module Cartes d’intérieur

Cet exemple montre comment utiliser le module *Cartes d’intérieur d’Azure Maps* dans votre application web. Bien que l’étendue de l’exemple soit limitée, il couvre les informations de base sur ce dont vous avez besoin pour commencer à utiliser le module *Cartes d’intérieur d’Azure Maps*. Le code HTML complet figure dans les étapes ci-dessous.

1. Utilisez l’[option](#embed-the-indoor-maps-module) Azure Content Delivery Network pour installer le module *Cartes d’intérieur d’Azure Maps*.

2. Créez un fichier HTML

3. Dans l’en-tête HTML, référencez le JavaScript et la feuille de style du module *Cartes d’intérieur d’Azure Maps*.

4. Initialisez un *objet Map*. L’*objet Map* prend en charge les options suivantes :
    - `Subscription key` est votre clé d’abonnement principale Azure Maps.
    - `center` définit une latitude et une longitude pour l’emplacement du centre de votre carte d’intérieur. Fournissez une valeur pour `center` si vous ne souhaitez pas fournir de valeur pour `bounds`. Le format doit être `center`: [-122.13315, 47.63637].
    - `bounds` est la plus petite forme rectangulaire qui englobe les données cartographiques du tileset. Définissez une valeur pour `bounds` si vous ne souhaitez pas définir de valeur pour `center`. Vous pouvez trouver vos limites de carte en appelant l’[API de liste de tileset](/rest/api/maps/v2/tileset/list). L’API de liste de tileset retourne la valeur `bbox`, que vous pouvez analyser et assigner à `bounds`. Le format doit apparaître comme `bounds` : [# west, # south, # east, # north].
    - `style` vous permet de définir la couleur de l’arrière-plan. Pour afficher un arrière-plan blanc, définissez `style` sur « vide ».
    - `zoom` vous permet de spécifier les niveaux de zoom minimum et maximum pour votre carte.

5. Ensuite, créez le module *Gestionnaire d’intérieur*. Assignez l’ID `tilesetId` de *Cartes d’intérieur d’Azure Maps* et ajoutez éventuellement l’ID `statesetId`.

6. Instanciez le contrôle *Sélecteur de niveau intérieur*.

7. Ajoutez les écouteurs d’événements d’*objet Map*.  

Votre fichier doit maintenant ressembler au code HTML ci-dessous.

  ```html
  <!DOCTYPE html>
  <html lang="en">
    <head>
      <meta charset="utf-8" />
      <meta name="viewport" content="width=device-width, user-scalable=no" />
      <title>Indoor Maps App</title>
      
      <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
      <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/indoor/0.1/atlas-indoor.min.css" type="text/css"/>

      <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>
      <script src="https://atlas.microsoft.com/sdk/javascript/indoor/0.1/atlas-indoor.min.js"></script>
        
      <style>
        html,
        body {
          width: 100%;
          height: 100%;
          padding: 0;
          margin: 0;
        }

        #map-id {
          width: 100%;
          height: 100%;
        }
      </style>
    </head>

    <body>
      <div id="map-id"></div>
      <script>
        const subscriptionKey = "<Your Azure Maps Primary Subscription Key>";
        const tilesetId = "<your tilesetId>";
        const statesetId = "<your statesetId>";

        const map = new atlas.Map("map-id", {
          //use your facility's location
          center: [-122.13315, 47.63637],
          //or, you can use bounds: [# west, # south, # east, # north] and replace # with your Map bounds
          style: "blank",
          view: 'Auto',
          authOptions: { 
              authType: 'subscriptionKey',
              subscriptionKey: subscriptionKey
          },
          zoom: 19,
        });

        const levelControl = new atlas.control.LevelControl({
          position: "top-right",
        });

        const indoorManager = new atlas.indoor.IndoorManager(map, {
          levelControl: levelControl, //level picker
          tilesetId: tilesetId,
          statesetId: statesetId // Optional
        });

        if (statesetId.length > 0) {
          indoorManager.setDynamicStyling(true);
        }

        map.events.add("levelchanged", indoorManager, (eventData) => {
          //put code that runs after a level has been changed
          console.log("The level has changed:", eventData);
        });

        map.events.add("facilitychanged", indoorManager, (eventData) => {
          //put code that runs after a facility has been changed
          console.log("The facility has changed:", eventData);
        });
      </script>
    </body>
  </html>
  ```

Pour afficher votre carte d’intérieur, chargez-la dans un navigateur web. Elle doit ressembler à l’image ci-dessous. Si vous cliquez sur la caractéristique stairwell, le *sélecteur de niveau* s’affiche dans l’angle supérieur droit.

  ![image de carte d’intérieur](media/how-to-use-indoor-module/indoor-map-graphic.png)

[Voir la démonstration en direct](https://azuremapscodesamples.azurewebsites.net/?sample=Creator%20indoor%20maps)

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les API relatives au module *Cartes d’intérieur d’Azure Maps* :

> [!div class="nextstepaction"]
> [exigences du package de dessin](drawing-requirements.md)

>[!div class="nextstepaction"]
> [Créateur pour cartes d’intérieur](creator-indoor-maps.md)

En savoir plus sur l’ajout de données à votre carte :

> [!div class="nextstepaction"]
> [Application de style dynamique de Cartes d’intérieur](indoor-map-dynamic-styling.md)

> [!div class="nextstepaction"]
> [Exemples de code](/samples/browse/?products=azure-maps)
