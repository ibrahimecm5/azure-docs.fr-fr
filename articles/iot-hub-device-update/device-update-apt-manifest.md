---
title: Comprendre le manifeste APT Device Update pour Azure IoT Hub | Microsoft Docs
description: Découvrez comment Device Update pour IoT Hub utilise le manifeste APT pour une mise à jour basée sur un package.
author: vimeht
ms.author: vimeht
ms.date: 2/17/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: 24c0d04b6a29b813dc23604594ca4b1b1235dc37
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132373210"
---
# <a name="device-update-apt-manifest"></a>Manifeste APT Device Update

Le manifeste APT est un fichier JSON qui décrit les détails d’une mise à jour dont le gestionnaire de mise à jour APT a besoin. Ce fichier peut être importé dans Device Update pour IoT Hub comme n’importe quelle autre mise à jour.

[En savoir plus](import-update.md) sur l’importation des mises à jour dans Device Update.

## <a name="overview"></a>Vue d’ensemble

Quand un manifeste APT est délivré à un agent Device Update en tant que mise à jour, l’agent traite le manifeste et effectue les opérations nécessaires. Ces opérations incluent le téléchargement et l’installation des packages spécifiés dans le fichier manifeste APT et leurs dépendances à partir d’un référentiel désigné.

Device Update prend en charge APT UpdateType et le gestionnaire de mise à jour APT. Cette prise en charge permet à l’agent Device Update d’évaluer les packages Debian installés et de mettre à jour les packages nécessaires. 

## <a name="schema"></a>schéma

Un fichier manifeste APT est un fichier JSON avec un schéma versionné.

```json
{
    "name": "<name>",
    "version": "<version>",
    "packages": [
        {
            "name": "<package name>",
            "version": "<version specifier>"
        }
    ]
}
```

Exemple :

```json
{
    "name": "contoso-iot-edge",
    "version": "1.0.0.0",
    "packages": [
        {
            "name" : "thermocontrol",
            "version" : "1.0.1"
        },
        {
            "name" : "tempreport",
            "version" : "2.0.0"
        }
    ]
}
```

### <a name="name"></a>name

Nom de ce manifeste APT. Il peut s’agir de n’importe quel nom ou ID significatif pour vos scénarios. Par exemple : `contoso-iot-edge`.

### <a name="version"></a>version

Numéro de version pour ce manifeste APT. Par exemple : `1.0.0.0`.


### <a name="packages"></a>packages

Liste d’objets contenant des propriétés spécifiques au package.

#### <a name="name"></a>name

Nom ou ID du package. Par exemple : `iotedge`.

#### <a name="version"></a>version

Les critères de la version souhaitée pour le package. Par exemple : `1.0.8-2`.

Actuellement, seul le numéro de version exact est pris en charge. Le numéro de version est la version du package Debian souhaitée au format [époque:]version_amont[-révision_debian].

**époque** est un entier non signé.

**version_amont** peut inclure des caractères alphanumériques et des caractères comme « . », « + », « - » et « ~ ». Il doit commencer par un chiffre.
> [!NOTE]
> « 1.0.8 » équivaut à « 1.0.8-0 »

Par exemple, **`"name":"iotedge" and "version":"1.0.8-2"`** équivaut à installer un package en utilisant la commande `apt-get install iotedge=1.0.8-2`

> [!NOTE]
> La valeur de la version ne contient pas de signe égal

Si la version est omise, la dernière version disponible du package spécifié sera installée.

