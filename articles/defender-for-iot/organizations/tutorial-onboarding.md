---
title: Installation d’un essai de Microsoft Defender pour IoT
description: Dans ce tutoriel, vous allez apprendre à intégrer à Microsoft Defender pour IoT un capteur virtuel, sur une machine virtuelle, en utilisant un abonnement d’essai de Microsoft Defender pour IoT.
author: ElazarK
ms.author: v-ekrieg
ms.topic: tutorial
ms.date: 11/09/2021
ms.custom: template-tutorial
ms.openlocfilehash: 91e7d217861be41453669ea47525ed563200f54e
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132283632"
---
# <a name="tutorial-microsoft-defender-for-iot-trial-setup"></a>Tutoriel : Installation d’un essai de Microsoft Defender pour IoT

Ce tutoriel va vous aider à apprendre à intégrer à Microsoft Defender pour IoT un capteur virtuel, sur une machine virtuelle, en utilisant un abonnement d’essai de Microsoft Defender pour IoT. Vous verrez la configuration optimale à mettre en place pour une personne qui souhaite tester Microsoft Defender pour IoT avant de s’inscrire et de l’intégrer à son environnement.

En utilisant des environnements virtuels ainsi que les logiciels nécessaires pour créer un capteur, Defender pour IoT vous permet d’effectuer les opérations suivantes :

- Utiliser une supervision passive et sans agent du réseau pour obtenir un inventaire complet de l’ensemble de vos appareils IoT et OT, de leurs détails et de la façon dont ils communiquent, sans aucun impact sur le réseau IoT et OT.

- Identifier les risques et les vulnérabilités dans votre environnement IoT et OT Par exemple, identifiez les appareils non corrigés, les ports ouverts et les applications et connexions non autorisées. Vous pouvez également identifier les modifications apportées aux configurations des appareils, au code PLC et au microprogramme.

- Détecter les activités anormales ou non autorisées grâce au renseignement sur les menaces et à l’analytique comportementale spécialisés tenant compte de l’IoT et de l’OT. Vous pouvez même détecter les menaces avancées ignorées par les IOC statiques, comme les programmes malveillants zero-day, les programmes malveillants sans fichier et les tactiques « Living off the land ».

- Effectuez une intégration à Microsoft Sentinel pour avoir une vue d’ensemble de votre organisation. Implémentez une gouvernance unifiée de la sécurité de l’IoT et de l’OT avec intégration à vos workflows existants, notamment des outils tiers tels que Splunk, IBM QRadar et ServiceNow.

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Effectuer l’intégration à Microsoft Defender pour IoT
> * Télécharger l’ISO du capteur virtuel
> * Créer une machine virtuelle pour le capteur
> * Installer le logiciel du capteur virtuel
> * Configurer un port SPAN
> * Intégrer et activer le capteur virtuel

## <a name="prerequisites"></a>Prérequis

- Autorisations : niveau **Propriétaires d’abonnements** ou **Contributeurs d’abonnement** Azure.

- Au moins un appareil à superviser connecté à un port SPAN sur le commutateur.

- Hyperviseur VMware (ESXi 5.5 ou ultérieur) ou Hyper-V (Windows 10 Professionnel ou Entreprise) installé et opérationnel.

