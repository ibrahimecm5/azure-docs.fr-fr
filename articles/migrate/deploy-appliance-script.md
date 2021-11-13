---
title: Configurer une appliance Azure Migrate avec un script
description: Découvrez comment configurer une appliance Azure Migrate avec un script
ms.topic: how-to
author: Vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.date: 11/02/2021
ms.openlocfilehash: 2c82b8c14f68ab11ad0585390e68de4864ee7169
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131500162"
---
# <a name="set-up-an-appliance-with-a-script"></a>Configurer une appliance avec un script

Suivez cet article pour le déploiement d’une [appliance Azure Migrate](./migrate-appliance-architecture.md) à l’aide d’un script PowerShell pour :
- la détection, l’évaluation et la réplication sans agent des serveurs exécutés dans un environnement VMware
- la détection et l’évaluation des serveurs exécutés dans un environnement Hyper-V.

Vous pouvez déployer l’appliance pour des serveurs sur VMware et Hyper-V à l’aide d’un script ou d'un modèle (OVA/VHD) en téléchargement sur le portail Azure. Un script s'avère utile si vous ne parvenez pas à créer une appliance à l’aide du modèle téléchargé.

- Pour utiliser un modèle, suivez les tutoriels pour [VMware](./tutorial-discover-vmware.md) ou [Hyper-V](./tutorial-discover-hyper-v.md).
- Pour configurer une appliance pour les serveurs physiques, seul un script peut être utilisé. Suivez [cet article](how-to-set-up-appliance-physical.md).
- Pour configurer une appliance dans un Cloud Azure Government, vous ne pouvez utiliser qu’un script. Suivez [cet article](deploy-appliance-script-government.md).

## <a name="prerequisites"></a>Configuration requise

Vous pouvez utiliser le script pour déployer l’appliance Azure Migrate sur un serveur existant dans votre environnement VMware ou Hyper-V.

- Le serveur hébergeant l’appliance doit remplir les conditions suivantes en matière de matériel et de système d’exploitation :

Scénario | Spécifications
--- | ---
VMware | Windows Server 2016 avec 32 Go de mémoire, huit processeurs virtuels, environ 80 Go de stockage sur disque.
Hyper-V | Windows Server 2016 avec 16 Go de mémoire, huit processeurs virtuels, environ 80 Go de stockage sur disque.

