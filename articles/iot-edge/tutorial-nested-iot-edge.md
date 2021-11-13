---
title: 'Tutoriel : Créer une hiérarchie d’appareils IoT Edge - Azure IoT Edge'
description: Ce tutoriel montre comment créer une structure hiérarchique d’appareils IoT Edge à l’aide de passerelles.
author: v-tcassi
ms.author: v-tcassi
ms.date: 2/26/2021
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
monikerRange: '>=iotedge-2020-11'
ms.openlocfilehash: 5c204a07a0457852d2faefacdd77740f147b9d88
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130225988"
---
# <a name="tutorial-create-a-hierarchy-of-iot-edge-devices"></a>Tutoriel : Créer une hiérarchie d’appareils IoT Edge

[!INCLUDE [iot-edge-version-202011](../../includes/iot-edge-version-202011.md)]

Déployez des nœuds Azure IoT Edge sur des réseaux organisés en couches hiérarchiques. Chaque couche d’une hiérarchie est un appareil de passerelle qui gère les messages et les requêtes des appareils situés dans la couche du dessous.

Vous pouvez structurer une hiérarchie d’appareils de sorte que seule la couche supérieure soit connectée au cloud, et que les couches inférieures puissent uniquement communiquer avec les couches Nord et Sud adjacentes. Cette structure en couches réseau est la base de la plupart des réseaux industriels, qui respectent la [norme ISA-95](https://en.wikipedia.org/wiki/ANSI/ISA-95).

L’objectif de ce tutoriel est de créer une hiérarchie d’appareils IoT Edge, qui simule un environnement de production simplifié. À la fin, vous déploierez le [module Simulated Temperature Sensor](https://azuremarketplace.microsoft.com/marketplace/apps/azure-iot.simulated-temperature-sensor) sur un appareil de couche inférieure sans accès à Internet en téléchargeant des images conteneur à travers la hiérarchie.

Pour atteindre cet objectif, ce tutoriel vous guide dans la création d’une hiérarchie d’appareils IoT Edge, le déploiement de conteneurs de runtime IoT Edge sur vos appareils et la configuration locale de vos appareils. Dans ce tutoriel, vous allez utiliser un outil de configuration automatisé pour :

> [!div class="checklist"]
>
> * Créer et définir les relations dans une hiérarchie d’appareils IoT Edge.
> * Configurer le runtime IoT Edge sur les appareils de votre hiérarchie.
> * Installer des certificats cohérents dans votre hiérarchie d’appareils.
> * Ajouter des charges de travail aux appareils de votre hiérarchie.
> * Utiliser le [module Proxy d’API IoT Edge](https://azuremarketplace.microsoft.com/marketplace/apps/azure-iot.azureiotedge-api-proxy?tab=Overview) pour router en toute sécurité le trafic HTTP sur un port unique à partir de vos appareils de couche inférieure.

>[!TIP]
>Ce tutoriel comprend un mélange d’étapes manuelles et automatisées pour fournir une présentation des fonctionnalités IoT Edge imbriquées.
>
>Si vous souhaitez une présentation entièrement automatisée de la configuration d’une hiérarchie d’appareils IoT Edge, vous pouvez suivre l’[exemple Azure IoT Edge pour IoT industriel](https://aka.ms/iotedge-nested-sample) scripté. Ce scénario scripté déploie des machines virtuelles Azure en tant qu’appareils préconfigurés pour simuler un environnement de fabrique.
>
>Si vous souhaitez examiner en détail les étapes manuelles de création et de gestion d’une hiérarchie d’appareils IoT Edge, consultez le [guide pratique sur les hiérarchies d’appareils de passerelle IoT Edge](how-to-connect-downstream-iot-edge-device.md).

Dans ce tutoriel, les couches réseau suivantes sont définies :

* **Couche supérieure** : les appareils IoT Edge de cette couche peuvent se connecter directement au cloud.

* **Couches inférieures** : les appareils IOT Edge se trouvant dans des couches en dessous de la couche supérieure ne peuvent pas se connecter directement au cloud. Ils doivent passer par un ou plusieurs appareils IoT Edge intermédiaires pour envoyer et recevoir des données.

Par souci de simplicité, ce tutoriel utilise une hiérarchie à deux appareils illustrée ci-dessous. Un appareil, l’**appareil de couche supérieure** dans la hiérarchie, peut se connecter directement au cloud. Cet appareil sera également appelé **appareil parent**. L’autre appareil, l’**appareil de couche inférieure** dans la hiérarchie, ne peut pas se connecter directement au cloud. Vous pouvez ajouter d’autres appareils de couche inférieure pour représenter votre environnement de production, selon les besoins. Les appareils dans les couches inférieures sont également appelés **appareils enfants**.

![Structure de la hiérarchie du tutoriel, avec deux appareils : l’appareil de couche supérieure et l’appareil de couche inférieure](./media/tutorial-nested-iot-edge/tutorial-hierarchy-diagram.png)

## <a name="prerequisites"></a>Prérequis

Pour créer une hiérarchie d’appareils IoT Edge, vous aurez besoin des éléments suivants :

* Un ordinateur (Windows ou Linux) avec une connectivité Internet
* Un compte Azure avec un abonnement valide. Si vous n’avez pas [d’abonnement Azure](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing), créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.
* Un [hub IoT](../iot-hub/iot-hub-create-through-portal.md) de niveau gratuit ou standard dans Azure
* Interpréteur de commandes Bash dans Azure Cloud Shell utilisant Azure CLI v2.3.1 avec l’extension Azure IoT v0.10.6 ou supérieure installée. Ce tutoriel utilise [Azure Cloud Shell](../cloud-shell/overview.md). Si vous ne connaissez pas Azure Cloud Shell, [consultez ce guide de démarrage rapide pour plus d’informations](./quickstart-linux.md#prerequisites).
  * Pour afficher vos versions actuelles des modules et extensions Azure CLI, exécutez la commande [az version](/cli/azure/reference-index?#az_version).
* Un appareil Linux à configurer en tant qu’appareil IoT Edge pour chaque appareil dans votre hiérarchie. Ce tutoriel utilise deux appareils. Si vous n’avez pas d’appareils disponibles, vous pouvez créer des machines virtuelles Azure pour chaque appareil de votre hiérarchie à l’aide de la commande ci-dessous.

   Remplacez le texte de l’espace réservé dans la commande suivante et exécutez-le à deux reprises, une fois pour chaque machine virtuelle. Chaque machine virtuelle a besoin d’un préfixe DNS unique, qui lui sert également de nom. Le préfixe DNS doit être conforme à l’expression régulière suivante : `[a-z][a-z0-9-]{1,61}[a-z0-9]`.

   ```azurecli
   az deployment group create \
    --resource-group <REPLACE_WITH_YOUR_RESOURCE_GROUP> \
    --template-uri "https://raw.githubusercontent.com/Azure/iotedge-vm-deploy/1.2.0/edgeDeploy.json" \
    --parameters dnsLabelPrefix='<REPLACE_WITH_UNIQUE_DNS_FOR_VIRTUAL_MACHINE>' \
    --parameters adminUsername='azureuser' \
    --parameters authenticationType='sshPublicKey' \
    --parameters adminPasswordOrKey="$(< ~/.ssh/id_rsa.pub)" \
    --query "properties.outputs.[publicFQDN.value, publicSSH.value]" -o tsv
   ```

   La machine virtuelle utilise des clés SSH pour authentifier les utilisateurs. Si vous n’êtes pas familiarisé avec la création et l’utilisation de clés SSH, vous pouvez suivre les [instructions relatives aux paires de clés publiques-privées SSH pour les machines virtuelles Linux dans Azure](../virtual-machines/linux/mac-create-ssh-keys.md).

   IoT Edge version 1.2 est préinstallé avec ce modèle ARM, ce qui évite d’avoir à installer manuellement les ressources sur les machines virtuelles. Si vous installez IoT Edge sur vos propres appareils, consultez [Installer Azure IoT Edge pour Linux (version 1.2)](how-to-provision-single-device-linux-symmetric.md) ou [Mettre à jour IoT Edge vers la version 1.2](how-to-update-iot-edge.md#special-case-update-from-10-or-11-to-12).

   La création réussie d’une machine virtuelle à l’aide de ce modèle ARM permet de générer le handle `SSH` et le nom de domaine complet (`FQDN`) de votre machine virtuelle. Dans la mesure où vous allez utiliser le handle SSH et le FQDN (nom de domaine complet) ou l’adresse IP de chaque machine virtuelle pour le processus de configuration dans les étapes à venir, gardez une trace de ces informations. Un exemple de sortie est illustré ci-dessous.

   >[!TIP]
   >Vous pouvez également trouver l’adresse IP et le FQDN sur le portail Azure. Pour l’adresse IP, accédez à votre liste de machines virtuelles, puis notez le contenu du champ **Adresse IP publique**. Pour le FQDN, accédez à la page de vue d’ensemble de chaque machine virtuelle, puis recherchez le champ **Nom DNS**.

   ![Au moment où elle est créée, la machine virtuelle génère un fichier JSON, qui contient son handle SSH](./media/tutorial-nested-iot-edge/virtual-machine-outputs.png)

* Vérifiez que les ports suivants sont ouverts en entrée pour tous les appareils, à l’exception de l’appareil de la couche la plus basse : 443, 5671, 8883 :
  * 443 : Utilisé entre les hubs de périphérie parent et enfant pour les appels d’API REST et pour extraire des images conteneur Docker.
  * 5671, 8883 : Utilisé pour AMQP et MQTT.

  Pour plus d’informations, consultez [Guide pratique pour ouvrir des ports sur une machine virtuelle avec le portail Azure](../virtual-machines/windows/nsg-quickstart-portal.md).

## <a name="configure-your-iot-edge-device-hierarchy"></a>Configurer votre hiérarchie d’appareils IoT Edge

### <a name="create-a-hierarchy-of-iot-edge-devices"></a>Créer une hiérarchie d’appareils IoT Edge

Les appareils IoT Edge composent les couches de votre hiérarchie. Ce tutoriel crée une hiérarchie de deux appareils IoT Edge : l’**appareil de couche supérieure**, et son enfant, l’**appareil de couche inférieure**. Vous pouvez créer des appareils enfants supplémentaires selon les besoins.

Pour créer et configurer votre hiérarchie d’appareils IoT Edge, vous allez utiliser l’outil `iotedge-config`. Cet outil simplifie la configuration de la hiérarchie en automatisant et en condensant plusieurs étapes en deux :

1. Préparation de la configuration du cloud et de la configuration de chaque appareil, ce qui inclut :

   * Création d’appareils dans votre hub IoT
   * Définition des relations parent-enfant pour autoriser la communication entre les appareils
   * Génération d’une chaîne de certificats pour chaque appareil afin d’établir une communication sécurisée entre eux
   * Génération de fichiers config pour chaque appareil

1. Installation de la configuration de chaque appareil, ce qui inclut :

   * Installation de certificats sur chaque appareil
   * Application des fichiers config pour chaque appareil

L’outil `iotedge-config` ​​permet également de déployer automatiquement les modules sur votre appareil IoT Edge.

Pour utiliser l’outil `iotedge-config` afin de créer et configurer votre hiérarchie, suivez les étapes ci-dessous dans Azure CLI :

1. Dans [Azure Cloud Shell](https://shell.azure.com/), créez un répertoire pour les ressources de votre tutoriel :

   ```bash
   mkdir nestedIotEdgeTutorial
   ```

1. Téléchargez la version de l’outil de configuration et des modèles de configuration :

   ```bash
   cd ~/nestedIotEdgeTutorial
   wget -O iotedge_config.tar "https://github.com/Azure-Samples/iotedge_config_cli/releases/download/latest/iotedge_config_cli.tar.gz"
   tar -xvf iotedge_config.tar
   ```

   Cela entraîne la création du dossier `iotedge_config_cli_release` dans le répertoire du tutoriel.

   Le fichier de modèle utilisé pour créer la hiérarchie d’appareils est le fichier `iotedge_config.yaml` situé dans `~/nestedIotEdgeTutorial/iotedge_config_cli_release/templates/tutorial`. Dans le même répertoire, vous trouverez `deploymentLowerLayer.json`, un fichier de déploiement JSON contenant des instructions relatives aux modules à déployer sur votre **appareil de couche inférieure**. Le fichier `deploymentTopLayer.json` est identique, mais il correspond à votre **appareil de couche supérieure**, car les modules déployés sur chaque appareil ne sont pas les mêmes. Le fichier `device_config.toml` est un modèle pour les configurations d’appareils IoT Edge. Il est utilisé pour générer automatiquement les bundles de configurations des appareils de votre hiérarchie.

   Si vous souhaitez voir le code source et les scripts de l’outil `iotedge-config`, consultez le [dépôt Azure-Samples sur GitHub](https://github.com/Azure-Samples/iotedge_config_cli).

1. Ouvrez le modèle de configuration du tutoriel, et modifiez-le avec vos informations :

   ```bash
   code ~/nestedIotEdgeTutorial/iotedge_config_cli_release/templates/tutorial/iotedge_config.yaml
   ```

   Dans la section **iothub**, remplissez les champs `iothub_hostname` et `iothub_name` avec vos informations. Vous trouverez ces informations dans la page de vue d’ensemble de votre hub IoT sur le portail Azure.

   Dans la section facultative **certificates**, vous pouvez remplir les champs en indiquant les chemins absolus de votre certificat et de votre clé. Si vous laissez ces champs vides, le script génère automatiquement des certificats de test auto-signés que vous pouvez utiliser. Si vous ne savez pas comment les certificats sont utilisés dans un scénario de passerelle, consultez la [section relative aux certificats dans le guide pratique](how-to-connect-downstream-iot-edge-device.md#prepare-certificates).

   Dans la section **configuration**, `template_config_path` est le chemin du modèle `device_config.toml` utilisé pour créer les configurations des appareils. Le champ `default_edge_agent` détermine ce que les appareils de couche inférieure de l’image de l’agent Edge vont tirer (pull), et à partir d’où.

   Dans la section **edgedevices**, pour un scénario de production, vous pouvez modifier l’arborescence hiérarchique afin de refléter la structure souhaitée. Pour les besoins de ce tutoriel, acceptez l’arborescence par défaut. Pour chaque appareil, il existe un champ `device_id` dans lequel vous pouvez nommer vos appareils. Il existe également le champ `deployment`, qui spécifie le chemin du fichier JSON de déploiement pour cet appareil.

   Vous pouvez également inscrire manuellement les appareils IoT Edge dans votre hub IoT à partir du portail Azure, d’Azure Cloud Shell ou de Visual Studio Code. Pour en savoir plus, consultez [le début du guide de bout en bout sur le provisionnement manuel d’un appareil IoT Edge Linux](how-to-provision-single-device-linux-symmetric.md#register-your-device).

   Vous pouvez également définir les relations parent-enfant manuellement. Pour en savoir plus, consultez la section [créer une hiérarchie de passerelle](how-to-connect-downstream-iot-edge-device.md#create-a-gateway-hierarchy) du guide pratique.

   ![La section edgedevices du fichier config vous permet de définir votre hiérarchie](./media/tutorial-nested-iot-edge/hierarchy-config-sample.png)

1. Enregistrez et fermez le fichier :

   `CTRL + S`, `CTRL + Q`

1. Créez un répertoire de sorties pour les bundles de configurations dans le répertoire des ressources du tutoriel :

   ```bash
   mkdir ~/nestedIotEdgeTutorial/iotedge_config_cli_release/outputs
   ```

1. Accédez au répertoire `iotedge_config_cli_release` et exécutez l’outil pour créer votre hiérarchie d’appareils IoT Edge :

   ```bash
   cd ~/nestedIotEdgeTutorial/iotedge_config_cli_release
   ./iotedge_config --config ~/nestedIotEdgeTutorial/iotedge_config_cli_release/templates/tutorial/iotedge_config.yaml --output ~/nestedIotEdgeTutorial/iotedge_config_cli_release/outputs -f
   ```

   Quand l’indicateur `--output` est défini, l’outil crée les certificats d’appareil, les bundles de certificats ainsi qu’un fichier journal dans le répertoire de votre choix. Quand l’indicateur `-f` est défini, l’outil recherche automatiquement les appareils IoT Edge existants dans votre hub IoT, et les supprime, pour éviter les erreurs et nettoyer le hub.

   L’outil de configuration crée les appareils IoT Edge et configure les relations parent-enfant entre eux. Si vous le souhaitez, il peut créer les certificats que vos appareils doivent utiliser. Si des chemins de fichiers JSON de déploiement sont fournis, l’outil crée automatiquement ces déploiements sur les appareils, mais cela n’est pas obligatoire. Enfin, l’outil va générer les bundles de configurations des appareils, et les placer dans le répertoire de sortie. Pour avoir un aperçu complet des étapes suivies par l’outil de configuration, consultez le fichier journal dans le répertoire de sortie.

   ![Le script affiche une topologie de votre hiérarchie au moment de l’exécution](./media/tutorial-nested-iot-edge/successful-setup-tool-run.png)

Vérifiez à nouveau que la sortie de topologie du script est correcte. Une fois que vous êtes satisfait de la structure de votre hiérarchie, vous êtes prêt à continuer.

### <a name="configure-the-iot-edge-runtime"></a>Configurer le runtime IoT Edge

Outre l’approvisionnement de vos appareils, les étapes de configuration établissent une communication approuvée entre les appareils dans votre hiérarchie à l’aide des certificats que vous avez créés précédemment. Les étapes commencent également à établir la structure réseau de votre hiérarchie. L’appareil de couche supérieure conserve la connectivité Internet, ce qui lui permet d’extraire des images pour son exécution à partir du cloud, tandis que les appareils de couche inférieure passent par l’appareil de couche supérieure pour accéder à ces images.

Pour configurer le runtime IoT Edge, vous devez appliquer les bundles de configurations créés par le script d’installation à vos appareils. Les configurations de l’**appareil de couche supérieure** et d’un **appareil de couche inférieure** étant légèrement différentes, veillez à appliquer le bon fichier config à chaque appareil.

1. Chaque appareil a besoin de son bundle de configurations correspondant. Vous pouvez utiliser une clé USB ou un programme de [copie sécurisée de fichiers](https://www.ssh.com/ssh/scp/) pour déplacer les bundles de configurations sur chaque appareil.

   Veillez à envoyer le bundle de configurations approprié à chaque appareil.

   ```bash
   scp <PATH_TO_CONFIGURATION_BUNDLE> <USER>@<VM_IP_OR_FQDN>:~
   ```

   Le `:~` signifie que le dossier de configuration sera placé dans le répertoire de base de la machine virtuelle.

1. Connectez-vous à votre machine virtuelle pour appliquer le bundle de configurations à l’appareil :

   ```bash
   ssh <USER>@<VM_IP_OR_FQDN>
   ```

1. Sur chaque appareil, décompressez le bundle de configurations. Vous devez d’abord installer le fichier zip :

   ```bash
   sudo apt install zip
   unzip ~/<PATH_TO_CONFIGURATION_BUNDLE>/<CONFIGURATION_BUNDLE>.zip
   ```

1. Sur chaque appareil, appliquez le bundle de configurations :

   ```bash
   sudo ./install.sh
   ```

   Sur l’**appareil de couche supérieure**, vous êtes invité à entrer le nom d’hôte. Sur l’**appareil de couche inférieure**, vous êtes invité à entrer le nom d’hôte et le nom d’hôte du parent. Indiquez l’IP ou le FQDN (nom de domaine complet) approprié à chaque invite. Vous pouvez utiliser l’un ou l’autre, mais soyez cohérent dans votre choix sur tous les appareils. La sortie du script d’installation est illustrée ci-dessous.

   Si vous souhaitez examiner de plus près les modifications apportées au fichier config de votre appareil, consultez la [section Configurer IoT Edge sur les appareils dans le guide pratique](how-to-connect-downstream-iot-edge-device.md#configure-iot-edge-on-devices).

  ![L’installation des bundles de configurations va entraîner la mise à jour des fichiers config.toml sur votre appareil et le redémarrage automatique de tous les services IoT Edge](./media/tutorial-nested-iot-edge/configuration-install-output.png)

Si vous avez correctement suivi les étapes ci-dessus, vous pouvez vérifier que vos appareils sont configurés correctement.

Effectuez les vérifications de configuration et de connectivité sur vos appareils. Pour l’**appareil de couche supérieure** :

   ```bash
   sudo iotedge check
   ```

Pour l’**appareil de couche inférieure**, l’image de diagnostic doit être passée manuellement dans la commande :

   ```bash
   sudo iotedge check --diagnostics-image-name <parent_device_fqdn_or_ip>:443/azureiotedge-diagnostics:1.2
   ```

Sur l’**appareil de couche supérieure**, attendez-vous à voir une sortie avec plusieurs évaluations réussies. Vous pouvez voir des avertissements concernant les stratégies de journaux et, en fonction de votre réseau, les stratégies DNS.

<!-- Add pic after GA -->
<!-- KEEP! A sample output of the `iotedge check` is shown below: -->

<!-- KEEP! ![Sample configuration and connectivity results](./media/tutorial-nested-iot-edge/configuration-and-connectivity-check-results.png) -->

Une fois que vous êtes satisfait de vos configurations sur chaque appareil, vous êtes prêt à continuer.

## <a name="deploy-modules-to-your-devices"></a>Déployer des modules sur vos appareils

Les déploiements de modules sur vos appareils ont été automatiquement générés au moment de la création des appareils. L’outil `iotedge-config-cli` ​​a alimenté les fichiers JSON de déploiement pour les **appareils de couches supérieure et inférieure** après leur création. Les déploiements de modules étaient en attente pendant que vous configuriez le runtime IoT Edge sur chaque appareil. Une fois que vous avez configuré le runtime, les déploiements sur l’**appareil de couche supérieure** ont commencé. Une fois ces déploiements effectués, l’**appareil de couche inférieure** peut utiliser le module **Proxy d’API IoT Edge** pour tirer les images nécessaires.

Dans [Azure Cloud Shell](https://shell.azure.com/), vous pouvez consulter le fichier JSON de déploiement de l’**appareil de couche supérieure** pour déterminer quels sont les modules déployés sur l’appareil :

   ```bash
   cat ~/nestedIotEdgeTutorial/iotedge_config_cli_release/templates/tutorial/deploymentTopLayer.json
   ```

En plus des modules de runtime **Agent IoT Edge** et **Hub IoT Edge**, l’**appareil de couche supérieure** reçoit les modules **Registre Docker** et **Proxy d’API IoT Edge**.

Le module **Registre Docker** pointe vers un registre Azure Container Registry existant. Dans ce cas, `REGISTRY_PROXY_REMOTEURL` pointe vers Microsoft Container Registry. Par défaut, le **registre Docker** écoute sur le port 5000.

Le module **Proxy d’API IoT Edge** route les requêtes HTTP vers d’autres modules, ce qui permet aux appareils de couche inférieure de tirer (pull) des images conteneur ou de pousser (push) des objets blob vers le stockage. Dans ce tutoriel, il communique sur le port 443 et est configuré pour envoyer des demandes de tirage (pull requests) d’images conteneur Docker à votre module **Registre Docker** sur le port 5000. De plus, toutes les demandes de chargement du service Stockage Blob sont routées vers le module AzureBlobStorageonIoTEdge sur le port 11002. Pour plus d’informations sur le module **Proxy d’API IoT Edge** et sur sa configuration, consultez le [guide pratique](how-to-configure-api-proxy-module.md) du module.

Pour découvrir comment créer un déploiement comme celui-ci via le portail Azure ou Azure Cloud Shell, consultez la [section consacrée aux appareils de couche supérieure dans le guide pratique](how-to-connect-downstream-iot-edge-device.md#deploy-modules-to-top-layer-devices).

Dans [Azure Cloud Shell](https://shell.azure.com/), vous pouvez consulter le fichier JSON de déploiement de l’**appareil de couche inférieure** pour déterminer quels sont les modules déployés sur l’appareil :

   ```bash
   cat ~/nestedIotEdgeTutorial/iotedge_config_cli_release/templates/tutorial/deploymentLowerLayer.json
   ```

Vous pouvez voir sous `systemModules` que les modules de runtime de l’**appareil de couche inférieure** sont configurés pour être tirés à partir de `$upstream:443`, au lieu de `mcr.microsoft.com`, comme c’était le cas pour l’**appareil de couche supérieure**. L’**appareil de couche inférieure** envoie des demandes d’image Docker au module **Proxy d’API IoT Edge** sur le port 443, car il ne peut pas tirer directement les images du cloud. L’autre module déployé sur l’**appareil de couche inférieure**, le module **Simulated Temperature Sensor**, effectue également sa demande d’image auprès de `$upstream:443`.

Pour découvrir comment créer un déploiement comme celui-ci via le portail Azure ou Azure Cloud Shell, consultez la [section consacrée aux appareils de couche inférieure dans le guide pratique](how-to-connect-downstream-iot-edge-device.md#deploy-modules-to-lower-layer-devices).

Vous pouvez voir l’état des modules à l’aide de la commande :

   ```azurecli
   az iot hub module-twin show --device-id <edge_device_id> --module-id '$edgeAgent' --hub-name <iot_hub_name> --query "properties.reported.[systemModules, modules]"
   ```

   Cette commande renvoie toutes les propriétés edgeAgent signalées. Voici quelques exemples utiles pour superviser l’état de l’appareil : *état du runtime*, *heure de début du runtime*, *heure de la dernière sortie du runtime*, *nombre de redémarrages du runtime*.

Vous pouvez également voir l’état des modules sur le [portail Azure](https://ms.portal.azure.com/). Accédez à la section **IoT Edge** de votre hub IoT pour voir les appareils et les modules.

Une fois que vous êtes satisfait des déploiements des modules, vous êtes prêt à continuer.

## <a name="view-generated-data"></a>Afficher les données générées

Le module **Capteur de température simulé** que vous avez envoyé (push) génère des exemples de données d’environnement. Il envoie des messages qui incluent la température et l’humidité ambiantes, la température et la pression de la machine, ainsi qu’un horodatage.

Vous pouvez aussi afficher ces messages par le biais d’[Azure Cloud Shell](https://shell.azure.com/) :

   ```azurecli-interactive
   az iot hub monitor-events -n <iothub_name> -d <lower-layer-device-name>
   ```

## <a name="troubleshooting"></a>Dépannage

Exécutez la commande `iotedge check` pour vérifier la configuration et résoudre les problèmes.

Vous pouvez exécuter `iotedge check` dans une hiérarchie imbriquée, même si les machines enfants n’ont pas d’accès direct à Internet.

Quand vous exécutez `iotedge check` à partir de la couche inférieure, le programme essaie de tirer l’image du parent via le port 443.

```bash
sudo iotedge check --diagnostics-image-name $upstream:443/azureiotedge-diagnostics:1.2
```

La valeur `azureiotedge-diagnostics` est extraite du registre de conteneurs lié au module registre. Dans ce tutoriel, elle est définie par défaut sur https://mcr.microsoft.com:.

| Name | Valeur |
| - | - |
| `REGISTRY_PROXY_REMOTEURL` | `https://mcr.microsoft.com` |

Si vous utilisez un registre de conteneurs privé, vérifiez que toutes les images (IoTEdgeAPIProxy, edgeAgent, edgeHub, Simulated Temperature Sensor et diagnostics) sont présentes dans le registre de conteneurs.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Vous pouvez supprimer les ressources Azure et les configurations locales que vous avez créées dans cet article pour éviter des frais.

Pour supprimer les ressources :

1. Connectez-vous au [portail Azure](https://portal.azure.com), puis sélectionnez **Groupes de ressources**.

2. Sélectionnez le nom du groupe de ressources contenant vos ressources de test de IoT Edge. 

3. Passez en revue la liste des ressources contenues dans votre groupe de ressources. Si vous souhaitez toutes les supprimer, vous pouvez sélectionner **Supprimer le groupe de ressources**. Si vous souhaitez en supprimer seulement quelques-unes, vous pouvez cliquer sur chaque ressource pour les supprimer individuellement. 

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez configuré deux appareils IoT Edge en tant que passerelles et défini l’un des deux comme appareil parent de l’autre. Vous avez vu ensuite comment tirer une image conteneur sur l’appareil enfant via une passerelle à l’aide du module Proxy d’API IoT Edge. Pour en savoir plus, consultez le [guide pratique sur l’utilisation du module proxy](how-to-configure-api-proxy-module.md).

Pour en savoir plus sur l’utilisation des passerelles pour créer des couches hiérarchiques d’appareils IoT Edge, consultez le [guide pratique sur la connexion d’appareils IoT Edge en aval](how-to-connect-downstream-iot-edge-device.md).

Vous pouvez passer à d’autres didacticiels pour savoir comment Azure IoT Edge peut trouver d’autres solutions pour votre entreprise.

> [!div class="nextstepaction"]
> [Déployer un modèle Azure Machine Learning en tant que module](tutorial-deploy-machine-learning.md)
