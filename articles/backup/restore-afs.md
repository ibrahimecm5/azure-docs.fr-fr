---
title: Restaurer des partages de fichiers Azure
description: Découvrez comment utiliser le portail Azure pour restaurer un partage de fichiers entier ou des fichiers spécifiques à partir d’un point de restauration créé par le service Sauvegarde Azure.
ms.topic: conceptual
ms.date: 11/03/2021
author: v-amallick
ms.service: backup
ms.author: v-amallick
ms.openlocfilehash: 464c9927f901a373310ad6d1c0812a5a5de1eaaa
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131846818"
---
# <a name="restore-azure-file-shares"></a>Restaurer des partages de fichiers Azure

Cet article explique comment utiliser le portail Azure pour restaurer l’intégralité d’un partage de fichiers ou des fichiers spécifiques à partir d’un point de restauration créé par le service [Sauvegarde Azure](./backup-overview.md).

Dans cet article, vous allez apprendre à :

* restaurer un partage de fichiers Azure complet ;
* restaurer des fichiers ou dossiers individuels ;
* suivre le statut de l’opération de restauration.

## <a name="steps-to-perform-a-restore-operation"></a>Procédure d’exécution d’une opération de restauration

Pour exécuter une opération de restauration, procédez comme suit.

### <a name="select-the-file-share-to-restore"></a>Sélectionner le partage de fichiers à restaurer

