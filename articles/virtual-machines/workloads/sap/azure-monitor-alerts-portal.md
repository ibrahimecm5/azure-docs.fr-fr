---
title: Configurer des alertes dans Azure Monitor pour SAP Solutions à l’aide du portail Azure
description: Découvrez comment utiliser une méthode de navigateur pour configurer des alertes dans Azure Monitor pour SAP Solutions.
author: sameeksha91
ms.author: sakhare
ms.topic: how-to
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.date: 08/30/2021
ms.openlocfilehash: 642eb96d07a351537f1320f823e698968e38d23e
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132309564"
---
# <a name="configure-alerts-in-azure-monitor-for-sap-solutions-by-using-the-azure-portal"></a>Configurer des alertes dans Azure Monitor pour SAP Solutions à l’aide du portail Azure

Dans cet article, nous allons passer en revue les étapes de configuration des alertes dans Azure Monitor pour SAP Solutions. Nous allons configurer des alertes et des notifications à partir du [Portail Azure](https://azure.microsoft.com/features/azure-portal) à l’aide de son interface basée sur le navigateur.

## <a name="prerequisites"></a>Prérequis

Déployez la ressource Azure Monitor pour SAP Solutions avec au moins un fournisseur. Vous pouvez configurer des fournisseurs pour : 
- Application SAP (NetWeaver)
- SAP HANA
- Microsoft SQL Server
- Cluster (pacemaker) haute disponibilité

## <a name="sign-in-to-the-portal"></a>Se connecter au portail

Connectez-vous au [portail Azure](https://portal.azure.com).

## <a name="create-an-alert-rule"></a>Création d'une règle d'alerte

1.  Dans le portail Azure, parcourez et sélectionnez votre ressource Azure Monitor pour SAP Solutions. Vérifiez que vous disposez d’au moins un fournisseur configuré pour cette ressource. 
2.  Accédez aux classeurs de votre choix. Par exemple, SAP HANA et sélectionnez une instance HANA.

    :::image type="content" source="./media/ams-alerts/ams-alert-1.png" alt-text="Capture d’écran montrant la position du bouton d’alerte." lightbox="./media/ams-alerts/ams-alert-1.png":::
  
3.  Sélectionnez le bouton **Alertes** pour afficher les **modèles d’alerte** disponibles.

    :::image type="content" source="./media/ams-alerts/ams-alert-2.png" alt-text="Capture d’écran montrant la liste des modèles d’alerte." lightbox="./media/ams-alerts/ams-alert-2.png":::
    
4.  Sélectionnez **Créer une règle** pour configurer une alerte de votre choix.
5.  Entrez le **seuil d’alerte**, choisissez une **instance de fournisseur** et choisissez ou créez un **groupe d’actions** pour configurer le paramètre de notification. Vous pouvez modifier les informations de fréquence et de gravité selon vos besoins.

    >[!Tip]
    > En savoir plus sur les [groupes d’actions](../../../azure-monitor/alerts/action-groups.md). 
    
7.  Sélectionnez **Activer une règle d’alerte**.

    :::image type="content" source="./media/ams-alerts/ams-alert-3.png" alt-text="Capture d’écran montrant la page de configuration des alertes." lightbox="./media/ams-alerts/ams-alert-3.png":::
    
7.  Sélectionnez **Déployer la règle d’alerte** pour terminer la configuration de votre règle d’alerte. Vous pouvez choisir d’afficher le modèle d’alerte en cliquant sur **Afficher le modèle**.

    :::image type="content" source="./media/ams-alerts/ams-alert-4.png" alt-text="Capture d’écran montrant la dernière étape de la configuration d’une alerte." lightbox="./media/ams-alerts/ams-alert-4.png":::
    
8.  Accédez à **Règles d’alerte** pour afficher la règle d’alerte nouvellement créée. Lorsque et si des alertes sont déclenchées, vous pouvez les voir sous **Alertes déclenchées**.

    :::image type="content" source="./media/ams-alerts/ams-alert-5.png" alt-text="Capture d’écran montrant le résultat de la configuration des alertes." lightbox="./media/ams-alerts/ams-alert-5.png":::

## <a name="next-steps"></a>Étapes suivantes

Découvrez plus en détail Azure Monitor pour SAP Solutions.

> [!div class="nextstepaction"]
> [Superviser SAP sur Azure](monitor-sap-on-azure.md)
