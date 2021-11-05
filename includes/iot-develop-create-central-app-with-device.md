---
title: Fichier include
description: Fichier include
author: timlt
ms.service: iot-develop
ms.topic: include
ms.date: 04/28/2021
ms.author: timlt
ms.custom: include file
ms.openlocfilehash: 210978e3ecc817c55103267014fef9bd9e36a0e7
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131032785"
---
## <a name="create-an-application"></a>Créer une application
Il existe plusieurs méthodes pour connecter des appareils à Azure IoT. Dans cette section, vous allez apprendre à connecter un appareil à l’aide d’Azure IoT Central. IoT Central est une plateforme d’application IoT qui réduit le coût et la complexité de la gestion des appareils dans une solution IoT.

Pour créer une application :
1. Accédez à [Azure IoT Central](https://apps.azureiotcentral.com/) et connectez-vous avec un compte Microsoft personnel, professionnel ou scolaire.
1. Accédez à **Générer**, puis sélectionnez **Applications personnalisées**.
   :::image type="content" source="media/iot-develop-create-central-app-with-device/iot-central-build.png" alt-text=" Page d’accueil d’IoT Central ":::
1. Dans **Nom de l’application**, entrez un nom unique ou utilisez le nom généré.
1. Dans **URL**, entrez un préfixe d’URL d’application facile à mémoriser ou utilisez le préfixe d’URL généré.
1. Laissez **Modèle d’application** défini sur *Application personnalisée*. 
1. Sélectionnez une option de **plan tarifaire**. 
    - Pour utiliser l’application gratuitement pendant sept jours, sélectionnez **Gratuit**. Vous pouvez convertir une application gratuite en tarification standard avant qu’elle n’expire.
    - Si vous le souhaitez, vous pouvez sélectionner un plan tarifaire standard. Si vous sélectionnez la tarification standard, d’autres options s’affichent et vous devez définir un **répertoire**, un **abonnement Azure** et un **emplacement**. Pour en savoir plus sur la tarification, consultez [Tarification d’Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/). 
        - **Annuaire** correspond à l’annuaire Azure Active Directory dans lequel vous créez votre application. Un annuaire Azure Active Directory contient les identités des utilisateurs, les informations d’identification et d’autres informations de l’organisation. Si vous n’avez pas d’instance d’Azure Active Directory, il s’en crée une automatiquement quand vous créez un abonnement Azure.
        - Un **abonnement Azure** vous permet de créer des instances de services Azure. IoT Central provisionne des ressources dans votre abonnement. Si vous n’avez pas d’abonnement, vous pouvez en créer un [gratuitement](https://aka.ms/createazuresubscription). Si vous disposez d’un abonnement, vous pouvez le sélectionner dans la liste déroulante.
        - L’**emplacement** désigne la [région géographique Azure](https://azure.microsoft.com/global-infrastructure/geographies/) dans laquelle vous créez une application. Sélectionnez la région qui est physiquement la plus proche de vos appareils, de façon à obtenir des performances optimales. Une fois que vous aurez choisi un emplacement, vous ne pourrez plus déplacer l’application vers un autre emplacement.

    :::image type="content" source="media/iot-develop-create-central-app-with-device/iot-central-pricing.png" alt-text="Boîte de dialogue Nouvelle application d’IoT Central":::
1. Sélectionnez **Create** (Créer).
    
    Après avoir créé l’application, IoT Central vous redirige vers le tableau de bord de l’application.
    :::image type="content" source="media/iot-develop-create-central-app-with-device/iot-central-created.png" alt-text="Tableau de bord Nouvelle application d’IoT Central":::

## <a name="add-a-device"></a>Ajout d’un appareil
Dans cette section, vous ajoutez un nouvel appareil à votre application IoT Central. L’appareil est l’instance d’un modèle d’appareil qui représente un appareil que vous allez connecter à l’application. 

Pour créer un nouvel appareil :
1. Dans le volet gauche, sélectionnez **Appareils**, puis sélectionnez **+Nouveau**.
1. Laissez **Modèle d’appareil** défini sur *Non affecté* et **Simuler cet appareil ?** défini sur *Non*.

1. Définissez un **nom d’appareil** convivial et un **ID d’appareil**. Si vous le souhaitez, utilisez les valeurs générées.
    :::image type="content" source="media/iot-develop-create-central-app-with-device/iot-central-create-device.png" alt-text="Boîte de dialogue Nouvel appareil d’IoT Central":::

1. Sélectionnez **Create** (Créer).

    L’appareil créé s’affiche dans la liste **Tous les appareils**.
    :::image type="content" source="media/iot-develop-create-central-app-with-device/iot-central-devices-list.png" alt-text="Liste Tous les appareils d’IoT Central":::
    
Pour obtenir les détails de connexion du nouvel appareil :
1. Dans la liste **Tous les appareils**, cliquez sur le nom de l’appareil lié pour afficher les détails. 
1. Dans le menu supérieur, sélectionnez **Se connecter**.

    La boîte de dialogue **Connexion de l’appareil** affiche les détails de la connexion : :::image type="content" source="media/iot-develop-create-central-app-with-device/iot-central-device-connect.png" alt-text="Détails de connexion de l’appareil d’IoT Central":::
1. Copiez les valeurs suivantes de la boîte de dialogue **Connexion de l’appareil** dans un emplacement sûr. Vous utiliserez ces valeurs pour connecter votre appareil à IoT Central.
    * `ID scope`
    * `Device ID`
    * `Primary key`