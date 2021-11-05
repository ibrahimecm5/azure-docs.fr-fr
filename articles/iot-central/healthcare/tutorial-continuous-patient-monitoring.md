---
title: Tutoriel – Suivi continu des patients Azure IoT | Microsoft Docs
description: Ce tutoriel vous montre comment déployer et utiliser le modèle d’application de suivi continu des patients pour IoT Central.
author: philmea
ms.author: philmea
ms.date: 08/02/2021
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: eliotgra
ms.openlocfilehash: 4d5b00526219b0a4ade24b7522ea4826236fd70e
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131057758"
---
# <a name="tutorial-deploy-and-walkthrough-the-continuous-patient-monitoring-app-template"></a>Tutoriel : Déployer et découvrir pas à pas un modèle d’application de suivi continu des patients

:::image type="content" source="media/cpm-architecture.png" alt-text="Architecture du suivi continu des patients":::

## <a name="bluetooth-low-energy-ble-medical-devices"></a>Appareils médicaux Bluetooth basse consommation (BLE, Bluetooth Low Energy)

De nombreux appareils médicaux de type wearable utilisés dans les solutions médicales IoT sont des appareils BLE. Ces appareils ne peuvent pas communiquer directement avec le cloud et doivent utiliser une passerelle pour échanger des données avec votre solution cloud. Cette architecture utilise une application de téléphone mobile comme passerelle.

## <a name="mobile-phone-gateway"></a>Passerelle de téléphone portable

