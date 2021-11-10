---
title: Matrice de protection MABS V3 UR1 (serveur de sauvegarde Azure)
description: Cet article fournit une matrice de prise en charge répertoriant toutes les charges de travail, les types de données et les installations que protège le serveur de sauvegarde Azure.
ms.date: 07/27/2021
ms.topic: conceptual
ms.openlocfilehash: c3ad2fe2b74179f3797f8986e6a16c9c36d1b2a2
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131446787"
---
# <a name="mabs-azure-backup-server-v3-ur1-and-later-protection-matrix"></a>Matrice de protection MABS V3 UR1 (et versions ultérieures) (serveur de sauvegarde Azure)

Cet article répertorie les différents serveurs et charges de travail que vous pouvez protéger avec le serveur de sauvegarde Azure. La matrice suivante répertorie ce qui peut être protégé avec le serveur de sauvegarde Azure.

Utilisez la matrice suivante pour MABS v3 UR1 (et versions ultérieures) :

* Charges de travail : type de technologie pour la charge de travail.

* Version : version de MABS prise en charge pour les charges de travail.

* Installation de MABS : ordinateur/emplacement où vous souhaitez installer MABS.

* Protection et récupération : listez les informations détaillées sur les charges de travail, telles que le conteneur de stockage pris en charge ou le déploiement pris en charge.

