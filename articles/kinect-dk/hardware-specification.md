---
title: Spécifications matérielles pour Azure Kinect DK
description: Découvrez les composants, les spécifications et les fonctionnalités d’Azure Kinect DK.
author: qm13
ms.author: quentinm
ms.prod: kinect-dk
ms.date: 03/18/2021
ms.topic: article
keywords: azure, kinect, spécifications, matériel, DK, fonctionnalités, profondeur, couleur, RVB, IMU, microphone, réseau, profondeur
ms.custom: CI 114092, CSSTroubleshooting
audience: ITPro
manager: dcscontentpm
ms.localizationpriority: high
ms.openlocfilehash: 45941395864bde4442748671c6b5c33da81b319d
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131082490"
---
# <a name="azure-kinect-dk-hardware-specifications"></a>Spécifications matérielles pour Azure Kinect DK

Cet article fournit des informations sur la façon dont le matériel Azure Kinect intègre la toute dernière technologie de capteur de Microsoft dans un accessoire connecté par USB.

![Azure Kinect DK](./media/resources/hardware-specs-media/device-wire.png)

## <a name="terms"></a>Termes

Les abréviations suivantes seront utilisées tout au long de cet article.

- NFOV (mode de profondeur à champ de vision étroit)
- WFOV (mode de profondeur à champ de vision large)
- FOV (champ de vision)
- FPS (images par seconde)
- IMU (unité de mesure inertielle)
- FoI (champ d’intérêt)

## <a name="product-dimensions-and-weight"></a>Dimensions et poids du produit

Le poids et les dimensions de l’appareil Azure Kinect sont les suivants.

- **Dimensions** : 103 x 39 x 126 mm
- **Poids** : 440 g

![Dimensions Azure Kinect DK](./media/resources/hardware-specs-media/dimensions.png)

Un fichier STEP pour l’appareil Azure Kinect est disponible [ici](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/blob/develop/assets). 

## <a name="operating-environment"></a>Environnement d’exploitation

Azure Kinect DK est destiné aux développeurs et aux entreprises commerciales disposant des conditions ambiantes suivantes :

- **Température** : 10-25 <sup>°</sup>C
- **Humidité** : 8 à 90 % d’humidité relative (sans condensation)

> [!NOTE]
> L’utilisation de cet appareil dans des conditions ambiantes autres que celles-ci peut provoquer la panne de l’appareil ou un fonctionnement anormal. Ces conditions ambiantes sont applicables à l’environnement immédiat de l’appareil, quelles que soient les conditions opérationnelles. Lorsque l’appareil est utilisé avec un boîtier externe, il est recommandé d’utiliser une solution de contrôle actif de la température et/ou d’autres solutions de refroidissement afin de garantir le maintien de la température dans les plages indiquées. L’appareil comprend un canal de refroidissement situé entre la section avant et la section arrière. Lorsque vous utilisez l’appareil, vérifiez que le canal de refroidissement n’est pas obstrué.

