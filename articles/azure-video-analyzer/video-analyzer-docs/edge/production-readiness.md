---
title: Préparation à la production et bonnes pratiques
description: Cet article fournit des conseils sur la configuration et le déploiement du module périphérique Azure Video Analyzer dans des environnements de production.
ms.topic: reference
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: d25965ed8143b8d74f33794fbc97282502b84b96
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131560185"
---
# <a name="production-readiness-and-best-practices"></a>Préparation à la production et bonnes pratiques

[!INCLUDE [header](includes/edge-env.md)]

Cet article fournit des conseils sur la configuration et le déploiement du module Edge et du service cloud Video Analyzer dans des environnements de production. Consultez également l'article [Préparer le déploiement en production d'une solution IoT Edge](../../../iot-edge/production-checklist.md) consacré à la préparation d'une solution IoT Edge.

Pour les aspects liés à la sécurité, contactez le service informatique de votre organisation.

## <a name="creating-the-video-analyzer-account"></a>Création du compte Video Analyzer

Lorsque vous [créez](../create-video-analyzer-account.md) un compte Video Analyzer, l’approche recommandée est la suivante :

1. Le propriétaire de l’abonnement doit créer un groupe de ressources sous lequel doivent être créées toutes les ressources dont Video Analyzer a besoin.
1. Ensuite, le propriétaire doit vous accorder les rôles [Contributeur](../../../role-based-access-control/built-in-roles.md#contributor) et [Administrateur de l’accès utilisateur](../../../role-based-access-control/built-in-roles.md#user-access-administrator) pour ce groupe de ressources.
1. Vous pouvez ensuite créer les ressources appropriées : compte de stockage, hub IoT, identité managée affectée par l’utilisateur et compte Video Analyzer sous ce groupe de ressources.

## <a name="running-the-module-as-a-local-user"></a>Exécution du module en tant qu'utilisateur local

Lorsque vous déployez le module Edge Video Analyzer sur un appareil IoT Edge, celui-ci s’exécute par défaut avec des privilèges élevés. Vous pouvez vérifier cela à l’aide des journaux du module (`sudo iotedge logs {name-of-module}`) qui devraient fournir les informations suivantes :

```
!! production readiness: user accounts – Warning
       LOCAL_USER_ID and LOCAL_GROUP_ID environment variables are not set. The program will run as root!
       For optimum security, make sure to set LOCAL_USER_ID and LOCAL_GROUP_ID environment variables to a non-root user and group.
```

Les sections ci-dessous expliquent comment répondre à cet avertissement.

### <a name="creating-and-using-a-local-user-account"></a>Création et utilisation d'un compte d'utilisateur local

Vous pouvez et devez exécuter le module Edge Video Analyzer en production à l’aide d’un compte doté du moins de privilèges possible. Les commandes suivantes, par exemple, montrent comment créer un compte d'utilisateur local sur une machine virtuelle Linux :

```
sudo groupadd -g 1010 localedgegroup
sudo useradd --home-dir /home/localedgeuser --uid 1010 --gid 1010 localedgeuser
```

Ensuite, dans le manifeste de déploiement, vous pouvez définir les variables d'environnement LOCAL_USER_ID et LOCAL_GROUP_ID pour l'utilisateur et le groupe non racine correspondants :

```
"avaedge": {
  "version": "1.0",
  …
  "env": {
    "LOCAL_USER_ID": {
       "value": "1010"
    },
    "LOCAL_GROUP_ID": {
     "value": "1010"
    }
  }
},
…
```

### <a name="granting-permissions-to-device-storage"></a>Octroi d'autorisations au stockage du périphérique

Le module Edge Video Analyzer doit avoir la possibilité d’écrire des fichiers dans le système de fichiers local dans les cas suivants :

- Utilisation d’une propriété de jumeau de module [`applicationDataDirectory`](module-twin-configuration-schema.md), sachant que vous devez spécifier un répertoire du système de fichiers local pour le stockage des données de configuration
- Utilisation d’un pipeline pour l’enregistrement de vidéo dans le cloud, sachant que le module requiert l’utilisation d’un répertoire sur le périphérique comme cache (voir l’article [Enregistrement vidéo continu](../continuous-video-recording.md) pour plus d’informations).
- [Enregistrement dans un fichier local](../event-based-video-recording-concept.md), sachant que vous spécifiez le chemin de la vidéo enregistrée.

Dans tous les cas, vous devez vous assurer que le compte d'utilisateur ci-dessus a accès au répertoire qui convient. Prenons l’exemple `applicationDataDirectory`. Vous pouvez créer un répertoire sur le périphérique et lier le stockage du périphérique au stockage du module.

```
sudo mkdir /var/lib/videoanalyzer
sudo chown -R localedgeuser:localedgegroup /var/lib/videoanalyzer
```

Ensuite, à partir des options de création du module de périphérie dans le manifeste de déploiement, vous pouvez ajouter un paramètre `binds` mappant le répertoire (« /var/lib/videoanalyzer ») ci-dessus à un répertoire dans le module (par exemple, « /var/lib/videoanalyzer »). Et vous devez utiliser ce dernier répertoire comme valeur pour `applicationDataDirectory`.

```
        "modules": {
          "avaedge": {
            "version": "1.1",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "mcr.microsoft.com/media/video-analyzer:1",
              "createOptions": "{ \"HostConfig\": { \"LogConfig\": { \"Type\": \"\", \"Config\": { \"max-size\": \"10m\", \"max-file\": \"10\" } }, \"Binds\": [ \"/var/media/:/var/media/\", \"/var/lib/videoanalyzer/:/var/lib/videoanalyzer\" ], \"IpcMode\": \"host\", \"ShmSize\": 1536870912 } }"
            },
            "env": {
              "LOCAL_USER_ID": {
                "value": "1010"
              },
              "LOCAL_GROUP_ID": {
                "value": "1010"
              }
            }
          },
          …
        },
        
    …
    
    "avaedge": {
       "properties.desired": {
          "applicationDataDirectory": "/var/lib/videoanalyzer",
          "ProvisioningToken": "{your-token}",
          "diagnosticsEventsOutputName": "diagnostics",
          "operationalEventsOutputName": "operational",
          "logLevel": "information",
          "LogCategories": "Application,Events",
          "allowUnsecuredEndpoints": false,
          "telemetryOptOut": false
    }
}
```

Si vous examinez les exemples de pipelines pour le démarrage rapide et des tutoriels tels que [Enregistrement vidéo continu](use-continuous-video-recording.md), vous remarquerez que le répertoire du cache multimédia (`localMediaCachePath`) utilise un sous-répertoire sous `applicationDataDirectory`. Il s'agit de l'approche recommandée, car le cache contient des données temporaires.

Notez également que `allowedUnsecuredEndpoints` a la valeur `false`, comme recommandé pour les environnements de production où vous allez utiliser le chiffrement TLS pour sécuriser le trafic.

### <a name="tips-about-maintaining-your-edge-device"></a>Conseils sur la maintenance de votre périphérique

Les conseils ci-dessous ne constituent pas une liste exhaustive, mais devraient vous aider à résoudre des problèmes connus couramment rencontrés.

La machine virtuelle Linux que vous utilisez en tant que périphérique IoT Edge peut cesser de répondre en l’absence de gestion régulière. Il est essentiel de nettoyer les caches, d'éliminer les packages inutiles et de supprimer les conteneurs inutilisés de la machine virtuelle. Pour ce faire, voici un ensemble de commandes recommandées que vous pouvez utiliser sur votre machine virtuelle de périphérie.

- `sudo apt-get clean`

La commande apt-get clean efface du référentiel local les fichiers de package récupérés qui sont conservés dans/var/cache. Les répertoires qu'elle nettoie sont /var/cache/apt/archives/ et /var/cache/apt/archives/partial/. Les seuls fichiers qu'elle laisse dans/var/cache/apt/archives sont le fichier de verrouillage et le sous-répertoire partiel. La commande apt-get clean est généralement utilisée pour libérer de l'espace disque en fonction des besoins, notamment dans le cadre d'une maintenance planifiée régulière. Pour plus d’informations, consultez [Nettoyage avec apt-get](https://www.networkworld.com/article/3453032/cleaning-up-with-apt-get.html).

- `sudo apt-get autoclean`

L'option apt-get autoclean, comme apt-get clean, efface du référentiel local les fichiers de package récupérés, mais elle supprime uniquement les fichiers qui ne peuvent plus être téléchargés et qui sont inutiles. Cela permet d'éviter que votre cache ne devienne trop volumineux.

- `sudo apt-get autoremove`

L'option auto remove supprime les packages qui ont été installés automatiquement parce que d'autres packages en avaient besoin, mais qui ne sont plus nécessaires suite à la suppression de ces autres packages.

- `sudo docker image ls` : fournit une liste d'images Docker sur votre système de périphérie

- `sudo docker system prune`

Docker adopte une approche conservatrice en matière de nettoyage des objets inutilisés (souvent appelés « garbage collection »), tels que les images, les conteneurs, les volumes et les réseaux : ces objets ne sont généralement pas supprimés, sauf si vous le demandez explicitement à Docker. Cela peut amener Docker à utiliser de l'espace disque supplémentaire. Pour chaque type d'objet, Docker fournit une commande d'élagage. En outre, vous pouvez utiliser la commande `docker system prune` pour nettoyer plusieurs types d’objets à la fois. Pour plus d’informations, consultez [Élagage d’objets Docker inutilisés](https://docs.docker.com/config/pruning/).

- `sudo docker rmi REPOSITORY:TAG`

Au fil des mises à jour du module de périphérie, votre instance de Docker peut contenir des versions anciennes de celui-ci. Dans ce cas, il est conseillé d’utiliser la commande `docker rmi` pour supprimer des images spécifiques identifiées par leur balise de version.

## <a name="next-steps"></a>Étapes suivantes

[Démarrage rapide : Bien démarrer avec Azure Video Analyzer](get-started-detect-motion-emit-events.md)
