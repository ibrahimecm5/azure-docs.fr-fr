---
title: Créer et télécharger un VHD Oracle Linux
description: Apprenez à créer et à télécharger un disque dur virtuel (VHD) Azure contenant un système d'exploitation Oracle Linux.
author: srijang
ms.service: virtual-machines
ms.collection: linux
ms.subservice: oracle
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 11/09/2021
ms.author: srijangupta
ms.openlocfilehash: 26eb625604d1947b5e246c27f766af63777b6239
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132309545"
---
# <a name="prepare-an-oracle-linux-virtual-machine-for-azure"></a>Préparation d’une machine virtuelle Linux Oracle pour Azure

**S’applique à :** :heavy_check_mark: Machines virtuelles Linux :heavy_check_mark: Groupes identiques flexibles 

Cet article suppose que vous avez déjà installé un système d'exploitation Oracle Linux dans un disque dur virtuel. Il existe de multiples outils dédiés à la création de fichiers .vhd, comme la solution de virtualisation Hyper-V. Pour obtenir des instructions, consultez la page [Installation du rôle Hyper-V et configuration d'une machine virtuelle](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh846766(v=ws.11)).

## <a name="oracle-linux-installation-notes"></a>Notes générales d’installation d’Oracle Linux
* Consultez également les [Notes générales d’installation sous Linux](create-upload-generic.md#general-linux-installation-notes) pour obtenir d’autres conseils sur la préparation de Linux pour Azure.
* Hyper-V et Azure prennent en charge Oracle Linux avec le noyau d’entreprise UEK ou le noyau compatible Red Hat.
* Le noyau UEK2 d'Oracle n'est pas pris en charge sur Hyper-V et Azure, car il ne comporte pas les pilotes nécessaires.
* Azure ne prend pas en charge le format VHDX, seulement le **VHD fixe**.  Vous pouvez convertir le disque au format VHD à l'aide de Hyper-V Manager ou de l’applet de commande convert-vhd.
* **La prise en charge du noyau pour le montage de systèmes de fichiers UDF est requise.** Au premier démarrage sur Azure, la configuration d’approvisionnement est transmise à la machine virtuelle Linux via des supports au format UDF reliés à l’invité. L’agent Linux Azure doit être en mesure de monter le système de fichiers UDF pour lire sa configuration et approvisionner la machine virtuelle.
* Lors de l’installation du système Linux, il est recommandé d’utiliser les partitions standard plutôt que LVM (qui est souvent le choix par défaut pour de nombreuses installations). Ceci permettra d'éviter les conflits de noms avec des machines virtuelles clonées, notamment si un disque de système d'exploitation doit être relié à une autre machine virtuelle pour la dépanner. La technique [LVM](/previous-versions/azure/virtual-machines/linux/configure-lvm) ou [RAID](/previous-versions/azure/virtual-machines/linux/configure-raid) peut être utilisée sur les disques de données, le cas échéant.
* Les versions du noyau Linux antérieures à la version 2.6.37 ne prennent pas en charge NUMA sur Hyper-V avec des machines virtuelles de taille supérieure. Ce problème affecte principalement les anciennes distributions à l’aide du noyau Red Hat 2.6.32 en amont et a été résolu dans Oracle Linux 6.6 et versions ultérieures.
* Ne configurez pas une partition d'échange sur le disque du système d'exploitation. Les étapes ci-dessous fournissent plus d’informations à ce sujet.
* Tous les VDH sur Azure doivent avoir une taille virtuelle alignée à 1 Mo. Lors de la conversion d’un disque brut vers VDH, vous devez vous assurer que la taille du disque brut est un multiple de 1 Mo avant la conversion. Pour plus d’informations, consultez [Notes d’installation sur Linux](create-upload-generic.md#general-linux-installation-notes).
* Assurez-vous que le référentiel `Addons` est activé. Modifiez le fichier `/etc/yum.repos.d/public-yum-ol6.repo`(Oracle Linux 6) ou `/etc/yum.repos.d/public-yum-ol7.repo`(Oracle Linux 7), remplacez la ligne `enabled=0` par `enabled=1` sous **[ol6_addons]** ou **[ol7_addons]** dans ce fichier.

## <a name="oracle-linux-64-and-later"></a>Oracle Linux 6.4 et versions ultérieures
Vous devez suivre des étapes de configuration spécifiques dans le système d'exploitation afin que la machine virtuelle fonctionne sur Azure.

1. Dans le panneau central de Hyper-V Manager, sélectionnez la machine virtuelle.
2. Cliquez sur **Connect** pour ouvrir la fenêtre de la machine virtuelle.
3. Exécutez la commande suivante pour désinstaller NetworkManager :

    ```console
    # sudo rpm -e --nodeps NetworkManager
    ```

    **Remarque :** si le package n'est pas déjà installé, la commande échoue et un message d'erreur s'affiche. Ceci est normal.
4. Créez un fichier nommé **network** in the `/etc/sysconfig/` et entrez-y le texte suivant :

    ```config   
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

5. Créez un fichier nommé **ifcfg-eth0** in the `/etc/sysconfig/network-scripts/` et entrez-y le texte suivant :

    ```config
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    ```

6. Modifiez les règles udev pour éviter la génération de règles statiques pour les interfaces Ethernet. Ces règles peuvent causer des problèmes lors du clonage d’une machine virtuelle dans Microsoft Azure ou Hyper-V :

    ```console
    # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
    # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules
    ```

7. Assurez-vous que le service réseau commencera aux heures de démarrage en exécutant la commande suivante :

    ```console
    # chkconfig network on
    ```

8. Saisissez la commande suivante pour installer python-pyasn1 :

    ```console
    # sudo yum install python-pyasn1
    ```

9. Modifiez la ligne de démarrage du noyau dans votre configuration grub pour y inclure les paramètres de noyau supplémentaires pour Azure. Pour cela, ouvrez le fichier « /boot/grub/menu.lst » dans un éditeur de texte et vérifiez que le noyau comprend les paramètres suivants :

    ```config-grub
    console=ttyS0 earlyprintk=ttyS0 rootdelay=300
    ```

   Ceci permet d'assurer que tous les messages de la console sont envoyés vers le premier port série, ce qui peut simplifier les problèmes de débogage pour l'assistance d'Azure.
   
   Outre les précautions ci-dessus, il est recommandé de *supprimer* les paramètres suivants :

    ```config-grub
    rhgb quiet crashkernel=auto
    ```

   Le démarrage graphique et transparent n’est pas utile dans un environnement cloud où nous voulons que tous les journaux d’activité soient envoyés au port série.
   
   L’option `crashkernel` peut éventuellement être conservée. Notez cependant que ce paramètre réduit la quantité de mémoire disponible dans la machine virtuelle de 128 Mo ou plus, ce qui peut être problématique sur les machines virtuelles de petite taille.
10. Vérifiez que le serveur SSH est installé et configuré pour démarrer au moment prévu.  C'est généralement le cas par défaut.
11. Installez l’agent Linux Azure en exécutant la commande suivante : La version la plus récente est la 2.0.15.

    ```console
    # sudo yum install WALinuxAgent
    ```

    L'installation du package WALinuxAgent entraîne la suppression des packages NetworkManager et NetworkManager-gnome, s'ils n'avaient pas déjà été supprimés comme indiqué à l'étape 2.
12. Ne créez pas d'espace swap sur le disque du système d'exploitation.
    
    L'agent Linux Azure peut configurer automatiquement un espace swap à l'aide du disque local de ressources connecté à la machine virtuelle après déploiement sur Azure. Notez que le disque de ressources local est un disque *temporaire* et qu'il peut être vidé lors de l'annulation de l'approvisionnement de la machine virtuelle. Après avoir installé l'agent Linux Azure (voir l'étape précédente), modifiez les paramètres suivants dans le fichier /etc/waagent.conf :

    ```config-conf
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.EnableSwap=y
    ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.
    ```

13. Exécutez les commandes suivantes pour annuler le déploiement de la machine virtuelle et préparer son déploiement sur Azure :

    ```console
    # sudo waagent -force -deprovision
    # export HISTSIZE=0
    # logout
    ```

14. Cliquez sur **Action -> Arrêter** dans le Gestionnaire Hyper-V. Votre disque dur virtuel Linux est alors prêt pour le téléchargement dans Azure.

---
## <a name="oracle-linux-70-and-later"></a>Oracle Linux 7.0 et versions ultérieures
**Modifications dans Oracle Linux 7**

La préparation d'une machine virtuelle Oracle Linux 7 pour Azure est très similaire à Oracle Linux 6 ; cependant, certaines différences importantes méritent d'être notées :

* Azure prend en charge Oracle Linux avec le noyau de l’entreprise (UEK) ou le noyau compatible Red Hat. Oracle Linux avec UEK est recommandé.
* Le package NetworkManager n'est plus en conflit avec l'agent Azure Linux. Ce package est installé par défaut ; nous recommandons de ne pas le supprimer.
* GRUB2 est maintenant utilisé comme chargeur de démarrage (bootloader) par défaut ; la modification des paramètres du noyau a donc changé (voir ci-dessous).
* XFS est maintenant le système de fichiers par défaut. Le système de fichiers ext4 est toujours utilisable si vous le souhaitez.

**Configuration**

1. Dans le Gestionnaire Hyper-V, sélectionnez la machine virtuelle.
2. Cliquez sur **Connecter** pour ouvrir une fenêtre de console de la machine virtuelle.
3. Créez un fichier nommé **network** in the `/etc/sysconfig/` et entrez-y le texte suivant :

    ```config
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

4. Créez un fichier nommé **ifcfg-eth0** in the `/etc/sysconfig/network-scripts/` et entrez-y le texte suivant :

    ```config
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    ```

5. Modifiez les règles udev pour éviter la génération de règles statiques pour les interfaces Ethernet. Ces règles peuvent causer des problèmes lors du clonage d’une machine virtuelle dans Microsoft Azure ou Hyper-V :

    ```console
    # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
    ```

6. Assurez-vous que le service réseau commencera aux heures de démarrage en exécutant la commande suivante :

    ```console
    # sudo chkconfig network on
    ```

7. Exécutez la commande suivante pour installer le package python-pyasn1 :

    ```console
    # sudo yum install python-pyasn1
    ```

8. Exécutez la commande suivante pour effacer les métadonnées yum actuelles et installer les mises à jour le cas échéant :

    ```console 
    # sudo yum clean all
    # sudo yum -y update
    ```

9. Modifiez la ligne de démarrage du noyau dans votre configuration grub pour y inclure les paramètres de noyau supplémentaires pour Azure. Pour cela, ouvrez le fichier « /etc/default/grub » dans un éditeur de texte et modifiez le paramètre `GRUB_CMDLINE_LINUX` , par exemple :

    ```config-grub
    GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
    ```

   Ce permet également d'assurer que tous les messages de la console sont envoyés vers le premier port série, ce qui peut simplifier les problèmes de débogage pour la prise en charge d'Azure. Il désactive également les conventions d’affectation de noms pour les cartes réseau dans Oracle Linux 7 avec le noyau de l’entreprise. Outre les précautions ci-dessus, il est recommandé de *supprimer* les paramètres suivants :

    ```config-grub
       rhgb quiet crashkernel=auto
    ```
 
   Le démarrage graphique et transparent n’est pas utile dans un environnement cloud où nous voulons que tous les journaux d’activité soient envoyés au port série.
   
   L’option `crashkernel` peut éventuellement être conservée. Notez cependant que ce paramètre réduit la quantité de mémoire disponible dans la machine virtuelle de 128 Mo ou plus, ce qui peut être problématique sur les machines virtuelles de petite taille.
10. Lorsque vous avez fini de modifier le fichier « /etc/default/grub », exécutez la commande suivante pour régénérer la configuration grub :

    ```console
    # sudo grub2-mkconfig -o /boot/grub2/grub.cfg
    ```

11. Vérifiez que le serveur SSH est installé et configuré pour démarrer au moment prévu.  C'est généralement le cas par défaut.
12. Installez l'agent Linux Azure et les dépendances :

    ```bash
    sudo yum install WALinuxAgent
    sudo systemctl enable waagent
    ```

13. Installez cloud-init pour gérer l’approvisionnement :

    ```console
    yum install -y cloud-init cloud-utils-growpart gdisk hyperv-daemons

    # Configure waagent for cloud-init
    sed -i 's/Provisioning.UseCloudInit=n/Provisioning.UseCloudInit=y/g' /etc/waagent.conf
    sed -i 's/Provisioning.Enabled=y/Provisioning.Enabled=n/g' /etc/waagent.conf


    echo "Adding mounts and disk_setup to init stage"
    sed -i '/ - mounts/d' /etc/cloud/cloud.cfg
    sed -i '/ - disk_setup/d' /etc/cloud/cloud.cfg
    sed -i '/cloud_init_modules/a\\ - mounts' /etc/cloud/cloud.cfg
    sed -i '/cloud_init_modules/a\\ - disk_setup' /etc/cloud/cloud.cfg

    echo "Allow only Azure datasource, disable fetching network setting via IMDS"
    cat > /etc/cloud/cloud.cfg.d/91-azure_datasource.cfg <<EOF
    datasource_list: [ Azure ]
    datasource:
        Azure:
            apply_network_config: False
    EOF

    if [[ -f /mnt/resource/swapfile ]]; then
    echo Removing swapfile - RHEL uses a swapfile by default
    swapoff /mnt/resource/swapfile
    rm /mnt/resource/swapfile -f
    fi

    echo "Add console log file"
    cat >> /etc/cloud/cloud.cfg.d/05_logging.cfg <<EOF

    # This tells cloud-init to redirect its stdout and stderr to
    # 'tee -a /var/log/cloud-init-output.log' so the user can see output
    # there without needing to look on the console.
    output: {all: '| tee -a /var/log/cloud-init-output.log'}
    EOF

    ```

14. La configuration d’échange ne crée pas d’espace d’échange sur le disque du système d’exploitation.

    Auparavant, l’agent Linux Azure était utilisé pour configurer automatiquement un espace d’échange à l’aide du disque de ressources local attaché à la machine virtuelle après l’approvisionnement de cette dernière sur Azure. Toutefois, cela est désormais géré par cloud-init, et vous **ne devez pas** utiliser l’agent Linux pour formater le disque de ressources. Créez le fichier d’échange et modifiez les paramètres suivants dans `/etc/waagent.conf` de manière appropriée :

    ```console
    sed -i 's/ResourceDisk.Format=y/ResourceDisk.Format=n/g' /etc/waagent.conf
    sed -i 's/ResourceDisk.EnableSwap=y/ResourceDisk.EnableSwap=n/g' /etc/waagent.conf
    ```

    Si vous souhaitez monter, formater et créer un échange, vous pouvez :
    * Transmettre ceci en tant que configuration cloud-init chaque fois que vous créez une machine virtuelle
    * Utiliser une directive cloud-init intégrée à l’image qui effectuera cette opération chaque fois que la machine virtuelle est créée :

        ```console
        echo 'DefaultEnvironment="CLOUD_CFG=/etc/cloud/cloud.cfg.d/00-azure-swap.cfg"' >> /etc/systemd/system.conf
        cat > /etc/cloud/cloud.cfg.d/00-azure-swap.cfg << EOF
        #cloud-config
        # Generated by Azure cloud image build
        disk_setup:
          ephemeral0:
            table_type: mbr
            layout: [66, [33, 82]]
            overwrite: True
        fs_setup:
          - device: ephemeral0.1
            filesystem: ext4
          - device: ephemeral0.2
            filesystem: swap
        mounts:
          - ["ephemeral0.1", "/mnt"]
          - ["ephemeral0.2", "none", "swap", "sw", "0", "0"]
        EOF
        ```


15. Exécutez les commandes suivantes pour annuler le déploiement de la machine virtuelle et préparer son déploiement sur Azure :

    **Remarque :** Si vous migrez une machine virtuelle spécifique et que vous ne souhaitez pas créer d’image généralisée, ignorez l’étape de déprovisionnement.
    
    ```console
    # sudo cloud-init clean
    # sudo rm -f /var/log/waagent.log

    # waagent -force -deprovision+user
    # rm -f ~/.bash_history
    

    # export HISTSIZE=0

    # logout
    ```

16. Cliquez sur **Action -> Arrêter** dans le Gestionnaire Hyper-V. Votre disque dur virtuel Linux est alors prêt pour le téléchargement dans Azure.

## <a name="next-steps"></a>Étapes suivantes
Vous êtes maintenant prêt à utiliser votre disque dur virtuel Oracle Linux .vhd pour créer des machines virtuelles dans Azure. S’il s’agit de votre premier chargement du fichier .vhd sur Azure, consultez [Créer une machine virtuelle Linux à partir d’un disque personnalisé](upload-vhd.md#option-1-upload-a-vhd).
