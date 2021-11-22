---
title: Connectivité des appareils dans Azure IoT Central | Microsoft Docs
description: Cet article présente les concepts clés relatifs à la connectivité des appareils dans Azure IoT Central
author: dominicbetts
ms.author: dobett
ms.date: 09/07/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom:
- amqp
- mqtt
- device-developer
ms.openlocfilehash: e35f61d53ead7772743f31acfad686ec62a707af
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132492905"
---
# <a name="get-connected-to-azure-iot-central"></a>Se connecter à Azure IoT Central

Cet article explique comment les appareils se connectent à une application Azure IoT Central. Pour échanger des données avec IoT Central, un appareil doit :

- *S’authentifier*. L’authentification auprès de l’application IoT Central utilise un _jeton de signature d’accès partagé (SAP)_ ou un _certificat X.509_. Les certificats X.509 sont recommandés dans les environnements de production.
- *S’inscrire*. Les appareils doivent être inscrits auprès de l’application IoT Central. Vous pouvez afficher les appareils inscrits sur la page **Appareils** de l’application.
- *S’associer à un modèle d’appareil*. Dans une application IoT Central, les modèles d’appareil définissent l’interface utilisateur utilisée par les opérateurs pour afficher et gérer les appareils connectés.

IoT Central prend en charge les deux scénarios d’inscription d’appareil suivants :

