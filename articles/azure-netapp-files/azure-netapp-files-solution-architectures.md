---
title: Architectures de solution avec Azure NetApp Files | Microsoft Docs
description: Cet article contient des références aux meilleures pratiques associées aux architectures de solution avec Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/4/2021
ms.author: b-juche
ms.openlocfilehash: a69dd2528b4804e434cf6652d1ce2b03357025ee
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131848607"
---
# <a name="solution-architectures-using-azure-netapp-files"></a>Architectures de solution avec Azure NetApp Files
Cet article fournit des références aux meilleures pratiques pouvant vous aider à comprendre les architectures de solution pour l’utilisation d’Azure NetApp Files.  

Le diagramme suivant résume les catégories d’architectures de solution que Azure NetApp Files offre :

![Catégories d’architectures de solution](../media/azure-netapp-files/solution-architecture-categories.png)

## <a name="linux-oss-apps-and-database-solutions"></a>Applications et solutions de base de données OSS Linux

Cette section fournit des références pour les solutions de bases de données et applications OSS Linux. 

### <a name="linux-oss-apps"></a>Applications OSS Linux

* [Migration d’AIX UNIX local vers Azure Linux – Exemples de scénarios Azure](/azure/architecture/example-scenario/unix-migration/migrate-aix-azure-linux)

### <a name="mainframe-refactor"></a>Refactorisation de mainframe

* [Refactorisation générale de mainframe vers Azure – Exemples de scénarios Azure](/azure/architecture/example-scenario/mainframe/general-mainframe-refactor)
* [Refactoriser des applications mainframe avec options avancées – Exemples de scénarios Azure](/azure/architecture/example-scenario/mainframe/refactor-mainframe-applications-advanced)

### <a name="oracle"></a>Oracle

