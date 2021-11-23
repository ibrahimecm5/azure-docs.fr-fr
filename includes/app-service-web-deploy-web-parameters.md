---
author: cephalin
ms.service: app-service-web
ms.topic: include
ms.date: 11/03/2016
ms.author: cephalin
ms.openlocfilehash: af26bc94e96c9d4bd1fdf59d5479bdc7181221f5
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132298509"
---
Azure Resource Manager vous permet de définir des paramètres pour les valeurs que vous voulez spécifier lorsque le modèle est déployé. Ce modèle inclut une section appelée Paramètres, qui contient toutes les valeurs de paramètres. Vous devez définir un paramètre pour les valeurs qui varient en fonction du projet que vous déployez ou de l’environnement dans lequel vous effectuez le déploiement. Ne définissez pas de paramètres pour les valeurs qui sont constantes. Chaque valeur de paramètre est utilisée dans le modèle pour définir les ressources déployées.

Lorsque vous définissez les paramètres, utilisez le champ **allowedValues** pour spécifier les valeurs qu'un utilisateur peut fournir au cours du déploiement. Utilisez le champ **defaultValue** pour affecter une valeur au paramètre, si aucune valeur n'est fournie pendant le déploiement.

Nous allons décrire chaque paramètre du modèle.

### <a name="sitename"></a>siteName

Le nom de l'application web que vous souhaitez créer.

```config
"siteName":{
  "type":"string"
}
```

### <a name="hostingplanname"></a>hostingPlanName

Le nom du plan App Service à utiliser pour héberger l'application web.

```config
"hostingPlanName":{
  "type":"string"
}
```

### <a name="sku"></a>sku

Le niveau de tarification du plan d'hébergement.

```json
"sku": {
  "type": "string",
  "allowedValues": [
    "F1",
    "D1",
    "B1",
    "B2",
    "B3",
    "S1",
    "S2",
    "S3",
    "P1",
    "P2",
    "P3",
    "P4"
  ],
  "defaultValue": "S1",
  "metadata": {
    "description": "The pricing tier for the hosting plan."
  }
}
```

Le modèle définit les valeurs autorisées pour ce paramètre et affecte une valeur par défaut de `S1` si aucune valeur n’est spécifiée.

### <a name="workersize"></a>workerSize

La taille d'instance du plan d'hébergement (petite, moyenne ou grande).

```json
"workerSize":{
  "type":"string",
  "allowedValues":[
    "0",
    "1",
    "2"
  ],
  "defaultValue":"0"
}
```

Le modèle définit les valeurs autorisées pour ce paramètre (`0`, `1` ou `2`), et affecte une valeur par défaut de `0` si aucune valeur n’est spécifiée. Les valeurs correspondent à une taille petite, moyenne et grande.
