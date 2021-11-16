---
title: Mettre à jour la version IoT Edge sur les appareils - Azure IoT Edge | Microsoft Docs
description: Guide pratique pour mettre à jour des appareils IoT Edge afin qu’ils exécutent les dernières versions du démon de sécurité et le runtime IoT Edge
keywords: ''
author: kgremban
ms.author: kgremban
ms.date: 06/15/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 90a03b86e54c214fb5dd17f11ea01247b7b77e9b
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132057210"
---
# <a name="update-iot-edge"></a>Mettre à jour IoT Edge

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

Chaque fois que le service IoT Edge publiera de nouvelles versions, vous pourrez mettre à jour vos appareils IoT Edge pour obtenir les dernières fonctionnalités et améliorations de la sécurité. Cet article fournit des informations sur la façon de mettre à jour vos appareils IoT Edge quand une nouvelle version est disponible.

Deux composants d’un appareil IoT Edge doivent être mis à jour si vous souhaitez passer à une version plus récente. Le premier est le démon de sécurité qui s’exécute sur l’appareil et démarre les modules du runtime au démarrage de l’appareil. Le démon de sécurité ne peut être mis à jour qu’à partir de l’appareil lui-même. Le second composant est le runtime, constitué des modules de l’agent IoT Edge et du hub IoT Edge. Selon la façon dont vous structurez votre déploiement, le runtime peut être mis à jour à partir de l’appareil ou à distance.

Pour rechercher la dernière version d’Azure IoT Edge, consultez [Versions d’Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases).

## <a name="update-the-security-daemon"></a>Mettre à jour le démon de sécurité

Le démon de sécurité IoT Edge est un composant natif qui doit être mis à jour à l’aide du gestionnaire de package sur l’appareil IoT Edge.

Vérifiez la version du démon de sécurité qui s’exécute sur votre appareil à l’aide de la commande `iotedge version`. Si vous utilisez IoT Edge pour Linux sur Windows, vous devez vous connecter par protocole SSH à la machine virtuelle Linux pour vérifier la version.

# <a name="linux"></a>[Linux](#tab/linux)

