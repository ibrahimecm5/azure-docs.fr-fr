---
title: FAQ sur la sauvegarde Azure NetApp Files | Microsoft Docs
description: Réponses aux questions fréquemment posées (FAQ) sur l’utilisation de la fonctionnalité de sauvegarde Azure NetApp Files.
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: conceptual
author: b-juche
ms.author: b-juche
ms.date: 10/11/2021
ms.openlocfilehash: cec399e00215994e55e06430f2170d8c19c20b03
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130270752"
---
# <a name="azure-netapp-files-backup-faqs"></a>FAQ sur la sauvegarde Azure NetApp Files

Cet article répond aux questions fréquemment posées (FAQ) sur l’utilisation de la fonctionnalité de [sauvegarde Azure NetApp Files](backup-introduction.md). 

## <a name="when-do-my-backups-occur"></a>Quand mes sauvegardes se produisent-elles ?   

Les sauvegardes Azure NetApp Files démarrent dans un laps de temps aléatoire après la saisie de la fréquence d'une stratégie de sauvegarde. Par exemple, les sauvegardes hebdomadaires sont initiées dimanche dans un intervalle affecté de façon aléatoire après 00 h 00 (minuit). Cette heure ne peut pas être modifiée par les utilisateurs pour l'instant. La sauvegarde de base est lancée dès que vous attribuez la stratégie de sauvegarde au volume.

## <a name="what-happens-if-a-backup-operation-encounters-a-problem"></a>Que se passe-t-il si une opération de sauvegarde rencontre un problème ?

Si un problème se produit pendant une opération de sauvegarde, la sauvegarde Azure NetApp Files retente automatiquement l'opération, sans intervention de l'utilisateur. Si les nouvelles tentatives continuent d'échouer, la sauvegarde Azure NetApp Files signale l'échec de l'opération.

## <a name="can-i-change-the-location-or-storage-tier-of-my-backup-vault"></a>Puis-je modifier l'emplacement ou le niveau de stockage de mon coffre de sauvegarde ?

Non, Azure NetApp Files gère automatiquement l’emplacement des données de sauvegarde dans le stockage Azure. Cet emplacement ou le niveau de stockage Azure ne peuvent pas être modifiés par l’utilisateur.

## <a name="what-types-of-security-are-provided-for-the-backup-data"></a>Quels sont les types de sécurité fournis pour les données de sauvegarde ?

Azure NetApp Files utilise le chiffrement AES-256 bits pendant l'encodage des données de sauvegarde reçues. En outre, les données chiffrées sont transmises en toute sécurité au stockage Azure à l'aide de connexions HTTPS TLSv 1.2. La sauvegarde Azure NetApp Files dépend de la fonctionnalité intégrée de chiffrement au repos du compte de stockage Azure pour le stockage des données de sauvegarde.

## <a name="what-happens-to-the-backups-when-i-delete-a-volume-or-my-netapp-account"></a>Qu'advient-il des sauvegardes lorsque je supprime un volume ou mon compte NetApp ? 

 Lorsque vous supprimez un volume Azure NetApp Files, les sauvegardes sont conservées. Si vous ne souhaitez pas que les sauvegardes soient conservées, désactivez les sauvegardes avant de supprimer le volume. Lorsque vous supprimez un compte NetApp, les sauvegardes sont toujours conservées et affichées sous d'autres comptes NetApp du même abonnement, afin qu'elles soient toujours disponibles pour la restauration. Si vous supprimez tous les comptes NetApp dans le cadre d'un abonnement, vous devez veiller à désactiver les sauvegardes avant de supprimer tous les volumes sous tous les comptes NetApp.

## <a name="whats-the-systems-maximum-backup-retries-for-a-volume"></a>Quel est le nombre maximum de nouvelles tentatives de sauvegarde du système pour un volume ?  

Le système effectue 10 nouvelles tentatives lors du traitement d'une tâche de sauvegarde planifiée. En cas d'échec du travail, le système ne parvient pas à effectuer la sauvegarde. En cas de sauvegarde planifiée (en fonction de la stratégie configurée), le système tente de sauvegarder les données toutes les heures. Si de nouveaux instantanés non transférés (ou ayant échoué au cours de la dernière tentative) sont disponibles, ces instantanés sont pris en compte pour le transfert. 

## <a name="next-steps"></a>Étapes suivantes  

- [Création d’une demande de support Azure](../azure-portal/supportability/how-to-create-azure-support-request.md)
- [FAQ sur la mise en réseau](faq-networking.md)
- [Forum aux questions sur la sécurité](faq-security.md)
- [Questions fréquentes (FAQ) relatives aux performances](faq-performance.md)
- [FAQ relatifs au NFS](faq-nfs.md)
- [Questions fréquentes sur SMB](faq-smb.md)
- [Questions fréquentes (FAQ) sur la gestion de la capacité](faq-capacity-management.md)
- [Questions fréquentes sur la migration et la protection des données](faq-data-migration-protection.md)
- [FAQ relatives à la résilience des applications](faq-application-resilience.md)
- [Forum aux questions sur l’intégration](faq-integration.md)
