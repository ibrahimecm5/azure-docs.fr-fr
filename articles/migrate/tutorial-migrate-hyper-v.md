---
title: Migrer des machines virtuelles Hyper-V vers Azure avec la migration de serveurs Azure Migrate | Microsoft Docs
description: Découvrir comment migrer des machines virtuelles Hyper-V locales vers Azure avec la migration de serveurs Azure Migrate
author: bsiva
ms.author: bsiva
ms.manager: abhemraj
ms.topic: tutorial
ms.date: 03/18/2021
ms.custom:
- MVC
- fasttrack-edit
ms.openlocfilehash: 4588e782d8d397d09b6d66f4855884c859e69e2b
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132278072"
---
# <a name="migrate-hyper-v-vms-to-azure"></a>Migrer des machines virtuelles Hyper-V vers Azure

Cet article explique comment effectuer une migration de machines virtuelles Hyper-V locales vers Azure avec l’outil [Azure Migrate : Migration de serveur](migrate-services-overview.md#azure-migrate-server-migration-tool).

Ce tutoriel est le troisième d’une série qui explique comment évaluer et migrer des machines virtuelles vers Azure.

> [!NOTE]
> Les tutoriels vous montrent le chemin de déploiement le plus simple pour un scénario donné afin que vous puissiez configurer rapidement une preuve de concept. Ils utilisent des options par défaut, le cas échéant, et ne montrent pas tous les paramètres et chemins possibles.

 Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Ajoutez l’outil Azure Migrate : Migration de serveur.
> * Découvrir les machines virtuelles que vous souhaitez migrer
> * Démarrer la réplication des machines virtuelles
> * Exécuter une migration de test pour vérifier que tout fonctionne comme prévu.
> * Exécuter une migration de machine virtuelle complète

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/) avant de commencer.


## <a name="prerequisites"></a>Prérequis


Avant de commencer ce didacticiel, vous devez :

