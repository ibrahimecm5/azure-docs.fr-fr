---
title: Forum aux questions sur Defender for IoT
description: Trouvez des réponses à la plupart des questions fréquemment posées sur les fonctionnalités et le service Microsoft Defender pour IoT.
ms.topic: conceptual
ms.date: 11/09/2021
ms.openlocfilehash: 68a681e527c6c68f601c91f27addf9f36e4d0b57
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132325214"
---
# <a name="microsoft-defender-for-iot-frequently-asked-questions"></a>Questions fréquentes (FAQ) sur Microsoft Defender pour IoT

Cet article fournit une liste de questions fréquemment posées et les réponses correspondantes au sujet de Defender pour IoT.

## <a name="what-is-azures-unique-value-proposition-for-iot-security"></a>Quelle est la proposition de valeur unique d’Azure pour la sécurité IoT ?

Defender pour IoT permet aux entreprises d’étendre leur vue de la cybersécurité existante à la totalité des solutions IoT. Azure fournit une vue de bout en bout de votre solution d’entreprise, ce qui vous permet de prendre des mesures et décisions professionnelles en fonction de la posture de sécurité de votre entreprise et des données collectées. La sécurité combinée d’Azure IoT, Azure IoT Edge et Microsoft Defender pour le cloud vous permet de créer la solution que vous souhaitez avec la sécurité dont vous avez besoin.

## <a name="our-organization-uses-proprietary-non-standard-industrial-protocols-are-they-supported"></a>Notre organisation utilise des protocoles industriels non standard propriétaires. Sont-ils pris en charge ? 

Microsoft Defender pour IoT offre une prise en charge complète des protocoles. En plus de la prise en charge des protocoles incorporés, vous pouvez sécuriser les appareils IoT et OT exécutant des protocoles propriétaires et personnalisés, ou des protocoles qui s’écartent de n’importe quel standard. À l’aide du kit de développement logiciel (SDK) horizon Open Development Environment (ODE), les développeurs peuvent créer des plug-ins de dissecteurs qui décodent le trafic réseau en fonction des protocoles définis. Le trafic est analysé par les services pour fournir une analyse complète, des alertes et des rapports. Utilisez horizon pour :
- Développez la visibilité et le contrôle sans avoir à effectuer une mise à niveau vers les nouvelles versions.
- Sécurisez les informations propriétaires en développant sur site en tant que plug-in externe. 
- Localisez le texte pour les alertes, les événements et les paramètres de protocole.

Cette solution unique pour le développement de protocoles en tant que plug-ins ne requiert pas d’équipes de développeurs dédiées ou de versions de version pour prendre en charge un nouveau protocole. Les développeurs, les partenaires et les clients peuvent développer des protocoles en toute sécurité et partager des Insights et des connaissances en utilisant Horizon. 

## <a name="do-i-have-to-purchase-hardware-appliances-from-microsoft-partners"></a>Dois-je acheter des appliances matérielles auprès de partenaires Microsoft ?
Microsoft Defender pour le capteur IoT s’exécute sur des spécifications matérielles spécifiques, comme décrit dans le [Guide des spécifications matérielles](./how-to-identify-required-appliances.md), les clients peuvent acheter du matériel certifié auprès de partenaires Microsoft ou utiliser la nomenclature (BOM, bill of materials) fournie et les acheter eux-mêmes. 

Le matériel certifié a été testé dans nos laboratoires pour la stabilité des pilotes, le rejet des paquets et le dimensionnement du réseau.


## <a name="regulation-does-not-allow-us-to-connect-our-system-to-the-internet-can-we-still-utilize-defender-for-iot"></a>La réglementation ne nous permet pas de connecter notre système à Internet. Pouvons-nous toujours utiliser Defender pour IoT ?

Oui, vous pouvez ! La solution locale Microsoft Defender pour la plateforme IoT est déployée en tant qu’appliance de capteur physique ou virtuel qui ingère passivement le trafic réseau (via SPAN, RSPAN ou TAP) pour analyser, découvrir et superviser en continu les réseaux IT, OT et IoT. Pour les grandes entreprises, plusieurs capteurs peuvent agréger leurs données dans une console de gestion locale.

## <a name="where-in-the-network-should-i-connect-monitoring-ports"></a>Où dois-je connecter les ports de surveillance dans le réseau ?

Le capteur Microsoft Defender pour IoT se connecte à un port SPAN ou un TAP réseau et commence immédiatement à collecter le trafic réseau ICS via une supervision passive (sans agent). Il n’a aucun impact sur les réseaux OT, car il n’est pas placé dans le chemin d’accès aux données et n’analyse pas activement les appareils.

Par exemple :
- Une seule Appliance (virtuelle physique) peut se trouver dans la couche DMZ de l’atelier, ce qui a pour effet d’acheminer le trafic des cellules de l’atelier vers cette couche.
- Vous pouvez également localiser de petits mini-capteurs dans chaque cellule d’atelier avec l’administration Cloud ou locale qui résidera dans la couche DMZ d’atelier. Une autre appliance (virtuelle ou physique) peut surveiller le trafic dans la couche DMZ d’atelier (pour les environnements SCADA, historien ou MES).

