---
title: Check-list pour la suppression de composants Azure Virtual Network Manager (préversion)
description: Cet article est une check-list pour la suppression de composants dans Azure Virtual Network Manager.
author: duongau
ms.author: duau
ms.service: virtual-network-manager
ms.topic: conceptual
ms.date: 11/02/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 65987c576ff16d766cb8da88ec824f0bb5bdd40a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131096286"
---
# <a name="remove-and-update-azure-virtual-network-manager-preview-components-checklist"></a>Check-list pour la suppression et la mise à jour de composants Azure Virtual Network Manager (préversion)

Cet article présente une check-list des étapes à effectuer pour supprimer ou mettre à jour un composant de configuration d’Azure Virtual Network Manager (préversion).

## <a name="remove-components-checklist"></a><a name="remove"></a> Check-list pour la suppression de composants

| Action | Étapes | 
| ------ | ----- |
| Annuler un déploiement de configuration de connectivité | Déployez une configuration de connectivité **None** sur les régions cibles. |
| Annuler un déploiement de la configuration de l’administration de la sécurité | Déployez une configuration de connectivité **None** sur les régions cibles. |
| Supprimer une règle d’administration de la sécurité | 1. Annulez le déploiement de la configuration de l’administration de la sécurité. </br> 2. Supprimez les règles d’administration de la sécurité associées à la configuration de la sécurité. |
| Supprimer une collection de règles de sécurité | 1. Annulez le déploiement de la configuration de l’administration de la sécurité. </br> 2. Supprimez les règles d’administration de la sécurité associées à la configuration de la sécurité. </br> 3. Supprimez la collection de règles. |
| Supprimer une configuration de l’administration de la sécurité | 1. Annulez le déploiement de la configuration de l’administration de la sécurité. </br> 2. Supprimez les règles d’administration de la sécurité associées à la configuration de la sécurité. </br> 3. Supprimez la collection de règles. </br> 4. Supprimez la configuration de l’administration de la sécurité. |
| Supprimer une configuration de la connectivité | 1. Annulez le déploiement de la configuration de la connectivité. </br> 2. Supprimez la configuration de la connectivité. |
| Supprimer un groupe réseau | 1. Supprimez la configuration de connectivité associée à ce groupe réseau. </br> 2. Supprimez toutes les règles de sécurité associées au groupe réseau. </br> 3. Supprimez le groupe réseau. |
| Supprimer l’instance Azure Virtual Network Manager | 1. Supprimez toutes les configurations de l’administration de la connectivité et de la sécurité. </br> 2. Supprimez tous les groupes réseau. </br> 3. Supprimez l’instance Azure Virtual Network Manager. |

## <a name="update-components-checklist"></a>Check-list pour la mise à jour de composants

Les informations présentées dans ce tableau partent du principe que vous disposez d’une configuration d’administration de la connectivité ou de la sécurité déployée sur une région cible.

| Action | Étapes |
| ------ | ----- |
| Ajouter ou supprimer un réseau virtuel utilisant une appartenance statique | 1. Ajoutez le réseau virtuel au groupe réseau. </br> 2. Validez la configuration de la connectivité ou de la sécurité contenant le groupe réseau. |
| Ajouter ou supprimer un réseau virtuel utilisant une appartenance dynamique | Azure Virtual Network Manager effectue automatiquement des modifications pour mettre en correspondance les instructions conditionnelles. |
| Ajouter, supprimer ou mettre à jour des règles de sécurité | * Si la configuration de la sécurité est appliquée à un groupe réseau contenant des membres statiques, vous devez redéployer la configuration pour qu’elle prenne effet. </br> * Les groupes réseau contenant des membres dynamiques sont mis à jour automatiquement. |
| Ajouter, supprimer ou mettre à jour des regroupements de règles | * Si la configuration de la sécurité est appliquée à un groupe réseau contenant des membres statiques, vous devez redéployer la configuration pour qu’elle prenne effet. </br> * Les groupes réseau contenant des membres dynamiques sont mis à jour automatiquement. |

## <a name="next-steps"></a>Étapes suivantes

Créez une instance [Azure Virtual Network Manager](create-virtual-network-manager-portal.md) en utilisant le portail Azure.
