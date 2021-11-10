---
author: fvneerden
ms.service: azure-video-analyzer
ms.topic: include
ms.date: 11/04/2021
ms.author: faneerde
ms.custom: ignite-fall-2021
ms.openlocfilehash: 82d0bddf25488a8cd3bce195221a96136d5f53bf
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131861601"
---
Pour exécuter l’exemple de code, effectuez les étapes suivantes :

1. Dans Visual Studio Code, ouvrez l’onglet **Extensions** (ou appuyez sur Ctrl+Maj+X) et recherchez Azure IoT Hub.
1. Cliquez avec le bouton droit et sélectionnez **Paramètres d’extension**.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="../../../media/vscode-common-screenshots/extension-settings.png" alt-text="Paramètres d’extension":::

1. Recherchez et activez « Afficher le message détaillé ».

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="../../../media/vscode-common-screenshots/verbose-message.png" alt-text="Afficher le message détaillé":::

1. Dans Visual Studio Code, accédez à _src/cloud-to-device-console-app/operations.json_.
1. Sur le nœud **pipelineTopologySet**, vérifiez que vous voyez la valeur suivante :

   ```
   "pipelineTopologyUrl" : "https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/motion-detection/topology.json"
   ```

1. Dans les nœuds `livePipelineSet` et `livePipelineDelete`, vérifiez que la valeur de **topologyName** correspond à celle de la propriété **name** dans la topologie de pipeline :

   `"topologyName" : "MotionDetection"`

1. Démarrez une session de débogage en appuyant sur la touche F5. La fenêtre **TERMINAL** affiche des messages.
1. Le fichier _operations.json_ commence par appeler `pipelineTopologyList` et `livePipelineList`. Si vous avez nettoyé les ressources après avoir suivi les guides de démarrage rapide précédents, ce processus va retourner des listes vides.

   ```
   -----------------------  Request: pipelineTopologyList  --------------------------------------------------

   {
   "@apiVersion": "1.1"
   }

   ---------------  Response: pipelineTopologyList - Status: 200  ---------------

   {
     "value": []
   }

   --------------------------------------------------------------------------

   ```

   La fenêtre **TERMINAL** affiche le jeu d’appels de méthode directe suivant :

   - Un appel à `pipelineTopologySet` qui utilise l’élément pipelineTopologyUrl précédent.
   - Un appel à `livePipelineSet` qui utilise le corps suivant :

   ```json
   {
     "@apiVersion": "1.1",
     "name": "Sample-Pipeline-1",
     "properties": {
       "topologyName": "MotionDetection",
       "description": "Sample pipeline description",
       "parameters": [
         {
           "name": "rtspUrl",
           "value": "rtsp://rtspsim:554/media/camera-300s.mkv"
         },
         {
           "name": "rtspUserName",
           "value": "testuser"
         },
         {
           "name": "rtspPassword",
           "value": "testpassword"
         }
       ]
     }
   }
   ```

   - Un appel à `livePipelineActivate` qui démarre le pipeline en direct et le flux vidéo.
   - Un deuxième appel à `livePipelineList` qui indique que le pipeline en direct est dans l’état En cours d’exécution.

1. La sortie affichée dans la fenêtre **TERMINAL** fait une pause au niveau de `Press Enter to continue`. Ne sélectionnez pas encore Entrée. Faites défiler vers le haut pour voir les charges utiles de réponse JSON pour les méthodes directes que vous avez invoquées.
1. Basculez vers la fenêtre **SORTIE** de Visual Studio Code. Vous voyez des messages indiquant que le module périphérique Video Analyzer effectue des envois au hub IoT. La section suivante de ce guide de démarrage rapide décrit ces messages.
1. Le pipeline en direct continue à s’exécuter et imprime des résultats. Le simulateur RTSP continue de boucler la vidéo source. Pour arrêter le pipeline en direct, retournez à la fenêtre **TERMINAL** et sélectionnez Entrée.

   La série d’appels suivante nettoie les ressources :

   - Un appel à `livePipelineDeactivate` désactive le pipeline.
   - Un appel à `livePipelineDelete` supprime le pipeline.
   - Un appel à `pipelineTopologyDelete` supprime la topologie.
   - Un dernier appel à `pipelineTopologyList` indique que la liste est vide.
