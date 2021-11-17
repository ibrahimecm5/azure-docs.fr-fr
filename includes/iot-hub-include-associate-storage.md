---
title: Fichier include
description: Fichier include
services: iot-hub
author: JimacoMS3
ms.service: iot-hub
ms.topic: include
ms.date: 01/22/2019
ms.author: v-jbrannian
ms.custom: include file
ms.openlocfilehash: b4f390fe573e732bb01dde6ef2a63a6164d7f6e5
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130288459"
---
## <a name="associate-an-azure-storage-account-to-iot-hub"></a>Associer un compte Azure Storage à IoT Hub

Pour télécharger des fichiers à partir d’un appareil, vous devez disposer d’un compte de stockage Azure et d’un conteneur Stockage Blob Azure associés à votre IoT Hub. Une fois que vous associez le compte de stockage et le conteneur à votre hub IoT, il peut fournir les éléments d’un URI SAS lorsqu’il est demandé par un appareil. L’appareil peut ensuite utiliser ces éléments pour construire l’URI SAS qu’il utilise pour s’authentifier auprès du Stockage Azure et charger des fichiers dans le conteneur d’objets blob.

Pour associer un compte de Stockage Azure à votre hub IoT :

1. Dans **Paramètres hub**, sélectionnez **Chargement de fichiers** dans le volet gauche de votre hub IoT.

    :::image type="content" source="./media/iot-hub-include-associate-storage/select-storage.png" alt-text="Capture d’écran présentant les paramètres de téléchargement de fichiers à partir du portail." border="true":::

1. Dans le volet **Chargement de fichiers** sélectionnez **Conteneur de stockage Azure**. Pour cet article, il est recommandé que votre compte de stockage et votre IoT Hub se trouvent dans la même région.
    * Si vous avez déjà un compte de stockage que vous souhaitez utiliser, sélectionnez-le dans la liste.

    * Pour créer un nouveau compte de stockage, sélectionnez **+Compte de stockage**. Donnez un nom au compte de stockage et veillez à ce que l’**Emplacement** soit défini sur la même région que votre hub IoT, puis sélectionnez **OK**. Le nouveau compte est créé dans le même groupe de ressources que votre IoT Hub. Une fois le déploiement terminé, sélectionnez le compte de stockage dans la liste.

    Une fois le compte de stockage sélectionné, le volet **Conteneurs** s’ouvre.

1. Dans le volet **Conteneurs**, sélectionnez le conteneur d’objets blob.
    * Si vous disposez déjà d’un conteneur d’objets blob que vous souhaitez utiliser, sélectionnez-le dans la liste et cliquez sur **Sélectionner**.

    * Sélectionnez **+ Conteneur** pour créer un nouveau conteneur d’objets blob. Donnez un nom au nouveau conteneur. Dans le cadre de cet article, vous pouvez conserver les valeurs par défaut de tous les autres champs. Sélectionnez **Create** (Créer). Une fois le déploiement terminé, sélectionnez le conteneur dans la liste et cliquez sur **Sélectionner**.

1. De nouveau dans le volet **Chargement de fichiers** veillez à ce que les notifications de fichiers soient configurées sur **On**. Vous pouvez garder les valeurs par défaut de tous les autres paramètres. Sélectionnez **Enregistrer** et attendez l’exécution des paramètres avant de passer à la section suivante.

    :::image type="content" source="./media/iot-hub-include-associate-storage/file-upload-settings-small.png" alt-text="Capture d’écran présentant les paramètres de confirmation de chargement du fichier dans le portail." border="true":::

Pour plus d’informations sur la création d’un compte de stockage Azure, consultez [Créer un compte de stockage](../articles/storage/common/storage-account-create.md). Pour obtenir des instructions plus détaillées sur la façon d’associer un compte de stockage et un conteneur d’objets blob à un hub IoT, consultez [Configurer les chargements de fichiers à l’aide du portail Azure](../articles/iot-hub/iot-hub-configure-file-upload.md).
