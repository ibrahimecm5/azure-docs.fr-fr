---
title: Sauvegarder une base de données Oracle Database 19c sur une machine virtuelle Linux Azure à l’aide de l’outil RMAN et d’Azure Files
description: Apprenez à sauvegarder une base de données Oracle Database 19c dans Azure Files.
author: cro27
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: article
ms.date: 01/28/2021
ms.author: cholse
ms.reviewer: dbakevlar
ms.openlocfilehash: 2c9128f6e03f039ddc6e56ec9672a7a0a18f9889
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132520405"
---
# <a name="back-up-and-recover-an-oracle-database-19c-database-on-an-azure-linux-vm-using-azure-files"></a>Sauvegarder et récupérer une base de données Oracle Database 19c sur une machine virtuelle Linux Azure à l’aide d’Azure Files

**S’applique à :** :heavy_check_mark: Machines virtuelles Linux 

Cet article explique comment utiliser Azure Files comme support pour sauvegarder et restaurer une base de données Oracle exécutée sur une machine virtuelle Azure. Vous utiliserez Oracle RMAN pour sauvegarder la base de données sur un partage de fichiers Azure monté sur la machine virtuelle à l’aide du protocole SMB. L’utilisation d’Azure Files comme support de sauvegarde est une solution extrêmement économique et performante. Cela dit, pour les bases de données très volumineuses, le service Sauvegarde Azure constitue une meilleure solution.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../includes/azure-cli-prepare-your-environment.md)]

- Pour bénéficier du processus de sauvegarde et de récupération, vous devez commencer par créer une machine virtuelle Linux sur laquelle une instance d'Oracle Database 19c est installée. L'image de la Place de marché actuellement utilisée pour créer la machine virtuelle est **Oracle:oracle-database-19-3:oracle-database-19-0904:latest**. Suivez les étapes du [Guide de démarrage rapide Créer une base de données Oracle](./oracle-database-quick-create.md) afin de créer une base de données Oracle qui vous permettra d'utiliser ce tutoriel.

## <a name="prepare-the-database-environment"></a>Préparation de l'environnement de base de données

1. Pour créer une session Secure Shell (SSH) avec la machine virtuelle, utilisez la commande suivante. Remplacez la combinaison d’adresse IP et de nom d’hôte par la valeur `publicIpAddress` pour votre machine virtuelle.
    
   ```bash
   ssh azureuser@<publicIpAddress>
   ```
   
2. Basculez vers l'utilisateur ***racine*** :
 
   ```bash
   sudo su -
   ```
    
3. Ajoutez l'utilisateur oracle au fichier ***/etc/sudoers*** :

   ```bash
   echo "oracle   ALL=(ALL)      NOPASSWD: ALL" >> /etc/sudoers
   ```

4. Cette étape suppose que vous disposez d’une instance d’Oracle (test) exécutée sur une machine virtuelle nommée *vmoracle19c*.

   Basculez vers l'utilisateur *oracle* :

   ```bash
   sudo su - oracle
   ```
    
5. Avant de vous connecter, vous devez définir la variable d'environnement ORACLE_SID :
    
    ```bash
    export ORACLE_SID=test;
    ```
   
   Vous devez également ajouter la variable ORACLE_SID au fichier `.bashrc` des utilisateurs `oracle` pour les prochaines connexions à l'aide de la commande suivante :

    ```bash
    echo "export ORACLE_SID=test" >> ~oracle/.bashrc
    ```
    
