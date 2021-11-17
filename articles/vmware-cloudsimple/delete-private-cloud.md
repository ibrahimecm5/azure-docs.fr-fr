---
title: Supprimer un cloud privé Azure VMware Solution by CloudSimple
description: Découvrez comment supprimer un cloud privé CloudSimple. Lorsque vous supprimez un cloud privé, tous ses clusters sont supprimés.
author: suzizuber
ms.author: v-szuber
ms.date: 08/06/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 602e7315a398f428276fac6eeadf7dc9eba7340d
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132322604"
---
# <a name="delete-a-cloudsimple-private-cloud"></a>Supprimer un cloud privé CloudSimple

CloudSimple fournit la flexibilité nécessaire à la suppression d’un cloud privé.  Un cloud privé se compose d’un ou de plusieurs clusters vSphere. Chaque cluster peut contenir entre 3 et 16 nœuds. Lorsque vous supprimez un cloud privé, tous ses clusters sont supprimés.

## <a name="before-you-begin"></a>Avant de commencer

Lorsque vous supprimez un cloud privé, vous supprimez l’intégralité de celui-ci.  Tous les composants du cloud privé seront supprimés.  Si vous souhaitez conserver certaines données, sauvegardez ces données dans un stockage local ou dans le stockage Azure.

Un cloud privé comprend les composants suivants :

* Nœuds CloudSimple
* Machines virtuelles
* Réseaux VLAN/Sous-réseaux
* Toutes les données utilisateur stockées dans le cloud privé
* Tous les éléments joints aux règles de pare-feu d’un réseau VLAN ou d’un sous-réseau

## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous au portail Azure sur [https://portal.azure.com](https://portal.azure.com).

## <a name="delete-a-private-cloud"></a>Supprimer un cloud privé

1. [Accédez au portail CloudSimple](access-cloudsimple-portal.md).

2. Ouvrez la page **Ressources**.

3. Cliquez sur le cloud privé que vous souhaitez supprimer.

4. Dans la page récapitulative, cliquez sur **Supprimer**.

    ![Supprimer un cloud privé](media/delete-private-cloud.png)

5. Dans la page confirmation, entrez le nom du cloud privé, puis cliquez sur **Supprimer**. 

    ![Supprimer un cloud privé - Confirmation](media/delete-private-cloud-confirm.png)

Le cloud privé est marqué pour la suppression.  Le processus de suppression démarre au bout de trois heures et aboutit à la suppression du cloud privé.

> [!CAUTION]
> Les nœuds doivent être supprimés après la suppression du cloud privé.  Le contrôle des nœuds continuera tant que ceux-ci ne seront pas supprimés de votre abonnement.

## <a name="next-steps"></a>Étapes suivantes

* [Supprimer des nœuds](delete-nodes.md)