- Un compte Azure. Si vous n’avez pas encore de compte Azure, vous pouvez [créer un compte Azure gratuit dès aujourd’hui](https://azure.microsoft.com/free/).

## <a name="onboard-with-microsoft-defender-for-iot"></a>Effectuer l’intégration à Microsoft Defender pour IoT

Pour commencer à utiliser Microsoft Defender pour IoT, vous devez disposer d’un abonnement Microsoft Azure. Si vous n’avez pas encore d’abonnement, vous pouvez [créer un compte Azure gratuit dès aujourd’hui](https://azure.microsoft.com/free/).

Pour évaluer Defender pour IoT, vous pouvez utiliser un abonnement d’essai. L’essai est valable pendant 30 jours et prend en charge jusqu’à 1 000 appareils validés. L’essai vous permet de déployer un capteur virtuel sur votre réseau. Utilisez les capteurs pour surveiller le trafic, analyser des données, générer des alertes, en savoir plus sur les risques et les vulnérabilités du réseau, et bien plus encore. L’essai vous permet également de déployer une console de gestion locale virtuelle pour voir les informations agrégées générées par le capteur.

**Pour intégrer un abonnement à Microsoft Defender pour IoT** :

1. Accédez au [portail Azure](https://portal.azure.com/).

1. Recherchez et sélectionnez **Microsoft Defender pour IoT**.

1. Sélectionnez **Intégrer un abonnement**.

    :::image type="content" source="media/tutorial-onboarding/onboard-subscription.png" alt-text="Capture d’écran montrant la sélection du bouton Intégrer l’abonnement dans la page de démarrage.":::

1. Dans la page Tarifs, sélectionnez **Démarrer avec un essai**.

   :::image type="content" source="media/tutorial-onboarding/start-with-trial.png" alt-text="Capture d’écran du bouton Démarrer avec un essai, utilisé pour ouvrir la fenêtre d’essai.":::

1. Sélectionnez un abonnement dans le volet abonnement d’essai intégré, puis sélectionnez **Évaluer**.

1. Confirmez votre évaluation.

## <a name="download-the-iso-for-the-virtual-sensor"></a>Télécharger l’ISO du capteur virtuel

Les appliances virtuelles ont des spécifications minimales qui doivent être respectées pour le capteur et la console de gestion. Le tableau suivant présente les spécifications requises pour le capteur en fonction de votre environnement.

### <a name="virtual-sensor"></a>Capteur virtuel

| Type | Entreprise | Enterprise | SMB |
|--|--|--|--|
| Processeurs virtuels | 32 | 8 | 4 |
| Mémoire | 32 Go | 32 Go | 8 Go |
| Stockage | 5,6 To | 1,8 To | 500 Go |

**Pour télécharger le fichier ISO pour le capteur virtuel** :

1. Accédez au [portail Azure](https://portal.azure.com/).

1. Recherchez et sélectionnez **Microsoft Defender pour IoT**.

1. Dans la page de démarrage, sélectionnez l’onglet **Capteur**.

1. Sélectionnez **Télécharger**.

    :::image type="content" source="media/tutorial-onboarding/sensor-download.png" alt-text="Capture d’écran de l’onglet Capteur, avec le bouton Télécharger utilisé pour télécharger le fichier ISO du capteur virtuel.":::

## <a name="create-a-virtual-machine-for-the-sensor"></a>Créer une machine virtuelle pour le capteur

Le capteur virtuel prend en charge les options de déploiement de VMware et Hyper-V. Avant de commencer l’installation, assurez-vous de disposer des éléments suivants :

- Hyperviseur VMware (ESXi 5.5 ou ultérieur) ou Hyper-V (Windows 10 Professionnel ou Entreprise) installé et opérationnel.

- Ressources matérielles disponibles pour la machine virtuelle.

- Fichier d’installation ISO pour le capteur Microsoft Defender pour IoT

- Assurez-vous que l’hyperviseur fonctionne.

### <a name="create-the-virtual-machine-for-the-sensor-with-esxi"></a>Créer la machine virtuelle pour le capteur avec ESXi

**Pour créer la machine virtuelle pour le capteur (ESXi)**  :

1. Connectez-vous à ESXi, choisissez le **magasin de données** approprié et sélectionnez **Navigateur de magasin de données**.

1. **Chargez** l’image, puis sélectionnez **Fermer**.

1. Accédez à **Machines virtuelles**, puis sélectionnez **Créer/inscrire une machine virtuelle**.

1. Sélectionnez **Créer une nouvelle machine virtuelle**, puis sélectionnez **Suivant**.

1. Ajoutez un nom de capteur et choisissez :

   - Compatibilité : **&lt;version ESXi la plus récente&gt;**

   - Famille de système d’exploitation invité : **Linux**

   - Version du système d’exploitation invité : **Ubuntu Linux (64 bits)**

1. Sélectionnez **Suivant**.

1. Choisissez le magasin de données approprié et sélectionnez **Suivant**.

1. Modifiez les paramètres du matériel virtuel en fonction de l’[architecture](#download-the-iso-for-the-virtual-sensor) requise.

1. Pour **Lecteur CD/DVD 1**, sélectionnez **Fichier ISO de magasin de données** et choisissez le fichier ISO que vous avez chargé précédemment.

1. Sélectionnez **Suivant** > **Terminer**.

1. Mettez sous tension la machine virtuelle et ouvrez une console.

### <a name="create-a-virtual-machine-for-the-sensor-with-hyper-v"></a>Créer une machine virtuelle pour le capteur avec Hyper-V

Cette procédure décrit comment créer une machine virtuelle à l’aide d’Hyper-V.

**Pour créer une machine virtuelle avec Hyper-V** :

1. Créez un disque virtuel dans Hyper-V Manager.

1. Sélectionnez **format = VHDX**.

1. Sélectionnez **type = Développement dynamique**.

1. Entrez le nom et l’emplacement du disque dur virtuel.

1. Entrez la taille requise (en fonction de l’[architecture](#download-the-iso-for-the-virtual-sensor)).

1. Vérifiez le résumé et sélectionnez **Terminer**.

1. Dans le menu **Actions**, créez une machine virtuelle.

1. Entrez un nom pour la machine virtuelle.

1. Sélectionnez **Spécifier la génération** > **Génération 1**.

1. Spécifiez l’allocation de mémoire (en fonction de l’[architecture](#download-the-iso-for-the-virtual-sensor)) et cochez la case de la mémoire dynamique.

1. Configurez la carte réseau en fonction de la topologie de réseau de votre serveur.

1. Connectez le VHDX créé précédemment à la machine virtuelle.

1. Vérifiez le résumé et sélectionnez **Terminer**.

1. Cliquez avec le bouton droit sur la nouvelle machine virtuelle, puis sélectionnez **Paramètres**.

1. Sélectionnez **Ajouter du matériel** et ajouter une nouvelle carte réseau.

1. Sélectionnez le commutateur virtuel qui sera connecté au réseau de gestion du capteur.

1. Allouez des ressources de processeur (selon l’[architecture](#download-the-iso-for-the-virtual-sensor)).

1. Connectez l’image ISO de la console de gestion à un lecteur de DVD virtuel.

1. Démarrez la machine virtuelle.

1. Dans le menu **Actions**, sélectionnez **Connexion** pour poursuivre l’installation du logiciel.

## <a name="install-the-virtual-sensor-software-with-esxi-or-hyper-v"></a>Installer le logiciel du capteur virtuel avec ESXi ou Hyper-V

Vous pouvez utiliser ESXi ou Hyper-V pour installer le logiciel pour le capteur virtuel.

**Pour installer le logiciel sur le capteur virtuel** :

1. Ouvrez la console de la machine virtuelle.

1. La machine virtuelle démarre à partir de l’image ISO et l’écran de sélection de la langue s’affiche. Sélectionnez **Français**.

1. Sélectionnez l’[architecture](#download-the-iso-for-the-virtual-sensor) requise.

1. Définissez le profil de l’appareil et les propriétés du réseau :

    | Paramètre | Configuration |
    | ----------| ------------- |
    | **Profil matériel** | En fonction de l’[architecture](#download-the-iso-for-the-virtual-sensor) requise. |
    | **Interface de gestion** | **ens192** |
    | **Paramètres réseau (fournis par le client)** | **Adresse IP du réseau de gestion :** <br/>**Masque de sous-réseau :** <br>**Nom d’hôte de l’appliance :** <br/>**DNS :** <br/>**Passerelle par défaut :** <br/>**Interfaces d’entrée :**|
    | **Interfaces de pont :** | Il n’est pas nécessaire de configurer l’interface de pont. Cette option est réservée à des cas d’usage particuliers. |

1. Entrez **O** pour accepter les paramètres.

1. Les informations d’identification de connexion sont automatiquement générées et présentées. Copiez le nom d’utilisateur et le mot de passe en lieu sûr, car vous en aurez besoin pour vous connecter à votre appareil et le gérer. Le nom d’utilisateur et le mot de passe ne seront pas présentés de nouveau.

    - **Support** : Utilisateur administratif pour la gestion des utilisateurs.

    - **CyberX** : Équivalent de la racine pour accéder à l’appliance.

1. L’appliance redémarre.

1. Accédez au capteur au moyen de l’adresse IP précédemment configurée : `https://ip_address`.

### <a name="post-installation-validation"></a>Validation après installation

Pour valider l’installation d’une appliance physique, vous devez effectuer de nombreux tests.

La validation est disponible pour l’utilisateur **Support** et l’utilisateur **CyberX**.

**Pour accéder à l’outil de post-validation** :

1. Connectez-vous au capteur.

1. Sélectionnez **Paramètres système** dans le volet latéral gauche.

1. Sélectionnez le bouton :::image type="icon" source="media/tutorial-onboarding/system-statistics-icon.png" border="false":::.

    :::image type="content" source="media/tutorial-onboarding/system-health-check-screen.png" alt-text="Capture d’écran de la vérification de l’intégrité du système." lightbox="media/tutorial-onboarding/system-health-check-screen-expanded.png":::

Pour la validation post-installation, vous devez effectuer des tests pour vérifier que le système est en cours d’exécution, que vous utilisez la bonne version et que toutes les interfaces d’entrée configurées durant le processus d’installation sont en cours d’exécution.

**Pour vérifier que le système est en cours d’exécution** :

1. Sélectionnez **Appliance**, puis vérifiez que chaque élément de ligne indique `Running` et que la ligne inférieure indique `System is up`.

1. Sélectionnez **Version** et vérifiez que la bonne version est indiquée.

1. Sélectionnez **ifconfig** pour afficher les paramètres des interfaces physiques de l’appliance et vérifiez qu’ils sont corrects.

## <a name="configure-a-span-port"></a>Configurer un port SPAN

Un commutateur virtuel n’a pas de fonctionnalités de mise en miroir. Toutefois, vous pouvez utiliser le mode de proximité dans un environnement de commutateur virtuel. Le mode de proximité est un mode de fonctionnement, ainsi qu’une technique de sécurité, de supervision et d’administration, défini au niveau du commutateur virtuel, ou des ports. Par défaut, le mode de proximité est désactivé. Lorsque le mode de proximité est activé, les interfaces réseau de la machine virtuelle qui se trouvent dans le même groupe de ports utilisent le mode de proximité pour afficher tout le trafic réseau qui traverse ce commutateur virtuel. Vous pouvez implémenter une solution de contournement avec ESXi ou Hyper-V.

:::image type="content" source="media/tutorial-onboarding/purdue-model.png" alt-text="Capture d’écran de l’emplacement du capteur dans votre architecture.":::

### <a name="configure-a-span-port-with-esxi"></a>Configurer un port SPAN avec ESXi

**Pour configurer un port SPAN avec ESXi** :

1. Ouvrez les propriétés du commutateur virtuel.

1. Sélectionnez **Ajouter**.

1. Sélectionnez **Machine virtuelle** > **Suivant**.

1. Insérez une étiquette réseau **Réseau SPAN**, sélectionnez **ID VLAN** > **Tout**, puis sélectionnez **Suivant**.

1. Sélectionnez **Terminer**.

1. Sélectionnez **Réseau SPAN** > **Modifier*.

1. Sélectionnez **Sécurité** et vérifiez que la stratégie **Mode de proximité** est définie sur **Accepter le mode**.

1. Sélectionnez **OK**, puis sélectionnez **Fermer** pour fermer les propriétés du commutateur virtuel.

1. Ouvrez les propriétés de la **machine virtuelle XSense**.

1. Pour **Carte réseau 2**, sélectionnez le réseau **SPAN**.

1. Sélectionnez **OK**.

1. Connectez-vous au capteur et vérifiez que la mise en miroir fonctionne.

### <a name="configure-a-span-port-with-hyper-v"></a>Configurer un port SPAN avec Hyper-V

Avant de commencer, vous devez :

- Vérifier qu’aucune instance d’appliance virtuelle n’est en cours d’exécution.

- Activer SPAN sur le port de données, et non sur le port de gestion.

- Vérifier que la configuration SPAN du port de données n’est pas définie avec une adresse IP.

**Pour configurer un port SPAN avec Hyper-V** :

1. Ouvrez le gestionnaire de commutateur virtuel.

1. Dans la liste des commutateurs virtuels, sélectionnez **Nouveau commutateur réseau virtuel** > **Externe** comme type de carte réseau avec SPAN dédiée.

    :::image type="content" source="media/tutorial-onboarding/new-virtual-network.png" alt-text="Capture d’écran de la sélection de Nouveau réseau virtuel et de l’option Externe avant la création du commutateur virtuel.":::

1. Sélectionnez **Créer un commutateur virtuel**.

1. Sous Type de connexion, sélectionnez **Réseau externe**.

1. Vérifiez que la case **Autoriser le système d’exploitation de gestion à partager cette carte réseau** est cochée.

   :::image type="content" source="media/tutorial-onboarding/external-network.png" alt-text="Sélectionnez Réseau externe, puis Autoriser le système d’exploitation de gestion à partager cette carte réseau.":::

1. Sélectionnez **OK**.

#### <a name="attach-a-span-virtual-interface-to-the-virtual-switch"></a>Attacher une interface virtuelle SPAN au commutateur virtuel

Vous pouvez attacher une interface virtuelle SPAN au commutateur virtuel via Windows PowerShell ou via le gestionnaire Hyper-V.

**Pour attacher une interface virtuelle SPAN au commutateur virtuel avec PowerShell** :

1. Sélectionnez le commutateur virtuel SPAN qui vient d’être ajouté, puis ajoutez une nouvelle carte réseau avec la commande suivante :

    ```bash
    ADD-VMNetworkAdapter -VMName VK-C1000V-LongRunning-650 -Name Monitor -SwitchName vSwitch_Span
    ```

1. Activez la mise en miroir des ports pour l’interface sélectionnée comme destination SPAN avec la commande suivante :

    ```bash
    Get-VMNetworkAdapter -VMName VK-C1000V-LongRunning-650 | ? Name -eq Monitor | Set-VMNetworkAdapter -PortMirroring Destination
    ```

    | Paramètre | Description |
    |--|--|
    | VK-C1000V-LongRunning-650 | Nom VA CPPM |
    |vSwitch_Span |Nom du commutateur virtuel SPAN récemment ajouté |
    |Superviser |Nom de l’adaptateur récemment ajouté |

1. Sélectionnez **OK**.

Ces commandes définissent l’adaptateur qui vient d’être ajouté avec le nom `Monitor`. Si vous utilisez le gestionnaire Hyper-V, le matériel de l’adaptateur récemment ajouté est défini avec le nom `Network Adapter`.

**Pour attacher une interface virtuelle SPAN au commutateur virtuel avec le gestionnaire Hyper-V** :

1. Sous la liste du matériel, sélectionnez **Carte réseau**.

1. Dans le champ Commutateur virtuel, sélectionnez **vSwitch_Span**.

    :::image type="content" source="media/tutorial-onboarding/vswitch-span.png" alt-text="Capture d’écran montrant la sélection des options suivantes sur l’écran du commutateur virtuel.":::

1. Dans la liste du matériel, sous la liste déroulante des cartes réseau, sélectionnez **Fonctionnalités avancées**.

1. Dans la section Mise en miroir des ports, sélectionnez **Destination** comme mode de mise en miroir pour la nouvelle interface virtuelle.

    :::image type="content" source="media/tutorial-onboarding/destination.png" alt-text="Capture d’écran montrant les sélections nécessaires pour configurer le mode de mise en miroir.":::

1. Sélectionnez **OK**.

#### <a name="enable-microsoft-ndis-capture-extensions-for-the-virtual-switch"></a>Activer les extensions Microsoft NDIS Capture pour le commutateur virtuel

Les extensions Microsoft NDIS Capture doivent être activées pour le nouveau commutateur virtuel.

**Pour activer les extensions Microsoft NDIS Capture pour le commutateur virtuel récemment ajouté** :

1. Ouvrez le gestionnaire de commutateur virtuel sur l’hôte Hyper-V.

1. Dans la liste des commutateurs virtuels, développez le nom du commutateur virtuel `vSwitch_Span` et sélectionnez **Extensions**.

1. Dans le champ Extensions du commutateur, sélectionnez **Microsoft NDIS Capture**.

    :::image type="content" source="media/tutorial-onboarding/microsoft-ndis.png" alt-text="Capture d’écran montrant la sélection du menu Extensions et l’activation de Microsoft NDIS Capture.":::

1. Sélectionnez **OK**.

#### <a name="set-the-mirroring-mode-on-the-external-port"></a>Définir le mode de mise en miroir sur le port externe

Le mode de mise en miroir doit être défini sur le port externe du nouveau commutateur virtuel à utiliser comme source.

Vous devez configurer le commutateur virtuel Hyper-V (vSwitch_Span) pour transférer tout le trafic qui arrive au port source externe vers la carte réseau virtuelle que vous avez configurée comme destination.

Utilisez les commandes PowerShell suivantes pour définir le port de commutateur virtuel externe en mode miroir source :

```bash
$ExtPortFeature=Get-VMSystemSwitchExtensionPortFeature -FeatureName "Ethernet Switch Port Security Settings"
$ExtPortFeature.SettingData.MonitorMode=2
Add-VMSwitchExtensionPortFeature -ExternalPort -SwitchName vSwitch_Span -VMSwitchExtensionFeature $ExtPortFeature
```

| Paramètre | Description |
|--|--|
| vSwitch_Span | Nom du commutateur virtuel SPAN récemment ajouté. |
| MonitorMode=2 | Source |
| MonitorMode=1 | Destination |
| MonitorMode=0 | Aucun |

Utilisez la commande PowerShell suivante pour vérifier l’état du mode de supervision :

```bash
Get-VMSwitchExtensionPortFeature -FeatureName "Ethernet Switch Port Security Settings" -SwitchName vSwitch_Span -ExternalPort | select -ExpandProperty SettingData
```

| Paramètre | Description |
|--|--|
| vSwitch_Span | Nom du commutateur virtuel SPAN récemment ajouté |
## <a name="onboard-and-activate-the-virtual-sensor"></a>Intégrer et activer le capteur virtuel

Avant de commencer à utiliser votre capteur Defender pour IoT, vous devez intégrer le capteur virtuel créé à votre abonnement Azure et télécharger le fichier d’activation du capteur virtuel pour activer le capteur.

### <a name="onboard-the-virtual-sensor"></a>Intégrer le capteur virtuel

**Pour intégrer le capteur virtuel :**

1. Accédez à [Defender pour IoT : Prise en main](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started) dans le portail Azure.

1. Sélectionnez **Intégrer un capteur**.

   :::image type="content" source="media/tutorial-onboarding/onboard-a-sensor.png" alt-text="Capture d’écran montrant la sélection de l’option Intégrer le capteur pour démarrer le processus d’intégration du capteur.":::

1. Donnez un nom au capteur.

   Nous vous recommandons d’inclure l’adresse IP du capteur dans le nom ou d’utiliser un nom facilement identifiable. En nommant votre capteur de cette manière, vous faciliterez son suivi.

1. Sélectionnez un abonnement dans la liste déroulante.

    :::image type="content" source="media/tutorial-onboarding/name-subscription.png" alt-text="Capture d’écran montrant l’entrée d’un nom explicite et la connexion de votre capteur à un abonnement.":::

1. Choisissez un mode de connexion du capteur à l’aide du bouton bascule **Connecté au cloud**. Si le bouton bascule est activé, le capteur est connecté au cloud. Si le bouton bascule est désactivé, le capteur est géré localement.

   - **Capteurs connectés au cloud** : Les informations détectées par le capteur s’affichent dans la console du capteur. Les informations sur les alertes sont transmises par le biais d’un hub IoT et peuvent être partagées avec d’autres services Azure, comme Microsoft Sentinel. En outre, les packages de renseignement sur les menaces peuvent être envoyés (push) aux capteurs à partir de Defender pour IoT. À l’inverse, lorsque le capteur n’est pas connecté au cloud, vous devez télécharger les packages de renseignement sur les menaces, puis les charger sur les capteurs de votre entreprise. Pour autoriser Defender pour IoT à envoyer (push) des packages aux capteurs, activez le bouton bascule **Mises à jour automatiques du renseignement sur les menaces**. Pour plus d’informations, consultez [Recherche et packages de renseignement sur les menaces](how-to-work-with-threat-intelligence-packages.md).

      Pour les capteurs connectés au cloud, le nom défini lors de l’intégration est le nom qui apparaît dans la console de capteur. Vous ne pouvez pas modifier ce nom directement à partir de la console. Pour les capteurs gérés localement, le nom appliqué pendant l’intégration est stocké dans Azure, mais peut être mis à jour dans la console du capteur.

   - **Capteurs gérés localement** : Les informations détectées par les capteurs s’affichent dans leur console. Si vous travaillez sur un réseau en air gap et que vous souhaitez une vue unifiée de toutes les informations détectées par plusieurs capteurs gérés localement, utilisez la console de gestion locale.

1. Sélectionnez le site auquel vous souhaitez associer votre capteur dans un hub IoT. Le hub IoT servira de passerelle entre ce capteur et Microsoft Defender pour IoT. Définissez le nom complet et la zone. Vous pouvez également ajouter des étiquettes descriptives. Le nom complet, la zone et les étiquettes constituent des entrées descriptives de la page [Afficher les capteurs intégrés](how-to-manage-sensors-on-the-cloud.md#view-onboarded-sensors).

1. Sélectionnez **Inscription**.

### <a name="download-the-sensor-activation-file"></a>Télécharger le fichier d’activation du capteur

Une fois l’inscription du capteur terminée, vous pouvez télécharger un fichier d’activation pour le capteur. Le fichier d’activation de capteur contient des instructions relatives au mode d’administration du capteur. Le fichier d’activation que vous téléchargez est propre à chaque capteur que vous déployez. L’utilisateur qui se connecte à la console du capteur pour la première fois charge le fichier d’activation sur le capteur.

**Pour télécharger un fichier d’activation :**

1. Dans la page **Capteur intégré**, sélectionnez **Inscrire**.

1. Sélectionnez **Télécharger le fichier d’activation**.

1. Rendez le fichier accessible à l’utilisateur qui se connecte à la console du capteur pour la première fois.

### <a name="sign-in-and-activate-the-sensor"></a>Se connecter et activer le capteur

**Pour vous connecter et activer :**

1. Accédez à la console du capteur à partir de votre navigateur à l’aide de l’adresse IP définie lors de l’installation.

    :::image type="content" source="media/tutorial-onboarding/azure-defender-for-iot-sensor-log-in-screen.png" alt-text="Capture d’écran du capteur Microsoft Defender pour IoT.":::

1. Entrez les informations d’identification définies lors de l’installation du capteur.

1. Une fois que vous êtes connecté, la boîte de dialogue **Activation** s’ouvre. Sélectionnez **Charger** et accédez au fichier d’activation que vous avez téléchargé durant l’intégration du capteur.

   :::image type="content" source="media/tutorial-onboarding/activation-upload-screen-with-upload-button.png" alt-text="Capture d’écran montrant le bouton Charger et l’accès au fichier d’activation.":::

1. Acceptez les conditions générales.

1. Sélectionnez **Activer**. La boîte de dialogue Certificat SSL/TLS s’ouvre.

1. Définissez un nom de certificat.

1. Chargez les fichiers de clé et CRT.

1. Entrez une phrase secrète et chargez un fichier PEM le cas échéant.

1. Sélectionnez **Suivant**. L’écran de validation s’ouvre. Par défaut, la validation entre la console de gestion et les capteurs connectés est activée.

1. Désactivez le bouton bascule **Activer la validation à l’échelle du système** pour désactiver la validation. Nous vous recommandons d’activer la validation.

1. Sélectionnez **Enregistrer**.  

Vous devrez peut-être actualiser votre écran après avoir chargé le certificat signé par une autorité de certification.

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment configurer des [appliances supplémentaires](how-to-install-software.md#about-defender-for-iot-appliances).

Découvrez l’[architecture sans agent](architecture.md).
