---
title: Configurer une appliance Azure Migrate dans Azure Government
description: Apprenez à configurer une appliance Azure Migrate dans Azure Government
author: vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: how-to
ms.date: 11/02/2021
ms.openlocfilehash: 9586685bc5fd482bf3e87dcfd6848a08c74dfc4f
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131509673"
---
# <a name="set-up-an-appliance-for-azure-government-cloud"></a>Configurer une appliance pour Azure Government Cloud

Suivez cet article pour déployer une [appliance Azure Migrate](./migrate-appliance-architecture.md) pour Azure Government Cloud pour effectuer :

- la détection, l’évaluation et la réplication sans agent des serveurs exécutés dans un environnement VMware
- la découverte et l’évaluation des serveurs s’exécutant dans un environnement Hyper-V
- la détection et l’évaluation des serveurs physiques ou des serveurs s’exécutant sur d’autres Clouds comme AWS, GCP, Xen, etc.

Si vous souhaitez configurer une appliance dans le cloud public, suivez [cet article](deploy-appliance-script.md).

> [!NOTE]
> L’option de déploiement d’une appliance à l’aide d’un modèle (pour les serveurs exécutant un environnement VMware ou Hyper-V) n’est pas prise en charge dans Azure Government. Vous devez utiliser le script du programme d’installation uniquement.

## <a name="prerequisites"></a>Prérequis

Vous pouvez utiliser le script pour déployer l’appliance Azure Migrate sur un serveur physique ou virtualisé existant.

- Le serveur qui fera office d'appliance doit exécuter Windows Server 2016 et répondre aux autres exigences pour [VMware](migrate-appliance.md#appliance---vmware), [Hyper-V](migrate-appliance.md#appliance---hyper-v) et les [serveurs physiques](migrate-appliance.md#appliance---physical).
- Si vous exécutez le script sur un serveur sur lequel l’appliance Azure Migrate est déjà configurée, vous pouvez choisir de nettoyer la configuration existante et de configurer une nouvelle appliance de la configuration souhaitée. Lorsque vous exécutez le script, vous recevrez une notification comme indiqué ci-dessous :
  
    :::image type="content" source="./media/deploy-appliance-script/script-reconfigure-appliance.png" alt-text="Capture d’écran montrant comment reconfigurer une appliance.":::

## <a name="set-up-the-appliance-for-vmware"></a>Configurer l'appliance pour VMware

