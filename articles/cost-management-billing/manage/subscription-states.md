---
title: États des abonnements Azure
description: Cet article décrit les différents états et statuts d’un abonnement Azure.
keywords: États des abonnements Azure
author: bandersmsft
ms.reviewer: andalmia
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 09/15/2021
ms.author: banders
ms.openlocfilehash: 9902bfc0e1214ee59ce2c0c6fc01b9620529d9ca
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131438699"
---
# <a name="azure-subscription-states"></a>États des abonnements Azure

Cet article décrit les différents états d’un abonnement Azure. Ces états sont affichés sous la forme d’**État** dans les zones d’abonnement du portail Azure.

| État de l’abonnement | Description |
|-------------| ----------------|
| **Actif**/**Activé** | Votre abonnement Azure est actif. Vous pouvez utiliser l’abonnement pour déployer de nouvelles ressources et gérer celles qui existent déjà.<br><br>Toutes les opérations (PUT, PATCH, DELETE, POST, GET) sont disponibles pour les fournisseurs de ressources [inscrits pour votre abonnement](../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal). |
| **Supprimé** | Votre abonnement Azure a été supprimé avec toutes ses ressources et données sous-jacentes.<br><br>Aucune opération n’est disponible. |
| **Désactivé** | Votre abonnement Azure est désactivé et ne peut plus être utilisé pour créer ou gérer des ressources Azure. Si tel est l’état de votre abonnement, vos machines virtuelles sont désallouées, les adresses IP temporaires sont libérées, le stockage devient accessible en lecture seule et les autres services sont désactivés. Un abonnement peut être désactivé pour les raisons suivantes : Votre crédit a peut-être expiré. Vous avez peut-être atteint votre limite de dépense. Vous avez une facture en retard. La limite de votre carte de crédit a été dépassée. Ou bien, il a été explicitement désactivé ou annulé. Suivant le type d’abonnement, un abonnement peut rester désactivé entre 1 et 90 jours. Après quoi, il est définitivement supprimé. Pour plus d’informations, consultez [Réactiver un abonnement Azure désactivée](subscription-disabled.md).<br><br>Les opérations de création ou de mise à jour des ressources (PUT, PATCH) sont désactivées. Les opérations qui entreprennent une action (POST) sont également désactivées. Vous pouvez récupérer ou supprimer des ressources (GET, DELETE). Vos ressources sont toujours disponibles. |
| **Expired** | Votre abonnement Azure a expiré car il a été annulé. Vous pouvez réactiver un abonnement expiré. Pour plus d’informations, consultez [Réactiver un abonnement Azure désactivée](subscription-disabled.md).<br><br>Les opérations de création ou de mise à jour des ressources (PUT, PATCH) sont désactivées. Les opérations qui entreprennent une action (POST) sont également désactivées. Vous pouvez récupérer ou supprimer des ressources (GET, DELETE).|
| **En retard** | Un paiement est en attente pour votre abonnement Azure. Votre abonnement est toujours actif, mais le non paiement des frais peut entraîner sa désactivation. Pour plus d’informations, consultez [Résoudre un solde impayé pour votre abonnement Azure](resolve-past-due-balance.md).<br><br>Toutes les opérations sont disponibles. |
| **Averti** | Votre abonnement Azure fait l’objet d’un avertissement. Il sera désactivé sous peu si vous ne résolvez pas le motif de l’avertissement. Un abonnement peut faire l’objet d’un avertissement en cas de retard de paiement, d’annulation par l’utilisateur, d’expiration, etc.<br><br>Vous pouvez récupérer ou supprimer des ressources (GET/DELETE), mais vous ne pouvez pas en créer (PUT/PATCH/POST) |
