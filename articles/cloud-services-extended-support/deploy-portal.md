---
title: Déployer un service cloud Azure (support étendu) - Portail Azure
description: Déployer un service cloud Azure (support étendu) à l’aide du portail Azure
ms.topic: tutorial
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 4e35814e898e35a7ba9251d3eb36f6bde4d086e1
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132339988"
---
# <a name="deploy-a-azure-cloud-services-extended-support-using-the-azure-portal"></a>Déployer une instance Azure Cloud Services (support étendu) à l’aide du portail Azure
Cet article explique comment utiliser le portail Azure pour créer un déploiement de service cloud (support étendu). 

## <a name="before-you-begin"></a>Avant de commencer

Consultez les [prérequis du déploiement](deploy-prerequisite.md) de Cloud Services (support étendu) et créez les ressources associées. 

## <a name="deploy-a-cloud-services-extended-support"></a>Déployer une instance Cloud Services (support étendu) 
1. Connectez-vous au [portail Azure](https://portal.azure.com)

2.  À l’aide de la barre de recherche située en haut du portail Azure, recherchez et sélectionnez **Cloud Services (support étendu)** .

    :::image type="content" source="media/deploy-portal-1.png" alt-text="L’image montre le panneau de toutes les ressources dans le portail Azure.":::
 
3.  Dans le volet Cloud Services (support étendu), sélectionnez **Créer**. 

    :::image type="content" source="media/deploy-portal-2.png" alt-text="L’image montre l’achat d’un service cloud à partir de la Place de marché.":::

4. La fenêtre de création de Cloud Services (support étendu) s’ouvre sous l’onglet **Informations de base**. 
    - Sélectionnez un abonnement.
    - Choisissez un groupe de ressources ou créez-en un.
    - Entrez le nom souhaité pour votre déploiement de service cloud (support étendu).
        - Le nom DNS du service cloud est distinct spécifié par l’étiquette de nom DNS de l’adresse IP publique et peut être modifié dans la section d’adresse IP publique de l’onglet Configuration.
    -  Sélectionnez la région dans laquelle effectuer le déploiement.

    :::image type="content" source="media/deploy-portal-3.png" alt-text="L’image montre le panneau d’accueil Cloud Services (support étendu).":::

5. Ajoutez vos fichiers de configuration, de package et de définition de service cloud. Vous pouvez ajouter des fichiers existants à partir du stockage d’objets blob ou les charger à partir de votre ordinateur local. En cas de chargement à partir de votre ordinateur local, les fichiers sont alors stockés dans un compte de stockage. 

    :::image type="content" source="media/deploy-portal-4.png" alt-text="L’image montre la section de chargement de l’onglet des informations de base lors de la création.":::

6. Une fois tous les champs complétés, accédez à l’onglet **Configuration** et remplissez-le. 
    - Sélectionnez un réseau virtuel à associer au service cloud ou créez-en un nouveau. 
        - Les déploiements de service cloud (support étendu) **doivent** être effectués dans un réseau virtuel. Le réseau virtuel **doit** également être référencé dans le fichier de configuration de service (.cscfg) sous la section `NetworkConfiguration`.
    - Sélectionnez une adresse IP publique existante à associer au service cloud ou créez-en une nouvelle.
        - Si vous avez des **points de terminaison d’entrée d’adresse IP** définis dans votre fichier de définition de service (.csdef), une adresse IP publique doit être créée pour votre service cloud. 
        - Cloud Services (support étendu) prend uniquement en charge la référence SKU d’adresse IP de base.
        - Si votre configuration de service (.cscfg) contient une adresse IP réservée, le type d’allocation pour l’adresse IP publique doit être défini sur **Statique**. 
        - Attribuez éventuellement un nom DNS à votre point de terminaison de service cloud en mettant à jour la propriété d’étiquette DNS de l’adresse IP publique associée au service cloud.  
    - (Facultatif) Démarrez le service cloud. Choisissez de démarrer ou ne pas démarrer le service immédiatement après la création.
    - Sélectionnez un coffre de clés. 
        - Key Vault est nécessaire quand vous spécifiez un ou plusieurs certificats dans votre fichier de configuration de service (.cscfg). Quand vous sélectionnez un coffre de clés, nous essayons de trouver les certificats sélectionnés dans votre fichier de configuration de service (.cscfg) en fonction de leurs empreintes numériques. Si des certificats sont manquants dans votre coffre de clés, vous pouvez les charger maintenant, puis cliquer sur **Actualiser**.   

 :::image type="content" source="media/deploy-portal-5.png" alt-text="L’image montre le panneau de configuration dans le portail Azure lors de la création d’une instance Cloud Services (support étendu).":::

7. Une fois tous les champs complétés, accédez à l’onglet **Vérifier et créer** pour valider la configuration de votre déploiement et créer votre service cloud (support étendu).

## <a name="next-steps"></a>Étapes suivantes 
- Consultez les [questions fréquentes (FAQ)](faq.yml) concernant Cloud Services (support étendu).
- Déployez un service cloud (support étendu) avec le [portail Azure](deploy-portal.md), [PowerShell](deploy-powershell.md), un [modèle](deploy-template.md) ou [Visual Studio](deploy-visual-studio.md).
- Visitez le [dépôt d’exemples de Cloud Services (support étendu)](https://github.com/Azure-Samples/cloud-services-extended-support).
