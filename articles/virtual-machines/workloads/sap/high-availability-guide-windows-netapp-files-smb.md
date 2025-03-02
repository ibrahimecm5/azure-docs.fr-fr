---
title: Haute disponibilité des machines virtuelles Azure pour SAP NW sur Windows avec Azure NetApp Files (SMB) | Microsoft Docs
description: Haute disponibilité pour SAP NetWeaver sur des machines virtuelles Azure sur Windows avec Azure NetApp Files (SMB) pour les applications SAP
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/29/2021
ms.author: radeltch
ms.openlocfilehash: 59c977b94efe62489208fca0d2514a5d2e381aff
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124803685"
---
# <a name="high-availability-for-sap-netweaver-on-azure-vms-on-windows-with-azure-netapp-filessmb-for-sap-applications"></a>Haute disponibilité pour SAP NetWeaver sur des machines virtuelles Azure sur Windows avec Azure NetApp Files (SMB) pour les applications SAP

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md
[high-availability-guide]:high-availability-guide.md

[anf-azure-doc]:../../../azure-netapp-files/azure-netapp-files-introduction.md
[anf-avail-matrix]:https://azure.microsoft.com/global-infrastructure/services/?products=storage&regions=all
[anf-sap-applications-azure]:https://www.netapp.com/us/media/tr-4746.pdf

[2205917]:https://launchpad.support.sap.com/#/notes/2205917
[1944799]:https://launchpad.support.sap.com/#/notes/1944799
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[suse-ha-guide]:https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
[suse-drbd-guide]:https://www.suse.com/documentation/sle-ha-12/singlehtml/book_sleha_techguides/book_sleha_techguides.html
[suse-ha-12sp3-relnotes]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP3/

[template-multisid-xscs]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fapplication-workloads%2Fsap%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[template-converged]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fapplication-workloads%2Fsap%2Fsap-3-tier-marketplace-image-converged-md%2Fazuredeploy.json
[template-file-server]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fapplication-workloads%2Fsap%2Fsap-file-server-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability.md
[nfs-ha]:high-availability-guide-suse-nfs.md

Cet article décrit comment déployer, configurer les machines virtuelles, installer l’infrastructure de cluster et installer un système SAP NetWeaver 7.50 à haute disponibilité sur des machines virtuelles Windows, à l’aide de [SMB](/windows/win32/fileio/microsoft-smb-protocol-and-cifs-protocol-overview) sur [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md).  

La couche de base de données n’est pas couverte en détail dans cet article. Nous supposons que le [réseau virtuel](../../../virtual-network/virtual-networks-overview.md) Azure a déjà été créé.  

Commencez par lire les notes et publications SAP suivantes :

* [Documentation Azure NetApp Files][anf-azure-doc] 
* La note SAP [1928533][1928533], qui contient :  
  * une liste des tailles de machines virtuelles Azure prises en charge pour le déploiement de logiciels SAP
  * des informations importantes sur la capacité en fonction de la taille des machines virtuelles Azure
  * les logiciels SAP pris en charge et les combinaisons entre système d’exploitation et base de données
  * la version du noyau SAP requise pour Windows sur Microsoft Azure