Reportez-vous aux [informations de sécurité](https://support.microsoft.com/help/4023454/safety-information) supplémentaires sur le produit.

## <a name="depth-camera-supported-operating-modes"></a>Modes de fonctionnement pris en charge pour la caméra à profondeur de champ

Azure Kinect DK comprend une caméra à profondeur de champ ToF 1 Mpx conçue par Microsoft qui utilise le [capteur d’images présenté lors de la conférence ISSCC 2018](/windows/mixed-reality/ISSCC-2018). La caméra à profondeur de champ prend en charge les modes indiqués ci-dessous :

 | Mode            | Résolution | FoI       | i/s                | Plage de fonctionnement* | Temps d’exposition |
|-----------------|------------|-----------|--------------------|------------------|---------------|
| NFOV sans compartimentation   | 640 x 576    | 75°x°65   | 0, 5, 15, 30       | 0,5 - 3,86 m       | 12,8 ms        |
| NFOV 2x2 avec compartimentation (SW) | 320 x 288    | 75°x°65   | 0, 5, 15, 30       | 0,5 - 5,46 m       | 12,8 ms        |
| WFOV 2x2 avec compartimentation | 512 x 512    | 120°x°120 | 0, 5, 15, 30       | 0,25 - 2,88 m      | 12,8 ms        |
| WFOV sans compartimentation   | 1024 x 1024  | 120°x°120 | 0, 5, 15           | 0,25 - 2,21 m      | 20,3 ms        |
| Infrarouge passif      | 1024 x 1024  | N/A       | 0, 5, 15, 30       | N/A              | 1,6 ms         |

\*Réflectivité de 15 à 95 % à 850 nm, 2,2 μW/cm<sup>2</sup>/nm, erreur aléatoire écart type ≤ 17 mm, erreur systématique typique < 11 mm + 0,1 % de la distance sans interférence multichemin. Une profondeur peut être fournie en dehors de la plage de fonctionnement indiquée ci-dessus. Elle dépend de la réflectivité d’un objet.

## <a name="color-camera-supported-operating-modes"></a>Modes de fonctionnement pris en charge pour la caméra couleur

Azure Kinect DK comprend un capteur CMOS OV12A10 12 Mpx avec obturateur roulant. Les modes de fonctionnement natifs sont listé ci-dessous :

|             Résolution de la caméra RVB (H x V)  |          Proportions  |          Options de format   |          Fréquence d’images (FPS)  |          FOV nominal (H x V) (après traitement)  |
|------------------------------------------|------------------------|---------------------------|-----------------------------|---------------------------------------------|
|       3 840 x 2 160                          |          16:9          |          MJPEG            |          0, 5, 15, 30       |          90°x°59                              |
|       2 560 x 1 440                          |          16:9          |          MJPEG            |          0, 5, 15, 30       |          90°x°59                              |
|       1 920 x 1 080                          |          16:9          |          MJPEG            |          0, 5, 15, 30       |          90°x°59                              |
|       1 280 x 720                           |          16:9          |          MJPEG/YUY2/NV12  |          0, 5, 15, 30       |          90°x°59                              |
|       4 096 x 3 072                          |          4:3           |          MJPEG             |          0, 5, 15           |          90°x°74,3                            |
|       2 048 x 1 536                          |          4:3           |          MJPEG             |          0, 5, 15, 30       |          90°x°74,3                            |

La caméra RVB est compatible avec les classes vidéo USB et peut être utilisée sans le SDK du capteur. Espace colorimétrique de la caméra RVB : plage complète BT.601 [0..255]. Le [sous-échantillonnage de la chrominance](https://en.wikipedia.org/wiki/Chroma_subsampling) MJPEG est 4:2:2.

> [!NOTE]
> Le SDK du capteur peut fournir des images en couleurs au format de pixel BVRA. Ce mode n’est pas pris en charge par l’appareil et provoque une charge processeur supplémentaire lorsqu’il est utilisé. Le processeur hôte est utilisé pour convertir les images MJPEG reçues de l’appareil.

## <a name="rgb-camera-exposure-time-values"></a>Valeurs de temps d’exposition de la caméra RVB

Vous trouverez ci-dessous les valeurs d’exposition manuelle qui sont acceptées pour la caméra RVB :

| exp| 2^exp | 50 Hz   |60 Hz    |
|----|-------|--------|--------|
| -11|     488|    500|    500 |
| -10|     977|   1250|   1250 |
|  -9|    1953|   2 500|   2 500 |
|  -8|    3906|  10000|   8330 |
|  -7|    7813|  20000|  16670 |
|  -6|   15625|  30000|  33330 |
|  -5|   31250|  40000|  41670 |
|  -4|   62500|  50000|  50000 |
|  -3|  125000|  60000|  66670 |
|  -2|  250 000|  80000|  83330 |
|  -1|  500000| 100000| 100000 |
|   0| 1000000| 120 000| 116670 |
|   1| 2000000| 130000| 133330 |

## <a name="depth-sensor-raw-timing"></a>Minutage brut du capteur de profondeur

Mode de profondeur | IR <br>Impulsions | d’impulsion <br>Largeur  | Idle <br>Points| Durée d’inactivité | Exposition : <br> Temps
-|-|-|-|-|-
NFOV sans compartimentation <br>  NFOV 2xx avec compartimentation <br> WFOV 2x2 avec compartimentation | 9 | 125 us | 8 | 1 450 us | 12,8 ms 
WFOV sans compartimentation                                            | 9 | 125 us | 8 | 2 390 us | 20,3 ms


## <a name="camera-field-of-view"></a>Champ de vision de la caméra

L’image suivante montre la profondeur et le champ de vision de la caméra RVB, ou les angles que les capteurs « voient ». Ce diagramme montre la caméra RVB en mode 4:3.

![Champ de vision de la caméra](./media/resources/hardware-specs-media/camera-fov.png)

Cette image montre le champ de vision de la caméra, vu de devant à une distance de 2 000 mm.

![Champ de vision de la caméra - À l’avant](./media/resources/hardware-specs-media/fov-front.png)

> [!NOTE]
> Lorsque la profondeur est en mode NFOV, la caméra RVB dispose d’un meilleur chevauchement des pixels avec une résolution 4:3 qu’avec une résolution 16:9.

## <a name="motion-sensor-imu"></a>Capteur de mouvement (IMU)

L’unité de mesure inertielle (IMU) incorporée est un LSM6DSMUS qui comprend un accéléromètre et un gyroscope. L’accéléromètre et le gyroscope sont échantillonnés simultanément à 1,6 kHz. Les échantillons sont signalés à l’hôte à 208 Hz.

## <a name="microphone-array"></a>Réseau de microphones

Azure Kinect DK comprend un réseau circulaire de 7 microphones haute qualité qui correspond à un appareil de classe audio USB 2.0 standard. Vous pouvez accéder à l’ensemble des 7 canaux. Les spécifications de performances sont les suivantes :

- Sensibilité : -22 dBFS (94 dB SPL, 1 kHz)
- Rapport signal/bruit > 65 dB
- Point de surcharge acoustique : 116 dB

![Bulle du microphone](./media/resources/hardware-specs-media/mic-bubble.png)

## <a name="usb"></a>USB

Azure Kinect DK est un appareil composite USB3 qui expose les points de terminaison matériels suivants au système d’exploitation :

L’ID de fournisseur est 0x045E (Microsoft). La table Product ID est montrée ci-dessous :

|    Interface USB        |    IP PNP    |     Notes            |
|-------------------------|--------------|----------------------|
|    Hub USB 3.1 Gen2    |    0x097A    |    Hub principal    |
|    Hub USB 2.0         |    0x097B    |    HS USB          |
|    Caméra à profondeur de champ       |    0x097C    |    USB 3.0            |
|    Caméra couleur       |    0x097D    |    USB 3.0            |
|    Microphones        |    0x097E    |    HS USB          |

## <a name="indicators"></a>Indicateurs

L’appareil possède un voyant pour la diffusion de données de la caméra, qui est situé à l’avant et qui peut être désactivé par programmation à l’aide du SDK du capteur.

Le voyant situé à l’arrière de l’appareil indique l’état de celui-ci :

| Si le voyant est     | Signification                                                   |
|-----------------------|------------------------------------------------------------|
| Blanc et fixe           | L’appareil est allumé et fonctionne correctement.                         |
| Blanc clignotant        | L’appareil est allumé mais ne dispose pas d’une connexion de données USB 3.0.   |
| Orange clignotant        | L’appareil n’a pas suffisamment de puissance pour fonctionner.               |
| Orange, puis blanc clignotant  | Mise à jour du microprogramme ou récupération en cours                    |

## <a name="power-device"></a>Bloc d’alimentation

L’appareil peut être alimenté de deux manières :

1. À l’aide du dispositif d’alimentation fourni avec l’appareil. Le connecteur d’alimentation est un OD 4,5 mm avec un ID 3,0 mm et un diamètre de broche de 0,6 mm.
2. En utilisant un câble Type C vers Type C à la fois pour l’alimentation et pour les données.

L’appareil Azure Kinect DK n’est pas fourni avec un câble Type C vers Type C.

> [!NOTE]
> - Le câble d’alimentation fourni est un câble USB Type A vers connecteur cylindrique simple. Utilisez le dispositif d’alimentation murale fourni avec ce câble. L’appareil est capable de prendre plus d’énergie que deux ports USB Type A standard ne peuvent fournir.
> - Les câbles USB sont très importants. Nous vous recommandons donc d’utiliser des câbles de haute qualité et de vérifier qu’ils fonctionnent correctement avant de déployer l’unité à distance.

> [!TIP]
> Pour sélectionner un bon câble Type C vers Type C :
> - Le [câble certifié USB](https://www.usb.org/products) doit prendre en charge à la fois l’alimentation et les données.
> - Un câble passif doit mesurer moins de 1,5 mètre. S’il est plus long, utilisez plutôt un câble actif. 
> - Le câble doit prendre en charge au moins 1,5 A. Dans le cas contraire, vous devrez connecter une alimentation externe.

Vérifiez le câble :

- Connectez l’appareil via le câble au PC hôte.
- Vérifiez que tous les appareils sont énumérés correctement dans le gestionnaire de périphériques Windows. La caméra à profondeur de champ et la caméra RVB doivent s’afficher comme dans l’exemple ci-dessous.

  ![Azure Kinect DK dans le Gestionnaire de périphériques](./media/resources/hardware-specs-media/device-manager.png)

- Dans la visionneuse Azure Kinect, vérifiez que le câble peut diffuser des données de façon fiable pour tous les capteurs, avec les paramètres suivants :

  - Caméra à champ de profondeur : NFOV unbinned
  - Caméra RVB : 2160p
  - Microphones et IMU activés

## <a name="what-does-the-light-mean"></a>Que signifie ce voyant ?

Le voyant d’alimentation est situé à l’arrière de l’appareil Azure Kinect DK. La couleur du voyant change en fonction de l’état de l’appareil.

![L’image montre l’arrière de l’appareil Azure Kinect DK. Il y a trois légendes numérotées : une pour le voyant, et au-dessous, deux pour les câbles.](./media/quickstarts/azure-kinect-dk-power-indicator.png)

Ce schéma montre les composants suivants :

1. Le voyant d’alimentation
1. Le câble d’alimentation (connecté à une source d’alimentation)
1. Le câble de données USB Type C (connecté au PC)

Vérifiez que les câbles sont connectés comme indiqué. Consultez ensuite le tableau suivant pour connaître les différents états du voyant d’alimentation.

|Si le voyant est : |Signification : |Vous devez : |
| ---| --- | --- |
|Blanc et fixe |L’appareil est sous tension et fonctionne correctement. |Utiliser l’appareil. |
|Éteint |L’appareil n’est pas connecté au PC. |Vérifier que le câble du connecteur d’alimentation rond est connecté à l’appareil et à l’adaptateur secteur USB.<br /><br />Vérifier que le câble USB Type C est connecté à l’appareil et à votre PC. |
|Blanc clignotant |L’appareil est allumé mais ne dispose pas d’une connexion de données USB 3.0. |Vérifier que le câble du connecteur d’alimentation rond est connecté à l’appareil et à l’adaptateur secteur USB.<br /><br />Vérifier que le câble USB Type C est connecté à l’appareil et à un port USB 3.0 du PC.<br /><br />Connecter l’appareil sur un autre port USB 3.0 du PC.<br /><br />Sur l’ordinateur, ouvrir le Gestionnaire de périphériques (**Démarrer** > **Panneau de configuration** > **Gestionnaire de périphériques**) et vérifier que l’ordinateur dispose d’un contrôleur hôte USB 3.0 pris en charge. |
|Orange clignotant |L’appareil n’a pas suffisamment de puissance pour fonctionner. |Vérifier que le câble du connecteur d’alimentation rond est connecté à l’appareil et à l’adaptateur secteur USB.<br /><br />Vérifier que le câble USB Type C est connecté à l’appareil et à votre PC. |
|Orange, puis blanc clignotant |L’appareil est sous tension et reçoit une mise à jour du microprogramme ou restaure les paramètres d’usine. |Attendre que le voyant d’alimentation soit blanc et fixe. Pour plus d’informations, consultez [Réinitialiser Azure Kinect DK](reset-azure-kinect-dk.md). |

## <a name="power-consumption"></a>Consommation énergétique

Azure Kinect DK peut consommer jusqu’à 5,9 W. Toutefois, la consommation dépend du cas d’utilisation.

## <a name="calibration"></a>Étalonnage

Azure Kinect DK est étalonné en usine. Les paramètres d’étalonnage des capteurs visuels et inertiels peuvent être interrogés par programmation par le biais du SDK du capteur.

## <a name="device-recovery"></a>Récupération de l’appareil

Le microprogramme de l’appareil peut être réinitialisé vers le microprogramme d’origine à l’aide du bouton situé sous la vis.

![Bouton de récupération Azure Kinect DK](./media/resources/hardware-specs-media/recovery.png)

Pour effectuer une récupération de l’appareil, consultez [ces instructions](reset-azure-kinect-dk.md).

## <a name="next-steps"></a>Étapes suivantes

- [Utiliser le SDK du capteur Azure Kinect](about-sensor-sdk.md)
- [Configurer le matériel](set-up-azure-kinect-dk.md)