[En savoir plus](https://www.debian.org/doc/debian-policy/ch-controlfields.html#s-f-version) sur la gestion des versions des packages Debian.

> [!NOTE]
> Le gestionnaire de package APT ignore les spécifications de version données par un package quand les packages dépendants à installer sont résolus automatiquement. À moins que des versions explicites des packages dépendants ne soient fournies, elles utiliseront la dernière version, même si le package lui-même peut spécifier une exigence stricte (=) sur une version donnée. Cette résolution automatique peut entraîner des erreurs concernant une dépendance non satisfaite. [En savoir plus](https://unix.stackexchange.com/questions/350192/apt-get-not-properly-resolving-a-dependency-on-a-fixed-version-in-a-debian-ubunt)

Si vous mettez à jour une version spécifique du démon de sécurité d’Azure IoT Edge, vous devez inclure la version souhaitée du package `iotedge` et son package dépendant `libiothsm-std` dans votre manifeste APT.
[En savoir plus](../iot-edge/how-to-update-iot-edge.md#update-the-security-daemon)

> [!NOTE]
> Un manifeste APT peut être utilisé pour mettre à jour l’agent Device Update et ses dépendances. Spécifiez le nom et la version souhaitée de l’agent Device Update dans le manifeste APT, comme vous le feriez pour n’importe quel autre package. Ce manifeste APT peut ensuite être importé et déployé via le pipeline Device Update pour IoT Hub. 

## <a name="removing-packages"></a>Suppression de packages

Vous pouvez aussi utiliser un manifeste APT pour supprimer des packages installés de votre appareil. Un même manifeste APT peut être utilisé pour supprimer, ajouter et mettre à jour plusieurs packages. Pour supprimer un package, ajoutez un signe moins « - » après le nom du package. Vous ne devez pas inclure un numéro de version pour les packages que vous supprimez. La suppression de packages via un manifeste APT ne supprime pas ses dépendances ni ses configurations.

Exemple :

```json
{
    "name": "contoso-video",
    "version": "2.0.0.1",
    "packages": [
        {
            "name" : "foo-"
        }
    ]
}
```
Ce manifeste APT va supprimer le package « foo » du ou des appareils sur lesquels il est déployé. 

## <a name="recommended-value-for-installed-criteria"></a>Valeur recommandée pour le critère Installed

Le critère Installed pour un manifeste APT est `<name>-<version>`, où `<name>` est le nom du manifeste APT et `<version>` est la version du manifeste APT. Par exemple : `contoso-iot-edge-1.0.0.0`. 

## <a name="guidelines-on-creating-an-apt-manifest"></a>Instructions pour la création d’un manifeste APT

Lors de la création du manifeste APT, tenez compte des instructions suivantes :

- Vérifiez toujours que le manifeste APT est un fichier JSON bien formé
- Chaque manifeste APT doit avoir une version unique. Essayez de trouver une méthodologie standardisée pour incrémenter la version du manifeste APT, afin qu’il soit logique pour vos scénarios et puisse être facilement suivi.
- Quand il s’agit de l’état souhaité de chaque package individuel, spécifiez le nom et la version exacts du package que vous voulez installer sur votre appareil. Vérifiez toujours les valeurs par rapport au référentiel de packages que vous prévoyez d’utiliser comme source pour le package.
- Vérifiez que les packages du manifeste APT sont listés dans l’ordre où ils doivent être installés/supprimés.
- Vérifiez toujours l’installation des packages sur un appareil de test pour être sûr que le résultat est bien ce que vous souhaitez
- Lors de l’installation d’une version spécifique d’un package (par exemple `iotedge 1.0.9-1`), c’est une bonne pratique que d’avoir aussi dans le manifeste APT les versions explicites des packages dépendants à installer (par exemple `libiothsm 1.0.9-1`).
- Bien que ce ne soit pas obligatoire, vérifiez toujours que votre manifeste APT est cumulatif pour éviter de placer votre appareil dans un état inconnu. Une mise à jour cumulative garantit que vos appareils disposent de la version souhaitée de chaque package qui vous intéresse, même si l’appareil a ignoré un déploiement de mise à jour APT en raison d’une défaillance dans l’installation ou en cas de déconnexion.

Par exemple :

**Manifeste APT de base**

```JSON
{
    "name": "contoso-iot-edge",
    "version": "1.0",
    "packages": [
        {
            "name": "foo",
            "version": "1.0.1"
        }
    ]
}
```

**MISE À JOUR INCORRECTE**

Cette mise à jour inclut le package « bar », mais pas le package « foo ».

```JSON
{
    "name": "contoso-iot-edge",
    "version": "2.0",
    "packages": [
        {
            "name": "bar",
            "version": "3.0.2"
        }
    ]
}
```

**MISE À JOUR CORRECTE**

Cette mise à jour inclut le package « foo » et inclut également le package « bar ».

```JSON
{
    "name": "contoso-iot-edge",
    "version": "2.0",
    "packages": [
        {
            "name": "foo",
            "version": "1.0.1"
        },
        {
            "name": "bar",
            "version": "3.0.2"
        }
    ]
}
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Importer une nouvelle mise à jour](import-update.md)