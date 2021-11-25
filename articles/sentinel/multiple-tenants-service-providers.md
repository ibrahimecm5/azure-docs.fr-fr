---
title: Gérer plusieurs locataires dans Microsoft Sentinel en tant que fournisseur de services de sécurité gérée | Microsoft Docs
description: Intégration et gestion de plusieurs locataires dans Microsoft Sentinel en tant que fournisseur de services de sécurité gérés (MSSP) à l’aide d’Azure Lighthouse.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 5b2ae955210a230353cdc6af54c8e17b56388192
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132517498"
---
# <a name="manage-multiple-tenants-in-microsoft-sentinel-as-an-mssp"></a>Gérer plusieurs locataires dans Microsoft Sentinel en tant que MSSP

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Si vous êtes un fournisseur de services de sécurité gérés (MSSP, managed security service provider) et que vous utilisez [Azure Lighthouse](../lighthouse/overview.md) pour offrir des services de centre des opérations de sécurité (SOC) à vos clients, vous pouvez gérer les ressources Microsoft Sentinel de vos clients directement depuis votre propre locataire Azure, sans avoir à vous connecter au locataire du client. 

## <a name="prerequisites"></a>Prérequis

- [Intégrez Azure Lighthouse](../lighthouse/how-to/onboard-customer.md).

- Pour que le processus fonctionne correctement, votre locataire (le locataire du MSSP) doit disposer de fournisseurs de ressources Microsoft Sentinel inscrits sur au moins un abonnement. En outre, les fournisseurs de ressources doivent être inscrits pour chacun des locataires de vos clients. Si vous avez inscrit Microsoft Sentinel dans votre locataire et vos clients dans le leur, vous êtes prêt à commencer. Pour vérifier l’inscription, procédez comme suit :

    1. Sélectionnez **Abonnements** dans le portail Azure, puis sélectionnez un abonnement approprié dans le menu.

    1. Dans le menu de navigation de l’écran d’abonnement, sous **Paramètres**, sélectionnez **Fournisseurs de ressources**.

    1. À partir de l’écran ***nom de l’abonnement* | Fournisseurs de ressources**, recherchez et sélectionnez *Microsoft.OperationalInsights* et *Microsoft.SecurityInsights*, puis vérifiez la colonne **État**. Si l’état du fournisseur est *NotRegistered*, sélectionnez **Inscrire**.
    
        :::image type="content" source="media/multiple-tenants-service-providers/check-resource-provider.png" alt-text="Vérification des fournisseurs de ressources":::

## <a name="how-to-access-microsoft-sentinel-in-managed-tenants"></a>Accéder à Microsoft Sentinel dans des locataires gérés

1. Sous **Répertoire + abonnement**, sélectionnez les répertoires délégués (répertoire = locataire) et les abonnements où se trouvent les espaces de travail Microsoft Sentinel de votre client.

    :::image type="content" source="media/multiple-tenants-service-providers/directory-subscription.png" alt-text="Choisir les locataires et les abonnements":::

1. Ouvrez Microsoft Sentinel. Vous voyez tous les espaces de travail des abonnements sélectionnés. Vous pourrez les utiliser en toute transparence comme n’importe quel espace de travail de votre propre locataire.

> [!NOTE]
> Vous ne pourrez pas déployer de connecteurs dans Microsoft Sentinel à partir d’un espace de travail managé. Pour déployer un connecteur, vous devez vous connecter directement au locataire sur lequel vous souhaitez le déployer et vous authentifier avec les autorisations nécessaires.

## <a name="next-steps"></a>Étapes suivantes

Ce document vous a permis d’apprendre à gérer plusieurs locataires Microsoft Sentinel en toute transparence. Pour en savoir plus sur Microsoft Sentinel, consultez les articles suivants :
- Découvrez comment [avoir une visibilité sur vos données et les menaces potentielles](get-visibility.md).
- Prise en main de la [détection des menaces avec Microsoft Sentinel](detect-threats-built-in.md).
