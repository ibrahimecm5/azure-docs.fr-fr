---
title: Présentation de l'architecture de solution sans agent
description: Découvrez l’architecture sans agent et le flux d’informations de Microsoft Defender pour IoT.
ms.topic: overview
ms.date: 11/09/2021
ms.openlocfilehash: 9a3a125ea91ccb9649a4bfc70555885b87d59e65
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132318918"
---
# <a name="microsoft-defender-for-iot-architecture"></a>Architecture de Microsoft Defender pour IoT

Cet article décrit l’architecture système fonctionnelle de la solution Defender pour IoT sans agent. Microsoft Defender pour IoT offre deux ensembles de fonctionnalités pour répondre aux besoins de votre environnement, une solution sans agent pour les organisations, et une solution basée sur un agent pour les fabricants d’appareils.

## <a name="agentless-solution-architecture-for-organizations"></a>Architecture de la solution sans agent pour les organisations
### <a name="defender-for-iot-components"></a>Composants de Defender pour IoT

Defender pour IoT se connecte au cloud Azure et aux composants locaux. La solution est conçue pour offrir une scalabilité dans les environnements de grande taille distribués géographiquement avec plusieurs emplacements distants. Cette solution active une architecture distribuée multicouche par pays, région, unité commerciale ou zone. 

Microsoft Defender pour IoT comprend les composants suivants : 

**Déploiements connectés au cloud**

- Machine virtuelle ou appliance capteur Microsoft Defender pour IoT
- Portail Azure pour la gestion cloud et l’intégration à Microsoft Sentinel
- Console de gestion locale pour la gestion de site local
- Un agent de sécurité incorporé (facultatif)

**Déploiements hermétiques (hors connexion)**

- Machine virtuelle ou appliance capteur Microsoft Defender pour IoT
- Console de gestion locale pour la gestion de site local

:::image type="content" source="./media/architecture/defender-iot-security-architecture-v3.png" alt-text="Architecture de Defender pour IoT.":::

### <a name="microsoft-defender-for-iot-sensors"></a>Capteurs Microsoft Defender pour IoT

Les capteurs Defender pour IoT découvrent et supervisent en permanence les appareils du réseau. Les capteurs collectent le trafic réseau de partage de connexion Internet (ICS) via une surveillance passive (sans agent) des appareils IoT et OT. 
 
Spécialement conçue pour les réseaux IoT et OT, la technologie sans agent offre une visibilité approfondie du risque IoT et OT, quelques minutes seulement après la connexion au réseau. En raison du caractère non invasif de son approche d’analyse du trafic réseau (NTA), elle n’a aucune incidence sur les performances du réseau et des périphériques réseau. 
 
En appliquant des solutions brevetées d’analytique comportementale prenant en compte l’IoT et l’OT, et l’inspection approfondie des paquets (DPI) de couche 7, cette technologie vous permet d’analyser au-delà des solutions traditionnelles basées sur des signatures pour détecter immédiatement des menaces IoT et OT avancées (comme des programmes malveillants sans fichier) en fonction d’une activité anormale ou non autorisée. 
  
Les capteurs Defender pour IoT se connectent à un port SPAN ou à un TAP de réseau virtuel et commencent immédiatement à effectuer la DPI sur le trafic réseau IoT et OT. 
 
La collecte, le traitement et l’analyse de données, ainsi que les alertes s’effectuent directement sur le capteur. Ce processus est idéal pour les emplacements disposant d’une connectivité à faible bande passante ou à latence élevée, car seules les métadonnées sont transférées vers la console de gestion.

Le capteur comprend cinq moteurs de détection analytique. Les moteurs déclenchent des alertes en fonction de l’analyse du trafic en temps réel et pré-enregistré. Les moteurs disponibles sont les suivants : 

#### <a name="protocol-violation-detection-engine"></a>Moteur de détection de violation de protocole
Le moteur de détection de violation de protocole identifie l’utilisation des structures de paquets et des valeurs de champ qui ne respectent pas les spécifications de protocole ICS, par exemple : exception Modbus et initiation d’alertes de code de fonction obsolète.

#### <a name="policy-violation-detection-engine"></a>Moteur de détection de violation de stratégie
Grâce à l’apprentissage automatique, le moteur de détection de violation de stratégie alerte les utilisateurs de tout écart par rapport au comportement de base, comme l’utilisation non autorisée de codes de fonction spécifiques, l’accès à des objets spécifiques ou des modifications de configuration d’appareil. Par exemple, la version du logiciel DeltaV a changé, ainsi que les alertes de programmation PLC non autorisées. Plus précisément, le moteur de violation de stratégie modélise les réseaux ICS en tant que séquences déterministes d’états et de transitions, en utilisant une technique brevetée appelée modélisation d’état fini industriel (Industrial Finite State Modeling, IFSM). Le moteur de détection de violation de stratégie établit une ligne de base des réseaux ICS, de sorte que la plateforme nécessite, pour créer une ligne de base du réseau, une période d’apprentissage plus courte que les approches ou analyses mathématiques génériques qui ont été développées à l’origine pour les réseaux informatiques plutôt que pour les réseaux OT.