La fonction principale de l’application de téléphone mobile est de collecter les données BLE des appareils médicaux et de les communiquer à IoT Central. L’application guide également les patients dans la configuration de l’appareil et leur permet de visualiser leurs données médicales personnelles. D’autres solutions pourraient utiliser une passerelle de tablette ou une passerelle statique dans une chambre d’hôpital. Un exemple d’application mobile open source est disponible pour Android et iOS, que vous pouvez utiliser comme point de départ pour le développement de votre application. Pour plus d’informations, consultez l’[exemple d’application mobile d’analyse continue des patients sur GitHub](https://github.com/iot-for-all/iotc-cpm-sample).

## <a name="export-to-azure-api-for-fhirreg"></a>Exporter vers l’API Azure pour FHIR&reg;

Azure IoT Central est conforme à HIPAA et certifié HITRUST&reg;. Vous pouvez également envoyer les données médicales des patients à d’autres services en utilisant l’[API Azure pour FHIR](../../healthcare-apis/fhir/overview.md). L’API Azure pour FHIR est une API basée sur des standards pour les données médicales cliniques. Le [connecteur Azure IoT pour FHIR](../../healthcare-apis/fhir/iot-fhir-portal-quickstart.md) vous permet d’utiliser l’API Azure pour FHIR comme destination d’exportation des données en continu depuis IoT Central.

## <a name="machine-learning"></a>Apprentissage automatique

Utilisez des modèles Machine Learning avec vos données FHIR pour générer des insights et venir en appui des décisions prises par votre équipe soignante. Pour plus d’informations, consultez la [documentation Machine Learning](../../machine-learning/index.yml).

## <a name="provider-dashboard"></a>Tableau de bord de fournisseur

Utilisez les données de la l’API Azure pour FHIR pour créer un tableau de bord des insights sur les patients ou intégrez-les directement dans un dossier médical électronique utilisé par les équipes soignantes. Les équipes soignantes peuvent utiliser le tableau de bord pour assister les patients et identifier les signes précoces avertissant d’une détérioration. Pour plus d’informations, consultez le tutoriel [Créer un tableau de bord de fournisseur Power BI](tutorial-health-data-triage.md).

Dans ce tutoriel, vous allez apprendre à :

- Créer un modèle d’application
- Parcourir pas à pas le modèle d’application

## <a name="prerequisites"></a>Prérequis

- Le déploiement de cette application ne nécessite aucun prérequis.
- Vous pouvez utiliser le plan tarifaire gratuit ou utiliser un abonnement Azure.

## <a name="create-continuous-patient-monitoring-application"></a>Créer une application de suivi continu des patients

1. Accédez au site de [création d’applications Azure IoT Central](https://aka.ms/iotcentral). Ensuite, connectez-vous avec un compte Microsoft personnel, scolaire ou professionnel. Sélectionnez **Générer** dans la barre de navigation de gauche, puis sélectionnez l’onglet **Santé** : :::image type="content" source="media/app-manager-health.png" alt-text="Modèle d’application":::

1. Sélectionnez **Créer une application de suivi continu des patients** sous **Suivi continu des patients**.

Pour plus d’informations, consultez [Créer une application IoT Central](../core/howto-create-iot-central-application.md).

## <a name="walk-through-the-application"></a>examiner l’application

Les sections suivantes passent en revue les principales fonctionnalités de l’application :

### <a name="dashboards"></a>Tableaux de bord

Après avoir déployé le modèle d’application, vous accédez dans un premier temps au **tableau de bord de surveillance des patients Lamna**. Lamna Healthcare est un ensemble hospitalier fictif qui se compose de deux hôpitaux : Woodgrove Hospital et Burkville Hospital. Dans le tableau de bord de l’opérateur du Woodgrove Hospital, vous pouvez :

* Consulter les données de télémétrie et les propriétés des appareils, comme le **niveau de charge de la batterie** ou le statut de **connectivité**.

* Affichez le **plan d’étage** et l’emplacement de l’appareil Smart Vitals Patch.

* **Reprovisionner** le Smart Vitals Patch pour un nouveau patient.

* Voir un exemple de **tableau de bord de fournisseur** dont pourrait se servir une équipe de soins hospitaliers pour assurer le suivi de leurs patients.

* Changer le **statut de surveillance** de votre appareil pour indiquer s’il est utilisé à l’hôpital ou dans un scénario à distance.

:::image type="content" source="media/lamna-in-patient.png" alt-text="État du patient hospitalisé":::

Vous pouvez aussi sélectionner **Go to remote patient dashboard** (Accéder au tableau de bord de surveillance à distance des patients) pour voir le tableau de bord de l’opérateur du Burkville Hospital. Ce tableau de bord contient un ensemble similaire d’actions, de données de télémétrie et d’informations. Vous pouvez également voir plusieurs appareils en cours d’utilisation et choisir de **mettre à jour le microprogramme** sur chacun.

:::image type="content" source="media/lamna-remote.png" alt-text="Tableau de bord de l’opérateur distant":::

### <a name="device-templates"></a>Modèles d’appareil

Si vous sélectionnez **Modèles d’appareil**, vous voyez les deux types d’appareils dans le modèle :

- **Smart Vitals Patch** : cet appareil représente un patch qui mesure différents signes vitaux. Il sert au suivi des patients à l’intérieur et à l’extérieur de l’hôpital. Si vous sélectionnez le modèle, vous constatez que le patch envoie à la fois des données sur l’appareil comme le niveau de charge de la batterie et la température de l’appareil, et des données médicales des patients comme la fréquence respiratoire et la pression artérielle.

- **Smart Knee Brace** : cet appareil représente une attelle de genou que les patients utilisent à la suite d’une opération de reconstruction du genou. Si vous sélectionnez ce modèle, vous voyez des fonctionnalités comme des données sur l’appareil, l’amplitude de mouvement et la mobilité.

:::image type="content" source="media/smart-vitals-device-template.png" alt-text="Modèle de patch intelligent":::

### <a name="device-groups"></a>Groupes d'appareils

Les groupes d’appareils vous permettent de regrouper logiquement un ensemble d’appareils, puis d’effectuer des requêtes ou des opérations dessus.

Si vous sélectionnez l’onglet Groupes d’appareils, vous voyez un groupe d’appareils par défaut pour chaque modèle d’appareil dans l’application. Deux exemples de groupes d’appareils supplémentaires ont aussi été créés, appelés **Provision devices** (Appareils de provisionnement) et **Devices with outdated firmware** (Appareils avec microprogramme obsolète). Vous pouvez utiliser ces exemples de groupes d’appareils comme entrées pour exécuter certains des [travaux](#jobs) dans l’application.

### <a name="rules"></a>Règles

Si vous sélectionnez **Règles**, vous voyez les trois règles dans le modèle :

- **Brace temperature high** (Température élevée de l’attelle) : cette règle se déclenche quand la température de l’attelle de genou Smart Knee Brace est supérieure à 95 &deg;F (35 °C) sur une période de 5 minutes. Utilisez cette règle pour alerter le patient et l’équipe de soins afin qu’elle refroidisse l’appareil à distance.

- **Fall detected** (Chute détectée) : cette règle se déclenche si une chute de patient est détectée. Utilisez cette règle pour configurer une action d’intervention d’équipe opérationnelle afin de secourir le patient qui a chuté.

- **Patch battery low** (Faible niveau de charge du patch) : cette règle se déclenche quand le niveau de charge de la batterie de l’appareil est inférieur à 10 %. Utilisez cette règle pour déclencher une notification à l’intention du patient afin qu’il charge son appareil.

:::image type="content" source="media/brace-temp-rule.png" alt-text="Règles":::

### <a name="jobs"></a>travaux

Les travaux vous permettent d’exécuter des opérations en bloc sur un ensemble d’appareils, en utilisant des [groupes d’appareils](#device-groups) comme entrée. Le modèle d’application comporte deux exemples de travaux qu’un opérateur peut exécuter :

* **Mettre à jour le microprogramme des genouillères** : Ce travail recherche les appareils dans le groupe d’appareils **Devices with outdated firmware** et exécute une commande pour mettre à jour ces appareils vers la dernière version du microprogramme. Cet exemple de travail suppose que les appareils peuvent gérer une commande **update** et récupérer les fichiers de microprogramme à partir du cloud.  

* **Reprovisionner des appareils** : Vous disposez d’un ensemble d’appareils qui ont été récemment retournés à l’hôpital. Ce travail recherche les appareils dans le groupe d’appareils **Provision devices** et exécute une commande afin de les reprovisionner pour l’ensemble suivant de patients.

### <a name="devices"></a>Périphériques

Sélectionnez l’onglet **Appareils**, puis une instance de **Smart Knee Brace**. Il existe trois vues qui permettent d’explorer les informations sur l’appareil que vous avez sélectionné. Ces vues sont créées et publiées au moment de générer le modèle de votre appareil. Par conséquent, elles sont identiques sur tous les appareils que vous connectez ou simulez.

La vue **Tableau de bord** offre une vue d’ensemble des données de télémétrie et des propriétés de l’appareil destinées aux opérateurs.

L’onglet **Propriétés** vous permet de modifier les propriétés du cloud et de lire/d’écrire les propriétés de l’appareil.

L’onglet **Commandes** vous permet d’exécuter des commandes sur l’appareil.

:::image type="content" source="media/knee-brace-dashboard.png" alt-text="Tableau de bord de l’attelle de genou":::


## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous n’envisagez pas de continuer à utiliser cette application, supprimez-la en accédant à **Administration > Application settings** (Administration > Paramètre d’application), puis cliquez sur **Delete** (Supprimer).

:::image type="content" source="media/admin-delete.png" alt-text="Nettoyer les ressources":::

## <a name="next-steps"></a>Étapes suivantes

Passez à l’article suivant pour apprendre à créer un tableau de bord de fournisseur qui se connecte à votre application IoT Central.

> [!div class="nextstepaction"]
> [Créer un tableau de bord de fournisseur](tutorial-health-data-triage.md)