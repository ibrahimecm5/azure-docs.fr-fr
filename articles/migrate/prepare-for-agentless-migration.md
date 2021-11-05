---
title: Préparer des machines pour la migration sans agent avec Azure Migrate
description: Découvrez comment préparer des machines locales pour la migration sans agent avec Azure Migrate.
author: deseelam
ms.author: deseelam
ms.manager: bsiva
ms.topic: conceptual
ms.date: 07/06/2021
ms.openlocfilehash: 1765b671f09dc90506636cc643337e55957c95cd
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131084631"
---
# <a name="prepare-for-vmware-agentless-migration"></a>Préparer la migration sans agent VMware

Cet article fournit une vue d’ensemble des modifications effectuées quand vous [migrez des machines virtuelles VMware vers Azure via la méthode de migration sans agent](./tutorial-migrate-vmware.md) en utilisant l’outil Azure Migrate : Migration de serveur.

Avant de migrer votre machine virtuelle locale vers Azure, vous pouvez être amené à effectuer quelques modifications afin de préparer la machine virtuelle pour Azure. Ces modifications sont importantes pour garantir que la machine virtuelle migrée peut démarrer correctement dans Azure et que la connectivité à la machine virtuelle Azure peut être établie.
Azure Migrate gère automatiquement ces modifications de configuration pour les versions de système d’exploitation mentionnées ci-dessous pour Linux et pour Windows. Ce processus est appelé *hydratation*.

**Versions de système d’exploitation prises en charge pour l’hydratation**

- Windows Server 2008 ou version ultérieure
- Red Hat Enterprise Linux 8.x, 7.9, 7.8, 7.7, 7.6, 7.5, 7.4, 7.0, 6.x
- CentOS 8.x, 7.7, 7.6, 7.5, 7.4, 6.x
- SUSE Linux Enterprise Server 15 SP0, 15 SP1, 12
- Ubuntu 20.04, 19.04, 19.10, 18.04LTS, 16.04LTS, 14.04LTS
- Ubuntu 18.04 LTS, 16.04 LTS
- Debian 9, 8, 7
- Oracle Linux 6, 7.7, 7.7-CI

Vous pouvez également utiliser cet article afin de préparer manuellement les machines virtuelles pour la migration vers Azure pour les versions de systèmes d’exploitation non listées ci-dessus. Dans les grandes lignes, ces modifications sont les suivantes :

- Vérifier la présence des pilotes nécessaires
- Activer la console série
- Configurer les paramètres réseau
- Installer l’agent invité de machine virtuelle

## <a name="hydration-process"></a>Processus d’hydratation

