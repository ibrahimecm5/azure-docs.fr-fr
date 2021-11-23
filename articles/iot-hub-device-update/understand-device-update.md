---
title: Présentation de Device Update pour Azure IoT Hub | Microsoft Docs
description: Device Update pour IoT Hub est un service qui vous permet de déployer des mises à jour OTA (over-the-air) pour vos appareils IoT.
author: vimeht
ms.author: vimeht
ms.date: 2/11/2021
ms.topic: overview
ms.service: iot-hub-device-update
ms.openlocfilehash: 0b763f9f6c83f6255a460fc4441f8cfdb74193f0
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132399368"
---
# <a name="device-update-for-iot-hub-preview-overview"></a>Présentation de Device Update pour IoT Hub (préversion)

Device Update pour IoT Hub est un service qui vous permet de déployer des mises à jour OTA (over-the-air) pour vos appareils IoT.

Dans le souci d’améliorer la productivité et l’efficacité opérationnelle, les organisations adoptent de plus en plus rapidement des solutions IoT (Internet des objets). Il est donc essentiel que les appareils qui constituent ces solutions s’appuient sur des bases fiables et sécurisées et qu’ils soient faciles à connecter et à gérer à grande échelle. Device Update pour IoT Hub est une plateforme de bout en bout que les clients peuvent utiliser pour publier, distribuer et gérer des mises à jour OTA portant sur un large choix d’appareils, qu’il s’agisse de capteurs minuscules ou d’appareils de passerelle. 

Pour tirer pleinement parti de la transformation numérique IoT, les clients ont besoin de cette capacité à utiliser, gérer et mettre à jour des appareils à grande échelle. Découvrez les avantages de l’implémentation de Device Update pour IoT Hub, notamment la possibilité de répondre rapidement aux menaces de sécurité et de déployer de nouvelles fonctionnalités pour atteindre des objectifs métier sans impliquer de coûts de développement et de maintenance supplémentaires liés à la création de vos propres plateformes de mise à jour.

## <a name="support-for-a-wide-range-of-iot-devices"></a>Prise en charge d’un large choix d’appareils IoT