#### <a name="industrial-malware-detection-engine"></a>Moteur de détection de programmes malveillant industriels
Le moteur de détection de programmes malveillants industriels identifie les comportements qui indiquent la présence de programmes malveillants connus, tels que Conficker, Black Energy, Havex, WannaCry, NotPetya et Triton. 

#### <a name="anomaly-detection-engine"></a>Moteur de détection d’anomalies
Le moteur de détection d’anomalies détecte les communications et comportements machine à machine (M2M) inhabituels. En modélisant les réseaux ICS en tant que séquences déterministes d’états et de transitions, la plateforme nécessite une période d’apprentissage plus brève que les approches mathématiques génériques ou analyses développées à l’origine pour l’informatique plutôt que pour l’OT. Elle détecte également les anomalies plus rapidement, avec un nombre minimal de faux positifs. Les alertes du moteur de détection d’anomalies incluent les tentatives de connexion SMB excessives et les alertes détectées par analyse PLC.

#### <a name="operational-incident-detection"></a>Détection d’incidents opérationnels
La détection d’incidents opérationnels détecte des problèmes opérationnels, tels qu’une connectivité intermittente, qui peuvent indiquer des signes précoces de défaillance de l’équipement. Par exemple, l’appareil est supposé être déconnecté (ne répondant pas), et la commande PLC d’arrêt Siemens S7 a reçu des alertes.

### <a name="management-consoles"></a>Consoles de gestion
La gestion de Microsoft Defender pour IoT dans des environnements hybrides s’effectue via deux portails : 
- Console de capteur
- Console de gestion locale
- Le portail Azure

### <a name="sensor-console"></a>Console de capteur
Les détections de capteur s’affichent dans la console de capteur, où elles peuvent être consultées, examinées et analysées dans un mappage réseau, un inventaire des appareils et un vaste éventail de rapports, tels que des rapports d’évaluation des risques, des requêtes d’exploration de données et des vecteurs d’attaque. Vous pouvez également utiliser la console pour afficher et gérer les menaces détectées par des moteurs de capteur, transmettre des informations à des systèmes partenaires, gérer des utilisateurs, et bien plus encore.

:::image type="content" source="./media/architecture/sensor-console-v2.png" alt-text="Console de capteur Defender pour IoT":::

### <a name="on-premises-management-console"></a>Console de gestion locale
La console de gestion locale permet aux opérateurs du centre des opérations de sécurité (security operations center, SOC) de gérer et analyser les alertes agrégées à partir de plusieurs capteurs dans un seul tableau de bord donnant une vue d’ensemble de l’intégrité des réseaux OT.

Cette architecture fournit une vue unifiée complète du réseau au niveau du SOC, une gestion optimisée des alertes et le contrôle de la sécurité réseau opérationnelle, garantissant ainsi que la prise de décision et la gestion des risques restent irréprochables.

Outre le multilocataire, la supervision, l’analyse des données et le contrôle à distance centralisé des capteurs, la console de gestion fournit des outils supplémentaires de maintenance du système (comme l’exclusion d’alertes) ainsi que des fonctionnalités de création de rapports entièrement personnalisées pour chaque appliance à distance. Cette architecture prend en charge la gestion locale au niveau d’un site ou d’une zone, et la gestion globale au sein du SOC.

Il est possible de déployer la console de gestion pour une configuration de haute disponibilité, qui fournit une console de sauvegarde recevant régulièrement les sauvegardes de tous les fichiers de configuration requis pour une éventuelle récupération. En cas de défaillance de la console principale, les appliances de gestion du site local basculent automatiquement pour se synchroniser avec la console de sauvegarde afin de maintenir la disponibilité sans interruption.

Étroitement intégrée à vos workflows SOC et à vos runbooks, elle permet de hiérarchiser facilement les activités d’atténuation et de corréler les menaces entre les différents sites.

- Holistique : réduisez la complexité grâce à une seule plateforme unifiée pour la gestion des appareils, la gestion des risques et des vulnérabilités, et la supervision des menaces avec réponse aux incidents.

- Agrégation et corrélation : affichez, agrégez et analysez les données et les alertes collectées sur tous les sites.

- Contrôler tous les capteurs : configurez et surveillez tous les capteurs à partir d’un emplacement unique.

   :::image type="content" source="media/updates/alerts-and-site-management-v2.png" alt-text="Gérez l’ensemble de vos alertes et informations.":::

### <a name="azure-portal"></a>Portail Azure

Defender pour IoT dans le portail Azure dans Azure est conçu pour vous aider à :

- Acheter des appliances de solution

- Installer et mettre à jour les logiciels

- Intégrer des capteurs dans Azure

- Mettre à jour des packages de renseignement sur les menaces

## <a name="next-steps"></a>Étapes suivantes

[FAQ de Defender pour IoT](resources-frequently-asked-questions.md)

[Prérequis système](quickstart-system-prerequisites.md)