6. Si ce n'est déjà fait, démarrez l'écouteur Oracle :
    
   ```bash
   $ lsnrctl start
   ```

    La sortie doit être semblable à l’exemple suivant :

    ```bash
    LSNRCTL for Linux: Version 19.0.0.0.0 - Production on 18-SEP-2020 03:23:49

    Copyright (c) 1991, 2019, Oracle.  All rights reserved.

    Starting /u01/app/oracle/product/19.0.0/dbhome_1/bin/tnslsnr: please wait...

    TNSLSNR for Linux: Version 19.0.0.0.0 - Production
    System parameter file is /u01/app/oracle/product/19.0.0/dbhome_1/network/admin/listener.ora
    Log messages written to /u01/app/oracle/diag/tnslsnr/vmoracle19c/listener/alert/log.xml
    Listening on: (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=vmoracle19c.eastus.cloudapp.azure.com)(PORT=1521)))
    Listening on: (DESCRIPTION=(ADDRESS=(PROTOCOL=ipc)(KEY=EXTPROC1521)))

    Connecting to (DESCRIPTION=(ADDRESS=(PROTOCOL=TCP)(HOST=vmoracle19c.eastus.cloudapp.azure.com)(PORT=1521)))
    STATUS of the LISTENER
    ------------------------
    Alias                     LISTENER
    Version                   TNSLSNR for Linux: Version 19.0.0.0.0 - Production
    Start Date                18-SEP-2020 03:23:49
    Uptime                    0 days 0 hr. 0 min. 0 sec
    Trace Level               off
    Security                  ON: Local OS Authentication
    SNMP                      OFF
    Listener Parameter File   /u01/app/oracle/product/19.0.0/dbhome_1/network/admin/listener.ora
    Listener Log File         /u01/app/oracle/diag/tnslsnr/vmoracle19c/listener/alert/log.xml
    Listening Endpoints Summary...
     (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=vmoracle19c.eastus.cloudapp.azure.com)(PORT=1521)))
    (DESCRIPTION=(ADDRESS=(PROTOCOL=ipc)(KEY=EXTPROC1521)))
    The listener supports no services
    The command completed successfully
    ```

7.  Créez l'emplacement de la zone de récupération rapide (FRA) :

    ```bash
    mkdir /u02/fast_recovery_area
    ```

8.  Connectez-vous à la base de données :

    ```bash
    sqlplus / as sysdba
    ```

9.  Si ce n'est déjà fait, démarrez la base de données :

    ```bash
    SQL> startup
    ```

10. Définissez les variables d'environnement de la base de données pour la zone de récupération rapide :

    ```bash
    SQL> alter system set db_recovery_file_dest_size=4096M scope=both;
    SQL> alter system set db_recovery_file_dest='/u02/fast_recovery_area' scope=both;
    ```
    
11. Assurez-vous que la base de données est en mode journal d'archivage pour activer les sauvegardes en ligne.
    Commencez par vérifier l'état de l'archivage des journaux :

    ```bash
    SQL> SELECT log_mode FROM v$database;

    LOG_MODE
    ------------
    NOARCHIVELOG
    ```

    S'il est en mode NOARCHIVELOG, exécutez les commandes suivantes :

    ```bash
    SQL> SHUTDOWN IMMEDIATE;
    SQL> STARTUP MOUNT;
    SQL> ALTER DATABASE ARCHIVELOG;
    SQL> ALTER DATABASE OPEN;
    SQL> ALTER SYSTEM SWITCH LOGFILE;
    ```

12. Créez une table pour tester les opérations de sauvegarde et de restauration :

    ```bash
    SQL> create user scott identified by tiger quota 100M on users;
    SQL> grant create session, create table to scott;
    SQL> connect scott/tiger
    SQL> create table scott_table(col1 number, col2 varchar2(50));
    SQL> insert into scott_table VALUES(1,'Line 1');
    SQL> commit;
    SQL> quit
    ```

## <a name="back-up-to-azure-files"></a>Effectuer une sauvegarde sur Azure Files

Pour effectuer une sauvegarde sur Azure Files, procédez comme suit :

1. Configurer Azure Files.
1. Monter le partage de fichiers Azure sur votre machine virtuelle.
1. Sauvegardez la base de données.
1. Restaurez et récupérez la base de données.

### <a name="set-up-azure-files"></a>Configurer Azure Files

Au cours de cette étape, vous allez sauvegarder la base de données Oracle dans Azure Files à l’aide d’Oracle Recovery Manager (RMAN). Les partages de fichiers Azure sont des partages de fichiers entièrement gérés qui résident dans le cloud. Ils sont accessibles via le protocole SMB (Server Message Block) ou NFS (Network File System). Cette étape couvre la création d'un partage de fichiers qui utilise le protocole SMB pour procéder au montage sur votre machine virtuelle. Pour plus d’informations sur le montage à l’aide de NFS, voir [Comment créer un partage NFS](../../../storage/files/storage-files-how-to-create-nfs-shares.md).