Device Update pour IoT Hub est conçu pour offrir un déploiement de mises à jour optimisé et des opérations rationalisées grâce à son intégration avec [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/). Cette intégration facilite l’adoption de Device Update sur une solution existante. Elle fournit une solution hébergée dans le cloud pour se connecter à pratiquement tout appareil. Device Update prend en charge une large gamme de systèmes d’exploitation IoT, notamment Linux et [Azure RTOS](https://azure.microsoft.com/services/rtos/) (système d’exploitation en temps réel). Celle-ci peut s’étendre par le biais de l’open source. Nous développons une mise à jour des appareils pour les offres IoT Hub avec nos partenaires de semi-conducteurs, dont STMicroelectronics, NXP, Renesas et Microchip. Consultez les [exemples](https://github.com/azure-rtos/samples/tree/PublicPreview/ADU) de panneaux d’évaluation des semiconducteurs clés qui incluent des guides de prise en main pour apprendre à configurer, générer et déployer des mises à jour OTA sur les appareils de classe MCU. 

Un fichier binaire de simulateur d’agent Device Update et des images Yocto de référence Raspberry Pi sont fournis.
Device Update pour IoT Hub prend également en charge la mise à jour des appareils Azure IoT Edge. Un agent Device Update est fourni pour la plateforme Ubuntu Server 18.04 amd64. Device Update pour IoT Hub fournit également du code open source si vous n’exécutez pas l’une des plateformes ci-dessus. Vous pouvez porter l’agent vers la distribution que vous exécutez.

Device Update fonctionne avec IoT Plug-and-Play et peut gérer tous les appareils qui prennent en charge les interfaces IoT Plug-and-Play nécessaires. Pour plus d’informations, consultez [Device Update pour IoT Hub et IoT Plug-and-Play](device-update-plug-and-play.md).

## <a name="support-for-a-wide-range-of-update-artifacts"></a>Prise en charge d’un large éventail d’artefacts de mise à jour

Device Update pour IoT Hub prend en charge deux formes de mises à jour : l’une basée sur une image et l’autre sur un package.

Les mises à jour basées sur un package sont des mises à jour ciblées qui modifient uniquement un composant ou une application spécifique sur l’appareil. Cela réduit la consommation de bande passante et permet de réduire le temps de téléchargement et d’installation des mises à jour. Les mises à jour de package permettent généralement de réduire les temps d’arrêt des appareils lors de l’application d’une mise à jour et d’éviter une surcharge liée à la création d’images.

Les mises à jour basées sur une image offrent un niveau de confiance plus élevé dans l’état final de l’appareil. Il est généralement plus facile de répliquer les résultats d’une mise à jour basée sur une image entre un environnement de préproduction et un environnement de production, car cela ne présente pas les mêmes défis que les packages et leurs dépendances.
En raison de leur nature atomique, il est également possible d’adopter aisément un modèle de basculement A/B.

Il n’y a pas de réponse unique. Vous pouvez faire un choix différent selon vos cas d’usage particuliers. Device Update pour IoT Hub prend en charge les mises à jour sous forme à la fois d’image et de package, ce qui vous permet de choisir le modèle de mise à jour qui convient le mieux à l’environnement de votre appareil.

## <a name="flexible-features-for-updating-devices"></a>Fonctionnalités flexibles pour la mise à jour des appareils

Les fonctionnalités de Device Update pour IoT Hub offrent une expérience puissante et flexible :

* Expérience utilisateur de gestion des mises à jour intégrée à Azure IoT Hub
* Lancement progressif des mises à jour par le biais de groupes d’appareils et de contrôles de planification des mises à jour
* API programmatiques pour activer des expériences d’automatisation et de personnalisation de portail
* Vues en un clin d’œil de la conformité et de l’état des mises à jour sur des flottes d’appareils hétérogènes
* Prise en charge des mises à jour d’appareil résilientes (A/B) pour assurer une restauration continue
* Contrôles d’accès basés sur les rôles et les abonnements disponibles par le biais du portail Azure.com
* Cache de contenu local et prise en charge de la périphérie imbriquée pour permettre la mise à jour des appareils déconnectés du cloud
* Outils de gestion des mises à jour et de création de rapports détaillés 

Avec les contrôles de gestion et de déploiement de Device Update pour IoT Hub, les utilisateurs peuvent maximiser la productivité et gagner du temps précieux. Device Update pour IoT Hub inclut la capacité à regrouper des appareils et à spécifier ceux sur lesquels une mise à jour doit être déployée. Les utilisateurs peuvent également voir l’état des déploiements de mises à jour pour vérifier que chaque appareil applique correctement ces mises à jour.

En cas d’échec, Device Update pour IoT Hub permet également aux utilisateurs d’identifier les appareils qui n’ont pas réussi à appliquer la mise à jour et d’obtenir d’autres détails sur l’échec. La capacité à identifier les appareils qui n’ont pas pu être mis à jour fait économiser de nombreuses heures de recherche manuelle.

### <a name="best-in-class-security-at-global-scale"></a>Sécurité de pointe à l’échelle mondiale

Microsoft Azure prend en charge plus d’un milliard d’appareils IoT à travers le monde, un nombre qui augmente rapidement de jour en jour. Device Update pour IoT Hub s’appuie sur cette expérience et sur la fiabilité reconnue de la plateforme Windows Update. Ainsi, les appareils peuvent être mis à jour en continu à l’échelle mondiale.

Device Update pour IoT Hub utilise une sécurité complète entre le cloud et la périphérie qui est développée pour Microsoft Azure, de sorte que les clients n’ont pas besoin de consacrer du temps à la créer eux-mêmes de toutes pièces.


## <a name="device-update-workflows"></a>Workflows de Device Update

La fonctionnalité de Device Update comprend trois domaines : intégration de l’agent, importation et gestion.

### <a name="device-update-agent"></a>Agent Device Update

Quand une commande de mise à jour est reçue sur un appareil, elle exécute la phase demandée de la mise à jour (téléchargement, installation et application). Au cours de chaque phase, l’état est renvoyé à Device Update par le biais d’IoT Hub pour que vous puissiez voir l’état actuel d’un déploiement. Si aucune mise à jour n’est en cours, l’état retourné est « inactif ». Un déploiement peut être annulé à tout moment.

:::image type="content" source="media/understand-device-update/client-agent-workflow.png" alt-text="Diagramme du workflow de l’agent Device Update." lightbox="media/understand-device-update/client-agent-workflow.png":::

[Découvrez-en plus](device-update-agent-overview.md) sur l’agent Device Update. 

### <a name="importing"></a>Importation en cours

L’importation est la manière dont vos mises à jour sont ingérées dans la mise à jour de l’appareil afin qu’elles puissent être déployées sur les appareils. Device Update prend en charge le déploiement d’une seule mise à jour par appareil. Cela s’avère idéal pour les mises à jour d’image complète qui s’appliquent à une partition de système d’exploitation entière en une seule fois, ou à un [manifeste APT](device-update-apt-manifest.md) qui décrit tous les packages à mettre à jour sur votre appareil à partir d’un dépôt désigné. Pour importer des mises à jour dans Device Update, commencez par créer un manifeste d’importation qui décrit la mise à jour, puis chargez le ou les fichiers de mise à jour et le manifeste d’importation sur un emplacement accessible sur Internet. Ensuite, vous pouvez utiliser le portail Azure ou l’[API REST Device Update](/rest/api/deviceupdate/) pour lancer le processus asynchrone d’importation des mises à jour. Device Update permet de charger les fichiers, de les traiter et de les rendre disponibles pour une distribution vers des appareils IoT.

Si le contenu est sensible, protégez le téléchargement à l’aide d’une signature d’accès partagé (SAS), comme une SAS ad hoc pour Stockage Blob Azure. [En savoir plus sur SAS](../storage/common/storage-sas-overview.md)

:::image type="content" source="media/understand-device-update/import-update.png" alt-text="Diagramme du workflow d’importation de Device Update pour IoT Hub." lightbox="media/understand-device-update/import-update.png":::

[Découvrez-en plus](import-concepts.md) sur l’importation de mises à jour. 

### <a name="grouping-and-deployment"></a>Regroupement et déploiement

Après avoir importé une mise à jour, vous pouvez voir les mises à jour compatibles avec vos appareils et classes d’appareils.

Device Update prend en charge le concept de **groupes** par le biais d’étiquettes dans IoT Hub. Le déploiement d’une mise à jour sur un groupe de test est un bon moyen de réduire le risque de rencontrer des problèmes pendant un déploiement en production.

Dans Device Update, les déploiements constituent un moyen de connecter le contenu approprié à un ensemble spécifique d’appareils compatibles. Device Update orchestre le processus d’envoi des commandes à chaque appareil, en demandant à chacun d’eux de télécharger et d’installer les mises à jour, puis d’envoyer leur état en retour.

:::image type="content" source="media/understand-device-update/manage-deploy-updates.png" alt-text="Diagramme du workflow de regroupement et de déploiement de Device Update pour IoT Hub." lightbox="media/understand-device-update/manage-deploy-updates.png":::

[En savoir plus](device-update-compliance.md) sur les concepts de déploiement

[En savoir plus](device-update-groups.md) sur les groupes de mises à jour des appareils


## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Créer un compte et une instance Device Update](create-device-update-account.md)
