---
title: Restaurer un serveur flexible Azure Database pour MySQL supprimé
description: Cet article décrit comment restaurer un serveur supprimé dans un serveur flexible Azure Database pour MySQL à l’aide du portail Azure.
author: adig
ms.author: adig
ms.service: mysql
ms.topic: how-to
ms.date: 11/10/2021
ms.openlocfilehash: a41aad558c067247868e1f83118c5feb86e83b37
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132350642"
---
# <a name="restore-a-deleted-azure-database-for-mysql-flexible-server"></a>Restaurer un serveur flexible Azure Database pour MySQL supprimé

[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

Lors de la suppression d’un serveur flexible, la sauvegarde du serveur de base de données peut être conservée dans le service pendant jusqu’à cinq jours. La sauvegarde du serveur est accessible et peut être restaurée uniquement à partir de l’abonnement Azure sur lequel le serveur résidait initialement. Vous pouvez suivre les étapes recommandées ci-dessous pour récupérer une ressource de serveur flexible MySQL supprimée dans les cinq jours suivant la suppression du serveur. Les étapes recommandées ne fonctionnent que si la sauvegarde du serveur est toujours disponible et n’a pas été supprimée du système.

## <a name="pre-requisites"></a>Conditions préalables
Pour restaurer un serveur flexible Azure Database pour MySQL supprimé, vous avez besoin des éléments suivants :
- Nom de l’abonnement Azure hébergeant le serveur d’origine
- Emplacement où le serveur a été créé

## <a name="steps-to-restore"></a>Étapes de restauration

1. Accédez au [Journal d’activité](https://ms.portal.azure.com/#blade/Microsoft_Azure_ActivityLog/ActivityLogBlade) dans le panneau Surveiller du portail Azure. 

2. Dans Journal d’activité, cliquez sur **Ajouter un filtre** comme indiqué, puis définissez les filtres suivants pour 

    - **Abonnement** = votre abonnement hébergeant le serveur supprimé
    - **Type de ressource** = Serveurs flexibles Azure Database for MySQL (Microsoft.DBforMySQL/flexibleServers) 
    - **Opération** = Delete MySQL Server (Microsoft.DBforMySQL/flexibleServers/delete) 
 
     [![Journal d’activité filtré pour l’opération de suppression du serveur MySQL](./media/how-to-restore-server-portal/monitor-log-delete-server.png)](./media/how-to-restore-server-portal/monitor-log-delete-server.png#lightbox)
   
 3. Double-cliquez sur l’événement Supprimer le serveur MySQL, puis cliquez sur l’onglet JSON et notez les attributs « resourceId » et « submissionTimestamp » dans la sortie JSON. Le resourceId est au format suivant : /subscriptions/ffffffff-ffff-ffff-ffff-ffffffffffff/resourceGroups/TargetResourceGroup/providers/Microsoft.DBforMySQL/servers/deletedserver.
 
 4. Accédez à la [page relative à la création d’un serveur de l’API REST](/rest/api/mysql/flexibleserver/servers/create), cliquez sur l’onglet « Essayer » mis en surbrillance en vert et connectez-vous avec votre compte Azure.
 
 5. Indiquez resourceGroupName, serverName (nom du serveur supprimé), subscriptionId, dérivé de l’attribut resourceId capturé à l’étape 3, tandis qu’api-version est prérempli comme illustré dans l’image.
 
     [![Créer un serveur à l’aide de l’API REST](./media/how-to-restore-server-portal/server-create-rest-api.png)](./media/how-to-restore-server-portal/server-create-rest-api.png#lightbox)
  
 6. Faites défiler la page jusqu’à la section Request Body et collez-y ce qui suit :
 
    ```json
    {
        "location": "Dropped Server Location",  
        "properties": 
            {
                "restorePointInTime": "submissionTimestamp - 15 minutes",
                "createMode": "PointInTimeRestore",
                "sourceServerResourceId": "resourceId"
            }
    }
    ```
7. Remplacez les valeurs suivantes dans le corps de la demande ci-dessus :
   * « Dropped server Location » par la région Azure où le serveur supprimé a été initialement créé.
   * « submissionTimestamp » et « resourceId » par les valeurs capturées à l’étape 3. 
   * Pour « restorePointInTime », spécifiez une valeur de « submissionTimestamp » moins **15 minutes** pour vous assurer que la commande ne génère pas d’erreur.
   
8. Si vous voyez le code de réponse 201 ou 202, cela signifie que la requête de restauration a été correctement envoyée. 

9. La création du serveur peut prendre du temps en fonction de la taille de la base de données et des ressources de calcul approvisionnées sur le serveur d’origine. L’état de la restauration peut être surveillé à partir du journal d’activité en filtrant sur : 
   - **Abonnement** = votre abonnement
   - **Type de ressource** = Serveurs flexibles Azure Database for MySQL (Microsoft.DBforMySQL/flexibleServers) 
   - **Opération** = mettre à jour la création de serveur MySQL

## <a name="next-steps"></a>Étapes suivantes
- Si vous essayez de restaurer un serveur dans un délai de cinq jours et que vous recevez toujours une erreur après avoir correctement effectué les étapes décrites précédemment, ouvrez un incident de support pour obtenir de l’aide. Si vous essayez de restaurer un serveur supprimé après cinq jours, une erreur est attendue, car le fichier de sauvegarde est introuvable. N’ouvrez pas de ticket de support dans ce cas. L’équipe de support ne peut pas fournir d’assistance si la sauvegarde est supprimée du système. 
- Pour éviter la suppression accidentelle de serveurs, nous vous recommandons vivement d’utiliser des [verrous de ressources](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/preventing-the-disaster-of-accidental-deletion-for-your-mysql/ba-p/825222).