1. Pour configurer l’appliance, téléchargez le fichier compressé dénommé AzureMigrateInstaller.zip à partir du portail ou de [cette page](https://go.microsoft.com/fwlink/?linkid=2140334).
1. Extrayez le contenu sur le serveur sur lequel vous souhaitez déployer l’appliance.
1. Exécuter le script PowerShell pour lancer le gestionnaire de configuration de l’appliance.
1. Installez l'appliance et configurez-la pour la première fois.

### <a name="download-the-script"></a>Télécharger le script

1. Dans **Objectifs de migration** > **Windows, Linux et SQL Server** > **Azure Migrate : Découverte et évaluation**, cliquez sur **Découvrir**.
2. Dans **Découvrir les serveurs** > **Vos serveurs sont-ils virtualisés ?** , sélectionnez **Oui, avec l’hyperviseur VMware vSphere**.
3. Fournissez un nom d’appliance et générez une clé de projet sur le portail.
3. Cliquez sur **Télécharger** pour télécharger le fichier zip.

### <a name="verify-security"></a>Vérifier la sécurité

Vérifiez que le fichier compressé est sécurisé avant de le déployer.

1. Sur le serveur où vous avez téléchargé le fichier, ouvrez une fenêtre de commande d’administrateur.
2. Exécutez la commande suivante pour générer le code de hachage du fichier compressé :
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Exemple d’utilisation : ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256 ```
3.  Vérifiez la dernière version de l’appliance et la valeur de hachage :

    **Télécharger** | **Valeur de hachage**
    --- | ---
    [Version la plus récente](https://go.microsoft.com/fwlink/?linkid=2140337) | 3C00F9EB54CC6C55E127EDE47DFA28CCCF752697377EB1C9F3435E75DA5AA029


### <a name="run-the-script"></a>Exécuter le script

1. Extrayez le fichier compressé dans un dossier sur le serveur qui hébergera l’appliance.  Veillez à ne pas exécuter le script sur un serveur disposant d’une appliance Azure Migrate.

2. Lancez PowerShell sur le serveur ci-dessus avec un privilège administratif (élevé).

3. Remplacez le répertoire PowerShell par le dossier dans lequel le contenu a été extrait du fichier compressé téléchargé.

4. Exécutez le script nommé **AzureMigrateInstaller.ps1** via la commande suivante :

   `PS C:\Users\administrator\Desktop\AzureMigrateInstaller> .\AzureMigrateInstaller.ps1 `

5. Sélectionnez parmi les options de scénario, de cloud et de connectivité pour déployer une appliance avec la configuration souhaitée. Par exemple, la sélection présentée ci-dessous configure une appliance pour la détection, l’évaluation et la migration de **serveurs exécutés sur votre environnement VMware** dans un projet Azure Migrate doté d’une **connectivité _(point de terminaison public)_ par défaut** sur **Azure Government Cloud**.

   :::image type="content" source="./media/deploy-appliance-script-government/script-vmware-gov-inline.png" alt-text="Capture d’écran montrant comment configurer l’appliance avec la configuration souhaitée pour Vmware." lightbox="./media/deploy-appliance-script-government/script-vmware-gov-expanded.png":::

6. Le script du programme d’installation effectue les opérations suivantes :

   - Installe les agents et une application web.
   - Installe des rôles Windows, notamment le service d’activation Windows, IIS et PowerShell ISE.
   - Télécharge et installe un module réinscriptible IIS.
   - Met à jour une clé de Registre (HKLM) avec les détails de paramètres persistants pour Azure Migrate.
   - Crée les fichiers suivants sous le chemin :
     - **Fichiers de configuration** : %Programdata%\Microsoft Azure\Config
     - **Fichiers journaux** : %Programdata%\Microsoft Azure\Logs

Une fois que le script a été exécuté avec succès, le gestionnaire de configuration de l’appliance est lancé automatiquement.

### <a name="verify-access"></a>Vérifier l'accès

Vérifiez que l'appliance peut se connecter aux URL Azure des [clouds du secteur public](migrate-appliance.md#government-cloud-urls).

## <a name="set-up-the-appliance-for-hyper-v"></a>Configurer l'appliance pour Hyper-V

1. Pour configurer l’appliance, téléchargez le fichier compressé dénommé AzureMigrateInstaller.zip à partir du portail ou de [cette page](https://go.microsoft.com/fwlink/?linkid=2140334).
1. Extrayez le contenu sur le serveur sur lequel vous souhaitez déployer l’appliance.
1. Exécuter le script PowerShell pour lancer le gestionnaire de configuration de l’appliance.
1. Installez l'appliance et configurez-la pour la première fois.

### <a name="download-the-script"></a>Télécharger le script

1.  Dans **Objectifs de migration** > **Windows, Linux et SQL Server** > **Azure Migrate : Découverte et évaluation**, cliquez sur **Découvrir**.
2.  Dans **Découvrir les serveurs** > **Vos serveurs sont-ils virtualisés ?** , sélectionnez **Oui, avec Hyper-V**.
3. Fournissez un nom d’appliance et générez une clé de projet sur le portail.
3. Cliquez sur **Télécharger** pour télécharger le fichier zip. 

### <a name="verify-security"></a>Vérifier la sécurité

Vérifiez que le fichier compressé est sécurisé avant de le déployer.

1. Sur le serveur où vous avez téléchargé le fichier, ouvrez une fenêtre de commande d’administrateur.
2. Exécutez la commande suivante pour générer le code de hachage du fichier compressé :
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Exemple d’utilisation : ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256 ```
3.  Vérifiez la dernière version de l’appliance et la valeur de hachage :

    **Télécharger** | **Valeur de hachage**
    --- | ---
    [Version la plus récente](https://go.microsoft.com/fwlink/?linkid=2140424) | 3C00F9EB54CC6C55E127EDE47DFA28CCCF752697377EB1C9F3435E75DA5AA029

### <a name="run-the-script"></a>Exécuter le script

1. Extrayez le fichier compressé dans un dossier sur le serveur qui hébergera l’appliance.  Veillez à ne pas exécuter le script sur un serveur disposant d’une appliance Azure Migrate.

2. Lancez PowerShell sur le serveur ci-dessus avec un privilège administratif (élevé).

3. Remplacez le répertoire PowerShell par le dossier dans lequel le contenu a été extrait du fichier compressé téléchargé.

4. Exécutez le script nommé **AzureMigrateInstaller.ps1** via la commande suivante :

   `PS C:\Users\administrator\Desktop\AzureMigrateInstaller> .\AzureMigrateInstaller.ps1`

5. Sélectionnez parmi les options de scénario, de cloud et de connectivité pour déployer une appliance avec la configuration souhaitée. Par exemple, la sélection présentée ci-dessous configure une appliance pour la détection et l’évaluation de **serveurs exécutés sur votre environnement Hyper-V** dans un projet Azure Migrate doté d’une **connectivité _(point de terminaison public)_ par défaut** sur **Azure Government Cloud**.

    :::image type="content" source="./media/deploy-appliance-script-government/script-hyperv-gov-inline.png" alt-text="Capture d’écran montrant comment configurer l’appliance avec la configuration souhaitée pour Hyper-V." lightbox="./media/deploy-appliance-script-government/script-hyperv-gov-expanded.png":::

6. Le script du programme d’installation effectue les opérations suivantes :

    - Installe les agents et une application web.
    - Installe des rôles Windows, notamment le service d’activation Windows, IIS et PowerShell ISE.
    - Télécharge et installe un module réinscriptible IIS.
    - Met à jour une clé de Registre (HKLM) avec les détails de paramètres persistants pour Azure Migrate.
    - Crée les fichiers suivants sous le chemin :
    - **Fichiers de configuration** : %Programdata%\Microsoft Azure\Config
    - **Fichiers journaux** : %Programdata%\Microsoft Azure\Logs

Une fois que le script a été exécuté avec succès, le gestionnaire de configuration de l’appliance est lancé automatiquement.

### <a name="verify-access"></a>Vérifier l'accès

Vérifiez que l'appliance peut se connecter aux URL Azure des [clouds du secteur public](migrate-appliance.md#government-cloud-urls).


## <a name="set-up-the-appliance-for-physical-servers"></a>Configurer l'appliance pour des serveurs physiques

1. Pour configurer l’appliance, téléchargez le fichier compressé dénommé AzureMigrateInstaller.zip à partir du portail ou de [cette page](https://go.microsoft.com/fwlink/?linkid=2140334).
1. Extrayez le contenu sur le serveur sur lequel vous souhaitez déployer l’appliance.
1. Exécuter le script PowerShell pour lancer le gestionnaire de configuration de l’appliance.
1. Installez l'appliance et configurez-la pour la première fois.

### <a name="download-the-script"></a>Télécharger le script

1. Dans **Objectifs de migration** > **Windows, Linux et SQL Server** > **Azure Migrate : Découverte et évaluation**, cliquez sur **Découvrir**.
2. Dans **Découvrir des serveurs** > **Vos serveurs sont-ils virtualisés ?** , sélectionnez **Physiques ou autres (AWS, GCP, Xen, etc.)** .
3. Cliquez sur **Télécharger** pour télécharger le fichier zip.

### <a name="verify-security"></a>Vérifier la sécurité

Vérifiez que le fichier compressé est sécurisé avant de le déployer.

1. Sur le serveur où vous avez téléchargé le fichier, ouvrez une fenêtre de commande d’administrateur.
2. Exécutez la commande suivante pour générer le code de hachage du fichier compressé :
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Exemple d’utilisation : ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256 ```
3.  Vérifiez la dernière version de l’appliance et la valeur de hachage :

    **Télécharger** | **Valeur de hachage**
    --- | ---
    [Version la plus récente](https://go.microsoft.com/fwlink/?linkid=2140338) | 3C00F9EB54CC6C55E127EDE47DFA28CCCF752697377EB1C9F3435E75DA5AA029

> [!NOTE]
> Le même script peut être utilisé pour configurer une appliance physique pour Azure Government Cloud ou un cloud Azure Government avec une connectivité de point de terminaison public ou privé.

### <a name="run-the-script"></a>Exécuter le script

1. Extrayez le fichier compressé dans un dossier sur le serveur qui hébergera l’appliance.  Veillez à ne pas exécuter le script sur un serveur disposant d’une appliance Azure Migrate.

2. Lancez PowerShell sur le serveur ci-dessus avec un privilège administratif (élevé).

3. Remplacez le répertoire PowerShell par le dossier dans lequel le contenu a été extrait du fichier compressé téléchargé.

4. Exécutez le script nommé **AzureMigrateInstaller.ps1** via la commande suivante :

    `PS C:\Users\administrator\Desktop\AzureMigrateInstaller> .\AzureMigrateInstaller.ps1 `

5. Sélectionnez parmi les options de scénario, de cloud et de connectivité pour déployer une appliance avec la configuration souhaitée. Par exemple, la sélection présentée ci-dessous configure une appliance pour découvrir et évaluer des **serveurs physiques** _(ou des serveurs qui s’exécutent sur d’autres clouds comme AWS, GCP, Xen, etc.)_ dans un projet Azure Migrate avec une **connectivité par défaut _(point de terminaison public)_** sur **Azure Government Cloud**.

    :::image type="content" source="./media/deploy-appliance-script-government/script-physical-gov-inline.png" alt-text="Capture d’écran montrant comment configurer l’appliance avec la configuration souhaitée pour serveurs physiques." lightbox="./media/deploy-appliance-script-government/script-physical-gov-expanded.png":::

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

### <a name="verify-access"></a>Vérifier l'accès

Vérifiez que l'appliance peut se connecter aux URL Azure des [clouds du secteur public](migrate-appliance.md#government-cloud-urls).

## <a name="next-steps"></a>Étapes suivantes

Après avoir déployé l’appliance, vous devez la configurer pour la première fois et l’inscrire auprès du projet.

- Configurez l’appliance pour [VMware](how-to-set-up-appliance-vmware.md#4-configure-the-appliance).
- Configurez l’appliance pour [Hyper-V](how-to-set-up-appliance-hyper-v.md#configure-the-appliance).
- Configurez l’appliance pour les [serveurs physiques](how-to-set-up-appliance-physical.md).
