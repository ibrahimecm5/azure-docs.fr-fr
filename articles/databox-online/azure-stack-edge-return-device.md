---
title: Retour d’appareil Azure Stack Edge
description: Apprenez à réinitialiser les données et à retourner votre appareil Azure Stack Edge, puis à supprimer la ressource associée à l’appareil.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 10/28/2021
ms.author: alkohli
ms.openlocfilehash: b17b613c07430ce7d4a86e251a86a96d250f0255
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131469288"
---
# <a name="return-your-azure-stack-edge-device"></a>Retourner votre appareil Azure Stack Edge

[!INCLUDE [applies-to-pro-fpga](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-fpga-sku.md)]

Cet article décrit comment effacer les données, puis retourner votre appareil Azure Stack Edge. Une fois que vous avez retourné l’appareil, vous pouvez également supprimer la ressource associée à l’appareil.

Dans cet article, vous apprendrez comment :

> [!div class="checklist"]
>
> * Effacer les données des disques de données sur l’appareil
> * Initier le retour de l’appareil dans le portail Azure
> * Emballer l’appareil et planifier un enlèvement
> * Supprimer la ressource dans le Portail Azure

## <a name="erase-data-from-the-device"></a>Effacer les données de l’appareil

Pour effacer les données des disques de données de votre appareil, vous devez réinitialiser votre appareil.

Avant de réinitialiser, créez une copie des données locales sur l’appareil si nécessaire. Vous pouvez copier les données de l’appareil dans un conteneur de Stockage Azure. 

Vous pouvez initier le retour de l’appareil même avant la réinitialisation de l’appareil.