* [Oracle Database avec Azure NetApp Files : exemples de scénarios Azure](/azure/architecture/example-scenario/file-storage/oracle-azure-netapp-files)
* [Bases de données Oracle sur Microsoft Azure avec Azure NetApp Files](https://www.netapp.com/media/17105-tr4780.pdf)
* [Images de machine virtuelle Oracle et leur déploiement sur Microsoft Azure : options de configuration de stockage partagé](../virtual-machines/workloads/oracle/oracle-vm-solutions.md#shared-storage-configuration-options)
* [Performances des bases de données Oracle sur des volumes uniques Azure NetApp Files](performance-oracle-single-volumes.md)
* [Avantages de l’utilisation d’Azure NetApp Files avec Oracle Database](solutions-benefits-azure-netapp-files-oracle-database.md)

### <a name="machine-learning"></a>Machine Learning
*   [Cloudera Machine Learning](https://docs.cloudera.com/machine-learning/cloud/requirements-azure/topics/ml-requirements-azure.html)
*   [Formation distribuée dans Azure : détection de voie - conception de solution](https://www.netapp.com/media/32427-tr-4896-design.pdf)
*   [Formation distribuée dans Azure : prédiction de taux de clic – Conception de solution](https://docs.netapp.com/us-en/netapp-solutions/ai/aks-anf_introduction.html)

### <a name="education"></a>Education
* [Moodle sur stockage NFS Azure NetApp Files](https://techcommunity.microsoft.com/t5/azure-architecture-blog/azure-netapp-files-for-nfs-storage-with-moodle/ba-p/2300630)

## <a name="windows-apps-and-sql-server-solutions"></a>Applications Windows et les solutions de SQL Server

Cette section fournit des références pour les applications Windows et les solutions de SQL Server.

### <a name="file-sharing-and-global-file-caching"></a>Partage de fichiers et mise en cache de fichiers globaux

* [Récupération d’urgence pour Enterprise File Shares avec Azure NetApp Files et DFS Namespaces](https://techcommunity.microsoft.com/t5/azure-architecture-blog/disaster-recovery-for-enterprise-file-shares/ba-p/2808757)
* [Build Your Own Azure NFS? Wrestling Linux File Shares into Cloud](https://cloud.netapp.com/blog/ma-anf-blg-build-your-own-linux-nfs-file-shares)
* [Globally Distributed Enterprise File Sharing with Azure NetApp Files and NetApp Global File Cache](https://f.hubspotusercontent20.net/hubfs/525875/NA-580-0521-Architecture-Doc-R3.pdf)
* [Conformité cloud pour Azure NetApp Files](https://cloud.netapp.com/hubfs/Cloud%20Compliance%20for%20Azure%20NetApp%20Files%20-%20November%202020.pdf)

### <a name="sql-server"></a>SQL Server

* [SQL Server sur Machines virtuelles Microsoft Azure avec Azure NetApp Files : exemples de scénarios Azure](/azure/architecture/example-scenario/file-storage/sql-server-azure-netapp-files)
* [Guide de déploiement de SQL Server sur Azure à l’aide d’Azure NetApp Files](https://www.netapp.com/pdf.html?item=/media/27154-tr-4888.pdf)
* [Avantages de l’utilisation de Azure NetApp Files pour le déploiement de SQL Server](solutions-benefits-azure-netapp-files-sql-server.md)
* [Déployer des SQL Server sur SMB avec Azure NetApp Files](https://www.youtube.com/watch?v=x7udfcYbibs)
* [Déployer un cluster de basculement permanent de Microsoft SQL Server sur SMB avec les fichiers Azure NetApp Files](https://www.youtube.com/watch?v=zuNJ5E07e8Q) 
* [Déployer des groupes de disponibilité permanents avec Azure NetApp Files](https://www.youtube.com/watch?v=y3VQmzzeyvc) 

## <a name="sap-on-azure-solutions"></a>Solutions SAP sur Azure

Cette section fournit des références pour les Solutions SAP sur Azure. 

### <a name="generic-sap-and-sap-netweaver"></a>SAP et SAP Netweavereaver génériques 

* [Exécuter SAP NetWeaver dans Windows sur Azure – Centre des architectures Azure](/azure/architecture/reference-architectures/sap/sap-netweaver)
* [SAP applications on Microsoft Azure - Using Azure NetApp Files](https://www.netapp.com/us/media/tr-4746.pdf)
* [Haute disponibilité pour SAP NetWeaver sur les machines virtuelles Azure sur SUSE Linux Enterprise Server avec Azure NetApp Files pour les applications SAP](../virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files.md)
* [Haute disponibilité pour SAP NetWeaver sur des machines virtuelles Azure sur Red Hat Enterprise Linux avec Azure NetApp Files pour les applications SAP](../virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files.md)
* [Haute disponibilité pour SAP NetWeaver sur des machines virtuelles Azure sur Windows avec Azure NetApp Files (SMB) pour les applications SAP](../virtual-machines/workloads/sap/high-availability-guide-windows-netapp-files-smb.md)
* [Guide multi-SID de haute disponibilité pour SAP NetWeaver sur les machines virtuelles Azure sur Red Hat Enterprise Linux for SAP Applications](../virtual-machines/workloads/sap/high-availability-guide-rhel-multi-sid.md)

### <a name="sap-hana"></a>SAP HANA 

* [SAP HANA pour machines virtuelles Linux dans des systèmes de scale-up – Centre des architectures Azure](/azure/architecture/reference-architectures/sap/run-sap-hana-for-linux-virtual-machines)
* [SAP S/4HANA dans Linux sur Azure – Centre des architectures Azure](/azure/architecture/reference-architectures/sap/sap-s4hana)
* [Exécuter SAP BW/4HANA avec des machines virtuelles Linux – Centre des architectures Azure](/azure/architecture/reference-architectures/sap/run-sap-bw4hana-with-linux-virtual-machines)
* [Configurations du stockage des machines virtuelles SAP HANA Azure](../virtual-machines/workloads/sap/hana-vm-operations-storage.md)
* [Volumes NFS v4.1 sur Azure NetApp Files pour SAP HANA](../virtual-machines/workloads/sap/hana-vm-operations-netapp.md)
* [Haute disponibilité du scale-up SAP HANA avec Azure NetApp Files sur Red Hat Enterprise Linux](../virtual-machines/workloads/sap/sap-hana-high-availability-netapp-files-red-hat.md)
* [Scale-out de SAP HANA avec le nœud de secours sur les machines virtuelles Azure avec Azure NetApp Files sur SUSE Linux Enterprise Server](../virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse.md)
* [Scale-out de SAP HANA avec le nœud de secours sur les machines virtuelles Azure avec Azure NetApp Files sur Red Hat Enterprise Linux](../virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel.md)
* [Scale-out de SAP HANA avec HSR et Pacemaker sur RHEL - Machines virtuelles Azure](../virtual-machines/workloads/sap/sap-hana-high-availability-scale-out-hsr-rhel.md)
* [Outil Azure Application Consistent Snapshot (AzAcSnap)](azacsnap-introduction.md)
* [Récupération d’urgence SAP HANA avec Azure NetApp Files](https://docs.netapp.com/us-en/netapp-solutions-sap/pdfs/sidebar/SAP_HANA_Disaster_Recovery_with_Azure_NetApp_Files.pdf)

### <a name="sap-anydb"></a>SAP AnyDB

* [Système SAP sur Oracle Database sur Azure – Centre des architectures Azure](/azure/architecture/example-scenario/apps/sap-on-oracle)
* [Déploiement SGBD de machines virtuelles Oracle Azure pour charge de travail SAP - Machines virtuelles Azure](../virtual-machines/workloads/sap/dbms_guide_oracle.md#oracle-configuration-guidelines-for-sap-installations-in-azure-vms-on-linux)
* [Déployer SAP AnyDB (Oracle 19c) avec Azure NetApp Files](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/deploy-sap-anydb-oracle-19c-with-azure-netapp-files/ba-p/2064043)
* [Déploiement SGBD de machines virtuelles IBM Db2 Azure pour charge de travail SAP avec Azure NetApp Files](../virtual-machines/workloads/sap/dbms_guide_ibm.md#using-azure-netapp-files)

### <a name="sap-iq-nls"></a>SAP IQ-NLS

*   [Déploiement de la solution de haute disponibilité SAP IQ-NLS avec Azure NetApp Files sur SUSE Linux Enterprise Server](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/deploy-sap-iq-nls-ha-solution-using-azure-netapp-files-on-suse/ba-p/1651172#.X2tDfpNzBh4.linkedin)
* [Comment gérer une licence SAP IQ dans un scénario HA](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/how-to-manage-sap-iq-license-in-ha-scenario/ba-p/2052583)

### <a name="sap-tech-community-and-blog-posts"></a>Billets de blog et communauté SAP Tech 

* [Azure NetApp Files : sauvegarde SAP HANA en secondes](https://blog.netapp.com/azure-netapp-files-sap-hana-backup-in-seconds/)
* [Azure NetApp Files : restaurer votre base de données HANA à partir d’une sauvegarde d’instantané](https://blog.netapp.com/azure-netapp-files-backup-sap-hana)
* [Azure NetApp Files : déchargement de sauvegardes SAP HANA avec Cloud Sync](https://blog.netapp.com/azure-netapp-files-sap-hana)
* [Accélérez vos copies système SAP HANA à l’aide d’Azure NetApp Files](https://blog.netapp.com/sap-hana-faster-using-azure-netapp-files/)
* [Cloud Volumes ONTAP et Azure NetApp Files : migration du système SAP HANA facilitée](https://blog.netapp.com/cloud-volumes-ontap-and-azure-netapp-files-sap-hana-system-migration-made-easy/)
* [Décisions architecturales pour optimiser les investissements ANF dans l’architecture de Scale-out HANA N+M – Partie 1](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/architectural-decisions-to-maximize-anf-investment-in-hana-n-m/ba-p/2078737)
* [Décisions architecturales pour optimiser les investissements ANF dans l’architecture de Scale-out HANA N+M – Partie 2](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/architectural-decisions-to-maximize-anf-investment-in-hana-n-m/ba-p/2117130)
* [Décisions architecturales pour optimiser les investissements ANF dans l’architecture de Scale-out HANA N+M – Partie 3](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/architectural-decisions-to-maximize-anf-investment-in-hana-n-m/ba-p/2215948)
* [Consolidation du volume et de la taille d’un paysage SAP avec Azure NetApp Files](https://techcommunity.microsoft.com/t5/sap-on-microsoft/sap-landscape-sizing-and-volume-consolidation-with-anf/m-p/2145572/highlight/true#M14)

## <a name="azure-vmware-solutions"></a>Azure VMware Solution

* [Azure NetApp Files avec Azure VMware Solution – Montages du système d’exploitation invité](../azure-vmware/netapp-files-with-azure-vmware-solution.md)

## <a name="virtual-desktop-infrastructure-solutions"></a>Solutions d’infrastructure de bureau virtuel

Cette section fournit des références pour les solutions d’infrastructure de bureau virtuel.

### <a name="azure-virtual-desktop"></a><a name="windows-virtual-desktop"></a>Azure Virtual Desktop

* [Avantages d’utiliser Azure NetApp Files avec Azure Virtual Desktop](solutions-windows-virtual-desktop.md)
* [Options de stockage des conteneurs de profils FSLogix dans Azure Virtual Desktop](../virtual-desktop/store-fslogix-profile.md#azure-platform-details)
* [Créer un conteneur de profils FSLogix pour un pool d’hôtes à l'aide d’Azure NetApp Files](../virtual-desktop/create-fslogix-profile-container.md)
* [Azure Virtual Desktop à l’échelle de l’entreprise](/azure/architecture/example-scenario/wvd/windows-virtual-desktop)
* [Microsoft FSLogix pour l’entreprise – Meilleures pratiques Azure NetApp Files](/azure/architecture/example-scenario/wvd/windows-virtual-desktop-fslogix#azure-netapp-files-best-practices)
* [Configuration d’Azure NetApp Files pour l’attachement d’application MSIX](https://techcommunity.microsoft.com/t5/windows-virtual-desktop/setting-up-azure-netapp-files-for-msix-app-attach-step-by-step/m-p/1990021)

### <a name="citrix"></a>Citrix   

* [Guide de gestion des profils Citrix avec Azure NetApp Files meilleures pratiques](https://www.netapp.com/pdf.html?item=/media/55973-tr-4901.pdf)


## <a name="hpc-solutions"></a>Solutions HPC

Cette section fournit des références pour les solutions HPC (Calcul haute performance). 

### <a name="generic-hpc"></a>HPC générique

* [Azure NetApp Files : Tirer le meilleur parti de votre stockage cloud](https://cloud.netapp.com/hubfs/Resources/ANF%20PERFORMANCE%20TESTING%20IN%20TEMPLATE.pdf)
* [Exécuter des charges de travail MPI avec Azure Batch et Azure NetApp Files](https://azure.microsoft.com/resources/run-mpi-workloads-with-azure-batch-and-azure-netapp-files/)
* [Cloud Azure Cloud : Environnements HPC CycleCloud sur Azure NetApp Files](/azure/cyclecloud/overview)

### <a name="oil-and-gas"></a>Hydrocarbures

* [Calcul haute performance (HPC) : Hydrocarbures dans Azure](https://techcommunity.microsoft.com/t5/azure-global/high-performance-computing-hpc-oil-and-gas-in-azure/ba-p/824926)
* [Exécuter le logiciel de simulation de réservoir sur Azure](/azure/architecture/example-scenario/infrastructure/reservoir-simulation)

### <a name="electronic-design-automation-eda"></a>Automatisation de la conception électronique (EDA)

* [Avantages de l’utilisation d’Azure NetApp Files pour l’automatisation de la conception électronique](solutions-benefits-azure-netapp-files-electronic-design-automation.md)
* [Azure CycleCloud : Laboratoire EDA HPC avec Azure NetApp Files](https://github.com/Azure/cyclecloud-hands-on-labs/blob/master/EDA/README.md)
* [Azure pour le secteur des semi-conducteurs](https://azurecomcdn.azureedge.net/cvt-f40f39cd9de2d875ab0c198a8d7b186350cf0bca161e80d7896941389685d012/mediahandler/files/resourcefiles/azure-for-the-semiconductor-industry/Azure_for_the_Semiconductor_Industry.pdf)

### <a name="analytics"></a>Analytics

* [Guide de l’architecture SAS sur Azure - Centre des architectures Azure | Azure NetApp Files](/azure/architecture/guide/sas/sas-overview#azure-netapp-files-nfs)
* [Azure NetApp Files : un système de fichiers partagé à utiliser avec la grille SAS sur Microsoft Azure](https://communities.sas.com/t5/Administration-and-Deployment/Azure-NetApp-Files-A-shared-file-system-to-use-with-SAS-Grid-on/m-p/705192)
* [Azure NetApp Files : un système de fichiers partagé à utiliser avec la grille SAS sur MS Azure – MISE À JOUR RHEL8.3/nconnect](https://communities.sas.com/t5/Administration-and-Deployment/Azure-NetApp-Files-A-shared-file-system-to-use-with-SAS-Grid-on/m-p/722261#M21648)
* [Meilleures pratiques d’utilisation de Microsoft Azure avec SAS®](https://communities.sas.com/t5/Administration-and-Deployment/Best-Practices-for-Using-Microsoft-Azure-with-SAS/m-p/676833#M19680)

### <a name="healthcare"></a>Santé

* [Epic EHR sur Microsoft Azure avec Azure NetApp Files](https://www.netapp.com/pdf.html?item=/media/62375-tr-4909.pdf)

## <a name="azure-platform-services-solutions"></a>Déployer des solutions pour les services de plateforme Azure

Cette section fournit des solutions pour les services de plateforme Azure. 

### <a name="azure-kubernetes-services-and-kubernetes"></a>Azure Kubernetes Serviceset Kubernetes

* [Astra : protégez, récupérez et gérez vos charges de travail AKS sur Azure NetApp Files](https://cloud.netapp.com/hubfs/Astra%20Azure%20Documentation.pdf) 
* [Intégrer Azure NetApp Files à Azure Kubernetes Service](../aks/azure-netapp-files.md)
* [Performances exceptionnelles de Kubernetes sur Azure avec Azure NetApp Files](https://cloud.netapp.com/blog/ma-anf-blg-configure-kubernetes-openshift)
* [Azure NetApp Files + Trident = stockage dynamique et persistant pour Kubernetes](https://anfcommunity.com/2021/02/16/azure-netapp-files-trident-dynamic-and-persistent-storage-for-kubernetes/)
* [Trident - Storage Orchestrator pour conteneurs](https://netapp-trident.readthedocs.io/en/stable-v20.04/kubernetes/operations/tasks/backends/anf.html)
* [Plateforme d’e-commerce Magento dans Azure Kubernetes Service (AKS)](/azure/architecture/example-scenario/magento/magento-azure)

### <a name="azure-red-hat-openshift"></a>Azure Red Hat OpenShift   

*   [Utilisation de Trident pour automatiser Azure NetApp Files à partir d’OpenShift](https://techcommunity.microsoft.com/t5/fasttrack-for-azure/using-trident-to-automate-azure-netapp-files-from-openshift/ba-p/2367351)

### <a name="azure-batch"></a>Azure Batch

* [Exécuter des charges de travail MPI avec Azure Batch et Azure NetApp Files](https://azure.microsoft.com/resources/run-mpi-workloads-with-azure-batch-and-azure-netapp-files/)
