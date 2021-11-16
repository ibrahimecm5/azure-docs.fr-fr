---
title: Configurer les journaux syslog VMware pour Azure VMware Solution
description: Découvrez comment configurer les paramètres de diagnostic afin de collecter les journaux syslog VMware pour votre cloud privé Azure VMware Solution.
ms.topic: how-to
ms.date: 09/24/2021
ms.openlocfilehash: 48422f6c0769953cee20c373ade8b8056ff0fee7
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2021
ms.locfileid: "131892764"
---
# <a name="configure-vmware-syslogs-for-azure-vmware-solution"></a>Configurer les journaux syslog VMware pour Azure VMware Solution

Les paramètres de diagnostic permettent de configurer l’exportation en continu des journaux et des métriques de la plateforme pour une ressource vers la destination de votre choix. Vous pouvez créer jusqu’à cinq paramètres de diagnostic différents pour envoyer différents journaux et métriques à des destinations indépendantes. 

Dans cet article, vous allez configurer un paramètre de diagnostic afin de collecter les journaux syslog VMware pour votre cloud privé Azure VMware Solution. Vous allez stocker le journal syslog dans un compte de stockage pour afficher les journaux vCenter et les analyser à des fins de diagnostic. 

## <a name="prerequisites"></a>Prérequis

Vérifiez que vous disposez d’un cloud privé Azure VMware Solution avec accès aux interfaces vCenter et NSX-T Manager. 

## <a name="configure-diagnostic-settings"></a>Configurer les paramètres de diagnostic

1. À partir de votre cloud privé Azure VMware Solution, sélectionnez **Paramètres de diagnostic**, puis **Ajouter des paramètres de diagnostic**.
 
   :::image type="content" source="media/diagnostic-settings/diagnostic-settings-log-analytics-workspace-1.png" alt-text="Capture d’écran montrant où configurer les journaux syslog VMware." lightbox="media/diagnostic-settings/diagnostic-settings-log-analytics-workspace-1.png":::


1. Sélectionnez **vmwaresyslog**, **Toutes les métriques**, puis sélectionnez l’une des options présentées suivantes.

   >[!IMPORTANT]
   >L’option **Envoyer à l’espace de travail Log Analytics** ne fonctionne pas actuellement.
 
   ### <a name="archive-to-storage-account"></a>Archiver dans un compte de stockage

    1. Dans **Paramètre de diagnostic**, sélectionnez le compte de stockage dans lequel vous souhaitez stocker les journaux, puis sélectionnez **Enregistrer**.

       :::image type="content" source="media/diagnostic-settings/diagnostic-settings-log-analytics-workspace-2.png" alt-text="Capture d’écran montrant les options à sélectionner pour le stockage des journaux syslog." lightbox="media/diagnostic-settings/diagnostic-settings-log-analytics-workspace-2.png":::

    1. Accédez à vos **Comptes de stockage**, vérifiez que les **journaux Insight vmwarelog** ont été créés, puis sélectionnez-les. 
 
       :::image type="content" source="media/diagnostic-settings/diagnostic-settings-log-analytics-workspace-3.png" alt-text="Capture d’écran montrant l’option vmwarelog des journaux Insight créée et disponible." lightbox="media/diagnostic-settings/diagnostic-settings-log-analytics-workspace-3.png":::


    1. Parcourez les **journaux Insight vmwarelog** pour localiser et télécharger le fichier json et consulter les journaux.

       :::image type="content" source="media/diagnostic-settings/diagnostic-settings-log-analytics-workspace-4.png" alt-text="Capture d’écran montrant le chemin du fichier json." lightbox="media/diagnostic-settings/diagnostic-settings-log-analytics-workspace-4.png"::: 

   ### <a name="stream-to-microsoft-azure-event-hubs"></a>Diffuser vers Microsoft Azure Event Hubs

    1. Dans **Paramètre de diagnostic**, sous Détails de la destination, sélectionnez **Diffuser vers les Event Hubs**. 
    1. Dans le menu déroulant de l’**espace de noms Event Hub**, choisissez l’emplacement auquel vous souhaitez envoyer les journaux, sélectionnez-le, puis **Enregistrez**.
    
       :::image type="content" source="media/diagnostic-settings/stream-event-hub.png" alt-text="Capture d’écran montrant le chemin pour l’envoi des journaux." lightbox="media/diagnostic-settings/stream-event-hub.png"::: 