* La note SAP [2015553][2015553] répertorie les conditions préalables au déploiement de logiciels SAP pris en charge par SAP sur Azure.
* La note SAP [2178632][2178632] contient des informations détaillées sur toutes les métriques de surveillance rapportées pour SAP sur Azure.
* La note SAP [1999351][1999351] contient des informations de dépannage supplémentaires pour l’extension d’analyse Azure améliorée pour SAP.
* La note SAP [2287140](https://launchpad.support.sap.com/#/notes/2287140) répertorie les conditions préalables pour la fonctionnalité d’autorité de certification prise en charge par SAP du protocole SMB 3.x.
* La note SAP [2802770](https://launchpad.support.sap.com/#/notes/2802770) contient des informations sur la résolution des problèmes liés à l’exécution lente de la transaction SAP AL11 sur Windows 2012 et 2016.
* La note SAP [1911507](https://launchpad.support.sap.com/#/notes/1911507) contient des informations sur la fonctionnalité de basculement transparent pour un partage de fichiers sur Windows Server avec le protocole SMB 3.0.
* La note SAP [662452](https://launchpad.support.sap.com/#/notes/662452) offre une recommandation (désactivation de la génération de noms 8.3) pour résoudre les erreurs/performances médiocres du système de fichiers lors des accès aux données.
* [Installer la haute disponibilité SAP NetWeaver sur un cluster de basculement Windows et un partage de fichiers pour des instances SAP ASCS/SCS sur Azure](./sap-high-availability-installation-wsfc-file-share.md) 
* [Scénarios et architecture de haute disponibilité de machines virtuelles Azure pour SAP NetWeaver](./sap-high-availability-architecture-scenarios.md)
* [Ajouter un port de sondage dans la configuration du cluster ASCS](sap-high-availability-installation-wsfc-file-share.md)
* [Installation d’une instance (A)SCS sur un cluster de basculement](https://www.sap.com/documents/2017/07/f453332f-c97c-0010-82c7-eda71af511fa.html)
* [Créer un volume SMB pour Azure NetApp Files](../../../azure-netapp-files/create-active-directory-connections.md#requirements-for-active-directory-connections)
* [Applications SAP NetApp su Microsoft Azure avec Azure NetApp Files][anf-sap-applications-azure]

> [!IMPORTANT]
> ATTENTION : Sachez que l’installation d’un système SAP avec SWPM sur un partage SMB hébergé sur un volume SMB [Azure NetApp Files][anf-azure-doc], peut échouer avec une erreur d’installation pour cause d’autorisations insuffisantes, telle que « warningPerm n’est pas défini ». Pour éviter une telle erreur, l’utilisateur sous lequel le contexte SWPM est exécuté doit disposer d’un privilège élevé « Administrateur de domaine » lors de l’installation du système SAP.  

## <a name="overview"></a>Vue d’ensemble

SAP a développé une nouvelle approche et une alternative aux disques partagés pour le clustering d’une instance SAP ASCS/SCS sur un cluster de basculement Windows. Au lieu d’utiliser des disques partagés de cluster, il est possible d’utiliser un partage de fichiers SMB pour déployer des fichiers d’hôte global SAP. Azure NetApp Files prend en charge SMBv3 (ainsi que NFS) avec la liste de contrôle d’accès NTFS à l’aide d’Active Directory. Azure NetApp Files est automatiquement hautement disponible (car il s’agit d’un service PaaS). Ces fonctionnalités font d’Azure NetApp Files l’option idéale pour héberger le partage de fichiers SMB pour SAP global.  
[Azure Active Directory (AD) Domain Services](../../../active-directory-domain-services/overview.md) et [Active Directory Domain Services (AD DS)](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) sont tous deux pris en charge. Vous pouvez utiliser des contrôleurs de domaine Active Directory existants avec Azure NetApp Files. Les contrôleurs de domaine peuvent figurer dans Azure en tant que machines virtuelles ou en local via ExpressRoute ou S2S VPN. Dans cet article, nous allons utiliser un contrôleur de domaine dans une machine virtuelle Azure.  
La haute disponibilité pour les services centraux SAP Netweaver nécessite un stockage partagé. Pour parvenir à cela sur Windows, il était nécessaire de créer un cluster SOFS ou d’utiliser un logiciel de disque partagé de cluster comme SIOS. Maintenant, il est possible d’atteindre la haute disponibilité SAP Netweaver à l’aide d’un stockage partagé, déployé sur Azure NetApp Files. L’utilisation d’Azure NetApp Files pour le stockage partagé élimine le besoin de recourir à SOFS ou SIOS.  

> [!NOTE]
> Le clustering d’instances SAP ASCS/SCS avec le partage de fichiers est pris en charge pour SAP NetWeaver 7.40 (et versions ultérieures), avec SAP Kernel 7.49 (et versions ultérieures).  

![Architecture à haute disponibilité SAP ASCS/SCS avec partage SMB](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb.png)

Conditions préalables pour un partage de fichiers SMB :
* Protocole SMB 3.0 (ou version ultérieure)
* Possibilité de définir les listes de contrôle d’accès (ACL, access control list) Active Directory pour les groupes d’utilisateurs Active Directory et l’objet d’ordinateur computer$.
* La haute disponibilité doit être activée pour le partage de fichiers.

Le partage pour les services centraux SAP de cette architecture de référence est proposé par Azure NetApp Files :

![Détails sur l’architecture à haute disponibilité SAP ASCS/SCS avec partage SMB](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb-detail.png)

## <a name="create-and-mount-smb-volume-for-azure-netapp-files"></a>Créer et monter un volume SMB pour Azure NetApp Files

Procédez comme suit pour préparer l’utilisation d’Azure NetApp Files.  

1. Créez un compte Azure NetApp, en suivant les étapes décrites dans [Créer un compte NetApp](../../../azure-netapp-files/azure-netapp-files-create-netapp-account.md)  
2. Configurez le pool de capacité, en suivant les instructions de la rubrique [Configurer un pool de capacité](../../../azure-netapp-files/azure-netapp-files-set-up-capacity-pool.md)
3. Les ressources d’Azure NetApp Files doivent résider dans un sous-réseau délégué. Suivez les instructions de la rubrique [Déléguer un sous-réseau à Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-delegate-subnet.md) pour créer un sous-réseau délégué.  

   > [!IMPORTANT]
   > Vous devez créer des connexions Active Directory avant de créer un volume SMB. Passez en revue la [configuration requise pour les connexions Active Directory](../../../azure-netapp-files/create-active-directory-connections.md#requirements-for-active-directory-connections).  
   >   
   > Lorsque vous créez la connexion Active Directory, veillez à entrer un préfixe de serveur SMB (compte d’ordinateur) de plus de 8 caractères pour éviter la limitation du nom d’hôte de 13 caractères pour les applications SAP (un suffixe est automatiquement ajouté au nom du compte de l’ordinateur SMB).     
   > Les limitations de nom d’hôte des applications SAP sont décrites dans [2718300 - Limitations de longueur de nom d’hôte physique et virtuel](https://launchpad.support.sap.com/#/notes/2718300) et [611361 - Noms d’hôte de serveurs de plateforme SAP ABAP](https://launchpad.support.sap.com/#/notes/611361).  

4. Créez la connexion Active Directory, comme décrit dans [Créer une connexion Active Directory](../../../azure-netapp-files/create-active-directory-connections.md#create-an-active-directory-connection)  
5. Créez un volume SMB Azure NetApp Files, en suivant les instructions fournies dans [Ajouter un volume SMB](../../../azure-netapp-files/azure-netapp-files-create-volumes-smb.md#add-an-smb-volume)  
6. Montez le volume SMB sur votre machine virtuelle Windows.

> [!TIP]
> Vous trouverez des instructions sur la façon de monter le volume Azure NetApp Files, si vous naviguez dans le [Portail Azure](https://portal.azure.com/#home) vers l’objet Azure NetApp Files, cliquez sur le panneau **Volumes**, puis **Instructions de montage**.  

## <a name="prepare-the-infrastructure-for-sap-ha-by-using-a-windows-failover-cluster"></a>Préparer l’infrastructure pour la haute disponibilité SAP à l’aide d’un cluster de basculement Windows 

1. [Définissez les règles d’équilibrage de charge ASCS/SCS par défaut pour l’équilibreur de charge interne Azure](./sap-high-availability-infrastructure-wsfc-shared-disk.md#fe0bd8b5-2b43-45e3-8295-80bee5415716).
2. [Ajoutez des machines virtuelles Windows au domaine](./sap-high-availability-infrastructure-wsfc-shared-disk.md#e69e9a34-4601-47a3-a41c-d2e11c626c0c).
3. [Ajoutez des entrées de registre aux deux nœuds de cluster de l’instance SAP ASCS/SCS](./sap-high-availability-infrastructure-wsfc-shared-disk.md#661035b2-4d0f-4d31-86f8-dc0a50d78158)
4. [Configurez un cluster de basculement Windows Server pour une instance SAP ASCS/SCS](./sap-high-availability-infrastructure-wsfc-shared-disk.md#0d67f090-7928-43e0-8772-5ccbf8f59aab)
5. Si vous utilisez Windows Server 2016, nous vous recommandons de configurer un [témoin Azure Cloud](/windows-server/failover-clustering/deploy-cloud-witness).


## <a name="install-sap-ascs-instance-on-both-nodes"></a>Installer l’instance SAP ASCS sur les deux nœuds

Vous avez besoin des logiciels suivants de SAP :
   * Outil d’installation SAP Software Provisioning Manager (SWPM) version SPS25 (ou ultérieure).
   * Noyau SAP 7.49 ou version ultérieure
   * Créez un nom d’hôte virtuel (nom de réseau en cluster) pour l’instance SAP ASCS/SCS en cluster, comme décrit dans [Créer un nom d’hôte virtuel pour l’instance SAP ASCS/SCS en cluster](./sap-high-availability-installation-wsfc-shared-disk.md#a97ad604-9094-44fe-a364-f89cb39bf097).

> [!NOTE]
> Le clustering d’instances SAP ASCS/SCS avec le partage de fichiers est pris en charge pour SAP NetWeaver 7.40 (et versions ultérieures), avec SAP Kernel 7.49 (et versions ultérieures).  

### <a name="install-an-ascsscs-instance-on-the-first-ascsscs-cluster-node"></a>Installer une instance ASCS/SCS sur le premier nœud de cluster ASCS/SCS

1. Installez une instance ASCS/SCS SAP sur le premier nœud de cluster. Démarrez l’outil d’installation de SAP SWPM, puis accédez à : **Product (Produit)**  > **DBMS (SGBD)** > Installation > Application Server ABAP (ou Java) > High-Availability System (Système haute disponibilité) > ASCS/SCS instance (Instance ASCS/SCS) > First cluster node (Premier nœud de cluster).  

2. Sélectionnez **File Share Cluster (Cluster de partage de fichiers)** en tant que configuration de partage de cluster dans SWPM.  
3. Lorsque vous y êtes invité à l’étape **SAP System Cluster Parameters (Paramètres du cluster du système SAP)**, entrez le nom d’hôte du partage SMB Azure NetApp Files que vous avez déjà créé comme **File Share Host Name (Nom d’hôte du partage de fichiers)**.  Dans cet exemple, le nom d’hôte du partage SMB est **anfsmb-9562**. 

   > [!IMPORTANT]
   > Si les résultats de l’outil de vérification de la configuration requise dans SWPM indiquent que la fonctionnalité de disponibilité continue n’est pas remplie, vous pouvez résoudre ce problème en suivant les instructions figurant dans [Retard de message d’erreur lorsque vous essayez d’accéder à un dossier partagé qui n’existe plus dans Windows](https://support.microsoft.com/help/2820470/delayed-error-message-when-you-try-to-access-a-shared-folder-that-no-l).  

   > [!TIP]
   > Si les résultats de l’outil de vérification de la configuration requise dans SWPM indiquent qu’une condition de taille d’échange n’est pas remplie, vous pouvez ajuster la taille d’échange en accédant à Poste de travail > Propriétés système > Paramètres de performances > Options avancées > Mémoire virtuelle > Modifier.  

4. Configurez une ressource de cluster SAP, le port de sonde `SAP-SID-IP`, à l’aide de PowerShell. Exécutez cette configuration sur l’un des nœuds de cluster SAP ASCS/SCS, comme décrit dans [Configurer le port de sonde](./sap-high-availability-installation-wsfc-shared-disk.md#10822f4f-32e7-4871-b63a-9b86c76ce761).

### <a name="install-an-ascsscs-instance-on-the-second-ascsscs-cluster-node"></a>Installer une instance ASCS/SCS sur le deuxième nœud de cluster ASCS/SCS

1. Installez une instance ASCS/SCS SAP sur le deuxième nœud de cluster. Démarrez l’outil d’installation SAP SWPM, puis accédez à **Product (Produit)**  > **DBMS (SGBD)** > Installation > Application Server ABAP (ou Java) > High-Availability System (Système à haute disponibilité) > ASCS/SCS instance (Instance ASCS/SCS) > Additional cluster node (Noeud de cluster supplémentaire).  

### <a name="update-the-sap-ascsscs-instance-profile"></a>Mettre à jour le profil d’instance SAP ASCS/SCS

Mettez à jour les paramètres dans le profil d’instance ASCS/SCS SAP \<SID>_ASCS/SCS\<Nr>_ \<Host>.


| Nom du paramètre | Valeur du paramètre |
| --- | --- |
| gw/netstat_once | **0** |
| enque/encni/set_so_keepalive  | **true** |
| service/ha_check_node | **1** |

Le paramètre `enque/encni/set_so_keepalive` est requis uniquement si vous utilisez ENSA1.  
Redémarrez l’instance SAP ASCS/SCS. Définissez les paramètres `KeepAlive` sur les deux nœuds de cluster SAP ASCS/SCS en suivant les instructions indiquées dans [Set registry entries on the cluster nodes of the SAP ASCS/SCS instance (Définir des entrées de Registre sur les nœuds de cluster de l’instance SAP ASCS/SCS)][high-availability-guide]. 

### <a name="install-a-dbms-instance-and-sap-application-servers"></a>Installer une instance de SGBD et les serveurs d’applications SAP

Finalisez votre installation SAP en installant :

   * Une instance de SGBD  
   * Un serveur d’applications SAP principal  
   * Un serveur d’applications SAP supplémentaire  

## <a name="test-the-sap-ascsscs-instance-failover"></a>Testez le basculement de l’instance SAP ASCS/SCS 

### <a name="fail-over-from-cluster-node-a-to-cluster-node-b-and-back"></a>Basculement du nœud de cluster A vers le nœud de cluster B et inversément
Dans ce scénario de test, nous faisons référence au nœud de cluster sapascs1 comme nœud A et au nœud de cluster sapascs2 en tant que nœud B.

1. Vérifiez que les ressources du cluster s’exécutent sur le nœud A. ![Figure 1 : Ressources du cluster de basculement Windows Server exécutées sur le nœud A avant le test de basculement](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb-figure-1.png)  

2. Redémarrez le nœud de cluster A. Les ressources de cluster SAP sont déplacées vers le nœud de cluster B. ![Figure 2 : Ressources du cluster de basculement Windows Server exécutées sur le nœud B après le test de basculement](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb-figure-2.png)  


## <a name="lock-entry-test"></a>Test d’entrée de verrou

1. Vérifiez que le serveur ERS (Enqueue Replication Server) est actif  
2. Connectez-vous au système SAP, exécutez la transaction SU01 et ouvrez un ID utilisateur en mode modification. Cela génère une entrée de verrou SAP.  
3. Lorsque vous êtes connecté au système SAP, affichez l’entrée de verrou, en accédant à la transaction ST12.  
4. Basculez les ressources ASCS du nœud de cluster A vers le nœud de cluster B.  
5. Vérifiez que l’entrée de verrou, générée avant le basculement des ressources de cluster SAP ASCS/SCS, est conservée.  

![Figure 3 : L’entrée de verrou est conservée après le test de basculement](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb-figure-3.png)  

Pour plus d’informations, consultez [Troubleshooting for Enqueue Failover in ASCS with ERS](https://wiki.scn.sap.com/wiki/display/SI/Troubleshooting+for+Enqueue+Failover+in+ASCS+with+ERS) (en anglais)

## <a name="optional-configurations"></a>Configurations facultatives

Les diagrammes suivants montrent plusieurs instances SAP sur des machines virtuelles Azure exécutant le cluster de basculement Microsoft Windows pour réduire le nombre total de machines virtuelles.

Il peut s’agir de serveurs d’applications SAP locaux sur un cluster ASCS/SCS SAP ou d’un rôle de cluster ASCS/SCS SAP sur des nœuds Always On Microsoft SQL Server.

> [!IMPORTANT]
> L’installation d’un serveur d’applications SAP local sur un nœud Always On SQL Server n’est pas prise en charge.
>

ASCS/SCS SAP et la base de données Microsoft SQL Server sont tous deux des points de défaillance uniques (SPOF). Pour protéger ces points de défaillance uniques dans un environnement Windows, Azure NetApp Files SMB est utilisé.

Bien que la consommation de ressources ASCS/SCS SAP soit relativement faible, il est recommandé de réduire de 2 Go la configuration de la mémoire pour SQL Server ou le serveur d’applications SAP.

### <a name="sap-application-servers-on-wsfc-nodes-using-netapp-files-smb"></a><a name="5121771a-7618-4f36-ae14-ccf9ee5f2031"></a>Serveurs d’applications SAP sur des nœuds WSFC utilisant NetApp Files SMB

![Figure 4 : configuration du clustering de basculement Windows Server dans Azure avec Windows NetApp Files SMB et le serveur d’applications SAP installé localement][sap-ha-guide-figure-8007A]

> [!NOTE]
> L’illustration montre l’utilisation de disques locaux supplémentaires. Cela est facultatif pour les clients qui n’installent pas les logiciels d’application sur le lecteur du système d’exploitation (C:\)
>
### <a name="sap-ascsscs-on-sql-server-always-on-nodes-using-azure-netapp-files-smb"></a><a name="01541cf2-0a03-48e3-971e-e03575fa7b4f"></a> ASCS/SCS SAP sur des nœuds Always On SQL Server utilisant Azure NetApp Files SMB

> [!IMPORTANT]
> L’utilisation d’Azure NetApp Files SMB pour tout volume SQL Server n’est pas prise en charge.
> 

![Figure : ASCS/SCS SAP sur des nœuds Always On SQL Server utilisant Azure NetApp Files SMB][sap-ha-guide-figure-8007B]

> [!NOTE]
> L’illustration montre l’utilisation de disques locaux supplémentaires. Cela est facultatif pour les clients qui n’installent pas les logiciels d’application sur le lecteur du système d’exploitation (C:\)
>

## <a name="next-steps"></a>Étapes suivantes

* [Planification et implémentation de machines virtuelles Azure pour SAP][planning-guide]
* [Déploiement de machines virtuelles Azure pour SAP][deployment-guide]
* [Déploiement SGBD de machines virtuelles Azure pour SAP][dbms-guide]
* Pour découvrir comment établir la haute disponibilité et planifier la récupération d’urgence de SAP 
* HANA sur Azure (grandes instances), consultez [Haute disponibilité et récupération d’urgence SAP HANA (grandes instances) sur Azure](hana-overview-high-availability-disaster-recovery.md).
* Pour savoir comment établir une haute disponibilité et planifier la récupération d’urgence de SAP HANA sur des machines virtuelles Azure, consultez [Haute disponibilité de SAP HANA sur des machines virtuelles Azure][sap-hana-ha]

[sap-ha-guide-figure-8007A]:./media/virtual-machines-shared-sap-high-availability-guide/ha-smb-as.png
[sap-ha-guide-figure-8007B]:./media/virtual-machines-shared-sap-high-availability-guide/ha-sql-ascs-smb.png