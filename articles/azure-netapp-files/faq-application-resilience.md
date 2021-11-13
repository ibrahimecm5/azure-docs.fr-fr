---
title: FAQ sur la résilience des applications pour Azure NetApp Files | Microsoft Docs
description: Contient les réponses à certaines questions fréquemment posées sur la résilience des applications Azure NetApp Files.
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: conceptual
author: b-juche
ms.author: b-juche
ms.date: 10/11/2021
ms.openlocfilehash: 3a0b243203c0edb01bc5b647a15093ee52b78e7c
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130270913"
---
# <a name="application-resilience-faqs-for-azure-netapp-files"></a>FAQ sur la résilience des applications pour Azure NetApp Files

Cet article répond aux questions fréquemment posées sur la résilience des applications Azure NetApp Files.

## <a name="what-do-you-recommend-for-handling-potential-application-disruptions-due-to-storage-service-maintenance-events"></a>Que recommandez-vous pour gérer les interruptions potentielles de l’application dues à des événements de maintenance de service de stockage ?

Azure NetApp Files peut faire l’objet d’une maintenance planifiée occasionnelle (par exemple, mises à jour de la plateforme, mises à niveau de services ou de logiciels). Du point de vue du protocole de fichier (NFS/SMB), les opérations de maintenance ne sont pas perturbatrices, tant que l’application peut gérer les pauses d’E/S qui peuvent se produire brièvement au cours de ces événements. Les pauses d’e/s sont généralement courtes, allant de quelques secondes à 30 secondes. Le protocole NFS est particulièrement robuste, et les opérations sur les fichiers client-serveur se poursuivent normalement. Certaines applications peuvent nécessiter un réglage pour gérer les pauses d’E/S allant jusqu’à 30-45 secondes. Par conséquent, veillez à prendre connaissance des paramètres de résilience de l’application pour gérer les événements de maintenance de service de stockage. Pour les applications avec interaction humaine tirant parti du protocole SMB, les paramètres standard du protocole sont généralement suffisants. 

## <a name="do-i-need-to-take-special-precautions-for-smb-based-applications"></a>Dois-je prendre des précautions particulières pour les applications basées sur le protocole SMB ?