Lors du montage d'Azure Files, nous utiliserons `cache=none` pour désactiver la mise en cache des données du partage de fichiers. Et pour veiller à ce que les fichiers créés dans le partage appartiennent à l'utilisateur oracle, définissez également les options `uid=oracle` et `gid=oinstall`. 

# <a name="portal"></a>[Portail](#tab/azure-portal)

Pour commencer, configurez votre compte de stockage.

1. Dans le portail Azure, sélectionnez **+ Créer une ressource** _, puis recherchez et sélectionnez _ *_Compte de stockage_**
    
    ![Capture d’écran montrant où créer une ressource et sélectionner le compte de stockage.](./media/oracle-backup-recovery/storage-1.png)
    
2. Dans la page Créer un compte de stockage, choisissez votre groupe de ressources existant ***rg-oracle** _, nommez votre compte de stockage _*_oracbkup1_*_ et choisissez _*_Stockage V2 (generalpurpose v2)_*_ pour Type de compte. Changez la réplication en _*_Stockage localement redondant (LRS)_*_ et définissez Performances sur _*_Standard_**. Assurez-vous que l'Emplacement est défini sur la même région que toutes les autres ressources du groupe de ressources. 
    
    ![Capture d’écran montrant où choisir votre groupe de ressources existant.](./media/oracle-backup-recovery/file-storage-1.png)
   
3. Cliquez sur l’onglet ***Avancé** _ et, sous Azure Files, définissez _*_Partages de fichiers volumineux_*_ sur _*_Activé_**. Cliquez sur Vérifier + créer, puis sur Créer.
    
    ![Capture d’écran montrant où définir Partages de fichiers volumineux sur Activé.](./media/oracle-backup-recovery/file-storage-2.png)
    
4. Une fois le compte de stockage créé, accédez à la ressource et sélectionnez ***Partages de fichiers***.
    
    ![Capture d’écran montrant où sélectionner Partages de fichiers.](./media/oracle-backup-recovery/file-storage-3.png)
    
5. Cliquez sur * **+ Partage de fichiers** _ et, dans le panneau _*_Nouveau partage de fichiers_*_, nommez votre partage de fichiers _*_orabkup1_*_. Définissez _*_Quota_*_ sur _*_10240_*_  Gio et cochez _*_Transaction optimisée_*_ comme niveau. Le quota reflète une limite supérieure que le partage de fichiers peut atteindre. Comme nous utilisons un stockage standard, les ressources reposent sur le paiement à l'utilisation (PAYG) et ne sont pas approvisionnées. Par conséquent, leur définition sur 10 Tio n'entraîne pas de frais au-delà de ce que vous utilisez. Si votre stratégie de sauvegarde nécessite davantage de stockage, vous devez fixer le quota sur un niveau approprié pour contenir toutes les sauvegardes.   Une fois le panneau Nouveau partage de fichiers renseigné, cliquez sur _*_Créer_**.
    
    ![Capture d’écran montrant où ajouter un nouveau partage de fichiers.](./media/oracle-backup-recovery/file-storage-4.png)
    
    
