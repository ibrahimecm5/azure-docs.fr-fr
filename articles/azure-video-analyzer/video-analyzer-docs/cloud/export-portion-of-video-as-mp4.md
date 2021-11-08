---
title: Exporter une partie d’une vidéo enregistrée dans Azure Video Analyzer dans un fichier MP4
description: Dans ce didacticiel, vous allez apprendre à exporter une vidéo enregistrée avec Video Analyzer sous la forme d’un fichier MP4 stocké en tant que vidéo Video Analyzer qui peut être téléchargée et consommée en dehors de l’écosystème du compte Video Analyzer.
ms.topic: tutorial
ms.date: 10/18/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 74b46b6c46e3ae6cb6ae9f4567b20bcee3e37ebe
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131096757"
---
# <a name="tutorial-export-portion-of-recorded-video-as-an-mp4-file"></a>Didacticiel : Exporter une partie de la vidéo enregistrée sous forme de fichier MP4

[!INCLUDE [header](includes/cloud-env.md)]

Dans ce didacticiel, vous allez apprendre à exporter une partie de vidéo qui a été enregistrée avec le compte Azure Video Analyzer.  Cette partie de vidéo exportée est enregistrée sous la forme d’un fichier MP4 qui peut être téléchargé et consommé en dehors du compte Video Analyzer.

## <a name="suggested-pre-reading"></a>Lecture préalable suggérée  

Lisez ces articles avant de commencer :

* [Vue d’ensemble d’Azure Video Analyzer](../overview.md)
* [Terminologie d’Azure Video Analyzer](../terminology.md)
* [Concepts relatifs aux pipelines Azure Video Analyzer](../pipeline.md)
* [Enregistrement de vidéo basé sur les événements](../event-based-video-recording-concept.md)

## <a name="prerequisites"></a>Prérequis

Les prérequis pour ce tutoriel sont les suivants :

* Un compte Azure incluant un abonnement actif. Si vous n’en avez pas déjà un, [créez un compte](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) gratuitement.
* [Compte Video Analyzer](../create-video-analyzer-account.md).
* A terminé le [Démarrage rapide : Détecter le mouvement, enregistrer la vidéo sur Video Analyzer](../edge/detect-motion-record-video-clips-cloud.md) ou tout pipeline Video Analyzer qui enregistre sur un récepteur vidéo.

## <a name="overview"></a>Vue d’ensemble

Video Analyzer peut enregistrer des vidéos à partir d’une source RTSP. Ces vidéos sont enregistrées dans une archive segmentée du compte Video Analyzer.  Le format d’archive segmentée autorise une durée d’enregistrement vidéo illimitée. Toutefois, dans certains cas, il est nécessaire d’enregistrer une partie de la vidéo au format MP4 pour pouvoir être archivée, téléchargée ou lue en dehors de l’écosystème Video Analyzer.

Ce tutoriel vous apprendra à effectuer les opérations suivantes :

* À propos des topologies et tâches de pipeline par lots
* Comment créer une topologie de lots.
* Comment créer une tâche de pipeline par lots à partir d’une archive vidéo Video Analyzer qui créera un fichier MP4 contenant la valeur de temps spécifiée (jusqu’à 24 heures).

## <a name="pipeline-topology-of-batch-kind"></a>Topologie du pipeline du genre **lot**