>[!IMPORTANT]
>Si vous mettez à jour un appareil de la version 1.0 ou 1.1 vers la version 1.2, il existe des différences entre les processus d’installation et de configuration qui nécessitent des étapes supplémentaires. Pour plus d’informations, reportez-vous aux étapes décrites plus loin dans cet article : [Cas particulier : mise à jour de la version 1.0 ou 1.1 vers la version 1.2](#special-case-update-from-10-or-11-to-12).

Sur des appareils Linux x64, utilisez apt-get ou votre gestionnaire de package approprié pour mettre à jour le démon de sécurité vers la dernière version.

Procurez-vous la dernière configuration du référentiel auprès de Microsoft :

* **Ubuntu Server 18.04** :

   ```bash
   curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
   ```

* **Raspberry Pi OS Stretch** :

   ```bash
   curl https://packages.microsoft.com/config/debian/stretch/multiarch/prod.list > ./microsoft-prod.list
   ```

Copiez la liste générée.

   ```bash
   sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   ```

Installez la clé publique Microsoft GPG.

   ```bash
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
   ```

Mettez à jour apt.

   ```bash
   sudo apt-get update
   ```

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

Vérifiez les versions d’IoT Edge disponibles.

   ```bash
   apt list -a iotedge
   ```

Si vous souhaitez effectuer la mise à jour vers la version la plus récente du démon de sécurité, utilisez la commande suivante, qui met également à jour **libiothsm-std** vers la dernière version :

   ```bash
   sudo apt-get install iotedge
   ```

Si vous souhaitez mettre à jour vers une version spécifique du démon de sécurité, spécifiez la version dans la sortie de liste apt. Chaque fois que **iotedge** est mis à jour, il tente automatiquement de mettre à jour le package **libiothsm-std** vers la dernière version, ce qui peut entraîner un conflit de dépendance. Si vous n’allez pas utiliser la version la plus récente, veillez à cibler les deux packages avec la même version. Par exemple, la commande suivante installe une version spécifique de la version 1.1 :

   ```bash
   sudo apt-get install iotedge=1.1.1 libiothsm-std=1.1.1
   ```

Si la version que vous souhaitez installer n’est pas disponible par le biais de apt-get, vous pouvez utiliser curl pour cibler n’importe quelle version du référentiel [IoT Edge](https://github.com/Azure/azure-iotedge/releases). Quelle que soit la version que vous souhaitez installer, localisez les fichiers **libiothsm-STD** et **iotedge** correspondant à votre appareil. Pour chaque fichier, cliquez avec le bouton droit de la souris sur le lien du fichier, puis copiez l’adresse du lien. Utilisez l’adresse du lien pour installer les versions spécifiques de ces composants :

```bash
curl -L <libiothsm-std link> -o libiothsm-std.deb && sudo apt-get install ./libiothsm-std.deb
curl -L <iotedge link> -o iotedge.deb && sudo apt-get install ./iotedge.deb
```
<!-- end 1.1 -->
:::moniker-end

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

Vérifiez les versions d’IoT Edge disponibles.

   ```bash
   apt list -a aziot-edge
   ```

Si vous souhaitez effectuer une mise à jour vers la version la plus récente d’IoT Edge, utilisez la commande suivante qui met également à jour le service d’identité vers la dernière version :

   ```bash
   sudo apt-get install aziot-edge
   ```
<!-- end 1.2 -->
:::moniker-end

# <a name="linux-on-windows"></a>[Linux sur Windows](#tab/linuxonwindows)

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

>[!NOTE]
>Actuellement, il n’existe pas de prise en charge d’IoT Edge version 1.2 s’exécutant sur des machines virtuelles Linux sur Windows.
>
>Pour consulter les étapes de mise à jour d’IoT Edge pour Linux sous Windows, consultez [IoT Edge 1.1](?view=iotedge-2018-06&preserve-view=true&tabs=linuxonwindows).

:::moniker-end
<!-- end 1.2 -->

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

>[!IMPORTANT]
>Si vous mettez à jour un appareil de la préversion publique d’IoT Edge pour Linux sur Windows vers la version en disponibilité générale, vous devez désinstaller et réinstaller Azure IoT Edge.
>
>Pour savoir si vous utilisez actuellement la préversion publique, accédez à **Paramètres** > **Applications** sur votre appareil Windows. Recherchez **Azure IoT Edge** dans la liste des applications et des fonctionnalités. Si la version indiquée est 1.0.x, vous exécutez la préversion publique. Désinstallez l’application, puis [installez et provisionnez IoT Edge pour Linux sur Windows](how-to-provision-single-device-linux-on-windows-symmetric.md). Si la version indiquée est 1.1.x, vous exécutez la version en disponibilité générale et vous pouvez recevoir des mises à jour par le biais de Microsoft Update.

>[!IMPORTANT]
>Si vous mettez à jour un appareil Windows Server de référence SKU antérieure à la version 1.1.2110.03111 de IoT Edge pour Linux sur Windows vers la dernière version disponible, vous devez opérer une migration manuelle.
>
>La mise à jour [1.1.2110.0311](https://github.com/Azure/iotedge-eflow/releases/tag/1.1.2110.03111) a introduit une modification de la technologie de machine virtuelle (de HCS en VMMS) utilisée pour les déploiements EFLOW de Windows Server. Vous pouvez opérer la migration de machine virtuelle en procédant comme suit :
> 1. À l’aide de Microsoft Update, téléchargez et installez la mise à jour 1.1.2110.03111 (comme n’importe quelle autre mise à jour EFLOW, vous n’avez pas besoin d’étapes manuelles tant que les mises à jour EFLOW sont activées).
> 2. Une fois la mise à jour EFLOW terminée, ouvrez une session PowerShell avec des privilèges élevés.
> 3. Exécutez le script de migration :
>  ```powershell
>   Migrate-EflowVmFromHcsToVmms
>   ```
>
> Remarque : les nouvelles installations EFLOW de msi 1.1.2110.0311 sur des références SKU de Windows Server entraînant des déploiements EFLOW utilisant la technologie VMMS, aucune migration n’est nécessaire.

Avec IoT Edge pour Linux sur Windows, IoT Edge s’exécute sur une machine virtuelle Linux hébergée sur un appareil Windows. Cette machine virtuelle est préinstallée avec IoT Edge, et vous ne pouvez pas mettre à jour ou modifier manuellement les composants IoT Edge. Au lieu de cela, la machine virtuelle est managée avec Microsoft Update pour maintenir automatiquement à jour les composants.

Pour trouver la dernière version d’Azure IoT Edge pour Linux sur Windows, consultez [Versions d’EFLOW](https://aka.ms/AzEFLOW-Releases).


Afin de recevoir les mises à jour d’IoT Edge pour Linux sur Windows, l’hôte Windows doit être configuré pour recevoir les mises à jour d’autres produits Microsoft. Vous pouvez activer cette option en procédant comme suit :

1. Ouvrez **Paramètres** sur l’hôte Windows.

1. Sélectionnez **Mise à jour et sécurité**.

1. Sélectionnez **Options avancées**.

1. Basculez le bouton *Recevoir les mises à jour d’autres produits Microsoft lorsque vous mettez à jour Windows* sur **Activé**.

:::moniker-end
<!-- end 1.1 -->

# <a name="windows"></a>[Windows](#tab/windows)

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

>[!NOTE]
>Actuellement, il n’existe pas de prise en charge d’IoT Edge version 1.2 s’exécutant sur des appareils Windows.
>
>Pour consulter les étapes de mise à jour d’IoT Edge pour Linux sous Windows, consultez [IoT Edge 1.1](?view=iotedge-2018-06&preserve-view=true&tabs=windows).

:::moniker-end
<!-- end 1.2 -->

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

Avec IoT Edge pour Windows, IoT Edge s’exécute directement sur l’appareil Windows.

Utilisez la commande `Update-IoTEdge` pour mettre à jour le démon de sécurité. Le script extrait automatiquement la dernière version du démon de sécurité.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Update-IoTEdge
```

La commande Update-IoTEdge supprime et met à jour le démon de sécurité de l’appareil, ainsi que les deux images conteneur du runtime. Le fichier config.yaml est conservé sur l’appareil, de même que les données du moteur de conteneur Moby. Le fait de conserver les informations de configuration évite d’avoir à indiquer de nouveau la chaîne de connexion ou le service Device Provisioning de votre appareil lors du processus de mise à jour.

Si vous souhaitez mettre à jour vers une version spécifique du démon de sécurité, recherchez la version du canal de distribution 1.1 que vous souhaitez cibler à partir de [Versions d’Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases). Dans cette version, téléchargez le fichier **Microsoft-Azure-IoTEdge.cab**. Utilisez ensuite le paramètre `-OfflineInstallationPath` pour pointer vers l’emplacement du fichier local. Par exemple :

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Update-IoTEdge -OfflineInstallationPath <absolute path to directory>
```

>[!NOTE]
>Le paramètre `-OfflineInstallationPath` recherche un fichier nommé **Microsoft-Azure-IoTEdge.cab** dans le répertoire fourni. Renommez le fichier pour supprimer le suffixe d’architecture, le cas échéant.

Si vous souhaitez mettre à jour un appareil hors connexion, recherchez la version que vous souhaitez cibler parmi les [versions Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases). Dans cette version, téléchargez les fichiers *IoTEdgeSecurityDaemon.ps1* et *Microsoft-Azure-IoTEdge.cab*. Il est important d’utiliser le script PowerShell de la même version que le fichier .cab que vous utilisez, car les fonctionnalités changent pour prendre en charge les fonctionnalités de chaque version.

Si le fichier .cab que vous avez téléchargé est doté d’un suffixe d’architecture, renommez le fichier uniquement **Microsoft-Azure-IoTEdge.cab**.

Pour mettre à jour avec des composants hors connexion, [effectuez un appel de source de type « dot source »](/powershell/module/microsoft.powershell.core/about/about_scripts#script-scope-and-dot-sourcing) de la copie locale du script PowerShell. Ensuite, utilisez le paramètre `-OfflineInstallationPath` dans la commande `Update-IoTEdge` et spécifiez le chemin absolu du répertoire des fichiers. Par exemple,

```powershell
. <path>\IoTEdgeSecurityDaemon.ps1
Update-IoTEdge -OfflineInstallationPath <path>
```

Pour plus d’informations sur les options de mise à jour, utilisez la commande `Get-Help Update-IoTEdge -full` ou consultez [Script PowerShell pour IoT Edge pour conteneurs Windows](reference-windows-scripts.md).

:::moniker-end
<!-- end 1.1 -->

---

## <a name="update-the-runtime-containers"></a>Mettre à jour les conteneurs du runtime

La façon dont vous mettez à jour les conteneurs de l’agent IoT Edge et du hub IoT Edge diffère selon que vous utilisez des étiquettes évolutives (par exemple, 1.1) ou des étiquettes spécifiques (par exemple, 1.1.1) dans votre déploiement.

Vérifiez la version des modules de l’agent IoT Edge et du hub IoT Edge sur votre appareil à l’aide des commandes `iotedge logs edgeAgent` ou `iotedge logs edgeHub`. Si vous utilisez IoT Edge pour Linux sur Windows, vous devez vous connecter par protocole SSH à la machine virtuelle Linux pour vérifier les versions des modules d’exécution.

  ![Rechercher la version du conteneur dans les journaux d’activité](./media/how-to-update-iot-edge/container-version.png)

### <a name="understand-iot-edge-tags"></a>Comprendre les étiquettes IoT Edge

Les images de l’agent IoT Edge et du hub IoT Edge sont marquées avec la version IoT Edge à laquelle elles sont associées. Il existe deux façons d’utiliser des étiquettes avec les images de runtime :

* **Étiquettes évolutives** : utilisez uniquement les deux premières valeurs du numéro de version pour obtenir la dernière image qui correspond à ces chiffres. Par exemple, la version 1.1 est mise à jour lorsqu’une nouvelle mise en production pointe vers la dernière version 1.1.x. Si le runtime du conteneur sur votre appareil IoT Edge réextrait l’image, les modules de runtime sont mis à jour vers la dernière version. Les déploiements à partir du portail Azure adoptent par défaut des étiquettes évolutives. *Cette approche est conseillée à des fins de développement.*

* **Étiquettes spécifiques** : utilisez les trois valeurs du numéro de version pour définir explicitement la version de l’image. Par exemple, la version 1.1.0 ne change pas après sa mise en production initiale. Vous pouvez déclarer un nouveau numéro de version dans le manifeste de déploiement quand vous êtes prêt à effectuer une mise à jour. *Cette approche est conseillée à des fins de production.*

### <a name="update-a-rolling-tag-image"></a>Mettre à jour une image avec des étiquettes évolutives

Si vous utilisez des étiquettes évolutives dans votre déploiement (par exemple, mcr.microsoft.com/azureiotedge-hub:**1.1**), vous devez forcer le runtime du conteneur sur votre appareil à extraire la dernière version de l’image.

Supprimez la version locale de l’image de votre appareil IoT Edge. Sur les ordinateurs Windows, la désinstallation du démon de sécurité entraîne également la suppression des images du runtime, ce qui permet de sauter cette étape.

```bash
docker rmi mcr.microsoft.com/azureiotedge-hub:1.1
docker rmi mcr.microsoft.com/azureiotedge-agent:1.1
```

Vous devrez peut-être utiliser l’indicateur `-f` (forcer) pour supprimer les images.

Le service IoT Edge extrait les dernières versions des images de runtime et les démarre automatiquement sur votre appareil à nouveau.

### <a name="update-a-specific-tag-image"></a>Mettre à jour une image avec des étiquettes spécifiques

Si vous utilisez des étiquettes spécifiques dans votre déploiement (par exemple, mcr.microsoft.com/azureiotedge-hub:**1.1.1**), il vous suffit de mettre à jour l’étiquette dans votre manifeste de déploiement et d’appliquer les modifications à votre appareil.

1. Dans l’IoT Hub du portail Azure, sélectionnez votre appareil IoT Edge, puis sélectionnez **Définir les modules**.

1. Dans la section **Modules IoT Edge**, sélectionnez **Paramètres d'exécution**.

   ![Configurer les paramètres d'exécution](./media/how-to-update-iot-edge/configure-runtime.png)

1. Dans **Paramètres d’exécution**, mettez à jour la valeur de l’**image** pour **Edge Hub** avec la version souhaitée. Ne sélectionnez pas encore **Enregistrer**.

   ![Mettre à jour la version de l’image Edge Hub.](./media/how-to-update-iot-edge/runtime-settings-edgehub.png)

1. Réduisez les paramètres **Edge Hub**, ou faites défiler la liste vers le bas et mettez à jour la valeur de l’**image** pour l’**agent Edge** avec la même version souhaitée.

   ![Mettre à jour la version de l’agent Edge Hub](./media/how-to-update-iot-edge/runtime-settings-edgeagent.png)

1. Sélectionnez **Enregistrer**.

1. Sélectionnez **Vérifier + Créer**, vérifiez le déploiement, puis sélectionnez **Créer**.

## <a name="special-case-update-from-10-or-11-to-12"></a>Cas particulier : mise à jour de la version 1.0 ou 1.1 vers la version 1.2

>[!NOTE]
>Si vous utilisez des conteneurs Windows ou IoT Edge pour Linux sur Windows, cette section sur les cas particuliers ne s’applique pas.

Depuis la version 1.2, le service IoT Edge utilise un nouveau nom de package et présente quelques différences dans les processus d’installation et de configuration. Si vous avez un appareil IoT Edge exécutant la version 1.0 ou 1.1, suivez ces instructions pour savoir comment effectuer la mise à jour vers la version 1.2.

>[!NOTE]
>Actuellement, il n’existe aucune prise en charge d’IoT Edge version 1.2 s’exécutant sur des appareils Windows.

Voici quelques-unes des principales différences entre la version 1.2 et les versions antérieures :

* Le nom du package est passé de **iotedge** à **aziot-Edge**.
* Le package **libiothsm-STD** n’est plus utilisé. Si vous avez utilisé le package standard fourni dans le cadre de la mise en production d’IoT Edge, vos configurations peuvent être transférées vers la nouvelle version. Si vous avez utilisé une autre implémentation de libiothsm-std, tous les certificats fournis par l’utilisateur, tel le certificat d’identité d’appareil, l’autorité de certification de l’appareil et le bundle de confiance doivent être reconfigurés.
* Un nouveau service d’identité, **aziot-identity-service** , a été introduit dans le cadre de la mise en production 1.2. Ce service gère l’approvisionnement et la gestion des identités pour IoT Edge et pour d’autres composants d’appareil qui doivent communiquer avec IoT Hub, comme [Device Update pour IoT Hub](../iot-hub-device-update/understand-device-update.md).
* Le fichier de configuration par défaut a un nouveau nom et un nouvel emplacement. Les informations de configuration de votre appareil qui se trouvaient auparavant dans `/etc/iotedge/config.yaml` sont désormais supposées se trouver dans `/etc/aziot/config.toml` par défaut. La commande `iotedge config import` peut aider à migrer les informations de configuration de l’ancien emplacement et de l’ancienne syntaxe.
  * La commande d’importation ne peut pas détecter ou modifier les règles d’accès au module de plateforme sécurisée (TPM) d’un appareil. Si votre appareil utilise l’attestation TPM, vous devez mettre à jour manuellement le fichier/etc/udev/rules.d/tpmaccess.rules pour accorder l’accès au service aziottpm. Pour plus d’informations, consultez [Donner à IoT Edge l’accès au TPM](how-to-auto-provision-simulated-device-linux.md?view=iotedge-2020-11&preserve-view=true#give-iot-edge-access-to-the-tpm).
* L’API de charge de travail dans la version 1.2 enregistre les secrets chiffrés dans un nouveau format. Si vous effectuez une mise à niveau à partir d’une version antérieure vers la version 1.2, la clé de chiffrement principale existante est importée. L’API de charge de travail peut lire les secrets enregistrés dans le format précédent à l’aide de la clé de chiffrement importée. Toutefois, l’API de charge de travail ne peut pas écrire de secrets chiffrés dans l’ancien format. Une fois qu’une clé secrète est chiffrée à nouveau par un module, elle est enregistrée dans le nouveau format. Les secrets chiffrés dans la version 1.2 ne peuvent pas être lus par le même module dans la version 1.1. Si vous conservez des données chiffrées dans un dossier ou un volume monté par l’hôte, créez toujours une copie de sauvegarde des données *avant* d’effectuer la mise à niveau afin de conserver la possibilité de repasser à une version antérieure si nécessaire.

Avant d’automatiser tout processus de mise à jour, vérifiez qu’il fonctionne sur des machines de test.

Lorsque vous êtes prêt, procédez comme suit pour mettre à jour IoT Edge sur vos appareils :

1. Procurez-vous la dernière configuration du référentiel auprès de Microsoft :

   * **Ubuntu Server 18.04** :

     ```bash
     curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
     ```

   * **Raspberry Pi OS Stretch** :

     ```bash
     curl https://packages.microsoft.com/config/debian/stretch/multiarch/prod.list > ./microsoft-prod.list
     ```

2. Copiez la liste générée.

   ```bash
   sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   ```

3. Installez la clé publique Microsoft GPG.

   ```bash
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
   ```

4. Mettez à jour apt.

   ```bash
   sudo apt-get update
   ```

5. Désinstallez la version précédente d’IoT Edge, en laissant vos fichiers de configuration en place.

   ```bash
   sudo apt-get remove iotedge
   ```

6. Installez la version la plus récente d’IoT Edge, ainsi que le service d’identité IoT.

   ```bash
   sudo apt-get install aziot-edge
   ```

7. Importez votre ancien fichier config. yaml dans son nouveau format, puis appliquez les informations de configuration.

   ```bash
   sudo iotedge config import
   ```

À pérsent que le service IoT Edge s’exécutant sur vos appareils a été mis à jour, suivez les étapes de cet article pour également [Mettre à jour les conteneurs du runtime](#update-the-runtime-containers).

## <a name="special-case-update-to-a-release-candidate-version"></a>Cas particulier : mise à jour vers une version Release Candidate

>[!NOTE]
>Si vous utilisez des conteneurs Windows ou IoT Edge pour Linux sur Windows, cette section sur les cas particuliers ne s’applique pas.

Azure IoT Edge publie régulièrement de nouvelles versions du service IoT Edge. Avant chaque version stable, il y a une ou plusieurs versions Release Candidate (RC). Les versions RC incluent toutes les fonctionnalités planifiées de la version, mais sont encore sujettes aux processus de tests et de validation. Si vous souhaitez tester très tôt une nouvelle fonctionnalité, vous pouvez installer un version RC et envoyer des commentaires via GitHub.

Les versions Release Candidate suivent la même convention de numérotation que les versions publiées, mais **-rc** et un nombre incrémentiel sont ajoutés à la fin. Vous pouvez voir les versions Release Candidate dans la même liste [Versions d’Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases) que les versions stables. Par exemple, recherchez **1.2.0-rc4**, une des versions Release Candidate publiées avant **1.2.0**. Vous pouvez également voir que les versions RC sont marquées avec des étiquettes **préversion**.

Les modules agent et hub d’IoT Edge ont des versions RC qui sont marquées selon la même convention. For example, **mcr.microsoft.com/azureiotedge-hub:1.2.0-rc4**.

Tout comme les préversions, les versions Release Candidate ne sont pas incluses comme la version la plus récente ciblée par les programmes d’installation réguliers. Au lieu de cela, vous devez cibler manuellement les ressources de la version RC que vous souhaitez tester. Dans la plupart des cas, l’installation ou la mise à jour vers une version RC sont identiques au ciblage d’une autre version d’IoT Edge.

Reportez-vous aux sections de cet article pour savoir comment mettre à jour un appareil IoT Edge vers une version spécifique du démon de sécurité ou des modules d’exécution.

Si vous installez IoT Edge, plutôt que de mettre à niveau une installation existante, suivez les étapes décrites dans [Mettre à jour hors connexion ou vers une version spécifique](how-to-provision-single-device-linux-symmetric.md#offline-or-specific-version-installation-optional).

## <a name="next-steps"></a>Étapes suivantes

Déterminez les dernières [versions d’Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases).

Restez informé des dernières mises à jour et annonces en consultant le [blog Internet of Things](https://azure.microsoft.com/blog/topics/internet-of-things/)