## <a name="how-does-defender-for-iot-compare-to-the-competition"></a>Comment Defender pour IoT se compare-t-il par rapport à la concurrence ?

Microsoft Defender pour IoT offre une sécurité complète sur l’ensemble de vos appareils IoT/OT. Pour les **organisations d’utilisateurs finaux**, Microsoft Defender pour IoT offre une sécurité sans agent, au niveau de la couche réseau, qui est rapidement déployée, fonctionne avec divers équipements OT propriétaires et différents systèmes Windows hérités, et qui interagit avec Microsoft Sentinel et d’autres outils SOC. La solution peut être déployée localement ou dans des environnements connectés à Azure. Pour les **fabricants d’appareils IoT**, Microsoft Defender pour IoT propose des agents légers permettant d’intégrer la sécurité de la couche appareil dans les nouvelles initiatives IoT/OT.

## <a name="do-i-have-to-be-an-azure-customer"></a>Dois-je être un client Azure ?

Non, pour la version sans agent de Microsoft Defender pour IoT, vous n’avez pas besoin d’être un client Azure. Toutefois, si vous souhaitez envoyer des alertes à Azure Microsoft, provisionner les capteurs du réseau et superviser leur intégrité à partir du cloud et tirer parti des mises à jour automatiques des logiciels et du renseignement sur les menaces, vous devrez connecter le capteur à Azure via Azure IoT Hub.

Pour la version de Microsoft Defender pour IoT basée sur un agent, vous devez être un client Azure.

## <a name="what-happens-when-the-internet-connection-stops-working"></a>Que se passe-t-il lorsque la connexion Internet cesse de fonctionner ?

Les capteurs et les agents continuent à s’exécuter et à stocker les données tant que l’appareil est en cours d’exécution. Les données sont stockées dans le cache de messages de sécurité en fonction de la configuration de taille. Lorsque l’appareil retrouve sa connectivité, les messages de sécurité sont à nouveau envoyés.

## <a name="how-can-i-change-a-users-passwords"></a>Comment puis-je modifier les mots de passe d’un utilisateur

Découvrez comment [Modifier le mot de passe d’un utilisateur](how-to-create-and-manage-users.md#change-a-users-password) pour le capteur ou la console de gestion locale.

Vous pouvez également [Récupérer le mot de passe pour la console de gestion locale ou le capteur](how-to-create-and-manage-users.md#recover-the-password-for-the-on-premises-management-console-or-the-sensor).

## <a name="how-do-i-activate-the-sensor-and-on-premises-management-console"></a>Comment activer le capteur et de la console de gestion locale

Pour plus d’informations sur l’activation de votre capteur, consultez [Se connecter et activer le capteur](how-to-activate-and-set-up-your-sensor.md#sign-in-and-activate-the-sensor).

Pour plus d’informations sur l’activation de votre console de gestion locale, consultez [Activer la console de gestion locale](how-to-activate-and-set-up-your-on-premises-management-console.md#activate-the-on-premises-management-console).

## <a name="how-to-change-the-network-configuration"></a>Comment modifier la configuration du réseau

Vous pouvez voir comment [mettre à jour la configuration de votre réseau de capteur avant l’activation](how-to-activate-and-set-up-your-sensor.md#update-sensor-network-configuration-before-activation).

Vous pouvez également [mettre à jour la configuration du réseau du capteur](how-to-manage-individual-sensors.md#update-the-sensor-network-configuration) après l’activation.

Vous pouvez utiliser les [commandes](references-work-with-defender-for-iot-cli-commands.md#network-configuration) CLI pour [modifier les configurations réseau](references-work-with-defender-for-iot-cli-commands.md#network-configuration).

## <a name="how-do-i-check-the-sanity-of-my-deployment"></a>Comment faire vérifier l’intégrité de mon déploiement

Après avoir installé le logiciel de votre capteur ou de la console de gestion locale, vous devez effectuer la [validation après l’installation](how-to-install-software.md#post-installation-validation). Vous y trouverez des informations sur [la vérification de l’intégrité du système à l’aide de l’interface de commande](how-to-install-software.md#check-system-health-by-using-the-cli), l'exécution de vérification de la [validité](how-to-install-software.md#sanity) et l’examen de vos [statistiques système](how-to-install-software.md#system) générales.

Vous pouvez suivre ces liens si [l’appliance ne répond pas](how-to-install-software.md#the-appliance-isnt-responding) ou si [vous ne pouvez pas vous connecter à l’aide d’une interface Web](how-to-install-software.md#you-cant-connect-by-using-a-web-interface).

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur la prise en main de Defender pour IoT, consultez la documentation suivante :

- Lire la [Vue d’ensemble de Defender pour IoT](overview.md)
- Vérifier la [configuration requise pour le système](quickstart-system-prerequisites.md)
- En savoir plus sur la [Prise en main de Defender pour IoT](getting-started.md)