1. Dans le [portail Azure](https://portal.azure.com/), accédez au **Centre de sauvegarde**, puis cliquez sur **Restaurer**.

   :::image type="content" source="./media/restore-afs/backup-center-restore-inline.png" alt-text="Capture d’écran montrant comment démarrer le processus de restauration du partage de fichiers Azure." lightbox="./media/restore-afs/backup-center-restore-expanded.png":::

1. Sélectionnez **Azure Files (Stockage Azure)** comme type de source de source, sélectionnez le partage de fichiers que vous souhaitez restaurer, puis cliquez sur **Continuer**.

   :::image type="content" source="./media/restore-afs/azure-file-share-select-instance.png" alt-text="Capture d’écran montrant la sélection de l’option des éléments de sauvegarde.":::

### <a name="full-share-recovery"></a>Récupération complète du partage

Vous pouvez utiliser cette option de restauration pour restaurer l’intégralité du partage de fichiers dans l’emplacement d’origine ou dans un autre emplacement.

1. Une fois que vous avez sélectionné **Continuer** à l’étape précédente, le volet **Restaurer** s’ouvre. Pour sélectionner le point de restauration à utiliser pour exécuter l’opération de restauration, choisissez le texte du lien **Sélectionner** situé sous la zone de texte **Point de restauration**.

    ![Sélectionner un point de restauration en choisissant Sélectionner](./media/restore-afs/select-restore-point.png)

1. Le volet contextuel **Sélectionner un point de restauration** s’ouvre à droite et propose une liste de points de restauration disponibles pour le partage de fichiers sélectionné. Sélectionnez le point de restauration que vous souhaitez utiliser pour effectuer l’opération de restauration, puis sélectionnez **OK**.

   :::image type="content" source="./media/restore-afs/azure-file-share-select-restore-point-inline.png" alt-text="Capture d’écran montrant la sélection d’un point de restauration." lightbox="./media/restore-afs/azure-file-share-select-restore-point-expanded.png":::

    >[!NOTE]
    >Par défaut, le volet **Sélectionner un point de restauration** présente les points de restauration des 30 derniers jours. Si vous souhaitez afficher les points de restauration créés à un moment précis, spécifiez la plage en sélectionnant l’**heure de début** et l’**heure de fin** appropriées, puis sélectionnez le bouton **Actualiser**.

1. L’étape suivante consiste à choisir l’**emplacement de restauration**. Dans la section **Destination de récupération**, spécifiez où et comment restaurer les données. Sélectionnez l’une des deux options suivantes à l’aide du bouton bascule :

    * **Emplacement d’origine** : Restaurez le partage de fichiers complet au même emplacement que la source d’origine.
    * **Autre emplacement** : Restaurez le partage de fichiers complet à un autre emplacement et conservez le partage de fichiers d’origine tel quel.

#### <a name="restore-to-the-original-location-full-share-recovery"></a>Restaurer à l'emplacement d'origine (récupération de l'intégralité du partage)

1. Sélectionnez **Emplacement d’origine** comme **Destination de récupération** et choisissez d’ignorer ou de remplacer en cas de conflit en sélectionnant l’option appropriée dans la liste déroulante **En cas de conflit**.

1. Sélectionnez **Restaurer** pour démarrer l’opération de restauration.

   :::image type="content" source="./media/restore-afs/azure-file-share-original-location-recovery.png" alt-text="Capture d’écran montrant la sélection de restaurer pour démarrer.":::

#### <a name="restore-to-an-alternate-location-full-share-recovery"></a>Restaurer à un autre emplacement (récupération de l'intégralité du partage)

1. Sélectionnez **Autre emplacement** comme **Destination de récupération**.
1. Dans la liste déroulante **Compte de stockage**, sélectionnez le compte de stockage de destination dans lequel vous souhaitez restaurer le contenu sauvegardé.
1. La liste déroulante **Sélectionner le partage de fichiers** affiche les partages de fichiers présents dans le compte de stockage que vous avez sélectionné à l’étape 2. Sélectionnez le partage de fichiers dans lequel vous souhaitez restaurer le contenu sauvegardé.
1. Dans la zone **Nom du dossier**, spécifiez le nom du dossier que vous souhaitez créer dans le partage de fichiers de destination avec le contenu restauré.
1. Indiquez si les conflits doivent être ignorés ou remplacés.
1. Après avoir entré les valeurs appropriées dans toutes les zones, sélectionnez **Restaurer** pour démarrer l’opération de restauration.

   :::image type="content" source="./media/restore-afs/azure-file-share-alternate-location-recovery.png" alt-text="Capture d’écran montrant la sélection de l’option Autre emplacement.":::

### <a name="item-level-recovery"></a>Récupération au niveau de l'élément

Vous pouvez utiliser cette option de restauration pour restaurer des fichiers ou dossiers individuels à l’emplacement d’origine ou dans un autre emplacement.

1. Accédez au **Centre de sauvegarde** et sélectionnez **Instances de sauvegarde** dans le menu, en sélectionnant le type de source de données **Stockage Azure (Azure Files)** .
1. Sélectionnez le partage de fichiers pour lequel vous souhaitez effectuer une récupération au niveau de l’élément.

   Le menu d’élément de sauvegarde s’affiche avec l’option **Récupération de fichier**.

    ![Sélectionner la récupération de fichier](./media/restore-afs/file-recovery.png)

1. Lorsque vous sélectionnez **Récupération de fichier**, le volet **Restaurer** s’ouvre. Pour sélectionner le point de restauration à utiliser pour exécuter l’opération de restauration, choisissez le texte du lien **Sélectionner** situé sous la zone de texte **Point de restauration**.

    ![Sélectionner un point de restauration en choisissant le lien Sélectionner](./media/restore-afs/select-restore-point.png)

1. Le volet contextuel **Sélectionner un point de restauration** s’ouvre à droite et propose une liste de points de restauration disponibles pour le partage de fichiers sélectionné. Sélectionnez le point de restauration que vous souhaitez utiliser pour effectuer l’opération de restauration, puis sélectionnez **OK**.

    ![Sélectionner le point de restauration](./media/restore-afs/restore-point.png)

1. L’étape suivante consiste à choisir l’**emplacement de restauration**. Dans la section **Destination de récupération**, spécifiez où et comment restaurer les données. Sélectionnez l’une des deux options suivantes à l’aide du bouton bascule :

    * **Emplacement d’origine** : Restaurez les fichiers ou dossiers sélectionnés dans le même partage de fichiers que la source d’origine.
    * **Autre emplacement** : Restaurez les fichiers ou dossiers sélectionnés dans un autre emplacement et conservez le contenu du partage de fichiers d’origine tel quel.

#### <a name="restore-to-the-original-location-item-level-recovery"></a>Restaurer à l'emplacement d'origine (récupération au niveau de l'élément)

1. Sélectionnez **Emplacement d’origine** comme **Destination de récupération** et choisissez d’ignorer ou de remplacer en cas de conflit en sélectionnant l’option appropriée dans la liste déroulante **En cas de conflit**.

    ![Emplacement d’origine pour la récupération au niveau de l’élément](./media/restore-afs/original-location-item-level.png)

1. Pour sélectionner les fichiers ou dossiers à restaurer, sélectionnez le bouton **Ajouter un fichier**. Un volet contextuel s’ouvre à droite et présente le contenu du point de récupération du partage de fichiers sélectionné pour restauration.

    ![Choisir Ajouter un fichier](./media/restore-afs/add-file.png)

1. Activez la case à cocher correspondant au fichier ou au dossier que vous souhaitez restaurer, puis choisissez **Sélectionner**.

    ![Sélectionner un fichier ou un dossier](./media/restore-afs/select-file-folder.png)

1. Répétez les étapes 2 à 4 pour sélectionner plusieurs fichiers ou dossiers à restaurer.
1. Après avoir sélectionné tous les éléments à restaurer, sélectionnez **Restaurer** pour démarrer l’opération de restauration.

    ![Sélectionner Restaurer pour démarrer](./media/restore-afs/click-restore.png)

#### <a name="restore-to-an-alternate-location-item-level-recovery"></a>Restaurer à un autre emplacement (récupération au niveau de l'élément)

1. Sélectionnez **Autre emplacement** comme **Destination de récupération**.
1. Dans la liste déroulante **Compte de stockage**, sélectionnez le compte de stockage de destination dans lequel vous souhaitez restaurer le contenu sauvegardé.
1. La liste déroulante **Sélectionner le partage de fichiers** affiche les partages de fichiers présents dans le compte de stockage que vous avez sélectionné à l’étape 2. Sélectionnez le partage de fichiers dans lequel vous souhaitez restaurer le contenu sauvegardé.
1. Dans la zone **Nom du dossier**, spécifiez le nom du dossier que vous souhaitez créer dans le partage de fichiers de destination avec le contenu restauré.
1. Indiquez si les conflits doivent être ignorés ou remplacés.
1. Pour sélectionner les fichiers ou dossiers à restaurer, sélectionnez le bouton **Ajouter un fichier**. Un volet contextuel s’ouvre à droite et présente le contenu du point de récupération du partage de fichiers sélectionné pour restauration.

    ![Sélectionner les éléments à restaurer à un autre emplacement](./media/restore-afs/restore-to-alternate-location.png)

1. Activez la case à cocher correspondant au fichier ou au dossier que vous souhaitez restaurer, puis choisissez **Sélectionner**.

    ![Sélectionner la destination de récupération](./media/restore-afs/recovery-destination.png)

1. Répétez les étapes 6 à 8 pour sélectionner plusieurs fichiers à restaurer.
1. Après avoir sélectionné tous les éléments à restaurer, sélectionnez **Restaurer** pour démarrer l’opération de restauration.

    ![Sélectionner OK après avoir sélectionné tous les fichiers](./media/restore-afs/after-selecting-all-items.png)

## <a name="track-a-restore-operation"></a>Suivre une opération de restauration

Une fois que vous déclenchez l’opération de restauration, le service de sauvegarde crée un travail à des fins de suivi. Sauvegarde Azure affiche des notifications sur le travail dans le portail. Pour afficher les opérations du travail, sélectionner le lien hypertexte notifications.

![Sélectionner le lien hypertexte notifications](./media/restore-afs/notifications-link.png)

Vous pouvez également surveiller la progression de la restauration à partir du coffre Recovery Services :

1. Accédez au **Centre de sauvegarde**, puis, dans le menu, cliquez sur **Travaux de sauvegarde**.
1. Filtrez les travaux pour le type de source de données et l’état de travail requis.

   :::image type="content" source="./media/restore-afs/backup-center-jobs-inline.png" alt-text="Capture d’écran montrant la sélection de l’option Travaux de sauvegarde." lightbox="./media/restore-afs/backup-center-jobs-expanded.png":::

1. Sélectionnez le nom de la charge de travail correspondant à votre partage de fichiers afin d’afficher plus de détails sur l’opération de restauration, par exemple, les **Données transférées** et le **Nombre de fichiers restaurés**.

    ![Voir les détails restaurés](./media/restore-afs/restore-details.png)

## <a name="next-steps"></a>Étapes suivantes

* Apprendre à [Gérer les sauvegardes de partage de fichiers Azure](manage-afs-backup.md).
