---
title: Afficher des données personnalisées sur une carte raster dans Microsoft Azure Maps
description: Découvrez comment ajouter des clics-infos, des étiquettes et des formes géométriques à une carte raster. Pour ce faire, découvrez comment utiliser le service d’images statiques dans Azure Maps.
author: stevemunk
ms.author: v-munksteve
ms.date: 10/28/2021
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 9197090d405210db485d1ac8d471d899f58b8e43
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131443639"
---
# <a name="render-custom-data-on-a-raster-map"></a>Afficher des données personnalisées sur une carte raster

Cet article décrit comment utiliser le [service d’image statique](/rest/api/maps/render/getmapimage) avec la fonctionnalité de composition d’image. La fonctionnalité de composition d’image prend en charge la récupération d’une mosaïque raster statique qui contient des données personnalisées.

Voici des exemples de données personnalisées :

- Repères personnalisés
- Étiquettes
- Superpositions de géométrie

> [!Tip]
> Il est souvent plus économique d’utiliser le kit SDK d’Azure Maps Web pour afficher une carte simple sur une page web plutôt que d’utiliser le service d’images statiques. Le kit SDK web utilise des vignettes de carte et, sauf si l’utilisateur effectue un panoramique et un zoom sur la carte, il ne génère souvent qu’une fraction de transaction par charge de carte. Le kit de développement logiciel (SDK) web Azure Maps dispose d’options pour désactiver le panoramique et le zoom. De plus, le kit de développement logiciel (SDK) web Azure Maps offre beaucoup plus d’options de visualisation de données qu’un service web de cartes statiques.  

## <a name="prerequisites"></a>Prérequis

