---
title: Identifier les appareils nécessaires
description: En savoir plus sur les appliances matérielles et virtuelles pour les capteurs certifiés Defender pour IoT et la console de gestion locale.
ms.date: 11/09/2021
ms.topic: how-to
ms.openlocfilehash: 1b0f96ac19c014ff2cc89df10cae3f21b38211ff
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132343588"
---
# <a name="identify-required-appliances"></a>Identifier les appareils nécessaires

Cet article fournit des informations sur les appliances de détection certifiées Defender pour IoT. La solution Defender pour IoT peut être déployée sur des appliances physiques et virtuelles.

Cela comprend des appliances *préconfigurées* certifiées, sur lesquelles les logiciels sont déjà installés, et des appliances certifiées non configurées sur lesquelles vous pouvez télécharger et installer les logiciels requis.

L’article fournit également des spécifications pour une appliance de console de gestion locale. La console de gestion locale n’est pas disponible en tant qu’appliance préconfigurée.

- Si vous souhaitez acheter un capteur préconfiguré, passez en revue les modèles disponibles dans la section [Appliances de détection](#sensor-appliances), puis procédez à l’achat.

- Si vous souhaitez acheter votre propre appliance, passez en revue les modèles disponibles dans la section [Appliances de détection](#sensor-appliances) et dans la section [Autres appliances certifiées](#additional-certified-appliances). Une fois que vous avez acquis l’appliance, vous pouvez télécharger et installer le logiciel.

- Si vous souhaitez acheter la console de gestion locale, passez en revue les informations contenues dans la section [Appliance de console de gestion locale](#on-premises-management-console-appliance). Une fois que vous avez acquis l’appareil, vous pouvez télécharger et installer le logiciel.

Une fois que vous avez terminé les tâches décrites ici, vous pouvez installer le logiciel et configurer votre réseau.

## <a name="sensor-appliances"></a>Appliances de détection

Defender pour IoT prend en charge les déploiements physiques et virtuels.

### <a name="physical-sensors"></a>Capteurs physiques

Cette section fournit une vue d’ensemble des modèles de capteur physique disponibles. Vous pouvez acheter des capteurs avec des logiciels préconfigurés ou acheter des capteurs qui ne sont pas préconfigurés.

| Type de déploiement | Entreprise | Enterprise | Montage en rack SMB| SMB renforcé |
|--|--|--|--|--|
| Image | :::image type="content" source="media/how-to-prepare-your-network/corporate-hpe-proliant-dl360-v2.png" alt-text="Modèle de niveau société."::: | :::image type="content" source="media/how-to-prepare-your-network/enterprise-and-smb-hpe-proliant-dl20-v2.png" alt-text="Modèle de niveau entreprise."::: | :::image type="content" source="media/how-to-prepare-your-network/enterprise-and-smb-hpe-proliant-dl20-v2.png" alt-text="Modèle de niveau SMB."::: | :::image type="content" source="media/how-to-prepare-your-network/office-ruggedized.png" alt-text="Le modèle de niveau SMB renforcé."::: |
| Modèle | HPE ProLiant DL360 | HPE ProLiant DL20 | HPE ProLiant DL20 | HPE EL300 |
| Ports de surveillance | jusqu’à 15 RJ45 ou 8 OPT | jusqu’à 8 RJ45 ou 6 OPT | jusqu’à 4 RJ45 | jusqu’à 5 RJ45 |
| Bande passante maximum[1](#anchortext) | 3 Go/s | 1 Go/s | 200 Mo/s | 100 Mo/s |
| Nombre maximal d’appareils protégés | 12 000 | 10 000 | 1 000 | 800 |

Pour plus d’informations sur le fournisseur, consultez [Spécifications des appliances](#appliance-specifications).

À propos des capteurs préconfigurés : Microsoft s’est associé à Arrow pour fournir des capteurs préconfigurés. Pour acheter un capteur préconfiguré, contactez Arrow à l’adresse suivante : <hardware.sales@arrow.com>.

À propos de l’utilisation de votre propre appliance : Passez en revue les modèles pris en charge décrits ici. Une fois que vous avez acquis votre appliance, accédez à **Defender pour IoT** > **ISO des capteurs de réseau** > **Installation** pour télécharger le logiciel.

:::image type="content" source="media/how-to-prepare-your-network/azure-defender-for-iot-sensor-download-software-screen.png" alt-text="ISO des capteurs de réseau.":::

<a id="anchortext">1</a> La capacité de bande passante peut varier, en fonction de la distribution des protocoles.

### <a name="virtual-sensors"></a>Capteurs virtuels

Cette section décrit les capteurs virtuels qui sont disponibles.

| Type de déploiement | Entreprise | Enterprise | SMB |
|--|--|--|--|
| Bande passante maximum | 2,5 Go/s | 800 Mo/s | 160 Mo/s |
| Nombre maximal d’appareils protégés | 12 000 | 10 000 | 800 |

## <a name="on-premises-management-console-appliance"></a>Appliance de console de gestion locale

La console de gestion est disponible en tant que déploiement virtuel.

| Type de déploiement | Enterprise |
|--|--|
| Type d’appliance | Machine virtuelle HPE DL20 |
| Nombre de capteurs gérés | Jusqu’à 300 |

Une fois que vous avez acquis une console de gestion locale, accédez à **Defender pour IoT** > **Console de gestion locale** > **Installation ISO** pour télécharger le fichier ISO.

:::image type="content" source="media/how-to-prepare-your-network/azure-defender-for-iot-iso-download-screen.png" alt-text="Console de gestion locale.":::

## <a name="appliance-specifications"></a>Spécifications des appliances

Cette section décrit les spécifications matérielles pour les appliances suivantes :

- Déploiement de société : HPE ProLiant DL360

- Déploiement d’entreprise : HPE ProLiant DL20

- Déploiement SMB : HPE ProLiant DL20

- Spécifications de l’appliance virtuelle

## <a name="corporate-deployment-hpe-proliant-dl360"></a>Déploiement de société : HPE ProLiant DL360

| Composant | Spécifications techniques |
|--|--|
| Châssis | Serveur rack 1U |
| Dimensions | 42,9 x 43,46 x 70,7 cm/1,69 x 17,11 x 27,83 po |
| Poids | 16,27 kg max (35,86 lb) |
| Processeur | Intel Xeon Silver 4215 R 3,2 GHz, 11M cache, 8c/16T, 130 W |
| Circuit microprogrammé | Intel C621 |
| Mémoire | 32 Go = 2 x DDR4 ECC UDIMM 16 Go 2 666 MT/s |
| Stockage | 6 x Disque dur enfichable à chaud 1,2 To SAS 12G Enterprise 10K SFF (2,5 po) – RAID 5 |
| Contrôleur de réseau | Intégré : 2 x 1 Go <br>Intégré : carte de ports iLO 1 Go <br>Externe : 1 x Adaptateur HPE Ethernet 366FLR 1 Go 4 ports |
| Gestion | Licence HPE iLO Advanced |
| Accès sur l’appareil | Deux ports USB 3.0 à l’arrière<br>Un USB 2.0 frontal<br>Un USB 3.0 interne |
| Power | 2 x Kit d’alimentation HPE platine, à logement flexible, enfichable à chaud, faible teneur en halogène, 500 W |
| Support du rack | Kit de glissières faciles à installer à petit facteur de forme HPE 1U de 10e génération |

### <a name="appliance-bom"></a>Nomenclature de l’appliance

| PN | Description | Quantité |
|--|--|--|
| P19766-B21 | Serveur HPE NC CTO DL360 8SFF de 10e génération | 1 |
| P19766-B21 | Europe : localisation multilingue | 1 |
| P24479-L21 | Kit Intel Xeon-S 4215 R FIO pour DL360 Gen10 | 1 |
| P24479-B21 | Kit Intel Xeon-S 4215 R pour DL360 de 10e génération | 1 |
| P00922-B21 | Module de mémoire HPE 16 Go 2Rx8 PC4-2933Y-R | 2 |
| 872479-B21 | HDD HPE 1,2 To SAS 10K SFF SC DS | 6 |
| 811546-B21 | Adaptateur HPE BASE-T I350 1 GbE 4 ports | 1 |
| P02377-B21 | Câble HPE Smart Hybrid Capacitor 145 mm | 1 |
| 804331-B21 | Contrôleur HPE Smart Array P408i-a SR de 10e génération | 1 |
| 665240-B21 | Adaptateur HPE FLR-T I350 1 GbE 4 ports | 1 |
| 871244-B21 | Kit de ventilateurs haute performance HPE DL360 de 10e génération | 1 |
| 865408-B21 | Kit d’alimentation HPE platine, à logement flexible, enfichable à chaud, faible teneur en halogène, 500 W | 2 |
| 512485-B21 | Licence pour HPE iLO Advanced 1 serveur avec 1 an d’assistance | 1 |
| 874543-B21 | Kit de glissières faciles à installer à petit facteur de forme HPE 1U de 10e génération | 1 |

## <a name="enterprise-deployment-hpe-proliant-dl20"></a>Déploiement d’entreprise : HPE ProLiant DL20

| Composant | Spécifications techniques |
|--|--|
| Châssis | Serveur rack 1U |
| Dimensions (hauteur x largeur x profondeur) | 4,32 x 43,46 x 38,22 cm/1,70 x 17,11 x 15,05 po |
| Poids | 7,9 kg/17,41 lb |
| Processeur | Intel Xeon E-2234, 3,6 GHz, 4C/8T, 71 W |
| Circuit microprogrammé | Intel C242 |
| Mémoire | 2 x Module de mémoire DDR4-2666 Dual Rank x8 16 Go |
| Stockage | 3 x SATA 1 To 6G Midline 7,2 K-SFF (2,5 po) – RAID 5 avec contrôleur Smart Array P408i-a SR |
| Contrôleur de réseau | Intégré : 2 x 1 Go <br>Intégré : carte de ports iLO 1 Go <br>Externe : 1 x Adaptateur HPE Ethernet 366FLR 1 Go 4 ports |
| Gestion | Licence HPE iLO Advanced |
| Accès sur l’appareil | Avant : 1 x USB 3.0, 1 x port USB du service iLO <br>Arrière : 2 x USB 3.0 <br>Interne : 1 x USB 3.0 |
| Power | Double alimentation enfichable à chaud 500 W |
| Support du rack | Kit de rail de friction pour armoire HPE 1U |

### <a name="appliance-bom"></a>Nomenclature de l’appliance

| PN | Description : haut de gamme | Quantité |
|--|--|--|
| P06963-B21 | Serveur CTO 4SFF HPE DL20 de 10e génération | 1 |
| P06963-B21 | Serveur CTO 4SFF HPE DL20 de 10e génération | 1 |
| P17104-L21 | Kit FIO E-2234 HPE DL20 de 10e génération | 1 |
| 879507-B21 | Kit STND HPE 16 Go 2Rx8 PC4-2666V-E | 2 |
| 655710-B21 | HDD SATA HPE 1 To 7,2 K SFF SC DS | 3 |
| P06667-B21 | Kit d’extension HPE DL20 de 10e génération x8x16 FlexibleLOM | 1 |
| 665240-B21 | Adaptateur HPE Ethernet 366FLR 1 Go 4 ports | 1 |
| 782961-B21 | Batterie HPE Smart Storage 12 W | 1 |
| 869081-B21 | Contrôleur LH modulaire HPE Smart Array P408i-a SR de 10e génération | 1 |
| 865408-B21 | Kit d’alimentation HPE platine, à logement flexible, enfichable à chaud, faible teneur en halogène, 500 W | 2 |
| 512485-B21 | Licence pour HPE iLO Advanced 1 serveur avec 1 an d’assistance | 1 |
| P06722-B21 | Kit RPS Enablement FIO HPE DL20 de 10e génération | 1 |
| 775612-B21 | Kit de rail de friction pour armoire HPE 1U | 1 |

## <a name="smb-deployment-hpe-proliant-dl20"></a>Déploiement SMB : HPE ProLiant DL20

| Composant | Spécifications techniques |
|--|--|
| Châssis | Serveur rack 1U |
| Dimensions (hauteur x largeur x profondeur) | 4,32 x 43,46 x 38,22 cm/1,70 x 17,11 x 15,05 po |
| Poids | 7,88 kg/17,37 lb |
| Processeur | Intel Xeon E-2224, 3,4 GHz, 4C, 71 W |
| Circuit microprogrammé | Intel C242 |
| Mémoire | 1 x Module de mémoire DDR4-2666 Dual Rank x8 8 Go |
| Stockage | 2 x SATA 1 To 6G Midline 7,2 K-SFF (2,5 po) – RAID 1 avec Smart Array P208i-a |
| Contrôleur de réseau | Intégré : 2 x 1 Go <br>Intégré : carte de ports iLO 1 Go <br>Externe : 1 x Adaptateur HPE Ethernet 366FLR 1 Go 4 ports |
| Gestion | Licence HPE iLO Advanced |
| Accès sur l’appareil | Avant : 1 x USB 3.0, 1 x port USB du service iLO <br>Arrière : 2 x USB 3.0 <br>Interne : 1 x USB 3.0 |
| Power | Alimentation enfichable à chaud 290 W |
| Support du rack | Kit de rail de friction pour armoire HPE 1U |

### <a name="appliance-bom"></a>Nomenclature de l’appliance

| PN | Description | Quantité |
|--|--|--|
| P06961-B21 | Serveur CTO 2LFF NHP HPE DL20 de 10e génération | 1 |
| P06961-B21 | Serveur CTO 2LFF NHP HPE DL20 de 10e génération | 1 |
| P17102-L21 | Kit FIO E-2224 HPE DL20 de 10e génération | 1 |
| 879505-B21 | Kit STND HPE 8 Go 1Rx8 PC4-2666V-E | 1 |
| 801882-B21 | HDD SATA HPE 1 To 7,2 K LFF RW | 2 |
| P06667-B21 | Kit d’extension HPE DL20 de 10e génération x8x16 FlexibleLOM | 1 |
| 665240-B21 | Adaptateur HPE Ethernet 366FLR 1 Go 4 ports | 1 |
| 869079-B21 | Contrôleur LH modulaire HPE Smart Array E208i-a SR de 10e génération | 1 |
| P21649-B21 | Kit d’alimentation FIO HPE DL20 de 10e génération platine 290 W | 1 |
| P06683-B21 | Kit de câbles AROC HPE DL20 de 10e génération M.2 SATA/LFF | 1 |
| 512485-B21 | Licence pour HPE iLO Advanced 1 serveur avec 1 an d’assistance | 1 |
| 775612-B21 | Kit de rail de friction pour armoire HPE 1U | 1 |

## <a name="smb-rugged-hpe-edgeline-el300"></a>SMB renforcé : HPE Edgeline EL300

| Composant | Spécifications techniques |
|--|--|
| Construction | Aluminium, conception sans ventilateur et étanche à la poussière |
| Dimensions (hauteur x largeur x profondeur) | 200,5 mm (7,9") de hauteur, 232 mm (9,14") de large par 100 mm (3,9") de profondeur |
| Poids | 4,91 kg (10,83 lb) |
| UC | Intel Core i7-8650U (1,9 GHz/4 cœurs/15 W) |
| Circuit microprogrammé | Concentrateur de contrôleur de plateforme Intel® Q170 |
| Mémoire | Module SODIMM 8 Go DDR4 2133 MHz à large plage de température |
| Stockage | SSD mSATA 128 Go 3ME3 à large plage de température |
| Contrôleur de réseau | 6 x ports Gigabit Ethernet par Intel® I219 |
| Accès sur l’appareil  | 4 USB : 2 avant ; 2 arrière ; 1 interne |
| Adaptateur secteur | 250 V/10 A |
| Montage | Kit de montage, rail DIN |
| Température de fonctionnement | 0 à +70°C  |
| Humidité | De 10 % à 90 % sans condensation |
| Vibration | 0,3 gramme 10 à 300 Hz, 15 minutes par axe - rail DIN   |
| Choc | 10 g 10 ms, demi-sinusoïde, trois pour chaque axe. (Impulsions positives et négatives) – Rail Din |

### <a name="appliance-bom"></a>Nomenclature de l’appliance
| Produit | Description |
|--|--|
| P25828-B21 | Système de périphérie convergé HPE Edgeline EL300 v2 |
| P25828-B21 B19 | Système de périphérie convergé HPE EL300 v2 |
| P25833-B21 | Intel Core i7-8650U (1,9 GHz/4 cœurs/15 W) Kit de processeur de base FIO pour HPE Edgeline EL300 |
| P09176-B21 | Adaptateur HPE Edgeline 8 (1x8 Go) Dual Rank x8 DDR4-2666 SODIMM WT CAS-19-19-19 Kit mémoire inscrite FIO |
| P09188-B21 | HPE Edgeline 256 Go SATA 6G lecture intensive M.2 2242 Gtie 3 ans Wty SSD large place de température |
| P04054-B21 | Kit d’activation HPE Edgeline EL300 SFF vers M.2 |
| P08120-B21 | Carte de transfert HPE Edgeline EL300 12 V CC FIO |
| P08641-B21 | Alimentation HPE Edgeline EL300 80 W 12 V CC |
| AF564A | Adaptateur d’alimentation HPE C13-SI-32 IL 250 V 10 A, 1,83 m |
| P25835-B21 | Carte de transporteur HPE EL300 v2 FIO |
| R1P49AAE | HPE EL300 iSM Adv 3yr 24x7 Sup_Upd E-LTU |
| P08018-B21 facultatif | Kit de support bas profil HPE Edgeline EL300  |
| P08019-B21 facultatif | Kit de montage de rail DIN HPE Edgeline EL300 |
| P08020-B21 facultatif | Kit de montage mural HPE Edgeline EL300 |
| P03456-B21 facultatif | Carte fille HPE Edgeline 1GbE 4 ports TSN FIO |

## <a name="virtual-appliance-specifications"></a>Spécifications de l’appliance virtuelle

### <a name="sensors"></a>Capteurs

| Type | Entreprise | Enterprise | SMB |
|--|--|--|--|
| Processeurs virtuels | 32 | 8 | 4 |
| Mémoire | 32 Go | 32 Go | 8 Go |
| Stockage | 5,6 To | 1,8 To | 500 Go |

### <a name="on-premises-management-console-appliance"></a>Appliance de console de gestion locale

| Type | Enterprise |
|--|--|
| Description | Appliance virtuelle pour les déploiements de type entreprise |
| Processeurs virtuels | 8 |
| Mémoire | 32 Go |
| Stockage | 1,8 To |

Hyperviseurs pris en charge : VMware ESXi 5.0 et versions ultérieures, Hyper-V

## <a name="additional-certified-appliances"></a>Autres appliances certifiées

Cette section détaille les appliances supplémentaires qui ont été certifiées par Microsoft, mais qui ne sont pas proposées en tant qu’appliances préconfigurées.

| Type de déploiement | Enterprise |
|--|--|
| Image | :::image type="content" source="media/how-to-prepare-your-network/deployment-type-enterprise-for-azure-defender-for-iot-v2.png" alt-text="Déploiement de type entreprise."::: |
| Modèle | Dell PowerEdge R340 XL |
| Ports de surveillance | Jusqu’à neuf RJ45 ou six OPT |
| Bande passante maximale [1](#anchortext2)| 1 Go/s |
| Nombre maximal d’appareils protégés | 10 000 |

<a id="anchortext2">1</a> La capacité de bande passante peut varier, en fonction de la distribution des protocoles.

Une fois que vous avez acheté l’appliance, accédez à **Defender pour IoT** > **ISO des capteurs de réseau** > **Installation** pour télécharger le logiciel.

:::image type="content" source="media/how-to-prepare-your-network/azure-defender-for-iot-sensor-download-software-screen.png" alt-text="ISO des capteurs de réseau.":::

## <a name="next-steps"></a>Étapes suivantes

[À propos de l’installation de Microsoft Defender pour IoT](how-to-install-software.md)

[À propos de la configuration du réseau pour Microsoft Defender pour IoT](how-to-set-up-your-network.md)
