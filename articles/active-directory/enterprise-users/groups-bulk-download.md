---
title: Télécharger une liste de groupes dans le portail Azure Active Directory | Microsoft Docs
description: Téléchargez des propriétés de groupe en bloc dans le centre d’administration Azure dans Azure Active Directory.
services: active-directory
author: curtand
ms.author: curtand
manager: KarenH444
ms.date: 10/26/2021
ms.topic: how-to
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: 298e961eff1736f32d2fa703dfb0079ea758e343
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132401380"
---
# <a name="bulk-download-a-list-of-groups-in-azure-active-directory"></a>Télécharger une liste de groupes par lot dans Azure Active Directory

À l’aide du portail Azure Active Directory (Azure AD), vous pouvez télécharger en bloc la liste de tous les groupes de votre organisation dans un fichier de valeurs séparées par des virgules (CSV). Tous les administrateurs et les utilisateurs non administrateurs peuvent télécharger des listes de groupes.

## <a name="to-download-a-list-of-groups"></a>Pour télécharger une liste de groupes

1. Connectez-vous [au portail Azure](https://portal.azure.com) avec un compte dans votre organisation.
1. Dans Azure AD, sélectionnez **Groupes** > **Télécharger les groupes**.
1. Dans la page de **Téléchargement de groupes**, sélectionnez **Démarrer** pour recevoir un fichier CSV qui répertorie vos groupes.

   ![La commande Télécharger les groupes se trouve sur la page Tous les groupes](./media/groups-bulk-download/bulk-download.png)

## <a name="check-download-status"></a>Vérifier l’état du téléchargement

Vous pouvez voir l’état de toutes vos demandes d’opération en bloc en attente dans la page **Résultats de l’opération en bloc**.

[![Vérifier l'état sur la page Résultats des opérations en bloc.](./media/groups-bulk-download/bulk-center.png)](./media/groups-bulk-download/bulk-center.png#lightbox)

## <a name="bulk-download-service-limits"></a>Limites du service de téléchargement en bloc

Chaque opération en bloc de téléchargement de liste de groupes peut prendre jusqu’à une heure. Cela vous permet de télécharger une liste d’au moins 300 000 groupes.

## <a name="next-steps"></a>Étapes suivantes

- [Supprimer en bloc les membres d’un groupe](groups-bulk-remove-members.md)
- [Télécharger les membres d’un groupe](groups-bulk-download-members.md)