Oui, certaines applications basées sur le protocole SMB nécessitent le basculement transparent SMB. Le basculement transparent SMB permet d’effectuer des opérations de maintenance sur le service Azure NetApp Files sans interrompre la connectivité aux applications du serveur qui stockent et accèdent aux données sur les volumes SMB. Pour prendre en charge le basculement transparent SMB pour des applications spécifiques, Azure NetApp Files prend désormais en charge l’[option de partages SMB à disponibilité continue](azure-netapp-files-create-volumes-smb.md#continuous-availability). 

## <a name="i-am-running-ibm-mq-on-azure-netapp-files-what-precautions-can-i-take-to-avoid-disruptions-due-to-storage-service-maintenance-events-despite-using-the-nfs-protocol"></a>J’exécute IBM MQ sur Azure NetApp Files. Quelles précautions puis-je prendre pour éviter les interruptions dues à des événements de maintenance de service de stockage malgré l’utilisation du protocole NFS ?

Si vous exécutez l’[application IBM MQ dans une configuration de fichiers partagés](https://www.ibm.com/docs/en/ibm-mq/9.2?topic=multiplatforms-sharing-mq-files), où les données et les journaux IBM MQ sont stockés sur un volume Azure NetApp Files, les considérations suivantes sont recommandées pour améliorer la résilience pendant les événements de maintenance du service de stockage :

* Vous devez utiliser le protocole NFS v4.1 uniquement.
* Pour une haute disponibilité, vous devez utiliser une [configuration multi-instance IBM MQ utilisant des volumes NFS v4.1 partagés](https://www.ibm.com/docs/en/ibm-mq/9.2?topic=manager-create-multi-instance-queue-linux). 
* Vous devez vérifier la fonctionnalité de la [configuration multi-instance IBM utilisant des volumes NFS v4.1 partagés](https://www.ibm.com/docs/en/ibm-mq/9.2?topic=multiplatforms-verifying-shared-file-system-behavior). 
* Vous devez implémenter une architecture IBM MQ en scale-out au lieu d’utiliser une configuration multi-instance IBM MQ de grande taille. En répartissant la charge de traitement des messages sur plusieurs paires multi-instances IBM MQ, le risque d’interruption de service peut être réduit, car chaque paire multi-instance MQ traitera moins de messages.

> [!NOTE] 
> Le nombre de messages que chaque paire multi-instance MQ doit traiter dépend fortement de votre environnement spécifique. Vous devez décider du nombre de paires multi-instances MQ nécessaires ou des règles de scale-up ou de scale-down.

L’architecture scale-out se compose de plusieurs paires multi-instances IBM MQ déployées derrière un équilibreur de charge Azure. Les applications configurées pour communiquer avec IBM MQ seraient ensuite configurées pour communiquer avec les instances d’IBM MQ via Azure Load Balancer. Pour une assistance relative à IBM MQ sur des volumes NFS partagés, vous devez vous adresser au support d’IBM.

## <a name="i-am-running-apache-activemq-with-leveldb-or-kahadb-on-azure-netapp-files-what-precautions-can-i-take-to-avoid-disruptions-due-to-storage-service-maintenance-events-despite-using-the-nfs-protocol"></a>J’exécute Apache ActiveMQ avec LevelDB ou KahaDB sur Azure NetApp Files. Quelles précautions puis-je prendre pour éviter les interruptions dues à des événements de maintenance de service de stockage malgré l’utilisation du protocole *NFS* ?

>[!NOTE]
> Cette section contient des références aux termes *esclave* et *maître*, termes que Microsoft n’utilise plus. Lorsque le terme sera supprimé du logiciel, nous le supprimerons de cet article.

Si vous utilisez Apache ActiveMQ, nous vous recommandons de déployer la [haute disponibilité d’ActiveMQ avec des casiers de stockage enfichables](https://www.openlogic.com/blog/pluggable-storage-lockers-activemq). 

Les modèles de haute disponibilité ActiveMQ garantissent qu’une instance de répartiteur est toujours en ligne et en mesure de traiter le trafic des messages. Les deux modèles de haute disponibilité ActiveMQ les plus courants impliquent le partage d’un système de fichiers sur un réseau. L’objectif est de fournir LevelDB ou KahaDB aux instances actives et passives du répartiteur. Ces modèles de haute disponibilité nécessitent qu’un verrou au niveau du système d’exploitation soit obtenu et maintenu sur un fichier dans les répertoires LevelDB ou KahaDB, appelé « lock ». Ce modèle de haute disponibilité ActiveMQ présente quelques problèmes. Ils peuvent aboutir à une situation « sans maître », où l’« esclave » ne sait pas qu’il peut verrouiller le fichier.  Ils peuvent également aboutir à une configuration « maître-maître » qui entraîne l’altération de l’index ou du journal, et finalement la perte de messages. La plupart de ces problèmes proviennent de facteurs hors du contrôle d’ActiveMQ. Par exemple, un client NFS mal optimisé peut causer l’obsolescence des données de verrouillage sous la charge, ce qui entraîne un temps d’arrêt « sans maître » pendant le basculement. 

Étant donné que la plupart des problèmes liés à cette solution de haute disponibilité proviennent du verrouillage inexact de fichiers au niveau du système d’exploitation, la communauté ActiveMQ [a introduit le concept d’un casier de stockage enfichable](https://www.openlogic.com/blog/pluggable-storage-lockers-activemq) dans la version 5.7 du répartiteur. Cette approche permet à un utilisateur de tirer parti d’un autre moyen de verrou partagé, à l’aide d’un verrou de base de données JDBC au niveau de la ligne par opposition à un verrou de système de fichiers au niveau du système d’exploitation. Pour obtenir de l’aide ou des conseils sur les architectures et les déploiements de la haute disponibilité ActiveMQ, [contactez OpenLogic by Perforce](https://www.openlogic.com/contact-us).

## <a name="i-am-running-apache-activemq-with-leveldb-or-kahadb-on-azure-netapp-files-what-precautions-can-i-take-to-avoid-disruptions-due-to-storage-service-maintenance-events-despites-using-the-smb-protocol"></a>J’exécute Apache ActiveMQ avec LevelDB ou KahaDB sur Azure NetApp Files. Quelles précautions puis-je prendre pour éviter les interruptions dues à des événements de maintenance de service de stockage malgré l’utilisation du protocole *SMB* ?

La recommandation générale des professionnels du secteur consiste à [ne pas exécuter votre stockage partagé KahaDB sur CIFS/SMB](https://www.openlogic.com/blog/activemq-community-deprecates-leveldb-what-you-need-know). Si vous avez des difficultés à maintenir un état de verrouillage précis, consultez le casier de stockage enfichable de JDBC, qui peut fournir un mécanisme de verrouillage plus fiable. Pour obtenir de l’aide ou des conseils sur les architectures et les déploiements de la haute disponibilité ActiveMQ, [contactez OpenLogic by Perforce](https://www.openlogic.com/contact-us).

## <a name="next-steps"></a>Étapes suivantes  

- [Création d’une demande de support Azure](../azure-portal/supportability/how-to-create-azure-support-request.md)
- [FAQ sur la mise en réseau](faq-networking.md)
- [Forum aux questions sur la sécurité](faq-security.md)
- [Questions fréquentes (FAQ) relatives aux performances](faq-performance.md)
- [FAQ relatifs au NFS](faq-nfs.md)
- [Questions fréquentes sur SMB](faq-smb.md)
- [Questions fréquentes (FAQ) sur la gestion de la capacité](faq-capacity-management.md)
- [Questions fréquentes sur la migration et la protection des données](faq-data-migration-protection.md)
- [FAQ sur la sauvegarde Azure NetApp Files](faq-backup.md)
- [Forum aux questions sur l’intégration](faq-integration.md)