Vous devez apporter quelques modifications à la configuration des machines virtuelles avant la migration pour garantir que les machines virtuelles migrées fonctionnent correctement sur Azure. Azure Migrate gère ces modifications de configuration via le processus d’*hydratation*. Le processus d’hydratation est effectué seulement pour les versions de systèmes d’exploitation prises en charge par Azure qui sont listées ci-dessus. Avant de migrer, il peut être nécessaire d’effectuer manuellement les modifications requises pour d’autres versions de système d’exploitation qui ne sont pas listées ci-dessus. Si la machine virtuelle est migrée sans les modifications nécessaires, il est possible que la machine virtuelle ne démarre pas ou que vous n’ayez pas de connectivité à la machine virtuelle migrée. Le diagramme suivant vous montre qu’Azure Migrate effectue le processus d’hydratation.

 [![Étapes de l’hydratation](./media/concepts-prepare-vmware-agentless-migration/hydration-process-inline.png)](./media/concepts-prepare-vmware-agentless-migration/hydration-process-expanded.png#lightbox)

Quand un utilisateur déclenche *Tester la migration* ou *Migrer*, Azure Migrate effectue le processus d’hydratation pour préparer la machine virtuelle locale pour la migration vers Azure.
Pour configurer le processus d’hydratation, Azure Migrate crée une machine virtuelle Azure temporaire et associe les disques de la machine virtuelle source pour effectuer des modifications afin de préparer la machine virtuelle source pour Azure. La machine virtuelle Azure temporaire est une machine virtuelle intermédiaire créée lors du processus de migration avant la création de la machine virtuelle finale migrée. La machine virtuelle temporaire est créée avec un type de système d’exploitation similaire (Windows/Linux) en utilisant une des images de système d’exploitation de la Place de marché. Si la machine virtuelle locale exécute Windows, le disque du système d’exploitation de la machine virtuelle locale est attaché en tant que disque de données à la machine virtuelle temporaire pour effectuer les modifications. S’il s’agit d’un serveur Linux, tous les disques attachés à la machine virtuelle locale sont attachés en tant que disques de données à la machine virtuelle Azure temporaire.

Azure Migrate va créer l’interface réseau, un nouveau réseau virtuel, un sous-réseau et un groupe de sécurité réseau pour héberger la machine virtuelle temporaire. Ces ressources sont créées dans l’abonnement du client. S’il y a des stratégies en conflit qui empêchent la création des artefacts réseau, Azure Migrate va tenter de créer la machine virtuelle Azure temporaire dans le réseau virtuel et le sous-réseau fournis dans le cadre des options des paramètres de la cible de réplication.

Une fois la machine virtuelle créée, Azure Migrate va appeler l’[extension de script personnalisé](../virtual-machines/extensions/custom-script-windows.md) sur la machine virtuelle temporaire en utilisant l’API REST de Machine virtuelle Azure. L’utilitaire Extension de script personnalisé exécute un script de préparation contenant la configuration requise pour la préparation à Azure sur les disques de la machine virtuelle locale attachés à la machine virtuelle Azure temporaire. Le script de préparation est téléchargé depuis un compte de stockage détenu par Azure Migrate. Les règles du groupe de sécurité réseau du réseau virtuel seront configurées pour permettre à la machine virtuelle Azure temporaire d’accéder au compte de stockage Azure Migrate pour appeler le script.

 ![Étapes de la migration](./media/concepts-vmware-agentless-migration/migration-steps.png)

## <a name="changes-performed-during-the-hydration-process"></a>Modifications effectuées lors du processus d’hydratation

Le script de préparation effectue les modifications suivantes en fonction du type de système d’exploitation de la machine virtuelle source à migrer. Vous pouvez également utiliser cette section comme guide afin de préparer manuellement les machines virtuelles pour la migration, pour des versions de systèmes d’exploitation non prises en charge par l’hydratation.

### <a name="changes-performed-on-windows-servers"></a>Modifications effectuées sur les serveurs Windows

1. **Découvrir et préparer le volume du système d’exploitation Windows**

   Avant d’effectuer les modifications de configuration appropriées, le script de préparation vérifie si le disque de système d’exploitation correct a été sélectionné pour la migration. Le script de préparation examine tous les volumes attachés visibles pour le système et recherche le chemin du fichier de la ruche du Registre SYSTÈME pour trouver le volume du système d’exploitation source.

   Les actions suivantes sont effectuées dans cette étape :
   - Montage de chaque partition sur le disque du système d’exploitation attaché à la machine virtuelle temporaire.

   - Recherche les fichiers du Registre \Windows\System32\Config\System après le montage de la partition.
   - Si les fichiers sont introuvables, la partition est démontée et la recherche de la partition correcte continue.
   - Si les fichiers ne sont présents sur aucune des partitions, cela peut signifier qu’un disque de système d’exploitation incorrect a été sélectionné ou que le disque du système d’exploitation est endommagé. Azure Migrate fait alors échouer le processus de migration avec une erreur appropriée.

   >[!NOTE]
   >Cette étape n’est pas pertinente si vous préparez manuellement les serveurs pour la migration.

1. **Apporter des modifications liées au démarrage et à la connectivité**

   Une fois les fichiers du volume du système d’exploitation source détectés, le script de préparation charge la ruche du Registre SYSTÈME dans l’éditeur du Registre de la machine virtuelle Azure temporaire et effectue les modifications suivantes pour garantir le démarrage et la connectivité de la machine virtuelle. Vous devez configurer ces paramètres manuellement si la version du système d’exploitation n’est pas prise en charge pour l’hydratation.

   1. **Vérifier la présence des pilotes nécessaires**
      
      Vérifiez si les pilotes nécessaires sont installés et configurés pour être chargés au **démarrage**. Ces pilotes de Windows permettent au serveur de communiquer avec le matériel et d’autres périphériques connectés.

      - IntelIde.sys
      - Atapi
      - Storfit
      - Storvsc
      - VMbus

   1. **Définir la stratégie de réseau de zone de stockage (SAN) sur Online All (Entièrement en ligne)**

      Ceci garantit que les volumes Windows sur la machine virtuelle Azure utilisent les mêmes affectations de lettre de lecteur que la machine virtuelle locale. Par défaut, le lecteur D: est affecté aux machines virtuelles Azure pour une utilisation comme stockage temporaire. À la suite de cette affectation de lecteur, tous les autres lecteurs de stockage attachés sont incrémentés d’une lettre. Pour empêcher cette affectation automatique et faire en sorte qu’Azure affecte la lettre de lecteur disponible suivante à son volume temporaire, définissez la stratégie de réseau de zone de stockage (SAN) sur Online All (Entièrement en ligne).

      Pour configurer ce paramètre manuellement :

      - Sur le serveur local, ouvrez l’invite de commandes avec des privilèges élevés et entrez **diskpart**.

        ![Configuration manuelle](./media/concepts-prepare-vmware-agentless-migration/command-prompt-diskpart.png)

      - Entrez SAN. Si la lettre de lecteur du système d’exploitation invité n’est pas conservée, Mettre hors connexion tous les disques ou Mettre hors connexion les disques partagés est retourné.

      - À l’invite DISKPART, entrez SAN Policy=OnlineAll. Cette valeur garantit les disques sont mis en ligne, et que vous pouvez lire et écrire sur les deux disques.

        ![Stratégie en ligne de diskpart sur l’invite de commandes administrateur](./media/concepts-prepare-vmware-agentless-migration/diskpart-online-policy.png)

1. **Définir le type de démarrage de DHCP**

   Le script de préparation définit également le type de démarrage du service DHCP sur Automatique. Ceci permettra à la machine virtuelle migrée d’obtenir une adresse IP et d’établir la connectivité après la migration. Vérifiez que le service DHCP est configuré et que l’état est En cours d’exécution.

    ![Définir le type de démarrage de DHCP](./media/concepts-prepare-vmware-agentless-migration/get-service-dhcp.png)

   Pour modifier manuellement les paramètres de démarrage de DHCP, exécutez l’exemple suivant dans Windows PowerShell :

   ```
   Get-Service -Name Dhcp
   Where-Object StartType -ne Automatic
   Set-Service -StartupType Automatic
   ```

1. **Désactiver VMware Tools**

   Définissez le type de démarrage du service « VMware Tools » sur désactivé s’il existe, car il n’est pas nécessaire pour la machine virtuelle dans Azure.

   >[!NOTE]                        
   >Pour vous connecter à des machines virtuelles Windows Server 2003, Hyper-V Integration Services doit être installé sur la machine virtuelle Azure. Cette option n’est pas installée par défaut sur les ordinateurs Windows Server 2003. Consultez cet [article](./prepare-windows-server-2003-migration.md) pour installer et préparer votre machine Windows Server pour la migration.

1. **Installer l’agent invité Azure Windows**

    Azure Migrate va tenter d’installer l’agent de machine virtuelle Microsoft Azure, un processus léger et sécurisé qui gère l’interaction des machines virtuelles avec le contrôleur de structure Azure. L’agent de machine virtuelle a un rôle important dans l’activation et l’exécution des extensions de machine virtuelle Azure qui permettent de configurer des machines virtuelles après le déploiement, comme installer et configurer des logiciels. Azure Migrate installe automatiquement l’agent de machine virtuelle Windows sur Windows Server 2008 R2 et ses versions ultérieures.

    L’agent de machine virtuelle Windows peut être installé manuellement avec un package Windows Installer. Pour installer manuellement l’agent de machine virtuelle Windows, [téléchargez le programme d’installation de l’agent de machine virtuelle](https://go.microsoft.com/fwlink/?LinkID=394789). Vous pouvez aussi rechercher une version spécifique parmi les [versions de l’agent de machine virtuelle IaaS Windows sur GitHub](https://github.com/Azure/WindowsVMAgent/releases). L’agent de machine virtuelle est pris en charge sur Windows Server 2008 (64 bits) et ultérieur.

    Pour vérifier si l’agent de machine virtuelle Azure a été installé correctement, ouvrez le Gestionnaire des tâches, sélectionnez l’onglet **Détails** et recherchez le nom de processus *WindowsAzureGuestAgent.exe*. La présence de ce processus indique que l’agent de machine virtuelle est installé. Vous pouvez également utiliser [PowerShell pour détecter l’agent de machine virtuelle.](../virtual-machines/extensions/agent-windows.md#powershell)

    ![Installation réussie de l’agent de machine virtuelle Azure](./media/concepts-prepare-vmware-agentless-migration/installation-azure-vm-agent.png)

    Une fois les modifications mentionnées ci-dessus effectuées, la partition système est déchargée. La machine virtuelle est maintenant prête pour la migration.
    [En savoir plus sur les modifications pour les serveurs Windows.](../virtual-machines/windows/prepare-for-upload-vhd-image.md)

### <a name="changes-performed-on-linux-servers"></a>Modifications effectuées sur les serveurs Linux

1. **Découvrir et monter les partitions du système d’exploitation Linux**  

   Avant d’effectuer les modifications de configuration appropriées, le script de préparation vérifie si le disque de système d’exploitation correct a été sélectionné pour la migration. Le script va collecter des informations sur toutes les partitions, leurs UUID et les points de montage. Le script va examiner toutes ces partitions visibles pour localiser les partitions /boot et /root.

   Les actions suivantes sont effectuées dans cette étape :

   - Découvrir la partition /root :
     - Montez chaque partition visible et rechercher etc/fstab.
      - Si les fichiers fstab sont introuvables, la partition est démontée et la recherche de la partition correcte continue.
      - Si les fichiers fstab sont trouvés, lisez le contenu de fstab pour identifier le périphérique racine et montez-le en tant que point de montage de base.
   - Découvrir /boot et les autres partitions système :
     - Utilisez le contenu de fstab pour déterminer si /boot est une partition distincte. S’il s’agit d’une partition distincte, obtenez le nom du périphérique de la partition de démarrage auprès du contenu de fstab ou recherchez la partition qui a l’indicateur de démarrage.
     - Le script va procéder à la découverte et au montage de /boot et d’autres partitions nécessaires sur « /mnt/azure_sms_root » pour créer l’arborescence du système de fichiers racine nécessaire pour le chroot jail. Les autres partitions nécessaires sont notamment : boot/grub/menu.lst, /boot/grub/grub.conf, /boot/grub2/grub.cfg, /boot/grub/grub.cfg, /boot/efi (pour le démarrage UEFI), /var, /lib, /etc, /usr et d’autres.

1. **Découvrir la version du système d’exploitation**

   Une fois la partition root découverte, le script utilise les fichiers ci-dessous pour déterminer la distribution et la version du système d’exploitation Linux.

   - RHEL/CentOS : etc/redhat-release
   - OL : etc/oracle-release
   - SLES : etc/SuSE-release
   - Ubuntu : etc/lsb-release
   - Debian : etc/debian_version

1. **Installer Hyper-V Linux Integration Services et regénérer l’image du noyau**  

   L’étape suivante consiste à inspecter l’image du noyau et à regénérer l’image initiale de Linux de sorte qu’elle contienne les pilotes Hyper-V nécessaires (**hv_vmbus, hv_storvsc, hv_netvsc**) sur le ramdisk initial. La regénération de l’image init est l’assurance que la machine virtuelle démarrera dans Azure.

   Azure s’exécute sur l’hyperviseur Hyper-V. Linux nécessite donc l’exécution de certains modules du noyau dans Azure. Pour préparer votre image Linux, vous devez regénérer l’initrd afin que les modules du noyau hv_vmbus et hv_storvsc soient disponibles sur le ramdisk initial. Le mécanisme de reconstruction d'image initrd ou initramfs varie en fonction de la distribution. Consultez la documentation ou le support de votre distribution pour trouver la procédure appropriée. Voici un exemple de regénération d’initrd avec l’utilitaire mkinitrd :

   1. Rechercher la liste des noyaux installés sur le système (/lib/modules)
   1. Pour chaque module, vérifiez si les pilotes Hyper-V sont déjà inclus.
   1. Si un ou plusieurs ces pilotes sont manquant, ajoutez les pilotes nécessaires et regénérez l’image pour la version correspondante du noyau.

      >[!NOTE]
      >Cette étape peut ne pas s’appliquer aux machines virtuelles Ubuntu et Debian, car les pilotes Hyper-V sont intégrés par défaut. [En savoir plus sur les changements.](../virtual-machines/linux/create-upload-generic.md#installing-kernel-modules-without-hyper-v)

      Exemple illustrant la regénération d’initrd

      - Sauvegarder l’image initrd existante

        ```
        cd /boot
        sudo cp initrd-`uname -r`.img  initrd-`uname -r`.img.bak
        ```

      - Régénérez le ramdisk initrd avec les modules noyau hv_vmbus et hv_storvsc :

        ```
          sudo mkinitrd --preload=hv_storvsc --preload=hv_vmbus -v -f initrd-`uname -r`.img `uname -r`
        ```
   Ils sont inclus par défaut dans la plupart des nouvelles versions des distributions Linux. Si ce n’est pas le cas, installez-les manuellement pour toutes les versions autres que celles indiquées ci-dessus, en utilisant les étapes mentionnées plus haut.

1. **Activer la journalisation de la console série Azure**

   Le script va alors effectuer les modifications pour activer la journalisation de la console série Azure. L’activation de la journalisation de la console aide à résoudre les problèmes sur la machine virtuelle Azure. Pour plus d’informations sur la console série Azure pour Linux, consultez [Console série Azure pour Linux - Machines virtuelles | Microsoft Docs](/troubleshoot/azure/virtual-machines/serial-console-linux).

   Modifiez la ligne de démarrage du noyau dans GRUB ou GRUB2 afin d’inclure les paramètres suivants pour que tous les messages de la console soient envoyés au premier port série. Ces messages peuvent aider l’équipe du support Azure à procéder au débogage.

   ```
    console=ttyS0,115200n8 earlyprintk=ttyS0,115200 rootdelay=300
   ```

   Nous vous recommandons également de supprimer les paramètres suivants le cas échéant.

   ```
   rhgb quiet crashkernel=auto
   ```
    Pour les modifications spécifiques, [consultez cet this article](../virtual-machines/linux/create-upload-generic.md#general-linux-system-requirements).

1. **Modifications réseau pour la connectivité**

   En fonction de la version du système d’exploitation, le script va effectuer les modifications réseau nécessaires pour la connectivité à la machine virtuelle migrée. Les modifications sont notamment les suivantes :

   1. Déplacez (ou supprimez) les règles udev afin d’éviter la génération de règles statiques pour l’interface Ethernet. Ces règles provoquent des problèmes quand vous clonez une machine virtuelle dans Azure.

      Exemple pour des serveurs RedHat

      ```console
        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules
      ```

   1. Supprimez le gestionnaire de réseau si nécessaire. Le gestionnaire de réseau peut interférer avec l’agent Linux Azure pour quelques versions du système d’exploitation. Il est recommandé d’effectuer ces modifications pour les serveurs qui exécutent des distributions RedHat et Ubuntu.

   1. Exécutez la commande suivante pour désinstaller le package :
    
      Exemple pour des serveurs RedHat

      ```console
         # sudo rpm -e --nodeps NetworkManager
      ```

   1. Sauvegardez les paramètres de carte réseau existants, et créez un fichier de configuration de carte réseau eth0 avec les paramètres DHCP. Pour ce faire, le script va crée ou modifier le fichier /etc/sysconfig/network-scripts/ifcfg-eth0, puis ajouter le texte suivant :

      Exemple pour des serveurs RedHat

      ```config
         DEVICE=eth0
         ONBOOT=yes
         BOOTPROTO=dhcp
         TYPE=Ethernet
         USERCTL=no
         PEERDNS=yes
         IPV6INIT=no
         PERSISTENT_DHCLIENT=yes
         NM_CONTROLLED=yes
      ```

   1. Réinitialisez le fichier etc/sysconfig/network comme suit :

      Exemple pour des serveurs RedHat

      ```config
         NETWORKING=yes
         HOSTNAME=localhost.localdomain
      ```

1. **Vérification de fstab**

    Azure Migrate va vérifier les entrées du fichier fstab et remplacera les entrées fstab par des identificateurs de volumes persistants, des UUID, là où c’est nécessaire. Ceci garantit que le nom du lecteur/de la partition reste constant quel que soit le système auquel il est attaché.

   - Si le nom du périphérique est un nom de périphérique standard (par exemple /dev/sdb1) :
     - S’il s’agit d’une partition racine ou de démarrage, elle est remplacée par l’UUID.
     - Si la partition coexiste avec la partition racine ou de démarrage en tant que partition standard sur le même disque, elle est remplacée par l’UUID.
   - Si le nom du périphérique est UUID/LABEL/LV, aucune modification n’est effectuée.
   - S’il s’agit d’un périphérique réseau (nfs, cifs, smbfs, etc.), le script place l’entrée en commentaire. Pour y accéder, vous pouvez décommenter cette entrée et redémarrer votre machine virtuelle Azure.

1. **Installer l’agent invité Linux Azure**

    Azure Migrate va tenter d’installer l’agent Linux Microsoft Azure (waagent), un processus léger et sécurisé qui gère le provisionnement de Linux et de FreeBSD, et l’interaction des machines virtuelles avec le contrôleur de structure Azure.  [En savoir plus](../virtual-machines/extensions/agent-linux.md) sur les fonctionnalités activées pour les déploiements IaaS Linux et FreeBSD via l’agent Linux.

    Passez en revue la liste des [packages requis](../virtual-machines/extensions/agent-linux.md#requirements) pour installer l’agent VM Linux. Azure Migrate installe automatiquement l’agent de machine virtuelle Linux pour RRHEL 8.x/7.x/6.x, CentOS 8.x/7.x/6.x, Ubuntu 14.04/16.04/18.04/19.04/19.10/20.04, SUSE 15 SP0/15 SP1/12, Debian 9/8/7 et Oracle 7 lors de l’utilisation de la méthode de migration VMware sans agent. Suivez ces instructions pour [installer l’agent Linux manuellement](../virtual-machines/extensions/agent-linux.md#installation) pour d’autres versions de système d’exploitation.

    Vous pouvez utiliser la commande pour vérifier l’état du service de l’agent Linux Azure et être sûr qu’il est en cours d’exécution. Le nom du service peut être **walinuxagent** ou **waagent**.
    Une fois les modifications d’hydratation effectuées, le script va démonter toutes les partitions montées, désactiver les groupes de volumes, puis vider les périphériques.

   ```
    $ vgchange -an <vg-name>
    $ blockdev –flushbufs <disk-device-name>
   ```

   [En savoir plus sur les modifications pour les serveurs Linux.](../virtual-machines/linux/create-upload-generic.md)

### <a name="clean-up-the-temporary-vm"></a>Nettoyer la machine virtuelle temporaire

Après avoir effectué les modifications nécessaires, Azure Migrate va supprimer la machine virtuelle temporaire et libérer les disques de système d’exploitation (et les disques de données) attachés. Ceci marque la fin du *processus d’hydratation*.     

Après cela, le disque du système d’exploitation et les disques de données modifiés contenant les données répliquées sont clonés. Une nouvelle machine virtuelle est créé dans la région, le réseau virtuel et le sous-réseau cibles, et les disques clonés sont attachés à la machine virtuelle. Ceci marque la fin du processus de migration.

## <a name="learn-more"></a>En savoir plus

- [Préparer des machines locales pour la migration vers Azure.](./prepare-for-migration.md)
