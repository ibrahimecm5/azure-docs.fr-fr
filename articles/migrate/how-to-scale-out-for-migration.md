---
title: Configurer une appliance scale-out Azure Migrate pour une migration VMware sans agent
description: Découvrez comment configurer une appliance de montée en puissance parallèle Azure Migrate pour migrer des machines virtuelles Hyper-V.
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.topic: how-to
ms.date: 03/02/2021
ms.openlocfilehash: 9a041511dcd921c7bf4a9316cdbf3d7617ef66b7
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131503712"
---
# <a name="scale-agentless-migration-of-vmware-virtual-machines-to-azure"></a>Mettre à l’échelle une migration sans agent de machines virtuelles VMware vers Azure

Cet article explique comment utiliser une appliance de Scale-out pour migrer un grand nombre de machines virtuelles VMware vers Azure à l’aide de la méthode sans agent de l’outil Migration de serveur Azure Migrate.

Grâce à la méthode de migration sans agent pour les machines virtuelles VMware, vous pouvez :

- répliquer simultanément jusqu’à 300 machines virtuelles à partir d’un seul serveur vCenter à l’aide d’une seule appliance Azure Migrate ;
- répliquer simultanément jusqu’à 500 machines virtuelles à partir d’un seul serveur vCenter en déployant une deuxième appliance de montée en puissance parallèle pour la migration.

Dans cet article, vous allez apprendre à :

- Ajouter une appliance de Scale-out pour la migration sans agent de machines virtuelles VMware
- migrez simultanément jusqu’à 500 machines virtuelles à l’aide de l’appliance de montée en puissance parallèle.

##  <a name="prerequisites"></a>Prérequis

Avant de commencer, vous devez suivre les étapes suivantes :

- créer le projet Azure Migrate ;
- déployer l’appliance Azure Migrate (appliance principale) et procéder à la découverte des machines virtuelles VMware que gère votre serveur vCenter ;
- configurez la réplication pour une ou plusieurs machines virtuelles à migrer.
> [!IMPORTANT]
> Vous devez disposer d’au moins une machine virtuelle de réplication dans le projet avant de pouvoir ajouter une appliance de montée en puissance parallèle pour la migration.

Pour savoir comment procéder, consultez le tutoriel sur la [migration de machines virtuelles VMware vers Azure avec la méthode de migration sans agent](./tutorial-migrate-vmware.md).

## <a name="deploy-a-scale-out-appliance"></a>Déployer une appliance de montée en puissance parallèle

Pour ajouter une appliance de montée en puissance parallèle, procédez de la manière décrite ci-dessous :

1. Cliquez sur **Découvrir** > **Vos machines sont-elles virtualisées ?** 
1. Sélectionnez **Oui, avec l’hyperviseur vSphere VMware.**
1. Sélectionnez la réplication sans agent à l’étape suivante.
1. Sélectionnez **Montée en puissance parallèle d’une appliance principale existante** dans le menu de sélection du type d’appliance.
1. Sélectionnez l’appliance principale (l’appliance à l’aide de laquelle la découvrir a été effectuée) pour laquelle vous voulez opérer une montée en puissance parallèle.

:::image type="content" source="./media/how-to-scale-out-for-migration/add-scale-out.png" alt-text="Page Découvrir les machines pour l’intégration de la montée en puissance parallèle":::

### <a name="1-generate-the-azure-migrate-project-key"></a>1. Générer la clé de projet Azure Migrate

1. Dans **Générer une clé de projet Azure Migrate**, fournissez un nom de suffixe pour l’appliance de montée en puissance parallèle. Le suffixe ne peut contenir que des caractères alphanumériques et sa longueur est limitée à 14 caractères.
2. Cliquez sur **Générer une clé** pour lancer la création des ressources Azure requises. Ne fermez pas la page Découvrir pendant la création des ressources.
3. Copiez la clé générée. Vous aurez besoin de la clé ultérieurement pour effectuer l’inscription de l’appliance de montée en puissance parallèle.

### <a name="2-download-the-installer-for-the-scale-out-appliance"></a>2. Télécharger le programme d’installation pour l’appliance de montée en puissance parallèle

