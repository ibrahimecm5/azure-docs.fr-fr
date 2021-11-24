---
title: Migrer une instance de cluster de basculement
description: Découvrez comment effectuer un lift-and-shift de votre solution de haute disponibilité de l’instance de cluster de basculement Always On vers SQL Server sur machines virtuelles Azure à l’aide d’Azure Migrate.
ms.service: virtual-machines-sql
ms.subservice: migration-guide
author: rahugup
manager: bsiva
ms.topic: how-to
ms.date: 4/25/2021
ms.author: rahugup
ms.openlocfilehash: a1ae8cd56d705df0234425488029ca7073c6dfea
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132347705"
---
# <a name="migrate-failover-cluster-instance-to-sql-server-on-azure-vms"></a>Migrer une instance de cluster de basculement vers SQL Server sur machines virtuelles Azure

Cet article vous apprend à migrer votre instance de cluster de basculement (FCI) Always On vers SQL Server sur machines virtuelles Azure à l’aide de l’[outil Azure Migrate : Server Migration](../../../migrate/migrate-services-overview.md#azure-migrate-server-migration-tool). À l’aide de l’outil de migration, vous serez en mesure de migrer chaque nœud de l’instance de cluster de basculement vers une machine virtuelle Azure hébergeant SQL Server, ainsi que les métadonnées du cluster et de la FCI.  

Dans cet article, vous apprendrez comment : 

> [!div class="checklist"]
> * Préparer l’environnement source et Azure pour la migration.
> * Démarrer la réplication des machines virtuelles
> * Superviser la réplication.
> * Exécuter une migration de machine virtuelle complète
> * Reconfigurer le cluster de basculement SQL avec les disques partagés Azure.


Ce guide utilise l’approche de migration basée sur un agent d’Azure Migrate, qui traite les serveurs ou machines virtuelles comme des serveurs physiques. Lors de la migration de machines physiques, l’outil Azure Migrate : Server Migration utilise la même architecture de réplication que la fonctionnalité de récupération d’urgence basée sur un agent du service Azure Site Recovery, et certains des composants utilisés partagent la même base de code. Certains contenus peuvent être liés à la documentation Site Recovery.


## <a name="prerequisites"></a>Prérequis

Avant de commencer ce didacticiel, vous devez :

1. Un abonnement Azure. Créer un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/), si nécessaire. 
1. Installer le [module `Az` Azure PowerShell](/powershell/azure/install-az-ps). 
1. Télécharger les [exemples de scripts PowerShell](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/SQL%20Migration) à partir du référentiel GitHub.

## <a name="prepare-azure"></a>Préparer Azure

Préparer Azure pour la migration avec Migration de serveur.