Une topologie de pipeline de type lot vous permet de décrire la manière dont la vidéo enregistrée doit être traitée pour l’exportation en fonction de vos besoins personnalisés via trois nœuds interconnectés.  Une topologie de pipeline de type lot est la base utilisée pour les tâches de pipeline.  Une tâche de pipeline est l’instance individuelle d’une topologie de pipeline de type lot.  La tâche de pipeline importe la vidéo Video Analyzer enregistrée et la sauvegarde dans le compte de stockage Video Analyzer, sous la forme d’un fichier MP4 téléchargeable.  La topologie de pipeline de type lot utilise un [nœud source vidéo](../pipeline.md#video-source) qui se connecte à un [nœud de processeur d’encodeur](../pipeline.md#encoder-processor), puis se connecte à un [nœud récepteur vidéo](../pipeline.md#video-sink).

> [!NOTE]
> Pour plus d’informations sur les sources, les processeurs et les récepteurs, consultez [Sources, processeurs et récepteurs](../pipeline.md#sources-processors-and-sinks). Pour plus d’informations sur les tâches de pipeline, consultez [Tâches de pipeline](../pipeline.md#batch-pipeline)

## <a name="create-a-pipeline-job-from-videos"></a>Créer un travail de pipeline (à partir de vidéos)

1. Dans le Portail Azure, accédez à votre compte Video Analyzer.
1. Sélectionnez **Vidéos** sous la section `Video Analyzer`, puis sélectionnez le flux vidéo à utiliser pour exporter une partie de la vidéo.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/export-portion-of-video-as-mp4/video-analyzer-video.png" alt-text="Image de la section de menu de l’analyseur vidéo Azure Video Analyzer mettant en surbrillance la sélection de vidéos.":::
1. Dans le panneau du lecteur de widget vidéo, cliquez sur **Créer une tâche** en haut.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/export-portion-of-video-as-mp4/create-job.png" alt-text="Image du panneau widget vidéo d’Azure Video Analyzer mettant en surbrillance la sélection Créer un travail.":::
1. Dans le panneau volant`Create Job`, sélectionnez :

   1. Sélectionnez **Créer à partir d’un exemple** pour le `Batch topology`.
   1. Sélectionnez l’exemple de topologie **Exportation de vidéo** dans la liste déroulante `Batch topology name`.
   1. Entrez un nom dans la `Batch topology name` pour enregistrer la topologie sous.

        > [!NOTE]
        > L’exemple de topologie sera enregistré sous le nom entré ci-dessus.  Il pourra être réutilisé la prochaine fois qu’une vidéo sera exportée.
1. Dans la section `Name your job`, entrez un **Nom de travail** dans le champ Nom du travail et une **Description** pour le travail dans le champ Description (la description est facultative)
1. Dans la section `Define parameters` :
    1. Entrez le nom de la vidéo enregistrée avec Video Analyzer dans le champ `sourceVideoName`.
    1. Dans le champ `videoSourceTimeSequenceParameter`, sélectionnez les dates de début et de fin en cliquant sur l’icône de calendrier et en sélectionnant les dates pour chaque valeur.  Dans les champs Heure, entrez les heures de début et de fin de la tâche de pipeline à utiliser pour créer le clip vidéo.
        > [!NOTE]
        > La valeur de temps d’une vidéo enregistrée donnée s’affiche en haut à droite du lecteur de widget vidéo.  Cette valeur d’heure s’affiche dans l’image ci-dessous avec un cadre rouge.  L’icône du calendrier s’affiche également dans l’image ci-dessous, avec un cadre vert.

    1. Entrez un nom pour le fichier MP4 exporté dans le champ `exportedVideoName`.
  
      > [!div class="mx-imgBorder"]
      > :::image type="content" source="./media/export-portion-of-video-as-mp4/video-widget-job-creation.png" alt-text="Image du widget du lecteur vidéo et des tâches de pipeline Azure Video Analyzer mettant en surbrillance l’horodatage de la vidéo dans un cadre rouge et l’icône du calendrier avec un cadre vert.":::
1. Cliquez sur l’option **Créer** figurant en bas du panneau volant `Create a job`.  
    
    Pour surveiller la tâche de pipeline, accédez à `Batch Jobs tab`.
1. Dans la section `Video Analyzer`, sélectionnez **Lot**.
1. Cliquez sur l’onglet `Jobs` en haut du panneau volant.
  
    Le traitement par lots entrera en état de traitement, puis, en cas de réussite de l’opération, il passera à l’état `Completed`.  Pour afficher le fichier vidéo MP4 associé :
1. Cliquez sur **Vidéos** sous la section `Video Analyzer`.
1. Cliquez sur le nom de la vidéo qui correspond au nom des travaux de traitement par lots utilisé précédemment à l’étape 5.  

Le lecteur du widget vidéo doit démarrer la lecture du fichier MP4.  Pour télécharger le fichier MP4, cliquez sur `Download video` en haut du panneau.  Le fichier MP4 s’ouvre dans un nouvel onglet de navigateur. Cliquez avec le bouton droit sur la vidéo, puis cliquez sur **Enregistrer sous**.

## <a name="cancel-a-pipeline-job"></a>Annuler une tâche de pipeline

Une fois qu’une tâche de pipeline est passée à l’état de traitement, la tâche de pipeline peut être annulée.  Annuler une tâche de pipeline :

1. Accédez au compte Video Analyzer et sélectionnez **Lot** dans la section **Video Analyzer**.
1. Dans le panneau Lot, sélectionnez l’onglet **Tâches** en haut.
1. Sous l’onglet Tâches, vous trouverez la liste des tâches qui se trouvent dans différents états. Recherchez la tâche en cours de traitement que vous souhaitez annuler et sélectionnez **Annuler** sur le côté droit de l’onglet Tâches de pipeline par lots, puis cliquez sur **Oui**.

    > [!NOTE]
    > Impossible d’annuler une tâche de pipeline qui a échoué.

## <a name="delete-a-pipeline-job"></a>Suppression d’une tâche de pipeline

Une fois qu’une tâche de pipeline est passée à l’état Terminée ou Échec, la tâche de pipeline peut être supprimée.  Pour supprimer une tâche de pipeline :

1. Accédez au compte Video Analyzer et sélectionnez **Lot** dans la section **Video Analyzer**.
1. Dans le panneau Lot, sélectionnez l’onglet **Tâches** en haut.
1. Sous l’onglet Tâches, vous trouverez la liste des tâches qui se trouvent dans différents états.  Recherchez la tâche que vous souhaitez supprimer (avec l’état Annulée, Terminée ou Échec), sélectionnez **Supprimer** dans la partie droite du panneau Tâches de pipeline, puis cliquez sur **Supprimer**.

## <a name="delete-a-pipeline-topology-of-batch-kind"></a>Supprimer une topologie de pipeline de type lot

Pour supprimer une topologie de pipeline de type lot, toutes les tâches de pipeline associées à la topologie de pipeline doivent être supprimées.  Pour supprimer une topologie de pipeline de type lot :

1. Accédez au compte Video Analyzer.
2. Cliquez sur **Lot** sous la section `Video Analyzer`.
3. Sous l’onglet Topologies, localisez la topologie de votre pipeline de type lot à supprimer, puis cliquez sur le bouton « **...** » à droite de la topologie du pipeline de type lot.
4. Cliquez sur **Supprimer la topologie**.

    > [!NOTE]
    > Toutes les tâches de pipeline doivent être supprimées d’une topologie de pipeline de type lot avant qu’une topologie de pipeline de type lot puisse être supprimée.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous voulez essayer d’autres guides de démarrage rapide ou tutoriels, conservez les ressources que vous avez créées. Dans le cas contraire, accédez au portail Azure, puis à vos groupes de ressources. Sélectionnez ensuite le groupe de ressources où vous avez exécuté ce guide de démarrage rapide, puis supprimez toutes les ressources.

## <a name="next-steps"></a>Étapes suivantes

* [Connecter des caméras directement dans le cloud](./connect-cameras-to-cloud.md) dans l’ordre de capture et d’enregistrement vidéo, à l’aide de [pipelines cloud](../pipeline.md).
* [Connecter les caméras au service Video Analyzer par le biais du module Edge Video Analyzer agissant comme une passerelle transparente pour les paquets vidéo via le protocole RTSP](./use-remote-device-adapter.md).