1. [Créer un compte Azure Maps](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Obtenir une clé d’abonnement principale](quick-demo-map-app.md#get-the-primary-key-for-your-account), également appelée clé primaire ou clé d’abonnement.

Cet article utilise l’application [Postman](https://www.postman.com/), mais vous pouvez utiliser un autre environnement de développement d’API.

Nous allons utiliser les [API du service de données](/rest/api/maps/data) d’Azure Maps pour stocker et afficher les superpositions.

## <a name="render-pushpins-with-labels-and-a-custom-image"></a>Afficher des repères avec des étiquettes et une image personnalisée

> [!Note]
> Un compte Azure Maps du niveau tarifaire Gen1 ou Gen2 est nécessaire pour suivre la procédure de cette section.
Le compte Azure Maps au niveau Gen 1 Standard S0 prend en charge une seule instance du paramètre `pins`. Cela vous permet d’afficher jusqu’à cinq repères, spécifiés dans la requête URL, avec une image personnalisée.

### <a name="get-static-image-with-custom-pins-and-labels"></a>Obtenir une image statique avec des épingles et des étiquettes personnalisées

Pour obtenir une image statique avec des épingles et des étiquettes personnalisées :

1. Dans l’application Postman, sélectionnez **New** (Nouveau).

2. Dans la fenêtre **Create New** (Créer), sélectionnez **HTTP Request** (Requête HTTP).

3. Entrez un **Request name** (Nom de requête) pour la requête, par exemple *GET Static Image*.


4. Sélectionnez la méthode HTTP **GET**.


5. Entrez l’URL suivante (remplacez {`Your-Azure-Maps-Primary-Subscription-key}` par votre clé d’abonnement principale) :

    ```HTTP
    https://atlas.microsoft.com/map/static/png?subscription-key={Your-Azure-Maps-Primary-Subscription-key}&api-version=1.0&layer=basic&style=main&zoom=12&center=-73.98,%2040.77&pins=custom%7Cla15+50%7Cls12%7Clc003b61%7C%7C%27CentralPark%27-73.9657974+40.781971%7C%7Chttps%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FAzureMapsCodeSamples%2Fmaster%2FAzureMapsCodeSamples%2FCommon%2Fimages%2Ficons%2Fylw-pushpin.png
    ```

6. Sélectionnez **Envoyer**.

7. Le service retourne l’image suivante :

    :::image type="content" source="./media/how-to-render-custom-data/render-pins.png" alt-text="Un repère personnalisé avec une étiquette.":::

## <a name="upload-pins-and-path-data"></a>Charger des données d’épingles et de chemin

> [!Note]
> Un compte Azure Maps du niveau tarifaire Gen1 (S1) ou Gen2 est nécessaire pour suivre la procédure de cette section.

Dans cette section, nous allons charger des données de chemin et d’épingle dans le stockage de données Azure Map.

Pour charger des données d’épingles et de chemin :

1. Dans l’application Postman, sélectionnez **New** (Nouveau).

2. Dans la fenêtre **Create New** (Créer), sélectionnez **HTTP Request** (Requête HTTP).

3. Entrez un **Request name** (Nom de requête) pour la requête, par exemple *POST Path and Pin Data*.

4. Sélectionnez la méthode HTTP **POST**.

5. Entrez l’URL suivante (remplacez {`Your-Azure-Maps-Primary-Subscription-key}` par votre clé d’abonnement principale) :

    ```HTTP
    https://us.atlas.microsoft.com/mapData?subscription-key={Your-Azure-Maps-Primary-Subscription-key}&api-version=2.0&dataFormat=geojson
    ```

6. Sélectionnez l’onglet **Corps** .

7. Dans les listes déroulantes, sélectionnez **raw** (brut) et **JSON**.

8. Copiez les données  JSON suivantes en tant que données à charger, puis collez-les dans la fenêtre **Body** (Corps) :
  
    ```JSON
    {
      "type": "FeatureCollection",
      "features": [
        {
          "type": "Feature",
          "properties": {},
          "geometry": {
            "type": "Polygon",
            "coordinates": [
              [
                [
                  -73.98235,
                  40.76799
                ],
                [
                  -73.95785,
                  40.80044
                ],
                [
                  -73.94928,
                  40.7968
                ],
                [
                  -73.97317,
                  40.76437
                ],
                [
                  -73.98235,
                  40.76799
                ]
              ]
            ]
          }
        },
        {
          "type": "Feature",
          "properties": {},
          "geometry": {
            "type": "LineString",
            "coordinates": [
              [
                -73.97624731063843,
                40.76560773817073
              ],
              [
                -73.97914409637451,
                40.766826609362575
              ],
              [
                -73.98513078689575,
                40.7585866048861
              ]
            ]
          }
        }
      ]
    }
    ```

9. Sélectionnez **Envoyer**.

10. Dans la fenêtre de réponse, sélectionnez l’onglet **Headers** (En-têtes).

11. Copiez la valeur de la clé **Operation-Location**, qui correspond à `status URL`. Nous allons utiliser le `status URL` pour vérifier l’état de la requête de chargement dans la section suivante. `status URL` est au format suivant :

   ```HTTP
   https://us.atlas.microsoft.com/mapData/operations/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx?api-version=2.0
   ```

>[!TIP]
>Pour obtenir vos propres informations de chemin et d’emplacement d’épingle, utilisez l'[API de chargement de données](/rest/api/maps/data-v2/upload-preview).

### <a name="check-pins-and-path-data-upload-status"></a>Vérifier l’état du chargement des données d’épingles et de chemin

Pour vérifier l’état du chargement des données et récupérer son ID unique (`udid`) :

1. Dans l’application Postman, sélectionnez **New** (Nouveau).

2. Dans la fenêtre **Create New** (Créer), sélectionnez **HTTP Request** (Requête HTTP).

3. Entrez un **Request name** (Nom de demande) pour la demande, par exemple *GET Data Upload Status*.

4. Sélectionnez la méthode HTTP **GET**.

5. Entrez le `status URL` que vous avez copié dans [Upload pins and path data](#upload-pins-and-path-data) (Charger des données d’épingles et de chemin). La requête doit ressembler à l’URL suivante (remplacez {`Your-Azure-Maps-Primary-Subscription-key}` par votre clé d’abonnement principale) :

   ```HTTP
     https://us.atlas.microsoft.com/mapData/operations/{statusUrl}?api-version=2.0&subscription-key={Your-Azure-Maps-Primary-Subscription-key}
   ```

6. Sélectionnez **Envoyer**.

7. Dans la fenêtre de réponse, sélectionnez l’onglet **Headers** (En-têtes).

8. Copiez la valeur de la clé **Resource-Location**, qui correspond à `resource location URL`. `resource location URL` contient l’identificateur unique (`udid`) de la ressource de package de dessin.

    :::image type="content" source="./media/how-to-render-custom-data/resource-location-url.png" alt-text="Copiez l’URL de l’emplacement de la ressource.":::

### <a name="render-uploaded-features-on-the-map"></a>Afficher les fonctionnalités chargées sur la carte

Pour afficher les données d’épingles et de chemin chargées sur la carte :

1. Dans l’application Postman, sélectionnez **New** (Nouveau).

2. Dans la fenêtre **Create New** (Créer), sélectionnez **HTTP Request** (Requête HTTP).

3. Entrez un **Request name** (Nom de demande) pour la demande, par exemple *GET Data Upload Status*.

4. Sélectionnez la méthode HTTP **GET**.

5. Entrez l’URL suivante pour le [service de rendu](/rest/api/maps/render/get-map-image) (remplacez {`Your-Azure-Maps-Primary-Subscription-key}` par votre clé d’abonnement principale et `udid` par l’`udid` des données chargées) :

    ```HTTP
    https://atlas.microsoft.com/map/static/png?subscription-key={Your-Azure-Maps-Primary-Subscription-key}&api-version=1.0&layer=basic&style=main&zoom=12&center=-73.96682739257812%2C40.78119135317995&pins=default|la-35+50|ls12|lc003C62|co9B2F15||'Times Square'-73.98516297340393 40.758781646381024|'Central Park'-73.96682739257812 40.78119135317995&path=lc0000FF|fc0000FF|lw3|la0.80|fa0.30||udid-{udId}
    ```

6. Le service retourne l’image suivante :

    :::image type="content" source="./media/how-to-render-custom-data/uploaded-path.png" alt-text="Affichez les données chargées dans une image de carte statique.":::

## <a name="render-a-polygon-with-color-and-opacity"></a>Afficher un polygone avec une couleur et une opacité

> [!Note]
> Un compte Azure Maps du niveau tarifaire Gen1 (S1) ou Gen2 est nécessaire pour suivre la procédure de cette section.

Vous pouvez modifier l’apparence d’un polygone en utilisant des modificateurs de style avec le [paramètre path](/rest/api/maps/render/getmapimage#uri-parameters).

Pour afficher un polygone avec une couleur et une opacité :

1. Dans l’application Postman, sélectionnez **New** (Nouveau).

2. Dans la fenêtre **Create New** (Créer), sélectionnez **HTTP Request** (Requête HTTP).

3. Entrez un **Request name** (Nom de requête) pour la requête, par exemple *GET Polygon*.

4. Sélectionnez la méthode HTTP **GET**.

5. Entrez l’URL suivante pour le [service de rendu](/rest/api/maps/render/get-map-image) (remplacez {`Your-Azure-Maps-Primary-Subscription-key}` par votre clé d’abonnement principale) :
  
    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&sku=S1&zoom=14&height=500&Width=500&center=-74.040701, 40.698666&path=lc0000FF|fc0000FF|lw3|la0.80|fa0.50||-74.03995513916016 40.70090237454063|-74.04082417488098 40.70028420372218|-74.04113531112671 40.70049568385827|-74.04298067092896 40.69899904076542|-74.04271245002747 40.69879568992435|-74.04367804527283 40.6980961582905|-74.04364585876465 40.698055487620714|-74.04368877410889 40.698022951066996|-74.04168248176573 40.696444909137|-74.03901100158691 40.69837271818651|-74.03824925422668 40.69837271818651|-74.03809905052185 40.69903971085914|-74.03771281242369 40.699340668780984|-74.03940796852112 40.70058515602143|-74.03948307037354 40.70052821920425|-74.03995513916016 40.70090237454063
    &subscription-key={Your-Azure-Maps-Primary-Subscription-key}
    ```

6. Le service retourne l’image suivante :

    :::image type="content" source="./media/how-to-render-custom-data/opaque-polygon.png" alt-text="Affichez un polygone opaque.":::

## <a name="render-a-circle-and-pushpins-with-custom-labels"></a>Afficher un cercle et des repères avec des étiquettes personnalisées

> [!Note]
> Un compte Azure Maps du niveau tarifaire Gen1 (S1) ou Gen2 est nécessaire pour suivre la procédure de cette section.

Vous pouvez modifier l’apparence des repères en ajoutant des modificateurs de style. Par exemple, pour augmenter ou diminuer la taille des clics-infos et de leurs étiquettes, utilisez le modificateur de « style de l’échelle » `sc`. Ce modificateur accepte une valeur supérieure à zéro. La valeur 1 représente l’échelle standard. Les valeurs supérieures à 1 agrandissent les repères tandis que les valeurs qui lui sont inférieures les diminuent. Pour plus d’informations sur les modificateurs de style, consultez [Paramètres du chemin du service d’image statique](/rest/api/maps/render/getmapimage#uri-parameters).

Pour afficher un cercle et des repères avec des étiquettes personnalisées :

1. Dans l’application Postman, sélectionnez **New** (Nouveau).

2. Dans la fenêtre **Create New** (Créer), sélectionnez **HTTP Request** (Requête HTTP).

3. Entrez un **Request name** (Nom de requête) pour la requête, par exemple *GET Polygon*.

4. Sélectionnez la méthode HTTP **GET**.

5. Entrez l’URL suivante pour le [service de rendu](/rest/api/maps/render/get-map-image) (remplacez {`Your-Azure-Maps-Primary-Subscription-key}` par votre clé d’abonnement principale) :

    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&zoom=14&height=700&Width=700&center=-122.13230609893799,47.64599069048016&path=lcFF0000|lw2|la0.60|ra1000||-122.13230609893799 47.64599069048016&pins=default|la15+50|al0.66|lc003C62|co002D62||'Microsoft Corporate Headquarters'-122.14131832122801  47.64690503939462|'Microsoft Visitor Center'-122.136828 47.642224|'Microsoft Conference Center'-122.12552547454833 47.642940335653996|'Microsoft The Commons'-122.13687658309935  47.64452336193245&subscription-key={Your-Azure-Maps-Primary-Subscription-key}
    ```

6. Sélectionnez **Envoyer**.

7. Le service retourne l’image suivante :

    :::image type="content" source="./media/how-to-render-custom-data/circle-custom-pins.png" alt-text="Affichez un cercle avec des repères personnalisés.":::

8. À présent, nous allons modifier la couleur des repères en modifiant le modificateur de style `co`. Si vous observez la valeur du paramètre `pins` (`pins=default|la15+50|al0.66|lc003C62|co002D62|`), vous constatez que la couleur actuelle est `#002D62`. Pour appliquer la couleur `#41d42a`, nous allons remplacer `#002D62` par `#41d42a`.  Le paramètre `pins` est maintenant `pins=default|la15+50|al0.66|lc003C62|co41D42A|`. La requête ressemble à l’URL suivante :

    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&zoom=14&height=700&Width=700&center=-122.13230609893799,47.64599069048016&path=lcFF0000|lw2|la0.60|ra1000||-122.13230609893799 47.64599069048016&pins=default|la15+50|al0.66|lc003C62|co41D42A||'Microsoft Corporate Headquarters'-122.14131832122801  47.64690503939462|'Microsoft Visitor Center'-122.136828 47.642224|'Microsoft Conference Center'-122.12552547454833 47.642940335653996|'Microsoft The Commons'-122.13687658309935  47.64452336193245&subscription-key={Your-Azure-Maps-Primary-Subscription-key}
    ```

9. Sélectionnez **Envoyer**.

10. Le service retourne l’image suivante :

    :::image type="content" source="./media/how-to-render-custom-data/circle-updated-pins.png" alt-text="Affichez un cercle avec des repères mis à jour.":::

De même, vous pouvez modifier, ajouter et supprimer d’autres modificateurs de style.

## <a name="next-steps"></a>Étapes suivantes

* Explorez la documentation de l’[API Azure Maps Get Map Image](/rest/api/maps/render/getmapimage).
* Pour en savoir plus sur le service de données Azure Maps, consultez la [documentation du service](/rest/api/maps/data).
