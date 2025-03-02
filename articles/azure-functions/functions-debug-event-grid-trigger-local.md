---
title: Débogage local de fonctions Azure Event Grid
description: Apprendre à déboguer localement des fonctions Azure Functions déclenchées par un événement Event Grid
author: craigshoemaker
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: cshoe
ms.openlocfilehash: 240b721d71ef53ad6f43f076f1d5db0e29b556a4
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111970927"
---
# <a name="azure-function-event-grid-trigger-local-debugging"></a>Débogage local lors du déclenchement de fonctions Azure Event Grid

Cet article montre comment déboguer une fonction locale qui gère un événement Azure Event Grid déclenché par un compte de stockage. 

## <a name="prerequisites"></a>Prérequis

- Créer ou utiliser une application de fonction existante
- Créez ou utilisez un compte de stockage existant. L’abonnement aux notifications Event Grid peut être défini sur des comptes de stockage Azure pour `BlobStorage`, `StorageV2` ou [Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md).
- Télécharger [ngrok](https://ngrok.com/) pour permettre à Azure d’appeler une fonction locale

## <a name="create-a-new-function"></a>Créer une fonction

Ouvrez votre application de fonction dans Visual Studio, cliquez avec le bouton droit sur le nom du projet dans l’Explorateur de solutions, puis cliquez sur **Ajouter > Nouvelle fonction Azure**.

Dans la fenêtre *Nouvelle fonction Azure*, sélectionnez **Déclencheur Event Grid**, puis cliquez sur **OK**.

![Créer une fonction](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-add-function.png)

Une fois la fonction créée, ouvrez le fichier de code et copiez l’URL commentée en haut du fichier. Cet emplacement est utilisé lorsque vous configurez le déclencheur Event Grid.

![Emplacement de copie](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-copy-location.png)

Définissez ensuite un point d’arrêt sur la ligne commençant par `log.LogInformation`.

![Définir le point d’arrêt](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-set-breakpoint.png)


**Appuyez sur F5** pour démarrer une session de débogage.

[!INCLUDE [functions-event-grid-local-dev](../../includes/functions-event-grid-local-dev.md)]

## <a name="debug-the-function"></a>Déboguer la fonction

Quand Event Grid reconnaît qu'un nouveau fichier est chargé dans le conteneur de stockage, le point d’arrêt est atteint dans votre fonction locale.

![Démarrer ngrok](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-breakpoint.png)

## <a name="clean-up-resources"></a>Nettoyer les ressources

Pour nettoyer les ressources créées dans cet article, supprimez le conteneur **test** de votre compte de stockage.

## <a name="next-steps"></a>Étapes suivantes

- [Automatiser le redimensionnement des images chargées à l’aide d’Event Grid](../event-grid/resize-images-on-storage-blob-upload-event.md)
- [Déclencheur Event Grid pour Azure Functions](./functions-bindings-event-grid.md)