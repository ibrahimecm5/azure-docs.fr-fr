---
title: Télécharger la bibliothèque de client OSM
description: Télécharger et configurer la bibliothèque de client Open Service Mesh (OSM)
services: container-service
ms.topic: article
ms.date: 8/26/2021
ms.custom: mvc, devx-track-azurecli
ms.author: pgibson
zone_pivot_groups: client-operating-system
ms.openlocfilehash: 2524dca92126ac0fdd6c0ace502cc16d78b6d480
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131066840"
---
# <a name="download-and-configure-the-open-service-mesh-osm-client-library"></a>Télécharger et configurer la bibliothèque de client Open Service Mesh (OSM)

Cet article explique comment télécharger la bibliothèque de client OSM à utiliser pour exploiter et configurer le module complémentaire OSM pour AKS, et comment configurer le fichier binaire pour votre environnement.

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Linux - download and install client binary](includes/servicemesh/osm/open-service-mesh-binary-install-linux.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [macOS - download and install client binary](includes/servicemesh/osm/open-service-mesh-binary-install-macos.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [Windows - download and install client binary](includes/servicemesh/osm/open-service-mesh-binary-install-windows.md)]

::: zone-end

> [!WARNING]
> N’essayez pas d’installer OSM à partir du fichier binaire avec `osm install`. Cela aboutira à une installation d’OSM qui n’est pas intégrée en tant que module complémentaire pour AKS.

## <a name="configure-osm-cli-variables-with-an-osm_config-file"></a>Configurer les variables CLI OSM avec un fichier OSM_CONFIG

Les utilisateurs peuvent remplacer la configuration CLI OSM par défaut pour améliorer l’expérience du module complémentaire. Pour ce faire, vous pouvez créer un fichier de configuration, semblable à `kubeconfig`. Le fichier de configuration peut être créé au niveau de `$HOME/.osm/config.yaml` ou dans un chemin différent qui est exporté à l’aide de la variable d’environnement `OSM_CONFIG`.

Le fichier doit contenir le contenu au format YAML suivant :

```yaml
install:
  kind: managed
  distribution: AKS
  namespace: kube-system
```

Si le fichier n’est pas créé dans `$HOME/.osm/config.yaml`, n’oubliez pas de définir la variable d’environnement `OSM_CONFIG` pour qu’elle pointe vers le chemin où le fichier de configuration est créé.

Après avoir défini OSM_CONFIG, la sortie de la commande `osm env` doit être la suivante :

```console
$ osm env
---
install:
  kind: managed
  distribution: AKS
  namespace: kube-system
```