6. Cliquez ensuite sur ***orabkup1*** sur la page Paramètres du partage de fichiers. 
    Cliquez sur l’onglet ***Se connecter** _ pour ouvrir le panneau Se connecter, puis cliquez sur l’onglet _ *_Linux_**. Copiez les commandes fournies pour monter le partage de fichiers à l'aide du protocole SMB. 
    
    ![Page d'ajout d'un compte de stockage](./media/oracle-backup-recovery/file-storage-5.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pour configurer votre compte de stockage et le partage de fichiers, exécutez les commandes suivantes dans Azure CLI.

1. Créez le compte de stockage dans le même groupe de ressources et au même emplacement que votre machine virtuelle :
   ```azurecli
   az storage account create -n orabackup1 -g rg-oracle -l eastus --sku Standard_LRS --enable-large-file-share
   ```

2. Créez un partage de fichiers sur le compte de stockage avec un quota de 10 Tio :

   ```azurecli
   az storage share create --account-name orabackup1 --name orabackup --quota 10240
   ```

3. Récupérez la clé primaire du compte de stockage (key1) car vous en aurez besoin lors du montage du partage de fichiers sur votre machine virtuelle :

   ```azurecli
   az storage account keys list --resource-group rg-oracle --account-name orabackup1
   ```

---

### <a name="mount-the-azure-storage-file-share-to-your-vm"></a>Monter le partage de fichiers Azure Files sur votre machine virtuelle

1. Créez le point de montage :

   ```bash
   sudo mkdir /mnt/orabackup
   ```

2. Configurez les informations d'identification :

   ```bash
   if [ ! -d "/etc/smbcredentials" ]; then
    sudo mkdir /etc/smbcredentials
   fi
   ```

   Remplacez `<Your Storage Account Key1>` par la clé de compte de stockage récupérée précédemment, avant d'exécuter la commande suivante :
   ```bash
   if [ ! -f "/etc/smbcredentials/orabackup1.cred" ]; then
     sudo bash -c 'echo "username=orabackup1" >> /etc/smbcredentials/orabackup1.cred'
     sudo bash -c 'echo "password=<Your Storage Account Key1>" >> /etc/smbcredentials/orabackup1.cred'
   fi
   ```

3. Modifiez les autorisations sur le fichier d'informations d’identification :

   ```bash
   sudo chmod 600 /etc/smbcredentials/orabackup1.cred
   ```

4. Ajoutez le montage au fichier /etc/fstab :

   ```bash
   sudo bash -c 'echo "//orabackup1.file.core.windows.net/orabackup /mnt/orabackup cifs nofail,vers=3.0,credentials=/etc/smbcredentials/orabackup1.cred,dir_mode=0777,file_mode=0777,serverino,cache=none,uid=oracle,gid=oinstall" >> /etc/fstab'
   ```

5. Exécutez les commandes suivantes pour monter le stockage Azure Files à l'aide du protocole SMB :

   ```bash
   sudo mount -t cifs //orabackup1.file.core.windows.net/orabackup /mnt/orabackup -o vers=3.0,credentials=/etc/smbcredentials/orabackup1.cred,dir_mode=0777,file_mode=0777,serverino,cache=none,uid=oracle,gid=oinstall
   ```

   Si vous recevez une erreur semblable à la suivante :

   ```output
   mount: wrong fs type, bad option, bad superblock on //orabackup1.file.core.windows.net/orabackup 
   ```

   cela signifie peut-être que le package CIFS n'est pas installé sur votre hôte Linux. 
   
   Pour déterminer si CIS est installé, exécutez la commande suivante :

   ```bash
   sudo rpm -qa|grep cifs-utils
   ```

   Si la commande ne renvoie aucune sortie, installez le package CIFS à l'aide de la commande suivante :

   ```bash 
   sudo yum install cifs-utils
   ```

   Réexécutez la commande de montage ci-dessus pour monter le stockage Azure Files.

6. Vérifiez que le partage de fichiers est correctement monté à l'aide de la commande suivante :

   ```bash
   df -h
   ```

   La sortie doit être semblable à ce qui suit :

   ```output
   $ df -h
   Filesystem                                    Size  Used Avail Use% Mounted on
   devtmpfs                                      3.3G     0  3.3G   0% /dev
   tmpfs                                         3.3G     0  3.3G   0% /dev/shm
   tmpfs                                         3.3G   17M  3.3G   1% /run
   tmpfs                                         3.3G     0  3.3G   0% /sys/fs/cgroup
   /dev/sda2                                      30G  9.1G   19G  34% /
   /dev/sdc1                                      59G  2.7G   53G   5% /u02
   /dev/sda1                                     497M  199M  298M  41% /boot
   /dev/sda15                                    495M  9.7M  486M   2% /boot/efi
   tmpfs                                         671M     0  671M   0% /run/user/54321
   /dev/sdb1                                      14G  2.1G   11G  16% /mnt/resource
   tmpfs                                         671M     0  671M   0% /run/user/54322
   //orabackup1.file.core.windows.net/orabackup   10T     0   10T   0% /mnt/orabackup
   ```

### <a name="back-up-the-database"></a>Sauvegarder la base de données

Dans cette section, nous utiliserons l'outil Oracle Recovery Manager (RMAN) pour effectuer une sauvegarde complète de la base de données et des journaux d'archivage, et écrire la sauvegarde en tant que jeu de sauvegarde sur le partage de fichiers Azure Files monté précédemment. 

1. Configurez l'outil RMAN pour effectuer la sauvegarde sur le point de montage Azure Files :

    ```bash
    $ rman target /
    RMAN> configure snapshot controlfile name to '/mnt/orabkup/snapcf_ev.f';
    RMAN> configure channel 1 device type disk format '/mnt/orabkup/%d/Full_%d_%U_%T_%s';
    RMAN> configure channel 2 device type disk format '/mnt/orabkup/%d/Full_%d_%U_%T_%s'; 
    ```

2. Étant donné que la taille maximale des partages de fichiers standard Azure est de 1 Tio, nous allons limiter la taille des éléments de sauvegarde RMAN à 1 Tio. (Notez que la taille maximale des partages de fichiers premium est de 4 Tio. Pour plus d'informations, consultez [Objectifs de scalabilité et de performances d'Azure Files](../../../storage/files/storage-files-scale-targets.md).

    ```bash
    RMAN> configure channel device type disk maxpiecesize 1000G;
    ```

3. Confirmez les détails du changement de configuration :

    ```bash
    RMAN> show all;
    ```

4. Exécutez la sauvegarde. La commande suivante permet d'effectuer une sauvegarde complète de la base de données, fichiers journaux d'archivage compris, sous forme de jeu de sauvegarde au format compressé :   

    ```bash
    RMAN> backup as compressed backupset database plus archivelog;
    ```

Vous venez de sauvegarder la base de données en ligne à l’aide d’Oracle RMAN, et la sauvegarde a été placée dans Azure Files. Cette méthode présente l’avantage d’utiliser les fonctionnalités de RMAN tout en stockant les sauvegardes dans Azure Files, où elles sont accessibles à partir d’autres machines virtuelles, ce qui peut s’avérer utile si vous avez besoin de cloner la base de données.  
    
L’utilisation de RMAN et d’Azure Files pour la sauvegarde de bases de données présente de nombreux avantages, mais le temps de sauvegarde et de restauration est lié à la taille de la base de données. Par conséquent, pour les bases de données très volumineuses, cette opération peut prendre beaucoup de temps. Un autre mécanisme de sauvegarde, utilisant des sauvegardes de machines virtuelles cohérentes avec les applications Sauvegarde Azure, a recours à la technologie de capture instantanée qui permet des sauvegardes très rapides, quelle que soit la taille de la base de données. L'intégration à un coffre Recovery Services offre un stockage sécurisé de vos sauvegardes Oracle Database sur le stockage cloud Azure, ainsi qu'un accès à partir d'autres machines virtuelles et régions Azure. 

### <a name="restore-and-recover-the-database"></a>Restaurer et récupérer la base de données

1.  Arrêtez l'instance d'Oracle :

    ```bash
    sqlplus / as sysdba
    SQL> shutdown abort
    ORACLE instance shut down.
    ```

2.  Supprimez les fichiers de données de la base de données :

    ```bash
    cd /u02/oradata/TEST
    rm -f *.dbf
    ```

3. Les commandes suivantes utilisent RMAN pour restaurer les fichiers de données manquants et récupérer la base de données :

    ```bash
    rman target /
    RMAN> startup mount;
    RMAN> restore database;
    RMAN> recover database;
    RMAN> alter database open;
    ```
    
4. Vérifiez que le contenu de la base de données a été entièrement récupéré :

    ```bash
    RMAN> SELECT * FROM scott.scott_table;
    ```


Le processus de sauvegarde et de récupération de la base de données Oracle Database 19c sur une machine virtuelle Linux Azure est maintenant terminé.

## <a name="delete-the-vm"></a>Supprimer la machine virtuelle

Quand vous n’avez plus besoin de la machine virtuelle, vous pouvez utiliser la commande suivante pour supprimer le groupe de ressources, la machine virtuelle et toutes les ressources associées :

```azurecli
az group delete --name rg-oracle
```

## <a name="next-steps"></a>Étapes suivantes

[Tutoriel : Créer des machines virtuelles hautement disponibles](../../linux/create-cli-complete.md)

[Explorer des exemples Azure CLI de déploiement de machines virtuelles](https://github.com/Azure-Samples/azure-cli-samples/tree/master/virtual-machine)