- *Inscription automatique*. L’appareil est automatiquement inscrit lorsqu’il se connecte pour la première fois. Ce scénario permet également aux OEM de fabriquer en masse des appareils qui peuvent se connecter sans avoir été inscrits. Un OEM génère des informations d’identification appropriées et configure les appareils en usine. Vous pouvez également demander à un opérateur d’approuver l’appareil avant qu’il ne commence à envoyer des données. Ce scénario implique que vous configuriez une _inscription de groupe_ X.509 ou SAP dans votre application.
- *Inscription manuelle*. Les opérateurs inscrivent les appareils individuels sur la page **Appareils** ou [importent un fichier CSV](howto-manage-devices-in-bulk.md#import-devices) pour inscrire des appareils en bloc. Dans ce scénario, vous pouvez utiliser l’_inscription de groupe_ X.509 ou SAP ou l’_inscription individuelle_ X.509 ou SAP.

Les appareils qui se connectent à IoT Central doivent suivre les *conventions IoT Plug-and-Play*. L’une de ces conventions implique qu’un appareil envoie l’_ID de modèle_ du modèle d’appareil qu’il implémente lorsqu’il se connecte. L’ID de modèle permet à l’application IoT Central d’associer l’appareil au modèle d’appareil qui convient.

IoT Central utilise le [service Azure IoT Device Provisioning (DPS)](../../iot-dps/about-iot-dps.md) pour gérer le processus de connexion. Un appareil se connecte d’abord à un point de terminaison DPS pour récupérer les informations dont il a besoin pour se connecter à votre application. En interne, votre application IoT Central utilise un hub IoT pour gérer la connectivité des appareils. L’utilisation de DPS permet :

- à IoT Central de prendre en charge l’intégration et la connexion des appareils à l’échelle ;
- aux clients de générer des informations d’identification d’appareils et de les configurer hors ligne sans avoir à les inscrire via l’interface utilisateur d’IoT Central ;
- aux clients d’utiliser leurs propres ID d’appareils pour inscrire des appareils dans IoT Central. L’utilisation de vos propres ID d’appareils simplifie l’intégration dans des systèmes back-office existants.
- C’est une manière cohérente de connecter des appareils à IoT Central.

Cet article décrit la procédure de connexion des appareils :

- [Inscription de groupe X.509](#x509-group-enrollment)
- [Inscription de groupe SAP](#sas-group-enrollment)
- [Inscription individuelle](#individual-enrollment)
- [Enregistrement de l’appareil](#device-registration)
- [Associer un appareil à un modèle d’appareil](#associate-a-device-with-a-device-template)

## <a name="x509-group-enrollment"></a>Inscription de groupe X.509

Dans un environnement de production, l’utilisation de certificats X.509 est le mécanisme d’authentification des appareils recommandé pour IoT Central. Pour plus d’informations, consultez [Authentification des appareils à l’aide de certificats d’autorité de certification X.509](../../iot-hub/iot-hub-x509ca-overview.md).

Pour connecter un appareil avec un certificat X.509 à votre application :

1. Créez un *groupe d’inscription* qui utilise le type d’attestation **Certificats (X.509)** .
1. Ajoutez et vérifiez un certificat X.509 intermédiaire ou racine dans le groupe d’inscription.
1. Générez un certificat feuille à partir du certificat racine ou intermédiaire dans le groupe d’inscription. Envoyez le certificat feuille à partir de l’appareil lorsqu’il se connecte à votre application.

Pour plus d’informations, consultez [Connecter des appareils avec des certificats X.509](how-to-connect-devices-x509.md).

### <a name="for-testing-purposes-only"></a>Uniquement à des fins de test

À des fins de test uniquement, vous pouvez vous servir des utilitaires suivants pour générer des certificats racines, intermédiaires et d’appareils :

- [Outils pour le kit de développement logiciel (SDK) pour appareils Azure IoT Device Provisioning](https://github.com/Azure/azure-iot-sdk-node/blob/master/provisioning/tools/readme.md) : une collection d’outils Node.js que vous pouvez utiliser pour générer et vérifier des certificats X.509 et des clés.
- [Gérer les certificats d’autorité de certification de test pour les exemples et les tutoriels](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) : une collection de scripts PowerShell et Bash pour :
  - Créer une chaîne de certificat.
  - Enregistrez les certificats en tant que fichiers .cer à télécharger vers votre application IoT Central.
  - Le code de vérification de l’application IoT Central permet de générer le certificat de vérification.
  - Créez des certificats de nœud terminal pour vos appareils à l’aide de vos ID d’appareils en tant que paramètre de l’outil.

## <a name="sas-group-enrollment"></a>Inscription de groupe X.509

Pour connecter un appareil à votre application à l’aide d’une clé SAP :

1. Créez un *groupe d’inscription* utilisant le type d’attestation **Signature d’accès partagé (SAP)** . 
1. Copiez la clé primaire ou secondaire du groupe à partir du groupe d’inscription.
1. Utilisez Azure CLI pour générer une clé d’appareil à partir de la clé de groupe :

    ```azurecli
    az iot central device compute-device-key --primary-key <enrollment group primary key> --device-id <device ID>
    ```

1. Utilisez la clé d’appareil générée lorsque l’appareil se connecte à votre application IoT Central.

> [!NOTE]
> Pour utiliser des clés SAS existantes dans vos groupes d’inscription, désactivez le bouton bascule **Générer automatiquement les clés** et tapez les clés SAS.

## <a name="individual-enrollment"></a>Inscription individuelle

Les clients qui connectent des appareils ayant chacun leurs propres informations d’authentification utilisent les inscriptions individuelles. Il s’agit d’une entrée pour un seul appareil autorisé à se connecter. Les inscriptions individuelles peuvent utiliser des certificats feuilles X.509 ou des jetons SAP (à partir d’un module de plateforme sécurisée physique ou virtuel) comme mécanismes d’attestation. Un ID d’appareil peut contenir des lettres, des chiffres et le caractère `-`. Pour plus d’informations, consultez [Inscription DPS individuelle](../../iot-dps/concepts-service.md#individual-enrollment).

> [!NOTE]
> Lorsque vous créez une inscription individuelle pour un appareil, elle prend la priorité sur les options d’inscription de groupe par défaut de votre application IoT Central.

### <a name="create-individual-enrollments"></a>Créer des inscriptions individuelles

IoT Central prend en charge les mécanismes d’attestation suivants pour les inscriptions individuelles :

- **Attestation de clé symétrique :** L’attestation de clé symétrique est une approche simple pour authentifier un appareil avec une instance DPS. Pour créer une inscription individuelle qui utilise des clés symétriques, ouvrez la page **Connexion de l’appareil** correspondant à l’appareil, sélectionnez **Inscription individuelle** comme méthode de connexion, puis **Signature d’accès partagé (SAP)** comme mécanisme. Entrez les clés primaires et secondaires codées base64 et enregistrez vos modifications. Utilisez l'**étendue de l’ID**, l’**ID de l’appareil** et la clé primaire ou secondaire pour connecter votre appareil.

    > [!TIP]
    > À des fins de test, vous pouvez utiliser **OpenSSL** pour générer des clés codées base64 : `openssl rand -base64 64`

- **Certificats X.509 :** Pour créer une inscription individuelle avec des certificats X.509, ouvrez la page **Connexion de l’appareil**, sélectionnez **Inscription individuelle** comme méthode de connexion et **Certificats (X.509)** comme mécanisme. Les certificats d’appareils utilisés avec une entrée d’inscription individuelle requièrent que le nom commun de l’émetteur et du sujet soit l’ID de l’appareil.

    > [!TIP]
    > À des fins de test, vous pouvez utiliser les [outils pour le kit de développement logiciel (SDK) d’appareil Azure IoT Device Provisioning pour Node.js](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/tools) pour générer un certificat auto-signé : `node create_test_cert.js device "mytestdevice"`

- **Attestation de module de plateforme sécurisée (TPM) :** Un [module de plateforme sécurisée](../../iot-dps/concepts-tpm-attestation.md) est un type de module de sécurité matériel. L’utilisation d’un module de plateforme sécurisée (TPM) est l’un des moyens les plus sécurisés pour connecter un appareil. Cet article suppose que vous utilisez un microprogramme discret, ou un module de plateforme sécurisée (TPM) intégré. Les TPM logiciels émulés sont parfaitement adaptés pour le prototypage ou le test, mais ils n’offrent pas le même niveau de sécurité que les modules de plateforme sécurisée (TPM) intégrés ou les microprogrammes discrets. N’utilisez pas de module de plateforme sécurisée (TPM) logiciel en production. Pour créer une inscription individuelle utilisant un module de plateforme sécurisée (TPM), ouvrez la page **Connexion de l’appareil**, sélectionnez **Inscription individuelle** comme méthode de connexion et **Module de plateforme sécurisée (TPM)** comme mécanisme. Entrez la paire de clés de type EK (Endorsement Key) et enregistrez les informations de connexion de l’appareil.

## <a name="device-registration"></a>Enregistrement de l’appareil

Pour se connecter à une application IoT Central, un appareil doit être inscrit dans l’application :

- Les appareils peuvent automatiquement s’inscrire lorsqu’ils se connectent pour la première fois. Pour utiliser cette option, vous devez utiliser l’[inscription de groupe X.509](#x509-group-enrollment) ou l’[inscription de groupe SAP](#sas-group-enrollment).
- Un opérateur peut importer un fichier CSV pour inscrire en bloc une liste d’appareils dans l’application.
- Un opérateur peut manuellement inscrire un appareil individuel sur la page **Appareils** de l’application.

IoT Central permet aux OEM de fabriquer en masse des appareils qui peuvent s’inscrire automatiquement. Un OEM génère des informations d’identification appropriées et configure les appareils en usine. Lorsqu’un client active un appareil pour la première fois, il se connecte à DPS, qui connecte à son tour automatiquement l’appareil à la bonne application IoT Central. Vous pouvez également exiger d’un opérateur qu’il approuve l’appareil avant que ce dernier ne commence à envoyer des données à l’application.

> [!TIP]
> Sur la page **Administration > Connexion de l’appareil**, l’option **Approbation automatique** détermine si un opérateur doit approuver manuellement l’appareil avant qu’il puisse commencer à envoyer des données.

### <a name="automatically-register-devices-that-use-x509-certificates"></a>Inscrire automatiquement des appareils utilisant des certificats X.509

1. Générez des certificats feuilles pour vos appareils à l’aide du certificat racine ou intermédiaire que vous avez ajouté à votre [groupe d’inscription X.509](#x509-group-enrollment). Utilisez les ID d’appareils comme `CNAME` dans les certificats feuilles. Un ID d’appareil peut contenir des lettres, des chiffres et le caractère `-`.

1. En tant qu’OEM, dotez chaque appareil d’un ID d’appareil, d’un certificat feuille X.509 généré et de la valeur **Étendue de l’ID** de l’application. Le code de l’appareil doit également envoyer l’ID de modèle du modèle d’appareil qu’il implémente.

1. Lorsque vous basculez sur un appareil, il se connecte d’abord à DPS pour récupérer ses informations de connexion IoT Central.

1. L’appareil utilise les informations du DPS pour se connecter et s’inscrire auprès de votre application IoT Central.

L’application IoT Central utilise l’ID de modèle envoyé par l’appareil pour [associer l’appareil inscrit à un modèle d’appareil](#associate-a-device-with-a-device-template).

### <a name="automatically-register-devices-that-use-sas-tokens"></a>Inscrire automatiquement des appareils utilisant des jetons SAP

1. Copiez la clé primaire du groupe à partir du groupe d’inscription **SAS-IoT-Devices** :

    :::image type="content" source="media/concepts-get-connected/group-primary-key.png" alt-text="Clé primaire du groupe à partir du groupe d’inscription SAS-IoT-Devices":::

1. Utilisez la commande `az iot central device compute-device-key` pour générer les clés SAS des appareils. Utilisez la clé primaire de groupe de l’étape précédente. L’ID d’appareil peut contenir des lettres, des chiffres et le caractère `-` :

    ```azurecli
    az iot central device compute-device-key --primary-key <enrollment group primary key> --device-id <device ID>
    ```

1. En tant qu’OEM, dotez chaque appareil d’un ID d’appareil, d’une clé SAP d’appareil générée et de la valeur d'**étendue d’ID** de l’application. Le code de l’appareil doit également envoyer l’ID de modèle du modèle d’appareil qu’il implémente.

1. Lorsque vous basculez sur un appareil, il se connecte d’abord à DPS pour récupérer ses informations d’inscription à IoT Central.

1. L’appareil utilise les informations du DPS pour se connecter et s’inscrire auprès de votre application IoT Central.

L’application IoT Central utilise l’ID de modèle envoyé par l’appareil pour [associer l’appareil inscrit à un modèle d’appareil](#associate-a-device-with-a-device-template).

### <a name="bulk-register-devices-in-advance"></a>Inscrire à l’avance des appareils en bloc

Pour inscrire un grand nombre d’appareils avec votre application IoT Central, utilisez un fichier CSV pour [importer les ID et les noms des appareils](howto-manage-devices-in-bulk.md#import-devices).

Si vos appareils utilisent des jetons SAP pour s’authentifier, [exportez un fichier CSV à partir de votre application IoT Central](howto-manage-devices-in-bulk.md#export-devices). Le fichier CSV exporté inclut les ID des appareils et les clés SAS.

Si vos appareils utilisent des certificats X.509 pour s’authentifier, générez des certificats feuilles X.509 pour vos appareils à l’aide du certificat racine ou intermédiaire que vous avez chargé dans votre groupe d’inscription X.509. Utilisez les ID des appareils que vous avez importés en tant que valeur `CNAME` dans les certificats feuilles.

Les appareils doivent utiliser la valeur **Étendue de l'ID** pour votre application et envoyer un ID de modèle lorsqu’ils se connectent.

> [!TIP]
> Vous pouvez trouver la valeur **Étendue de l'ID** sous **Administration > Connexion de l’appareil**.

### <a name="register-a-single-device-in-advance"></a>Inscrire un appareil unique à l’avance

Cette approche est pratique quand vous faites des essais avec IoT Central ou quand vous testez des appareils. Sélectionnez **+ Nouveau** dans la page **Appareils** pour inscrire un appareil individuel. Vous pouvez utiliser les clés SAP de connexion de l’appareil pour connecter l’appareil à votre application IoT Central. Copiez la _clé SAP de l’appareil_ à partir des informations de connexion d’un appareil inscrit :

![Clés SAP pour un appareil individuel](./media/concepts-get-connected/single-device-sas.png)

## <a name="associate-a-device-with-a-device-template"></a>Associer un appareil à un modèle d’appareil

IoT Central associe automatiquement un appareil à un modèle d’appareil lorsque l’appareil se connecte. Un appareil envoie un [ID de modèle](../../iot-fundamentals/iot-glossary.md?toc=/azure/iot-central/toc.json&bc=/azure/iot-central/breadcrumb/toc.json#model-id) lorsqu’il se connecte. IoT Central utilise l’ID de modèle pour identifier le modèle d’appareil correspondant à ce modèle d’appareil spécifique. Le processus de détection fonctionne comme suit :

1. Si le modèle d’appareil est déjà publié dans l’application IoT Central, l’appareil est associé au modèle d’appareil.
1. Si le modèle d’appareil n’est pas encore publié dans l’application IoT Central, cette dernière le recherche dans le [référentiel de modèles public](https://github.com/Azure/iot-plugandplay-models). Si IoT Central trouve le modèle, il l’utilise pour générer un modèle d’appareil de base.
1. Si IoT Central ne trouve pas le modèle dans le référentiel de modèles public, l’appareil est marqué comme **non associé**. Un opérateur peut soit créer un modèle d’appareil pour l’appareil, puis migrer l’appareil non associé vers le nouveau modèle d’appareil, soit [générer automatiquement un modèle d’appareil](howto-set-up-template.md#autogenerate-a-device-template) en fonction des données que l’appareil envoie.

La capture d’écran suivante montre comment afficher l’ID d’un modèle d’appareil dans IoT Central. Dans un modèle d’appareil, sélectionnez un composant, puis **Modifier l’identité** :

:::image type="content" source="media/concepts-get-connected/model-id.png" alt-text="Capture d’écran de l’ID de modèle dans le modèle d’appareil Thermostat.":::

Le modèle [Thermostat](https://github.com/Azure/iot-plugandplay-models/blob/main/dtmi/com/example/thermostat-1.json) apparaît dans le référentiel de modèles public. La définition de l’ID de modèle se présente ainsi :

```json
"@id": "dtmi:com:example:Thermostat;1"
```

Utilisez la charge utile DPS suivante pour associer l’appareil à un modèle d’appareil :

```json
{
  "modelId":"dtmi:com:example:TemperatureController;2"
}
```

Pour plus d’informations sur la charge utile DPS, consultez l’exemple de code utilisé dans le [Tutoriel : Créer et connecter une application cliente à votre application Azure IoT Central](tutorial-connect-device.md).

## <a name="device-status-values"></a>Valeurs d’état de l’appareil

Quand un appareil réel se connecte à votre application IoT Central, son état change comme suit :

1. L’état de l’appareil est d’abord **Inscrit**. Cet état signifie que l’appareil est créé dans IoT Central et a une identité d'appareil. Un appareil est inscrit quand :
    - un nouvel appareil réel est ajouté à la page **Appareils** ;
    - un ensemble d’appareils est ajouté avec **Importer** à la page **Appareils** ;

1. L’état passe à **Approvisionné** lorsque l’appareil connecté à votre application IoT Central avec des informations d’identification valides termine l’étape de provisionnement. À cette étape, l’appareil utilise DPS pour récupérer automatiquement une chaîne de connexion à partir du hub IoT utilisé par votre application IoT Central. L’appareil peut maintenant se connecter à IoT Central et commencer à envoyer des données.

1. Un opérateur peut bloquer un appareil. Quand un appareil est bloqué, il ne peut pas envoyer de données à votre application IoT Central. Les appareils bloqués affichent un état **Bloqué**. Un opérateur doit réinitialiser l’appareil avant que ce dernier puisse recommencer à envoyer des données. Quand un opérateur débloque un appareil, l’état revient à sa valeur précédente, à savoir **Inscrit** ou **Approvisionné**.

1. Si l’état de l’appareil est **En attente d’approbation**, cela signifie que l’option **Approbation automatique** est désactivée. Un opérateur doit approuver explicitement un appareil avant qu’il ne commence à envoyer des données. Les appareils non enregistrés manuellement sur la page **Appareils**, mais connectés avec des informations d'identification valides afficheront l’état **En attente d'approbation**. Les opérateurs peuvent approuver ces appareils sur la page **Appareils** en utilisant le bouton **Approver**.

1. Si l’état de l’appareil est **Non associé**, cela signifie que les appareils se connectant à IoT Central n’ont pas de modèle d’appareil associé. C’est généralement le cas dans les scénarios suivants :

    - Un ensemble d’appareils est ajouté en utilisant **Importer** sur la page **Appareils**, sans spécifier le modèle d’appareil.
    - Un appareil a été inscrit manuellement sur la page **Appareils** sans spécifier le modèle d’appareil. L’appareil est alors connecté avec des informations d’identification valides.  

    Un opérateur peut associer un appareil à un modèle d’appareil sur la page **Appareils** en utilisant le bouton **Migrer**.

## <a name="device-connection-status"></a>État de la connexion d’appareil

Quand un appareil ou un appareil de périphérie se connecte à l’aide du protocole MQTT, les événements _connecté_ et _déconnecté_ pour l’appareil sont générés. Ces événements ne sont pas envoyés par l’appareil, ils sont générés en interne par IoT Central.

Le diagramme suivant montre comment, lorsqu’un appareil se connecte, la connexion est inscrite à la fin d’une fenêtre de temps. Si plusieurs événements de connexion et de déconnexion se produisent, IoT Central inscrit celui qui est le plus proche de la fin de la fenêtre de temps. Par exemple, si un appareil se déconnecte et se reconnecte dans la fenêtre de temps, IoT Central inscrit l’événement de connexion. Actuellement, la fenêtre de temps est d’environ une minute.

:::image type="content" source="media/concepts-get-connected/device-connectivity-diagram.png" alt-text="Diagramme montrant la fenêtre d’événements pour les événements connecté et déconnecté." border="false":::

Regardez la vidéo suivante pour en savoir plus sur la surveillance de l’état de la connexion des appareils :

> [!VIDEO https://www.youtube.com/embed/EUZH_6Ihtto]

Vous pouvez inclure des événements de connexion et de déconnexion dans les [exportations à partir d’IoT Central](howto-export-data.md#set-up-data-export). Pour plus d’informations, consultez [Réagir aux événements IoT Hub > Limitations pour les événements d’état de la connexion et de la déconnexion d’appareils](../../iot-hub/iot-hub-event-grid.md#limitations-for-device-connected-and-device-disconnected-events).

## <a name="sdk-support"></a>Prise en charge des Kits de développement logiciel (SDK)

Le kit de développement logiciel (SDK) Azure Device est le moyen le plus simple pour vous d’implémenter votre code d’appareil. Les kits SDK d’appareils suivants sont disponibles :

- [SDK Azure IoT pour C](https://github.com/azure/azure-iot-sdk-c)
- [SDK Azure IoT pour Python](https://github.com/azure/azure-iot-sdk-python)
- [SDK Azure IoT pour Node.js](https://github.com/azure/azure-iot-sdk-node)
- [SDK Azure IoT pour Java](https://github.com/azure/azure-iot-sdk-java)
- [SDK Azure IoT pour .NET](https://github.com/azure/azure-iot-sdk-csharp)

### <a name="sdk-features-and-iot-hub-connectivity"></a>Fonctionnalités du SDK et connectivité d’IoT Hub

Toutes les communications des appareils avec IoT Hub utilisent les options de connectivité IoT Hub suivantes :

- [Messages d’appareil-à-cloud](../../iot-hub/iot-hub-devguide-messages-d2c.md)
- [Messages de cloud-à-appareil](../../iot-hub/iot-hub-devguide-messages-c2d.md)
- [Représentations d’appareil physique](../../iot-hub/iot-hub-devguide-device-twins.md)

Le tableau suivant récapitule à quelles fonctionnalités des appareils Azure IoT Central correspondent les fonctionnalités IoT Hub :

| Azure IoT Central | Azure IoT Hub |
| ----------- | ------- |
| Télémétrie | Messages d’appareil-à-cloud |
| Commandes hors connexion | Messages de cloud-à-appareil |
| Propriété | Propriétés signalées du jumeau d’appareil |
| Propriété (accessible en écriture) | Propriétés souhaitées et signalées du jumeau d’appareil |
| Commande | Méthodes directes |

### <a name="protocols"></a>Protocoles

Les kits SDK d’appareil prennent en charge les protocoles réseau suivants pour la connexion à un hub IoT :

- MQTT
- AMQP
- HTTPS

Pour plus d’informations sur les différences entre ces protocoles et des conseils sur le choix de l’un d’eux, consultez [Choisir un protocole de communication](../../iot-hub/iot-hub-devguide-protocols.md).

Si votre appareil ne peut utiliser aucun des protocoles pris en charge, utilisez Azure IoT Edge pour procéder à une conversion de protocole. IoT Edge prend en charge d’autres scénarios avancés permettant de décharger l’application Azure IoT Central de certains traitements.

## <a name="security"></a>Sécurité

Toutes les données échangées entre les appareils et votre application Azure IoT Central sont chiffrées. IoT Hub authentifie chaque demande provenant d’un appareil qui se connecte aux points de terminaison IoT Hub exposés aux appareils. Pour éviter d’échanger des informations d’identification sur le réseau, un appareil utilise des jetons signés pour s’authentifier. Pour plus d’informations, consultez [Contrôler l’accès à IoT Hub](../../iot-hub/iot-hub-devguide-security.md).

## <a name="next-steps"></a>Étapes suivantes

Voici quelques suggestions pour continuer :

- Passez en revue les [meilleures pratiques](concepts-best-practices.md) pour le développement d’appareils.
- Consultez un exemple de code montrant comment utiliser les jetons SAS dans [Tutoriel : Créer et connecter une application cliente à votre application Azure IoT Central](tutorial-connect-device.md)
- Découvrez comment [connecter des appareils avec des certificats X.509 à l’aide du kit de développement logiciel (SDK) d’appareil Node.js pour une application IoT Central](how-to-connect-devices-x509.md)
- Découvrez comment [superviser la connectivité des appareils à l’aide d’Azure CLI](./howto-monitor-devices-azure-cli.md)
- En savoir plus sur [les appareils Azure IoT Edge et Azure IoT Central](./concepts-iot-edge.md)