Vous pouvez réinitialiser votre appareil via l’interface utilisateur web locale ou PowerShell. Pour obtenir des instructions concernant PowerShell, consultez [Réinitialiser votre appareil](./azure-stack-edge-connect-powershell-interface.md#reset-your-device).

[!INCLUDE [Reset data from the device](../../includes/azure-stack-edge-device-reset.md)]

> [!NOTE]
> - Si vous échangez pour ou effectuez une mise à niveau vers un nouvel appareil, nous vous recommandons de ne réinitialiser votre appareil que lorsque vous avez reçu le nouvel appareil.
> - La réinitialisation de l’appareil supprime uniquement les données locales de l’appareil. Les données qui se trouvent dans le cloud ne sont pas supprimées et entraîne des [frais](https://azure.microsoft.com/pricing/details/storage/). Ces données doivent être supprimées séparément à l’aide d’un outil de gestion de stockage cloud tel que [Explorateur Stockage Azure](https://azure.microsoft.com/features/storage-explorer/).

## <a name="initiate-device-return"></a>Commencer la configuration de l’appareil

Pour commencer le processus de retour, effectuez les étapes suivantes.

---

### <a name="azure-edge-hardware-center-preview"></a>[Azure Edge Hardware Center (préversion)](#tab/azure-edge-hardware-center) 

Si vous avez utilisé Azure Edge Hardware Center pour commander votre appareil, procédez comme suit pour retourner l’appareil :

1. Dans le portail Azure, accédez à la ressource de commande d’Azure Edge Hardware Center. Dans la **Vue d’ensemble**, accédez à la barre de commandes supérieure dans le volet droit, puis sélectionnez **Retourner**. L’option de retour n’est activée qu’une fois que vous avez reçu un appareil.

    ![Retourner l’appareil 1](media/azure-stack-edge-return-device/hardware-center-return-device-1.png)  

1. Dans le panneau **Retourner le matériel**, fournissez les informations suivantes :

    ![Retourner l’appareil 2](media/azure-stack-edge-return-device/hardware-center-return-device-2.png) 

    1. Dans la liste déroulante, sélectionnez un **Motif de retour**.

    1. Indiquez le numéro de série de l’appareil. Pour récupérer le numéro de série de l’appareil, accédez à l’interface utilisateur web locale de l’appareil, puis accédez à **Vue d’ensemble**.  
    
       ![Numéro de série de l’appareil 1](media/azure-stack-edge-return-device/device-serial-number-1.png) 

    1. (Facultatif) Entrez le numéro d’**étiquette de service**. Le numéro d’étiquette de service est un identificateur avec au moins cinq caractères, propre à votre appareil. L’étiquette de service se trouve dans l’angle inférieur droit de l’appareil (quand vous êtes face à celui-ci). Retirez l’étiquette d’informations (panneau d’étiquette coulissant). Ce panneau contient des informations système telles que l’étiquette de service, la carte d’interface réseau, l’adresse MAC, etc. 
    
       ![Numéro d’étiquette de service 1](media/azure-stack-edge-return-device/service-tag-number-1.png)

    1. Pour demander une boîte d’expédition de retour, cochez la case **Boîte d’expédition requise pour retourner l’unité matérielle**. Vous pouvez la demander. Répondez **Oui** à la question **Need an empty box to return** (Besoin d’une boîte vide pour le retour).
    
    1. Passez en revue les **conditions de confidentialité** et activez la case à cocher en regard de la note, disant que vous avez lu et acceptez les conditions de confidentialité.

    1. Vérifiez les **Détails du prélèvement**. Par défaut, ces paramètres sont définis sur votre adresse de livraison. Vous pouvez ajouter une nouvelle adresse ou en sélectionner une différente parmi les adresses enregistrées pour la récupération du retour.

        ![Retourner l’appareil 3](media/azure-stack-edge-return-device/hardware-center-return-device-3.png) 

    1. Sélectionnez **Initier le retour**.

1. Une fois la demande de retour envoyée, la ressource de la commande commence à refléter l’état de votre retour. L’état passe de **Retour lancé** à **Récupéré**, puis **Retour terminé**. Utilisez le portail pour vérifier l’état de retour de votre ressource à tout moment.

    ![Retourner l’appareil 5](media/azure-stack-edge-return-device/hardware-center-return-device-4.png) 

1. Une fois la demande initiée, l’équipe des opérations Azure Stack Edge vous contacte pour vous aider à planifier la collecte de l’appareil.

L’étape suivante consiste à emballer l’appareil.


### <a name="portal-classic"></a>[Portail (classique)](#tab/azure-portal)

Si vous avez utilisé le portail classique pour commander votre appareil, procédez comme suit pour retourner l’appareil :

1. Accédez à la ressource de votre Azure Stack Edge dans le portail Azure. Dans la **Vue d’ensemble**, accédez à la barre de commandes dans le volet droit, puis sélectionnez **Retourner l’appareil**. 

    ![Retourner l’appareil 1](media/azure-stack-edge-return-device/return-device-1.png)  

2. Dans le panneau **Retourner l’appareil**, sous **Détails de base** :

    1. Indiquez le numéro de série de l’appareil. Pour récupérer le numéro de série de l’appareil, accédez à l’interface utilisateur web locale de l’appareil, puis accédez à **Vue d’ensemble**.  
    
       ![Numéro de série de l’appareil 1](media/azure-stack-edge-return-device/device-serial-number-1.png) 

    2. Entrez le numéro d’étiquette de service. Le numéro d’étiquette de service est un identificateur avec au moins cinq caractères, propre à votre appareil. L’étiquette de service se trouve dans l’angle inférieur droit de l’appareil (quand vous êtes face à celui-ci). Retirez l’étiquette d’informations (panneau d’étiquette coulissant). Ce panneau contient des informations système telles que l’étiquette de service, la carte d’interface réseau, l’adresse MAC, etc. 
    
       ![Numéro d’étiquette de service 1](media/azure-stack-edge-return-device/service-tag-number-1.png)

    3. Dans la liste déroulante, choisissez une raison pour le retour.

       ![Retourner l’appareil 2](media/azure-stack-edge-return-device/return-device-2.png) 

3. Sous **Informations de livraison** :

    1. Indiquez votre nom, le nom de votre société et l’adresse complète de votre entreprise. Entrez un numéro de téléphone professionnel, en incluant l’indicatif régional et un ID d’e-mail pour la notification.
    2. Si vous avez besoin d’une boîte d’expédition de retour, vous pouvez en demander une. Répondez **Oui** à la question **Need an empty box to return** (Besoin d’une boîte vide pour le retour).

    ![Retourner l’appareil 3](media/azure-stack-edge-return-device/return-device-3.png)

4. Passez en revue les **conditions de confidentialité** et activez la case à cocher en regard de la note, disant que vous avez lu et acceptez les conditions de confidentialité.

5. Sélectionnez **Initier le retour**.

    ![Retourner l’appareil 4](media/azure-stack-edge-return-device/return-device-4.png) 

6. Une fois que les détails du retour de l’appareil ont été capturés, vous pouvez informer l’équipe des opérations Azure Stack Edge par e-mail. Vous pouvez utiliser votre application de messagerie, en supposant que l’application de messagerie est installée et configurée. Vous pouvez également copier les données pour créer et envoyer un e-mail.

    ![Retourner l’appareil 5](media/azure-stack-edge-return-device/return-device-5.png) 

7. Une fois que l’équipe des opérations Azure Stack Edge reçoit l’e-mail, elle vous envoie une étiquette de livraison de retour. Lorsque vous recevez cette étiquette, vous pouvez planifier la collecte de l’appareil avec le transporteur. 

---

## <a name="pack-the-device"></a>Empaqueter l’appareil

Pour compresser l’appareil, procédez comme suit.

1. Arrêtez l’appareil. Sur l’interface utilisateur web locale, accédez à **Maintenance > Paramètres d'alimentation**.
2. Sélectionnez **Arrêter**. Lorsque la boîte de dialogue de confirmation apparaît, cliquez sur **Oui** pour continuer. Pour plus d’informations, consultez [Gérer l’alimentation](../databox-online/azure-stack-edge-manage-access-power-connectivity-mode.md#manage-power).
3. Débranchez les câbles d’alimentation et retirez tous les câbles réseau de l’appareil.
4. Préparez soigneusement la boîte d’expédition conformément aux instructions ci-dessous et comme indiqué dans le diagramme suivant :

    ![Emballage des appareils](media/azure-stack-edge-return-device/device-packaging-1.svg) 

    1. Utilisez la boîte d’envoi que vous avez demandée à partir d’Azure ou la boîte d’envoi d’origine avec son emballage en mousse. 
    1. Placez la pièce en mousse inférieure dans la zone.
    1. Posez l’appareil sur la mousse en veillant à ce qu’il soit placé dans la mousse.
    1. Placez la pièce de mousse supérieure dans l’emballage.
    1. Placez les cordons d’alimentation dans la barre des accessoires et les rails sur la pièce en mousse supérieure.
    1. Scellez la boîte et apposez l’étiquette d’expédition que vous avez reçue d’Azure sur l’emballage.
   
    > [!IMPORTANT]
    > Si les recommandations appropriées pour préparer la livraison de retour ne sont pas respectées, l’appareil peut être endommagé et des frais d’appareil endommagés peuvent s’appliquer. Passez en revue les [Conditions d’entretien du produit](https://www.microsoft.com/licensing/product-licensing/products) et le [Forum aux questions sur l’appareil perdu ou endommagé](https://azure.microsoft.com/pricing/details/databox/edge/).
 


## <a name="schedule-a-pickup"></a>Planification d’un enlèvement

Pour planifier une collecte, procédez comme suit.

1. Planifiez un enlèvement avec votre transporteur régional. Si vous retournez l’appareil aux États-Unis, votre transporteur est UPS ou FedEx. Pour planifier un enlèvement avec UPS :

    1. Appelez l’UPS local (numéro d’appel gratuit propre à chaque pays/région).
    2. Lors de votre appel, indiquez le numéro de suivi de retour mentionné sur votre étiquette imprimée.
    3. Si le numéro de suivi n’est pas indiqué, UPS vous facturera des frais supplémentaires à l’enlèvement.

    Au lieu de planifier l’enlèvement, vous pouvez également déposer le disque Azure Stack Edge au point de dépôt le plus proche.

## <a name="complete-return"></a>Retour complet

Dans cette section, vous pouvez vérifier à quel moment le retour est terminé, puis choisir de supprimer la commande. 

---

### <a name="azure-edge-hardware-center-preview"></a>[Azure Edge Hardware Center (préversion)](#tab/azure-edge-hardware-center)

Lorsque l’appareil est reçu par le centre de données Azure, inspectez-le pour rechercher des dommages ou des signes de falsification.

- Si l’appareil arrive intact et en bon état, le compteur de facturation s’arrête pour cette ressource. L’équipe des opérations Azure Stack Edge vous contactera pour confirmer que l’appareil a été renvoyé. L’état de l’élément de commande reflète également l’achèvement du retour.  Vous pouvez maintenant choisir de supprimer la ressource de commande dans le portail Azure.
- Si l’appareil arrive sérieusement endommagé, des frais peuvent s’appliquer. Pour plus de détails, consultez le [FAQ relatif aux appareils perdus ou endommagés](https://azure.microsoft.com/pricing/details/databox/edge/) et les [conditions d’utilisation du produit](https://www.microsoft.com/licensing/product-licensing/products). 

### <a name="portal-classic"></a>[Portail (classique)](#tab/azure-portal) 

Lorsque l’appareil est reçu par le centre de données Azure, il est inspecté pour rechercher des dommages ou des signes de falsification.

- Si l’appareil arrive intact et en bon état, le compteur de facturation s’arrête pour cette ressource. L’équipe des opérations Azure Stack Edge vous contactera pour confirmer que l’appareil a été renvoyé. Vous pouvez alors choisir de supprimer la ressource associée à l’appareil dans le portail Azure.
- Si l’appareil arrive sérieusement endommagé, des frais peuvent s’appliquer. Pour plus de détails, consultez le [FAQ relatif aux appareils perdus ou endommagés](https://azure.microsoft.com/pricing/details/databox/edge/) et les [conditions d’utilisation du produit](https://www.microsoft.com/licensing/product-licensing/products).  


Vous pouvez supprimer l’appareil dans le Portail Azure :

- Après avoir passé une commande et avant que Microsoft ait préparé l’appareil.
- Une fois que vous avez retourné un appareil à Microsoft, et que l’équipe d’opérations d’Azure Stack Edge a appelé pour confirmer le retour de l’appareil. L’équipe d’opérations n’appelle pas jusqu’à ce que l’appareil retourné passe l’inspection physique au centre de gestion Azure.

Si vous avez activé l’appareil sur un autre abonnement ou à un autre emplacement, Microsoft déplacera votre commande vers le nouvel abonnement ou emplacement dans un délai d’un jour ouvrable. Lorsque la commande est déplacée, vous pouvez supprimer cette ressource.


Effectuez les étapes suivantes pour supprimer l’appareil et la ressource dans le Portail Azure.

1. Dans le Portail Azure, accédez à votre ressource, puis à **Vue d’ensemble**. Dans la barre de commandes, sélectionnez **Supprimer**.

    ![Sélectionner Supprimer](media/azure-stack-edge-return-device/delete-resource-1.png)

2. Dans le panneau **Supprimer l’appareil**, saisissez le nom de l’appareil que vous souhaitez supprimer, puis sélectionnez **Supprimer**.

    ![Confirmation de suppression](media/azure-stack-edge-return-device/delete-resource-2.png)

Vous êtes informé lorsque l’appareil et la ressource associée ont bien été supprimés.

---

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [Obtenir un appareil Azure Stack Edge de remplacement](azure-stack-edge-replace-device.md).