1. [Examiner](hyper-v-migration-architecture.md) l’architecture de la migration Hyper-V.
1. [Passez en revue](migrate-support-matrix-hyper-v-migration.md#hyper-v-host-requirements) la configuration requise de l’hôte Hyper-V pour la migration et les URL Azure auxquelles les hôtes et les clusters Hyper-V ont besoin d’accéder pour la migration de machine virtuelle.
1. [Passez en revue](migrate-support-matrix-hyper-v-migration.md#hyper-v-vms) la configuration requise des machines virtuelles Hyper-V que vous souhaitez migrer vers Azure.
1. Nous vous recommandons d’[évaluer les machines virtuelles Hyper-V](tutorial-assess-hyper-v.md) avant de les migrer vers Azure, mais cela n’est pas une obligation.
1. Accéder au projet déjà créé ou [créer un autre projet.](./create-manage-projects.md)
1. Vérifiez les autorisations de votre compte Azure – Votre compte Azure a besoin d’autorisations pour créer une machine virtuelle et écrire sur un disque managé Azure.

## <a name="download-the-provider"></a>Télécharger le fournisseur

Pour la migration de machines virtuelles Hyper-V, Azure Migrate : Migration de serveur installe des fournisseurs de logiciels (fournisseur Microsoft Azure Site Recovery et agent Microsoft Azure Recovery Services) sur des nœuds de cluster ou hôtes Hyper-V. Notez que l’[appliance Azure Migrate](migrate-appliance.md) n’est pas utilisée dans le cadre de la migration Hyper-V.

1. Dans le projet Azure Migrate > **Serveurs**, dans **Azure Migrate : Server Migration**, cliquez sur **Découvrir**.
1. Dans **Découvrir des machines** > **Vos machines sont-elles virtualisées ?** , sélectionnez **Oui, avec Hyper-V**.
1. Dans **Région cible**, sélectionnez la région Azure vers laquelle vous souhaitez migrer les machines.
1. Sélectionnez **Confirmer que la région cible pour la migration est « nom_région »** .
1. Cliquez sur **Créer des ressources**. Un coffre Azure Site Recovery est créé en arrière-plan.
    - Si vous avez déjà configuré la migration avec Azure Migrate Server Migration, cette option ne s’affiche pas car les ressources ont été configurées précédemment.
    - Après avoir cliqué sur ce bouton, vous ne pouvez plus changer la région cible de ce projet.
    - Toutes les migrations suivantes sont effectuées dans cette région.

1. Dans **Préparer les serveurs hôtes Hyper-V**, téléchargez le fournisseur de réplication Hyper-V et le fichier de clé d’inscription.
    - La clé d’inscription est nécessaire pour inscrire l’hôte Hyper-V auprès d’Azure Migrate Server Migration.
    - Une fois générée, la clé reste valide pendant 5 jours.

    ![Télécharger le fournisseur et la clé](./media/tutorial-migrate-hyper-v/download-provider-hyper-v.png)

1. Copiez le fichier de configuration du fournisseur et le fichier de clé d’inscription sur chaque hôte Hyper-V (ou nœud de cluster) exécutant des machines virtuelles que vous voulez répliquer.  

## <a name="install-and-register-the-provider"></a>Installer et inscrire le fournisseur

Copiez le fichier de configuration du fournisseur et le fichier de clé d’inscription sur chaque hôte Hyper-V (ou nœud de cluster) exécutant des machines virtuelles que vous voulez répliquer. Pour installer et inscrire le fournisseur, suivez les étapes ci-dessous à l’aide de l’interface utilisateur ou des commandes :

# <a name="using-ui"></a>[Utilisation de l’interface utilisateur](#tab/UI)

Exécutez le fichier de configuration du fournisseur sur chaque hôte, comme décrit ci-dessous :

1. Cliquez sur l’icône de fichier dans la barre des tâches pour ouvrir le dossier dans lequel sont téléchargés le fichier du programme d’installation et la clé d’inscription.
1. Sélectionnez le fichier **AzureSiteRecoveryProvider.exe**.
    - Dans l’Assistant Installation du fournisseur, assurez-vous que l’option **Activé (recommandé)** est cochée, puis cliquez sur **Suivant**.
    - Sélectionnez **Installer** pour accepter le dossier d’installation par défaut.
    - Sélectionnez **Inscrire** pour inscrire ce serveur dans le coffre Azure Site Recovery.
    - Cliquez sur **Parcourir**.
    - Recherchez la clé d’inscription, puis cliquez sur **Ouvrir**.
    - Cliquez sur **Suivant**.
    - Assurez-vous que l’option **Se connecter directement à Azure Site Recovery sans serveur proxy** est sélectionnée, puis cliquez sur **Suivant**.
    - Cliquez sur **Terminer**.


# <a name="using-commands"></a>[Utilisation des commandes](#tab/commands) 

Exécutez les commandes suivantes sur chaque hôte, tel que décrit ci-dessous :

1. Procédez de la façon suivante pour extraire le contenu du fichier du programme d’installation dans un dossier local (par exemple, .\Temp) de la machine :

    ```
     AzureSiteRecoveryProvider.exe /q /x:.\Temp\Extracted
    ```

1. Accédez au dossier contenant tous les fichiers extraits.

    ```
    cd .\Temp\Extracted
    ```
1. Installez le fournisseur de réplication Hyper-V. Les résultats sont enregistrés dans %Programdata%\ASRLogs\DRASetupWizard.log.

    ```
    .\setupdr.exe /i 
    ```

1. Inscrivez l’hôte Hyper-V à Azure Migrate.

    ```
    "C:\Program Files\Microsoft Azure Site Recovery Provider\DRConfigurator.exe" /r /Credentials <key file path>
    ```

    **Configurer les règles du proxy :** si vous devez vous connecter à Internet via un proxy, utilisez les paramètres facultatifs /proxyaddress et /proxyport pour spécifier l’adresse du proxy (sous la forme http://ProxyIPAddress) ) et le port d’écoute du proxy. Pour le proxy authentifié, vous pouvez utiliser les paramètres facultatifs /proxyusername et /proxypassword.

    ``` 
   "C:\Program Files\Microsoft Azure Site Recovery Provider\DRConfigurator.exe" /r [/proxyaddress http://ProxyIPAddress] [/proxyport portnumber] [/proxyusername username] [/proxypassword password]
    ```
 
    **Configurez les règles de contournement du proxy :** pour configurer des règles de contournement de proxy, utilisez le paramètre facultatif /AddBypassUrls et fournissez une ou plusieurs URL de contournement pour le proxy séparé par « ; » et exécutez les commandes suivantes :

    ```
    "C:\Program Files\Microsoft Azure Site Recovery Provider\DRConfigurator.exe" /r [/proxyaddress http://ProxyIPAddress] [/proxyport portnumber] [/proxyusername username] [/proxypassword password] [/AddBypassUrls URLs]
    ```
    et
    ```
    "C:\Program Files\Microsoft Azure Site Recovery Provider\DRConfigurator.exe" /configure /AddBypassUrls URLs 
    ```
---

Après avoir installé le fournisseur sur les hôtes, accédez au Portail Azure et dans **Découvrir des machines**, cliquez sur **Finaliser l’inscription**.

 ![Finaliser l’inscription](./media/tutorial-migrate-hyper-v/finalize-registration.png) 

La finalisation de l’inscription peut prendre jusqu’à 15 minutes, jusqu’à ce que les machines virtuelles découvertes apparaissent dans Azure Migrate Server Migration. À mesure que des machines virtuelles sont découvertes, le nombre de **Serveurs découverts** augmente.

 ![Serveurs découverts](./media/tutorial-migrate-hyper-v/discovered-servers.png)

## <a name="replicate-hyper-v-vms"></a>Répliquer des machines virtuelles Hyper-V

Une fois la découverte terminée, vous pouvez commencer la réplication des machines virtuelles Hyper-V vers Azure.

> [!NOTE]
> Vous pouvez répliquer ensemble jusqu’à 10 machines. Si vous avez besoin d’en répliquer davantage, répliquez-les simultanément par lots de 10.

1. Dans le projet Azure Migrate > **Serveurs**, **Azure Migrate : Server Migration**, cliquez sur **Répliquer**.
1. Dans **Répliquer** > **Paramètres de la source** > **Vos machines sont-elles virtualisées ?** , sélectionnez **Oui, avec Hyper-V**. Cliquez ensuite sur **Suivant : Machines virtuelles**.
1. Dans **Machines virtuelles**, sélectionnez les machines à répliquer.
    - Si vous avez exécuté une évaluation des machines virtuelles, vous pouvez appliquer les recommandations relatives au dimensionnement et au type de disque (Premium/Standard) des machines virtuelles qui apparaissent dans les résultats de l’évaluation. Pour ce faire, dans **Importer les paramètres de migration à partir d’une évaluation Azure Migrate ?** , sélectionnez l’option **Oui**.
    - Si vous n’avez pas exécuté d’évaluation ou que vous ne souhaitez pas utiliser les paramètres de l’évaluation, sélectionnez l’option **Non**.
    - Si vous avez choisi d’utiliser l’évaluation, sélectionnez le groupe de machines virtuelles et le nom de l’évaluation.

        ![Sélectionner l’évaluation](./media/tutorial-migrate-hyper-v/select-assessment.png)

1. Dans **Machines virtuelles**, recherchez les machines virtuelles en fonction des besoins et cochez chaque machine virtuelle que vous souhaitez migrer. Cliquez ensuite sur **Suivant : Paramètres de la cible**.

    ![Sélectionner les machines virtuelles](./media/tutorial-migrate-hyper-v/select-vms.png)

1. Dans **Paramètres de la cible**, sélectionnez la région cible vers laquelle vous allez effectuer la migration, l’abonnement, puis le groupe de ressources dans lequel les machines virtuelles Azure résideront après la migration.
1. Dans **Compte de stockage de réplication**, sélectionnez le compte Stockage Azure dans lequel les données répliquées seront stockées dans Azure.
1. Dans **Réseau virtuel**, sélectionnez le réseau virtuel/sous-réseau Azure auquel les machines virtuelles Azure seront jointes après la migration.
1. Dans **Options de disponibilité**, sélectionnez :
    -  Zone de disponibilité pour épingler la machine migrée à une Zone de disponibilité spécifique dans la région. Utilisez cette option pour distribuer les serveurs qui forment une couche Application à plusieurs nœuds entre des Zones de disponibilité. Si vous sélectionnez cette option, vous devez spécifier la Zone de disponibilité à utiliser pour chaque machine sélectionnée dans l’onglet Calcul. Cette option est disponible uniquement si la région cible sélectionnée pour la migration prend en charge les Zones de disponibilité
    -  Groupe à haute disponibilité pour placer la machine migrée dans un groupe à haute disponibilité. Pour utiliser cette option, le groupe de ressources cible qui a été sélectionné doit avoir un ou plusieurs groupes à haute disponibilité.
    - Aucune option de redondance de l’infrastructure n’est requise si vous n’avez pas besoin de ces configurations de disponibilité pour les machines migrées.
1. Dans **Azure Hybrid Benefit** :

    - Sélectionnez **Non** si vous ne souhaitez pas appliquer Azure Hybrid Benefit. Cliquez ensuite sur **Suivant**.
    - Sélectionnez **Oui** si vous avez des machines Windows Server couvertes par des abonnements Software Assurance ou Windows Server actifs et que vous souhaitez appliquer l’avantage aux machines que vous migrez. Cliquez ensuite sur **Suivant**.

    ![Paramètres de la cible](./media/tutorial-migrate-hyper-v/target-settings.png)

1. Dans **Calcul**, vérifiez le nom de la machine virtuelle, sa taille, le type de disque du système d’exploitation et la configuration de la disponibilité (si elle a été sélectionnée à l’étape précédente). Les machines virtuelles doivent satisfaire aux [exigences d’Azure](migrate-support-matrix-hyper-v-migration.md#azure-vm-requirements).

    - **Taille de la machine virtuelle** : si vous utilisez les recommandations de l’évaluation, la liste déroulante Taille de la machine virtuelle contient la taille recommandée. Sinon, Azure Migrate choisit une taille qui correspond à la taille la plus proche dans l’abonnement Azure. Vous pouvez également choisir une taille manuelle dans **Taille de la machine virtuelle Azure**.
    - **Disque de système d’exploitation** : spécifiez le disque du système d’exploitation (démarrage) pour la machine virtuelle. Le disque du système d’exploitation est le disque qui contient le chargeur de démarrage et le programme d’installation du système d’exploitation.
    - **Groupe à haute disponibilité** : si la machine virtuelle doit se trouver dans un groupe à haute disponibilité Azure après la migration, spécifiez-le ici. Ce groupe doit figurer dans le groupe de ressources cible que vous spécifiez pour la migration.

    ![Paramètres de capacité de calcul de la machine virtuelle](./media/tutorial-migrate-hyper-v/compute-settings.png)

1. Dans **Disques**, spécifiez les disques de machine virtuelle qui doivent être répliqués sur Azure. Cliquez ensuite sur **Suivant**.
    - Vous pouvez exclure des disques de la réplication.
    - Si vous excluez des disques, ils ne seront pas présents sur la machine virtuelle Azure après la migration.

    ![Capture d’écran montrant l’onglet Disques de la boîte de dialogue Répliquer.](./media/tutorial-migrate-hyper-v/disks.png)

1. Dans **Passer en revue et démarrer la réplication**, passez en revue les paramètres, puis cliquez sur **Répliquer** pour démarrer la réplication initiale pour les serveurs.

> [!NOTE]
> Vous pouvez mettre à jour les paramètres de réplication à tout moment avant le démarrage de la réplication, dans **Gérer** > **Réplication des machines**. Vous ne pouvez pas changer les paramètres après le démarrage de la réplication.

## <a name="provision-for-the-first-time"></a>Provisionnement initial

S’il s’agit de la première machine virtuelle que vous répliquez dans le projet Azure Migrate, Azure Migrate : Server Migration provisionne automatiquement ces ressources dans le même groupe de ressources que le projet.
- **Compte de stockage de cache** : le logiciel du fournisseur Azure Site Recovery installé sur les hôtes Hyper-V charge les données de réplication pour les machines virtuelles configurées pour la réplication sur un compte de stockage (appelé compte de stockage de cache ou compte de stockage de journal) dans votre abonnement. Le service Azure Migrate copie ensuite les données de réplication chargées à partir du compte de stockage vers les disques managés de réplica correspondant à la machine virtuelle. Le compte de stockage de cache doit être spécifié lors de la configuration de la réplication pour une machine virtuelle. Le portail Azure Migrate en crée un automatiquement pour le projet Azure Migrate quand la réplication est configurée pour la première fois dans le projet.

## <a name="track-and-monitor"></a>Suivre et superviser


- Quand vous cliquez sur **Répliquer**, un travail Démarrer la réplication commence.
- Une fois le travail Démarrer la réplication terminé, les machines commencent leur réplication initiale vers Azure.
- Au terme de la réplication initiale, la réplication différentielle commence. Les changements incrémentiels apportés aux disques locaux sont répliqués régulièrement vers Azure.

Vous pouvez suivre l’état du travail dans les notifications du portail.

Vous pouvez superviser l’état de la réplication en cliquant sur **Réplication de serveurs** dans **Azure Migrate : Server Migration**.
![Superviser la réplication](./media/tutorial-migrate-hyper-v/replicating-servers.png)



## <a name="run-a-test-migration"></a>Exécuter un test de migration


Quand la réplication différentielle commence, vous pouvez exécuter une migration de test avec les machines virtuelles avant d’exécuter une migration complète vers Azure. Avant la migration, nous vous recommandons vivement d’effectuer cette opération au moins une fois pour chaque machine.

- L’exécution d’une migration de test permet de vérifier que la migration fonctionne comme prévu, sans impact sur les machines locales qui restent opérationnelles et poursuivent la réplication.
- Une migration de test simule la migration en créant une machine virtuelle Azure à l’aide de données répliquées (il s’agit en général d’une migration vers un réseau virtuel Azure hors production dans votre abonnement Azure).
- Vous pouvez utiliser la machine virtuelle Azure de test répliquée pour valider la migration, effectuer des tests d’applications et résoudre les éventuels problèmes avant la migration complète.

Effectuez une migration de test de la façon suivante :


1. Dans **Objectifs de migration** > **Serveurs** > **Azure Migrate : Server Migration**, cliquez sur **Tester les serveurs migrés**.

     ![Tester les serveurs migrés](./media/tutorial-migrate-hyper-v/test-migrated-servers.png)

1. Cliquez avec le bouton droit sur la machine virtuelle à tester, puis cliquez sur **Migration de test**.

    ![Migration de test](./media/tutorial-migrate-hyper-v/test-migrate.png)

1. Dans **Migration de test**, sélectionnez le réseau virtuel Azure dans lequel la machine virtuelle Azure se trouvera après la migration. Nous vous recommandons d’utiliser un réseau virtuel hors production.
1. Le travail **Migration de test** démarre. Supervisez le travail dans les notifications du portail.
1. Une fois la migration terminée, affichez la machine virtuelle Azure migrée dans **Machines virtuelles** dans le portail Azure. Le nom de la machine a le suffixe **-Test**.
1. Une fois le test terminé, cliquez avec le bouton droit sur la machine virtuelle Azure dans **Réplication des machines**, puis cliquez sur **Nettoyer la migration de test**.

    ![Nettoyer la migration](./media/tutorial-migrate-hyper-v/clean-up.png)
    > [!NOTE]
    > Vous pouvez désormais inscrire vos serveurs exécutant SQL Server auprès du fournisseur de ressources SQL Server sur des machines virtuelles Azure pour bénéficier de la mise à jour corrective automatisée, de la sauvegarde automatisée et de la gestion simplifiée des licences à l’aide de l’extension SQL IaaS Agent.
    >- Sélectionnez **Gérer** > **Réplication de serveurs** > **Machine contenant SQL Server** > **Calcul et réseau**, puis sélectionnez **oui** pour effectuer l’inscription auprès du fournisseur de ressources SQL Server sur des machines virtuelles Azure.
    >- Sélectionnez Azure Hybrid Benefit pour SQL Server si vous disposez d’instances de SQL Server couvertes par des abonnements Software Assurance ou SQL Server actifs, et si vous souhaitez appliquer cet avantage aux machines que vous migrez.

## <a name="migrate-vms"></a>Migrer des machines virtuelles

Après avoir vérifié que la migration de test fonctionne comme prévu, vous pouvez migrer des machines locales.

1. Dans le projet Azure Migrate > **Serveurs** > **Azure Migrate : Server Migration**, cliquez sur **Réplication de serveurs**.

    ![Réplication de serveurs](./media/tutorial-migrate-hyper-v/replicate-servers.png)

1. Dans **Réplication des machines**, cliquez avec le bouton droit sur la machine virtuelle > **Migrer**.
1. Dans **Migrer** > **Arrêter les machines virtuelles et effectuer une migration planifiée sans perte de données**, sélectionnez **Oui** > **OK**.
    - Par défaut, Azure Migrate arrête la machine virtuelle locale et exécute une réplication à la demande pour synchroniser tout changement apporté à la machine virtuelle depuis la dernière réplication. Cela permet d’éviter toute perte de données.
    - Si vous ne souhaitez pas arrêter la machine virtuelle, sélectionnez **Non**.
1. Un travail de migration démarre pour la machine virtuelle. Suivez le travail dans les notifications Azure.
1. Une fois le travail terminé, vous pouvez afficher et gérer la machine virtuelle à partir de la page **Machines virtuelles**.

## <a name="complete-the-migration"></a>Effectuer la migration

1. Une fois la migration terminée, cliquez avec le bouton droit sur la machine virtuelle > **Arrêter la réplication**. Cette opération effectue les actions suivantes :
    - Arrête la réplication pour l’ordinateur local.
    - Supprime l’ordinateur du nombre **Réplication de serveurs** dans Azure Migrate : Server Migration.
    - Nettoie les informations d’état de réplication pour la machine virtuelle.
1. Vérifiez et [dépannez tout problème d’activation de Windows sur la machine virtuelle Azure.](/troubleshoot/azure/virtual-machines/troubleshoot-activation-problems)
1. Effectuez les éventuels ajustements post-migration de l’application, comme la mise à jour des noms d’hôte, des chaînes de connexion de base de données et les configurations du serveur web.
1. Effectuez les tests finaux de réception de l’application et de la migration sur l’application migrée qui s’exécute maintenant dans Azure.
1. Réduisez le trafic vers l’instance de machine virtuelle Azure migrée.
1. Supprimez les machines virtuelles locales de votre inventaire des machines virtuelles locales.
1. Supprimez les machines virtuelles locales des sauvegardes locales.
1. Mettez à jour la documentation interne en y mentionnant le nouvel emplacement et la nouvelle adresse IP des machines virtuelles Azure.

## <a name="post-migration-best-practices"></a>Bonnes pratiques après la migration

- Pour une meilleure résilience :
    - Sécurisez les données en sauvegardant les machines virtuelles Azure avec le service Sauvegarde Azure. [Plus d’informations](../backup/quick-backup-vm-portal.md)
    - Conservez les charges de travail en cours d’exécution et disponibles en continu en répliquant des machines virtuelles Azure vers une région secondaire avec Site Recovery. [Plus d’informations](../site-recovery/azure-to-azure-tutorial-enable-replication.md)
- Pour renforcer la sécurité :
    - Verrouillez et limitez l’accès du trafic entrant grâce à l’[administration juste-à-temps de Microsoft Defender pour le cloud](../security-center/security-center-just-in-time.md).
    - Limitez le trafic réseau vers les points de terminaison de gestion avec des [groupes de sécurité réseau](../virtual-network/network-security-groups-overview.md).
    - Déployez [Azure Disk Encryption](../security/fundamentals/azure-disk-encryption-vms-vmss.md) pour sécuriser les disques, et protégez les données contre le vol et les accès non autorisés.
    - Pour en savoir plus sur la [sécurisation des ressources IaaS](https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/), consultez le site [Microsoft Defender pour le cloud](https://azure.microsoft.com/services/security-center/).
- Pour la surveillance et la gestion :
-  Envisagez de déployer [Azure Cost Management](../cost-management-billing/cost-management-billing-overview.md) pour surveiller l’utilisation et les coûts des ressources.

## <a name="next-steps"></a>Étapes suivantes

Examinez le [parcours de migration cloud](/azure/architecture/cloud-adoption/getting-started/migrate) dans le framework d’adoption du cloud Azure.