Dans **Télécharger l’appliance Azure Migrate**, cliquez sur **Télécharger**. Vous devez télécharger le script du programme d’installation PowerShell pour déployer l’appliance de montée en puissance parallèle sur un serveur existant exécutant Windows Server 2016 et présentant la configuration matérielle requise (32 Go de RAM, 8 processeurs virtuels, environ 80 Go de stockage sur disque et accès Internet, directement ou via un proxy).

:::image type="content" source="./media/how-to-scale-out-for-migration/download-scale-out.png" alt-text="Télécharger le script pour l’appliance de montée en puissance parallèle":::

> [!TIP]
> Vous pouvez valider la somme de contrôle du fichier zip téléchargé en procédant comme suit :
>
> 1. Sur le serveur où vous avez téléchargé le fichier, ouvrez une fenêtre de commande d’administrateur.
> 2. Exécutez la commande suivante pour générer le code de hachage du fichier compressé :
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Exemple d’utilisation : ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256 ```
> 3. Téléchargez la dernière version du programme d’installation de l’appliance de scale-out à partir du portail si la valeur de hachage calculée ne correspond pas à cette chaîne : 3C00F9EB54CC6C55E127EDE47DFA28CCCF752697377EB1C9F3435E75DA5AA029

### <a name="3-run-the-azure-migrate-installer-script"></a>3. Exécuter le script du programme d’installation Azure Migrate

1. Extrayez le fichier compressé dans un dossier sur le serveur qui hébergera l’appliance.  Veillez à ne pas exécuter le script sur un serveur disposant d’une appliance Azure Migrate.

2. Lancez PowerShell sur le serveur ci-dessus avec un privilège administratif (élevé).

3. Remplacez le répertoire PowerShell par le dossier dans lequel le contenu a été extrait du fichier compressé téléchargé.

4. Exécutez le script `AzureMigrateInstaller.ps1` à l’aide de la commande suivante :

  `PS C:\Users\administrator\Desktop\AzureMigrateInstaller> .\AzureMigrateInstaller.ps1 `

5. Sélectionnez parmi les options de scénario, de cloud, de configuration et de connectivité pour déployer l’appliance souhaitée. Par exemple, la sélection présentée ci-dessous configure une **appliance de scale-out** pour lancer des réplications concurrentes sur des serveurs s’exécutant dans votre environnement VMware vers un projet Azure Migrate avec une connectivité **par défaut _(point de terminaison public)_**  sur le **cloud public Azure**.

    :::image type="content" source="./media/how-to-scale-out-for-migration/script-vmware-scaleout-inline.png" alt-text="Capture d’écran montrant comment configurer une appliance de scale-out." lightbox="./media/how-to-scale-out-for-migration/script-vmware-scaleout-expanded.png":::

6. Le script du programme d’installation effectue les opérations suivantes :

    - Installe l’agent de passerelle et le gestionnaire de configuration d’appliance pour effectuer davantage de réplications de serveur simultanées.
    - Installe des rôles Windows, notamment le service d’activation Windows, IIS et PowerShell ISE.
    - Télécharge et installe un module réinscriptible IIS.
    - Met à jour une clé de Registre (HKLM) avec les détails de paramètres persistants pour Azure Migrate.
    - Crée les fichiers suivants sous le chemin :
        - **Fichiers de configuration** : %Programdata%\Microsoft Azure\Config
        - **Fichiers journaux** : %Programdata%\Microsoft Azure\Logs

Une fois que le script a été exécuté avec succès, le gestionnaire de configuration de l’appliance est lancé automatiquement.

> [!NOTE]
> Si vous rencontrez des problèmes, vous pouvez accéder aux journaux de script dans C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>horodatage</em>.log pour les résoudre.


### <a name="4-configure-the-appliance"></a>4. Configurer l’appliance

Avant de commencer, assurez-vous que [ces points de terminaison Azure](migrate-appliance.md#public-cloud-urls) sont accessibles à partir de l’appliance de montée en puissance parallèle.

- Ouvrez un navigateur sur une machine pouvant se connecter au serveur d’appliance de montée en puissance parallèle, puis ouvrez l’URL du gestionnaire de configuration d’appliance : **https://*nom ou adresse IP de l’appliance de montée en puissance parallèle*: 44368**.

   Vous pouvez également ouvrir le gestionnaire de configuration à partir du bureau du serveur d’appliance de montée en puissance parallèle en utilisant le raccourci du gestionnaire de configuration.
- Acceptez les **termes du contrat de licence** et lisez les informations relatives aux tiers.
- Dans le gestionnaire de configuration > **Configurer les prérequis**, procédez comme suit :
   - **Connectivité** : l’appliance vérifie que le serveur a accès à Internet. Si le serveur utilise un proxy :
     1. Cliquez sur **Configurer le proxy** pour spécifier l’adresse du proxy sous la forme http://ProxyIPAddress ou http://ProxyFQDN), ainsi que le port d’écoute.
     2. Spécifiez les informations d’identification si le proxy nécessite une authentification.
     3. Seuls les proxys HTTP sont pris en charge.
     4. Si vous avez ajouté les détails du proxy ou désactivé le proxy et/ou l’authentification, cliquez sur **Enregistrer** pour relancer la vérification de la connectivité.
   - **Synchronisation de l’heure** : L’heure de l’appliance doit être synchronisée avec l’heure Internet pour que la découverte fonctionne correctement.
   - **Installer les mises à jour** : L’appliance vérifie que les dernières mises à jour sont installées. Une fois la vérification terminée, vous pouvez cliquer sur **Afficher les services de l’appliance** pour voir l’état et les versions des services s’exécutant sur le serveur d’appliance.
   - **Installer VDDK** : L’appliance vérifie que VDDK (VMware vSphere Virtual Disk Development Kit) est installé. S’il n’est pas installé, téléchargez VDDK 6.7 à partir de VMware, puis extrayez le contenu du fichier zip téléchargé vers l’emplacement sur l’appliance spécifié dans les **instructions d’installation** affichées sur l’écran du gestionnaire de configuration d’appliance.


### <a name="register-the-appliance-with-azure-migrate"></a>Inscrire l’appliance auprès d’Azure Migrate

1. Collez la **clé de projet Azure Migrate** copiée à partir du portail. Si vous n’avez pas la clé, accédez à **Évaluation de serveur > Découvrir > Gérer les appliances existantes**, sélectionnez le nom de l’appliance principale, recherchez l’appliance de montée en puissance parallèle qui y est associée, puis copiez la clé correspondante.
1. Vous aurez besoin d’un code d’appareil pour vous authentifier auprès d’Azure. Le fait de cliquer sur **Connexion** ouvre une boîte de dialogue modale comprenant le code de l’appareil, comme celle affichée ci-dessous.

   :::image type="content" source="./media/tutorial-discover-vmware/device-code.png" alt-text="Boîte de dialogue modale indiquant le code de l’appareil":::

1. Cliquez sur **Copier le code et se connecter** pour copier le code de l’appareil et ouvrir une invite de connexion Azure dans un nouvel onglet de navigateur. S’il n’apparaît pas, vérifiez que vous avez désactivé le bloqueur de fenêtres publicitaires dans le navigateur.
1. Sous le nouvel onglet, collez le code de l’appareil, puis connectez-vous avec votre nom d’utilisateur et votre mot de passe Azure.
   
   La connexion avec un code PIN n’est pas prise en charge.
3. Si vous avez fermé accidentellement l’onglet Connexion avant de vous être connecté, vous devrez actualiser l’onglet Appliance Configuration Manager pour réactiver le bouton de connexion.
1. Une fois connecté, revenez à l’onglet précédent avec le gestionnaire de configuration d’appliance.
1. Si le compte d’utilisateur Azure utilisé pour la connexion dispose des autorisations adéquates sur les ressources Azure créées au moment de la génération de la clé, l’inscription de l’appliance est lancée.
:::image type="content" source="./media/how-to-scale-out-for-migration/registration-scale-out.png" alt-text="Volet 2 sur le gestionnaire de configuration d’appliance":::

#### <a name="import-appliance-configuration-from-primary-appliance"></a>Importer une configuration d’appliance à partir de l’appliance principale

Pour terminer l’inscription de l’appliance de montée en puissance parallèle, cliquez sur **Importer** pour obtenir les fichiers de configuration nécessaires de l’appliance principale.

1. Cliquer sur **Importer** a pour effet d’ouvrir une fenêtre contextuelle contenant des instructions sur la façon d’importer les fichiers de configuration nécessaires à partir de l’appliance principale.

:::image type="content" source="./media/how-to-scale-out-for-migration/import-modal-scale-out.png" alt-text="Boîte de dialogue modale":::

1. Connectez-vous (bureau à distance) à l’appliance principale, puis exécutez les commandes PowerShell suivantes :

   `PS cd 'C:\Program Files\Microsoft Azure Appliance Configuration Manager\Scripts\PowerShell' `
    
    `PS .\ExportConfigFiles.ps1 `

1. Copiez le fichier zip créé en exécutant les commandes ci-dessus sur l’appliance de montée en puissance parallèle. Le fichier zip contient les fichiers de configuration nécessaires pour l’inscription de l’appliance de montée en puissance parallèle.

1. Dans la fenêtre contextuelle ouverte à l’étape précédente, sélectionnez l’emplacement du fichier zip de configuration copié, puis cliquez sur **Enregistrer**.

Une fois les fichiers importés, l’inscription de l’appliance de montée en puissance parallèle s’achève et l’horodateur de la dernière importation réussie s’affiche. Vous pouvez également consulter les détails de l’inscription en cliquant sur **Afficher les détails**.
:::image type="content" source="./media/how-to-scale-out-for-migration/import-success.png" alt-text="Capture d’écran montrant l’inscription de l’appliance de scale-out avec un projet Azure Migrate.":::

À ce stade, vous devez revalider le fait que l’appliance de montée en puissance parallèle est en mesure de se connecter à votre serveur vCenter. Cliquez sur **Revalider** pour valider la connectivité vCenter Server à partir de l’appliance de montée en puissance parallèle.
:::image type="content" source="./media/how-to-scale-out-for-migration/view-sources.png" alt-text="Capture d’écran montrant la vue des informations d’identification et sources de découverte à valider.":::

> [!IMPORTANT]
> Si vous modifiez les informations d’identification de vCenter Server sur l’appliance principale, veillez à réimporter les fichiers de configuration sur l’appliance de montée en puissance parallèle pour obtenir la configuration la plus récente et poursuivre les réplications en cours.<br/> Si vous n’avez plus besoin de l’appliance de montée en puissance parallèle, veillez à la désactiver. [**En savoir plus**](./common-questions-appliance.md) sur la désactivation de l’appliance de montée en puissance parallèle lorsque vous n’en avez pas besoin.

## <a name="replicate"></a>Réplication

1. Une fois l’appliance de montée en puissance parallèle inscrite, dans la vignette Azure Migrate : migration du serveur, cliquez sur **Répliquer**.

2.  Suivez les étapes à l’écran pour démarrer la réplication de machines virtuelles supplémentaires. 

Avec l’appliance de montée en puissance parallèle en place, vous pouvez désormais répliquer 500 machines virtuelles simultanément. Vous pouvez également migrer des machines virtuelles par lots de 200 via le portail Azure.

L’outil de migration Azure Migrate Server s’occupe de la distribution des machines virtuelles entre l’appliance principale et l’appliance de montée en puissance parallèle pour la réplication. Une fois la réplication terminée, vous pouvez migrer les machines virtuelles.

> [!TIP]
> Si vous souhaitez migrer un grand nombre de machines virtuelles, pour des performances optimales, nous vous recommandons de les migrer par lots de 200.
  
## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris :
- Comment configurer une appliance de montée en puissance parallèle
- Comment répliquer des machines virtuelles à l’aide d’une appliance de montée en puissance parallèle


[En savoir plus](./tutorial-migrate-vmware.md) sur la migration de serveurs vers Azure à l’aide de l’outil de migration de serveur d’Azure Migrate.