- Le serveur a également besoin d’un commutateur virtuel externe. Il nécessite une adresse IP statique ou dynamique. 
- Avant de déployer l’appliance, examinez en détail la configuration requise pour [VMware](migrate-appliance.md#appliance---vmware) et les [Hyper-V](migrate-appliance.md#appliance---hyper-v).
- Si vous exécutez le script sur un serveur sur lequel l’appliance Azure Migrate est déjà configurée, vous pouvez choisir de nettoyer la configuration existante et de configurer une nouvelle appliance de la configuration souhaitée. Lorsque vous exécutez le script, vous recevrez une notification comme indiqué ci-dessous :

    ![Configuration de l’appliance avec la configuration souhaitée](./media/deploy-appliance-script/script-reconfigure-appliance.png)

## <a name="set-up-the-appliance-for-vmware"></a>Configurer l'appliance pour VMware

1. Pour configurer l’appliance, téléchargez le fichier compressé dénommé AzureMigrateInstaller.zip à partir du portail ou de [cette page](https://go.microsoft.com/fwlink/?linkid=2116601).
1. Extrayez le contenu sur le serveur sur lequel vous souhaitez déployer l’appliance.
1. Exécuter le script PowerShell pour lancer le gestionnaire de configuration de l’appliance.
1. Installez l'appliance et configurez-la pour la première fois.

### <a name="verify-security"></a>Vérifier la sécurité

Vérifiez que le fichier compressé est sécurisé avant de le déployer.

1. Sur le serveur où vous avez téléchargé le fichier, ouvrez une fenêtre de commande d’administrateur.
2. Exécutez la commande suivante pour générer le code de hachage du fichier compressé :
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Exemple d’utilisation : ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256 ```
3.  Vérifiez la dernière version de l’appliance et la valeur de hachage :

    **Télécharger** | **Valeur de hachage**
    --- | ---
    [Version la plus récente](https://go.microsoft.com/fwlink/?linkid=2116601) | 3C00F9EB54CC6C55E127EDE47DFA28CCCF752697377EB1C9F3435E75DA5AA029

> [!NOTE]
> Le même script peut être utilisé pour configurer une appliance VMware pour un cloud public Azure ou un cloud Azure Government.

### <a name="run-the-script"></a>Exécuter le script

1. Extrayez le fichier compressé dans un dossier sur le serveur qui hébergera l’appliance.
> [!NOTE]
> Veillez à ne pas exécuter le script sur un serveur disposant d’une appliance Azure Migrate. L’exécution du script sur l’appliance Azure Migrate supprimera la configuration de travail et la remplacera par une configuration nouvellement définie.

2. Lancez PowerShell sur le serveur ci-dessus avec un privilège administratif (élevé).

3. Remplacez le répertoire PowerShell par le dossier dans lequel le contenu a été extrait du fichier compressé téléchargé.

4. Exécutez le script nommé **AzureMigrateInstaller.ps1** via la commande suivante :

   `PS C:\Users\administrator\Desktop\AzureMigrateInstaller> .\AzureMigrateInstaller.ps1`

5. Sélectionnez parmi les options de scénario, de cloud et de connectivité pour déployer une appliance avec la configuration souhaitée. Par exemple, la sélection présentée ci-dessous configure une appliance pour la détection, l’évaluation et la migration de **serveurs exécutés sur votre environnement VMware** dans un projet Azure Migrate doté d’une **connectivité _(point de terminaison public)_ par défaut** sur le **cloud public Azure**.

   :::image type="content" source="./media/deploy-appliance-script/script-vmware-default-inline.png" alt-text="Capture d’écran montrant comment configurer l’appliance Vmware avec la configuration souhaitée." lightbox="./media/deploy-appliance-script/script-vmware-default-expanded.png":::

6. Le script du programme d’installation effectue les opérations suivantes :

 - Installe les agents et une application web.
 - Installe des rôles Windows, notamment le service d’activation Windows, IIS et PowerShell ISE.
 - Télécharge et installe un module réinscriptible IIS.
 - Met à jour une clé de Registre (HKLM) avec les détails de paramètres persistants pour Azure Migrate.
 - Crée les fichiers suivants sous le chemin :
    - **Fichiers de configuration** : `%ProgramData%\Microsoft Azure\Config`
    - **Fichiers journaux** : `%ProgramData%\Microsoft Azure\Logs`

Une fois que le script a été exécuté avec succès, le gestionnaire de configuration de l’appliance est lancé automatiquement.

> [!NOTE]
> Si vous rencontrez des problèmes, vous pouvez accéder aux journaux de script dans C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>horodatage</em>.log pour les résoudre.

### <a name="verify-access"></a>Vérifier l’accès

Vérifiez que l’appliance peut se connecter aux URL Azure pour le cloud [public](migrate-appliance.md#public-cloud-urls).

## <a name="set-up-the-appliance-for-hyper-v"></a>Configurer l'appliance pour Hyper-V

1. Pour configurer l’appliance, téléchargez le fichier compressé dénommé AzureMigrateInstaller.zip à partir du portail ou de [cette page](https://go.microsoft.com/fwlink/?linkid=2116657).
1. Extrayez le contenu sur le serveur sur lequel vous souhaitez déployer l’appliance.
1. Exécuter le script PowerShell pour lancer le gestionnaire de configuration de l’appliance.
1. Installez l'appliance et configurez-la pour la première fois.

### <a name="verify-security"></a>Vérifier la sécurité

Vérifiez que le fichier compressé est sécurisé avant de le déployer.

1. Sur le serveur où vous avez téléchargé le fichier, ouvrez une fenêtre de commande d’administrateur.
2. Exécutez la commande suivante pour générer le code de hachage du fichier compressé :
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Exemple d’utilisation : ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256 ```
3.  Vérifiez la dernière version de l’appliance et la valeur de hachage :

    **Télécharger** | **Valeur de hachage**
    --- | ---
    [Version la plus récente](https://go.microsoft.com/fwlink/?linkid=2116657) | 3C00F9EB54CC6C55E127EDE47DFA28CCCF752697377EB1C9F3435E75DA5AA029

> [!NOTE]
> Le même script peut être utilisé pour configurer une appliance Hyper-V pour un cloud public Azure ou un cloud Azure Government.

### <a name="run-the-script"></a>Exécuter le script

1. Extrayez le fichier compressé dans un dossier sur le serveur qui hébergera l’appliance.
> [!NOTE]
> Veillez à ne pas exécuter le script sur une appliance Azure Migrate existante. L’exécution du script sur l’appliance Azure Migrate supprimera la configuration de travail et la remplacera par une configuration nouvellement définie.

2. Lancez PowerShell sur le serveur ci-dessus avec un privilège administratif (élevé).
3. Remplacez le répertoire PowerShell par le dossier dans lequel le contenu a été extrait du fichier compressé téléchargé.
4. Exécutez le script `AzureMigrateInstaller.ps1` à l’aide de la commande suivante :

   `PS C:\Users\administrator\Desktop\AzureMigrateInstaller> .\AzureMigrateInstaller.ps1 `

5. Sélectionnez parmi les options de scénario, de cloud et de connectivité pour déployer une appliance avec la configuration souhaitée. Par exemple, la sélection présentée ci-dessous configure une appliance pour la détection et l’évaluation de **serveurs exécutés sur votre environnement Hyper-V** dans un projet Azure Migrate doté d’une **connectivité _(point de terminaison public)_ par défaut** sur le **cloud public Azure**.

    :::image type="content" source="./media/deploy-appliance-script/script-hyperv-default-inline.png" alt-text="Capture d’écran montrant comment configurer l’appliance Hyper-V avec la configuration souhaitée." lightbox="./media/deploy-appliance-script/script-hyperv-default-expanded.png":::

6. Le script du programme d’installation effectue les opérations suivantes :

    - Installe les agents et une application web.
    - Installe des rôles Windows, notamment le service d’activation Windows, IIS et PowerShell ISE.
    - Télécharge et installe un module réinscriptible IIS.
    - Met à jour une clé de Registre (HKLM) avec les détails de paramètres persistants pour Azure Migrate.
    - Crée les fichiers suivants sous le chemin :
        - **Fichiers de configuration** : %Programdata%\Microsoft Azure\Config
        - **Fichiers journaux** : %Programdata%\Microsoft Azure\Logs

Une fois que le script a été exécuté avec succès, le gestionnaire de configuration de l’appliance est lancé automatiquement.

> [!NOTE]
> Si vous rencontrez des problèmes, vous pouvez accéder aux journaux de script dans C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>horodatage</em>.log pour les résoudre.

### <a name="verify-access"></a>Vérifier l’accès

Vérifiez que l’appliance peut se connecter aux URL Azure pour le cloud [public](migrate-appliance.md#public-cloud-urls).

## <a name="next-steps"></a>Étapes suivantes

Après avoir déployé l'appliance, vous devez la configurer pour la première fois et l'inscrire auprès du projet.

- Configurez l’appliance pour [VMware](how-to-set-up-appliance-vmware.md#4-configure-the-appliance).
- Configurez l’appliance pour [Hyper-V](how-to-set-up-appliance-hyper-v.md#configure-the-appliance).
