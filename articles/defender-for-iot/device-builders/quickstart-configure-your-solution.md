---
title: 'Démarrage rapide : Ajouter des ressources Azure à votre solution IoT'
description: Dans ce guide de démarrage rapide, vous allez découvrir comment configurer votre solution IoT de bout en bout avec Microsoft Defender pour IoT.
ms.topic: quickstart
ms.date: 11/09/2021
ms.openlocfilehash: 2ac4a91870f9a8ed0a0df2d9b4f8e904806eb482
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132283901"
---
# <a name="quickstart-configure-your-microsoft-defender-for-iot-solution"></a>Démarrage rapide : Configurer votre solution Microsoft Defender pour IoT

Cet article explique comment configurer votre solution de sécurité IoT à l’aide de Defender pour IoT pour la première fois.

## <a name="prerequisites"></a>Prérequis

- Aucun

## <a name="what-is-defender-for-iot"></a>Qu’est-ce que Defender pour IoT ?

Defender pour IoT fournit aux solutions IoT basées sur Azure une sécurité complète de bout en bout.

Avec Defender pour IoT, vous pouvez superviser l’intégralité de votre solution IoT dans un seul tableau de bord exposant l’ensemble de vos appareils IoT, plateformes IoT et ressources back-end dans Azure.

Une fois que vous avez activé Defender pour IoT sur votre hub IoT, Defender pour IoT identifie automatiquement les autres services Azure et se connecte aux services associés affiliés à votre solution IoT.

Vous pouvez également sélectionner d’autres groupes de ressources Azure qui font partie de votre solution IoT.

Vos sélections vous permettent d’ajouter des abonnements, groupes de ressources ou ressources uniques dans leur intégralité.

Une fois définies toutes les relations de ressources, Defender pour IoT utilise Defender pour le cloud afin de fournir des alertes et des recommandations de sécurité pour ces ressources.

## <a name="add-azure-resources-to-your-iot-solution"></a>Ajouter des ressources Azure à votre solution IoT

**Pour ajouter une nouvelle ressource à votre solution IoT** :

1. Dans le portail Azure, recherchez et sélectionnez **IoT Hub**.

1. Sous la section Sécurité, sélectionnez **Paramètres** > **Ressources supervisées**.

1. Sélectionnez **Modifier**, puis les ressources supervisées appartenant à votre solution IoT.

1. Sélectionnez **Ajouter**.

Un nouveau groupe de ressources est maintenant ajouté à votre solution IoT.

Defender pour IoT supervise désormais les groupes de ressources que vous avez récemment ajoutés, et expose les alertes et recommandations de sécurité pertinentes dans le cadre de votre solution IoT.

## <a name="next-steps"></a>Étapes suivantes

Allez à l’article suivant pour découvrir comment créer des micro-agents Defender-IoT...

> [!div class="nextstepaction"]
> [Créer des micro-agents Defender-IoT](quickstart-create-security-twin.md)
