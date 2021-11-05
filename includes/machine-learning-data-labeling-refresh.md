---
author: sgilley
ms.service: machine-learning
ms.topic: include
ms.date: 09/23/2021
ms.author: sdgilley
ms.openlocfilehash: 1c1ab85b6da016b966bab7c66cd2a22f257d27d1
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131068388"
---
Si vous envisagez d’ajouter de nouveaux fichiers à votre jeu de données, utilisez l’actualisation incrémentielle pour les ajouter à votre projet.   Quand l’**actualisation incrémentielle** est activée, des recherches sont régulièrement effectuées dans le jeu de données pour que de nouveaux fichiers soient ajoutés à un projet, en fonction du taux d’achèvement de l’étiquetage.   La recherche de nouvelles données s’arrête quand le projet contient le nombre maximal de 500 000 fichiers.

Pour ajouter d’autres fichiers à votre projet, utilisez l’[Explorateur Stockage Azure](https://azure.microsoft.com/features/storage-explorer/) pour les charger dans le dossier approprié du stockage Blob. 

Sélectionnez **Activer l’actualisation incrémentielle** quand vous souhaitez que votre projet supervise continuellement les nouvelles données dans le magasin de données. Les données sont tirées (pull) dans votre projet une fois par jour quand l’option est activée. Une fois que vous avez ajouté de nouvelles données au magasin de données, vous devez attendre qu’elles s’affichent dans votre projet.  Vous trouverez l’horodatage de la dernière actualisation des données dans la section **Actualisation incrémentielle** de l’onglet **Détails** pour votre projet.
Désélectionnez cette option si vous ne voulez pas que les nouveaux fichiers du magasin de données soient ajoutés à votre projet.