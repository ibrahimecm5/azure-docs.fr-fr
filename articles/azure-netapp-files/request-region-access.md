---
title: Demander l’accès à une région pour Azure NetApp Files | Microsoft Docs
description: Décrit comment demander l’accès à une région pour utiliser Azure NetApp Files.
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
ms.topic: how-to
ms.date: 11/15/2021
ms.author: b-juche
ms.openlocfilehash: 9d610a74e63c85a66cd0be42cfdda3139c673f67
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132520633"
---
# <a name="request-region-access-for-azure-netapp-files"></a>Demander l’accès à une région pour Azure NetApp Files

Dans certains cas particuliers, vous devrez peut-être demander explicitement l’accès à une région. Cet article explique comment envoyer une demande. 

## <a name="steps"></a>Étapes

1. Sous **Nouvelle demande de support**, sélectionnez **Suport + dépannage**.   

2. Sous l’onglet **Description du problème**, fournissez les informations requises :
    1. Pour **Type de problème**, sélectionnez **Limites du service et de l’abonnement (quotas)** .
    2. Pour **Abonnement**, sélectionnez votre abonnement. 
    3. Pour **Type de quota**, sélectionnez **Stockage : limites d’Azure NetApp Files**.

    ![Capture d’écran montrant l’onglet Description du problème.](../media/azure-netapp-files/support-problem-descriptions.png)

3. Sous l’onglet **Détails supplémentaires**, cliquez sur **Entrer les détails** dans le champ Détails de la demande.  

    ![Capture d’écran montrant l’onglet Détails et le champ Entrer les détails.](../media/azure-netapp-files/quota-additional-details.png)

4. Pour demander l’accès à une région, fournissez les informations suivantes dans la fenêtre Détails du quota qui s’affiche :   
    1. Dans **Type de quota**, sélectionnez **Accès à une région**.
    2. Dans **Région demandée**, sélectionnez votre région.

    ![Capture d’écran montrant la fenêtre Détails du quota pour la demande d’accès à une région.](../media/azure-netapp-files/quota-details-region-access.png)

5. Cliquez sur **Enregistrer et continuer**. Cliquez sur **Vérifier + créer** pour créer la demande.

## <a name="next-steps"></a>Étapes suivantes  

- [Comprendre la hiérarchie de stockage d’Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)
- [Modèle de coût pour Azure NetApp Files](azure-netapp-files-cost-model.md)
- [Quota de capacité régionale pour Azure NetApp Files](regional-capacity-quota.md)
- [Limites des ressources pour Azure NetApp Files](azure-netapp-files-resource-limits.md)