>[!NOTE]
>La prise en charge de l’agent de protection 32 bits est dépréciée avec MABS v3 UR1 (et versions ultérieures). Consultez [Dépréciation de l’agent de protection 32 bits](backup-mabs-whats-new-mabs.md#32-bit-protection-agent-deprecation).

## <a name="protection-support-matrix"></a>Matrice de prise en charge de la protection

Les sections suivantes détaillent la matrice de prise en charge de protection pour MABS :

* [Sauvegarde des applications](#applications-backup)
* [Sauvegarde des machines virtuelles](#vm-backup)
* [Linux](#linux)

## <a name="applications-backup"></a>Sauvegarde des applications

| **Charge de travail**               | **Version**                                                  | **Installation du serveur de sauvegarde Azure**                       | **Serveur de sauvegarde Azure pris en charge** | **Protection et récupération**                                  |
| -------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ | --------------------------------- | ------------------------------------------------------------ |
| Ordinateurs clients (64 bits) | Windows 10                                                  | Serveur physique  <br><br>    Machine virtuelle Hyper-V   <br><br>   Machine virtuelle VMware | V3 UR1 et V3 UR2                            | Volume, partage, dossier, fichiers, volumes dédupliqués   <br><br>   Les volumes protégés doivent être au format NTFS. Les formats FAT et FAT32 ne sont pas pris en charge.  <br><br>    Les volumes doivent être d’au moins 1 Go. Le serveur de sauvegarde Azure utilise le service VSS pour prendre un l’instantané des données, et cet instantané fonctionne seulement si le volume est d’au moins 1 Go. |
| Serveurs (64 bits)          | Windows Server 2022, 2019, 2016, 2012 R2, 2012                    | Machine virtuelle Azure (quand la charge de travail s’exécute en tant que machine virtuelle Azure)  <br><br>    Serveur physique  <br><br>    Machine virtuelle Hyper-V <br><br>     Machine virtuelle VMware  <br><br>    Azure Stack | V3 UR1 et V3 UR2                            | Volume, partage, dossier, fichier <br><br>    Volumes dédupliqués (NTFS uniquement) <br><br>Quand vous protégez un volume dédupliqué NTFS WS 2016 avec MABS v3 exécuté sur Windows Server 2019, les récupérations peuvent être affectées. Nous avons un correctif pour effectuer les récupérations d’une manière non dédupliquée que vous retrouverez dans les versions ultérieures de MABS. Contactez le support de MABS si vous avez besoin de ce correctif sur MABS v3 UR1.<br><br> Quand vous protégez un volume dédupliqué NTFS WS 2019 avec MABS v3 exécuté sur Windows Server 2016, les sauvegardes et les restaurations ne seront pas dédupliquées. Cela signifie que les sauvegardes consommeront plus d’espace sur le serveur MABS que le volume dédupliqué NTFS d’origine.   <br><br>   État système et sauvegarde complète (non pris en charge quand la charge de travail s’exécute en tant que machine virtuelle Azure) |
| Serveurs (64 bits)          | Windows Server 2008 R2 SP1, Windows Server 2008 SP2 (vous devez installer [Windows Management Framework](https://www.microsoft.com/download/details.aspx?id=54616)) | Serveur physique  <br><br>    Machine virtuelle Hyper-V  <br><br>      Machine virtuelle VMware  <br><br>   Azure Stack | V3 UR1 et V3 UR2                           | Volume, partage, dossier, fichier, état système/sauvegarde complète        |
| SQL Server                | SQL Server 2019, 2017, 2016 et [Service Packs pris en charge](https://support.microsoft.com/lifecycle/search?alpha=SQL%20Server%202016), 2014 et [Service Packs](https://support.microsoft.com/lifecycle/search?alpha=SQL%20Server%202014) pris en charge | Serveur physique  <br><br>     Machine virtuelle Hyper-V   <br><br>     Machine virtuelle VMware  <br><br>   Machine virtuelle Azure (quand la charge de travail s’exécute en tant que machine virtuelle Azure)  <br><br>     Azure Stack | V3 UR1 et V3 UR2                            | Tous les scénarios de déploiement : base de données       <br><br>    MABS v3 UR2 (et versions ultérieures) prend en charge la sauvegarde de la base de données SQL, stockée sur le volume partagé de cluster.     <br><br>  MABS v3 UR1 prend en charge la sauvegarde des bases de données SQL sur les volumes ReFS     <br><br>     MABS ne prend pas en charge les bases de données SQL Server hébergées sur des serveurs de fichiers avec montée en puissance parallèle (SOFS) de Windows Server 2012. <br><br>   MABS ne peut pas protéger le groupe de disponibilité distribué (DAG) ou le groupe de disponibilité (AG) de SQL Server, où le nom de rôle sur le cluster de basculement est différent du groupe de disponibilité nommé sur SQL.       |
| Exchange                   | Exchange 2019, 2016                                         | Serveur physique   <br><br>   Machine virtuelle Hyper-V  <br><br>      Machine virtuelle VMware  <br><br>   Azure Stack  <br><br>    Machine virtuelle Azure (quand la charge de travail s’exécute en tant que machine virtuelle Azure) | V3 UR1 et V3 UR2                            | Protéger (tous les scénarios de déploiement) : serveur Exchange autonome, base de données dans un groupe de disponibilité de base de données  <br><br>    Récupérer (tous les scénarios de déploiement) : boîte aux lettres, bases de données de boîtes aux lettres dans un groupe de disponibilité de base de données    <br><br>  La sauvegarde d’Exchange sur ReFS est prise en charge avec MABS v3 UR1 |
| SharePoint                 | SharePoint 2019, 2016 avec les derniers Service Packs                       | Serveur physique  <br><br>    Machine virtuelle Hyper-V <br><br>    Machine virtuelle VMware  <br><br>   Machine virtuelle Azure (quand la charge de travail s’exécute en tant que machine virtuelle Azure)   <br><br>   Azure Stack | V3 UR1 et V3 UR2                            | Protéger (tous les scénarios de déploiement) : batterie de serveurs, contenu de serveur web front-end  <br><br>    Récupérer (tous les scénarios de déploiement) : batterie de serveurs, base de données, application web, élément de fichier ou de liste, recherche SharePoint, serveur web front-end  <br><br>    La protection d’une batterie de serveurs SharePoint qui utilise la fonctionnalité AlwaysOn de SQL Server 2012 pour les bases de données de contenu n’est pas prise en charge. |

## <a name="vm-backup"></a>Sauvegarde des machines virtuelles

| **Charge de travail**                                                 | **Version**                                             | **Installation du serveur de sauvegarde Azure**                      | **Serveur de sauvegarde Azure pris en charge** | **Protection et récupération**                                 |
| ------------------------------------------------------------ | ------------------------------------------------------- | ------------------------------------------------------------ | ---------------------------------- | ------------------------------------------------------------ |
| Hôte Hyper-V : agent de protection MABS sur un serveur hôte, un cluster ou une machine virtuelle Hyper-V | Windows Server 2022, 2019, 2016, 2012 R2, 2012               | Serveur physique  <br><br>    Machine virtuelle Hyper-V <br><br>    Machine virtuelle VMware | V3 UR1 et V3 UR2                                 | Protéger : machines virtuelles, volumes partagés de cluster  <br><br>    Récupérer : machine virtuelle, récupération au niveau élément de fichiers et de dossiers disponible uniquement pour Windows, volumes, disques durs virtuels |
| Azure Stack HCI  |       V1 et 20H2      |    Serveur physique        <br><br>     Machine virtuelle Azure Stack HCI/Hyper-V     <br><br>    Machine virtuelle VMware     |    V3 UR2 et versions ultérieures   | Protéger : machines virtuelles, volumes partagés de cluster      <br><br>     Récupérer : machine virtuelle, récupération au niveau élément de fichiers et de dossiers disponible uniquement pour Windows, volumes, disques durs virtuels |
| Machines virtuelles VMware                                                  | Serveur VMware 5.5, 6.0 ou 6.5, 6.7 (version sous licence) | Machine virtuelle Hyper-V  <br><br>   Machine virtuelle VMware         | V3 UR1                             | Protéger :  Machines virtuelles VMware sur volumes partagés de cluster (CSV), NFS et stockage SAN   <br><br>     Récupérer : machine virtuelle, récupération au niveau élément de fichiers et de dossiers disponible uniquement pour Windows, volumes, disques durs virtuels <br><br>    Les applications virtuelles VMware ne sont pas prises en charge. |
| Machines virtuelles VMware      |   Serveur VMware 7.0, 6.7, 6.5 ou 6.0 (version sous licence)     |    Machine virtuelle Hyper-V     <br><br>    Machine virtuelle VMware    |   V3 UR2 et versions ultérieures    |   Protéger : machines virtuelles VMware sur un stockage CSV (à volumes partagés de cluster), NFS ou SAN     <br><br>    Récupérer : machine virtuelle, récupération au niveau élément de fichiers et de dossiers disponible uniquement pour Windows, volumes, disques durs virtuels     <br><br>    Les applications virtuelles VMware ne sont pas prises en charge.  |

>[!NOTE]
> MABS ne prend pas en charge la sauvegarde des machines virtuelles dotées de disques pass-through ou de celles qui utilisent un disque dur virtuel distant. Dans ces scénarios, nous vous recommandons d’utiliser une sauvegarde au niveau invité à l’aide de MABS et d’installer un agent sur la machine virtuelle pour sauvegarder les données.

## <a name="linux"></a>Linux

| **Charge de travail** | **Version**                               | **Installation du serveur de sauvegarde Azure**                      | **Serveur de sauvegarde Azure pris en charge** | **Protection et récupération**                                 |
| ------------ | ----------------------------------------- | ------------------------------------------------------------ | ---------------------------------- | ------------------------------------------------------------ |
| Linux        | Linux s’exécutant en tant qu’invité [Hyper-V](back-up-hyper-v-virtual-machines-mabs.md) ou [VMware](backup-azure-backup-server-vmware.md) | Serveur physique, machine virtuelle Hyper-V locale, machine virtuelle Windows dans VMware | V3 UR1 et V3 UR2                             | Hyper-V doit s’exécuter sur Windows Server 2012 R2, Windows Server 2016 ou Windows Server 2019. Protéger :  toute la machine virtuelle   <br><br>   Récupérer : toute la machine virtuelle   <br><br>    Seuls les instantanés cohérents au niveau des fichiers sont pris en charge.    <br><br>   Pour obtenir la liste complète des versions et distributions Linux prises en charge, consultez l’article [Linux sur les distributions approuvées par Azure](../virtual-machines/linux/endorsed-distros.md). |

## <a name="azure-expressroute-support"></a>Support Azure ExpressRoute

Vous pouvez sauvegarder vos données sur Azure ExpressRoute avec le Peering publique (disponible pour les anciens circuits) et le Peering Microsoft. La sauvegarde sur un peering privé n’est pas prise en charge.

Avec le Peering public : Garantissez l’accès aux domaines/adresses suivants :

* URLs
  * `www.msftncsi.com`
  * `*.Microsoft.com`
  * `*.WindowsAzure.com`
  * `*.microsoftonline.com`
  * `*.windows.net`
  * `www.msftconnecttest.com`
* Adresses IP
  * 20.190.128.0/18
  * 40.126.0.0/18

Avec le peering Microsoft, sélectionnez les services/régions et les valeurs de communauté pertinentes suivants :

* Azure Active Directory (12076:5060)
* Région Microsoft Azure (en fonction de l’emplacement de votre coffre Recovery Services)
* Stockage Azure (en fonction de l’emplacement de votre coffre Recovery Services)

Pour plus d’informations, consultez [Exigences du routage ExpressRoute](../expressroute/expressroute-routing.md).

>[!NOTE]
>Le peering public Azure est déconseillé pour les nouveaux circuits.

## <a name="cluster-support"></a>Prise en charge des clusters

Le serveur de sauvegarde Azure peut protéger les données dans les applications en cluster suivantes :

* Serveurs de fichiers

* SQL Server

* Hyper-V : si vous protégez un cluster Hyper-V en utilisant l’agent de protection MABS avec montée en charge, vous ne pouvez pas ajouter de protection secondaire pour les charges de travail Hyper-V protégées.

* Exchange Server : le serveur de sauvegarde Azure peut protéger des clusters de disques non partagés pour les versions d’Exchange Server prises en charge (réplication continue en cluster) et peut également protéger Exchange Server configuré pour la réplication continue locale.

* SQL Server : le serveur de sauvegarde Azure ne prend en charge la sauvegarde des bases de données SQL Server hébergées sur des volumes partagés de cluster.

>[!NOTE]
>MABS prend uniquement en charge la protection des machines virtuelles Hyper-V sur les volumes partagés de cluster (CSV). La protection d’autres charges de travail hébergées sur des volumes partagés de cluster n’est pas prise en charge.

Le serveur de sauvegarde Azure peut protéger les charges de travail de cluster qui se trouvent dans le même domaine que le serveur MABS, et dans un domaine enfant ou approuvé. Si vous voulez protéger des sources de données dans des domaines ou des groupes de travail non approuvés, utilisez NTLM ou l’authentification par certificat pour un serveur unique, ou l’authentification par certificat uniquement pour un cluster.

## <a name="data-protection-issues"></a>Problèmes de protection des données

* MABS ne peut pas sauvegarder les machines virtuelles qui utilisent des lecteurs partagés (potentiellement attachés à d’autres machines virtuelles), car l’enregistreur VSS d’Hyper-V ne peut pas sauvegarder les volumes sauvegardés par des disques durs virtuels partagés.

* quand vous protégez un dossier partagé, son chemin d’accès comprend le chemin d’accès logique au volume. Si vous déplacez le dossier partagé, la protection échouera. si vous devez déplacer un dossier partagé protégé, supprimez-le du groupe de protection, puis ajoutez-le de nouveau après l’avoir déplacé.  En outre, la protection des données échouera si la longueur du chemin d’accès à une source de données protégée, présente sur un volume utilisant le système EFS (Encrypting File System), dépasse 5120 caractères.

* vous ne pouvez pas modifier le domaine d’un ordinateur protégé et poursuivre la protection sans interruption de service. De plus, vous ne pouvez pas modifier le domaine d’un ordinateur protégé et associer les réplicas et les points de récupération existants à l’ordinateur quand celui-ci est protégé pour la seconde fois. Si vous devez modifier le domaine d'un ordinateur protégé, supprimez d'abord la protection des sources de données sur l'ordinateur. Ensuite, protégez la source de données sur l'ordinateur une fois que le nouveau domaine a été attribué.

* vous ne pouvez pas modifier le nom d’un ordinateur protégé et poursuivre la protection sans interruption de service. De plus, vous ne pouvez pas modifier le nom d’un ordinateur protégé et associer les réplicas et les points de récupération existants à l’ordinateur quand celui-ci est protégé pour la seconde fois. Si vous devez modifier le nom d'un ordinateur protégé, supprimez d'abord la protection des sources de données sur l'ordinateur. Ensuite, protégez la source de données sur l'ordinateur une fois que le nouveau nom a été attribué.

* MABS identifie automatiquement le fuseau horaire d’un ordinateur protégé pendant l’installation de l’agent de protection. Si un ordinateur protégé est déplacé vers un autre fuseau horaire après la configuration de la protection, assurez-vous de modifier l'heure de l'ordinateur dans le Panneau de configuration. Ensuite, mettez à jour le fuseau horaire dans la base de données MABS.

* MABS peut protéger les charges de travail dans le même domaine que le serveur MABS ou dans des domaines enfants ou approuvés. Vous pouvez aussi protéger les charges de travail suivantes dans des groupes de travail et des domaines non approuvés à l’aide de l’authentification par certificat ou NTLM :

  * SQL Server
  * Serveur de fichiers
  * Hyper-V

  Ces charges de travail peuvent s'exécuter sur un seul serveur ou dans une configuration de cluster. Pour protéger une charge de travail qui n’est pas dans un domaine approuvé, consultez [Préparer des ordinateurs inclus dans des domaines non approuvés et des groupes de travail](/system-center/dpm/back-up-machines-in-workgroups-and-untrusted-domains?view=sc-dpm-2019&preserve-view=true#supported-scenarios) pour obtenir des détails précis sur ce qui est pris en charge et sur l’authentification requise.

## <a name="unsupported-data-types"></a>Types de données non pris en charge

MABS ne prend pas en charge la protection des types de données suivants :

* Liens directs

* Points d'analyse, y compris les liens DFS et les points de jonction

* Métadonnées de points de montage : un groupe de protection peut contenir des données avec des points de montage. Dans ce cas, DPM protège le volume monté qui est la cible du point de montage, mais il ne protège pas les métadonnées du point de montage. Quand vous récupérez des données contenant des points de montage, vous devez recréer manuellement votre hiérarchie de point de montage.

* Données de volumes montés dans des volumes montés

* Corbeille

* Fichiers d'échange

* Dossier System Volume Information Pour protéger les informations système d’un ordinateur, vous devez sélectionner l’état système de l’ordinateur comme membre du groupe de protection.

* Volumes non NTFS

* Fichiers contenant des liens directs ou des liens symboliques à partir de Windows Vista.

* Données sur des partages de fichiers hébergeant des disques de profil utilisateur

* Fichiers avec l'une des combinaisons d'attributs suivantes :

  * Chiffrement et analyse

  * Chiffrement et SIS (Single Instance Storage)

  * Chiffrement et respect de la casse

  * Chiffrement et fragmentation

  * Respect de la casse et SIS

  * Compression et SIS

## <a name="next-steps"></a>Étapes suivantes

* [Tableau de prise en charge pour la sauvegarde avec un serveur de sauvegarde Microsoft Azure ou System Center DPM](backup-support-matrix-mabs-dpm.md)