**Tâche** | **Détails**
--- | ---
**Créer un projet Azure Migrate** | Votre compte Azure doit disposer d’autorisations Contributeur ou Propriétaire pour [créer un projet](../../../migrate/create-manage-projects.md).
**Vérifier les autorisations pour votre compte Azure** | Votre compte Azure doit disposer d’autorisations Contributeur ou Propriétaire sur l’abonnement Azure, des autorisations pour inscrire des applications AAD (Azure Active Directory) et des autorisations Administrateur de l’accès utilisateur sur l’abonnement Azure pour créer un coffre de clés, créer une machine virtuelle et écrire sur un disque managé Azure.
**Configurer un réseau virtuel Azure** | [Configurez](../../../virtual-network/manage-virtual-network.md#create-a-virtual-network) un réseau virtuel Azure. Quand vous effectuez une réplication sur Azure, des machines virtuelles Azure sont créées et jointes au réseau virtuel Azure que vous avez spécifié lors de la configuration de la migration.


Pour vérifier que vous disposez des autorisations appropriées, procédez comme suit : 

1. Dans le portail Azure, ouvrez l’abonnement, puis sélectionnez **Contrôle d’accès (IAM)** .
2. Dans **Vérifier l’accès**, recherchez le compte approprié, puis sélectionnez-le pour afficher les autorisations.
3. Vous devez disposer des autorisations de **Contributeur** ou de **Propriétaire**.
    - Si vous venez de créer un compte Azure gratuit, vous êtes le propriétaire de votre abonnement.
    - Si vous n’êtes pas le propriétaire de l’abonnement, demandez au propriétaire de vous attribuer le rôle.

Si vous devez attribuer des autorisations, suivez les étapes de la section [Préparer un compte d’utilisateur Azure](../../../migrate/tutorial-discover-vmware.md#prepare-an-azure-user-account).


## <a name="prepare-for-migration"></a>Préparation de la migration

Pour préparer la migration du serveur, vous devez vérifier les paramètres du serveur et préparer le déploiement d’une appliance de réplication.

### <a name="check-machine-requirements"></a>Vérifier la configuration requise pour l’ordinateur

Vérifiez que les machines sont conformes aux exigences de migration vers Azure. 

1. [Vérifiez](../../../migrate/migrate-support-matrix-physical-migration.md#physical-server-requirements) les exigences relatives au serveur.
2. Vérifiez que les ordinateurs sources que vous répliquez sur Azure respectent les [exigences des machines virtuelles Azure](../../../migrate/migrate-support-matrix-physical-migration.md#azure-vm-requirements).
1. Certaines sources [Windows](../../../migrate/prepare-for-migration.md#windows-machines) nécessitent quelques modifications supplémentaires. La migration de la source avant d’effectuer ces modifications peut empêcher la machine virtuelle de démarrer dans Azure. Pour certains systèmes d’exploitation, Azure Migrate effectue ces modifications automatiquement. 

### <a name="prepare-for-replication"></a>Préparer pour la réplication 

Azure Migrate : Server Migration utilise une appliance de réplication pour répliquer les machines sur Azure. L’appliance de réplication exécute les composants suivants : 

- **Serveur de configuration** : Le serveur de configuration coordonne la communication entre les ordinateurs locaux et Azure.et gère la réplication des données.
- **Serveur de traitement**: Le serveur de processus fait office de passerelle de réplication. Il reçoit les données de réplication, les optimise grâce à une mise en cache, une compression et un chiffrement, puis les envoie à un compte de stockage de cache dans Azure. 

Préparez le déploiement de l’appliance comme suit :

- Créez un ordinateur Windows Server 2016 pour héberger l’appliance de réplication.  Passez en revue la [configuration requise pour l’ordinateur](../../../migrate/migrate-replication-appliance.md#appliance-requirements).
- L’appliance de réplication utilise MySQL. Passez en revue les [options](../../../migrate/migrate-replication-appliance.md#mysql-installation) d’installation de MySQL sur l’appliance.
- Passez en revue les URL Azure nécessaires à l’appliance de réplication pour accéder aux clouds [publics](../../../migrate/migrate-replication-appliance.md#url-access) et du [secteur public](../../../migrate/migrate-replication-appliance.md#azure-government-url-access).
- Examinez les conditions d’accès aux [ports](../../../migrate/migrate-replication-appliance.md#port-access) pour l’appliance de réplication.

> [!NOTE]
> L’appliance de réplication doit être installée sur un ordinateur autre que la machine source que vous répliquez ou migrez, et non sur un ordinateur sur lequel l’appliance de découverte et d’évaluation Azure Migrate a déjà été installée auparavant.

### <a name="download-replication-appliance-installer"></a>Télécharger le programme d’installation de l’appliance de réplication

Pour télécharger le programme d’installation de l’appliance de réplication, procédez comme suit : 

1. Dans le projet Azure Migrate > **Serveurs**, dans **Azure Migrate : Server Migration**, sélectionnez **Découvrir**.

    ![Détection des machines virtuelles](../../../migrate/media/tutorial-migrate-physical-virtual-machines/migrate-discover.png)

1. Dans **Découvrir des machines** > **Vos machines sont-elles virtualisées ?** , sélectionnez **Physiques ou autres (AWS, GCP, Xen, etc.)** .
1. Dans **Région cible**, sélectionnez la région Azure vers laquelle vous souhaitez migrer les machines.
1. Sélectionnez **Confirmer que la région cible pour la migration est « nom_région »** .
1. Sélectionnez **Créer des ressources**. Un coffre Azure Site Recovery est créé en arrière-plan.
    - Si vous avez déjà configuré la migration avec Azure Migrate Server Migration, l’option Cible ne peut pas être configurée, car les ressources ont déjà été configurées.    
    - Après avoir sélectionné ce bouton, vous ne pouvez plus changer la région cible de ce projet.
    - Toutes les migrations suivantes sont effectuées dans cette région.

1. Dans **Voulez-vous installer une nouvelle appliance de réplication ?** , sélectionnez **Installer une appliance de réplication**.
1. Dans **Télécharger et installer le logiciel de l’appliance de réplication**, téléchargez le programme d’installation de l’appliance et la clé d’inscription. Vous avez besoin de la clé pour inscrire l’appliance. Une fois téléchargée, la clé reste valide pendant 5 jours.

    ![Télécharger le fournisseur](../../../migrate/media/tutorial-migrate-physical-virtual-machines/download-provider.png)

1. Copiez le fichier de configuration de l’appliance et le fichier de clé sur la machine Windows Server 2016 que vous avez créée pour l’appliance.
1. Une fois l’installation terminée, l’Assistant Configuration de l’appliance démarre automatiquement (vous pouvez également le lancer manuellement à l’aide du raccourci cspsconfigtool créé sur le Bureau de la machine de l’appliance). Utilisez l’onglet **Gérer les comptes** de l’Assistant pour créer un compte factice avec les informations suivantes :

   -  « guest » comme nom convivial
   -  « username » comme nom d’utilisateur
   -  « password » comme mot de passe du compte 
   
   Vous allez utiliser ce compte factice lors de la phase Activer la réplication. 

1. Une fois l’installation terminée et l’appliance redémarrée, dans **Découvrir des machines**, sélectionnez la nouvelle appliance dans **Sélectionner un serveur de configuration**, puis **Finaliser l’inscription**. L’option Finaliser l’inscription exécute quelques tâches finales pour préparer l’appliance de réplication.

    ![Finaliser l’inscription](../../../migrate/media/tutorial-migrate-physical-virtual-machines/finalize-registration.png)


## <a name="install-the-mobility-service"></a>Installer le service Mobilité 

Installez l’agent du service Mobility sur les serveurs à migrer. Les programmes d’installation de l’agent sont disponibles dans l’appliance de réplication. Recherchez le programme d’installation approprié, puis installez l’agent sur chaque machine à migrer. 

Pour installer le service Mobility, procédez comme suit : 

1. Connectez-vous à l’appliance de réplication.
2. Accédez au dossier **%ProgramData%\ASR\home\svsystems\pushinstallsvc\repository**.
3. Recherchez le programme d’installation correspondant à la version du système d’exploitation de la machine. Vérifiez quels sont les [systèmes d’exploitation pris en charge](../../../site-recovery/vmware-physical-azure-support-matrix.md#replicated-machines). 
4. Copiez le fichier du programme d’installation sur la machine qui doit faire l’objet d’une migration.
5. Vérifiez que vous disposez de la phrase secrète qui a été générée lorsque vous avez déployé l’appliance.
    - Stockez le fichier dans un fichier texte temporaire sur la machine.
    - Vous pouvez obtenir la phrase secrète dans l’appliance de réplication. Sur la ligne de commande, exécutez **C:\ProgramData\ASR\home\svsystems\bin\genpassphrase.exe -v** pour afficher la phrase secrète.
    - Ne regénérez pas la phrase secrète. Si vous la regénérez, la connectivité sera perdue et vous devrez réinscrire l’appliance de réplication.
    - Dans le paramètre */Platform*, spécifiez *VMware* pour les machines VMware et les machines physiques. 

1. Connectez-vous à l’ordinateur et extrayez le contenu du fichier du programme d’installation dans un dossier local (par exemple, C:\temp). Exécutez ce qui suit dans une invite de commandes d’administration : 

    ```
    ren Microsoft-ASR_UA*Windows*release.exe MobilityServiceInstaller.exe
    MobilityServiceInstaller.exe /q /x:C:\Temp\Extracted
    cd C:\Temp\Extracted
    ```

2. Exécutez le programme d’installation de Mobility Service :

    ```
    UnifiedAgent.exe /Role "MS" /Platform "VmWare" /Silent
    ```

3. Inscrivez l’agent auprès de l’appliance de réplication :

    ```
    cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
    UnifiedAgentConfigurator.exe  /CSEndPoint <replication appliance IP address> /PassphraseFilePath <Passphrase File Path>
    ```

Après l’installation, il peut s’écouler un certain temps avant que les machines découvertes apparaissent dans Azure Migrate : Server Migration. À mesure que des machines virtuelles sont découvertes, le nombre de **Serveurs découverts** augmente.

![Serveurs découverts](../../../migrate/media/tutorial-migrate-physical-virtual-machines/discovered-servers.png)

## <a name="prepare-source-machines"></a>Préparer les ordinateurs sources

Pour préparer les ordinateurs sources, vous aurez besoin d’informations du cluster. 

> [!CAUTION]
> - Conservez la propriété des disques tout au long du processus de réplication et jusqu’au basculement final. En cas de modification de la propriété du disque, il est possible que les volumes soient endommagés et que la réplication doive être redéclenchée. Définissez le propriétaire préféré pour chaque disque afin d’éviter le transfert de la propriété pendant le processus de réplication. 
> - Évitez les mises à jour correctives et les redémarrages du système pendant le processus de réplication pour empêcher le transfert de la propriété des disques.

Pour préparer les ordinateurs sources, procédez comme suit : 

1. **Identifier la propriété du disque :** Connectez-vous à l’un des nœuds du cluster et ouvrez Gestionnaire du cluster de basculement. Identifiez le nœud propriétaire des disques pour déterminer les disques qui doivent être migrés avec chaque serveur.  
2. **Récupérer les informations du cluster :** Exécutez le script `Get-ClusterInfo.ps1` sur un nœud de cluster pour récupérer des informations sur les ressources du cluster. Le script génère le nom du rôle, le nom de la ressource, l’IP et le port de la sonde dans le fichier `Cluster-Config.csv`. Utilisez ce fichier CSV pour créer et attribuer des ressources dans Azure plus loin dans cet article.  
    
   ```powershell
   ./Get-ClusterInfo.ps1
   ```

## <a name="create-load-balancer"></a>Créer un équilibreur de charge

Pour que le cluster et les rôles de cluster répondent correctement aux demandes, un équilibreur de charge Azure est requis. Sans équilibreur de charge, les autres machines virtuelles ne sont pas en mesure d’atteindre l’adresse IP du cluster, car elle n’est pas reconnue comme appartenant au réseau ni au cluster. 

1. Renseignez les colonnes dans le fichier `Cluster-Config.csv` : 

    **En-tête de colonne** | **Description**
    --- | ---
    NewIP | Spécifiez l’adresse IP dans le réseau virtuel Azure (ou sous-réseau) pour chaque ressource dans le fichier CSV.
    ServicePort | Spécifiez le port de service à utiliser par chaque ressource dans le fichier CSV. Pour la ressource de cluster SQL, utilisez la même valeur pour le port de service que pour le port de la sonde dans le fichier CSV. Pour les autres rôles de cluster, les valeurs par défaut utilisées sont 1433, mais vous pouvez continuer à utiliser les numéros de port définis dans votre configuration actuelle. 


1. Exécutez le script `Create-ClusterLoadBalancer.ps1` pour créer l’équilibreur de charge avec les paramètres obligatoires suivants : 

    **Paramètre** | **Type** | **Description**
    --- | --- | ---
    ConfigFilePath | Obligatoire |  Spécifiez le chemin du fichier `Cluster-Config.csv` que vous avez renseigné à l’étape précédente.
    ResourceGroupName | Obligatoire| Spécifiez le nom du groupe de ressources dans lequel l’équilibreur de charge doit être créé. 
    VNetName | Obligatoire| Spécifiez le nom du réseau virtuel Azure auquel l’équilibreur de charge sera associé. 
    SubnetName | Obligatoire| Spécifiez le nom du sous-réseau dans le réseau virtuel Azure auquel l’équilibreur de charge sera associé. 
    VNetResourceGroupName | Obligatoire| Spécifiez le nom du groupe de ressources pour le réseau virtuel Azure auquel l’équilibreur de charge sera associé. 
    Location | Obligatoire| Spécifiez l’emplacement dans lequel l’équilibreur de charge doit être créé. 
    LoadBalancerName | Obligatoire| Spécifiez le nom de l’équilibreur de charge à créer. 


    ```powershell
    ./Create-ClusterLoadBalancer.ps1 -ConfigFilePath ./cluster-config.csv -ResourceGroupName $resoucegroupname -VNetName $vnetname -subnetName $subnetname -VnetResourceGroupName $vnetresourcegroupname -Location “eastus” -LoadBalancerName $loadbalancername
    ```

## <a name="replicate-machines"></a>Répliquer les machines

À présent, sélectionnez les machines pour la migration. Vous pouvez répliquer ensemble jusqu’à 10 machines. Si vous avez besoin d’en répliquer davantage, répliquez-les simultanément par lots de 10.

1. Dans le projet Azure Migrate > **Serveurs**, **Azure Migrate : Server Migration**, sélectionnez **Répliquer**.

    ![Capture d’écran de l’écran Azure Migrate - Serveurs montrant le bouton Répliquer sélectionné dans Azure Migrate : Migration du serveur sous Outils de migration.](../../../migrate/media/tutorial-migrate-physical-virtual-machines/select-replicate.png)

1. Dans **Répliquer** > **Paramètres de la source** > **Vos machines sont-elles virtualisées ?** , sélectionnez **Physiques ou autres (AWS, GCP, Xen, etc.)** .
1. Dans **Appliance locale**, sélectionnez le nom de l’appliance Azure Migrate que vous avez configurée.
1. Dans **Process Server**, sélectionnez le nom de l’appliance de réplication.
1. Dans **Informations d’identification de l’invité**, sélectionnez le compte factice créé précédemment au cours de la [configuration du programme d’installation de la réplication](#download-replication-appliance-installer). Sélectionnez ensuite **Suivant : Machines virtuelles**.

    ![Capture d’écran de l’onglet Paramètres de la source dans l’écran Répliquer avec le champ Informations d’identification de l’invité en surbrillance.](../../../migrate/media/tutorial-migrate-physical-virtual-machines/source-settings.png)

1. Dans **Machines virtuelles**, dans **Importer les paramètres de migration à partir d’une évaluation ?** , conservez le paramètre par défaut **Non, je vais spécifier les paramètres de migration manuellement**.
1. Cochez chaque machine virtuelle devant faire l’objet d’une migration. Ensuite, sélectionnez **Next: Paramètres de la cible**.

    ![Sélectionner les machines virtuelles](../../../migrate/media/tutorial-migrate-physical-virtual-machines/select-vms.png)


1. Dans **Paramètres de la cible**, sélectionnez l’abonnement et la région cible vers laquelle vous allez migrer, puis spécifiez le groupe de ressources dans lequel les machines virtuelles Azure résideront après la migration.
1. Dans **Réseau virtuel**, sélectionnez le réseau virtuel/sous-réseau Azure auquel les machines virtuelles Azure seront jointes après la migration.
1. Dans **Options de disponibilité**, sélectionnez :
    -  Zone de disponibilité pour épingler la machine migrée à une Zone de disponibilité spécifique dans la région. Utilisez cette option pour distribuer les serveurs qui forment une couche Application à plusieurs nœuds entre des Zones de disponibilité. Si vous sélectionnez cette option, vous devez spécifier la Zone de disponibilité à utiliser pour chaque machine sélectionnée dans l’onglet Calcul. Cette option est disponible uniquement si la région cible sélectionnée pour la migration prend en charge les Zones de disponibilité
    -  Groupe à haute disponibilité pour placer la machine migrée dans un groupe à haute disponibilité. Pour utiliser cette option, le groupe de ressources cible qui a été sélectionné doit avoir un ou plusieurs groupes à haute disponibilité.
    - Aucune option de redondance de l’infrastructure n’est requise si vous n’avez pas besoin de ces configurations de disponibilité pour les machines migrées.
    
1. Dans **Type de chiffrement de disque**, sélectionnez :
    - Chiffrement au repos avec une clé gérée par la plateforme
    - Chiffrement au repos avec une clé gérée par le client
    - Chiffrement double avec des clés gérées par la plateforme et des clés gérées par le client

    > [!NOTE]
    > Pour répliquer des machines virtuelles avec une clé gérée par le client, vous devez [créer un jeu de chiffrement de disque](../../../virtual-machines/disks-enable-customer-managed-keys-portal.md#set-up-your-disk-encryption-set) sous le groupe de ressources cible. Un objet de jeu de chiffrement de disque mappe les disques managés à un coffre de clés contenant les clés gérées par le client à utiliser pour le chiffrement côté serveur.
  
1. Dans **Azure Hybrid Benefit** :

    - Sélectionnez **Non** si vous ne souhaitez pas appliquer Azure Hybrid Benefit. Sélectionnez ensuite **Suivant**.
    - Sélectionnez **Oui** si vous avez des machines Windows Server couvertes par des abonnements Software Assurance ou Windows Server actifs et que vous souhaitez appliquer l’avantage aux machines que vous migrez. Sélectionnez ensuite **Suivant**.

    :::image type="content" source="../../../migrate/media/tutorial-migrate-vmware/target-settings.png" alt-text="Paramètres de la cible":::

1. Dans **Calcul**, vérifiez le nom de la machine virtuelle, sa taille, le type de disque du système d’exploitation et la configuration de la disponibilité (si elle a été sélectionnée à l’étape précédente). Les machines virtuelles doivent satisfaire aux [exigences d’Azure](../../../migrate/migrate-support-matrix-physical-migration.md#azure-vm-requirements).

    - **Taille de la machine virtuelle** : si vous suivez les recommandations de l’évaluation, la liste déroulante Taille de la machine virtuelle affiche la taille recommandée. Sinon, Azure Migrate choisit une taille qui correspond à la taille la plus proche dans l’abonnement Azure. Vous pouvez également choisir une taille manuelle dans **Taille de la machine virtuelle Azure**.
    - **Disque de système d’exploitation** : spécifiez le disque du système d’exploitation (démarrage) pour la machine virtuelle. Le disque du système d’exploitation est le disque qui contient le chargeur de démarrage et le programme d’installation du système d’exploitation.
    - **Zone de disponibilité** : Spécifiez la zone de disponibilité à utiliser.
    - **Groupe à haute disponibilité** : Spécifiez le groupe à haute disponibilité à utiliser.

    ![Paramètres de capacité de calcul](../../../migrate/media/tutorial-migrate-physical-virtual-machines/compute-settings.png)

1. Dans **Disques**, indiquez si les disques de machine virtuelle doivent être répliqués sur Azure, puis sélectionnez le type de disque (SSD/HDD standard ou disques managés Premium) dans Azure. Sélectionnez ensuite **Suivant**.
    - Utilisez la liste que vous avez créée précédemment pour sélectionner les disques à répliquer avec chaque serveur. Excluez les autres disques de la réplication.
   

    ![Paramètres des disques](../../../migrate/media/tutorial-migrate-physical-virtual-machines/disks.png)

1. Dans **Passer en revue et démarrer la réplication**, examinez les paramètres, puis sélectionnez **Répliquer** pour lancer la réplication initiale des serveurs.

> [!NOTE]
> Vous pouvez mettre à jour les paramètres de réplication à tout moment avant le démarrage de la réplication (**Gérer** > **Réplication des machines**). Vous ne pouvez pas changer les paramètres après le démarrage de la réplication.

## <a name="track-and-monitor"></a>Suivre et superviser

La réplication se déroule dans l’ordre suivant : 

- Quand vous sélectionnez **Répliquer**, un travail _Démarrer la réplication_ commence. 
- Une fois le travail _Démarrer la réplication_ terminé, les machines commencent leur réplication initiale vers Azure.
- Au terme de la réplication initiale, la réplication différentielle commence. Les modifications incrémentielles apportées aux disques locaux sont répliquées régulièrement sur les disques de réplica dans Azure.
- Une fois la réplication initiale terminée, configurez les éléments Calcul et Réseau pour chaque machine virtuelle. Les clusters ont généralement plusieurs cartes réseau, mais une seule carte réseau est requise pour la migration (définissez les autres comme ne pas créer).

Vous pouvez suivre l’état du travail dans les notifications du portail.

Vous pouvez superviser l’état de la réplication en sélectionnant **Réplication de serveurs** dans **Azure Migrate : Server Migration**.
![Superviser la réplication](../../../migrate/media/tutorial-migrate-physical-virtual-machines/replicating-servers.png)


## <a name="migrate-vms"></a>Migrer des machines virtuelles

Une fois les machines répliquées, elles sont prêtes pour la migration. Pour migrer vos serveurs, procédez comme suit : 

1. Dans le projet Azure Migrate > **Serveurs** > **Azure Migrate : Server Migration**, sélectionnez **Réplication de serveurs**.

    ![Réplication de serveurs](../../../migrate/media/tutorial-migrate-physical-virtual-machines/replicate-servers.png)

1. Pour vous assurer que le serveur migré est synchronisé avec le serveur source, arrêtez la ressource SQL Server (dans **Gestionnaire du cluster de basculement** > **Rôles** > **Autres ressources**) tout en veillant à ce que les disques de cluster soient en ligne.   
1. Dans **Réplication des machines** > sélectionnez le nom du serveur > **Vue d’ensemble**, assurez-vous que le timestamp de la dernière synchronisation est le suivant : vous avez arrêté la ressource SQL Server sur les serveurs à migrer avant de passer à l’étape suivante. Ce processus doit prendre quelques minutes seulement. 
1. Dans **Réplication des machines**, cliquez avec le bouton droit sur la machine virtuelle > **Migrer**.
1. Dans **Migrer** > **Arrêter les machines virtuelles et effectuer une migration planifiée sans perte de données**, sélectionnez **Non** > **OK**.
   
   > [!NOTE]
   > Pour la migration de serveurs physiques, l’arrêt de la machine source n’est pas pris en charge automatiquement. Il est recommandé de fermer l’application dans le cadre de la fenêtre de migration (ne laissez pas les applications accepter de connexions), puis de lancer la migration (le serveur doit continuer à fonctionner pour que les modifications restantes puissent être synchronisées avant la fin de la migration).

1. Un travail de migration démarre pour la machine virtuelle. Suivez le travail dans les notifications Azure.
1. Une fois le travail terminé, vous pouvez afficher et gérer la machine virtuelle à partir de la page **Machines virtuelles**.

## <a name="reconfigure-cluster"></a>Reconfigurer le cluster 

Une fois vos machines virtuelles migrées, reconfigurez le cluster. Suivez les étapes ci-dessous : 

1. Arrêtez les serveurs migrés dans Azure.
1. Ajoutez les machines migrées au pool principal de l’équilibreur de charge. Accédez à **Équilibreur de charge** > **Pools principaux** > sélectionnez le pool principal > **Ajouter des machines migrées**. 

1. Reconfigurez les disques migrés des serveurs en tant que disques partagés en exécutant le script `Create-SharedDisks.ps1`. Le script est interactif et demande une liste de machines, puis affiche les disques disponibles à extraire (uniquement les disques de données). Vous serez invité une fois à sélectionner les machines qui contiennent les lecteurs à transformer en disques partagés. Une fois la sélection effectuée, vous serez invité à nouveau, une fois par machine, à choisir les disques spécifiques. 

   **Paramètre** | **Type** | **Description**
   --- | --- | ---
   ResourceGroupName | Obligatoire |  Spécifiez le nom du groupe de ressources contenant les serveurs migrés.
   NumberofNodes | Facultatif | Spécifiez le nombre de nœuds dans votre instance de cluster de basculement. Ce paramètre est utilisé pour identifier la référence SKU appropriée pour les disques partagés à créer. Par défaut, le script suppose que le nombre de nœuds du cluster est 2.   
   DiskNamePrefix | Facultatif | Spécifiez le préfixe que vous souhaitez ajouter aux noms de vos disques partagés. 
   
   ```powershell
   ./Create-SharedDisks.ps1 -ResourceGroupName $resoucegroupname -NumberofNodes $nodesincluster -DiskNamePrefix $disknameprefix 
   ```

1. Attachez les disques partagés aux serveurs migrés en exécutant le script `Attach-SharedDisks.ps1`. 

   **Paramètre** | **Type** |**Description**
   --- | ---  | ---
   ResourceGroupName | Obligatoire | Spécifiez le nom du groupe de ressources contenant les serveurs migrés.
   StartingLunNumber | Facultatif |Spécifiez le numéro d’unité logique de départ qui est disponible pour les disques partagés à attacher. Par défaut, le script tente d’attacher des disques partagés au numéro d’unité logique à partir de 0.  
   
   ```powershell
   ./Attach-ShareDisks.ps1 -ResourceGroupName $resoucegroupname 
   ```

1. Démarrez les serveurs migrés dans Azure et connectez-vous à n’importe quel nœud. 

1. Copiez le fichier `ClusterConfig.csv` et exécutez le script `Update-ClusterConfig.ps1` en passant le fichier CSV en tant que paramètre. Cela garantira que les ressources du cluster sont mises à jour avec la nouvelle configuration pour que le cluster fonctionne dans Azure. 

   ```powershell
   ./Update-ClusterConfig.ps1 -ConfigFilePath $filepath
   ```

Votre instance de cluster de basculement SQL Server est prête. 

## <a name="complete-the-migration"></a>Effectuer la migration

1. Une fois la migration terminée, cliquez avec le bouton droit sur la machine virtuelle > **Arrêter la migration**. Cette opération effectue les actions suivantes :
    - Arrête la réplication pour l’ordinateur local.
    - Supprime l’ordinateur du nombre **Réplication de serveurs** dans Azure Migrate : Server Migration.
    - Nettoie les informations d’état de réplication pour la machine.
1. Installez l’agent [Windows](../../../virtual-machines/extensions/agent-windows.md) de machine virtuelle Azure sur les machines migrées.
1. Effectuez les éventuels ajustements post-migration de l’application, comme la mise à jour des chaînes de connexion de base de données et les configurations du serveur web.
1. Effectuez les tests finaux de réception de l’application et de la migration sur l’application migrée qui s’exécute maintenant dans Azure.
1. Réduisez le trafic vers l’instance de machine virtuelle Azure migrée.
1. Supprimez les machines virtuelles locales de votre inventaire des machines virtuelles locales.
1. Supprimez les machines virtuelles locales des sauvegardes locales.
1. Mettez à jour la documentation interne en y mentionnant le nouvel emplacement et la nouvelle adresse IP des machines virtuelles Azure. 

## <a name="post-migration-best-practices"></a>Bonnes pratiques après la migration

- Pour SQL Server :
    -  Installez l’[extension SQL Server IaaS Agent](../../virtual-machines/windows/sql-server-iaas-agent-extension-automate-management.md) pour automatiser les tâches de gestion et d’administration. 
    - [Optimisez](../../virtual-machines/windows/performance-guidelines-best-practices-checklist.md) les performances de SQL Server sur les machines virtuelles Azure. 
    - Prenez connaissance de la [tarification](../../virtual-machines/windows/pricing-guidance.md#free-licensed-sql-server-editions) de SQL Server sur Azure. 
- Pour une meilleure résilience :
    - Sécurisez les données en sauvegardant les machines virtuelles Azure avec le [service Sauvegarde Azure](../../../backup/quick-backup-vm-portal.md). 
    - Maintenez les charges de travail en cours d’exécution et disponibles en permanence en répliquant les machines virtuelles Azure vers une région secondaire avec [Site Recovery](../../../site-recovery/azure-to-azure-tutorial-enable-replication.md).
- Pour renforcer la sécurité :
    - Verrouillez et limitez l’accès du trafic entrant grâce à l’[administration juste-à-temps de Microsoft Defender pour le cloud](../../../security-center/security-center-just-in-time.md).
    - Limitez le trafic réseau vers les points de terminaison de gestion avec des [groupes de sécurité réseau](../../../virtual-network/network-security-groups-overview.md).
    - Déployez [Azure Disk Encryption](../../../security/fundamentals/azure-disk-encryption-vms-vmss.md) pour sécuriser les disques, et protégez les données contre le vol et les accès non autorisés.
    - Pour en savoir plus sur la [sécurisation des ressources IaaS](https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/), consultez le site [Microsoft Defender pour le cloud](https://azure.microsoft.com/services/security-center/).
- Pour la surveillance et la gestion :
    - Envisagez de déployer [Azure Cost Management](../../../cost-management-billing/cost-management-billing-overview.md) pour surveiller l’utilisation et les coûts des ressources.


## <a name="next-steps"></a>Étapes suivantes

Examinez le [parcours de migration cloud](/azure/architecture/cloud-adoption/getting-started/migrate) dans le framework d’adoption du cloud Azure.
